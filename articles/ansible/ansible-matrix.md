---
title: Macierz modułu i wersji Ansible dla platformy Azure
description: Macierz modułu i wersji Ansible dla platformy Azure
ms.service: ansible
keywords: ansible, ról, macierzy, wersja, azure, opracowywania oprogramowania
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 03/25/2018
ms.topic: article
ms.openlocfilehash: 011cb173ffdecc7a22c2e470209719ccaf6bda58
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
ms.locfileid: "30834150"
---
# <a name="ansible-module-and-version-matrix"></a>Macierz Ansible modułu i wersji

## <a name="ansible-modules-for-azure"></a>Moduły Ansible dla platformy Azure
Ansible jest dostarczany z modułów, które mogą być wykonywane bezpośrednio na hostach zdalnych lub za pośrednictwem playbooks.
W tym artykule wymieniono modułów Ansible dla platformy Azure, które można udostępnić zasobów chmury Azure, takich jak maszyny wirtualnej, sieci i usługi kontenera. Możesz uzyskać te moduły z oficjalnego wydania Ansible lub następujące role podręcznika dotyczącego opublikowane przez firmę Microsoft.

| Moduł Ansible dla platformy Azure                   |  Ansible 2.4 |  Ansible 2.5 |  Rola podręcznikowym [azure_preview_module](#introduction-to-azurepreviewmodule) | 
|---------------------------------------------|--------------|-----------------------------|-------------------------------------| 
| **Obliczanie**                    |           |                          |                                  | 
| azure_rm_availabilityset                    | Yes          | Yes                         | Yes                                 | 
| azure_rm_availabilityset_facts              | Yes          | Yes                         | Yes                                 | 
| azure_rm_deployment                         | Yes          | Yes                         | Yes                                 | 
| azure_rm_virtualmachine_scaleset_facts      | Yes          | Yes                         | Yes                                 | 
| azure_rm_virtualmachineimage_facts          | Yes          | Yes                         | Yes                                 | 
| azure_rm_resourcegroup                      | Yes          | Yes                         | Yes                                 | 
| azure_rm_resourcegroup_facts                | Yes          | Yes                         | Yes                                 | 
| azure_rm_virtualmachine                     | Yes          | Yes                         | Yes                                 | 
| azure_rm_virtualmachine_extension           | Yes          | Yes                         | Yes                                 | 
| azure_rm_virtualmachine_scaleset            | Yes          | Yes                         | Yes                                 | 
| azure_rm_image                              |              | Yes                         | Yes                                 | 
| **Sieć**                    |           |                          |                                  | 
| azure_rm_virtualnetwork                     | Yes          | Yes                         | Yes                                 | 
| azure_rm_virtualnetwork_facts               | Yes          | Yes                         | Yes                                 | 
| azure_rm_subnet                             | Yes          | Yes                         | Yes                                 | 
| azure_rm_networkinterface                   | Yes          | Yes                         | Yes                                 | 
| azure_rm_networkinterface_facts             | Yes          | Yes                         | Yes                                 | 
| azure_rm_publicipaddress                    | Yes          | Yes                         | Yes                                 | 
| azure_rm_publicipaddress_facts              | Yes          | Yes                         | Yes                                 | 
| azure_rm_dnsrecordset                       | Yes          | Yes                         | Yes                                 | 
| azure_rm_dnsrecordset_facts                 | Yes          | Yes                         | Yes                                 | 
| azure_rm_dnszone                            | Yes          | Yes                         | Yes                                 | 
| azure_rm_dnszone_facts                      | Yes          | Yes                         | Yes                                 | 
| azure_rm_loadbalancer                       | Yes          | Yes                         | Yes                                 | 
| azure_rm_loadbalancer_facts                 | Yes          | Yes                         | Yes                                 | 
| azure_rm_appgw                              | -            | -                           | Yes                                 | 
| azure_rm_appgwroute                         | -            | -                           | Yes                                 | 
| azure_rm_appgwroute                         | -            | -                           | Yes                                 |
| azure_rm_appgwroute_facts                   | -            | -                           | Yes                                 |
| azure_rm_appgwroutetable                    | -            | -                           | Yes                                 |
| azure_rm_securitygroup                      | Yes          | Yes                         | Yes                                 | 
| azure_rm_appgwroutetable_facts              | -            | -                           | Yes                                 | 
| **Storage**                    |           |                          |                                  | 
| azure_rm_storageaccount                     | Yes          | Yes                         | Yes                                 | 
| azure_rm_storageaccount_facts               | Yes          | Yes                         | Yes                                 | 
| azure_rm_storageblob                        | Yes          | Yes                         | Yes                                 | 
| azure_rm_managed_disk                       | Yes          | Yes                         | Yes                                 | 
| azure_rm_managed_disk_facts                 | Yes          | Yes                         | Yes                                 | 
| **Kontenery**                    |           |                          |                                  | 
| azure_rm_acs                                | Yes          | Yes                         | Yes                                 | 
| azure_rm_containerinstance                  | -            | Yes                         | Yes                                 | 
| azure_rm_containerinstance_facts            | -            | -                           | Yes                                 | 
| azure_rm_containerregistry                  | -            | Yes                         | Yes                                 | 
| azure_rm_containerregistry_facts            | -            | -                           | Yes                                 | 
| azure_rm_containerregistryreplication       | -            | -                           | Yes                                 | 
| azure_rm_containerregistryreplication_facts | -            | -                           | Yes                                 | 
| azure_rm_containerregistrywebhook           | -            | -                           | Yes                                 | 
| azure_rm_containerregistrywebhook_facts     | -            | -                           | Yes                                 | 
| **Azure Functions**                    |           |                          |                                  | 
| azure_rm_functionapp                        | Yes          | Yes                         | Yes                                 | 
| azure_rm_functionapp_facts                  | Yes          | Yes                         | Yes                                 | 
| **Bazy danych**                    |           |                          |                                  | 
| azure_rm_sqlserver                          | -            | Yes                         | Yes                                 | 
| azure_rm_sqlserver_facts                    | -            | Yes                         | Yes                                 | 
| azure_rm_sqldatabase                        | -            | Yes                         | Yes                                 | 
| azure_rm_sqldatabase_facts                  | -            | -                           | Yes                                 | 
| azure_rm_sqlelasticpool                     | -            | -                           | Yes                                 | 
| azure_rm_sqlelasticpool_facts               | -            | -                           | Yes                                 | 
| azure_rm_sqlfirewallrule                    | -            | -                           | Yes                                 | 
| azure_rm_sqlfirewallrule_facts              | -            | -                           | Yes                                 | 
| azure_rm_mysqlserver                        | -            | Yes                         | Yes                                 | 
| azure_rm_mysqlserver_facts                  | -            | -                           | Yes                                 | 
| azure_rm_mysqldatabase                      | -            | Yes                         | Yes                                 | 
| azure_rm_mysqldatabase_facts                | -            | -                           | Yes                                 | 
| azure_rm_mysqlfirewallrule                  | -            | -                           | Yes                                 | 
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | Yes                                 | 
| azure_rm_mysqlconfiguration                 | -            | -                           | Yes                                 | 
| azure_rm_mysqlconfiguration_facts           | -            | -                           | Yes                                 | 
| azure_rm_postgresqlserver                   | -            | Yes                         | Yes                                 | 
| azure_rm_postgresqlserver_facts             | -            | -                           | Yes                                 | 
| azure_rm_postgresqldatabase                 | -            | Yes                         | Yes                                 | 
| azure_rm_postgresqldatabase_facts           | -            | -                           | Yes                                 | 
| azure_rm_postgresqlfirewallrule             | -            | -                           | Yes                                 | 
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | Yes                                 | 
| azure_rm_postgresqlconfiguration            | -            | -                           | Yes                                 | 
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | Yes                                 | 
| **Usługa Key Vault**                    |           |                          |                                  | 
| azure_rm_keyvault                           | -            | Yes                         | Yes                                 |
| azure_rm_keyvault_facts                     | -            | -                           | Yes                                 |
| azure_rm_keyvaultkey                        | -            | Yes                         | Yes                                 |
| azure_rm_keyvaultsecret                     | -            | Yes                         | Yes                                 |


## <a name="introduction-to-playbook-role-for-azure"></a>Wprowadzenie do podręcznika dotyczącego roli dla platformy Azure
[Azure_preview_module podręcznikowym roli](https://galaxy.ansible.com/Azure/azure_preview_modules/) jest najbardziej kompleksowe roli i zawiera najnowsze Azure modułów. Aktualizacje i poprawki są wykonywane w sposób terminowy więcej niż oficjalnego wydania Ansible. Jeśli używasz Ansible dla celów udostępniania zasobów platformy Azure, możesz zaleca się zainstalowanie roli azure_preview_module.

Rola podręcznika dotyczącego azure_preview_module zwolnieniu co trzy tygodnie.

## <a name="next-steps"></a>Kolejne kroki
Więcej informacji dotyczących ról podręcznika dotyczącego odwoływać się do [tworzenie Playbooks wielokrotnego użytku](http://docs.ansible.com/ansible/latest/playbooks_reuse.html). 
