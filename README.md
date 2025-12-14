# Dont-visit
import pygame
import sys

pygame.init()
width, height = 1000, 700
screen = pygame.display.set_mode((width, height))
pygame.display.set_caption("Clicker Game")
clock = pygame.time.Clock()
font = pygame.font.Font(None, 36)
small_font = pygame.font.Font(None, 24)

# Game state
coins = 0
click_power = 1
auto_income = 0https://github.com/s202344015-droid/Dont-visit/edit/main/README.md
rebirths = 0
max_rebirths = 3
upgrade_levels = [0, 0, 0, 0, 0]  # Wood1, Glass2, Plastic3, Gold4, Diamond5
upgrade_costs = [10, 30, 60, 100, 150]  # Base costs +20 each level
rebirth_cost = 3000

# Colors and positions
black, white, gold, green, red = (0,0,0), (255,255,255), (255,215,0), (0,255,0), (255,0,0)
main_btn = pygame.Rect(50, 50, 150, 150)
shop_btns = [pygame.Rect(250, 50 + i*120, 200, 100) for i in range(5)]
rebirth_btn = pygame.Rect(500, 500, 200, 80)

def draw_text(text, font, color, x, y):
    screen.blit(font.render(text, True, color), (x, y))

running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        if event.type == pygame.MOUSEBUTTONDOWN:
            # Main click button
            if main_btn.collidepoint(event.pos):
                coins += click_power * (2 ** rebirths)
            # Shop buttons
            for i, btn in enumerate(shop_btns):
                if btn.collidepoint(event.pos) and coins >= upgrade_costs[i]:
                    coins -= upgrade_costs[i]
                    upgrade_levels[i] += 1
                    click_power += (i + 2)  # Wood1=2, Glass2=3, etc.
                    auto_income += (i + 2)
                    upgrade_costs[i] += 20
            # Rebirth
            if rebirth_btn.collidepoint(event.pos) and coins >= rebirth_cost and rebirths < max_rebirths:
                coins = 0
                rebirths += 1
                click_power *= 2
                auto_income *= 2

    # Auto income per second (simulated per frame)
    coins += auto_income * (2 ** rebirths) / 60

    # Draw
    screen.fill(black)
    draw_text(f"Gold: {int(coins)}", font, gold, 250, 20)
    draw_text(f"Click Power: {click_power}", small_font, white, 250, 60)
    draw_text(f"Rebirths: {rebirths}/3", small_font, white, 250, 90)
    
    # Main button
    pygame.draw.rect(screen, gold, main_btn)
    draw_text("CLICK!", font, black, 70, 100)
    
    # Shop
    draw_text("UPGRADES", font, green, 260, 20)
    for i, btn in enumerate(shop_btns):
        color = green if coins >= upgrade_costs[i] else red
        pygame.draw.rect(screen, color, btn)
        draw_text(f"{['Wood','Glass','Plastic','Gold','Diamond'][i]} Lv{upgrade_levels[i]}", 
                  small_font, black, btn.x+10, btn.y+10)
        draw_text(f"Cost: {upgrade_costs[i]}", small_font, white, btn.x+10, btn.y+40)
    
    # Rebirth
    color = green if coins >= rebirth_cost and rebirths < max_rebirths else red
    pygame.draw.rect(screen, color, rebirth_btn)
    draw_text("REBIRTH", font, black, 520, 520)
    draw_text(f"Cost: {rebirth_cost}", small_font, white, 520, 560)
    
    pygame.display.flip()
    clock.tick(60)

pygame.quit()
sys.exit()
