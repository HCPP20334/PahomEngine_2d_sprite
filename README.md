# PahomEngine Documentation

## Overview
PahomEngine is a lightweight game engine built for Windows, utilizing OpenGL for rendering, ImGui for GUI, and custom audio and input handling. It supports basic 2D game mechanics, including image rendering, audio playback, input management (keyboard and gamepad), and memory monitoring. The engine is designed for simple games with features like collision detection, asset management, and customizable UI elements.

## Key Components

### 1. PahomEngineStruct
The core structure that encapsulates the engine's functionality and state.

#### Key Members
- **CVsync**: Boolean to enable/disable V-Sync.
- **fCPoint**: Frame counter (uint64_t).
- **fStep**: Step size for movement (int64_t, default 13).
- **img**: Unique pointer to `CImage` for image handling.
- **ptrGamepad1**: Unique pointer to `JoyStickAPI` for gamepad input.
- **bSettings**: Boolean to toggle settings menu.
- **fillColorRGBA**: ImVec4 for UI color customization.
- **i64WindowSize**: Array storing window dimensions (default: 800x600).
- **fPahomPosX, fPahomPosY**: Player position coordinates.
- **fMaxPahomPosX, fMaxPahomPosY**: Maximum player position boundaries.
- **fBreadPosX, fBreadPosY**: Position of a game object (e.g., "bread").
- **bLoadingFrame, bLoadingFrameOK**: Booleans for loading state.
- **bControlsIsGamepad, bControlsIsKeyboard**: Input mode flags.
- **bDebug**: Debug mode toggle.
- **bFullscreen**: Fullscreen mode toggle.
- **bGameOver**: Game over state.
- **i64CPUDelay**: CPU delay for frame pacing (default: 10ms).
- **bStartGame**: Game start flag.
- **fStepMove**: Movement speed (default: 6.0f).
- **i64BreadSize, i64PahomSize**: Sizes for game objects (bread: 64x64, player: 128x128).

#### Methods
- **StyleLoad()**: Configures ImGui styles with a dark theme and custom colors.
- **RGBA(float r, float g, float b, float a)**: Converts RGBA values to ImVec4.
- **setTextCenter(const char* text)**: Centers text horizontally.
- **setItemCenterX(float x)**: Centers an item horizontally by size.
- **setItemCenter(ImVec2 Size)**: Centers an item both horizontally and vertically.
- **HwndWSizeA(HWND HwNd)**: Retrieves window size from a window handle.
- **InputI64(const char* id, int64_t* v)**: Renders a 64-bit integer input field.
- **InputFloat(const char* id, float* v)**: Renders a float input field.
- **reloadBreadPos()**: Randomizes the position of the "bread" object.
- **rand64(int64_t in_v)**: Generates a random 64-bit integer up to `in_v`.
- **randfloat(float in_v)**: Generates a random float up to `in_v`.
- **CheckColiision()**: Checks for collision between player and bread objects.
- **GetGamepadKey(int64_t iKey)**: Checks if a specific gamepad button is pressed.
- **Text(ImVec4 col, std::string text)**: Adds colored text to the event buffer.
- **Tbuffer()**: Renders the event text buffer.
- **log(std::string text)**: Logs text to the console.
- **progress_bar(float fraction)**: Displays a console-based progress bar.
- **logo()**: Prints the engine's ASCII logo to the console.

### 2. CImage
Handles image loading and rendering using OpenGL and stb_image.

#### Key Members
- **x, y**: Arrays for storing image positions.
- **fIctx, fIcty**: Current image dimensions.
- **gl_buffer**: OpenGL texture buffer.
- **CICharBuffer**: Image data buffer.

#### Methods
- **CreateImg()**: Initializes an image (not implemented in provided code).
- **LoadTextureFromFile(const char* filename, GLuint* out_texture, int* out_width, int* out_height, unsigned char* imgBuffer)**: Loads an image file into an OpenGL texture.
- **ResizeImage(uint64_t fCArrayFloat)**: Resizes the image based on array index.
- **InitCImage(std::string png_file)**: Initializes an image from a PNG file.
- **CreateImage(int64_t w, int64_t h)**: Renders an image with ImGui.
- **getAspectRatio(int x, int y)**: Returns the aspect ratio as a string (e.g., "4:3").
- **GetImageSize(int width, int height)**: Calculates image size in VRAM (width * height * 4 for RGBA).
- **GetFileSize(const std::string& filename)**: Returns the file size in bytes.

### 3. KurlikAUDIO
Manages audio playback with a custom `Audio::Sound` class.

#### Key Members
- **audiolist**: Array of audio file paths (e.g., "kurlik.wav").
- **idx**: Current audio index.
- **masterVolume**: Global volume control (default: 0.02f).
- **audioDevice, audioDevice2**: Audio playback devices.

#### Methods
- **play(int64_t i)**: Plays audio from `audiolist[i]` on `audioDevice`.
- **play2(int64_t i)**: Plays audio from `audiolist[i]` on `audioDevice2`.
- **pause()**: Pauses `audioDevice`.

### 4. STRINGSDATA
Handles logging and engine branding.

#### Key Members
- **PAHOM_ENGINE**: ASCII art for the engine logo.

#### Methods
- **log(std::string text, std::string moduleName)**: Logs text with a module prefix.

### 5. ASSETSDATA
Manages game assets (images).

#### Key Members
- **asset**: Array of asset file paths (e.g., "logo.png", "back.jpg").

#### Methods
- **validFiles(std::string file)**: Checks if a file exists.

### 6. IMAGEDATA
Stores image-related data for rendering.

#### Key Members
- **TextureArray**: Array of OpenGL texture IDs.
- **TextureBufferArray**: Array of texture buffers.
- **TextureX, TextureY**: Arrays for texture dimensions.

### 7. KEYMAPDATA
Manages input bindings for keyboard and gamepad.

#### Key Members
- **u8FORWARD, u8BACK, u8RESET, u8SPACE, u8ButtonLeft, u8ButtonRight**: Keyboard key bindings.
- **i64BACKGamepad, i64FORWARDGamepad, i64UPGamepad, i64AGamepad, i64START**: Gamepad button mappings.
- **kbDelay, vMaxDelay**: Keyboard input delay settings.

#### Methods
- **reMap(uint8_t key, uint8_t newBind)**: Reassigns a key binding.

### 8. MEMORYDATA
Monitors system memory usage.

#### Key Members
- **i64MemoryTotal, i64MemoryFree, i64MemoryUsed**: Memory metrics in GB.
- **MemoryPtr**: Windows `MEMORYSTATUS` structure.

#### Methods
- **getData(int64_t* mem, int64_t idx_data)**: Retrieves memory data (total, used, free).
- **MemoryInfo()**: Returns a formatted string with memory information.

### 9. GameEvent
Manages in-game text events.

#### Key Members
- **TextBufferStr**: Stores event text.
- **col**: Text color (ImVec4).
- **isTextHidden**: Toggles text visibility.
- **i64WindowSize**: Window dimensions for text positioning.

#### Methods
- **TextBuffer()**: Renders centered text.
- **Text(ImVec4 col, std::string text)**: Adds colored text to the buffer.
- **setColorText(ImVec4 col)**: Sets the text color.
- **clearEvent()**: Clears the text buffer.

### 10. EXCEPTIONS
Handles error logging.

#### Key Members
- **ErrorTextures**: Error flag for texture issues.
- **sLastError**: Last error message.
- **pLastStack**: Pointer to the error stack.

#### Methods
- **Write(std::string t, void* pErrorSegment)**: Logs an error with stack information.

### 11. ImGui Extensions
Custom ImGui widgets for enhanced UI.

#### Methods
- **CustomToggle(const char* label, bool* v)**: Renders a toggle switch with a glow effect when active.
- **Spinner(const char* label, float radius, int thickness, const ImU32& color)**: Displays a spinning loader animation.
- **InputInt64(const char* label, int64_t* v, int64_t step, int64_t step_fast, ImGuiInputTextFlags flags)**: Input field for 64-bit integers.
- **SliderInt64(const char* label, int64_t* v, int64_t v_min, int64_t v_max, const char* format, ImGuiSliderFlags flags)**: Slider for 64-bit integers.

### 12. Utility Functions
- **fstack(void* reg, std::string param_name)**: Logs a pointer and returns its value as int64_t.
- **str_stack(void* reg, const std::string& param_name)**: Logs a string pointer and returns its content.
- **RGBAtoIV4(int r, int g, int b, int a)**: Converts RGBA values to ImVec4.

## Dependencies
- **Windows API**: For window management and memory queries.
- **OpenGL**: For rendering textures.
- **ImGui**: For GUI rendering (includes imgui_impl_opengl3 and imgui_impl_win32).
- **stb_image**: For image loading.
- **Audio::Sound**: Custom audio library (requires Audio.lib).
- **JoyStickAPI**: Custom gamepad input handling.

## Defines
- **PAHOM_ENGINE_ID**: Engine identifier (0x17).
- **LOGO_IMAGE, BACK_IMAGE, BREAD_IMAGE, PAHOM_IMAGE, PAHOM2_IMAGE, PANEl_IMAGE**: Asset indices for image management.

## Usage
1. **Initialization**:
   - Create a `PahomEngineStruct` instance using `std::make_unique<PahomEngineStruct>()`.
   - Load assets using `CImage::InitCImage()` and validate with `ASSETSDATA::validFiles()`.
   - Configure ImGui styles with `StyleLoad()`.

2. **Game Loop**:
   - Handle input using `KEYMAPDATA` and `GetGamepadKey()`.
   - Update game state (e.g., `fPahomPosX`, `fBreadPosX`) with `CheckColiision()` for gameplay logic.
   - Render images with `CImage::CreateImage()` and UI with ImGui extensions.
   - Play audio using `KurlikAUDIO::play()` or `play2()`.

3. **Debugging**:
   - Use `log()` for console output.
   - Monitor memory with `MEMORYDATA::MemoryInfo()`.
   - Handle errors with `EXCEPTIONS::Write()`.

## Example
```cpp
//
 init ImGui OpenGL 
//
PahomEngine->StyleLoad(); // Apply ImGui styles
PahomEngine->img->LoadTextureFromFile(reinterpret_cast<const char*>(PahomEngine->assets.asset[tid].c_str()),
                    &PahomEngine->ImageData.TextureArray[tid],
                    &PahomEngine->ImageData.TextureX[tid],
                    &PahomEngine->ImageData.TextureY[tid],
                    &PahomEngine->ImageData.TextureBufferArray[tid]));
PahomEngine->audio.play(0); // Play kurlik.wav
PahomEngine->Text(engine->RGBA(255, 255, 255, 255), "Game Started!");
PahomEngine->Tbuffer(); // Render text
//
 Load Texture to Videomemory
//
PahomEngine->img->LoadTextureFromFile(reinterpret_cast<const char*>(PahomEngine->assets.asset[0].c_str()),
                    &PahomEngine->ImageData.TextureArray[0],
                    &PahomEngine->ImageData.TextureX[0],
                    &PahomEngine->ImageData.TextureY[0],
                    &PahomEngine->ImageData.TextureBufferArray[0]));
// used
ImGui::Image((int64_t)(void*)PahomEngine->ImageData.TextureArray[2], ImVec2(64, 64));
// and
int64_t ptrint64_t(GLuint tx) {
    return (int64_t)(void*)tx;
}
 ImGui::Image(ptrint64_t(PahomEngine->ImageData.TextureArray[0]), ImVec2(imageSizeX, imageSizeY));
key event and Gamepad event

// fragment to my Game controls
 if (PahomEngine->bControlsIsGamepad) { // check flag controller
     if (PahomEngine->ptrGamepad1->IsConnected()) { // check state gamepad connection
         if (PahomEngine->GetGamepadKey(PahomEngine->keyMap.i64FORWARDGamepad)) { // check event button gam,epad
             PahomEngine->keyMap.kbDelay++;
             if (PahomEngine->keyMap.kbDelay == PahomEngine->keyMap.vMaxDelay) {
                 PahomEngine->fPahomPosX += PahomEngine->fStep;
                 PahomEngine->bIsRevesed = true;
                 if (PahomEngine->fPahomPosX >= PahomEngine->fMaxPahomPosX) {
                     PahomEngine->fPahomPosX = PahomEngine->fMaxPahomPosX;
                 }
                 PahomEngine->keyMap.kbDelay = 0;
             }
             keyPresedStr = "FORWARD" + (PahomEngine->keyMap.i64FORWARDGamepad);
         }
         if (PahomEngine->GetGamepadKey(PahomEngine->keyMap.i64BACKGamepad)) {//PahomEngine->keyMap.u8BACKGamepad
             PahomEngine->keyMap.kbDelay++;
             if (PahomEngine->keyMap.kbDelay == PahomEngine->keyMap.vMaxDelay) {
                 PahomEngine->bIsRevesed = false;
                 PahomEngine->fPahomPosX -= PahomEngine->fStep;
                 if (PahomEngine->fPahomPosX <= PahomEngine->fMinPahomPosX) {
                     PahomEngine->fPahomPosX = PahomEngine->fMinPahomPosX;
                 }
                 PahomEngine->keyMap.kbDelay = 0;
             }
             keyPresedStr = (const char*)("BACK" + PahomEngine->keyMap.u8BACK);
         }
         if (PahomEngine->GetGamepadKey(PahomEngine->keyMap.i64UPGamepad)) {
             PahomEngine->fPahomPosY = 300.0f;
         }
     }
 }
 

 if (PahomEngine->bControlsIsKeyboard) {
     if (GetAsyncKeyState(PahomEngine->keyMap.u8BACK)) {//PahomEngine->keyMap.u8FORWARD
         PahomEngine->keyMap.kbDelay++;
         if (PahomEngine->keyMap.kbDelay == PahomEngine->keyMap.vMaxDelay) {
             PahomEngine->fPahomPosX += PahomEngine->fStep;
             PahomEngine->bIsRevesed = true;
             if (PahomEngine->fPahomPosX >= PahomEngine->fMaxPahomPosX) {
                 PahomEngine->fPahomPosX = PahomEngine->fMaxPahomPosX;
             }
             PahomEngine->keyMap.kbDelay = 0;
         }
         keyPresedStr = "FORWARD" + (PahomEngine->keyMap.u8FORWARD);
     }
     if (GetAsyncKeyState(PahomEngine->keyMap.u8FORWARD)) {//PahomEngine->keyMap.u8BACK
         PahomEngine->keyMap.kbDelay++;
         if (PahomEngine->keyMap.kbDelay == PahomEngine->keyMap.vMaxDelay) {
             PahomEngine->bIsRevesed = false;
             PahomEngine->fPahomPosX -= PahomEngine->fStep;
             if (PahomEngine->fPahomPosX <= PahomEngine->fMinPahomPosX) {
                 PahomEngine->fPahomPosX = PahomEngine->fMinPahomPosX;
             }
             PahomEngine->keyMap.kbDelay = 0;
         }
         keyPresedStr = (const char*)("BACK" + PahomEngine->keyMap.u8BACK);
     }
 }

Main code

//
//

clear GL and destroy frame
//
```

## Notes
- The engine is in version b0.23, indicating an early development stage.
- The `JoyStick.h` header is commented out in some places, suggesting incomplete gamepad support.
- Ensure all asset files exist in the specified paths (`assets/`).
- The engine assumes RGBA textures for image rendering.
- Audio playback relies on an external `Audio::Sound` class, which must be properly linked.

This documentation provides a comprehensive overview of the PahomEngine's structure and functionality. For further details, refer to the source code or contact the developer.
