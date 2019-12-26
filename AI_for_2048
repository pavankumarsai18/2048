import pygame
import random
import neat
import os
import time
import pickle

# start pygame setup
pygame.init()
# create the canvas
(width, height) = (400, 450)
# create the screen
screen = pygame.display.set_mode((width, height))

# caption and icons
pygame.display.set_caption("2048")
icon = pygame.image.load("game-controller.png")
pygame.display.set_icon(icon)

gen = 0
# TODO Add a deep neural-net
# TODO Add "Game Over" or "Won the game" Display after finishing the current game

class Box:
    # TODO Add a screen varaible and pass that to the draw function
    side_length = 97
    color = [245, 158, 66]
    font = pygame.font.SysFont('Arial', side_length//2)

    def __init__(self, x, y):
        # setting the coordinates of the box
        self.x = x
        self.y = y
        # value of the box is either 2 or 4 initially
        self.val = random.randint(1, 2)*2
        self.font_size = 0

    def double(self):
        """whenever two boxes collide we double the value and change font"""
        self.val *= 2
        self.font = pygame.font.SysFont('Arial', int(self.side_length//2+self.val/5))

    def draw(self):
        color = {2: [199, 198, 193],
                  4: [240, 227, 165],
                  8:[250, 175, 35],
                  16:[255, 117, 66],
                  32:[204, 74, 27],
                  64:[204, 74, 27],
                  128: [232, 229, 63],
                  256:[199, 196, 24],
                  512:[168, 166, 7],
                  1024:[204, 74, 27],
                  2048:[255, 117, 66]}
        font_sz = {
            2: 25,
            4: 25,
            8: 25,
            16: 23,
            32: 20,
            64: 20,
            128: 15,
            256: 15,
            512: 15,
            1024: 10,
            2048: 10
        }
        if self.get_val() < 2048:
            self.color = color[self.val]
            self.font_size = font_sz[self.val]
        else:
            temp = self.val
            while temp > 2048:
                temp /= 2
            self.color = color[temp]
            self.font_size = font_sz[self.val]

        # draws a rectangle to the screen
        pygame.draw.rect(screen, self.color,[self.x, self.y, self.side_length, self.side_length])

        # adjusting the x coordinate to move left as the value of the box doubles
        x_ = (self.x + self.side_length//2 - (len(str(self.val))))
        y_ = self.y - 2 + self.side_length//2

        txt_cor = (x_, y_)
        # adjusting the font_size to decrease its value as the val increases
        self.font = pygame.font.SysFont('Arial', self.font_size)
        # draw the text to the screen
        screen.blit(self.font.render(str(self.val), True, (0, 0, 0)), txt_cor)
        pygame.display.update()

    def get_val(self):
        # Returns the value of the Box
        return self.val

    def update_coordinates(self, i, j):
        # updating the coordinates of the boxes
        self.y = i*(self.side_length+3) + 1.5
        self.x = j*(self.side_length+3) + 1.5


class Board:
    # TODO change the background color to match the Game

    def __init__(self):
        self.boxes = [[None for j in range(4)] for i in range(4)]
        # a = Box(0,0)
        # b = Box(100,100)
        # c = Box(200,100)
        # a.double()
        # a.double()
        # b.double()
        # c.double()
        # self.boxes[0][0] = a
        # self.boxes[2][2] = b
        # self.boxes[3][2] = c
        rand_int_x = random.randint(2, 3)
        rand_int_y = random.randint(2, 3)

        self.boxes[rand_int_x][rand_int_y] = Box(100*(rand_int_x-1),100*(rand_int_y - 1))
        self.score = 0
        # 4 * length of the side of a box
        self.side_length = 400
        # color background color of the board should be white
        self.bg_color = (255, 255, 255)

    def print_board(self):
        # Prints boxes to terminal/console
        for i in range(len(self.boxes)):
            print("[", end=" ")
            for j in range(len(self.boxes[i])):
                if self.boxes[i][j] is not None:
                    print(self.boxes[i][j].get_val(), end=" ")
                else:
                    print("None", end=" ")
            print("]")
        print("---------------")

    def draw_board(self):
        # draws the board
        pygame.draw.rect(screen, self.bg_color, [0, 0, self.side_length, self.side_length])

        # draw 8 lines
        # vertical lines
        for i in range(4):
            pygame.draw.line(screen, (0, 0, 0), ((i+1)*self.side_length//4, 0 ),( (i+1)*self.side_length//4 , self.side_length), 4)

        # horizontal lines
        for i in range(4):
            pygame.draw.line(screen, (0, 0, 0), (0, (i + 1) * self.side_length // 4),
                             (self.side_length, (i + 1) * self.side_length // 4), 4)

        pygame.display.update()

    def draw_boxes(self):
        for i in range(len(self.boxes)):
            for j in range(len(self.boxes[i])):
                if self.boxes[i][j] is not None:
                    self.boxes[i][j].draw()

        # Drawing Score
        font_size = 15
        font = pygame.font.SysFont('Arial', font_size)
        # draw the text to the screen
        score_msg = "Score :" + str(self.score)
        screen.blit(font.render(score_msg, True, (0, 0, 0)), (200,425))
        pygame.display.update()

    def game_over(self):
        empty_boxes = 0

        for i in range(len(self.boxes)):
            for j in range(len(self.boxes[i])):
                if self.boxes[i][j] is None:
                    empty_boxes += 1

        if empty_boxes != 0:
            return (False, self.score)

        # checks all the adjacent horizontal
        for i in range(len(self.boxes)):
            for j in range(len(self.boxes[i]) - 1):
                if self.boxes[i][j].get_val() == self.boxes[i][j+1].get_val():
                    return (False, self.score)

        # check all the adj vertical positions
        for j in range(len(self.boxes)):
            for i in range(len(self.boxes[j]) - 1):
                if self.boxes[i][j].get_val() == self.boxes[i+1][j].get_val():
                    return (False, self.score)
        return (True, self.score)

    # moves each box to the left most possible vacant place and merges two boxes into one when
    # merge condition is satisfied
    def swipe_right(self):
        check_list = [self.boxes[i].copy() for i in range(len(self.boxes))]
        changed = False
        vacant_indices = []
        # First we move each block to its right most possible position
        for i in range(len(self.boxes)):
            for j in range(len(self.boxes[i]) - 2,-1,-1):
                j_ = j
                while len(self.boxes[i]) - 1 > j_ >= 0 and self.boxes[i][j_] is not None and self.boxes[i][j_+1] is None:
                    self.boxes[i][j_], self.boxes[i][j_+1] = self.boxes[i][j_+1], self.boxes[i][j_]
                    j_ += 1

        # Next we check for al possible merges in each row
        for i in range(len(self.boxes)):
            for j in range(len(self.boxes[i]) - 1):
                if self.boxes[i][j] is not None and self.boxes[i][j+1] is not None:
                    if self.boxes[i][j].get_val() == self.boxes[i][j+1].get_val():
                        self.boxes[i][j+1].double()
                        self.boxes[i][j] = None
                        self.score += self.boxes[i][j+1].get_val()

        # Finally Again we move each block to its right most possible position
        for i in range(len(self.boxes)):
            for j in range(len(self.boxes[i]) - 2,-1, -1):
                j_ = j
                while len(self.boxes[i]) - 1 > j_ >= 0 and self.boxes[i][j_] is not None and self.boxes[i][j_+1] is None:
                    self.boxes[i][j_], self.boxes[i][j_+1] = self.boxes[i][j_+1], self.boxes[i][j_]
                    j_ += 1

        for i_ in range(len(self.boxes)):
            for j_ in range(len(self.boxes[i_])):
                if self.boxes[i_][j_] is not None and check_list[i_][j_] is not None:
                    if self.boxes[i_][j_].get_val() != check_list[i_][j_].get_val():
                        changed = True
                        break
                        break
                elif (self.boxes[i][j] is None and check_list[i_][j_] is not None) or (self.boxes[i][j] is not None and check_list[i_][j_] is None):
                    changed = True
                    break
                    break
        if changed:
            # Next we populate the vacant_indices in a list
            # and update the coordinates of each box
            for i in range(len(self.boxes)):
                for j in range(len(self.boxes[i])):
                    if self.boxes[i][j] is None:
                        vacant_indices.append((i,j))
                    else:
                        self.boxes[i][j].update_coordinates(i, j)

        return (changed,vacant_indices)

    def swipe_left(self):
        check_list = [self.boxes[i].copy() for i in range(len(self.boxes))]
        changed = False
        vacant_indices = []
        # Next we move each block to the left most possible position
        for i in range(len(self.boxes)):
            for j in range(1,len(self.boxes[i])):
                j_ = j
                while len(self.boxes[i]) >= j_ >= 1 and self.boxes[i][j_ - 1] is None and self.boxes[i][j_] is not None:
                    self.boxes[i][j_ - 1], self.boxes[i][j_] = self.boxes[i][j_], self.boxes[i][j_-1]
                    j_ -= 1

        # first we check for al possible merges in each row
        for i in range(len(self.boxes)):
            for j in range(len(self.boxes[i]) - 1):
                if self.boxes[i][j] is not None and self.boxes[i][j+1] is not None:
                    if self.boxes[i][j].get_val() == self.boxes[i][j+1].get_val():
                        self.boxes[i][j].double()
                        self.boxes[i][j+1] = None
                        self.score += self.boxes[i][j].get_val()

        # Next we move each block to the left most possible position
        for i in range(len(self.boxes)):
            for j in range(1,len(self.boxes[i])):
                j_ = j
                while len(self.boxes[i]) >= j_ >= 1 and self.boxes[i][j_ - 1] is None and self.boxes[i][j_] is not None:
                    self.boxes[i][j_ - 1], self.boxes[i][j_] = self.boxes[i][j_], self.boxes[i][j_-1]
                    j_ -= 1
        for i_ in range(len(self.boxes)):
            for j_ in range(len(self.boxes[i_])):
                if self.boxes[i_][j_] is not None and check_list[i_][j_] is not None:
                    if self.boxes[i_][j_].get_val() != check_list[i_][j_].get_val():
                        changed = True
                        break
                        break
                elif (self.boxes[i][j] is None and check_list[i_][j_] is not None) or (self.boxes[i][j] is not None and check_list[i_][j_] is None):
                    changed = True
                    break
                    break
        if changed:
            # Next we populate the vacant_indices in a list
            # and update the coordinates of each box
            for i in range(len(self.boxes)):
                for j in range(len(self.boxes[i])):
                    if self.boxes[i][j] is None:
                        vacant_indices.append((i,j))

        return (changed,vacant_indices)

    def swipe_up(self):
        check_list = [self.boxes[i].copy() for i in range(len(self.boxes))]
        changed = False

        vacant_indices = []
        # Then we move all the non None block to the top most place
        for j in range(len(self.boxes)):
            for i in range(1, len(self.boxes[j])):
                temp = i
                while self.boxes[temp][j] is not None and self.boxes[temp-1][j] is None and 1 <= temp < 4:
                    self.boxes[temp][j], self.boxes[temp-1][j] = self.boxes[temp-1][j], self.boxes[temp][j]
                    temp -= 1

        # first  we look at all possible merges in a column
        for j in range(len(self.boxes)):
            for i in range(1,len(self.boxes[j])):
                if self.boxes[i][j] is not None and self.boxes[i - 1][j] is not None:
                    if self.boxes[i][j].get_val() == self.boxes[i-1][j].get_val():
                        self.boxes[i-1][j].double()
                        self.score += self.boxes[i-1][j].get_val()
                        self.boxes[i][j] = None

        # Then we move all the non None block to the top most place
        for j in range(len(self.boxes)):
            for i in range(1, len(self.boxes[j])):
                temp = i
                while self.boxes[temp][j] is not None and self.boxes[temp-1][j] is None and 1 <= temp < 4:
                    self.boxes[temp][j], self.boxes[temp-1][j] = self.boxes[temp-1][j], self.boxes[temp][j]
                    temp -= 1

        for i_ in range(len(self.boxes)):
            for j_ in range(len(self.boxes[i_])):
                if self.boxes[i_][j_] is not None and check_list[i_][j_] is not None:
                    if self.boxes[i_][j_].get_val() != check_list[i_][j_].get_val():
                        changed = True
                        break
                        break
                elif (self.boxes[i][j] is None and check_list[i_][j_] is not None) or (self.boxes[i][j] is not None and check_list[i_][j_] is None):
                    changed = True
                    break
                    break
        if changed:
            # Check for each vacant place and populate it in the list
            for i in range(len(self.boxes)):
                for j in range(len(self.boxes[i])):
                    if self.boxes[i][j] is None:
                        vacant_indices.append((i, j))

        return (changed, vacant_indices)

    def swipe_down(self):
        check_list = [self.boxes[i].copy() for i in range(len(self.boxes))]
        changed = False
        vacant_indices = []
        # Then we move all the non None block to the top most place
        for j in range(len(self.boxes)):
            for i in range(len(self.boxes[j]) - 2, -1, -1):
                temp = i
                while self.boxes[temp][j] is not None and 0 <= temp < 3 and self.boxes[temp + 1][j] is None :
                    self.boxes[temp][j], self.boxes[temp + 1][j] = self.boxes[temp + 1][j], self.boxes[temp][j]
                    temp += 1

        # first  we look at all possible merges in a column
        for j in range(len(self.boxes)):
            for i in range(0, len(self.boxes[j]) - 1):
                if self.boxes[i][j] is not None and self.boxes[i + 1][j] is not None:
                    if self.boxes[i][j].get_val() == self.boxes[i + 1][j].get_val():
                        self.boxes[i + 1][j].double()
                        self.score += self.boxes[i + 1][j].get_val()
                        self.boxes[i][j] = None

        # Then we move all the non None block to the top most place
        for j in range(len(self.boxes)):
            for i in range(len(self.boxes[j]) - 2, -1, -1):
                temp = i
                while self.boxes[temp][j] is not None and 0 <= temp < 3 and self.boxes[temp + 1][j] is None :
                    self.boxes[temp][j], self.boxes[temp + 1][j] = self.boxes[temp + 1][j], self.boxes[temp][j]
                    temp += 1
        for i_ in range(len(self.boxes)):
            for j_ in range(len(self.boxes[i_])):
                if self.boxes[i_][j_] is not None and check_list[i_][j_] is not None:
                    if self.boxes[i_][j_].get_val() != check_list[i_][j_].get_val():
                        changed = True
                        break
                        break
                elif (self.boxes[i][j] is None and check_list[i_][j_] is not None) or (self.boxes[i][j] is not None and check_list[i_][j_] is None):
                    changed = True
                    break
                    break
        if changed:
            # Check for each vacant place and populate it in the list
            for i in range(len(self.boxes)):
                for j in range(len(self.boxes[i])):
                    if self.boxes[i][j] is None:
                        vacant_indices.append((i, j))

        return (changed, vacant_indices)

    def move(self, direction, count):
        if direction == 0:
            changed, vacant_spaces = self.swipe_left()
        elif direction == 1:
            changed, vacant_spaces = self.swipe_right()
        elif direction == 2:
            changed, vacant_spaces = self.swipe_up()
        else:
            changed, vacant_spaces = self.swipe_down()

        if changed or count == 0:
            # get a random index from the vacant_spaces
            # print(changed, count)
            # print("vacant spaces",vacant_spaces)
            if len(vacant_spaces) != 0 :
                index = random.randrange(0, len(vacant_spaces))
                x, y = vacant_spaces[index]
            if count == 0:
                x_index = random.randrange(0, len(self.boxes))
                y_index = random.randrange(0, len(self.boxes[0]))
                x, y = x_index, y_index

            for i in range(len(self.boxes)):
                for j in range(len(self.boxes[i])):
                    if self.boxes[i][j] is not None:
                        self.boxes[i][j].update_coordinates(i, j)

            # given the index we get the left most x coordinate and the top most y coordinate
            x_l, y_t = y*self.side_length//4, x*self.side_length//4
            pygame.time.delay(1000)
            self.boxes[x][y] = Box(x_l, y_t)
            return True
        else:
            return False
def display_game_over(score):
    font = pygame.font.SysFont('Arial', 20)
    screen.blit(font.render("Game Over!!!", True, (0, 0, 0)), (100, 200))
    score_msg = "Your Score was " + str(score)
    screen.blit(font.render(score_msg, True, (0,0,0)), (100, 255))
    pygame.display.update()

def main(genomes, config):
    global gen
    gen += 1

    nets = []
    boards = []
    ge = []

    for genome_id, genome in genomes:
        genome.fitness = 0  # start with fitness level of 0
        net = neat.nn.FeedForwardNetwork.create(genome, config)
        nets.append(net)
        boards.append(Board())
        ge.append(genome)


    game_over = None
    bg = (255, 0, 255)
    blue = (0, 0, 255)
    # setting the background color
    screen.fill(bg)

    for i in range(len(boards)):
        if len(boards)>0:
            # print("gen", gen,"fitness",ge[i].fitness,"index", i)
            running = True
            b = boards[i]
            pygame.display.update()
            clock = pygame.time.Clock()
            count = 0
            prev_score = 0
            num_correct_moves = 0
        else:
            break
        start_time = time.time()

        while running and len(boards) > 0:
            # print("current_fitness", ge[i].fitness)
            input = []
            for i_ in range(len(b.boxes)):
                for j_ in range(len(b.boxes[i_])):
                    if b.boxes[i_][j_] is None:
                        input.append(0)
                    else:
                        input.append(b.boxes[i_][j_].get_val())

            prev_score = b.score

            output = nets[i].activate(tuple(input))

            max_val = None
            max_ind = None
            for ind, val in enumerate(output):
                if max_val is None:
                    max_val = val
                    max_ind = ind
                elif max_val < val:
                    max_ind = ind

            # print("output", max_ind)
            b.move(max_ind, count)
            diff = b.score - prev_score

            if diff == 0:
                ge[i].fitness -= 0.01
            elif diff > 0:
                ge[i].fitness += diff
                num_correct_moves+= 1

            if time.time() - start_time > 120*gen or ge[i].fitness < -50:
                ge[i].fitness = b.score + num_correct_moves
                ge.pop(i)
                boards.pop(i)
                nets.pop(i)
                # print("Popped an item")
                # print("Length", len(boards), len(ge), len(nets))
                if len(boards) == 0:
                    break

            for event in pygame.event.get():
                # modify this according to the output of the neat prgram
                if event.type == pygame.KEYDOWN:
                    if event.key == pygame.K_q:
                        running = False
                game_over = b.game_over()
                if game_over[0]:
                    screen.fill(bg)
                    display_game_over(game_over[1])
                    ge[i].fitness -= 1
                    ge.pop(i)
                    boards.pop(i)
                    nets.pop(i)
                    # print("Popped an item")
                    # running = False

                # draw the text to the screen
                if event.type == pygame.QUIT:
                    pygame.quit()

                clock.tick(30)
                if game_over[0]:
                    display_game_over(game_over[1])
                else:
                    count += 1
                    b.draw_board()
                    b.draw_boxes()
                pygame.display.update()
                pygame.display.flip()
                screen.fill(bg)

def run_neat(config_path):
   # Configures the neat Algorithm to match the contents of the file in the given config_path
   config = neat.config.Config(neat.DefaultGenome, neat.DefaultReproduction,
                               neat.DefaultSpeciesSet, neat.DefaultStagnation, config_path)
   # Population
   pop = neat.Population(config)

   # Statistics about each generation
   pop.add_reporter(neat.StdOutReporter(True))
   stats = neat.StatisticsReporter()
   pop.add_reporter(stats)
   winner = pop.run(main, 20)
   with open("AI", 'wb') as file:
       pickle.dump(winner, file)

   print(winner)

if __name__ == "__main__":
    local_dir = os.path.dirname(__file__)
    neat_config_path = os.path.join(local_dir, "neat_config_file.txt")
    run_neat(neat_config_path)

