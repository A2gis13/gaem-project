# gaem-project
import pygame
import sys
import random
import time

pygame.init()

WIDTH, HEIGHT = 800, 400
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("장애물 피하기 게임")

WHITE = (255, 255, 255)

game_state = "start"

clock = pygame.time.Clock()
FPS = 60

player_img = pygame.image.load("student.png.png")
player_img = pygame.transform.scale(player_img, (60, 60))

chair_img = pygame.image.load("chair.png.png")
chair_img = pygame.transform.scale(chair_img, (40, 60))

desk_img = pygame.image.load("desk.png.png")
desk_img = pygame.transform.scale(desk_img, (45, 60))

books_img = pygame.image.load("books.png.png")
books_img = pygame.transform.scale(books_img, (35, 60))

background_img = pygame.image.load("hallway.png")
background_img = pygame.transform.scale(background_img, (WIDTH, HEIGHT))

bg_x = 0

ground_y = 340

player_rect = player_img.get_rect(midbottom=(80, ground_y))
player_y_vel = 0
gravity = 0.8
is_jumping = False

obstacle_images = [chair_img, desk_img, books_img]
obstacle_list = []
obstacle_timer = 0

start_time = 0
font = pygame.font.SysFont(None, 40)

def show_start_screen():
    screen.blit(background_img, (0, 0))
    text = font.render("Press space bar to start game!", True, (0, 0, 0))
    screen.blit(text, (250, 180))

def show_game_over_screen():
    screen.blit(background_img, (0, 0))
    text = font.render("Game over! Press space bar to restart game", True, (255, 0, 0))
    screen.blit(text, (230, 180))

def show_clear_screen():
    screen.blit(background_img, (0, 0))
    text = font.render("Game Clear!", True, (0, 128, 0))
    screen.blit(text, (250, 180))

def run_gameplay():
    global player_y_vel, is_jumping, obstacle_list, obstacle_timer, bg_x, game_state, start_time

    current_time = time.time()
    elapsed = current_time - start_time
    remaining = max(0, int(90 - elapsed))

    bg_x -= 2
    if bg_x <= -WIDTH:
        bg_x = 0
    screen.blit(background_img, (bg_x, 0))
    screen.blit(background_img, (bg_x + WIDTH, 0))

    player_y_vel += gravity
    player_rect.y += player_y_vel
    if player_rect.bottom >= ground_y:
        player_rect.bottom = ground_y
        is_jumping = False

 
    obstacle_timer += 1
    if obstacle_timer > 90:
        obstacle_timer = 0
        img = random.choice(obstacle_images)
        rect = img.get_rect(midbottom=(WIDTH + 20, ground_y))
        obstacle_list.append((img, rect))


    for i, (img, rect) in enumerate(obstacle_list):
        rect.x -= 6 + (elapsed // 15)  
        obstacle_list[i] = (img, rect)

   
    obstacle_list = [(img, rect) for img, rect in obstacle_list if rect.right > 0]


    for img, rect in obstacle_list:
        screen.blit(img, rect)
        if player_rect.colliderect(rect):
            game_state = "game_over"

 
    screen.blit(player_img, player_rect)


    timer_text = font.render(f"playtime: {remaining}s", True, (0, 0, 0))
    screen.blit(timer_text, (20, 20))

 
    if elapsed >= 90:
        game_state = "clear"

while True:
    screen.fill(WHITE)

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            sys.exit()

        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_SPACE:
                if game_state == "start":
                    game_state = "playing"
                    start_time = time.time()
                    obstacle_list.clear()
                    player_rect.midbottom = (80, ground_y)
                    player_y_vel = 0
                    is_jumping = False
                elif game_state in ["game_over", "clear"]:
                    game_state = "start"

        if event.type == pygame.MOUSEBUTTONDOWN:
            if game_state == "start":
                game_state = "playing"
                start_time = time.time()
                obstacle_list.clear()
                player_rect.midbottom = (80, ground_y)
                player_y_vel = 0
                is_jumping = False

 
    keys = pygame.key.get_pressed()
    if (keys[pygame.K_SPACE] or pygame.mouse.get_pressed()[0]) and not is_jumping and game_state == "playing":
        player_y_vel = -15
        is_jumping = True


    if game_state == "start":
        show_start_screen()
    elif game_state == "playing":
        run_gameplay()
    elif game_state == "game_over":
        show_game_over_screen()
    elif game_state == "clear":
        show_clear_screen()

    pygame.display.update()
    clock.tick(FPS)

