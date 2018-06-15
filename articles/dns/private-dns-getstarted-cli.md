---
title: Rozpoczynanie pracy z usługą Azure DNS Private Zones przy użyciu interfejsu wiersza polecenia platformy Azure w wersji 2.0 | Microsoft Docs
description: Dowiedz się, jak utworzyć prywatną strefę i rekord DNS w usłudze Azure DNS. W tym szczegółowym przewodniku pokazano, jak po raz pierwszy utworzyć prywatną strefę i rekord DNS przy użyciu interfejsu wiersza polecenia platformy Azure 2.0.
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
ms.date: 03/15/2018
ms.author: kumud
ms.openlocfilehash: d10f3201adb972468d8de7e66a940232ed19562d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "30191453"
---
# <a name="get-started-with-azure-dns-private-zones-using-azure-cli-20"></a>Rozpoczynanie pracy z usługą Azure DNS Private Zones przy użyciu interfejsu wiersza polecenia platformy Azure w wersji 2.0

> [!div class="op_single_selector"]
> * [Program PowerShell](private-dns-getstarted-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure 2.0](private-dns-getstarted-cli.md)

W tym artykule przedstawiono procedurę tworzenia po raz pierwszy prywatnej strefy i rekordu DNS przy użyciu wieloplatformowego interfejsu wiersza polecenia platformy Azure 2.0, który jest dostępny dla systemów Windows, Mac i Linux. Te kroki można również wykonać przy użyciu programu Azure PowerShell.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

Strefa DNS jest używana do hostowania rekordów DNS dla określonej domeny. Aby rozpocząć hostowanie domeny w usłudze Azure DNS, musisz utworzyć strefę DNS dla tej nazwy domeny. Każdy rekord DNS domeny zostanie utworzony w tej strefie DNS. Aby opublikować prywatną strefę DNS w sieci wirtualnej, musisz określić listę sieci wirtualnych, które mogą rozpoznawać rekordy w strefie.  Nazywamy je „sieciami wirtualnymi rozpoznawania”.  Możesz również określić sieć wirtualną, dla której usługa Azure DNS utrzymuje rekordy nazw hosta zawsze, gdy maszyna wirtualna jest tworzona, zmienia protokół internetowy lub jest niszczona.  Nazywamy ją „siecią wirtualną rejestracji”.

W tych instrukcjach założono, że już zainstalowano interfejs wiersza polecenia platformy Azure 2.0 i zalogowano się do niego, a także zainstalowano wymagane rozszerzenie interfejsu wiersza polecenia, które obsługuje strefy prywatne. Aby uzyskać pomoc, zobacz [How to manage DNS zones using Azure CLI 2.0](dns-operations-dnszones-cli.md) (Jak zarządzać strefami systemu DNS przy użyciu interfejsu wiersza polecenia platformy Azure 2.0).

## <a name="to-installuse-azure-dns-private-zones-feature-public-preview"></a>Instalowanie/korzystanie z funkcji Azure DNS Private Zones (w publicznej wersji zapoznawczej)
Funkcja Azure DNS Private Zones została wydana w publicznej wersji zapoznawczej i udostępniona za pomocą rozszerzenia wiersza polecenia platformy Azure. Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure dla usługi „dns” 

```
az extension add --name dns
``` 

## <a name="create-the-resource-group"></a>Tworzenie grupy zasobów

Przed utworzeniem strefy DNS należy utworzyć dla niej grupę zasobów. Poniżej przedstawiono polecenia.

```azurecli
az group create --name MyResourceGroup --location "West US"
```

## <a name="create-a-dns-private-zone"></a>Tworzenie strefy prywatnej DNS

Do tworzenia strefy prywatnej DNS służy polecenie `az network dns zone create`. Aby wyświetlić pomoc dla tego polecenia, wpisz `az network dns zone create --help`.

Poniższy przykład tworzy strefę prywatną DNS o nazwie *contoso.local* w grupie zasobów *MyResourceGroup* i udostępnia ją (tworzy połączenia z nią) sieci wirtualnej *MyAzureVnet* za pomocą parametru sieci wirtualnej rozpoznawania. Skorzystaj z tego przykładu, aby utworzyć strefę DNS, podstawiając własne wartości.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.local --zone-type Private --resolution-vnets MyAzureVnet
```

Uwaga: w powyższym przykładzie sieć wirtualna „MyAzureVnet” należy do tej samej grupy zasobów i subskrypcji co strefa prywatna. Jeśli chcesz połączyć sieć wirtualną należącą do innej grupy zasobów lub subskrypcji, musisz określić pełny identyfikator usługi Azure Resource Manager zamiast samej nazwy sieci wirtualnej dla parametru --resolution-vnets. 

Jeśli platforma Azure ma automatycznie tworzyć rekordy nazw hosta w strefie, użyj parametru *registration-vnets* zamiast *resolution-vnets*.  Sieci wirtualne rejestracji są włączane automatycznie do rozpoznawania.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.local --zone-type Private --registration-vnets MyAzureVnet
```

## <a name="create-a-dns-record"></a>Tworzenie rekordu DNS

Aby utworzyć rekord DNS, użyj polecenia `az network dns record-set [record type] add-record`. Aby uzyskać pomoc, na przykład dotyczącą rekordów A, zobacz `azure network dns record-set A add-record --help`.

W poniższym przykładzie jest tworzony rekord o nazwie względnej „ip1” w strefie DNS „contoso.local” w grupie zasobów „MyResourceGroup”. W pełni kwalifikowaną nazwą zestawu rekordów jest „ip1.contoso.local”. Typ rekordu to „A” z adresem IP „10.0.0.1”.

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.local -n ip1 -a 10.0.0.1
```

Aby uzyskać informacje o innych typach rekordów, zestawach rekordów zawierających więcej niż jeden rekord, alternatywnych wartościach czasu wygaśnięcia oraz sposobie modyfikowania istniejących rekordów, zobacz [Manage DNS records and record sets using the Azure CLI 2.0](dns-operations-recordsets-cli.md) (Zarządzanie rekordami i zestawami rekordów DNS przy użyciu interfejsu wiersza polecenia platformy Azure 2.0).

## <a name="view-records"></a>Wyświetlanie rekordów

Aby wyświetlić listę rekordów DNS w strefie, należy użyć:

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.com
```

## <a name="get-a-dns-private-zone"></a>Uzyskiwanie strefy prywatnej DNS

Aby uzyskać strefę prywatną DNS, należy użyć polecenia `az network dns zone show`. Aby uzyskać pomoc, zobacz `az network dns zone show --help`.

Poniższy przykład zwraca strefę DNS *contoso.local* i jej skojarzone dane z grupy zasobów *MyResourceGroup*. 

```azurecli
az network dns zone show --resource-group MyResourceGroup --name contoso.local
```

Odpowiedzią jest poniższy przykład.

```json
{
  "etag": "00000002-0000-0000-3d4d-64aa3689d201",
  "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/MyResourceGroup/providers/Microsoft.Network/dnszones/contoso.local",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.local",
  "nameServers": null,
  "numberOfRecordSets": 1,
  "registrationVirtualNetworks": [],
  "resolutionVirtualNetworks": [
    {
      "additionalProperties": {},
      "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyAzureVnet",
      "resourceGroup": "MyResourceGroup"
    }
  ]
  "resourceGroup": "MyResourceGroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones",
  "zoneType": "Private"
}
```

Należy pamiętać, że rekordy DNS nie są zwracane przez polecenie `az network dns zone show`. Aby wyświetlić listę rekordów DNS, użyj polecenia `az network dns record-set list`.


## <a name="list-dns-zones"></a>Wyświetlanie listy stref DNS

Aby wyliczyć strefy DNS, należy użyć polecenia `az network dns zone list`. Aby uzyskać pomoc, zobacz `az network dns zone list --help`.

Określenie grupy zasobów powoduje wyświetlenie listy tylko następujących stref w grupie zasobów:

```azurecli
az network dns zone list --resource-group MyResourceGroup
```

Pominięcie grupy zasobów powoduje wyświetlenie listy wszystkich stref w subskrypcji:

```azurecli
az network dns zone list 
```

## <a name="update-a-dns-zone"></a>Aktualizowanie strefy DNS

Zmiany w zasobie strefy DNS można wprowadzić przy użyciu polecenia `az network dns zone update`. Aby uzyskać pomoc, zobacz `az network dns zone update --help`.

To polecenie nie powoduje aktualizacji żadnego z zestawów rekordów DNS w strefie (zobacz [Jak zarządzać rekordami DNS](dns-operations-recordsets-cli.md)). Służy ono wyłącznie do aktualizacji właściwości samego zasobu strefy. W przypadku stref prywatnych można zaktualizować sieci wirtualne rejestracji lub rozpoznawania połączone ze strefą. 

W poniższym przykładzie pokazano, jak zaktualizować sieć wirtualną rozpoznawania połączoną ze strefą prywatną DNS. Istniejąca połączona sieć wirtualna rozpoznawania zostaje zastąpiona przez określoną nową sieć wirtualną.

```azurecli
az network dns zone update --resource-group MyResourceGroup --name contoso.local --zone-type Private --resolution-vnets MyNewAzureVnet
```

## <a name="delete-a-dns-zone"></a>Usuwanie strefy DNS

Strefy DNS można usunąć przy użyciu polecenia `az network dns zone delete`. Aby uzyskać pomoc, zobacz `az network dns zone delete --help`.

> [!NOTE]
> Usunięcie strefy DNS powoduje również usunięcie wszystkich rekordów DNS w strefie. Tej operacji nie można cofnąć. Jeśli strefa DNS jest używana, po jej usunięciu usługi korzystające z tej strefy będą kończyć się niepowodzeniem.
>
>Aby zapobiec przypadkowemu usunięciu strefy, zobacz [jak chronić strefy i rekordy DNS](dns-protect-zones-recordsets.md).

To polecenie wyświetla monit o potwierdzenie. Opcjonalny przełącznik `--yes` pomija ten monit.

W poniższym przykładzie pokazano, jak usunąć strefę *contoso.local* z grupy zasobów *MyResourceGroup*.

```azurecli
az network dns zone delete --resource-group myresourcegroup --name contoso.local
```

## <a name="delete-all-resources"></a>Usuwanie wszystkich zasobów
 
Aby usunąć wszystkie zasoby utworzone w tym artykule, wykonaj następujące czynności:

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat usługi Azure DNS, zobacz [Omówienie usługi Azure DNS](dns-overview.md).

Aby dowiedzieć się więcej na temat zarządzania strefami DNS w usłudze Azure DNS, zobacz [Manage DNS zones in Azure DNS using Azure CLI 2.0](dns-operations-dnszones-cli.md) (Zarządzanie strefami DNS w usłudze Azure DNS przy użyciu interfejsu wiersza polecenia platformy Azure 2.0).

Aby dowiedzieć się więcej na temat zarządzania rekordami DNS w usłudze Azure DNS, zobacz [Manage DNS records and record sets in Azure DNS using Azure CLI 2.0](dns-operations-recordsets-cli.md) (Zarządzanie rekordami i zestawami rekordów DNS w usłudze Azure DNS przy użyciu interfejsu wiersza polecenia platformy Azure 2.0).
