import pygame
from random import randint

pygame.init()

width, height = 800, 600
FPS = 60
screen = pygame.display.set_mode((width, height))
clock = pygame.time.Clock()

pygame.display.set_caption('Flappy bird')

font1 = pygame.font.Font(None, 35)
font2 = pygame.font.Font(None, 80)

image_back_ground = pygame.image.load('images/background.png')
image_bird = pygame.image.load('images/bird.png')
image_tile_top = pygame.image.load('images/tile_top.png')
image_tile_bottom = pygame.image.load('images/tile_bottom.png')

player_y, speed_y, fast = height // 2, 0, 0  # speed_y - изменение скорости персонажа, fast - ускорение
player = pygame.Rect(width // 3, player_y, 34, 24)
frame = 0  # для покадровой отрисовки

state = 'start'
timer = 10

tiles = []  # список труб
skys = []  # список для фона
tile_scores = []  # список труб для просчитывания количества очков

tile_speed = 3  # скорость движения труб
tile_size = 200
tile_pos = height // 2  # позиция прохода

skys.append(pygame.Rect(0, 0, 288, 600))

lives = 3  # количество жизней
scores = 0

running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    click = pygame.mouse.get_pressed()[0] or pygame.key.get_pressed()[pygame.K_SPACE]
    # проверка для начала игры (какие кнопки нажаты)
    if timer > 0:
        timer -= 1  # для отслеживания времени

    frame = (frame + 0.2) % 3  # выделение на картинки часть с нужным кадром (0.2 для более медленной анимации)
    title_speed = 3 + scores // 100  # изменение скорости после прохождения трубы

    for i in range(len(skys) - 1, -1, -1):
        sky = skys[i]
        sky.x -= tile_speed // 2  # движение неба

        if sky.right < 0:
            skys.remove(sky)  # удаление в случае, если небо ушло за пределы поля

    if skys[len(skys) - 1].right <= width:
        skys.append(pygame.Rect(skys[len(skys) - 1].right, 0, 288, 600))
        # отслеживание, чтобы весь фон был залит и добавление нового

    for i in range(len(tiles) - 1, -1, -1):
        tile = tiles[i]
        tile.x -= tile_speed  # непрерывное движение труб

        if tile.right < 0:
            tiles.remove(tile)  # для удаления трубы, если та уйдет за границу
            if tile in tile_scores:
                tile_scores.remove(tile)  # удаление труб, когда произошло столкновение

    if state == 'start':
        if click and timer == 0 and len(tiles) == 0:
            state = 'play'

        player_y += (height // 2 - player_y) * 0.1  # стремление птички в центр экрана (сколько ей не хватает до него)
        player.y = player_y  # возвращение птицы в центр

    elif state == 'play':
        if click:
            fast = -2  # даст нам в формуле для скорости вычитание 1
        else:
            fast = 0

        player_y += speed_y
        speed_y = (speed_y + fast + 1) * 0.98  # умножение на 0.98 для плавности, 1 для увеличения скорости
        player.y = player_y

        if len(tiles) == 0 or tiles[len(tiles) - 1].x < width - 200:  # создание новых труб и условия на них
            tiles.append(pygame.Rect(width, 0, 52, tile_pos - tile_size // 2))
            tiles.append(pygame.Rect(width, tile_pos + tile_size // 2, 52, height - tile_pos + tile_size // 2))

            tile_pos += randint(-100, 100)  # изменение позиции прохода
            if tile_pos < tile_size:
                tile_pos = tile_size  # проверка на то, что проход подходит для птички
            elif tile_pos > height - tile_size:
                tile_pos = height - tile_size

        if player.top < 0 or player.bottom > height:  # проверка на то, не вышла ли птичка за экран
            state = 'fall'

        for tile in tiles:
            if player.colliderect(tile):  # проверка на пересечение по прямоугольнику
                state = 'fall'

            if tile.right < player.left and tile not in title_scores:
                # проверка на прохождение птички трубы
                tile_scores.append(tile)  # добавление трубы, как уже пройденной
                scores += 5  # подсчет количества очков
    elif state == 'fall':
        speed_y, fast = 0, 0
        tile_pos = height // 2  # возвращение трубы в исходное положение

        lives -= 1  # уменьшение жизни на единицу
        if lives > 0:
            state = 'start'
            timer = 60
        else:
            state = 'game over'
            timer = 180
    else:
        player_y += speed_y
        speed_y = (speed_y + fast + 1) * 0.98
        player.y = player_y  # передвижение птицы до завершения игры (падение)

if timer == 0:
    running = False  # конец игры

for sky in skys:
    screen.blit(image_back_ground, sky)  # отрисовка неба

for tile in tiles:  # отрисовка труб
    if tile.y == 0:  # определение верхней трубы
        rect = image_tile_top.get_rect(bottomleft=tile.bottomleft)  # совмещение двух картинок
        screen.blit(image_tile_top, rect)
    else:
        rect = image_tile_bottom.get_rect(topleft=tile.topleft)
        screen.blit(image_tile_bottom, rect)

image = image_bird.subsurface(34 * int(frame), 0, 34, 24)  # выбор конкретной части изображения
image = pygame.transform.rotate(image, -speed_y * 2)  # вращение изображения пр поднятии и опускании
screen.blit(image, player)

text = font1.render('Очки: ' + str(scores), 1, pygame.Color('black'))  # 1, чтобы шрифт был гладкий
screen.blit(text, (10, 10))

text = font1.render('Жизни: ' + str(lives), 1, pygame.Color('black'))
screen.blit(text, (10, height - 30))

pygame.display.update()
clock.tick(FPS)
pygame.quit()
