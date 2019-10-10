---
title: Samouczek — Konfigurowanie tematów w Azure Service Bus przy użyciu rozwiązania ansible
description: Informacje dotyczące tworzenia Azure Service Bus tematu przy użyciu programu rozwiązania ansible
keywords: rozwiązania ansible, Azure, DevOps, bash, element PlayBook, Service Bus, tematy, subskrypcje
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 952779db582f9437f10608bf86b0b80560ded2c0
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241217"
---
# <a name="tutorial-configure-topics-in-azure-service-bus-using-ansible"></a>Samouczek: Konfigurowanie tematów w Azure Service Bus przy użyciu rozwiązania ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Tworzenie tematu
> * Tworzenie subskrypcji
> * Tworzenie zasad sygnatury dostępu współdzielonego
> * Pobierz informacje o przestrzeni nazw
> * Pobierz informacje o temacie i subskrypcji
> * Odwoływanie zasad SAS

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-topic"></a>Tworzenie tematu Service Bus

Przykładowy kod element PlayBook tworzy następujące zasoby:
- Grupa zasobów platformy Azure
- Service Bus przestrzeni nazw w grupie zasobów
- Service Bus tematu z przestrzenią nazw

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

Uruchom element PlayBook za pomocą polecenia `ansible-playbook`:

```bash
ansible-playbook servicebus_topic.yml
```

## <a name="create-the-subscription"></a>Utwórz subskrypcję

Przykładowy kod element PlayBook tworzy subskrypcję w temacie Service Bus. Tematy Azure Service Bus mogą mieć wiele subskrypcji. Subskrybent tematu może odbierać kopię każdej wiadomości wysyłanej do tematu. Subskrypcje są nazwanymi jednostkami, które zostały utworzone przez trwale, ale mogą opcjonalnie wygasnąć.

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

Uruchom element PlayBook za pomocą polecenia `ansible-playbook`:

```bash
ansible-playbook servicebus_subscription.yml
```

## <a name="create-the-sas-policy"></a>Tworzenie zasad SAS

[Sygnatura dostępu współdzielonego (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) jest mechanizmem autoryzacji opartym na oświadczeniach przy użyciu tokenów. 

Przykładowy kod element PlayBook tworzy dwie zasady sygnatury dostępu współdzielonego dla kolejki Service Bus z różnymi uprawnieniami.

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

Uruchom element PlayBook za pomocą polecenia `ansible-playbook`:

```bash
ansible-playbook servicebus_topic_policy.yml
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

Uruchom element PlayBook za pomocą polecenia `ansible-playbook`:

```bash
ansible-playbook servicebus_namespace_info.yml
```

## <a name="retrieve-topic-and-subscription-information"></a>Pobierz informacje o temacie i subskrypcji

Przykładowy kod element PlayBook zapytania dla następujących informacji:
- Informacje dotyczące Service Bus tematu
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

Przed uruchomieniem element PlayBook zapoznaj się z następującymi uwagami:
- Wartość `show_sas_policies` wskazuje, czy mają być pokazywane zasady sygnatury dostępu współdzielonego w ramach określonej kolejki. Domyślnie ta wartość jest ustawiona na `False`, aby uniknąć dodatkowych obciążeń sieci.

Uruchom element PlayBook za pomocą polecenia `ansible-playbook`:

```bash
ansible-playbook servicebus_list.yml
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

Uruchom element PlayBook za pomocą polecenia `ansible-playbook`:

```bash
ansible-playbook servicebus_topic_policy_delete.yml
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

Uruchom element PlayBook za pomocą polecenia `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"] 
> [Rozwiązanie Ansible na platformie Azure](/azure/ansible/)