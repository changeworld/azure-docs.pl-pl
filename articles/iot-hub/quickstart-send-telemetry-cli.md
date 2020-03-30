---
title: Wysyłanie danych telemetrycznych do szybkiego startu usługi Azure IoT Hub (CLI)
description: Ten przewodnik Szybki start pokazuje deweloperom nowi w Centrum IoT, jak rozpocząć pracę przy użyciu interfejsu wiersza polecenia platformy Azure do tworzenia centrum IoT hub, wysyłania danych telemetrycznych i wyświetlania komunikatów między urządzeniem a koncentratorem.
ms.service: iot-hub
ms.topic: quickstart
ms.custom:
- iot-send-telemetry-cli
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 11/06/2019
ms.openlocfilehash: 711e15986265324bbb353fb2b4404cbfeb48dc84
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78851426"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-monitor-it-with-the-azure-cli"></a>Szybki start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT i monitorowanie ich za pomocą interfejsu wiersza polecenia platformy Azure

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub to usługa platformy Azure, która umożliwia pozyskiwanie dużych ilości danych telemetrycznych z urządzeń IoT do chmury w celu magazynowania lub przetwarzania. W tym przewodniku Szybki start można użyć interfejsu wiersza polecenia platformy Azure, aby utworzyć centrum IoT Hub i symulowane urządzenie, wysłać dane telemetryczne urządzenia do centrum i wysłać komunikat z chmury do urządzenia. Witryna Azure Portal służy również do wizualizacji metryk urządzenia. Jest to podstawowy przepływ pracy dla deweloperów, którzy używają interfejsu wiersza polecenia do interakcji z aplikacją IoT Hub.

## <a name="prerequisites"></a>Wymagania wstępne
- Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatną subskrypcję](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.
- Interfejsu wiersza polecenia platformy Azure. Wszystkie polecenia można uruchomić w tym przewodniku Szybki start przy użyciu usługi Azure Cloud Shell, interaktywnej powłoki interfejsu wiersza polecenia, która jest uruchamiana w przeglądarce. Jeśli używasz Cloud Shell, nie musisz niczego instalować. Jeśli wolisz używać interfejsu wiersza polecenia lokalnie, ten szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.76 lub nowszej. Aby odnaleźć wersję, uruchom polecenie az --version. Aby zainstalować lub uaktualnić, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal
Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

Niezależnie od tego, czy cli jest uruchamiany lokalnie, czy w usłudze Cloud Shell, należy zachować otwarty portal w przeglądarce.  Można go użyć w dalszej części tego przewodnika Szybki start.

## <a name="launch-the-cloud-shell"></a>Uruchom powłokę chmury
W tej sekcji można uruchomić wystąpienie usługi Azure Cloud Shell. Jeśli używasz interfejsu wiersza polecenia lokalnie, przejdź do sekcji [Przygotowywanie dwóch sesji interfejsu wiersza polecenia](#prepare-two-cli-sessions).

Aby uruchomić powłokę Cloud Shell:

1. Wybierz przycisk **Powłoka chmury** na pasku menu w prawym górnym rogu w witrynie Azure portal. 

    ![Przycisk Powłoki chmury portalu Azure](media/quickstart-send-telemetry-cli/cloud-shell-button.png)

    > [!NOTE]
    > Jeśli jest to pierwszy raz, gdy używasz powłoki chmury, monituje o utworzenie magazynu, który jest wymagany do korzystania z powłoki chmury.  Wybierz subskrypcję, aby utworzyć konto magazynu i udział plików Microsoft Azure. 

2. Wybierz preferowane środowisko interfejsu wiersza polecenia w menu rozwijanym **Wybierz środowisko.** Ten szybki start używa środowiska **Bash.** Wszystkie następujące polecenia interfejsu wiersza polecenia działają również w środowisku programu Powershell. 

    ![Wybierz środowisko interfejsu wiersza polecenia](media/quickstart-send-telemetry-cli/cloud-shell-environment.png)

## <a name="prepare-two-cli-sessions"></a>Przygotowanie dwóch sesji interfejsu wiersza polecenia

W tej sekcji należy przygotować dwie sesje interfejsu wiersza polecenia platformy Azure. Jeśli używasz usługi Cloud Shell, uruchomisz dwie sesje w oddzielnych kartach przeglądarki. W przypadku korzystania z lokalnego klienta interfejsu wiersza polecenia, można uruchomić dwa oddzielne wystąpienia interfejsu wiersza polecenia. Pierwsza sesja będzie używana jako symulowane urządzenie, a druga do monitorowania i wysyłania wiadomości. Aby uruchomić polecenie, wybierz **pozycję Kopiuj,** aby skopiować blok kodu w tym przewodniku Szybki start, wkleić go do sesji powłoki i uruchomić.

Narzędzie cli platformy Azure wymaga zalogowania się do konta platformy Azure. Cała komunikacja między sesją powłoki interfejsu wiersza polecenia platformy Azure a centrum IoT hub jest uwierzytelniona i zaszyfrowana. W rezultacie ten szybki start nie wymaga dodatkowego uwierzytelniania, którego można używać z rzeczywistym urządzeniem, takim jak parametry połączenia.

*  Uruchom [polecenie dodawania rozszerzenia az,](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az-extension-add) aby dodać rozszerzenie IoT usługi Microsoft Azure dla platformy Azure CLI do powłoki interfejsu wiersza polecenia. Rozszerzenie IOT dodaje IoT Hub, IoT Edge i Usługi inicjowania obsługi administracyjnej urządzeń IoT (DPS) polecenia do interfejsu wiersza polecenia platformy Azure.

   ```azurecli
   az extension add --name azure-iot
   ```
   
   Po zainstalowaniu rozszerzenia usługi Azure IOT nie trzeba go ponownie instalować w dowolnej sesji usługi Cloud Shell. 

   [!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

*  Otwórz drugą sesję interfejsu wiersza polecenia.  Jeśli używasz powłoki chmury, wybierz pozycję **Otwórz nową sesję**. Jeśli używasz interfejsu wiersza polecenia lokalnie, otwórz drugie wystąpienie. 

    >[!div class="mx-imgBorder"]
    >![Otwórz nową sesję Cloud Shell](media/quickstart-send-telemetry-cli/cloud-shell-new-session.png)

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub
W tej sekcji używasz interfejsu wiersza polecenia platformy Azure do utworzenia grupy zasobów i centrum IoT Hub.  Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Centrum IoT działa jako centralne centrum komunikatów dla dwukierunkowej komunikacji między aplikacją IoT a urządzeniami. 

> [!TIP]
> Opcjonalnie można utworzyć grupę zasobów platformy Azure, Centrum IoT i inne zasoby przy użyciu [portalu Azure,](iot-hub-create-through-portal.md) [kodu programu Visual Studio](iot-hub-create-use-iot-toolkit.md)lub innych metod programowych.  

1. Uruchom polecenie [utwórz grupę AZ,](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) aby utworzyć grupę zasobów. Następujące polecenie tworzy grupę zasobów o nazwie *MyResourceGroup* w lokalizacji *eastus.* 

    ```azurecli
    az group create --name MyResourceGroup --location eastus
    ```

1. Uruchom polecenie [az iot hub create,](https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az-iot-hub-create) aby utworzyć centrum IoT hub. Utworzenie centrum IoT hub może potrwać kilka minut. 

    *YourIotHubName*. zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub. Nazwa centrum IoT musi być unikatowa globalnie na platformie Azure. Ten symbol zastępczy jest używany w pozostałej części tego przewodnika Szybki start do reprezentowania nazwy centrum IoT.

    ```azurecli
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName}
    ```

## <a name="create-and-monitor-a-device"></a>Tworzenie i monitorowanie urządzenia
W tej sekcji utworzysz symulowane urządzenie w pierwszej sesji interfejsu wiersza polecenia. Symulowane urządzenie wysyła dane telemetryczne urządzenia do centrum IoT hub. W drugiej sesji interfejsu wiersza polecenia można monitorować zdarzenia i dane telemetryczne oraz wysyłać komunikat z chmury do urządzenia do symulowanego urządzenia.

Aby utworzyć i uruchomić symulowane urządzenie:
1. Uruchom polecenie [tworzenia tożsamości urządzenia az iot hub](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) w pierwszej sesji interfejsu wiersza polecenia. Spowoduje to utworzenie symulowanej tożsamości urządzenia. 

    *YourIotHubName*. zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub. 

    *simDevice*. Tej nazwy można używać bezpośrednio dla symulowanego urządzenia w pozostałej części tego przewodnika Szybki start. Opcjonalnie należy użyć innej nazwy. 

    ```azurecli
    az iot hub device-identity create --device-id simDevice --hub-name {YourIoTHubName} 
    ```

1. Uruchom polecenie [symulacji urządzenia az iot](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/device?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-device-simulate) w pierwszej sesji interfejsu wiersza polecenia.  Spowoduje to uruchomienie symulowanego urządzenia. Urządzenie wysyła dane telemetryczne do centrum IoT hub i odbiera wiadomości z niego.  

    *YourIotHubName*. zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub. 

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

Aby monitorować urządzenie:
1. W drugiej sesji interfejsu wiersza polecenia uruchom polecenie [az iot hub monitor-events.](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-monitor-events) Spowoduje to rozpoczęcie monitorowania symulowanego urządzenia. Dane wyjściowe pokazuje dane telemetryczne, które symulowane urządzenie wysyła do centrum IoT hub.

    *YourIotHubName*. zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub. 

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

    ![Usługa Monitoruj zdarzenia w usłudze Cloud Shell](media/quickstart-send-telemetry-cli/cloud-shell-monitor.png)

1. Po monitorowaniu symulowanego urządzenia w drugiej sesji interfejsu wiersza polecenia naciśnij klawisze Ctrl+C, aby zatrzymać monitorowanie. 

## <a name="use-the-cli-to-send-a-message"></a>Wysyłanie wiadomości za pomocą interfejsu wiersza polecenia
W tej sekcji użyj drugiej sesji interfejsu wiersza polecenia, aby wysłać wiadomość do symulowanego urządzenia.

1. W pierwszej sesji interfejsu wiersza polecenia upewnij się, że symulowane urządzenie jest uruchomione. Jeśli urządzenie zostało zatrzymane, uruchom następujące polecenie, aby go uruchomić:

    *YourIotHubName*. zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub. 

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

1. W drugiej sesji interfejsu wiersza polecenia uruchom polecenie [e2d-message urządzenia az iot.](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/device/c2d-message?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-device-c2d-message-send) Spowoduje to wysłanie komunikatu chmury do urządzenia z centrum IoT hub do symulowanego urządzenia. Komunikat zawiera ciąg i dwie pary klucz-wartość.  

    *YourIotHubName*. zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub. 

    ```azurecli
    az iot device c2d-message send -d simDevice --data "Hello World" --props "key0=value0;key1=value1" -n {YourIoTHubName}
    ```
    Opcjonalnie można wysyłać komunikaty z chmury do urządzenia przy użyciu witryny Azure portal. Aby to zrobić, przejdź do strony przeglądu centrum IoT Hub, wybierz pozycję **Urządzenia IoT**, wybierz symulowane urządzenie i wybierz pozycję **Wiadomość do urządzenia**. 

1. W pierwszej sesji interfejsu wiersza polecenia upewnij się, że symulowane urządzenie odebrało wiadomość. 

    ![Komunikat chmury shella z chmury do urządzenia](media/quickstart-send-telemetry-cli/cloud-shell-receive-message.png)

1. Po wyświetleniach wiadomości zamknij drugą sesję interfejsu wiersza polecenia. Zachowaj otwartą pierwszą sesję interfejsu wiersza polecenia. Służy do czyszczenia zasobów w późniejszym kroku.

## <a name="view-messaging-metrics-in-the-portal"></a>Wyświetlanie danych obsługi wiadomości w portalu
Witryna Azure portal umożliwia zarządzanie wszystkimi aspektami usługi IoT Hub i urządzeń. W typowej aplikacji Usługi IoT Hub, która posuwa dane telemetryczne z urządzeń, można monitorować urządzenia lub wyświetlać metryki na telemetrii urządzenia. 

Aby wizualizować metryki obsługi wiadomości w witrynie Azure portal:
1. W menu nawigacji po lewej stronie portalu wybierz pozycję **Wszystkie zasoby**. Spowoduje to wyświetlenie listy wszystkich zasobów w ramach subskrypcji, w tym utworzonego centrum IoT Hub. 

1. Wybierz łącze utworzonego centrum IoT. Portal wyświetla stronę przeglądu centrum.

1. Wybierz **metryki** w lewym okienku centrum IoT Hub. 

    ![Metryki obsługi wiadomości usługi IoT Hub](media/quickstart-send-telemetry-cli/iot-hub-portal-metrics.png)

1. Wprowadź nazwę centrum IoT w **zakresie**.

2. Wybierz *pozycję Iot Hub Standard Metrics* in **Metric Metror .**

3. Wybierz *pozycję Całkowita liczba wiadomości używanych* w **metryki**. 

4. Umieść wskaźnik myszy nad obszarem osi czasu, na którym urządzenie wysyła wiadomości. Całkowita liczba wiadomości w punkcie w czasie pojawia się w lewym dolnym rogu osi czasu.

    ![Wyświetlanie metryk usługi Azure IoT Hub](media/quickstart-send-telemetry-cli/iot-hub-portal-view-metrics.png)

5. Opcjonalnie użyj listy rozwijanej **Metryka,** aby wyświetlić inne metryki na symulowanym urządzeniu. Na przykład *zrealizowane dostawy wiadomości C2d* lub *Łączna liczba urządzeń (wersja zapoznawcza)*. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Jeśli nie potrzebujesz już zasobów platformy Azure utworzonych w tym przewodniku Szybki start, możesz użyć interfejsu wiersza polecenia platformy Azure, aby je usunąć.

Jeśli będziesz kontynuować następny zalecany artykuł, możesz zachować zasoby, które zostały już utworzone, i ponownie ich użyć. 

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne. Grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub grupy zasobów. 

Aby usunąć grupę zasobów na podstawie nazwy:
1. Uruchom polecenie [delete grupy az.](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) Spowoduje to usunięcie grupy zasobów, Centrum IoT hub i utworzonej rejestracji urządzenia.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Uruchom polecenie [listy grup AZ,](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-list) aby potwierdzić, że grupa zasobów została usunięta.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Następne kroki
W tym przewodniku Szybki start użyto interfejsu wiersza polecenia platformy Azure do utworzenia centrum IoT hub, utworzenia symulowanego urządzenia, wysłania danych telemetrycznych, monitorowania danych telemetrycznych, wysyłania komunikatu z chmury do urządzenia i czyszczenia zasobów. Witryna Azure portal została użyta do wizualizacji metryk obsługi wiadomości na urządzeniu.

Jeśli jesteś deweloperem urządzeń, sugerowany następny krok jest, aby wyświetlić szybki start telemetrii, który używa zestawu SDK urządzenia Usługi Azure IoT dla języka C. Opcjonalnie, zobacz jeden z dostępnych artykułów szybkiego startu telemetrii Usługi Azure IoT hub w preferowanym języku lub SDK.

> [!div class="nextstepaction"]
> [Szybki start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT hub (C)](quickstart-send-telemetry-c.md)
