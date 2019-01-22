---
title: Uruchamianie rozwiązania Ansible przy użyciu programu Bash w usłudze Azure Cloud Shell
description: Dowiedz się, jak wykonywać różne zadania rozwiązania Ansible przy użyciu programu Bash w usłudze Azure Cloud Shell
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 08/07/2018
ms.topic: quickstart
ms.openlocfilehash: 61b23b5bc2620c82051b0ba1de4bb54b44a495e0
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359191"
---
# <a name="run-ansible-with-bash-in-azure-cloud-shell"></a>Uruchamianie rozwiązania Ansible przy użyciu programu Bash w usłudze Azure Cloud Shell

Z tego samouczka dowiesz się, jak skonfigurować subskrypcję platformy Azure jako obszar roboczy rozwiązania Ansible przy użyciu programu Bash w ramach usługi Cloud Shell. 

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja Azure** — jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Skonfiguruj usługę Azure Cloud Shell** — jeśli jesteś nowym użytkownikiem usługi Azure Cloud Shell, zapoznaj się z artykułem [Szybki start: powłoka Bash w usłudze Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart), w którym pokazano, jak uruchomić i skonfigurować usługę Cloud Shell. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Konfiguracja poświadczeń automatycznych

Rozwiązanie Ansible uwierzytelnia się na platformie Azure po zalogowaniu do usługi Cloud Shell, umożliwiając zarządzanie infrastrukturą bez konieczności przeprowadzania jakiejkolwiek dodatkowej konfiguracji. Jeśli masz więcej niż jedną subskrypcję, możesz wybrać tę, z którą ma współpracować rozwiązanie Ansible, eksportując zmienną środowiskową `AZURE_SUBSCRIPTION_ID`. Aby wyświetlić listę wszystkich swoich subskrypcji platformy Azure, uruchom następujące polecenie:

```azurecli-interactive
az account list
```

Używając **identyfikatora** subskrypcji, z którą chcesz pracować, ustaw parametr **AZURE_SUBSCRIPTION_ID** w następujący sposób:

```azurecli-interactive
export AZURE_SUBSCRIPTION_ID=<your-subscription-id>
```

## <a name="verify-the-configuration"></a>Sprawdzanie konfiguracji
Aby sprawdzić, czy konfiguracja zakończyła się pomyślnie, użyj rozwiązania Ansible w celu utworzenia grupy zasobów.

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-create-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Tworzenie podstawowej maszyny wirtualnej na platformie Azure za pomocą rozwiązania Ansible](/azure/virtual-machines/linux/ansible-create-vm)