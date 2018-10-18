---
title: Tworzenie usługi Azure Web Apps za pomocą rozwiązania Ansible (wersja zapoznawcza)
description: Dowiedz się, jak za pomocą rozwiązania Ansible utworzyć aplikację internetową w języku Java 8 korzystającą ze środowiska uruchomieniowego kontenera Tomcat w ramach usługi App Service w systemie Linux
ms.service: ansible
keywords: ansible, azure, devops, bash, element playbook, Azure App Service, aplikacja internetowa, Java
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2018
ms.openlocfilehash: 1899b1fc1e0a38d859fb3a7ce2153585579650f3
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2018
ms.locfileid: "47586715"
---
# <a name="create-azure-app-service-web-apps-using-ansible-preview"></a>Tworzenie usługi Web Apps w ramach usługi Azure App Service za pomocą rozwiązania Ansible (wersja zapoznawcza)
[Usługa Web Apps w ramach usługi App Service](https://docs.microsoft.com/azure/app-service/app-service-web-overview) (lub po prostu usługa Web Apps) to usługa do hostowania aplikacji internetowych, interfejsów API REST i zapleczy aplikacji mobilnych. Możesz programować w ulubionym języku — .NET, .NET Core, Java, Ruby, Node.js, PHP lub Python.

Rozwiązanie Ansible umożliwia zautomatyzowanie wdrażania i konfigurowania zasobów w Twoim środowisku. Ten artykuł przedstawia, jak można utworzyć aplikację internetową za pomocą rozwiązania Ansible i środowiska uruchomieniowego języka Java. 

## <a name="prerequisites"></a>Wymagania wstępne
- **Subskrypcja Azure** — jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Rozwiązanie Ansible 2.7 jest wymagane do uruchamiania następujących przykładowych elementów playbook w ramach tego samouczka. Rozwiązanie Ansible 2.7 RC możesz zainstalować, uruchamiając polecenie `sudo pip install ansible[azure]==2.7.0rc2`. Rozwiązanie Ansible 2.7 zostanie wydane w październiku 2018 r. Po tym czasie nie trzeba będzie tutaj określać wersji, ponieważ wersją domyślną będzie wersja 2.7. 

## <a name="create-a-simple-app-service"></a>Tworzenie prostej usługi aplikacji
W tej sekcji przedstawiono przykładowy podręcznik rozwiązania Ansible, który definiuje następujące zasoby:
- Grupę zasobów, do której zostanie wdrożony plan usługi aplikacji i aplikacja internetowa
- Aplikację internetową w języku Java 8 korzystającą ze środowiska uruchomieniowego kontenera Tomcat w ramach usługi App Service w systemie Linux

```
- hosts: localhost
  connection: local
  vars:
    resource_group: myfirstResourceGroup
    webapp_name: myfirstWebApp
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
          name: myappplan
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
Zapisz powyższy element playbook jako plik firstwebapp.yml.

Aby uruchomić element playbook, użyj polecenia **ansible-playbook** w następujący sposób:
```bash
ansible-playbook firstwebapp.yml
```

Dane wyjściowe z uruchomienia elementu playbook rozwiązania Ansible pokazują, że aplikacja internetowa została utworzona pomyślnie:

```
TASK [Create a resource group] *************************************************
changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] ******************************
 [WARNING]: Azure API profile latest does not define an entry for
WebSiteManagementClient
changed: [localhost]

PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=2    unreachable=0    failed=0   
```

## <a name="create-app-service-with-traffic-manager"></a>Tworzenie usługi App Service z usługą Traffic Manager
Usługi [Azure Traffic Manager](https://docs.microsoft.com/azure/app-service/web-sites-traffic-manager) możesz użyć do kontrolowania dystrybucji żądań klientów internetowych kierowanych do aplikacji w usłudze Azure App Service. Gdy punkty końcowe usługi App Service są dodawane do profilu usługi Azure Traffic Manager, usługa Azure Traffic Manager śledzi stan aplikacji usługi App Service (uruchomiona, zatrzymana lub usunięta), dzięki czemu można zdecydować, który z tych punktów końcowych powinien odbierać ruch.

W usłudze App Service aplikacje są uruchamiane w ramach [planu usługi App Service](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview
). Plan usługi App Service definiuje zestaw zasobów obliczeniowych dla aplikacji internetowej używanych podczas jej uruchamiania. Planem usługi App Service i aplikacją internetową można zarządzać w różnych grupach.

W tej sekcji przedstawiono przykładowy podręcznik rozwiązania Ansible, który definiuje następujące zasoby:
- Grupę zasobów, do której zostanie wdrożony plan usługi aplikacji
- Plan usługi App Service
- Pomocnicza grupa zasobów, do której zostanie wdrożona aplikacja internetowa
- Aplikację internetową w języku Java 8 korzystającą ze środowiska uruchomieniowego kontenera Tomcat w ramach usługi App Service w systemie Linux
- Profil usługi Traffic Manager
- Punkt końcowy usługi Traffic Manager używający utworzonej witryny internetowej

```
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    plan_resource_group: planResourceGroup
    app_name: myLinuxWebApp
    location: eastus
    linux_plan_name: myAppServicePlan
    traffic_manager_profile_name: myTrafficManagerProfile
    traffic_manager_endpoint_name: myTrafficManagerEndpoint

  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

  - name: Create secondary resource group
    azure_rm_resourcegroup:
        name: "{{ plan_resource_group }}"
        location: "{{ location }}"

  - name: Create App Service Plan
    azure_rm_appserviceplan:
      resource_group: "{{ plan_resource_group }}"
      name: "{{ linux_plan_name }}"
      location: "{{ location }}"
      is_linux: true
      sku: S1
      number_of_workers: 1

  - name: Create App Service on Linux with Java Runtime
    azure_rm_webapp:
        resource_group: "{{ resource_group }}"
        name: "{{ app_name }}"
        plan:
          resource_group: "{{ plan_resource_group }}"
          name: "{{ linux_plan_name }}"
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
      resource_group: "{{ resource_group }}"
      name: "{{ app_name }}"
    register: webapp
    
  - name: Create Traffic Manager Profile
    azure_rm_trafficmanagerprofile:
      resource_group: "{{ resource_group }}"
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
      resource_group: "{{ resource_group }}"
      profile_name: "{{ traffic_manager_profile_name }}"
      name: "{{ traffic_manager_endpoint_name }}"
      type: azure_endpoints
      location: "{{ location }}"
      target_resource_id: "{{ webapp.webapps[0].id }}"

```
Zapisz powyższy element playbook jako plik webapp.yml lub [pobierz element playbook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/webapp.yml).

Aby uruchomić element playbook, użyj polecenia **ansible-playbook** w następujący sposób:
```bash
ansible-playbook webapp.yml
```

Dane wyjściowe będące wynikiem uruchomienia elementu playbook rozwiązania Ansible określają, że plan usługi aplikacji, aplikacja internetowa, profil usługi Traffic Manager i punkt końcowy zostały utworzone pomyślnie:
```
TASK [Create resource group] ****************************************************************************
changed: [localhost]

TASK [Create resource group for app service plan] ****************************************************************************
changed: [localhost]

TASK [Create App Service Plan] ****************************************************************************
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] ****************************************************************************
changed: [localhost]

TASK [Get web app facts] *****************************************************************************
ok: [localhost]

TASK [Create Traffic Manager Profile] *****************************************************************************
 [WARNING]: Azure API profile latest does not define an entry for TrafficManagerManagementClient

changed: [localhost]

TASK [Add endpoint to traffic manager profile, using the web site created above] *****************************************************************************
changed: [localhost]

TASK [Get Traffic Manager Profile facts] ******************************************************************************
ok: [localhost]

PLAY RECAP ******************************************************************************
localhost                  : ok=9    changed=6    unreachable=0    failed=0
```

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"] 
> [Rozwiązanie Ansible na platformie Azure](https://docs.microsoft.com/azure/ansible/)