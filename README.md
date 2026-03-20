<img width="204" height="192" alt="image" src="https://github.com/user-attachments/assets/b6f26e45-378a-4023-b078-cb0eaaf53571" />
import pygame
import sys

# Ініціалізація
pygame.init()
SCREEN_WIDTH, SCREEN_HEIGHT = 1000, 800
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
pygame.display.set_caption("Експедиція: Європа")

# Кольори
WHITE = (255, 255, 255)
GOLD = (255, 215, 0)
RED = (200, 0, 0)

# Дані про країни (Координати x, y підібрані під умовну карту)
# В реальному проекті вони мають збігатися з вашим фоновим малюнком
COUNTRIES = {
    "Ukraine": {"pos": (700, 450), "neighbors": ["Poland", "Romania", "Slovakia"]},
    "Poland": {"pos": (550, 400), "neighbors": ["Ukraine", "Germany", "Czechia"]},
    "Germany": {"pos": (450, 420), "neighbors": ["Poland", "France", "Austria"]},
    "France": {"pos": (300, 500), "neighbors": ["Germany", "Spain", "Italy"]},
    "Italy": {"pos": (450, 600), "neighbors": ["France", "Austria", "Switzerland"]},
}

class Game:
    def __init__(self):
        self.current_location = "Ukraine"
        self.target_location = "Ukraine"
        self.player_pos = list(COUNTRIES["Ukraine"]["pos"])
        self.speed = 2.0
        self.score = 0

    def move_player(self):
        # Плавне переміщення до обраної країни
        target_x, target_y = COUNTRIES[self.target_location]["pos"]
        curr_x, curr_y = self.player_pos

        if abs(curr_x - target_x) > self.speed:
            self.player_pos[0] += self.speed if target_x > curr_x else -self.speed
        if abs(curr_y - target_y) > self.speed:
            self.player_pos[1] += self.speed if target_y > curr_y else -self.speed
        
        # Якщо дісталися - оновлюємо локацію
        if abs(curr_x - target_x) <= self.speed and abs(curr_y - target_y) <= self.speed:
            self.current_location = self.target_location

    def draw(self):
        screen.fill((30, 30, 50)) # Темний фон (замінити на pygame.image.load('map.png'))
        
        # Малюємо зв'язки (дороги)
        for name, data in COUNTRIES.items():
            for neighbor in data["neighbors"]:
                start = data["pos"]
                end = COUNTRIES[neighbor]["pos"]
                pygame.draw.line(screen, (70, 70, 90), start, end, 2)

        # Малюємо точки країн
        for name, data in COUNTRIES.items():
            color = GOLD if name == self.current_location else WHITE
            pygame.draw.circle(screen, color, data["pos"], 10)
            
            # Назви країн
            font = pygame.font.SysFont(None, 24)
            img = font.render(name, True, WHITE)
            screen.blit(img, (data["pos"][0] + 15, data["pos"][1] - 10))

        # Малюємо гравця
        pygame.draw.circle(screen, RED, (int(self.player_pos[0]), int(self.player_pos[1])), 15)

# Основний цикл
game = Game()
clock = pygame.time.Clock()

while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            sys.exit()
        
        if event.type == pygame.MOUSEBUTTONDOWN:
            mouse_pos = pygame.mouse.get_pos()
            # Перевірка, чи клікнули на країну
            for name, data in COUNTRIES.items():
                dist = ((mouse_pos[0]-data["pos"][0])**2 + (mouse_pos[1]-data["pos"][1])**2)**0.5
                if dist < 20:
                    # Можна летіти тільки в сусідні країни
                    if name in COUNTRIES[game.current_location]["neighbors"]:
                        game.target_location = name
                    else:
                        print("Ця країна занадто далеко!")

    game.move_player()
    game.draw()
    pygame.display.flip()
    clock.tick(60)
    
