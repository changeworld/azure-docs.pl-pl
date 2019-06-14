---
title: 'Synchronizacja programu Azure AD Connect: Konfiguracja w usłudze Azure AD Connect sync | Dokumentacja firmy Microsoft'
description: Przedstawiono sposób wprowadzania zmian do konfiguracji w usłudze Azure AD Connect sync.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 7b9df836-e8a5-4228-97da-2faec9238b31
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 31fe3877fd6098b18686b9d99a012cbfbef7c300
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60244041"
---
# <a name="azure-ad-connect-sync-make-a-change-to-the-default-configuration"></a>Synchronizacja programu Azure AD Connect: Zmiany w konfiguracji domyślnej
Ten artykuł ma na celu objaśniono sposób wprowadzania zmian w domyślnej konfiguracji synchronizacji usługi Azure Active Directory (Azure AD) Connect. Zapewnia on kroki kilka typowych scenariuszy. Za pomocą tej wiedzy można wszechstronną własnych konfiguracji na podstawie własnych reguł biznesowych.

> [!WARNING]
> W przypadku wprowadzenia zmian do domyślnych reguł synchronizacji następnie zmiany te zostaną zastąpione przy kolejnym uruchomieniu program Azure AD Connect jest aktualizowany, wynikiem wyniki nieoczekiwany i prawdopodobnie niechciane synchronizacji.
>
> Reguły synchronizacji out-of-box mają odcisku palca. Jeśli wprowadzisz zmiany w tych reguł odcisk palca nie jest zgodny. Mogą wystąpić problemy w przyszłości, gdy użytkownik próbuje zastosować nowej wersji programu Azure AD Connect. Tylko zmiany sposobu, w których jest on opisany w tym artykule.

## <a name="synchronization-rules-editor"></a>Edytor reguł synchronizacji
Narzędzia Synchronization Rules Editor służy do wyświetlić i zmienić konfigurację domyślną. Można znaleźć na **Start** menu w obszarze **program Azure AD Connect** grupy.  
![Menu Start, za pomocą edytora reguł synchronizacji](./media/how-to-connect-sync-change-the-configuration/startmenu2.png)

Po otwarciu edytora, zobaczysz domyślnych reguł out-of-box.

![Edytor reguł synchronizacji](./media/how-to-connect-sync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>Nawigacja w edytorze
Korzystając z list rozwijanych w górnej części edytora, można szybko znaleźć określonej reguły. Na przykład jeśli chcesz zobaczyć reguły, w której jest dołączony atrybut proxyAddresses można zmienić listy rozwijane do następujących:  
![Filtrowanie SRE](./media/how-to-connect-sync-change-the-configuration/filtering.png)  
Aby zresetować, filtrowanie i załadować nowej konfiguracji, naciśnij klawisz F5.

W prawym górnym rogu **Dodaj nową regułę** przycisku. Ten przycisk umożliwia utworzenie własnej niestandardowej.

W dolnej części są przyciski działanie na podstawie reguły synchronizacji wybrane. **Edytuj** i **Usuń** oczekiwaniami ich. **Eksportuj** tworzy skrypt programu PowerShell do ponownego tworzenia reguły synchronizacji. Ta procedura umożliwia przeniesienie reguły synchronizacji z jednego serwera do innego.

## <a name="create-your-first-custom-rule"></a>Tworzenie swojej pierwszej reguły niestandardowe
Najbardziej typowe zmiany dotyczą przepływy atrybutów. Dane w katalogu źródłowym nie może być taki sam jak w usłudze Azure AD. W przykładzie w tej sekcji, upewnij się, dana nazwa użytkownika jest zawsze w *odpowiednich przypadków*.

### <a name="disable-the-scheduler"></a>Wyłącz harmonogram
[Harmonogramu](how-to-connect-sync-feature-scheduler.md) jest domyślnie uruchamiany co 30 minut. Upewnij się, że nie jest uruchomiona, podczas wprowadzania zmian i rozwiązywanie problemów z nowych zasad. Aby tymczasowo wyłączyć harmonogram, uruchom program PowerShell i uruchom `Set-ADSyncScheduler -SyncCycleEnabled $false`.

![Wyłącz harmonogram](./media/how-to-connect-sync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>Utwórz regułę
1. Kliknij przycisk **Dodaj nową regułę**.
2. Na **opis** strony i wprowadź następujące:  
   ![Liczba przychodzących reguł filtrowania](./media/how-to-connect-sync-change-the-configuration/description2.png)  
   * **Nazwa**: Nadaj regule nazwę opisową.
   * **Opis**: Podaj niektórych wyjaśnienie, aby ktoś inny mógł zrozumieć, co to jest reguła.
   * **Połączony System**: Jest to system, w którym można znaleźć obiektu. W takim przypadku wybierz **łącznika usługi Active Directory**.
   * **Typ obiektu systemu/Metaverse podłączonej**: Wybierz **użytkownika** i **osoby**, odpowiednio.
   * **Typ łącza**: Zmienić tę wartość na **Dołącz**.
   * **Pierwszeństwo**: Podaj wartość, która jest unikatowa w systemie. Niższa wartość liczbowa oznacza wyższy priorytet.
   * **Tag**: Pozostaw puste. Tylko out-of-box zasady firmy Microsoft powinna mieć to pole wypełnione wartością.
3. Na **filtru Scoping** wpisz **givenName ISNOTNULL**.  
   ![Reguła filtru określania zakresu ruchu przychodzącego](./media/how-to-connect-sync-change-the-configuration/scopingfilter.png)  
   W tej sekcji służy do definiowania, aby obiekty, które powinno być stosowane reguły. Jeśli pozostanie puste, reguła będzie stosowana do wszystkie obiekty użytkowników. Jednak Obejmowałoby to sale konferencyjne, konta usług i innych obiektów użytkownika innych osób.
4. Na **Dołącz zasady** strony, pozostaw pole puste.
5. Na **przekształcenia** strony, zmień **dla przepływu** do **wyrażenie**. Aby uzyskać **atrybut docelowy**, wybierz opcję **givenName**. I **źródła**, wprowadź **PCase([givenName])** .
   ![Przekształcenia reguły dla ruchu przychodzącego](./media/how-to-connect-sync-change-the-configuration/transformations.png)  
   Aparat synchronizacji jest rozróżniana wielkość liter, zarówno nazwę funkcji, jak i nazwę atrybutu. Jeśli wpiszesz coś niewłaściwego pojawić się ostrzeżenie podczas dodawania reguły. Można zapisać i kontynuować, ale musisz ponownie otworzyć i Popraw regułę.
6. Kliknij przycisk **Dodaj** można zapisać reguły.

Twoja nowa niestandardowa reguła powinna być widoczna przy użyciu innych reguł synchronizacji w systemie.

### <a name="verify-the-change"></a>Sprawdź zmiany
Dzięki tej zmianie nowe chcesz upewnij się, że działa zgodnie z oczekiwaniami i nie zgłasza wszelkie błędy. Istnieją dwa sposoby, aby wykonać ten krok, w zależności od liczby obiektów, do których masz:

- Uruchom pełną synchronizację wszystkich obiektów.
- Uruchom Podgląd i pełną synchronizację na pojedynczy obiekt.

Otwórz **usługi synchronizacji** z **Start** menu. Kroki opisane w tej sekcji są wszystkie w tym narzędziu.

**Pełna synchronizacja na wszystkich obiektach**  

   1. Wybierz **łączników** u góry. Identyfikacja łącznika, który został zmodyfikowany w poprzedniej sekcji (w tym przypadku Active Directory Domain Services) i wybierz ją. 
   2. Aby uzyskać **akcje**, wybierz opcję **Uruchom**.
   3. Wybierz **pełną synchronizację**, a następnie wybierz pozycję **OK**.
   ![Pełna synchronizacja](./media/how-to-connect-sync-change-the-configuration/fullsync.png)  
   Obiekty są teraz aktualizowane w magazynie metaverse. Sprawdź wprowadzone zmiany, patrząc na obiekt w obiekcie metaverse.

**(Wersja zapoznawcza) i pełną synchronizację na pojedynczy obiekt**  

   1. Wybierz **łączników** u góry. Identyfikacja łącznika, który został zmodyfikowany w poprzedniej sekcji (w tym przypadku Active Directory Domain Services) i wybierz ją.
   2. Wybierz **wyszukiwania obszaru łącznika**. 
   3. Użyj **zakres** można znaleźć obiektu, który chcesz użyć, aby przetestować zmiany. Wybierz obiekt i kliknij przycisk **Podgląd**. 
   4. Na nowym ekranie Wybierz **zatwierdzenia w wersji zapoznawczej**.  
   ![Zatwierdź (wersja zapoznawcza)](./media/how-to-connect-sync-change-the-configuration/commitpreview.png)  
   Zmiana teraz zobowiązała się do obiektu metaverse.

**Widok obiektu w obiekcie metaverse**  

1. Masz do wyboru kilka obiektów próbki, upewnij się, że oczekiwaną wartością jest i że reguła jest stosowana. 
2. Wybierz **wyszukiwanie magazynu Metaverse** od góry. Dodaj dowolny filtr, który należy znaleźć odpowiednich obiektów. 
3. W wynikach wyszukiwania Otwórz obiekt. Spójrz na wartości atrybutów, a także Sprawdź, czy w **reguły synchronizacji** kolumny, która reguła jest stosowana zgodnie z oczekiwaniami.  
![Wyszukiwanie magazynu metaverse](./media/how-to-connect-sync-change-the-configuration/mvsearch.png)  

### <a name="enable-the-scheduler"></a>Włącz harmonogram
Jeśli wszystko, co jest zgodne z oczekiwaniami, możesz ponownie włączyć harmonogram. Za pomocą programu PowerShell, uruchom `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="other-common-attribute-flow-changes"></a>Inne typowe zmiany przepływu atrybutów
W poprzedniej sekcji opisano sposób wprowadzania zmian do przepływu atrybutu. W tej sekcji podano kilka dodatkowych przykładów. Kroki dotyczące sposobu tworzenia reguły synchronizacji jest skracana, ale pełne instrukcje można znaleźć w poprzedniej sekcji.

### <a name="use-an-attribute-other-than-the-default"></a>Użyj atrybutu innego niż domyślny
W tym scenariuszu firma Fabrikam jest lasu, gdy lokalnego alfabetu jest stosowana do imię, nazwisko i nazwę wyświetlaną. Reprezentacja znaków łacińskich te atrybuty można znaleźć w atrybutach rozszerzenia. Do tworzenia adresu globalnego listy w usłudze Azure AD i Office 365, organizacja chce, aby użyć tych atrybutów.

Z domyślną konfiguracją obiekt z lasu lokalnego wygląda następująco:  
![Przepływ atrybutów 1](./media/how-to-connect-sync-change-the-configuration/attributeflowjp1.png)

Aby utworzyć regułę z innych przepływów atrybutów, wykonaj następujące czynności:

1. Otwórz **Synchronization Rules Editor** z **Start** menu.
2. Za pomocą **przychodzący** nadal po lewej stronie, kliknij pozycję **Dodaj nową regułę** przycisku.
3. Nadaj regule nazwę i opis. Wybierz wystąpienia usługi Active Directory w środowisku lokalnym i typy odpowiednich obiektów. W **typu łącza**, wybierz opcję **Dołącz**. Aby uzyskać **pierwszeństwo**, wybierz numer, który nie jest używany przez inną regułę. Reguły out-of-box rozpoczynać 100, więc wartość 50 mogą być używane w tym przykładzie.
  ![Przepływ atrybutu 2](./media/how-to-connect-sync-change-the-configuration/attributeflowjp2.png)
4. Pozostaw **filtru Scoping** puste. (Oznacza to, natomiast powinna odnoszą się do wszystkich obiektów użytkownika w lesie.)
5. Pozostaw **Dołącz zasady** puste. (Oznacza to, niech reguły out-of-box obsługi wszelkie sprzężenia).
6. W **przekształcenia**, tworzenie następujących przepływów:  
  ![Przepływ atrybutu 3](./media/how-to-connect-sync-change-the-configuration/attributeflowjp3.png)
7. Kliknij przycisk **Dodaj** można zapisać reguły.
8. Przejdź do **Menedżera usługi synchronizacji**. Na **łączników**, wybierz łącznik, którego dodana reguła. Wybierz **Uruchom**, a następnie wybierz pozycję **pełną synchronizację**. Pełną synchronizację ponownie oblicza wszystkie obiekty przy użyciu bieżącej reguły.

Jest to wynik dla tego samego obiektu przy użyciu reguły niestandardowej:  
![Przepływ atrybutu 4](./media/how-to-connect-sync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>Długość atrybutów
Atrybuty ciągu są można indeksować domyślnie, a maksymalna długość to 448 znaków. Jeśli pracujesz z atrybutami ciągu, które mogą zawierać więcej upewnij się, które mają zostać objęte następujące przepływu atrybutów:  
`attributeName` <- `Left([attributeName],448)`.

### <a name="changing-the-userprincipalsuffix"></a>Zmiana userPrincipalSuffix
Atrybut userPrincipalName w usłudze Active Directory nie zawsze jest znany przez użytkowników i mogą nie być odpowiednie, jako identyfikator logowania. Za pomocą Kreatora instalacji synchronizacji Azure AD Connect, możesz wybrać inny atrybut — na przykład *poczty*. Jednak w niektórych przypadkach atrybutu muszą zostać obliczone.

Na przykład firma Contoso ma dwa katalogi usługi Azure AD, jeden dla środowiska produkcyjnego i jeden dla testowania. Chcą, aby użytkownicy w ramach ich dzierżawy test, aby użyć innego sufiksu w Identyfikatora logowania:  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`.

W tym wyrażeniu wziąć wszystko po lewej stronie pierwszego @-sign (Word) i ZŁĄCZ.teksty z ciągiem stałym.

### <a name="convert-a-multi-value-attribute-to-single-value"></a>Konwertuj atrybutów wielowartościowych na pojedynczą wartość
Niektóre atrybuty w usłudze Active Directory są wielowartościowe w schemacie, mimo że wyglądają jednowartościowych w użytkownicy usługi Active Directory i komputery. Przykładem jest atrybut Opis:  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`.

W tym wyrażeniu, jeśli ten atrybut ma wartość, wykonać pierwszy element (*elementu*) w atrybucie, Usuń spacje początkowe i końcowe (*Trim*), a następnie najpierw 448 znaków (*po lewej stronie* ) w ciągu.

### <a name="do-not-flow-an-attribute"></a>Nie przepływ atrybutu
W tle od scenariusza, w tej sekcji, zobacz [kontrolowania procesu przepływu atrybutu](concept-azure-ad-connect-sync-declarative-provisioning.md#control-the-attribute-flow-process).

Istnieją dwa sposoby nie przepływu atrybutu. Pierwsza to za pomocą Kreatora instalacji, aby [usunąć wybrane atrybuty](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering). Ta opcja działa, jeśli nigdy nie zostały zsynchronizowane atrybutu przed. Jeśli zostało rozpoczęte zsynchronizować ten atrybut i okaże się usunięcie go z tą funkcją, zatrzymuje aparat synchronizacji, zarządzanie atrybut i istniejącej wartości są pozostawiane w usłudze Azure AD.

Jeśli chcesz usunąć wartość atrybutu i upewnij się, że w przyszłości przepływać, musisz utworzyć regułę niestandardową.

W tym scenariuszu firma Fabrikam zdaliśmy mają, że niektóre atrybuty, które firma Microsoft synchronizować z chmurą nie powinny być dostępne. Chcemy upewnić się, że te atrybuty są usuwane z usługi Azure AD.  
![Zły rozszerzeń atrybuty](./media/how-to-connect-sync-change-the-configuration/badextensionattribute.png)

1. Utwórz nową regułę synchronizacji ruchu przychodzącego i wypełnić opisu.
  ![Opisy](./media/how-to-connect-sync-change-the-configuration/syncruledescription.png)
2. Tworzenie przepływów atrybutów z **wyrażenie** dla **dla przepływu** i **AuthoritativeNull** dla **źródła**. Literał **AuthoritativeNull** wskazuje, że wartość powinna być pusta w magazynie metaverse programu nawet wtedy, gdy reguła synchronizacji niższego pierwszeństwa podejmie próbę wypełnienia wartość.
  ![Przekształcania dla atrybutów rozszerzenia](./media/how-to-connect-sync-change-the-configuration/syncruletransformations.png)
3. Zapisz reguły synchronizacji. Uruchom **usługi synchronizacji**, możesz znaleźć odpowiedniego łącznika, wybierz **Uruchom**, a następnie wybierz pozycję **pełną synchronizację**. Ten krok ponownie oblicza wszystkie przepływy atrybutów.
4. Sprawdź, czy zamierzone zmiany do wyeksportowania, wyszukując przestrzeni łącznika.
  ![Usuń etapowe](./media/how-to-connect-sync-change-the-configuration/deletetobeexported.png)

## <a name="create-rules-with-powershell"></a>Tworzenie reguł za pomocą programu PowerShell
Za pomocą edytora reguł synchronizacji działa prawidłowo, jeśli masz tylko kilka zmian, aby wprowadzić. Jeśli musisz wprowadzić wiele zmian, programu PowerShell może być lepszym rozwiązaniem. Niektóre funkcje zaawansowane są dostępne tylko przy użyciu programu PowerShell.

### <a name="get-the-powershell-script-for-an-out-of-box-rule"></a>Pobierz skrypt programu PowerShell dla reguły out-of-box
Aby zobaczyć, programu PowerShell, skrypt, który utworzył regułę out-of-box, wybierz regułę za pomocą edytora reguł synchronizacji i kliknij przycisk **wyeksportować**. Ta akcja umożliwia skrypt programu PowerShell, który utworzył regułę.

### <a name="advanced-precedence"></a>Pierwszeństwo zaawansowane
Reguły synchronizacji out-of-box rozpoczynać pierwszeństwo wartość 100. Jeśli masz wiele lasów, należy wprowadzić wiele zmian niestandardowe następnie reguły synchronizacji 99 może nie być wystarczająca.

Można poinstruować aparat synchronizacji, który ma dodatkowe zasady wstawiany przed regułami out-of-box. Aby uzyskać takie zachowanie, wykonaj następujące kroki:

1. Oznacz pierwszą regułę synchronizacji out-of-box (**w z Dołącz do użytkownika usługi AD**) w Edytor reguł synchronizacji i wybierz pozycję **wyeksportować**. Skopiuj wartość identyfikatora SR.  
![PowerShell przed wprowadzeniem zmiany](./media/how-to-connect-sync-change-the-configuration/powershell1.png)  
2. Utwórz nową regułę synchronizacji. Za pomocą edytora reguł synchronizacji do jego utworzenia. Eksportuj zasady do skryptu programu PowerShell.
3. We właściwości **PrecedenceBefore**, Wstaw wartość identyfikatora reguły out-of-box. Ustaw **pierwszeństwo** do **0**. Upewnij się, że atrybut Identyfikator jest unikatowy i że nie ponownego użycia identyfikatora GUID z inną regułą. Ponadto upewnij się, że **ImmutableTag** nie ustawiono właściwości. Ta właściwość powinna być ustawiona tylko w przypadku regułę out-of-box.
4. Zapisz skrypt programu PowerShell i uruchom go. Wynik jest niestandardowe reguły jest przypisywana wartość pierwszeństwa 100 i wszystkie inne zasady out-of-box są zwiększane.  
![Program PowerShell po zmianie](./media/how-to-connect-sync-change-the-configuration/powershell2.png)  

Może mieć wiele reguł synchronizacji niestandardowych, korzystając z tych samych **PrecedenceBefore** wartości w razie potrzeby.

## <a name="enable-synchronization-of-usertype"></a>Włącz synchronizację UserType
Azure AD Connect obsługuje synchronizację **UserType** atrybutu dla **użytkownika** obiektów w 1.1.524.0 wersji i nowszych. W szczególności zostały wprowadzone następujące zmiany:

- Schemat typu obiektu **użytkownika** w łączniku usługi Azure AD jest rozszerzona, aby uwzględnić atrybut UserType, który jest typu ciąg i jest pojedynczej wartości.
- Schemat typu obiektu **osoby** w magazynie metaverse jest rozszerzona, aby uwzględnić atrybut UserType, który jest typu ciąg i jest pojedynczej wartości.

Domyślnie atrybut UserType nie włączono synchronizacji, ponieważ nie ma odpowiedniego UserType atrybutu w usłudze Active Directory w środowisku lokalnym. Należy ręcznie włączyć synchronizację. Przed wykonaniem tego musi Zwróć uwagę na następujące zachowanie wymuszane przez usługę Azure AD:

- Usługa Azure AD akceptuje tylko dwie wartości dla atrybutu UserType: **Element członkowski** i **gościa**.
- Jeśli atrybut UserType nie jest włączona dla synchronizacji w programie Azure AD Connect, użytkowników usługi Azure AD, utworzony za pomocą synchronizacji katalogów będzie mieć atrybut UserType ustawiony na **elementu członkowskiego**.
- Usługa Azure AD nie zezwala na atrybut UserType na istniejących użytkowników usługi Azure AD, które mają być zmienione przez program Azure AD Connect. Można można ustawić tylko podczas tworzenia użytkowników usługi Azure AD.

Przed włączeniem synchronizacji atrybut UserType, należy najpierw zdecyduj, jak ten atrybut pochodzi z usługi Active Directory w środowisku lokalnym. Poniżej przedstawiono najbardziej typowe metody:

- Wyznacz nieużywane w środowisku lokalnym atrybutu usługi AD (na przykład extensionAttribute1) ma być używany jako atrybutu źródłowego. Wyznaczonej lokalnej usługi AD atrybut powinien być typu **ciąg**, być jednowartościowe i zawierają wartość **elementu członkowskiego** lub **gościa**. 

    Jeśli wybierzesz tę opcję, należy upewnić się wyznaczonym atrybutu jest wypełniana poprawną wartość dla wszystkich istniejących obiektów użytkowników w usłudze Active Directory w środowisku lokalnym, które są synchronizowane z usługą Azure AD przed włączeniem synchronizacji atrybut UserType .

- Alternatywnie możesz mogą dziedziczyć po wartości atrybutu UserType inne właściwości. Na przykład chcesz synchronizować wszystkich użytkowników jako **gościa** Jeśli lokalnych atrybut userPrincipalName AD kończy się część domeny <em>@partners.fabrikam123.org</em>. 

    Jak wspomniano wcześniej, program Azure AD Connect nie zezwala na atrybut UserType na istniejących użytkowników usługi Azure AD, które mają być zmienione przez program Azure AD Connect. W związku z tym należy zagwarantować, że logika zostały określone, jest zgodna z jak atrybut UserType jest już skonfigurowany dla wszystkich istniejących użytkowników usługi Azure AD w swojej dzierżawie.

Kroki, aby włączyć synchronizację atrybut UserType można podsumować jako:

1.  Wyłącz harmonogram synchronizacji i sprawdzić, czy jest brak synchronizacji w toku.
2.  Dodawanie atrybutu źródłowego do serwera lokalnego łącznika AD schematu.
3.  Dodaj UserType schematu łącznik usługi Azure AD.
4.  Utwórz regułę synchronizacji ruchu przychodzącego na przepływ wartości atrybutu z usługi Active Directory w środowisku lokalnym.
5.  Utwórz regułę synchronizacji ruchu wychodzącego do przepływu wartość atrybutu do usługi Azure AD.
6.  Uruchom pełną synchronizację cyklu.
7.  Włącz harmonogram synchronizacji.

>[!NOTE]
> Pozostała część tej sekcji omówiono następujące kroki. Zostały one opisane w kontekście wdrożenia usługi Azure AD z topologią obejmujących jeden las i bez reguł niestandardowych. W przypadku topologii z wieloma lasami reguły synchronizacji niestandardowych skonfigurowany lub masz serwer przejściowy, musisz odpowiednio dostosować czynności.

### <a name="step-1-disable-the-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Krok 1: Wyłącz harmonogram synchronizacji i sprawdzić, czy jest brak synchronizacji w toku
Aby uniknąć eksportowanie niezamierzone zmiany do usługi Azure AD, upewnij się, że synchronizacja nie ma miejsce, gdy trwa aktualizowanie reguł synchronizacji. Aby wyłączyć harmonogram synchronizacji wbudowane:

 1. Uruchom sesję programu PowerShell na serwerze programu Azure AD Connect.
 2. Wyłącz zaplanowanej synchronizacji uruchamiając polecenie cmdlet `Set-ADSyncScheduler -SyncCycleEnabled $false`.
 3. Otwórz Menedżera usługi synchronizacji, przechodząc do **Start** > **usługi synchronizacji**.
 4. Przejdź do **operacji** kartę i upewnij się, brak żadna operacja ze stanem *w toku*.

### <a name="step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema"></a>Krok 2: Dodawanie atrybutu źródłowego do serwera lokalnego schemat łącznika AD
Nie wszystkie atrybuty usługi Azure AD są importowane do lokalnego obszaru łącznika usługi AD. Aby dodać atrybut źródłowy do listy atrybutów zaimportowane:

 1. Przejdź do **łączników** kartę w Menedżerze usługi synchronizacji.
 2. Kliknij prawym przyciskiem myszy lokalną łącznik AD, a następnie wybierz pozycję **właściwości**.
 3. W podręcznym oknie dialogowym, przejdź do **wybierz atrybuty** kartę.
 4. Upewnij się, że atrybut źródłowy jest zaznaczona na liście atrybutów.
 5. Kliknij przycisk **OK** do zapisania.
![Dodaj atrybut źródłowy do sieci lokalnej schemat łącznika AD](./media/how-to-connect-sync-change-the-configuration/usertype1.png)

### <a name="step-3-add-the-usertype-to-the-azure-ad-connector-schema"></a>Krok 3: Dodaj UserType schematu łącznik usługi Azure AD
Domyślnie atrybut UserType nie jest zaimportowany do platformy Azure AD Connect miejsca. Aby dodać atrybut UserType do listy atrybutów zaimportowane:

 1. Przejdź do **łączników** kartę w Menedżerze usługi synchronizacji.
 2. Kliknij prawym przyciskiem myszy **łącznik usługi Azure AD** i wybierz **właściwości**.
 3. W podręcznym oknie dialogowym, przejdź do **wybierz atrybuty** kartę.
 4. Upewnij się, że atrybut UserType jest zaznaczona na liście atrybutów.
 5. Kliknij przycisk **OK** do zapisania.

![Dodaj atrybut źródłowy ze schematem łącznik usługi Azure AD](./media/how-to-connect-sync-change-the-configuration/usertype2.png)

### <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>Krok 4: Utwórz regułę synchronizacji ruchu przychodzącego na przepływ wartości atrybutu z usługi Active Directory w środowisku lokalnym
Reguła synchronizacji ruchu przychodzącego zezwala na wartość atrybutu, które będą przepływać z atrybutu źródłowego z usługi Active Directory środowiska lokalnego do środowiska metaverse:

1. Otwórz narzędzia Synchronization Rules Editor, przechodząc do **Start** > **Synchronization Rules Editor**.
2. Ustaw filtr wyszukiwania **kierunek** jako **przychodzący**.
3. Kliknij przycisk **Dodaj nową regułę** przycisk, aby utworzyć nową regułę dla ruchu przychodzącego.
4. W obszarze **opis** kartę, należy zapewnić następującą konfigurację:

    | Atrybut | Wartość | Szczegóły |
    | --- | --- | --- |
    | Name (Nazwa) | *Podaj nazwę* | Na przykład *w z usługi AD — UserType użytkownika* |
    | Opis | *Podaj opis* |  |
    | Połączonego systemu | *Wybierz lokalny łącznik usługi AD* |  |
    | Połączony System typu obiektu | **Użytkownik** |  |
    | Typ obiektu Metaverse | **Osoby** |  |
    | Typ łącza | **Join** |  |
    | Pierwszeństwo | *Wybierz liczbę z zakresu od 1 – 99* | 1 – 99 jest zarezerwowana dla reguły synchronizacji niestandardowych. Nie wybierz wartość, która jest używana przez inną regułę synchronizacji. |

5. Przejdź do **filtru Scoping** karta i Dodaj **pojedynczą grupę filtrów określania zakresu** z następującą klauzulę:

    | Atrybut | Operator | Wartość |
    | --- | --- | --- |
    | adminDescription | NOTSTARTWITH | Użytkownik\_ |

    Filtru określania zakresu określa, aby lokalnych AD obiektów, że jest stosowana ta reguła synchronizacji ruchu przychodzącego. W tym przykładzie używamy tego samego filtru określania zakresu używane w *w z usługi AD — typowe użytkownika* reguły synchronizacji out-of-box, która uniemożliwia reguły synchronizacji Trwa stosowanie w obiektach użytkownika utworzone za pomocą użytkownika usługi Azure AD Funkcja zapisywania zwrotnego. Może być konieczne dostosowanie filtru określania zakresu, zgodnie z wdrożenia usługi Azure AD Connect.

6. Przejdź do **przekształcania** kartę i zaimplementować reguły odpowiednie przekształcenie. Na przykład, jeśli został wyznaczony nieużywane w środowisku lokalnym atrybutu usługi AD (na przykład extensionAttribute1) jako atrybut źródłowy dla UserType, można zaimplementować przepływ atrybutu bezpośrednie:

    | Typ przepływu | Atrybut docelowy | source | Zastosuj raz | Scal typu |
    | --- | --- | --- | --- | --- |
    | Direct | UserType | extensionAttribute1 | Niezaznaczone | Aktualizacja |

    W kolejnym przykładzie chcesz dziedziczyć wartość atrybutu UserType inne właściwości. Na przykład chcesz synchronizować wszystkich użytkowników jako gościa, jeśli ich w środowisku lokalnym atrybut userPrincipalName AD kończy się część domeny <em>@partners.fabrikam123.org</em>. Można zaimplementować wyrażenia następująco:

    | Typ przepływu | Atrybut docelowy | source | Zastosuj raz | Scal typu |
    | --- | --- | --- | --- | --- |
    | Wyrażenie | UserType | IIf(IsPresent([userPrincipalName]),IIf(CBool(InStr(LCase([userPrincipalName])"@partners.fabrikam123.org")=0) "Elementu członkowskiego", "Gość"), błąd ("UserPrincipalName nie jest obecna, aby określić UserType")) | Niezaznaczone | Aktualizacja |

7. Kliknij przycisk **Dodaj** do utworzenia reguły dla ruchu przychodzącego.

![Utwórz regułę synchronizacji ruchu przychodzącego](./media/how-to-connect-sync-change-the-configuration/usertype3.png)

### <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>Krok 5. Utwórz regułę synchronizacji ruchu wychodzącego do przepływu wartość atrybutu do usługi Azure AD
Reguła synchronizacji ruchu wychodzącego zezwala na wartość atrybutu mogą przepływać z obiektu metaverse w atrybut UserType w usłudze Azure AD:

1. Przejdź do narzędzia Synchronization Rules Editor.
2. Ustaw filtr wyszukiwania **kierunek** jako **ruchu wychodzącego**.
3. Kliknij przycisk **Dodaj nową regułę** przycisku.
4. W obszarze **opis** kartę, należy zapewnić następującą konfigurację:

    | Atrybut | Wartość | Szczegóły |
    | ----- | ------ | --- |
    | Name (Nazwa) | *Podaj nazwę* | Na przykład *Out do usługi AAD — UserType użytkownika* |
    | Opis | *Podaj opis* ||
    | Połączonego systemu | *Wybierz łącznik usługi AAD* ||
    | Połączony System typu obiektu | **Użytkownik** ||
    | Typ obiektu Metaverse | **Osoby** ||
    | Typ łącza | **Join** ||
    | Pierwszeństwo | *Wybierz liczbę z zakresu od 1 – 99* | 1 – 99 jest zarezerwowana dla reguły synchronizacji niestandardowych. Nie wybierz wartość, która jest używana przez inną regułę synchronizacji. |

5. Przejdź do **filtru Scoping** karta i Dodaj **pojedynczą grupę filtrów określania zakresu** z dwóch klauzul:

    | Atrybut | Operator | Wartość |
    | --- | --- | --- |
    | sourceObjectType | RÓWNA SIĘ | Użytkownik |
    | cloudMastered | NOTEQUAL | True |

    Filtru określania zakresu określa, do którego usługa Azure AD obiekty, że jest stosowana ta reguła synchronizacji ruchu wychodzącego. W tym przykładzie używamy tego samego filtru określania zakresu z *poza usługą AD — tożsamość użytkownika* reguły synchronizacji out-of-box. Reguły synchronizacji zapobiega stosowaniu w obiektach użytkownika, które nie są synchronizowane z usługi Active Directory w środowisku lokalnym. Może być konieczne dostosowanie filtru określania zakresu, zgodnie z wdrożenia usługi Azure AD Connect.

6. Przejdź do **przekształcania** kartę i zaimplementować następującą regułę przekształcania:

    | Typ przepływu | Atrybut docelowy | source | Zastosuj raz | Scal typu |
    | --- | --- | --- | --- | --- |
    | Direct | UserType | UserType | Niezaznaczone | Aktualizacja |

7. Kliknij przycisk **Dodaj** do utworzenia reguły ruchu wychodzącego.

![Utwórz regułę synchronizacji ruchu wychodzącego](./media/how-to-connect-sync-change-the-configuration/usertype4.png)

### <a name="step-6-run-a-full-synchronization-cycle"></a>Krok 6: Uruchom pełną synchronizację cyklu
Ogólnie rzecz biorąc cyklu Pełna synchronizacja jest wymagana, ponieważ firma Microsoft ma dodaje nowe atrybuty do usługi Active Directory, jak i łącznik usługi Azure AD schematów i wprowadzić reguły synchronizacji niestandardowych. Chcesz zweryfikować zmiany przed rozpoczęciem eksportowania ich do usługi Azure AD. 

Następujące kroki można użyć, aby zweryfikować zmiany podczas ręcznego uruchamiania czynności, które tworzą cyklu pełną synchronizację.

1. Uruchom **pełny import** na **AD łącznika lokalnego**:

   1. Przejdź do **operacji** kartę w Menedżerze usługi synchronizacji.
   2. Kliknij prawym przyciskiem myszy **AD łącznika lokalnego** i wybierz **Uruchom**.
   3. W podręcznym oknie dialogowym, wybierz **pełny Import** a następnie kliknij przycisk **OK**.
   4. Poczekaj na zakończenie operacji.

      > [!NOTE]
      > Możesz pominąć pełny import na lokalną łącznik AD, jeśli atrybut źródłowy znajduje się już na liście zaimportowane atrybutów. Oznacza to, trzeba wprowadzać żadnych zmian podczas [krok 2: Dodawanie atrybutu źródłowego do serwera lokalnego łącznika AD schematu](#step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema).

2. Uruchom **pełny import** na **łącznik usługi Azure AD**:

   1. Kliknij prawym przyciskiem myszy **łącznik usługi Azure AD** i wybierz **Uruchom**.
   2. W podręcznym oknie dialogowym, wybierz **pełny Import** a następnie kliknij przycisk **OK**.
   3. Poczekaj na zakończenie operacji.

3. Sprawdź zmiany reguł synchronizacji na istniejący obiekt użytkownika:

    Atrybut źródłowy z lokalne usługi Active Directory i UserType z usługi Azure AD zostały zaimportowane do ich odpowiednich miejsc do magazynowania łącznika. Przed wykonaniem pełnej synchronizacji należy wykonać **Podgląd** na istniejącym użytkownikiem obiektu w lokalnej przestrzeni łącznika usługi AD. Obiekt, który został wybrany powinny mieć atrybut źródłowy wypełnione.
    
    Pomyślny **Podgląd** z wartością UserType wypełnione w magazynie metaverse jest dobry wskaźnik skonfigurowano synchronizację reguł poprawnie. Aby uzyskać informacje na temat **Podgląd**, zapoznaj się z sekcją [sprawdzić zmiany](#verify-the-change).

4. Uruchom **pełnej synchronizacji** na **on-premises AD Connector**:

   1. Kliknij prawym przyciskiem myszy **AD łącznika lokalnego** i wybierz **Uruchom**.
   2. W podręcznym oknie dialogowym, wybierz **pełną synchronizację** a następnie kliknij przycisk **OK**.
   3. Poczekaj na zakończenie operacji.

5. Sprawdź **oczekujące operacje eksportu** do usługi Azure AD:

   1. Kliknij prawym przyciskiem myszy **łącznik usługi Azure AD** i wybierz **wyszukiwania obszaru łącznika**.
   2. W **wyszukiwania obszaru łącznika** wyskakujące okno dialogowe:

      - Ustaw **zakres** do **operacja eksportowania oczekująca**.
      - Zaznacz wszystkie trzy pola wyboru: **Dodaj**, **zmodyfikować**, i **Usuń**.
      - Kliknij przycisk **wyszukiwania** przycisk, aby uzyskać listę obiektów, ze zmianami, które mają zostać wyeksportowane. Aby sprawdzić zmiany dla danego obiektu, kliknij dwukrotnie obiekt.
      - Upewnij się, czy zmiany są oczekiwane.

6. Uruchom **wyeksportować** na **łącznik usługi Azure AD**:

   1. Kliknij prawym przyciskiem myszy **łącznik usługi Azure AD** i wybierz **Uruchom**.
   2. W **uruchomienia łącznika** wyskakujące okno dialogowe, wybierz opcję **wyeksportować** a następnie kliknij przycisk **OK**.
   3. Poczekaj, aż eksportu do usługi Azure AD, aby zakończyć.

> [!NOTE]
> Te kroki nie obejmują pełnej synchronizacji i kroki w programie Azure AD Connector eksportowania. Te kroki nie są wymagane, ponieważ wartości atrybutów przepływają do usługi Azure AD tylko z usługi Active Directory w środowisku lokalnym.

### <a name="step-7-re-enable-the-sync-scheduler"></a>Krok 7: Ponowne włączenie programu sync scheduler
Ponownie włączyć harmonogram synchronizacji wbudowane:

1. Uruchom sesję programu PowerShell.
2. Ponowne włączenie zaplanowanej synchronizacji uruchamiając polecenie cmdlet `Set-ADSyncScheduler -SyncCycleEnabled $true`.


## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o modelu konfiguracji w [Aprowizacja Deklaratywna opis](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Dowiedz się więcej o języku wyrażeń w [interpretacji wyrażenia inicjowania obsługi administracyjnej deklaratywne](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).

**Tematy poglądowe**

* [Synchronizacja w programie Azure AD Connect: Zrozumienie i dostosowywanie synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
