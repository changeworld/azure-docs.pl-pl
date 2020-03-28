---
title: Wdrażanie brytyjskich oficjalnych próbek planu NHS & w Wielkiej Brytanii
description: Wdrażanie kroków dla brytyjskich próbek planu NHS i UK, w tym szczegółów parametru artefaktu planu.
ms.date: 06/26/2019
ms.topic: sample
ms.openlocfilehash: 3d69f4477163618aa225150ac6352a16e901f826
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74546395"
---
# <a name="deploy-the-uk-official-and-uk-nhs-blueprint-samples"></a>Wdrażanie próbek planu brytyjskiego OFICJALNEGO i brytyjskiego nhs

Aby wdrożyć próbki planu brytyjskiego OFICJALNEGO i brytyjskiego NHS, należy podjąć następujące kroki:

> [!div class="checklist"]
> - Tworzenie nowego planu z przykładu
> - Oznaczanie kopii próbki jako **opublikowanej**
> - Przypisywanie kopii planu do istniejącej subskrypcji

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free) przed rozpoczęciem.

## <a name="create-blueprint-from-sample"></a>Tworzenie planu z przykładu

Najpierw zaimplementuj przykład planu, tworząc nowy plan w środowisku przy użyciu próbki jako starter.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Na stronie **Wprowadzenie** po lewej stronie wybierz przycisk **Utwórz** w obszarze _Tworzenie planu_.

1. Znajdź przykład planu **BRYTYJSKIEGO OFICJALNEGO** lub **BRYTYJSKIEGO PLANU NHS** w obszarze Inne _próbki_ i wybierz użyj **tej próbki**.

1. Wprowadź _podstawy_ przykładu planu:

   - **Nazwa planu:** Podaj nazwę kopii przykładu planu.
   - **Lokalizacja definicji:** Użyj wielokropek i wybierz grupę zarządzania, aby zapisać kopię próbki.

1. Wybierz kartę _Artefakty_ u góry strony lub **Dalej: Artefakty** u dołu strony.

1. Przejrzyj listę artefaktów, które tworzą przykład planu. Wiele artefaktów ma parametry, które zdefiniujemy później. Po zakończeniu przeglądania przykładu planu wybierz pozycję Zapisz wersja **robocza.**

## <a name="publish-the-sample-copy"></a>Publikowanie przykładowej kopii

Kopia przykładu planu została utworzona w twoim środowisku. Jest tworzony w trybie **roboczym** i musi zostać **opublikowany,** zanim będzie można go przypisać i wdrożyć. Kopię przykładu planu można dostosować do środowiska i potrzeb, ale ta modyfikacja może odsunąć go od standardu.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Po lewej stronie wybierz stronę **Definicje planu.** Użyj filtrów, aby znaleźć kopię przykładu planu, a następnie wybierz go.

1. Wybierz **pozycję Publikuj plan** u góry strony. Na nowej stronie po prawej stronie podaj **wersję** dla kopii przykładu planu. Ta właściwość jest przydatna, jeśli dokonasz modyfikacji później. Podaj **notatki o zmianach,** takie jak "Pierwsza wersja opublikowana z brytyjskiej oficjalnej lub brytyjskiej próbki planu NHS". Następnie wybierz **pozycję Publikuj** u dołu strony.

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

   - Parametry artefaktu

     Parametry zdefiniowane w tej sekcji dotyczą artefaktu, w którym jest zdefiniowany. Parametry te są [parametry dynamiczne,](../../concepts/parameters.md#dynamic-parameters) ponieważ są one zdefiniowane podczas przypisywania planu. Aby uzyskać pełną listę lub parametry artefaktu i ich opisy, zobacz [Tabela parametrów artefaktów](#artifact-parameters-table).

1. Po wprowadzeniu wszystkich parametrów wybierz **pozycję Przypisz** u dołu strony. Zostanie utworzone przypisanie planu i rozpocznie się wdrażanie artefaktów. Wdrożenie trwa około godziny. Aby sprawdzić stan wdrożenia, otwórz przypisanie planu.

> [!WARNING]
> Usługa Azure Blueprints i wbudowane przykłady planu są **bezpłatne.** Zasoby platformy Azure są [wyceniane według produktu](https://azure.microsoft.com/pricing/). Kalkulator [cen](https://azure.microsoft.com/pricing/calculator/) służy do szacowania kosztów uruchomionych zasobów wdrożonych w tym przykładzie planu.

## <a name="artifact-parameters-table"></a>Tabela parametrów artefaktów

Poniższa tabela zawiera listę parametrów artefaktu planu:

Nazwa artefaktu|Typ artefaktu|Nazwa parametru|Opis|
|-|-|-|-|
|Inicjatywa Blueprint dla brytyjskiego OFICJALNEGO lub brytyjskiego NHS|Przypisywanie zasad |Typy zasobów do inspekcji dzienników diagnostycznych (Policy: Blueprint initiative for UK OFFICIAL or UK NHS) |Lista typów zasobów do inspekcji, jeśli ustawienie dziennika diagnostycznego jest włączone.  Aby uzyskać akceptowalne wartości, zobacz [Obsługiwane usługi, schematy i kategorie dzienników diagnostycznych platformy Azure.](../../../../azure-monitor/platform/diagnostic-logs-schema.md) |
|\[Wersja\]zapoznawcza : Wdrażanie agenta analizy dzienników dla maszyn wirtualnych z systemem Linux |Przypisywanie zasad |Opcjonalnie: Lista obrazów maszyn wirtualnych, które obsługiwały system \[operacyjny\]Linux, aby dodać do zakresu (Zasady: Podgląd: Wdrażanie agenta analizy dzienników dla maszyn wirtualnych z systemem Linux) |(Opcjonalnie) Wartość domyślna to _brak_. Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego usługi Log Analytics w witrynie Azure portal](../../../../azure-monitor/learn/quick-create-workspace.md). |
|\[Wersja\]zapoznawcza : Wdrażanie agenta analizy dzienników dla maszyn wirtualnych systemu Windows |Przypisywanie zasad |Opcjonalnie: Lista obrazów maszyn wirtualnych, które obsługiwały system \[operacyjny\]Windows, aby dodać go do zakresu (Zasady: Podgląd: Wdrażanie agenta analizy dzienników dla maszyn wirtualnych systemu Windows) |(Opcjonalnie) Wartość domyślna to _brak_. Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego usługi Log Analytics w witrynie Azure portal](../../../../azure-monitor/learn/quick-create-workspace.md). |

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już przeglądowi kroki, aby wdrożyć UK OFFICIAL i UK NHS plan próbek, odwiedź następujące artykuły, aby dowiedzieć się o przeglądzie i mapowania kontroli:

> [!div class="nextstepaction"]
> [Uk OFFICIAL i UK NHS plany - Przegląd](./index.md)
> [UK OFICJALNE i UK NHS plany - Mapowanie kontroli](./control-mapping.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Dowiedz się więcej o [cyklu życia planu](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).