---
title: Tworzenie, zmienianie lub usuwanie prefiksu publicznego adresu IP platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak utworzyć, zmienić lub usunąć prefiks publicznego adresu IP.
services: virtual-network
documentationcenter: na
author: anavinahar
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/13/2019
ms.author: anavin
ms.openlocfilehash: 26d8ee34c735cab8f1033a9aad897ec0b1bed524
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65952683"
---
# <a name="create-change-or-delete-a-public-ip-address-prefix"></a>Tworzenie, zmienianie i usuwanie prefiksu publicznego adresu IP

Dowiedz się więcej o prefiksie publicznego adresu IP oraz o tym, jak go utworzyć, zmienić i usunąć. Prefiks publicznego adresu IP to ciągły zakres adresów oparty na liczbie określanych publicznych adresów IP. Adresy są przypisane do subskrypcji. Podczas tworzenia zasobu publicznego adresu IP można przypisać statyczny publiczny adres IP z prefiksu i skojarzyć adres z maszynami wirtualnymi, modułami równoważenia obciążenia lub innymi zasobami, aby włączyć łączność z Internetem. Jeśli nie znasz prefiksów publicznego adresu IP, zobacz [Omówienie prefiksu publicznego adresu IP](public-ip-address-prefix.md)

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Wykonaj następujące zadania przed wykonaniem kroków w dowolnej sekcji tego artykułu:

- Jeśli nie masz jeszcze konta platformy Azure, zarejestruj się, aby uzyskać [bezpłatne konto próbne.](https://azure.microsoft.com/free)
- Jeśli korzystasz z https://portal.azure.comportalu, otwórz program i zaloguj się za pomocą konta platformy Azure.
- Jeśli do wykonywania zadań w tym artykule są używane polecenia programu PowerShell, należy uruchomić polecenia w [usłudze Azure Cloud Shell](https://shell.azure.com/powershell)lub uruchomić program PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Ten samouczek wymaga modułu programu Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.
- Jeśli do wykonywania zadań w tym artykule przy użyciu poleceń interfejsu wiersza polecenia platformy Azure należy uruchomić polecenia w [usłudze Azure Cloud Shell](https://shell.azure.com/bash)lub uruchomić interfejs wiersza polecenia z komputera. Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.41 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli). Jeśli korzystasz z interfejsu wiersza polecenia platformy `az login` Azure lokalnie, należy również uruchomić, aby utworzyć połączenie z platformą Azure.

Konto, do którego się logujesz lub z którą łączysz się z platformą Azure, musi być przypisane do roli [współautora sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do [roli niestandardowej,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) której przypisano odpowiednie akcje wymienione w [sekcji Uprawnienia](#permissions).

Prefiksy publicznego adresu IP mają opłatę. Aby uzyskać szczegółowe informacje, zobacz [cennik](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="create-a-public-ip-address-prefix"></a>Tworzenie prefiksu publicznego adresu IP

1. U góry lewego rogu portalu wybierz pozycję **+ Utwórz zasób**.
2. Wprowadź *prefiks publicznego adresu IP* w polu *Wyszukaj w Portalu Marketplace.* Gdy **prefiks publicznego adresu IP** pojawi się w wynikach wyszukiwania, wybierz go.
3. W **obszarze Prefiks publicznego adresu IP**wybierz pozycję **Utwórz**.
4. Wprowadź lub wybierz wartości dla następujących ustawień w obszarze **Tworzenie prefiksu publicznego adresu IP**, a następnie wybierz pozycję **Utwórz**:

   |Ustawienie|Wymagana?|Szczegóły|
   |---|---|---|
   |Subskrypcja|Tak|Musi istnieć w tej samej [subskrypcji](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) co zasób, z którego chcesz skojarzyć publiczny adres IP.|
   |Grupa zasobów|Tak|Może istnieć w tej samej lub innej [grupie zasobów](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) jako zasób, z którego chcesz skojarzyć publiczny adres IP.|
   |Nazwa|Tak|Nazwa musi być unikatowa w wybranej grupie zasobów.|
   |Region|Tak|Musi istnieć w tym samym [regionie](https://azure.microsoft.com/regions)co publiczne adresy IP, które będą przypisywane adresy z zakresu.|
   |Rozmiar prefiksu|Tak| Rozmiar potrzebnego prefiksu. Domyślne są adresy IP /28 lub 16.

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az sieci public-ip prefiks create](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create)|
|PowerShell|[Nowy-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix)|

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>Tworzenie statycznego publicznego adresu IP na podstawie prefiksu
Po utworzeniu prefiksu należy utworzyć statyczne adresy IP z prefiksu. Aby to zrobić, wykonaj poniższe czynności.

1. W polu zawierającym tekst *Zasoby wyszukiwania* u góry witryny Azure portal wpisz *prefiks publicznego adresu IP*. Gdy **prefiksy publicznego adresu IP** pojawiają się w wynikach wyszukiwania, wybierz je.
2. Wybierz prefiks, z którego chcesz utworzyć publiczne wiadomości IP.
3. Gdy pojawi się w wynikach wyszukiwania, wybierz go i kliknij **+Dodaj adres IP** w sekcji Przegląd.
4. Wprowadź lub wybierz wartości dla następujących ustawień w obszarze **Tworzenie publicznego adresu IP**. Ponieważ prefiks jest dla standardowej jednostki SKU, IPv4 i statyczne, wystarczy podać następujące informacje:

   |Ustawienie|Wymagana?|Szczegóły|
   |---|---|---|
   |Nazwa|Tak|Nazwa publicznego adresu IP musi być unikatowa w wybranej grupie zasobów.|
   |Limit czasu bezczynnego (minuty)|Nie|Ile minut na otwarcie połączenia TCP lub HTTP bez polegania na klientach w celu wysyłania wiadomości keep-alive. |
   |Etykieta nazwy DNS|Nie|Musi być unikatowa w regionie platformy Azure, w której tworzysz nazwę (we wszystkich subskrypcjach i wszystkich klientach). Platforma Azure automatycznie rejestruje nazwę i adres IP w swoim systemie DNS, dzięki czemu można połączyć się z zasobem o nazwie. Platforma Azure dołącza domyślną podsieć, taką jak *location.cloudapp.azure.com* (gdzie lokalizacja jest wybraną lokalizacją) do nazwy, którą podasz, aby utworzyć w pełni kwalifikowaną nazwę DNS. Aby uzyskać więcej informacji, zobacz [Używanie usługi Azure DNS z publicznym adresem IP platformy Azure](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|

Alternatywnie można użyć poleceń INTERFEJSU WIERSZA i PS poniżej z parametrami --public-ip-prefix (CLI) i -PublicIpPrefix (PS), aby utworzyć zasób publicznego adresu IP. 

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az network public-ip create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create)|
|PowerShell|[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress?view=azps-2.0.0)|

## <a name="view-or-delete-a-prefix"></a>Wyświetlanie lub usuwanie prefiksu

1. W polu zawierającym tekst *Zasoby wyszukiwania* u góry witryny Azure portal wpisz *prefiks publicznego adresu IP*. Gdy **prefiksy publicznego adresu IP** pojawiają się w wynikach wyszukiwania, wybierz je.
2. Wybierz nazwę publicznego prefiksu adresu IP, który chcesz wyświetlić, zmień ustawienia lub usuń z listy.
3. Wykonaj jedną z następujących opcji, w zależności od tego, czy chcesz wyświetlić, usunąć lub zmienić prefiks publicznego adresu IP.
   - **Widok**: Sekcja **Przegląd** zawiera kluczowe ustawienia prefiksu publicznego adresu IP, takie jak prefiks.
   - **Usuń**: Aby usunąć prefiks publicznego adresu IP, wybierz pozycję **Usuń** w sekcji **Przegląd.** Jeśli adresy w prefiksie są skojarzone z zasobami publicznego adresu IP, należy najpierw usunąć publiczne zasoby adresów IP. Zobacz [usuwanie publicznego adresu IP](virtual-network-public-ip-address.md#view-change-settings-for-or-delete-a-public-ip-address).

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az sieci public-ip prefiks listy](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-list) do listy publicznych adresów IP, [az sieci public-ip prefiks pokazać](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-show) ustawienia; [az sieci public-ip prefiks aktualizacji](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-update) do aktualizacji; [AZ sieci public-ip prefiks usunąć,](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-delete) aby usunąć|
|PowerShell|[Get-AzPublicIpPrefix,](/powershell/module/az.network/get-azpublicipprefix) aby pobrać publiczny obiekt adresu IP i wyświetlić jego ustawienia, [Set-AzPublicIpPrefix,](/powershell/module/az.network/set-azpublicipprefix) aby zaktualizować ustawienia; [Usuń-AzPublicIpPrefix](/powershell/module/az.network/remove-azpublicipprefix) do usunięcia|

## <a name="permissions"></a>Uprawnienia

Aby wykonywać zadania w prefiksach publicznego adresu IP, konto musi być przypisane do roli [współautora sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do roli [niestandardowej,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) która jest przypisana do odpowiednich akcji wymienionych w poniższej tabeli:

| Akcja                                                            | Nazwa                                                           |
| ---------                                                         | -------------                                                  |
| Microsoft.Network/publicIPPrefixes/read                           | Odczytywanie prefiksu publicznego adresu IP                                |
| Microsoft.Network/publicIPPrefixes/write                          | Tworzenie lub aktualizowanie prefiksu publicznego adresu IP                    |
| Microsoft.Network/publicIPPrefixes/delete                         | Usuwanie prefiksu publicznego adresu IP                              |
|Microsoft.Network/publicIPPrefixes/join/action                     | Tworzenie publicznego adresu IP na podstawie prefiksu |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o scenariuszach i korzyściach płynących z używania [publicznego prefiksu IP](public-ip-address-prefix.md)
