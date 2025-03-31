# -
대학 캡스톤디자인 DB파트

DB 기본 설계 방향

주요엔터티 후보

사용자: 학생, 교사, 학부모 구분

문제: AI 생성/공공 데이터 문제 정보 저장

풀이: 학생의 문제 풀이 기록

성취도: 정답률, 풀이 시간 등 데이터 저장

AI 분석: AI가 제공한 피드백 저장


ERD)
![image](https://github.com/user-attachments/assets/1444be64-002a-4271-9f02-6db537b9ca1f)

https://dbdiagram.io/d

Table users {

    user_id INT [primary key]
    
    username VARCHAR(50) [not null]
    
    email VARCHAR(100) [not null, unique]
    
    password VARCHAR(255) [not null]
    
    role ENUM('student', 'teacher', 'parent') [not null]
    
    created_at TIMESTAMP [default: "CURRENT_TIMESTAMP"]
    
}

Table problems {

    problem_id INT [primary key]
    
    question TEXT [not null]
    
    answer VARCHAR(255) [not null]
    
    difficulty ENUM('easy', 'medium', 'hard') [not null]
    
    source ENUM('AI', 'public', 'manual') [not null]
    
    created_at TIMESTAMP [default: "CURRENT_TIMESTAMP"]
    
}

Table solutions {

    solution_id INT [primary key]
    
    user_id INT [not null]
    
    problem_id INT [not null]
    
    user_answer VARCHAR(255) [not null]
    
    is_correct BOOLEAN [not null]
    
    time_taken INT [not null]
    
    solved_at TIMESTAMP [default: "CURRENT_TIMESTAMP"]
    
}

Table achievements {

    achievement_id INT [primary key]
    
    user_id INT [not null]
    
    correct_rate FLOAT [not null]
    
    avg_time_taken FLOAT [not null]
    
    repeated_mistakes TEXT
    
    updated_at TIMESTAMP [default: "CURRENT_TIMESTAMP"]
    
}

Ref: solutions.user_id > users.user_id

Ref: solutions.problem_id > problems.problem_id

Ref: achievements.user_id > users.user_id




AI 활용 방식 정리

문제 생성: 기존 문제 + AI 생성 문제 병합

실시간 피드백: AI가 풀이 과정 분석 후 힌트 제공

난이도 조절: 학생의 정답률, 풀이 시간 분석 후 문제 추천

심리 분석: 감정 상태 기반 학습 방식 조정 (예: 쉬운 문제 추천)

문제 생성 (AI + 기존 문제 병합)

Endpoint: POST /api/problems/generate

요청 데이터:

json

{

  "topic": "algebra",
  
  "difficulty": "medium"
  
}

응답 데이터:

json

{

  "problem_id": 101,
  
  "question": "Solve for x: 3x + 5 = 20",
  
  "answer": "x = 5",
  
  "source": "AI"
  
}

실시간 피드백 (풀이 과정 분석)

Endpoint: POST /api/feedback

요청 데이터:

json

{

  "user_id": 1,
  
  "problem_id": 101,
  
  "user_answer": "x = 6"
  
}

응답 데이터:

json

{

  "is_correct": false,
  
  "hint": "다시 확인해보세요! 3x + 5 = 20을 푸는 첫 번째 단계는 5를 빼는 것입니다."
  
}

난이도 조절 (정답률 & 풀이 시간 기반 추천)
 
Endpoint: GET /api/recommendations/{user_id}

응답 데이터:

json

{

  "recommended_difficulty": "medium",
  
  "next_problems": [102, 103, 104]
  
}

심리 분석 (감정 기반 학습 조정)

Endpoint: GET /api/mood-adjustment/{user_id}

응답 데이터:

json

{

  "mood": "frustrated",
  
  "adjustment": "쉬운 문제를 추천합니다."
  
}

2. DB 설계 (MySQL)
3. 
problems 테이블 (문제 데이터 저장)

sql

CREATE TABLE problems (
    problem_id INT AUTO_INCREMENT PRIMARY KEY,
    question TEXT NOT NULL,
    answer VARCHAR(255) NOT NULL,
    difficulty ENUM('easy', 'medium', 'hard') NOT NULL,
    source ENUM('AI', 'public', 'manual') NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
solutions 테이블 (풀이 데이터 저장)
sql
CREATE TABLE solutions (
    solution_id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT NOT NULL,
    problem_id INT NOT NULL,
    user_answer VARCHAR(255) NOT NULL,
    is_correct BOOLEAN NOT NULL,
    time_taken INT NOT NULL,
    solved_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(user_id),
    FOREIGN KEY (problem_id) REFERENCES problems(problem_id)
);
 user_performance 테이블 (정답률 & 풀이 시간 추적)
sql

CREATE TABLE user_performance (
    user_id INT PRIMARY KEY,
    correct_rate FLOAT NOT NULL,
    avg_time_taken FLOAT NOT NULL,
    last_updated TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(user_id)
);
user_mood 테이블 (감정 상태 저장)
sql

CREATE TABLE user_mood (
    user_id INT PRIMARY KEY,
    mood ENUM('neutral', 'frustrated', 'confident') NOT NULL,
    last_updated TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(user_id)
);


전체 시스템 구조 정리 

프론트엔드 → 사용자 UI

백엔드 → 데이터 처리

DB → 데이터 저장

AI 모델 → 문제 생성, 피드백 분석

서버 → 전체 서비스 운영


