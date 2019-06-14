---
title: 'Łączenie sieci wirtualnej z obwodem — ExpressRoute: Witryna Azure portal | Dokumentacja firmy Microsoft'
description: Łączenie sieci wirtualnej z obwodem usługi ExpressRoute platformy Azure. Instrukcje.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 66c43556e698063b95ce1c3787458e40c9d71504
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60365594"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>Łączenie sieci wirtualnej z obwodem usługi ExpressRoute za pomocą portalu
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [Program PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-linkvnet-cli.md)
> * [Wideo — witryna Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-linkvnet-classic.md)
> 

Ten artykuł ułatwia tworzenie połączenia do łączenia sieci wirtualnej z obwodem usługi ExpressRoute platformy Azure przy użyciu witryny Azure portal. Sieci wirtualne, z którymi się łączysz obwód usługi ExpressRoute platformy Azure może być w tej samej subskrypcji lub mogą być częścią innej subskrypcji.

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Przegląd [wymagania wstępne](expressroute-prerequisites.md), [wymagania dotyczące routingu](expressroute-routing.md), i [przepływy pracy](expressroute-workflows.md) przed rozpoczęciem konfiguracji.

* Musisz mieć aktywny obwód usługi ExpressRoute.
  * Postępuj zgodnie z instrukcjami, aby [utworzyć obwód usługi ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) i który powinien zostać włączony przez dostawcę połączenia.
  * Upewnij się, że prywatnej komunikacji równorzędnej Azure skonfigurowany dla obwodu. Zobacz [Konfigurowanie routingu](expressroute-howto-routing-portal-resource-manager.md) artykuł, aby uzyskać instrukcje routingu.
  * Upewnij się, że skonfigurowano prywatnej komunikacji równorzędnej Azure i komunikację równorzędną BGP między siecią a Microsoft działa tak, aby umożliwić łączność end-to-end.
  * Upewnij się, że masz sieci wirtualnej i bramy sieci wirtualnej, utworzona i w pełni zaaprowizowanym. Postępuj zgodnie z instrukcjami, aby [Tworzenie bramy sieci wirtualnej dla usługi ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Brama sieci wirtualnej dla usługi ExpressRoute używa GatewayType "ExpressRoute", nie sieci VPN.

* Maksymalnie 10 sieciami wirtualnymi można połączyć standardowy obwód usługi ExpressRoute. Wszystkie sieci wirtualne muszą znajdować się w tym samym regionie geograficznymi, korzystając z standardowy obwód usługi ExpressRoute.

* Pojedynczej sieci wirtualnej można połączyć maksymalnie cztery obwodów usługi ExpressRoute. Użyj poniżej proces, aby utworzyć nowy obiekt połączenia dla każdy obwód usługi ExpressRoute, z którą jest nawiązywane. Obwody usługi ExpressRoute może być w tej samej subskrypcji, w różnych subskrypcjach lub kombinacji obu.

* Łączenie sieci wirtualnej poza region geopolityczny obwodu usługi ExpressRoute lub połączenia większej liczby sieci wirtualnych z obwód usługi ExpressRoute, jeśli został włączony dodatek premium usługi ExpressRoute. Sprawdź [— często zadawane pytania](expressroute-faqs.md) więcej informacji na temat używania dodatku premium.

* Możesz [klip wideo](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit) przed rozpoczęciem lepiej zrozumieć kroki.

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>Połączenie sieci wirtualnej z obwodem — w tej samej subskrypcji

> [!NOTE]
> Informacje o konfiguracji protokołu BGP nie pojawią się jeśli dostawca warstwy 3 skonfigurowany z komunikacji równorzędnej. Jeśli Twój obwód ma stan aprowizowano, należy utworzyć połączenia.
>

### <a name="to-create-a-connection"></a>Aby utworzyć połączenie

1. Upewnij się, że obwód usługi ExpressRoute i prywatnej komunikacji równorzędnej Azure zostały pomyślnie skonfigurowane. Postępuj zgodnie z instrukcjami w [utworzyć obwód usługi ExpressRoute](expressroute-howto-circuit-arm.md) i [Konfigurowanie routingu](expressroute-howto-routing-arm.md). Obwód usługi ExpressRoute powinien wyglądać podobnie do następującego:

   ![Zrzut ekranu z obwodem usługi ExpressRoute](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png)
2. Możesz teraz rozpocząć Inicjowanie obsługi administracyjnej połączenia do łączenia bramy sieci wirtualnej na obwód usługi ExpressRoute. Kliknij przycisk **połączenia** > **Dodaj** otworzyć **Dodaj połączenie** strony, a następnie skonfiguruj wartości.

   ![Dodaj zrzut ekranu połączenia](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png)
3. Po pomyślnym skonfigurowaniu połączenia obiektu połączenia będą zawierają informacje dotyczące połączenia.

   ![Zrzut ekranu obiektu połączenia](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)

## <a name="connect-a-vnet-to-a-circuit---different-subscription"></a>Połączenie sieci wirtualnej z obwodem — w innej subskrypcji

Obwód usługi ExpressRoute można udostępniać w wielu subskrypcjach. Na poniższym rysunku przedstawiono prosty schemat działa jak udostępniania dla obwodów usługi ExpressRoute w wielu subskrypcjach.

![Łączność między subskrypcjami](./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png)

- Każdy z mniejszych chmury w chmurze dużych jest używana do reprezentowania subskrypcje, które należą do różnych działów w organizacji.
- Każdego z działów w organizacji można użyć własnej subskrypcji do wdrażania ich usług, ale mogą oni udostępnić pojedynczego obwodu usługi ExpressRoute i połączyć się ponownie z Twojej sieci lokalnej.
- Jednego działu (w tym przykładzie: IT) mogą być właścicielami obwód usługi ExpressRoute. Inne subskrypcje w ramach organizacji, można użyć obwodu usługi ExpressRoute i autoryzacji skojarzone z obwodu, łącznie z subskrypcjami powiązanymi z innych dzierżaw usługi Azure Active Directory i rejestracji umowy Enterprise Agreement.

  > [!NOTE]
  > Połączeniami i przepustowością opłat za obwód dedykowany będą stosowane do właściciela obwodu usługi ExpressRoute. Wszystkie sieci wirtualne współdzielą ten sam przepustowość.
  >
  >

### <a name="administration---about-circuit-owners-and-circuit-users"></a>Administrowanie — o obwodu właścicieli i użytkowników obwodu

Właściciela obwodu jest autoryzowanym użytkownikiem Power zasobu obwodu usługi ExpressRoute. Właściciela obwodu tworzyć autoryzacje do zrealizowania przez "Użytkownicy obwodu". Użytkownicy obwodu są właścicielami bram sieci wirtualnych, które nie znajdują się w innej subskrypcji niż obwód usługi ExpressRoute. Użytkownicy obwodu można realizować autoryzacje (jeden autoryzację dla pojedynczej sieci wirtualnej).

Właściciela obwodu ma uprawnienia do modyfikowania i odwołać autoryzacji w dowolnym momencie. Cofanie autoryzacji skutkuje wszystkich połączeń usuwana z subskrypcji, do których dostęp został odwołany.

### <a name="circuit-owner-operations"></a>Operacje właściciela obwodu

**Aby utworzyć tymczasową autoryzację połączenia**

Właściciela obwodu tworzy autoryzacji. Powoduje to utworzenie klucza autoryzacji, który może służyć przez użytkowników obwodu połączyć ich bramy sieci wirtualnej z obwodem usługi ExpressRoute. Autoryzacja jest prawidłowe dla tylko jedno połączenie.

> [!NOTE]
> Każde połączenie wymaga oddzielnych autoryzacji.
>

1. Na stronie usługi ExpressRoute, kliknij przycisk **autoryzacji** , a następnie wpisz **nazwa** autoryzacji, a następnie kliknij przycisk **Zapisz**.

   ![Autoryzacje](./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png)
2. Po zapisaniu konfiguracji Skopiuj **identyfikator zasobu** i **klucza autoryzacji**.

   ![Klucz autoryzacji](./media/expressroute-howto-linkvnet-portal-resource-manager/authkey.png)

**Można usunąć autoryzacji połączenia**

Połączenie można usunąć, wybierając **Usuń** ikonę na stronie połączenia.

### <a name="circuit-user-operations"></a>Operacje użytkownika obwodu

Obwód trzeba podać identyfikator zasobu i klucza autoryzacji z właściciela obwodu.

**Aby zrealizować autoryzacji połączenia**

1. Kliknij przycisk **+ nowy** przycisku.

   ![Kliknij przycisk Nowy](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection1.png)
2. Wyszukaj **"Connection"** w witrynie Marketplace, zaznacz go, a następnie kliknij przycisk **Utwórz**.

   ![Szukaj połączeń](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection2.png)
3. Upewnij się, że **typu połączenia** jest ustawiona na "ExpressRoute".
4. Wypełnij szczegóły, a następnie kliknij przycisk **OK** na stronie podstawy.

   ![Podstawowe informacje o stronie](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection3.png)
5. W **ustawienia** stronie wybierz **bramy sieci wirtualnej** i sprawdź **Zrealizuj autoryzację** pole wyboru.
6. Wprowadź **klucza autoryzacji** i **identyfikator URI obwodu równorzędnego** i nazwij połączenie. Kliknij przycisk **OK**.

   ![Strona Ustawienia](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection4.png)
7. Zapoznaj się z informacjami w **Podsumowanie** strony, a następnie kliknij przycisk **OK**.

**Aby zwolnić autoryzacji połączenia**

Autoryzacji można zwolnić, usuwając połączenia prowadzący obwód usługi ExpressRoute w sieci wirtualnej.

## <a name="delete-a-connection-to-unlink-a-vnet"></a>Usuwanie połączenia można odłączyć sieci wirtualnej

Można usunąć połączenia i odłączyć sieci wirtualnej z obwodem usługi ExpressRoute, wybierając **Usuń** ikonę na stronie połączenia.

## <a name="next-steps"></a>Kolejne kroki
Więcej informacji na temat usługi ExpressRoute znajduje się w artykule [ExpressRoute FAQ](expressroute-faqs.md) (Usługa ExpressRoute — często zadawane pytania).
