---
title: Za pomocą platformy Azure za pomocą rozwiązania Ansible
description: Wprowadzenie do korzystania z rozwiązania Ansible w celu automatyzuje aprowizowanie chmury, zarządzanie konfiguracją i wdrożenia aplikacji.
ms.service: ansible
keywords: rozwiązania ansible, azure, omówienie, modułów rozwiązania ansible, aprowizowanie chmury, wdrażanie aplikacji, devops, zarządzanie konfiguracją, elementy playbook rozwiązania ansible
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 01/19/2018
ms.topic: article
ms.openlocfilehash: e710770131c844598762feebe09ba50dc120de0c
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43106904"
---
# <a name="ansible-with-azure"></a>Rozwiązanie Ansible na platformie Azure

[Rozwiązanie Ansible](http://www.ansible.com) to produkt typu open source, który automatyzuje aprowizowanie chmury, zarządzanie konfiguracją i wdrożenia aplikacji. Za pomocą rozwiązania Ansible można aprowizować maszyny wirtualne, kontenery i sieci i ukończyć infrastruktur chmury. Ponadto rozwiązania Ansible pozwala na zautomatyzowanie wdrażania i konfiguracji zasobów w danym środowisku.

Ten artykuł zawiera omówienie niektórych korzyści z używania rozwiązania Ansible na platformie Azure.

## <a name="ansible-playbooks"></a>Elementy playbook rozwiązania Ansible

[Elementy playbook rozwiązania Ansible](http://docs.ansible.com/ansible/latest/playbooks.html) rozwiązania Ansible w konfiguracji, wdrażania i aranżacji języka. Opisują one mają swoje systemów zdalnych, aby wymusić zasady lub zestaw kroków ogólnych procesów IT. Po utworzeniu elementu playbook możesz to zrobić za pomocą kodu YAML, który definiuje model konfiguracji lub procesu.

## <a name="ansible-modules"></a>Moduły rozwiązania Ansible

Rozwiązanie Ansible zawiera pakiet [modułów Ansible](http://docs.ansible.com/ansible/latest/modules_by_category.html) mogą być wykonywane bezpośrednio na hostach zdalnych lub za pośrednictwem [elementy playbook](http://docs.ansible.com/ansible/latest/playbooks.html). Użytkownicy mogą także tworzyć własne moduły. Moduły mogą służyć do kontrolowania zasobów systemowych — takich jak usługi, pakietów i pliki — lub wykonania poleceń systemu.

Do interakcji z usługami platformy Azure, Ansible zawiera pakiet [modułów do chmury rozwiązanie Ansible](http://docs.ansible.com/ansible/list_of_cloud_modules.html#azure) , udostępnia narzędzia umożliwiające łatwe tworzenie i organizowanie infrastruktury na platformie Azure. 

## <a name="migrate-existing-workload-to-azure"></a>Migrowanie istniejących obciążeń na platformę Azure

Po rozwiązaniu Ansible używanych do definiowania infrastruktury, można zastosować elementu playbook aplikacji, umożliwiając Azure automatycznego skalowania środowiska zgodnie z potrzebami. 

## <a name="automate-cloud-native-application-in-azure"></a>Automatyzacja aplikacji natywnych dla chmury na platformie Azure

Rozwiązanie Ansible umożliwia automatyzację aplikacjom natywnym dla chmury na platformie Azure przy użyciu mikrousług platformy Azure, takich jak [usługi Azure Functions](https://azure.microsoft.com//services/functions/) i [Kubernetes na platformie Azure](https://azure.microsoft.com/services/container-service/kubernetes/)).  

## <a name="manage-deployments-with-dynamic-inventory"></a>Zarządzanie wdrożeniami przy użyciu magazynu dynamicznego
Za pomocą jego [dynamiczny spis](http://docs.ansible.com/ansible/intro_dynamic_inventory.html) funkcji rozwiązania Ansible zapewnia możliwość ściągania spisu z zasobów platformy Azure. Możesz następnie tagów istniejących wdrożeń platformy Azure i zarządzać tym wdrożeniom oznakowane za pomocą rozwiązania Ansible.

## <a name="additional-azure-marketplace-options"></a>Dodatkowe opcje w witrynie Azure Marketplace
[Ansible Tower](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.ansible-tower) obrazu portalu Azure Marketplace, by Red Hat ułatwia organizacjom skalowanie automatyzacji struktury informatycznej oraz zarządzanie złożonymi wdrożeniami w infrastrukturach fizycznych, wirtualnych i infrastruktury chmury. Ansible Tower obejmuje funkcje, które zapewniają dodatkowe poziomy widoczności, kontroli, bezpieczeństwa i wydajności niezbędne dla współczesnych przedsiębiorstw. Ansible Tower szyfruje poświadczenia, takie jak klucze platformy Azure i SSH, dzięki czemu możesz delegować zadania dla mniej doświadczonych pracowników, bez ryzyka ujawnienia poświadczeń.

## <a name="next-steps"></a>Kolejne kroki
- [Konfigurowanie rozwiązania Ansible](/azure/virtual-machines/linux/ansible-install-configure?toc=%2Fen-us%2Fazure%2Fansible%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Tworzenie maszyny wirtualnej z systemem Linux](/azure/virtual-machines/linux/ansible-create-vm?toc=%2Fen-us%2Fazure%2Fansible%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)