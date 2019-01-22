---
title: Używanie rozwiązania Ansible z platformą Azure
description: Wprowadzenie do używania rozwiązania Ansible w celu automatyzowania aprowizowania chmury, zarządzania konfiguracją i wdrażania aplikacji.
ms.service: ansible
keywords: ansible, azure, devops, overview, cloud provision, configuration management, application deployment, ansible modules, ansible playbooks
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 09/02/2018
ms.topic: overview
ms.openlocfilehash: 55dcda953454ce3eb4e19dabbf198f886d028180
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2019
ms.locfileid: "54357934"
---
# <a name="ansible-with-azure"></a>Rozwiązanie Ansible z platformą Azure

[Ansible](https://www.ansible.com) jest produktem typu open source, które automatyzuje aprowizowanie chmury, zarządzanie konfiguracją i wdrożenia aplikacji. Korzystając z rozwiązania Ansible, można aprowizować maszyny wirtualne, kontenery, sieć oraz infrastruktury chmury. Ponadto rozwiązanie Ansible umożliwia zautomatyzowanie wdrażania i konfigurowania zasobów w Twoim środowisku.

Ten artykuł zawiera ogólne omówienie niektórych korzyści wynikających z używania rozwiązania Ansible z platformą Azure.

## <a name="ansible-playbooks"></a>Elementy playbook rozwiązania Ansible

[Elementy playbook rozwiązania Ansible](https://docs.ansible.com/ansible/latest/playbooks.html) to język konfiguracji, wdrażania i orkiestracji rozwiązania Ansible. Mogą one opisywać zasady, które chcesz wymuszać za pomocą systemów zdalnych, lub zestaw kroków w ogólnym procesie informatycznym. Elementy playbook tworzy się przy użyciu języka YAML, który definiuje model konfiguracji lub procesu.

## <a name="ansible-modules"></a>Moduły rozwiązania Ansible

Rozwiązanie Ansible zawiera pakiet [modułów Ansible](https://docs.ansible.com/ansible/latest/modules_by_category.html), które można wykonywać bezpośrednio na hostach zdalnych lub za pośrednictwem [elementów playbook](https://docs.ansible.com/ansible/latest/playbooks.html). Użytkownicy mogą także tworzyć własne moduły. Moduły mogą służyć do kontrolowania zasobów systemowych — takich jak usługi, pakiety i pliki — lub do wykonywania poleceń systemowych.

Na potrzeby interakcji z usługami platformy Azure rozwiązanie Ansible udostępnia pakiet [modułów chmurowych rozwiązania Ansible](https://docs.ansible.com/ansible/list_of_cloud_modules.html#azure), który zawiera narzędzia umożliwiające łatwe tworzenie i orkiestrowanie infrastruktury na platformie Azure. 

## <a name="migrate-existing-workload-to-azure"></a>Migrowanie istniejących obciążeń na platformę Azure

Po zdefiniowaniu infrastruktury przy użyciu rozwiązania Ansible możesz zastosować element playbook aplikacji, umożliwiając platformie Azure automatyczne skalowanie środowiska zgodnie z potrzebami. 

## <a name="automate-cloud-native-application-in-azure"></a>Automatyzacja aplikacji natywnych dla chmury na platformie Azure

Rozwiązanie Ansible umożliwia automatyzację aplikacji natywnych dla chmury na platformie Azure przy użyciu mikrousług platformy Azure, takich jak usługi [Azure Functions](https://azure.microsoft.com//services/functions/) i [Kubernetes na platformie Azure](https://azure.microsoft.com/services/container-service/kubernetes/).  

## <a name="manage-deployments-with-dynamic-inventory"></a>Zarządzanie wdrożeniami przy użyciu spisu dynamicznego
Rozwiązanie Ansible, za pośrednictwem funkcji [spisu dynamicznego](https://docs.ansible.com/ansible/intro_dynamic_inventory.html), zapewnia możliwość ściągania spisu z zasobów platformy Azure. Następnie można oznaczyć tagami swoje istniejące wdrożenia platformy Azure i zarządzać tymi otagowanymi wdrożeniami za pośrednictwem rozwiązania Ansible.

## <a name="additional-azure-marketplace-options"></a>Dodatkowe opcje w witrynie Azure Marketplace
Obraz [Ansible Tower](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.ansible-tower) firmy Red Hat dostępny w witrynie Azure Marketplace ułatwia organizacjom skalowanie automatyzacji struktury informatycznej oraz zarządzanie złożonymi wdrożeniami w infrastrukturach fizycznych, wirtualnych i chmurowych. Rozwiązanie Ansible Tower obejmuje możliwości zapewniające dodatkowe poziomy widoczności, kontroli, zabezpieczeń i wydajności, niezbędne dla dzisiejszych przedsiębiorstw. Rozwiązanie Ansible Tower szyfruje poświadczenia, takie jak klucze platformy Azure i klucze SSH, co pozwala na delegowanie zadań do mniej doświadczonych pracowników bez ryzyka ujawnienia poświadczeń.

## <a name="ansible-module-and-version-matrix-for-azure"></a>Moduł rozwiązania Ansible i macierz wersji dla platformy Azure
Rozwiązanie Ansible jest dostarczane z kilkoma modułami, które można wykonywać bezpośrednio na hostach zdalnych lub za pośrednictwem elementów playbook.
Sekcja [Moduł rozwiązania Ansible i macierz wersji](./ansible-matrix.md) zawiera listę modułów rozwiązania Ansible dla platformy Azure, za pomocą których można aprowizować zasoby chmurowe platformy Azure, takie jak maszyny wirtualne, sieci i usługi kontenerów. 

## <a name="next-steps"></a>Następne kroki
- [Konfigurowanie rozwiązania Ansible](/azure/virtual-machines/linux/ansible-install-configure?toc=%2Fen-us%2Fazure%2Fansible%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Tworzenie maszyny wirtualnej z systemem Linux](/azure/virtual-machines/linux/ansible-create-vm?toc=%2Fen-us%2Fazure%2Fansible%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
