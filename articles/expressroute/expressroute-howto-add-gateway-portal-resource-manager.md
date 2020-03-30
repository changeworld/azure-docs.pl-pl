---
title: 'Usługa Azure ExpressRoute: dodawanie bramy do sieci wirtualnej: portal'
description: W tym artykule oszukuje cię dodawanie bramy sieci wirtualnej do już utworzonej sieci wirtualnej Menedżera zasobów dla usługi ExpressRoute przy użyciu portalu Azure.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 87b656f0ef999b3b15a89476f5cba4c4fcfc2b1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264832"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-the-azure-portal"></a>Konfigurowanie bramy sieci wirtualnej dla usługi ExpressRoute przy użyciu portalu Azure
> [!div class="op_single_selector"]
> * [Resource Manager — witryna Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager — program PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klasyczny - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Klip wideo — portal Platformy Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

W tym artykule otrzymasz od kroków, aby dodać bramę sieci wirtualnej dla istniejącej sieci wirtualnej. W tym artykule otrzymasz od kroków, aby dodać, zmienić rozmiar i usunąć bramę sieci wirtualnej dla istniejącej sieci wirtualnej. Kroki dla tej konfiguracji są specjalnie dla sieci wirtualnych, które zostały utworzone przy użyciu modelu wdrażania Menedżera zasobów, który będzie używany w konfiguracji usługi ExpressRoute. Aby uzyskać więcej informacji na temat bram sieci wirtualnej i ustawień konfiguracji bramy w ucieszyć usługę ExpressRoute, zobacz [Informacje o bramy sieci wirtualnej dla usługi ExpressRoute](expressroute-about-virtual-network-gateways.md). 


## <a name="before-beginning"></a>Przed rozpoczęciem

Kroki dla tego zadania użyć sieci wirtualnej na podstawie wartości na poniższej liście odwołań konfiguracji. Używamy tej listy w naszych krokach przykładowych. Można skopiować listę, aby użyć jako odniesienia, zastępując wartości własnymi.

**Lista odwołań do konfiguracji**

* Nazwa sieci wirtualnej = "TestVNet"
* Przestrzeń adresowa sieci wirtualnej = 192.168.0.0/16
* Nazwa podsieci = "FrontEnd" 
    * Przestrzeń adresowa podsieci = "192.168.1.0/24"
* Grupa zasobów = "TestRG"
* Lokalizacja = "Wschodnie stany USA"
* Nazwa podsieci bramy: "GatewaySubnet" Zawsze należy nadać nazwę podsieci *gateway GatewaySubnet*.
    * Przestrzeń adresowa podsieci bramy = "192.168.200.0/26"
* Nazwa bramy = "ERGW"
* Nazwa publicznego adresu IP bramy = "MyERGWVIP"
* Typ bramy = "ExpressRoute" Ten typ jest wymagany dla konfiguracji usługi ExpressRoute.

Przed rozpoczęciem konfiguracji można wyświetlić [film wideo](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network) z tymi krokami.

## <a name="create-the-gateway-subnet"></a>Tworzenie podsieci bramy

1. W [portalu](https://portal.azure.com) przejdź do sieci wirtualnej usługi Resource Manager, dla której chcesz utworzyć bramę sieci wirtualnej.
2. W sekcji **Ustawienia** bloku sieci wirtualnej kliknij pozycję **Podsieci**, aby rozwinąć blok Podsieci.
3. W bloku **Podsieci** kliknij pozycję **+Podsieć bramy**, aby otworzyć blok **Dodaj podsieć**. 
   
    ![Dodaj podsieć bramy](./media/expressroute-howto-add-gateway-portal-resource-manager/addgwsubnet.png "Dodaj podsieć bramy")


4. **Nazwa** dla podsieci zostanie automatycznie wypełniona wartością „GatewaySubnet”. Ta wartość jest wymagana, aby platforma Azure mogła rozpoznać podsieć jako podsieć bramy. Dostosuj automatycznie wypełnione wartości w polu **Zakres adresów** do wymagań konfiguracji. Zalecamy utworzenie podsieci bramy z /27 lub większym (/26, /25 itp.). Następnie kliknij przycisk **OK,** aby zapisać wartości i utworzyć podsieć bramy.

    ![Dodawanie podsieci](./media/expressroute-howto-add-gateway-portal-resource-manager/addsubnetgw.png "Dodawanie podsieci")

## <a name="create-the-virtual-network-gateway"></a>Tworzenie bramy sieci wirtualnej

1. W portalu po lewej stronie **+** kliknij i wpisz "Brama sieci wirtualnej" w wyszukiwaniu. Znajdź pozycję **Brama sieci wirtualnej** w wynikach wyszukiwania i kliknij tę pozycję. U dołu bloku **Brama sieci wirtualnej** kliknij pozycję **Utwórz**. Spowoduje to otwarcie bloku **Tworzenie bramy sieci wirtualnej**.
2. W bloku **Tworzenie bramy sieci wirtualnej** wypełnij wartości dla swojej bramy sieci wirtualnej.

    ![Tworzenie pól bloku bramy sieci wirtualnej](./media/expressroute-howto-add-gateway-portal-resource-manager/gw.png "Tworzenie pól bloku bramy sieci wirtualnej")
3. **Nazwa**: Nadaj nazwę bramie. Nie chodzi o nazwę podsieci bramy. Jest to nazwa obiektu bramy, który zostanie utworzony.
4. **Typ bramy**: Wybierz **expressroute**.
5. **Jednostka SKU**: Wybierz jednostkę SKU bramy z listy rozwijanej.
6. **Lokalizacja**: Dostosuj wartość w polu **Lokalizacja**, aby wskazywała lokalizację sieci wirtualnej. Jeśli lokalizacja nie wskazuje regionu, w którym znajduje się Twoja sieć wirtualna, sieć ta nie jest widoczna na liście rozwijanej „Wybierz sieć wirtualną”.
7. Wybierz sieć wirtualną, do której chcesz dodać bramę. Kliknij **pozycję Sieć wirtualna,** aby otworzyć blok **Wybierz sieć wirtualną.** Wybierz sieć wirtualną. Jeśli nie widzisz sieci wirtualnej, upewnij się, że pole **Lokalizacja** wskazuje region, w którym znajduje się sieć wirtualna.
9. Wybierz publiczny adres IP. Kliknij polecenie **Publiczny adres IP**, aby otworzyć blok **Wybieranie publicznego adresu IP**. Następnie kliknij przycisk **Utwórz nowy**, aby otworzyć **blok Tworzenie bramy sieci lokalnej**. Wprowadź nazwę dla publicznego adresu IP. W bloku zostanie utworzony obiekt publicznego adresu IP, do którego publiczny adres IP zostanie dynamicznie przypisany. Kliknij przycisk **OK**, aby zapisać zmiany w tym bloku.
10. **Subskrypcja**: Sprawdź, czy wybrano poprawną subskrypcję.
11. **Grupa zasobów**: To ustawienie jest określane przez wybraną sieć wirtualną.
12. Nie zmieniaj ustawienia **Lokalizacja** po określeniu poprzednich ustawień.
13. Sprawdź poprawność ustawień. Jeśli chcesz, aby brama była wyświetlana na pulpicie nawigacyjnym, możesz wybrać opcję **Przypnij do pulpitu nawigacyjnego** znajdującą się u dołu bloku.
14. Kliknij przycisk **Utwórz**, aby rozpocząć tworzenie bramy. Ustawienia zostaną zweryfikowane i brama zostanie wdrożona. Utworzenie bramy sieci wirtualnej może potrwać do 45 minut.

## <a name="next-steps"></a>Następne kroki
Po utworzeniu bramy sieci wirtualnej można połączyć swoją witrynę wirtualną z obwódem usługi ExpressRoute. Zobacz [Łączenie sieci wirtualnej z obwodem usługi ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md).
