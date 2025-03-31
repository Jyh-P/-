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

이 기능을 백엔드(스프링)에서 어떻게 API로 제공할지, DB에 어떻게 저장할지 정리하면 좋아.



전체 시스템 구조 정리 

프론트엔드 → 사용자 UI

백엔드 → 데이터 처리

DB → 데이터 저장

AI 모델 → 문제 생성, 피드백 분석

서버 → 전체 서비스 운영


