---
title: Zarządzanie urządzeniami w aplikacji Azure IoT centralnej | Dokumentacja firmy Microsoft
description: Jako operatora informacje o sposobie zarządzania urządzeniami w aplikacji Azure IoT centralnej.
author: ellenfosborne
ms.author: elfarber
ms.date: 01/21/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: b0772e3186c86239c773222a2b2e8d602a46aa52
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36300598"
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


## <a name="import-devices"></a>Importuj urządzeń

Do nawiązania połączenia z aplikacji Azure IoT centralnej dużej liczby urządzeń oferty zbiorcze importowania urządzeń przy użyciu pliku CSV. 

Wymagania dotyczące pliku CSV:
1. Plik CSV powinien mieć tylko jedną kolumnę, który zawiera identyfikatory urządzeń.

1. Plik nie powinien mieć żadnych nagłówka.


Do zbiorczego rejestrowania urządzeń w aplikacji:

1. Wybierz **Explorer** w menu nawigacji po lewej stronie.

1. W lewym panelu wybierz szablon urządzenia, dla którego chcesz zbiorcze utworzyć urządzenia.

 >   [!NOTE] 
    Jeśli nie ma szablonu urządzenia jeszcze zaimportowanie urządzeń bez względu na **nieskojarzonych urządzeń** i zarejestruj je bez żadnych szablonów. Gdy urządzenia zostały zaimportowane, następnie można skojarzyć je z szablonem jako kolejnych czynności.

1. Kliknij przycisk **Importuj**.

    [![Akcja importu](./media/howto-manage-devices/BulkImport1.png)](./media/howto-manage-devices/BulkImport1.png#lightbox)

1. Wybierz plik CSV, który zawiera listę identyfikatorów urządzenia do zaimportowania.

1. Importuj urządzenia zaczyna się po przekazaniu pliku. Można śledzić stan importu u góry siatki urządzenia.

1. Po zakończeniu importowania komunikat z potwierdzeniem jest wyświetlana na siatce urządzenia.

    [![Powodzenie importu](./media/howto-manage-devices/BulkImport3.png)](./media/howto-manage-devices/BulkImport3.png#lightbox)

Jeśli urządzenie zaimportować operacja kończy się niepowodzeniem, zostanie wyświetlony komunikat o błędzie na siatce urządzenia. Plik dziennika Przechwytywanie wszystkich błędów jest generowana i mogą być pobierane przez kliknięcie przycisku komunikat o błędzie.


**Kojarzenie urządzeń z szablonu**

Po zarejestrowaniu urządzeń przez uruchomienie importu w obszarze **nieskojarzonych urządzeń**, następnie urządzenia są tworzone bez skojarzenia szablonu urządzenia. Urządzenie musi być powiązany z szablonem, aby eksplorować dane i inne szczegółowe informacje o urządzeniu. Wykonaj następujące kroki, aby skojarzyć urządzenia z szablonu:
1. Wybierz **Explorer** w menu nawigacji po lewej stronie.
1. W lewym panelu, wybierz **nieskojarzonych urządzeń**.
    [![Nieskojarzony urządzeń](./media/howto-manage-devices/UnassociatedDevices1.png)](./media/howto-manage-devices/UnassociatedDevices1.png#lightbox)
1. Wybierz urządzenia, które chcesz skojarzyć z szablonem.
1. Kliknij przycisk **skojarzyć** opcji.
    [![Kojarzenie urządzeń](./media/howto-manage-devices/UnassociatedDevices2.png)](./media/howto-manage-devices/UnassociatedDevices2.png#lightbox)
1. Wybierz szablon z listy dostępnych szablonów, a następnie kliknij przycisk **skojarzyć** przycisku.
1. Wybrane urządzenia zostaną przeniesione na podstawie szablonu odpowiednich urządzeń.

 >   [!NOTE] 
    Gdy urządzenie został skojarzony z szablonem, nie można zmienić ani skojarzony z innego szablonu.

## <a name="export-devices"></a>Eksportuj urządzeń

Aby udostępnić urządzeniom na łączenie z centralnego IoT, konieczne będzie parametry połączenia urządzenia, które jest generowany przez centralne IoT. Funkcja eksportu można pobrać parametry połączenia i inne właściwości urządzeń zbiorczo z poziomu aplikacji. Eksport tworzy plik CSV z tożsamości urządzenia, nazwę urządzenia i podstawowe parametry połączenia dla wybranych urządzeń.

Do zbiorczego eksportu urządzenia z poziomu aplikacji:
1. Wybierz **Explorer** w menu nawigacji po lewej stronie.

1. W lewym panelu wybierz szablon urządzenia, dla którego chcesz wyeksportować urządzenia.

1. Wybierz urządzenia, które chcesz wyeksportować, a następnie kliknij przycisk **wyeksportować** akcji.

    [![Eksportuj](./media/howto-manage-devices/Export1.png)](./media/howto-manage-devices/Export1.png#lightbox)

1. Proces eksportowania zostanie uruchomiona, można śledzić stan w górnej części siatki. 

1. Po zakończeniu eksportu, wraz z linkiem zostanie wyświetlony komunikat Powodzenie pobierania wygenerowanego pliku.

1. Polecenie **komunikat z potwierdzeniem** można pobrać pliku do folderu lokalnego na dysku.

    [![Powodzenie eksportu](./media/howto-manage-devices/Export2.png)](./media/howto-manage-devices/Export2.png#lightbox)

1. Wyeksportowany plik CSV ma następujące informacje:
    1. Name (Nazwa)
    1. Identyfikator urządzenia
    1. Parametry połączenia podstawowej


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
