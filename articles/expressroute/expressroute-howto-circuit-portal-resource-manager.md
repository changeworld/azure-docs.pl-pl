---
title: Samouczek — Tworzenie i modyfikowanie obwodu za pomocą ExpressRoute
description: W tym samouczku dowiesz się, jak tworzyć, inicjować, sprawdzać, aktualizować, usuwać i cofać obsługę administracyjną obwodu ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/20/2018
ms.author: cherylmc
ms.openlocfilehash: 7327031a7cd05674e9823f21601aab34c859f540
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2019
ms.locfileid: "74423570"
---
# <a name="tutorial-create-and-modify-an-expressroute-circuit"></a>Samouczek: Tworzenie i modyfikowanie obwodu ExpressRoute

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [Program PowerShell](expressroute-howto-circuit-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-circuit-cli.md)
> * [Szablon usługi Azure Resource Manager](expressroute-howto-circuit-resource-manager-template.md)
> * [Wideo — Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-circuit-classic.md)
>

Ten artykuł pomoże Ci utworzyć obwodu ExpressRoute za pomocą witryny Azure portal i modelu wdrażania usługi Azure Resource Manager. Można również sprawdzić stan, aktualizacji, usunięcia lub anulować aprowizację obwodu.

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Przed rozpoczęciem konfiguracji Przejrzyj [wymagania wstępne](expressroute-prerequisites.md) i [przepływy pracy](expressroute-workflows.md) .
* Upewnij się, że masz dostęp do [Azure Portal](https://portal.azure.com).
* Upewnij się, że masz uprawnienia do tworzenia nowych zasobów sieciowych. Jeśli nie masz odpowiednich uprawnień, skontaktuj się z administratorem konta.
* [Film wideo można wyświetlić](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) przed rozpoczęciem, aby lepiej zrozumieć te czynności.

## <a name="create"></a>Tworzenie i Inicjowanie obsługi obwodu ExpressRoute

### <a name="1-sign-in-to-the-azure-portal"></a>1. Zaloguj się do Azure Portal

Przejdź w przeglądarce do witryny [Azure Portal](https://portal.azure.com) i zaloguj się przy użyciu konta platformy Azure.

### <a name="2-create-a-new-expressroute-circuit"></a>2. Utwórz nowy obwód ExpressRoute

> [!IMPORTANT]
> Obwód usługi ExpressRoute jest rozliczana w momencie utworzenia klucza usługi. Upewnij się, gdy dostawca połączenia jest gotowy do obsługi administracyjnej obwodu podczas wykonywania tej operacji.

1. Obwód usługi ExpressRoute można utworzyć, wybierając opcję utworzenia nowego zasobu. Kliknij pozycję **Utwórz zasób** > **sieci** > **ExpressRoute**, jak pokazano na poniższej ilustracji:

   ![Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit1.png)
2. Po kliknięciu przycisku **ExpressRoute**zostanie wyświetlona strona **Tworzenie obwodu ExpressRoute** . Podczas one wypełnianie wartości na tej stronie, upewnij się, że podajesz poprawne warstwa jednostki SKU (standardowa / Premium) oraz danych pomiaru użytkowania model rozliczeń (bez ograniczeń lub Metered).

   ![Konfigurowanie warstwa jednostki SKU i pomiaru danych](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit.png)

   * **Warstwa** określa, czy włączono dodatek ExpressRoute Standard lub ExpressRoute Premium. Możesz określić **Standard** , aby uzyskać standardową jednostkę SKU lub **Premium** dla dodatku Premium.
   * **Pomiar danych** określa typ rozliczeń. Możesz określić **taryfowy** plan taryfowy i **nieograniczone** dla nieograniczonego planu taryfowego. Należy pamiętać, że typ rozliczeń można zmienić z **taryfowe** na **nieograniczony**.

     > [!IMPORTANT]
     > Nie można zmienić typu z **nieograniczonego** na **taryfowy**.

   * **Lokalizacja komunikacji równorzędnej** to lokalizacja fizyczna, w której Komunikacja równorzędna jest przeprowadzana z firmą Microsoft.

     > [!IMPORTANT]
     > Lokalizacja komunikacji równorzędnej wskazuje [lokalizację fizyczną](expressroute-locations.md) , w której jest przeprowadzana Komunikacja równorzędna z firmą Microsoft. **Nie** jest to połączenie z właściwością "Location", która odnosi się do lokalizacji geograficznej, w której znajduje się dostawca zasobów sieciowych platformy Azure. Gdy nie są powiązane, jest dobrym rozwiązaniem, aby wybrać dostawca zasobów sieciowych w lokalizacji geograficznej blisko lokalizacji komunikacji równorzędnej obwodu.

### <a name="3-view-the-circuits-and-properties"></a>3. Wyświetl obwody i właściwości

**Wyświetl wszystkie obwody**

Wszystkie utworzone obwody można wyświetlić, wybierając pozycję **wszystkie zasoby** w menu po lewej stronie.

![Wyświetl obwodów](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

**Wyświetl właściwości**

Możesz wyświetlić właściwości obwodu, wybierając ją. Na stronie **Omówienie** obwodu w polu klucz usługi pojawi się klucz usługi. Należy skopiować klucza usługi dla obwodu i przekazywać je do dostawcy usług w celu ukończenia procesu inicjowania obsługi administracyjnej. Klucz usługi obwodu jest charakterystyczne dla obwodu.

![Wyświetl właściwości](./media/expressroute-howto-circuit-portal-resource-manager/servicekey1.png)

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. Wyślij klucz usługi do dostawcy połączenia w celu aprowizacji

Na tej stronie **stan dostawcy** zawiera informacje o bieżącym stanie aprowizacji po stronie Dostawca usług. **Stan obwodu** zapewnia stan po stronie firmy Microsoft. Aby uzyskać więcej informacji o Stanach aprowizacji obwodów, zobacz artykuł [przepływy pracy](expressroute-workflows.md#expressroute-circuit-provisioning-states) .

Podczas tworzenia nowego obwodu usługi ExpressRoute obwód jest w następującym stanie:

Stan dostawcy: nie zainicjowano obsługi administracyjnej<BR>
Circuit stan: włączony

![Zainicjuj proces aprowizacji](./media/expressroute-howto-circuit-portal-resource-manager/status.png)

Po dostawcy połączenia Trwa włączanie go dla Ciebie, obwodu zmienia się w następującym stanie:

Stan dostawcy: Inicjowanie obsługi administracyjnej<BR>
Circuit stan: włączony

Należy mieć możliwość użycia obwodu usługi ExpressRoute musi być w następującym stanie:

Stan dostawcy: zainicjowano obsługę administracyjną<BR>
Circuit stan: włączony

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. okresowe sprawdzanie stanu i stanu klucza obwodu

Można wyświetlić właściwości obwodu, w którym chcesz się dowiedzieć, wybierając ją. Sprawdź **stan dostawcy** i upewnij się, że został przeniesiony do programu z **obsługą administracyjną** przed kontynuowaniem.

![Stan obwodu i dostawcy](./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png)

### <a name="6-create-your-routing-configuration"></a>6. Utwórz konfigurację routingu

Aby uzyskać instrukcje krok po kroku, zapoznaj się z artykułem [Konfiguracja routingu obwodów usługi ExpressRoute](expressroute-howto-routing-portal-resource-manager.md) w celu utworzenia i zmodyfikowania komunikacji równorzędnej obwodu.

> [!IMPORTANT]
> Te instrukcje dotyczą tylko obwodów, które są utworzonych przy pomocy dostawców oferujących usługi łączności 2 warstwy. Jeśli używasz dostawcy usług, który oferuje zarządzane w warstwie 3 usługi (zazwyczaj IP sieci VPN, np. MPLS), dostawca połączenia skonfiguruje i zarządza routing.

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7. Łączenie sieci wirtualnej z obwodem ExpressRoute

Następnie łączenie sieci wirtualnej na obwód usługi ExpressRoute. Podczas pracy z modelem wdrażania Menedżer zasobów należy użyć artykułu [łączenie sieci wirtualnych do obwodów usługi ExpressRoute](expressroute-howto-linkvnet-arm.md) .

## <a name="status"></a>Pobieranie stanu obwodu ExpressRoute

Możesz wyświetlić stan obwodu, wybierając ją i wyświetlanie na stronie Przegląd.

## <a name="modify"></a>Modyfikowanie obwodu ExpressRoute

Można modyfikować niektórych właściwości obwodu usługi ExpressRoute, bez wywierania wpływu na łączność. Na stronie **Konfiguracja** można modyfikować przepustowość, jednostkę SKU, model rozliczeń i zezwalać na działania klasyczne. Aby uzyskać informacje o limitach i ograniczeniach, zobacz [często zadawane pytania](expressroute-faqs.md)dotyczące usługi ExpressRoute.

Można wykonać poniższe zadania bez przerw w dostępności:

* Włącz lub Wyłącz dodatek ExpressRoute Premium dla obwodu usługi ExpressRoute.
* Zwiększyć przepustowość obwodu usługi ExpressRoute, pod warunkiem, że pojemność dostępna na porcie.

  > [!IMPORTANT]
  > Obniżenie przepustowości obwodu nie jest obsługiwane.

* Zmień plan pomiaru z *danych mierzonych* na *dane nieograniczone*.

  > [!IMPORTANT]
  > Zmiana planu zliczania z danymi nieograniczonymi plan taryfowy z danymi nie jest obsługiwana.

* Można włączać i wyłączać *Zezwalanie na klasyczne operacje*.
  > [!IMPORTANT]
  > Może być konieczne odtworzenie obwód usługi ExpressRoute, jeśli istnieje niewystarczająca wydajność przy użyciu istniejącego portu. Nie można uaktualnić obwodu, jeśli w tej lokalizacji jest dostępna nie dodatkowej pojemności.
  >
  > Mimo że można bezproblemowo przeprowadzić uaktualnienie przepustowości, nie można zmniejszyć przepustowość obwodu usługi ExpressRoute bez przerw w działaniu. Obniżenie przepustowości wymaga anulować aprowizację obwodu usługi ExpressRoute, a następnie ponownie udostępnić nowego obwodu usługi ExpressRoute.
  >
  > Wyłączanie operacji Dodatek Premium może zakończyć się niepowodzeniem, jeśli używasz zasobów, które są większe niż co to jest dozwolone w przypadku obwód standardowy.

Aby zmodyfikować obwód ExpressRoute, kliknij pozycję **Konfiguracja**.

![Modyfikowanie obwodu](./media/expressroute-howto-circuit-portal-resource-manager/modifycircuit.png)

## <a name="delete"></a>Cofanie aprowizacji i usuwanie obwodu ExpressRoute

Obwód ExpressRoute można usunąć, wybierając ikonę **Usuń** . Zanotuj następujące informacje:

* Musisz odłączyć wszystkie sieci wirtualne od obwodu usługi ExpressRoute. Jeśli operacja zakończy się niepowodzeniem, sprawdź, czy wszystkie sieci wirtualne są połączone z obwodem.
* Jeśli stan aprowizacji dostawcy usługi obwodu ExpressRoute jest inicjowany lub **Zainicjowano obsługę administracyjną** , należy skontaktować się z dostawcą usług w celu **anulowania aprowizacji** obwodu po stronie. Firma Microsoft nadal rezerwowania zasobów oraz są naliczane, dopóki dostawcy usług wykonuje anulowanie aprowizacji obwodu i będzie powiadamiał.
* Jeśli dostawca usług anulował obsługę administracyjną obwodu (stan aprowizacji dostawcy usług jest ustawiony na **nieinicjowany**), można usunąć obwód. Spowoduje to zatrzymanie naliczania opłat za obwód.

## <a name="next-steps"></a>Następne kroki

Po utworzeniu obwodu, Kontynuuj z następujących czynności:

* [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Łączenie sieci wirtualnej z obwodem ExpressRoute](expressroute-howto-linkvnet-arm.md)
