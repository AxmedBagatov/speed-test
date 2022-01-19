import pygame
from pygame.locals import *
import sys
import time
import random

###### размер окна 750 x 500
import Butttton

class Game:

    def __init__(self):
        self.w = 750
        self.h = 500
        self.engbutton = False
        self.rusbutton = True
        self.reset = True
        self.active = False
        self.input_text = ''
        self.word = ''
        self.records = 0
        self.highscore = 0
        self.res4 = 0
        self.time_start = 0
        self.total_time = 0
        self.accuracy = '0%'
        self.results = 'Time:0 Accuracy:0 % Wpm:0 '
        self.wpm = 0
        self.end = False
        self.HEAD_C = (0, 130, 40)
        self.TEXT_C = (240, 240, 240)
        self.RESULT_C = (0, 0, 205)

        pygame.init()
        self.open_img = pygame.image.load('type-speed-open.png')
        self.open_img = pygame.transform.scale(self.open_img, (self.w, self.h))

        Icon = pygame.image.load('icon_game.png')
        pygame.display.set_icon(Icon)

        self.screen = pygame.display.set_mode((self.w, self.h))
        pygame.display.set_caption('Тест на скорость печати')

        self.music = pygame.mixer.music.load("Sound.mp3")

    def draw_text(self, screen, msg, y, fsize, color):
        font = pygame.font.Font(None, fsize)
        text = font.render(msg, 1, color)
        text_rect = text.get_rect(center=(self.w / 2, y)) #\\\\\\\ по центру
        screen.blit(text, text_rect)
        pygame.display.update()
        # screen.blit(text, (x, y))   // свои коор

    def get_sentence(self):
        f = open('sentences.txt',encoding='utf-8').read()
        sentences = f.split('\n')
        sentence = random.choice(sentences)
        return sentence

    def get_sentence_eng(self):
        f = open('sentences_eng.txt').read()
        sentences = f.split('\n')
        sentence = random.choice(sentences)
        return sentence

    def show_results(self, screen):
        if (not self.end):
            # Расчет времени
            self.total_time = time.time() - self.time_start

            # Расчет точности
            count = 0
            for i, c in enumerate(self.word):
                try:
                    if self.input_text[i] == c:
                        count += 1
                except:
                    pass
            self.accuracy = count / len(self.word) * 100

            # Расчет количества слов в минуту
            self.wpm = len(self.input_text) * 60 / ( self.total_time)
            self.end = True
            print(self.total_time)
            self.results = 'Время:' + str(round(self.total_time)) + " сек   Точность:" + str(
                round(self.accuracy)) + "%" + '   кол-во букв в минуту: ' + str(round(self.wpm))
            # Загрузка иконки
            self.time_img = pygame.image.load('icon.png')
            self.time_img = pygame.transform.scale(self.time_img, (150, 150))
            # screen.blit(self.time_img, (80,320))
            screen.blit(self.time_img, (self.w / 2 - 75, self.h - 140))
            self.draw_text(screen, "", self.h - 70, 26, (100, 100, 100))

            res1 = self.accuracy * 0.1 * 5
            res2 = self.total_time * 5
            res3 = self.wpm
            self.res4 = res1 + res2 + res3
            self.records = ' Ваш счет: ' + str(round(self.res4))
            print(self.results)
            print(int(self.res4))

            pygame.display.update()

    def run(self):
        self.reset_game()
        self.running = True
        while (self.running):
            clock = pygame.time.Clock()
            self.screen.fill((0, 0, 0), (50, 250, 650, 50))
            pygame.draw.rect(self.screen, self.HEAD_C, (50, 250, 650, 50), 2)
            # Обновление текста пользовательского ввода
            self.draw_text(self.screen, self.input_text, 274, 26, (250, 250, 250))
            pygame.display.update()
            for event in pygame.event.get():
                if event.type == QUIT:
                    self.running = False
                    sys.exit()
                elif event.type == pygame.MOUSEBUTTONUP:
                    x, y = pygame.mouse.get_pos()
                    # Расположение окна ввода
                    if (x >= 50 and x <= 650 and y >= 250 and y <= 300):
                        self.active = True
                        self.input_text = ''
                        self.time_start = time.time()
                        # Расположение кнопки сброса
                    if (x >= 310 and x <= 510 and y >= 390 and self.end):
                        self.reset_game()
                        x, y = pygame.mouse.get_pos()



                elif event.type == pygame.KEYDOWN:
                    if self.active and not self.end:
                        if event.key == pygame.K_RETURN:
                            print(self.input_text)
                            self.show_results(self.screen)
                            print(self.results)

                            self.draw_text(self.screen, self.results, 335, 28, self.RESULT_C)
                            self.draw_text(self.screen, str(self.records), 210, 28, self.RESULT_C)
                            
                            self.end = True

                        elif event.key == pygame.K_BACKSPACE:
                            self.input_text = self.input_text[:-1]
                        else:
                            try:
                                self.input_text += event.unicode
                            except:
                                pass



            pygame.display.update()

        clock.tick(60)

    def reset_game(self):

        pygame.display.update()

        self.reset = False
        self.end = False

        self.input_text = ''
        self.word = ''
        self.time_start = 0
        self.total_time = 0
        self.wpm = 0

        # Получаем случайное предложение
        self.getlang()
        if (not self.word): self.reset_game()
        # Загрузка окна
        self.screen.fill((0, 0, 0))

        msg = "Тест на скорость печати"
        self.draw_text(self.screen, msg, 80, 80, self.HEAD_C)
        # Отрисовка поля ввода
        pygame.draw.rect(self.screen, (255, 192, 85), (50, 250, 650, 50), 2)

        # Отрисовка строки предложения
        self.draw_text(self.screen, self.word, 150, 25, self.TEXT_C)

        pygame.display.update()

    def engtext(self):
        self.engbutton = True
        self.rusbutton = False

    def rustext(self):
        self.engbutton = False
        self.rusbutton = True

    def getlang(self):
        if self.rusbutton == True and self.engbutton == False:
            self.word = self.get_sentence()
        elif self.rusbutton == False and self.engbutton == True:
            self.word = self.get_sentence_eng()

    def musicON(self):
        pygame.mixer.music.play(-1)

    def musicOFF(self):
        pygame.mixer.music.pause()

    def gamemenu(self):
        pygame.display.update()
        self.gamemenu = True
        while (self.gamemenu):
            for event in pygame.event.get():
                if event.type == QUIT:
                    self.gamemenu = False
                    sys.exit()

            self.draw_text(self.screen, 'Настройки', 350 , 30, (0, 130, 40))

            pygame.display.update()
            button1 = Butttton.Button(self.screen, 300, 200)
            button1.draw(self.screen, 250, 20, 'Старт Игры', self.run , 30)

            self.START = pygame.image.load('START.jpg')
            self.START = pygame.transform.scale(self.START, (300, 200))
            self.screen.blit(self.START, (250, 20))

            pygame.display.update()
            button2 = Butttton.Button(self.screen, 150, 50)
            button2.draw(self.screen, 50, 300, '', self.musicON , 30)


            self.SoundImageOn = pygame.image.load('Soundimage.jpg')
            self.SoundImageOn = pygame.transform.scale(self.SoundImageOn, (150, 50))
            self.screen.blit(self.SoundImageOn, (50, 300))

            pygame.display.update()
            button3 = Butttton.Button(self.screen, 150, 50)
            button3.draw(self.screen, 500, 300, '', self.musicOFF , 0)

            self.NOSoundImageOn = pygame.image.load('NOSoundimage.jpg')
            self.NOSoundImageOn = pygame.transform.scale(self.NOSoundImageOn, (150, 50))
            self.screen.blit(self.NOSoundImageOn, (500, 300))


            pygame.display.update()
            button4 = Butttton.Button(self.screen, 150, 50)
            button4.draw(self.screen, 50, 400, '', self.rustext, 30)


            button5 = Butttton.Button(self.screen, 150, 50)
            button5.draw(self.screen, 500, 400, '', self.engtext, 30)

            self.RUS = pygame.image.load('RUS.jpg')
            self.RUS = pygame.transform.scale(self.RUS, (150, 50))
            self.screen.blit(self.RUS, (50, 400))

            self.ENG = pygame.image.load('ENG.jpg')
            self.ENG = pygame.transform.scale(self.ENG, (150, 50))
            self.screen.blit(self.ENG, (500, 400))




Game().gamemenu()
