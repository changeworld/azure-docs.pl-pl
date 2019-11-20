---
title: Dodawanie interfejsów sieciowych do maszyn wirtualnych platformy Azure lub usuwanie ich z niej
description: Dowiedz się, jak dodać interfejsy sieciowe do lub usunąć interfejsy sieciowe z maszyn wirtualnych.
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
ms.date: 12/15/2017
ms.author: kumud
ms.openlocfilehash: a55bf014a2da10069e4e6a5f6f4eb4b8cd9ff205
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196750"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Dodawanie interfejsów sieciowych do lub usuwanie interfejsów sieciowych z maszyn wirtualnych

Dowiedz się, jak dodać istniejący interfejs sieciowy podczas tworzenia maszyny wirtualnej platformy Azure lub dodać lub usunąć interfejsy sieciowe z istniejącej maszyny wirtualnej w stanie zatrzymania (bez przydziału). Interfejs sieciowy umożliwia maszynie wirtualnej platformy Azure komunikowanie się z Internetem, platformą Azure i zasobami lokalnymi. Maszyna wirtualna może mieć co najmniej jeden interfejs sieciowy. 

Jeśli musisz dodać, zmienić lub usunąć adresy IP dla interfejsu sieciowego, zobacz [Zarządzanie adresami IP interfejsu sieciowego](virtual-network-network-interface-addresses.md). Jeśli musisz utworzyć, zmienić lub usunąć interfejsy sieciowe, zobacz [Zarządzanie interfejsami sieciowymi](virtual-network-network-interface.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Przed wykonaniem kroków opisanych w sekcji tego artykułu wykonaj następujące zadania:

- Jeśli nie masz jeszcze konta platformy Azure, Utwórz [konto bezpłatnej wersji próbnej](https://azure.microsoft.com/free).
- Jeśli używasz portalu, Otwórz https://portal.azure.comi zaloguj się przy użyciu konta platformy Azure.
- W przypadku wykonywania zadań w tym artykule przy użyciu poleceń programu PowerShell uruchom polecenia w [Azure Cloud Shell](https://shell.azure.com/powershell)lub przez uruchomienie programu PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Ten samouczek wymaga modułu Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.
- W przypadku korzystania z poleceń interfejsu wiersza polecenia (CLI) platformy Azure w celu wykonania zadań w tym artykule Uruchom polecenia w [Azure Cloud Shell](https://shell.azure.com/bash)lub przez uruchomienie interfejsu wiersza polecenia na komputerze. Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.26 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, musisz również uruchomić `az login`, aby utworzyć połączenie z platformą Azure.

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>Dodawanie istniejących interfejsów sieciowych do nowej maszyny wirtualnej

Gdy tworzysz maszynę wirtualną za pomocą portalu, Portal tworzy interfejs sieciowy z ustawieniami domyślnymi i dołącza go do maszyny wirtualnej. Nie można dodać istniejących interfejsów sieciowych do nowej maszyny wirtualnej ani utworzyć maszyny wirtualnej z wieloma interfejsami sieciowymi przy użyciu Azure Portal. Można to zrobić przy użyciu interfejsu wiersza polecenia lub programu PowerShell, ale pamiętaj o zapoznaniu się z [ograniczeniami](#constraints). W przypadku tworzenia maszyny wirtualnej z wieloma interfejsami sieciowymi należy również skonfigurować system operacyjny, aby korzystał z nich prawidłowo po utworzeniu maszyny wirtualnej. Dowiedz się, jak skonfigurować [system](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) lub Windows dla wielu interfejsów sieciowych.

### <a name="commands"></a>Polecenia

Przed utworzeniem maszyny wirtualnej Utwórz interfejs sieciowy, wykonując kroki opisane w temacie [Tworzenie interfejsu sieciowego](virtual-network-network-interface.md#create-a-network-interface).

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|PowerShell|[New-AzVM](/powershell/module/az.compute/new-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>Dodawanie interfejsu sieciowego do istniejącej maszyny wirtualnej

1. Zaloguj się do Portalu Azure.
2. W polu wyszukiwania w górnej części portalu wpisz nazwę maszyny wirtualnej, do której chcesz dodać interfejs sieciowy, lub Przeglądaj w poszukiwaniu maszyny wirtualnej, wybierając pozycję **wszystkie usługi**, a następnie pozycję **maszyny wirtualne**. Po znalezieniu maszyny wirtualnej wybierz ją. Maszyna wirtualna musi obsługiwać liczbę interfejsów sieciowych, które chcesz dodać. Aby dowiedzieć się, ile interfejsów sieciowych obsługuje każdy rozmiar maszyny wirtualnej, zobacz [rozmiary maszyn wirtualnych z systemem Linux na platformie Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [rozmiary maszyn wirtualnych z systemem Windows na platformie Azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  
3. Wybierz pozycję **Przegląd**, a następnie pozycję **Ustawienia**. Wybierz pozycję **Zatrzymaj**, a następnie zaczekaj, aż **stan** maszyny wirtualnej zmieni się na **zatrzymane (cofnięto przydział)** .
4. W obszarze **Ustawienia**wybierz pozycję **Sieć**.
5. Wybierz pozycję **Dołącz interfejs sieciowy**. Z listy interfejsów sieciowych, które nie są aktualnie dołączone do innej maszyny wirtualnej, wybierz tę, którą chcesz dołączyć.

   >[!NOTE]
   >Wybrany interfejs sieciowy nie może mieć włączonej przyspieszonej sieci, nie może mieć przypisanego adresu IPv6 i musi znajdować się w tej samej sieci wirtualnej, która zawiera interfejs sieciowy aktualnie dołączony do maszyny wirtualnej.

   Jeśli nie masz istniejącego interfejsu sieciowego, musisz go najpierw utworzyć. W tym celu wybierz pozycję **Utwórz interfejs sieciowy**. Aby dowiedzieć się więcej na temat tworzenia interfejsu sieciowego, zobacz [Tworzenie interfejsu sieciowego](virtual-network-network-interface.md#create-a-network-interface). Aby dowiedzieć się więcej na temat dodatkowych ograniczeń podczas dodawania interfejsów sieciowych do maszyn wirtualnych, zobacz [ograniczenia](#constraints).

6. Wybierz **OK**.
7. Wybierz pozycję **Przegląd**, w obszarze **Ustawienia**, a następnie **Uruchom** polecenie, aby uruchomić maszynę wirtualną.
8. Skonfiguruj system operacyjny maszyny wirtualnej tak, aby używał wielu interfejsów sieciowych prawidłowo. Dowiedz się, jak skonfigurować [system](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) lub Windows dla wielu interfejsów sieciowych.

### <a name="commands"></a>Polecenia
|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[AZ VM nic Add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json) (odwołanie) lub [Szczegółowa procedura](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (odwołanie) lub [szczegółowe kroki](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>Wyświetlanie interfejsów sieciowych dla maszyny wirtualnej

Możesz wyświetlić interfejsy sieciowe aktualnie dołączone do maszyny wirtualnej, aby dowiedzieć się więcej na temat konfiguracji poszczególnych interfejsów sieciowych i adresów IP przypisanych do każdego interfejsu sieciowego. 

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta, któremu przypisano rolę współautor właściciela, współautora lub sieci dla subskrypcji. Aby dowiedzieć się więcej o sposobie przypisywania ról do kont, zobacz [wbudowane role dla kontroli dostępu opartej na rolach na platformie Azure](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. W polu zawierającym **zasoby wyszukiwania** tekstu w górnej części Azure Portal wpisz **Virtual Machines**. Gdy **maszyny wirtualne** pojawiają się w wynikach wyszukiwania, wybierz je.
3. Wybierz nazwę maszyny wirtualnej, dla której chcesz wyświetlić interfejsy sieciowe.
4. W sekcji **Ustawienia** dla wybranej maszyny wirtualnej wybierz pozycję **Sieć**. Aby dowiedzieć się więcej o ustawieniach interfejsu sieciowego i sposobach ich zmiany, zobacz [Zarządzanie interfejsami sieciowymi](virtual-network-network-interface.md). Aby dowiedzieć się, jak dodawać, zmieniać lub usuwać adresy IP przypisane do interfejsu sieciowego, zobacz [Zarządzanie adresami IP interfejsu sieciowego](virtual-network-network-interface-addresses.md).

### <a name="commands"></a>Polecenia

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|PowerShell|[Get-AzVM](/powershell/module/az.compute/get-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>Usuwanie interfejsu sieciowego z maszyny wirtualnej

1. Zaloguj się do Portalu Azure.
2. W polu wyszukiwania w górnej części portalu Wyszukaj nazwę maszyny wirtualnej, którą chcesz usunąć (Odłącz) od interfejsu sieciowego lub Wyszukaj MASZYNę wirtualną, wybierając pozycję **wszystkie usługi**, a następnie pozycję **maszyny wirtualne**. Po znalezieniu maszyny wirtualnej wybierz ją.
3. Wybierz pozycję **Przegląd**, w obszarze **Ustawienia**, a następnie **Zatrzymaj**. Zaczekaj, aż **stan** maszyny wirtualnej zmieni się na **zatrzymane (cofnięto przydział)** .
4. W obszarze **Ustawienia**wybierz pozycję **Sieć**.
5. Wybierz pozycję **Odłącz interfejs sieciowy**. Z listy interfejsów sieciowych, które są aktualnie dołączone do maszyny wirtualnej, wybierz interfejs sieciowy, który chcesz odłączyć.

   >[!NOTE]
   >Jeśli na liście znajduje się tylko jeden interfejs sieciowy, nie można go odłączyć, ponieważ maszyna wirtualna musi mieć dołączony co najmniej jeden interfejs sieciowy.
6. Wybierz **OK**.

### <a name="commands"></a>Polecenia

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[AZ VM nic Remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json) (odwołanie) lub [szczegółowe kroki](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remove-AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (odwołanie) lub [szczegółowe kroki](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>Ograniczenia

- Do maszyny wirtualnej musi być dołączony co najmniej jeden interfejs sieciowy.
- Maszyna wirtualna może mieć tylko tyle interfejsów sieciowych dołączonych do niej, ponieważ rozmiar maszyny wirtualnej obsługuje. Aby dowiedzieć się więcej o liczbie interfejsów sieciowych obsługiwanych przez poszczególne rozmiary maszyn wirtualnych, zobacz [rozmiary maszyn wirtualnych z systemem Linux na platformie Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [rozmiary maszyn wirtualnych z systemem Windows na platformie Azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Wszystkie rozmiary obsługują co najmniej dwa interfejsy sieciowe.
- Interfejsy sieciowe dodawane do maszyny wirtualnej nie mogą być obecnie dołączone do innej maszyny wirtualnej. Aby dowiedzieć się więcej na temat tworzenia interfejsów sieciowych, zobacz [Tworzenie interfejsu sieciowego](virtual-network-network-interface.md#create-a-network-interface).
- W przeszłości interfejsy sieciowe mogą zostać dodane tylko do maszyn wirtualnych, które obsługują wiele interfejsów sieciowych i zostały utworzone przy użyciu co najmniej dwóch interfejsów sieciowych. Nie można dodać interfejsu sieciowego do maszyny wirtualnej, która została utworzona przy użyciu jednego interfejsu sieciowego, nawet jeśli rozmiar maszyny wirtualnej obsługuje wiele interfejsów sieciowych. Z kolei można usunąć tylko interfejsy sieciowe z maszyny wirtualnej z co najmniej trzema interfejsami sieciowymi, ponieważ maszyny wirtualne utworzone przy użyciu co najmniej dwóch interfejsów sieciowych zawsze musiały mieć co najmniej dwa interfejsy sieciowe. Żadne z tych ograniczeń nie mają już zastosowania. Teraz można utworzyć maszynę wirtualną z dowolną liczbą interfejsów sieciowych (do liczby obsługiwanej przez rozmiar maszyny wirtualnej).
- Domyślnie pierwszy interfejs sieciowy dołączony do maszyny wirtualnej jest zdefiniowany jako *podstawowy* interfejs sieciowy. Wszystkie inne interfejsy sieciowe w maszynie wirtualnej są *dodatkowymi* interfejsami sieciowymi.
- Chociaż można kontrolować interfejs sieciowy, do którego wysyłany jest ruch wychodzący, domyślnie cały ruch wychodzący z maszyny wirtualnej jest wysyłany przy użyciu adresu IP przypisanego do podstawowej konfiguracji adresu IP głównego interfejsu sieciowego.
- W przeszłości wszystkie maszyny wirtualne w tym samym zestawie dostępności musiały mieć jeden lub wiele interfejsów sieciowych. Maszyny wirtualne z dowolną liczbą interfejsów sieciowych mogą teraz istnieć w tym samym zestawie dostępności, do liczby obsługiwanej przez rozmiar maszyny wirtualnej. Możesz dodać maszynę wirtualną do zestawu dostępności, gdy zostanie on utworzony. Aby dowiedzieć się więcej o zestawach dostępności, zobacz [Zarządzanie dostępnością maszyn wirtualnych na platformie Azure](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).
- Interfejsy sieciowe znajdujące się na tej samej maszynie wirtualnej mogą być połączone z różnymi podsieciami w sieci wirtualnej, a wszystkie interfejsy sieciowe muszą być połączone z tą samą siecią wirtualną.
- Można dodać dowolny adres IP dla dowolnej konfiguracji protokołu IP dowolnego podstawowego lub pomocniczego interfejsu sieciowego do puli zaplecza Azure Load Balancer. W przeszłości tylko podstawowy adres IP dla podstawowego interfejsu sieciowego można dodać do puli zaplecza. Aby dowiedzieć się więcej o adresach IP i konfiguracjach, zobacz [Dodawanie, zmienianie lub usuwanie adresów IP](virtual-network-network-interface-addresses.md).
- Usunięcie maszyny wirtualnej nie powoduje usunięcia podłączonych do niej interfejsów sieciowych. Po usunięciu maszyny wirtualnej interfejsy sieciowe są odłączone od maszyny wirtualnej. Możesz dodać interfejsy sieciowe do różnych maszyn wirtualnych lub je usunąć.
- Podobnie jak w przypadku protokołu IPv6, nie można dołączyć interfejsu sieciowego z włączoną obsługą przyspieszonej sieci do maszyny wirtualnej po jej utworzeniu. Dodatkowo, aby korzystać z przyspieszonej sieci, należy również wykonać kroki opisane w systemie operacyjnym maszyny wirtualnej. Dowiedz się więcej na temat przyspieszonej sieci i innych ograniczeń w przypadku korzystania z niej w przypadku maszyn wirtualnych z [systemem Windows](create-vm-accelerated-networking-powershell.md) lub [Linux](create-vm-accelerated-networking-cli.md) .

## <a name="next-steps"></a>Kolejne kroki
Aby utworzyć maszynę wirtualną z wieloma interfejsami sieciowymi lub adresami IP, zobacz następujące artykuły:

|Zadanie|Narzędzie|
|---|---|
|Tworzenie maszyny wirtualnej z wieloma kartami sieciowymi|[Interfejs wiersza polecenia](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), program [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Tworzenie pojedynczej maszyny wirtualnej karty sieciowej z wieloma adresami IPv4|[Interfejs wiersza polecenia](virtual-network-multiple-ip-addresses-cli.md), program [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Utwórz pojedynczą maszynę wirtualną kart sieciowych z prywatnym adresem IPv6 (za Azure Load Balancer)|[Interfejs wiersza polecenia](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), program [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [szablon Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
