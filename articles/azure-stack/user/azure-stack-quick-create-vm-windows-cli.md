---
title: Utwórz maszynę wirtualną Windows w usłudze Azure Stack przy użyciu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć maszyny Wirtualnej z systemem Windows w usłudze Azure Stack przy użyciu wiersza polecenia platformy Azure
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
ms.openlocfilehash: 67e0ccfa883e79d66eb9ca38a6cf15f00154c487
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59265716"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-by-using-azure-cli-in-azure-stack"></a>Szybki Start: tworzenie maszyny wirtualnej systemu Windows Server przy użyciu wiersza polecenia platformy Azure w usłudze Azure Stack

‎*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Można utworzyć maszynę wirtualną systemu Windows Server 2016 przy użyciu wiersza polecenia platformy Azure. Wykonaj kroki opisane w tym artykule, aby utworzyć maszynę wirtualną. Ten artykuł zawiera również następujące kroki:

* Łączenie z maszyną wirtualną za pomocą zdalnego klienta.
* Zainstaluj serwer sieci web usług IIS i wyświetlić domyślną stronę główną.
* Czyszczenie zasobów.

## <a name="prerequisites"></a>Wymagania wstępne

* Upewnij się, że operator usługi Azure Stack dodane **systemu Windows Server 2016** obrazu portalu Marketplace usługi Azure Stack.

* Usługa Azure Stack wymaga określoną wersję interfejsu wiersza polecenia platformy Azure do tworzenia i zarządzania zasobami. Jeśli nie masz skonfigurowany dla usługi Azure Stack interfejsu wiersza polecenia platformy Azure, wykonaj kroki, aby [Instalowanie i Konfigurowanie interfejsu wiersza polecenia Azure](azure-stack-version-profiles-azurecli2.md).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów to logiczny kontener, w której można wdrożyć aplikację i zarządzanie zasobami usługi Azure Stack. W środowisku usługi Azure Stack, uruchom polecenie [Tworzenie grupy az](/cli/azure/group#az-group-create) polecenie, aby utworzyć grupę zasobów.

> [!NOTE]
>  Wartości są przypisywane do wszystkich zmiennych w przykładach kodu. Jednak można przypisać nowe wartości, jeśli chcesz.

Poniższy przykład tworzy grupę zasobów o nazwie myResourceGroup w lokalizacji lokalnej.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną (VM) przy użyciu [tworzenie az vm](/cli/azure/vm#az-vm-create) polecenia. Poniższy przykład tworzy Maszynę wirtualną o nazwie myVM. W tym przykładzie użyto Demouser nazwa użytkownika administracyjnego i Demouser@123 jako hasło użytkownika. Zmień te wartości na coś, co jest odpowiednia dla użytkowanego środowiska.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "Win2016Datacenter" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --location local
```

Po utworzeniu maszyny Wirtualnej **publicznego adresu IP** parametr w danych wyjściowych zawiera publiczny adres IP dla maszyny wirtualnej. Zanotuj ten adres, ponieważ będziesz ich potrzebować do dostępu do maszyny wirtualnej.

## <a name="open-port-80-for-web-traffic"></a>Otwieranie portu 80 na potrzeby ruchu w sieci Web

Ponieważ ta maszyna wirtualna do uruchomienia serwera sieci web usług IIS, należy otworzyć port 80 dla ruchu internetowego.

Użyj [az vm open-port](/cli/azure/vm) polecenie, aby otworzyć port 80.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-the-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną

Poniższe polecenie umożliwia utworzenie połączenia pulpitu zdalnego z maszyną wirtualną. Zastąp "Publiczny adres IP" z adresem IP swojej maszyny wirtualnej. Po wyświetleniu monitu wprowadź nazwę użytkownika i hasło, które jest używane jako maszyny wirtualnej.

```
mstsc /v <Public IP Address>
```

## <a name="install-iis-using-powershell"></a>Instalowanie usług IIS przy użyciu programu PowerShell

Teraz, że logujesz się do maszyny wirtualnej, można użyć programu PowerShell, aby zainstalować usługi IIS. Uruchom program PowerShell na maszynie wirtualnej, a następnie uruchom następujące polecenie:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Wyświetlanie strony powitalnej usług IIS

Aby wyświetlić domyślną stronę powitalną usług IIS, można użyć wybranej przeglądarki sieci web. Użyj publicznego adresu IP, opisane w poprzedniej sekcji do odwiedzenia strony domyślnej.

![Domyślna witryna usług IIS](./media/azure-stack-quick-create-vm-windows-cli/default-iis-website.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Wyczyść zasoby, których nie potrzebujesz już. Użyj [usunięcie grupy az](/cli/azure/group#az-group-delete) polecenia, aby usunąć grupę zasobów, maszyna wirtualna i wszystkie pokrewne zasoby.

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start wdrożono podstawowe maszyny wirtualnej systemu Windows Server. Aby dowiedzieć się więcej o maszynach wirtualnych usługi Azure Stack, w dalszym ciągu [uwagi dotyczące maszyn wirtualnych w usłudze Azure Stack](azure-stack-vm-considerations.md).
