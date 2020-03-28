---
title: Samouczek — konfigurowanie aplikacji w usłudze Azure App Service przy użyciu aplikacji Ansible
description: Dowiedz się, jak utworzyć aplikację w usłudze Azure App Service w usłudze Java 8 i czasie działania kontenera Tomcat
keywords: ansible, azure, devops, bash, element playbook, Azure App Service, aplikacja internetowa, Java
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 2891ff47b17900c4c1c8e1c21f22495b65108fd5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74156559"
---
# <a name="tutorial-configure-apps-in-azure-app-service-using-ansible"></a>Samouczek: Konfigurowanie aplikacji w usłudze Azure App Service przy użyciu aplikacji Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-app-service.md](../../includes/open-source-devops-intro-app-service.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Tworzenie aplikacji w usłudze Azure App Service w usłudze Java 8 i czasie wykonywania kontenera Tomcat
> * Tworzenie profilu usługi Azure Traffic Manager
> * Definiowanie punktu końcowego usługi Traffic Manager przy użyciu utworzonej aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-basic-app-service"></a>Tworzenie podstawowej usługi aplikacji

Kod podręcznika w tej sekcji definiuje następujące zasoby:

* Grupa zasobów platformy Azure, w której wdrożono plan i aplikację usługi App Service
* Usługa aplikacji w systemie Linux z oprogramowaniem Java 8 i czasem wykonywania kontenera Tomcat

Zapisz następujący podręcznik jako `firstwebapp.yml`:

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    webapp_name: myfirstWebApp
    plan_name: myAppServicePlan
    location: eastus
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

    - name: Create App Service on Linux with Java Runtime
      azure_rm_webapp:
        resource_group: "{{ resource_group }}"
        name: "{{ webapp_name }}"
        plan:
          resource_group: "{{ resource_group }}"
          name: "{{ plan_name }}"
          is_linux: true
          sku: S1
          number_of_workers: 1
        frameworks:
          - name: "java"
            version: "8"
            settings:
              java_container: tomcat
              java_container_version: 8.5
```

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook firstwebapp.yml
```

Po uruchomieniu podręcznika zobaczysz dane wyjściowe podobne do następujących wyników:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create a resource group] 
changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

PLAY RECAP 
localhost                  : ok=3    changed=2    unreachable=0    failed=0
```

## <a name="create-an-app-and-use-azure-traffic-manager"></a>Tworzenie aplikacji i korzystanie z usługi Azure Traffic Manager

[Usługa Azure Traffic Manager](/azure/app-service/web-sites-traffic-manager) umożliwia kontrolowanie sposobu dystrybucji żądań od klientów sieci web do aplikacji w usłudze Azure App Service. Gdy punkty końcowe usługi App Service zostaną dodane do profilu usługi Azure Traffic Manager, usługa Traffic Manager śledzi stan aplikacji usługi App Service. Obejmuje to następujące stany: uruchomiono, zatrzymano i usunięto. Usługa Traffic Manager służy do decydowania, które punkty końcowe powinny odbierać ruch.

W usłudze App Service aplikacje są uruchamiane w ramach [planu usługi App Service](/azure/app-service/overview-hosting-plans). Plan usługi aplikacji definiuje zestaw zasobów obliczeniowych dla aplikacji do uruchomienia. Planem usługi App Service i aplikacją internetową można zarządzać w różnych grupach.

Kod podręcznika w tej sekcji definiuje następujące zasoby:

* Grupa zasobów platformy Azure, w ramach której wdrożono plan usługi app service
* Plan usługi App Service
* Grupa zasobów platformy Azure, w której aplikacja jest wdrażana
* Usługa aplikacji w systemie Linux z oprogramowaniem Java 8 i czasem wykonywania kontenera Tomcat
* Profil usługi Traffic Manager
* Punkt końcowy Usługi Traffic Manager przy użyciu utworzonej aplikacji

Zapisz następujący podręcznik jako `webapp.yml`:

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group_webapp: myResourceGroupWebapp
    resource_group: myResourceGroup
    webapp_name: myLinuxWebApp
    plan_name: myAppServicePlan
    location: eastus
    traffic_manager_profile_name: myTrafficManagerProfile
    traffic_manager_endpoint_name: myTrafficManagerEndpoint

  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
        name: "{{ resource_group_webapp }}"
        location: "{{ location }}"

  - name: Create secondary resource group
    azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

  - name: Create App Service Plan
    azure_rm_appserviceplan:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
      location: "{{ location }}"
      is_linux: true
      sku: S1
      number_of_workers: 1

  - name: Create App Service on Linux with Java Runtime
    azure_rm_webapp:
        resource_group: "{{ resource_group_webapp }}"
        name: "{{ webapp_name }}"
        plan:
          resource_group: "{{ resource_group }}"
          name: "{{ plan_name }}"
          is_linux: true
          sku: S1
          number_of_workers: 1
        app_settings:
          testkey: "testvalue"
        frameworks:
          - name: java
            version: 8
            settings:
              java_container: "Tomcat"
              java_container_version: "8.5"

  - name: Get web app facts
    azure_rm_webapp_facts:
      resource_group: "{{ resource_group_webapp }}"
      name: "{{ webapp_name }}"
    register: webapp
    
  - name: Create Traffic Manager Profile
    azure_rm_trafficmanagerprofile:
      resource_group: "{{ resource_group_webapp }}"
      name: "{{ traffic_manager_profile_name }}"
      location: global
      routing_method: performance
      dns_config:
        relative_name: "{{ traffic_manager_profile_name }}"
        ttl:  60
      monitor_config:
        protocol: HTTPS
        port: 80
        path: '/'

  - name: Add endpoint to traffic manager profile, using created web site
    azure_rm_trafficmanagerendpoint:
      resource_group: "{{ resource_group_webapp }}"
      profile_name: "{{ traffic_manager_profile_name }}"
      name: "{{ traffic_manager_endpoint_name }}"
      type: azure_endpoints
      location: "{{ location }}"
      target_resource_id: "{{ webapp.webapps[0].id }}"
```

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook webapp.yml
```

Po uruchomieniu podręcznika zobaczysz dane wyjściowe podobne do następujących wyników:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create resource group] 
changed: [localhost]

TASK [Create resource group for app service plan] 
changed: [localhost]

TASK [Create App Service Plan] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] 
changed: [localhost]

TASK [Get web app facts] 
ok: [localhost]

TASK [Create Traffic Manager Profile] 
 [WARNING]: Azure API profile latest does not define an entry for TrafficManagerManagementClient

changed: [localhost]

TASK [Add endpoint to traffic manager profile, using the web site created above] 
changed: [localhost]

TASK [Get Traffic Manager Profile facts] 
ok: [localhost]

PLAY RECAP 
localhost                  : ok=9    changed=6    unreachable=0    failed=0
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Samouczek: skalowanie aplikacji w usłudze Azure App Service przy użyciu ansible](/azure/ansible/ansible-scale-azure-web-apps)