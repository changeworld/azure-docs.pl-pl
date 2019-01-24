---
title: Wybieranie obrazów maszyn wirtualnych systemu Linux przy użyciu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak określić wydawcy, oferty, jednostki SKU i wersji dla obrazów maszyn wirtualnych w portalu Marketplace za pomocą wiersza polecenia platformy Azure.
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 7a858e38-4f17-4e8e-a28a-c7f801101721
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/28/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2ff08b0f155be54ee57d2d9999c47aa56a366d09
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2019
ms.locfileid: "54852205"
---
# <a name="how-to-find-linux-vm-images-in-the-azure-marketplace-with-the-azure-cli"></a>Jak znaleźć obrazy maszyny Wirtualnej systemu Linux w witrynie Azure Marketplace przy użyciu wiersza polecenia platformy Azure
W tym temacie opisano sposób używania interfejsu wiersza polecenia platformy Azure do obrazów maszyn wirtualnych można znaleźć w witrynie Azure Marketplace. Te informacje służą do określania obrazu z witryny Marketplace, gdy programowo utworzyć Maszynę wirtualną przy użyciu interfejsu wiersza polecenia, szablonów usługi Resource Manager lub innych narzędzi.

Również przeglądać dostępne obrazy i ofert przy użyciu [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/) storefront, [witryny Azure portal](https://portal.azure.com), lub [programu Azure PowerShell](../windows/cli-ps-findimage.md). 

Upewnij się, że jest zainstalowana najnowsza wersja [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i zalogować się do konta platformy Azure (`az login`).

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

## <a name="list-popular-images"></a>Listę popularnych obrazów

Uruchom [listy obrazów maszyn wirtualnych az](/cli/azure/vm/image#az_vm_image_list) polecenia bez `--all` opcję, aby wyświetlić listę popularnych obrazów maszyn wirtualnych w witrynie Azure Marketplace. Na przykład uruchom następujące polecenie, aby wyświetlić pamięci podręcznej listę popularnych obrazów w formacie tabeli:

```azurecli
az vm image list --output table
```

Dane wyjściowe zawiera obraz URN (wartość w *Urn* kolumny). Podczas tworzenia maszyny Wirtualnej przy użyciu jednej z tych popularnych obrazów z portalu Marketplace, możesz też określić *UrnAlias*, takie jak skróconą *UbuntuLTS*.

```
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
openSUSE-Leap  SUSE                    42.3                SUSE:openSUSE-Leap:42.3:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
...
```

## <a name="find-specific-images"></a>Znajdowanie określonych obrazów

Aby znaleźć określonego obrazu maszyny Wirtualnej w portalu Marketplace, użyj `az vm image list` polecenia `--all` opcji. Ta wersja polecenia zajmuje trochę czasu, aby zakończyć i może zwrócić długich danych wyjściowych, dlatego zazwyczaj filtrować listę według `--publisher` lub innego parametru. 

Na przykład następujące polecenie wyświetla wszystkie oferty Debian (należy pamiętać, że bez `--all` przełączyć, przeszukuje tylko lokalną pamięć podręczną obrazów wspólnych):

```azurecli
az vm image list --offer Debian --all --output table 

```

Częściowe dane wyjściowe: 
```
Offer    Publisher    Sku                Urn                                              Version
-------  -----------  -----------------  -----------------------------------------------  --------------
...
Debian   credativ     7                  credativ:Debian:7:7.0.201602010                  7.0.201602010
Debian   credativ     7                  credativ:Debian:7:7.0.201603020                  7.0.201603020
Debian   credativ     7                  credativ:Debian:7:7.0.201604050                  7.0.201604050
Debian   credativ     7                  credativ:Debian:7:7.0.201604200                  7.0.201604200
Debian   credativ     7                  credativ:Debian:7:7.0.201606280                  7.0.201606280
Debian   credativ     7                  credativ:Debian:7:7.0.201609120                  7.0.201609120
Debian   credativ     7                  credativ:Debian:7:7.0.201611020                  7.0.201611020
Debian   credativ     8                  credativ:Debian:8:8.0.201602010                  8.0.201602010
Debian   credativ     8                  credativ:Debian:8:8.0.201603020                  8.0.201603020
Debian   credativ     8                  credativ:Debian:8:8.0.201604050                  8.0.201604050
Debian   credativ     8                  credativ:Debian:8:8.0.201604200                  8.0.201604200
Debian   credativ     8                  credativ:Debian:8:8.0.201606280                  8.0.201606280
Debian   credativ     8                  credativ:Debian:8:8.0.201609120                  8.0.201609120
Debian   credativ     8                  credativ:Debian:8:8.0.201611020                  8.0.201611020
Debian   credativ     8                  credativ:Debian:8:8.0.201701180                  8.0.201701180
Debian   credativ     8                  credativ:Debian:8:8.0.201703150                  8.0.201703150
Debian   credativ     8                  credativ:Debian:8:8.0.201704110                  8.0.201704110
Debian   credativ     8                  credativ:Debian:8:8.0.201704180                  8.0.201704180
Debian   credativ     8                  credativ:Debian:8:8.0.201706190                  8.0.201706190
Debian   credativ     8                  credativ:Debian:8:8.0.201706210                  8.0.201706210
Debian   credativ     8                  credativ:Debian:8:8.0.201708040                  8.0.201708040
...
```

Zastosuj filtry podobne z `--location`, `--publisher`, i `--sku` opcje. Można przeprowadzić udanych dopasowań w filtrze, takie jak wyszukiwanie `--offer Deb` można znaleźć wszystkie obrazy Debian.

Jeśli nie określisz danej lokalizacji przy użyciu `--location` opcji, zwracane są wartości domyślnej lokalizacji. (Ustaw lokalizację różne domyślne, uruchamiając `az configure --defaults location=<location>`.)

Na przykład następujące polecenie wyświetla listę wszystkich Debian 8 publikowania jednostek SKU w lokalizacji Europa Zachodnia:

```azurecli
az vm image list --location westeurope --offer Deb --publisher credativ --sku 8 --all --output table
```

Częściowe dane wyjściowe:

```
Offer    Publisher    Sku                Urn                                              Version
-------  -----------  -----------------  -----------------------------------------------  -------------
Debian   credativ     8                  credativ:Debian:8:8.0.201602010                  8.0.201602010
Debian   credativ     8                  credativ:Debian:8:8.0.201603020                  8.0.201603020
Debian   credativ     8                  credativ:Debian:8:8.0.201604050                  8.0.201604050
Debian   credativ     8                  credativ:Debian:8:8.0.201604200                  8.0.201604200
Debian   credativ     8                  credativ:Debian:8:8.0.201606280                  8.0.201606280
Debian   credativ     8                  credativ:Debian:8:8.0.201609120                  8.0.201609120
Debian   credativ     8                  credativ:Debian:8:8.0.201611020                  8.0.201611020
Debian   credativ     8                  credativ:Debian:8:8.0.201701180                  8.0.201701180
Debian   credativ     8                  credativ:Debian:8:8.0.201703150                  8.0.201703150
Debian   credativ     8                  credativ:Debian:8:8.0.201704110                  8.0.201704110
Debian   credativ     8                  credativ:Debian:8:8.0.201704180                  8.0.201704180
Debian   credativ     8                  credativ:Debian:8:8.0.201706190                  8.0.201706190
Debian   credativ     8                  credativ:Debian:8:8.0.201706210                  8.0.201706210
...
```

## <a name="navigate-the-images"></a>Nawigować po obrazach 
Innym sposobem, aby znaleźć obraz w lokalizacji jest uruchomienie [obrazu maszyny wirtualnej az list wydawców](/cli/azure/vm/image#az_vm_image_list_publishers), [obrazu maszyny wirtualnej az list-offers](/cli/azure/vm/image), i [az vm list-jednostki SKU obrazów](/cli/azure/vm/image#az_vm_image_list_skus) poleceń w sekwencji. Za pomocą poniższych poleceń należy określić następujące wartości:

1. Wyświetl listę wydawców obrazów.
2. Dla danego wydawcy wyświetl listę ofert.
3. Dla danej oferty wyświetl listę wersji SKU.

Następnie dla wybranej jednostki SKU, możesz wybrać wersję do wdrożenia.

Na przykład następujące polecenie wyświetla listę wydawców obrazów w lokalizacji zachodnie stany USA:

```azurecli
az vm image list-publishers --location westus --output table
```

Częściowe dane wyjściowe:

```
Location    Name
----------  ----------------------------------------------------
westus      128technology
westus      1e
westus      4psa
westus      5nine-software-inc
westus      7isolutions
westus      a10networks
westus      abiquo
westus      accellion
westus      accessdata-group
westus      accops
westus      Acronis
westus      Acronis.Backup
westus      actian-corp
westus      actian_matrix
westus      actifio
westus      activeeon
westus      advantech-webaccess
westus      aerospike
westus      affinio
westus      aiscaler-cache-control-ddos-and-url-rewriting-
westus      akamai-technologies
westus      akumina
...
```
Dzięki tym informacjom można znaleźć oferty od określonego wydawcy. Na przykład w przypadku *Canonical* oferty znaleźć wydawcy w lokalizacji zachodnie stany USA, uruchamiając `azure vm image list-offers`. Przekaż, lokalizacji i wydawcy, jak w poniższym przykładzie:

```azurecli
az vm image list-offers --location westus --publisher Canonical --output table
```

Dane wyjściowe:

```
Location    Name
----------  -------------------------
westus      Ubuntu15.04Snappy
westus      Ubuntu15.04SnappyDocker
westus      UbunturollingSnappy
westus      UbuntuServer
westus      Ubuntu_Core
```
Zobaczysz, że w regionie zachodnie stany USA, Canonical publikuje *UbuntuServer* oferty na platformie Azure. Ale o jakie wersje SKU chodzi? Aby uzyskać te wartości, należy uruchomić `azure vm image list-skus` i Ustaw lokalizację, wydawcy i oferty, który zostanie odnalezione:

```azurecli
az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
```

Dane wyjściowe:

```
Location    Name
----------  -----------------
westus      12.04.3-LTS
westus      12.04.4-LTS
westus      12.04.5-LTS
westus      14.04.0-LTS
westus      14.04.1-LTS
westus      14.04.2-LTS
westus      14.04.3-LTS
westus      14.04.4-LTS
westus      14.04.5-DAILY-LTS
westus      14.04.5-LTS
westus      16.04-DAILY-LTS
westus      16.04-LTS
westus      16.04.0-LTS
westus      17.10
westus      17.10-DAILY
westus      18.04-DAILY-LTS
westus      18.04-LTS
westus      18.10-DAILY
```

Na koniec użyj `az vm image list` polecenia, można znaleźć określonej wersji jednostki SKU, na przykład *16.04 LTS*:

```azurecli
az vm image list --location westus --publisher Canonical --offer UbuntuServer --sku 16.04-LTS --all --output table
```

Częściowe dane wyjściowe:

```
Offer         Publisher    Sku        Urn                                               Version
------------  -----------  ---------  ------------------------------------------------  ---------------
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201611220  16.04.201611220
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201611300  16.04.201611300
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201612050  16.04.201612050
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201612140  16.04.201612140
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201612210  16.04.201612210
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201701130  16.04.201701130
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702020  16.04.201702020
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702200  16.04.201702200
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702210  16.04.201702210
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702240  16.04.201702240
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703020  16.04.201703020
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703030  16.04.201703030
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703070  16.04.201703070
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703270  16.04.201703270
...
```

Teraz można dokładnie obrazu, którego chcesz użyć, biorąc pod uwagę na wartość URN. Przekazuje tę wartość za pomocą `--image` parametru podczas tworzenia maszyny Wirtualnej z [tworzenie az vm](/cli/azure/vm#az_vm_create) polecenia. Należy pamiętać, że można opcjonalnie zastąpić numer wersji w URN "najnowszej"wersji. Ta wersja jest zawsze najnowszą wersję obrazu. 

Jeśli wdrożysz maszynę Wirtualną przy użyciu szablonu usługi Resource Manager, parametry obrazu należy ustawić indywidualnie w `imageReference` właściwości. Zobacz [dokumentację szablonu](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Wyświetl właściwości planu

Aby wyświetlić informacje o planu zakupu z obrazu, uruchom [az vm image show](/cli/azure/image#az_image_show) polecenia. Jeśli `plan` właściwość w danych wyjściowych jest `null`, obraz, który ma warunki muszą zaakceptować przed przystąpieniem do wdrożenia programowego.

Na przykład obrazów Canonical Ubuntu Server 16.04 LTS nie ma dodatkowe warunki, ponieważ `plan` informacje są `null`:

```azurecli
az vm image show --location westus --urn Canonical:UbuntuServer:16.04-LTS:latest
```

Dane wyjściowe:

```
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/16.04-LTS/Versions/16.04.201801260",
  "location": "westus",
  "name": "16.04.201809120",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": null,
  "tags": null
}
```

Uruchomiony podobne polecenie RabbitMQ certyfikowanych przez Bitnami obraz pokazuje następujące `plan` właściwości: `name`, `product`, i `publisher`. (Niektóre obrazy również mieć `promotion code` właściwości.) Aby wdrożyć ten obraz, zobacz poniższe sekcje, aby zaakceptować warunki i włączyć wdrożenia programowe.

```azurecli
az vm image show --location westus --urn bitnami:rabbitmq:rabbitmq:latest
```
Dane wyjściowe:

```
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/bitnami/ArtifactTypes/VMImage/Offers/rabbitmq/Skus/rabbitmq/Versions/3.7.1807171506",
  "location": "westus",
  "name": "3.7.1809211005",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": {
    "name": "rabbitmq",
    "product": "rabbitmq",
    "publisher": "bitnami"
  },
  "tags": null
}
```

### <a name="accept-the-terms"></a>Zaakceptuj warunki

Aby wyświetlić i zaakceptować postanowienia licencyjne, należy użyć [az vm obraz zaakceptować terms](/cli/azure/vm/image?#az_vm_image_accept_terms) polecenia. Jeśli akceptujesz jej warunki, możesz włączyć wdrożenia programowe w ramach subskrypcji. Musisz zaakceptować warunki raz na subskrypcję dla obrazu. Na przykład:

```azurecli
az vm image accept-terms --urn bitnami:rabbitmq:rabbitmq:latest
``` 

Dane wyjściowe obejmują `licenseTextLink` do licencji warunki co oznacza, że wartość `accepted` jest `true`:

```
{
  "accepted": true,
  "additionalProperties": {},
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.MarketplaceOrdering/offertypes/bitnami/offers/rabbitmq/plans/rabbitmq",
  "licenseTextLink": "https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_BITNAMI%253a24RABBITMQ%253a24RABBITMQ%253a24IGRT7HHPIFOBV3IQYJHEN2O2FGUVXXZ3WUYIMEIVF3KCUNJ7GTVXNNM23I567GBMNDWRFOY4WXJPN5PUYXNKB2QLAKCHP4IE5GO3B2I.txt",
  "name": "rabbitmq",
  "plan": "rabbitmq",
  "privacyPolicyLink": "https://bitnami.com/privacy",
  "product": "rabbitmq",
  "publisher": "bitnami",
  "retrieveDatetime": "2018-02-22T04:06:28.7641907Z",
  "signature": "XXXXXXLAZIK7ZL2YRV5JYQXONPV76NQJW3FKMKDZYCRGXZYVDGX6BVY45JO3BXVMNA2COBOEYG2NO76ONORU7ITTRHGZDYNJNXXXXXX",
  "type": "Microsoft.MarketplaceOrdering/offertypes"
}
```

### <a name="deploy-using-purchase-plan-parameters"></a>Wdrażanie przy użyciu parametrów planu zakupu

Po zaakceptowaniu warunków dla obrazu, możesz wdrożyć Maszynę wirtualną w subskrypcji. Aby wdrożyć obraz przy użyciu `az vm create` poleceń, podaj parametry dla planu zakupu z dodatkowo do URN dla obrazu. Na przykład, aby wdrożyć Maszynę wirtualną za pomocą certyfikowane oprogramowania RabbitMQ przez obraz Bitnami:

```azurecli
az group create --name myResourceGroupVM --location westus

az vm create --resource-group myResourceGroupVM --name myVM --image bitnami:rabbitmq:rabbitmq:latest --plan-name rabbitmq --plan-product rabbitmq --plan-publisher bitnami

```

## <a name="next-steps"></a>Kolejne kroki
Aby szybko utworzyć maszynę wirtualną przy użyciu informacji o obrazie, zobacz [tworzenie i zarządzanie maszynami wirtualnymi systemu Linux przy użyciu wiersza polecenia platformy Azure](tutorial-manage-vm.md).
