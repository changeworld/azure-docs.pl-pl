---
title: Moduł rozwiązania Ansible i macierz wersji dla platformy Azure
description: Moduł rozwiązania Ansible i macierz wersji dla platformy Azure
ms.service: ansible
keywords: rozwiązanie ansible, ról, macierz, wersji, azure, metodyki devops
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 09/22/2018
ms.topic: article
ms.openlocfilehash: 149f37d0231ecc0547e8dc7937d22a9cc38b7df3
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55810222"
---
# <a name="ansible-module-and-version-matrix"></a>Macierz modułu i wersji rozwiązania Ansible

## <a name="ansible-modules-for-azure"></a>Rozwiązanie Ansible moduły dla platformy Azure
Rozwiązanie Ansible jest dostarczane z kilkoma modułami, które można wykonywać bezpośrednio na hostach zdalnych lub za pośrednictwem elementów playbook.
W tym artykule wymieniono modułów Ansible na platformie Azure, które można udostępnić zasoby platformy Azure w chmurze, takie jak maszyny wirtualnej, sieci i usługi kontenerów. Możesz uzyskać te moduły z jej oficjalnym wydaniem Ansible lub następujące role elementu playbook opublikowane przez firmę Microsoft.

| Moduł Ansible na platformie Azure                   |  2.4 rozwiązania Ansible |  2.5 rozwiązania Ansible |  2.6 rozwiązania Ansible | Rozwiązanie Ansible w wersji 2.7 | Rozwiązanie Ansible roli | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|-------------------------------------| 
| **Obliczanie**                    |           |                          |                          |                            |                                | 
| azure_rm_availabilityset                    | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_availabilityset_facts              | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_deployment                         | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_resource                           | -            | -                           | Yes          | Yes          | Yes                                 | 
| azure_rm_resource_facts                     | -            | -                           | Yes          | Yes          | Yes                                 | 
| azure_rm_virtualmachine_scaleset_facts      | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_virtualmachineimage_facts          | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_resourcegroup                      | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_resourcegroup_facts                | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_virtualmachine                     | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_virtualmachine_facts               | -            | -                           | -            | Yes          | Yes                                 | 
| azure_rm_virtualmachine_extension           | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_virtualmachine_scaleset            | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_image                              |              | Yes                         | Yes          | Yes          | Yes                                 | 
| **Sieć**                    |           |                          |                          |                             |                               | 
| azure_rm_virtualnetwork                     | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_virtualnetwork_facts               | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_subnet                             | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_networkinterface                   | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_networkinterface_facts             | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_publicipaddress                    | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_publicipaddress_facts              | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_dnsrecordset                       | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_dnsrecordset_facts                 | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_dnszone                            | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_dnszone_facts                      | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_loadbalancer                       | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_loadbalancer_facts                 | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_appgateway                         | -            | -                           | -            | Yes          | Yes                                 | 
| azure_rm_appgwroute                         | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_appgwroute                         | -            | -                           | -            | -            | Yes                                 |
| azure_rm_appgwroute_facts                   | -            | -                           | -            | -            | Yes                                 |
| azure_rm_appgwroutetable                    | -            | -                           | -            | -            | Yes                                 |
| azure_rm_appgwroutetable_facts              | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_securitygroup                      | Yes          | Yes                         | Yes          | Yes          | Yes                                 |
| azure_rm_route                              | -            | -                           | -            | Yes          | Yes                                 | 
| azure_rm_routetable                         | -            | -                           | -            | Yes          | Yes                                 | 
| azure_rm_routetable_facts                   | -            | -                           | -            | Yes          | Yes                                 | 
| **Storage**                    |           |                          |                          |                             |                               | 
| azure_rm_storageaccount                     | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_storageaccount_facts               | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_storageblob                        | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_managed_disk                       | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_managed_disk_facts                 | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| **Containers**                    |           |                          |                          |                            |                                | 
| azure_rm_aks                                | -            | -                           | Yes          | Yes          | Yes                                 | 
| azure_rm_aks_facts                          | -            | -                           | Yes          | Yes          | Yes                                 | 
| azure_rm_acs                                | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_containerinstance                  | -            | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_containerinstance_facts            | -            | -                           | -              | -            | Yes                                 | 
| azure_rm_containerregistry                  | -            | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_containerregistry_facts            | -            | -                           | -            | Yes          | Yes                                 | 
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_containerregistryreplication_facts | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_containerregistrywebhook_facts     | -            | -                           | -            | -            | Yes                                 | 
| **Azure Functions**                    |           |                          |                          |                            |                                | 
| azure_rm_functionapp                        | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_functionapp_facts                  | Yes          | Yes                         | Yes          | Yes          | Yes                                 | 
| **Bazy danych**                    |           |                          |                          |                             |                               | 
| azure_rm_sqlserver                          | -            | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_sqlserver_facts                    | -            | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_sqldatabase                        | -            | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_sqldatabase_facts                  | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_sqlelasticpool                     | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_sqlelasticpool_facts               | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | Yes          | Yes                                 | 
| azure_rm_sqlfirewallrule_facts              | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_mysqlserver                        | -            | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_mysqlserver_facts                  | -            | -                           | -            | Yes          | Yes                                 | 
| azure_rm_mysqldatabase                      | -            | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_mysqldatabase_facts                | -            | -                           | -            | Yes          | Yes                                 | 
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_mysqlconfiguration_facts           | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_postgresqlserver                   | -            | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_postgresqlserver_facts             | -            | -                           | -            | Yes          | Yes                                 | 
| azure_rm_postgresqldatabase                 | -            | Yes                         | Yes          | Yes          | Yes                                 | 
| azure_rm_postgresqldatabase_facts           | -            | -                           | -            | Yes          | Yes                                 | 
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | Yes                                 | 
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | -            | -            | Yes                                 | 
| **Usługa Key Vault**                    |           |                          |                          |                             |                               | 
| azure_rm_keyvault                           | -            | Yes                         | Yes          | Yes          | Yes                                 |
| azure_rm_keyvault_facts                     | -            | -                           | -              | -              | Yes                               |
| azure_rm_keyvaultkey                        | -            | Yes                         | Yes          | Yes          | Yes                                 |
| azure_rm_keyvaultsecret                     | -            | Yes                         | Yes          | Yes          | Yes                                 |
| **Web Apps**                    |           |                          |                          |                             |                               | 
| azure_rm_appserviceplan                          | -            | -                         | -          | Yes          | Yes                                 | 
| azure_rm_appserviceplan_facts                    | -            | -                         | -          | Yes          | Yes                                 | 
| azure_rm_webapp                                  | -            | -                         | -          | Yes          | Yes                                 | 
| azure_rm_webapp_facts                            | -            | -                         | -          | Yes          | Yes                                 | 
| **Traffic Manager**                    |           |                          |                          |                             |                               | 
| azure_rm_trafficmanagerendpoint                  | -            | -                         | -          | Yes          | Yes                                 | 
| azure_rm_trafficmanagerendpoint_facts            | -            | -                         | -          | Yes          | Yes                                 | 
| azure_rm_trafficmanagerprofile                   | -            | -                         | -          | Yes          | Yes                                 | 
| azure_rm_trafficmanagerprofile_facts             | -            | -                         | -          | Yes          | Yes                                 | 
| **Automatyczne skalowanie**                    |           |                          |                          |                             |                               | 
| azure_rm_autoscale                  | -            | -                         | -          | Yes          | Yes                                 | 
| azure_rm_autoscale_facts            | -            | -                         | -          | Yes          | Yes                                 | 

## <a name="introduction-to-playbook-role-for-azure"></a>Wprowadzenie do podręcznika dotyczącego roli na platformie Azure
[Azure_preview_module podręcznikiem rola](https://galaxy.ansible.com/Azure/azure_preview_modules/) to najbardziej kompleksowe rola i zawiera wszystkich najnowszych modułów platformy Azure. Aktualizacje i poprawki błędów są wykonywane w sposób terminowy więcej niż oficjalnym wydaniem Ansible. Jeśli dla celów aprowizowania zasobów platformy Azure za pomocą rozwiązania Ansible, jesteś zaleca się instalowanie roli elementu playbook azure_preview_module.

Rola elementu playbook azure_preview_module jest publikowany co trzy tygodnie.

## <a name="next-steps"></a>Kolejne kroki
Więcej informacji związanych z role elementu playbook [tworzenie elementów Playbook wielokrotnego użytku, do](https://docs.ansible.com/ansible/latest/playbooks_reuse.html). 
