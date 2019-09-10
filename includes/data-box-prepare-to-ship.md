---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/25/2019
ms.author: alkohli
ms.openlocfilehash: 1d52117440028c75b249f469f2b3576c2ab1c5c5
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2019
ms.locfileid: "67183895"
---
Ostatnim krokiem jest przygotowanie urządzenia do wysłania. W tym kroku wszystkie udziały urządzenia zostają przeniesione do trybu offline. Po rozpoczęciu tego procesu dostęp do udziałów nie jest już możliwy.

> [!IMPORTANT]
> Przygotowywanie do wysłania jest wymagane, ponieważ umożliwia oflagowanie danych, które nie są zgodne z konwencjami nazewnictwa platformy Azure. Pominięcie tego kroku może prowadzić do potencjalnych niepowodzeń przekazywania danych z powodu ich niezgodności.

1. Przejdź do pozycji **Przygotowanie do wysłania** i kliknij przycisk **Rozpocznij przygotowywanie**. Domyślnie sumy kontrolne są obliczane podczas kopiowania danych. Podczas przygotowywania do wysłania kończone jest obliczanie sum kontrolnych i tworzona jest lista plików (*pliki BOM*). Obliczanie sum kontrolnych może potrwać klika godzin a nawet dni — w zależności od rozmiaru danych. 
   
    ![Przygotowanie do wysłania 1](media/data-box-prepare-to-ship/prepare-to-ship1.png)

    Jeśli z jakiegoś powodu chcesz zatrzymać przygotowywanie urządzenia, kliknij przycisk **Zatrzymaj przygotowywanie**. Przygotowywanie do wysłania można wznowić później.
        
    ![Przygotowanie do wysłania 2](media/data-box-prepare-to-ship/prepare-to-ship2.png)
    
2. Przygotowywanie do wysłania rozpoczyna się i udziały urządzenia przechodzą w tryb offline. Po przygotowaniu urządzenia jest wyświetlane przypomnienie o pobraniu etykiety wysyłkowej.

    ![Przypomnienie o pobraniu etykiety wysyłkowej](media/data-box-prepare-to-ship/download-shipping-label-reminder.png)

3. Po zakończeniu przygotowywania urządzenia jego stan jest aktualizowany do wartości *Gotowe do wysyłki*, a urządzenie jest blokowane.
        
    ![Przygotowanie do wysłania 3](media/data-box-prepare-to-ship/prepare-to-ship3.png)

    Jeśli chcesz skopiować więcej danych na urządzenie, możesz je odblokować, skopiować dane i ponownie uruchomić proces przygotowywania do wysłania.

    Jeśli w tym kroku wystąpią błędy, konieczne będzie pobranie dziennika błędów i rozwiązanie problemów. Po usunięciu błędów uruchom **przygotowywanie do wysłania**.

4. Po pomyślnym (bezbłędnym) zakończeniu przygotowywania do wysłania pobierz listę plików (tzw. manifest) skopiowanych w ramach tego procesu. Później możesz użyć tej listy, aby zweryfikować pliki przekazane na platformę Azure. Aby uzyskać więcej informacji, zobacz [Inspekcja plików BOM podczas przygotowywania do wysłania](../articles/databox/data-box-logs.md#inspect-bom-during-prepare-to-ship).
        
    ![Przygotowanie do wysłania 1](media/data-box-prepare-to-ship/prepare-to-ship4.png)

5. Zamknij urządzenie. Przejdź do strony **Zamknij lub uruchom ponownie** i kliknij przycisk **Zamknij**. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **OK**, aby kontynuować.

6. Odłącz kable. Następnym krokiem jest wysłanie urządzenia do firmy Microsoft.
