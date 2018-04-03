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
ms.date: 09/25/2017
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 69036b522b375eced604256340b532ad14a8708e
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="create-a-linux-virtual-machine-by-using-azure-cli-in-azure-stack"></a>Utwórz maszynę wirtualną systemu Linux przy użyciu wiersza polecenia platformy Azure w stosie Azure

*Dotyczy: Azure stosu zintegrowane systemy*

Interfejs wiersza polecenia platformy Azure jest używana do tworzenia i zarządzania zasobami Azure stosu w wierszu polecenia. Szczegóły tego szybkiego startu do utworzenia maszyny wirtualnej systemu Linux w stosie Azure przy użyciu wiersza polecenia platformy Azure.  Po utworzeniu maszyny Wirtualnej serwera sieci web jest zainstalowana, a port 80 jest otwarty zezwalająca na ruch w sieci web.

## <a name="prerequisites"></a>Wymagania wstępne 

* Upewnij się, operatorem Azure stos został dodany obrazu "Ubuntu Server 16.04 LTS" do stosu Azure marketplace. 

* Stos Azure wymaga określonej wersji interfejsu wiersza polecenia Azure, aby utworzyć i zarządzać zasobami. Jeśli nie jest skonfigurowany do stosu Azure wiersza polecenia platformy Azure, zaloguj się do [zestaw deweloperski](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), lub z systemem Windows klient zewnętrzny w przypadku [połączone za pośrednictwem sieci VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) i wykonaj kroki, aby [zainstalować i Konfigurowanie interfejsu wiersza polecenia Azure](azure-stack-version-profiles-azurecli2.md).

* Klucz publiczny SSH o nazwie id_rsa.pub powinny zostać utworzone w katalogu .ssh profilu użytkownika systemu Windows. Aby uzyskać szczegółowe informacje na temat tworzenia kluczy SSH, zobacz [klucze tworzenie SSH w systemie Windows](../../virtual-machines/linux/ssh-from-windows.md). 

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów jest kontenerem logicznym, do których stosu Azure wdrożone i zarządzane zasoby. Z Twojego zestawu rozwoju lub stos Azure zintegrowany system obsługi [Tworzenie grupy az](/cli/azure/group#az_group_create) polecenie, aby utworzyć grupę zasobów. Firma Microsoft zostały przypisane wartości wszystkich zmiennych w tym dokumencie, można używać ich jest lub przypisać inną wartość. Poniższy przykład tworzy grupę zasobów o nazwie myResourceGroup w lokalizacji lokalnej.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę Wirtualną za pomocą [tworzenia maszyny wirtualnej az](/cli/azure/vm#az_vm_create) polecenia. Poniższy przykład tworzy Maszynę wirtualną o nazwie myVM. W tym przykładzie użyto Demouser dla nazwy użytkownika administracyjnego i Demouser@123 jako hasło. Zmień te wartości, aby pasowały do Twojego środowiska. Te wartości są wymagane, gdy połączenie z maszyną wirtualną.

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

Po zakończeniu polecenia dane wyjściowe obejmują parametry dla maszyny wirtualnej.  Zwróć uwagę na *publicznego adresu IP*, ponieważ umożliwia to połączenie i zarządzanie nimi na komputerze wirtualnym.

## <a name="open-port-80-for-web-traffic"></a>Otwieranie portu 80 na potrzeby ruchu w sieci Web

Domyślnie dozwolone są tylko połączenia SSH z maszynami wirtualnymi z systemem Linux wdrożonymi na platformie Azure. Jeśli ta maszyna wirtualna ma być serwerem sieci Web, port 80 należy otworzyć z Internetu. Otwórz odpowiedni port za pomocą polecenia [az vm open-port](/cli/azure/vm#open-port).

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="ssh-into-your-vm"></a>Łączenie z maszyną wirtualną za pośrednictwem protokołu SSH

Z poziomu systemu z zainstalowanym protokołem SSH użyj następującego polecenia, aby nawiązać połączenie z maszyną wirtualną. W przypadku korzystania z systemu Windows w celu utworzenia połączenia można użyć programu [Putty](http://www.putty.org/). Upewnij się zastąpić poprawne publiczny adres IP maszyny wirtualnej. W naszym przykładzie powyżej adres IP został 192.168.102.36.

```bash
ssh <publicIpAddress>
```

## <a name="install-nginx"></a>Instalowanie serwera NGINX

Użyj poniższego skryptu powłoki systemowej w celu zaktualizowania źródeł pakietów i zainstalowania najnowszego pakietu NGINX. 

```bash 
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Wyświetlanie strony powitalnej serwera NGINX

Po zainstalowaniu serwera NGINX i otwarciu portu 80 na maszynie wirtualnej z Internetu możesz użyć wybranej przeglądarki sieci Web, aby wyświetlić domyślną stronę powitalną przeglądarki serwera NGINX. Upewnij się, że w celu odwiedzenia strony domyślnej używasz udokumentowanego powyżej *publicznego adresu IP*. 

![Domyślna witryna serwera NGINX](./media/azure-stack-quick-create-vm-linux-cli/nginx.png) 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [az group delete](/cli/azure/group#az_group_delete).

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

W tym szybkiego startu wdrożeniu proste maszyny wirtualnej systemu Linux. Aby dowiedzieć się więcej o maszynach wirtualnych Azure stosu, nadal [zagadnienia dotyczące maszyn wirtualnych w stosie Azure](azure-stack-vm-considerations.md).

