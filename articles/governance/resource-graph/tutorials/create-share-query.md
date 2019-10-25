---
title: Tworzenie i udostępnianie zapytania w Azure Portal
description: W tym samouczku dowiesz się, jak utworzyć zapytanie grafu zasobów i udostępnić je innym osobom w Azure Portal.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: resource-graph
ms.openlocfilehash: 10f93f34923fb2399a4b2053167576ba004ae467
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72821683"
---
# <a name="tutorial-create-and-share-an-azure-resource-graph-query-in-azure-portal"></a>Samouczek: Tworzenie i udostępnianie kwerendy wykresu zasobów platformy Azure w Azure Portal

Eksplorator usługi Azure Resource Graph umożliwia zapisywanie zapytań grafu zasobów bezpośrednio w Azure Portal. Istnieją dwa typy zapytań, _prywatne_ i _udostępnione_. Zapytanie _prywatne_ jest zapisywane w ustawieniach Azure Portal, ale _udostępnione_ zapytanie jest zasobem Menedżer zasobów, którym można zarządzać za pomocą kontroli dostępu opartej na rolach (RBAC) i chronionych blokadami zasobów.

Zapisywanie zapytań w Azure Portal pozwala zaoszczędzić czas poświęcony na wyszukiwanie ulubionych lub często używanych zapytań. Podczas udostępniania zapytań, zespół może być spójny i powtarzalny. W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> - Tworzenie i usuwanie zapytania _prywatnego_
> - Utwórz zapytanie _udostępnione_
> - Odnajdź _udostępnione_ zapytania
> - Usuń _udostępnione_ zapytanie

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka potrzebna jest subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="create-and-delete-a-private-query"></a>Tworzenie i usuwanie zapytania prywatnego

Zapytania _prywatne_ są dostępne tylko dla konta, które je utworzyło. Ponieważ są one zapisywane w ustawieniach Azure Portal konta, można je tworzyć, używać i usuwać z wewnątrz Azure Portal. Zapytanie _prywatne_ nie jest zasobem Menedżer zasobów. Utwórz nowe zapytanie _prywatne_ , wykonując następujące czynności:

1. W menu portalu wybierz pozycję "wszystkie usługi" lub użyj pola wyszukiwania platformy Azure w górnej części wszystkich stron.
   Wyszukaj i wybierz pozycję "Eksplorator grafów zasobów".

1. Na karcie "kwerenda 1" na stronie Eksplorator zasobów platformy Azure wprowadź następujące zapytanie. Aby uzyskać informacje dotyczące tego zapytania, zobacz [przykłady — liczba maszyn wirtualnych według typu systemu operacyjnego](../samples/starter.md#count-virtual-machines-by-os-type).
   Wybierz pozycję **Uruchom zapytanie** , aby zobaczyć wyniki zapytania w dolnym okienku.

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

1. Wybierz pozycję **Zapisz** lub **Zapisz jako**, wprowadź _nazwę_ jako "Liczba maszyn wirtualnych według systemu operacyjnego", pozostaw _Typ_ jako "zapytanie prywatne", a następnie wybierz pozycję **Zapisz** w dolnej części okienka _Zapisz zapytanie_ . Tytuł karty zmieni się z "Query 1" na "Liczba maszyn wirtualnych według systemu operacyjnego".

1. Przejdź do Eksploratora grafu zasobów platformy Azure w Azure Portal, a następnie wróć do niego. Zapisane zapytanie nie jest już wyświetlane, a karta "Query 1" została zwrócona.

1. Wybierz pozycję **Otwórz zapytanie**. Sprawdź, czy _Typ_ to "zapytanie prywatne". Zapisane "Liczba maszyn wirtualnych według systemu operacyjnego" teraz pojawiają się na liście _Nazwa zapytania_ . Wybierz łącze tytuł zapisanego zapytania i zostanie załadowane do nowej karty z tą nazwą zapytania.

   > [!NOTE]
   > Gdy zapisane zapytanie jest otwarte, a na karcie zostanie wyświetlona jego _Nazwa_, przycisk **Zapisz** aktualizuje go przy użyciu wszelkich wprowadzonych zmian. Aby utworzyć nowe zapisane zapytanie, użyj polecenia **Zapisz jako** i postępuj zgodnie z instrukcjami, tak jakby były to nowe zapisane zapytanie.

1. Aby usunąć zapisane zapytanie, wybierz pozycję **Otwórz zapytanie** ponownie i sprawdź, czy jest to _Typ_ "zapytanie prywatne". W wierszu zapisywanych zapytań "Liczba maszyn wirtualnych według systemu operacyjnego" Wybierz ikonę kosza. W oknie dialogowym potwierdzenia wybierz pozycję **tak** , aby zakończyć usuwanie zapytania. Następnie zamknij okienko _Otwórz zapytanie_ .

## <a name="create-a-shared-query"></a>Utwórz zapytanie udostępnione

W przeciwieństwie do zapytania _prywatnego_ , _udostępnione_ zapytanie jest zasobem Menedżer zasobów. Oznacza to, że zapytanie jest zapisywane w grupie zasobów, może być zarządzane i kontrolowane za pomocą RBAC, a nawet być chronione przy użyciu blokad zasobów. Jako zasób, każdy z odpowiednimi uprawnieniami może go zobaczyć i używać. Utwórz nowe zapytanie _udostępnione_ , wykonując następujące czynności:

1. W menu portalu wybierz pozycję "wszystkie usługi" lub użyj pola wyszukiwania platformy Azure w górnej części wszystkich stron.
   Wyszukaj i wybierz pozycję "Eksplorator grafów zasobów".

1. Na karcie "kwerenda 1" na stronie Eksplorator zasobów platformy Azure wprowadź następujące zapytanie. Aby uzyskać informacje dotyczące tego zapytania, zobacz [przykłady — liczba maszyn wirtualnych według typu systemu operacyjnego](../samples/starter.md#count-virtual-machines-by-os-type).
   Wybierz pozycję **Uruchom zapytanie** , aby wyświetlić wyniki zapytania w dolnym okienku.

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

1. Wybierz pozycję **Zapisz** lub **Zapisz jako**.

   ![Zapisz nowe zapytanie przy użyciu przycisku Zapisz](../media/create-share-query/save-shared-query-buttons.png)

1. W okienku _Zapisz zapytanie_ wprowadź _nazwę_ jako "Liczba maszyn wirtualnych według systemu operacyjnego", Zmień _Typ_ na "udostępnione zapytanie", ustaw _Opis_ na "Liczba maszyn wirtualnych według typu systemu operacyjnego" i wybierz _subskrypcję_ , w której utworzono zasób zapytania. Pozostaw zaznaczone pole wyboru "Publikuj w usłudze Resource-Graph-regrafs", a w _lokalizacji grupy zasobów_ ustaw wartość "(USA) zachodnie stany USA". Następnie wybierz pozycję **Zapisz** w dolnej części okienka _Zapisz zapytanie_ . Tytuł karty zmieni się z "Query 1" na "Liczba maszyn wirtualnych według systemu operacyjnego". Używana jest Grupa zasobów po raz pierwszy "Zażądaj grafu", co powoduje utworzenie grupy zasobów.

   ![Zapisz nowe zapytanie jako zapytanie udostępnione](../media/create-share-query/save-shared-query-window.png)

   > [!NOTE]
   > W razie potrzeby usuń zaznaczenie, aby podać nazwę istniejącej grupy zasobów, w której ma zostać zapisane zapytanie udostępnione. Użycie domyślnej nazwy grupy zasobów dla zapytań sprawia, że zapytania _udostępnione_ są łatwiejsze do odnajdywania. Bardziej oczywiste jest również przeznaczenie tej grupy zasobów. Jednak wybranie istniejącej grupy zasobów może zostać wykonane ze względów bezpieczeństwa w oparciu o istniejące uprawnienia.

1. Przejdź do Eksploratora grafu zasobów platformy Azure w Azure Portal, a następnie wróć do niego. Zapisane zapytanie nie jest już wyświetlane, a karta "Query 1" została zwrócona.

1. Wybierz pozycję **Otwórz zapytanie**. Sprawdź, czy _Typ_ to "udostępnione zapytanie" i czy kombinacja _subskrypcji_ i _grupy zasobów_ jest zgodna z miejscem, w którym zapisano zapytanie. Zapisane "Liczba maszyn wirtualnych według systemu operacyjnego" teraz pojawiają się na liście _Nazwa zapytania_ . Wybierz łącze tytuł zapisanego zapytania i zostanie załadowane do nowej karty z tą nazwą zapytania. Jako zapytanie _udostępnione_ wyświetla ikonę na karcie obok tytułu oznaczającego go jako udostępniony.

   ![Pokaż udostępnione zapytanie z ikoną](../media/create-share-query/show-saved-shared-query.png)

   > [!NOTE]
   > Gdy zapisane zapytanie jest otwarte, a na karcie zostanie wyświetlona jego _Nazwa_, przycisk **Zapisz** aktualizuje go przy użyciu wszelkich wprowadzonych zmian. Aby utworzyć nowe zapisane zapytanie, użyj polecenia **Zapisz jako** i postępuj zgodnie z instrukcjami, tak jakby były to nowe zapisane zapytanie.

## <a name="discover-shared-queries"></a>Odnajdź udostępnione zapytania

Ponieważ _udostępnione_ zapytanie jest zasobem Menedżer zasobów, można je znaleźć na kilka sposobów:

- W Eksploratorze grafu zasobów wybierz pozycję **Otwórz zapytanie** i ustaw _Typ_ na "udostępnione zapytanie"
- Strona portalu kwerendy grafu zasobów
- Grupa zasobów, w której została zapisana
- Z zapytaniem do grafu zasobów

### <a name="view-resource-graph-queries"></a>Wyświetl zapytania grafu zasobów

W Azure Portal na stronie kwerendy grafu zasobów są wyświetlane zapytania _udostępnione_ , do których zalogowane konto ma dostęp. Ta strona umożliwia filtrowanie według nazwy, subskrypcji, grupy zasobów i innych właściwości zapytania grafu zasobów. Zapytania dotyczące grafów zasobów można także oznaczyć, wyeksportować i usunąć przy użyciu tego interfejsu.

Wybranie jednego z zapytań spowoduje otwarcie strony zapytania wykresu zasobów. Podobnie jak w przypadku innych zasobów Menedżer zasobów, ta strona oferuje Interaktywny przegląd wraz z dziennikiem aktywności, kontrolą dostępu i tagami. Blokadę zasobu można również zastosować bezpośrednio z tej strony.

Uzyskaj dostęp do strony zapytania grafu zasobów z menu Portal, wybierając pozycję "wszystkie usługi" lub korzystając z pola wyszukiwania platformy Azure w górnej części wszystkich stron. Wyszukaj i wybierz pozycję "Eksplorator grafów zasobów".

### <a name="list-resource-groups-resources"></a>Wyświetlanie listy zasobów grup zasobów

Zapytanie grafu zasobów jest wyświetlane wraz z innymi zasobami, które są częścią grupy zasobów.
Wybranie zapytania grafu zasobów spowoduje otwarcie strony dla tego zapytania. Opcje wielokropka lub kliknij prawym przyciskiem myszy działają tak samo, jak na stronie kwerendy wykresu zasobów.

### <a name="query-resource-graph"></a>Zapytanie grafu zasobów

Jako zasób Menedżer zasobów kwerendy grafu zasobów można znaleźć za pomocą zapytania do grafu zasobów.
Poniższe ograniczenia zapytania wykresu zasobów według typu `Microsoft.ResourceGraph/queries`, a następnie używają `project` do wyświetlania tylko nazwy, czasu modyfikacji i samego zapytania:

```kusto
Resources
| where type == "microsoft.resourcegraph/queries"
| project name, properties.timeModified, properties.query
```

## <a name="delete-a-shared-query"></a>Usuń udostępnione zapytanie

Jeśli zapytanie _udostępnione_ nie jest już potrzebne, usuń je. Usunięcie zapytania _udostępnionego_ spowoduje usunięcie rzeczywistego zasobu Menedżer zasobów. Wszystkie pulpity nawigacyjne, na których wykres wyników został przypięty, teraz wyświetlają komunikat o błędzie. Gdy zostanie wyświetlony komunikat o błędzie, użyj przycisku **Usuń z pulpitu nawigacyjnego** , aby wyczyścić pulpit nawigacyjny.

Zapytanie _udostępnione_ można usunąć z następujących interfejsów:
- Strona zapytania grafu zasobów
- Strona kwerendy wykresu zasobów
- Eksplorator wykresu zasobów — Otwórz stronę zapytania
- Strona grupy zasobów

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu pracy z tym samouczkiem Usuń utworzone zapytania _prywatne_ i _udostępnione_ , jeśli nie będą już potrzebne.

## <a name="next-steps"></a>Następne kroki

- Uruchom pierwsze zapytanie przy użyciu [Azure Portal](../first-query-portal.md)
- Uzyskaj więcej informacji na temat [języka zapytań](../concepts/query-language.md)
- Dowiedz się, jak [eksplorować zasoby](../concepts/explore-resources.md)
- Zobacz przykłady [zapytań dla początkujących](../samples/starter.md)
- Zobacz przykłady [zapytań zaawansowanych](../samples/advanced.md)
- Podziel się opinią na platformie [UserVoice](https://feedback.azure.com/forums/915958-azure-governance)