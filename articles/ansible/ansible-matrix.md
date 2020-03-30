---
title: Ansible moduł i matryca wersji dla platformy Azure | Dokumenty firmy Microsoft
description: Moduł rozwiązania Ansible i macierz wersji dla platformy Azure
keywords: ansible, role, macierz, wersja, azure, devops
ms.topic: reference
ms.date: 10/14/2019
ms.openlocfilehash: 54e27c7570ba1cdbce7355740181d68a2f3efbac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74155983"
---
# <a name="ansible-module-and-version-matrix"></a>Moduł rozwiązania Ansible i macierz wersji

Ansible zawiera zestaw modułów do użycia w inicjowaniu obsługi administracyjnej i konfigurowania zasobów platformy Azure. Zasoby te obejmują maszyny wirtualne, zestawy skalowania, usługi sieciowe i usługi kontenerów. W tym artykule wymieniono różne moduły Ansible dla platformy Azure i wersje Ansible, w których są dostarczane.

## <a name="ansible-modules-for-azure"></a>Moduły Ansible dla platformy Azure

Następujące moduły mogą być wykonywane bezpośrednio na zdalnych hostach lub za pośrednictwem podręczników.  

Moduły te są dostępne w oficjalnym wydaniu Ansible i z następujących ról podręcznika firmy Microsoft.

> [!NOTE]
> Od ansible 2.9, zmieniliśmy nazwę wszystkich *_facts modułów na *_info, aby przestrzegać konwencji nazewnictwa Ansible. Stare i przemianowane moduły są połączone, więc oprócz wyświetlania ostrzeżenia o deprecation, wszystkie moduły działają jak poprzednio.

| Moduł Ansible dla platformy Azure                   |  Możliwość dosyć 2.4 |  Możliwość dosyć 2,5 |  Możliwość dolnego 2,6 | Możliwość dosyć 2.7 | Możliwość dosyć 2,8 | Możliwość dosyć 2,9 | Rolą ansible | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|--------------|--------------|--------------|  
| **Obliczanie**                    |           |                          |                          |                            |           |           |           |
| azure_rm_availabilityset                   | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_availabilityset_info              | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_batchaccount                       | -            | -                           | -            | -            | -            | Tak          | Tak          |
| azure_rm_deployment                         | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_deployment_info                   | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_functionapp                        | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_functionapp_info                  | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_gallery                            | -            | -                           | -            | -            | -            | Tak          | Tak          |
| azure_rm_gallery_info                       | -            | -                           | -            | -            | -            | Tak          | Tak          |
| azure_rm_galleryimage                       | -            | -                           | -            | -            | -            | Tak          | Tak          |
| azure_rm_galleryimage_info                  | -            | -                           | -            | -            | -            | Tak          | Tak          |
| azure_rm_galleryimageversion                | -            | -                           | -            | -            | -            | Tak          | Tak          |
| azure_rm_galleryimageversion_info           | -            | -                           | -            | -            | -            | Tak          | Tak          |
| azure_rm_image                              | -            | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_image_info                        | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_resource                           | -            | -                           | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_resource_info                     | -            | -                           | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_resourcegroup                      | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_resourcegroup_info                | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_snapshot                           | -            | -                           | -            | -            | -            | Tak          | Tak          |
| azure_rm_virtualmachine                     | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_virtualmachine_info               | -            | -                           | -            | Tak          | Tak          | Tak          | Tak          |
| azure_rm_virtualmachineextension            | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_virtualmachineextension_info      | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_virtualmachineimage_info          | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_virtualmachinescaleset             | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_virtualmachinescaleset_info       | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_virtualmachinescalesetextension    | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_virtualmachinescalesetextension_info | -            | -                        | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_virtualmachinescalesetinstance     | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_virtualmachinescalesetinstance_info | -            | -                         | -            | -            | Tak          | Tak          | Tak          |
| **Obsługa sieci**                              |              |                             |              |              |              |              |              |
| azure_rm_appgateway                         | -            | -                           | -            | Tak          | Tak          | Tak          | Tak          |
| azure_rm_applicationsecuritygroup           | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_applicationsecuritygroup_info     | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_cdnendpoint                        | -            | -                         | -          | -            | Tak          | Tak          | Tak          |
| azure_rm_cdnendpoint_info                  | -            | -                         | -          | -            | Tak          | Tak          | Tak          |
| azure_rm_cdnprofile                         | -            | -                         | -          | -            | Tak          | Tak          | Tak          |
| azure_rm_cdnprofile_info                   | -            | -                         | -          | -            | Tak          | Tak          | Tak          |
| azure_rm_dnsrecordset                       | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_dnsrecordset_info                 | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_dnszone                            | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_dnszone_info                      | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_firewall                           | -            | -                           | -            | -            | -            | Tak          | Tak          |
| azure_rm_firewall_info                      | -            | -                           | -            | -            | -            | Tak          | Tak          |
| azure_rm_loadbalancer                       | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_loadbalancer_info                 | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_networkinterface                   | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_networkinterface_info             | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_publicipaddress                    | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_publicipaddress_info              | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_route                              | -            | -                           | -            | Tak          | Tak          | Tak          | Tak          |
| azure_rm_routetable                         | -            | -                           | -            | Tak          | Tak          | Tak          | Tak          |
| azure_rm_routetable_info                   | -            | -                           | -            | Tak          | Tak          | Tak          | Tak          |
| azure_rm_securitygroup                      | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_securitygroup_info                 | -            | -                           | -            | -            | -            | Tak          | Tak          |
| azure_rm_subnet                             | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_subnet_info                       | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_trafficmanagerendpoint             | -            | -                         | -          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_trafficmanagerendpoint_info       | -            | -                         | -          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_trafficmanagerprofile              | -            | -                         | -          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_trafficmanagerprofile_info        | -            | -                         | -          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_virtualnetwork                     | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_virtualnetwork_info               | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_virtualnetworkgateway              | -            | -                         | -          | -            | Tak          | Tak          | Tak          |
| azure_rm_virtualnetworkpeering              | -            | -                         | -          | -            | Tak          | Tak          | Tak          |
| azure_rm_virtualnetworkpeering_info         | -            | -                         | -          | -            | -            | Tak          | Tak          |
| **Magazyn**                    |           |                          |                          |                            |           |           |         |
| azure_rm_manageddisk                        | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_manageddisk_info                  | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_storageaccount                     | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_storageaccount_info               | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_storageblob                        | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| **Sieć Web**                    |           |                          |                          |                             |           |           |           |
| azure_rm_appserviceplan                     | -            | -                         | -          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_appserviceplan_info               | -            | -                         | -          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_webapp                             | -            | -                         | -          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_webapp_info                       | -            | -                         | -          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_webappslot                         | -            | -                         | -          | -            | Tak          | Tak          | Tak          |
| **Kontenery**                    |           |                          |                          |                            |           |           |          |
| azure_rm_acs                                | Tak          | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_aks                                | -            | -                           | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_aks_info                          | -            | -                           | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_aksversion_info                   | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_containerinstance                  | -            | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_containerinstance_info            | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_containerregistry                  | -            | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_containerregistry_info            | -            | -                           | -            | Tak          | Tak          | Tak          | Tak          |
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | -          | -          | Tak          |
| azure_rm_containerregistryreplication_info  | -            | -                           | -            | -            | -          | -          | Tak          |
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | -          | -          | Tak          |
| azure_rm_containerregistrywebhook_info      | -            | -                           | -            | -            | -          | -          | Tak          |
| **Bazy danych**                    |           |                          |                          |                             |           |           |          |
| azure_rm_cosmosdbaccount                    | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_cosmosdbaccount_info              | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_mariadbconfiguration               | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_mariadbconfiguration_info         | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_mariadbdatabase                    | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_mariadbdatabase_info              | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_mariadbfirewallrule                | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_mariadbfirewallrule_info          | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_mariadbserver                      | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_mariadbserver_info                | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_mysqlconfiguration_info           | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_mysqldatabase                      | -            | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_mysqldatabase_info                | -            | -                           | -            | Tak          | Tak          | Tak          | Tak          |
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_mysqlfirewallrule_info            | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_mysqlserver                        | -            | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_mysqlserver_info                  | -            | -                           | -            | Tak          | Tak          | Tak          | Tak          |
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_postgresqlconfiguration_info      | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_postgresqldatabase                 | -            | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_postgresqldatabase_info           | -            | -                           | -            | Tak          | Tak          | Tak          | Tak          |
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_postgresqlfirewallrule_info       | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_postgresqlserver                   | -            | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_postgresqlserver_info             | -            | -                           | -            | Tak          | Tak          | Tak          | Tak          |
| azure_rm_rediscache                         | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_rediscache_info                   | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_rediscachefirewallrule             | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_sqldatabase                        | -            | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_sqldatabase_info                  | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_sqlelasticpool                    | -            | -                           | -            | -            | -          | -          | Tak          |
| azure_rm_sqlelasticpool_info               | -            | -                           | -            | -            | -          | -          | Tak          |
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | Tak          | Tak          | Tak          | Tak          |
| azure_rm_sqlfirewallrule_info              | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_sqlserver                          | -            | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_sqlserver_info                    | -            | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| **Analiza**                    |           |                          |                          |                             |           |           |          |
| azure_rm_hdinsightcluster                   | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_hdinsightcluster_info              | -            | -                           | -            | -            | -            | Tak          | Tak          |
| **Integracja**                    |           |                          |                          |                             |           |           |          |
| azure_rm_servicebus                         | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_servicebus_info                   | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_servicebusqueue                    | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_servicebussaspolicy                | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_servicebustopic                    | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_servicebustopicsubscription        | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| **Zabezpieczenia**                    |           |                          |                          |                             |           |           |           |
| azure_rm_keyvault                           | -            | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_keyvault_info                     | -            | -                           | -              | -          | Tak          | Tak          | Tak          |
| azure_rm_keyvaultkey                        | -            | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_keyvaultkey_info                   | -            | -                           | -            | -            | -            | Tak          | Tak          |
| azure_rm_keyvaultsecret                     | -            | Tak                         | Tak          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_roleassignment                     | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_roleassignment_info               | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_roledefinition                     | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_roledefinition_info               | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| **DevOps**               |           |                          |                          |                             |           |           |                  |
| azure_rm_devtestlab                         | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_devtestlab_info                   | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_devtestlabarmtemplate_info        | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_devtestlabartifact_info           | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_devtestlabartifactsource           | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_devtestlabartifactsource_info     | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_devtestlabcustomimage              | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_devtestlabcustomimage_info         | -            | -                           | -            | -            | -            | Tak          | Tak          |
| azure_rm_devtestlabenvironment              | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_devtestlabenvironment_info         | -            | -                           | -            | -            | -            | Tak          | Tak          |
| azure_rm_devtestlabpolicy                   | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_devtestlabpolicy_info              | -            | -                           | -            | -            | -            | Tak          | Tak          |
| azure_rm_devtestlabschedule                 | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_devtestlabschedule_info            | -            | -                           | -            | -            | -            | Tak          | Tak          |
| azure_rm_devtestlabvirtualmachine           | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_devtestlabvirtualmachine_info | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_devtestlabvirtualnetwork           | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_devtestlabvirtualnetwork_info     | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| **Azure Monitor**                           |              |                           |            |              |                 |           |              |
| azure_rm_autoscale                          | -            | -                         | -          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_autoscale_info                    | -            | -                         | -          | Tak          | Tak          | Tak          | Tak          |
| azure_rm_loganalyticsworkspace              | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_loganalyticsworkspace_info        | -            | -                           | -            | -            | Tak          | Tak          | Tak          |
| azure_rm_monitorlogprofile                  | -            | -                           | -            | -            | -            | Tak          | Tak          |
| **Zarządzanie i ład**     |              |                           |            |            |            |            |              |
| azure_rm_automationaccount        | -            | -                         | -          | -          | -          | Tak        | Tak          |
| azure_rm_automationaccount_info   | -            | -                         | -          | -          | -          | Tak        | Tak          |
| azure_rm_lock                     | -            | -                         | -          | -          | -          | Tak        | Tak          |
| azure_rm_lock_info                | -            | -                         | -          | -          | -          | Tak        | Tak          |
| **Internet rzeczy**            |              |                           |            |            |            |            |              |
| azure_rm_iotdevice                | -            | -                         | -          | -          | -          | Tak        | Tak          |
| azure_rm_iotdevice_info           | -            | -                         | -          | -          | -          | Tak        | Tak          |
| azure_rm_iotdevicemodule          | -            | -                         | -          | -          | -          | Tak        | Tak          |
| azure_rm_iothub_info              | -            | -                         | -          | -          | -          | Tak        | Tak          |
| azure_rm_iothub_info              | -            | -                         | -          | -          | -          | Tak        | Tak          |
| azure_rm_iothubconsumergroup      | -            | -                         | -          | -          | -          | Tak        | Tak          |

## <a name="introduction-to-playbook-role-for-azure"></a>Wprowadzenie do roli podręcznika dla platformy Azure

[Rola azure_preview_module playbook](https://galaxy.ansible.com/Azure/azure_preview_modules/) zawiera wszystkie najnowsze moduły platformy Azure. Aktualizacje i poprawki błędów są wykonywane w bardziej terminowy sposób niż oficjalne wydanie Ansible. Jeśli używasz Ansible do celów inicjowania obsługi administracyjnej `azure_preview_module` zasobów platformy Azure, zachęcamy do zainstalowania roli podręcznika.

Rola `azure_preview_module` podręcznika jest wydana co trzy tygodnie.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat ról [podręcznika, zobacz Tworzenie podręczników wielokrotnego użytku](https://docs.ansible.com/ansible/latest/playbooks_reuse.html). 