#include <iostream>
#include <conio.h>
#include <windows.h>
#include <ctime>
#include <vector>
using namespace std;
const int N = 10;
int Ships_id = 1;
int Ships[10] = { 0 };
int Ships1[10] = { 0 };
int damagedCount[20] = { 0 };
int damagedShips[20][4][2] = { 0 };

int CPU = 0;
int memory_x = 0;
int memory_y = 0;
int memory_dir = -1;

int hitParts[4][2];     // Массив координат подбитых частей текущего корабля
int hitCount = 0;       // Количество подбитых частей
int currentDirection = -1; // Текущее направление добивания (-1 = не определено)
int searchPhase = 0;    // Фаза поиска: 0 = первые попадания, 1 = определено направление

enum direction { Up = 72, Left = 75, Right = 77, Down = 80, Enter = 13, BackSpace = 8, Q = 113, R = 114 };
HANDLE hStdOut = GetStdHandle(STD_OUTPUT_HANDLE);
void SetCursor(int x, int y) //функция для того чтобы устанавливать позицию курсора в консоли по оси Х и Y
{
    COORD myCoords = { x,y }; //инициализация координат
    SetConsoleCursorPosition(hStdOut, myCoords); //Способ перемещения курсора на нужные координаты
}
int menu(int menu_number) {
    int ch;
    int x, y;
    x = 22;
    y = 15;
    SetCursor(22, 14);
    if (menu_number == 1) {
        cout << "Выберите уровень сложности:";
        SetCursor(22, 15);
        cout << "Легкий уровень";
        SetCursor(22, 16);
        cout << "Обычный уровень";
    }
    else {
        cout << "Выберите расстановку кораблей:";
        SetCursor(22, 15);
        cout << "Автоматическая расстановка";
        SetCursor(22, 16);
        cout << "Ручная расстановка";
    }
    SetCursor(22, 15);
    while (true) {
        ch = _getch();
        switch (ch)
        {
        case Up:
            if (y > 15) {
                y--;
                SetCursor(x, y);
            }
            break;
        case Down:
            if (y < 16) {
                y++;
                SetCursor(x, y);
            }
            break;
        case Enter:
            switch (y)
            {
            case 15:
                for (size_t i = 0; i < 30; i++) {
                    cout << ' '; // Стираем по одному символу
                }
                SetCursor(x, y - 1);
                for (size_t i = 0; i < 30; i++) {
                    cout << ' '; // Стираем по одному символу
                }
                SetCursor(x, y + 1);
                for (size_t i = 0; i < 30; i++) {
                    cout << ' '; // Стираем по одному символу
                }
                return 0;
                break;
            case 16:
                for (size_t i = 0; i < 30; i++) {
                    cout << ' '; // Стираем по одному символу
                }
                SetCursor(x, y - 1);
                for (size_t i = 0; i < 30; i++) {
                    cout << ' '; // Стираем по одному символу
                }
                SetCursor(x, y - 2);
                for (size_t i = 0; i < 30; i++) {
                    cout << ' '; // Стираем по одному символу
                }
                return 1;
                break;
            default:
                break;
            }
        default:
            break;
        }
    }
}
void Replicas(int r) {
    Sleep(100);
    SetCursor(22, 15);
    for (size_t i = 0; i < 15; i++) {
        cout << ' '; // Стираем по одному символу
    }
    SetCursor(22, 15);
    switch (r)
    {
    case 5:
        cout << "Вы победили!";
        break;
    case 4:
        cout << "Вы проиграли!";
        break;
    case 1:
        cout << "Ранен!";
        break;
    case 3:
        cout << "Убит!";
        break;
    case 0:
        cout << "Промах!";
        break;
    case 2:
        cout << "Ход компьютера";
        break;
    case 10:
        cout << "Игра окончена!";
        break;
    default:
        break;
    }
}
void Ship_conclusion(int map[N][N], int size_ship, int num_ships, bool map_flag) {
    int x, y;
    int direction_ship = 0;
    int count_ship = 0;
    int count_tact = 0;
    srand(time(NULL));
    while (count_ship < num_ships) {
        count_tact++;
        if (count_tact > 1000) {
            break;
        }
        x = rand() % N;
        y = rand() % N;
        int temp_x = x;
        int temp_y = y;
        bool setting_is_possible = true;
        direction_ship = rand() % 4;

        for (int i = 0; i < size_ship;i++) {
            if (x < 1 || x >= N + 1 || y < 1 || y >= N + 1) {
                setting_is_possible = false;
                break;
            }
            if (map[x - 1][y - 1] >= 1 ||
                map[x][y - 1] >= 1 ||
                map[x][y] >= 1 ||
                map[x - 1][y] >= 1 ||
                map[x - 2][y] >= 1 ||
                map[x - 2][y - 1] >= 1 ||
                map[x - 2][y - 2] >= 1 ||
                map[x - 1][y - 2] >= 1 ||
                map[x][y - 2] >= 1) {
                setting_is_possible = false;
                break;
            }
            switch (direction_ship)
            {
            case 0:
                x++;
                break;
            case 1:
                y++;
                break;
            case 2:
                x--;
                break;
            case 3:
                y--;
                break;
            default:
                break;
            }
        }

        if (setting_is_possible == true) {
            x = temp_x;
            y = temp_y;
            for (int i = 0;i < size_ship;i++) {
                map[x - 1][y - 1] = Ships_id;
                switch (direction_ship)
                {
                case 0:
                    x++;
                    break;
                case 1:
                    y++;
                    break;
                case 2:
                    x--;
                    break;
                case 3:
                    y--;
                    break;
                default:
                    break;
                }
            }
            if (map_flag) Ships1[Ships_id] = { size_ship };
            else Ships[Ships_id] = { size_ship };
            Ships_id++;
            count_ship++;
        }
    }
}
void map_show(int map[N][N], int mask[N][N], bool map_flag) {
    if (map_flag) SetCursor(2 * N, 0);
    else SetCursor(0, 0);
    char mas[10] = { 'А','Б','В','Г','Д','Е','Ж','З','И','К' };
    cout << ' ';
    for (int i = 0; i < N;i++) {
        cout << mas[i];
    }
    cout << endl;
    for (int i = 0; i < N;i++) {
        if (map_flag) SetCursor(2 * N, i + 1);
        else SetCursor(0, i + 1);
        cout << i;
    }
    for (int i = 0; i < N;i++) {
        if (map_flag) SetCursor(1 + 2 * N, i + 1);
        else SetCursor(1, i + 1);
        for (int j = 0; j < N;j++) {
            if (map_flag) {
                if (mask[j][i] >= 1) {
                    cout << map[j][i];
                }
                else {
                    cout << ' ';
                }
            }
            else {
                if (map[j][i] == 0) {
                    cout << '-';
                }
                else cout << map[j][i];
            }
            /*if (map[j][i] == 0) {
                cout << '-';
            }
            else cout << map[j][i];*/
        }
        cout << endl;
    }
}
bool set_ship(int map[N][N], int mask[N][N], int x, int y, int direction_ship, int size_ship) {
    bool setting_is_possible = true;
    int temp_x = x;
    int temp_y = y;
    for (int i = 0;i < size_ship;i++) {
        if (map[x - 1][y - 1] >= 1) {
            setting_is_possible = false;
            break;
        }
        if (y < N) {
            if (map[x - 1][y] >= 1) {
                setting_is_possible = false;
                break;
            }
        }
        if (y > 1) {
            if (map[x - 1][y - 2] >= 1) {
                setting_is_possible = false;
                break;
            }
        }
        if (x < N) {
            if (map[x][y - 1] >= 1) {
                setting_is_possible = false;
                break;
            }
        }
        if (x > 1) {
            if (map[x - 2][y - 1] >= 1) {
                setting_is_possible = false;
                break;
            }
        }
        if (x < N && y < N) {
            if (map[x][y] >= 1) {
                setting_is_possible = false;
                break;
            }
        }
        if (x < N && y > 1) {
            if (map[x][y - 2] >= 1) {
                setting_is_possible = false;
                break;
            }
        }
        if (y < N && x > 1) {
            if (map[x - 2][y] >= 1) {
                setting_is_possible = false;
                break;
            }
        }
        if (x > 1 && y > 1) {
            if (map[x - 2][y - 2] >= 1) {
                setting_is_possible = false;
                break;
            }
        }
        switch (direction_ship)
        {
        case 0:
            x++;
            break;
        case 1:
            y++;
            break;
        case 2:
            x--;
            break;
        case 3:
            y--;
            break;
        default:
            break;
        }
    }
    if (setting_is_possible == true) {
        x = temp_x;
        y = temp_y;
        for (int i = 0;i < size_ship;i++) {
            map[x - 1][y - 1] = Ships_id;
            mask[x - 1][y - 1] = Ships_id;
            switch (direction_ship)
            {
            case 0:
                x++;
                break;
            case 1:
                y++;
                break;
            case 2:
                x--;
                break;
            case 3:
                y--;
                break;
            default:
                break;
            }
        }
        Ships[Ships_id] = { size_ship };
        Ships_id++;
    }
    return setting_is_possible;
}
void ship_show(int x, int y, int dir, int size_ship, bool map_flag) {
    for (int i = 0;i < size_ship;i++) {
        SetCursor(x, y);
        cout << '#';
        switch (dir)
        {
        case 0:
            x++;
            break;
        case 1:
            y++;
            break;
        case 2:
            x--;
            break;
        case 3:
            y--;
            break;
        default:
            break;
        }
    }
}
void ClearScreen() {
    // Очистка только игрового поля (пример для N=10)
    for (int y = 0; y < N + 1; y++) {
        SetCursor(0, y);
        for (int x = 0; x < N + 1; x++) {
            cout << ' ';
        }
    }
}
void auto_rasstanovka(int map[N][N], int mask[N][N], bool map_flag) {
    Ship_conclusion(map, 4, 1, map_flag);
    Ship_conclusion(map, 3, 2, map_flag);
    Ship_conclusion(map, 2, 3, map_flag);
    Ship_conclusion(map, 1, 3, map_flag);
    map_show(map, mask, map_flag);
}
void hand_rasstanovka(int map[N][N], int mask[N][N], bool map_flag, int x, int y, int dir, int size_ship) {
    int ch;
    int count_ship = 1;
    int koordinat = 0;
    if (map_flag) koordinat = 2 * N;
    while (count_ship < 10) {
        map_show(map, mask, map_flag);
        ship_show(x, y, dir, size_ship, map_flag);
        SetCursor(x, y);
        ch = _getch();
        switch (ch)
        {
        case Up:
            if (y > 1) {
                y--;
            }
            break;
        case Down:
            if (dir) {
                if (y < N - size_ship + 1) {
                    y++;
                }
            }
            else {
                if (y < N) {
                    y++;
                }
            }
            break;
        case Left:
            if (x > 1 + koordinat) {
                x--;
            }
            break;
        case Right:
            if (!dir) {
                if (x < N + koordinat - size_ship + 1) x++;
            }
            else {
                if (x < N + koordinat) x++;
            }
            break;
        case R:
            if (dir) {
                if (x < N + koordinat - size_ship + 2) {
                    dir = !dir;
                }
            }
            else {
                if (y < N - size_ship + 2) {
                    dir = !dir;
                }
            }
            break;
        case Enter:
            switch (count_ship)
            {
            case 1:
                if (set_ship(map, mask, x, y, dir, size_ship)) {
                    count_ship++;
                    size_ship = 3;
                }
                break;
            case 2:
                if (set_ship(map, mask, x, y, dir, size_ship)) {
                    count_ship++;
                }
                break;
            case 3:
                if (set_ship(map, mask, x, y, dir, size_ship)) {
                    count_ship++;
                    size_ship = 2;
                }
                break;
            case 4:
                if (set_ship(map, mask, x, y, dir, size_ship)) {
                    count_ship++;
                }
                break;
            case 5:
                if (set_ship(map, mask, x, y, dir, size_ship)) {
                    count_ship++;
                }
                break;
            case 6:
                if (set_ship(map, mask, x, y, dir, size_ship)) {
                    count_ship++;
                    size_ship = 1;
                }
                break;
            default:
                if (set_ship(map, mask, x, y, dir, size_ship)) {
                    count_ship++;
                }
                break;
            }
            break;
        default:
            break;
        }
        ClearScreen();
    }
    //SetCursor(0, 0);
    map_show(map, mask, map_flag);
}
void kill(int map[N][N], int mask[N][N], bool map_flag, int x, int y) {
    int koordinat = 0;
    if (map_flag) koordinat = 2 * N;
    // Проверяем все 8 соседних клеток
    for (int kill_x = -1; kill_x <= 1; kill_x++) {
        for (int kill_y = -1; kill_y <= 1; kill_y++) {
            int nx = x + kill_x;
            int ny = y + kill_y;

            // Проверяем, что координаты в пределах поля
            if (nx >= 0 && nx < N && ny >= 0 && ny < N) {
                // Если клетка не принадлежит кораблю и не была подбита ранее
                if (map[nx][ny] == 0 && mask[nx][ny] != -1) {
                    mask[nx][ny] = -1;  // Помечаем как "промах"
                    SetCursor(nx + 1 + koordinat, ny + 1);  // Обновляем отображение (для карты игрока)
                    cout << '.';  // Выводим "промах"
                }
            }
        }
    }
}
// Проверка возможности выстрела
bool canShoot(int mask[N][N], int x, int y) {
    if (x < 0 || x >= N || y < 0 || y >= N) return false;
    return mask[x][y] != -1;
}

// Добавить подбитую часть корабля
void addHitPart(int x, int y) {
    if (hitCount < 4) {
        hitParts[hitCount][0] = x;
        hitParts[hitCount][1] = y;
        hitCount++;
    }
}

// Очистить память о подбитом корабле
void clearHitParts() {
    hitCount = 0;
    currentDirection = -1;
    searchPhase = 0;
}

// Найти минимальные и максимальные координаты подбитых частей
void findMinMaxCoords(int& min_x, int& max_x, int& min_y, int& max_y) {
    min_x = max_x = hitParts[0][0];
    min_y = max_y = hitParts[0][1];

    for (int i = 1; i < hitCount; i++) {
        if (hitParts[i][0] < min_x) min_x = hitParts[i][0];
        if (hitParts[i][0] > max_x) max_x = hitParts[i][0];
        if (hitParts[i][1] < min_y) min_y = hitParts[i][1];
        if (hitParts[i][1] > max_y) max_y = hitParts[i][1];
    }
}

// ГЛАВНАЯ функция выбора цели
void findNextTarget(int mask[N][N], int& target_x, int& target_y) {
    // Направления: 0=вверх, 1=вправо, 2=вниз, 3=влево
    int dx[] = { 0, 1, 0, -1 };
    int dy[] = { -1, 0, 1, 0 };

    if (hitCount == 0) {
        // Ошибка - не должно такого быть
        CPU = 0;
        return;
    }

    if (hitCount == 1) {
        // ФАЗА 1: Одно попадание - ищем вторую часть корабля
        // Порядок поиска: вправо -> влево -> вниз -> вверх (более логичный)
        int searchOrder[] = { 1, 3, 2, 0 }; // право, лево, вниз, вверх

        int start_x = hitParts[0][0];
        int start_y = hitParts[0][1];

        for (int i = 0; i < 4; i++) {
            int dir = searchOrder[i];
            int nx = start_x + dx[dir];
            int ny = start_y + dy[dir];

            if (canShoot(mask, nx, ny)) {
                target_x = nx;
                target_y = ny;
                currentDirection = dir; // Запоминаем направление попытки
                return;
            }
        }
    }
    else {
        // ФАЗА 2: Несколько попаданий - определяем направление корабля
        int min_x, max_x, min_y, max_y;
        findMinMaxCoords(min_x, max_x, min_y, max_y);

        // Определяем ориентацию корабля
        bool isHorizontal = (min_y == max_y); // все Y одинаковые = горизонтальный
        bool isVertical = (min_x == max_x);   // все X одинаковые = вертикальный

        if (isHorizontal) {
            // ГОРИЗОНТАЛЬНЫЙ КОРАБЛЬ - стреляем только влево и вправо
            // Сначала пробуем вправо от самой правой части
            if (canShoot(mask, max_x + 1, min_y)) {
                target_x = max_x + 1;
                target_y = min_y;
                currentDirection = 1; // вправо
                searchPhase = 1;
                return;
            }
            // Потом влево от самой левой части
            if (canShoot(mask, min_x - 1, min_y)) {
                target_x = min_x - 1;
                target_y = min_y;
                currentDirection = 3; // влево
                searchPhase = 1;
                return;
            }
        }
        else if (isVertical) {
            // ВЕРТИКАЛЬНЫЙ КОРАБЛЬ - стреляем только вверх и вниз
            // Сначала пробуем вниз от самой нижней части
            if (canShoot(mask, min_x, max_y + 1)) {
                target_x = min_x;
                target_y = max_y + 1;
                currentDirection = 2; // вниз
                searchPhase = 1;
                return;
            }
            // Потом вверх от самой верхней части
            if (canShoot(mask, min_x, min_y - 1)) {
                target_x = min_x;
                target_y = min_y - 1;
                currentDirection = 0; // вверх
                searchPhase = 1;
                return;
            }
        }
        else {
            // Корабль имеет странную форму (этого не должно быть в морском бое)
            // Пробуем стрелять от последней подбитой части
            int last_x = hitParts[hitCount - 1][0];
            int last_y = hitParts[hitCount - 1][1];

            for (int i = 0; i < 4; i++) {
                int nx = last_x + dx[i];
                int ny = last_y + dy[i];
                if (canShoot(mask, nx, ny)) {
                    target_x = nx;
                    target_y = ny;
                    currentDirection = i;
                    return;
                }
            }
        }
    }

    // Если ничего не нашли - что-то пошло не так
    CPU = 0;
    clearHitParts();
}

// Основная функция хода компьютера
void computerMove(int map[N][N], int mask[N][N], int choice_play_level, int& comp_x, int& comp_y) {
    if (choice_play_level == 0) {
        // Легкий уровень - всегда случайная стрельба
        do {
            comp_x = rand() % N + 1;
            comp_y = rand() % N + 1;
        } while (mask[comp_x - 1][comp_y - 1] == -1);
        return;
    }

    // Сложный уровень
    if (CPU == 0) {
        // Случайная стрельба
        do {
            comp_x = rand() % N + 1;
            comp_y = rand() % N + 1;
        } while (mask[comp_x - 1][comp_y - 1] == -1);
    }
    else if (CPU == 1) {
        // Режим добивания корабля
        int target_x, target_y;
        findNextTarget(mask, target_x, target_y);

        if (CPU == 0) {
            // Если функция сбросила режим добивания, стреляем случайно
            do {
                comp_x = rand() % N + 1;
                comp_y = rand() % N + 1;
            } while (mask[comp_x - 1][comp_y - 1] == -1);
        }
        else {
            comp_x = target_x + 1;
            comp_y = target_y + 1;
        }
    }
}

int shot(int map[N][N], int mask[N][N], int x, int y, bool map_flag) {
    int koordinat = 0;
    if (map_flag) koordinat = 2 * N;
    if (mask[x - 1][y - 1] == -1) return 1;
    if (map[x - 1][y - 1] >= 1) {
        int ship_index = map[x - 1][y - 1];
        if (!map_flag) ship_index += 10;
        damagedShips[ship_index][damagedCount[ship_index]][0] = x - 1;
        damagedShips[ship_index][damagedCount[ship_index]][1] = y - 1;
        damagedCount[ship_index]++;
        if (map_flag) {
            Ships1[map[x - 1][y - 1]]--;
            if (Ships1[map[x - 1][y - 1]] <= 0) {
                Replicas(3);
                int index = 0;
                while (damagedCount[ship_index] > 0) {
                    kill(map, mask, map_flag, damagedShips[ship_index][index][0], damagedShips[ship_index][index][1]);
                    index++;
                    damagedCount[ship_index]--;
                }
            }
            else Replicas(1);
        }
        else {
            Ships[map[x - 1][y - 1]]--;
            if (Ships[map[x - 1][y - 1]] <= 0) {
                Replicas(3);
                int index = 0;
                CPU = 0;
                clearHitParts();
                memory_dir = -1;
                while (damagedCount[ship_index] > 0) {
                    kill(map, mask, map_flag, damagedShips[ship_index][index][0], damagedShips[ship_index][index][1]);
                    index++;
                    damagedCount[ship_index]--;
                }
            }
            else {
                CPU = 1;
                memory_x = x - 1;
                memory_y = y - 1;
                addHitPart(x - 1, y - 1);
                Replicas(1);
            }
        }
        SetCursor(x + koordinat, y);
        mask[x - 1][y - 1] = -1;
        cout << 'X';
        map[x - 1][y - 1] = 0;
        return 1;
    }
    else {
        if (mask[x - 1][y - 1] == -1) Replicas(0);
        else {
            mask[x - 1][y - 1] = -1;
            SetCursor(x + koordinat, y);
            cout << '.';
            Replicas(0);
        }
        return 0;
    }
}
int ships_detect(int map[N][N], int mask[N][N]) {
    for (int i = 0; i < N;i++) {
        for (int j = 0; j < N;j++) {
            if (map[i][j] >= 1) {
                return true;
            }
        }
    }
    return false;
}


int main()
{
    setlocale(LC_ALL, "RU");
    srand(time(NULL));
    int map[N][N] = { 0 };
    int map2[N][N] = { 0 };
    int mask[N][N] = { 0 };
    int mask2[N][N] = { 0 };
    int x = 1, y = 1;
    int dir = 1;
    int size_ship = 4;

    //SetCursor(0, 0);
    map_show(map, mask, 0);
    //SetCursor(N+10, 0);
    map_show(map2, mask2, 1);
    auto_rasstanovka(map2, mask2, 1);
    Ships_id = 1;
    int choice_play_level = menu(1);
    int rasstanovka;
    switch (choice_play_level)
    {
    case 0:
        rasstanovka = menu(2);
        switch (rasstanovka)
        {
        case 0:
            auto_rasstanovka(map, mask, 0);
            break;
        case 1:
            hand_rasstanovka(map, mask, 0, x, y, dir, size_ship);
            break;
        default:
            break;
        }
        break;
    case 1:
        rasstanovka = menu(2);
        switch (rasstanovka)
        {
        case 0:
            auto_rasstanovka(map, mask, 0);
            break;
        case 1:
            hand_rasstanovka(map, mask, 0, x, y, dir, size_ship);
            break;
        default:
            break;
        }
        break;
    default:
        break;
    }
    int ch = 0;
    x = 1 + 2 * N;
    y = 1;
    bool turn = 1;
    bool repeat;

    SetCursor(x, y);
    while (ch != 27) {
        if (turn) {
            if (ships_detect(map2, mask2)) {
                ch = _getch();
                switch (ch)
                {
                case Up:
                    if (y > 1) {
                        y--;
                        SetCursor(x, y);
                    }
                    break;
                case Down:
                    if (y < N) {
                        y++;
                        SetCursor(x, y);
                    }
                    break;
                case Left:
                    if (x > 1 + 2 * N) {
                        x--;
                        SetCursor(x, y);
                    }
                    break;
                case Right:
                    if (x < 3 * N) {
                        x++;
                        SetCursor(x, y);
                    }
                    break;
                case Enter: {
                    int a = x - 2 * N;
                    repeat = shot(map2, mask2, a, y, 1);
                    SetCursor(x, y);
                    if (!repeat) turn = !turn;
                    break;
                }
                default:
                    break;
                }
            }
            else {
                Replicas(5);
                Sleep(5000);
                break;
            }
        }
        else {
            if (ships_detect(map, mask)) {
                Replicas(2);
                Sleep(1000);
                int comp_x, comp_y;
                computerMove(map, mask, choice_play_level, comp_x, comp_y);
                //Sleep(1000);
                repeat = shot(map, mask, comp_x, comp_y, 0);
                SetCursor(x, y);
                if (!repeat) turn = !turn;
            }
            else {
                Replicas(4);
                Sleep(5000);
                break;
            }
        }
    }
    Replicas(10);
    return 0;
}

