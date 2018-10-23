---
title: Macierz modułu i wersji rozwiązania Ansible na platformie Azure
description: Macierz modułu i wersji rozwiązania Ansible na platformie Azure
ms.service: ansible
keywords: rozwiązanie ansible, ról, macierz, wersji, azure, metodyki devops
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 09/22/2018
ms.topic: article
ms.openlocfilehash: a71f28413028efe3547df7c464ec852fe2161f63
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2018
ms.locfileid: "49649945"
---
# <a name="ansible-module-and-version-matrix"></a>Macierz modułu i wersji rozwiązania Ansible

## <a name="ansible-modules-for-azure"></a>Rozwiązanie Ansible moduły dla platformy Azure
Ansible jest dostarczany z modułów, które mogą być wykonywane bezpośrednio na hostach zdalnych lub za pomocą elementów playbook.
W tym artykule wymieniono modułów Ansible na platformie Azure, które można udostępnić zasoby platformy Azure w chmurze, takie jak maszyny wirtualnej, sieci i usługi kontenerów. Możesz uzyskać te moduły z jej oficjalnym wydaniem Ansible lub następujące role elementu playbook opublikowane przez firmę Microsoft.

| Moduł Ansible na platformie Azure                   |  2.4 rozwiązania Ansible |  2.5 rozwiązania Ansible |  2.6 rozwiązania Ansible | Rozwiązanie Ansible w wersji 2.7 | [Rozwiązanie Ansible roli](#introduction-to-azurepreviewmodule) | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|-------------------------------------| 
| **Obliczanie**                    |           |                          |                          |                            |                                | 
| azure_rm_availabilityset                    | Tak          | Yes                         | Yes          | Yes          | Tak                                 | 
| azure_rm_availabilityset_facts              | Tak          | Yes                         | Yes          | Yes          | Tak                                 | 
| azure_rm_deployment                         | Tak          | Yes                         | Yes          | Yes          | Tak                                 | 
| azure_rm_resource                           | -            | -                           | Tak          | Yes          | Tak                                 | 
| azure_rm_resource_facts                     | -            | -                           | Tak          | Yes          | Tak                                 | 
| azure_rm_virtualmachine_scaleset_facts      | Tak          | Yes                         | Yes          | Yes          | Tak                                 | 
| azure_rm_virtualmachineimage_facts          | Tak          | Yes                         | Yes          | Yes          | Tak                                 | 
| azure_rm_resourcegroup                      | Tak          | Yes                         | Yes          | Yes          | Tak                                 | 
| azure_rm_resourcegroup_facts                | Tak          | Yes                         | Yes          | Yes          | Tak                                 | 
| azure_rm_virtualmachine                     | Tak          | Yes                         | Yes          | Yes          | Tak                                 | 
| azure_rm_virtualmachine_facts               | -            | -                           | -            | Tak          | Tak                                 | 
| azure_rm_virtualmachine_extension           | Tak          | Yes                         | Yes          | Yes          | Tak                                 | 
| azure_rm_virtualmachine_scaleset            | Tak          | Yes                         | Yes          | Yes          | Tak                                 | 
| azure_rm_image                              |              | Tak                         | Yes          | Yes          | Tak                                 | 
| **Sieć**                    |           |                          |                          |                             |                               | 
| azure_rm_virtualnetwork                     | Tak          | Yes                         | Yes          | Yes          | Tak                                 | 
| azure_rm_virtualnetwork_facts               | Tak          | Yes                         | Yes          | Yes          | Tak                                 | 
| azure_rm_subnet                             | Tak          | Yes                         | Yes          | Yes          | Tak                                 | 
| azure_rm_networkinterface                   | Tak          | Yes                         | Yes          | Yes          | Tak                                 | 
| azure_rm_networkinterface_facts             | Tak          | Yes                         | Yes          | Yes          | Tak                                 | 
| azure_rm_publicipaddress                    | Tak          | Yes                         | Yes          | Yes          | Tak                                 | 
| azure_rm_publicipaddress_facts              | Tak          | Yes                         | Yes          | Yes          | Tak                                 | 
| azure_rm_dnsrecordset                       | Tak          | Yes                         | Yes          | Yes          | Tak                                 | 
| azure_rm_dnsrecordset_facts                 | Tak          | Yes                         | Yes          | Yes          | Tak                                 | 
| azure_rm_dnszone                            | Tak          | Yes                         | Yes          | Yes          | Tak                                 | 
| azure_rm_dnszone_facts                      | Tak          | Yes                         | Yes          | Yes          | Tak                                 | 
| azure_rm_loadbalancer                       | Tak          | Yes                         | Yes          | Yes          | Tak                                 | 
| azure_rm_loadbalancer_facts                 | Tak          | Yes                         | Yes          | Yes          | Tak                                 | 
| azure_rm_appgateway                         | -            | -                           | -            | Tak          | Tak                                 | 
| azure_rm_appgwroute                         | -            | -                           | -            | -            | Tak                                 | 
| azure_rm_appgwroute                         | -            | -                           | -            | -            | Tak                                 |
| azure_rm_appgwroute_facts                   | -            | -                           | -            | -            | Tak                                 |
| azure_rm_appgwroutetable                    | -            | -                           | -            | -            | Tak                                 |
| azure_rm_appgwroutetable_facts              | -            | -                           | -            | -            | Tak                                 | 
| azure_rm_securitygroup                      | Tak          | Yes                         | Yes          | Yes          | Tak                                 |
| azure_rm_route                              | -            | -                           | -            | Tak          | Tak                                 | 
| azure_rm_routetable                         | -            | -                           | -            | Tak          | Tak                                 | 
| azure_rm_routetable_facts                   | -            | -                           | -            | Tak          | Tak                                 | 
| **Storage**                    |           |                          |                          |                             |                               | 
| azure_rm_storageaccount                     | Tak          | Yes                         | Yes          | Yes          | Tak                                 | 
| azure_rm_storageaccount_facts               | Tak          | Yes                         | Yes          | Yes          | Tak                                 | 
| azure_rm_storageblob                        | Tak          | Yes                         | Yes          | Yes          | Tak                                 | 
| azure_rm_managed_disk                       | Tak          | Yes                         | Yes          | Yes          | Tak                                 | 
| azure_rm_managed_disk_facts                 | Tak          | Yes                         | Yes          | Yes          | Tak                                 | 
| **Containers**                    |           |                          |                          |                            |                                | 
| azure_rm_aks                                | -            | -                           | Tak          | Yes          | Tak                                 | 
| azure_rm_aks_facts                          | -            | -                           | Tak          | Yes          | Tak                                 | 
| azure_rm_acs                                | Tak          | Yes                         | Yes          | Yes          | Tak                                 | 
| azure_rm_containerinstance                  | -            | Tak                         | Yes          | Yes          | Tak                                 | 
| azure_rm_containerinstance_facts            | -            | -                           | -              | -            | Tak                                 | 
| azure_rm_containerregistry                  | -            | Tak                         | Yes          | Yes          | Tak                                 | 
| azure_rm_containerregistry_facts            | -            | -                           | -            | Tak          | Tak                                 | 
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | Tak                                 | 
| azure_rm_containerregistryreplication_facts | -            | -                           | -            | -            | Tak                                 | 
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | Tak                                 | 
| azure_rm_containerregistrywebhook_facts     | -            | -                           | -            | -            | Tak                                 | 
| **Azure Functions**                    |           |                          |                          |                            |                                | 
| azure_rm_functionapp                        | Tak          | Yes                         | Yes          | Yes          | Tak                                 | 
| azure_rm_functionapp_facts                  | Tak          | Yes                         | Yes          | Yes          | Tak                                 | 
| **Bazy danych**                    |           |                          |                          |                             |                               | 
| azure_rm_sqlserver                          | -            | Tak                         | Yes          | Yes          | Tak                                 | 
| azure_rm_sqlserver_facts                    | -            | Tak                         | Yes          | Yes          | Tak                                 | 
| azure_rm_sqldatabase                        | -            | Tak                         | Yes          | Yes          | Tak                                 | 
| azure_rm_sqldatabase_facts                  | -            | -                           | -            | -            | Tak                                 | 
| azure_rm_sqlelasticpool                     | -            | -                           | -            | -            | Tak                                 | 
| azure_rm_sqlelasticpool_facts               | -            | -                           | -            | -            | Tak                                 | 
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | Tak          | Tak                                 | 
| azure_rm_sqlfirewallrule_facts              | -            | -                           | -            | -            | Tak                                 | 
| azure_rm_mysqlserver                        | -            | Tak                         | Yes          | Yes          | Tak                                 | 
| azure_rm_mysqlserver_facts                  | -            | -                           | -            | Tak          | Tak                                 | 
| azure_rm_mysqldatabase                      | -            | Tak                         | Yes          | Yes          | Tak                                 | 
| azure_rm_mysqldatabase_facts                | -            | -                           | -            | Tak          | Tak                                 | 
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | Tak                                 | 
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | -            | -            | Tak                                 | 
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | Tak                                 | 
| azure_rm_mysqlconfiguration_facts           | -            | -                           | -            | -            | Tak                                 | 
| azure_rm_postgresqlserver                   | -            | Tak                         | Yes          | Yes          | Tak                                 | 
| azure_rm_postgresqlserver_facts             | -            | -                           | -            | Tak          | Tak                                 | 
| azure_rm_postgresqldatabase                 | -            | Tak                         | Yes          | Yes          | Tak                                 | 
| azure_rm_postgresqldatabase_facts           | -            | -                           | -            | Tak          | Tak                                 | 
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | Tak                                 | 
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | -            | -            | Tak                                 | 
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | Tak                                 | 
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | -            | -            | Tak                                 | 
| **Usługa Key Vault**                    |           |                          |                          |                             |                               | 
| azure_rm_keyvault                           | -            | Tak                         | Yes          | Yes          | Tak                                 |
| azure_rm_keyvault_facts                     | -            | -                           | -              | -              | Tak                               |
| azure_rm_keyvaultkey                        | -            | Tak                         | Yes          | Yes          | Tak                                 |
| azure_rm_keyvaultsecret                     | -            | Tak                         | Yes          | Yes          | Tak                                 |
| **Web Apps**                    |           |                          |                          |                             |                               | 
| azure_rm_appserviceplan                          | -            | -                         | -          | Tak          | Tak                                 | 
| azure_rm_appserviceplan_facts                    | -            | -                         | -          | Tak          | Tak                                 | 
| azure_rm_webapp                                  | -            | -                         | -          | Tak          | Tak                                 | 
| azure_rm_webapp_facts                            | -            | -                         | -          | Tak          | Tak                                 | 
| **Traffic Manager**                    |           |                          |                          |                             |                               | 
| azure_rm_trafficmanagerendpoint                  | -            | -                         | -          | Tak          | Tak                                 | 
| azure_rm_trafficmanagerendpoint_facts            | -            | -                         | -          | Tak          | Tak                                 | 
| azure_rm_trafficmanagerprofile                   | -            | -                         | -          | Tak          | Tak                                 | 
| azure_rm_trafficmanagerprofile_facts             | -            | -                         | -          | Tak          | Tak                                 | 
| **Automatyczne skalowanie**                    |           |                          |                          |                             |                               | 
| azure_rm_autoscale                  | -            | -                         | -          | Tak          | Tak                                 | 
| azure_rm_autoscale_facts            | -            | -                         | -          | Tak          | Tak                                 | 

## <a name="introduction-to-playbook-role-for-azure"></a>Wprowadzenie do podręcznika dotyczącego roli na platformie Azure
[Azure_preview_module podręcznikiem rola](https://galaxy.ansible.com/Azure/azure_preview_modules/) to najbardziej kompleksowe rola i zawiera wszystkich najnowszych modułów platformy Azure. Aktualizacje i poprawki błędów są wykonywane w sposób terminowy więcej niż oficjalnym wydaniem Ansible. Jeśli dla celów aprowizowania zasobów platformy Azure za pomocą rozwiązania Ansible, jesteś zaleca się instalowanie roli elementu playbook azure_preview_module.

Rola elementu playbook azure_preview_module jest publikowany co trzy tygodnie.

## <a name="next-steps"></a>Kolejne kroki
Więcej informacji związanych z role elementu playbook [tworzenie elementów Playbook wielokrotnego użytku, do](http://docs.ansible.com/ansible/latest/playbooks_reuse.html). 
