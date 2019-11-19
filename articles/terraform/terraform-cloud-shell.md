---
title: Samouczek — Konfigurowanie Azure Cloud Shell dla Terraform
description: Korzystając z narzędzia Terraform z usługą Azure Cloud Shell, można uprościć uwierzytelnianie i konfigurowanie szablonów.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: db9edfadbe01edc1ee9df09c284e3895ee11f3d3
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159125"
---
# <a name="tutorial-configure-azure-cloud-shell-for-terraform"></a>Samouczek: Konfigurowanie Azure Cloud Shell dla Terraform

Terraform dobrze działa z wiersza polecenia bash w systemie macOS, Windows lub Linux. Uruchamianie konfiguracji Terraform w środowisku bash [Azure Cloud Shell](/azure/cloud-shell/overview) ma pewne unikatowe zalety. W tym samouczku pokazano, jak napisać skrypty Terraform wdrażane na platformie Azure przy użyciu Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Konfiguracja poświadczeń automatycznych

Narzędzie Terraform jest instalowane i od razu dostępne w usłudze Cloud Shell. Skrypty Terraform uwierzytelniają się przy użyciu platformy Azure podczas logowania w celu Cloud Shell zarządzania infrastrukturą bez dodatkowej konfiguracji. Automatyczne uwierzytelnianie pomija dwa procesy ręczne:
- Tworzenie jednostki usługi Active Directory
- Konfigurowanie zmiennych dostawcy usługi Azure Terraform


## <a name="use-modules-and-providers"></a>Korzystanie z modułów i dostawców

Moduły Terraform platformy Azure wymagają poświadczeń w celu uzyskania dostępu do zasobów platformy Azure i ich modyfikowania. Aby użyć modułów Terraform w Cloud Shell, Dodaj następujący kod:


```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
}
```

Cloud Shell przekazuje wymagane wartości dostawcy `azurerm` za pomocą zmiennych środowiskowych w przypadku korzystania z poleceń interfejsu wiersza polecenia `terraform`.

## <a name="other-cloud-shell-developer-tools"></a>Inne narzędzia programistyczne w usłudze Cloud Shell

Pliki i stany powłoki pozostają w usłudze Azure Storage między sesjami usługi Cloud Shell. Użyj [Eksplorator usługi Azure Storage](/azure/vs-azure-tools-storage-manage-with-storage-explorer) do kopiowania i przekazywania plików do Cloud Shell z komputera lokalnego.

Interfejs wiersza polecenia platformy Azure jest dostępny w Cloud Shell i jest doskonałym narzędziem do testowania konfiguracji i sprawdzania pracy po zakończeniu `terraform apply` lub `terraform destroy`.


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie małego klastra maszyn wirtualnych przy użyciu rejestru modułu](terraform-create-vm-cluster-module.md)
