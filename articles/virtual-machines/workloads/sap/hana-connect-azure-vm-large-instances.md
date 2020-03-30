---
title: Konfiguracja łączności z maszyn wirtualnych do SAP HANA na platformie Azure (duże wystąpienia) | Dokumenty firmy Microsoft
description: Konfiguracja łączności z maszyn wirtualnych do korzystania z sap HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fb6f88fbfcbd539603e435b11661c428d54f3c34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74224725"
---
# <a name="connecting-azure-vms-to-hana-large-instances"></a>Łączenie maszyn wirtualnych platformy Azure z funkcją HANA — duże wystąpienia

Artykuł [Co to jest SAP HANA na platformie Azure (duże wystąpienia)?](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) wspomina, że minimalne wdrożenie dużych wystąpień HANA z warstwą aplikacji SAP na platformie Azure wygląda następująco:

![Usługa Wirtualna platformy Azure połączona z usługą SAP HANA na platformie Azure (duże wystąpienia) i lokalnie](./media/hana-overview-architecture/image1-architecture.png)

Przyglądanie się bliżej stronie sieci wirtualnej platformy Azure, istnieje potrzeba:

- Definicja sieci wirtualnej platformy Azure, w której zamierzasz wdrożyć maszyny wirtualne warstwy aplikacji SAP.
- Definicja domyślnej podsieci w sieci wirtualnej platformy Azure, która jest naprawdę tą, w której są wdrażane maszyny wirtualne.
- Utworzona sieć wirtualna platformy Azure musi mieć co najmniej jedną podsieć maszyn wirtualną i jedną podsieć bramy sieci wirtualnej usługi Azure ExpressRoute. Podsieciom tym należy przypisać zakresy adresów IP, jak określono i omówiono w poniższych sekcjach.


## <a name="create-the-azure-virtual-network-for-hana-large-instances"></a>Tworzenie sieci wirtualnej platformy Azure dla dużych wystąpień HANA

>[!Note]
>Sieć wirtualna platformy Azure dla dużych wystąpień HANA musi zostać utworzona przy użyciu modelu wdrażania usługi Azure Resource Manager. Starszy model wdrażania platformy Azure, powszechnie znany jako klasyczny model wdrażania, nie jest obsługiwany przez rozwiązanie dużych wystąpień HANA.

Do utworzenia sieci wirtualnej można użyć witryny Azure portal, powershell, szablonu platformy Azure lub interfejsu wiersza polecenia platformy Azure. (Aby uzyskać więcej informacji, zobacz [Tworzenie sieci wirtualnej przy użyciu witryny Azure Portal).](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network) W poniższym przykładzie przyjrzymy się sieci wirtualnej, która jest tworzona przy użyciu witryny Azure portal.

Podczas odwoływania się do **przestrzeni adresowej** w tej dokumentacji do przestrzeni adresowej, której może używać sieć wirtualna platformy Azure. Ta przestrzeń adresowa jest również zakresem adresów używanym przez sieć wirtualną do propagacji marszruty BGP. Ta **przestrzeń adresowa** można zobaczyć tutaj:

![Przestrzeń adresowa sieci wirtualnej platformy Azure wyświetlana w witrynie Azure portal](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

W poprzednim przykładzie z 10.16.0.0/16 sieci wirtualnej platformy Azure otrzymał dość duży i szeroki zakres adresów IP do użycia. W związku z tym wszystkie zakresy adresów IP kolejnych podsieci w tej sieci wirtualnej mogą mieć swoje zakresy w tej przestrzeni adresowej. Zwykle nie zaleca się tak dużego zakresu adresów dla pojedynczej sieci wirtualnej na platformie Azure. Ale przyjrzyjmy się podsieciom, które są zdefiniowane w sieci wirtualnej platformy Azure:

![Podsieci sieci wirtualnej platformy Azure i ich zakresy adresów IP](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

Patrzymy na sieć wirtualną z pierwszą podsiecią maszyn wirtualną (tutaj nazywaną "domyślną") i podsiecią o nazwie "GatewaySubnet".

W dwóch poprzednich grafice **przestrzeń adresowa sieci wirtualnej** obejmuje zarówno **zakres adresów IP podsieci maszyny Wirtualnej platformy Azure,** jak i zakres bramy sieci wirtualnej.

**Przestrzeń adresowa sieci wirtualnej** można ograniczyć do określonych zakresów używanych przez każdą podsieć. Można również zdefiniować **przestrzeń adresową sieci wirtualnej** sieci wirtualnej jako wiele określonych zakresów, jak pokazano poniżej:

![Przestrzeń adresowa sieci wirtualnej platformy Azure z dwoma przestrzeniami](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

W takim przypadku **przestrzeń adresowa sieci wirtualnej** ma zdefiniowane dwie przestrzenie. Są one takie same jak zakresy adresów IP, które są zdefiniowane dla zakresu adresów IP podsieci maszyny Wirtualnej platformy Azure i bramy sieci wirtualnej. 

Można użyć dowolnego standardu nazewnictwa, który ci się podoba dla tych podsieci dzierżawy (podsieci maszyn wirtualnych). Jednak **zawsze musi istnieć jedna i tylko jedna podsieć bramy dla każdej sieci wirtualnej,** która łączy się z usługą SAP HANA na platformie Azure (duże wystąpienia) obwód usługi ExpressRoute. **Ta podsieć bramy musi mieć nazwę "GatewaySubnet",** aby upewnić się, że brama usługi ExpressRoute jest prawidłowo umieszczona.

> [!WARNING] 
> Bardzo ważne jest, aby podsieć bramy zawsze była nazywana "GatewaySubnet".

Można użyć wielu podsieci maszyn wirtualnych i niesąsiadujących zakresów adresów. Te zakresy adresów muszą być objęte **przestrzenią adresową sieci wirtualnej** sieci wirtualnej. Mogą być w formie zagregowane. Mogą one również znajdować się na liście dokładnych zakresów podsieci maszyny wirtualnej i podsieci bramy.

Poniżej znajduje się podsumowanie ważnych faktów dotyczących sieci wirtualnej platformy Azure, która łączy się z wystąpieniami dużymi hana:

- Podczas początkowego wdrażania dużych wystąpień hana należy przesłać **przestrzeń adresową sieci wirtualnej** do firmy Microsoft. 
- **Przestrzeń adresowa sieci wirtualnej** może być jednym większym zakresem, który obejmuje zakresy zarówno dla zakresu adresów IP podsieci maszyny Wirtualnej platformy Azure, jak i bramy sieci wirtualnej.
- Można też przesłać wiele zakresów obejmujących różne zakresy adresów IP zakresów adresów IP podsieci i zakres adresów IP bramy wirtualnej.
- Zdefiniowana **przestrzeń adresowa sieci wirtualnej** jest używana do propagacji routingu BGP.
- Nazwa podsieci bramy musi być: **"GatewaySubnet"**.
- Przestrzeń adresowa jest używana jako filtr po stronie dużych wystąpień HANA, aby zezwolić lub uniemożliwić ruch do jednostek dużych wystąpień HANA z platformy Azure. Informacje o routingu protokołu BGP sieci wirtualnej platformy Azure i zakresy adresów IP, które są skonfigurowane do filtrowania po stronie dużych wystąpień HANA, powinny być zgodne. W przeciwnym razie mogą wystąpić problemy z łącznością.
- Istnieją pewne szczegóły dotyczące podsieci bramy, które zostały omówione później, w sekcji **Łączenie sieci wirtualnej z hana dużych wystąpień expressroute.**



## <a name="different-ip-address-ranges-to-be-defined"></a>Różne zakresy adresów IP do zdefiniowania 

Niektóre zakresy adresów IP, które są niezbędne do wdrażania hana dużych wystąpień został już wprowadzony. Ale istnieje więcej zakresów adresów IP, które są również ważne. Nie wszystkie następujące zakresy adresów IP muszą zostać przesłane do firmy Microsoft. Jednak należy je zdefiniować przed wysłaniem żądania do wdrożenia początkowego:

- **Przestrzeń adresowa sieci wirtualnej:** **Przestrzeń adresowa sieci wirtualnej** to zakresy adresów IP przypisane do parametru przestrzeni adresowej w sieciach wirtualnych platformy Azure. Sieci te łączą się ze środowiskiem dużych wystąpień SAP HANA. Zaleca się, aby ten parametr przestrzeni adresowej był wartością wielowierszową. Powinien on składać się z zakresu podsieci maszyny Wirtualnej platformy Azure i zakresów podsieci bramy platformy Azure. Ten zakres podsieci został pokazany w poprzedniej grafice. Nie może pokrywać się z lokalnymi lub serwerową pulą adresów IP lub zakresami adresów ER-P2P. Jak uzyskać te zakresy adresów IP? Zespół lub dostawca usług sieci firmowej powinien podać jeden lub wiele zakresów adresów IP, które nie są używane w sieci. Na przykład podsieć maszyny Wirtualnej platformy Azure jest 10.0.1.0/24, a podsieci podsieci bramy platformy Azure jest 10.0.2.0/28.  Zaleca się, że przestrzeń adresowa sieci wirtualnej platformy Azure jest zdefiniowana jako: 10.0.1.0/24 i 10.0.2.0/28. Chociaż wartości przestrzeni adresowej można agregować, zaleca się dopasowywanie ich do zakresów podsieci. W ten sposób można przypadkowo uniknąć ponownego pożytku nieużywane zakresy adresów IP w większych przestrzeniach adresowych w innych miejscach sieci. **Przestrzeń adresowa sieci wirtualnej jest zakresem adresów IP. Należy go przesłać do firmy Microsoft, gdy poprosisz o wstępne wdrożenie**.
- **Zakres adresów IP podsieci maszyny Wirtualnej platformy Azure:** Ten zakres adresów IP jest ten, który można przypisać do parametru podsieci sieci wirtualnej platformy Azure. Ten parametr znajduje się w sieci wirtualnej platformy Azure i łączy się ze środowiskiem dużych wystąpień SAP HANA. Ten zakres adresów IP służy do przypisywania adresów IP do maszyn wirtualnych platformy Azure. Adresy IP poza tym zakresem mogą łączyć się z serwerami dużych wystąpień SAP HANA. W razie potrzeby można użyć wielu podsieci maszyny wirtualnej platformy Azure. Firma Microsoft zaleca /24 blok CIDR dla każdej podsieci maszyny wirtualnej platformy Azure. Ten zakres adresów musi być częścią wartości, które są używane w przestrzeni adresowej sieci wirtualnej platformy Azure. Jak uzyskać ten zakres adresów IP? Zespół lub dostawca usług sieci firmowej powinien podać zakres adresów IP, który nie jest używany w sieci.
- **Zakres adresów IP podsieci bramy sieci wirtualnej:** W zależności od funkcji, których zamierzasz używać, zalecany rozmiar to:
   - Ultrawydajna brama usługi ExpressRoute: /26 blok adresu — wymagane dla klasy typu II jednostek SKU.
   - Współistnienie z siecią VPN i programem ExpressRoute przy użyciu wysokiej wydajności bramy sieci wirtualnej usługi ExpressRoute (lub mniejszej): /27 bloku adresów.
   - Wszystkie inne sytuacje: /28 blok adresu. Ten zakres adresów musi być częścią wartości używanych w wartościach "przestrzeń adresowa sieci wirtualnej". Ten zakres adresów musi być częścią wartości, które są używane w wartości przestrzeni adresowej sieci wirtualnej platformy Azure, które są przesyłane do firmy Microsoft. Jak uzyskać ten zakres adresów IP? Zespół lub dostawca usług sieci firmowej powinien podać zakres adresów IP, który nie jest obecnie używany w sieci. 
- **Zakres adresów dla łączności ER-P2P:** Ten zakres jest zakresem IP połączenia P2P usługi SAP HANA Large Instance ExpressRoute (ER). Ten zakres adresów IP musi być zakresem adresów IP CIDR /29. Ten zakres NIE może pokrywać się z lokalnymi lub innymi zakresami adresów IP platformy Azure. Ten zakres adresów IP służy do konfigurowania łączności ER z wirtualnej bramy usługi ExpressRoute do serwerów dużych wystąpień SAP HANA. Jak uzyskać ten zakres adresów IP? Zespół lub dostawca usług sieci firmowej powinien podać zakres adresów IP, który nie jest obecnie używany w sieci. **Ten zakres jest zakresem adresów IP. Należy go przesłać do firmy Microsoft, gdy poprosisz o wstępne wdrożenie**.  
- **Zakres adresów puli adresów IP serwera:** Ten zakres adresów IP służy do przypisywania indywidualnego adresu IP do serwerów dużych wystąpień HANA. Zalecany rozmiar podsieci to blok /24 CIDR. W razie potrzeby może być mniejszy, z zaledwie 64 adresami IP. Z tego zakresu pierwsze 30 adresów IP jest zarezerwowanych do użytku przez firmę Microsoft. Upewnij się, że uwzględniasz ten fakt, wybierając rozmiar zakresu. Ten zakres NIE może pokrywać się z lokalnymi lub innymi adresami IP platformy Azure. Jak uzyskać ten zakres adresów IP? Zespół lub dostawca usług sieci firmowej powinien podać zakres adresów IP, który nie jest obecnie używany w sieci.  **Ten zakres jest zakresem adresów IP, który należy przesłać do firmy Microsoft z prośbą o wstępne wdrożenie**.

Opcjonalne zakresy adresów IP, które ostatecznie muszą zostać przesłane do firmy Microsoft:

- Jeśli zdecydujesz się użyć usługi [ExpressRoute Global Reach,](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) aby włączyć routing bezpośredni z lokalnych do jednostek dużych wystąpień HANA, musisz zarezerwować inny zakres adresów IP /29. Ten zakres nie może pokrywać się z żadnym z innych zakresów adresów IP zdefiniowanych wcześniej.
- Jeśli zdecydujesz się użyć [usługi ExpressRoute Global Reach,](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) aby włączyć routing bezpośredni z dzierżawy dużych wystąpień HANA w jednym regionie platformy Azure do innej dzierżawy dużych wystąpień HANA w innym regionie platformy Azure, musisz zarezerwować inny zakres adresów IP /29. Ten zakres nie może pokrywać się z żadnym z innych zakresów adresów IP zdefiniowanych wcześniej.

Aby uzyskać więcej informacji na temat globalnego zasięgu usługi ExpressRoute i użycia wokół dużych wystąpień HANA, sprawdź dokumenty:

- [Architektura sieci SAP HANA (Duże wystąpienia)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture)
- [Łączenie sieci wirtualnej z dużymi wystąpieniami HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route)
 
Należy zdefiniować i zaplanować zakresy adresów IP, które zostały opisane wcześniej. Nie trzeba jednak przesyłać wszystkich z nich do firmy Microsoft. Zakresy adresów IP, które są wymagane do nazwy firmy Microsoft są następujące:

- Przestrzeń adresowa sieci wirtualnej platformy Azure
- Zakres adresów dla łączności ER-P2P
- Zakres adresów puli adresów IP serwera

Jeśli dodasz dodatkowe sieci wirtualne, które muszą połączyć się z hana dużych wystąpień, należy przesłać nową przestrzeń adresową sieci wirtualnej platformy Azure, który dodajesz do firmy Microsoft. 

Oto przykład różnych zakresów i kilka przykładowych zakresów, które należy skonfigurować i ostatecznie dostarczyć firmie Microsoft. Wartość przestrzeni adresowej sieci wirtualnej platformy Azure nie jest agregowana w pierwszym przykładzie. Jednak jest zdefiniowany z zakresów pierwszego zakresu adresów IP podsieci platformy Azure i zakresu adresów IP bramy bramy sieci wirtualnej. 

Podczas konfigurowania i przesyłania dodatkowych zakresów adresów IP dodatkowych podsieci maszyn wirtualnych w ramach przestrzeni adresowej sieci wirtualnej można używać wielu podsieci maszyn wirtualnych w sieci wirtualnej platformy Azure.

![Zakresy adresów IP wymagane w sap HANA na platformie Azure (duże wystąpienia) minimalne wdrożenie](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

Grafika nie pokazuje dodatkowych zakresów adresów IP wymaganych do opcjonalnego użycia usługi ExpressRoute Global Reach.

Można również agregować dane przesyłane do firmy Microsoft. W takim przypadku przestrzeń adresowa sieci wirtualnej platformy Azure zawiera tylko jedno miejsce. Przy użyciu zakresów adresów IP z wcześniejszego przykładu zagregowana przestrzeń adresowa sieci wirtualnej może wyglądać następująco:

![Druga możliwość zakresu adresów IP wymaganych w sap HANA na platformie Azure (duże wystąpienia) minimalne wdrożenie](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

W przykładzie zamiast dwóch mniejszych zakresów, które zdefiniowały przestrzeń adresową sieci wirtualnej platformy Azure, mamy jeden większy zakres, który obejmuje 4096 adresów IP. Tak duża definicja przestrzeni adresowej pozostawia dość duże zakresy nieużywane. Ponieważ wartości przestrzeni adresowej sieci wirtualnej są używane do propagacji tras BGP, użycie nieużywanych zakresów lokalnie lub w innym miejscu w sieci może powodować problemy z routingiem. Grafika nie pokazuje dodatkowych zakresów adresów IP wymaganych do opcjonalnego użycia usługi ExpressRoute Global Reach.

Zaleca się, aby przestrzeń adresowa była ściśle wyrównana z rzeczywistą przestrzenią adresową podsieci, której używasz. W razie potrzeby, bez ponoszenia przestojów w sieci wirtualnej, zawsze można dodać nowe wartości przestrzeni adresowej później.
 
> [!IMPORTANT] 
> Każdy zakres adresów IP w ER-P2P, puli adresów IP serwera i przestrzeni adresowej sieci wirtualnej platformy Azure **NIE** może pokrywać się ze sobą lub z innym zakresem, który jest używany w sieci. Każdy musi być dyskretny. Jak pokazują dwie poprzednie grafiki, nie mogą one być również podsiecią innego zakresu. Jeśli występują nakładanie się między zakresami, sieć wirtualna platformy Azure może nie łączyć się z obwódem usługi ExpressRoute.

## <a name="next-steps-after-address-ranges-have-been-defined"></a>Kolejne kroki po zdefiniowaniu zakresów adresów
Po zdefiniowaniu zakresów adresów IP muszą się zdarzyć następujące rzeczy:

1. Prześlij zakresy adresów IP dla przestrzeni adresowej sieci wirtualnej platformy Azure, łączności ER-P2P i zakresu adresów puli adresów IP serwera wraz z innymi danymi, które zostały wymienione na początku dokumentu. W tym momencie można również rozpocząć tworzenie sieci wirtualnej i podsieci maszyny wirtualnej. 
2. Obwód usługi ExpressRoute jest tworzony przez firmę Microsoft między subskrypcją platformy Azure a sygnaturą dużego wystąpienia HANA.
3. Sieć dzierżawy jest tworzona na znaczek duże wystąpienie przez firmę Microsoft.
4. Firma Microsoft konfiguruje sieć w infrastrukturze SAP HANA na platformie Azure (duże wystąpienia), aby akceptować adresy IP z przestrzeni adresowej sieci wirtualnej platformy Azure, która komunikuje się z dużymi wystąpieniami HANA.
5. W zależności od określonej jednostki SKU platformy Azure (dużych wystąpień) firmy SAP, która została kupiona, firma Microsoft przypisuje jednostkę obliczeniową w sieci dzierżawy. Przydziela również i montuje pamięć masową i instaluje system operacyjny (SUSE lub Red Hat Linux). Adresy IP dla tych jednostek są wyjęte z zakresu adresów puli adresów serwera IP przesłanych do firmy Microsoft.

Po zakończeniu procesu wdrażania firma Microsoft dostarcza następujące dane:
- Informacje potrzebne do połączenia sieci wirtualnych platformy Azure z obwodem usługi ExpressRoute łączącym sieci wirtualne platformy Azure z dużymi wystąpieniami HANA:
     - Klucz(-y) autoryzacji
     - ExpressRoute PeerID
- Dane dostępu do dużych wystąpień HANA po ustanowieniu obwodu usługi ExpressRoute i sieci wirtualnej platformy Azure.

Sekwencję łączenia dużych wystąpień HANA można również znaleźć w dokumencie [SAP HANA na platformie Azure (Duże wystąpienia).](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/) Wiele z następujących kroków są wyświetlane w przykładowym wdrożeniu w tym dokumencie. 

## <a name="next-steps"></a>Następne kroki

- Zobacz [Łączenie sieci wirtualnej z usługą Hana Large Instance ExpressRoute](hana-connect-vnet-express-route.md).
