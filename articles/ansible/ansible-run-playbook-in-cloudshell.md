---
title: Uruchamianie rozwiązania Ansible przy użyciu programu Bash w usłudze Azure Cloud Shell
description: Dowiedz się, jak wykonywać różne zadania Ansible, przy użyciu programu Bash w usłudze Azure Cloud Shell
ms.service: ansible
keywords: ansible, azure, metodyki devops, bash, cloudshell, element playbook, bash
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 08/07/2018
ms.topic: article
ms.openlocfilehash: 9928f646905dd0da4b15166ec55e5d8a183cb210
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2018
ms.locfileid: "42060658"
---
# <a name="run-ansible-with-bash-in-azure-cloud-shell"></a>Uruchamianie rozwiązania Ansible przy użyciu programu Bash w usłudze Azure Cloud Shell

W tym samouczku dowiesz się, jak skonfigurować subskrypcję platformy Azure, co Twój obszar roboczy rozwiązania Ansible za pomocą powłoki Bash w usłudze Cloud Shell. 

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja platformy Azure** — Jeśli nie masz subskrypcji platformy Azure, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Konfigurowanie usługi Azure Cloud Shell** — Jeśli jesteś nowym użytkownikiem usługi Azure Cloud Shell, artykuł [Szybki Start dla programu Bash w usłudze Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart), pokazano, jak uruchamianie i konfigurowanie usługi Cloud Shell. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Poświadczenie automatycznej konfiguracji

Po zalogowaniu w usłudze Cloud Shell, Ansible uwierzytelnia się za pomocą platformy Azure w celu zarządzania infrastrukturą bez przeprowadzania dodatkowej konfiguracji. Jeśli masz więcej niż jedną subskrypcję, możesz wybrać subskrypcję, która Ansible powinny współpracować z przez wyeksportowanie `AZURE_SUBSCRIPTION_ID` zmiennej środowiskowej. Aby wyświetlić listę wszystkich subskrypcji platformy Azure, uruchom następujące polecenie:

```azurecli-interactive
az account list
```

Za pomocą **identyfikator** subskrypcji, z którym chcesz pracować, ustaw **AZURE_SUBSCRIPTION_ID** w następujący sposób:

```azurecli-interactive
export AZURE_SUBSCRIPTION_ID=<your-subscription-id>
```

## <a name="verify-the-configuration"></a>Sprawdź, czy konfiguracja
Aby sprawdzić, Konfiguracja zakończyła się pomyślnie, za pomocą rozwiązania Ansible, aby utworzyć grupę zasobów.

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-create-resource-group.md)]

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"] 
> [Tworzenie podstawowego maszyny wirtualnej na platformie Azure za pomocą rozwiązania Ansible](/azure/virtual-machines/linux/ansible-create-vm)