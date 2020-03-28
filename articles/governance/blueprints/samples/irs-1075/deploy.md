---
title: Przykład planu wdrażania planu IRS 1075
description: Wdrażanie kroków dla przykładu planu IRS 1075 (Rev.11-2016), w tym szczegółów parametru artefaktu planu.
ms.date: 11/20/2019
ms.topic: sample
ms.openlocfilehash: 15fcac5bfd11d889522d078853bd6f916eb54616
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74546816"
---
# <a name="deploy-the-irs-1075-blueprint-sample"></a>Wdrażanie przykładu planu IRS 1075

Aby wdrożyć przykład planu Azure Blueprints IRS 1075 (Rev.11-2016), należy wykonać następujące kroki:

> [!div class="checklist"]
> - Tworzenie nowego planu z przykładu
> - Oznaczanie kopii próbki jako **opublikowanej**
> - Przypisywanie kopii planu do istniejącej subskrypcji

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free) przed rozpoczęciem.

## <a name="create-blueprint-from-sample"></a>Tworzenie planu z przykładu

Najpierw zaimplementuj przykład planu, tworząc nowy plan w środowisku przy użyciu próbki jako starter.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Na stronie **Wprowadzenie** po lewej stronie wybierz przycisk **Utwórz** w obszarze _Tworzenie planu_.

1. Znajdź przykład planu **IRS 1075 (Rev.11-2016)** w obszarze _Inne próbki_ i wybierz **użyj tego przykładu**.

1. Wprowadź _podstawy_ przykładu planu:

   - **Nazwa planu:** Podaj nazwę kopii przykładowego planu IRS 1075 (Rev.11-2016).
   - **Lokalizacja definicji:** Użyj wielokropek i wybierz grupę zarządzania, aby zapisać kopię próbki.

1. Wybierz kartę _Artefakty_ u góry strony lub **Dalej: Artefakty** u dołu strony.

1. Przejrzyj listę artefaktów, które tworzą przykład planu. Wiele artefaktów ma parametry, które zdefiniujemy później. Po zakończeniu przeglądania przykładu planu wybierz pozycję Zapisz wersja **robocza.**

## <a name="publish-the-sample-copy"></a>Publikowanie przykładowej kopii

Kopia przykładu planu została utworzona w twoim środowisku. Jest tworzony w trybie **roboczym** i musi zostać **opublikowany,** zanim będzie można go przypisać i wdrożyć. Kopię przykładu planu można dostosować do środowiska i potrzeb, ale ta modyfikacja może odsunąć go od wyrównania za pomocą formantów NIST SP 800-53.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Po lewej stronie wybierz stronę **Definicje planu.** Użyj filtrów, aby znaleźć kopię przykładu planu, a następnie wybierz go.

1. Wybierz **pozycję Publikuj plan** u góry strony. Na nowej stronie po prawej stronie podaj **wersję** dla kopii przykładu planu. Ta właściwość jest przydatna, jeśli dokonasz modyfikacji później. Podaj **informacje o zmianach,** takie jak "Pierwsza wersja opublikowana z przykładu planu NIST SP 800-53 R4". Następnie wybierz **pozycję Publikuj** u dołu strony.

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

|Nazwa artefaktu|Typ artefaktu|Nazwa parametru|Opis|
|-|-|-|-|
|Inspekcja kontroli i wdrażania rozszerzeń maszyn wirtualnych w celu obsługi wymagań inspekcji|Przypisywanie zasad|Identyfikator obszaru roboczego usługi Log Analytics, dla których maszyny wirtualne powinny być skonfigurowane|Jest to identyfikator (GUID) obszaru roboczego usługi Log Analytics, dla których powinny być skonfigurowane maszyny wirtualne.|
|Inspekcja kontroli i wdrażania rozszerzeń maszyn wirtualnych w celu obsługi wymagań inspekcji|Przypisywanie zasad|Lista typów zasobów, które powinny mieć włączone dzienniki diagnostyczne|Lista typów zasobów do inspekcji, jeśli ustawienie dziennika diagnostycznego nie jest włączone. Dopuszczalne wartości można znaleźć w [schematach dzienników diagnostycznych usługi Azure Monitor.](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type)|
|Inspekcja kontroli i wdrażania rozszerzeń maszyn wirtualnych w celu obsługi wymagań inspekcji|Przypisywanie zasad|Lista użytkowników, którzy powinni zostać wykluczeni z grupy Administratorzy maszyn wirtualnych systemu Windows|Oddzielona średnikami lista członków, które powinny zostać wykluczone w grupie lokalnej Administratorzy. Przykład: Administrator; myUser1; myUser2|
|Inspekcja kontroli i wdrażania rozszerzeń maszyn wirtualnych w celu obsługi wymagań inspekcji|Przypisywanie zasad|Lista użytkowników, którzy powinni zostać uwzględnieni w grupie Administratorzy maszyn wirtualnych systemu Windows|Oddzielona średnikami lista elementów członkowskich, które powinny zostać uwzględnione w grupie lokalnej Administratorzy. Przykład: Administrator; myUser1; myUser2|
|Wdrażanie agenta analizy dzienników dla zestawów skalowania maszyn wirtualnych systemu Linux (VMSS)|Przypisywanie zasad|Obszar roboczy analizy dzienników dla zestawów skalowania maszyn wirtualnych systemu Linux (VMSS)|Jeśli ten obszar roboczy znajduje się poza zakresem przydziału, należy ręcznie przyznać uprawnienia współautora analizy dziennika (lub podobne) do identyfikatora głównego przypisania zasad.|
|Wdrażanie agenta analizy dzienników dla zestawów skalowania maszyn wirtualnych systemu Linux (VMSS)|Przypisywanie zasad|Opcjonalnie: lista obrazów maszyn wirtualnych, które obsługiwały system operacyjny Linux, aby dodać go do zakresu|Pusta tablica może służyć do wskazania żadnych parametrów opcjonalnych:\[\]|
|Wdrażanie agenta analizy dzienników dla maszyn wirtualnych z systemem Linux|Przypisywanie zasad|Obszar roboczy analizy dzienników dla maszyn wirtualnych z systemem Linux|Jeśli ten obszar roboczy znajduje się poza zakresem przydziału, należy ręcznie przyznać uprawnienia współautora analizy dziennika (lub podobne) do identyfikatora głównego przypisania zasad.|
|Wdrażanie agenta analizy dzienników dla maszyn wirtualnych z systemem Linux|Przypisywanie zasad|Opcjonalnie: lista obrazów maszyn wirtualnych, które obsługiwały system operacyjny Linux, aby dodać go do zakresu|Pusta tablica może służyć do wskazania żadnych parametrów opcjonalnych:\[\]|
|Wdrażanie agenta analizy dzienników dla zestawów skalowania maszyn wirtualnych systemu Windows (VMSS)|Przypisywanie zasad|Obszar roboczy analizy dzienników dla zestawów skalowania maszyn wirtualnych systemu Windows (VMSS)|Jeśli ten obszar roboczy znajduje się poza zakresem przydziału, należy ręcznie przyznać uprawnienia współautora analizy dziennika (lub podobne) do identyfikatora głównego przypisania zasad.|
|Wdrażanie agenta analizy dzienników dla zestawów skalowania maszyn wirtualnych systemu Windows (VMSS)|Przypisywanie zasad|Opcjonalnie: lista obrazów maszyn wirtualnych, które obsługiwały system operacyjny Windows, aby dodać go do zakresu|Pusta tablica może służyć do wskazania żadnych parametrów opcjonalnych:\[\]|
|Wdrażanie agenta analizy dzienników dla maszyn wirtualnych systemu Windows|Przypisywanie zasad|Obszar roboczy analizy dzienników dla maszyn wirtualnych systemu Windows|Jeśli ten obszar roboczy znajduje się poza zakresem przydziału, należy ręcznie przyznać uprawnienia współautora analizy dziennika (lub podobne) do identyfikatora głównego przypisania zasad.|
|Wdrażanie agenta analizy dzienników dla maszyn wirtualnych systemu Windows|Przypisywanie zasad|Opcjonalnie: lista obrazów maszyn wirtualnych, które obsługiwały system operacyjny Windows, aby dodać go do zakresu|Pusta tablica może służyć do wskazania żadnych parametrów opcjonalnych:\[\]|
|Wdrażanie zaawansowanej ochrony przed zagrożeniami na kontach magazynu|Przypisywanie zasad|Efekt|Informacje o efektach zasad można znaleźć na stronie [Opis efektów zasad platformy Azure](../../../policy/concepts/effects.md)|
|Wdrażanie inspekcji na serwerach SQL|Przypisywanie zasad|Wartość w dniach okresu przechowywania (0 oznacza nieograniczoną retencję)|Dni przechowywania (opcjonalnie, 180 dni, jeśli nieokreślone)|
|Wdrażanie inspekcji na serwerach SQL|Przypisywanie zasad|Nazwa grupy zasobów dla konta magazynu do inspekcji serwera SQL|Inspekcja zapisuje zdarzenia bazy danych w dzienniku inspekcji na koncie usługi Azure Storage (konto magazynu zostanie utworzone w każdym regionie, w którym zostanie utworzony program SQL Server, który będzie współużytkowany przez wszystkie serwery w tym regionie). Ważne — dla prawidłowego działania inspekcji nie należy usuwać ani zmieniać nazwy grupy zasobów ani kont magazynu.|
|Wdrażanie ustawień diagnostycznych dla grup zabezpieczeń sieciowych|Przypisywanie zasad|Prefiks konta magazynu dla diagnostyki sieciowej grupy zabezpieczeń|Ten prefiks zostanie połączony z lokalizacją sieciowej grupy zabezpieczeń w celu utworzenia nazwy konta magazynu.|
|Wdrażanie ustawień diagnostycznych dla grup zabezpieczeń sieciowych|Przypisywanie zasad|Nazwa grupy zasobów dla konta magazynu dla diagnostyki sieciowej grupy zabezpieczeń (musi istnieć)|Grupa zasobów, w których zostanie utworzone konto magazynu. Ta grupa zasobów musi już istnieć.|

## <a name="next-steps"></a>Następne kroki

Teraz, gdy przejrzysz kroki, aby wdrożyć przykład planu IRS 1075 (Rev.11-2016), odwiedź następujące artykuły, aby dowiedzieć się więcej o mapowaniu planu i kontroli:

> [!div class="nextstepaction"]
> [Plan IRS 1075 — przegląd](./index.md)
> [planu IRS 1075 — mapowanie sterowania](./control-mapping.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Dowiedz się więcej o [cyklu życia planu](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).