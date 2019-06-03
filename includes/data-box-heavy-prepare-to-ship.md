---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/28/2019
ms.author: alkohli
ms.openlocfilehash: f5b60d862be0d71f71f770c47d88ad39f2fc6ac7
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66419573"
---
Ostatnim krokiem jest przygotowanie urządzenia do wysłania. W tym kroku wszystkie udziały urządzenia zostają przeniesione do trybu offline. Po rozpoczęciu tego procesu dostęp do udziałów nie jest już możliwy.

> [!IMPORTANT]
> Przygotowywanie do wysłania jest wymagane, ponieważ umożliwia oflagowanie danych, które nie są zgodne z konwencjami nazewnictwa platformy Azure. Ten krok zostanie pominięty, można wynik w danych potencjalne błędy przekazywania z powodu niezgodnych danych.

1. Przejdź do pozycji **Przygotowanie do wysłania** i kliknij przycisk **Rozpocznij przygotowywanie**. Domyślnie sumy kontrolne są obliczane, podczas gdy dane są kopiowane. Przygotowywanie do wysłania kończy obliczanie sum kontrolnych i tworzy listę plików (znany także jako *pliki BOM* lub manifest). Obliczanie sum kontrolnych może potrwać klika godzin a nawet dni — w zależności od rozmiaru danych.
   
    ![Przygotowanie do wysłania 1](media/data-box-heavy-prepare-to-ship/prepare-to-ship1.png)

    Jeśli z jakiegoś powodu chcesz zatrzymać przygotowywanie urządzenia, kliknij przycisk **Zatrzymaj przygotowywanie**. Przygotowywanie do wysłania można wznowić później.
        
    ![Przygotowanie do wysłania 2](media/data-box-heavy-prepare-to-ship/prepare-to-ship2.png)
    
2. Przygotowywanie do wysłania rozpoczyna się i udziały urządzenia przechodzą w tryb offline. Po przygotowaniu urządzenia jest wyświetlane przypomnienie o pobraniu etykiety wysyłkowej.

    ![Przypomnienie o pobraniu etykiety wysyłkowej](media/data-box-heavy-prepare-to-ship/download-shipping-label-reminder.png)

3. Po zakończeniu przygotowywania urządzenia jego stan jest aktualizowany do wartości *Gotowe do wysyłki*, a urządzenie jest blokowane.
        
    ![Przygotowanie do wysłania 3](media/data-box-heavy-prepare-to-ship/prepare-to-ship3.png)

    Jeśli chcesz skopiować więcej danych na urządzenie, możesz je odblokować, skopiować dane i ponownie uruchomić proces przygotowywania do wysłania.

    Jeśli występują błędy, w tym kroku Pobierz dziennik błędów i usunąć błędy. Po usunięciu błędów uruchom **przygotowywanie do wysłania**.

4. Przygotowywanie do wysłania — po zostało ukończone pomyślnie (z bez błędów), Pobierz listę plików (znany także jako *pliki BOM* lub manifest) kopiowane w ramach tego procesu. 

    ![Pobierz listę plików lub znak BOM](media/data-box-heavy-prepare-to-ship/download-list-of-files.png)

   Później możesz użyć tej listy, aby zweryfikować pliki przekazane na platformę Azure. Aby uzyskać więcej informacji, zobacz [BOM sprawdzanie plików podczas przygotowywania do wysłania](../articles/databox/data-box-logs.md#inspect-bom-during-prepare-to-ship).
        
    ![Przykładowy plik BOM](media/data-box-heavy-prepare-to-ship/sample-bom-file.png)

5. Zamknij urządzenie. Przejdź do strony **Zamknij lub uruchom ponownie** i kliknij przycisk **Zamknij**. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **OK**, aby kontynuować.

    ![Zamknij pierwszego węzła urządzenia](media/data-box-heavy-prepare-to-ship/shut-down-device-node.png)

6. Powtórz powyższe kroki dla węzła urządzeń.
7. Gdy urządzenie ma zostać całkowicie zamknięty, wszystkie diod LED tyłu urządzenie będzie została wyłączona. Następnym krokiem jest, aby usunąć wszystkie kable i odesłanie urządzenia do firmy Microsoft.
