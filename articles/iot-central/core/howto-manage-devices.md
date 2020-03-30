---
title: Zarządzanie urządzeniami w aplikacji Azure IoT Central | Dokumenty firmy Microsoft
description: Jako operator dowiedz się, jak zarządzać urządzeniami w aplikacji Azure IoT Central.
author: sarahhubbard
ms.author: sahubbar
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 8725a822c575ce80b9810d56bfd072241ded4c86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157946"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Zarządzanie urządzeniami w aplikacji Azure IoT Central



W tym artykule opisano, jak jako operator zarządzać urządzeniami w aplikacji Azure IoT Central. Jako operator możesz:

- Strona **Urządzenia** służy do wyświetlania, dodawania i usuwania urządzeń połączonych z aplikacją Azure IoT Central.
- Utrzymuj aktualny spis urządzeń.
- Aktualizuj metadane urządzenia, zmieniając wartości zapisane we właściwościach urządzenia z widoków.
- Kontroluj zachowanie urządzeń, aktualizując ustawienie na określonym urządzeniu z widoków.

## <a name="view-your-devices"></a>Wyświetlanie urządzeń

Aby wyświetlić poszczególne urządzenia:

1. Wybierz **pozycję Urządzenia** w lewym okienku. Tutaj zobaczysz listę wszystkich urządzeń i szablonów urządzeń.

1. Wybierz szablon urządzenia.

1. W okienku po prawej stronie strony **Urządzenia** zostanie wyświetlona lista urządzeń utworzonych na podstawie tego szablonu urządzenia. Wybierz pojedyncze urządzenie, aby wyświetlić stronę szczegółów urządzenia dla tego urządzenia:

    ![Strona szczegółów urządzenia](./media/howto-manage-devices/devicelist.png)


## <a name="add-a-device"></a>Dodawanie urządzenia

Aby dodać urządzenie do aplikacji Usługi Azure IoT Central:

1. Wybierz **pozycję Urządzenia** w lewym okienku.

1. Wybierz szablon urządzenia, z którego chcesz utworzyć urządzenie.

1. Wybierz + **Nowy**.

1. Włącz **lub** **wyłącz**przełącznik **Symulowane** . Prawdziwe urządzenie jest dla urządzenia fizycznego, które łączysz się z aplikacją Azure IoT Central. Symulowane urządzenie ma przykładowe dane wygenerowane dla Ciebie przez usługę Azure IoT Central.

1. Kliknij przycisk **Utwórz**.

1. To urządzenie pojawi się teraz na liście urządzeń dla tego szablonu. Wybierz urządzenie, aby wyświetlić stronę szczegółów urządzenia zawierającą wszystkie widoki urządzenia.

## <a name="import-devices"></a>Importowanie urządzeń

Aby podłączyć dużą liczbę urządzeń do aplikacji, można zbiorczo importować urządzenia z pliku CSV. Plik CSV powinien mieć następujące kolumny i nagłówki:

* **IOTC_DeviceID** - identyfikator urządzenia powinien być małe litery.
* **IOTC_DeviceName** - ta kolumna jest opcjonalna.

Aby zarejestrować urządzenia zbiorczo w aplikacji:

1. Wybierz **pozycję Urządzenia** w lewym okienku.

1. Na lewym panelu wybierz szablon urządzenia, dla którego chcesz zbiorczo utworzyć urządzenia.

    > [!NOTE]
    > Jeśli nie masz jeszcze szablonu urządzenia, możesz zaimportować urządzenia w obszarze **Wszystkie urządzenia** i zarejestrować je bez szablonu. Po zaimportowaniu urządzeń można je przeprowadzić na przykład.

1. Wybierz pozycję **Import**.

    ![Akcja importu](./media/howto-manage-devices/bulkimport1a.png)


1. Wybierz plik CSV zawierający listę identyfikatorów urządzeń do zaimportowania.

1. Importowanie urządzeń rozpoczyna się po przesłaniu pliku. Stan importu można śledzić w panelu Operacje urządzenia. Ten panel pojawia się automatycznie po rozpoczęciu importowania lub można uzyskać do niego dostęp za pomocą ikony dzwonka w prawym górnym rogu.

1. Po zakończeniu importowania w panelu Operacje urządzenia wyświetlany jest komunikat o powodach.

    ![Sukces importu](./media/howto-manage-devices/bulkimport3a.png)


Jeśli operacja importowania urządzenia nie powiedzie się, na panelu Operacje urządzenia zostanie wyświetlony komunikat o błędzie. Generowany jest plik dziennika przechwytujący wszystkie błędy, które można pobrać.

**Migrowanie urządzeń do szablonu**

Jeśli zarejestrujesz urządzenia, uruchamiając import w obszarze **Wszystkie urządzenia,** urządzenia są tworzone bez skojarzenia szablonu urządzenia. Urządzenia muszą być skojarzone z szablonem, aby eksplorować dane i inne szczegóły dotyczące urządzenia. Wykonaj następujące kroki, aby skojarzyć urządzenia z szablonem:

1. Wybierz **pozycję Urządzenia** w lewym okienku.

1. Na lewym panelu wybierz **pozycję Wszystkie urządzenia:**

    ![Urządzenia nieskoczył](./media/howto-manage-devices/unassociateddevices1a.png)


1. Użyj filtru w siatce, aby ustalić, czy wartość w kolumnie **Szablon urządzenia** jest "Nieskojęte" dla któregokolwiek z twoich urządzeń.

1. Wybierz urządzenia, które chcesz skojarzyć z szablonem:

1. Wybierz **opcję Migruj:**

    ![Skojarz urządzenia](./media/howto-manage-devices/unassociateddevices2a.png)


1. Wybierz szablon z listy dostępnych szablonów i wybierz pozycję **Migruj**.

1. Wybrane urządzenia są skojarzone z wybranym szablonem urządzenia.


## <a name="export-devices"></a>Eksportowanie urządzeń

Aby podłączyć prawdziwe urządzenie do usługi IoT Central, potrzebny jest jego ciąg połączenia. Można zbiorczo wyeksportować szczegóły urządzenia, aby uzyskać informacje potrzebne do utworzenia ciągów połączenia urządzenia. Proces eksportowania tworzy plik CSV z tożsamością urządzenia, nazwą urządzenia i kluczami dla wszystkich wybranych urządzeń.

Aby zbiorczo eksportować urządzenia z aplikacji:

1. Wybierz **pozycję Urządzenia** w lewym okienku.

1. W lewym okienku wybierz szablon urządzenia, z którego chcesz wyeksportować urządzenia.

1. Wybierz urządzenia, które chcesz wyeksportować, a następnie wybierz akcję **Eksportuj.**

    ![Eksportowanie](./media/howto-manage-devices/export1a.png)


1. Rozpocznie się proces eksportowania. Stan można śledzić za pomocą panelu Operacje urządzenia.

1. Po zakończeniu eksportu zostanie wyświetlony komunikat o sukcesie wraz z łączem umożliwiającym pobranie wygenerowanego pliku.

1. Wybierz łącze **Pobierz plik,** aby pobrać plik do folderu lokalnego na dysku.

    ![Sukces eksportu](./media/howto-manage-devices/export2a.png)


1. Eksportowany plik CSV zawiera następujące kolumny: identyfikator urządzenia, nazwa urządzenia, klucze urządzenia i odciski palców certyfikatu X509:

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

Aby uzyskać więcej informacji na temat ciągów połączeń i łączenia rzeczywistych urządzeń z aplikacją IoT Central, zobacz [Łączność urządzenia w usłudze Azure IoT Central](concepts-get-connected.md).

## <a name="delete-a-device"></a>Usuwanie urządzenia

Aby usunąć rzeczywiste lub symulowane urządzenie z aplikacji Azure IoT Central:

1. Wybierz **pozycję Urządzenia** w lewym okienku.

1. Wybierz szablon urządzenia, które chcesz usunąć.

1. Użyj narzędzi filtrowania, aby filtrować i wyszukiwać urządzenia. Zaznacz pole obok urządzeń do usunięcia.

1. Wybierz przycisk **Usuń**. Stan tego usunięcia można śledzić w panelu Operacje urządzenia.

## <a name="change-a-property"></a>Zmienianie właściwości

Właściwości chmury to metadane urządzenia skojarzone z urządzeniem, takie jak miasto i numer seryjny. Zapisywalne właściwości kontrolują zachowanie urządzenia. Innymi słowy umożliwiają one dostarczanie danych wejściowych do urządzenia.  Właściwości urządzenia są ustawiane przez urządzenie i są tylko do odczytu w IoT Central. Właściwości można wyświetlać i aktualizować w widokach **Szczegóły** urządzenia dla urządzenia.

1. Wybierz **pozycję Urządzenia** w lewym okienku.

1. Wybierz szablon urządzenia, którego właściwości chcesz zmienić, i wybierz urządzenie docelowe.

1. Wybierz widok zawierający właściwości urządzenia, ten widok umożliwia wprowadzanie wartości i wybierz **zapisz** u góry strony. W tym miejscu zobaczysz właściwości urządzenia i ich bieżące wartości. Właściwości chmury i właściwości zapisywalne mają edytowalne pola, podczas gdy właściwości urządzenia są tylko do odczytu. W przypadku właściwości zapisywalnych ich stan synchronizacji można zobaczyć u dołu pola. 

1. Zmodyfikuj właściwości do potrzebnych wartości. Można zmodyfikować wiele właściwości w czasie i zaktualizować je wszystkie w tym samym czasie.

1. Wybierz pozycję **Zapisz**. Jeśli zapisane właściwości zapisywalne, wartości są wysyłane do urządzenia. Gdy urządzenie potwierdzi zmianę dla właściwości zapisywalnej, stan powraca do **synchronizacji**. Jeśli użytkownik zapisał właściwość w chmurze, zostanie zaktualizowana.


## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak zarządzać urządzeniami w aplikacji Azure IoT Central, oto sugerowany następny krok:

> [!div class="nextstepaction"]
> [Jak korzystać z grup urządzeń](tutorial-use-device-groups.md)

<!-- Next how-tos in the sequence -->
