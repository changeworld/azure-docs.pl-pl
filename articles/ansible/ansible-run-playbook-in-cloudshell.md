---
title: Szybki start — uruchamianie czytelnych podręczników za pośrednictwem programu Bash w usłudze Azure Cloud Shell
description: W tym przewodniku Szybki start dowiedz się, jak wykonywać różne zadania ansible za pomocą programu Bash w usłudze Azure Cloud Shell
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: quickstart
ms.date: 04/30/2019
ms.openlocfilehash: 2a938179cf2e07a61749042db32ef9e1c9d843ba
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78247888"
---
# <a name="quickstart-run-ansible-playbooks-via-bash-in-azure-cloud-shell"></a>Szybki start: uruchamianie czytelnych podręczników za pośrednictwem programu Bash w usłudze Azure Cloud Shell

Usługa Azure Cloud Shell to interaktywna, dostępna w przeglądarce powłoka służąca do zarządzania zasobami platformy Azure. Usługa Cloud Shell umożliwia użycie wiersza polecenia Bash lub PowerShell. W tym artykule używasz Bash w usłudze Azure Cloud Shell do uruchomienia podręcznika Ansible.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Konfigurowanie usługi Azure Cloud Shell** — jeśli jesteś nowy w usłudze Azure Cloud Shell, zobacz Szybki start dla bash w [usłudze Azure Cloud Shell.](https://docs.microsoft.com/azure/cloud-shell/quickstart)
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Konfiguracja poświadczeń automatycznych

Rozwiązanie Ansible uwierzytelnia się na platformie Azure po zalogowaniu do usługi Cloud Shell, umożliwiając zarządzanie infrastrukturą bez konieczności przeprowadzania jakiejkolwiek dodatkowej konfiguracji. 

Podczas pracy z wieloma subskrypcjami określ użycie ansible `AZURE_SUBSCRIPTION_ID` subskrypcji przez eksportowanie zmiennej środowiskowej. 

Aby wyświetlić listę wszystkich swoich subskrypcji platformy Azure, uruchom następujące polecenie:

```azurecli-interactive
az account list
```

Przy użyciu identyfikatora subskrypcji `AZURE_SUBSCRIPTION_ID` platformy Azure, ustaw w następujący sposób:

```console
export AZURE_SUBSCRIPTION_ID=<your-subscription-id>
```

## <a name="verify-the-configuration"></a>Sprawdzanie konfiguracji
Aby zweryfikować pomyślną konfigurację, użyj ansible, aby utworzyć grupę zasobów platformy Azure.

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Szybki start: konfigurowanie maszyny wirtualnej na platformie Azure przy użyciu aplikacji Ansible](./ansible-create-vm.md)