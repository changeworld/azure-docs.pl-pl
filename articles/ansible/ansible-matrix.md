---
title: Macierz modułu i wersji rozwiązania Ansible na platformie Azure | Dokumentacja firmy Microsoft
description: Moduł rozwiązania Ansible i macierz wersji dla platformy Azure
keywords: rozwiązanie ansible, ról, macierz, wersji, azure, metodyki devops
ms.topic: reference
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 3f5bda36368ee1fe1f37527422c6072c3ffda177
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763331"
---
# <a name="ansible-module-and-version-matrix"></a>Macierz modułu i wersji rozwiązania Ansible

Rozwiązanie Ansible zawiera zestaw modułów do użytku w aprowizacji i konfigurowania jej zasobów. Te zasoby obejmują maszyny wirtualne, zestawy skalowania, sieci, usługi i usługi kontenera. W tym artykule wymieniono różne moduły Ansible dla platformy Azure i wersji rozwiązania Ansible, w których są dostarczane.

## <a name="ansible-modules-for-azure"></a>Rozwiązanie Ansible moduły dla platformy Azure

Następujące moduły mogą być wykonywane bezpośrednio na hostach zdalnych lub za pomocą elementów playbook.

Te moduły są dostępne z rozwiązania Ansible oficjalnym wydaniem i z następujące role elementu playbook firmy Microsoft.

| Moduł Ansible na platformie Azure                   |  2.4 rozwiązania Ansible |  2.5 rozwiązania Ansible |  2.6 rozwiązania Ansible | Rozwiązanie Ansible w wersji 2.7 | 2.8 rozwiązania Ansible | Rozwiązanie Ansible roli | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|--------------|--------------| 
| **Obliczanie**                    |           |                          |                          |                            |           |           |
| azure_rm_availabilityset                    | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_availabilityset_facts              | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_deployment                         | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_deployment_facts                   | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_functionapp                        | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_functionapp_facts                  | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_image                              | -            | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_image_facts                        | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_resource                           | -            | -                           | Yes          | Yes          | Yes          | Yes          |
| azure_rm_resource_facts                     | -            | -                           | Yes          | Yes          | Yes          | Yes          |
| azure_rm_resourcegroup                      | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_resourcegroup_facts                | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_virtualmachine                     | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_virtualmachine_facts               | -            | -                           | -            | Yes          | Yes          | Yes          |
| azure_rm_virtualmachineextension           | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_virtualmachineextension_facts      | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_virtualmachineimage_facts          | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_virtualmachinescaleset            | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_virtualmachinescaleset_facts      | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_virtualmachinescalesetextension    | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_virtualmachinescalesetextension_facts | -            | -                        | -            | -            | Yes          | Yes          |
| azure_rm_virtualmachinescalesetinstance     | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_virtualmachinescalesetinstance_facts | -            | -                         | -            | -            | Yes          | Yes          |
| **Sieć**                              |              |                             |              |              |              |              |
| azure_rm_appgateway                         | -            | -                           | -            | Yes          | Yes          | Yes          |
| azure_rm_appgwroute                         | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_appgwroute                         | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_appgwroute_facts                   | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_appgwroutetable                    | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_appgwroutetable_facts              | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_applicationsecuritygroup           | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_applicationsecuritygroup_facts     | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_cdnendpoint                        | -            | -                         | -          | -            | Yes          | Yes          |
| azure_rm_cdnendpoint_facts                  | -            | -                         | -          | -            | Yes          | Yes          |
| azure_rm_cdnprofile                         | -            | -                         | -          | -            | Yes          | Yes          |
| azure_rm_cdnprofile_facts                   | -            | -                         | -          | -            | Yes          | Yes          |
| azure_rm_dnsrecordset                       | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_dnsrecordset_facts                 | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_dnszone                            | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_dnszone_facts                      | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_loadbalancer                       | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_loadbalancer_facts                 | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_networkinterface                   | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_networkinterface_facts             | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_publicipaddress                    | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_publicipaddress_facts              | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_route                              | -            | -                           | -            | Yes          | Yes          | Yes          |
| azure_rm_routetable                         | -            | -                           | -            | Yes          | Yes          | Yes          |
| azure_rm_routetable_facts                   | -            | -                           | -            | Yes          | Yes          | Yes          |
| azure_rm_securitygroup                      | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_subnet                             | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_subnet_facts                       | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_trafficmanagerendpoint             | -            | -                         | -          | Yes          | Yes          | Yes          |
| azure_rm_trafficmanagerendpoint_facts       | -            | -                         | -          | Yes          | Yes          | Yes          |
| azure_rm_trafficmanagerprofile              | -            | -                         | -          | Yes          | Yes          | Yes          |
| azure_rm_trafficmanagerprofile_facts        | -            | -                         | -          | Yes          | Yes          | Yes          |
| azure_rm_virtualnetwork                     | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_virtualnetwork_facts               | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_virtualnetworkpeering              | -            | -                         | -          | -            | Yes          | Yes          |
| **Storage**                    |           |                          |                          |                            |           |           |
| azure_rm_manageddisk                        | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_manageddisk_facts                  | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_storageaccount                     | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_storageaccount_facts               | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_storageblob                        | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          |
| **Sieć Web**                    |           |                          |                          |                             |           |           |
| azure_rm_appserviceplan                     | -            | -                         | -          | Yes          | Yes          | Yes          |
| azure_rm_appserviceplan_facts               | -            | -                         | -          | Yes          | Yes          | Yes          |
| azure_rm_webapp                             | -            | -                         | -          | Yes          | Yes          | Yes          |
| azure_rm_webapp_facts                       | -            | -                         | -          | Yes          | Yes          | Yes          |
| azure_rm_webappslot                         | -            | -                         | -          | -            | Yes          | Yes          |
| **Containers**                    |           |                          |                          |                            |           |           |
| azure_rm_acs                                | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_aks                                | -            | -                           | Yes          | Yes          | Yes          | Yes          |
| azure_rm_aks_facts                          | -            | -                           | Yes          | Yes          | Yes          | Yes          |
| azure_rm_aksversion_facts                   | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_containerinstance                  | -            | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_containerinstance_facts            | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_containerregistry                  | -            | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_containerregistry_facts            | -            | -                           | -            | Yes          | Yes          | Yes          |
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_containerregistryreplication_facts | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_containerregistrywebhook_facts     | -            | -                           | -            | -            | Yes          | Yes          |
| **Bazy danych**                    |           |                          |                          |                             |           |           |
| azure_rm_cosmosdbaccount                    | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_cosmosdbaccount_facts              | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_mariadbconfiguration               | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_mariadbconfiguration_facts         | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_mariadbdatabase                    | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_mariadbdatabase_facts              | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_mariadbfirewallrule                | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_mariadbfirewallrule_facts          | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_mariadbserver                      | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_mariadbserver_facts                | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_mysqlconfiguration_facts           | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_mysqldatabase                      | -            | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_mysqldatabase_facts                | -            | -                           | -            | Yes          | Yes          | Yes          |
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_mysqlserver                        | -            | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_mysqlserver_facts                  | -            | -                           | -            | Yes          | Yes          | Yes          |
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_postgresqldatabase                 | -            | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_postgresqldatabase_facts           | -            | -                           | -            | Yes          | Yes          | Yes          |
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_postgresqlserver                   | -            | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_postgresqlserver_facts             | -            | -                           | -            | Yes          | Yes          | Yes          |
| azure_rm_rediscache                         | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_rediscache_facts                   | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_rediscachefirewallrule             | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_sqldatabase                        | -            | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_sqldatabase_facts                  | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_sqlelasticpool                     | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_sqlelasticpool_facts               | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | Yes          | Yes          | Yes          |
| azure_rm_sqlfirewallrule_facts              | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_sqlserver                          | -            | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_sqlserver_facts                    | -            | Yes                         | Yes          | Yes          | Yes          | Yes          |
| **Analiza**                    |           |                          |                          |                             |           |           |
| azure_rm_hdinsightcluster                   | -            | -                           | -            | -            | Yes          | Yes          |
| **Integracja**                    |           |                          |                          |                             |           |           |
| azure_rm_servicebus                         | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_servicebus_facts                   | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_servicebusqueue                    | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_servicebussaspolicy                | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_servicebustopic                    | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_servicebustopicsubscription        | -            | -                           | -            | -            | Yes          | Yes          |
| **Bezpieczeństwo**                    |           |                          |                          |                             |           |           |
| azure_rm_keyvault                           | -            | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_keyvault_facts                     | -            | -                           | -              | -          | Yes          | Yes          |
| azure_rm_keyvaultkey                        | -            | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_keyvaultsecret                     | -            | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_roleassignment                     | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_roleassignment_facts               | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_roledefinition                     | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_roledefinition_facts               | -            | -                           | -            | -            | Yes          | Yes          |
| **DevOps**               |           |                          |                          |                             |           |           |
| azure_rm_devtestlab                         | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_devtestlab_facts                   | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_devtestlabarmtemplate_facts        | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_devtestlabartifact_facts           | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_devtestlabartifactsource           | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_devtestlabartifactsource_facts     | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_devtestlabcustomimage              | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_devtestlabenvironment              | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_devtestlabpolicy                   | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_devtestlabschedule                 | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_devtestlabvirtualmachine           | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_devtestlabvirtualmachine_facts | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_devtestlabvirtualnetwork           | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_devtestlabvirtualnetwork_facts     | -            | -                           | -            | -            | Yes          | Yes          |
| **Azure Monitor**          |           |                          |                          |                             |           |           |
| azure_rm_autoscale                  | -            | -                         | -          | Yes          | Yes          | Yes          |
| azure_rm_autoscale_facts            | -            | -                         | -          | Yes          | Yes          | Yes          |
| azure_rm_loganalyticsworkspace              | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_loganalyticsworkspace_facts        | -            | -                           | -            | -            | Yes          | Yes          |

## <a name="introduction-to-playbook-role-for-azure"></a>Wprowadzenie do podręcznika dotyczącego roli na platformie Azure

[Azure_preview_module podręcznikiem rola](https://galaxy.ansible.com/Azure/azure_preview_modules/) obejmuje wszystkich najnowszych modułów platformy Azure. Aktualizacje i poprawki błędów są wykonywane w sposób terminowy więcej niż oficjalnym wydaniem Ansible. Jeśli dla celów aprowizowania zasobów platformy Azure za pomocą rozwiązania Ansible, masz zaleca się instalowanie `azure_preview_module` roli elementu playbook.

`azure_preview_module` Podręcznikiem rola jest zwalniany co trzy tygodnie.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat role elementu playbook, zobacz [tworzenie elementów playbook wielokrotnego użytku](https://docs.ansible.com/ansible/latest/playbooks_reuse.html). 