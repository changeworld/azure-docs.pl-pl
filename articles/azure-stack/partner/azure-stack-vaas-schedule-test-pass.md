---
title: Użyj sprawdzania poprawności jako usługa dla usługi Azure Stack portal do zaplanowania Twojego pierwszego testu | Dokumentacja firmy Microsoft
description: Aby zaplanować pierwszy test, należy użyć weryfikacji jako usługa dla portalu Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: How to
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 0e9ad89e504ce1cb86daad48bec9ffe9423e2cf1
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2019
ms.locfileid: "54319549"
---
# <a name="scheduling-a-test"></a>Planowanie testów

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Planowanie testów weryfikacji jako portal usługi (VaaS), dla Twojego rozwiązania usługi Azure Stack. Rozwiązanie VaaS reprezentuje rozwiązanie usługi Azure Stack przy użyciu konkretnego sprzętu zestawienie komponentów (BoM). Można zaplanować test, aby sprawdzić, czy sprzętu można uruchomić usługi Azure Stack.

Aby sprawdzić swoje rozwiązanie, należy utworzyć przepływ pracy dla testów. Przepływ pracy VaaS działa w kontekście rozwiązania VaaS. Reprezentuje zbiór zestawów testów, które przetestuj funkcjonalność wdrożenia usługi Azure Stack na danym urządzeniu. Dodaj parametry środowiska swoje rozwiązanie i wybierz jeden lub więcej testów do uruchomienia w rozwiązaniu.

Podczas przepływu pracy przebieg testu może służyć do uruchamiania każdego testu, dostarczone przez VaaS, w tym testy z przepływów pracy sprawdzania poprawności, wyników z przebiegu testu przepływu pracy nie są uważane za *oficjalne*. Uzyskać informacji o przepływach pracy oficjalne weryfikacji, zobacz [przepływy pracy](azure-stack-vaas-key-concepts.md#workflows).

## <a name="prerequisites"></a>Wymagania wstępne

Przed wykonaniem tego przewodnika Szybki Start, powinno zostać zakończone następujące elementy:

- [Konfigurowanie walidacji jako zasoby usługi](azure-stack-vaas-set-up-resources.md)
- [Wdrażanie lokalnego agenta](azure-stack-vaas-local-agent.md) (wymagane)
- [Sprawdzanie poprawności jako kluczowe pojęcia dotyczące usługi](azure-stack-vaas-key-concepts.md) (wymagane)

## <a name="start-a-workflow"></a>Uruchamianie przepływu pracy

![Zaloguj się do portalu VaaS](media/vaas_portalsignin.png)

Zaloguj się do portalu, wybierz opcję lub tworzenia rozwiązania, a następnie wybierz rozwiązanie.

1. Zaloguj się do [VaaS portal](https://azurestackvalidation.com).
2. Wpisz nazwę istniejącego rozwiązania, lub wybierz **nowe rozwiązanie** do tworzenia nowego rozwiązania. Aby uzyskać instrukcje, zobacz [tworzenie rozwiązania w portalu VaaS](azure-stack-vaas-key-concepts.md#create-a-solution-in-the-vaas-portal).
3. Wybierz **Start** na **przebiegów testowych** kafelka.

## <a name="specify-parameters"></a>Określ parametry

![Tekst alternatywny](media/vaas_test_pass_parameters.png)

Definiowanie przepływu pracy dla danego rozwiązania. Przepływ pracy ma kroki procesu, który umożliwia przetestowanie rozwiązania.

1. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]
2. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]
3. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]
4. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
5. Wybierz **dalej** aby wybrać testy do zaplanowania.

## <a name="select-tests-to-run"></a>Wybrać testy do uruchomienia

Wybierz testy, które chcesz uruchomić w przepływie pracy.

1. Wybierz testy, które chcesz uruchomić w przepływie pracy.

    Jeśli chcesz zastąpić typowych parametrów (oznacza to, że parametry podane w poprzedniej sekcji) dla każdego testu, wybierz **Edytuj** łącze obok, aby określić nowe wartości.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](includes/azure-stack-vaas-workflow-step_select-agent.md)]
1. Wybierz **dalej** Aby zapoznać się z przepływu pracy.

## <a name="review-and-submit"></a>Przejrzyj i zatwierdź

Przejrzyj, Utwórz, a następnie zaplanować przepływu pracy.

1. Przejrzyj wyświetlane informacje.

    Usług tworzy przepływ pracy przy użyciu podanych informacji i wybrane testy zostaną zaplanowane.

    Jeśli niczego wydaje się nieprawidłowy, należy użyć **Wstecz** przycisków, aby przejść do wcześniejszej sekcji.

1. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]

## <a name="next-steps"></a>Kolejne kroki

- [Monitorowanie i zarządzanie testami w portalu VaaS](azure-stack-vaas-monitor-test.md)
