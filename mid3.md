# 3번 답안
## 숫자야구 판정 게임을 작성하시오.

```
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

int main() {
    int answer[3];
    int user[3];
    int strikes = 0, balls = 0;
    int attempts = 0;

    // 난수 생성 초기화
    srand((unsigned int)time(NULL));

    // 1~9 사이의 중복되지 않는 랜덤 숫자 3개 생성
    answer[0] = (rand() % 9) + 1;
    do {
        answer[1] = (rand() % 9) + 1;
    } while (answer[1] == answer[0]);

    do {
        answer[2] = (rand() % 9) + 1;
    } while (answer[2] == answer[0] || answer[2] == answer[1]);

    // 게임 안내 및 입력 양식 명시
    printf("=========================================\n");
    printf("         NUMBER BASEBALL GAME            \n");
    printf("=========================================\n");
    printf("Rule: Guess 3 unique digits (range: 1 to 9).\n");
    printf("Input Format: Enter three numbers separated by spaces (e.g., 1 2 3)\n");
    printf("-----------------------------------------\n\n");

    // 정답을 맞출 때까지 반복 실행
    while (1) {
        strikes = 0;
        balls = 0;

        printf("Enter your guess: ");
        if (scanf_s("%d %d %d", &user[0], &user[1], &user[2]) != 3) {
            printf("Invalid input! Please enter 3 numbers correctly.\n");
            // 입력 버퍼 비우기 (오류 방지)
            while (getchar() != '\n');
            continue;
        }

        attempts++;

        // 스트라이크 및 볼 판정 로직
        for (int i = 0; i < 3; i++) {
            for (int j = 0; j < 3; j++) {
                if (answer[i] == user[j]) {
                    if (i == j) {
                        strikes++; // 숫자와 위치가 모두 같은 경우
                    }
                    else {
                        balls++;   // 숫자는 같지만 위치가 다른 경우
                    }
                }
            }
        }

        // 결과 출력
        printf("Result -> %d Strike(s), %d Ball(s)\n", strikes, balls);

        // 3 스트라이크 달성 시 홈런 출력 및 게임 종료
        if (strikes == 3) {
            printf("\n=========================================\n");
            printf("              HOMERUN!                   \n");
            printf("  You solved the puzzle in %d attempts!  \n", attempts);
            printf("=========================================\n");
            break;
        }
        printf("\n");
    }

    return 0;
}
```


<img width="1106" height="622" alt="image" src="https://github.com/user-attachments/assets/aa0787aa-974b-4e3a-a8b0-b6b0ce68b9c2" />
