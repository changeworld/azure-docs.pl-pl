---
title: Alerty zabezpieczeń według typu w usłudze Azure Security Center | Microsoft Docs
description: W tym artykule omówiono różne rodzaje alertów zabezpieczeń dostępnych w usłudze Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: b3e7b4bc-5ee0-4280-ad78-f49998675af1
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2018
ms.author: v-mohabe
ms.openlocfilehash: 4592caacf7f73e4bce9f974fb3bb2ab3ed1a89ff
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65968365"
---
# <a name="understanding-security-alerts-in-azure-security-center"></a>Informacje o alertach zabezpieczeń w usłudze Azure Security Center
Ten artykuł ułatwia zapoznanie się z różnymi typami alertów zabezpieczeń i powiązanych szczegółowych informacji dostępnych w usłudze Azure Security Center. Więcej informacji na temat zarządzania alertami i zdarzeniami znajduje się w artykule [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md).

Aby skonfigurować wykrywanie zaawansowane, przeprowadź uaktualnienie usługi Azure Security Center do wersji Standard. Dostępna jest bezpłatna wersja próbna. W celu uaktualnienia wybierz pozycję **Warstwa cenowa** w obszarze [Zasady zabezpieczeń](tutorial-security-policy.md). Aby dowiedzieć się więcej, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="what-type-of-alerts-are-available"></a>Jakie typy alertów są dostępne?
Usługa Azure Security Center używa różnych [funkcji wykrywania](security-center-detection-capabilities.md), aby ostrzec klientów przed potencjalnymi atakami wymierzonymi w ich środowiska. Te alerty zawierają cenne informacje dotyczące przyczyny ich wyzwolenia, zasobów, których dotyczy atak, i źródła ataku. Informacje zawarte w alercie różnią się w zależności od typu analizy użytej do wykrycia zagrożenia. Zdarzenia mogą również zawierać dodatkowe informacje kontekstowe przydatne podczas badania zagrożenia.  Ten artykuł zawiera informacje na temat następujących typów alertów:

* Analiza zachowania maszyny wirtualnej (VMBA)
* Analiza sieci
* Analiza bazy danych SQL Database i magazynu SQL Data Warehouse
* Informacje kontekstowe

## <a name="virtual-machine-behavioral-analysis"></a>Analiza zachowania maszyny wirtualnej
Usługa Azure Security Center może użyć analizy behawioralnej w celu identyfikacji zasobów, których bezpieczeństwo zostało naruszone, na podstawie analizy dzienników zdarzeń maszyny wirtualnej, na przykład zdarzeń tworzenia procesów i zdarzeń logowania. Ponadto istnieje korelacja z innymi sygnałami, które wykrywają dowody potwierdzające istnienie szeroko zakrojonej kampanii ataku.

> [!NOTE]
> Aby uzyskać więcej informacji na temat sposobu działania funkcji wykrywania usługi Security Center, zobacz [Funkcje wykrywania usługi Azure Security Center](security-center-detection-capabilities.md).


### <a name="event-analysis"></a>Analiza zdarzeń
Usługa Security Center używa zaawansowanej analizy w celu identyfikacji zasobów, których bezpieczeństwo zostało naruszone, na podstawie analizy dzienników zdarzeń maszyny wirtualnej, na przykład zdarzeń tworzenia procesów i zdarzeń logowania. Ponadto istnieje korelacja z innymi sygnałami, które wykrywają dowody potwierdzające istnienie szeroko zakrojonej kampanii ataku.

* **Wykryto wykonanie podejrzanego procesu**: Osoby atakujące często próbują wykonać złośliwy kod bez wykrycia, maskując procesy jako niegroźne. Te alerty wskazują, że wykonanie procesu jest zgodne z jednym z następujących wzorców:
    * Wykonany proces jest znany z użycia do złośliwych celów. Gdy poszczególne polecenia mogą wydawać się niegroźne, ten alert jest oceniany na podstawie na agregacji tych poleceń.
    * Proces został wykonany z nietypowej lokalizacji.
    * Proces został wykonany z lokalizacji powiązanej z podejrzanymi plikami.
    * Proces został wykonany z podejrzanej ścieżki.
    * Proces został wykonany w nietypowym kontekście.
    * Proces został wykonany z poziomu nietypowego konta.
    * Wykonany proces miał podejrzane rozszerzenie.
    * Wykonany proces miał podejrzane podwójne rozszerzenie.
    * Wykonany proces miał w nazwie pliku podejrzany znak typu „od prawej do lewej”.
    * Wykonany proces, którego nazwa jest podobne do ale o innej z często uruchamianego procesu
    * Wykonany proces miał nazwę odpowiadającą znanemu narzędziu używanemu przez osoby atakujące.
    * Wykonany proces miał losową nazwę.
    * Wykonany proces miał podejrzane rozszerzenie.
    * Wykonano ukryty plik.
    * Proces został wykonany jako element podrzędny innego niepowiązanego procesu.
    * Nietypowy proces został utworzony przez proces systemowy.
    * Nietypowy proces został uruchomiony przez usługę Windows Update.
    * Proces został wykonany przy użyciu nietypowego wiersza polecenia. Ta sytuacja jest powiązana z przejmowaniem autentycznych procesów w celu wykonania złośliwej zawartości.
    * Podjęto próbę uruchomienia wszystkich plików wykonywalnych (*.exe) w katalogu z poziomu wiersza polecenia.
    * Proces został wykonany przy użyciu narzędzia PsExec, które może służyć do zdalnego uruchamiania procesów.
    * Nadrzędny plik wykonywalny Apache Tomcat® (Tomcat#.exe) został użyty do uruchomienia podejrzanych procesów podrzędnych, które mogą hostować lub uruchamiać złośliwe polecenia.
    * Usługa systemu Microsoft Windows „Asystent zgodności programów” (pcalua.exe) została użyta do uruchomienia kodu wykonywalnego, który może być złośliwy.
    * Wykryto nagłe zakończenie podejrzanego procesu.
    * Proces systemowy SVCHOST został wykonany w nietypowym kontekście.
    * Proces systemowy SVCHOST został wykonany w rzadkiej grupie usług.
    * Wykonano podejrzany wiersz polecenia.
    * Skrypt programu PowerShell ma cechy wspólne ze znanymi podejrzanymi skryptami.
    * Wykonano znane złośliwe polecenie cmdlet z kolekcji PowerSploit programu PowerShell.
    * Wbudowany użytkownik języka SQL wykonał proces, który normalnie nie byłby uruchamiany.
    * Wykryto plik wykonywalny z kodowaniem base-64, co może oznaczać, że osoba atakująca próbowała uniknąć wykrycia, tworząc plik wykonywalny na bieżąco, za pośrednictwem sekwencji poleceń.

* **Podejrzane działanie zasobu protokołu RDP**: Osoby atakujące często wybierają otwarte porty zarządzania takie jak protokół RDP ataków siłowych. Te alerty wskazują wystąpienie podejrzanego działania logowania do Pulpitu zdalnego, co oznacza, że:
    * Podjęto próby logowania do Pulpitu zdalnego.
    * Podjęto próby logowania do Pulpitu zdalnego przy użyciu nieprawidłowych kont.
    * Podjęto próby logowania do pulpitu zdalnego, z których część udało się pomyślnie zalogować się do maszyny.
* **Podejrzane działanie zasobu protokołu SSH**: Osoby atakujące często wybierają otwarte porty zarządzania takie jak protokół SSH za pomocą ataków siłowych. Te alerty wskazują wystąpienie podejrzanego działania logowania do protokołu SSH, co oznacza, że:
    * Podjęto nieudane próby logowania do protokołu SSH.
    * Podjęto próby logowania do protokołu SSH, a niektóre z nich zakończyły się pomyślnie.
* **Podejrzana wartość rejestru WindowPosition**: Ten alert wskazuje, że podjęto próbę zmiany konfiguracji rejestru WindowPosition, co może świadczyć o ukrywaniu okien aplikacji w niewidocznych sekcjach pulpitu.
* **Potencjalna próba obejścia funkcji AppLocker**: Funkcja AppLocker może służyć do ograniczania procesów, które można uruchomić na Windows, co zmniejsza stopnień narażenia złośliwego oprogramowania. Ten alert wskazuje na możliwą próbę ominięcia ograniczeń funkcji AppLocker przez użycie zaufanych plików wykonywalnych (dozwolonych przez zasady funkcji AppLocker) do wykonania niezaufanego kodu.
* **Podejrzane komunikacja z nazwanym potokiem**: Ten alert wskazuje, że dane zostały zapisane w lokalnym nazwanym potoku z poziomu polecenia konsoli Windows. Nazwane potoki są używane przez osoby atakujące do przydzielania zadań do złośliwego elementu i komunikowania się z nim.
* **Dekodowanie pliku wykonywalnego przy użyciu wbudowanego narzędzia certutil.exe**: Ten alert wskazuje, że wbudowane narzędzie administratora, certutil.exe, zostało użyte do dekodowania pliku wykonywalnego. Osoby atakujące nadużywają funkcjonalności wiarygodnych narzędzi administratora w celu wykonania złośliwych akcji. Mogą na przykład użyć narzędzia, takiego jak certutil.exe, do dekodowania złośliwego pliku wykonywalnego, który następnie zostanie wykonany.
* **Dziennik zdarzeń został wyczyszczony**: Ten alert wskazuje podejrzane dziennika zdarzeń operacji, która jest często używana przez osoby atakujące w celu ukrycia śladów ich działania.
* **Wyłączanie i usuwanie usług IIS pliki dziennika**: Ten alert wskazuje, że plik dziennika usług IIS zostały wyłączone i/lub usunięte, która jest często używana przez osoby atakujące w celu ukrycia śladów ich działania.
* **Podejrzane usunięcie plików**: Ten alert wskazuje na podejrzane usunięcie plików, które mogą być używane przez osobę atakującą w celu usunięcia dowodu istnienia złośliwych plików binarnych.
* **Usunięto wszystkie kopie w tle pliku**: Ten alert wskazuje, że kopie w tle zostały usunięte.
* **Podejrzane polecenia czyszczenia pliku**: Ten alert wskazuje na kombinacji poleceń systeminfo do wykonania działania samodzielnego czyszczenia po wystąpieniu naruszenia.  Plik *systeminfo.exe* to autentyczne narzędzie systemu Windows, ale jego uruchomienie dwa razy z rzędu, a następnie użycie polecenia Usuń w opisany tutaj sposób jest rzadkie.
* **Podejrzane utworzenie konta**: Ten alert wskazuje, że zostało utworzone konto bardzo podobne do istniejącego wbudowanego konta z uprawnieniami administracyjnymi. Ta technika może zostać użyta przez osoby atakujące do utworzenia nieautoryzowanego konta bez wykrycia.
* **Działanie kopiowania w tle woluminu podejrzane**: Ten alert wskazuje na działanie usunięcia kopii w tle dla danego zasobu. Kopiowanie woluminów w tle (VSC, Volume Shadow Copy) to ważny artefakt, który przechowuje migawki danych. To działanie jest skojarzony z wykorzystaniem oprogramowania wymuszającego Okup, ale może być również autentyczne.
* **Metoda trwałości rejestru Windows**: Ten alert wskazuje na próbę utrzymania pliku wykonywalnego w rejestrze systemu Windows. Złośliwe oprogramowanie często używa takiej techniki w celu przetrwania rozruchu.
* **Podejrzana Nowa reguła zapory**: Ten alert wskazuje na to, że dodano nową regułę zapory za pośrednictwem *netsh.exe* zezwalająca na ruch z pliku wykonywalnego w podejrzanej lokalizacji.
* **Podejrzane wykonania XCOPY**: Ten alert wskazuje serię wykonań XCOPY, które może oznaczać, że jedna z maszyn zostało naruszone i użyto do propagowania złośliwego oprogramowania.
* **Pomijanie prawnych wyświetlanych dla użytkowników podczas logowania**: Ten alert oznacza zmianę klucza rejestru, który kontroluje, czy informacje prawne jest wyświetlana dla użytkowników podczas logowania. Jest to typowe działanie podejmowane przez osoby atakujące po naruszeniu bezpieczeństwa hosta.
* **Wykryto nietypową kombinację wielkich i małych liter w wierszu polecenia**: Ten alert wskazuje na użycie kombinacji wielkich i małych liter w wierszu polecenia, jest to technika używana przez osoby atakujące, aby ukryć zasad komputera uwzględniających wielkość liter lub bazujących na skrótach.
* **Wiersz polecenia zaciemnionego**: Ten alert wskazuje, czy podejrzane wskaźniki zasłanianie zostały wykryte w wierszu polecenia.
* **Wiele zapytań do kont domeny**: Osoby atakujące często zapytania konta domeny usługi AD podczas rozpoznawania użytkowników, kont administratora domeny, kontrolerów domeny i relacje zaufania między domenami. Ten alert wskazuje na to, że w krótkim czasie wykonano nietypową liczbę zapytań o różne konta domeny.
* **Możliwe działanie lokalne**: Ten alert wskazuje, że zostały wykonane kombinacji poleceń systeminfo skojarzonych z działaniem.  Plik *systeminfo.exe* to oryginalne narzędzie systemu Windows, ale wykonanie go dwa razy z rzędu jest rzadkie.
* **Możliwe wykonanie pliku wykonywalnego keygen**: Ten alert wskazuje, proces, w której nazwa to wskazuje na narzędzie keygen została wykonana. Takie narzędzia są zwykle używane w celu pokonania mechanizmów licencjonowania oprogramowania, ale są one często pobierane w pakiecie z innym złośliwym oprogramowaniem.
* **Podejrzane wykonanie za pośrednictwem rundll32.exe**: Ten alert wskazuje, że ten rundll32.exe został użyty do wykonania procesu o nietypowej nazwie, spójne ze schematem nazewnictwa procesów używana przez osoby atakujące do zainstalowania elementu pierwszego etapu na hoście ze złamanymi zabezpieczeniami.
* **Podejrzana kombinacja hosta HTA i programu PowerShell**: Ten alert wskazuje, że Microsoft HTML Application Host (HTA) uruchamia polecenia programu PowerShell. Jest to technika używana przez osoby atakujące do uruchamiania złośliwych skryptów programu PowerShell.
* **Zmień klucz rejestru, który może być nadużywany w celu obejścia kontroli konta użytkownika**: Ten alert wskazuje, że klucz rejestru, który może być nadużywany w celu obejścia kontroli konta użytkownika (Kontrola konta użytkownika) została zmieniona, która jest często używana przez osoby atakujące można przenieść z nieuprzywilejowanym (użytkownik standardowy) do dostępu uprzywilejowanego (na przykład administrator) naruszonego hosta.
* **Użycie podejrzanej nazwy domeny w wierszu polecenia**: Ten alert wskazuje na to, że użyto podejrzanej nazwy domeny, co może być dowodem osoba atakująca hostuje złośliwe narzędzia i jako punktów końcowych dla poleceń i kontroli danych oraz ich eksfiltracji.
* **Konto zostało utworzone na wielu hostach w okresie 24-godzinnym**: Ten alert wskazuje, że podjęto do utworzenia tego samego konta użytkownika na wielu hostach, które może być dowodem osoba atakująca przenosi się w bok w sieci po co najmniej jednej jednostki sieci zostały złamane.
* **Podejrzane użycie list CACLS w celu obniżenia stanu zabezpieczeń systemu**: Ten alert wskazuje, że zmiana listy kontroli dostępu (CACLS) został zmieniony. Ta technika jest często używana przez osoby atakujące w celu nadania praw pełnego dostępu systemowym plikom binarnym, takim jak ftp.exe, net.exe, wscript.exe itd.
* **Podejrzane parametry ataku bilet uwierzytelniania Golden Ticket protokołu Kerberos**: Ten alert wskazuje, że zostały wykonane zgodnie z atak Golden Ticket protokołu Kerberos parametry wiersza polecenia. Naruszony klucz krbtgt może zostać użyty przez osobę atakującą do podszycia się pod dowolnego użytkownika.
* **Włączenie klucza rejestru WDigest UseLogonCredential**: Ten alert wskazuje, że klucz rejestru został zmieniony logowania w poświadczeniach, które mają być przechowywane w postaci zwykłego tekstu w pamięci LSA, które następnie mogą być zbierane z pamięci.
* **Potencjalnie podejrzane użycie narzędzia Telegram**: Ten alert wskazuje na zainstalowanie Telegram, bezpłatnej oparte na chmurze błyskawiczne usługą obsługi wiadomości używana przez osoby atakujące do przesyłania złośliwych plików binarnych do dowolnego komputera, telefonu lub tabletu.
* **Tworzenie nowego punktu ASEP**: Ten alert wskazuje na utworzenie nowych punktów ASEP (Auto Start Extensibility Point), co powoduje, że nazwy procesu zidentyfikowanej w wierszu polecenia, który ma zostać automatycznie uruchomiony i może służyć osobie atakującej do osiągnięcia trwałości.
* **Podejrzane Set-ExecutionPolicy i usługi WinRM zmiany**: Ten alert wskazuje na zmiany konfiguracji, które są skojarzone z użyciem złośliwej powłoki internetowej chinachopper.
* **Wyłączenie usług krytycznych**: Ten alert wskazuje, że polecenie "net.exe stop" zostało użyte do zatrzymania usług krytycznych, takich jak SharedAccess lub Centrum zabezpieczeń Windows.
* **Podejrzane użycie przełącznika FTP -s**: Ten alert wskazuje na użycie FTP "-s" przełącznika, który może służyć przez złośliwe oprogramowanie do nawiązywania połączeń ze zdalnym serwerem FTP i pobierania dodatkowych złośliwych plików binarnych.
* **Podejrzane wykonanie polecenia VBScript.Encode**: Ten alert wskazuje na *VBScript.Encode* polecenie zostało wykonane, które koduje skrypty do tekstu nie można go odczytać, co utrudnia użytkownikom badanie kodu.
* **Alokacja obiektu VBScript HTTP**: Ten alert wskazuje na utworzenie pliku VBScript przy użyciu wiersza polecenia; który może służyć do pobierania złośliwych plików.
* **Atak typu klawisze trwałe**: Ten alert wskazuje, że osoba atakująca może subverting binarne ułatwień dostępu (na przykład klawisze, klawiatura ekranowa, narrator) w celu zapewnienia dostępu Tylne wejście do systemu.
* **Wskaźniki oprogramowania wymuszającego okup Petya**: Ten alert wskazuje, że zaobserwowano techniki skojarzone z oprogramowaniem wymuszającym okup Petya.
* **Próba wyłączenia interfejsu AMSI**: Ten alert wskazuje na próbę wyłączenia interfejsu skanowania ochrony przed złośliwym kodem (AMSI), co spowodowałoby wyłączenie wykrywania ochrony przed złośliwym oprogramowaniem.
* **Wskaźniki oprogramowania wymuszającego Okup**: Ten alert wskazuje na podejrzane działania tradycyjnie kojarzone z oprogramowaniem wymuszającym okup ekran blokady i szyfrowania.
* **Plik wyjściowy zbierania śladów podejrzane**: Ten alert wskazuje, że zebrano ślady i dane wyjściowe do pliku nietypowego typu śledzenia (na przykład aktywności sieciowej).
* **Oprogramowanie wysokiego ryzyka**: Ten alert oznacza korzystanie z oprogramowania, które zostało skojarzone z instalacją złośliwego oprogramowania. Osoby atakujące często umieszczają złośliwe oprogramowanie w pakiecie z niegroźnymi narzędziami, takimi jak pokazane w tym alercie, a następnie dyskretnie instalują złośliwe oprogramowanie w tle.
* **Tworzenie podejrzanego pliku**: Ten alert wskazuje na utworzenie lub wykonanie procesu używanego przez osoby atakujące do pobierania dodatkowego złośliwego oprogramowania naruszonego hosta po otwarciu załącznika w dokumencie przeznaczonym do wyłudzania.
* **Podejrzane poświadczenia w wierszu polecenia**: Ten alert wskazuje podejrzane hasło używane do wykonywania pliku. Ta technika była używana przez osoby atakujące do wykonywania złośliwego oprogramowania Pirpi.
* **Możliwe wykonanie podrzucającego złośliwe**: Ten alert wskazuje nazwę pliku, który została użyta przez osoby atakujące do zainstalowania złośliwego oprogramowania.
* **Podejrzane wykonanie za pośrednictwem rundll32.exe**: Ten alert wskazuje na rundll32.exe jest używany do wykonywania notepad.exe lub reg.exe — jest to spójne z techniką iniekcji procesu stosowaną przez osoby atakujące.
* **Podejrzane argumenty wiersza polecenia**: Ten alert wskazuje podejrzane argumenty wiersza polecenia używane w połączeniu z odwrotną powłoką stosowaną przez grupę działań HYDROGEN.
* **Podejrzane poświadczenia dokumentu**: Ten alert wskazuje na podejrzany, wstępnie obliczony skrót hasła używany przez złośliwe oprogramowanie jest używany do wykonywania pliku.
* **Dynamiczna konstrukcja skryptu programu PS**: Ten alert wskazuje na skrypt programu PowerShell jest tworzony dynamicznie. Osoby atakujące używają tej techniki, aby stopniowo kompilować skrypt w celu omijania systemów IDS.
* **Wskaźniki Metasploit**: Ten alert wskazuje na działanie skojarzone z platformą Metasploit, która udostępnia szereg możliwości i narzędzi.
* **Podejrzane działanie na koncie**: Ten alert wskazuje na próbę połączenia z maszyną za pomocą konta, które zostało niedawno naruszone.
* **Tworzenie konta**: Ten alert wskazuje na utworzenie nowego konta na komputerze.


### <a name="crash-analysis"></a>Analiza awarii


Analiza awaryjnego zrzutu pamięci jest metodą używaną do wykrywania zaawansowanego złośliwego oprogramowania, które jest w stanie ominąć tradycyjne rozwiązania w zakresie zabezpieczeń. Różne rodzaje złośliwego oprogramowania próbują zmniejszyć prawdopodobieństwo wykrycia przez programy antywirusowe, rezygnując z zapisu na dysku lub szyfrując składniki oprogramowania zapisywane na dysku. Ta technika sprawia, że złośliwe oprogramowanie jest trudne do wykrycia przy użyciu tradycyjnych metod ochrony. Złośliwe oprogramowanie może jednak zostać wykryte za pomocą analizy pamięci, ponieważ aby mogło działać, musi zostawić ślady w pamięci maszyny.

Jeśli wystąpi awaria oprogramowania, zrzut awaryjny przechwytuje część pamięci w chwili pojawienia się awarii. Awaria może być spowodowana przez złośliwe oprogramowanie, powszechnie stosowane aplikacje lub problemy z systemem. Dzięki analizie pamięci w zrzucie awaryjnym usługa Security Center może wykryć metody, jakie zostały użyte w celu wykorzystania luk obecnych w oprogramowaniu, uzyskania dostępu do poufnych danych i dyskretnego pozostania na zainfekowanym komputerze. Jest to realizowane przy minimalnym wpływie na wydajność hostów, ponieważ analiza jest wykonywana przez zaplecze usługi Security Center.

* **Wykryto iniekcję kodu**: Iniekcja kodu to wstawianie modułów wykonywalnych do uruchomionych procesów lub wątków. Ta technika jest używany przez złośliwe oprogramowanie na dostęp do danych, ukrywanie i zapobiec jego usunięciu (np. trwałość). Ten alert wskazuje, że wprowadzony moduł znajduje się w zrzucie awaryjnym. Wiarygodni programiści czasami wykonują iniekcję kodu z niezłośliwych powodów, takich jak modyfikowanie lub rozbudowywanie istniejącej aplikacji albo składnika systemu operacyjnego. Aby ułatwić odróżnienie niezłośliwych modułów od złośliwych, usługa Security Center sprawdza, czy wprowadzony moduł odpowiada profilowi podejrzanego zachowania. Wynik tej kontroli jest widoczny w polu alertu „SIGNATURE” i obejmuje ważność alertu, opis alertu oraz czynności zaradcze alertu.
* **Podejrzany segment kodu**: Alert podejrzanego segmentu kodu wskazuje, że segment kodu został przydzielony przy użyciu niestandardowych metod, takich jak używane przez iniekcję odbijającą i zamianę pamięci procesu. Dodatkowe charakterystyki segmentu kodu są przetwarzane w celu zapewnienia kontekstu dotyczącego możliwości i zachowań zgłoszonego segmentu kodu.
* **Wykryto kod powłoki**: Kod powłoki to ładunek uruchamiany po wykorzystaniu przez złośliwe oprogramowanie luki w zabezpieczeniach oprogramowania. Ten alert oznacza, że analiza zrzutu awaryjnego wykryła zachowanie kodu wykonywalnego typowe dla złośliwych ładunków. Wprawdzie niezłośliwe oprogramowanie może zachowywać się podobnie, jednak nie jest to typowe w przypadku zwykłych metod tworzenia oprogramowania.
* **Wykryto przejęcie modułu**: System Windows używa bibliotek dołączanych dynamicznie (DLL, Dynamic Link Library), aby umożliwić oprogramowaniu korzystanie z typowych funkcji systemu Windows. Przejęcie biblioteki DLL ma miejsce, gdy złośliwe oprogramowanie zmienia kolejność ładowania bibliotek DLL, aby załadować złośliwy ładunek do pamięci, w której może zostać wykonany dowolny kod. Ten alert oznacza, że analiza zrzutu awaryjnego wykryła moduł o podobnej nazwie, który jest ładowany z dwóch różnych ścieżek. Jedna z nich odpowiada typowej lokalizacji plików binarnych systemu Windows. Wiarygodni programiści czasami zmieniają kolejność ładowania bibliotek DLL z niezłośliwych powodów, takich jak instrumentacja albo rozbudowa systemu operacyjnego Windows lub aplikacji. Aby ułatwić rozróżnienie złośliwych i potencjalnie niegroźnych zmian kolejności ładowania bibliotek DLL, usługa Security Center sprawdza, czy załadowany moduł jest zgodny z podejrzanym profilem.
* **Wykryto zamaskowany moduł Windows**: Złośliwe oprogramowanie może używać typowych nazw plików binarnych systemu Windows (na przykład SVCHOST. Z rozszerzeniem EXE) lub modułów (na przykład NTDLL. Biblioteka DLL) w celu zamaskowania charakteru złośliwego oprogramowania przed administratorami systemu. Ten alert oznacza, że plik zrzutu awaryjnego zawiera moduły używające nazw modułów systemu Windows, które nie spełniają innych kryteriów typowych dla tego rodzaju modułów. Analiza kopii dyskowej zamaskowanego modułu może dostarczyć dodatkowych informacji na temat wiarygodności lub złośliwości tego modułu.
* **Zmodyfikowany binarny plik systemowy**: Złośliwe oprogramowanie może modyfikować podstawowe pliki binarne systemu, aby potajemnie uzyskać dostęp do danych lub niezauważenie przetrwać w zaatakowanym systemie. Ten alert oznacza, że analiza zrzutu awaryjnego wykryła modyfikację podstawowych plików binarnych systemu operacyjnego Windows w pamięci lub na dysku. Wiarygodni programiści czasami modyfikują moduły systemu w pamięci z niezłośliwych powodów, na przykład w celu obejścia lub uzyskania zgodności aplikacji. Aby ułatwić rozróżnienie złośliwych i potencjalnie autentycznych modułów, usługa Security Center sprawdza, czy zmodyfikowany moduł jest zgodny z podejrzanym profilem.

## <a name="network-analysis"></a>Analiza sieci
Wykrywanie zagrożeń sieci za pomocą usługi Security Center polega na automatycznym zbieraniu informacji o zabezpieczeniach uzyskanych na podstawie ruchu protokołu IPFIX (Internet Protocol Flow Information Export) na platformie Azure. Analizuje ona te informacje, często zestawiając informacje z wielu źródeł, aby zidentyfikować zagrożenia.

* **Możliwe przychodzące próby ataków siłowych SQL wymusić prób**: Analiza ruchu w sieci wykryła podejrzaną przychodzącą komunikację SQL. To działanie jest zgodne z próbami ataków siłowych podejmowanych na serwerach SQL.
* **Podejrzane przychodzące działanie sieci protokołu RDP z wielu źródeł**: Analiza ruchu w sieci wykryła nietypową przychodzącą komunikację protokołu RDP (Remote Desktop) z wielu źródeł. W szczególności próbkowane dane sieci pokazują unikatowe adresy IP łączące się z maszyną, co jest uznawane za nietypowe w przypadku tego środowiska. To działanie może oznaczać próbę ataku siłowego w punkcie końcowym protokołu RDP z wielu hostów (Botnet).
* **Podejrzane przychodzące działanie sieci protokołu RDP**: Analiza ruchu w sieci wykryła nietypową przychodzącą komunikację protokołu RDP (Remote Desktop). W szczególności próbkowane dane sieci pokazują dużą liczbę przychodzących połączeń z maszyną, co jest uznawane za nietypowe w przypadku tego środowiska. To działanie może oznaczać próbę ataku siłowego w punkcie końcowym protokołu RDP.
* **Podejrzane wychodzące działanie sieci protokołu RDP do wielu miejsc docelowych**: Analiza ruchu w sieci wykryła nietypową wychodzącą komunikację protokołu RDP (Remote Desktop) do wielu miejsc docelowych. To działanie może wskazywać, że bezpieczeństwo maszyny zostało naruszone i obecnie jest używana do przeprowadzania ataków siłowych w zewnętrznych punktach końcowych protokołu RDP. Należy pamiętać, że ten typ działań może powodować oflagowanie adresu IP jako złośliwego przez jednostki zewnętrzne.
* **Podejrzane wychodzące działanie sieci protokołu RDP**: Analiza ruchu w sieci wykryła nietypową wychodzącą komunikację protokołu RDP (Remote Desktop). W szczególności próbkowane dane sieci pokazują dużą liczbę wychodzących połączeń z maszyny, co jest uznawane za nietypowe w przypadku tego środowiska. To działanie może wskazywać, że bezpieczeństwo maszyny zostało naruszone i obecnie jest używana do przeprowadzania ataków siłowych w zewnętrznych punktach końcowych protokołu RDP. Należy pamiętać, że ten typ działań może powodować oflagowanie adresu IP jako złośliwego przez jednostki zewnętrzne.
* **Podejrzane przychodzące działanie sieci protokołu SSH**: Analiza ruchu w sieci wykryła nietypową przychodzącą komunikację protokołu SSH. W szczególności próbkowane dane sieci pokazują dużą liczbę przychodzących połączeń z maszyną, co jest uznawane za nietypowe w przypadku tego środowiska. To działanie może oznaczać próbę ataku siłowego w punkcie końcowym protokołu SSH.
* **Podejrzane przychodzące działanie sieci protokołu SSH z wielu źródeł**: Analiza ruchu w sieci wykryła nietypową przychodzącą komunikację protokołu SSH. W szczególności próbkowane dane sieci pokazują unikatowe adresy IP łączące się z maszyną, co jest uznawane za nietypowe w przypadku tego środowiska. To działanie może oznaczać próbę ataku siłowego w punkcie końcowym protokołu SSH z wielu hostów (Botnet).
* **Podejrzane wychodzące działanie sieci protokołu SSH**: Analiza ruchu w sieci wykryła nietypową wychodzącą komunikację protokołu SSH. W szczególności próbkowane dane sieci pokazują dużą liczbę wychodzących połączeń z maszyny, co jest uznawane za nietypowe w przypadku tego środowiska. To działanie może wskazywać, że bezpieczeństwo maszyny zostało naruszone i obecnie jest używana do przeprowadzania ataków siłowych w zewnętrznych punktach końcowych protokołu SSH. Należy pamiętać, że ten typ działań może powodować oflagowanie adresu IP jako złośliwego przez jednostki zewnętrzne.
* **Podejrzane wychodzące działanie sieci protokołu SSH do wielu miejsc docelowych**: Analiza ruchu w sieci wykryła nietypową wychodzącą komunikację protokołu SSH do wielu miejsc docelowych. W szczególności próbkowane dane sieci pokazują maszynę łączącą się z unikatowymi adresami IP, co jest uznawane za nietypowe w przypadku tego środowiska. To działanie może wskazywać, że bezpieczeństwo maszyny zostało naruszone i obecnie jest używana do przeprowadzania ataków siłowych w zewnętrznych punktach końcowych protokołu SSH. Należy pamiętać, że ten typ działań może powodować oflagowanie adresu IP jako złośliwego przez jednostki zewnętrzne.
* **Komunikacja sieciowa ze złośliwą maszyną wykryto**: Analiza ruchu w sieci wskazuje, że komputer ma przekazane prawdopodobnie komunikowała się centrum poleceń i kontroli.
* **Możliwe naruszenie bezpieczeństwa maszyny wykryto**: Analiza ruchu w sieci wykryła działanie wychodzące, co może oznaczać, że działa ona jako część botnetu. Analiza opiera się na adresach IP używanych przez zasób razem z publicznymi rekordami systemu DNS.


## <a name="sql-database-and-sql-data-warehouse-analysis"></a>Analiza bazy danych SQL Database i magazynu SQL Data Warehouse

Analiza zasobów usługi Security Center koncentruje się na usługach PaaS, takich jak integracja z funkcją [wykrywania zagrożeń bazy danych Azure SQL Database](../sql-database/sql-database-threat-detection.md) i usługi Azure SQL Data Warehouse. Funkcja wykrywania zagrożeń SQL wykrywa nietypowe działania wskazujące potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub wykorzystania ich luk w zabezpieczeniach i wyzwala następujące alerty:

* **Podatność na iniekcję SQL**: Ten alert jest wyzwalany, jeśli aplikacja generuje błędnej instrukcji SQL w bazie danych. Może to wskazywać lukę w zabezpieczeniach umożliwiającą ataki przez iniekcję SQL. Istnieją dwie możliwe przyczyny generowania błędnej instrukcji:
    * Usterka w kodzie aplikacji powoduje utworzenie błędnej instrukcji SQL
    * Kod aplikacji lub procedury składowane nie oczyszczają danych wejściowych użytkownika podczas tworzenia błędnej instrukcji SQL, co można wykorzystać do iniekcji SQL
* **Potencjalna iniekcja SQL**: Ten alert jest wyzwalany w przypadku aktywnego wykorzystania przed luką w zabezpieczeniach aplikacji zidentyfikowanego na iniekcję SQL. Oznacza to, że osoba atakująca próbuje wstrzyknąć złośliwe instrukcje SQL, korzystając z podatnego kodu aplikacji lub procedur składowanych.
* **Dostęp z nietypowej lokalizacji**: Ten alert jest wyzwalany, gdy występuje w przypadku zmiany wzorca dostępu do programu SQL server, gdy ktoś zalogował się z serwerem SQL z nietypowej lokalizacji geograficznej. W niektórych przypadkach ten alert wykrywa prawidłowe działanie (nowa aplikacja lub konserwacja przeprowadzana przez deweloperów). W innych przypadkach ten alert wykrywa złośliwe działanie (były pracownik, zewnętrzna osoba atakująca).
* **Dostęp z centrum danych platformy Azure nietypowe**: Ten alert jest wyzwalany, gdy występuje w przypadku zmiany wzorca dostępu do programu SQL server, gdy ktoś zalogował się do programu SQL server w centrum danych platformy Azure, co zostało zaobserwowane na tym serwerze w ostatnim czasie. W niektórych przypadkach ten alert wykrywa prawidłowe działanie (nowa aplikacja na platformie Azure, usługa Power BI, edytor zapytań SQL platformy Azure). W innych przypadkach ten alert wykrywa złośliwe działanie z zasobu/usługi platformy Azure (były pracownik, zewnętrzna osoba atakująca).
* **Dostęp z nieznanego podmiotu zabezpieczeń**: Ten alert jest wyzwalany, gdy występuje w przypadku zmiany wzorca dostępu do programu SQL server, gdy ktoś zalogował się do programu SQL server za pomocą nietypowego podmiotu zabezpieczeń (użytkownika SQL). W niektórych przypadkach ten alert wykrywa prawidłowe działanie (nowa aplikacja, konserwacja przeprowadzana przez deweloperów). W innych przypadkach ten alert wykrywa złośliwe działanie (były pracownik, zewnętrzna osoba atakująca).
* **Dostęp z potencjalnie szkodliwej aplikacji**: Ten alert jest wyzwalany, gdy potencjalnie szkodliwa aplikacja jest używana do dostępu do bazy danych. W niektórych przypadkach ten alert wykrywa przeprowadzany test penetracji. W innych przypadkach ten alert wykrywa atak przy użyciu typowych narzędzi ataku.
* **Atak siłowy na poświadczenia SQL**: Ten alert jest wyzwalany, gdy ma nietypowo dużej liczby nieudanych prób logowania przy użyciu różnych poświadczeń. W niektórych przypadkach ten alert wykrywa przeprowadzany test penetracji. W innych przypadkach ten alert wykrywa atak siłowy.

## <a name="contextual-information"></a>Informacje kontekstowe
Podczas badania analitycy potrzebują dodatkowego kontekstu w celu określenia rodzaju zagrożenia i sposobu jego wyeliminowania.  Przykład: wykryto anomalię dotyczącą sieci, ale bez zrozumienia tego, jakie inne akcje mają miejsce w sieci lub są związane z zasobem, którego dotyczy zagrożenie, bardzo trudno określić, jakie działania należy podjąć. Aby ułatwić rozwiązanie tego, zdarzenie naruszenia zabezpieczeń może zawierać artefakty, powiązane zdarzenia i informacje, które mogą pomóc analitykom. Dostępność dodatkowych informacji będzie różna zależnie od typu wykrytego zagrożenia i konfiguracji środowiska — nie będą one dostępne dla wszystkich zdarzeń naruszenia zabezpieczeń.

Jeśli dostępne są dodatkowe informacje, zostaną one wyświetlone w zdarzeniu naruszenia zabezpieczeń poniżej listy alertów. Może ono zawierać informacje, takie jak:

- Zdarzenia czyszczenia dziennika
- Urządzenie PNP podłączone z nieznanego urządzenia
- Alerty, które nie są informacje z możliwością działania
- Tworzenie nowego konta
- Plik zdekodowany przy użyciu narzędzia certutil

![Alert o nietypowym dostępie](./media/security-center-alerts-type/security-center-alerts-type-fig20.png)


## <a name="next-steps"></a>Kolejne kroki
W tym artykule opisano różne typy alertów zabezpieczeń w usłudze Security Center. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Obsługa zdarzeń naruszenia zabezpieczeń w usłudze Azure Security Center](security-center-incident.md)
* [Funkcje wykrywania usługi Azure Security Center](security-center-detection-capabilities.md)
* [Przewodnik planowania i obsługi usługi Azure Security Center](security-center-planning-and-operations-guide.md)
* [Usługa Azure Security Center — często zadawane pytania](security-center-faq.md): Odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog Azure security](https://blogs.msdn.com/b/azuresecurity/): Wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.
