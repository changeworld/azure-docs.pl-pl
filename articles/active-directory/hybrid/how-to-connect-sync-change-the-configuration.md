---
title: 'Synchronizacja usługi Azure AD Connect: wprowadzanie zmian konfiguracji w synchronizacji usługi Azure AD Connect'
description: Przeprowadzi Cię przez jak wprowadzić zmiany w konfiguracji w usłudze Azure AD Connect synchronizacji.
services: active-directory
author: billmath
manager: daveba
ms.assetid: 7b9df836-e8a5-4228-97da-2faec9238b31
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d77882817934d5ad98f16965aeb9dc246931c495
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261166"
---
# <a name="azure-ad-connect-sync-make-a-change-to-the-default-configuration"></a>Synchronizacja usługi Azure AD Connect: wprowadzanie zmian w konfiguracji domyślnej
Celem tego artykułu jest przejście przez sposób wprowadzania zmian w konfiguracji domyślnej w usłudze Azure Active Directory (Azure AD) Connect sync. Zawiera kroki dla niektórych typowych scenariuszy. Dzięki tej wiedzy powinieneś być w stanie wprowadzić proste zmiany do własnej konfiguracji w oparciu o własne reguły biznesowe.

> [!WARNING]
> Jeśli wprowadzą zmiany do domyślnych reguł synchronizacji out-of-box, a następnie te zmiany zostaną zastąpione przy następnej aktualizacji usługi Azure AD Connect, co powoduje nieoczekiwane i prawdopodobnie niepożądane wyniki synchronizacji.
>
> Domyślne reguły synchronizacji out-of-box mają odcisk palca. Jeśli zmienisz te reguły, odcisk palca nie jest już pasując. Podczas próby zastosowania nowej wersji usługi Azure AD Connect mogą wystąpić problemy w przyszłości. Wprowadzaj zmiany tylko w sposób opisany w tym artykule.

## <a name="synchronization-rules-editor"></a>Edytor reguł synchronizacji
Edytor reguł synchronizacji służy do wyświetlania i zmieniania domyślnej konfiguracji. Można go znaleźć w menu **Start** w grupie **Usługi Azure AD Connect.**  
![Menu Start z Edytorem reguł synchronizacji](./media/how-to-connect-sync-change-the-configuration/startmenu2.png)

Po otwarciu edytora zobaczysz domyślne reguły gotowe do użycia.

![Edytor reguł synchronizacji](./media/how-to-connect-sync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>Nawigacja w edytorze
Korzystając z rozwijanych w górnej części edytora, można szybko znaleźć określoną regułę. Na przykład, jeśli chcesz zobaczyć reguły, w których znajduje się adresy proxy atrybutu, możesz zmienić listy rozwijane na następujące:  
![Filtrowanie SRE](./media/how-to-connect-sync-change-the-configuration/filtering.png)  
Aby zresetować filtrowanie i załadować nową konfigurację, naciśnij klawisz F5 na klawiaturze.

W prawym górnym rogu znajduje się przycisk **Dodaj nową regułę.** Ten przycisk służy do tworzenia własnej reguły niestandardowej.

Na dole znajdują się przyciski do działania na wybranej regule synchronizacji. **Edytuj** i **usuń** zrobić to, czego oczekujesz od nich. **Export** tworzy skrypt programu PowerShell do ponownego tworzenia reguły synchronizacji. Za pomocą tej procedury można przenieść regułę synchronizacji z jednego serwera na inny.

## <a name="create-your-first-custom-rule"></a>Tworzenie pierwszej reguły niestandardowej
Najczęstsze zmiany są do przepływów atrybutów. Dane w katalogu źródłowym mogą nie być takie same jak w usłudze Azure AD. W przykładzie w tej sekcji upewnij się, że podane imię i nazwisko użytkownika jest zawsze w *odpowiednim przypadku*.

### <a name="disable-the-scheduler"></a>Wyłączanie harmonogramu
[Harmonogram](how-to-connect-sync-feature-scheduler.md) jest domyślnie uruchamiany co 30 minut. Upewnij się, że nie uruchamia się podczas wprowadzania zmian i rozwiązywania problemów z nowymi regułami. Aby tymczasowo wyłączyć harmonogram, uruchom program PowerShell i uruchom program `Set-ADSyncScheduler -SyncCycleEnabled $false`.

![Wyłączanie harmonogramu](./media/how-to-connect-sync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>Tworzenie reguły
1. Kliknij **pozycję Dodaj nową regułę**.
2. Na stronie **Opis** wprowadź następujące elementy:  
   ![Filtrowanie reguł ruchu przychodzącego](./media/how-to-connect-sync-change-the-configuration/description2.png)  
   * **Nazwa**: Nadaj regule nazwę opisową.
   * **Opis**: Daj kilka wyjaśnień, aby ktoś inny mógł zrozumieć, do czego służy reguła.
   * **Connected System**: Jest to system, w którym można znaleźć obiekt. W takim przypadku wybierz pozycję **Łącznik usługi Active Directory**.
   * **Połączony typ obiektu system/metaverse:** wybierz odpowiednio **użytkownika** i **osobę.**
   * **Typ łącza:** Zmień tę wartość na **Dołącz**.
   * **Pierwszeństwo:** Podaj wartość, która jest unikatowa w systemie. Niższa wartość liczbowa wskazuje wyższy priorytet.
   * **Tag:** Pozostaw to puste. Tylko gotowe reguły firmy Microsoft powinny mieć to pole wypełnione wartością.
3. Na stronie **filtru zakres** wprowadź **podanynamięmie ISNOTNULL**.  
   ![Filtr zakresu reguł ruchu przychodzącego](./media/how-to-connect-sync-change-the-configuration/scopingfilter.png)  
   Ta sekcja służy do definiowania obiektów, które reguła powinna mieć zastosowanie. Jeśli pozostanie pusta, reguła będzie stosowana do wszystkich obiektów użytkownika. Jednak obejmowałoby to sale konferencyjne, konta usług i inne obiekty użytkowników niebędących osobami.
4. Na stronie **Reguły dołączania** pozostaw pole puste.
5. Na stronie **Przekształcenia** zmień **flowtype** na **wyrażenie**. W przypadku **atrybutu docelowego**wybierz pozycję **givenName**. I dla **Source**, wprowadź **PCase([givenName])**.
   ![Przekształcenia reguł ruchu przychodzącego](./media/how-to-connect-sync-change-the-configuration/transformations.png)  
   Aparat synchronizacji jest rozróżniana wielkość liter zarówno dla nazwy funkcji, jak i nazwy atrybutu. Jeśli wpiszesz coś nie tak, zobaczysz ostrzeżenie po dodaniu reguły. Można zapisać i kontynuować, ale trzeba ponownie otworzyć i poprawić regułę.
6. Kliknij **przycisk Dodaj,** aby zapisać regułę.

Nowa reguła niestandardowa powinna być widoczna z innymi regułami synchronizacji w systemie.

### <a name="verify-the-change"></a>Sprawdź zmianę
Dzięki tej nowej zmianie chcesz upewnić się, że działa zgodnie z oczekiwaniami i nie zgłasza żadnych błędów. W zależności od liczby posiadanych obiektów można wykonać dwa sposoby wykonania tego kroku:

- Uruchom pełną synchronizację na wszystkich obiektach.
- Uruchamianie podglądu i pełna synchronizacja na jednym obiekcie.

Otwórz **usługę synchronizacji** z menu **Start.** Kroki opisane w tej sekcji znajdują się w tym narzędziu.

**Pełna synchronizacja wszystkich obiektów**  

   1. Wybierz **łączniki** u góry. Zidentyfikuj łącznik zmieniony w poprzedniej sekcji (w tym przypadku Usługi domenowe Active Directory) i wybierz go. 
   2. W obszarze **Akcje**wybierz pozycję **Uruchom**.
   3. Wybierz **pozycję Pełna synchronizacja**, a następnie wybierz przycisk **OK**.
   ![Pełna synchronizacja](./media/how-to-connect-sync-change-the-configuration/fullsync.png)  
   Obiekty są teraz aktualizowane w metaverse. Sprawdź zmiany, patrząc na obiekt w metaverse.

**Podgląd i pełna synchronizacja na jednym obiekcie**  

   1. Wybierz **łączniki** u góry. Zidentyfikuj łącznik zmieniony w poprzedniej sekcji (w tym przypadku Usługi domenowe Active Directory) i wybierz go.
   2. Wybierz **miejsce łącznika wyszukiwania**. 
   3. Użyj **zakresu,** aby znaleźć obiekt, który ma być używany do testowania zmiany. Zaznacz obiekt i kliknij pozycję **Podgląd**. 
   4. Na nowym ekranie wybierz pozycję **Zatwierdzanie podglądu**.  
   ![Podgląd zatwierdzania](./media/how-to-connect-sync-change-the-configuration/commitpreview.png)  
   Zmiana jest teraz zobowiązana do metaverse.

**Wyświetlanie obiektu w metaverse**  

1. Wybierz kilka przykładowych obiektów, aby upewnić się, że wartość jest oczekiwana i że reguła została zastosowana. 
2. Wybierz **metaverse search** od góry. Dodaj dowolny filtr, który należy znaleźć odpowiednie obiekty. 
3. Z wyniku wyszukiwania otwórz obiekt. Spójrz na wartości atrybutów, a także sprawdź w kolumnie **Reguły synchronizacji,** czy reguła została zastosowana zgodnie z oczekiwaniami.  
![Wyszukiwanie magazynu metaverse](./media/how-to-connect-sync-change-the-configuration/mvsearch.png)  

### <a name="enable-the-scheduler"></a>Włączanie harmonogramu
Jeśli wszystko jest zgodnie z oczekiwaniami, można włączyć harmonogram ponownie. Z programu PowerShell uruchom program `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="other-common-attribute-flow-changes"></a>Inne typowe zmiany przepływu atrybutów
W poprzedniej sekcji opisano, jak wprowadzić zmiany w przepływie atrybutów. W tej sekcji przedstawiono kilka dodatkowych przykładów. Kroki dotyczące tworzenia reguły synchronizacji są skrócone, ale pełne kroki można znaleźć w poprzedniej sekcji.

### <a name="use-an-attribute-other-than-the-default"></a>Używanie atrybutu innego niż domyślny
W tym scenariuszu Fabrikam istnieje las, w którym alfabet lokalny jest używany dla podanego imienia, nazwiska i nazwy wyświetlanej. Reprezentacja tych atrybutów znaków łacińskich znajduje się w atrybutach rozszerzenia. Aby zbudować globalną listę adresów w usłudze Azure AD i usłudze Office 365, organizacja chce zamiast tego użyć tych atrybutów.

W konfiguracji domyślnej obiekt z lasu lokalnego wygląda następująco:  
![Przepływ atrybutów 1](./media/how-to-connect-sync-change-the-configuration/attributeflowjp1.png)

Aby utworzyć regułę z innymi przepływami atrybutów, wykonaj następujące czynności:

1. Otwórz **Edytor reguł synchronizacji** z menu **Start.**
2. **Gdy ruch przychodzący jest** nadal zaznaczony po lewej stronie, kliknij przycisk Dodaj nową **regułę.**
3. Nadaj regule nazwę i opis. Wybierz lokalne wystąpienie usługi Active Directory i odpowiednie typy obiektów. W **obszarze Typ łącza**wybierz pozycję **Dołącz**. W przypadku **opcji Pierwszeństwo**wybierz numer, który nie jest używany przez inną regułę. Reguły out-of-box zaczynają się od 100, więc wartość 50 może być używana w tym przykładzie.
  ![Przepływ atrybutów 2](./media/how-to-connect-sync-change-the-configuration/attributeflowjp2.png)
4. Pozostaw **filtr zakresu** pusty. (Oznacza to, że należy zastosować do wszystkich obiektów użytkownika w lesie.)
5. Pozostaw **reguły sprzężenia** puste. (Oznacza to, że niech out-of-box reguły obsługi wszystkich sprzężeń.)
6. W **przekształceniach**należy utworzyć następujące przepływy:  
  ![Przepływ atrybutów 3](./media/how-to-connect-sync-change-the-configuration/attributeflowjp3.png)
7. Kliknij **przycisk Dodaj,** aby zapisać regułę.
8. Przejdź do **Menedżera usług synchronizacji**. W **obszarze Łączniki**wybierz łącznik, w którym została dodana reguła. Wybierz **pozycję Uruchom**, a następnie wybierz pozycję Pełna **synchronizacja**. Pełna synchronizacja ponownie oblicza wszystkie obiekty przy użyciu bieżących reguł.

Jest to wynik dla tego samego obiektu z tą regułą niestandardową:  
![Przepływ atrybutów 4](./media/how-to-connect-sync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>Długość atrybutów
Atrybuty ciągów są domyślnie indeksowalne, a maksymalna długość wynosi 448 znaków. Jeśli pracujesz z atrybutami ciągu, które mogą zawierać więcej, upewnij się, że w przepływie atrybutów uwzględniają się następujące elementy:  
`attributeName` <- `Left([attributeName],448)`.

### <a name="changing-the-userprincipalsuffix"></a>Zmiana userPrincipalSuffix
Atrybut userPrincipalName w usłudze Active Directory nie zawsze jest znany przez użytkowników i może nie być odpowiedni jako identyfikator logowania. Za pomocą kreatora instalacji synchronizacji usługi Azure AD Connect można wybrać inny atrybut — na przykład *pocztę*. Ale w niektórych przypadkach atrybut musi być obliczony.

Na przykład firma Contoso ma dwa katalogi usługi Azure AD, jeden dla produkcji i jeden do testowania. Chcą, aby użytkownicy w dzierżawie testowej używali innego sufiksu w identyfikatorze logowania:  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`.

W tym wyrażeniu należy @-sign wziąć wszystko po lewej stronie pierwszego (Word) i połączyć ze stałym ciągiem.

### <a name="convert-a-multi-value-attribute-to-single-value"></a>Konwertowanie atrybutu wielowartowiowego na pojedynczą wartość
Niektóre atrybuty w usłudze Active Directory są wielokrotniewartościowane w schemacie, nawet jeśli w u użytkowników i komputerach usługi Active Directory wyglądają one z jedną wartością. Przykładem jest atrybut opisu:  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`.

W tym wyrażeniu, jeśli atrybut ma wartość, weź pierwszy element (*Element*) w atrybucie, usuń spacje początkowe i końcowe (*Przytnij*), a następnie zachowaj pierwsze 448 znaków (*Po lewej*) w ciągu.

### <a name="do-not-flow-an-attribute"></a>Nie należy przesyłać atrybutu
Aby uzyskać informacje na temat scenariusza dla tej sekcji, zobacz [Kontrolowanie procesu przepływu atrybutów](concept-azure-ad-connect-sync-declarative-provisioning.md#control-the-attribute-flow-process).

Istnieją dwa sposoby, aby nie przepływać atrybutu. Pierwszym z nich jest użycie kreatora instalacji w celu [usunięcia wybranych atrybutów](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering). Ta opcja działa, jeśli atrybut nigdy wcześniej nie był synchronizowany. Jeśli jednak rozpoczęto synchronizowanie tego atrybutu, a później usunięcie go za pomocą tej funkcji, aparat synchronizacji przestaje zarządzać atrybutem, a istniejące wartości pozostają w usłudze Azure AD.

Jeśli chcesz usunąć wartość atrybutu i upewnić się, że nie będzie on przepływał w przyszłości, musisz utworzyć regułę niestandardową.

W tym scenariuszu Fabrikam zdaliśmy sobie sprawę, że niektóre atrybuty, które synchronizujemy z chmurą nie powinny tam być. Chcemy upewnić się, że te atrybuty są usuwane z usługi Azure AD.  
![Nieprawidłowe atrybuty rozszerzenia](./media/how-to-connect-sync-change-the-configuration/badextensionattribute.png)

1. Utwórz nową regułę synchronizacji przychodzącej i wypełnij opis.
  ![Opisy](./media/how-to-connect-sync-change-the-configuration/syncruledescription.png)
2. Tworzenie przepływów atrybutów za pomocą **wyrażenia** dla **flowtype** i **authoritativeNull** for **Source**. Literał **AuthoritativeNull** wskazuje, że wartość powinna być pusta w metaverse, nawet jeśli reguła synchronizacji niższego pierwszeństwa próbuje wypełnić wartość.
  ![Transformacja dla atrybutów rozszerzenia](./media/how-to-connect-sync-change-the-configuration/syncruletransformations.png)
3. Zapisz regułę synchronizacji. Uruchom **usługę synchronizacji**, znajdź łącznik, wybierz pozycję **Uruchom**, a następnie wybierz pozycję **Pełna synchronizacja**. Ten krok ponownie oblicza wszystkie przepływy atrybutów.
4. Sprawdź, czy zamierzone zmiany mają zostać wyeksportowane, przeszukując przestrzeń łącznika.
  ![Usuwanie etapowe](./media/how-to-connect-sync-change-the-configuration/deletetobeexported.png)

## <a name="create-rules-with-powershell"></a>Tworzenie reguł za pomocą programu PowerShell
Korzystanie z edytora reguł synchronizacji działa poprawnie, gdy masz tylko kilka zmian do wniesienia. Jeśli trzeba wprowadzić wiele zmian, Program PowerShell może być lepszym rozwiązaniem. Niektóre zaawansowane funkcje są dostępne tylko w programie PowerShell.

### <a name="get-the-powershell-script-for-an-out-of-box-rule"></a>Pobierz skrypt programu PowerShell dla reguły po wyjęciu z pudełka
Aby wyświetlić skrypt programu PowerShell, który utworzył regułę po wyjęciu z pudełka, zaznacz regułę w edytorze reguł synchronizacji i kliknij przycisk **Eksportuj**. Ta akcja daje skrypt programu PowerShell, który utworzył regułę.

### <a name="advanced-precedence"></a>Priorytet zaawansowany
Reguły synchronizacji out-of-box zaczynają się od wartości pierwszeństwa 100. Jeśli masz wiele lasów i musisz wprowadzić wiele niestandardowych zmian, reguły synchronizacji 99 mogą nie wystarczyć.

Aparat synchronizacji można poinstruować, że mają zostać wstawione dodatkowe reguły przed regułami gotowymi do użycia. Aby uzyskać to zachowanie, wykonaj następujące kroki:

1. Oznacz pierwszą regułę synchronizacji out-of-box **(In from AD-User Join)** w edytorze reguł synchronizacji i wybierz pozycję **Eksportuj**. Skopiuj wartość identyfikatora SR.  
![Program PowerShell przed zmianą](./media/how-to-connect-sync-change-the-configuration/powershell1.png)  
2. Utwórz nową regułę synchronizacji. Do jego utworzenia można użyć edytora reguł synchronizacji. Eksportuj regułę do skryptu programu PowerShell.
3. We właściwości **PrecedenceBefore**wstaw wartość identyfikatora z reguły out-of-box. Ustaw **pierwszeństwo** na **0**. Upewnij się, że atrybut Identyfikator jest unikatowy i że nie jest ponownie za pomocą identyfikatora GUID z innej reguły. Upewnij się również, że właściwość **ImmutableTag** nie jest ustawiona. Ta właściwość powinna być ustawiona tylko dla reguły out-of-box.
4. Zapisz skrypt programu PowerShell i uruchom go. W rezultacie reguły niestandardowej jest przypisana wartość pierwszeństwa 100 i wszystkie inne reguły out-of-box są zwiększane.  
![Program PowerShell po zmianie](./media/how-to-connect-sync-change-the-configuration/powershell2.png)  

W razie potrzeby można mieć wiele reguł synchronizacji niestandardowej przy użyciu tej samej wartości **PrecedenceBefore.**

## <a name="enable-synchronization-of-usertype"></a>Włącz synchronizację typu użytkownika
Usługa Azure AD Connect obsługuje synchronizację atrybutu **UserType** dla obiektów **użytkownika** w wersji 1.1.524.0 i nowszej. W szczególności wprowadzono następujące zmiany:

- Schemat typu obiektu **Użytkownik** w usłudze Azure AD Connector jest rozszerzany o atrybut UserType, który jest ciągiem typu i jest wyceniany jednowartościowo.
- Schemat typu obiektu **Person** w metaverse jest rozszerzony o atrybut UserType, który jest ciągiem typu i jest wyceniany jednowartościowo.

Domyślnie atrybut UserType nie jest włączony do synchronizacji, ponieważ w lokalnej usłudze Active Directory nie ma odpowiedniego atrybutu UserType. Synchronizację należy włączyć ręcznie. Przed wykonaniem tej czynności należy wziąć pod uwagę następujące zachowanie wymuszane przez usługę Azure AD:

- Usługa Azure AD akceptuje tylko dwie wartości dla atrybutu UserType: **Member** i **Guest**.
- Jeśli atrybut UserType nie jest włączony do synchronizacji w usłudze Azure AD Connect, użytkownicy usługi Azure AD utworzone za pomocą synchronizacji katalogów będą mieli userType atrybut ustawiony na **Element członkowski**.
- Usługa Azure AD nie zezwala na zmianę atrybutu UserType u istniejących użytkowników usługi Azure AD przez usługę Azure AD Connect. Można go ustawić tylko podczas tworzenia użytkowników usługi Azure AD i [zmienić za pomocą programu Powershell](/powershell/module/azuread/set-azureaduser?view=azureadps-2.0).

Przed włączeniem synchronizacji atrybutu UserType należy najpierw zdecydować, w jaki sposób atrybut jest pochodną lokalnej usługi Active Directory. Poniżej przedstawiono najczęstsze podejścia:

- Wyznacz nieużywane lokalne atrybuty USŁUGI AD (takie jak extensionAttribute1), który ma być używany jako atrybut źródłowy. Wyznaczony lokalny atrybut AD powinien mieć **ciąg**typu, być jednowącą i zawierać wartość **Element członkowski** lub **Gość**. 

    Jeśli wybierzesz tę opcję, należy upewnić się, że wyznaczony atrybut jest wypełniany poprawną wartością dla wszystkich istniejących obiektów użytkownika w lokalnej usłudze Active Directory, które są synchronizowane z usługą Azure AD przed włączeniem synchronizacji atrybutu UserType .

- Alternatywnie można wyprowadzić wartość dla UserType atrybut z innych właściwości. Na przykład chcesz zsynchronizować wszystkich użytkowników jako **Gość,** jeśli ich lokalny atrybut AD userPrincipalName kończy się na części <em>@partners.fabrikam123.org</em>domeny . 

    Jak wspomniano wcześniej, usługa Azure AD Connect nie zezwala na zmianę atrybutu UserType na istniejących użytkownikach usługi Azure AD przez usługę Azure AD Connect. W związku z tym należy upewnić się, że logika, którą zdecydowano jest zgodna z jak UserType atrybut jest już skonfigurowany dla wszystkich istniejących użytkowników usługi Azure AD w dzierżawie.

Kroki umożliwiające synchronizację atrybutu UserType można podsumować w następujący sposób:

1.  Wyłącz harmonogram synchronizacji i sprawdź, czy nie jest synchronizacja w toku.
2.  Dodaj atrybut źródłowy do lokalnego schematu łącznika usługi AD.
3.  Dodaj typ użytkownika do schematu łącznika usługi Azure AD.
4.  Utwórz regułę synchronizacji przychodzącej, aby przepływać wartość atrybutu z lokalnej usługi Active Directory.
5.  Utwórz regułę synchronizacji ruchu wychodzącego, aby przepłynąć wartość atrybutu do usługi Azure AD.
6.  Uruchom pełny cykl synchronizacji.
7.  Włącz harmonogram synchronizacji.

>[!NOTE]
> Pozostała część tej sekcji obejmuje te kroki. Są one opisane w kontekście wdrożenia usługi Azure AD z topologii jednego lasu i bez reguł synchronizacji niestandardowej. Jeśli masz topologię wielu lasów, skonfigurowane niestandardowe reguły synchronizacji lub serwer przejściowy, musisz odpowiednio dostosować te kroki.

### <a name="step-1-disable-the-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Krok 1: Wyłącz harmonogram synchronizacji i sprawdź, czy nie ma synchronizacji w toku
Aby uniknąć eksportowania niezamierzonych zmian do usługi Azure AD, upewnij się, że synchronizacja nie ma miejsca, gdy jesteś w trakcie aktualizowania reguł synchronizacji. Aby wyłączyć wbudowany harmonogram synchronizacji:

 1. Uruchom sesję programu PowerShell na serwerze usługi Azure AD Connect.
 2. Wyłącz zaplanowaną synchronizację, uruchamiając `Set-ADSyncScheduler -SyncCycleEnabled $false`polecenie cmdlet .
 3. Otwórz Menedżera usług synchronizacji, przechodząc do **usługi Uruchom** > **synchronizację**.
 4. Przejdź do karty **Operacje** i upewnij się, że nie ma operacji o stanie *w toku*.

### <a name="step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema"></a>Krok 2: Dodawanie atrybutu źródłowego do lokalnego schematu łącznika usługi AD
Nie wszystkie atrybuty usługi Azure AD są importowane do lokalnego obszaru łącznika usługi AD. Aby dodać atrybut źródłowy do listy importowanych atrybutów:

 1. Przejdź do karty **Łączniki** w Menedżerze usług synchronizacji.
 2. Kliknij prawym przyciskiem myszy lokalny łącznik usługi AD i wybierz polecenie **Właściwości**.
 3. W wyskakującym oknie dialogowym przejdź do karty **Wybierz atrybuty.**
 4. Upewnij się, że atrybut źródłowy jest zaznaczony na liście atrybutów.
 5. Kliknij **przycisk OK,** aby zapisać.
![Dodawanie atrybutu źródłowego do lokalnego schematu łącznika usługi AD](./media/how-to-connect-sync-change-the-configuration/usertype1.png)

### <a name="step-3-add-the-usertype-to-the-azure-ad-connector-schema"></a>Krok 3: Dodawanie typu użytkownika do schematu łącznika usługi Azure AD
Domyślnie userType atrybut nie jest importowany do obszaru połączenia usługi Azure AD. Aby dodać atrybut UserType do listy zaimportowanych atrybutów:

 1. Przejdź do karty **Łączniki** w Menedżerze usług synchronizacji.
 2. Kliknij prawym przyciskiem myszy łącznik **usługi Azure AD i** wybierz polecenie **Właściwości**.
 3. W wyskakującym oknie dialogowym przejdź do karty **Wybierz atrybuty.**
 4. Upewnij się, że atrybut UserType jest zaznaczony na liście atrybutów.
 5. Kliknij **przycisk OK,** aby zapisać.

![Dodawanie atrybutu źródłowego do schematu usługi Azure AD Connector](./media/how-to-connect-sync-change-the-configuration/usertype2.png)

### <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>Krok 4: Tworzenie reguły synchronizacji przychodzącej w celu przepływu wartości atrybutu z lokalnej usługi Active Directory
Reguła synchronizacji przychodzącej umożliwia przepływ wartości atrybutu z atrybutu źródłowego z lokalnej usługi Active Directory do metaverse:

1. Otwórz Edytor reguł synchronizacji, przechodząc do **edytora** > **reguł synchronizacji**.
2. Ustaw filtr wyszukiwania **Kierunek** jako **Przychodzący**.
3. Kliknij przycisk **Dodaj nową regułę,** aby utworzyć nową regułę przychodzącą.
4. Na karcie **Opis** podaj następującą konfigurację:

    | Atrybut | Wartość | Szczegóły |
    | --- | --- | --- |
    | Nazwa | *Podaj nazwę* | Na przykład *In from AD — UserType* |
    | Opis | *Podaj opis* |  |
    | Podłączony system | *Wybieranie lokalnego łącznika usługi AD* |  |
    | Typ obiektu systemu podłączonego | **Użytkownik** |  |
    | Typ obiektu Metaverse | **Person (Osoba)** |  |
    | Typ łącza | **Dołącz** |  |
    | Pierwszeństwo | *Wybierz liczbę z 1–99* | 1–99 jest zarezerwowany dla reguł synchronizacji niestandardowej. Nie należy wybierać wartości używanej przez inną regułę synchronizacji. |

5. Przejdź do karty **Filtr zakresu** i dodaj **pojedynczą grupę filtrów zakresu** z następującą klauzulą:

    | Atrybut | Operator | Wartość |
    | --- | --- | --- |
    | scripta języka administracyjnego | NOTSTARTWITH (NIEROZPOCZĘCI) | Użytkownika\_ |

    Filtr zakresu określa, do których lokalnych obiektów usługi AD jest stosowana ta reguła synchronizacji przychodzącej. W tym przykładzie używamy tego samego filtru zakresu *używanego* w reguły synchronizacji in from AD — User Common out-of-box, która uniemożliwia stosowanie reguły synchronizacji do obiektów użytkownika utworzonych za pośrednictwem funkcji zapisywania zwrotnego użytkownika usługi Azure AD. Może być konieczne dostosowanie filtru zakresu zgodnie z wdrożeniem usługi Azure AD Connect.

6. Przejdź do karty **Transformacja** i zaimplementuj żądaną regułę transformacji. Na przykład jeśli wyznaczono nieużywane lokalne atrybuty AD (takie jak extensionAttribute1) jako atrybut źródłowy dla UserType, można zaimplementować przepływ atrybutów bezpośrednich:

    | Typ przepływu | Atrybut docelowy | Element źródłowy | Złóż wniosek raz | Typ scalania |
    | --- | --- | --- | --- | --- |
    | Direct | UserType | extensionAttribute1 | Niezaznaczone | Aktualizacja |

    W innym przykładzie chcesz wyprowadzić wartość dla usertype atrybut z innych właściwości. Na przykład chcesz zsynchronizować wszystkich użytkowników jako Gość, jeśli ich lokalny atrybut AD userPrincipalName kończy się na części <em>@partners.fabrikam123.org</em>domeny . Można zaimplementować wyrażenie w ten sposób:

    | Typ przepływu | Atrybut docelowy | Element źródłowy | Złóż wniosek raz | Typ scalania |
    | --- | --- | --- | --- | --- |
    | Wyrażenie | UserType | IIF(IsPresent([userPrincipalName]),IIF(CBool(InStr(LCase([userPrincipalName])"@partners.fabrikam123.org")=0),"Member","Gość",Error("UserPrincipalName nie jest obecny w celu określenia typu użytkownika")) | Niezaznaczone | Aktualizacja |

7. Kliknij **przycisk Dodaj,** aby utworzyć regułę ruchu przychodzącego.

![Tworzenie reguły synchronizacji przychodzącej](./media/how-to-connect-sync-change-the-configuration/usertype3.png)

### <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>Krok 5: Tworzenie reguły synchronizacji ruchu wychodzącego w celu prze przepływania wartości atrybutu do usługi Azure AD
Reguła synchronizacji ruchu wychodzącego umożliwia przepływ wartości atrybutu z metaverse do atrybutu UserType w usłudze Azure AD:

1. Przejdź do Edytora reguł synchronizacji.
2. Ustaw kierunek filtru wyszukiwania **jako** **Wychodzący**.
3. Kliknij przycisk **Dodaj nową regułę.**
4. Na karcie **Opis** podaj następującą konfigurację:

    | Atrybut | Wartość | Szczegóły |
    | ----- | ------ | --- |
    | Nazwa | *Podaj nazwę* | Na przykład Out to *AAD — UserType* |
    | Opis | *Podaj opis* ||
    | Podłączony system | *Wybierz złącze AAD* ||
    | Typ obiektu systemu podłączonego | **Użytkownik** ||
    | Typ obiektu Metaverse | **Person (Osoba)** ||
    | Typ łącza | **Dołącz** ||
    | Pierwszeństwo | *Wybierz liczbę z 1–99* | 1–99 jest zarezerwowany dla reguł synchronizacji niestandardowej. Nie należy wybierać wartości używanej przez inną regułę synchronizacji. |

5. Przejdź do karty **Filtr zakresu** i dodaj **pojedynczą grupę filtrów zakresu** z dwiema klauzulami:

    | Atrybut | Operator | Wartość |
    | --- | --- | --- |
    | SourceObjectType | Równe | Użytkownik |
    | chmuraMastered | NOTEQUAL (100) | True |

    Filtr zakresu określa, do których obiektów usługi Azure AD jest stosowana ta reguła synchronizacji ruchu wychodzącego. W tym przykładzie używamy tego samego filtru zakresu z out *do AD — tożsamość użytkownika* out-of-box reguły synchronizacji. Zapobiega to stosowaniu reguły synchronizacji do obiektów użytkownika, które nie są synchronizowane z lokalnej usługi Active Directory. Może być konieczne dostosowanie filtru zakresu zgodnie z wdrożeniem usługi Azure AD Connect.

6. Przejdź do karty **Transformacja** i zaimplementuj następującą regułę transformacji:

    | Typ przepływu | Atrybut docelowy | Element źródłowy | Złóż wniosek raz | Typ scalania |
    | --- | --- | --- | --- | --- |
    | Direct | UserType | UserType | Niezaznaczone | Aktualizacja |

7. Kliknij **przycisk Dodaj,** aby utworzyć regułę ruchu wychodzącego.

![Tworzenie reguły synchronizacji ruchu wychodzącego](./media/how-to-connect-sync-change-the-configuration/usertype4.png)

### <a name="step-6-run-a-full-synchronization-cycle"></a>Krok 6: Uruchom pełny cykl synchronizacji
Ogólnie rzecz biorąc, pełny cykl synchronizacji jest wymagany, ponieważ dodaliśmy nowe atrybuty do schematów usługi Active Directory i usługi Azure AD Connector i wprowadziliśmy niestandardowe reguły synchronizacji. Chcesz zweryfikować zmiany przed wyeksportowaniem ich do usługi Azure AD. 

Poniższe kroki można wykonać, aby zweryfikować zmiany podczas ręcznego uruchamiania kroków, które składają się na pełny cykl synchronizacji.

1. Uruchamianie **pełnego importu** w **lokalnym łączniku usługi AD:**

   1. Przejdź do karty **Operacje** w Menedżerze usług synchronizacji.
   2. Kliknij prawym przyciskiem myszy **lokalny łącznik usługi AD i** wybierz polecenie **Uruchom**.
   3. W wyskakującym oknie dialogowym wybierz pozycję **Pełny import,** a następnie kliknij przycisk **OK**.
   4. Poczekaj na zakończenie operacji.

      > [!NOTE]
      > Można pominąć pełne importowanie lokalnego łącznika usługi AD, jeśli atrybut źródłowy jest już uwzględniony na liście zaimportowanych atrybutów. Innymi słowy nie trzeba było wprowadzać żadnych zmian podczas [kroku 2: Dodaj atrybut źródłowy do lokalnego schematu łącznika usługi AD](#step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema).

2. Uruchom **pełne importowanie** w usłudze **Azure AD Connector:**

   1. Kliknij prawym przyciskiem myszy łącznik **usługi Azure AD i** wybierz polecenie **Uruchom**.
   2. W wyskakującym oknie dialogowym wybierz pozycję **Pełny import,** a następnie kliknij przycisk **OK**.
   3. Poczekaj na zakończenie operacji.

3. Sprawdź zmiany reguły synchronizacji w istniejącym obiekcie Użytkownik:

    Atrybut źródłowy z lokalnej usługi Active Directory i usertype z usługi Azure AD zostały zaimportowane do odpowiednich spacji łączników. Przed przystąpieniem do pełnej synchronizacji wykonaj **podgląd** istniejącego obiektu User w lokalnym obszarze łącznika usługi AD. Wybrany obiekt powinien być wypełniony atrybutem źródłowym.
    
    Pomyślny **podgląd** z usertype wypełnione w metaverse jest dobrym wskaźnikiem, że zostały skonfigurowane reguły synchronizacji poprawnie. Aby uzyskać informacje dotyczące sposobu wykonywania **wersji zapoznawczej,** zobacz sekcję [Sprawdź zmianę](#verify-the-change).

4. Uruchamianie **pełnej synchronizacji** lokalnego **łącznika usługi AD:**

   1. Kliknij prawym przyciskiem myszy **lokalny łącznik usługi AD i** wybierz polecenie **Uruchom**.
   2. W wyskakującym oknie dialogowym wybierz pozycję **Pełna synchronizacja,** a następnie kliknij przycisk **OK**.
   3. Poczekaj na zakończenie operacji.

5. Sprawdź **oczekujące eksporty** do usługi Azure AD:

   1. Kliknij prawym przyciskiem myszy łącznik **usługi Azure AD i** wybierz pozycję Obszar **łącznika wyszukiwania**.
   2. W oknie **podręcznym Obszar łącznika wyszukiwania:**

      - Ustaw **zakres** **na Oczekujące na Eksport**.
      - Zaznacz wszystkie trzy pola wyboru: **Dodaj,** **Modyfikuj**i **Usuń**.
      - Kliknij przycisk **Wyszukaj,** aby wyświetlić listę obiektów ze zmianami do wyeksportowania. Aby sprawdzić zmiany dla danego obiektu, kliknij go dwukrotnie.
      - Sprawdź, czy zmiany są oczekiwane.

6. Uruchamianie **eksportu** w usłudze **Azure AD Connector:**

   1. Kliknij prawym przyciskiem myszy łącznik **usługi Azure AD i** wybierz polecenie **Uruchom**.
   2. W oknie dialogowym **Uruchamianie łącznika** wybierz pozycję **Eksportuj,** a następnie kliknij przycisk **OK**.
   3. Poczekaj na zakończenie eksportu do usługi Azure AD.

> [!NOTE]
> Te kroki nie obejmują pełnej synchronizacji i eksportu kroki w usłudze Azure AD Connector. Te kroki nie są wymagane, ponieważ wartości atrybutów są przepływające z lokalnej usługi Active Directory tylko do usługi Azure AD.

### <a name="step-7-re-enable-the-sync-scheduler"></a>Krok 7: Ponowne włączenie harmonogramu synchronizacji
Ponownie włącz wbudowany harmonogram synchronizacji:

1. Rozpocznij sesję programu PowerShell.
2. Ponownie włącz zaplanowaną synchronizację, uruchamiając `Set-ADSyncScheduler -SyncCycleEnabled $true`polecenie cmdlet .


## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o modelu konfiguracji w [opisie aprowizacji deklaratywnej](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Dowiedz się więcej o języku wyrażeń w [opisie wyrażeń deklaratywnej inicjowania obsługi administracyjnej](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).

**Tematy omówienie**

* [Synchronizacja usługi Azure AD Connect: zrozumienie i dostosowywanie synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
