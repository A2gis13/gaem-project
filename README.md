# gaem-project
import pygame
import random
import sys

pygame.init()

WIDTH, HEIGHT = 800, 400
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("장애물 피하기 게임")

clock = pygame.time.Clock()
FPS = 60

WHITE = (255, 255, 255)

player_img = pygame.image.load("student.png.png").convert_alpha()
chair_img = pygame.image.load("chair.png.png").convert_alpha()
desk_img = pygame.image.load("desk.png.png").convert_alpha()
books_img = pygame.image.load("books.png.png").convert_alpha()

player_img = pygame.transform.scale(player_img, (60, 60))
chair_img = pygame.transform.scale(chair_img, (40, 60))
desk_img = pygame.transform.scale(desk_img, (40, 60))
books_img = pygame.transform.scale(books_img, (40, 60))

player_rect = player_img.get_rect(midbottom=(80, 340))
player_y_vel = 0
gravity = 0.6
is_jumping = False

obstacle_list = []
obstacle_images = [chair_img, desk_img, books_img]

SPAWN_OBSTACLE = pygame.USEREVENT + 1
pygame.time.set_timer(SPAWN_OBSTACLE, 1500)  # 1.5초마다 장애물 생성

running = True
while running:
    screen.fill(WHITE)

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_SPACE and not is_jumping:
                player_y_vel = -12
                is_jumping = True

        if event.type == SPAWN_OBSTACLE:
            obstacle_img = random.choice(obstacle_images)
            obstacle_rect = obstacle_img.get_rect(midbottom=(900, 340))
            obstacle_list.append((obstacle_img, obstacle_rect))

    player_y_vel += gravity
    player_rect.y += player_y_vel
    if player_rect.bottom >= 340:
        player_rect.bottom = 340
        is_jumping = False

    for i, (img, rect) in enumerate(obstacle_list):
        rect.x -= 5
        obstacle_list[i] = (img, rect)

    for img, rect in obstacle_list:
        if player_rect.colliderect(rect):
            print("충돌! 게임 종료")
            pygame.quit()
            sys.exit()

    screen.blit(player_img, player_rect)
    for img, rect in obstacle_list:
        screen.blit(img, rect)

    pygame.display.update()
    clock.tick(FPS)
