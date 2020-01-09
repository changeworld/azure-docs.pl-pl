---
title: Integracja rozwiązań zabezpieczeń w usłudze Azure Security Center | Microsoft Docs
description: Poznaj sposób integracji usługi Azure Security Center z partnerami w celu poprawy ogólnego stanu zabezpieczeń zasobów platformy Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2019
ms.author: memildin
ms.openlocfilehash: 7174003485d51cf582c798c4b18404b1b72de0fb
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/28/2019
ms.locfileid: "75530956"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integracja rozwiązań zabezpieczeń w usłudze Azure Security Center
Ten dokument ułatwia zarządzanie rozwiązaniami zabezpieczeń już połączonymi z usługą Azure Security Center i dodawanie nowych.

> [!NOTE]
> Podzestaw rozwiązań zabezpieczeń został wycofany 31 lipca 2019. Aby uzyskać więcej informacji i alternatywnych usług, zobacz wycofywanie [funkcji Security Center (lipiec 2019)](security-center-features-retirement-july2019.md#menu_solutions).

## <a name="integrated-azure-security-solutions"></a>Zintegrowane rozwiązania zabezpieczeń platformy Azure
Usługa Security Center ułatwia włączanie zintegrowanych rozwiązań zabezpieczeń na platformie Azure. Korzyści:

- **Uproszczone wdrażanie**: usługa Security Center oferuje udoskonaloną aprowizację zintegrowanych rozwiązań partnerskich. W przypadku rozwiązań takich jak oprogramowanie chroniące przed złośliwym kodem i Ocena luk w zabezpieczeniach Security Center można zainicjować obsługę agenta na maszynach wirtualnych. W przypadku urządzeń zapory Security Center może wymagać większości wymaganych konfiguracji sieci.
- **Zintegrowane funkcje wykrywania**: zdarzenia zabezpieczeń z rozwiązań partnerskich są automatycznie zbierane, agregowane i wyświetlane w ramach zdarzeń i alertów usługi Security Center. Te zdarzenia są także połączone z funkcjami wykrywania z innych źródeł, aby zapewnić zaawansowane możliwości w zakresie wykrywania zagrożeń.
- **Ujednolicone zarządzanie monitorowaniem kondycji**: klienci mogą używać zintegrowanych zdarzeń kondycji do błyskawicznego monitorowania wszystkich rozwiązań partnerskich. Podstawowe funkcje zarządzania zapewniają łatwy dostęp do konfiguracji zaawansowanej przy użyciu rozwiązania partnerskiego.

Obecnie zintegrowane rozwiązania zabezpieczeń obejmują ocenę luk w zabezpieczeniach przez [Qualys](https://www.qualys.com/public-cloud/#azure) i [Rapid7](https://www.rapid7.com/products/insightvm/) oraz zaporę aplikacji sieci Web firmy Microsoft Application Gateway.

> [!NOTE]
> Security Center nie zainstaluje Microsoft Monitoring Agent na urządzeniach wirtualnych partnera, ponieważ większość dostawców zabezpieczeń zabroni zewnętrznych agentów działających na ich urządzeniach.
>
>

## <a name="how-security-solutions-are-integrated"></a>Jak są integrowane rozwiązania zabezpieczeń
Rozwiązania zabezpieczeń platformy Azure, które zostały wdrożone z usługi Security Center, są automatycznie połączone. Można także połączyć inne źródła danych zabezpieczeń, w tym komputery działające lokalnie lub w innych chmurach.

![Integracja rozwiązań partnerskich](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Zarządzanie zintegrowanymi rozwiązaniami zabezpieczeń platformy Azure i innymi źródłami danych

1. Zaloguj się do [portalu Azure](https://azure.microsoft.com/features/azure-portal/).

2. W **menu platformy Microsoft Azure** wybierz pozycję **Security Center**. Zostanie otwarte okno **Security Center — Przegląd**.

3. W menu usługi Security Center wybierz pozycję **Rozwiązania zabezpieczeń**.

   ![Security Center — Przegląd](./media/security-center-partner-integration/overview.png)

W **rozwiązaniach zabezpieczeń**można sprawdzić kondycję zintegrowanych rozwiązań zabezpieczeń platformy Azure i uruchomić podstawowe zadania zarządzania.

### <a name="connected-solutions"></a>Rozwiązania połączone

Sekcja **połączone rozwiązania** zawiera rozwiązania zabezpieczeń, które są obecnie połączone z Security Center. Pokazuje również stan kondycji każdego rozwiązania.  

![Rozwiązania połączone](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

Stanem rozwiązania partnerskiego może być:

* Zdrowy (zielony) — brak problemów z kondycją.
* Zła kondycja (czerwony) — występuje problem z kondycją, który wymaga natychmiastowej uwagi.
* Problemy z kondycją (pomarańczowy) — rozwiązanie przestało zgłaszać swoją kondycję.
* Nie zgłoszono (szare) — rozwiązanie nie zgłosiło jeszcze niczego i żadne dane kondycji nie są dostępne. Stan rozwiązania może być nieraportowany, jeśli był on ostatnio połączony i nadal jest wdrażany.

> [!NOTE]
> Jeśli dane stanu kondycji są niedostępne, Security Center pokazuje datę i godzinę ostatniego odebranego zdarzenia, aby wskazać, czy rozwiązanie zgłasza, czy nie. Jeśli dane dotyczące kondycji nie są dostępne i nie odebrano żadnych alertów w ciągu ostatnich 14 dni, Security Center wskazuje, że rozwiązanie jest w złej kondycji lub nie jest raportowane.
>
>

1. Wybierz opcję **Widok** , aby uzyskać dodatkowe informacje i opcje, takie jak:

   - **Konsola rozwiązania**. Otwiera środowisko zarządzania dla tego rozwiązania.
   - **Połącz maszynę wirtualną**. Otwiera stronę łączenie aplikacji. W tym miejscu możesz połączyć zasoby z rozwiązaniem partnerskim.
   - **Usuń rozwiązanie**.
   - **Skonfiguruj**.

   ![Szczegóły rozwiązania partnerskiego](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>Rozwiązania odnalezione

Security Center automatycznie wykrywa rozwiązania zabezpieczeń działające na platformie Azure, ale nie połączyły się z Security Center i wyświetla rozwiązania w sekcji **odnalezione rozwiązania** . Rozwiązania te obejmują rozwiązania platformy Azure, takie jak [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)i rozwiązania partnerskie.

> [!NOTE]
> Funkcja odnalezionych rozwiązań wymaga usługi Security Center w warstwie Standardowa na poziomie subskrypcji. Zobacz [Cennik](security-center-pricing.md) , aby dowiedzieć się więcej o warstwach cenowych.
>
>

Wybierz pozycję **Połącz** w ramach rozwiązania, aby przeprowadzić integrację z usługą Security Center i otrzymywać powiadomienia o alertach zabezpieczeń.

### <a name="add-data-sources"></a>Dodawanie źródeł danych

Sekcja **Dodawanie źródeł danych** obejmuje inne dostępne źródła danych, które mogą zostać połączone. Aby uzyskać instrukcje dotyczące dodawania danych z dowolnego z tych źródeł, kliknij przycisk **DODAJ**.

![Źródła danych](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

## <a name="exporting-data-to-a-siem"></a>Eksportowanie danych do SIEM

> [!NOTE]
> Aby uzyskać szczegółowe informacje na temat prostszej metody (obecnie w wersji zapoznawczej) do eksportowania danych do SIEM, zobacz temat [Eksportowanie alertów zabezpieczeń i zaleceń (wersja zapoznawcza)](continuous-export.md). Nowa metoda nie używa dziennika aktywności jako intermediator i umożliwia bezpośrednie Eksportowanie z Security Center do Event Hubs (a następnie do SIEM), ale również obsługuje eksport zaleceń dotyczących zabezpieczeń.


Można skonfigurować rozwiązań Siem lub inne narzędzia do monitorowania do odbierania zdarzeń Azure Security Center.

Wszystkie zdarzenia z Azure Security Center są publikowane w [dzienniku aktywności](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)platformy Azure Azure monitor. Azure Monitor używa [skonsolidowanego potoku](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) do przesyłania strumieniowego danych do centrum zdarzeń, w którym można następnie ściągnąć do narzędzia monitorowania.

W następnych sekcjach opisano sposób konfigurowania danych przesyłanych strumieniowo do centrum zdarzeń. W tych krokach przyjęto, że masz już Azure Security Center skonfigurowany w ramach subskrypcji platformy Azure.

### <a name="high-level-overview"></a>Ogólne omówienie

![Ogólne omówienie](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Jakie dane zabezpieczeń platformy Azure są ujawniane SIEM?

W tej wersji ujawniamy [alerty zabezpieczeń.](../security-center/security-center-managing-and-responding-alerts.md) W przyszłych wersjach zostanie wzbogacony zestaw danych z zaleceniami dotyczącymi zabezpieczeń.

### <a name="how-to-set-up-the-pipeline"></a>Jak skonfigurować potok

#### <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń

Przed rozpoczęciem [utwórz Event Hubs przestrzeń nazw](../event-hubs/event-hubs-create.md) — miejsce docelowe dla wszystkich danych monitorowania.

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Prześlij strumieniowo dziennik aktywności platformy Azure do Event Hubs

Zapoznaj się z następującym artykułem [Dziennik aktywności w usłudze Stream, aby Event Hubs](../azure-monitor/platform/activity-logs-stream-event-hubs.md)

#### <a name="install-a-partner-siem-connector"></a>Instalowanie łącznika SIEM partnera 

Kierowanie danych monitorowania do centrum zdarzeń za pomocą Azure Monitor pozwala na łatwą integrację z SIEM partnerskim i narzędziami do monitorowania.

Zobacz następujący artykuł, aby zapoznać się z listą [obsługiwanych rozwiązań Siem](../azure-monitor/platform/resource-logs-stream-event-hubs.md#what-you-can-do-with-platform-logs-sent-to-an-event-hub)

### <a name="example-for-querying-data"></a>Przykład na potrzeby wykonywania zapytań dotyczących danych 

Poniżej przedstawiono niektóre zapytania Splunk, których można użyć do ściągania danych alertu:

| **Opis zapytania** | **Zapytanie** |
|----|----|
| Wszystkie alerty| index = Main Microsoft. Security/Locations/Alerts|
| Podsumuj liczbę operacji według ich nazwy| index = Main sourceType = "Amal: Security" \| tabeli OperationName \| wg liczby statystyk według operacji|
| Pobierz informacje o alertach: godzina, nazwa, stan, identyfikator i subskrypcja | index = Main Microsoft. Security/Locations/Alerts \| tabelę \_Time, Properties. EventName, State, Properties. operationId, am_subscriptionId |


## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób zintegrowania rozwiązania partnerskiego w usłudze Security Center. Aby dowiedzieć się więcej na temat usługi Security Center, zobacz następujące artykuły:

* [Monitorowanie kondycji zabezpieczeń w usłudze Security Center](security-center-monitoring.md). Informacje na temat sposobu monitorowania kondycji zasobów platformy Azure.
* [Azure Security Center — często zadawane pytania](security-center-faq.md). Odpowiedzi na często zadawane pytania dotyczące korzystania z usługi Security Center.
* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/). Wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.
