---
title: Samouczek — przechowywanie stanu Terraform w usłudze Azure Storage
description: Wprowadzenie do przechowywania stanu Terraform w usłudze Azure Storage.
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: cb85897e0a7d281eca4ad3f42e8ef28c9e3fdb7b
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73833540"
---
# <a name="tutorial-store-terraform-state-in-azure-storage"></a>Samouczek: przechowywanie stanu Terraform w usłudze Azure Storage

Stan Terraform służy do uzgadniania wdrożonych zasobów z konfiguracjami Terraform. Stan umożliwia Terraform informacji o zasobach platformy Azure, które należy dodać, zaktualizować lub usunąć. Domyślnie stan Terraform jest przechowywany lokalnie podczas uruchamiania polecenia `terraform apply`. Ta konfiguracja nie jest idealna z następujących powodów:

- Stan lokalny nie działa dobrze w zespole ani środowisku współpracy
- Stan Terraform może zawierać informacje poufne
- Przechowywanie stanu lokalnie zwiększa szansę przypadkowego usunięcia

Terraform obsługuje utrwalanie stanu w magazynie zdalnym. Jednym z obsługiwanych zapleczy jest usługa Azure Storage. W tym dokumencie przedstawiono sposób konfigurowania usługi Azure Storage i korzystania z niej w tym celu.

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

Zaplecze stanu Terraform jest konfigurowane podczas uruchamiania polecenia `terraform init`. do skonfigurowania zaplecza stanu są konieczne następujące dane:

- storage_account_name — nazwa konta usługi Azure Storage.
- container_name — nazwa kontenera obiektów BLOB.
- Key — nazwa pliku magazynu Stanów, który ma zostać utworzony.
- access_key — klucz dostępu do magazynu.

Każdą z tych wartości można określić w pliku konfiguracji Terraform lub w wierszu polecenia, jednak zaleca się użycie zmiennej środowiskowej dla `access_key`. Użycie zmiennej środowiskowej uniemożliwia zapisanie klucza na dysku.

Utwórz zmienną środowiskową o nazwie `ARM_ACCESS_KEY` z wartością klucza dostępu do usługi Azure Storage.

```bash
export ARM_ACCESS_KEY=<storage access key>
```

Aby dodatkowo chronić klucz dostępu do konta usługi Azure Storage, Zapisz go w Azure Key Vault. Zmienną środowiskową można następnie ustawić przy użyciu polecenia podobnego do poniższego. Aby uzyskać więcej informacji na temat Azure Key Vault, zobacz [dokumentację Azure Key Vault](../key-vault/quick-create-cli.md).

```bash
export ARM_ACCESS_KEY=$(az keyvault secret show --name terraform-backend-key --vault-name myKeyVault --query value -o tsv)
```

Aby skonfigurować Terraform do korzystania z zaplecza, należy wykonać następujące czynności:
- Uwzględnij `backend` blok konfiguracji z typem `azurerm`.
- Dodaj wartość `storage_account_name` do bloku konfiguracji.
- Dodaj wartość `container_name` do bloku konfiguracji.
- Dodaj wartość `key` do bloku konfiguracji.

Poniższy przykład umożliwia skonfigurowanie zaplecza Terraform i utworzenie grupy zasobów platformy Azure.

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

Zainicjuj konfigurację, wykonując następujące czynności:

1. Uruchom polecenie `terraform init`.
1. Uruchom polecenie `terraform apply`.

Teraz można znaleźć plik stanu w Azure Storage Blob.

## <a name="state-locking"></a>Blokowanie stanu

Obiekty blob usługi Azure Storage są automatycznie blokowane przed jakąkolwiek operacją, która zapisuje stan. Ten wzorzec uniemożliwia wykonywanie współbieżnych operacji stanu, co może spowodować uszkodzenie. 

Aby uzyskać więcej informacji, zobacz [blokowanie stanu](https://www.terraform.io/docs/state/locking.html) w dokumentacji Terraform.

Blokadę można zobaczyć podczas badania obiektu BLOB za pomocą Azure Portal lub innych narzędzi do zarządzania platformy Azure.

![Obiekt blob platformy Azure z blokadą](media/terraform-backend/lock.png)

## <a name="encryption-at-rest"></a>Szyfrowanie w spoczynku

Dane przechowywane w obiekcie blob platformy Azure są szyfrowane przed utrwaleniem. W razie konieczności Terraform Pobiera stan z zaplecza i zapisuje go w pamięci lokalnej. Korzystając z tego wzorca, stan nigdy nie jest zapisywana na dysku lokalnym.

Aby uzyskać więcej informacji na temat szyfrowania usługi Azure Storage, zobacz [szyfrowanie usługi Storage platformy Azure dla danych](../storage/common/storage-service-encryption.md)przechowywanych.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Dowiedz się więcej o korzystaniu z Terraform na platformie Azure](/azure/terraform)