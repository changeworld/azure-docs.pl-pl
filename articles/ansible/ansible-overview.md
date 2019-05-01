---
title: Za pomocą platformy Azure za pomocą rozwiązania Ansible | Dokumentacja firmy Microsoft
description: Wprowadzenie do używania rozwiązania Ansible w celu automatyzowania aprowizowania chmury, zarządzania konfiguracją i wdrażania aplikacji.
keywords: ansible, azure, devops, overview, cloud provision, configuration management, application deployment, ansible modules, ansible playbooks
ms.topic: overview
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 54d66a0ac425a9a0a63c91317ead0e02ecf9452c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64726004"
---
# <a name="using-ansible-with-azure"></a>Używanie rozwiązania Ansible z platformą Azure

[Ansible](https://www.ansible.com) jest produktem typu open source, które automatyzuje aprowizowanie chmury, zarządzanie konfiguracją i wdrożenia aplikacji. Korzystając z rozwiązania Ansible, można aprowizować maszyny wirtualne, kontenery, sieć oraz infrastruktury chmury. Ponadto rozwiązania Ansible pozwala na zautomatyzowanie wdrażania i konfiguracji zasobów w danym środowisku.

Ten artykuł zawiera ogólne omówienie niektórych korzyści wynikających z używania rozwiązania Ansible z platformą Azure.

## <a name="ansible-playbooks"></a>Elementy playbook rozwiązania Ansible

[Elementy playbook rozwiązania Ansible](https://docs.ansible.com/ansible/latest/playbooks.html) pozwala na bezpośrednie Ansible, aby skonfigurować środowisko. Elementy playbook są kodowane za pomocą kodu YAML tak, aby były czytelne dla człowieka. Sekcja samouczków zawiera wiele przykładów przy użyciu elementów playbook, aby zainstalować i skonfigurować zasoby platformy Azure. 

## <a name="ansible-modules"></a>Moduły rozwiązania Ansible

Rozwiązanie Ansible zawiera pakiet [modułów Ansible](https://docs.ansible.com/ansible/latest/modules_by_category.html) są uruchamiane bezpośrednio na hostach zdalnych lub za pośrednictwem [elementy playbook](https://docs.ansible.com/ansible/latest/playbooks.html). Użytkownicy mogą tworzyć własne moduły. Moduły są używane do kontrolowania zasobów systemowych — takich jak usługi, pakietów i pliki — lub wykonania poleceń systemowych.

Do interakcji z usługami platformy Azure, Ansible zawiera pakiet [modułów do chmury rozwiązanie Ansible](https://docs.ansible.com/ansible/list_of_cloud_modules.html#azure). Te moduły umożliwiają tworzenie i organizowanie infrastruktury na platformie Azure. 

## <a name="migrate-existing-workload-to-azure"></a>Migrowanie istniejących obciążeń na platformę Azure

Po do definiowania infrastruktury za pomocą rozwiązania Ansible, można zastosować elementu playbook aplikacji, umożliwiając Azure automatycznego skalowania środowiska zgodnie z potrzebami. 

## <a name="automate-cloud-native-application-in-azure"></a>Automatyzacja aplikacji natywnych dla chmury na platformie Azure

Rozwiązanie Ansible umożliwia automatyzację aplikacji natywnych dla chmury na platformie Azure przy użyciu mikrousług platformy Azure, takich jak usługi [Azure Functions](https://azure.microsoft.com//services/functions/) i [Kubernetes na platformie Azure](https://azure.microsoft.com/services/container-service/kubernetes/).  

## <a name="manage-deployments-with-dynamic-inventory"></a>Zarządzanie wdrożeniami przy użyciu spisu dynamicznego

Rozwiązanie Ansible, za pośrednictwem funkcji [spisu dynamicznego](https://docs.ansible.com/ansible/intro_dynamic_inventory.html), zapewnia możliwość ściągania spisu z zasobów platformy Azure. Następnie można oznaczyć tagami swoje istniejące wdrożenia platformy Azure i zarządzać tymi otagowanymi wdrożeniami za pośrednictwem rozwiązania Ansible.

## <a name="additional-azure-marketplace-options"></a>Dodatkowe opcje w witrynie Azure Marketplace

[Ansible Tower](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.ansible-tower) jest obrazu witryny Azure Marketplace, by Red Hat. 

Ansible Tower jest oparte na sieci web interfejsu użytkownika, a pulpit nawigacyjny rozwiązania Ansible, który ma następujące cechy:

* Umożliwia zdefiniowanie kontroli dostępu opartej na rolach, planowanie zadań i Zarządzanie zapasami graficznego. 
* Obejmuje interfejs API REST i interfejsu wiersza polecenia, wieży mogą być wstawiane do istniejących narzędzi i procesów. 
* Obsługuje w czasie rzeczywistym dane wyjściowe uruchomienia elementów playbook. 
* Szyfruje poświadczenia — takie jak klucze platformy Azure i SSH -, dzięki czemu można delegować zadania bez ujawniania poświadczeń.

## <a name="ansible-module-and-version-matrix-for-azure"></a>Moduł rozwiązania Ansible i macierz wersji dla platformy Azure

Rozwiązanie Ansible zawiera zestaw modułów do użytku w aprowizacji i konfigurowania jej zasobów. Te zasoby obejmują maszyny wirtualne, zestawy skalowania, sieci, usługi i usługi kontenera. [Macierzy Ansible](./ansible-matrix.md) Wyświetla listę modułów Ansible dla platformy Azure i wersji rozwiązania Ansible, w których są dostarczane.

## <a name="next-steps"></a>Kolejne kroki

- [Szybki start: Wdrażanie szablonu rozwiązania Ansible na platformie Azure CentOS](./ansible-deploy-solution-template.md)
- [Szybki start: Konfigurowanie maszyn wirtualnych systemu Linux na platformie Azure, za pomocą rozwiązania Ansible](/azure/virtual-machines/linux/ansible-install-configure?toc=%2Fazure%2Fansible%2Ftoc.json&bc=%2Fazure%2Fbread%2Ftoc.json)