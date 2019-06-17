---
title: Obsługiwane typy zasobów w usłudze Azure Resource Health | Dokumentacja firmy Microsoft
description: Obsługiwane typy zasobów w usłudze Azure Resource health
author: stephbaron
ms.author: stbaron
ms.topic: conceptual
ms.service: service-health
ms.date: 01/29/2019
ms.openlocfilehash: 0f79a1eed044814d6c2e27f4eadb5ba68a47303f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60622287"
---
# <a name="resource-types-and-health-checks-in-azure-resource-health"></a>Typy zasobów i kontrole kondycji w usłudze Azure resource health
Poniżej znajduje się pełna lista wszystkich testów, które są wykonywane w usłudze resource health według typów zasobów.

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers
|Wykonane testy|
|---|
|<ul><li>Jest serwerem pracę?</li><li>Serwer zabrakło pamięci?</li><li>Serwer uruchamiania?</li><li>Odzyskuje serwer</li></ul>|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
|Wykonane testy|
|---|
|<ul><li>Działa usługa Api Management, a następnie uruchomiona?</li></ul>|

## <a name="microsoftcacheredisredis"></a>Microsoft.CacheRedis/Redis
|Wykonane testy|
|---|
|<ul><li>Czy wszystkie węzły w pamięci podręcznej działa?</li><li>Pamięć podręczna jest osiągalna z w obrębie centrum danych?</li><li>Pamięć podręczna osiągnęła maksymalną liczbę połączeń?</li><li> Pamięć podręczna wyczerpało jego dostępnej pamięci? </li><li>Pamięć podręczna występuje duża liczba błędów strony?</li><li>Jest to pamięć podręczna pod dużym obciążeniem?</li></ul>|

## <a name="microsoftcdnprofile"></a>Microsoft.CDN/profile
|Wykonane testy|
|---|
|<ul> <li>Portal dodatkowy jest dostępny dla operacji konfiguracji sieci CDN?</li><li>Czy istnieją problemy ciągłego dostarczania z punktami końcowymi usługi CDN?</li><li>Użytkownicy konfigurację można zmienić ich zasobów sieci CDN?</li><li>Są zmiany w konfiguracji propagowanie zgodnie ze stawką oczekiwanego?</li><li>Użytkownicy, można zarządzać konfiguracji sieci CDN, za pomocą witryny Azure portal, programu PowerShell lub interfejsu API?</li> </ul>|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classiccompute/virtualmachines
|Wykonane testy|
|---|
|<ul><li>Serwer hosta należy została uruchomiona i działa?</li><li>Rozruch systemu operacyjnego hosta zakończonych?</li><li>Są aprowizowane kontenera maszyny wirtualnej i włączone?</li><li>Czy istnieje połączenie sieciowe między hostem i konto magazynu?</li><li>Rozruch systemu operacyjnego gościa zakończonych?</li><li>Czy istnieje planowanej konserwacji?</li></ul>|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.cognitiveservices/accounts
|Wykonane testy|
|---|
|<ul><li>To konto jest osiągalna z w obrębie centrum danych?</li><li>Dostawca zasobów usługi Cognitive Services jest dostępna?</li><li>Cognitive Service jest dostępna w regionie odpowiednie?</li><li>Może odczytywać można wykonać operacji na koncie magazynu, zawierający metadane zasobu?</li><li>Został osiągnięty limit przydziału wywołania interfejsu API?</li><li>Wywołanie interfejsu API odczytu limit zostanie osiągnięty?</li></ul>|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.compute/virtualmachines
|Wykonane testy|
|---|
|<ul><li>Ma serwera hostingu tej maszyny wirtualnej w górę i uruchomiona</li><li>Rozruch systemu operacyjnego hosta zakończonych?</li><li>Są aprowizowane kontenera maszyny wirtualnej i włączone?</li><li>Czy istnieje połączenie sieciowe między hostem i konto magazynu?</li><li>Rozruch systemu operacyjnego gościa zakończonych?</li><li>Czy istnieje planowanej konserwacji?</li></ul>|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.datafactory/factories
|Wykonane testy|
|---|
|<ul><li>Zostały niepowodzenia uruchomienia potoku?</li><li>Klaster hostuje usługi Data Factory dobrej kondycji?</li></ul>|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.datalakeanalytics/accounts
|Wykonane testy|
|---|
|<ul><li>Masz problemy doświadczonym użytkownikom przesyłania lub wyświetlania listy zadań usługi Data Lake Analytics?</li><li>Są w stanie ukończyć z powodu błędów systemowych zadań usługi Data Lake Analytics?</li></ul>|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.datalakestore/accounts
|Wykonane testy|
|---|
|<ul><li>Użytkownicy wystąpiły problemy z przekazywaniem danych do usługi Data Lake Store?</li><li>Użytkownicy wystąpiły problemy, pobieranie danych z programu Data Lake Store?</li></ul>|

## <a name="microsoftdatamigrationservices"></a>Microsoft.datamigration/services
|Wykonane testy|
|---|
|<ul><li>Usługa migracji bazy danych nie może aprowizować?</li><li>Usługa migracji bazy danych zatrzymał ze względu na nieaktywności lub żądania użytkownika?</li></ul>|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers
|Wykonane testy|
|---|
|<ul><li>Serwer jest niedostępny z powodu konserwacji?</li><li>Serwer jest niedostępny z powodu ponownej konfiguracji?</li></ul>|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers
|Wykonane testy|
|---|
|<ul><li>Serwer jest niedostępny z powodu konserwacji?</li><li>Serwer jest niedostępny z powodu ponownej konfiguracji?</li></ul>|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers
|Wykonane testy|
|---|
|<ul><li>Serwer jest niedostępny z powodu konserwacji?</li><li>Serwer jest niedostępny z powodu ponownej konfiguracji?</li></ul>|

## <a name="microsoftdevicesiothubs"></a>Microsoft.devices/iothubs
|Wykonane testy|
|---|
|<ul><li>Jest usługa IoT hub działa?</li></ul>|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.documentdb/databaseAccounts
|Wykonane testy|
|---|
|<ul><li>Zostały wszystkie żądania bazy danych lub kolekcji nie jest obsługiwany z powodu niedostępności usługi Azure Cosmos DB?</li><li>Zostały wszystkie żądania dokumentu nie jest obsługiwany z powodu niedostępności usługi Azure Cosmos DB?</li></ul>|

## <a name="microsofteventhubnamespaces"></a>Microsoft.eventhub/Namespaces
|Wykonane testy|
|---|
|<ul><li>Przestrzeń nazw usługi Event Hubs występują błędy wygenerowane przez użytkowników?</li><li>Jest przestrzeń nazw usługi Event Hubs jest obecnie uaktualniana?</li></ul>|

## <a name="microsofthdinsightclusters"></a>Microsoft.hdinsight/clusters
|Wykonane testy|
|---|
|<ul><li>Dostępne są również podstawowe usługi w klastrze HDInsight?</li><li>Klaster HDInsight dostęp klucz dla funkcji BYOK szyfrowanie w spoczynku?</li></ul>|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults
|Wykonane testy|
|---|
|<ul><li>Czy żądania do usługi key vault kończy się niepowodzeniem z powodu problemów z platformy Azure KeyVault?</li><li>Są ograniczane ze względu na zbyt wiele żądań podjęte przez klienta żądania do usługi key vault?</li></ul>|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.network/applicationgateways
|Wykonane testy|
|---|
|<ul><li>Czy wydajność bramy Application Gateway, negatywny wpływ na dostępność?</li><li>Application Gateway jest dostępny?</li></ul>|

## <a name="microsoftnetworkconnections"></a>Microsoft.network/connections
|Wykonane testy|
|---|
|<ul><li>Tunel VPN jest podłączony?</li><li>Czy istnieją konflikty konfiguracji połączenia?</li><li>Klucze wstępne prawidłowo skonfigurowano?</li><li>Lokalne urządzenie sieci VPN jest dostępny?</li><li>Zasady zabezpieczeń protokołu IPSec/IKE są niezgodności?</li><li>Czy połączenia sieci VPN S2S poprawnie udostępniane w stanie niepowodzenia?</li><li>Czy połączenia sieć wirtualna-sieć wirtualna poprawnie udostępniane w stanie niepowodzenia?</li></ul>|

## <a name="microsoftnetworkexpressreoutecircuits"></a>Microsoft.network/expressreoutecircuits
|Wykonane testy|
|---|
|<ul><li>Obwód usługi ExpressRoute jest w dobrej kondycji?</li></ul>|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.network/frontdoors
|Wykonane testy|
|---|
|<ul><li>Drzwiami frontowymi zaplecza odpowiadają z błędami na sond kondycji?</li><li>Zmiany konfiguracji są opóźnione?</li></ul>|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.network/virtualNetworkGateways
|Wykonane testy|
|---|
|<ul><li>Bramy sieci VPN jest dostępny z Internetu?</li><li>Bramy sieci VPN jest w trybie rezerwy?</li><li>Usługa VPN działa w ramach bramy?</li></ul>|

## <a name="microsoftnotificationhubsnamespace"></a>Microsoft.NotificationHubs/namespace
|Wykonane testy|
|---|
|<ul><li>Można wykonać operacji środowiska uruchomieniowego, takich jak rejestracji, instalacji lub wysyłania w przestrzeni nazw?</li></ul>|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.operationalinsights/workspaces
|Wykonane testy|
|---|
|<ul><li>Istnieją indeksowania opóźnienia dla obszaru roboczego?</li></ul>|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/Capacities
|Wykonane testy|
|---|
|<ul><li>Jest zasobem pojemności pracę?</li><li>Czy wszystkie obciążenia działa?</li></ul>|

## <a name="microsoftpowerbiworkspacecollections"></a>Microsoft.PowerBI/workspaceCollections
|Wykonane testy|
|---|
|<ul><li>Jest system operacyjny hosta działa?</li><li>WorkspaceCollection jest osiągalny z poza centrum danych?</li><li>Dostawca zasobów usługi Power BI jest dostępny?</li><li>Usługa Power BI jest dostępna w regionie odpowiednie?</li></ul>|

## <a name="microsoftsearchsearchservices"></a>Microsoft.search/searchServices
|Wykonane testy|
|---|
|<ul><li>Można wykonać operacji diagnostyki w klastrze?</li></ul>|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces
|Wykonane testy|
|---|
|<ul><li>Klienci występują błędy usługi Service Bus wygenerowane przez użytkowników?</li><li>Użytkownicy występują wzrost liczby błędów przejściowych, z powodu uaktualnienia przestrzeni nazw usługi Service Bus?</li></ul>|

## <a name="microsoftsqlserverdatabase"></a>Microsoft.SQL/Server/database
|Wykonane testy|
|---|
|<ul><li> Zostały logowania do bazy danych?</li></ul>|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts
|Wykonane testy|
|---|
|<ul><li>Czy żądania odczytu danych z konta magazynu kończy się niepowodzeniem z powodu problemów z platformy Azure Storage?</li><li>Czy żądania zapisu danych do konta magazynu kończy się niepowodzeniem z powodu problemów z platformy Azure Storage?</li><li>Klaster magazynu, w której znajduje się konto magazynu jest niedostępne?</li></ul>|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs
|Wykonane testy|
|---|
|<ul><li>Czy wszystkie hosty, których wykonanie się i uruchamianie zadania?</li><li>Nie można uruchomić zadania?</li><li>Czy istnieją bieżące środowisko uruchomieniowe uaktualnienia?</li><li>To zadanie w oczekiwanym stanie (na przykład uruchomiona lub zatrzymana przez klienta)?</li><li>Zadanie napotkał się wyjątków pamięci?</li><li>Dostępne są aktualizacje bieżące zaplanowane obliczeniowych?</li><li>Menedżera wykonywania (planu kontroli) jest dostępny?</li></ul>|

## <a name="microsoftwebserverfarms"></a>Microsoft.web/serverFarms
|Wykonane testy|
|---|
|<ul><li>Serwer hosta należy została uruchomiona i działa?</li><li>Internet Information Services jest uruchomiona?</li><li>Moduł równoważenia obciążenia jest uruchomiona?</li><li>Plan usługi App Service jest osiągalna z w obrębie centrum danych?</li><li>Konto magazynu obsługuje zawartość witryny serverFarm dostępne?</li></ul>|

## <a name="microsoftwebsites"></a>Microsoft.web/sites
|Wykonane testy|
|---|
|<ul><li>Serwer hosta należy została uruchomiona i działa?</li><li>Internet Information server jest uruchomiona?</li><li>Moduł równoważenia obciążenia jest uruchomiona?</li><li>Aplikacja sieci Web jest osiągalna z w obrębie centrum danych?</li><li>Konto magazynu obsługuje zawartość witryny?</li></ul>|

# <a name="next-steps"></a>Następne kroki
-  Zobacz [wprowadzenie do pulpitu nawigacyjnego usługi Azure Service Health](service-health-overview.md) i [wprowadzenie do usługi Azure Resource Health](resource-health-overview.md) Aby dowiedzieć się więcej o nich. 
-  [Często zadawane pytania dotyczące usługi Azure Resource Health](resource-health-faq.md)
- Konfigurowanie alertów, dzięki czemu użytkownik jest powiadamiany o problemów z kondycją. Aby uzyskać więcej informacji, zobacz [skonfigurować alerty dla zdarzeń kondycji usługi](../azure-monitor/platform/alerts-activity-log-service-notifications.md). 
