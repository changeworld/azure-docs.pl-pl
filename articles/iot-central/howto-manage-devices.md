---
title: Zarządzanie urządzeniami w Twojej aplikacji usługi Azure IoT Central | Dokumentacja firmy Microsoft
description: Operator Dowiedz się, jak zarządzać urządzeniami w usłudze Azure IoT Central aplikacji.
author: ellenfosborne
ms.author: elfarber
ms.date: 01/30/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: a723ca6d69d45613636c7659e6566cec19b31d65
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60364182"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Zarządzanie urządzeniami w usłudze Azure IoT Central aplikacji

W tym artykule opisano jak operator, aby zarządzać urządzeniami w usłudze Azure IoT Central aplikacji. Operator może:

- Użyj **Device Explorer** strona do wyświetlania, dodawania i usuwania urządzeń połączonych z aplikacją usługi Azure IoT Central.
- Prowadź rejestr aktualności urządzeń.
- Aktualizuj metadane urządzenia, zmieniając wartości przechowywane we właściwościach urządzenia.
- Sterowanie zachowaniem urządzeń, aktualizując ustawienia na konkretnym urządzeniu z **ustawienia** strony.

## <a name="view-your-devices"></a>Wyświetlanie urządzeń

Aby wyświetlić poszczególnych urządzeń:

1. Wybierz **Device Explorer** w menu nawigacji po lewej stronie. W tym miejscu możesz wyświetlić listę swoje [szablonów urządzeń](howto-set-up-template.md).

1. Wybierz szablon urządzenia w **szablony** listy.

1. W okienku po prawej stronie **Device Explorer** strony, zobaczysz listę urządzeń utworzone na podstawie szablonu, którego urządzenia. Wybierz poszczególnych urządzeń, aby wyświetlić strony szczegółów urządzenia dla tego urządzenia:

    ![Strony szczegółów urządzenia](./media/howto-manage-devices/devicelist.png)

## <a name="add-a-device"></a>Dodawanie urządzenia

Aby dodać urządzenie do usługi Azure IoT Central aplikacji:

1. Wybierz **Device Explorer** w menu nawigacji po lewej stronie.

1. Wybierz szablon urządzenia, z którego chcesz utworzyć urządzenia.

1. Wybierz + **nowe**.

1. Wybierz **rzeczywistych** lub **symulowane**. Rzeczywiste urządzenie jest w przypadku urządzenia fizycznego, w której się łączysz się z aplikacją usługi Azure IoT Central. Symulowane urządzenia znajdują się przykładowe dane wygenerowane automatycznie przez usługi Azure IoT Central.

## <a name="import-devices"></a>Importuj urządzenia

Aby połączyć z dużą liczbą urządzeń do aplikacji, można zbiorczo urządzenia importowania z pliku CSV. Ten plik CSV powinien zawierać następujące kolumn i nagłówków:

* **IOTC_DeviceID** — identyfikator urządzenia powinny zawierać tylko małe litery.
* **IOTC_DeviceName** — w tej kolumnie jest opcjonalne.

Aby zbiorczo — rejestracja urządzeń w Twojej aplikacji:

1. Wybierz **Device Explorer** w menu nawigacji po lewej stronie.

1. W lewym panelu wybierz szablon urządzenia, dla którego chcesz zbiorczo utworzyć urządzenia.

    > [!NOTE]
    > Jeśli nie masz szablon urządzenia jeszcze zaimportowanie urządzeń w obszarze **Brak skojarzenia urządzenia** i zarejestruj je bez szablonu. Po zaimportowaniu urządzeń można skojarzyć je z szablonem.

1. Wybierz **importu**.

    ![Akcja importu](./media/howto-manage-devices/BulkImport1.png)

1. Wybierz plik CSV, który zawiera listę identyfikatorów urządzeń do zaimportowania.

1. Importuj urządzenia rozpoczyna się po przekazaniu pliku. Można śledzić stan importu w górnej części siatki urządzenia.

1. Po zakończeniu importowania w siatce urządzeń wyświetlany jest komunikat o powodzeniu.

    ![Powodzenie importu](./media/howto-manage-devices/BulkImport3.png)

Jeśli urządzenie zaimportować operacja kończy się niepowodzeniem, zobaczysz komunikat o błędzie na siatce urządzenia. Generowany jest plik dziennika przechwytywania wszystkich błędów do pobrania.

**Kojarzenie urządzeń przy użyciu szablonu**

Po zarejestrowaniu urządzenia przez uruchamianie importu w obszarze **Brak skojarzenia urządzenia**, a następnie urządzenia są tworzone bez skojarzenia szablonu dowolnego urządzenia. Urządzenia muszą zostać skojarzony z szablonem, aby eksplorować dane oraz inne szczegóły dotyczące danego urządzenia. Wykonaj następujące kroki, aby skojarzyć urządzenia z szablonem:

1. Wybierz **Device Explorer** w menu nawigacji po lewej stronie.

1. W panelu po lewej stronie wybierz **Brak skojarzenia urządzenia**:

    ![Urządzenia nieskojarzone](./media/howto-manage-devices/UnassociatedDevices1.png)

1. Wybierz urządzenia, które chcesz skojarzyć z szablonem:

1. Wybierz **skojarzyć**:

    ![Skojarz urządzenia](./media/howto-manage-devices/UnassociatedDevices2.png)

1. Wybierz szablon z listy dostępnych szablonów, a następnie wybierz **skojarzyć**.

1. Wybrane urządzenia są skojarzone z wybranym szablonie urządzenia.

> [!NOTE]
> Po urządzenia został skojarzony z szablonem, nie może być Brak skojarzenia ani skojarzony z innego szablonu.

## <a name="export-devices"></a>Eksportowanie urządzeń

Aby połączyć prawdziwe urządzenie z IoT Central, jego parametry połączenia będą potrzebne. Możesz wyeksportować szczegóły urządzenia w trybie zbiorczym, aby uzyskać informacje potrzebne do tworzenia parametrów połączenia urządzenia. Proces eksportowania tworzy plik CSV przy użyciu tożsamości urządzenia, nazwę urządzenia i klucze dla wszystkich wybranych urządzeń.

Aby zbiorczo eksportu urządzenia z poziomu aplikacji:

1. Wybierz **Device Explorer** w menu nawigacji po lewej stronie.

1. Na lewym panelu wybierz szablon urządzenia, z którego chcesz wyeksportować urządzenia.

1. Wybierz urządzenia, które chcesz wyeksportować, a następnie wybierz pozycję **wyeksportować** akcji.

    ![Eksportowanie](./media/howto-manage-devices/Export1.png)

1. Rozpoczyna się proces eksportowania. Można śledzić stan u góry strony siatki.

1. Po zakończeniu eksportu, komunikat o powodzeniu jest wyświetlany wraz z linkiem do pobrania wygenerowany plik.

1. Wybierz **komunikat o powodzeniu** można pobrać pliku do lokalnego folderu na dysku.

    ![Powodzenie eksportu](./media/howto-manage-devices/Export2.png)

1. Wyeksportowany plik CSV zawiera następujące kolumny: identyfikator urządzenia, nazwę urządzenia, klucze urządzeń i X509 odcisk palca certyfikatu:

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

Zobacz [łączność urządzeń w usłudze Azure IoT Central](concepts-connectivity.md), aby uzyskać więcej informacji o parametrach połączenia i połączenie rzeczywistych urządzeń, aplikacji IoT Central.

## <a name="delete-a-device"></a>Usuwanie urządzenia

Aby usunąć albo rzeczywistych lub symulowanych urządzeń z poziomu aplikacji usługi Azure IoT Central:

1. Wybierz **Device Explorer** w menu nawigacji.

1. Wybierz szablon urządzeń, urządzenia, które chcesz usunąć.

1. Zaznacz pole obok urządzenia, które można usunąć.

1. Wybierz **Usuń**.

## <a name="change-a-device-setting"></a>Zmień ustawienia urządzenia

Ustawienia sterują zachowaniem urządzenia. Innymi słowy umożliwiają one Podaj dane wejściowe do Twojego urządzenia. Możesz wyświetlić i zaktualizować ustawienia urządzenia na **szczegóły urządzenia** strony.

1. Wybierz **Device Explorer** w menu nawigacji.

1. Wybierz szablon urządzeń, urządzenia, którego ustawienia chcesz zmienić.

1. Wybierz **ustawienia** kartę. W tym miejscu zobaczysz wszystkie ustawienia, którego urządzenie ma oraz ich bieżących wartości. Dla każdego ustawienia, można wyświetlić, jeśli urządzenie jest nadal trwa synchronizowanie.

1. Zmodyfikuj ustawienia do wartości, których potrzebujesz. Można modyfikować wiele ustawień w czasie i zaktualizować je wszystkie w tym samym czasie.

1. Wybierz **aktualizacji**. Wartości są wysyłane do urządzenia. Gdy urządzenie potwierdza zmianę ustawienia, stan ustawienie powraca do **synchronizowane**.

## <a name="change-a-property"></a>Zmień właściwości

Właściwości są metadane urządzenia skojarzone z urządzenia, takie jak miejscowość i numer seryjny. Można wyświetlać i aktualizować właściwości na **szczegóły urządzenia** strony.

1. Wybierz **Device Explorer** w menu nawigacji.

1. Wybierz szablon urządzeń, urządzenia, którego właściwości chcesz zmienić.

1. Wybierz **właściwości** karty, w którym zostaną wyświetlone wszystkie właściwości.

1. Zmodyfikuj właściwości aplikacji, aby wartości, których potrzebujesz. Można zmodyfikować jednocześnie wiele właściwości atrybutu i zaktualizować je wszystkie w tym samym czasie. Wybierz **aktualizacji**.

> [!NOTE]
> Nie można zmienić wartość _właściwości urządzenia_. Właściwości są ustawiane przez urządzenia i urządzenia są tylko do odczytu w ramach aplikacji usługi Azure IoT Central.

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz sposobu zarządzania urządzeniami w usłudze Azure IoT Central aplikacji, poniżej przedstawiono sugerowany następny krok:

> [!div class="nextstepaction"]
> [Jak użyć zestawów urządzenia](howto-use-device-sets.md)

<!-- Next how-tos in the sequence -->
