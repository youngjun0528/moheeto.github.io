# 03. Resource Performance

## 최적화 항목

> - 이미지 사이즈 최적화
> - 폰트 최적화
> - 캐시 최적화
> - 불필요한 CSS 제거

## Tools

### Coverage panel (Google Chrome)

웹 페이지 랜더링하는 과정에서 어떤 코드가 실행되었는지 확인  
각 파일의 코드가 얼마나 실행되었는지 비율 확인

### Squoosh

이미지 압축, 포멧 변경 Tool  
https://squoosh.app/

### PurgeCSS

사용하지 않는 CSS 제거 Tool  
https://purgecss.com/

### Tailwind CSS

Utility-First CSS Framework  
미리 특정 CSS 속성에 대해 Class 를 생성하고 사용하는 객체 지향적 CSS

## Optimization

### 이미지 지연 로딩

- 가장 먼저 뷰 포트에 보여지는 이미지 혹은 동영상을 먼저 로드되도록 하고, 이후 스크롤 하면 나머지 이미지나 동영상을 다운로드 하도록 지정
- Intersection Observer
    - 웹페이지의 특정 요소를 Observe 하면서 페이지 스크롤시 각 요소가 화면에 들어왔을 때, 함수를 호출
  ```Javascript
    const imgRef = useRef(null)
    
    useEffect(() => {
        const options = {}
        const callback = (entries, observer) => {
            // console.log('Entries', entries)
            entries.forEach(entry => {
                if(entry.isIntersecting) {
                    console.log('Is intersecting', entry.target.dataset.src)
                    entry.target.src = entry.target.dataset.src
                    observer.unobserve(entry.target)
                }
            })
        }
    
        const observer = new IntersectionObserver(callback, options)
    
        observer.observe(imgRef.current)
    
        return () => observer.disconnect()
	  }, [])
    ```

### 이미지 사이즈 최적화

#### 이미지 Format

- PNG
    - 무손실 압축 방식
    - 배경이 투명한 이미지
- JPG
    - 손실 압축
    - 기존 이미지 보다 작은 사이즈로 변환 가능
- WebP
    - 무손실 압축과 손실 압축을 모두 지원하는 최신 이미지 포멧
    - PNG 대비 26%, JPG 대비 25~34% 압축 효율을 가지고 있음
    - https://developers.google.com/speed/webp?hl=ko
    - 호환성 문제로 img 태그 보다는 picture 태그의 source 를 사용하기를 권장
        - 소스내 이미지를 찾지 못하면 img 태그내 이미지를 로딩
          ```Javascript
              <picture>
                  <source media="(min-width:650px)" srcset="img_pink_flowers.jpg" />
                  <source media="(min-width:465px)" srcset="img_white_flowers.jpg" />
                  <img src="img_orange_flowers.jpg" alt="Flowers" style="width:auto;" />
              </picture>
            
              <picture>
                  <source srcset="photo.avif" type="image/avif" />
                  <source srcset="photo.webp" type="image/webp" />
                  <img src="photo.jpg" alt="photo" />
              </picture>
          ```

- 사이즈 : PNG > JPG > WebP
- 화질 : PNG == WebP > JPG
- 호환성 : PNG == JPG > WebP

### 동영상 압축

- 동영상 압축은 화질을 맞추는 작업으로 동영상이 메인 콘텐츠라면 추천하지 않으며,
- 단순 정보 제공 혹은 백그라운드 디자인에 활용되는 동영상 콘텐츠를 보여주는 작업에 추천한다.
- 호환성 문제로 video 태그의 source 를 사용하기를 권장
    ```Javascript
      <video>
          <source src={video_webm} type="video/webm" />
          <source src={video} type="video/mp4" />
      </video>
    ```
- Tips. 웹 디자인 배경 목적의 동영상이라면 화질 저하 발생시 필터 적용

### 폰트 최적화

- 텍스트가 보여지는 시점과 폰트가 다운로드 되는 시점이 달라지면 기본 텍스트 폰트에서 다운로드 적용된 폰트로 변화

#### FOUT, FOIT

- FOUT(Flash of Unstyled Text)
    - Edge 브라우저에서 폰트를 로드하는 방식으로 폰트 다운로드와 상관없이 먼저 텍스트를 보여주고 폰트 다운로드 완료 이후 텍스트에 폰트를 적용
- FOIT(Flash of Invisible Text)
    - 크롬, 사파리 등에서 폰트를 로드하는 방식으로 폰트가 일정 기간(3초 동안) 완전히 다운로드 될 때까지 텍스트를 보여주지 않음.
    - 만약 3초 이후에도 폰트가 로드 되지 않으면 기본 텍스트를 보여줌.

#### 최적화 방식

- 폰트 적용 시점 제어
    - CSS의 font-display 속성 이용
        - ```css
          @font-face{
            font-family: Arial;
            src: url("./asset/....");
            font-display: auto;
          }
          ```
        - auto : 브라우저 기본값
        - block : FOIT (timeout == 3s)
            - 이 경우 갑자기 폰트가 나라나므로 font fade-in/out 효과를 적용하면 좋다.
        - swap : FOUT
        - fallback : FOIT (timeout == 0.1s) 3초 이후에도 블러오지 못한 경우 기본 폰트로 유지, 이후 캐시
        - optional : FOIT (timeout == 0.1s) 이후 네트워크 상황에 따라 기본 폰트로 유지 할지 결정, 이후 캐시
    - fontfaceobserver : Font Download 시점을 알아내기 위한 라이브러리
- 폰트 파일 크기 최적화
    - 폰트 포멧 변경
        - TFT : 파일 크기가 큰 폰트로 주로 운영체제에서 사용된다.
        - WOFF : Web Open Font Format
        - 폰트크기 : EOT > TTF/OTF > WOFF > WOFF2
    - 서브셋 폰트 사용
        - 일부 문자의 포멧만 가지고 있는 폰트
    - Data-URI 형태의 폰트로 CSS 에 포함
        - 폰트 파일을 데이터 스트리밍 형태로 변경

### 캐시 최적화

- 캐시
    - 자주 사용되는 데이터나 값을 미리 복사해둔 임시 저장 공간 또는 저장하는 동작
    - 메모리 캐시 : 메모리에 저장하는 방식
    - 디스크 캐시 : 파일 형태로 디스크에 저장하는 방식
    - Cache-Control 이라는 응답 헤더의 값에 따라 알고리즘 작동 방식이 달라짐.
        - no-cache : 캐시를 사용하기 전 서버에 검사 후 사용
        - no-store : 캐시 사용 안함
        - public : 모든 환경에서 캐시 사용 가능
        - private : 브라우저 환경에서만 캐시 사용, 외부 캐시 서버 사용 불가능
        - max-age : 캐시 유효 시간
        - ex) Cache-Control: public, max-age=600
            - 브라우저 환경에서만 600초(1분) 동안 캐시를 사용
    - 적절한 캐시 유효 시간 설정
        - HTML : no-cache, 캐시를 사용하지 않고 계속 로드하면 그 안의 JS와 CSS도 항상 로드
            - 즉, JS/CSS 가 변경이 없다면 그대로 사용 유지
        - JS/CSS/IMG : public
            - 캐시를 사용하여도 무방
        - 나머지 소스들은 캐시 사용 안함.

![performance-08.png](performance-08.png)

### 불필요한 CSS 제거

- PurgeCSS
    - 파일에 들어있는 모든 CSS 키워드를 추출하여 불필요한 CSS 파일을 지우는 방식

![performance-09.png](performance-09.png)

![performance-10.png](performance-10.png)

