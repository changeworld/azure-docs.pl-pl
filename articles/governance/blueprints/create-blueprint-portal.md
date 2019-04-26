---
title: Tworzenie strategii w portalu
description: Plany usługi Azure umożliwia tworzenie, zdefiniuj i wdrażaj artefakty za pośrednictwem witryny Azure portal.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/11/2019
ms.topic: quickstart
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 28fef394ee400949f9911983bdbca41d6bfcb458
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60683527"
---
# <a name="define-and-assign-a-blueprint-in-the-portal"></a>Zdefiniuj i przypisz plan w witrynie portal

Gdy dowiesz się, jak utworzyć i przypisać schematy, można zdefiniować typowe wzorce do tworzenia wielokrotnego użytku i szybko wdrożyć konfiguracje oparte na usłudze Azure Resource Manager szablony, zasady, zabezpieczeń i inne. W tym samouczku dowiesz się, czy niektóre typowe zadania związane z tworzenie, publikowanie i przypisywanie planu w Twojej organizacji za pomocą planów usługi Azure. Te zadania obejmują:

> [!div class="checklist"]
> - Tworzenie nowej strategii i dodawanie różnych obsługiwanych artefaktów
> - Wprowadzanie zmian do istniejącej strategii, która wciąż znajduje się w stanie **Wersja robocza**
> - Oznaczanie strategii jako gotowej do przypisania za pomocą stanu **Opublikowano**
> - Przypisywanie strategii do istniejącej subskrypcji
> - Sprawdzanie stanu i postępu przypisanej strategii
> - Usuwanie strategii, która została przypisana do subskrypcji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

## <a name="create-a-blueprint"></a>Tworzenie strategii

Pierwszym krokiem podczas definiowania standardowego wzorca zgodności jest utworzenie strategii z dostępnych zasobów. W tym przykładzie należy utworzyć nowy plan o nazwie **MyBlueprint** do konfigurowania ról, przypisywanie zasad dla subskrypcji. Następnie dodaj nową grupę zasobów i utworzyć przypisanie szablonu i roli Menedżera zasobów dla nowej grupy zasobów.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Wybierz **planu definicje** ze strony na pasku po lewej stronie i wybierz **+ Utwórz plan** znajdujący się u góry strony.

   Lub wybierz **Utwórz** z **wprowadzenie** strony, aby przejść bezpośrednio do tworzenia planu.

   ![Tworzenie planu ze strony definicji planu](./media/create-blueprint-portal/create-blueprint-button.png)

1. Podaj **nazwy planu** takich jak **MyBlueprint**. (Użyj maksymalnie 48 litery i cyfry, ale bez spacji i znaków specjalnych). Pozostaw **planu opis** puste.

1. W **Lokalizacja definicji** polu, wybierz przycisk wielokropka po prawej stronie, wybierz [grupy zarządzania](../management-groups/overview.md) lub subskrypcji, w którym chcesz zapisać planu, a następnie wybierz **wybierz**.

1. Sprawdź, czy informacje są poprawne. **Nazwy planu** i **Lokalizacja definicji** pola nie można zmienić później. Następnie wybierz pozycję **dalej: Artefakty** w dolnej części strony lub kartę **Artefakty** w górnej części strony.

1. Dodawanie przypisania roli na poziomie subskrypcji:

   1. Wybierz **+ Dodawanie artefaktu** wiersz w obszarze **subskrypcji**. **Dodawanie artefaktu** po prawej stronie w przeglądarce zostanie otwarte okno.

   1. Wybierz **przypisania roli** dla **typ artefaktu**.

   1. W obszarze **roli**, wybierz opcję **Współautor**. Pozostaw **Dodaj użytkownika, aplikacji lub grupy** pola, przy czym pole wyboru, który wskazuje parametr dynamiczny.

   1. Wybierz **Dodaj** można dodać tego artefaktu do planu.

   ![Przypisanie roli dla artefaktu planu](./media/create-blueprint-portal/add-role-assignment.png)

   > [!NOTE]
   > Większość artefaktów obsługuje parametrów. Parametr, który ma przypisaną wartość, podczas tworzenia planu jest *parametru statycznego*. Jeśli parametr nie zostanie przypisana podczas przypisywania planu jest *parametru dynamicznego*. Aby uzyskać więcej informacji, zobacz [Parametry strategii](./concepts/parameters.md).

1. Dodaj przypisanie zasad na poziomie subskrypcji:

   1. Wybierz **+ Dodawanie artefaktu** wiersz pod artefaktu przypisania roli.

   1. Wybierz **przypisania zasad** dla **typ artefaktu**.

   1. Zmiana **typu** do **wbudowanych**. W **wyszukiwania**, wprowadź **tag**.

   1. Kliknij poza polem **Wyszukiwanie**, aby zastosować filtrowanie. Wybierz **Zastosuj tag i jego wartość domyślną do grup zasobów**.

   1. Wybierz **Dodaj** można dodać tego artefaktu do planu.

1. Zaznacz wiersz przypisania zasad **Zastosuj tag i jego wartość domyślną do grup zasobów**.

1. Okna, aby podać parametry do artefaktu w ramach definicji planu otwiera i umożliwia ustawienie parametrów dla wszystkich przypisań (parametry statyczne) oparte na ten plan zamiast podczas przypisywania (parametry dynamiczne). W tym przykładzie użyto parametrów dynamicznych podczas przypisywania planu, więc pozostaw wartości domyślne i wybierz pozycję **anulować**.

1. Dodaj grupę zasobów na poziomie subskrypcji:

   1. Wybierz **+ Dodawanie artefaktu** wiersz w obszarze **subskrypcji**.

   1. Wybierz **grupy zasobów** dla **typ artefaktu**.

   1. Pozostaw **nazwę wyświetlaną artefaktu**, **nazwy grupy zasobów**, i **lokalizacji** pola puste, ale upewnij się, że zaznaczono pole wyboru dla każdej właściwości parametru były one parametry dynamiczne.

   1. Wybierz **Dodaj** można dodać tego artefaktu do planu.

1. Dodawanie szablonu w ramach grupy zasobów:

   1. Wybierz **+ Dodawanie artefaktu** wiersz w obszarze **ResourceGroup** wpisu.

   1. Wybierz **szablonu usługi Azure Resource Manager** dla **typ artefaktu**ustaw **nazwę wyświetlaną artefaktu** do **StorageAccount**i pozostawić  **Opis** puste.

   1. Na karcie **Szablon** w polu edytora wklej poniższy szablon usługi Resource Manager.
      Po wklejeniu tego szablonu, wybierz **parametry** kartę i zwróć uwagę, że parametry szablonu **storageAccountType** i **lokalizacji** zostały wykryte. Każdy parametr został automatycznie wykryte i wypełnione, ale skonfigurowany jako parametr dynamiczny.

      > [!IMPORTANT]
      > Jeśli importujesz szablonu, upewnij się, że plik jest tylko JSON i nie zawiera kodu HTML. Gdy wskazuje adres URL w witrynie GitHub, upewnij się, że wybrano **RAW** można pobrać czystej plik JSON, a nie jednego, ujęte w nawiasy HTML do wyświetlenia w witrynie GitHub. Jeśli zaimportowany szablon nie jest czystym plikiem JSON, wystąpi błąd.

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

   1. Wyczyść **storageAccountType** pole wyboru i zwróć uwagę, że listy rozwijanej zawiera tylko wartości zawarte w szablonie usługi Resource Manager w obszarze **allowedValues**. Zaznacz pole, aby ustawić jej parametrów dynamicznych.

   1. Wybierz **Dodaj** można dodać tego artefaktu do planu.

   ![Szablon usługi Resource Manager dla artefaktu planu](./media/create-blueprint-portal/add-resource-manager-template.png)

1. Ukończona strategia powinna wyglądać podobnie do poniższej. Należy zauważyć, że każdego artefaktu ma  **_x_ poza _y_ parametry wypełnione** w **parametry** kolumny. Parametry dynamiczne są ustawiane podczas każdego przydziału planu.

   ![Definicji planu ukończone](./media/create-blueprint-portal/completed-blueprint.png)

1. Teraz, że dodano wszystkie artefakty planowane, zaznacz **Zapisz wersję roboczą** w dolnej części strony.

## <a name="edit-a-blueprint"></a>Edytowanie strategii

W [Tworzenie planu](#create-a-blueprint), nie Podaj opis lub dodawanie przypisania roli do nowej grupy zasobów. Problem można rozwiązać zarówno, wykonując następujące czynności:

1. Wybierz pozycję **Definicje strategii** w lewej części strony.

1. Na liście Schematy, kliknij prawym przyciskiem myszy jeden, która została wcześniej utworzona, a następnie wybierz **edycji planu**.

1. W polu **Opis strategii** podaj informacje o strategii i artefaktach, które ją tworzą. W tym przypadku wprowadź opis podobny do następującego: **Ten plan ustawia tag zasad i przypisanie roli w ramach subskrypcji, tworzy grupy zasobów, a następnie wdraża zasobów szablonu i przypisanie roli do tej grupy zasobów.**

1. Wybierz opcję **Dalej: Artefakty** w dolnej części strony lub kartę **Artefakty** w górnej części strony.

1. Dodaj przypisanie roli w ramach grupy zasobów:

   1. Wybierz **+ Dodawanie artefaktu** wiersz bezpośrednio pod **ResourceGroup** wpisu.

   1. Wybierz **przypisania roli** dla **typ artefaktu**.

   1. W obszarze **roli**, wybierz opcję **właściciela**i usuń zaznaczenie pola wyboru w obszarze **Dodaj użytkownika, aplikacji lub grupy** pole.

   1. Wyszukaj i wybierz użytkownika, aplikacji lub grupy do dodania. Ten artefakt używa zestawu parametrów statyczne takie same w każdym przypisania tego planu.

   e. Wybierz **Dodaj** można dodać tego artefaktu do planu.

   ![Drugie przypisanie roli dla artefaktu planu](./media/create-blueprint-portal/add-role-assignment-2.png)

1. Ukończona strategia powinna wyglądać podobnie do poniższej. Należy zauważyć, że przypisanie roli w nowo dodanym pokazuje **parametry 1 z 1 wypełnione**. Oznacza to, że jest to parametr statyczne.

   ![Druga definicja planu ukończone](./media/create-blueprint-portal/completed-blueprint-2.png)

1. Wybierz **Zapisz wersję roboczą** teraz, gdy został zaktualizowany.

## <a name="publish-a-blueprint"></a>Publikowanie strategii

Po dodaniu wszystkich zaplanowanych artefaktów do strategii czas na jej opublikowanie.
Publikowanie sprawia, że plan dostępne do przypisania do subskrypcji.

1. Wybierz pozycję **Definicje strategii** w lewej części strony.

1. Na liście Schematy, kliknij prawym przyciskiem myszy ten, który został wcześniej utworzony, a następnie wybierz pozycję **publikowania planu**.

1. W otwartym okienku **wersji** (litery, cyfry i łączniki o maksymalnej długości 20 znaków), takie jak **v1**. Opcjonalnie wprowadź tekst w **zmienić uwagi**, takich jak **najpierw opublikować**.

1. Wybierz **Publikuj** w dolnej części strony.

## <a name="assign-a-blueprint"></a>Przypisywanie strategii

Po opublikowaniu planu można ją przypisać do subskrypcji. Przypisz plan, który został utworzony do jednej subskrypcji w obszarze hierarchia grup zarządzania. Jeśli strategia została zapisana w subskrypcji, można ją przypisać tylko do tej subskrypcji.

1. Wybierz pozycję **Definicje strategii** w lewej części strony.

1. Na liście Schematy, kliknij prawym przyciskiem myszy jeden, który został wcześniej utworzony (lub wybierz przycisk wielokropka) i wybierz **planu Przypisz**.

1. Na **planu Przypisz** stronie **subskrypcji** listy rozwijanej, wybierz subskrypcje, które chcesz wdrożyć ten plan do.

   Jeśli w portalu [Azure Billing](../../billing/index.md) są dostępne obsługiwane oferty dotyczące wersji Enterprise, zostanie uaktywniony link **Utwórz nową** w obszarze **Subskrypcja**. Wykonaj następujące kroki:

   1. Wybierz link **Utwórz nową**, aby utworzyć nową subskrypcję, zamiast wybierać już istniejące.

   1. W polu **Nazwa wyświetlana** podaj nazwę dla nowej subskrypcji.

   1. Wybierz dostępnych **oferują** z listy rozwijanej.

   1. Użyj wielokropka, aby wybrać [grupy zarządzania](../management-groups/index.md) , subskrypcja będzie elementem podrzędnym.

   1. Wybierz pozycję **Utwórz** w dolnej części strony.

   ![Utwórz subskrypcję dla subskrypcji przypisanie planu](./media/create-blueprint-portal/assignment-create-subscription.png)

   > [!IMPORTANT]
   > Nowa subskrypcja jest tworzony natychmiast, po wybraniu **Utwórz**.

   > [!NOTE]
   > Przypisanie jest tworzony dla każdej subskrypcji, którą można wybrać. Możesz zmiany przypisania pojedynczej subskrypcji, w późniejszym czasie bez konieczności wprowadzania zmian w pozostałej części wybranych subskrypcji.

1. Aby uzyskać **nazwa przypisania**, Podaj unikatową nazwę dla tego przypisania.

1. W **lokalizacji**, wybierz region dla zarządzanego obiektu wdrożenia tożsamości i subskrypcji do utworzenia w. Usługa Azure Blueprint używa tej tożsamości zarządzanej do wdrażania wszystkich artefaktów w przypisanej strategii. Aby dowiedzieć się więcej, zobacz [Tożsamości zarządzane dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md).

1. Pozostaw **wersji definicji planu** listy rozwijanej wyboru **opublikowano** wersji na **v1** wpisu. (Wartość domyślna to najnowszej opublikowanej wersji).

1. Dla opcji **Blokowanie przypisania** pozostaw wartość domyślną **Nie blokuj**. Aby uzyskać więcej informacji, zobacz [blokowania zasobów plany](./concepts/resource-locking.md).

   ![Blokowanie i zarządzanych tożsamości dla przypisania](./media/create-blueprint-portal/assignment-locking-mi.png)

1. W obszarze **tożsamości zarządzanej**, pozostaw wartość domyślną **przypisanej w systemie**.

1. Dla przypisania roli na poziomie subskrypcji **[Grupa użytkowników lub nazwa aplikacji]: Współautor** wyszukaj i wybierz użytkownika, aplikację lub grupę.

1. W przypadku przypisania zasady na poziomie subskrypcji, ustawić **nazwa tagu** do **CostCenter** i **wartość tagu** do **ContosoIT**.

1. Dla **ResourceGroup**, podaj **nazwa** z **StorageAccount** i **lokalizacji** z **wschodnie stany USA 2** z listy rozwijanej.

   > [!NOTE]
   > Dla każdego artefaktu dodanego w ramach grupy zasobów w definicji planu tego artefaktu tworzone jest wcięcie aby było zgodne z grupą zasobów lub obiekt, który ją wdrożysz.
   > Artefakty, które nie przyjmują parametrów albo nie może mieć parametrów określonych w przypisania są wyświetlane tylko w przypadku informacji kontekstowych.

1. W szablonie usługi Azure Resource Manager **StorageAccount**, wybierz opcję **Standard_GRS** dla **storageAccountType** parametru.

1. Przeczytaj pole informacyjne w dolnej części strony, a następnie wybierz **przypisać**.

## <a name="track-deployment-of-a-blueprint"></a>Śledzenie wdrażania strategii

Po przypisaniu strategii do co najmniej jednej subskrypcji dzieją się dwie rzeczy:

- Planu jest dodawany do **przypisane plany** strony dla każdej subskrypcji.
- Rozpoczyna się proces wdrażania wszystkich artefaktów, które są definiowane przez planu.

Teraz, gdy planu zostało przypisane do subskrypcji, sprawdź postęp wdrażania:

1. Wybierz pozycję **Przypisane strategie** w lewej części strony.

1. Na liście Schematy, kliknij prawym przyciskiem myszy, które zostały wcześniej przypisane, a następnie wybierz **Wyświetl szczegóły przydziału**.

   ![Wyświetl szczegóły przydziału ze strony przypisane plany](./media/create-blueprint-portal/view-assignment-details.png)

1. Na **planu przypisania** strony, weryfikowanie, czy wszystkich artefaktów zostały pomyślnie wdrożone i że nie wystąpiły błędy podczas wdrażania. Jeśli wystąpiły błędy, zobacz [Rozwiązywanie problemów z plany](./troubleshoot/general.md) kroki określić, co poszło źle.

## <a name="unassign-a-blueprint"></a>Cofanie przypisania strategii

Jeśli przypisanie planu nie są już potrzebne, usuń go z subskrypcji. Planu może zostały zastąpione przez nowszą planu z zaktualizowane wzorce, zasad i projekty. Po usunięciu strategii artefakty przypisane w jej ramach są pozostawiane. Aby usunąć przypisanie strategii, wykonaj następujące kroki:

1. Wybierz pozycję **Przypisane strategie** w lewej części strony.

1. Na liście plany wybierz planu, który chcesz cofnąć to przypisanie. Następnie wybierz pozycję **Cofnij przypisanie planu** znajdujący się u góry strony.

1. Przeczytaj komunikat potwierdzający, a następnie wybierz pozycję **OK**.

## <a name="delete-a-blueprint"></a>Usuwanie strategii

1. Wybierz pozycję **Definicje strategii** w lewej części strony.

1. Kliknij prawym przyciskiem myszy planu, który chcesz usunąć, a następnie wybierz pozycję **usuwania planu**. Następnie wybierz pozycję **tak** w oknie dialogowym potwierdzenia.

> [!NOTE]
> Trwa usuwanie planu w przypadku tej metody spowoduje również usunięcie wszystkich opublikowane wersje wybranego planu.
> Aby usunąć jedną wersję, otwórz planu, wybierz **opublikowane wersje** , a następnie wybierz wersję, którą chcesz usunąć, a następnie wybierz **Usuń tę wersję**. Ponadto nie można usunąć planu, dopóki nie zostały usunięte wszystkie przypisania planu tej definicji planu.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [planu cyklu życia](./concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](./concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](./concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](./concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](./how-to/update-existing-assignments.md).
- Rozwiązywanie problemów podczas przypisywania strategii za pomocą [ogólnych procedur rozwiązywania problemów](./troubleshoot/general.md).