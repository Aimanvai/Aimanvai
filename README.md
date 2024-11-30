import pygame
import random
import time

# Initialize pygame
pygame.init()

# Set up the screen dimensions and create the screen object
SCREEN_WIDTH = 800
SCREEN_HEIGHT = 600
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
pygame.display.set_caption("2D Car Unlimited Drive")

# Define colors
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
GREY = (169, 169, 169)

# Load car image
car_img = pygame.image.load("car.png")  # Ensure you have a 'car.png' image in your working directory
car_width = 50
car_height = 100

# Define the car class
class Car:
    def __init__(self):
        self.x = SCREEN_WIDTH // 2 - car_width // 2
        self.y = SCREEN_HEIGHT - car_height - 10
        self.speed = 5

    def move(self, left=False, right=False):
        if left and self.x > 0:
            self.x -= self.speed
        if right and self.x < SCREEN_WIDTH - car_width:
            self.x += self.speed

    def draw(self):
        screen.blit(car_img, (self.x, self.y))

# Define obstacle class
class Obstacle:
    def __init__(self):
        self.width = random.randint(50, 100)
        self.height = random.randint(20, 50)
        self.x = random.randint(0, SCREEN_WIDTH - self.width)
        self.y = -self.height
        self.speed = 5

    def move(self):
        self.y += self.speed

    def draw(self):
        pygame.draw.rect(screen, GREY, (self.x, self.y, self.width, self.height))

# Main game loop
def game_loop():
    car = Car()
    obstacles = []
    clock = pygame.time.Clock()
    score = 0
    game_over = False
    left = right = False

    while not game_over:
        screen.fill(WHITE)

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                game_over = True
            elif event.type == pygame.KEYDOWN:
                if event.key == pygame.K_LEFT:
                    left = True
                if event.key == pygame.K_RIGHT:
                    right = True
            elif event.type == pygame.KEYUP:
                if event.key == pygame.K_LEFT:
                    left = False
                if event.key == pygame.K_RIGHT:
                    right = False

        # Move car
        car.move(left, right)

        # Create new obstacles
        if random.random() < 0.02:  # Control frequency of obstacles
            obstacles.append(Obstacle())

        # Move and draw obstacles
        for obstacle in obstacles[:]:
            obstacle.move()
            obstacle.draw()

            # Check for collision
            if car.x < obstacle.x + obstacle.width and car.x + car_width > obstacle.x and car.y < obstacle.y + obstacle.height and car.y + car_height > obstacle.y:
                game_over = True  # Game over if there's a collision

            if obstacle.y > SCREEN_HEIGHT:  # Remove obstacles that pass the screen
                obstacles.remove(obstacle)
                score += 1

        # Draw car
        car.draw()

        # Draw score
        font = pygame.font.SysFont(None, 30)
        score_text = font.render(f"Score: {score}", True, BLACK)
        screen.blit(score_text, (10, 10))

        # Refresh screen
        pygame.display.update()

        # Frame rate
        clock.tick(60)

    # Game over screen
    font = pygame.font.SysFont(None, 50)
    game_over_text = font.render("GAME OVER", True, BLACK)
    screen.blit(game_over_text, (SCREEN_WIDTH // 2 - game_over_text.get_width() // 2, SCREEN_HEIGHT // 3))
    pygame.display.update()
    time.sleep(2)  # Wait for 2 seconds before quitting
    pygame.quit()

# Start the game
game_loop()
