---
title: Samouczek — Konfigurowanie pamięci podręcznych dla pamięci podręcznej Redis za pomocą rozwiązania Ansible w pamięć podręczna systemu Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzenie, skalowanie i ponowne uruchomienie usługi Azure Cache dla pamięci podręcznej Redis za pomocą rozwiązania Ansible
keywords: ansible, azure, devops, bash, playbook, cache, redis
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 693b042f631044352eaae708905fefca02f38415
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231019"
---
# <a name="tutorial-configure-caches-in-azure-cache-for-redis-using-ansible"></a>Samouczek: Konfigurowanie pamięci podręcznych dla pamięci podręcznej Redis za pomocą rozwiązania Ansible w pamięć podręczna systemu Azure

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[Pamięć podręczna systemu Azure dla usługi Redis](/azure/azure-cache-for-redis/) jest usługą zgodne typu open source, która umożliwia tworzenie szybko reagujących aplikacji dzięki zapewnieniu szybkiego dostępu do danych. 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Tworzenie pamięci podręcznej
> * Skalowanie pamięci podręcznej
> * Uruchom ponownie pamięć podręczną
> * Dodaj regułę zapory do pamięci podręcznej
> * Usuń pamięć podręczną

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-cache"></a>Tworzenie pamięci podręcznej

Tworzenie pamięci podręcznej Azure dla usługi Redis, w ramach nowej grupy zasobów.

```yml
  - name: Create resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"

  - name: Create Azure Cache for Redis
    azure_rm_rediscache:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
      sku:
        name: basic
        size: C1 
```

Może potrwać kilka minut, aby uaktywnić pamięć podręczną. Poniższy kod zawiera rozwiązania Ansible, poczekaj na zakończenie operacji:

```yml
  - name: Wait for Redis provisioning to complete
    azure_rm_rediscache_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
    register: facts
    until: "{{ facts.rediscaches[0]['provisioning_state'] == 'Succeeded' }}"
    retries: 100
    delay: 60
```

Podczas procesu inicjowania obsługi administracyjnej długich pojawi się kilka wiadomości "error". Te komunikaty można bezpiecznie zignorować. Ważne wiadomości jest ostatni komunikat. W poniższym przykładzie istnieje wiele komunikaty o błędach aż do końcowego komunikatu ("ok").

```Output
FAILED - RETRYING: Get facts (100 retries left).
FAILED - RETRYING: Get facts (99 retries left).
FAILED - RETRYING: Get facts (98 retries left).
FAILED - RETRYING: Get facts (97 retries left).
FAILED - RETRYING: Get facts (96 retries left).
FAILED - RETRYING: Get facts (95 retries left).
FAILED - RETRYING: Get facts (94 retries left).
FAILED - RETRYING: Get facts (93 retries left).
FAILED - RETRYING: Get facts (92 retries left).
FAILED - RETRYING: Get facts (91 retries left).
ok: [localhost]
```

## <a name="scale-the-cache"></a>Skalowanie pamięci podręcznej

Pamięć podręczna systemu Azure dla usługi Redis zawiera oferty pamięci podręcznej różne w zależności od potrzeb aplikacji. Te opcje pamięci podręcznej stanowią elastyczności w wyborze funkcji i rozmiar pamięci podręcznej. W przypadku zmiany wymagań aplikacji po utworzeniu pamięci podręcznej, można skalować pamięć podręczną, zgodnie z potrzebami. Aby uzyskać więcej informacji na temat skalowania, zobacz [jak skalowanie pamięci podręcznej Azure dla usługi Redis](/azure/azure-cache-for-redis/cache-how-to-scale).

Następujący przykładowy kod jest skalowana w pamięci podręcznej w celu **standardowa**:

```yml
- name: Scale up Azure Cache for Redis
    azure_rm_rediscache:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
      sku:
        name: standard
        size: C1
```

Może potrwać kilka minut, Skalowanie pamięci podręcznej. Poniższy kod zawiera rozwiązania Ansible, poczekaj na zakończenie operacji:

```yml
  - name: Wait for Redis scaling up to complete
    azure_rm_rediscache_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
    register: facts
    until: "{{ facts.rediscaches[0]['provisioning_state'] == 'Succeeded' }}"
    retries: 100
    delay: 60
```

Podobnie jak zadania, aby aprowizować pamięć podręczna systemu Azure dla usługi Redis, dane wyjściowe podobne do następującego komunikatu jest normalne:

```Ouput
**FAILED - RETRYING: Get facts (100 retries left)** is normal.
```

## <a name="reboot-the-cache"></a>Ponowny rozruch z pamięci podręcznej

Poniższy kod jest wykonywany ponowny pamięci podręcznej, utworzony w poprzednich sekcjach.

```yml
  - name: Reboot Azure Cache for Redis
    azure_rm_rediscache:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
      reboot:
        reboot_type: all
```

### <a name="add-firewall-rule"></a>Dodawanie reguły zapory

Poniższy kod dodaje regułę zapory pamięci podręcznej:

```yml
  - name: Add Firewall rule
    azure_rm_rediscachefirewallrule:
      resource_group: "{{ resource_group }}"
      cache_name: "{{ redis_name }}"
      name: rule1
      start_ip_address: 168.1.1.1
      end_ip_address: 168.1.1.4
```

## <a name="delete-the-cache"></a>Usuń pamięć podręczną

Poniższy kod usuwa pamięci podręcznej:

```yml
  - name: Delete Azure Cache for Redis
    azure_rm_rediscache:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
      state: absent
```

## <a name="get-the-sample-playbook"></a>Pobierz Podręcznik próbki

Istnieją dwa sposoby pobrania Podręcznik pełny przykład:
- [Pobierz Podręcznik](https://github.com/Azure-Samples/ansible-playbooks/blob/master/rediscache.yml) i zapisać go w celu `rediscache.yml`.
- Utwórz nowy plik o nazwie `rediscache.yml` i skopiuj do niego następującą zawartość:

```yml
- name: Manage Azure Cache for Redis
  hosts: localhost
  connection: local
  vars:
    resource_group: "{{ resource_group_name }}"
    redis_name: "redis{{ resource_group_name }}"
    location: eastus2

  roles:
    - azure.azure_preview_modules

  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"

  - name: Create Azure Cache for Redis
    azure_rm_rediscache:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
      sku:
        name: basic
        size: C1

  - name: Wait for Redis provisioning to complete
    azure_rm_rediscache_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
    register: facts
    until: "{{ facts.rediscaches[0]['provisioning_state'] == 'Succeeded' }}"
    retries: 100
    delay: 60

  - name: Scale up Azure Cache for Redis
    azure_rm_rediscache:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
      sku:
        name: standard
        size: C1

  - name: Wait for Redis scaling up to complete
    azure_rm_rediscache_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
    register: facts
    until: "{{ facts.rediscaches[0]['provisioning_state'] == 'Succeeded' }}"
    retries: 100
    delay: 60

  - name: Reboot Azure Cache for Redis
    azure_rm_rediscache:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
      reboot:
        reboot_type: all

  - name: Add Firewall rule
    azure_rm_rediscachefirewallrule:
      resource_group: "{{ resource_group }}"
      cache_name: "{{ redis_name }}"
      name: rule1
      start_ip_address: 168.1.1.1
      end_ip_address: 168.1.1.4

  - name: Delete Azure Cache for Redis
    azure_rm_rediscache:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
      state: absent
```

## <a name="run-the-sample-playbook"></a>Uruchamianie elementu playbook próbki

W tej sekcji Uruchamianie elementu playbook, aby przetestować różne funkcje przedstawione w tym artykule.

W `vars` sekcji i Zastąp `{{ resource_group_name }}` nazwą grupy zasobów.

Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

```bash
ansible-playbook rediscache.yml
```

Dane wyjściowe wyglądają podobnie do następujących wyników:

```Output
TASK [create resource group] 
Tuesday 12 March 2019  16:21:07 +0800 (0:00:00.054)       0:00:01.503 
ok: [localhost]

TASK [Create Azure Cache for Redis] 
Tuesday 12 March 2019  16:21:09 +0800 (0:00:01.950)       0:00:03.454 
 [WARNING]: Azure API profile latest does not define an entry for RedisManagementClient

changed: [localhost]

TASK [Dump host name] 
Tuesday 12 March 2019  16:21:49 +0800 (0:00:40.125)       0:00:43.580 
ok: [localhost] =>
  output['host_name']: redis0312.redis.cache.windows.net

TASK [Get facts] 
Tuesday 12 March 2019  16:21:49 +0800 (0:00:00.056)       0:00:43.636 
 [WARNING]: conditional statements should not include jinja2 templating delimiters such as {{ }} or {% %}. Found: {{ facts.rediscaches[0]['provisioning_state'] == 'Succeeded' }}

FAILED - RETRYING: Get facts (100 retries left).
FAILED - RETRYING: Get facts (99 retries left).
FAILED - RETRYING: Get facts (98 retries left).
FAILED - RETRYING: Get facts (97 retries left).
FAILED - RETRYING: Get facts (96 retries left).
FAILED - RETRYING: Get facts (95 retries left).
FAILED - RETRYING: Get facts (94 retries left).
FAILED - RETRYING: Get facts (93 retries left).
FAILED - RETRYING: Get facts (92 retries left).
FAILED - RETRYING: Get facts (91 retries left).
FAILED - RETRYING: Get facts (90 retries left).
ok: [localhost]

TASK [Scale up Azure Cache for Redis] 
Tuesday 12 March 2019  16:33:20 +0800 (0:11:31.296)       0:12:14.933 
changed: [localhost]

TASK [Get facts] 
Tuesday 12 March 2019  16:33:29 +0800 (0:00:09.164)       0:12:24.097 
 [WARNING]: conditional statements should not include jinja2 templating delimiters such as {{ }} or {% %}. Found: {{ facts.rediscaches[0]['provisioning_state'] == 'Succeeded' }}

FAILED - RETRYING: Get facts (100 retries left).
FAILED - RETRYING: Get facts (99 retries left).
FAILED - RETRYING: Get facts (98 retries left).
FAILED - RETRYING: Get facts (97 retries left).
FAILED - RETRYING: Get facts (96 retries left).
FAILED - RETRYING: Get facts (95 retries left).
FAILED - RETRYING: Get facts (94 retries left).
FAILED - RETRYING: Get facts (93 retries left).
FAILED - RETRYING: Get facts (92 retries left).
FAILED - RETRYING: Get facts (91 retries left).
ok: [localhost]

TASK [Reboot Azure Cache for Redis] 
Tuesday 12 March 2019  16:43:57 +0800 (0:10:27.740)       0:22:51.838 
ok: [localhost]

TASK [Add Firewall rule] 
Tuesday 12 March 2019  16:44:02 +0800 (0:00:05.432)       0:22:57.271 
changed: [localhost]

TASK [Delete Azure Cache for Redis] 
Tuesday 12 March 2019  16:44:08 +0800 (0:00:05.137)       0:23:02.409 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=10   changed=4    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

Tuesday 12 March 2019  16:44:14 +0800 (0:00:06.217)       0:23:08.626 

```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, Usuń zasoby utworzone w tym artykule. 

Zapisz poniższy kod jako `cleanup.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: "{{ resource_group_name }}"
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        state: absent
```

W `vars` sekcji i Zastąp `{{ resource_group_name }}` nazwą grupy zasobów.

Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"] 
> [Rozwiązanie Ansible na platformie Azure](https://docs.microsoft.com/azure/ansible/)