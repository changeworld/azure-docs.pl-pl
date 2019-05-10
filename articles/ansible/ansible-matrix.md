---
title: Macierz modułu i wersji rozwiązania Ansible na platformie Azure | Dokumentacja firmy Microsoft
description: Moduł rozwiązania Ansible i macierz wersji dla platformy Azure
keywords: rozwiązanie ansible, ról, macierz, wersji, azure, metodyki devops
ms.topic: reference
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: b3396b2f4639cc7298b77810dbaafadd308d6b24
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65230983"
---
# <a name="ansible-module-and-version-matrix"></a>Macierz modułu i wersji rozwiązania Ansible

Rozwiązanie Ansible zawiera zestaw modułów do użytku w aprowizacji i konfigurowania jej zasobów. Te zasoby obejmują maszyny wirtualne, zestawy skalowania, sieci, usługi i usługi kontenera. W tym artykule wymieniono różne moduły Ansible dla platformy Azure i wersji rozwiązania Ansible, w których są dostarczane.

## <a name="ansible-modules-for-azure"></a>Rozwiązanie Ansible moduły dla platformy Azure

Następujące moduły mogą być wykonywane bezpośrednio na hostach zdalnych lub za pomocą elementów playbook.

Te moduły są dostępne z rozwiązania Ansible oficjalnym wydaniem i z następujące role elementu playbook firmy Microsoft.

| Moduł Ansible na platformie Azure                   |  2.4 rozwiązania Ansible |  2.5 rozwiązania Ansible |  2.6 rozwiązania Ansible | Rozwiązanie Ansible w wersji 2.7 | 2.8 rozwiązania Ansible | Rozwiązanie Ansible roli | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|--------------|--------------| 
| **Obliczanie**                    |           |                          |                          |                            |           |           |
| azure_rm_availabilityset                    | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_availabilityset_facts              | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_deployment                         | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_deployment_facts                   | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_functionapp                        | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_functionapp_facts                  | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_image                              | -            | Tak                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_image_facts                        | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_resource                           | -            | -                           | Tak          | Yes          | Yes          | Tak          |
| azure_rm_resource_facts                     | -            | -                           | Tak          | Yes          | Yes          | Tak          |
| azure_rm_resourcegroup                      | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_resourcegroup_facts                | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_virtualmachine                     | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_virtualmachine_facts               | -            | -                           | -            | Tak          | Yes          | Tak          |
| azure_rm_virtualmachineextension           | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_virtualmachineextension_facts      | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_virtualmachineimage_facts          | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_virtualmachinescaleset            | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_virtualmachinescaleset_facts      | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_virtualmachinescalesetextension    | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_virtualmachinescalesetextension_facts | -            | -                        | -            | -            | Tak          | Tak          |
| azure_rm_virtualmachinescalesetinstance     | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_virtualmachinescalesetinstance_facts | -            | -                         | -            | -            | Tak          | Tak          |
| **Sieć**                              |              |                             |              |              |              |              |
| azure_rm_appgateway                         | -            | -                           | -            | Tak          | Yes          | Tak          |
| azure_rm_appgwroute                         | -            | -                           | -            | -            | -          | Tak          |
| azure_rm_appgwroute_facts                   | -            | -                           | -            | -            | -          | Tak          |
| azure_rm_appgwroutetable                    | -            | -                           | -            | -            | -          | Tak          |
| azure_rm_appgwroutetable_facts              | -            | -                           | -            | -            | -          | Tak          |
| azure_rm_applicationsecuritygroup           | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_applicationsecuritygroup_facts     | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_cdnendpoint                        | -            | -                         | -          | -            | Tak          | Tak          |
| azure_rm_cdnendpoint_facts                  | -            | -                         | -          | -            | Tak          | Tak          |
| azure_rm_cdnprofile                         | -            | -                         | -          | -            | Tak          | Tak          |
| azure_rm_cdnprofile_facts                   | -            | -                         | -          | -            | Tak          | Tak          |
| azure_rm_dnsrecordset                       | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_dnsrecordset_facts                 | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_dnszone                            | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_dnszone_facts                      | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_loadbalancer                       | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_loadbalancer_facts                 | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_networkinterface                   | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_networkinterface_facts             | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_publicipaddress                    | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_publicipaddress_facts              | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_route                              | -            | -                           | -            | Tak          | Yes          | Tak          |
| azure_rm_routetable                         | -            | -                           | -            | Tak          | Yes          | Tak          |
| azure_rm_routetable_facts                   | -            | -                           | -            | Tak          | Yes          | Tak          |
| azure_rm_securitygroup                      | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_subnet                             | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_subnet_facts                       | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_trafficmanagerendpoint             | -            | -                         | -          | Tak          | Yes          | Tak          |
| azure_rm_trafficmanagerendpoint_facts       | -            | -                         | -          | Tak          | Yes          | Tak          |
| azure_rm_trafficmanagerprofile              | -            | -                         | -          | Tak          | Yes          | Tak          |
| azure_rm_trafficmanagerprofile_facts        | -            | -                         | -          | Tak          | Yes          | Tak          |
| azure_rm_virtualnetwork                     | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_virtualnetwork_facts               | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_virtualnetworkpeering              | -            | -                         | -          | -            | Tak          | Tak          |
| **Storage**                    |           |                          |                          |                            |           |           |
| azure_rm_manageddisk                        | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_manageddisk_facts                  | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_storageaccount                     | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_storageaccount_facts               | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_storageblob                        | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| **Sieć Web**                    |           |                          |                          |                             |           |           |
| azure_rm_appserviceplan                     | -            | -                         | -          | Tak          | Yes          | Tak          |
| azure_rm_appserviceplan_facts               | -            | -                         | -          | Tak          | Yes          | Tak          |
| azure_rm_webapp                             | -            | -                         | -          | Tak          | Yes          | Tak          |
| azure_rm_webapp_facts                       | -            | -                         | -          | Tak          | Yes          | Tak          |
| azure_rm_webappslot                         | -            | -                         | -          | -            | Tak          | Tak          |
| **Containers**                    |           |                          |                          |                            |           |           |
| azure_rm_acs                                | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_aks                                | -            | -                           | Tak          | Yes          | Yes          | Tak          |
| azure_rm_aks_facts                          | -            | -                           | Tak          | Yes          | Yes          | Tak          |
| azure_rm_aksversion_facts                   | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_containerinstance                  | -            | Tak                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_containerinstance_facts            | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_containerregistry                  | -            | Tak                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_containerregistry_facts            | -            | -                           | -            | Tak          | Yes          | Tak          |
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_containerregistryreplication_facts | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_containerregistrywebhook_facts     | -            | -                           | -            | -            | Tak          | Tak          |
| **Bazy danych**                    |           |                          |                          |                             |           |           |
| azure_rm_cosmosdbaccount                    | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_cosmosdbaccount_facts              | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_mariadbconfiguration               | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_mariadbconfiguration_facts         | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_mariadbdatabase                    | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_mariadbdatabase_facts              | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_mariadbfirewallrule                | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_mariadbfirewallrule_facts          | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_mariadbserver                      | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_mariadbserver_facts                | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_mysqlconfiguration_facts           | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_mysqldatabase                      | -            | Tak                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_mysqldatabase_facts                | -            | -                           | -            | Tak          | Yes          | Tak          |
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_mysqlserver                        | -            | Tak                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_mysqlserver_facts                  | -            | -                           | -            | Tak          | Yes          | Tak          |
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_postgresqldatabase                 | -            | Tak                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_postgresqldatabase_facts           | -            | -                           | -            | Tak          | Yes          | Tak          |
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_postgresqlserver                   | -            | Tak                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_postgresqlserver_facts             | -            | -                           | -            | Tak          | Yes          | Tak          |
| azure_rm_rediscache                         | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_rediscache_facts                   | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_rediscachefirewallrule             | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_sqldatabase                        | -            | Tak                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_sqldatabase_facts                  | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_sqlelasticpool                     | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_sqlelasticpool_facts               | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | Tak          | Yes          | Tak          |
| azure_rm_sqlfirewallrule_facts              | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_sqlserver                          | -            | Tak                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_sqlserver_facts                    | -            | Tak                         | Yes          | Yes          | Yes          | Tak          |
| **Analiza**                    |           |                          |                          |                             |           |           |
| azure_rm_hdinsightcluster                   | -            | -                           | -            | -            | Tak          | Tak          |
| **Integracja**                    |           |                          |                          |                             |           |           |
| azure_rm_servicebus                         | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_servicebus_facts                   | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_servicebusqueue                    | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_servicebussaspolicy                | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_servicebustopic                    | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_servicebustopicsubscription        | -            | -                           | -            | -            | Tak          | Tak          |
| **Zabezpieczenia**                    |           |                          |                          |                             |           |           |
| azure_rm_keyvault                           | -            | Tak                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_keyvault_facts                     | -            | -                           | -              | -          | Tak          | Tak          |
| azure_rm_keyvaultkey                        | -            | Tak                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_keyvaultsecret                     | -            | Tak                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_roleassignment                     | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_roleassignment_facts               | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_roledefinition                     | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_roledefinition_facts               | -            | -                           | -            | -            | Tak          | Tak          |
| **DevOps**               |           |                          |                          |                             |           |           |
| azure_rm_devtestlab                         | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_devtestlab_facts                   | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_devtestlabarmtemplate_facts        | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_devtestlabartifact_facts           | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_devtestlabartifactsource           | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_devtestlabartifactsource_facts     | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_devtestlabcustomimage              | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_devtestlabenvironment              | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_devtestlabpolicy                   | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_devtestlabschedule                 | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_devtestlabvirtualmachine           | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_devtestlabvirtualmachine_facts | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_devtestlabvirtualnetwork           | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_devtestlabvirtualnetwork_facts     | -            | -                           | -            | -            | Tak          | Tak          |
| **Azure Monitor**          |           |                          |                          |                             |           |           |
| azure_rm_autoscale                  | -            | -                         | -          | Tak          | Yes          | Tak          |
| azure_rm_autoscale_facts            | -            | -                         | -          | Tak          | Yes          | Tak          |
| azure_rm_loganalyticsworkspace              | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_loganalyticsworkspace_facts        | -            | -                           | -            | -            | Tak          | Tak          |

## <a name="introduction-to-playbook-role-for-azure"></a>Wprowadzenie do podręcznika dotyczącego roli na platformie Azure

[Azure_preview_module podręcznikiem rola](https://galaxy.ansible.com/Azure/azure_preview_modules/) obejmuje wszystkich najnowszych modułów platformy Azure. Aktualizacje i poprawki błędów są wykonywane w sposób terminowy więcej niż oficjalnym wydaniem Ansible. Jeśli dla celów aprowizowania zasobów platformy Azure za pomocą rozwiązania Ansible, masz zaleca się instalowanie `azure_preview_module` roli elementu playbook.

`azure_preview_module` Podręcznikiem rola jest zwalniany co trzy tygodnie.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat role elementu playbook, zobacz [tworzenie elementów playbook wielokrotnego użytku](https://docs.ansible.com/ansible/latest/playbooks_reuse.html). 
