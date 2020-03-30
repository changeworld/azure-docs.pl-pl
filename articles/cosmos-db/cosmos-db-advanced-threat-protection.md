---
title: Zaawansowana ochrona przed zagrożeniami dla usługi Azure Cosmos DB
description: Dowiedz się, jak usługa Azure Cosmos DB zapewnia szyfrowanie danych w spoczynku i jak są implementowane.
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: seodec18
ms.author: memildin
author: memildin
manager: rkarlin
ms.openlocfilehash: bcc1c6ffe7cdec4aed325a67969235ae993a5109
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614831"
---
# <a name="advanced-threat-protection-for-azure-cosmos-db-preview"></a>Zaawansowana ochrona przed zagrożeniami dla usługi Azure Cosmos DB (wersja zapoznawcza)

Zaawansowana ochrona przed zagrożeniami dla usługi Azure Cosmos DB zapewnia dodatkową warstwę analizy zabezpieczeń, która wykrywa nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do kont usługi Azure Cosmos DB lub ich wykorzystania. Ta warstwa ochrony pozwala na rozwiązywanie zagrożeń, nawet bez bycia ekspertem w dziedzinie bezpieczeństwa, i integrowanie ich z centralnymi systemami monitorowania bezpieczeństwa.

Alerty zabezpieczeń są wyzwalane, gdy występują anomalie w działaniu. Te alerty zabezpieczeń są zintegrowane z [usługą Azure Security Center](https://azure.microsoft.com/services/security-center/)i są również wysyłane za pośrednictwem poczty e-mail do administratorów subskrypcji, ze szczegółami podejrzanej aktywności i zaleceniami dotyczącymi sposobu badania i korygować zagrożenia.

> [!NOTE]
>
> * Zaawansowana ochrona przed zagrożeniami dla usługi Azure Cosmos DB jest obecnie dostępna tylko dla interfejsu API SQL.
> * Zaawansowana ochrona przed zagrożeniami dla usługi Azure Cosmos DB nie jest obecnie dostępna w środowiskach rządowych platformy Azure i suwerennych regionach chmury.

Aby uzyskać pełne środowisko badania alertów zabezpieczeń, zalecono włączenie [rejestrowania diagnostycznego w usłudze Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/logging), która rejestruje operacje w samej bazie danych, w tym operacje CRUD na wszystkich dokumentach, kontenerach i bazach danych.

## <a name="threat-types"></a>Typy zagrożeń

Zaawansowana ochrona przed zagrożeniami dla usługi Azure Cosmos DB wykrywa nietypowe działania wskazujące na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania. Obecnie może wyzwolić następujące alerty:

- **Dostęp z nietypowych lokalizacji:** Ten alert jest wyzwalany, gdy nastąpi zmiana wzorca dostępu do konta usługi Azure Cosmos, gdzie ktoś ma połączenie z punktem końcowym usługi Azure Cosmos DB z nietypowej lokalizacji geograficznej. W niektórych przypadkach alert wykrywa prawidłowe działanie, co oznacza nową aplikację lub operację konserwacji dewelopera. W innych przypadkach alert wykrywa złośliwą akcję od byłego pracownika, zewnętrznego atakującego itp.

- **Nietypowe wyodrębnianie danych:** Ten alert jest wyzwalany, gdy klient wyodrębnia niezwykłą ilość danych z konta usługi Azure Cosmos DB. Może to być objawem niektórych eksfiltracji danych wykonywane w celu przeniesienia wszystkich danych przechowywanych na koncie do zewnętrznego magazynu danych.

## <a name="set-up-advanced-threat-protection"></a>Konfigurowanie zaawansowanej ochrony przed zagrożeniami

### <a name="set-up-atp-using-the-portal"></a>Konfigurowanie narzędzia ATP za pomocą portalu

1. Uruchom portal Platformy [https://portal.azure.com](https://portal.azure.com/)Azure pod adresem .

2. Z konta usługi Azure Cosmos DB z menu **Ustawienia** wybierz polecenie **Zaawansowane zabezpieczenia**.

    ![Konfigurowanie atp](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp.png)

3. W bloku **konfiguracja zabezpieczeń zaawansowanej:**

    * Kliknij opcję **Zaawansowana ochrona przed zagrożeniami,** aby ustawić ją **na WŁ.**
    * Kliknij przycisk **Zapisz**, aby zapisać nowe lub zaktualizowane zasady usługi Advanced Threat Protection.   

### <a name="set-up-atp-using-rest-api"></a>Konfigurowanie narzędzia ATP przy użyciu interfejsu API REST

Polecenia interfejsu API rest umożliwia tworzenie, aktualizowanie lub uzyskanie ustawienia Zaawansowanej ochrony przed zagrożeniami dla określonego konta usługi Azure Cosmos DB.

* [Zaawansowana ochrona przed zagrożeniami — tworzenie](https://go.microsoft.com/fwlink/?linkid=2099745)
* [Zaawansowana ochrona przed zagrożeniami - Pobierz](https://go.microsoft.com/fwlink/?linkid=2099643)

### <a name="set-up-atp-using-azure-powershell"></a>Konfigurowanie narzędzia ATP przy użyciu programu Azure PowerShell

Użyj następujących poleceń cmdlet programu PowerShell:

* [Włączanie zaawansowanej ochrony przed zagrożeniami](https://go.microsoft.com/fwlink/?linkid=2099607&clcid=0x409)
* [Uzyskaj zaawansowaną ochronę przed zagrożeniami](https://go.microsoft.com/fwlink/?linkid=2099608&clcid=0x409)
* [Wyłącz zaawansowaną ochronę przed zagrożeniami](https://go.microsoft.com/fwlink/?linkid=2099709&clcid=0x409)

### <a name="using-azure-resource-manager-templates"></a>Używanie szablonów usługi Azure Resource Manager

Użyj szablonu usługi Azure Resource Manager, aby skonfigurować usługę Cosmos DB z włączoną zaawansowaną ochroną przed zagrożeniami.
Aby uzyskać więcej informacji, zobacz [Tworzenie konta usługi CosmosDB z zaawansowaną ochroną przed zagrożeniami](https://azure.microsoft.com/resources/templates/201-cosmosdb-advanced-threat-protection-create-account/).

### <a name="using-azure-policy"></a>Korzystanie z zasad platformy Azure

Użyj zasad platformy Azure, aby włączyć zaawansowaną ochronę przed zagrożeniami dla usługi Cosmos DB.

1. Uruchom stronę Zasady platformy Azure **— definicje** i wyszukaj zasady **Wdrażanie zaawansowanej ochrony przed zagrożeniami dla usługi Cosmos DB.**

    ![Zasady wyszukiwania](./media/cosmos-db-advanced-threat-protection/cosmos-db.png) 

1. Kliknij zasadę **Wdrażanie zaawansowanej ochrony przed zagrożeniami dla usługi CosmosDB,** a następnie kliknij przycisk **Przypisz**.

    ![Wybierz subskrypcję lub grupę](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-policy.png)


1. W polu **Zakres** kliknij trzy kropki, wybierz subskrypcję platformy Azure lub grupę zasobów, a następnie kliknij przycisk **Wybierz**.

    ![Strona definicje zasad](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-details.png)


1. Wprowadź inne parametry i kliknij przycisk **Przypisz**.

## <a name="manage-atp-security-alerts"></a>Zarządzanie alertami zabezpieczeń ATP

Gdy wystąpią anomalie działania usługi Azure Cosmos DB, alert zabezpieczeń jest wyzwalany z informacjami o podejrzanym zdarzeniu zabezpieczeń. 

 W usłudze Azure Security Center można przeglądać [bieżące alerty zabezpieczeń](../security-center/security-center-alerts-overview.md)i zarządzać nimi.  Kliknij określony alert w [Centrum zabezpieczeń,](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/0) aby wyświetlić możliwe przyczyny i zalecane działania w celu zbadania i złagodzenia potencjalnego zagrożenia. Na poniższej ilustracji przedstawiono przykład szczegółów alertu podanych w u centrum zabezpieczeń.

 ![Szczegóły zagrożenia](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert-details.png)

Wysyłane jest również powiadomienie e-mail ze szczegółami alertu i zalecanymi działaniami. Na poniższej ilustracji przedstawiono przykład wiadomości e-mail z alertem.

 ![Szczegóły alertu](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert.png)

## <a name="cosmos-db-atp-alerts"></a>Alerty COSMOS DB ATP

 Aby wyświetlić listę alertów generowanych podczas monitorowania kont usługi Azure Cosmos DB, zobacz sekcję [alertów usługi Cosmos DB](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos) w dokumentacji usługi Azure Security Center.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [rejestrowaniu diagnostycznym w usłudze Azure Cosmos DB](cosmosdb-monitor-resource-logs.md)
* Dowiedz się więcej o [usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
