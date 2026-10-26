# 2번 답안
## 문제: SQLite를 활용한 간단한 DB를 설계하고 구현 하시오.(시나리오는 각자 만들어서 하시오, 반드시 3명 이상의 데이터를 넣고 검색을 포함해야 합니다.)

```
#include <stdio.h>
#include <stdlib.h>
#include "sqlite3.h"

// SELECT 결과를 출력할 때 사용하는 콜백 함수
int callback(void* NotUsed, int argc, char** argv, char** azColName) {
    for (int i = 0; i < argc; i++) {
        printf("%s = %s\n", azColName[i], argv[i] ? argv[i] : "NULL");
    }
    printf("\n");
    return 0;
}

int main() {
    sqlite3* db;
    char* err_msg = 0;
    int rc;

    // 1. 데이터베이스 파일 열기 (없으면 자동 생성)
    rc = sqlite3_open("school.db", &db);
    if (rc != SQLITE_OK) {
        fprintf(stderr, "DB 열기 실패: %s\n", sqlite3_errmsg(db));
        sqlite3_close(db);
        return 1;
    }

    // 2. 테이블 생성 쿼리
    char* sql_create = "CREATE TABLE IF NOT EXISTS students("
        "id INT, name TEXT, score INT);";
    rc = sqlite3_exec(db, sql_create, 0, 0, &err_msg);
    if (rc != SQLITE_OK) {
        fprintf(stderr, "SQL 에러 (테이블 생성): %s\n", err_msg);
        sqlite3_free(err_msg);
        sqlite3_close(db);
        return 1;
    }

    // 3. 3명 이상의 데이터 삽입 (INSERT)
    char* sql_insert = "INSERT INTO students VALUES (1, 'Kim', 95);"
        "INSERT INTO students VALUES (2, 'Lee', 88);"
        "INSERT INTO students VALUES (3, 'Park', 92);";
    rc = sqlite3_exec(db, sql_insert, 0, 0, &err_msg);
    if (rc != SQLITE_OK) {
        fprintf(stderr, "SQL 에러 (데이터 삽입): %s\n", err_msg);
        sqlite3_free(err_msg);
    }

    // 4. 데이터 검색 및 조회 (SELECT)
    printf("--- [학생 데이터 검색 결과] ---\n");
    char* sql_select = "SELECT * FROM students;";
    rc = sqlite3_exec(db, sql_select, callback, 0, &err_msg);
    if (rc != SQLITE_OK) {
        fprintf(stderr, "SQL 에러 (검색): %s\n", err_msg);
        sqlite3_free(err_msg);
    }

    // 5. DB 연결 닫기
    sqlite3_close(db);
    return 0;
}
```


<img width="1106" height="618" alt="image" src="https://github.com/user-attachments/assets/b694c7db-6366-42d7-bb7d-dab6050a13eb" />
