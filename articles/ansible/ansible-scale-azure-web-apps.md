---
title: Skalowanie aplikacji internetowych w ramach usługi Azure App Service za pomocą rozwiązania Ansible
description: Dowiedz się, jak za pomocą rozwiązania Ansible utworzyć aplikację internetową w języku Java 8 korzystającą ze środowiska uruchomieniowego kontenera Tomcat w ramach usługi App Service w systemie Linux
ms.service: azure
keywords: ansible, azure, devops, bash, playbook, Azure App Service, Web App, scale, Java
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 12/08/2018
ms.openlocfilehash: 2bafb73afa35c7670ac45f7027545277c70075ef
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792280"
---
# <a name="scale-azure-app-service-web-apps-by-using-ansible"></a>Skalowanie aplikacji internetowych w ramach usługi Azure App Service za pomocą rozwiązania Ansible
[Funkcja Azure App Service Web Apps](https://docs.microsoft.com/azure/app-service/overview) (lub po prostu Web Apps) hostuje aplikacje internetowe, interfejsy API REST i zaplecze aplikacji mobilnych. Możesz programować w ulubionym języku &mdash; .NET, .NET Core, Java, Ruby, Node.js, PHP lub Python.

Rozwiązanie Ansible umożliwia zautomatyzowanie wdrażania i konfigurowania zasobów w Twoim środowisku. Z tego artykułu dowiesz się, jak używać rozwiązania Ansible w celu skalowania aplikacji w usłudze Azure App Service.

## <a name="prerequisites"></a>Wymagania wstępne
- **Subskrypcja Azure** — jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]
- **Usługa Azure App Service Web Apps** — jeśli nie masz jeszcze aplikacji internetowej usług Azure App Service, możesz [utworzyć aplikacje internetowe platformy Azure za pomocą rozwiązania Ansible](ansible-create-configure-azure-web-apps.md).

## <a name="scale-up-an-app-in-app-service"></a>Skalowanie aplikacji w górę w usłudze App Service
Skalowanie w górę można przeprowadzać, zmieniając warstwę cenową planu usługi App Service, do którego należy aplikacja. W tej sekcji przedstawiono przykładowy element playbook rozwiązania Ansible, który definiuje następującą operację:
- Pobieranie faktów dotyczących istniejącego planu usługi App Service
- Aktualizowanie planu usługi App Service do warstwy S2 z trzema procesami roboczymi

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    plan_name: myAppServicePlan
    location: eastus

  tasks:
  - name: Get facts of existing App service plan
    azure_rm_appserviceplan_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
    register: facts

  - debug: 
      var: facts.appserviceplans[0].sku

  - name: Scale up the App service plan
    azure_rm_appserviceplan:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
      is_linux: true
      sku: S2
      number_of_workers: 3
      
  - name: Get facts
    azure_rm_appserviceplan_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
    register: facts

  - debug: 
      var: facts.appserviceplans[0].sku
```

Zapisz ten element playbook jako *webapp_scaleup.yml*.

Aby uruchomić element playbook, użyj polecenia **ansible-playbook** w następujący sposób:
```bash
ansible-playbook webapp_scaleup.yml
```

Po uruchomieniu elementu playbook dane wyjściowe podobne do poniższego przykładu pokazują, że plan usługi App Service został pomyślnie zaktualizowany do warstwy S2 z trzema procesami roboczymi:
```Output
PLAY [localhost] **************************************************************

TASK [Gathering Facts] ********************************************************
ok: [localhost]

TASK [Get facts of existing App service plan] **********************************************************
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

ok: [localhost]

TASK [debug] ******************************************************************
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 1,
        "family": "S",
        "name": "S1",
        "size": "S1",
        "tier": "Standard"
    }
}

TASK [Scale up the App service plan] *******************************************
changed: [localhost]

TASK [Get facts] ***************************************************************
ok: [localhost]

TASK [debug] *******************************************************************
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 3,
        "family": "S",
        "name": "S2",
        "size": "S2",
        "tier": "Standard"
    }
}

PLAY RECAP **********************************************************************
localhost                  : ok=6    changed=1    unreachable=0    failed=0 
```

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"] 
> [Rozwiązanie Ansible na platformie Azure](https://docs.microsoft.com/azure/ansible/)