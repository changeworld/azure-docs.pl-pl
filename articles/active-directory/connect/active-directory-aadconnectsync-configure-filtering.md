---
title: 'Synchronizacja programu Azure AD Connect: Konfigurowanie filtrowania | Dokumentacja firmy Microsoft'
description: Wyjaśniono, jak skonfigurować filtrowanie synchronizacji Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 880facf6-1192-40e9-8181-544c0759d506
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 0b4b306d1224b5521774b05a110c862b58450eb3
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
# <a name="azure-ad-connect-sync-configure-filtering"></a>Synchronizacja programu Azure AD Connect: konfigurowanie filtrowania
Za pomocą filtrowania, można kontrolować obiekty, które są wyświetlane w usłudze Azure Active Directory (Azure AD) z katalogu lokalnego. Domyślna konfiguracja pobiera wszystkie obiekty we wszystkich domenach w lesie skonfigurowanym. Ogólnie rzecz biorąc jest to zalecana konfiguracja. Użytkowników przy użyciu usługi Office 365 obciążeń, takich jak Exchange Online i Skype dla firm, korzystać z pełną globalnej listy adresowej, wysyłać wiadomości e-mail i wywołać Wszyscy. W konfiguracji domyślnej zostałyby to samo środowisko korzystania, które zostałyby implementacja lokalnego programu Exchange lub Lync.

W niektórych przypadkach jednak masz wymagane wprowadzić kilka zmian w konfiguracji domyślnej. Oto kilka przykładów:

* Zamierzasz używać [wielu Azure AD directory topologii](active-directory-aadconnect-topologies.md#each-object-only-once-in-an-azure-ad-tenant). Następnie należy zastosować filtr, aby kontrolować obiekty, które są synchronizowane z określonego katalogu usługi Azure AD.
* Uruchom projekt pilotażowy dla platformy Azure lub usługi Office 365 i mają tylko dla podzbioru użytkowników w usłudze Azure AD. W małych pilotażu nie jest istotne pełną globalna lista adresów do pokazu funkcjonalności.
* Masz wiele kont usług i innych gromadzone kont, które nie mają w usłudze Azure AD.
* Ze względu na zgodność nie usuwaj żadnych użytkownika konta lokalnego. Możesz tylko je wyłączyć. Jednak w usłudze Azure AD mają tylko aktywne konta obecności.

W tym artykule opisano sposób konfigurowania różnych metod filtrowania.

> [!IMPORTANT]
> Microsoft nie obsługuje modyfikowania lub działania synchronizacji Azure AD Connect poza akcje, które są udokumentowane formalnie. Te czynności może spowodować niespójne lub nieobsługiwany stan synchronizacji Azure AD Connect. W związku z tym Microsoft nie świadczy pomocy technicznej dla tych wdrożeń.

## <a name="basics-and-important-notes"></a>Podstawy i ważne uwagi
Synchronizacja programu Azure AD Connect można włączyć, filtrowanie, w dowolnym momencie. Jeśli rozpoczynać się od domyślnej konfiguracji synchronizacji katalogów, a następnie skonfigurować filtrowanie, obiekty, które są odfiltrowywane nie są synchronizowane z usługą Azure AD. Z powodu tej zmiany wszystkie obiekty w usłudze Azure AD, które wcześniej zostały zsynchronizowane, ale następnie zostały przefiltrowane są usuwane z usługi Azure AD.

Przed rozpoczęciem wprowadzania zmian do filtrowania, upewnij się, że możesz [wyłączyć zaplanowane zadanie](#disable-scheduled-task) tak przypadkowo nie Eksportuj zmiany, które jeszcze nie zweryfikowano są prawidłowe.

Ponieważ filtrowanie można usunąć wiele obiektów w tym samym czasie, chcesz upewnij się, że nowe filtry są poprawne, przed rozpoczęciem eksportowania wszystkie zmiany do usługi Azure AD. Po ukończeniu czynności konfiguracyjnych, zdecydowanie zaleca się przestrzeganie [kroki weryfikacji](#apply-and-verify-changes) przed wyeksportować i wprowadzać zmiany do usługi Azure AD.

W celu zabezpieczenia przed usuwanie wielu obiektów przypadkowo, funkcję "[Zapobieganie przypadkowemu usuwaniu](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)" jest domyślnie włączone. Jeśli usuniesz wiele obiektów z powodu filtrowania (500 domyślnie), musisz wykonaj kroki opisane w tym artykule, aby umożliwić usuwa podąża do usługi Azure AD.

Jeśli używasz kompilacji zanim listopad 2015 ([1.0.9125](active-directory-aadconnect-version-history.md#1091250)), wprowadzić zmiany w konfiguracji filtru i użyj synchronizacji skrótu hasła, należy wywołać pełnej synchronizacji haseł wszystkich po zakończeniu konfiguracji. Kroki do wyzwolenia pełnej synchronizacji haseł, zobacz [wyzwolenia pełnej synchronizacji haseł wszystkich](active-directory-aadconnectsync-troubleshoot-password-hash-synchronization.md#trigger-a-full-sync-of-all-passwords). Jeśli podczas kompilacji 1.0.9125 lub nowszym, następnie regular **pełnej synchronizacji** akcji oblicza również, czy hasła powinny być synchronizowane, a jeśli ten dodatkowy krok nie jest już wymagane.

Jeśli **użytkownika** obiekty przypadkowo zostały usunięte w usłudze Azure AD z powodu błędu filtrowania, można ponownie utworzyć obiekty użytkownika w usłudze Azure AD przez usunięcie konfiguracji filtrowania. Następnie można ponownie zsynchronizuj swoje katalogi. Ta akcja przywraca użytkowników z Kosza w usłudze Azure AD. Jednak można cofnąć usunięcia innych obiektów. Na przykład jeśli przypadkowo usunąć grupę zabezpieczeń i została użyta do listy ACL zasobu, grupy i jego listy kontroli dostępu nie można odzyskać.

Azure AD Connect usuwa tylko obiekty, które raz uwzględniono znajdował się w zakresie. Jeśli te obiekty nie znajdują się w zakresie ma obiektów w usłudze Azure AD, które zostały utworzone przez inny aparat synchronizacji, dodawanie filtrowanie nie powoduje usunięcia ich. Na przykład jeśli rozpoczyna serwera narzędzia DirSync, na którym utworzyć pełną kopię całej katalogu w usłudze Azure AD i zainstalować nowy serwer synchronizacji Azure AD Connect równolegle z filtrowaniem włączonym od początku, Azure AD Connect nie powoduje usunięcia dodatkowe obiekty, które zostały utworzone przy użyciu narzędzia DirSync.

Konfiguracja filtrowania jest zachowywane po instalacji lub uaktualnienia do nowszej wersji programu Azure AD Connect. Zawsze jest najlepszym rozwiązaniem, aby sprawdzić, czy nie został przypadkowo zmieniła się konfiguracja po uaktualnieniu do nowszej wersji przed uruchomieniem pierwszego cyklu synchronizacji.

Jeśli masz więcej niż jednym lesie, należy najpierw zastosować konfiguracje filtrowania, które zostały opisane w tym temacie, aby w każdym lesie (przy założeniu, że ma taką samą konfigurację dla wszystkich z nich).

### <a name="disable-the-scheduled-task"></a>Wyłącz zaplanowane zadania
Aby wyłączyć wbudowane harmonogramu, który wyzwala cykl synchronizacji co 30 minut, wykonaj następujące kroki:

1. Przejdź do programu PowerShell monitu.
2. Uruchom `Set-ADSyncScheduler -SyncCycleEnabled $False` Aby wyłączyć harmonogram.
3. Wprowadź zmiany, które są udokumentowane w tym artykule.
4. Uruchom `Set-ADSyncScheduler -SyncCycleEnabled $True` ponownie włączyć harmonogramu.

**Jeśli używasz usługi Azure AD Connect kompilacji przed 1.1.105.0**  
Aby wyłączyć zaplanowane zadanie, które wyzwala cykl synchronizacji co trzy godziny, wykonaj następujące kroki:

1. Uruchom **harmonogram zadań** z **Start** menu.
2. Bezpośrednio pod **Biblioteka Harmonogramu zadań**, znaleźć zadania o nazwie **Azure AD Sync Scheduler**, kliknij prawym przyciskiem myszy, a następnie wybierz **wyłączyć**.  
   ![Harmonogram zadań](./media/active-directory-aadconnectsync-configure-filtering/taskscheduler.png)  
3. Teraz można wprowadzić zmiany w konfiguracji i uruchomić aparatu synchronizacji ręcznie z **Menedżera usługi synchronizacji** konsoli.

Po zakończeniu wszystkich zmian filtrowania, nie zapomnij wrócić i **włączyć** zadanie ponownie.

## <a name="filtering-options"></a>Opcje filtrowania
Do narzędzia synchronizacji katalogów, należy zastosować następujące typy konfiguracji filtrowania:

* [**Na podstawie grupy**](#group-based-filtering): filtrowanie oparte na pojedynczej grupy można skonfigurować tylko na początkowej instalacji przy użyciu Kreatora instalacji.
* [**Oparte na domenie**](#domain-based-filtering): przy użyciu tej opcji, możesz wybrać domen zsynchronizować z usługą Azure AD. Można również dodawać i usuwać domeny z Konfiguracja aparatu synchronizacji, po wprowadzeniu zmian w infrastrukturze lokalnej po zainstalowaniu synchronizacja programu Azure AD Connect.
* [**Jednostka organizacyjna (OU) — na podstawie**](#organizational-unitbased-filtering): przy użyciu tej opcji, można wybrać, które jednostek organizacyjnych zsynchronizować z usługą Azure AD. Ta opcja jest dla wszystkich typów obiektów w wybranych jednostek organizacyjnych.
* [**Na podstawie atrybutów**](#attribute-based-filtering): przy użyciu tej opcji, możesz filtrować obiektów na podstawie wartości atrybutu obiektów. Można również mieć różnych filtrów do różnych typów obiektów.

Wiele opcji filtrowania można użyć w tym samym czasie. Można na przykład użyć filtrowanie na podstawie jednostki Organizacyjnej obejmujący tylko obiekty w jednej jednostce Organizacyjnej. W tym samym czasie możesz użyć filtrowania na podstawie atrybutów do filtrowania dodatkowe obiekty. W przypadku użycia wielu metod filtrowania, filtry użyć logiczne "i" między filtrów.

## <a name="domain-based-filtering"></a>Filtrowanie oparte na domenie
Ta sekcja zawiera czynności, aby skonfigurować filtr domeny. Jeśli dodane lub usunięte domen w lesie, po zainstalowaniu usługi Azure AD Connect, należy zaktualizować konfiguracji filtrowania.

Preferowany sposób, aby zmienić filtrowanie oparte na domenie jest uruchomienie Kreatora instalacji i zmieniając [domeny i jednostki Organizacyjnej filtrowania](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering). Kreator instalacji tworzy automatycznie wszystkie zadania, które są opisane w tym temacie.

Kroki te należy wykonać tylko, jeśli nie można uruchomić Kreatora instalacji z jakiegoś powodu.

Oparte na domenie filtrowania Konfiguracja obejmuje następujące kroki:

1. [Wybierz domeny](#select-domains-to-be-synchronized) , który ma być uwzględniony w synchronizacji.
2. Każdy dodawaniem i usuwaniem domeny, Dostosuj [profilów uruchamiania](#update-run-profiles).
3. [Zastosuj i Sprawdź zmiany](#apply-and-verify-changes).

### <a name="select-the-domains-to-be-synchronized"></a>Wybierz domenę do synchronizacji
Aby ustawić filtr domeny, wykonaj następujące czynności:

1. Zaloguj się do serwera, który działa synchronizacja programu Azure AD Connect przy użyciu konta, które jest członkiem **ADSyncAdmins** grupy zabezpieczeń.
2. Uruchom **usługi synchronizacji** z **Start** menu.
3. Wybierz **łączników**, a następnie w **łączników** wybierz łącznik z typem **usług domenowych w usłudze Active Directory**. W **akcje**, wybierz pozycję **właściwości**.  
   ![Właściwości łącznika](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)  
4. Kliknij przycisk **Konfigurowanie partycji katalogu**.
5. W **Wybieranie partycji katalogu** listy, wybierz i usuń zaznaczenie domeny, zgodnie z potrzebami. Sprawdź, że wybrane są tylko partycje, które mają być synchronizowane.  
   ![Partycje](./media/active-directory-aadconnectsync-configure-filtering/connectorpartitions.png)  
   Jeśli została zmieniona infrastruktury lokalnej usługi Active Directory i dodać lub usunąć domen z lasu, należy kliknąć **Odśwież** przycisk, aby wyświetlić zaktualizowaną listę. Podczas odświeżania, są wyświetlane pytanie o poświadczenia. Podaj wszystkie poświadczenia z dostępem do odczytu do usługi Active Directory systemu Windows Server. Nie musi być użytkownikiem, który jest wypełniana wstępnie w oknie dialogowym.  
   ![Wymagane odświeżenie](./media/active-directory-aadconnectsync-configure-filtering/refreshneeded.png)  
6. Gdy wszystko będzie gotowe, Zamknij **właściwości** okno dialogowe, klikając **OK**. Jeśli usunięto domen z lasu, wyskakujących komunikat informuje, że domeny został usunięty i że konfiguracji zostaną wyczyszczone.
7. Kontynuuj dopasować [profilów uruchamiania](#update-run-profiles).

### <a name="update-the-run-profiles"></a>Aktualizowanie profilów uruchamiania
Jeśli użytkownik zaktualizował filtru domeny, należy zaktualizować profile uruchamiania.

1. W **łączniki** listy, upewnij się, że zaznaczono łącznik, który został zmodyfikowany w poprzednim kroku. W **akcje**, wybierz pozycję **Konfigurowanie profilów uruchamiania**.  
   ![Łącznik 1 profilów uruchamiania](./media/active-directory-aadconnectsync-configure-filtering/connectorrunprofiles1.png)  
2. Znajdź i zidentyfikuj następujące profile:
    * Pełny Import
    * Pełna synchronizacja
    * Import zmian
    * Synchronizacja przyrostowa
    * Eksportowanie
3. Dla każdego profilu dostosować **dodane** i **usunięte** domen.
    1. Dla każdego z pięć profilów, wykonaj następujące czynności dla każdej **dodane** domeny:
        1. Wybierz profil, a następnie kliknij przycisk **nowy krok**.
        2. Na **kroku skonfigurować** strony w **typu** menu rozwijanego, wybierz typ kroku z taką samą nazwę jak profil, który jest konfigurowany. Następnie kliknij przycisk **Next** (Dalej).  
        ![Łącznik 2 profilów uruchamiania](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep1.png)  
        3. Na **konfiguracji łącznika** strony w **partycji** menu rozwijanego wybierz nazwę domeny, dodanego do filtru domeny.  
        ![Łącznik 3 profilów uruchamiania](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep2.png)  
        4. Aby zamknąć **konfigurowania profilu uruchamiania** okna dialogowego, kliknij przycisk **Zakończ**.
    2. Dla każdego z pięć profilów, wykonaj następujące czynności dla każdej **usunięte** domeny:
        1. Wybierz profil uruchamiania.
        2. Jeśli **wartość** z **partycji** atrybutu jest identyfikatorem GUID, wybierz wykonywania krok i kliknij przycisk **usunąć krok**.  
        ![Łącznik 4 profilów uruchamiania](./media/active-directory-aadconnectsync-configure-filtering/runprofilesdeletestep.png)  
    3. Sprawdź zmiany. Każdej domeny, który chcesz synchronizować powinien być wyświetlany jako etapem każdego profilu uruchamiania.
4. Aby zamknąć **Konfigurowanie profilów uruchamiania** okna dialogowego, kliknij przycisk **OK**.
5.  Aby zakończyć konfigurację, należy uruchomić **pełny import** i **synchronizacja różnicowa**. Kontynuuj czytanie sekcji [Zastosuj i Sprawdź zmiany](#apply-and-verify-changes).

## <a name="organizational-unitbased-filtering"></a>Filtrowanie na podstawie jednostki organizacyjnej
Preferowany sposób, aby zmienić filtrowanie na podstawie jednostki Organizacyjnej jest uruchomienie Kreatora instalacji i zmieniając [domeny i jednostki Organizacyjnej filtrowania](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering). Kreator instalacji tworzy automatycznie wszystkie zadania, które są opisane w tym temacie.

Kroki te należy wykonać tylko, jeśli nie można uruchomić Kreatora instalacji z jakiegoś powodu.

Aby skonfigurować filtrowanie na podstawie jednostki organizacyjnej, wykonaj następujące czynności:

1. Zaloguj się do serwera, który działa synchronizacja programu Azure AD Connect przy użyciu konta, które jest członkiem **ADSyncAdmins** grupy zabezpieczeń.
2. Uruchom **usługi synchronizacji** z **Start** menu.
3. Wybierz **łączników**, a następnie w **łączników** wybierz łącznik z typem **usług domenowych w usłudze Active Directory**. W **akcje**, wybierz pozycję **właściwości**.  
   ![Właściwości łącznika](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)  
4. Kliknij przycisk **Konfigurowanie partycji katalogu**, wybierz domenę, w której chcesz skonfigurować, a następnie kliknij przycisk **kontenery**.
5. Po wyświetleniu monitu podaj wszystkie poświadczenia z dostępem do odczytu do lokalnej usługi Active Directory. Nie musi być użytkownikiem, który jest wypełniana wstępnie w oknie dialogowym.
6. W **wybierz kontenery** okno dialogowe wyczyść jednostek organizacyjnych, które nie chcesz synchronizować z katalogiem chmury, a następnie kliknij pozycję **OK**.  
   ![Jednostki organizacyjne w oknie dialogowym Wybieranie kontenerów](./media/active-directory-aadconnectsync-configure-filtering/ou.png)  
   * **Komputery** kontenera, należy wybrać komputery z systemem Windows 10 można pomyślnie zsynchronizowane z usługą Azure AD. Jeśli komputery przyłączone do domeny znajdują się w innych jednostek organizacyjnych, upewnij się, że są wybrane.
   * Należy wybrać kontener **ForeignSecurityPrincipals** w przypadku używania wielu lasów z relacjami zaufania. Ten kontener umożliwia rozpoznanie członkostwa w grupie zabezpieczeń obejmującej wiele lasów.
   * **RegisteredDevices** jednostki Organizacyjnej należy wybrać, jeśli włączono funkcję zapisywania zwrotnego urządzeń. Jeśli używasz innej funkcji zapisywania zwrotnego, takie jak zapisywanie zwrotne grup, upewnij się, że wybrane są następujące lokalizacje.
   * Wybierz inne OU, gdzie znajdują się użytkownicy, obiekty InetOrgPerson, grup, kontaktów i komputery. Na ilustracji tych jednostek organizacyjnych znajdują się w jednostce Organizacyjnej ManagedObjects.
   * Jeśli używasz filtrowanie na podstawie grupy, jednostki Organizacyjnej, w której znajduje się grupa muszą być uwzględnione.
   * Należy pamiętać, że możesz określić, czy nowych jednostek organizacyjnych, które są dodawane po zakończeniu konfiguracji filtrowania są zsynchronizowane lub nie jest zsynchronizowany. Zobacz następną sekcję, aby uzyskać szczegółowe informacje.
7. Gdy wszystko będzie gotowe, Zamknij **właściwości** okno dialogowe, klikając **OK**.
8. Aby zakończyć konfigurację, należy uruchomić **pełny import** i **synchronizacja różnicowa**. Kontynuuj czytanie sekcji [Zastosuj i Sprawdź zmiany](#apply-and-verify-changes).

### <a name="synchronize-new-ous"></a>Synchronizuj nowych jednostek organizacyjnych
Domyślnie są synchronizowane nowych jednostek organizacyjnych, które są tworzone po filtrowania został skonfigurowany. Ten stan jest wskazywany przez zaznaczenie pola wyboru. Możesz również usunąć zaznaczenie niektórych podkluczy jednostek organizacyjnych. Aby uzyskać ten problem, kliknij pole aż przyjmie postać białe niebieski znacznika wyboru (stanu domyślnego). Następnie usuń zaznaczenie żadnych sub-jednostek organizacyjnych, które chcesz zsynchronizować.

Jeśli wszystkie podrzędne jednostki organizacyjne są zsynchronizowane, pole jest białym znacznikiem wyboru niebieski.  
![Jednostki Organizacyjnej z wszystkich zaznaczonych bloków](./media/active-directory-aadconnectsync-configure-filtering/ousyncnewall.png)

Jeśli niektóre podrzędne jednostki organizacyjne zostały usunięte, to pole jest szary z białym znacznikiem wyboru.  
![Jednostki Organizacyjnej z niektórych sub-jednostek organizacyjnych niezaznaczony](./media/active-directory-aadconnectsync-configure-filtering/ousyncnew.png)

W tej konfiguracji nowej jednostki Organizacyjnej, który został utworzony w obszarze ManagedObjects jest zsynchronizowany.

Kreator instalacji Azure AD Connect tworzy zawsze tej konfiguracji.

### <a name="dont-synchronize-new-ous"></a>Nie Synchronizuj nowych jednostek organizacyjnych
Można skonfigurować aparatu synchronizacji nie zsynchronizować nowe jednostkami organizacyjnymi po zakończeniu konfiguracji filtrowania. Ten stan jest wskazywany w Interfejsie użytkownika przez pole wyświetlaniu kolor szary bez znacznika wyboru. Aby uzyskać ten problem, kliknij pole aż przyjmie postać białe bez znacznika wyboru. Następnie wybierz sub-jednostek organizacyjnych, które mają być synchronizowane.

![Jednostki Organizacyjnej z głównego niezaznaczony](./media/active-directory-aadconnectsync-configure-filtering/oudonotsyncnew.png)

W tej konfiguracji nowej jednostki Organizacyjnej, który został utworzony w obszarze ManagedObjects nie jest zsynchronizowany.

## <a name="attribute-based-filtering"></a>Filtrowanie na podstawie atrybutu
Upewnij się, że używasz listopad 2015 ([1.0.9125](active-directory-aadconnect-version-history.md#1091250)) lub nowszej kompilacji dla te kroki, aby pracować.

Filtrowanie na podstawie atrybutu jest najbardziej elastyczny sposób z obiektami filtru. Można wykorzystać możliwości [aprowizacją deklaratywną](active-directory-aadconnectsync-understanding-declarative-provisioning.md) do kontrolowania prawie każdego aspektu działania gdy obiekt jest zsynchronizowany z usługą Azure AD.

Możesz zastosować [przychodzących](#inbound-filtering) filtrowania z usługi Active Directory do środowiska metaverse, i [wychodzących](#outbound-filtering) filtrowania z metaverse do usługi Azure AD. Firma Microsoft zaleca stosowanie filtrowanie ruchu przychodzącego, ponieważ jest to najprostsza do obsługi. Należy używać tylko z filtrowania wychodzących, jeśli są wymagane, aby dołączyć obiektów z więcej niż jednym lesie przed oceny można przeprowadzać.

### <a name="inbound-filtering"></a>Liczba przychodzących filtrowania
Filtrowanie ruchu przychodzącego używa konfiguracji domyślnej, gdzie obiektów, przechodząc do usługi Azure AD musi mieć cloudFiltered atrybut metaverse nie ustawiono wartości mają być synchronizowane. Jeśli wartość tego atrybutu jest ustawiona na **True**, a następnie obiekt nie jest zsynchronizowany. Nie należy ustawiać **False**, zgodnie z projektem. Upewnij się, inne reguły mają możliwość współtworzenia wartość, ten atrybut jest tylko powinien mieć wartości **True** lub **NULL** (Brak).

W filtrowanie ruchu przychodzącego, wykorzystaj możliwości **zakres** Aby określić, które obiekty do synchronizacji lub nie synchronizacji. Jest to, gdzie można wprowadzać zmiany wymagań swojej organizacji. Moduł zakresu ma **grupy** i **klauzuli** ustalenie, kiedy reguła synchronizacji znajduje się w zakresie. Grupa zawiera jeden lub wiele klauzul. Brak logiczne "i" między wiele klauzul i logiczne "lub" między wiele grup.

Daj nam przyjrzeć się przykładem:  
![Zakres](./media/active-directory-aadconnectsync-configure-filtering/scope.png)  
To powinno zostać odczytany jako **(działu = IT) lub (działu = sprzedaży i c = US)**.

W następujące przykłady i czynności na przykład przy użyciu obiektu użytkownika, ale możesz użyć tej funkcji dla wszystkich typów obiektów.

W poniższych przykładach wartość pierwszeństwa rozpoczyna się od 50. Może być dowolna liczba nie jest używany, ale powinna być niższa niż 100.

#### <a name="negative-filtering-do-not-sync-these"></a>Filtrowanie ujemny: "synchronizuje je"
W poniższym przykładzie można odfiltrować (Synchronizuj) wszystkich użytkowników gdzie **extensionAttribute15** ma wartość **NoSync**.

1. Zaloguj się do serwera, który działa synchronizacja programu Azure AD Connect przy użyciu konta, które jest członkiem **ADSyncAdmins** grupy zabezpieczeń.
2. Uruchom **Edytor reguł synchronizacji** z **Start** menu.
3. Upewnij się, że **przychodzący** jest zaznaczone, a następnie kliknij przycisk **Dodaj nową regułę**.
4. Nadaj regule nazwę opisową, takie jak "*w z usługi Active Directory — DoNotSyncFilter użytkownika*". Wybierz las poprawna, wybierz pozycję **użytkownika** jako **typu obiektu CS**i wybierz **osoby** jako **typu obiektu MV**. W **typu łącza**, wybierz pozycję **Join**. W **pierwszeństwo**wpisz wartość, która nie jest obecnie używany przez inną regułę synchronizacji (na przykład 50), a następnie kliknij przycisk **dalej**.  
   ![Opis elementu 1 dla ruchu przychodzącego](./media/active-directory-aadconnectsync-configure-filtering/inbound1.png)  
5. W **filtru Scoping**, kliknij przycisk **Dodaj grupę**i kliknij przycisk **Dodaj klauzulę**. W **atrybutu**, wybierz pozycję **ExtensionAttribute15**. Upewnij się, że **Operator** ustawiono **RÓWNY**i wpisz wartość **NoSync** w **wartość** pole. Kliknij przycisk **Dalej**.  
   ![Liczba przychodzących zakresu 2](./media/active-directory-aadconnectsync-configure-filtering/inbound2.png)  
6. Pozostaw **Join** zasady pusty, a następnie kliknij przycisk **dalej**.
7. Kliknij przycisk **dodać przekształcania**, wybierz pozycję **dla przepływu** jako **stałej**i wybierz **cloudFiltered** jako **atrybut Target**. W **źródła** polu tekstowym **True**. Kliknij przycisk **Dodaj** można zapisać reguły.  
   ![Liczba przychodzących przekształcania 3](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)
8. Aby zakończyć konfigurację, należy uruchomić **pełnej synchronizacji**. Kontynuuj czytanie sekcji [Zastosuj i Sprawdź zmiany](#apply-and-verify-changes).

#### <a name="positive-filtering-only-sync-these"></a>Filtrowanie dodatnią: "tylko synchronizować te"
Wyrażanie dodatnią filtrowania może być trudniejsza, ponieważ należy również wziąć pod uwagę obiektów, które nie są oczywiste mają być synchronizowane, takich jak sal konferencyjnych. Ma również zastąpić domyślny filtr w regule out-of-box **w z usługi Active Directory — użytkownik przyłączyć**. Podczas tworzenia niestandardowego filtru, upewnij się, że zawierają obiekty krytycznego, obiekty konflikt replikacji specjalne skrzynek pocztowych i kont usług Azure AD Connect.

Opcji filtrowania dodatnią wymaga dwie reguły synchronizacji. Należy z niewłaściwym zakresie obiekty do zsynchronizowania jedną regułę (lub kilka). Należy również drugą regułę synchronizacji wychwytywania, który odfiltrowuje wszystkie obiekty, które nie zostały jeszcze zidentyfikowane jako obiekt, który powinien zostać zsynchronizowany.

W poniższym przykładzie, tylko synchronizować obiekty użytkownika, gdy atrybut działu ma wartość **sprzedaży**.

1. Zaloguj się do serwera, który działa synchronizacja programu Azure AD Connect przy użyciu konta, które jest członkiem **ADSyncAdmins** grupy zabezpieczeń.
2. Uruchom **Edytor reguł synchronizacji** z **Start** menu.
3. Upewnij się, że **przychodzący** jest zaznaczone, a następnie kliknij przycisk **Dodaj nową regułę**.
4. Nadaj regule nazwę opisową, takie jak "*w z usługi Active Directory — sprzedaży użytkownika synchronizacji*". Wybierz las poprawna, wybierz pozycję **użytkownika** jako **typu obiektu CS**i wybierz **osoby** jako **typu obiektu MV**. W **typu łącza**, wybierz pozycję **Join**. W **pierwszeństwo**wpisz wartość, która nie jest obecnie używany przez inną regułę synchronizacji (na przykład 51), a następnie kliknij przycisk **dalej**.  
   ![Opis elementu 4 przychodzące](./media/active-directory-aadconnectsync-configure-filtering/inbound4.png)  
5. W **filtru Scoping**, kliknij przycisk **Dodaj grupę**i kliknij przycisk **Dodaj klauzulę**. W **atrybutu**, wybierz pozycję **działu**. Upewnij się, że Operator jest ustawiona na **RÓWNY**i wpisz wartość **sprzedaży** w **wartość** pole. Kliknij przycisk **Dalej**.  
   ![Liczba przychodzących zakres 5](./media/active-directory-aadconnectsync-configure-filtering/inbound5.png)  
6. Pozostaw **Join** zasady pusty, a następnie kliknij przycisk **dalej**.
7. Kliknij przycisk **dodać przekształcania**, wybierz pozycję **stałej** jako **dla przepływu**i wybierz **cloudFiltered** jako **atrybut Target**. W **źródła** wpisz **False**. Kliknij przycisk **Dodaj** można zapisać reguły.  
   ![Liczba przychodzących przekształcania 6](./media/active-directory-aadconnectsync-configure-filtering/inbound6.png)  
   Jest to szczególnych przypadkach, w którym zostanie jawnie ustawiona cloudFiltered **False**.
8. Mamy teraz utworzyć regułę synchronizacji wychwytywania. Nadaj regule nazwę opisową, takie jak "*w z usługi Active Directory — filtr wychwytywania użytkownika*". Wybierz las poprawna, wybierz pozycję **użytkownika** jako **typu obiektu CS**i wybierz **osoby** jako **typu obiektu MV**. W **typu łącza**, wybierz pozycję **Join**. W **pierwszeństwo**, wpisz wartość, która nie jest obecnie używany przez inną regułę synchronizacji (na przykład 99). Wybrana wartość priorytetu (niższe pierwszeństwo) poprzednie reguły synchronizacji. Ale również zostały pozostawać niektóre miejsca, aby dodać więcej reguł filtrowania synchronizacji później, gdy ma się rozpocząć synchronizowanie dodatkowe działów. Kliknij przycisk **Dalej**.  
   ![Opis elementu 7 przychodzące](./media/active-directory-aadconnectsync-configure-filtering/inbound7.png)  
9. Pozostaw **filtru Scoping** pusty, a następnie kliknij przycisk **dalej**. Pusty filtr wskazuje, czy reguła ma być stosowany do wszystkich obiektów.
10. Pozostaw **Join** zasady pusty, a następnie kliknij przycisk **dalej**.
11. Kliknij przycisk **dodać przekształcania**, wybierz pozycję **stałej** jako **dla przepływu**i wybierz **cloudFiltered** jako **atrybut Target**. W **źródła** wpisz **True**. Kliknij przycisk **Dodaj** można zapisać reguły.  
    ![Liczba przychodzących przekształcania 3](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)  
12. Aby zakończyć konfigurację, należy uruchomić **pełnej synchronizacji**. Kontynuuj czytanie sekcji [Zastosuj i Sprawdź zmiany](#apply-and-verify-changes).

Jeśli zachodzi potrzeba, można utworzyć więcej reguły pierwszego typu dodanie więcej obiektów w synchronizacji.

### <a name="outbound-filtering"></a>Filtrowanie ruchu wychodzącego
W niektórych przypadkach jest niezbędne do przeprowadzenia filtrowania dopiero po dołączeniu obiektów w magazynie metaverse. Na przykład może być konieczne przyjrzeć się atrybut poczty z lasu zasobów, a atrybut userPrincipalName z lasu kont, aby określić, czy obiekt powinien zostać zsynchronizowany. W takich przypadkach możesz utworzyć filtrowania Reguła ruchu wychodzącego.

W tym przykładzie zmienisz filtrowania, tak aby tylko dla użytkowników, które mają zarówno ich poczty i userPrincipalName kończy się rozszerzeniem @contoso.com są synchronizowane:

1. Zaloguj się do serwera, który działa synchronizacja programu Azure AD Connect przy użyciu konta, które jest członkiem **ADSyncAdmins** grupy zabezpieczeń.
2. Uruchom **Edytor reguł synchronizacji** z **Start** menu.
3. W obszarze **typu reguły**, kliknij przycisk **wychodzące**.
4. W zależności od wersji programu Connect używasz, albo znaleźć reguły o nazwie **Out do usługi AAD — użytkownik przyłączyć** lub **się do usługi AAD — użytkownik przyłączyć SOAInAD**i kliknij przycisk **Edytuj**.
5. W oknie podręcznym odpowiedzi **tak** można utworzyć kopii reguły.
6. Na **opis** Zmień **pierwszeństwo** do nieużywanych wartość, na przykład 50.
7. Kliknij przycisk **filtru Scoping** na nawigacji po lewej stronie, a następnie kliknij **Dodaj klauzulę**. W **atrybutu**, wybierz pozycję **poczty**. W **Operator**, wybierz pozycję **ENDSWITH**. W **wartość**, typ **@contoso.com**, a następnie kliknij przycisk **Dodaj klauzulę**. W **atrybutu**, wybierz pozycję **userPrincipalName**. W **Operator**, wybierz pozycję **ENDSWITH**. W **wartość**, typ **@contoso.com**.
8. Kliknij pozycję **Zapisz**.
9. Aby zakończyć konfigurację, należy uruchomić **pełnej synchronizacji**. Kontynuuj czytanie sekcji [Zastosuj i Sprawdź zmiany](#apply-and-verify-changes).

## <a name="apply-and-verify-changes"></a>Zastosuj i Sprawdź zmiany
Po wprowadzeniu zmian w konfiguracji, należy zastosować je do obiektów, które już znajdują się w systemie. Można ją również powinny być przetwarzane obiekty, które nie są obecnie w aparacie synchronizacji (i aparatu synchronizacji musi odczytać systemu źródłowego ponownie w celu zweryfikowania jego zawartość).

Jeśli zmieniono konfigurację za pomocą **domeny** lub **jednostki organizacyjnej** filtrowania, a następnie należy wykonać **pełny import**, a następnie **synchronizacja przyrostowa**.

Jeśli zmieniono konfigurację za pomocą **atrybutu** filtrowania, a następnie należy wykonać **pełnej synchronizacji**.

Wykonaj następujące czynności:

1. Uruchom **usługi synchronizacji** z **Start** menu.
2. Wybierz **łączniki**. W **łączniki** listy, wybierz łącznik, w którym wprowadzono zmiany wcześniej konfiguracji. W **akcje**, wybierz pozycję **Uruchom**.  
   ![Uruchom łącznika](./media/active-directory-aadconnectsync-configure-filtering/connectorrun.png)  
3. W **profilów uruchamiania**, wybierz operację, którą wspomniano w poprzedniej sekcji. Jeśli musisz uruchomić dwie akcje, uruchom sekundy po zakończeniu pierwszego z nich. ( **Stanu** kolumna jest **bezczynny** wybranego łącznika.)

Po synchronizacji wszystkie zmiany są przygotowane do wyeksportowania. Przed wprowadzeniem zmian faktycznie w usłudze Azure AD, chcesz sprawdzić, czy te zmiany są poprawne.

1. Uruchom wiersz polecenia i przejdź do `%Program Files%\Microsoft Azure AD Sync\bin`.
2. Uruchom polecenie `csexport "Name of Connector" %temp%\export.xml /f:x`.  
   Nazwa łącznika jest usługi synchronizacji. Ma on nazwę, podobnie jak "contoso.com — AAD" dla usługi Azure AD.
3. Uruchom polecenie `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`.
4. Istnieje już plik o nazwie export.csv, która może być zbadana w programie Microsoft Excel % temp %. Ten plik zawiera wszystkie zmiany, które mają być eksportowane.
5. Wprowadź niezbędne zmiany dane lub konfiguracja, a następnie uruchom te kroki ponownie (importowania, synchronizowania i sprawdź, czy) do momentu zmiany, które mają zostać wyeksportowane oczekiwań.

Po zakończeniu, należy wyeksportować zmiany do usługi Azure AD.

1. Wybierz **łączniki**. W **łączniki** wybierz łącznik usługi Azure AD. W **akcje**, wybierz pozycję **Uruchom**.
2. W **profilów uruchamiania**, wybierz pozycję **wyeksportować**.
3. Jeśli zmiany w konfiguracji usuwają wiele obiektów, następnie zostanie wyświetlony błąd w pliku eksportu, jeśli liczba jest większa niż skonfigurowany próg (domyślnie 500). Jeśli zostanie wyświetlony ten błąd, a następnie chcesz tymczasowo wyłączyć "[Zapobieganie przypadkowemu usuwaniu](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)" funkcji.

Teraz nadszedł czas, aby ponownie włączyć harmonogramu.

1. Uruchom **harmonogram zadań** z **Start** menu.
2. Bezpośrednio pod **Biblioteka Harmonogramu zadań**, znaleźć zadania o nazwie **Azure AD Sync Scheduler**, kliknij prawym przyciskiem myszy, a następnie wybierz **włączyć**.

## <a name="group-based-filtering"></a>Filtrowanie na podstawie grupy
Można skonfigurować filtrowanie na podstawie grupy po raz pierwszy zainstalować Azure AD Connect przy użyciu [instalacji niestandardowej](active-directory-aadconnect-get-started-custom.md#sync-filtering-based-on-groups). Jest on przeznaczony do wdrożenia pilotażowego, w którym ma niewielki zestaw obiektów, które mają być synchronizowane. Po wyłączeniu filtrowanie na podstawie grupy, nie można włączyć ponownie. Ma ona *nieobsługiwane* umożliwia filtrowanie na podstawie grupy w konfiguracji niestandardowej. Jest obsługiwana tylko do tej funkcji można skonfigurować przy użyciu Kreatora instalacji. Po zakończeniu pracy programu pilotażowego, następnie użyj jednej z innych opcji filtrowania w tym temacie. Korzystając z filtrowanie oparte na jednostce Organizacyjnej w połączeniu z filtrowanie na podstawie grupy, jednostką organizacyjną, w którym znajdują się grupie i jej elementów członkowskich należy dołączyć.

Podczas synchronizowania wiele lasów usługi AD, można skonfigurować filtrowanie na podstawie grupy, określając inną grupę przez każdy łącznik AD. W razie potrzeby do synchronizowania użytkownika AD jeden las i tego samego użytkownika zawiera co najmniej odpowiadającego więcej obiektów w innych lasach AD, należy upewnić się, że obiekt użytkownika i wszystkie odpowiednie obiekty w ramach oparte na grupach filtrowania zakresu. Przykłady:

* Masz użytkownika w jednym lesie, który ma odpowiedni obiekt FSP (obcego podmiotu zabezpieczeń) w innym lesie. Zarówno do obiektów musi być w oparte na grupach filtrowanie zakresu. W przeciwnym razie użytkownik nie będą synchronizowane z usługą Azure AD.

* Masz użytkownika w jednym lesie, który ma odpowiadającego mu konta zasobu (np. połączona Skrzynka pocztowa) w innym lesie. Ponadto skonfigurowano Azure AD Connect, aby połączyć użytkownika z konta zasobu. Zarówno do obiektów musi być w oparte na grupach filtrowanie zakresu. W przeciwnym razie użytkownik nie będą synchronizowane z usługą Azure AD.

* Masz użytkownika w jednym lesie, który ma odpowiednie poczty kontaktu w innym lesie. Co więcej skonfigurowaniu usługi Azure AD Connect, aby połączyć użytkownika z kontakt poczty. Zarówno do obiektów musi być w oparte na grupach filtrowanie zakresu. W przeciwnym razie użytkownik nie będą synchronizowane z usługą Azure AD.


## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [synchronizacja programu Azure AD Connect](active-directory-aadconnectsync-whatis.md) konfiguracji.
- Dowiedz się więcej o [integrowanie tożsamości lokalnych z usługą Azure AD](active-directory-aadconnect.md).
