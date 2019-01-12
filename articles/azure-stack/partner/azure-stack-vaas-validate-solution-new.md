---
title: Weryfikowanie nowych rozwiązań usługi Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak sprawdzanie poprawności nowego rozwiązania usługi Azure Stack z weryfikacją jako usługa.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 12/20/2018
ms.author: mabrigg
ms.reviewer: johnhas
ROBOTS: NOINDEX
ms.openlocfilehash: c8a723137761c12ab335af79dfffb9e124348eac
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2019
ms.locfileid: "54246606"
---
# <a name="validate-a-new-azure-stack-solution"></a>Weryfikowanie nowych rozwiązań usługi Azure Stack

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Dowiedz się, jak używać **sprawdzania poprawności rozwiązań** przepływu pracy, aby przeprowadzić certyfikację nowych rozwiązań usługi Azure Stack.

Rozwiązanie usługi Azure Stack jest sprzętu zestawienie komponentów (BoM), który ma zostać wspólnie uzgodnione między firmami Microsoft i partnerów po spełniające wymagania certyfikacji logo systemu Windows Server. Rozwiązanie musi recertified, gdy nastąpiła zmiana na sprzęt BoM. W razie dalszych pytań o tym, kiedy ponownie certyfikować rozwiązania, skontaktuj się z zespołem pod adresem [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com).

Aby przeprowadzić certyfikację rozwiązania, należy uruchomić przepływ pracy weryfikacji rozwiązania dwukrotnie. Uruchomić go jeden raz dla *minimalny zestaw* obsługiwana konfiguracja. Drugi czas uruchamiania *maksymalnie* obsługiwana konfiguracja. Certyfikacja potwierdzająca rozwiązania firmy Microsoft, jeśli obie konfiguracje pomyślnie przejść wszystkie testy.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="create-a-solution-validation-workflow"></a>Tworzenie przepływu pracy weryfikacji rozwiązania

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

3. Wybierz **Start** na **walidacji rozwiązania** kafelka.

    ![Rozwiązanie walidacji przepływu pracy kafelka](media/tile_validation-solution.png)

4. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

5. Wybierz **konfiguracji rozwiązania**.
    - **Minimalna**: rozwiązanie jest skonfigurowany przy użyciu obsługiwanych minimalna liczba węzłów.
    - **Maksymalna**: rozwiązanie jest skonfigurowany przy użyciu obsługiwanych maksymalną liczbę węzłów.
6. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

    ![Informacje o rozwiązaniu sprawdzania poprawności](media/workflow_validation-solution_info.png)

7. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > Nie można modyfikować parametrów środowiska po utworzeniu przepływu pracy.

8. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
9. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    Nastąpi przekierowanie do strony Podsumowanie testów.

## <a name="run-solution-validation-tests"></a>Uruchom testy weryfikacyjne rozwiązania

W **rozwiązanie weryfikacji sprawdza Podsumowanie** strony, zobaczysz listę testów, wymagane do zakończenia weryfikacji.

W przepływach pracy sprawdzania poprawności **planowania** przetestujesz typowe parametry przepływu pracy na poziomie, które zostały określone podczas tworzenia przepływu pracy (zobacz [wspólnych parametrów przepływów pracy dla usługi Azure Stack weryfikacji jako usługa](azure-stack-vaas-parameters.md)). Jeśli dowolna z wartości parametrów testu stają się nieprawidłowe, użytkownik musi resupply je, zgodnie z instrukcją w [zmodyfikować parametry przepływu pracy](azure-stack-vaas-monitor-test.md#change-workflow-parameters).

> [!NOTE]
> Planowanie testów sprawdzania poprawności w istniejącym wystąpieniu utworzy nowe wystąpienie zamiast w starym wystąpieniu w portalu. Dzienniki starej instalacji zostaną zachowane, ale nie są dostępne z poziomu portalu.  
Po pomyślnym zakończeniu testu **harmonogram** akcja zostanie wyłączona.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](includes/azure-stack-vaas-workflow-step_select-agent.md)]

2. Wybierz następujące testy:
    - Aparat symulacji w chmurze
    - Obliczenia operacyjnej zestaw SDK
    - Test identyfikator dysku
    - Pakiet operacyjnej zestaw SDK rozszerzenia usługi KeyVault
    - Operacyjnej zestaw SDK magazynu kluczy
    - Pakiet operacyjnej zestaw SDK sieci
    - Pakiet operacyjnej zestaw SDK konta magazynu

3. Wybierz **harmonogram** z menu kontekstowego, aby otworzyć wiersz do planowania wystąpieniem testu.

4. Przejrzyj parametry testu, a następnie wybierz pozycję **przesyłania** do zaplanowania testów do wykonania.

![Test weryfikacji rozwiązania harmonogramu](media/workflow_validation-solution_schedule-test.png)

## <a name="next-steps"></a>Kolejne kroki

- [Monitorowanie i zarządzanie testami w portalu VaaS](azure-stack-vaas-monitor-test.md)