---
title: Zarządzanie strefami DNS w usłudze Azure DNS — PowerShell | Dokumenty firmy Microsoft
description: Strefy DNS można zarządzać za pomocą programu Azure Powershell. W tym artykule opisano sposób aktualizowania, usuwania i tworzenia stref DNS w usłudze Azure DNS
services: dns
documentationcenter: na
author: rohinkoul
manager: timlt
ms.assetid: a67992ab-8166-4052-9b28-554c5a39e60c
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2018
ms.author: rohink
ms.openlocfilehash: 0120501aab7f0a63721126bfb5b3d04d9deb42fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76936799"
---
# <a name="how-to-manage-dns-zones-using-powershell"></a>Jak zarządzać strefami DNS za pomocą programu PowerShell

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [Powershell](dns-operations-dnszones.md)
> * [Klasyczny interfejs wiersza polecenia platformy Azure](dns-operations-dnszones-cli-nodejs.md)
> * [Interfejs wiersza polecenia platformy Azure](dns-operations-dnszones-cli.md)

W tym artykule pokazano, jak zarządzać strefami DNS przy użyciu programu Azure PowerShell. Strefy DNS można również zarządzać za pomocą wieloplatformowego interfejsu [wiersza polecenia platformy Azure](dns-operations-dnszones-cli.md) lub witryny Azure portal.

Ten przewodnik dotyczy w szczególności publicznych stref DNS. Aby uzyskać informacje na temat używania programu Azure PowerShell do zarządzania strefami prywatnymi w usłudze Azure DNS, zobacz [Wprowadzenie do stref prywatnych dns platformy Azure przy użyciu programu Azure PowerShell](private-dns-getstarted-powershell.md).

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]


## <a name="create-a-dns-zone"></a>Tworzenie strefy DNS

Strefa DNS jest tworzona za pomocą polecenia cmdlet `New-AzureRmDnsZone`.

Poniższy przykład tworzy strefę DNS o nazwie *contoso.com* w grupie zasobów o nazwie *MyResourceGroup:*

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

W poniższym przykładzie pokazano, jak utworzyć strefę DNS z dwoma [tagami Usługi Azure Resource Manager](dns-zones-records.md#tags), project = *demo* i *env = test:*

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

Usługa Azure DNS obsługuje również prywatne strefy DNS.  Aby uzyskać więcej informacji na temat prywatnych stref DNS, zobacz [Using Azure DNS for private domains (Używanie usługi Azure DNS dla domen prywatnych)](private-dns-overview.md). Aby uzyskać przykład sposobu tworzenia prywatnej strefy DNS, zobacz [Rozpoczynanie pracy ze strefami prywatnymi usługi Azure DNS przy użyciu programu PowerShell](./private-dns-getstarted-powershell.md).

## <a name="get-a-dns-zone"></a>Pobierz strefę DNS

Aby pobrać strefę DNS, `Get-AzureRmDnsZone` użyj polecenia cmdlet. Ta operacja zwraca obiekt strefy DNS odpowiadający istniejącej strefie w usłudze Azure DNS. Obiekt zawiera dane o strefie (takie jak liczba zestawów rekordów), ale `Get-AzureRmDnsRecordSet`nie zawiera samych zestawów rekordów (patrz ).

```powershell
Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup

Name                  : contoso.com
ResourceGroupName     : myresourcegroup
Etag                  : 00000003-0000-0000-8ec2-f4879750d201
Tags                  : {project, env}
NameServers           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org.,
                        ns4-01.azure-dns.info.}
NumberOfRecordSets    : 2
MaxNumberOfRecordSets : 5000
```

## <a name="list-dns-zones"></a>Wyświetlanie listy stref DNS

Pomijając nazwę strefy w elemencie `Get-AzureRmDnsZone`, można wyliczyć wszystkie strefy w grupie zasobów. Ta operacja zwraca tablicę obiektów stref.

```powershell
$zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

Pomijając zarówno nazwę strefy, jak i nazwę grupy zasobów w elemencie `Get-AzureRmDnsZone`, można wyliczyć wszystkie strefy w subskrypcji platformy Azure.

```powershell
$zoneList = Get-AzureRmDnsZone
```

## <a name="update-a-dns-zone"></a>Aktualizowanie strefy DNS

Zmiany w zasobie strefy DNS można wprowadzić przy użyciu polecenia `Set-AzureRmDnsZone`. To polecenie cmdlet nie powoduje aktualizacji żadnego z zestawów rekordów DNS w strefie (zobacz [How to Manage DNS records (Jak zarządzać rekordami DNS)](dns-operations-recordsets.md)). Służy ono wyłącznie do aktualizacji właściwości samego zasobu strefy. Właściwości strefy zapisywalne są obecnie ograniczone do ["tagów" usługi Azure Resource Manager dla zasobu strefy.](dns-zones-records.md#tags)

Aby zaktualizować strefę DNS, użyj jednego z następujących dwóch sposobów:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group"></a>Określanie strefy przy użyciu nazwy strefy i grupy zasobów

Takie podejście zastępuje istniejące znaczniki strefy określonymi wartościami.

```powershell
Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

### <a name="specify-the-zone-using-a-zone-object"></a>Określenie strefy przy użyciu obiektu $zone

Takie podejście pobiera istniejący obiekt strefy, modyfikuje znaczniki, a następnie zatwierdza zmiany. W ten sposób istniejące znaczniki mogą być zachowywane.

```powershell
# Get the zone object
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup

# Remove an existing tag
$zone.Tags.Remove("project")

# Add a new tag
$zone.Tags.Add("status","approved")

# Commit changes
Set-AzureRmDnsZone -Zone $zone
```

Podczas `Set-AzureRmDnsZone` korzystania z obiektu [$zone, Etag kontroli](dns-zones-records.md#etags) są używane w celu zapewnienia równoczesnych zmian nie są zastępowane. Można użyć przełącznika opcjonalnego, `-Overwrite` aby pominąć te kontrole.

## <a name="delete-a-dns-zone"></a>Usuwanie strefy DNS

Strefy DNS można usunąć `Remove-AzureRmDnsZone` za pomocą polecenia cmdlet.

> [!NOTE]
> Usunięcie strefy DNS powoduje również usunięcie wszystkich rekordów DNS w strefie. Tej operacji nie można cofnąć. Jeśli strefa DNS jest używana, po jej usunięciu usługi korzystające z tej strefy będą kończyć się niepowodzeniem.
>
>Aby zapobiec przypadkowemu usunięciu strefy, zobacz [jak chronić strefy i rekordy DNS](dns-protect-zones-recordsets.md).


Strefę DNS można usunąć przy użyciu jednego z dwóch poniższych sposobów:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>Określenie strefy przy użyciu nazwy strefy i nazwy grupy zasobów

```powershell
Remove-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

### <a name="specify-the-zone-using-a-zone-object"></a>Określenie strefy przy użyciu obiektu $zone

Strefę do usunięcia można wskazać przy użyciu obiektu `$zone` zwróconego przez element `Get-AzureRmDnsZone`.

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsZone -Zone $zone
```

Obiekt strefy można również przekazać w potoku zamiast przekazywania jako parametr:

```powershell
Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsZone

```

Podobnie `Set-AzureRmDnsZone`jak w przypadku , `$zone` określenie strefy za pomocą obiektu umożliwia etag kontroli, aby upewnić się, że równoczesne zmiany nie są usuwane. Użyj `-Overwrite` przełącznika, aby pominąć te kontrole.

## <a name="confirmation-prompts"></a>Monity o potwierdzenie

Polecenia cmdlet `New-AzureRmDnsZone`, `Set-AzureRmDnsZone` i `Remove-AzureRmDnsZone` obsługują monity o potwierdzenie.

Polecenia `New-AzureRmDnsZone` i `Set-AzureRmDnsZone` monitują o potwierdzenie, jeśli zmienna preferencji `$ConfirmPreference` programu PowerShell ma wartość `Medium` lub mniejszą. Z powodu potencjalnie dużego wpływu usunięcia strefy DNS polecenie cmdlet `Remove-AzureRmDnsZone` monituje o potwierdzenie, jeśli zmienna `$ConfirmPreference` programu PowerShell ma wartość inną niż `None`.

Ponieważ domyślną wartością elementu `$ConfirmPreference` jest `High`, tylko polecenie `Remove-AzureRmDnsZone` domyślnie monituje o potwierdzenie.

Bieżące ustawienie `$ConfirmPreference` można zastąpić przy użyciu parametru `-Confirm`. W przypadku wybrania elementów `-Confirm` lub `-Confirm:$True` polecenie cmdlet monituje o potwierdzenie przed uruchomieniem. W przypadku wybrania elementu `-Confirm:$False` polecenie cmdlet nie monituje o potwierdzenie.

Aby uzyskać więcej informacji na temat elementów `-Confirm` i `$ConfirmPreference`, zobacz [About Preference Variables (Informacje o zmiennych preferencji)](/powershell/module/microsoft.powershell.core/about/about_preference_variables).

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [zarządzać zestawami rekordów i rekordami](dns-operations-recordsets.md) w strefie DNS.
<br>
Dowiedz się, jak [delegować domenę do usługi Azure DNS](dns-domain-delegation.md).
<br>
Przejrzyj [dokumentację referencyjną programu Azure DNS PowerShell](/powershell/module/azurerm.dns).

