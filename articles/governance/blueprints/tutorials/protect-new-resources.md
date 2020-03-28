---
title: 'Samouczek: Ochrona nowych zasobów za pomocą zamków'
description: W tym samouczku używasz opcji blokad zasobów planów platformy Azure tylko do odczytu i Nie usuwaj, aby chronić nowo wdrożone zasoby.
ms.date: 11/21/2019
ms.topic: tutorial
ms.openlocfilehash: ee57ff0c08f4fb8aa710dd2fa4dcef664484973d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74327442"
---
# <a name="tutorial-protect-new-resources-with-azure-blueprints-resource-locks"></a>Samouczek: Ochrona nowych zasobów za pomocą blokad zasobów usługi Azure Blueprints

Za pomocą [blokad zasobów](../concepts/resource-locking.md)usługi Azure Blueprints można chronić nowo wdrożone zasoby przed ingerencją, nawet przez konto z rolą _właściciela._ Tę ochronę można dodać w definicjach planu zasobów utworzonych przez artefakt szablonu Menedżera zasobów.

W tym samouczku wykonaj następujące kroki:

> [!div class="checklist"]
> - Tworzenie definicji planu
> - Oznacz swoją definicję planu jako **opublikowaną**
> - Przypisywanie definicji planu do istniejącej subskrypcji
> - Sprawdzanie nowej grupy zasobów
> - Co niesesignować plan, aby usunąć zamki

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free) przed rozpoczęciem.

## <a name="create-a-blueprint-definition"></a>Tworzenie definicji planu

Najpierw utwórz definicję planu.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Na stronie **Wprowadzenie** po lewej stronie wybierz pozycję **Utwórz** w obszarze **Utwórz plan**.

1. Znajdź przykład planu **pustego planu** u góry strony. Wybierz **pozycję Rozpocznij od pustego planu**.

1. Wprowadź te informacje na karcie **Podstawy:**

   - **Nazwa planu:** Podaj nazwę kopii przykładu planu. W tym samouczku użyjemy nazwy **zablokowanego konta magazynu**.
   - **Opis planu:** Dodaj opis definicji planu. Użyj **do testowania blokowania zasobów planu na wdrożonych zasobach**.
   - **Lokalizacja definicji**: Wybierz przycisk wielokropka (...) a następnie wybierz grupę zarządzania lub subskrypcję, w którym chcesz zapisać definicję planu.

1. Wybierz kartę **Artefakty** u góry strony lub wybierz **pozycję Dalej: Artefakty** u dołu strony.

1. Dodawanie grupy zasobów na poziomie subskrypcji:
   1. Wybierz wiersz **Dodaj artefakt** w obszarze **Subskrypcja**.
   1. Wybierz **pozycję Grupa zasobów** w obszarze Typ **artefaktu**.
   1. Ustaw **nazwę wyświetlaną artefaktu** na **RGtoLock**.
   1. Pola **Nazwa grupy zasobów** i **Lokalizacja** należy pozostawić puste, ale upewnij się, że pole wyboru jest zaznaczone na każdej właściwości, aby uczynić je **dynamicznymi parametrami**.
   1. Wybierz **dodaj,** aby dodać artefakt do planu.

1. Dodawanie szablonu w grupie zasobów:
   1. Wybierz wiersz **Dodaj artefakt** pod wpisem **RGtoLock.**
   1. Wybierz **szablon Usługi Azure Resource Manager** w obszarze Typ **artefaktu,** ustaw **nazwę wyświetlaną artefaktu** na **Konto magazynu**i pozostaw **opis** jako pusty.
   1. Na karcie **Szablon** wklej następujący szablon Menedżera zasobów do pola edytora.
      Po wklejeniu w szablonie wybierz pozycję **Dodaj,** aby dodać artefakt do planu.

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
           "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
       },
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts",
           "name": "[variables('storageAccountName')]",
           "location": "[resourceGroup().location]",
           "apiVersion": "2018-07-01",
           "sku": {
               "name": "[parameters('storageAccountType')]"
           },
           "kind": "StorageV2",
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

1. Wybierz **pozycję Zapisz pochyłość** u dołu strony.

Ten krok tworzy definicję planu w wybranej grupie zarządzania lub subskrypcji.

Po **zapisaniu definicji planu powiodło się** powiadomienie portalu, przejdź do następnego kroku.

## <a name="publish-the-blueprint-definition"></a>Publikowanie definicji planu

Definicja planu została utworzona w twoim środowisku. Jest tworzony w trybie **roboczym** i musi zostać opublikowany, zanim będzie można go przypisać i wdrożyć.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Po lewej stronie wybierz stronę **Definicje planu.** Użyj filtrów, aby znaleźć definicję planu **konta zamkniętego magazynu,** a następnie wybierz ją.

1. Wybierz **pozycję Publikuj plan** u góry strony. W nowym okienku po prawej stronie wprowadź **1.0** jako **wersję**. Ta właściwość jest przydatna, jeśli później zostanie wniesiena zmiana. Wprowadź **informacje o zmianie,** takie jak **Pierwsza wersja opublikowana w celu blokowania wdrożonych zasobów planu**. Następnie wybierz **pozycję Publikuj** u dołu strony.

Ten krok umożliwia przypisanie planu do subskrypcji. Po opublikowaniu definicji planu nadal można wprowadzać zmiany. Jeśli wniesiesz zmiany, należy opublikować definicję z nową wartością wersji, aby śledzić różnice między wersjami tej samej definicji planu.

Po **opublikowaniu definicji planu publikowania powiodło** się powiadomienie portalu, przejdź do następnego kroku.

## <a name="assign-the-blueprint-definition"></a>Przypisywanie definicji planu

Po opublikowaniu definicji planu można przypisać ją do subskrypcji w grupie zarządzania, w której została zapisana. W tym kroku należy podać parametry, aby każde wdrożenie definicji planu unikatowe.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Po lewej stronie wybierz stronę **Definicje planu.** Użyj filtrów, aby znaleźć definicję planu **konta zamkniętego magazynu,** a następnie wybierz ją.

1. Wybierz **pozycję Przypisz plan** u góry strony definicji planu.

1. Podaj wartości parametrów dla przypisania planu:

   - **Podstawy**

     - **Subskrypcje:** Wybierz jedną lub więcej subskrypcji, które znajdują się w grupie zarządzania, w której zapisano definicję planu. Jeśli wybierzesz więcej niż jedną subskrypcję, dla każdej subskrypcji zostanie utworzone przypisanie przy użyciu wprowadzonych parametrów.
     - **Nazwa przydziału:** Nazwa jest wstępnie wypełniona na podstawie nazwy definicji planu. Chcemy, aby to przypisanie reprezentowało blokowanie nowej grupy zasobów, więc zmień nazwę przydziału na **przydział-locked-storageaccount-TestingBPLocks**.
     - **Lokalizacja:** Wybierz region, w którym ma być utworzona tożsamość zarządzana. Usługa Azure Blueprint używa tej tożsamości zarządzanej do wdrażania wszystkich artefaktów w przypisanej strategii. Aby dowiedzieć się więcej, zobacz [tożsamości zarządzane dla zasobów platformy Azure](../../../active-directory/managed-identities-azure-resources/overview.md).
       W tym samouczku wybierz **pozycję Wschodnie stany USA 2**.
     - **Wersja definicji planu:** Wybierz opublikowaną wersję **1.0** definicji planu.

   - **Przypisanie blokady**

     Wybierz tryb blokady planu **tylko do odczytu.** Aby uzyskać więcej informacji, zobacz [blokowanie zasobów strategii](../concepts/resource-locking.md).

   - **Tożsamość zarządzana**

     Użyj opcji domyślnej: **Przypisano system**. Aby uzyskać więcej informacji, zobacz [tożsamości zarządzane](../../../active-directory/managed-identities-azure-resources/overview.md).

   - **Parametry artefaktu**

     Parametry zdefiniowane w tej sekcji dotyczą artefaktu, w którym są zdefiniowane. Parametry te są [parametry dynamiczne,](../concepts/parameters.md#dynamic-parameters) ponieważ są one zdefiniowane podczas przypisywania planu. Dla każdego artefaktu ustaw wartość parametru na to, co widzisz w kolumnie **Wartość.**

     |Nazwa artefaktu|Typ artefaktu|Nazwa parametru|Wartość|Opis|
     |-|-|-|-|-|
     |Grupa zasobów RGtoLock|Grupa zasobów|Nazwa|TestingBPLocks|Definiuje nazwę nowej grupy zasobów, do aby zastosować blokady planu.|
     |Grupa zasobów RGtoLock|Grupa zasobów|Lokalizacja|Zachodnie stany USA 2|Definiuje lokalizację nowej grupy zasobów, do aby zastosować blokady planu.|
     |StorageAccount|Szablon usługi Resource Manager|typ konta magazynu (konto magazynu)|Standard_GRS|Jednostka SKU magazynu. Wartość domyślna to _Standard_LRS_.|

1. Po wprowadzeniu wszystkich parametrów wybierz pozycję **Przypisz** u dołu strony.

Ten krok wdraża zdefiniowane zasoby i konfiguruje wybrane **przypisanie blokady**. Stosowanie blokad planu może potrwać do 30 minut.

Po **przypisując definicji planu pomyślnie** powiadomienie portalu, przejdź do następnego kroku.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Inspekcja zasobów wdrożonych przez przydział

Przydział tworzy grupę zasobów _TestingBPLocks_ i konto magazynu wdrożone przez artefakt szablonu Menedżera zasobów. Nowa grupa zasobów i wybrany stan blokady są wyświetlane na stronie szczegółów przydziału.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Po lewej stronie wybierz stronę **Przypisane plany.** Użyj filtrów, aby znaleźć przypisanie planu **przydziału zablokowanego magazynu-TestingBPLocks,** a następnie wybierz je.

   Na tej stronie widzimy, że przypisanie powiodło się i że zasoby zostały wdrożone z nowym stanem blokady planu. Jeśli przypisanie zostanie zaktualizowane, w części rozwijanej **Operacja przydziału** są wyświetlane szczegółowe informacje o wdrażaniu każdej wersji definicji. Można wybrać grupę zasobów, aby otworzyć stronę właściwości.

1. Wybierz grupę zasobów **TestingBPLocks.**

1. Po lewej stronie wybierz stronę **Kontrola dostępu (IAM).** Następnie wybierz kartę **Przypisania ról.**

   W tym miejscu widzimy, że przypisanie planu _przypisania planu przypisania przypisania do planu blokady blokad z blokadami z blokadami przypisania przypisania przypisania przypisania przypisania przypisania ma_ rolę _właściciela._ Ma tę rolę, ponieważ ta rola została użyta do wdrożenia i zablokowania grupy zasobów.

1. Wybierz kartę **Odmów przydziałów.**

   Przydział planu utworzył [przypisanie odmowy](../../../role-based-access-control/deny-assignments.md) w wdrożonej grupie zasobów w celu wymuszenia trybu **blokady** planu tylko do odczytu. Przypisanie odmowy uniemożliwia osobie z odpowiednimi prawami na karcie **Przypisania ról podejmowanie** określonych akcji. Przypisanie odmowy dotyczy _wszystkich podmiotów._

   Aby uzyskać informacje na temat wykluczania podmiotu z przypisania [odmowy,](../concepts/resource-locking.md#exclude-a-principal-from-a-deny-assignment)zobacz blokowanie zasobów planów .

1. Wybierz przypisanie odmowy, a następnie wybierz stronę **Odmowa uprawnień** po lewej stronie.

   Przypisanie odmowy uniemożliwia wszystkie **\*** operacje z konfiguracją i **działaniem,** ale umożliwia dostęp do odczytu, wykluczając ** \*/odczyt** za pośrednictwem **NotActions**.

1. W portalu Azure breadcrumb wybierz **TestingBPLocks - Kontrola dostępu (IAM)**. Następnie wybierz stronę **Przegląd** po lewej stronie, a następnie przycisk **Usuń grupę zasobów.** Wprowadź nazwę **TestingBPLocks,** aby potwierdzić usunięcie, a następnie wybierz **pozycję Usuń** u dołu okienka.

   Powiadomienie portalu **Usuń grupę zasobów TestingBPLocks nie powiodło się.** Błąd stwierdza, że chociaż konto ma uprawnienia do usuwania grupy zasobów, dostęp jest odrzucany przez przypisanie planu. Pamiętaj, że wybraliśmy tryb blokady planu **tylko do odczytu** podczas przypisywania planu. Blokada planu uniemożliwia kontu z uprawnieniami, nawet _właściciel,_ usunięcie zasobu. Aby uzyskać więcej informacji, zobacz [blokowanie zasobów strategii](../concepts/resource-locking.md).

Te kroki pokazują, że nasze wdrożone zasoby są teraz chronione za pomocą blokad planu, które zapobiegają niechcianemu usunięciu, nawet z konta, które ma uprawnienia do usuwania zasobów.

## <a name="unassign-the-blueprint"></a>Niepodpisaj planu

Ostatnim krokiem jest usunięcie przypisania definicji planu. Usunięcie przypisania nie powoduje usunięcia skojarzonych artefaktów.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Po lewej stronie wybierz stronę **Przypisane plany.** Użyj filtrów, aby znaleźć przypisanie planu **przydziału zablokowanego magazynu-TestingBPLocks,** a następnie wybierz je.

1. Wybierz **opcję Cozbuj plan** w górnej części strony. Przeczytaj ostrzeżenie w oknie dialogowym potwierdzenia, a następnie wybierz przycisk **OK**.

   Po usunięciu przypisania planu blokady planu są również usuwane. Zasoby można ponownie usunąć przez konto z odpowiednimi uprawnieniami.

1. Wybierz **grup zasobów** z menu Azure, a następnie wybierz **testingBPLocks**.

1. Po lewej stronie wybierz stronę **Kontrola dostępu (IAM),** a następnie wybierz kartę **Przypisania ról.**

Zabezpieczenia dla grupy zasobów pokazują, że przypisanie planu nie ma już dostępu _właściciela._

Po usunięciu **przypisania planu powiodło się** powiadomienie portalu, przejdź do następnego kroku.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu pracy z tym samouczkiem usuń następujące zasoby:

- Grupa zasobów _TestingBPLocks_
- Konto _zamkniętego konta przechowywania definicji_ planu

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak chronić nowe zasoby wdrożone za pomocą planów platformy Azure. Aby dowiedzieć się więcej o planach platformy Azure, przejdź do artykułu cyklu życia planu.

> [!div class="nextstepaction"]
> [Dowiedz się więcej o cyklu życia planu](../concepts/lifecycle.md)