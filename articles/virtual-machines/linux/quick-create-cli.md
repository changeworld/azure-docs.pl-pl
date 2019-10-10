---
title: 'Szybki Start: Tworzenie maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure'
description: W tym przewodniku szybki start dowiesz się, jak utworzyć maszynę wirtualną z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/09/2018
ms.author: cynthn
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: 0208e72263991daceed52073bcce8b3e01f8e8c2
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174295"
---
# <a name="quickstart-create-a-linux-virtual-machine-with-the-azure-cli"></a>Szybki Start: Tworzenie maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure

Ten przewodnik Szybki Start przedstawia sposób wdrażania maszyny wirtualnej z systemem Linux na platformie Azure przy użyciu interfejsu wiersza polecenia (CLI) platformy Azure. Interfejs wiersza polecenia platformy Azure umożliwia tworzenie zasobów platformy Azure i zarządzanie nimi z poziomu wiersza poleceń lub skryptów. W tym samouczku będziemy instalować Ubuntu 16,04 LTS. Aby wyświetlić maszynę wirtualną w działaniu, nawiąż połączenie z nią przy użyciu protokołu SSH i Zainstaluj serwer sieci Web NGINX.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell uruchamiania

Azure Cloud Shell to bezpłatna interaktywna powłoka, za pomocą której można wykonać kroki opisane w tym artykule. Ma ona wspólne narzędzia platformy Azure preinstalowane i skonfigurowane do użycia z Twoim kontem. 

Aby otworzyć Cloud Shell, po prostu wybierz opcję **Wypróbuj** w prawym górnym rogu bloku kodu. Możesz również otworzyć Cloud Shell na osobnej karcie przeglądarki, przechodząc do [https://shell.azure.com/bash](https://shell.azure.com/bash). Wybierz pozycję **Kopiuj** , aby skopiować bloki kodu, wklej je do Cloud Shell i wybierz **klawisz ENTER** , aby go uruchomić.

Jeśli wolisz zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik Szybki Start będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.30 lub nowszej. Uruchom `az --version`, aby znaleźć wersję. Jeśli konieczne jest zainstalowanie lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [AZ Group Create](/cli/azure/group) . Grupa zasobów platformy Azure to logiczny kontener, w którym są wdrażane i zarządzane zasoby platformy Azure. Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *Wschodnie* :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Utwórz maszynę wirtualną

Utwórz maszynę wirtualną za pomocą polecenia [AZ VM Create](/cli/azure/vm) .

Poniższy przykład tworzy maszynę wirtualną o nazwie *myVM* i dodaje konto użytkownika o nazwie *azureuser*. Parametr `--generate-ssh-keys` umożliwia automatyczne wygenerowanie klucza SSH i umieszczenie go w lokalizacji klucza domyślnego ( *~/.SSH*). Aby zamiast tego użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Tworzenie maszyny wirtualnej i obsługę zasobów może potrwać kilka minut. Następujące przykładowe dane wyjściowe pokazują, że operacja tworzenia maszyny wirtualnej zakończyła się pomyślnie.

```output
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

Zwróć uwagę na własne `publicIpAddress` w danych wyjściowych z maszyny wirtualnej. Ten adres służy do uzyskiwania dostępu do maszyny wirtualnej w następnych krokach.

## <a name="open-port-80-for-web-traffic"></a>Otwórz port 80 dla ruchu w sieci Web

Domyślnie podczas tworzenia maszyny wirtualnej z systemem Linux na platformie Azure są otwierane tylko połączenia SSH. Użyj [AZ VM Open-Port](/cli/azure/vm) , aby otworzyć port TCP 80 do użycia z serwerem sieci Web Nginx:

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-virtual-machine"></a>Połącz z maszyną wirtualną

Użyj protokołu SSH na potrzeby maszyny wirtualnej. Zastąp **publicIpAddress** publicznym adresem IP maszyny wirtualnej, zgodnie z powyższym wyjściem z maszyny wirtualnej:

```bash
ssh azureuser@publicIpAddress
```

## <a name="install-web-server"></a>Zainstaluj serwer sieci Web

Aby sprawdzić działanie maszyny wirtualnej, Zainstaluj serwer sieci Web NGINX. Zaktualizuj źródła pakietów, a następnie zainstaluj najnowszy pakiet NGINX.

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

Po zakończeniu wpisz `exit`, aby opuścić sesję SSH.

## <a name="view-the-web-server-in-action"></a>Wyświetlanie serwera sieci Web w akcji

Użyj wybranej przeglądarki sieci Web, aby wyświetlić domyślną stronę powitalną NGINX. Użyj publicznego adresu IP maszyny wirtualnej jako adresu internetowego. W poniższym przykładzie przedstawiono domyślną witrynę sieci Web NGINX:

![Lokacja domyślna NGINX](./media/quick-create-cli/nginx.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [AZ Group Delete](/cli/azure/group) . 

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start wdrożono prostą maszynę wirtualną, otwarto port sieciowy dla ruchu w sieci Web i zainstalowano podstawowy serwer sieci Web. Aby dowiedzieć się więcej o usłudze Azure Virtual Machines, przejdź do samouczka dotyczącego maszyn wirtualnych z systemem Linux.


> [!div class="nextstepaction"]
> [Samouczki dotyczące maszyn wirtualnych z systemem Linux na platformie Azure](./tutorial-manage-vm.md)
