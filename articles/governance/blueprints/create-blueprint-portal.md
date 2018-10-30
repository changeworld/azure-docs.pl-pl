---
title: Tworzenie strategii platformy Azure w portalu
description: Usługa Azure Blueprints umożliwia tworzenie, definiowanie i wdrażanie artefaktów.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: quickstart
ms.service: blueprints
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 6b7ca276f3273faa485d08633061f882493f72f7
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2018
ms.locfileid: "49647276"
---
# <a name="define-and-assign-an-azure-blueprint-in-the-portal"></a>Definiowanie i przypisywanie strategii platformy Azure w portalu

Znajomość sposobu tworzenia i przypisywania strategii na platformie Azure umożliwia organizacji definiowanie typowych wzorców spójności oraz tworzenie konfiguracji wielokrotnego użytku, które można szybko wdrażać, w oparciu o szablony usługi Resource Manager, zasady, zabezpieczenia itd. Z tego samouczka dowiesz się, jak za pomocą usługi Azure Blueprints wykonywać niektóre typowe zadania związane z tworzeniem, publikowaniem i przypisywaniem strategii w organizacji, takie jak:

> [!div class="checklist"]
> - Tworzenie nowej strategii i dodawanie różnych obsługiwanych artefaktów
> - Wprowadzanie zmian do istniejącej strategii, która wciąż znajduje się w stanie **Wersja robocza**
> - Oznaczanie strategii jako gotowej do przypisania za pomocą stanu **Opublikowano**
> - Przypisywanie strategii do istniejącej subskrypcji
> - Sprawdzanie stanu i postępu przypisanej strategii
> - Usuwanie strategii, która została przypisana do subskrypcji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

## <a name="create-a-blueprint"></a>Tworzenie strategii

Pierwszym krokiem podczas definiowania standardowego wzorca zgodności jest utworzenie strategii z dostępnych zasobów. W tym przykładzie utworzysz strategię o nazwie „MyBlueprint”, aby skonfigurować przypisania ról i zasad dla subskrypcji, dodasz grupę zasobów oraz utworzysz przypisanie roli i szablonu usługi Resource Manager w grupie zasobów.

1. Uruchom usługę Azure Blueprints w witrynie Azure Portal, klikając pozycję **Wszystkie usługi**, a następnie wyszukując i wybierając pozycję **Zasady** w okienku po lewej stronie. Na stronie **Zasady** kliknij pozycję **Strategie**.

1. Wybierz pozycję **Definicje strategii** w lewej części strony i kliknij przycisk **+ Utwórz strategię** znajdujący się u góry strony.

   - Ewentualnie kliknij pozycję **Utwórz** na stronie **Wprowadzenie**, aby przejść bezpośrednio do tworzenia strategii.

   ![Tworzenie strategii](./media/create-blueprint-portal/create-blueprint-button.png)

1. W polu **Nazwa strategii** podaj nazwę strategii, na przykład „MyBlueprint” (litery i cyfry, maksymalnie 48 znaków, ale bez spacji i znaków specjalnych). Pole **Opis strategii** pozostaw na razie puste.  W polu **Lokalizacja definicji** kliknij wielokropek po prawej stronie, wybierz [grupę zarządzania](../management-groups/overview.md), w której chcesz zapisać strategię, a następnie kliknij pozycję **Wybierz**.

   > [!NOTE]
   > Definicje strategii można zapisywać tylko w grupach zarządzania. Aby utworzyć pierwszą grupę zarządzania, wykonaj [te kroki](../management-groups/create.md).

1. Sprawdź, czy informacje są poprawne (wartości pól **Nazwa strategii** i **Lokalizacja definicji** nie można później zmienić), a następnie kliknij pozycję **Dalej: Artefakty** w dolnej części strony lub kartę **Artefakty** w górnej części strony.

1. Dodaj przypisanie roli w subskrypcji: kliknij lewym przyciskiem myszy wiersz **+ Dodaj artefakt...**  w obszarze **Subskrypcja**, aby otworzyć okno „Dodawanie artefaktu” po prawej stronie przeglądarki. W polu _Typ artefaktu_ wybierz wartość „Przypisanie roli”. W polu _Rola_ wybierz wartość „Współautor” i pozostaw pole _Dodaj użytkownika, aplikację lub grupę_ z zaznaczonym polem wyboru wskazującym **parametr dynamiczny**. Kliknij pozycję **Dodaj**, aby dodać ten artefakt do strategii.

   ![Artefakt — Przypisanie roli](./media/create-blueprint-portal/add-role-assignment.png)

   > [!NOTE]
   > Większość _artefaktów_ obsługuje parametry. Parametr z wartością przypisaną podczas tworzenia strategii to **parametr statyczny**. Parametr przypisywany podczas przypisywania strategii to **parametr dynamiczny**. Aby uzyskać więcej informacji, zobacz [Parametry strategii](./concepts/parameters.md).

1. Dodaj przypisanie zasad w subskrypcji: kliknij lewym przyciskiem myszy wiersz **+ Dodaj artefakt...**  bezpośrednio pod elementem **Subskrypcja**. W polu _Typ artefaktu_ wybierz wartość „Przypisanie zasad”. Zmień _Typ_ na „Wbudowane” i w polu _Wyszukiwanie_ wprowadź wartość „tag”. Kliknij poza polem _Wyszukiwanie_, aby zastosować filtrowanie. Wybierz opcję „Zastosuj tag i jego wartość domyślną do grup zasobów”, klikając ją. Kliknij pozycję **Dodaj**, aby dodać ten artefakt do strategii.

1. Kliknij wiersz przypisania zasad „Zastosuj tag i jego wartość domyślną do grup zasobów”. Zostanie otwarte okno, w którym można podać parametry do artefaktu jako część definicji strategii i ustawić parametry dla wszystkich przypisań (**parametry statyczne**) na podstawie tej strategii zamiast ustawiania ich podczas przypisywania (**parametry dynamiczne**). W tym przykładzie jest pożądane użycie **parametrów dynamicznych** podczas przypisywania strategii, więc pozostaw wartości domyślne i kliknij przycisk **Anuluj**.

1. Dodaj grupę zasobów w subskrypcji: kliknij lewym przyciskiem myszy wiersz **+ Dodaj artefakt...** w obszarze **Subskrypcja**. W polu _Typ artefaktu_ wybierz opcję „Grupa zasobów”. Pozostaw pola _Nazwa grupy zasobów_ i _Lokalizacja_ puste, ale upewnij się, że dla każdej właściwości jest zaznaczone pole wyboru, aby były one **parametrami dynamicznymi**. Kliknij pozycję **Dodaj**, aby dodać ten artefakt do strategii.

1. Dodaj szablon w ramach grupy zasobów: kliknij lewym przyciskiem myszy wiersz **+ Dodaj artefakt...** bezpośrednio pod wpisem **ResourceGroup**. W polu _Typ artefaktu_ wybierz wartość „Szablon usługi Resource Manager”, w polu _Nazwa wyświetlana artefaktu_ ustaw wartość „StorageAccount”, a pole _Opis_ pozostaw puste. Na karcie **Szablon** w polu edytora wklej poniższy szablon usługi Resource Manager. Po wklejeniu tego szablonu kliknij kartę **Parametry** i zwróć uwagę, że parametr szablonu **storageAccountType** i wartość domyślna **Standard_LRS** zostały automatycznie wykryte i wypełnione, ale skonfigurowane jako **parametr dynamiczny**. Usuń zaznaczenie pola wyboru i zwróć uwagę, że lista rozwijana zawiera tylko wartości zawarte w szablonie usługi Resource Manager w obszarze **allowedValues**. Zaznacz pole, aby ustawić je powrotem jako **parametr dynamiczny**. Kliknij pozycję **Dodaj**, aby dodać ten artefakt do strategii.

   > [!IMPORTANT]
   > W przypadku importowania szablonu upewnij się, że plik jest całkowicie w formacie JSON i nie zawiera kodu HTML. W przypadku wskazywania adresu URL w witrynie GitHub upewnij się, że została kliknięta opcja **RAW** (Plik nieprzetworzony), aby pobrać czysty plik JSON, a nie ten opakowany za pomocą kodu HTML do wyświetlania w witrynie GitHub. Jeśli zaimportowany szablon nie jest czystym plikiem JSON, wystąpi błąd.

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
           "storageAccountType": {
               "type": "string",
               "defaultValue": "Standard_LRS",
               "allowedValues": [
                   "Standard_LRS",
                   "Standard_GRS",
                   "Standard_ZRS",
                   "Premium_LRS"
               ],
               "metadata": {
                   "description": "Storage Account type"
               }
           }
       },
       "variables": {
           "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
       },
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts",
           "name": "[variables('storageAccountName')]",
           "apiVersion": "2016-01-01",
           "location": "[resourceGroup().location]",
           "sku": {
               "name": "[parameters('storageAccountType')]"
           },
           "kind": "Storage",
           "properties": {}
       }],
       "outputs": {
           "storageAccountName": {
               "type": "string",
               "value": "[variables('storageAccountName')]"
           }
       }
   }
   ```

   ![Artefakt — szablon usługi Resource Manager](./media/create-blueprint-portal/add-resource-manager-template.png)

1. Ukończona strategia powinna wyglądać podobnie do poniższej. Zauważ, że każdy artefakt ma informację „wypełniono _x_ z _y_ parametrów” w kolumnie _Parametry_. **Parametry dynamiczne** będą ustawiane każdorazowo podczas przypisywania strategii.

   ![Ukończona strategia](./media/create-blueprint-portal/completed-blueprint.png)

1. Teraz, kiedy wszystkie planowane artefakty zostały dodane, kliknij pozycję **Zapisz wersję roboczą** w dolnej części strony.

## <a name="edit-a-blueprint"></a>Edytowanie strategii

W ramach procedury [Tworzenie strategii](#create-a-blueprint) nie podano opisu strategii i nie dodano przypisania roli do nowej grupy zasobów. Oba te błędy można naprawić, wykonując następujące kroki:

1. Wybierz pozycję **Definicje strategii** w lewej części strony.

1. Na liście strategii kliknij prawym przyciskiem myszy tę, która została wcześniej utworzona, a następnie wybierz opcję **Edytuj strategię**.

1. W polu **Opis strategii** podaj informacje o strategii i artefaktach, które ją tworzą.  W tym przypadku wprowadź opis podobny do następującego: „Ta strategia ustawia zasady tagu i przypisanie roli w ramach subskrypcji, tworzy grupę ResourceGroup i wdraża szablon zasobów oraz przypisanie roli w tej grupie ResourceGroup”.

1. Kliknij pozycję **Dalej: Artefakty** w dolnej części strony lub kartę **Artefakty** w górnej części strony.

1. Dodaj przypisanie roli w ramach grupy zasobów: kliknij lewym przyciskiem myszy wiersz **+ Dodaj artefakt...** bezpośrednio pod wpisem **ResourceGroup**. W polu _Typ artefaktu_ wybierz wartość „Przypisanie roli”. W obszarze _Rola_ wybierz rolę „Właściciel” i usuń zaznaczenie dla pola _Dodaj użytkownika, aplikację lub grupę_, a następnie wyszukaj i wybierz użytkownika, aplikację lub grupę do dodania. Będzie to **parametr statyczny**, który będzie używany w każdym przypisaniu tej strategii. Kliknij pozycję **Dodaj**, aby dodać ten artefakt do strategii.

   ![Artefakt — Przypisanie roli nr 2](./media/create-blueprint-portal/add-role-assignment-2.png)

1. Ukończona strategia powinna wyglądać podobnie do poniższej. Zwróć uwagę, że nowo dodane przypisanie roli wyświetla informację **wypełniono 1 z 1 parametrów**, co oznacza, że jest to **parametr statyczny**.

   ![Ukończona strategia nr 2](./media/create-blueprint-portal/completed-blueprint-2.png)

1. Teraz, gdy strategia została zaktualizowana, kliknij pozycję **Zapisz wersję roboczą**.

## <a name="publish-a-blueprint"></a>Publikowanie strategii

Po dodaniu wszystkich zaplanowanych artefaktów do strategii czas na jej opublikowanie.
Opublikowanie strategii umożliwia przypisanie jej do subskrypcji.

1. Wybierz pozycję **Definicje strategii** w lewej części strony.

1. Na liście strategii kliknij prawym przyciskiem myszy tę, która została wcześniej utworzona, a następnie wybierz opcję **Opublikuj strategię**.

1. W wyświetlonym oknie dialogowym podaj **wersję** (litery, cyfry i łączniki, maksymalna długość 20 znaków), na przykład „wersja 1” i **uwagi dotyczące zmian** (opcjonalnie), na przykład „Pierwsza publikacja”.

1. Kliknij pozycję **Publikuj** w dolnej części strony.

## <a name="assign-a-blueprint"></a>Przypisywanie strategii

Po opublikowaniu strategii można przypisać ją do subskrypcji. Przypisz utworzoną przez siebie strategię do jednej z subskrypcji w Twojej hierarchii grup zarządzania.

1. Wybierz pozycję **Definicje strategii** w lewej części strony.

1. Na liście strategii kliknij prawym przyciskiem myszy tę, która została wcześniej utworzona (lub kliknij lewym przyciskiem myszy wielokropek), a następnie wybierz opcję **Przypisz strategię**.

1. Na stronie **Przypisywanie strategii** wybierz z listy rozwijanej **Subskrypcje** subskrypcje, w których chcesz wdrożyć tę strategię.

   > [!NOTE]
   > Dla każdej wybranej subskrypcji zostanie utworzone przypisanie, co umożliwia późniejsze zmiany w przypisaniu pojedynczej subskrypcji bez wymuszania zmian w pozostałych wybranych subskrypcjach.

1. W polu **Przypisana nazwa** podaj unikatową nazwę tego przypisania.

1. W polu **Lokalizacja** wybierz region, w którym ma zostać utworzona tożsamość zarządzana. Usługa Azure Blueprint używa tej tożsamości zarządzanej do wdrażania wszystkich artefaktów w przypisanej strategii. Aby dowiedzieć się więcej, zobacz [Tożsamości zarządzane dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md).

1. Pozostaw listę rozwijaną **Wersja definicji strategii** wersji **opublikowanych** z wpisem „wersja 1” (domyślnie jako ostatnia **opublikowana** wersja).

1. Dla opcji **Blokowanie przypisania** pozostaw wartość domyślną **Nie blokuj**. Aby uzyskać więcej informacji, zobacz [blokowanie zasobów strategii](./concepts/resource-locking.md).

1. Dla przypisania roli na poziomie subskrypcji **[Grupa użytkowników lub nazwa aplikacji]: Współautor** wyszukaj i wybierz użytkownika, aplikację lub grupę.

1. Dla przypisania zasad na poziomie subskrypcji ustaw właściwość **Nazwa tagu** na wartość „CostCenter”, a właściwość **Wartość tagu** na wartość „ContosoIT”.

1. Dla grupy „ResourceGroup” podaj **nazwę** „StorageAccount” i wybierz **lokalizację** „Wschodnie stany USA 2” z listy rozwijanej.

   > [!NOTE]
   > Dla każdego artefaktu, który został dodany w ramach grupy zasobów podczas definiowania strategii, tworzone jest wcięcie w celu wyrównania tego artefaktu do grupy zasobów lub obiektu, z którym zostanie wdrożony. Artefakty, które nie przyjmują parametrów lub nie mają parametrów do zdefiniowania podczas przypisania, zostaną wyświetlone tylko jako informacje kontekstowe.

1. W szablonie „StorageAccount” usługi Azure Resource Manager wybierz wartość „Standard_GRS” dla parametru **storageAccountType**.

1. Przeczytaj informacje w polu w dolnej części strony, a następnie kliknij przycisk **przypisać**.

## <a name="track-deployment-of-a-blueprint"></a>Śledzenie wdrażania strategii

Po przypisaniu strategii do co najmniej jednej subskrypcji dzieją się dwie rzeczy:

- Strategia jest dodawana do strony **Przypisane strategie** dla subskrypcji, do której jest przypisana
- Rozpoczyna się proces wdrażania wszystkich artefaktów zdefiniowanych przez strategię

Teraz, gdy strategia została przypisana do subskrypcji, sprawdź postęp wdrażania.

1. Wybierz pozycję **Przypisane strategie** w lewej części strony.

1. Na liście strategii kliknij prawym przyciskiem myszy tę wcześniej przypisaną, a następnie wybierz opcję **Wyświetl szczegóły przypisania**.

   ![Wyświetlanie szczegółów przypisania](./media/create-blueprint-portal/view-assignment-details.png)

1. Na stronie **Szczegóły wdrożenia** sprawdź, czy wszystkie artefakty zostały pomyślnie wdrożone, i czy nie było żadnych błędów podczas wdrażania. Jeśli wystąpiły błędy, zobacz kroki w temacie [rozwiązywanie problemów ze strategią](./troubleshoot/general.md), aby ustalić, co poszło źle.

## <a name="unassign-a-blueprint"></a>Cofanie przypisania strategii

Strategie można usunąć z subskrypcji, jeśli nie są już potrzebne lub zostały zastąpione nowszymi strategiami zawierającymi zaktualizowane wzorce, zasady i projekty. Po usunięciu strategii artefakty przypisane w jej ramach są pozostawiane. Aby usunąć przypisanie strategii, wykonaj następujące kroki:

1. Wybierz pozycję **Przypisane strategie** w lewej części strony.

1. Na liście strategii wybierz strategię, której przypisanie chcesz cofnąć, a następnie kliknij przycisk **Cofnij przypisanie strategii** u góry strony.

1. Przeczytaj komunikat potwierdzenia, a następnie kliknij przycisk **OK**.

## <a name="delete-a-blueprint"></a>Usuwanie strategii

1. Wybierz pozycję **Definicje strategii** w lewej części strony.

1. Kliknij prawym przyciskiem myszy strategię, którą chcesz usunąć, i wybierz polecenie **Usuń strategię**. Następnie w oknie dialogowym potwierdzenia kliknij przycisk **Tak**.

> [!NOTE]
> Usunięcie strategii za pomocą tej metody spowoduje również usunięcie wszystkich **opublikowanych wersji** wybranej strategii. Aby usunąć jedną wersję, otwórz strategię, kliknij kartę **Opublikowane wersje**, wybierz i kliknij wersję, którą chcesz usunąć, a następnie kliknij pozycję **Usuń tę wersję**. Ponadto strategii z przypisaniami nie można usunąć, dopóki wszystkie te przypisania strategii nie zostały usunięte.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [cyklu życia strategii](./concepts/lifecycle.md)
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](./concepts/parameters.md)
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](./concepts/sequencing-order.md)
- Dowiedz się, jak używać [blokowania zasobów strategii](./concepts/resource-locking.md)
- Dowiedz się, jak [zaktualizować istniejące przypisania](./how-to/update-existing-assignments.md)
- Rozwiązywanie problemów podczas przypisywania strategii za pomocą [ogólnych procedur rozwiązywania problemów](./troubleshoot/general.md)
