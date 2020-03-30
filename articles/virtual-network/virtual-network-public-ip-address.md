---
title: Tworzenie, zmienianie lub usuwanie publicznego adresu IP platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak utworzyć, zmienić lub usunąć publiczny adres IP.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/06/2019
ms.author: kumud
ms.openlocfilehash: 38ff40b537730418fe9f0f8295884dae98a2fe0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244916"
---
# <a name="create-change-or-delete-a-public-ip-address"></a>Tworzenie, zmienianie i usuwanie publicznego adresu IP

Dowiedz się więcej o publicznym adresie IP oraz o tym, jak go utworzyć, zmienić i usunąć. Publiczny adres IP to zasób z własnymi ustawieniami konfigurowalnymi. Przypisywanie publicznego adresu IP do zasobu platformy Azure obsługującego publiczne adresy IP umożliwia:
- Komunikacja przychodząca z Internetu do zasobu, takiego jak maszyny wirtualne platformy Azure (VM), bramy aplikacji platformy Azure, moduły równoważenia obciążenia platformy Azure, bramy sieci VPN platformy Azure i inne. Nadal można komunikować się z niektórymi zasobami, takimi jak maszyny wirtualne, z Internetu, jeśli maszyna wirtualna nie ma przypisanego publicznego adresu IP, o ile maszyna wirtualna jest częścią puli zaplecza modułu równoważenia obciążenia, a modułowi równoważenia obciążenia jest przypisywany publiczny adres IP. Aby ustalić, czy zasobu dla określonej usługi platformy Azure można przypisać publiczny adres IP lub czy można go komunikować za pośrednictwem publicznego adresu IP innego zasobu platformy Azure, zobacz dokumentację usługi.
- Łączność wychodząca z Internetem przy użyciu przewidywalnego adresu IP. Na przykład maszyna wirtualna może komunikować się wychodzących do Internetu bez publicznego adresu IP przypisane do niego, ale jego adres jest adres sieciowy przetłumaczone przez platformę Azure do nieprzewidywalnego adresu publicznego, domyślnie. Przypisanie publicznego adresu IP do zasobu umożliwia poznanie, który adres IP jest używany dla połączenia wychodzącego. Choć jest to przewidywalne, adres może ulec zmianie, w zależności od wybranej metody przypisania. Aby uzyskać więcej informacji, zobacz [Tworzenie publicznego adresu IP](#create-a-public-ip-address). Aby dowiedzieć się więcej o połączeniach wychodzących z zasobów platformy Azure, zobacz [Opis połączeń wychodzących](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Wykonaj następujące zadania przed wykonaniem kroków w dowolnej sekcji tego artykułu:

- Jeśli nie masz jeszcze konta platformy Azure, zarejestruj się, aby uzyskać [bezpłatne konto próbne.](https://azure.microsoft.com/free)
- Jeśli korzystasz z https://portal.azure.comportalu, otwórz program i zaloguj się za pomocą konta platformy Azure.
- Jeśli do wykonywania zadań w tym artykule są używane polecenia programu PowerShell, należy uruchomić polecenia w [usłudze Azure Cloud Shell](https://shell.azure.com/powershell)lub uruchomić program PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Ten samouczek wymaga modułu programu Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.
- Jeśli do wykonywania zadań w tym artykule przy użyciu poleceń interfejsu wiersza polecenia platformy Azure należy uruchomić polecenia w [usłudze Azure Cloud Shell](https://shell.azure.com/bash)lub uruchomić interfejs wiersza polecenia z komputera. Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.31 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli korzystasz z interfejsu wiersza polecenia platformy `az login` Azure lokalnie, należy również uruchomić, aby utworzyć połączenie z platformą Azure.

Konto, do którego się logujesz lub z którą łączysz się z platformą Azure, musi być przypisane do roli [współautora sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do [roli niestandardowej,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) której przypisano odpowiednie akcje wymienione w [sekcji Uprawnienia](#permissions).

Publiczne adresy IP mają nominalną opłatę. Aby wyświetlić ceny, przeczytaj stronę [cennika adresu IP.](https://azure.microsoft.com/pricing/details/ip-addresses)

## <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP

1. W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób**.
2. Wprowadź *publiczny adres IP* w polu *Wyszukaj w Marketplace.* Gdy **publiczny adres IP** pojawi się w wynikach wyszukiwania, wybierz go.
3. W **obszarze Publiczny adres IP**wybierz pozycję **Utwórz**.
4. Wprowadź lub wybierz wartości dla następujących ustawień w obszarze **Tworzenie publicznego adresu IP,** a następnie wybierz pozycję **Utwórz**:

   |Ustawienie|Wymagana?|Szczegóły|
   |---|---|---|
   |Wersja IP|Tak| Wybierz IPv4 lub IPv6 lub Oba. Wybranie opcji Oba spowoduje utworzenie 2 publicznych adresów IP: 1 adres IPv4 i 1 adres IPv6. Dowiedz się więcej o [IPv6 w sieciach wirtualnych platformy Azure.](../virtual-network/ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
   |SKU|Tak|Wszystkie publiczne adresy IP utworzone przed wprowadzeniem jednostek SKU **są** podstawowymi publicznymi adresami IP jednostki SKU. Nie można zmienić jednostki SKU po utworzeniu publicznego adresu IP. Autonomiczna maszyna wirtualna, maszyny wirtualne w ramach zestawu dostępności lub zestawy skalowania maszyny wirtualnej mogą używać jednostek SKU basic lub Standard. Mieszanie jednostek SKU między maszynami wirtualnymi w ramach zestawów dostępności lub zestawów skalowania lub autonomicznych maszyn wirtualnych jest niedozwolone. **Podstawowe** Jednostka SKU: Jeśli tworzysz publiczny adres IP w regionie obsługującym strefy dostępności, ustawienie **Strefa dostępności** jest domyślnie ustawiona na *Brak.* Podstawowe publiczne wiadomości IP nie obsługują stref dostępności. **Standard** Jednostka SKU: Standardowy publiczny adres IP jednostki SKU może być skojarzony z maszyną wirtualną lub frontonałem modułu równoważenia obciążenia. Jeśli tworzysz publiczny adres IP w regionie obsługującym strefy dostępności, ustawienie **Strefa dostępności** jest domyślnie ustawiona na *Strefę nadmiarową.* Aby uzyskać więcej informacji o strefach dostępności, zobacz ustawienie **Strefy dostępności.** Standardowa jednostka SKU jest wymagana, jeśli adres jest skojarzony z modułem równoważenia obciążenia standardowego. Aby dowiedzieć się więcej o standardowych modułach równoważenia obciążenia, zobacz [standardowa jednostka SKU modułu równoważenia obciążenia platformy Azure](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Po przypisaniu standardowego publicznego adresu IP jednostki SKU do interfejsu sieciowego maszyny wirtualnej musisz jawnie zezwolić na ruch do miejsca przeznaczenia przy użyciu [sieciowej grupy zabezpieczeń](security-overview.md#network-security-groups). Próba komunikacji z zasobem będzie kończyć się niepowodzeniem do momentu utworzenia i skojarzenia sieciowej grupy zabezpieczeń, a następnie jawnego zezwolenia na żądany ruch.|
   |Nazwa|Tak|Nazwa musi być unikatowa w wybranej grupie zasobów.|
   |Przypisanie adresu IP|Tak|**Dynamiczny:** Adresy dynamiczne są przypisywane tylko po skojarzenia publicznego adresu IP z zasobem platformy Azure, a zasób jest uruchamiany po raz pierwszy. Adresy dynamiczne mogą ulec zmianie, jeśli są przypisane do zasobu, takiego jak maszyna wirtualna, a maszyna wirtualna zostanie zatrzymana (cofnięta alokacja), a następnie ponownie uruchomiona. Adres pozostaje taki sam, jeśli maszyna wirtualna zostanie ponownie uruchomiona lub zatrzymana (ale nie zostanie przydzielona). Adresy dynamiczne są zwalniane, gdy publiczny zasób adresu IP jest odłączony od zasobu, z którym jest skojarzony. **Statyczne:** Adresy statyczne są przypisywane podczas tworzenia publicznego adresu IP. Adresy statyczne nie są zwalniane, dopóki nie zostanie usunięty publiczny zasób adresu IP. Jeśli adres nie jest skojarzony z zasobem, można zmienić metodę przypisania po utworzeniu adresu. Jeśli adres jest skojarzony z zasobem, zmiana metody przypisania może być niedymiła. Jeśli wybierzesz *protokół IPv6* dla **wersji IP,** metodą przypisania musi być *dynamiczna* dla podstawowej jednostki SKU.  Standardowe adresy jednostek SKU są *statyczne* zarówno dla IPv4, jak i IPv6. |
   |Limit czasu bezczynnego (minuty)|Nie|Ile minut na otwarcie połączenia TCP lub HTTP bez polegania na klientach w celu wysyłania wiadomości keep-alive. Jeśli wybierzesz Protokół IPv6 dla **wersji IP,** tej wartości nie można zmienić. |
   |Etykieta nazwy DNS|Nie|Musi być unikatowa w lokalizacji platformy Azure, w której tworzysz nazwę (we wszystkich subskrypcjach i wszystkich klientach). Platforma Azure automatycznie rejestruje nazwę i adres IP w swoim systemie DNS, dzięki czemu można połączyć się z zasobem o nazwie. Platforma Azure dołącza domyślną podsieć, taką jak *location.cloudapp.azure.com* (gdzie lokalizacja jest wybraną lokalizacją) do nazwy, którą podasz, aby utworzyć w pełni kwalifikowaną nazwę DNS. Jeśli wybierzesz opcję utworzenia obu wersji adresów, ta sama nazwa DNS jest przypisywana zarówno do adresów IPv4, jak i IPv6. Domyślny dns platformy Azure zawiera rekordy nazw IPv4 A i IPv6 AAAA i odpowiada za pomocą obu rekordów, gdy nazwa DNS jest wyszukany. Klient wybiera adres (IPv4 lub IPv6) do komunikowania się z. Zamiast lub oprócz używania etykiety nazwy DNS z domyślnym sufiksem możesz użyć usługi Azure DNS do skonfigurowania nazwy DNS z sufiksem niestandardowym, który jest rozpoznawany jako publiczny adres IP. Aby uzyskać więcej informacji, zobacz [Use Azure DNS with an Azure public IP address (Używanie usługi Azure DNS z publicznym adresem IP platformy Azure)](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|
   |Nazwa (widoczna tylko po wybraniu wersji IP **obu)**|Tak, jeśli wybierzesz wersję IP **obu**|Nazwa musi być inna niż nazwa wprowadzona dla pierwszego **imienia** na tej liście. Jeśli wybierzesz utworzenie zarówno adresu IPv4, jak i adresu IPv6, portal utworzy dwa oddzielne publiczne zasoby adresów IP, po jednym z przypisaną do niego każdą wersją adresu IP.|
   |Przypisanie adresu IP (widoczne tylko po wybraniu wersji IP **obu)**|Tak, jeśli wybierzesz wersję IP **obu**|Te same ograniczenia co przypisanie adresu IP powyżej|
   |Subskrypcja|Tak|Musi istnieć w tej samej [subskrypcji](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) co zasób, z którym będziesz kojarzyć publiczne adresy IP.|
   |Grupa zasobów|Tak|Może istnieć w tej samej lub innej [grupie zasobów](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) jako zasób, z którym będziesz kojarzyć publiczne adresy IP.|
   |Lokalizacja|Tak|Musi istnieć w tej samej [lokalizacji](https://azure.microsoft.com/regions), również określane jako region, jako zasób, do którego będziesz kojarzyć publicznych adresów IP.|
   |Strefa dostępności| Nie | To ustawienie jest wyświetlane tylko po wybraniu obsługiwanej lokalizacji. Aby uzyskać listę obsługiwanych lokalizacji, zobacz [Omówienie stref dostępności](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Jeśli wybrano **podstawową** jednostkę SKU, *opcja Brak* zostanie wybrana automatycznie. Jeśli wolisz zagwarantować określoną strefę, możesz wybrać określoną strefę. Każdy wybór nie jest strefowy. Jeśli wybrano **standardową** jednostkę SKU: Zone-redundant jest automatycznie wybierana dla Ciebie i sprawia, że ścieżka danych jest odporna na awarię strefy. Jeśli wolisz zagwarantować określoną strefę, która nie jest odporna na awarię strefy, możesz wybrać określoną strefę.

**Polecenia**

Chociaż portal udostępnia opcję utworzenia dwóch publicznych zasobów adresów IP (jednego IPv4 i jednego Protokołu IPv6), następujące polecenia interfejsu wiersza polecenia i programu PowerShell tworzą jeden zasób z adresem dla jednej wersji IP lub drugiej. Jeśli chcesz dwa publiczne zasoby adresów IP, po jednym dla każdej wersji IP, należy uruchomić polecenie dwa razy, określając różne nazwy i wersje IP dla zasobów publicznego adresu IP.

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create)|
|PowerShell|[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)|

## <a name="view-change-settings-for-or-delete-a-public-ip-address"></a>Wyświetlanie, zmienianie ustawień publicznego adresu IP lub usuwanie go

1. W polu zawierającym tekst *Szukaj zasobów* u góry witryny Azure portal wpisz *publiczny adres IP*. Gdy **publiczne adresy IP** pojawią się w wynikach wyszukiwania, wybierz je.
2. Wybierz nazwę publicznego adresu IP, który chcesz wyświetlić, zmień ustawienia lub usuń z listy.
3. Wykonaj jedną z następujących opcji, w zależności od tego, czy chcesz wyświetlić, usunąć lub zmienić publiczny adres IP.
   - **Widok**: Sekcja **Przegląd** zawiera kluczowe ustawienia publicznego adresu IP, takie jak interfejs sieciowy, z który jest skojarzony (jeśli adres jest skojarzony z interfejsem sieciowym). Portal nie wyświetla wersji adresu (IPv4 lub IPv6). Aby wyświetlić informacje o wersji, użyj polecenia Programu PowerShell lub CLI, aby wyświetlić publiczny adres IP. Jeśli wersją adresu IP jest protokół IPv6, przypisany adres nie jest wyświetlany przez portal, program PowerShell ani wiersz polecenia.
   - **Usuń**: Aby usunąć publiczny adres IP, wybierz pozycję **Usuń** w sekcji **Przegląd.** Jeśli adres jest obecnie skojarzony z konfiguracją IP, nie można go usunąć. Jeśli adres jest obecnie skojarzony z konfiguracją, wybierz **opcję Odłącz,** aby odłączyć adres od konfiguracji IP.
   - **Zmień**: wybierz **opcję Konfiguracja**. Zmień ustawienia, korzystając z informacji w kroku 4 [tworzenie publicznego adresu IP](#create-a-public-ip-address). Aby zmienić przypisanie adresu IPv4 ze statycznego na dynamiczny, należy najpierw odłączyć publiczny adres IPv4 od konfiguracji IP, z którą jest skojarzony. Następnie można zmienić metodę przypisania na dynamiczną i wybrać **opcję Skojarz,** aby skojarzyć adres IP z tą samą konfiguracją IP, inną konfiguracją lub pozostawić ją rozdzieloną. Aby odłączyć publiczny adres IP, w sekcji **Przegląd** wybierz opcję **Odłączenie**.

   >[!WARNING]
   >Po zmianie metody przypisania ze statycznego na dynamiczny tracisz adres IP przypisany do publicznego adresu IP. Podczas gdy publiczne serwery DNS platformy Azure obsługują mapowanie między adresami statycznymi lub dynamicznymi a dowolną etykietą nazwy DNS (jeśli została zdefiniowana), dynamiczny adres IP może ulec zmianie po uruchomieniu maszyny wirtualnej po tym, jak została zatrzymana (cofnięta). Aby zapobiec zmianie adresu, przypisz statyczny adres IP.

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az sieć public-ip lista](/cli/azure/network/public-ip#az-network-public-ip-list) do listy publicznych adresów IP, [az sieci public-ip show,](/cli/azure/network/public-ip#az-network-public-ip-show) aby pokazać ustawienia; [az sieci public-ip aktualizacji](/cli/azure/network/public-ip#az-network-public-ip-update) do aktualizacji; [az sieć public-ip usunąć,](/cli/azure/network/public-ip#az-network-public-ip-delete) aby usunąć|
|PowerShell|[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) do pobierania publicznego obiektu adresu IP i wyświetlić jego ustawienia, [Set-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress) do aktualizacji ustawień; [Usuń-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) do usunięcia|

## <a name="assign-a-public-ip-address"></a>Przypisywanie publicznego adresu IP

Dowiedz się, jak przypisać publiczny adres IP do następujących zasobów:

- Maszyna wirtualna [z systemem Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (podczas tworzenia) lub [istniejąca maszyna wirtualna](virtual-network-network-interface-addresses.md#add-ip-addresses)
- [Internetowy moduł równoważenia obciążenia](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Application Gateway](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Połączenie lokacja z lokacją przy użyciu bramy sieci VPN platformy Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Zestaw skalowania maszyny wirtualnej platformy Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Uprawnienia

Aby wykonywać zadania na publicznych adresach IP, konto musi być przypisane do roli [współautora sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do roli [niestandardowej,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) która jest przypisana odpowiednie akcje wymienione w poniższej tabeli:

| Akcja                                                             | Nazwa                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft.Network/publicIPAddresses/read                           | Odczytywanie publicznego adresu IP                                          |
| Microsoft.Network/publicIPAddresses/write                          | Tworzenie lub aktualizowanie publicznego adresu IP                           |
| Microsoft.Network/publicIPAddresses/delete                         | Usuwanie publicznego adresu IP                                     |
| Microsoft.Network/publicIPAddresses/join/action                    | Kojarzenie publicznego adresu IP z zasobem                    |

## <a name="next-steps"></a>Następne kroki

- Tworzenie publicznego adresu IP przy użyciu przykładowych skryptów interfejsu [wiersza polecenia programu](cli-samples.md) [PowerShell](powershell-samples.md) lub platformy Azure lub przy użyciu [szablonów usługi](template-samples.md) Azure Resource Manager
- Tworzenie i stosowanie [zasad platformy Azure](policy-samples.md) dla publicznych adresów IP
