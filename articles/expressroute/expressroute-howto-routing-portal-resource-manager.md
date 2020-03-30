---
title: 'Azure ExpressRoute: Konfigurowanie komunikacji równorzędnej'
description: W tym artykule dokumentuje kroki tworzenia i inicjowania obsługi administracyjnej usługi ExpressRoute private i microsoft peering. W tym artykule pokazano również, jak sprawdzić stan, zaktualizować lub usunąć komunikacji równorzędnej dla obwodu.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: mialdrid
ms.openlocfilehash: 18d2db18e9880028c60b4b545c3628f4a9cb4703
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264793"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit"></a>Tworzenie i modyfikowanie komunikacji równorzędnej dla obwodu usługi ExpressRoute

Ten artykuł ułatwia tworzenie konfiguracji routingu i zarządzanie nią dla obwodu usługi Azure Resource Manager (ARM) ExpressRoute przy użyciu witryny Azure portal. Można również sprawdzić stan, zaktualizować lub usunąć i anulować komunikację równorzędną dla obwodu usługi ExpressRoute. Jeśli chcesz użyć innej metody do pracy z obwodem, wybierz artykuł z następującej listy:

> [!div class="op_single_selector"]
> * [Portal Azure](expressroute-howto-routing-portal-resource-manager.md)
> * [Powershell](expressroute-howto-routing-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-routing-cli.md)
> * [Komunikacja równorzędna](about-public-peering.md)
> * [Wideo - Prywatne peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Wideo — komunikacja równorzędna firmy Microsoft](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-routing-classic.md)
> 

Można skonfigurować prywatną komunikację równorzędną i komunikację równorzędną firmy Microsoft dla obwodu usługi ExpressRoute (publiczne równorzędne komunikacji równorzędnej platformy Azure jest przestarzałe dla nowych obwodów). Komunikacji równorzędnej można skonfigurować w dowolnej kolejności. Musisz jednak pamiętać, aby kończyć konfiguracje poszczególnych komunikacji równorzędnych pojedynczo. Aby uzyskać więcej informacji na temat routingu domen i komunikacji równorzędnej, zobacz [Domeny routingu usługi ExpressRoute](expressroute-circuit-peerings.md). Aby uzyskać informacje na temat komunikacji równorzędnej publicznej, zobacz [Komunikacja równorzędna usługi ExpressRoute](about-public-peering.md).

## <a name="configuration-prerequisites"></a>Wymagania wstępne dotyczące konfiguracji

* Pamiętaj, aby przed rozpoczęciem konfiguracji przejrzeć strony z [wymaganiami wstępnymi](expressroute-prerequisites.md), [wymaganiami routingu](expressroute-routing.md) oraz [przepływami pracy](expressroute-workflows.md).
* Musisz mieć aktywny obwód usługi ExpressRoute. Zanim przejdziesz dalej, postępuj zgodnie z instrukcjami, aby [utworzyć obwód usługi ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md), który powinien zostać włączony przez dostawcę połączenia. Aby skonfigurować komunikacji równorzędnej, obwód usługi ExpressRoute musi być w stanie aprowizowanym i włączonym. 
* Jeśli planujesz użyć udostępnionego skrótu klucza/MD5, należy użyć tego po obu stronach tunelu i ograniczyć liczbę znaków alfanumeryczne do maksymalnie 25. Znaki specjalne nie są obsługiwane. 

Te instrukcje dotyczą tylko obwodów utworzonych przy pomocy dostawców oferujących usługi łączności warstwy 2. Jeśli korzystasz z dostawcy usług, który oferuje zarządzane usługi warstwy 3 (zazwyczaj ipvpn, takie jak MPLS), dostawca łączności konfiguruje i zarządza routingiem. 

> [!IMPORTANT]
> Obecnie nie anonsujemy komunikacji równorzędnej skonfigurowanej przez dostawców usług w portalu zarządzania usługami. Pracujemy nad tym, by wkrótce włączyć tę funkcję. Przed skonfigurowaniem komunikacji równorzędnej protokołu BGP skontaktuj się z usługodawcą.
> 
> 

## <a name="microsoft-peering"></a><a name="msft"></a>Komunikacja równorzędna firmy Microsoft

Ta sekcja ułatwia tworzenie, ujmowanie, aktualizowanie i usuwanie konfiguracji komunikacji równorzędnej firmy Microsoft dla obwodu usługi ExpressRoute.

> [!IMPORTANT]
> Komunikacja równorzędna obwodów usługi ExpressRoute skonfigurowana przed 1 sierpnia 2017 r. będzie miała wszystkie prefiksy usługi anonsowane za pośrednictwem komunikacji równorzędnej firmy Microsoft, nawet jeśli filtry tras nie są zdefiniowane. Komunikacja równorzędna obwodów usługi ExpressRoute skonfigurowana 1 sierpnia 2017 r. lub później nie będzie miała żadnych prefiksów anonsowanych, dopóki filtr trasy nie zostanie dołączony do obwodu. Aby uzyskać więcej informacji, zobacz [Konfigurowanie filtru marszruty dla komunikacji równorzędnej firmy Microsoft](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Aby utworzyć komunikację równorzędną Microsoft

1. Skonfiguruj obwód usługi ExpressRoute. Sprawdź **stan dostawcy,** aby upewnić się, że obwód jest w pełni aprowizować przez dostawcę łączności przed kontynuowaniem.

   Jeśli dostawca łączności oferuje zarządzane usługi warstwy 3, możesz poprosić dostawcę łączności o włączenie komunikacji równorzędnej firmy Microsoft. W takim przypadku nie trzeba postępować zgodnie z instrukcjami podanymi w następnych sekcjach. Jeśli jednak dostawca łączności nie zarządza routingiem, po utworzeniu obwodu wykonaj następujące kroki.

   **Obwód — stan dostawcy: Nie aprowizować**

    [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m.png "Provider status: Not provisioned")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m-lightbox.png#lightbox)

   **Obwód — stan dostawcy: Aprowizowana**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m.png "Provider status = Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m-lightbox.png#lightbox)
2. Skonfiguruj komunikację równorzędną Microsoft dla obwodu. Zanim przejdziesz dalej, upewnij się, że masz poniższe informacje.

   * Podsieć /30 dla połączenia podstawowego. Musi to być prawidłowy publiczny prefiks IPv4, którego jesteś właścicielem, zarejestrowany w RIR/IRR. Z tej podsieci zostanie przypisany pierwszy użyteczny adres IP do routera, ponieważ firma Microsoft używa drugiego użytecznego adresu IP dla swojego routera.
   * Podsieć /30 dla połączenia dodatkowego. Musi to być prawidłowy publiczny prefiks IPv4, którego jesteś właścicielem, zarejestrowany w RIR/IRR. Z tej podsieci zostanie przypisany pierwszy użyteczny adres IP do routera, ponieważ firma Microsoft używa drugiego użytecznego adresu IP dla swojego routera.
   * Prawidłowy identyfikator sieci VLAN do ustanowienia tej komunikacji równorzędnej jest włączony. Upewnij się, że żadna inna komunikacja równorzędna w obwodzie nie używa tego samego identyfikatora VLAN. Zarówno dla podstawowych i pomocniczych łączy należy użyć tego samego identyfikatora sieci VLAN.
   * Numer AS do komunikacji równorzędnej. Możesz używać 2-bajtowych i 4-bajtowych numerów AS.
   * Anonsowane prefiksy: musisz podać listę wszystkich prefiksów, które planujesz anonsować za pośrednictwem sesji BGP. Akceptowane są tylko prefiksy publicznych adresów IP. Jeśli planujesz wysłać zestaw prefiksów, możesz wysłać listę oddzieloną przecinkami. Prefiksy te muszą być zarejestrowane na Ciebie w RIR/IRR.
   * **Opcjonalnie -** Asn klienta: Jeśli reklamujesz prefiksy, które nie są zarejestrowane na numer AS komunikacji równorzędnej, możesz określić numer AS, na który są zarejestrowane.
   * Nazwa rejestru routingu: możesz określić RIR/IRR, względem którego rejestrowany jest numer AS i prefiksy.
   * **Opcjonalnie -** Skrót MD5, jeśli zdecydujesz się go użyć.
3. Można wybrać komunikację równorzędnej, którą chcesz skonfigurować, jak pokazano w poniższym przykładzie. Zaznacz wiersz dotyczący komunikacji równorzędnej firmy Microsoft.

   [![Wybieranie wiersza komunikacji równorzędnej firmy Microsoft](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m.png "Wybieranie wiersza komunikacji równorzędnej firmy Microsoft")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m-lightbox.png#lightbox)
4. Skonfiguruj komunikację równorzędną firmy Microsoft. **Zapisz** konfigurację po określeniu wszystkich parametrów. Na poniższej ilustracji przedstawiono przykładową konfigurację:

   ![Konfigurowanie komunikacji równorzędnej firmy Microsoft](./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png)

> [!IMPORTANT]
> Firma Microsoft sprawdza, czy określone "Anonsowane prefiksy publiczne" i "Peer ASN" (lub "Asn klienta") są przypisane do Ciebie w rejestrze routingu internetowego. Jeśli otrzymujesz prefiksy publiczne z innej jednostki i jeśli przypisanie nie jest rejestrowane w rejestrze routingu, automatyczne sprawdzanie poprawności nie zostanie zakończone i będzie wymagać ręcznego sprawdzania poprawności. Jeśli automatyczne sprawdzanie poprawności nie powiedzie się, zostanie wyświetlony komunikat "Sprawdzanie poprawności potrzebne". 
>
> Jeśli zostanie wyświetlony komunikat "Sprawdzanie poprawności potrzebne", zebrać dokumenty, które pokazują prefiksy publiczne są przypisane do organizacji przez jednostkę, która jest wymieniona jako właściciel prefiksów w rejestrze routingu i przesłać te dokumenty do ręcznego sprawdzania poprawności przez otwierając bilet wsparcia, jak pokazano poniżej. 
>

   Jeśli twój obwód osiągnie stan "Sprawdzanie poprawności potrzebne", należy otworzyć bilet pomocy technicznej, aby pokazać dowód własności prefiksów do naszego zespołu pomocy technicznej. Bilet pomocy technicznej można otworzyć bezpośrednio z portalu, jak pokazano w poniższym przykładzie:

   ![Wymagana walidacja - bilet pomocy technicznej](./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png)

5. Po pomyślnym zaakceptowaniu konfiguracji zobaczysz coś podobnego do następującego obrazu:

   ![Stan komunikacji równorzędnej: Skonfigurowano](./media/expressroute-howto-routing-portal-resource-manager/configured-m.png "Stan komunikacji równorzędnej: skonfigurowano")]

### <a name="to-view-microsoft-peering-details"></a><a name="getmsft"></a>Aby wyświetlić szczegóły dotyczące komunikacji równorzędnej firmy Microsoft

Można wyświetlić właściwości komunikacji równorzędnej firmy Microsoft, wybierając wiersz dla komunikacji równorzędnej.

[![Wyświetlanie właściwości komunikacji równorzędnej firmy Microsoft](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png "Wyświetl właściwości")](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m-lightbox.png#lightbox)
### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Aby zaktualizować konfigurację komunikacji równorzędnej firmy Microsoft

Można wybrać wiersz dla komunikacji równorzędnej, który chcesz zmodyfikować, a następnie zmodyfikować właściwości komunikacji równorzędnej i zapisać modyfikacje.

![Wybieranie wiersza komunikacji równorzędnej](./media/expressroute-howto-routing-portal-resource-manager/update-peering-m.png)

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Aby usunąć komunikację równorzędną firmy Microsoft

Konfigurację komunikacji równorzędnej można usunąć, klikając ikonę usuwania, jak pokazano na poniższej ilustracji:

![Usuwanie komunikacji równorzędnej](./media/expressroute-howto-routing-portal-resource-manager/delete-peering-m.png)

## <a name="azure-private-peering"></a><a name="private"></a>Prywatna komunikacja równorzędna Azure

Ta sekcja ułatwia tworzenie, ujmowanie, aktualizowanie i usuwanie prywatnej konfiguracji komunikacji równorzędnej platformy Azure dla obwodu usługi ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Aby utworzyć prywatną komunikację równorzędną

1. Skonfiguruj obwód usługi ExpressRoute. Zanim przejdziesz dalej, upewnij się, że obwód jest w całości obsługiwany przez dostawcę połączenia. 

   Jeśli dostawca łączności oferuje zarządzane usługi warstwy 3, możesz poprosić dostawcę łączności o włączenie prywatnej komunikacji równorzędnej platformy Azure. W takim przypadku nie trzeba postępować zgodnie z instrukcjami podanymi w następnych sekcjach. Jeśli jednak dostawca łączności nie zarządza routingiem, po utworzeniu obwodu należy wykonać kolejne kroki.

   **Obwód — stan dostawcy: Nie aprowizować**

   [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p.png "Provider status = Not Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p-lightbox.png#lightbox)

   **Obwód — stan dostawcy: Aprowizowana**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p.png "Provider Status = Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p-lightbox.png#lightbox)

2. Skonfiguruj prywatną komunikację równorzędną Azure dla obwodu. Zanim przejdziesz do następnych kroków, upewnij się, czy masz następujące elementy:

   * Podsieć /30 dla połączenia podstawowego. Podsieć nie może być częścią żadnej przestrzeni adresowej zarezerwowanej dla sieci wirtualnych. Z tej podsieci zostanie przypisany pierwszy użyteczny adres IP do routera, ponieważ firma Microsoft używa drugiego użytecznego adresu IP dla swojego routera.
   * Podsieć /30 dla połączenia dodatkowego. Podsieć nie może być częścią żadnej przestrzeni adresowej zarezerwowanej dla sieci wirtualnych. Z tej podsieci zostanie przypisany pierwszy użyteczny adres IP do routera, ponieważ firma Microsoft używa drugiego użytecznego adresu IP dla swojego routera.
   * Prawidłowy identyfikator sieci VLAN do ustanowienia tej komunikacji równorzędnej jest włączony. Upewnij się, że żadna inna komunikacja równorzędna w obwodzie nie używa tego samego identyfikatora VLAN. Zarówno dla podstawowych i pomocniczych łączy należy użyć tego samego identyfikatora sieci VLAN.
   * Numer AS do komunikacji równorzędnej. Możesz używać 2-bajtowych i 4-bajtowych numerów AS. Można użyć prywatnego numeru AS dla tej komunikacji równorzędnej, z wyjątkiem numeru od 65515 do 65520 włącznie.
   * Podczas konfigurowania prywatnej komunikacji równorzędnej należy anonsować trasy z lokalnego routera usługi Edge do platformy Azure za pośrednictwem protokołu BGP.
   * **Opcjonalnie -** Skrót MD5, jeśli zdecydujesz się go użyć.
3. Wybierz prywatny wiersz komunikacji równorzędnej platformy Azure, jak pokazano w poniższym przykładzie:

   [![Wybieranie prywatnego wiersza komunikacji równorzędnej](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p.png "Wybieranie prywatnego wiersza komunikacji równorzędnej")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p-lightbox.png#lightbox)
4. Skonfiguruj prywatną komunikację równorzędną. **Zapisz** konfigurację po określeniu wszystkich parametrów.

   ![konfigurowanie prywatnej komunikacji równorzędnej](./media/expressroute-howto-routing-portal-resource-manager/configuration-p.png)
5. Po pomyślnym zaakceptowaniu konfiguracji zobaczysz coś podobnego do następującego przykładu:

   ![zapisane prywatne peering](./media/expressroute-howto-routing-portal-resource-manager/save-p.png)

### <a name="to-view-azure-private-peering-details"></a><a name="getprivate"></a>Aby wyświetlić szczegóły dotyczące prywatnej komunikacji równorzędnej Azure

Możesz wyświetlić właściwości prywatnej komunikacji równorzędnej Azure, wybierając ją.

[![Wyświetlanie prywatnych właściwości komunikacji równorzędnej](./media/expressroute-howto-routing-portal-resource-manager/view-p.png "Wyświetlanie prywatnych właściwości komunikacji równorzędnej")](./media/expressroute-howto-routing-portal-resource-manager/view-p-lightbox.png#lightbox)

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>Aby zaktualizować konfigurację prywatnej komunikacji równorzędnej Azure

Można wybrać wiersz dotyczący komunikacji równorzędnej i zmodyfikować jej właściwości. Po aktualizacji zapisz zmiany.

![aktualizowanie prywatnej komunikacji równorzędnej](./media/expressroute-howto-routing-portal-resource-manager/update-peering-p.png)

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Aby usunąć prywatną komunikację równorzędną Azure

Konfigurację komunikacji równorzędnej można usunąć, wybierając ikonę usuwania, jak pokazano na poniższej ilustracji:

> [!WARNING]
> Przed uruchomieniem tego przykładu należy upewnić się, że wszystkie sieci wirtualne i połączenia usługi ExpressRoute Global Reach zostaną usunięte. 
> 
> 

![usuwanie prywatnej komunikacji równorzędnej](./media/expressroute-howto-routing-portal-resource-manager/delete-p.png)


## <a name="next-steps"></a>Następne kroki

Następny [krok: Łączenie sieci wirtualnej z obwodem usługi ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
* Więcej informacji na temat przepływów pracy usługi ExpressRoute znajduje się w artykule [ExpressRoute workflows](expressroute-workflows.md) (Przepływy pracy usługi ExpressRoute).
* Aby uzyskać więcej informacji o komunikacji równorzędnej obwodu, zobacz artykuł [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (Obwody i domeny routingu usługi ExpressRoute).
* Więcej informacji na temat pracy z sieciami wirtualnymi znajduje się w artykule [Virtual network overview](../virtual-network/virtual-networks-overview.md) (Omówienie sieci wirtualnych).
