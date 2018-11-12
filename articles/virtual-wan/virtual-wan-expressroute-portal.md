---
title: Użyj usługi Azure Virtual WAN do tworzenia połączeń usługi ExpressRoute z platformą Azure i środowiskami lokalnymi | Microsoft Docs
description: W tym samouczku pokazano, jak używać usługi Azure Virtual WAN do tworzenia połączeń usługi ExpressRoute z platformą Azure i środowiskami lokalnymi.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 10/5/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my corporoate on-premises network(s) to my VNets using Virtual WAN and ExpressRoute.
ms.openlocfilehash: c02020ba8d49b123cf8914214d52ac40896a3c20
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51248184"
---
# <a name="tutorial-create-an-expressroute-association-using-azure-virtual-wan-preview"></a>Samouczek: tworzenie skojarzenia usługi ExpressRoute przy użyciu usługi Azure Virtual WAN (wersja zapoznawcza)

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

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="register"></a>Rejestrowanie tej funkcji

Aby móc skonfigurować usługę Virtual WAN, należy najpierw zarejestrować swoją subskrypcję w wersji zapoznawczej. Jeśli tego nie zrobisz, usługa Virtual WAN nie będzie dostępna w portalu. Aby się zarejestrować, wyślij wiadomość e-mail na adres **azurevirtualwan@microsoft.com**, podając identyfikator subskrypcji. Gdy subskrypcja zostanie zarejestrowana, otrzymasz wiadomość e-mail.

**Zagadnienia dotyczące wersji zapoznawczej:**

* Dostępność w regionie: zachodnio-środkowe stany USA
* Obwód usługi ExpressRoute musi być włączony w kraju, w którym obsługiwany jest [zasięg globalny usługi ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#where-is-expressroute-global-reach-supported)

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

1. Wybierz swoją wirtualną sieć WAN, a następnie w obszarze **Architektura wirtualnej sieci WAN** wybierz pozycję **Obwody usługi ExpressRoute**
2. Jeśli obwód usługi ExpressRoute znajduje się w tej samej subskrypcji, co Twoja wirtualna sieć WAN, kliknij pozycję **Wybierz obwód usługi ExpressRoute** ze swoich subskrypcji 
3. Z menu rozwijanego wybierz obwód usługi ExpressRoute, który chcesz skojarzyć z koncentratorem.
4. Jeśli obwód usługi ExpressRoute nie znajduje się w tej samej subskrypcji lub udostępniony został [klucz autoryzacji i identyfikator komunikacji równorzędnej](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md), wybierz pozycję **Znajdź obwód realizujący klucz autoryzacji**
5. Wprowadź następujące wartości:
* **Klucz autoryzacji** — wygenerowany przez właściciela obwodu zgodnie z powyższym opisem
* **Identyfikator URI obwodu równorzędnego** — identyfikator URI obwodu udostępniony przez właściciela obwodu będący jego unikatowym identyfikatorem
* **Waga routingu** - [Waga routingu](../expressroute/expressroute-optimize-routing.md) umożliwia preferowanie określonych ścieżek, gdy wiele obwodów z różnych lokalizacji komunikacji równorzędnej jest połączonych z tym samym koncentratorem
6. Kliknij pozycję **Wyszukaj obwód**, a następnie wybierz obwód, jeśli został odnaleziony
7. Wybierz co najmniej jeden koncentrator z listy rozwijanej, a następnie kliknij pozycję **Zapisz**

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

Gdy grupa zasobów i zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). Zastąp wartość „myResourceGroup” nazwą grupy zasobów, a następnie uruchom następujące polecenie programu PowerShell:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Następne kroki

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
