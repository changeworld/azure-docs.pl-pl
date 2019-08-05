---
title: Co to jest Azure Security Center? | Microsoft Docs
description: Uzyskaj informacje na temat Centrum zabezpieczeń Azure, jego kluczowych możliwości i sposobu działania.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/06/2019
ms.author: v-mohabe
ms.openlocfilehash: f0ee4a1129246cedd7899056359144cab75f9475
ms.sourcegitcommit: f7998db5e6ba35cbf2a133174027dc8ccf8ce957
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/05/2019
ms.locfileid: "68782432"
---
# <a name="what-is-azure-security-center"></a>Co to jest Centrum zabezpieczeń Azure?

Usługa Azure Security Center to ujednolicony system zarządzania bezpieczeństwem infrastruktury. Ma on za zadanie zwiększenie poziomu bezpieczeństwa centrów danych i zapewnienie zaawansowanej ochrony przed zagrożeniami w przypadku obciążeń hybrydowych w chmurze (zarówno na platformie Azure, jak i poza nią) oraz w środowisku lokalnym.

Utrzymywanie bezpieczeństwa zasobów to wspólna odpowiedzialność dostawcy usług w chmurze, platformy Azure i klienta. Podczas przechodzenia do chmury trzeba upewnić się, że obciążenia będą bezpieczne. Trzeba też pamiętać, że przejście na model IaaS (infrastruktura jako usługa) to większa odpowiedzialność po stronie klienta niż w przypadku korzystania z modeli PaaS (platforma jako usługa) i SaaS (oprogramowanie jako usługa). Usługa Azure Security Center zapewnia narzędzia potrzebne do zwiększenia bezpieczeństwa sieci, zabezpieczenia usług i zapewnienia maksymalnego poziomu bezpieczeństwa.

Azure Security Center rozwiązuje trzy najbardziej pilne problemy związane z zabezpieczeniami:

-   **Szybko zmieniające się obciążenia** — jest to mocna strona rozwiązań chmurowych, a jednocześnie wyzwanie. Z jednej strony przed użytkownikami końcowymi otwiera się więcej możliwości. Z drugiej jednak pojawia się problem — jak sprawić, by ciągle zmieniające się usługi, które tworzą i wykorzystują użytkownicy, spełniały standardy bezpieczeństwa i były zgodne z najlepszymi rozwiązaniami dotyczącymi bezpieczeństwa?

-   **Coraz bardzie zaawansowane ataki** — wszędzie tam, gdzie pracujesz, ataki stają się coraz bardziej wyrafinowane. Trzeba zabezpieczyć pracę w chmurze publicznej, która jest połączona z Internetem, co sprawia, że jest jeszcze bardziej narażona na ataki w przypadku braku zastosowania najlepszych zabezpieczeń.

-   **Umiejętność zabezpieczeń jest w krótkim dostawie** — liczba alertów zabezpieczeń i systemów zgłaszających alerty pozwala uzyskać pewność, że Twoje środowiska są chronione. Bycie na bieżąco z najnowszymi atakami jest dużym wyzwaniem. Nie można stać w miejscu, gdy sytuacja w zakresie zabezpieczeń ciągle się zmienia.

Aby sprostać tym wyzwaniom, Security Center oferuje narzędzia umożliwiające:

-   **Zwiększanie poziomu bezpieczeństwa**: Security Center ocenia środowisko i pozwala poznać stan zasobów i określić, czy są one bezpieczne.

-   **Ochronę przed zagrożeniami**: Security Center umożliwia ocenę obciążeń i generuje zalecenia dotyczące zapobiegania zagrożeniom oraz alerty związane z wykryciem zagrożeń.

-   **Szybsze osiągnięcie bezpieczeństwa**: w usłudze Security Center wszystko jest wykonywane z szybkością chmury. Dzięki natywnej integracji wdrożenie usługi Security Center jest łatwe i zapewnia automatyczne aprowizowanie i ochronę w ramach usług platformy Azure.

## <a name="architecture"></a>Architektura

Ponieważ usługa Security Center jest częścią platformy Azure, usługi PaaS na platformie Azure — w tym Service Fabric, bazy danych SQL i konta magazynu — są monitorowane i chronione przez usługę Security Center bez konieczności każdorazowego wdrażania.

Ponadto usługa Security Center dzięki zainstalowaniu programu Microsoft Monitoring Agent chroni również nienależące do platformy Azure serwery i maszyny wirtualne, działające w chmurze lub lokalnie, zarówno w przypadku serwerów opartych na systemach Windows, jak i Linux. Maszyny wirtualne Azure są automatycznie aprowizowane w usłudze Security Center.

Zdarzenia zbierane z agentów i z platformy Azure są korelowane w aparacie analizy zabezpieczeń w celu generowania alertów wykrywania zagrożeń oraz dostarczania dostosowanych zaleceń (zadania związane ze zwiększaniem bezpieczeństwa). Należy je wykonywać, aby mieć pewność, że obciążenia są bezpieczne. Alerty należy badać jak najszybciej, aby upewnić się, że nie dochodzi do złośliwych ataków na działające obciążenia.

Po włączeniu usługi Security Center zasady zabezpieczeń wbudowane w usługę Security Center są odzwierciedlane w usłudze Azure Policy jako wbudowana inicjatywa w kategorii Security Center. Wbudowana inicjatywa jest automatycznie przypisywana do wszystkich subskrypcji zarejestrowanych w usłudze Security Center (w warstwach Bezpłatna lub Standardowa). Wbudowana inicjatywa zawiera tylko zasady inspekcji. Aby uzyskać więcej informacji na temat zasad usługi Security Center w usłudze Azure Policy, zobacz [Praca z zasadami zabezpieczeń](tutorial-security-policy.md).

## <a name="strengthen-security-posture"></a>Zwiększanie poziomu bezpieczeństwa

Usługa Azure Security Center umożliwia zwiększanie poziomu bezpieczeństwa. Oznacza to, że będzie ona pomagać w identyfikowaniu i wykonywaniu zalecanych jako najlepsze rozwiązania zadań polegających na zwiększaniu poziomu zabezpieczeń, a także we wdrażaniu tych zadań na maszynach, w usługach związanych z danymi i w aplikacjach. Obejmuje to zarządzanie zasadami zabezpieczeń i ich wymuszanie oraz upewnienie się, że maszyny wirtualne na platformie Azure, serwery spoza Azure oraz usługi PaaS platformy Azure je spełniają. Security Center zapewnia narzędzia dające syntetyczne informacje na temat obciążeń ze szczególnym uwzględnieniem stanu zabezpieczeń sieci. 

### <a name="manage-organization-security-policy-and-compliance"></a>Zarządzanie zasadami zabezpieczeń oraz zgodnością w organizacji

Podstawową sprawą jest zapewnienie, że działające obciążenia są bezpieczne. Aby to osiągnąć, trzeba wdrożyć dostosowane zasady zabezpieczeń. Ponieważ wszystkie zasady w usłudze Security Center są oparte na zasadach platformy Azure, otrzymujesz kompleksowe i elastyczne ** rozwiązanie na światowym poziomie**. W usłudze Security Center można ustawić uruchamianie zasad bezpieczeństwa na poziomie grupy zarządzania, subskrypcji lub nawet dla jednego dzierżawcy.

![Pulpit nawigacyjny usługi Security Center](media/security-center-intro/sc-dashboard.png)

Usługa Security Center ułatwia **identyfikowanie subskrypcji niezatwierdzonych przez dział IT**. Sprawdzając subskrypcje oznaczone na pulpicie nawigacyjnym etykietą **Nieobjęte**, można dowiedzieć się natychmiast, że zostały utworzone nowe subskrypcje, i upewnić się, że są objęte zasadami oraz chronione przez usługę Azure Security Center.

![Pulpit nawigacyjny z zasadami w usłudze Security Center](media/security-center-intro/sc-policy-dashboard.png)

Zaawansowane możliwości monitorowania w usłudze Security Center pozwalają również **na śledzenie zgodności i ładu oraz zarządzanie nimi w czasie**. Opcja **Ogólna zgodność** dostarcza dane na temat tego, ile subskrypcji jest zgodnych z zasadami skojarzonymi z danym obciążeniem. 

![Zasady w usłudze Security Center w czasie](media/security-center-intro/sc-policy-time.png)

### <a name="continuous-assessments"></a>Ciągłe oceny

Usługa Security Center ciągle wykrywa nowe zasoby wdrażane w różnych obciążeniach i ocenia, czy są one skonfigurowane zgodnie z najlepszymi rozwiązaniami w zakresie zabezpieczeń. Zasoby, które nie spełniają tego wymogu, są oznaczane, a przy tym generowana jest lista zaleceń z określonymi priorytetami. Zalecenia wskazują, co należy zrobić, aby chronić maszyny.

 **Mapa sieci** jest jednym z najbardziej zaawansowanych narzędzi w usłudze Security Center umożliwiającym stałe monitorowanie stanu zabezpieczeń sieci. Mapa umożliwia wyświetlenie topologii obciążeń, aby sprawdzić, czy każdy węzeł jest odpowiednio skonfigurowany. Można zobaczyć, jak węzły są połączone, co pomaga blokować niechciane połączenia, które mogą potencjalnie ułatwić osobie atakującej dostanie się do sieci.

![Mapa sieci w usłudze Security Center](media/security-center-intro/sc-net-map.png)

Usługa Security Center umożliwia lepsze radzenie sobie z alertami bezpieczeństwa dzięki dodaniu funkcji **Wskaźnik bezpieczeństwa**. Wskaźniki bezpieczeństwa są teraz skojarzone z każdym zaleceniem, co pozwala lepiej zrozumieć ważność danego zalecenia dla ogólnego poziomu bezpieczeństwa. Ma to kluczowe znaczenie przy **określaniu priorytetów pracy nad zabezpieczeniami**.

![Wskaźnik bezpieczeństwa w usłudze Security Center](media/security-center-intro/sc-secure-score.png)

### <a name="optimize-and-improve-security-by-configuring-recommended-controls"></a>Optymalizacja i zwiększanie bezpieczeństwa dzięki konfigurowaniu zalecanych środków kontroli

Główną wartość usługi Azure Security Center stanowią zalecenia. Zalecenia są dostosowane do określonych problemów z bezpieczeństwem wykrytych w obciążeniach klienta. Usługa Security Center wykonuje pracę administratora zabezpieczeń, nie tylko znajdując luki w zabezpieczeniach, ale też udostępniając szczegółowe instrukcje na temat sposobu ich usunięcia.

![Zalecenia usługi Security Center](media/security-center-intro/sc-recommendations.png)

W ten sposób usługa Security Center umożliwia nie tylko ustanawianie zasad zabezpieczeń, ale też zastosowanie bezpiecznych standardów konfiguracji we wszystkich zasobach.

Zalecenia pomagają zmniejszyć obszar ataków w każdym z zasobów. Obejmują one maszyny wirtualne platformy Azure, serwery spoza platformy Azure oraz usługi PaaS platformy Azure takie jak konta SQL i konta magazynu. Każdy rodzaj zasobu jest oceniany oddzielnie i ma swoje własne standardy.

![Przykład zaleceń usługi Security Center](media/security-center-intro/sc-recommendation-example.png)

## <a name="protect-against-threats"></a>Ochrona przed zagrożeniami

Ochrona przed zagrożeniami w usłudze Security Center umożliwia wykrywanie zagrożeń i zapobieganie im w warstwie infrastruktury jako usługi (IaaS), na serwerach spoza platformy Azure, jak również w przypadku platform działających jako usługa (PaaS) na platformie Azure.

Ochrona przed zagrożeniami w usłudze Security Center obejmuje kompleksową analizę łańcucha ataku, która automatycznie koreluje alerty w danym środowisku na podstawie modelu łańcucha ataku cybernetycznego. Dzięki temu uzyskujesz lepszy wgląd we wszystkie etapy ataku, jego punkt początkowy oraz wpływ na zasoby.



![Zalecenie usługi Security Center dotyczące ataku](media/security-center-intro/sc-attack-recommendation.png)

### <a name="advanced-threat-protection"></a>Zaawansowana ochrona przed zagrożeniami

Dzięki usłudze Security Center uzyskasz od razu natywną integrację z Zaawansowaną ochroną przed zagrożeniami w usłudze Windows Defender. Oznacza to, że maszyny wirtualne i serwery z systemem Windows są w pełni zintegrowane z zaleceniami i ocenami pochodzącymi z usługi Security Center bez konieczności wykonywania jakiejkolwiek konfiguracji. Gotowa funkcja zaawansowanego wykrywania zagrożeń jest również oferowana dla serwerów i maszyn wirtualnych z systemem Linux.

Ponadto usługa Security Center pozwala zautomatyzować zasady kontroli aplikacji w środowisku serwera. Funkcje adaptacyjnego sterowania aplikacjami w usłudze Security Center umożliwiają włączenie kompleksowego tworzenia listy dozwolonych aplikacji na serwerach z systemem Windows. Nie musisz tworzyć reguł i sprawdzać naruszeń, ponieważ wszystko odbywa się automatycznie.

### <a name="protect-paas"></a>Ochrona usług PaaS

Usługa Security Center ułatwia wykrywanie zagrożeń w usługach PaaS platformy Azure. Możesz wykrywać zagrożenia dotyczące usług platformy Azure, w tym usługi Azure App Service, Azure SQL, konta usługi Azure Storage i innych usług danych. Możesz również korzystać z natywnej integracji z funkcją analizy zachowań użytkowników i jednostek (UEBA) dostępną w usłudze Microsoft Cloud App Security w celu wykrywania anomalii w dziennikach aktywności platformy Azure.

### <a name="block-brute-force-attacks"></a>Blokowanie ataków siłowych

Usługa Security Center pomaga ograniczyć narażenie na ataki siłowe. Ograniczenie dostępu do portów maszyny wirtualnej i korzystanie z dostępu just-in-time do maszyny wirtualnej umożliwia zwiększenie bezpieczeństwa sieci dzięki zapobieganiu niepotrzebnemu dostępowi. Można ustawić zasady bezpiecznego dostępu na wybranych portach tylko dla autoryzowanych użytkowników, dozwolonych zakresów źródłowych adresów IP lub określonych adresów IP oraz przez określony czas.

![Usługa Security Center i atak siłowy](media/security-center-intro/sc-brute-force.png)

### <a name="protect-data-services"></a>Ochrona usług danych

Usługa Security Center obejmuje funkcje, które ułatwiają wykonywanie automatycznej klasyfikacji danych w Azure SQL. Możesz także uzyskać oceny potencjalnych luk w zabezpieczeniach w usługach Azure SQL i Storage oraz zalecenia dotyczące sposobu rozwiązania tych problemów.

### <a name="protect-iot-and-hybrid-cloud-workloads-preview"></a>Ochrona obciążeń związanych z chmurą IoT i hybrydową (wersja zapoznawcza)

Azure Security Center IoT (Internet rzeczy) upraszczają hybrydową ochronę obciążenia, zapewniając ujednoliconą widoczność i kontrolę, adaptacyjne zapobieganie zagrożeniom oraz Inteligentne wykrywanie zagrożeń oraz reagowanie na nie w ramach obciążeń działających na urządzeniach brzegowych, lokalnych, w Azure i w innych chmurach. Aby uzyskać więcej informacji, zobacz [Azure Security Center for IoT (wersja zapoznawcza)](https://docs.microsoft.com/azure/asc-for-iot/).

## <a name="get-secure-faster"></a>Szybsze osiągnięcie bezpieczeństwa

Natywna integracja platformy Azure (w tym Azure Policy i dzienników Azure Monitor) z bezproblemową integracją z innymi rozwiązaniami zabezpieczeń firmy Microsoft, takimi jak Microsoft Cloud App Security i usługa Windows Defender Advanced Threat Protection, należy się upewnić, że Rozwiązanie zabezpieczeń jest kompleksowe, a także proste do dołączenia i wdrożenia.

Ponadto można rozszerzyć pełne rozwiązanie poza platformę Azure na obciążenia działające w innych chmurach, jak i w lokalnych centrach danych.

### <a name="automatically-discover-and-onboard-azure-resources"></a>Automatyczne wykrywanie i dodawanie zasobów platformy Azure

Usługa Security Center zapewnia bezproblemową natywną integrację z platformą Azure i z zasobami Azure. Oznacza to, że można zebrać wszystkie ustawienia zabezpieczeń obejmujące zasady usługi Azure Policy i wbudowane zasady usługi Security Center we wszystkich zasobach Azure oraz mieć pewność, że w przypadku nowych zasobów od razu po ich utworzeniu na platformie Azure te zasady są automatycznie stosowane.

Rozbudowane kolekcje dzienników — dzienniki z systemów Windows i Linux są wykorzystywane przez aparat analizy zabezpieczeń i używane do tworzenia zaleceń i alertów.

## <a name="next-steps"></a>Kolejne kroki

- Do rozpoczęcia pracy z usługą Security Center jest wymagana subskrypcja platformy Microsoft Azure. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/).
- Usługa Security Center w warstwie cenowej Bezpłatna jest włączona w ramach subskrypcji platformy Azure. Aby móc korzystać z zaawansowanych możliwości wykrywania zagrożeń i zarządzania zabezpieczeniami, musisz przeprowadzić uaktualnienie do warstwy cenowej Standardowa. Warstwę Standardowa można wypróbować bezpłatnie. Aby uzyskać więcej informacji, zobacz [stronę z cennikiem usługi Security Center](https://azure.microsoft.com/pricing/details/security-center/).
- Jeśli chcesz teraz włączyć usługę Security Center, skorzystaj z artykułu [Szybki start: dołączanie subskrypcji platformy Azure do usługi Security Center w warstwie Standardowa](security-center-get-started.md), w którym opisano konieczne kroki.

