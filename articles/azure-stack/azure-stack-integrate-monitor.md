---
title: Integracja rozwiązania monitorowania zewnętrznych z stosu Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zintegrować stosu Azure z zewnętrznego rozwiązanie monitorowania w centrum danych.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 856738a7-1510-442a-88a8-d316c67c757c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 4188d114aa86086821b2c640d7f2d98a78bcbf4e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="integrate-external-monitoring-solution-with-azure-stack"></a>Integracja rozwiązania monitorowania zewnętrznych z Azure stosu

Dla zewnętrznych monitorowania infrastruktury stosu Azure, należy monitorować oprogramowania stosu Azure, komputery fizyczne i przełączniki sieci fizycznej. Każdy z tych obszarów udostępnia metody można pobrać informacji o kondycji i alertów:

- Oprogramowanie stosu Azure oferuje opartego na interfejsie REST interfejsu API można pobrać kondycji i alertów. (Przy użyciu zdefiniowanych przez oprogramowanie technologii, takich jak bezpośrednie miejsca do magazynowania, kondycję magazynu i alerty są częścią oprogramowania monitorowania.).
- Komputerów fizycznych można udostępnić kondycji i informacji o alertach za pomocą kontrolerów zarządzania płytą główną (BMC).
- Fizyczne urządzenia sieciowe można udostępnić kondycji i informacji o alertach za pośrednictwem protokołu SNMP.

Każde rozwiązanie stosu Azure jest dostarczany z hostem cyklu życia sprzętu. Ten host uruchamia monitorowania oprogramowania dostawcy sprzętu Original Equipment Manufacturer (OEM) dla serwerów fizycznych i urządzeń sieciowych. W razie potrzeby można pominąć te rozwiązania monitorowania i bezpośrednio zintegrować z istniejącymi rozwiązaniami do monitorowania w centrum danych.

> [!IMPORTANT]
> Zewnętrznych rozwiązanie monitorowania, którego używasz, musi być bez wykorzystania agentów. Nie można zainstalować agentów innych firm wewnątrz składniki stosu Azure.

Na poniższym diagramie przedstawiono przepływ ruchu między systemu Azure stosu zintegrowane, hosta cyklu życia sprzętu zewnętrznych rozwiązanie monitorowania i system biletów/danych zewnętrznych kolekcji.

![Diagram przedstawiający ruchu między Azure stosu, monitorowania i wystawiania biletów rozwiązania.](media/azure-stack-integrate-monitor/MonitoringIntegration.png)  

W tym artykule wyjaśniono, jak zintegrować stosu Azure z zewnętrznego monitorowania rozwiązań, takich jak System Center Operations Manager i Nagios. Zawiera również sposób pracy z alertami programowo przy użyciu programu PowerShell lub za pośrednictwem interfejsu API REST.

## <a name="integrate-with-operations-manager"></a>Integracja z programem Operations Manager

Operations Manager służy do monitorowania zewnętrznych stosu Azure. Pakiet administracyjny System Center dla programu Microsoft Azure stosu umożliwia monitorowanie wielu wdrożeń stosu Azure przy użyciu pojedynczego wystąpienia programu Operations Manager. Pakiet zarządzania korzysta z dostawcy zasobów kondycji i dostawcy zasobów aktualizacji interfejsów API REST do komunikowania się z stosu Azure. Jeśli planujesz obejścia OEM monitorowania oprogramowania, które jest uruchomiona na hoście cyklu życia sprzętu, należy zainstalować pakiety administracyjne dostawcy do monitorowania serwerów fizycznych. Aby monitorować przełączników sieciowych umożliwia także odnajdywania urządzeń sieciowych programu Operations Manager.

Pakiet administracyjny dla stosu Azure oferuje następujące możliwości:

- Możesz zarządzać wielu wdrożeń Azure stosu
- Brak obsługi dla usługi Azure Active Directory (Azure AD) i Active Directory Federation Services (AD FS)
- Możesz pobrać i Zamknij alerty
- Brak kondycję i wydajność pulpit nawigacyjny
- Obejmuje wykrywania automatycznego trybu konserwacji dla gdy poprawek i aktualizacji (P & U) jest w toku
- Obejmuje zadania Wymuszanie aktualizacji do wdrożenia i regionu
- Możesz dodawać niestandardowe informacje w regionie
- Obsługa powiadomień i raportowanie

Możesz pobrać pakiet administracyjny programu System Center dla programu Microsoft Azure stosu oraz skojarzonych z nimi [Podręcznik użytkownika](https://www.microsoft.com/en-us/download/details.aspx?id=55184), lub bezpośrednio z programu Operations Manager.

W przypadku obsługi biletów rozwiązania można zintegrować programu Operations Manager z programem System Center Service Manager. Łącznik produktu zintegrowane umożliwia komunikację dwukierunkową, który umożliwia zamknięcie alertu w stosie Azure a programem Operations Manager po rozwiązaniu żądania obsługi w programie Service Manager.

Na poniższym diagramie przedstawiono integracji stosu Azure z istniejącego wdrożenia programu System Center. Można zautomatyzować programu Service Manager dalsze z programu System Center Orchestrator lub automatyzacji zarządzania usługi (SMA) do uruchamiania operacji w stosie Azure.

![Diagram przedstawiający integrację z modelu obiektów programu Service Manager i SMA.](media/azure-stack-integrate-monitor/SystemCenterIntegration.png)

## <a name="integrate-with-nagios"></a>Integracja z Nagios

Nagios, monitorowanie wtyczki opracowano wraz z rozwiązań Cloudbase partnerskich, który jest dostępny w ramach licencji ograniczająca bezpłatnego oprogramowania — MIT (Massachusetts Institute of Technology).

Wtyczka jest napisany w języku Python i wykorzystuje dostawcy zasobów kondycji interfejsu API REST. Zapewnia podstawowe funkcje do pobierania i Zamknij alerty w stosie Azure. Podobnie jak pakiet administracyjny programu System Center umożliwia dodawanie wielu wdrożeń stosu Azure oraz wysyłać powiadomienia.

Wtyczka współpracuje z Nagios przedsiębiorstwa i Nagios podstawowe. Można go pobrać [tutaj](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details). Pobieranie zawiera także szczegóły instalacji i konfiguracji.

### <a name="plugin-parameters"></a>Parametry wtyczki

Konfigurowanie pliku wtyczki "Azurestack_plugin.py" z następującymi parametrami:

| Parametr | Opis | Przykład |
|---------|---------|---------|
| *arm_endpoint* | Punktu końcowego platformy Azure Resource Manager (administrator) |https://adminmanagement.local.azurestack.external |
| *api_endpoint* | Punktu końcowego platformy Azure Resource Manager (administrator)  | https://adminmanagement.local.azurestack.external |
| *Tenant_id* | Identyfikator subskrypcji administratora | Pobrać za pomocą portalu administratora lub programu PowerShell |
| *nazwa_użytkownika* | Nazwa użytkownika subskrypcji — operator | operator@myazuredirectory.onmicrosoft.com |
| *User_password* | Hasło subskrypcji — operator | mojehasło |
| *Client_id* | Klient | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417* |
| *region* |  Nazwa regionu platformy Azure stosu | lokalne |
|  |  |

* PowerShell identyfikator GUID, który jest dostarczany jest uniwersalny. Służy on do każdego wdrożenia.

## <a name="use-powershell-to-monitor-health-and-alerts"></a>Monitor kondycji i alerty przy użyciu programu PowerShell

Jeśli nie używasz programu Operations Manager, Nagios lub rozwiązanie oparte na Nagios, można włączyć szeroki zakres monitorowania rozwiązania do integracji z stosu Azure za pomocą programu PowerShell.
 
1. Przy użyciu programu PowerShell, upewnij się, że masz [PowerShell zainstalowany i skonfigurowany](azure-stack-powershell-configure-quickstart.md) dla środowiska operator stosu Azure. Instalowanie programu PowerShell na komputerze lokalnym, który można osiągnąć punktu końcowego Menedżera zasobów (administrator) (https://adminmanagement. [ Region]. [External_FQDN]).

2. Uruchom następujące polecenia, aby Połącz ze środowiskiem Azure stosu jako operator Azure stosu:

   ```PowerShell
   Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint https://adminmanagement.[Region].[External_FQDN]

   Add-AzureRmAccount -EnvironmentName "AzureStackAdmin"
   ```
3. Przejdź do katalogu, w którym zainstalowano [narzędzia Azure stosu](https://github.com/Azure/AzureStack-Tools) w ramach instalacji programu PowerShell, na przykład c:\azurestack-tools-master. Następnie przejdź do katalogu, infrastruktury i uruchom następujące polecenie, aby zaimportować moduł infrastruktury:

   ```PowerShell
   Import-Module .\AzureStack.Infra.psm1
    ```
4. Użyj polecenia takie jak następujące przykłady do pracy z alertami:
   ```PowerShell
   #Retrieve all alerts
   Get-AzsAlert -location [Region]

   #Filter for active alerts
   $Active=Get-AzsAlert -location [Region] | Where {$_.State -eq "active"}
   $Active

   #Close alert
   Close-AzsAlert -location [Region] -AlertID "ID"

   #Retrieve resource provider health
   Get-AzsResourceProviderHealths -location [Region]

   #Retrieve infrastructure role instance health
   Get-AzsInfrastructureRoleHealths -location [Region]
   ```

## <a name="use-the-rest-api-to-monitor-health-and-alerts"></a>Za pomocą interfejsu API REST do monitorowania kondycji i alerty

Wywołania interfejsu API REST umożliwia uzyskiwanie alertów, Zamknij alerty i pobrać kondycji dostawców zasobów.

### <a name="get-alert"></a>Pobierz alertu

**Żądanie**

Żądanie pobiera wszystkie aktywne i zamknięte alerty dotyczące subskrypcji dostawcy domyślnego. Nie ma żadnych treści żądania.


|Metoda  |Identyfikator URI żądania  |
|---------|---------|
|GET     |   https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/Alerts?api-version=2016-05-01"      |
|     |         |

**Argumenty**

|Argument  |Opis  |
|---------|---------|
|armendpoint     |  Punkt końcowy usługi Azure Resource Manager środowiska Azure stosu w formacie https://adminmanagement.{RegionName}.{External FQDN}. Na przykład, jeśli jest zewnętrznych *azurestack.external* i jest nazwa regionu *lokalnego*, to jest punkt końcowy Menedżera zasobów https://adminmanagement.local.azurestack.external.       |
|subid     |   Identyfikator subskrypcji użytkownika, który jest wywołania. Ten interfejs API w zapytaniu można użyć tylko z użytkownikiem, który ma uprawnienia do subskrypcji dostawcy domyślnego.      |
|RegionName     |    Nazwa regionu Azure stosu wdrożenia.     |
|wersja interfejsu API     |  Wersja protokołu, który służy do zgłoszenia tego żądania. Należy użyć 2016-05-01.      |
|     |         |

**Odpowiedź**

```http
GET https://adminmanagement.local.azurestack.external/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/Alerts?api-version=2016-05-01 HTTP/1.1
```

```json
{
"value":[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"name":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/alerts",
"location":"local",
"tags":{},
"properties":
{
"closedTimestamp":"",
"createdTimestamp":"2017-08-10T20:13:57.4398842Z",
"description":[{"text":"The infrastructure role (Updates) is experiencing issues.",
"type":"Text"}],
"faultId":"ServiceFabric:/UpdateResourceProvider/fabric:/AzurestackUpdateResourceProvider",
"alertId":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"faultTypeId":"ServiceFabricApplicationUnhealthy",
"lastUpdatedTimestamp":"2017-08-10T20:18:58.1584868Z",
"alertProperties":
{
"healthState":"Warning",
"name":"Updates",
"fabricName":"fabric:/AzurestackUpdateResourceProvider",
"description":null,
"serviceType":"UpdateResourceProvider"},
"remediation":[{"text":"1. Navigate to the (Updates) and restart the role. 2. If after closing the alert the issue persists, please contact support.",
"type":"Text"}],
"resourceRegistrationId":null,
"resourceProviderRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"serviceRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"severity":"Warning",
"state":"Active",
"title":"Infrastructure role is unhealthy",
"impactedResourceId":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/UpdateResourceProvider",
"impactedResourceDisplayName":"UpdateResourceProvider",
"closedByUserAlias":null
}
},

…
```

**Szczegóły odpowiedzi**


|  Argument  |Opis  |
|---------|---------|
|*id*     |      Unikatowy identyfikator alertu.   |
|*Nazwa*     |     Wewnętrzna nazwa alertu.   |
|*type*     |     Definicja zasobu.    |
|*location*     |       Nazwa regionu.     |
|*tagów*     |   Tagi zasobów.     |
|*closedtimestamp*    |  Czas UTC, kiedy alert został zamknięty.    |
|*createdtimestamp*     |     Czas UTC utworzenia alertu.   |
|*Opis elementu*     |    Opis alertu.     |
|*faultid*     | Składnik.        |
|*identyfikatorem alertu*     |  Unikatowy identyfikator alertu.       |
|*faulttypeid*     |  Unikatowy typ składnika uszkodzony.       |
|*lastupdatedtimestamp*     |   Godzinę UTC ostatniej aktualizacji informacji o alertach.    |
|*healthstate*     | Ogólny stan kondycji.        |
|*Nazwa*     |   Nazwa określonego alertu.      |
|*fabricname*     |    Nazwa sieci szkieletowej w zarejestrowany uszkodzony składnika.   |
|*Opis elementu*     |  Opis elementu zarejestrowanych sieci szkieletowej.   |
|*servicetype*     |   Typ usługi zarejestrowanych sieci szkieletowej.   |
|*Korygowania*     |   Kroki zalecanych czynności naprawczych.    |
|*type*     |   Typ alertu.    |
|*resourceRegistrationid*    |     Identyfikator zarejestrowanego zasobu.    |
|*resourceProviderRegistrationID*   |    Identyfikator zarejestrowanego dostawcy zasobów wykorzystywanych składnika.  |
|*serviceregistrationid*     |    Identyfikator zarejestrowanej usługi.   |
|*Ważność*     |     Ważność alertu.  |
|*state*     |    Stan alertu.   |
|*Tytuł*     |    Tytuł alertu.   |
|*impactedresourceid*     |     Identyfikator zasobu objęte wpływem.    |
|*ImpactedresourceDisplayName*     |     Nazwa zasobu objęte wpływem.  |
|*closedByUserAlias*     |   Użytkownik zamknął alert.      |

### <a name="close-alert"></a>Zamknij alert

**Żądanie**

Żądanie zostanie zamknięte alertu przez jego unikatowy identyfikator.

|Metoda    |Identyfikator URI żądania  |
|---------|---------|
|UMIEŚĆ     |   https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/Alerts/alertid?api-version=2016-05-01"    |

**Argumenty**


|Argument  |Opis  |
|---------|---------|
|*armendpoint*     |   Punkt końcowy Menedżera zasobów środowiska Azure stosu w formacie https://adminmanagement.{RegionName}.{External FQDN}. Na przykład, jeśli jest zewnętrznych *azurestack.external* i jest nazwa regionu *lokalnego*, to jest punkt końcowy Menedżera zasobów https://adminmanagement.local.azurestack.external.      |
|*subid*     |    Identyfikator subskrypcji użytkownika, który jest wywołania. Ten interfejs API w zapytaniu można użyć tylko z użytkownikiem, który ma uprawnienia do subskrypcji dostawcy domyślnego.     |
|*RegionName*     |   Nazwa regionu Azure stosu wdrożenia.      |
|*wersja interfejsu API*     |    Wersja protokołu, który służy do zgłoszenia tego żądania. Należy użyć 2016-05-01.     |
|*identyfikatorem alertu*     |    Unikatowy identyfikator alertu.     |

**Treść**

```json

{
"value":[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"name":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/alerts",
"location":"local",
"tags":{},
"properties":
{
"closedTimestamp":"2017-08-10T20:18:58.1584868Z",
"createdTimestamp":"2017-08-10T20:13:57.4398842Z",
"description":[{"text":"The infrastructure role (Updates) is experiencing issues.",
"type":"Text"}],
"faultId":"ServiceFabric:/UpdateResourceProvider/fabric:/AzurestackUpdateResourceProvider",
"alertId":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"faultTypeId":"ServiceFabricApplicationUnhealthy",
"lastUpdatedTimestamp":"2017-08-10T20:18:58.1584868Z",
"alertProperties":
{
"healthState":"Warning",
"name":"Updates",
"fabricName":"fabric:/AzurestackUpdateResourceProvider",
"description":null,
"serviceType":"UpdateResourceProvider"},
"remediation":[{"text":"1. Navigate to the (Updates) and restart the role. 2. If after closing the alert the issue persists, please contact support.",
"type":"Text"}],
"resourceRegistrationId":null,
"resourceProviderRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"serviceRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"severity":"Warning",
"state":"Closed",
"title":"Infrastructure role is unhealthy",
"impactedResourceId":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/UpdateResourceProvider",
"impactedResourceDisplayName":"UpdateResourceProvider",
"closedByUserAlias":null
}
},
```
**Odpowiedź**

```http
PUT https://adminmanagement.local.azurestack.external//subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b?api-version=2016-05-01 HTTP/1.1
```

```json
{
"value":[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"name":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/alerts",
"location":"local",
"tags":{},
"properties":
{
"closedTimestamp":"",
"createdTimestamp":"2017-08-10T20:13:57.4398842Z",
"description":[{"text":"The infrastructure role (Updates) is experiencing issues.",
"type":"Text"}],
"faultId":"ServiceFabric:/UpdateResourceProvider/fabric:/AzurestackUpdateResourceProvider",
"alertId":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"faultTypeId":"ServiceFabricApplicationUnhealthy",
"lastUpdatedTimestamp":"2017-08-10T20:18:58.1584868Z",
"alertProperties":
{
"healthState":"Warning",
"name":"Updates",
"fabricName":"fabric:/AzurestackUpdateResourceProvider",
"description":null,
"serviceType":"UpdateResourceProvider"},
"remediation":[{"text":"1. Navigate to the (Updates) and restart the role. 2. If after closing the alert the issue persists, please contact support.",
"type":"Text"}],
"resourceRegistrationId":null,
"resourceProviderRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"serviceRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"severity":"Warning",
"state":"Closed",
"title":"Infrastructure role is unhealthy",
"impactedResourceId":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/UpdateResourceProvider",
"impactedResourceDisplayName":"UpdateResourceProvider",
"closedByUserAlias":null
}
},
```

**Szczegóły odpowiedzi**


|  Argument  |Opis  |
|---------|---------|
|*id*     |      Unikatowy identyfikator alertu.   |
|*Nazwa*     |     Wewnętrzna nazwa alertu.   |
|*type*     |     Definicja zasobu.    |
|*location*     |       Nazwa regionu.     |
|*tagów*     |   Tagi zasobów.     |
|*closedtimestamp*    |  Czas UTC, kiedy alert został zamknięty.    |
|*createdtimestamp*     |     Czas UTC utworzenia alertu.   |
|*Opis*     |    Opis alertu.     |
|*faultid*     | Składnik.        |
|*identyfikatorem alertu*     |  Unikatowy identyfikator alertu.       |
|*faulttypeid*     |  Unikatowy typ składnika uszkodzony.       |
|*lastupdatedtimestamp*     |   Godzinę UTC ostatniej aktualizacji informacji o alertach.    |
|*healthstate*     | Ogólny stan kondycji.        |
|*Nazwa*     |   Nazwa określonego alertu.      |
|*fabricname*     |    Nazwa sieci szkieletowej w zarejestrowany uszkodzony składnika.   |
|*Opis elementu*     |  Opis elementu zarejestrowanych sieci szkieletowej.   |
|*servicetype*     |   Typ usługi zarejestrowanych sieci szkieletowej.   |
|*Korygowania*     |   Kroki zalecanych czynności naprawczych.    |
|*type*     |   Typ alertu.    |
|*resourceRegistrationid*    |     Identyfikator zarejestrowanego zasobu.    |
|*resourceProviderRegistrationID*   |    Identyfikator zarejestrowanego dostawcy zasobów wykorzystywanych składnika.  |
|*serviceregistrationid*     |    Identyfikator zarejestrowanej usługi.   |
|*Ważność*     |     Ważność alertu.  |
|*state*     |    Stan alertu.   |
|*Tytuł*     |    Tytuł alertu.   |
|*impactedresourceid*     |     Identyfikator zasobu objęte wpływem.    |
|*ImpactedresourceDisplayName*     |     Nazwa zasobu objęte wpływem.  |
|*closedByUserAlias*     |   Użytkownik zamknął alert.      |

### <a name="get-resource-provider-health"></a>Pobierz kondycja dostawcy zasobów

**Żądanie**

Żądanie pobiera stan kondycji dla wszystkich dostawców zarejestrowanych zasobów.


|Metoda  |Identyfikator URI żądania  |
|---------|---------|
|GET    |   https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/serviceHealths?api-version=2016-05-01"   |


**Argumenty**


|Argumenty  |Opis  |
|---------|---------|
|*armendpoint*     |    Punkt końcowy Menedżera zasobów środowiska Azure stosu w formacie https://adminmanagement.{RegionName}.{External FQDN}. Na przykład jeśli zewnętrznej nazwy FQDN jest azurestack.external i nazwa regionu jest lokalny, następnie punktu końcowego Menedżera zasobów jest https://adminmanagement.local.azurestack.external.     |
|*subid*     |     Identyfikator subskrypcji użytkownika, który jest wywołania. Ten interfejs API w zapytaniu można użyć tylko z użytkownikiem, który ma uprawnienia do subskrypcji dostawcy domyślnego.    |
|*RegionName*     |     Nazwa regionu Azure stosu wdrożenia.    |
|*wersja interfejsu API*     |   Wersja protokołu, który służy do zgłoszenia tego żądania. Należy użyć 2016-05-01.      |


**Odpowiedź**

```http
GET https://adminmanagement.local.azurestack.external/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths?api-version=2016-05-01
```

```json
{
"value":[
{
"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths/03ccf38f-f6b1-4540-9dc8-ec7b6389ecca",
"name":"03ccf38f-f6b1-4540-9dc8ec7b6389ecca",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/serviceHealths",
"location":"local",
"tags":{},
"properties":{
"registrationId":"03ccf38f-f6b1-4540-9dc8-ec7b6389ecca",
"displayName":"Key Vault",
"namespace":"Microsoft.KeyVault.Admin",
"routePrefix":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.KeyVault.Admin/locations/local",
"serviceLocation":"local",
"infraURI":"/subscriptions/4aa97de3-6b83-4582-86e1-65a5e4d1295b/resourceGroups/system.local/providers/Microsoft.KeyVault.Admin/locations/local/infraRoles/Key Vault",
"alertSummary":{"criticalAlertCount":0,"warningAlertCount":0},
"healthState":"Healthy"
}
}

…
```
**Szczegóły odpowiedzi**


|Argument  |Opis  |
|---------|---------|
|*Identyfikator*     |   Unikatowy identyfikator alertu.      |
|*Nazwa*     |  Wewnętrzna nazwa alertu.       |
|*type*     |  Definicja zasobu.       |
|*location*     |  Nazwa regionu.       |
|*tagów*     |     Tagi zasobów.    |
|*registrationId*     |   Unikatowy Rejestracja dostawcy zasobów.      |
|*displayName*     |Nazwa wyświetlana dostawcy zasobów.        |
|*namespace*     |   Implementuje interfejs API przestrzeń nazw dostawcy zasobów.       |
|*routePrefix*     |    Identyfikator URI do interakcji z dostawcy zasobów.     |
|*serviceLocation*     |   Region tego dostawcy zasobów jest zarejestrowany.      |
|*infraURI*     |   Identyfikator URI dostawcy zasobów wyświetlany jako rola infrastruktury.      |
|*alertSummary*     |   Podsumowanie alertu krytycznego i ostrzeżenia skojarzoną z dostawcą zasobów.      |
|*healthState*     |    Stan kondycji dostawcy zasobów.     |


### <a name="get-resource-health"></a>Pobierz kondycja zasobów

Żądanie pobiera stan kondycji dla określonych zarejestrowanego dostawcy zasobów.

**Żądanie**

|Metoda  |Identyfikator URI żądania  |
|---------|---------|
|GET     |     https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/serviceHealths/{RegistrationID}/resourceHealths?api-version=2016-05-01"    |

**Argumenty**

|Argumenty  |Opis  |
|---------|---------|
|*armendpoint*     |    Punkt końcowy Menedżera zasobów środowiska Azure stosu w formacie https://adminmanagement.{RegionName}.{External FQDN}. Na przykład jeśli zewnętrznej nazwy FQDN jest azurestack.external i nazwa regionu jest lokalny, następnie punktu końcowego Menedżera zasobów jest https://adminmanagement.local.azurestack.external.     |
|*subid*     |Identyfikator subskrypcji użytkownika, który jest wywołania. Ten interfejs API w zapytaniu można użyć tylko z użytkownikiem, który ma uprawnienia do subskrypcji dostawcy domyślnego.         |
|*RegionName*     |  Nazwa regionu Azure stosu wdrożenia.       |
|*wersja interfejsu API*     |  Wersja protokołu, który służy do zgłoszenia tego żądania. Należy użyć 2016-05-01.       |
|*RegistrationID* |Identyfikator rejestracji dla dostawcy określonego zasobu. |

**Odpowiedź**

```http
GET https://adminmanagement.local.azurestack.external/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths/03ccf38f-f6b1-4540-9dc8-ec7b6389ecca /resourceHealths?api-version=2016-05-01 HTTP/1.1
```

```json
{
"value":
[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths/472aaaa6-3f63-43fa-a489-4fd9094e235f/resourceHealths/028c3916-ab86-4e7f-b5c2-0468e607915c",
"name":"028c3916-ab86-4e7f-b5c2-0468e607915c",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/serviceHealths/resourceHealths",
"location":"local",
"tags":{},
"properties":
{"registrationId":"028c3916-ab86-4e7f-b5c2 0468e607915c","namespace":"Microsoft.Fabric.Admin","routePrefix":"/subscriptions/4aa97de3-6b83-4582-86e1 65a5e4d1295b/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local",
"resourceType":"infraRoles",
"resourceName":"Privileged endpoint",
"usageMetrics":[],
"resourceLocation":"local",
"resourceURI":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/Privileged endpoint",
"rpRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"alertSummary":{"criticalAlertCount":0,"warningAlertCount":0},"healthState":"Unknown"
}
}
…
```

**Szczegóły odpowiedzi**

|Argument  |Opis  |
|---------|---------|
|*Identyfikator*     |   Unikatowy identyfikator alertu.      |
|*Nazwa*     |  Wewnętrzna nazwa alertu.       |
|*type*     |  Definicja zasobu.       |
|*location*     |  Nazwa regionu.       |
|*tagów*     |     Tagi zasobów.    |
|*registrationId*     |   Unikatowy Rejestracja dostawcy zasobów.      |
|*resourceType*     |Typ zasobu.        |
|*resourceName*     |   Nazwa zasobu.   |
|*usageMetrics*     |    Metryki użycia zasobów.     |
|*resourceLocation*     |   Nazwa regionu w przypadku, gdy wdrożone.      |
|*resourceURI*     |   Identyfikator URI zasobu.   |
|*alertSummary*     |   Podsumowanie krytyczne i alerty ostrzegawcze, stan kondycji.     |

## <a name="learn-more"></a>Dowiedz się więcej

Informacje o monitorowaniu kondycji wbudowanych, zobacz [monitorowania kondycji i alertów w stosie Azure](azure-stack-monitor-health.md).


## <a name="next-steps"></a>Kolejne kroki

[Integracja z zabezpieczeniami](azure-stack-integrate-security.md)
