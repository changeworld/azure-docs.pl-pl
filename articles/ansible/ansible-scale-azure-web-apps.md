---
title: Samouczek — skalowanie aplikacji w usłudze Azure App Service przy użyciu ansible
description: Dowiedz się, jak skalować aplikację w usłudze Azure App Service
keywords: ansible, azure, devops, bash, playbook, Azure App Service, Web App, scale, Java
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 9eb50922361c817de8047dece4849a9b221677f0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74155912"
---
# <a name="tutorial-scale-apps-in-azure-app-service-using-ansible"></a>Samouczek: skalowanie aplikacji w usłudze Azure App Service przy użyciu ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-app-service.md](../../includes/open-source-devops-intro-app-service.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Pobieranie faktów dotyczących istniejącego planu usługi App Service
> * Skalowanie planu usługi app service do S2 z trzema pracownikami

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **Aplikacja usługi Azure App Service** — jeśli nie masz aplikacji usługi Azure App Service, [skonfiguruj aplikację w usłudze Azure App Service przy użyciu funkcji Ansible.](ansible-create-configure-azure-web-apps.md)

## <a name="scale-up-an-app"></a>Skalowanie w górę aplikacji

Istnieją dwa przepływy pracy do skalowania: *skalowanie w górę* i *skalowanie w poziomie*.

**Skalowanie w górę:** Skalowanie w górę oznacza pozyskanie większej ilości zasobów. Zasoby te obejmują procesor, pamięć, miejsce na dysku, maszyny wirtualne i inne. Skalowanie w górę aplikacji przez zmianę warstwy cenowej planu usługi app service, do którego należy aplikacja. 
**Skalowanie w poziomie:** Skalowanie w poziomie oznacza zwiększenie liczby wystąpień maszyn wirtualnych, które uruchamiają aplikację. W zależności od warstwy cenowej planu usługi App Service można skalować w poziomie do 20 wystąpień. [Skalowanie automatyczne](/azure/azure-monitor/platform/autoscale-get-started) umożliwia automatyczne skalowanie liczby wystąpień na podstawie wstępnie zdefiniowanych reguł i harmonogramów.

Kod podręcznika w tej sekcji definiuje następującą operację:

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

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook webapp_scaleup.yml
```

Po uruchomieniu podręcznika zobaczysz dane wyjściowe podobne do następujących wyników:

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

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Rozwiązanie Ansible na platformie Azure](/azure/ansible/)