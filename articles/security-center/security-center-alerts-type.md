---
title: Alerty zabezpieczeń według typu w usłudze Azure Security Center | Microsoft Docs
description: W tym artykule omówiono różne rodzaje alertów zabezpieczeń dostępnych w usłudze Azure Security Center.
services: security-center
documentationcenter: na
author: terrylan
manager: mbaldwin
editor: ''
ms.assetid: b3e7b4bc-5ee0-4280-ad78-f49998675af1
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/21/2018
ms.author: yurid
ms.openlocfilehash: 0573442568115fc872cc4cf4cf8c369cd635028e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51262118"
---
# <a name="understanding-security-alerts-in-azure-security-center"></a>Informacje o alertach zabezpieczeń w usłudze Azure Security Center
Ten artykuł ułatwia zapoznanie się z różnymi typami alertów zabezpieczeń i powiązanych szczegółowych informacji dostępnych w usłudze Azure Security Center. Więcej informacji na temat zarządzania alertami i zdarzeniami znajduje się w artykule [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md).

Aby skonfigurować wykrywanie zaawansowane, przeprowadź uaktualnienie usługi Azure Security Center do wersji Standard. Dostępna jest bezpłatna 60-dniowa wersja próbna. W celu uaktualnienia wybierz pozycję **Warstwa cenowa** w obszarze [Zasady zabezpieczeń](security-center-policies.md). Aby dowiedzieć się więcej, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/security-center/).

> [!NOTE]
> Usługa Security Center została wydana w ograniczonej wersji zapoznawczej z nowym zestawem funkcji wykrywania, które wykorzystują rekordy inspekcji, czyli wspólną platformę inspekcji, do wykrywania złośliwych zachowań na maszynach z systemem Linux. Wyślij [nam](mailto:ASC_linuxdetections@microsoft.com) wiadomość e-mail ze swoim identyfikatorem subskrypcji, aby dołączyć do tej wersji zapoznawczej.

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

* **Wykryto wykonanie podejrzanego procesu**: osoby atakujące często próbują wykonać złośliwy kod bez wykrycia, maskując procesy jako niegroźne. Te alerty wskazują, że wykonanie procesu jest zgodne z jednym z następujących wzorców:
    * Wykonany proces jest znany z użycia do złośliwych celów. Poszczególne polecenia mogą wydawać się niegroźne, ale alert jest oceniany na podstawie na agregacji tych poleceń.
    * Proces został wykonany z nietypowej lokalizacji.
    * Proces został wykonany z lokalizacji powiązanej z podejrzanymi plikami.
    * Proces został wykonany z podejrzanej ścieżki.
    * Proces został wykonany w nietypowym kontekście.
    * Proces został wykonany z poziomu nietypowego konta.
    * Wykonany proces miał podejrzane rozszerzenie.
    * Wykonany proces miał podejrzane podwójne rozszerzenie.
    * Wykonany proces miał w nazwie pliku podejrzany znak typu „od prawej do lewej”.
    * Wykonany proces miał nazwę bardzo podobną do nazwy bardzo często uruchamianego procesu, ale inną.
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

* **Podejrzane działanie zasobu protokołu RDP**: osoby atakujące często wybierają otwarte porty zarządzania, takie jak protokół RDP, jako cel ataków siłowych. Te alerty wskazują wystąpienie podejrzanego działania logowania do Pulpitu zdalnego, co oznacza, że:
    * Podjęto próby logowania do Pulpitu zdalnego.
    * Podjęto próby logowania do Pulpitu zdalnego przy użyciu nieprawidłowych kont.
    * Podjęto próby logowania do Pulpitu zdalnego, a niektóre z nich spowodowały pomyślnie zalogowanie do maszyny.
* **Podejrzane działanie zasobu protokołu SSH**: osoby atakujące często wybierają otwarte porty zarządzania, takie jak protokół SSH, jako cel ataków siłowych. Te alerty wskazują wystąpienie podejrzanego działania logowania do protokołu SSH, co oznacza, że:
    * Podjęto nieudane próby logowania do protokołu SSH.
    * Podjęto próby logowania do protokołu SSH, a niektóre z nich zakończyły się pomyślnie.
* **Podejrzana wartość rejestru WindowPosition**: ten alert wskazuje, że podjęto próbę wprowadzenia zmiany konfiguracji rejestru WindowPosition, co może świadczyć o ukrywaniu okien aplikacji w niewidocznych sekcjach pulpitu.
* **Potencjalna próba obejścia funkcji AppLocker**: funkcja AppLocker może służyć do ograniczania procesów, które można uruchomić w systemie Windows, co zmniejsza stopnień narażenia na działanie złośliwego oprogramowania. Ten alert wskazuje na możliwą próbę ominięcia ograniczeń funkcji AppLocker przez użycie zaufanych plików wykonywalnych (dozwolonych przez zasady funkcji AppLocker) do wykonania niezaufanego kodu.
* **Podejrzana komunikacja z nazwanym potokiem**: ten alert wskazuje, że dane zostały zapisane w lokalnym nazwanym potoku z poziomu polecenia konsoli systemu Windows. Nazwane potoki są używane przez osoby atakujące do przydzielania zadań do złośliwego elementu i komunikowania się z nim.
* **Dekodowanie pliku wykonywalnego przy użyciu wbudowanego narzędzia certutil.exe**: ten alert wskazuje, że wbudowane narzędzie administratora, certutil.exe, zostało użyte w celu dekodowania pliku wykonywalnego. Osoby atakujące nadużywają funkcjonalności wiarygodnych narzędzi administratora w celu wykonania złośliwych akcji. Mogą na przykład użyć narzędzia, takiego jak certutil.exe, do dekodowania złośliwego pliku wykonywalnego, który następnie zostanie wykonany.
* **Dziennik zdarzeń został wyczyszczony**: ten alert wskazuje na podejrzaną operację dziennika zdarzeń, która jest często używana przez osoby atakujące w celu ukrycia śladów ich działania.
* **Wyłączanie i usuwanie plików dziennika usług IIS**: ten alert wskazuje, że pliki dziennika usług IIS zostały wyłączone i/lub usunięte — jest to często stosowane przez osoby atakujące w celu ukrycia śladów ich działania.
* **Podejrzane usunięcie plików**: ten alert wskazuje na podejrzane usunięcie plików, co może być stosowane przez osobę atakującą w celu usunięcia dowodu istnienia złośliwych plików binarnych.
* **Wszystkie kopie pliku w tle zostały usunięte**: ten alert wskazuje, że kopie w tle zostały usunięte.
* **Podejrzane polecenia czyszczenia pliku**: ten alert wskazuje na użycie kombinacji poleceń systeminfo do przeprowadzenia działania samodzielnego czyszczenia po wystąpieniu naruszenia.  Plik *systeminfo.exe* to autentyczne narzędzie systemu Windows, ale jego uruchomienie dwa razy z rzędu, a następnie użycie polecenia Usuń w opisany tutaj sposób jest rzadkie.
* **Podejrzane utworzenie konta**: ten alert wskazuje, że zostało utworzone konto bardzo podobne do istniejącego wbudowanego konta z uprawnieniami administracyjnymi. Ta technika może zostać użyta przez osoby atakujące do utworzenia nieautoryzowanego konta bez wykrycia.
* **Podejrzane działanie kopiowania woluminu w tle**: ten alert wskazuje na działanie usunięcia kopii w tle w ramach zasobu. Kopiowanie woluminów w tle (VSC, Volume Shadow Copy) to ważny artefakt, który przechowuje migawki danych. To działanie jest zwykle kojarzone z oprogramowaniem wymuszającym okup, ale może być również autentyczne.
* **Metoda trwałości rejestru systemu Windows**: ten alert wskazuje na próbę utrzymania pliku wykonywalnego w rejestrze systemu Windows. Złośliwe oprogramowanie często używa takiej techniki w celu przetrwania rozruchu.
* **Podejrzana nowa reguła zapory**: ten alert wskazuje na to, że dodano nową regułę zapory za pośrednictwem pliku *netsh.exe*, aby zezwolić na ruch z pliku wykonywalnego w podejrzanej lokalizacji.
* **Podejrzane wykonania XCOPY**: ten alert wskazuje na serię wykonań XCOPY, co może oznaczać, że bezpieczeństwo jednej z maszyn zostało naruszone i użyto jej do propagowania złośliwego oprogramowania.
* **Pomijanie informacji prawnych wyświetlanych dla użytkowników podczas logowania**: ten alert wskazuje na zmianę klucza rejestru, który kontroluje wyświetlanie informacji prawnych dla użytkowników podczas logowania. Jest to typowe działanie podejmowane przez osoby atakujące po naruszeniu bezpieczeństwa hosta.
* **Wykryto nietypową kombinację wielkich i małych liter w wierszu polecenia**: ten alert wskazuje na użycie kombinacji wielkich i małych liter w wierszu polecenia. Jest to technika stosowana przez osoby atakujące w celu ukrycia się przed regułą uwzględniania wielkości liter lub maszyny opartej na skrótach.
* **Wiersz polecenia z zaciemnionym kodem**: ten alert wskazuje na to, że w wierszu polecenia zostały wykryte podejrzane wskaźniki zaciemnienia.
* **Zapytania o wiele kont domeny**: osoby atakujące podczas rozpoznawania użytkowników często wykonują zapytania o konta domeny usługi AD, konta administratorów domeny, kontrolerów domeny oraz relacje zaufania między domenami. Ten alert wskazuje na to, że w krótkim czasie wykonano nietypową liczbę zapytań o różne konta domeny.
* **Możliwe działanie dotyczące rozpoznania lokalnego**: ten alert wskazuje na wykonanie kombinacji poleceń systeminfo skojarzonych z działaniem rozpoznania.  Plik *systeminfo.exe* to oryginalne narzędzie systemu Windows, ale wykonanie go dwa razy z rzędu jest rzadkie.
* **Możliwe wykonanie pliku wykonywalnego keygen**: ten alert wskazuje na wykonanie procesu, którego nazwa wskazuje na narzędzie keygen. Takie narzędzia są zwykle używane w celu pokonania mechanizmów licencjonowania oprogramowania, ale są one często pobierane w pakiecie z innym złośliwym oprogramowaniem.
* **Podejrzane wykonanie za pośrednictwem pliku rundll32.exe**: ten alert wskazuje na to, że plik rundll32.exe został użyty do wykonania procesu o nietypowej nazwie, zgodnie ze schematem nazewnictwa procesów stosowanym przez osoby atakujące w celu zainstalowania elementu pierwszego etapu na hoście, którego bezpieczeństwo zostało naruszone.
* **Podejrzana kombinacja hosta HTA i programu PowerShell**: ten alert wskazuje, że host HTA (HTML Application Host) firmy Microsoft uruchamia polecenia programu PowerShell. Jest to technika używana przez osoby atakujące do uruchamiania złośliwych skryptów programu PowerShell.
* **Zmiana na klucz rejestru, który może być nadużywany w celu obejścia kontroli konta użytkownika**: ten alert wskazuje, że klucz rejestru, który może być używany do obejścia kontroli konta użytkownika został zmieniony. Ta technika jest często używana przez osoby atakujące w celu przeniesienia z konta o dostępie nieuprzywilejowanym (użytkownik standardowy) do konta o dostępie uprzywilejowanym (np. administrator) na hoście, którego bezpieczeństwo zostało naruszone.
* **Użycie podejrzanej nazwy domeny w wierszu polecenia**: ten alert wskazuje na to, że użyto podejrzanej nazwy domeny, co może być dowodem na to, że osoba atakująca hostuje złośliwe narzędzia i pełni rolę punktu końcowego dla poleceń i kontroli danych oraz ich eksfiltracji.
* **Konto zostało utworzone na wielu hostach w okresie 24-godzinnym**: ten alert wskazuje na to, że nastąpiła próba utworzenia tego samego konta użytkownika na wielu hostach, co może być dowodem na to, że osoba atakująca przenosi się w bok w sieci po naruszeniu bezpieczeństwa co najmniej jednej jednostki sieci.
* **Podejrzane użycie list CACLS w celu obniżenia stanu zabezpieczeń systemu**: ten alert wskazuje na wprowadzenie zmiany listy kontroli zmian dostępu (CACLS). Ta technika jest często używana przez osoby atakujące w celu nadania praw pełnego dostępu systemowym plikom binarnym, takim jak ftp.exe, net.exe, wscript.exe itd.
* **Parametry podejrzenia ataku na „złoty bilet” protokołu Kerberos**: ten alert wskazuje na wykonanie parametrów wiersza polecenia zgodnych z atakiem na „złoty bilet” protokołu Kerberos. Naruszony klucz krbtgt może zostać użyty przez osobę atakującą do podszycia się pod dowolnego użytkownika.
* **Włączenie klucza rejestru WDigest UseLogonCredential**: ten alert wskazuje na to, że klucz rejestru został zmieniony tak, aby zezwalać na przechowywanie poświadczeń logowania w postaci zwykłego tekstu w pamięci LSA, a następnie pobieranie ich z pamięci.
* **Potencjalnie podejrzane użycie narzędzia Telegram**: ten alert wskazuje na zainstalowanie narzędzia Telegram, bezpłatnej, opartej na chmurze usługi do obsługi wiadomości błyskawicznych, której osoby atakujące używają do przesyłania złośliwych plików binarnych do dowolnego komputera, telefonu lub tabletu.
* **Tworzenie nowego punktu ASEP**: ten alert wskazuje na utworzenie nowych punktów ASEP (Auto Start Extensibility Point), co powoduje automatyczne uruchamianie nazwy procesu zidentyfikowanej w wierszu polecenia i może służyć osobie atakującej do osiągnięcia trwałości.
* **Podejrzane zmiany polecenia Set-ExecutionPolicy i usługi WinRM**: ten alert wskazuje na zmiany konfiguracji skojarzone z użyciem złośliwej powłoki internetowej ChinaChopper.
* **Wyłączenie usług krytycznych**: ten alert wskazuje, że polecenie „net.exe stop” zostało użyte do zatrzymania usług krytycznych, takich jak SharedAccess lub Centrum zabezpieczeń systemu Windows.
* **Podejrzane użycie przełącznika FTP -s**: ten alert oznacza użycie przełącznika FTP „-s”, który może służyć w złośliwym oprogramowaniu do łączenia się ze zdalnym serwerem FTP i pobierania dodatkowych złośliwych plików binarnych.
* **Podejrzane wykonanie polecenia VBScript.Encode**: ten alert wskazuje na wykonanie polecenia *VBScript.Encode*, które koduje skrypty do postaci tekstu niemożliwego do odczytania, co utrudnia użytkownikom badanie kodu.
* **Alokacja obiektu HTTP VBScript**: ten alert wskazuje na utworzenie pliku VBScript przy użyciu wiersza polecenia. Może on służyć do pobierania złośliwych plików.
* **Atak typu Klawisze trwałe**: ten alert wskazuje, że osoba atakująca może wykorzystać pliki binarne ułatwień dostępu (takich jak klawisze trwałe, klawiatura ekranowa, narrator) w celu zapewnienia sobie tylnego wejścia do systemu.
* **Wskaźniki oprogramowania wymuszającego okup Petya**: ten alert wskazuje na to, że zaobserwowano techniki skojarzone z oprogramowaniem wymuszającym okup Petya.
* **Próba wyłączenia interfejsu AMSI**: ten alert wskazuje na próbę wyłączenia interfejsu skanowania w celu ochrony przed złośliwym kodem (AMSI), co spowodowałoby wyłączenie wykrywania ochrony przed złośliwym kodem.
* **Wskaźniki oprogramowania wymuszającego okup**: ten alert wskazuje na podejrzane działania tradycyjnie kojarzone z oprogramowaniem wymuszającym okup przez blokowanie ekranu i szyfrowanie.
* **Podejrzany plik wyjściowy zbierania śladów**: ten alert wskazuje na to, że zebrano ślady i umieszczono je jako dane wyjściowe w pliku nietypowego typu.
* **Oprogramowanie wysokiego ryzyka**: ten alert oznacza korzystanie z oprogramowania, które zostało skojarzone z instalacją złośliwego oprogramowania. Osoby atakujące często umieszczają złośliwe oprogramowanie w pakiecie z niegroźnymi narzędziami, takimi jak pokazane w tym alercie, a następnie dyskretnie instalują złośliwe oprogramowanie w tle.
* **Tworzenie podejrzanego pliku**: ten alert wskazuje na utworzenie lub wykonanie procesu używanego przez osoby atakujące do pobierania dodatkowego złośliwego oprogramowania na host, którego bezpieczeństwo zostało naruszone, po otwarciu załącznika w dokumencie przeznaczonym do wyłudzania informacji.
* **Podejrzane poświadczenia w wierszu polecenia**: ten alert wskazuje podejrzane hasło używane do wykonywania pliku. Ta technika była używana przez osoby atakujące do wykonywania złośliwego oprogramowania Pirpi.
* **Możliwe wykonanie pliku podrzucającego złośliwe oprogramowanie**: ten alert wskazuje nazwę pliku, który został użyty przez osoby atakujące do zainstalowania złośliwego oprogramowania.
* **Podejrzane wykonanie za pośrednictwem pliku rundll32.exe**: ten alert wskazuje na to, że plik rundll32.exe jest używany do wykonywania pliku notepad.exe lub reg.exe — jest to spójne z techniką iniekcji procesu stosowaną przez osoby atakujące.
* **Podejrzane argumenty wiersza polecenia**: ten alert wskazuje podejrzane argumenty wiersza polecenia używane w połączeniu z odwrotną powłoką stosowaną przez grupę działań HYDROGEN.
* **Podejrzane poświadczenia dokumentu**: ten alert wskazuje na podejrzany, wstępnie obliczony skrót hasła używany przez złośliwe oprogramowanie stosowane do wykonywania pliku.
* **Dynamiczna konstrukcja skryptu programu PS**: ten alert wskazuje na to, że skrypt programu PowerShell jest tworzony dynamicznie. Osoby atakujące używają tej techniki, aby stopniowo kompilować skrypt w celu omijania systemów IDS.
* **Wskaźniki platformy Metaploit**: ten alert wskazuje na działanie skojarzone z platformą Metasploit, która udostępnia szereg możliwości i narzędzi dla osób atakujących.
* **Podejrzane działanie na koncie**: ten alert wskazuje na próbę połączenia z maszyną za pomocą konta, którego bezpieczeństwo zostało niedawno naruszone.
* **Tworzenie konta**: ten alert wskazuje na utworzenie nowego konta na maszynie.


### <a name="crash-analysis"></a>Analiza awarii


Analiza awaryjnego zrzutu pamięci jest metodą używaną do wykrywania zaawansowanego złośliwego oprogramowania, które jest w stanie ominąć tradycyjne rozwiązania w zakresie zabezpieczeń. Różne rodzaje złośliwego oprogramowania próbują zmniejszyć prawdopodobieństwo wykrycia przez programy antywirusowe, rezygnując z zapisu na dysku lub szyfrując składniki oprogramowania zapisywane na dysku. Ta technika sprawia, że złośliwe oprogramowanie jest trudne do wykrycia przy użyciu tradycyjnych metod ochrony. Złośliwe oprogramowanie może jednak zostać wykryte za pomocą analizy pamięci, ponieważ aby mogło działać, musi zostawić ślady w pamięci maszyny.

Jeśli wystąpi awaria oprogramowania, zrzut awaryjny przechwytuje część pamięci w chwili pojawienia się awarii. Awaria może być spowodowana przez złośliwe oprogramowanie, powszechnie stosowane aplikacje lub problemy z systemem. Dzięki analizie pamięci w zrzucie awaryjnym usługa Security Center może wykryć metody, jakie zostały użyte w celu wykorzystania luk obecnych w oprogramowaniu, uzyskania dostępu do poufnych danych i dyskretnego pozostania na zainfekowanym komputerze. Jest to realizowane przy minimalnym wpływie na wydajność hostów, ponieważ analiza jest wykonywana przez zaplecze usługi Security Center.

* **Wykryto iniekcję kodu**: iniekcja kodu to wstawianie modułów wykonywalnych do uruchomionych procesów lub wątków. Ta metoda jest używana przez złośliwe oprogramowanie do uzyskiwania dostępu do danych, ukrywania swojej obecności lub zapobiegania usunięciu (np. trwałość). Ten alert wskazuje, że wprowadzony moduł znajduje się w zrzucie awaryjnym. Wiarygodni programiści czasami wykonują iniekcję kodu z niezłośliwych powodów, takich jak modyfikowanie lub rozbudowywanie istniejącej aplikacji albo składnika systemu operacyjnego. Aby ułatwić odróżnienie niezłośliwych modułów od złośliwych, usługa Security Center sprawdza, czy wprowadzony moduł odpowiada profilowi podejrzanego zachowania. Wynik tej kontroli jest widoczny w polu alertu „SIGNATURE” i obejmuje ważność alertu, opis alertu oraz czynności zaradcze alertu.
* **Podejrzany segment kodu**: alert podejrzanego segmentu kodu wskazuje, że segment kodu został przydzielony przy użyciu niestandardowych metod, takich jak metody używane przez iniekcję odbijającą i zamianę pamięci procesu. Dodatkowe charakterystyki segmentu kodu są przetwarzane w celu zapewnienia kontekstu dotyczącego możliwości i zachowań zgłoszonego segmentu kodu.
* **Wykryto kod powłoki**: kod powłoki to ładunek uruchamiany po wykorzystaniu przez złośliwe oprogramowanie luki w zabezpieczeniach oprogramowania. Ten alert oznacza, że analiza zrzutu awaryjnego wykryła zachowanie kodu wykonywalnego typowe dla złośliwych ładunków. Wprawdzie niezłośliwe oprogramowanie może zachowywać się podobnie, jednak nie jest to typowe w przypadku zwykłych metod tworzenia oprogramowania.
* **Wykryto przejęcie modułu**: system Windows używa bibliotek dołączanych dynamicznie (DLL, Dynamic Link Library), aby umożliwić oprogramowaniu korzystanie z typowych funkcji systemu Windows. Przejęcie biblioteki DLL ma miejsce, gdy złośliwe oprogramowanie zmienia kolejność ładowania bibliotek DLL, aby załadować złośliwy ładunek do pamięci, w której może zostać wykonany dowolny kod. Ten alert oznacza, że analiza zrzutu awaryjnego wykryła moduł o podobnej nazwie, który jest ładowany z dwóch różnych ścieżek. Jedna z nich odpowiada typowej lokalizacji plików binarnych systemu Windows. Wiarygodni programiści czasami zmieniają kolejność ładowania bibliotek DLL z niezłośliwych powodów, takich jak instrumentacja albo rozbudowa systemu operacyjnego Windows lub aplikacji. Aby ułatwić rozróżnienie złośliwych i potencjalnie niegroźnych zmian kolejności ładowania bibliotek DLL, usługa Security Center sprawdza, czy załadowany moduł jest zgodny z podejrzanym profilem.
* **Wykryto zamaskowany moduł systemu Windows**: złośliwe oprogramowanie może używać typowych nazw plików binarnych systemu Windows (np. SVCHOST.EXE) lub modułów (np. NTDLL.DLL) w celu ukrycia charakteru złośliwego oprogramowania przed administratorami systemu. Ten alert oznacza, że plik zrzutu awaryjnego zawiera moduły używające nazw modułów systemu Windows, które nie spełniają innych kryteriów typowych dla tego rodzaju modułów. Analiza kopii dyskowej zamaskowanego modułu może dostarczyć dodatkowych informacji na temat wiarygodności lub złośliwości tego modułu.
* **Wykryto zmodyfikowany plik binarny systemu**: złośliwe oprogramowanie może modyfikować podstawowe pliki binarne systemu, aby potajemnie uzyskać dostęp do danych lub niezauważenie przetrwać w zaatakowanym systemie. Ten alert oznacza, że analiza zrzutu awaryjnego wykryła modyfikację podstawowych plików binarnych systemu operacyjnego Windows w pamięci lub na dysku. Wiarygodni programiści czasami modyfikują moduły systemu w pamięci z niezłośliwych powodów, na przykład w celu obejścia lub uzyskania zgodności aplikacji. Aby ułatwić rozróżnienie złośliwych i potencjalnie autentycznych modułów, usługa Security Center sprawdza, czy zmodyfikowany moduł jest zgodny z podejrzanym profilem.

## <a name="network-analysis"></a>Analiza sieci
Wykrywanie zagrożeń sieci za pomocą usługi Security Center polega na automatycznym zbieraniu informacji o zabezpieczeniach uzyskanych na podstawie ruchu protokołu IPFIX (Internet Protocol Flow Information Export) na platformie Azure. Analizuje ona te informacje, często zestawiając informacje z wielu źródeł, aby zidentyfikować zagrożenia.

* **Możliwe przychodzące próby ataków siłowych SQL**: analiza ruchu w sieci wykryła podejrzaną przychodzącą komunikację SQL. To działanie jest zgodne z próbami ataków siłowych podejmowanych na serwerach SQL.
* **Podejrzane przychodzące działanie sieci protokołu RDP z wielu źródeł**: analiza ruchu w sieci wykryła nietypową przychodzącą komunikację protokołu RDP (Remote Desktop Protocol) z wielu źródeł. W szczególności próbkowane dane sieci pokazują unikatowe adresy IP łączące się z maszyną, co jest uznawane za nietypowe w przypadku tego środowiska. To działanie może oznaczać próbę ataku siłowego w punkcie końcowym protokołu RDP z wielu hostów (Botnet).
* **Podejrzane przychodzące działanie sieci protokołu RDP**: analiza ruchu w sieci wykryła nietypową przychodzącą komunikację protokołu RDP (Remote Desktop Protocol). W szczególności próbkowane dane sieci pokazują dużą liczbę przychodzących połączeń z maszyną, co jest uznawane za nietypowe w przypadku tego środowiska. To działanie może oznaczać próbę ataku siłowego w punkcie końcowym protokołu RDP.
* **Podejrzane wychodzące działanie sieci protokołu RDP do wielu miejsc docelowych**: analiza ruchu w sieci wykryła nietypową wychodzącą komunikację protokołu RDP (Remote Desktop Protocol) do wielu miejsc docelowych. To działanie może wskazywać, że bezpieczeństwo maszyny zostało naruszone i obecnie jest używana do przeprowadzania ataków siłowych w zewnętrznych punktach końcowych protokołu RDP. Należy pamiętać, że ten typ działań może powodować oflagowanie adresu IP jako złośliwego przez jednostki zewnętrzne.
* **Podejrzane wychodzące działanie sieci protokołu RDP**: analiza ruchu w sieci wykryła nietypową wychodzącą komunikację protokołu RDP (Remote Desktop Protocol). W szczególności próbkowane dane sieci pokazują dużą liczbę wychodzących połączeń z maszyny, co jest uznawane za nietypowe w przypadku tego środowiska. To działanie może wskazywać, że bezpieczeństwo maszyny zostało naruszone i obecnie jest używana do przeprowadzania ataków siłowych w zewnętrznych punktach końcowych protokołu RDP. Należy pamiętać, że ten typ działań może powodować oflagowanie adresu IP jako złośliwego przez jednostki zewnętrzne.
* **Podejrzane przychodzące działanie sieci protokołu SSH**: analiza ruchu w sieci wykryła nietypową przychodzącą komunikację protokołu SSH. W szczególności próbkowane dane sieci pokazują dużą liczbę przychodzących połączeń z maszyną, co jest uznawane za nietypowe w przypadku tego środowiska. To działanie może oznaczać próbę ataku siłowego w punkcie końcowym protokołu SSH.
* **Podejrzane przychodzące działanie sieci protokołu SSH z wielu źródeł**: analiza ruchu w sieci wykryła nietypową przychodzącą komunikację protokołu SSH. W szczególności próbkowane dane sieci pokazują unikatowe adresy IP łączące się z maszyną, co jest uznawane za nietypowe w przypadku tego środowiska. To działanie może oznaczać próbę ataku siłowego w punkcie końcowym protokołu SSH z wielu hostów (Botnet).
* **Podejrzane wychodzące działanie sieci protokołu SSH**: analiza ruchu w sieci wykryła nietypową wychodzącą komunikację protokołu SSH. W szczególności próbkowane dane sieci pokazują dużą liczbę wychodzących połączeń z maszyny, co jest uznawane za nietypowe w przypadku tego środowiska. To działanie może wskazywać, że bezpieczeństwo maszyny zostało naruszone i obecnie jest używana do przeprowadzania ataków siłowych w zewnętrznych punktach końcowych protokołu SSH. Należy pamiętać, że ten typ działań może powodować oflagowanie adresu IP jako złośliwego przez jednostki zewnętrzne.
* **Podejrzane wychodzące działanie sieci protokołu SSH do wielu miejsc docelowych**: analiza ruchu w sieci wykryła nietypową wychodzącą komunikację protokołu SSH do wielu miejsc docelowych. W szczególności próbkowane dane sieci pokazują maszynę łączącą się z unikatowymi adresami IP, co jest uznawane za nietypowe w przypadku tego środowiska. To działanie może wskazywać, że bezpieczeństwo maszyny zostało naruszone i obecnie jest używana do przeprowadzania ataków siłowych w zewnętrznych punktach końcowych protokołu SSH. Należy pamiętać, że ten typ działań może powodować oflagowanie adresu IP jako złośliwego przez jednostki zewnętrzne.
* **Wykryto komunikację sieciową ze złośliwą maszyną**: analiza ruchu w sieci wskazuje, że maszyna prawdopodobnie komunikowała się z centrum poleceń i kontroli.
* **Wykryto możliwe naruszenie bezpieczeństwa maszyny**: analiza ruchu w sieci wykryła działanie wychodzące, co może oznaczać, że występuje ono jako część botnetu. Analiza opiera się na adresach IP używanych przez zasób razem z publicznymi rekordami systemu DNS.


## <a name="sql-database-and-sql-data-warehouse-analysis"></a>Analiza bazy danych SQL Database i magazynu SQL Data Warehouse

Analiza zasobów usługi Security Center koncentruje się na usługach PaaS, takich jak integracja z funkcją [wykrywania zagrożeń bazy danych Azure SQL Database](../sql-database/sql-database-threat-detection.md) i usługi Azure SQL Data Warehouse. Funkcja wykrywania zagrożeń SQL wykrywa nietypowe działania wskazujące potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub wykorzystania ich luk w zabezpieczeniach i wyzwala następujące alerty:

* **Podatność na iniekcję SQL**: Ten alert jest wyzwalany, gdy aplikacja generuje błędną instrukcję SQL w bazie danych. Może to wskazywać lukę w zabezpieczeniach umożliwiającą ataki przez iniekcję SQL. Istnieją dwie możliwe przyczyny generowania błędnej instrukcji:
    * Usterka w kodzie aplikacji powoduje utworzenie błędnej instrukcji SQL
    * Kod aplikacji lub procedury składowane nie oczyszczają danych wejściowych użytkownika podczas tworzenia błędnej instrukcji SQL, co można wykorzystać do iniekcji SQL
* **Potencjalna iniekcja SQL**: Ten alert jest wyzwalany w przypadku aktywnego wykorzystania zidentyfikowanej luki umożliwiającej iniekcję SQL w aplikacji. Oznacza to, że osoba atakująca próbuje wstrzyknąć złośliwe instrukcje SQL, korzystając z podatnego kodu aplikacji lub procedur składowanych.
* **Dostęp z nietypowej lokalizacji**: Ten alert jest wyzwalany w przypadku zmiany wzorca dostępu do serwera SQL, gdy ktoś zalogował się do serwera SQL z nietypowej lokalizacji geograficznej. W niektórych przypadkach ten alert wykrywa prawidłowe działanie (nowa aplikacja lub konserwacja przeprowadzana przez deweloperów). W innych przypadkach ten alert wykrywa złośliwe działanie (były pracownik, zewnętrzna osoba atakująca).
* **Dostęp z nietypowego centrum danych platformy Azure**: Ten alert jest wyzwalany w przypadku zmiany wzorca dostępu do serwera SQL, gdy ktoś zalogował się do serwera SQL z nietypowego centrum danych platformy Azure, co zostało zaobserwowane na tym serwerze w ostatnim czasie. W niektórych przypadkach ten alert wykrywa prawidłowe działanie (nowa aplikacja na platformie Azure, usługa Power BI, edytor zapytań SQL platformy Azure). W innych przypadkach ten alert wykrywa złośliwe działanie z zasobu/usługi platformy Azure (były pracownik, zewnętrzna osoba atakująca).
* **Dostęp z nieznanego podmiotu zabezpieczeń**: Ten alert jest wyzwalany w przypadku zmiany wzorca dostępu do serwera SQL, gdy ktoś zalogował się do serwera SQL za pomocą nietypowego podmiotu zabezpieczeń (użytkownika SQL). W niektórych przypadkach ten alert wykrywa prawidłowe działanie (nowa aplikacja, konserwacja przeprowadzana przez deweloperów). W innych przypadkach ten alert wykrywa złośliwe działanie (były pracownik, zewnętrzna osoba atakująca).
* **Dostęp z potencjalnie szkodliwej aplikacji**: Ten alert jest wyzwalany, gdy potencjalnie szkodliwa aplikacja jest używana w celu uzyskania dostępu do bazy danych. W niektórych przypadkach ten alert wykrywa przeprowadzany test penetracji. W innych przypadkach ten alert wykrywa atak przy użyciu typowych narzędzi ataku.
* **Atak siłowy na poświadczenia SQL**: Ten alert jest wyzwalany w przypadku nietypowo dużej liczby nieudanych prób logowania przy użyciu różnych poświadczeń. W niektórych przypadkach ten alert wykrywa przeprowadzany test penetracji. W innych przypadkach ten alert wykrywa atak siłowy.

## <a name="contextual-information"></a>Informacje kontekstowe
Podczas badania analitycy potrzebują dodatkowego kontekstu w celu określenia rodzaju zagrożenia i sposobu jego wyeliminowania.  Przykład: wykryto anomalię dotyczącą sieci, ale bez zrozumienia tego, jakie inne akcje mają miejsce w sieci lub są związane z zasobem, którego dotyczy zagrożenie, bardzo trudno określić, jakie działania należy podjąć. Aby ułatwić rozwiązanie tego problemu, zdarzenie naruszenia zabezpieczeń może zawierać artefakty, powiązane zdarzenia i informacje, które mogą pomóc analitykom. Dostępność dodatkowych informacji będzie różna zależnie od typu wykrytego zagrożenia i konfiguracji środowiska — nie będą one dostępne dla wszystkich zdarzeń naruszenia zabezpieczeń.

Jeśli dostępne są dodatkowe informacje, zostaną one wyświetlone w zdarzeniu naruszenia zabezpieczeń poniżej listy alertów. Może ono zawierać informacje, takie jak:

- Zdarzenia czyszczenia dziennika
- Urządzenie PNP podłączone z nieznanego urządzenia
- Alerty, względem których nie można wykonać żadnych działań
- Tworzenie nowego konta
- Plik zdekodowany przy użyciu narzędzia certutil 

![Alert o nietypowym dostępie](./media/security-center-alerts-type/security-center-alerts-type-fig20.png)


## <a name="next-steps"></a>Kolejne kroki
W tym artykule opisano różne typy alertów zabezpieczeń w usłudze Security Center. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Obsługa zdarzeń naruszenia zabezpieczeń w usłudze Azure Security Center](security-center-incident.md)
* [Funkcje wykrywania usługi Azure Security Center](security-center-detection-capabilities.md)
* [Przewodnik planowania i obsługi usługi Azure Security Center](security-center-planning-and-operations-guide.md)
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.
