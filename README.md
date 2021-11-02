# Lynx
# //////////////////////////////////////////////////////////////////////////////
#                          Задание крестики-нолики

print('<< Игра крестики-нолики >>\n'
      'Правила: \n'
      '- Игроки ходят по очереди\n'
      '- Координаты клеток вводятся через пробел "X Y" \n'
      '- выигрывает тот, у которого ... \n'
      '- для выхода из игры введите значение координат "401"'
      )
print()

def input_coordinates(player_mumber):
    global play_matrix
    global matrix_size

    message_cod   = 0
    message_text  = ''
    message_value = []
    x1=-1; x2=-1

    # input_value = ' 1 , 0 '
    while True:
        input_value = input("Ход игрока " + str(player_mumber) + '\n координаты: ')
        input_value = input_value.strip().split()
        # input_value = input_value.replace(' ', '')

        # -------------------------------------------------------
        # выход из игры
        if len(input_value) == 0:
            print('Введите координаты еще раз!')
            continue

        if input_value[0] == '401':
            message_cod   = 401
            message_value = [x1,x2]
            message_text = 'Выход из игры!'
            break

        # -------------------------------------------------------
        # проверка на корректный ввод координат

        x1,x2 = map(int,input_value)

        if x1 < 0 or x1 > matrix_size or x2 < 0 or x2 > matrix_size:
            message_cod = 403
            message_text = message_text + 'значения координат находятся за пределами размерности матрицы \n'

        elif play_matrix[x1][x2] == 1 or play_matrix[x1][x2] == 2:
            message_cod = 404
            message_text = message_text + 'значение по данным координатам уже установлено \n'

        if message_cod >402:
            print(message_text)
            print('Введите координаты еще раз')
            continue

        # -------------------------------------------------------
        # введены корректные данные, выходим из функции
        message_cod   = 0
        message_value = [x1,x2]
        break
        # -------------------------------------------------------

    result = {'message_cod': message_cod,'message_value':message_value,'message_text':message_text}

    return result

def show_matrix(play_matrix):
    play_matrix_str = ''

    for row_i in play_matrix:
        str_row = ''
        for col_j in row_i:
            if col_j == 1:   vl = 'X'
            elif col_j == 2: vl = '0'
            else:            vl = col_j

            str_row = str_row + vl+' '

        play_matrix_str = play_matrix_str + str_row + '\n'

    return play_matrix_str

def set_coordinates(x1,x2,set_value='-'):
    global play_matrix
    play_matrix[x1][x2] = set_value

def get_game_result(n,play_matrix):
    result = 0
    p1 = [0, 0, 0, 0]
    p2 = [0, 0, 0, 0]

    for i in range(n+1):
        p1_0 = 0; p2_0 = 0
        p1_1 = 0; p2_1 = 0
        for j in range(n+1):
            # подсчет по строкам
            if play_matrix[i][j]   == 1: p1_1 += 1
            elif play_matrix[i][j] == 2: p2_1 += 1

            # подсчет по колонкам
            if play_matrix[j][i]   == 1: p1_0 += 1
            elif play_matrix[j][i] == 2: p2_0 += 1

        if p1[1] < p1_0: p1[0] = p1_0
        if p2[1] < p2_0: p2[0] = p2_0
        if p1[1] < p1_1: p1[1] = p1_1
        if p2[1] < p2_1: p2[1] = p2_1

        # подсчет по диагоналям
        if play_matrix[i][i]   == 1: p1[2] += 1
        elif play_matrix[i][i] == 2: p2[2] += 1

        if play_matrix[n-i][i]   == 1: p1[3] += 1
        elif play_matrix[n-i][i] == 2: p2[3] += 1

    if max(p1)==n+1: result = 1
    if max(p2)==n+1: result = 2

    # print(p1,p2)

    return result

#  -----------------------------------------------------------------------
#                         начальные данные
matrix_size = 2
steps_max   = (matrix_size+1)**2
game_result = 0

count_steps   = 0
сontinue_play = True
stop_game     = False

#  -----------------------------------------------------------------------
# создаем матрицу
# play_matrix = [['-','-','-'],['-','-','-'],['-','-','-']]

play_matrix = [['-']*(matrix_size+1) for i in range(matrix_size+1)]

print(show_matrix(play_matrix))

# -----------------------------------------------------------------------
#                           начинаем игру

while сontinue_play:
    count_steps += 1

    if count_steps%2==0: player_mumber = 2
    else: player_mumber = 1

    # -------------------------------------------------------
    # 1. ввод координат
    x1 = -1; x2 = -1

    answer_message = input_coordinates(player_mumber)
    if answer_message['message_cod'] == 401:
        game_result = 401
        break

    if answer_message['message_cod'] == 0:
        x1,x2 = answer_message['message_value']

    # -------------------------------------------------------
    # 3. устанавка значения по координатам

    set_coordinates(x1, x2, player_mumber)
    print(show_matrix(play_matrix))

    # -------------------------------------------------------
    # 4. проверяем на выигрыш

    game_result = get_game_result(matrix_size, play_matrix)

    if game_result != 0:
        stop_game = True

    # -------------------------------------------------------
    # 5. проверка на окончание игры (если раньше выигрыш или количество ходов больше размерности матрицы)
    if count_steps >= steps_max or stop_game:
        сontinue_play = False #break

    # -------------------------------------------------------


if game_result == 401: test_message = 'Выход из игры!'
elif game_result == 0: test_message = '"Ничья!"'
else: test_message = 'Выиграл игрок № '+str(game_result)

print(test_message)
