---
title: 'Usługa ExpressRoute: łączenie sieci wirtualnej z obwodem: Witryna Azure portal'
description: Podłączanie sieci wirtualnej do obwodu usługi Azure ExpressRoute. Instrukcje.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 4c7a24ad692086398059d1afd48c8927e9d18582
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272918"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>Nawiązywanie połączenia sieci wirtualnej z obwodem usługi ExpressRoute za pomocą witryny Portal
> [!div class="op_single_selector"]
> * [Portal Azure](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [Powershell](expressroute-howto-linkvnet-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-linkvnet-cli.md)
> * [Klip wideo — portal Platformy Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-linkvnet-classic.md)
> 

Ten artykuł ułatwia utworzenie połączenia w celu połączenia sieci wirtualnej z obwodem usługi Azure ExpressRoute przy użyciu witryny Azure portal. Sieci wirtualne, które łączysz się z obwodem usługi Azure ExpressRoute może być w tej samej subskrypcji lub mogą być częścią innej subskrypcji.

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Przed rozpoczęciem konfiguracji należy zapoznać się [z wymaganiami wstępnymi,](expressroute-prerequisites.md) [wymaganiami dotyczącymi routingu](expressroute-routing.md)i [przepływami pracy.](expressroute-workflows.md)

* Musisz mieć aktywny obwód usługi ExpressRoute.
  * Postępuj zgodnie z instrukcjami, aby [utworzyć obwód usługi ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) i włączyć obwód przez dostawcę łączności.
  * Upewnij się, że masz skonfigurowany private peering platformy Azure dla obwodu. Zobacz [Tworzenie i modyfikowanie komunikacji równorzędnej dla obwodu usługi ExpressRoute](expressroute-howto-routing-portal-resource-manager.md) artykułu dla komunikacji równorzędnej i routingu instrukcje.
  * Upewnij się, że prywatna komunikacja równorzędna platformy Azure jest skonfigurowana, a komunikacja równorzędna BGP między siecią a firmą Microsoft jest w górę, dzięki czemu można włączyć łączność end-to-end.
  * Upewnij się, że masz sieć wirtualną i bramę sieci wirtualnej utworzone i w pełni aprowizowana. Postępuj zgodnie z instrukcjami, aby [utworzyć bramę sieci wirtualnej dla usługi ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Brama sieci wirtualnej dla usługi ExpressRoute używa gatewayType "ExpressRoute", a nie SIECI VPN.

* Do standardowego obwodu usługi ExpressRoute można połączyć do 10 sieci wirtualnych. Wszystkie sieci wirtualne muszą znajdować się w tym samym regionie geopolitycznym podczas korzystania ze standardowego obwodu usługi ExpressRoute.

* Pojedyncza sieci wirtualnej mogą być połączone z maksymalnie czterech obwodów usługi ExpressRoute. Użyj poniższego procesu, aby utworzyć nowy obiekt połączenia dla każdego obwodu usługi ExpressRoute, z który się łączysz. Obwody usługi ExpressRoute mogą być w tej samej subskrypcji, różnych subskrypcji lub kombinacji obu.

* Sieć wirtualną można połączyć poza regionem geopolitycznym obwodu usługi ExpressRoute lub połączyć większą liczbę sieci wirtualnych z obwodem usługi ExpressRoute, jeśli włączono dodatek Usługi ExpressRoute premium. Więcej informacji na temat dodatku premium można znaleźć w [często zadawanych](expressroute-faqs.md) pytaniach.

* Możesz [obejrzeć film](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit) przed rozpoczęciem, aby lepiej zrozumieć kroki.

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>Podłączanie sieci wirtualnej do obwodu — ta sama subskrypcja

> [!NOTE]
> Informacje o konfiguracji protokołu BGP nie będą wyświetlane, jeśli dostawca warstwy 3 skonfigurował elementy równorzędne. Jeśli obwód jest w stanie aprowizowanym, powinno być możliwe tworzenie połączeń.
>

### <a name="to-create-a-connection"></a>Aby utworzyć połączenie

1. Upewnij się, że obwód usługi ExpressRoute i prywatna komunikacja równorzędna platformy Azure zostały pomyślnie skonfigurowane. Postępuj zgodnie z instrukcjami w [aplikacji Utwórz obwód usługi ExpressRoute](expressroute-howto-circuit-arm.md) i [utwórz i zmodyfikuj komunikację równorzędną dla obwodu usługi ExpressRoute](expressroute-howto-routing-arm.md). Obwód usługi ExpressRoute powinien wyglądać następująco:

   [![Zrzut ekranu przedstawiający obwód usługi ExpressRoute](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png "Zobacz obwód")](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1-exp.png#lightbox)
2. Teraz można rozpocząć inicjowanie obsługi administracyjnej połączenia, aby połączyć bramę sieci wirtualnej z obwodem usługi ExpressRoute. Kliknij **pozycję Dodaj połączenie,** > **Add** aby otworzyć stronę **Dodaj połączenie,** a następnie skonfiguruj wartości.

   [![Dodawanie zrzutu ekranu połączenia](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png "Zrzut ekranu przedstawiający dodawanie połączenia")](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1-exp.png#lightbox)
3. Po pomyślnym skonfigurowaniu połączenia obiekt połączenia wyświetli informacje o połączeniu.

   ![Zrzut ekranu przedstawiający obiekt połączenia](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)


## <a name="connect-a-vnet-to-a-circuit---different-subscription"></a>Podłączanie sieci wirtualnej do obwodu — inna subskrypcja

Obwód usługi ExpressRoute można udostępniać w wielu subskrypcjach. Poniższy rysunek przedstawia prosty schemat działania udostępniania dla obwodów usługi ExpressRoute w wielu subskrypcjach.

![Łączność między subskrypcjami](./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png)

- Każda z mniejszych chmur w dużej chmurze jest używana do reprezentowania subskrypcji należących do różnych działów w organizacji.
- Każdy z działów w organizacji może używać własnej subskrypcji do wdrażania swoich usług, ale może udostępniać jeden obwód usługi ExpressRoute, aby połączyć się z powrotem do sieci lokalnej.
- Jeden dział (w tym przykładzie: IT) może być właścicielem obwodu usługi ExpressRoute. Inne subskrypcje w organizacji mogą korzystać z obwodu usługi ExpressRoute i autoryzacji skojarzonych z obwódem, w tym subskrypcji połączonych z innymi dzierżawami usługi Azure Active Directory i rejestracjami umowy Enterprise Agreement.

  > [!NOTE]
  > Opłaty za łączność i przepustowość dla dedykowanego obwodu zostaną zastosowane do właściciela obwodu usługi ExpressRoute. Wszystkie sieci wirtualne mają tę samą przepustowość.
  >
  >

### <a name="administration---about-circuit-owners-and-circuit-users"></a>Administracja — informacje o właścicielach obwodów i użytkownikach obwodów

"Właściciel obwodu" jest autoryzowanym użytkownikiem zasilania zasobu obwodu usługi ExpressRoute. Właściciel obwodu może tworzyć autoryzacje, które mogą być realizowane przez "użytkowników obwodu". Użytkownicy obwodów są właścicielami bram sieci wirtualnej, które nie są w ramach tej samej subskrypcji co obwód usługi ExpressRoute. Użytkownicy obwodów mogą realizować autoryzacje (jedna autoryzacja na sieć wirtualną).

Właściciel obwodu ma prawo do modyfikowania i cofania autoryzacji w dowolnym momencie. Cofnięcie autoryzacji powoduje usunięcie wszystkich połączeń łączy z subskrypcji, której dostęp został odwołany.

### <a name="circuit-owner-operations"></a>Operacje właściciela obwodu

**Aby utworzyć autoryzację połączenia**

Właściciel obwodu tworzy autoryzację. Powoduje to utworzenie klucza autoryzacji, który może być używany przez użytkownika obwodu do łączenia swoich bram sieci wirtualnej z obwodem usługi ExpressRoute. Autoryzacja jest ważna tylko dla jednego połączenia.

> [!NOTE]
> Każde połączenie wymaga osobnej autoryzacji.
>

1. Na stronie Usługi ExpressRoute kliknij pozycję **Autoryzacje,** a następnie wpisz **nazwę** autoryzacji i kliknij przycisk **Zapisz**.

   ![Autoryzacje](./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png)
2. Po zapisaniu konfiguracji skopiuj **identyfikator zasobu** i **klucz autoryzacji**.

   ![Klucz autoryzacji](./media/expressroute-howto-linkvnet-portal-resource-manager/authkey.png)

**Aby usunąć autoryzację połączenia**

Połączenie można usunąć, wybierając ikonę **Usuń** na stronie połączenia.

### <a name="circuit-user-operations"></a>Operacje użytkownika obwodu

Użytkownik obwodu potrzebuje identyfikatora zasobu i klucza autoryzacji od właściciela obwodu.

**Aby zrealizować autoryzację połączenia**

1. Kliknij **przycisk +Nowy.**

   ![Kliknij pozycję Nowy](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection1.png)
2. Wyszukaj **hasło "Połączenie"** w portalu Marketplace, wybierz je i kliknij pozycję **Utwórz**.

   ![Wyszukiwanie połączenia](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection2.png)
3. Upewnij się, że **typ połączenia** jest ustawiony na "ExpressRoute".
4. Wprowadź szczegóły, a następnie kliknij przycisk **OK** na stronie Podstawy.

   ![Strona Podstawy](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection3.png)
5. Na stronie **Ustawienia** wybierz **bramę sieci wirtualnej** i zaznacz pole wyboru **Zrealizuj autoryzację.**
6. Wprowadź **klucz autoryzacji** i **identyfikator URI obwodu równorzędnego** i nadaj nazwę połączenia. Kliknij przycisk **OK**. Identyfikator **URI obwodu równorzędnego** jest identyfikatorem zasobu obwodu usługi ExpressRoute (który można znaleźć w okienku Ustawień właściwości obwodu usługi ExpressRoute).

   ![Strona Ustawienia](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection4.png)
7. Przejrzyj informacje na stronie **Podsumowanie** i kliknij przycisk **OK**.

**Aby zwolnić autoryzację połączenia**

Autoryzację można zwolnić, usuwając połączenie łączące obwód usługi ExpressRoute z siecią wirtualną.

## <a name="delete-a-connection-to-unlink-a-vnet"></a>Usuwanie połączenia w celu odłączenia sieci wirtualnej

Połączenie można usunąć i odłączyć sieci wirtualnej od obwodu usługi ExpressRoute, wybierając ikonę **Usuń** na stronie połączenia.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat usługi ExpressRoute, zobacz [często zadawane pytania dotyczące usługi ExpressRoute](expressroute-faqs.md).
