---
title: Ustawienia łączności z maszyn wirtualnych do platformy SAP HANA na platformie Azure (duże wystąpienia) | Dokumentacja firmy Microsoft
description: Ustawienia łączności z maszyn wirtualnych używających platformy SAP HANA na platformie Azure (duże wystąpienia).
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
ms.openlocfilehash: f0e09e6dfce5d0ede525f461193866219b7f9429
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44164757"
---
# <a name="connecting-azure-vms-to-hana-large-instances"></a>Łączenie maszyn wirtualnych platformy Azure do dużych wystąpień HANA

Jak już wspomniano w [platformy SAP HANA (duże wystąpienia) — omówienie i architektura na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) podstawowe wdrożenie dużych wystąpień HANA z warstwą aplikacji SAP w wygląda platformy Azure, takich jak:

![Sieci wirtualnej platformy Azure, połączone platformy SAP HANA na platformie Azure (duże wystąpienia) i w środowisku lokalnym](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

Wyszukiwanie bliżej stronie sieci wirtualnej platformy Azure, Zdajemy sobie sprawę, potrzeby:

- Definicja sieci wirtualnej platformy Azure, która będzie służyć do wdrażania maszyn wirtualnych w warstwie aplikacji SAP w.
- Czy automatycznie oznacza, że domyślna podsieć w sieci wirtualnej platformy Azure jest zdefiniowany, jest używana do wdrażania maszyn wirtualnych do.
- Sieci wirtualnej platformy Azure, który jest tworzony, musi mieć co najmniej jedną podsieć maszyny Wirtualnej i jedną podsieć bramy usługi ExpressRoute. Te podsieci powinien być przypisany zakresów adresów IP, jak określono i opisano w poniższych sekcjach.

Dlatego Przyjrzyjmy się nieco bliżej do tworzenia sieci wirtualnej platformy Azure dla dużych wystąpień HANA

## <a name="creating-the-azure-vnet-for-hana-large-instances"></a>Tworzenie sieci wirtualnej platformy Azure dla dużych wystąpień HANA

>[!Note]
>Należy utworzyć sieć wirtualną platformy Azure dla dużych wystąpień HANA przy użyciu modelu wdrażania usługi Azure Resource Manager. Starszy model wdrażania platformy Azure, powszechnie znane jako Klasyczny model wdrażania, nie jest obsługiwana za pomocą rozwiązania dużych wystąpień HANA.

Sieć wirtualną można utworzyć przy użyciu witryny Azure portal, programu PowerShell, szablon platformy Azure lub interfejsu wiersza polecenia platformy Azure (zobacz [Utwórz sieć wirtualną przy użyciu witryny Azure portal](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)). W poniższym przykładzie spojrzymy w sieci wirtualnej utworzonymi za pomocą witryny Azure portal.

Jeśli spojrzymy na definicje siecią wirtualną platformy Azure za pośrednictwem witryny Azure portal, Przyjrzyjmy się niektórym definicje oraz jak te odnoszą się do mamy listę zakresów adresów z innym adresem IP. Jak możemy rozmawiają o **przestrzeni adresowej**, mamy na myśli przestrzeni adresowej sieci wirtualnej platformy Azure może używać. Ta przestrzeń adresowa jest również odpowiedni zakres adresów sieci wirtualnej używa propagację trasy protokołu BGP. To **przestrzeni adresowej** są widoczne w tym miejscu:

![Adres miejsca z siecią wirtualną platformy Azure wyświetlane w witrynie Azure portal](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

W przypadku kolejnych kroków z 10.16.0.0/16 siecią wirtualną platformy Azure podano dość duży i szeroki zakres adresów IP do użycia. Oznacza, że wszystkie zakresy adresów IP kolejne podsieci w tej sieci wirtualnej może mieć ich zakresy w tej przestrzeni adresowej. Zazwyczaj nie zalecamy zakresu duża dla jednej sieci wirtualnej na platformie Azure. Jednak wprowadzenie nawet o krok dalej, sprawdźmy, z podsieciami zdefiniowanymi w sieci wirtualnej platformy Azure:

![Usługa Azure podsieci sieci wirtualnej i ich zakresy adresów IP](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

Jak widać, przyjrzymy się sieci wirtualnej przy użyciu pierwszej podsieci maszyny Wirtualnej (w tym miejscu o nazwie "default") i w podsieci o nazwie "GatewaySubnet".
W poniższej sekcji nazywamy zakres adresów IP podsieci, która została wywołana "default" w grafice jako **zakresu adresów IP podsieci maszyny Wirtualnej platformy Azure**. W poniższych sekcjach nazywamy zakresu adresów IP w podsieci bramy jako **zakres adresów IP podsieci bramy sieci wirtualnej**. 

W przypadku dowodzą grafiki dwa powyżej, zobaczysz, że **przestrzeni adresowej sieci wirtualnej** obejmuje zarówno **zakresu adresów IP podsieci maszyny Wirtualnej platformy Azure** i **zakres adresów IP podsieci bramy sieci wirtualnej**. 

W innych przypadkach, w których trzeba zachować lub być określone za pomocą zakresów adresów IP, można ograniczyć **przestrzeni adresowej sieci wirtualnej** do określonych zakresów, używany przez każdą z podsieci sieci wirtualnej. W takim przypadku można zdefiniować **przestrzeni adresowej sieci wirtualnej** sieci wirtualnej określonej jako wiele zakresów, jak pokazano poniżej:

![Usługa Azure przestrzeni adresowej sieci wirtualnej z dwóch miejsc do magazynowania](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

W tym przypadku **przestrzeni adresowej sieci wirtualnej** ma dwa miejsca do magazynowania, które są zdefiniowane. Te dwie spacje są takie same jak zakresy adresów IP, które są zdefiniowane dla **zakresu adresów IP podsieci maszyny Wirtualnej platformy Azure** i **zakres adresów IP podsieci bramy sieci wirtualnej.**

Standard nazewnictwa, które można użyć dla tych podsieci dzierżawy (podsieci maszyny Wirtualnej). Jednak **musi zawsze istnieć jeden i tylko jeden podsieć bramy dla każdej sieci wirtualnej** łączący się z platformą SAP HANA na obwód usługi ExpressRoute platformy Azure (duże wystąpienia). I **tę podsieć bramy zawsze musi mieć nazwę "GatewaySubnet"** zapewnienie odpowiedniego ustawienia bramy usługi ExpressRoute.

> [!WARNING] 
> Koniecznie się, że podsieć bramy zawsze ma nazwę "GatewaySubnet".

Wiele podsieci maszyny Wirtualnej mogą być używane, nawet przy użyciu zakresów adresów niesąsiadujące. Ale jak już wspomniano, te zakresy adresów muszą być objęte **przestrzeni adresowej sieci wirtualnej** sieci wirtualnej w zagregowanej formie lub na liście dokładnie zakresy adresów podsieci maszyny Wirtualnej i podsieci bramy.

Podsumowanie ważna informacja dotycząca siecią wirtualną platformy Azure łączącej się z dużymi wystąpieniami platformy HANA:

- Należy przesłać do firmy Microsoft **przestrzeni adresowej sieci wirtualnej** podczas przeprowadzania początkowe wdrożenie dużych wystąpień HANA. 
- **Przestrzeni adresowej sieci wirtualnej** może być jedną większym zakresie uwzględniającą zakres zakresy adresów IP podsieci maszyny Wirtualnej platformy Azure i zakres adresów IP podsieci bramy sieci wirtualnej.
- Lub możesz przesłać jako **przestrzeni adresowej sieci wirtualnej** wiele zakresów, które obejmują innym adresem IP adresów zakresów zakresy adresów IP podsieci maszyny Wirtualnej i zakres adresów IP podsieci bramy sieci wirtualnej.
- Zdefiniowane **przestrzeni adresowej sieci wirtualnej** jest używane propagacji routingu BGP.
- Nazwa podsieci bramy musi być: **"Gatewaysubent".**
- **Przestrzeni adresowej sieci wirtualnej** jest używany jako filtr na stronie dużych wystąpień HANA Zezwalaj lub nie zezwalaj na ruch z jednostkami dużych wystąpień HANA na platformie Azure. Jeśli informacje o routingu BGP z siecią wirtualną platformy Azure i zakresy adresów IP, które są skonfigurowane w celu filtrowania po stronie dużych wystąpień HANA są zgodne, mogą wystąpić problemy z łącznością.
- Dostępne są niektóre szczegółowe informacje o podsieci bramy, które są omówione w dalszych szczegółów w sekcji "Nawiązywanie połączenia z siecią wirtualną usługi ExpressRoute platformy HANA duże wystąpienia"



## <a name="different-ip-address-ranges-to-be-defined"></a>Do zdefiniowania różnych zakresów adresów IP 

Już wdrożyliśmy niektóre zakresy adresów IP niezbędnych do wdrożenia dużych wystąpień HANA we wcześniejszych sekcjach. Jednak istnieją pewne więcej zakresów adresów IP, które są ważne. Przejdźmy przez kilka dalszych szczegółów. Następujące adresy IP, których nie wszystkie muszą być przesyłane do firmy Microsoft muszą być zdefiniowane przed wysłaniem żądania do początkowego wdrożenia:

- **Przestrzeń adresowa sieci wirtualnej:** już wprowadzone wcześniej, jest albo są zakresy przypisano adres IP (lub planujesz przypisać) do parametru przestrzeni adresów w łączenie sieci wirtualnych platformy Azure (VNet), ze środowiskiem duże wystąpienie SAP HANA. Zaleca się, że ten parametr przestrzeni adresowej jest wartością wielowierszowego, składająca się z zakresy podsieci maszyny Wirtualnej platformy Azure i zakres podsieci bramy platformy Azure, jak pokazano w grafiki wcześniej. W środowisku lokalnym lub zakres adresów puli adresów IP serwerów lub ER P2P nie może nakładać tego zakresu. Jak uzyskać to lub te zakresy adresów IP? Twój dostawca zespołu lub usługa sieci firmowej powinien zapewniać jednego lub wielu liczbą zakresy adresów IP, który jest lub nie są używane w Twojej sieci. Przykład: Jeśli podsieci maszyny Wirtualnej platformy Azure (patrz wcześniej) jest 10.0.1.0/24 i podsieć bramy platformy Azure (patrz niżej) jest 10.0.2.0/28, następnie przestrzeń adresowa sieci wirtualnej platformy Azure jest zalecane jako dwa wiersze; 10.0.1.0/24 i 10.0.2.0/28. Mimo że można agregować wartości przestrzeni adresowej, zalecane jest dopasowywanie ich do zakresów adresów podsieci w celu uniknięcia przypadkowego ponownemu Nieużywane zakresy adresów IP w obrębie większej przestrzeni adresowych w przyszłości innym miejscu w sieci. **Przestrzeń adresowa sieci Wirtualnej jest zakres adresów IP, która musi być przesłane do firmy Microsoft, podczas pytania o początkowe wdrożenie**

- **Usługa Azure zakresu adresów IP podsieci maszyny Wirtualnej:** ten zakres adresów IP, zgodnie z opisem wcześniej już to ten, który został przypisany (lub planujesz przypisać) do parametru podsieci sieci wirtualnej platformy Azure w sieci Wirtualnej platformy Azure, łączenie ze środowiskiem duże wystąpienie SAP HANA. Ten zakres adresów IP jest używany do przypisywania adresów IP do maszyn wirtualnych platformy Azure. Adresy IP poza tym zakresem mogą łączyć się na serwerach programu duże wystąpienie SAP HANA. Jeśli to konieczne, wiele podsieci maszyny Wirtualnej platformy Azure mogą być używane. A/24 blok CIDR jest zalecane przez firmę Microsoft dla każdej podsieci maszyny Wirtualnej platformy Azure. Ten zakres adresów musi należeć do wartości używanych w przestrzeni adresowej sieci wirtualnej platformy Azure. Jak uzyskać ten zakres adresów IP? Twój dostawca zespołu lub usługa sieci firmowej powinien zapewniać zakres adresów IP, który nie jest obecnie używany w Twojej sieci.

- **Zakres adresów IP podsieci bramy sieci wirtualnej:** w zależności od funkcji, o której planujesz używać będzie zalecany rozmiar:
   - Bramy usługi ExpressRoute z największą wydajnością: / 26 blok adresów - wymagane dla klasy jednostek SKU typu II
   - Współistnienie przy użyciu sieci VPN i usługi ExpressRoute za pomocą bramy usługi ExpressRoute o wysokiej wydajności (lub mniejsze): / 27 blok adresów
   - Innych sytuacjach: / 28 bloku adresu. Ten zakres adresów musi należeć do wartości używanych w wartości "Przestrzeni adresowej sieci wirtualnej". Ten zakres adresów musi należeć do wartości używanych w wartości przestrzeni adresowej sieci wirtualnej platformy Azure, które należy przesłać do firmy Microsoft. Jak uzyskać ten zakres adresów IP? Twój dostawca zespołu lub usługa sieci firmowej powinien zapewniać zakres adresów IP, który nie jest obecnie używany w Twojej sieci. 

- **Zakres dla łączności ER P2P adresów:** ten zakres to zakres adresów IP dla połączenia P2P SAP HANA duże wystąpienie usługi ExpressRoute (ER). Ten zakres adresów IP musi mieć wartość/29 zakres adresów CIDR IP. Ten zakres nie może nakładać przy użyciu lokalnych lub innych adresów IP platformy Azure zakresy adresów. Ten zakres adresów IP jest używana do skonfigurowania łączności ER, z bramy usługi ExpressRoute sieci wirtualnej platformy Azure do serwerów duże wystąpienie SAP HANA. Jak uzyskać ten zakres adresów IP? Twój dostawca zespołu lub usługa sieci firmowej powinien zapewniać zakres adresów IP, który nie jest obecnie używany w Twojej sieci. **Ten zakres to zakres adresów IP, która musi być przesłane do firmy Microsoft, podczas pytania o początkowe wdrożenie**
  
- **Zakres adresów puli adresów IP serwera:** tego zakresu adresów IP jest używany do przypisywania pojedynczy adres IP do serwerów dużych wystąpień HANA. Rozmiar podsieci zalecane jest prefiksie/24 CIDR block — ale jeśli konieczne może być mniejszy do minimum związanych z udostępnianiem 64 adresów IP. Z tego zakresu pierwsze 30 adresy IP są zarezerwowane do użytku przez firmę Microsoft. Upewnij się, że ten fakt jest rozliczane przy wyborze rozmiarem zakresu. Ten zakres nie może nakładać przy użyciu lokalnych lub innych adresów IP platformy Azure adresy. Jak uzyskać ten zakres adresów IP? Twój dostawca zespołu lub usługa sieci firmowej powinien zapewniać zakres adresów IP, który nie jest obecnie używany w Twojej sieci. Prefiksie/24 unikatowy CIDR (zalecane) blokuje służący do przypisywania adresów IP określonych, potrzebnych dla rozwiązania SAP HANA na platformie Azure (duże wystąpienia). **Ten zakres to zakres adresów IP, która musi być przesłane do firmy Microsoft, podczas pytania o początkowe wdrożenie**
 
Chociaż należy zdefiniować i Planowanie zakresów adresów IP powyżej, nie wszystkie ich muszą zostać przesłane do firmy Microsoft. Aby podsumować zapotrzebowanie powyżej, zakresy adresów IP, które są wymagane, aby nazwać do firmy Microsoft są:

- Przestrzenie adres sieci wirtualnej platformy Azure
- Zakres adresów dla łączności ER P2P
- Zakres adresów puli adresów IP serwera

Dodawanie dodatkowych sieci wirtualnych, które muszą połączyć się z dużymi wystąpieniami platformy HANA, wymaga przesyłania przestrzeni adresowej nowej sieci wirtualnej platformy Azure dodajesz do firmy Microsoft. 

Oto przykład różnych zakresów i niektóre zakresy przykład potrzebnych do konfigurowania i ostatecznie przesyłanie do firmy Microsoft. Jak widać, wartość przestrzeni adresowej sieci wirtualnej platformy Azure nie jest agregowany w pierwszym przykładzie, ale jest zdefiniowana z zakresów pierwszy zakresu adresów IP podsieci maszyny Wirtualnej platformy Azure i zakres adresów IP podsieci bramy sieci wirtualnej. Za pomocą wielu podsieci maszyny Wirtualnej w sieci wirtualnej platformy Azure będzie działać przez skonfigurowanie w związku z tym przesyłania dodatkowych adresów IP zakresy adresów i o dodatkowe podsieci maszyny Wirtualnej jako część przestrzeni adresowej sieci wirtualnej platformy Azure.

![Zakresy adresów IP wymagane na platformie SAP HANA na podstawowe wdrożenie na platformie Azure (duże wystąpienia)](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

Istnieje również możliwość agregowania danych, który można przesłać do firmy Microsoft. W takiej sytuacji przestrzeni adresowej sieci wirtualnej platformy Azure będzie zawierać tylko jedną spację. Za pomocą zakresów adresów IP używanych w przykładzie wcześniej. Ta zagregowane przestrzeń adresowa sieci wirtualnej może wyglądać następująco:

![Druga możliwość zakresów adresów IP wymagane na platformie SAP HANA na podstawowe wdrożenie na platformie Azure (duże wystąpienia)](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

Jak pokazano powyżej, a nie dwa zakresy mniejszych, zdefiniowane w przestrzeni adresowej sieci wirtualnej platformy Azure, mamy jeden większym zakresie, który obejmuje 4096 adresów IP. Duże definicję przestrzeni adresowej spowoduje, że niektóre zamiast duże zakresy pozostanie nieużywana. Ponieważ propagację trasy protokołu BGP są używane wartości przestrzeni adresowej sieci wirtualnej, nieużywane zakresy w środowisku lokalnym lub w innym miejscu w sieci, może to spowodować problemów z routingiem. Dlatego zalecane jest, aby zachować przestrzeni adresowej ściśle powiązana z przestrzeni adresowej podsieci rzeczywistego, które są używane. Jeśli to konieczne, bez powodowania przestoju w sieci wirtualnej, można dodać nowe wartości przestrzeń adresową później.
 
> [!IMPORTANT] 
> Każdy adres IP przestrzeni adresowej sieci wirtualnej platformy Azure w zakresie ER-P2P, puli adresów IP serwerów, należy **nie** wzajemnego nakładania się lub używane inne zakresu, gdzie indziej w sieci; każdy musi być kolumną dyskretną, i jako dwa grafiki wcześniej show, może nie być podsieć innych zakresu. Jeśli wystąpią nakładania się między zakresami, sieci wirtualnej platformy Azure nie może połączyć z obwodem usługi ExpressRoute.

## <a name="next-steps-after-address-ranges-have-been-defined"></a>Następne kroki po zakresów adresów zostały zdefiniowane.
Po zdefiniowaniu zakresów adresów IP należy wykonać następujące działania:

1. Przedstawia zakresy adresów IP dla przestrzeni adresowej sieci wirtualnej platformy Azure, ER P2P łączności i zakres adresów puli adresów IP serwera, wraz z innymi danymi, wymienionego na początku dokumentu. W tym momencie możesz również rozpocząć tworzenie sieci wirtualnej i podsieci maszyny Wirtualnej. 
2. Obwód usługi Expressroute jest tworzony przez firmę Microsoft między subskrypcją platformy Azure i sygnaturę dużych wystąpień HANA.
3. Sieci dzierżawcy powstaje w sygnaturze duże wystąpienie przez firmę Microsoft.
4. Microsoft konfiguruje sieć platformie SAP HANA w infrastrukturze platformy Azure (duże wystąpienia), aby zaakceptować adresów IP platformy Azure sieci wirtualnej przestrzeni adresowej komunikujący się przy użyciu dużych wystąpień HANA.
5. W zależności od określonego środowiska SAP HANA na platformy Azure (duże wystąpienia) jednostek SKU zakupione firma Microsoft przypisuje Jednostka obliczeniowa w sieci dzierżawcy, Przydziel instalacji magazynu i zainstaluj system operacyjny (SUSE lub Red Hat Linux). Adresy IP dla tych jednostek pochodzą spoza adresów puli adresów IP serwerów zakresu przesłane do firmy Microsoft.

Na końcu procesu wdrażania firma Microsoft dostarcza następujące dane dla użytkownika:
- Informacje wymagane do nawiązania swoje sieciach wirtualnych platformy Azure z obwodem usługi ExpressRoute, który nawiązuje połączenie sieci wirtualnych platformy Azure do dużych wystąpień HANA:
     - Klucze autoryzacji
     - PeerID usługi ExpressRoute
- Dane do dostępu do dużych wystąpień HANA po ustanowieniu obwodu usługi ExpressRoute i sieci wirtualnej platformy Azure.

Możesz również znaleźć sekwencji nawiązywania połączenia z dużymi wystąpieniami platformy HANA w dokumencie [konfiguracja typu End to End duże wystąpienia SAP HANA](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/). Wiele z następujących czynności przedstawiono przykład wdrożenia w tym dokumencie. 

**Następne kroki**

- Zapoznaj się [procesu łączenia sieci z usługą ExpressRoute dużych wystąpień HANA](hana-connect-vnet-express-route.md).