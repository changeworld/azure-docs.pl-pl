---
title: Obsługiwane typy zasobów za pośrednictwem kondycji zasobów platformy Azure | Dokumentacja firmy Microsoft
description: Obsługiwane typy zasobów za pośrednictwem kondycji zasobów platformy Azure
services: Resource health
documentationcenter: ''
author: BernardoAMunoz
manager: ''
editor: ''
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 10/09/2017
ms.author: BernardoAMunoz
ms.openlocfilehash: 35eedb170e1c79f4d282f71608b456a271c4d59b
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37018227"
---
# <a name="resource-types-and-health-checks-in-azure-resource-health"></a>Typy zasobów i kondycji sprawdza w kondycji zasobów platformy Azure
Poniżej przedstawiono pełną listę wszystkie testy wykonane przez kondycja zasobów według typów zasobów.

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers
|Wykonanie testów|
|---|
|<ul><li>Jest serwer działa prawidłowo?</li><li>Serwer zabrakło pamięci?</li><li>Serwer uruchamiania?</li><li>Trwa odzyskiwanie serwera?</li></ul>|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
|Wykonanie testów|
|---|
|<ul><li>Działa usługa Api Management i systemem?</li></ul>|

## <a name="microsoftcacheredisredis"></a>Microsoft.CacheRedis/Redis
|Wykonanie testów|
|---|
|<ul><li>Czy wszystkie węzły w pamięci podręcznej działa prawidłowo?</li><li>Pamięć podręczna jest osiągalna z wewnątrz centrum danych?</li><li>Pamięć podręczna osiągnęła maksymalną liczbę połączeń?</li><li> Pamięć podręczna wyczerpała jego dostępnej pamięci? </li><li>Pamięć podręczna występują dużej liczby błędów stron?</li><li>Pamięć podręczna jest mocno obciążony?</li></ul>|

## <a name="microsoftcdnprofile"></a>Microsoft.CDN/profile
|Wykonanie testów|
|---|
|<ul> <li>Wszelkie punkty końcowe została zatrzymana, usunięte lub jest niepoprawnie skonfigurowany?</li><li>Uzupełniające portalu jest dostępny dla operacji konfiguracji sieci CDN?</li><li>Czy istnieją dostarczania bieżących problemów z punktami końcowymi usługi CDN?</li><li>Użytkownicy, można zmienić konfigurację z zasobami w sieci CDN?</li><li>Zmiany konfiguracji propagują szybkością oczekiwanego?</li><li>Użytkownicy, można zarządzać konfiguracją CDN przy użyciu portalu Azure, programu PowerShell lub interfejsu API?</li> </ul>|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classiccompute/virtualmachines
|Wykonanie testów|
|---|
|<ul><li>Jest serwerem hosta działa prawidłowo?</li><li>Rozruch systemu operacyjnego hosta ukończono?</li><li>Są udostępniane kontenera maszyny wirtualnej i włączone?</li><li>Czy istnieje połączenie sieciowe między hostem i konto magazynu?</li><li>Rozruch system operacyjny gościa ukończono?</li><li>Istnieje już uruchomione zaplanowanej konserwacji?</li></ul>|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.cognitiveservices/accounts
|Wykonanie testów|
|---|
|<ul><li>Konto jest osiągalna z wewnątrz centrum danych?</li><li>Kognitywnych dostawcy zasobów usługi jest dostępny?</li><li>Kognitywnych usługi jest dostępna w regionie odpowiednie?</li><li>Może być odczytany można wykonać operacji na koncie magazynu zawierający metadane zasobu?</li><li>Został osiągnięty limit przydziału wywołania interfejsu API?</li><li>Wywołanie interfejsu API odczytu — limit został osiągnięty?</li></ul>|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.compute/virtualmachines
|Wykonanie testów|
|---|
|<ul><li>Jest serwer obsługujący tej maszyny wirtualnej w górę i systemem</li><li>Rozruch systemu operacyjnego hosta ukończono?</li><li>Są udostępniane kontenera maszyny wirtualnej i włączone?</li><li>Czy istnieje połączenie sieciowe między hostem i konto magazynu?</li><li>Rozruch system operacyjny gościa ukończono?</li><li>Istnieje już uruchomione zaplanowanej konserwacji?</li></ul>|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.datalakeanalytics/accounts
|Wykonanie testów|
|---|
|<ul><li>Występują problemy doświadczeni użytkownicy przesyłania lub listę zadań usługi Data Lake Analytics?</li><li>Możemy ukończyć błędów systemowych zadań usługi Data Lake Analytics?</li></ul>|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.datalakestore/accounts
|Wykonanie testów|
|---|
|<ul><li>Użytkownicy wystąpić problemy przekazywanie danych do usługi Data Lake Store?</li><li>Użytkownicy wystąpić problemy pobieranie danych z usługi Data Lake Store?</li></ul>|

## <a name="microsoftdevicesiothubs"></a>Microsoft.devices/iothubs

|Wykonanie testów|
|---|
|<ul><li>To Centrum IoT działa prawidłowo?</li></ul>|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.documentdb/databaseAccounts
|Wykonanie testów|
|---|
|<ul><li>Zostały jeszcze żądań bazy danych lub kolekcji nie jest wyświetlona z powodu niedostępności usługi bazy danych Azure rozwiązania Cosmos?</li><li>Zostały jeszcze żądań dokumentu nie jest wyświetlona z powodu niedostępności usługi bazy danych Azure rozwiązania Cosmos?</li></ul>|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults
|Wykonanie testów|
|---|
|<ul><li>Żądania do magazynu kluczy występują problemy z platformy Azure KeyVault?</li><li>Są ograniczane żądania do magazynu kluczy z powodu zbyt wiele żądań wysyłanych przez klienta?</li></ul>|

## <a name="microsoftnetworkconnections"></a>Microsoft.network/connections
|Wykonanie testów|
|---|
|<ul><li>Tunel VPN jest podłączony?</li><li>Połączenia są konflikty konfiguracji?</li><li>Klucze wstępne poprawnie skonfigurowanych?</li><li>Lokalnego urządzenia sieci VPN jest dostępny?</li><li>Zasady zabezpieczeń IPSec i IKE czy niezgodności?</li><li>Prawidłowo zainicjowana lub w stanie niepowodzenia, jest połączenie sieci VPN S2S?</li><li>Jest połączenia do Wirtualnymi prawidłowo zainicjowana lub w stanie niepowodzenia?</li></ul>|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.network/virtualNetworkGateways
|Wykonanie testów|
|---|
|<ul><li>Brama sieci VPN jest dostępny z Internetu?</li><li>Brama sieci VPN jest w trybie gotowości?</li><li>Usługa sieci VPN jest uruchomiona w bramie?</li></ul>|

## <a name="microsoftnotificationhubsnamespace"></a>Microsoft.NotificationHubs/namespace
|Wykonanie testów|
|---|
|<ul><li> Środowisko uruchomieniowe operacje, takie jak rejestracji, instalacji lub wysyłania można wykonać w przestrzeni nazw?</li></ul>|

## <a name="microsoftpowerbiworkspacecollections"></a>Microsoft.PowerBI/workspaceCollections
|Wykonanie testów|
|---|
|<ul><li>Jest systemu operacyjnego hosta działa prawidłowo?</li><li>WorkspaceCollection jest dostępny z poza centrum danych?</li><li>Dostawcy zasobów usługi PowerBI jest dostępny?</li><li>Usługi Power BI jest dostępna w regionie odpowiednie?</li></ul>|

## <a name="microsoftsearchsearchservices"></a>Microsoft.search/searchServices
|Wykonanie testów|
|---|
|<ul><li>Można wykonać operacji diagnostyki w klastrze?</li></ul>|

## <a name="microsoftsqlserverdatabase"></a>Microsoft.SQL/Server/database
|Wykonanie testów|
|---|
|<ul><li> Nie ma w niej logowania do bazy danych?</li></ul>|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts
|Wykonanie testów|
|---|
|<ul><li>Żądania do odczytywania danych z konta magazynu występują problemy z platformy Azure Storage?</li><li>Żądania do zapisywania danych na koncie magazynu występują problemy z platformy Azure Storage?</li><li>Klastra magazynów, w którym znajduje się konto magazynu jest niedostępny?</li></ul>|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs
|Wykonanie testów|
|---|
|<ul><li>Czy wszystkie hosty, w którym wykonywania w górę i uruchomione zadanie?</li><li>Nie można uruchomić zadania?</li><li>Czy istnieją bieżące środowisko uruchomieniowe uaktualnienia?</li><li>Zadanie w oczekiwanym stanem (np. uruchomiona lub zatrzymana przez klienta)?</li><li>Zadanie napotkał limit pamięci wyjątków?</li><li>Dostępne są aktualizacje trwającą obliczeń zaplanowane?</li><li>Menedżera wykonywania (plan kontroli) jest dostępny?</li></ul>|

## <a name="microsoftwebserverfarms"></a>Microsoft.web/serverFarms
|Wykonanie testów|
|---|
|<ul><li>Jest serwerem hosta działa prawidłowo?</li><li>Internetowe usługi informacyjne jest uruchomiona?</li><li>Moduł równoważenia obciążenia jest uruchomiona?</li><li>Plan usługi aplikacji jest osiągalna z wewnątrz centrum danych?</li><li>Konto magazynu jest hostem zawartość witryny serverFarm dostępne?</li></ul>|

## <a name="microsoftwebsites"></a>Microsoft.web/sites
|Wykonanie testów|
|---|
|<ul><li>Jest serwerem hosta działa prawidłowo?</li><li>Internet Information server jest uruchomiona?</li><li>Moduł równoważenia obciążenia jest uruchomiona?</li><li>Aplikacja sieci Web jest osiągalna z wewnątrz centrum danych?</li><li>Konto magazynu jest hostem dostępnej zawartości witryny?</li></ul>|

# <a name="next-steps"></a>Następne kroki
-  Zobacz [wprowadzenie do pulpit nawigacyjny kondycji usługi Azure](service-health-overview.md) i [wprowadzenie do kondycji zasobów Azure](resource-health-overview.md) Aby dowiedzieć się więcej o nich. 
-  [Często zadawane pytania dotyczące kondycji zasobów platformy Azure](resource-health-faq.md)
- Konfigurowanie alertów, więc użytkownik jest powiadamiany o kondycji problemy. Aby uzyskać więcej informacji, zobacz [skonfiguruj alerty dla zdarzenia kondycji usługi](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md). 
