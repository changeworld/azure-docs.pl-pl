---
title: Tworzenie stref DNS i zestawów rekordów w usłudze Azure DNS przy użyciu zestawu .NET SDK | Dokumentacja firmy Microsoft
description: Jak utworzyć strefy DNS i zestawami rekordów w usłudze Azure DNS przy użyciu zestawu .NET SDK.
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
ms.assetid: eed99b87-f4d4-4fbf-a926-263f7e30b884
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2016
ms.author: victorh
ms.openlocfilehash: a06d629087e853c2578e6d35a2ea90c5a8eff840
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60308947"
---
# <a name="create-dns-zones-and-record-sets-using-the-net-sdk"></a>Tworzenie strefy DNS i zestawów rekordów przy użyciu zestawu .NET SDK

Można automatyzować operacje, aby utworzyć, usunąć lub aktualizacji strefy, zestawów rekordów i rekordów DNS przy użyciu zestawu SDK DNS za pomocą biblioteki .NET — Zarządzanie DNS. Pełny projekt programu Visual Studio jest dostępny [tutaj.](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True)

## <a name="create-a-service-principal-account"></a>Utwórz konto jednostki usługi

Zazwyczaj udzielany jest dostęp programowy do zasobów platformy Azure za pomocą dedykowanego konta, a nie poświadczeń użytkownika. Te dedykowanego konta noszą nazwę kont "jednostki usługi". Aby korzystać z projektem przykładowym i zestawu SDK usługi Azure DNS, należy najpierw utworzyć konto dla nazwy głównej usługi i przypisać jej odpowiednie uprawnienia.

1. Postępuj zgodnie z [w instrukcjach](../active-directory/develop/howto-authenticate-service-principal-powershell.md) można utworzyć konta nazwy głównej usługi (przykładowy projekt zestawu SDK usługi Azure DNS przy założeniu uwierzytelniania opartego na hasłach).
2. Utwórz grupę zasobów ([Oto jak](../azure-resource-manager/resource-group-template-deploy-portal.md)).
3. Uprawnienia konta głównego usługi "Współautor strefy DNS" w grupie zasobów przy użyciu kontroli RBAC platformy Azure ([Oto jak](../role-based-access-control/role-assignments-portal.md).)
4. Jeśli używasz zestawu SDK usługi Azure DNS przykładowy projekt, przeprowadź edycję pliku "program.cs" w następujący sposób:

   * Wstaw poprawne wartości dla `tenantId`, `clientId` (znany także jako identyfikator konta), `secret` (hasło konta nazwy głównej usługi) i `subscriptionId` w przypadku użycia w kroku 1.
   * Wprowadź nazwę grupy zasobów, wybrana w kroku 2.
   * Wprowadź wybraną nazwę strefy DNS.

## <a name="nuget-packages-and-namespace-declarations"></a>Pakiety NuGet i deklaracje przestrzeni nazw

Aby użyć zestawu .NET SDK usługi Azure DNS, musisz zainstalować **biblioteki zarządzania usługi Azure DNS** pakietu NuGet i inne wymagane pakiety platformy Azure.

1. W **programu Visual Studio**, otwórz projekt lub nowy projekt.
2. Przejdź do **narzędzia** **>** **Menedżera pakietów NuGet** **>** **Zarządzaj pakietami NuGet dla Rozwiązanie...** .
3. Kliknij przycisk **Przeglądaj**, Włącz **Uwzględnij wersję wstępną** zaznacz pole wyboru i wpisz **Microsoft.Azure.Management.Dns** w polu wyszukiwania.
4. Wybierz pakiet, a następnie kliknij przycisk **zainstalować** Aby dodać go do projektu programu Visual Studio.
5. Powtórz proces powyżej, aby również Zainstaluj następujące pakiety: **Microsoft.Rest.ClientRuntime.Azure.Authentication** i **Microsoft.Azure.Management.ResourceManager**.

## <a name="add-namespace-declarations"></a>Dodawanie deklaracji przestrzeni nazw

Dodaj następujące deklaracje przestrzeni nazw

```cs
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.Dns;
using Microsoft.Azure.Management.Dns.Models;
```

## <a name="initialize-the-dns-management-client"></a>Inicjowanie klienta zarządzania DNS

`DnsManagementClient` Zawiera metody i właściwości niezbędne do zarządzania rekordami i zestawami stref DNS.  Poniższy kod loguje się do konta głównego usługi i tworzy `DnsManagementClient` obiektu.

```cs
// Build the service credentials and DNS management client
var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, secret);
var dnsClient = new DnsManagementClient(serviceCreds);
dnsClient.SubscriptionId = subscriptionId;
```

## <a name="create-or-update-a-dns-zone"></a>Utwórz lub zaktualizuj strefę DNS

Aby utworzyć strefę DNS, najpierw "Strefa" tworzony jest obiekt zawiera parametry strefy DNS. Ponieważ stref DNS nie są połączone w określonym regionie, lokalizacja jest ustawiona na "global". W tym przykładzie [usługi Azure Resource Manager "tag"](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) jest także dodawane do strefy.

Do faktycznie tworzenia lub aktualizacji strefy w usłudze Azure DNS, strefy obiekt zawierający parametry strefy jest przekazywany do `DnsManagementClient.Zones.CreateOrUpdateAsyc` metody.

> [!NOTE]
> DnsManagementClient obsługuje trzy tryby działania: synchroniczne ("CreateOrUpdate"), asynchroniczny ("CreateOrUpdateAsync"), lub asynchroniczna dzięki dostępowi do odpowiedzi HTTP (CreateOrUpdateWithHttpMessagesAsync).  W zależności od potrzeb aplikacji można wybrać dowolny z następujących trybów.

Usługa Azure DNS obsługuje optymistycznej współbieżności, o nazwie [elementów etag](dns-getstarted-create-dnszone.md). W tym przykładzie określenie "*" dla "If-None-Match" Nagłówek informuje o usłudze Azure DNS, aby utworzyć strefę DNS, jeśli już nie istnieje.  Wywołanie zakończy się niepowodzeniem, jeśli strefa o podanej nazwie już istnieje w grupie danego zasobu.

```cs
// Create zone parameters
var dnsZoneParams = new Zone("global"); // All DNS zones must have location = "global"

// Create an Azure Resource Manager 'tag'.  This is optional.  You can add multiple tags
dnsZoneParams.Tags = new Dictionary<string, string>();
dnsZoneParams.Tags.Add("dept", "finance");

// Create the actual zone.
// Note: Uses 'If-None-Match *' ETAG check, so will fail if the zone exists already.
// Note: For non-async usage, call dnsClient.Zones.CreateOrUpdate(resourceGroupName, zoneName, dnsZoneParams, null, "*")
// Note: For getting the http response, call dnsClient.Zones.CreateOrUpdateWithHttpMessagesAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*")
var dnsZone = await dnsClient.Zones.CreateOrUpdateAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*");
```

## <a name="create-dns-record-sets-and-records"></a>Tworzenie zestawów rekordów DNS i rekordów

Rekordy DNS są zarządzane jako zestaw rekordów. Zestaw rekordów to zestaw rekordów o tej samej nazwie i typie rekordu w strefie.  Nazwa zestawu rekordów jest określana względem nazwę strefy, a nie w pełni kwalifikowanej nazwy DNS.

Aby utworzyć lub zaktualizować zestawu rekordów, jest utworzony i przekazany do obiektu parametrów "Zestawu rekordów" `DnsManagementClient.RecordSets.CreateOrUpdateAsync`. Zgodnie ze strefami DNS są dostępne trzy tryby działania: synchroniczne ("CreateOrUpdate"), asynchroniczny ("CreateOrUpdateAsync"), lub asynchroniczna dzięki dostępowi do odpowiedzi HTTP (CreateOrUpdateWithHttpMessagesAsync).

Podobnie jak w przypadku stref DNS, operacje na zestawach rekordów obejmują obsługę optymistycznej współbieżności.  W tym przykładzie ponieważ określono "If-Match" ani "If-None-Match" zestawu rekordów zawsze jest tworzony.  To wywołanie spowoduje zastąpienie dowolnego istniejącego zestawu rekordów z taką samą nazwę i typ rekordu w tej strefie DNS.

```cs
// Create record set parameters
var recordSetParams = new RecordSet();
recordSetParams.TTL = 3600;

// Add records to the record set parameter object.  In this case, we'll add a record of type 'A'
recordSetParams.ARecords = new List<ARecord>();
recordSetParams.ARecords.Add(new ARecord("1.2.3.4"));

// Add metadata to the record set.  Similar to Azure Resource Manager tags, this is optional and you can add multiple metadata name/value pairs
recordSetParams.Metadata = new Dictionary<string, string>();
recordSetParams.Metadata.Add("user", "Mary");

// Create the actual record set in Azure DNS
// Note: no ETAG checks specified, will overwrite existing record set if one exists
var recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSetParams);
```

## <a name="get-zones-and-record-sets"></a>Strefami i zestawami rekordów

`DnsManagementClient.Zones.Get` i `DnsManagementClient.RecordSets.Get` metody pobierania poszczególnych stref rekordami i zestawami, odpowiednio. Zestawy rekordów są identyfikowane przez ich typu, nazwy i strefy i grupę zasobów, które istnieją w. Strefy są identyfikowane przez ich nazwy i grupę zasobów, które istnieją w.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);
```

## <a name="update-an-existing-record-set"></a>Aktualizowanie istniejącego zestawu rekordów

Do aktualizowania istniejącego zestawu rekordów DNS, najpierw pobrać zestawu rekordów, a następnie zaktualizować zawartość zestawu rekordów, a następnie przesłać zmiany.  W tym przykładzie określamy "Etag" z zestawu rekordów pobrane w parametrze "If-Match". Wywołanie zakończy się niepowodzeniem, jeśli wykonywana równolegle operacja został zmodyfikowany w międzyczasie zestawu rekordów.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);

// Add a new record to the local object.  Note that records in a record set must be unique/distinct
recordSet.ARecords.Add(new ARecord("5.6.7.8"));

// Update the record set in Azure DNS
// Note: ETAG check specified, update will be rejected if the record set has changed in the meantime
recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSet, recordSet.Etag);
```

## <a name="list-zones-and-record-sets"></a>Wyświetlanie listy stref i zestawów rekordów

Aby wyświetlić listę stref, użyj *DnsManagementClient.Zones.List...*  metody, które obsługują wyświetlanie listy wszystkich stref w danej grupy zasobów albo wszystkie strefy w ramach danej subskrypcji platformy Azure (między grupami zasobów.) Aby wyświetlić listę zestawów rekordów, użyj *DnsManagementClient.RecordSets.List...*  metody, które obsługują, albo listę wszystkich zestawów rekordów w określonej strefy lub tylko te zestawy rekordów określonego typu.

Należy pamiętać podczas wyświetlania stref i zestawy rekordów, które wyniki mogą być z podziałem na strony.  Poniższy przykład pokazuje, jak do iterowania po stronach wyników. (Rozmiar sztucznie strony "2" jest używany do wymuszania stronicowania; w praktyce należy pominąć ten parametr i domyślny rozmiar strony używany)

```cs
// Note: in this demo, we'll use a very small page size (2 record sets) to demonstrate paging
// In practice, to improve performance you would use a large page size or just use the system default
int recordSets = 0;
var page = await dnsClient.RecordSets.ListAllInResourceGroupAsync(resourceGroupName, zoneName, "2");
recordSets += page.Count();

while (page.NextPageLink != null)
{
    page = await dnsClient.RecordSets.ListAllInResourceGroupNextAsync(page.NextPageLink);
    recordSets += page.Count();
}
```

## <a name="next-steps"></a>Kolejne kroki

Pobierz [Azure DNS z zestawu .NET SDK przykładowy projekt](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True), która obejmuje dalsze przykłady dotyczące używania usługi Azure DNS zestawu .NET SDK, wraz z przykładami dla innych typów rekordów DNS.
