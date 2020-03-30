---
title: 'Synchronizacja usługi Azure AD Connect: konfigurowanie filtrowania | Dokumenty firmy Microsoft'
description: W tym artykule wyjaśniono, jak skonfigurować filtrowanie w synchronizacji usługi Azure AD Connect.
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
ms.openlocfilehash: 983699dfbfe3e8fa332da4810d1514a11029077f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261101"
---
# <a name="azure-ad-connect-sync-configure-filtering"></a>Synchronizacja programu Azure AD Connect: konfigurowanie filtrowania
Za pomocą filtrowania można kontrolować, które obiekty są wyświetlane w usłudze Azure Active Directory (Azure AD) z katalogu lokalnego. Domyślna konfiguracja przyjmuje wszystkie obiekty we wszystkich domenach w skonfigurowanych lasach. Ogólnie rzecz biorąc jest to zalecana konfiguracja. Użytkownicy korzystający z obciążeń usługi Office 365, takich jak Exchange Online i Skype dla firm, korzystają z pełnej globalnej listy adresów, dzięki czemu mogą wysyłać wiadomości e-mail i dzwonić do wszystkich. W konfiguracji domyślnej będą miały takie samo środowisko, jakie miałyby z lokalną implementacją programu Exchange lub Lync.

W niektórych przypadkach jednak wymagane jest wprowadzenie pewnych zmian w konfiguracji domyślnej. Oto kilka przykładów:

* Planujesz używać [topologii katalogów usługi AZURE dla wielu usług Ad](plan-connect-topologies.md#each-object-only-once-in-an-azure-ad-tenant). Następnie należy zastosować filtr do kontroli, które obiekty są synchronizowane z określonym katalogiem usługi Azure AD.
* Uruchom program pilotażowy dla platformy Azure lub usługi Office 365 i chcesz tylko podzbiór użytkowników w usłudze Azure AD. W małym pilocie nie jest ważne, aby mieć pełną globalną listę adresów, aby zademonstrować funkcjonalność.
* Masz wiele kont usług i innych kont nieosobowych, których nie chcesz w usłudze Azure AD.
* Ze względu na zgodność nie usuwasz żadnych kont użytkowników w środowisku lokalnym. Wyłączasz tylko te te. Ale w usłudze Azure AD chcesz mieć tylko aktywne konta.

W tym artykule opisano, jak skonfigurować różne metody filtrowania.

> [!IMPORTANT]
> Firma Microsoft nie obsługuje modyfikowania ani działania synchronizacji programu Azure AD Connect poza akcjami, które zostały formalnie udokumentowane. Każda z tych akcji może spowodować niespójny lub nieobsługiowany stan synchronizacji usługi Azure AD Connect. W rezultacie firma Microsoft nie może zapewnić pomocy technicznej dla takich wdrożeń.

## <a name="basics-and-important-notes"></a>Podstawy i ważne uwagi
W usłudze Azure AD Connect synchronizacji można włączyć filtrowanie w dowolnym momencie. Jeśli zaczniesz od domyślnej konfiguracji synchronizacji katalogów, a następnie skonfigurujesz filtrowanie, obiekty, które są odfiltrowane, nie będą już synchronizowane z usługą Azure AD. Z powodu tej zmiany wszystkie obiekty w usłudze Azure AD, które zostały wcześniej zsynchronizowane, ale zostały następnie filtrowane, są usuwane w usłudze Azure AD.

Przed rozpoczęciem wprowadzania zmian w filtrowaniu upewnij się, że [zostało wyłączone zaplanowane zadanie,](#disable-the-scheduled-task) aby nie wyeksportować przypadkowo zmian, które nie zostały jeszcze zweryfikowane jako poprawne.

Ponieważ filtrowanie można usunąć wiele obiektów w tym samym czasie, należy upewnić się, że nowe filtry są poprawne przed rozpoczęciem eksportowania wszelkich zmian do usługi Azure AD. Po wykonaniu kroków konfiguracji zdecydowanie zaleca się wykonanie [kroków weryfikacji](#apply-and-verify-changes) przed wyeksportowaniem i wprowadzeniem zmian w usłudze Azure AD.

Aby chronić cię przed usunięciem wielu obiektów przez przypadek, funkcja "[zapobiegaj przypadkowym usuwaniu](how-to-connect-sync-feature-prevent-accidental-deletes.md)" jest domyślnie włączona. Jeśli usuniesz wiele obiektów z powodu filtrowania (domyślnie 500), należy wykonać kroki opisane w tym artykule, aby umożliwić usunięcie przejść do usługi Azure AD.

Jeśli używasz kompilacji przed listopadem 2015 r.[(1.0.9125](reference-connect-version-history.md#1091250)), wprowadzać zmiany w konfiguracji filtru i używać synchronizacji skrótów haseł, musisz wyzwolić pełną synchronizację wszystkich haseł po zakończeniu konfiguracji. Aby uzyskać informacje o tym, jak wyzwolić pełną synchronizację hasła, zobacz [Wyzwalanie pełnej synchronizacji wszystkich haseł](tshoot-connect-password-hash-synchronization.md#trigger-a-full-sync-of-all-passwords). Jeśli korzystasz z kompilacji 1.0.9125 lub nowszej, akcja **regularnej pełnej synchronizacji** oblicza również, czy hasła powinny być synchronizowane i czy ten dodatkowy krok nie jest już wymagany.

Jeśli obiekty **użytkownika** zostały przypadkowo usunięte w usłudze Azure AD z powodu błędu filtrowania, można ponownie utworzyć obiekty użytkownika w usłudze Azure AD, usuwając konfiguracje filtrowania. Następnie można ponownie zsynchronizować katalogi. Ta akcja przywraca użytkowników z kosza w usłudze Azure AD. Jednak nie można cofnąć usunięcia innych typów obiektów. Na przykład jeśli przypadkowo usuniesz grupę zabezpieczeń i została ona użyta do listy ACL zasób, grupy i jej list ACL nie będzie można odzyskać.

Usługa Azure AD Connect usuwa tylko obiekty, które kiedyś uważała za znajdujące się w zakresie. Jeśli istnieją obiekty w usłudze Azure AD, które zostały utworzone przez inny aparat synchronizacji i te obiekty nie są w zakresie, dodanie filtrowania nie usuwa ich. Na przykład, jeśli zaczniesz od serwera DirSync, który utworzył pełną kopię całego katalogu w usłudze Azure AD, a nowy serwer synchronizacji usługi Azure AD Connect zostanie zainstalowany równolegle z włączonym filtrem od początku usługa Azure AD Connect nie usunie dodatkowych obiektów które są tworzone przez DirSync.

Konfiguracja filtrowania jest zachowywana podczas instalowania lub uaktualniania do nowszej wersji usługi Azure AD Connect. Zawsze najlepszym rozwiązaniem jest sprawdzenie, czy konfiguracja nie została przypadkowo zmieniona po uaktualnieniu do nowszej wersji przed uruchomieniem pierwszego cyklu synchronizacji.

Jeśli masz więcej niż jeden las, należy zastosować konfiguracje filtrowania, które są opisane w tym temacie do każdego lasu (przy założeniu, że chcesz taką samą konfigurację dla wszystkich z nich).

### <a name="disable-the-scheduled-task"></a>Wyłączanie zaplanowanego zadania
Aby wyłączyć wbudowany harmonogram, który wyzwala cykl synchronizacji co 30 minut, wykonaj następujące kroki:

1. Przejdź do monitu programu PowerShell.
2. Uruchom, `Set-ADSyncScheduler -SyncCycleEnabled $False` aby wyłączyć harmonogram.
3. W tym artykule należy wprowadzić zmiany.
4. Uruchom, `Set-ADSyncScheduler -SyncCycleEnabled $True` aby ponownie włączyć harmonogram.

**Jeśli używasz kompilacji usługi Azure AD Connect przed 1.1.105.0**  
Aby wyłączyć zaplanowane zadanie, które wyzwala cykl synchronizacji co trzy godziny, wykonaj następujące kroki:

1. Uruchom **Harmonogram zadań** z menu **Start.**
2. Bezpośrednio w obszarze **Biblioteka harmonogramu zadań**znajdź zadanie o nazwie **Harmonogram synchronizacji usługi Azure AD**, kliknij prawym przyciskiem myszy i wybierz polecenie **Wyłącz**.  
   ![Harmonogram zadań](./media/how-to-connect-sync-configure-filtering/taskscheduler.png)  
3. Teraz można wprowadzać zmiany w konfiguracji i ręcznie uruchamiać aparat synchronizacji z konsoli **Menedżera usług synchronizacji.**

Po zakończeniu wszystkich zmian filtrowania nie zapomnij wrócić i ponownie **włączyć** zadanie.

## <a name="filtering-options"></a>Opcje filtrowania
Do narzędzia do synchronizacji katalogów można zastosować następujące typy konfiguracji filtrowania:

* [**Oparte na grupach**](#group-based-filtering): Filtrowanie na podstawie jednej grupy można skonfigurować tylko przy instalacji początkowej za pomocą kreatora instalacji.
* [**Oparte na domenie:**](#domain-based-filtering)Korzystając z tej opcji, można wybrać, które domeny są synchronizowane z usługą Azure AD. Po zainstalowaniu synchronizacji usługi Azure AD Connect można również dodawać i usuwać domeny z konfiguracji aparatu synchronizacji po wprowadzeniu zmian w infrastrukturze lokalnej.
* [**Jednostka organizacyjna (OU)- oparty:**](#organizational-unitbased-filtering)Korzystając z tej opcji, można wybrać, które jednostki organizacyjne synchronizują z usługą Azure AD. Ta opcja jest dostępna dla wszystkich typów obiektów w wybranych systemach organizacyjnych.
* [**Oparte na atrybutach**](#attribute-based-filtering): Za pomocą tej opcji można filtrować obiekty na podstawie wartości atrybutów obiektów. Można również mieć różne filtry dla różnych typów obiektów.

Można użyć wielu opcji filtrowania w tym samym czasie. Na przykład można użyć filtrowania opartego na uj, aby uwzględnić tylko obiekty w jednej ou. W tym samym czasie można użyć filtrowania opartego na atrybutach, aby filtrować obiekty dalej. W przypadku korzystania z wielu metod filtrowania filtry używają logicznego "AND" między filtrami.

## <a name="domain-based-filtering"></a>Filtrowanie oparte na domenie
W tej sekcji przedstawiono kroki konfigurowania filtru domeny. Jeśli domeny zostały dodane lub usunięte w lesie po zainstalowaniu usługi Azure AD Connect, należy również zaktualizować konfigurację filtrowania.

Preferowanym sposobem zmiany filtrowania opartego na domenie jest uruchomienie kreatora instalacji i zmiana [filtrowania domeny i instalacji organizacyjnej.](how-to-connect-install-custom.md#domain-and-ou-filtering) Kreator instalacji automatyzuje wszystkie zadania, które są udokumentowane w tym temacie.

Należy wykonać następujące kroki tylko wtedy, gdy z jakiegoś powodu nie można uruchomić kreatora instalacji.

Konfiguracja filtrowania oparta na domenie składa się z następujących kroków:

1. Wybierz domeny, które chcesz uwzględnić w synchronizacji.
2. Dla każdej dodanej i usuniętej domeny dostosuj profile uruchamiania.
3. [Zastosuj i zweryfikuj zmiany](#apply-and-verify-changes).

### <a name="select-the-domains-to-be-synchronized"></a>Wybieranie domen, które mają być synchronizowane
Istnieją dwa sposoby wybierania domen, które mają być synchronizowane:
    - Korzystanie z usługi synchronizacji
    - Korzystanie z kreatora usługi Azure AD Connect.


#### <a name="select-the-domains-to-be-synchronized-using-the-synchronization-service"></a>Wybierz domeny, które mają być synchronizowane za pomocą usługi synchronizacji
Aby ustawić filtr domeny, wykonaj następujące czynności:

1. Zaloguj się do serwera, na który jest uruchomiona synchronizacja usługi Azure AD Connect przy użyciu konta, które jest członkiem grupy zabezpieczeń **ADSyncAdmins.**
2. Uruchom **usługę synchronizacji** z menu **Start.**
3. Wybierz **pozycję Łączniki**i na liście **Łączniki** wybierz łącznik z **typem Usługi domenowe Active Directory**. W **obszarze Akcje**wybierz pozycję **Właściwości**.  
   ![Właściwości łącznika](./media/how-to-connect-sync-configure-filtering/connectorproperties.png)  
4. Kliknij **pozycję Konfiguruj partycje katalogu**.
5. Na liście **Wybierz partycje katalogu** zaznacz i usuń zaznaczenie domen zgodnie z potrzebami. Sprawdź, czy są zaznaczone tylko partycje, które chcesz zsynchronizować.  
   ![Partycje](./media/how-to-connect-sync-configure-filtering/connectorpartitions.png)  
   Jeśli lokalna infrastruktura usługi Active Directory została zmieniona i dodana lub usunięta domena z lasu, kliknij przycisk **Odśwież,** aby uzyskać zaktualizowaną listę. Podczas odświeżania zostaniesz poproszony o podanie poświadczeń. Podaj wszelkie poświadczenia z dostępem do odczytu do usługi Active Directory systemu Windows Server. Nie musi być użytkownikiem, który jest wstępnie wypełniony w oknie dialogowym.  
   ![Odświeżanie potrzebne](./media/how-to-connect-sync-configure-filtering/refreshneeded.png)  
6. Po zakończeniu zamknij okno dialogowe **Właściwości,** klikając przycisk **OK**. Jeśli domeny zostały usunięte z lasu, w wyskakującym okienku zostanie wyświetlony komunikat informujący, że domena została usunięta i że konfiguracja zostanie wyczyszczona.
7. Kontynuuj dostosowywanie profili przebiegu.

#### <a name="select-the-domains-to-be-synchronized-using-the-azure-ad-connect-wizard"></a>Wybieranie domen, które mają być synchronizowane za pomocą kreatora usługi Azure AD Connect
Aby ustawić filtr domeny, wykonaj następujące czynności:

1.  Uruchamianie kreatora usługi Azure AD Connect
2.  Kliknij **pozycję Konfiguruj**.
3.  Wybierz **pozycję Dostosuj opcje synchronizacji** i kliknij przycisk **Dalej**.
4.  Wprowadzanie poświadczeń usługi Azure AD
5.  Na ekranie **Połączone katalogi** kliknij przycisk **Dalej**.
6.  Na **stronie filtrowanie domeny i wy organizacyjnej** kliknij pozycję **Odśwież**.  Teraz pojawiają się nowe chore domeny, a usunięte domeny znikną.
   ![Partycje](./media/how-to-connect-sync-configure-filtering/update2.png)  

### <a name="update-the-run-profiles"></a>Aktualizowanie profilów uruchamiania
Jeśli zaktualizowałeś filtr domeny, musisz również zaktualizować profile uruchamiania.

1. Na liście **Łączniki** upewnij się, że łącznik, który został zmieniony w poprzednim kroku jest zaznaczony. W **obszarze Akcje**wybierz pozycję **Konfiguruj profile uruchamiania**.  
   ![Profile uruchamiania łącznika 1](./media/how-to-connect-sync-configure-filtering/connectorrunprofiles1.png)  
2. Znajdź i zidentyfikuj następujące profile:
    * Pełny import
    * Pełna synchronizacja
    * Import zmian
    * Synchronizacja zmian
    * Eksportowanie
3. Dla każdego profilu dostosuj **dodane** i **usunięte** domeny.
    1. Dla każdego z pięciu profili wykonaj następujące czynności dla każdej **dodanej** domeny:
        1. Zaznacz profil uruchamiania i kliknij pozycję **Nowy krok**.
        2. Na stronie **Konfiguruj krok** w menu rozwijanym **Typ** wybierz typ kroku o takiej samej nazwie jak profil, który konfigurujesz. Następnie kliknij przycisk **Dalej**.  
        ![Profile uruchamiania złączy 2](./media/how-to-connect-sync-configure-filtering/runprofilesnewstep1.png)  
        3. Na stronie **Konfiguracja łącznika** w menu rozwijanym **Partycja** wybierz nazwę domeny dodanej do filtru domeny.  
        ![Profile uruchamiania złączy 3](./media/how-to-connect-sync-configure-filtering/runprofilesnewstep2.png)  
        4. Aby zamknąć okno dialogowe **Konfigurowanie profilu uruchamiania,** kliknij przycisk **Zakończ**.
    2. Dla każdego z pięciu profili wykonaj następujące czynności dla każdej **usuniętej** domeny:
        1. Wybierz profil uruchomienia.
        2. Jeśli **wartość** atrybutu **Partycja** jest identyfikatorem GUID, zaznacz krok uruchomienia i kliknij przycisk **Usuń krok**.  
        ![Profile uruchamiania złączy 4](./media/how-to-connect-sync-configure-filtering/runprofilesdeletestep.png)  
    3. Zweryfikuj zmianę. Każda domena, którą chcesz zsynchronizować, powinna być wymieniona jako krok w każdym profilu uruchamiania.
4. Aby zamknąć okno dialogowe **Konfigurowanie profili uruchamiania,** kliknij przycisk **OK**.
5.  Aby ukończyć konfigurację, należy uruchomić **pełny import** i **synchronizację delta**. Kontynuuj czytanie sekcji [Zastosuj i zweryfikuj zmiany](#apply-and-verify-changes).

## <a name="organizational-unitbased-filtering"></a>Filtrowanie oparte na jednostkach organizacyjnych
Preferowanym sposobem zmiany filtrowania opartego na uj polega na uruchomieniu kreatora instalacji i zmianie [filtrowania domeny i instalacji organizacyjnej.](how-to-connect-install-custom.md#domain-and-ou-filtering) Kreator instalacji automatyzuje wszystkie zadania, które są udokumentowane w tym temacie.

Należy wykonać następujące kroki tylko wtedy, gdy z jakiegoś powodu nie można uruchomić kreatora instalacji.

Aby skonfigurować filtrowanie oparte na jednostkach organizacyjnych, wykonaj następujące czynności:

1. Zaloguj się do serwera, na który jest uruchomiona synchronizacja usługi Azure AD Connect przy użyciu konta, które jest członkiem grupy zabezpieczeń **ADSyncAdmins.**
2. Uruchom **usługę synchronizacji** z menu **Start.**
3. Wybierz **pozycję Łączniki**i na liście **Łączniki** wybierz łącznik z **typem Usługi domenowe Active Directory**. W **obszarze Akcje**wybierz pozycję **Właściwości**.  
   ![Właściwości łącznika](./media/how-to-connect-sync-configure-filtering/connectorproperties.png)  
4. Kliknij **pozycję Konfiguruj partycje katalogu**, wybierz domenę, którą chcesz skonfigurować, a następnie kliknij pozycję **Kontenery**.
5. Po wyświetleniu monitu podaj wszystkie poświadczenia z dostępem do odczytu do lokalnej usługi Active Directory. Nie musi być użytkownikiem, który jest wstępnie wypełniony w oknie dialogowym.
6. W oknie dialogowym **Wybieranie kontenerów** wyczyść środowisko organizacyjne, których nie chcesz synchronizować z katalogiem w chmurze, a następnie kliknij przycisk **OK**.  
   ![Liczba procesorów w oknie dialogowym Wybieranie kontenerów](./media/how-to-connect-sync-configure-filtering/ou.png)  
   * Kontener **Komputery** powinien zostać wybrany dla komputerów z systemem Windows 10, które mają zostać pomyślnie zsynchronizowane z usługą Azure AD. Jeśli komputery przyłączone do domeny znajdują się w innych systemach organizacyjnych, upewnij się, że są zaznaczone.
   * Należy wybrać kontener **ForeignSecurityPrincipals** w przypadku używania wielu lasów z relacjami zaufania. Ten kontener umożliwia rozpoznanie członkostwa w grupie zabezpieczeń obejmującej wiele lasów.
   * W przypadku włączenia funkcji storcowania urządzenia należy wybrać operację OU **registereddevices.** Jeśli używasz innej funkcji stornowanie, takiej jak stornowanie grup, upewnij się, że te lokalizacje są zaznaczone.
   * Wybierz dowolną inną ou, w której znajdują się użytkownicy, iNetOrgPersons, grupy, kontakty i komputery. Na rysunku wszystkie te jednostki organizacyjne znajdują się w managedobjects jednostki organizacyjnej.
   * Jeśli używasz filtrowania opartego na grupach, należy uwzględnić jednostka organizacyjna, w której znajduje się grupa.
   * Należy zauważyć, że można skonfigurować, czy nowe procesory organizacyjne, które są dodawane po zakończeniu konfiguracji filtrowania są synchronizowane, czy nie są synchronizowane. Zobacz następną sekcję, aby uzyskać szczegółowe informacje.
7. Po zakończeniu zamknij okno dialogowe **Właściwości,** klikając przycisk **OK**.
8. Aby ukończyć konfigurację, należy uruchomić **pełny import** i **synchronizację delta**. Kontynuuj czytanie sekcji [Zastosuj i zweryfikuj zmiany](#apply-and-verify-changes).

### <a name="synchronize-new-ous"></a>Synchronizowanie nowych procesorów operacyjnych
Nowe procesory organizacyjne tworzone po skonfigurowaniu filtrowania są domyślnie synchronizowane. Ten stan jest wskazywany przez zaznaczone pole wyboru. Można również usunąć zaznaczenie niektórych podzespołów. Aby uzyskać to zachowanie, kliknij pole, aż stanie się białe z niebieskim znacznikiem wyboru (jego stan domyślny). Następnie usuń zaznaczenie wszystkich podrzędnych procesorów operacyjnych, których nie chcesz synchronizować.

Jeśli wszystkie podzespoły są zsynchronizowane, pole jest białe z niebieskim znacznikiem wyboru.  
![OU z zaznaczonymi wszystkimi polami](./media/how-to-connect-sync-configure-filtering/ousyncnewall.png)

Jeśli niektóre podzeprocesowe nie zostały zaznaczone, pole jest szare z białym znacznikiem wyboru.  
![OU z niektórymi niewybranymi systemami organizacyjnymi](./media/how-to-connect-sync-configure-filtering/ousyncnew.png)

W tej konfiguracji zostanie zsynchronizowany nowy ou utworzony w obszarze ManagedObjects.

Kreator instalacji usługi Azure AD Connect zawsze tworzy tę konfigurację.

### <a name="dont-synchronize-new-ous"></a>Nie synchronizuj nowych procesorów operacyjnych
Można skonfigurować aparat synchronizacji, aby nie synchronizować nowych układów organizacyjnych po zakończeniu konfiguracji filtrowania. Ten stan jest wskazywany w interfejsie użytkownika przez pole przedstawiające kolor na szaro bez znacznika wyboru. Aby uzyskać to zachowanie, kliknij pole, aż stanie się białe bez znacznika wyboru. Następnie wybierz podrzędne procesory organizacyjne, które chcesz zsynchronizować.

![OU z katalogiem głównym niezaznaczone](./media/how-to-connect-sync-configure-filtering/oudonotsyncnew.png)

W tej konfiguracji nowa jednostka organizacyjna utworzona w obszarze ManagedObjects nie jest synchronizowana.

## <a name="attribute-based-filtering"></a>Filtrowanie oparte na atrybutach
Upewnij się, że używasz kompilacji z listopada 2015[r. (1.0.9125)](reference-connect-version-history.md#1091250)lub nowszej, aby te kroki działały.

> [!IMPORTANT]
>Firma Microsoft zaleca, aby nie modyfikować reguł domyślnych utworzonych przez **usługę Azure AD Connect**. Jeśli chcesz zmodyfikować regułę, sklonuj ją i wyłącz oryginalną regułę. Wprowadzać zmiany w sklonowanej regule. Należy pamiętać, że w ten sposób (wyłączenie pierwotnej reguły) przegapisz wszelkie poprawki błędów lub funkcje włączone przez tę regułę.

Filtrowanie oparte na atrybutach jest najbardziej elastycznym sposobem filtrowania obiektów. Można użyć uprawnień [deklaratywne inicjowania obsługi administracyjnej](concept-azure-ad-connect-sync-declarative-provisioning.md) do kontrolowania prawie każdy aspekt, gdy obiekt jest synchronizowany z usługą Azure AD.

Filtrowanie [przychodzące](#inbound-filtering) z usługi Active Directory można zastosować do metaverse i filtrowanie [wychodzące](#outbound-filtering) z metaverse do usługi Azure AD. Zaleca się zastosowanie filtrowania przychodzącego, ponieważ jest to najłatwiejsze do utrzymania. Filtrowanie wychodzące należy używać tylko wtedy, gdy jest wymagane łączenie obiektów z więcej niż jednego lasu przed przeprowadzeniem oceny.

### <a name="inbound-filtering"></a>Filtrowanie przychodzące
Filtrowanie przychodzące używa domyślnej konfiguracji, w której obiekty przechodzące do usługi Azure AD muszą mieć atrybut metaverse cloudFiltered nie ustawiony na wartość do synchronizacji. Jeśli wartość tego atrybutu jest ustawiona na **Wartość True,** obiekt nie jest synchronizowany. Nie powinien być ustawiony na **False**, zgodnie z projektem. Aby upewnić się, że inne reguły mają możliwość współtworzenia wartości, ten atrybut ma mieć tylko wartości **Prawda** lub **NULL** (brak).

Podczas filtrowania przychodzącego można użyć mocy **zakresu,** aby określić, które obiekty mają być synchronizowane lub niezsynchronizowane. W tym miejscu wprowadzasz zmiany, aby dopasować je do wymagań własnej organizacji. Moduł zakresu ma **grupę** i **klauzulę,** aby określić, kiedy reguła synchronizacji jest w zakresie. Grupa zawiera jedną lub wiele klauzul. Istnieje logiczne "AND" między wieloma klauzulami i logiczne "OR" między wieloma grupami.

Spójrzmy na przykład:  
![Zakres](./media/how-to-connect-sync-configure-filtering/scope.png)  
Należy to odczytać jako **(dział = IT) LUB (dział = Sprzedaż i c = USA)**.

W poniższych przykładach i krokach można użyć obiektu użytkownika jako przykładu, ale można go użyć dla wszystkich typów obiektów.

W poniższych przykładach wartość pierwszeństwa zaczyna się od 50. Może to być dowolna liczba, która nie jest używana, ale powinna być niższa niż 100.

#### <a name="negative-filtering-do-not-sync-these"></a>Filtrowanie ujemne: "nie synchronizuj tych"
W poniższym przykładzie odfiltrować (nie synchronizować) wszystkich użytkowników, gdzie **extensionAttribute15** ma wartość **NoSync**.

1. Zaloguj się do serwera, na który jest uruchomiona synchronizacja usługi Azure AD Connect przy użyciu konta, które jest członkiem grupy zabezpieczeń **ADSyncAdmins.**
2. Uruchom **Edytor reguł synchronizacji** z menu **Start.**
3. Upewnij się, że **opcja Ruch przychodzący** jest zaznaczona, a następnie kliknij przycisk **Dodaj nową regułę**.
4. Nadaj regule nazwę opisową, na przykład "*In from AD – User DoNotSyncFilter*". Wybierz właściwy las, wybierz **opcję Użytkownik** jako typ **obiektu CS**i wybierz opcję **Osoba** jako typ **obiektu MV**. W **obszarze Typ łącza**wybierz pozycję **Dołącz**. W **polu Pierwszeństwo**wpisz wartość, która nie jest obecnie używana przez inną regułę synchronizacji (na przykład 50), a następnie kliknij przycisk **Dalej**.  
   ![Opis przychodzącego 1](./media/how-to-connect-sync-configure-filtering/inbound1.png)  
5. W **filtrze zakres kliknij**pozycję Dodaj **grupę**i kliknij pozycję **Dodaj klauzulę**. W **obszarze Atrybut**wybierz pozycję **ExtensionAttribute15**. Upewnij się, że **operator** jest ustawiony na **EQUAL**i wpisz wartość **NoSync** w polu **Wartość.** Kliknij przycisk **alej**.  
   ![Zakres przychodzący 2](./media/how-to-connect-sync-configure-filtering/inbound2.png)  
6. Pozostaw reguły **dołączania** puste, a następnie kliknij przycisk **Dalej**.
7. Kliknij **pozycję Dodaj transformację**, wybierz **typ flowtype** jako **stałą**i wybierz **cloudFiltered** jako **atrybut docelowy**. W polu **tekstowym Źródło** wpisz wartość **True**. Kliknij **przycisk Dodaj,** aby zapisać regułę.  
   ![Transformacja przychodząca 3](./media/how-to-connect-sync-configure-filtering/inbound3.png)
8. Aby ukończyć konfigurację, musisz uruchomić **pełną synchronizację**. Kontynuuj czytanie sekcji [Zastosuj i zweryfikuj zmiany](#apply-and-verify-changes).

#### <a name="positive-filtering-only-sync-these"></a>Filtrowanie pozytywne: "synchronizuj tylko te"
Wyrażanie filtrowania pozytywnego może być trudniejsze, ponieważ należy również wziąć pod uwagę obiekty, które nie są oczywiste do synchronizacji, takie jak sale konferencyjne. Masz również zamiar zastąpić domyślny filtr w out-of-box reguły **w z usługi AD - User Join**. Podczas tworzenia filtru niestandardowego upewnij się, że nie zawiera krytycznych obiektów systemowych, obiektów konfliktu replikacji, specjalnych skrzynek pocztowych i kont usługi Azure AD Connect.

Opcja filtrowania dodatniego wymaga dwóch reguł synchronizacji. Potrzebna jest jedna reguła (lub kilka) z poprawnym zakresem obiektów do synchronizacji. Potrzebna jest również druga reguła synchronizacji typu catch-all, która odfiltruje wszystkie obiekty, które nie zostały jeszcze zidentyfikowane jako obiekt, który powinien zostać zsynchronizowany.

W poniższym przykładzie można synchronizować tylko obiekty użytkownika, w których atrybut działu ma wartość **Sprzedaż**.

1. Zaloguj się do serwera, na który jest uruchomiona synchronizacja usługi Azure AD Connect przy użyciu konta, które jest członkiem grupy zabezpieczeń **ADSyncAdmins.**
2. Uruchom **Edytor reguł synchronizacji** z menu **Start.**
3. Upewnij się, że **opcja Ruch przychodzący** jest zaznaczona, a następnie kliknij przycisk **Dodaj nową regułę**.
4. Nadaj regule nazwę opisową, na przykład "*W z ad — synchronizacja sprzedaży użytkownika*". Wybierz właściwy las, wybierz **opcję Użytkownik** jako typ **obiektu CS**i wybierz opcję **Osoba** jako typ **obiektu MV**. W **obszarze Typ łącza**wybierz pozycję **Dołącz**. W **polu Pierwszeństwo**wpisz wartość, która nie jest obecnie używana przez inną regułę synchronizacji (na przykład 51), a następnie kliknij przycisk **Dalej**.  
   ![Opis przychodzącego 4](./media/how-to-connect-sync-configure-filtering/inbound4.png)  
5. W **filtrze zakres kliknij**pozycję Dodaj **grupę**i kliknij pozycję **Dodaj klauzulę**. W **obszarze Atrybut**wybierz **dział**. Upewnij się, że operator jest ustawiony na **EQUAL**i wpisz wartość **Sprzedaż** w polu **Wartość.** Kliknij przycisk **alej**.  
   ![Zakres przychodzący 5](./media/how-to-connect-sync-configure-filtering/inbound5.png)  
6. Pozostaw reguły **dołączania** puste, a następnie kliknij przycisk **Dalej**.
7. Kliknij **pozycję Dodaj transformację**, wybierz pozycję **Stała** jako **typ przepływu**i wybierz **cloudFiltered** jako **atrybut docelowy**. W polu **Źródło** wpisz **fałsz**. Kliknij **przycisk Dodaj,** aby zapisać regułę.  
   ![Transformacja przychodząca 6](./media/how-to-connect-sync-configure-filtering/inbound6.png)  
   Jest to szczególny przypadek, w którym jawnie ustawić cloudFiltered do **False**.
8. Teraz musimy utworzyć regułę synchronizacji catch-all. Nadaj regule nazwę opisową, na przykład "*In from AD — User Catch-all filter*". Wybierz właściwy las, wybierz **opcję Użytkownik** jako typ **obiektu CS**i wybierz opcję **Osoba** jako typ **obiektu MV**. W **obszarze Typ łącza**wybierz pozycję **Dołącz**. W **polu Pierwszeństwo**wpisz wartość, która nie jest obecnie używana przez inną regułę synchronizacji (na przykład 99). Wybrano wartość pierwszeństwa, która jest wyższa (niższy priorytet) niż poprzednia reguła synchronizacji. Ale pozostało też trochę miejsca, dzięki czemu możesz dodać więcej reguł synchronizacji filtrowania później, gdy chcesz rozpocząć synchronizację dodatkowych działów. Kliknij przycisk **alej**.  
   ![Opis przychodzącego 7](./media/how-to-connect-sync-configure-filtering/inbound7.png)  
9. Pozostaw **filtr zakresu** pustym, a następnie kliknij przycisk **Dalej**. Pusty filtr wskazuje, że reguła ma być stosowana do wszystkich obiektów.
10. Pozostaw reguły **dołączania** puste, a następnie kliknij przycisk **Dalej**.
11. Kliknij **pozycję Dodaj transformację**, wybierz pozycję **Stała** jako **typ przepływu**i wybierz opcję **cloudFiltered** jako **atrybut docelowy**. W polu **Źródło** wpisz wartość **Prawda**. Kliknij **przycisk Dodaj,** aby zapisać regułę.  
    ![Transformacja przychodząca 3](./media/how-to-connect-sync-configure-filtering/inbound3.png)  
12. Aby ukończyć konfigurację, musisz uruchomić **pełną synchronizację**. Kontynuuj czytanie sekcji [Zastosuj i zweryfikuj zmiany](#apply-and-verify-changes).

W razie potrzeby można utworzyć więcej reguł pierwszego typu, w których do synchronizacji dołącza się więcej obiektów.

### <a name="outbound-filtering"></a>Filtrowanie wychodzące
W niektórych przypadkach konieczne jest, aby wykonać filtrowanie tylko po obiekty zostały połączone w metaverse. Na przykład może być konieczne przyjrzenie się atrybutowi poczty z lasu zasobów i atrybutu userPrincipalName z lasu kont, aby ustalić, czy obiekt powinien być zsynchronizowany. W takich przypadkach należy utworzyć filtrowanie reguły ruchu wychodzącego.

W tym przykładzie można zmienić filtrowanie tak, aby tylko użytkownicy, którzy @contoso.com mają zarówno ich poczty i userPrincipalName kończące się są synchronizowane:

1. Zaloguj się do serwera, na który jest uruchomiona synchronizacja usługi Azure AD Connect przy użyciu konta, które jest członkiem grupy zabezpieczeń **ADSyncAdmins.**
2. Uruchom **Edytor reguł synchronizacji** z menu **Start.**
3. W obszarze **Typ reguł**kliknij pozycję **Wychodzące**.
4. W zależności od używanej wersji connect znajdź regułę o nazwie Out to **AAD — User Join** or **Out to AAD — User Join SOAInAD**i kliknij przycisk **Edytuj**.
5. W wyskakującym okienku odpowiedz **tak,** aby utworzyć kopię reguły.
6. Na **opis** strony zmień **pierwszeństwo** na nieużywane wartości, takie jak 50.
7. Kliknij **pozycję Filtr zakresu** w nawigacji po lewej stronie, a następnie kliknij pozycję Dodaj **klauzulę**. W **obszarze Atrybut**wybierz pozycję **Poczta**. W **obszarze Operator**wybierz **endswith**. W **polu Wartość**wpisz ** \@contoso.com**, a następnie kliknij przycisk Dodaj **klauzulę**. W **obszarze Atrybut**wybierz pozycję **userPrincipalName**. W **obszarze Operator**wybierz **endswith**. W **polu Wartość**wpisz ** \@contoso.com**.
8. Kliknij przycisk **Zapisz**.
9. Aby ukończyć konfigurację, musisz uruchomić **pełną synchronizację**. Kontynuuj czytanie sekcji [Zastosuj i zweryfikuj zmiany](#apply-and-verify-changes).

## <a name="apply-and-verify-changes"></a>Stosowanie i weryfikowanie zmian
Po dokonaniu zmian konfiguracji należy zastosować je do obiektów, które są już obecne w systemie. Może być również, że obiekty, które nie są obecnie w aparat synchronizacji powinny być przetwarzane (i aparat synchronizacji musi ponownie odczytać system źródłowy, aby zweryfikować jego zawartość).

Jeśli konfiguracja została zmieniona przy użyciu filtrowania **domeny** lub **jednostki organizacyjnej,** należy wykonać **pełne importowanie,** a następnie **synchronizację delta**.

Jeśli konfiguracja została zmieniona przy użyciu filtrowania **atrybutów,** należy wykonać **pełną synchronizację**.

Wykonaj następujące czynności:

1. Uruchom **usługę synchronizacji** z menu **Start.**
2. Wybierz **łączniki**. Na liście **Łączniki** wybierz łącznik, w którym wcześniej dokonano zmiany konfiguracji. W **obszarze Akcje**wybierz pozycję **Uruchom**.  
   ![Uruchomienie złącza](./media/how-to-connect-sync-configure-filtering/connectorrun.png)  
3. W **obszarze Uruchom profile**wybierz operację wymienioną w poprzedniej sekcji. Jeśli musisz uruchomić dwie akcje, uruchom drugą po zakończeniu pierwszej. (Kolumna **Stan** jest **bezczynna** dla wybranego łącznika).

Po synchronizacji wszystkie zmiany są przemieszczane do wyeksportowania. Zanim faktycznie wprowadzić zmiany w usłudze Azure AD, chcesz sprawdzić, czy wszystkie te zmiany są poprawne.

1. Uruchom wiersz polecenia i `%ProgramFiles%\Microsoft Azure AD Sync\bin`przejdź do pliku .
2. Uruchom polecenie `csexport "Name of Connector" %temp%\export.xml /f:x`.  
   Nazwa łącznika znajduje się w usłudze synchronizacji. Ma nazwę podobną do "contoso.com — AAD" dla usługi Azure AD.
3. Uruchom polecenie `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`.
4. Masz teraz plik o nazwie %temp% o nazwie export.csv, który można zbadać w programie Microsoft Excel. Ten plik zawiera wszystkie zmiany, które mają zostać wyeksportowane.
5. Wprowadzać niezbędne zmiany w danych lub konfiguracji i uruchomić te kroki ponownie (Importuj, Synchronizuj i Weryfikuj), dopóki zmiany, które mają zostać wyeksportowane są oczekiwane.

Po zakończeniu eksportuj zmiany do usługi Azure AD.

1. Wybierz **łączniki**. Na liście **Łączniki** wybierz usługę Azure AD Connector. W **obszarze Akcje**wybierz pozycję **Uruchom**.
2. W **obszarze Uruchom profile**wybierz pozycję **Eksportuj**.
3. Jeśli zmiany konfiguracji usunąć wiele obiektów, a następnie zostanie wyświetlony błąd w eksporcie, gdy liczba jest większa niż skonfigurowany próg (domyślnie 500). Jeśli widzisz ten błąd, musisz tymczasowo wyłączyć funkcję "[zapobiegaj przypadkowym usuwaniom](how-to-connect-sync-feature-prevent-accidental-deletes.md)".

Teraz nadszedł czas, aby ponownie włączyć harmonogram.

1. Uruchom **Harmonogram zadań** z menu **Start.**
2. Bezpośrednio w obszarze **Biblioteka harmonogramu zadań**znajdź zadanie o nazwie **Harmonogram synchronizacji usługi Azure AD**, kliknij prawym przyciskiem myszy i wybierz polecenie **Włącz**.

## <a name="group-based-filtering"></a>Filtrowanie oparte na grupach
Filtrowanie oparte na grupach można skonfigurować przy pierwszej instalacji usługi Azure AD Connect przy użyciu [instalacji niestandardowej](how-to-connect-install-custom.md#sync-filtering-based-on-groups). Jest przeznaczony do wdrożenia pilotażowego, w którym chcesz zsynchronizować tylko niewielki zestaw obiektów. Po wyłączeniu filtrowania opartego na grupach nie można go ponownie włączyć. Nie jest *obsługiwane* używanie filtrowania opartego na grupach w konfiguracji niestandardowej. Jest obsługiwana tylko skonfigurować tę funkcję za pomocą kreatora instalacji. Po zakończeniu pilotażu użyj jednej z innych opcji filtrowania w tym temacie. W przypadku korzystania z filtrowania opartego na ujach w połączeniu z filtrem opartym na grupach należy uwzględnić jednostka organizacyjna, w której znajdują się grupy i jej członkowie.

Podczas synchronizowania wielu lasów usługi AD można skonfigurować filtrowanie oparte na grupach, określając inną grupę dla każdego łącznika usługi AD. Jeśli chcesz zsynchronizować użytkownika w jednym lesie usługi AD, a ten sam użytkownik ma jeden lub więcej odpowiednich obiektów w innych lasach usługi AD, należy upewnić się, że obiekt użytkownika i wszystkie odpowiadające mu obiekty znajdują się w zakresie filtrowania opartym na grupach. Przykłady:

* Użytkownik ma w jednym lesie, który ma odpowiedni FSP (Foreign Security Principal) obiektu w innym lesie. Oba obiekty muszą znajdować się w zakresie filtrowania opartego na grupach. W przeciwnym razie użytkownik nie zostanie zsynchronizowany z usługą Azure AD.

* Użytkownik ma w jednym lesie, który ma odpowiednie konto zasobów (np. połączona skrzynka pocztowa) w innym lesie. Ponadto skonfigurowano usługę Azure AD Connect, aby połączyć użytkownika z kontem zasobu. Oba obiekty muszą znajdować się w zakresie filtrowania opartego na grupach. W przeciwnym razie użytkownik nie zostanie zsynchronizowany z usługą Azure AD.

* Użytkownik w jednym lesie ma odpowiedni kontakt pocztowy w innym lesie. Ponadto skonfigurowano usługę Azure AD Connect, aby połączyć użytkownika z kontaktem poczty. Oba obiekty muszą znajdować się w zakresie filtrowania opartego na grupach. W przeciwnym razie użytkownik nie zostanie zsynchronizowany z usługą Azure AD.


## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o konfiguracji [synchronizacji usługi Azure AD Connect.](how-to-connect-sync-whatis.md)
- Dowiedz się więcej o [integrując tożsamości lokalne z usługą Azure AD.](whatis-hybrid-identity.md)
