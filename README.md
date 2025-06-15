# Car_Avoid_Game
import pygame
import random

# Initialize Pygame
pygame.init()

# Constants
WIDTH, HEIGHT = 600, 800
CAR_WIDTH, CAR_HEIGHT = 50, 80
FPS = 60
WHITE = (255, 255, 255)
GRAY = (34, 34, 34)
BLACK = (0, 0, 0)

# Screen Setup
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Car Game Final Project")
clock = pygame.time.Clock()

# Fonts
font = pygame.font.SysFont(None, 40)

# Base Car Class
class Car:
    def __init__(self, color, make, model, year):
        self.color = color
        self.make = make
        self.model = model
        self.year = year
        self.speed = 0
        self.direction = "straight"

    def accelerate(self, amount):
        self.speed += amount

    def brake(self, amount):
        self.speed -= amount
        if self.speed < 0:
            self.speed = 0

    def turn(self, new_direction):
        self.direction = new_direction

# Player Car Class
class PlayerCar(Car):
    def __init__(self):
        super().__init__(color=(200, 0, 0), make="Toyota", model="Supra", year=2023)
        self.x = WIDTH // 2 - CAR_WIDTH // 2
        self.y = HEIGHT - CAR_HEIGHT - 120
        self.speed = 5

    def move(self, keys):
        if keys[pygame.K_LEFT] and self.x > 0:
            self.x -= self.speed
        if keys[pygame.K_RIGHT] and self.x < WIDTH - CAR_WIDTH:
            self.x += self.speed

    def draw(self):
        pygame.draw.rect(screen, self.color, (self.x, self.y, CAR_WIDTH, CAR_HEIGHT), border_radius=8)

        # Car roof
        roof_width = CAR_WIDTH * 0.6
        roof_height = CAR_HEIGHT * 0.50
        roof_x = self.x + (CAR_WIDTH - roof_width) // 2
        roof_y = self.y + (CAR_HEIGHT * 0.2)
        pygame.draw.rect(screen, (160, 160, 160), (roof_x, roof_y, roof_width, roof_height), border_radius=5)

        # Front, Back, Left and Right Windows
        window_color = (240, 220, 200)
        # Front window
        pygame.draw.rect(screen, window_color, (roof_x + 5, roof_y - 10, roof_width - 10, 8), border_radius=2)
        # Back window
        pygame.draw.rect(screen, window_color, (roof_x + 5, roof_y + roof_height + 2, roof_width - 10, 8), border_radius=2)
        # Left window
        pygame.draw.rect(screen, window_color, (roof_x - 6, roof_y + 4, 5, roof_height - 8), border_radius=2)
        # Right window
        pygame.draw.rect(screen, window_color, (roof_x + roof_width + 1, roof_y + 4, 5, roof_height - 8), border_radius=2)

        # Back trunk
        trunk_width = CAR_WIDTH * 0.8
        trunk_height = 6
        trunk_x = self.x + (CAR_WIDTH - trunk_width) // 2
        trunk_y = self.y + CAR_HEIGHT - trunk_height - 2
        pygame.draw.rect(screen, (120, 0, 0), (trunk_x, trunk_y, trunk_width, trunk_height), border_radius=3)

        # Lights
        pygame.draw.rect(screen, (255, 255, 100), (self.x + 5, self.y, 8, 5))
        pygame.draw.rect(screen, (255, 255, 100), (self.x + CAR_WIDTH - 13, self.y, 8, 5))
        pygame.draw.rect(screen, (255, 0, 0), (self.x + 5, self.y + CAR_HEIGHT - 5, 8, 5))
        pygame.draw.rect(screen, (255, 0, 0), (self.x + CAR_WIDTH - 13, self.y + CAR_HEIGHT - 5, 8, 5))

        # Tires
        pygame.draw.rect(screen, BLACK, (self.x - 6, self.y + 10, 6, 14))
        pygame.draw.rect(screen, BLACK, (self.x - 6, self.y + CAR_HEIGHT - 24, 6, 14))
        pygame.draw.rect(screen, BLACK, (self.x + CAR_WIDTH, self.y + 10, 6, 14))
        pygame.draw.rect(screen, BLACK, (self.x + CAR_WIDTH, self.y + CAR_HEIGHT - 24, 6, 14))

# Opponent Car Class
class OpponentCar(Car):
    def __init__(self, x, y, color):
        super().__init__(color=color, make="Opponent", model="Car", year=2022)
        self.x = x
        self.y = y
        self.speed = 5

    def move(self):
        self.y += self.speed

    def draw(self):
        pygame.draw.rect(screen, self.color, (self.x, self.y, CAR_WIDTH, CAR_HEIGHT), border_radius=8)

        # Car roof
        roof_width = CAR_WIDTH * 0.6
        roof_height = CAR_HEIGHT * 0.50
        roof_x = self.x + (CAR_WIDTH - roof_width) // 2
        roof_y = self.y + (CAR_HEIGHT * 0.2)
        pygame.draw.rect(screen, (160, 160, 160), (roof_x, roof_y, roof_width, roof_height), border_radius=5)

        # Front, Back, Left and Right Windows
        window_color = (180, 220, 200)
        # Front window
        pygame.draw.rect(screen, window_color, (roof_x + 5, roof_y - 10, roof_width - 10, 8), border_radius=2)
        # Back window
        pygame.draw.rect(screen, window_color, (roof_x + 5, roof_y + roof_height + 2, roof_width - 10, 8), border_radius=2)
        # Left window
        pygame.draw.rect(screen, window_color, (roof_x - 6, roof_y + 4, 5, roof_height - 8), border_radius=2)
        # Right window
        pygame.draw.rect(screen, window_color, (roof_x + roof_width + 1, roof_y + 4, 5, roof_height - 8), border_radius=2)

        # Front trunk (opponent is coming from top so trunk should be at top)
        trunk_width = CAR_WIDTH * 0.6
        trunk_height = 6
        trunk_x = self.x + (CAR_WIDTH - trunk_width) // 2
        trunk_y = self.y + 2
        pygame.draw.rect(screen, (120, 0, 0), (trunk_x, trunk_y, trunk_width, trunk_height), border_radius=3)

        # Lights
        pygame.draw.rect(screen, (255, 255, 100), (self.x + 5, self.y, 8, 5))
        pygame.draw.rect(screen, (255, 255, 100), (self.x + CAR_WIDTH - 13, self.y, 8, 5))
        pygame.draw.rect(screen, (255, 0, 0), (self.x + 5, self.y + CAR_HEIGHT - 5, 8, 5))
        pygame.draw.rect(screen, (255, 0, 0), (self.x + CAR_WIDTH - 13, self.y + CAR_HEIGHT - 5, 8, 5))

        # Tires
        pygame.draw.rect(screen, BLACK, (self.x - 6, self.y + 10, 6, 14))
        pygame.draw.rect(screen, BLACK, (self.x - 6, self.y + CAR_HEIGHT - 24, 6, 14))
        pygame.draw.rect(screen, BLACK, (self.x + CAR_WIDTH, self.y + 10, 6, 14))
        pygame.draw.rect(screen, BLACK, (self.x + CAR_WIDTH, self.y + CAR_HEIGHT - 24, 6, 14))

# Game Loop
def main():
    run = True
    score = 0
    player = PlayerCar()
    opponent_cars = []

    while run:
        clock.tick(FPS)
        screen.fill(GRAY)

        # Draw road and lane dividers
        lane_width = WIDTH // 4
        for i in range(1, 4):
            pygame.draw.line(screen, WHITE, (lane_width * i, 0), (lane_width * i, HEIGHT), 2)

        # Events
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                run = False

        # Move
        keys = pygame.key.get_pressed()
        player.move(keys)

        for car in opponent_cars:
            car.move()

        # Add new cars without overlapping
        lanes = [WIDTH // 8 - CAR_WIDTH // 2, WIDTH // 2 - CAR_WIDTH // 2, 7 * WIDTH // 8 - CAR_WIDTH // 2]
        if random.randint(1, 50) == 1:
            lane_x = random.choice(lanes)
            can_spawn = True
            for car in opponent_cars:
                if abs(car.x - lane_x) < 10 and car.y < 150:
                    can_spawn = False
                    break
            if can_spawn:
                color = random.choice([(0, 200, 0), (0, 100, 255), (255, 255, 0), (200, 0, 200)])
                opponent_cars.append(OpponentCar(lane_x, -CAR_HEIGHT, color))

        # Collision Detection
        player_rect = pygame.Rect(player.x, player.y, CAR_WIDTH, CAR_HEIGHT)
        for car in opponent_cars:
            car_rect = pygame.Rect(car.x, car.y, CAR_WIDTH, CAR_HEIGHT)
            if player_rect.colliderect(car_rect):
                msg = font.render("Game Over! Final Score: " + str(score), True, WHITE)
                screen.blit(msg, (WIDTH // 2 - msg.get_width() // 2, HEIGHT // 2))
                pygame.display.flip()
                pygame.time.wait(3000)
                run = False

        # Remove off-screen cars
        opponent_cars = [car for car in opponent_cars if car.y < HEIGHT]

        # Draw
        player.draw()
        for car in opponent_cars:
            car.draw()

        # Score
        score += 1
        score_text = font.render("Score: " + str(score), True, WHITE)
        screen.blit(score_text, (WIDTH - score_text.get_width() - 10, 50))

        pygame.display.flip()

    pygame.quit()

if __name__ == '__main__':
    main()
