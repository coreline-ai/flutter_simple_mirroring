# Flutter Simple Mirroring (RustDesk Client)

![Flutter](https://img.shields.io/badge/Flutter-3.0%2B-02569B?logo=flutter&logoColor=white)
![Dart](https://img.shields.io/badge/Dart-3.0%2B-0175C2?logo=dart&logoColor=white)
![Rust](https://img.shields.io/badge/Rust-Enabled-000000?logo=rust&logoColor=white)
![License](https://img.shields.io/badge/License-AGPL--3.0-red.svg)


이 프로젝트는 오픈 소스 원격 데스크톱 솔루션인 **[RustDesk](https://github.com/rustdesk/rustdesk)**의 클라이언트(`flutter_hbb`)를 기반으로 한 Flutter 애플리케이션입니다. Flutter의 유연한 UI와 Rust의 강력한 성능을 결합하여 고성능 원격 제어 경험을 제공합니다.

## 📚 프로젝트 개요

*   **패키지명**: `flutter_hbb`
*   **기반 기술**: Flutter (Frontend) + Rust (Backend Core)
*   **주요 기능**: 원격 데스크톱 제어, 파일 전송, 포트 포워딩, 채팅, 터미널 접속
*   **지원 플랫폼**: Windows, macOS, Linux, Android, iOS, Web

## 🏗 기술 아키텍처 (Technical Architecture)

이 프로젝트는 **하이브리드 아키텍처**와 **멀티 윈도우 시스템**을 핵심으로 합니다.

### 1. Hybrid Core (Flutter + Rust)
애플리케이션은 크게 두 계층으로 나뉩니다.
*   **Rust Layer**: 네트워킹(P2P/Relay), 비디오 코덱(Encoding/Decoding), 오디오 처리, 보안 등 퍼포먼스가 중요한 핵심 로직을 담당합니다.
*   **Flutter Layer**: 사용자 인터페이스(UI)를 담당하며, FFI(Foreign Function Interface)를 통해 Rust 코어와 통신합니다.

> **통신 방식 (Flutter Rust Bridge)**:
> `lib/generated_bridge.dart`를 통해 Dart에서 Rust 함수를 호출하고, Rust에서 발생한 이벤트(비디오 프레임 수신, 메시지 등)는 Dart Stream을 통해 비동기적으로 UI에 전달됩니다.

### 2. Multi-Window System (Desktop)
데스크톱 환경(Windows, macOS, Linux)에서는 다양한 작업을 독립적으로 수행하기 위해 **멀티 윈도우** 구조를 채택했습니다.
*   `desktop_multi_window` 패키지를 사용하여 각 세션을 격리된 프로세스/창으로 실행합니다.
*   **Main Window**: 앱 설정, 연결 대기, 주소록 관리.
*   **Session Windows**:
    *   🖥 **Remote Desktop**: 원격 화면 제어 (`DesktopRemoteScreen`)
    *   📂 **File Transfer**: 파일 탐색기 형 인터페이스 (`DesktopFileTransferScreen`)
    *   ⌨️ **Terminal**: 원격 터미널 접속 (`DesktopTerminalScreen`)

### 3. High-Performance Rendering
원격지에서 수신한 고해상도 비디오 프레임(YUV/RGB)을 지연 없이 그리기 위해 **GPU 가속 텍스처 렌더링**을 사용합니다.
*   `flutter_gpu_texture_renderer` / `texture_rgba_renderer`를 사용하여 CPU 오버헤드를 최소화합니다.

---

## 📂 프로젝트 구조 (Project Structure)

```bash
lib/
├── main.dart                # 앱 진입점 (Entry Point)
│                             # - 실행 인자(args)에 따라 메인 창 또는 세션 창 분기 처리
│                             # - Rust Bridge 및 환경 초기화 (initEnv)
├── generated_bridge.dart    # Rust FFI 바인딩 (Auto-generated)
│                             # - Rust 함수 호출 및 이벤트 리스너 정의
├── models/                  # 상태 관리 모델 (Provider)
│   ├── state_model.dart     # 전역 상태
│   ├── platform_model.dart  # 플랫폼별 기능
│   └── ...
├── desktop/                 # 데스크톱 전용 UI
│   ├── pages/               # 탭 페이지, 서버 페이지 등
│   └── screen/              # 실제 원격 제어 화면, 파일 전송 화면 등
├── mobile/                  # 모바일 전용 UI
└── common/                  # 공통 위젯 및 유틸리티
```

## 🚀 시작하기 (Getting Started)

### 필수 조건 (Prerequisites)
*   [Flutter SDK](https://flutter.dev/docs/get-started/install) (3.1.0 이상 권장)
*   [Rust](https://www.rust-lang.org/tools/install) (Core 라이브러리 빌드용)

### 설치 및 실행

1.  **리포지토리 클론**
    ```bash
    git clone https://github.com/HwanChoi/flutter_simple_mirroring.git
    cd flutter_simple_mirroring
    ```

2.  **종속성 설치**
    ```bash
    flutter pub get
    ```

3.  **애플리케이션 실행**
    *   **Desktop (macOS)**:
        ```bash
        flutter run -d macos
        ```
    *   **Mobile (Android/iOS)**:
        ```bash
        flutter run -d android  # 또는 ios
        ```

## 🤝 기여 (Contributing)
이 프로젝트는 RustDesk의 오픈 소스 정신을 따릅니다. 버그 리포트, 기능 제안, PR은 언제나 환영합니다.

## 📜 라이선스 (License)
**GNU Affero General Public License v3 (AGPL-3.0)**
상세 내용은 `LICENSE` 파일을 확인하세요.
