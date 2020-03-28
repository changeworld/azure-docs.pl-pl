---
title: Przykład planu dod impact level 4
description: Wdrażanie kroków dla przykładu planu dod impact level 4, w tym szczegółów parametru artefaktu planu.
ms.date: 03/06/2020
ms.topic: sample
ms.openlocfilehash: c92e24327bc1879b8fea89cf85a50f845e473810
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79415313"
---
# <a name="deploy-the-dod-impact-level-4-blueprint-sample"></a>Wdrażanie przykładu planu dod impact level 4

Aby wdrożyć przykład planu 4 planu działania departamentu obrony platformy Azure, należy wykonać następujące kroki:

> [!div class="checklist"]
> - Tworzenie nowego planu z przykładu
> - Oznaczanie kopii próbki jako **opublikowanej**
> - Przypisywanie kopii planu do istniejącej subskrypcji

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free) przed rozpoczęciem.

## <a name="create-blueprint-from-sample"></a>Tworzenie planu z przykładu

Najpierw zaimplementuj przykład planu, tworząc nowy plan w środowisku przy użyciu próbki jako starter.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Na stronie **Wprowadzenie** po lewej stronie wybierz przycisk **Utwórz** w obszarze _Tworzenie planu_.

1. Znajdź przykład planu **DoD Impact Level 4** w obszarze _Inne przykłady_ i wybierz opcję **Użyj tego przykładu**.

1. Wprowadź _podstawy_ przykładu planu:

   - **Nazwa planu:** Podaj nazwę kopii przykładu planu dod impact level 4.
   - **Lokalizacja definicji:** Użyj wielokropek i wybierz grupę zarządzania, aby zapisać kopię próbki.

1. Wybierz kartę _Artefakty_ u góry strony lub **Dalej: Artefakty** u dołu strony.

1. Przejrzyj listę artefaktów, które tworzą przykład planu. Wiele artefaktów ma parametry, które zdefiniujemy później. Po zakończeniu przeglądania przykładu planu wybierz pozycję Zapisz wersja **robocza.**

## <a name="publish-the-sample-copy"></a>Publikowanie przykładowej kopii

Kopia przykładu planu została utworzona w twoim środowisku. Jest tworzony w trybie **roboczym** i musi zostać **opublikowany,** zanim będzie można go przypisać i wdrożyć. Kopię przykładu planu można dostosować do środowiska i potrzeb, ale ta modyfikacja może odsunąć go od wyrównania za pomocą formantów DoD Impact Level 4.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Po lewej stronie wybierz stronę **Definicje planu.** Użyj filtrów, aby znaleźć kopię przykładu planu, a następnie wybierz go.

1. Wybierz **pozycję Publikuj plan** u góry strony. Na nowej stronie po prawej stronie podaj **wersję** dla kopii przykładu planu. Ta właściwość jest przydatna, jeśli dokonasz modyfikacji później. Podaj **uwagi dotyczące zmian,** takie jak "Pierwsza wersja opublikowana z przykładu planu DoD IL4". Następnie wybierz **pozycję Publikuj** u dołu strony.

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
|\[Podgląd\]: DoD Impact Poziom 4|Przypisywanie zasad|Identyfikator obszaru roboczego usługi Log Analytics, dla których maszyny wirtualne powinny być skonfigurowane|Jest to identyfikator (GUID) obszaru roboczego usługi Log Analytics, dla których powinny być skonfigurowane maszyny wirtualne.|
|\[Podgląd\]: DoD Impact Poziom 4|Przypisywanie zasad|Lista typów zasobów, które powinny mieć włączone dzienniki diagnostyczne|Lista typów zasobów do inspekcji, jeśli ustawienie dziennika diagnostycznego nie jest włączone. Dopuszczalne wartości można znaleźć w [schematach dzienników diagnostycznych usługi Azure Monitor.](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type)|
|\[Podgląd\]: DoD Impact Poziom 4|Przypisywanie zasad|Lista użytkowników, którzy powinni zostać wykluczeni z grupy Administratorzy maszyn wirtualnych systemu Windows|Oddzielona średnikami lista członków, które powinny zostać wykluczone w grupie lokalnej Administratorzy. Przykład: Administrator; myUser1; myUser2|
|\[Podgląd\]: DoD Impact Poziom 4|Przypisywanie zasad|Lista użytkowników, którzy powinni zostać uwzględnieni w grupie Administratorzy maszyn wirtualnych systemu Windows|Oddzielona średnikami lista elementów członkowskich, które powinny zostać uwzględnione w grupie lokalnej Administratorzy. Przykład: Administrator; myUser1; myUser2|
|\[Wersja\]zapoznawcza : Wdrażanie agenta analizy dzienników dla zestawów skalowania maszyn wirtualnych systemu Linux (VMSS)|Przypisywanie zasad|Obszar roboczy analizy dzienników dla zestawów skalowania maszyn wirtualnych systemu Linux (VMSS)|Jeśli ten obszar roboczy znajduje się poza zakresem przydziału, należy ręcznie przyznać uprawnienia współautora analizy dziennika (lub podobne) do identyfikatora głównego przypisania zasad.|
|\[Wersja\]zapoznawcza : Wdrażanie agenta analizy dzienników dla zestawów skalowania maszyn wirtualnych systemu Linux (VMSS)|Przypisywanie zasad|Opcjonalnie: lista obrazów maszyn wirtualnych, które obsługiwały system operacyjny Linux, aby dodać go do zakresu|Pusta tablica może służyć do wskazania żadnych parametrów opcjonalnych:\[\]|
|\[Wersja\]zapoznawcza : Wdrażanie agenta analizy dzienników dla maszyn wirtualnych z systemem Linux|Przypisywanie zasad|Obszar roboczy analizy dzienników dla maszyn wirtualnych z systemem Linux|Jeśli ten obszar roboczy znajduje się poza zakresem przydziału, należy ręcznie przyznać uprawnienia współautora analizy dziennika (lub podobne) do identyfikatora głównego przypisania zasad.|
|\[Wersja\]zapoznawcza : Wdrażanie agenta analizy dzienników dla maszyn wirtualnych z systemem Linux|Przypisywanie zasad|Opcjonalnie: lista obrazów maszyn wirtualnych, które obsługiwały system operacyjny Linux, aby dodać go do zakresu|Pusta tablica może służyć do wskazania żadnych parametrów opcjonalnych:\[\]|
|\[Wersja\]zapoznawcza : Wdrażanie agenta analizy dzienników dla zestawów skalowania maszyn wirtualnych systemu Windows (VMSS)|Przypisywanie zasad|Obszar roboczy analizy dzienników dla zestawów skalowania maszyn wirtualnych systemu Windows (VMSS)|Jeśli ten obszar roboczy znajduje się poza zakresem przydziału, należy ręcznie przyznać uprawnienia współautora analizy dziennika (lub podobne) do identyfikatora głównego przypisania zasad.|
|\[Wersja\]zapoznawcza : Wdrażanie agenta analizy dzienników dla zestawów skalowania maszyn wirtualnych systemu Windows (VMSS)|Przypisywanie zasad|Opcjonalnie: lista obrazów maszyn wirtualnych, które obsługiwały system operacyjny Windows, aby dodać go do zakresu|Pusta tablica może służyć do wskazania żadnych parametrów opcjonalnych:\[\]|
|\[Wersja\]zapoznawcza : Wdrażanie agenta analizy dzienników dla maszyn wirtualnych systemu Windows|Przypisywanie zasad|Obszar roboczy analizy dzienników dla maszyn wirtualnych systemu Windows|Jeśli ten obszar roboczy znajduje się poza zakresem przydziału, należy ręcznie przyznać uprawnienia współautora analizy dziennika (lub podobne) do identyfikatora głównego przypisania zasad.|
|\[Wersja\]zapoznawcza : Wdrażanie agenta analizy dzienników dla maszyn wirtualnych systemu Windows|Przypisywanie zasad|Opcjonalnie: lista obrazów maszyn wirtualnych, które obsługiwały system operacyjny Windows, aby dodać go do zakresu|Pusta tablica może służyć do wskazania żadnych parametrów opcjonalnych:\[\]|
|Wdrażanie zaawansowanej ochrony przed zagrożeniami na kontach magazynu|Przypisywanie zasad|Efekt|Informacje o efektach zasad można znaleźć na stronie [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|Wdrażanie inspekcji na serwerach SQL|Przypisywanie zasad|Wartość w dniach okresu przechowywania (0 oznacza nieograniczoną retencję)|Dni przechowywania (opcjonalnie, 180 dni, jeśli nieokreślone)|
|Wdrażanie inspekcji na serwerach SQL|Przypisywanie zasad|Nazwa grupy zasobów dla konta magazynu do inspekcji serwera SQL|Inspekcja zapisuje zdarzenia bazy danych w dzienniku inspekcji na koncie usługi Azure Storage (konto magazynu zostanie utworzone w każdym regionie, w którym zostanie utworzony program SQL Server, który będzie współużytkowany przez wszystkie serwery w tym regionie). Ważne — dla prawidłowego działania inspekcji nie należy usuwać ani zmieniać nazwy grupy zasobów ani kont magazynu.|
|Wdrażanie ustawień diagnostycznych dla grup zabezpieczeń sieciowych|Przypisywanie zasad|Prefiks konta magazynu dla diagnostyki sieciowej grupy zabezpieczeń|Ten prefiks zostanie połączony z lokalizacją sieciowej grupy zabezpieczeń w celu utworzenia nazwy konta magazynu.|
|Wdrażanie ustawień diagnostycznych dla grup zabezpieczeń sieciowych|Przypisywanie zasad|Nazwa grupy zasobów dla konta magazynu dla diagnostyki sieciowej grupy zabezpieczeń (musi istnieć)|Grupa zasobów, w których zostanie utworzone konto magazynu. Ta grupa zasobów musi już istnieć.|
|\[Podgląd\]: DoD Impact Poziom 4|Przypisywanie zasad|Dozwolone lokalizacje zasobów i grup zasobów|Lista lokalizacji platformy Azure, które organizacja może określić podczas wdrażania zasobów. Ta podana wartość jest również używana przez zasady "Dozwolone lokalizacje" w ramach inicjatywy dotyczącej polityki.|
|\[Podgląd\]: DoD Impact Poziom 4|Przypisywanie zasad|Ocena luk w zabezpieczeniach powinna być włączona w wystąpieniach zarządzanych SQL|Informacje o efektach zasad można znaleźć na stronie [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Podgląd\]: DoD Impact Poziom 4|Przypisywanie zasad|Ocena luk w zabezpieczeniach powinna być włączona na serwerach SQL|Informacje o efektach zasad można znaleźć na stronie [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Podgląd\]: DoD Impact Poziom 4|Przypisywanie zasad|Ocena luk w zabezpieczeniach powinna być włączona na maszynach wirtualnych|Informacje o efektach zasad można znaleźć na stronie [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Podgląd\]: DoD Impact Poziom 4|Przypisywanie zasad|Magazyn geograficznie nadmiarowy powinien być włączony dla kont magazynu|Informacje o efektach zasad można znaleźć na stronie [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Podgląd\]: DoD Impact Poziom 4|Przypisywanie zasad|Kopia zapasowa zbędna geograficznie powinna być włączona dla usługi Azure Database dla mariadb|Informacje o efektach zasad można znaleźć na stronie [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Podgląd\]: DoD Impact Poziom 4|Przypisywanie zasad|Kopia zapasowa zbędna geograficznie powinna być włączona dla usługi Azure Database dla mysql|Informacje o efektach zasad można znaleźć na stronie [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Podgląd\]: DoD Impact Poziom 4|Przypisywanie zasad|Kopia zapasowa zbędna geograficznie powinna być włączona dla usługi Azure Database dla postgreSQL|Informacje o efektach zasad można znaleźć na stronie [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Podgląd\]: DoD Impact Poziom 4|Przypisywanie zasad|Reguły sieciowej grupy zabezpieczeń dla maszyn wirtualnych z dostępem do Internetu powinny zostać zaostrzone|Informacje o efektach zasad można znaleźć na stronie [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Podgląd\]: DoD Impact Poziom 4|Przypisywanie zasad|Aplikacja internetowa powinna być dostępna tylko za pośrednictwem protokołu HTTPS|Informacje o efektach zasad można znaleźć na stronie [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Podgląd\]: DoD Impact Poziom 4|Przypisywanie zasad|Aplikacja function powinna być dostępna tylko za pośrednictwem protokołu HTTPS|Informacje o efektach zasad można znaleźć na stronie [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Podgląd\]: DoD Impact Poziom 4|Przypisywanie zasad|Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji|Informacje o efektach zasad można znaleźć na stronie [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Podgląd\]: DoD Impact Poziom 4|Przypisywanie zasad|Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji|Informacje o efektach zasad można znaleźć na stronie [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Podgląd\]: DoD Impact Poziom 4|Przypisywanie zasad|Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji|Informacje o efektach zasad można znaleźć na stronie [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Podgląd\]: DoD Impact Poziom 4|Przypisywanie zasad|Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji|Informacje o efektach zasad można znaleźć na stronie [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Podgląd\]: DoD Impact Poziom 4|Przypisywanie zasad|Przestarzałe konta powinny zostać usunięte z subskrypcji|Informacje o efektach zasad można znaleźć na stronie [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Podgląd\]: DoD Impact Poziom 4|Przypisywanie zasad|CORS nie powinien zezwalać każdemu zasobowi na dostęp do aplikacji sieci Web|Informacje o efektach zasad można znaleźć na stronie [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Podgląd\]: DoD Impact Poziom 4|Przypisywanie zasad|Należy zainstalować aktualizacje systemu w zestawach skalowania maszyny wirtualnej|Informacje o efektach zasad można znaleźć na stronie [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Podgląd\]: DoD Impact Poziom 4|Przypisywanie zasad|Usługa MFA powinna być włączona na kontach z uprawnieniami do odczytu w ramach subskrypcji|Informacje o efektach zasad można znaleźć na stronie [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Podgląd\]: DoD Impact Poziom 4|Przypisywanie zasad|Usługa MFA powinna być włączona na kontach z uprawnieniami właściciela w ramach subskrypcji|Informacje o efektach zasad można znaleźć na stronie [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Podgląd\]: DoD Impact Poziom 4|Przypisywanie zasad|Usługa MFA powinna być włączona na kontach z uprawnieniami do zapisu w ramach subskrypcji|Informacje o efektach zasad można znaleźć na stronie [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Podgląd\]: DoD Impact Poziom 4|Przypisywanie zasad|Długoterminowa geograficzna nadmiarowa kopia zapasowa powinna być włączona dla baz danych SQL azure|Informacje o efektach zasad można znaleźć na stronie [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|Dozwolone lokalizacje|Przypisanie zasad|Dozwolone lokalizacje|Ta zasada umożliwia ograniczenie lokalizacji, które organizacja może określić podczas wdrażania zasobów. Służy do wymuszania wymagań dotyczących zgodności obszarów geograficznych.|
|Dozwolone lokalizacje dla grup zasobów|Przypisanie zasad |Dozwolone lokalizacje|Ta zasada umożliwia ograniczenie lokalizacji, w których organizacja może tworzyć grupy zasobów. Służy do wymuszania wymagań dotyczących zgodności obszarów geograficznych.|

## <a name="next-steps"></a>Następne kroki

Teraz, gdy przejrzysz kroki, aby wdrożyć przykład planu DoD Impact Level 4, odwiedź następujące artykuły, aby dowiedzieć się więcej o mapowaniu planu i kontroli:

> [!div class="nextstepaction"]
> [Plan poziomu 4 dod impact - przegląd](./index.md)
> [planu dod impact level 4 - mapowanie sterowania](./control-mapping.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Dowiedz się więcej o [cyklu życia planu](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).