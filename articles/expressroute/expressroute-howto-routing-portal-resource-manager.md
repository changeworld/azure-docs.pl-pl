---
title: 'Azure ExpressRoute: Konfigurowanie komunikacji równorzędnej'
description: W tym artykule opisano kroki tworzenia i inicjowania obsługi ExpressRoute prywatnych i komunikacji równorzędnej firmy Microsoft. W tym artykule przedstawiono również sposób sprawdzania stanu, aktualizacji lub usuwania komunikacji równorzędnej dla obwodu.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: mialdrid
ms.openlocfilehash: 18d2db18e9880028c60b4b545c3628f4a9cb4703
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79264793"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit"></a>Utworzyć i zmodyfikować komunikację równorzędną dla obwodu usługi ExpressRoute

W tym artykule opisano tworzenie i zarządzanie konfiguracją routingu Azure Resource Manager dla obwodu ExpressRoute (ARM) przy użyciu Azure Portal. Możesz również sprawdzić stan, update lub delete i anulowanie aprowizacji komunikacji równorzędnej dla obwodu usługi ExpressRoute. Jeśli chcesz użyć innej metody do pracy z Twojego obwodu, wybierz artykułu z następującej listy:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-routing-cli.md)
> * [Publiczna Komunikacja równorzędna](about-public-peering.md)
> * [Wideo — prywatna Komunikacja równorzędna](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Wideo — Komunikacja równorzędna firmy Microsoft](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-routing-classic.md)
> 

Można skonfigurować prywatną komunikację równorzędną i komunikację równorzędną firmy Microsoft dla obwodu usługi ExpressRoute (publiczna Komunikacja równorzędna Azure jest przestarzała dla nowych obwodów). Komunikację równorzędną można skonfigurować w dowolnej wybranej kolejności. Musisz jednak pamiętać, aby kończyć konfiguracje poszczególnych komunikacji równorzędnych pojedynczo. Aby uzyskać więcej informacji o domenach routingu i komunikacji równorzędnej, zobacz [ExpressRoute Routing domen](expressroute-circuit-peerings.md). Aby uzyskać informacje na temat publicznej komunikacji równorzędnej, zobacz [ExpressRoute publicznej komunikacji równorzędnej](about-public-peering.md).

## <a name="configuration-prerequisites"></a>Wymagania wstępne dotyczące konfiguracji

* Pamiętaj, aby przed rozpoczęciem konfiguracji przejrzeć strony z [wymaganiami wstępnymi](expressroute-prerequisites.md), [wymaganiami routingu](expressroute-routing.md) oraz [przepływami pracy](expressroute-workflows.md).
* Musisz mieć aktywny obwód usługi ExpressRoute. Zanim przejdziesz dalej, postępuj zgodnie z instrukcjami, aby [utworzyć obwód usługi ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md), który powinien zostać włączony przez dostawcę połączenia. Aby można było skonfigurować komunikację równorzędną, obwód ExpressRoute musi być w stanie aprowizacji i włączony. 
* Jeśli planujesz użycie skrótu klucza wspólnego/MD5, pamiętaj, aby użyć go na obu stronach tunelu i ograniczyć liczbę znaków alfanumerycznych do maksymalnie 25. Znaki specjalne nie są obsługiwane. 

Te instrukcje dotyczą tylko obwodów utworzonych przy pomocy dostawców oferujących usługi łączności warstwy 2. Jeśli używasz dostawcy usług, który oferuje zarządzane w warstwie 3 usługi (zwykle IPVPN, np. MPLS), dostawca połączenia skonfiguruje i zarządza routing. 

> [!IMPORTANT]
> Obecnie nie anonsujemy komunikacji równorzędnej skonfigurowanej przez dostawców usług w portalu zarządzania usługami. Pracujemy nad tym, by wkrótce włączyć tę funkcję. Skontaktuj się z dostawcą usług, przed skonfigurowaniem komunikacji równorzędnej BGP.
> 
> 

## <a name="msft"></a>Komunikacja równorzędna firmy Microsoft

Ta sekcja ułatwia tworzenie, pobieranie, aktualizacji i usuwania konfiguracji komunikacji równorzędnej firmy Microsoft dla obwodu usługi ExpressRoute.

> [!IMPORTANT]
> Obwodów usługi ExpressRoute, które zostały skonfigurowane przed 1 sierpnia 2017 r. komunikacji równorzędnej firmy Microsoft będzie miał wszystkie prefiksy usługi anonsowanego za pośrednictwem komunikacji równorzędnej firmy Microsoft, nawet jeśli nie zdefiniowano filtry tras. Komunikacja równorzędna firmy Microsoft obwodów usługi ExpressRoute, skonfigurowanych po 1 sierpnia 2017 r. nie będzie miał wszelkie prefiksy anonsowane do czasu podłączenia filtru tras do obwodu. Aby uzyskać więcej informacji, zobacz [Konfigurowanie filtru tras dla komunikacji równorzędnej firmy Microsoft](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Aby utworzyć komunikację równorzędną Microsoft

1. Skonfiguruj obwód usługi ExpressRoute. Sprawdź **stan dostawcy** , aby upewnić się, że obwód jest w pełni obsługiwany przez dostawcę łączności przed dalszym kontynuowaniem.

   Jeśli dostawca połączenia oferuje zarządzane usługi warstwy 3, możesz poprosić dostawcą połączenia, aby umożliwić firmie Microsoft komunikacji równorzędnej. W takim przypadku nie trzeba postępować zgodnie z instrukcjami podanymi w następnych sekcjach. Jeśli jednak dostawca łączności nie zarządza usługą dla Ciebie, po utworzeniu obwodu należy wykonać poniższe kroki.

   **Obwód — stan dostawcy: nie zainicjowano obsługi administracyjnej**

    [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m.png "Provider status: Not provisioned")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m-lightbox.png#lightbox)

   **Obwód — stan dostawcy: zainicjowano**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m.png "Provider status = Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m-lightbox.png#lightbox)
2. Skonfiguruj komunikację równorzędną Microsoft dla obwodu. Zanim przejdziesz dalej, upewnij się, że masz poniższe informacje.

   * Podsieć /30 dla połączenia podstawowego. Musi to być prawidłowy publiczny prefiks IPv4, którego jesteś właścicielem, zarejestrowany w RIR/IRR. Z tej podsieci spowoduje przypisanie pierwszego adresu IP niemożliwe do routera, jak firma Microsoft używa drugiego IP niemożliwe do jego routera.
   * Podsieć /30 dla połączenia dodatkowego. Musi to być prawidłowy publiczny prefiks IPv4, którego jesteś właścicielem, zarejestrowany w RIR/IRR. Z tej podsieci spowoduje przypisanie pierwszego adresu IP niemożliwe do routera, jak firma Microsoft używa drugiego IP niemożliwe do jego routera.
   * Prawidłowy identyfikator sieci VLAN do ustanowienia tej komunikacji równorzędnej jest włączony. Upewnij się, że żadna inna komunikacja równorzędna w obwodzie nie używa tego samego identyfikatora VLAN. Łączy podstawowe i pomocnicze należy używać tego samego identyfikatora sieci VLAN.
   * Numer AS do komunikacji równorzędnej. Możesz używać 2-bajtowych i 4-bajtowych numerów AS.
   * Anonsowane prefiksy: musisz podać listę wszystkich prefiksów, które planujesz anonsować za pośrednictwem sesji BGP. Akceptowane są tylko prefiksy publicznych adresów IP. Jeśli zamierzasz wysłać zestaw prefiksów, możesz wysłać listę rozdzielonych przecinkami. Prefiksy te muszą być zarejestrowane na Ciebie w RIR/IRR.
   * **Opcjonalne-** Numer ASN klienta: w przypadku anonsowania prefiksów, które nie są zarejestrowane w komunikacji równorzędnej jako liczba, można określić liczbę AS, do której są zarejestrowani.
   * Nazwa rejestru routingu: możesz określić RIR/IRR, względem którego rejestrowany jest numer AS i prefiksy.
   * **Opcjonalne-** Skrót MD5, jeśli zdecydujesz się na użycie jednego z nich.
3. Możesz wybrać komunikację równorzędną, którą chcesz skonfigurować, jak pokazano w poniższym przykładzie. Zaznacz wiersz dotyczący komunikacji równorzędnej firmy Microsoft.

   [![Wybierz wiersz komunikacji równorzędnej firmy Microsoft](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m.png "Wybierz wiersz komunikacji równorzędnej firmy Microsoft")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m-lightbox.png#lightbox)
4. Skonfiguruj komunikację równorzędną firmy Microsoft. **Zapisz** konfigurację po określeniu wszystkich parametrów. Na poniższej ilustracji przedstawiono przykładową konfigurację:

   ![Konfigurowanie komunikacji równorzędnej firmy Microsoft](./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png)

> [!IMPORTANT]
> Firma Microsoft sprawdza, czy określone "anonsowane publiczne prefiksy" i "peer ASN" (lub "ASN klienta") są przypisane do Ciebie w rejestrze routingu internetowego. Jeśli otrzymujesz publiczne prefiksy z innej jednostki i jeśli przypisanie nie zostanie zarejestrowane w rejestrze routingu, automatyczne sprawdzanie poprawności nie zostanie ukończone i będzie wymagać ręcznej walidacji. Jeśli automatyczne sprawdzanie poprawności nie powiedzie się, zostanie wyświetlony komunikat "weryfikacja nie jest konieczna". 
>
> Jeśli zobaczysz komunikat "wymagana Walidacja", Zbierz dokumenty, które pokazują, że publiczne prefiksy są przypisane do organizacji przez jednostkę wymienioną jako właściciel prefiksów w rejestrze routingu i prześlij te dokumenty w celu ręcznej weryfikacji przez Otwieranie biletu pomocy technicznej, jak pokazano poniżej. 
>

   Jeśli obwód jest w stanie "wymagana Walidacja", musisz otworzyć bilet pomocy technicznej, aby wyświetlić dowód własności prefiksów dla naszego zespołu pomocy technicznej. Można otworzyć bilet pomocy technicznej bezpośrednio z poziomu portalu, jak pokazano w poniższym przykładzie:

   ![Wymagana weryfikacja — bilet pomocy technicznej](./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png)

5. Po pomyślnym zaakceptowaniu konfiguracji zobaczysz coś podobnego do poniższej ilustracji:

   ![Stan komunikacji równorzędnej: skonfigurowany](./media/expressroute-howto-routing-portal-resource-manager/configured-m.png "Stan komunikacji równorzędnej: skonfigurowano")]

### <a name="getmsft"></a>Aby wyświetlić szczegóły dotyczące komunikacji równorzędnej firmy Microsoft

Możesz wyświetlić właściwości komunikacji równorzędnej firmy Microsoft, wybierając wiersz dla komunikacji równorzędnej.

[![Wyświetl właściwości komunikacji równorzędnej firmy Microsoft](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png "Wyświetl właściwości")](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m-lightbox.png#lightbox)
### <a name="updatemsft"></a>Aby zaktualizować konfigurację komunikacji równorzędnej firmy Microsoft

Możesz wybrać wiersz dla komunikacji równorzędnej, którą chcesz zmodyfikować, a następnie zmodyfikować właściwości komunikacji równorzędnej i zapisać modyfikacje.

![Wybierz wiersz komunikacji równorzędnej](./media/expressroute-howto-routing-portal-resource-manager/update-peering-m.png)

### <a name="deletemsft"></a>Aby usunąć komunikację równorzędną firmy Microsoft

Aby usunąć konfigurację komunikacji równorzędnej, kliknij ikonę Usuń, jak pokazano na poniższej ilustracji:

![Usuwanie komunikacji równorzędnej](./media/expressroute-howto-routing-portal-resource-manager/delete-peering-m.png)

## <a name="private"></a>Prywatna Komunikacja równorzędna Azure

Ta sekcja ułatwia tworzenie, pobieranie, aktualizowanie i usuwanie prywatnej komunikacji równorzędnej konfiguracji Azure dla obwodu usługi ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Aby utworzyć prywatną komunikację równorzędną

1. Skonfiguruj obwód usługi ExpressRoute. Zanim przejdziesz dalej, upewnij się, że obwód jest w całości obsługiwany przez dostawcę połączenia. 

   Jeśli dostawca połączenia oferuje zarządzane usługi warstwy 3, możesz poprosić dostawcą połączenia, aby umożliwić prywatną komunikację równorzędną Azure dla Ciebie. W takim przypadku nie trzeba postępować zgodnie z instrukcjami podanymi w następnych sekcjach. Jeśli jednak dostawca połączenia nie zarządza usługą routingu, po utworzeniu obwodu przejdź do następnych kroków.

   **Obwód — stan dostawcy: nie zainicjowano obsługi administracyjnej**

   [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p.png "Provider status = Not Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p-lightbox.png#lightbox)

   **Obwód — stan dostawcy: zainicjowano**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p.png "Provider Status = Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p-lightbox.png#lightbox)

2. Skonfiguruj prywatną komunikację równorzędną Azure dla obwodu. Zanim przejdziesz do następnych kroków, upewnij się, czy masz następujące elementy:

   * Podsieć /30 dla połączenia podstawowego. Podsieci nie może być częścią żadnej przestrzeni adresowej zarezerwowanej dla sieci wirtualnych. Z tej podsieci spowoduje przypisanie pierwszego adresu IP niemożliwe do routera, jak firma Microsoft używa drugiego IP niemożliwe do jego routera.
   * Podsieć /30 dla połączenia dodatkowego. Podsieci nie może być częścią żadnej przestrzeni adresowej zarezerwowanej dla sieci wirtualnych. Z tej podsieci spowoduje przypisanie pierwszego adresu IP niemożliwe do routera, jak firma Microsoft używa drugiego IP niemożliwe do jego routera.
   * Prawidłowy identyfikator sieci VLAN do ustanowienia tej komunikacji równorzędnej jest włączony. Upewnij się, że żadna inna komunikacja równorzędna w obwodzie nie używa tego samego identyfikatora VLAN. Łączy podstawowe i pomocnicze należy używać tego samego identyfikatora sieci VLAN.
   * Numer AS do komunikacji równorzędnej. Możesz używać 2-bajtowych i 4-bajtowych numerów AS. Możesz użyć numeru prywatnego jako dla tej komunikacji równorzędnej z wyjątkiem liczby od 65515 do 65520 włącznie.
   * Trasy należy anonsować od lokalnego routera brzegowego do platformy Azure za pośrednictwem protokołu BGP podczas konfigurowania prywatnej komunikacji równorzędnej.
   * **Opcjonalne-** Skrót MD5, jeśli zdecydujesz się na użycie jednego z nich.
3. Wybierz wiersz prywatnej komunikacji równorzędnej Azure, jak pokazano w następującym przykładzie:

   [![Wybierz wiersz prywatnej komunikacji równorzędnej](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p.png "Wybierz wiersz prywatnej komunikacji równorzędnej")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p-lightbox.png#lightbox)
4. Skonfiguruj prywatną komunikację równorzędną. **Zapisz** konfigurację po określeniu wszystkich parametrów.

   ![Skonfiguruj prywatną komunikację równorzędną](./media/expressroute-howto-routing-portal-resource-manager/configuration-p.png)
5. Po pomyślnym zaakceptowaniu konfiguracji, zostanie wyświetlony podobny do poniższego przykładu:

   ![Zapisano prywatną komunikację równorzędną](./media/expressroute-howto-routing-portal-resource-manager/save-p.png)

### <a name="getprivate"></a>Aby wyświetlić szczegóły prywatnej komunikacji równorzędnej Azure

Możesz wyświetlić właściwości prywatnej komunikacji równorzędnej Azure, wybierając ją.

[![Wyświetl właściwości prywatnych komunikacji równorzędnej](./media/expressroute-howto-routing-portal-resource-manager/view-p.png "Wyświetl właściwości prywatnych komunikacji równorzędnej")](./media/expressroute-howto-routing-portal-resource-manager/view-p-lightbox.png#lightbox)

### <a name="updateprivate"></a>Aby zaktualizować konfigurację prywatnej komunikacji równorzędnej Azure

Można wybrać wiersz dotyczący komunikacji równorzędnej i zmodyfikować jej właściwości. Po uaktualnieniu Zapisz zmiany.

![Aktualizuj prywatnej komunikacji równorzędnej](./media/expressroute-howto-routing-portal-resource-manager/update-peering-p.png)

### <a name="deleteprivate"></a>Aby usunąć prywatną komunikację równorzędną Azure

Możesz usunąć konfigurację komunikacji równorzędnej, wybierając ikonę usuwania, jak pokazano na poniższej ilustracji:

> [!WARNING]
> Należy się upewnić, czy wszystkie połączenia zasięgu globalnym usługi ExpressRoute i sieci wirtualne zostały usunięte przed uruchomieniem tego przykładu. 
> 
> 

![Usuwanie prywatnej komunikacji równorzędnej](./media/expressroute-howto-routing-portal-resource-manager/delete-p.png)


## <a name="next-steps"></a>Następne kroki

Następny krok, [łączenie sieci wirtualnej z obwodem ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
* Więcej informacji na temat przepływów pracy usługi ExpressRoute znajduje się w artykule [ExpressRoute workflows](expressroute-workflows.md) (Przepływy pracy usługi ExpressRoute).
* Aby uzyskać więcej informacji o komunikacji równorzędnej obwodu, zobacz artykuł [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (Obwody i domeny routingu usługi ExpressRoute).
* Więcej informacji na temat pracy z sieciami wirtualnymi znajduje się w artykule [Virtual network overview](../virtual-network/virtual-networks-overview.md) (Omówienie sieci wirtualnych).
