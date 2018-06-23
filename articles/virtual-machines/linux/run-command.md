---
title: Uruchamianie skryptów powłoki w maszyny Wirtualnej systemu Linux na platformie Azure
description: W tym temacie opisano sposób uruchamiania skryptów w maszynie wirtualnej systemu Linux platformy Azure przy użyciu polecenia Uruchom
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 93972c88676cceef07b39b4a4ed34bab5b74cbd3
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "36334877"
---
# <a name="run-shell-scripts-in-your-linux-vm-with-run-command"></a>Uruchamianie skryptów powłoki w sieci maszyny Wirtualnej systemu Linux przy użyciu polecenia Uruchom

Uruchom polecenie używa agenta maszyny Wirtualnej do uruchamiania skryptów powłoki w ramach maszyny Wirtualnej systemu Linux na platformie Azure. Skrypty te mogą służyć do zarządzania aplikacji lub głównej maszyny i umożliwia szybkie diagnozowania i rozwiązywania problemów programu access i sieć maszyny Wirtualnej i pobieranie maszyny Wirtualnej do stanu dobrej.

## <a name="benefits"></a>Korzyści

Istnieje wiele opcji, które mogą być używane do dostępu maszyn wirtualnych. Polecenie uruchomienia można uruchamiać skrypty na maszynach wirtualnych zdalnie przy użyciu agenta maszyny Wirtualnej. Uruchom polecenie może być używane za pośrednictwem portalu Azure [interfejsu API REST](/rest/api/compute/virtual%20machines%20run%20commands/runcommand), [interfejsu wiersza polecenia Azure](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke), lub [PowerShell](/powershell/module/azurerm.compute/invoke-azurermvmruncommand).

Ta funkcja jest przydatna we wszystkich scenariuszach, w którym chcesz uruchomić skrypt w maszynach wirtualnych, a jest jednym ze sposobów tylko Rozwiązywanie problemów i korygowanie maszynę wirtualną, która nie ma protokołu RDP lub Otwórz SSH port z powodu nieprawidłowej sieci lub użytkownik administracyjny Konfiguracja.

## <a name="restrictions"></a>Ograniczenia

Poniżej przedstawiono listę ograniczenia, które są dostępne za pomocą polecenia Uruchom.

* Dane wyjściowe są ograniczone do ostatniego 4096 bajtów
* Minimalny czas, aby uruchomić skrypt około 20 sekund
* Skrypty są domyślnie uruchamiane jako użytkownik z podwyższonym poziomem uprawnień w systemie Linux
* Może uruchomić skrypt w czasie
* Skrypty, które monit o podanie informacji (w trybie interaktywnym) nie są obsługiwane.
* Nie można anulować uruchamianie skryptu
* Maksymalny czas, który można uruchomić skryptu wynosi 90 minut, po jakim zostanie limit czasu

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Poniżej przedstawiono przykład za pomocą [polecenia Uruchom az wirtualna](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) polecenie do uruchomienia skryptu powłoki na maszynie Wirtualnej platformy Azure w systemie Linux.

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "sudo apt-get update && sudo apt-get install -y nginx"
```

> [!NOTE]
> Aby uruchomić polecenia jako inny użytkownik, można użyć `sudo -u` Aby określić konto użytkownika, które będzie używane.

## <a name="azure-portal"></a>Azure Portal

Przejdź do maszyny Wirtualnej w ramach [Azure](https://portal.azure.com) i wybierz **Uruchom polecenie** w obszarze **operacji**. Jest wyświetlana lista dostępnych poleceń, aby uruchomić na maszynie Wirtualnej.

![Uruchom polecenie list](./media/run-command/run-command-list.png)

Wybierz polecenie do uruchomienia. Niektóre polecenia może mieć opcjonalne lub wymagane parametry wejściowe. Dla tych poleceń parametry są wyświetlane jako pola tekstowego do dostarczania wartości wejściowych. Dla każdego polecenia można wyświetlić skryptu, który jest uruchamiany po rozwinięciu **Wyświetl skrypt**. **RunShellScript** różni się od innych poleceń, ponieważ umożliwia podanie niestandardowego skryptu. 

> [!NOTE]
> Wbudowane polecenia jest niemożliwa.

Po wybraniu polecenia, kliknij przycisk **Uruchom** do uruchomienia skryptu. Skrypt jest uruchamiany, a po zakończeniu zwraca dane wyjściowe oraz wszelkie błędy w oknie danych wyjściowych. Poniższy zrzut ekranu przedstawia przykład danych wyjściowych uruchamianie **ifconfig** polecenia.

![Uruchom polecenia skryptu w danych wyjściowych](./media/run-command/run-command-script-output.png)

## <a name="available-commands"></a>Dostępne polecenia

Ta tabela zawiera listę poleceń, które są dostępne dla maszyn wirtualnych systemu Linux. **RunShellScript** polecenia może służyć do uruchomienia dowolnego niestandardowego skryptu, ma.

|**Nazwa**|**Opis**|
|---|---|
|**RunShellScript**|Wykonuje skrypt powłoki systemu Linux.|
|**ifconfig**| Pobierz konfigurację wszystkich interfejsów sieciowych.|

## <a name="limiting-access-to-run-command"></a>Ograniczanie dostępu do polecenia Uruchom

Wyświetlanie Uruchom polecenia lub zawierającego szczegóły polecenia wymagają `Microsoft.Compute/locations/runCommands/read` uprawnienia, które wbudowane [czytnika](../../role-based-access-control/built-in-roles.md#reader) roli i mają wyższy.

Uruchomienie polecenia wymaga `Microsoft.Compute/virtualMachines/runCommand/action` uprawnienia, które [współautora](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) roli i wyższych.

Można użyć jednej z [wbudowanych](../../role-based-access-control/built-in-roles.md) ról lub Utwórz [niestandardowych](../../role-based-access-control/custom-roles.md) rolę użyj polecenia Uruchom.

## <a name="next-steps"></a>Kolejne kroki

Zobacz, [uruchamianie skryptów w sieci maszyny Wirtualnej systemu Linux](run-scripts-in-vm.md) Aby dowiedzieć się więcej na temat innych sposobów zdalnie uruchamiać skrypty i polecenia w maszynie Wirtualnej.
