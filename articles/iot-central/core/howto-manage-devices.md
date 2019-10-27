---
title: Zarządzanie urządzeniami w aplikacji IoT Central platformy Azure | Microsoft Docs
description: Jako operator, Dowiedz się, jak zarządzać urządzeniami w aplikacji IoT Central platformy Azure.
author: ellenfosborne
ms.author: elfarber
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: c3d07c95592c1c43a070afbbf5091693ce0a6611
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72950355"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Zarządzanie urządzeniami w aplikacji IoT Central platformy Azure

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

W tym artykule opisano, jak za pomocą operatora zarządzać urządzeniami w aplikacji IoT Central platformy Azure. Jako operator można:

- Korzystając ze strony **Device Explorer** , można wyświetlać, dodawać i usuwać urządzenia połączone z aplikacją IoT Central platformy Azure.
- Przechowywanie aktualnych danych spisu urządzeń.
- Zadbaj o aktualność metadanych urządzenia, zmieniając wartości przechowywane we właściwościach urządzenia.
- Kontroluj zachowanie urządzeń, aktualizując ustawienia na określonym urządzeniu ze strony **Ustawienia** .

## <a name="view-your-devices"></a>Wyświetlanie urządzeń

Aby wyświetlić pojedyncze urządzenie:

1. Wybierz **Device Explorer** w lewym okienku. W tym miejscu zostanie wyświetlona lista [szablonów urządzeń](howto-set-up-template.md).

1. Wybierz szablon urządzenia na liście **Szablony** .

1. W okienku po prawej stronie **Device Explorer** zostanie wyświetlona lista urządzeń utworzonych na podstawie tego szablonu urządzenia. Wybierz pojedyncze urządzenie, aby wyświetlić stronę szczegółów urządzenia dla tego urządzenia:

    ![Strona szczegółów urządzenia](./media/howto-manage-devices/devicelist.png)

## <a name="add-a-device"></a>Dodawanie urządzenia

Aby dodać urządzenie do aplikacji usługi Azure IoT Central:

1. Wybierz **Device Explorer** w lewym okienku.

1. Wybierz szablon urządzenia, z którego chcesz utworzyć urządzenie.

1. Wybierz pozycję + **Nowy**.

1. Wybierz opcję **rzeczywiste** lub **symulowane**. Rzeczywiste urządzenie dotyczy urządzenia fizycznego połączonego z aplikacją Azure IoT Central. Urządzenie symulowane ma przykładowe dane wygenerowane przez usługę Azure IoT Central.

## <a name="import-devices"></a>Importuj urządzenia

Aby połączyć dużą liczbę urządzeń z aplikacją, można zbiorczo importować urządzenia z pliku CSV. Plik CSV powinien zawierać następujące kolumny i nagłówki:

* **IOTC_DeviceID** — identyfikator urządzenia powinien zawierać tylko małe litery.
* **IOTC_DeviceName** — ta kolumna jest opcjonalna.

Aby przeprowadzić zbiorczą rejestrację urządzeń w aplikacji:

1. Wybierz **Device Explorer** w lewym okienku.

1. Na panelu po lewej stronie wybierz szablon urządzenia, dla którego chcesz utworzyć zbiorczo urządzenia.

    > [!NOTE]
    > Jeśli nie masz jeszcze szablonu urządzenia, możesz zaimportować urządzenia w obszarze **nieskojarzone urządzenia** i zarejestrować je bez szablonu. Po zaimportowaniu urządzeń można je skojarzyć z szablonem.

1. Wybierz pozycję **Importuj**.

    ![Importuj akcję](./media/howto-manage-devices/bulkimport1a.png)

1. Wybierz plik CSV, który zawiera listę identyfikatorów urządzeń do zaimportowania.

1. Importowanie urządzenia rozpocznie się po przekazaniu pliku. Można śledzić stan importu u góry siatki urządzeń.

1. Po zakończeniu importowania na siatce urządzeń zostanie wyświetlony komunikat o powodzeniu.

    ![Powodzenie importowania](./media/howto-manage-devices/bulkimport3a.png)

Jeśli operacja importowania urządzenia nie powiedzie się, na siatce urządzeń zostanie wyświetlony komunikat o błędzie. Zostanie wygenerowany plik dziennika, który przechwytuje wszystkie błędy, które można pobrać.

**Kojarzenie urządzeń z szablonem**

W przypadku rejestrowania urządzeń przez uruchomienie importu w obszarze **nieskojarzone urządzenia**zostaną utworzone urządzenia bez skojarzenia szablonu urządzenia. Urządzenia muszą być skojarzone z szablonem, aby eksplorować dane oraz inne szczegóły dotyczące urządzenia. Wykonaj następujące kroki, aby skojarzyć urządzenia z szablonem:

1. Wybierz **Device Explorer** w lewym okienku.

1. Na panelu po lewej stronie wybierz pozycję **nieskojarzone urządzenia**:

    ![Nieskojarzone urządzenia](./media/howto-manage-devices/unassociateddevices1a.png)

1. Wybierz urządzenia, które chcesz skojarzyć z szablonem:

1. Wybierz pozycję **Skojarz**:

    ![Skojarz urządzenia](./media/howto-manage-devices/unassociateddevices2a.png)

1. Wybierz szablon z listy dostępnych szablonów i wybierz pozycję **Skojarz**.

1. Wybrane urządzenia są skojarzone z wybranym szablonem urządzenia.

> [!NOTE]
> Po skojarzeniu urządzenia z szablonem nie można go usunąć ani skojarzyć z innym szablonem.

## <a name="export-devices"></a>Eksportuj urządzenia

Aby podłączyć rzeczywiste urządzenie do IoT Central, potrzebne są jego parametry połączenia. Aby uzyskać informacje potrzebne do tworzenia parametrów połączenia urządzenia, można zbiorczo wyeksportować szczegóły dotyczące urządzeń. Proces eksportowania tworzy plik CSV z tożsamością urządzenia, nazwą urządzenia i kluczami dla wszystkich wybranych urządzeń.

Aby wyeksportować zbiorczo urządzenia z aplikacji:

1. Wybierz **Device Explorer** w lewym okienku.

1. Na panelu po lewej stronie wybierz szablon urządzenia, z którego mają zostać wyeksportowane urządzenia.

1. Wybierz urządzenia, które chcesz wyeksportować, a następnie wybierz akcję **Eksportuj** .

    ![Eksportuj](./media/howto-manage-devices/export1a.png)

1. Rozpocznie się proces eksportowania. Stan można śledzić w górnej części siatki.

1. Po zakończeniu eksportowania zostanie wyświetlony komunikat o powodzeniu wraz z linkiem umożliwiającym pobranie wygenerowanego pliku.

1. Wybierz **komunikat o powodzeniu** , aby pobrać plik do folderu lokalnego na dysku.

    ![Powodzenie eksportowania](./media/howto-manage-devices/export2a.png)

1. Wyeksportowany plik CSV zawiera następujące kolumny: Identyfikator urządzenia, nazwa urządzenia, klucze urządzeń i odciski palca certyfikatu x509:

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

Aby uzyskać więcej informacji na temat parametrów połączenia i połączeń rzeczywistych urządzeń z aplikacją IoT Central, zobacz [łączność urządzeń w usłudze Azure IoT Central](concepts-connectivity.md).

## <a name="delete-a-device"></a>Usuwanie urządzenia

Aby usunąć urządzenie prawdziwe lub symulowane z aplikacji IoT Central platformy Azure:

1. Wybierz **Device Explorer** w lewym okienku.

1. Wybierz szablon urządzenia, które chcesz usunąć.

1. Zaznacz pole wyboru obok urządzenia, które ma zostać usunięte.

1. Wybierz pozycję **Usuń**.

## <a name="change-a-device-setting"></a>Zmiana ustawienia urządzenia

Ustawienia sterują zachowaniem urządzenia. Innymi słowy, umożliwiają one dostarczenie danych wejściowych na urządzeniu. Ustawienia urządzenia można wyświetlać i aktualizować na stronie **szczegóły urządzenia** .

1. Wybierz **Device Explorer** w lewym okienku.

1. Wybierz szablon urządzenia urządzenia, którego ustawienia chcesz zmienić.

1. Wybierz kartę **Ustawienia** . W tym miejscu zobaczysz wszystkie ustawienia urządzenia i ich bieżące wartości. Dla każdego ustawienia można sprawdzić, czy urządzenie jest nadal synchronizowane.

1. Zmodyfikuj ustawienia do potrzebnych wartości. Można modyfikować wiele ustawień jednocześnie i aktualizować je wszystkie w tym samym czasie.

1. Wybierz pozycję **Aktualizuj**. Wartości są wysyłane do urządzenia. Gdy urządzenie potwierdzi zmianę ustawienia, stan ustawienia wraca do **synchronizacji**.

## <a name="change-a-property"></a>Zmiana właściwości

Właściwości to metadane urządzenia skojarzone z urządzeniem, takie jak miasto i numer seryjny. Właściwości można wyświetlać i aktualizować na stronie **szczegółów urządzenia** .

1. Wybierz **Device Explorer** w lewym okienku.

1. Wybierz szablon urządzenia, którego właściwości chcesz zmienić.

1. Wybierz kartę **Właściwości** , w której zobaczysz wszystkie właściwości.

1. Zmodyfikuj właściwości aplikacji, tak aby były potrzebne wartości. Jednocześnie można modyfikować wiele właściwości i aktualizować je wszystkie w tym samym czasie. Wybierz pozycję **Aktualizuj**.

> [!NOTE]
> Nie można zmienić wartości _Właściwości urządzenia_. Właściwości urządzenia są ustawiane przez urządzenie i są tylko do odczytu w ramach aplikacji IoT Central platformy Azure.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak zarządzać urządzeniami w aplikacji IoT Central platformy Azure, Oto sugerowany następny krok:

> [!div class="nextstepaction"]
> [Jak korzystać z zestawów urządzeń](howto-use-device-sets.md)

<!-- Next how-tos in the sequence -->
