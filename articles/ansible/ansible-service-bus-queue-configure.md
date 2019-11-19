---
title: Samouczek — Konfigurowanie kolejek w Azure Service Bus przy użyciu rozwiązania ansible
description: Dowiedz się, jak utworzyć kolejkę Azure Service Bus za pomocą rozwiązania ansible
keywords: rozwiązania ansible, Azure, DevOps, bash, element PlayBook, Service Bus, Queue
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 9903419a52be61cd7ec74214858bce81df7e727e
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74155809"
---
# <a name="tutorial-configure-queues-in-azure-service-bus-using-ansible"></a>Samouczek: Konfigurowanie kolejek w Azure Service Bus przy użyciu rozwiązania ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Tworzenie kolejki
> * Tworzenie plicy sygnatury dostępu współdzielonego
> * Pobierz informacje o przestrzeni nazw
> * Pobierz informacje o kolejce
> * Odwołaj zasady dotyczące kolejki SAS

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-queue"></a>Tworzenie kolejki Service Bus

Przykładowy kod element PlayBook tworzy następujące zasoby:
- Grupa zasobów platformy Azure
- Service Bus przestrzeni nazw w grupie zasobów
- Kolejka Service Bus z przestrzenią nazw

Zapisz następujący podręcznik jako `servicebus_queue.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Ensure resource group exist
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
    - name: Create a namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
    - name: Create a queue
      azure_rm_servicebusqueue:
          name: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: queue
    - debug:
          var: queue
```

Uruchom element PlayBook przy użyciu polecenia `ansible-playbook`:

```bash
ansible-playbook servicebus_queue.yml
```

## <a name="create-the-sas-policy"></a>Tworzenie zasad SAS

[Sygnatura dostępu współdzielonego (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) jest mechanizmem autoryzacji opartym na oświadczeniach przy użyciu tokenów. 

Przykładowy kod element PlayBook tworzy dwie zasady sygnatury dostępu współdzielonego dla kolejki Service Bus z różnymi uprawnieniami.

Zapisz następujący podręcznik jako `servicebus_queue_policy.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen priviledge
      azure_rm_servicebussaspolicy:
          name: "{{ queue }}-policy"
          queue: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          rights: listen_send
      register: policy
    - debug:
          var: policy
```

Przed uruchomieniem element PlayBook zapoznaj się z następującymi uwagami:
- Wartość `rights` reprezentuje uprawnienie, które użytkownik dysponuje z kolejką. Określ jedną z następujących wartości: `manage`, `listen`, `send`lub `listen_send`.

Uruchom element PlayBook przy użyciu polecenia `ansible-playbook`:

```bash
ansible-playbook servicebus_queue_policy.yml
```

## <a name="retrieve-namespace-information"></a>Pobierz informacje o przestrzeni nazw

Przykładowy kod element PlayBook wysyła zapytanie do informacji o przestrzeni nazw.

Zapisz następujący podręcznik jako `servicebus_namespace_info.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
  tasks:
    - name: Get a namespace's information
      azure_rm_servicebus_facts:
          type: namespace
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: ns
    - debug:
          var: ns
```

Przed uruchomieniem element PlayBook zapoznaj się z następującymi uwagami:
- Wartość `show_sas_policies` wskazuje, czy mają być wyświetlane zasady sygnatury dostępu współdzielonego w ramach określonego obszaru nazw. Domyślnie wartość jest `False`, aby uniknąć dodatkowych obciążeń sieci.

Uruchom element PlayBook przy użyciu polecenia `ansible-playbook`:

```bash
ansible-playbook servicebus_namespace_info.yml
```

## <a name="retrieve-queue-information"></a>Pobierz informacje o kolejce

Przykładowy kod element PlayBook zapytań informacje o kolejce. 

Zapisz następujący podręcznik jako `servicebus_queue_info.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Get a queue's information
      azure_rm_servicebus_facts:
          type: queue
          name: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: queue
    - debug:
          var: queue
```

Przed uruchomieniem element PlayBook zapoznaj się z następującymi uwagami:
- Wartość `show_sas_policies` wskazuje, czy mają być wyświetlane zasady sygnatury dostępu współdzielonego w ramach określonej kolejki. Domyślnie ta wartość jest ustawiana na `False`, aby uniknąć dodatkowych obciążeń sieci.

Uruchom element PlayBook przy użyciu polecenia `ansible-playbook`:

```bash
ansible-playbook servicebus_queue_info.yml
```

## <a name="revoke-the-queue-sas-policy"></a>Odwołaj zasady dotyczące kolejki SAS

Przykładowy kod element PlayBook usuwa zasady sygnatury dostępu współdzielonego kolejki.

Zapisz następujący podręcznik jako `servicebus_queue_policy_delete.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen priviledge
      azure_rm_servicebussaspolicy:
          name: "{{ queue }}-policy"
          queue: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
```

Uruchom element PlayBook przy użyciu polecenia `ansible-playbook`:

```bash
ansible-playbook servicebus_queue_policy_delete.yml
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, Usuń zasoby utworzone w tym artykule. 

Zapisz następujący kod jako `cleanup.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Delete queue
      azure_rm_servicebusqueue:
          name: "{{ queue }}"
          resource_group: "{{ resource_group }}"
          namespace: "{{ namespace }}"
          state: absent
    - name: Delete namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
    - name: Delete resource group
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          state: absent
          force_delete_nonempty: yes
```

Uruchom element PlayBook przy użyciu polecenia `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"] 
> [Samouczek: Konfigurowanie tematu w Azure Service Bus przy użyciu rozwiązania ansible](ansible-service-bus-topic-configure.md)