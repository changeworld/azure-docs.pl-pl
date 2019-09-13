---
title: Instalowanie i używanie programu Azure IoT Explorer | Microsoft Docs
description: Zainstaluj narzędzie Azure IoT Explorer i użyj go do współpracy z urządzeniami z programem IoT Plug and Play w wersji zapoznawczej podłączonym do centrum IoT Hub.
author: miagdp
ms.author: miag
ms.date: 07/02/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 4e23a440f46b52633a88d0212e08c7b584f61a38
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70932476"
---
# <a name="install-and-use-azure-iot-explorer"></a>Instalowanie i korzystanie z programu Azure IoT Explorer

Program Azure IoT Explorer jest graficznym narzędziem do współdziałania z urządzeniami w wersji zapoznawczej IoT Plug and Play i testowania. Po zainstalowaniu narzędzia na komputerze lokalnym można używać go do nawiązywania połączenia z urządzeniem. Możesz użyć narzędzia, aby wyświetlić dane telemetryczne wysyłane przez urządzenie, współpracować z właściwościami urządzenia i wywoływać polecenia.

W tym artykule pokazano, jak:

- Zainstaluj i skonfiguruj narzędzie Azure IoT Explorer.
- Użyj narzędzia, aby korzystać z i testować urządzenia.

## <a name="prerequisites"></a>Wymagania wstępne

Aby korzystać z narzędzia Azure IoT Explorer, potrzebne są:

- Usługa Azure IoT Hub. Istnieje wiele sposobów dodawania usługi IoT Hub do subskrypcji platformy Azure, takich jak [Tworzenie Centrum IoT Hub przy użyciu interfejsu wiersza polecenia platformy Azure](../iot-hub/iot-hub-create-using-cli.md). Do uruchomienia narzędzia Azure IoT Explorer potrzebne są parametry połączenia z usługą IoT Hub. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Urządzenie zarejestrowane w usłudze IoT Hub. Aby zarejestrować urządzenie, można użyć poniższego polecenia interfejsu CLI platformy Azure. Pamiętaj, aby zastąpić `{YourIoTHubName}` symbole zastępcze i `{YourDeviceID}` wartościami:

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

## <a name="install-azure-iot-explorer"></a>Instalowanie programu Azure IoT Explorer

Przejdź do [wersji programu Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases) i rozwiń listę zasobów dla najnowszej wersji. Pobierz i zainstaluj najnowszą wersję aplikacji.

## <a name="use-azure-iot-explorer"></a>Korzystanie z programu Azure IoT Explorer

W przypadku urządzenia można podłączyć własne urządzenie lub użyć jednego z przykładowych symulowanych urządzeń. Wykonaj [te instrukcje](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/iothub_client/samples) , aby uruchomić przykład symulowanego urządzenia.

### <a name="connect-to-your-hub"></a>Nawiązywanie połączenia z centrum

Przy pierwszym uruchomieniu programu Azure IoT Explorer zostanie wyświetlony monit o podanie parametrów połączenia Centrum IoT. Po dodaniu parametrów połączenia wybierz pozycję **Połącz**. Możesz użyć ustawień narzędzia, aby przełączyć się do innego Centrum IoT Hub przez zaktualizowanie parametrów połączenia.

Definicje modeli dla urządzenia IoT Plug and Play są przechowywane w repozytorium publicznym, w repozytorium firmy lub na podłączonym urządzeniu. Domyślnie narzędzie szuka definicji modelu w repozytorium modelu publicznego i podłączonym urządzeniu. Możesz dodawać i usuwać źródła albo konfigurować priorytety źródeł w **ustawieniach**:

Aby dodać Źródło:

1. Przejdź do **ustawienia**.
1. Wybierz pozycję **Nowy** i wybierz źródło.
1. W przypadku dodawania repozytorium modelu firmy podaj parametry połączenia.

Aby usunąć Źródło:

1. Przejdź do **ustawienia**.
1. Znajdź źródło, które chcesz usunąć.
1. Wybierz pozycję **X** , aby ją usunąć. Nie można usunąć repozytorium modelu publicznego, ponieważ wspólne definicje interfejsu pochodzą z tego repozytorium.

Zmień priorytety źródłowe:

Możesz przeciągać i upuszczać jeden ze źródeł definicji modelu do innej klasyfikacji na liście. Jeśli występuje konflikt, źródła definicji o wyższych klasyfikacjach przesłaniają źródła z niższą klasyfikacją.

### <a name="overview-page"></a>Strona przeglądu

#### <a name="device-overview"></a>Przegląd urządzenia

Po połączeniu narzędzia z Centrum IoT zostanie wyświetlona strona przegląd zawierająca listę wszystkich tożsamości urządzeń zarejestrowanych w usłudze Azure IoT Hub. Wybierz urządzenie, aby wyświetlić więcej szczegółów.

#### <a name="device-management"></a>Zarządzanie urządzeniami

- Aby zarejestrować nowe urządzenie w centrum, wybierz pozycję **Dodaj**. Wprowadź identyfikator urządzenia. Użyj ustawień domyślnych, aby automatycznie generować klucze uwierzytelniania i włączyć połączenie z centrum.
- Aby usunąć tożsamość urządzenia, wybierz pozycję **Usuń**. Przed ukończeniem tej akcji Przejrzyj szczegóły urządzenia, aby upewnić się, że usuwasz właściwą tożsamość urządzenia.
- Narzędzie obsługuje zapytania w `capabilityID` i. `interfaceID` Dodaj swój `capabilityID` lub `interfaceID` jako parametr, aby wykonać zapytanie dotyczące urządzeń.

## <a name="interact-with-a-device"></a>Korzystanie z urządzenia

Kliknij dwukrotnie urządzenie na stronie Przegląd, aby wyświetlić następny poziom szczegółowości. Istnieją dwie sekcje: **Urządzenia** i **sznurki cyfrowe**.

### <a name="device"></a>Urządzenie

Ta sekcja zawiera karty **tożsamość urządzenia**, dane **telemetryczne**i **sznurki urządzenia** .

- Informacje o tożsamości urządzenia można wyświetlić i zaktualizować na karcie **tożsamość urządzenia** .
- Jeśli urządzenie jest połączone i aktywnie wysyła dane, można je wyświetlić na karcie **telemetrii** .
- Dostęp do informacji o bliźniaczych urządzeniach można uzyskać na karcie **sznurki urządzenia** .

### <a name="digital-twin"></a>Cyfrowe sznurki

Możesz użyć narzędzia, aby wyświetlić wystąpienie cyfrowego przędzenia urządzenia. W tym artykule zostaną wyświetlone wszystkie interfejsy skojarzone z modelem możliwości urządzenia Plug and Play IoT. Wybierz interfejs, aby rozwinąć odpowiednie elementy podstawowe [Plug and Play IoT](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL).

#### <a name="properties"></a>Właściwości

Właściwości tylko do odczytu zdefiniowane w interfejsie można wyświetlić na stronie **Właściwości** . Można aktualizować właściwości do zapisu zdefiniowane w interfejsie na stronie właściwości do **zapisu** .

1. Przejdź do strony **właściwości z możliwością zapisu** .
1. Kliknij właściwość, którą chcesz zaktualizować.
1. Wprowadź nową wartość właściwości.
1. Wyświetlenie podglądu ładunku do wysłania na urządzenie.
1. Prześlij zmianę.

Po przesłaniu zmiany można śledzić stan aktualizacji: **Synchronizacja**, **sukces**lub **błąd**. Po zakończeniu synchronizacji zostanie wyświetlona nowa wartość właściwości w kolumnie **raportowana Właściwość** . Jeśli przejdziesz do innych stron przed ukończeniem synchronizacji, narzędzie będzie nadal powiadamiane po zakończeniu aktualizacji. Możesz również użyć Centrum powiadomień narzędzia, aby wyświetlić historię powiadomień.

#### <a name="commands"></a>Polecenia

Aby wysłać polecenie do urządzenia, przejdź do strony **poleceń** :

1. Na liście poleceń Rozwiń polecenie, które ma zostać wyzwolone.
1. Wprowadź wszelkie wymagane wartości dla polecenia.
1. Wyświetlenie podglądu ładunku do wysłania na urządzenie.
1. Prześlij polecenie.

#### <a name="telemetry"></a>Telemetria

Aby wyświetlić dane telemetryczne dla wybranego interfejsu, przejdź do strony **telemetrii** .

## <a name="next-steps"></a>Następne kroki

W tym artykule poznasz sposób instalowania i używania programu Azure IoT Explorer do współpracy z urządzeniami Plug and Play IoT. Sugerowanym następnym krokiem jest zapoznanie się z tematem [Instalowanie i używanie rozszerzenia interfejsu wiersza polecenia platformy Azure](./howto-install-pnp-cli.md).
