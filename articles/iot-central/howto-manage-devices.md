---
title: Zarządzanie urządzeniami w Twojej aplikacji usługi Azure IoT Central | Dokumentacja firmy Microsoft
description: Operator Dowiedz się, jak zarządzać urządzeniami w usłudze Azure IoT Central aplikacji.
author: ellenfosborne
ms.author: elfarber
ms.date: 11/02/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: dc241612149de5c4ea5c1d2e698741e77d429fc7
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2018
ms.locfileid: "51004888"
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

1. Wybierz szablon urządzenia w obszarze Szablony w okienku po lewej stronie.

1. W okienku po prawej stronie strony Device Explorer zobaczysz listę urządzeń utworzone na podstawie tego szablonu urządzenia, jak pokazano poniżej. Wybierz poszczególnych urządzeń, aby wyświetlić strony szczegółów urządzenia dla tego urządzenia:

    [![Strony szczegółów urządzenia](./media/howto-manage-devices/image1.png)](./media/howto-manage-devices/image1.png#lightbox)

## <a name="add-a-device"></a>Dodawanie urządzenia

Aby dodać urządzenie do usługi Azure IoT Central aplikacji:

1. Wybierz **Device Explorer** w menu nawigacji po lewej stronie.

1. Wybierz szablon urządzenia, z którego chcesz utworzyć urządzenia.

1. Wybierz + **nowe**.

1. Wybierz **rzeczywistych** lub **symulowane**. Rzeczywiste urządzenie jest w przypadku urządzenia fizycznego, w której się łączysz się z aplikacją usługi Azure IoT Central. Symulowane urządzenia znajdują się przykładowe dane wygenerowane automatycznie przez usługi Azure IoT Central. W tym przykładzie użyto rzeczywistego urządzenia. Wybierz **rzeczywistych** można przejść do **szczegóły urządzenia** strona nowe urządzenie.


## <a name="import-devices"></a>Importuj urządzenia

Do łączenia z dużą liczbą urządzeń do aplikacji usługi Azure IoT Central oferty zbiorczo importowania urządzeń przy użyciu pliku CSV. Ten plik CSV powinien zawierać następujące kolumny (i nagłówki)
1.  IOTC_DeviceID  **<span style="color:Red">(powinno wskazywać na małe litery)</span>**
1.  IOTC_DeviceName (opcjonalnie)


Aby zbiorczo — rejestracja urządzeń w Twojej aplikacji:

1. Wybierz **Device Explorer** w menu nawigacji po lewej stronie.

1. W lewym panelu wybierz szablon urządzenia, dla którego chcesz zbiorczo utworzyć urządzenia.

 >   [!NOTE] 
    Jeśli nie masz szablon urządzenia jeszcze zaimportowanie urządzeń w obszarze **Brak skojarzenia urządzenia** i zarejestruj je bez żadnych szablonów. Po zaimportowaniu urządzenia można następnie skojarzyć je przy użyciu szablonu jako kolejny krok.

1. Kliknij przycisk **Importuj**.

    [![Akcja importu](./media/howto-manage-devices/BulkImport1.png)](./media/howto-manage-devices/BulkImport1.png#lightbox)

1. Wybierz plik CSV, który zawiera listę identyfikatorów urządzeń do zaimportowania.

1. Importuj urządzenia rozpoczyna się po przekazaniu pliku. Można śledzić stan importu w górnej części siatki urządzenia.

1. Po zakończeniu importowania w siatce urządzeń wyświetlany jest komunikat o powodzeniu.

    [![Powodzenie importu](./media/howto-manage-devices/BulkImport3.png)](./media/howto-manage-devices/BulkImport3.png#lightbox)

Jeśli urządzenie zaimportowany operacja kończy się niepowodzeniem, zobaczysz komunikat o błędzie na siatce urządzenia. Plik dziennika przechwytywania wszystkich błędów jest generowany i można je pobrać, klikając komunikat o błędzie.


**Kojarzenie urządzeń przy użyciu szablonu**

Po zarejestrowaniu urządzenia przez uruchamianie importu w obszarze **Brak skojarzenia urządzenia**, a następnie urządzenia są tworzone bez skojarzenia szablonu dowolnego urządzenia. Urządzenie musi być skojarzony z szablonem, aby eksplorować dane oraz inne szczegóły dotyczące danego urządzenia. Wykonaj następujące kroki, aby skojarzyć urządzenia z szablonem:
1. Wybierz **Device Explorer** w menu nawigacji po lewej stronie.
1. W panelu po lewej stronie wybierz **Brak skojarzenia urządzenia**.
    [![Urządzenia nieskojarzone](./media/howto-manage-devices/UnassociatedDevices1.png)](./media/howto-manage-devices/UnassociatedDevices1.png#lightbox)
1. Wybierz urządzenia, które chcesz skojarzyć z szablonem.
1. Kliknij przycisk **skojarzyć** opcji.
    [![Skojarzenie urządzenia](./media/howto-manage-devices/UnassociatedDevices2.png)](./media/howto-manage-devices/UnassociatedDevices2.png#lightbox)
1. Wybierz szablon z listy dostępnych szablonów, a następnie kliknij przycisk **skojarzyć** przycisku.
1. Wybrane urządzenia zostaną przeniesione na podstawie szablonu odpowiednie urządzenie.

 >   [!NOTE] 
    Gdy urządzenie znajdowało skojarzony z szablonem, nie może być Brak skojarzenia ani skojarzony z innego szablonu.

## <a name="export-devices"></a>Eksportowanie urządzeń

Aby zainicjować obsługę urządzeniom na łączenie z IoT Central, konieczne będzie parametry połączenia urządzenia, który jest generowany przez IoT Central. Funkcja eksportu można pobrać parametry połączenia i inne właściwości urządzeń zbiorczo z poziomu aplikacji. Eksportu plik CSV jest tworzona przy użyciu tożsamości urządzenia, nazwę urządzenia i podstawowe parametry połączenia dla wszystkich wybranych urządzeń.

Aby zbiorczo eksportu urządzenia z poziomu aplikacji:
1. Wybierz **Device Explorer** w menu nawigacji po lewej stronie.

1. Na lewym panelu wybierz szablon urządzenia, dla którego chcesz wyeksportować urządzenia.

1. Wybierz urządzenia, które chcesz wyeksportować, a następnie kliknij przycisk **wyeksportować** akcji.

    [![Eksportuj](./media/howto-manage-devices/Export1.png)](./media/howto-manage-devices/Export1.png#lightbox)

1. Rozpocznie się proces eksportu i można śledzić stan u góry strony siatki. 

1. Po zakończeniu eksportu, komunikat o powodzeniu jest wyświetlany wraz z linkiem do pobrania wygenerowany plik.

1. Kliknij pozycję **komunikat o powodzeniu** można pobrać pliku do lokalnego folderu na dysku.

    [![Powodzenie eksportu](./media/howto-manage-devices/Export2.png)](./media/howto-manage-devices/Export2.png#lightbox)

1. Wyeksportowany plik CSV będzie zawierał następujące informacje kolumn: **identyfikator urządzenia, nazwę urządzenia, klucze Priamry/pomocniczy urządzeń i podstawowy/pomocniczy certyfikat thumbrpints**
    *   IOTC_DEVICEID
    *   IOTC_DEVICENAME
    *   IOTC_SASKEY_PRIMARY
    *   IOTC_SASKEY_SECONDARY
    *   IOTC_X509THUMBPRINT_PRIMARY 
    *   IOTC_X509THUMBPRINT_SECONDARY

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

1. Zmodyfikuj ustawienia, aby Twoje odpowiednie wartości. Można zmodyfikować jednocześnie wiele ustawień i zaktualizować je wszystkie w tym samym czasie.

1. Wybierz **aktualizacji**. Wartości są wysyłane do urządzenia. Gdy urządzenie potwierdza zmiany ustawień, ustawienie powraca do **synchronizowane**.

## <a name="change-a-property"></a>Zmień właściwości

Właściwości są metadane urządzenia skojarzone z urządzenia, takie jak miejscowość i numer seryjny. Można wyświetlać i aktualizować właściwości na **szczegóły urządzenia** strony.

1. Wybierz **Device Explorer** w menu nawigacji.

1. Wybierz szablon urządzeń, urządzenia, którego właściwości chcesz zmienić.

1. Wybierz **właściwości** karty, w którym zostaną wyświetlone wszystkie właściwości.

1. Zmodyfikuj właściwości aplikacji, aby Twoje odpowiednie wartości. Można zmodyfikować jednocześnie wiele właściwości atrybutu i zaktualizować je wszystkie w tym samym czasie. Wybierz **aktualizacji**.

> [!NOTE]
> Nie można zmienić wartość _właściwości urządzenia_. Właściwości są ustawiane przez urządzenia i urządzenia są tylko do odczytu w ramach aplikacji usługi Azure IoT Central.

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak zarządzać urządzeniami w usłudze Azure IoT Central aplikacji, poniżej przedstawiono sugerowany następnego kroku:

> [!div class="nextstepaction"]
> [Jak użyć zestawów urządzenia](howto-use-device-sets.md)

<!-- Next how-tos in the sequence -->
