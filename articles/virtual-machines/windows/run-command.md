---
title: Uruchamianie skryptów programu PowerShell na maszynie Wirtualnej systemu Windows na platformie Azure
description: W tym temacie opisano sposób uruchamiania skryptów programu PowerShell na maszynie wirtualnej systemu Azure Windows przy użyciu funkcji Uruchom polecenie
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: fa7f72989d47499127714eddfa6b5e98aa80178c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73749224"
---
# <a name="run-powershell-scripts-in-your-windows-vm-by-using-run-command"></a>Uruchamianie skryptów programu PowerShell na maszynie Wirtualnej systemu Windows przy użyciu polecenia Uruchom

Funkcja Uruchom polecenie używa agenta maszyny wirtualnej (VM) do uruchamiania skryptów programu PowerShell w maszynie Wirtualnej systemu Windows platformy Azure. Tych skryptów można używać do ogólnego zarządzania maszynami lub aplikacjami. Mogą one pomóc szybko zdiagnozować i rozwiązać problemy z dostępem do maszyn wirtualnych i sieci i przywrócić maszynę wirtualną do dobrego stanu.

 

## <a name="benefits"></a>Korzyści

Dostęp do maszyn wirtualnych można uzyskać na wiele sposobów. Uruchom polecenie można uruchamiać skrypty na maszynach wirtualnych zdalnie przy użyciu agenta maszyny wirtualnej. Polecenia Uruchom można za pośrednictwem portalu Azure, [interfejsu API REST](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)lub programu [PowerShell](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) dla maszyn wirtualnych z systemem Windows.

Ta funkcja jest przydatna we wszystkich scenariuszach, w których chcesz uruchomić skrypt w maszynie wirtualnej. Jest to jeden z niewielu sposobów rozwiązywania problemów i korygowanie maszyny wirtualnej, która nie ma otwartego portu RDP lub SSH z powodu niewłaściwej konfiguracji sieci lub użytkownika administracyjnego.

## <a name="restrictions"></a>Ograniczenia

Podczas korzystania z polecenia Uruchom polecenie obowiązują następujące ograniczenia:

* Dane wyjściowe są ograniczone do ostatnich 4096 bajtów.
* Minimalny czas uruchamiania skryptu wynosi około 20 sekund.
* Skrypty działają jako system w systemie Windows.
* Jeden skrypt naraz można uruchomić.
* Skrypty monitowane o informacje (tryb interaktywny) nie są obsługiwane.
* Nie można anulować uruchomionego skryptu.
* Maksymalny czas uruchomienia skryptu wynosi 90 minut. Po tym, to będzie limit czasu.
* Łączność wychodząca z maszyny Wirtualnej jest wymagana do zwrócenia wyników skryptu.

> [!NOTE]
> Aby działać poprawnie, Uruchom polecenie wymaga łączności (port 443) do publicznych adresów IP platformy Azure. Jeśli rozszerzenie nie ma dostępu do tych punktów końcowych, skrypty mogą działać pomyślnie, ale nie zwracają wyników. Jeśli blokujesz ruch na maszynie wirtualnej, możesz użyć [tagów usługi,](../../virtual-network/security-overview.md#service-tags) aby `AzureCloud` zezwolić na ruch na publicznych adresach IP platformy Azure przy użyciu tagu.

## <a name="available-commands"></a>Dostępne polecenia

W tej tabeli przedstawiono listę poleceń dostępnych dla maszyn wirtualnych systemu Windows. Za pomocą polecenia **RunPowerShellScript** można uruchomić dowolny skrypt niestandardowy. Podczas korzystania z interfejsu wiersza polecenia platformy Azure lub programu PowerShell `--command-id` `-CommandId` do uruchamiania polecenia, wartość, która jest podasz dla lub parametr musi być jedną z następujących wymienionych wartości. Po określeniu wartości, która nie jest dostępnym poleceniem, pojawia się ten błąd:

```error
The entity was not found in this Azure location
```

|**Nazwa**|**Opis**|
|---|---|
|**Kod RunPowerShellScript**|Uruchamia skrypt programu PowerShell.|
|**EnableRemotePS**|Konfiguruje urządzenie do włączania zdalnego programu PowerShell.|
|**Włącz konto AdminAccount**|Sprawdza, czy lokalne konto administratora jest wyłączone, a jeśli tak, to włącza.|
|**Ipconfig**| Pokazuje szczegółowe informacje dotyczące adresu IP, maski podsieci i bramy domyślnej dla każdej karty powiązanej z usługą TCP/IP.|
|**RDPSettings**|Sprawdza ustawienia rejestru i ustawienia zasad domeny. Sugeruje akcje zasad, jeśli komputer jest częścią domeny lub modyfikuje ustawienia do wartości domyślnych.|
|**ResetRDPCert**|Usuwa certyfikat SSL powiązany z odbiornikiem RDP i przywraca domyślne zabezpieczenia odbiornika RDP. Użyj tego skryptu, jeśli widzisz jakiekolwiek problemy z certyfikatem.|
|**Port SetRDP**|Ustawia domyślny lub określony przez użytkownika numer portu dla połączeń pulpitu zdalnego. Włącza reguły zapory dla dostępu przychodzącego do portu.|

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

W poniższym przykładzie użyto polecenia [az vm run-command](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) do uruchomienia skryptu powłoki na maszynie wirtualnej systemu Windows platformy Azure.

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

## <a name="azure-portal"></a>Portal Azure

Przejdź do maszyny Wirtualnej w [witrynie Azure portal](https://portal.azure.com) i wybierz **polecenie Uruchom w** obszarze **OPERACJE**. Widoczna jest lista dostępnych poleceń do uruchomienia na maszynie Wirtualnej.

![Lista poleceń](./media/run-command/run-command-list.png)

Wybierz polecenie do uruchomienia. Niektóre polecenia mogą mieć opcjonalne lub wymagane parametry wejściowe. Dla tych poleceń parametry są prezentowane jako pola tekstowe, aby zapewnić wartości wejściowe. Dla każdego polecenia można wyświetlić skrypt uruchamiany przez rozwinięcie **skryptu Widok**. **RunPowerShellScript** różni się od innych poleceń, ponieważ pozwala na dostarczenie własnego skryptu niestandardowego.

> [!NOTE]
> Wbudowane polecenia nie są edytowalne.

Po wybraniu polecenia wybierz pozycję **Uruchom,** aby uruchomić skrypt. Po zakończeniu skryptu zwraca dane wyjściowe i wszelkie błędy w oknie danych wyjściowych. Poniższy zrzut ekranu przedstawia przykładowe dane wyjściowe z uruchomienia polecenia **RDPSettings.**

![Uruchom dane wyjściowe skryptu polecenia](./media/run-command/run-command-script-output.png)

## <a name="powershell"></a>PowerShell

W poniższym przykładzie użyto polecenia cmdlet [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) do uruchomienia skryptu programu PowerShell na maszynie Wirtualnej platformy Azure. Polecenie cmdlet oczekuje, że skrypt, do którego odwołuje się `-ScriptPath` parametr, będzie lokalny, do którego jest uruchamiane polecenie cmdlet.

```azurepowershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Ograniczanie dostępu do polecenia Uruchom

Wyświetlanie poleceń uruchamiania lub wyświetlanie szczegółów `Microsoft.Compute/locations/runCommands/read` polecenia wymaga uprawnienia na poziomie subskrypcji. Wbudowana rola [czytnika](../../role-based-access-control/built-in-roles.md#reader) i wyższe poziomy mają to uprawnienie.

Uruchamianie polecenia `Microsoft.Compute/virtualMachines/runCommand/action` wymaga uprawnienia na poziomie subskrypcji. Rola [współautora maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) i wyższe poziomy mają to uprawnienie.

Można użyć jednej z [wbudowanych ról](../../role-based-access-control/built-in-roles.md) lub utworzyć [rolę niestandardową,](../../role-based-access-control/custom-roles.md) aby użyć polecenia Uruchom.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o innych sposobach zdalnego uruchamiania skryptów i poleceń na maszynie wirtualnej, zobacz [Uruchamianie skryptów na maszynie Wirtualnej systemu Windows](run-scripts-in-vm.md).
