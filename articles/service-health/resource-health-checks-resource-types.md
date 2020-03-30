---
title: Obsługiwane typy zasobów za pośrednictwem usługi Azure Resource Health | Dokumenty firmy Microsoft
description: Obsługiwane typy zasobów za pośrednictwem kondycji zasobów platformy Azure
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: 3ba7b308c0c7671df8a652194830cb910cb89acc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258306"
---
# <a name="resource-types-and-health-checks-in-azure-resource-health"></a>Typy zasobów i kontrole kondycji w kondycji zasobów platformy Azure
Poniżej znajduje się pełna lista wszystkich kontroli wykonywanych za pośrednictwem kondycji zasobów według typów zasobów.

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisSługa/serwery
|Wykonane kontrole|
|---|
|<ul><li>Czy serwer jest uruchomiony?</li><li>Czy na serwerze zabrakło pamięci?</li><li>Czy serwer jest uruchamiany?</li><li>Czy serwer jest odzyskiwalny?</li></ul>|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
|Wykonane kontrole|
|---|
|<ul><li>Czy usługa Api Management jest uruchomiona?</li></ul>|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchKonkludy
|Wykonane kontrole|
|---|
|<ul><li>Czy konto batch jest uruchomione?</li><li>Czy przydział puli został przekroczony dla tego konta wsadowego?</li></ul>|

## <a name="microsoftcacheredis"></a>Pamięć podręczna firmy Microsoft/Redis
|Wykonane kontrole|
|---|
|<ul><li>Czy wszystkie węzły pamięci podręcznej są uruchomione?</li><li>Czy można uzyskać kontakt z pamięcią podręczną z poziomu centrum danych?</li><li>Czy pamięć podręczna osiągnęła maksymalną liczbę połączeń?</li><li> Czy pamięć podręczna wyczerpała swoją dostępną pamięć? </li><li>Czy w pamięci podręcznej występuje duża liczba błędów strony?</li><li>Czy pamięć podręczna jest obciążana dużym obciążeniem?</li></ul>|

## <a name="microsoftcdnprofile"></a>Microsoft.CDN/profile
|Wykonane kontrole|
|---|
|<ul> <li>Czy dodatkowy portal jest dostępny dla operacji konfiguracji sieci CDN?</li><li>Czy istnieją bieżące problemy z dostarczaniem punktów końcowych sieci CDN?</li><li>Czy użytkownicy mogą zmienić konfigurację swoich zasobów sieci CDN?</li><li>Czy zmiany konfiguracji są propagujące w oczekiwanym tempie?</li><li>Czy użytkownicy mogą zarządzać konfiguracją sieci CDN przy użyciu portalu Azure, programu PowerShell lub interfejsu API?</li> </ul>|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classiccompute/virtualmachines
|Wykonane kontrole|
|---|
|<ul><li>Czy serwer hosta jest uruchomiony?</li><li>Czy uruchamianie systemu operacyjnego hosta zostało zakończone?</li><li>Czy kontener maszyny wirtualnej jest aprowizowany i zasilany?</li><li>Czy istnieje łączność sieciowa między hostem a kontem magazynu?</li><li>Czy rozruch systemu operacyjnego gościa został zakończony?</li><li>Czy planowane są prace konserwacyjne?</li><li>Czy sprzęt hosta jest zdegradowany i przewiduje się, że wkrótce się nie powiedzie?</li></ul>|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.cognitiveservices/accounts
|Wykonane kontrole|
|---|
|<ul><li>Czy można uzyskać do niego kontakt z poziomu centrum danych?</li><li>Czy dostawca zasobów usług Cognitive Services jest dostępny?</li><li>Czy usługa Cognitive Service jest dostępna w odpowiednim regionie?</li><li>Czy operacje odczytu mogą być wykonywane na koncie magazynu, na którym przechowywane są metadane zasobu?</li><li>Czy osiągnięto przydział wywołania interfejsu API?</li><li>Czy osiągnięto limit odczytu wywołania interfejsu API?</li></ul>|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.compute/virtualmachines
|Wykonane kontrole|
|---|
|<ul><li>Czy serwer obsługujący tę maszynę wirtualną jest uruchomiony?</li><li>Czy uruchamianie systemu operacyjnego hosta zostało zakończone?</li><li>Czy kontener maszyny wirtualnej jest aprowizowany i zasilany?</li><li>Czy istnieje łączność sieciowa między hostem a kontem magazynu?</li><li>Czy rozruch systemu operacyjnego gościa został zakończony?</li><li>Czy planowane są prace konserwacyjne?</li><li>Czy sprzęt hosta jest zdegradowany i przewiduje się, że wkrótce się nie powiedzie?</li></ul>|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.datafactory/factories
|Wykonane kontrole|
|---|
|<ul><li>Czy wystąpiły awarie przebiegu potoku?</li><li>Czy klaster obsługujący fabrykę danych jest w dobrej kondycji?</li></ul>|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.datalakeanalytics/accounts Microsoft.datalakeanalytics/accounts Microsoft.datalakeanalytics/accounts Microsoft.
|Wykonane kontrole|
|---|
|<ul><li>Czy użytkownicy mieli problemy z przesyłaniem lub wystawianiem ofert pracy usługi Data Lake Analytics?</li><li>Czy zadania usługi Data Lake Analytics nie mogą zostać ukończone z powodu błędów systemu?</li></ul>|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.datalakestore/accounts
|Wykonane kontrole|
|---|
|<ul><li>Czy użytkownicy mieli problemy z przesyłaniem danych do usługi Data Lake Store?</li><li>Czy użytkownicy mieli problemy z pobieraniem danych z usługi Data Lake Store?</li></ul>|

## <a name="microsoftdatamigrationservices"></a>Microsoft.datamigration/services
|Wykonane kontrole|
|---|
|<ul><li>Czy usługa migracji bazy danych nie może aprowizować?</li><li>Czy usługa migracji bazy danych została zatrzymana z powodu braku aktywności lub żądania użytkownika?</li></ul>|

## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/konta
|Wykonane kontrole|
|---|
|<ul><li>Czy konto udostępniania danych jest uruchomione?</li><li>Czy klaster hostujący udział danych jest dostępny?</li></ul>|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/serwery
|Wykonane kontrole|
|---|
|<ul><li>Czy serwer jest niedostępny z powodu konserwacji?</li><li>Czy serwer jest niedostępny z powodu ponownej konfiguracji?</li></ul>|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers
|Wykonane kontrole|
|---|
|<ul><li>Czy serwer jest niedostępny z powodu konserwacji?</li><li>Czy serwer jest niedostępny z powodu ponownej konfiguracji?</li></ul>|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers
|Wykonane kontrole|
|---|
|<ul><li>Czy serwer jest niedostępny z powodu konserwacji?</li><li>Czy serwer jest niedostępny z powodu ponownej konfiguracji?</li></ul>|

## <a name="microsoftdevicesiothubs"></a>Microsoft.devices/iothubs
|Wykonane kontrole|
|---|
|<ul><li>Czy centrum IoT jest uruchomione?</li></ul>|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.documentdb/databaseKonta kont
|Wykonane kontrole|
|---|
|<ul><li>Czy nie było żadnych żądań bazy danych lub kolekcji nie są obsługiwane z powodu niedostępności usługi Azure Cosmos DB?</li><li>Czy były jakieś żądania dokumentów nie obsługiwane z powodu niedostępności usługi Azure Cosmos DB?</li></ul>|

## <a name="microsofteventhubnamespaces"></a>Microsoft.eventhub/przestrzenie nazw
|Wykonane kontrole|
|---|
|<ul><li>Czy obszar nazw Centrum zdarzeń jest doświadczany błędów generowanych przez użytkownika?</li><li>Czy obszar nazw centrum zdarzeń jest obecnie uaktualniany?</li></ul>|

## <a name="microsofthdinsightclusters"></a>Microsoft.hdinsight/klastry
|Wykonane kontrole|
|---|
|<ul><li>Czy podstawowe usługi są dostępne w klastrze HDInsight?</li><li>Czy klaster HDInsight może uzyskać dostęp do klucza szyfrowania BYOK w spoczynku?</li></ul>|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/przechowalnia
|Wykonane kontrole|
|---|
|<ul><li>Czy żądania do magazynu kluczy nie powiększy się z powodu problemów z platformą Azure KeyVault?</li><li>Czy żądania do magazynu kluczy są ograniczane z powodu zbyt wielu żądań składanych przez klienta?</li></ul>|

## <a name="microsoftmachinelearningwebservices"></a>Microsoft.MachineLearning/webServices
|Wykonane kontrole|
|---|
|<ul><li>Czy usługa internetowa jest uruchomiona?</li></ul>|

## <a name="microsoftmediamediaservices"></a>Usługi Microsoft.Media/media
|Wykonane kontrole|
|---|
|<ul><li>Czy usługa multimedialna jest uruchomiona?</li></ul>|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.network/applicationgateways
|Wykonane kontrole|
|---|
|<ul><li>Czy wydajność bramy aplikacji jest obniżona?</li><li>Czy brama aplikacji jest dostępna?</li></ul>|

## <a name="microsoftnetworkconnections"></a>Sieć/połączenia firmy Microsoft
|Wykonane kontrole|
|---|
|<ul><li>Czy tunel SIECI VPN jest połączony?</li><li>Czy występują konflikty konfiguracji w połączeniu?</li><li>Czy klucze wstępnie udostępnione są poprawnie skonfigurowane?</li><li>Czy urządzenie lokalne sieci VPN jest osiągalne?</li><li>Czy zasady zabezpieczeń IPSec/IKE są niezgodne z zasadami?</li><li>Czy połączenie sieci VPN S2S jest poprawnie aprowizować lub w stanie awarii?</li><li>Czy połączenie sieci wirtualnej do sieci wirtualnej jest poprawnie aprowiedny lub w stanie awarii?</li></ul>|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.network/expressroutecircuits Microsoft.network/expressroutecircuits Microsoft.network/expressroutecircuits Microsoft.
|Wykonane kontrole|
|---|
|<ul><li>Czy obwód usługi ExpressRoute jest zdrowy?</li></ul>|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.network/frontdoory
|Wykonane kontrole|
|---|
|<ul><li>Czy backendy drzwi frontowych reagują z błędami na sondy kondycji?</li><li>Czy zmiany konfiguracji są opóźnione?</li></ul>|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.network/LoadBalancers
|Wykonane kontrole|
|---|
|<ul><li>Czy są dostępne punkty końcowe równoważenia obciążenia?</li></ul>|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.network/virtualNetworkGateways
|Wykonane kontrole|
|---|
|<ul><li>Czy brama sieci VPN jest osiągalna z Internetu?</li><li>Czy brama sieci VPN jest w trybie gotowości?</li><li>Czy usługa SIECI VPN jest uruchomiona w bramie?</li></ul>|

## <a name="microsoftnotificationhubsnamespace"></a>Microsoft.NotificationHubs/obszar nazw
|Wykonane kontrole|
|---|
|<ul><li>Czy operacje środowiska uruchomieniowego, takie jak rejestracja, instalacja lub wysyłanie, mogą być wykonywane w obszarze nazw?</li></ul>|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.operationalinsights/workspaces
|Wykonane kontrole|
|---|
|<ul><li>Czy istnieją opóźnienia indeksowania obszaru roboczego?</li></ul>|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/Możliwości
|Wykonane kontrole|
|---|
|<ul><li>Czy zasób pojemności jest uruchomiony?</li><li>Czy wszystkie obciążenia są uruchomione?</li></ul>|

## <a name="microsoftpowerbiworkspacecollections"></a>Microsoft.PowerBI/workspaceKokucje
|Wykonane kontrole|
|---|
|<ul><li>Czy system operacyjny hosta jest uruchomiony?</li><li>Czy workspaceCollection jest osiągalny spoza centrum danych?</li><li>Czy dostawca zasobów usługi Power BI jest dostępny?</li><li>Czy usługa Power BI jest dostępna w odpowiednim regionie?</li></ul>|

## <a name="microsoftsearchsearchservices"></a>Microsoft.search/searchSługs
|Wykonane kontrole|
|---|
|<ul><li>Czy operacje diagnostyczne mogą być wykonywane w klastrze?</li></ul>|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/przestrzenie nazw
|Wykonane kontrole|
|---|
|<ul><li>Czy klienci doświadczają błędów generowanej przez użytkownika usługi Service Bus?</li><li>Czy użytkownicy doświadczają wzrostu błędów przejściowych z powodu uaktualnienia obszaru nazw usługi Service Bus?</li></ul>|

## <a name="microsoftservicefabricclusters"></a>Microsoft.ServiceFabric/klastry
|Wykonane kontrole|
|---|
|<ul><li>Czy klaster sieci szkieletowej usług jest uruchomiony?</li><li>Czy klastrem sieci szkieletowej usług można zarządzać za pośrednictwem usługi Azure Resource Manager?</li></ul>|

## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft.SQL/managedInstances/databases
|Wykonane kontrole|
|---|
|<ul><li>Czy baza danych jest uruchomiona?</li></ul>|

## <a name="microsoftsqlserverdatabases"></a>Microsoft.SQL/Serwer/bazy danych
|Wykonane kontrole|
|---|
|<ul><li>Czy były logowania do bazy danych?</li></ul>|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageKondyje
|Wykonane kontrole|
|---|
|<ul><li>Czy żądania odczytu danych z konta magazynu nie powiódł się z powodu problemów z platformą Azure Storage?</li><li>Czy żądania zapisu danych na koncie magazynu nie powiódł się z powodu problemów z platformą Azure Storage?</li><li>Czy klaster Magazynu, w którym znajduje się konto Magazyn, jest niedostępny?</li></ul>|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalityka/szybkość przesyłania strumieniowego
|Wykonane kontrole|
|---|
|<ul><li>Czy wszystkie hosty, w których zadanie jest wykonywane i uruchamiane?</li><li>Czy zadanie nie mogło się rozpocząć?</li><li>Czy trwają uaktualniania środowiska uruchomieniowego?</li><li>Czy zadanie jest w oczekiwanym stanie (na przykład uruchomione lub zatrzymane przez klienta)?</li><li>Czy zadanie napotkał z wyjątków pamięci?</li><li>Czy są w toku zaplanowane aktualizacje obliczeń?</li><li>Czy menedżer wykonania (plan kontroli) jest dostępny?</li></ul>|

## <a name="microsoftwebserverfarms"></a>Microsoft.web/serverFarms
|Wykonane kontrole|
|---|
|<ul><li>Czy serwer hosta jest uruchomiony?</li><li>Czy internetowe usługi informacyjne są uruchomione?</li><li>Czy moduł równoważenia obciążenia jest uruchomiony?</li><li>Czy można uzyskać kontakt z planem usługi app service z poziomu centrum danych?</li><li>Czy konto magazynu hosting zawartości witryn dla serweraFarm jest dostępny?</li></ul>|

## <a name="microsoftwebsites"></a>Witryny microsoft.web/witryny
|Wykonane kontrole|
|---|
|<ul><li>Czy serwer hosta jest uruchomiony?</li><li>Czy uruchomiony jest internetowy serwer informacji?</li><li>Czy moduł równoważenia obciążenia jest uruchomiony?</li><li>Czy można uzyskać do aplikacji sieci Web z poziomu centrum danych?</li><li>Czy konto magazynu, na które znajduje się zawartość witryny, jest dostępne?</li></ul>|

## <a name="next-steps"></a>Następne kroki
-  Zobacz [Wprowadzenie do pulpitu nawigacyjnego usługi Azure Service Health](service-health-overview.md) i wprowadzenie do usługi Azure Resource [Health,](resource-health-overview.md) aby dowiedzieć się więcej o nich. 
-  [Często zadawane pytania dotyczące kondycji zasobów platformy Azure](resource-health-faq.md)
- Skonfiguruj alerty, aby otrzymywać powiadomienia o problemach zdrowotnych. Aby uzyskać więcej informacji, zobacz [Konfigurowanie alertów dla zdarzeń kondycji usługi](../azure-monitor/platform/alerts-activity-log-service-notifications.md). 
