#include <iostream>
#include <conio.h>
#include <windows.h>
#include <vector>

using namespace std;

// Konfigurasi Layar
const int LEBAR = 40;
const int TINGGI = 20;

// Variabel Burung
int birdX = 10;
float birdY = TINGGI / 2.0f;
float velocity = 0.0f;
float gravity = 0.4f;
float jump = -2.0f;

// Variabel Pipa
int pipeX = LEBAR - 1;
int pipeGapY = 8;     // Posisi celah pipa
int gapSize = 5;      // Ukuran celah pipa

// Variabel Game
int score = 0;
bool gameOver = false;

void HideCursor() {
    HANDLE consoleHandle = GetStdHandle(STD_OUTPUT_HANDLE);
    CONSOLE_CURSOR_INFO info;
    info.dwSize = 100;
    info.bVisible = FALSE;
    SetConsoleCursorInfo(consoleHandle, &info);
}

void SetCursorPosition(int x, int y) {
    COORD coord;
    coord.X = x;
    coord.Y = y;
    SetConsoleCursorPosition(GetStdHandle(STD_OUTPUT_HANDLE), coord);
}

void Input() {
    if (_kbhit()) {
        char key = _getch();
        if (key == ' ' || key == 'w' || key == 'W') {
            velocity = jump;
        }
    }
}

void Logic() {
    // Fisika Burung
    velocity += gravity;
    birdY += velocity;

    // Pergerakan Pipa
    pipeX--;
    if (pipeX < 0) {
        pipeX = LEBAR - 1;
        pipeGapY = rand() % (TINGGI - gapSize - 4) + 2;
        score++;
    }

    int currentBirdY = (int)birdY;

    // Deteksi Tabrakan Atas/Bawah
    if (currentBirdY <= 0 || currentBirdY >= TINGGI - 1) {
        gameOver = true;
    }

    // Deteksi Tabrakan Pipa
    if (pipeX == birdX) {
        if (currentBirdY < pipeGapY || currentBirdY >= pipeGapY + gapSize) {
            gameOver = true;
        }
    }
}

void Draw() {
    SetCursorPosition(0, 0);

    for (int y = 0; y < TINGGI; y++) {
        for (int x = 0; x < LEBAR; x++) {
            // Batas Atas dan Bawah
            if (y == 0 || y == TINGGI - 1) {
                cout << "#";
            }
            // Posisi Burung
            else if (x == birdX && y == (int)birdY) {
                cout << "O";
            }
            // Posisi Pipa
            else if (x == pipeX) {
                if (y < pipeGapY || y >= pipeGapY + gapSize) {
                    cout << "|";
                } else {
                    cout << " ";
                }
            }
            // Area Kosong
            else {
                cout << " ";
            }
        }
        cout << "\n";
    }

    cout << "Score: " << score << endl;
    cout << "Tekan SPASI / W untuk melompat!" << endl;
}

int main() {
    HideCursor();

    while (!gameOver) {
        Draw();
        Input();
        Logic();
        Sleep(50); // Kecepatan frame game (~20 FPS)
    }

    // Tampilan Game Over
    SetCursorPosition(0, TINGGI + 2);
    cout << "=================================\n";
    cout << "           GAME OVER!            \n";
    cout << "       Skor Akhir Anda: " << score << "\n";
    cout << "=================================\n";

    return 0;
}
