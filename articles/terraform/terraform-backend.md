---
title: Używać usługi Azure Storage jako zaplecza programu Terraform
description: Wprowadzenie do przechowywania stanu programu Terraform w usłudze Azure Storage.
services: terraform
author: tomarchermsft
ms.service: azure
ms.topic: article
ms.date: 09/13/2018
ms.author: tarcher
ms.openlocfilehash: a88ad25e335026d5172c7997f62629d5ada46f6e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66693298"
---
# <a name="store-terraform-state-in-azure-storage"></a>Stan programu Terraform Store w usłudze Azure Storage

Stan programu Terraform służy do uzgodnienia wdrożonych zasobów przy użyciu programu Terraform konfiguracji. Przy użyciu stanu, Terraform wie, których zasobów platformy Azure, aby dodać, zaktualizować lub usunąć. Domyślnie stan programu Terraform jest przechowywany lokalnie, podczas uruchamiania *zastosować Terraform*. Ta konfiguracja nie jest idealnym rozwiązaniem dla kilka możliwych przyczyn:

- Stan lokalnego nie działa dobrze w zespół lub środowisko współpracy
- Stan programu Terraform mogą zawierać poufne informacje
- Przechowywanie stanu lokalnie zwiększa prawdopodobieństwo przypadkowego usunięcia

Terraform obejmuje koncepcję stan wewnętrznej bazy danych, czyli Magazyn zdalny Terraform stanu. Korzystając z zaplecza stanu, plik stanu jest przechowywany w magazynie danych, takich jak Azure Storage. W tym dokumencie szczegółowo opisuje sposób konfigurowania i używania usługi Azure Storage jako zaplecza programu Terraform stanu.

## <a name="configure-storage-account"></a>Konfigurowanie konta magazynu

Przed rozpoczęciem korzystania z usługi Azure Storage jako zaplecza, można utworzyć konta magazynu. Konto magazynu można utworzyć za pomocą witryny Azure portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure lub programu Terraform, sam. Użyj poniższego przykładu, aby skonfigurować konto magazynu przy użyciu wiersza polecenia platformy Azure.

```azurecli-interactive
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

Zanotuj nazwę konta magazynu, nazwy kontenera i klucz dostępu do magazynu. Te wartości są wymagane podczas konfigurowania zdalnego stanu.

## <a name="configure-state-backend"></a>Skonfiguruj stan wewnętrznej bazy danych

Stan programu Terraform wewnętrznej bazy danych jest skonfigurowane, podczas uruchamiania *Terraform init*. Aby skonfigurować stan wewnętrznej bazy danych, wymagane są następujące dane.

- storage_account_name - nazwa konta usługi Azure Storage.
- container_name — nazwa kontenera obiektów blob.
- klucz: Nazwa stanu przechowywania pliku ma zostać utworzony.
- access_key - klucz dostępu do magazynu.

Każda z tych wartości można określić w pliku konfiguracyjnym programu Terraform lub w wierszu polecenia, jednak zalecane jest użycie zmiennej środowiskowej, aby uzyskać `access_key`. Za pomocą zmiennej środowiskowej uniemożliwia klucza zapisywana na dysku.

Utwórz zmienną środowiskową o nazwie `ARM_ACCESS_KEY` z wartością klucza dostępu do magazynu Azure.

```console
export ARM_ACCESS_KEY=<storage access key>
```

Aby dodatkowo zabezpieczyć klucz dostępu konta magazynu platformy Azure, zapisz go w usłudze Azure Key Vault. Następnie można ustawić zmienną środowiskową przy użyciu polecenia podobnego do następującego. Aby uzyskać więcej informacji na temat usługi Azure Key Vault, zobacz [dokumentacji usługi Azure Key Vault][azure-key-vault].

```console
export ARM_ACCESS_KEY=$(az keyvault secret show --name terraform-backend-key --vault-name myKeyVault --query value -o tsv)
```

Aby skonfigurować narzędzie Terraform w celu użycia wewnętrznej bazy danych, należy dołączyć *zaplecza* konfiguracji o typie *azurerm* wewnątrz konfiguracji programu Terraform. Dodaj *storage_account_name*, *container_name*, i *klucz* wartości do bloku konfiguracji.

Poniższy przykład umożliwia skonfigurowanie programu Terraform w wewnętrznej bazie danych i tworzy grupę zasobów platformy Azure. Zastąp wartości wartościami z używanego środowiska.

```json
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

Teraz zainicjować konfiguracji z *Terraform init* , a następnie uruchom konfigurację z *zastosować Terraform*. Po zakończeniu można znaleźć pliku stanu w usłudze Azure Blob Storage.

## <a name="state-locking"></a>Stan blokowania

Korzystając z usługi Azure Storage Blob do przechowywania stanów, obiekt blob zostanie automatycznie zablokowane przed dowolną operacją, która zapisuje stan. Taka konfiguracja zapobiega wiele operacji współbieżnych stanu, co może powodować uszkodzenie. Aby uzyskać więcej informacji, zobacz [stan blokowania] [ terraform-state-lock] o dokumentacji programu Terraform.

Blokady są widoczne podczas badania obiektów blob za pośrednictwem witryny Azure portal lub inne narzędzie do zarządzania platformy Azure.

![Obiektów blob platformy Azure z blokadą](media/terraform-backend/lock.png)

## <a name="encryption-at-rest"></a>Szyfrowanie w spoczynku

Domyślnie dane przechowywane w usłudze Azure Blob są szyfrowane przed są utrwalane w infrastrukturze magazynu. Gdy Terraform musi stanu, jest pobierana z wewnętrznej bazy danych i przechowywane w pamięci w systemie deweloperskim. W tej konfiguracji stan jest zabezpieczone w usłudze Azure Storage i nie są zapisywane na dysku lokalnym.

Aby uzyskać więcej informacji o szyfrowaniu usługi Azure Storage, zobacz [szyfrowanie usługi Azure Storage dla danych magazynowanych][azure-storage-encryption].

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat narzędzia Terraform w wewnętrznej bazie danych konfiguracji w [dokumentacji wewnętrznej bazy danych programu Terraform][terraform-backend].

<!-- LINKS - internal -->
[azure-key-vault]: ../key-vault/quick-create-cli.md
[azure-storage-encryption]: ../storage/common/storage-service-encryption.md

<!-- LINKS - external -->
[terraform-azurerm]: https://www.terraform.io/docs/backends/types/azurerm.html
[terraform-backend]: https://www.terraform.io/docs/backends/
[terraform-state-lock]: https://www.terraform.io/docs/state/locking.html
