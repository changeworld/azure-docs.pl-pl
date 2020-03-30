---
title: Omówienie bramy usługi Azure API Management hosted samodzielnie | Dokumenty firmy Microsoft
description: Dowiedz się, jak samodzielnie hostowana brama usługi Azure API Management pomaga organizacjom zarządzać interfejsami API w środowiskach hybrydowych i wielochmurowych.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 415f0e209e607a863d715b1a66a2435603a662f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73513720"
---
# <a name="self-hosted-api-management-gateway-overview"></a>Omówienie bramy zarządzania interfejsami API hostowanego przez siebie

W tym artykule wyjaśniono, jak funkcja bramy hostowane samodzielnie umożliwia hybrydowe i wielochmurowe zarządzanie interfejsami API, prezentuje architekturę wysokiego poziomu i podkreśla jej podstawowe możliwości.

> [!NOTE]
> Funkcja bramy hostowanego samodzielnie jest w wersji zapoznawczej. W wersji zapoznawczej brama hostowana samodzielnie jest dostępna tylko w warstwach Deweloper i Premium bez dodatkowych opłat. Warstwa deweloperów jest ograniczona do pojedynczego wdrożenia bramy hostowanej samodzielnie.

## <a name="hybrid-and-multi-cloud-api-management"></a>Hybrydowe i wielochmurowe zarządzanie interfejsami API

Funkcja bramy hostowane samodzielnie rozszerza obsługę zarządzania interfejsami API dla środowisk hybrydowych i wielochmurowych i umożliwia organizacjom efektywne i bezpieczne zarządzanie interfejsami API hostowanymi lokalnie i w chmurach z jednej usługi zarządzania interfejsami API na platformie Azure.

Dzięki bramie hostowanej samodzielnie klienci mają możliwość wdrażania konteneryzowanej wersji składnika bramy usługi API Management w tych samych środowiskach, w których hostują swoje interfejsy API. Wszystkie bramy hostowane samodzielnie są zarządzane za pomocą usługi api Management, z którą są sfederowane, zapewniając klientom widoczność i ujednolicone środowisko zarządzania we wszystkich wewnętrznych i zewnętrznych interfejsach API. Umieszczenie bram w pobliżu interfejsów API umożliwia klientom optymalizację przepływów ruchu interfejsu API oraz spełnienie wymagań dotyczących zabezpieczeń i zgodności.

Każda usługa api management składa się z następujących kluczowych składników:

-   Płaszczyzna zarządzania, udostępniana jako interfejs API, używana do konfigurowania usługi za pośrednictwem portalu Azure, programu PowerShell i innych obsługiwanych mechanizmów.
-   Brama (lub płaszczyzna danych) jest odpowiedzialna za proxy żądań interfejsu API, stosowanie zasad i zbieranie danych telemetrycznych
-   Portal dla deweloperów używany przez deweloperów do odnajdywać, uczyć się i dołączać do korzystania z interfejsów API

Domyślnie wszystkie te składniki są wdrażane na platformie Azure, co powoduje, że cały ruch interfejsu API (wyświetlany jako czarne strzałki na obrazku poniżej) przepływa przez platformę Azure, niezależnie od tego, gdzie są hostowane zaplecze implementujące interfejsy API. Prostota operacyjna tego modelu wiąże się ze zwiększonym opóźnieniem, problemami ze zgodnością, a w niektórych przypadkach dodatkowymi opłatami za transfer danych.

![Przepływ ruchu interfejsu API bez bram hostowanych samodzielnie](media/self-hosted-gateway-overview/without-gateways.png)

Wdrażanie bram hostowanych samodzielnie w tych samych środowiskach co implementacje interfejsu API wewnętrznej bazy danych i dodawanie ich do usługi Api Management umożliwia przepływ ruchu interfejsu API bezpośrednio do interfejsów API wewnętrznej bazy danych, co zwiększa opóźnienia, optymalizuje koszty transferu danych i umożliwia zgodność przy zachowaniu korzyści z posiadania jednego punktu zarządzania i odnajdywania wszystkich interfejsów API w organizacji, niezależnie od tego, gdzie ich implementacje są hostowane.

![Przepływ ruchu interfejsu API z bramami hostowanymi samodzielnie](media/self-hosted-gateway-overview/with-gateways.png)

## <a name="packaging-and-features"></a>Opakowania i funkcje

Brama hostowana samodzielnie jest konteneryzowaną, funkcjonalnie równoważną wersją bramy zarządzanej wdrożonej na platformie Azure w ramach każdej usługi zarządzania interfejsami API. Brama hostowana samodzielnie jest dostępna jako kontener platformy Docker oparty na systemie Linux z rejestru kontenerów firmy Microsoft. Można go wdrożyć w programie Docker, Kubernetes lub innym rozwiązaniu aranżacji kontenerów uruchomionym na komputerze stacjonarnym, klastrze serwerów lub infrastrukturze chmury.

> [!IMPORTANT]
> Niektóre funkcje dostępne w bramie zarządzanej nie są jeszcze dostępne w wersji zapoznawczej. Przede wszystkim: Logowanie do zasad Centrum zdarzeń, integracja sieci szkieletowej usług, niższego protokołu HTTP/2. Nie ma planu udostępnienia wbudowanej pamięci podręcznej w bramie hostowanej przez siebie.

## <a name="connectivity-to-azure"></a>Łączność z platformą Azure

Brama hostowana samodzielnie wymaga wychodzącej łączności TCP/IP z platformą Azure na porcie 443. Każda brama hostowana samodzielnie musi być skojarzona z jedną usługą zarządzania interfejsem API i jest konfigurowana za pośrednictwem płaszczyzny zarządzania. Brama hostowana samodzielnie używa łączności z platformą Azure w celu:

-   Zgłaszanie swojego statusu przez wysyłanie wiadomości z bicia serca co minutę
-   Regularne sprawdzanie (co 10 sekund) i stosowanie aktualizacji konfiguracji, gdy są dostępne
-   Wysyłanie dzienników żądań i metryk do usługi Azure Monitor, jeśli jest to skonfigurowane
-   Wysyłanie zdarzeń do usługi Application Insights, jeśli jest to ustawione

Po utracie łączności z platformą Azure brama hostowana samodzielnie nie będzie mogła odbierać aktualizacji konfiguracji, raportować jej stanu ani przekazywać danych telemetrycznych.

Brama hostowana samodzielnie jest zaprojektowana tak, aby "nie powiodła się statyczna" i może przetrwać tymczasową utratę łączności z platformą Azure. Można go wdrożyć z włączono lokalną kopię zapasową konfiguracji lub bez niej. W pierwszym przypadku bramy hostowane samodzielnie będą regularnie zapisywać kopię zapasową konfiguracji na woluminie trwałym dołączonym do kontenera lub zasobnika.

Gdy kopia zapasowa konfiguracji jest wyłączona, a łączność z platformą Azure jest przerywana:

-   Hostowane przez siebie bramy, które są uruchomione, będą nadal działać przy użyciu kopii konfiguracji w pamięci
-   Zatrzymane bramy hostowane samodzielnie nie będą mogły się uruchomić

Gdy kopia zapasowa konfiguracji jest włączona, a łączność z platformą Azure jest przerywana:

-   Hostowane przez siebie bramy, które są uruchomione, będą nadal działać przy użyciu kopii konfiguracji w pamięci
-   Zatrzymane bramy hostowane samodzielnie zaczną używać kopii zapasowej konfiguracji

Po przywróceniu łączności każda brama hostowana samodzielnie, na którą ma wpływ awaria, automatycznie ponownie połączy się ze skojarzoną usługą API Management i pobierze wszystkie aktualizacje konfiguracji, które wystąpiły, gdy brama była "w trybie offline".

## <a name="next-steps"></a>Następne kroki

-   [Przeczytaj oficjalny dokument, aby uzyskać dodatkowe informacje na ten temat](https://aka.ms/hybrid-and-multi-cloud-api-management)
-   [Wdrażanie bramy hostowane samodzielnie w u platformie Docker](api-management-howto-deploy-self-hosted-gateway-to-docker.md)
-   [Wdrażanie bramy hostowane samodzielnie w umoście w umięsień](api-management-howto-deploy-self-hosted-gateway-to-k8s.md)
