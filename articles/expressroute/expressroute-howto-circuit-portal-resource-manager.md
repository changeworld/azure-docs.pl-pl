---
title: 'Tworzenie i modyfikowanie obwodu ExpressRoute — Portal: Azure | Microsoft Docs'
description: Tworzenie, aprowizować, sprawdź, aktualizacji, usuwania i anulować aprowizację obwodu usługi ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 10/20/2018
ms.author: cherylmc
ms.reviewer: ganesr
ms.custom: seodec18
ms.openlocfilehash: 25821f60f47b1279e70cba2574901cd5df3d327f
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846584"
---
# <a name="create-and-modify-an-expressroute-circuit"></a>Tworzenie i modyfikowanie obwodu usługi ExpressRoute

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [Program PowerShell](expressroute-howto-circuit-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-circuit-cli.md)
> * [Szablon usługi Azure Resource Manager](expressroute-howto-circuit-resource-manager-template.md)
> * [Wideo — witryna Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-circuit-classic.md)
>

Ten artykuł pomoże Ci utworzyć obwodu ExpressRoute za pomocą witryny Azure portal i modelu wdrażania usługi Azure Resource Manager. Można również sprawdzić stan, aktualizacji, usunięcia lub anulować aprowizację obwodu.

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Przegląd [wymagania wstępne](expressroute-prerequisites.md) i [przepływy pracy](expressroute-workflows.md) przed rozpoczęciem konfiguracji.
* Upewnij się, że masz dostęp do [witryny Azure portal](https://portal.azure.com).
* Upewnij się, że masz uprawnienia do tworzenia nowych zasobów sieciowych. Jeśli nie masz odpowiednich uprawnień, skontaktuj się z administratorem konta.
* Możesz [klip wideo](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) przed rozpoczęciem, aby lepiej zrozumieć kroki.

## <a name="create"></a>Tworzenie i aprowizowanie obwodu usługi ExpressRoute

### <a name="1-sign-in-to-the-azure-portal"></a>1. Logowanie się do witryny Azure Portal

Przejdź w przeglądarce do witryny [Azure Portal](https://portal.azure.com) i zaloguj się przy użyciu konta platformy Azure.

### <a name="2-create-a-new-expressroute-circuit"></a>2. Tworzenie nowego obwodu usługi ExpressRoute

> [!IMPORTANT]
> Obwód usługi ExpressRoute jest rozliczana w momencie utworzenia klucza usługi. Upewnij się, gdy dostawca połączenia jest gotowy do obsługi administracyjnej obwodu podczas wykonywania tej operacji.

1. Obwód usługi ExpressRoute można utworzyć, wybierając opcję utworzenia nowego zasobu. Kliknij przycisk **Utwórz zasób** > **sieć** > **ExpressRoute**, jak pokazano na poniższej ilustracji:

   ![Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit1.png)
2. Po kliknięciu **ExpressRoute**, zobaczysz **obwodu ExpressRoute tworzenie** strony. Podczas one wypełnianie wartości na tej stronie, upewnij się, że podajesz poprawne warstwa jednostki SKU (standardowa / Premium) oraz danych pomiaru użytkowania model rozliczeń (bez ograniczeń lub Metered).

   ![Konfigurowanie warstwa jednostki SKU i pomiaru danych](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit.png)

   * **Warstwa** Określa, czy włączona jest standardem usługi ExpressRoute lub dodatek ExpressRoute premium. Można określić **standardowa** można pobrać standardowej jednostki SKU lub **Premium** używania dodatku premium.
   * **Funkcji pomiaru danych** Określa typ rozliczeń. Można określić **Metered** plan taryfowy z danymi i **nieograniczone** dla plan z nieograniczonymi danymi. Należy pamiętać, że typ rozliczeń można  zmienić z taryfowe na **nieograniczony**.

     > [!IMPORTANT]
     > Nie można zmienić typu z nieograniczonego na **taryfowy**.

   * **Lokalizacja komunikacji równorzędnej** jest lokalizacji fizycznej, w którym są komunikacji równorzędnej z firmą Microsoft.

     > [!IMPORTANT]
     > Lokalizacja komunikacji równorzędnej wskazuje [lokalizacji fizycznej](expressroute-locations.md) gdzie są komunikacji równorzędnej z firmą Microsoft. Jest to **nie** połączone z właściwością "Lokalizacja", która odwołuje się do lokalizacji geograficznej, w którym znajduje się dostawca zasobów sieciowych platformy Azure. Gdy nie są powiązane, jest dobrym rozwiązaniem, aby wybrać dostawca zasobów sieciowych w lokalizacji geograficznej blisko lokalizacji komunikacji równorzędnej obwodu.

### <a name="3-view-the-circuits-and-properties"></a>3. Wyświetlanie właściwości i obwodów

**Wyświetlanie wszystkich obwodów**

Można wyświetlić wszystkich obwodów utworzonych przez wybranie **wszystkie zasoby** w menu po lewej stronie.

![Wyświetl obwodów](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

**Wyświetl właściwości**

Możesz wyświetlić właściwości obwodu, wybierając ją. Na **Przegląd** stronie Twojego obwodu, klucz usługi pojawia się w polu klucza usługi. Należy skopiować klucza usługi dla obwodu i przekazywać je do dostawcy usług w celu ukończenia procesu inicjowania obsługi administracyjnej. Klucz usługi obwodu jest charakterystyczne dla obwodu.

![Wyświetl właściwości](./media/expressroute-howto-circuit-portal-resource-manager/servicekey1.png)

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. Wyślij klucz usługi dostawcy łączności dla inicjowania obsługi administracyjnej

Na tej stronie **stan dostawcy** zawiera informacje dotyczące bieżącego stanu aprowizacji po stronie dostawcy usług. **Circuit stan** zapewnia stanu po stronie firmy Microsoft. Aby uzyskać więcej informacji o stanach aprowizacji obwodu, zobacz [przepływy pracy](expressroute-workflows.md#expressroute-circuit-provisioning-states) artykułu.

Podczas tworzenia nowego obwodu usługi ExpressRoute obwód jest w następującym stanie:

Stan dostawcy: Nieobsługiwane<BR>
Stan obwodu: Włączono

![Zainicjuj proces aprowizacji](./media/expressroute-howto-circuit-portal-resource-manager/status.png)

Po dostawcy połączenia Trwa włączanie go dla Ciebie, obwodu zmienia się w następującym stanie:

Stan dostawcy: Inicjowanie obsługi<BR>
Stan obwodu: Włączono

Należy mieć możliwość użycia obwodu usługi ExpressRoute musi być w następującym stanie:

Stan dostawcy: Zaaprowizowane<BR>
Stan obwodu: Włączono

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. Okresowo sprawdzać stan i stan klawisza obwodu

Można wyświetlić właściwości obwodu, w którym chcesz się dowiedzieć, wybierając ją. Sprawdź **stan dostawcy** i upewnij się, że została przeniesiona do **Aprowizowana** przed kontynuowaniem.

![Stan obwodu i dostawcy](./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png)

### <a name="6-create-your-routing-configuration"></a>6. Utwórz konfigurację routingu

Aby uzyskać instrukcje krok po kroku dotyczą [obwód usługi ExpressRoute, konfiguracji routingu](expressroute-howto-routing-portal-resource-manager.md) artykuł, aby tworzyć i modyfikować komunikacja równorzędna obwodu.

> [!IMPORTANT]
> Te instrukcje dotyczą tylko obwodów, które są utworzonych przy pomocy dostawców oferujących usługi łączności 2 warstwy. Jeśli używasz dostawcy usług, który oferuje zarządzane w warstwie 3 usługi (zazwyczaj IP sieci VPN, np. MPLS), dostawca połączenia skonfiguruje i zarządza routing.

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7. Łączenie sieci wirtualnej z obwodem usługi ExpressRoute

Następnie łączenie sieci wirtualnej na obwód usługi ExpressRoute. Użyj [łączenie sieci wirtualnych obwodów usługi ExpressRoute](expressroute-howto-linkvnet-arm.md) artykuł podczas pracy z modelem wdrażania usługi Resource Manager.

## <a name="status"></a>Uzyskiwanie stanu obwodu usługi ExpressRoute

Możesz wyświetlić stan obwodu, wybierając ją i wyświetlanie na stronie Przegląd.

## <a name="modify"></a>Modyfikowanie obwodu usługi ExpressRoute

Można modyfikować niektórych właściwości obwodu usługi ExpressRoute, bez wywierania wpływu na łączność. Możesz zmodyfikować model rozliczeń za przepustowość, jednostki SKU i i Zezwalaj na klasyczne operacje na **konfiguracji** strony. Aby uzyskać informacje na temat limity i ograniczenia, zobacz [ExpressRoute — często zadawane pytania](expressroute-faqs.md).

Można wykonać poniższe zadania bez przerw w dostępności:

* Włącz lub Wyłącz dodatek ExpressRoute Premium dla obwodu usługi ExpressRoute.
* Zwiększyć przepustowość obwodu usługi ExpressRoute, pod warunkiem, że pojemność dostępna na porcie.

  > [!IMPORTANT]
  > Obniżenie przepustowości obwodu nie jest obsługiwane.

* Zmień zliczania plan z *plan taryfowy z danymi* do *dane nieograniczone*.

  > [!IMPORTANT]
  > Zmiana planu zliczania z danymi nieograniczonymi plan taryfowy z danymi nie jest obsługiwana.

* Można włączać i wyłączać *Zezwalaj na klasyczne operacje*.
  > [!IMPORTANT]
  > Może być konieczne odtworzenie obwód usługi ExpressRoute, jeśli istnieje niewystarczająca wydajność przy użyciu istniejącego portu. Nie można uaktualnić obwodu, jeśli w tej lokalizacji jest dostępna nie dodatkowej pojemności.
  >
  > Mimo że można bezproblemowo przeprowadzić uaktualnienie przepustowości, nie można zmniejszyć przepustowość obwodu usługi ExpressRoute bez przerw w działaniu. Obniżenie przepustowości wymaga anulować aprowizację obwodu usługi ExpressRoute, a następnie ponownie udostępnić nowego obwodu usługi ExpressRoute.
  >
  > Wyłączanie operacji Dodatek Premium może zakończyć się niepowodzeniem, jeśli używasz zasobów, które są większe niż co to jest dozwolone w przypadku obwód standardowy.

Aby zmodyfikować obwodu usługi ExpressRoute, kliknij **konfiguracji**.

![Modyfikowanie obwodu](./media/expressroute-howto-circuit-portal-resource-manager/modifycircuit.png)

## <a name="delete"></a>Anulowanie aprowizacji i usuwanie obwodu usługi ExpressRoute

Możesz usunąć obwód usługi ExpressRoute, wybierając **Usuń** ikony. Zanotuj następujące informacje:

* Musisz odłączyć wszystkie sieci wirtualne od obwodu usługi ExpressRoute. Jeśli operacja zakończy się niepowodzeniem, sprawdź, czy wszystkie sieci wirtualne są połączone z obwodem.
* Jeśli dostawca usług obwodu usługi ExpressRoute, w stanie inicjowania obsługi **aprowizacji** lub **Aprowizowana** należy skontaktować się z dostawcą usługi, aby anulować aprowizację obwodu po ich stronie. Firma Microsoft nadal rezerwowania zasobów oraz są naliczane, dopóki dostawcy usług wykonuje anulowanie aprowizacji obwodu i będzie powiadamiał.
* Jeśli dostawca usług ma anulowanie aprowizacji obwodu (stan aprowizacji dostawcy usług jest równa **nie zainicjowano obsługi administracyjnej**), można usunąć obwodu. Spowoduje to zatrzymanie naliczania opłat za obwód.

## <a name="next-steps"></a>Kolejne kroki

Po utworzeniu obwodu, Kontynuuj z następujących czynności:

* [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Łączenie sieci wirtualnej na obwód usługi ExpressRoute](expressroute-howto-linkvnet-arm.md)
