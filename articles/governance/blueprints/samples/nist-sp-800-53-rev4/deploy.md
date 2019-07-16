---
title: Przykładowe - SP NIST 800-53 R4 planu — kroki wdrażania
description: Wdróż kroki NIST SP 800-53 R4 planu próbki.
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 206763e2d17f4ad711ff5fd897f1429814e61837
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "68228905"
---
# <a name="deploy-the-nist-sp-800-53-r4-blueprint-sample"></a>Wdrażanie NIST SP 800-53 R4 planu próbki

Aby wdrożyć SP NIST schematy Azure przykładowy plan R4 800-53, należy podjąć następujące kroki:

> [!div class="checklist"]
> - Tworzenie nowego planu z próbki
> - Oznacz kopii przykładu jako **opublikowano**
> - Przypisz swoją kopię planu do istniejącej subskrypcji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

## <a name="create-blueprint-from-sample"></a>Utwórz plan z próbki

Najpierw należy zaimplementować przykładowy plan przez utworzenie nowego planu w środowisku przy użyciu przykładu jako początkowego.

1. Wybierz **wszystkich usług** Wyszukaj i wybierz pozycję **zasad** w okienku po lewej stronie. Na **zasad** wybierz opcję **plany**.

1. Z **wprowadzenie** strony po lewej stronie, wybierz opcję **Utwórz** przycisku w obszarze _Tworzenie planu_.

1. Znajdź **R4 SP NIST 800-53** przykładowy plan w ramach _inne przykłady_ i wybierz **korzystać z tej próbki**.

1. Wprowadź _podstawy_ próbki planu:

   - **Nazwa planu**: Podaj nazwę dla tej kopii przykładu NIST SP 800-53 R4 planu.
   - **Lokalizacja definicji**: Użyj wielokropek i wybierz grupę zarządzania, aby zapisać kopię próbki.

1. Wybierz _artefaktów_ kartę w górnej części strony lub **dalej: Artefakty** w dolnej części strony.

1. Przejrzyj listę artefaktów, które tworzą przykładowy plan. Wiele artefaktów ma parametry, które zdefiniujemy później. Wybierz **Zapisz wersję roboczą** po zakończeniu przeglądania przykładu planu.

## <a name="publish-the-sample-copy"></a>Kopiowanie przykładowych publikowania

Utworzono kopię przykładowej planu w danym środowisku. Jest tworzony w **projekt** tryb i musi być **opublikowano** przed mogą zostać przypisane i wdrożony. Kopię przykładowych planu można dostosować do Twojego środowiska i potrzeb, niejedna modyfikacji może Przenieś go od spełnia wymagania programu NIST SP 800-53 kontrolki.

1. Wybierz **wszystkich usług** Wyszukaj i wybierz pozycję **zasad** w okienku po lewej stronie. Na **zasad** wybierz opcję **plany**.

1. Wybierz **planu definicje** strony po lewej stronie. Użyj filtrów, aby znaleźć kopii przykładu planu, a następnie wybierz ją.

1. Wybierz **publikowania planu** w górnej części strony. W nowej strony po prawej stronie zapewniają **wersji** dla tej kopii przykładu planu. Ta właściwość jest przydatne w przypadku, jeśli wprowadzisz zmiany w dalszej części. Podaj **zmienić uwagi** takie jak "pierwsza wersja publikowane z poziomu NIST SP 800-53 R4 planu przykład." Następnie wybierz pozycję **Publikuj** w dolnej części strony.

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
|\[Podgląd\]: Inspekcja NIST SP 800-53 R4 kontrolek i wdrażanie określonych rozszerzeń maszyn wirtualnych, aby spełnić wymagania dotyczące inspekcji|Przypisywanie zasad|Identyfikator obszaru roboczego analizy dzienników, powinny mieć skonfigurowane maszyny wirtualne|Jest to identyfikator (GUID) maszyny wirtualne należy skonfigurować dla obszaru roboczego usługi Log Analytics.|
|\[Podgląd\]: Inspekcja NIST SP 800-53 R4 kontrolek i wdrażanie określonych rozszerzeń maszyn wirtualnych, aby spełnić wymagania dotyczące inspekcji|Przypisywanie zasad|Lista typów zasobów, które mają włączone dzienniki diagnostyczne|Lista typów zasobów do inspekcji, jeśli nie włączono ustawienie dzienników diagnostycznych. Dopuszczalne wartości, można znaleźć w folderze [schematy dla dzienników diagnostycznych usługi Azure Monitor](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type).|
|\[Podgląd\]: Inspekcja NIST SP 800-53 R4 kontrolek i wdrażanie określonych rozszerzeń maszyn wirtualnych, aby spełnić wymagania dotyczące inspekcji|Przypisywanie zasad|Lista użytkowników, które mają zostać wykluczone z grupy administratorów maszyn wirtualnych Windows|Rozdzielana średnikami lista elementów członkowskich, które powinny być wyłączone w lokalnej grupie Administratorzy. Na przykład: Administrator. myUser1; myUser2|
|\[Podgląd\]: Inspekcja NIST SP 800-53 R4 kontrolek i wdrażanie określonych rozszerzeń maszyn wirtualnych, aby spełnić wymagania dotyczące inspekcji|Przypisywanie zasad|Lista użytkowników, które powinny być uwzględnione w grupie administratorów maszyn wirtualnych Windows|Rozdzielana średnikami lista elementów członkowskich, które powinny znajdować się w lokalnej grupie Administratorzy. Na przykład: Administrator. myUser1; myUser2|
|\[Podgląd\]: Wdrażanie programu Log Analytics Agent for Linux VM Scale Sets (zestawu skalowania maszyn wirtualnych)|Przypisywanie zasad|Obszar roboczy usługi log Analytics dla zestawów skalowania maszyny Wirtualnej systemu Linux (VMSS)|Jeśli ten obszar roboczy jest poza zakresem przypisania należy ręcznie przyznać uprawnienia "Współautor usługi Log Analytics" (lub podobny) do przypisania zasad jednostki identyfikatora.|
|\[Podgląd\]: Wdrażanie programu Log Analytics Agent for Linux VM Scale Sets (zestawu skalowania maszyn wirtualnych)|Przypisywanie zasad|Opcjonalnie: Lista obrazów maszyn wirtualnych, obsługiwanych systemów operacyjnych Linux, można dodać do zakresu|Pusta tablica może służyć do wskazania nie parametry opcjonalne: \[\]|
|\[Podgląd\]: Wdrażanie programu Log Analytics Agent na maszynach wirtualnych systemu Linux|Przypisywanie zasad|Zaloguj się obszaru roboczego analizy dla maszyn wirtualnych z systemem Linux|Jeśli ten obszar roboczy jest poza zakresem przypisania należy ręcznie przyznać uprawnienia "Współautor usługi Log Analytics" (lub podobny) do przypisania zasad jednostki identyfikatora.|
|\[Podgląd\]: Wdrażanie programu Log Analytics Agent na maszynach wirtualnych systemu Linux|Przypisywanie zasad|Opcjonalnie: Lista obrazów maszyn wirtualnych, obsługiwanych systemów operacyjnych Linux, można dodać do zakresu|Pusta tablica może służyć do wskazania nie parametry opcjonalne: \[\]|
|\[Podgląd\]: Wdrażanie programu Log Analytics Agent Windows VM Scale Sets (zestawu skalowania maszyn wirtualnych)|Przypisywanie zasad|Obszar roboczy usługi log Analytics dla zestawów skalowania maszyn wirtualnych Windows (VMSS)|Jeśli ten obszar roboczy jest poza zakresem przypisania należy ręcznie przyznać uprawnienia "Współautor usługi Log Analytics" (lub podobny) do przypisania zasad jednostki identyfikatora.|
|\[Podgląd\]: Wdrażanie programu Log Analytics Agent Windows VM Scale Sets (zestawu skalowania maszyn wirtualnych)|Przypisywanie zasad|Opcjonalnie: Lista obrazów maszyn wirtualnych, obsługiwanych systemów operacyjnych Windows, aby dodać do zakresu|Pusta tablica może służyć do wskazania nie parametry opcjonalne: \[\]|
|\[Podgląd\]: Wdrażanie programu Log Analytics Agent for Windows VMs|Przypisywanie zasad|Zaloguj się obszaru roboczego analizy dla maszyn wirtualnych Windows|Jeśli ten obszar roboczy jest poza zakresem przypisania należy ręcznie przyznać uprawnienia "Współautor usługi Log Analytics" (lub podobny) do przypisania zasad jednostki identyfikatora.|
|\[Podgląd\]: Wdrażanie programu Log Analytics Agent for Windows VMs|Przypisywanie zasad|Opcjonalnie: Lista obrazów maszyn wirtualnych, obsługiwanych systemów operacyjnych Windows, aby dodać do zakresu|Pusta tablica może służyć do wskazania nie parametry opcjonalne: \[\]|
|Wdróż zaawansowaną ochronę przed zagrożeniami dla kont magazynu|Przypisywanie zasad|Efekt|Informacje dotyczące efektów zasad znajduje się w temacie [zrozumieć wpływ zasad platformy Azure](../../../policy/concepts/effects.md)|
|Wdrażanie inspekcji na serwerach SQL|Przypisywanie zasad|Wyrażona w dniach długość okresu przechowywania (0 oznacza nieograniczone przechowywanie)|Liczba dni przechowywania (opcjonalnie; 180 dni, jeśli nie określono tego parametru)|
|Wdrażanie inspekcji na serwerach SQL|Przypisywanie zasad|Nazwa grupy zasobów dla konta magazynu dla inspekcji usługi SQL server|Zdarzenia inspekcji bazy danych zapisów inspekcji, zaloguj się na Twoim koncie usługi Azure Storage (konto magazynu zostanie utworzone w każdym regionie tworzona programu SQL Server, która będzie współdzielona przez wszystkie serwery w danym regionie). Uwaga: Aby zapewnić sprawne działanie inspekcji nie usuwaj ani nie zmienić nazwę grupy zasobów ani kont magazynu.|
|Wdrażanie ustawień diagnostycznych dla sieciowych grup zabezpieczeń|Przypisywanie zasad|Prefiks konta magazynu dla diagnostyki grupy zabezpieczeń sieci|Ten prefiks jest łączona z lokalizacji grupy zabezpieczeń sieci w celu utworzenia nazwy konta magazynu utworzonego.|
|Wdrażanie ustawień diagnostycznych dla sieciowych grup zabezpieczeń|Przypisywanie zasad|Nazwa grupy zasobów dla konta magazynu diagnostyki grupy zabezpieczeń sieci (musi istnieć)|Zostanie utworzone konto magazynu w grupie zasobów. Ta grupa zasobów musi już istnieć.|

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy przejrzeniu kroki umożliwiające wdrożenie NIST SP 800-53 R4 planu próbki, odwiedź następujące artykuły, aby informacje na temat planu i mapowanie kontrolek:

> [!div class="nextstepaction"]
> [SP NIST 800-53 R4 planu — omówienie](./index.md)
> [planu R4 SP NIST 800-53 - mapowanie kontrolek](./control-mapping.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).
