---
title: Używanie rozwiązania Ansible z platformą Azure
description: Wprowadzenie do używania rozwiązania Ansible w celu automatyzowania aprowizowania chmury, zarządzania konfiguracją i wdrażania aplikacji.
keywords: ansible, azure, devops, overview, cloud provision, configuration management, application deployment, ansible modules, ansible playbooks
ms.topic: overview
ms.date: 04/30/2019
ms.openlocfilehash: e9d5b8858f052083b157c7d4809fe21018518bcd
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77193569"
---
# <a name="using-ansible-with-azure"></a>Używanie rozwiązania Ansible z platformą Azure

[Ansible](https://www.ansible.com) jest produktem typu open source, które automatyzuje aprowizowanie chmury, zarządzanie konfiguracją i wdrożenia aplikacji. Korzystając z rozwiązania Ansible, można aprowizować maszyny wirtualne, kontenery, sieć oraz infrastruktury chmury. Ponadto ansible umożliwia automatyzację wdrażania i konfiguracji zasobów w środowisku.

Ten artykuł zawiera ogólne omówienie niektórych korzyści wynikających z używania rozwiązania Ansible z platformą Azure.

## <a name="ansible-playbooks"></a>Elementy playbook rozwiązania Ansible

[Czytelne podręczniki](https://docs.ansible.com/ansible/latest/playbooks.html) umożliwiają kierowanie programu Ansible w celu skonfigurowania środowiska. Podręczniki są kodowane przy użyciu YAML, tak aby były czytelne dla człowieka. W sekcji Samouczki przedstawiono wiele przykładów instalowania i konfigurowania zasobów platformy Azure za pomocą podręczników. 

## <a name="ansible-modules"></a>Moduły rozwiązania Ansible

Ansible zawiera zestaw [modułów Ansible,](https://docs.ansible.com/ansible/latest/modules_by_category.html) które są uruchamiane bezpośrednio na zdalnych hostach lub za pośrednictwem [podręczników.](https://docs.ansible.com/ansible/latest/playbooks.html) Użytkownicy mogą tworzyć własne moduły. Moduły są używane do kontrolowania zasobów systemowych — takich jak usługi, pakiety lub pliki — lub wykonywania poleceń systemowych.

Do interakcji z usługami platformy Azure Ansible zawiera zestaw [modułów chmury Ansible.](https://docs.ansible.com/ansible/list_of_cloud_modules.html#azure) Te moduły umożliwiają tworzenie i organizowanie infrastruktury na platformie Azure. 

## <a name="migrate-existing-workload-to-azure"></a>Migrowanie istniejących obciążeń na platformę Azure

Po użyciu Ansible do definiowania infrastruktury, można zastosować podręcznik aplikacji, dzięki czemu platforma Azure automatycznie skalować środowisko w razie potrzeby. 

## <a name="automate-cloud-native-application-in-azure"></a>Automatyzacja aplikacji natywnych dla chmury na platformie Azure

Rozwiązanie Ansible umożliwia automatyzację aplikacji natywnych dla chmury na platformie Azure przy użyciu mikrousług platformy Azure, takich jak usługi [Azure Functions](https://azure.microsoft.com//services/functions/) i [Kubernetes na platformie Azure](https://azure.microsoft.com/services/container-service/kubernetes/).  

## <a name="manage-deployments-with-dynamic-inventory"></a>Zarządzanie wdrożeniami przy użyciu spisu dynamicznego

Rozwiązanie Ansible, za pośrednictwem funkcji [spisu dynamicznego](https://docs.ansible.com/ansible/intro_dynamic_inventory.html), zapewnia możliwość ściągania spisu z zasobów platformy Azure. Następnie można oznaczyć tagami swoje istniejące wdrożenia platformy Azure i zarządzać tymi otagowanymi wdrożeniami za pośrednictwem rozwiązania Ansible.

## <a name="additional-azure-marketplace-options"></a>Dodatkowe opcje w witrynie Azure Marketplace

[Ansible Tower](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.ansible-tower) to obraz portalu Azure Marketplace autorstwa Red Hat. 

Ansible Tower to internetowy interfejs użytkownika i pulpit nawigacyjny dla programu Ansible, który ma następujące funkcje:

* Umożliwia definiowanie kontroli dostępu opartej na rolach, planowania zadań i graficznego zarządzania zapasami. 
* Zawiera interfejs API REST i interfejs wiersza polecenia, dzięki czemu można wstawić wieżę do istniejących narzędzi i procesów. 
* Obsługuje wydruki w czasie rzeczywistym. 
* Szyfruje poświadczenia — takie jak azure i klucze SSH — dzięki czemu można delegować zadania bez ujawniania poświadczeń.

## <a name="ansible-module-and-version-matrix-for-azure"></a>Moduł rozwiązania Ansible i macierz wersji dla platformy Azure

Ansible zawiera zestaw modułów do użycia w inicjowaniu obsługi administracyjnej i konfigurowania zasobów platformy Azure. Zasoby te obejmują maszyny wirtualne, zestawy skalowania, usługi sieciowe i usługi kontenerów. [Macierz Ansible](./ansible-matrix.md) zawiera listę modułów Ansible dla platformy Azure i wersji Ansible, w których są dostarczane.

## <a name="next-steps"></a>Następne kroki

- [Szybki start: wdrażanie szablonu rozwiązania Ansible dla platformy Azure w centos](./ansible-deploy-solution-template.md)
- [Szybki start: konfigurowanie maszyn wirtualnych systemu Linux na platformie Azure przy użyciu aplikacji Ansible](./ansible-install-configure.md)