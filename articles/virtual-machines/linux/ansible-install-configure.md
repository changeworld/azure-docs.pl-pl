---
title: Przewodnik Szybki Start — instalacja Ansible na maszynach wirtualnych systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: W tym przewodniku Szybki Start Dowiedz się, jak zainstalować i skonfigurować rozwiązanie Ansible do zarządzania zasobami platformy Azure w systemie Ubuntu, CentOS i w systemie SLES
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: gwallace
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: d3302d999dd70a83be18ce610b9c3d44992c865c
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671838"
---
# <a name="quickstart-install-ansible-on-linux-virtual-machines-in-azure"></a>Szybki start: Instalowanie rozwiązania Ansible na maszynach wirtualnych systemu Linux na platformie Azure

Rozwiązanie Ansible umożliwia zautomatyzowanie wdrażania i konfigurowania zasobów w Twoim środowisku. W tym artykule przedstawiono sposób konfigurowania rozwiązania Ansible dla niektórych typowych dystrybucje systemu Linux. Aby zainstalować rozwiązanie Ansible na inne dystrybucje, Dostosuj zainstalowane pakiety dla konkretnej platformy. 

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-sp.md](../../../includes/open-source-devops-prereqs-create-service-principal.md)]
- **Dostęp do systemu Linux lub maszyny wirtualnej z systemem Linux** — jeśli nie masz maszyny wirtualnej z systemem Linux, [utwórz ją](/azure/virtual-network/quick-create-cli).

## <a name="install-ansible-on-an-azure-linux-virtual-machine"></a>Instalowanie rozwiązania Ansible na maszynie wirtualnej z systemem Linux na platformie Azure

Zaloguj się na maszynie z systemem Linux i wybierz jedną z następujących dystrybucji, aby zapoznać się z czynnościami wymaganymi do zainstalowania rozwiązania Ansible:

- [CentOS 7.4](#centos-74)
- Ubuntu 16.04 LTS
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

W tej sekcji skonfigurujesz CentOS za pomocą rozwiązania Ansible.

1. Otwórz okno terminalu.

1. Wprowadź następujące polecenie, aby zainstalować wymagane pakiety dla modułów zestawu Azure Python SDK:

    ```bash
    sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
    sudo yum install -y python-pip python-wheel
    ```

1. Wprowadź następujące polecenie, aby zainstalować wymagane pakiety Ansible:

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Tworzenie poświadczeń platformy Azure](#create-azure-credentials).

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

W tej sekcji skonfigurujesz Ubuntu za pomocą rozwiązania Ansible.

1. Otwórz okno terminalu.

1. Wprowadź następujące polecenie, aby zainstalować wymagane pakiety dla modułów zestawu Azure Python SDK:

    ```bash
    sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip
    ```

1. Wprowadź następujące polecenie, aby zainstalować wymagane pakiety Ansible:

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Tworzenie poświadczeń platformy Azure](#create-azure-credentials).

### <a name="sles-12-sp2"></a>SLES 12 SP2

W tej sekcji możesz skonfigurować za pomocą rozwiązania Ansible w systemie SLES.

1. Otwórz okno terminalu.

1. Wprowadź następujące polecenie, aby zainstalować wymagane pakiety dla modułów zestawu Azure Python SDK:

    ```bash
    sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
        python-devel libopenssl-devel libtool python-pip python-setuptools
    ```

1. Wprowadź następujące polecenie, aby zainstalować wymagane pakiety Ansible:

    ```bash
    sudo pip install ansible[azure]
    ```

1. Wprowadź następujące polecenie, aby usunąć powodujące konflikt pakietu kryptograficznego języka Python:

    ```bash
    sudo pip uninstall -y cryptography
    ```

1. [Tworzenie poświadczeń platformy Azure](#create-azure-credentials).

## <a name="create-azure-credentials"></a>Tworzenie poświadczeń platformy Azure

Aby skonfigurować poświadczenia rozwiązania Ansible, potrzebne są następujące informacje:

* Identyfikator subskrypcji platformy Azure 
* Wartości nazwy głównej usługi

Jeśli używasz Ansible Tower lub systemu Jenkins, należy zadeklarować wartości nazwy głównej usługi jako zmienne środowiskowe.

Skonfiguruj poświadczenia rozwiązania Ansible, przy użyciu jednej z następujących technik:

- [Tworzenie pliku poświadczeń rozwiązania Ansible](#file-credentials)
- [Używanie zmiennych środowiskowych rozwiązania Ansible](#env-credentials)

### <a name="span-idfile-credentials-create-ansible-credentials-file"></a><span id="file-credentials"/> Tworzenie pliku poświadczeń rozwiązania Ansible

W tej sekcji utworzysz plik poświadczeń lokalnych o podanie poświadczeń w celu rozwiązania Ansible. 

Aby uzyskać więcej informacji na temat definiowania Ansible poświadczeń, zobacz [dostarczanie poświadczeń do modułów Azure](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules).

1. Dla środowiska programowania, Utwórz plik o nazwie `credentials` na maszynie wirtualnej hosta:

    ```bash
    mkdir ~/.azure
    vi ~/.azure/credentials
    ```

1. Wstaw następujące wiersze do pliku. Zastąp symbole zastępcze wartości nazwy głównej usługi.

    ```bash
    [default]
    subscription_id=<your-subscription_id>
    client_id=<security-principal-appid>
    secret=<security-principal-password>
    tenant=<security-principal-tenant>
    ```

1. Zapisz i zamknij plik.

### <a name="span-idenv-credentialsuse-ansible-environment-variables"></a><span id="env-credentials"/>Używanie zmiennych środowiskowych rozwiązania Ansible

W tej sekcji możesz wyeksportować wartości nazwy głównej usługi w celu skonfigurowania poświadczeń Ansible.

1. Otwórz okno terminalu.

1. Eksportuj wartości nazwy głównej usługi:

    ```bash
    export AZURE_SUBSCRIPTION_ID=<your-subscription_id>
    export AZURE_CLIENT_ID=<security-principal-appid>
    export AZURE_SECRET=<security-principal-password>
    export AZURE_TENANT=<security-principal-tenant>
    ```

## <a name="verify-the-configuration"></a>Sprawdzanie konfiguracji

Aby sprawdzić, Konfiguracja zakończyła się pomyślnie, za pomocą rozwiązania Ansible, aby utworzyć grupę zasobów platformy Azure.

[!INCLUDE [create-resource-group-with-ansible.md](../../../includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Szybki start: Konfigurowanie maszyny wirtualnej systemu Linux na platformie Azure za pomocą rozwiązania Ansible](./ansible-create-vm.md)