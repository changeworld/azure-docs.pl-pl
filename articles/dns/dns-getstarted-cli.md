---
title: Rozpoczynanie pracy z usługą Azure DNS przy użyciu interfejsu wiersza polecenia platformy Azure 2.0 | Microsoft Docs
description: Dowiedz się, jak utworzyć strefę i rekord DNS w usłudze Azure DNS. W tym szczegółowym przewodniku pokazano, jak po raz pierwszy utworzyć strefę i rekord DNS przy użyciu interfejsu wiersza polecenia platformy Azure 2.0.
services: dns
documentationcenter: na
author: KumuD
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2017
ms.author: kumud
ms.openlocfilehash: d24eaa4974f8bff09b337384e4fd139edb6ebd70
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "30175243"
---
# <a name="get-started-with-azure-dns-using-azure-cli-20"></a>Rozpoczynanie pracy z usługą Azure DNS przy użyciu interfejsu wiersza polecenia platformy Azure 2.0

> [!div class="op_single_selector"]
> * [Azure Portal](dns-getstarted-portal.md)
> * [Program PowerShell](dns-getstarted-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure 2.0](dns-getstarted-cli.md)

W tym artykule przedstawiono procedurę tworzenia po raz pierwszy strefy i rekordu DNS przy użyciu wieloplatformowego interfejsu wiersza polecenia platformy Azure 2.0, który jest dostępny dla systemów Windows, Mac i Linux. Te kroki można również wykonać przy użyciu witryny Azure Portal lub programu Azure PowerShell.

Strefa DNS jest używana do hostowania rekordów DNS dla określonej domeny. Aby rozpocząć hostowanie domeny w usłudze Azure DNS, musisz utworzyć strefę DNS dla tej nazwy domeny. Każdy rekord DNS domeny zostanie utworzony w tej strefie DNS. Aby na koniec opublikować strefę DNS w Internecie, należy skonfigurować serwery nazw dla domeny. Poniżej opisano każdy z tych kroków.

W tych instrukcjach założono, że już zainstalowano interfejs wiersza polecenia platformy Azure 2.0 i zalogowano się do niego. Aby uzyskać pomoc, zobacz [How to manage DNS zones using Azure CLI 2.0](dns-operations-dnszones-cli.md) (Jak zarządzać strefami systemu DNS przy użyciu interfejsu wiersza polecenia platformy Azure 2.0).

Usługa Azure DNS teraz obsługuje również prywatne strefy DNS (obecnie w wersji zapoznawczej). Aby uzyskać więcej informacji na temat prywatnych stref DNS, zobacz [Using Azure DNS for private domains (Używanie usługi Azure DNS dla domen prywatnych)](private-dns-overview.md). Aby uzyskać przykład sposobu tworzenia prywatnej strefy DNS, zobacz [Rozpoczynanie pracy ze strefami prywatnymi usługi Azure DNS przy użyciu interfejsu wiersza polecenia](./private-dns-getstarted-cli.md).

## <a name="create-the-resource-group"></a>Tworzenie grupy zasobów

Przed utworzeniem strefy DNS należy utworzyć dla niej grupę zasobów. Poniżej przedstawiono polecenia.

```azurecli
az group create --name MyResourceGroup --location "West US"
```

## <a name="create-a-dns-zone"></a>Tworzenie strefy DNS

Do tworzenia strefy DNS służy polecenie `az network dns zone create`. Aby wyświetlić pomoc dla tego polecenia, wpisz `az network dns zone create -h`.

Poniższy przykład obejmuje tworzenie strefy DNS o nazwie *contoso.com* w grupie zasobów o nazwie *MyResourceGroup*. Skorzystaj z tego przykładu, aby utworzyć strefę DNS, podstawiając własne wartości.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.com
```

## <a name="create-a-dns-record"></a>Tworzenie rekordu DNS

Aby utworzyć rekord DNS, użyj polecenia `az network dns record-set [record type] add-record`. Aby uzyskać pomoc, na przykład dotyczącą rekordów A, zobacz `azure network dns record-set A add-record -h`.

W poniższym przykładzie tworzony jest rekord o nazwie względnej „www” w strefie DNS „contoso.com” w grupie zasobów „MyResourceGroup”. W pełni kwalifikowaną nazwą zestawu rekordów jest „www.contoso.com”. Typ rekordu to „A” z adresem IP „1.2.3.4”, a jako domyślny czas wygaśnięcia używana jest wartość 3600 sekund (1 godzina).

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.com -n www -a 1.2.3.4
```

Aby uzyskać informacje o innych typach rekordów, zestawach rekordów zawierających więcej niż jeden rekord, alternatywnych wartościach czasu wygaśnięcia oraz sposobie modyfikowania istniejących rekordów, zobacz [Manage DNS records and record sets using the Azure CLI 2.0](dns-operations-recordsets-cli.md) (Zarządzanie rekordami i zestawami rekordów DNS przy użyciu interfejsu wiersza polecenia platformy Azure 2.0).

## <a name="view-records"></a>Wyświetlanie rekordów

Aby wyświetlić listę rekordów DNS w strefie, należy użyć:

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.com
```

## <a name="update-name-servers"></a>Aktualizowanie serwerów nazw

Po poprawnym skonfigurowaniu strefy i rekordów DNS należy skonfigurować nazwę domeny w celu użycia serwerów nazw usługi Azure DNS. Umożliwi to innym użytkownikom w Internecie znalezienie Twoich rekordów DNS.

Serwery nazw dla strefy można wyświetlić za pomocą polecenia `az network dns zone show`. Aby wyświetlić nazwy serwerów nazw, należy użyć danych wyjściowych JSON, jak pokazano w poniższym przykładzie.

```azurecli
az network dns zone show -g MyResourceGroup -n contoso.com -o json

{
  "etag": "00000003-0000-0000-b40d-0996b97ed101",
  "id": "/subscriptions/a385a691-bd93-41b0-8084-8213ebc5bff7/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.com",
  "nameServers": [
    "ns1-01.azure-dns.com.",
    "ns2-01.azure-dns.net.",
    "ns3-01.azure-dns.org.",
    "ns4-01.azure-dns.info."
  ],
  "numberOfRecordSets": 3,
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones"
}
```

Te serwery nazw powinny zostać skonfigurowane u rejestratora nazw domen (w miejscu zakupu nazwy domeny). Rejestrator zaoferuje opcję skonfigurowania serwerów nazw na potrzeby domeny. Aby uzyskać więcej informacji, zobacz [Delegowanie domeny do usługi Azure DNS](dns-domain-delegation.md).

## <a name="delete-all-resources"></a>Usuwanie wszystkich zasobów
 
Aby usunąć wszystkie zasoby utworzone w tym artykule, wykonaj następujące czynności:

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat usługi Azure DNS, zobacz [Omówienie usługi Azure DNS](dns-overview.md).

Aby dowiedzieć się więcej na temat zarządzania strefami DNS w usłudze Azure DNS, zobacz [Manage DNS zones in Azure DNS using Azure CLI 2.0](dns-operations-dnszones-cli.md) (Zarządzanie strefami DNS w usłudze Azure DNS przy użyciu interfejsu wiersza polecenia platformy Azure 2.0).

Aby dowiedzieć się więcej na temat zarządzania rekordami DNS w usłudze Azure DNS, zobacz [Manage DNS records and record sets in Azure DNS using Azure CLI 2.0](dns-operations-recordsets-cli.md) (Zarządzanie rekordami i zestawami rekordów DNS w usłudze Azure DNS przy użyciu interfejsu wiersza polecenia platformy Azure 2.0).
