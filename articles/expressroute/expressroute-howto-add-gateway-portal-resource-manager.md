---
title: 'Dodawanie bramy do sieci wirtualnej platformy Azure dla usługi ExpressRoute: Portal | Microsoft Docs'
description: Ten artykuł przeprowadzi dodawania bramy sieci wirtualnej do sieci wirtualnej usługi Resource Manager zostały już utworzone dla usługi ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 68376751a3c673b2d89d028312f992aec40d4dee
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60366020"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-the-azure-portal"></a>Konfigurowanie bramy sieci wirtualnej dla usługi ExpressRoute za pomocą witryny Azure portal
> [!div class="op_single_selector"]
> * [Resource Manager — witryna Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager — program PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klasyczny — PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Wideo — witryna Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

W tym artykule przedstawiono kroki Aby dodać bramę sieci wirtualnej dla istniejących sieci wirtualnej. W tym artykule przedstawiono kroki, aby dodać, zmienić rozmiar lub usunąć bramę sieci wirtualnej (VNet) wstępnie istniejącej sieci wirtualnej. W krokach dla tej konfiguracji są przeznaczone na potrzeby sieci wirtualnych, które zostały utworzone przy użyciu modelu wdrażania usługi Resource Manager, który będzie używany w konfiguracji usługi ExpressRoute. Aby uzyskać więcej informacji na temat bram sieci wirtualnej i ustawienia konfiguracji bramy usługi ExpressRoute, zobacz [o bramach sieci wirtualnej dla usługi ExpressRoute](expressroute-about-virtual-network-gateways.md). 


## <a name="before-beginning"></a>Przed rozpoczęciem

Kroki opisane w tym celu użyć sieci wirtualnej na podstawie wartości na poniższej liście konfiguracji odniesienia. Używamy tej listy w naszym przykładzie krokach. Możesz skopiować listy, które będzie używany jako odwołanie, zastępując wartości swoimi własnymi.

**Lista odwołań konfiguracji**

* Nazwa sieci wirtualnej = "TestVNet"
* Przestrzeń adresowa sieci wirtualnej = 192.168.0.0/16
* Nazwa podsieci = "Fronton" 
    * Przestrzeń adresowa podsieci = "192.168.1.0/24"
* Grupa zasobów = "TestRG"
* Lokalizacja = "Wschodnie stany USA"
* Nazwa podsieci bramy: "Gatewaysubent" podsieć bramy należy zawsze nazywać *GatewaySubnet*.
    * Przestrzeń adresową podsieci bramy = "192.168.200.0/26"
* Nazwa bramy = "ERGW"
* Nazwa publicznego adresu IP bramy = "MyERGWVIP"
* Typ bramy "ExpressRoute" = tego typu jest wymagany dla konfiguracji usługi ExpressRoute.

Możesz wyświetlić [wideo](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network) z następujących czynności przed rozpoczęciem konfiguracji.

## <a name="create-the-gateway-subnet"></a>Tworzenie podsieci bramy

1. W [portalu](https://portal.azure.com) przejdź do sieci wirtualnej usługi Resource Manager, dla której chcesz utworzyć bramę sieci wirtualnej.
2. W sekcji **Ustawienia** bloku sieci wirtualnej kliknij pozycję **Podsieci**, aby rozwinąć blok Podsieci.
3. W bloku **Podsieci** kliknij pozycję **+Podsieć bramy**, aby otworzyć blok **Dodaj podsieć**. 
   
    ![Dodawanie podsieci bramy](./media/expressroute-howto-add-gateway-portal-resource-manager/addgwsubnet.png "Dodawanie podsieci bramy")


4. **Nazwa** dla podsieci zostanie automatycznie wypełniona wartością „GatewaySubnet”. Ta wartość jest wymagana, aby platforma Azure mogła rozpoznać podsieć jako podsieć bramy. Dostosuj automatycznie wypełnione wartości w polu **Zakres adresów** do wymagań konfiguracji. Zaleca się utworzenie podsieci bramy/27 lub większej (/ 26, / 25 itp.). Następnie kliknij przycisk **OK** Zapisz wartości i utworzyć podsieć bramy.

    ![Dodawanie podsieci](./media/expressroute-howto-add-gateway-portal-resource-manager/addsubnetgw.png "Dodawanie podsieci")

## <a name="create-the-virtual-network-gateway"></a>Tworzenie bramy sieci wirtualnej

1. W portalu po lewej stronie kliknij pozycję **+** i wpisz „brama sieci wirtualnej” w polu wyszukiwania. Znajdź pozycję **Brama sieci wirtualnej** w wynikach wyszukiwania i kliknij tę pozycję. U dołu bloku **Brama sieci wirtualnej** kliknij pozycję **Utwórz**. Spowoduje to otwarcie bloku **Tworzenie bramy sieci wirtualnej**.
2. W bloku **Tworzenie bramy sieci wirtualnej** wypełnij wartości dla swojej bramy sieci wirtualnej.

    ![Pola bloku Tworzenie bramy sieci wirtualnej](./media/expressroute-howto-add-gateway-portal-resource-manager/gw.png "Pola bloku Tworzenie bramy sieci wirtualnej")
3. **Nazwa**: Nazwa bramy. Nie chodzi o nazwę podsieci bramy. Jest to nazwa obiektu bramy, który zostanie utworzony.
4. **Typ bramy**: Wybierz **ExpressRoute**.
5. **SKU**: Wybierz jednostkę SKU bramy z listy rozwijanej.
6. **Lokalizacja**: Dostosuj pole **Lokalizacja**, aby wskazywało miejsce, w którym znajduje się sieć wirtualna. Jeśli lokalizacja nie wskazuje regionu, w którym znajduje się Twoja sieć wirtualna, sieć ta nie jest widoczna na liście rozwijanej „Wybierz sieć wirtualną”.
7. Wybierz sieć wirtualną, do której chcesz dodać bramę. Kliknij polecenie **Sieć wirtualna**, aby otworzyć blok **Wybieranie sieci wirtualnej**. Wybierz sieć wirtualną. Jeśli sieć wirtualna nie jest widoczna, upewnij się, że wartość w polu **Lokalizacja** wskazuje region, w którym znajduje się sieć wirtualna.
9. Wybierz publiczny adres IP. Kliknij polecenie **Publiczny adres IP**, aby otworzyć blok **Wybieranie publicznego adresu IP**. Następnie kliknij przycisk **Utwórz nowy**, aby otworzyć **blok Tworzenie bramy sieci lokalnej**. Wprowadź nazwę dla publicznego adresu IP. W bloku zostanie utworzony obiekt publicznego adresu IP, do którego publiczny adres IP zostanie dynamicznie przypisany. Kliknij przycisk **OK**, aby zapisać zmiany w tym bloku.
10. **Subskrypcja**: Sprawdź, czy wybrano poprawną subskrypcję.
11. **Grupa zasobów**: To ustawienie jest określane przez sieci wirtualnej, która została wybrana.
12. Nie zmieniaj ustawienia **Lokalizacja** po określeniu poprzednich ustawień.
13. Sprawdź poprawność ustawień. Jeśli chcesz, aby brama była wyświetlana na pulpicie nawigacyjnym, możesz wybrać opcję **Przypnij do pulpitu nawigacyjnego** znajdującą się u dołu bloku.
14. Kliknij przycisk **Utwórz**, aby rozpocząć tworzenie bramy. Ustawienia zostaną zweryfikowane i brama zostanie wdrożona. Tworzenie bramy sieci wirtualnej może potrwać do 45 minut.

## <a name="next-steps"></a>Kolejne kroki
Po utworzeniu bramy sieci wirtualnej, możesz połączyć sieć wirtualną z obwodem usługi ExpressRoute. Zobacz [łączenie sieci wirtualnej z obwodem usługi ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md).
