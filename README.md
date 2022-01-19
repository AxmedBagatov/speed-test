
    import pygame


    class Button:
        def __init__(self, screen, width, height):
            self.width = width
            self.height = height
            self.pass_color = (100, 100, 100)
            self.active_color = (25, 25, 25)

    def print_text(self, message, screen, x, y, font_color=(0, 0, 0), font_type='ComicSansMS.ttf', font_size=30):
        font_type = pygame.font.Font(font_type, font_size)
        text = font_type.render(message, True, font_color)
        screen.blit(text, (x, y))

    def draw(self, screen, x, y, message, action=None, font_size=30):
        mouse = pygame.mouse.get_pos()
        click = pygame.mouse.get_pressed()
        button_sound = pygame.mixer.Sound('button.wav')

        if x < mouse[0] < x + self.width:
            if y < mouse[1] < y + self.height:
                pygame.draw.rect(screen, self.active_color, (x, y, self.width, self.height))

                if click[0] == 1:
                    pygame.mixer.Sound.play(button_sound)
                    pygame.time.delay(300)
                    if action is not None:
                        action()

        else:
            pygame.draw.rect(screen, self.pass_color, (x, y, self.width, self.height))

        self.print_text(message=message, screen=screen, x=x + 10, y=y + 10, font_size=font_size)
