---
title: Przykład sieci obwodowej — tworzenie sieci obwodowej w ochronie aplikacji z zaporą i grup NSG | Dokumentacja firmy Microsoft
description: Tworzenie sieci obwodowej z zaporą i grup zabezpieczeń sieci (NSG)
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: c78491c7-54ac-4469-851c-b35bfed0f528
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: jonor;sivae
ms.openlocfilehash: cc0e8a3fa749eb2e6f65ef92c2d3cb404cfc8bc0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
---
# <a name="example-2--build-a-dmz-to-protect-applications-with-a-firewall-and-nsgs"></a>Przykład 2 — Tworzenie sieci obwodowej w ochronie aplikacji z zaporą i grupy NSG
[Wróć do strony zabezpieczeń granic najlepsze praktyki][HOME]

W tym przykładzie utworzy sieć obwodową z zaporą, windows czterech serwerów i grup zabezpieczeń sieci. On również przeprowadzi wszystkich odpowiednich poleceń, aby zapewnić głębsze zrozumienie każdego kroku. Jest również sekcji scenariusza ruchu zapewnienie szczegółowe instrukcje, jak ruchu obejmującego warstw zabezpieczeń w sieci obwodowej. Ponadto w odwołaniach sekcja jest kompletny kod i instrukcje do tworzenia tego środowiska, aby przetestować i wypróbować różne scenariusze. 

![Przychodzący sieć obwodowa NVA i grupy NSG][1]

## <a name="environment-description"></a>Opis elementu środowiska
W tym przykładzie jest subskrypcji, która zawiera następujące elementy:

* Dwie usługi w chmurze: "FrontEnd001" i "BackEnd001"
* Sieć wirtualna "CorpNetwork", z dwoma podsieciami: "FrontEnd" i "Wewnętrzna"
* Pojedynczej grupy zabezpieczeń sieci stosowany do obu podsieci
* Urządzenie wirtualne sieci, w tym przykładzie zaporę NextGen Barracuda podłączone do podsieci frontonu
* Windows Server, który reprezentuje serwer sieci web aplikacji ("IIS01")
* Dwa okna serwerów, które reprezentują aplikacji z powrotem kończyć serwery ("AppVM01", "AppVM02")
* Windows server, który reprezentuje serwer DNS ("DNS01")

> [!NOTE]
> Mimo że w tym przykładzie użyto zapory NextGen Barracuda, wiele różnych urządzeń wirtualnych sieci można w tym przykładzie.
> 
> 

W poniższej sekcji odwołań jest skrypt programu PowerShell, który zostanie skompilowany większość środowiska opisane powyżej. Tworzenie maszyn wirtualnych i sieci wirtualnych, chociaż są realizowane przez skrypt przykładowy nie opisano szczegółowo w tym dokumencie.

Aby utworzyć środowiska:

1. Zapisz plik xml konfiguracji sieci zawarte w sekcji odwołań (zaktualizowany o nazwy, lokalizacji i IP adresów do dopasowania danego scenariusza)
2. Zaktualizuj zmienne użytkownika w skrypcie, aby dopasować środowiska, które jest skrypt do uruchomienia przed (subskrypcje, nazwy usługi itp.)
3. Uruchom skrypt programu PowerShell

**Uwaga**: region oznaczony w skrypcie programu PowerShell musi odpowiadać oznaczony w pliku xml konfiguracji sieci.

Po pomyślnym uruchomieniu skryptu może podjąć następujące kroki skryptu po:

1. Konfigurowanie reguł zapory, ten temat znajdują się w sekcji poniżej: reguł zapory.
2. Opcjonalnie w sekcji odwołań są dwa skrypty do konfiguracji serwera sieci web oraz serwer aplikacji z prostą aplikację sieci web umożliwia testowanie za pomocą tej konfiguracji sieci obwodowej.

W następnej sekcji objaśniono większości instrukcji skryptów względem grup zabezpieczeń sieci.

## <a name="network-security-groups-nsg"></a>Sieciowe grupy zabezpieczeń (NSG)
Na przykład grupy NSG jest wbudowana i następnie ładowane przy użyciu sześciu reguł. 

> [!TIP]
> Ogólnie rzecz biorąc należy najpierw utworzyć określonych reguł "Zezwalaj", a następnie ostatni bardziej ogólnym reguły "Deny". Określają priorytetem, do których zasady są oceniane pierwszej. Po znalezieniu ruchu do zastosowania do określonej reguły nie dalsze reguły są sprawdzane. Reguły NSG można zastosować w jednym kierunku ruchu przychodzącego lub wychodzącego (z punktu widzenia podsieci).
> 
> 

Deklaratywnie są tworzone następujące reguły dla ruchu przychodzącego:

1. Wewnętrzny ruch DNS (port 53) jest dozwolony
2. Ruch RDP (port 3389) z Internetu do żadnej maszyny Wirtualnej jest dozwolone.
3. Ruch HTTP (port 80) z Internetu do NVA (zapory) jest dozwolone.
4. Cały ruch (wszystkie porty) z IIS01 do AppVM1 jest dozwolone.
5. Cały ruch (wszystkie porty) z Internetu do całej sieci wirtualnej (obie podsieci) jest zabroniony.
6. Odmowa cały ruch (wszystkie porty) z podsieci frontonu do podsieci wewnętrznej bazy danych

Przy użyciu tych reguł powiązany z każdej podsieci, jeśli żądanie HTTP zostało przychodzące z Internetu do serwera sieci web, obie zasady 3 (Zezwalaj) i 5 (odmówić go) będą miały zastosowania, ale ponieważ reguła 3 ma wyższy priorytet tylko będą miały zastosowania i reguły 5 nie przybyły do odtwarzania. W związku z tym żądania HTTP mogliby zapory. Jeśli ten sam ruch próbował uzyskać dostęp do serwera DNS01, reguły 5 (odmowa) będzie pierwszy do zastosowania, a ruch będzie niedozwolone do przekazania na serwer. Reguła 6 (Odmów) uniemożliwia podsieci frontonu z rozmowie z podsieci wewnętrznej bazy danych (z wyjątkiem dozwolonego ruchu w regułach 1 i 4), co chroni sieć zaplecze w przypadku dokonywania atakująca aplikacja sieci web frontonu, osoba atakująca może mieć ograniczony dostęp do sieci wewnętrznej bazy danych "chronionej" (tylko dla zasobów udostępniane na serwerze AppVM01).

Brak domyślnej regule wychodzącej, która umożliwia ruchu wychodzącego do Internetu. Na przykład firma Microsoft zezwala na ruch wychodzący i nie modyfikowanie reguł wychodzących. Do blokowania ruchu w zarówno wskazówek zdefiniowanych routingu użytkownika jest wymagana, jest to przedstawione w przykładzie różnych, który można znaleźć w [dokumentu granic zabezpieczeń głównego][HOME].

Reguły NSG opisanych powyżej są bardzo podobne do reguł NSG w [przykład 1 — Tworzenie prostej sieci obwodowej z grup NSG][Example1]. Przejrzyj opis grupy NSG, w tym dokumencie, aby uzyskać szczegółowy widok każdej reguły NSG i jego atrybuty.

## <a name="firewall-rules"></a>Reguły zapory
Klienta zarządzania musi być zainstalowana na komputerze Zarządzanie zaporą i tworzyć konfiguracje wymagane. Można znaleźć dostawcy dokumentacji zapory (lub inne NVA) do zarządzania urządzeniem. W pozostałej części tej sekcji opisano konfigurację zapory, za pomocą klienta zarządzania dostawców (tj. portalu Azure lub programu PowerShell).

Instrukcje dotyczące pobierania klienta i nawiązywania połączenia z Barracuda używana w tym przykładzie można znaleźć tutaj: [Barracuda NG administratora](https://techlib.barracuda.com/NG61/NGAdmin)

Zapory zasady przekazywania zostanie muszą zostać utworzone. Ponieważ w tym przykładzie tylko trasy internet ruchu przychodzącego zapory, a następnie do serwera sieci web, przekazywanie tylko jedną regułę NAT jest wymagane. W zaporze NextGen Barracuda używana w tym przykładzie reguły byłoby regułę NAT docelowego ("czasu letniego NAT") do przekazania ten ruch.

Utworzyć następującą regułę (lub sprawdź, czy istniejących reguł domyślnych), począwszy od pulpitu nawigacyjnego klienta Barracuda NG administratora, przejdź do karty konfiguracji, w konfiguracji operacyjnej sekcji kliknij zestaw reguł. Siatka o nazwie "Main reguły" wyświetli istniejących reguł active i zdezaktywowane na zaporze. W prawym górnym rogu tej siatki jest mały, zielony "+", kliknij tutaj, aby utworzyć nową regułę (Uwaga: Zapora może być "zablokowana" zmian, jeśli zostanie wyświetlony przycisk oznaczony jako "Lock" i nie można utworzyć lub edytować reguły, kliknij ten przycisk "Odblokuj" zestaw reguł i Zezwól na edytowanie). Jeśli chcesz edytować istniejącą regułę, wybierz reguły, kliknij prawym przyciskiem myszy i wybierz pozycję Edytuj regułę.

Utwórz nową regułę i podaj nazwę, na przykład "WebTraffic". 

Ikona reguł NAT docelowego wygląda następująco: ![docelowym translacji adresów Sieciowych ikony][2]

Reguła sam będzie wyglądać mniej więcej tak:

![Reguły zapory][3]

Tutaj żadnego ruchu przychodzącego adresu, który trafienia zapory próba nawiązania połączenia HTTP (port 80 i 443 dla protokołu HTTPS) zostanie wysłane z interfejsu "DHCP1 lokalny adres IP" zapory i Przekierowanie do serwera sieci Web z adresem IP 10.0.1.5. Ponieważ ruch jest przychodzących na porcie 80 i przechodząc do serwera sieci web na porcie 80 zostało niezbędne nie zmiany portu. Jednak listy docelowej mogło być 10.0.1.5:8080 jeśli nasłuch serwera sieci Web na porcie 8080, w związku z tym tłumaczenia przychodzący port 80 w zaporze przychodzący port 8080 na serwerze sieci web.

Metodę połączenia powinien również być oznaczony, docelowy reguły z Internetu, "Dynamiczne SNAT" jest najbardziej odpowiednia. 

Mimo że tylko jedna reguła została utworzona ważne jest jej priorytet została poprawnie ustawiona. Jeśli w siatce wszystkie reguły zapory nowej znajdzie się na dole (poniżej reguła "BLOCKALL") nie będą wchodzić w grę. Sprawdź, czy nowo utworzonej reguły dla ruchu w sieci web jest powyżej BLOCKALL reguły.

Gdy tworzona jest reguła, musi być przypisany do zapory i następnie aktywowany, jeśli nie zostanie to zrobione zmiana reguła nie zacznie obowiązywać. W następnej sekcji opisano sposób wypychania i aktywacji.

## <a name="rule-activation"></a>Reguły aktywacji
Z ruleset zmodyfikowany w celu dodania tej reguły zestaw reguł należy przekazać do zapory i aktywowane.

![Aktywacja reguły zapory][4]

W prawym górnym rogu klienta zarządzania są klastra przycisków. Kliknij przycisk "Wyślij zmiany", aby wysłać zmodyfikowane reguły zapory, a następnie kliknij przycisk "Uaktywnij".

W aktywacji zestaw reguł zapory tej kompilacji w środowisku przykładzie zostanie zakończona. Opcjonalnie można uruchamiać skrypty kompilacji post w sekcji odwołań do dodania do testowania aplikacji w tym środowisku poniżej scenariusze ruchu.

> [!IMPORTANT]
> Jest bardzo ważne należy pamiętać, że nie nastąpi trafienie serwera sieci web bezpośrednio. Gdy przeglądarka żąda strony HTTP z FrontEnd001.CloudApp.Net, punkt końcowy HTTP (port 80) przekazuje ruch w zaporze nie jest serwerem sieci web. Zapora następnie, zgodnie z zasadą utworzone powyżej translatory adresów sieciowych, które żądanie do serwera sieci Web.
> 
> 

## <a name="traffic-scenarios"></a>Scenariusze ruchu
#### <a name="allowed-web-to-web-server-through-firewall"></a>(Dozwolone) Serwer sieci Web w sieci Web za pośrednictwem zapory
1. Strony internetowe użytkownika żądania HTTP z FrontEnd001.CloudApp.Net (Internet ukierunkowane usługa w chmurze)
2. Chmury usługi przekazuje ruch przez otwarty punkt końcowy na porcie 80 do interfejsu lokalnego zapory 10.0.1.4:80
3. Podsieci frontonu rozpoczyna przetwarzanie przychodzących reguł:
   1. Reguły NSG 1 DNS (Domain Name System) nie są spełnione, przejść do następnej reguły
   2. 2 reguły NSG (RDP) nie są spełnione, przejść do następnej reguły
   3. Zastosować grupy NSG zasady 3 (Internet zaporą), ruch jest dozwolony, stop reguły przetwarzania
4. Ruch trafienia wewnętrzny adres IP zapory (10.0.1.4)
5. Reguły zapory przekazywania Zobacz to jest ruch na porcie 80, przekierowuje go do serwera sieci web IIS01
6. IIS01 nasłuchuje ruchu w sieci web, otrzymuje tego żądania i rozpoczyna przetwarzanie żądania
7. IIS01 żąda od serwera SQL na AppVM01 informacji
8. Nie reguł dla ruchu wychodzącego w podsieci frontonu, ruch jest dozwolony
9. Podsieci wewnętrznej bazy danych rozpoczyna się przetwarzanie przychodzących reguł:
   1. Reguły NSG 1 DNS (Domain Name System) nie są spełnione, przejść do następnej reguły
   2. 2 reguły NSG (RDP) nie są spełnione, przejść do następnej reguły
   3. Grupy NSG zasady 3 (Internet do zapory) nie są spełnione, przejść do następnej reguły
   4. 4 reguły NSG zastosować (IIS01 do AppVM01), ruch jest dozwolony, Zatrzymaj przetwarzania zasad
10. AppVM01 odbiera zapytanie SQL i odpowiada
11. Ponieważ nie ma żadnych reguł dla ruchu wychodzącego w podsieci wewnętrznej bazy danych jest dozwolone odpowiedzi
12. Podsieci frontonu rozpoczyna przetwarzanie przychodzących reguł:
    1. Nie zasady grupy NSG, która ma zastosowanie do przychodzącego ruchu w podsieci wewnętrznej bazy danych do podsieci frontonu, aby żadna grupa NSG zasady stosowane
    2. Domyślna reguła system zezwala na ruch między podsieciami pozwala tego rodzaju ruch, ruch jest dozwolony.
13. Serwer usług IIS otrzyma odpowiedź SQL i kończy odpowiedź HTTP i przesyła do żądającego
14. Ponieważ jest to sesji translatora adresów Sieciowych z zapory, miejsce docelowe odpowiedzi (początkowo) jest zapory
15. Zapora odbiera odpowiedź z serwera sieci Web i przekazuje użytkownikowi Internet
16. Ponieważ ma nie reguł dla ruchu wychodzącego w podsieci frontonu odpowiedzi jest dozwolone, a Internet użytkownik otrzymuje żądanej strony sieci web.

#### <a name="allowed-rdp-to-backend"></a>(Dozwolone) RDP do wewnętrznej bazy danych
1. Administrator serwera w Internecie żądań sesji protokołu RDP do AppVM01 na BackEnd001.CloudApp.Net:xxxxx gdzie xxxxx jest numerem losowo przypisany port protokołu RDP do AppVM01 (przypisanego portu można znaleźć w portalu Azure lub za pomocą programu PowerShell)
2. Ponieważ Zapora tylko nasłuchuje na adresie FrontEnd001.CloudApp.Net, nie jest elementem z tego przepływu ruchu
3. Podsieci wewnętrznej bazy danych rozpoczyna się przetwarzanie przychodzących reguł:
   1. Reguły NSG 1 DNS (Domain Name System) nie są spełnione, przejść do następnej reguły
   2. Zastosuj 2 reguły NSG (RDP), ruch jest dozwolony, stop reguły przetwarzania
4. Z nie reguł dla ruchu wychodzącego Zastosuj reguły domyślne i zwracany ruch jest dozwolony
5. Włączono sesji protokołu RDP
6. AppVM01 wyświetli monit o hasło nazwy użytkownika

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Dozwolone) Wyszukiwania DNS serwera sieci Web na serwerze DNS
1. Sieci Web serwera, IIS01, musi na www.data.gov strumieniowego źródła danych, ale musi rozpoznać adresu.
2. Konfigurację sieci dla listy sieci wirtualnej DNS01 (10.0.2.4 podsieci wewnętrznej bazy danych) jako podstawowy serwer DNS, IIS01 wysyła żądanie DNS DNS01
3. Nie reguł dla ruchu wychodzącego w podsieci frontonu, ruch jest dozwolony
4. Podsieci wewnętrznej bazy danych rozpoczyna się przetwarzanie przychodzących reguł:
   1. Zastosuj reguły NSG 1 DNS (Domain Name System), ruch jest dozwolony, stop reguły przetwarzania
5. Serwer DNS odbiera żądanie
6. Serwer DNS nie ma adresu w pamięci podręcznej i prosi o główny serwer DNS w Internecie
7. Nie reguł dla ruchu wychodzącego w podsieci wewnętrznej bazy danych, ruch jest dozwolony
8. DNS w Internecie serwer odpowiada, ponieważ ta sesja została zainicjowana wewnętrznie, odpowiedź jest dozwolone.
9. Serwer DNS będzie buforować odpowiedź i odpowiada na żądania początkowego do IIS01
10. Nie reguł dla ruchu wychodzącego w podsieci wewnętrznej bazy danych, ruch jest dozwolony
11. Podsieci frontonu rozpoczyna przetwarzanie przychodzących reguł:
    1. Nie zasady grupy NSG, która ma zastosowanie do przychodzącego ruchu w podsieci wewnętrznej bazy danych do podsieci frontonu, aby żadna grupa NSG zasady stosowane
    2. Domyślna reguła system zezwala na ruch między podsieciami pozwala tego rodzaju ruch, ruch jest dozwolony
12. IIS01 odbiera odpowiedź z DNS01

#### <a name="allowed-web-server-access-file-on-appvm01"></a>(Dozwolone) Plik dostępu do serwera sieci Web na AppVM01
1. IIS01 żąda pliku na AppVM01
2. Nie reguł dla ruchu wychodzącego w podsieci frontonu, ruch jest dozwolony
3. Podsieci wewnętrznej bazy danych rozpoczyna się przetwarzanie przychodzących reguł:
   1. Reguły NSG 1 DNS (Domain Name System) nie są spełnione, przejść do następnej reguły
   2. 2 reguły NSG (RDP) nie są spełnione, przejść do następnej reguły
   3. Grupy NSG zasady 3 (Internet do zapory) nie są spełnione, przejść do następnej reguły
   4. 4 reguły NSG zastosować (IIS01 do AppVM01), ruch jest dozwolony, Zatrzymaj przetwarzania zasad
4. AppVM01 odbiera żądanie i odpowiada plik (przy założeniu, że autoryzacji dostępu)
5. Ponieważ nie ma żadnych reguł dla ruchu wychodzącego w podsieci wewnętrznej bazy danych jest dozwolone odpowiedzi
6. Podsieci frontonu rozpoczyna przetwarzanie przychodzących reguł:
   1. Nie zasady grupy NSG, która ma zastosowanie do przychodzącego ruchu w podsieci wewnętrznej bazy danych do podsieci frontonu, aby żadna grupa NSG zasady stosowane
   2. Domyślna reguła system zezwala na ruch między podsieciami pozwala tego rodzaju ruch, ruch jest dozwolony.
7. Serwer usług IIS odbiera plik

#### <a name="denied-web-direct-to-web-server"></a>(Odmowa) Bezpośrednio do serwera sieci Web w sieci Web
Ponieważ serwer sieci Web, IIS01 i zapory znajdują się w tej samej usługi w chmurze mają ten sam publicznego połączonej adres IP. W związku z tym cały ruch HTTP będą kierowane do zapory. Gdy żądanie będzie obsłużone pomyślnie, nie można przejść bezpośrednio do serwera sieci Web, przeszła, zgodnie z założeniami przez zaporę, najpierw. Zobacz pierwszego scenariusza, w tej sekcji dla przepływu ruchu.

#### <a name="denied-web-to-backend-server"></a>(Odmowa) Do serwera wewnętrznej bazy danych w sieci Web
1. Internet użytkownik próbuje uzyskać dostęp w pliku na AppVM01 za pośrednictwem usługi BackEnd001.CloudApp.Net
2. Ponieważ nie ma żadnych punktów końcowych otwarty do udziału plików, to nie przejdzie usługi w chmurze, a nie dociera do serwera
3. Jeśli punktów końcowych, które były otwarte jakiegoś powodu, reguły NSG 5 (Internet do sieci wirtualnej) czy zablokować ruch

#### <a name="denied-web-dns-lookup-on-dns-server"></a>(Odmowa) Wyszukiwania DNS w sieci Web na serwerze DNS
1. Internet użytkownik podejmuje próbę wyszukiwania wewnętrzny rekord DNS na DNS01 za pośrednictwem usługi BackEnd001.CloudApp.Net
2. Ponieważ nie ma żadnych punktów końcowych otwarte dla serwera DNS, to nie przejdzie usługi w chmurze, a nie dociera do serwera
3. Jeśli punktów końcowych, które były otwarte jakiegoś powodu, reguły NSG 5 (Internet do sieci wirtualnej) czy zablokować ruch (Uwaga: nie ma zastosowania tej reguły 1 (DNS) z dwóch przyczyn, najpierw adresu źródłowego jest internet, ta reguła ma zastosowanie tylko do lokalnej sieci wirtualnej jako źródło, również jest Reguła zezwalająca, nigdy nie będzie go odmówić ruchu)

#### <a name="denied-web-to-sql-access-through-firewall"></a>(Odmowa) Aby SQL dostęp za pośrednictwem zapory w sieci Web
1. Internet użytkownik żąda danych SQL z FrontEnd001.CloudApp.Net (Internet ukierunkowane usługa w chmurze)
2. Ponieważ nie ma żadnych punktów końcowych otwarte dla bazy danych SQL, to nie przejdzie usługi w chmurze i nie osiągnąć zapory
3. Jeśli punkty końcowe zostały otwarte jakiegoś powodu, podsieci frontonu rozpoczyna przetwarzanie przychodzącej reguły:
   1. Reguły NSG 1 DNS (Domain Name System) nie są spełnione, przejść do następnej reguły
   2. 2 reguły NSG (RDP) nie są spełnione, przejść do następnej reguły
   3. Zastosuj 2 reguły NSG (Internet zaporą), ruch jest dozwolony, stop reguły przetwarzania
4. Ruch trafienia wewnętrzny adres IP zapory (10.0.1.4)
5. Zapora nie ma przekazywanie reguł dla bazy danych SQL i porzuca ruchu

## <a name="conclusion"></a>Podsumowanie
Jest to stosunkowo proste do przodu sposób ochrony aplikacji za pomocą zapory i izolowanie podsieci wewnętrznej z ruchu przychodzącego.

Więcej przykładów i Przegląd granic zabezpieczeń sieci można znaleźć [tutaj][HOME].

## <a name="references"></a>Dokumentacja
### <a name="main-script-and-network-config"></a>Skrypt głównego i konfiguracji sieci
Zapisz skrypt pełna w pliku skryptu programu PowerShell. Zapisz konfigurację sieci w pliku o nazwie "NetworkConf2.xml".
Modyfikuj zmienne zdefiniowane przez użytkownika, zgodnie z potrzebami. Uruchom skrypt, a następnie postępuj zgodnie z instrukcjami instalacji reguły zapory powyżej.

#### <a name="full-script"></a>Pełna skryptu
Ten skrypt zostanie, na podstawie zmiennych zdefiniowanych przez użytkownika:

1. Łączenie się z subskrypcją platformy Azure
2. Utwórz nowe konto magazynu
3. Utwórz nową sieć wirtualną z dwoma podsieciami, zgodnie z definicją w pliku konfiguracji sieci
4. Tworzenie maszyn wirtualnych serwera 4 windows
5. Skonfiguruj grupy NSG w tym:
   * Tworzenie grupy NSG
   * Zapełnianie reguły
   * Powiązanie grupy NSG z odpowiednich podsieci

Ten skrypt programu PowerShell powinien zostać uruchomiony lokalnie na się, że połączenie internetowe, komputera lub serwera.

> [!IMPORTANT]
> Uruchomienie tego skryptu można ostrzeżenia lub inne komunikaty informacyjne, które pop w programie PowerShell. Tylko komunikaty o błędach na czerwono są przyczyną problemu.
> 
> 

    <# 
     .SYNOPSIS
      Example of DMZ and Network Security Groups in an isolated network (Azure only, no hybrid connections)

     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Two new cloud services
       - Two Subnets (FrontEnd and BackEnd subnets)
       - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
       - One server on the FrontEnd Subnet (plus the NVA on the FrontEnd subnet)
       - Three Servers on the BackEnd Subnet
       - Network Security Groups to allow/deny traffic patterns as declared

      Before running script, ensure the network configuration file is created in
      the directory referenced by $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).

     .Notes
      Security requirements are different for each use case and can be addressed in a
      myriad of ways. Please be sure that any sensitive data or applications are behind
      the appropriate layer(s) of protection. This script serves as an example of some
      of the techniques that can be used, but should not be used for all scenarios. You
      are responsible to assess your security needs and the appropriate protections
      needed, and then effectively implement those protections.

      FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
       myFirewall - 10.0.1.4
       IIS01      - 10.0.1.5

      BackEnd Service (BackEnd subnet 10.0.2.0/24)
       DNS01      - 10.0.2.4
       AppVM01    - 10.0.2.5
       AppVM02    - 10.0.2.6

    #>

    # Fixed Variables
        $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
        $VMName = @()
        $ServiceName = @()
        $VMFamily = @()
        $img = @()
        $size = @()
        $SubnetName = @()
        $VMIP = @()

    # User Defined Global Variables
      # These should be changes to reflect your subscription and services
      # Invalid options will fail in the validation section

      # Subscription Access Details
        $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

      # VM Account, Location, and Storage Details
        $LocalAdmin = "theAdmin"
        $DeploymentLocation = "Central US"
        $StorageAccountName = "vmstore02"

      # Service Details
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"

      # Network Details
        $VNetName = "CorpNetwork"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf2.xml"

      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}

      # NSG Details
        $NSGName = "MyVNetSG"

    # User Defined VM Specific Config
        # Note: To ensure proper NSG Rule creation later in this script:
        #       - The Web Server must be VM 1
        #       - The AppVM1 Server must be VM 2
        #       - The DNS server must be VM 4
        #
        #       Otherwise the NSG rules in the last section of this
        #       script will need to be changed to match the modified
        #       VM array numbers ($i) so the NSG Rule IP addresses
        #       are aligned to the associated VM IP addresses.

        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $FrontEndService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"

        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.5"

        # VM 2 - The First Appliaction Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"

        # VM 3 - The Second Appliaction Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"

        # VM 4 - The DNS Server
          $VMName += "DNS01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.4"

    # ----------------------------- #
    # No User Defined Varibles or   #
    # Configuration past this point #
    # ----------------------------- #

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

      # Create Storage Account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}

      # Update Subscription Pointer to New Storage Account
        Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
        Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop

    # Validation
    $FatalError = $false

    If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
         Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
         $FatalError = $true}

    If (Test-AzureName -Service -Name $FrontEndService) { 
        Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}

    If (Test-AzureName -Service -Name $BackEndService) { 
        Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}

    If (-Not (Test-Path $NetworkConfigFile)) { 
        Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The network config file was found" -ForegroundColor Green
            If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation varible is correct and the netowrk config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}

    If ($FatalError) {
        Write-Host "A fatal error has occured, please see the above messages for more information." -ForegroundColor Red
        Return}
    Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}

    # Create VNET
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop

    # Create Services
        Write-Host "Creating Services" -ForegroundColor Cyan
        New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop

    # Build VMs
        $i=0
        $VMName | Foreach {
            Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
            If ($VMFamily[$i] -eq "Firewall") 
                { 
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Set up all the EndPoints we'll need once we're up and running
                # Note: Web traffic goes through the firewall, so we'll need to set up a HTTP endpoint.
                #       Also, the firewall will be redirecting web traffic to a new IP and Port in a
                #       forwarding rule, so the HTTP endpoint here will have the same public and local
                #       port and the firewall will do the NATing and redirection as declared in the
                #       firewall rule.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                    # Note: A Remote Desktop endpoint is automatically created when each VM is created.
                }
            $i++
        }

    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan

      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"

      # Add NSG Rules
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
            -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[4] -DestinationPortRange '53' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
            -SourceAddressPrefix Internet -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[1]) to $($VMName[2])" -Type Inbound -Priority 130 -Action Allow `
            -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[2] -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 140 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" -Type Inbound -Priority 150 -Action Deny `
            -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
            -DestinationAddressPrefix $BEPrefix -DestinationPortRange '*' `
            -Protocol *

        # Assign the NSG to the Subnets
            Write-Host "Binding the NSG to both subnets" -ForegroundColor Cyan
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

    # Optional Post-script Manual Configuration
      # Configure Firewall
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install Backend resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host


#### <a name="network-config-file"></a>Plik konfiguracji sieci
Zapisz ten plik xml z lokalizacji zaktualizowane i dodać link do tego pliku do zmiennej $NetworkConfigFile w skrypcie powyżej.

    <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <Dns>
          <DnsServers>
            <DnsServer name="DNS01" IPAddress="10.0.2.4" />
            <DnsServer name="Level3" IPAddress="209.244.0.3" />
          </DnsServers>
        </Dns>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="CorpNetwork" Location="Central US">
            <AddressSpace>
              <AddressPrefix>10.0.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="FrontEnd">
                <AddressPrefix>10.0.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>10.0.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
            <DnsServersRef>
              <DnsServerRef name="DNS01" />
              <DnsServerRef name="Level3" />
            </DnsServersRef>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>

#### <a name="sample-application-scripts"></a>Przykładowe skrypty aplikacji
Jeśli chcesz zainstalować przykładową aplikację dla tego i innych przykładów sieci obwodowej, jeden podano przy użyciu następującego łącza: [przykładowy skrypt aplikacji][SampleApp]

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-asm/example2design.png "Przychodząca sieć obwodowa z grupy NSG"
[2]: ./media/virtual-networks-dmz-nsg-fw-asm/dstnaticon.png "Ikona NAT docelowego"
[3]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallrule.png "Reguły zapory"
[4]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallruleactivate.png "Aktywacja reguły zapory"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
[Example1]: ./virtual-networks-dmz-nsg-asm.md
