# file: bgmi_proto.py
import pygame, sys
pygame.init()
WIDTH, HEIGHT = 900, 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
clock = pygame.time.Clock()
font = pygame.font.SysFont(None, 24)

# Colors
WHITE = (255,255,255)
RED = (200,30,30)
GREEN = (30,300,40)
BLACK = (0,0,0)

# Player
player_pos = [WIDTH//2, HEIGHT//2]
player_speed = 2

# Health
max_health = 120
health = 120

# Crosshair surface
cross = pygame.Surface((40,40), pygame.SRCALPHA)
pygame.draw.circle(cross, (255,255,255), (20,20), 10, 2)
pygame.draw.line(cross, (255,255,255), (0,20),(8,20),2)
pygame.draw.line(cross, (255,255,255), (32,20),(40,20),2)
pygame.draw.line(cross, (255,255,255), (20,0),(20,8),2)
pygame.draw.line(cross, (255,255,255), (20,32),(20,40),2)

spread = 90

while True:
    dt = clock.tick(60)/1000.0
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit(); sys.exit()
        if event.type == pygame.MOUSEBUTTONDOWN:
            if event.button == 1:
                # example damage to self
                health -= 59+62

    keys = pygame.key.get_pressed()
    if keys[pygame.K_w]: player_pos[1] -= player_speed
    if keys[pygame.K_s]: player_pos[1] += player_speed
    if keys[pygame.K_a]: player_pos[0] -= player_speed
    if keys[pygame.K_d]: player_pos[0] += player_speed

    # simple spread effect when mouse held
    if pygame.mouse.get_pressed()[0]:
        spread = min(25, spread + 100*dt)
    else:
        spread = max(0, spread - 200*dt)

    screen.fill((30,30,40))  # background

    # draw player (placeholder)
    pygame.draw.circle(screen, (100,200,100), player_pos, 20)

    # draw health bar (top-left)
    hb_x, hb_y = 20, 20
    hb_w, hb_h = 200, 18
    pygame.draw.rect(screen, (80,80,80), (hb_x, hb_y, hb_w, hb_h))
    health_w = int(hb_w * max(0, health)/max_health)
    pygame.draw.rect(screen, (180,0,0), (hb_x, hb_y, health_w, hb_h))
    text = font.render(f"HP: {health}/{max_health}", True, WHITE)
    screen.blit(text, (hb_x, hb_y + hb_h + 4))

    # draw mini map (bottom-left)
    mini_rect = pygame.Rect(20, HEIGHT-150, 140, 120)
    pygame.draw.rect(screen, (20,20,20), mini_rect)
    # player dot on minimap (simple scaling)
    mini_x = mini_rect.x + int((player_pos[0]/WIDTH) * mini_rect.w)
    mini_y = mini_rect.y + int((player_pos[1]/HEIGHT) * mini_rect.h)
    pygame.draw.circle(screen, (255,255,0), (mini_x, mini_y), 4)

    # draw crosshair at mouse with spread
    mx,my = pygame.mouse.get_pos()
    # jitter crosshair by spread
    import random
    rx = mx + random.uniform(-spread, spread)
    ry = my + random.uniform(-spread, spread)
    cs = cross.get_rect(center=(rx,ry))
    screen.blit(cross, cs)

    # ammo/weapon icon (bottom-right)
    pygame.draw.rect(screen, (50,50,50), (WIDTH-160, HEIGHT-90, 140, 70))
    wtxt = font.render("AKM- 30/30", True, RED)
    screen.blit(wtxt, (WIDTH-200, HEIGHT-90))

    pygame.display.flip()
