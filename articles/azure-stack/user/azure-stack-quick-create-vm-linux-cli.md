---
title: Utwórz maszynę wirtualną systemu Linux przy użyciu wiersza polecenia platformy Azure w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Utwórz maszynę wirtualną systemu Linux przy użyciu interfejsu wiersza polecenia w usłudze Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 01/14/2019
ms.author: mabrigg
ms.custom: mvc
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: e9ef2def2aea83499d177549b497c741da0f606d
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59262486"
---
# <a name="quickstart-create-a-linux-server-virtual-machine-by-using-azure-cli-in-azure-stack"></a>Szybki Start: tworzenie maszyny wirtualnej z systemem Linux server przy użyciu wiersza polecenia platformy Azure w usłudze Azure Stack

*Dotyczy Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Przy użyciu wiersza polecenia platformy Azure, można utworzyć maszynę wirtualną Ubuntu Server 16.04 LTS. Wykonaj kroki opisane w tym artykule, aby utworzyć maszynę wirtualną. Ten artykuł zawiera również kroki, aby:

* Łączenie z maszyną wirtualną za pomocą zdalnego klienta.
* Zainstaluj serwer internetowy NGINX i wyświetlić domyślną stronę główną.
* Wyczyść nieużywane zasoby.

## <a name="prerequisites"></a>Wymagania wstępne

* **Obraz systemu Linux w witrynie marketplace usługi Azure Stack**

   W portalu marketplace usługi Azure Stack nie zawiera obraz systemu Linux, domyślnie. Pobierz operatora infrastruktury Azure Stack, aby zapewnić **Ubuntu Server 16.04 LTS** obrazu potrzebujesz. Operator może użyć procedury opisanej w [pobieranie elementów portalu marketplace z platformy Azure do usługi Azure Stack](../azure-stack-download-azure-marketplace-item.md) artykułu.

* Usługa Azure Stack wymaga określoną wersję interfejsu wiersza polecenia platformy Azure do tworzenia i zarządzania zasobami. Jeśli nie masz skonfigurowany dla usługi Azure Stack wiersza polecenia platformy Azure, zaloguj się do [deweloperski](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), oparte na Windows zewnętrznych klienta lub jeśli [połączone za pośrednictwem sieci VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) i postępuj zgodnie z instrukcjami, aby [ Instalowanie i Konfigurowanie interfejsu wiersza polecenia Azure](azure-stack-version-profiles-azurecli2.md).

* Publiczny klucz SSH przy użyciu id_rsa.pub nazwa zapisany w katalogu SSH profilu użytkownika Windows. Aby uzyskać szczegółowe informacje na temat tworzenia kluczy SSH, zobacz [kluczy tworzenia SSH w Windows](../../virtual-machines/linux/ssh-from-windows.md).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów to logiczny kontener, w której można wdrożyć aplikację i zarządzanie zasobami usługi Azure Stack. Z Twojego zestawu SDK narzędzia lub usługi Azure Stack zintegrowany system, uruchom [Tworzenie grupy az](/cli/azure/group#az-group-create) polecenie, aby utworzyć grupę zasobów.

> [!NOTE]
>  Wartości są przypisywane do wszystkich zmiennych w przykładach kodu. Jednak można przypisać nowe wartości, jeśli chcesz.

Poniższy przykład tworzy grupę zasobów o nazwie myResourceGroup w lokalizacji lokalnej.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną przy użyciu [tworzenie az vm](/cli/azure/vm#az-vm-create) polecenia. Poniższy przykład tworzy Maszynę wirtualną o nazwie myVM. W tym przykładzie użyto Demouser nazwa użytkownika administracyjnego i Demouser@123 jako hasło użytkownika. Zmień te wartości na coś, co jest odpowiednia dla użytkowanego środowiska.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "UbuntuLTS" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --location local
```

Publiczny adres IP jest zwracany w **publicznego adresu IP** parametru. Zanotuj ten adres, ponieważ będziesz ich potrzebować do dostępu do maszyny wirtualnej.

## <a name="open-port-80-for-web-traffic"></a>Otwieranie portu 80 na potrzeby ruchu w sieci Web

Ponieważ ta maszyna wirtualna ma uruchomić serwera sieci web usług IIS, należy otworzyć port 80 dla ruchu internetowego. Otwórz odpowiedni port za pomocą polecenia [az vm open-port](/cli/azure/vm).

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="use-ssh-to-connect-to-the-virtual-machine"></a>Łączenie z maszyną wirtualną za pomocą protokołu SSH

Z poziomu komputera klienckiego przy użyciu protokołu SSH, zainstalowane połączyć z maszyną wirtualną. Jeśli pracujesz na komputerze klienckim Windows, użyj [Putty](https://www.putty.org/) do utworzenia połączenia. Aby połączyć z maszyną wirtualną, użyj następującego polecenia:

```bash
ssh <publicIpAddress>
```

## <a name="install-the-nginx-web-server"></a>Instalowanie serwera internetowego NGINX

Aby zaktualizować pakiet zasobów i zainstalowania najnowszego pakietu NGINX, uruchom następujący skrypt:

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Wyświetlanie strony powitalnej serwera NGINX

Dzięki po zainstalowaniu serwera NGINX i otwarciu na maszynie wirtualnej portu 80 możesz uzyskać dostęp do serwera sieci web przy użyciu publicznego adresu IP maszyny wirtualnej. Otwórz przeglądarkę internetową i przejdź do ```http://<public IP address>```.

![Strona powitalna serwera sieci web NGINX](./media/azure-stack-quick-create-vm-linux-cli/nginx.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Wyczyść zasoby, których nie potrzebujesz już. Możesz użyć [usunięcie grupy az](/cli/azure/group#az-group-delete) polecenie, aby usunąć te zasoby. Aby usunąć grupę zasobów i wszystkie jej zasoby, uruchom następujące polecenie:

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start wdrożono podstawowe Linux serwera maszynę wirtualną z serwerem sieci web. Aby dowiedzieć się więcej o maszynach wirtualnych usługi Azure Stack, w dalszym ciągu [uwagi dotyczące maszyn wirtualnych w usłudze Azure Stack](azure-stack-vm-considerations.md).
