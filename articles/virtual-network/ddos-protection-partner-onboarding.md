---
title: Współpraca ze standardem ochrony przed atakami DDoS platformy Azure
description: Poznaj możliwości współpracy z partnerem włączone przez standard ochrony przed atakami DDoS platformy Azure.
ms.service: virtual-network
documentationcenter: na
author: anupamvi
mms.devlang: na
ms.topic: article
ms.date: 01/28/2020
ms.author: kumud
ms.openlocfilehash: 39cb2f90b49725c78746df69505fad7160e1db92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76849684"
---
# <a name="partnering-with-azure-ddos-protection-standard"></a>Współpraca ze standardem ochrony przed atakami DDoS platformy Azure
W tym artykule opisano możliwości współpracy partnerskiej włączone przez standard ochrony przed atakami DDoS platformy Azure. Ten artykuł ma na celu pomóc menedżerom produktów i rolami rozwoju biznesowego zrozumieć ścieżki inwestycyjne i zapewnić wgląd w propozycje wartości partnerskiej.

## <a name="background"></a>Tło
Ataki typu "rozproszona odmowa usługi" (DDoS) są jednym z najważniejszych problemów związanych z dostępnością i bezpieczeństwem, które wyrażają klienci przenoszący swoje aplikacje do chmury. Z wymuszenie i hacktivism jest wspólne motywacje za ataki DDoS, zostały one konsekwentnie rośnie w rodzaju, skali, i częstotliwość występowania, ponieważ są one stosunkowo łatwe i tanie do uruchomienia.

Usługa Azure DDoS Protection zapewnia środki zaradcze przeciwko najbardziej zaawansowanym zagrożeniom DDoS, wykorzystując globalną skalę sieci platformy Azure. Usługa zapewnia rozszerzone możliwości ograniczania ddos dla aplikacji i zasobów wdrożonych w sieciach wirtualnych.

Partnerzy technologiczni mogą chronić zasoby swoich klientów natywnie za pomocą standardu ochrony przed atakami DDoS azure, aby rozwiązać problemy związane z dostępnością i niezawodnością spowodowane atakami DDoS.

## <a name="introduction-to-azure-ddos-protection-standard"></a>Wprowadzenie do standardu ochrony przed atakami DDoS platformy Azure
Standard ochrony przed atakami DDoS firmy Azure zapewnia ulepszone funkcje ograniczania ochrony przed atakami DDoS warstwy 3 i warstwy 4. Poniżej przedstawiono najważniejsze funkcje usługi DDoS Protection Standard.

### <a name="adaptive-real-time-tuning"></a>Adaptacyjne strojenie w czasie rzeczywistym
Dla każdej chronionej aplikacji standard ochrony przed atakami DDoS firmy Azure automatycznie dostosowuje progi zasad ograniczania ryzyka DDoS na podstawie wzorców profilu ruchu aplikacji. Usługa realizuje to dostosowanie przy użyciu dwóch szczegółowych informacji:

- Automatyczne uczenie się wzorców ruchu na klienta (na ip) dla warstwy 3 i 4.
- Minimalizowanie fałszywych alarmów, biorąc pod uwagę, że skala platformy Azure umożliwia wchłonięcie znacznej ilości ruchu.

![Adaptacyjne strojenie w czasie rzeczywistym](./media/ddos-protection-partner-onboarding/real-time-tuning.png)

### <a name="attack-analytics-telemetry-monitoring-and-alerting"></a>Analiza ataków, telemetria, monitorowanie i ostrzeganie
Usługa Azure DDoS Protection identyfikuje i ogranicza ataki DDoS bez interwencji użytkownika.

- Jeśli chroniony zasób znajduje się w ramach subskrypcji objętej usługą Azure Security Center, standard ochrony przed atakami DDoS automatycznie wysyła alert do centrum zabezpieczeń za każdym razem, gdy zostanie wykryty atak DDoS i złagodzony przed chronioną aplikacją.
- Alternatywnie, aby otrzymywać powiadomienia, gdy istnieje aktywne środki zaradcze dla chronionego publicznego adresu IP, można [skonfigurować alert](manage-ddos-protection.md#configure-alerts-for-ddos-protection-metrics) na metrykę w obszarze ataku DDoS, czy nie.
- Możesz dodatkowo utworzyć alerty dla innych metryk DDoS i [skonfigurować analizę ataków,](manage-ddos-protection.md#configure-ddos-attack-analytics) aby zrozumieć skalę ataku, porzuconego ruchu, wektorów ataku, najlepszych współautorów i innych szczegółów.

![Metryki DDoS](./media/ddos-protection-partner-onboarding/ddos-metrics.png)

### <a name="ddos-rapid-response-drr"></a>Szybka reakcja DDoS (DRR)
Klienci DDoS Protection Standard mają dostęp do [zespołu rapid response](https://azure.microsoft.com/blog/ddos-protection-attack-analytics-rapid-response/) podczas aktywnego ataku. DRR może pomóc w badaniu ataku, niestandardowe środki zaradcze podczas ataku i analizy po ataku.

### <a name="sla-guarantee-and-cost-protection"></a>Gwarancja umowy SLA i ochrona kosztów
Usługa DDoS Protection Standard jest objęta 99,99% SLA, a ochrona kosztów zapewnia kredyty zasobów do skalowania w poziomie podczas udokumentowanego ataku. Aby uzyskać więcej informacji, zobacz [SLA dla ochrony przed atakami DDoS platformy Azure](https://azure.microsoft.com/support/legal/sla/ddos-protection/v1_0/).

## <a name="featured-partner-scenarios"></a>Polecane scenariusze partnerów
Poniżej przedstawiono najważniejsze korzyści, które można uzyskać, integrując się ze standardem ochrony przed atakami DDoS azure:

- Oferowane przez partnerów usługi (moduł równoważenia obciążenia, zapora aplikacji sieci web, zapora sieciowa itp.) dla swoich klientów są automatycznie chronione (oznaczone białym oznaczeniem) przez standard ochrony DDoS azure na zapleczu.
- Partnerzy mają dostęp do analizy ataków i danych telemetrycznych standardu ochrony usługi Azure DDoS, które mogą zintegrować z własnymi produktami, oferując ujednolicone środowisko obsługi klienta.  
- Partnerzy mają dostęp do pomocy technicznej szybkiej reakcji DDoS, nawet w przypadku braku szybkiej reakcji platformy Azure w przypadku problemów związanych z atakami DDoS.
- Aplikacje chronione przez partnerów są wspierane przez gwarancję DDoS SLA i ochronę kosztów w przypadku ataków DDoS.

## <a name="technical-integration-overview"></a>Przegląd integracji technicznej
Możliwości partnerskie standardu ochrony przed usługami Azure DDoS są udostępniane za pośrednictwem witryny Azure portal, interfejsów API i interfejsu WIERSZA/PSA.

### <a name="integrate-with-ddos-protection-standard"></a>Integracja ze standardem ochrony przed atakami DDoS
Następujące kroki są wymagane dla partnerów do skonfigurowania integracji ze standardem ochrony przed atakami DDoS azure:
1. Utwórz plan ochrony przed atakami DDoS w żądanej subskrypcji (partnerskiej). Aby uzyskać instrukcje krok po kroku, zobacz [Tworzenie planu ochrony standardowej DDoS](manage-ddos-protection.md#create-a-ddos-protection-plan).
   > [!NOTE]
   > Tylko 1 Plan ochrony przed atakami DDoS musi zostać utworzony dla danej dzierżawy. 
2. Wdrażanie usługi z publicznym punktem końcowym w subskrypcjach (partnerów), takich jak moduł równoważenia obciążenia, zapory i zapora aplikacji sieci web. 
3. Włącz standard ochrony przed atakami DDoS w sieci wirtualnej usługi, która ma publiczne punkty końcowe przy użyciu planu ochrony przed atakami DDoS utworzonego w pierwszym kroku. Aby uzyskać instrukcje szczegółowe, zobacz [Włączanie standardowego planu ochrony DDoS](manage-ddos-protection.md#enable-ddos-for-an-existing-virtual-network)
   > [!IMPORTANT] 
   > Po włączeniu standardu ochrony przed atakami DDoS w sieci wirtualnej wszystkie publiczne adresy IP w tej sieci wirtualnej są automatycznie chronione. Pochodzenie tych publicznych adresów IP może znajdować się w ramach platformy Azure (subskrypcja klienta) lub poza platformą Azure. 
4. Opcjonalnie zintegruj standardowe dane telemetryczne usługi Azure DDoS Protection standard i analizy ataków na pulpicie nawigacyjnym dla klienta specyficznym dla aplikacji. Aby uzyskać więcej informacji na temat korzystania z danych telemetrycznych, zobacz [Używanie danych telemetrycznych ochrony przed atakami DDoS](manage-ddos-protection.md#use-ddos-protection-telemetry). Aby uzyskać więcej informacji na temat konfigurowania analizy ataków, zobacz [Konfigurowanie analizy ataków DDoS](manage-ddos-protection.md#configure-ddos-attack-analytics)

### <a name="onboarding-guides-and-technical-documentation"></a>Przewodniki dołączania i dokumentacja techniczna

- [Strona produktu usługi Azure DDoS Protection](https://azure.microsoft.com/services/ddos-protection/)
- [Dokumentacja usługi Azure DDoS Protection](ddos-protection-overview.md)
- [Odwołanie do interfejsu API ochrony przed atakami DDoS usługi Azure](https://docs.microsoft.com/rest/api/virtualnetwork/ddosprotectionplans)
- [Odwołanie do interfejsu API sieci wirtualnej platformy Azure](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks)

### <a name="get-help"></a>Uzyskiwanie pomocy

- Jeśli masz pytania dotyczące integracji aplikacji, usługi lub produktów ze standardem ochrony przed atakami DDoS, skontaktuj się ze [społecznością zabezpieczeń platformy Azure.](https://techcommunity.microsoft.com/t5/security-identity/bd-p/Azure-Security)
- Śledź dyskusje na temat [Przepełnienia stosu](https://stackoverflow.com/tags/azure-ddos/).

### <a name="get-to-market"></a>Wejdź na rynek

- Podstawowym programem współpracy z firmą Microsoft jest sieć Partner Network firmy [Microsoft.](https://partner.microsoft.com/) – Integracje microsoft graph security należą do [ścieżki niezależnego dostawcy oprogramowania MPN (ISV).](https://partner.microsoft.com/saas-solution-guide)
- [Microsoft Intelligent Security Association](https://www.microsoft.com/security/business/intelligent-security-association?rtc=1) to program przeznaczony specjalnie dla partnerów zabezpieczeń firmy Microsoft, który pomaga wzbogacić produkty zabezpieczające i zwiększyć wykrywalność integracji z produktami firmy Microsoft Security.

## <a name="next-steps"></a>Następne kroki
Wyświetlanie istniejących integracji partnerów:

- [Barracuda WAF jako usługa](https://www.barracuda.com/waf-as-a-service)
- [Usługa Azure Cloud WAF firmy Radware](https://www.radware.com/resources/microsoft-azure/)
