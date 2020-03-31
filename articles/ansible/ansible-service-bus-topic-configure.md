---
title: Samouczek — konfigurowanie tematów w usłudze Azure Service Bus przy użyciu ansible
description: Dowiedz się, jak utworzyć temat usługi Azure Service Bus za pomocą ansible
keywords: ansible, azure, devops, bash, playbook, service bus, tematy, subskrypcje
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: d07c7622043353a79d5a82994c2fab4f0835b453
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74155797"
---
# <a name="tutorial-configure-topics-in-azure-service-bus-using-ansible"></a>Samouczek: Konfigurowanie tematów w usłudze Azure Service Bus przy użyciu ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Tworzenie tematu
> * Tworzenie subskrypcji
> * Tworzenie zasad sygnatury dostępu Współdzielonego
> * Pobieranie informacji o obszarze nazw
> * Pobieranie informacji o temacie i subskrypcji
> * Odwoływanie zasad sygnatury dostępu Współdzielonego

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-topic"></a>Tworzenie tematu Usługi Service Bus

Przykładowy kod podręcznika tworzy następujące zasoby:
- Grupa zasobów platformy Azure
- Obszar nazw usługi Service Bus w grupie zasobów
- Temat Usługi Service Bus z obszarem nazw

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

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook servicebus_topic.yml
```

## <a name="create-the-subscription"></a>Tworzenie subskrypcji

Przykładowy kod podręcznika tworzy subskrypcję w temacie usługi Service Bus. Tematy usługi Azure Service Bus mogą mieć wiele subskrypcji. Subskrybent tematu może odbierać kopię każdej wiadomości wysłanej do tematu. Subskrypcje są nazwane jednostki, które są trwale tworzone, ale opcjonalnie może wygasnąć.

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

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook servicebus_subscription.yml
```

## <a name="create-the-sas-policy"></a>Tworzenie zasad sygnatury dostępu Współdzielonego

[Sygnatura dostępu współdzielonego (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) to mechanizm autoryzacji oparty na oświadczeniach przy użyciu tokenów. 

Przykładowy kod podręcznika tworzy dwie zasady sygnatury dostępu Współdzielonego dla kolejki usługi Service Bus z różnymi uprawnieniami.

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

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook servicebus_topic_policy.yml
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

## <a name="retrieve-topic-and-subscription-information"></a>Pobieranie informacji o temacie i subskrypcji

Przykładowy kod podręcznika pyta o następujące informacje:
- Informacje o temacie usługi Service Bus
- Lista szczegółów subskrypcji dla tematu
 
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

Przed uruchomieniem podręcznika zapoznaj się z następującymi uwagami:
- Wartość `show_sas_policies` wskazuje, czy mają być wyświetlane zasady sygnatury dostępu Współdzielonego w określonej kolejce. Domyślnie ta wartość jest `False` ustawiona, aby uniknąć dodatkowych narzutów sieciowych.

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook servicebus_list.yml
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

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook servicebus_topic_policy_delete.yml
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

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"] 
> [Rozwiązanie Ansible na platformie Azure](/azure/ansible/)