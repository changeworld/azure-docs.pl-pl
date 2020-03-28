---
title: 'Samouczek: Zarządzanie zapytaniami w witrynie Azure portal'
description: W tym samouczku utworzysz zapytanie wykresu zasobów i udostępnisz nowe zapytanie innym osobom w witrynie Azure portal.
ms.date: 11/21/2019
ms.topic: tutorial
ms.openlocfilehash: 00cb3f95112804c81beb6bce6fc35891e6197e60
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74303947"
---
# <a name="tutorial-create-and-share-an-azure-resource-graph-query-in-the-azure-portal"></a>Samouczek: Tworzenie i udostępnianie kwerendy programu Azure Resource Graph w witrynie Azure portal

Usługa Azure Resource Graph Explorer umożliwia zapisywanie zapytań wykresu zasobów bezpośrednio w witrynie Azure portal. Istnieją dwa typy zapytań: _prywatne_ i _udostępnione_. Zapytanie prywatne jest zapisywane w ustawieniach witryny Azure portal. Natomiast kwerenda udostępniona jest zasobem Menedżera zasobów, którym można zarządzać za pomocą kontroli dostępu opartego na rolach (RBAC) i chronić je za pomocą blokad zasobów. Oba typy zapytań są szyfrowane w spoczynku.

Zapisując zapytania w witrynie Azure portal, można zaoszczędzić czas, który w przeciwnym razie można spędzić szukając ulubionych lub często używanych zapytań. Podczas udostępniania zapytań, pomagasz zespołowi realizować cele spójności i wydajności poprzez powtórzenie.

W tym samouczku wykonasz następujące zadania:

> [!div class="checklist"]
> - Tworzenie i usuwanie zapytania prywatnego
> - Tworzenie kwerendy udostępnionej
> - Odnajduj zapytania udostępnione
> - Usuwanie kwerendy udostępnionej

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka potrzebna jest subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="create-and-delete-a-private-query"></a>Tworzenie i usuwanie zapytania prywatnego

Zapytania prywatne są dostępne i widoczne tylko dla konta, które je tworzy. Ponieważ są one zapisywane w ustawieniach witryny Azure portal konta, mogą być tworzone, używane i usuwane tylko z wewnątrz witryny Azure portal. Kwerenda prywatna nie jest zasobem Menedżera zasobów. Aby utworzyć nową kwerendę prywatną, wykonaj następujące czynności:

1. Z menu portalu wybierz **pozycję Wszystkie usługi** lub użyj pola wyszukiwania platformy Azure u góry wszystkich stron. Wyszukaj, a następnie wybierz pozycję **Eksplorator wykresów zasobów**.

1. Na karcie **Kwerenda 1** na stronie Eksplorator wykresu zasobów platformy Azure wprowadź następującą kwerendę:

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

   Wybierz **pozycję Uruchom kwerendę,** aby wyświetlić wyniki kwerendy w dolnym okienku.

   Aby uzyskać więcej informacji na temat tej [kwerendy, zobacz Przykłady — zliczanie maszyn wirtualnych według typu systemu operacyjnego](../samples/starter.md#count-virtual-machines-by-os-type).


1. Wybierz **pozycję Zapisz** lub Zapisz **jako**, wprowadź **nazwę Zliczanie maszyn wirtualnych według systemu operacyjnego,** pozostaw ten typ jako **kwerendę prywatną,** a następnie wybierz pozycję **Zapisz** u dołu okienka **Zapisz kwerendę.** Tytuł karty zmienia się z **kwerendy 1** na **Zliczanie maszyn wirtualnych przez system operacyjny**.

1. Odejdź od Eksploratora wykresów zasobów platformy Azure w witrynie Azure portal, a następnie wróć do niego. Należy zauważyć, że zapisana kwerenda nie jest już wyświetlana i zwrócono kartę **Kwerenda 1.**

1. Wybierz **pozycję Otwórz kwerendę**. Upewnij się, że typem jest **kwerenda prywatna**. Zapisane nazwy **Zliczanie** maszyn wirtualnych przez system operacyjny są teraz wyświetlane na liście Nazwa **kwerendy.** Po wybraniu łącza do tytułu zapisanej kwerendy jest ono ładowane do nowej karty o nazwie tej kwerendy.

   > [!NOTE] 
   > Gdy zapisane zapytanie jest otwarte, a karta wyświetla jej nazwę, wybranie przycisku **Zapisz** aktualizuje ją z wszelkimi wprowadzonymi zmianami. Aby utworzyć nową zapisaną kwerendę z tej otwartej kwerendy, wybierz pozycję **Zapisz jako** i kontynuuj tak, jakby zapisywano zupełnie nową kwerendę.

1. Aby usunąć **zapisaną kwerendę,** wybierz ponownie pozycję Otwórz kwerendę i sprawdź, czy pole **Typ** jest ustawione na **Kwerenda prywatna**. W wierszu zapisanej `Count VMs by OS` kwerendy wybierz pozycję **Usuń** (ikona Kosza). W oknie dialogowym potwierdzenia wybierz pozycję **Tak,** aby zakończyć usuwanie kwerendy.
   Następnie zamknij **okienko Otwórz kwerendę.**

## <a name="create-a-shared-query"></a>Tworzenie kwerendy udostępnionej

W przeciwieństwie do zapytania prywatnego kwerenda udostępniona jest zasobem Menedżera zasobów. Fakt ten oznacza, że kwerenda zostanie zapisana w grupie zasobów, może być zarządzana i kontrolowana za pomocą RBAC, a nawet może być chroniona za pomocą blokad zasobów. Jako zasób każdy, kto ma odpowiednie uprawnienia, może go zobaczyć i używać.
Aby utworzyć nową kwerendę udostępnioną, wykonaj następujące czynności:

1. Z menu portalu wybierz pozycję **Wszystkie usługi**lub użyj pola wyszukiwania platformy Azure u góry wszystkich stron, aby wyszukać i wybrać **Eksplorator wykresu zasobów**.

1. Na karcie **Kwerenda 1** na stronie Eksplorator wykresu zasobów platformy Azure wprowadź następującą kwerendę:

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```
    
   Wybierz **pozycję Uruchom kwerendę,** aby wyświetlić wyniki kwerendy w dolnym okienku.

   Aby uzyskać więcej informacji na temat tej [kwerendy, zobacz Przykłady — zliczanie maszyn wirtualnych według typu systemu operacyjnego](../samples/starter.md#count-virtual-machines-by-os-type).

1. Wybierz **pozycję Zapisz** lub Zapisz **jako**.

   
   ![Zapisywanie nowej kwerendy za pomocą przycisku zapisz](../media/create-share-query/save-shared-query-buttons.png)

1. W okienku **Zapisz kwerendę** wprowadź dla nazwy liczbę **maszyn wirtualnych według systemu operacyjnego.**

1. Zmień typ **na Kwerenda udostępniona,** ustaw opis **na Liczba maszyn wirtualnych według typu systemu operacyjnego**i ustaw **subskrypcję,** aby określić, gdzie tworzony jest zasób kwerendy.

1. Pozostaw pole wyboru **Publikuj do grupy zasobów-rograf-kwerendy zasobów zaznaczone** pole wyboru, a **lokalizacja grupy zasobów ustawiona** na **(USA) West Central US**.

1. Wybierz **pozycję Zapisz** u dołu okienka Zapisz **kwerendę.** Tytuł karty zmienia się z **kwerendy 1** na **Zliczanie maszyn wirtualnych przez system operacyjny**. Przy pierwszym użyciu grupy **zasobów-wykres-kwerendy,** zapisz trwa dłużej niż oczekiwano, jak grupa zasobów zostanie utworzona.
   
   ![Zapisywanie nowej kwerendy jako kwerendy udostępnionej](../media/create-share-query/save-shared-query-window.png)

   > [!NOTE] 
   > Jeśli chcesz podać nazwę istniejącej grupy zasobów, aby zapisać kwerendę udostępnioną, można wyczyścić pole wyboru Grupa zasobów Publikowania **do kwerendy wykresu zasobów.** Użycie domyślnej nazwanej grupy zasobów dla kwerend ułatwia odnajdowanie zapytań udostępnionych. To również sprawia, że cel tej grupy zasobów bardziej widoczne. Można jednak wybrać istniejącą grupę zasobów ze względów bezpieczeństwa na podstawie istniejących uprawnień.

1. Odejdź od Eksploratora wykresów zasobów platformy Azure w witrynie Azure portal, a następnie wróć do niego. Należy zauważyć, że zapisana kwerenda nie jest już wyświetlana i zwrócono kartę **Kwerenda 1.**

1. Wybierz **pozycję Otwórz kwerendę**. Sprawdź, czy typ jest ustawiony na **Kwerenda udostępniona** i kombinacja **dopasowania grupy subskrypcja** i **zasoby,** w której kwerenda została zapisana. Zapisany element **Zliczanie maszyn wirtualnych według systemu operacyjnego** jest teraz wyświetlany na liście **Nazwa kwerendy.** Wybierz łącze do tytułu zapisanej kwerendy, aby załadować je do nowej karty o nazwie tej kwerendy. Jako zapytanie udostępnione wyświetla ikonę na karcie obok tytułu, oznaczającą ją jako udostępnioną.

   ![Pokaż kwerendę udostępnioną z ikoną](../media/create-share-query/show-saved-shared-query.png)

   > [!NOTE] 
   > Gdy zapisane zapytanie jest otwarte, a karta wyświetla jej nazwę, przycisk **Zapisz** aktualizuje ją o wszelkie wprowadzone zmiany. Aby utworzyć nową zapisaną kwerendę, wybierz pozycję **Zapisz jako** i kontynuuj tak, jakby zapisywano zupełnie nową kwerendę.

## <a name="discover-shared-queries"></a>Odnajduj zapytania udostępnione

Ponieważ kwerenda udostępniona jest zasobem Menedżera zasobów, istnieje kilka sposobów, aby go znaleźć:

- W Eksploratorze wykresów zasobów wybierz pozycję **Otwórz kwerendę** i ustaw typ **zapytania udostępnionego**.
- Na stronie Portalu zapytań wykresu zasobów.
- Z grupy zasobów, w którym została zapisana kwerenda udostępniona.
- Za pośrednictwem kwerendy do wykresu zasobów.

### <a name="view-resource-graph-queries"></a>Wyświetlanie zapytań wykresu zasobów

W witrynie Azure portal na stronie kwerend wykresu zasobów są wyświetlane zapytania udostępnione, do których ma dostęp zalogowane konto. Ta strona umożliwia filtrowanie według nazwy, subskrypcji, grupy zasobów i innych właściwości kwerendy Wykres zasobów. Za pomocą tego interfejsu można również oznaczać, eksportować i usuwać kwerendy wykresu zasobów.

Wybranie jednego z kwerend powoduje otwarcie strony kwerendy Wykres zasobów. Podobnie jak inne zasoby Menedżera zasobów, ta strona oferuje interaktywne omówienie wraz z dziennikiem aktywności, kontrolą dostępu i tagami. Blokadę zasobów można również zastosować bezpośrednio z tej strony.

Uzyskaj stronę Kwerend wykresu zasobów z menu portalu, wybierając **pozycję Wszystkie usługi** lub korzystając z pola wyszukiwania platformy Azure u góry wszystkich stron. Wyszukaj i wybierz **Eksploratora wykresów zasobów**.

### <a name="list-resource-groups-resources"></a>Lista zasobów grup zasobów zasobów

Kwerenda Wykres zasobów jest wyświetlana obok innych zasobów, które są częścią grupy zasobów.
Wybranie kwerendy Wykres zasobów powoduje otwarcie strony dla tej kwerendy. Opcje menu wielokropek i skrótów (wyzwalane przez kliknięcie prawym przyciskiem myszy) działają tak samo, jak na stronie kwerendy Wykres zasobów.

### <a name="query-resource-graph"></a>Wykres zasobu kwerendy

Kwerendy wykresu zasobów można znaleźć za pomocą kwerendy do wykresu zasobów. Następujące zapytania Wykres zasobów ogranicza `Microsoft.ResourceGraph/queries`według typu `project` , a następnie używa do listy tylko nazwę, czas zmodyfikowany i sama kwerenda:

```kusto
Resources
| where type == "microsoft.resourcegraph/queries"
| project name, properties.timeModified, properties.query
```

## <a name="delete-a-shared-query"></a>Usuwanie kwerendy udostępnionej

Jeśli zapytanie udostępnione nie jest już potrzebne, usuń ją. Usuwając kwerendę udostępnioną, należy usunąć odpowiedni zasób Menedżera zasobów. Wszystkie pulpity nawigacyjne, do których przypięto wykres wyników, są teraz wyświetlane komunikat o błędzie. Po wyświetleniu tego komunikatu o błędzie użyj przycisku **Usuń z pulpitu nawigacyjnego,** aby wyczyścić pulpit nawigacyjny.

Kwerendę udostępnioną można usunąć za pomocą następujących interfejsów:
- Strona kwerend wykresu zasobów
- Strona kwerendy Wykres zasobów
- Strona **Otwórz zapytanie** w Eksploratorze wykresów zasobów
- Strona Grupy zasobów

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu tego samouczka usuń utworzone zapytania prywatne i udostępnione, jeśli nie chcesz ich.

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono zapytania prywatne i udostępnione. Aby dowiedzieć się więcej o języku wykresu zasobów, przejdź do strony szczegółów języka kwerendy.

> [!div class="nextstepaction"]
> [Uzyskaj więcej informacji o języku kwerendy](../concepts/query-language.md)