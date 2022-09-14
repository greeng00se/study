### Route53에 호스트 영역 등록
- ALB에서 사용하기 위한 인증서 발급을 위해 Route 53에 호스트 영역을 등록 해줍니다.
- 기존에 namecheap에서 도메인을 구매했기 때문에 namecheap에서 NS 레코드를 등록해줬습니다.

<img width="1034" alt="image" src="https://user-images.githubusercontent.com/58586537/190102092-b5cf1d1f-b052-441a-b8ce-c5fde069c707.png">

- 아래 4개의 레코드가 Route 53에서 자동 생성한 레코드입니다.
- Route 53의 처음 25개 호스팅 영역의 경우 호스팅 영역당 한 달에 0.50 USD 의 비용이 발생합니다.

### ACM에서 퍼블릭 인증서 요청
<img width="1112" alt="image" src="https://user-images.githubusercontent.com/58586537/190102979-48d97c29-1d9c-4839-a997-986d5eb606ac.png">

- 인증서 발급 요청은 퍼블릭 인증서의 경우 무료로 발급받을 수 있습니다.
- A 레코드를 따로 사용할 경우 앞에 *을 붙여서 정규화된 도메인명으로 등록합니다.
- 호스트가 없는 도메인을 사용하는 경우 따로 등록해야 합니다.
- 도메인 검증과 이메일 검증이 있는데 도메인 검증을 하는 것을 권장합니다. 

### Route 53에서 레코드 생성
<img width="1177" alt="image" src="https://user-images.githubusercontent.com/58586537/190106963-3341660f-a6db-4f7d-a59b-c50c79cde891.png">

- 도메인 검증을 하기 위해서는 Route 53에 등록한 호스트 영역에 CNAME 레코드를 등록해야하는데 인증서 페이지에서 편하게 등록이 가능합니다. 
<img width="1194" alt="image" src="https://user-images.githubusercontent.com/58586537/190107334-c0215a59-5da5-4230-a0e6-2c8babda391d.png">

### Beanstalk ALB 리스너 & 프로세스추가
<img width="933" alt="image" src="https://user-images.githubusercontent.com/58586537/190142682-f3c877d2-b2ce-49f4-9004-d10d6ba9a475.png">

- 이전에 발급받은 인증서를 이용하여 Application Load Balancer에 리스너를 추가합니다.
- 로드밸런서가 프로세스의 상태 검사를 할 수 있도록 상태 검사 경로도 https로 설정해줍니다.
- 추가로 아래 링크의 설정을 통해 로드밸런서에서 백그라운드 프로세스간 암호화도 할 수 있습니다.
    - https://aws.amazon.com/ko/premiumsupport/knowledge-center/elastic-beanstalk-https-configuration/

### Route 53에서 레코드 생성
<img width="1107" alt="image" src="https://user-images.githubusercontent.com/58586537/190146184-91a73714-4b05-446a-b570-ddc8b5702652.png">

- 마지막으로 Route 53에서 Elastic Beanstalk 환경에 대한 A 레코드를 생성해주면 정상적으로 https 통신을 할 수 있습니다.

<img width="559" alt="image" src="https://user-images.githubusercontent.com/58586537/190146444-bdd00eab-dd8c-4c49-96b5-d39dde734c8f.png">


### 참고 자료
- https://aws.amazon.com/ko/route53/pricing/
