# KakaoTalk 오픈채팅 추출 및 데이터 관리 연구 보고서
# KakaoTalk OpenChat Extraction & Data Management Research Report

## 📋 목차 (Table of Contents)

1. [핵심 요약](#핵심-요약)
2. [오픈채팅 백업 및 추출 방법](#오픈채팅-백업-및-추출-방법)
3. [첨부파일 처리 및 유효기간](#첨부파일-처리-및-유효기간)
4. [공식 API 현황](#공식-api-현황)
5. [자동화 도구 및 솔루션](#자동화-도구-및-솔루션)
6. [구현 시나리오 및 권장사항](#구현-시나리오-및-권장사항)
7. [법적 고려사항 및 제한사항](#법적-고려사항-및-제한사항)

---

## 핵심 요약

### 주요 발견사항

❌ **카카오톡 공식 API로는 오픈채팅 내용을 프로그래밍 방식으로 추출할 수 없습니다.**

✅ **사용 가능한 방법:**
- 카카오톡 내장 "대화 내용 내보내기" 기능 (수동)
- 삼성 스마트 스위치를 통한 백업 (삼성 기기 한정)
- 서드파티 도구를 사용한 수동 추출

### 핵심 제약사항

1. **백업 불가**: 오픈채팅은 카카오톡의 자동 백업 대상에 포함되지 않음
2. **복원 불가**: 내보낸 파일로 새 기기에서 복원 불가능
3. **API 미제공**: 오픈채팅 메시지 조회 API가 공식적으로 제공되지 않음
4. **첨부파일 만료**: 서버에 임시 저장된 파일은 일정 기간 후 다운로드 불가

---

## 오픈채팅 백업 및 추출 방법

### 1. 카카오톡 내장 내보내기 기능 (공식)

#### 📱 모바일 앱 절차

```
오픈채팅방 접속
└─> 우상단 메뉴(≣) 터치
    └─> 우하단 톱니바퀴(⚙️) 터치
        └─> "대화 내용 내보내기" 선택
            └─> 옵션 선택:
                ├─ "텍스트만 보내기"
                └─ "모든 메시지 도큐멘트로 저장"
```

#### 💻 데스크톱 앱 절차

```
오픈채팅방 접속
└─> 좌측 하단 자신의 이니셜 클릭
    └─> 설정 메뉴
        └─> 개인정보
            └─> "데이터 내보내기" 선택
                └─> 이메일로 다운로드 링크 수신 (약 10분 소요)
```

#### 📦 내보내기 결과물

내보낸 압축 파일(.zip)에 포함되는 내용:

| 항목 | 포맷 | 설명 |
|-----|------|------|
| 대화 내용 | `.txt` | 텍스트 형식의 대화 기록 |
| 이미지 | `.jpg`, `.png` 등 | 채팅방에서 공유된 이미지 파일 |
| 기타 파일 | 다양 | 저장 시점에 다운로드 가능한 첨부파일 |

**주의사항:**
- 1MB 이상의 대화는 자동으로 여러 파일로 분할됨
- 내보낸 파일로는 복원 불가능 (아카이빙 용도만)
- 유효기간이 지난 첨부파일은 포함되지 않음

### 2. 삼성 스마트 스위치 (Samsung Smart Switch)

#### 특징
- **최초의 오픈채팅 백업 지원 솔루션**
- 삼성 갤럭시 기기 간 데이터 이전 시 오픈채팅 복원 가능
- 안드로이드 사용자 사이에서 오픈채팅 인기를 반영한 기능

#### 제한사항
- 삼성 갤럭시 기기 간 이동에만 적용
- 다른 제조사 기기와는 호환 불가

---

## 첨부파일 처리 및 유효기간

### 파일 저장 정책

#### 📁 일반 파일 유효기간

카카오톡에서 전송되는 멀티미디어 파일의 서버 저장 정책:

| 파일 유형 | 저장 위치 | 유효기간 |
|----------|----------|----------|
| 사진, 동영상 | 카카오 서버 임시 저장 | 수신 후 **일정 기간** (명시되지 않음) |
| 일반 파일 | 카카오 서버 임시 저장 | 수신 후 **일정 기간** |
| 롱탭 검색 이미지 | 카카오 서버 임시 저장 | 수신 후 **일정 기간** |
| 톡게시판 첨부파일 | 카카오 서버 | **1년** (2023년 9월 4일 이후) |

#### ⚠️ 파일 만료 원인

1. **서버 정책**: 메시지 수신 후 일정 기간 경과 시 임시 데이터 삭제
2. **캐시 삭제**: 사용자가 카카오톡 캐시를 삭제한 경우
3. **기기 변경**: 새 기기로 이동 시 이전 파일 접근 불가

#### 💾 톡게시판 파일 정책 (2023년 9월 4일부터)

```
업로드 시점: 2023년 9월 4일 이후
└─> 저장 기간: 1년
    └─> 만료 처리: 업로드 1년 후 자동 삭제
        └─> 이전 파일도 2023년 9월 4일부터 1년 후 삭제
```

### 만료된 파일 복구

#### ❌ 복구 불가능

> "지금은 셀프로는 카카오톡 만료된 파일 복구가 불가능하다"

과거에는 일부 메신저 파일 관리 서비스를 통해 만료된 파일의 일부를 확인하고 스크린샷으로 복구할 수 있었으나, 현재는 해당 기능이 삭제됨.

#### ✅ 예방책

**톡서랍 플러스 (Tok Drawer Plus)** - 유료 백업 서비스
- 메시지 및 파일을 클라우드에 자동 백업
- 만료 전에 미리 백업하여 파일 손실 방지
- 유료 서비스

### 첨부파일 다운로드 전략

요청하신 기능을 구현하려면:

```python
# 개념적 워크플로우
def download_valid_attachments(chat_data):
    """
    유효기간 내 첨부파일만 다운로드
    """
    for message in chat_data:
        if message.has_attachment:
            try:
                # 파일 접근 시도
                download_file(message.attachment_url)
                print(f"✅ 다운로드 성공: {message.attachment_name}")
            except FileExpiredError:
                # 만료된 파일 처리
                print(f"⏰ 만료된 파일: {message.attachment_name}")
                log_expired_file(message)
            except Exception as e:
                print(f"❌ 다운로드 실패: {e}")
```

**실제 구현 시 문제점:**
- 카카오톡 API가 첨부파일 URL에 대한 프로그래밍 접근을 제공하지 않음
- 내보내기 기능 사용 시점에 유효한 파일만 포함됨
- 실시간 유효성 검사 API 없음

---

## 공식 API 현황

### Kakao Developers API 개요

#### 📡 제공되는 API

| API 카테고리 | 기능 | 오픈채팅 지원 |
|-------------|------|--------------|
| **Kakao Login** | 사용자 인증 | ❌ |
| **Kakao Talk Message** | 나에게/친구에게 메시지 전송 | ❌ |
| **Kakao Talk Channel** | 채널 추가, 1:1 채팅 시작 | ❌ |
| **Kakao Talk Share** | 메시지 공유 및 전달 알림 | ⚠️ 전송만 가능 |
| **Kakao Moment** | 광고 및 마케팅 | ❌ |

#### ❌ 제공되지 않는 기능

카카오 개발자 문서를 철저히 조사한 결과, 다음 기능은 **공식적으로 제공되지 않습니다**:

1. ❌ 채팅 메시지 조회 (Chat History Retrieval)
2. ❌ 오픈채팅 데이터 접근 (OpenChat Data Access)
3. ❌ 메시지 백업 API (Message Backup API)
4. ❌ 첨부파일 관리 API (Attachment Management API)
5. ❌ 대화방 메시지 읽기 (Read Messages from Chat Rooms)

### Kakao Talk Message API 상세

#### 🔑 주요 엔드포인트

**메시지 전송** (Message Sending)

```
POST /v2/api/talk/memo/default/send
- 나에게 메시지 보내기 (Send to Me)

POST /v2/api/talk/friends/message/default/send
- 친구에게 메시지 보내기 (Send to Friends)
- 요구사항: KakaoTalk Social API 권한
```

**메시지 전달 알림** (Delivery Webhook)

```
Webhook Callback
- 메시지가 친구나 채팅방에 성공적으로 전달되면 알림
- 전송 통계 확인 가능
```

#### ⚠️ API 제약사항

> "KakaoTalk Message API **only supports message sending** between users within the same service"

- **읽기 불가**: 기존 메시지 조회 불가
- **전송만 가능**: 새 메시지 발송만 지원
- **히스토리 없음**: 대화 기록 접근 API 없음

### 비즈니스 메시징 API (알림톡/친구톡)

#### 📨 기업용 메시징 솔루션

카카오는 기업용 메시징 API를 별도로 제공:

**알림톡 (AlimTalk)** - 정보성 알림
```
POST /v2/info/message/send - 메시지 발송
GET  /v2/info/message/search - 발송 상태 조회
```

**친구톡 (FriendTalk)** - 마케팅 메시지
```
POST /v2/friend/message/send - 메시지 발송
GET  /v2/friend/message/search - 발송 상태 조회
```

**주요 특징:**
- ✅ 발송 후 상태 조회 가능
- ✅ 메시지 전송 결과 확인
- ❌ 수신자의 응답 메시지는 조회 불가
- ❌ 일반 채팅과는 별개의 시스템

---

## 자동화 도구 및 솔루션

### 1. 오픈소스 도구

#### 🐍 kakaotalk-analyzer (Python)

**GitHub**: `graup/kakaotalk-analyzer`

**기능:**
- 내보낸 카카오톡 파일 통계 분석
- 자동으로 분할된 파일 인식 및 병합 (1MB 이상 대화)
- 시간대별 메시지 빈도 시각화

**사용법:**
```bash
# 설치
git clone https://github.com/graup/kakaotalk-analyzer
cd kakaotalk-analyzer

# 실행
python kakaotalk.py [file-path] [action] [period]

# 예시
python kakaotalk.py exported_chat.txt analyze weekly
```

**입력 파일 준비:**
1. 카카오톡에서 "텍스트만 내보내기" 실행
2. 내보낸 .txt 파일을 스크립트 경로에 위치
3. 분할된 파일들은 자동으로 인식됨

**제한사항:**
- 마지막 호환 버전: KakaoTalk 4.5.2
- 텍스트만 분석 (미디어 파일 미지원)
- 프로젝트 상태: "Work in Progress"
- 커뮤니티 활동 낮음 (1 star)

### 2. 자동화 접근 방식

#### 🤖 GUI 자동화 (Python)

공식 API가 없어서 일부 개발자들이 사용하는 방법:

**pyautogui 기반 자동화**

```python
import pyautogui
import pyperclip
import ctypes

# 오픈채팅 창 활성화
def activate_chat_window(chat_name):
    """
    채팅방 이름으로 윈도우 찾기
    """
    # ctypes로 윈도우 핸들 찾기
    hwnd = ctypes.windll.user32.FindWindowW(None, chat_name)
    ctypes.windll.user32.SetForegroundWindow(hwnd)

# 대화 내용 복사
def copy_chat_content():
    """
    채팅 내용을 클립보드로 복사
    """
    pyautogui.hotkey('ctrl', 'a')  # 전체 선택
    time.sleep(0.5)
    pyautogui.hotkey('ctrl', 'c')  # 복사
    time.sleep(0.5)
    return pyperclip.paste()

# 대화 내용 저장
def save_chat():
    """
    채팅 저장 단축키 실행
    """
    pyautogui.hotkey('ctrl', 's')  # 저장
```

**⚠️ 문제점:**
1. **불안정성**: 대화 내용이 많으면 클립보드가 제대로 저장 안 됨
2. **속도**: 매우 느림 (GUI 조작 대기 시간)
3. **신뢰성**: 윈도우 포커스, 해상도 등에 영향받음
4. **유지보수**: 카카오톡 UI 변경 시 코드 수정 필요

### 3. 서드파티 솔루션

#### 🌐 Kakao Openchat Search API (비공식)

**GitHub**: `parkkw472/Kakao-Openchat-Search-API`
**문서**: https://docs.develope.kr/

**기능:**
- 오픈채팅방 검색 API 제공
- 채팅방 목록 조회
- 연구 목적으로 제공

**⚠️ 주의사항:**
```
"이 소스 코드는 연구 목적으로만 사용하십시오.
사용에 대한 모든 책임은 사용자에게 있습니다."
```

#### 🏢 상업용 스크래핑 서비스

일부 기업이 카카오톡 데이터 스크래핑 서비스 제공:
- Actowiz Solutions 등
- 유료 서비스
- 데이터 수집 및 분석 자동화

---

## 구현 시나리오 및 권장사항

### 요청하신 기능 구현 방안

> "수많은 오픈채팅방에서 원하는 내용을 정리해서 볼 수 있는 기능"

#### 📊 아키텍처 개요

```
┌─────────────────────────────────────────────────────────┐
│  1. 데이터 수집 (Data Collection)                          │
├─────────────────────────────────────────────────────────┤
│  • 카카오톡 "대화 내용 내보내기" 기능 사용                   │
│  • 여러 오픈채팅방에서 반복적으로 내보내기 실행              │
│  • ZIP 파일 수집 및 저장                                   │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│  2. 데이터 파싱 (Data Parsing)                             │
├─────────────────────────────────────────────────────────┤
│  • ZIP 파일 자동 압축 해제                                 │
│  • .txt 파일 파싱 (메시지, 타임스탬프, 사용자)              │
│  • 첨부파일 분류 및 메타데이터 추출                         │
│  • 링크 추출 및 유효성 검사                                │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│  3. 데이터 처리 (Data Processing)                          │
├─────────────────────────────────────────────────────────┤
│  • NLP 기반 주제 분류 (토픽 모델링)                        │
│  • 중요 내용 자동 요약                                     │
│  • 링크 메타데이터 크롤링                                  │
│  • 첨부파일 유효성 확인 및 다운로드                         │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│  4. 마크다운 생성 (Markdown Generation)                    │
├─────────────────────────────────────────────────────────┤
│  • 채팅방별 마크다운 문서 생성                              │
│  • 주요 내용 요약 섹션                                     │
│  • 링크 목록 (클릭 가능)                                   │
│  • 첨부파일 목록 및 다운로드 링크                           │
│  • 시간순/주제별 정렬 옵션                                 │
└─────────────────────────────────────────────────────────┘
```

### 구현 예시 코드

#### Python 기반 구현

```python
import zipfile
import re
from pathlib import Path
from datetime import datetime
from collections import defaultdict
import requests
from bs4 import BeautifulSoup

class KakaoTalkOpenChatOrganizer:
    """
    카카오톡 오픈채팅 데이터를 정리하고 마크다운으로 변환
    """

    def __init__(self, export_folder):
        self.export_folder = Path(export_folder)
        self.chats_data = []

    def extract_all_exports(self):
        """
        내보낸 ZIP 파일들을 모두 압축 해제
        """
        for zip_file in self.export_folder.glob("*.zip"):
            extract_path = self.export_folder / zip_file.stem
            with zipfile.ZipFile(zip_file, 'r') as zip_ref:
                zip_ref.extractall(extract_path)
            print(f"✅ 압축 해제: {zip_file.name}")

    def parse_chat_file(self, txt_file):
        """
        카카오톡 텍스트 파일 파싱
        """
        with open(txt_file, 'r', encoding='utf-8') as f:
            content = f.read()

        # 메시지 패턴: 2024-01-15 14:30, 사용자명 : 메시지
        pattern = r'(\d{4}-\d{2}-\d{2} \d{2}:\d{2}), (.+?) : (.+?)(?=\n\d{4}-\d{2}-\d{2}|\Z)'
        messages = re.findall(pattern, content, re.DOTALL)

        parsed_messages = []
        for timestamp, user, message in messages:
            parsed_messages.append({
                'timestamp': datetime.strptime(timestamp, '%Y-%m-%d %H:%M'),
                'user': user.strip(),
                'message': message.strip(),
                'links': self.extract_links(message),
                'has_attachment': self.check_attachment_reference(message)
            })

        return parsed_messages

    def extract_links(self, message):
        """
        메시지에서 링크 추출
        """
        url_pattern = r'https?://[^\s]+'
        return re.findall(url_pattern, message)

    def check_attachment_reference(self, message):
        """
        첨부파일 참조 확인
        """
        attachment_keywords = ['사진', '동영상', '파일', '이미지']
        return any(keyword in message for keyword in attachment_keywords)

    def get_link_metadata(self, url):
        """
        링크의 제목 및 설명 가져오기
        """
        try:
            response = requests.get(url, timeout=5)
            soup = BeautifulSoup(response.content, 'html.parser')

            title = soup.find('title')
            title = title.string if title else url

            description = soup.find('meta', attrs={'name': 'description'})
            description = description['content'] if description else ""

            return {'title': title, 'description': description, 'url': url}
        except:
            return {'title': url, 'description': '', 'url': url}

    def summarize_messages(self, messages, max_length=500):
        """
        메시지 요약 (간단한 버전)
        실제로는 LLM API나 요약 모델 사용 권장
        """
        combined_text = " ".join([m['message'] for m in messages])

        # 간단한 요약: 가장 긴 메시지들 선택
        sorted_messages = sorted(messages, key=lambda x: len(x['message']), reverse=True)
        summary = []
        current_length = 0

        for msg in sorted_messages[:10]:
            if current_length + len(msg['message']) <= max_length:
                summary.append(f"- {msg['user']}: {msg['message'][:100]}...")
                current_length += len(msg['message'])

        return "\n".join(summary)

    def generate_markdown(self, chat_name, messages, attachments_folder):
        """
        마크다운 문서 생성
        """
        # 날짜별 그룹화
        messages_by_date = defaultdict(list)
        for msg in messages:
            date_key = msg['timestamp'].strftime('%Y-%m-%d')
            messages_by_date[date_key].append(msg)

        # 링크 수집
        all_links = []
        for msg in messages:
            all_links.extend(msg['links'])
        unique_links = list(set(all_links))

        # 첨부파일 목록
        attachments = []
        if attachments_folder.exists():
            attachments = list(attachments_folder.glob("*"))

        # 마크다운 생성
        md_content = f"# {chat_name}\n\n"
        md_content += f"**추출 날짜**: {datetime.now().strftime('%Y-%m-%d %H:%M')}\n\n"
        md_content += f"**메시지 수**: {len(messages):,}개\n\n"
        md_content += f"**기간**: {messages[0]['timestamp'].strftime('%Y-%m-%d')} ~ {messages[-1]['timestamp'].strftime('%Y-%m-%d')}\n\n"

        # 주요 내용 요약
        md_content += "## 📝 주요 내용 요약\n\n"
        md_content += self.summarize_messages(messages)
        md_content += "\n\n"

        # 공유된 링크
        if unique_links:
            md_content += "## 🔗 공유된 링크\n\n"
            for link in unique_links[:20]:  # 최대 20개
                link_meta = self.get_link_metadata(link)
                md_content += f"### [{link_meta['title']}]({link_meta['url']})\n"
                if link_meta['description']:
                    md_content += f"> {link_meta['description']}\n\n"
                else:
                    md_content += "\n"

        # 첨부파일 목록
        if attachments:
            md_content += "## 📎 첨부파일 (유효기간 내)\n\n"
            for attachment in attachments:
                file_size = attachment.stat().st_size / 1024  # KB
                md_content += f"- [{attachment.name}]({attachment.relative_to(self.export_folder)}) ({file_size:.1f} KB)\n"
            md_content += "\n"
            md_content += "*⚠️ 유효기간이 지난 첨부파일은 포함되지 않았습니다.*\n\n"

        # 날짜별 대화
        md_content += "## 💬 대화 내용\n\n"
        for date in sorted(messages_by_date.keys()):
            md_content += f"### {date}\n\n"
            for msg in messages_by_date[date]:
                time_str = msg['timestamp'].strftime('%H:%M')
                md_content += f"**{time_str}** - **{msg['user']}**: {msg['message']}\n\n"

        return md_content

    def process_all_chats(self):
        """
        모든 채팅방 처리 및 마크다운 생성
        """
        # 압축 해제
        self.extract_all_exports()

        # 각 채팅방 폴더 처리
        for chat_folder in self.export_folder.iterdir():
            if not chat_folder.is_dir():
                continue

            chat_name = chat_folder.name
            print(f"\n처리 중: {chat_name}")

            # 텍스트 파일 찾기
            txt_files = list(chat_folder.glob("*.txt"))
            if not txt_files:
                print(f"  ⚠️ 텍스트 파일 없음")
                continue

            # 메시지 파싱
            all_messages = []
            for txt_file in txt_files:
                messages = self.parse_chat_file(txt_file)
                all_messages.extend(messages)
                print(f"  ✅ 파싱: {txt_file.name} ({len(messages):,}개 메시지)")

            # 시간순 정렬
            all_messages.sort(key=lambda x: x['timestamp'])

            # 마크다운 생성
            md_content = self.generate_markdown(chat_name, all_messages, chat_folder)

            # 마크다운 파일 저장
            output_file = self.export_folder / f"{chat_name}_정리.md"
            with open(output_file, 'w', encoding='utf-8') as f:
                f.write(md_content)

            print(f"  💾 저장: {output_file.name}")

# 사용 예시
if __name__ == "__main__":
    organizer = KakaoTalkOpenChatOrganizer("./kakaotalk_exports")
    organizer.process_all_chats()
    print("\n✨ 모든 채팅방 처리 완료!")
```

### 실행 워크플로우

#### 1단계: 데이터 수집

```bash
# 각 오픈채팅방에서 수동으로:
# 1. 채팅방 메뉴 → 설정 → 대화 내용 내보내기
# 2. "모든 메시지 도큐멘트로 저장" 선택
# 3. 내보낸 ZIP 파일을 ./kakaotalk_exports/ 폴더에 저장
```

#### 2단계: 스크립트 실행

```bash
# 의존성 설치
pip install requests beautifulsoup4

# 스크립트 실행
python organize_kakaotalk.py
```

#### 3단계: 결과물

```
kakaotalk_exports/
├── 오픈채팅방1_정리.md
├── 오픈채팅방2_정리.md
├── 오픈채팅방3_정리.md
└── ...
```

### 마크다운 결과물 예시

```markdown
# AI 개발자 오픈채팅방

**추출 날짜**: 2025-01-10 14:30

**메시지 수**: 15,234개

**기간**: 2024-06-01 ~ 2025-01-10

## 📝 주요 내용 요약

- 김철수: 최신 LLM 모델 성능 비교 자료 공유했습니다...
- 이영희: RAG 구현 시 벡터 DB 선택 기준에 대해 질문...
- 박민수: 프롬프트 엔지니어링 베스트 프랙티스 정리...

## 🔗 공유된 링크

### [Attention Is All You Need - arXiv](https://arxiv.org/abs/1706.03762)
> Transformer 모델을 소개하는 원본 논문

### [LangChain Documentation](https://python.langchain.com/docs/)
> LLM 애플리케이션 개발을 위한 프레임워크

## 📎 첨부파일 (유효기간 내)

- architecture_diagram.png (245.3 KB)
- benchmark_results.xlsx (89.7 KB)
- code_example.py (12.4 KB)

*⚠️ 유효기간이 지난 첨부파일은 포함되지 않았습니다.*

## 💬 대화 내용

### 2024-06-01

**09:15** - **김철수**: 안녕하세요! GPT-4와 Claude 3.5 Sonnet의 코딩 능력을 비교한 벤치마크 자료 공유합니다.

**09:18** - **이영희**: 감사합니다! 특히 긴 컨텍스트 처리 성능이 궁금했는데 도움이 되네요.

...
```

### 향상된 기능 추가

#### 🤖 AI 기반 요약 (OpenAI/Anthropic API 사용)

```python
import anthropic

def ai_summarize(messages, max_tokens=500):
    """
    Claude API를 사용한 지능형 요약
    """
    client = anthropic.Anthropic(api_key="your-api-key")

    # 메시지 샘플링 (너무 많으면 샘플링)
    sample_size = min(100, len(messages))
    sampled = messages[::len(messages)//sample_size]

    combined_text = "\n".join([
        f"{msg['user']}: {msg['message']}"
        for msg in sampled
    ])

    prompt = f"""다음은 카카오톡 오픈채팅방의 대화 내용입니다.
주요 토픽과 핵심 내용을 3-5개의 bullet point로 요약해주세요.

대화 내용:
{combined_text}

요약:"""

    message = client.messages.create(
        model="claude-3-5-sonnet-20250110",
        max_tokens=max_tokens,
        messages=[
            {"role": "user", "content": prompt}
        ]
    )

    return message.content[0].text
```

#### 🏷️ 자동 태깅 및 분류

```python
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.decomposition import LatentDirichletAllocation

def extract_topics(messages, n_topics=5):
    """
    토픽 모델링으로 주제 추출
    """
    texts = [msg['message'] for msg in messages]

    vectorizer = TfidfVectorizer(max_features=1000)
    doc_term_matrix = vectorizer.fit_transform(texts)

    lda = LatentDirichletAllocation(n_components=n_topics, random_state=42)
    lda.fit(doc_term_matrix)

    # 각 토픽의 주요 단어
    feature_names = vectorizer.get_feature_names_out()
    topics = []

    for topic_idx, topic in enumerate(lda.components_):
        top_words = [feature_names[i] for i in topic.argsort()[-10:]]
        topics.append({
            'topic_id': topic_idx,
            'keywords': top_words
        })

    return topics
```

### 권장 아키텍처

```
┌──────────────────────────────────────────────────────────┐
│                    프론트엔드 (Optional)                    │
│  - 웹 대시보드: 채팅방 목록, 검색, 필터링                    │
│  - 마크다운 뷰어: 정리된 내용 표시                          │
├──────────────────────────────────────────────────────────┤
│                    백엔드 서비스                            │
│  - FastAPI/Flask: REST API                              │
│  - 파일 업로드 엔드포인트                                   │
│  - 비동기 처리: Celery/RQ                                  │
├──────────────────────────────────────────────────────────┤
│                    데이터 처리 레이어                        │
│  - 파서: ZIP 및 TXT 파싱                                   │
│  - NLP: 요약, 토픽 모델링                                  │
│  - 링크 크롤러: 메타데이터 추출                             │
├──────────────────────────────────────────────────────────┤
│                    데이터베이스                             │
│  - PostgreSQL: 메시지, 메타데이터                          │
│  - MongoDB: 원본 데이터                                    │
│  - Elasticsearch: 전문 검색                               │
├──────────────────────────────────────────────────────────┤
│                    스토리지                                 │
│  - S3/MinIO: 첨부파일, 마크다운 파일                        │
└──────────────────────────────────────────────────────────┘
```

---

## 법적 고려사항 및 제한사항

### ⚖️ 이용약관 및 법적 위험

#### 카카오톡 이용약관 위반 주의

> "DB 봇 사용은 카카오톡 이용약관을 위반합니다 (법률 위반은 아님)"

**위반 시 처벌:**
- ⚠️ 계정 영구 정지 가능
- ⚠️ 서비스 이용 제한
- ⚠️ 법적 책임 (악의적 사용 시)

#### 금지된 행위

| 행위 | 위험도 | 결과 |
|-----|-------|------|
| 공식 API 외 크롤링/스크래핑 | 🔴 높음 | 계정 정지 |
| 자동화 봇 (비승인) | 🔴 높음 | 영구 정지 |
| 대량 데이터 수집 | 🟡 중간 | 이용 제한 |
| GUI 자동화 (pyautogui 등) | 🟡 중간 | 감지 시 제재 |
| 내보내기 기능 사용 | 🟢 낮음 | 합법적 사용 |

### 🔒 개인정보 보호

#### GDPR 및 개인정보보호법 준수

오픈채팅 데이터를 처리할 때 반드시 고려해야 할 사항:

1. **동의 획득**
   - 채팅방 참여자 전원의 데이터 수집 동의 필요
   - 명시적 opt-in 방식 권장

2. **데이터 최소화**
   - 필요한 데이터만 수집 및 저장
   - 개인 식별 정보(PII) 익명화

3. **보안 조치**
   - 암호화 저장
   - 접근 제어
   - 정기적 보안 감사

4. **데이터 보유 기간**
   - 명확한 보유 기간 설정
   - 기간 만료 시 자동 삭제

#### 보안 사고 사례

> "[단독] 카톡 오픈채팅 해킹...1명당 단가 7000원·2시간 만에 '뚝딱'"
> - 2023년 3월, 전자신문

오픈채팅 사용자 데이터가 불법적으로 추출되고 거래된 사례가 있으므로, 데이터 보안에 특히 주의해야 합니다.

### ✅ 합법적 사용 가이드라인

#### 안전한 사용 방법

1. **공식 기능만 사용**
   ```
   ✅ 카카오톡 내장 "대화 내용 내보내기"
   ✅ 개인 용도의 데이터 정리
   ✅ 동의된 채팅방 데이터 처리
   ```

2. **금지 행위 피하기**
   ```
   ❌ 무단 스크래핑
   ❌ 대량 자동화
   ❌ 타인 동의 없는 데이터 수집
   ❌ 상업적 재판매
   ```

3. **윤리적 사용**
   - 개인 아카이빙 목적
   - 소규모 커뮤니티 정리
   - 참여자 동의 하의 데이터 분석

### 📋 체크리스트

프로젝트 시작 전 확인사항:

- [ ] 모든 참여자의 동의 획득
- [ ] 개인정보 처리 방침 수립
- [ ] 데이터 보안 조치 구현
- [ ] 이용약관 검토
- [ ] 상업적 목적 여부 확인
- [ ] 백업 및 복구 계획 수립
- [ ] 데이터 보유 기간 설정

---

## 📌 결론 및 추천 방안

### 현실적인 구현 전략

#### ⭐ 최선의 방안

**단계별 접근:**

1. **Phase 1: 수동 수집 + 자동 정리**
   - 카카오톡 내보내기 기능 활용
   - Python 스크립트로 마크다운 변환
   - 로컬 저장 및 개인 사용

2. **Phase 2: 반자동화**
   - 주기적 내보내기 루틴 설정
   - 클라우드 저장소 연동
   - 검색 기능 추가

3. **Phase 3: 고도화**
   - AI 기반 요약 및 분류
   - 웹 대시보드 구축
   - 팀 내 공유 기능

### 기술 스택 권장사항

```yaml
언어: Python 3.10+
프레임워크:
  - FastAPI: REST API
  - Celery: 비동기 작업
라이브러리:
  - beautifulsoup4: 링크 메타데이터
  - scikit-learn: 토픽 모델링
  - anthropic/openai: AI 요약
데이터베이스:
  - PostgreSQL: 구조화 데이터
  - Elasticsearch: 전문 검색
배포:
  - Docker: 컨테이너화
  - GitHub Actions: CI/CD
```

### 향후 개선 방향

1. **카카오 API 업데이트 모니터링**
   - 공식 오픈채팅 API 출시 가능성
   - 정기적으로 개발자 문서 확인

2. **AI 기능 강화**
   - RAG 기반 질의응답
   - 자동 태깅 및 분류
   - 감정 분석

3. **협업 기능**
   - 다중 사용자 지원
   - 권한 관리
   - 공유 및 내보내기

## 🔍 추가 의견 및 세부 고려사항

### 1. 합법·안전·안정성 범위 정의의 중요성

- **공식 API 부재**로 인해, 오픈채팅 “내용”을 완전 자동으로 수집하려면 반드시 비공식 기법(클라이언트 후킹, UI 자동화 등)에 의존해야 함.
- 비공식 방법은 **약관 위반 위험**과 **카카오 계정 제한** 가능성을 수반하므로, “어디까지 자동화할 것인지” 범위를 먼저 정의하는 것이 핵심.
- **운영자 본인 방, 참여자 고지 및 동의, 내부 분석 목적**이라는 조건을 명확히 해야 리스크를 최소화할 수 있음.

### 2. 현실적인 자동화 패턴: Export → n8n → 분석

- **데이터 확보:**
  - 정기적인 “대화 내보내기” 또는 PC 카카오톡 UI 매크로(예: AutoHotkey)로 텍스트 로그를 확보.
  - 매크로 방식은 기술적으로 가능하지만, 카카오 정책 변화나 클라이언트 업데이트 시 쉽게 깨질 수 있으므로 **주기적인 점검 및 보완**이 필요.
- **파이프라인:**
  - 내보낸 파일을 Google Drive / Dropbox / Email 등에 업로드 → n8n 트리거(Drive Trigger, IMAP Trigger 등)로 감지.
  - n8n Function/Code 노드에서 파싱, 정제, 구조화(JSON, 링크 목록, QA 후보 등) 후 OpenAI/Anthropic 노드로 요약·분석.
  - 결과물을 Notion, Google Sheets, 이메일, Slack 등으로 전달해 반복 업무를 최소화.

### 3. 시스템 설계 시 체크포인트

- **데이터 보존 정책:** 오픈채팅 첨부파일의 유효기간이 짧으므로, 로그 확보 시점에 필요한 파일을 즉시 백업하고 만료 전에 별도 스토리지로 이관.
- **보안 및 접근 제어:** n8n 워크플로에 포함되는 API 키·토큰을 Vault/Secret Manager에 저장하고, 워크플로 실행 로그에는 민감 정보를 남기지 않도록 설계.
- **에러 복구 프로세스:** UI 자동화가 실패했을 때 수동 절차로 대체할 수 있도록 **백업 플랜**(수동 내보내기, 로그 검증)을 마련.
- **감사·추적 가능성:** 참여자 문의나 규제 대응에 대비해, 언제 어떤 데이터를 수집·분석했는지 기록하는 메타데이터(타임스탬프, 담당자)를 유지.

### 4. 완전 자동·실시간 수집이 필요한 경우의 대안 검토

- 오픈채팅 대신 **KakaoTalk 채널(비즈니스)** 로 커뮤니케이션 구조를 전환하면, 공식 파트너(Sinch, Sendbird, respond.io 등)의 API/Webhook으로 메시지를 수신하여 n8n과 연동할 수 있음.
- 다만 채널 구조는 오픈채팅 커뮤니티와 운영 방식이 다르므로, **고객 응대/일방향 브로드캐스트 중심** 흐름에 맞게 참여자 경험을 재설계해야 함.
- 비공식 실시간 수집 도구는 기술적으로 n8n 앞단에 스크래퍼를 붙이면 구현 가능하나, **안정성과 법적 위험이 커서** 서비스/브랜드 관점에서는 신중한 판단이 필요.

### 5. 실행 전 사전 점검 체크리스트 (재확인)

- [ ] 참여자 동의 및 개인정보 처리 방침 공지 여부
- [ ] 로그 파일/첨부파일의 보관 기간과 삭제 정책 정의
- [ ] 자동화 도구(n8n, 매크로) 업데이트/장애 시 대응 계획 마련
- [ ] 보안(암호화, 접근 통제)과 비상 연락 체계 구축
- [ ] 카카오 정책 변경 모니터링 루틴 운영

### 참고 자료

**공식 문서:**
- [Kakao Developers](https://developers.kakao.com/)
- [KakaoTalk Customer Service](https://cs.kakao.com/)

**오픈소스:**
- [kakaotalk-analyzer (GitHub)](https://github.com/graup/kakaotalk-analyzer)
- [Kakao-Openchat-Search-API (GitHub)](https://github.com/parkkw472/Kakao-Openchat-Search-API)

**관련 기술:**
- [LangChain Documentation](https://python.langchain.com/)
- [Anthropic Claude API](https://docs.anthropic.com/)
- [BeautifulSoup Documentation](https://www.crummy.com/software/BeautifulSoup/bs4/doc/)

---

## 📞 추가 지원

이 보고서는 2025년 1월 기준 정보입니다. 카카오톡 정책 및 API는 변경될 수 있으므로, 프로젝트 시작 전 최신 정보를 확인하시기 바랍니다.

**질문이나 추가 정보가 필요하시면 말씀해 주세요!**

---

*생성 일시: 2025-01-10*
*버전: 1.0*
