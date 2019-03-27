---
title: 'Synchronizacja programu Azure AD Connect: Konfigurowanie filtrowania | Dokumentacja firmy Microsoft'
description: Opisano sposób konfigurowania, filtrowanie w usłudze Azure AD Connect sync.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 880facf6-1192-40e9-8181-544c0759d506
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: eeb2af6283e5c9d8a41e74152a94b85efdae1866
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58487321"
---
# <a name="azure-ad-connect-sync-configure-filtering"></a>Synchronizacja programu Azure AD Connect: Konfigurowanie filtrowania
Za pomocą filtrowania, możesz kontrolować obiekty, które są wyświetlane w usłudze Azure Active Directory (Azure AD) z katalogu lokalnego. Domyślna konfiguracja pobiera wszystkie obiekty we wszystkich domenach w lesie skonfigurowanym. Ogólnie rzecz biorąc jest to zalecana konfiguracja. Użytkowników przy użyciu obciążeń usługi Office 365, takich jak Exchange Online i Skype dla firm, korzystają z pełną globalnej listy adresowej, dzięki czemu mogą wysyłać wiadomości e-mail i wywołać wszystkich użytkowników. W przypadku domyślnej konfiguracji zostałyby taki sam sposób, że współpracujemy z implementacją lokalnego programu Exchange lub Lync.

W niektórych przypadkach jednak masz wymagane pewnych zmian w konfiguracji domyślnej. Oto kilka przykładów:

* Zamierzasz używać [wielu Azure AD directory topologii](plan-connect-topologies.md#each-object-only-once-in-an-azure-ad-tenant). Następnie należy zastosować filtr, aby kontrolować obiekty, które są synchronizowane z określonego katalogu Azure AD.
* Uruchom projekt pilotażowy dla platformy Azure lub usługi Office 365 i chcesz tylko podzbioru użytkowników w usłudze Azure AD. W małych pilotażu nie jest ważne, aby mieć pełny adres listę globalną do zademonstrowania funkcji.
* Masz wiele kont usług i innych kont gromadzone, które mają w usłudze Azure AD.
* W celu zachowania zgodności nie usuwaj żadnych użytkowników kont lokalnych. Możesz tylko je wyłączyć. Jednak w usłudze Azure AD tylko aktywne konta być obecne.

W tym artykule opisano sposób konfigurowania różnych metod filtrowania.

> [!IMPORTANT]
> Firma Microsoft nie obsługuje modyfikowania ani działania synchronizacji programu Azure AD Connect poza akcjami, które zostały formalnie udokumentowane. Każda z tych akcji może spowodować niespójny lub nieobsługiwany stan synchronizacji programu Azure AD Connect. W związku z tym firma Microsoft nie może świadczyć pomocy technicznej w przypadku takich wdrożeń.

## <a name="basics-and-important-notes"></a>Podstawy i ważne uwagi
Synchronizacja programu Azure AD Connect można włączyć, filtrowanie, w dowolnym momencie. Jeśli rozpoczęcie domyślną konfigurację synchronizacji katalogów, a następnie skonfigurować filtrowanie, obiekty, które są odfiltrowywane już nie są synchronizowane z usługą Azure AD. Ze względu na tę zmianę wszystkie obiekty w usłudze Azure AD, które wcześniej zostały zsynchronizowane, ale następnie zostały przefiltrowane są usuwane z usługi Azure AD.

Przed rozpoczęciem wprowadzania zmian do filtrowania, upewnij się, że możesz [wyłączyć zaplanowane zadanie](#disable-the-scheduled-task) aby przypadkowo nie Eksportuj zmiany, które jeszcze nie zweryfikowano prawdopodobnie jest niepoprawny.

Ponieważ filtrowanie mogą usuwać wiele obiektów w tym samym czasie, chcesz się upewnić, że nowe filtry są poprawne, przed rozpoczęciem eksportowania wszelkie zmiany do usługi Azure AD. Po ukończeniu czynności konfiguracyjnych, zdecydowanie zalecamy, aby wykonać [kroki weryfikacji](#apply-and-verify-changes) przed eksportu i wprowadzić zmiany do usługi Azure AD.

W celu zabezpieczenia przed usunięcie wielu obiektów awarii, funkcja "[Zapobieganie przypadkowemu usuwaniu](how-to-connect-sync-feature-prevent-accidental-deletes.md)" jest domyślnie włączone. Jeśli usuniesz wiele obiektów, ze względu na filtrowanie (500 domyślnie), musisz wykonaj kroki opisane w tym artykule, aby umożliwić usuwa poświęcić na przejście do usługi Azure AD.

Jeśli używasz kompilacji przed listopada 2015 r. ([1.0.9125](reference-connect-version-history.md#1091250)), wprowadź zmianę konfiguracji filtru i użyj synchronizacji skrótów haseł, wówczas należy wyzwolenie pełnej synchronizacji wszystkie hasła, po zakończeniu konfiguracji. Aby uzyskać instrukcje dotyczące sposobu wyzwolić pełną synchronizację haseł, zobacz [wyzwolić pełną synchronizację haseł wszystkich](tshoot-connect-password-hash-synchronization.md#trigger-a-full-sync-of-all-passwords). Jeśli jesteś w kompilacji 1.0.9125 lub nowszym, następnie zwykłych **pełnej synchronizacji** akcji oblicza również, czy hasła powinny być synchronizowane i jeśli ten dodatkowy krok nie jest już wymagany.

Jeśli **użytkownika** obiekty zostały przypadkowo usunięte w usłudze Azure AD z powodu błędu filtrowania, można ponownie utworzyć obiekty użytkownika w usłudze Azure AD, usuwając konfiguracje filtrowania. Następnie można ponownie zsynchronizuj swoje katalogi. Ta akcja przywraca użytkownika z Kosza w usłudze Azure AD. Jednak nie można cofnąć usunięcia innych obiektów. Na przykład jeśli przypadkowo usuniesz grupę zabezpieczeń i został on użyty do listy ACL zasobu, grupy i jego list ACL nie można odzyskać.

Program Azure AD Connect usuwa tylko obiekty, które po ma uważa się w zakresie. Jeśli istnieją obiekty w usłudze Azure AD, które zostały utworzone przez inny aparat synchronizacji, a te obiekty nie są w zakresie, dodając filtrowanie nie powoduje usunięcia ich. Na przykład jeśli zaczniesz od serwera narzędzia DirSync, na którym utworzyć pełną kopię całego katalogu w usłudze Azure AD i zainstalować nowy serwer synchronizacji Azure AD Connect równolegle z filtrowaniem włączone od samego początku, program Azure AD Connect nie usuwa dodatkowe obiekty które są tworzone przez narzędzie DirSync.

Konfiguracja filtrowania zostaną zachowane w przypadku instalowania lub uaktualnienia do nowszej wersji programu Azure AD Connect. Zawsze jest najlepszym rozwiązaniem, aby sprawdzić, czy konfiguracja nie została przypadkowo zmienione po uaktualnieniu do nowszej wersji przed uruchomieniem pierwszego cyklu synchronizacji.

Jeśli masz więcej niż jednym lesie, należy najpierw zastosować konfiguracje filtrowania, które są opisane w tym temacie, aby w każdym lesie (przy założeniu, ma taką samą konfigurację dla wszystkich z nich).

### <a name="disable-the-scheduled-task"></a>Wyłącz zaplanowane zadania
Aby wyłączyć wbudowaną harmonogram, który wyzwala cykl synchronizacji co 30 minut, wykonaj następujące kroki:

1. Przejdź do programu PowerShell monitu.
2. Uruchom `Set-ADSyncScheduler -SyncCycleEnabled $False` można wyłączyć harmonogram.
3. Wprowadź zmiany, które są opisane w tym artykule.
4. Uruchom `Set-ADSyncScheduler -SyncCycleEnabled $True` umożliwienie harmonogram.

**Jeśli używasz kompilacji program Azure AD Connect przed 1.1.105.0**  
Aby wyłączyć zaplanowane zadanie, które wyzwala cykl synchronizacji co trzy godziny, wykonaj następujące kroki:

1. Rozpocznij **harmonogram zadań** z **Start** menu.
2. Bezpośrednio pod **Biblioteka Harmonogramu zadań**, znaleźć zadania o nazwie **usługi Azure AD Sync Scheduler**, kliknij prawym przyciskiem myszy, a następnie wybierz **wyłączyć**.  
   ![Task Scheduler](./media/how-to-connect-sync-configure-filtering/taskscheduler.png)  
3. Teraz możesz wprowadzić zmiany w konfiguracji i uruchomienia aparatu synchronizacji ręcznie z **Menedżera usługi synchronizacji** konsoli.

Po ukończeniu wszystkich zmian filtrowania, nie zapomnij możesz wrócić i **Włącz** zadanie ponownie.

## <a name="filtering-options"></a>Opcje filtrowania
Do narzędzia do synchronizacji katalogów, należy zastosować następujące typy konfiguracji filtrowania:

* [**Oparte na grupach**](#group-based-filtering): Filtrowanie na podstawie jednej grupy można skonfigurować tylko na początkowej instalacji za pomocą Kreatora instalacji.
* [**Oparte na domenie**](#domain-based-filtering): Za pomocą tej opcji, możesz wybrać, które domeny synchronizacji z usługą Azure AD. Można również dodawać i usuwać domeny z Konfiguracja aparatu synchronizacji, po wprowadzeniu zmian w infrastrukturze lokalnej po zainstalowaniu synchronizacji programu Azure AD Connect.
* [**Jednostka organizacyjna (OU) — na podstawie**](#organizational-unitbased-filtering): Za pomocą tej opcji, możesz też wybrać opcję jednostek organizacyjnych zsynchronizować z usługą Azure AD. Ta opcja dotyczy wszystkich typów obiektów w wybranych jednostek organizacyjnych.
* [**Oparte na atrybutach**](#attribute-based-filtering): Za pomocą tej opcji, możesz filtrować obiektów na podstawie wartości atrybutu dla obiektów. Może również mieć różnych filtrów dla różnych typów obiektów.

Wiele opcji filtrowania można użyć w tym samym czasie. Można na przykład użyć filtrowania na podstawie jednostki Organizacyjnej obejmujący tylko obiekty w jednej jednostce Organizacyjnej. W tym samym czasie można użyć filtrowania na podstawie atrybutu Aby odfiltrować obiekty dalej. W przypadku użycia wielu metod filtrowania, filtry użyć logiczne "i" między filtrami.

## <a name="domain-based-filtering"></a>Filtrowanie oparte na domenie
Ta sekcja zawiera proste kroki, aby skonfigurować filtr domeny. Jeśli dodane lub usunięte domen w lesie, po zainstalowaniu usługi Azure AD Connect, należy zaktualizować konfigurację filtrowania.

Preferowanym sposobem zmiany filtrowania opartego na domenie jest uruchomienie Kreatora instalacji, a następnie zmieniając [domeny filtrowanie i jednostek organizacyjnych](how-to-connect-install-custom.md#domain-and-ou-filtering). Kreator instalacji tworzy automatycznie wszystkie zadania, które są opisane w tym temacie.

Tylko powinni wykonać następujące czynności, jeśli nie można uruchomić Kreatora instalacji przyczyny.

Konfiguracja filtrowania opartego na domenie składa się z następujących czynności:

1. Wybierz domeny, które mają zostać uwzględnione w synchronizacji.
2. Dla każdej domeny dodanych i usuniętych dopasować profile uruchamiania.
3. [Zastosuj i Zweryfikuj zmiany](#apply-and-verify-changes).

### <a name="select-the-domains-to-be-synchronized"></a>Wybierz domeny, które mają być synchronizowane
Istnieją dwa sposoby, aby wybrać domeny, które mają być synchronizowane:
    - Za pomocą usługi synchronizacji
    - Za pomocą Kreatora programu Azure AD Connect.


#### <a name="select-the-domains-to-be-synchronized-using-the-synchronization-service"></a>Wybierz domeny, które mają być synchronizowane za pomocą usługi synchronizacji
Aby ustawić filtr domeny, wykonaj następujące czynności:

1. Zaloguj się do serwera, na którym działa synchronizacja programu Azure AD Connect przy użyciu konta będącego członkiem **ADSyncAdmins** grupy zabezpieczeń.
2. Rozpocznij **usługi synchronizacji** z **Start** menu.
3. Wybierz **łączników**, a następnie w **łączników** listy, wybierz łącznik usługi z typem **Active Directory Domain Services**. W **akcje**, wybierz opcję **właściwości**.  
   ![Właściwości łącznika](./media/how-to-connect-sync-configure-filtering/connectorproperties.png)  
4. Kliknij przycisk **Konfigurowanie partycji katalogu**.
5. W **wybierz partycje katalogu** listy, wybierz i usuń zaznaczenie domen, zgodnie z potrzebami. Sprawdź, że zaznaczone są tylko te partycje, które mają być synchronizowane.  
   ![Partycje](./media/how-to-connect-sync-configure-filtering/connectorpartitions.png)  
   Jeśli została zmieniona z infrastruktury lokalnej usługi Active Directory i dodane lub usunięte domen z lasu, kliknij przycisk **Odśwież** przycisk, aby uzyskać zaktualizowaną listę. Podczas odświeżania, pojawi się prośba o poświadczenia. Podaj wszystkie poświadczenia z dostępem do odczytu do usługi Active Directory systemu Windows Server. Nie musi być użytkownikiem, które są wstępnie wypełnione w oknie dialogowym.  
   ![Wymagane odświeżenie](./media/how-to-connect-sync-configure-filtering/refreshneeded.png)  
6. Gdy skończysz, Zamknij **właściwości** okno dialogowe, klikając **OK**. Usunięcie domen z lasu wyskakującego komunikat informuje, że usunięto domenę i konfiguracji zostaną wyczyszczone.
7. W dalszym ciągu dopasować profile uruchamiania.

#### <a name="select-the-domains-to-be-synchronized-using-the-azure-ad-connect-wizard"></a>Wybierz domeny, które mają być synchronizowane za pomocą Kreatora programu Azure AD Connect
Aby ustawić filtr domeny, wykonaj następujące czynności:

1.  Uruchom Kreatora programu Azure AD Connect
2.  Kliknij pozycję **Konfiguruj**.
3.  Wybierz **Dostosowywanie opcji synchronizacji** i kliknij przycisk **dalej**.
4.  Wprowadzanie poświadczeń usługi Azure AD
5.  Na **połączone katalogi** ekranu kliknij **dalej**.
6.  Na **domenę i jednostkę Organizacyjną filtrowania strony** kliknij **Odśwież**.  Pojawiają się nowe domeny ill i usunięto domen zniknie.
   ![Partycje](./media/how-to-connect-sync-configure-filtering/update2.png)  

### <a name="update-the-run-profiles"></a>Aktualizowanie profilów przebiegu
Jeśli zaktualizowano filtr domeny, należy zaktualizować profile uruchamiania.

1. W **łączników** liście, upewnij się, że zaznaczono łącznik, który został zmodyfikowany w poprzednim kroku. W **akcje**, wybierz opcję **Configure Run Profiles profilów**.  
   ![Uruchom profile 1 łącznika](./media/how-to-connect-sync-configure-filtering/connectorrunprofiles1.png)  
2. Znajdź i zidentyfikować następujące profile:
    * Pełny Import
    * Pełna synchronizacja
    * Import zmian
    * Synchronizacja przyrostowa
    * Eksportowanie
3. Dla każdego profilu dostosować **dodano** i **usunięte** domen.
    1. Dla każdej z pięciu profilami, wykonaj następujące czynności dla każdego **dodano** domeny:
        1. Wybierz profil, a następnie kliknij przycisk **nowy krok**.
        2. Na **krok konfigurowania** stronie **typu** menu rozwijanym, wybierz typ kroku z taką samą nazwę jak profil, którą konfigurujesz. Następnie kliknij przycisk **Next** (Dalej).  
        ![Uruchom profile 2 łącznika](./media/how-to-connect-sync-configure-filtering/runprofilesnewstep1.png)  
        3. Na **konfiguracji łącznika** stronie **partycji** menu rozwijanego wybierz nazwę domeny, do której zostały dodane do filtru domeny.  
        ![Uruchom profile 3 łącznika](./media/how-to-connect-sync-configure-filtering/runprofilesnewstep2.png)  
        4. Aby zamknąć **konfigurowania profilu uruchamiania** okno dialogowe, kliknij przycisk **Zakończ**.
    2. Dla każdej z pięciu profilami, wykonaj następujące czynności dla każdego **usunięte** domeny:
        1. Wybierz profil.
        2. Jeśli **wartość** z **partycji** atrybutu jest identyfikatorem GUID, wybierz krok wykonywania i kliknij przycisk **kroku Usuń**.  
        ![Uruchom profile 4 łącznika](./media/how-to-connect-sync-configure-filtering/runprofilesdeletestep.png)  
    3. Zweryfikować zmiany. Powinny zostać wyświetlone każdej domeny, która ma być synchronizowany etapem każdego profilu uruchamiania.
4. Aby zamknąć **Configure Run Profiles profilów** okno dialogowe, kliknij przycisk **OK**.
5.  Aby ukończyć konfigurację, musisz uruchomić **pełny import** i **synchronizacja różnicowa**. Kontynuuj czytanie sekcji [Zastosuj i Zweryfikuj zmiany](#apply-and-verify-changes).

## <a name="organizational-unitbased-filtering"></a>Filtrowanie na podstawie jednostki organizacyjnej
Preferowanym sposobem zmiany filtrowania na podstawie jednostki Organizacyjnej jest uruchomienie Kreatora instalacji, a następnie zmieniając [domeny filtrowanie i jednostek organizacyjnych](how-to-connect-install-custom.md#domain-and-ou-filtering). Kreator instalacji tworzy automatycznie wszystkie zadania, które są opisane w tym temacie.

Tylko powinni wykonać następujące czynności, jeśli nie można uruchomić Kreatora instalacji przyczyny.

Aby skonfigurować filtrowanie na podstawie jednostki organizacyjnej, wykonaj następujące czynności:

1. Zaloguj się do serwera, na którym działa synchronizacja programu Azure AD Connect przy użyciu konta będącego członkiem **ADSyncAdmins** grupy zabezpieczeń.
2. Rozpocznij **usługi synchronizacji** z **Start** menu.
3. Wybierz **łączników**, a następnie w **łączników** listy, wybierz łącznik usługi z typem **Active Directory Domain Services**. W **akcje**, wybierz opcję **właściwości**.  
   ![Właściwości łącznika](./media/how-to-connect-sync-configure-filtering/connectorproperties.png)  
4. Kliknij przycisk **Konfigurowanie partycji katalogu**, wybierz domenę, którą chcesz skonfigurować, a następnie kliknij przycisk **kontenery**.
5. Po wyświetleniu monitu podaj żadnych poświadczeń z dostępem do odczytu do usługi Active Directory w środowisku lokalnym. Nie musi być użytkownikiem, które są wstępnie wypełnione w oknie dialogowym.
6. W **Wybieranie kontenerów** okna dialogowego pole, jednostek organizacyjnych, które nie chcesz synchronizować z katalogiem w chmurze, a następnie kliknij przycisk Wyczyść **OK**.  
   ![Jednostki organizacyjne w oknie dialogowym Wybieranie kontenerów](./media/how-to-connect-sync-configure-filtering/ou.png)  
   * **Komputerów** kontenera należy wybrać dla komputerów z systemem Windows 10 do można pomyślnie zsynchronizować z usługą Azure AD. W przypadku komputerów przyłączonych do domeny znajdują się w innych jednostek organizacyjnych, upewnij się, że są wybrane.
   * Należy wybrać kontener **ForeignSecurityPrincipals** w przypadku używania wielu lasów z relacjami zaufania. Ten kontener umożliwia rozpoznanie członkostwa w grupie zabezpieczeń obejmującej wiele lasów.
   * **RegisteredDevices** jednostki Organizacyjnej należy wybrać włączenie funkcji zapisywania zwrotnego urządzeń. Jeśli używasz innej funkcji zapisywania zwrotnego, takie jak zapisywanie zwrotne grup, upewnij się, że te lokalizacje są zaznaczone.
   * Wybierz dowolnej innej jednostki Organizacyjnej, gdzie znajdują się użytkownicy, obiekty InetOrgPerson, grupy, kontakty i komputerów. Na ilustracji tych jednostek organizacyjnych znajdują się w jednostce Organizacyjnej ManagedObjects.
   * Jeśli używasz filtrowania opartego na grupach, jednostki Organizacyjnej, w którym znajduje się grupa musi być uwzględniony.
   * Należy pamiętać, że możesz określić, czy nowe jednostki organizacyjne, które są dodawane po zakończeniu konfiguracji filtrowania są synchronizowane, lub nie jest zsynchronizowany. Zobacz następną sekcję, aby uzyskać szczegółowe informacje.
7. Gdy skończysz, Zamknij **właściwości** okno dialogowe, klikając **OK**.
8. Aby ukończyć konfigurację, musisz uruchomić **pełny import** i **synchronizacja różnicowa**. Kontynuuj czytanie sekcji [Zastosuj i Zweryfikuj zmiany](#apply-and-verify-changes).

### <a name="synchronize-new-ous"></a>Synchronizuj nowe jednostki organizacyjne
Nowe jednostki organizacyjne, które są tworzone po skonfigurowaniu filtrowania są domyślnie synchronizowane. Ten stan jest wskazywany przez zaznaczenie pola wyboru. Możesz również usunąć zaznaczenie niektórych podrzędnych jednostek organizacyjnych. Aby uzyskać takie zachowanie, kliknij pole, aż przyjmie postać białym znacznikiem, niebieski (ze stanem domyślnym). Następnie usuń zaznaczenie wszelkich sub-jednostek organizacyjnych, które nie chcesz synchronizować.

Jeśli wszystkie podrzędne jednostki organizacyjne są zsynchronizowane, pole jest białym znacznikiem wyboru niebieski.  
![Jednostki Organizacyjnej z wszystkie wybrane pola](./media/how-to-connect-sync-configure-filtering/ousyncnewall.png)

Jeśli niektóre podrzędne jednostki organizacyjne niezaznaczone, pole jest szary z białym znacznikiem wyboru.  
![Jednostki Organizacyjnej z niektórych sub-jednostek organizacyjnych niezaznaczone](./media/how-to-connect-sync-configure-filtering/ousyncnew.png)

W przypadku tej konfiguracji nowej jednostki Organizacyjnej, który został utworzony w obszarze ManagedObjects są synchronizowane.

Kreator instalacji usługi Azure AD Connect tworzy zawsze tej konfiguracji.

### <a name="dont-synchronize-new-ous"></a>Nie Synchronizuj nowe jednostki organizacyjne
Można skonfigurować aparat synchronizacji, aby nie synchronizować nowe jednostki organizacyjne, po zakończeniu konfiguracji filtrowania. Ten stan jest wskazywany w Interfejsie użytkownika przez pole, które pojawiają się kolor szary bez znacznika wyboru. Aby uzyskać takie zachowanie, kliknij pole, aż przyjmie postać białe bez znacznika wyboru. Następnie wybierz sub-jednostek organizacyjnych, które mają być synchronizowane.

![Jednostki Organizacyjnej z certyfikatem głównym niezaznaczone](./media/how-to-connect-sync-configure-filtering/oudonotsyncnew.png)

W przypadku tej konfiguracji nowej jednostki Organizacyjnej, który został utworzony w obszarze ManagedObjects nie jest zsynchronizowany.

## <a name="attribute-based-filtering"></a>Filtrowanie na podstawie atrybutu
Upewnij się, że listopada 2015 roku ([1.0.9125](reference-connect-version-history.md#1091250)) lub nowszej kompilacji dla tych kroków do pracy.

> [!IMPORTANT]
>Firma Microsoft zaleca, aby nie modyfikować domyślnych reguł utworzonych przez **program Azure AD Connect**. Jeśli chcesz zmodyfikować regułę, sklonować ten projekt i Wyłącz istniejącą regułę. Wprowadź zmiany w sklonowanym reguły. Należy pamiętać, że w ten sposób (wyłączanie oryginalnej reguły) będą pominięcia wszelkie poprawki lub funkcje włączone za pomocą tej reguły.

Filtrowanie na podstawie atrybutu jest najbardziej elastyczny sposób Filtruj obiekty. Można użyć możliwości [aprowizacja deklaratywna](concept-azure-ad-connect-sync-declarative-provisioning.md) do kontrolowania niemal każdy aspekt gdy obiekt jest zsynchronizowany z usługą Azure AD.

Można zastosować [dla ruchu przychodzącego](#inbound-filtering) filtrowania z usługi Active Directory do środowiska metaverse, i [wychodzącego](#outbound-filtering) filtrowania w magazynie metaverse do usługi Azure AD. Firma Microsoft zaleca stosowanie filtrowanie ruchu przychodzącego, ponieważ jest to najłatwiejsza do obsługi. Należy używać tylko z filtrowania ruchu wychodzącego, jeśli jest to wymagane do dołączenia do obiektów z więcej niż jednym lesie, zanim oceny może odbywać się.

### <a name="inbound-filtering"></a>Liczba przychodzących filtrowania
Filtrowanie ruchu przychodzącego używa konfiguracji domyślnej, gdzie obiekty, przechodząc do usługi Azure AD musi mieć cloudFiltered atrybut metaverse nie ustawiono wartości mają być synchronizowane. Jeśli wartość tego atrybutu jest równa **True**, a następnie obiektu nie jest zsynchronizowany. Nie należy ustawiać **False**, zgodnie z projektem. Aby upewnić się, inne reguły mają możliwość współtworzenia wartością, ten atrybut tylko powinien mieć wartości **True** lub **NULL** (lub nieobecny).

Filtrowania ruchu przychodzącego, użyj możliwości **zakres** ustalenie, które obiekty, aby zsynchronizować lub nie. Jest to, gdzie możesz wprowadzać zmiany wymagań własnej organizacji. Moduł zakres ma **grupy** i **klauzuli** ustalenie, kiedy reguła synchronizacji znajduje się w zakresie. Grupa zawiera jedną lub wiele klauzul. Ma logiczny "i" między wiele klauzul i logiczne "lub" między wiele grup.

Przyjrzyjmy się przykładowi:  
![Zakres](./media/how-to-connect-sync-configure-filtering/scope.png)  
Powinny to być odczytywane jako **(dział = IT) lub (dział = sprzedaży i c = US)**.

W następujących przykładów i czynności obiekt użytkownika jest używany jako przykładu, ale możesz użyć tego dla wszystkich typów obiektów.

W następujących przykładach wartość pierwszeństwa rozpoczyna się od 50. Może być dowolną liczbę, nie jest używany, ale powinna być niższa niż 100.

#### <a name="negative-filtering-do-not-sync-these"></a>Filtrowanie ujemny: "nie są synchronizowane są"
W poniższym przykładzie, możesz odfiltrować (Synchronizuj) wszystkich użytkowników gdzie **extensionAttribute15** ma wartość **NoSync**.

1. Zaloguj się do serwera, na którym działa synchronizacja programu Azure AD Connect przy użyciu konta będącego członkiem **ADSyncAdmins** grupy zabezpieczeń.
2. Rozpocznij **Synchronization Rules Editor** z **Start** menu.
3. Upewnij się, że **przychodzący** jest zaznaczone, a następnie kliknij przycisk **Dodawanie nowej reguły**.
4. Nadaj regule nazwę opisową, taką jak "*w z usługi AD — DoNotSyncFilter użytkownika*". Wybierz prawidłowe lasu, wybierz pozycję **użytkownika** jako **typu obiektu CS**i wybierz **osoby** jako **typu obiektu MV**. W **typu łącza**, wybierz opcję **Dołącz**. W **pierwszeństwo**wpisz wartość, która nie jest obecnie używany przez inną regułę synchronizacji (na przykład 50), a następnie kliknij przycisk **dalej**.  
   ![Opis elementu 1 dla ruchu przychodzącego](./media/how-to-connect-sync-configure-filtering/inbound1.png)  
5. W **filtru Scoping**, kliknij przycisk **Dodaj grupę**i kliknij przycisk **Dodaj klauzulę**. W **atrybut**, wybierz opcję **ExtensionAttribute15**. Upewnij się, że **Operator** jest ustawiona na **RÓWNY**, a następnie wpisz wartość **NoSync** w **wartość** pole. Kliknij przycisk **Dalej**.  
   ![Liczba przychodzących zakresu 2](./media/how-to-connect-sync-configure-filtering/inbound2.png)  
6. Pozostaw **Dołącz** reguł pusty, a następnie kliknij przycisk **dalej**.
7. Kliknij przycisk **Dodaj przekształcenie**, wybierz opcję **dla przepływu** jako **stałej**i wybierz **cloudFiltered** jako **docelowej Atrybut**. W **źródła** polu tekstowym **True**. Kliknij przycisk **Dodaj** można zapisać reguły.  
   ![Liczba przychodzących przekształcania 3](./media/how-to-connect-sync-configure-filtering/inbound3.png)
8. Aby ukończyć konfigurację, musisz uruchomić **Full sync**. Kontynuuj czytanie sekcji [Zastosuj i Zweryfikuj zmiany](#apply-and-verify-changes).

#### <a name="positive-filtering-only-sync-these"></a>Filtrowanie dodatnią: "zsynchronizować tylko te"
Może przedstawiać dodatnie filtrowanie może być trudniejsza, ponieważ należy również wziąć pod uwagę obiekty, które nie są oczywiste do synchronizacji, takie jak sale konferencyjne. Ma również zastąpić domyślny filtr w regule out-of-box **w z usługi AD — użytkownik przyłączyć**. Podczas tworzenia niestandardowego filtru, upewnij się, że nie zawierają obiekty systemowe krytycznych, obiekty konflikt replikacji, specjalne skrzynek pocztowych i konta usług programu Azure AD Connect.

Dodatnia opcji filtrowania wymaga dwie reguły synchronizacji. Wymagany jest jedna reguła (lub kilka) przy użyciu poprawny zakres obiekty do zsynchronizowania. Należy również drugą regułę przechwytującą cały synchronizacji, który odfiltrowuje wszystkie obiekty, które jeszcze nie zostały zidentyfikowane jako obiekt, który powinien zostać zsynchronizowany.

W poniższym przykładzie sama synchronizacja obiektów użytkowników, których atrybut działu ma wartość **sprzedaży**.

1. Zaloguj się do serwera, na którym działa synchronizacja programu Azure AD Connect przy użyciu konta będącego członkiem **ADSyncAdmins** grupy zabezpieczeń.
2. Rozpocznij **Synchronization Rules Editor** z **Start** menu.
3. Upewnij się, że **przychodzący** jest zaznaczone, a następnie kliknij przycisk **Dodawanie nowej reguły**.
4. Nadaj regule nazwę opisową, taką jak "*w z usługi AD — sprzedaż użytkownika synchronizacji*". Wybierz prawidłowe lasu, wybierz pozycję **użytkownika** jako **typu obiektu CS**i wybierz **osoby** jako **typu obiektu MV**. W **typu łącza**, wybierz opcję **Dołącz**. W **pierwszeństwo**wpisz wartość, która nie jest obecnie używany przez inną regułę synchronizacji (na przykład 51), a następnie kliknij przycisk **dalej**.  
   ![Liczba przychodzących opis 4](./media/how-to-connect-sync-configure-filtering/inbound4.png)  
5. W **filtru Scoping**, kliknij przycisk **Dodaj grupę**i kliknij przycisk **Dodaj klauzulę**. W **atrybut**, wybierz opcję **działu**. Upewnij się, że Operator jest ustawiona na **RÓWNY**, a następnie wpisz wartość **sprzedaży** w **wartość** pole. Kliknij przycisk **Dalej**.  
   ![Liczba przychodzących zakres 5](./media/how-to-connect-sync-configure-filtering/inbound5.png)  
6. Pozostaw **Dołącz** reguł pusty, a następnie kliknij przycisk **dalej**.
7. Kliknij przycisk **Dodaj przekształcenie**, wybierz opcję **stałej** jako **dla przepływu**i wybierz **cloudFiltered** jako **docelowej Atrybut**. W **źródła** wpisz **False**. Kliknij przycisk **Dodaj** można zapisać reguły.  
   ![Liczba przychodzących przekształcania 6](./media/how-to-connect-sync-configure-filtering/inbound6.png)  
   Jest to szczególny przypadek, gdzie możesz jawnie ustawione cloudFiltered **False**.
8. Teraz musimy utworzyć regułę przechwytującą cały synchronizacji. Nadaj regule nazwę opisową, taką jak "*w z usługi AD — filtr użytkownika wychwytywania*". Wybierz prawidłowe lasu, wybierz pozycję **użytkownika** jako **typu obiektu CS**i wybierz **osoby** jako **typu obiektu MV**. W **typu łącza**, wybierz opcję **Dołącz**. W **pierwszeństwo**, wpisz wartość, która nie jest obecnie używany przez inną regułę synchronizacji (na przykład 99). Wybrana wartość pierwszeństwa, która jest wyższa (niższy priorytet) niż poprzednie reguły synchronizacji. Jednak również Opuszczono trochę miejsca, aby dodać więcej reguł synchronizacji filtrowania później rozpocząć synchronizowanie dodatkowe działów. Kliknij przycisk **Dalej**.  
   ![Liczba przychodzących opis 7](./media/how-to-connect-sync-configure-filtering/inbound7.png)  
9. Pozostaw **filtru Scoping** puste, a następnie kliknij przycisk **dalej**. Pusty filtr wskazuje, że reguła jest mają być stosowane do wszystkich obiektów.
10. Pozostaw **Dołącz** reguł pusty, a następnie kliknij przycisk **dalej**.
11. Kliknij przycisk **Dodaj przekształcenie**, wybierz opcję **stałej** jako **dla przepływu**i wybierz **cloudFiltered** jako **docelowej Atrybut**. W **źródła** wpisz **True**. Kliknij przycisk **Dodaj** można zapisać reguły.  
    ![Liczba przychodzących przekształcania 3](./media/how-to-connect-sync-configure-filtering/inbound3.png)  
12. Aby ukończyć konfigurację, musisz uruchomić **Full sync**. Kontynuuj czytanie sekcji [Zastosuj i Zweryfikuj zmiany](#apply-and-verify-changes).

Jeśli zachodzi potrzeba, można utworzyć więcej reguł pierwszy typ dodanie więcej obiektów w synchronizacji.

### <a name="outbound-filtering"></a>Filtrowanie ruchu wychodzącego
W niektórych przypadkach zachodzi konieczność przeprowadzenia filtrowania tylko wtedy, gdy dołączają do obiektów w magazynie metaverse. Na przykład może być konieczne Przyjrzyj się atrybut poczty z lasu zasobów i atrybut userPrincipalName z lasu kont, aby określić, jeśli obiekt mają być synchronizowane. W takich przypadkach możesz utworzyć filtrowania reguły ruchu wychodzącego.

W tym przykładzie zmienisz, filtrowania, aby tylko użytkowników, które mają ich wiadomości e-mail i userPrincipalName kończy się rozszerzeniem @contoso.com są synchronizowane:

1. Zaloguj się do serwera, na którym działa synchronizacja programu Azure AD Connect przy użyciu konta będącego członkiem **ADSyncAdmins** grupy zabezpieczeń.
2. Rozpocznij **Synchronization Rules Editor** z **Start** menu.
3. W obszarze **typ reguł**, kliknij przycisk **ruchu wychodzącego**.
4. W zależności od wersji programu Connect, możesz użyć albo znaleźć reguły o nazwie **Out do usługi AAD — użytkownik przyłączyć** lub **się do usługi AAD — użytkownik przyłączyć SOAInAD**i kliknij przycisk **Edytuj**.
5. W oknie podręcznym odpowiedzi **tak** do utworzenia kopii reguły.
6. Na **opis** strony, zmień **pierwszeństwo** nieużywanych wartości, takich jak 50.
7. Kliknij przycisk **filtru Scoping** na nawigacji po lewej stronie, a następnie kliknij przycisk **Dodaj klauzulę**. W **atrybut**, wybierz opcję **poczty**. W **Operator**, wybierz opcję **ENDSWITH**. W **wartość**, typ  **\@contoso.com**, a następnie kliknij przycisk **Dodaj klauzulę**. W **atrybut**, wybierz opcję **userPrincipalName**. W **Operator**, wybierz opcję **ENDSWITH**. W **wartość**, typ  **\@contoso.com**.
8. Kliknij pozycję **Zapisz**.
9. Aby ukończyć konfigurację, musisz uruchomić **Full sync**. Kontynuuj czytanie sekcji [Zastosuj i Zweryfikuj zmiany](#apply-and-verify-changes).

## <a name="apply-and-verify-changes"></a>Zastosuj i Zweryfikuj zmiany
Po wprowadzeniu zmian w konfiguracji, należy je zastosować do obiektów, które znajdują się już w systemie. Może być również mają być przetwarzane obiekty, które nie są obecnie w aparacie synchronizacji (i aparat synchronizacji musi odczytać w systemie źródłowym ponownie w celu zweryfikowania jego zawartość).

Jeśli zmienisz konfigurację przy użyciu **domeny** lub **jednostki organizacyjnej** filtrowania, a następnie wystarczy **pełny import**, a następnie **Delta Synchronizacja**.

Jeśli zmienisz konfigurację przy użyciu **atrybut** filtrowania, a następnie wystarczy **pełnej synchronizacji**.

Wykonaj następujące czynności:

1. Rozpocznij **usługi synchronizacji** z **Start** menu.
2. Wybierz **łączników**. W **łączników** listy, wybierz łącznik, w którym wprowadzone wcześniej zmiany konfiguracji. W **akcje**, wybierz opcję **Uruchom**.  
   ![Uruchom łącznika](./media/how-to-connect-sync-configure-filtering/connectorrun.png)  
3. W **profile uruchamiania**, wybrać operację, która została wymieniona w poprzedniej sekcji. Jeśli musisz uruchomić dwie akcje, należy uruchomić sekundy po zakończeniu pierwszej. ( **Stanu** kolumna jest **bezczynne** wybranego łącznika.)

Po synchronizacji wszystkie zmiany są przygotowane do wyeksportowania. Przed faktycznie wprowadzisz zmiany w usłudze Azure AD, należy sprawdzić, czy te zmiany są poprawne.

1. Uruchom wiersz polecenia i przejdź do `%Program Files%\Microsoft Azure AD Sync\bin`.
2. Uruchom polecenie `csexport "Name of Connector" %temp%\export.xml /f:x`.  
   Nazwa łącznika jest usługa synchronizacji. Ma on nazwę, podobnie jak "contoso.com — usługi AAD" dla usługi Azure AD.
3. Uruchom polecenie `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`.
4. Masz teraz plik w % temp % o nazwie export.csv, które można zbadać w programie Microsoft Excel. Ten plik zawiera wszystkie zmiany, które mają zostać wyeksportowane.
5. Wprowadź niezbędne zmiany dane lub konfiguracja, a następnie uruchom następujące kroki, ponownie (importowania, synchronizacji i sprawdź, czy) do momentu zmiany, które mają zostać wyeksportowane czego oczekiwać.

Jeśli uzyskano satysfakcjonujący eksportować zmian z usługą Azure AD.

1. Wybierz **łączników**. W **łączników** wybierz łącznik usługi Azure AD. W **akcje**, wybierz opcję **Uruchom**.
2. W **profile uruchamiania**, wybierz opcję **wyeksportować**.
3. Jeśli zmiany konfiguracji usuwają wiele obiektów, następnie zostanie wyświetlony błąd podczas eksportowania Jeśli liczba jest większa niż skonfigurowany próg (domyślnie 500). Jeśli zostanie wyświetlony ten błąd, a następnie trzeba tymczasowo wyłączyć "[Zapobieganie przypadkowemu usuwaniu](how-to-connect-sync-feature-prevent-accidental-deletes.md)" funkcji.

Teraz nadszedł czas, aby ponownie włączyć harmonogram.

1. Rozpocznij **harmonogram zadań** z **Start** menu.
2. Bezpośrednio pod **Biblioteka Harmonogramu zadań**, znaleźć zadania o nazwie **usługi Azure AD Sync Scheduler**, kliknij prawym przyciskiem myszy, a następnie wybierz **Włącz**.

## <a name="group-based-filtering"></a>Filtrowanie oparte na grupach
Można skonfigurować filtrowanie oparte na grupy po raz pierwszy zainstalować program Azure AD Connect przy użyciu [instalacji niestandardowej](how-to-connect-install-custom.md#sync-filtering-based-on-groups). Jest on przeznaczony dla wdrożenia pilotażowego, w którym ma niewielki zestaw obiektów, które mają być synchronizowane. Po wyłączeniu filtrowania na podstawie grupy, nie można włączyć ponownie. Ma ona *nieobsługiwane* używać filtrowania na podstawie grupy w konfiguracji niestandardowej. Jest obsługiwana tylko do tej funkcji można skonfigurować za pomocą Kreatora instalacji. Po zakończeniu wdrożenia pilotażowego, użyj jednej z innych opcji filtrowania w tym temacie. Korzystając z filtrowania opartego na jednostce Organizacyjnej w połączeniu z filtrowaniem opartym na grupach, jednostką organizacyjną, w którym znajdują się grupę i jej elementów członkowskich musi być uwzględniony.

Podczas synchronizowania wielu lasów usługi AD, możesz skonfigurować filtrowanie na podstawie grupy, określając inną grupę dla każdego łącznika usługi AD. Jeśli chcesz, aby zsynchronizować użytkownika w jednym AD lasu i tego samego użytkownika zawiera co najmniej odpowiadające więcej obiektów w innych lasach AD należy upewnić się, że obiekt użytkownika i wszystkie odpowiednie obiekty są w terminie opartym na grupach filtrowanie zakresu. Przykłady:

* Należy mieć użytkownika w jednym lesie, który ma odpowiedni obiekt FSP (obcego podmiotu zabezpieczeń) w innym lesie. Oba obiekty muszą być w terminie opartym na grupach filtrowanie zakresu. W przeciwnym razie użytkownik nie będą synchronizowane z usługą Azure AD.

* Należy mieć użytkownika w jednym lesie, który ma odpowiednie konto zasobu (np. połączona Skrzynka pocztowa) w innym lesie. Co więcej skonfigurowaniu usługi Azure AD Connect, aby połączyć użytkownika z konta zasobu. Oba obiekty muszą być w terminie opartym na grupach filtrowanie zakresu. W przeciwnym razie użytkownik nie będą synchronizowane z usługą Azure AD.

* Masz użytkownika w jednym lesie, który ma odpowiedni wiadomość e-mail kontaktu w innym lesie. Ponadto skonfigurowano usługi Azure AD Connect, aby połączyć użytkownika z kontaktu wiadomości e-mail. Oba obiekty muszą być w terminie opartym na grupach filtrowanie zakresu. W przeciwnym razie użytkownik nie będą synchronizowane z usługą Azure AD.


## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [synchronizacji programu Azure AD Connect](how-to-connect-sync-whatis.md) konfiguracji.
- Dowiedz się więcej o [integrowanie tożsamości lokalnych z usługą Azure AD](whatis-hybrid-identity.md).
