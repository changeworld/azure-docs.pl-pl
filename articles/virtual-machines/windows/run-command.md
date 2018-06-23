---
title: Uruchamiać skrypty programu PowerShell w maszynie Wirtualnej platformy Windows Azure
description: W tym temacie opisano sposób uruchamiania skryptów programu PowerShell na maszynie wirtualnej Azure Windows za pomocą polecenia Uruchom
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: ddbac24020110e32792286a1ac64070316cfb081
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "36332718"
---
# <a name="run-powershell-scripts-in-your-windows-vm-with-run-command"></a>Uruchamiać skrypty programu PowerShell w sieci maszyny Wirtualnej systemu Windows za pomocą polecenia Uruchom

Uruchom polecenie używa agenta maszyny Wirtualnej do uruchamiania skryptów powłoki PowerShell w ramach maszyny Wirtualnej systemu Windows Azure. Skrypty te mogą służyć do zarządzania aplikacji lub głównej maszyny i umożliwia szybkie diagnozowania i rozwiązywania problemów programu access i sieć maszyny Wirtualnej i pobieranie maszyny Wirtualnej do stanu dobrej.

## <a name="benefits"></a>Korzyści

Istnieje wiele opcji, które mogą być używane do dostępu maszyn wirtualnych. Polecenie uruchomienia można uruchamiać skrypty na maszynach wirtualnych zdalnie przy użyciu agenta maszyny Wirtualnej. Uruchom polecenie może być używane za pośrednictwem portalu Azure [interfejsu API REST](/rest/api/compute/virtual%20machines%20run%20commands/runcommand), [interfejsu wiersza polecenia Azure](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke), lub [PowerShell](/powershell/module/azurerm.compute/invoke-azurermvmruncommand).

Ta funkcja jest przydatna we wszystkich scenariuszach, w którym chcesz uruchomić skrypt w maszynach wirtualnych, a jest jednym ze sposobów tylko Rozwiązywanie problemów i korygowanie maszynę wirtualną, która nie ma protokołu RDP lub Otwórz SSH port z powodu nieprawidłowej sieci lub użytkownik administracyjny Konfiguracja.

## <a name="restrictions"></a>Ograniczenia

Korzystając z polecenia Uruchom, obowiązują następujące ograniczenia:

* Dane wyjściowe jest ograniczony do ostatnich 4096 bajtów.
* Minimalny czas do uruchomienia skryptu to około 20 sekund
* Skrypty uruchamiane jako System w systemie Windows
* Może uruchomić skrypt w czasie
* Nie można anulować uruchamianie skryptu
* Maksymalny czas, który można uruchomić skryptu wynosi 90 minut, po jakim zostanie limit czasu

**PermissionsConfig-OrchestratorUsersGroup***GroupName***-OrchestratorUser***UserName***\-remote** 

## <a name="run-a-command"></a>Uruchom polecenie

Przejdź do maszyny Wirtualnej w ramach [Azure](https://portal.azure.com) i wybierz **Uruchom polecenie** w obszarze **operacji**. Jest wyświetlana lista dostępnych poleceń, aby uruchomić na maszynie Wirtualnej.

![Uruchom polecenie list](./media/run-command/run-command-list.png)

Wybierz polecenie do uruchomienia. Niektóre polecenia może mieć opcjonalne lub wymagane parametry wejściowe. Dla tych poleceń parametry są wyświetlane jako pola tekstowego do dostarczania wartości wejściowych. Dla każdego polecenia można wyświetlić skryptu, który jest uruchamiany po rozwinięciu **Wyświetl skrypt**. **RunPowerShellScript** różni się od innych poleceń, ponieważ umożliwia podanie niestandardowego skryptu.

> [!NOTE]
> Wbudowane polecenia jest niemożliwa.

Po wybraniu polecenia, kliknij przycisk **Uruchom** do uruchomienia skryptu. Skrypt jest uruchamiany, a po zakończeniu zwraca dane wyjściowe oraz wszelkie błędy w oknie danych wyjściowych. Poniższy zrzut ekranu przedstawia przykład danych wyjściowych uruchamianie **RDPSettings** polecenia.

![Uruchom polecenia skryptu w danych wyjściowych](./media/run-command/run-command-script-output.png)

## <a name="commands"></a>Polecenia

Ta tabela zawiera listę poleceń, które są dostępne dla maszyn wirtualnych systemu Windows. **RunPowerShellScript** polecenia może służyć do uruchomienia dowolnego niestandardowego skryptu, ma.

|**Nazwa**|**Opis**|
|---|---|
|**RunPowerShellScript**|Wykonuje skrypt programu PowerShell|
|**EnableRemotePS**|Konfiguruje maszynie, aby umożliwić zdalne programu PowerShell.|
|**EnableAdminAccount**|Sprawdza, czy konta administratora lokalnego jest wyłączona, a jeśli tak umożliwia.|
|**Polecenie IPConfig**| Przedstawia szczegółowe informacje dla adresu IP adresów podsieci bramy maski i domyślne dla każdej karty powiązany z protokołem TCP/IP.|
|**RDPSettings**|Sprawdza ustawienia rejestru i ustawienia zasad domeny. Akcje zasady sugeruje, jeśli komputer jest częścią domeny lub modyfikuje ustawienia do wartości domyślnych.|
|**ResetAccountPassword**| Resetuje hasło konta administratora wbudowanych.|
|**ResetRDPCert**|Usuwa certyfikat SSL powiązany odbiornika protokołu RDP i przywraca zabezpieczeń listerner RDP do ustawień domyślnych. Jeśli widać jakiekolwiek problemy dotyczące certyfikatu, należy użyć tego skryptu.|
|**SetRDPPort**|Ustawia domyślną lub użytkownik określony numer portu dla połączeń pulpitu zdalnego. Włącza reguły zapory dla ruchu przychodzącego dostępu do portu.|

## <a name="powershell"></a>PowerShell

Poniżej przedstawiono przykład za pomocą [Invoke AzureRmVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand) polecenia cmdlet, aby uruchomić skrypt programu PowerShell na maszynie Wirtualnej platformy Azure.

```azurepowershell-interactive
Invoke-AzureRmVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Ograniczanie dostępu do polecenia Uruchom

Wyświetlanie Uruchom polecenia lub zawierającego szczegóły polecenia wymagają `Microsoft.Compute/locations/runCommands/read` uprawnienia, które wbudowane [czytnika](../../role-based-access-control/built-in-roles.md#reader) roli i mają wyższy.

Uruchomienie polecenia wymaga `Microsoft.Compute/virtualMachines/runCommand/action` uprawnienia, które [współautora](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) roli i wyższych.

Można użyć jednej z [wbudowanych](../../role-based-access-control/built-in-roles.md) ról lub Utwórz [niestandardowych](../../role-based-access-control/custom-roles.md) rolę użyj polecenia Uruchom.

## <a name="next-steps"></a>Kolejne kroki

Zobacz, [uruchamianie skryptów w maszynie Wirtualnej Windows](run-scripts-in-vm.md) Aby dowiedzieć się więcej na temat innych sposobów zdalnie uruchamiać skrypty i polecenia w maszynie Wirtualnej.