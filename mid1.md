# 1번 답안
## 무지개 블록깨기 게임을 C언어로 작성하시오. (그래픽 라이브러리 활용)
## 나의 프롬프트: C언어로 Visual Studio에서 raylib를 활용하여, 무지개 블록 깨기 게임을 만들어줘, raylib는 최신 버전으로 nuget으로 설치를 하고 실행 되도록 확인해서 코드 만들어줘

```
#include "raylib.h"

#define PLAYER_MAX_LIFE 3
#define ROWS 6
#define COLS 10

// 블록 구조체 정의
typedef struct Brick {
    Rectangle rect;
    Color color;
    bool active;
} Brick;

int main(void) {
    // 1. 화면 초기화 (800x600 해상도)
    const int screenWidth = 800;
    const int screenHeight = 600;
    InitWindow(screenWidth, screenHeight, "Rainbow Brick Breaker - Raylib");

    // 2. 패들(플레이어 바) 설정
    Rectangle paddle = { screenWidth / 2.0f - 50, screenHeight - 40, 100, 15 };
    float paddleSpeed = 7.0f;

    // 3. 공 설정
    Vector2 ballPos = { screenWidth / 2.0f, screenHeight / 2.0f };
    Vector2 ballSpeed = { 5.0f, -5.0f };
    float ballRadius = 8.0f;

    // 4. 무지개 블록 배치 설정 (6행 10열)
    Brick bricks[ROWS][COLS];
    Color rainbow[ROWS] = { RED, ORANGE, YELLOW, GREEN, BLUE, PURPLE };

    float brickWidth = 70.0f;
    float brickHeight = 20.0f;
    float padding = 8.0f;
    float offsetX = 35.0f;
    float offsetY = 50.0f;

    for (int i = 0; i < ROWS; i++) {
        for (int j = 0; j < COLS; j++) {
            bricks[i][j].rect = (Rectangle){
                offsetX + j * (brickWidth + padding),
                offsetY + i * (brickHeight + padding),
                brickWidth,
                brickHeight
            };
            bricks[i][j].color = rainbow[i];
            bricks[i][j].active = true;
        }
    }

    int score = 0;
    int lives = PLAYER_MAX_LIFE;
    bool gameOver = false;
    bool victory = false;

    SetTargetFPS(60); // 초당 60프레임 고정

    // 게임 루프
    while (!WindowShouldClose()) {
        // --- [업데이트 로직] ---
        if (!gameOver && !victory) {
            // 패들 이동 (좌우 방향키 또는 A/D 키)
            if (IsKeyDown(KEY_LEFT) || IsKeyDown(KEY_A)) paddle.x -= paddleSpeed;
            if (IsKeyDown(KEY_RIGHT) || IsKeyDown(KEY_D)) paddle.x += paddleSpeed;

            // 화면 벽을 넘어가지 않도록 제한
            if (paddle.x < 0) paddle.x = 0;
            if (paddle.x + paddle.width > screenWidth) paddle.x = screenWidth - paddle.width;

            // 공 이동
            ballPos.x += ballSpeed.x;
            ballPos.y += ballSpeed.y;

            // 벽 충돌 체크 (좌, 우, 위)
            if (ballPos.x - ballRadius < 0) { ballPos.x = ballRadius; ballSpeed.x *= -1; }
            if (ballPos.x + ballRadius > screenWidth) { ballPos.x = screenWidth - ballRadius; ballSpeed.x *= -1; }
            if (ballPos.y - ballRadius < 0) { ballPos.y = ballRadius; ballSpeed.y *= -1; }

            // 바닥에 떨어졌을 때 (목숨 감소)
            if (ballPos.y + ballRadius > screenHeight) {
                lives--;
                if (lives <= 0) {
                    gameOver = true;
                } else {
                    // 공 위치 및 속도 리셋
                    ballPos = (Vector2){ screenWidth / 2.0f, screenHeight / 2.0f };
                    ballSpeed = (Vector2){ 5.0f, -5.0f };
                }
            }

            // 패들과 공 충돌 체크
            if (CheckCollisionCircleRec(ballPos, ballRadius, paddle)) {
                ballSpeed.y *= -1;
                // 패들의 맞은 위치에 따라 X축 반사각 다르게 조절
                float hitFactor = (ballPos.x - (paddle.x + paddle.width / 2.0f)) / (paddle.width / 2.0f);
                ballSpeed.x = hitFactor * 6.0f;
            }

            // 블록과 공 충돌 체크
            bool allCleared = true;
            for (int i = 0; i < ROWS; i++) {
                for (int j = 0; j < COLS; j++) {
                    if (bricks[i][j].active) {
                        allCleared = false;
                        if (CheckCollisionCircleRec(ballPos, ballRadius, bricks[i][j].rect)) {
                            bricks[i][j].active = false;
                            ballSpeed.y *= -1;
                            score += 10;
                        }
                    }
                }
            }

            // 모든 블록을 깼을 경우 승리
            if (allCleared) {
                victory = true;
            }
        } 
        else {
            // 게임 오버 또는 승리 시 엔터키를 누르면 재시작
            if (IsKeyPressed(KEY_ENTER)) {
                score = 0;
                lives = PLAYER_MAX_LIFE;
                gameOver = false;
                victory = false;
                ballPos = (Vector2){ screenWidth / 2.0f, screenHeight / 2.0f };
                ballSpeed = (Vector2){ 5.0f, -5.0f };
                for (int i = 0; i < ROWS; i++) {
                    for (int j = 0; j < COLS; j++) {
                        bricks[i][j].active = true;
                    }
                }
            }
        }

        // --- [렌더링(그리기) 로직] ---
        BeginDrawing();
        ClearBackground(RAYWHITE);

        if (!gameOver && !victory) {
            // 패들 그리기
            DrawRectangleRec(paddle, DARKGRAY);

            // 공 그리기
            DrawCircleV(ballPos, ballRadius, MAROON);

            // 무지개 블록 그리기
            for (int i = 0; i < ROWS; i++) {
                for (int j = 0; j < COLS; j++) {
                    if (bricks[i][j].active) {
                        DrawRectangleRec(bricks[i][j].rect, bricks[i][j].color);
                        DrawRectangleLinesEx(bricks[i][j].rect, 1, Fade(BLACK, 0.3f));
                    }
                }
            }

            // 점수 및 목숨 표시
            DrawText(TextFormat("Score: %i", score), 20, 15, 20, DARKGRAY);
            DrawText(TextFormat("Lives: %i", lives), screenWidth - 110, 15, 20, DARKGRAY);
        } 
        else if (gameOver) {
            DrawText("GAME OVER", screenWidth / 2 - MeasureText("GAME OVER", 40) / 2, screenHeight / 2 - 40, 40, RED);
            DrawText("Press [ENTER] to Restart", screenWidth / 2 - MeasureText("Press [ENTER] to Restart", 20) / 2, screenHeight / 2 + 10, 20, DARKGRAY);
        } 
        else if (victory) {
            DrawText("VICTORY!", screenWidth / 2 - MeasureText("VICTORY!", 40) / 2, screenHeight / 2 - 40, 40, GREEN);
            DrawText("Press [ENTER] to Restart", screenWidth / 2 - MeasureText("Press [ENTER] to Restart", 20) / 2, screenHeight / 2 + 10, 20, DARKGRAY);
        }

        EndDrawing();
    }

    // 5. 종료
    CloseWindow();
    return 0;
}
```


<img width="799" height="625" alt="image" src="https://github.com/user-attachments/assets/cb33ba24-e949-4cdd-a5da-aa86a4e2982a" />
