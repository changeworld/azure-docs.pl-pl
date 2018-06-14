---
title: 'Tworzenie i modyfikowanie obwodu usługi ExpressRoute: portalu Azure | Dokumentacja firmy Microsoft'
description: W tym artykule opisano sposób tworzenia, obsługi administracyjnej, sprawdź, aktualizacji, usuwania i anulowanie zastrzeżenia obwodu usługi ExpressRoute.
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 68d59d59-ed4d-482f-9cbc-534ebb090613
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/20/2017
ms.author: cherylmc;ganesr
ms.openlocfilehash: be97f441d6eb2e97d85d598b0ed66718aec97b14
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
ms.locfileid: "29389516"
---
# <a name="create-and-modify-an-expressroute-circuit"></a>Tworzenie i modyfikowanie obwodu usługi ExpressRoute
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [Program PowerShell](expressroute-howto-circuit-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-circuit-cli.md)
> * [Video - portalu Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-circuit-classic.md)
>

W tym artykule opisano sposób tworzenia obwodu usługi Azure ExpressRoute przy użyciu portalu Azure i modelu wdrażania usługi Azure Resource Manager. Poniższe kroki przedstawiają także sposób Sprawdź stan obwodu, zaktualizować lub usunąć i anulowanie zastrzeżenia go.


## <a name="before-you-begin"></a>Przed rozpoczęciem
* Przegląd [wymagania wstępne](expressroute-prerequisites.md) i [przepływy pracy](expressroute-workflows.md) przed rozpoczęciem konfigurowania.
* Upewnij się, że masz dostęp do [portalu Azure](https://portal.azure.com).
* Upewnij się, że masz uprawnienia do tworzenia nowych zasobów sieciowych. Jeśli nie masz odpowiednich uprawnień, skontaktuj się z administratorem konta.
* Możesz [wyświetlanie wideo](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) przed rozpoczęciem, aby lepiej zrozumieć kroki.

## <a name="create"></a>Tworzenie i przydzielanie obwodu usługi ExpressRoute
### <a name="1-sign-in-to-the-azure-portal"></a>1. Logowanie się do witryny Azure Portal
Przejdź w przeglądarce do witryny [Azure Portal](http://portal.azure.com) i zaloguj się przy użyciu konta platformy Azure.

### <a name="2-create-a-new-expressroute-circuit"></a>2. Utwórz nowy obwód usługi ExpressRoute
> [!IMPORTANT]
> Jest on rozliczany od momentu jego wystawieniu klucz usługi obwodu usługi ExpressRoute. Upewnij się, gdy dostawca łączności jest gotowy do udostępniania obwodu podczas wykonywania tej operacji.
> 
> 

1. Można utworzyć obwodu usługi ExpressRoute, wybierając opcję, aby utworzyć nowy zasób. Kliknij przycisk **Utwórz zasób** > **sieci** > **ExpressRoute**, jak pokazano na poniższej ilustracji:

  ![Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit1.png)
2. Po kliknięciu **ExpressRoute**, zobaczysz **obwodu ExpressRoute utworzyć** strony. Gdy jest wypełnianie wartości na tej stronie, upewnij się, należy określić prawidłowe jednostki SKU warstwy (standardowy lub Premium) i danych zliczania modelu rozliczeń (bez ograniczeń lub Metered).

  ![Skonfiguruj warstwy jednostki SKU i pomiaru danych](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit.png)

  * **Warstwa** Określa, czy włączone jest standardem ExpressRoute lub dodatek usługi ExpressRoute w warstwie premium. Można określić **standardowe** można pobrać wersji standard lub **Premium** dla dodatku premium.
  * **Pomiaru danych** Określa typ rozliczeń. Można określić **Metered** planu dane naliczane i **nieograniczone** planu nieograniczone danych. Zmiana rozliczeń typu z **Metered** do **bez ograniczeń**, ale nie można zmienić typu z **bez ograniczeń** do **Metered**.
  * **Lokalizacja komunikacji równorzędnej** jest fizycznej lokalizacji, gdzie są komunikacji równorzędnej z firmą Microsoft.

    > [!IMPORTANT]
    > Wskazuje lokalizację równorzędna [lokalizacji fizycznej](expressroute-locations.md) gdzie są komunikacji równorzędnej z firmą Microsoft. Jest to **nie** połączone z właściwością "Lokalizacja", która odwołuje się do lokalizacji geograficznej, w którym znajduje się dostawcy zasobów sieciowych Azure. Gdy nie są powiązane, jest dobrym rozwiązaniem, aby wybrać dostawcy zasobów sieciowych w lokalizacji geograficznej blisko lokalizacji elementu równorzędnego obwodu.
    >
    >

### <a name="3-view-the-circuits-and-properties"></a>3. Wyświetl obwody i właściwości
**Wyświetlanie wszystkich obwodów**

Można wyświetlić wszystkich obwodów, które zostały utworzone przez wybranie **wszystkie zasoby** w menu po lewej stronie.

![Obwody widoku](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

**Wyświetl właściwości**

Właściwości obwodu można wyświetlić, wybierając go. Na **omówienie** dla obwodu, strony klucza usługi pojawia się w polu klucza usługi. Należy skopiować klucza usługi dla obwodu i przekaż go do dostawcy usług, aby ukończyć proces inicjowania obsługi administracyjnej. Klucz usługi obwodu jest specyficzne dla obwodu.

![Wyświetl właściwości](./media/expressroute-howto-circuit-portal-resource-manager/servicekey1.png)

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. Wyślij klucz usługi do dostawcą połączenia do inicjowania obsługi
Na tej stronie **stan dostawcy** zawiera informacje o bieżącym stanie inicjowania obsługi administracyjnej po stronie dostawcy usług. **Stan obwodu** zapewnia stanu po stronie firmy Microsoft. Aby uzyskać więcej informacji na temat obwodu inicjowania obsługi administracyjnej stanów zobacz [przepływy pracy](expressroute-workflows.md#expressroute-circuit-provisioning-states) artykułu.

Podczas tworzenia nowego obwodu ExpressRoute obwodu znajduje się w następującym stanie:

Stan dostawcy: nie zainicjowano obsługę administracyjną<BR>
Stan obwodu: włączone

![Zainicjuj proces inicjowania obsługi administracyjnej](./media/expressroute-howto-circuit-portal-resource-manager/status.png)

Po dostawca połączenia trwa jej włączanie obwodu zmienia się na następujący:

Stan dostawcy: Inicjowanie obsługi administracyjnej<BR>
Stan obwodu: włączone

Dla Ciebie można było używać obwodu usługi ExpressRoute musi być w następującym stanie:

Stan dostawcy: zainicjowano obsługę administracyjną<BR>
Stan obwodu: włączone

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. Okresowo sprawdzać stan i stan klucz obwodu
Można wyświetlić właściwości obwodu, w którym są zainteresowani przez zaznaczenie go. Sprawdź **stan dostawcy** i upewnij się, że została przeniesiona do **obsługiwane administracyjnie** przed kontynuowaniem.

![Stan obwodu i dostawcy](./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png)

### <a name="6-create-your-routing-configuration"></a>6. Tworzenie konfiguracji routingu
Aby uzyskać instrukcje, zapoznaj się [obwodu ExpressRoute konfiguracji routingu](expressroute-howto-routing-portal-resource-manager.md) artykułu do tworzenia i modyfikowania obwodu komunikacji równorzędnych.

> [!IMPORTANT]
> Te instrukcje dotyczą tylko obwody, które zostały utworzone z dostawców usług, które oferują warstwy 2 łączności usługi. Jeśli używasz usługodawcy, który oferuje zarządzanych warstwy 3 usługi (zazwyczaj IP sieci VPN, takie jak MPLS), dostawca połączenia konfiguruje i zarządza nimi routingu dla Ciebie.
> 
> 

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7. Łączenie sieci wirtualnej z obwodem usługi ExpressRoute
Następnie połączyć sieć wirtualną obwodu usługi ExpressRoute. Użyj [łączenia sieci wirtualne obwody usługi ExpressRoute](expressroute-howto-linkvnet-arm.md) artykuł podczas pracy z modelu wdrażania usługi Resource Manager.

## <a name="status"></a>Pobieranie stanu obwodu usługi ExpressRoute
Stan obwodu można wyświetlić, wybierając ją i wyświetlania strony Przegląd. 

## <a name="modify"></a>Modyfikowanie obwodu usługi ExpressRoute
Można zmodyfikować niektórych właściwości obwodu usługi ExpressRoute, bez wywierania wpływu na łączność. Można modyfikować modelu rozliczeń przepustowości, jednostka SKU i Zezwalaj na operacje klasycznego **konfiguracji** strony. Informacje dotyczące limity i ograniczenia, zobacz [ExpressRoute — często zadawane pytania](expressroute-faqs.md). 

Można wykonać poniższe zadania bez przestojów:

* Włącz lub Wyłącz dodatek Premium usługi ExpressRoute dla obwodu usługi ExpressRoute.
* Zwiększyć przepustowość obwodu ExpressRoute, pod warunkiem Brak dostępnej pojemności na porcie. Przepustowości obwodu zmiana wersji na starszą nie jest obsługiwane. 
* Zmień plan zliczania z *naliczane danych* do *dane nieograniczone*. Zmiany planu zliczania dane nieograniczone naliczane danych nie jest obsługiwana.
* Można włączyć lub wyłączyć *operacje klasycznego*.

> [!IMPORTANT]
> Może być konieczne ponowne utworzenie obwodu usługi expressroute w przypadku niewystarczającego pojemności na istniejącego portu. Nie można uaktualnić obwodu, jeśli nie bez dodatkowej pojemności dostępnej w tej lokalizacji.
>
> Chociaż możesz bezproblemowo uaktualnić przepustowości, nie można zmniejszyć przepustowość obwodu usługi ExpressRoute bez zakłóceń. Zmiana wersji na starszą przepustowości wymaga anulowanie zastrzeżenia obwodu ExpressRoute, a następnie Udostępnij ponownie nowy obwód usługi ExpressRoute.
> 
> Wyłączenie operacji Dodatek Premium może zakończyć się niepowodzeniem, jeśli używasz zasobów, które są większe niż co to jest dozwolone dla standardowych obwodu.
> 
> 

Aby zmodyfikować obwodu usługi ExpressRoute, kliknij przycisk **konfiguracji**.

![Modyfikowanie obwodu](./media/expressroute-howto-circuit-portal-resource-manager/modifycircuit.png)




## <a name="delete"></a>Anulowania obsługi i usuwania obwodu usługi ExpressRoute
Można usunąć obwodu usługi ExpressRoute, wybierając **usunąć** ikony. Należy uwzględnić następujące informacje:

* Należy odłączyć wszystkie sieci wirtualne z obwodem usługi ExpressRoute. Jeśli ta operacja nie powiedzie się, sprawdź, czy wszystkie sieci wirtualne są połączone z obwodem.
* Jeśli dostawca usługi obwodu ExpressRoute stan inicjowania obsługi jest **inicjowania obsługi administracyjnej** lub **obsługiwane administracyjnie** należy skontaktować się z dostawcą usług na anulowanie zastrzeżenia obwód w bok. Będziemy nadal zarezerwować zasobów i obciążać Cię do czasu dostawcy usług wykonuje anulowania obsługi obwodu i powiadomienia NAS.
* Jeśli usługodawca została anulowana obwodu (ustawiono dostawcę usługi stan inicjowania obsługi **nieudostępniane**), można usunąć obwodu. Powoduje to zatrzymanie rozliczeń dla obwodu.

## <a name="next-steps"></a>Kolejne kroki
Po utworzeniu obwodu kontynuować z następujących czynności:

* [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Link sieci wirtualnej do obwodu usługi ExpressRoute](expressroute-howto-linkvnet-arm.md)
