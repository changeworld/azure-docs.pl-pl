---
title: Utwórz maszynę wirtualną systemu Windows na stosie Azure przy użyciu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć Maszynę wirtualną systemu Windows na stosie Azure przy użyciu wiersza polecenia platformy Azure
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: E26B246E-811D-44C9-9BA6-2B3CE5B62E83
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/19/2018
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 5665af14b9b0d0705b68c8a27c593b19c31b053e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="quickstart-create-a-windows-virtual-machine-in-azure-stack-using-azure-cli"></a>Szybki Start: Utwórz maszynę wirtualną systemu Windows Azure stosu przy użyciu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure jest używana do tworzenia i zarządzania zasobami Azure stosu w wierszu polecenia. W tym artykule pokazano, jak za pomocą wiersza polecenia platformy Azure można utworzyć oraz dostęp do maszyny wirtualnej systemu Windows Server 2016, w stosie Azure.

## <a name="prerequisites"></a>Wymagania wstępne

* Upewnij się, operatorem Azure stos został dodany obrazu "Windows Server 2016" do stosu Azure marketplace.

* Stos Azure wymaga określonej wersji interfejsu wiersza polecenia Azure, aby utworzyć i zarządzać zasobami. Jeśli nie jest skonfigurowany do stosu Azure wiersza polecenia platformy Azure, wykonaj kroki, aby [Instalowanie i Konfigurowanie interfejsu wiersza polecenia Azure](azure-stack-version-profiles-azurecli2.md).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów jest kontenerem logicznym, do których stosu Azure wdrożone i zarządzane zasoby. Z Twojego zestawu rozwoju lub stos Azure zintegrowany system obsługi [Tworzenie grupy az](/cli/azure/group#az_group_create) polecenie, aby utworzyć grupę zasobów. Wartości są przypisywane do wszystkich zmiennych w tym dokumencie, można użyć tych wartości lub przypisać nowe wartości. Poniższy przykład tworzy grupę zasobów o nazwie myResourceGroup w lokalizacji lokalnej.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę Wirtualną za pomocą [tworzenia maszyny wirtualnej az](/cli/azure/vm#az_vm_create) polecenia. Poniższy przykład tworzy Maszynę wirtualną o nazwie myVM. W tym przykładzie użyto Demouser dla nazwy użytkownika administracyjnego i Demouser@123 jako hasło. Zmień te wartości, aby pasowały do Twojego środowiska. Te wartości są wymagane, gdy połączenie z maszyną wirtualną.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "Win2016Datacenter" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --use-unmanaged-disk \
  --location local
```

Po utworzeniu maszyny Wirtualnej, *publicznego adresu IP* parametr jest danych wyjściowych. Zapisz ten adres, ponieważ będzie potrzebny dostęp do maszyny Wirtualnej.

## <a name="open-port-80-for-web-traffic"></a>Otwieranie portu 80 na potrzeby ruchu w sieci Web

Domyślnie tylko połączenia RDP są dozwolone na maszynę wirtualną systemu Windows wdrożone w stosie Azure. Jeśli ta maszyna wirtualna ma być serwerem sieci Web, port 80 należy otworzyć z Internetu. Otwórz odpowiedni port za pomocą polecenia [az vm open-port](/cli/azure/vm#open-port).

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-the-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną

Użyj następującego polecenia, aby utworzyć sesję usług pulpitu zdalnego z maszyną wirtualną. Zastąp adres IP publicznym adresem IP Twojej maszyny wirtualnej. Po wyświetleniu monitu wprowadź poświadczenia używane podczas tworzenia maszyny wirtualnej.

```
mstsc /v <Public IP Address>
```

## <a name="install-iis-using-powershell"></a>Instalowanie usług IIS przy użyciu programu PowerShell

Po zalogowaniu się do maszyny Wirtualnej Azure, można użyć jednowierszowego programu PowerShell instalacji usług IIS i włączenie reguły zapory lokalnej w celu umożliwienia ruchu w sieci web. Otwórz wiersz polecenia programu PowerShell i uruchom następujące polecenie:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Wyświetlanie strony powitalnej usług IIS

Wybraną przeglądarkę sieci web służy do wyświetlania domyślna strona powitalna usług IIS. Umożliwia publiczny adres IP, opisane w poprzedniej sekcji, odwiedź stronę domyślną.

![Domyślna witryna usług IIS](./media/azure-stack-quick-create-vm-windows-cli/default-iis-website.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, można użyć [usunięcie grupy az](/cli/azure/group#az_group_delete) polecenie Usuń grupę zasobów maszyny Wirtualnej, i wszystkich powiązanych zasobów.

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

W tym szybkiego startu wdrożeniu prostą maszynę wirtualną systemu Windows. Aby dowiedzieć się więcej o maszynach wirtualnych Azure stosu, nadal [zagadnienia dotyczące maszyn wirtualnych w stosie Azure](azure-stack-vm-considerations.md).
