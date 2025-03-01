#include <iostream>
#include <vector>
#include <conio.h> // For input without pressing Enter (Windows)
#include <thread>  // For sleep
#include <chrono>  // For sleep duration

using namespace std;

const int width = 20;
const int height = 20;

struct Point {
    int x, y;
};

vector<Point> snake;
Point fruit;
int score = 0;
enum Direction { STOP = 0, LEFT, RIGHT, UP, DOWN };
Direction dir;
bool gameOver = false;

void Setup() {
    gameOver = false;
    dir = STOP;
    snake.clear();
    snake.push_back({width / 2, height / 2}); // Starting position
    fruit.x = rand() % width;
    fruit.y = rand() % height;
    score = 0;
}

void Draw() {
    system("cls"); // Clear the console (Windows)
    // system("clear"); // Clear the console (Linux/macOS) - use if conio.h doesn't work

    for (int i = 0; i < width + 2; i++)
        cout << "#";
    cout << endl;

    for (int i = 0; i < height; i++) {
        for (int j = 0; j < width; j++) {
            if (j == 0)
                cout << "#";

            bool printSnake = false;
            for (int k = 0; k < snake.size(); k++) {
                if (snake[k].x == j && snake[k].y == i) {
                    cout << "O"; // Snake body
                    printSnake = true;
                    break;
                }
            }

            if (!printSnake) {
                if (i == fruit.y && j == fruit.x)
                    cout << "F"; // Fruit
                else
                    cout << " "; // Empty space
            }

            if (j == width - 1)
                cout << "#";
        }
        cout << endl;
    }

    for (int i = 0; i < width + 2; i++)
        cout << "#";
    cout << endl;
    cout << "Score: " << score << endl;
}

void Input() {
    if (_kbhit()) {
        switch (_getch()) {
        case 'a':
            dir = LEFT;
            break;
        case 'd':
            dir = RIGHT;
            break;
        case 'w':
            dir = UP;
            break;
        case 's':
            dir = DOWN;
            break;
        case 'x':
            gameOver = true;
            break;
        }
    }
}

void Logic() {
    // 1. Update snake position
    vector<Point> prevSnake = snake;
    Point head = snake[0];

    switch (dir) {
    case LEFT:
        head.x--;
        break;
    case RIGHT:
        head.x++;
        break;
    case UP:
        head.y--;
        break;
    case DOWN:
        head.y++;
        break;
    }

    snake.insert(snake.begin(), head);
    snake.pop_back();

    // 2. Check for collisions
    if (head.x >= width || head.x < 0 || head.y >= height || head.y < 0) {
        gameOver = true; // Hit the wall
    }

    for (int i = 1; i < snake.size(); i++) {
        if (snake[i].x == head.x && snake[i].y == head.y) {
            gameOver = true; // Hit itself
        }
    }

    // 3. Check if ate fruit
    if (head.x == fruit.x && head.y == fruit.y) {
        score += 10;
        fruit.x = rand() % width;
        fruit.y = rand() % height;

        // Grow the snake
        snake.insert(snake.begin(), head); // Add a new head
    }
}

int main() {
    Setup();
    while (!gameOver) {
        Draw();
        Input();
        Logic();
        this_thread::sleep_for(chrono::milliseconds(50)); // Adjust speed
    }
    cout << "Game Over! Score: " << score << endl;
    return 0;
}
