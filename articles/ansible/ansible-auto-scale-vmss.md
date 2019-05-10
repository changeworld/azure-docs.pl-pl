---
title: Samouczek — automatyczne skalowanie zestawów skalowania maszyn wirtualnych na platformie Azure za pomocą rozwiązania Ansible | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skalować zestawy skalowania maszyn wirtualnych z funkcją automatycznego skalowania na platformie Azure za pomocą rozwiązania Ansible
keywords: ansible, azure, devops, bash, playbook, scale, autoscale, virtual machine, virtual machine scale set, vmss
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 4f2cd66b7460fc6fe48cb55f45bf4bc309ae054c
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231268"
---
# <a name="tutorial-autoscale-virtual-machine-scale-sets-in-azure-using-ansible"></a>Samouczek: Zestawy skalowania maszyn wirtualnych skalowania automatycznego na platformie Azure za pomocą rozwiązania Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

Funkcja automatycznego dostosowania liczby wystąpień maszyn wirtualnych jest nazywana [skalowania automatycznego](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview). Zaletą skalowania automatycznego jest zmniejsza koszty zarządzania do monitorowania i optymalizowania wydajności aplikacji. Skalowanie automatyczne można skonfigurować w odpowiedzi na żądanie lub zgodnie ze zdefiniowanym harmonogramem. Za pomocą rozwiązania Ansible, można określić reguły skalowania automatycznego, które określają akceptowalny poziom wydajności dla pozytywnych komfort.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Definiowanie profilu skalowania automatycznego
> * Automatyczne skalowanie na podstawie harmonogramu cyklicznego
> * Automatyczne skalowanie na podstawie wydajności aplikacji
> * Pobieranie informacji o ustawieniach automatycznego skalowania 
> * Ustawienie automatycznego skalowania

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]

## <a name="autoscale-based-on-a-schedule"></a>Automatyczne skalowanie na podstawie harmonogramu

Aby włączyć skalowanie automatyczne na zestawie skalowania, najpierw zdefiniuj profil skalowania automatycznego. Ten profil obejmuje definiowanie domyślnej, minimalnej i maksymalnej pojemności zestawu skalowania. Dzięki tym limitom możesz kontrolować koszty, ponieważ wystąpienia maszyn wirtualnych nie są tworzone w sposób ciągły, zaś akceptowalna wydajność jest zrównoważona z minimalną liczbą wystąpień, które pozostają w zdarzeniu skalowania w pionie. 

Rozwiązanie Ansible umożliwia skalowanie zestawami skalowania w określonym dniu lub Harmonogram cykliczny.

Kod elementu playbook w tej sekcji zwiększa liczbę wystąpień maszyn wirtualnych do trzech o 10:00 w każdy poniedziałek.

Zapisz następujący podręcznik jako `vmss-auto-scale.yml`:

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    name: autoscalesetting
  tasks: 
    - name: Create autoscaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         target:
           namespace: "Microsoft.Compute"
           types: "virtualMachineScaleSets"
           name: "{{ vmss_name }}"
         enabled: true
         profiles:
         - count: '3'
           min_count: '3'
           max_count: '3'
           name: Auto created scale condition
           recurrence_timezone: Pacific Standard Time
           recurrence_frequency: Week
           recurrence_days:
              - Monday
           recurrence_mins:
              - '0'
           recurrence_hours:
              - '10'
```

Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

```bash
ansible-playbook vmss-auto-scale.yml
```

## <a name="autoscale-based-on-performance-data"></a>Automatyczne skalowanie na podstawie danych wydajności

Wraz ze wzrostem zapotrzebowania aplikacji zwiększa się obciążenie wystąpień maszyn wirtualnych w zestawach skalowania. Jeśli wzrost obciążenia ma cechy stałego trendu, można skonfigurować reguły skalowania automatycznego umożliwiające zwiększenie liczby wystąpień maszyn wirtualnych w zestawie skalowania. Gdy aplikacje zostaną wdrożone, zestaw skalowania rozpoczyna kierowanie ruchu do nowo utworzonych wystąpień maszyn wirtualnych za pośrednictwem modułu równoważenia obciążenia. Rozwiązanie Ansible pozwala określić, jakie metryki do monitorowania, takich jak użycie procesora CPU, użycie dysku i czas ładowania aplikacji. Możesz skalować w i skalowania w poziomie w skali ustawia wydajności na podstawie progów metryk, według powtarzającego się harmonogramu lub według określonej daty. 

Kod elementu playbook w tej sekcji wyszukuje poprzednie 10 minut w godzinie 18:00 w każdy poniedziałek obciążenie procesora CPU. 

Oparte na metryki procent procesora CPU, element playbook wykonuje jedną z następujących czynności:

- Można skalować w poziomie liczby wystąpień maszyn wirtualnych, 4
- Skaluje liczbę wystąpień maszyn wirtualnych do jednego

Zapisz następujący podręcznik jako `vmss-auto-scale-metrics.yml`:

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    name: autoscalesetting
  tasks:
  - name: Get facts of the resource group
    azure_rm_resourcegroup_facts:
      name: "{{ resource_group }}"
    register: rg

  - name: Get scale set resource uri
    set_fact:
      vmss_id: "{{ rg.ansible_facts.azure_resourcegroups[0].id }}/providers/Microsoft.Compute/virtualMachineScaleSets/{{ vmss_name }}"
    
  - name: Create autoscaling
    azure_rm_autoscale:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      target: "{{ vmss_id }}"
      enabled: true
      profiles:
      - count: '1'
        max_count: '1'
        min_count: '1'
        name: 'This scale condition is executed when none of the other scale condition(s) match'
        recurrence_days:
          - Monday
        recurrence_frequency: Week
        recurrence_hours:
          - 18
        recurrence_mins:
          - 0
        recurrence_timezone: Pacific Standard Time
      - count: '1'
        min_count: '1'
        max_count: '4'
        name: Auto created scale condition
        recurrence_days:
          - Monday
        recurrence_frequency: Week
        recurrence_hours:
          - 18
        recurrence_mins:
          - 0
        recurrence_timezone: Pacific Standard Time
        rules:
          - cooldown: 5
            direction: Increase
            metric_name: Percentage CPU
            metric_resource_uri: "{{ vmss_id }}"
            operator: GreaterThan
            statistic: Average
            threshold: 70
            time_aggregation: Average
            time_grain: 1
            time_window: 10
            type: ChangeCount
            value: '1'
          - cooldown: 5
            direction: Decrease
            metric_name: Percentage CPU
            metric_resource_uri: "{{ vmss_id }}"
            operator: LessThan
            statistic: Average
            threshold: 30
            time_aggregation: Average
            time_grain: 1
            time_window: 10
            type: ChangeCount
            value: '1'
```

Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

```bash
ansible-playbook vmss-auto-scale-metrics.yml
```

## <a name="get-autoscale-settings-information"></a>Uzyskaj informacje dotyczące ustawień automatycznego skalowania 

Kod elementu playbook w tej sekcji używa `azure_rm_autoscale_facts` modułu można pobrać szczegółów ustawienie skalowania automatycznego.

Zapisz następujący podręcznik jako `vmss-auto-scale-get-settings.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Retrieve autoscale settings information
      azure_rm_autoscale_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ name }}"
      register: autoscale_query
    
    - debug:
        var: autoscale_query.autoscales[0]
```

Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

```bash
ansible-playbook vmss-auto-scale-get-settings.yml
```

## <a name="disable-autoscale-settings"></a>Wyłącz ustawienia automatycznego skalowania

Istnieją dwa sposoby, aby wyłączyć ustawienia automatycznego skalowania. Jednym ze sposobów jest zmiana `enabled` klucza z `true` do `false`. Drugi sposób polega na usunięciu ustawienie.

Kod elementu playbook w tej sekcji usuwa ustawienie skalowania automatycznego. 

Zapisz następujący podręcznik jako `vmss-auto-scale-delete-setting.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Delete autoscaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         state: absent
```

Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

```bash
vmss-auto-scale-delete-setting.yml
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"] 
> [Samouczek: Ustawia obraz niestandardowy aktualizacji skali maszyny wirtualnej platformy Azure, za pomocą rozwiązania Ansible](./ansible-vmss-update-image.md)