---
title: Przy użyciu Ansible w usłudze Azure
description: Wprowadzenie do korzystania z Ansible do automatyzuje obsługę chmury, zarządzanie konfiguracją i wdrożeń aplikacji.
ms.service: ansible
keywords: ansible, azure, przegląd, moduły ansible, udostępniania chmury, wdrożenia aplikacji, devops, zarządzania konfiguracją, ansible playbooks
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 01/19/2018
ms.topic: article
ms.openlocfilehash: a7ce3c239a50462a9af137eb958268f72dbf79d1
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2018
ms.locfileid: "28212106"
---
# <a name="ansible-with-azure"></a>Ansible z platformy Azure

[Ansible](http://www.ansible.com) jest to produkt typu open source, który automatyzuje obsługę chmury, zarządzanie konfiguracją i wdrożeń aplikacji. Za pomocą Ansible można udostępnić sieć maszyn wirtualnych, kontenery i i ukończyć infrastruktury chmury. Ponadto Ansible pozwala zautomatyzować wdrożenie i konfigurację systemu zasobów w danym środowisku.

Ten artykuł zawiera omówienie niektóre korzyści wynikające ze stosowania Ansible z platformy Azure.

## <a name="ansible-playbooks"></a>Ansible playbooks

[Ansible playbooks](http://docs.ansible.com/ansible/latest/playbooks.html) Ansible w konfiguracji, wdrażania i aranżacji języka. Opisują one ma z systemów zdalnych, aby wymusić zasady lub zestaw kroków w ogólne procesów IT. Po utworzeniu podręcznika dotyczącego można to zrobić przy użyciu yaml programu, który definiuje model konfiguracji lub procesu.

## <a name="ansible-modules"></a>Moduły Ansible

Ansible zawiera pakiet [modułów Ansible](http://docs.ansible.com/ansible/latest/modules_by_category.html) mogą być wykonywane bezpośrednio na hostach zdalnych lub za pośrednictwem [playbooks](http://docs.ansible.com/ansible/latest/playbooks.html). Użytkownicy mogą także tworzyć własne moduły. Moduły mogą służyć do kontrolowania zasobów systemowych — takie jak usługi, pakiety lub plików — lub wykonywania poleceń systemu.

Do interakcji z usługami Azure, Ansible zawiera pakiet [Ansible chmury modułów](http://docs.ansible.com/ansible/list_of_cloud_modules.html#azure) zapewnia narzędzia do łatwego tworzenia i organizowania infrastruktury na platformie Azure. 

## <a name="migrate-existing-workload-to-azure"></a>Migrowanie istniejących obciążeń na platformie Azure

Po Ansible ma być używany do definiowania infrastruktury, możesz zastosować podręcznika dotyczącego aplikacji, dzięki czemu Azure automatycznie skalować środowiska zgodnie z potrzebami. 

## <a name="automate-cloud-native-application-in-azure"></a>Automatyzowanie aplikacji natywnych chmurze na platformie Azure

Ansible pozwala zautomatyzować aplikacje natywne w chmurze na platformie Azure przy użyciu mikrousług Azure, takich jak [usługi Azure Functions](https://azure.microsoft.com//services/functions/) i [Kubernetes na platformie Azure](https://azure.microsoft.com/services/container-service/kubernetes/)).  

## <a name="manage-deployments-with-dynamic-inventory"></a>Zarządzanie wdrożeniami w spisie dynamiczne
Za pomocą jego [dynamiczne spisu](http://docs.ansible.com/ansible/intro_dynamic_inventory.html) funkcji, Ansible zapewnia możliwość ściągania spisu z zasobów platformy Azure. Można następnie tagu istniejących wdrożeń platformy Azure i zarządzanie wdrożeniami tych oznakowanych Ansible.

## <a name="additional-azure-marketplace-options"></a>Dodatkowe opcje portalu Azure Marketplace
[Wieża Ansible](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.ansible-tower) obrazu z witryny Marketplace Azure przez Red Hat pomaga organizacjom skalować ich automatyzacji i zarządzanie złożone wdrożenia za pośrednictwem fizycznych, wirtualnych oraz infrastruktury chmury. Wieża Ansible zawiera funkcje, które zapewniają dodatkowe poziomy widoczność, kontroli zabezpieczeń i wydajności niezbędne dla współczesnych przedsiębiorstwach. Wieża Ansible szyfruje poświadczenia, takie jak klucze Azure i SSH, dzięki czemu możesz delegować zadania, mniej doświadczeni pracownikom bez ryzyka związanego z ujawnieniem poświadczeń.

## <a name="next-steps"></a>Kolejne kroki
- [Skonfiguruj Ansible](/azure/virtual-machines/linux/ansible-install-configure?toc=%2Fen-us%2Fazure%2Fansible%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Utwórz Maszynę wirtualną systemu Linux](/azure/virtual-machines/linux/ansible-create-vm?toc=%2Fen-us%2Fazure%2Fansible%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)