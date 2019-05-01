---
title: Dodawanie interfejsów sieciowych lub usuwanie z maszyn wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać interfejsy sieciowe do lub usuwanie interfejsów sieciowych z maszyn wirtualnych.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
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
ms.openlocfilehash: 23e46290af6bdb4c217d8fa0cd836673652fc81d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64701371"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Dodawanie interfejsów sieciowych lub usuwanie interfejsów sieciowych z maszyn wirtualnych

Dowiedz się, jak dodać istniejący interfejs sieciowy, podczas tworzenia maszyny wirtualnej (VM) platformy Azure lub dodawanie lub usuwanie interfejsów sieciowych z istniejącej maszyny Wirtualnej w stanie zatrzymania (przydział zostanie cofnięty). Interfejs sieciowy umożliwia maszynie wirtualnej platformy Azure do komunikowania się z Internetem, Azure i zasobami lokalnymi. Maszyna wirtualna może mieć co najmniej jeden interfejs sieciowy. 

Jeśli potrzebujesz dodać, zmienić, lub usunąć adresy IP dla interfejsu sieciowego, zobacz [zarządzania adresami IP interfejsu sieciowego](virtual-network-network-interface-addresses.md). Jeśli potrzebujesz do tworzenia, zmienić, lub usuwanie interfejsów sieciowych, zobacz [zarządzanie interfejsami sieciowymi](virtual-network-network-interface.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Przed wykonaniem kroków w żadnej sekcji tego artykułu, należy wykonać następujące zadania:

- Jeśli nie masz jeszcze konta platformy Azure, należy zasubskrybować [konto bezpłatnej wersji próbnej](https://azure.microsoft.com/free).
- Jeśli przy użyciu portalu, otwórz https://portal.azure.comi zaloguj się przy użyciu konta platformy Azure.
- Jeśli za pomocą poleceń programu PowerShell w celu wykonania zadań w tym artykule, albo Uruchom polecenia [usługi Azure Cloud Shell](https://shell.azure.com/powershell), lub korzystając z polecenia programu PowerShell na komputerze. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Ten samouczek wymaga programu Azure PowerShell w wersji modułu 1.0.0 lub nowszym. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.
- Jeśli za pomocą poleceń interfejsu wiersza polecenia platformy Azure (CLI) w celu wykonania zadań w tym artykule albo Uruchom polecenia [usługi Azure Cloud Shell](https://shell.azure.com/bash), lub korzystając z polecenia interfejsu wiersza polecenia na komputerze. Ten samouczek wymaga interfejsu wiersza polecenia Azure w wersji 2.0.26 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, trzeba będzie również uruchomić `az login` do utworzenia połączenia z platformą Azure.

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>Dodaj istniejące interfejsy sieciowe do nowej maszyny Wirtualnej

Podczas tworzenia maszyny wirtualnej za pośrednictwem portalu, portal tworzy interfejs sieciowy przy użyciu ustawień domyślnych i dołącza go do maszyny Wirtualnej dla Ciebie. Nie można dodać istniejące interfejsy sieciowe do nowej maszyny Wirtualnej ani tworzenie maszyny Wirtualnej z wieloma interfejsami sieciowymi przy użyciu witryny Azure portal. Możesz wykonać obie czynności użyciu interfejsu wiersza polecenia lub programu PowerShell, ale pamiętaj zapoznać się z [ograniczenia](#constraints). Jeśli tworzysz Maszynę wirtualną z wieloma interfejsami sieciowymi, możesz również skonfigurować system operacyjny, który z nich korzystać prawidłowo po utworzeniu maszyny Wirtualnej. Dowiedz się, jak skonfigurować [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) lub [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) wielu interfejsów sieciowych.

### <a name="commands"></a>Polecenia

Przed utworzeniem maszyny Wirtualnej, utworzyć interfejsu sieciowego wykonując kroki opisane w [Utwórz interfejs sieciowy](virtual-network-network-interface.md#create-a-network-interface).

|Tool|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|PowerShell|[New-AzVM](/powershell/module/az.compute/new-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>Dodawanie interfejsu sieciowego do istniejącej maszyny Wirtualnej

1. Zaloguj się do Portalu Azure.
2. W polu wyszukiwania w górnej części portalu wpisz nazwę maszyny Wirtualnej, do którego chcesz dodać interfejs sieciowy lub Przeglądaj w poszukiwaniu maszyny Wirtualnej, wybierając **wszystkich usług**, a następnie **maszyn wirtualnych**. Po znalezieniu maszyny Wirtualnej, wybierz ją. Maszyna wirtualna musi obsługiwać liczbę interfejsów sieciowych, które chcesz dodać. Aby dowiedzieć się, jak wiele sieci interfejsy każdego rozmiaru maszyny Wirtualnej obsługuje, zobacz [rozmiary maszyn wirtualnych systemu Linux na platformie Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [rozmiary dla Windows maszyn wirtualnych na platformie Azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  
3. Wybierz **Przegląd**w obszarze **ustawienia**. Wybierz **zatrzymać**, a następnie poczekaj, aż do **stan** maszyny wirtualnej zmieni się na **zatrzymane (cofnięty przydział)**.
4. Wybierz **sieć**w obszarze **ustawienia**.
5. Wybierz **dołączanie interfejsu sieciowego**. Z listy interfejsów sieciowych, które nie są obecnie dołączone do innej maszyny Wirtualnej wybierz ten, który chcesz dołączyć.

   >[!NOTE]
   >Interfejs sieciowy, którą wybierzesz nie mają funkcji przyspieszonej łączności sieciowej włączone, nie może mieć przypisanego adresu IPv6 i muszą znajdować się w tej samej sieci wirtualnej, która zawiera interfejs sieciowy obecnie dołączona do maszyny Wirtualnej.

   Jeśli nie masz istniejący interfejs sieciowy, użytkownik musi najpierw utworzyć. Aby to zrobić, wybierz **Utwórz interfejs sieciowy**. Aby dowiedzieć się więcej o sposobie tworzenia interfejsu sieciowego, zobacz [Utwórz interfejs sieciowy](virtual-network-network-interface.md#create-a-network-interface). Aby dowiedzieć się więcej o dodatkowe ograniczenia podczas dodawania interfejsy sieciowe dla maszyn wirtualnych, zobacz [ograniczenia](#constraints).

6. Kliknij przycisk **OK**.
7. Wybierz **Przegląd**w obszarze **ustawienia**, a następnie **Start** można uruchomić maszyny wirtualnej.
8. Konfigurowanie systemu operacyjnego maszyny Wirtualnej, aby prawidłowo używać z wieloma interfejsami sieciowymi. Dowiedz się, jak skonfigurować [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) lub [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) wielu interfejsów sieciowych.

### <a name="commands"></a>Polecenia
|Tool|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[AZ vm nic dodawać](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json) (odwołanie w) lub [szczegółową procedurę](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Dodaj AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (odwołanie w) lub [szczegółową procedurę](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>Wyświetl interfejsy sieciowe maszyny wirtualnej

Możesz wyświetlić interfejsów sieciowych podłączonych do maszyny Wirtualnej, aby dowiedzieć się więcej na temat konfiguracji każdego interfejsu sieciowego i adresów IP przypisanych do każdego interfejsu sieciowego. 

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta, któremu przypisano rolę właściciela, współautora lub Współautor sieci dla Twojej subskrypcji. Aby dowiedzieć się więcej na temat sposobu przypisywania ról do kont, zobacz [wbudowane role kontroli dostępu opartej na rolach na platformie Azure](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. W polu zawierającym tekst **Wyszukaj zasoby** w górnej części witryny Azure portal, wpisz polecenie **maszyn wirtualnych**. Gdy **maszyn wirtualnych** pojawi się w wynikach wyszukiwania, wybierz ją.
3. Wybierz nazwę maszyny Wirtualnej, dla którego chcesz wyświetlić interfejsów sieciowych.
4. W **ustawienia** sekcji dla maszyny Wirtualnej została wybrana, wybierz **sieć**. Aby dowiedzieć się więcej na temat ustawienia interfejsu sieciowego i sposobami ich zmiany, zobacz [zarządzanie interfejsami sieciowymi](virtual-network-network-interface.md). Aby dowiedzieć się więcej na temat Dodawanie, zmienianie lub usuwanie adresów IP przypisanych do interfejsu sieciowego, zobacz [zarządzania adresami IP interfejsu sieciowego](virtual-network-network-interface-addresses.md).

### <a name="commands"></a>Polecenia

|Tool|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|PowerShell|[Get-AzVM](/powershell/module/az.compute/get-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>Usuwanie interfejsu sieciowego maszyny Wirtualnej

1. Zaloguj się do Portalu Azure.
2. W polu wyszukiwania w górnej części portalu, wyszukaj nazwę maszyny wirtualnej, aby usunąć (odłączyć) interfejsu sieciowego, lub Wyszukaj maszynę Wirtualną, wybierając **wszystkich usług**, a następnie **maszyn wirtualnych**. Po znalezieniu maszyny Wirtualnej, wybierz ją.
3. Wybierz **Przegląd**w obszarze **ustawienia**, a następnie **zatrzymać**. Poczekaj, aż **stan** maszyny wirtualnej zmieni się na **zatrzymane (cofnięty przydział)**.
4. Wybierz **sieć**w obszarze **ustawienia**.
5. Wybierz **Odłącz interfejs sieciowy**. Z listy interfejsów sieciowych podłączonych do maszyny wirtualnej wybierz interfejs sieciowy, który chcesz odłączyć.

   >[!NOTE]
   >Jeśli tylko jeden interfejs sieciowy ma na liście, nie można odłączyć, ponieważ maszyna wirtualna zawsze musi mieć co najmniej jeden interfejs sieciowy dołączony do niego.
6. Kliknij przycisk **OK**.

### <a name="commands"></a>Polecenia

|Tool|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[Usuń az vm nic](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json) (odwołanie w) lub [szczegółową procedurę](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Usuń AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (odwołanie w) lub [szczegółową procedurę](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>Ograniczenia

- Maszyna wirtualna musi mieć co najmniej jeden interfejs sieciowy dołączony do niego.
- Maszyny Wirtualnej może mieć tylko jako wiele sieci interfejsów dołączone do niego jako obsługuje rozmiar maszyny Wirtualnej. Aby dowiedzieć się więcej, o ile interfejsach sieciowych obsługuje każdego rozmiaru maszyny Wirtualnej, zobacz [rozmiary maszyn wirtualnych systemu Linux na platformie Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [rozmiary dla Windows maszyn wirtualnych na platformie Azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Wszystkie rozmiary obsługuje co najmniej dwa interfejsy sieciowe.
- Interfejsy sieciowe, które dodasz do maszyny Wirtualnej, obecnie nie można dołączyć do innej maszyny Wirtualnej. Aby dowiedzieć się więcej o sposobie tworzenia interfejsów sieciowych, zobacz [Utwórz interfejs sieciowy](virtual-network-network-interface.md#create-a-network-interface).
- W przeszłości tylko można dodać interfejsów sieciowych do maszyn wirtualnych obsługiwanych wiele interfejsów sieciowych, które zostały utworzone za pomocą co najmniej dwa interfejsy sieciowe. Nie można dodać interfejs sieciowy do maszyny Wirtualnej, który został utworzony z interfejsami sieciowymi, nawet jeśli rozmiar maszyny Wirtualnej obsługuje wiele interfejsów sieciowych. Z drugiej strony można tylko usunąć interfejsów sieciowych z maszyny Wirtualnej z co najmniej trzy interfejsy sieciowe, ponieważ maszyny wirtualne utworzone z siecią co najmniej dwa interfejsy zawsze musi mieć co najmniej dwa interfejsy sieciowe. Żadna z tych warunków ograniczających dłużej zastosowania. Teraz można utworzyć Maszynę wirtualną z dowolnej liczby interfejsów sieciowych (maksymalnie liczby obsługiwanej przez rozmiar maszyny Wirtualnej).
- Domyślnie pierwszy interfejs sieciowy dołączony do maszyny Wirtualnej jest zdefiniowana jako *głównej* interfejsu sieciowego. Wszystkie inne interfejsy sieciowe w maszynie Wirtualnej są *dodatkowej* interfejsy sieciowe.
- Chociaż można kontrolować, które interfejsu sieciowego ruchu wychodzącego, są wysyłane domyślnie, cały ruch wychodzący z maszyny Wirtualnej zostanie wysłane adres IP przypisany do podstawowa konfiguracja adresów IP głównego interfejsu sieciowego.
- W przeszłości wszystkie maszyny wirtualne w tym samym zestawie dostępności musiały mieć jednego lub wielu interfejsów. Maszyny wirtualne z dowolną liczbę interfejsów sieciowych może teraz znajdować się w tym samym zestawie dostępności, maksymalnie do liczby obsługiwanej przez rozmiar maszyny Wirtualnej. Maszynę wirtualną można dodawać tylko do zestaw dostępności podczas jego tworzenia. Aby dowiedzieć się więcej o zestawach dostępności, zobacz [Zarządzanie dostępnością maszyn wirtualnych na platformie Azure](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).
- Chociaż interfejsy sieciowe w tej samej maszyny Wirtualnej, można je podłączyć do różnych podsieci w sieci wirtualnej, interfejsy sieciowe muszą połączone z tej samej sieci wirtualnej.
- Dowolnego adresu IP dla dowolnej konfiguracji IP każdy interfejs sieciowy podstawowy lub pomocniczy można dodać do puli zaplecza modułu równoważenia obciążenia platformy Azure. W przeszłości tylko podstawowy adres IP głównego interfejsu sieciowego można dodać do puli zaplecza. Aby dowiedzieć się więcej na temat adresów IP i konfiguracje, zobacz [Dodawanie, zmienianie lub usuwanie adresów IP](virtual-network-network-interface-addresses.md).
- Usuwanie maszyny Wirtualnej nie powoduje usunięcia interfejsów sieciowych, które są dołączone do niego. Jeśli usuniesz Maszynę wirtualną, interfejsy sieciowe są odłączone od maszyny Wirtualnej. Możesz dodać interfejsy sieciowe do różnych maszyn wirtualnych lub usuń je.
- Jeśli interfejs sieciowy ma do niej przypisany prywatny adres IPv6, należy dodać (dołączanie) do maszyny Wirtualnej podczas tworzenia maszyny Wirtualnej. Nie można dodać interfejs sieciowy przy użyciu adresu IPv6 przypisany do maszyny Wirtualnej, po utworzeniu maszyny Wirtualnej. Jeśli dodasz interfejs sieciowy, za pomocą prywatnego adresu IPv6 przypisany podczas tworzenia maszyny wirtualnej, można tylko dodać ten interfejs sieciowy do maszyny wirtualnej, niezależnie od tego, jak wiele interfejsów sieciowych, które obsługuje rozmiar maszyny Wirtualnej. Zobacz [zarządzania adresami IP interfejsu sieciowego](virtual-network-network-interface-addresses.md) Aby dowiedzieć się więcej na temat sposobu przypisywania adresów IP do interfejsów sieciowych.
- Podobnie jak w przypadku protokołu IPv6, nie można dołączyć interfejsu sieciowego z przyspieszoną siecią włączone do maszyny Wirtualnej, po jego utworzeniu. Co więcej Aby móc korzystać z przyspieszonej łączności sieciowej, należy również wykonać kroki w ramach systemu operacyjnego maszyny Wirtualnej. Dowiedz się więcej na temat przyspieszonej łączności sieciowej i inne ograniczenia, podczas korzystania z niego, aby uzyskać [Windows](create-vm-accelerated-networking-powershell.md) lub [Linux](create-vm-accelerated-networking-cli.md) maszyn wirtualnych.

## <a name="next-steps"></a>Kolejne kroki
Aby utworzyć Maszynę wirtualną z wielu interfejsów sieciowych lub adresów IP, zobacz następujące artykuły:

|Zadanie|Tool|
|---|---|
|Tworzenie maszyny wirtualnej z wieloma kartami sieciowymi|[Interfejs wiersza polecenia](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [programu PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Tworzenie pojedynczej maszyny Wirtualnej karty Sieciowej z wieloma adresami IPv4|[Interfejs wiersza polecenia](virtual-network-multiple-ip-addresses-cli.md), [programu PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Utwórz na jednej maszynie Wirtualnej karty Sieciowej z prywatnym adresem IPv6 (za modułem równoważenia obciążenia platformy Azure)|[Interfejs wiersza polecenia](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [szablonu usługi Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
