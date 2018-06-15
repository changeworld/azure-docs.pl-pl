---
title: Wybierz obrazów maszyny Wirtualnej systemu Linux za pomocą wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać interfejsu wiersza polecenia Azure w celu określenia wydawcy, oferty, jednostki SKU i wersji dla obrazów maszyn wirtualnych w witrynie Marketplace.
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
ms.date: 02/28/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c65ebbc8a61c13b96364dadde45bd4bca828e337
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2018
ms.locfileid: "29740887"
---
# <a name="how-to-find-linux-vm-images-in-the-azure-marketplace-with-the-azure-cli"></a>Jak znaleźć maszyny Wirtualnej systemu Linux obrazów w portalu Azure Marketplace z wiersza polecenia platformy Azure
W tym temacie opisano sposób użycia 2.0 interfejsu wiersza polecenia platformy Azure można znaleźć obrazów maszyn wirtualnych w portalu Azure Marketplace. Te informacje służą do obrazu z witryny Marketplace można określić podczas tworzenia maszyny Wirtualnej programowo z poziomu interfejsu wiersza polecenia, szablony usługi Resource Manager lub innych narzędzi.

Upewnij się, że jest zainstalowana najnowsza wersja [Azure CLI 2.0](/cli/azure/install-az-cli2) i zalogować się do konta platformy Azure (`az login`).

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

## <a name="list-popular-images"></a>Listy obrazów popularnych

Uruchom [listy obrazów maszyny wirtualnej az](/cli/azure/vm/image#az_vm_image_list) polecenia bez `--all` opcję, aby wyświetlić listę popularnych obrazów maszyn wirtualnych w portalu Azure Marketplace. Na przykład uruchom następujące polecenie, aby wyświetlić listę buforowanych popularnych obrazów w formacie tabeli:

```azurecli
az vm image list --output table
```

Dane wyjściowe zawiera obraz URN (wartość w *Urn* kolumny). Podczas tworzenia maszyny Wirtualnej przy użyciu jednej z tych popularnych obrazów Marketplace, można również określić *UrnAlias*, takich jak skróconą *UbuntuLTS*.

```
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
openSUSE-Leap  SUSE                    42.2                SUSE:openSUSE-Leap:42.2:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
...
```

## <a name="find-specific-images"></a>Znajdowanie określonych obrazów

Aby znaleźć określony obraz maszyny Wirtualnej w portalu Marketplace, użyj `az vm image list` z `--all` opcji. Ta wersja polecenia wymaga pewnego czasu do zakończenia i mogą zwracać długich danych wyjściowych, dlatego zazwyczaj przefiltrować listę wg `--publisher` lub inny parametr. 

Na przykład następujące polecenie wyświetla wszystkie oferty Debian (należy pamiętać, że bez `--all` przełącznika, przeszukiwane tylko w lokalnej pamięci podręcznej obrazów wspólnej):

```azurecli
az vm image list --offer Debian --all --output table 

```

Częściowe dane wyjściowe: 
```
Offer    Publisher    Sku                Urn                                              Version
-------  -----------  -----------------  -----------------------------------------------  --------------
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

Stosowanie filtrów podobne z `--location`, `--publisher`, i `--sku` opcje. Wyniki pasujące częściowo można wykonywać nawet w filtrze, takie jak wyszukiwanie `--offer Deb` można znaleźć wszystkie obrazy Debian.

Jeśli nie określisz określonej lokalizacji z `--location` opcji są zwracane wartości domyślnej lokalizacji. (Ustaw domyślną inną lokalizację, uruchamiając `az configure --defaults location=<location>`.)

Na przykład następujące polecenie wyświetla listę wszystkich Debian SKU 8 w lokalizacji Europa:

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

## <a name="navigate-the-images"></a>Przejdź obrazów 
Innym sposobem znajdowania obrazu w lokalizacji jest uruchomienie [obrazu maszyny wirtualnej az listy wydawców](/cli/azure/vm/image#az_vm_image_list_publishers), [obrazu maszyny wirtualnej az listy oferty](/cli/azure/vm/image#az_vm_image_list_offers), i [obrazu maszyny wirtualnej az listy SKU](/cli/azure/vm/image#az_vm_image_list_skus) polecenia w sekwencji. Przy użyciu następujących poleceń można określić te wartości:

1. Wyświetl listę wydawców obrazów.
2. Dla danego wydawcy wyświetl listę ofert.
3. Dla danej oferty wyświetl listę wersji SKU.

Następnie dla wybranej jednostki SKU, można wybrać wersję do wdrożenia.

Na przykład następujące polecenie wyświetla listę wydawców obrazu w lokalizacji zachodnie stany USA:

```azurecli
az vm image list-publishers --location westus --output table
```

Częściowe dane wyjściowe:

```
Location    Name
----------  ----------------------------------------------------
westus      1e
westus      4psa
westus      7isolutions
westus      a10networks
westus      abiquo
westus      accellion
westus      Acronis
westus      Acronis.Backup
westus      actian_matrix
westus      actifio
westus      activeeon
westus      adatao
...
```
Dzięki tym informacjom można znaleźć ofert od określonego wydawcy. Na przykład jeśli *Canonical* jest wydawcą obrazu w lokalizacji zachodnie stany USA, Znajdź ofert uruchamiając `azure vm image list-offers`. Przekaż lokalizacji i wydawcy, jak w poniższym przykładzie:

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
westus      Ubuntu_Snappy_Core
westus      Ubuntu_Snappy_Core_Docker
```
Zobacz, czy w regionu zachodnie stany USA Canonical publikuje *UbuntuServer* oferują na platformie Azure. Ale o jakie wersje SKU chodzi? Aby uzyskać te wartości, należy uruchomić `azure vm image list-skus` i Ustaw lokalizację, wydawcy i oferty, które zostanie odnalezione:

```azurecli
az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
```

Dane wyjściowe:

```
Location    Name
----------  -----------------
westus      12.04.3-LTS
westus      12.04.4-LTS
westus      12.04.5-DAILY-LTS
westus      12.04.5-LTS
westus      12.10
westus      14.04.0-LTS
westus      14.04.1-LTS
westus      14.04.2-LTS
westus      14.04.3-LTS
westus      14.04.4-LTS
westus      14.04.5-DAILY-LTS
westus      14.04.5-LTS
westus      16.04-beta
westus      16.04-DAILY-LTS
westus      16.04-LTS
westus      16.04.0-LTS
westus      16.10
westus      16.10-DAILY
westus      17.04
westus      17.04-DAILY
westus      17.10-DAILY
```

Na koniec użyj `az vm image list` polecenia, można znaleźć określonej wersji jednostki SKU, na przykład *16.04 LTS*:

```azurecli
az vm image list --location westus --publisher Canonical --offer UbuntuServer --sku 16.04-LTS --all --output table
```

Dane wyjściowe:

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
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703280  16.04.201703280
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703300  16.04.201703300
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201705080  16.04.201705080
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201705160  16.04.201705160
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201706100  16.04.201706100
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201706191  16.04.201706191
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201707210  16.04.201707210
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201707270  16.04.201707270
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201708030  16.04.201708030
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201708110  16.04.201708110
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201708151  16.04.201708151
```

Teraz można precyzyjnie obraz, który ma być używany przez biorąc pod uwagę wartość URN. Przekaż tę wartość z `--image` parametru podczas tworzenia maszyny Wirtualnej z [tworzenia maszyny wirtualnej az](/cli/azure/vm#az_vm_create) polecenia. Należy pamiętać, że można opcjonalnie zastąpić numeru wersji w nazwy URN "r". Ta wersja jest zawsze najnowszą wersję obrazu. 

Należy wdrożyć maszynę Wirtualną za pomocą szablonu usługi Resource Manager, należy ustawić parametry obrazu indywidualnie w `imageReference` właściwości. Zobacz [odwołania do szablonu](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Wyświetl właściwości planu
Aby wyświetlić informacji o planie zakupu obrazu, uruchom [Pokaż obraz maszyny wirtualnej az](/cli/azure/image#az_image_show) polecenia. Jeśli `plan` właściwość w danych wyjściowych nie jest `null`, obraz ma warunki musisz zaakceptować przed wdrażaniem programowym.

Na przykład obraz Canonical Ubuntu Server 16.04 LTS nie ma dodatkowe postanowienia, ponieważ `plan` informacje są `null`:

```azurecli
az vm image show --location westus --publisher Canonical --offer UbuntuServer --sku 16.04-LTS --version 16.04.201801260
```

Dane wyjściowe:

```
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/16.04-LTS/Versions/16.04.201801260",
  "location": "westus",
  "name": "16.04.201801260",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": null,
  "tags": null
}
```

Uruchomiony podobne polecenie RabbitMQ certyfikowanych przez obraz Bitnami zawiera następujące `plan` właściwości: `name`, `product`, i `publisher`. (Niektóre obrazy również mieć `promotion code` właściwości.) Aby wdrożyć ten obraz, zobacz następujące sekcje, aby zaakceptować warunki i włączyć wdrożenia programowe.

```azurecli
az vm image show --location westus --publisher bitnami --offer rabbitmq --sku rabbitmq --version 3.7.1801130730
```
Dane wyjściowe:

```
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/bitnami/ArtifactTypes/VMImage/Offers/rabbitmq/Skus/rabbitmq/Versions/3.7.1801130730",
  "location": "westus",
  "name": "3.7.1801130730",
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
Aby wyświetlić i zaakceptuj postanowienia licencyjne, użyj [az wirtualna obrazu zaakceptować — warunki](/cli/azure/vm/image?#az_vm_image_accept_terms) polecenia. Jeśli akceptujesz jej warunki, umożliwiasz wdrażania programowego w ramach subskrypcji. Musisz zaakceptować postanowienia raz w subskrypcji dla obrazu. Na przykład:

```azurecli
az vm image accept-terms --urn bitnami:rabbitmq:rabbitmq:latest
``` 

Dane wyjściowe obejmują `licenseTextLink` do licencji definicje terminów i wskazuje, że wartość `accepted` jest `true`:

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
  "signature": "WVIEA3LAZIK7ZL2YRV5JYQXONPV76NQJW3FKMKDZYCRGXZYVDGX6BVY45JO3BXVMNA2COBOEYG2NO76ONORU7ITTRHGZDYNJNKLNLWI",
  "type": "Microsoft.MarketplaceOrdering/offertypes"
}
```

### <a name="deploy-using-purchase-plan-parameters"></a>Wdrażanie przy użyciu parametrów planu zakupów
Po zaakceptowaniu warunków obrazu, można wdrożyć maszyny Wirtualnej w ramach subskrypcji. Aby wdrożyć obraz za pomocą `az vm create` polecenia, stanowią parametry dla planu zakupów dodatkowo URN obrazu. Na przykład, aby wdrożyć maszynę Wirtualną z certyfikowane RabbitMQ za Bitnami obrazu:

```azurecli
az group create --name myResourceGroupVM --location westus

az vm create --resource-group myResourceGroupVM --name myVM --image bitnami:rabbitmq:rabbitmq:latest --plan-name rabbitmq --plan-product rabbitmq --plan-publisher bitnami

```



## <a name="next-steps"></a>Kolejne kroki
Aby szybko utworzyć maszynę wirtualną, korzystając z obrazu informacji, zobacz [tworzenie i zarządzanie maszyn wirtualnych systemu Linux z wiersza polecenia platformy Azure](tutorial-manage-vm.md).