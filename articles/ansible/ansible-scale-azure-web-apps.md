---
title: Samouczek — skalowanie aplikacji w Azure App Service przy użyciu rozwiązania ansible
description: Dowiedz się, jak skalować aplikację w górę w Azure App Service
keywords: ansible, azure, devops, bash, playbook, Azure App Service, Web App, scale, Java
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 9eb50922361c817de8047dece4849a9b221677f0
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74155912"
---
# <a name="tutorial-scale-apps-in-azure-app-service-using-ansible"></a>Samouczek: skalowanie aplikacji w Azure App Service przy użyciu rozwiązania ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-app-service.md](../../includes/open-source-devops-intro-app-service.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Pobieranie faktów dotyczących istniejącego planu usługi App Service
> * Skaluj w górę plan App Service do S2 z trzema pracownikami

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **Aplikacja Azure App Service** — Jeśli nie masz aplikacji Azure App Service, [skonfiguruj aplikację w Azure App Service przy użyciu rozwiązania ansible](ansible-create-configure-azure-web-apps.md).

## <a name="scale-up-an-app"></a>Skalowanie aplikacji w górę

Istnieją dwa przepływy pracy do skalowania: *skalowanie w górę* i w *poziomie*.

**Skaluj w górę:** Aby skalować w górę środki w celu uzyskania większej ilości zasobów. Te zasoby obejmują procesor CPU, pamięć, miejsce na dysku, maszyny wirtualne i wiele innych. Możesz skalować aplikację, zmieniając warstwę cenową planu App Service, do którego należy aplikacja. 
**Skalowanie w poziomie:** Aby skalować w poziomie, Zwiększ liczbę wystąpień maszyn wirtualnych, na których działa aplikacja. W zależności od warstwy cenowej planu App Service można skalować w poziomie do maksymalnie 20 wystąpień. [Skalowanie](/azure/azure-monitor/platform/autoscale-get-started) automatyczne umożliwia skalowanie liczby wystąpień automatycznie na podstawie wstępnie zdefiniowanych reguł i harmonogramów.

Kod element PlayBook w tej sekcji definiuje następującą operację:

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

Uruchom element PlayBook przy użyciu polecenia `ansible-playbook`:

```bash
ansible-playbook webapp_scaleup.yml
```

Po uruchomieniu element PlayBook są wyświetlane dane wyjściowe podobne do następujących:

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