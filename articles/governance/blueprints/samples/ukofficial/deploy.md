---
title: Przykład — oficjalne UK i NHS UK planu — wdrażanie kroki
description: Wdróż kroki oficjalne UK i przykładów planu NHS Zjednoczone Królestwo.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/26/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 43aae882f27031d3e51ac8a4f5a68d243a973d6d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453205"
---
# <a name="deploy-the-uk-official-and-uk-nhs-blueprint-samples"></a>Wdrażanie oficjalne UK i przykłady planu NHS Zjednoczone Królestwo

Aby wdrożyć oficjalne UK i przykłady planu NHS Zjednoczone Królestwo, należy podjąć następujące kroki:

> [!div class="checklist"]
> - Tworzenie nowego planu z próbki
> - Oznacz kopii przykładu jako **opublikowano**
> - Przypisz swoją kopię planu do istniejącej subskrypcji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

## <a name="create-blueprint-from-sample"></a>Utwórz plan z próbki

Najpierw należy zaimplementować przykładowy plan przez utworzenie nowego planu w środowisku przy użyciu przykładu jako początkowego.

1. Wybierz **wszystkich usług** Wyszukaj i wybierz pozycję **zasad** w okienku po lewej stronie. Na **zasad** wybierz opcję **plany**.

1. Z **wprowadzenie** strony po lewej stronie, wybierz opcję **Utwórz** przycisku w obszarze _Tworzenie planu_.

1. Znajdź **oficjalne UK** lub **NHS Zjednoczone Królestwo** przykładowy plan w ramach _inne przykłady_ i wybierz **korzystać z tej próbki**.

1. Wprowadź _podstawy_ próbki planu:

   - **Nazwa planu**: Podaj nazwę dla tej kopii przykładu planu.
   - **Lokalizacja definicji**: Użyj wielokropek i wybierz grupę zarządzania, aby zapisać kopię próbki.

1. Wybierz _artefaktów_ kartę w górnej części strony lub **dalej: Artefakty** w dolnej części strony.

1. Przejrzyj listę artefaktów, które tworzą przykładowy plan. Wiele artefaktów ma parametry, które zdefiniujemy później. Wybierz **Zapisz wersję roboczą** po zakończeniu przeglądania przykładu planu.

## <a name="publish-the-sample-copy"></a>Kopiowanie przykładowych publikowania

Utworzono kopię przykładowej planu w danym środowisku. Jest tworzony w **projekt** tryb i musi być **opublikowano** przed mogą zostać przypisane i wdrożony. Kopię przykładowych planu można dostosować do Twojego środowiska i potrzeb, niejedna modyfikacji może odbiegać od jej standardowej.

1. Wybierz **wszystkich usług** Wyszukaj i wybierz pozycję **zasad** w okienku po lewej stronie. Na **zasad** wybierz opcję **plany**.

1. Wybierz **planu definicje** strony po lewej stronie. Użyj filtrów, aby znaleźć kopii przykładu planu, a następnie wybierz ją.

1. Wybierz **publikowania planu** w górnej części strony. W nowej strony po prawej stronie zapewniają **wersji** dla tej kopii przykładu planu. Ta właściwość jest przydatne w przypadku, jeśli wprowadzisz zmiany w dalszej części. Podaj **zmienić uwagi** takie jak "pierwsza wersja opublikowany z próbki planu UK-OFFICIAL lub NHS Wielkiej Brytanii." Następnie wybierz pozycję **Publikuj** w dolnej części strony.

## <a name="assign-the-sample-copy"></a>Przypisania kopiowania próbki

Gdy kopii przykładu planu została pomyślnie **opublikowano**, może ona zostać przypisana do subskrypcji w obrębie grupy zarządzania został zapisany w. Ten krok jest, gdzie parametry są przekazywane do unikatowość każdego wdrożenia kopii przykładu planu.

1. Wybierz **wszystkich usług** Wyszukaj i wybierz pozycję **zasad** w okienku po lewej stronie. Na **zasad** wybierz opcję **plany**.

1. Wybierz **planu definicje** strony po lewej stronie. Użyj filtrów, aby znaleźć kopii przykładu planu, a następnie wybierz ją.

1. Wybierz **planu Przypisz** w górnej części strony definicji planu.

1. Podaj wartości parametrów dla przypisania planu:

   - Podstawy

     - **Subskrypcje**: Wybierz co najmniej jednej z subskrypcji, które znajdują się w grupie zarządzania należy zapisać swoją kopię przykładowy plan do. Jeśli wybierzesz więcej niż jedną subskrypcję, przypisania zostaną utworzone dla każdego przy użyciu podanych parametrów.
     - **Nazwa przypisania**: Nazwa jest wstępnie wypełniona automatycznie na podstawie nazwy planu.
       Zmień stosownie do potrzeb, lub pozostawić.
     - **Lokalizacja**: Wybierz region dla tożsamości zarządzanej, które zostały utworzone w. Usługa Azure Blueprint używa tej tożsamości zarządzanej do wdrażania wszystkich artefaktów w przypisanej strategii. Aby dowiedzieć się więcej, zobacz [Tożsamości zarządzane dla zasobów platformy Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Wersja definicji planu**: Wybierz **opublikowano** wersji kopii przykładu planu.

   - Przypisanie blokady

     Wybierz opcję blokady planu, ustawienie dla danego środowiska. Aby uzyskać więcej informacji, zobacz [blokowanie zasobów strategii](../../concepts/resource-locking.md).

   - Tożsamość zarządzana

     Pozostaw wartość domyślną _przypisanej w systemie_ zarządzane opcja tożsamości.

   - Parametry artefaktu

     Parametry zdefiniowane w tej sekcji dotyczą artefaktu w ramach której jest zdefiniowany. Te parametry są [parametrów dynamicznych](../../concepts/parameters.md#dynamic-parameters) ponieważ są one definiowane podczas przypisywania planu. Aby uzyskać pełną listę lub artefaktu parametrów i ich opisy, zobacz [tabeli Parametry artefaktu](#artifact-parameters-table).

1. Po wprowadzeniu wszystkich parametrów, wybierz **przypisać** w dolnej części strony. Przypisanie planu jest tworzony i rozpocznie się wdrażanie artefaktów. Wdrożenie potrwa około godziny. Aby sprawdzić stan wdrożenia, otwórz przypisanie planu.

> [!WARNING]
> Usługa Azure schematy i przykłady wbudowanych planu są **bezpłatna**. Zasoby platformy Azure są [rozliczana według produktu](https://azure.microsoft.com/pricing/). Użyj [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/) można oszacować koszt zasobów wdrożonych w tym przykładzie planu.

## <a name="artifact-parameters-table"></a>Artefakt parametrów tabeli

Poniższa tabela zawiera listę planu artefaktu parametry:

Nazwa artefaktu|Typ artefaktu|Nazwa parametru|Opis|
|-|-|-|-|
|Planu inicjatywy oficjalne UK lub NHS Zjednoczone Królestwo|Przypisywanie zasad |Typy zasobów, aby przeprowadzić inspekcję dzienników diagnostycznych (zasad: Planu inicjatywy oficjalne UK lub NHS Zjednoczone Królestwo) |Lista typów zasobów do inspekcji, jeśli ustawienie dzienników diagnostycznych jest włączone.  Dopuszczalne wartości, zobacz [obsługiwane usługi, schematów i kategorie dla dzienników diagnostycznych usługi Azure](../../../../azure-monitor/platform/diagnostic-logs-schema.md). |
|[Wersja zapoznawcza]\: Wdrażanie programu Log Analytics Agent na maszynach wirtualnych systemu Linux |Przypisywanie zasad |Opcjonalnie: Lista obrazów maszyn wirtualnych, obsługiwanych systemów operacyjnych Linux, można dodać do zakresu (zasad: [wersja zapoznawcza]: Wdrażanie programu Log Analytics Agent na maszynach wirtualnych systemu Linux) |(Opcjonalnie) Wartość domyślna to _Brak_. Aby uzyskać więcej informacji, zobacz [Utwórz obszar roboczy usługi Log Analytics w witrynie Azure portal](../../../../azure-monitor/learn/quick-create-workspace.md). |
|[Wersja zapoznawcza]\: Wdrażanie programu Log Analytics Agent for Windows VMs |Przypisywanie zasad |Opcjonalnie: Lista obrazów maszyn wirtualnych, obsługiwanych systemów operacyjnych Windows, aby dodać do zakresu (zasad: [wersja zapoznawcza]: Wdrażanie programu Log Analytics Agent dla maszyn wirtualnych Windows) |(Opcjonalnie) Wartość domyślna to _Brak_. Aby uzyskać więcej informacji, zobacz [Utwórz obszar roboczy usługi Log Analytics w witrynie Azure portal](../../../../azure-monitor/learn/quick-create-workspace.md). |

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy przejrzeniu kroki umożliwiające wdrożenie oficjalne UK i przykłady planu NHS Zjednoczone Królestwo, odwiedź następujące artykuły, aby dowiedzieć się o przegląd i mapowanie kontrolek:

> [!div class="nextstepaction"]
> [Schematy oficjalne UK i UK NHS - Przegląd](./index.md)
> [oficjalne UK i schematy UK NHS - kontrolować mapowania](./control-mapping.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).
