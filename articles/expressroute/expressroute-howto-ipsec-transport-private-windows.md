---
title: 'Prywatna komunikacja równorzędna usługi Azure ExpressRoute: konfigurowanie trybu transportu IPsec — hosty systemu Windows'
description: Jak włączyć tryb transportu IPsec między maszynami wirtualnymi systemu Windows platformy Azure a lokalnymi hostami systemu Windows za pośrednictwem prywatnej komunikacji równorzędnej usługi ExpressRoute przy użyciu obiektów zasad grupy i obiektów organizacyjnych.
services: expressroute
author: fabferri
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: fabferri
ms.custom: seodec18
ms.openlocfilehash: 1bc33047d31262af443cddc418853fbacd88aec1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022010"
---
# <a name="configure-ipsec-transport-mode-for-expressroute-private-peering"></a>Konfigurowanie trybu transportu Protokołu IPsec dla prywatnej komunikacji równorzędnej usługi ExpressRoute

Ten artykuł ułatwia tworzenie tuneli IPsec w trybie transportu za pomocą prywatnej komunikacji równorzędnej usługi ExpressRoute między maszynami wirtualnymi platformy Azure z systemem Windows a lokalnymi hostami systemu Windows. Kroki opisane w tym artykule tworzą tę konfigurację przy użyciu obiektów zasad grupy. Chociaż możliwe jest utworzenie tej konfiguracji bez użycia jednostek organizacyjnych (OU) i obiektów zasad grupy (GPO), połączenie jednostek organizacyjnych i obiektów zasad grupy pomoże uprościć kontrolę nad zasadami zabezpieczeń i umożliwia szybkie skalowanie w górę. Kroki opisane w tym artykule zakładają, że masz już konfigurację usługi Active Directory i że znasz za pomocą procesorów operacyjnych i procesorów operacyjnych.

## <a name="about-this-configuration"></a>Informacje o tej konfiguracji

Konfiguracja w poniższych krokach użyj jednej sieci wirtualnej platformy Azure (VNet) z prywatną komunikacją równorzędną usługi ExpressRoute. Jednak ta konfiguracja może obejmować więcej sieci wirtualnych platformy Azure i sieci lokalnych. Ten artykuł pomoże Ci zdefiniować zasady szyfrowania IPsec i zastosować ją do grupy maszyn wirtualnych platformy Azure i hostów lokalnych, które są częścią tej samej grupy organizacyjnej. Szyfrowanie między maszynami wirtualnymi platformy Azure (vm1 i vm2) i hostem lokalnym1 tylko dla ruchu HTTP z portem docelowym 8080. Różne typy zasad Protokołu IPsec można tworzyć na podstawie wymagań.

### <a name="working-with-ous"></a>Praca z systemami operacyjnymi 

Zasady zabezpieczeń skojarzone z obiektem organizacyjnym są wypychane do komputerów za pośrednictwem obiektu zasad grupy. Kilka zalet korzystania z jednostek organizacyjnych, zamiast stosowania zasad do jednego hosta, to:

* Skojarzenie zasad z o.o. gwarantuje, że komputery należące do tej samej grupy organizacyjnej otrzymują te same zasady.
* Zmiana zasad zabezpieczeń skojarzonych z usługą organizacyjną spowoduje zastosowanie zmian do wszystkich hostów w uikw.

### <a name="diagrams"></a>Diagramy

Na poniższym diagramie przedstawiono wzajemne połączenia i przypisaną przestrzeń adresową IP. Maszyny wirtualne platformy Azure i host lokalny są uruchomione w systemie Windows 2016. Maszyny wirtualne platformy Azure i host lokalny1 są częścią tej samej domeny. Maszyny wirtualne platformy Azure i hosty lokalne mogą poprawnie rozpoznać nazwy przy użyciu systemu DNS.

[![1]][1]

Ten diagram przedstawia tunele IPsec w tranzycie w prywatnej komunikacji równorzędnej usługi ExpressRoute.

[![4]][4]

### <a name="working-with-ipsec-policy"></a>Praca z zasadami protokołu IPsec

W systemie Windows szyfrowanie jest skojarzone z zasadami IPsec. Zasady protokołu IPsec określają, który ruch IP jest zabezpieczony, a mechanizm zabezpieczeń stosowany do pakietów IP.
**Zasady IPSec** składają się z następujących elementów: **Listy filtrów,** **Akcje filtrowania**i **Reguły zabezpieczeń**.

Podczas konfigurowania zasad protokołu IPsec należy zapoznać się z następującą terminologią zasad Protokołu IPsec:

* **Zasady protokołu IPsec:** Zbiór reguł. Tylko jedna zasada może być aktywna ("przypisana") w dowolnym momencie. Każda zasada może mieć jedną lub więcej reguł, z których wszystkie mogą być aktywne jednocześnie. W danym momencie na komputerze można przypisać tylko jedną aktywną zasadę Protokołu IPsec. Jednak w ramach zasad Protokołu IPsec można zdefiniować wiele akcji, które mogą być podejmowane w różnych sytuacjach. Każdy zestaw reguł IPsec jest skojarzony z listą filtrów, która wpływa na typ ruchu sieciowego, do którego ma zastosowanie reguła.

* **Listy filtrów:** Listy filtrów są pakietem co najmniej jednego filtra. Jedna lista może zawierać wiele filtrów. Filtr określa, czy komunikacja jest dozwolona, zabezpieczona lub zablokowana, zgodnie z zakresami adresów IP, protokołami, a nawet określonymi portami protokołu. Każdy filtr pasuje do określonego zestawu warunków; na przykład pakiety wysyłane z określonej podsieci do określonego komputera na określonym porcie docelowym. Jeśli warunki sieciowe odpowiadają jednemu lub więcej z tych filtrów, lista filtrów jest aktywowana. Każdy filtr jest zdefiniowany wewnątrz określonej listy filtrów. Filtrów nie można udostępniać między listami filtrów. Jednak dana lista filtrów może być włączona do kilku zasad Protokołu IPsec. 

* **Akcje filtrowania:** Metoda zabezpieczeń definiuje zestaw algorytmów zabezpieczeń, protokołów i kluczy, które komputer oferuje podczas negocjacji usługi IKE. Akcje filtrowania to listy metod zabezpieczeń uszeregowane według preferencji.  Gdy komputer negocjuje sesję protokołu IPsec, akceptuje lub wysyła propozycje na podstawie ustawienia zabezpieczeń przechowywanego na liście akcji filtrowania.

* **Zasady bezpieczeństwa:** Reguły regulują sposób i czas, kiedy zasady IPsec chronią komunikację. Używa **listy filtrów** i **akcji filtrowania** do utworzenia reguły IPsec w celu utworzenia połączenia IPsec. Każda zasada może mieć jedną lub więcej reguł, z których wszystkie mogą być aktywne jednocześnie. Każda reguła zawiera listę filtrów IP i zbiór akcji zabezpieczeń, które mają miejsce po dopasowaniu do tej listy filtrów:
  * Akcje filtru IP
  * Metody uwierzytelniania
  * Ustawienia tunelu IP
  * Typy połączeń

[![5]][5]

## <a name="before-you-begin"></a>Przed rozpoczęciem

Upewnij się, że spełniasz następujące wymagania wstępne:

* Musisz mieć działającą konfigurację usługi Active Directory, której można użyć do zaimplementowania ustawień zasad grupy. Aby uzyskać więcej informacji na temat obiektów zasad grupy, zobacz [Obiekty zasad grupy](https://msdn.microsoft.com/library/windows/desktop/aa374162(v=vs.85).aspx).

* Musisz mieć aktywny obwód usługi ExpressRoute.
  * Aby uzyskać informacje dotyczące tworzenia obwodu usługi ExpressRoute, zobacz [Tworzenie obwodu usługi ExpressRoute](expressroute-howto-circuit-arm.md). 
  * Sprawdź, czy obwód jest włączony przez dostawcę łączności. 
  * Sprawdź, czy dla obwodu skonfigurowano prywatną komunikację równorzędna platformy Azure. Zapoznaj się z artykułem [konfigurowania routingu,](expressroute-howto-routing-arm.md) aby uzyskać instrukcje dotyczące routingu. 
  * Sprawdź, czy masz sieć wirtualną i bramę sieci wirtualnej utworzone i w pełni aprowizowana. Postępuj zgodnie z instrukcjami, aby [utworzyć bramę sieci wirtualnej dla usługi ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Brama sieci wirtualnej dla usługi ExpressRoute używa gatewayType "ExpressRoute", a nie SIECI VPN.

* Brama sieci wirtualnej usługi ExpressRoute musi być podłączona do obwodu usługi ExpressRoute. Aby uzyskać więcej informacji, zobacz [Łączenie sieci wirtualnej z obwodem usługi ExpressRoute](expressroute-howto-linkvnet-arm.md).

* Sprawdź, czy maszyny wirtualne systemu Windows platformy Azure są wdrażane w sieci wirtualnej.

* Sprawdź, czy istnieje łączność między hostami lokalnymi a maszynami wirtualnymi platformy Azure.

* Sprawdź, czy maszyny wirtualne systemu Windows platformy Azure i hosty lokalne mogą używać systemu DNS do prawidłowego rozpoznawania nazw.

### <a name="workflow"></a>Przepływ pracy

1. Utwórz obiekt zasad grupy i skojarz go z o.o.
2. Zdefiniuj **akcję filtru**IPsec .
3. Definiowanie listy **filtrów**protokołu IPsec .
4. Tworzenie zasad protokołu IPsec z **regułami zabezpieczeń**.
5. Przypisz obiekt zasad grupy IPsec do obiektu organizacyjnego.

### <a name="example-values"></a>Przykładowe wartości

* **Nazwa domeny:** ipsectest.com

* **ZU:** Protokół IPSecou

* **Lokalny komputer z systemem Windows:** host1

* **Maszyny wirtualne systemu Windows platformy Azure:** vm1, vm2

## <a name="1-create-a-gpo"></a><a name="creategpo"></a>1. Tworzenie obiektu zasad grupy

1. Aby utworzyć nowy obiekt zasad grupy połączony z obiektem organizacyjnym, otwórz przystawkę Zarządzanie zasadami grupy i znajdź obiekt organizacyjny, z którym zostanie połączony obiekt zasad grupy. W tym przykładzie o nazwie OU jest nazwany **IPSecOU**. 

   [![9]][9]
2. W przystawce Zarządzanie zasadami grupy wybierz pozycję OU i kliknij prawym przyciskiem myszy. W menu rozwijanym kliknij przycisk "**Utwórz obiekt zasad grupy w tej domenie i połącz go tutaj...**".

   [![10]][10]
3. Nadaj obiektowi zasad grupy intuicyjną nazwę, dzięki czemu można ją łatwo zlokalizować później. Kliknij **przycisk OK,** aby utworzyć i połączyć obiekt zasad grupy.

   [![11]][11]

## <a name="2-enable-the-gpo-link"></a><a name="enablelink"></a>2. Włącz łącze obiektu zasad grupy

Aby zastosować obiekt zasad grupy do obiektu organizacyjnego, obiekt zasad grupy musi być nie tylko połączony z ojętą, ale także musi być włączony.

1. Znajdź utworzony obiekt zasad grupy, kliknij prawym przyciskiem myszy i wybierz polecenie **Edytuj** z listy rozwijanej.
2. Aby zastosować obiekt zasad grupy do oku, wybierz opcję **Włączono łącze**.

   [![12]][12]

## <a name="3-define-the-ip-filter-action"></a><a name="filteraction"></a>3. Zdefiniuj akcję filtru IP

1. Z listy rozwijanej kliknij prawym przyciskiem myszy **pozycję Zasady zabezpieczeń IP w usłudze Active Directory,** a następnie kliknij polecenie **Zarządzaj listami filtrów IP i akcjami filtrowania...**.

   [![15]][15]
2. Na karcie **"Zarządzaj akcjami filtru"** kliknij pozycję **Dodaj**.

   [![16]][16]

3. W **kreatorze akcji filtru zabezpieczeń IP**kliknij przycisk **Dalej**.

   [![17]][17]
4. Nadaj akcji filtru intuicyjną nazwę, aby można ją było później znaleźć. W tym przykładzie akcja filtru nosi nazwę **myEncryption**. Można również dodać opis. Następnie kliknij przycisk **Dalej**.

   [![18]][18]
5. **Negocjuj zabezpieczenia** umożliwia zdefiniowanie zachowania, jeśli nie można ustanowić protokołu IPsec z innym komputerem. Wybierz **pozycję Negocjuj zabezpieczenia,** a następnie kliknij przycisk **Dalej**.

   [![19]][19]
6. Na stronie **Komunikowanie się z komputerami, które nie obsługują protokołu IPsec,** wybierz pozycję **Nie zezwalaj na komunikację niezabezpieczoną,** a następnie kliknij przycisk **Dalej**.

   [![20]][20]
7. Na stronie **Ruch IP i zabezpieczenia** wybierz pozycję **Niestandardowe**, a następnie kliknij pozycję **Ustawienia...**.

   [![21]][21]
8. Na stronie **Niestandardowe ustawienia metody zabezpieczeń** wybierz pozycję **Integralność i szyfrowanie danych (ESP): SHA1, 3DES**. Następnie kliknij przycisk **OK**.

   [![22]][22]
9. Na stronie **Zarządzanie akcjami filtru** widać, że filtr **myEncryption** został pomyślnie dodany. Kliknij przycisk **Zamknij**.

   [![23]][23]

## <a name="4-define-an-ip-filter-list"></a><a name="filterlist1"></a>4. Definiowanie listy filtrów IP

Utwórz listę filtrów określającą zaszyfrowany ruch HTTP z portem docelowym 8080.

1. Aby zakwalifikować typy ruchu, należy użyć **listy filtrów IP**. Na karcie **Zarządzanie listami filtrów IP** kliknij pozycję **Dodaj,** aby dodać nową listę filtrów IP.

   [![24]][24]
2. W polu **Nazwa:** wpisz nazwę listy filtrów IP. Na przykład **azure-onpremises-HTTP8080**. Następnie kliknij przycisk **Add** (Dodaj).

   [![25]][25]
3. Na stronie **Opis filtru IP i Funkcja Dublowana** wybierz pozycję **Dublowane**. Ustawienie dublowane dopasowuje pakiety przechodzące w obu kierunkach, co pozwala na dwukierunkową komunikację. Następnie kliknij przycisk **Dalej**.

   [![26]][26]
4. Na stronie **Źródło ruchu IP** z listy rozwijanej Adres **źródłowy:** wybierz pozycję **Określony adres IP lub podsieć**. 

   [![27]][27]
5. Określ adres **źródłowy ADRES IP lub podsieć:** ruchu IP, a następnie kliknij przycisk **Dalej**.

   [![28]][28]
6. Określ **adres docelowy:** adres IP lub podsieć. Następnie kliknij przycisk **Dalej**.

   [![29]][29]
7. Na stronie **Typ protokołu IP** wybierz pozycję **TCP**. Następnie kliknij przycisk **Dalej**.

   [![30]][30]
8. Na stronie **Port protokołu IP** wybierz opcję Z dowolnego **portu** i Do **tego portu:**. Wpisz **8080** w polu tekstowym. Te ustawienia określają tylko ruch HTTP na porcie docelowym 8080 będą szyfrowane. Następnie kliknij przycisk **Dalej**.

   [![31]][31]
9. Wyświetl listę filtrów IP.  Konfiguracja listy filtrów IP **azure-onpremises-HTTP8080** wyzwala szyfrowanie dla całego ruchu, który spełnia następujące kryteria:

   * Dowolny adres źródłowy w 10.0.1.0/24 (podsieć Platformy Azure2)
   * Dowolny adres docelowy w 10.2.27.0/25 (podsieć lokalna)
   * Protokół TCP
   * Port docelowy 8080

   [![32]][32]

## <a name="5-edit-the-ip-filter-list"></a><a name="filterlist2"></a>5. Edytuj listę filtrów IP

Aby zaszyfrować ten sam typ ruchu w przeciwnym kierunku (z hosta lokalnego do maszyny Wirtualnej platformy Azure) potrzebujesz drugiego filtru IP. Proces konfigurowania nowego filtru jest tym samym procesem, który został użyty do skonfigurowania pierwszego filtru IP. Jedyne różnice to podsieć źródłowa i podsieć docelowa.

1. Aby dodać nowy filtr IP do listy filtrów IP, wybierz pozycję **Edytuj**.

   [![33]][33]
2. Na stronie **Lista filtrów IP** kliknij pozycję **Dodaj**.

   [![34]][34]
3. Utwórz drugi filtr IP przy użyciu ustawień w poniższym przykładzie:

   [![35]][35]
4. Po utworzeniu drugiego filtru IP lista filtrów IP będzie wyglądać następująco:

   [![36]][36]

Jeśli szyfrowanie jest wymagane między lokalizacją lokalną a podsiecią platformy Azure w celu ochrony aplikacji, zamiast modyfikować istniejącą listę filtrów IP, można zamiast tego dodać nową listę filtrów IP. Skojarzenie 2 list filtrów IP z tymi samymi zasadami Protokołu IPsec zapewnia większą elastyczność, ponieważ określona lista filtrów IP może być modyfikowana lub usuwana w dowolnym momencie bez wpływu na inne listy filtrów IP.

## <a name="6-create-an-ipsec-security-policy"></a><a name="ipsecpolicy"></a>6. Tworzenie zasad zabezpieczeń IPsec 

Utwórz zasadę Protokołu IPsec z regułami zabezpieczeń.

1. Wybierz **zasady ipsecurity w usłudze Active Directory** skojarzonej z o.o. Kliknij prawym przyciskiem myszy i wybierz pozycję **Utwórz zasady zabezpieczeń IP**.

   [![37]][37]
2. Nazwij zasady zabezpieczeń. Na przykład **zasady azure-onpremises**. Następnie kliknij przycisk **Dalej**.

   [![38]][38]
3. Kliknij **przycisk Dalej** bez zaznaczania pola wyboru.

   [![39]][39]
4. Sprawdź, czy zaznaczono pole wyboru **Edytuj właściwości,** a następnie kliknij przycisk **Zakończ**.

   [![40]][40]

## <a name="7-edit-the-ipsec-security-policy"></a><a name="editipsec"></a>7. Edytowanie zasad zabezpieczeń IPsec

Dodaj do zasad IPsec wcześniej skonfigurowane **wykaz filtrów IP** i **akcję filtrowania.**

1. Na karcie **Reguły** właściwości zasad HTTP kliknij pozycję **Dodaj**.

   [![41]][41]
2. Na stronie powitalnej kliknij przycisk **Dalej**.

   [![42]][42]
3. Reguła umożliwia zdefiniowanie trybu IPsec: tryb tunelu lub tryb transportu.

   * W trybie tunelowania oryginalny pakiet jest hermetyzowany przez zestaw nagłówków IP. Tryb tunelu chroni wewnętrzne informacje o routingu, szyfrując nagłówek IP oryginalnego pakietu. Tryb tunelowania jest szeroko implementowany między bramami w scenariuszach sieci VPN lokacja lokacja. Tryb tunelu jest w większości przypadków używany do szyfrowania end-to-end między hostami.

   * Tryb transportu szyfruje tylko ładunek i przyczepę ESP; nagłówek IP oryginalnego pakietu nie jest zaszyfrowany. W trybie transportu źródło IP i miejsce docelowe ip pakietów pozostają niezmienione.

   Wybierz **ta reguła nie określa tunelu,** a następnie kliknij przycisk **Dalej**.

   [![43]][43]
4. **Typ sieci** określa, które połączenie sieciowe jest skojarzone z zasadami zabezpieczeń. Wybierz **pozycję Wszystkie połączenia sieciowe**, a następnie kliknij przycisk **Dalej**.

   [![44]][44]
5. Wybierz listę filtrów IP utworzoną wcześniej, **azure-onpremises-HTTP8080**, a następnie kliknij przycisk **Dalej**.

   [![45]][45]
6. Wybierz istniejącą akcję filtru **myEncryption,** która została utworzona wcześniej.

   [![46]][46]
7. System Windows obsługuje cztery różne typy uwierzytelniania: Kerberos, certyfikaty, NTLMv2 i klucz wstępny. Ponieważ pracujemy z hostami przyłączanym do domeny, wybierz **pozycję Active Directory default (protokół Kerberos V5),** a następnie kliknij przycisk **Dalej**.

   [![47]][47]
8. Nowa zasada tworzy regułę zabezpieczeń: **azure-onpremises-HTTP8080**. Kliknij przycisk **OK**.

   [![48]][48]

Zasady protokołu IPsec wymagają, aby wszystkie połączenia HTTP na porcie docelowym 8080 używały trybu transportu IPsec. Ponieważ protokół HTTP jest protokołem zwykłego tekstu, włączenie zasad zabezpieczeń gwarantuje szyfrowanie danych podczas przesyłania za pośrednictwem prywatnej komunikacji równorzędnej usługi ExpressRoute. Zasady zabezpieczeń IP dla usługi Active Directory są bardziej złożone niż Zapora systemu Windows z zabezpieczeniami zaawansowanymi, ale umożliwiają bardziej dostosowywanie połączenia IPsec.

## <a name="8-assign-the-ipsec-gpo-to-the-ou"></a><a name="assigngpo"></a>8. Przypisz obiekt zasad grupy IPsec do

1. Wyświetl zasady. Zasady grupy zabezpieczeń są zdefiniowane, ale nie zostały jeszcze przypisane.

   [![49]][49]
2. Aby przypisać zasady grupy zabezpieczeń do **protokołu OU IPSecOU,** kliknij prawym przyciskiem myszy zasadę zabezpieczeń i wybierz polecenie **Przypisz**.
   Każdy komputer tht należy do ou będzie miał przypisane zasady grupy zabezpieczeń.

   [![50]][50]

## <a name="check-traffic-encryption"></a><a name="checktraffic"></a>Sprawdzanie szyfrowania ruchu

Aby wyewidencjonować obiekt zasad szyfrowania zastosowany na instalacji organizacyjnej, zainstaluj usługi IIS na wszystkich maszynach wirtualnych platformy Azure i w hostze1. Wszystkie usługi IIS są dostosowywane do odpowiedzi na żądania HTTP na porcie 8080.
Aby zweryfikować szyfrowanie, można zainstalować sniffer sieci (jak Wireshark) na wszystkich komputerach w OU.
Skrypt programu PowerShell działa jako klient HTTP do generowania żądań HTTP na porcie 8080:

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
Następujące przechwytywanie sieci pokazuje wyniki dla hosta lokalnego1 z filtrem wyświetlania ESP, aby dopasować tylko zaszyfrowany ruch:

[![51]][51]

Po uruchomieniu skryptu programu PowerShell na premisies (klient HTTP), przechwytywanie sieci w maszynie Wirtualnej platformy Azure pokazuje podobny ślad.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi ExpressRoute, zobacz [często zadawane pytania dotyczące usługi ExpressRoute](expressroute-faqs.md).

<!--Image References-->

[1]: ./media/expressroute-howto-ipsec-transport-private-windows/network-diagram.png "tryb transportu Diagram sieciowy IPsec za pośrednictwem usługi ExpressRoute"
[4]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-interesting-traffic.png "IPsec ciekawy ruch"
[5 Zasady]: ./media/expressroute-howto-ipsec-transport-private-windows/windows-ipsec.png "protokołu IPsec systemu Windows"
[9]: ./media/expressroute-howto-ipsec-transport-private-windows/ou.png "Jednostka organizacyjna w zasadach grupy"
[10]: ./media/expressroute-howto-ipsec-transport-private-windows/create-gpo-ou.png "utworzyć obiekt zasad grupy skojarzony z obiektem organizacyjnym"
[11]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-name.png "przypisz nazwę do obiektu zasad grupy skojarzonej z obiektem organizacyjnym"
[12]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-gpo.png "edytować obiekt zasad grupy"
[15]: ./media/expressroute-howto-ipsec-transport-private-windows/manage-ip-filter-list-filter-actions.png "Zarządzanie listami filtrów IP i akcjami filtrowania"
[16]: ./media/expressroute-howto-ipsec-transport-private-windows/add-filter-action.png "dodaj akcję filtru"
[17]: ./media/expressroute-howto-ipsec-transport-private-windows/action-wizard.png "Kreator akcji"
[18]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-name.png "Nazwa akcji filtrowania"
[19]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action.png "Akcja filtrowania"
[20]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-no-ipsec.png "określić zachowanie jest niezabezpieczone połączenie jest ustanawiane"
[21]: ./media/expressroute-howto-ipsec-transport-private-windows/security-method.png "mechanizm bezpieczeństwa"
[22]: ./media/expressroute-howto-ipsec-transport-private-windows/custom-security-method.png "Niestandardowa metoda zabezpieczeń"
[23]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-actions-list.png "lista akcji filtru"
[24]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-ip-filter.png "Dodawanie nowej listy filtrów IP"
[25]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-http-traffic.png "Dodawanie ruchu HTTP do filtru IP"
[Pakiet 26]: ./media/expressroute-howto-ipsec-transport-private-windows/match-both-direction.png "dopasowań w obu kierunkach"
[27]: ./media/expressroute-howto-ipsec-transport-private-windows/source-address.png "wybór podsieci Źródło"
[28]: ./media/expressroute-howto-ipsec-transport-private-windows/source-network.png "Sieć źródłowcza"
[29]: ./media/expressroute-howto-ipsec-transport-private-windows/destination-network.png "Sieć docelowa"
[30]: ./media/expressroute-howto-ipsec-transport-private-windows/protocol.png "Protokół"
[31]: ./media/expressroute-howto-ipsec-transport-private-windows/source-port-and-destination-port.png "port źródłowy i port docelowy"
[32]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list.png "lista filtrów"
[33]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-for-http.png "Lista filtrów IP z ruchem HTTP"
[34]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-add-second-entry.png "Dodawanie drugiego filtru IP"
[35]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-second-entry.png "Filtr IP -drugi wpis"
[36]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list-2entries.png "Filtr IP -drugi wpis"
[37]: ./media/expressroute-howto-ipsec-transport-private-windows/create-ip-security-policy.png "tworzenie zasad zabezpieczeń IP"
[38]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-policy-name.png "nazwa zasady IPsec"
[39]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-wizard.png "Kreator zasad protokołu IPsec"
[40]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-security-policy.png "edycja zasad Protokołu IPsec"
[41]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-rule.png "dodawanie nowej reguły zabezpieczeń do zasad protokołu IPsec"
[42]: ./media/expressroute-howto-ipsec-transport-private-windows/create-security-rule.png "tworzenie nowej reguły zabezpieczeń"
[43 Tryb]: ./media/expressroute-howto-ipsec-transport-private-windows/transport-mode.png "transportu"
[44]: ./media/expressroute-howto-ipsec-transport-private-windows/network-type.png "Typ sieci"
[45]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-list.png "wybór istniejącej listy filtrów IP"
[46]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-action.png "wybór istniejącej akcji filtra"
[47]: ./media/expressroute-howto-ipsec-transport-private-windows/authentication-method.png "wybór metody uwierzytelniania"
[48]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-completed.png "zakończenie procesu tworzenia polityki bezpieczeństwa"
[49]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-not-assigned.png "Zasady IPsec połączone z obiektem zasad grupy, ale nieprzypisane"
[50]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-assigned.png "zasad IPsec przypisanych do obiektu zasad grupy"
[51]: ./media/expressroute-howto-ipsec-transport-private-windows/encrypted-traffic.png "Przechwytywanie ruchu szyfrowanego IPsec"
