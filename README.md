## 🦆 Duckey – AI 감정 분석 기반 정서 케어 플랫폼

Duckey는 사용자의 음성에서 감정과 상황을 분석하고, 캐릭터와 대화를 통해 공감을 전달한 뒤, 그 감정에 맞는 숏폼 영상을 추천하는 정서 케어 앱입니다. 청년층의 불안과 우울이 증가하는 사회적 현실 속에서, 부담 없이 마음을 털어놓고 즉각적인 환기를 얻을 수 있도록 설계되었습니다.

## 🌟 핵심 기능
	•	음성 감정 분석: 사용자가 이야기하는 내용을 Whisper를 통해 텍스트로 변환한 후, 모델이 감정(슬픔·분노·기쁨 등)과 상황(이별·실수·갈등 등)을 라벨링합니다. 분석된 감정 정보는 사용자 맞춤형 반응과 추천에 활용됩니다.
	•	캐릭터 기반 공감 대화: MBTI 성향을 반영한 F형(감정 중심) 또는 T형(조언 중심) 캐릭터가 대화를 통해 공감과 조언을 제공합니다. 대화가 쌓일수록 캐릭터는 사용자 히스토리를 학습해 더욱 개인화된 반응을 생성합니다.
	•	숏폼 추천: 감정·상황 라벨을 바탕으로 YouTube Shorts API를 통해 맞춤형 숏폼 영상 5편을 추천합니다. 추천 영상 중 일부는 감정에 맞는 제품·서비스 광고로 구성해 감성 커머스를 구현합니다.
	•	피드백 루프: 사용자가 영상과 반응에 대해 피드백(좋아요/별로예요/도움돼요)을 남기면 추천 알고리즘이 학습하여 정확도를 높입니다.

## 🛠️ 기술 아키텍처

Duckey는 프런트엔드와 백엔드를 하나의 저장소에서 관리합니다. 주요 기술 스택은 다음과 같습니다.

### 프런트엔드 (React + Vite)
	
  •	구조: src/ 디렉터리에는 컴포넌트와 훅, 스크린이 분리되어 있습니다. 예를 들어 components/AnimatedDuckCharacter.jsx는 덕 캐릭터 애니메이션을 구현하며, hooks/useSpeechRecognition.js와 hooks/useSpeechSynthesis.js는 브라우저 음성 인식과 음성 합성을 처리합니다. lib/utils.js에서는 Tailwind 클래스 병합 등 유틸리티 함수를 제공합니다.
	•	UI 라이브러리: Tailwind CSS와 shadcn/ui를 사용하여 미니멀하면서도 따뜻한 디자인을 구현했습니다.
	•	음성 인터페이스: Media Recorder API로 음성을 녹음하고, 프런트엔드에서 백엔드 API로 업로드하여 감정 분석을 요청합니다.

### 백엔드 (Java Spring Boot)
	
  •	API 모듈: api/ 디렉터리에는 Java 기반의 Spring Boot 프로젝트가 있습니다. 컨트롤러 계층은 ChatController, ChatGPTController, EmotionController, VideoController 등으로 나뉘며, 각 기능별 REST API를 제공합니다. 예를 들어 EmotionController는 음성 파일을 받아 감정 분석 및 추천을 수행하고 결과를 반환합니다, ChatGPTController는 OpenAI API를 호출해 캐릭터 대화 응답을 생성합니다.
	•	서비스 계층: service/ 패키지는 감정 분석, 챗봇 대화, 추천 로직 등을 담당합니다. 외부 서비스로 OpenAI API, 유튜브 데이터 API를 연동하여 분석과 추천을 처리합니다.
	•	보안 및 인증: security/ 패키지에서 사용자 인증과 토큰 관리를 처리하며, UserController에서 회원가입·로그인·피드백 기록 등을 담당합니다.

## 데이터 흐름
	
  1.	사용자 발화: 사용자가 앱에서 음성을 녹음하면 프런트엔드가 Whisper API를 호출해 텍스트로 변환합니다.
	2.	감정·상황 분석: 백엔드의 EmotionController가 텍스트와 음성 특징을 분석하여 감정과 상황 라벨을 추출합니다.
	3.	캐릭터 응답 생성: ChatGPTController가 캐릭터 성향(F/T)에 맞는 공감 대화와 행동 팁을 생성합니다.
	4.	숏폼 추천: EmotionController 또는 VideoController가 감정 라벨에 맞춰 YouTube Shorts 영상을 검색하여 5개를 추천합니다.
	5.	피드백 학습: 사용자의 피드백은 추천 알고리즘에 저장되어 이후 추천의 정확도를 높입니다.

## 📂 주요 디렉터리 구조
```
Duckey/
├── api/               # Spring Boot 백엔드 (REST API)
│   ├── src/
│   │   ├── main/java/com/duckchat/api/
│   │   │   ├── controller/    # Auth, Chat, ChatGPT, Emotion, Video 등 컨트롤러
│   │   │   ├── service/       # 비즈니스 로직: 감정 분석, 대화 생성, 추천
│   │   │   ├── repository/    # JPA 리포지토리
│   │   │   ├── entity/        # 데이터 모델
│   │   │   ├── security/      # 인증·인가 설정
│   │   │   └── ...
│   │   └── resources/         # application.yml 등 설정 파일
│   └── build.gradle, Dockerfile 등
├── public/           # 정적 리소스 (이미지, 아이콘)
├── src/              # React 프런트엔드
│   ├── components/   # UI 컴포넌트 (AnimatedDuckCharacter, ProductCard, SpeechBubble 등)
│   ├── hooks/        # 커스텀 훅 (useSpeechRecognition, useSpeechSynthesis 등)
│   ├── screens/      # 페이지 단위 화면 (App.jsx 등)
│   ├── lib/          # 유틸리티 함수, 테마 설정
│   ├── index.html    # Vite 엔트리 포인트
│   └── main.jsx      # React 엔트리 포인트
├── package.json      # 프런트엔드 의존성 및 스크립트
├── tailwind.config.js
└── README.md         # 프로젝트 소개 문서
```

## 🚀 시작하기

필수 조건
	•	Node.js 18 이상, npm 또는 pnpm
	•	Java 17 이상, Gradle
	•	OpenAI API 키 및 유튜브 데이터 API 키

설치 및 실행
	1.  Front
  ```
  # 프로젝트 루트에서
cd src
npm install      # 또는 pnpm install
npm run dev      # 로컬 개발 서버 (Vite)
npm run build    # 프로덕션 빌드
  ```

  2. Back
  ```
# 프로젝트 루트에서
cd api
./gradlew bootRun     # Spring Boot 서버 실행
./gradlew build       # 빌드 (jar 생성)
  ```

  3.	.env 또는 application.yml에 OpenAI 및 YouTube API 키를 설정해야 합니다. 자세한 API 키 설정 방법은 api 모듈의 application.yml 예시를 참고하세요.

### 도커 실행

루트 디렉터리의 Dockerfile을 이용해 백엔드 컨테이너를 빌드할 수 있으며, docker-compose.yml을 작성해 프런트엔드와 백엔드를 함께 실행할 수 있습니다.

## 💡 향후 확장 계획
	
  •	감정 커머스: 감정 라벨과 상품 카테고리를 매핑해 추천 코드 수익화를 실험합니다.
	•	감정 콘텐츠 제휴: 독서·음악 구독 서비스(예: 밀리의 서재, 스포티파이)와 연계해 감정 기반 콘텐츠를 추천합니다.
	•	B2B 확장: 대학 상담 센터나 기업 HR팀을 위한 감정 리포트 플랫폼으로 확장하여 정서 관리 SaaS 형태로 제공할 계획입니다.
	•	위기 감지 기능(추후): 위기 감지는 민감한 기능이므로 전문가 자문과 윤리적 검토 후 향후 버전에 반영할 예정입니다.
