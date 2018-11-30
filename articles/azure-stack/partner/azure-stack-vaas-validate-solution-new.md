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
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 7949e764baa7a4e20eb988c78817b6b4f0045593
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/27/2018
ms.locfileid: "52333772"
---
# <a name="validate-a-new-azure-stack-solution"></a>Weryfikowanie nowych rozwiązań usługi Azure Stack

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Dowiedz się, jak używać **sprawdzania poprawności rozwiązań** przepływu pracy, aby przeprowadzić certyfikację nowych rozwiązań usługi Azure Stack.

Rozwiązanie usługi Azure Stack jest sprzętu zestawienie komponentów (BoM), który ma zostać wspólnie uzgodnione między firmami Microsoft i partnerów po spełniające wymagania certyfikacji logo systemu Windows Server. Rozwiązanie musi recertified, gdy nastąpiła zmiana na sprzęt BoM. W razie dalszych pytań o tym, kiedy ponownie certyfikować rozwiązania, skontaktuj się z zespołem pod adresem [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com).

Aby przeprowadzić certyfikację rozwiązania, należy uruchomić przepływu pracy weryfikacji rozwiązania dwukrotnie. Uruchomić go jeden raz dla *minimalny zestaw* obsługiwana konfiguracja. Drugi czas uruchamiania *maksymalnie* obsługiwana konfiguracja. Certyfikacja potwierdzająca rozwiązania firmy Microsoft, jeśli obie konfiguracje pomyślnie przejść wszystkie testy.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="create-a-solution-validation-workflow"></a>Tworzenie przepływu pracy weryfikacji rozwiązania

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]
2. Wybierz **Start** na **walidacji rozwiązania** kafelka.

    ![Rozwiązanie walidacji przepływu pracy kafelka](media/tile_validation-solution.png)

3. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]
4. Wybierz **konfiguracji rozwiązania**.
    - **Minimalna**: rozwiązanie jest skonfigurowany przy użyciu obsługiwanych minimalna liczba węzłów.
    - **Maksymalna**: rozwiązanie jest skonfigurowany przy użyciu obsługiwanych maksymalną liczbę węzłów.
5. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

    ![Informacje o rozwiązaniu sprawdzania poprawności](media/workflow_validation-solution_info.png)

6. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > Nie można modyfikować parametrów środowiska po utworzeniu przepływu pracy.

7. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
8. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    Nastąpi przekierowanie do strony Podsumowanie testów.

## <a name="execute-solution-validation-tests"></a>Wykonywanie testów sprawdzania poprawności rozwiązań

W **podsumowania testy sprawdzania poprawności rozwiązań** strony, zobaczysz listę testów, wymagane do zakończenia weryfikacji.

[!INCLUDE [azure-stack-vaas-workflow-validation-section_schedule](includes/azure-stack-vaas-workflow-validation-section_schedule.md)]

![Test weryfikacji rozwiązania harmonogramu](media/workflow_validation-solution_schedule-test.png)

## <a name="next-steps"></a>Kolejne kroki

- [Monitorowanie i zarządzanie testami w portalu VaaS](azure-stack-vaas-monitor-test.md)