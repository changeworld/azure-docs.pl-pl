---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: a8070d25e2606d8ad72ac231a0a208072c612c5c
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59737224"
---
Ostatnim krokiem jest przygotowanie urządzenia do wysłania. W tym kroku wszystkie udziały urządzenia zostają przeniesione do trybu offline. Po rozpoczęciu tego procesu dostęp do udziałów nie jest już możliwy.

> [!IMPORTANT]
> Przygotowywanie do wysłania jest wymagane, ponieważ umożliwia oflagowanie danych, które nie są zgodne z konwencjami nazewnictwa platformy Azure. Ten krok zostanie pominięty, można wynik w danych potencjalne błędy przekazywania z powodu niezgodnych danych.

1. Przejdź do pozycji **Przygotowanie do wysłania** i kliknij przycisk **Rozpocznij przygotowywanie**. Domyślnie sumy kontrolne są obliczane podczas przygotowania do wysłania. Obliczanie sum kontrolnych może potrwać klika godzin a nawet dni — w zależności od rozmiaru danych. 
   
    ![Przygotowanie do wysłania 1](media/data-box-prepare-to-ship/prepare-to-ship1.png)

    Jeśli z jakiegoś powodu chcesz zatrzymać przygotowywanie urządzenia, kliknij przycisk **Zatrzymaj przygotowywanie**. Przygotowywanie do wysłania można wznowić później.
        
    ![Przygotowanie do wysłania 2](media/data-box-prepare-to-ship/prepare-to-ship2.png)
    
2. Przygotowywanie do wysłania rozpoczyna się i udziały urządzenia przechodzą w tryb offline. Po przygotowaniu urządzenia jest wyświetlane przypomnienie o pobraniu etykiety wysyłkowej.

    ![Przypomnienie o pobraniu etykiety wysyłkowej](media/data-box-prepare-to-ship/download-shipping-label-reminder.png)

3. Po zakończeniu przygotowywania urządzenia jego stan jest aktualizowany do wartości *Gotowe do wysyłki*, a urządzenie jest blokowane.
        
    ![Przygotowanie do wysłania 3](media/data-box-prepare-to-ship/prepare-to-ship3.png)

    Jeśli chcesz skopiować więcej danych na urządzenie, możesz je odblokować, skopiować dane i ponownie uruchomić proces przygotowywania do wysłania.

    Jeśli w tym kroku wystąpią błędy, konieczne będzie pobranie dziennika błędów i rozwiązanie problemów. Po usunięciu błędów uruchom **przygotowywanie do wysłania**.

4. Po pomyślnym (bezbłędnym) zakończeniu przygotowywania do wysłania pobierz listę plików (tzw. manifest) skopiowanych w ramach tego procesu. Później możesz użyć tej listy, aby zweryfikować pliki przekazane na platformę Azure.
        
    ![Przygotowanie do wysłania 1](media/data-box-prepare-to-ship/prepare-to-ship4.png)

5. Zamknij urządzenie. Przejdź do strony **Zamknij lub uruchom ponownie** i kliknij przycisk **Zamknij**. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **OK**, aby kontynuować.

6. Odłącz kable. Następnym krokiem jest wysłanie urządzenia do firmy Microsoft.
