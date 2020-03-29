---
title: Tworzenie stref DNS i zestawów rekordów przy użyciu sdk .NET
titleSuffix: Azure DNS
description: W tej ścieżce szkoleniowej rozpocznij tworzenie stref DNS i zestawów rekordów w usłudze Azure DNS przy użyciu sdk .NET.
services: dns
documentationcenter: na
author: rohinkoul
manager: kumudD
ms.assetid: eed99b87-f4d4-4fbf-a926-263f7e30b884
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2016
ms.author: rohink
ms.openlocfilehash: c497209e456ff838786edaa19e46ebc5c1858d5f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938864"
---
# <a name="create-dns-zones-and-record-sets-using-the-net-sdk"></a>Tworzenie stref DNS i zestawów rekordów przy użyciu sdk .NET

Operacje w celu tworzenia, usuwania lub aktualizowania stref DNS, zestawów rekordów i rekordów można zautomatyzować przy użyciu pakietu DNS SDK z biblioteką zarządzania DNS .NET. Pełny projekt programu Visual Studio jest dostępny [tutaj.](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True)

## <a name="create-a-service-principal-account"></a>Tworzenie konta głównego dostawcy usługi

Zazwyczaj programowy dostęp do zasobów platformy Azure jest przyznawany za pośrednictwem konta dedykowanego, a nie własnych poświadczeń użytkownika. Te dedykowane konta są nazywane kontami "jednostki usługi". Aby użyć przykładowego projektu azure DNS SDK, należy najpierw utworzyć konto jednostki usługi i przypisać mu poprawne uprawnienia.

1. Postępuj zgodnie [z tymi instrukcjami,](../active-directory/develop/howto-authenticate-service-principal-powershell.md) aby utworzyć konto głównej usługi (przykładowy projekt azure DNS SDK zakłada uwierzytelnianie oparte na hasłach).
2. Tworzenie grupy zasobów ([oto jak](../azure-resource-manager/templates/deploy-portal.md)).
3. Użyj narzędzia Azure RBAC, aby udzielić grupie zasobów uprawnień głównego konta usługi "Współautor strefy DNS"[(oto jak](../role-based-access-control/role-assignments-portal.md)to zrobić).
4. Jeśli używasz przykładowego projektu azure DNS SDK, edytuj plik "program.cs" w następujący sposób:

   * Wstaw poprawne wartości `tenantId` `clientId` dla , (znany również `secret` jako identyfikator konta), `subscriptionId` (hasło konta głównego usługi) i używane w kroku 1.
   * Wprowadź nazwę grupy zasobów wybraną w kroku 2.
   * Wprowadź wybraną nazwę strefy DNS.

## <a name="nuget-packages-and-namespace-declarations"></a>Pakiety NuGet i deklaracje obszaru nazw

Aby korzystać z zestawu Azure DNS .NET SDK, należy zainstalować pakiet NuGet **biblioteki zarządzania usługi Azure DNS** i inne wymagane pakiety platformy Azure.

1. W **programie Visual Studio**otwórz projekt lub nowy projekt.
2. Przejdź do **narzędzia** **>** **NuGet Package Manager** **>** **Manage Pakiety NuGet dla rozwiązania ...**.
3. Kliknij **przycisk Przeglądaj**, włącz pole wyboru **Dołącz wydanie wstępne** i wpisz w polu wyszukiwania pozycję **Microsoft.Azure.Management.Dns.**
4. Wybierz pakiet i kliknij przycisk **Zainstaluj,** aby dodać go do projektu programu Visual Studio.
5. Powtórz powyższy proces, aby zainstalować również następujące pakiety: **Microsoft.Rest.ClientRuntime.Azure.Authentication** i **Microsoft.Azure.Management.ResourceManager**.

## <a name="add-namespace-declarations"></a>Dodawanie deklaracji przestrzeni nazw

Dodawanie następujących deklaracji obszaru nazw

```cs
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.Dns;
using Microsoft.Azure.Management.Dns.Models;
```

## <a name="initialize-the-dns-management-client"></a>Inicjowanie klienta zarządzania systemem DNS

Zawiera `DnsManagementClient` metody i właściwości niezbędne do zarządzania strefami DNS i zestawami rekordów.  Poniższy kod loguje się do konta `DnsManagementClient` jednostki usługi i tworzy obiekt.

```cs
// Build the service credentials and DNS management client
var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, secret);
var dnsClient = new DnsManagementClient(serviceCreds);
dnsClient.SubscriptionId = subscriptionId;
```

## <a name="create-or-update-a-dns-zone"></a>Tworzenie lub aktualizowanie strefy DNS

Aby utworzyć strefę DNS, najpierw tworzony jest obiekt "Strefa", który zawiera parametry strefy DNS. Ponieważ strefy DNS nie są połączone z określonym regionem, lokalizacja jest ustawiona na "globalną". W tym przykładzie ["tag" usługi Azure Resource Manager](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) jest również dodawany do strefy.

Aby faktycznie utworzyć lub zaktualizować strefę w usłudze Azure DNS, `DnsManagementClient.Zones.CreateOrUpdateAsyc` obiekt strefy zawierający parametry strefy jest przekazywany do metody.

> [!NOTE]
> DnsManagementClient obsługuje trzy tryby działania: synchroniczne ("CreateOrUpdate"), asynchroniczne ("CreateOrUpdateAsync") lub asynchroniczne z dostępem do odpowiedzi HTTP ("CreateOrUpdateWithHttpMessagesAsync").  W zależności od potrzeb aplikacji można wybrać dowolny z tych trybów.

Usługa Azure DNS obsługuje optymistyczną współbieżność, zwaną [Etags](dns-getstarted-create-dnszone.md). W tym przykładzie określenie "*" dla nagłówka "If-None-Match" informuje usługę Azure DNS o utworzeniu strefy DNS, jeśli jeszcze nie istnieje.  Wywołanie kończy się niepowodzeniem, jeśli strefa o podanej nazwie już istnieje w danej grupie zasobów.

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

## <a name="create-dns-record-sets-and-records"></a>Tworzenie zestawów rekordów i rekordów DNS

Rekordy DNS są zarządzane jako zestaw rekordów. Zestaw rekordów to zestaw rekordów o tej samej nazwie i typie rekordu w strefie.  Nazwa zestawu rekordów jest względna do nazwy strefy, a nie w pełni kwalifikowanej nazwy DNS.

Aby utworzyć lub zaktualizować zestaw rekordów, tworzony jest obiekt `DnsManagementClient.RecordSets.CreateOrUpdateAsync`parametrów "RecordSet" i przekazywany do programu . Podobnie jak w strefach DNS, istnieją trzy tryby działania: synchroniczne ("CreateOrUpdate"), asynchroniczne ("CreateOrUpdateAsync") lub asynchroniczne z dostępem do odpowiedzi HTTP ("CreateOrUpdateWithHttpMessagesAsync").

Podobnie jak w przypadku stref DNS, operacje w zestawach rekordów obejmują obsługę optymistycznej współbieżności.  W tym przykładzie, ponieważ nie określono ani "If-Match" ani "If-None-Match", zestaw rekordów jest zawsze tworzony.  To wywołanie zastępuje istniejący zestaw rekordów o tej samej nazwie i typie rekordu w tej strefie DNS.

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

## <a name="get-zones-and-record-sets"></a>Pobierz strefy i zestawy rekordów

Metody `DnsManagementClient.Zones.Get` `DnsManagementClient.RecordSets.Get` i metody pobierają odpowiednio poszczególne strefy i zestawy rekordów. Zestawy rekordów są identyfikowane przez ich typ, nazwę oraz strefę i grupę zasobów, w których istnieją. Strefy są identyfikowane według ich nazwy i grupy zasobów, w których istnieją.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);
```

## <a name="update-an-existing-record-set"></a>Aktualizowanie istniejącego zestawu rekordów

Aby zaktualizować istniejący zestaw rekordów DNS, najpierw pobierz zestaw rekordów, a następnie zaktualizuj zawartość zestawu rekordów, a następnie prześlij zmianę.  W tym przykładzie określamy "Etag" z pobranego rekordu ustawionego w parametrze "If-Match". Wywołanie kończy się niepowodzeniem, jeśli jednoczesna operacja zmodyfikowała rekord ustawiony w międzyczasie.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);

// Add a new record to the local object.  Note that records in a record set must be unique/distinct
recordSet.ARecords.Add(new ARecord("5.6.7.8"));

// Update the record set in Azure DNS
// Note: ETAG check specified, update will be rejected if the record set has changed in the meantime
recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSet, recordSet.Etag);
```

## <a name="list-zones-and-record-sets"></a>Lista stref i zestawów rekordów

Aby wyświetlić listę stref, użyj *dnsmanagementClient.Zones.List...* metody, które obsługują listę wszystkich stref w danej grupie zasobów lub wszystkich stref w danej subskrypcji platformy Azure (w grupach zasobów).) Aby wyświetlić listę zestawów rekordów, należy użyć metody *DnsManagementClient.RecordSets.List...,* które obsługują listę wszystkich zestawów rekordów w danej strefie lub tylko te zestawy rekordów określonego typu.

Należy pamiętać, że w przypadku wyświetlania stref i zestawów rekordów wyniki mogą być na marginesie.  W poniższym przykładzie pokazano, jak iterować za pośrednictwem stron wyników. (Sztucznie mały rozmiar strony "2" jest używany do wymuszania stronicowania; w praktyce ten parametr należy pominąć i użyć domyślnego rozmiaru strony).

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

## <a name="next-steps"></a>Następne kroki

Pobierz [przykładowy projekt usługi Azure DNS .NET SDK](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True), który zawiera dalsze przykłady dotyczące używania sdk azure DNS .NET, w tym przykłady dla innych typów rekordów DNS.
