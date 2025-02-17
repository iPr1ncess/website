---
title: 속도 제한
slug: rate-limits
top_graphic: 1
date: 2018-01-04
lastmod: 2019-06-04
---

{{< lastmod >}}

Let's Encrypt는 가능한 한 많은 사람들이 공정하게 사용할 수 있도록 속도를 제한합니다. 이 속도 제한이 대부분의 사람들에게 기본적으로 작용할 만큼 충분히 높다고 믿습니다.  
또한 인증을 갱신하는 것이 속도 제한에 거의 도달하지 않고, 대형 기관들이 Let's Encrypt의 개입 없이 발급할 수 있는 증명서의 수를 점진적으로 늘릴 수 있도록 설계했습니다.

만약에 귀하가 Let's Encrypt 클라이언트를 적극적으로 개발하거나 테스트하는 경우, 제품 API 대신에 [준비 환경]({{< relref "/docs/staging-environment.md" >}})을 활용하십시오.
귀하가 공급자 또는 대형 웹 사이트와 통합하는 작업을 하는 경우 [당사의 통합 가이드를 검토하십시오]({{< relref "/docs/integration-guide.md" >}}).

주요한 한계는 <a name="certificates-per-registered-domain"></a>**등록된 도메인당 인증서**, (주 50회). 등록된 도메인은 일반적으로 도메인 이름 등록 업체에서 구입한 도메인의 부분이다. 예를 들어, `www.example.com`에서 등록된 도메인은 `example.com`입니다. `new.blog.example.co.uk`에서는, 등록된 도메인은 `example.co.uk`입니다. 등록된 도메인을 계산하기 위해 [Public Suffix List](https://publicsuffix.org)를 사용합니다.

귀하가 하위 도메인을 많이 가지고 있는 경우, 최대 100개까지 하나의 인증서로 결합하고 싶을 수 있습니다. <a name="names-per-certificate"></a>**인증서당 이름 수**.위의 한도와 결합하면, 1주일에 최대 5000개의 고유 서브 도메인이 포함된 인증서를 발급할 수 있습니다. 이름이 여러 개인 인증서는 보통 SAN 인증서 또는 때로 UCC 인증서라고 불립니다.

리뉴얼은 특별히 취급됩니다: 리뉴얼이 **등록 도메인당 인증서** 수 한도에 따라 제한되지는 않지만 **중복 인증서** 한도는 주당 5개로 제한될 수 있습니다. 참고: 2019년 3월까지 등록 도메인당 인증서 한도에 갱신이 제한되었지만, [이제는 그렇지 않습니다](https://community.letsencrypt.org/t/rate-limits-fixing-certs-per-name-rate-limit-order-of-operations-gotcha/88189).

인증서에 정확히 동일한 호스트 이름 집합이 포함되어 있는 경우에 인증서는 이전 인증서의 갱신 (또는 중복)으로 간주되며, 대문자 및 호스트 이름의 순서를 무시합니다. 예를 들어, [`www.example.com`, `example.com`]이라는 이름의 인증서를 요청한 경우, 그 주에 [`www.example.com`, `example.com`]의 인증서를 4개 더 추가로 요청할 수 있습니다. [`blog.example.com`]을 추가하여 호스트 이름 집합을 변경한 경우 추가 인증서를 요청할 수 있습니다.

중복 인증서 제한 및 갱신 예외는 요청된 공개키 및 확장을 무시합니다. 인증서 발행은 귀하가 새로운 키를 사용하고 있더라도 갱신으로 여겨집니다.

인증서를 재발급하는 데 사용된 리소스가 이미 소비되었기 때문에, **인증서 재발급시 속도 제한이 재설정되지 않습니다.**

시간당, 호스트 이름당, 계정당 5회 <a name="failed-validations"></a>**실패 검증** 제한이 있습니다. 이 제한은 [준비 환경]({{< relref "/docs/staging-environment.md" >}})에서 더 높기 때문에, 귀하는 그 환경을 사용하여 연결 문제를 디버그 할 수 있습니다.

"신규-등록","신규-인증","신규-인증서" 엔드포인트에는 초당 20개의 <a name="overall-requests"></a>**전반적인 요청** 제한이 있습니다. "/directory" 엔드포인트와 "/acme" 디렉토리및 하위 디렉토리는 초당 40개의 전체 요청 제한을 가집니다.

귀하가 경험할 것 같지 않은 두가지 한계점이 있습니다.

귀하는 3시간 당 <a name="accounts-per-ip-address"></a>**IP 주소당 최대 10개의 계정**을 만들 수 있습니다. 귀하는 3시간 당 IPv6/49 내에서 **IP 범위 당 최대 500개의 계정**을 만들 수 있습니다. 어느 쪽이든 한도를 치는 것은 매우 드문 일이며, 대형 통합업체들은 [많은 고객을 위한 하나의 계정을 사용]({{< relref "/docs/integration-guide.md" >}})하는 디자인을 선호한다고 권장합니다.

귀하는 귀하의 계정에 최고 300개의 <a name="pending-authorizations"></a>**발행 허가**를 가질 수 있습니다. 이 요금 한도에 이르는 것은 드물며, ACME 클라이언트를 개발할 때 가장 자주 발생합니다. 보통 귀하의 고객이 허가를 만들고 그것을 이행하지 않는다는 것을 의미합니다. 귀하가 ACME 클라이언트를 개발하는 경우 [준비 환경]({{< relref "/docs/staging-environment.md" >}})을 사용하십시오.

ACME v2 API 사용자의 경우, 귀하는 3시간당 계정 당 최대 300개의 <a name="new-orders"></a>**새로운 주문**을 만들 수 있습니다.

# <a name="overrides"></a>덮어쓰기

귀하가 요금 한도를 넘으셨다면, 임시로 재설정할 방법이 없습니다. 일주일 후에 요금 한도가 만료될 때까지 기다려야 할것입니다. 슬라이딩 창구를 사용하기 때문에 귀하가 월요일에 25개, 금요일에 25개 이상의 인증서를 발급해 주었으면, 월요일부터 다시 발급해 주셔야 할 것입니다. 귀하는 공공의 [인증서 투명성](https://www.certificate-transparency.org) 로그를 사용하는 [crt.sh에서 검색](https://crt.sh)하면 등록 도메인에 대해 발급된 인증서 목록을 얻을 수 있습니다.

인증서 발급에 관련된 자원이 이미 사용되었기 때문에 인증서 해지 시 속도 제한이 재설정되지 않습니다.

만약 귀하가 Let's Encrypt 통합을 하고 있는 대규모 호스팅 제공자 또는 조직이라면, 더 높은 요금 제한을 요청할 수 있는 [요금 제한 양식](https://goo.gl/forms/plqRgFVnZbdGhE9n1)이 있습니다. 요청을 처리하는 데 몇 주가 걸리기 때문에 이 양식은 요금제한이 스스로 재설정 되는 것보다 더 빨리 재설정하면 안 됩니다.

귀하가 발급할 수 있는 고유한 등록 도메인의 수에 제한이 없기 때문에 대부분의 호스팅 제공자는 요금 한도를 올릴 필요가 없습니다. 대부분의 고객이 등록된 도메인에 2000개 이상의 하위 도메인을 가지고 있지 않는 한, 귀하는 아마 증가가 필요하지 않을 것입니다. 자세한 내용은 [통합 가이드]({{< relref "/docs/integration-guide.md" >}})를 참조하십시오.

# <a name="clearing-pending"></a>대기 중인 승인 지우기

보류 중인 인증 개체가 많고 속도 제한 오류가 발생하는 경우, 귀하는 [ACME 사양](https://github.com/ietf-wg-acme/acme/blob/master/draft-ietf-acme-acme.md#responding-to-challenges)에 설명된 대로 JWS로 서명된 POST를 해당 과제 중 하나에 제출하여 인증 개체의 유효성 검사 시도를 작동시킬 수 있습니다. 보류 중인 권한 부여 개체는 https://acme-v01.api.letsencrypt.org/acme/authz/XYZ 형식의 URL로 표시되며 귀하의 클라이언트 로그에 표시되어야 합니다. 검증의 성공 여부는 중요하지 않다는 점에 유의하십시오. 어느 쪽이든 '보류 중' 상태에서 인증을 받을 것입니다. 귀하가 관련 인증 URL이 들어 있는 로그가 없는 경우, 요금 제한이 만료될때까지 기다리십시오. 위에서 설명한 바와 같이, 슬라이딩 윈도우가 있으므로, 발행 패턴에 따라 1주일도 걸리지 않을 수 있습니다.

많은 수의 보류 중인 인증서가 있는 것은 일반적으로 버그가 있는 클라이언트의 결과라는 점에 유의하십시오. 만약 귀하가 요금 한도를 자주 넘는다면 귀하는 귀하의 클라이언트 코드를 다시 확인해야 합니다.
