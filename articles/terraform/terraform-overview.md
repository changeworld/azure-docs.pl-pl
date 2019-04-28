---
title: Korzystanie z narzędzia Terraform na platformie Azure
description: Wprowadzenie do wersjonowania i wdrażania infrastruktury platformy Azure za pomocą narzędzia Terraform.
services: terraform
ms.service: azure
keywords: terraform, devops, overview, plan, apply, automate
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 08/31/2018
ms.openlocfilehash: 8b5ad3f060e2c8760014d7fc1990b971976388f7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60884447"
---
# <a name="terraform-with-azure"></a>Narzędzie Terraform na platformie Azure

[Hashicorp Terraform](https://www.terraform.io/) jest narzędziem typu open source do aprowizowania infrastruktury chmury i zarządzania nią. Określa ono infrastrukturę w plikach konfiguracji opisujących topologię zasobów w chmurze, takich jak maszyny wirtualne, konta magazynu i interfejsy sieciowe. Interfejs wiersza polecenia (CLI) narzędzia Terraform zawiera prosty mechanizm wdrażania i wersjonowania plików konfiguracji na platformie Azure lub w dowolnej innej obsługiwanej chmurze.

W tym artykule opisano korzyści wynikające z zarządzania infrastrukturą platformy Azure za pomocą narzędzia Terraform.

## <a name="automate-infrastructure-management"></a>Automatyzacja zarządzania infrastrukturą

Oparte na szablonach pliki konfiguracji narzędzia Terraform umożliwiają definiowanie, aprowizowanie i konfigurowanie zasobów platformy Azure w powtarzalny i przewidywalny sposób. Zautomatyzowanie infrastruktury przynosi kilka korzyści:

- Zmniejsza możliwość popełnienia błędów przez człowieka podczas wdrażania infrastruktury i zarządzania nią.
- Wielokrotnie wdraża ten sam szablon, aby tworzyć identyczne środowiska programowania, testowania i produkcji.
- Zmniejsza koszt środowisk programowania i testowania, tworząc je na żądanie.

## <a name="understand-infrastructure-changes-before-they-are-applied"></a>Omówienie zmian w infrastrukturze przed ich zastosowaniem 

Gdy topologia zasobów staje się coraz bardziej skomplikowana, zrozumienie znaczenia i wpływu zmian w infrastrukturze może być trudne.

Narzędzie Terraform zapewnia interfejs wiersza polecenia (CLI), który umożliwia użytkownikom zweryfikowanie i wyświetlenie podglądu zmian w infrastrukturze przed ich wdrożeniem. Wyświetlenie podglądu zmian w infrastrukturze w bezpieczny, produktywny sposób przynosi kilka korzyści:
- Członkowie zespołu mogą współpracować bardziej efektywnie dzięki szybkiemu zrozumieniu proponowanych zmian i ich wpływu.
- Niezamierzone zmiany można wychwycić na wczesnym etapie procesu programowania.


## <a name="deploy-infrastructure-to-multiple-clouds"></a>Wdrażanie infrastruktury w wielu chmurach

Terraform to popularne narzędzie w przypadku scenariuszy z wieloma chmurami, w których podobna infrastruktura jest wdrażana na platformie Azure oraz u dodatkowych dostawców usług w chmurze lub w lokalnych centrach danych. Umożliwia ono deweloperom zarządzanie infrastrukturą u wielu dostawców chmury za pomocą tych samych narzędzi i plików konfiguracji.

## <a name="next-steps"></a>Kolejne kroki

Oto następne sugerowane kroki po zapoznaniu się z omówieniem narzędzia Terraform i jego zalet:

- Zacznij od [zainstalowania narzędzia Terraform i skonfigurowania go na potrzeby korzystania z platformy Azure](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure).
- [Utwórz maszynę wirtualną platformy Azure przy użyciu narzędzia Terraform](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-create-complete-vm).
- Zapoznaj się z tematem [Moduł usługi Azure Resource Manager dla narzędzia Terraform](https://www.terraform.io/docs/providers/azurerm/). 