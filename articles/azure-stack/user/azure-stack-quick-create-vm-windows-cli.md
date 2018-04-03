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
ms.date: 09/25/2017
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 2a4eb909c39051ce9fa2efd7e7997644d9b8b1b1
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="create-a-windows-virtual-machine-on-azure-stack-using-azure-cli"></a>Utwórz maszynę wirtualną systemu Windows na stosie Azure przy użyciu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure jest używana do tworzenia i zarządzania zasobami Azure stosu w wierszu polecenia. Szczegóły ten przewodnik do utworzenia maszyny wirtualnej systemu Windows Server 2016 w stosie Azure przy użyciu wiersza polecenia platformy Azure. Po utworzeniu maszyny wirtualnej będzie łączyć się z pulpitu zdalnego, zainstalować usługi IIS następnie wyświetlić domyślnej witryny sieci Web. 

## <a name="prerequisites"></a>Wymagania wstępne 

* Upewnij się, operatorem Azure stos został dodany obrazu "Windows Server 2016" do stosu Azure marketplace.  

* Stos Azure wymaga określonej wersji interfejsu wiersza polecenia Azure, aby utworzyć i zarządzać zasobami. Jeśli nie jest skonfigurowany do stosu Azure wiersza polecenia platformy Azure, wykonaj kroki, aby [Instalowanie i Konfigurowanie interfejsu wiersza polecenia Azure](azure-stack-version-profiles-azurecli2.md).

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
  --image "Win2016Datacenter" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --use-unmanaged-disk \
  --location local
```

Po utworzeniu maszyny Wirtualnej Zwróć uwagę na *publicznego adresu IP* parametr, który jest wyprowadzana, który będzie używany do maszyny Wirtualnej.
 
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

Teraz po zalogowaniu do maszyny wirtualnej platformy Azure możesz użyć jednego wiersza w programie PowerShell, aby zainstalować usługi IIS i włączyć lokalną regułę zapory, która zezwala na ruch w sieci Web. Otwórz wiersz polecenia programu PowerShell i uruchom następujące polecenie:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Wyświetlanie strony powitalnej usług IIS

Po zainstalowaniu usług IIS i otwarciu portu 80 na maszynie wirtualnej z Internetu możesz użyć wybranej przeglądarki sieci Web, aby wyświetlić domyślną stronę powitalną przeglądarki usług IIS. Upewnij się, że w celu odwiedzenia strony domyślnej używasz udokumentowanego powyżej publicznego adresu IP. 

![Domyślna witryna usług IIS](./media/azure-stack-quick-create-vm-windows-cli/default-iis-website.png) 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [az group delete](/cli/azure/group#az_group_delete).

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

W tym szybkiego startu wdrożeniu prostą maszynę wirtualną systemu Windows. Aby dowiedzieć się więcej o maszynach wirtualnych Azure stosu, nadal [zagadnienia dotyczące maszyn wirtualnych w stosie Azure](azure-stack-vm-considerations.md).
