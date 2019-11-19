---
title: Używanie rozwiązania Ansible z platformą Azure
description: Wprowadzenie do używania rozwiązania Ansible w celu automatyzowania aprowizowania chmury, zarządzania konfiguracją i wdrażania aplikacji.
keywords: ansible, azure, devops, overview, cloud provision, configuration management, application deployment, ansible modules, ansible playbooks
ms.topic: overview
ms.date: 04/30/2019
ms.openlocfilehash: eab2a44257c6769106124c4c9ab6711ba3be531e
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74155782"
---
# <a name="using-ansible-with-azure"></a>Używanie rozwiązania Ansible z platformą Azure

[Ansible](https://www.ansible.com) jest produktem typu open source, które automatyzuje aprowizowanie chmury, zarządzanie konfiguracją i wdrożenia aplikacji. Korzystając z rozwiązania Ansible, można aprowizować maszyny wirtualne, kontenery, sieć oraz infrastruktury chmury. Ponadto rozwiązania ansible umożliwia automatyzację wdrażania i konfigurowania zasobów w środowisku.

Ten artykuł zawiera ogólne omówienie niektórych korzyści wynikających z używania rozwiązania Ansible z platformą Azure.

## <a name="ansible-playbooks"></a>Elementy playbook rozwiązania Ansible

[Rozwiązania ansible elementy PlayBook](https://docs.ansible.com/ansible/latest/playbooks.html) umożliwia bezpośrednią konfigurację środowiska. Elementy PlayBook są kodowane przy użyciu YAML, aby można było je odczytać przez człowieka. Sekcja samouczków zawiera wiele przykładów korzystania z elementy PlayBook w celu instalowania i konfigurowania zasobów platformy Azure. 

## <a name="ansible-modules"></a>Moduły rozwiązania Ansible

Rozwiązania ansible zawiera zestaw [modułów rozwiązania ansible](https://docs.ansible.com/ansible/latest/modules_by_category.html) , które są uruchamiane bezpośrednio na hostach zdalnych lub za pośrednictwem [elementy PlayBook](https://docs.ansible.com/ansible/latest/playbooks.html). Użytkownicy mogą tworzyć własne moduły. Moduły służą do kontrolowania zasobów systemowych, takich jak usługi, pakiety lub pliki, lub wykonywanie poleceń systemu.

W przypadku korzystania z usług platformy Azure rozwiązania ansible obejmuje pakiet [modułów chmurowych rozwiązania ansible](https://docs.ansible.com/ansible/list_of_cloud_modules.html#azure). Te moduły umożliwiają tworzenie i organizowanie infrastruktury na platformie Azure. 

## <a name="migrate-existing-workload-to-azure"></a>Migrowanie istniejących obciążeń na platformę Azure

Po użyciu rozwiązania ansible do zdefiniowania infrastruktury możesz zastosować element PlayBook aplikacji, dzięki czemu platforma Azure automatycznie skaluje swoje środowisko w miarę potrzeb. 

## <a name="automate-cloud-native-application-in-azure"></a>Automatyzacja aplikacji natywnych dla chmury na platformie Azure

Rozwiązanie Ansible umożliwia automatyzację aplikacji natywnych dla chmury na platformie Azure przy użyciu mikrousług platformy Azure, takich jak usługi [Azure Functions](https://azure.microsoft.com//services/functions/) i [Kubernetes na platformie Azure](https://azure.microsoft.com/services/container-service/kubernetes/).  

## <a name="manage-deployments-with-dynamic-inventory"></a>Zarządzanie wdrożeniami przy użyciu spisu dynamicznego

Rozwiązanie Ansible, za pośrednictwem funkcji [spisu dynamicznego](https://docs.ansible.com/ansible/intro_dynamic_inventory.html), zapewnia możliwość ściągania spisu z zasobów platformy Azure. Następnie można oznaczyć tagami swoje istniejące wdrożenia platformy Azure i zarządzać tymi otagowanymi wdrożeniami za pośrednictwem rozwiązania Ansible.

## <a name="additional-azure-marketplace-options"></a>Dodatkowe opcje w witrynie Azure Marketplace

[Rozwiązania ansible Tower](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.ansible-tower) to obraz portalu Azure Marketplace, który Red Hat. 

Rozwiązania ansible Tower to oparty na sieci Web interfejs użytkownika i pulpit nawigacyjny dla rozwiązania ansible, który ma następujące funkcje:

* Umożliwia zdefiniowanie kontroli dostępu opartej na rolach, planowania zadań i zarządzania zapasami graficznymi. 
* Obejmuje interfejs API REST i interfejsu wiersza polecenia, dzięki czemu można wstawiać wieża do istniejących narzędzi i procesów. 
* Obsługuje dane wyjściowe przebiegów element PlayBook w czasie rzeczywistym. 
* Szyfruje poświadczenia — takie jak Azure i klucze SSH — umożliwia delegowanie zadań bez ujawniania poświadczeń.

## <a name="ansible-module-and-version-matrix-for-azure"></a>Moduł rozwiązania Ansible i macierz wersji dla platformy Azure

Rozwiązania ansible zawiera zestaw modułów do użycia podczas aprowizacji i konfigurowania zasobów platformy Azure. Do tych zasobów należą maszyny wirtualne, zestawy skalowania, usługi sieciowe i usługi kontenera. [Macierz rozwiązania ansible](./ansible-matrix.md) zawiera moduły rozwiązania ansible dla platformy Azure i wersje rozwiązania ansible, w których są dostarczane.

## <a name="next-steps"></a>Następne kroki

- [Szybki Start: Wdrażanie szablonu rozwiązania rozwiązania ansible dla platformy Azure w usłudze CentOS](./ansible-deploy-solution-template.md)
- [Szybki Start: Konfigurowanie maszyn wirtualnych z systemem Linux na platformie Azure przy użyciu rozwiązania ansible](/azure/virtual-machines/linux/ansible-install-configure?toc=%2Fazure%2Fansible%2Ftoc.json&bc=%2Fazure%2Fbread%2Ftoc.json)