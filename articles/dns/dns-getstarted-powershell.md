---
title: Szybki start — tworzenie strefy i rekordu usługi Azure DNS przy użyciu programu Azure PowerShell
description: Dowiedz się, jak utworzyć strefę i rekord DNS w usłudze Azure DNS. W tym szczegółowym przewodniku Szybki start pokazano, jak po raz pierwszy utworzyć strefę i rekord DNS przy użyciu programu Azure PowerShell.
services: dns
author: vhorne
ms.service: dns
ms.topic: quickstart
ms.date: 12/4/2018
ms.author: victorh
ms.openlocfilehash: 839c97ccccbc1ce2cf646afcd27894a190eda1b0
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2019
ms.locfileid: "56000896"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-powershell"></a>Szybki start: Tworzenie strefy i rekordu usługi Azure DNS przy użyciu programu Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W tym przewodniku Szybki start utworzysz po raz pierwszy strefę i rekord DNS przy użyciu programu Azure PowerShell. Te kroki można również wykonać przy użyciu witryny [Azure Portal](dns-getstarted-portal.md) lub [interfejsu wiersza polecenia platformy Azure](dns-getstarted-cli.md). 

Strefa DNS jest używana do hostowania rekordów DNS dla określonej domeny. Aby rozpocząć hostowanie domeny w usłudze Azure DNS, musisz utworzyć strefę DNS dla tej nazwy domeny. Każdy rekord DNS domeny zostanie utworzony w tej strefie DNS. Aby na koniec opublikować strefę DNS w Internecie, należy skonfigurować serwery nazw dla domeny. Poniżej opisano każdy z tych kroków.

Usługa Azure DNS obsługuje też tworzenie domen prywatnych. Aby uzyskać instrukcje krok po kroku dotyczące tworzenia pierwszej prywatnej strefy DNS i pierwszego rekordu, zobacz [Rozpoczynanie pracy ze strefami prywatnymi usługi Azure DNS przy użyciu programu PowerShell](private-dns-getstarted-powershell.md).

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-the-resource-group"></a>Tworzenie grupy zasobów

Przed utworzeniem strefy DNS należy utworzyć dla niej grupę zasobów:

```powershell
New-AzResourceGroup -name MyResourceGroup -location "eastus"
```

## <a name="create-a-dns-zone"></a>Tworzenie strefy DNS

Strefa DNS jest tworzona za pomocą polecenia cmdlet `New-AzDnsZone`. Poniższy przykład tworzy strefę DNS o nazwie *contoso.com* w grupie zasobów o nazwie *MyResourceGroup*. Skorzystaj z tego przykładu, aby utworzyć strefę DNS, podstawiając własne wartości.

```powershell
New-AzDnsZone -Name contoso.com -ResourceGroupName MyResourceGroup
```

## <a name="create-a-dns-record"></a>Tworzenie rekordu DNS

Zestawy rekordów są tworzone za pomocą polecenia cmdlet `New-AzDnsRecordSet`. W poniższym przykładzie tworzony jest rekord o nazwie względnej „www” w strefie DNS „contoso.com” w grupie zasobów „MyResourceGroup”. W pełni kwalifikowaną nazwą zestawu rekordów jest „www.contoso.com”. Typ rekordu to „A” z adresem IP „1.2.3.4”, a czas wygaśnięcia wynosi 3600 sekund.

```powershell
New-AzDnsRecordSet -Name www -RecordType A -ZoneName contoso.com -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4")
```

## <a name="view-records"></a>Wyświetlanie rekordów

Aby wyświetlić listę rekordów DNS w strefie, należy użyć:

```powershell
Get-AzDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyResourceGroup
```

## <a name="update-name-servers"></a>Aktualizowanie serwerów nazw

Po poprawnym skonfigurowaniu strefy i rekordów DNS należy skonfigurować nazwę domeny w celu użycia serwerów nazw usługi Azure DNS. Umożliwi to innym użytkownikom w Internecie znalezienie Twoich rekordów DNS.

Serwery nazw dla strefy można wyświetlić za pomocą polecenia cmdlet `Get-AzDnsZone`:

```powershell
Get-AzDnsZone -Name contoso.com -ResourceGroupName MyResourceGroup

Name                  : contoso.com
ResourceGroupName     : myresourcegroup
Etag                  : 00000003-0000-0000-b40d-0996b97ed101
Tags                  : {}
NameServers           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org., ns4-01.azure-dns.info.}
NumberOfRecordSets    : 3
MaxNumberOfRecordSets : 5000
```

Te serwery nazw powinny zostać skonfigurowane u rejestratora nazw domen (w miejscu zakupu nazwy domeny). Rejestrator zaoferuje opcję skonfigurowania serwerów nazw na potrzeby domeny. Więcej informacji znajduje się artykule [Samouczek: hostowanie własnej domeny w usłudze Azure DNS](dns-delegate-domain-azure-dns.md#delegate-the-domain).

## <a name="delete-all-resources"></a>Usuwanie wszystkich zasobów

Jeśli zasoby utworzone w tym przewodniku Szybki start nie są już potrzebne, możesz je usunąć, usuwając grupę zasobów:

```powershell
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Następne kroki

Po utworzeniu strefy i rekordu DNS po raz pierwszy przy użyciu programu Azure PowerShell możesz utworzyć rekordy dla aplikacji internetowej w domenie niestandardowej.

> [!div class="nextstepaction"]
> [Create DNS records for a web app in a custom domain (Tworzenie rekordów DNS aplikacji internetowej w domenie niestandardowej)](./dns-web-sites-custom-domain.md)

