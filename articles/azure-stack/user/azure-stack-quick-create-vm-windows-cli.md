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
ms.date: 04/23/2018
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 381c1c37b0675d97adc058979a5d9b5c4fd2cc8b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-a-windows-server-virtual-machine-by-using-azure-cli-in-azure-stack"></a>Szybki Start: Utwórz maszynę wirtualną systemu Windows Server przy użyciu wiersza polecenia platformy Azure w stosie Azure

‎*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Można utworzyć maszyny wirtualnej systemu Windows Server 2016 przy użyciu wiersza polecenia platformy Azure. Wykonaj kroki opisane w tym artykule do utworzenia i użycia maszyny wirtualnej. Ten artykuł zawiera także następujące kroki:

* Połączenie z maszyną wirtualną za pomocą zdalnego klienta.
* Zainstaluj serwer sieci web usług IIS i wyświetlić domyślną stronę główną.
* Oczyszczanie zasobów.

## <a name="prerequisites"></a>Wymagania wstępne

* Upewnij się, że dodane operatorem stosu Azure **systemu Windows Server 2016** obrazu do stosu Azure marketplace.

* Stos Azure wymaga określonej wersji interfejsu wiersza polecenia Azure, aby utworzyć i zarządzać zasobami. Jeśli nie jest skonfigurowany do stosu Azure wiersza polecenia platformy Azure, wykonaj kroki, aby [Instalowanie i Konfigurowanie interfejsu wiersza polecenia Azure](azure-stack-version-profiles-azurecli2.md).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów jest kontenerem logicznym, w której można wdrożyć aplikację i zarządzania zasobami Azure stosu. W środowisku Azure stosu Uruchom [Tworzenie grupy az](/cli/azure/group#az_group_create) polecenie, aby utworzyć grupę zasobów.

>[!NOTE]
 Wartości są przypisywane do wszystkich zmiennych w przykładach kodu. Jednak aby można przypisać nowe wartości.

Poniższy przykład tworzy grupę zasobów o nazwie myResourceGroup w lokalizacji lokalnej.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną (VM) przy użyciu [tworzenia maszyny wirtualnej az](/cli/azure/vm#az_vm_create) polecenia. Poniższy przykład tworzy Maszynę wirtualną o nazwie myVM. W tym przykładzie użyto Demouser dla nazwy użytkownika administracyjnego i Demouser@123 jako hasło użytkownika. Zmiana tych wartości do zasobu, który jest odpowiedni dla danego środowiska.

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

Po utworzeniu maszyny Wirtualnej, **publicznego adresu IP** parametru w danych wyjściowych zawiera publicznego adresu IP dla maszyny wirtualnej. Zapisz ten adres, ponieważ będzie potrzebny dostęp do maszyny wirtualnej.

## <a name="open-port-80-for-web-traffic"></a>Otwieranie portu 80 na potrzeby ruchu w sieci Web

Ponieważ ta maszyna wirtualna ma uruchomić serwera sieci web usług IIS, należy otworzyć port 80 na ruch internetowy.

Użyj [port Otwórz az maszyny wirtualnej](/cli/azure/vm#open-port) polecenie, aby otworzyć port 80.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-the-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną

Następne polecenie umożliwia utworzenie połączenia pulpitu zdalnego z maszyną wirtualną. Zastąp "Publicznego adresu IP" adres IP maszyny wirtualnej. Po wyświetleniu monitu wprowadź nazwę użytkownika i hasło używane dla maszyny wirtualnej.

```
mstsc /v <Public IP Address>
```

## <a name="install-iis-using-powershell"></a>Instalowanie usług IIS przy użyciu programu PowerShell

Teraz, że logujesz się do maszyny wirtualnej, można zainstalować usługi IIS za pomocą programu PowerShell. Uruchom program PowerShell na maszynie wirtualnej, a następnie uruchom następujące polecenie:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Wyświetlanie strony powitalnej usług IIS

Wybraną przeglądarkę sieci web służy do wyświetlania domyślna strona powitalna usług IIS. Umożliwia publiczny adres IP, opisane w poprzedniej sekcji, odwiedź stronę domyślną.

![Domyślna witryna usług IIS](./media/azure-stack-quick-create-vm-windows-cli/default-iis-website.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Oczyszczanie zasobów, które nie jest już konieczne. Użyj [usunięcie grupy az](/cli/azure/group#az_group_delete) polecenie Usuń grupę zasobów dla maszyny wirtualnej, i wszystkich powiązanych zasobów.

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

Ta opcja szybkiego startu wdrożono podstawowej maszyny wirtualnej systemu Windows Server. Aby dowiedzieć się więcej o maszynach wirtualnych Azure stosu, nadal [zagadnienia dotyczące maszyn wirtualnych w stosie Azure](azure-stack-vm-considerations.md).
