---
title: Weryfikacja interaktywnych funkcji testowania w usługi Azure Stack weryfikacji jako usługa | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć testy weryfikacji interaktywnych funkcji dla usługi Azure Stack z weryfikacją jako usługa.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: d3db8ea8639f73f3522ddaa358195e7c9ef2f9a9
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2019
ms.locfileid: "57766006"
---
# <a name="interactive-feature-verification-testing"></a>Testowanie weryfikacyjne interaktywnych funkcji  

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Struktura testowania weryfikacji interaktywnych funkcji służy do żądania testów w systemie. W przypadku żądania testu, firma Microsoft wykorzystuje platformę przygotować testy, które wymagają ręcznej czynności interaktywne. Firma Microsoft mogła używać platformę, aby połączyć ze sobą kilka autonomicznego zautomatyzowane testy.

W tym artykule opisano prosty scenariusz ręczne. Test sprawdza, czy zamiana dysku w usłudze Azure Stack. Struktura zbiera dzienniki diagnostyczne na każdym kroku. Możesz debugować błędy, jak je znaleźć. Struktura również pozwala na udostępnianie dzienników generowanych przez inne narzędzia i procesy i pozwala na dostarczenie informacji zwrotnych od scenariusza.

> [!Important]  
> W tym artykule odwołuje się do czynności do wykonania identyfikator dysku. Jest to po prostu demonstracyjnych, ponieważ żadnych wyników, zebranych z przepływu pracy przebiegu testu nie mogą być używane do nowego rozwiązania weryfikacji.

## <a name="overview-of-interactive-testing"></a>Omówienie interaktywne testowania

Test wymiana dysku jest typowym scenariuszem. W tym przykładzie testu ma pięć kroków:

1. Utwórz nową **przebiegu testu** przepływu pracy.
2. Wybierz **dysku Test identyfikacyjny**.
3. Wykonaj krok wykonywany ręcznie po wyświetleniu monitu.
4. Sprawdź wynik tego scenariusza.
5. Wynik testu można wysyłać do firmy Microsoft.

## <a name="create-a-new-test-pass"></a>Utwórz nowy przebieg testu

Jeśli nie masz istniejącego testu przekazać dostępności, postępuj zgodnie ze wskazówkami dotyczącymi [planowania testu](azure-stack-vaas-schedule-test-pass.md).

## <a name="schedule-the-test"></a>Planowanie testów

1. Wybierz **dysku Test identyfikacyjny**.

    > [!Note]  
    > Powoduje zwiększenie wersją testu, ponieważ wprowadzono ulepszenia do zabezpieczeń testu. Najwyższa wersja zawsze należy używać, chyba że wskazuje firmy Microsoft, w przeciwnym razie.

    ![Tekst alternatywny](media/azure-stack-vaas-interactive-feature-verification/image4.png)

1. Podaj nazwę użytkownika administratora domeny i hasło, wybierając **Edytuj**.

1. Wybierz odpowiedni test wykonywania agenta/Menedżer DVM można uruchomić test na.

    ![Tekst alternatywny](media/azure-stack-vaas-interactive-feature-verification/image5.png)

1. Wybierz **przesyłania** aby rozpocząć test.

    ![Tekst alternatywny](media/azure-stack-vaas-interactive-feature-verification/image6.png)

1. Dostęp do interfejsu użytkownika dla testu interakcyjne z agenta wybrane w poprzednim kroku.

    ![Tekst alternatywny](media/azure-stack-vaas-interactive-feature-verification/image8.png)

1. Postępuj zgodnie z **dokumentacji** i **weryfikacji** łącza, aby przejrzeć instrukcje od firmy Microsoft dotyczące realizacji tego scenariusza.

    ![Tekst alternatywny](media/azure-stack-vaas-interactive-feature-verification/image9.png)

1. Wybierz opcję **Dalej**.

    ![Tekst alternatywny](media/azure-stack-vaas-interactive-feature-verification/image10.png)

1. Postępuj zgodnie z instrukcjami, aby uruchomić skrypt precheck.

    ![Tekst alternatywny](media/azure-stack-vaas-interactive-feature-verification/image11.png)

1. Po pomyślnym zakończeniu precheck skrypt uruchamiania ręcznego scenariusza (wymiana dysku) zgodnie **dokumentacji** i **weryfikacji** łączy z **informacji**kartę.

    ![Tekst alternatywny](media/azure-stack-vaas-interactive-feature-verification/image12.png)

    > [!Important]  
    > Nie zamykaj okna dialogowego, podczas wykonywania ręcznych scenariusza.

1. Po zakończeniu wykonywania ręcznych scenariusz, wykonaj instrukcje do uruchomienia skryptu Sprawdź wpis.

    ![Tekst alternatywny](media/azure-stack-vaas-interactive-feature-verification/image13.png)

1. Po pomyślnym zakończeniu ręcznego scenariusza (wymiana dysku) wybierz **dalej**.

    ![Tekst alternatywny](media/azure-stack-vaas-interactive-feature-verification/image14.png)

    > [!Important]  
    > Jeśli zamkniesz okno, test zostanie zatrzymane, zanim to się robi.

1. Przekazać opinię, aby uzyskać wyniki testu. Odpowiedzi na następujące pytania pomogą firmie Microsoft, oceny jakości szybkości i wydania Powodzenie scenariusza.

    ![Tekst alternatywny](media/azure-stack-vaas-interactive-feature-verification/image15.png)

1. Dołącz wszystkie pliki dziennika, który chcesz przesłać do firmy Microsoft.

    ![Tekst alternatywny](media/azure-stack-vaas-interactive-feature-verification/image16.png)

1. Zaakceptuj Umowę licencyjną przesyłania opinii.

1. Wybierz **przesyłania** wysyłać wyniki do firmy Microsoft.

## <a name="next-steps"></a>Kolejne kroki

- [Monitorowanie i zarządzanie testami w portalu VaaS](azure-stack-vaas-monitor-test.md)
