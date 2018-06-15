---
title: Tworzenie strefy DNS i zestawy rekordów w usłudze Azure DNS przy użyciu zestawu .NET SDK | Dokumentacja firmy Microsoft
description: Jak utworzyć strefy DNS i zestawy rekordów w usłudze Azure DNS przy użyciu zestawu .NET SDK.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
ms.assetid: eed99b87-f4d4-4fbf-a926-263f7e30b884
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2016
ms.author: kumud
ms.openlocfilehash: 645bf755cc3cb4036104145765196fc97039c551
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
ms.locfileid: "32772300"
---
# <a name="create-dns-zones-and-record-sets-using-the-net-sdk"></a>Tworzenie strefy DNS i zestawy rekordów przy użyciu zestawu .NET SDK

Można automatyzować operacje można utworzyć, usunąć lub zaktualizować stref, zestawów rekordów i rekordy DNS przy użyciu zestawu SDK DNS z biblioteki .NET zarządzania DNS. Pełna projektu programu Visual Studio jest dostępny [tutaj.](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True)

## <a name="create-a-service-principal-account"></a>Utwórz konto główne usługi

Zazwyczaj otrzymuje programowy dostęp do zasobów platformy Azure za pomocą dedykowanego konta, a nie poświadczenia użytkownika. Te dedykowanego konta są nazywane "nazwy głównej usługi" kont. Aby użyć zestawu SDK usługi Azure DNS przykładowy projekt, należy najpierw utworzyć konto główne usługi i przypisać jej odpowiednie uprawnienia.

1. Postępuj zgodnie z [tych instrukcji](../azure-resource-manager/resource-group-authenticate-service-principal.md) do utworzenia konta głównego usługi (przykładowy projekt zestawu SDK usługi Azure DNS założono uwierzytelniania opartego na hasłach).
2. Utwórz grupę zasobów ([Oto jak](../azure-resource-manager/resource-group-template-deploy-portal.md)).
3. Użycie funkcji RBAC Azure można udzielić uprawnienia "Współautora strefy DNS" do grupy zasobów konta głównego usługi ([Oto jak](../role-based-access-control/role-assignments-portal.md).)
4. Jeśli przy użyciu zestawu SDK usługi Azure DNS przykładowy projekt, przeprowadź edycję pliku "program.cs" w następujący sposób:

   * Wstaw poprawne wartości dla identyfikatora dzierżawcy, clientId (znanej także jako identyfikator konta), klucz tajny (hasło konta głównego usługi) i identyfikator subskrypcji w kroku 1.
   * Wprowadź nazwę grupy zasobów, wybrana w kroku 2.
   * Wprowadź nazwę strefy DNS.

## <a name="nuget-packages-and-namespace-declarations"></a>Pakiety NuGet i deklaracje przestrzeni nazw

Aby użyć zestawu .NET SDK usługi Azure DNS, musisz zainstalować **biblioteki zarządzania usługi Azure DNS** pakietu NuGet i inne wymagane pakiety platformy Azure.

1. W **programu Visual Studio**, otwórz projekt lub nowego projektu.
2. Przejdź do **narzędzia** **>** **Menedżera pakietów NuGet** **>** **Zarządzaj pakietami NuGet dla rozwiązania...** .
3. Kliknij przycisk **Przeglądaj**, Włącz **Uwzględnij wersję wstępną** wyboru i wpisz **Microsoft.Azure.Management.Dns** w polu wyszukiwania.
4. Wybierz pakiet, a następnie kliknij przycisk **zainstalować** ją dodać do projektu programu Visual Studio.
5. Powtórz proces powyżej, aby również zainstalować następujących pakietów: **Microsoft.Rest.ClientRuntime.Azure.Authentication** i **Microsoft.Azure.Management.ResourceManager**.

## <a name="add-namespace-declarations"></a>Dodawanie deklaracji przestrzeni nazw

Dodaj następujące deklaracje przestrzeni nazw

```cs
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.Dns;
using Microsoft.Azure.Management.Dns.Models;
```

## <a name="initialize-the-dns-management-client"></a>Inicjowanie klienta zarządzania DNS

*DnsManagementClient* zawiera metody i właściwości niezbędne do zarządzania strefy DNS i zestawy rekordów.  Poniższy kod loguje się do konta głównego usługi i tworzy obiekt DnsManagementClient.

```cs
// Build the service credentials and DNS management client
var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, secret);
var dnsClient = new DnsManagementClient(serviceCreds);
dnsClient.SubscriptionId = subscriptionId;
```

## <a name="create-or-update-a-dns-zone"></a>Utwórz lub zaktualizuj strefę DNS

Aby utworzyć strefę DNS, najpierw "Strefy" tworzony jest obiekt zawierający parametry strefy DNS. Ponieważ strefy DNS nie są połączone z określonego regionu, lokalizacja jest ustawiona na "global". W tym przykładzie [usługi Azure Resource Manager "tag"](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) jest także dodawane do strefy.

Faktycznie Utwórz lub zaktualizuj strefę w usłudze Azure DNS, obiekt strefy zawierających parametry strefy jest przekazywany do *DnsManagementClient.Zones.CreateOrUpdateAsyc* metody.

> [!NOTE]
> DnsManagementClient obsługuje trzy tryby działania: synchroniczne ("CreateOrUpdate"), asynchroniczne ("CreateOrUpdateAsync"), lub asynchroniczna z dostępem do odpowiedzi HTTP (CreateOrUpdateWithHttpMessagesAsync).  Można wybrać dowolną z tych trybów, w zależności od potrzeb aplikacji.

Usługa DNS platformy Azure obsługuje optymistycznej współbieżności, nazywany [elementy etag](dns-getstarted-create-dnszone.md). W tym przykładzie określenie "*" dla "If-None-Match" nagłówka informuje usługi Azure DNS, aby utworzyć strefę DNS, jeśli już nie istnieje.  Wywołanie zakończy się niepowodzeniem, jeśli strefę o podanej nazwie już istnieje w określonej grupy zasobów.

```cs
// Create zone parameters
var dnsZoneParams = new Zone("global"); // All DNS zones must have location = "global"

// Create a Azure Resource Manager 'tag'.  This is optional.  You can add multiple tags
dnsZoneParams.Tags = new Dictionary<string, string>();
dnsZoneParams.Tags.Add("dept", "finance");

// Create the actual zone.
// Note: Uses 'If-None-Match *' ETAG check, so will fail if the zone exists already.
// Note: For non-async usage, call dnsClient.Zones.CreateOrUpdate(resourceGroupName, zoneName, dnsZoneParams, null, "*")
// Note: For getting the http response, call dnsClient.Zones.CreateOrUpdateWithHttpMessagesAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*")
var dnsZone = await dnsClient.Zones.CreateOrUpdateAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*");
```

## <a name="create-dns-record-sets-and-records"></a>Tworzenie zestawów rekordów DNS i rekordów

Rekordy DNS są zarządzane jako zestawu rekordów. Zestaw rekordów jest zestaw rekordów o tej samej nazwie i typ rekordu w strefie.  Nazwa zestawu rekordów jest określana względem nazwę strefy, a nie w pełni kwalifikowana nazwa DNS.

Utwórz lub zaktualizuj zestaw rekordów, obiekt parametrów "Zestawu rekordów" jest utworzony i przekazane do *DnsManagementClient.RecordSets.CreateOrUpdateAsync*. Zgodnie ze strefami DNS są dostępne trzy tryby działania: synchroniczne ("CreateOrUpdate"), asynchroniczne ("CreateOrUpdateAsync"), lub asynchroniczna z dostępem do odpowiedzi HTTP (CreateOrUpdateWithHttpMessagesAsync).

Podobnie jak w przypadku stref DNS, operacje na zestawów rekordów obejmuje obsługę optymistycznej współbieżności.  W tym przykładzie ponieważ "If-Match" i "If-None-Match" nie są określone, zestawu rekordów zawsze jest tworzone.  Tego wywołania spowoduje zastąpienie wszelkich istniejącego zestawu rekordów z taką samą nazwę i typ rekordu w tej strefie DNS.

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

## <a name="get-zones-and-record-sets"></a>Pobierz stref i zestawy rekordów

*DnsManagementClient.Zones.Get* i *DnsManagementClient.RecordSets.Get* metody pobrać odpowiednio poszczególnych stref i zestawy rekordów. Zestawy rekordów są identyfikowane przez ich typu, nazwy i grupie strefy i zasobów, które istnieją w. Strefy są identyfikowane przez ich nazwy i grupy zasobów, które istnieją w.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);
```

## <a name="update-an-existing-record-set"></a>Aktualizowanie istniejącego zestawu rekordów

Aby zaktualizować istniejącego zestawu rekordów DNS, najpierw pobrać zestawu rekordów, a następnie zaktualizować zawartość zestawu rekordów, a następnie przesłać zmiany.  W tym przykładzie określono "Tagu" z zestawu rekordów pobrane w parametrze "If-Match". Wywołanie zakończy się niepowodzeniem, jeśli operacja współbieżna został zmodyfikowany w tym czasie zestawu rekordów.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);

// Add a new record to the local object.  Note that records in a record set must be unique/distinct
recordSet.ARecords.Add(new ARecord("5.6.7.8"));

// Update the record set in Azure DNS
// Note: ETAG check specified, update will be rejected if the record set has changed in the meantime
recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSet, recordSet.Etag);
```

## <a name="list-zones-and-record-sets"></a>Lista stref i zestawy rekordów

Aby wyświetlić strefy, użyj *DnsManagementClient.Zones.List...*  metody, które obsługują wyświetlanie listy wszystkich stref w danej grupy zasobów albo wszystkie strefy w ramach danej subskrypcji platformy Azure (za pośrednictwem grup zasobów.) Aby wyświetlić listę zestawów rekordów, użyj *DnsManagementClient.RecordSets.List...*  metody, które obsługują, albo listę wszystkich zestawów rekordów w strefie danego lub tylko tych zestawów rekordów określonego typu.

Należy pamiętać, podczas wyświetlania stref i zestawy rekordów, które powoduje może zostać podzielony na strony.  Poniższy przykład pokazuje, jak do iterowania po stronach wyników. (Rozmiar sztucznie strony "2", można wymusić stronicowania; w praktyce należy pominąć ten parametr i używany domyślny rozmiar strony.)

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

Pobierz [zestawu SDK usługi Azure DNS .NET przykładowy projekt](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True), który zawiera dalsze przykłady sposobu korzystania z usługi Azure DNS zestawu .NET SDK, wraz z przykładami dla innych typów rekordów DNS.
