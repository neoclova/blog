---
title: "How Can MySQL Catch Up with PostgreSQL’s Momentum?"
date: 2025-04-05 18:00:00 +0900
categories: [DBMS, MySQL]
tags: [Open source, DBMS, MySQL, MariaDB, PostgreSQL]
image:
  path: /assets/img/posts_2025/How_Can_MySQL_Catch_Up_with_PostgreSQL’s_Momentum-thumbnail.jpg
  lqip: data:image/webp;base64,UklGRvYAAABXRUJQVlA4IOoAAADQBQCdASoUABQAPm0ukkYkIqGhKA1QgA2JQBOmUGdDLVSvA97ww5fMTrnNRFfNyZk8WevW7eCAAP7+Jc5XELECMSii3v4JAZtmOiJCiH8sZrfh+f2CrlZjg8+Yyzhptvfys6MFNagen8KJ5zvPZlviEQ67nG1l+dydspffLDQLkf9dOQwMxYatf9jFuRbKnp6AAfnx1QjSKRLKOktPCEghS8sbeKPB+R+Ud0ZVuMvu7f/ycEh0qqfL69yEB
  alt: MySQL
---

> 원문 : <a href="https://www.percona.com/blog/how-can-mysql-catch-up-with-postgresqls-momentum/" target="_blank">How Can MySQL Catch Up with PostgreSQL’s Momentum?</a> (November 1, 2024)
{: .prompt-info }

MySQL 커뮤니티의 오래된 분들과 이야기를 나누다 보면 이런 질문을 자주 듣습니다. "DB-Engines 의 평가에 따르면 MySQL 은 여전히 PostgreSQL 보다 인기가 많고 훌륭한데, 왜 PostgreSQL 의 인기는 계속해서 상승하고 있고 MySQL 은 점차 인기를 잃어가고 있는 걸까?" 이러한 추세를 반전시키기 위해 MySQL 생태계에서 할 수 있는 일이 있을까요? 살펴봅시다!

![DB-Engines Ranking](/assets/img/posts_2025/How_Can_MySQL_Catch_Up_with_PostgreSQL’s_Momentum.png){:border-radius="8px"}{: .rounded-img }

PostgreSQL 의 강세와 MySQL 의 하락세를 설명하는 이유는 다음과 같이 요약할 수 있을 것 같습니다. Ownership 과 governance, license, community, architecture 그리고 오픈 소스 제품의 성장 동력이라고 생각합니다.


## Ownership and governance
MySQL 은 PostgreSQL 처럼 "community driven(커뮤니티 중심)"이었던 적이 없습니다. MySQL 이 스웨덴의 작은 회사 MySQL AB 가 소유하고 있었고, BDFL(Benevolent Dictator for Life)인 Michael "Monty" Widenious 가 책임자였을 때는 커뮤니티의 신뢰를 많이 받았으며, 무엇보다도 대기업에서 특별히 위협적인 존재로 여겨지지 않았습니다. 지금은 상황이 다릅니다. Oracle 이 MySQL 을 소유하고 있으며, 특히 클라우드 벤더들을 비롯한 업계의 많은 대기업들은 Oracle 을 경쟁자로 보고 있습니다. 당연히 경쟁사를 위해 코드나 마케팅에 기여할 이유가 별로 없으며, MySQL 상표를 소유한 Oracle 이 항상 우선권을 가지게 됩니다.

반면 PostgreSQL 은 커뮤니티에 의해 운영되며, 상업적 벤더들이 모두 동등한 위치에 있습니다. 예를 들어 EDB 같은 큰 회사도 PostgreSQL 생태계의 소규모 기업들과 특별한 우선권 없이 경쟁합니다.

이러한 구조 덕분에 대기업들은 PostgreSQL 에 기여하고 이를 우선적으로 추천하는 것을 선호하게 됩니다. 이는 경쟁사의 가치를 높이지 않으면서 PostgreSQL 프로젝트 방향에 더 큰 영향을 미칠 수 있기 때문입니다. 또한 수백 개의 소규모 기업들이 로컬 커뮤니티와 마케팅을 통해 PostgreSQL 을 전 세계적으로 확산시키고 있습니다.

<b>그렇다면 MySQL 커뮤니티는 이를 해결하기 위해 무엇을 할 수 있을까요?</b> 사실 MySQL 커뮤니티가 할 수 있는 일은 거의 없습니다. 이 문제는 Oracle 의 손에 달려 있습니다. 제가 <a href="https://www.percona.com/blog/can-oracle-save-mysql/" target="_blank">"Oracle 이 MySQL 을 구할 수 있을까?"</a> 라는 글에서 언급한 것처럼, MySQL 을 리눅스나 쿠버네티스와 같은 중립적인 재단 아래에 두는 것이 PostgreSQL 과 경쟁할 수 있는 기회를 제공할 수 있을 것입니다. 하지만 Oracle 이 현재로서는 MySQL  의 채택을 늘리기보다는 수익화에 더 관심이 있는 것으로 보이기 때문에 큰 기대는 하지 않고 있습니다.


## License
MySQL 은 GPLv2 와 Oracle 에서 구매할 수 있는 상업용 라이선스의 이중 라이선스 방식을 사용합니다. 반면 PostgreSQL 은 매우 자유로운 PostgreSQL 라이선스를 따릅니다.

차이점은, 상업용 라이선스가 있는 <a href="https://wiki.postgresql.org/wiki/PostgreSQL_derived_databases" target="_blank">PostgreSQL-derived forks</a> 버전을 쉽게 만들거나 상업용 라이선스가 있는 프로젝트에 포함시킬 수 있습니다. 물론 이러한 제품을 만드는 사람들은 PostgreSQL 을 지원하고 홍보하고 있습니다.

MySQL 역시 클라우드 벤더들이 상업용 fork 버전을 만들 수 있도록 허용하며, Amazon Aurora 의 MySQL 호환 버전이 그중 가장 잘 알려진 성공 사례입니다. 하지만 소프트웨어 배포 단계에서는 MySQL 의 제약이 작용합니다.

<b>MySQL 커뮤니티가 할 수 있는 일은 무엇일까요?</b> 다시 말하지만, 할 수 있는 일이 거의 없습니다. MySQL 을 더 자유로운 라이선스로 변경할 수 있는 유일한 회사는 Oracle 이며, Oracle 이 그들의 통제력을 줄이고 싶어 할 이유는 거의 없습니다. 허용적인 라이선스를 가지는 "core" 소프트웨어가 "open core" 나 "cloud only" 버전과 잘 맞아떨어지는 경우가 종종 있지만, Oracle 이 이러한 방향으로 MySQL 을 바꿀 가능성은 낮다고 봅니다.


## Community
오픈 소스 커뮤니티를 생각할 때, <a href="https://peterzaitsev.com/there-are-three-open-source-communities-not-just-one/" target="_blank">세 개의 커뮤니티</a>를 생각하는 것이 좋다고 생각합니다.

첫 번째로,
<b>"user community"</b> 에서 MySQL 은 여전히 좋은 성과를 내고 있지만, 새로운 애플리케이션에서는 PostgreSQL 이 점점 더 선호되고 있습니다. 다만 user community 는 다른 커뮤니티들의 노력의 결과로 형성되는 경우가 많습니다.

두 번째로,
<b>"Contributor community"</b> 는 PostgreSQL 쪽이 더 강세를 보입니다. PostgreSQL 은 여러 조직이 주도하기 때문에 Contributor community 가 자연스럽게 활성화되었습니다. 기여자에 초점을 맞춘 행사들이 열리고, PostgreSQL 에 기여하는 방법에 대한 책도 출판되고 있습니다. 또한 PostgreSQL 의 확장 아키텍처는 PostgreSQL 을 손쉽게 확장하고, 자신의 작업을 공개적으로 공유할 수 있도록 합니다.

세 번째로,
<b>"Vendor community"</b> 가 있는데, 여기에 MySQL 의 주요 문제가 있습니다. 많은 기업들이 MySQL 을 홍보하는 데 큰 관심이 없습니다. 홍보한다 해도 Oracle 의 가치를 높이는 일에 그칠 수 있기 때문입니다. 그렇다면 Oracle 의 파트너들이 나서서 MySQL 을 홍보하지 않겠느냐고 생각할 수도 있지만, 일부 파트너가 지원하는 MySQL 행사가 세계 곳곳에서 열리긴 해도 PostgreSQL 에 대한 벤더들의 지원에 비할 바는 아닙니다. PostgreSQL 은 많은 벤더들이 "자신의 프로젝트" 로 여기기 때문입니다.

<b>그렇다면 MySQL 커뮤니티가 할 수 있는 일은 무엇일까요?</b> 이번 경우에는 커뮤니티의 손을 완전히 벗어났다고 보지 않습니다. 다른 분야에서는 현재 상황이 더 어렵고 보상이 적을 수 있지만, 커뮤니티가 여전히 할 수 있는 일은 많습니다. MySQL 의 미래를 위해 노력하고 싶다면, 다양한 행사에 참여하고 이를 조직하는 것을 추천합니다. 특히 MySQL 생태계를 넘어서는 영역에서 활동하며, 기사 작성, 영상 촬영, 책 출판 등을 통해 MySQL 을 널리 알릴 수 있습니다.이를 소셜 미디어에 홍보하고 Hacker News 에도 제출해보세요.

특히, <a href="https://www.mysqlandfriends.eu/" target="_blank">FOSDEM 2025 MySQL Devroom</a> 의 논문 모집을 놓치지 마세요!

또한, 이 부분에서 Oracle 도 수익성을 줄이지 않으면서 기여할 여지가 있습니다. 잠재적인 기여자들을 대상으로 하는 이벤트를 마련하고, 그들과 계획을 공유하며 그들의 기여 노력을 지원하는 것입니다. 적어도 Oracle 의 "MySQL community" 로드맵과 맞는 방향으로 이런 기회를 제공하는 것은 도움이 될 것입니다.


## Architecture
PostgreSQL 진영에서는 PostgreSQL 의 인기가 높은 이유를 더 나은 아키텍처와 깔끔한 코드베이스에서 찾는 사람들이 있습니다. 이것이 중요한 요소일 수는 있지만, 주요 이유는 아니라고 생각합니다. 그럼에도 논의할 만한 가치가 있습니다.

PostgreSQL 은 확장성을 염두에 두고 설계되어 있으며, 이를 통해 수많은 강력한 확장 기능들이 구현되어 있습니다. 반면 MySQL 은 확장 가능성이 제한적입니다. 예외적으로 스토리지 엔진 인터페이스가 있는데, MySQL 은 다양한 스토리지 엔진을 지원하는 반면, PostgreSQL 은 하나의 스토리지 엔진만 제공합니다. (<a href="https://neon.tech/" target="_blank">Neon</a> 이나 <a href="https://www.orioledb.com/" target="_blank">OrioleDB</a> 와 같은 fork 버전은 패치를 통해 변경하기도 합니다).

<b>그렇다면 MySQL 커뮤니티가 할 수 있는 일은 무엇일까요?</b> 이러한 확장성 덕분에 PostgreSQL 에서는 핵심 서버에 포함시키지 않고도 기여자 커뮤니티가 다양한 혁신을 시도하기 훨씬 쉬워집니다. 그렇다면 MySQL 커뮤니티는 무엇을 할 수 있을까요? MySQL 의 확장성이 제한적이기는 하지만, MySQL 에서 이미 지원하는 <a href="https://dev.mysql.com/doc/extending-mysql/8.0/en/plugin-types.html" target="_blank">다양한 plugin</a> 과 <a href="https://dev.mysql.com/doc/refman/8.4/en/components.html" target="_blank">"components"</a> 를 통해 할 수 있는 일이 많습니다. 먼저 필요한 것은 MySQL 용 plugin 들이 모이는 "community marketplace" 입니다. 이를 통해 개발자들이 더 많은 plugin 을 만들도록 장려하고, 쉽게 노출될 수 있도록 해야 합니다. 또 하나는 Oracle 의 지원이 필요합니다. MySQL plugin 아키텍처를 확장하는 데 대한 Oracle 의 약속이 있다면 개발자들이 더 강력한 plugin 을 만들 수 있을 것입니다. 이는 일부 Oracle 제품과 경쟁이 생길 수도 있겠지만, MySQL 이 커스터마이징 가능한 데이터 유형과 pluggable 인덱스를 지원하는 plugin 이 있었다면, MySQL 용 PNG 벡터 대안과 같은 혁신도 이미 나왔을 것입니다.


## Open source product momentum
데이터베이스를 선택하는 것은 장기적인 투자와 같습니다. 데이터베이스를 변경하는 일이 쉽지 않기 때문에, 과거 Oracle 을 선택했다가 현재 그 선택에 묶여 있는 사람들에게 물어보면 잘 알 수 있습니다. 이는 데이터베이스를 선택할 때 미래를 고려해야 함을 의미합니다. 지금 고려 중인 데이터베이스가 향후에도 존재할지, 그리고 시간이 지나면서 미래 기술 요구 사항에 맞춰 발전할지도 생각해봐야 합니다.

제가 <a href="https://www.percona.com/blog/is-oracle-finally-killing-mysql/" target="_blank">"Oracle 이 드디어 MySQL을 버리는가?"</a> 라는 글에서 언급했듯이, Oracle 은 개발 초점을 상당 부분 독점적이고 클라우드 전용 MySQL 버전으로 옮긴 듯합니다. MySQL Community 는 사실상 방치된 상태입니다. 오늘날 MySQL 은 여전히 많은 애플리케이션에 훌륭하지만, 발전 속도는 뒤처지고 있고, MySQL 커뮤니티 내에서도 MySQL 이 미래에도 적합할지 의문을 품는 사람들이 많습니다.

<b>그렇다면 MySQL 커뮤니티가 할 수 있는 일은 무엇일까요?</b> 여기에서도 주도권은 거의 Oracle 에 있습니다. Oracle 이 공식 MySQL 로드맵을 단독으로 결정하기 때문입니다. "그렇다면 <a href="https://www.percona.com/mysql/software" target="_blank">Percona Server for MySQL</a> 은 어떤가?" 라고 질문할 수도 있습니다. Percona 에서는 Oracle 의 MySQL 에 대한 오픈 소스 대안을 제공하기 위해 노력하고 있지만, 완전한 호환성을 유지하기 위해 변경 사항을 신중하게 고려해야 합니다. 호환성을 손상시키거나 업스트림과의 병합 비용을 높일 수 있는 변경은 피해야 하기 때문입니다. 반면, <a href="https://mariadb.org/" target="_blank">MariaDB</a> 는 다소 다른 선택을 하고 있습니다. 제약 없는 혁신을 추구하며 MySQL 과의 호환성은 줄어들고, 새 버전이 나올 때마다 MySQL 과의 차이가 점점 더 커지고 있습니다.


## MariaDB
MariaDB 에 대해 언급했으니, "MariaDB 가 이미 이러한 문제를 어느 정도 해결한 것이 아닌가?" 라고 생각할 수 있습니다. 하지만 그렇게 단순하지는 않습니다. 제 생각에 MariaDB 는 <a href="https://www.percona.com/blog/open-source-and-flawed-foundations/" target="_blank">몇 가지 결함</a>을 가진 재단입니다. 모든 지적 재산권(IP), 특히 상표권을 보유하지 않기 때문에 모든 벤더들에게 공정한 환경을 제공하지 못합니다. "MariaDB" 와 관련된 모든 것을 제공할 수 있는 회사는 여전히 하나뿐이며, 이로 인해 상표 독점 문제가 존재합니다.

하지만 MariaDB 에는 기회의 창이 있을 수도 있습니다. <a href="https://k1.com/k1-acquires-mariadb/" target="_blank">K1 이 MariaDB 를 인수</a>하면서 PostgreSQL 에 가까운 governance 와 상표 소유 구조로 변화할 기회가 생겼습니다. 다만, 상표 IP 에 대한 통제를 줄이는 것은 사모펀드 회사가 흔히 하는 일이 아니기 때문에 큰 기대는 하지 않고 있습니다.

물론, MariaDB 재단이 프로젝트의 상표권을 완전히 통제하기 위해 MariaDB 의 이름을 다른 이름(SomethingElseDB 등)으로 변경하는 선택지도 있습니다. 그러나 이는 MariaDB 가 쌓아온 인지도와 브랜드 가치를 잃게 될 것이므로 가능성은 낮습니다.

또한, MariaDB 는 MySQL 과 상당히 다르게 발전해 왔으며, 두 시스템 간의 차이를 다시 통합하는 데는 여러 해가 걸릴 것입니다. 하지만 충분한 자원과 커뮤니티의 의지가 있다면 해결 가능한 문제라고 생각합니다.


### Summary
보시다시피, MySQL 커뮤니티는 MySQL 의 소유 및 governance 방식 때문에 할 수 있는 일이 제한적입니다.장기적으로, MySQL 커뮤니티가 PostgreSQL 과 경쟁력을 유지하기 위해서는 모든 주요 플레이어들이 협력하여 (<a href="https://valkey.io/" target="_blank">Valkey Project</a> 와 유사하게) 새로운 브랜드로 MySQL 의 대안을 만드는 것이 유일한 방법일 것입니다. 이는 위에서 언급한 대부분의 문제를 해결할 수 있습니다.

___


### About the Author
<div style="display: flex; align-items: flex-start; gap: 1rem; margin: 1rem 0;">
  <img src="/assets/img/posts_2025/peter-zaitsev.jpg" alt="Peter Zaitsev" style="width: 300px; height: auto; border-radius: 8px;">
  <div>
    <p>
      <a href="https://www.percona.com/blog/author/pz/" target="_blank">Peter Zaitsev</a><br>
      Peter 는 2006년까지 MySQL 에서 고성능 그룹(High Performance Group)을 관리하다가 Percona 를 설립했습니다. Peter 는 컴퓨터 과학 석사 학위를 보유하고 있으며, 데이터베이스 커널, 컴퓨터 하드웨어, 애플리케이션 확장 분야의 전문가입니다.
    </p>
  </div>
</div>