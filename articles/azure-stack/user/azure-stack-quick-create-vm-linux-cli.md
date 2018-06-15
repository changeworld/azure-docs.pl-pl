---
title: Utwórz maszynę wirtualną systemu Linux przy użyciu wiersza polecenia platformy Azure w stosie Azure | Dokumentacja firmy Microsoft
description: Utwórz maszynę wirtualną systemu Linux z interfejsu wiersza polecenia Azure stosu.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 21F7D599-1FEC-4827-A5C3-06495C5F53A4
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/24/2018
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 90b36183ba32e75e06d434098d26cb10f3736373
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32156695"
---
# <a name="quickstart-create-a-linux-server-virtual-machine-by-using-azure-cli-in-azure-stack"></a>Szybki Start: tworzenie maszyny wirtualnej systemu Linux serwera przy użyciu wiersza polecenia platformy Azure w stosie Azure

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Można utworzyć maszyny wirtualnej systemu Ubuntu Server 16.04 LTS przy użyciu wiersza polecenia platformy Azure. Wykonaj kroki opisane w tym artykule do utworzenia i użycia maszyny wirtualnej. Ten artykuł zawiera także zapoznać się z procedurą:

* Połączenie z maszyną wirtualną za pomocą zdalnego klienta.
* Zainstaluj serwer sieci web NGINX i wyświetlić domyślną stronę główną.
* Wyczyścić zasoby nieużywane.

## <a name="prerequisites"></a>Wymagania wstępne

* **Obraz systemu Linux w stosie Azure marketplace**

   Domyślnie, stos Azure marketplace nie zawiera obrazu systemu Linux. Pobierz operatora stosu Azure, aby zapewnić **Ubuntu Server 16.04 LTS** obrazu należy. Operator można użyć procedury opisanej w [pobieranie elementów marketplace z platformy Azure do stosu Azure](../azure-stack-download-azure-marketplace-item.md) artykułu.

* Stos Azure wymaga określonej wersji interfejsu wiersza polecenia Azure, aby utworzyć i zarządzać zasobami. Jeśli nie jest skonfigurowany dla usługi Azure stosu interfejsu wiersza polecenia Azure, zaloguj się do [zestaw deweloperski](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), lub z systemem Windows klient zewnętrzny w przypadku [połączone za pośrednictwem sieci VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) i wykonaj kroki, aby [ Instalowanie i Konfigurowanie interfejsu wiersza polecenia Azure](azure-stack-version-profiles-azurecli2.md).

* Klucz publiczny SSH z id_rsa.pub nazwa zapisany w katalogu .ssh profilu użytkownika systemu Windows. Aby uzyskać szczegółowe informacje na temat tworzenia kluczy SSH, zobacz [klucze tworzenie SSH w systemie Windows](../../virtual-machines/linux/ssh-from-windows.md).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów jest kontenerem logicznym, w której można wdrożyć aplikację i zarządzania zasobami Azure stosu. Z Twojego zestawu rozwoju lub stos Azure zintegrowany system obsługi [Tworzenie grupy az](/cli/azure/group#az_group_create) polecenie, aby utworzyć grupę zasobów.

>[!NOTE]
 Wartości są przypisywane do wszystkich zmiennych w przykładach kodu. Jednak aby można przypisać nowe wartości.

Poniższy przykład tworzy grupę zasobów o nazwie myResourceGroup w lokalizacji lokalnej.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną za pomocą [tworzenia maszyny wirtualnej az](/cli/azure/vm#az_vm_create) polecenia. Poniższy przykład tworzy Maszynę wirtualną o nazwie myVM. W tym przykładzie użyto Demouser dla nazwy użytkownika administracyjnego i Demouser@123 jako hasło użytkownika. Zmiana tych wartości do zasobu, który jest odpowiedni dla danego środowiska.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "UbuntuLTS" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --use-unmanaged-disk \
  --location local
```

Publiczny adres IP jest zwracany w **publicznego adresu IP** parametru. Zapisz ten adres, ponieważ będzie potrzebny dostęp do maszyny wirtualnej.

## <a name="open-port-80-for-web-traffic"></a>Otwieranie portu 80 na potrzeby ruchu w sieci Web

Ponieważ ta maszyna wirtualna ma uruchomić serwera sieci web usług IIS, należy otworzyć port 80 na ruch internetowy. Otwórz odpowiedni port za pomocą polecenia [az vm open-port](/cli/azure/vm#open-port).

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="use-ssh-to-connect-to-the-virtual-machine"></a>Używanie protokołu SSH do nawiązania połączenia z maszyną wirtualną

Z komputera klienckiego przy użyciu protokołu SSH, zainstalowane połączenia z maszyną wirtualną. Jeśli pracujesz w kliencie systemu Windows, użyj [Putty](http://www.putty.org/) do utworzenia połączenia. Aby podłączyć się do maszyny wirtualnej, użyj następującego polecenia:

```bash
ssh <publicIpAddress>
```

## <a name="install-the-nginx-web-server"></a>Zainstaluj serwer sieci web NGINX

Aby zaktualizować zasoby pakietu i zainstalować najnowszy pakiet NGINX, uruchom następujący skrypt:

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Wyświetlanie strony powitalnej serwera NGINX

Z NGINX zainstalowane, a port 80 jest otwarty na maszynie wirtualnej można uzyskać dostępu do serwera sieci web przy użyciu publicznego adresu IP maszyny wirtualnej. Otwórz przeglądarkę sieci web i przejdź do ```http://<public IP address>```.

![Strona powitalna serwera sieci web NGINX](./media/azure-stack-quick-create-vm-linux-cli/nginx.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Oczyszczanie zasobów, które nie jest już konieczne. Można użyć [usunięcie grupy az](/cli/azure/group#az_group_delete) polecenie, aby usunąć te zasoby. Aby usunąć grupę zasobów i wszystkie jego zasoby, uruchom następujące polecenie:

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

Ta opcja szybkiego startu wdrożono podstawowego serwera maszyny wirtualnej systemu Linux przez serwer sieci web. Aby dowiedzieć się więcej o maszynach wirtualnych Azure stosu, nadal [zagadnienia dotyczące maszyn wirtualnych w stosie Azure](azure-stack-vm-considerations.md).
