---
title: 'Konfigurowanie trybu transportu IPsec dla komunikacji równorzędnej prywatnej hostów Windows: ExpressRoute: Azure | Microsoft Docs'
description: Jak włączyć trybu transportu IPsec między maszynami wirtualnymi z Windows Azure i hostów Windows w środowisku lokalnym za pośrednictwem prywatnej komunikacji równorzędnej przy użyciu obiektów zasad grupy i jednostki organizacyjne usługi ExpressRoute.
services: expressroute
author: fabferri
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: fabferri
ms.custom: seodec18
ms.openlocfilehash: d728980517988e2dc39be4e4b64d20157a1aef54
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60367275"
---
# <a name="configure-ipsec-transport-mode-for-expressroute-private-peering"></a>Konfigurowanie trybu transportu IPsec dla prywatnej komunikacji równorzędnej usługi ExpressRoute

Ten artykuł pomoże Ci w utworzeniu tuneli IPsec w trybie transportu za pośrednictwem usługi ExpressRoute prywatnych, komunikację równorzędną między systemem Windows maszyn wirtualnych platformy Azure i lokalne hosty Windows. Kroki opisane w tym artykule tworzenia takiej konfiguracji, za pomocą obiektów zasad grupy. Chociaż istnieje możliwość tworzenia takiej konfiguracji bez korzystania z jednostki organizacyjne (OU) i (GPO). obiekty zasad grupy, kombinację jednostek organizacyjnych i obiektów zasad grupy pomoże uprościć kontrolki zasad zabezpieczeń i pozwala szybko skalować się. Kroki opisane w tym artykule założono, że istnieje już Konfiguracja usługi Active Directory, i dopiero zaznajomieni z używaniem w jednostkach organizacyjnych i obiektów zasad grupy.

## <a name="about-this-configuration"></a>Informacje o tej konfiguracji

Konfiguracja w poniższych krokach za pomocą jednej sieci wirtualnej platformy Azure (VNet) prywatnej komunikacji równorzędnej usługi ExpressRoute. Jednak ta konfiguracja może obejmować więcej sieci wirtualnych platformy Azure i sieciami lokalnymi. Ten artykuł pomoże Ci zdefiniować zasady szyfrowania IPsec i zastosować je do grupy maszyn wirtualnych platformy Azure i hosty w środowisku lokalnym, które są częścią tej samej jednostce Organizacyjnej. Możesz skonfigurować szyfrowanie między maszyny wirtualne platformy Azure (maszyna vm1 i vm2) i host1 lokalnej tylko dla ruchu HTTP do portu docelowego 8080. Różne rodzaje protokołu IPsec można utworzyć zasady na podstawie wymagań.

### <a name="working-with-ous"></a>Praca z jednostkami organizacyjnymi 

Zasady zabezpieczeń skojarzonych z jednostką Organizacyjną zostanie przypisany do komputerów za pośrednictwem zasad grupy. Kilka zalet za pomocą jednostek organizacyjnych, zamiast stosowania zasad do jednego hosta, są:

* Skojarzenie zasad z jednostką Organizacyjną gwarantuje, że komputery, które należą do tej samej jednostce Organizacyjnej uzyskać te same zasady.
* Zmiana zasad zabezpieczeń skojarzonych z jednostki Organizacyjnej będzie miała zastosowanie zmiany do wszystkich hostów w jednostce Organizacyjnej.

### <a name="diagrams"></a>Diagramy

Na poniższym diagramie przedstawiono połączenia i przypisane przestrzeń adresów IP. Windows 2016 działają maszyny wirtualne platformy Azure i hosta lokalnego. Maszyny wirtualne platformy Azure i host1 lokalnych są częścią tej samej domenie. Maszyny wirtualne platformy Azure i hosty w środowisku lokalnym mogły rozpoznawać nazwy u prawidłowo przy użyciu systemu DNS.

[![1]][1]

Ten diagram przedstawia tuneli IPsec przesyłanych w prywatnej komunikacji równorzędnej usługi ExpressRoute.

[![4]][4]

### <a name="working-with-ipsec-policy"></a>Praca z zasad protokołu IPsec

W Windows szyfrowanie jest skojarzone z zasadami zabezpieczeń IPsec. Zasad protokołu IPsec określa, jaki ruch IP jest zabezpieczony i mechanizmu zabezpieczeń stosowane do pakietów IP.
**Zasady protokołu IPSec** składają się z następujących elementów: **Filtrowanie list**, **akcji filtrowania**, i **reguły zabezpieczeń**.

Podczas konfigurowania zasad protokołu IPsec, ważne jest zrozumienie następującą terminologią zasad protokołu IPsec:

* **Zasady protokołu IPsec:** Kolekcja reguł. Tylko jedna zasada może być aktywne ("przypisany") w danym momencie. Każda zasada może mieć jedną lub więcej reguł, które mogą być aktywne jednocześnie. Komputer można przypisać tylko jedno aktywne zasady IPsec przy danym czasie. Jednak w ramach zasad protokołu IPsec można zdefiniować wiele akcji, które mogą zostać podjęte w różnych sytuacjach. Każdy zestaw reguł IPsec jest skojarzony z listy filtrów, który wpływa na typ ruchu sieciowego, do której ta reguła ma zastosowanie.

* **Listy filtrów:** Listy filtrów są pakietu co najmniej jeden filtr. Jedna lista może zawierać wiele filtrów. Filtr definiuje, jeśli dozwolone, chronione lub zablokowane, zgodnie z zakresów adresów IP, protokołów lub nawet określonych portów komunikacji. Każdy filtr dopasowuje wartość do określonego zestawu warunków. na przykład pakiety wysyłane z określonej podsieci dla danego komputera, na określony port docelowy. Gdy się warunków sieciowych są zgodne, co najmniej jedną z tych filtrów, jest aktywowana lista filtrów. Każdy filtr zdefiniowano w obrębie listy określonego filtru. Filtry nie mogą być współdzielone listy filtrów. Jednak lista podanego filtra może być dołączany do kilku zasad protokołu IPsec. 

* **Akcje filtru:** Metoda zabezpieczeń definiuje zestaw algorytmów zabezpieczeń, protokołów, i oferuje kluczy komputera podczas negocjacji protokołu IKE. Akcje filtru są listy metod zabezpieczeń, uporządkowanych według priorytetu.  Gdy komputer negocjuje sesję protokołu IPsec, akceptuje lub wysyła wniosków na podstawie ustawienia zabezpieczeń przechowywane na liście akcji filtrowania.

* **Reguły zabezpieczeń:** Reguły określające sposób i kiedy zasady IPsec chroni komunikacji. Używa ona **lista filtrów** i **akcji filtrowania** można utworzyć reguły protokołu IPsec do tworzenia połączenia IPsec. Każda zasada może mieć jedną lub więcej reguł, które mogą być aktywne jednocześnie. Każda reguła zawiera listę filtrów IP kolekcję akcji zabezpieczeń, które odbywają się na pasują do tej listy filtr:
  * Akcje filtru IP
  * Metody uwierzytelniania
  * Ustawienia tunelu protokołu IP
  * Typy połączeń

[![5]][5]

## <a name="before-you-begin"></a>Przed rozpoczęciem

Upewnij się, że spełniasz następujące wymagania wstępne:

* Musi mieć działającej konfiguracji usługi Active Directory, którego można użyć, aby zaimplementować ustawienia zasad grupy. Aby uzyskać więcej informacji na temat obiektów zasad grupy, zobacz [obiekty zasad grupy](https://msdn.microsoft.com/library/windows/desktop/aa374162(v=vs.85).aspx).

* Musisz mieć aktywny obwód usługi ExpressRoute.
  * Aby uzyskać informacje dotyczące tworzenia obwodu usługi ExpressRoute, zobacz [utworzyć obwód usługi ExpressRoute](expressroute-howto-circuit-arm.md). 
  * Sprawdź, czy obwód jest włączony przez dostawcę połączenia. 
  * Sprawdź, czy prywatną komunikację równorzędną Azure skonfigurowany dla obwodu. Zobacz [Konfigurowanie routingu](expressroute-howto-routing-arm.md) artykuł, aby uzyskać instrukcje routingu. 
  * Sprawdź, czy masz sieci wirtualnej i bramy sieci wirtualnej, utworzona i w pełni zaaprowizowanym. Postępuj zgodnie z instrukcjami, aby [Tworzenie bramy sieci wirtualnej dla usługi ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Brama sieci wirtualnej dla usługi ExpressRoute używa GatewayType "ExpressRoute", nie sieci VPN.

* Brama sieci wirtualnej usługi ExpressRoute musi być połączony z obwodem usługi ExpressRoute. Aby uzyskać więcej informacji, zobacz [połączyć sieć wirtualną z obwodem usługi ExpressRoute](expressroute-howto-linkvnet-arm.md).

* Sprawdź, czy maszyny wirtualne Windows Azure są wdrażane w sieci wirtualnej.

* Sprawdź, czy jest łączność między hostami w środowisku lokalnym i maszyn wirtualnych platformy Azure.

* Sprawdź możliwość korzystania z serwera DNS można poprawnie rozpoznać nazwy maszyn wirtualnych Windows Azure i hosty w środowisku lokalnym.

### <a name="workflow"></a>Przepływ pracy

1. Utwórz obiekt zasad grupy i skojarz go z jednostką organizacyjną.
2. Zdefiniuj IPsec **Akcja filtrowania**.
3. Zdefiniuj IPsec **filtrowanie listy**.
4. Tworzenie zasad protokołu IPsec z **reguły zabezpieczeń**.
5. Przypisz IPsec obiektu zasad grupy do jednostki Organizacyjnej.

### <a name="example-values"></a>Przykładowe wartości

* **Nazwa domeny:** ipsectest.com

* **JEDNOSTKI ORGANIZACYJNEJ:** IPSecOU

* **Komputer Windows w środowisku lokalnym:** host1

* **Maszyny wirtualne Windows Azure:** maszyny vm1, vm2

## <a name="creategpo"></a>1. Utwórz obiekt zasad grupy

1. Aby utworzyć nowy obiekt zasad grupy połączone z jednostką Organizacyjną, otwórz przystawkę Zarządzanie zasadami grupy, a następnie zlokalizuj jednostkę Organizacyjną, do którego zostanie połączony obiekt zasad grupy. W tym przykładzie nosi nazwę jednostki Organizacyjnej **IPSecOU**. 

   [![9]][9]
2. W przystawce Zarządzanie zasadami grupy wybierz jednostkę Organizacyjną, a następnie kliknij prawym przyciskiem myszy. Na liście rozwijanej kliknij pozycję "**Utwórz obiekt GPO w tej domenie i umieść tu łącze...** ".

   [![10]][10]
3. Intuicyjne nazwy tak, aby łatwo znaleźć nazwę obiektu zasad grupy. Kliknij przycisk **OK** utworzyć i połączyć obiekt zasad grupy.

   [![11]][11]

## <a name="enablelink"></a>2. Włącz łącze obiektu zasad grupy

Aby zastosować obiekt zasad grupy do jednostki Organizacyjnej, obiekt zasad grupy należy nie można połączyć tylko z jednostki Organizacyjnej, ale łącze musi być także włączona.

1. Zlokalizuj utworzony obiekt zasad grupy, kliknij prawym przyciskiem myszy i wybierz **Edytuj** z listy rozwijanej.
2. Aby zastosować obiekt zasad grupy do jednostki Organizacyjnej, wybierz **łącze włączone**.

   [![12]][12]

## <a name="filteraction"></a>3. Zdefiniuj akcję filtru IP

1. Z listy rozwijanej, kliknij prawym przyciskiem myszy **zasady zabezpieczeń IP w usłudze Active Directory**, a następnie kliknij przycisk **Zarządzanie adresów IP listy filtrów i akcje filtru...** .

   [![15]][15]
2. Na "**Zarządzaj filtru akcji**" kliknij pozycję **Dodaj**.

   [![16]][16]

3. Na **Kreatora akcji filtrowania zabezpieczeń IP**, kliknij przycisk **dalej**.

   [![17]][17]
4. Nazwa akcji filtrowania intuicyjne nazwę, aby można je znaleźć w dalszej części. W tym przykładzie nosi nazwę akcji filtrowania **myEncryption**. Można również dodać opis. Następnie kliknij przycisk **Dalej**.

   [![18]][18]
5. **Negocjowanie zabezpieczeń** umożliwia definiowanie zachowania, jeśli IPsec nie można ustanowić za pomocą innego komputera. Wybierz **negocjowanie zabezpieczeń**, następnie kliknij przycisk **dalej**.

   [![19]][19]
6. Na **Communicating z komputerami, które nie obsługują protokołu IPsec** wybierz opcję **nie zezwalają na komunikację niezabezpieczoną**, następnie kliknij przycisk **dalej**.

   [![20]][20]
7. Na **ruch IP i zabezpieczenia** wybierz opcję **niestandardowe**, następnie kliknij przycisk **ustawień...** .

   [![21]][21]
8. Na **niestandardowych ustawień metody zabezpieczeń** wybierz opcję **integralność danych i szyfrowanie (ESP): ALGORYTM SHA1, 3DES**. Następnie kliknij przycisk **OK**.

   [![22]][22]
9. Na **Zarządzanie akcji filtrowania** stronie można zobaczyć które **myEncryption** filtr został pomyślnie dodany. Kliknij przycisk **Zamknij**.

   [![23]][23]

## <a name="filterlist1"></a>4. Definiowanie listy filtrów IP

Utwórz listę filtrów, który określa zaszyfrowany ruch HTTP do portu docelowego 8080.

1. Aby zakwalifikować się, jakie rodzaje ruchu muszą być szyfrowane, należy użyć **lista filtru IP**. W **Zarządzanie listami filtru IP** kliknij pozycję **Dodaj** Aby dodać nową listę filtrów IP.

   [![24]][24]
2. W **Name:** wpisz nazwę dla listy filtrów. Na przykład **HTTP8080-azure opłacanie**. Następnie kliknij przycisk **Dodaj**.

   [![25]][25]
3. Na **właściwości opisu filtru IP i dublowane** wybierz opcję **dublowane**. Dublowany ustawienie pasuje pakietów, możesz to zrobić w obu kierunkach, co pozwala uzyskać komunikacja dwukierunkowa. Następnie kliknij przycisk **Next** (Dalej).

   [![26]][26]
4. Na **źródła ruchu IP** strony, od **adres źródłowy:** listy rozwijanej wybierz **określonego adresu IP lub podsieci**. 

   [![27]][27]
5. Określ adres źródłowy **adresu IP lub podsieci:** ruch IP, następnie kliknij przycisk **dalej**.

   [![28]][28]
6. Określ **adres docelowy:** Adres IP lub podsieci. Następnie kliknij przycisk **Dalej**.

   [![29]][29]
7. Na **typ protokołu IP** wybierz opcję **TCP**. Następnie kliknij przycisk **Dalej**.

   [![30]][30]
8. Na **Port protokołu IP** wybierz opcję **z dowolnego portu** i **z tym portem:** . Typ **8080** w polu tekstowym. Te ustawienia określają, że będą szyfrowane tylko ruch HTTP na porcie docelowym 8080. Następnie kliknij przycisk **Dalej**.

   [![31]][31]
9. Wyświetl listę filtrów IP.  Konfiguracja lista filtru IP **HTTP8080-azure opłacanie** wyzwala szyfrowania dla całego ruchu, które spełniają następujące kryteria:

   * Dowolnego źródłowego adresu w 10.0.1.0/24 (Subnet2 platformy Azure)
   * Dowolny docelowy adres w 10.2.27.0/25 (podsieć lokalna)
   * Protokół TCP
   * Docelowy port 8080

   [![32]][32]

## <a name="filterlist2"></a>5. Edytowanie listy filtrów IP

Aby zaszyfrować ten sam typ ruchu w odwrotnym kierunku (z hosta na lokalnej maszynie Wirtualnej platformy Azure), musisz mieć drugi filtr adresów IP. Proces konfigurowania nowego filtru jest ten sam proces, który umożliwia konfigurowanie pierwszy filtr adresów IP. Jedyne różnice są podsieci źródła i podsieci docelowej.

1. Aby dodać nowy filtr adresów IP do listy filtrów adresów IP, wybierz **Edytuj**.

   [![33]][33]
2. Na **lista filtru IP** kliknij **Dodaj**.

   [![34]][34]
3. Utwórz drugi filtr adresów IP przy użyciu ustawień w poniższym przykładzie:

   [![35]][35]
4. Po utworzeniu drugi filtr adresów IP, listy filtrów będzie wyglądać następująco:

   [![36]][36]

Jeśli szyfrowanie jest wymagane między lokalizacją lokalną i podsieci platformy Azure, aby ochronić aplikację, zamiast modyfikowania istniejącego lista filtru IP możesz dodać nową listę filtrów IP. Kojarzenie 2 IP listy filtrów do tych samych zasad protokołu IPsec zapewnia większą elastyczność, ponieważ określone lista filtru IP może modyfikować lub usunąć w dowolnej chwili, bez wywierania wpływu na inne listy filtrów IP.

## <a name="ipsecpolicy"></a>6. Tworzenie zasad zabezpieczeń protokołu IPsec 

Tworzenie zasad protokołu IPsec z zasadami zabezpieczeń.

1. Wybierz **IPSecurity zasady w usłudze Active directory** skojarzonego z jednostki Organizacyjnej. Kliknij prawym przyciskiem myszy, a następnie wybierz pozycję **Utwórz zasady zabezpieczeń IP**.

   [![37]][37]
2. Nazwa zasad zabezpieczeń. Na przykład **zasad azure opłacanie**. Następnie kliknij przycisk **Dalej**.

   [![38]][38]
3. Kliknij przycisk **dalej** bez zaznaczania pola wyboru.

   [![39]][39]
4. Upewnij się, że **Edytuj właściwości** pole wyboru jest zaznaczone, a następnie kliknij przycisk **Zakończ**.

   [![40]][40]

## <a name="editipsec"></a>7. Edytuj zasady zabezpieczeń protokołu IPsec

Dodawanie zasad IPsec **lista filtru IP** i **akcji filtrowania** skonfigurowaną wcześniej.

1. Zasady protokołu HTTP, właściwości **reguły** kliknij pozycję **Dodaj**.

   [![41]][41]
2. Na stronie powitalnej kliknij **dalej**.

   [![42]][42]
3. Reguła zapewnia możliwość definiowania trybu IPsec: trybu tunelu lub transportu.

   * W trybie tunelowania oryginalnym pakiet jest hermetyzowany przez zestaw nagłówki IP. Tryb tunelowania chroni wewnętrznych informacji o routingu, szyfrując nagłówek IP oryginalnego pakietu. Tryb tunelowania jest często stosowana między bramami w scenariuszach sieci VPN typu lokacja lokacja. Tryb tunelowania jest w większości przypadków używany do szyfrowania end-to-end między hostami.

   * Tryb transportu szyfruje tylko ładunek oraz ESP; Nagłówek IP oryginalny pakiet nie jest zaszyfrowany. W trybie transportu IP źródłowego i docelowego adresu IP pakietów nie ulegną zmianie.

   Wybierz **ta reguła określa tunel**, a następnie kliknij przycisk **dalej**.

   [![43]][43]
4. **Typ sieci** definiuje, które kojarzy połączenia za pomocą zasad zabezpieczeń sieci. Wybierz **wszystkich połączeń sieciowych**, a następnie kliknij przycisk **dalej**.

   [![44]][44]
5. Wybierz z listy filtrów IP, który został utworzony wcześniej, **HTTP8080-azure opłacanie**, a następnie kliknij przycisk **dalej**.

   [![45]][45]
6. Wybierz istniejącą akcję filtru **myEncryption** utworzonego wcześniej.

   [![46]][46]
7. Windows obsługuje cztery różne rodzaje uwierzytelnienia: Protokół Kerberos, certyfikaty, NTLMv2 i klucz wstępny. Ponieważ pracujemy z hostami przyłączonych do domeny, wybierz **domyślne usługi Active Directory (protokół Kerberos V5)** , a następnie kliknij przycisk **dalej**.

   [![47]][47]
8. Nowe zasady tworzy reguły zabezpieczeń: **HTTP8080-azure opłacanie**. Kliknij przycisk **OK**.

   [![48]][48]

Zasady protokołu IPsec wymaga wszystkich połączeń HTTP na porcie 8080, aby użyć trybu transportu IPsec docelowym. HTTP jest protokół zwykłego tekstu, mających włączone zasady zabezpieczeń zapewnia, że dane są szyfrowane, gdy jest przesyłany za pośrednictwem prywatnej komunikacji równorzędnej usługi ExpressRoute. Zasady zabezpieczeń IP dla usługi Active Directory jest bardziej złożona, aby skonfigurować niż Zapora Windows z zabezpieczeniami zaawansowanymi, ale umożliwiające większym stopniu połączenia IPsec.

## <a name="assigngpo"></a>8. Przypisz IPsec obiektu zasad grupy do jednostki Organizacyjnej

1. Wyświetl zasady. Zasady grupy zabezpieczeń jest określony, ale nie została jeszcze przypisana.

   [![49]][49]
2. Aby przypisać zasady grupy zabezpieczeń do jednostki Organizacyjnej **IPSecOU**, kliknij prawym przyciskiem myszy zasady zabezpieczeń i wybrać **przypisać**.
   Każdy komputer umożliwiający należy do jednostki Organizacyjnej będą mieć przypisanych zasad grupy zabezpieczeń.

   [![50]][50]

## <a name="checktraffic"></a>Sprawdź szyfrowania ruchu

Aby sprawdzić szyfrowanie zastosowano w jednostce Organizacyjnej obiekt zasad grupy, należy zainstalować usługi IIS na wszystkich maszynach wirtualnych platformy Azure i w host1. Każdy program IIS jest dostosowany do odpowiedzi na żądania HTTP na porcie 8080.
Aby sprawdzić, szyfrowanie, szperacz sieciowy (np. programu Wireshark) można zainstalować na wszystkich komputerach w jednostce Organizacyjnej.
Skrypt programu powershell działa jako klient HTTP do generowania żądań HTTP na porcie 8080:

```powershell
$url = "http://10.0.1.20:8080"
while ($true) {
try {
[net.httpWebRequest]
$req = [net.webRequest]::create($url)
$req.method = "GET"
$req.ContentType = "application/x-www-form-urlencoded"
$req.TimeOut = 60000

$start = get-date
[net.httpWebResponse] $res = $req.getResponse()
$timetaken = ((get-date) - $start).TotalMilliseconds

Write-Output $res.Content
Write-Output ("{0} {1} {2}" -f (get-date), $res.StatusCode.value__, $timetaken)
$req = $null
$res.Close()
$res = $null
} catch [Exception] {
Write-Output ("{0} {1}" -f (get-date), $_.ToString())
}
$req = $null

# uncomment the line below and change the wait time to add a pause between requests
#Start-Sleep -Seconds 1
}

```
Następujące przechwytywania sieci przedstawiono wyniki dla host1 w środowisku lokalnym za pomocą wyświetlania ESP filtru, aby dopasować tylko zaszyfrowany ruch sieciowy:

[![51]][51]

Po uruchomieniu programu powershell script na premisies (klienta HTTP) przechwytywania sieci w maszynie Wirtualnej platformy Azure zawiera podobne śledzenia.

## <a name="next-steps"></a>Kolejne kroki

Więcej informacji na temat usługi ExpressRoute znajduje się w artykule [ExpressRoute FAQ](expressroute-faqs.md) (Usługa ExpressRoute — często zadawane pytania).

<!--Image References-->

[1]: ./media/expressroute-howto-ipsec-transport-private-windows/network-diagram.png "trybu transportu Diagram IPsec sieci za pośrednictwem usługi ExpressRoute"
[4]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-interesting-traffic.png "interesujących ruchu protokołu IPsec"
[5]: ./media/expressroute-howto-ipsec-transport-private-windows/windows-ipsec.png "zasad protokołu IPsec dla Windows"
[9]: ./media/expressroute-howto-ipsec-transport-private-windows/ou.png "jednostki organizacyjnej w zasadach grupy"
[10]: ./media/expressroute-howto-ipsec-transport-private-windows/create-gpo-ou.png "Utwórz obiekt zasad grupy skojarzonych z jednostką Organizacyjną"
[11]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-name.png "Przypisz nazwę do obiektu zasad grupy skojarzonych z jednostką Organizacyjną"
[12]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-gpo.png "edycji obiektu zasad grupy"
[15]: ./media/expressroute-howto-ipsec-transport-private-windows/manage-ip-filter-list-filter-actions.png "Zarządzanie listami filtrów IP i akcji filtrowania"
[16]: ./media/expressroute-howto-ipsec-transport-private-windows/add-filter-action.png "dodać akcję filtru"
[17]: ./media/expressroute-howto-ipsec-transport-private-windows/action-wizard.png "Kreator akcji"
[18]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-name.png "nazwy filtru akcji"
[19]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action.png "Akcja filtrowania"
[20]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-no-ipsec.png "określić zachowanie jest niebezpieczne połączenie zostanie nawiązane"
[21]: ./media/expressroute-howto-ipsec-transport-private-windows/security-method.png "mechanizmu zabezpieczeń"
[22]: ./media/expressroute-howto-ipsec-transport-private-windows/custom-security-method.png "niestandardowe metody zabezpieczeń"
[23]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-actions-list.png "listę akcji filtrów"
[24]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-ip-filter.png "Dodaj nową listę filtrów IP"
[25]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-http-traffic.png "ruch HTTP, Dodaj do filtru IP"
[26]: ./media/expressroute-howto-ipsec-transport-private-windows/match-both-direction.png "pakietów dopasowania w obu kierunkach"
[27]: ./media/expressroute-howto-ipsec-transport-private-windows/source-address.png "wybór podsieć źródłowa"
[28]: ./media/expressroute-howto-ipsec-transport-private-windows/source-network.png "źródła sieci"
[29]: ./media/expressroute-howto-ipsec-transport-private-windows/destination-network.png "sieci docelowej"
[30]: ./media/expressroute-howto-ipsec-transport-private-windows/protocol.png "protokołu"
[31]: ./media/expressroute-howto-ipsec-transport-private-windows/source-port-and-destination-port.png "źródłowy port i port docelowy"
[32]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list.png "listy filtrów"
[33]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-for-http.png "listy filtrów z ruchem protokołu HTTP"
[34]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-add-second-entry.png "Dodawanie drugi filtr adresów IP"
[35]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-second-entry.png "wpisu listy drugi filtr adresów IP"
[36]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list-2entries.png "wpisu listy drugi filtr adresów IP"
[37]: ./media/expressroute-howto-ipsec-transport-private-windows/create-ip-security-policy.png "Tworzenie zasad zabezpieczeń IP"
[38]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-policy-name.png "nazwy zasad IPsec"
[39]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-wizard.png "Kreator zasad protokołu IPsec"
[40]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-security-policy.png "edytowanie zasad IPsec"
[41]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-rule.png "Dodaj nową regułę zabezpieczeń zasad IPsec"
[42]: ./media/expressroute-howto-ipsec-transport-private-windows/create-security-rule.png "Utwórz nową regułę zabezpieczeń"
[43]: ./media/expressroute-howto-ipsec-transport-private-windows/transport-mode.png "tryb transportu"
[44]: ./media/expressroute-howto-ipsec-transport-private-windows/network-type.png "typ sieci"
[45]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-list.png "wybór istniejącej listy filtrów"
[46]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-action.png "wybór istniejącej akcji filtrowania"
[47]: ./media/expressroute-howto-ipsec-transport-private-windows/authentication-method.png "wybór metody uwierzytelniania"
[48]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-completed.png "koniec procesu tworzenia zasad zabezpieczeń"
[49]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-not-assigned.png "zasad protokołu IPsec, połączyć obiekt zasad grupy, ale nie przypisane"
[50]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-assigned.png "zasad IPsec przypisanych do obiektu zasad grupy"
[51]: ./media/expressroute-howto-ipsec-transport-private-windows/encrypted-traffic.png "przechwytywania IPsec zaszyfrowanego ruchu sieciowego"
