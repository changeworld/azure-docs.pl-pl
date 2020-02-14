---
title: Szybki Start — Instalowanie rozwiązania ansible na maszynach wirtualnych z systemem Linux na platformie Azure
description: W tym przewodniku szybki start dowiesz się, jak zainstalować i skonfigurować rozwiązania ansible do zarządzania zasobami platformy Azure na Ubuntu, CentOS i SLES
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: gwallace
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 44007000475793005560914fd816cd0c16927f9a
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202410"
---
# <a name="quickstart-install-ansible-on-linux-virtual-machines-in-azure"></a>Szybki Start: Instalowanie rozwiązania ansible na maszynach wirtualnych z systemem Linux na platformie Azure

Rozwiązanie Ansible umożliwia zautomatyzowanie wdrażania i konfigurowania zasobów w Twoim środowisku. W tym artykule przedstawiono sposób konfigurowania rozwiązania ansible dla niektórych typowych dystrybucje systemu Linux. Aby zainstalować rozwiązania ansible na innych dystrybucje, Dostosuj zainstalowane pakiety dla konkretnej platformy. 

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

W tej sekcji skonfigurujesz CentOS do korzystania z rozwiązania ansible.

1. Otwórz okno terminalu.

1. Wprowadź następujące polecenie, aby zainstalować wymagane pakiety dla modułów zestawu Azure Python SDK:

    ```bash
    sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
    sudo yum install -y python-pip python-wheel
    ```

1. Wprowadź następujące polecenie, aby zainstalować wymagane pakiety rozwiązania ansible:

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Utwórz poświadczenia platformy Azure](#create-azure-credentials).

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

W tej sekcji skonfigurujesz Ubuntu do korzystania z rozwiązania ansible.

1. Otwórz okno terminalu.

1. Wprowadź następujące polecenie, aby zainstalować wymagane pakiety dla modułów zestawu Azure Python SDK:

    ```bash
    sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip
    ```

1. Wprowadź następujące polecenie, aby zainstalować wymagane pakiety rozwiązania ansible:

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Utwórz poświadczenia platformy Azure](#create-azure-credentials).

### <a name="sles-12-sp2"></a>SLES 12 SP2

W tej sekcji skonfigurujesz SLES do korzystania z rozwiązania ansible.

1. Otwórz okno terminalu.

1. Wprowadź następujące polecenie, aby zainstalować wymagane pakiety dla modułów zestawu Azure Python SDK:

    ```bash
    sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
        python-devel libopenssl-devel libtool python-pip python-setuptools
    ```

1. Wprowadź następujące polecenie, aby zainstalować wymagane pakiety rozwiązania ansible:

    ```bash
    sudo pip install ansible[azure]
    ```

1. Wprowadź następujące polecenie, aby usunąć sprzeczny pakiet kryptograficzny języka Python:

    ```bash
    sudo pip uninstall -y cryptography
    ```

1. [Utwórz poświadczenia platformy Azure](#create-azure-credentials).

## <a name="create-azure-credentials"></a>Tworzenie poświadczeń platformy Azure

Aby skonfigurować poświadczenia rozwiązania ansible, potrzebne są następujące informacje:

* Identyfikator subskrypcji platformy Azure 
* Wartości główne usługi

Jeśli używasz rozwiązania ansible Tower lub Jenkins, zadeklaruj wartości głównej usługi jako zmienne środowiskowe.

Skonfiguruj poświadczenia rozwiązania ansible przy użyciu jednej z następujących technik:

- [Tworzenie pliku poświadczeń rozwiązania Ansible](#file-credentials)
- [Używanie zmiennych środowiskowych rozwiązania Ansible](#env-credentials)

### <a name="span-idfile-credentials-create-ansible-credentials-file"></a><span id="file-credentials"/> Tworzenie pliku poświadczeń rozwiązania Ansible

W tej sekcji utworzysz plik poświadczeń lokalnych w celu podania poświadczeń do rozwiązania ansible. 

Więcej informacji o definiowaniu poświadczeń rozwiązania ansible można znaleźć w temacie [dostarczanie poświadczeń do modułów platformy Azure](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules).

1. W przypadku środowiska programistycznego Utwórz plik o nazwie `credentials` na maszynie wirtualnej hosta:

    ```bash
    mkdir ~/.azure
    vi ~/.azure/credentials
    ```

1. Wstaw następujące wiersze do pliku. Zamień symbole zastępcze na wartości głównej usługi.

    ```bash
    [default]
    subscription_id=<your-subscription_id>
    client_id=<security-principal-appid>
    secret=<security-principal-password>
    tenant=<security-principal-tenant>
    ```

1. Zapisz i zamknij plik.

### <a name="span-idenv-credentialsuse-ansible-environment-variables"></a><span id="env-credentials"/>Używanie zmiennych środowiskowych rozwiązania Ansible

W tej sekcji wyeksportowano wartości główne usługi w celu skonfigurowania poświadczeń rozwiązania ansible.

1. Otwórz okno terminalu.

1. Wyeksportuj wartości główne usługi:

    ```bash
    export AZURE_SUBSCRIPTION_ID=<your-subscription_id>
    export AZURE_CLIENT_ID=<security-principal-appid>
    export AZURE_SECRET=<security-principal-password>
    export AZURE_TENANT=<security-principal-tenant>
    ```

## <a name="verify-the-configuration"></a>Sprawdzanie konfiguracji

Aby sprawdzić pomyślną konfigurację, użyj rozwiązania ansible do utworzenia grupy zasobów platformy Azure.

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Szybki Start: Konfigurowanie maszyny wirtualnej z systemem Linux na platformie Azure przy użyciu rozwiązania ansible](./ansible-create-vm.md)