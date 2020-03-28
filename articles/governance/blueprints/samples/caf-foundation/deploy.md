---
title: Wdrażanie przykładowego planu fundacji CAF
description: Wdrażanie kroków dla przykładu planu CAF Foundation, w tym szczegóły parametru artefaktu planu.
ms.date: 08/20/2019
ms.topic: sample
ms.openlocfilehash: df17f8c3f539e25635ea4718be9d51d5e5e3f708
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74545515"
---
# <a name="deploy-the-microsoft-cloud-adoption-framework-for-azure-foundation-blueprint-sample"></a>Wdrażanie przykładowego planu wdrażania platformy Microsoft Cloud Framework dla platformy Azure Foundation

Aby wdrożyć przykład planu planu microsoft cloud adoption framework dla platformy Azure (CAF), należy wykonać następujące kroki:

> [!div class="checklist"]
> - Tworzenie nowego planu z przykładu
> - Oznaczanie kopii próbki jako **opublikowanej**
> - Przypisywanie kopii planu do istniejącej subskrypcji

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free) przed rozpoczęciem.

## <a name="create-blueprint-from-sample"></a>Tworzenie planu z przykładu

Najpierw zaimplementuj przykład planu, tworząc nowy plan w środowisku przy użyciu próbki jako starter.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Na stronie **Wprowadzenie** po lewej stronie wybierz przycisk **Utwórz** w obszarze _Tworzenie planu_.

1. Znajdź przykład planu **CAF Foundation** w obszarze _Inne próbki_ i wybierz opcję Użyj **tego przykładu**.

1. Wprowadź _podstawy_ przykładu planu:

   - **Nazwa planu**: Podaj nazwę kopii przykładu planu CAF Foundation.
   - **Lokalizacja definicji:** Użyj wielokropek i wybierz grupę zarządzania, aby zapisać kopię próbki.

1. Wybierz kartę _Artefakty_ u góry strony lub **Dalej: Artefakty** u dołu strony.

1. Przejrzyj listę artefaktów, które tworzą przykład planu. Wiele artefaktów ma parametry, które zdefiniujemy później. Po zakończeniu przeglądania przykładu planu wybierz pozycję Zapisz wersja **robocza.**

## <a name="publish-the-sample-copy"></a>Publikowanie przykładowej kopii

Kopia przykładu planu została utworzona w twoim środowisku. Jest tworzony w trybie **roboczym** i musi zostać **opublikowany,** zanim będzie można go przypisać i wdrożyć. Kopię przykładu planu można dostosować do środowiska i potrzeb, ale ta modyfikacja może odsunąć go od planu CAF Foundation.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Po lewej stronie wybierz stronę **Definicje planu.** Użyj filtrów, aby znaleźć kopię przykładu planu, a następnie wybierz go.

1. Wybierz **pozycję Publikuj plan** u góry strony. Na nowej stronie po prawej stronie podaj **wersję** dla kopii przykładu planu. Ta właściwość jest przydatna, jeśli dokonasz modyfikacji później. Podaj **notatki o zmianach,** takie jak "Pierwsza wersja opublikowana z przykładowego planu CAF Foundation". Następnie wybierz **pozycję Publikuj** u dołu strony.

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
     - **Lokalizacja:** Wybierz region dla zarządzanej tożsamości, w którym ma zostać utworzona.
     - Usługa Azure Blueprint używa tej tożsamości zarządzanej do wdrażania wszystkich artefaktów w przypisanej strategii.
       Aby dowiedzieć się więcej, zobacz [tożsamości zarządzane dla zasobów platformy Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Wersja definicji planu:** Wybierz **opublikowaną** wersję kopii przykładu planu.

   - Przypisanie blokady

     Wybierz ustawienie blokady planu dla środowiska. Aby uzyskać więcej informacji, zobacz [blokowanie zasobów strategii](../../concepts/resource-locking.md).

   - Tożsamość zarządzana

     Wybierz domyślną opcję tożsamości _zarządzanej przypisaną przez system_ lub opcję tożsamości _przypisaną przez użytkownika._

   - Parametry strategii

     Parametry zdefiniowane w tej sekcji są używane przez wiele artefaktów w definicji planu, aby zapewnić spójność.

     - **Organizacja**: Wprowadź nazwę organizacji, taką jak Contoso, musi być unikatowa.
     - **Region platformy Azure:** wybierz region platformy Azure do wdrożenia.
     - **Dozwolone lokalizacje:** Które regiony platformy Azure zezwolisz na wbudowanie zasobów?
     
   - Parametry artefaktu

     Parametry zdefiniowane w tej sekcji dotyczą artefaktu, w którym jest zdefiniowany. Parametry te są [parametry dynamiczne,](../../concepts/parameters.md#dynamic-parameters) ponieważ są one zdefiniowane podczas przypisywania planu. Aby uzyskać pełną listę lub parametry artefaktu i ich opisy, zobacz [Tabela parametrów artefaktów](#artifact-parameters-table).

1. Po wprowadzeniu wszystkich parametrów wybierz **pozycję Przypisz** u dołu strony. Zostanie utworzone przypisanie planu i rozpocznie się wdrażanie artefaktów. Wdrożenie trwa około godziny. Aby sprawdzić stan wdrożenia, otwórz przypisanie planu.

> [!WARNING]
> Usługa Azure Blueprints i wbudowane przykłady planu są **bezpłatne.** Zasoby platformy Azure są [wyceniane według produktu](https://azure.microsoft.com/pricing/). Kalkulator [cen](https://azure.microsoft.com/pricing/calculator/) służy do szacowania kosztów uruchomionych zasobów wdrożonych w tym przykładzie planu.

## <a name="artifact-parameters-table"></a>Tabela parametrów artefaktów

Poniższa tabela zawiera listę parametrów artefaktu planu:

|Nazwa artefaktu|Typ artefaktu|Nazwa parametru|Opis|
|-|-|-|-|
|Dozwolone jednostki SKU konta magazynu|Przypisywanie zasad|Policy_Allowed-StorageAccount-SKU|Jednostka SKU używana na kontach magazynu dziennika diagnostycznego|
|Dozwolone jednostki SKU maszyny wirtualnej|Przypisywanie zasad|Policy_Allowed-VM-SKU|Dozwolone jednostki SKU maszyny wirtualnej|
|Dołączanie znacznika costcenter do grup zasobów|Przypisywanie zasad|Policy_CostCenter_Tag|Dołącz tag costcenter i jego wartość z grupy zasobów|
|Typy zasobów, na które nie chcesz zezwalać w swoim środowisku|Przypisywanie zasad|Typy zasad _Allowed zasobami|Zasoby platformy Azure, które chcesz zezwolić w swoim środowisku|
|Wdrażanie magazynu kluczy|Szablon usługi Resource Manager|KV-AccessPolicy|**Zablokowane** — grupa usługi <Object ID> Azure AD lub użytkownik do udzielania uprawnień w magazynie kluczy|
|Wdrażanie analizy dzienników|Szablon usługi Resource Manager|LogAnalytics_DataRetention|**Zablokowane** — liczba dni, przez które dane będą przechowywane w usłudze Log Analytics|
|Wdrażanie analizy dzienników|Szablon usługi Resource Manager|LogAnalytics_Location|**Zablokowane** — region używany podczas ustanawiania obszaru roboczego|

## <a name="next-steps"></a>Następne kroki

Teraz, gdy zostały sprawdzone kroki, aby wdrożyć przykład planu CAF Foundation, odwiedź następujący artykuł, aby dowiedzieć się więcej o architekturze:

> [!div class="nextstepaction"]
> [Plan Fundacji CAF - Przegląd](./index.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Dowiedz się więcej o [cyklu życia planu](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).