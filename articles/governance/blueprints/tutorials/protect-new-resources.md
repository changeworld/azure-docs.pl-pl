---
title: Ochrona nowych zasobów za pomocą blokad zasobów strategii
description: Informacje na temat używania blokad zasobów platformy Azure, plany tylko do odczytu i nie usuwaj chronić nowo wdrożone zasoby.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/28/2019
ms.topic: tutorial
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: d315fb5fe3ce7844946e6a9405a9a5f6a0be8b9d
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59272278"
---
# <a name="protect-new-resources-with-azure-blueprints-resource-locks"></a>Ochrona nowe zasoby za pomocą blokad zasobów platformy Azure, plany

Plany usługi Azure [blokad zasobów](../concepts/resource-locking.md) umożliwia ochronę zasobów nowo wdrożone z są naruszone, nawet przy użyciu konta z _właściciela_ roli. Tę ochronę można dodać do zasobów w ramach artefaktu szablonu usługi Resource Manager, w definicji planu.

Omówione są następujące czynności:

> [!div class="checklist"]
> - Utwórz nową definicję planu
> - Oznacz jako definicji planu **opublikowano**
> - Przypisywanie definicji planu do istniejącej subskrypcji
> - Sprawdź nową grupę zasobów
> - Cofnij przypisanie planu, aby usunąć blokady

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka, jest potrzebna jest subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="create-new-blueprint-definition"></a>Tworzenie nowych definicji planu

Najpierw utwórz nową definicję planu.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Z **wprowadzenie** strony po lewej stronie, wybierz opcję **Utwórz** przycisku w obszarze _Tworzenie planu_.

1. Znajdź **pustego planu** przykładowy plan w górnej części strony i wybierz pozycję **rozpoczynać pustego planu**.

1. Wprowadź _podstawy_ próbki planu:

   - **Nazwa planu**: Podaj nazwę dla tej kopii przykładu planu. W tym samouczku użyjemy nazwy _zablokowane storageaccount_.
   - **Opis planu**: W tym artykule opisano definicji planu. Na użytek "planu zasobem testowym blokowania na wdrożone zasoby."
   - **Lokalizacja definicji**: Wielokropek i wybierz grupę zarządzania lub subskrypcji, które można zapisać definicji planu do.

1. Wybierz _artefaktów_ kartę w górnej części strony lub **dalej: Artefakty** w dolnej części strony.

1. Dodaj grupę zasobów w subskrypcji: Wybierz **+ Dodawanie artefaktu...**  wiersz w obszarze **subskrypcji**.
   W polu _Typ artefaktu_ wybierz opcję „Grupa zasobów”. Ustaw _nazwę wyświetlaną artefaktu_ do **RGtoLock**.
   Pozostaw pola _Nazwa grupy zasobów_ i _Lokalizacja_ puste, ale upewnij się, że dla każdej właściwości jest zaznaczone pole wyboru, aby były one **parametrami dynamicznymi**. Kliknij pozycję **Dodaj**, aby dodać ten artefakt do strategii.

1. Dodaj szablon w grupie zasobów: Wybierz **+ Dodawanie artefaktu...** wiersz w obszarze **RGtoLock** wpisu. W polu _Typ artefaktu_ wybierz wartość „Szablon usługi Resource Manager”, w polu _Nazwa wyświetlana artefaktu_ ustaw wartość „StorageAccount”, a pole _Opis_ pozostaw puste. Na karcie **Szablon** w polu edytora wklej poniższy szablon usługi Resource Manager. Po wklejeniu tego szablonu, wybierz **Dodaj** można dodać tego artefaktu do planu.

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

Gdy **Zapisywanie definicji planu powiodło się** pojawi się powiadomienie portalu, przejść do następnego kroku.

## <a name="publish-the-blueprint-definition"></a>Publikowanie definicji planu

Utworzono definicji planu w danym środowisku. Jest tworzony w **projekt** tryb i musi być **opublikowano** przed mogą zostać przypisane i wdrożony.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Wybierz **planu definicje** strony po lewej stronie. Użyj filtrów, aby znaleźć _zablokowane storageaccount_ planu definicji, a następnie wybierz ją.

1. Wybierz **publikowania planu** w górnej części strony. W okienku po prawej stronie, podaj **wersji** jako _1.0_. Ta właściwość jest przydatne w przypadku, jeśli wprowadzisz zmiany w dalszej części. Podaj **zmienić uwagi** takie jak "pierwsza wersja opublikowana na potrzeby blokowania planu wdrożonych zasobów." Następnie wybierz pozycję **Publikuj** w dolnej części strony.

Ten krok sprawia, że można przypisać planu do subskrypcji. Po opublikowaniu, nadal można wprowadzać zmiany. Dodatkowe zmiany wymagają publikowanie z nową **wersji** wartość, aby śledzić różnice między różnymi wersjami tego samego definicji planu.

Gdy **publikowania planu definicji powiodło się** pojawi się powiadomienie portalu, przejść do następnego kroku.

## <a name="assign-the-blueprint-definition"></a>Przypisywanie definicji planu

Po definicji planu została pomyślnie **opublikowano**, może ona zostać przypisana do subskrypcji w obrębie grupy zarządzania został zapisany w. Ten krok jest, gdzie parametry są przekazywane do unikatowość każdego wdrożenia definicji planu.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Wybierz **planu definicje** strony po lewej stronie. Użyj filtrów, aby znaleźć _zablokowane storageaccount_ planu definicji, a następnie wybierz ją.

1. Wybierz **planu Przypisz** w górnej części strony definicji planu.

1. Podaj wartości parametrów dla przypisania planu:

   - Podstawy

     - **Subskrypcje**: Wybierz co najmniej jednej z subskrypcji, które znajdują się w grupie zarządzania należy zapisać do definicji planu. Jeśli wybierzesz więcej niż jedną subskrypcję, przypisania zostaną utworzone dla każdego przy użyciu podanych parametrów.
     - **Nazwa przypisania**: Nazwa jest wstępnie wypełniona automatycznie na podstawie nazwy definicji planu. Chcemy, aby to przypisanie do reprezentowania blokowania nową grupę zasobów, więc zmieniasz nazwę przypisania do _przypisania — zablokowane — konto magazynu — TestingBPLocks_.
     - **Lokalizacja**: Wybierz region dla tożsamości zarządzanej, które zostały utworzone w. Usługa Azure Blueprint używa tej tożsamości zarządzanej do wdrażania wszystkich artefaktów w przypisanej strategii. Aby dowiedzieć się więcej, zobacz [Tożsamości zarządzane dla zasobów platformy Azure](../../../active-directory/managed-identities-azure-resources/overview.md).
       Na potrzeby tego samouczka wybierz _wschodnie stany USA 2_.
     - **Wersja definicji planu**: Wybierz **opublikowano** wersji _1.0_ definicji planu.

   - Zablokuj przypisanie

     Wybierz _tylko do odczytu_ tryb blokady planu. Aby uzyskać więcej informacji, zobacz [blokowanie zasobów strategii](../concepts/resource-locking.md).

   - Tożsamość zarządzana

     Pozostaw wartość domyślną _przypisanej w systemie_ opcji. Aby uzyskać więcej informacji, zobacz [zarządzanych tożsamości](../../../active-directory/managed-identities-azure-resources/overview.md).

   - Parametry artefaktu

     Parametry zdefiniowane w tej sekcji dotyczą artefaktu w ramach której jest zdefiniowany. Te parametry są [parametrów dynamicznych](../concepts/parameters.md#dynamic-parameters) ponieważ są one definiowane podczas przypisywania planu. Dla każdego artefaktu, należy ustawić wartość tego parametru, co to jest zdefiniowany w **wartość** kolumny.

     |Nazwa artefaktu|Typ artefaktu|Nazwa parametru|Wartość|Opis|
     |-|-|-|-|-|
     |Grupa zasobów RGtoLock|Grupa zasobów|Name (Nazwa)|TestingBPLocks|Określa nazwę nowej grupy zasobów planu blokady do zastosowania.|
     |Grupa zasobów RGtoLock|Grupa zasobów|Lokalizacja|Zachodnie stany USA 2|Określa lokalizację, do nowej grupy zasobów do planu blokady do zastosowania.|
     |StorageAccount|Szablon usługi Resource Manager|storageAccountType (StorageAccount)|Standard_GRS|Wybierz jednostkę SKU magazynu. Wartość domyślna to _Standard_LRS_.|

1. Po wprowadzeniu wszystkich parametrów, wybierz **przypisać** w dolnej części strony.

Ten krok umożliwia wdrażanie zdefiniowanych zasobów i konfiguruje wybrane **przypisania blokady**. Plan blokady może potrwać do 30 minut do zastosowania.

Gdy **przypisywanie definicji planu powiodło się** pojawi się powiadomienie portalu, przejść do następnego kroku.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Sprawdź zasoby wdrożone przez przypisanie

Przypisanie utworzona grupa zasobów _TestingBPLocks_ i konto magazynu, wdrożone przez artefaktu szablonu usługi Resource Manager. Nową grupę zasobów i stan wybranego blokady są wyświetlane na stronie szczegółów przypisania.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Wybierz **przypisane plany** strony po lewej stronie. Użyj filtrów, aby znaleźć _przypisania — zablokowane — konto magazynu — TestingBPLocks_ planu przypisania, a następnie wybierz ją.

   Na tej stronie widać przypisanie zakończyło się pomyślnie, a zasoby zostały wdrożone za pomocą nowego stan blokady planu. Jeśli przypisanie zostanie zaktualizowane, **operacji przypisania** listy rozwijanej przedstawia szczegółowe informacje dotyczące wdrażania dla każdej definicji wersji. Grupy zasobów można kliknąć, aby otworzyć bezpośrednio na stronie właściwości.

1. Wybierz **TestingBPLocks** grupy zasobów.

1. Wybierz **kontrola dostępu (IAM)** strony po lewej stronie i następnie **przypisań ról** kartę.

   Tutaj widzimy, że _przypisania — zablokowane — konto magazynu — TestingBPLocks_ przypisaniu planu _właściciela_ roli, ponieważ był używany do wdrażania i blokowanie grupy zasobów.

1. Wybierz **Odmów przypisania** kartę.

   Przypisanie planu utworzone [Odmów przypisania](../../../role-based-access-control/deny-assignments.md) w grupie zasobów wdrożonego, aby wymusić _tylko do odczytu_ tryb blokady planu. Przypisanie Odmów zapobiega osoba, która ma odpowiednie uprawnienia na _przypisań ról_ kartę możliwość podejmowania określonych działań. Wpływa na przypisanie Odmów _wszystkich podmiotów zabezpieczeń_.

   Aby dowiedzieć się, jak wyłączanie podmiot zabezpieczeń z przypisania Odmów, zobacz [plany blokowania zasobów](../concepts/resource-locking.md#exclude-a-principal-from-a-deny-assignment).

1. Wybierz przypisanie Odmów, a następnie wybierz **odmowa uprawnień** strony po lewej stronie.

   Przypisanie Odmów uniemożliwia wszystkie operacje wykonywane **\*** i **akcji** konfiguracji, ale umożliwia dostęp do odczytu, z wyłączeniem  **\* /odczyt**za pośrednictwem **NotActions**.

1. W portalu Azure obszarze nawigacji wybierz **TestingBPLocks — kontrola dostępu (IAM)**. Następnie wybierz pozycję **Przegląd** strony po lewej stronie i następnie **Usuń grupę zasobów** przycisku. Wprowadź nazwę _TestingBPLocks_ o potwierdzenie usunięcia i wybierz pozycję **Usuń** w dolnej części okienka.

   Powiadomienia portalu **TestingBPLocks nie powiodło się. Usuń grupę zasobów** jest wyświetlana. Przez przypisanie planu opisu błędu wynika, że gdy Twoje konto ma uprawnienia do usuwania grupy zasobów, jest odmowa dostępu. Należy pamiętać, że wybrano _tylko do odczytu_ planu tryb blokady podczas przypisywania planu. Plan zastosowana Blokada zapobiega konta z uprawnieniami, nawet _właściciela_, usunięcie tego zasobu. Aby uzyskać więcej informacji, zobacz [blokowanie zasobów strategii](../concepts/resource-locking.md).

Te kroki pokazują, że nasze zaawansowane zasoby wdrożone są teraz chronione z blokadami planu, zabronione usuwanie niepożądanych, nawet z konta z uprawnieniami.

## <a name="unassign-the-blueprint"></a>Cofnij przypisanie planu

Ostatnim krokiem jest, aby usunąć przypisanie definicji planu. Usuwanie przypisania nie powoduje usunięcia modyfikacji artefaktów.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Wybierz **przypisane plany** strony po lewej stronie. Użyj filtrów, aby znaleźć _przypisania — zablokowane — konto magazynu — TestingBPLocks_ planu przypisania, a następnie wybierz ją.

1. Wybierz **Cofnij przypisanie planu** znajdujący się u góry strony. Przeczytaj ostrzeżenie w oknie dialogowym potwierdzenia, a następnie wybierz **OK**.

   Za pomocą przypisanie planu usunięte blokady planu są również usuwane. Ponownie można usunąć zasoby utworzone za pomocą konta z uprawnieniami.

1. Wybierz **grup zasobów** z menu platformy Azure, następnie wybierz pozycję **TestingBPLocks**.

1. Wybierz **kontrola dostępu (IAM)** strony po lewej stronie i następnie **przypisań ról** kartę.

Zabezpieczeń dla grupy zasobów pokazuje, że przypisania planu nie ma już _właściciela_ dostępu.

Gdy **usuwanie przypisania planu powiodło się** pojawi się powiadomienie portalu, przejść do następnego kroku.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu tego samouczka, należy usunąć następujące zasoby:

- Grupa zasobów _TestingBPLocks_
- Definicja planu _storageaccount zablokowane_

## <a name="next-steps"></a>Kolejne kroki

- Uzyskaj informacje na temat [cyklu życia strategii](../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../concepts/parameters.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../concepts/resource-locking.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../concepts/sequencing-order.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../how-to/update-existing-assignments.md).
- Rozwiązywanie problemów podczas przypisywania strategii za pomocą [ogólnych procedur rozwiązywania problemów](../troubleshoot/general.md).