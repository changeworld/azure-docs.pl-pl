---
title: Terraform za pomocą powłoki w chmurze Azure
description: Upraszczanie uwierzytelniania i konfiguracji szablonu przy użyciu Terraform z powłoki chmury Azure.
keywords: terraform, devops, skalowanie maszyny ustawiony, wirtualnych, sieci, magazynu, modułów
ms.service: virtual-machines-linux
author: dcaro
ms.author: dcaro
ms.date: 10/19/2017
ms.topic: article
ms.openlocfilehash: 5157066086f1bdfa580c1946942bda4505e48935
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2018
ms.locfileid: "29121529"
---
# <a name="terraform-cloud-shell-development"></a>Programowanie Terraform chmury powłoki 

Terraform rozwiązanie idealne z wiersza polecenia Bash, takich jak system macOS Terminal lub Bash w systemie Windows lub Linux. Uruchomione z Terraform konfiguracje Bash środowisko [powłoki chmury Azure](/azure/cloud-shell/overview) ma niektóre zalety unikatowy, aby przyspieszyć programowanie cykl.

W tym artykule pojęcia obejmuje powłoki chmury funkcje, które ułatwiają pisanie skryptów Terraform, które wdrażanie na platformie Azure.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Poświadczenie automatycznej konfiguracji

Terraform jest zainstalowana i natychmiast dostępna w chmurze powłoki. Skrypty Terraform uwierzytelniania za pomocą usługi Azure podczas logowania chmurze powłoki zarządzania infrastrukturą bez przeprowadzania dodatkowej konfiguracji. Automatyczne uwierzytelnianie pomija trzeba ręcznie utworzyć główną usługi Active Directory i skonfiguruj zmienne dostawcy Azure Terraform.


## <a name="using-modules-and-providers"></a>Przy użyciu modułów i dostawców

Azure modułów Terraform wymagają poświadczeń w celu dostępu i wprowadź zmiany do zasobów w Twojej subskrypcji platformy Azure. Podczas pracy w powłoce chmury, Dodaj następujący kod do skryptów do użycia w powłoce chmury Azure Terraform modułów:

```tf
# Configure the Microsoft Azure Provider
provider "azurerm" {
}
```

Powłoka chmury przekazuje wymagane wartości `azurerm` dostawcy za pomocą zmiennych środowiskowych przy użyciu dowolnej z `terraform` polecenia interfejsu wiersza polecenia.

## <a name="other-cloud-shell-developer-tools"></a>Inne narzędzia deweloperskie powłoki chmury

Pliki i stanów powłoki zachować w usłudze Azure Storage między sesjami powłoki chmury. Użyj [Eksploratora usługi Storage Azure](/azure/vs-azure-tools-storage-manage-with-storage-explorer) do skopiowania i przekazać pliki do powłoki chmury z komputera lokalnego.

2.0 interfejsu wiersza polecenia platformy Azure jest dostępna w chmurze powłoki i to doskonałe narzędzie do testowania konfiguracji i sprawdzanie pracę po `terraform apply` lub `terraform destroy` zakończeniu.


## <a name="next-steps"></a>Kolejne kroki

[Tworzenie małych klastra maszyny Wirtualnej za pomocą rejestru moduł](terraform-create-vm-cluster-module.md)
[tworzenie małych klastra maszyny Wirtualnej przy użyciu niestandardowych HCL](terraform-create-vm-cluster-with-infrastructure.md)
