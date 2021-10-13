# Python贪吃蛇

​	python上机课时闲着没事写了一个贪吃蛇的小游戏玩了玩，之后又加了一些音效啥的东西，代码里都有注释。

​	该程序有声音效果和计分机制，不能暂停。

可以对照注释修改里面的参数。

按下任意键开始游戏，没获得5分时蛇的移动速度会加快，增加游戏难度。

> 运行截图

![](https://i.loli.net/2021/10/13/k1HJNWVwZT8u3Cl.png)

![](https://i.loli.net/2021/10/13/dQrj89nCcN2omJt.png)

![](https://i.loli.net/2021/10/13/XSJELtqorIhaZpB.png)

```python
import pygame
from sys import exit
import random

width = 1920 #800   屏幕宽的像素
hight = 960 #600   屏幕高的像素
ROW = 48    #30     行显示多少个格子
CLO = 96    #40     列显示多少个格子

#颜色定义
white = (255, 255, 255)
yellow = (255, 255, 102)
black = (0, 0, 0)
red = (213, 50, 80)
green = (0, 255, 0)
blue = (50, 153, 213)

head_color = (221, 147, 64)  #头的颜色
snake_color = (19, 161, 14)#蛇身体的颜色
food_color = (254, 39, 0)#食物的颜色
back_color = (222, 240, 242)           #背景颜色
score = 0
last_score = 0 #上次的分数

pygame.init()                                   #pygame初始化
window = pygame.display.set_mode((width,hight)) #新建游戏窗口
pygame.display.set_caption("贪吃蛇小游戏")        #设定标题

clock = pygame.time.Clock()  #用来处理游戏的帧率

#设定字体
font_style = pygame.font.SysFont("SimHei", 66)
score_font = pygame.font.SysFont("SimHei", 35)

#设定背景音乐
pygame.mixer.music.load("music/background.mp3")
pygame.mixer.music.play(loops=-1)  #无限循环播放

#音效对象
eat_music = pygame.mixer.Sound("music/eat.wav") #吃食物音效
big_music = pygame.mixer.Sound("music/big.wav") #升级音效
die_music = pygame.mixer.Sound("music/die.wav") #死亡音效

class Point():          #格子类的定义
    row = 0
    clo = 0
    def __init__(self,row = 0, col = 0):
        self.row = row
        self.clo = col

    def copy(self):
        return Point(row=self.row,col=self.clo)

def gen_food():   #随机生成食物
    while True:
        position = Point(row=random.randint(0,ROW - 1),col=random.randint(0,CLO - 1))
        is_ok = True
        if position.row == head.row and position.clo == head.clo:
            is_ok = False
        for body in snake:
            if body.row == position.row and body.clo == position.clo:
                is_ok = False
                break
        if is_ok:
            break
    return position

def message(msg, color):
    mesg = font_style.render(msg, True, color)
    window.blit(mesg, [width / 4, hight / 4])

def Your_score(score):
    value = score_font.render("Score: " + str(score), True, yellow)
    window.blit(value, [0, 0])

def rect(point, color):  #渲染格子
    left = point.clo * width / CLO
    top = point.row * hight / ROW
    pygame.draw.rect(window, color, (left, top, width / CLO - 0, hight / ROW - 0))

def died(score):
    if score > 0 and score <= 10:
        message("你怎么这么菜！！", red)
    if score > 10 and score <= 20:
        message("你好菜啊！！", red)
    if score == 0:
        message("666！", red)
    if score >= 20 and score <= 30:
        message("有点菜！！", red)
    if score >= 30 and score <= 40:
        message("还行吧！！", red)
    if score >= 40 and score <= 50:
        message("有点东西！！", red)
    if score >= 50 and score <= 70:
        message("牛牛牛！！", red)
    if score > 70:
        message("你就是神！！", red)
    pygame.display.update()
    pygame.time.delay(2000)
    die_music.play()

head = Point(row=ROW / 2, col=CLO / 2)  # 头初始化
snake = [Point(row=ROW / 2, col=CLO / 2 + 1), Point(row=ROW / 2, col=CLO / 2 + 2),
         Point(row=ROW / 2, col=CLO / 2 + 3)]  # 蛇身体初始化
direct = 'left'  # 设定初始方向
food = gen_food()  # 生成食物

#-------------------------------主循环------------------------
def main_loop():
    head = Point(row=ROW / 2, col=CLO / 2)  # 头初始化
    snake = [Point(row=ROW / 2, col=CLO / 2 + 1), Point(row=ROW / 2, col=CLO / 2 + 2),
             Point(row=ROW / 2, col=CLO / 2 + 3)]  # 蛇身体初始化
    direct = 'left'  # 设定初始方向
    food = gen_food()  # 生成食物
    frame = 8  # 帧率，控制游戏快慢
    global score
    score = 0
    is_ok = True
    while is_ok:  # 逻辑主函数
        clock.tick(frame)  # 帧率设置
        for event in pygame.event.get():  # 产生按键中断时进行处理
            if event.type == pygame.QUIT:
                state = False
                pygame.quit()
                exit(0)
            elif event.type == pygame.KEYDOWN:
                if event.key == 97 or event.key == 276:  # a键
                    if direct == 'top' or direct == 'bottom':
                        direct = 'left'
                if event.key == 119 or event.key == 273:  # w键
                    if direct == 'left' or direct == 'right':
                        direct = 'top'
                if event.key == 274 or event.key == 115:  # s键
                    if direct == 'left' or direct == 'right':
                        direct = 'bottom'
                if event.key == 100 or event.key == 275:  # d键
                    if direct == 'top' or direct == 'bottom':
                        direct = 'right'
        # 蛇移动刷新
        snake.insert(0, head.copy())
        if direct == 'left':
            head.clo -= 1
        if direct == 'right':
            head.clo += 1
        if direct == 'top':
            head.row -= 1
        if direct == 'bottom':
            head.row += 1

        # 碰撞检测
        if head.row < 0 or head.row > ROW or head.clo < 0 or head.clo > CLO:
            died(score)
            break

        for point in snake:
            if head.row == point.row and head.clo == point.clo:
                died(score)
                is_ok = False
                break

        # 是否吃到食物
        is_eat = False
        if food.row == head.row and food.clo == head.clo:  # 如果吃到食物
            is_eat = True
            food = gen_food()
            score += 1
            eat_music.play()

        if not is_eat:
            snake.pop()

        #评分每达到5升级，加快一点游戏速度
        if score % 5 == 0 and score != 0 and last_score != score:
            big_music.play()
            frame += 2

        # 渲染背景
        pygame.draw.rect(window, back_color, (0, 0, width, hight))
        # 渲染蛇和食物
        rect(head, head_color)
        rect(food, food_color)
        for body in snake:
            rect(body, snake_color)
        # 让计算机进行刷新
        Your_score(score)
        pygame.display.update()
        last_score = score

is_loop = True

while is_loop:
    window.fill(back_color)
    score_string = "Your Score:" + str(score)
    message(score_string, red)
    pygame.display.update()
    for event in pygame.event.get():  # 产生按键中断时进行处理
        if event.type == pygame.KEYDOWN:
            main_loop()
        elif event.type == pygame.QUIT:
            is_loop = False
            pygame.quit()
            exit(0)
```

如果要运行的话需要有Img和music目录里的资源文件。

下载地址：

链接：https://pan.baidu.com/s/1sj2o8Led6srMEjHUzQD0Ig 
提取码：r4tj

