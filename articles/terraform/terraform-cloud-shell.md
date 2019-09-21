---
title: Konfigurowanie Azure Cloud Shell Terraform
description: Korzystając z narzędzia Terraform z usługą Azure Cloud Shell, można uprościć uwierzytelnianie i konfigurowanie szablonów.
services: terraform
ms.service: azure
keywords: terraform, devops, scale set, virtual machine, network, storage, modules
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2019
ms.openlocfilehash: e0a59697a3e4da97cf082c4c771fe93ad33b6035
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173542"
---
# <a name="terraform-cloud-shell-development"></a>Programowanie za pomocą narzędzia Terraform w usłudze Cloud Shell 

Narzędzie Terraform znakomicie działa z poziomu wiersza polecenia powłoki Bash, na przykład w narzędziu macOS Terminal czy w powłoce Bash w systemie Windows lub Linux. Uruchamianie konfiguracji narzędzia Terraform w środowisku powłoki Bash usługi [Azure Cloud Shell](/azure/cloud-shell/overview) przynosi kilka wyjątkowych korzyści, które przyspieszają cykl tworzenia oprogramowania.

W tym artykule dotyczącym pojęć opisano funkcje usługi Cloud Shell, które ułatwiają pisanie skryptów narzędzia Terraform wdrażanych na platformie Azure.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Konfiguracja poświadczeń automatycznych

Narzędzie Terraform jest instalowane i od razu dostępne w usłudze Cloud Shell. Skrypty narzędzia Terraform uwierzytelniają się na platformie Azure po zalogowaniu do usługi Cloud Shell, umożliwiając zarządzanie infrastrukturą bez konieczności przeprowadzania jakiejkolwiek dodatkowej konfiguracji. Automatyczne uwierzytelnianie omija potrzebę ręcznego tworzenia jednostki usługi Active Directory i konfigurowania zmiennych dostawcy narzędzia Azure Terraform.


## <a name="using-modules-and-providers"></a>Używanie modułów i dostawców

Moduły Azure Terraform wymagają poświadczeń w celu uzyskania dostępu i wprowadzenia zmian w zasobach subskrypcji platformy Azure. Podczas pracy w usłudze Cloud Shell dodaj do skryptów następujący kod, aby używać modułów Azure Terraform w usłudze Cloud Shell:

```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
}
```

Usługa Cloud Shell przekazuje wymagane wartości dla dostawcy `azurerm` za pomocą zmiennych środowiskowych, używając dowolnego z poleceń `terraform` interfejsu wiersza polecenia.

## <a name="other-cloud-shell-developer-tools"></a>Inne narzędzia programistyczne w usłudze Cloud Shell

Pliki i stany powłoki pozostają w usłudze Azure Storage między sesjami usługi Cloud Shell. Używając [Eksploratora usługi Azure Storage](/azure/vs-azure-tools-storage-manage-with-storage-explorer), skopiuj i przekaż pliki z komputera lokalnego do usługi Cloud Shell.

W usłudze Cloud Shell jest dostępny interfejs wiersza polecenia platformy Azure, który stanowi doskonałe narzędzie do testowania konfiguracji i kontrolowania pracy po wykonaniu polecenia `terraform apply` lub `terraform destroy`.


## <a name="next-steps"></a>Następne kroki

[Tworzenie niewielkiego klastra maszyn wirtualnych za pomocą rejestru modułu](terraform-create-vm-cluster-module.md)
[Tworzenie niewielkiego klastra maszyn wirtualnych za pomocą niestandardowego języka HCL](terraform-create-vm-cluster-with-infrastructure.md)
