---
title: Infrastruktury i łączności z SAP HANA na platformie Azure (wystąpienia duże) | Dokumentacja firmy Microsoft
description: Konfigurowanie infrastruktury wymaga połączenia do użycia na platformie Azure (wystąpienia duże) SAP HANA.
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
ms.date: 06/04/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4c0f5d0c5ed3814495a68d7fd49d41cec521bbd7
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114562"
---
# <a name="sap-hana-large-instances-infrastructure-and-connectivity-on-azure"></a>Infrastruktura SAP HANA (duże wystąpień) i łączność na platformie Azure 

Definicje wyprzedzeniem przed przeczytaniem tego przewodnika. W [omówienie SAP HANA (duże wystąpień) i architektury na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) wprowadzono dwóch różnych klas jednostek HANA dużych wystąpienie:

- S72, S72m S144, S144m, S192, S192m i S192xm, który nazywamy "Typ klasy I" z jednostki SKU.
- S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm i S960m, który nazywamy "klasy typu II' SKU.

Specyfikatory klas będą na potrzeby całej dokumentacji wystąpienia dużych HANA ostatecznie odwoływać się do różnych funkcji i wymagań oparte na jednostki SKU HANA dużych wystąpienia.

Inne definicje, często używanych są:
- **Sygnatura wystąpienia dużych:** stos infrastruktury sprzętu SAP HANA TDI certyfikowane i do uruchomienia wystąpień SAP HANA w obrębie platformy Azure w wersji dedykowanej.
- **SAP HANA na platformie Azure (duże wystąpienia):** oficjalną nazwą języka dla oferty na platformie Azure uruchomić HANA wystąpień w SAP HANA TDI certyfikowanego sprzętu, które zostało wdrożone w dużych wystąpienia sygnatury w różnych regionach platformy Azure. Powiązane termin **wystąpienia dużych HANA** jest skrót SAP HANA na platformie Azure (wystąpienia duże) i jest powszechnie używany ten przewodnik wdrożenia technicznego.
 

Po zakupu SAP HANA na platformie Azure (wystąpienia duże) jest zakończona między użytkownikiem a zespół kont Microsoft enterprise, poniższe informacje jest wymagany przez firmę Microsoft do wdrażania HANA dużych jednostek wystąpienie:

- Nazwa klienta
- (W tym adres e-mail i numer telefonu) informacje dotyczące kontaktów biznesowych
- Skontaktuj się z pomocą informacje techniczne (w tym wiadomości e-mail adres i numer telefonu)
- Techniczne informacje kontaktowe sieci (w tym wiadomości e-mail adres i numer telefonu)
- Region wdrożenia usługi Azure (zachodnie stany USA, wschodnie stany USA, Australia Wschodnia, Australia Południowo-Wschodnia, Europa Zachodnia i Europa Północna, począwszy od 2017 lipca)
- Potwierdź SAP HANA na Azure (wystąpienia duże) jednostki SKU (Konfiguracja)
- Jak już szczegółowo w dokumencie omówienie i architektura HANA dużych wystąpień, dla każdego regionu Azure wdrażane na:
    - /29 zakres adresów IP w połączeniach ER P2P łączenia sieci wirtualnych platformy Azure z wystąpieniami dużych HANA
    - Prefiksie/24 blok CIDR używany dla HANA dużych wystąpień serwera puli adresów IP
- Używane w atrybucie przestrzeni adresowej sieci wirtualnej co sieć wirtualną Azure łączy do wystąpień dużych HANA wartości zakresu adresów IP
- Dane dla każdego wystąpienia dużych HANA systemu:
  - Żądany hostname - najlepiej z pełną nazwę domeny.
  - Żądany adres IP dla jednostki wystąpienia dużych HANA poza zakres adresów puli adresów IP serwera — należy pamiętać, że pierwsze 30 adresów IP, zakresu adresów puli adresów IP serwera są zarezerwowane do użytku wewnętrznego w dużych wystąpień HANA
  - Nazwa SAP HANA SID dla wystąpienia SAP HANA (wymagane do tworzenia woluminów potrzeby związane z SAP HANA dysku). Identyfikator SID HANA jest wymagane do tworzenia uprawnienia sidadm w woluminach systemu plików NFS, uzyskiwanie dołączonych do jednostki HANA dużych wystąpienia. Ponadto jest używany jako jeden ze składników nazwy woluminów dysków, które uzyskać zainstalowane. Jeśli chcesz uruchomić więcej niż jedno wystąpienie HANA w jednostce należy listy wielu HANA identyfikatorów SID. Każda z nich pobiera osobny zestaw woluminów przypisane.
  - Identyfikator grupy, z którą ma użytkownik sidadm w systemie operacyjnym Linux jest wymagane do utworzenia woluminów konieczne dysków związanych z SAP HANA. Instalacja SAP HANA zazwyczaj tworzy grupy sapsys z identyfikatorem grupy 1001. Użytkownik sidadm wchodzi w skład tej grupy
  - Identyfikator użytkownika, z którą ma użytkownik sidadm w systemie operacyjnym Linux jest wymagany do tworzenia woluminów konieczne dysku powiązane SAP HANA. Jeśli używasz wielu wystąpień HANA na jednostce, należy wyświetlić listę wszystkich <sid>adm użytkowników 
- Identyfikator subskrypcji platformy Azure dla subskrypcji platformy Azure, do których SAP HANA na Azure HANA mają być bezpośrednio połączony dużych wystąpień. Ten identyfikator subskrypcji odwołuje się do subskrypcji platformy Azure, który będzie obciążana HANA dużych wystąpienia jednostki.

Po podaniu informacji firmy Microsoft udostępnia SAP HANA na platformie Azure (wystąpienia duże) i zwróci informacje link do wystąpień dużych HANA sieciom wirtualnym platformy Azure i dostęp do jednostki HANA dużych wystąpienia.

## <a name="connecting-azure-vms-to-hana-large-instances"></a>Łączenie maszyny wirtualne platformy Azure z wystąpień dużych HANA

Jak już wspomniano w [omówienie SAP HANA (duże wystąpień) i architektury na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) minimalnego wdrożenia HANA dużych wystąpień z warstwą aplikacji SAP w Azure wygląda, takich jak:

![Połączony SAP HANA Azure (wystąpienia duże) i lokalnej sieci wirtualnej platformy Azure](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

Wyszukiwanie bliżej na stronie sieci wirtualnej Azure, Zdajemy sobie sprawę potrzebę:

- Definicja sieć wirtualną platformy Azure, który ma być używany do wdrażania maszyn wirtualnych w warstwie aplikacji SAP do.
- Czy automatycznie oznacza, że domyślna podsieci w sieci wirtualnej platformy Azure jest zdefiniowana który jest używany do wdrażania maszyn wirtualnych do.
- Sieci wirtualnej Azure, która jest tworzona musi mieć co najmniej jedną podsieć maszyny Wirtualnej i jedną podsieć bramy ExpressRoute. Te podsieci powinien być przypisany zakresów adresów IP, jak określono i opisanych w poniższych sekcjach.

Tak Oto nieco zbliżonej do tworzenia sieci wirtualnej platformy Azure dla wystąpień dużych HANA

### <a name="creating-the-azure-vnet-for-hana-large-instances"></a>Tworzenie sieci wirtualnej platformy Azure dla wystąpień dużych HANA

>[!Note]
>Sieć wirtualna Azure HANA dużych wystąpienia muszą być tworzone przy użyciu modelu wdrażania usługi Azure Resource Manager. Starszy model wdrożenia usługi Azure, powszechnie znane jako klasycznego modelu wdrażania, nie jest obsługiwana z rozwiązaniem HANA dużych wystąpienia.

Można utworzyć sieci wirtualnej przy użyciu portalu Azure, programu PowerShell, szablon Azure lub interfejsu wiersza polecenia Azure (zobacz [utworzyć sieć wirtualną przy użyciu portalu Azure](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)). W poniższym przykładzie opisano, w sieci wirtualnej została utworzona za pośrednictwem portalu Azure.

Jeśli szukamy do definicji sieć wirtualną platformy Azure za pośrednictwem portalu Azure, Oto do niektórych definicje i jak te dotyczą możemy liście zakresów adresów IP innego. Jak możemy mówimy więc o **przestrzeni adresowej**, możemy oznacza przestrzeni adresowej sieci wirtualnej platformy Azure może używać. Ta przestrzeń adresowa jest również zakres adresów sieci wirtualnej używa dla propagowania tras BGP. To **przestrzeni adresowej** są widoczne w tym miejscu:

![Adres miejsca Azure VNet wyświetlane w portalu Azure](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

W przypadku kolejnych z 10.16.0.0/16 sieć wirtualną Azure podano raczej duży i szeroki zakres adresów IP do użycia. Oznacza, że wszystkie zakresy adresów IP kolejne podsieci w tej sieci wirtualnej może mieć ich zakresów w ramach tej przestrzeni adresowej. Zwykle nie zalecamy zakres adresów dużych jednej sieci wirtualnej na platformie Azure. Jednak wprowadzenie kolejny krok, Oto na podsieci zdefiniowanej w sieci wirtualnej platformy Azure:

![Podsieci sieci wirtualnej platformy Azure i ich zakresy adresów IP](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

Jak widać, przyjrzymy się sieci wirtualnej z pierwszej podsieci maszyny Wirtualnej (w tym miejscu o nazwie "default") i podsieć o nazwie "GatewaySubnet".
W poniższej sekcji, zwane zakres adresów IP podsieci, w której wywołano "default" w grafiki jako **zakres adresów IP podsieci maszyny Wirtualnej Azure**. W poniższych sekcjach dotyczą możemy zakresu adresów IP podsieci bramy jako **zakres adresów IP podsieci bramy sieci wirtualnej**. 

W przypadku dowodzą grafiki dwa powyżej, zobacz który **przestrzeni adresowej sieci wirtualnej** obejmuje zarówno **zakres adresów IP podsieci maszyny Wirtualnej Azure** i **zakres adresów IP podsieci bramy sieci wirtualnej**. 

W pozostałych przypadkach konieczne do ochrony i przeznaczone z zakresy adresów IP można ograniczyć **przestrzeni adresowej sieci wirtualnej** sieci wirtualnej do określonych zakresów używany przez każdej podsieci. W takim przypadku można zdefiniować **przestrzeni adresowej sieci wirtualnej** z sieci wirtualnej określonych jako wiele zakresów, jak pokazano poniżej:

![Azure przestrzeni adresowej sieci wirtualnej z dwoma obszarami](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

W takim przypadku **przestrzeni adresowej sieci wirtualnej** ma dwa pola zdefiniowane. Te dwie spacje są takie same jak zakresy adresów IP, które są zdefiniowane dla **zakres adresów IP podsieci maszyny Wirtualnej Azure** i **zakres adresów IP podsieci bramy sieci wirtualnej.**

Można użyć dowolnego standard nazewnictwa, który chcesz dla tych podsieci dzierżawy (podsieci maszyny Wirtualnej). Jednak **musi zawsze występować jeden i tylko jeden podsieci bramy dla każdej sieci wirtualnej** łączące się SAP HANA obwodu usługi ExpressRoute Azure (wystąpienia duże). I **tej podsieci bramy muszą zawsze być o nazwie "GatewaySubnet"** do zapewnienia odpowiedniego ustawienia bramę usługi ExpressRoute.

> [!WARNING] 
> Jest bardzo istotne, czy podsieci bramy zawsze jest o nazwie "GatewaySubnet".

Wiele podsieci maszyny Wirtualnej mogą być używane, nawet przy użyciu zakresów adresów nieciągłych. Ale jak już wspomniano, tych zakresów adresów muszą być objęte **przestrzeni adresowej sieci wirtualnej** sieci wirtualnej w formie zagregowanej lub na liście dokładne zakresy podsieci maszyny Wirtualnej i podsieci bramy.

Podsumowanie ważne informacja dotycząca sieć wirtualną platformy Azure, łączącego się HANA dużych wystąpień:

- Należy przesłać do firmy Microsoft **przestrzeni adresowej sieci wirtualnej** podczas wykonywania początkowe wdrożenie HANA dużych wystąpień. 
- **Przestrzeni adresowej sieci wirtualnej** może być jeden zakres większy, które obejmuje zakres dla zakresów adresów IP podsieci maszyny Wirtualnej platformy Azure i zakres adresów IP podsieci bramy sieci wirtualnej.
- Lub można przesłać jako **przestrzeni adresowej sieci wirtualnej** wiele zakresów, które obejmują różne od adresów zakresów adresów IP podsieci maszyny Wirtualnej i zakres adresów IP podsieci bramy sieci wirtualnej.
- Zdefiniowanych **przestrzeni adresowej sieci wirtualnej** jest używane propagacji routingu BGP.
- Nazwa podsieci bramy musi być: **"GatewaySubnet".**
- **Przestrzeni adresowej sieci wirtualnej** służy jako filtr po stronie wystąpienia dużych HANA Zezwalaj lub nie zezwalaj na ruch z jednostkami HANA dużych wystąpienia z platformy Azure. Jeśli informacje routingu BGP sieci wirtualnej platformy Azure i zakresy adresów IP, które są skonfigurowane do filtrowania na stronie dużych wystąpienia HANA nie są zgodne, mogą wystąpić problemy z łącznością.
- Dostępne są niektóre szczegółowe informacje o podsieci bramy, które są omawiane dalszych szczegółów w sekcji "Łączenie sieci wirtualnej usługi expressroute HANA dużych wystąpienia"



### <a name="different-ip-address-ranges-to-be-defined"></a>Do zdefiniowania różnych zakresów adresów IP 

Już wprowadzone niektóre niezbędnych do wdrożenia HANA wystąpień dużych we wcześniejszych sekcjach zakresów adresów IP. Istnieją pewne więcej zakresów adresów IP, które są ważne. Przejdź przez niektóre dodatkowe szczegóły. Następujące adresy IP, których nie wszystkie muszą zostać przesłane do firmy Microsoft, trzeba zdefiniować, przed wysłaniem żądania początkowego rozmieszczania:

- **Przestrzeń adresowa sieci wirtualnej:** już wprowadzone wcześniej, jest lub mają parametru przestrzeni adresów łączenia sieci wirtualnych Azure (VNet) do środowiska programu SAP HANA dużych wystąpienia range(s) przypisano adres IP (lub planujesz jej przypisać). Zaleca się, że ten parametr przestrzeń adresowa jest wartość wielowierszowego składa się z zakresów podsieci maszyny Wirtualnej platformy Azure i zakresu podsieci bramy Azure, jak pokazano wcześniej grafiki. Ten zakres nie mogą się pokrywać z lokalnymi lub zakres adresów puli adresów IP serwera lub ER P2P. Jak uzyskać to lub tych zakresów adresów IP? Dostawca zespołu lub usługi sieci firmowej powinna zapewniać jednego lub wielu liczbą zakresów adresów IP, która jest lub nie są używane w sieci. Przykład: Jeśli podsieć maszyny Wirtualnej Azure (zobacz wcześniej) jest 10.0.1.0/24 i podsieć bramy Azure (patrz niżej) jest 10.0.2.0/28, następnie przestrzeń adresowa sieci wirtualnej Azure zaleca się dwa wiersze; 10.0.1.0/24 i 10.0.2.0/28. Mimo że można agregowane wartości przestrzeni adresowej, zalecane jest dopasowywanie ich do określonych zakresów podsieci Aby uniknąć przypadkowego ponownego użycia nieużywane zakresów adresów IP w większych przestrzeni adresów w przyszłości w innych miejscach w sieci. **Przestrzeń adresów sieci Wirtualnej jest zakres adresów IP, które muszą zostać przesłane do firmy Microsoft podczas pytania o początkowe wdrożenie**

- **Zakres adresów IP podsieci Azure VM:** ten zakres adresów IP, zgodnie z opisem wcześniej już jest jeden przypisano (lub planujesz jej przypisać) do parametru podsieci sieci wirtualnej platformy Azure w połączeniu ze środowiskiem programu SAP HANA dużych wystąpienia sieci wirtualnej platformy Azure. Ten zakres adresów IP jest używana do przypisywania adresów IP na maszynach wirtualnych platformy Azure. Adresy IP poza tym zakresem mogą łączyć serwery programu SAP HANA dużych wystąpienia. W razie potrzeby wiele podsieci maszyny Wirtualnej platformy Azure mogą być używane. A/24 blok CIDR jest zalecana przez firmę Microsoft dla każdej podsieci maszyny Wirtualnej Azure. Ten zakres adresów musi być częścią wartości używane do przestrzeni adresowej sieci wirtualnej platformy Azure. Jak uzyskać ten zakres adresów IP? Dostawca zespołu lub usługi sieci firmowej powinien zapewnić zakres adresów IP, który nie jest obecnie używany w sieci.

- **Zakres adresów IP podsieci bramy sieci wirtualnej:** w zależności od funkcji planujesz użyć, jest zalecany rozmiar:
   - Bramy z największą wydajnością ExpressRoute: / 26 blok adresów — wymagany dla typu II klasy jednostki SKU
   - Współistnienie z sieci VPN i ExpressRoute przy użyciu bramy ExpressRoute wysokiej wydajności (lub mniejsze): / 27 blok adresów
   - Innych sytuacjach: / 28 blok adresów. Ten zakres adresów musi być częścią wartości użyte w wartości "Przestrzeni adresowej sieci wirtualnej". Ten zakres adresów musi być częścią wartości użyte w wartości przestrzeni adresowej sieci wirtualnej Azure, które należy przesłać do firmy Microsoft. Jak uzyskać ten zakres adresów IP? Dostawca zespołu lub usługi sieci firmowej powinien zapewnić zakres adresów IP, który nie jest obecnie używany w sieci. 

- **Zakres łączności ER P2P adresów:** ten zakres jest zakres adresów IP połączenie P2P SAP HANA dużych wystąpienia usługi ExpressRoute (ER). Ten zakres adresów IP musi być /29 zakres adresów CIDR IP. Ten zakres musi nie pokrywają się z lokalnymi lub innymi IP platformy Azure zakresów adresów. Ten zakres adresów IP jest używana do konfigurowania łączności ER z bramę usługi ExpressRoute sieci wirtualnej Azure do serwerów wystąpienia dużych SAP HANA. Jak uzyskać ten zakres adresów IP? Dostawca zespołu lub usługi sieci firmowej powinien zapewnić zakres adresów IP, który nie jest obecnie używany w sieci. **Ten zakres jest zakres adresów IP, które muszą zostać przesłane do firmy Microsoft podczas pytania o początkowe wdrożenie**
  
- **Zakres puli adresów IP serwera:** ten zakres adresów IP jest używana do przypisywania pojedynczy adres IP HANA dużych wystąpienia serwerów. Rozmiar podsieci zalecane jest prefiksie/24 CIDR zablokować — ale jeśli potrzebne może być mniejszy do minimum dostarczać 64 adresy IP. Z tego zakresu pierwsze 30 adresy IP są zarezerwowane do użytku przez firmę Microsoft. Upewnij się, że ten fakt przypada na wybierając rozmiar zakresu. Ten zakres musi nie pokrywają się z lokalnymi lub innymi IP platformy Azure adresów. Jak uzyskać ten zakres adresów IP? Dostawca zespołu lub usługi sieci firmowej powinien zapewnić zakres adresów IP, który nie jest obecnie używany w sieci. Prefiksie/24 blok CIDR unikatowy (zalecane) służący do przypisywania adresów IP określonych, potrzebnego do SAP HANA na platformie Azure (wystąpienia duże). **Ten zakres jest zakres adresów IP, które muszą zostać przesłane do firmy Microsoft podczas pytania o początkowe wdrożenie**
 
Chociaż należy zdefiniować i Planowanie zakresów adresów IP powyżej, nie wszystkie ich muszą zostać przesłane do firmy Microsoft. Podsumowując powyższego, zakresy adresów IP, do nazwy do firmy Microsoft są wymagane są:

- Space(s) adresów sieci wirtualnej platformy Azure
- Zakres adresów dla łączności ER P2P
- Zakresu adresów puli adresów IP serwera

Dodawanie dodatkowych sieci wirtualnych, które są potrzebne do łączenia z wystąpieniami dużych HANA, wymaga przesyłania przestrzeni adresowej nowej sieci wirtualnej Azure jest dodawany do firmy Microsoft. 

Poniżej przedstawiono przykład różnych zakresów i niektóre zakresy przykład potrzebne do skonfigurowania i podaj ostatecznie do firmy Microsoft. Jak widać, wartość przestrzeni adresowej sieci wirtualnej platformy Azure nie jest przedstawiona w pierwszym przykładzie, ale zdefiniowano z zakresów pierwszy zakres adresów IP podsieci maszyny Wirtualnej platformy Azure i zakres adresów IP podsieci bramy sieci wirtualnej. Przy użyciu wielu podsieci maszyny Wirtualnej w ramach sieci wirtualnej platformy Azure będzie działać przez konfigurowanie odpowiednio i przesyłanie IP dodatkowych adresów zakresów dodatkowe adresy podsieci używane maszyny Wirtualnej jako część przestrzeni adresowej sieci wirtualnej platformy Azure.

![Zakresy adresów IP w SAP HANA wymagane we wdrożeniu minimalnego Azure (wystąpienia duże)](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

Istnieje również możliwość agregowanie danych, które można przesłać do firmy Microsoft. W takim przypadku przestrzeni adresowej sieci wirtualnej Azure tylko obejmuje jedną spację. Za pomocą zakresów adresów IP, które są używane w tym przykładzie wcześniej. Ta zagregowane przestrzeń adresowa sieci wirtualnej może wyglądać jak:

![Druga możliwość zakresów adresów IP w SAP HANA wymagane we wdrożeniu minimalnego Azure (wystąpienia duże)](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

Jak pokazano powyżej, a nie dwa zakresy mniejszych, zdefiniowane w przestrzeni adresowej sieci wirtualnej Azure, będziemy mieć jeden zakres większy, który obejmuje 4096 adresów IP. Duże definicji przestrzeni adresowej pozostawia niektórych raczej duże zakresy nieużywane. Ponieważ propagacji trasy protokołu BGP są używane wartości przestrzeni adresowej sieci wirtualnej, użycie zakresów nieużywane lokalnej lub w innym miejscu w sieci, mogą powodować problemy routingu. Dlatego zaleca się zachować przestrzeni adresowej ściśle dostosowany przestrzeni adresowej podsieci rzeczywiste używane. W razie potrzeby bez przestoju w sieci wirtualnej, można dodać nowe wartości przestrzeni adresowej później.
 
> [!IMPORTANT] 
> Każdy adres IP przestrzeni adresowej sieci wirtualnej Azure zakresu ER-P2P, pula IP serwera, należy **nie** pokrywających się ze sobą lub inny zakres używany w innym miejscu w sieci; każdego musi być kolumną dyskretną i jako dwa grafiki nie może być wcześniej Pokaż podsieć inny zakres. Ewentualnych nakładania się między zakresy sieci wirtualnej platformy Azure nie może połączyć się obwodu usługi expressroute.

### <a name="next-steps-after-address-ranges-have-been-defined"></a>Następne kroki po zdefiniowaniu zakresów adresów
Po zdefiniowaniu zakresów adresów IP, należy się zdarzyć, następujące działania:

1. Przedstawia zakresy adresów IP dla przestrzeni adresowej sieci wirtualnej Azure, ER P2P łączności i zakres puli adresów IP serwera, oraz innych danych, który został wystawiony na początku dokumentu. W tym momencie możesz również można uruchomić tworzenia sieci wirtualnej i podsieci maszyny Wirtualnej. 
2. Obwodzie usługi Express Route jest tworzony przez firmę Microsoft, między subskrypcją platformy Azure i sygnatura wystąpienia dużych HANA.
3. Na sygnatura wystąpienia dużych sieci dzierżawcy jest tworzony przez firmę Microsoft.
4. Microsoft konfiguruje sieci w SAP HANA w infrastrukturze Azure (wystąpienia duże) do akceptowania adresów IP z Azure sieć wirtualna przestrzeń adresowa komunikującym się z wystąpieniami dużych HANA.
5. W zależności od określonych SAP HANA na Azure (wystąpienia duże) jednostki SKU zakupu firma Microsoft przypisuje Jednostka obliczeniowa w sieci dzierżawcy, Przydziel instalacji magazynu i instalowania systemu operacyjnego (SUSE lub Red Hat Linux). Adresy IP dla tych jednostek są wyjmowane z adresów puli adresów IP serwera zakresu przesłane do firmy Microsoft.

Na koniec procesu wdrażania firma Microsoft dostarcza następujące dane dla użytkownika:
- Informacje wymagane do nawiązania VNet(s) Twojego Azure obwodu ExpressRoute, łączącej sieci wirtualnych platformy Azure do wystąpień dużych HANA:
     - Klucze autoryzacji
     - ExpressRoute PeerID
- Danych o stanie wystąpień dużych HANA po ustanowieniu obwodu ExpressRoute i sieci wirtualnej platformy Azure.

Możesz również znaleźć sekwencji łączący HANA dużych wystąpień w dokumencie [pełnego Instalatora programu SAP HANA dużych wystąpień](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/). Wiele z następujących kroków przedstawiono przykład wdrożenia w tym dokumencie. 


## <a name="connecting-a-vnet-to-hana-large-instance-expressroute"></a>Nawiązywanie połączenia ExpressRoute dużych wystąpienia HANA sieci wirtualnej

Zdefiniowane wszystkie zakresy adresów IP, a teraz otrzymano danych firmy Microsoft, możesz uruchomić sieć wirtualną utworzono przed nawiązywania połączenia z wystąpień dużych HANA. Po utworzeniu sieci wirtualnej platformy Azure w sieci wirtualnej, aby połączyć sieć wirtualną obwodu ExpressRoute, łączącego się dzierżawy klienta w sygnaturze dużych wystąpienia należy utworzyć bramę usługi ExpressRoute.

> [!NOTE] 
> Ten krok może potrwać do 30 minut, aby zakończyć, ponieważ nowej bramy jest tworzony w wyznaczonym subskrypcji platformy Azure i dołączenie do określonej sieci wirtualnej platformy Azure.

Jeśli brama już istnieje, sprawdź, czy należy bramę usługi ExpressRoute. Jeśli nie, bramy należy usunąć i utworzyć ponownie jako bramę usługi ExpressRoute. Jeśli brama usługi ExpressRoute już zostanie nawiązane, ponieważ sieć wirtualna Azure jest już połączona z obwodem usługi ExpressRoute dla połączenia lokalnego, należy przejść do poniższej sekcji Łączenie z sieciami wirtualnymi.

- Użyj albo (nowy) [portalu Azure](https://portal.azure.com/), lub programu PowerShell, aby utworzyć bramę sieci VPN ExpressRoute podłączony do sieci wirtualnej.
  - Jeśli używasz portalu Azure, Dodaj nową **Brama sieci wirtualnej** , a następnie wybierz **ExpressRoute** jako typu bramy.
  - Jeśli zamiast tego wybrano programu PowerShell, należy najpierw pobrać i użyć najnowszej wersji [zestawu SDK usługi Azure PowerShell](https://azure.microsoft.com/downloads/) zapewnienie zapewnienia optymalnego działania. Następujące polecenia Utwórz bramę usługi ExpressRoute. Teksty poprzedzony _$_ to zmienne zdefiniowane przez użytkownika, które muszą zostać zaktualizowane z konkretnych informacji.

```PowerShell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA Large Instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzureRmVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

W tym przykładzie użyto jednostka SKU bramy wysokowydajnej. Opcje są wysokowydajnej lub UltraPerformance jako brama tylko jednostki SKU obsługiwanych przez SAP HANA na platformie Azure (wystąpienia duże).

> [!IMPORTANT]
> HANA dużych wystąpienia typu II classs jednostki SKU użycie jednostka SKU bramy UltraPerformance jest obowiązkowe.

### <a name="linking-vnets"></a>Łączenie sieci wirtualnych

Teraz, gdy sieć wirtualna Azure ma bramę usługi ExpressRoute, możesz skorzystać z informacji autoryzacji, obsługiwane przez firmę Microsoft nawiązać bramę usługi ExpressRoute, aby SAP HANA obwodu Azure ExpressRoute (duże wystąpień) utworzone dla tego połączenia. Ten krok można wykonać przy użyciu portalu Azure lub programu PowerShell. Zaleca się portalu, jednak instrukcje programu PowerShell są następujące. 

- Można wykonywać następujące polecenia dla każdej bramy sieci wirtualnej przy użyciu różnych AuthGUID dla każdego połączenia. Pierwsze dwie pozycje następujące skryptu pochodzą z informacje dostarczane przez firmę Microsoft. Ponadto AuthGUID jest specyficzne dla każdej sieci wirtualnej i bramy. Oznacza, jeśli chcesz dodać do innej sieci wirtualnej Azure, należy uzyskać inny AuthID Twojego obwodu usługi expressroute łączy HANA dużych wystąpień na platformie Azure. 

```PowerShell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway Information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzureRmVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID
```

Jeśli chcesz połączyć bramę do wielu obwody usługi ExpressRoute, które są skojarzone z subskrypcją, może być konieczne wykonanie tego kroku więcej niż raz. Na przykład prawdopodobnie ma się połączyć z tą samą bramą sieci wirtualnej do obwodu ExpressRoute, który łączy sieci wirtualnej sieci lokalnej.

## <a name="adding-more-ip-addresses-or-subnets"></a>Dodawanie więcej adresów IP lub podsieci

Użyj portalu Azure, programu PowerShell lub interfejsu wiersza polecenia w przypadku dodawania więcej IP adresów albo podsieci.

W takim przypadku zalecane jest dodanie nowego zakresu adresów IP jako nowy zakres do przestrzeni adresowej sieci wirtualnej zamiast generowania nowego zagregowanych zakresu. W obu przypadkach należy przesłać tej zmiany do firmy Microsoft zezwalająca na łączności z tego nowego zakresu adresów IP z jednostkami wystąpienia dużych HANA na kliencie. Można otworzyć żądania pomocy technicznej platformy Azure można pobrać nowej przestrzeni adresowej sieci wirtualnej dodane. Po otrzymaniu potwierdzenia, wykonaj kolejne kroki.

Aby utworzyć dodatkowe podsieci przy użyciu portalu Azure, zobacz artykuł [utworzyć sieć wirtualną przy użyciu portalu Azure](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)i aby utworzyć na podstawie programu PowerShell, zobacz [utworzyć sieć wirtualną przy użyciu programu PowerShell](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network).

## <a name="adding-vnets"></a>Dodawanie sieci wirtualnych

Po początkowym nawiązaniu połączenia sieci wirtualnych platformy Azure, możesz dodać dodatkowe dostępne SAP HANA na platformie Azure (wystąpienia duże). Najpierw należy przesłać żądanie pomocy technicznej platformy Azure, w tym żądaniu zawiera zarówno informacje szczegółowe identyfikujący określonego wdrożenia usługi Azure i zakresów przestrzeni adresów IP przestrzeni adresowej sieci wirtualnej platformy Azure. SAP HANA na zarządzania usługą Azure następnie dostarcza niezbędne informacje potrzebne do połączenia dodatkowych sieci wirtualne i usługi ExpressRoute. Dla każdej sieci wirtualnej należy nawiązać połączenia z obwodem usługi ExpressRoute z wystąpieniami dużych HANA Unikatowy klucz autoryzacji.

Kroki w celu dodania nowej sieci wirtualnej platformy Azure:

1. Zobacz pełną pierwszym etapem procesu dołączania _tworzenie sieci wirtualnej Azure_ sekcji.
2. Zobacz pełną drugim etapem procesu dołączania _tworzenia podsieci bramy_ sekcji.
3. Aby połączyć Twoje dodatkowe sieci wirtualnych obwodem usługi ExpressRoute HANA dużych wystąpienia, otwarcia żądania pomocy technicznej platformy Azure przy użyciu informacji o nowej sieci wirtualnej i zażądaj nowego klucza autoryzacji.
4. Po powiadomienie, że autoryzacja została zakończona, użyj informacji o autoryzacji firmy Microsoft do ukończenia trzeci krok w procesie dołączania zobacz _łączenia sieci wirtualnych_ sekcji.

## <a name="increasing-expressroute-circuit-bandwidth"></a>Zwiększenie przepustowości obwodu ExpressRoute

Zapoznaj się z SAP HANA na zarządzanie usługami Azure. Jeśli zaleca się zwiększyć przepustowość SAP HANA obwodu usługi ExpressRoute Azure (wystąpienia duże), Utwórz żądanie pomocy technicznej platformy Azure. (Żądanie zwiększenia do przepustowości jednego obwodu maksymalnie 10 GB/s). Następnie możesz otrzymać powiadomienie po zakończeniu operacji; żadne dodatkowe akcje niezbędne do włączenia tej wyższej szybkości na platformie Azure.

## <a name="adding-an-additional-expressroute-circuit"></a>Dodawanie dodatkowych obwodu usługi ExpressRoute

Zapoznaj się z SAP HANA na zarządzanie usługami Azure, jeśli zaleca się, że potrzebne są dodatkowe obwodu ExpressRoute, wprowadź Azure obsługuje żądania, aby utworzyć nowy obwód usługi ExpressRoute (i można pobrać informacji o autoryzacji do nawiązania połączenia). Przestrzeń adresów, która ma być używany w sieci wirtualne muszą być zdefiniowane przed wykonaniem żądania, w celu SAP HANA na zarządzania usługą Azure zapewnia autoryzacji.

Po utworzeniu nowego obwodu i SAP HANA w konfiguracji zarządzania usługą Azure zostało ukończone, będą otrzymywać powiadomienia o informacje potrzebne kontynuować. Wykonaj kroki podane powyżej umożliwiające tworzenie i łączenie nową sieć wirtualną z tego dodatkowe obwodu. Nie jest możliwe do połączenia z sieciami wirtualnymi platformy Azure to dodatkowe obwodu jeśli one już połączony inny SAP HANA obwodu ExpressRoute Azure (wystąpienia duże) w tym samym regionie Azure.

## <a name="deleting-a-subnet"></a>Usuwanie podsieci

Aby usunąć podsieć sieci wirtualnej, można użyć portalu Azure, programu PowerShell lub interfejsu wiersza polecenia. W przypadku, gdy IP sieci wirtualnej platformy Azure adres zakresu/Azure przestrzeni adresowej sieci wirtualnej została zagregowanych zakresu, nie ma żadnych wykonaj dla możesz z firmą Microsoft. Z tą różnicą, że sieć wirtualna jest nadal trwa propagowanie przestrzeni adresowej trasy protokołu BGP, która obejmuje usunięto podsieci. Jeśli IP sieci wirtualnej platformy Azure adres zakresu/Azure przestrzeni adresowej sieci wirtualnej jest zdefiniowany jako wiele zakresów adresów IP, z których jeden została przypisana do usuniętego podsieci, Usuń to poza przestrzeń adresowa sieci wirtualnej i następnie informuje SAP HANA na zarządzanie usługą Azure usunięcie go z zakresów, które może komunikować się z SAP HANA na platformie Azure (wystąpienia duże).

Aby usunąć podsieć, zobacz [usunąć podsieć](../../../virtual-network/virtual-network-manage-subnet.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-subnet) Aby uzyskać więcej informacji na temat tworzenia podsieci.

## <a name="deleting-a-vnet"></a>Usunięcie sieci wirtualnej

Aby usunąć sieć wirtualną, zobacz [usunąć sieci wirtualnej](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-virtual-network). SAP HANA na zarządzania usługą Azure usuwa istniejące zezwolenia na SAP HANA obwodu usługi Azure ExpressRoute (duże wystąpień) i Usuń IP sieci wirtualnej platformy Azure adres zakresu/Azure przestrzeni adresowej sieci wirtualnej do komunikacji z wystąpieniami dużych HANA.

Po usunięciu sieci wirtualnej, otwórz żądanie pomocy technicznej platformy Azure do zapewnienia zakresów przestrzeni adresów IP do usunięcia.

Aby zapewnić, że wszystko jest usuwany, Usuń następujące elementy:

- Połączenia ExpressRoute, Brama sieci wirtualnej publiczny adres IP bramy sieci wirtualnej i sieci wirtualnej

## <a name="deleting-an-expressroute-circuit"></a>Usuwanie obwodu usługi ExpressRoute

Aby usunąć dodatkowe SAP HANA obwodu usługi ExpressRoute Azure (wystąpienia duże), otwórz żądanie pomocy technicznej platformy Azure z SAP HANA na zarządzania usługą Azure i żądania obwodu powinien zostać usunięty. W ramach subskrypcji platformy Azure możesz usunąć lub zachować sieci wirtualnej, w razie potrzeby. Jednak należy usunąć połączenia między obwodem usługi ExpressRoute HANA dużych wystąpień i połączone bramy sieci wirtualnej.

Jeśli chcesz również usunąć sieci wirtualnej, postępuj zgodnie ze wskazówkami na temat usuwania sieci wirtualnej w powyższej sekcji.


