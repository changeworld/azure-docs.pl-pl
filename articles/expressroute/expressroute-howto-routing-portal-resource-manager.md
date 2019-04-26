---
title: 'Skonfiguruj komunikację równorzędną dla obwodu — ExpressRoute: Azure | Microsoft Docs'
description: Ten artykuł zawiera instrukcje tworzenia i inicjowania obsługi komunikacji równorzędnej prywatnej, publicznej i firmy Microsoft obwodu usługi ExpressRoute. W tym artykule opisano również, jak aktualizować i usuwać komunikację równoległą dla obwodu oraz sprawdzać jej stan.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 6d1ce56ef66d224b89f49a00c2883ebbf22a5745
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60365064"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit"></a>Utworzyć i zmodyfikować komunikację równorzędną dla obwodu usługi ExpressRoute

Ten artykuł ułatwia tworzenie i zarządzanie nimi konfiguracji routingu dla obwodu usługi ExpressRoute w modelu wdrażania usługi Resource Manager przy użyciu witryny Azure portal. Możesz również sprawdzić stan, update lub delete i anulowanie aprowizacji komunikacji równorzędnej dla obwodu usługi ExpressRoute. Jeśli chcesz użyć innej metody do pracy z Twojego obwodu, wybierz artykułu z następującej listy:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [Program PowerShell](expressroute-howto-routing-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-routing-cli.md)
> * [Wideo — prywatna komunikacja równorzędna](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Wideo — publicznej komunikacji równorzędnej](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Wideo — komunikacja równorzędna firmy Microsoft](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-routing-classic.md)
> 

Można skonfigurować jedną komunikację równorzędną, dwie lub trzy (prywatną Azure, publiczną Azure i Microsoft) dla obwodu usługi ExpressRoute. Możesz skonfigurować komunikację równorzędną w dowolnej kolejności. Musisz jednak pamiętać, aby kończyć konfiguracje poszczególnych komunikacji równorzędnych pojedynczo. Aby uzyskać więcej informacji na temat routingu domen i komunikacji równorzędnej, zobacz [informacje o obwodach i komunikacji równorzędnej](expressroute-circuit-peerings.md).

## <a name="configuration-prerequisites"></a>Wymagania wstępne dotyczące konfiguracji

* Pamiętaj, aby przed rozpoczęciem konfiguracji przejrzeć strony z [wymaganiami wstępnymi](expressroute-prerequisites.md), [wymaganiami routingu](expressroute-routing.md) oraz [przepływami pracy](expressroute-workflows.md).
* Musisz mieć aktywny obwód usługi ExpressRoute. Zanim przejdziesz dalej, postępuj zgodnie z instrukcjami, aby [utworzyć obwód usługi ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md), który powinien zostać włączony przez dostawcę połączenia. Obwód usługi ExpressRoute musi być w stanie zainicjowany i włączony na tym, aby można było uruchamiać polecenia cmdlet w kolejnych sekcjach.
* Jeśli planujesz używać udostępnionego skrótu klucza/MD5, pamiętaj użyjemy w obie strony tunelu i ograniczyć liczbę znaków alfanumerycznych, maksymalnie 25. Znaki specjalne są nieobsługiwane. 

Te instrukcje dotyczą tylko obwodów utworzonych przy pomocy dostawców oferujących usługi łączności warstwy 2. Jeśli używasz dostawcy usług, który oferuje zarządzane w warstwie 3 usługi (zwykle IPVPN, np. MPLS), dostawca połączenia skonfiguruje i zarządza routing. 

> [!IMPORTANT]
> Obecnie nie anonsujemy komunikacji równorzędnej skonfigurowanej przez dostawców usług w portalu zarządzania usługami. Pracujemy nad tym, by wkrótce włączyć tę funkcję. Skontaktuj się z dostawcą usług, przed skonfigurowaniem komunikacji równorzędnej BGP.
> 
> 

## <a name="msft"></a>Komunikacja równorzędna firmy Microsoft

Ta sekcja ułatwia tworzenie, pobieranie, aktualizacji i usuwania konfiguracji komunikacji równorzędnej firmy Microsoft dla obwodu usługi ExpressRoute.

> [!IMPORTANT]
> Obwodów usługi ExpressRoute, które zostały skonfigurowane przed 1 sierpnia 2017 r. komunikacji równorzędnej firmy Microsoft będzie miał wszystkie prefiksy usługi anonsowanego za pośrednictwem komunikacji równorzędnej firmy Microsoft, nawet jeśli nie zdefiniowano filtry tras. Komunikacja równorzędna firmy Microsoft obwodów usługi ExpressRoute, skonfigurowanych po 1 sierpnia 2017 r. nie będzie miał wszelkie prefiksy anonsowane do czasu podłączenia filtru tras do obwodu. Aby uzyskać więcej informacji, zobacz [skonfigurować filtr tras dla komunikacji równorzędnej firmy Microsoft](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Aby utworzyć komunikację równorzędną Microsoft

1. Skonfiguruj obwód usługi ExpressRoute. Zanim przejdziesz dalej, upewnij się, że obwód jest w całości obsługiwany przez dostawcę połączenia. Jeśli dostawca połączenia oferuje zarządzane usługi warstwy 3, możesz poprosić dostawcą połączenia, aby umożliwić firmie Microsoft komunikacji równorzędnej. W takiej sytuacji nie trzeba będzie wykonywać instrukcji wymienionych w następnych sekcjach. Jednak jeśli dostawca połączenia nie zarządza routingiem, po utworzeniu obwodu, nadal konfigurację za pomocą następnych kroków.

   ![Lista komunikacji równorzędnej firmy Microsoft](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Skonfiguruj komunikację równorzędną Microsoft dla obwodu. Zanim przejdziesz dalej, upewnij się, że masz poniższe informacje.

   * Podsieć /30 dla połączenia podstawowego. Musi to być prawidłowy publiczny prefiks IPv4, którego jesteś właścicielem, zarejestrowany w RIR/IRR. Z tej podsieci spowoduje przypisanie pierwszego adresu IP niemożliwe do routera, jak firma Microsoft używa drugiego IP niemożliwe do jego routera.
   * Podsieć /30 dla połączenia dodatkowego. Musi to być prawidłowy publiczny prefiks IPv4, którego jesteś właścicielem, zarejestrowany w RIR/IRR. Z tej podsieci spowoduje przypisanie pierwszego adresu IP niemożliwe do routera, jak firma Microsoft używa drugiego IP niemożliwe do jego routera.
   * Prawidłowy identyfikator sieci VLAN do ustanowienia tej komunikacji równorzędnej jest włączony. Upewnij się, że żadna inna komunikacja równorzędna w obwodzie nie używa tego samego identyfikatora VLAN. Łączy podstawowe i pomocnicze należy używać tego samego identyfikatora sieci VLAN.
   * Numer AS do komunikacji równorzędnej. Możesz używać 2-bajtowych i 4-bajtowych numerów AS.
   * Anonsowane prefiksy: Musisz podać listę wszystkich prefiksów, które planujesz anonsować za pośrednictwem sesji BGP. Akceptowane są tylko prefiksy publicznych adresów IP. Jeśli zamierzasz wysłać zestaw prefiksów, możesz wysłać listę rozdzielonych przecinkami. Prefiksy te muszą być zarejestrowane na Ciebie w RIR/IRR.
   * **Opcjonalnie —** numer ASN klienta: Jeśli anonsujesz prefiksy, które nie są rejestrowane do numeru AS komunikacji równorzędnej, możesz określić numer AS, do którego są rejestrowane.
   * Nazwa rejestru routingu: Możesz określić RIR / IRR, względem którego numer AS i prefiksy są.
   * **Opcjonalnie —** Skrót MD5, jeśli zdecydujesz się go użyć.
3. Możesz wybrać komunikację równorzędną, którą chcesz skonfigurować, jak pokazano w poniższym przykładzie. Zaznacz wiersz dotyczący komunikacji równorzędnej firmy Microsoft.

   ![Wybierz wiersz dotyczący komunikacji równorzędnej firmy Microsoft](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft1.png)
4. Skonfiguruj komunikację równorzędną firmy Microsoft. Na poniższej ilustracji przedstawiono przykładową konfigurację:

   ![Konfigurowanie komunikacji równorzędnej firmy Microsoft](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft2.png)
5. Po określeniu wszystkich parametrów zapisz konfigurację.

   Jeśli Twój obwód pobiera do "konieczna weryfikacja" stanu (jak pokazano na ilustracji), należy otworzyć bilet pomocy technicznej, aby okazać dowód własności prefiksów zespołowi pomocy technicznej.

   ![Zapisz konfigurację komunikacji równorzędnej firmy Microsoft](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft5.png)

   Można otworzyć bilet pomocy technicznej bezpośrednio z poziomu portalu, jak pokazano w poniższym przykładzie:

   ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft6.png)


1. Po pomyślnym zaakceptowaniu konfiguracji, zostanie wyświetlony podobny do poniższej ilustracji:

   ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)

### <a name="getmsft"></a>Aby wyświetlić szczegóły dotyczące komunikacji równorzędnej firmy Microsoft

Możesz wyświetlić właściwości publicznej komunikacji równorzędnej Azure, wybierając ją.

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft3.png)

### <a name="updatemsft"></a>Aby zaktualizować konfigurację komunikacji równorzędnej firmy Microsoft

Można wybrać wiersz dotyczący komunikacji równorzędnej i zmodyfikować jej właściwości.

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)

### <a name="deletemsft"></a>Aby usunąć komunikację równorzędną firmy Microsoft

Możesz usunąć konfigurację komunikacji równorzędnej, wybierając ikonę usuwania, jak pokazano na poniższej ilustracji:

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft4.png)

## <a name="private"></a>Prywatną komunikację równorzędną Azure

Ta sekcja ułatwia tworzenie, pobieranie, aktualizowanie i usuwanie prywatnej komunikacji równorzędnej konfiguracji Azure dla obwodu usługi ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Aby utworzyć prywatną komunikację równorzędną

1. Skonfiguruj obwód usługi ExpressRoute. Zanim przejdziesz dalej, upewnij się, że obwód jest w całości obsługiwany przez dostawcę połączenia. Jeśli dostawca połączenia oferuje zarządzane usługi warstwy 3, możesz poprosić dostawcą połączenia, aby umożliwić prywatną komunikację równorzędną Azure dla Ciebie. W takiej sytuacji nie trzeba będzie wykonywać instrukcji wymienionych w następnych sekcjach. Jednak jeśli dostawca połączenia nie zarządza routingiem, po utworzeniu obwodu, nadal konfigurację za pomocą następnych kroków.

   ![list](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Skonfiguruj prywatną komunikację równorzędną Azure dla obwodu. Zanim przejdziesz do następnych kroków, upewnij się, czy masz następujące elementy:

   * Podsieć /30 dla połączenia podstawowego. Podsieci nie może być częścią żadnej przestrzeni adresowej zarezerwowanej dla sieci wirtualnych. Z tej podsieci spowoduje przypisanie pierwszego adresu IP niemożliwe do routera, jak firma Microsoft używa drugiego IP niemożliwe do jego routera.
   * Podsieć /30 dla połączenia dodatkowego. Podsieci nie może być częścią żadnej przestrzeni adresowej zarezerwowanej dla sieci wirtualnych. Z tej podsieci spowoduje przypisanie pierwszego adresu IP niemożliwe do routera, jak firma Microsoft używa drugiego IP niemożliwe do jego routera.
   * Prawidłowy identyfikator sieci VLAN do ustanowienia tej komunikacji równorzędnej jest włączony. Upewnij się, że żadna inna komunikacja równorzędna w obwodzie nie używa tego samego identyfikatora VLAN. Łączy podstawowe i pomocnicze należy używać tego samego identyfikatora sieci VLAN.
   * Numer AS do komunikacji równorzędnej. Możesz używać 2-bajtowych i 4-bajtowych numerów AS. Możesz użyć prywatnego numeru AS dla tej komunikacji równorzędnej z wyjątkiem numer AS od 65515 do 65520, włącznie.
   * **Opcjonalnie —** Skrót MD5, jeśli zdecydujesz się go użyć.
3. Wybierz wiersz dotyczący komunikacji równorzędnej Azure prywatnych, jak pokazano w poniższym przykładzie:

   ![prywatna](./media/expressroute-howto-routing-portal-resource-manager/rprivate1.png)
4. Skonfiguruj prywatną komunikację równorzędną. Na poniższej ilustracji przedstawiono przykładową konfigurację:

   ![Skonfiguruj prywatną komunikację równorzędną](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)
5. Po określeniu wszystkich parametrów zapisz konfigurację. Po pomyślnym zaakceptowaniu konfiguracji, zostanie wyświetlony podobny do poniższego przykładu:

   ![Zapisz prywatnej komunikacji równorzędnej](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)

### <a name="getprivate"></a>Aby wyświetlić Azure szczegóły dotyczące komunikacji równorzędnej prywatnej

Możesz wyświetlić właściwości prywatnej komunikacji równorzędnej Azure, wybierając ją.

![Wyświetl prywatnej komunikacji równorzędnej](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)

### <a name="updateprivate"></a>Aby zaktualizować konfigurację komunikacji równorzędnej prywatnej Azure

Można wybrać wiersz dotyczący komunikacji równorzędnej i zmodyfikować jej właściwości.

![Aktualizuj prywatnej komunikacji równorzędnej](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)

### <a name="deleteprivate"></a>Aby usunąć prywatną komunikację równorzędną Azure

Możesz usunąć konfigurację komunikacji równorzędnej, wybierając ikonę usuwania, jak pokazano na poniższej ilustracji:

> [!WARNING]
> Należy się upewnić, czy wszystkie połączenia zasięgu globalnym usługi ExpressRoute i sieci wirtualne zostały usunięte przed uruchomieniem tego przykładu. 
> 
> 

![Usuwanie prywatnej komunikacji równorzędnej](./media/expressroute-howto-routing-portal-resource-manager/rprivate4.png)

## <a name="public"></a>Publicznej komunikacji równorzędnej Azure

Ta sekcja ułatwia tworzenie, pobieranie, aktualizowanie i usuwanie publicznej komunikacji równorzędnej konfiguracji Azure dla obwodu usługi ExpressRoute.

### <a name="to-create-azure-public-peering"></a>Aby utworzyć publiczną komunikację równorzędną Azure

1. Skonfiguruj obwód usługi ExpressRoute. Zanim przejdziesz dalej, upewnij się, że obwód jest w całości obsługiwany przez dostawcę połączenia. Jeśli dostawca połączenia oferuje zarządzane usługi warstwy 3, możesz poprosić dostawcą połączenia, aby umożliwić publicznej komunikacji równorzędnej Azure dla Ciebie. W takiej sytuacji nie trzeba będzie wykonywać instrukcji wymienionych w następnych sekcjach. Jednak jeśli dostawca połączenia nie zarządza routingiem, po utworzeniu obwodu, nadal konfigurację za pomocą następnych kroków.

   ![Lista publicznej komunikacji równorzędnej](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Skonfiguruj publiczną konfigurację równorzędną Azure dla obwodu. Zanim przejdziesz do następnych kroków, upewnij się, czy masz następujące elementy:

   * Podsieć /30 dla połączenia podstawowego. Musi to być prawidłowy publiczny prefiks IPv4. Z tej podsieci spowoduje przypisanie pierwszego adresu IP niemożliwe do routera, jak firma Microsoft używa drugiego IP niemożliwe do jego routera. 
   * Podsieć /30 dla połączenia dodatkowego. Musi to być prawidłowy publiczny prefiks IPv4. Z tej podsieci spowoduje przypisanie pierwszego adresu IP niemożliwe do routera, jak firma Microsoft używa drugiego IP niemożliwe do jego routera.
   * Prawidłowy identyfikator sieci VLAN do ustanowienia tej komunikacji równorzędnej jest włączony. Upewnij się, że żadna inna komunikacja równorzędna w obwodzie nie używa tego samego identyfikatora VLAN. Łączy podstawowe i pomocnicze należy używać tego samego identyfikatora sieci VLAN.
   * Numer AS do komunikacji równorzędnej. Możesz używać 2-bajtowych i 4-bajtowych numerów AS.
   * **Opcjonalnie —** Skrót MD5, jeśli zdecydujesz się go użyć.
3. Wybierz Azure publicznych wiersz dotyczący komunikacji równorzędnej, jak pokazano na poniższej ilustracji:

   ![Wybierz wiersz dotyczący publicznej komunikacji równorzędnej](./media/expressroute-howto-routing-portal-resource-manager/rpublic1.png)
4. Skonfiguruj publiczną komunikację równorzędną. Na poniższej ilustracji przedstawiono przykładową konfigurację:

   ![Konfigurowanie publicznej komunikacji równorzędnej](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)
5. Po określeniu wszystkich parametrów zapisz konfigurację. Po pomyślnym zaakceptowaniu konfiguracji, zostanie wyświetlony podobny do poniższego przykładu:

   ![Zapisywanie konfiguracji publicznej komunikacji równorzędnej](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)

### <a name="getpublic"></a>Aby wyświetlić Azure szczegóły dotyczące komunikacji równorzędnej publicznej

Możesz wyświetlić właściwości publicznej komunikacji równorzędnej Azure, wybierając ją.

![właściwości publicznej komunikacji równorzędnej widoku](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)

### <a name="updatepublic"></a>Aby zaktualizować konfigurację komunikacji równorzędnej publicznej Azure

Można wybrać wiersz dotyczący komunikacji równorzędnej i zmodyfikować jej właściwości.

![Wybierz wiersz dotyczący publicznej komunikacji równorzędnej](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)

### <a name="deletepublic"></a>Aby usunąć publiczną komunikację równorzędną Azure

Możesz usunąć konfigurację komunikacji równorzędnej, wybierając ikonę usuwania, jak pokazano w poniższym przykładzie:

![Usuwanie publicznej komunikacji równorzędnej](./media/expressroute-howto-routing-portal-resource-manager/rpublic4.png)

## <a name="next-steps"></a>Kolejne kroki

Następny krok: [połączyć sieć wirtualną z obwodem usługi ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
* Więcej informacji na temat przepływów pracy usługi ExpressRoute znajduje się w artykule [ExpressRoute workflows](expressroute-workflows.md) (Przepływy pracy usługi ExpressRoute).
* Aby uzyskać więcej informacji o komunikacji równorzędnej obwodu, zobacz artykuł [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (Obwody i domeny routingu usługi ExpressRoute).
* Więcej informacji na temat pracy z sieciami wirtualnymi znajduje się w artykule [Virtual network overview](../virtual-network/virtual-networks-overview.md) (Omówienie sieci wirtualnych).
