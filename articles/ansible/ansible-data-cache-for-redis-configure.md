---
title: Samouczek — Konfigurowanie pamięci podręcznych w usłudze Azure cache for Redis za pomocą rozwiązania ansible
description: Dowiedz się, jak używać rozwiązania ansible do tworzenia, skalowania, ponownego uruchamiania i dodawania reguły zapory do usługi Azure cache dla Redis
keywords: rozwiązania ansible, Azure, DevOps, bash, element PlayBook, cache, Redis
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 95b42bc9539c36a533eb528c3c26ade89e99290b
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614366"
---
# <a name="tutorial-configure-caches-in-azure-cache-for-redis-using-ansible"></a>Samouczek: Konfigurowanie pamięci podręcznych w usłudze Azure cache for Redis za pomocą rozwiązania ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[Pamięć podręczna platformy Azure dla Redis](/azure/azure-cache-for-redis/) to usługa zgodna z systemem Open Source, która umożliwia tworzenie aplikacji, które są w sposób szybki dostęp do danych. 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Tworzenie pamięci podręcznej
> * Skalowanie pamięci podręcznej
> * Ponowne uruchamianie pamięci podręcznej
> * Dodawanie reguły zapory do pamięci podręcznej
> * Usuwanie pamięci podręcznej

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-cache"></a>Tworzenie pamięci podręcznej

Utwórz pamięć podręczną platformy Azure dla usługi Redis w ramach nowej grupy zasobów.

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

Inicjowanie obsługi administracyjnej pamięci podręcznej może potrwać kilka minut. Poniższy kod instruuje rozwiązania ansible, aby poczekać na zakończenie operacji:

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

Podczas długotrwałego procesu aprowizacji zostanie wyświetlonych kilka komunikatów o błędach. Te komunikaty można bezpiecznie zignorować. Ważna wiadomość jest ostatnim komunikatem. W poniższym przykładzie istnieje wiele komunikatów o błędach do momentu końcowego komunikatu ("OK").

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

Usługa Azure cache for Redis ma różne oferty pamięci podręcznej w zależności od potrzeb aplikacji. Te opcje pamięci podręcznej zapewniają elastyczność w wyborze rozmiaru i funkcji pamięci podręcznej. Jeśli wymagania dotyczące aplikacji zmieniają się po utworzeniu pamięci podręcznej, można skalować pamięć podręczną stosownie do potrzeb. Aby uzyskać więcej informacji na temat skalowania, zobacz [Jak skalować pamięć podręczną platformy Azure dla Redis](/azure/azure-cache-for-redis/cache-how-to-scale).

Następujący przykładowy kod skaluje pamięć podręczną do warstwy **standardowa**:

```yml
- name: Scale up Azure Cache for Redis
    azure_rm_rediscache:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
      sku:
        name: standard
        size: C1
```

Skalowanie pamięci podręcznej może potrwać kilka minut. Poniższy kod instruuje rozwiązania ansible, aby poczekać na zakończenie operacji:

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

Podobnie jak w przypadku zadania inicjowania obsługi usługi Azure cache for Redis, dane wyjściowe podobne do następującego:

```Ouput
**FAILED - RETRYING: Get facts (100 retries left)** is normal.
```

## <a name="reboot-the-cache"></a>Ponowne uruchamianie pamięci podręcznej

Poniższy kod wykonuje ponowny rozruch pamięci podręcznej utworzonej w poprzednich sekcjach.

```yml
  - name: Reboot Azure Cache for Redis
    azure_rm_rediscache:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
      reboot:
        reboot_type: all
```

### <a name="add-firewall-rule"></a>Dodaj regułę zapory

Poniższy kod dodaje regułę zapory do pamięci podręcznej:

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

Poniższy kod usuwa pamięć podręczną:

```yml
  - name: Delete Azure Cache for Redis
    azure_rm_rediscache:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
      state: absent
```

## <a name="get-the-sample-playbook"></a>Pobierz przykładową element PlayBook

Istnieją dwa sposoby uzyskania kompletnej przykładowej element PlayBook:
- [Pobierz element PlayBook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/rediscache.yml) i Zapisz go w `rediscache.yml`.
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

## <a name="run-the-sample-playbook"></a>Uruchamianie przykładowej element PlayBook

W tej sekcji należy uruchomić element PlayBook w celu przetestowania różnych funkcji przedstawionych w tym artykule.

W sekcji `vars` Zamień symbol zastępczy `{{ resource_group_name }}` na nazwę grupy zasobów.

Uruchom element PlayBook przy użyciu polecenia `ansible-playbook`:

```bash
ansible-playbook rediscache.yml
```

Dane wyjściowe wyglądają podobnie do następujących:

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

Gdy nie jest już potrzebne, Usuń zasoby utworzone w tym artykule. 

Zapisz następujący kod jako `cleanup.yml`:

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

W sekcji `vars` Zamień symbol zastępczy `{{ resource_group_name }}` na nazwę grupy zasobów.

Uruchom element PlayBook przy użyciu polecenia `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Rozwiązanie Ansible na platformie Azure](https://docs.microsoft.com/azure/ansible/)