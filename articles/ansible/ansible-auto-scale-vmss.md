---
title: Samouczek — Skalowanie automatyczne zestawów skalowania maszyn wirtualnych na platformie Azure przy użyciu rozwiązania ansible
description: Dowiedz się, jak skalować zestawy skalowania maszyn wirtualnych przy użyciu funkcji automatycznego skalowania na platformie Azure
keywords: ansible, azure, devops, bash, playbook, scale, autoscale, virtual machine, virtual machine scale set, vmss
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 784cb532c11b16c820336ceeaf8d38f0225c832f
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242104"
---
# <a name="tutorial-autoscale-virtual-machine-scale-sets-in-azure-using-ansible"></a>Samouczek: automatyczne skalowanie zestawów skalowania maszyn wirtualnych na platformie Azure przy użyciu rozwiązania ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

Funkcja automatycznego dostosowywania liczby wystąpień maszyn wirtualnych jest nazywana [autoskalowaniem](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview). Zaletą automatycznego skalowania jest zmniejszenie kosztów związanych z zarządzaniem w celu monitorowania i optymalizowania wydajności aplikacji. Skalowanie automatyczne można skonfigurować w odpowiedzi na żądanie lub zgodnie ze zdefiniowanym harmonogramem. Za pomocą rozwiązania ansible można określić reguły automatycznego skalowania, które definiują akceptowalną wydajność dla pozytywnego doświadczenia klienta.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Definiowanie profilu skalowania automatycznego
> * Automatyczne skalowanie na podstawie harmonogramu cyklicznego
> * Automatyczne skalowanie na podstawie wydajności aplikacji
> * Pobierz informacje o ustawieniach automatycznego skalowania 
> * Wyłączanie ustawienia automatycznego skalowania

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]

## <a name="autoscale-based-on-a-schedule"></a>Automatyczne skalowanie na podstawie harmonogramu

Aby włączyć skalowanie automatyczne na zestawie skalowania, najpierw zdefiniuj profil skalowania automatycznego. Ten profil obejmuje definiowanie domyślnej, minimalnej i maksymalnej pojemności zestawu skalowania. Dzięki tym limitom możesz kontrolować koszty, ponieważ wystąpienia maszyn wirtualnych nie są tworzone w sposób ciągły, zaś akceptowalna wydajność jest zrównoważona z minimalną liczbą wystąpień, które pozostają w zdarzeniu skalowania w pionie. 

Rozwiązania ansible umożliwia skalowanie zestawów skalowania w określonym dniu lub cyklicznym harmonogramie.

Kod element PlayBook w tej sekcji zwiększa liczbę wystąpień maszyn wirtualnych do trzech o 10:00 co poniedziałek.

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

Uruchom element PlayBook za pomocą polecenia `ansible-playbook`:

```bash
ansible-playbook vmss-auto-scale.yml
```

## <a name="autoscale-based-on-performance-data"></a>Automatyczne skalowanie na podstawie danych wydajności

Wraz ze wzrostem zapotrzebowania aplikacji zwiększa się obciążenie wystąpień maszyn wirtualnych w zestawach skalowania. Jeśli wzrost obciążenia ma cechy stałego trendu, można skonfigurować reguły skalowania automatycznego umożliwiające zwiększenie liczby wystąpień maszyn wirtualnych w zestawie skalowania. Gdy aplikacje zostaną wdrożone, zestaw skalowania rozpoczyna kierowanie ruchu do nowo utworzonych wystąpień maszyn wirtualnych za pośrednictwem modułu równoważenia obciążenia. Rozwiązania ansible umożliwia kontrolowanie metryk, które mają być monitorowane, takich jak użycie procesora CPU, użycie dysku i czas ładowania aplikacji. Można skalować zestawy skalowania w poziomie i skalować je na podstawie progów metryk wydajności, cyklicznego harmonogramu lub w określonym dniu. 

Kod element PlayBook w tej sekcji sprawdza obciążenie procesora CPU przez poprzednie 10 minut o 18:00 co poniedziałek. 

W oparciu o metryki procentu procesora CPU element PlayBook wykonuje jedną z następujących akcji:

- Skalowanie liczby wystąpień maszyn wirtualnych do czterech
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

Uruchom element PlayBook za pomocą polecenia `ansible-playbook`:

```bash
ansible-playbook vmss-auto-scale-metrics.yml
```

## <a name="get-autoscale-settings-information"></a>Pobierz informacje o ustawieniach automatycznego skalowania 

Kod element PlayBook w tej sekcji używa modułu `azure_rm_autoscale_facts` do pobierania szczegółów ustawienia skalowania automatycznego.

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

Uruchom element PlayBook za pomocą polecenia `ansible-playbook`:

```bash
ansible-playbook vmss-auto-scale-get-settings.yml
```

## <a name="disable-autoscale-settings"></a>Wyłącz ustawienia skalowania automatycznego

Istnieją dwa sposoby wyłączenia ustawień skalowania automatycznego. Jednym ze sposobów jest zmiana klucza `enabled` z `true` na `false`. Drugi sposób polega na usunięciu tego ustawienia.

Kod element PlayBook w tej sekcji usuwa ustawienie skalowania automatycznego. 

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

Uruchom element PlayBook za pomocą polecenia `ansible-playbook`:

```bash
vmss-auto-scale-delete-setting.yml
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Samouczek: aktualizowanie niestandardowego obrazu zestawów skalowania maszyn wirtualnych platformy Azure za pomocą rozwiązania ansible](./ansible-vmss-update-image.md)