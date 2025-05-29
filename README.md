# 프론트엔드 배포 파이프라인

1. **GitHub 저장소**
   - 메인 브랜치에 코드 푸시 시 워크플로우 트리거
   - GitHub Actions 실행 환경 구성

2. **GitHub Actions**
   - Ubuntu 최신 버전에서 실행
   - 워크플로우 단계별 실행 관리
   - 빌드 및 배포 자동화

3. **AWS 서비스**
   - S3: 정적 파일 호스팅
   - CloudFront: CDN 및 캐시 관리
   - IAM: 보안 자격 증명 관리

4. **최종 사용자**
   - CloudFront를 통한 전세계 엣지 로케이션 접근
   - 최적화된 콘텐츠 전송
   - 낮은 지연 시간 


## 주요 링크

- S3 버킷 웹사이트 엔드포인트: http://hanghae-fe.s3-website-us-east-1.amazonaws.com
- CloudFrount 배포 도메인 이름: https://d3eriwk5ukh1q.cloudfront.net

## 주요 개념

- GitHub Actions과 CI/CD 도구: Continuous Integration / Continuous Deployment
	- GitHub Actions는 GitHub에서 제공하는 CI/CD 자동화 도구입니다.
	- 코드를 Push할 때 자동으로 테스트, 빌드, 배포 등의 워크플로우를 실행합니다.
	- CI는 코드 변경 시 자동 테스트와 통합, CD는 변경된 코드를 자동 배포하는 개념입니다.

- S3와 스토리지: 정적 파일 호스팅용 객체 스토리지
  - Amazon S3(Simple Storage Service)는 AWS에서 제공하는 객체 기반 스토리지 서비스입니다.
  - 이미지, HTML, JS, CSS 등 정적 파일을 저장하고 정적 웹 사이트 호스팅도 가능합니다.
  - 높은 내구성과 가용성을 제공하며, 퍼블릭 접근이 가능하도록 설정할 수 있습니다.

- CloudFront와 CDN: 콘텐츠 전송 네트워크
  - Amazon CloudFront는 AWS의 글로벌 CDN(Content Delivery Network) 서비스입니다.
  - S3에 있는 정적 리소스를 전 세계 엣지 로케이션에 캐시하여 빠르게 전달합니다.
  - 지연 시간 감소, 트래픽 분산, 보안 향상 등의 이점이 있습니다.

- 캐시 무효화(Cache Invalidation): 업데이트된 콘텐츠 반영
  - CDN에 캐시된 리소스를 삭제하여 최신 버전의 파일로 강제로 갱신하는 과정입니다.
  - CloudFront는 기본적으로 오래된 콘텐츠를 자동으로 갱신하지 않기 때문에, 수동으로 Invalidation 요청을 보내야 할 수 있습니다.
  - 예: /index.html을 배포했는데 캐시된 오래된 파일이 계속 보일 경우 무효화 필요.

- Repository secret과 환경변수: 민감한 설정 정보 관리
  - GitHub Actions에서 Secrets는 API 키, 토큰, 배포 키 등 민감한 정보를 암호화하여 저장하는 기능입니다.
  - 워크플로우에서 ${{ secrets.MY_SECRET }}과 같이 접근합니다.
  - 환경변수(environment variables)는 빌드나 배포 중에 필요한 설정값들을 워크플로우에서 직접 정의하거나 .env 파일로 관리합니다.

## CDN과 성능최적화

- CloudFront는 AWS의 CDN 서비스로, 전 세계 여러 엣지 로케이션에 캐시를 두고 콘텐츠를 사용자 가까이에서 제공함으로써 성능을 최적화합니다.

- 성능 비교 (스크린샷 기반)

  - 📁 CloudFront 사용 (1번째 이미지 기준)
    - 총 전송 용량: 199kB
    - 총 로딩 시간: 125ms
    - 주요 리소스의 로딩 시간: 대체로 13~19ms 수준의 응답 시간 기록
  - 📁 S3 직접 접근 (2번째 이미지 기준)
    - 총 전송 용량: 447kB
    - 총 로딩 시간: 1.09s
    - 주요 리소스 로딩 시간: 다수가 200ms 이상 소요됨 (최대 462ms)
  - 전송 용량: S3(447kB) 대비 CloudFront(199kB)는 약 55.5% 감소
  - 로딩 시간: S3(1.09s) 대비 CloudFront(125ms)는 약 88.5% 감소

- 정적 리소스 제공에는 CloudFront를 활용한 CDN 구성 방식이 성능 및 확장성 측면에서 명확히 유리합니다.
- 특히 글로벌 사용자 대응, 고빈도 리소스 접근, 모바일 환경 대응이 필요한 경우 CDN 사용은 필수에 가깝습니다.
