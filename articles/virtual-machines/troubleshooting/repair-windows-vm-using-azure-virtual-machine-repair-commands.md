---
title: Naprawianie maszyny Wirtualnej systemu Windows przy użyciu poleceń naprawy maszyny wirtualnej platformy Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano, jak używać poleceń naprawy maszyny Wirtualnej platformy Azure do łączenia dysku z inną maszyną wirtualną systemu Windows w celu naprawienia błędów, a następnie przebudowywania oryginalnej maszyny wirtualnej.
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-machines
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 09/10/2019
ms.author: v-miegge
ms.openlocfilehash: 2055558ef80a641084a7cf9d299281497d282936
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060669"
---
# <a name="repair-a-windows-vm-by-using-the-azure-virtual-machine-repair-commands"></a>Naprawianie maszyny wirtualnej z systemem Windows za pomocą poleceń naprawiania maszyny wirtualnej platformy Azure

Jeśli maszyna wirtualna systemu Windows (VM) na platformie Azure napotka błąd rozruchu lub dysku, może być konieczne wykonanie środków zaradczych na samym dysku. Typowym przykładem może być nieudana aktualizacja aplikacji, która uniemożliwia maszynie wirtualnej pomyślne uruchomienie. W tym artykule opisano, jak używać poleceń naprawy maszyny Wirtualnej platformy Azure do łączenia dysku z inną maszyną wirtualną systemu Windows w celu naprawienia błędów, a następnie przebudowywania oryginalnej maszyny wirtualnej.

> [!IMPORTANT]
> Skrypty w tym artykule dotyczą tylko maszyn wirtualnych korzystających z [usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="repair-process-overview"></a>Omówienie procesu naprawy

Teraz można użyć poleceń naprawy maszyny Wirtualnej platformy Azure, aby zmienić dysk systemu operacyjnego dla maszyny Wirtualnej i nie trzeba już usuwać i ponownietwolać maszyny Wirtualnej.

Wykonaj następujące kroki, aby rozwiązać problem z maszyną wirtualną:

1. Uruchamianie usługi Azure Cloud Shell
2. Uruchom az rozszerzenie dodaj/aktualizuj.
3. Uruchom az vm repair create.
4. Uruchom az vm repair run.
5. Uruchom przywracanie naprawy az vm.

Aby uzyskać dodatkową dokumentację i instrukcje, zobacz [az vm repair](https://docs.microsoft.com/cli/azure/ext/vm-repair/vm/repair).

## <a name="repair-process-example"></a>Przykład procesu naprawy

> [!NOTE]
> * Łączność wychodząca z maszyny Wirtualnej (port 443) jest wymagana do uruchomienia skryptu.
> * W ciągu jednego czasu może działać tylko jeden skrypt.
> * Nie można anulować uruchomionego skryptu.
> * Maksymalny czas uruchomienia skryptu wynosi 90 minut, po czym limit czasu.

1. Uruchamianie usługi Azure Cloud Shell

   Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Zawiera typowe narzędzia platformy Azure preinstalowane i skonfigurowane do używania z kontem.

   Aby otworzyć powłokę chmury, wybierz **pozycję Wypróbuj ją** w prawym górnym rogu bloku kodu. Możesz również otworzyć Usługę Cloud Shell w [https://shell.azure.com](https://shell.azure.com)osobnej karcie przeglądarki, odwiedzając stronę .

   Wybierz **pozycję Kopiuj,** aby skopiować bloki kodu, a następnie wklej kod do powłoki chmury, a następnie wybierz pozycję **Enter,** aby go uruchomić.

   Jeśli wolisz zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.30 lub nowszej. Uruchom polecenie ``az --version``, aby dowiedzieć się, jaka wersja jest używana. Jeśli chcesz zainstalować lub uaktualnić platformę Interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

2. Jeśli jest to pierwszy raz, `az vm repair` gdy używasz poleceń, dodaj rozszerzenie interfejsu wiersza polecenia naprawy vm.

   ```azurecli-interactive
   az extension add -n vm-repair
   ```

   Jeśli wcześniej używano `az vm repair` poleceń, zastosuj wszelkie aktualizacje do rozszerzenia vm-repair.

   ```azurecli-interactive
   az extension update -n vm-repair
   ```

3. Uruchom polecenie `az vm repair create`. To polecenie utworzy kopię dysku systemu operacyjnego dla nie funkcjonalnej maszyny Wirtualnej, utworzy maszynę wirtualną naprawy i dołączy dysk.

   ```azurecli-interactive
   az vm repair create -g MyResourceGroup -n myVM --repair-username username --repair-password password!234 --verbose
   ```

4. Uruchom polecenie `az vm repair run`. To polecenie uruchomi określony skrypt naprawy na dołączonym dysku za pośrednictwem maszyny wirtualnej naprawy.

   ```azurecli-interactive
   az vm repair run  –g MyResourceGroup –n MyVM -–run-on-repair --run-id 2 --verbose
   ```

5. Uruchom polecenie `az vm repair restore`. To polecenie zamieni naprawiony dysk systemu operacyjnego na oryginalny dysk systemu operacyjnego maszyny Wirtualnej.

   ```azurecli-interactive
   az vm repair restore -g MyResourceGroup -n MyVM --verbose
   ```

## <a name="verify-and-enable-boot-diagnostics"></a>Weryfikowanie i włączanie diagnostyki rozruchu

Poniższy przykład włącza rozszerzenie diagnostyczne ``myVMDeployed`` na maszynie ``myResourceGroup``Wirtualnej o nazwie w grupie zasobów o nazwie:

Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
az vm boot-diagnostics enable --name myVMDeployed --resource-group myResourceGroup --storage https://mystor.blob.core.windows.net/
```

## <a name="next-steps"></a>Następne kroki

* Jeśli masz problemy z połączeniem się z maszyną [wirtualną, zobacz Rozwiązywanie problemów z połączeniami protokołu RDP z maszyną wirtualną platformy Azure.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-rdp-connection)
* W przypadku problemów z uzyskiwaniem dostępu do aplikacji uruchomionych na maszynie wirtualnej zobacz [Rozwiązywanie problemów z łącznością aplikacji na maszynach wirtualnych na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-app-connection).
* Aby uzyskać więcej informacji na temat korzystania z Menedżera zasobów, zobacz [Omówienie usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
