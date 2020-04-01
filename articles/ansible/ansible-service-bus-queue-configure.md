---
title: Samouczek — konfigurowanie kolejek w usłudze Azure Service Bus przy użyciu ansible
description: Dowiedz się, jak utworzyć kolejkę usługi Azure Service Bus za pomocą ansible
keywords: ansible, azure, devops, bash, playbook, autobus usług, kolejka
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 8ba4c2296d903c4f35aa36eb92dfbc3b56ec4b18
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76713219"
---
# <a name="tutorial-configure-queues-in-azure-service-bus-using-ansible"></a>Samouczek: Konfigurowanie kolejek w usłudze Azure Service Bus przy użyciu usługi Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Tworzenie kolejki
> * Tworzenie plicy SAS
> * Pobieranie informacji o obszarze nazw
> * Pobieranie informacji o kolejce
> * Odwoływanie zasad kolejki sygnatury dostępu Współdzielonego

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-queue"></a>Tworzenie kolejki usługi Service Bus

Przykładowy kod podręcznika tworzy następujące zasoby:
- Grupa zasobów platformy Azure
- Obszar nazw usługi Service Bus w grupie zasobów
- Kolejka usługi Service Bus z obszarem nazw

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

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook servicebus_queue.yml
```

## <a name="create-the-sas-policy"></a>Tworzenie zasad sygnatury dostępu Współdzielonego

[Sygnatura dostępu współdzielonego (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) to mechanizm autoryzacji oparty na oświadczeniach przy użyciu tokenów. 

Przykładowy kod podręcznika tworzy dwie zasady sygnatury dostępu Współdzielonego dla kolejki usługi Service Bus z różnymi uprawnieniami.

Zapisz następujący podręcznik jako `servicebus_queue_policy.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen privilege
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

Przed uruchomieniem podręcznika zapoznaj się z następującymi uwagami:
- Wartość `rights` reprezentuje uprawnienia użytkownika z kolejki. Określ jedną z `manage`następujących `listen` `send`wartości: `listen_send`, , , lub .

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook servicebus_queue_policy.yml
```

## <a name="retrieve-namespace-information"></a>Pobieranie informacji o obszarze nazw

Przykładowy kod podręcznika wysyła zapytanie do informacji o obszarze nazw.

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

Przed uruchomieniem podręcznika zapoznaj się z następującymi uwagami:
- Wartość `show_sas_policies` wskazuje, czy mają być wyświetlane zasady sygnatury dostępu Współdzielonego w określonym obszarze nazw. Domyślnie wartość jest `False` uniknięcie dodatkowych narzutów sieciowych.

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook servicebus_namespace_info.yml
```

## <a name="retrieve-queue-information"></a>Pobieranie informacji o kolejce

Przykładowy kod podręcznika wysyła informacje o kolejce. 

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

Przed uruchomieniem podręcznika zapoznaj się z następującymi uwagami:
- Wartość `show_sas_policies` wskazuje, czy mają być wyświetlane zasady sygnatury dostępu Współdzielonego w określonej kolejce. Domyślnie ta wartość jest `False` ustawiona, aby uniknąć dodatkowych narzutów sieciowych.

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook servicebus_queue_info.yml
```

## <a name="revoke-the-queue-sas-policy"></a>Odwoływanie zasad kolejki sygnatury dostępu Współdzielonego

Przykładowy kod podręcznika usuwa zasady sygnatury dostępu Współdzielonego kolejki.

Zapisz następujący podręcznik jako `servicebus_queue_policy_delete.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen privilege
      azure_rm_servicebussaspolicy:
          name: "{{ queue }}-policy"
          queue: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
```

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook servicebus_queue_policy_delete.yml
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, usuń zasoby utworzone w tym artykule. 

Zapisz następujący kod `cleanup.yml`jako:

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

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"] 
> [Samouczek: Konfigurowanie tematu w usłudze Azure Service Bus przy użyciu usługi Ansible](ansible-service-bus-topic-configure.md)