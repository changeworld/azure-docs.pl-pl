---
title: Korzystanie z narzędzia Terraform na platformie Azure
description: Wprowadzenie do wersjonowania i wdrażania infrastruktury platformy Azure za pomocą narzędzia Terraform.
ms.topic: overview
ms.date: 10/26/2019
ms.openlocfilehash: 05b92fdf8c0a0f84d2f29b4aa7479850b2721441
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77472166"
---
# <a name="terraform-with-azure"></a>Narzędzie Terraform na platformie Azure

[Hashicorp Terraform](https://www.terraform.io/) jest narzędziem typu open source do aprowizowania infrastruktury chmury i zarządzania nią. To codifies infrastruktury w plikach konfiguracyjnych, które opisują topologii zasobów w chmurze. Zasoby te obejmują maszyny wirtualne, konta magazynu i interfejsy sieciowe. Interfejs wiersza polecenia Terraform zapewnia prosty mechanizm wdrażania i wersji plików konfiguracyjnych na platformie Azure.

W tym artykule opisano korzyści wynikające z zarządzania infrastrukturą platformy Azure za pomocą narzędzia Terraform.

## <a name="automate-infrastructure-management"></a>Automatyzacja zarządzania infrastrukturą

Oparte na szablonach pliki konfiguracji narzędzia Terraform umożliwiają definiowanie, aprowizowanie i konfigurowanie zasobów platformy Azure w powtarzalny i przewidywalny sposób. Zautomatyzowanie infrastruktury przynosi kilka korzyści:

- Zmniejsza możliwość popełnienia błędów przez człowieka podczas wdrażania infrastruktury i zarządzania nią.
- Wielokrotnie wdraża ten sam szablon, aby tworzyć identyczne środowiska programowania, testowania i produkcji.
- Zmniejsza koszt środowisk programowania i testowania, tworząc je na żądanie.

## <a name="understand-infrastructure-changes-before-being-applied"></a>Opis zmian w infrastrukturze przed zastosowaniem

Gdy topologia zasobów staje się coraz bardziej skomplikowana, zrozumienie znaczenia i wpływu zmian w infrastrukturze może być trudne.

Interfejs wiersza polecenia Terraform umożliwia użytkownikom sprawdzanie poprawności i wyświetlanie podglądu zmian infrastruktury przed aplikacją. Podgląd zmian infrastruktury w bezpieczny sposób ma kilka zalet:
- Członkowie zespołu mogą współpracować bardziej efektywnie dzięki szybkiemu zrozumieniu proponowanych zmian i ich wpływu.
- Niezamierzone zmiany można wychwycić na wczesnym etapie procesu programowania.

## <a name="deploy-infrastructure-to-multiple-clouds"></a>Wdrażanie infrastruktury w wielu chmurach

Terraform jest biegły we wdrażaniu infrastruktury w wielu dostawcach chmury. Umożliwia deweloperom korzystanie z spójnych narzędzi do zarządzania każdą definicją infrastruktury.

## <a name="next-steps"></a>Następne kroki

Oto następne sugerowane kroki po zapoznaniu się z omówieniem narzędzia Terraform i jego zalet:

- Zacznij od [zainstalowania narzędzia Terraform i skonfigurowania go na potrzeby korzystania z platformy Azure](terraform-install-configure.md).
- [Utwórz maszynę wirtualną platformy Azure przy użyciu narzędzia Terraform](terraform-create-complete-vm.md).
- Zapoznaj się z tematem [Moduł usługi Azure Resource Manager dla narzędzia Terraform](https://www.terraform.io/docs/providers/azurerm/). 