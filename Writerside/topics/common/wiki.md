# Wiki

## 위키 엔진

### Confluence

- https://www.atlassian.com/software/confluence
- 유료, Free 플랜이 존재(Cloud)
- 2024년 Confluence Server 버전이 종료
- 부동의 업계 1위
- Blog / Wiki 로서의 기능보다는 협업 Tool 로서의 기능이 제공됨.
- 백업은 Atlassian 에서 제공 - 다른 곳으로의 백업은 불가능.
- Markdown Edit 불가능
- 미사용 시간이 길면, 자동으로 계정이 일시중지 되는 것으로 보임.

### Wiki.js

- https://js.wiki/
- Confluence 의 대체
- 현재 Version 2, Version 3 를 3년째 준비 중
- OpenSource 이고, 개발 진척도가 늦어서 버그가 있음.
- Markdown 에디터 제공
- 자체 Draw.io 다이어그램 툴 제공
- GitLab 또는 GitHub 외 다양한 백업 방식 제공
- 별도의 서버 및 도메인 필요
- 협업 뿐만 아니라 Blog 용으로 대체 가능

### GitHub / GitLab Pages with. Jekyll

- https://jekyllrb-ko.github.io/
- GitHub / GitLab 의 자체 호스팅 기능으로 개인 Blog 생성 가능
- Content 는 Markdown 이 기본이고 Liquid 추가
- 다양한 Layout
- Adsense 및 댓글 기능 추가 가능
- Customizing 범위가 넓음.
- HTML/CSS 의 기본 지식이 필요 ( 기본적인 프로그래밍 지식이 있어서 원할하게 페이지 구성이 가능함. )
- Menu 및 Tree 구성에 대한 복잡도 증가

### WriteSide of Jetbrains

- https://www.jetbrains.com/ko-kr/writerside/
- (2023.11.28 기준) EAP 버전, 향후 계속 무료 버전 사용 가능
- GitHub / GitLab 의 자체 호스팅 기능 또는 자체 서버를 통해서 개인 Blog 생성 가능
- 본래 목적은 API Documents 생성
- Content 는 Markdown 이 기본이고 ( WriteSide 에서 미리 정의한 ) XML Tag 추가
- 일부 색상 테마 등 Customizing 가능
- Adsense 및 댓글 기능 추가 불가능
- WriteSide 에디터를 통해서 손쉽게 문서 작성 가능

## Conclusion

<procedure>
    <step>
    초기에는 Wiki.js 를 사용하여 Google Cloud (GCP) 를 통해 서버를 구성하고, 무료 도메인으로 페이지를 구성
    <p>- 자체 서버 운영 및 도메인 관리의 어려움, 버그 등으로 인해서 종료, Ver 3. 릴리즈 되면 다시 시도할 예정</p>
    </step>
    <step>
    GitHub Jekyll 을 사용하여 GitHub 의 자체 호스팅 기능 사용
    <p>- Blog 를 작성하는 것인지, Coding 을 하는 것인지...</p>
    </step>
    <step>
    목적은, 블로그는 간략히 쉽게 작성되어야 한다. Cloud / Online 이 아닌 Offline 에서 작성이 가능해야 한다.
    <p>- 위 기준에 따라서 WriteSide of Jetbrains 를 한번 시도</p>
    <p>- 어차피 수익은 안날거 같고, 다른 사람의 의견 필요없이 개인 블로그이니, Adsense 및 댓글 기능 없애는 것으로..</p>
    </step>
</procedure>
 