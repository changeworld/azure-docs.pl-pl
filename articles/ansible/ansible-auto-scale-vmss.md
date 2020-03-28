---
title: Samouczek — skalowanie automatyczne zestawów skalowania maszyn na platformie Azure przy użyciu ansible
description: Dowiedz się, jak używać ansible do skalowania zestawów skalowania maszyny wirtualnej za pomocą skalowania automatycznego na platformie Azure
keywords: ansible, azure, devops, bash, playbook, scale, autoscale, virtual machine, virtual machine scale set, vmss
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: fb8d2a4bfca32be4575ca8f11018e5cab17cd9a2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74156820"
---
# <a name="tutorial-autoscale-virtual-machine-scale-sets-in-azure-using-ansible"></a>Samouczek: Automatyczne skalowanie zestawów skalowania maszyn wirtualnych na platformie Azure przy użyciu ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

Funkcja automatycznego dostosowywania liczby wystąpień maszyn wirtualnych nazywana jest [skalowaniem automatycznym](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview). Zaletą skalowania automatycznego jest to, że zmniejsza obciążenie związane z zarządzaniem do monitorowania i optymalizacji wydajności aplikacji. Skalowanie automatyczne można skonfigurować w odpowiedzi na żądanie lub zgodnie ze zdefiniowanym harmonogramem. Za pomocą Ansible można określić reguły skalowania automatycznego, które definiują dopuszczalną wydajność dla pozytywnego doświadczenia klienta.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Definiowanie profilu skalowania automatycznego
> * Skalowanie automatyczne na podstawie harmonogramu cyklicznego
> * Skalowanie automatyczne na podstawie wydajności aplikacji
> * Pobieranie informacji o ustawieniach skalowania automatycznego 
> * Wyłączanie ustawienia skalowania automatycznego

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]

## <a name="autoscale-based-on-a-schedule"></a>Skalowanie automatyczne na podstawie harmonogramu

Aby włączyć skalowanie automatyczne na zestawie skalowania, najpierw zdefiniuj profil skalowania automatycznego. Ten profil obejmuje definiowanie domyślnej, minimalnej i maksymalnej pojemności zestawu skalowania. Dzięki tym limitom możesz kontrolować koszty, ponieważ wystąpienia maszyn wirtualnych nie są tworzone w sposób ciągły, zaś akceptowalna wydajność jest zrównoważona z minimalną liczbą wystąpień, które pozostają w zdarzeniu skalowania w pionie. 

Ansible umożliwia skalowanie zestawów skalowania według określonej daty lub harmonogramu cyklicznego.

Kod podręcznika w tej sekcji zwiększa liczbę wystąpień maszyn wirtualnych do trzech o 10:00 w każdy poniedziałek.

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

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook vmss-auto-scale.yml
```

## <a name="autoscale-based-on-performance-data"></a>Skalowanie automatyczne na podstawie danych dotyczących wydajności

Wraz ze wzrostem zapotrzebowania aplikacji zwiększa się obciążenie wystąpień maszyn wirtualnych w zestawach skalowania. Jeśli wzrost obciążenia ma cechy stałego trendu, można skonfigurować reguły skalowania automatycznego umożliwiające zwiększenie liczby wystąpień maszyn wirtualnych w zestawie skalowania. Gdy aplikacje zostaną wdrożone, zestaw skalowania rozpoczyna kierowanie ruchu do nowo utworzonych wystąpień maszyn wirtualnych za pośrednictwem modułu równoważenia obciążenia. Ansible pozwala kontrolować, jakie metryki do monitorowania, takie jak użycie procesora CPU, użycie dysku i czas ładowania aplikacji. Można skalować i skalować w poziomie w zestawach skalowania na podstawie progów metryki wydajności, harmonogramu cyklicznego lub określonej daty. 

Kod podręcznika w tej sekcji sprawdza obciążenie procesora CPU dla poprzednich 10 minut o godzinie 18:00 w każdy poniedziałek. 

Na podstawie metryk procentowych procesora CPU podręcznik wykonuje jedną z następujących czynności:

- Skaluje liczbę wystąpień maszyn wirtualnych do czterech
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

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook vmss-auto-scale-metrics.yml
```

## <a name="get-autoscale-settings-information"></a>Uzyskaj informacje o ustawieniach skalowania automatycznego 

Kod podręcznika w tej sekcji `azure_rm_autoscale_facts` używa modułu do pobierania szczegółów ustawienia skalowania automatycznego.

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

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook vmss-auto-scale-get-settings.yml
```

## <a name="disable-autoscale-settings"></a>Wyłączanie ustawień skalowania automatycznego

Istnieją dwa sposoby wyłączania ustawień skalowania automatycznego. Jednym ze sposobów `enabled` jest `true` zmiana `false`klucza z na . Drugim sposobem jest usunięcie ustawienia.

Kod podręcznika w tej sekcji powoduje usunięcie ustawienia skalowania automatycznego. 

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

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
vmss-auto-scale-delete-setting.yml
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Samouczek: Aktualizowanie niestandardowego obrazu zestawów skalowania maszyny wirtualnej platformy Azure przy użyciu ansible](./ansible-vmss-update-image.md)