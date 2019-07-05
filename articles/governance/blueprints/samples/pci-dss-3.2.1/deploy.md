---
title: Przykładowe — PCI-DSS v3.2.1 planu — kroki wdrażania
description: Wdróż kroki Payment Card Industry Data Security Standard v3.2.1 próbki planu.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: b4e9435e5f569a076fc5beb6441b9da935b87f3a
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561362"
---
# <a name="deploy-the-pci-dss-v321-blueprint-sample"></a>Wdrażanie przykładowej planu v3.2.1 PCI-DSS

Aby wdrożyć przykładowy plan v3.2.1 plany platformy Azure ze standardami PCI-DSS, należy podjąć następujące kroki:

> [!div class="checklist"]
> - Tworzenie nowego planu z próbki
> - Oznacz kopii przykładu jako **opublikowano**
> - Przypisz swoją kopię planu do istniejącej subskrypcji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

## <a name="create-blueprint-from-sample"></a>Utwórz plan z próbki

Najpierw należy zaimplementować przykładowy plan przez utworzenie nowego planu w środowisku przy użyciu przykładu jako początkowego.

1. Wybierz **wszystkich usług** Wyszukaj i wybierz pozycję **zasad** w okienku po lewej stronie. Na **zasad** wybierz opcję **plany**.

1. Z **wprowadzenie** strony po lewej stronie, wybierz opcję **Utwórz** przycisku w obszarze _Tworzenie planu_.

1. Znajdź **v3.2.1 PCI-DSS** przykładowy plan w ramach _inne przykłady_ i wybierz **korzystać z tej próbki**.

1. Wprowadź _podstawy_ próbki planu:

   - **Nazwa planu**: Podaj nazwę dla tej kopii przykładu planu v3.2.1 PCI-DSS.
   - **Lokalizacja definicji**: Użyj wielokropek i wybierz grupę zarządzania, aby zapisać kopię próbki.

1. Wybierz _artefaktów_ kartę w górnej części strony lub **dalej: Artefakty** w dolnej części strony.

1. Przejrzyj listę artefaktów, które tworzą przykładowy plan. Wiele artefaktów ma parametry, które zdefiniujemy później. Wybierz **Zapisz wersję roboczą** po zakończeniu przeglądania przykładu planu.

## <a name="publish-the-sample-copy"></a>Kopiowanie przykładowych publikowania

Utworzono kopię przykładowej planu w danym środowisku. Jest tworzony w **projekt** tryb i musi być **opublikowano** przed mogą zostać przypisane i wdrożony. Kopię przykładowych planu można dostosować do Twojego środowiska i potrzeb, niejedna modyfikacji może odbiegać od jego standardowa v3.2.1 PCI-DSS.

1. Wybierz **wszystkich usług** Wyszukaj i wybierz pozycję **zasad** w okienku po lewej stronie. Na **zasad** wybierz opcję **plany**.

1. Wybierz **planu definicje** strony po lewej stronie. Użyj filtrów, aby znaleźć kopii przykładu planu, a następnie wybierz ją.

1. Wybierz **publikowania planu** w górnej części strony. W nowej strony po prawej stronie zapewniają **wersji** dla tej kopii przykładu planu. Ta właściwość jest przydatne w przypadku, jeśli wprowadzisz zmiany w dalszej części. Podaj **zmienić uwagi** takie jak "pierwsza wersja opublikowany z próbki planu v3.2.1 PCI-DSS." Następnie wybierz pozycję **Publikuj** w dolnej części strony.

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

|Nazwa artefaktu|Typ artefaktu|Nazwa parametru|Opis|
|-|-|-|-|
|[Wersja zapoznawcza]\: Inspekcja PCI v3.2.1:2018 kontrolek i wdrażanie określonych rozszerzeń maszyn wirtualnych, aby spełnić wymagania dotyczące inspekcji|Przypisanie zasad|Lista typów zasobów | Przeprowadź inspekcję ustawienia diagnostyki dla wybranych typów zasobów. Wartość domyślna to, że wybrane są wszystkie zasoby| 
|Dozwolone lokalizacje|Przypisanie zasad|Lista dozwolonych lokalizacji|Lista dozwolone dla dowolnych zasobów do wdrożenia w lokalizacji centrum danych. Ta lista jest dostosowywane do żądanej lokalizacji platformy Azure na całym świecie. Wybierz lokalizacje, które mają być dozwolone.| 
|Dozwolone lokalizacje dla grup zasobów|Przypisanie zasad |Dozwolonych lokalizacji |Te zasady umożliwiają ograniczenie lokalizacji, w których Twoja organizacja może tworzyć grupy zasobów na platformie. Służy do wymuszania wymagań dotyczących zgodności obszarów geograficznych.| 
|Wdrażanie inspekcji na serwerach SQL|Przypisanie zasad|Liczba dni przechowywania|Rentention danych w dniach. Wartość domyślna to 180, ale PCI wymaga 365.| 
|Wdrażanie inspekcji na serwerach SQL|Przypisanie zasad|Nazwa grupy zasobów dla konta magazynu|Zdarzenia inspekcji bazy danych zapisów inspekcji, zaloguj się na Twoim koncie usługi Azure Storage (konto magazynu zostanie utworzone w każdym regionie tworzona programu SQL Server, która będzie współdzielona przez wszystkie serwery w danym regionie).| 

## <a name="next-steps"></a>Kolejne kroki

Teraz, po przejrzeniu kroki, aby wdrożyć przykładowy plan v3.2.1 PCI DSS, odwiedź następujące artykuły, aby dowiedzieć się o przegląd i mapowanie kontrolek:

> [!div class="nextstepaction"]
> [PCI-DSS v3.2.1 planu — omówienie](./index.md)
> [planu v3.2.1 PCI-DSS — mapowanie kontrolek](./control-mapping.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).
