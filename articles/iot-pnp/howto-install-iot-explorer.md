---
title: Instalowanie i używanie eksploratora IoT platformy Azure | Dokumenty firmy Microsoft
description: Zainstaluj narzędzie Eksploratora IoT platformy Azure i użyj go do interakcji z urządzeniami IoT Plug and Play Preview połączonymi z moim centrum IoT hub.
author: miagdp
ms.author: miag
ms.date: 12/27/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 963421fa1ef06599448c9a4197f0d7a6ad2e142d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159187"
---
# <a name="install-and-use-azure-iot-explorer"></a>Instalowanie i używanie eksploratora IoT platformy Azure

Eksplorator IoT platformy Azure to graficzne narzędzie do interakcji z urządzeniami IoT Plug and Play Preview i testowania ich. Po zainstalowaniu narzędzia na komputerze lokalnym można go użyć do podłączenia do urządzenia. Za pomocą narzędzia można wyświetlić dane telemetryczne wysyłane przez urządzenie, pracować z właściwościami urządzenia i polecenia wywołania.

W tym artykule wyjaśniono, jak:

- Zainstaluj i skonfiguruj narzędzie eksploratora IoT platformy Azure.
- Użyj narzędzia do interakcji z urządzeniami i testowania.

## <a name="prerequisites"></a>Wymagania wstępne

Aby korzystać z narzędzia Eksploratora IoT platformy Azure, potrzebujesz:

- Centrum Usługi Azure IoT. Istnieje wiele sposobów dodawania centrum IoT hub do subskrypcji platformy Azure, takich jak [Tworzenie centrum IoT przy użyciu interfejsu wiersza polecenia platformy Azure.](../iot-hub/iot-hub-create-using-cli.md) Aby uruchomić narzędzie do eksplorowania IoT usługi Azure, potrzebujesz ciągu połączenia centrum IoT. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.
- Urządzenie zarejestrowane w centrum IoT Hub. Aby zarejestrować urządzenie, można użyć następującego polecenia interfejsu wiersza polecenia platformy Azure. Pamiętaj, aby `{YourIoTHubName}` zastąpić `{YourDeviceID}` symbole zastępcze wartościami:

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

## <a name="install-azure-iot-explorer"></a>Instalowanie eksploratora Usługi Azure IoT

Przejdź do [wersji eksploratora Usługi Azure IoT](https://github.com/Azure/azure-iot-explorer/releases) i rozwiń listę zasobów dla najnowszej wersji. Pobierz i zainstaluj najnowszą wersję aplikacji.

## <a name="use-azure-iot-explorer"></a>Korzystanie z Eksploratora IoT platformy Azure

W przypadku urządzenia możesz podłączyć własne urządzenie lub użyć jednego z naszych przykładowych symulowanych urządzeń. Postępuj zgodnie z [tymi instrukcjami,](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/iothub_client/samples) aby uruchomić próbkę symulowanego urządzenia.

### <a name="connect-to-your-hub"></a>Łączenie się z koncentratorem

Przy pierwszym uruchomieniu eksploratora Usługi Azure IoT zostanie wyświetlony monit o połączenie ciągu połączenia centrum IoT. Po dodaniu ciągu połączenia wybierz pozycję **Połącz**. Za pomocą ustawień narzędzia można przełączyć się do innego centrum IoT, aktualizując parametry połączenia.

Definicja modelu urządzenia IoT Plug and Play jest przechowywana w publicznym repozytorium, repozytorium firmowym lub podłączonym urządzeniu. Domyślnie narzędzie wyszukuje definicję modelu w publicznym repozytorium modelu i podłączonym urządzeniu. Można dodawać i usuwać źródła lub konfigurować priorytet źródeł w **ustawieniach:**

Aby dodać źródło:

1. Przejdź do obszaru **Settings** (Ustawienia).
1. Wybierz **pozycję Nowy** i wybierz źródło.
1. Jeśli dodajesz repozytorium modelu firmy, podaj parametry połączenia.

Aby usunąć źródło:

1. Przejdź do obszaru **Settings** (Ustawienia).
1. Znajdź źródło, które chcesz usunąć.
1. Wybierz **X,** aby go usunąć. Nie można usunąć repozytorium modelu publicznego, ponieważ typowe definicje interfejsu pochodzą z tego repozytorium.

Zmień priorytety źródłowe:

Można przeciągnąć i upuścić jedno ze źródeł definicji modelu do innego rankingu na liście. Jeśli występuje konflikt, źródła definicji z wyższymi rankingami zastępują źródła o niższych rankingach.

### <a name="view-devices"></a>Wyświetlanie urządzeń

Po połączeniu się narzędzia z centrum IoT hub, wyświetla **urządzenia** listy strony, która zawiera listę tożsamości urządzenia zarejestrowane w centrum IoT hub. Możesz rozwinąć dowolny wpis na liście, aby wyświetlić więcej informacji.

Na stronie listy **Urządzenia** możesz:

- Wybierz **pozycję Dodaj,** aby zarejestrować nowe urządzenie w centrum. Następnie wprowadź identyfikator urządzenia. Użyj ustawień domyślnych, aby automatycznie generować klucze uwierzytelniania i włączyć połączenie z koncentratorem.
- Wybierz urządzenie, a następnie wybierz pozycję **Usuń,** aby usunąć tożsamość urządzenia. Przejrzyj szczegóły urządzenia przed wykonaniem tej akcji, aby upewnić się, że usuwasz właściwą tożsamość urządzenia.
- Zapytanie `capabilityID` `interfaceID`według i . Dodaj swój `capabilityID` lub `interfaceID` jako parametr, aby zbadać swoje urządzenia.

## <a name="interact-with-a-device"></a>Interakcja z urządzeniem

Na stronie listy **Urządzenia** wybierz wartość w kolumnie **Identyfikator urządzenia,** aby wyświetlić stronę szczegółów zarejestrowanego urządzenia. Dla każdego urządzenia istnieją dwie sekcje: **Urządzenie** i **Digital Twin**.

### <a name="device"></a>Urządzenie

Ta sekcja zawiera **karty Tożsamość urządzenia,** **Bliźniacza bliźniacza urządzenia,** **Telemetria,** **Bezpośrednia metoda** i Wiadomości **z chmury do urządzenia.**

- Informacje o tożsamości [urządzenia](../iot-hub/iot-hub-devguide-identity-registry.md) można wyświetlać i aktualizować na karcie **Tożsamość urządzenia.**
- Informacje o [bliźniaczej reprezentacji urządzenia](../iot-hub/iot-hub-devguide-device-twins.md) można uzyskać na karcie **Bliźniacze urządzenie.**
- Jeśli urządzenie jest połączone i aktywnie wysyła dane, można wyświetlić [dane telemetryczne](../iot-hub/iot-hub-devguide-messages-read-builtin.md) na karcie **Telemetria.**
- Metodę [bezpośrednią](../iot-hub/iot-hub-devguide-direct-methods.md) można wywołać na urządzeniu na karcie **Metoda bezpośrednia.**
- Na karcie Wiadomości z chmury do **urządzenia** można wysłać komunikat z chmury do [urządzenia.](../iot-hub/iot-hub-devguide-messages-c2d.md)

### <a name="digital-twin"></a>Cyfrowy bliźniak

Za pomocą narzędzia można wyświetlić cyfrowe podwójne wystąpienie urządzenia. W przypadku urządzenia Typu Plug and Play IoT wszystkie interfejsy skojarzone z modelem możliwości urządzenia są wyświetlane w tej sekcji narzędzia. Wybierz interfejs, aby rozwinąć odpowiednie [elementy podstawowe IoT Plug and Play](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL).

### <a name="interface"></a>Interface

Na stronie **Interfejs** można wyświetlić definicję JSON interfejsu.

#### <a name="properties"></a>Właściwości

Można wyświetlić właściwości tylko do odczytu zdefiniowane w interfejsie na stronie **Właściwości nieupisalne.** Można zaktualizować zapisywalne właściwości zdefiniowane w interfejsie na stronie **Właściwości zapisywalne:**

1. Przejdź do strony **Właściwości zapisywalne.**
1. Kliknij właściwość, którą chcesz zaktualizować.
1. Wprowadź nową wartość właściwości.
1. Wyświetl podgląd ładunku, który ma zostać wysłany do urządzenia.
1. Prześlij zmianę.

Po przesłaniu zmiany można śledzić stan aktualizacji: **synchronizowanie,** **powodzenie**lub **błąd.** Po zakończeniu synchronizacji zostanie wyświetlna nowa wartość właściwości w kolumnie **Zgłoszona właściwość.** Jeśli przejdziesz do innych stron przed zakończeniem synchronizacji, narzędzie nadal powiadamia użytkownika po zakończeniu aktualizacji. Możesz również użyć centrum powiadomień narzędzia, aby wyświetlić historię powiadomień.

#### <a name="commands"></a>Polecenia

Aby wysłać polecenie do urządzenia, przejdź do strony **Polecenia:**

1. Na liście poleceń rozwiń polecenie, które chcesz wyzwolić.
1. Wprowadź wszystkie wymagane wartości dla polecenia.
1. Wyświetl podgląd ładunku, który ma zostać wysłany do urządzenia.
1. Prześlij polecenie.

#### <a name="telemetry"></a>Telemetria

Aby wyświetlić dane telemetryczne dla wybranego interfejsu, przejdź do jego strony **Telemetria.**

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano, jak zainstalować i używać eksploratora Usługi Azure IoT do interakcji z urządzeniami Typu Plug and Play usługi IoT. Sugerowanym następnym krokiem jest dowiedzenie się, jak [zainstalować i używać rozszerzenia interfejsu wiersza polecenia platformy Azure.](./howto-install-pnp-cli.md)
