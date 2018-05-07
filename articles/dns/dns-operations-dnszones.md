---
title: Zarządzanie strefami DNS w usłudze Azure DNS - PowerShell | Dokumentacja firmy Microsoft
description: Możesz zarządzać stref DNS przy użyciu programu Azure Powershell. W tym artykule opisano sposób aktualizowanie, usuwanie i tworzenie stref DNS w usłudze Azure DNS
services: dns
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: a67992ab-8166-4052-9b28-554c5a39e60c
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2018
ms.author: kumud
ms.openlocfilehash: e7b0bc32d3fa8fbcf73298b6988655fca7cfa793
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="how-to-manage-dns-zones-using-powershell"></a>Jak zarządzać stref DNS przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [Program PowerShell](dns-operations-dnszones.md)
> * [Interfejs wiersza polecenia platformy Azure 1.0](dns-operations-dnszones-cli-nodejs.md)
> * [Interfejs wiersza polecenia platformy Azure 2.0](dns-operations-dnszones-cli.md)

W tym artykule przedstawiono sposób zarządzania stref DNS przy użyciu programu Azure PowerShell. Można również zarządzać stref DNS przy użyciu wielu platform [interfejsu wiersza polecenia Azure](dns-operations-dnszones-cli.md) lub w portalu Azure.

Ten przewodnik dotyczy w szczególności stref w publicznym systemie DNS. Informacje dotyczące używania programu Azure PowerShell do zarządzania prywatnego stref w usłudze Azure DNS, zobacz [wprowadzenie stref DNS prywatnego Azure przy użyciu programu Azure PowerShell](private-dns-getstarted-powershell.md).

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]


## <a name="create-a-dns-zone"></a>Tworzenie strefy DNS

Strefa DNS jest tworzona za pomocą polecenia cmdlet `New-AzureRmDnsZone`.

Poniższy przykład tworzy strefę DNS o nazwie *contoso.com* w grupie zasobów o nazwie *MyResourceGroup*:

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

Poniższy przykład przedstawia sposób tworzenia strefy DNS przy użyciu dwóch [znaczniki usługi Azure Resource Manager](dns-zones-records.md#tags), *project = demo* i *env = test*:

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

Usługa Azure DNS teraz obsługuje również prywatne strefy DNS (obecnie w wersji zapoznawczej).  Aby uzyskać więcej informacji na temat prywatnych stref DNS, zobacz [Using Azure DNS for private domains (Używanie usługi Azure DNS dla domen prywatnych)](private-dns-overview.md). Aby uzyskać przykład sposobu tworzenia prywatnej strefy DNS, zobacz [Rozpoczynanie pracy ze strefami prywatnymi usługi Azure DNS przy użyciu programu PowerShell](./private-dns-getstarted-powershell.md).

## <a name="get-a-dns-zone"></a>Pobierz strefę DNS

Aby uzyskać dostęp do strefy DNS, należy użyć `Get-AzureRmDnsZone` polecenia cmdlet. Ta operacja zwraca obiekt strefy DNS odpowiadającej istniejącej strefy w usłudze Azure DNS. Obiekt zawiera dane dotyczące strefy (na przykład liczba zestawów rekordów), ale nie zawiera zestawów rekordów, same (zobacz `Get-AzureRmDnsRecordSet`).

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

Zmiany w zasobie strefy DNS można wprowadzić przy użyciu polecenia `Set-AzureRmDnsZone`. To polecenie cmdlet nie powoduje aktualizacji żadnego z zestawów rekordów DNS w strefie (zobacz [How to Manage DNS records (Jak zarządzać rekordami DNS)](dns-operations-recordsets.md)). Służy ono wyłącznie do aktualizacji właściwości samego zasobu strefy. Właściwości strefy zapisu są obecnie ograniczone do [usługi Azure Resource Manager "tagów" dla zasobu strefy](dns-zones-records.md#tags).

Użyj jednej z dwóch sposobów do aktualizacji strefy DNS:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group"></a>Określ strefę za pomocą grupy strefy nazwy i zasobów

Ta metoda zastępuje istniejące znaczniki strefy podane wartości.

```powershell
Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

### <a name="specify-the-zone-using-a-zone-object"></a>Określenie strefy przy użyciu obiektu $zone

Takie podejście pobiera istniejący obiekt strefy, modyfikuje tagi, a następnie zatwierdza zmiany. W ten sposób istniejące znaczniki będzie możliwe.

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

Korzystając z `Set-AzureRmDnsZone` z obiektem $zone [sprawdza Etag](dns-zones-records.md#etags) są używane do zapewnienia równoległe zmiany nie zostaną zastąpione. Można użyć opcjonalnego `-Overwrite` przełącznik, aby pominąć kontrole.

## <a name="delete-a-dns-zone"></a>Usuń strefę DNS

Można usunąć strefy DNS przy użyciu `Remove-AzureRmDnsZone` polecenia cmdlet.

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

Jak `Set-AzureRmDnsZone`, określając przy użyciu strefy `$zone` obiektu umożliwia Etag kontroli w celu zapewnienia, równoległe zmiany nie zostaną usunięte. Użyj `-Overwrite` przełącznik, aby pominąć kontrole.

## <a name="confirmation-prompts"></a>Monity o potwierdzenie

Polecenia cmdlet `New-AzureRmDnsZone`, `Set-AzureRmDnsZone` i `Remove-AzureRmDnsZone` obsługują monity o potwierdzenie.

Polecenia `New-AzureRmDnsZone` i `Set-AzureRmDnsZone` monitują o potwierdzenie, jeśli zmienna preferencji `$ConfirmPreference` programu PowerShell ma wartość `Medium` lub mniejszą. Z powodu potencjalnie dużego wpływu usunięcia strefy DNS polecenie cmdlet `Remove-AzureRmDnsZone` monituje o potwierdzenie, jeśli zmienna `$ConfirmPreference` programu PowerShell ma wartość inną niż `None`.

Ponieważ domyślną wartością elementu `$ConfirmPreference` jest `High`, tylko polecenie `Remove-AzureRmDnsZone` domyślnie monituje o potwierdzenie.

Bieżące ustawienie `$ConfirmPreference` można zastąpić przy użyciu parametru `-Confirm`. W przypadku wybrania elementów `-Confirm` lub `-Confirm:$True` polecenie cmdlet monituje o potwierdzenie przed uruchomieniem. W przypadku wybrania elementu `-Confirm:$False` polecenie cmdlet nie monituje o potwierdzenie.

Aby uzyskać więcej informacji na temat elementów `-Confirm` i `$ConfirmPreference`, zobacz [About Preference Variables (Informacje o zmiennych preferencji)](https://msdn.microsoft.com/powershell/reference/5.1/Microsoft.PowerShell.Core/about/about_Preference_Variables).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [zarządzać zestawów rekordów i rekordami](dns-operations-recordsets.md) w strefie DNS.
<br>
Dowiedz się, jak [Delegowanie domeny do usługi Azure DNS](dns-domain-delegation.md).
<br>
Przegląd [dokumentacji programu PowerShell usługi Azure DNS](/powershell/module/azurerm.dns).

