---
title: Samouczek — Konfigurowanie Azure Cloud Shell dla Terraform
description: Korzystając z narzędzia Terraform z usługą Azure Cloud Shell, można uprościć uwierzytelnianie i konfigurowanie szablonów.
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 693ed462fb1ba3dfed079e8ae97152732c771253
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969595"
---
# <a name="tutorial-configure-azure-cloud-shell-for-terraform"></a>Samouczek: Konfigurowanie Azure Cloud Shell dla Terraform

Terraform dobrze działa z wiersza polecenia bash w systemie macOS, Windows lub Linux. Uruchamianie konfiguracji Terraform w środowisku bash [Azure Cloud Shell](/azure/cloud-shell/overview) ma pewne unikatowe zalety. W tym samouczku pokazano, jak napisać skrypty Terraform wdrażane na platformie Azure przy użyciu Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Konfiguracja poświadczeń automatycznych

Narzędzie Terraform jest instalowane i od razu dostępne w usłudze Cloud Shell. Skrypty narzędzia Terraform uwierzytelniają się na platformie Azure po zalogowaniu do usługi Cloud Shell, umożliwiając zarządzanie infrastrukturą bez konieczności przeprowadzania jakiejkolwiek dodatkowej konfiguracji. Automatyczne uwierzytelnianie pomija dwa procesy ręczne:
- Tworzenie jednostki usługi Active Directory.
- Konfigurowanie zmiennych dostawcy usługi Azure Terraform.


## <a name="using-modules-and-providers"></a>Używanie modułów i dostawców

Moduły Terraform platformy Azure wymagają poświadczeń w celu uzyskania dostępu do zasobów platformy Azure i ich modyfikowania. Aby użyć modułów Terraform w Cloud Shell, Dodaj następujący kod:


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

> [!div class="nextstepaction"]
> [Tworzenie małego klastra maszyn wirtualnych przy użyciu rejestru modułu](terraform-create-vm-cluster-module.md)