---
title: Korzystanie z usługi Azure Storage jako zaplecza Terraform
description: Wprowadzenie do przechowywania stanu Terraform w usłudze Azure Storage.
services: terraform
author: tomarchermsft
ms.service: azure
ms.topic: article
ms.date: 09/20/2019
ms.author: tarcher
ms.openlocfilehash: e9b447f4f4dc9d0ee090da9729e483cc17ac7c15
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71169946"
---
# <a name="store-terraform-state-in-azure-storage"></a>Przechowywanie stanu Terraform w usłudze Azure Storage

Stan Terraform służy do uzgadniania wdrożonych zasobów z konfiguracjami Terraform. Użycie stanu Terraform wie, jakie zasoby platformy Azure dodać, zaktualizować lub usunąć. Domyślnie stan Terraform jest przechowywany lokalnie podczas działania *Terraform Apply*. Ta konfiguracja nie jest idealna z kilku powodów:

- Stan lokalny nie działa dobrze w zespole ani środowisku współpracy
- Stan Terraform może zawierać informacje poufne
- Przechowywanie stanu lokalnie zwiększa szansę przypadkowego usunięcia

Terraform obejmuje koncepcję zaplecza stanu, czyli Magazyn zdalny dla stanu Terraform. W przypadku korzystania z zaplecza stanu plik stanu jest przechowywany w magazynie danych, takim jak usługa Azure Storage. Ten dokument zawiera szczegółowe informacje dotyczące konfigurowania usługi Azure Storage i używania jej jako zaplecza stanu Terraform.

## <a name="configure-storage-account"></a>Skonfiguruj konto magazynu

Przed rozpoczęciem korzystania z usługi Azure Storage jako zaplecza należy utworzyć konto magazynu. Konto magazynu można utworzyć przy użyciu Azure Portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure lub samego Terraform. Użyj poniższego przykładu, aby skonfigurować konto magazynu za pomocą interfejsu wiersza polecenia platformy Azure.

```azurecli
#!/bin/bash

RESOURCE_GROUP_NAME=tstate
STORAGE_ACCOUNT_NAME=tstate$RANDOM
CONTAINER_NAME=tstate

# Create resource group
az group create --name $RESOURCE_GROUP_NAME --location eastus

# Create storage account
az storage account create --resource-group $RESOURCE_GROUP_NAME --name $STORAGE_ACCOUNT_NAME --sku Standard_LRS --encryption-services blob

# Get storage account key
ACCOUNT_KEY=$(az storage account keys list --resource-group $RESOURCE_GROUP_NAME --account-name $STORAGE_ACCOUNT_NAME --query [0].value -o tsv)

# Create blob container
az storage container create --name $CONTAINER_NAME --account-name $STORAGE_ACCOUNT_NAME --account-key $ACCOUNT_KEY

echo "storage_account_name: $STORAGE_ACCOUNT_NAME"
echo "container_name: $CONTAINER_NAME"
echo "access_key: $ACCOUNT_KEY"
```

Zanotuj nazwę konta magazynu, nazwę kontenera i klucz dostępu do magazynu. Te wartości są odpowiednie podczas konfigurowania stanu zdalnego.

## <a name="configure-state-backend"></a>Konfigurowanie zaplecza stanu

Zaplecze stanu Terraform jest konfigurowane podczas uruchamiania *Terraform init*. Aby można było skonfigurować zaplecze stanu, wymagane są następujące dane.

- storage_account_name — nazwa konta usługi Azure Storage.
- container_name — nazwa kontenera obiektów BLOB.
- Key — nazwa pliku magazynu Stanów, który ma zostać utworzony.
- access_key — klucz dostępu do magazynu.

Każdą z tych wartości można określić w pliku konfiguracji Terraform lub w wierszu polecenia, jednak zaleca się użycie zmiennej środowiskowej dla programu `access_key`. Użycie zmiennej środowiskowej uniemożliwia zapisanie klucza na dysku.

Utwórz zmienną środowiskową o `ARM_ACCESS_KEY` nazwie przy użyciu wartości klucza dostępu do usługi Azure Storage.

```bash
export ARM_ACCESS_KEY=<storage access key>
```

Aby dodatkowo chronić klucz dostępu do konta usługi Azure Storage, Zapisz go w Azure Key Vault. Zmienną środowiskową można następnie ustawić przy użyciu polecenia podobnego do poniższego. Aby uzyskać więcej informacji na temat Azure Key Vault, zobacz [dokumentację Azure Key Vault][azure-key-vault].

```bash
export ARM_ACCESS_KEY=$(az keyvault secret show --name terraform-backend-key --vault-name myKeyVault --query value -o tsv)
```

Aby skonfigurować Terraform do korzystania z zaplecza, należy uwzględnić konfigurację zaplecza z typem *Azurerm* wewnątrz konfiguracji Terraform. Dodaj wartości *storage_account_name*, *container_name*i *Key* do bloku Configuration.

Poniższy przykład umożliwia skonfigurowanie zaplecza Terraform i utworzenie grupy zasobów platformy Azure. Zastąp wartości wartościami ze środowiska.

```hcl
terraform {
  backend "azurerm" {
    storage_account_name  = "tstate09762"
    container_name        = "tstate"
    key                   = "terraform.tfstate"
  }
}

resource "azurerm_resource_group" "state-demo-secure" {
  name     = "state-demo"
  location = "eastus"
}
```

Teraz zainicjuj konfigurację przy użyciu *Terraform init* , a następnie uruchom konfigurację z *Terraform Apply*. Po zakończeniu można znaleźć plik stanu w Azure Storage Blob.

## <a name="state-locking"></a>Blokowanie stanu

W przypadku korzystania z Azure Storage Blob dla magazynu Stanów obiekt BLOB jest automatycznie blokowany przed jakąkolwiek operacją, która zapisuje stan. Ta konfiguracja uniemożliwia wiele współbieżnych operacji na stanie, co może spowodować uszkodzenie. Aby uzyskać więcej informacji, zobacz [blokowanie stanu][terraform-state-lock] w dokumentacji Terraform.

Blokadę można zobaczyć podczas badania obiektu BLOB za pomocą Azure Portal lub innych narzędzi do zarządzania platformy Azure.

![Obiekt blob platformy Azure z blokadą](media/terraform-backend/lock.png)

## <a name="encryption-at-rest"></a>Szyfrowanie w spoczynku

Domyślnie dane przechowywane w obiekcie blob platformy Azure są szyfrowane przed utrwaleniem do infrastruktury magazynu. Gdy Terraform wymaga stanu, zostanie pobrany z zaplecza i zapisany w pamięci w systemie deweloperskim. W tej konfiguracji stan jest zabezpieczany w usłudze Azure Storage i nie jest zapisywana na dysku lokalnym.

Aby uzyskać więcej informacji na temat szyfrowania usługi Azure Storage, zobacz [szyfrowanie usługi Storage platformy Azure dla danych][azure-storage-encryption]przechowywanych.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o konfiguracji zaplecza Terraform w [dokumentacji zaplecza Terraform][terraform-backend].

<!-- LINKS - internal -->
[azure-key-vault]: ../key-vault/quick-create-cli.md
[azure-storage-encryption]: ../storage/common/storage-service-encryption.md

<!-- LINKS - external -->
[terraform-azurerm]: https://www.terraform.io/docs/backends/types/azurerm.html
[terraform-backend]: https://www.terraform.io/docs/backends/
[terraform-state-lock]: https://www.terraform.io/docs/state/locking.html
