---
title: 'Łączenie sieci wirtualnej platformy Azure z inną siecią wirtualną: Portal | Microsoft Docs'
description: Utwórz połączenie usługi bramy VPN Gateway między sieciami wirtualnymi przy użyciu usługi Resource Manager i witryny Azure Portal.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: a7015cfc-764b-46a1-bfac-043d30a275df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: cherylmc
ms.openlocfilehash: 7e72e9ff9be0b092bc9425828bab46ffd60f0822
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38630271"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-using-the-azure-portal"></a>Konfigurowanie połączenia bramy sieci VPN między sieciami wirtualnymi przy użyciu witryny Azure Portal

Ten artykuł pomoże Ci połączyć sieci wirtualne przy użyciu typu połączenia sieć wirtualna-sieć wirtualna. Sieci wirtualne mogą być zlokalizowane w tych samych lub różnych regionach i mogą funkcjonować w ramach tej samej lub różnych subskrypcji. W przypadku łączenia sieci wirtualnych z różnych subskrypcji subskrypcje nie muszą być skojarzone z tą samą dzierżawą usługi Active Directory. 

Kroki podane w tym artykule mają zastosowanie do modelu wdrażania przy użyciu usługi Resource Manager i użyto w nich witryny Azure Portal. Tę konfigurację możesz również utworzyć przy użyciu innego narzędzia wdrażania lub modelu wdrażania, wybierając inną opcję z następującej listy:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Program PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Interfejs wiersza polecenia platformy Azure](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Portal Azure (klasyczny)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Łączenie różnych modeli wdrażania — witryna Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Łączenie różnych modeli wdrażania — program PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

![Diagram połączenia między sieciami wirtualnymi (v2v)](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

## <a name="about"></a>Łączenie sieci wirtualnych — informacje

Z sieciami wirtualnymi można łączyć się na wiele sposobów. W poniższych sekcjach opisano różne metody łączenia się z sieciami wirtualnymi.

### <a name="vnet-to-vnet"></a>Połączenia między sieciami wirtualnymi

Skonfigurowanie połączenia sieci wirtualnej z siecią wirtualną jest dobrym sposobem łatwego połączenia sieci wirtualnych. Proces łączenia dwóch sieci wirtualnych przy użyciu typu połączenia sieć wirtualna-sieć wirtualna (VNet2VNet) przebiega podobnie do procesu tworzenia połączenia IPsec typu lokacja-lokacja z lokacją lokalną. Oba typy połączeń używają bramy sieci VPN, aby zapewnić bezpieczny tunel korzystający z protokołu IPsec/IKE, oraz działają tak samo pod względem komunikacji. Różnica między nimi dotyczy konfiguracji bramy sieci lokalnej. Podczas tworzenia połączenia sieć wirtualna-sieć wirtualna przestrzeń adresowa bramy sieci lokalnej nie jest widoczna. Jest ona tworzona i wypełniana automatycznie. Po zaktualizowaniu przestrzeni adresowej dla jednej sieci wirtualnej druga sieć wirtualna automatycznie kieruje ruch do zaktualizowanej przestrzeni adresowej. Utworzenie połączenia sieć wirtualna-sieć wirtualna jest zwykle szybsze i łatwiejsze niż utworzenie połączenia lokacja-lokacja między sieciami wirtualnymi.

### <a name="site-to-site-ipsec"></a>Lokacja-lokacja (IPsec)

W przypadku pracy ze złożoną konfiguracją sieci lepszym rozwiązaniem może być połączenie sieci wirtualnych za pomocą procedury tworzenia połączenia [lokacja-lokacja](vpn-gateway-howto-site-to-site-resource-manager-portal.md). W przypadku korzystania z procedury tworzenia połączenia IPsec typu lokacja-lokacja bramy sieci lokalnej są tworzone i konfigurowane ręcznie. Każda z bram sieci lokalnej sieci wirtualnej traktuje drugą sieć wirtualną jako lokację lokalną. Dzięki temu można określić dodatkową przestrzeń adresową dla bramy sieci lokalnej w celu kierowania ruchu. W przypadku zmiany przestrzeni adresowej sieci wirtualnej należy zaktualizować odpowiednią bramę sieci lokalnej w celu odzwierciedlenia zmiany. Nie jest to aktualizowane automatycznie.

### <a name="vnet-peering"></a>Komunikacja równorzędna sieci wirtualnych

Warto rozważyć połączenie sieci wirtualnych za pomocą komunikacji równorzędnej sieci wirtualnych. W przypadku komunikacji równorzędnej sieci wirtualnych nie jest używana brama sieci VPN i są z nią związane inne ograniczenia. Ponadto [ceny dotyczące komunikacji równorzędnej sieci wirtualnych](https://azure.microsoft.com/pricing/details/virtual-network) są obliczane inaczej niż [ceny dotyczące usługi VPN Gateway połączenia sieć wirtualna-sieć wirtualna](https://azure.microsoft.com/pricing/details/vpn-gateway). Aby uzyskać więcej informacji, zobacz temat [Komunikacja równorzędna sieci wirtualnych](../virtual-network/virtual-network-peering-overview.md).

## <a name="why"></a>Dlaczego warto utworzyć połączenie sieć wirtualna-sieć wirtualna?

Sieci wirtualne można łączyć za pomocą połączenia sieć wirtualna-sieć wirtualna z następujących powodów:

* **Niezależna od regionu nadmiarowość i obecność geograficzna**

  * Można tworzyć własne replikacje geograficzne lub przeprowadzać synchronizację z bezpiecznym połączeniem bez przechodzenia przez punkty końcowe dostępne z Internetu.
  * Usługi Azure Traffic Manager i Load Balancer pozwalają skonfigurować obciążenie o wysokiej dostępności z nadmiarowością geograficzną w wielu regionach świadczenia usługi Azure. Istotnym przykładem jest konfiguracja ustawienia SQL Zawsze włączone, w przypadku którego grupy dostępności rozciągają się na wiele regionów świadczenia usługi Azure.
* **Regionalne aplikacje wielowarstwowe z izolacją lub granicami administracyjnymi**

  * W ramach jednego regionu można skonfigurować aplikacje wielowarstwowe z wielu połączonych ze sobą sieci wirtualnych, korzystając z izolacji lub wymagań administracyjnych.

Komunikację między sieciami wirtualnymi można łączyć z konfiguracjami obejmującymi wiele lokacji. Pozwala to tworzyć topologie sieci, które łączą wdrożenia obejmujące wiele lokalizacji z połączeniami między sieciami wirtualnymi, jak pokazano na poniższym diagramie.

![Informacje o połączeniach](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "Informacje o połączeniach")

W tym artykule opisano łączenie sieci wirtualnych za pomocą typu połączenia sieć wirtualna-sieć wirtualna. Korzystając z tych kroków w charakterze ćwiczenia, można użyć przykładowych wartości ustawień. W tym przykładzie sieci wirtualne należą do tej samej subskrypcji, ale do różnych grup zasobów. Jeśli sieci wirtualne należą do różnych subskrypcji, nie można utworzyć połączenia w portalu. Można użyć programu [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) lub [interfejsu wiersza polecenia](vpn-gateway-howto-vnet-vnet-cli.md). Więcej informacji na temat połączeń między sieciami wirtualnymi znajduje się w sekcji [Często zadawane pytania dotyczące połączeń między sieciami wirtualnymi](#faq) na końcu tego artykułu.

### <a name="values"></a>Przykładowe ustawienia

**Wartości dla sieci TestVNet1:**

* Nazwa sieci wirtualnej: TestVNet1
* Przestrzeń adresowa: 10.11.0.0/16
* Subskrypcja: wybierz subskrypcję, której chcesz użyć
* Grupa zasobów: TestRG1
* Lokalizacja: Wschodnie stany USA
* Nazwa podsieci: FrontEnd
* Zakres adresów podsieci: 10.11.0.0/24
* Nazwa podsieci bramy: GatewaySubnet (zostanie automatycznie wypełniona w portalu)
* Zakres adresów podsieci bramy: 10.11.255.0/27
* Serwer DNS: użyj adresu IP serwera DNS.
* Nazwa bramy sieci wirtualnej: TestVNet1GW
* Typ bramy: VPN
* Typ sieci VPN: oparta na trasach
* Jednostka SKU: wybierz jednostkę SKU bramy, która ma być używana.
* Publiczna nazwa adresu IP: TestVNet1GWIP
* Nazwa połączenia: TestVNet1toTestVNet4
* Klucz współużytkowany: klucz współużytkowany można utworzyć samodzielnie. W tym przykładzie użyjemy wartości abc123. Ważne jest, aby podczas tworzenia połączenia między sieciami wirtualnymi, wartości były zgodne.

**Wartości dla sieci TestVNet4:**

* Nazwa sieci wirtualnej: TestVNet4
* Przestrzeń adresowa: 10.41.0.0/16
* Subskrypcja: wybierz subskrypcję, której chcesz użyć
* Grupa zasobów: TestRG4
* Lokalizacja: West US
* Nazwa podsieci: FrontEnd
* Zakres adresów podsieci: 10.41.0.0/24
* Nazwa podsieci bramy: GatewaySubnet (zostanie automatycznie wypełniona w portalu)
* Zakres adresów podsieci GatewaySubnet: 10.41.255.0/27
* Serwer DNS: użyj adresu IP serwera DNS.
* Nazwa bramy sieci wirtualnej: TestVNet4GW
* Typ bramy: VPN
* Typ sieci VPN: oparta na trasach
* Jednostka SKU: wybierz jednostkę SKU bramy, która ma być używana.
* Nazwa publicznego adresu IP: TestVNet4GWIP
* Nazwa połączenia: TestVNet4toTestVNet1
* Klucz współużytkowany: klucz współużytkowany można utworzyć samodzielnie. W tym przykładzie użyjemy wartości abc123. Ważne jest, aby podczas tworzenia połączenia między sieciami wirtualnymi, wartości były zgodne.

## <a name="CreatVNet"></a>1. Tworzenie i konfigurowanie sieci TestVNet1
Jeśli masz już sieć wirtualną, sprawdź, czy ustawienia są zgodne z projektem bramy sieci VPN. Zwróć szczególną uwagę na wszelkie podsieci, które mogą pokrywać się z innymi sieciami. Obecność nakładających się podsieci spowoduje, że połączenie nie będzie działać prawidłowo. Jeśli sieć wirtualną skonfigurowano poprawnie, można rozpocząć wykonywanie kroków z sekcji [Określanie serwera DNS](#dns).

### <a name="to-create-a-virtual-network"></a>Aby utworzyć sieć wirtualną
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="subnets"></a>2. Dodawanie dodatkowej przestrzeni adresowej i tworzenie podsieci
Po utworzeniu sieci wirtualnej można dodać do niej dodatkową przestrzeń adresową oraz utworzyć podsieci.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="gatewaysubnet"></a>3. Tworzenie podsieci bramy
Przed utworzeniem bramy sieci wirtualnej dla sieci wirtualnej należy najpierw utworzyć podsieć bramy. Podsieć bramy zawiera adresy IP używane przez bramę sieci wirtualnej. Jeśli to możliwe, najlepiej utworzyć podsieć bramy przy użyciu bloku CIDR /28 lub /27 w celu zapewnienia wystarczającej liczby adresów IP, aby uwzględnić wymagania dotyczące dodatkowych przyszłych konfiguracji.

Jeśli tworzysz tę konfigurację w ramach ćwiczenia, użyj [przykładowych ustawień](#values) podczas tworzenia podsieci bramy.

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-gateway-subnet"></a>Aby utworzyć podsieć bramy
[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="dns"></a>4. Określanie serwera DNS (opcjonalne)
Serwer DNS nie jest wymagany w przypadku połączenia typu sieć wirtualna-sieć wirtualna. Jeśli jednak chcesz korzystać z funkcji rozpoznawania nazw dla zasobów, które zostały wdrożone w Twojej sieci wirtualnej, określ serwer DNS. To ustawienie umożliwia określenie serwera DNS, który ma być używany do rozpoznawania nazw dla tej sieci wirtualnej. Nie powoduje ono jednak utworzenia serwera DNS.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="VNetGateway"></a>5. Tworzenie bramy sieci wirtualnej
W tym kroku zostaje utworzona brama dla sieci wirtualnej użytkownika. Tworzenie bramy często może trwać 45 minut lub dłużej, w zależności od wybranej jednostki SKU bramy. W przypadku tworzenia tej konfiguracji w ramach ćwiczenia można korzystać z [przykładowych ustawień](#values).

### <a name="to-create-a-virtual-network-gateway"></a>Aby utworzyć bramę sieci wirtualnej
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="CreateTestVNet4"></a>6. Tworzenie i konfigurowanie sieci TestVNet4
Po skonfigurowaniu sieci TestVNet1 utwórz sieć TestVNet4, powtarzając poprzednie kroki, używając wartości dla sieci TestVNet4. Przed skonfigurowaniem sieci TestVNet4 nie musisz czekać, aż tworzenie bramy sieci wirtualnej dla sieci TestVNet1 zostanie zakończone. Jeśli używasz własnych wartości, upewnij się, że przestrzenie adresowe nie pokrywają się z żadnymi sieciami wirtualnymi, z którymi chcesz się połączyć.

## <a name="TestVNet1Connection"></a>7. Konfigurowanie połączenia bramy TestVNet1
Po zakończeniu tworzenia bram sieci wirtualnej (zarówno dla sieci TestVNet1, jak i TestVNet4) można utworzyć połączenia między bramami sieci wirtualnych. W tej sekcji opisano tworzenie połączenia z sieci VNet1 do sieci VNet4. Następujące kroki działają tylko w przypadku sieci wirtualnych należących do tej samej subskrypcji. Jeśli Twoje sieci wirtualne znajdują się w różnych subskrypcjach, do utworzenia połączenia musisz użyć programu PowerShell. Zobacz artykuł [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md). Jeśli jednak Twoje sieci wirtualne znajdują się w różnych grupach zasobów w tej samej subskrypcji, możesz utworzyć połączenie między nimi przy użyciu portalu.

1. W sekcji **Wszystkie zasoby** przejdź do bramy swojej sieci wirtualnej (np. **TestVNet1GW**). Kliknij pozycję **TestVNet1GW**, aby otworzyć stronę bramy sieci wirtualnej.

  ![Strona Połączenia](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/1to4connect2.png "Strona Połączenia")
2. Kliknij przycisk **+Dodaj**, aby otworzyć stronę **Dodaj połączenie**.

  ![Dodawanie połączenia](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/add.png "Dodawanie połączenia")
3. Na stronie **Dodaj połączenie** wpisz nazwę połączenia w polu nazwy. (np. **TestVNet1toTestVNet4**).
4. W polu **Typ połączenia** wybierz pozycję **Sieć wirtualna-sieć wirtualna** z listy rozwijanej.
5. Wartość pola **Pierwsza brama sieci wirtualnej** zostaje podana automatycznie, ponieważ połączenie jest tworzone z określonej bramy sieci wirtualnej.
6. Pole **Druga brama sieci wirtualnej** jest bramą sieci wirtualnej sieci wirtualnej, z którą chcesz utworzyć połączenie. Kliknij pozycję **Wybierz inną bramę sieci wirtualnej**, aby otworzyć stronę **Wybieranie bramy sieci wirtualnej**.
7. Wyświetl bramy sieci wirtualnej wymienione na tej stronie. Pamiętaj, że wyświetlane są tylko bramy sieci wirtualnej objęte subskrypcją. Jeśli chcesz połączyć się z bramą sieci wirtualnej, której nie ma w Twojej subskrypcji, zapoznaj się z [artykułem dotyczącym programu PowerShell](vpn-gateway-vnet-vnet-rm-ps.md).
8. Kliknij bramę sieci wirtualnej, z którą chcesz nawiązać połączenie.
9. W polu **Klucz współużytkowany** wpisz klucz, który będzie używany dla tego połączenia. Klucz można wygenerować lub utworzyć samodzielnie. W przypadku połączenia lokacja-lokacja używany klucz jest dokładnie taki sam dla urządzenia lokalnego oraz połączenia bramy sieci wirtualnej. Koncepcja jest tutaj podobna, z tym że zamiast połączenia z urządzeniem sieci VPN następuje połączenie z inną bramą sieci wirtualnej.
10. Kliknij przycisk **OK** u dołu strony, aby zapisać zmiany.

## <a name="TestVNet4Connection"></a>8. Konfigurowanie połączenia bramy TestVNet4
Następnie utwórz połączenie z sieci TestVNet4 do sieci TestVNet1. W portalu znajdź bramę sieci wirtualnej skojarzoną z siecią TestVNet4. Wykonaj kroki opisane w poprzedniej sekcji, zastępując wartości, aby utworzyć połączenie z sieci TestVNet4 do sieci TestVNet1. Pamiętaj, aby użyć tego samego klucza współużytkowanego.

## <a name="VerifyConnection"></a>9. Sprawdź połączenia

W portalu znajdź bramę sieci wirtualnej. Na stronie bramy sieci wirtualnej kliknij pozycję **Połączenia**, aby wyświetlić stronę połączeń dla bramy sieci wirtualnej. Po ustanowieniu połączenia wartości stanu zmienią się na **Powodzenie** i **Połączono**. Możesz kliknąć dwukrotnie połączenie, aby otworzyć stronę **Podstawy** i wyświetlić więcej informacji.

![Powodzenie](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connected.png "Powodzenie")

Po rozpoczęciu przepływu danych zostaną wyświetlone wartości dla danych wejściowych i danych wyjściowych.

![Podstawy](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/essentials.png "Podstawy")

## <a name="to-add-additional-connections"></a>Aby dodać więcej połączeń

Jeśli chcesz dodać więcej połączeń, przejdź do bramy sieci wirtualnej, z której chcesz utworzyć połączenie, a następnie kliknij pozycję **Połączenia**. Możesz utworzyć kolejne połączenie sieć wirtualna-sieć wirtualna lub połączenie IPsec lokacja-lokacja do lokalizacji lokalnej. Pamiętaj, aby dopasować wartość w polu **Typ połączenia** do typu połączenia, które chcesz utworzyć. Przed utworzeniem dodatkowych połączeń sprawdź, czy przestrzeń adresowa Twojej sieci wirtualnej nie nakłada się na żadne przestrzenie adresowe, z którymi chcesz nawiązać połączenie. Aby zapoznać się z procedurą tworzenia połączenia lokacja-lokacja, zobacz [Tworzenie połączenia typu lokacja-lokacja](vpn-gateway-howto-site-to-site-resource-manager-portal.md).

## <a name="faq"></a>Często zadawane pytania dotyczące połączeń między sieciami wirtualnymi
Ta sekcja zawiera odpowiedzi na często zadawane pytań dotyczące połączeń między sieciami wirtualnymi.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Następne kroki

Zobacz [Zabezpieczenia sieci](../virtual-network/security-overview.md), aby uzyskać informacje na temat sposobu ograniczania ruchu sieciowego do zasobów w sieci wirtualnej.

Zobacz [Routing ruchu w sieci wirtualnej](../virtual-network/virtual-networks-udr-overview.md), aby uzyskać informacje na temat sposobu kierowania ruchu platformy Azure między platformą Azure a zasobami lokalnymi i internetowymi.
