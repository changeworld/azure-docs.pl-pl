---
title: Samouczek — Konfigurowanie tematów w usłudze Azure Service Bus za pomocą rozwiązania Ansible | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć temat usługi Azure Service Bus za pomocą rozwiązania Ansible
keywords: ansible, azure, metodyki devops, bash, element playbook, usługi service bus, tematy i subskrypcje
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: ca8d849796520ac260d888d772c064316db68a30
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65230862"
---
# <a name="tutorial-configure-topics-in-azure-service-bus-using-ansible"></a>Samouczek: Konfiguruj tematy w usłudze Azure Service Bus za pomocą rozwiązania Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Tworzenie tematu
> * Tworzenie subskrypcji
> * Tworzenie zasad sygnatury dostępu Współdzielonego
> * Pobieranie informacji dotyczących przestrzeni nazw
> * Pobieranie informacji z tematu i subskrypcji
> * Odwołaj zasad sygnatury dostępu Współdzielonego

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-topic"></a>Tworzenie tematu usługi Service Bus

Element playbook przykładowy kod umożliwia utworzenie następujących zasobów:
- Grupa zasobów platformy Azure
- Przestrzeń nazw magistrali usług w grupie zasobów
- Tematu usługi Service Bus z przestrzenią nazw

Zapisz następujący podręcznik jako `servicebus_topic.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Ensure resource group exist
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
    - name: Create a namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
    - name: Create a topic
      azure_rm_servicebustopic:
          name: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: topic
    - debug:
          var: topic
```

Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

```bash
ansible-playbook servicebus_topic.yml
```

## <a name="create-the-subscription"></a>Utwórz subskrypcję

Przykładowy kod zawarty w podręczniku dotyczącym umożliwia utworzenie subskrypcji w ramach tematu usługi Service Bus. Tematy usługi Service Bus platformy Azure może mieć wiele subskrypcji. Subskrybenta do tematu może otrzymuje kopię każdy komunikat wysyłany do tematu. Subskrypcje są nazywane jednostek, które trwale są tworzone, ale można opcjonalnie wygasa.

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      topic: servicebustesttopic
      subscription: servicebustestsubs
  tasks:
    - name: Create a subscription
      azure_rm_servicebustopicsubscription:
          name: "{{ subscription }}"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: subs
    - debug:
          var: subs
```

Zapisz następujący podręcznik jako `servicebus_subscription.yml`:

Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

```bash
ansible-playbook servicebus_subscription.yml
```

## <a name="create-the-sas-policy"></a>Tworzenie zasad sygnatury dostępu Współdzielonego

A [sygnatury dostępu współdzielonego (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) mechanizm autoryzacji opartej na oświadczeniach przy użyciu tokenów. 

Przykładowy kod zawarty w podręczniku dotyczącym tworzy dwie zasady sygnatury dostępu Współdzielonego dla kolejki usługi Service Bus przy użyciu różnych uprawnień.

Zapisz następujący podręcznik jako `servicebus_topic_policy.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Create a policy with send and listen privilege
      azure_rm_servicebussaspolicy:
          name: "{{ topic }}-{{ item }}"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          rights: "{{ item }}"
      with_items:
        - send
        - listen
      register: policy
    - debug:
          var: policy
```

Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

```bash
ansible-playbook servicebus_topic_policy.yml
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

## <a name="retrieve-topic-and-subscription-information"></a>Pobieranie informacji z tematu i subskrypcji

Przykładowe zapytania kod zawarty w podręczniku dotyczącym następujące informacje:
- Informacje o temacie usługi Service Bus
- Lista szczegółów subskrypcji tematu
 
Zapisz następujący podręcznik jako `servicebus_list.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Get a topic's information
      azure_rm_servicebus_facts:
          type: topic
          name: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: topic_fact
    - name: "List subscriptions under topic {{ topic }}"
      azure_rm_servicebus_facts:
          type: subscription
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: subs_fact
    - debug:
          var: "{{ item }}"
      with_items:
        - topic_fact.servicebuses[0]
        - subs_fact.servicebuses
```

Przed uruchomieniem elementu playbook, zobacz następujące uwagi:
- `show_sas_policies` Wartość wskazuje, czy ma być wyświetlana zasady sygnatury dostępu Współdzielonego w ramach określonej kolejki. Domyślnie ta wartość jest równa `False` w celu uniknięcia obciążenie dodatkowe sieci.

Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

```bash
ansible-playbook servicebus_list.yml
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
      topic: servicebustesttopic
  tasks:
    - name: Delete a policy
      azure_rm_servicebussaspolicy:
          name: "{{ topic }}-policy"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
```

Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

```bash
ansible-playbook servicebus_topic_policy_delete.yml
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
      topic: servicebustesttopic
      subscription: servicebustestsubs
  tasks:
    - name: Delete subscription
      azure_rm_servicebustopicsubscription:
          name: "{{ subscription }}"
          topic: "{{ topic }}"
          resource_group: "{{ resource_group }}"
          namespace: "{{ namespace }}"
          state: absent
    - name: Delete topic
      azure_rm_servicebustopic:
          name: "{{ topic }}"
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
> [Rozwiązanie Ansible na platformie Azure](/azure/ansible/)