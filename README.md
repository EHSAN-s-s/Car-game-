# Car-game-import pygame
import random
import time

pygame.init()


WIDTH = 400
HEIGHT = 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("بازی ماشین - فرار از ترافیک!")


WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
RED = (255, 0, 0)
GREEN = (0, 255, 0)
BLUE = (0, 0, 255)
YELLOW = (255, 255, 0)


clock = pygame.time.Clock()
FPS = 60


font = pygame.font.SysFont("Tahoma", 40)
small_font = pygame.font.SysFont("Tahoma", 25)


player_width = 50
player_height = 80
player_x = WIDTH // 2 - player_width // 2
player_y = HEIGHT - player_height - 30
player_speed = 7


enemy_width = 50
enemy_height = 80
enemy_speed = 5
enemy_list = []


score = 0
start_time = time.time()


def create_enemy():
    x = random.randint(50, WIDTH - 50 - enemy_width)
    y = -enemy_height
    enemy_list.append([x, y])


def collision(px, py, ex, ey):
    if px < ex + enemy_width and px + player_width > ex:
        if py < ey + enemy_height and py + player_height > ey:
            return True
    return False


running = True
game_over = False


pygame.time.set_timer(pygame.USEREVENT, 1500)  # هر 1.5 ثانیه یه دشمن جدید

while running:
    clock.tick(FPS)
    
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
            
        if event.type == pygame.USEREVENT and not game_over:
            create_enemy()
            
        if event.type == pygame.KEYDOWN and game_over:
            if event.key == pygame.K_r:  # 

                enemy_list = []
                player_x = WIDTH // 2 - player_width // 2
                score = 0
                start_time = time.time()
                game_over = False
                enemy_speed = 5
                pygame.time.set_timer(pygame.USEREVENT, 1500)

    if not game_over:
        
        keys = pygame.key.get_pressed()
        if keys[pygame.K_LEFT] and player_x > 20:
            player_x -= player_speed
        if keys[pygame.K_RIGHT] and player_x < WIDTH - player_width - 20:
            player_x += player_speed
        if keys[pygame.K_UP] and player_y > 0:
            player_y -= player_speed
        if keys[pygame.K_DOWN] and player_y < HEIGHT - player_height - 20:
            player_y += player_speed

        
        score = int(time.time() - start_time)

        
        if score > 20:
            enemy_speed = 7
        if score > 50:
            enemy_speed = 9

    
    screen.fill((50, 50, 50))  # خاکستری تیره
    
    
    for i in range(0, HEIGHT, 40):
        line_y = (i + int(time.time() * 100) % 40) % HEIGHT
        pygame.draw.rect(screen, WHITE, (WIDTH//2 - 5, line_y, 10, 25))

    
    for enemy in enemy_list[:]:
        enemy[1] += enemy_speed
        
        
        pygame.draw.rect(screen, RED, (enemy[0], enemy[1], enemy_width, enemy_height))
        pygame.draw.rect(screen, BLACK, (enemy[0] + 10, enemy[1] + 15, 30, 20))  # شیشه
        
        
        if enemy[1] > HEIGHT:
            enemy_list.remove(enemy)
            score += 1  


        if collision(player_x, player_y, enemy[0], enemy[1]):
            game_over = True

    pygame.draw.rect(screen, BLUE, (player_x, player_y, player_width, player_height))
    pygame.draw.rect(screen, WHITE, (player_x + 10, player_y + 15, 30, 20))  
    pygame.drw.circle(screen, BLACK, (player_x + 15, player_y + 70), 8)  


    pygame.draw.circle(screen, BLACK, (player_x + player_width - 15, player_y + 70), 8)

    
    score_text = small_font.render(f"امتیاز: {score}", True, WHITE)
    screen.blit(score_text, (10, 10))

    
    if game_over:
        game_over_text = font.render("بازی تموم شد!", True, RED)
        restart_text = small_font.render("برای شروع دوباره R را بزن", True, WHITE)
        final_score = font.render(f"امتیاز نهایی: {score}", True, YELLOW)
        
        screen.blit(game_over_text, (WIDTH//2 - 120, HEIGHT//2 - 80))
        screen.blit(final_score, (WIDTH//2 - 140, HEIGHT//2 - 20))
        screen.blit(restart_text, (WIDTH//2 - 140, HEIGHT//2 + 40))

    pygame.display.update()

pygame.quit()
