---
title: Siatki usług – informacje
description: Uzyskaj przegląd siatek usług, ich architektury i możliwości oraz kryteria, które należy wziąć pod uwagę przy wyborze jednego do wdrożenia.
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: eca49a3fac1ea0398ebe1d05bde20fbca3c81232
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594315"
---
# <a name="about-service-meshes"></a>Siatki usług – informacje

Siatka usług zapewnia funkcje, takie jak zarządzanie ruchem, odporność, zasady, zabezpieczenia, silna tożsamość i możliwość obserwoniania obciążeń. Aplikacja jest oddzielona od tych możliwości operacyjnych i siatki usług przenosi je z warstwy aplikacji i w dół do warstwy infrastruktury.

## <a name="scenarios"></a>Scenariusze

Oto niektóre ze scenariuszy, które można włączyć dla obciążeń podczas korzystania z siatki usługi:

- **Szyfruj cały ruch w klastrze** — włącz wzajemne TLS między określonymi usługami w klastrze. Można to rozszerzyć na ruch przychodzący i wychodzący na obwodzie sieci. Zapewnia bezpieczną opcję domyślną bez zmian wymaganych dla kodu aplikacji i infrastruktury.

- **Canary i stopniowe wdrażanie** — określ warunki podzbioru ruchu, który ma być kierowany do zestawu nowych usług w klastrze. Po pomyślnym przetestowaniu uwolnienia kanarek usuń routing warunkowy i fazę stopniowo zwiększając % całego ruchu do nowej usługi. Ostatecznie cały ruch zostanie przekierowany do nowej usługi.

- **Zarządzanie ruchem i manipulowanie** - Tworzenie zasad w usłudze, która ograniczy cały ruch do wersji usługi z określonego źródła. Lub zasady, która stosuje strategię ponawiania do klas błędów między określonymi usługami. Dublowanie ruchu na żywo do nowych wersji usług podczas migracji lub do debugowania problemów. Wstrzyknąć błędy między usługami w środowisku testowym, aby przetestować odporność.

- **Observability** — uzyskaj wgląd w sposób, w jaki usługi są połączone ruchu, który przepływa między nimi. Uzyskaj metryki, dzienniki i ślady dla całego ruchu w klastrze i transferu danych przychodzących/wychodzących. Dodaj rozproszone możliwości śledzenia do aplikacji.

## <a name="architecture"></a>Architektura

Siatka usługi składa się zazwyczaj z płaszczyzny kontrolnej i płaszczyzny danych.

Płaszczyzna **sterowania** ma wiele komponentów, które obsługują zarządzanie siatką usług. Zazwyczaj obejmuje to interfejs zarządzania, który może być interfejsem użytkownika lub interfejsem API. Zazwyczaj będą również zazwyczaj składniki, które zarządzają definicjami reguł i zasad, które definiują sposób, w jaki siatka usług powinna implementować określone możliwości. Istnieją również składniki, które zarządzają aspektami zabezpieczeń, takimi jak silna tożsamość i certyfikaty dla usługi mTLS. Siatki usług będą również zazwyczaj mają metryki lub składnik obserwowalności, który zbiera i agreguje metryki i dane telemetryczne z obciążeń.

Płaszczyzna **danych** zazwyczaj składa się z serwera proxy, który jest wtryskiwiony jako boczny do obciążeń. Ten serwer proxy jest skonfigurowany do kontrolowania całego ruchu sieciowego do i z zasobnika zawierającego obciążenie. Dzięki temu serwer proxy można skonfigurować do zabezpieczania ruchu za pośrednictwem usługi mTLS, dynamicznie trasy ruchu, stosowania zasad ruchu i zbierać metryki i informacje o śledzeniu. 

![Typowa architektura siatki usług](media/servicemesh/typical-architecture.png)

## <a name="capabilities"></a>Możliwości

Każda z siateczek usługi mają naturalne dopasowanie i skupić się na obsłudze określonych scenariuszy, ale zazwyczaj okaże się, że większość z nich zaimplementuje szereg, jeśli nie wszystkie z następujących możliwości.

### <a name="traffic-management"></a>Zarządzanie ruchem 

- **Protokół** – warstwa 7 (http, grpc)
- **Routing dynamiczny** — warunkowe, ważące, dublowanie
- Odporność — **limity** czasu, ponownych prób, wyłączniki
- **Polityka** – kontrola dostępu, limity stawek, przydziały
- **Testowanie** - wtrysk usterek

### <a name="security"></a>Zabezpieczenia

- **Szyfrowanie** – mTLS, zarządzanie certyfikatami, zewnętrzny urząd certyfikacji
- **Silna tożsamość** – SPIFFE lub podobne
- **Auth** – uwierzytelnianie, autoryzacja

### <a name="observability"></a>Obserwowalność

- **Metryki** – złote metryki, prometeusz, grafana
- **Śledzenie** — ślady między obciążeniami
- **Ruch** — klaster, ruch przychodzący/wychodzący

### <a name="mesh"></a>Siatka

- **Obsługiwane obliczenia** — kubernetes, maszyny wirtualne
- **Multi-cluster** - bramy, federacja

## <a name="selection-criteria"></a>Kryteria wyboru

Przed wybraniem siatki usług upewnij się, że rozumiesz wymagania i powody instalowania siatki usług. Spróbuj zadać następujące pytania.

- **Czy kontroler ingress jest wystarczający dla moich potrzeb?** - Czasami o możliwości, takich jak a / b testowania lub podziału ruchu przy przystawku jest wystarczająca do obsługi wymaganego scenariusza. Nie dodawaj złożoności do środowiska bez żadnych plusów.

- **Czy moje obciążenia i środowisko tolerują dodatkowe koszty ogólne?** - Wszystkie dodatkowe składniki wymagane do obsługi siatki usługi wymagają dodatkowych zasobów, takich jak procesor i pamięć. Ponadto wszystkie serwery proxy i ich skojarzone testy zasad zwiększają opóźnienie ruchu. Jeśli masz obciążeń, które są bardzo wrażliwe na opóźnienie lub nie można zapewnić dodatkowe zasoby na pokrycie składników siatki usługi, a następnie ponownie rozważyć.

- **Czy to niepotrzebnie zwiększa złożoność?** - Jeśli powodem instalacji siatki serwisowej jest uzyskanie możliwości, która niekoniecznie jest krytyczna dla zespołów biznesowych lub operacyjnych, zastanów się, czy dodatkowa złożoność instalacji, konserwacji i konfiguracji jest tego warta.

- **Czy można to przyjąć w sposób stopniowy?** - Niektóre z oczek serwisowych, które zapewniają wiele możliwości, można przyjąć w bardziej stopniowym podejściu. Zainstaluj tylko składniki, których potrzebujesz, aby zapewnić sukces. Gdy jesteś bardziej pewny siebie i wymagane są dodatkowe możliwości, a następnie eksploruj je. Oprzeć się pokusie, aby zainstalować *wszystko* od początku.

Jeśli po dokładnym rozważeniu zdecydujesz, że potrzebujesz siatki usług, aby zapewnić wymagane możliwości, następną decyzją jest *siatka serwisowa?*

Należy wziąć pod uwagę następujące obszary i które z nich są najbardziej zgodne z twoimi wymaganiami. Spowoduje to, że będziesz zmierzać w kierunku najlepszego dopasowania do środowiska i obciążeń. Sekcja [Następne kroki](#next-steps) pozwoli Ci uzyskać szczegółowe informacje na temat określonych siatek usług i sposobu mapowania tych obszarów.

- **Techniczne** - zarządzanie ruchem, polityka, bezpieczeństwo, możliwość obserwacji

- **Biznes** - wsparcie komercyjne, fundacja (CNCF), licencja OSS, zarządzanie

- **Operacyjne** — instalacja/uaktualnienia, wymagania dotyczące zasobów, wymagania dotyczące wydajności, integracje (metryki, telemetria, pulpity nawigacyjne, narzędzia, SMI), obciążenia mieszane (pule węzłów Linux i Windows), obliczenia (Kubernetes, maszyny wirtualne), wieloterwersywersy

- **Bezpieczeństwo** — eru, tożsamość, zarządzanie certyfikatami i rotacja, podłączane zewnętrzne urzędy certyfikacji


## <a name="next-steps"></a>Następne kroki

Poniższa dokumentacja zawiera więcej informacji na temat siateczek usług, które można wypróbować w usłudze Azure Kubernetes Service (AKS):

> [!div class="nextstepaction"]
> [Dowiedz się więcej o Istio ...][istio-about]

> [!div class="nextstepaction"]
> [Dowiedz się więcej o Linkerd ...][linkerd-about]

> [!div class="nextstepaction"]
> [Dowiedz się więcej o konsulu ...][consul-about]

Można również zapoznać się z interfejsem SMI (Service Mesh Interface), standardowym interfejsem dla siatki usług w usłudze Kubernetes:

- [Interfejs siatki usługi (SMI)][smi]


<!-- LINKS - external -->
[smi]: https://smi-spec.io/

<!-- LINKS - internal -->
[istio-about]: ./servicemesh-istio-about.md
[linkerd-about]: ./servicemesh-linkerd-about.md
[consul-about]: ./servicemesh-consul-about.md