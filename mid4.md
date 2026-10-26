# 4번 답안
## 문제: 높이 N을 입력받아 중앙 정렬된 피라미드 형태의 문자 패턴을 출력하시오. 홀수 번째 행은 *, 짝수 번째 행은 #으로 출력합니다.

```
#include <stdio.h>

void main() {
	int n, count = 0;
	scanf_s("%d", &n);
	for (int i = 0; i < n; i++) {
		for (int j = 0; j < n - count - 1; j++) {
			printf(" ");
		}
		if (count % 2 == 0) {
			for (int j = 0; j < count * 2 + 1; j++) {
				printf("*");
			}
		}
		else {
			for (int j = 0; j < count * 2 + 1; j++) {
				printf("#");
			}
		}
		printf("\n");
		count++;
	}
}
```


<img width="1109" height="610" alt="image" src="https://github.com/user-attachments/assets/7a556122-6078-49b6-bfc6-f4b2fedb5048" />

<img width="1105" height="602" alt="image" src="https://github.com/user-attachments/assets/02c718b7-1727-420c-87d0-6ca705a6fec2" />
