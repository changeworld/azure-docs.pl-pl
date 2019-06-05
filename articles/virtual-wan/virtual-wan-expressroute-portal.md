---
title: Użyj usługi Azure Virtual WAN do tworzenia połączeń usługi ExpressRoute z platformą Azure i środowiskami lokalnymi | Microsoft Docs
description: W tym samouczku pokazano, jak używać usługi Azure Virtual WAN do tworzenia połączeń usługi ExpressRoute z platformą Azure i środowiskami lokalnymi.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 04/02/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my corporate on-premises network(s) to my VNets using Virtual WAN and ExpressRoute.
ms.openlocfilehash: d43cdec9e906087c98759a478ebc0c86beaa1354
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2019
ms.locfileid: "66693275"
---
# <a name="tutorial-create-an-expressroute-association-using-azure-virtual-wan-preview"></a>Samouczek: Tworzenie skojarzenia usługi ExpressRoute przy użyciu usługi Azure Virtual WAN (wersja zapoznawcza)

W tym samouczku pokazano, w jaki sposób przy użyciu usługi Virtual WAN utworzyć połączenie z zasobami na platformie Azure za pośrednictwem obwodu i skojarzenia usługi ExpressRoute. Aby uzyskać więcej informacji na temat usługi Virtual WAN, zobacz [Omówienie usługi Virtual WAN](virtual-wan-about.md)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie wirtualnej sieci WAN
> * Tworzenie koncentratora
> * Wyszukiwanie obwodu i kojarzenie go z koncentratorem
> * Kojarzenie obwodu z koncentratorami
> * Łączenie sieci wirtualnej z koncentratorem
> * Wyświetlanie wirtualnej sieci WAN
> * Wyświetlanie kondycji zasobu
> * Monitorowanie połączenia

> [!IMPORTANT]
> Ten podgląd publiczny nie jest objęty umową dotyczącą poziomu usług i nie należy korzystać z niego w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="register"></a>Rejestrowanie tej funkcji

Aby móc skonfigurować usługę Virtual WAN, należy najpierw zarejestrować swoją subskrypcję w wersji zapoznawczej. Jeśli tego nie zrobisz, usługa Virtual WAN nie będzie dostępna w portalu. Aby zarejestrować urządzenie, Wyślij wiadomość e-mail do **azurevirtualwan\@microsoft.com** przy użyciu identyfikatora subskrypcji. Gdy subskrypcja zostanie zarejestrowana, otrzymasz wiadomość e-mail.

**Zagadnienia dotyczące wersji zapoznawczej:**

Obwód usługi ExpressRoute musi być włączona w kraju/regionu, który obsługuje [zasięgu globalnym ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#where-is-expressroute-global-reach-supported).
Obwód usługi ExpressRoute musi być obwodu — wersja Premium, aby nawiązać połączenie wirtualnej sieci WAN Centrum. 

## <a name="vnet"></a>1. Tworzenie sieci wirtualnej

[!INCLUDE [Create a virtual network](../../includes/virtual-wan-tutorial-vnet-include.md)]

## <a name="openvwan"></a>2. Tworzenie wirtualnej sieci WAN

Przejdź w przeglądarce do witryny [Azure Portal](https://aka.ms/azurevirtualwanpreviewfeatures) (wersja zapoznawcza) i zaloguj się przy użyciu konta platformy Azure.

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

### <a name="getting-started-page"></a>Strona Wprowadzenie

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-gettingstarted-include.md)]

## <a name="hub"></a>3. Tworzenie koncentratora

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="hub"></a>4. Wyszukiwanie obwodu i kojarzenie go z koncentratorem

1. Wybierz swoje vWAN i w obszarze **architektura sieci WAN wirtualnego**, wybierz opcję **obwodów usługi ExpressRoute**.
1. Jeśli obwód usługi ExpressRoute znajduje się w tej samej subskrypcji, co Twoja vWAN, kliknij przycisk **obwodu ExpressRoute wybierz** z subskrypcji. 
1. Z menu rozwijanego wybierz obwód usługi ExpressRoute, który chcesz skojarzyć z koncentratorem.
1. Jeśli obwód usługi ExpressRoute nie znajduje się w tej samej subskrypcji lub udostępniony został [klucz autoryzacji i identyfikator komunikacji równorzędnej](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md), wybierz pozycję **Znajdź obwód realizujący klucz autoryzacji**
1. Wprowadź następujące wartości:
1. **Klucz autoryzacji** — wygenerowany przez właściciela obwodu zgodnie z powyższym opisem
1. **Identyfikator URI obwodu równorzędnego** — identyfikator URI obwodu udostępniony przez właściciela obwodu będący jego unikatowym identyfikatorem
1. **Waga routingu** - [Waga routingu](../expressroute/expressroute-optimize-routing.md) umożliwia preferowanie określonych ścieżek, gdy wiele obwodów z różnych lokalizacji komunikacji równorzędnej jest połączonych z tym samym koncentratorem
1. Kliknij przycisk **znaleźć obwodów** i wybierz obwód, jeśli znaleziono.
1. Wybierz co najmniej 1 koncentratory z listy rozwijanej, a następnie kliknij przycisk **Zapisz**.

## <a name="vnet"></a>5. Łączenie sieci wirtualnej z koncentratorem

W tym kroku zostanie utworzone połączenie równorzędne pomiędzy koncentratorem i siecią wirtualną. Powtórz te czynności dla każdej sieci wirtualnej, z którą chcesz się połączyć.

1. Na stronie usługi Virtual WAN kliknij pozycję **Połączenie sieci wirtualnej**.
2. Na stronie połączenia sieci wirtualnej kliknij polecenie **+ Dodaj połączenie**.
3. Na stronie **Dodaj połączenie** wypełnij następujące pola:

    * **Nazwa połączenia** — nazwij połączenie.
    * **Koncentratory** — wybierz koncentrator, z którym chcesz skojarzyć to połączenie.
    * **Subskrypcja** — sprawdź, czy wybrano właściwą subskrypcję.
    * **Sieć wirtualna** — wybierz sieć wirtualną, którą chcesz połączyć z tym koncentratorem. Sieć wirtualna nie może mieć istniejącej bramy sieci wirtualnej.


## <a name="viewwan"></a>6. Wyświetlanie wirtualnej sieci WAN

1. Przejdź do wirtualnej sieci WAN.
2. Na stronie Przegląd każdy punkt na mapie przedstawia koncentrator. Umieść kursor na dowolnym punkcie, aby wyświetlić podsumowanie kondycji koncentratora.
3. W sekcji dotyczącej koncentratorów i połączeń możesz wyświetlić stan koncentratora, lokację, region, stan połączenia sieci VPN oraz bajty przychodzące i wychodzące.

## <a name="viewhealth"></a>7. Wyświetlanie kondycji zasobów

1. Przejdź do sieci WAN.
2. Na stronie sieci WAN w sekcji **Pomoc techniczna i rozwiązywanie problemów** kliknij pozycję **Kondycja** i wyświetl zasób.

## <a name="connectmon"></a>8. Monitorowanie połączenia

Utwórz połączenie, aby monitorować komunikację pomiędzy maszyną wirtualną platformy Azure a zdalną lokacją. Aby uzyskać informacje dotyczące konfigurowania monitora połączeń, zobacz [Monitorowanie komunikacji sieciowej](~/articles/network-watcher/connection-monitor.md). Pole Źródło zawiera adres IP maszyny wirtualnej na platformie Azure, a docelowy adres IP to adres IP lokacji.

## <a name="cleanup"></a>9. Oczyszczanie zasobów

Jeśli te zasoby nie są już potrzebne, możesz użyć [AzResourceGroup Usuń](/powershell/module/az.resources/remove-azresourcegroup) Aby usunąć grupę zasobów i wszystkie zawarte w niej zasoby. Zastąp wartość „myResourceGroup” nazwą grupy zasobów, a następnie uruchom następujące polecenie programu PowerShell:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie wirtualnej sieci WAN
> * Tworzenie koncentratora
> * Wyszukiwanie obwodu i kojarzenie go z koncentratorem
> * Kojarzenie obwodu z koncentratorami
> * Łączenie sieci wirtualnej z koncentratorem
> * Wyświetlanie wirtualnej sieci WAN
> * Wyświetlanie kondycji zasobu
> * Monitorowanie połączenia

Aby uzyskać więcej informacji na temat usługi Virtual WAN, zobacz stronę [Omówienie usługi Virtual WAN](virtual-wan-about.md).
