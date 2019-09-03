---
title: Przykłady — plan CAF Foundation — kroki wdrażania
description: Wdróż kroki przykładu planu CAF Foundation.
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/20/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.custom: fasttrack-new
ms.openlocfilehash: f4235599d3156c35b50aea1815f3d62d925f326a
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231854"
---
# <a name="deploy-the-microsoft-cloud-adoption-framework-for-azure-foundation-blueprint-sample"></a>Wdróż platformę wdrażania Microsoft Cloud dla przykładowego planu usługi Azure Foundation

Aby wdrożyć przykład strategii Microsoft Cloud wdrażania platformy Azure (CAF) Foundation, należy wykonać następujące czynności:

> [!div class="checklist"]
> - Utwórz nowy plan z przykładu
> - Oznacz swoją kopię przykładowej publikacji jako opublikowaną
> - Przypisz swoją kopię planu do istniejącej subskrypcji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

## <a name="create-blueprint-from-sample"></a>Utwórz plan z przykładu

Najpierw Zaimplementuj przykład strategii, tworząc nowy plan w środowisku przy użyciu przykładu jako starter.

1. Wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **zasady** w okienku po lewej stronie. Na stronie **zasady** wybierz pozycję **plany**.

1. Na stronie **pierwsze kroki** z lewej strony wybierz przycisk **Utwórz** w obszarze _Utwórz plan_.

1. Znajdź przykład planu **CAF Foundation** pod _innymi_ przykładami i wybierz opcję **Użyj tego przykładu**.

1. Wprowadź _podstawy_ przykładu planu:

   - **Nazwa**planu: Podaj nazwę kopii przykładowej strategii CAF Foundation.
   - **Lokalizacja definicji**: Użyj wielokropka i wybierz grupę zarządzania, w której ma zostać zapisana kopia przykładu.

1. Wybierz kartę _artefakty_ w górnej części strony lub **Następny: Artefakty** w dolnej części strony.

1. Zapoznaj się z listą artefaktów, które składają się na przykład strategii. Wiele artefaktów ma parametry, które zostaną zdefiniowane w dalszej części. Wybierz pozycję **Zapisz wersję roboczą** po zakończeniu przeglądania przykładu planu.

## <a name="publish-the-sample-copy"></a>Publikowanie kopii przykładowej

Twoja kopia przykładu strategii została teraz utworzona w Twoim środowisku. Jest on tworzony w trybie **wersji roboczej** i musi być **opublikowany** , aby można go było przypisać i wdrożyć. Kopię przykładowej strategii można dostosować do środowiska i potrzeb, ale modyfikacja może ją przenieść z planu CAF Foundation.

1. Wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **zasady** w okienku po lewej stronie. Na stronie **zasady** wybierz pozycję **plany**.

1. Wybierz stronę **definicje** strategii po lewej stronie. Użyj filtrów, aby znaleźć kopię przykładowej strategii, a następnie wybierz ją.

1. Wybierz pozycję **Publikuj plan** w górnej części strony. Na nowej stronie z prawej strony Podaj **wersję** kopii przykładowej strategii. Ta właściwość jest przydatna w przypadku późniejszej modyfikacji. Podaj **informacje** o zmianach, takie jak "Pierwsza wersja opublikowana z przykładu planu CAF Foundation". Następnie wybierz pozycję **Publikuj** w dolnej części strony.

## <a name="assign-the-sample-copy"></a>Przypisz przykładową kopię

Po pomyślnym opublikowaniu kopii przykładowej strategiimożna ją przypisać do subskrypcji w grupie zarządzania, w której został on zapisany. Ten krok polega na tym, że parametry są dostarczane, aby każde wdrożenie kopii przykładowej strategii było unikatowe.

1. Wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **zasady** w okienku po lewej stronie. Na stronie **zasady** wybierz pozycję **plany**.

1. Wybierz stronę **definicje** strategii po lewej stronie. Użyj filtrów, aby znaleźć kopię przykładowej strategii, a następnie wybierz ją.

1. Wybierz pozycję **Przypisz plan** w górnej części strony definicji planu.

1. Podaj wartości parametrów dla przypisania planu:

   - Ustawienia podstawowe
       - **Subskrypcje**: Wybierz co najmniej jedną subskrypcję znajdującą się w grupie zarządzania, do której została zapisana kopia przykładu planu. W przypadku wybrania więcej niż jednej subskrypcji zostanie utworzone przypisanie dla każdego z nich przy użyciu wprowadzonych parametrów.
     - **Nazwa przypisania**: Nazwa jest wstępnie wypełniana na podstawie nazwy planu.
       Zmień stosownie do potrzeb lub pozostaw jako is.
     - **Lokalizacja**: Wybierz region, w którym ma zostać utworzona zarządzana tożsamość.
     - Usługa Azure Blueprint używa tej tożsamości zarządzanej do wdrażania wszystkich artefaktów w przypisanej strategii.
       Aby dowiedzieć się więcej, zobacz [Tożsamości zarządzane dla zasobów platformy Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Wersja definicji**planu: Wybierz **opublikowaną** wersję kopii przykładowej strategii.

   - Zablokuj przypisanie

     Wybierz ustawienie blokowania planu dla danego środowiska. Aby uzyskać więcej informacji, zobacz [blokowanie zasobów strategii](../../concepts/resource-locking.md).

   - Tożsamość zarządzana

     Wybierz opcję Domyślna przypisana przez _system_ lub opcję tożsamości przypisanej do _użytkownika_ .

   - Parametry planu

     Parametry zdefiniowane w tej sekcji są używane przez wiele artefaktów w definicji strategii w celu zapewnienia spójności.

     - **Organizacja**: Wprowadź nazwę organizacji, taką jak contoso, i musi być unikatowa.
     - **Region świadczenia usługi Azure**: Wybierz region platformy Azure na potrzeby wdrożenia.
     - **Dozwolone lokalizacje**: W jakich regionach platformy Azure będzie można tworzyć zasoby?
     
   - Parametry artefaktu

     Parametry zdefiniowane w tej sekcji dotyczą artefaktu, w ramach którego jest zdefiniowany. Parametry te są [parametrami dynamicznymi](../../concepts/parameters.md#dynamic-parameters) , ponieważ są one definiowane podczas przypisywania planu. Aby uzyskać pełną listę lub parametry artefaktów oraz ich opisy, zobacz [tabela parametrów artefaktów](#artifact-parameters-table).

1. Po wprowadzeniu wszystkich parametrów wybierz pozycję **Przypisz** w dolnej części strony. Utworzono przypisanie strategii i rozpocznie się wdrażanie artefaktów. Wdrażanie zajmuje trochę czasu. Aby sprawdzić stan wdrożenia, Otwórz przypisanie planu.

> [!WARNING]
> Usługa plany platformy Azure i wbudowane przykłady planów są **bezpłatne**. Zasoby platformy Azure są [wyceniane według produktu](https://azure.microsoft.com/pricing/). Za pomocą [kalkulatora cen](https://azure.microsoft.com/pricing/calculator/) można oszacować koszt uruchamiania zasobów wdrożonych przez ten przykład strategii.

## <a name="artifact-parameters-table"></a>Tabela parametrów artefaktów

Poniższa tabela zawiera listę parametrów artefaktów strategii:

|Nazwa artefaktu|Typ artefaktu|Nazwa parametru|Opis|
|-|-|-|-|
|Dozwolone jednostki SKU konta magazynu|Przypisanie zasad|Policy_Allowed-StorageAccount-SKU|Jednostka SKU używana na kontach magazynu dzienników diagnostycznych|
|Dozwolone jednostki SKU maszyn wirtualnych|Przypisanie zasad|Policy_Allowed-VM-SKU|Dozwolone jednostki SKU maszyn wirtualnych|
|Dołącz TAG CostCenter do grup zasobów|Przypisanie zasad|Policy_CostCenter_Tag|Dołącz TAG CostCenter i jego wartość z grupy zasobów|
|Typy zasobów, których nie chcesz zezwalać w danym środowisku|Przypisanie zasad|Zasady _Allowed — typy zasobów|Które zasoby platformy Azure, które mają być dozwolone w danym środowisku|
|Wdróż Key Vault|Szablon usługi Resource Manager|KV — AccessPolicy|**Zablokowany** — Grupa lub użytkownik <Object ID> usługi Azure AD, aby przyznać uprawnienia do usługi w Key Vault|
|Wdróż Log Analytics|Szablon usługi Resource Manager|LogAnalytics_DataRetention|Dane z zablokowanej liczby dni zostaną zachowane w log Analytics|
|Wdróż Log Analytics|Szablon usługi Resource Manager|LogAnalytics_Location|Region **zablokowany** używany podczas ustanawiania obszaru roboczego|

## <a name="next-steps"></a>Następne kroki

Po przejrzeniu kroków związanych z wdrażaniem przykładu planu CAF Foundation zapoznaj się z następującym artykułem, aby dowiedzieć się więcej o architekturze:

> [!div class="nextstepaction"]
> [Plan CAF Foundation — Omówienie](./index.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Dowiedz się więcej o [cyklu życia](../../concepts/lifecycle.md)planu.
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).