---
title: Zarządzanie urządzeniami w aplikacji Azure IoT centralnej | Dokumentacja firmy Microsoft
description: Jako operatora informacje o sposobie zarządzania urządzeniami w aplikacji Azure IoT centralnej.
services: iot-central
author: ellenfosborne
ms.author: elfarber
ms.date: 01/21/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 75472d701160e7cfd331d01efcdc1a19ae20fb2d
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Zarządzanie urządzeniami w aplikacji Azure IoT centralnej

W tym artykule opisano sposób jako operatora, w celu zarządzania urządzeniami w aplikacji Microsoft Azure IoT centralnej. Operator może:

- Użyj **Explorer** strona do wyświetlania, dodawania i usuwania urządzeń podłączonych do aplikacji Azure IoT centralnej.
- Prowadź rejestr aktualności urządzeń.
- Aktualizowanie metadanych urządzenia, zmieniając wartości przechowywane we właściwościach urządzenia.
- Sterowania działaniem urządzeń, aktualizując ustawienie na określonym urządzeniu z **ustawienia** strony.

## <a name="view-your-devices"></a>Wyświetlanie urządzeń

Aby wyświetlić poszczególne urządzenia:

1. Wybierz **Explorer** w menu nawigacji po lewej stronie. W tym miejscu wyświetlić listę z [szablony urządzenia](howto-set-up-template.md).

1. Wybierz **szablonu urządzenia** w okienku po lewej stronie.

1. W okienku po prawej stronie możesz wyświetlić listę urządzeń utworzone na podstawie tego szablonu urządzenia. Wybierz poszczególne urządzenia, aby wyświetlić **szczegóły urządzenia** strony dla tego urządzenia:

    [![Strona szczegółów urządzenia](./media/howto-manage-devices/image1.png)](./media/howto-manage-devices/image1.png#lightbox)

## <a name="add-a-device"></a>Dodawanie urządzenia

Aby dodać urządzenie do aplikacji Azure IoT centralnej:

1. Wybierz **Explorer** w menu nawigacji po lewej stronie.

1. Wybierz szablon urządzenia, z którego chcesz utworzyć urządzenia.

1. Wybierz + **nowe**.

1. Wybierz **rzeczywistych** lub **symulowane**. Jest rzeczywiste urządzenie fizyczne urządzenia, które można połączyć się z aplikacją Azure IoT centralnej. Symulowane urządzenie ma przykładowych danych, wygenerowane automatycznie przez Azure IoT centralnej. W tym przykładzie użyto rzeczywistego urządzenia. Wybierz **rzeczywistych** można przejść do **szczegóły urządzenia** strony dla nowego urządzenia.


## <a name="bulk-import-devices"></a>Importowania zbiorczego urządzeń

Do nawiązania połączenia z aplikacji Azure IoT centralnej dużej liczby urządzeń oferty zbiorcze importowania urządzeń przy użyciu pliku CSV. 

Wymagania dotyczące pliku CSV:
1. Plik CSV powinien mieć tylko jedną kolumnę zawierającą identyfikatory urządzeń.

1. Plik nie powinien mieć żadnych nagłówka.


Do zbiorczego rejestrowania urządzeń w aplikacji:

1. Wybierz **Explorer** w menu nawigacji po lewej stronie.

1. W lewym panelu wybierz szablon urządzenia, dla którego chcesz zbiorcze utworzyć urządzenia.

1. Wybierz **nowy** i wybierz **importowania zbiorczego**.

    [![Działania importowania zbiorczego](./media/howto-manage-devices/BulkImport1.png)](./media/howto-manage-devices/BulkImport1.png#lightbox)

1. Wybierz plik CSV, który zawiera listę identyfikatorów urządzenia do zaimportowania.

1. Importuj urządzenia zaczyna się po przekazaniu pliku. Można śledzić stan importu u góry siatki urządzenia.

1. Po zakończeniu importowania komunikat z potwierdzeniem jest wyświetlana na siatce urządzenia.

    [![Powodzenie importowania zbiorczego](./media/howto-manage-devices/BulkImport3.png)](./media/howto-manage-devices/BulkImport3.png#lightbox)

Jeśli urządzenie zaimportować operacja kończy się niepowodzeniem, będzie błąd wyświetlana na siatce urządzenia. Plik dziennika Przechwytywanie wszystkich błędów jest generowana i mogą być pobierane przez kliknięcie przycisku komunikat o błędzie.



## <a name="delete-a-device"></a>Usuwanie urządzenia

Aby usunąć albo prawdziwe lub symulowane urządzenie z aplikacji Azure IoT centralnej:

1. Wybierz **Explorer** w menu nawigacji.

1. Wybierz urządzenia, które chcesz usunąć szablon urządzenia.

1. Zaznacz pole obok urządzenia, aby usunąć.

1. Wybierz **usunąć**.

## <a name="change-a-device-setting"></a>Zmień ustawienia urządzenia

Ustawienia określają zachowanie urządzenia. Innymi słowy umożliwiają one Podaj dane wejściowe do Twojego urządzenia. Możesz wyświetlić i zaktualizować ustawienia urządzenia na **szczegóły urządzenia** strony.

1. Wybierz **Explorer** w menu nawigacji.

1. Wybierz szablon urządzenia urządzenia, którego ustawienia chcesz zmienić.

1. Wybierz **ustawienia** kartę. W tym miejscu wyświetlić wszystkie ustawienia, którego urządzenie ma i ich wartości. Dla każdego ustawienia, można wyświetlić, jeśli urządzenie jest nadal synchronizowany.

1. Zmodyfikuj ustawienia do żądanej wartości. Można zmodyfikować jednocześnie wiele ustawień i zaktualizować je wszystko na tym samym czasie.

1. Wybierz **aktualizacji**. Wartości są wysyłane do urządzenia. Gdy urządzenie potwierdza zmiana ustawień, ustawienie wraca do **zsynchronizowane**.

## <a name="change-a-property"></a>Zmień właściwości

Właściwości są urządzenia metadane skojarzone z urządzenia, takie jak miejscowość i numer seryjny. Można wyświetlać i aktualizować właściwości na **szczegóły urządzenia** strony.

1. Wybierz **Explorer** w menu nawigacji.

1. Wybierz szablon urządzenia urządzenia, którego właściwości chcesz zmienić.

1. Wybierz **właściwości** kartę, której możesz zobaczyć wszystkie właściwości.

1. Modyfikuj właściwości do żądanej wartości. Można zmodyfikować jednocześnie wiele właściwości i zaktualizować je wszystko na tym samym czasie.

1. Wybierz **aktualizacji**.

> [!NOTE]
> Nie można zmienić wartości _właściwości urządzenia_. Właściwości urządzenia są ustawiane przez urządzenie i są tylko do odczytu w aplikacji Azure IoT centralnej.

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz sposobu zarządzania urządzeniami w aplikacji Azure IoT centralnej, Oto sugerowane następnego kroku:

> [!div class="nextstepaction"]
> [Jak używać zestawów urządzeń](howto-use-device-sets.md)

<!-- Next how-tos in the sequence -->
