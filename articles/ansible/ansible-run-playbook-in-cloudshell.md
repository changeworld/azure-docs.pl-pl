---
title: Przewodnik Szybki Start — elementy playbook rozwiązania Ansible przebiegu, za pośrednictwem funkcji Bash w usłudze Azure Cloud Shell | Dokumentacja firmy Microsoft
description: W tym przewodniku Szybki Start Dowiedz się, jak wykonywać różne zadania Ansible przy użyciu programu Bash w usłudze Azure Cloud Shell
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: a0d0544601bfc6ce0c7ef642f576ed780c8642ba
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763377"
---
# <a name="quickstart-run-ansible-playbooks-via-bash-in-azure-cloud-shell"></a>Szybki start: Uruchom elementy playbook rozwiązania Ansible za pośrednictwem funkcji Bash w usłudze Azure Cloud Shell

Usługa Azure Cloud Shell jest powłoką interaktywne, dostępny w przeglądarce do zarządzania zasobami platformy Azure. Usługa cloud Shell zapewnia umożliwia przy użyciu wiersza polecenia powłoki Bash lub programu Powershell. W tym artykule umożliwia Bash w usłudze Azure Cloud Shell Uruchom element playbook rozwiązania Ansible.

## <a name="prerequisites"></a>Wymagania wstępne

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Konfigurowanie usługi Azure Cloud Shell** — Jeśli jesteś nowym użytkownikiem usługi Azure Cloud Shell, zobacz [Szybki Start dla programu Bash w usłudze Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart).
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Konfiguracja poświadczeń automatycznych

Rozwiązanie Ansible uwierzytelnia się na platformie Azure po zalogowaniu do usługi Cloud Shell, umożliwiając zarządzanie infrastrukturą bez konieczności przeprowadzania jakiejkolwiek dodatkowej konfiguracji. 

Podczas pracy z wieloma subskrypcjami, określ subskrypcję korzysta z rozwiązania Ansible, eksportując `AZURE_SUBSCRIPTION_ID` zmiennej środowiskowej. 

Aby wyświetlić listę wszystkich swoich subskrypcji platformy Azure, uruchom następujące polecenie:

```azurecli-interactive
az account list
```

Przy użyciu swojego Identyfikatora subskrypcji platformy Azure, ustaw `AZURE_SUBSCRIPTION_ID` w następujący sposób:

```azurecli-interactive
export AZURE_SUBSCRIPTION_ID=<your-subscription-id>
```

## <a name="verify-the-configuration"></a>Sprawdzanie konfiguracji
Aby sprawdzić, Konfiguracja zakończyła się pomyślnie, za pomocą rozwiązania Ansible, aby utworzyć grupę zasobów platformy Azure.

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"] 
> [Szybki start: Konfigurowanie maszyny wirtualnej na platformie Azure, za pomocą rozwiązania Ansible](/azure/virtual-machines/linux/ansible-create-vm)