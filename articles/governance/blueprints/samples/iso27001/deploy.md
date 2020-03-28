---
title: Wdrażanie przykładu planu ISO 27001
description: Wdrażanie kroków dla autonomicznego przykładu planu ISO 27001, w tym szczegółów parametru artefaktu planu.
ms.date: 01/13/2020
ms.topic: sample
ms.openlocfilehash: f5920541d49c454bd6648318c04ed607eada1dfb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75922504"
---
# <a name="deploy-the-iso-27001-blueprint-sample"></a>Wdrażanie przykładu planu ISO 27001

Aby wdrożyć przykład planu Azure Blueprints 27001, należy wykonać następujące kroki:

> [!div class="checklist"]
> - Tworzenie nowego planu z przykładu
> - Oznaczanie kopii próbki jako **opublikowanej**
> - Przypisywanie kopii planu do istniejącej subskrypcji

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free) przed rozpoczęciem.

## <a name="create-blueprint-from-sample"></a>Tworzenie planu z przykładu

Najpierw zaimplementuj przykład planu, tworząc nowy plan w środowisku przy użyciu próbki jako starter.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Na stronie **Wprowadzenie** po lewej stronie wybierz przycisk **Utwórz** w obszarze _Tworzenie planu_.

1. Znajdź przykład planu **ISO 27001** w obszarze _Inne próbki_ i wybierz **użyj tego przykładu**.

1. Wprowadź _podstawy_ przykładu planu:

   - **Nazwa planu:** Podaj nazwę kopii przykładu planu ISO 27001.
   - **Lokalizacja definicji:** Użyj wielokropek i wybierz grupę zarządzania, aby zapisać kopię próbki.

1. Wybierz kartę _Artefakty_ u góry strony lub **Dalej: Artefakty** u dołu strony.

1. Przejrzyj listę artefaktów, które tworzą przykład planu. Wiele artefaktów ma parametry, które zdefiniujemy później. Po zakończeniu przeglądania przykładu planu wybierz pozycję Zapisz wersja **robocza.**

## <a name="publish-the-sample-copy"></a>Publikowanie przykładowej kopii

Kopia przykładu planu została utworzona w twoim środowisku. Jest tworzony w trybie **roboczym** i musi zostać **opublikowany,** zanim będzie można go przypisać i wdrożyć. Kopię przykładu planu można dostosować do środowiska i potrzeb, ale ta modyfikacja może odsunąć ją od normy ISO 27001.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Po lewej stronie wybierz stronę **Definicje planu.** Użyj filtrów, aby znaleźć kopię przykładu planu, a następnie wybierz go.

1. Wybierz **pozycję Publikuj plan** u góry strony. Na nowej stronie po prawej stronie podaj **wersję** dla kopii przykładu planu. Ta właściwość jest przydatna, jeśli dokonasz modyfikacji później. Podaj **uwagi dotyczące zmian,** takie jak "Pierwsza wersja opublikowana z przykładu planu ISO 27001". Następnie wybierz **pozycję Publikuj** u dołu strony.

## <a name="assign-the-sample-copy"></a>Przypisywanie przykładowej kopii

Po **pomyślnym**opublikowaniu kopii przykładu planu można ją przypisać do subskrypcji w grupie zarządzania, do której została zapisana. W tym kroku są dostarczane parametry, aby każde wdrożenie kopii przykładu planu unikatowe.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Po lewej stronie wybierz stronę **Definicje planu.** Użyj filtrów, aby znaleźć kopię przykładu planu, a następnie wybierz go.

1. Wybierz **pozycję Przypisz plan** u góry strony definicji planu.

1. Podaj wartości parametrów dla przypisania planu:

   - Podstawy

     - **Subskrypcje:** Wybierz jedną lub więcej subskrypcji, które znajdują się w grupie zarządzania, do której została zapisana kopia przykładu planu. Jeśli wybierzesz więcej niż jedną subskrypcję, dla każdej z nich zostanie utworzone przypisanie przy użyciu wprowadzonych parametrów.
     - **Nazwa przypisania:** Nazwa jest wstępnie wypełniona na podstawie nazwy planu.
       Zmień w razie potrzeby lub zostaw, jak jest.
     - **Lokalizacja:** Wybierz region dla zarządzanej tożsamości, w którym ma zostać utworzona. Usługa Azure Blueprint używa tej tożsamości zarządzanej do wdrażania wszystkich artefaktów w przypisanej strategii. Aby dowiedzieć się więcej, zobacz [tożsamości zarządzane dla zasobów platformy Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Wersja definicji planu:** Wybierz **opublikowaną** wersję kopii przykładu planu.

   - Przypisanie blokady

     Wybierz ustawienie blokady planu dla środowiska. Aby uzyskać więcej informacji, zobacz [blokowanie zasobów strategii](../../concepts/resource-locking.md).

   - Tożsamość zarządzana

     Pozostaw domyślną opcję tożsamości _zarządzanej przypisanej systemowi._

   - Parametry strategii

     Parametry zdefiniowane w tej sekcji są używane przez wiele artefaktów w definicji planu, aby zapewnić spójność.

     - **Dozwolona lokalizacja dla zasobów i grup zasobów:** Wartość wskazująca dozwolone lokalizacje dla grup zasobów i zasobów.

   - Parametry artefaktu

     Parametry zdefiniowane w tej sekcji dotyczą artefaktu, w którym jest zdefiniowany. Parametry te są [parametry dynamiczne,](../../concepts/parameters.md#dynamic-parameters) ponieważ są one zdefiniowane podczas przypisywania planu. Aby uzyskać pełną listę lub parametry artefaktu i ich opisy, zobacz [Tabela parametrów artefaktów](#artifact-parameters-table).

1. Po wprowadzeniu wszystkich parametrów wybierz **pozycję Przypisz** u dołu strony. Zostanie utworzone przypisanie planu i rozpocznie się wdrażanie artefaktów. Wdrożenie trwa około godziny. Aby sprawdzić stan wdrożenia, otwórz przypisanie planu.

> [!WARNING]
> Usługa Azure Blueprints i wbudowane przykłady planu są **bezpłatne.** Zasoby platformy Azure są [wyceniane według produktu](https://azure.microsoft.com/pricing/). Kalkulator [cen](https://azure.microsoft.com/pricing/calculator/) służy do szacowania kosztów uruchomionych zasobów wdrożonych w tym przykładzie planu.

## <a name="artifact-parameters-table"></a>Tabela parametrów artefaktów

Poniższa tabela zawiera listę parametrów artefaktu planu:

|Nazwa artefaktu|Typ artefaktu|Nazwa parametru|Opis|
|-|-|-|-|
|\[Wersja\]zapoznawcza : Wdrażanie agenta analizy dzienników dla zestawów skalowania maszyn wirtualnych systemu Linux (VMSS)|Przypisywanie zasad|Obszar roboczy analizy dzienników dla zestawów skalowania maszyn wirtualnych systemu Linux (VMSS)|Jeśli ten obszar roboczy znajduje się poza zakresem przydziału, należy ręcznie przyznać uprawnienia współautora analizy dziennika (lub podobne) do identyfikatora głównego przypisania zasad.|
|\[Wersja\]zapoznawcza : Wdrażanie agenta analizy dzienników dla zestawów skalowania maszyn wirtualnych systemu Linux (VMSS)|Przypisywanie zasad|Opcjonalnie: lista obrazów maszyn wirtualnych, które obsługiwały system operacyjny Linux, aby dodać go do zakresu|Pusta tablica może służyć do wskazania żadnych parametrów opcjonalnych:\[\]|
|\[Wersja\]zapoznawcza : Wdrażanie agenta analizy dzienników dla maszyn wirtualnych z systemem Linux|Przypisywanie zasad|Obszar roboczy analizy dzienników dla maszyn wirtualnych z systemem Linux|Jeśli ten obszar roboczy znajduje się poza zakresem przydziału, należy ręcznie przyznać uprawnienia współautora analizy dziennika (lub podobne) do identyfikatora głównego przypisania zasad.|
|\[Wersja\]zapoznawcza : Wdrażanie agenta analizy dzienników dla maszyn wirtualnych z systemem Linux|Przypisywanie zasad|Opcjonalnie: lista obrazów maszyn wirtualnych, które obsługiwały system operacyjny Linux, aby dodać go do zakresu|Pusta tablica może służyć do wskazania żadnych parametrów opcjonalnych:\[\]|
|\[Wersja\]zapoznawcza : Wdrażanie agenta analizy dzienników dla zestawów skalowania maszyn wirtualnych systemu Windows (VMSS)|Przypisywanie zasad|Obszar roboczy analizy dzienników dla zestawów skalowania maszyn wirtualnych systemu Windows (VMSS)|Jeśli ten obszar roboczy znajduje się poza zakresem przydziału, należy ręcznie przyznać uprawnienia współautora analizy dziennika (lub podobne) do identyfikatora głównego przypisania zasad.|
|\[Wersja\]zapoznawcza : Wdrażanie agenta analizy dzienników dla zestawów skalowania maszyn wirtualnych systemu Windows (VMSS)|Przypisywanie zasad|Opcjonalnie: lista obrazów maszyn wirtualnych, które obsługiwały system operacyjny Windows, aby dodać go do zakresu|Pusta tablica może służyć do wskazania żadnych parametrów opcjonalnych:\[\]|
|\[Wersja\]zapoznawcza : Wdrażanie agenta analizy dzienników dla maszyn wirtualnych systemu Windows|Przypisywanie zasad|Obszar roboczy analizy dzienników dla maszyn wirtualnych systemu Windows|Jeśli ten obszar roboczy znajduje się poza zakresem przydziału, należy ręcznie przyznać uprawnienia współautora analizy dziennika (lub podobne) do identyfikatora głównego przypisania zasad.|
|\[Wersja\]zapoznawcza : Wdrażanie agenta analizy dzienników dla maszyn wirtualnych systemu Windows|Przypisywanie zasad|Opcjonalnie: lista obrazów maszyn wirtualnych, które obsługiwały system operacyjny Windows, aby dodać go do zakresu|Pusta tablica może służyć do wskazania żadnych parametrów opcjonalnych:\[\]|
|Dozwolone jednostki SKU konta magazynu|Przypisywanie zasad|Lista dozwolonych jednostek SKU magazynu|Lista jednostek SKU, które można określić dla kont magazynu.|
|Dozwolone jednostki SKU maszyny wirtualnej|Przypisywanie zasad|Lista dozwolonych jednostek SKU maszyny wirtualnej|Lista jednostek SKU, które można określić dla maszyn wirtualnych.|
|Inicjatywa planu dla ISO 27001|Przypisywanie zasad|Lista typów zasobów, które powinny mieć włączone dzienniki diagnostyczne|Lista typów zasobów do inspekcji, jeśli ustawienie dziennika diagnostycznego nie jest włączone. Dopuszczalne wartości można znaleźć w [schematach dzienników diagnostycznych usługi Azure Monitor.](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type)|

## <a name="next-steps"></a>Następne kroki

Teraz, gdy zostały sprawdzone kroki, aby wdrożyć iso 27001 przykład planu, odwiedź następujące artykuły, aby dowiedzieć się więcej o architekturze i mapowania kontroli:

> [!div class="nextstepaction"]
> [Plan ISO 27001 — przegląd](./index.md)
> [planu ISO 27001 — mapowanie sterowania](./control-mapping.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Dowiedz się więcej o [cyklu życia planu](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).
