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
ms.openlocfilehash: 31d945f64cccd0c811d4dc45163583224102fb8a
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2019
ms.locfileid: "55965243"
---
# <a name="example-2--build-a-dmz-to-protect-applications-with-a-firewall-and-nsgs"></a>Przykład 2 — Tworzenie sieci obwodowej w ochronie aplikacji z zaporą i grupy NSG
[Wróć do strony zabezpieczeń granic najlepsze praktyki][HOME]

W tym przykładzie utworzy sieć obwodową z zaporą, windows czterech serwerów i grup zabezpieczeń sieci. On również przeprowadzi każdego odpowiednie polecenia, aby zapewnić lepiej zrozumieć każdego kroku. Jest również sekcji scenariusza ruchu zapewnienie szczegółowe instrukcje, jak ruchu obejmującego warstw zabezpieczeń w sieci obwodowej. Na koniec w odwołaniach sekcja jest kompletny kod i instrukcje umożliwiające tworzenie tego środowiska do testowania i eksperymentowania z różnymi scenariuszami. 

![Przychodzący sieć obwodowa NVA i grupy NSG][1]

## <a name="environment-description"></a>Opis środowiska
W tym przykładzie ma subskrypcję, która zawiera następujące elementy:

* Dwie usługi w chmurze: "FrontEnd001" i "BackEnd001"
* Sieć wirtualna "CorpNetwork" z dwiema podsieciami: "Fronton" i "Wewnętrzna"
* Jednej grupy zabezpieczeń sieci, stosowany do obu podsieci
* Wirtualne urządzenie sieciowe, w tym przykładzie zapora Barracuda NextGen, połączone z podsiecią Frontend
* Windows Server, który reprezentuje serwer aplikacji sieci web ("IIS01")
* Serwery dwa okna, które reprezentują aplikacji z powrotem zakończenia serwerów ("AppVM01", "AppVM02")
* Serwer Windows, który reprezentuje serwer DNS ("DNS01")

> [!NOTE]
> Mimo że w tym przykładzie użyto Barracuda NextGen Firewall, w tym przykładzie można użyć wielu różne wirtualne urządzenia sieciowe.
> 
> 

W poniższej sekcji odwołania są skrypt programu PowerShell, który zostanie skompilowany większość środowiska opisanych powyżej. Tworzenie maszyn wirtualnych i sieci wirtualne, chociaż są wykonywane tylko przez przykładowy skrypt, nie opisano szczegółowo w tym dokumencie.

Aby skompilować środowiska:

1. Zapisz plik xml konfiguracji sieci znajdujących się w części odwołania (zaktualizowane przy użyciu nazwy, lokalizacji i adresów IP adresy, które mają odpowiadać danym scenariuszu)
2. Zaktualizuj zmienne użytkownika w skrypcie, aby dopasować środowiska, które skrypt ma być uruchamiana względem (subskrypcji, nazwy usług itp.)
3. Uruchom skrypt programu PowerShell

**Uwaga**: Region oznaczony w skrypcie programu PowerShell musi odpowiadać oznaczony w pliku xml konfiguracji sieci.

Poniższe kroki skryptu używanego po utworzeniu mogą zostać podjęte, gdy skrypt zostanie uruchomiony pomyślnie:

1. Konfigurowanie reguł zapory, znajdują się w sekcji poniżej: Reguły zapory.
2. Opcjonalnie w sekcji odwołań są dwa skrypty do konfiguracji serwera sieci web oraz serwer aplikacji z prostą aplikację sieci web umożliwia testowanie za pomocą tej konfiguracji sieci obwodowej.

W następnej sekcji objaśniono większość instrukcji skryptów względem sieciowych grup zabezpieczeń.

## <a name="network-security-groups-nsg"></a>Sieciowe grupy zabezpieczeń (NSG)
Na przykład grupy NSG jest utworzone i następnie ładowany za pomocą sześciu reguł. 

> [!TIP]
> Ogólnie rzecz biorąc należy najpierw utworzyć określone reguły "Zezwalaj", a następnie ostatniego bardziej ogólnymi zasadami "Deny". Określają priorytetem, których zasady są oceniane pierwszy. Nie dodatkowe reguły są oceniane, po znalezieniu ruchu do zastosowania do określonej reguły. Reguły sieciowej grupy zabezpieczeń można stosować w jednym kierunku ruchu przychodzącego lub wychodzącego (z punktu widzenia podsieci).
> 
> 

Deklaratywne są tworzone następujące reguły dla ruchu przychodzącego:

1. Ruch DNS wewnętrzny (port 53) jest dozwolone.
2. Ruch protokołu RDP (port 3389) z Internetu do dowolnej maszyny Wirtualnej jest dozwolony.
3. Jest dozwolony ruch HTTP (port 80) z Internetu do urządzenia WUS (zapora)
4. Cały ruch (wszystkich portów) z IIS01 do AppVM1 jest dozwolone.
5. Cały ruch (wszystkich portów) z Internetu do całej sieci wirtualnej (zarówno podsieci) zostanie odrzucone.
6. Cały ruch (wszystkich portów) z podsieci Frontend do podsieci zaplecza jest zabroniony.

Przy użyciu tych reguł powiązany z każdej podsieci, jeśli żądanie HTTP zostało ruch przychodzący z Internetu do serwera sieci web, obie reguły 3 (Zezwalaj na) i 5 (odmowa) będzie miało zastosowanie, ale ponieważ reguła 3 ma wyższy priorytet, tylko naliczona zostałaby i reguły 5 nie przybyły, aby wpływające na. Ten sposób żądania HTTP będzie mogła zapory. Jeśli ten sam ruch próbował uzyskać dostęp do serwera DNS01, reguła 5 (odmowa) może być pierwszy do zastosowania, a ruch będzie nie można przekazać do serwera. Reguła 6 (Odmów) blokuje podsieć frontonu w rozmowie z podsieci wewnętrznej bazy danych (z wyjątkiem dozwolonego ruchu w regułach 1 i 4), ochronę sieci wewnętrznej bazy danych, w przypadku, gdy osoba atakująca naruszeń aplikacji sieci web we frontonie, osoba atakująca może mają ograniczony dostęp do sieci wewnętrznej bazy danych "chroniony" (tylko do zasobów udostępnianych na serwerze AppVM01).

Brak domyślną regułę ruchu wychodzącego, która umożliwia ruchu wychodzącego z Internetem. W tym przykładzie firma Microsoft zezwala na ruch wychodzący i nie modyfikowanie żadnych reguł dla ruchu wychodzącego. Aby zablokować ruch sieciowy w obie kierunki, routingu zdefiniowanego użytkownika jest wymagana, jest to przedstawione w innym przykładzie, który znajduje się w [dokumentu granic zabezpieczeń głównych][HOME].

Reguły NSG opisanych powyżej są bardzo podobne do reguł NSG w [przykład 1 — Tworzenie prostej sieci obwodowej z grup NSG][Example1]. Przejrzyj opis sieciowej grupy zabezpieczeń, w tym dokumencie, aby wyświetlić szczegółowy widok każdej reguły sieciowej grupy zabezpieczeń i jego atrybuty.

## <a name="firewall-rules"></a>Reguły zapory
Klient zarządzania będzie muszą być zainstalowane na komputerze do zarządzania zaporą i tworzyć konfiguracje wymagane. Wyświetlone dostawcę dokumentacji zapory (lub innego urządzenia WUS) do zarządzania urządzeniem. Dalszej części tej sekcji opisano konfigurację zapory, za pomocą klienta zarządzania dostawcami, (tj. nie witryny Azure portal lub programu PowerShell).

Instrukcje dotyczące pobierania klienta i nawiązywania połączenia z Barracuda, używany w tym przykładzie można znaleźć tutaj: [Barracuda NG administratora](https://techlib.barracuda.com/NG61/NGAdmin)

Na zaporze reguły przekazywania będzie muszą zostać utworzone. Ponieważ w tym przykładzie tylko trasy ruchu internetowego przychodzącego do zapory, a następnie do serwera sieci web, przekazywanie tylko jedną regułę NAT jest wymagane. Na Barracuda NextGen Firewall, używany w tym przykładzie reguła będzie reguła NAT do docelowej ("czasu letniego NAT") można przekazywać ten ruch.

Aby utworzyć następującą regułę (lub Sprawdź istniejące reguły domyślne), uruchamianie na pulpicie nawigacyjnym administratora NG Barracuda klienta, przejdź na kartę Konfiguracja w konfiguracji operacyjnej sekcji kliknij pozycję zestaw reguł. Siatce o nazwie "Main reguły" pokaże istniejące reguły aktywne i nieaktywne w zaporze. W prawym górnym rogu tej siatce są małe, zielony "+" przycisk, kliknij tutaj, aby utworzyć nową regułę (Uwaga: Zapora może być "zablokowany" w przypadku zmian, jeśli zostanie wyświetlony przycisk oznaczona jako "Zablokuj" i nie można utworzyć lub edytować reguły, kliknij ten przycisk, aby "Odblokuj" zestaw reguł i Zezwól na edytowanie). Jeśli chcesz edytować istniejącą regułę, wybierz regułę, że prawym przyciskiem myszy i wybierz pozycję Edytuj regułę.

Utwórz nową regułę, a następnie podaj nazwę, na przykład "WebTraffic". 

Ikona reguły translatora adresów Sieciowych docelowego wygląda następująco: ![Ikona translatora adresów Sieciowych docelowego][2]

Reguła sam będzie wyglądać mniej więcej tak:

![Reguły zapory][3]

W tym miejscu żadnego ruchu przychodzącego adresu, który uderza w zaporze próba nawiązania połączenia HTTP (port 80 i 443 dla protokołu HTTPS) zostanie wysłane interfejsu "Lokalny adres IP komputera DHCP1" zapory i Przekierowanie do serwera sieci Web o adresie IP 10.0.1.5. Ponieważ ruch jest mieszczących się na porcie 80 i przesyłane na serwer sieci web na porcie 80 było wymagane nie zmiany portu. Jednak listy docelowej mogło być 10.0.1.5:8080 Jeśli Nasz serwer sieci Web posłuchaliśmy na porcie 8080, w związku z tym tłumaczenia przychodzący port 80 w zaporze na przychodzący port 8080 na serwerze sieci web.

Metody połączenia powinien również można oznaczony, docelowy reguły z Internetu, "SNAT dynamicznego" jest najbardziej odpowiednie. 

Mimo że tylko jedna reguła została utworzona ważne jest, że jego priorytet jest prawidłowo. Jeśli w siatce wszystkie reguły zapory tej nowej reguły znajduje się w dolnej części (poniżej reguła "BLOCKALL") nigdy nie będą pochodzić do gry. Upewnij się, że nowo utworzonej reguły dla ruchu w sieci web przekracza reguły BLOCKALL.

Gdy ta reguła jest tworzona, musi być wypchnięte do zapory i następnie aktywowany, jeśli nie zostanie to zrobione zmianie reguły nie zostały zastosowane. W następnej sekcji opisano sposób wypychania i aktywacji.

## <a name="rule-activation"></a>Reguła aktywacji
Za pomocą reguł zmodyfikować, aby dodać tę regułę zestaw reguł należy przekazać do zapory i aktywowane.

![Aktywacja reguły zapory][4]

W prawym górnym rogu klienta zarządzania są klastra przycisków. Kliknij przycisk "Wyślij zmiany", aby wysyłać modyfikacji reguły zapory, a następnie kliknij przycisk "Aktywuj".

Za pomocą aktywacji zestawu reguł zapory na przykład kompilacji środowiska zostało ukończone. Opcjonalnie można uruchamiać skrypty kompilacji post, w sekcji odwołań do dodania aplikacji do tego środowiska, aby przetestować poniższe scenariusze ruchu.

> [!IMPORTANT]
> Koniecznie należy pamiętać, że nie nastąpi trafienie serwer sieci web bezpośrednio. Gdy przeglądarka zgłasza strony HTTP z FrontEnd001.CloudApp.Net, punkt końcowy HTTP (port 80) przekazuje ruch do zapory nie na serwerze sieci web. Zapora następnie zgodnie z regułą utworzonego powyżej translatorami adresów sieciowych, które żądania do serwera sieci Web.
> 
> 

## <a name="traffic-scenarios"></a>Scenariusze ruchu
#### <a name="allowed-web-to-web-server-through-firewall"></a>(Dozwolone) Serwer sieci Web w sieci Web za pośrednictwem zapory
1. Stronę internetową użytkownika żądania HTTP z FrontEnd001.CloudApp.Net (usługa chmury połączonego z Internetem)
2. Chmury usługi przekazuje ruch przez otwarty punkt końcowy na porcie 80 do lokalnego interfejsu zapory 10.0.1.4:80
3. Podsieć frontonu rozpoczyna przetwarzanie reguł dla ruchu przychodzącego:
   1. 1 regułę sieciowej grupy zabezpieczeń (DNS, Domain Name System) nie zastosować, przenieść następną regułę
   2. 2 reguły sieciowej grupy zabezpieczeń (RDP) nie są spełnione, przenieść następną regułę
   3. Zastosuj 3 reguły sieciowej grupy zabezpieczeń (Internet do zapory), ruch jest przetwarzanie reguł dozwolonych, Zatrzymaj
4. Ruch osiąga wewnętrzny adres IP zapory (10.0.1.4)
5. Reguły przekazywania zapory, zobacz ten jest ruch na porcie 80, przekierowuje go do serwera sieci web IIS01
6. IIS01 nasłuchuje ruchu w sieci web, odbiera żądania i rozpoczyna przetwarzanie żądania
7. IIS01 programu SQL Server na AppVM01 monituje o podanie informacji
8. Nie reguł ruchu wychodzącego w podsieci Frontend jest dozwolony ruch
9. Podsieci wewnętrznej bazy danych rozpoczyna się przetwarzanie reguł dla ruchu przychodzącego:
   1. 1 regułę sieciowej grupy zabezpieczeń (DNS, Domain Name System) nie zastosować, przenieść następną regułę
   2. 2 reguły sieciowej grupy zabezpieczeń (RDP) nie są spełnione, przenieść następną regułę
   3. 3 reguły sieciowej grupy zabezpieczeń (Internet do zapory) nie zastosować, przenieść następną regułę
   4. 4 reguły sieciowej grupy zabezpieczeń (IIS01 do AppVM01) są spełnione, ruch jest dozwolony, Zatrzymaj przetwarzanie reguł
10. AppVM01 odbiera zapytanie SQL i odpowiada
11. Ponieważ brak reguł ruchu wychodzącego w podsieci zaplecza jest dozwolone odpowiedzi
12. Podsieć frontonu rozpoczyna przetwarzanie reguł dla ruchu przychodzącego:
    1. Istnieje nie reguły sieciowej grupy zabezpieczeń, która ma zastosowanie do ruchu przychodzącego ruchu sieciowego z podsieci zaplecza do podsieci frontonu, aby Brak sieciowej grupy zabezpieczeń reguły, zastosuj
    2. Reguła systemu domyślnej zezwalającej na ruch pomiędzy podsieciami umożliwia ten ruch, ruch jest dozwolony.
13. Na serwerze usług IIS odbiera odpowiedź SQL i kończy odpowiedź HTTP i wysyła do obiektu żądającego
14. Ponieważ jest to sesji translatora adresów Sieciowych z zaporą, miejsce docelowe odpowiedzi (wstępnie) jest zapory
15. Zapora odbiera odpowiedź z serwera sieci Web i przekazuje użytkownikowi Internet
16. Ponieważ istnieją nie reguł ruchu wychodzącego w podsieci frontonu odpowiedzi jest dozwolone, a użytkownik Internet otrzymuje żądanej strony sieci web.

#### <a name="allowed-rdp-to-backend"></a>(Dozwolone) Protokół RDP do wewnętrznej bazy danych
1. Administrator serwera w Internecie żądań sesji protokołu RDP, aby AppVM01 na BackEnd001.CloudApp.Net:xxxxx gdzie xxxxx jest liczbą losowo przypisany port dla protokołu RDP do AppVM01 (przypisanego portu można znaleźć w witrynie Azure Portal lub za pośrednictwem programu PowerShell)
2. Ponieważ zapory nasłuchuje tylko adres FrontEnd001.CloudApp.Net, nie jest zaangażowana za pomocą tego przepływu ruchu
3. Podsieci wewnętrznej bazy danych rozpoczyna się przetwarzanie reguł dla ruchu przychodzącego:
   1. 1 regułę sieciowej grupy zabezpieczeń (DNS, Domain Name System) nie zastosować, przenieść następną regułę
   2. Stosowanie 2 reguły sieciowej grupy zabezpieczeń (RDP), ruch jest przetwarzanie reguł dozwolonych, Zatrzymaj
4. Za pomocą reguł ruchu wychodzącego domyślne reguły i ruch powrotny jest dozwolone
5. Sesję RDP jest włączona.
6. AppVM01 monituje o podanie hasła nazwy użytkownika

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Dozwolone) Wyszukiwanie DNS serwera sieci Web na serwerze DNS
1. Sieci Web Server, IIS01, potrzeb w www.data.gov strumieniowych źródeł danych, ale musi rozpoznać adresu.
2. W konfiguracji sieci VNet list DNS01 (10.0.2.4 w podsieci wewnętrznej bazy danych) jako podstawowy serwer DNS, IIS01 wysyła żądania DNS DNS01
3. Nie reguł ruchu wychodzącego w podsieci Frontend jest dozwolony ruch
4. Podsieci wewnętrznej bazy danych rozpoczyna się przetwarzanie reguł dla ruchu przychodzącego:
   1. Zastosuj 1 regułę sieciowej grupy zabezpieczeń (DNS, Domain Name System), ruch jest przetwarzanie reguł dozwolonych, Zatrzymaj
5. Serwer DNS odbiera żądanie
6. Serwer DNS nie ma adresu pamięci podręcznej i prosi główny serwer DNS w Internecie
7. Nie reguł ruchu wychodzącego w podsieci zaplecza ruch jest dozwolony.
8. Odpowiada internetowym serwerze DNS, ponieważ ta sesja została zainicjowana wewnętrznie, odpowiedź jest dozwolone.
9. Serwer DNS będzie buforować odpowiedź i odpowiada na żądanie początkowy powrót do IIS01
10. Nie reguł ruchu wychodzącego w podsieci zaplecza ruch jest dozwolony.
11. Podsieć frontonu rozpoczyna przetwarzanie reguł dla ruchu przychodzącego:
    1. Istnieje nie reguły sieciowej grupy zabezpieczeń, która ma zastosowanie do ruchu przychodzącego ruchu sieciowego z podsieci zaplecza do podsieci frontonu, aby Brak sieciowej grupy zabezpieczeń reguły, zastosuj
    2. Reguła systemowa domyślnej zezwalającej na ruch pomiędzy podsieciami będzie zezwalającej na ten ruch, więc ruch jest dozwolony
12. IIS01 odbiera odpowiedź od DNS01

#### <a name="allowed-web-server-access-file-on-appvm01"></a>(Dozwolone) Plik dostępu do serwera sieci Web na AppVM01
1. IIS01 poprosi o podanie pliku na AppVM01
2. Nie reguł ruchu wychodzącego w podsieci Frontend jest dozwolony ruch
3. Podsieci wewnętrznej bazy danych rozpoczyna się przetwarzanie reguł dla ruchu przychodzącego:
   1. 1 regułę sieciowej grupy zabezpieczeń (DNS, Domain Name System) nie zastosować, przenieść następną regułę
   2. 2 reguły sieciowej grupy zabezpieczeń (RDP) nie są spełnione, przenieść następną regułę
   3. 3 reguły sieciowej grupy zabezpieczeń (Internet do zapory) nie zastosować, przenieść następną regułę
   4. 4 reguły sieciowej grupy zabezpieczeń (IIS01 do AppVM01) są spełnione, ruch jest dozwolony, Zatrzymaj przetwarzanie reguł
4. AppVM01 odbiera żądanie i odpowiada za pomocą pliku (przy założeniu, że dostęp jest autoryzowany)
5. Ponieważ brak reguł ruchu wychodzącego w podsieci zaplecza jest dozwolone odpowiedzi
6. Podsieć frontonu rozpoczyna przetwarzanie reguł dla ruchu przychodzącego:
   1. Istnieje nie reguły sieciowej grupy zabezpieczeń, która ma zastosowanie do ruchu przychodzącego ruchu sieciowego z podsieci zaplecza do podsieci frontonu, aby Brak sieciowej grupy zabezpieczeń reguły, zastosuj
   2. Reguła systemu domyślnej zezwalającej na ruch pomiędzy podsieciami umożliwia ten ruch, ruch jest dozwolony.
7. Serwer IIS odbiera pliku

#### <a name="denied-web-direct-to-web-server"></a>(Odrzucony) Bezpośrednio do serwera sieci Web w sieci Web
Ponieważ serwer sieci Web, IIS01 i zapory znajdują się w tej samej usługi w chmurze współużytkują one ten sam publiczny IP adres. Ten sposób cały ruch HTTP będzie nastąpi przekierowanie do zapory. Gdy żądanie będzie obsłużone pomyślnie, nie można przejść bezpośrednio do serwera sieci Web, jego zakończonych powodzeniem, zgodnie z założeniami przez zaporę najpierw. Zobacz pierwszego scenariusza, w tej sekcji dla ruchu przychodzącego.

#### <a name="denied-web-to-backend-server"></a>(Odrzucony) Do serwera wewnętrznej bazy danych w sieci Web
1. Internet użytkownik próbuje uzyskać dostęp pliku na AppVM01 za pośrednictwem usługi BackEnd001.CloudApp.Net
2. Punkty końcowe nie są otwarte dla udziału plików, to nie przejdzie z usługą w chmurze, a nie dociera do serwera
3. W przypadku punktów końcowych, które były otwarte niektóre przyczyny, ten ruch mogłyby spowodować zablokowanie reguły sieciowej grupy zabezpieczeń 5 (Internetu do sieci wirtualnej)

#### <a name="denied-web-dns-lookup-on-dns-server"></a>(Odrzucony) Wyszukiwanie DNS w sieci Web na serwerze DNS
1. Internet użytkownik podejmuje próbę wyszukiwania wewnętrznego rekord DNS na DNS01 za pośrednictwem usługi BackEnd001.CloudApp.Net
2. Punkty końcowe nie są otwarte w systemie DNS, to nie przejdzie z usługą w chmurze, a nie dociera do serwera
3. W przypadku punktów końcowych, które były otwarte niektóre przyczyny, ten ruch mogłyby spowodować zablokowanie reguły sieciowej grupy zabezpieczeń 5 (Internet z siecią wirtualną) (Uwaga: nie ma zastosowania tej reguły 1 domen (DNS) z dwóch przyczyn, najpierw adres źródłowy jest internet, ta zasada ma zastosowanie tylko do lokalnej sieci wirtualnej jako źródło również jest regułę zezwalania więc go może nigdy nie zezwalają na ruch)

#### <a name="denied-web-to-sql-access-through-firewall"></a>(Odrzucony) Dostęp do programu SQL, za pośrednictwem zapory w sieci Web
1. Internet użytkownik żąda danych serwera SQL z FrontEnd001.CloudApp.Net (usługa chmury połączonego z Internetem)
2. Punkty końcowe nie są otwarte dla programu SQL, to nie przejdzie z usługą w chmurze i w takich sytuacjach przydałaby dotrzeć do zapory
3. Jeśli punkty końcowe były otwarte niektóre przyczyny, z podsiecią Frontend rozpoczyna przetwarzanie reguł dla ruchu przychodzącego:
   1. 1 regułę sieciowej grupy zabezpieczeń (DNS, Domain Name System) nie zastosować, przenieść następną regułę
   2. 2 reguły sieciowej grupy zabezpieczeń (RDP) nie są spełnione, przenieść następną regułę
   3. Stosowanie 2 reguły sieciowej grupy zabezpieczeń (Internet do zapory), ruch jest przetwarzanie reguł dozwolonych, Zatrzymaj
4. Ruch osiąga wewnętrzny adres IP zapory (10.0.1.4)
5. Zapory SQL nie ma żadnych reguł przekazywania i odrzuca ruch

## <a name="conclusion"></a>Podsumowanie
Jest to stosunkowo proste sposób ochrony aplikacji z zaporą i izolowanie podsieci wewnętrznej od ruchu przychodzącego.

Więcej przykładów i przegląd granice zabezpieczeń sieci można znaleźć [tutaj][HOME].

## <a name="references"></a>Dokumentacja
### <a name="main-script-and-network-config"></a>Skrypt głównego i konfiguracji sieci
Pełny skrypt należy zapisać w pliku skryptu programu PowerShell. W pliku o nazwie "NetworkConf2.xml", można zapisać konfiguracji sieci.
Zmodyfikuj zmienne zdefiniowane przez użytkownika, zgodnie z potrzebami. Uruchom skrypt, a następnie postępuj zgodnie z powyższych instrukcji konfiguracji reguły zapory.

#### <a name="full-script"></a>Pełny skrypt
Ten skrypt będzie na podstawie zmiennych zdefiniowanych przez użytkownika:

1. Łączenie się z subskrypcją platformy Azure
2. Tworzenie nowego konta magazynu
3. Utwórz nową sieć wirtualną z dwoma podsieciami, zgodnie z definicją w pliku konfiguracji sieci
4. Tworzenie maszyn wirtualnych z serwera dla systemu windows 4
5. Konfigurowanie sieciowej grupy zabezpieczeń w tym:
   * Tworzenie sieciowej grupy zabezpieczeń
   * Zapełnianie reguły
   * Powiązanie odpowiednie podsieci sieciową grupę zabezpieczeń

Ten skrypt programu PowerShell można uruchamiać lokalnie na połączone z Internetem, komputera lub serwera.

> [!IMPORTANT]
> Po uruchomieniu tego skryptu można ostrzeżenia lub inne komunikaty informacyjne, które punktów obecności w programie PowerShell. Tylko komunikaty o błędach w kolorze czerwonym są zaniepokoić.
> 
> 

```powershell
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
    # No User Defined Variables or   #
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
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation variable is correct and the network config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}

    If ($FatalError) {
        Write-Host "A fatal error has occurred, please see the above messages for more information." -ForegroundColor Red
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
```

#### <a name="network-config-file"></a>Plik konfiguracji sieci
Zapisz ten plik xml z lokalizacją zaktualizowane i dodać link do tego pliku do zmiennej $NetworkConfigFile w skrypcie powyżej.

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
Jeśli chcesz zainstalować przykładową aplikację w tym i inne przykłady strefy DMZ, jedno zostało podane z łącza: [Przykładowy skrypt aplikacji][SampleApp]

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-asm/example2design.png "Przychodząca sieć obwodowa z grupy NSG"
[2]: ./media/virtual-networks-dmz-nsg-fw-asm/dstnaticon.png "Ikona translatora adresów Sieciowych docelowego"
[3]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallrule.png "Reguły zapory"
[4]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallruleactivate.png "Aktywacja reguły zapory"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
[Example1]: ./virtual-networks-dmz-nsg-asm.md
