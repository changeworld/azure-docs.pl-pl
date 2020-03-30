---
title: Szybki start — instalowanie ansible na maszynach wirtualnych systemu Linux na platformie Azure
description: W tym przewodniku Szybki start dowiedz się, jak zainstalować i skonfigurować program Ansible do zarządzania zasobami platformy Azure w ubuntu, centos i sles
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: gwallace
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 44007000475793005560914fd816cd0c16927f9a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77202410"
---
# <a name="quickstart-install-ansible-on-linux-virtual-machines-in-azure"></a>Szybki start: instalowanie ansible na maszynach wirtualnych systemu Linux na platformie Azure

Rozwiązanie Ansible umożliwia zautomatyzowanie wdrażania i konfigurowania zasobów w Twoim środowisku. W tym artykule pokazano, jak skonfigurować opcję Ansible dla niektórych z najczęstszych dystrybucji linuksa. Aby zainstalować Ansible na innych dystrybucjach, dostosuj zainstalowane pakiety dla danej platformy. 

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-sp.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
- **Dostęp do systemu Linux lub maszyny wirtualnej z systemem Linux** — jeśli nie masz maszyny wirtualnej z systemem Linux, [utwórz ją](/azure/virtual-network/quick-create-cli).

## <a name="install-ansible-on-an-azure-linux-virtual-machine"></a>Instalowanie rozwiązania Ansible na maszynie wirtualnej z systemem Linux na platformie Azure

Zaloguj się na maszynie z systemem Linux i wybierz jedną z następujących dystrybucji, aby zapoznać się z czynnościami wymaganymi do zainstalowania rozwiązania Ansible:

- [CentOS 7.4](#centos-74)
- Ubuntu 16.04 LTS
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

W tej sekcji można skonfigurować CentOS do używania ansible.

1. Otwórz okno terminalu.

1. Wprowadź następujące polecenie, aby zainstalować wymagane pakiety dla modułów zestawów SDK języka Azure Python:

    ```bash
    sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
    sudo yum install -y python-pip python-wheel
    ```

1. Wprowadź następujące polecenie, aby zainstalować wymagane pakiety Ansible:

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Utwórz poświadczenia platformy Azure](#create-azure-credentials).

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

W tej sekcji można skonfigurować Ubuntu do korzystania z Ansible.

1. Otwórz okno terminalu.

1. Wprowadź następujące polecenie, aby zainstalować wymagane pakiety dla modułów zestawów SDK języka Azure Python:

    ```bash
    sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip
    ```

1. Wprowadź następujące polecenie, aby zainstalować wymagane pakiety Ansible:

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Utwórz poświadczenia platformy Azure](#create-azure-credentials).

### <a name="sles-12-sp2"></a>SLES 12 SP2

W tej sekcji skonfigurujesz SLES do używania ansible.

1. Otwórz okno terminalu.

1. Wprowadź następujące polecenie, aby zainstalować wymagane pakiety dla modułów zestawów SDK języka Azure Python:

    ```bash
    sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
        python-devel libopenssl-devel libtool python-pip python-setuptools
    ```

1. Wprowadź następujące polecenie, aby zainstalować wymagane pakiety Ansible:

    ```bash
    sudo pip install ansible[azure]
    ```

1. Wprowadź następujące polecenie, aby usunąć pakiet kryptografii pythona powodujący konflikt:

    ```bash
    sudo pip uninstall -y cryptography
    ```

1. [Utwórz poświadczenia platformy Azure](#create-azure-credentials).

## <a name="create-azure-credentials"></a>Tworzenie poświadczeń platformy Azure

Aby skonfigurować poświadczenia Ansible, potrzebne są następujące informacje:

* Identyfikator subskrypcji platformy Azure 
* Wartości jednostkowe usługi

Jeśli używasz Ansible Tower lub Jenkins, zadeklarować wartości jednostki usługi jako zmienne środowiskowe.

Skonfiguruj poświadczenia Ansible przy użyciu jednej z następujących technik:

- [Tworzenie pliku poświadczeń rozwiązania Ansible](#file-credentials)
- [Użyj zmiennych środowiskowych Ansible](#env-credentials)

### <a name="span-idfile-credentials-create-ansible-credentials-file"></a><span id="file-credentials"/> Tworzenie pliku poświadczeń rozwiązania Ansible

W tej sekcji utworzysz lokalny plik poświadczeń, aby zapewnić poświadczenia do ansible. 

Aby uzyskać więcej informacji na temat definiowania poświadczeń Ansible, zobacz [dostarczanie poświadczeń do modułów platformy Azure](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules).

1. W środowisku deweloperskim utwórz plik o nazwie `credentials` na platformie wirtualnej hosta:

    ```bash
    mkdir ~/.azure
    vi ~/.azure/credentials
    ```

1. Wstaw następujące wiersze do pliku. Zastąp symbole zastępcze wartościami jednostki usługi.

    ```bash
    [default]
    subscription_id=<your-subscription_id>
    client_id=<security-principal-appid>
    secret=<security-principal-password>
    tenant=<security-principal-tenant>
    ```

1. Zapisz i zamknij plik.

### <a name="span-idenv-credentialsuse-ansible-environment-variables"></a><span id="env-credentials"/>Używanie zmiennych środowiskowych rozwiązania Ansible

W tej sekcji można wyeksportować wartości jednostki usługi, aby skonfigurować poświadczenia Ansible.

1. Otwórz okno terminalu.

1. Wyeksportuj wartości jednostkowe usługi:

    ```bash
    export AZURE_SUBSCRIPTION_ID=<your-subscription_id>
    export AZURE_CLIENT_ID=<security-principal-appid>
    export AZURE_SECRET=<security-principal-password>
    export AZURE_TENANT=<security-principal-tenant>
    ```

## <a name="verify-the-configuration"></a>Sprawdzanie konfiguracji

Aby zweryfikować pomyślną konfigurację, użyj ansible, aby utworzyć grupę zasobów platformy Azure.

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Szybki start: konfigurowanie maszyny wirtualnej systemu Linux na platformie Azure przy użyciu funkcji Ansible](./ansible-create-vm.md)