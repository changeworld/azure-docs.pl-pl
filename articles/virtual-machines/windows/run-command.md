---
title: Uruchamiaj skrypty programu PowerShell na maszynie Wirtualnej Windows na platformie Azure
description: W tym temacie opisano sposób uruchamiania skryptów programu PowerShell na maszynie wirtualnej Windows Azure przy użyciu polecenia Uruchom
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: 23973445992ceaeb0cd3bc0589665f2fac5b64e5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64575340"
---
# <a name="run-powershell-scripts-in-your-windows-vm-with-run-command"></a>Uruchamiaj skrypty programu PowerShell na maszynie wirtualnej Windows za pomocą polecenia Uruchom

Uruchom polecenie używa agenta maszyny Wirtualnej, aby uruchomić skrypty programu PowerShell w ramach maszyny Wirtualnej z systemem Windows Azure. Skrypty te mogą służyć do ogólnych maszyny lub Zarządzanie aplikacjami i umożliwia szybkie diagnozowanie i rozwiązywanie problemów access i sieć maszyny Wirtualnej oraz uzyskiwanie maszyny Wirtualnej do stanu dobrej.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="benefits"></a>Korzyści

Istnieje wiele opcji, które mogą służyć do dostępu do maszyn wirtualnych. Uruchom polecenie można uruchamiać skrypty na maszynach wirtualnych zdalnie przy użyciu agenta maszyny Wirtualnej. Uruchom polecenia mogą być używane w witrynie Azure portal, [interfejsu API REST](/rest/api/compute/virtual%20machines%20run%20commands/runcommand), lub [PowerShell](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) dla maszyn wirtualnych Windows.

Ta możliwość jest przydatna we wszystkich scenariuszach, gdzie chcesz uruchomić skrypt w ramach maszyn wirtualnych i jest jednym ze sposobów tylko rozwiązywanie i korygowanie maszynę wirtualną, która nie ma protokołu RDP lub otworzyć SSH port, z powodu nieprawidłowej sieci lub użytkownik administracyjny Konfiguracja.

## <a name="restrictions"></a>Ograniczenia

Korzystając z polecenia Uruchom obowiązują następujące ograniczenia:

* Dane wyjściowe są ograniczone do ostatnich 4096 bajtów
* Minimalny czas do uruchomienia skryptu to około 20 sekund
* Skrypty uruchamiane jako System na Windows
* Jeden skrypt w danym momencie może być uruchamiany
* Skrypty, monit o podanie informacji (tryb interakcyjny), które nie są obsługiwane.
* Nie można anulować uruchamianie skryptu
* Maksymalny czas, który można uruchomić skryptu wynosi 90 minut, po których przekroczy limit czasu
* Łączność wychodząca z maszyny Wirtualnej jest wymagana do zwracania wyników skryptu.

> [!NOTE]
> Aby funkcjonowało poprawnie, uruchom polecenie wymaga łączności (port 443) do platformy Azure publicznych adresów IP. Jeśli rozszerzenie nie ma dostępu do tych punktów końcowych, skrypty może wykonane pomyślnie, ale nie zwracają wartości. Jeśli blokujesz ruchu na maszynie wirtualnej, możesz użyć [tagów usług](../../virtual-network/security-overview.md#service-tags) zezwalająca na ruch do platformy Azure publicznych adresów IP przy użyciu `AzureCloud` tagu.

## <a name="run-a-command"></a>Uruchom polecenie

Przejdź do maszyny Wirtualnej na platformie [Azure](https://portal.azure.com) i wybierz **Uruchom polecenie** w obszarze **operacji**. Zostanie wyświetlona lista dostępnych poleceń, aby uruchomić na maszynie Wirtualnej.

![Uruchom listy poleceń](./media/run-command/run-command-list.png)

Wybierz polecenie do uruchomienia. Niektóre polecenia może być opcjonalne lub wymagane parametry wejściowe. Dla tych poleceń, parametry są uporządkowane jako pola tekstowe umożliwiające podawanie wartości wejściowych. Dla każdego polecenia, można wyświetlić skryptu, który jest uruchamiany, rozwijając **wyświetlić skryptu**. **RunPowerShellScript** różni się od innych poleceń, ponieważ umożliwia podanie własnego niestandardowego skryptu.

> [!NOTE]
> Wbudowane polecenia nie są edytowalne.

Po wybraniu polecenia kliknij **Uruchom** do uruchomienia skryptu. Skrypt jest uruchamiany, a po zakończeniu zwraca dane wyjściowe i błędów w oknie danych wyjściowych. Poniższy zrzut ekranu przedstawia przykładowe dane wyjściowe uruchamianie **RDPSettings** polecenia.

![Uruchom polecenie skryptu w danych wyjściowych](./media/run-command/run-command-script-output.png)

## <a name="commands"></a>Polecenia

Ta tabela zawiera listę dostępnych poleceń dla maszyn wirtualnych Windows. **RunPowerShellScript** polecenie może służyć do dowolnego niestandardowego skryptu, które chcesz uruchomić.

|**Nazwa**|**Opis**|
|---|---|
|**RunPowerShellScript**|Uruchamia skrypt programu PowerShell|
|**EnableRemotePS**|Konfiguruje maszynę, aby włączyć zdalne programu PowerShell.|
|**EnableAdminAccount**|Sprawdza, czy jeśli konta administratora lokalnego jest wyłączona, a jeśli tak włączy ją.|
|**IPConfig**| Przedstawia szczegółowe informacje dla adresu IP adres bramy domyślne i maski podsieci dla każdej karty sieciowej, powiązany z protokołem TCP/IP.|
|**RDPSettings**|Umożliwia sprawdzenie ustawień rejestru i ustawienia zasad domeny. Sugeruje akcje zasad, jeśli komputer jest częścią domeny lub modyfikuje ustawienia do wartości domyślnych.|
|**ResetRDPCert**|Usuwa certyfikat SSL powiązany odbiornik protokołu RDP i przywraca zabezpieczeń listerner protokołu RDP do ustawień domyślnych. Użyj tego skryptu, jeśli napotkasz jakiekolwiek problemy z certyfikatem.|
|**SetRDPPort**|Ustawia domyślną lub użytkownika określony numer portu dla połączeń usług pulpitu zdalnego. Włącza reguły zapory dla dostępu przychodzącego do portu.|

## <a name="powershell"></a>PowerShell

Poniżej przedstawiono przykład za pomocą [Invoke AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) polecenia cmdlet, aby uruchomić skrypt programu PowerShell na Maszynie wirtualnej platformy Azure. Polecenie cmdlet oczekuje skryptu, do którego odwołuje się `-ScriptPath` parametru, aby określać elementy lokalne, na którym uruchomiono polecenie cmdlet.


```azurepowershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Ograniczanie dostępu do polecenia Uruchom

Lista uruchamianie poleceń lub przedstawiający szczegółowe informacje o poleceniu wymagają `Microsoft.Compute/locations/runCommands/read` uprawnienia na poziomie subskrypcji której wbudowane [czytnika](../../role-based-access-control/built-in-roles.md#reader) roli i nowszej.

Uruchomienie polecenia wymaga `Microsoft.Compute/virtualMachines/runCommand/action` uprawnienia na poziomie subskrypcji, która [Współautor maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) roli i nowszej.

Możesz użyć jednej z [wbudowanych](../../role-based-access-control/built-in-roles.md) ról lub utworzyć [niestandardowe](../../role-based-access-control/custom-roles.md) rolę, uruchom polecenie.

## <a name="next-steps"></a>Kolejne kroki

Zobacz, [uruchamianie skryptów w maszynie Wirtualnej Windows](run-scripts-in-vm.md) Aby dowiedzieć się więcej na temat innych sposobów, aby zdalnie uruchomić skrypty i polecenia na maszynie wirtualnej.
