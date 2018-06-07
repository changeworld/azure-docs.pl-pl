---
title: Instalowanie i konfigurowanie Ansible do użytku z maszyn wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zainstalować i skonfigurować Ansible dla zarządzania zasobami platformy Azure na SLES, Ubuntu i CentOS
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
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
ms.author: iainfou
ms.openlocfilehash: c00ebcb771081f8e35c67bf384f5f6822e16f268
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34652995"
---
# <a name="install-and-configure-ansible-to-manage-virtual-machines-in-azure"></a>Instalowanie i konfigurowanie Ansible do zarządzania maszynami wirtualnymi na platformie Azure

Ansible umożliwia automatyzację wdrożenia i konfiguracji zasobów w danym środowisku. Ansible służy do zarządzania maszyn wirtualnych (VM) na platformie Azure, takie same jak w przypadku innych zasobów. Ten artykuł zawiera szczegóły dotyczące sposobu instalowania Ansible i wymagane moduły Azure Python SDK dla niektórych typowych dystrybucjach systemu Linux. Ansible można zainstalować na innych dystrybucjach, dopasowując zainstalowanych pakietów do określonej platformy. Tworzenie zasobów Azure, w sposób bezpieczny, możesz również Dowiedz się, jak utworzyć i zdefiniować poświadczenia dla Ansible do użycia.

Więcej opcji instalacji i kroki dla dodatkowych platform, zobacz [Przewodnik instalacji Ansible](https://docs.ansible.com/ansible/intro_installation.html).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli do zainstalowania i używania interfejsu wiersza polecenia lokalnie, w tym artykule, wymaga używasz interfejsu wiersza polecenia Azure w wersji 2.0.30 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="install-ansible"></a>Zainstaluj Ansible

Jednym z najprostszych sposobów Ansible za pomocą usługi Azure jest przy użyciu powłoki chmury Azure obsługi opartej na przeglądarce powłoki, zarządzania i opracowanie zasobów platformy Azure. Ansible jest wstępnie zainstalowane w powłoce chmury można pominąć instrukcje na temat instalowania Ansible i przejdź do [poświadczenia Azure utworzyć](#create-azure-credentials). Listę dodatkowych narzędzi są dostępne również w chmurze powłoki, zobacz [funkcji i narzędzi dla Bash w powłoce chmury Azure](../../cloud-shell/features.md#tools).

Poniższe instrukcje pokazują, jak utworzyć Maszynę wirtualną dla różnych dystrybucjach systemu Linux, a następnie zainstaluj Ansible. Jeśli nie potrzebujesz utworzyć Maszynę wirtualną systemu Linux, Pomiń ten krok pierwszy do utworzenia grupy zasobów platformy Azure. Jeśli trzeba utworzyć Maszynę wirtualną, najpierw utworzyć nową grupę zasobów o [Tworzenie grupy az](/cli/azure/group#az_group_create). W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Teraz wybierz jedną z następujących dystrybucjach instrukcje dotyczące sposobu tworzenia maszyny Wirtualnej, jeśli to konieczne, a następnie zainstaluj Ansible:

- [7.4 centOS](#centos-74)
- [Ubuntu 16.04 LTS](#ubuntu1604-lts)
- [SLES 12 Z DODATKIEM SP2](#sles-12-sp2)

### <a name="centos-74"></a>7.4 centOS

W razie potrzeby utwórz maszynę Wirtualną za pomocą [tworzenia maszyny wirtualnej az](/cli/azure/vm#az_vm_create). Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVMAnsible*:

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image OpenLogic:CentOS:7.4:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH przy użyciu maszyny Wirtualnej `publicIpAddress` zauważyć w danych wyjściowych z maszyny Wirtualnej operacji tworzenia:

```bash
ssh azureuser@<publicIpAddress>
```

Na maszynie Wirtualnej Zainstaluj wymagane pakiety dla modułów Azure Python SDK i Ansible w następujący sposób:

```bash
## Install pre-requisite packages
sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
sudo yum install -y python-pip python-wheel

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

Teraz przejść do [poświadczenia Azure utworzyć](#create-azure-credentials).

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

W razie potrzeby utwórz maszynę Wirtualną za pomocą [tworzenia maszyny wirtualnej az](/cli/azure/vm#az_vm_create). Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVMAnsible*:

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image Canonical:UbuntuServer:16.04-LTS:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH przy użyciu maszyny Wirtualnej `publicIpAddress` zauważyć w danych wyjściowych z maszyny Wirtualnej operacji tworzenia:

```bash
ssh azureuser@<publicIpAddress>
```

Na maszynie Wirtualnej Zainstaluj wymagane pakiety dla modułów Azure Python SDK i Ansible w następujący sposób:

```bash
## Install pre-requisite packages
sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip

## Install Ansible and Azure SDKs via pip
pip install ansible[azure]
```

Teraz przejść do [poświadczenia Azure utworzyć](#create-azure-credentials).

### <a name="sles-12-sp2"></a>SLES 12 Z DODATKIEM SP2

W razie potrzeby utwórz maszynę Wirtualną za pomocą [tworzenia maszyny wirtualnej az](/cli/azure/vm#az_vm_create). Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVMAnsible*:

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image SUSE:SLES:12-SP2:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH przy użyciu maszyny Wirtualnej `publicIpAddress` zauważyć w danych wyjściowych z maszyny Wirtualnej operacji tworzenia:

```bash
ssh azureuser@<publicIpAddress>
```

Na maszynie Wirtualnej Zainstaluj wymagane pakiety dla modułów Azure Python SDK i Ansible w następujący sposób:

```bash
## Install pre-requisite packages
sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
    python-devel libopenssl-devel libtool python-pip python-setuptools

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]

# Remove conflicting Python cryptography package
sudo pip uninstall -y cryptography
```

Teraz przejść do [poświadczenia Azure utworzyć](#create-azure-credentials).

## <a name="create-azure-credentials"></a>Utwórz poświadczenia platformy Azure

Ansible komunikuje się z platformy Azure przy użyciu nazwy użytkownika i hasła lub nazwy głównej usługi. Podmiot zabezpieczeń usługi Azure jest tożsamość zabezpieczeń korzystających z aplikacji, usługami i automatyzacja takich narzędzi jak Ansible. Kontrolowanie i definiowanie uprawnień określające, jakie operacje nazwy głównej usługi można wykonać na platformie Azure. Zwiększające bezpieczeństwo za pośrednictwem tylko podanie nazwy użytkownika i hasła, w tym przykładzie tworzy podstawowe usługę podmiotu zabezpieczeń.

Na komputerze hosta lub w powłoce chmury Azure, utworzyć główną usługi za pomocą [az ad sp utworzyć do rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac). Poświadczenia, które wymaga Ansible są dane wyjściowe do ekranu:

```azurecli-interactive
az ad sp create-for-rbac --query '{"client_id": appId, "secret": password, "tenant": tenant}'
```

Przykład danych wyjściowych z poprzedniego polecenia jest następujący:

```json
{
  "client_id": "eec5624a-90f8-4386-8a87-02730b5410d5",
  "secret": "531dcffa-3aff-4488-99bb-4816c395ea3f",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

Aby uwierzytelniać na platformie Azure, również należy uzyskać identyfikator subskrypcją platformy Azure za pomocą [Pokaż konto az](/cli/azure/account#az-account-show):

```azurecli-interactive
az account show --query "{ subscription_id: id }"
```

Dane wyjściowe z tych dwóch poleceń należy użyć w następnym kroku.

## <a name="create-ansible-credentials-file"></a>Utwórz plik poświadczeń Ansible

Aby podać poświadczenia, aby Ansible, zdefiniuj zmienne środowiskowe, lub utworzyć plik lokalne poświadczenia. Aby uzyskać więcej informacji na temat definiowania Ansible poświadczeń, zobacz [dostarczanie poświadczeń do modułów Azure](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules).

Środowisko deweloperskie, można utworzyć *poświadczenia* pliku Ansible na hoście maszyny Wirtualnej. Utwórz plik poświadczeń na maszynie Wirtualnej, w którym zainstalowano Ansible w poprzednim kroku:

```bash
mkdir ~/.azure
vi ~/.azure/credentials
```

*Poświadczenia* sam plik łączy identyfikator subskrypcji z danych wyjściowych Tworzenie nazwy głównej usługi. Dane wyjściowe z poprzedniej [az ad sp utworzyć do rbac](/cli/azure/ad/sp#create-for-rbac) polecenie jest taka sama jak wartość wymagane dla *client_id*, *klucz tajny*, i *dzierżawy*. Poniższy przykład *poświadczenia* plik zawiera wartości te dane wyjściowe poprzedniego dopasowania. Wprowadź własne wartości w następujący sposób:

```bash
[default]
subscription_id=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
client_id=eec5624a-90f8-4386-8a87-02730b5410d5
secret=531dcffa-3aff-4488-99bb-4816c395ea3f
tenant=72f988bf-86f1-41af-91ab-2d7cd011db47
```

Zapisz i zamknij plik.

## <a name="use-ansible-environment-variables"></a>Używać zmiennych środowiskowych Ansible

Jeśli zamierzasz używać narzędzi, takich jak wieża Ansible lub Wpięć, musisz zdefiniować zmienne środowiskowe. Ten krok można pominąć, jeśli zamierzasz tylko za pomocą klienta Ansible i plik poświadczeń Azure utworzony w poprzednim kroku. Zmienne środowiskowe definiują tych samych informacji jako plik poświadczeń platformy Azure:

```bash
export AZURE_SUBSCRIPTION_ID=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
export AZURE_CLIENT_ID=eec5624a-90f8-4386-8a87-02730b5410d5
export AZURE_SECRET=531dcffa-3aff-4488-99bb-4816c395ea3f
export AZURE_TENANT=72f988bf-86f1-41af-91ab-2d7cd011db47
```

## <a name="next-steps"></a>Kolejne kroki

Masz teraz Ansible oraz wymagane moduły Azure Python SDK zainstalowany i poświadczeń określonych dla Ansible do użycia. Dowiedz się, jak [utworzyć Maszynę wirtualną z Ansible](ansible-create-vm.md). Możesz też dowiedzieć się, jak [utworzyć pełną maszyny Wirtualnej platformy Azure i pomocnicze zasoby z Ansible](ansible-create-complete-vm.md).
