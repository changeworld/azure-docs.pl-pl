---
title: Szybki start — tworzenie maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure 2.0 | Microsoft Docs
description: Z tego przewodnika Szybki start dowiesz się, jak utworzyć maszynę wirtualną z systemem Linux za pomocą interfejsu wiersza polecenia platformy Azure 2.0
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/24/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6536860bb75d068a96899f2d30ec7a6126a28436
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2018
ms.locfileid: "37927635"
---
# <a name="quickstart-create-a-linux-virtual-machine-with-the-azure-cli-20"></a>Szybki start: tworzenie maszyny wirtualnej z systemem Linux za pomocą interfejsu wiersza polecenia platformy Azure 2.0

Interfejs wiersza polecenia platformy Azure 2.0 umożliwia tworzenie zasobów platformy Azure i zarządzanie nimi z poziomu wiersza polecenia lub za pomocą skryptów. Z tego przewodnika Szybki start dowiesz się, jak za pomocą interfejsu wiersza polecenia platformy Azure 2.0 wdrożyć maszynę wirtualną platformy Azure z systemem Ubuntu. Aby następnie zobaczyć działanie maszyny wirtualnej, połączysz się z nią za pomocą protokołu SSH i zainstalujesz serwer internetowy NGINX.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.30 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm#az_vm_create).

W poniższym przykładzie pokazano tworzenie maszyny wirtualnej o nazwie *myVM*, dodawanie konta użytkownika o nazwie *azureuser* i generowanie kluczy SSH, jeśli jeszcze nie istnieją w domyślnej lokalizacji kluczy (*~/.ssh*). Aby użyć określonego zestawu kluczy, podaj opcję `--ssh-key-value`:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Utworzenie maszyny wirtualnej i zasobów pomocniczych potrwa kilka minut. Następujące przykładowe dane wyjściowe pokazują, że operacja utworzenia maszyny wirtualnej zakończyła się pomyślnie.

```azurecli-interactive
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

Zanotuj wartość elementu `publicIpAddress` z danych wyjściowych maszyny wirtualnej. Ten adres służy do uzyskiwania dostępu do maszyny wirtualnej w następnych krokach.

## <a name="open-port-80-for-web-traffic"></a>Otwieranie portu 80 na potrzeby ruchu w sieci Web

Domyślnie podczas tworzenia maszyny wirtualnej z systemem Linux na platformie Azure otwarte są tylko połączenia SSH. Otwórz port TCP 80 do używania z serwerem internetowym NGINX za pomocą polecenia [az vm open-port](/cli/azure/vm#az_vm_open_port):

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną

Połącz się przez protokół SSH z maszyną wirtualną w zwykły sposób. Zastąp ciąg **publicIpAddress** publicznym adresem IP maszyny wirtualnej, jak wspomniano we wcześniejszych danych wyjściowych z maszyny wirtualnej:

```bash
ssh azureuser@publicIpAddress
```

## <a name="install-web-server"></a>Instalowanie serwera internetowego

Aby zobaczyć działanie maszyny wirtualnej, zainstaluj serwer internetowy NGINX. Aby zaktualizować źródła pakietów i zainstalować najnowszy pakiet NGINX, uruchom następujące polecenia w sesji SSH:

```bash
# update packages
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

Po zakończeniu zamknij sesję SSH za pomocą polecenia `exit`.

## <a name="view-the-web-server-in-action"></a>Oglądanie działającego serwera internetowego

Po zainstalowaniu serwera NGINX i otwarciu portu 80 na maszynie wirtualnej dla ruchu z Internetu wyświetl domyślną stronę powitalną serwera NGINX przy użyciu wybranej przeglądarki internetowej. Użyj publicznego adresu IP maszyny wirtualnej uzyskanego w poprzednim kroku. W poniższym przykładzie przedstawiono domyślną witrynę internetową serwera NGINX:

![Domyślna witryna serwera NGINX](./media/quick-create-cli/nginx.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [az group delete](/cli/azure/group#az_group_delete). Upewnij się, że sesja SSH z maszyną wirtualną została zamknięta, a następnie usuń zasoby w następujący sposób:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wdrożono prostą maszynę wirtualną, otwarto port sieciowy na ruch internetowy oraz zainstalowano podstawowy serwer internetowy. Aby dowiedzieć się więcej o maszynach wirtualnych platformy Azure, przejdź do samouczka dla maszyn wirtualnych z systemem Linux.


> [!div class="nextstepaction"]
> [Samouczki dla maszyny wirtualnej platformy Azure z systemem Linux](./tutorial-manage-vm.md)
