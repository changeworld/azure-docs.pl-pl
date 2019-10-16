---
title: Uruchamianie skryptów programu PowerShell na maszynie wirtualnej z systemem Windows na platformie Azure
description: W tym temacie opisano sposób uruchamiania skryptów programu PowerShell na maszynie wirtualnej z systemem Windows Azure przy użyciu polecenia Uruchom
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: 0a9a5e465e160da34a21f66fd7176a8fea5d1aac
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376246"
---
# <a name="run-powershell-scripts-in-your-windows-vm-with-run-command"></a>Uruchamianie skryptów programu PowerShell na maszynie wirtualnej z systemem Windows za pomocą polecenia Run

Polecenie Run używa agenta maszyny wirtualnej do uruchamiania skryptów programu PowerShell na maszynie wirtualnej z systemem Windows Azure. Te skrypty mogą służyć do ogólnego zarządzania maszynami i aplikacjami oraz mogą być używane do szybkiego diagnozowania i rozwiązywania problemów z dostępem do maszyn wirtualnych oraz z powrotem do dobrego stanu.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="benefits"></a>Korzyści

Istnieje wiele opcji, których można użyć w celu uzyskania dostępu do maszyn wirtualnych. Polecenie Uruchom może zdalnie uruchamiać skrypty na maszynach wirtualnych za pomocą agenta maszyny wirtualnej. Polecenia Run można używać za pośrednictwem Azure Portal, [interfejsu API REST](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)lub [programu PowerShell](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) dla maszyn wirtualnych z systemem Windows.

Ta funkcja jest przydatna w każdym scenariuszu, w którym chcesz uruchomić skrypt w ramach maszyn wirtualnych. jest to jeden z jedynych sposobów rozwiązywania problemów i korygowania maszyny wirtualnej, która nie ma otwartego portu RDP lub protokołu SSH z powodu nieprawidłowej sieci lub użytkownika administracyjnego skonfigurować.

## <a name="restrictions"></a>Ograniczenia

W przypadku korzystania z polecenia Run obowiązują następujące ograniczenia:

* Dane wyjściowe są ograniczone do ostatnich 4096 bajtów
* Minimalny czas uruchomienia skryptu wynosi około 20 sekund
* Skrypty uruchamiane jako system w systemie Windows
* Jeden skrypt w czasie może być uruchomiony
* Skrypty, które monitują o informacje (tryb interaktywny) nie są obsługiwane.
* Nie można anulować uruchomionego skryptu
* Maksymalny czas uruchomienia skryptu wynosi 90 minut, po upływie którego limit czasu zostanie przekroczony
* Łączność wychodząca z maszyny wirtualnej jest wymagana do zwrócenia wyników skryptu.

> [!NOTE]
> Aby prawidłowo działać, polecenie Run wymaga łączności (port 443) z publicznymi adresami IP platformy Azure. Jeśli rozszerzenie nie ma dostępu do tych punktów końcowych, skrypty mogą działać prawidłowo, ale nie zwracają wyników. W przypadku blokowania ruchu na maszynie wirtualnej można używać [tagów usługi](../../virtual-network/security-overview.md#service-tags) , aby zezwalać na ruch do publicznych adresów IP platformy Azure przy użyciu znacznika `AzureCloud`.

## <a name="available-commands"></a>Dostępne polecenia

W tej tabeli przedstawiono listę poleceń dostępnych dla maszyn wirtualnych z systemem Windows. Polecenie **RunPowerShellScript** może służyć do uruchamiania dowolnego niestandardowego skryptu. Korzystając z interfejsu wiersza polecenia platformy Azure lub programu PowerShell, aby uruchomić polecenie, wartość podana dla parametru `--command-id` lub `-CommandId` musi być jedną z wartości wymienionych poniżej. Jeśli określisz wartość, która nie jest dostępnym poleceniem, zostanie wyświetlony komunikat o błędzie.

```error
The entity was not found in this Azure location
```

|**Nazwa**|**Opis**|
|---|---|
|**RunPowerShellScript**|Wykonuje skrypt programu PowerShell|
|**EnableRemotePS**|Konfiguruje maszynę w celu włączenia zdalnego programu PowerShell.|
|**EnableAdminAccount**|Sprawdza, czy konto administratora lokalnego jest wyłączone, a jeśli to możliwe, należy je włączyć.|
|**IPConfig**| Przedstawia szczegółowe informacje o adresie IP, masce podsieci i bramie domyślnej dla każdej karty powiązanej z protokołem TCP/IP.|
|**RDPSettings**|Sprawdza ustawienia rejestru i ustawienia zasad domeny. Sugeruje akcje zasad, jeśli komputer jest częścią domeny lub modyfikuje ustawienia do wartości domyślnych.|
|**ResetRDPCert**|Usuwa certyfikat SSL powiązany z odbiornikiem RDP i przywraca domyślne zabezpieczenia odbiornika RDP. Użyj tego skryptu, Jeśli zobaczysz problemy z certyfikatem.|
|**SetRDPPort**|Ustawia domyślny lub określony przez użytkownika numer portu dla połączeń Pulpit zdalny. Włącza regułę zapory dla dostępu przychodzącego do portu.|

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Poniżej znajduje się przykład za pomocą polecenia [AZ VM Run-command](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) do uruchomienia skryptu powłoki na maszynie wirtualnej z systemem Linux.

```azurecli-interactive
# script.ps1
#   param(
#       [string]$arg1,
#       [string]$arg2
#   )
#   Write-Host This is a sample script with parameters $arg1 and $arg2

az vm run-command invoke  --command-id RunPowerShellScript --name win-vm -g my-resource-group \
    --scripts @script.ps1 --parameters "arg1=somefoo" "arg2=somebar"
```

## <a name="azure-portal"></a>Azure Portal

Przejdź do maszyny wirtualnej na [platformie Azure](https://portal.azure.com) i wybierz pozycję **Uruchom polecenie** w obszarze **operacje**. Zostanie wyświetlona lista dostępnych poleceń do uruchomienia na maszynie wirtualnej.

![Uruchom listę poleceń](./media/run-command/run-command-list.png)

Wybierz polecenie do uruchomienia. Niektóre polecenia mogą zawierać opcjonalne lub wymagane parametry wejściowe. Dla tych poleceń parametry są prezentowane jako pola tekstowe umożliwiające podanie wartości wejściowych. Dla każdego polecenia można wyświetlić skrypt, który jest uruchamiany, rozszerzając **skrypt widoku**. **RunPowerShellScript** różni się od innych poleceń, ponieważ umożliwia udostępnianie własnego skryptu niestandardowego.

> [!NOTE]
> Wbudowane polecenia nie są edytowalne.

Po wybraniu polecenia kliknij przycisk **Uruchom** , aby uruchomić skrypt. Skrypt jest uruchamiany i po zakończeniu zwraca dane wyjściowe i wszystkie błędy w oknie danych wyjściowych. Poniższy zrzut ekranu przedstawia przykładowe dane wyjściowe polecenia **RDPSettings** .

![Uruchom dane wyjściowe skryptu polecenia](./media/run-command/run-command-script-output.png)

## <a name="powershell"></a>PowerShell

Poniżej przedstawiono przykład użycia polecenia cmdlet [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) w celu uruchomienia skryptu programu PowerShell na maszynie wirtualnej platformy Azure. Polecenie cmdlet oczekuje, że skrypt, do którego odwołuje się parametr `-ScriptPath`, ma być lokalny w miejscu, w którym jest uruchamiane polecenie cmdlet.

```azurepowershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Ograniczanie dostępu do polecenia Run

Wyświetlanie listy poleceń uruchamiania lub wyświetlanie szczegółów polecenia wymaga uprawnienia `Microsoft.Compute/locations/runCommands/read` na poziomie subskrypcji, który ma wbudowaną rolę [czytnika](../../role-based-access-control/built-in-roles.md#reader) i wyższą.

Uruchomienie polecenia wymaga uprawnienia `Microsoft.Compute/virtualMachines/runCommand/action` na poziomie subskrypcji, który ma rola [współautor maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) i nowsza.

Możesz użyć jednej z [wbudowanych](../../role-based-access-control/built-in-roles.md) ról lub utworzyć rolę [niestandardową](../../role-based-access-control/custom-roles.md) , aby użyć polecenia Uruchom.

## <a name="next-steps"></a>Następne kroki

Zobacz [Uruchamianie skryptów na maszynie wirtualnej z systemem Windows](run-scripts-in-vm.md) , aby dowiedzieć się więcej na temat innych sposobów uruchamiania skryptów i poleceń zdalnie na maszynie wirtualnej.
