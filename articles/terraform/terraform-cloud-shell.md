---
title: Samouczek — konfigurowanie powłoki chmury azure dla terraform
description: W tym samouczku używasz Terraform z usługi Azure Cloud Shell, aby uprościć uwierzytelnianie i konfigurację szablonu.
keywords: azure devops terraform powłoki chmury
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 3a9db1143ba07b549a271d53d610e0a4853467c6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78945331"
---
# <a name="tutorial-configure-azure-cloud-shell-for-terraform"></a>Samouczek: Konfigurowanie powłoki chmury azure dla terraform

Terraform działa dobrze z wiersza polecenia Bash w systemach macOS, Windows lub Linux. Uruchamianie konfiguracji terraform w doświadczeniu bash [usługi Azure Cloud Shell](/azure/cloud-shell/overview) ma pewne unikalne zalety. W tym samouczku pokazano, jak napisać skrypty Terraform, które wdrażają na platformie Azure przy użyciu usługi Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Konfiguracja poświadczeń automatycznych

Narzędzie Terraform jest instalowane i od razu dostępne w usłudze Cloud Shell. Skrypty Terraform uwierzytelniają się za pomocą platformy Azure po zalogowaniu się do usługi Cloud Shell w celu zarządzania infrastrukturą bez dodatkowej konfiguracji. Uwierzytelnianie automatyczne pomija dwa procesy ręczne:
- Tworzenie jednostki usługi Active Directory
- Konfigurowanie zmiennych dostawcy usługi Azure Terraform


## <a name="use-modules-and-providers"></a>Korzystanie z modułów i dostawców

Moduły usługi Azure Terraform wymagają poświadczeń, aby uzyskać dostęp do zasobów platformy Azure i zmodyfikować go. Aby użyć modułów Terraform w usłudze Cloud Shell, dodaj następujący kod:


```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
    # The "feature" block is required for AzureRM provider 2.x. 
    # If you are using version 1.x, the "features" block is not allowed.
    version = "~>2.0"
    features {}
}
```

Usługa Cloud Shell przekazuje `azurerm` wymagane wartości dla dostawcy za pośrednictwem `terraform` zmiennych środowiskowych podczas korzystania z dowolnego polecenia interfejsu wiersza polecenia.

## <a name="other-cloud-shell-developer-tools"></a>Inne narzędzia programistyczne w usłudze Cloud Shell

Pliki i stany powłoki pozostają w usłudze Azure Storage między sesjami usługi Cloud Shell. Użyj [Eksploratora usługi Azure Storage,](/azure/vs-azure-tools-storage-manage-with-storage-explorer) aby skopiować i przekazać pliki do usługi Cloud Shell z komputera lokalnego.

Narzędzie interfejsu wiersza polecenia platformy Azure jest dostępne w usłudze Cloud `terraform apply` `terraform destroy` Shell i jest doskonałym narzędziem do testowania konfiguracji i sprawdzania pracy po zakończeniu lub zakończeniu.


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie małego klastra maszyn wirtualnych przy użyciu rejestru modułów](terraform-create-vm-cluster-module.md)