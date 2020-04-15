---
title: 'Szybki start: tworzenie planu w portalu'
description: W tym przewodniku Szybki start można użyć planów platformy Azure do tworzenia, definiowania i wdrażania artefaktów za pośrednictwem witryny Azure portal.
ms.date: 03/25/2020
ms.topic: quickstart
ms.openlocfilehash: dd50b1833f16d364a4494483fcccfee017bb982b
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381889"
---
# <a name="quickstart-define-and-assign-a-blueprint-in-the-portal"></a>Szybki start: definiowanie i przypisywanie planu w portalu

Po dowiedzieć się, jak tworzyć i przypisywać plany, można zdefiniować typowe wzorce do tworzenia konfiguracji wielokrotnego użycia i szybko wdrażalne na podstawie szablonów usługi Azure Resource Manager, zasad, zabezpieczeń i innych. W tym samouczku nauczysz się używać planów platformy Azure do wykonywania niektórych typowych zadań związanych z tworzeniem, publikowaniem i przypisywaniem planu w organizacji. Zadania te obejmują:

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free) przed rozpoczęciem.

## <a name="create-a-blueprint"></a>Tworzenie strategii

Pierwszym krokiem podczas definiowania standardowego wzorca zgodności jest utworzenie strategii z dostępnych zasobów. W tym przykładzie utwórz nowy plan o nazwie **MyBlueprint,** aby skonfigurować przypisania ról i zasad dla subskrypcji. Następnie dodaj nową grupę zasobów i utwórz szablon Menedżera zasobów i przypisanie roli w nowej grupie zasobów.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Wybierz **definicje planu** ze strony po lewej stronie i wybierz przycisk **+ Utwórz plan** u góry strony.

   Możesz też wybrać **pozycję Utwórz** na stronie **Wprowadzenie,** aby przejść bezpośrednio do utworzenia planu.

   :::image type="content" source="./media/create-blueprint-portal/create-blueprint-button.png" alt-text="Tworzenie planu na stronie Definicje planu" border="false":::

1. Wybierz **rozpocznij od pustego planu** z karty u góry wbudowanej listy planów.

1. Podaj **nazwę planu,** taką jak **MyBlueprint**. (Użyj do 48 liter i cyfr, ale bez spacji lub znaków specjalnych). Na razie pozostaw **opis planu** pusty.

1. W polu **Lokalizacja definicja** wybierz wielokropek po prawej stronie, wybierz [grupę zarządzania](../management-groups/overview.md) lub subskrypcję, w której chcesz zapisać plan, a następnie wybierz pozycję **Wybierz**.

1. Sprawdź, czy informacje są poprawne. Nie można później zmienić pól **Nazwa planu** i **Lokalizacja definicji.** Następnie wybierz **przycisk Dalej: Artefakty** u dołu strony lub kartę **Artefakty** u góry strony.

1. Dodawanie przypisania roli na poziomie subskrypcji:

   1. Wybierz wiersz **+ Dodaj artefakt** w obszarze **Subskrypcja**. Po prawej stronie przeglądarki zostanie otwarte okno **Dodaj artefakt.**

   1. Wybierz **przypisanie roli** dla **typu artefaktu**.

   1. W obszarze **Rola**wybierz pozycję **Współautor**. Pozostaw pole **Dodaj użytkownika, aplikację lub grupę** z polem wyboru wskazującym parametr dynamiczny.

   1. Wybierz **dodaj,** aby dodać ten artefakt do planu.

   :::image type="content" source="./media/create-blueprint-portal/add-role-assignment.png" alt-text="Przypisanie roli artefaktu planu" border="false":::

   > [!NOTE]
   > Większość artefaktów obsługuje parametry. Parametrem, który jest przypisany wartość podczas tworzenia planu jest _parametrstatyczny_. Jeśli parametr jest przypisywany podczas przypisywania planu, jest to _parametr dynamiczny_. Aby uzyskać więcej informacji, zobacz [Parametry planu](./concepts/parameters.md).

1. Dodaj przypisanie zasad na poziomie subskrypcji:

   1. Wybierz wiersz **+ Dodaj artefakt** pod artefaktem przypisania roli.

   1. Wybierz **przypisanie zasad** dla **typu artefaktu**.

   1. Zmień **typ** na **wbudowany**. W **polu Wyszukaj**wprowadź **znacznik**.

   1. Kliknij poza polem **Wyszukiwanie**, aby zastosować filtrowanie. Wybierz **pozycję Dołącz tag i jego wartość domyślną do grup zasobów**.

   1. Wybierz **dodaj,** aby dodać ten artefakt do planu.

1. Zaznacz wiersz przypisania zasad **Dołącz tag i jego wartość domyślną do grup zasobów**.

1. Zostanie otwarte okno, w którym można podać parametry do artefaktu jako część definicji strategii i ustawić parametry dla wszystkich przypisań (parametry statyczne) na podstawie tej strategii zamiast ustawiania ich podczas przypisywania (parametry dynamiczne). W tym przykładzie użyto parametrów dynamicznych podczas przypisywania planu, więc pozostaw wartości domyślne i wybierz pozycję **Anuluj**.

1. Dodawanie grupy zasobów na poziomie subskrypcji:

   1. Wybierz wiersz **+ Dodaj artefakt** w obszarze **Subskrypcja**.

   1. Wybierz **grupę zasobów** dla **typu artefaktu**.

   1. Pozostaw **pola Nazwa wyświetlana artefaktu**, **Nazwa grupy zasobów**i **Lokalizacja** puste, ale upewnij się, że pole wyboru jest zaznaczone dla każdej właściwości parametru, aby uczynić je parametrami dynamicznymi.

   1. Wybierz **dodaj,** aby dodać ten artefakt do planu.

1. Dodawanie szablonu w grupie zasobów:

   1. Wybierz wiersz **+ Dodaj artefakt** pod wpisem **ResourceGroup.**

   1. Wybierz **szablon Usługi Azure Resource Manager** dla typu **artefaktu,** ustaw **nazwę wyświetlaną artefaktu** na **Konto magazynu**i pozostaw **opis** jako pusty.

   1. Na karcie **Szablon** w polu edytora wklej poniższy szablon usługi Resource Manager.
      Po wklejeniu szablonu wybierz kartę **Parametry** i zwróć uwagę, że wykryto parametry szablonu **storageAccountType** i **lokalizacja.** Każdy parametr został automatycznie wykryty i wypełniony, ale skonfigurowany jako parametr dynamiczny.

      > [!IMPORTANT]
      > Jeśli importujesz szablon, upewnij się, że plik jest tylko JSON i nie zawiera kodu HTML. Gdy wskazujesz adres URL w usłudze GitHub, upewnij się, że wybrano **raw,** aby uzyskać czysty plik JSON, a nie ten zawinięty w kod HTML do wyświetlenia w usłudze GitHub. Jeśli zaimportowany szablon nie jest czystym plikiem JSON, wystąpi błąd.

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
              },
              "location": {
                  "type": "string",
                  "defaultValue": "[resourceGroup().location]",
                  "metadata": {
                      "description": "Location for all resources."
                  }
              }
          },
          "variables": {
              "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
          },
          "resources": [{
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[variables('storageAccountName')]",
              "location": "[parameters('location')]",
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

   1. Wyczyść pole wyboru **storageAccountType** i zwróć uwagę, że lista rozwijana zawiera tylko wartości zawarte w szablonie Menedżera zasobów w obszarze **Dozwolone Wartości**. Zaznacz pole, aby przywrócić parametr dynamiczny.

   1. Wybierz **dodaj,** aby dodać ten artefakt do planu.

   :::image type="content" source="./media/create-blueprint-portal/add-resource-manager-template.png" alt-text="Szablon Menedżera zasobów dla artefaktu planu" border="false":::

1. Ukończona strategia powinna wyglądać podobnie do poniższej. Należy zauważyć, że każdy artefakt ma ** _x_ z _y_ parametry wypełnione** w **parametry** kolumny. Parametry dynamiczne są ustawiane każdorazowo podczas przypisywania strategii.

   :::image type="content" source="./media/create-blueprint-portal/completed-blueprint.png" alt-text="Ukończona definicja planu" border="false":::

1. Teraz, gdy wszystkie planowane artefakty zostały dodane, wybierz **zapisz pochówek** u dołu strony.

## <a name="edit-a-blueprint"></a>Edytowanie strategii

W [polu Utwórz plan](#create-a-blueprint)nie podasz opisu ani nie dodano przypisania roli do nowej grupy zasobów. Możesz rozwiązać oba te czynności, wykonując następujące czynności:

1. Wybierz **definicje planu** ze strony po lewej stronie.

1. Na liście planów kliknij prawym przyciskiem myszy utworzony wcześniej plan i wybierz polecenie **Edytuj plan**.

1. W polu **Opis strategii** podaj informacje o strategii i artefaktach, które ją tworzą. W takim przypadku wprowadź coś takiego: **Ten plan ustawia zasady tagów i przypisanie ról w subskrypcji, tworzy grupę zasobów i wdraża szablon zasobów i przypisanie roli do tej grupy zasobów.**

1. Wybierz **dalej: Artefakty** u dołu strony lub na karcie **Artefakty** u góry strony.

1. Dodawanie przypisania roli w grupie zasobów:

   1. Zaznacz wiersz **+ Dodaj artefakt** bezpośrednio pod wpisem **ResourceGroup.**

   1. Wybierz **przypisanie roli** dla **typu artefaktu**.

   1. W obszarze **Rola**wybierz pozycję **Właściciel**i wyczyść pole wyboru w polu **Dodaj użytkownika, aplikację lub grupę.**

   1. Wyszukaj i wybierz użytkownika, aplikację lub grupę do dodania. Ten artefakt używa tego samego parametru statycznego ustawionego w każdym przypisaniu tej strategii.

   1. Wybierz **dodaj,** aby dodać ten artefakt do planu.

   :::image type="content" source="./media/create-blueprint-portal/add-role-assignment-2.png" alt-text="Przypisanie drugiej roli artefaktu planu" border="false":::

1. Ukończona strategia powinna wyglądać podobnie do poniższej. Należy zauważyć, że nowo dodane przypisanie roli pokazuje **1 z 1 parametrów wypełnionych**. Oznacza to, że jest to parametr statyczny.

   :::image type="content" source="./media/create-blueprint-portal/completed-blueprint-2.png" alt-text="Druga definicja ukończonego planu" border="false":::

1. Wybierz **pozycję Zapisz pochówek,** teraz, gdy została zaktualizowana.

## <a name="publish-a-blueprint"></a>Publikowanie strategii

Po dodaniu wszystkich zaplanowanych artefaktów do strategii czas na jej opublikowanie.
Publikowanie udostępnia plan do przypisania do subskrypcji.

1. Wybierz **definicje planu** ze strony po lewej stronie.

1. Na liście planów kliknij prawym przyciskiem myszy utworzony wcześniej plan i wybierz polecenie **Publikuj plan**.

1. W okienku, które zostanie otwarte, podaj **wersję** (litery, cyfry i łączniki o maksymalnej długości 20 znaków), na przykład **v1**. Opcjonalnie wprowadź tekst w **polach Zmień notatki,** na przykład **Pierwsze publikowanie**.

1. Wybierz **pozycję Publikuj** u dołu strony.

## <a name="assign-a-blueprint"></a>Przypisywanie strategii

Po opublikowaniu planu można go przypisać do subskrypcji. Przypisz plan utworzony do jednej z subskrypcji w ramach hierarchii grupy zarządzania. Jeśli strategia została zapisana w subskrypcji, można ją przypisać tylko do tej subskrypcji.

1. Wybierz **definicje planu** ze strony po lewej stronie.

1. Na liście planów kliknij prawym przyciskiem myszy utworzony wcześniej (lub wybierz wielokropek) i wybierz pozycję **Przypisz plan**.

1. Na stronie **Przypisywanie planu** na liście rozwijanej **Subskrypcja** wybierz subskrypcje, na których chcesz wdrożyć ten plan.

   Jeśli w portalu [Azure Billing](../../billing/index.md) są dostępne obsługiwane oferty dotyczące wersji Enterprise, zostanie uaktywniony link **Utwórz nową** w obszarze **Subskrypcja**. Wykonaj następujące kroki:

   1. Wybierz link **Utwórz nową**, aby utworzyć nową subskrypcję, zamiast wybierać już istniejące.

   1. W polu **Nazwa wyświetlana** podaj nazwę dla nowej subskrypcji.

   1. Wybierz dostępną **ofertę** z listy rozwijanej.

   1. Użyj wielokropek, aby wybrać [grupę zarządzania,](../management-groups/overview.md) której subskrypcja będzie podrzędna.

   1. Wybierz pozycję **Utwórz** w dolnej części strony.

      :::image type="content" source="./media/create-blueprint-portal/assignment-create-subscription.png" alt-text="Tworzenie subskrypcji subskrypcji przydziału planu" border="false":::

      > [!IMPORTANT]
      > Nowa subskrypcja jest tworzona natychmiast po wybraniu opcji **Utwórz**.

   > [!NOTE]
   > Przypisanie jest tworzone dla każdej wybranej subskrypcji. Możesz wprowadzać zmiany w jednym przypisaniu subskrypcji w późniejszym czasie bez wymuszania zmian w pozostałej części wybranych subskrypcji.

1. W przypadku **nazwy przypisania**podaj unikatową nazwę dla tego przypisania.

1. W **obszarze Lokalizacja**wybierz region dla obiektu wdrożenia tożsamości zarządzanej i subskrypcji, w którym ma zostać utworzony obiekt. Usługa Azure Blueprint używa tej tożsamości zarządzanej do wdrażania wszystkich artefaktów w przypisanej strategii. Aby dowiedzieć się więcej, zobacz [Tożsamości zarządzane dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md).

1. Pozostaw wybór **rozwijany wersji definicji planu** **opublikowanych** wersji we wpisie **w wersji 1.** (Domyślna jest ostatnio opublikowana wersja).

1. Dla opcji **Blokowanie przypisania** pozostaw wartość domyślną **Nie blokuj**. Aby uzyskać więcej informacji, zobacz [Blokowanie zasobów planów](./concepts/resource-locking.md).

   :::image type="content" source="./media/create-blueprint-portal/assignment-locking-mi.png" alt-text="Blokowanie i zarządzanie tożsamościami dla przypisania" border="false":::

1. W obszarze **Tożsamość zarządzana**pozostaw domyślną **przypisaną opcję System**.

1. Dla przypisania roli na poziomie subskrypcji **[Grupa użytkowników lub nazwa aplikacji]: Współautor** wyszukaj i wybierz użytkownika, aplikację lub grupę.

1. W przypadku przypisania zasad na poziomie subskrypcji ustaw **nazwę znacznika** na **CostCenter** i **wartość znacznika** na **ContosoIT**.

1. W przypadku **resourcegroup**podaj **nazwę** **konta magazynu** i **lokalizację** **wschodnich stanów USA 2** z listy rozwijanej.

   > [!NOTE]
   > Dla każdego artefaktu, który został dodany w ramach grupy zasobów podczas definicji planu, ten artefakt jest wcięty, aby wyrównać z grupą zasobów lub obiektem, z którym będzie można go wdrożyć.
   > Artefakty, które nie przyjmują parametrów lub nie mają parametrów do zdefiniowania przy przypisywaniu, są wyświetlane tylko dla informacji kontekstowych.

1. W szablonie Usługi Azure Resource Manager **StorageAccount**wybierz **Standard_GRS** dla **parametru storageAccountType.**

1. Przeczytaj pole informacji u dołu strony, a następnie wybierz pozycję **Przypisz**.

## <a name="track-deployment-of-a-blueprint"></a>Śledzenie wdrażania strategii

Po przypisaniu strategii do co najmniej jednej subskrypcji dzieją się dwie rzeczy:

- Plan jest dodawany do **przypisane plany** strony dla każdej subskrypcji.
- Rozpoczyna się proces wdrażania wszystkich artefaktów zdefiniowanych przez plan.

Teraz, gdy plan został przypisany do subskrypcji, sprawdź postęp wdrożenia:

1. Wybierz **przypisane plany** ze strony po lewej stronie.

1. Na liście planów kliknij prawym przyciskiem myszy wcześniej przypisany plan, a następnie wybierz polecenie **Wyświetl szczegóły przydziału**.

   :::image type="content" source="./media/create-blueprint-portal/view-assignment-details.png" alt-text="Wyświetlanie szczegółów przydziału na stronie Przypisane plany" border="false":::

1. Na stronie **przydział planu** sprawdź, czy wszystkie artefakty zostały pomyślnie wdrożone i że podczas wdrażania nie wystąpiły żadne błędy. Jeśli wystąpiły błędy, zobacz [Plany rozwiązywania problemów,](./troubleshoot/general.md) aby uzyskać instrukcje, aby ustalić, co poszło nie tak.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

### <a name="unassign-a-blueprint"></a>Cofanie przypisania strategii

Jeśli nie potrzebujesz już przypisania planu, usuń je z subskrypcji. Plan mógł zostać zastąpiony nowszym planem ze zaktualizowanymi wzorcami, zasadami i projektami. Po usunięciu strategii artefakty przypisane w jej ramach są pozostawiane. Aby usunąć przypisanie strategii, wykonaj następujące kroki:

1. Wybierz **przypisane plany** ze strony po lewej stronie.

1. Na liście planów wybierz plan, który chcesz śmigij. Następnie wybierz przycisk **Cozbij plan** u góry strony.

1. Przeczytaj wiadomość z potwierdzeniem, a następnie wybierz **przycisk OK**.

### <a name="delete-a-blueprint"></a>Usuwanie strategii

1. Wybierz **definicje planu** ze strony po lewej stronie.

1. Kliknij prawym przyciskiem myszy plan, który chcesz usunąć, a następnie wybierz polecenie **Usuń plan**. Następnie wybierz **pozycję Tak** w oknie dialogowym potwierdzenia.

> [!NOTE]
> Usunięcie planu w tej metodzie powoduje również usunięcie wszystkich opublikowanych wersji wybranego planu.
> Aby usunąć pojedynczą wersję, otwórz plan, wybierz kartę **Opublikowane wersje,** wybierz wersję, którą chcesz usunąć, a następnie wybierz pozycję **Usuń tę wersję**. Ponadto nie można usunąć planu, dopóki nie usuniesz wszystkich przypisań planu tej definicji planu.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono, przypisano i usunięto plan za pomocą witryny Azure Portal. Aby dowiedzieć się więcej o planach platformy Azure, przejdź do artykułu cyklu życia planu.

> [!div class="nextstepaction"]
> [Dowiedz się więcej o cyklu życia planu](./concepts/lifecycle.md)