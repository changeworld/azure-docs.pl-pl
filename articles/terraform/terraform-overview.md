---
title: Przy użyciu Terraform w usłudze Azure
description: Wprowadzenie do korzystania z Terraform do program w wersji i wdrażanie infrastruktury platformy Azure.
ms.service: virtual-machines-linux
keywords: terraform, devops, przegląd, planowanie, zastosowanie oraz automatyzacji
author: binderjoe
ms.author: jbinder
ms.date: 10/19/2017
ms.topic: article
ms.openlocfilehash: 667752d8830cdac5e2338fd3ed7904917123be94
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2017
ms.locfileid: "26364474"
---
# <a name="terraform-with-azure"></a>Terraform z platformy Azure

[Hashicorp Terraform](https://www.terraform.io/) to narzędzie typu open source do obsługi i zarządzania nimi infrastruktury chmury. Określa on zasady infrastruktury w plikach konfiguracji, które opisują topologii zasobów w chmurze, takich jak maszyny wirtualne, konta magazynu i interfejsów sieci. W Terraform interfejsu wiersza polecenia (CLI) zawiera prosty mechanizm wdrażania i wersji pliki konfiguracyjne do platformy Azure lub innych obsługiwanych chmury.

W tym artykule opisano korzyści wynikające ze stosowania Terraform do zarządzania infrastrukturą systemu Azure.

## <a name="automate-infrastructure-management"></a>Automatyczne zarządzanie infrastrukturą.

Pliki konfiguracji na podstawie szablonu w Terraform umożliwiają definiowanie, udostępnić i skonfigurować zasoby platformy Azure w sposób powtarzalny i przewidywalne. Automatyzowanie infrastruktury ma wiele zalet:

- Zmniejsza potencjalnych błędów ludzkich podczas wdrażania infrastruktury i zarządzanie nią.
- Wdraża tego samego szablonu wielokrotnie Utwórz identyczne środowiska deweloperskie, badanie i produkcji.
- Zmniejsza koszt środowisk projektowania i testowania, ponieważ są one tworzone na żądanie.

## <a name="understand-infrastructure-changes-before-they-are-applied"></a>Zrozumienie zmian w infrastrukturze przed ich zastosowaniem 

Jako zasób topologii staje się złożonych opis znaczenie i wpływ zmiany infrastruktury może być trudne.

Terraform udostępnia interfejsu wiersza polecenia (CLI), który umożliwia użytkownikom sprawdzania poprawności i Podgląd zmian w infrastrukturze przed ich wdrożeniem. Podgląd zmian w infrastrukturze w sejfie, sposób produktywności ma wiele zalet:
- Członkowie zespołu można bardziej efektywnej współpracy przez szybkie opis proponowanych zmian i ich wpływu.
- Niezamierzone zmiany może być wykrytych wczesnym etapie projektowania


## <a name="deploy-infrastructure-to-multiple-clouds"></a>Wdrażanie infrastruktury na wiele chmur

Terraform nadaje popularne narzędzia dla chmury wielu scenariuszy, wdrożonym podobne infrastruktury na platformie Azure i dostawców w chmurze dodatkowe albo na lokalnych centrów danych. Umożliwia ona deweloperom używać tych samych narzędzi i pliki konfiguracji do zarządzania infrastruktury na wielu dostawców chmury.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz omówienie Terraform i korzyści, w tym miejscu są Sugerowane następne kroki:

- Rozpoczynanie pracy przez [Terraform Instalowanie i konfigurowanie go do użycia usługi Azure](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure).
- [Utwórz maszynę wirtualną platformy Azure przy użyciu Terraform](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-create-complete-vm)
- Eksploruj [moduł usługi Azure Resource Manager dla Terraform](https://www.terraform.io/docs/providers/azurerm/) 