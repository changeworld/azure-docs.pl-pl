---
title: 'Synchronizacja programu Azure AD Connect: Konfiguracja synchronizacji Azure AD Connect | Dokumentacja firmy Microsoft'
description: Przedstawiono sposób zmiany w konfiguracji synchronizacji Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 7b9df836-e8a5-4228-97da-2faec9238b31
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: bad1cbe0b142e146ada28f2af5d152973100e919
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34595108"
---
# <a name="azure-ad-connect-sync-make-a-change-to-the-default-configuration"></a>Synchronizacja programu Azure AD Connect: zmiany w konfiguracji domyślnej
Celem tego artykułu jest aby zademonstrować sposób wprowadzania zmian w domyślnej konfiguracji synchronizacji usługi Azure Active Directory (Azure AD) Connect. Ona instrukcje dla niektórych typowych scenariuszy. Z tym wiedzy należy wprowadzać proste zmiany własnych konfiguracją na podstawie własnych reguł biznesowych.

## <a name="synchronization-rules-editor"></a>Edytor reguł synchronizacji
Edytor reguł synchronizacji jest używany do Zobacz i zmienianie konfiguracji domyślnej. Można znaleźć na **Start** menu w obszarze **Azure AD Connect** grupy.  
![Edytor reguł synchronizacji z menu Start](./media/active-directory-aadconnectsync-change-the-configuration/startmenu2.png)

Po otwarciu edytora wyświetlany domyślnych reguł out-of-box.

![Edytor reguł synchronizacji](./media/active-directory-aadconnectsync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>Nawigacja w edytorze
Przy użyciu listy rozwijane w górnej części edytora, można szybko znaleźć określonej reguły. Na przykład jeśli chcesz wyświetlić reguły, gdzie znajduje się atrybut proxyAddresses, można zmienić listach rozwijanych do następującego:  
![Filtrowanie SRE](./media/active-directory-aadconnectsync-change-the-configuration/filtering.png)  
Aby zresetować filtrowania i załadować świeże konfiguracji, na klawiaturze naciśnij klawisz F5.

W prawym górnym rogu jest **Dodaj nową regułę** przycisku. Ten przycisk służy do tworzenia reguły niestandardowej.

Na dole to przyciski działanie na podstawie reguły synchronizacji wybrane. **Edytuj** i **usunąć** oczekiwaniami ich. **Eksportuj** tworzy skrypt programu PowerShell dotyczących ponownego tworzenia reguły synchronizacji. Z tej procedury można przenieść regułę synchronizacji z jednego serwera na inny.

## <a name="create-your-first-custom-rule"></a>Tworzenie pierwszej reguły niestandardowe
Najbardziej typowe zmiany są przepływów atrybutów. Dane w katalogu źródłowym nie może być taki sam jak Azure AD. W przykładzie w tej sekcji, upewnij się, jest zawsze o podanej nazwie użytkownika *przypadku prawidłowego*.

### <a name="disable-the-scheduler"></a>Wyłączanie harmonogramu
[Harmonogramu](active-directory-aadconnectsync-feature-scheduler.md) domyślnie uruchamiane co 30 minut. Upewnij się, że nie jest uruchomiona podczas wprowadzania zmian i rozwiązywanie problemów z nowych zasad. Aby tymczasowo wyłączyć harmonogram, uruchom program PowerShell i uruchom `Set-ADSyncScheduler -SyncCycleEnabled $false`.

![Wyłączanie harmonogramu](./media/active-directory-aadconnectsync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>Utworzyć regułę
1. Kliknij przycisk **Dodaj nową regułę**.
2. Na **opis** strony, wprowadź następujące:  
   ![Liczba przychodzących reguł filtrowania](./media/active-directory-aadconnectsync-change-the-configuration/description2.png)  
   * **Nazwa**: Nadaj regule nazwę opisową.
   * **Opis elementu**: podać niektóre wyjaśnienie, ktoś inny może zrozumieć, co to jest reguła dla.
   * **Połączony System**: jest to system, w którym można znaleźć obiektu. W takim przypadku wybierz **łącznika usługi Active Directory**.
   * **Typ obiektu systemu/Metaverse podłączonej**: Wybierz **użytkownika** i **osoby**odpowiednio.
   * **Typ łącza**: Zmień tę wartość na **Join**.
   * **Pierwszeństwo**: Podaj wartość, która jest unikatowa w systemie. Niższa wartość liczbowa oznacza wyższy priorytet.
   * **Tag**: to pole pozostanie puste. Tylko reguły out-of-box firmy Microsoft powinna mieć to pole wypełniane przy użyciu wartości.
3. Na **filtru Scoping** wprowadź **givenName ISNOTNULL**.  
   ![Reguła filtr zakresów ruchu przychodzącego](./media/active-directory-aadconnectsync-change-the-configuration/scopingfilter.png)  
   W tej sekcji służy do definiowania do obiekty, które ma zostać zastosowana reguła. Gdy zostanie pozostawiony pusty, reguła powinna zostać zastosowana do wszystkich obiektów użytkowników. Jednak Obejmowałoby to sal konferencyjnych, kont usług i innych obiektów użytkownika z systemem innym niż osób.
4. Na **dołączyć reguły** pozostaw pole puste.
5. Na **przekształcenia** Zmień **dla przepływu** do **wyrażenia**. Aby uzyskać **atrybut Target**, wybierz pozycję **givenName**. I **źródła**, wprowadź **PCase([givenName])**.
   ![Reguła ruchu przychodzącego przekształcenia](./media/active-directory-aadconnectsync-change-the-configuration/transformations.png)  
   Aparat synchronizacji jest rozróżniana wielkość liter, zarówno nazwę funkcji, jak i nazwę atrybutu. Jeśli wpiszesz problem, zobacz ostrzeżenie podczas dodawania reguły. Można zapisywać i można kontynuować, ale musisz ponownie i Popraw regułę.
6. Kliknij przycisk **Dodaj** można zapisać reguły.

Nowe reguły niestandardowe powinny być widoczne z innych reguł synchronizacji w systemie.

### <a name="verify-the-change"></a>Sprawdź zmiany
Dzięki tej zmianie nowego chcesz upewnij się, że działa zgodnie z oczekiwaniami i nie jest zgłaszanie błędów. W zależności od liczby obiektów, do których masz istnieją dwa sposoby, aby wykonać ten krok:

- Uruchom pełną synchronizację wszystkich obiektów.
- Uruchom Podgląd i pełną synchronizację w jeden obiekt.

Otwórz **usługi synchronizacji** z **Start** menu. Kroki opisane w tej sekcji są w tego narzędzia.

**Pełna synchronizacja wszystkich obiektów**  

   1. Wybierz **łączniki** u góry. Identyfikacja łącznika, który został zmodyfikowany w poprzedniej sekcji (w tym przypadku Active Directory Domain Services) i zaznacz go. 
   2. Aby uzyskać **akcje**, wybierz pozycję **Uruchom**.
   3. Wybierz **pełną synchronizację**, a następnie wybierz **OK**.
   ![Pełna synchronizacja](./media/active-directory-aadconnectsync-change-the-configuration/fullsync.png)  
   Obiekty są teraz zaktualizować w magazynie metaverse. Sprawdź wprowadzone zmiany, analizując obiektu w magazynie metaverse.

**Podgląd i pełną synchronizację dla pojedynczego obiektu**  

   1. Wybierz **łączniki** u góry. Identyfikacja łącznika, który został zmodyfikowany w poprzedniej sekcji (w tym przypadku Active Directory Domain Services) i zaznacz go.
   2. Wybierz **wyszukiwania przestrzeni łącznika**. 
   3. Użyj **zakres** można znaleźć obiektu, którego chcesz używać do testowania zmiany. Wybierz obiekt i kliknij przycisk **Podgląd**. 
   4. Na nowym ekranie Wybierz **zatwierdzić Podgląd**.  
   ![Zatwierdź podglądu](./media/active-directory-aadconnectsync-change-the-configuration/commitpreview.png)  
   Zmiana teraz przywiązuje dużą wagę do środowiska metaverse.

**Widok obiektu w magazynie metaverse**  

1. Wybierz kilka przykładowych obiektów oczekiwano wartość i reguła zastosowana. 
2. Wybierz **wyszukiwanie Metaverse** od góry. Dodaj dowolny filtr, który należy odnaleźć odpowiednich obiektów. 
3. W wyniku wyszukiwania Otwórz obiektu. Przyjrzyj się wartości atrybutów, a także Sprawdź, czy w **reguły synchronizacji** kolumny, która jest stosowana reguła zgodnie z oczekiwaniami.  
![Wyszukiwanie magazynu metaverse](./media/active-directory-aadconnectsync-change-the-configuration/mvsearch.png)  

### <a name="enable-the-scheduler"></a>Włącz harmonogram
Jeśli wszystko, co jest zgodne z oczekiwaniami, możesz włączyć ponownie harmonogramu. Z programu PowerShell, uruchom `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="other-common-attribute-flow-changes"></a>Inne typowe zmiany przepływu atrybutów
Poprzedniej sekcji opisano sposób wprowadzania zmian do przepływu atrybutu. W tej sekcji znajdują się dodatkowe przykłady. Kroki dotyczące sposobu tworzenia reguły synchronizacji jest skrócona, ale pełny procedurę można znaleźć w poprzedniej sekcji.

### <a name="use-an-attribute-other-than-the-default"></a>Użyj atrybutu innego niż domyślny
W tym scenariuszu firma Fabrikam jest lasu, gdzie są używane lokalne alfabetu imię, nazwisko i nazwę wyświetlaną. Zestaw znaków łacińskich reprezentację te atrybuty można znaleźć w atrybutach rozszerzenia. Do tworzenia adresu globalnego listę w usłudze Azure AD i Office 365, organizacja chce użyć tych atrybutów.

Z konfiguracji domyślnej obiekt z lokalnego lasu wygląda następująco:  
![Przepływ atrybutów 1](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp1.png)

Aby utworzyć regułę z innych przepływów atrybutów, wykonaj następujące czynności:

1. Otwórz **Edytor reguł synchronizacji** z **Start** menu.
2. Z **przychodzący** wybrane do lewej strony, kliknij przycisk **Dodaj nową regułę** przycisku.
3. Nadaj regule nazwę i opis. Wybierz lokalne wystąpienie usługi Active Directory i typy odpowiednich obiektów. W **typu łącza**, wybierz pozycję **Join**. Aby uzyskać **pierwszeństwo**, wybierz numer, który nie jest używany przez inną regułę. Reguły out-of-box rozpoczyna się od 100, dlatego wartość 50 może być używana w tym przykładzie.
  ![Przepływ atrybutu 2](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp2.png)
4. Pozostaw **filtru Scoping** puste. (To znaczy go należy zastosować wszystkie obiekty użytkowników w lesie.)
5. Pozostaw **dołączyć reguły** puste. (Oznacza to, umożliwiają reguły out-of-box obsługi wszelkie sprzężenia).
6. W **przekształcenia**, utwórz następujący przepływów:  
  ![Przepływ atrybutu 3](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp3.png)
7. Kliknij przycisk **Dodaj** można zapisać reguły.
8. Przejdź do **Menedżera usługi synchronizacji**. Na **łączniki**, wybierz łącznik, gdzie dodana reguła. Wybierz **Uruchom**, a następnie wybierz **pełnej synchronizacji**. Pełna synchronizacja ponownie oblicza wszystkie obiekty przy użyciu bieżące reguły.

Jest to wynik dla tego samego obiektu przy użyciu tej reguły niestandardowe:  
![Przepływ atrybutu 4](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>Długość atrybutów
Ciąg atrybuty można indeksować domyślnie, a maksymalna długość to 448 znaków. Jeśli pracujesz z atrybutami ciągów, które mogą zawierać więcej upewnij się, że są następujące przepływu atrybutu:  
`attributeName` <- `Left([attributeName],448)`.

### <a name="changing-the-userprincipalsuffix"></a>Zmiana userPrincipalSuffix
Atrybut userPrincipalName w usłudze Active Directory nie jest znany zawsze przez użytkowników i mogą nie być odpowiednie jako identyfikator logowania. Przy użyciu Kreatora instalacji synchronizacji Azure AD Connect, można wybrać inny atrybut — na przykład *poczty*. Jednak w niektórych przypadkach atrybutu muszą zostać obliczone.

Na przykład firma Contoso ma dwa katalogi usługi Azure AD, co w środowisku produkcyjnym i jeden do testowania. Mają one użytkowników w swojej dzierżawy testu, aby użyć innego sufiksu w identyfikator logowania:  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`.

W tym wyrażeniu zająć wszystko po lewej stronie pierwszego @-sign (Word) i ZŁĄCZ.teksty z ciągiem stałym.

### <a name="convert-a-multi-value-attribute-to-single-value"></a>Konwertuj atrybutu wielowartościowego na pojedynczą wartość
Niektóre atrybuty w usłudze Active Directory są wielowartościowe w schemacie, mimo że wyglądają jednowartościowych użytkownicy usługi Active Directory i komputery. Przykładem jest atrybut Opis:  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`.

W tym wyrażeniu, jeśli ten atrybut ma wartość, wykonać pierwszy element (*elementu*) w atrybucie, usunąć spacje początkowe i końcowe (*Trim*), a następnie zapewnić najpierw 448 znaków (*lewej* ) w ciągu.

### <a name="do-not-flow-an-attribute"></a>Nie przepływu atrybutu
W tle na scenariusz dla tej sekcji, zobacz [kontrolować proces przepływu atrybutu](active-directory-aadconnectsync-understanding-declarative-provisioning.md#control-the-attribute-flow-process).

Istnieją dwa sposoby nie przepływu atrybutu. Pierwsza to przy użyciu Kreatora instalacji, aby [Usuń wybranych atrybutów](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering). Ta opcja działa, jeśli atrybut przed nigdy nie zostały zsynchronizowane. Jeśli używasz zsynchronizować ten atrybut i usuń go później przy użyciu tej funkcji, zatrzymuje aparatu synchronizacji Zarządzanie atrybutu i istniejące wartości są pozostawiane w usłudze Azure AD.

Jeśli chcesz usunąć wartość atrybutu i upewnij się, że nie przepływu w przyszłości, należy utworzyć regułę niestandardową.

W tym scenariuszu firma Fabrikam mają uświadomiliśmy sobie, że niektóre atrybuty, które firma Microsoft synchronizować do chmury nie będą już istnieje. Chcemy upewnić się, że te atrybuty są usuwane z usługi Azure AD.  
![Złe rozszerzenie atrybutów](./media/active-directory-aadconnectsync-change-the-configuration/badextensionattribute.png)

1. Utwórz nową regułę synchronizacji ruchu przychodzącego i wypełnić opisu.
  ![Opisy](./media/active-directory-aadconnectsync-change-the-configuration/syncruledescription.png)
2. Tworzenie przepływów atrybutów z **wyrażenie** dla **dla przepływu** i **AuthoritativeNull** dla **źródła**. Literał **AuthoritativeNull** wskazuje, że wartość powinna być pusta w magazynie metaverse, nawet wtedy, gdy reguła synchronizacji niższego pierwszeństwa próbuje wypełnić wartości.
  ![Przekształcania atrybuty rozszerzenia](./media/active-directory-aadconnectsync-change-the-configuration/syncruletransformations.png)
3. Zapisz reguły synchronizacji. Uruchom **usługi synchronizacji**, znajdź łącznik, wybierz **Uruchom**, a następnie wybierz **pełnej synchronizacji**. Ten krok ponownie oblicza wszystkie przepływy atrybutów.
4. Sprawdź, czy zamierzone zmiany do wyeksportowania wyszukując przestrzeni łącznika.
  ![Usuń przemieszczanego](./media/active-directory-aadconnectsync-change-the-configuration/deletetobeexported.png)

## <a name="create-rules-with-powershell"></a>Tworzenie reguł przy użyciu programu PowerShell
Za pomocą edytora reguły synchronizacji działa prawidłowo, jeśli masz tylko wprowadzić kilka zmian. Jeśli potrzebujesz wprowadzania zmian, programu PowerShell może być lepszym rozwiązaniem. Niektóre zaawansowane funkcje są dostępne tylko przy użyciu programu PowerShell.

### <a name="get-the-powershell-script-for-an-out-of-box-rule"></a>Pobierz skrypt programu PowerShell dla reguły out-of-box
Aby wyświetlić programu PowerShell skryptu, który utworzył regułę out-of-box, wybierz regułę w edytorze reguły synchronizacji i kliknij przycisk **wyeksportować**. Ta akcja umożliwia skrypt programu PowerShell, który utworzył regułę.

### <a name="advanced-precedence"></a>Pierwszeństwo zaawansowane
Reguły synchronizacji out-of-box rozpoczynać się od wartości pierwszeństwo 100. Jeśli masz wiele lasów i które należy podjąć wiele zmian niestandardowych, następnie reguły synchronizacji 99 może nie być wystarczającej ilości.

Możesz wydać aparatu synchronizacji, która ma dodatkowe zasady wstawiony przed regułami out-of-box. Aby uzyskać ten problem, wykonaj następujące kroki:

1. Oznacz pierwszej reguły synchronizacji out-of-box (**w od użytkownika AD Join**) w edytorze reguły synchronizacji i wybierz **wyeksportować**. Skopiuj wartość identyfikatora SR.  
![PowerShell przed zmianą](./media/active-directory-aadconnectsync-change-the-configuration/powershell1.png)  
2. Utwórz nową regułę synchronizacji. Edytor reguł synchronizacji służy do jej utworzenia. Eksportuj zasady do skryptu programu PowerShell.
3. We właściwości **PrecedenceBefore**, Wstaw wartość identyfikatora z reguły out-of-box. Ustaw **pierwszeństwo** do **0**. Upewnij się, atrybut Identyfikator jest unikatowy i że nie ponownego użycia identyfikatora GUID z inną regułą. Ponadto upewnij się, że **ImmutableTag** nie ustawiono właściwości. Tej właściwości należy ustawić tylko dla reguły out-of-box.
4. Zapisz skrypt programu PowerShell i uruchom go. Wynik jest niestandardowe reguły jest przypisywana wartość priorytetu 100, a wszystkie inne zasady out-of-box jest zwiększany.  
![PowerShell po zmianie](./media/active-directory-aadconnectsync-change-the-configuration/powershell2.png)  

Można utworzyć wiele reguł synchronizacji niestandardowych korzystającej z tego samego **PrecedenceBefore** wartości w razie potrzeby.

## <a name="enable-synchronization-of-usertype"></a>Włącz synchronizację UserType
Azure AD Connect obsługuje synchronizacji **UserType** atrybutu dla **użytkownika** obiektów w wersji 1.1.524.0 i nowszych. W szczególności wprowadzono następujące zmiany:

- Schemat typu obiektu **użytkownika** w programie Azure AD Connector jest rozszerzona, aby uwzględnić atrybut UserType, który jest typu string i jest pojedynczej wartości.
- Schemat typu obiektu **osoby** w magazynie metaverse jest rozszerzona, aby uwzględnić atrybut UserType, który jest typu string i jest pojedynczej wartości.

Domyślnie ten atrybut UserType nie włączono synchronizacji, ponieważ w lokalnej usłudze Active Directory nie istnieje odpowiedni atrybut UserType. Musisz ręcznie włączyć synchronizację. Przed wykonaniem tego należy wykonać należy wziąć pod uwagę następujące zachowanie wymuszane przez usługę Azure AD:

- Usługi Azure AD akceptuje tylko dwie wartości dla atrybutu UserType: **elementu członkowskiego** i **gościa**.
- Jeśli atrybut UserType nie jest włączone dla synchronizacji w programie Azure AD Connect, został utworzony za pomocą synchronizacji katalogów użytkowników usługi Azure AD musi ustawić atrybut UserType **elementu członkowskiego**.
- Usługi Azure AD nie zezwala na atrybut UserType na istniejących użytkowników usługi Azure AD zostanie zmieniony przez program Azure AD Connect. Można można ustawić tylko podczas tworzenia użytkowników usługi Azure AD.

Przed włączeniem synchronizacji atrybut UserType, najpierw należy zdecydować, jak ten atrybut pochodzi z lokalnej usługi Active Directory. Najbardziej typowe podejścia są następujące:

- Wyznacz nieużywane lokalnymi atrybutów AD (na przykład extensionAttribute1) ma być używany jako atrybutu źródłowego. Wyznaczone AD atrybut powinien być typu lokalnymi **ciąg**, być jednowartościowe i zawierają wartość **elementu członkowskiego** lub **gościa**. 

    Jeśli wybierzesz takie podejście, musi zapewnić wyznaczonych atrybutu jest wypełniana poprawną wartość dla wszystkich istniejących obiektów użytkowników w lokalnej usłudze Active Directory, które są synchronizowane z usługą Azure AD przed włączeniem synchronizacji atrybut UserType .

- Alternatywnie wartość atrybut UserType może pochodzić od innych właściwości. Na przykład chcesz synchronizować wszystkich użytkowników jako **gościa** Jeśli lokalną atrybutu userPrincipalName AD kończy się wraz z części domenowej *@partners.fabrikam123.org*. 

    Jak wspomniano wcześniej, Azure AD Connect nie zezwala na atrybut UserType na istniejących użytkowników usługi Azure AD zostanie zmieniony przez program Azure AD Connect. W związku z tym upewnij się, że logika ustalony jest zgodna z jak atrybut UserType został już skonfigurowany dla wszystkich istniejących użytkowników usługi Azure AD w dzierżawie.

Kroki umożliwiające włączenie synchronizacji atrybut UserType można podsumować jako:

1.  Wyłączanie harmonogramu synchronizacji i sprawdź, że nie ma synchronizacji w toku.
2.  Dodaj atrybut źródłowy do lokalnej schematu w łączniku usługi AD.
3.  Dodaj UserType schematu łącznika usługi Azure AD.
4.  Utwórz regułę synchronizacji ruchu przychodzącego na przepływ wartości atrybutu z lokalnej usługi Active Directory.
5.  Utwórz regułę synchronizacji ruchu wychodzącego przepływu wartość atrybutu do usługi Azure AD.
6.  Uruchom pełną synchronizację cyklu.
7.  Włącz harmonogram synchronizacji.

>[!NOTE]
> Pozostałej części tej sekcji omówiono następujące kroki. Zostały one opisane w kontekście wdrożenia usługi Azure AD z topologii z jednym lasem i bez reguł niestandardowych. Jeśli masz topologią wielu lasów, reguły synchronizacji niestandardowych skonfigurowana lub serwer przemieszczania, należy odpowiednio zmienić kroki.

### <a name="step-1-disable-the-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Krok 1: Wyłącz harmonogramu synchronizacji i sprawdź, że nie ma synchronizacji w toku
Aby uniknąć eksportowanie niepożądanych zmian do usługi Azure AD, upewnij się, że synchronizacja nie ma miejsce, gdy trwa aktualizowanie reguł synchronizacji. Aby wyłączyć harmonogram synchronizacji wbudowany:

 1. Uruchom sesję programu PowerShell na serwerze programu Azure AD Connect.
 2. Wyłącz zaplanowanej synchronizacji, uruchamiając polecenie cmdlet `Set-ADSyncScheduler -SyncCycleEnabled $false`.
 3. Otwórz Menedżera usługi synchronizacji, przechodząc do **Start** > **usługi synchronizacji**.
 4. Przejdź do **operacji** karcie i upewnij się, Brak operacji ze stanem *w toku*.

### <a name="step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema"></a>Krok 2: Dodaj atrybut źródłowy do lokalnej schematu w łączniku AD
Nie wszystkie atrybuty usługi Azure AD są importowane do lokalnej przestrzeni łącznika usługi AD. Aby dodać atrybut źródłowy do listy importowanych atrybuty:

 1. Przejdź do **łączniki** kartę Menedżera usługi synchronizacji.
 2. Kliknij prawym przyciskiem myszy lokalnej usługi AD łącznik i wybierz **właściwości**.
 3. W wyświetlonym oknie dialogowym, przejdź do **wybierz atrybuty** kartę.
 4. Upewnij się, że atrybut źródłowy jest zaznaczona na liście atrybutów.
 5. Kliknij przycisk **OK** do zapisania.
![Dodaj atrybut źródłowy do lokalnego schematu w łączniku AD](./media/active-directory-aadconnectsync-change-the-configuration/usertype1.png)

### <a name="step-3-add-the-usertype-to-the-azure-ad-connector-schema"></a>Krok 3: Dodawanie UserType schematu łącznika usługi Azure AD
Domyślnie ten atrybut UserType nie zostanie zaimportowane do platformy Azure AD Connect miejsca. Dodaj atrybut UserType na liście atrybutów zaimportowane:

 1. Przejdź do **łączniki** kartę Menedżera usługi synchronizacji.
 2. Kliknij prawym przyciskiem myszy **łącznika usługi Azure AD** i wybierz **właściwości**.
 3. W wyświetlonym oknie dialogowym, przejdź do **wybierz atrybuty** kartę.
 4. Upewnij się, że zaznaczono atrybut UserType na liście atrybutów.
 5. Kliknij przycisk **OK** do zapisania.

![Dodaj atrybut źródłowy do schematu łącznika usługi Azure AD](./media/active-directory-aadconnectsync-change-the-configuration/usertype2.png)

### <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>Krok 4: Utwórz regułę synchronizacji ruchu przychodzącego na przepływ wartości atrybutu z lokalnej usługi Active Directory
Reguła synchronizacji ruchu przychodzącego zezwala na wartość atrybutu mogą przepływać z atrybutu źródłowego z lokalnej usługi Active Directory do środowiska metaverse:

1. Otwórz Edytor reguł synchronizacji, przechodząc do **Start** > **Edytor reguł synchronizacji**.
2. Ustaw filtr wyszukiwania **kierunek** jako **przychodzący**.
3. Kliknij przycisk **Dodaj nową regułę** przycisk, aby utworzyć nową regułę ruchu przychodzącego.
4. W obszarze **opis** karcie, podaj następującej konfiguracji:

    | Atrybut | Wartość | Szczegóły |
    | --- | --- | --- |
    | Name (Nazwa) | *Podaj nazwę* | Na przykład *w z usługi Active Directory — UserType użytkownika* |
    | Opis | *Podaj opis* |  |
    | System połączony | *Wybierz lokalną łączniku AD* |  |
    | Połączony System typu obiektu | **Użytkownika** |  |
    | Typ obiektu Metaverse | **Osoby** |  |
    | Typ łącza | **Dołącz** |  |
    | Pierwszeństwo | *Wybierz liczbę z zakresu od 1 – 99* | 1 – 99 jest zarezerwowana dla reguły synchronizacji niestandardowych. Odbiera wartość, która jest używana przez inną regułę synchronizacji. |

5. Przejdź do **filtru Scoping** karta i Dodaj **pojedynczej grupy ustalania zakresu filtru** z następującą klauzulą:

    | Atrybut | Operator | Wartość |
    | --- | --- | --- |
    | adminDescription | NOTSTARTWITH | Użytkownika\_ |

    Filtr zakresu określa, do których lokalnego AD obiekty się, że jest stosowana ta reguła synchronizacji ruchu przychodzącego. W tym przykładzie używamy tego samego zakresu filtru używane w *w z usługi Active Directory — typowe użytkownika* reguły synchronizacji out-of-box, co zapobiega reguły synchronizacji są stosowane do obiektów użytkownika została utworzona za pośrednictwem usługi Azure AD użytkownika Funkcja zapisywania zwrotnego. Może być konieczne dostosowanie zakresu filtru zgodnie z wdrożenia usługi Azure AD Connect.

6. Przejdź do **przekształcania** karcie i wdrożenie reguły przekształcania żądany. Na przykład, jeśli określono nieużywane lokalną atrybutu AD (na przykład extensionAttribute1) jako atrybut źródłowy dla UserType, można zaimplementować przepływu atrybutu bezpośrednie:

    | Typ przepływu | Atrybut docelowy | Element źródłowy | Zastosuj raz | Scal typu |
    | --- | --- | --- | --- | --- |
    | Bezpośrednie | UserType | extensionAttribute1 | Unchecked | Aktualizacja |

    W kolejnym przykładzie mają pochodzi wartość atrybut UserType od innych właściwości. Na przykład chcesz synchronizować wszystkich użytkowników jako Gość, jeśli ich lokalne atrybutu userPrincipalName AD kończy się wraz z części domenowej *@partners.fabrikam123.org*. Można zaimplementować wyrażenia następująco:

    | Typ przepływu | Atrybut docelowy | Element źródłowy | Zastosuj raz | Scal typu |
    | --- | --- | --- | --- | --- |
    | Bezpośrednie | UserType | IIf(IsPresent([userPrincipalName]),IIf(CBool(InStr(LCase([userPrincipalName])"@partners.fabrikam123.org")=0) "Elementu członkowskiego", "Gość"), błąd ("UserPrincipalName nie jest obecny w celu określenia UserType")) | Unchecked | Aktualizacja |

7. Kliknij przycisk **Dodaj** można utworzyć reguły dla ruchu przychodzącego.

![Utwórz regułę synchronizacji ruchu przychodzącego](./media/active-directory-aadconnectsync-change-the-configuration/usertype3.png)

### <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>Krok 5: Tworzenie reguły synchronizacji wychodzący przepływ wartość atrybutu do usługi Azure AD
Reguła synchronizacji ruchu wychodzącego zezwala na wartość atrybutu mogą przepływać z metaverse atrybut UserType w usłudze Azure AD:

1. Przejdź do edytora reguły synchronizacji.
2. Ustaw filtr wyszukiwania **kierunek** jako **wychodzące**.
3. Kliknij przycisk **Dodaj nową regułę** przycisku.
4. W obszarze **opis** karcie, podaj następującej konfiguracji:

    | Atrybut | Wartość | Szczegóły |
    | ----- | ------ | --- |
    | Name (Nazwa) | *Podaj nazwę* | Na przykład *Out do usługi AAD — UserType użytkownika* |
    | Opis | *Podaj opis* ||
    | System połączony | *Wybierz łącznik AAD* ||
    | Połączony System typu obiektu | **Użytkownika** ||
    | Typ obiektu Metaverse | **Osoby** ||
    | Typ łącza | **Dołącz** ||
    | Pierwszeństwo | *Wybierz liczbę z zakresu od 1 – 99* | 1 – 99 jest zarezerwowana dla reguły synchronizacji niestandardowych. Odbiera wartość, która jest używana przez inną regułę synchronizacji. |

5. Przejdź do **filtru Scoping** karta i Dodaj **pojedynczej grupy ustalania zakresu filtru** z klauzulami dwóch:

    | Atrybut | Operator | Wartość |
    | --- | --- | --- |
    | sourceObjectType | RÓWNOŚCI | Użytkownik |
    | cloudMastered | NOTEQUAL | True |

    Filtr zakresu określa, do którego usługi Azure AD obiekty się, że jest stosowana ta reguła synchronizacji ruchu wychodzącego. W tym przykładzie używamy tego samego zakresu filtru z *do AD — tożsamości użytkownika* reguły synchronizacji out-of-box. Reguła synchronizacji uniemożliwia są stosowane do obiektów użytkowników, które nie są synchronizowane z lokalnej usługi Active Directory. Może być konieczne dostosowanie zakresu filtru zgodnie z wdrożenia usługi Azure AD Connect.

6. Przejdź do **przekształcania** karcie i implementuje następującą regułę przekształcania:

    | Typ przepływu | Atrybut docelowy | Element źródłowy | Zastosuj raz | Scal typu |
    | --- | --- | --- | --- | --- |
    | Bezpośrednie | UserType | UserType | Unchecked | Aktualizacja |

7. Kliknij przycisk **Dodaj** Tworzenie reguły ruchu wychodzącego.

![Utwórz regułę synchronizacji ruchu wychodzącego](./media/active-directory-aadconnectsync-change-the-configuration/usertype4.png)

### <a name="step-6-run-a-full-synchronization-cycle"></a>Krok 6: Uruchomienie pełnej synchronizacji cyklu
Ogólnie rzecz biorąc cyklu Pełna synchronizacja jest wymagana, ponieważ firma Microsoft ma dodaje nowe atrybuty do usługi Active Directory, jak i łącznik usługi Azure AD schematów i wprowadzono reguł niestandardowych. Chcesz zweryfikować zmiany przed wykonaniem operacji eksportowania ich do usługi Azure AD. 

Aby zweryfikować zmiany podczas uruchamiania ręcznie czynności, które tworzą cyklu pełnej synchronizacji, można użyć następujące kroki.

1. Uruchom **pełny import** na **lokalnego łącznika AD**:

   1. Przejdź do **operacji** kartę Menedżera usługi synchronizacji.
   2. Kliknij prawym przyciskiem myszy **lokalnego łącznika AD** i wybierz **Uruchom**.
   3. W wyświetlonym oknie dialogowym, wybierz **pełny Import** , a następnie kliknij przycisk **OK**.
   4. Poczekaj na zakończenie operacji.

    > [!NOTE]
    > Pełny import można pominąć na lokalnej łączniku usługi AD, jeśli atrybut źródłowy jest już na liście zaimportowane atrybutów. Innymi słowy, użytkownik nie miał wprowadzać żadnych zmian podczas [krok 2: Dodaj atrybut źródłowy do lokalnej schematu łącznika AD](#step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema).

2. Uruchom **pełny import** na **łącznika usługi Azure AD**:

   1. Kliknij prawym przyciskiem myszy **łącznika usługi Azure AD** i wybierz **Uruchom**.
   2. W wyświetlonym oknie dialogowym, wybierz **pełny Import** , a następnie kliknij przycisk **OK**.
   3. Poczekaj na zakończenie operacji.

3. Sprawdź zmian reguły synchronizacji do istniejącego obiektu użytkownika:

    Atrybut źródłowy z lokalna Usługa Active Directory i UserType z usługi Azure AD zostały zaimportowane do ich odpowiednich obszary łączników. Przed kontynuowaniem pełną synchronizację, wykonaj **Podgląd** na istniejącego użytkownika obiektu w lokalnej przestrzeni łącznika usługi AD. Obiekt, który wybrano ma atrybut źródłowy wypełnione.
    
    Pomyślnie **Podgląd** z UserType wypełnione w magazynie metaverse jest dobry wskaźnik skonfigurowano synchronizacji zasady poprawnie. Aby uzyskać informacje na temat **Podgląd**, zapoznaj się z rozdziałem [zweryfikować zmianę](#verify-the-change).

4. Uruchom **pełnej synchronizacji** na **on-premises AD Connector**:

   1. Kliknij prawym przyciskiem myszy **lokalnego łącznika AD** i wybierz **Uruchom**.
   2. W wyświetlonym oknie dialogowym, wybierz **pełną synchronizację** , a następnie kliknij przycisk **OK**.
   3. Poczekaj na zakończenie operacji.

5. Sprawdź **oczekujące operacje eksportu** do usługi Azure AD:

   1. Kliknij prawym przyciskiem myszy **łącznika usługi Azure AD** i wybierz **przestrzeni łącznika wyszukiwania**.
   2. W **przestrzeni łącznika wyszukiwania** podręczne okno dialogowe:

      - Ustaw **zakres** do **oczekujących eksportu**.
      - Zaznacz wszystkie trzy pola wyboru: **Dodaj**, **Modyfikuj**, i **usunąć**.
      - Kliknij przycisk **wyszukiwania** przycisk, aby uzyskać listę obiektów ze zmianami do wyeksportowania. Aby sprawdzić zmiany dla danego obiektu, kliknij dwukrotnie obiekt.
      - Upewnij się, że zmiany zostaną wprowadzone.

6. Uruchom **wyeksportować** na **Azure AD Connector**:

   1. Kliknij prawym przyciskiem myszy **łącznika usługi Azure AD** i wybierz **Uruchom**.
   2. W **uruchomić łącznik** podręcznym wybierz pozycję **wyeksportować** , a następnie kliknij przycisk **OK**.
   3. Poczekaj na eksport do usługi Azure AD, aby zakończyć.

> [!NOTE]
> Te kroki nie obejmują pełnej synchronizacji i kroki w programie Azure AD Connector eksportowania. Te kroki nie są wymagane, ponieważ wartości atrybutów są przesyłane z lokalnej usługi Active Directory do usługi Azure AD tylko.

### <a name="step-7-re-enable-the-sync-scheduler"></a>Krok 7: Ponownie włączyć harmonogram synchronizacji
Ponowne włączenie harmonogramu synchronizacji wbudowany:

1. Uruchom sesję programu PowerShell.
2. Ponowne włączenie synchronizacji według harmonogramu przy użyciu polecenia cmdlet `Set-ADSyncScheduler -SyncCycleEnabled $true`.


## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o model konfiguracji w [Aprowizacją deklaratywną opis](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
* Dowiedz się więcej o języku wyrażenia w [opis deklaratywne inicjowania obsługi administracyjnej wyrażenia](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).

**Tematy poglądowe**

* [Synchronizacja programu Azure AD Connect: zrozumienie i dostosowywanie synchronizacji](active-directory-aadconnectsync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md)
