---
title: Omówienie strony Linkerd
description: Uzyskiwanie przeglądu aplikacji Linkerd
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 3181be62a14ec1b3450bd181172b5323ca176427
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593771"
---
# <a name="linkerd"></a>Linkerd ( Linkerd )

## <a name="overview"></a>Omówienie

[Linkerd][linkerd] to łatwa w użyciu i lekka siatka serwisowa.

## <a name="architecture"></a>Architektura

Linkerd zapewnia płaszczyznę danych, która składa się z ultralekkich [linkerd][linkerd-proxy] specjalistycznych samochodów bocznych proxy. Te inteligentne serwery proxy kontrolują cały ruch sieciowy w aplikacjach i obciążeniach z siatki. Serwery proxy również uwidaczniać metryki za pośrednictwem punktów końcowych metryk [Prometheus.][prometheus]

Płaszczyzna sterowania zarządza konfiguracją i zagregowanymi danymi telemetrycznymi za pomocą następujących [komponentów:][linkerd-architecture]

- **Kontroler** — udostępnia interfejs API, który napędza interfejs wiersza polecenia i pulpitu nawigacyjnego Linkerd. Zapewnia konfigurację serwerów proxy.

- **Dotknij** — sa ułatnij zegarki w czasie rzeczywistym na prośbach i odpowiedziach.

- **Tożsamość** — zapewnia funkcje tożsamości i zabezpieczeń, które umożliwiają mTLS między usługami.

- **Sieć Web** — udostępnia pulpit nawigacyjny linkerd.


Na poniższym diagramie architektury pokazano, jak różne składniki w płaszczyźnie danych i płaszczyzny sterującej współdziałają.


![Omówienie składników i architektury linkerd.](media/servicemesh/linkerd/about-architecture.png)


## <a name="selection-criteria"></a>Kryteria wyboru

Podczas oceny programu Linkerd dla obciążeń należy zrozumieć i wziąć pod uwagę następujące obszary:

- [Zasady projektowania](#design-principles)
- [Możliwości](#capabilities)
- [Scenariuszy](#scenarios)


### <a name="design-principles"></a>Zasady projektowania

Projekt Linkerd [kieruje][design-principles] następującymi zasadami projektowania:

- **Keep it Simple** - Musi być łatwy w użyciu i zrozumieniu.

- **Minimalizowanie wymagań dotyczących zasobów** — nakładanie minimalnych kosztów wydajności i zasobów.

- **Just Work** — nie przerywaj istniejących aplikacji i nie wymaga złożonej konfiguracji.


### <a name="capabilities"></a>Możliwości

Linkerd udostępnia następujący zestaw funkcji:

- **Siatka** – wbudowana opcja debugowania

- **Zarządzanie ruchem** – podział, przekrojowe limity czasu, ponownych prób, ruch przychodzący

- **Bezpieczeństwo** – szyfrowanie (mTLS), certyfikaty autorotowane co 24 godziny

- **Observability** – złote metryki, dotknij, śledzenie, profile usług i metryki trasy, pulpit nawigacyjny z wykresami topologii, prometeusz, grafana


### <a name="scenarios"></a>Scenariusze

Linkerd jest dobrze nadaje się do i sugerowane dla następujących scenariuszy:

- Prosty w użyciu z niezbędnym zestawem wymagań dotyczących możliwości

- Niskie opóźnienia, niskie obciążenie, z naciskiem na przestrzeganie i proste zarządzanie ruchem


## <a name="next-steps"></a>Następne kroki

W poniższej dokumentacji opisano sposób instalowania usługi Linkerd w usłudze Azure Kubernetes Service (AKS):

> [!div class="nextstepaction"]
> [Instalowanie usługi Linkerd w usłudze Azure Kubernetes (AKS)][linkerd-install]

Można również dalej badać funkcje i architekturę Linkerd:

- [Funkcje Linkerd][linkerd-features]
- [Architektura linkerd][linkerd-architecture]

<!-- LINKS - external -->
[linkerd]: https://linkerd.io/2/overview/
[linkerd-architecture]: https://linkerd.io/2/reference/architecture/
[linkerd-features]: https://linkerd.io/2/features/
[design-principles]: https://linkerd.io/2/design-principles/
[linkerd-proxy]: https://github.com/linkerd/linkerd2-proxy

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[linkerd-install]: ./servicemesh-linkerd-install.md