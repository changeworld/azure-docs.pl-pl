---
title: Samouczek — Konfigurowanie kolejki w usłudze Azure Service Bus za pomocą rozwiązania Ansible | Dokumentacja firmy Microsoft
description: Dowiedz się, jak za pomocą rozwiązania Ansible, można utworzyć kolejki usługi Azure Service Bus
keywords: ansible, azure, devops, bash, playbook, service bus, queue
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: a73519e2a51e0f8847e262e315f3c13057eb2ac9
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2019
ms.locfileid: "63767173"
---
# <a name="tutorial-configure-queues-in-azure-service-bus-using-ansible"></a>Samouczek: Konfigurowanie kolejki w usłudze Azure Service Bus za pomocą rozwiązania Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Tworzenie kolejki
> * Utwórz plicy sygnatury dostępu Współdzielonego
> * Pobieranie informacji dotyczących przestrzeni nazw
> * Pobieranie informacji z kolejki
> * Odwołaj kolejki zasad sygnatury dostępu Współdzielonego

## <a name="prerequisites"></a>Wymagania wstępne

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-queue"></a>Tworzenie kolejki usługi Service Bus

Element playbook przykładowy kod umożliwia utworzenie następujących zasobów:
- Grupa zasobów platformy Azure
- Przestrzeń nazw magistrali usług w grupie zasobów
- Kolejki usługi Service Bus z przestrzenią nazw

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

Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

```bash
ansible-playbook servicebus_queue.yml
```

## <a name="create-the-sas-policy"></a>Tworzenie zasad sygnatury dostępu Współdzielonego

A [sygnatury dostępu współdzielonego (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) mechanizm autoryzacji opartej na oświadczeniach przy użyciu tokenów. 

Przykładowy kod zawarty w podręczniku dotyczącym tworzy dwie zasady sygnatury dostępu Współdzielonego dla kolejki usługi Service Bus przy użyciu różnych uprawnień.

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

Przed uruchomieniem elementu playbook, zobacz następujące uwagi:
- `rights` Wartość reprezentuje uprawnień, użytkownik ma z kolejką. Określ jedną z następujących wartości: `manage`, `listen`, `send`, lub `listen_send`.

Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

```bash
ansible-playbook servicebus_queue_policy.yml
```

## <a name="retrieve-namespace-information"></a>Pobieranie informacji dotyczących przestrzeni nazw

Przykładowy kod zawarty w podręczniku dotyczącym kwerendy informacji dotyczących przestrzeni nazw.

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

Przed uruchomieniem elementu playbook, zobacz następujące uwagi:
- `show_sas_policies` Wartość wskazuje, czy ma być wyświetlana zasady sygnatury dostępu Współdzielonego w ramach określonego obszaru nazw. Domyślna wartość to `False` w celu uniknięcia obciążenie dodatkowe sieci.

Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

```bash
ansible-playbook servicebus_namespace_info.yml
```

## <a name="retrieve-queue-information"></a>Pobieranie informacji z kolejki

Przykładowe zapytania kod zawarty w podręczniku dotyczącym kolejki informacji. 

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

Przed uruchomieniem elementu playbook, zobacz następujące uwagi:
- `show_sas_policies` Wartość wskazuje, czy ma być wyświetlana zasady sygnatury dostępu Współdzielonego w ramach określonej kolejki. Domyślnie ta wartość jest równa `False` w celu uniknięcia obciążenie dodatkowe sieci.

Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

```bash
ansible-playbook servicebus_queue_info.yml
```

## <a name="revoke-the-queue-sas-policy"></a>Odwołaj kolejki zasad sygnatury dostępu Współdzielonego

Przykładowy kod zawarty w podręczniku dotyczącym Usuwa kolejkę zasad sygnatury dostępu Współdzielonego.

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

Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

```bash
ansible-playbook servicebus_queue_policy_delete.yml
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, Usuń zasoby utworzone w tym artykule. 

Zapisz poniższy kod jako `cleanup.yml`:

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

Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"] 
> [Samouczek: Konfigurowanie tematu w usłudze Azure Service Bus za pomocą rozwiązania Ansible](/azure/ansible/ansible-service-bus-create-topics)