---
title: Szybki Start — uruchamianie rozwiązania ansible elementy PlayBook za pośrednictwem bash w Azure Cloud Shell
description: W tym przewodniku szybki start dowiesz się, jak wykonywać różne zadania rozwiązania ansible z bash w Azure Cloud Shell
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: quickstart
ms.date: 04/30/2019
ms.openlocfilehash: 0eb89bcacae1629bdb1f6dcda8f9a25efdb6eedf
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77193654"
---
# <a name="quickstart-run-ansible-playbooks-via-bash-in-azure-cloud-shell"></a>Szybki Start: uruchamianie rozwiązania ansible elementy PlayBook za pośrednictwem bash w Azure Cloud Shell

Azure Cloud Shell to interaktywna powłoka dostępna dla przeglądarki służąca do zarządzania zasobami platformy Azure. Cloud Shell zapewnia, że można użyć wiersza polecenia bash lub programu PowerShell. W tym artykule użyto bash w Azure Cloud Shell do uruchomienia rozwiązania ansible element PlayBook.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Konfigurowanie Azure Cloud Shell** — Jeśli jesteś nowym Azure Cloud Shell, zobacz [Przewodnik Szybki Start dotyczący bash w Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart).
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Konfiguracja poświadczeń automatycznych

Rozwiązanie Ansible uwierzytelnia się na platformie Azure po zalogowaniu do usługi Cloud Shell, umożliwiając zarządzanie infrastrukturą bez konieczności przeprowadzania jakiejkolwiek dodatkowej konfiguracji. 

Podczas pracy z wieloma subskrypcjami Określ subskrypcję rozwiązania ansible używaną przez wyeksportowanie zmiennej środowiskowej `AZURE_SUBSCRIPTION_ID`. 

Aby wyświetlić listę wszystkich swoich subskrypcji platformy Azure, uruchom następujące polecenie:

```azurecli-interactive
az account list
```

Korzystając z identyfikatora subskrypcji platformy Azure, ustaw `AZURE_SUBSCRIPTION_ID` w następujący sposób:

```azurecli-interactive
export AZURE_SUBSCRIPTION_ID=<your-subscription-id>
```

## <a name="verify-the-configuration"></a>Sprawdzanie konfiguracji
Aby sprawdzić pomyślną konfigurację, użyj rozwiązania ansible do utworzenia grupy zasobów platformy Azure.

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Szybki Start: Konfigurowanie maszyny wirtualnej na platformie Azure przy użyciu rozwiązania ansible](./ansible-create-vm.md)