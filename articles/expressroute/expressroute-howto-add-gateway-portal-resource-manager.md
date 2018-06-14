---
title: 'Dodaj bramę sieci wirtualnej do sieci wirtualnej dla usługi ExpressRoute: Portal: Azure | Dokumentacja firmy Microsoft'
description: W tym artykule przedstawiono dodawania bramy sieci wirtualnej do już utworzonego sieci wirtualnej Menedżera zasobów dla usługi ExpressRoute.
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2017
ms.author: cherylmc
ms.openlocfilehash: 2bd0cf8be87937044ad515a2c6f253b1711bb2bf
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2017
ms.locfileid: "23850868"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-the-azure-portal"></a>Konfigurowanie bramy sieci wirtualnej dla usługi przy użyciu portalu Azure
> [!div class="op_single_selector"]
> * [Resource Manager — witryna Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager — program PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Classic — PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video - portalu Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

W tym artykule przedstawiono kroki, aby dodać bramę sieci wirtualnej istniejące sieci wirtualnej. W tym artykule przedstawiono kroki umożliwiające dodawanie, zmienianie rozmiaru i usunąć bramę sieci wirtualnej (VNet) dla istniejącej sieci wirtualnej. Kroki dla tej konfiguracji są przeznaczone dla sieci wirtualnych, które zostały utworzone przy użyciu modelu wdrażania usługi Resource Manager, który będzie używany w konfiguracji usługi ExpressRoute. Aby uzyskać więcej informacji na temat bram sieci wirtualnej i ustawień konfiguracji bramy ExpressRoute, zobacz [temat bram sieci wirtualnej dla usługi ExpressRoute](expressroute-about-virtual-network-gateways.md). 


## <a name="before-beginning"></a>Przed rozpoczęciem

Kroki dla tego zadania użyć sieci wirtualnej na podstawie wartości na poniższej liście odwołania konfiguracji. W naszym przykładzie kroki możemy Użyj tej listy. Możesz skopiować listę i użyj go jako odwołanie, zastąpionymi wartościami z własnego.

**Lista odwołań konfiguracji**

* Nazwa sieci wirtualnej = "TestVNet"
* Przestrzeń adresową sieci wirtualnej = 192.168.0.0/16
* Nazwa podsieci = "FrontEnd" 
    * Przestrzeń adresów podsieci = "192.168.1.0/24"
* Grupa zasobów = "TestRG"
* Lokalizacja = "Wschodnie stany USA"
* Nazwa podsieci bramy: "GatewaySubnet" zawsze nazwę podsieci bramy *GatewaySubnet*.
    * Przestrzeń adresów podsieci bramy = "192.168.200.0/26"
* Nazwa bramy = "ERGW"
* Nazwa IP bramy = "MyERGWVIP"
* Typ bramy = "ExpressRoute" tego typu jest wymagany dla konfiguracji usługi ExpressRoute.
* Nazwa publicznego adresu IP bramy = "MyERGWVIP"

Możesz wyświetlić [wideo](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network) te kroki przed rozpoczęciem konfiguracji.

## <a name="create-the-gateway-subnet"></a>Tworzenie podsieci bramy

1. W [portalu](http://portal.azure.com) przejdź do sieci wirtualnej usługi Resource Manager, dla której chcesz utworzyć bramę sieci wirtualnej.
2. W sekcji **Ustawienia** bloku sieci wirtualnej kliknij pozycję **Podsieci**, aby rozwinąć blok Podsieci.
3. W bloku **Podsieci** kliknij pozycję **+Podsieć bramy**, aby otworzyć blok **Dodaj podsieć**. 
   
    ![Dodawanie podsieci bramy](./media/expressroute-howto-add-gateway-portal-resource-manager/addgwsubnet.png "Dodawanie podsieci bramy")


4. **Nazwa** dla podsieci zostanie automatycznie wypełniona wartością „GatewaySubnet”. Ta wartość jest wymagana, aby platforma Azure mogła rozpoznać podsieć jako podsieć bramy. Dostosuj automatycznie wypełnione wartości w polu **Zakres adresów** do wymagań konfiguracji. Zaleca się utworzenie podsieci bramy z /27 lub większy (/ 26, / 25, itp.). Następnie kliknij przycisk **OK** Aby zapisać wartości i Utwórz podsieć bramy.

    ![Dodawanie podsieci](./media/expressroute-howto-add-gateway-portal-resource-manager/addsubnetgw.png "Dodawanie podsieci")

## <a name="create-the-virtual-network-gateway"></a>Tworzenie bramy sieci wirtualnej

1. W portalu po lewej stronie kliknij pozycję **+** i wpisz „brama sieci wirtualnej” w polu wyszukiwania. Znajdź pozycję **Brama sieci wirtualnej** w wynikach wyszukiwania i kliknij tę pozycję. U dołu bloku **Brama sieci wirtualnej** kliknij pozycję **Utwórz**. Spowoduje to otwarcie bloku **Tworzenie bramy sieci wirtualnej**.
2. W bloku **Tworzenie bramy sieci wirtualnej** wypełnij wartości dla swojej bramy sieci wirtualnej.

    ![Pola bloku Tworzenie bramy sieci wirtualnej](./media/expressroute-howto-add-gateway-portal-resource-manager/gw.png "Pola bloku Tworzenie bramy sieci wirtualnej")
3. **Nazwa**: Nadaj nazwę bramie. Nie chodzi o nazwę podsieci bramy. Jest to nazwa obiektu bramy, który zostanie utworzony.
4. **Typ bramy**: Wybierz **ExpressRoute**.
5. **Jednostka SKU**: Wybierz jednostkę SKU bramy z listy rozwijanej.
6. **Lokalizacja**: Dostosuj wartość w polu **Lokalizacja**, aby wskazywała lokalizację sieci wirtualnej. Jeśli lokalizacja nie wskazuje regionu, w którym znajduje się Twoja sieć wirtualna, sieć ta nie jest widoczna na liście rozwijanej „Wybierz sieć wirtualną”.
7. Wybierz sieć wirtualną, do której chcesz dodać bramę. Kliknij polecenie **Sieć wirtualna**, aby otworzyć blok **Wybieranie sieci wirtualnej**. Wybierz sieć wirtualną. Jeśli sieć wirtualna nie jest widoczna, upewnij się, że wartość w polu **Lokalizacja** wskazuje region, w którym znajduje się sieć wirtualna.
9. Wybierz publiczny adres IP. Kliknij polecenie **Publiczny adres IP**, aby otworzyć blok **Wybieranie publicznego adresu IP**. Następnie kliknij przycisk **Utwórz nowy**, aby otworzyć **blok Tworzenie bramy sieci lokalnej**. Wprowadź nazwę dla publicznego adresu IP. W bloku zostanie utworzony obiekt publicznego adresu IP, do którego publiczny adres IP zostanie dynamicznie przypisany. Kliknij przycisk **OK**, aby zapisać zmiany w tym bloku.
10. **Subskrypcja**: Sprawdź, czy wybrano poprawną subskrypcję.
11. **Grupa zasobów**: To ustawienie jest określane przez wybraną sieć wirtualną.
12. Nie zmieniaj ustawienia **Lokalizacja** po określeniu poprzednich ustawień.
13. Sprawdź poprawność ustawień. Jeśli chcesz, aby brama była wyświetlana na pulpicie nawigacyjnym, możesz wybrać opcję **Przypnij do pulpitu nawigacyjnego** znajdującą się u dołu bloku.
14. Kliknij przycisk **Utwórz**, aby rozpocząć tworzenie bramy. Ustawienia zostaną zweryfikowane i brama zostanie wdrożona. Tworzenie bramy sieci wirtualnej może potrwać do 45 minut.

## <a name="next-steps"></a>Kolejne kroki
Po utworzeniu bramy sieci wirtualnej sieci wirtualnej można połączyć z obwodem usługi ExpressRoute. Zobacz [połączyć sieć wirtualną z obwodem usługi ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md).