---
title: Integracja rozwiązań zabezpieczeń w usłudze Azure Security Center | Microsoft Docs
description: Poznaj sposób integracji usługi Azure Security Center z partnerami w celu poprawy ogólnego stanu zabezpieczeń zasobów platformy Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2019
ms.author: rkarlin
ms.openlocfilehash: d94567800a9fd020784c9cb07b2c6824cd032509
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67064270"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integracja rozwiązań zabezpieczeń w usłudze Azure Security Center
Ten dokument ułatwia zarządzanie rozwiązaniami zabezpieczeń już połączonymi z usługą Azure Security Center i dodawanie nowych.

> [!NOTE]
> Podzbiór rozwiązania w zakresie bezpieczeństwa zostaną wycofane z dniem 31 lipca 2019 r. Aby uzyskać więcej informacji i alternatywne usług zobacz [wycofywania Centrum zabezpieczeń funkcji (2019 lipca)](security-center-features-retirement-july2019.md#menu_solutions).

## <a name="integrated-azure-security-solutions"></a>Zintegrowane rozwiązania zabezpieczeń platformy Azure
Usługa Security Center ułatwia włączanie zintegrowanych rozwiązań zabezpieczeń na platformie Azure. Korzyści to:

- **Uproszczone wdrażanie**: Usługa Security Center oferuje udoskonaloną aprowizację zintegrowanych rozwiązań partnerskich. W przypadku rozwiązań takich jak ocena ochrony przed złośliwym oprogramowaniem i luk w zabezpieczeniach usługa Security Center może aprowizować wymaganego agenta na Twoich maszynach wirtualnych, zaś dla urządzeń zapory usługa Security Center określa większość wymaganej konfiguracji sieci.
- **Zintegrowane funkcje wykrywania**: Zdarzenia zabezpieczeń z rozwiązań partnerskich są automatycznie zbierane, agregowane i wyświetlane w ramach zdarzeń i alertów usługi Security Center. Te zdarzenia są także połączone z funkcjami wykrywania z innych źródeł, aby zapewnić zaawansowane możliwości w zakresie wykrywania zagrożeń.
- **Ujednolicone zarządzanie monitorowaniem kondycji**: Klienci mogą używać zintegrowanych zdarzeń kondycji w celu jednoczesnego monitorowania wszystkich rozwiązań partnerskich. Podstawowe funkcje zarządzania zapewniają łatwy dostęp do konfiguracji zaawansowanej przy użyciu rozwiązania partnerskiego.

Obecnie zintegrowane rozwiązania zabezpieczeń obejmują oceny luk w zabezpieczeniach przez [Qualys](https://www.qualys.com/public-clouds/microsoft-azure/) i [Rapid7](https://www.rapid7.com/products/insightvm/) i zapory aplikacji sieci Web bramy aplikacji firmy Microsoft.

> [!NOTE]
> Usługa Security Center nie może zainstalować programu Microsoft Monitoring Agent na wirtualnych urządzeniach partnera, ponieważ większość dostawców zabezpieczeń zabrania korzystania z zewnętrznych agentów na swoich urządzeniach.
>
>

## <a name="how-security-solutions-are-integrated"></a>Jak są integrowane rozwiązania zabezpieczeń
Rozwiązania zabezpieczeń platformy Azure, które zostały wdrożone z usługi Security Center, są automatycznie połączone. Można też połączyć inne źródła danych zabezpieczeń, w tym komputerami z systemami w środowisku lokalnym lub w innych chmurach.

![Integracja rozwiązań partnerskich](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Zarządzanie zintegrowanymi rozwiązaniami zabezpieczeń platformy Azure i innymi źródłami danych

1. Zaloguj się w witrynie [Azure Portal](https://azure.microsoft.com/features/azure-portal/).

2. W **menu platformy Microsoft Azure** wybierz pozycję **Security Center**. Zostanie otwarte okno **Security Center — Przegląd**.

3. W menu usługi Security Center wybierz pozycję **Rozwiązania zabezpieczeń**.

   ![Security Center — Przegląd](./media/security-center-partner-integration/overview.png)

W obszarze **Rozwiązania w zakresie bezpieczeństwa** możesz wyświetlić informacje o kondycji zintegrowanych rozwiązań zabezpieczeń platformy Azure i wykonać podstawowe zadania zarządzania. Możesz też połączyć inne typy źródeł danych zabezpieczeń, takie jak alerty usługi Azure Active Directory Identity Protection, i dzienniki zapory w formacie CEF.

### <a name="connected-solutions"></a>Rozwiązania połączone

Sekcja **Połączone rozwiązania** zawiera rozwiązania zabezpieczeń, które są aktualnie połączone z usługą Security Center, i informacje o stanie kondycji poszczególnych rozwiązań.  

![Rozwiązania połączone](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

Stan rozwiązania partnerskiego można:

* Zdrowy (zielony) — Brak problemów dotyczących kondycji.
* W złej kondycji (kolor czerwony) — istnieje problem z kondycją wymagający natychmiastowej uwagi.
* Problemy dotyczące kondycji (kolor pomarańczowy) — rozwiązania zostało zatrzymane raportowanie dotyczące kondycji.
* Niezgłoszone (kolor szary) — rozwiązanie nie zgłosiło niczego jeszcze, stan rozwiązania może zostać zgłoszony, jeśli został ostatnio połączony i jego wdrażanie nadal trwa lub nie są dostępne żadne dane kondycji.

> [!NOTE]
> Jeśli dane o stanie kondycji nie jest dostępna, usługa Security Center Pokazuje datę i godzinę ostatniego zdarzenia odebrana, aby wskazać, czy rozwiązanie jest raportowanie. Jeśli nie są dostępne żadne dane kondycji i alerty nie są odbierane w ciągu ostatnich 14 dni, usługa Security Center wskazuje, że rozwiązanie jest nieprawidłowy lub nie raportowania.
>
>

1. Wybierz **WIDOKU** dodatkowe informacje i opcji, która obejmuje:

   - **Konsola rozwiązań**. Otwiera środowisko zarządzania dla tego rozwiązania.
   - **Łączenie maszyny Wirtualnej**. Zostanie otwarty blok łączenie aplikacji. W tym miejscu możesz połączyć zasoby z rozwiązaniem partnerskim.
   - **Usuń rozwiązanie**.
   - **Konfigurowanie**.

   ![Szczegóły rozwiązania partnerskiego](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>Rozwiązania odnalezione

Usługa Security Center automatycznie odnajduje rozwiązania w zakresie zabezpieczeń działające na platformie Azure, ale niepołączone z usługą Security Center, i wyświetla rozwiązania w sekcji **Rozwiązania odnalezione**. Obejmuje to rozwiązania platformy Azure, takie jak [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection), jak również rozwiązania partnerskie.

> [!NOTE]
> Funkcja odnalezionych rozwiązań wymaga usługi Security Center w warstwie Standardowa na poziomie subskrypcji. Zobacz [cennik](security-center-pricing.md), aby dowiedzieć się więcej na temat warstw cenowych zabezpieczeń.
>
>

Wybierz pozycję **POŁĄCZ** w obszarze rozwiązania, aby zintegrować je z usługą Security Center i otrzymywać powiadomienia o alertach zabezpieczeń.

![Rozwiązania odnalezione](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

Usługa Security Center odnajduje również rozwiązania wdrożone w ramach subskrypcji, które mogą przekazywać dalej Dzienniki w formacie Common Event Format (CEF). Dowiedz się, jak [połączyć rozwiązanie w zakresie zabezpieczeń](quick-security-solutions.md), które używa dzienników CEF, z usługą Security Center.

### <a name="add-data-sources"></a>Dodawanie źródeł danych

Sekcja **Dodawanie źródeł danych** obejmuje inne dostępne źródła danych, które mogą zostać połączone. Aby uzyskać instrukcje dotyczące dodawania danych z dowolnego z tych źródeł, kliknij przycisk **DODAJ**.

![Źródła danych](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

## <a name="exporting-data-to-a-siem"></a>Eksportowanie danych do rozwiązania SIEM

Przetworzone zdarzenia generowane przez usługę Azure Security Center są publikowane na platformie Azure [dziennika aktywności](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), jeden z dziennika typów dostępnych za pośrednictwem usługi Azure Monitor. Usługa Azure Monitor udostępnia skonsolidowany potoku na potrzeby routingu jakichkolwiek danych monitorowania do narzędzia SIEM. Polega to na przesyłanie strumieniowe danych do Centrum zdarzeń, gdzie go może następnie zostać pobrane do narzędzia partnera.

Ten potok używa [monitorowania platformy Azure w jeden potok](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) w celu uzyskania dostępu do danych monitorowania ze środowiska platformy Azure. Dzięki temu można łatwo skonfigurować rozwiązań Siem i narzędzi do monitorowania, korzystającą z danych.

W kolejnych sekcjach opisano, jak można skonfigurować dane przesyłane strumieniowo do Centrum zdarzeń. W krokach założono, że masz już usługę Azure Security Center skonfigurowane w ramach subskrypcji platformy Azure.

Ogólne omówienie

![Ogólne omówienie](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Co to jest udostępniane do rozwiązania SIEM dane zabezpieczeń platformy Azure?

W tej wersji uwidaczniamy [alertów zabezpieczeń.](../security-center/security-center-managing-and-responding-alerts.md) W następnych wersjach firma Microsoft będzie wzbogacić zestawu danych z zaleceniami dotyczącymi zabezpieczeń.

### <a name="how-to-setup-the-pipeline"></a>Jak skonfigurować potok

#### <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń

Przed rozpoczęciem należy [tworzenie przestrzeni nazw usługi Event Hubs](../event-hubs/event-hubs-create.md). Ten obszar nazw i Centrum zdarzeń jest miejsce docelowe dla wszystkich danych monitorowania.

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Stream dziennika aktywności platformy Azure do usługi Event Hubs

Zapoznaj się z następującym artykułem [strumieniowe przesyłanie dzienników aktywności do usługi Event Hubs](../azure-monitor/platform/activity-logs-stream-event-hubs.md)

#### <a name="install-a-partner-siem-connector"></a>Instalowanie łącznika SIEM partnera 

Routing danych monitorowania do Centrum zdarzeń za pomocą usługi Azure Monitor pozwala łatwo zintegrować ją z partnerem rozwiązania SIEM i narzędzi do monitorowania.

Zapoznaj się z następującego linku, aby wyświetlić listę [obsługiwanych rozwiązań Siem](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)

### <a name="example-for-querying-data"></a>Przykład wykonywanie zapytań o dane 

Poniżej przedstawiono kilka zapytań Splunk, które służy do pobierania danych alertu:

| **Opis kwerendy** | **Zapytanie** |
|----|----|
| Wszystkie alerty| Indeks = Microsoft.Security/locations/alerts głównego|
| Podsumowanie łącznej liczby operacji według nazwy| Indeks = głównego sourcetype = "amal: zabezpieczeń" \| operationName tabeli \| statystyki, liczba przez operationName|
| Uzyskaj informacje o alerty: Czas, nazwa, stan, identyfikator i subskrypcji | Indeks = głównego Microsoft.Security/locations/alerts \| tabeli \_czasu, properties.eventName, stan, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób zintegrowania rozwiązania partnerskiego w usłudze Security Center. Aby dowiedzieć się więcej na temat usługi Security Center, zobacz następujące artykuły:

* [Monitorowanie kondycji zabezpieczeń w usłudze Security Center](security-center-monitoring.md). Informacje na temat sposobu monitorowania kondycji zasobów platformy Azure.
* [Azure Security Center — często zadawane pytania](security-center-faq.md). Odpowiedzi na często zadawane pytania dotyczące korzystania z usługi Security Center.
* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/). Wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.
