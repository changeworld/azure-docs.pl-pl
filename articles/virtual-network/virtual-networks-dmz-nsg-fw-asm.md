---
title: Przykład sieci obwodowej — kompilacja sieci obwodowej do ochrony aplikacji z zaporą i sieciowymi grupami zabezpieczeń | Dokumentacja firmy Microsoft
description: Tworzenie sieci obwodowej z zaporą i sieciowymi grupami zabezpieczeń (NSG)
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
ms.openlocfilehash: c1c64945aaa0bc4cd83cc769dab1c2a755896c01
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2019
ms.locfileid: "65603424"
---
# <a name="example-2-build-a-perimeter-network-to-protect-applications-with-a-firewall-and-nsgs"></a>Przykład 2: Tworzenie sieci obwodowej do ochrony aplikacji z zaporą i sieciowymi grupami zabezpieczeń
[Wróć do usług chmurowych firmy Microsoft i Strona zabezpieczeń sieci][HOME]

W tym przykładzie przedstawiono sposób tworzenia sieci obwodowej (znanej także jako *DMZ*, *strefą zdemilitaryzowaną*, i *podsiecią ekranowaną*) za pomocą zapory, cztery komputery serwerów systemu Windows, i sieciowe grupy zabezpieczeń (NSG). Zawiera szczegółowe informacje o poszczególnych odpowiednie polecenia, aby zapewnić lepiej zrozumieć każdego kroku. Sekcja "Scenariusze ruchu" zawiera instrukcje krok po kroku wyjaśnienie, w jaki sposób ruch przechodzi przez warstwy obrony w sieci obwodowej. Na koniec sekcji "Odwołań" zawiera kompletny kod i instrukcje dotyczące sposobu tworzenia tego środowiska do testowania i eksperymentowania z różnymi scenariuszami.

![Dla ruchu przychodzącego sieci obwodowej przy użyciu urządzeń WUS i sieciowymi grupami zabezpieczeń][1]

## <a name="environment"></a>Środowisko 
Ten przykład jest oparty na scenariuszu z subskrypcją platformy Azure, która zawiera następujące elementy:

* Dwie usługi w chmurze: FrontEnd001 i BackEnd001.
* Sieć wirtualną o nazwie CorpNetwork, która ma dwie podsieci: Frontonu i zaplecza.
* Jednej sieciowej grupy zabezpieczeń, jest stosowany do obu podsieci.
* Wirtualne urządzenie sieciowe: Barracuda NextGen Firewall podłączone do podsieci frontonu.
* Komputer systemu Windows Server, który reprezentuje serwer aplikacji sieci web: IIS01.
* Dwa komputery serwera Windows, które reprezentują serwery zaplecza aplikacji: AppVM01 i AppVM02.
* Komputer systemu Windows Server, który reprezentuje serwer DNS: DNS01.

> [!NOTE]
> Mimo że w tym przykładzie użyto Barracuda NextGen Firewall, można użyć wielu wirtualnych urządzeń sieciowych.
> 
> 

Skrypt programu PowerShell w części "Informacje" w tym artykule tworzy większość środowiska opisane w tym miejscu. Maszyny wirtualne i sieci wirtualne są tworzone przy użyciu skryptu, ale te procesy nie są szczegółowo opisane w tym dokumencie.

Aby skompilować środowiska:

1. Zapisz plik XML konfiguracji sieci znajdujących się w części "Informacje" (zaktualizowane przy użyciu nazwy, lokalizacji i adresów IP adresy, które mają odpowiada Twojemu scenariuszowi).
2. Zaktualizuj zmienne zdefiniowane przez użytkownika w skrypcie programu PowerShell, aby dopasować środowiska, w których skrypt będzie uruchamiany przed (subskrypcji, nazwy usługi i tak dalej).
3. Uruchom skrypt programu PowerShell.

> [!NOTE]
> Region określonego w skrypcie programu PowerShell musi odpowiadać określony w pliku XML konfiguracji sieci.
>
>

Po pomyślnym uruchomieniu skryptu możesz wykonać następujące czynności:

1. Konfigurowanie reguł zapory. Zobacz sekcję "Reguły zapory" tego artykułu.
2. Jeśli chcesz, możesz skonfigurować serwer sieci web i serwera aplikacji przy użyciu prostej aplikacji sieci web umożliwia testowanie za pomocą konfiguracji sieci obwodowej. Można użyć skryptów dwoma aplikacji podanego w sekcji "Odwołania".

W następnej sekcji objaśniono większość instrukcji skryptu, które odnoszą się do sieciowych grup zabezpieczeń.

## <a name="nsgs"></a>sieciowych grup zabezpieczeń
W tym przykładzie grupa sieciowej grupy zabezpieczeń jest utworzone i następnie ładowany za pomocą sześciu reguł.

> [!TIP]
> Ogólnie rzecz biorąc należy najpierw utworzyć określone reguły "Zezwalaj", a ostatni bardziej ogólnymi zasadami "Deny". Formanty priorytetem, których zasady są oceniane pierwszy. Po znalezieniu ruchu, która odnosi się do określonej reguły, nie dodatkowe reguły są oceniane. Reguły sieciowej grupy zabezpieczeń można stosować w przychodzący lub wychodzący kierunek (z punktu widzenia podsieci).
> 
> 

Deklaratywne te reguły są tworzone dla ruchu przychodzącego:

1. Ruch DNS wewnętrzny (port 53) jest dozwolone.
2. Ruch protokołu RDP (port 3389) z Internetu do dowolnej maszyny Wirtualnej jest dozwolony.
3. Ruch HTTP (port 80) z Internetu do urządzenia WUS (zapora) jest dozwolone.
4. Cały ruch (wszystkich portów) z IIS01 do AppVM01 jest dozwolone.
5. Cały ruch (wszystkich portów) z Internetu do całej sieci wirtualnej (zarówno podsieci) zostanie odrzucone.
6. Cały ruch (wszystkich portów) z podsieci FrontEnd do podsieci zaplecza jest odrzucane.

Przy użyciu tych reguł powiązany do każdej podsieci, gdyby żądania HTTP dla ruchu przychodzącego z Internetu do serwera sieci web, zarówno reguła 3 (Zezwalaj na) i reguły 5 (odmowa), wydaje się do zastosowania, ale ponieważ reguła 3 ma wyższy priorytet, tylko będzie stosowana. Reguła 5 nie dochodzą do głosu. Dlatego zapory możliwość żądania HTTP.

Jeśli ten sam ruch próbował uzyskać dostęp do serwera DNS01, zasada 5 (odmowa) będzie pierwszy do zastosowania, dzięki czemu ruch nie można przekazać do serwera. Reguła 6 (odmowa) blokuje podsieć frontonu komunikację z podsieci wewnętrznej bazy danych (z wyjątkiem ruch w regułach 1 i 4). Chroni to sieci wewnętrznej bazy danych, w przypadku, gdy osoba atakująca obniża aplikacji sieci web we frontonie. W takim przypadku osoba atakująca będzie ograniczony dostęp do sieci wewnętrznej bazy danych "chronione". (Osoba atakująca może mieć możliwość dostępu tylko do zasobów, które są udostępniane na serwerze AppVM01).

Brak domyślną regułę ruchu wychodzącego, która umożliwia ruchu wychodzącego z Internetem. W tym przykładzie firma Microsoft zezwala na ruch wychodzący i nie modyfikowanie żadnych reguł dla ruchu wychodzącego. Aby zablokować ruch w obu kierunkach, musisz mieć routingu zdefiniowanego przez użytkownika. Informacje na temat tej techniki w innym przykładzie w [dokumentu granic zabezpieczeń głównych][HOME].

Reguły sieciowej grupy zabezpieczeń opisane w tym miejscu są podobne do reguł sieciowej grupy zabezpieczeń w [przykład 1 — Tworzenie prosta strefa DMZ z sieciowymi grupami zabezpieczeń][Example1]. Przejrzyj opis sieciowej grupy zabezpieczeń, w tym artykule, aby wyświetlić szczegółowy widok każdej reguły sieciowej grupy zabezpieczeń i jego atrybuty.

## <a name="firewall-rules"></a>Reguły zapory
Musisz zainstalować klienta zarządzania na komputerze do zarządzania zaporą i tworzyć konfiguracje wymagane. Zobacz dostawcę dokumentacji zapory (lub innego urządzenia WUS) o tym, jak do zarządzania urządzeniem. W pozostałej części tej sekcji opisano konfigurację zapory, za pomocą dostawcy zarządzania klienta (nie witryny Azure portal lub programu PowerShell).

Zobacz [Barracuda NG administratora](https://techlib.barracuda.com/NG61/NGAdmin) instrukcje dotyczące pobierania klienta i nawiązywania połączenia z zapory Barracuda, używany w tym przykładzie.

Musisz utworzyć zasady przekazywania na zaporze. Ponieważ w ramach scenariusza opisywanego w tym przykładzie tylko kieruje ruch z Internetu dla ruchu przychodzącego zapory i serwer sieci web, wystarczy przekazywania jednej reguły translatora adresów Sieciowych. Zapory Barracuda, używany w tym przykładzie reguła będzie regułę docelowego translatora adresów Sieciowych (NAT Dst) do przekazania tego ruchu.

Aby utworzyć następującą regułę (lub sprawdzić istniejące reguły domyślne), wykonaj następujące kroki:
1. Na pulpicie nawigacyjnym klienta administrator NG Barracuda na karcie Konfiguracja w **konfigurację operacyjną** zaznacz **zestaw reguł**. 

   Siatka o nazwie **reguły Main** pokazuje istniejący aktywny i dezaktywowane reguły zapory.

2. Aby utworzyć nową regułę, wybierz zielony małych **+** przycisk w prawym górnym rogu tej siatki. (Zapora może być zablokowany. Jeśli zostanie wyświetlony przycisk oznaczony **blokady** i nie można utworzyć lub edytować reguły, wybierz przycisk, aby odblokować zestaw reguł i Zezwól na edytowanie.)
  
3. Aby edytować istniejącą regułę, wybierz regułę, kliknij prawym przyciskiem myszy, a następnie wybierz **edytowanie reguły**.

Utwórz nową regułę o nazwie podobny **WebTraffic.** 

Ikona reguły translatora adresów Sieciowych docelowego wygląda następująco: ![Ikona translatora adresów Sieciowych docelowego][2]

Reguła sam będą wyglądać mniej więcej tak:

![Reguły zapory][3]

Każdego przychodzącego adresu, który trafienia zapory próba nawiązania połączenia HTTP (port 80 i 443 dla protokołu HTTPS) zostaną wysłane poza interfejsu lokalnego adresu IP komputera DHCP1 zapory i Przekierowanie do serwera sieci web o adresie IP 10.0.1.5. Ponieważ przychodzącym ruch na porcie 80 i przesyłane na serwer sieci web na porcie 80, nie zmiany portu nie jest wymagana. Ale listy docelowej mogło być 10.0.1.5:8080 Jeśli serwer sieci web posłuchaliśmy na porcie 8080, przetłumaczy przychodzący port 80 w zaporze na przychodzący port 8080 na serwerze sieci web.

Należy także określić metodę połączenia. Reguła docelowa z Internetu SNAT dynamicznych jest najbardziej odpowiednią metodę.

Mimo, że po utworzeniu tylko jedną regułę, należy poprawnie ustawić jego priorytetu. W siatce wszystkie reguły zapory w przypadku tej nowej reguły u dołu (poniżej reguła BLOCKALL) go nigdy nie pojawią się do gry. Upewnij się, że powyżej reguły BLOCKALL nową regułę dla ruchu w sieci web.

Po utworzeniu reguły należy wypchnąć go do zapory, a następnie aktywować go. Jeśli użytkownik nie wykona następujące czynności, zmień reguły zaczyna obowiązywać. W następnej sekcji opisano proces wypychania i aktywacji.

## <a name="rule-activation"></a>Reguła aktywacji
Teraz, że reguła jest dodawana do zestaw reguł, musisz przekazać zestaw reguł zapory i aktywuj go.

![Aktywacja reguły zapory][4]

W prawym górnym rogu klienta zarządzania zobaczysz grupę przycisków. Wybierz **wysłać zmiany** do wysyłania zmodyfikowane zestaw reguł zapory, a następnie wybierz **Aktywuj**.

Teraz, gdy aktywowano zestaw reguł zapory, środowisko jest pełny. Przykładowe skrypty aplikację można uruchomić w sekcji "Odwołania do", aby dodać aplikację do środowiska. Po dodaniu aplikacji, możesz przetestować scenariusze ruchu opisane w następnej sekcji.

> [!IMPORTANT]
> Należy należy pamiętać, że zostanie osiągnięta serwer sieci web bezpośrednio. Gdy przeglądarka zgłasza strony HTTP z FrontEnd001.CloudApp.Net, punkt końcowy HTTP (port 80) przekazuje ruch do zapory, a nie do serwera sieci web. Zapora, ze względu na reguły, która została utworzona wcześniej, używa translatora adresów Sieciowych do mapowania żądania do serwera sieci web.
> 
> 

## <a name="traffic-scenarios"></a>Scenariusze ruchu
#### <a name="allowed-web-to-web-server-through-the-firewall"></a>(Dozwolone) Do serwera sieci web przez zaporę w sieci Web
1. Użytkownik internet żąda strony HTTP z FrontEnd001.CloudApp.Net (połączone z Internetem usługi w chmurze).
2. Usługi w chmurze przekazuje ruch przez otwarty punkt końcowy na porcie 80 do interfejsu lokalnego zapory na 10.0.1.4:80.
3. Podsieć frontonu rozpoczyna przetwarzanie reguł dla ruchu przychodzącego:
   1. Nie ma zastosowania reguły sieciowej grupy zabezpieczeń 1 (DNS). Przenieś na następną regułę.
   2. Nie ma zastosowania reguły sieciowej grupy zabezpieczeń 2 (RDP). Przenieś na następną regułę.
   3. Zastosuj reguły sieciowej grupy zabezpieczeń 3 (internet do zapory). Zezwalać na ruch. Zatrzymaj przetwarzanie reguł.
4. Ruch osiąga wewnętrzny adres IP zapory (10.0.1.4).
5. Zapora regułę przesyłania określa, czy to jest ruch na porcie 80 i przekieruje go do serwera sieci web IIS01.
6. IIS01 nasłuchuje ruchu w sieci web, odbiera żądanie, a następnie rozpoczyna przetwarzanie żądania.
7. IIS01 żąda informacji z wystąpienia programu SQL Server na AppVM01.
8. Brak reguł ruchu wychodzącego w podsieci frontonu, więc ruch jest dozwolony.
9. Podsieci wewnętrznej bazy danych rozpoczyna przetwarzanie reguł dla ruchu przychodzącego:
   1. Nie ma zastosowania reguły sieciowej grupy zabezpieczeń 1 (DNS). Przenieś na następną regułę.
   2. Nie ma zastosowania reguły sieciowej grupy zabezpieczeń 2 (RDP). Przenieś na następną regułę.
   3. Nie ma zastosowania reguły sieciowej grupy zabezpieczeń 3 (internet do zapory). Przenieś na następną regułę.
   4. Reguły sieciowej grupy zabezpieczeń 4 (IIS01 do AppVM01) ma zastosowania. Zezwalać na ruch. Zatrzymaj przetwarzanie reguł.
10. Wystąpienie programu SQL Server na AppVM01 odbiera żądanie i odpowiada.
11. Ponieważ w podsieci wewnętrznej bazy danych nie zawiera żadnych reguł dla ruchu wychodzącego, odpowiedź jest dozwolone.
12. Podsieć frontonu rozpoczyna przetwarzanie reguł dla ruchu przychodzącego:
    1. Istnieje nie reguły sieciowej grupy zabezpieczeń, która ma zastosowanie do przychodzący ruch z podsieci zaplecza do podsieci frontonu, więc żaden z reguły sieciowej grupy zabezpieczeń.
    2. Reguła systemowa domyślnej zezwalającej na ruch pomiędzy podsieciami umożliwia ten ruch, ruch jest dozwolony.
13. IIS01 odbiera odpowiedź od AppVM01 kończy odpowiedź HTTP i wysyła je do osoby zgłaszającej żądanie.
14. Ponieważ jest to sesji translatora adresów Sieciowych z zaporą, miejsce docelowe odpowiedzi jest początkowo zapory.
15. Zapora odbiera odpowiedź z serwera sieci web i przekazuje go do użytkownika internet.
16. Ponieważ nie zawiera żadnych reguł ruchu wychodzącego w podsieci frontonu, odpowiedź jest dozwolone, a użytkownik internet otrzymuje strony sieci web.

#### <a name="allowed-rdp-to-backend"></a>(Dozwolone) Protokół RDP do wewnętrznej bazy danych
1. Administrator serwera w Internecie żądań sesję RDP do AppVM01 na BackEnd001.CloudApp.Net:*xxxxx*, gdzie *xxxxx* jest liczbą losowo przypisany port dla protokołu RDP do AppVM01. (Można znaleźć przypisanego portu w witrynie Azure portal lub przy użyciu programu PowerShell).
2. Ponieważ zapory nasłuchuje tylko adres FrontEnd001.CloudApp.Net, nie jest zaangażowana z tym przepływem ruchu sieciowego.
3. Podsieci wewnętrznej bazy danych rozpoczyna przetwarzanie reguł dla ruchu przychodzącego:
   1. Nie ma zastosowania reguły sieciowej grupy zabezpieczeń 1 (DNS). Przenieś na następną regułę.
   2. Zastosuj reguły sieciowej grupy zabezpieczeń 2 (RDP). Zezwalać na ruch. Zatrzymaj przetwarzanie reguł.
4. Ponieważ nie zawiera żadnych reguł dla ruchu wychodzącego, domyślne reguły zastosowania i zwracać ruch jest dozwolony.
5. Sesję RDP jest włączona.
6. AppVM01 monituje o podanie nazwy użytkownika i hasła.

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Dozwolone) Wyszukiwanie DNS serwera sieci Web na serwerze DNS
1. Musi IIS01, serwera sieci web źródła danych na www.data.gov danych, ale musi rozpoznać adres.
2. W konfiguracji sieci w sieci wirtualnej przedstawiono DNS01 (10.0.2.4 w podsieci wewnętrznej bazy danych) jako podstawowy serwer DNS. IIS01 spowoduje wysłanie żądania DNS DNS01.
3. Ponieważ nie zawiera żadnych reguł ruchu wychodzącego w podsieci frontonu, ruch jest dozwolony.
4. Podsieci wewnętrznej bazy danych rozpoczyna przetwarzanie reguł dla ruchu przychodzącego:
   1. Ma zastosowanie reguła sieciowej grupy zabezpieczeń 1 (DNS). Zezwalać na ruch. Zatrzymaj przetwarzanie reguł.
5. Serwer DNS odbiera żądanie.
6. Serwer DNS nie ma adresu pamięci podręcznej i zapytania główny serwer DNS w Internecie.
7. Ponieważ w podsieci wewnętrznej bazy danych nie zawiera żadnych reguł dla ruchu wychodzącego, ruch jest dozwolony.
8. DNS w sieci internet serwer odpowiada. Odpowiedź jest dozwolona, ponieważ wewnętrznie zainicjowaniu sesji.
9. Serwer DNS będzie buforować odpowiedź, odpowiada na żądania IIS01.
10. Ponieważ w podsieci wewnętrznej bazy danych nie zawiera żadnych reguł dla ruchu wychodzącego, ruch jest dozwolony.
11. Podsieć frontonu rozpoczyna przetwarzanie reguł dla ruchu przychodzącego:
    1. Istnieje nie reguły sieciowej grupy zabezpieczeń, która ma zastosowanie do przychodzący ruch z podsieci zaplecza do podsieci frontonu, więc żaden z reguły sieciowej grupy zabezpieczeń.
    2. Reguła systemowa domyślnej zezwalającej na ruch pomiędzy podsieciami umożliwia ten ruch, więc ruch jest dozwolony.
12. IIS01 odbiera odpowiedź od DNS01.

#### <a name="allowed-web-server-file-access-on-appvm01"></a>(Dozwolone) Dostęp do plików serwera sieci Web na AppVM01
1. IIS01 żąda pliku na AppVM01.
2. Ponieważ nie zawiera żadnych reguł ruchu wychodzącego w podsieci frontonu, ruch jest dozwolony.
3. Podsieci wewnętrznej bazy danych rozpoczyna przetwarzanie reguł dla ruchu przychodzącego:
   1. Nie ma zastosowania reguły sieciowej grupy zabezpieczeń 1 (DNS). Przenieś na następną regułę.
   2. Nie ma zastosowania reguły sieciowej grupy zabezpieczeń 2 (RDP). Przenieś na następną regułę.
   3. Nie ma zastosowania reguły sieciowej grupy zabezpieczeń 3 (internet do zapory). Przenieś na następną regułę.
   4. Reguły sieciowej grupy zabezpieczeń 4 (IIS01 do AppVM01) ma zastosowania. Zezwalać na ruch. Zatrzymaj przetwarzanie reguł.
4. AppVM01 odbiera żądanie i odpowiada za pomocą pliku (przy założeniu, że dostęp jest autoryzowany).
5. Ponieważ w podsieci wewnętrznej bazy danych nie zawiera żadnych reguł dla ruchu wychodzącego, odpowiedź jest dozwolone.
6. Podsieć frontonu rozpoczyna przetwarzanie reguł dla ruchu przychodzącego:
   1. Istnieje nie reguły sieciowej grupy zabezpieczeń, która ma zastosowanie do przychodzący ruch z podsieci zaplecza do podsieci frontonu, więc żaden z reguły sieciowej grupy zabezpieczeń.
   2. Reguła systemowa domyślnej zezwalającej na ruch pomiędzy podsieciami umożliwia ten ruch, więc ruch jest dozwolony.
7. IIS01 odbiera pliku.

#### <a name="denied-web-direct-to-web-server"></a>(Odrzucony) Bezpośrednio do serwera sieci web w sieci Web
Ponieważ serwer sieci web IIS01 i zapory znajdują się w tej samej usłudze w chmurze, współużytkują one ten sam adres IP publicznego. Dlatego cały ruch HTTP jest kierowany do zapory. Gdy żądanie będzie służyć pomyślnie, wartość nie może przejść bezpośrednio do serwera sieci web. Przekazuje, zgodnie z założeniami przez zaporę najpierw. Zobacz pierwszego scenariusza, w tej sekcji dla ruchu przychodzącego.

#### <a name="denied-web-to-backend-server"></a>(Odrzucony) Do serwera wewnętrznej bazy danych w sieci Web
1. Użytkownik internet próbuje dostęp do pliku na AppVM01 za pośrednictwem usługi BackEnd001.CloudApp.Net.
2. Ponieważ nie zawiera żadnych punktów końcowych otwarte na potrzeby udostępniania plików, to nie będzie przekazywać usługi w chmurze i nie dociera do serwera.
3. Jeśli punkty końcowe są otwarte niektóre przyczyny, reguła sieciowej grupy zabezpieczeń 5 (internet z siecią wirtualną) blokuje ruch.

#### <a name="denied-web-dns-lookup-on-the-dns-server"></a>(Odrzucony) Wyszukiwanie DNS w sieci Web na serwerze DNS
1. Użytkownik internet próbuje wyszukać wewnętrzny rekord DNS na DNS01 za pośrednictwem usługi BackEnd001.CloudApp.Net.
2. Ponieważ punkty końcowe nie są otwarte w systemie DNS, to nie będzie przekazywać usługi w chmurze i nie dociera do serwera.
3. Jeśli punkty końcowe są otwarte niektóre przyczyny, reguła sieciowej grupy zabezpieczeń 5 (internet z siecią wirtualną) blokuje ruch. (Reguła 1 [DNS] nie ma zastosowania w dwóch powodów. Najpierw adres źródłowy jest internet, a ta reguła ma zastosowanie tylko wtedy, gdy lokalnej sieci wirtualnej jest źródłem. Po drugie, ta reguła ma regułę zezwalającą tak go nigdy nie zezwala na ruch.)

#### <a name="denied-web-to-sql-access-through-the-firewall"></a>(Odrzucony) Aby SQL dostęp przez zaporę w sieci Web
1. Użytkownik internet żąda danych serwera SQL z FrontEnd001.CloudApp.Net (usługi w chmurze połączone z Internetem).
2. Ponieważ punkty końcowe nie są otwarte dla programu SQL, to nie będzie przekazywać usługi w chmurze i nie będzie dotrzeć do zapory.
3. Jeśli punkty końcowe są otwarte niektóre przyczyny, z podsiecią FrontEnd rozpoczyna przetwarzanie reguł dla ruchu przychodzącego:
   1. Nie ma zastosowania reguły sieciowej grupy zabezpieczeń 1 (DNS). Przenieś na następną regułę.
   2. Nie ma zastosowania reguły sieciowej grupy zabezpieczeń 2 (RDP). Przenieś na następną regułę.
   3. Zastosuj reguły sieciowej grupy zabezpieczeń 3 (internet do zapory). Zezwalać na ruch. Zatrzymaj przetwarzanie reguł.
4. Ruch osiąga wewnętrzny adres IP zapory (10.0.1.4).
5. Zapora nie ma żadnych reguł przekazywania do języka SQL i odrzuca ruch.

## <a name="conclusion"></a>Podsumowanie
Ten przykład przedstawia stosunkowo prosty sposób ochrony aplikacji z zaporą i odizolowania podsieci zaplecza z ruchu przychodzącego.

Możesz znaleźć więcej przykładów i Przegląd sieci granic zabezpieczeń [tutaj][HOME].

## <a name="references"></a>Odwołania
### <a name="full-script-and-network-config"></a>Pełna Konfiguracja skryptu i sieci
Pełny skrypt należy zapisać w pliku skryptu programu PowerShell. Zapisz skrypt konfiguracji sieci w pliku o nazwie NetworkConf2.xml.
Zmień zdefiniowane — zmienne użytkownika, zgodnie z potrzebami. Uruchom skrypt, a następnie postępuj zgodnie z instrukcjami w sekcji "Reguły zapory" tego artykułu.

#### <a name="full-script"></a>Pełny skrypt
Ten skrypt na podstawie zmiennych zdefiniowanych przez użytkownika będzie wykonaj następujące czynności:

1. Połącz z subskrypcją platformy Azure.
2. Utwórz konto magazynu.
3. Utwórz sieć wirtualną i dwie podsieci, zgodnie z definicją w pliku konfiguracji sieci.
4. Twórz cztery maszyny wirtualne bramy Windows Server.
5. Konfigurowanie sieciowej grupy zabezpieczeń. Konfiguracja zostanie ukończona następujące kroki:
   * Tworzy sieciową grupę zabezpieczeń.
   * Wypełnia sieciowej grupy zabezpieczeń z regułami.
   * Tworzy powiązanie odpowiednie podsieci sieciową grupę zabezpieczeń.

Ten skrypt programu PowerShell należy uruchamiać lokalnie na serwerze lub komputerze połączonym z Internetem.

> [!IMPORTANT]
> Po uruchomieniu tego skryptu, ostrzeżenia i inne komunikaty informacyjne, które mogą być wyświetlane w programie PowerShell. Musisz mieć wątpliwości dotyczące komunikatów o błędach, które są wyświetlane w kolorze czerwonym.
> 
> 

```powershell
    <# 
     .SYNOPSIS
      Example of a perimeter network and Network Security Groups in an isolated network. (Azure only. No hybrid connections.)

     .DESCRIPTION
      This script will build out a sample perimeter network setup containing:
       - A default storage account for VM disks.
       - Two new cloud services.
       - Two subnets (the FrontEnd and BackEnd subnets).
       - A network virtual appliance (NVA): a Barracuda NextGen Firewall.
       - One server on the FrontEnd subnet (plus the NVA on the FrontEnd subnet).
       - Three servers on the BackEnd subnet.
       - Network Security Groups to allow/deny traffic patterns as declared.

      Before running the script, ensure the network configuration file is created in
      the directory referenced by the $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).

     .Notes
      Security requirements are different for each use case and can be addressed in many ways. Be sure that any sensitive data or applications are behind
      the appropriate layer(s) of protection. This script serves as an example of some
      of the techniques that you can use, but it should not be used for all scenarios. You
      are responsible for assessing your security needs and the appropriate protections
      and then effectively implementing those protections.

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

    # User-Defined Global Variables
      # These should be changed to reflect your subscription and services.
      # Invalid options will fail in the validation section.

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

    # User-Defined VM-Specific Config
        # To ensure proper NSG rule creation later in this script:
        #       - The web server must be VM 1.
        #       - The AppVM1 server must be VM 2.
        #       - The DNS server must be VM 4.
        #
        #       Otherwise the NSG rules in the last section of this
        #       script will need to be changed to match the modified
        #       VM array numbers ($i) so the NSG rule IP addresses
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

        # VM 2 - The First Application Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"

        # VM 3 - The Second Application Server
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
    # No user-defined variables or   #
    # configuration past this point #
    # ----------------------------- #

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

      # Create storage account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}

      # Update subscription pointer to new storage account
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
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation variable is correct and the network config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}

    If ($FatalError) {
        Write-Host "A fatal error has occurred, please see the above messages for more information." -ForegroundColor Red
        Return}
    Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}

    # Create virtual network
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop

    # Create services
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
                # Set up all the endpoints we'll need once we're up and running.
                # Note: Web traffic goes through the firewall, so we'll need to set up an HTTP endpoint.
                #       Also, the firewall will be redirecting web traffic to a new IP and port in a
                #       forwarding rule, so the HTTP endpoint here will have the same public and local
                #       port and the firewall will do the NATing and redirection as declared in the
                #       firewall rule.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: An SSH endpoint is automatically created on port 22 when the appliance is created.
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

      # Add NSG rules
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

        # Assign the NSG to the subnets
            Write-Host "Binding the NSG to both subnets" -ForegroundColor Cyan
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

    # Optional Post-Script Manual Configuration
      # Configure firewall
      # Install test web app (run post-build script on the IIS server)
      # Install back-end resources (run post-build script on AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host
```

#### <a name="network-config-file"></a>Plik konfiguracji sieci
Zapisz ten plik XML przy użyciu zaktualizowanych lokalizacji, a następnie dodać łącze do tego pliku w zmiennej $NetworkConfigFile w poprzednim skrypcie.

```xml
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
```

#### <a name="sample-application-scripts"></a>Przykładowe skrypty aplikacji
Jeśli chcesz zainstalować przykładową aplikację dla tego programu oraz inne przykłady sieci obwodowej, zobacz [przykładowy skrypt aplikacji][SampleApp].

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-asm/example2design.png "Przychodząca sieć obwodowa z grupy NSG"
[2]: ./media/virtual-networks-dmz-nsg-fw-asm/dstnaticon.png "Ikona translatora adresów Sieciowych docelowego"
[3]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallrule.png "Reguły zapory"
[4]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallruleactivate.png "Aktywacja reguły zapory"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
[Example1]: ./virtual-networks-dmz-nsg-asm.md
