---
title: Ustawienia łączności z maszyn wirtualnych do platformy SAP HANA na platformie Azure (duże wystąpienia) | Dokumentacja firmy Microsoft
description: Ustawienia łączności z maszyn wirtualnych przy użyciu oprogramowania SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 90d920a501d27ca21b1c2b7b7f5491cebb2c2822
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233722"
---
# <a name="connecting-azure-vms-to-hana-large-instances"></a>Łączenie maszyn wirtualnych platformy Azure do dużych wystąpień HANA

Jak już wspomniano w [platformy SAP HANA (duże wystąpienia) — omówienie i architektura na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture), podstawowe wdrożenie dużych wystąpień HANA z warstwą aplikacji SAP na platformie Azure wygląda podobnie do następującej:

![Sieci wirtualnej platformy Azure, połączone platformy SAP HANA na platformie Azure (duże wystąpienia) i w środowisku lokalnym](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

Wyszukiwanie bliżej po stronie sieci wirtualnej platformy Azure, Zdajemy sobie sprawę, potrzeby:

- Definicja usługa Azure virtual network, w którym zamierzasz wdrożyć maszyn wirtualnych w warstwie aplikacji SAP.
- Definicja domyślnego podsieci w sieci wirtualnej platformy Azure naprawdę jest tym, w której maszyny wirtualne są wdrażane.
- Sieci wirtualnej platformy Azure, który jest tworzony, musi mieć co najmniej jedną podsieć maszyny Wirtualnej i jedną podsieć bramy sieci wirtualnej usługi Azure ExpressRoute. Te podsieci powinien być przypisany zakresów adresów IP, jak określono i opisano w poniższych sekcjach.

Przyjrzyjmy się nieco bliżej podczas tworzenia sieci wirtualnej platformy Azure dla dużych wystąpień HANA.

## <a name="create-the-azure-virtual-network-for-hana-large-instances"></a>Tworzenie sieci wirtualnej platformy Azure dla dużych wystąpień HANA

>[!Note]
>Należy utworzyć sieci wirtualnej platformy Azure dla dużych wystąpień HANA przy użyciu modelu wdrażania usługi Azure Resource Manager. Starszy model wdrażania platformy Azure, powszechnie znane jako Klasyczny model wdrażania, nie jest obsługiwany przez rozwiązania dużych wystąpień HANA.

Aby utworzyć sieć wirtualną można użyć witryny Azure portal, programu PowerShell, szablonu platformy Azure lub interfejsu wiersza polecenia platformy Azure. (Aby uzyskać więcej informacji, zobacz [Utwórz sieć wirtualną przy użyciu witryny Azure portal](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)). W poniższym przykładzie przyjrzymy się sieci wirtualnej, która jest tworzona przy użyciu witryny Azure portal.

Zobaczymy, jak definicje sieci wirtualnych odnoszą się do możemy listy jako różnych zakresów adresów IP. Jeśli potrzebny jest o **przestrzeń adresowa**, mamy na myśli przestrzeni adresowej sieci wirtualnej platformy Azure może używać. Ta przestrzeń adresowa jest również odpowiedni zakres adresów sieci wirtualnej używa propagację trasy protokołu BGP. To **przestrzeń adresowa** są widoczne w tym miejscu:

![Przestrzeń adresowa sieci wirtualnej platformy Azure wyświetlane w witrynie Azure portal](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

W poprzednim przykładzie, za pomocą 10.16.0.0/16 Usługa Azure virtual network podano dość duży i szeroki zakres adresów IP do użycia. Wszystkie zakresy adresów IP kolejne podsieci w tej sieci wirtualnej mogą więc ich zakresy w tej przestrzeni adresowej. Zazwyczaj nie zaleca takich zakres duża dla jednej sieci wirtualnej na platformie Azure. Ale Przyjrzyjmy się do podsieci, które są zdefiniowane w sieci wirtualnej platformy Azure:

![Podsieci sieci wirtualnej platformy Azure i ich zakresy adresów IP](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

Przyjrzymy się sieci wirtualnej przy użyciu pierwszej podsieci maszyny Wirtualnej (w tym miejscu o nazwie "domyślna") i w podsieci o nazwie "GatewaySubnet".

W poniższej sekcji nazywamy zakres adresów IP podsieci, która została wywołana "domyślna" grafiki, jako **zakresu adresów IP podsieci maszyny Wirtualnej platformy Azure**. Będziemy odnosić się do zakresu adresów IP w podsieci bramy jako **zakres adresów IP podsieci bramy sieci wirtualnej**. 

W dwóch poprzednich grafice **przestrzeń adresową sieci wirtualnej** obejmuje zarówno **zakresu adresów IP podsieci maszyny Wirtualnej platformy Azure** i **zakres adresów IP podsieci bramy sieci wirtualnej**. 

Aby oszczędzać lub być określony za pomocą zakresów adresów IP, można ograniczyć **przestrzeń adresową sieci wirtualnej** do określonych zakresów stosowaną w każdej podsieci sieci wirtualnej. Można zdefiniować **przestrzeń adresową sieci wirtualnej** wirtualnej sieci jako wiele określonych zakresów, jak pokazano poniżej:

![Przestrzeń adresowa sieci wirtualnej platformy Azure z dwóch miejsc do magazynowania](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

W tym przypadku **przestrzeń adresową sieci wirtualnej** ma dwa miejsca do magazynowania, które są zdefiniowane. Są one takie same jak zakresy adresów IP, które są zdefiniowane dla **zakresu adresów IP podsieci maszyny Wirtualnej platformy Azure** i **zakres adresów IP podsieci bramy sieci wirtualnej.**

Standard nazewnictwa, które można użyć dla tych podsieci dzierżawy (podsieci maszyny Wirtualnej). Jednak **musi zawsze istnieć jeden i tylko jeden podsieć bramy dla każdej sieci wirtualnej** łączący się z platformą SAP HANA na obwód usługi ExpressRoute platformy Azure (duże wystąpienia). I **tej podsieci bramy musi nosić "nazwę GatewaySubnet"** aby upewnić się, że brama usługi ExpressRoute znajduje się poprawnie.

> [!WARNING] 
> Koniecznie się, że podsieć bramy zawsze mieć nazwę "GatewaySubnet".

Można użyć wielu podsieci maszyny Wirtualnej i zakresy adresów niesąsiadujące. Te zakresy adresów muszą być objęte **przestrzeń adresową sieci wirtualnej** sieci wirtualnej. Można je w postaci zagregowanej lub na liście dokładnie zakresy adresów podsieci maszyny Wirtualnej i podsieci bramy.

Poniżej przedstawiono podsumowanie ważnych informacji o sieci wirtualnej platformy Azure łączącej się z dużymi wystąpieniami platformy HANA:

- Użytkownik musi przesłać **przestrzeń adresową sieci wirtualnej** do firmy Microsoft, gdy wykonujesz początkowe wdrożenie dużych wystąpień HANA. 
- **Przestrzeń adresową sieci wirtualnej** może być jeden większym zakresie, który obejmuje zakres do zakresu adresów zakresy adresów IP w podsieci maszyny Wirtualnej platformy Azure oraz podsieci IP bramy sieci wirtualnej.
- Lub możesz przesłać wiele zakresów, które obejmują innym adresem IP adres zakresy adresów IP podsieci maszyny Wirtualnej i zakres adresów IP bramy sieci wirtualnej.
- Zdefiniowane **przestrzeń adresową sieci wirtualnej** służy do propagowania routingu BGP.
- Nazwa podsieci bramy musi być: **"Gatewaysubent"**.
- Przestrzeń adresowa służy jako filtr na stronie dużych wystąpień HANA do Zezwalaj lub nie zezwalaj na ruch z jednostkami dużych wystąpień HANA na platformie Azure. Upewnij się, że informacje o wirtualnej platformy Azure routing protokołu BGP sieci i zakresy adresów IP, które są skonfigurowane do filtrowania dużych wystąpień HANA dopasowanie, po stronie. W przeciwnym razie może wystąpić problemy z łącznością.
- Istnieją pewne szczegółowe informacje o podsieci bramy, które zostały omówione w dalszej części, w sekcji **łączenie sieci wirtualnej usługi expressroute platformy HANA dużych wystąpień.**



## <a name="different-ip-address-ranges-to-be-defined"></a>Do zdefiniowania różnych zakresów adresów IP 

Już wdrożyliśmy niektóre zakresy adresów IP, które są niezbędne do wdrażania dużych wystąpień HANA. Jednak istnieją więcej zakresów adresów IP, które są równie ważne. Omówmy niektóre szczegółowe informacje. Następujące adresy IP nie należy do przesłania do firmy Microsoft. Jednak należy zdefiniować je przed wysłaniem żądania do początkowego wdrożenia:

- **Przestrzeń adresowa sieci wirtualnej**: wprowadzonego wcześniej **przestrzeń adresową sieci wirtualnej** zakres adresów IP (lub zakresy), przypisaniu (lub planujesz przypisać) do parametru przestrzeni adresów w wirtualnej platformy Azure sieci, który Połącz ze środowiskiem duże wystąpienie SAP HANA.

 Zaleca się, że ten parametr przestrzeni adresów jest wartością wielowierszowego, składająca się z zakresy podsieci maszyny Wirtualnej platformy Azure i zakres podsieci bramy platformy Azure, jak pokazano na poprzednim grafiki. NIE może nakładać tego zakresu z lokalnych lub puli adresów IP serwerów lub zakresów adresów ER P2P. 
 
Jak uzyskać te zakresy adresów IP? 

Twój dostawca zespołu lub usługa sieci firmowej, powinien zapewnić co najmniej jeden adres IP, zakresy, nie są używane w Twojej sieci. Na przykład jeśli podsieć maszyny Wirtualnej platformy Azure jest 10.0.1.0/24 i podsieć bramy platformy Azure jest 10.0.2.0/28, zalecamy przestrzeń adresowa sieci wirtualnej platformy Azure to dwa wiersze: 10.0.1.0/24 i 10.0.2.0/28. 

Chociaż może być agregowany wartości przestrzeni adresów, zaleca się dopasowywanie ich do zakresów adresów podsieci. Dzięki temu można uniknąć przypadkowego ponownego użycia Nieużywane zakresy adresów IP w obrębie większej przestrzeni adresowych w przyszłości innym miejscu w sieci. **Przestrzeń adresowa sieci wirtualnej jest zakres adresów IP, które muszą być przesłane do firmy Microsoft, gdy zadajesz do początkowego wdrożenia**.

- **Usługa Azure zakresu adresów IP podsieci maszyny Wirtualnej:** ten zakres adresów IP, jak już wspomniano, to ten, który został przypisany (lub planujesz przypisać) do parametru podsieci sieci wirtualnej platformy Azure w sieci wirtualnej platformy Azure, który nawiązuje połączenie z duże wystąpienie SAP HANA środowisko. Ten zakres adresów IP jest używany do przypisywania adresów IP do maszyn wirtualnych platformy Azure. Adresy IP poza tym zakresem mogą łączyć się na serwerach programu duże wystąpienie SAP HANA. Jeśli to konieczne, można użyć wielu podsieci maszyny Wirtualnej platformy Azure. Firma Microsoft zaleca prefiksie/24 blok CIDR dla każdej podsieci maszyny Wirtualnej platformy Azure. Ten zakres adresów musi należeć do wartości, które są używane w przestrzeni adresowej sieci wirtualnej platformy Azure. 

Jak uzyskać ten zakres adresów IP? 

Twój dostawca zespołu lub usługa sieci firmowej powinien zapewniać zakres adresów IP, który nie jest używany w Twojej sieci.

- **Zakres adresów IP podsieci bramy sieci wirtualnej:** w zależności od funkcji, które planujesz użyć, jest zalecany rozmiar:
   - Bramy usługi ExpressRoute z największą wydajnością: / 26 blok adresów — wymagana dla klasy jednostek SKU typu II.
   - Współistnienie z sieci VPN i usługi ExpressRoute za pomocą bramy sieci wirtualnej usługi ExpressRoute o wysokiej wydajności (lub mniejsze): / 27 bloku adresu.
   - Innych sytuacjach: / 28 bloku adresu. Ten zakres adresów musi należeć do wartości używanych w wartości "Przestrzeni adresowej sieci wirtualnej". Ten zakres adresów musi należeć do wartości, które są używane w wartości przestrzeni adresów sieci wirtualnej platformy Azure, przesłanych do firmy Microsoft. Jak uzyskać ten zakres adresów IP? Twój dostawca zespołu lub usługa sieci firmowej powinien zapewniać zakres adresów IP, który nie jest obecnie jest używany w Twojej sieci. 

- **Zakres dla łączności ER P2P adresów:** ten zakres to zakres adresów IP dla połączenia P2P SAP HANA duże wystąpienie usługi ExpressRoute (ER). Ten zakres adresów IP musi mieć wartość/29 zakres adresów CIDR IP. Ten zakres nie może nakładać przy użyciu lokalnych lub innych adresów IP platformy Azure zakresy adresów. Ten zakres adresów IP jest używana do skonfigurowania łączności ER, z wirtualnej bramy usługi ExpressRoute na serwerach duże wystąpienie SAP HANA. Jak uzyskać ten zakres adresów IP? Twój dostawca zespołu lub usługa sieci firmowej powinien zapewniać zakres adresów IP, który nie jest obecnie jest używany w Twojej sieci. **Ten zakres to zakres adresów IP, która musi być przesłane do firmy Microsoft, podczas pytania o początkowe wdrożenie**.
  
- **Zakres adresów puli adresów IP serwera:** ten zakres adresów IP adres jest używany do przypisywania pojedynczy adres IP do serwerów dużych wystąpień HANA. Rozmiar podsieci zalecane jest prefiksie/24 blok CIDR. Jeśli to konieczne, może być mniejsze, mających co najmniej 64 adresów IP. Z tego zakresu pierwsze 30 adresy IP są zarezerwowane do użytku przez firmę Microsoft. Upewnij się, uwzględnione ten fakt po wybraniu rozmiarem zakresu. Ten zakres nie może nakładać przy użyciu lokalnych lub innych adresów IP platformy Azure adresy. Jak uzyskać ten zakres adresów IP? Twój dostawca zespołu lub usługa sieci firmowej powinien zapewniać zakres adresów IP, który nie jest obecnie jest używany w Twojej sieci. Prefiksie/24 (zalecane) unikatowy blok CIDR ma być używany do przypisywania określonych adresów IP jest wymagana dla oprogramowania SAP HANA na platformie Azure (duże wystąpienia). **Ten zakres to zakres adresów IP, która musi być przesłane do firmy Microsoft, podczas pytania o początkowe wdrożenie**.
 
Mimo że trzeba zdefiniować i Planowanie zakresów adresów IP, które zostały opisane wcześniej, nie trzeba przesyłać wszystkich z nich do firmy Microsoft. Zakresy adresów IP, które wymagają nazwy do firmy Microsoft to:

- Przestrzenie adresów sieci wirtualnej platformy Azure
- Zakres adresów dla łączności ER P2P
- Zakres adresów puli adresów IP serwera

Jeśli dodasz dodatkowe sieci wirtualne, które muszą połączyć się z dużymi wystąpieniami platformy HANA, należy przesłać nowe przestrzeni adresowej sieci wirtualnej platformy Azure jest dodawany do firmy Microsoft. 

Oto przykład różnych zakresów i niektóre zakresy przykład potrzebnych do konfigurowania i ostatecznie podać je do firmy Microsoft. Wartość przestrzeni adresowej sieci wirtualnej platformy Azure nie jest agregowana w pierwszym przykładzie, ale jest zdefiniowana z zakresów pierwszy zakresu adresów IP podsieci maszyny Wirtualnej platformy Azure i zakres adresów IP podsieci bramy sieci wirtualnej. 

Po skonfigurowaniu i przesłać dodatkowe zakresy adresów IP dodatkowe podsieci maszyny Wirtualnej w ramach przestrzeni adresowej sieci wirtualnej platformy Azure, można użyć wielu podsieci maszyny Wirtualnej w sieci wirtualnej platformy Azure.

![Zakresy adresów IP wymagane na platformie SAP HANA na podstawowe wdrożenie na platformie Azure (duże wystąpienia)](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

Możesz także agregować dane, która zostanie przesłana do firmy Microsoft. W takiej sytuacji przestrzeni adresowej sieci wirtualnej platformy Azure zawiera tylko jedno miejsce. Za pomocą zakresów adresów IP z poprzedniego przykładu, przestrzeni adresowej sieci wirtualnej w zagregowanej mogłoby wyglądać podobnie do następującego:

![Druga możliwość zakresów adresów IP wymagane na platformie SAP HANA na podstawowe wdrożenie na platformie Azure (duże wystąpienia)](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

Jak widać w przykładzie zamiast dwa zakresy mniejszych, zdefiniowane w przestrzeni adresowej sieci wirtualnej platformy Azure, mamy jeden większym zakresie, który obejmuje 4096 adresów IP. Duże definicję przestrzeni adresowej spowoduje, że niektóre zamiast duże zakresy pozostanie nieużywana. Ponieważ propagację trasy protokołu BGP są używane wartości przestrzeni adresów sieci wirtualnej, nieużywane zakresy w środowisku lokalnym lub w innym miejscu w sieci, może to spowodować problemów z routingiem. 

Dlatego zaleca się utrzymywanie przestrzeni adresowej ściśle powiązana z przestrzeni adresowej podsieci rzeczywiste, którego używasz. Jeśli to konieczne, bez powodowania przestoju w sieci wirtualnej, można dodać nowe wartości przestrzeni adresów później.
 
> [!IMPORTANT] 
> Wszystkie zakresy adresów IP w ER P2P, puli adresów IP serwerów i przestrzeni adresowej sieci wirtualnej platformy Azure muszą **nie** pokrywają się ze sobą lub z dowolnego zakresu, który jest używany w sieci. Każdy musi być kolumną dyskretną. Pokaż dwóch poprzednich grafiki, nie można je również inne zakresu podsieci. Jeśli wystąpią nakładania się między zakresami, sieci wirtualnej platformy Azure nie może nawiązywać połączenie z obwodem usługi ExpressRoute.

## <a name="next-steps-after-address-ranges-have-been-defined"></a>Następne kroki po zakresów adresów zostały zdefiniowane.
Po zdefiniowaniu zakresów adresów IP należy wykonać następujące czynności:

1. Przedstawia zakresy adresów IP w przestrzeni adresowej sieci wirtualnej platformy Azure, ER P2P łączności i serwera puli zakresu adresów IP oraz innych danych, który został wystawiony na początku dokumentu. W tym momencie można również uruchomić do tworzenia sieci wirtualnej i podsieci maszyny Wirtualnej. 
2. Obwód usługi ExpressRoute jest tworzony przez firmę Microsoft, między subskrypcją platformy Azure i sygnaturę dużych wystąpień HANA.
3. Sieci dzierżawcy powstaje w sygnaturze duże wystąpienie przez firmę Microsoft.
4. Microsoft konfiguruje sieć platformie SAP HANA w infrastrukturze platformy Azure (duże wystąpienia), aby zaakceptować adresów IP, który komunikuje się z dużymi wystąpieniami platformy HANA przestrzeni adresowej sieci wirtualnej platformy Azure.
5. W zależności od określonego środowiska SAP HANA na Azure jednostki SKU (duże wystąpienia), którego zakupiono Microsoft przypisuje Jednostka obliczeniowa w sieci dzierżawcy, przydziela i instaluje magazynu i instaluje system operacyjny (SUSE lub Red Hat Linux). Adresy IP dla tych jednostek pochodzą spoza zakresu adresów puli adresów IP serwerów, przesłane do firmy Microsoft.

Na końcu procesu wdrażania firma Microsoft dostarcza następujące dane dla użytkownika:
- Informacje potrzebne do łączenia z Twojej sieci wirtualnych platformy Azure z obwodem usługi ExpressRoute, który nawiązuje połączenie sieci wirtualnych platformy Azure do dużych wystąpień HANA:
     - Klucze autoryzacji
     - PeerID usługi ExpressRoute
- Dane do uzyskiwania dostępu do dużych wystąpień HANA po ustanowieniu obwodu usługi ExpressRoute i sieci wirtualnej platformy Azure.

Możesz również znaleźć sekwencji nawiązywania połączenia z dużymi wystąpieniami platformy HANA w dokumencie [platformy SAP HANA na platformie Azure (duże wystąpienia) konfiguracji](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/). Wiele z następujących czynności przedstawiono przykład wdrożenia w tym dokumencie. 

**Następne kroki**

- Zapoznaj się [łączenie sieci wirtualnej usługi expressroute platformy HANA duże wystąpienie](hana-connect-vnet-express-route.md).
