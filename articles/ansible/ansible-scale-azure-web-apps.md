---
title: Samouczek — skalowanie aplikacji w usłudze Azure App Service za pomocą rozwiązania Ansible | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skalować aplikację w usłudze Azure App Service
keywords: ansible, azure, devops, bash, playbook, Azure App Service, Web App, scale, Java
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 213c4e086db8b40fdec26ce9fb3e0be5ad055cbc
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64729432"
---
# <a name="tutorial-scale-apps-in-azure-app-service-using-ansible"></a>Samouczek: Skalowanie aplikacji w usłudze Azure App Service za pomocą rozwiązania Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-app-service.md](../../includes/open-source-devops-intro-app-service.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Pobieranie faktów dotyczących istniejącego planu usługi App Service
> * Skaluj w górę plan usługi App Service do warstwy S2, za pomocą trzech procesów roboczych

## <a name="prerequisites"></a>Wymagania wstępne

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **Aplikacja usługi Azure App Service** — Jeśli masz aplikację usługi Azure App Service [skonfigurować aplikację w usłudze Azure App Service za pomocą rozwiązania Ansible](ansible-create-configure-azure-web-apps.md).

## <a name="scale-up-an-app"></a>Skalowanie aplikacji

Istnieją dwa przepływy pracy na potrzeby skalowania: *skalowanie w górę* i *skalowanie w poziomie*.

**Skalowanie w górę:** Aby skalować w górę oznacza, że uzyskanie większej ilości zasobów. Te zasoby obejmują: procesor CPU, pamięci, miejsca na dysku i maszyn wirtualnych. Skalować aplikację, zmieniając warstwę cenową planu usługi App Service, do którego należy aplikacja. 
**Skalowanie w poziomie:** Skalowanie w poziomie polega na zwiększenie liczby wystąpień maszyn wirtualnych, które Uruchom aplikację. W zależności od warstwy cenowej planu usługi App Service można skalować w poziomie do maksymalnie 20 wystąpień. [Skalowanie automatyczne](/azure/azure-monitor/platform/autoscale-get-started) umożliwia skalowanie liczby wystąpień automatycznie na podstawie wstępnie zdefiniowanych reguł i harmonogramy.

Kod elementu playbook w tej sekcji określa się następujące działania:

* Pobieranie faktów dotyczących istniejącego planu usługi App Service
* Aktualizowanie planu usługi App Service do warstwy S2 z trzema procesami roboczymi

Zapisz następujący podręcznik jako `webapp_scaleup.yml`:

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

Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

```bash
ansible-playbook webapp_scaleup.yml
```

Po uruchomieniu elementu playbook, zobaczysz dane wyjściowe podobne do następujących wyników:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Get facts of existing App service plan] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

ok: [localhost]

TASK [debug] 
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 1,
        "family": "S",
        "name": "S1",
        "size": "S1",
        "tier": "Standard"
    }
}

TASK [Scale up the App service plan] 
changed: [localhost]

TASK [Get facts] 
ok: [localhost]

TASK [debug] 
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 3,
        "family": "S",
        "name": "S2",
        "size": "S2",
        "tier": "Standard"
    }
}

PLAY RECAP 
localhost                  : ok=6    changed=1    unreachable=0    failed=0 
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"] 
> [Rozwiązanie Ansible na platformie Azure](/azure/ansible/)