# Документация PahomEngine
**Сборка 0.6.99a0**  
<br>
**Компилятор MSVC**  
<br>
**IDE Visual Studio 2026 insiders**  
<br>
**Стандарт C++20**

## Обзор
PahomEngine — лёгкий игровой движок для Windows. Использует OpenGL 3.3 для рендеринга, ImGui для интерфейса, miniaudio для звука и XInput для геймпада. Поддерживает 2D-механики: текстуры, 3-канальный звук, ввод (клавиатура/геймпад), коллизии, проверку ассетов, мониторинг памяти, обработку крашей и кастомные виджеты UI. Предназначен для простых аркад с рандомизацией, событиями и отладкой.

**SWEET BREADS** — аркадная демка: Пахом собирает сладкий хлеб.

---

## Основные компоненты

### 1. PahomEngineStruct
Главная структура, содержащая всю функциональность и состояние движка.

#### Ключевые поля
- **CVsync**: Флаг включения/выключения V-Sync (`bool`).
- **fCPoint**: Счётчик кадров (`uint64_t`).
- **fStep**: Шаг перемещения (`int64_t`, по умолчанию 13).
- **img**: Уникальный указатель на `CImage` — загрузка и рендер текстур.
- **ptrGamepad1**: Уникальный указатель на `JoyStickAPI` — ввод с геймпада (XInput).
- **bSettings**: Флаг открытия меню настроек (`bool`).
- **fillColorRGBA**: `ImVec4` — цвет заливки интерфейса.
- **i64WindowSize**: Массив с размерами окна (по умолчанию: 800×600) `int64_t[1]` .
- **fPahomPosX, fPahomPosY**: Координаты игрока `float`.
- **fMaxPahomPosX, fMaxPahomPosY**: Границы движения игрока.`float`
- **fBreadPosX, fBreadPosY**: Позиция игрового объекта («хлеб»).`float`
- **bLoadingFrame, bLoadingFrameOK**: Флаги состояния загрузки.(`bool`)
- **bControlsIsGamepad, bControlsIsKeyboard**: Режимы ввода.(`bool`)
- **bDebug**: Режим отладки.(`bool`)
- **bFullscreen**: Полноэкранный режим.(`bool`)
- **bGameOver**: Состояние «игра окончена».(`bool`)
- **i64CPUDelay**: Задержка CPU для контроля FPS (по умолчанию: 2 мс).`int64_t`
- **bStartGame**: Флаг запуска игры.(`bool`)
- **fStepMove**: Скорость перемещения (по умолчанию: 6.0f).`float`
- **i64BreadSize, i64PahomSize**: Размеры объектов (хлеб: 64×64, игрок: 128×128).
- **audio**: Экземпляр `KurlikAUDIO` — 3-канальное воспроизведение звука. 
- **Event**: `GameEvent` — текст, прогресс-бары, анимации.
- **Mem**: `MEMORYDATA` — мониторинг ОЗУ (всего/используется/свободно, в ГБ).
- **Exceptions**: Указатель на `EXCEPTIONS` — обработка крашей и стек-трейсы.
- **pDiff**: Указатель на `Diffinity` — настройка сложности.
- **Bench64ptr**: Указатель на `cpu_bench64` — многопоточный бенчмарк CPU.

#### Методы
- **StyleLoad()**: Настраивает тёмную тему ImGui с кастомными цветами.
- **RGBA(float r, float g, float b, float a)**: Преобразует RGBA в `ImVec4`.
- **setTextCenter(const char* text)**: Центрирует текст по горизонтали.
- **setItemCenterX(float x)**: Центрирует элемент по горизонтали.
- **setItemCenter(ImVec2 Size)**: Центрирует элемент по обеим осям.
- **setTextCenterXY(const char* text)**: Центрирует текст по X и Y.
- **HwndWSizeA(HWND HwNd)**: Получает размер окна из дескриптора.
- **InputI64(const char* id, int64_t* v)**: Поле ввода 64-битного целого.
- **InputFloat(const char* id, float* v)**: Поле ввода float.
- **reloadBreadPos()**: Рандомизирует позицию «хлеба».
- **rand64(int64_t in_v)**: Генерирует случайное 64-битное число до `in_v`.
- **randfloat(float in_v)**: Генерирует случайное float до `in_v`.
- **CheckColiision()**: Проверка AABB-коллизии между игроком и хлебом.
- **GetGamepadKey(int64_t iKey)**: Проверка нажатия кнопки геймпада.
- **progress_bar(float fraction)**: Консольный прогресс-бар.
- **logo()**: Вывод цветного ASCII-арта в консоль.
- **log(std::string text)**: Логирование с префиксом `[PahomEngine::]`.
- **Text(ImVec4 col, std::string text)**: Вывод цветного текста через `GameEvent`.

---

### 2. CImage — Работа с текстурами
Загрузка и рендер PNG/JPG через `stb_image`.

#### Ключевые поля
- **gl_buffer**: ID текстуры OpenGL.
- **fIctx, fIcty**: Кэшированные размеры текстуры.

#### Методы
- **LoadTextureFromFile(const char* filename, ...)**: Загружает изображение в OpenGL, выводит первые 16 байт в hex.
- **CreateImage(int64_t w, int64_t h)**: Рендер через `ImGui::Image()`.
- **getAspectRatio(int x, int y)**: Возвращает строку `"16:9"`, `"4:3"` и т.д.
- **ResizeImage(uint64_t fCArrayFloat)**: Применяет предустановленные размеры из массивов `x[]`, `y[]`.
- **GetImageSize(int w, int h)**: Размер в VRAM (байты, RGBA).
- **GetFileSize(const std::string& filename)**: Размер файла в байтах.

---

### 3. KurlikAUDIO — Аудиосистема
3 одновременных канала через `Audio::Sound` (miniaudio).

#### Ключевые поля
- **audioDevice, audioDevice2, audioDevice3**: Три независимых звуковых устройства.
- **masterVolume**: По умолчанию `0.02f`.
- **audiolist[11]**: Пути к `.wav` файлам в `assets/audio/` `std::string[]`.
- **audioGain**: Мин/макс усиление в дБ.
- **audioTime**: Текущее/максимальное время в секундах.

#### Методы
- **play(int64_t i)**: Воспроизведение на канале 0.
- **play2(int64_t i)**: Воспроизведение на канале 1.
- **play3(int64_t i)**: Воспроизведение на канале 2.
- **VuePlay(int64_t device, int64_t file)**: Воспроизведение выбранного файла на выбранном устройстве.
- **vue()**: ImGui-панель управления звуком (устройство, файл, громкость, гейн, таймлайн).
- **getGain(int64_t idx)**: Обновляет значения гейна.
- **getTimeline(int64_t idx)**: Обновляет текущее время.
- **pause()**: Пауза текущего воспроизведения.
- **setReplay(bool)**: Зацикливание канала 3.

---

### 4. JoyStickAPI — Геймпад
Обёртка XInput с вибрацией и кэшированием батареи.

#### Ключевые методы
- **IsConnected()**: Проверка подключения.
- **GetState()**: Текущее состояние `XINPUT_STATE`.
- **Vibrate(int64_t left, int64_t right)**: Вибрация с `clamp(0, 65535)`.
- **BatLevel()**: Кэширует заряд батареи в `xBattery` (вызов 1 раз/кадр).

---

### 5. Расширения ImGui
Кастомные виджеты в пространстве имён `ImGui`.

- **CustomToggle(const char* label, bool* v)**: Анимированный переключатель с подсветкой при включении.
- **Spinner(const char* label, ...)**: Крутящийся индикатор загрузки.
- **InputInt64()**, **SliderInt64()**: 64-битные поля ввода и слайдеры.

---

### 6. EXCEPTIONS — Обработка крашей
Использует `dbghelp.lib` для стек-трейсов.

#### Ключевые методы
- **BugReport()**: Устанавливает `SetUnhandledExceptionFilter(CrashHandler)`.
- **CrashHandler()**: Логирует исключение, модуль, функцию, файл, строку → `crash_log.txt`.
- **GetStackTrace()**: Проход по стеку через `StackWalk64`.
- **Write()**: Запись пользовательской ошибки с указателем стека.

---

### 7. MEMORYDATA — Мониторинг ОЗУ
Через `GlobalMemoryStatusEx`.

#### Ключевые методы
- **getData(int64_t* mem, int64_t idx)**: Заполняет всего/используется/свободно в ГБ.
- **MemoryInfo()**: Форматированная строка с данными ОЗУ.

---

### 8. GameEvent — Игровой UI
Текст, прогресс-бары, анимация заполнения экрана.

#### Ключевые поля
- **TextBufferStr**: Текущий текст события.
- **col**: Цвет текста.
- **progress**: Прогресс анимации.
- **linesToDraw**: Количество линий в анимации заполнения.

#### Методы
- **Text(ImVec4 col, std::string text)**: Устанавливает текст и цвет.
- **TextBuffer()**: Центрированный вывод текста.
- **mt_fill(x_max, y_max)**: Анимированное заполнение экрана линиями.
- **mt_clear()**: Сброс анимации.
- **TimerToClear()**: Автоскрытие текста через 100 кадров.

---

### 9. Diffinity — Система сложности
Настраиваемые интервалы спавна.

#### Ключевые поля
- **i64diff[3]**: Базовые значения `{16, 24, 8}`.
- **ccDiff**: `{"Легкая", "Средняя", "Поехавший", "Рандомная"}`.
- **diffArray[3]**: Диапазоны спавна `{{4,6}, {6,10}, {12,20}}`.

#### Методы
- **setDiff(int64_t idx)**: Установка активной сложности.

---

### 10. cpu_bench64 — Бенчмарк CPU
Многопоточный тест генерации хэшей.

#### Ключевые поля
- **i64MaxSize**: Всего операций (1 000 000).`int64_t`
- **i32max_thread**: `std::jthread::hardware_concurrency()`.`int32_t`

#### Методы
- **mt()**: Запуск бенчмарка по всем ядрам.
- **hashFn128(int64_t sz)**: Генерация строки и логирование размера.

---
### 11. mathValues
- minv(a,b) - аналог std::min
- maxv(a,b) - аналог std::max
- random(max_value) - Генератор рандома
```cpp
int64_t a = 150;
int64_t b = 255;
PahomEngine->math->minv<int64_t>(a,b);// - вернет int64_t a;
//
PahomEngine->math->maxv<float>(0.4f,0.05f);//  - вернет float 0.4f;
//
PahomEngine->math->random<double>(144.0f);// - вернет число от 0.0f до 100.0f;

```
## Ассеты
```cpp
#define LOGO_IMAGE 0
#define BACK_IMAGE 1
#define BREAD_IMAGE 2
// ...
std::string asset[11] = { "assets/logo.png", ... };//`std::string`
// Использование в игровом цикле
if(!PahomEngine->bStartGame)
{
static int32_t tid = 0;
if(!PahomEngine->img->LoadTextureFromFile(
                    reinterpret_cast<const char*>(PahomEngine->assets.asset[tid].c_str()),
                    &PahomEngine->ImageData.TextureArray[tid],
                    &PahomEngine->ImageData.TextureX[tid],
                    &PahomEngine->ImageData.TextureY[tid],
                    &PahomEngine->ImageData.TextureBufferArray[tid])){
                        std::cout<<" Texture Falled!!" << std::endl;
                    }
tid++;
if(tid > PahomEngine->img->i64MaxTexturesToArray){
    tid = PahomEngine->img->i64MaxTexturesToArray;
    PahomEngine->bStartGame = true;
}
}
if(PahomEngine->bStartGame){
    //main logic
}
// загрузка через for
if(!PahomEngine->bStartGame)
{
 for(int64_t tid = 0; tid < PahomEngine->img->i64MaxTexturesToArray;tid++){
    if(!PahomEngine->img->LoadTextureFromFile(
                    reinterpret_cast<const char*>(PahomEngine->assets.asset[tid].c_str()),
                    &PahomEngine->ImageData.TextureArray[tid],
                    &PahomEngine->ImageData.TextureX[tid],
                    &PahomEngine->ImageData.TextureY[tid],
                    &PahomEngine->ImageData.TextureBufferArray[tid])){
                        std::cout<<" Texture Falled!!" << std::endl;
                    }
                    if(tid > PahomEngine->img->i64MaxTexturesToArray){
                     tid = PahomEngine->img->i64MaxTexturesToArray;
                      PahomEngine->bStartGame = true;
}
 }

}
if(PahomEngine->bStartGame){
    //main logic
}
