---
title: Samouczek — ochrona nowe zasoby za pomocą blokad zasobów planu
description: W tym samouczku dowiesz się, jak za pomocą opcji blokad zasobów schematy Azure tylko do odczytu i nie usuwaj chronić nowo wdrożone zasoby.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/28/2019
ms.topic: tutorial
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 274c437acd8df50d631727fc352c4b9ebecead18
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479974"
---
# <a name="tutorial-protect-new-resources-with-azure-blueprints-resource-locks"></a>Samouczek: Ochrona nowe zasoby za pomocą blokad zasobów platformy Azure, plany

Przy użyciu Azure plany [blokad zasobów](../concepts/resource-locking.md), nowo wdrożone zasoby można chronić przed są naruszone, nawet przy użyciu konta z _właściciela_ roli. W definicjach planu zasoby utworzone w ramach artefaktu szablonu usługi Resource Manager, można dodać tę ochronę.

W tym samouczku zostaną wykonane następujące kroki:

> [!div class="checklist"]
> - Tworzenie definicji planu
> - Oznacz jako definicji planu **opublikowano**
> - Przypisywanie definicji planu do istniejącej subskrypcji
> - Sprawdź nową grupę zasobów
> - Cofnij przypisanie planu, aby usunąć blokady

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka potrzebna jest subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="create-a-blueprint-definition"></a>Tworzenie definicji planu

Najpierw utwórz definicji planu.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Na **wprowadzenie** strony po lewej stronie, wybierz opcję **Utwórz** w obszarze **Tworzenie planu**.

1. Znajdź **pustego planu** przykładowy plan w górnej części strony. Wybierz **rozpoczynać pustego planu**.

1. Wprowadź te informacje w **podstawy** karty:

   - **Nazwa planu**: Podaj nazwę dla tej kopii przykładu planu. W tym samouczku użyjemy nazwy **zablokowane storageaccount**.
   - **Opis planu**: Dodaj opis definicji planu. Użyj **testowania planu zasobów blokowania na wdrożone zasoby**.
   - **Lokalizacja definicji**: Wybierz przycisk wielokropka (...), a następnie wybierz grupę zarządzania lub subskrypcji, które można zapisać definicji planu do.

1. Wybierz **artefaktów** karcie w górnej części strony, lub wybierz **dalej: Artefakty** w dolnej części strony.

1. Dodaj grupę zasobów na poziomie subskrypcji:
   1. Wybierz **Dodawanie artefaktu** wiersz w obszarze **subskrypcji**.
   1. Wybierz **grupy zasobów** w obszarze **typ artefaktu**.
   1. Ustaw **nazwę wyświetlaną artefaktu** do **RGtoLock**.
   1. Pozostaw **nazwy grupy zasobów** i **lokalizacji** pola puste, ale upewnij się, że jest zaznaczone pole wyboru każdej właściwości, aby były one **parametrów dynamicznych**.
   1. Wybierz **Dodaj** Dodawanie artefaktu do planu.

1. Dodawanie szablonu w ramach grupy zasobów:
   1. Wybierz **Dodawanie artefaktu** wiersz w obszarze **RGtoLock** wpisu. 
   1. Wybierz **szablonu usługi Azure Resource Manager** w obszarze **typ artefaktu**ustaw **nazwę wyświetlaną artefaktu** do **StorageAccount**i pozostawić  **Opis** puste. 
   1. Na **szablonu** kartę, wklej następujący szablon usługi Resource Manager w oknie edytora. Po wklejeniu w szablonie wybierz **Dodaj** Dodawanie artefaktu do planu.

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

1. Wybierz **Zapisz wersję roboczą** w dolnej części strony.

Spowoduje to utworzenie definicji planu w wybrana grupa zarządzania lub subskrypcji.

Po **Zapisywanie definicji planu powiodło się** pojawi się powiadomienie portalu, przejdź do następnego kroku.

## <a name="publish-the-blueprint-definition"></a>Publikowanie definicji planu

Utworzono definicji planu w danym środowisku. Jest on tworzony w **projekt** tryb i musi zostać opublikowany zanim mogą zostać przypisane i wdrożony.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Wybierz **planu definicje** strony po lewej stronie. Użyj filtrów, aby znaleźć **zablokowane storageaccount** planu definicji, a następnie wybierz ją.

1. Wybierz **publikowania planu** w górnej części strony. W okienku po prawej stronie, wprowadź **1.0** jako **wersji**. Ta właściwość jest przydatna, jeśli później wprowadzić zmiany. Wprowadź **zmienić uwagi**, takich jak **pierwszej wersji opublikowana na potrzeby blokowania zasobów planu wdrożone**. Następnie wybierz pozycję **Publikuj** w dolnej części strony.

Ten krok sprawia, że można przypisać planu do subskrypcji. Po opublikowaniu definicji planu, możesz nadal wprowadzić zmiany. Jeśli wprowadzisz zmiany, trzeba opublikować definicji z nową wartością wersji do śledzenia różnic między wersjami tego samego definicji planu.

Po **publikowania planu definicji powiodło się** pojawi się powiadomienie portalu, przejdź do następnego kroku.

## <a name="assign-the-blueprint-definition"></a>Przypisywanie definicji planu

Po opublikowaniu definicji planu można przypisać je do subskrypcji w ramach grupy zarządzania, w którym został zapisany. W tym kroku musisz podać parametry unikatowość każdego wdrożenia definicji planu.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Wybierz **planu definicje** strony po lewej stronie. Użyj filtrów, aby znaleźć **zablokowane storageaccount** planu definicji, a następnie wybierz ją.

1. Wybierz **planu Przypisz** w górnej części strony definicji planu.

1. Podaj wartości parametrów dla przypisania planu:

   - **Podstawy**

     - **Subskrypcje**: Wybierz co najmniej jednej z subskrypcji, znajdujących się w grupie zarządzania, w której zapisano definicji planu. Jeśli wybierzesz więcej niż jedną subskrypcję, zostanie utworzony przypisania dla każdej subskrypcji przy użyciu parametrów, które można wprowadzić.
     - **Nazwa przypisania**: Nazwa jest wstępnie wypełniony na podstawie nazwy definicji planu. Chcemy, aby to przypisanie do reprezentowania blokowania nową grupę zasobów, więc zmieniasz nazwę przypisania do **przypisania — zablokowane — konto magazynu — TestingBPLocks**.
     - **Lokalizacja**: Wybierz region, w której chcesz utworzyć tożsamość zarządzaną. Usługa Azure Blueprint używa tej tożsamości zarządzanej do wdrażania wszystkich artefaktów w przypisanej strategii. Aby dowiedzieć się więcej, zobacz [Tożsamości zarządzane dla zasobów platformy Azure](../../../active-directory/managed-identities-azure-resources/overview.md).
       Na potrzeby tego samouczka wybierz **wschodnie stany USA 2**.
     - **Wersja definicji planu**: Wybierz opublikowanej wersji **1.0** definicji planu.

   - **Przypisanie blokady**

     Wybierz **tylko do odczytu** tryb blokady planu. Aby uzyskać więcej informacji, zobacz [blokowanie zasobów strategii](../concepts/resource-locking.md).

   - **Tożsamość zarządzana**

     Użyj opcji domyślnej: **Przypisanej w systemie**. Aby uzyskać więcej informacji, zobacz [zarządzanych tożsamości](../../../active-directory/managed-identities-azure-resources/overview.md).

   - **Parametry artefaktu**

     Parametry zdefiniowane w tej sekcji dotyczą artefaktu w ramach której są zdefiniowane. Te parametry są [parametrów dynamicznych](../concepts/parameters.md#dynamic-parameters) ponieważ są one definiowane podczas przypisywania planu. Dla każdego artefaktu należy ustawić wartość tego parametru, zostanie wyświetlony w **wartość** kolumny.

     |Nazwa artefaktu|Typ artefaktu|Nazwa parametru|Wartość|Opis|
     |-|-|-|-|-|
     |Grupa zasobów RGtoLock|Grupa zasobów|Name (Nazwa)|TestingBPLocks|Określa nazwę nowej grupy zasobów planu blokady do zastosowania.|
     |Grupa zasobów RGtoLock|Grupa zasobów|Lokalizacja|Zachodnie stany USA 2|Określa lokalizację, do nowej grupy zasobów do planu blokady do zastosowania.|
     |StorageAccount|Szablon usługi Resource Manager|storageAccountType (StorageAccount)|Standard_GRS|Jednostka SKU magazynu. Wartość domyślna to _Standard_LRS_.|

1. Po wprowadzeniu wszystkich parametrów, wybierz **przypisać** w dolnej części strony.

Ten krok umożliwia wdrażanie zdefiniowanych zasobów i konfiguruje wybrane **przypisania blokady**. Może potrwać do 30 minut, aby zastosować blokady planu.

Po **przypisywanie definicji planu powiodło się** pojawi się powiadomienie portalu, przejdź do następnego kroku.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Sprawdź zasoby wdrożone przez przypisanie

To przypisanie spowoduje utworzenie grupy zasobów _TestingBPLocks_ i konto magazynu, wdrożone przez artefaktu szablonu usługi Resource Manager. Nową grupę zasobów i stan wybranego blokady są wyświetlane na stronie szczegółów przypisania.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Wybierz **przypisane plany** strony po lewej stronie. Użyj filtrów, aby znaleźć **przypisania — zablokowane — konto magazynu — TestingBPLocks** planu przypisania, a następnie wybierz ją.

   Na tej stronie widać, że przypisanie zakończyło się pomyślnie i czy zasoby zostały wdrożone za pomocą nowego stan blokady planu. Jeśli przypisanie zostanie zaktualizowane, **operacji przypisania** listy rozwijanej przedstawia szczegółowe informacje dotyczące wdrażania dla każdej definicji wersji. Można wybrać grupę zasobów, aby otworzyć stronę właściwości.

1. Wybierz **TestingBPLocks** grupy zasobów.

1. Wybierz **kontrola dostępu (IAM)** strony po lewej stronie. Następnie wybierz pozycję **przypisań ról** kartę.

   Tutaj widzimy, że _przypisania — zablokowane — konto magazynu — TestingBPLocks_ przypisaniu planu _właściciela_ roli. Posiada tej roli, ponieważ ta rola została użyta do wdrożenia i blokowanie grupy zasobów.

1. Wybierz **Odmów przypisania** kartę.

   Przypisanie planu utworzone [Odmów przypisania](../../../role-based-access-control/deny-assignments.md) w grupie zasobów wdrożonego, aby wymusić **tylko do odczytu** tryb blokady planu. Przypisanie Odmów zapobiega osoba, która ma odpowiednie uprawnienia na **przypisań ról** kartę możliwość podejmowania określonych działań. Wpływa na przypisanie Odmów _wszystkich podmiotów zabezpieczeń_.

   Aby dowiedzieć się, jak wyłączanie podmiot zabezpieczeń z przypisania Odmów, zobacz [plany blokowania zasobów](../concepts/resource-locking.md#exclude-a-principal-from-a-deny-assignment).

1. Wybierz przypisanie Odmów, a następnie wybierz **odmowa uprawnień** strony po lewej stronie.

   Przypisanie Odmów uniemożliwia wszystkie operacje wykonywane **\*** i **akcji** konfiguracji, ale umożliwia dostęp do odczytu, wykluczając  **\* /odczyt**za pośrednictwem **NotActions**.

1. W portalu Azure obszarze nawigacji wybierz **TestingBPLocks — kontrola dostępu (IAM)** . Następnie wybierz pozycję **Przegląd** strony po lewej stronie i następnie **Usuń grupę zasobów** przycisku. Wprowadź nazwę **TestingBPLocks** potwierdzenie usunięcia, a następnie wybierz pozycję **Usuń** w dolnej części okienka.

   Powiadomienia portalu **TestingBPLocks nie powiodło się. Usuń grupę zasobów** pojawia się. Przez przypisanie planu opisu błędu wynika, że mimo, że Twoje konto ma uprawnienia do usuwania grupy zasobów, jest odmowa dostępu. Należy pamiętać, że wybrano **tylko do odczytu** planu tryb blokady podczas przypisywania planu. Plan zastosowana Blokada zapobiega konta z uprawnieniami, nawet _właściciela_, usunięcie tego zasobu. Aby uzyskać więcej informacji, zobacz [blokowanie zasobów strategii](../concepts/resource-locking.md).

Te kroki pokazują nasze zaawansowane zasoby wdrożone są teraz chronione z blokadami planu, które uniemożliwiają usunięcie niechcianych, nawet z konta które ma uprawnienia do usuwania zasobów.

## <a name="unassign-the-blueprint"></a>Cofnij przypisanie planu

Ostatnim krokiem jest, aby usunąć przypisanie definicji planu. Usuwanie przypisania nie powoduje usunięcia skojarzone artefakty.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Wybierz **przypisane plany** strony po lewej stronie. Użyj filtrów, aby znaleźć **przypisania — zablokowane — konto magazynu — TestingBPLocks** planu przypisania, a następnie wybierz ją.

1. Wybierz **Cofnij przypisanie planu** w górnej części strony. Przeczytaj ostrzeżenia w oknie dialogowym potwierdzenia, a następnie wybierz **OK**.

   Usunięcie przypisania planu blokad planu również zostaną usunięte. Ponownie można usunąć zasobów za pomocą konta z odpowiednimi uprawnieniami.

1. Wybierz **grup zasobów** z menu platformy Azure, a następnie wybierz **TestingBPLocks**.

1. Wybierz **kontrola dostępu (IAM)** strony po lewej stronie, a następnie wybierz pozycję **przypisań ról** kartę.

Zabezpieczeń dla grupy zasobów pokazuje, że przypisania planu nie ma już _właściciela_ dostępu.

Po **usuwanie przypisania planu powiodło się** pojawi się powiadomienie portalu, przejdź do następnego kroku.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Te zasoby zostaną usunięte po zakończeniu tego samouczka:

- Grupa zasobów _TestingBPLocks_
- Definicja planu _storageaccount zablokowane_

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [planu cyklu życia](../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../concepts/parameters.md).
- Dowiedz się, jak używać [planu blokowania zasobów](../concepts/resource-locking.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../concepts/sequencing-order.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../how-to/update-existing-assignments.md).
- [Rozwiązywanie problemów z](../troubleshoot/general.md) podczas przypisywania planu.
