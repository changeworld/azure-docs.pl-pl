---
title: Przegląd istio
description: Uzyskaj przegląd istio
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 8518e30a54c2486abf84cd9ac026cc4dccb3fa84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593904"
---
# <a name="istio"></a>Istio (istio)

## <a name="overview"></a>Omówienie

[Istio][istio] to w pełni funkcjonalna, konfigurowalna i rozszerzalna siatka serwisowa.

## <a name="architecture"></a>Architektura

Istio udostępnia płaszczyznę danych, która składa się z [wózków][envoy-proxy]bocznych opartych na wysłanniku. Te inteligentne serwery proxy kontrolują cały ruch sieciowy w aplikacjach i obciążeniach z siatki.

Płaszczyzna sterowania zarządza konfiguracją, zasadami i danymi telemetrycznymi za pomocą następujących [składników:][what-is-istio]

- **Mikser** — wymusza zasady kontroli dostępu i użycia. Zbiera dane telemetryczne z serwerów proxy, które są wypychane do [Prometheus][prometheus].

- **Pilot** — zapewnia odnajdowanie usług i zarządzanie ruchem zasady/konfigurację dla serwerów proxy.

- **Cytadela** — zapewnia funkcje tożsamości i zabezpieczeń, które umożliwiają mTLS między usługami.

- **Kambuz** - streszczenia i zapewnia konfigurację składników.

Na poniższym diagramie architektury pokazano, jak różne składniki w płaszczyźnie danych i płaszczyzny sterującej współdziałają.


![Omówienie komponentów i architektury Istio.](media/servicemesh/istio/about-architecture.png)


## <a name="selection-criteria"></a>Kryteria wyboru

Podczas oceny istio dla obciążeń należy zrozumieć i wziąć pod uwagę następujące obszary:

- [Cele projektowe](#design-goals)
- [Możliwości](#capabilities)
- [Scenariuszy](#scenarios)


### <a name="design-goals"></a>Cele projektu

Projekt Istio [wyznacza][design-goals] następujące cele projektowe:

- **Maksymalizuj przejrzystość** — zezwalaj na przyjęcie z minimalną ilością pracy, aby uzyskać rzeczywistą wartość z systemu.

- **Rozszerzalność** - Musi być w stanie rosnąć i dostosowywać się do zmieniających się potrzeb.

- **Przenośność** — łatwo uruchamiaj w różnych środowiskach — w chmurze, lokalnie.

- **Jednolitość zasad** — spójność w definiowaniu zasad w różnych zasobach.


### <a name="capabilities"></a>Możliwości

Istio udostępnia następujący zestaw możliwości:

- **Siatka** – bramy (wielotereterasowe), maszyny wirtualne (rozszerzenie siatki)

- **Zarządzanie ruchem** – routing, podział, limity czasu, wyłączniki, ponownych prób, ruch przychodzący, wyjście

- **Zasady** — kontrola dostępu, limit szybkości, przydział, niestandardowe karty zasad

- **Bezpieczeństwo** – uwierzytelnianie (jwt), autoryzacja, szyfrowanie (mTLS), zewnętrzny urząd certyfikacji (HashiCorp Vault)

- **Observability** – złote metryki, lustro, śledzenie, niestandardowe adaptery, prometeusz, grafana

### <a name="scenarios"></a>Scenariusze

Istio jest dobrze nadaje się do i zasugerował dla następujących scenariuszy:

- Wymagaj rozszerzalności i bogatego zestawu funkcji

- Rozszerzenie siatki w celu uwzględnienia obciążeń opartych na maszynach wirtualnych

- Siatka usługi wielu klastrów

## <a name="next-steps"></a>Następne kroki

W poniższej dokumentacji opisano sposób instalowania programu Istio w usłudze Azure Kubernetes Service (AKS):

> [!div class="nextstepaction"]
> [Instalowanie programu Istio w usłudze Azure Kubernetes (AKS)][istio-install]

Można również dalej badać koncepcje Istio i dodatkowe modele wdrażania:

- [Koncepcje Istio][what-is-istio]
- [Modele wdrażania Istio][deployment-models]

<!-- LINKS - external -->
[istio]: https://istio.io
[what-is-istio]: https://istio.io/docs/concepts/what-is-istio/
[design-goals]: https://istio.io/docs/concepts/what-is-istio/#design-goals
[deployment-models]: https://istio.io/docs/concepts/deployment-models/

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[istio-install]: ./servicemesh-istio-install.md
