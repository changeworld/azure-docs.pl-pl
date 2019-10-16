---
title: Uruchamianie skryptów powłoki na maszynie wirtualnej z systemem Linux na platformie Azure
description: W tym temacie opisano sposób uruchamiania skryptów w maszynie wirtualnej platformy Azure z systemem Linux przy użyciu polecenia Uruchom
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: 6550b6e3f59ff7e6bac39dfc1abcf829256122d4
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376357"
---
# <a name="run-shell-scripts-in-your-linux-vm-with-run-command"></a>Uruchamianie skryptów powłoki na maszynie wirtualnej z systemem Linux przy użyciu polecenia Run

Polecenie Run używa agenta maszyny wirtualnej do uruchamiania skryptów powłoki na maszynie wirtualnej z systemem Linux systemu Azure. Te skrypty mogą służyć do ogólnego zarządzania maszynami i aplikacjami oraz mogą być używane do szybkiego diagnozowania i rozwiązywania problemów z dostępem do maszyn wirtualnych oraz z powrotem do dobrego stanu.

## <a name="benefits"></a>Korzyści

Istnieje wiele opcji, których można użyć w celu uzyskania dostępu do maszyn wirtualnych. Polecenie Uruchom może zdalnie uruchamiać skrypty na maszynach wirtualnych za pomocą agenta maszyny wirtualnej. Polecenia Run można używać w przypadku maszyn wirtualnych z systemem Linux w Azure Portal, [interfejsie API REST](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)lub [platformie Azure](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) .

Ta funkcja jest przydatna w każdym scenariuszu, w którym chcesz uruchomić skrypt w ramach maszyn wirtualnych. jest to jeden z jedynych sposobów rozwiązywania problemów i korygowania maszyny wirtualnej, która nie ma otwartego portu RDP lub protokołu SSH z powodu nieprawidłowej sieci lub użytkownika administracyjnego skonfigurować.

## <a name="restrictions"></a>Ograniczenia

Poniżej przedstawiono listę ograniczeń, które są obecne podczas korzystania z polecenia Uruchom.

* Dane wyjściowe są ograniczone do ostatnich 4096 bajtów
* Minimalny czas uruchomienia skryptu około 20 sekund
* Skrypty uruchomione domyślnie jako użytkownicy z podwyższonym poziomem uprawnień w systemie Linux
* Jeden skrypt w czasie może być uruchomiony
* Skrypty, które monitują o informacje (tryb interaktywny) nie są obsługiwane.
* Nie można anulować uruchomionego skryptu
* Maksymalny czas uruchomienia skryptu wynosi 90 minut, po upływie którego limit czasu zostanie przekroczony
* Łączność wychodząca z maszyny wirtualnej jest wymagana do zwrócenia wyników skryptu.

> [!NOTE]
> Aby prawidłowo działać, polecenie Run wymaga łączności (port 443) z publicznymi adresami IP platformy Azure. Jeśli rozszerzenie nie ma dostępu do tych punktów końcowych, skrypty mogą działać prawidłowo, ale nie zwracają wyników. W przypadku blokowania ruchu na maszynie wirtualnej można używać [tagów usługi](../../virtual-network/security-overview.md#service-tags) , aby zezwalać na ruch do publicznych adresów IP platformy Azure przy użyciu znacznika `AzureCloud`.

## <a name="available-commands"></a>Dostępne polecenia

W tej tabeli przedstawiono listę poleceń dostępnych dla maszyn wirtualnych z systemem Linux. Polecenie **RunShellScript** może służyć do uruchamiania dowolnego niestandardowego skryptu. Korzystając z interfejsu wiersza polecenia platformy Azure lub programu PowerShell, aby uruchomić polecenie, wartość podana dla parametru `--command-id` lub `-CommandId` musi być jedną z wartości wymienionych poniżej. Jeśli określisz wartość, która nie jest dostępnym poleceniem, zostanie wyświetlony komunikat o błędzie.

```error
The entity was not found in this Azure location
```

|**Nazwa**|**Opis**|
|---|---|
|**RunShellScript**|Wykonuje skrypt powłoki systemu Linux.|
|**ifconfig**| Pobierz konfigurację wszystkich interfejsów sieciowych.|

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Poniżej znajduje się przykład za pomocą polecenia [AZ VM Run-command](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) do uruchomienia skryptu powłoki na maszynie wirtualnej z systemem Linux.

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "sudo apt-get update && sudo apt-get install -y nginx"
```

> [!NOTE]
> Aby uruchomić polecenia jako inny użytkownik, możesz użyć `sudo -u`, aby określić konto użytkownika, które ma być używane.

## <a name="azure-portal"></a>Azure Portal

Przejdź do maszyny wirtualnej na [platformie Azure](https://portal.azure.com) i wybierz pozycję **Uruchom polecenie** w obszarze **operacje**. Zostanie wyświetlona lista dostępnych poleceń do uruchomienia na maszynie wirtualnej.

![Uruchom listę poleceń](./media/run-command/run-command-list.png)

Wybierz polecenie do uruchomienia. Niektóre polecenia mogą zawierać opcjonalne lub wymagane parametry wejściowe. Dla tych poleceń parametry są prezentowane jako pola tekstowe umożliwiające podanie wartości wejściowych. Dla każdego polecenia można wyświetlić skrypt, który jest uruchamiany, rozszerzając **skrypt widoku**. **RunShellScript** różni się od innych poleceń, ponieważ umożliwia udostępnianie własnego skryptu niestandardowego.

> [!NOTE]
> Wbudowane polecenia nie są edytowalne.

Po wybraniu polecenia kliknij przycisk **Uruchom** , aby uruchomić skrypt. Skrypt jest uruchamiany i po zakończeniu zwraca dane wyjściowe i wszystkie błędy w oknie danych wyjściowych. Poniższy zrzut ekranu przedstawia przykładowe dane wyjściowe polecenia **ifconfig** .

![Uruchom dane wyjściowe skryptu polecenia](./media/run-command/run-command-script-output.png)

### <a name="powershell"></a>PowerShell

Poniżej przedstawiono przykład użycia polecenia cmdlet [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) w celu uruchomienia skryptu programu PowerShell na maszynie wirtualnej platformy Azure. Polecenie cmdlet oczekuje, że skrypt, do którego odwołuje się parametr `-ScriptPath`, ma być lokalny w miejscu, w którym jest uruchamiane polecenie cmdlet.

```powershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Ograniczanie dostępu do polecenia Run

Wyświetlanie listy poleceń uruchamiania lub wyświetlanie szczegółów polecenia wymaga uprawnienia `Microsoft.Compute/locations/runCommands/read` na poziomie subskrypcji, który ma wbudowaną rolę [czytnika](../../role-based-access-control/built-in-roles.md#reader) i wyższą.

Uruchomienie polecenia wymaga uprawnienia `Microsoft.Compute/virtualMachines/runCommand/action` na poziomie subskrypcji, który ma rola [współautor maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) i nowsza.

Możesz użyć jednej z [wbudowanych](../../role-based-access-control/built-in-roles.md) ról lub utworzyć rolę [niestandardową](../../role-based-access-control/custom-roles.md) , aby użyć polecenia Uruchom.

## <a name="next-steps"></a>Następne kroki

Zobacz [Uruchamianie skryptów na maszynie wirtualnej z systemem Linux](run-scripts-in-vm.md) , aby dowiedzieć się więcej na temat innych sposobów uruchamiania skryptów i poleceń zdalnie na maszynie wirtualnej.
