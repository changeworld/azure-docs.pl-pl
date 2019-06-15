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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65230983"
---
# <a name="ansible-module-and-version-matrix"></a>Macierz modułu i wersji rozwiązania Ansible

Rozwiązanie Ansible zawiera zestaw modułów do użytku w aprowizacji i konfigurowania jej zasobów. Te zasoby obejmują maszyny wirtualne, zestawy skalowania, sieci, usługi i usługi kontenera. W tym artykule wymieniono różne moduły Ansible dla platformy Azure i wersji rozwiązania Ansible, w których są dostarczane.

## <a name="ansible-modules-for-azure"></a>Rozwiązanie Ansible moduły dla platformy Azure

Następujące moduły mogą być wykonywane bezpośrednio na hostach zdalnych lub za pomocą elementów playbook.

Te moduły są dostępne z rozwiązania Ansible oficjalnym wydaniem i z następujące role elementu playbook firmy Microsoft.

| Moduł Ansible na platformie Azure                   |  2\.4 rozwiązania Ansible |  2\.5 rozwiązania Ansible |  2\.6 rozwiązania Ansible | Rozwiązanie Ansible w wersji 2.7 | 2\.8 rozwiązania Ansible | Rozwiązanie Ansible roli | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|--------------|--------------| 
| **Obliczanie**                    |           |                          |                          |                            |           |           |
| azure_rm_availabilityset                    | Yes          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_availabilityset_facts              | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_deployment                         | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_deployment_facts                   | -            | -                           | -            | -            | Tak          | Yes          |
| azure_rm_functionapp                        | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_functionapp_facts                  | Yes          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_image                              | -            | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_image_facts                        | -            | -                           | -            | -            | Yes          | Tak          |
| azure_rm_resource                           | -            | -                           | Tak          | Yes          | Yes          | Tak          |
| azure_rm_resource_facts                     | -            | -                           | Yes          | Yes          | Yes          | Yes          |
| azure_rm_resourcegroup                      | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_resourcegroup_facts                | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_virtualmachine                     | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_virtualmachine_facts               | -            | -                           | -            | Tak          | Yes          | Tak          |
| azure_rm_virtualmachineextension           | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_virtualmachineextension_facts      | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_virtualmachineimage_facts          | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_virtualmachinescaleset            | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_virtualmachinescaleset_facts      | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_virtualmachinescalesetextension    | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_virtualmachinescalesetextension_facts | -            | -                        | -            | -            | Tak          | Tak          |
| azure_rm_virtualmachinescalesetinstance     | -            | -                           | -            | -            | Tak          | Yes          |
| azure_rm_virtualmachinescalesetinstance_facts | -            | -                         | -            | -            | Tak          | Tak          |
| **Sieć**                              |              |                             |              |              |              |              |
| azure_rm_appgateway                         | -            | -                           | -            | Tak          | Yes          | Yes          |
| azure_rm_appgwroute                         | -            | -                           | -            | -            | -          | Yes          |
| azure_rm_appgwroute_facts                   | -            | -                           | -            | -            | -          | Tak          |
| azure_rm_appgwroutetable                    | -            | -                           | -            | -            | -          | Tak          |
| azure_rm_appgwroutetable_facts              | -            | -                           | -            | -            | -          | Tak          |
| azure_rm_applicationsecuritygroup           | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_applicationsecuritygroup_facts     | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_cdnendpoint                        | -            | -                         | -          | -            | Tak          | Tak          |
| azure_rm_cdnendpoint_facts                  | -            | -                         | -          | -            | Tak          | Yes          |
| azure_rm_cdnprofile                         | -            | -                         | -          | -            | Tak          | Tak          |
| azure_rm_cdnprofile_facts                   | -            | -                         | -          | -            | Yes          | Tak          |
| azure_rm_dnsrecordset                       | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_dnsrecordset_facts                 | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_dnszone                            | Yes          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_dnszone_facts                      | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_loadbalancer                       | Tak          | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_loadbalancer_facts                 | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_networkinterface                   | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_networkinterface_facts             | Tak          | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_publicipaddress                    | Tak          | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_publicipaddress_facts              | Tak          | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_route                              | -            | -                           | -            | Yes          | Yes          | Yes          |
| azure_rm_routetable                         | -            | -                           | -            | Tak          | Yes          | Yes          |
| azure_rm_routetable_facts                   | -            | -                           | -            | Tak          | Yes          | Tak          |
| azure_rm_securitygroup                      | Yes          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_subnet                             | Tak          | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_subnet_facts                       | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_trafficmanagerendpoint             | -            | -                         | -          | Tak          | Yes          | Tak          |
| azure_rm_trafficmanagerendpoint_facts       | -            | -                         | -          | Tak          | Yes          | Yes          |
| azure_rm_trafficmanagerprofile              | -            | -                         | -          | Tak          | Yes          | Yes          |
| azure_rm_trafficmanagerprofile_facts        | -            | -                         | -          | Tak          | Yes          | Tak          |
| azure_rm_virtualnetwork                     | Tak          | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_virtualnetwork_facts               | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_virtualnetworkpeering              | -            | -                         | -          | -            | Tak          | Yes          |
| **Storage**                    |           |                          |                          |                            |           |           |
| azure_rm_manageddisk                        | Yes          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_manageddisk_facts                  | Yes          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_storageaccount                     | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_storageaccount_facts               | Tak          | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_storageblob                        | Tak          | Yes                         | Yes          | Yes          | Yes          | Yes          |
| **Sieć Web**                    |           |                          |                          |                             |           |           |
| azure_rm_appserviceplan                     | -            | -                         | -          | Tak          | Yes          | Tak          |
| azure_rm_appserviceplan_facts               | -            | -                         | -          | Yes          | Yes          | Yes          |
| azure_rm_webapp                             | -            | -                         | -          | Tak          | Yes          | Yes          |
| azure_rm_webapp_facts                       | -            | -                         | -          | Yes          | Yes          | Tak          |
| azure_rm_webappslot                         | -            | -                         | -          | -            | Yes          | Yes          |
| **Containers**                    |           |                          |                          |                            |           |           |
| azure_rm_acs                                | Yes          | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_aks                                | -            | -                           | Tak          | Yes          | Yes          | Yes          |
| azure_rm_aks_facts                          | -            | -                           | Yes          | Yes          | Yes          | Yes          |
| azure_rm_aksversion_facts                   | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_containerinstance                  | -            | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_containerinstance_facts            | -            | -                           | -            | -            | Yes          | Tak          |
| azure_rm_containerregistry                  | -            | Tak                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_containerregistry_facts            | -            | -                           | -            | Tak          | Yes          | Tak          |
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_containerregistryreplication_facts | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_containerregistrywebhook_facts     | -            | -                           | -            | -            | Tak          | Yes          |
| **Bazy danych**                    |           |                          |                          |                             |           |           |
| azure_rm_cosmosdbaccount                    | -            | -                           | -            | -            | Tak          | Yes          |
| azure_rm_cosmosdbaccount_facts              | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_mariadbconfiguration               | -            | -                           | -            | -            | Yes          | Tak          |
| azure_rm_mariadbconfiguration_facts         | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_mariadbdatabase                    | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_mariadbdatabase_facts              | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_mariadbfirewallrule                | -            | -                           | -            | -            | Yes          | Tak          |
| azure_rm_mariadbfirewallrule_facts          | -            | -                           | -            | -            | Yes          | Tak          |
| azure_rm_mariadbserver                      | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_mariadbserver_facts                | -            | -                           | -            | -            | Tak          | Yes          |
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_mysqlconfiguration_facts           | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_mysqldatabase                      | -            | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_mysqldatabase_facts                | -            | -                           | -            | Tak          | Yes          | Yes          |
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | Yes          | Tak          |
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_mysqlserver                        | -            | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_mysqlserver_facts                  | -            | -                           | -            | Yes          | Yes          | Tak          |
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | Tak          | Yes          |
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_postgresqldatabase                 | -            | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_postgresqldatabase_facts           | -            | -                           | -            | Tak          | Yes          | Tak          |
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_postgresqlserver                   | -            | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_postgresqlserver_facts             | -            | -                           | -            | Tak          | Yes          | Tak          |
| azure_rm_rediscache                         | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_rediscache_facts                   | -            | -                           | -            | -            | Tak          | Yes          |
| azure_rm_rediscachefirewallrule             | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_sqldatabase                        | -            | Tak                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_sqldatabase_facts                  | -            | -                           | -            | -            | Tak          | Yes          |
| azure_rm_sqlelasticpool                     | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_sqlelasticpool_facts               | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | Tak          | Yes          | Tak          |
| azure_rm_sqlfirewallrule_facts              | -            | -                           | -            | -            | Tak          | Yes          |
| azure_rm_sqlserver                          | -            | Yes                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_sqlserver_facts                    | -            | Yes                         | Yes          | Yes          | Yes          | Tak          |
| **Analiza**                    |           |                          |                          |                             |           |           |
| azure_rm_hdinsightcluster                   | -            | -                           | -            | -            | Tak          | Yes          |
| **Integracja**                    |           |                          |                          |                             |           |           |
| azure_rm_servicebus                         | -            | -                           | -            | -            | Yes          | Tak          |
| azure_rm_servicebus_facts                   | -            | -                           | -            | -            | Tak          | Yes          |
| azure_rm_servicebusqueue                    | -            | -                           | -            | -            | Tak          | Yes          |
| azure_rm_servicebussaspolicy                | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_servicebustopic                    | -            | -                           | -            | -            | Tak          | Yes          |
| azure_rm_servicebustopicsubscription        | -            | -                           | -            | -            | Tak          | Tak          |
| **Zabezpieczenia**                    |           |                          |                          |                             |           |           |
| azure_rm_keyvault                           | -            | Tak                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_keyvault_facts                     | -            | -                           | -              | -          | Yes          | Tak          |
| azure_rm_keyvaultkey                        | -            | Yes                         | Yes          | Yes          | Yes          | Tak          |
| azure_rm_keyvaultsecret                     | -            | Tak                         | Yes          | Yes          | Yes          | Yes          |
| azure_rm_roleassignment                     | -            | -                           | -            | -            | Yes          | Tak          |
| azure_rm_roleassignment_facts               | -            | -                           | -            | -            | Yes          | Tak          |
| azure_rm_roledefinition                     | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_roledefinition_facts               | -            | -                           | -            | -            | Tak          | Tak          |
| **DevOps**               |           |                          |                          |                             |           |           |
| azure_rm_devtestlab                         | -            | -                           | -            | -            | Tak          | Yes          |
| azure_rm_devtestlab_facts                   | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_devtestlabarmtemplate_facts        | -            | -                           | -            | -            | Yes          | Tak          |
| azure_rm_devtestlabartifact_facts           | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_devtestlabartifactsource           | -            | -                           | -            | -            | Tak          | Yes          |
| azure_rm_devtestlabartifactsource_facts     | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_devtestlabcustomimage              | -            | -                           | -            | -            | Yes          | Yes          |
| azure_rm_devtestlabenvironment              | -            | -                           | -            | -            | Yes          | Tak          |
| azure_rm_devtestlabpolicy                   | -            | -                           | -            | -            | Tak          | Yes          |
| azure_rm_devtestlabschedule                 | -            | -                           | -            | -            | Yes          | Tak          |
| azure_rm_devtestlabvirtualmachine           | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_devtestlabvirtualmachine_facts | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_devtestlabvirtualnetwork           | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_devtestlabvirtualnetwork_facts     | -            | -                           | -            | -            | Yes          | Tak          |
| **Azure Monitor**          |           |                          |                          |                             |           |           |
| azure_rm_autoscale                  | -            | -                         | -          | Tak          | Yes          | Tak          |
| azure_rm_autoscale_facts            | -            | -                         | -          | Tak          | Yes          | Tak          |
| azure_rm_loganalyticsworkspace              | -            | -                           | -            | -            | Tak          | Yes          |
| azure_rm_loganalyticsworkspace_facts        | -            | -                           | -            | -            | Tak          | Yes          |

## <a name="introduction-to-playbook-role-for-azure"></a>Wprowadzenie do podręcznika dotyczącego roli na platformie Azure

[Azure_preview_module podręcznikiem rola](https://galaxy.ansible.com/Azure/azure_preview_modules/) obejmuje wszystkich najnowszych modułów platformy Azure. Aktualizacje i poprawki błędów są wykonywane w sposób terminowy więcej niż oficjalnym wydaniem Ansible. Jeśli dla celów aprowizowania zasobów platformy Azure za pomocą rozwiązania Ansible, masz zaleca się instalowanie `azure_preview_module` roli elementu playbook.

`azure_preview_module` Podręcznikiem rola jest zwalniany co trzy tygodnie.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat role elementu playbook, zobacz [tworzenie elementów playbook wielokrotnego użytku](https://docs.ansible.com/ansible/latest/playbooks_reuse.html). 
