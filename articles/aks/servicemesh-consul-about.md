---
title: Przegląd konsula
description: Zapoznaj się z przeglądem konsula
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: c518985b360fa3264bd5ac1e3fe76d61b2810b9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594213"
---
# <a name="consul"></a>Konsul

## <a name="overview"></a>Omówienie

[Konsul][consul] jest rozwiązaniem sieciowym obsługującym wiele centrów danych do łączenia i zabezpieczania usług na platformach runtime. [Connect][consul-features] jest składnikiem, który zapewnia możliwości siatki usług.

## <a name="architecture"></a>Architektura

Konsul udostępnia płaszczyznę danych, która domyślnie składa się z [wózków bocznych opartych][consul-sidecar] na [wysłanniku.][envoy-proxy] Konsul posiada podłączoną architekturę proxy. Te inteligentne serwery proxy kontrolują cały ruch sieciowy w aplikacjach i obciążeniach z siatki.

Płaszczyzna sterowania zarządza konfiguracją i zasadami za pomocą następujących [składników:][consul-architecture]

- **Serwer** — agent konsula działający w trybie serwera, który utrzymuje stan klastra konsula.

- **Klient** - Agent konsula działający w lekkim trybie klienta. Każdy węzeł obliczeniowy musi mieć uruchomiony agent klienta. Ta konfiguracja brokerów klienta i zasady między obciążeniami i konfiguracji konsula. 

Na poniższym diagramie architektury pokazano, jak różne składniki w płaszczyźnie danych i płaszczyzny sterującej współdziałają.

![Przegląd komponentów konsula i architektury.](media/servicemesh/consul/about-architecture.png)


## <a name="selection-criteria"></a>Kryteria wyboru

Podczas oceny konsula pod kątem obciążeń ważne jest zrozumienie i rozważenie następujących obszarów:

- [Zasady konsula](#consul-principles)
- [Możliwości](#capabilities)
- [Scenariuszy](#scenarios)


### <a name="consul-principles"></a>Zasady konsula

Projekt konsula [kieruje][consul-principles] następującymi zasadami:

- **Api-Driven** — kodyfikuj całą konfigurację i zasady.

- **Uruchamianie i łączenie w dowolnym miejscu** — łączenie obciążeń na platformach środowiska uruchomieniowego (Kubernetes, VM, Serverless).

- **Rozszerzanie i integrowanie** — bezpieczne łączenie obciążeń między infrastrukturą.


### <a name="capabilities"></a>Możliwości

Konsul zapewnia następujący zestaw możliwości:

- **Siatka** – brama (centrum danych wielozadaniowe), maszyny wirtualne (poza węzłami klastra), synchronizacja usług, wbudowana opcja debugowania

- **Proxy** - Wysłannik, wbudowany serwer proxy, podłączalny, l4 proxy dostępne dla obciążeń systemu Windows

- **Zarządzanie ruchem** – routing, dzielenie, rozpoznawanie

- **Polityka** – intencje, listy ACL

- **Bezpieczeństwo** — autoryzacja, uwierzytelnianie, szyfrowanie, tożsamości oparte na spiffe, zewnętrzny urząd certyfikacji (Vault), zarządzanie certyfikatami i rotacja

- **Observability** – metryki, panel interfejsu użytkownika, prometeusz, grafana


### <a name="scenarios"></a>Scenariusze

Konsul jest dobrze dopasowany i zasugerowany w następujących scenariuszach:

- Rozszerzanie istniejących obciążeń połączonych konsula

- Wymagania dotyczące zgodności dotyczące zarządzania certyfikatami

- Siatka usługi wielu klastrów

- Obciążenia oparte na maszynach wirtualnych, które mają zostać uwzględnione w siatce usług



## <a name="next-steps"></a>Następne kroki

W poniższej dokumentacji opisano sposób instalowania konsula w usłudze Azure Kubernetes Service (AKS):

> [!div class="nextstepaction"]
> [Instalowanie konsula w usłudze Azure Kubernetes (AKS)][consul-install]

Można również dowiedzieć się więcej funkcje konsula i architektury:

- [Funkcje konsula][consul-features]
- [Architektura konsula][consul-architecture]
- [Konsul - Jak działa Connect][consul-how-connect-works]

<!-- LINKS - external -->
[consul]: https://www.consul.io/mesh.html
[consul-features]: https://www.consul.io/docs/connect/index.html
[consul-architecture]: https://www.consul.io/docs/internals/architecture.html
[consul-sidecar]: https://www.consul.io/docs/connect/proxies.html
[consul-how-connect-works]: https://www.consul.io/docs/connect/connect-internals.html
[consul-principles]: https://www.consul.io/

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[consul-install]: ./servicemesh-consul-install.md
