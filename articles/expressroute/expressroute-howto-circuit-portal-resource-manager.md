---
title: Samouczek - Tworzenie i modyfikowanie obwodu za pomocą usługi ExpressRoute
description: W tym samouczku dowiesz się, jak utworzyć, aprowizować, weryfikować, aktualizować, usuwać i usuwać usługi obwód usługi ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/20/2018
ms.author: cherylmc
ms.openlocfilehash: 686ac8013879eff8adc4476d56119bbb4a169900
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74813149"
---
# <a name="tutorial-create-and-modify-an-expressroute-circuit"></a>Samouczek: Tworzenie i modyfikowanie obwodu usługi ExpressRoute

> [!div class="op_single_selector"]
> * [Portal Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [Powershell](expressroute-howto-circuit-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-circuit-cli.md)
> * [Szablon usługi Azure Resource Manager](expressroute-howto-circuit-resource-manager-template.md)
> * [Klip wideo — portal Platformy Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-circuit-classic.md)
>

Ten artykuł ułatwia tworzenie obwodu usługi ExpressRoute przy użyciu witryny Azure portal i modelu wdrażania usługi Azure Resource Manager. Można również sprawdzić stan, zaktualizować, usunąć lub anulować aprowizję obwodu.

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Przejrzyj [wymagania wstępne](expressroute-prerequisites.md) i [przepływy pracy](expressroute-workflows.md) przed rozpoczęciem konfiguracji.
* Upewnij się, że masz dostęp do [witryny Azure portal](https://portal.azure.com).
* Upewnij się, że masz uprawnienia do tworzenia nowych zasobów sieciowych. Jeśli nie masz odpowiednich uprawnień, skontaktuj się z administratorem konta.
* Możesz [obejrzeć film](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) przed rozpoczęciem, aby lepiej zrozumieć kroki.

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Tworzenie i udostępnianie obwodu usługi ExpressRoute

### <a name="1-sign-in-to-the-azure-portal"></a>1. Zaloguj się do witryny Azure portal

Przejdź w przeglądarce do witryny [Azure Portal](https://portal.azure.com) i zaloguj się przy użyciu konta platformy Azure.

### <a name="2-create-a-new-expressroute-circuit"></a>2. Tworzenie nowego obwodu usługi ExpressRoute

> [!IMPORTANT]
> Twój obwód usługi ExpressRoute jest rozliczany od momentu wystawienia klucza usługi. Upewnij się, że ta operacja jest wykonywane, gdy dostawca łączności jest gotowy do aprowizowania obwodu.

Obwód usługi ExpressRoute można utworzyć, wybierając opcję utworzenia nowego zasobu. 

1. W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób**. Wybierz **opcję Networking** > **ExpressRoute**, jak pokazano na poniższej ilustracji:

   ![Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)](./media/expressroute-howto-circuit-portal-resource-manager/create-an-expressroute-circuit.png)

2. Po kliknięciu przycisku **ExpressRoute**zostanie wyświetlona strona **obwód Utwórz usługi ExpressRoute.** Podczas wypełniania wartości na tej stronie, upewnij się, że określono poprawną warstwę SKU (Standard lub Premium) i model rozliczeń pomiaru danych (nieograniczony lub taryfowy).

   ![Konfigurowanie warstwy jednostki SKU i pomiaru danych](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit.png)

   * **Warstwa** określa, czy jest włączony standard usługi ExpressRoute, czy dodatek premium usługi ExpressRoute premium. Możesz określić **standard,** aby uzyskać standardową jednostkę SKU lub **Premium** dla dodatku premium.
   * **Pomiar danych** określa typ rozliczeń. Można określić **Metered** dla taryfowego planu taryfowego i **Nieograniczony** dla nieograniczonego planu danych. Należy pamiętać, że można zmienić typ rozliczeń z **Taryfowy** na **Nieograniczony**.

     > [!IMPORTANT]
     > Nie można zmienić typu z **Nieograniczony** na **Taryfowy**.

   * **Lokalizacja komunikacji równorzędnej** to fizyczna lokalizacja, w której można komunikacji równorzędnej z firmą Microsoft.

     > [!IMPORTANT]
     > Lokalizacja komunikacji równorzędnej wskazuje [fizyczną lokalizację,](expressroute-locations.md) w której można komunikacji równorzędnej z firmą Microsoft. **Nie** jest to połączone z właściwością "Lokalizacja", która odwołuje się do lokalizacji geograficznej, w której znajduje się dostawca zasobów sieciowych platformy Azure. Chociaż nie są one powiązane, dobrą praktyką jest wybranie dostawcy zasobów sieciowych geograficznie blisko lokalizacji komunikacji równorzędnej obwodu.

### <a name="3-view-the-circuits-and-properties"></a>3. Zobacz obwody i właściwości

**Zobacz wszystkie obwody**

Wszystkie utworzone obwody można wyświetlić, wybierając **opcję Wszystkie zasoby** w menu po lewej stronie.

![Wyświetlanie obwodów](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

**Wyświetlanie właściwości**

Można wyświetlić właściwości obwodu, wybierając go. Na stronie **Przegląd** obwodu w polu klucz serwisu pojawi się klucz serwisu. Należy skopiować klucz usługi dla obwodu i przekazać go do usługodawcy, aby zakończyć proces inicjowania obsługi administracyjnej. Klucz serwisowy obwodu jest specyficzny dla obwodu.

![Wyświetl właściwości](./media/expressroute-howto-circuit-portal-resource-manager/servicekey1.png)

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. Wyślij klucz usługi do dostawcy łączności w celu inicjowania obsługi administracyjnej

Na tej stronie **stan dostawcy** zawiera informacje o bieżącym stanie inicjowania obsługi administracyjnej po stronie dostawcy usług. **Stan obwodu** zapewnia stan po stronie firmy Microsoft. Aby uzyskać więcej informacji na temat stanów inicjowania obsługi administracyjnej obwodów, zobacz artykuł [Przepływy pracy.](expressroute-workflows.md#expressroute-circuit-provisioning-states)

Podczas tworzenia nowego obwodu usługi ExpressRoute obwód jest w następującym stanie:

Stan dostawcy: nie jest aprowidyzowana<BR>
Stan obwodu: Włączono

![Inicjowanie procesu inicjowania obsługi administracyjnej](./media/expressroute-howto-circuit-portal-resource-manager/status.png)

Obwód zmienia się w następujący stan, gdy dostawca łączności jest w trakcie włączania go dla Ciebie:

Stan dostawcy: Inicjowanie obsługi administracyjnej<BR>
Stan obwodu: Włączono

Aby można było korzystać z obwodu usługi ExpressRoute, musi on być w następującym stanie:

Stan dostawcy: Aprowizowana<BR>
Stan obwodu: Włączono

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. Okresowo sprawdzaj stan i stan przycisku obwodu

Możesz wyświetlić właściwości obwodu, który Cię interesuje, wybierając go. Sprawdź **stan dostawcy** i upewnij się, że został przeniesiony do **aprowizowanych** przed kontynuowaniem.

![Stan obwodu i dostawcy](./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png)

### <a name="6-create-your-routing-configuration"></a>6. Tworzenie konfiguracji routingu

Aby uzyskać instrukcje krok po kroku, zapoznaj się z artykułem [konfiguracji routingu obwodu usługi ExpressRoute,](expressroute-howto-routing-portal-resource-manager.md) aby utworzyć i zmodyfikować komunikację równorzędną obwodu.

> [!IMPORTANT]
> Te instrukcje dotyczą tylko obwodów utworzonych z dostawcami usług, którzy oferują usługi łączności warstwy 2. Jeśli korzystasz z dostawcy usług, który oferuje usługi zarządzanej warstwy 3 (zazwyczaj sieć VPN IP, taką jak MPLS), dostawca łączności konfiguruje i zarządza routingiem.

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7. Łączenie sieci wirtualnej z obwodem usługi ExpressRoute

Następnie połącz sieć wirtualną z obwodem usługi ExpressRoute. Podczas pracy z modelem wdrażania Menedżera zasobów należy użyć artykułu Łączenie sieci wirtualnych z [obwodami usługi ExpressRoute.](expressroute-howto-linkvnet-arm.md)

## <a name="getting-the-status-of-an-expressroute-circuit"></a><a name="status"></a>Uzyskiwanie stanu obwodu usługi ExpressRoute

Stan obwodu można wyświetlić, zaznaczając go i wyświetlając stronę Przegląd.

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Modyfikowanie obwodu usługi ExpressRoute

Można zmodyfikować niektóre właściwości obwodu usługi ExpressRoute bez wpływu na łączność. Można zmodyfikować przepustowość, jednostkę SKU, model rozliczeń i zezwolić na operacje klasyczne na stronie **Konfiguracja.** Aby uzyskać informacje na temat ograniczeń i ograniczeń, zobacz [często zadawane pytania dotyczące usługi ExpressRoute](expressroute-faqs.md).

Można wykonywać następujące zadania bez przestojów:

* Włącz lub wyłącz dodatek ExpressRoute Premium dla obwodu usługi ExpressRoute.
* Zwiększ przepustowość obwodu usługi ExpressRoute, pod warunkiem, że na porcie jest dostępna pojemność.

  > [!IMPORTANT]
  > Obniżenie przepustowości obwodu nie jest obsługiwane.

* Zmień plan pomiaru z *Dane taryfowe* na *Nieograniczone dane*.

  > [!IMPORTANT]
  > Zmiana planu pomiaru z nieograniczonych danych na dane taryfowe nie jest obsługiwana.

* Można włączyć i wyłączyć *zezwalaj na operacje klasyczne*.
  > [!IMPORTANT]
  > Może być trzeba ponownie utworzyć obwód usługi ExpressRoute, jeśli istnieje niewystarczająca pojemność na istniejącym porcie. Nie można uaktualnić obwodu, jeśli w tej lokalizacji nie ma dostępnej dodatkowej pojemności.
  >
  > Chociaż można bezproblemowo uaktualnić przepustowość, nie można zmniejszyć przepustowości obwodu usługi ExpressRoute bez zakłóceń. Obniżenie przepustowości wymaga anulowania obsługi administracyjnej obwodu usługi ExpressRoute, a następnie ponownego obsługi administracyjnej nowego obwodu usługi ExpressRoute.
  >
  > Wyłączenie operacji dodatku Premium może zakończyć się niepowodzeniem, jeśli używasz zasobów, które są większe niż to, co jest dozwolone dla obwodu standardowego.

Aby zmodyfikować obwód usługi ExpressRoute, kliknij przycisk **Konfiguracja**.

![Modyfikowanie obwodu](./media/expressroute-howto-circuit-portal-resource-manager/modify-circuit-configuration.png)

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>Anulowanie aprowizacji i usuwanie obwodu usługi ExpressRoute

Obwód usługi ExpressRoute można usunąć, wybierając ikonę **usuwania.** Zanotuj następujące informacje:

* Musisz odłączyć wszystkie sieci wirtualne od obwodu usługi ExpressRoute. Jeśli ta operacja nie powiedzie się, sprawdź, czy wszystkie sieci wirtualne są połączone z obwódem.
* Jeśli stan inicjowania obsługi administracyjnej dostawcy usług obwodu usługi ExpressRoute to **inicjowanie obsługi administracyjnej** lub **inicjowana obsługa,** należy współpracować z dostawcą usług w celu anulowania obsługi administracyjnej obwodu po ich stronie. Nadal rezerwujemy zasoby i rozliczamy cię do czasu zakończenia przez usługodawcę anulowania obsługi administracyjnej obwodu i powiadomi nas o tym.
* Jeśli dostawca usług wyrejestrował obwód (stan inicjowania obsługi administracyjnej dostawcy usług jest ustawiony na **Nie aprowizowana),** można usunąć obwód. Spowoduje to zatrzymanie naliczania opłat za obwód.

## <a name="next-steps"></a>Następne kroki

Po utworzeniu obwodu należy wykonać następujące czynności:

* [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Łączenie sieci wirtualnej z obwodem usługi ExpressRoute](expressroute-howto-linkvnet-arm.md)
