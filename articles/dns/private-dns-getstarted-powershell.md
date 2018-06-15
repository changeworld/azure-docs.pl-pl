---
title: Rozpoczynanie pracy ze strefami prywatnymi usługi Azure DNS przy użyciu programu PowerShell | Microsoft Docs
description: Dowiedz się, jak utworzyć prywatną strefę i rekord DNS w usłudze Azure DNS. W tym szczegółowym przewodniku pokazano, jak po raz pierwszy utworzyć prywatną strefę i rekord DNS przy użyciu programu PowerShell i zarządzać nimi.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2017
ms.author: kumud
ms.openlocfilehash: d9e5c9b8caa5349fbcda9b71f7524fb9e99b976c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "30177658"
---
# <a name="get-started-with-azure-dns-private-zones-using-powershell"></a>Rozpoczynanie pracy ze strefami prywatnymi usługi Azure DNS przy użyciu programu PowerShell

W tym artykule przedstawiono kroki umożliwiające utworzenie po raz pierwszy prywatnej strefy i rekordu DNS przy użyciu programu Azure PowerShell.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

Strefa DNS jest używana do hostowania rekordów DNS dla określonej domeny. Aby rozpocząć hostowanie domeny w usłudze Azure DNS, musisz utworzyć strefę DNS dla tej nazwy domeny. Każdy rekord DNS domeny zostanie utworzony w tej strefie DNS. Aby opublikować prywatną strefę DNS w sieci wirtualnej, musisz określić listę sieci wirtualnych, które mogą rozpoznawać rekordy w strefie.  Nazywamy je „sieciami wirtualnymi rozpoznawania”.  Możesz również określić sieć wirtualną, dla której usługa Azure DNS utrzymuje rekordy nazw hosta zawsze, gdy maszyna wirtualna jest tworzona, zmienia protokół internetowy lub jest niszczona.  Nazywamy ją „siecią wirtualną rejestracji”.

# <a name="get-the-preview-powershell-modules"></a>Uzyskiwanie modułu programu PowerShell w wersji zapoznawczej
W poniższych instrukcjach przyjęto, że użytkownik zainstalował już program Azure PowerShell i zalogował się do niego, dzięki czemu ma moduły wymagane dla funkcji stref prywatnych. 

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]

## <a name="create-the-resource-group"></a>Tworzenie grupy zasobów

Przed utworzeniem strefy DNS należy utworzyć dla niej grupę zasobów. W poniższym przykładzie przedstawiono polecenie.

```powershell
New-AzureRMResourceGroup -name MyResourceGroup -location "westus"
```

## <a name="create-a-dns-private-zone"></a>Tworzenie strefy prywatnej DNS

Strefa DNS jest tworzona przy użyciu polecenia cmdlet `New-AzureRmDnsZone` i wartości „Private” parametru ZoneType. Poniższy przykład tworzy strefę DNS o nazwie *contoso.local* w grupie zasobów o nazwie *MyResourceGroup* i udostępnia strefę DNS w sieci wirtualnej o nazwie *MyAzureVnet* . Skorzystaj z tego przykładu, aby utworzyć strefę DNS, podstawiając własne wartości.

Pamiętaj, że w przypadku pominięcia parametru ZoneType strefa zostanie utworzona jako publiczna, dlatego jest on wymagany, jeśli musisz utworzyć strefę prywatną. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyAzureVnet -ResourceGroupName VnetResourceGroup
New-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ZoneType Private -ResolutionVirtualNetworkId @($vnet.Id)
```

Jeśli platforma Azure ma automatycznie tworzyć rekordy nazw hosta w strefie, użyj parametru *RegistrationVirtualNetworkId* zamiast *ResolutionVirtualNetworkId*.  Sieci wirtualne rejestracji są włączane automatycznie do rozpoznawania.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyAzureVnet -ResourceGroupName VnetResourceGroup
New-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ZoneType Private -RegistrationVirtualNetworkId @($vnet.Id)
```

## <a name="create-a-dns-record"></a>Tworzenie rekordu DNS

Zestawy rekordów są tworzone za pomocą polecenia cmdlet `New-AzureRmDnsRecordSet`. W poniższym przykładzie jest tworzony rekord o nazwie względnej „db” w strefie DNS „contoso.local” w grupie zasobów „MyResourceGroup”. W pełni kwalifikowaną nazwą zestawu rekordów jest „db.contoso.local”. Typ rekordu to „A” z adresem IP „10.0.0.4”, a czas wygaśnięcia wynosi 3600 sekund.

```powershell
New-AzureRmDnsRecordSet -Name db -RecordType A -ZoneName contoso.local -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "10.0.0.4")
```

Aby uzyskać informacje o innych typach rekordów, zestawach rekordów zawierających więcej niż jeden rekord oraz sposobie modyfikowania istniejących rekordów, zobacz [Manage DNS records and record sets using Azure PowerShell](dns-operations-recordsets.md) (Zarządzanie rekordami i zestawami rekordów DNS przy użyciu programu Azure PowerShell). 

## <a name="view-records"></a>Wyświetlanie rekordów

Aby wyświetlić listę rekordów DNS w strefie, należy użyć:

```powershell
Get-AzureRmDnsRecordSet -ZoneName contoso.local -ResourceGroupName MyResourceGroup
```

# <a name="list-dns-private-zones"></a>Wyświetlanie listy stref prywatnych DNS

Pomijając nazwę strefy w elemencie `Get-AzureRmDnsZone`, można wyliczyć wszystkie strefy w grupie zasobów. Ta operacja zwraca tablicę obiektów stref.

```powershell
$zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

Pomijając zarówno nazwę strefy, jak i nazwę grupy zasobów w elemencie `Get-AzureRmDnsZone`, można wyliczyć wszystkie strefy w subskrypcji platformy Azure.

```powershell
$zoneList = Get-AzureRmDnsZone
```

## <a name="update-a-dns-private-zone"></a>Aktualizowanie strefy prywatnej DNS

Zmiany w zasobie strefy DNS można wprowadzić przy użyciu polecenia `Set-AzureRmDnsZone`. To polecenie cmdlet nie powoduje aktualizacji żadnego z zestawów rekordów DNS w strefie (zobacz [How to Manage DNS records (Jak zarządzać rekordami DNS)](dns-operations-recordsets.md)). Służy ono wyłącznie do aktualizacji właściwości samego zasobu strefy. Możliwe do zapisu właściwości strefy są obecnie ograniczone do [„tagów” usługi Azure Resource Manager dla zasobu strefy](dns-zones-records.md#tags) oraz parametrów „RegistrationVirtualNetworkId” i „ResolutionVirtualNetworkId” dla stref prywatnych.

Poniższy przykład zastępuje sieć wirtualną rejestracji powiązaną ze strefą nową siecią MyNewAzureVnet.

Pamiętaj, że dla aktualizacji nie można określać parametru ZoneType, w odróżnieniu od tworzenia. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyNewAzureVnet -ResourceGroupName MyResourceGroup
Set-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -RegistrationVirtualNetworkId @($vnet.Id)
```

Poniższy przykład zastępuje sieć wirtualną rozpoznawania powiązaną ze strefą nową siecią „MyNewAzureVnet”.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyNewAzureVnet -ResourceGroupName MyResourceGroup
Set-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ResolutionVirtualNetworkId @($vnet.Id)
```

## <a name="delete-a-dns-private-zone"></a>Usuwanie strefy prywatnej DNS

Strefy prywatne DNS można usuwać przy użyciu polecenia cmdlet `Remove-AzureRmDnsZone`, tak jak strefy publiczne.

> [!NOTE]
> Usunięcie strefy DNS powoduje również usunięcie wszystkich rekordów DNS w strefie. Tej operacji nie można cofnąć. Jeśli strefa DNS jest używana, po jej usunięciu usługi korzystające z tej strefy będą kończyć się niepowodzeniem.
>
>Aby zapobiec przypadkowemu usunięciu strefy, zobacz [jak chronić strefy i rekordy DNS](dns-protect-zones-recordsets.md).

Strefę DNS można usunąć przy użyciu jednego z dwóch poniższych sposobów:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>Określenie strefy przy użyciu nazwy strefy i nazwy grupy zasobów

```powershell
Remove-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyAzureResourceGroup
```

### <a name="specify-the-zone-using-a-zone-object"></a>Określenie strefy przy użyciu obiektu $zone

Strefę do usunięcia można wskazać przy użyciu obiektu `$zone` zwróconego przez element `Get-AzureRmDnsZone`.

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup
Remove-AzureRmDnsZone -Zone $zone
```

Obiekt strefy można również przekazać w potoku zamiast przekazywania jako parametr:

```powershell
Get-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup | Remove-AzureRmDnsZone

```

## <a name="confirmation-prompts"></a>Monity o potwierdzenie

Polecenia cmdlet `New-AzureRmDnsZone`, `Set-AzureRmDnsZone` i `Remove-AzureRmDnsZone` obsługują monity o potwierdzenie.

Polecenia `New-AzureRmDnsZone` i `Set-AzureRmDnsZone` monitują o potwierdzenie, jeśli zmienna preferencji `$ConfirmPreference` programu PowerShell ma wartość `Medium` lub mniejszą. Z powodu potencjalnie dużego wpływu usunięcia strefy DNS polecenie cmdlet `Remove-AzureRmDnsZone` monituje o potwierdzenie, jeśli zmienna `$ConfirmPreference` programu PowerShell ma wartość inną niż `None`.

Ponieważ domyślną wartością elementu `$ConfirmPreference` jest `High`, tylko polecenie `Remove-AzureRmDnsZone` domyślnie monituje o potwierdzenie.

Bieżące ustawienie `$ConfirmPreference` można zastąpić przy użyciu parametru `-Confirm`. W przypadku wybrania elementów `-Confirm` lub `-Confirm:$True` polecenie cmdlet monituje o potwierdzenie przed uruchomieniem. W przypadku wybrania elementu `-Confirm:$False` polecenie cmdlet nie monituje o potwierdzenie.

Aby uzyskać więcej informacji na temat elementów `-Confirm` i `$ConfirmPreference`, zobacz [About Preference Variables (Informacje o zmiennych preferencji)](https://msdn.microsoft.com/powershell/reference/5.1/Microsoft.PowerShell.Core/about/about_Preference_Variables).


## <a name="delete-all-resources"></a>Usuwanie wszystkich zasobów

Aby usunąć wszystkie zasoby utworzone w tym artykule, wykonaj następujące czynności:

```powershell
Remove-AzureRMResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat prywatnych stref DNS, zobacz [Using Azure DNS for private domains (Używanie usługi Azure DNS dla domen prywatnych)](private-dns-overview.md).

Zapoznaj się z informacjami o niektórych typowych scenariuszach, które można realizować za pomocą funkcji stref prywatnych w usłudze Azure DNS, w temacie [Private Zone scenarios (Scenariusze dotyczące stref prywatnych)](./private-dns-scenarios.md).

Aby dowiedzieć się więcej na temat zarządzania rekordami DNS w usłudze Azure DNS, zobacz [Manage DNS records and record sets in Azure DNS using PowerShell](dns-operations-recordsets.md) (Zarządzanie rekordami i zestawami rekordów DNS w usłudze Azure DNS przy użyciu programu PowerShell).

