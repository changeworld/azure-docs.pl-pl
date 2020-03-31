---
title: Dodawanie interfejsów sieciowych do maszyn wirtualnych platformy Azure lub ich usuwanie
description: Dowiedz się, jak dodawać interfejsy sieciowe do maszyn wirtualnych lub usuwać je z maszyn wirtualnych.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: 4169bfb5da5b1ad13bab0eb01397f7c1fb20b11b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060322"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Dodawanie interfejsów sieciowych do maszyn wirtualnych lub ich usuwanie

Dowiedz się, jak dodać istniejący interfejs sieciowy podczas tworzenia maszyny wirtualnej platformy Azure (VM). Również nauczyć się dodawać lub usuwać interfejsy sieciowe z istniejącej maszyny Wirtualnej w stanie zatrzymany (cofnięto alokacji). Interfejs sieciowy umożliwia maszynie Wirtualnej platformy Azure komunikowanie się z zasobami internetowymi, platformy Azure i lokalnymi. Maszyna wirtualna ma co najmniej jeden interfejs sieciowy. 

Jeśli chcesz dodać, zmienić lub usunąć adresy IP interfejsu sieciowego, zobacz [Zarządzanie adresami IP interfejsu sieciowego](virtual-network-network-interface-addresses.md). Aby utworzyć, zmienić lub usunąć interfejsy sieciowe, zobacz [Zarządzanie interfejsami sieciowymi](virtual-network-network-interface.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Jeśli go nie masz, skonfiguruj konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Wykonaj jedno z następujących zadań przed rozpoczęciem pozostałej części tego artykułu:

- **Użytkownicy portalu:** Zaloguj się do [witryny Azure portal](https://portal.azure.com) za pomocą konta platformy Azure.

- **Użytkownicy programu PowerShell:** Uruchom polecenia w [usłudze Azure Cloud Shell](https://shell.azure.com/powershell)lub uruchom program PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Na karcie Przeglądarka usługi Azure Cloud Shell znajdź listę rozwijana **Wybierz środowisko,** a następnie wybierz program **PowerShell,** jeśli nie jest jeszcze zaznaczona.

    Jeśli używasz programu PowerShell lokalnie, użyj modułu programu Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az.Network`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Uruchom polecenie `Connect-AzAccount`, aby utworzyć połączenia z platformą Azure.

- **Użytkownicy interfejsu wiersza polecenia platformy Azure (CLI):** uruchom polecenia w [usłudze Azure Cloud Shell](https://shell.azure.com/bash)lub uruchom interfejs wiersza polecenia z komputera. Użyj interfejsu wiersza polecenia platformy Azure w wersji 2.0.26 lub nowszej, jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Uruchom polecenie `az login`, aby utworzyć połączenia z platformą Azure.

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>Dodawanie istniejących interfejsów sieciowych do nowej maszyny Wirtualnej

Podczas tworzenia maszyny wirtualnej za pośrednictwem portalu portal tworzy interfejs sieciowy z ustawieniami domyślnymi i dołącza interfejs sieciowy do maszyny wirtualnej. Nie można użyć portalu, aby dodać istniejące interfejsy sieciowe do nowej maszyny Wirtualnej lub utworzyć maszynę wirtualną z wieloma interfejsami sieciowymi. Można to zrobić za pomocą interfejsu wiersza polecenia lub programu PowerShell. Pamiętaj, aby zapoznać się z [ograniczeniami](#constraints). Jeśli tworzysz maszynę wirtualną z wieloma interfejsami sieciowymi, należy również skonfigurować system operacyjny tak, aby używał ich poprawnie po utworzeniu maszyny Wirtualnej. Dowiedz się, jak skonfigurować [system Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) lub [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) dla wielu interfejsów sieciowych.

### <a name="commands"></a>Polecenia

Przed utworzeniem maszyny Wirtualnej [utwórz interfejs sieciowy](virtual-network-network-interface.md#create-a-network-interface).

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-network-nic-create)|
|PowerShell|[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="add-a-network-interface-to-an-existing-vm"></a>Dodawanie interfejsu sieciowego do istniejącej maszyny Wirtualnej

Aby dodać interfejs sieciowy do maszyny wirtualnej:

1. Przejdź do [witryny Azure portal,](https://portal.azure.com) aby znaleźć istniejącą maszynę wirtualną. Wyszukaj i wybierz **maszyny wirtualne**.

2. Wybierz nazwę maszyny Wirtualnej. Maszyna wirtualna musi obsługiwać liczbę interfejsów sieciowych, które chcesz dodać. Aby dowiedzieć się, ile interfejsów sieciowych obsługuje każdy rozmiar maszyny Wirtualnej, zobacz rozmiary na platformie Azure dla [maszyn wirtualnych z systemem Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub maszyn [wirtualnych z systemem Windows.](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

3. Na pasku polecenia maszyny Wirtualnej wybierz pozycję **Zatrzymaj**, a następnie **ok** w oknie dialogowym potwierdzenia. Następnie poczekaj, aż **stan** maszyny Wirtualnej zmieni się na **Zatrzymana (cofnięta alokacja).**

4. Na pasku menu maszyny Wirtualnej wybierz pozycję**Interfejs sieciowy Dołączanie** **sieciowe** > . Następnie w **dołącz istniejący interfejs sieciowy**wybierz interfejs sieciowy, który chcesz dołączyć, a następnie wybierz **przycisk OK**.

    >[!NOTE]
    >Wybrany interfejs sieciowy nie może mieć włączonej przyspieszonej sieci, nie może mieć przypisanego adresu IPv6 i musi istnieć w tej samej sieci wirtualnej z interfejsem sieciowym aktualnie podłączonym do maszyny Wirtualnej.

    Jeśli nie masz istniejącego interfejsu sieciowego, musisz go najpierw utworzyć. Aby to zrobić, wybierz pozycję **Utwórz interfejs sieciowy**. Aby dowiedzieć się więcej o tworzeniu interfejsu sieciowego, zobacz [Tworzenie interfejsu sieciowego](virtual-network-network-interface.md#create-a-network-interface). Aby dowiedzieć się więcej o dodatkowych ograniczeniach podczas dodawania interfejsów sieciowych do maszyn wirtualnych, zobacz [Ograniczenia](#constraints).

5. Na pasku menu maszyny wirtualnej wybierz pozycję **Początek przeglądu,** > **aby** ponownie uruchomić maszynę wirtualną.

Teraz możesz skonfigurować system operacyjny maszyny Wirtualnej tak, aby poprawnie używał wielu interfejsów sieciowych. Dowiedz się, jak skonfigurować [system Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) lub [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) dla wielu interfejsów sieciowych.

### <a name="commands"></a>Polecenia

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az vm nic add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-add) (odwołanie); [szczegółowe kroki](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (odwołanie); [szczegółowe kroki](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>Wyświetl interfejsy sieciowe dla maszyny wirtualnej

Można wyświetlić interfejsy sieciowe aktualnie podłączone do maszyny Wirtualnej, aby dowiedzieć się więcej o konfiguracji każdego interfejsu sieciowego i adresach IP przypisanych do każdego interfejsu sieciowego. 

1. Przejdź do [witryny Azure portal,](https://portal.azure.com) aby znaleźć istniejącą maszynę wirtualną. Wyszukaj i wybierz **maszyny wirtualne**.

    >[!NOTE]
    >Zaloguj się przy użyciu konta, do które przypisano rolę Właściciel, Współautor lub Współautor sieci dla twojej subskrypcji. Aby dowiedzieć się więcej o przypisywaniu ról do kont, zobacz [Wbudowane role kontroli dostępu opartej na rolach platformy Azure](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).

2. Wybierz nazwę maszyny Wirtualnej, dla której chcesz wyświetlić dołączone interfejsy sieciowe.

3. Na pasku menu maszyny Wirtualnej wybierz pozycję **Sieć**.

Aby dowiedzieć się więcej o ustawieniach interfejsu sieciowego i sposobie ich zmiany, zobacz [Zarządzanie interfejsami sieciowymi](virtual-network-network-interface.md). Aby dowiedzieć się, jak dodawać, zmieniać lub usuwać adresy IP przypisane do interfejsu sieciowego, zobacz [Zarządzanie adresami IP interfejsu sieciowego](virtual-network-network-interface-addresses.md).

### <a name="commands"></a>Polecenia

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az vm nic lista](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-list)|
|PowerShell|[Get-AzVM ( Get-AzVM )](/powershell/module/az.compute/get-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>Usuwanie interfejsu sieciowego z maszyny Wirtualnej

1. Przejdź do [witryny Azure portal,](https://portal.azure.com) aby znaleźć istniejącą maszynę wirtualną. Wyszukaj i wybierz **maszyny wirtualne**.

2. Wybierz nazwę maszyny Wirtualnej, dla której chcesz wyświetlić dołączone interfejsy sieciowe.

3. Na pasku narzędzi maszyny Wirtualnej wybierz przycisk **Zatrzymaj**.

4. Poczekaj, aż **stan** maszyny Wirtualnej zmieni się na **Zatrzymana (cofnięta alokacja).**

5. Na pasku menu maszyny Wirtualnej wybierz pozycję**Interfejs sieciowy Odłączanie** **sieciowe** > .

6. W oknie dialogowym **Odłączanie interfejsu sieciowego** wybierz interfejs sieciowy, który chcesz odłączyć. Następnie wybierz przycisk **OK**.

    >[!NOTE]
    >Jeśli na liście znajduje się tylko jeden interfejs sieciowy, nie można go odłączyć, ponieważ maszyna wirtualna musi zawsze mieć co najmniej jeden interfejs sieciowy podłączony do niego.

### <a name="commands"></a>Polecenia

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az vm nic usunąć](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-remove) (odniesienie); [szczegółowe kroki](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Usuń-AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (odwołanie); [szczegółowe kroki](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>Ograniczenia

- Maszyna wirtualna musi mieć co najmniej jeden interfejs sieciowy podłączony do niego.

- Maszyna wirtualna może mieć tylko tyle interfejsów sieciowych dołączonych do niego, jak obsługuje rozmiar maszyny Wirtualnej. Aby dowiedzieć się więcej o liczbie interfejsów sieciowych, które obsługuje każdy rozmiar maszyny wirtualnej, zobacz rozmiary na platformie Azure dla [maszyn wirtualnych z systemem Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub maszyn [wirtualnych z systemem Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Wszystkie rozmiary obsługują co najmniej dwa interfejsy sieciowe.

- Interfejsów sieciowych dodawanych do maszyny Wirtualnej nie można obecnie dołączyć do innej maszyny Wirtualnej. Aby dowiedzieć się więcej o tworzeniu interfejsów sieciowych, zobacz [Tworzenie interfejsu sieciowego](virtual-network-network-interface.md#create-a-network-interface).

- W przeszłości można było dodawać interfejsy sieciowe tylko do maszyn wirtualnych, które obsługiwały wiele interfejsów sieciowych i zostały utworzone przy użyciu co najmniej dwóch interfejsów sieciowych. Nie można dodać interfejsu sieciowego do maszyny Wirtualnej, która została utworzona przy jednym interfejsie sieciowym, nawet jeśli rozmiar maszyny Wirtualnej obsługiwał więcej niż jeden interfejs sieciowy. Z drugiej strony można usunąć tylko interfejsy sieciowe z maszyny Wirtualnej z co najmniej trzema interfejsami sieciowymi, ponieważ maszyny wirtualne utworzone przy pomocą co najmniej dwóch interfejsów sieciowych zawsze musiały mieć co najmniej dwa interfejsy sieciowe. Te ograniczenia nie mają już zastosowania. Teraz można utworzyć maszynę wirtualną z dowolną liczbą interfejsów sieciowych (do numeru obsługiwanego przez rozmiar maszyny Wirtualnej).

- Domyślnie pierwszym interfejsem sieciowym dołączonym do maszyny Wirtualnej jest podstawowy interfejs *sieciowy.* Wszystkie inne interfejsy sieciowe na maszynie Wirtualnej są *pomocniczymi* interfejsami sieciowymi.

- Można kontrolować interfejs sieciowy, do którego wysyłasz ruch wychodzący. Jednak maszyna wirtualna domyślnie wysyła cały ruch wychodzący do adresu IP, który jest przypisany do podstawowej konfiguracji IP podstawowego interfejsu sieciowego.

- W przeszłości wszystkie maszyny wirtualne w ramach tego samego zestawu dostępności były wymagane do jednego lub wielu interfejsów sieciowych. Maszyny wirtualne z dowolną liczbą interfejsów sieciowych mogą teraz istnieć w tym samym zestawie dostępności, do liczby obsługiwanej przez rozmiar maszyny Wirtualnej. Maszynę Wirtualną można dodać tylko do zestawu dostępności, gdy jest tworzona. Aby dowiedzieć się więcej o zestawach dostępności, zobacz [Zarządzanie dostępnością maszyn wirtualnych na platformie Azure](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).

- Interfejsy sieciowe w tej samej maszynie wirtualnej można łączyć z różnymi podsieciami w sieci wirtualnej. Jednak wszystkie interfejsy sieciowe muszą być połączone z tą samą siecią wirtualną.

- Do puli zaplecza modułu równoważenia obciążenia platformy Azure można dodać dowolny adres IP dla dowolnej konfiguracji IP dowolnego podstawowego lub pomocniczego interfejsu sieciowego. W przeszłości tylko podstawowy adres IP dla podstawowego interfejsu sieciowego można było dodać do puli zaplecza. Aby dowiedzieć się więcej o adresach IP i konfiguracjach, zobacz [Dodawanie, zmienianie lub usuwanie adresów IP](virtual-network-network-interface-addresses.md).

- Usunięcie maszyny Wirtualnej nie powoduje usunięcia interfejsów sieciowych, które są do niej dołączone. Po usunięciu maszyny Wirtualnej interfejsy sieciowe są odłączane od maszyny Wirtualnej. Można dodać te interfejsy sieciowe do różnych maszyn wirtualnych lub usunąć je.

- Podobnie jak w przypadku IPv6, po utworzeniu nie można dołączyć interfejsu sieciowego z przyspieszoną obsługą sieciową do maszyny Wirtualnej. Ponadto, aby skorzystać z przyspieszonej sieci, należy również wykonać kroki w systemie operacyjnym maszyny Wirtualnej. Dowiedz się więcej o przyspieszonej sieci i innych ograniczeniach podczas korzystania z niej dla maszyn wirtualnych [z systemem Windows](create-vm-accelerated-networking-powershell.md) lub [Linux.](create-vm-accelerated-networking-cli.md)

## <a name="next-steps"></a>Następne kroki

Aby utworzyć maszynę wirtualną z wieloma interfejsami sieciowymi lub adresami IP, zobacz:

|Zadanie|Narzędzie|
|---|---|
|Tworzenie maszyny wirtualnej z wieloma kartami sieciowymi|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Program PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Tworzenie pojedynczej maszyny wirtualnej karty sieciowej z wieloma adresami IPv4|[CLI](virtual-network-multiple-ip-addresses-cli.md), [Program PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Tworzenie pojedynczej maszyny Wirtualnej karty sieciowej z prywatnym adresem IPv6 (za modułem równoważenia obciążenia platformy Azure)|[Szablon interfejsu wiersza polecenia](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [programu PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [usługi Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
