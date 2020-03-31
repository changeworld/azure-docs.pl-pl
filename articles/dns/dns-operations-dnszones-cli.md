---
title: Zarządzanie strefami DNS w usłudze Azure DNS — azure cli | Dokumenty firmy Microsoft
description: Strefy DNS można zarządzać za pomocą interfejsu wiersza polecenia platformy Azure. W tym artykule pokazano, jak aktualizować, usuwać i tworzyć strefy DNS w usłudze Azure DNS.
services: dns
documentationcenter: na
author: rohinkoul
manager: timlt
ms.assetid: 8ab63bc4-5135-4ed8-8c0b-5f0712b9afed
ms.service: dns
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: rohink
ms.openlocfilehash: 413c2ab3ee04249c2bb52bf42ca6a31a58fb9082
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76936928"
---
# <a name="how-to-manage-dns-zones-in-azure-dns-using-the-azure-cli"></a>Jak zarządzać strefami DNS w usłudze Azure DNS przy użyciu interfejsu wiersza polecenia platformy Azure

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [Powershell](dns-operations-dnszones.md)
> * [Interfejs wiersza polecenia platformy Azure](dns-operations-dnszones-cli.md)


W tym przewodniku pokazano, jak zarządzać strefami DNS przy użyciu wieloplatformowego interfejsu wiersza polecenia platformy Azure, który jest dostępny dla systemów Windows, Mac i Linux. Strefy DNS można również zarządzać za pomocą [programu Azure PowerShell](dns-operations-dnszones.md) lub witryny Azure Portal.

Ten przewodnik dotyczy w szczególności publicznych stref DNS. Aby uzyskać informacje na temat używania interfejsu wiersza polecenia platformy Azure do zarządzania strefami prywatnymi w usłudze Azure DNS, zobacz [Wprowadzenie do stref prywatnych dns platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](private-dns-getstarted-cli.md).

## <a name="introduction"></a>Wprowadzenie

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="set-up-azure-cli-for-azure-dns"></a>Konfigurowanie interfejsu wiersza polecenia platformy Azure dla usługi Azure DNS

### <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem konfiguracji sprawdź, czy dysponujesz następującymi elementami:

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).

* Zainstaluj najnowszą wersję interfejsu wiersza polecenia platformy Azure, który jest dostępny dla systemów Windows, Linux i MAC. Więcej informacji znajduje się w temacie [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-az-cli2).

### <a name="sign-in-to-your-azure-account"></a>Zaloguj się do swojego konta platformy Azure

Otwórz okno konsoli i uwierzytelnij się przy użyciu swoich poświadczeń. Aby uzyskać więcej informacji, zobacz [Logowanie się do platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)

```
az login
```

### <a name="select-the-subscription"></a>Wybierz subskrypcję

Sprawdź subskrypcje dostępne na koncie.

```
az account list
```

Wybierz subskrypcję platformy Azure do użycia.

```azurecli
az account set --subscription "subscription name"
```

### <a name="optional-to-installuse-azure-dns-private-zones-feature"></a>Opcjonalnie: aby zainstalować/użyć funkcji Strefy prywatne DNS platformy Azure
Funkcja prywatna strefa dns platformy Azure jest dostępna za pośrednictwem rozszerzenia interfejsu wiersza polecenia platformy Azure. Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure dla usługi „dns” 
```
az extension add --name dns
``` 

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Usługa Azure Resource Manager wymaga, żeby wszystkie grupy zasobów miały lokalizację. Będzie ona używana jako domyślna lokalizacja zasobów w danej grupie. Ponieważ jednak wszystkie zasoby DNS są globalne, a nie regionalne, wybór lokalizacji grupy zasobów nie ma wpływu na usługę Azure DNS.

Ten krok można pominąć, jeśli używasz istniejącej grupy zasobów.

```azurecli
az group create --name myresourcegroup --location "West US"
```

## <a name="getting-help"></a>Uzyskiwanie pomocy

Wszystkie polecenia interfejsu wiersza polecenia platformy `az network dns`Azure związane z usługą Azure DNS rozpoczynają się od . Pomoc jest dostępna dla `--help` każdego polecenia `-h`za pomocą opcji (formularz skrócony).  Przykład:

```azurecli
az network dns --help
az network dns zone --help
az network dns zone create --help
```

## <a name="create-a-dns-zone"></a>Tworzenie strefy DNS

Do tworzenia strefy DNS służy polecenie `az network dns zone create`. Aby uzyskać pomoc, zobacz `az network dns zone create -h`.

Poniższy przykład tworzy strefę DNS o nazwie *contoso.com* w grupie zasobów o nazwie *MyResourceGroup:*

```azurecli
az network dns zone create --resource-group MyResourceGroup --name contoso.com
```

### <a name="to-create-a-dns-zone-with-tags"></a>Aby utworzyć strefę DNS ze znacznikami

W poniższym przykładzie pokazano, jak utworzyć strefę DNS z dwoma [tagami usługi Azure Resource Manager](dns-zones-records.md#tags), project = *demo* i *env = test*, przy użyciu parametru `--tags` (formularz `-t`krótki):

```azurecli
az network dns zone create --resource-group MyResourceGroup --name contoso.com --tags "project=demo" "env=test"
```

## <a name="get-a-dns-zone"></a>Pobierz strefę DNS

Aby pobrać strefę DNS, użyj pliku `az network dns zone show`. Aby uzyskać pomoc, zobacz `az network dns zone show --help`.

Poniższy przykład zwraca *strefę* DNS contoso.com i skojarzone z nią dane z grupy zasobów *MyResourceGroup*. 

```azurecli
az network dns zone show --resource-group myresourcegroup --name contoso.com
```

Odpowiedzią jest poniższy przykład.

```json
{
  "etag": "00000002-0000-0000-3d4d-64aa3689d201",
  "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.com",
  "nameServers": [
    "ns1-04.azure-dns.com.",
    "ns2-04.azure-dns.net.",
    "ns3-04.azure-dns.org.",
    "ns4-04.azure-dns.info."
  ],
  "numberOfRecordSets": 4,
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones"
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

To polecenie nie powoduje aktualizacji żadnego z zestawów rekordów DNS w strefie (zobacz [Jak zarządzać rekordami DNS](dns-operations-recordsets-cli.md)). Służy ono wyłącznie do aktualizacji właściwości samego zasobu strefy. Te właściwości są obecnie ograniczone do ["tagów" usługi Azure Resource Manager](dns-zones-records.md#tags) dla zasobu strefy.

W poniższym przykładzie pokazano, jak zaktualizować znaczniki w strefie DNS. Istniejące znaczniki są zastępowane określoną wartością.

```azurecli
az network dns zone update --resource-group myresourcegroup --name contoso.com --set tags.team=support
```

## <a name="delete-a-dns-zone"></a>Usuwanie strefy DNS

Strefy DNS można usunąć przy użyciu polecenia `az network dns zone delete`. Aby uzyskać pomoc, zobacz `az network dns zone delete --help`.

> [!NOTE]
> Usunięcie strefy DNS powoduje również usunięcie wszystkich rekordów DNS w strefie. Tej operacji nie można cofnąć. Jeśli strefa DNS jest używana, po jej usunięciu usługi korzystające z tej strefy będą kończyć się niepowodzeniem.
>
>Aby zapobiec przypadkowemu usunięciu strefy, zobacz [jak chronić strefy i rekordy DNS](dns-protect-zones-recordsets.md).

To polecenie wyświetla monit o potwierdzenie. Opcjonalny przełącznik `--yes` pomija ten monit.

W poniższym przykładzie pokazano, jak usunąć *strefę contoso.com* z grupy zasobów *MyResourceGroup*.

```azurecli
az network dns zone delete --resource-group myresourcegroup --name contoso.com
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [zarządzać zestawami rekordów i rekordami](dns-getstarted-create-recordset-cli.md) w strefie DNS.

Dowiedz się, jak [delegować domenę do usługi Azure DNS](dns-domain-delegation.md).

