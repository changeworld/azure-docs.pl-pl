---
title: Korzystanie z narzędzia Terraform na platformie Azure
description: Wprowadzenie do wersjonowania i wdrażania infrastruktury platformy Azure za pomocą narzędzia Terraform.
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: overview
ms.date: 10/26/2019
ms.openlocfilehash: 1c6ac9b67f556b039b9ffd5ed725ea1f24aeeb3a
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969245"
---
# <a name="terraform-with-azure"></a>Narzędzie Terraform na platformie Azure

[Hashicorp Terraform](https://www.terraform.io/) jest narzędziem typu open source do aprowizowania infrastruktury chmury i zarządzania nią. Zawiera ona skodyfikowaną infrastrukturę w plikach konfiguracji, która opisuje topologię zasobów w chmurze. Te zasoby obejmują maszyny wirtualne, konta magazynu i interfejsy sieciowe. Interfejs wiersza polecenia Terraform udostępnia prosty mechanizm wdrażania plików konfiguracji i ich wersji na platformie Azure.

W tym artykule opisano korzyści wynikające z zarządzania infrastrukturą platformy Azure za pomocą narzędzia Terraform.

## <a name="automate-infrastructure-management"></a>Automatyzacja zarządzania infrastrukturą

Oparte na szablonach pliki konfiguracji narzędzia Terraform umożliwiają definiowanie, aprowizowanie i konfigurowanie zasobów platformy Azure w powtarzalny i przewidywalny sposób. Zautomatyzowanie infrastruktury przynosi kilka korzyści:

- Zmniejsza możliwość popełnienia błędów przez człowieka podczas wdrażania infrastruktury i zarządzania nią.
- Wielokrotnie wdraża ten sam szablon, aby tworzyć identyczne środowiska programowania, testowania i produkcji.
- Zmniejsza koszt środowisk programowania i testowania, tworząc je na żądanie.

## <a name="understand-infrastructure-changes-before-being-applied"></a>Informacje o zmianach infrastruktury przed ich zastosowaniem

Gdy topologia zasobów staje się coraz bardziej skomplikowana, zrozumienie znaczenia i wpływu zmian w infrastrukturze może być trudne.

Interfejs wiersza polecenia Terraform umożliwia użytkownikom weryfikowanie i przeglądanie zmian infrastruktury przed zastosowaniem aplikacji. Podgląd zmian w infrastrukturze w bezpieczny sposób ma kilka zalet:
- Członkowie zespołu mogą współpracować bardziej efektywnie dzięki szybkiemu zrozumieniu proponowanych zmian i ich wpływu.
- Niezamierzone zmiany można wychwycić na wczesnym etapie procesu programowania.

## <a name="deploy-infrastructure-to-multiple-clouds"></a>Wdrażanie infrastruktury w wielu chmurach

Terraform jest doświadczony podczas wdrażania infrastruktury dla wielu dostawców chmury. Umożliwia deweloperom używanie spójnych narzędzi do zarządzania każdą definicją infrastruktury.

## <a name="next-steps"></a>Następne kroki

Oto następne sugerowane kroki po zapoznaniu się z omówieniem narzędzia Terraform i jego zalet:

- Zacznij od [zainstalowania narzędzia Terraform i skonfigurowania go na potrzeby korzystania z platformy Azure](/azure/virtual-machines/linux/terraform-install-configure).
- [Utwórz maszynę wirtualną platformy Azure przy użyciu narzędzia Terraform](/azure/virtual-machines/linux/terraform-create-complete-vm).
- Zapoznaj się z tematem [Moduł usługi Azure Resource Manager dla narzędzia Terraform](https://www.terraform.io/docs/providers/azurerm/). 