---
title: Automatyczne skalowanie zestawu skalowania maszyn wirtualnych na platformie Azure przy użyciu rozwiązania Ansible
description: Dowiedz się, jak za pomocą autoskalowania skalować zestaw skalowania maszyn wirtualnych na platformie Azure przy użyciu rozwiązania Ansible
ms.service: ansible
keywords: ansible, azure, devops, bash, playbook, scale, autoscale, virtual machine, virtual machine scale set, vmss
author: tomarcher
manager: jeconnoc
ms.author: yuwzho, kyliel
ms.topic: tutorial
ms.date: 12/10/2018
ms.openlocfilehash: c6678d6df3a695d3a0471e5779bc3af4b6ba6c84
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2018
ms.locfileid: "53411166"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-azure-using-ansible"></a>Automatyczne skalowanie zestawu skalowania maszyn wirtualnych na platformie Azure przy użyciu rozwiązania Ansible
Rozwiązanie Ansible umożliwia zautomatyzowanie wdrażania i konfigurowania zasobów w Twoim środowisku. Rozwiązanie Ansible umożliwia zarządzanie zestawem skalowania maszyn wirtualnych na platformie Azure, tak jak ma to miejsce w przypadku każdego innego zasobu platformy Azure. 

Podczas tworzenia zestawu skalowania musisz zdefiniować liczbę wystąpień maszyn wirtualnych, które chcesz uruchamiać. W odpowiedzi na zmieniające się zapotrzebowanie aplikacji możesz automatycznie zwiększać lub zmniejszać liczbę wystąpień maszyn wirtualnych. Skalowanie automatyczne pozwala spełniać potrzeby klientów lub reagować na zmiany wydajności aplikacji w całym cyklu jej życia. W tym artykule utworzysz ustawienie autoskalowania i skojarzysz je z istniejącym zestawem skalowania maszyn wirtualnych. W ustawieniu autoskalowania można skonfigurować regułę, aby skalować w poziomie lub pionie, zgodnie z potrzebami.

## <a name="prerequisites"></a>Wymagania wstępne
- **Subskrypcja Azure** — jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]
- Istniejący zestaw skalowania maszyn wirtualnych platformy Azure. — Jeśli go nie masz, [utwórz zestaw skalowania maszyn wirtualnych na platformie Azure przy użyciu rozwiązania Ansible](https://docs.microsoft.com/azure/ansible/ansible-create-configure-vmss).

> [!Note]
> Rozwiązanie Ansible 2.7 jest wymagane do uruchamiania następujących przykładowych elementów playbook w ramach tego samouczka. 

## <a name="auto-scale-based-on-a-schedule"></a>Automatyczne skalowanie na podstawie harmonogramu   
Aby włączyć skalowanie automatyczne na zestawie skalowania, najpierw zdefiniuj profil skalowania automatycznego. Ten profil obejmuje definiowanie domyślnej, minimalnej i maksymalnej pojemności zestawu skalowania. Dzięki tym limitom możesz kontrolować koszty, ponieważ wystąpienia maszyn wirtualnych nie są tworzone w sposób ciągły, zaś akceptowalna wydajność jest zrównoważona z minimalną liczbą wystąpień, które pozostają w zdarzeniu skalowania w pionie. 

W usłudze Virtual Machine Scale Sets można skalować w górę i w dół według powtarzającego się harmonogramu lub według określonej daty. W tej sekcji przedstawiono przykładowy podręcznik rozwiązania Ansible, który tworzy ustawienie autoskalowania zwiększające liczbę wystąpień maszyn wirtualnych w zestawach skalowania do trzech w każdy poniedziałek o godzinie 10:00 czasu pacyficznego. 

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myVMSS
    name: autoscalesetting
  tasks: 
    - name: Create auto scaling
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

Zapisz ten podręcznik jako plik *vmss-auto-scale.yml*. Aby uruchomić podręcznik rozwiązania Ansible, użyj polecenia **ansible-playbook** w następujący sposób:

```bash
ansible-playbook vmss-auto-scale.yml
```

## <a name="auto-scale-based-on-performance-data"></a>Automatyczne skalowanie na podstawie danych wydajności
Wraz ze wzrostem zapotrzebowania aplikacji zwiększa się obciążenie wystąpień maszyn wirtualnych w zestawach skalowania. Jeśli wzrost obciążenia ma cechy stałego trendu, można skonfigurować reguły skalowania automatycznego umożliwiające zwiększenie liczby wystąpień maszyn wirtualnych w zestawie skalowania. Gdy aplikacje zostaną wdrożone, zestaw skalowania rozpoczyna kierowanie ruchu do nowo utworzonych wystąpień maszyn wirtualnych za pośrednictwem modułu równoważenia obciążenia. Możesz określić, jakie metryki mają być monitorowane, na przykład procesor CPU lub dysk, oraz jak długo obciążenie aplikacji musi przekraczać wartość progową, a także ile wystąpień maszyn wirtualnych ma zostać dodanych do zestawu skalowania.

W usłudze Virtual Machine Scale Sets można skalować w górę i w dół na podstawie progów metryk wydajności według powtarzającego się harmonogramu lub według określonej daty. W tej sekcji przedstawiono przykładowy podręcznik rozwiązania Ansible, który sprawdza obciążenie w ciągu ostatnich 10 minut w każdy poniedziałek o godzinie 18:00 czasu pacyficznego i skaluje w poziomie liczbę wystąpień maszyn wirtualnych w zestawach skalowania do czterech lub skaluje w pionie do jednego wystąpienia w zależności od metryk procentowego wykorzystania procesora CPU. 

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myVMSS
    name: autoscalesetting
  tasks:
  - name: Get facts of the resource group
    azure_rm_resourcegroup_facts:
      name: "{{ resource_group }}"
    register: rg

  - name: Get VMSS resource uri
    set_fact:
      vmss_id: "{{ rg.ansible_facts.azure_resourcegroups[0].id }}/providers/Microsoft.Compute/virtualMachineScaleSets/{{ vmss_name }}"
    
  - name: Create auto scaling
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

Zapisz ten podręcznik jako plik *vmss-auto-scale-metrics.yml*. Aby uruchomić podręcznik rozwiązania Ansible, użyj polecenia **ansible-playbook** w następujący sposób:

```bash
ansible-playbook vmss-auto-scale-metrics.yml
```

## <a name="get-information-for-existing-autoscale-settings"></a>Uzyskiwanie informacji dotyczących istniejących ustawień autoskalowania
Szczegóły dotyczące dowolnego ustawienia autoskalowania można uzyskać za pośrednictwem modułu *azure_rm_autoscale_facts* za pomocą podręcznika w następujący sposób:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Retrieve auto scale settings information
      azure_rm_autoscale_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ name }}"
      register: autoscale_query
    
    - debug:
        var: autoscale_query.autoscales[0]
```

## <a name="disable-the-autoscale-settings"></a>Wyłączanie ustawień autoskalowania
Ustawienie automatycznego skalowania można wyłączyć, zmieniając wartość `enabled: true` na wartość `enabled: false` lub usuwając ustawienia automatycznego skalowania za pomocą podręcznika w następujący sposób:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Delete auto scaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         state: absent
```

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"] 
> [Przykładowy podręcznik rozwiązania Ansible dla zestawów skalowania maszyn wirtualnych](https://github.com/Azure-Samples/ansible-playbooks/tree/master/vmss)