from typing import Any
from pygame import * 
from random import *


window = display.set_mode((700, 500))
background = transform.scale(image.load("galaxy.jpg"), (700, 500))

# mixer.init()
# mixer.music.load('space.ogg')
# mixer.music.set_volume(0.3)
# mixer.music.play()

class GameSprite(sprite.Sprite):
    def __init__(self, player_image, player_x, player_y, size_x, size_y, player_speed):
        sprite.Sprite.__init__(self)

        self.image = transform.scale(image.load(player_image), (size_x, size_y))
        self.speed = player_speed
        
        self.rect = self.image.get_rect()
        self.rect.x = player_x
        self.rect.y = player_y
    def reset(self):
        window.blit(self.image, (self.rect.x, self.rect.y))


bullets = sprite.Group()
class Player(GameSprite):
    def update(self):
        keys = key.get_pressed()
        if keys[K_LEFT] and self.rect.x > 0:
            self.rect.x -= self.speed 
        if keys[K_RIGHT] and self.rect.x < 630:
            self.rect.x += self.speed 

    def fire(self):
        bullet = Bullet("bullet.png", self.rect.x + 20, self.rect.y, 15, 20, 10)
        bullets.add(bullet)
missed = 0


class UFO(GameSprite):
    def update(self):
        global missed 
        self.rect.y += self.speed 
        if self.rect.y > 450:
            self.rect.y = 0
            self.rect.x = randint(0, 630)
            missed += 1


class  Asteroids(GameSprite):
    def update(self):
        global missed 
        self.rect.y += self.speed 
        if self.rect.y > 450:
            self.rect.y = 0
            self.rect.x = randint(0, 630)
            missed += 1




font.init()
font1 = font.Font(None, 80)
win = font1.render("YOU WIN!", 1, (0, 255, 0))
lose = font1.render("YOU LOSE!",1, (255, 0, 0))
font2 = font.Font(None, 36)

class Bullet(GameSprite):
    def update(self):
        self.rect.y -= self.speed
        if self.rect.y < 0:
            self.kill()


rocket = Player("rocket.png", 230, 400, 80, 90, 10)


ufos = sprite.Group()
asteroid = sprite.Group()


    

for i in range(1):
    ufo = UFO("ufo.png", randint(0, 630), 0, 80, 50, randint(1, 5))
    ufos.add(ufo)



hit_ufo =0 
finish = False
FPS = 60 
clock = time.Clock()
killed = 0
game = True 
while game:
    for e in event.get():
        if e.type == QUIT:
            game = False
        if e.type == KEYDOWN:
            if e.key == K_SPACE:
                rocket.fire()



    window.blit(background, (0, 0))


    collide = sprite.groupcollide(ufos, bullets, True, True)

    for c in collide:
        ufo = UFO("ufo.png", randint(0, 630), 0, 80, 50, randint(1, 5))
        ufos.add(ufo)
        killed += 1
        

    text_miss = font2.render("Missed:"+ str(missed), 1, (255, 255, 255))
    window.blit(text_miss, (10, 50))

    text_miss = font2.render("Killed:" + str(killed), 1, (255, 255, 255))
    window.blit(text_miss, (10, 10))

    if finish == False:
        rocket.reset()
        rocket.update()

        ufos.draw(window)
        ufos.update()

        bullets.draw(window)
        bullets.update()



    if sprite.spritecollide(rocket, ufos, False):
        hit_ufo += 1

    if missed >= 3 or hit_ufo>= 2:
        finish = True
        window.blit(lose, (200, 200))

    if killed > 10:
        finish = True
        window.blit(win, (200, 200))

    clock.tick(FPS)
    display.update()
