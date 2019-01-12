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
ms.date: 1/07/2019
ms.author: mabrigg
ms.reviewer: johnhas
ROBOTS: NOINDEX
ms.openlocfilehash: 5b20e170388aa6e9b73b7c5c26a286ec51b52209
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2019
ms.locfileid: "54245841"
---
# <a name="interactive-feature-verification-testing"></a>Testowanie weryfikacyjne interaktywnych funkcji  

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Struktura testowania weryfikacji interaktywnych funkcji służy do żądania testów w systemie. W przypadku żądania testu, firma Microsoft wykorzystuje platformę przygotować testy, które wymagają ręcznej czynności interaktywne. Firma Microsoft mogła używać platformę, aby połączyć ze sobą kilka autonomicznego zautomatyzowane testy.

W tym artykule opisano prosty scenariusz ręczne. Test sprawdza, czy zamiana dysku w usłudze Azure Stack. Struktura zbiera dzienniki diagnostyczne na każdym kroku. Możesz debugować błędy, jak je znaleźć. Struktura również pozwala na udostępnianie dzienników generowanych przez inne narzędzia i procesy i pozwala na dostarczenie informacji zwrotnych od scenariusza.

## <a name="overview-of-a-test-pass"></a>Przegląd przebiegu testu

Test wymiana dysku jest typowym scenariuszem. W tym przykładzie testu składa się z siedmiu kroków:

1.  Utwórz nową **przebiegu testu** przepływu pracy.
2.  Wybierz **dysku Test identyfikacyjny**.
3.  Uruchom test.
4.  Wybierz akcje w scenariuszu interaktywne weryfikacji.
5.  Sprawdź wynik tego scenariusza.
6.  Wynik testu można wysyłać do firmy Microsoft.
7.  Sprawdź stan w portalu VaaS.

## <a name="create-a-new-test-pass"></a>Utwórz nowy przebieg testu

1.  Przejdź do [portalu usługi Azure Stack weryfikacji](https://www.azurestackvalidation.com) i zaloguj się.

2.  Utwórz nowe rozwiązanie lub wybierz istniejącą grupę.

3.  Wybierz **Start** na **przebiegu testu** kafelka.

    ![Tekst alternatywny](media/azure-stack-vaas-interactive-feature-verification/image1.png)

4.  Wprowadź nazwę dla **przebiegu testu** przepływu pracy.

5.  Wprowadź środowiska i typowych parametrów testu, postępując zgodnie z instrukcjami w [wspólnych parametrów przepływów pracy dla usługi Azure Stack weryfikacji jako usługa](azure-stack-vaas-parameters.md) artykułu.

6.  Parametry połączenia diagnostyki muszą być zgodne z formatem określonym w [parametry testu](azure-stack-vaas-parameters.md#test-parameters) sekcji [wspólnych parametrów przepływów pracy](azure-stack-vaas-parameters.md) artykułu.

7.  Wprowadź wymagane parametry dla testu.

8.  Wybierz agenta, aby uruchomić interakcyjne testu.

> [!Note]  
> Należy określić domeny i hasła administratora dla dysku identyfikacji interaktywnych funkcji weryfikacji testu.

![Tekst alternatywny](media/azure-stack-vaas-interactive-feature-verification/image2.png)

## <a name="select-the-test"></a>Wybierz test

1.  Wybierz **Test identyfikacji dysku\<wersji >**.

    > [!Note]  
    > Powoduje zwiększenie wersją testu, ponieważ wprowadzono ulepszenia do zabezpieczeń testu. Najwyższa wersja zawsze należy używać, chyba że wskazuje firmy Microsoft, w przeciwnym razie.

    ![Tekst alternatywny](media/azure-stack-vaas-interactive-feature-verification/image4.png)

2.  Podaj użytkownika administratora domeny i hasła, wybierając **Edytuj**.

3.  Wybierz odpowiedni test wykonywania agenta/Menedżer DVM można uruchomić test na.

    ![Tekst alternatywny](media/azure-stack-vaas-interactive-feature-verification/image5.png)

4.  Wybierz **przesyłania** aby rozpocząć test.

![Tekst alternatywny](media/azure-stack-vaas-interactive-feature-verification/image6.png)

## <a name="start-the-test"></a>Uruchom test

Pokaż monity Test identyfikacji dysku na komputerze z uruchomionym agentem VaaS. Zazwyczaj jest to Menedżer DVM lub serwer Przesiadkowy dla wystąpienia usługi Azure Stack.

![Tekst alternatywny](media/azure-stack-vaas-interactive-feature-verification/image8.png)

## <a name="choose-the-actions"></a>Wybierz akcje

1.  Postępuj zgodnie z **dokumentacji** i **weryfikacji** łącza, aby przejrzeć instrukcje od firmy Microsoft dotyczące realizacji tego scenariusza.

    ![Tekst alternatywny](media/azure-stack-vaas-interactive-feature-verification/image9.png)

2.  Wybierz opcję **Dalej**.

    ![Tekst alternatywny](media/azure-stack-vaas-interactive-feature-verification/image10.png)

3.  Postępuj zgodnie z instrukcjami, aby uruchomić skrypt precheck.

    ![Tekst alternatywny](media/azure-stack-vaas-interactive-feature-verification/image11.png)

4.  Po pomyślnym wykonaniu skryptu precheck uruchamianie ręczne scenariusza (wymiana dysku) zgodnie **dokumentacji** i **weryfikacji** łączy z **informacji**kartę.

    ![Tekst alternatywny](media/azure-stack-vaas-interactive-feature-verification/image12.png)

5.  Nie zamykaj okna dialogowego, podczas wykonywania ręcznych scenariusza.

6.  Po zakończeniu wykonywania ręcznych scenariusz, wykonaj instrukcje do uruchomienia skryptu Sprawdź wpis.

    ![Tekst alternatywny](media/azure-stack-vaas-interactive-feature-verification/image13.png)

7.  Po pomyślnym zakończeniu ręcznego scenariusza (wymiana dysku) wybierz **dalej**.

    ![Tekst alternatywny](media/azure-stack-vaas-interactive-feature-verification/image14.png)

> [!Important]  
> Jeśli zamkniesz okno, test zostanie zatrzymane, zanim to się robi.

## <a name="check-the-status"></a>Sprawdź stan

1.  Po zakończeniu testu poprosimy Cię o opinię.

    ![Tekst alternatywny](media/azure-stack-vaas-interactive-feature-verification/image15.png)

2.  Odpowiedzi na następujące pytania pomogą firmie Microsoft, oceny jakości szybkości i wydania Powodzenie scenariusza.

## <a name="send-the-test-result"></a>Wyślij do wyniku testu

1.  Dołącz wszystkie pliki dziennika, który chcesz przesłać do firmy Microsoft.

    ![Tekst alternatywny](media/azure-stack-vaas-interactive-feature-verification/image16.png)

2.  Zaakceptuj Umowę licencyjną przesyłania opinii.

3.  Wybierz **przesyłania** wysyłać wyniki do firmy Microsoft.

## <a name="next-steps"></a>Kolejne kroki

- [Monitorowanie i zarządzanie testami w portalu VaaS](azure-stack-vaas-monitor-test.md)
