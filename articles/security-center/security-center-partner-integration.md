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
ms.openlocfilehash: 23a00c766dbb38853c57c91e7f59ec364390c44b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245384"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integracja rozwiązań zabezpieczeń w usłudze Azure Security Center
Ten dokument ułatwia zarządzanie rozwiązaniami zabezpieczeń już połączonymi z usługą Azure Security Center i dodawanie nowych.

> [!NOTE]
> Podzbiór rozwiązań zabezpieczających został wycofany 31 lipca 2019. Aby uzyskać więcej informacji i usług alternatywnych, zobacz [Wycofanie funkcji Centrum zabezpieczeń (lipiec 2019)](security-center-features-retirement-july2019.md#menu_solutions).

## <a name="integrated-azure-security-solutions"></a>Zintegrowane rozwiązania zabezpieczeń platformy Azure
Usługa Security Center ułatwia włączanie zintegrowanych rozwiązań zabezpieczeń na platformie Azure. Korzyści to:

- **Uproszczone wdrażanie**: usługa Security Center oferuje udoskonaloną aprowizację zintegrowanych rozwiązań partnerskich. W przypadku rozwiązań, takich jak ochrona przed złośliwym oprogramowaniem i ocena luk w zabezpieczeniach, usługa Security Center może aprowizować agenta na maszynach wirtualnych. W przypadku urządzeń zapory usługa Security Center może zająć się dużą wymaganą konfiguracją sieci.
- **Zintegrowane wykrywanie:** zdarzenia zabezpieczeń z rozwiązań partnerskich są automatycznie zbierane, agregowane i wyświetlane jako część alertów i incydentów w centrum zabezpieczeń. Te zdarzenia są także połączone z funkcjami wykrywania z innych źródeł, aby zapewnić zaawansowane możliwości w zakresie wykrywania zagrożeń.
- **Ujednolicone zarządzanie monitorowaniem kondycji**: klienci mogą używać zintegrowanych zdarzeń kondycji do błyskawicznego monitorowania wszystkich rozwiązań partnerskich. Podstawowe funkcje zarządzania zapewniają łatwy dostęp do konfiguracji zaawansowanej przy użyciu rozwiązania partnerskiego.

Obecnie zintegrowane rozwiązania zabezpieczające obejmują ocenę luk w zabezpieczeniach przez [qualys](https://www.qualys.com/public-cloud/#azure) i [rapid7](https://www.rapid7.com/products/insightvm/) oraz zaporę aplikacji Microsoft Application Gateway Web.

> [!NOTE]
> Usługa Security Center nie instaluje agenta monitorowania firmy Microsoft na partnerskich urządzeniach wirtualnych, ponieważ większość dostawców zabezpieczeń zabrania agentów zewnętrznych działających na ich urządzeniach.
>
>

## <a name="how-security-solutions-are-integrated"></a>Jak są integrowane rozwiązania zabezpieczeń
Rozwiązania zabezpieczeń platformy Azure, które zostały wdrożone z usługi Security Center, są automatycznie połączone. Można również połączyć inne źródła danych zabezpieczeń, w tym komputery z systemem lokalnym lub w innych chmurach.

![Integracja rozwiązań partnerskich](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Zarządzanie zintegrowanymi rozwiązaniami zabezpieczeń platformy Azure i innymi źródłami danych

1. Zaloguj się do [Portalu Azure](https://azure.microsoft.com/features/azure-portal/).

2. W **menu platformy Microsoft Azure** wybierz pozycję **Security Center**. Zostanie otwarte okno **Security Center — Przegląd**.

3. W menu usługi Security Center wybierz pozycję **Rozwiązania zabezpieczeń**.

   ![Security Center — Przegląd](./media/security-center-partner-integration/overview.png)

W **rozwiązaniach zabezpieczeń**można zobaczyć kondycję zintegrowanych rozwiązań zabezpieczeń platformy Azure i uruchomić podstawowe zadania zarządzania.

### <a name="connected-solutions"></a>Rozwiązania połączone

Sekcja **Rozwiązania połączone** zawiera rozwiązania zabezpieczające, które są obecnie połączone z usługą Security Center. Pokazuje również stan kondycji każdego rozwiązania.  

![Rozwiązania połączone](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

Stan rozwiązania partnerskiego może być:

* Zdrowy (zielony) - brak problemów zdrowotnych.
* Niezdrowe (czerwone) - istnieje problem zdrowotny, który wymaga natychmiastowej uwagi.
* Problemy zdrowotne (pomarańczowy) - rozwiązanie przestało zgłaszać swoje zdrowie.
* Nie zgłoszono (szary) — rozwiązanie nie zgłosiło jeszcze niczego i nie są dostępne żadne dane dotyczące kondycji. Stan rozwiązania może być niezgłoszony, jeśli był niedawno połączony i nadal jest wdrażany.

> [!NOTE]
> Jeśli dane o stanie kondycji nie są dostępne, usługa Security Center pokazuje datę i godzinę ostatniego odebranego zdarzenia, aby wskazać, czy rozwiązanie jest raportowanie, czy nie. Jeśli nie są dostępne żadne dane dotyczące kondycji i w ciągu ostatnich 14 dni nie odebrano żadnych alertów, usługa Security Center wskazuje, że rozwiązanie jest w złej kondycji lub nie jest raportowane.
>
>

1. Wybierz **widok, aby** uzyskać dodatkowe informacje i opcje, takie jak:

   - **Konsola rozwiązania**. Otwiera środowisko zarządzania dla tego rozwiązania.
   - **Maszyna wirtualna łącza**. Otwiera stronę Połącz aplikacje. W tym miejscu możesz połączyć zasoby z rozwiązaniem partnerskim.
   - **Usuń rozwiązanie**.
   - **Skonfiguruj**.

   ![Szczegóły rozwiązania partnerskiego](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>Rozwiązania odnalezione

Usługa Security Center automatycznie odnajduje rozwiązania zabezpieczeń działające na platformie Azure, ale niesie ze sobą rozwiązania Security Center i wyświetla rozwiązania w sekcji **Odnalezione rozwiązania.** Rozwiązania te obejmują rozwiązania platformy Azure, takie jak [usługa Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)i rozwiązania partnerskie.

> [!NOTE]
> Funkcja odnalezionych rozwiązań wymaga usługi Security Center w warstwie Standardowa na poziomie subskrypcji. Zobacz [Cennik,](security-center-pricing.md) aby dowiedzieć się więcej o warstwach cenowych.
>
>

Wybierz **connect** w ramach rozwiązania, aby zintegrować się z centrum zabezpieczeń i otrzymywać powiadomienia o alertach zabezpieczeń.

### <a name="add-data-sources"></a>Dodawanie źródeł danych

Sekcja **Dodawanie źródeł danych** obejmuje inne dostępne źródła danych, które mogą zostać połączone. Aby uzyskać instrukcje dotyczące dodawania danych z dowolnego z tych źródeł, kliknij przycisk **DODAJ**.

![Źródła danych](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

## <a name="exporting-data-to-a-siem"></a>Eksportowanie danych do SIEM

> [!NOTE]
> Aby uzyskać szczegółowe informacje na temat prostszej metody (obecnie w wersji zapoznawczej) do eksportowania danych do SIEM, zobacz [Eksportowanie alertów i zaleceń dotyczących zabezpieczeń (Wersja zapoznawcza).](continuous-export.md) Nowa metoda nie używa dziennika aktywności jako intermediatora i umożliwia bezpośredni eksport z Centrum zabezpieczeń do centrów zdarzeń (a następnie do siem), obsługuje również eksport zaleceń dotyczących zabezpieczeń.


Można skonfigurować siemie lub inne narzędzia do monitorowania do odbierania zdarzeń usługi Azure Security Center.

Wszystkie zdarzenia z usługi Azure Security Center są publikowane w [dzienniku aktywności usługi](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)Azure Monitor. Usługa Azure Monitor używa [potoku skonsolidowanego](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) do przesyłania strumieniowego danych do Centrum zdarzeń, gdzie można je następnie pobierać do narzędzia do monitorowania.

W następnych sekcjach opisano, jak można skonfigurować dane do przesyłania strumieniowego do centrum zdarzeń. Kroki zakładają, że masz już usługi Azure Security Center skonfigurowane w ramach subskrypcji platformy Azure.

### <a name="high-level-overview"></a>Ogólne omówienie

![Omówienie wysokiego poziomu](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Co to jest dane zabezpieczeń platformy Azure narażone na SIEM?

W tej wersji możemy udostępnić [alerty zabezpieczeń.](../security-center/security-center-managing-and-responding-alerts.md) W nadchodzących wersjach wzbogacimy zestaw danych o zalecenia dotyczące zabezpieczeń.

### <a name="how-to-set-up-the-pipeline"></a>Jak skonfigurować potok

#### <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń

Przed rozpoczęciem [utwórz obszar nazw centrum zdarzeń](../event-hubs/event-hubs-create.md) — miejsce docelowe dla wszystkich danych monitorowania.

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Przesyłanie strumieniowe dziennika aktywności platformy Azure do centrów zdarzeń

Zobacz następujący artykuł [dziennik aktywności strumienia do Centrum zdarzeń](../azure-monitor/platform/activity-logs-stream-event-hubs.md).

#### <a name="install-a-partner-siem-connector"></a>Instalowanie partnerskiego łącznika SIEM 

Routing danych monitorowania do Centrum zdarzeń za pomocą usługi Azure Monitor umożliwia łatwą integrację z siem partnera i narzędzi do monitorowania.

Zobacz poniższy artykuł, aby zapoznać się z [listą obsługiwanych SIEMów](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration).

### <a name="example-for-querying-data"></a>Przykład kwerendy danych 

Oto kilka zapytań splunk, których można użyć do ściągania danych alertów:

| **Opis kwerendy** | **Kwerendy** |
|----|----|
| All Alerts| index=główny microsoft.security/locations/alerts|
| Podsumuj liczbę operacji według ich nazwy| index=main sourcetype="amal:security" \| tabela \| operationName statystyki zliczane według operationName|
| Uzyskaj informacje o alertach: czas, nazwa, stan, identyfikator i subskrypcja | index=main \| Microsoft.Security/locations/alerts \_table time, properties.eventName, State, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób zintegrowania rozwiązania partnerskiego w usłudze Security Center. Aby dowiedzieć się więcej o umyciu Centrum zabezpieczeń, zobacz następujący artykuł:

* [Monitorowanie kondycji zabezpieczeń w usłudze Security Center](security-center-monitoring.md). Informacje na temat sposobu monitorowania kondycji zasobów platformy Azure.