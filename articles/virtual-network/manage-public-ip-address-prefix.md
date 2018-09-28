---
title: Tworzenie, zmienianie lub usuwanie Azure publiczny prefiks adresu IP | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzenie, zmienianie i usuwanie publicznej prefiksu adresu IP.
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: anavin
ms.openlocfilehash: 71dee9f36a3e8e11cc23f966c9157f6409a3b5e0
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2018
ms.locfileid: "47405737"
---
# <a name="create-change-or-delete-a-public-ip-address-prefix"></a>Tworzenie, zmienianie i usuwanie publicznej prefiksu adresu IP

Informacje na temat publicznych prefiksu adresu IP i sposobu tworzenia, Zmień i Usuń jeden. Publiczny prefiks adresu IP jest ciągły zakres adresów, na podstawie liczby publicznych adresów IP, które określisz. Te adresy są przypisane do Twojej subskrypcji. Podczas tworzenia zasobu publicznego adresu IP można przypisać statyczny publiczny adres IP z prefiksu i skojarzyć z maszynami wirtualnymi, obciążenia równoważenia lub inne zasoby, aby włączyć łączność z Internetem. Jeśli nie znasz prefiksy publicznych adresów IP, zobacz [Przegląd prefiksu adresu publicznego adresu IP](public-ip-address-prefix.md)

## <a name="before-you-begin"></a>Przed rozpoczęciem

> [!IMPORTANT]
> Prefiks publicznego adresu IP jest w publicznej wersji zapoznawczej w niektórych regionach. Możesz [Dowiedz się, co oznacza w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Publiczny prefiks adresu IP jest obecnie dostępna w: zachodnio-środkowe stany USA, zachodnie stany USA, zachodnie stany USA 2, środkowe stany USA, Europa Północna, Europa Zachodnia i Azja południowo-wschodnia. Aby uzyskać zaktualizowaną listę regionów, odwiedź [aktualizacje platformy Azure](https://azure.microsoft.com/updates/?product=virtual-network).

Przed wykonaniem kroków w żadnej sekcji tego artykułu, należy wykonać następujące zadania:

- Jeśli nie masz jeszcze konta platformy Azure, należy zasubskrybować [konto bezpłatnej wersji próbnej](https://azure.microsoft.com/free).
- Jeśli przy użyciu portalu, otwórz https://aka.ms/publicipprefixportali zaloguj się przy użyciu konta platformy Azure.
- Jeśli za pomocą poleceń programu PowerShell w celu wykonania zadań w tym artykule, albo Uruchom polecenia [usługi Azure Cloud Shell](https://shell.azure.com/powershell), lub korzystając z polecenia programu PowerShell na komputerze. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Ten samouczek wymaga AzureRm.Network modułu PowerShell w wersji 6.3.1 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM.Network`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/AzureRm.Network.6.3.1). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.
- Jeśli za pomocą poleceń interfejsu wiersza polecenia platformy Azure (CLI) w celu wykonania zadań w tym artykule albo Uruchom polecenia [usługi Azure Cloud Shell](https://shell.azure.com/bash), lub korzystając z polecenia interfejsu wiersza polecenia na komputerze. Ten samouczek wymaga interfejsu wiersza polecenia Azure w wersji 2.0.41 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, trzeba będzie również uruchomić `az login` do utworzenia połączenia z platformą Azure.

Konta, zaloguj się do lub łączenie z platformą Azure za pomocą, muszą być przypisane do [Współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roli lub [roli niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) przypisany odpowiednie działania, które są wymienione w [uprawnień ](#permissions).

Prefiksy publicznych adresów IP mają opłaty. Aby uzyskać więcej informacji, zobacz [ceny](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="create-a-public-ip-address-prefix"></a>Utwórz publiczny prefiks adresu IP

1. Górnego, lewego rogu portalu, wybierz **+ Utwórz zasób**.
2. Wprowadź *publiczny adres ip prefiksu adresu* w *Przeszukaj witrynę Marketplace* pole. Gdy **publiczny adres IP prefiksu adresu** pojawi się w wynikach wyszukiwania, wybierz ją.
3. W obszarze **publiczny adres IP prefiksu adresu**, wybierz opcję **Utwórz**.
4. Wprowadź lub wybierz wartości dla następujących ustawień w obszarze **Utwórz publiczny prefiks adresu IP**, a następnie wybierz **Utwórz**:

   |Ustawienie|Wymagana?|Szczegóły|
   |---|---|---|
   |Subskrypcja|Yes|Musi istnieć w tym samym [subskrypcji](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) jako zasób, którą chcesz skojarzyć publiczny adres IP.|
   |Grupa zasobów|Yes|Może znajdować się w tej samej lub różnych [grupy zasobów](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) jako zasób, którą chcesz skojarzyć publiczny adres IP.|
   |Name (Nazwa)|Yes|Nazwa musi być unikatowa w obrębie grupy zasobów, którą wybierzesz.|
   |Region|Yes|Musi istnieć w tym samym [region](https://azure.microsoft.com/regions)jako publiczne adresy IP można przypisywać adresów z zakresu. Prefiks jest obecnie dotyczy wersji zapoznawczej w zachodnio-środkowe stany USA, zachodnie stany USA, zachodnie stany USA 2, środkowe stany USA, Europa Północna, Europa Zachodnia i Azja południowo-wschodnia.|
   |Rozmiar prefiksu|Yes| Rozmiar prefiksu, których potrzebujesz. A/28 lub 16 adresów IP jest ustawieniem domyślnym. 

**Polecenia**


|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[Utwórz prefiks public-ip sieci az](/cli/azure/network/public-ip/prefix/az-network-public-ip-prefix-create)|
|PowerShell|[Nowe AzureRmPublicIpAddressPrefix](/powershell/module/azurerm.network/new-azurermpublicipaddressprefix)|

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>Tworzenie statycznego publicznego adresu IP na podstawie prefiksu
Po utworzeniu prefiks, musisz utworzyć statyczne adresy IP z prefiksu. Aby to zrobić, wykonaj poniższe kroki.

1. W polu zawierającym tekst *Wyszukaj zasoby* w górnej części witryny Azure portal, wpisz polecenie *publiczny adres ip prefiksu adresu*. Gdy **prefiksy adresów publiczny adres IP** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz prefx, aby utworzyć publiczne adresy IP z.
3. Gdy się pojawi się w wynikach wyszukiwania, zaznacz go i kliknij pozycję **+ Dodaj adres IP** w sekcji Przegląd. W przypadku, gdy nie jest widoczna, upewnij się, że używasz prawo łącze w wersji zapoznawczej: https://aka.ms/publicipprefixportal
4. Wprowadź lub wybierz wartości dla następujących ustawień w obszarze **tworzenie publicznego adresu IP**. Ponieważ prefiks jest dla standardowej jednostki SKU, protokołów IPv4 i statyczne, wystarczy podać następujące informacje:

   |Ustawienie|Wymagana?|Szczegóły|
    |---|---|---|
    |Name (Nazwa)|Yes|Nazwa publicznego adresu IP musi być unikatowa w obrębie grupy zasobów, którą wybierzesz.|
   |Limit czasu bezczynności (minuty)|Nie|Ile minut nie zamykaj połączenie TCP lub HTTP bez polegania na klientach, aby wysyłać komunikaty utrzymywania aktywności. |
   |Etykieta nazwy DNS|Nie|Musi być unikatowa w obrębie regionu świadczenia usługi Azure, Utwórz nazwę w (za pośrednictwem wszystkich subskrypcji i wszystkich klientów). Azure powoduje automatyczne zarejestrowanie nazwy i adresu IP w jej systemie DNS, dzięki czemu możesz nawiązać połączenie zasób o nazwie. Azure dołącza podsieć domyślna, takich jak *location.cloudapp.azure.com* (tam, gdzie lokalizacja to lokalizacja, możesz wybrać) do nazwy zostaną podane, można utworzyć w pełni kwalifikowana nazwa DNS. Aby uzyskać więcej informacji, zobacz [użycia usługi Azure DNS z publicznym adresem IP platformy Azure](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|

## <a name="view-or-delete-a-prefix"></a>Wyświetl lub usuń prefiks

1. W polu zawierającym tekst *Wyszukaj zasoby* w górnej części witryny Azure portal, wpisz polecenie *publiczny adres ip prefiksu adresu*. Gdy **prefiksy adresów publiczny adres IP** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz nazwę publicznego prefiksu adresu IP, który chcesz wyświetlić, zmienić ustawienia, lub usunąć z listy.
3. Wykonaj jedną z następujących opcji, w zależności od tego, czy chcesz wyświetlić, usunąć lub zmienić prefiks publicznego adresu IP.
    - **Widok**: **Przegląd** sekcji przedstawiono ustawienia kluczy dla prefiksu publicznego adresu IP, takie jak prefiks.
    - **Usuń**: Aby usunąć prefiks publicznego adresu IP, wybierz **Usuń** w **Przegląd** sekcji. Adresy w ramach prefiksu są skojarzone z zasobów publicznych adresów IP, należy najpierw usunąć zasoby publicznych adresów IP. Zobacz [usunąć publicznego adresu IP](virtual-network-public-ip-address.md#view-change-settings-for-or-delete-a-public-ip-address).

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[listę prefiksów public-ip sieci az](/cli/azure/network/public-ip/prefix/az-network-public-ip-prefix-list) publiczne adresy IP, [az sieci public-ip prefiksu show](/cli/azure/network/public-ip/prefix/az-network-public-ip-prefix-show) wyświetlanie ustawień; [az sieci public-ip prefiksu aktualizacji](/cli/azure/network/public-ip-prefix#az-network-public-ip-prefix-update) można zaktualizować; [az sieci public-ip prefiksu delete](/cli/azure/network/public-ip/prefix/az-network-public-ip-prefix-delete) do usunięcia|
|PowerShell|[Get-AzureRmPublicIpAddressPrefix](/powershell/module/azurerm.network/get-azurermpublicipaddressprefix) pobierania obiektu publicznego adresu IP i wyświetlić jej ustawienia [AzureRmPublicIpAddressPrefix zestaw](/powershell/module/azurerm.network/set-azurermpublicipaddressprefix) można zaktualizować ustawień; [AzureRmPublicIpAddressPrefix Usuń](/powershell/module/azurerm.network/remove-azurermpublicipaddressprefix) do usunięcia|

## <a name="permissions"></a>Uprawnienia

Do wykonywania zadań na prefiksy publicznych adresów IP, Twoje konto musi mieć przypisaną do [Współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roli lub [niestandardowe](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roli, którą przypisano odpowiednie działania wymienione w poniższej tabeli:

| Akcja                                                                   | Name (Nazwa)                                                           |
| ---------                                                                | -------------                                                  |
| Microsoft.Network/publicIPPrefixes/read                           | Przeczytaj publiczny prefiks adresu IP                                |
| Microsoft.Network/publicIPPrefixes/write                          | Utwórz lub zaktualizuj publiczny prefiks adresu IP                    |
| Microsoft.Network/publicIPPrefixes/delete                         | Usuń publiczny prefiks adresu IP                              |
|Microsoft.Network/publicIPPrefixes/join/action | Tworzenie publicznego adresu IP na podstawie prefiksu |

## <a name="next-steps"></a>Kolejne kroki

- Więcej informacji na temat scenariuszy i korzyści z używania [publiczny prefiks adresu IP](public-ip-address-prefix.md)
