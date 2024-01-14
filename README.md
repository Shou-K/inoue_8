import pygame
import sys
import random

# 初期化
pygame.init()

# 画面の設定
screen_width, screen_height = 600, 400
screen = pygame.display.set_mode((screen_width, screen_height))
pygame.display.set_caption("100戦錬磨")

player_image = pygame.image.load("data/img/chara.png")
enemy_image = pygame.image.load("data/img/enemy.png")
background_image = pygame.image.load("data/img/back.png")

# 色の定義
white = (255, 255, 255)
black = (0, 0, 0)
red = (255, 0, 0)

# プレイヤーの設定
player_rect = player_image.get_rect()
player_rect.center = (screen_width // 2, screen_height - player_rect.height - 10)
player_speed = 35

# 弾の設定
bullet_size = 10
bullet_speed = 7
bullets = []

# 敵の設定
enemy_size = 50
enemy_speed = 3
enemies = []

# スコア
score = 0
font = pygame.font.Font(None, 36)

def draw_bullet(x, y):
    pygame.draw.rect(screen, white, [x, y, bullet_size, bullet_size])

def show_score(score):
    score_text = font.render("Score: " + str(score), True, white)
    screen.blit(score_text, [10, 10])

# ゲームループ
clock = pygame.time.Clock()
accelerate_once = True
game_clear = False

while not game_clear:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            sys.exit()

        # キー入力
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_LEFT:
                player_rect.x -= player_speed
            elif event.key == pygame.K_RIGHT:
                player_rect.x += player_speed
            elif event.key == pygame.K_SPACE:
                bullets.append([player_rect.x + player_rect.width // 2 - bullet_size // 2, player_rect.y])

    # プレイヤーの移動範囲を制限
    player_x = max(0, min(player_rect.x, screen_width - player_rect.width))

    # 弾の移動
    for bullet in bullets:
        bullet[1] -= bullet_speed

    # 敵の生成と移動
    if random.randint(1, 50) == 1:
        enemy_x = random.randint(0, screen_width - enemy_size)
        enemy_y = -enemy_size
        enemies.append([enemy_x, enemy_y])

    for enemy in enemies:
        enemy[1] += enemy_speed

    # 衝突判定
    for bullet in bullets:
        for enemy in enemies:
            if (
                enemy[0] < bullet[0] < enemy[0] + enemy_size
                and enemy[1] < bullet[1] < enemy[1] + enemy_size
            ):
                bullets.remove(bullet)
                enemies.remove(enemy)
                score += 1
    
    if score == 100:
      game_clear = True
                
    if score > 0 and score % 10 == 0 and accelerate_once :
      enemy_speed += 0.6
      accelerate_once = False
      
    if score % 10 != 0:
      accelerate_once = True
      

    # 画面のクリア
    screen.fill(black)

    # 描画
    screen.blit(player_image, player_rect)
    for bullet in bullets:
         draw_bullet(bullet[0], bullet[1])
    for enemy in enemies:
         screen.blit(enemy_image, (enemy[0], enemy[1]))
    show_score(score)

    # 画面更新
    pygame.display.flip()

    # フレーム制御
    clock.tick(60)

font_game_clear = pygame.font.Font(None, 72)
text_game_clear = font_game_clear.render("Game Clear!",True,white)
text_rect_game_clear = text_game_clear.get_rect(center=(screen_width // 2, screen_height // 2))
screen.blit(text_game_clear, text_rect_game_clear)
pygame.display.flip()

pygame.time.wait(2000)
pygame.quit()
sys.exit()
