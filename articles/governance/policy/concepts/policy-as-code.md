---
title: Projektowanie przepływów pracy typu zasady jako kod
description: Dowiedz się, jak projektować przepływy pracy, aby wdrożyć definicje zasad platformy Azure jako kod i automatycznie zweryfikować zasoby.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 0914ba6510c9d2ef87d3f83417f97340d42c8bce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74267276"
---
# <a name="design-policy-as-code-workflows"></a>Projektowanie przepływów pracy typu zasady jako kod

W miarę postępów w podróży z cloud governance, należy przejść od ręcznego zarządzania każdą definicją zasad w witrynie Azure portal lub za pośrednictwem różnych zestawów SDK do czegoś łatwiejszego w zarządzaniu i powtarzalne w skali przedsiębiorstwa. Dwa dominujące podejścia do zarządzania systemami na dużą skalę w chmurze to:

- Infrastruktura jako kod: Praktyka traktowania zawartości, która definiuje środowiska, wszystko od szablonów Usługi Resource Manager do definicji zasad platformy Azure do planów platformy Azure, jako kod źródłowy.
- DevOps: Związek ludzi, procesów i produktów, aby umożliwić ciągłe dostarczanie wartości naszym użytkownikom końcowym.

Polityka jako Kodeks jest połączeniem tych pomysłów. Zasadniczo zachowaj definicje zasad w kontroli źródła i za każdym razem, gdy zostanie wniesiena zmiana, przetestuj i sprawdź poprawność tej zmiany. Jednak nie powinien to być zakres zaangażowania zasad z infrastruktury jako kod lub DevOps.

Krok sprawdzania poprawności powinien być również składnikiem innych ciągłej integracji lub ciągłego wdrażania przepływów pracy. Przykłady obejmują wdrażanie środowiska aplikacji lub infrastruktury wirtualnej. Dzięki temu, że usługa Azure Policy jest wczesnym składnikiem procesu kompilacji i wdrażania, zespoły aplikacji i operacji odnajdują, czy ich zmiany nie są związane z skargą, na długo przed tym, zanim będzie za późno i podejmują próbę wdrożenia w produkcji.

## <a name="workflow-overview"></a>Omówienie przepływu pracy

Zalecany ogólny przepływ pracy zasad jako kod wygląda następująco:

![Omówienie przepływu pracy zasady jako kodu](../media/policy-as-code/policy-as-code-workflow.png)

### <a name="create-and-update-policy-definitions"></a>Tworzenie i aktualizowanie definicji zasad

Definicje zasad są tworzone przy użyciu JSON i przechowywane w kontroli źródła. Każda zasada ma własny zestaw plików, takich jak parametry, reguły i parametry środowiska, które powinny być przechowywane w tym samym folderze. Poniższa struktura jest zalecanym sposobem utrzymania definicji zasad w kontroli źródła.

```text
.
|
|- policies/  ________________________ # Root folder for policies
|  |- policy1/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
|  |- policy2/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
```

Po dodaniu nowej zasady lub zaktualizowaniu istniejącej, przepływ pracy powinien automatycznie zaktualizować definicję zasad na platformie Azure. Testowanie nowej lub zaktualizowanej definicji zasad odbywa się w późniejszym kroku.

### <a name="create-and-update-initiative-definitions"></a>Tworzenie i aktualizowanie definicji inicjatywy

Podobnie inicjatywy mają własny plik JSON i powiązane pliki, które powinny być przechowywane w tym samym folderze. Definicja inicjatywy wymaga, aby definicja zasad już istniała, więc nie można jej utworzyć ani zaktualizować, dopóki źródło zasad nie zostanie zaktualizowane w formancie źródła, a następnie zaktualizowane na platformie Azure. Poniższa struktura jest zalecanym sposobem utrzymania definicji inicjatywy w kontroli źródła:

```text
.
|
|- initiatives/ ______________________ # Root folder for initiatives
|  |- init1/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
|  |- init2/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
```

Podobnie jak definicje zasad podczas dodawania lub aktualizowania istniejącej inicjatywy, przepływ pracy powinien automatycznie aktualizować definicję inicjatywy na platformie Azure. Testowanie nowej lub zaktualizowanej definicji inicjatywy odbywa się w późniejszym kroku.

### <a name="test-and-validate-the-updated-definition"></a>Testowanie i sprawdzanie poprawności zaktualizowanej definicji

Po automatyzacji podjęła nowo utworzone lub zaktualizowane definicje zasad lub inicjatywy i dokonał aktualizacji do obiektu na platformie Azure, nadszedł czas, aby przetestować zmiany, które zostały wprowadzone. Albo zasady lub inicjatywy, które są częścią powinny być następnie przypisane do zasobów w środowisku najdalej od produkcji. To środowisko jest zazwyczaj _Dev_.

Przypisanie należy użyć [enforcementMode](./assignment-structure.md#enforcement-mode) _wyłączone,_ tak aby tworzenie zasobów i aktualizacje nie są blokowane, ale istniejące zasoby są nadal kontrolowane pod kątem zgodności z definicją zaktualizowanych zasad. Nawet w przypadku enforcementMode zaleca się, że zakres przydziału jest albo grupy zasobów lub subskrypcji, która jest specjalnie używana do sprawdzania poprawności zasad.

> [!NOTE]
> Chociaż tryb wymuszania jest pomocny, nie zastępuje dokładnie testowania definicji zasad w różnych warunkach. Definicja zasad powinna być `PUT` `PATCH` testowana za pomocą wywołań interfejsu API REST, zgodnych i niezgodnych zasobów oraz przypadków brzegowych, takich jak właściwość, w których brakuje właściwości w zasobie.

Po wdrożeniu przypisania użyj zestawu SDK zasad, aby [uzyskać dane zgodności](../how-to/get-compliance-data.md) dla nowego przypisania. Środowisko używane do testowania zasad i przypisań powinno mieć zarówno zgodne, jak i niezgodne zasoby. Podobnie jak dobry test jednostkowy dla kodu, chcesz przetestować, że zasoby są zgodnie z oczekiwaniami i że nie masz również fałszywych alarmów lub fałszywych negatywów. Jeśli testujesz i sprawdzasz poprawność tylko dla tego, czego oczekujesz, może wystąpić nieoczekiwany i niezidentyfikowany wpływ zasad. Aby uzyskać więcej informacji, zobacz [Ocenianie wpływu nowej zasady platformy Azure](./evaluate-impact.md).

### <a name="enable-remediation-tasks"></a>Włączanie zadań korygowania

Jeśli sprawdzanie poprawności przypisania spełnia oczekiwania, następnym krokiem jest sprawdzenie poprawności korygowania.
Zasady, które używają [deployIfNotExists](./effects.md#deployifnotexists) lub [modyfikacji](./effects.md#modify) mogą zostać przekształcone w zadanie korygowania i popraw zasoby ze stanu niezgodnego.

Pierwszym krokiem do wykonania tej zasady jest przyznanie przypisania zasad przypisaniem roli zdefiniowanym w definicji zasad. To przypisanie roli daje przypisania zasad zarządzanych tożsamości wystarczających praw do wprowadzania niezbędnych zmian, aby zasób zgodny.

Gdy przypisanie zasad ma odpowiednie prawa, użyj zestawu SDK zasad, aby wyzwolić zadanie korygowania względem zestawu zasobów, o których wiadomo, że są niezgodne. Przed przystąpieniem do tych zadań należy wykonać trzy testy:

- Sprawdzanie, czy zadanie korygowania zostało pomyślnie ukończone
- Uruchom ocenę zasad, aby zobaczyć, że wyniki zgodności zasad są aktualizowane zgodnie z oczekiwaniami
- Uruchom test jednostki środowiska względem zasobów bezpośrednio, aby sprawdzić poprawność ich właściwości uległy zmianie

Testowanie zarówno zaktualizowanych wyników oceny zasad, jak i środowiska bezpośrednio potwierdza, że zadania korygujące zmieniły to, czego oczekiwano i że definicja zasad spowodowała zmianę zgodności zgodnie z oczekiwaniami.

### <a name="update-to-enforced-assignments"></a>Aktualizacja do wymuszonych przydziałów

Po zakończeniu wszystkich bramek sprawdzania poprawności zaktualizuj przypisanie, aby użyć **enforcementMode** _włączonego_. Ta zmiana powinna być początkowo dokonana w tym samym środowisku, z dala od produkcji. Po zatwierdzeniu tego środowiska jako działające zgodnie z oczekiwaniami, zmiana powinna być następnie zakres obejmują następne środowisko i tak dalej, dopóki zasady są wdrażane w zasobach produkcyjnych.

## <a name="process-integrated-evaluations"></a>Procesy zintegrowanych ocen

Ogólny przepływ pracy dla zasad jako kod jest do opracowywania i wdrażania zasad i inicjatyw w środowisku na dużą skalę. Jednak ocena zasad powinna być częścią procesu wdrażania dla każdego przepływu pracy, który wdraża lub tworzy zasoby na platformie Azure, takich jak wdrażanie aplikacji lub uruchamianie szablonów Menedżera zasobów w celu utworzenia infrastruktury.

W takich przypadkach po wdrożeniu aplikacji lub infrastruktury odbywa się do subskrypcji testowej lub grupy zasobów, oceny zasad należy wykonać dla tego zakresu sprawdzania sprawdzania poprawności wszystkich istniejących zasad i inicjatyw. Chociaż mogą być skonfigurowane jako **enforcementMode** _wyłączone_ w takim środowisku, warto wiedzieć wcześnie, jeśli wdrożenie aplikacji lub infrastruktury jest z naruszeniem definicji zasad wcześnie. Ta ocena zasad powinna zatem być krokiem w tych przepływach pracy i nie wdrożenia, które tworzą niezgodne zasoby.

## <a name="review"></a>Przegląd

W tym artykule opisano ogólny przepływ pracy dla zasad jako kod, a także gdzie ocena zasad powinna być częścią innych przepływów pracy wdrażania. Ten przepływ pracy może być używany w dowolnym środowisku, które obsługuje kroki skryptowe i automatyzacji na podstawie wyzwalaczy.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [strukturze definicji zasad](./definition-structure.md).
- Dowiedz się więcej o [strukturze przypisywania zasad](./assignment-structure.md).
- Dowiedz się, jak [programowo tworzyć zasady](../how-to/programmatically-create.md).
- Dowiedz się, jak [uzyskać dane dotyczące zgodności](../how-to/get-compliance-data.md).
- Dowiedz się, jak [korygować niezgodne zasoby](../how-to/remediate-resources.md).
- Sprawdź, czym jest grupa zarządzania, [organizuj swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/overview.md).