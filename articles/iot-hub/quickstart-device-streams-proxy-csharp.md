---
title: Urządzenie usługi Azure IoT Hub strumieniuje przewodnika Szybki start języka C# dla usług SSH i RDP
description: W tym przewodniku Szybki start uruchomisz dwie przykładowe aplikacje języka C#, które umożliwiają scenariusze SSH i RDP za pomocą strumienia urządzeń usługi IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 97551ac63066f7064c16a001d9ce1f6bc31465ec
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586583"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Szybki start: włączanie protokołu SSH i protokołu RDP za pośrednictwem strumienia urządzeń usługi IoT Hub przy użyciu aplikacji proxy języka C# (wersja zapoznawcza)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Usługa Microsoft Azure IoT Hub obsługuje obecnie strumienie urządzeń jako [funkcję w wersji zapoznawczej.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

[Strumienie urządzeń usługi IoT Hub](iot-hub-device-streams-overview.md) umożliwiają aplikacjom usług i urządzeń bezpieczną komunikację w sposób przyjazny dla zapory. Ten przewodnik szybki start obejmuje dwie aplikacje języka C#, które umożliwiają ruch aplikacji klient-serwer (takie jak Secure Shell [SSH] i Remote Desktop Protocol [RDP] do wysyłania za pośrednictwem strumienia urządzenia, który jest ustanawiany za pośrednictwem centrum IoT hub. Aby uzyskać omówienie konfiguracji, zobacz [Przykładowa aplikacja lokalnego serwera proxy dla protokołu SSH lub RDP](iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

W tym artykule opisano najpierw konfigurację protokołu SSH (przy użyciu portu 22), a następnie opisano sposób modyfikowania portu konfiguracji dla protokołu RDP. Ponieważ strumienie urządzeń są niezależną od aplikacji i protokołu, ten sam przykład można zmodyfikować w celu uwzględnienia innych typów ruchu aplikacji. Ta modyfikacja zwykle obejmuje tylko zmianę portu komunikacyjnego na ten, który jest używany przez zamierzoną aplikację.

## <a name="how-it-works"></a>Jak to działa

Na poniższym rysunku przedstawiono, jak aplikacje serwera proxy device-local i service-local w tym przykładzie umożliwiają łączność end-to-end między klientem SSH a procesami demona SSH. W tym miejscu zakładamy, że demon działa na tym samym urządzeniu co aplikacja serwera proxy device-local.

![Konfiguracja aplikacji lokalnego serwera proxy](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.png)

1. Aplikacja serwera proxy lokalnego usługi łączy się z centrum IoT hub i inicjuje strumień urządzenia do urządzenia docelowego.

1. Aplikacja serwera proxy lokalnego urządzenia kończy uzgadnianie inicjowania zainicjowania strumienia i ustanawia tunel przesyłania strumieniowego end-to-end przez punkt końcowy przesyłania strumieniowego usługi IoT hub do strony usługi.

1. Aplikacja serwera proxy lokalnego urządzenia łączy się z demonem SSH, który nasłuchuje na porcie 22 na urządzeniu. To ustawienie można konfigurować, zgodnie z opisem w sekcji "Uruchom aplikację serwera proxy lokalnego urządzenia".

1. Aplikacja serwera proxy lokalnego usługi czeka na nowe połączenia SSH od użytkownika, nasłuchiwanie na wyznaczonym porcie, który w tym przypadku jest port 2222. To ustawienie można konfigurować, zgodnie z opisem w sekcji "Uruchom aplikację serwera proxy lokalnego usługi". Gdy użytkownik łączy się za pośrednictwem klienta SSH, tunel umożliwia ruch aplikacji SSH mają być przesyłane między klientem SSH i aplikacji serwera.

> [!NOTE]
> Ruch SSH, który jest wysyłany za pośrednictwem strumienia urządzenia jest tunelowany przez punkt końcowy przesyłania strumieniowego usługi IoT hub, a nie wysyłany bezpośrednio między usługą a urządzeniem. Aby uzyskać więcej informacji, zobacz [zalety korzystania ze strumieni urządzeń usługi Iot Hub](iot-hub-device-streams-overview.md#benefits).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

* Podgląd strumieni urządzeń jest obecnie obsługiwany tylko dla centrów IoT, które są tworzone w następujących regionach:

  * Środkowe stany USA
  * Centralna amerykańska EUAP
  * Azja Południowo-Wschodnia
  * Europa Północna

* Dwie przykładowe aplikacje uruchomione w tym przewodniku Szybki start są zapisywane w języku C#. Potrzebujesz .NET Core SDK 2.1.0 lub nowszej na komputerze deweloperskim.

  Zestaw [.NET Core SDK dla wielu platform](https://www.microsoft.com/net/download/all)można pobrać z platformy .NET .

* Sprawdź bieżącą wersję języka C# na komputerze deweloperskim za pomocą następującego polecenia:

    ```
    dotnet --version
    ```

* Uruchom następujące polecenie, aby dodać rozszerzenie Usługi Azure IoT dla interfejsu wiersza polecenia platformy Azure do wystąpienia usługi Cloud Shell. Rozszerzenie IOT dodaje IoT Hub, IoT Edge i IoT Device Provisioning Service (DPS) polecenia specyficzne dla interfejsu wiersza polecenia platformy Azure.

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

   ```azurecli-interactive
   az extension add --name azure-iot
   ```
[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

* [Pobierz przykłady usługi Azure IoT C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)i wyodrębnij archiwum ZIP.

* Prawidłowe konto użytkownika i poświadczenia na urządzeniu (Windows lub Linux) używane do uwierzytelniania użytkownika.

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tym przewodniku Szybki start można użyć usługi Azure Cloud Shell do zarejestrowania symulowanego urządzenia.

1. Aby utworzyć tożsamość urządzenia, uruchom następujące polecenie w usłudze Cloud Shell:

   > [!NOTE]
   > * Zastąp symbol zastępczy *YourIoTHubName* nazwą wybraną dla centrum IoT.
   > * W przypadku nazwy zarejestrowanego urządzenia zaleca się użycie urządzenia *MyDevice* w sposób pokazany na rysunku. Jeśli wybierzesz inną nazwę urządzenia, użyj tej nazwy w tym artykule i zaktualizuj nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Aby uzyskać *parametry połączenia urządzenia* dla właśnie zarejestrowanego urządzenia, uruchom następujące polecenia w usłudze Cloud Shell:

   > [!NOTE]
   > Zastąp symbol zastępczy *YourIoTHubName* nazwą wybraną dla centrum IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Zanotuj zwrócony ciąg połączenia urządzenia do późniejszego użycia w tym przewodniku Szybki start. Wygląda to następująco:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

1. Aby połączyć się z centrum IoT hub i ustanowić strumień urządzenia, potrzebujesz również *ciągu połączenia usługi* z centrum IoT hub, aby włączyć aplikację po stronie usługi. Następujące polecenie umożliwia pobranie tej wartości dla centrum IoT:

   > [!NOTE]
   > Zastąp symbol zastępczy *YourIoTHubName* nazwą wybraną dla centrum IoT.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Zanotuj zwrócony ciąg połączenia usługi do późniejszego użycia w tym przewodniku Szybki start. Wygląda to następująco:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>Połączenie SSH z urządzeniem za pośrednictwem strumieni urządzeń

W tej sekcji ustanawiasz strumień end-to-end do tunelu ruchu SSH.

### <a name="run-the-device-local-proxy-application"></a>Uruchamianie aplikacji serwera proxy urządzenia lokalnego

W oknie terminala lokalnego `device-streams-proxy/device` przejdź do katalogu w rozpakowanym folderze projektu. Podtrzymuj następujące informacje:

| Nazwa argumentu | Wartość argumentu |
|----------------|-----------------|
| `DeviceConnectionString` | Ciąg połączenia urządzenia urządzenia utworzonego wcześniej. |
| `targetServiceHostName` | Adres IP, na który nasłuchuje serwer SSH. Adres byłby, `localhost` gdyby był to ten sam adres IP, w którym jest uruchomiona aplikacja serwera proxy lokalnego urządzenia. |
| `targetServicePort` | Port, który jest używany przez protokół aplikacji (dla SSH, domyślnie, będzie to port 22).  |

Skompiluj i uruchom kod za pomocą następujących poleceń:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run ${DeviceConnectionString} localhost 22

# In Windows
dotnet run {DeviceConnectionString} localhost 22
```

### <a name="run-the-service-local-proxy-application"></a>Uruchamianie aplikacji serwera proxy usługi lokalnej

W innym oknie terminala `iot-hub/quickstarts/device-streams-proxy/service` lokalnego przejdź do folderu projektu rozpakowanego. Podtrzymuj następujące informacje:

| Nazwa parametru | Wartość parametru |
|----------------|-----------------|
| `ServiceConnectionString` | Parametry połączenia usługi IoT Hub. |
| `MyDevice` | Utworzony wcześniej identyfikator urządzenia. |
| `localPortNumber` | Port lokalny, z który klient SSH połączy się. Używamy portu 2222 w tym przykładzie, ale można użyć innych dowolnych liczb. |

Skompiluj i uruchom kod za pomocą następujących poleceń:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run ${ServiceConnectionString} MyDevice 2222

# In Windows
dotnet run {ServiceConnectionString} MyDevice 2222
```

### <a name="run-the-ssh-client"></a>Uruchamianie klienta SSH

Teraz użyj aplikacji klienckiej SSH i połącz się z lokalną aplikacją proxy usługi na porcie 2222 (zamiast bezpośrednio demona SSH).

```
ssh {username}@localhost -p 2222
```

W tym momencie okno logowania SSH monituje o wprowadzenie poświadczeń.

Wyjście konsoli po stronie usługi (aplikacja serwera proxy lokalnego usługi nasłuchuje na porcie 2222):

![Dane wyjściowe aplikacji serwera proxy lokalne usługi](./media/quickstart-device-streams-proxy-csharp/service-console-output.png)

Wyjście konsoli w aplikacji proxy device-local, która łączy się z demonem SSH o *godzinie IP_address:22:*

![Dane wyjściowe aplikacji proxy lokalne dla urządzeń](./media/quickstart-device-streams-proxy-csharp/device-console-output.png)

Wyjście konsoli aplikacji klienckiej SSH. Klient SSH komunikuje się z demonem SSH, łącząc się z portem 22, na który nasłuchuje lokalna aplikacja proxy usługi:

![Dane wyjściowe aplikacji klienckiej SSH](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="rdp-to-a-device-via-device-streams"></a>Połączenie RDP z urządzeniem za pośrednictwem strumieni urządzeń

Konfiguracja protokołu RDP jest podobna do konfiguracji dla protokołu SSH (opisanego powyżej). Zamiast tego należy użyć docelowego adresu IP protokołu RDP i portu 3389 i użyć klienta RDP (zamiast klienta SSH).

### <a name="run-the-device-local-proxy-application-rdp"></a>Uruchamianie aplikacji proxy lokalnego urządzenia (RDP)

W oknie terminala lokalnego `device-streams-proxy/device` przejdź do katalogu w rozpakowanym folderze projektu. Podtrzymuj następujące informacje:

| Nazwa argumentu | Wartość argumentu |
|----------------|-----------------|
| `DeviceConnectionString` | Ciąg połączenia urządzenia urządzenia utworzonego wcześniej. |
| `targetServiceHostName` | Nazwa hosta lub adres IP, na którym działa serwer RDP. Adres byłby, `localhost` gdyby był to ten sam adres IP, w którym jest uruchomiona aplikacja serwera proxy lokalnego urządzenia. |
| `targetServicePort` | Port używany przez protokół aplikacji (dla protokołu RDP domyślnie będzie to port 3389).  |

Skompiluj i uruchom kod za pomocą następujących poleceń:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device

# Run the application
# In Linux or macOS
dotnet run ${DeviceConnectionString} localhost 3389

# In Windows
dotnet run {DeviceConnectionString} localhost 3389
```

### <a name="run-the-service-local-proxy-application-rdp"></a>Uruchamianie aplikacji serwera proxy lokalnego usługi (RDP)

W innym oknie terminala `device-streams-proxy/service` lokalnego przejdź do folderu projektu rozpakowanego. Podtrzymuj następujące informacje:

| Nazwa parametru | Wartość parametru |
|----------------|-----------------|
| `ServiceConnectionString` | Parametry połączenia usługi IoT Hub. |
| `MyDevice` | Utworzony wcześniej identyfikator urządzenia. |
| `localPortNumber` | Port lokalny, z który klient SSH połączy się. W tym przykładzie użyto portu 2222, ale można go zmienić na inny. |

Skompiluj i uruchom kod za pomocą następujących poleceń:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run ${ServiceConnectionString} MyDevice 2222

# In Windows
dotnet run {ServiceConnectionString} MyDevice 2222
```

### <a name="run-rdp-client"></a>Uruchamianie klienta RDP

Teraz użyj aplikacji klienckiej RDP i połącz się z aplikacją serwera proxy lokalnego usługi na porcie 2222 (był to dowolny dostępny port, który został wybrany wcześniej).

![Prow łączy się z aplikacją serwera proxy lokalnego usługi](./media/quickstart-device-streams-proxy-csharp/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start skonfigurowano centrum IoT hub, zarejestrowano urządzenie, wdrożone aplikacje serwera proxy device-local i service-local w celu ustanowienia strumienia urządzeń za pośrednictwem centrum IoT hub i użyto aplikacji proxy do tunelowania ruchu SSH lub RDP. Ten sam paradygmat może pomieścić inne protokoły klient-serwer, gdzie serwer działa na urządzeniu (na przykład demon SSH).

Aby dowiedzieć się więcej o strumieniach urządzeń, zobacz:

> [!div class="nextstepaction"]
> [Omówienie strumieni urządzeń](./iot-hub-device-streams-overview.md)
