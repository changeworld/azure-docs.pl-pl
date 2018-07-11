---
title: Instalowanie i konfigurowanie rozwiązania Ansible do użytku z maszynami wirtualnymi platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zainstalować i skonfigurować rozwiązanie Ansible do zarządzania zasobami platformy Azure w systemie Ubuntu, CentOS i w systemie SLES
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/04/2018
ms.author: cynthn
ms.openlocfilehash: e7d57ead2caff87db07380582b9085b831844f1e
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2018
ms.locfileid: "37930073"
---
# <a name="install-and-configure-ansible-to-manage-virtual-machines-in-azure"></a>Instalowanie i konfigurowanie rozwiązania Ansible do zarządzania maszynami wirtualnymi na platformie Azure

Rozwiązanie Ansible pozwala zautomatyzować procesy wdrażania i konfiguracji zasobów w danym środowisku. Aby zarządzać maszyn wirtualnych (VM) na platformie Azure, takie same jak w przypadku innych zasobów, można za pomocą rozwiązania Ansible. Ten artykuł szczegółowo opisuje sposób instalowania rozwiązania Ansible i wymaganych modułów z zestawu Azure Python SDK dla niektórych typowych dystrybucje systemu Linux. Rozwiązanie Ansible można zainstalować na inne dystrybucje, dostosowując zainstalowane pakiety w celu dopasowania określonej platformy. Tworzenie zasobów platformy Azure w sposób bezpieczny, możesz także Dowiedz się, jak utworzenie i zdefiniowanie poświadczenia na potrzeby rozwiązania Ansible do użycia.

Aby uzyskać więcej opcji instalacji i kroki dla dodatkowych platform, zobacz [Przewodnik instalacji rozwiązania Ansible](https://docs.ansible.com/ansible/intro_installation.html).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i korzystać z interfejsu wiersza polecenia lokalnie, ten artykuł wymaga, czy korzystasz z wiersza polecenia platformy Azure w wersji 2.0.30 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="install-ansible"></a>Instalowanie rozwiązania Ansible

Jednym z najprostszych sposobów z platformą Azure za pomocą rozwiązania Ansible jest za pomocą usługi Azure Cloud Shell, oparte na przeglądarce powłoki do zarządzania i programowania zasobów platformy Azure. Ansible jest wstępnie instalowane w usłudze Cloud Shell, można pominąć instrukcje dotyczące sposobu instalowania rozwiązania Ansible i przejdź do [Azure Utwórz poświadczenia](#create-azure-credentials). Aby uzyskać listę dodatkowych narzędzi również dostępne w usłudze Cloud Shell, zobacz [funkcjach i narzędziach w programie Bash w usłudze Azure Cloud Shell](../../cloud-shell/features.md#tools).

Następujące instrukcje pokazują, jak utworzyć Maszynę wirtualną na różne dystrybucje systemu Linux, a następnie zainstaluj rozwiązania Ansible. Jeśli nie potrzebujesz utworzyć Maszynę wirtualną systemu Linux, Pomiń ten pierwszy krok, aby utworzyć grupę zasobów platformy Azure. Jeśli musisz utworzyć Maszynę wirtualną, najpierw utwórz grupę zasobów za pomocą [Tworzenie grupy az](/cli/azure/group#az_group_create). W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Teraz wybierz jedną z poniższe dystrybucje, aby uzyskać instrukcje dotyczące sposobu tworzenia maszyny Wirtualnej, jeśli to konieczne, a następnie zainstaluj rozwiązania Ansible:

- [CentOS 7.4](#centos-74)
- [Ubuntu 16.04 LTS](#ubuntu1604-lts)
- [SLES 12 Z DODATKIEM SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

Jeśli to konieczne, Utwórz Maszynę wirtualną za pomocą [tworzenie az vm](/cli/azure/vm#az_vm_create). Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVMAnsible*:

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image OpenLogic:CentOS:7.4:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH z maszyną Wirtualną za pomocą `publicIpAddress` wymienionych w danych wyjściowych z maszyną Wirtualną Operacja tworzenia:

```bash
ssh azureuser@<publicIpAddress>
```

Na maszynie Wirtualnej Zainstaluj wymagane pakiety dla modułów zestawu Azure Python SDK i rozwiązania Ansible w następujący sposób:

```bash
## Install pre-requisite packages
sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
sudo yum install -y python-pip python-wheel

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

Teraz przejdź do [Azure Utwórz poświadczenia](#create-azure-credentials).

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

Jeśli to konieczne, Utwórz Maszynę wirtualną za pomocą [tworzenie az vm](/cli/azure/vm#az_vm_create). Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVMAnsible*:

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image Canonical:UbuntuServer:16.04-LTS:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH z maszyną Wirtualną za pomocą `publicIpAddress` wymienionych w danych wyjściowych z maszyną Wirtualną Operacja tworzenia:

```bash
ssh azureuser@<publicIpAddress>
```

Na maszynie Wirtualnej Zainstaluj wymagane pakiety dla modułów zestawu Azure Python SDK i rozwiązania Ansible w następujący sposób:

```bash
## Install pre-requisite packages
sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

Teraz przejdź do [Azure Utwórz poświadczenia](#create-azure-credentials).

### <a name="sles-12-sp2"></a>SLES 12 Z DODATKIEM SP2

Jeśli to konieczne, Utwórz Maszynę wirtualną za pomocą [tworzenie az vm](/cli/azure/vm#az_vm_create). Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVMAnsible*:

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image SUSE:SLES:12-SP2:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH z maszyną Wirtualną za pomocą `publicIpAddress` wymienionych w danych wyjściowych z maszyną Wirtualną Operacja tworzenia:

```bash
ssh azureuser@<publicIpAddress>
```

Na maszynie Wirtualnej Zainstaluj wymagane pakiety dla modułów zestawu Azure Python SDK i rozwiązania Ansible w następujący sposób:

```bash
## Install pre-requisite packages
sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
    python-devel libopenssl-devel libtool python-pip python-setuptools

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]

# Remove conflicting Python cryptography package
sudo pip uninstall -y cryptography
```

Teraz przejdź do [Azure Utwórz poświadczenia](#create-azure-credentials).

## <a name="create-azure-credentials"></a>Tworzenie poświadczeń platformy Azure

Rozwiązanie Ansible komunikuje się z platformy Azure przy użyciu nazwy użytkownika i hasło lub jednostki usługi. Jednostka usługi platformy Azure to tożsamość zabezpieczeń używanej z aplikacjami, usługami i narzędzia automatyzacji, takie jak rozwiązania Ansible. Ty określasz i zdefiniować uprawnienia dotyczące jakie operacje nazwy głównej usługi można wykonać na platformie Azure. Aby poprawić bezpieczeństwo za pośrednictwem tylko podanie nazwy użytkownika i hasła, ten przykład tworzy podstawowa usługa podmiotu zabezpieczeń.

Na komputerze hosta lub usługi Azure Cloud Shell Utwórz główną usługi za pomocą [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac). Poświadczenia, których potrzebuje rozwiązania Ansible są dane wyjściowe na ekranie:

```azurecli-interactive
az ad sp create-for-rbac --query '{"client_id": appId, "secret": password, "tenant": tenant}'
```

Przykład danych wyjściowych z poprzedniego polecenia jest następująca:

```json
{
  "client_id": "eec5624a-90f8-4386-8a87-02730b5410d5",
  "secret": "531dcffa-3aff-4488-99bb-4816c395ea3f",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

Aby uwierzytelniać się na platformie Azure, trzeba będzie również uzyskać, używając Identyfikatora subskrypcji platformy Azure [Pokaż konta az](/cli/azure/account#az-account-show):

```azurecli-interactive
az account show --query "{ subscription_id: id }"
```

Dane wyjściowe z tych dwóch poleceń można użyć w następnym kroku.

## <a name="create-ansible-credentials-file"></a>Utwórz plik poświadczeń rozwiązania Ansible

O podanie poświadczeń w celu rozwiązania Ansible, definiowania zmiennych środowiskowych, lub utworzyć plik poświadczeń lokalnych. Aby uzyskać więcej informacji na temat definiowania Ansible poświadczeń, zobacz [dostarczanie poświadczeń do modułów Azure](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules).

Środowisko projektowe, można utworzyć *poświadczenia* pliku rozwiązania Ansible na hoście maszyny Wirtualnej. Utwórz plik poświadczeń na maszynie Wirtualnej, w którym zainstalowano rozwiązania Ansible w poprzednim kroku:

```bash
mkdir ~/.azure
vi ~/.azure/credentials
```

*Poświadczenia* sam plik łączy identyfikator subskrypcji z danymi wyjściowymi tworzenia nazwy głównej usługi. Dane wyjściowe z poprzednich [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) polecenie jest taka sama, odpowiednio do potrzeb *client_id*, *klucz tajny*, i *dzierżawy*. Poniższy przykład *poświadczenia* pliku znajdują się te wartości, dane wyjściowe poprzedniego dopasowania. Wprowadź własne wartości w następujący sposób:

```bash
[default]
subscription_id=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
client_id=eec5624a-90f8-4386-8a87-02730b5410d5
secret=531dcffa-3aff-4488-99bb-4816c395ea3f
tenant=72f988bf-86f1-41af-91ab-2d7cd011db47
```

Zapisz i zamknij plik.

## <a name="use-ansible-environment-variables"></a>Użyj zmiennych środowiskowych rozwiązania Ansible

Jeśli zamierzasz użyć narzędzi, takich jak Ansible Tower lub systemu Jenkins, musisz zdefiniować zmienne środowiskowe. Ten krok można pominąć, jeśli tylko będą korzystać z klienta rozwiązania Ansible, a plik poświadczeń platformy Azure utworzone w poprzednim kroku. Zmienne środowiskowe definiują te same informacje co plik poświadczeń platformy Azure:

```bash
export AZURE_SUBSCRIPTION_ID=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
export AZURE_CLIENT_ID=eec5624a-90f8-4386-8a87-02730b5410d5
export AZURE_SECRET=531dcffa-3aff-4488-99bb-4816c395ea3f
export AZURE_TENANT=72f988bf-86f1-41af-91ab-2d7cd011db47
```

## <a name="next-steps"></a>Kolejne kroki

Masz teraz Ansible oraz wymagane moduły zestawu Azure Python SDK zainstalowany i poświadczeń określonych dla rozwiązania Ansible do użycia. Dowiedz się, jak [Utwórz Maszynę wirtualną za pomocą rozwiązania Ansible](ansible-create-vm.md). Możesz też dowiedzieć się, jak [tworzenie pełnej maszyny Wirtualnej platformy Azure i obsługi zasobów za pomocą rozwiązania Ansible](ansible-create-complete-vm.md).
