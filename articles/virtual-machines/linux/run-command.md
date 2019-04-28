---
title: Uruchamianie skryptów powłoki na Maszynie wirtualnej systemu Linux na platformie Azure
description: W tym temacie opisano sposób uruchamiania skryptów w maszynie wirtualnej systemu Linux platformy Azure przy użyciu polecenia Uruchom
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 10/25/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: e865d4e9cbad2c2064d961bc6e407440ce8556fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60772074"
---
# <a name="run-shell-scripts-in-your-linux-vm-with-run-command"></a>Uruchamianie skryptów powłoki w maszynie Wirtualnej systemu Linux przy użyciu polecenia Uruchom

Uruchom polecenie używa agenta maszyny Wirtualnej, aby uruchomić skrypty powłoki w maszynie Wirtualnej systemu Linux platformy Azure. Skrypty te mogą służyć do ogólnych maszyny lub Zarządzanie aplikacjami i umożliwia szybkie diagnozowanie i rozwiązywanie problemów access i sieć maszyny Wirtualnej oraz uzyskiwanie maszyny Wirtualnej do stanu dobrej.

## <a name="benefits"></a>Korzyści

Istnieje wiele opcji, które mogą służyć do dostępu do maszyn wirtualnych. Uruchom polecenie można uruchamiać skrypty na maszynach wirtualnych zdalnie przy użyciu agenta maszyny Wirtualnej. Uruchom polecenia mogą być używane w witrynie Azure portal, [interfejsu API REST](/rest/api/compute/virtual%20machines%20run%20commands/runcommand), lub [wiersza polecenia platformy Azure](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) maszyn wirtualnych systemu Linux.

Ta możliwość jest przydatna we wszystkich scenariuszach, gdzie chcesz uruchomić skrypt w ramach maszyn wirtualnych i jest jednym ze sposobów tylko rozwiązywanie i korygowanie maszynę wirtualną, która nie ma protokołu RDP lub otworzyć SSH port, z powodu nieprawidłowej sieci lub użytkownik administracyjny Konfiguracja.

## <a name="restrictions"></a>Ograniczenia

Poniżej przedstawiono listę ograniczeń, które występują, gdy za pomocą polecenia Uruchom.

* Dane wyjściowe są ograniczone do ostatnich 4096 bajtów
* Minimalny czas, aby uruchomić skrypt około 20 sekund
* Skrypty są domyślnie uruchamiane jako użytkownik z podwyższonym poziomem uprawnień w systemie Linux
* Jeden skrypt w danym momencie może być uruchamiany
* Skrypty, monit o podanie informacji (tryb interakcyjny), które nie są obsługiwane.
* Nie można anulować uruchamianie skryptu
* Maksymalny czas, który można uruchomić skryptu wynosi 90 minut, po których przekroczy limit czasu
* Łączność wychodząca z maszyny Wirtualnej jest wymagana do zwracania wyników skryptu.

> [!NOTE]
> Aby funkcjonowało poprawnie, uruchom polecenie wymaga łączności (port 443) do platformy Azure publicznych adresów IP. Jeśli rozszerzenie nie ma dostępu do tych punktów końcowych, skrypty może wykonane pomyślnie, ale nie zwracają wartości. Jeśli blokujesz ruchu na maszynie wirtualnej, możesz użyć [tagów usług](../../virtual-network/security-overview.md#service-tags) zezwalająca na ruch do platformy Azure publicznych adresów IP przy użyciu `AzureCloud` tagu.

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Poniżej przedstawiono przykład za pomocą [polecenia Uruchom az vm](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) polecenie, aby uruchomić skrypt powłoki, na maszynie Wirtualnej systemu Linux platformy Azure.

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "sudo apt-get update && sudo apt-get install -y nginx"
```

> [!NOTE]
> Aby uruchamiać polecenia jako inny użytkownik, można użyć `sudo -u` do określenia konta użytkownika do użycia.

## <a name="azure-portal"></a>Azure Portal

Przejdź do maszyny Wirtualnej na platformie [Azure](https://portal.azure.com) i wybierz **Uruchom polecenie** w obszarze **operacji**. Zostanie wyświetlona lista dostępnych poleceń, aby uruchomić na maszynie Wirtualnej.

![Uruchom listy poleceń](./media/run-command/run-command-list.png)

Wybierz polecenie do uruchomienia. Niektóre polecenia może być opcjonalne lub wymagane parametry wejściowe. Dla tych poleceń, parametry są uporządkowane jako pola tekstowe umożliwiające podawanie wartości wejściowych. Dla każdego polecenia, można wyświetlić skryptu, który jest uruchamiany, rozwijając **wyświetlić skryptu**. **RunShellScript** różni się od innych poleceń, ponieważ umożliwia podanie własnego niestandardowego skryptu.

> [!NOTE]
> Wbudowane polecenia nie są edytowalne.

Po wybraniu polecenia kliknij **Uruchom** do uruchomienia skryptu. Skrypt jest uruchamiany, a po zakończeniu zwraca dane wyjściowe i błędów w oknie danych wyjściowych. Poniższy zrzut ekranu przedstawia przykładowe dane wyjściowe uruchamianie **ifconfig** polecenia.

![Uruchom polecenie skryptu w danych wyjściowych](./media/run-command/run-command-script-output.png)

## <a name="available-commands"></a>Dostępne polecenia

Ta tabela zawiera listę dostępnych poleceń dla maszyn wirtualnych systemu Linux. **RunShellScript** polecenie może służyć do dowolnego niestandardowego skryptu, które chcesz uruchomić.

|**Nazwa**|**Opis**|
|---|---|
|**RunShellScript**|Wykonuje skrypt powłoki systemu Linux.|
|**ifconfig**| Pobierz konfigurację wszystkich interfejsów sieciowych.|

## <a name="limiting-access-to-run-command"></a>Ograniczanie dostępu do polecenia Uruchom

Lista uruchamianie poleceń lub przedstawiający szczegółowe informacje o poleceniu wymagają `Microsoft.Compute/locations/runCommands/read` uprawnienie, której wbudowane [czytnika](../../role-based-access-control/built-in-roles.md#reader) roli i nowszej.

Uruchomienie polecenia wymaga `Microsoft.Compute/virtualMachines/runCommand/action` uprawnienia, które [Współautor](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) roli i nowszej.

Możesz użyć jednej z [wbudowanych](../../role-based-access-control/built-in-roles.md) ról lub utworzyć [niestandardowe](../../role-based-access-control/custom-roles.md) rolę, uruchom polecenie.

## <a name="next-steps"></a>Kolejne kroki

Zobacz, [uruchamianie skryptów w maszynie Wirtualnej systemu Linux](run-scripts-in-vm.md) Aby dowiedzieć się więcej na temat innych sposobów, aby zdalnie uruchomić skrypty i polecenia na maszynie wirtualnej.
