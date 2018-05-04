---
title: Interfejsy sieciowe, aby dodać lub usunąć z maszyn wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać interfejsów sieciowych lub usunąć interfejsów sieciowych z maszyn wirtualnych.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: jdial
ms.openlocfilehash: 6193dcc6ba2e78c55ed6c6f769aea50fcfb3ac40
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Dodawanie interfejsów sieciowych lub usuwanie interfejsów sieciowych z maszyn wirtualnych

Dowiedz się, jak dodać istniejący interfejs sieciowy, podczas tworzenia maszyny wirtualnej platformy Azure (VM), lub dodawanie lub usuwanie interfejsów sieciowych z istniejącej maszyny Wirtualnej w stanie zatrzymania (cofnięciu przydziału). Interfejs sieciowy umożliwia maszynie wirtualnej platformy Azure do komunikowania się z Internetem, Azure i lokalnymi zasobami. Maszyna wirtualna może mieć jeden lub więcej interfejsów sieciowych. 

Aby dodać, zmienić, lub usunąć adresy IP dla interfejsu sieciowego, zobacz [zarządzania adresami IP interfejsu sieci](virtual-network-network-interface-addresses.md). Jeśli musisz utworzyć zmian, lub usuń interfejsów sieciowych, zobacz [Zarządzanie interfejsów sieciowych](virtual-network-network-interface.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed wykonaniem kroków w żadnej sekcji tego artykułu, należy wykonać następujące zadania:

- Jeśli nie masz jeszcze konta platformy Azure, należy zarejestrować się w celu [bezpłatnego konta wersji próbnej](https://azure.microsoft.com/free).
- Jeśli przy użyciu portalu, otwórz https://portal.azure.comi zaloguj się przy użyciu konta platformy Azure.
- Jeśli za pomocą poleceń programu PowerShell do wykonywania zadań w tym artykule, albo Uruchom polecenia w [powłoki chmury Azure](https://shell.azure.com/powershell), lub przez uruchomienie programu PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Ten samouczek wymaga programu Azure PowerShell w wersji modułu 5.2.0 lub nowszym. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.
- Jeśli za pomocą poleceń Azure interfejsu wiersza polecenia (CLI), aby wykonać zadania w tym artykule, albo Uruchom polecenia w [powłoki chmury Azure](https://shell.azure.com/bash), lub za pomocą interfejsu wiersza polecenia z tego komputera. Ten samouczek wymaga wiersza polecenia platformy Azure w wersji 2.0.26 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia Azure lokalnie, należy uruchomić `az login` można utworzyć połączenia z platformą Azure.

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>Dodawanie istniejących interfejsów sieciowych do nowej maszyny Wirtualnej

Podczas tworzenia maszyny wirtualnej za pośrednictwem portalu portal tworzy interfejs sieciowy z ustawieniami domyślnymi i dołącza go do maszyny Wirtualnej dla Ciebie. Nie można dodać istniejących interfejsów sieciowych do nowej maszyny Wirtualnej ani tworzenie maszyny Wirtualnej z wieloma interfejsami sieciowymi przy użyciu portalu Azure. Czy zarówno przy użyciu interfejsu wiersza polecenia lub programu PowerShell, ale pamiętaj zapoznać się z [ograniczenia](#constraints). Jeśli tworzysz Maszynę wirtualną z wieloma interfejsami sieciowymi, należy również skonfigurować system operacyjny z nich korzystać prawidłowo po utworzeniu maszyny Wirtualnej. Dowiedz się, jak skonfigurować [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) lub [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) wiele interfejsów sieciowych.

### <a name="commands"></a>Polecenia

Przed utworzeniem maszyny Wirtualnej, należy utworzyć interfejsu sieciowego przy użyciu kroków w [utworzyć interfejs sieciowy](virtual-network-network-interface.md#create-a-network-interface).

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_create)|
|PowerShell|[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>Dodaj do istniejącej maszyny Wirtualnej karty sieciowej

1. Zaloguj się do Portalu Azure.
2. W polu wyszukiwania w górnej części portalu, wpisz nazwę maszyny wirtualnej, do której chcesz dodać interfejsu sieciowego lub Przeglądaj w poszukiwaniu maszynę Wirtualną, wybierając **wszystkie usługi**, a następnie **maszyn wirtualnych**. Po znalezieniu maszyny Wirtualnej, wybierz go. Maszyna wirtualna musi obsługiwać liczba interfejsów sieciowych, który chcesz dodać. Aby dowiedzieć się, ile sieci interfejsy każdego rozmiaru maszyny Wirtualnej obsługuje, zobacz [rozmiary maszyn wirtualnych systemu Linux na platformie Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [rozmiary dla systemu Windows maszyny wirtualne na platformie Azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  
3. Wybierz **omówienie**w obszarze **ustawienia**. Wybierz **zatrzymać**, a następnie poczekaj, aż do **stan** maszyny wirtualnej zmienia się na **zatrzymane (cofnięciu przydziału)**. 
4. Wybierz **sieci**w obszarze **ustawienia**.
5. Wybierz **interfejsu sieciowego Attach**. Z listy interfejsów sieciowych, które aktualnie nie są dołączone do innej maszyny Wirtualnej wybierz ten, który chcesz dołączyć. 

    >[!NOTE]
    Interfejs sieciowy, którą wybierzesz nie można przyspieszyć sieci włączone, nie może mieć przypisanego adresu IPv6 i musi znajdować się w tej samej sieci wirtualnej, która zawiera interfejsu sieciowego, w obecnie dołączony do maszyny Wirtualnej. 

    Jeśli nie masz istniejący interfejs sieciowy, należy najpierw go utworzyć. Aby to zrobić, wybierz **interfejsu sieciowego Utwórz**. Aby dowiedzieć się więcej o sposobie tworzenia interfejsu sieciowego, zobacz [utworzyć interfejs sieciowy](virtual-network-network-interface.md#create-a-network-interface). Aby dowiedzieć się więcej o dodatkowe ograniczenia, dodając interfejsów sieciowych do maszyny wirtualnej, zobacz [ograniczenia](#constraints).

6. Kliknij przycisk **OK**.
7. Wybierz **omówienie**w obszarze **ustawienia**, a następnie **Start** można uruchomić maszyny wirtualnej.
8. Skonfiguruj system operacyjny maszyny Wirtualnej do użycia z wieloma interfejsami sieciowymi poprawnie. Dowiedz się, jak skonfigurować [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) lub [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) wiele interfejsów sieciowych.

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[Dodaj kartę sieciową maszyny wirtualnej az](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_nic_add) (odwołanie) lub [szczegółowe kroki](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Dodaj AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (odwołanie) lub [szczegółowe kroki](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>Wyświetlanie interfejsów sieciowych do maszyny Wirtualnej

Można wyświetlić interfejsów sieciowych podłączonych do maszyn wirtualnych, aby dowiedzieć się więcej na temat konfiguracji każdego interfejsu sieciowego i adresy IP przypisane do każdego interfejsu sieciowego. 

1. Zaloguj się do [portalu Azure](https://portal.azure.com) przy użyciu konta, którego przypisano rolę właściciela, współautora lub współautora sieci dla Twojej subskrypcji. Aby dowiedzieć się więcej na temat sposobu przypisywania ról do kont, zobacz [wbudowanych ról dla kontroli dostępu opartej na rolach na platformie Azure](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. W polu zawierająca tekst, który **wyszukiwania zasobów** w górnej części portalu Azure, wpisz **maszyn wirtualnych**. Gdy **maszyn wirtualnych** pojawia się w wynikach wyszukiwania, wybierz go.
3. Wybierz nazwę maszyny Wirtualnej, dla którego chcesz wyświetlić interfejsów sieciowych.
4. W **ustawienia** sekcji dla maszyny Wirtualnej została wybrana, wybierz **sieci**. Informacje na temat ustawienia interfejsu sieciowego oraz sposobu zmiany ich, zobacz [Zarządzanie interfejsów sieciowych](virtual-network-network-interface.md). Aby dowiedzieć się więcej na temat Dodawanie, zmienianie lub usuwanie adresów IP przypisanych do interfejsu sieciowego, zobacz [zarządzania adresami IP interfejsu sieci](virtual-network-network-interface-addresses.md).

### <a name="commands"></a>Polecenia

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_show)|
|PowerShell|[Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>Usunąć interfejsu sieciowego z maszyny Wirtualnej

1. Zaloguj się do Portalu Azure.
2. W polu wyszukiwania w górnej części portalu, wyszukaj nazwę maszyny wirtualnej do usunięcia (odłączyć) interfejsu sieciowego, lub Wyszukaj maszynę Wirtualną, wybierając **wszystkie usługi**, a następnie **maszyn wirtualnych**. Po znalezieniu maszyny Wirtualnej, wybierz go.
3. Wybierz **omówienie**w obszarze **ustawienia**, a następnie **zatrzymać**. Poczekaj na **stan** maszyny wirtualnej zmienia się na **zatrzymane (cofnięciu przydziału)**. 
4. Wybierz **sieci**w obszarze **ustawienia**.
5. Wybierz **interfejsu sieciowego Detach**. Wybierz interfejs sieciowy, który chcesz odłączyć z listy interfejsów sieciowych podłączonych do maszyny wirtualnej. 

    >[!NOTE]
    Jeśli tylko jeden interfejs sieciowy jest wyświetlana, nie można odłączyć, ponieważ maszyna wirtualna zawsze musi mieć co najmniej jeden interfejs sieciowy do niego dołączony.
6. Kliknij przycisk **OK**.

### <a name="commands"></a>Polecenia

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[Usuń kartę sieciową maszyny wirtualnej az](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_nic_remove) (odwołanie) lub [szczegółowe kroki](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Usuń AzureRMVMNetworkInterface](/powershell/module/azurerm.compute/remove-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (odwołanie) lub [szczegółowe kroki](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>Ograniczenia

- Maszyna wirtualna musi mieć co najmniej jeden interfejs sieciowy do niego dołączony.
- Maszyna wirtualna może mieć tylko jako wiele sieci interfejsy do niego dołączony jako obsługuje rozmiar maszyny Wirtualnej. Aby dowiedzieć się więcej, o ile interfejsów sieciowych obsługuje każdego rozmiaru maszyny Wirtualnej, zobacz [rozmiary maszyn wirtualnych systemu Linux na platformie Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [rozmiary dla systemu Windows maszyny wirtualne na platformie Azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Wszystkich rozmiarów obsługuje co najmniej dwa interfejsy sieciowe.
- Interfejsy sieciowe, które dodasz do maszyny Wirtualnej nie może obecnie dołączony do innej maszyny Wirtualnej. Aby dowiedzieć się więcej o sposobie tworzenia interfejsów sieciowych, zobacz [utworzyć interfejs sieciowy](virtual-network-network-interface.md#create-a-network-interface).
- W przeszłości interfejsów sieciowych można być dodane tylko do maszyn wirtualnych obsługiwanych wiele interfejsów sieciowych, które zostały utworzone z co najmniej dwa interfejsy sieci. Nie można dodać karty sieciowej do maszyny Wirtualnej, który został utworzony z jeden interfejs sieciowy, nawet jeśli rozmiar maszyny Wirtualnej obsługuje wiele interfejsów sieciowych. Z drugiej strony można tylko usunąć interfejsów sieciowych z maszyny Wirtualnej z co najmniej trzech interfejsów sieciowych, ponieważ maszyny wirtualne utworzone z siecią co najmniej dwa interfejsy zawsze musiała mieć co najmniej dwa interfejsy sieciowe. Żadna z tych warunków ograniczających już stosowane. Można teraz utworzyć Maszynę wirtualną z dowolnej liczby interfejsów sieciowych (do liczby obsługiwanych przez rozmiar maszyny Wirtualnej).
- Domyślnie pierwszy interfejs sieciowy dołączony do maszyny Wirtualnej jest zdefiniowany jako *głównej* interfejsu sieciowego. Wszystkie inne interfejsy sieciowe w maszynie Wirtualnej są *dodatkowej* interfejsów sieciowych.
- Chociaż można kontrolować, które interfejs sieciowy wysłany ruch wychodzący, domyślnie, cały ruch wychodzący z maszyny Wirtualnej jest wysłane z adresu IP przypisanego do podstawowej konfiguracji IP interfejsu sieci podstawowej.
- W przeszłości musi mieć interfejsy sieci jednego lub wielu, zostały wszystkich maszyn wirtualnych w ramach tego samego zestawu dostępności. Maszyny wirtualne z dowolnej liczby interfejsy sieciowe można teraz istnieje w tym samym zestawie dostępności, nawet z liczbą obsługiwaną przez rozmiar maszyny Wirtualnej. Można dodać tylko Maszynę wirtualną do zestawu po utworzeniu dostępności. Aby dowiedzieć się więcej na temat zestawów dostępności, zobacz [Zarządzaj dostępnością maszyn wirtualnych na platformie Azure](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).
- Gdy interfejsy sieciowe w tej samej maszyny Wirtualnej można podłączyć do różnych podsieci w sieci wirtualnej, interfejsy sieciowe muszą połączone z tej samej sieci wirtualnej.
- Dowolnego adresu IP dla żadnej konfiguracji adresu IP z żadnym interfejsem sieciowym podstawowy lub pomocniczy można dodać do puli zaplecza modułu równoważenia obciążenia Azure. W przeszłości tylko podstawowy adres IP dla interfejsu sieci podstawowej można można dodać do puli zaplecza. Aby dowiedzieć się więcej na temat adresów IP i konfiguracje, zobacz [Dodaj, Zmień lub Usuń adresy IP](virtual-network-network-interface-addresses.md).
- Usuwanie maszyny Wirtualnej nie powoduje usunięcia interfejsów sieciowych, które są dołączone do niego. Po usunięciu maszyny Wirtualnej interfejsy sieciowe są odłączone od maszyny Wirtualnej. Można dodać interfejsy sieciowe do różnych maszyn wirtualnych lub usuń je.
- Jeśli interfejs sieciowy ma przypisany prywatny adres IPv6, należy dodać (Dołącz) do maszyny Wirtualnej podczas tworzenia maszyny Wirtualnej. Nie można dodać karty sieciowej z adresu IPv6 przypisany do maszyny Wirtualnej, po utworzeniu maszyny Wirtualnej. Jeśli dodasz karty sieciowej z przypisany prywatny adres IPv6, po utworzeniu maszyny wirtualnej, można tylko dodać ten interfejs sieciowy do maszyny wirtualnej, niezależnie od tego, jak wiele interfejsów sieciowych obsługuje rozmiar maszyny Wirtualnej. Zobacz [zarządzania adresami IP interfejsu sieci](virtual-network-network-interface-addresses.md) Aby dowiedzieć się więcej na temat sposobu przypisywania adresów IP do interfejsów sieciowych.
- Podobnie jak w przypadku protokołu IPv6, nie można dołączyć karty sieciowej z siecią przyspieszonego włączony do maszyn wirtualnych, po jego utworzeniu. Co więcej Aby skorzystać z przyspieszonego sieci, musi również wykonaj kroki w ramach systemu operacyjnego maszyny Wirtualnej. Dowiedz się więcej o przyspieszonego sieci i innych ograniczeń w przypadku używania, dla [Windows](create-vm-accelerated-networking-powershell.md) lub [Linux](create-vm-accelerated-networking-cli.md) maszyn wirtualnych.

## <a name="next-steps"></a>Kolejne kroki
Aby utworzyć Maszynę wirtualną z wielu interfejsów sieciowych lub adresów IP, przeczytaj następujące artykuły:

### <a name="commands"></a>Polecenia

|Zadanie|Narzędzie|
|---|---|
|Tworzenie maszyny wirtualnej z wieloma kartami sieciowymi|[Interfejs wiersza polecenia](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [środowiska PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Tworzenie jednej maszyny Wirtualnej karty Sieciowej z wielu adresów IPv4|[Interfejs wiersza polecenia](virtual-network-multiple-ip-addresses-cli.md), [środowiska PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Tworzenie jednej maszyny Wirtualnej karty Sieciowej za pomocą prywatnego adresu IPv6 (za równoważenia obciążenia Azure)|[Interfejs wiersza polecenia](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [szablonu usługi Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|


