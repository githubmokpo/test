import random
import os
import time
import keyboard

# 게임 보드 초기화
board = [[' ' for _ in range(10)] for _ in range(20)]

# 테트리스 블록들의 형태
tetrominoes = [
    [[1, 1, 1, 1]],
    [[1, 1, 1, 1]],
    [[1, 1, 1, 0], [1]],
    [[1, 1, 1, 0], [0, 0, 1]],
    [[1, 1, 1, 0], [0, 1]],
    [[1, 1, 0, 0], [1, 1]],
    [[1, 1, 0], [0, 1, 1]],
]

# 현재 블록의 위치와 모양 초기화
current_tetromino = random.choice(tetrominoes)
current_tetromino_rotation = 0
current_tetromino_x = 3
current_tetromino_y = 0

# 게임 진행 플래그 초기화
game_over = False

def draw_board():
    os.system('clear')
    for row in board:
        print('|', end='')
        for cell in row:
            print(cell, end='')
        print('|')
    print('+-' * 10 + '+')

def draw_tetromino():
    for i, row in enumerate(current_tetromino[current_tetromino_rotation]):
        for j, cell in enumerate(row):
            if cell:
                board[current_tetromino_y + i][current_tetromino_x + j] = 'X'

def clear_tetromino():
    for i, row in enumerate(current_tetromino[current_tetromino_rotation]):
        for j, cell in enumerate(row):
            if cell:
                board[current_tetromino_y + i][current_tetromino_x + j] = ' '

def move_down():
    global current_tetromino_y
    clear_tetromino()
    current_tetromino_y += 1
    draw_tetromino()

def move_left():
    global current_tetromino_x
    clear_tetromino()
    current_tetromino_x -= 1
    draw_tetromino()

def move_right():
    global current_tetromino_x
    clear_tetromino()
    current_tetromino_x += 1
    draw_tetromino()

def rotate():
    global current_tetromino_rotation
    clear_tetromino()
    current_tetromino_rotation = (current_tetromino_rotation + 1) % len(current_tetromino)
    draw_tetromino()

def check_collision():
    for i, row in enumerate(current_tetromino[current_tetromino_rotation]):
        for j, cell in enumerate(row):
            if cell and board[current_tetromino_y + i][current_tetromino_x + j] != ' ':
                return True
    return False

def place_tetromino():
    for i, row in enumerate(current_tetromino[current_tetromino_rotation]):
        for j, cell in enumerate(row):
            if cell:
                board[current_tetromino_y + i][current_tetromino_x + j] = 'X'

def remove_completed_lines():
    global board
    board = [row for row in board if ' ' in row]
    while len(board) < 20:
        board.insert(0, [' '] * 10)

def game_loop():
    global game_over

    while not game_over:
        draw_board()
        draw_tetromino()

        if keyboard.is_pressed('a'):
            move_left()
        if keyboard.is_pressed('d'):
            move_right()
        if keyboard.is_pressed('s'):
            move_down()
        if keyboard.is_pressed('w'):
            rotate()

        if not keyboard.is_pressed('s'):
            time.sleep(0.5)
            move_down()

        if check_collision():
            place_tetromino()
            remove_completed_lines()
            current_tetromino = random.choice(tetrominoes)
            current_tetromino_rotation = 0
            current_tetromino_x = 3
            current_tetromino_y = 0
            game_over = check_collision()

if __name__ == '__main__':
    try:
        game_loop()
    except KeyboardInterrupt:
        pass
