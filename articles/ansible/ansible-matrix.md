---
title: Moduł rozwiązania Ansible i macierz wersji dla platformy Azure
description: Moduł rozwiązania Ansible i macierz wersji dla platformy Azure
keywords: rozwiązania ansible, role, macierz, wersja, Azure, DevOps
ms.topic: reference
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 18654500a78178c46e72f9f6cd01e8507fa179f0
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241544"
---
# <a name="ansible-module-and-version-matrix"></a>Moduł rozwiązania Ansible i macierz wersji

Rozwiązania ansible zawiera zestaw modułów do użycia podczas aprowizacji i konfigurowania zasobów platformy Azure. Do tych zasobów należą maszyny wirtualne, zestawy skalowania, usługi sieciowe i usługi kontenera. W tym artykule wymieniono różne moduły rozwiązania ansible dla platformy Azure i wersje rozwiązania ansible, w których są one dostarczane.

## <a name="ansible-modules-for-azure"></a>Moduły rozwiązania ansible dla platformy Azure

Następujące moduły można wykonać bezpośrednio na hostach zdalnych lub za pomocą elementy PlayBook.

Te moduły są dostępne z oficjalnego wydania rozwiązania ansible oraz z następujących ról programu Microsoft element PlayBook.

| Moduł rozwiązania ansible dla platformy Azure                   |  Rozwiązania ansible 2,4 |  Rozwiązania ansible 2,5 |  Rozwiązania ansible 2,6 | Rozwiązania ansible 2,7 | Rozwiązania ansible 2,8 | Rola rozwiązania ansible | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|--------------|--------------| 
| **Obliczanie**                    |           |                          |                          |                            |           |           |
| azure_rm_availabilityset                    | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          |
| azure_rm_availabilityset_facts              | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          |
| azure_rm_deployment                         | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          |
| azure_rm_deployment_facts                   | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_functionapp                        | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          |
| azure_rm_functionapp_facts                  | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          |
| azure_rm_image                              | -            | Tak                         | Tak          | Tak          | Tak          | Tak          |
| azure_rm_image_facts                        | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_resource                           | -            | -                           | Tak          | Tak          | Tak          | Tak          |
| azure_rm_resource_facts                     | -            | -                           | Tak          | Tak          | Tak          | Tak          |
| azure_rm_resourcegroup                      | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          |
| azure_rm_resourcegroup_facts                | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          |
| azure_rm_virtualmachine                     | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          |
| azure_rm_virtualmachine_facts               | -            | -                           | -            | Tak          | Tak          | Tak          |
| azure_rm_virtualmachineextension           | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          |
| azure_rm_virtualmachineextension_facts      | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_virtualmachineimage_facts          | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          |
| azure_rm_virtualmachinescaleset            | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          |
| azure_rm_virtualmachinescaleset_facts      | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          |
| azure_rm_virtualmachinescalesetextension    | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_virtualmachinescalesetextension_facts | -            | -                        | -            | -            | Tak          | Tak          |
| azure_rm_virtualmachinescalesetinstance     | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_virtualmachinescalesetinstance_facts | -            | -                         | -            | -            | Tak          | Tak          |
| **Sieć**                              |              |                             |              |              |              |              |
| azure_rm_appgateway                         | -            | -                           | -            | Tak          | Tak          | Tak          |
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
| azure_rm_dnsrecordset                       | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          |
| azure_rm_dnsrecordset_facts                 | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          |
| azure_rm_dnszone                            | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          |
| azure_rm_dnszone_facts                      | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          |
| azure_rm_loadbalancer                       | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          |
| azure_rm_loadbalancer_facts                 | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          |
| azure_rm_networkinterface                   | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          |
| azure_rm_networkinterface_facts             | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          |
| azure_rm_publicipaddress                    | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          |
| azure_rm_publicipaddress_facts              | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          |
| azure_rm_route                              | -            | -                           | -            | Tak          | Tak          | Tak          |
| azure_rm_routetable                         | -            | -                           | -            | Tak          | Tak          | Tak          |
| azure_rm_routetable_facts                   | -            | -                           | -            | Tak          | Tak          | Tak          |
| azure_rm_securitygroup                      | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          |
| azure_rm_subnet                             | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          |
| azure_rm_subnet_facts                       | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_trafficmanagerendpoint             | -            | -                         | -          | Tak          | Tak          | Tak          |
| azure_rm_trafficmanagerendpoint_facts       | -            | -                         | -          | Tak          | Tak          | Tak          |
| azure_rm_trafficmanagerprofile              | -            | -                         | -          | Tak          | Tak          | Tak          |
| azure_rm_trafficmanagerprofile_facts        | -            | -                         | -          | Tak          | Tak          | Tak          |
| azure_rm_virtualnetwork                     | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          |
| azure_rm_virtualnetwork_facts               | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          |
| azure_rm_virtualnetworkpeering              | -            | -                         | -          | -            | Tak          | Tak          |
| **Magazyn**                    |           |                          |                          |                            |           |           |
| azure_rm_manageddisk                        | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          |
| azure_rm_manageddisk_facts                  | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          |
| azure_rm_storageaccount                     | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          |
| azure_rm_storageaccount_facts               | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          |
| azure_rm_storageblob                        | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          |
| **Sieć Web**                    |           |                          |                          |                             |           |           |
| azure_rm_appserviceplan                     | -            | -                         | -          | Tak          | Tak          | Tak          |
| azure_rm_appserviceplan_facts               | -            | -                         | -          | Tak          | Tak          | Tak          |
| azure_rm_webapp                             | -            | -                         | -          | Tak          | Tak          | Tak          |
| azure_rm_webapp_facts                       | -            | -                         | -          | Tak          | Tak          | Tak          |
| azure_rm_webappslot                         | -            | -                         | -          | -            | Tak          | Tak          |
| **Containers**                    |           |                          |                          |                            |           |           |
| azure_rm_acs                                | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          |
| azure_rm_aks                                | -            | -                           | Tak          | Tak          | Tak          | Tak          |
| azure_rm_aks_facts                          | -            | -                           | Tak          | Tak          | Tak          | Tak          |
| azure_rm_aksversion_facts                   | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_containerinstance                  | -            | Tak                         | Tak          | Tak          | Tak          | Tak          |
| azure_rm_containerinstance_facts            | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_containerregistry                  | -            | Tak                         | Tak          | Tak          | Tak          | Tak          |
| azure_rm_containerregistry_facts            | -            | -                           | -            | Tak          | Tak          | Tak          |
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
| azure_rm_mysqldatabase                      | -            | Tak                         | Tak          | Tak          | Tak          | Tak          |
| azure_rm_mysqldatabase_facts                | -            | -                           | -            | Tak          | Tak          | Tak          |
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_mysqlserver                        | -            | Tak                         | Tak          | Tak          | Tak          | Tak          |
| azure_rm_mysqlserver_facts                  | -            | -                           | -            | Tak          | Tak          | Tak          |
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_postgresqldatabase                 | -            | Tak                         | Tak          | Tak          | Tak          | Tak          |
| azure_rm_postgresqldatabase_facts           | -            | -                           | -            | Tak          | Tak          | Tak          |
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_postgresqlserver                   | -            | Tak                         | Tak          | Tak          | Tak          | Tak          |
| azure_rm_postgresqlserver_facts             | -            | -                           | -            | Tak          | Tak          | Tak          |
| azure_rm_rediscache                         | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_rediscache_facts                   | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_rediscachefirewallrule             | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_sqldatabase                        | -            | Tak                         | Tak          | Tak          | Tak          | Tak          |
| azure_rm_sqldatabase_facts                  | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_sqlelasticpool                     | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_sqlelasticpool_facts               | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | Tak          | Tak          | Tak          |
| azure_rm_sqlfirewallrule_facts              | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_sqlserver                          | -            | Tak                         | Tak          | Tak          | Tak          | Tak          |
| azure_rm_sqlserver_facts                    | -            | Tak                         | Tak          | Tak          | Tak          | Tak          |
| **Analiza**                    |           |                          |                          |                             |           |           |
| azure_rm_hdinsightcluster                   | -            | -                           | -            | -            | Tak          | Tak          |
| **Integration**                    |           |                          |                          |                             |           |           |
| azure_rm_servicebus                         | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_servicebus_facts                   | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_servicebusqueue                    | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_servicebussaspolicy                | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_servicebustopic                    | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_servicebustopicsubscription        | -            | -                           | -            | -            | Tak          | Tak          |
| **Bezpieczeństwo**                    |           |                          |                          |                             |           |           |
| azure_rm_keyvault                           | -            | Tak                         | Tak          | Tak          | Tak          | Tak          |
| azure_rm_keyvault_facts                     | -            | -                           | -              | -          | Tak          | Tak          |
| azure_rm_keyvaultkey                        | -            | Tak                         | Tak          | Tak          | Tak          | Tak          |
| azure_rm_keyvaultsecret                     | -            | Tak                         | Tak          | Tak          | Tak          | Tak          |
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
| azure_rm_autoscale                  | -            | -                         | -          | Tak          | Tak          | Tak          |
| azure_rm_autoscale_facts            | -            | -                         | -          | Tak          | Tak          | Tak          |
| azure_rm_loganalyticsworkspace              | -            | -                           | -            | -            | Tak          | Tak          |
| azure_rm_loganalyticsworkspace_facts        | -            | -                           | -            | -            | Tak          | Tak          |

## <a name="introduction-to-playbook-role-for-azure"></a>Wprowadzenie do roli element PlayBook na platformie Azure

[Rola azure_preview_module element PlayBook](https://galaxy.ansible.com/Azure/azure_preview_modules/) obejmuje wszystkie najnowsze moduły platformy Azure. Aktualizacje i poprawki błędów są wykonywane w sposób bardziej czasochłonny niż oficjalne wydanie rozwiązania ansible. W przypadku korzystania z rozwiązania ansible na potrzeby aprowizacji zasobów platformy Azure zaleca się zainstalowanie roli element PlayBook `azure_preview_module`.

@No__t-0 rola element PlayBook jest wydawana co trzy tygodnie.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat ról element PlayBook, zobacz [Tworzenie elementy PlayBook wielokrotnego użytku](https://docs.ansible.com/ansible/latest/playbooks_reuse.html). 
