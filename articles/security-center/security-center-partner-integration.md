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
ms.openlocfilehash: 2c43a3c046243c4802fb433f3b755cee158002b6
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950557"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integracja rozwiązań zabezpieczeń w usłudze Azure Security Center
Ten dokument ułatwia zarządzanie rozwiązaniami zabezpieczeń już połączonymi z usługą Azure Security Center i dodawanie nowych.

> [!NOTE]
> Podzestaw rozwiązań zabezpieczeń został wycofany 31 lipca 2019. Aby uzyskać więcej informacji i alternatywnych usług, zobacz wycofywanie [funkcji Security Center (lipiec 2019)](security-center-features-retirement-july2019.md#menu_solutions).

## <a name="integrated-azure-security-solutions"></a>Zintegrowane rozwiązania zabezpieczeń platformy Azure
Usługa Security Center ułatwia włączanie zintegrowanych rozwiązań zabezpieczeń na platformie Azure. Korzyści to:

- **Uproszczone wdrażanie**: Security Center oferuje ulepszoną obsługę zintegrowanych rozwiązań partnerskich. W przypadku rozwiązań takich jak ocena ochrony przed złośliwym oprogramowaniem i luk w zabezpieczeniach usługa Security Center może aprowizować wymaganego agenta na Twoich maszynach wirtualnych, zaś dla urządzeń zapory usługa Security Center określa większość wymaganej konfiguracji sieci.
- **Zintegrowane wykrywanie**: Zdarzenia zabezpieczeń z rozwiązań partnerskich są automatycznie zbierane, agregowane i wyświetlane w ramach zdarzeń i alertów usługi Security Center. Te zdarzenia są także połączone z funkcjami wykrywania z innych źródeł, aby zapewnić zaawansowane możliwości w zakresie wykrywania zagrożeń.
- **Ujednolicone monitorowanie kondycji i zarządzanie nimi**: Klienci mogą używać zintegrowanych zdarzeń kondycji w celu jednoczesnego monitorowania wszystkich rozwiązań partnerskich. Podstawowe funkcje zarządzania zapewniają łatwy dostęp do konfiguracji zaawansowanej przy użyciu rozwiązania partnerskiego.

Obecnie zintegrowane rozwiązania zabezpieczeń obejmują ocenę luk w zabezpieczeniach przez [Qualys](https://www.qualys.com/public-clouds/microsoft-azure/) i [Rapid7](https://www.rapid7.com/products/insightvm/) oraz zaporę aplikacji sieci Web firmy Microsoft Application Gateway.

> [!NOTE]
> Usługa Security Center nie może zainstalować programu Microsoft Monitoring Agent na wirtualnych urządzeniach partnera, ponieważ większość dostawców zabezpieczeń zabrania korzystania z zewnętrznych agentów na swoich urządzeniach.
>
>

## <a name="how-security-solutions-are-integrated"></a>Jak są integrowane rozwiązania zabezpieczeń
Rozwiązania zabezpieczeń platformy Azure, które zostały wdrożone z usługi Security Center, są automatycznie połączone. Można także połączyć inne źródła danych zabezpieczeń, w tym komputery działające lokalnie lub w innych chmurach.

![Integracja rozwiązań partnerskich](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Zarządzanie zintegrowanymi rozwiązaniami zabezpieczeń platformy Azure i innymi źródłami danych

1. Zaloguj się w witrynie [Azure Portal](https://azure.microsoft.com/features/azure-portal/).

2. W **menu platformy Microsoft Azure** wybierz pozycję **Security Center**. Zostanie otwarte okno **Security Center — Przegląd**.

3. W menu usługi Security Center wybierz pozycję **Rozwiązania zabezpieczeń**.

   ![Security Center — Przegląd](./media/security-center-partner-integration/overview.png)

W obszarze **Rozwiązania w zakresie bezpieczeństwa** możesz wyświetlić informacje o kondycji zintegrowanych rozwiązań zabezpieczeń platformy Azure i wykonać podstawowe zadania zarządzania.

### <a name="connected-solutions"></a>Rozwiązania połączone

Sekcja **Połączone rozwiązania** zawiera rozwiązania zabezpieczeń, które są aktualnie połączone z usługą Security Center, i informacje o stanie kondycji poszczególnych rozwiązań.  

![Rozwiązania połączone](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

Stanem rozwiązania partnerskiego może być:

* Dobra kondycja (zielony) — nie występuje problem z kondycją.
* W złej kondycji (kolor czerwony) — istnieje problem z kondycją wymagający natychmiastowej uwagi.
* Problemy z kondycją (pomarańczowy) — rozwiązanie przestało zgłaszać swoją kondycję.
* Nie zgłoszono (szare) — rozwiązanie nie zgłosiło jeszcze żadnych informacji. stan rozwiązania może być nieraportowany, jeśli został on ostatnio połączony i nadal jest wdrażany lub żadne dane kondycji nie są dostępne.

> [!NOTE]
> Jeśli dane stanu kondycji są niedostępne, Security Center pokazuje datę i godzinę ostatniego odebranego zdarzenia, aby wskazać, czy rozwiązanie zgłasza, czy nie. Jeśli dane dotyczące kondycji nie są dostępne, a w ciągu ostatnich 14 dni nie odebrano żadnych alertów, Security Center wskazuje, że rozwiązanie jest w złej kondycji lub nie jest raportowane.
>
>

1. Wybierz opcję **Widok** , aby uzyskać dodatkowe informacje i opcje, w tym:

   - **Konsola rozwiązania**. Otwiera środowisko zarządzania dla tego rozwiązania.
   - **Połącz maszynę wirtualną**. Otwiera blok łączenie aplikacji. W tym miejscu możesz połączyć zasoby z rozwiązaniem partnerskim.
   - **Usuń rozwiązanie**.
   - **Skonfiguruj**.

   ![Szczegóły rozwiązania partnerskiego](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>Odnalezione rozwiązania

Usługa Security Center automatycznie odnajduje rozwiązania w zakresie zabezpieczeń działające na platformie Azure, ale niepołączone z usługą Security Center, i wyświetla rozwiązania w sekcji **Rozwiązania odnalezione**. Obejmuje to rozwiązania platformy Azure, takie jak [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection), jak również rozwiązania partnerskie.

> [!NOTE]
> Funkcja odnalezionych rozwiązań wymaga usługi Security Center w warstwie Standardowa na poziomie subskrypcji. Zobacz [cennik](security-center-pricing.md), aby dowiedzieć się więcej na temat warstw cenowych zabezpieczeń.
>
>

Wybierz pozycję **POŁĄCZ** w obszarze rozwiązania, aby zintegrować je z usługą Security Center i otrzymywać powiadomienia o alertach zabezpieczeń.

![Odnalezione rozwiązania](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

### <a name="add-data-sources"></a>Dodawanie źródeł danych

Sekcja **Dodawanie źródeł danych** obejmuje inne dostępne źródła danych, które mogą zostać połączone. Aby uzyskać instrukcje dotyczące dodawania danych z dowolnego z tych źródeł, kliknij przycisk **DODAJ**.

![Źródła danych](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

## <a name="exporting-data-to-a-siem"></a>Eksportowanie danych do SIEM

Przetwarzane zdarzenia tworzone przez Azure Security Center są publikowane w [dzienniku aktywności](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)platformy Azure, jeden z typów dzienników dostępnych za pomocą Azure monitor. Azure Monitor oferuje skonsolidowany potok do routingu dowolnych danych monitorowania do narzędzia SIEM. Jest to realizowane przez przesyłanie strumieniowe danych do centrum zdarzeń, w którym można następnie ściągnąć je do narzędzia partnerskiego.

Ten potok używa [pojedynczego potoku monitorowania platformy Azure](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) do uzyskiwania dostępu do danych monitorowania ze środowiska platformy Azure. Dzięki temu można łatwo konfigurować narzędzia rozwiązań Siem i monitorowania w celu korzystania z danych.

W następnych sekcjach opisano sposób konfigurowania danych przesyłanych strumieniowo do centrum zdarzeń. W tych krokach przyjęto, że masz już Azure Security Center skonfigurowany w ramach subskrypcji platformy Azure.

Ogólne omówienie

![Ogólne omówienie](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Jakie dane zabezpieczeń platformy Azure są ujawniane SIEM?

W tej wersji ujawniamy [alerty zabezpieczeń.](../security-center/security-center-managing-and-responding-alerts.md) W przyszłych wersjach zostanie wzbogacony zestaw danych z zaleceniami dotyczącymi zabezpieczeń.

### <a name="how-to-setup-the-pipeline"></a>Jak skonfigurować potok

#### <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń

Przed rozpoczęciem należy [utworzyć Event Hubs przestrzeni nazw](../event-hubs/event-hubs-create.md). Ta przestrzeń nazw i centrum zdarzeń są lokalizacją docelową dla wszystkich danych monitorowania.

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Prześlij strumieniowo dziennik aktywności platformy Azure do Event Hubs

Zapoznaj się z następującym artykułem [Dziennik aktywności w usłudze Stream, aby Event Hubs](../azure-monitor/platform/activity-logs-stream-event-hubs.md)

#### <a name="install-a-partner-siem-connector"></a>Instalowanie łącznika SIEM partnera 

Kierowanie danych monitorowania do centrum zdarzeń za pomocą Azure Monitor pozwala na łatwą integrację z SIEM partnerskim i narzędziami do monitorowania.

Skorzystaj z następującego linku, aby wyświetlić listę [obsługiwanych rozwiązań Siem](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)

### <a name="example-for-querying-data"></a>Przykład na potrzeby wykonywania zapytań dotyczących danych 

Oto kilka zapytań Splunk, których można użyć do ściągania danych alertu:

| **Opis zapytania** | **Zapytanie** |
|----|----|
| Wszystkie alerty| index = Main Microsoft. Security/Locations/Alerts|
| Podsumuj liczbę operacji według ich nazwy| index = Main sourceType = "Amal: Security" \| — \| Statystyka operacji tabeli countname według OperationName|
| Pobierz informacje o alertach: Godzina, nazwa, stan, identyfikator i subskrypcja | index = Main Microsoft. Security/Locations/ \| Alerts tabela \_czas, właściwości. EventName, State, Properties. operationId, am_subscriptionId |


## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób zintegrowania rozwiązania partnerskiego w usłudze Security Center. Aby dowiedzieć się więcej na temat usługi Security Center, zobacz następujące artykuły:

* [Monitorowanie kondycji zabezpieczeń w usłudze Security Center](security-center-monitoring.md). Informacje na temat sposobu monitorowania kondycji zasobów platformy Azure.
* [Azure Security Center — często zadawane pytania](security-center-faq.md). Odpowiedzi na często zadawane pytania dotyczące korzystania z usługi Security Center.
* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/). Wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.
