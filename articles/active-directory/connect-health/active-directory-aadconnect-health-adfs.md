---
title: Używanie programu Azure AD Connect Health z usługami AD FS | Microsoft Docs
description: To jest strona programu Azure AD Connect Health, która informuje, jak monitorować lokalną infrastrukturę usług AD FS.
services: active-directory
documentationcenter: ''
author: zhiweiw
manager: mtillman
editor: curtand
ms.assetid: dc0e53d8-403e-462a-9543-164eaa7dd8b3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/26/2018
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 317b7103dd3c5e6aada4f8d10e57204fcb6d968d
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37887729"
---
# <a name="monitor-ad-fs-using-azure-ad-connect-health"></a>Monitorowanie usług AD FS za pomocą programu Azure AD Connect Health
Poniższa dokumentacja dotyczy monitorowania infrastruktury usług AD FS przy użyciu programu Azure AD Connect Health. Aby uzyskać informacje na temat monitorowania programu Azure AD Connect (synchronizacja) za pomocą programu Azure AD Connect Health, zobacz [Używanie programu Azure AD Connect Health w celu synchronizacji](active-directory-aadconnect-health-sync.md). Ponadto, aby uzyskać informacje na temat monitorowania Usług domenowych Active Directory za pomocą programu Azure AD Connect Health, zobacz [Używanie programu Azure AD Connect Health z usługami AD DS](active-directory-aadconnect-health-adds.md).

## <a name="alerts-for-ad-fs"></a>Alerty dla usług AD FS
Sekcja Alerty programu Azure AD Connect Health udostępnia listę aktywnych alertów. Każdy alert zawiera istotne informacje, kroki do rozwiązania problemu i linki do powiązanej dokumentacji.

Kliknij dwukrotnie aktywny lub rozwiązany alert, aby otworzyć nowy blok z dodatkowymi informacjami, krokami, jakie można podjąć, aby rozwiązać alert, oraz linkami do dodatkowej dokumentacji. Można również wyświetlić dane historyczne na temat alertów, które zostały rozwiązane w przeszłości.

![Portal programu Azure AD Connect Health](./media/active-directory-aadconnect-health/alert2.png)

## <a name="usage-analytics-for-ad-fs"></a>Analiza użycia usług AD FS
Analiza użycia programu Azure AD Connect Health analizuje ruch na serwerach federacyjnych związany z uwierzytelnianiem. Kliknij dwukrotnie pole analizy użycia, aby otworzyć blok analizy użycia, który zawiera metryki i grupowania.

> [!NOTE]
> Aby móc korzystać z analizy użycia dla usług AD FS, należy się upewnić, że inspekcja usług AD FS jest włączona. Aby uzyskać więcej informacji, zobacz [Włączanie inspekcji dla usług AD FS](active-directory-aadconnect-health-agent-install.md#enable-auditing-for-ad-fs).
>
>

![Portal programu Azure AD Connect Health](./media/active-directory-aadconnect-health/report1.png)

Aby wybrać dodatkowe metryki, określić zakres czasu lub zmienić grupowanie, kliknij prawym przyciskiem myszy wykres analizy użycia i wybierz pozycję Edytuj wykres. Następnie możesz określić zakres czasu, wybrać inne metryki oraz zmienić grupowanie. Za pomocą odpowiednich parametrów funkcji „grupuj według” opisanych w poniższej sekcji możesz zobaczyć rozkład ruchu uwierzytelniania w oparciu o różne „metryki” i pogrupować wszystkie metryki.

**Metryka: Łączna liczba żądań** — łączna liczba żądań przetworzonych przez serwery usług AD FS.

|Grupuj według | Co oznacza grupowanie i dlaczego jest przydatne? |
| --- | --- |
| Wszyscy | Pokazuje łączną liczbę żądań przetworzonych przez wszystkie serwery usług AD FS.|
| Aplikacja | Grupuj wszystkie żądania na podstawie docelowej jednostki zależnej. To grupowanie przydaje się, aby sprawdzić, która aplikacja ma największy procentowy udział w całkowitym ruchu sieciowym. |
|  Serwer |Grupuje wszystkie żądania na podstawie serwera, który przetwarzał żądanie. Dzięki takiemu grupowaniu można sprawdzić rozkład obciążenia dla całego ruchu sieciowego.
| Urządzenia dołączone w miejscu pracy |Grupuje wszystkie te żądania, które pochodzą z urządzeń dołączonych w miejscu pracy (znane). Dzięki takiemu grupowaniu można sprawdzić, czy do Twoich zasobów uzyskują dostęp urządzenia nieznane dla infrastruktury do obsługi tożsamości. |
|  Metoda uwierzytelniania | Grupuje wszystkie żądania na podstawie metody uwierzytelniania użytej podczas uwierzytelniania. Dzięki takiemu grupowaniu można się dowiedzieć, jaka metoda uwierzytelniania jest najczęściej używana podczas uwierzytelniania. Możliwe są następujące metody uwierzytelniania <ol> <li>Zintegrowane uwierzytelnianie systemu Windows (system Windows)</li> <li>Uwierzytelnianie oparte na formularzach (Formularze)</li> <li>Logowanie jednokrotne (SSO, Single Sign On)</li> <li>Uwierzytelnianie w oparciu o certyfikat standardu X509 (Certyfikat)</li> <br>Jeśli serwery federacyjne otrzymają żądanie przez plik cookie SSO, żądanie jest traktowane jako SSO (logowanie jednokrotne). W takim przypadku, jeśli plik cookie jest ważny, użytkownik nie jest proszony o dostarczenie poświadczeń i od razu uzyskuje dostęp do aplikacji. To zachowanie ma miejsce często, gdy ma się wiele jednostek zależnych chronionych przez serwery federacyjne. |
| Lokalizacja sieciowa | Grupuje wszystkie żądania na podstawie lokalizacji sieciowej użytkownika. Może to być zarówno sieć intranet, jak i ekstranet. Dzięki takiemu grupowaniu można sprawdzić, jaki procent ruchu sieciowego pochodzi z intranetu, a jaki z ekstranetu. |


**Metryka: Łączna liczba żądań zakończonych niepowodzeniem** — łączna liczba żądań zakończonych niepowodzeniem przetworzonych przez usługę federacyjną. (Ta metryka jest dostępna tylko w usługach AD FS dla systemu Windows Server 2012 R2)

|Grupuj według | Co oznacza grupowanie i dlaczego jest przydatne? |
| --- | --- |
| Typ błędu | Wyświetla liczbę błędów na podstawie wcześniej zdefiniowanych typów błędów. Takie grupowanie umożliwia zapoznanie się z najczęściej popełnianymi typami błędów. <ul><li>Nieprawidłowa nazwa użytkownika lub hasło: Błędy spowodowane nieprawidłową nazwą użytkownika lub nieprawidłowym hasłem.</li> <li>„Zablokowany ekstranet”: Niepowodzenia spowodowane żądaniami otrzymanymi od użytkownika, któremu został zablokowany dostęp do sieci ekstranet. </li><li> „Hasło nieaktualne”: Niepowodzenia spowodowane logowaniem się użytkowników przy użyciu nieaktualnych haseł.</li><li>„Konto wyłączone”: Niepowodzenia spowodowane logowaniem się użytkowników przy użyciu wyłączonych kont.</li><li>„Uwierzytelnianie urządzenia”: Niepowodzenia spowodowane nieudanymi uwierzytelnieniami użytkowników przy użyciu uwierzytelniania urządzenia.</li><li>„Uwierzytelnianie certyfikatu użytkownika”: Niepowodzenia wynikające z nieudanych uwierzytelnień użytkowników z powodu nieprawidłowego certyfikatu.</li><li>„MFA”: Niepowodzenia wynikające z nieudanych uwierzytelnień użytkowników przy użyciu usługi Multi Factor Authentication.</li><li>„Inne poświadczenia”: „Autoryzacja wystawiania”: Niepowodzenia spowodowane błędami autoryzacji.</li><li>„Delegowanie wystawiania”: Niepowodzenia spowodowane błędami delegowania wystawiania.</li><li>„Akceptacja tokenu”: Niepowodzenia spowodowane odrzuceniem przez usługi AD FS tokenu od innego dostawcy tożsamości.</li><li>„Protokół”: Niepowodzenie z powodu błędów protokołu.</li><li>„Nieznane”: Wszystkie inne rodzaje niepowodzeń. Wszystkie inne niepowodzenia, które nie mieszczą się w zdefiniowanych kategoriach.</li> |
| Serwer | Grupuje błędy według serwera. Dzięki takiemu grupowaniu można sprawdzić, jak wygląda rozkład błędów między serwerami. Nierównomierny rozkład może oznaczać uszkodzenie serwera. |
| Lokalizacja sieciowa | Grupuje błędy na podstawie lokalizacji sieciowej żądań (intranet lub ekstranet). Dzięki takiemu grupowaniu można sprawdzić typy żądań, które kończą się niepowodzeniem. |
|  Aplikacja | Grupuje niepowodzenia na podstawie aplikacji docelowej (jednostki zależnej). Dzięki takiemu grupowaniu można sprawdzić, która aplikacja docelowa ma do czynienia z największą liczbą błędów. |

**Metryka: Liczba użytkowników** — średnia liczba unikatowych użytkowników aktywnie uwierzytelnianych za pomocą usług AD FS.

|Grupuj według | Co oznacza grupowanie i dlaczego jest przydatne? |
| --- | --- |
|Wszyscy |Ta metryka zapewnia liczenie średniej liczby użytkowników korzystających z usługi federacyjnej w wybranym przedziale czasu. Użytkownicy nie są zgrupowani. <br>Średnia zależy od wybranego przedziału czasu. |
| Aplikacja |Grupuje średnią liczbę użytkowników na podstawie aplikacji docelowej (jednostki zależnej). Dzięki takiemu grupowaniu można sprawdzić, ilu użytkowników korzysta z danej aplikacji. |

## <a name="performance-monitoring-for-ad-fs"></a>Monitorowanie wydajności dla usług AD FS
Funkcja monitorowania wydajności w programie Azure AD Connect Health udostępnia informacje o metrykach. Wybranie pola Monitorowanie powoduje otwarcie nowego bloku ze szczegółowymi informacjami o metrykach.

![Portal programu Azure AD Connect Health](./media/active-directory-aadconnect-health/perf1.png)

Po wybraniu opcji Filtrowanie u góry bloku można przeprowadzać filtrowanie według serwera, aby uzyskać informacje o poszczególnych metrykach serwera. Aby zmienić metrykę, kliknij prawym przyciskiem myszy wykres monitorowania pod blokiem monitorowania i wybierz pozycję Edytuj wykres (lub wybierz przycisk Edytuj wykres). W nowo otwartym bloku możesz wybrać dodatkowe metryki z listy rozwijanej i określić zakres czasu w celu wyświetlenia danych wydajności.

## <a name="top-50-users-with-failed-usernamepassword-logins"></a>50 użytkowników z największą liczbą nieudanych logowań z powodu błędnej nazwy użytkownika lub błędnego hasła
Typowe przyczyny niepowodzenia żądania uwierzytelnienia na serwerze usług AD FS to żądanie z nieprawidłowymi poświadczeniami, czyli z nieprawidłową nazwą użytkownika lub nieprawidłowym hasłem. Zwykle jest to spowodowane przez użycie złożonych haseł, zapomnienie haseł lub literówki.

Jednak istnieją inne przyczyny nieoczekiwanej liczby żądań obsługiwanych przez serwery usług AD FS, takie jak: wygaśnięcie poświadczeń użytkownika przechowywanych w pamięci podręcznej aplikacji lub próba zalogowania się do konta przez złośliwego użytkownika przy użyciu serii dobrze znanych haseł. Te dwa przykłady są potencjalnymi przyczynami wzrostu liczby żądań.

Program Azure AD Connect Health dla usług AD FS dostarcza raport dotyczący 50 użytkowników z największą liczbą nieudanych prób logowania z powodu wpisania nieprawidłowej nazwy użytkownika lub nieprawidłowego hasła. Ten raport jest uzyskiwany przez przetwarzanie zdarzeń inspekcji generowanych przez wszystkie serwery usług AD FS w farmach.

![Portal programu Azure AD Connect Health](./media/active-directory-aadconnect-health-adfs/report1a.png)

Ten raport daje łatwy dostęp do następujących informacji:

* Łączna liczba nieudanych żądań z błędną nazwą użytkownika lub błędnym hasłem w ciągu ostatnich 30 dni.
* Średnia dzienna liczba użytkowników, którzy popełnili błędy podczas logowania się w wyniku podania nieprawidłowej nazwy użytkownika lub nieprawidłowego hasła.

Kliknięcie tej części przenosi do głównego bloku raportu z dodatkowymi informacjami. Ten blok zawiera wykres z informacjami o trendach, który ułatwia ustalenie linii bazowej dotyczącej żądań z nieprawidłową nazwą użytkownika lub hasłem. Zawiera również listę 50 użytkowników o największej liczbie nieudanych prób w ciągu ostatniego tygodnia. Informacje o tych użytkownikach mogą pomóc w identyfikacji problemów związanych z nagłym wzrostem liczby przypadków podania nieprawidłowego hasła.  

Wykres udostępnia następujące informacje:

* Łączna liczba nieudanych logowań w ciągu jednego dnia z powodu podania nieprawidłowej nazwy użytkownika lub nieprawidłowego hasła.
* Łączna liczba unikatowych użytkowników w ciągu jednego dnia, którym nie udało się zalogować.
* Adres IP klienta dla ostatniego żądania

![Portal programu Azure AD Connect Health](./media/active-directory-aadconnect-health-adfs/report3a.png)

Raport zawiera następujące informacje:

| Element raportu | Opis |
| --- | --- |
| Identyfikator użytkownika |Wyświetla użyty identyfikator użytkownika. Jest to wartość wpisana przez użytkownika, co w niektórych przypadkach powoduje użycie nieprawidłowego identyfikatora użytkownika. |
| Nieudane próby |Pokazuje łączną liczbę nieudanych prób dla konkretnego identyfikatora użytkownika. Tabela jest sortowana według największej liczby nieudanych prób w kolejności malejącej. |
| Ostatnia nieudana próba |Wyświetla sygnaturę czasową momentu, w którym wystąpiła ostatnia awaria. |
| Adres IP ostatniej nieudanej próby |Wyświetla adres IP klienta dla ostatniego złego żądania. Jeśli w obszarze tej wartości widzisz kilka adresów IP, może on zawierać adres IP klienta na potrzeby przekazywania oraz adres IP użytkownika dla ostatniej próby żądania.  |

> [!NOTE]
> Ten raport jest aktualizowany automatycznie co 12 godzin o nowe informacje, które pojawiły się w tym czasie. W rezultacie próby logowania z ostatnich 12 godzin mogą nie zostać zawarte w raporcie.
>
>

## <a name="risky-ip-report-public-preview"></a>Raport ryzykownych adresów IP (podgląd publiczny)
Klienci usług AD FS mogą uwidaczniać w Internecie punkty końcowe uwierzytelniania za pomocą hasła w celu udostępniania użytkownikom końcowym usług uwierzytelniania umożliwiających dostęp do aplikacji SaaS, takich jak usługa Office 365. W takim przypadku możliwe są nieuprawnione próby logowania przy użyciu systemu AD FS w celu odgadnięcia hasła użytkownika końcowego i uzyskania dostępu do zasobów aplikacji. Od wersji 2012 R2 systemu Windows Server usługi AD FS udostępniają funkcję blokady konta na ekstranecie, która uniemożliwia przeprowadzenie takich ataków. Jeśli korzystasz ze starszej wersji, zdecydowanie zalecamy uaktualnienie systemu AD FS do wersji 2016 systemu Windows Server. <br />
Ponadto możliwe jest podejmowanie szeregu prób logowania z jednego adresu IP przy użyciu danych wielu użytkowników. W takich przypadkach liczba prób na użytkownika może być mniejsza od wartości progowej ochronnej blokady konta w usługach AD FS. Aktualnie program Azure AD Connect Health udostępnia raport ryzykownych adresów IP, który pozwala wykryć ten stan i powiadomić administratorów. Poniżej wymieniono najważniejsze zalety korzystania z tego raportu: 
- Wykrywanie adresów IP, w przypadku których przekroczono próg nieudanych prób logowania opartego na haśle
- Obsługa nieudanych prób logowania związanych z nieprawidłowym hasłem lub stanem blokady ekstranetu
- Możliwość natychmiastowego powiadamiania administratorów za pomocą konfigurowalnych wiadomości e-mail
- Możliwość konfiguracji ustawień progowych odpowiadających zasadom zabezpieczeń używanym w organizacji
- Możliwość pobierania raportów w celu ich analizy w trybie offline oraz integracji z innymi systemami za pomocą automatyzacji

> [!NOTE]
> Aby móc korzystać z tego raportu, należy się upewnić, że inspekcja usług AD FS jest włączona. Aby uzyskać więcej informacji, zobacz [Włączanie inspekcji dla usług AD FS](active-directory-aadconnect-health-agent-install.md#enable-auditing-for-ad-fs). <br />
> W celu uzyskania dostępu do wersji zapoznawczej wymagane są uprawnienia administratora globalnego lub [czytelnika zabezpieczeń](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#security-reader).  
> 

### <a name="what-is-in-the-report"></a>Zawartość raportu
Poszczególne elementy raportu ryzykownych adresów IP zawierają zagregowane informacje o nieudanych operacjach logowania za pomocą usług AD FS, przekraczających wyznaczoną wartość progową. Raport udostępnia następujące informacje: ![Portal programu Azure AD Connect Health](./media/active-directory-aadconnect-health-adfs/report4a.png)

| Element raportu | Opis |
| ------- | ----------- |
| Sygnatura czasowa | Zawiera sygnaturę czasową początku okna detekcji, opartą na czasie lokalnym witryny Azure Portal.<br /> Wszystkie zdarzenia dzienne są generowane o północy czasu UTC. <br />Sygnatura czasowa zdarzeń godzinowych jest zaokrąglona do początku godziny. Informację o godzinie rozpoczęcia pierwszego działania zawiera element „firstAuditTimestamp” w wyeksportowanym pliku. |
| Typ wyzwalacza | Zawiera typ przedziału czasu wykrywania. Wyzwalacze agregacji mogą być godzinne lub dziennie. Ten element ułatwia odróżnienie ataków siłowych o dużej częstotliwości od powolnych ataków, w przypadku których próby uzyskania dostępu są rozłożone na cały dzień. |
| Adres IP | Pojedynczy ryzykowny adres IP, powiązany z blokadą ekstranetu lub próbą logowania przy użyciu nieprawidłowego hasła. Może to być adres IPv4 lub IPv6. |
| Liczba błędów dotyczących nieprawidłowego hasła | Liczba błędów dotyczących nieprawidłowego hasła związanych z tym adresem IP, które wystąpiły w danym przedziale czasu wykrywania. W przypadku niektórych użytkowników błędy dotyczące użycia nieprawidłowego hasła mogą występować wielokrotnie. Zwróć uwagę, że nie obejmują one prób logowania, które nie powiodły się z powodu nieaktualnego hasła. |
| Liczba błędów dotyczących blokady ekstranetu | Liczba błędów dotyczących blokady ekstranetu związanych z tym adresem IP, które wystąpiły w danym przedziale czasu wykrywania. W przypadku niektórych użytkowników błędy dotyczące blokady ekstranetu mogą występować wielokrotnie. Będą one widoczne tylko wtedy, gdy skonfigurowano blokadę ekstranetu w usługach AD FS (w wersji 2012 R2 lub nowszej). <b>Uwaga</b> Zdecydowanie zalecamy włączenie tej funkcji w przypadku zezwolenia na logowanie z ekstranetu przy użyciu hasła. |
| Unikatowi użytkownicy, dla których podjęto próbę | Liczba kont unikatowych użytkowników związanych z tym adresem IP, przy użyciu których podjęto próbę w danym przedziale czasu wykrywania. Umożliwia ona odróżnienie wzorca ataku przy użyciu jednego konta użytkownika od wzorca ataku przy użyciu wielu kont użytkowników.  |

Na przykład zgodnie z poniższym elementem raportu w dniu 28.02.2018 w godzinach 18:00–19:00 z adresu IP <i>104.2XX.2XX.9</i> zainicjowano próby uzyskania dostępu, które spowodowały wystąpienie 284 błędów blokady ekstranetu. Nie wystąpiły błędy związane z nieprawidłowym hasłem. W ramach kryteriów zdarzenia obejmowały 14 unikatowych użytkowników. Działania przekroczyły wartość progową określoną w raporcie. 

![Portal programu Azure AD Connect Health](./media/active-directory-aadconnect-health-adfs/report4b.png)

> [!NOTE]
> - Raport zawiera tylko działania przekraczające wyznaczoną wartość progową. 
> - Raport może obejmować zdarzenia sprzed maksymalnie 30 dni.
> - Raport z alertem nie zawiera adresów IP programu Exchange ani prywatnych adresów IP. Jednak można je znaleźć na wyeksportowanej liście. 
>

![Portal programu Azure AD Connect Health](./media/active-directory-aadconnect-health-adfs/report4c.png)

### <a name="load-balancer-ip-addresses-in-the-list"></a>Adresy IP modułu równoważenia obciążenia są zawarte na liście
Moduł równoważenia obciążenia agreguje nieudane działania związane z logowaniem i osiąga próg alertu. Jeśli adresy IP modułu równoważenia obciążenia są widoczne, prawdopodobnie zewnętrzny moduł równoważenia obciążenia nie wysyła adresu IP klienta podczas przekazywania żądania do serwera proxy aplikacji internetowych. Skonfiguruj prawidłowo moduł równoważenia obciążenia, aby przekazać adres IP klienta na potrzeby przekazywania dalej. 

### <a name="download-risky-ip-report"></a>Pobieranie raportu ryzykownych adresów IP 
Przy użyciu funkcji **pobierania** całą listę ryzykownych adresów IP z ostatnich 30 dni można wyeksportować z portalu programu Connect Health. Wyeksportowane dane obejmują wszystkie nieudane próby logowania przy użyciu usług AD FS w poszczególnych przedziałach czasu wykrywania. Do wyeksportowanych informacji można zastosować własne filtry. Oprócz agregacji wyróżnionych w portalu wyeksportowane dane zawierają również dodatkowe szczegóły nieudanych prób logowania związanych z konkretnym adresem IP:

|  Element raportu  |  Opis  | 
| ------- | ----------- | 
| firstAuditTimestamp | Zawiera sygnaturę czasową pierwszej nieudanej próby w przedziale czasu wykrywania.  | 
| lastAuditTimestamp | Zawiera sygnaturę czasową ostatniej nieudanej próby w przedziale czasu wykrywania.  | 
| attemptCountThresholdIsExceeded | Flaga ustawiana, jeśli bieżące działania przekraczają próg alertu.  | 
| isWhitelistedIpAddress | Flaga ustawiana, jeśli adres IP został odfiltrowany z alertów i raportów. Prywatne adresy IP (<i>10.x.x.x, 172.x.x.x & 192.168.x.x</i>) i adresy IP programu Exchange są odfiltrowane i oznaczone wartością True. Jeśli zakresy prywatnych adresów IP są widoczne, prawdopodobnie zewnętrzny moduł równoważenia obciążenia nie wysyła adresu IP klienta podczas przekazywania żądania do serwera proxy aplikacji internetowych.  | 

### <a name="configure-notification-settings"></a>Konfigurowanie ustawień powiadomień
Zawarte w raporcie dane kontaktowe administratorów można zaktualizować za pośrednictwem **ustawień powiadomień**. Domyślnie powiadomienia e-mail z alertem o ryzykownych adresach IP są wyłączone. Można je włączyć za pomocą przełącznika w obszarze „Otrzymuj powiadomienia e-mail dotyczące raportu adresów IP przekraczających próg nieudanych działań”. Podobnie jak w przypadku ogólnych ustawień powiadomień z alertami w programie Connect Health, można tu dostosować listę adresatów powiadomień o raporcie o ryzykownych adresach IP. Wprowadzając zmiany, można również powiadomić wszystkich administratorów globalnych. 

### <a name="configure-threshold-settings"></a>Konfigurowanie ustawień progowych
Próg alertu można zaktualizować za pomocą ustawień progowych. W systemie jest ustawiony domyślny próg. Ustawienia progowe raportu o ryzykownych adresach IP zawierają cztery kategorie:

![Portal programu Azure AD Connect Health](./media/active-directory-aadconnect-health-adfs/report4d.png)

| Element progu | Opis |
| --- | --- |
| Nieprawidłowy użytkownik/hasło + blokada ekstranetu — dzień  | Ustawienie progowe sterujące raportowaniem działania i wyzwoleniem powiadomienia o alercie w przypadku przekroczenia liczby zdarzeń związanych z podaniem nieprawidłowego hasła oraz blokadą ekstranetu na **dzień**. |
| Nieprawidłowy użytkownik/hasło + blokada ekstranetu — godzina | Ustawienie progowe sterujące raportowaniem działania i wyzwoleniem powiadomienia o alercie w przypadku przekroczenia liczby zdarzeń związanych z podaniem nieprawidłowego hasła oraz blokadą ekstranetu na **godzinę**. |
| Blokada ekstranetu — dzień | Ustawienie progowe sterujące raportowaniem działania i wyzwoleniem powiadomienia o alercie w przypadku przekroczenia liczby zdarzeń związanych z blokadą ekstranetu na **dzień**. |
| Blokada ekstranetu — godzina| Ustawienie progowe sterujące raportowaniem działania i wyzwoleniem powiadomienia o alercie w przypadku przekroczenia liczby zdarzeń związanych z blokadą ekstranetu na **godzinę**. |

> [!NOTE]
> - Zmiana progu raportu zostanie zastosowana po godzinie od zmiany ustawień. 
> - Nie wpłynie ona na istniejące elementy raportu. 
> - Zaleca się dostosowanie progu na podstawie liczby zdarzeń występujących w danym środowisku. 
>
>

### <a name="faq"></a>Często zadawane pytania
1. Dlaczego w raporcie są widoczne zakresy prywatnych adresów IP?  <br />
Prywatne adresy IP (<i>10.x.x.x, 172.x.x.x & 192.168.x.x</i>) i adresy IP programu Exchange są odfiltrowane i oznaczone wartością True na liście dozwolonych adresów IP. Jeśli zakresy prywatnych adresów IP są widoczne, prawdopodobnie zewnętrzny moduł równoważenia obciążenia nie wysyła adresu IP klienta podczas przekazywania żądania do serwera proxy aplikacji internetowych.

2. Dlaczego w raporcie widzę adresy IP modułu równoważenia obciążenia?  <br />
Jeśli adresy IP modułu równoważenia obciążenia są widoczne, prawdopodobnie zewnętrzny moduł równoważenia obciążenia nie wysyła adresu IP klienta podczas przekazywania żądania do serwera proxy aplikacji internetowych. Skonfiguruj prawidłowo moduł równoważenia obciążenia, aby przekazać adres IP klienta na potrzeby przekazywania dalej. 

3. Co zrobić, aby zablokować adres IP?  <br />
Złośliwy adres IP należy dodać do zapory lub blokady w programie Exchange.   <br />

4. Dlaczego raport nie zawiera żadnych elementów? <br />
   - Nieudane działania związane z logowaniem nie przekraczają ustawień progowych. 
   - Upewnij się, że lista serwerów AD FS nie zawiera aktywnego alertu „Dane usługi kondycji są nieaktualne”.  Dowiedz się więcej o tym, jak [rozwiązywać problemy z tym alertem](active-directory-aadconnect-health-data-freshness.md).
   - Na farmach AD FS nie włączono inspekcji.
 
5. Dlaczego nie mam dostępu do raportu?  <br />
Wymagane są uprawnienia administratora globalnego lub [czytelnika zabezpieczeń](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#security-reader). Skontaktuj się z administratorem globalnym, aby uzyskać dostęp.


## <a name="related-links"></a>Powiązane linki
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Instalowanie agenta programu Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Operacje w programie Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Używanie programu Azure AD Connect Health w celu synchronizacji](active-directory-aadconnect-health-sync.md)
* [Używanie programu Azure AD Connect Health z usługami AD DS](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect Health — często zadawane pytania](active-directory-aadconnect-health-faq.md)
* [Historia wersji programu Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)
