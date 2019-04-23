---
title: Instalowanie rozwiązania Ansible na maszynach wirtualnych platformy Azure
description: Dowiedz się, w jaki sposób zainstalować i skonfigurować rozwiązanie Ansible, aby zarządzać zasobami platformy Azure w systemach Ubuntu, CentOS i SLES.
ms.service: virtual-machines-linux
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: quickstart
ms.date: 08/21/2018
ms.openlocfilehash: 38a1ffdc815b357f7bb7ebe2c337b55a738fb6b5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60187742"
---
# <a name="install-ansible-on-azure-virtual-machines"></a>Instalowanie rozwiązania Ansible na maszynach wirtualnych platformy Azure

Rozwiązanie Ansible umożliwia zautomatyzowanie wdrażania i konfigurowania zasobów w Twoim środowisku. Rozwiązanie Ansible umożliwia zarządzanie maszynami wirtualnymi na platformie Azure, tak jak ma to miejsce w przypadku każdego innego zasobu. W tym artykule omówiono szczegółowo sposób instalowania rozwiązania Ansible oraz moduły zestawu Azure Python SDK wymagane w przypadku najpopularniejszych dystrybucji systemu Linux. Możesz zainstalować rozwiązanie Ansible w innych dystrybucjach, dopasowując instalowane pakiety do konkretnej platformy. Dowiesz się również, w jaki sposób utworzyć i zdefiniować poświadczenia dla rozwiązania Ansible, aby bezpiecznie tworzyć zasoby na platformie Azure. Aby zapoznać się z listą dodatkowych narzędzi dostępnych w usłudze Azure Cloud Shell, zobacz [Features and tools for Bash in the Azure Cloud Shell (Funkcje i narzędzia dla programu Bash w usłudze Azure Cloud Shell)](../../cloud-shell/features.md#tools).

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja Azure** — jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Dostęp do systemu Linux lub maszyny wirtualnej z systemem Linux** — jeśli nie masz maszyny wirtualnej z systemem Linux, [utwórz ją](https://docs.microsoft.com/azure/virtual-network/quick-create-cli).

- **Jednostka usługi platformy Azure**: postępuj zgodnie z instrukcjami w sekcji dotyczącej **tworzenia jednostki usługi** artykułu [Tworzenie jednostki usługi platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest). Zapisz wartości pozycji **appId**, **displayName**, **password** i **tenant**.

## <a name="install-ansible-on-an-azure-linux-virtual-machine"></a>Instalowanie rozwiązania Ansible na maszynie wirtualnej z systemem Linux na platformie Azure

Zaloguj się na maszynie z systemem Linux i wybierz jedną z następujących dystrybucji, aby zapoznać się z czynnościami wymaganymi do zainstalowania rozwiązania Ansible:

- [CentOS 7.4](#centos-74)
- Ubuntu 16.04 LTS
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

Zainstaluj wymagane pakiety dla modułów zestawu Azure Python SDK oraz rozwiązania Ansible, wprowadzając następujące polecenia w oknie terminala lub programu Bash:

```bash
## Install pre-requisite packages
sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
sudo yum install -y python-pip python-wheel

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

Wykonaj czynności wymienione w sekcji [Tworzenie poświadczeń platformy Azure](#create-azure-credentials).

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

Zainstaluj wymagane pakiety dla modułów zestawu Azure Python SDK oraz rozwiązania Ansible, wprowadzając następujące polecenia w oknie terminala lub programu Bash:


```bash
## Install pre-requisite packages
sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

Wykonaj czynności wymienione w sekcji [Tworzenie poświadczeń platformy Azure](#create-azure-credentials).

### <a name="sles-12-sp2"></a>SLES 12 SP2

Zainstaluj wymagane pakiety dla modułów zestawu Azure Python SDK oraz rozwiązania Ansible, wprowadzając następujące polecenia w oknie terminala lub programu Bash:

```bash
## Install pre-requisite packages
sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
    python-devel libopenssl-devel libtool python-pip python-setuptools

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]

# Remove conflicting Python cryptography package
sudo pip uninstall -y cryptography
```

Wykonaj czynności wymienione w sekcji [Tworzenie poświadczeń platformy Azure](#create-azure-credentials).

## <a name="create-azure-credentials"></a>Tworzenie poświadczeń platformy Azure

Poświadczenia rozwiązania Ansible należy utworzyć za pomocą kombinacji identyfikatora subskrypcji i informacji zwróconych podczas tworzenia jednostki usługi. Można to zrobić na jeden z następujących sposobów:

- [Tworzenie pliku poświadczeń rozwiązania Ansible](#file-credentials)
- [Używanie zmiennych środowiskowych rozwiązania Ansible](#env-credentials)

Jeśli masz zamiar korzystać z takich narzędzi jak Ansible Tower lub Jenkins, konieczne jest zadeklarowanie wartości jednostki usługi jako zmiennych środowiskowych.

### <a name="span-idfile-credentials-create-ansible-credentials-file"></a><span id="file-credentials"/> Tworzenie pliku poświadczeń rozwiązania Ansible

W tej sekcji wyjaśniono, w jaki sposób utworzyć lokalny plik poświadczeń, aby dostarczyć poświadczenia rozwiązania Ansible. Aby uzyskać więcej informacji na temat definiowania poświadczeń rozwiązania Ansible, zobacz [Providing Credentials to Azure Modules (Dostarczanie poświadczeń modułom platformy Azure)](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules).

W przypadku środowiska projektowego utwórz plik *credentials* rozwiązania Ansible na Twojej maszynie wirtualnej hosta w następujący sposób:

```bash
mkdir ~/.azure
vi ~/.azure/credentials
```

Wstaw następujące wiersze do pliku *credentials*, zastępując symbole zastępcze informacjami uzyskanymi z utworzonej jednostki usługi.

```bash
[default]
subscription_id=<your-subscription_id>
client_id=<security-principal-appid>
secret=<security-principal-password>
tenant=<security-principal-tenant>
```

Zapisz i zamknij plik.

### <a name="span-idenv-credentialsuse-ansible-environment-variables"></a><span id="env-credentials"/>Używanie zmiennych środowiskowych rozwiązania Ansible

W tej sekcji wyjaśniono, w jaki sposób skonfigurować poświadczenia rozwiązania Ansible przez wyeksportowanie ich jako zmiennych środowiskowych.

W oknie terminala lub programu Bash wprowadź następujące polecenia:

```bash
export AZURE_SUBSCRIPTION_ID=<your-subscription_id>
export AZURE_CLIENT_ID=<security-principal-appid>
export AZURE_SECRET=<security-principal-password>
export AZURE_TENANT=<security-principal-tenant>
```

## <a name="verify-the-configuration"></a>Sprawdzanie konfiguracji
Aby sprawdzić, czy konfiguracja zakończyła się pomyślnie, możesz teraz użyć rozwiązania Ansible, aby utworzyć grupę zasobów.

[!INCLUDE [create-resource-group-with-ansible.md](../../../includes/ansible-create-resource-group.md)]

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"] 
> [Tworzenie maszyny wirtualnej z systemem Linux na platformie Azure za pomocą rozwiązania Ansible](./ansible-create-vm.md)
