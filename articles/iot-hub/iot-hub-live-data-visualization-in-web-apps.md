---
title: Wizualizacja danych w czasie rzeczywistym danych z czujników z usługi Azure IoT hub w aplikacji sieci web | Dokumentacja firmy Microsoft
description: Wizualizowanie danych temperatury i wilgotności, zebranych z czujników i wysyłane do usługi Iot hub za pomocą aplikacji sieci web.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 05/31/2019
ms.author: robinsh
ms.openlocfilehash: 22b15a95e529d4f09560e9b7e59d9f78f70651bc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66476004"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>Wizualizowanie danych z czujników w czasie rzeczywistym z usługi Azure IoT hub w aplikacji sieci web

![Diagram end-to-end](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Omawiane zagadnienia

W tym samouczku dowiesz się, jak wizualizować dane czujników w czasie rzeczywistym usługi IoT hub odbierze za pomocą aplikacji internetowej node.js uruchomionej na komputerze lokalnym. Po uruchomieniu aplikacji sieci web lokalnie, można opcjonalnie wykonaj kroki, aby hostować aplikację sieci web w usłudze Azure App Service. Jeśli chcesz wypróbować jej możliwości do wizualizacji danych w usłudze IoT hub za pomocą usługi Power BI, zobacz [usługa Power BI umożliwia wizualizowanie danych z czujników w czasie rzeczywistym z usługi Azure IoT Hub](iot-hub-live-data-visualization-in-power-bi.md).

## <a name="what-you-do"></a>Co należy zrobić

* Dodaj grupę odbiorców do usługi IoT hub, który aplikacja sieci web będzie używany do odczytu danych z czujników
* Pobieranie kodu aplikacji internetowej z usługi GitHub
* Badanie kodu aplikacji sieci web
* Konfigurowanie zmiennych środowiskowych do przechowywania artefaktów wymagane przez aplikację sieci web w Centrum IoT Hub
* Uruchamianie aplikacji sieci web na komputerze deweloperskim
* Otwórz stronę sieci web, aby wyświetlić dane w czasie rzeczywistym temperatury i wilgotności z usługi IoT hub
* (Opcjonalnie) Użyj wiersza polecenia platformy Azure do hostowania aplikacji sieci web w usłudze Azure App Service

## <a name="what-you-need"></a>Co jest potrzebne

* Wykonaj [symulatora w trybie online urządzenia Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) samouczka lub jednym z samouczków urządzenia; na przykład [Raspberry Pi w środowisku node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Obejmują one następujące wymagania:

  * Aktywna subskrypcja platformy Azure
  * Centrum Iot hub w ramach Twojej subskrypcji
  * Aplikacja kliencka, która wysyła komunikaty do Centrum Iot hub

* [Pobierz narzędzia Git](https://www.git-scm.com/downloads)

* W tym artykule założono w nim komputera deweloperskiego z systemem Windows; Jednakże, możesz łatwo te kroki można wykonać w systemie Linux w powłoce preferowany.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Uruchom następujące polecenie, aby dodać rozszerzenia usługi Microsoft Azure IoT dla interfejsu wiersza polecenia platformy Azure do swojego wystąpienia usługi Cloud Shell. Rozszerzenia IOT dodaje określone polecenia usługi IoT Hub, IoT Edge i usługi aprowizacji urządzeń IoT (DPS) do wiersza polecenia platformy Azure.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

## <a name="add-a-consumer-group-to-your-iot-hub"></a>Dodaj grupę odbiorców do usługi IoT hub

[Grupy konsumentów](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) zapewnia niezależne widoki do strumienia zdarzeń, które umożliwiają aplikacjom i usługom platformy Azure niezależnie korzystają z tego samego punktu końcowego Centrum zdarzeń danych. W tej sekcji dodajesz grupy odbiorców do usługi IoT hub wbudowany punkt końcowy, który aplikacja sieci web będzie używać do odczytywania danych z.

Uruchom następujące polecenie, aby dodać grupę odbiorców do wbudowany punkt końcowy usługi IoT Hub:

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

Uwaga w dół do wybranej nazwy, będzie on potrzebny w dalszej części tego samouczka.

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>Pobieranie parametrów połączenia usługi dla Centrum IoT hub

Centra IoT Hub są tworzone za pomocą kilku domyślne zasady dostępu. Takie zasady jest **usługi** zasad, które dostarcza wystarczających uprawnień dla usługi w celu odczytu i zapisu punktów końcowych usługi IoT hub. Uruchom następujące polecenie, aby pobrać parametry połączenia dla usługi IoT hub, która jest zgodna z zasadami usługi:

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIotHub --policy-name service
```

Parametry połączenia powinien wyglądać podobnie do następującego:

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

Zanotuj parametry połączenia usługi będzie potrzebny w dalszej części tego samouczka.

## <a name="download-the-web-app-from-github"></a>Pobieranie aplikacji internetowej z usługi GitHub

Otwórz okno polecenia i wprowadź następujące polecenia, aby pobrać próbkę z usługi GitHub, a następnie przejdź do katalogu, przykładowe:

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>Badanie kodu aplikacji sieci web

Z katalogu web-apps-node-iot-hub-data-visualization Otwórz aplikację sieci web w ulubionym edytorze. Poniżej przedstawiono strukturę pliku, wyświetlane w programie VS Code:

![Struktura plików aplikacji sieci Web](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

Poświęć chwilę, aby sprawdzić następujące pliki:

* **Server.js** skrypt po stronie usługi, która inicjuje gniazda sieci web i klasa otoki Centrum zdarzeń. Umożliwia wywołanie zwrotne do klasy otoki Centrum zdarzeń, która używa klasy w celu emisji komunikatów przychodzących dla gniazda sieci web.

* **Zdarzenia hub-reader.js** jest skryptu po stronie usługi, który nawiązuje połączenie z wbudowanego punktu końcowego Centrum IoT przy użyciu grupy ciągu i konsumentów określone połączenie. Go wyodrębnia DeviceId i EnqueuedTimeUtc z metadanych dla wiadomości przychodzących, a następnie przekazuje komunikat przy użyciu metody wywołania zwrotnego, zarejestrowanych przez server.js.

* **Wykres — urządzenia — data.js** to skrypt po stronie klienta, który nasłuchuje gniazda sieci web, przechowuje informacje o każdym DeviceId i przechowuje ostatnie 50 punkty danych przychodzących dla każdego urządzenia. Następnie powiąże dane wybranego urządzenia do obiektu wykresu.

* **Index.HTML** obsługuje układ interfejsu użytkownika dla strony sieci web i zawiera odwołania do wymaganymi skryptami logiki po stronie klienta.

## <a name="configure-environment-variables-for-the-web-app"></a>Konfigurowanie zmiennych środowiskowych dla aplikacji sieci web

Do odczytywania danych z usługi IoT hub, aplikacji sieci web wymaga parametrów połączenia Centrum IoT i nazwę grupy odbiorców, który powinni przeczytać za pośrednictwem. Pobiera te ciągi w środowisku procesu w następujących wierszach w server.js:

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

W oknie polecenia za pomocą następujących poleceń, należy ustawić zmienne środowiskowe. Zastąp wartości symboli zastępczych parametrami połączenia usługi dla Centrum IoT i nazwę grupy odbiorców, która została utworzona wcześniej. Nie oferty ciągi.

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>Uruchamianie aplikacji sieci web

1. Upewnij się, że urządzenie działa i wysyłanie danych.

2. W oknie wiersza polecenia Uruchom następujące wiersze, aby pobrać i zainstalować pakiety odwołania i uruchomić witrynę internetową:

   ```cmd
   npm install
   npm start
   ```

3. Powinny zostać wyświetlone dane wyjściowe w konsoli, która wskazuje, że aplikacja sieci web pomyślnie połączył się z Centrum IoT i nasłuchuje na porcie 3000:

   ![Aplikacja sieci Web, pracę w konsoli](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>Otwórz stronę sieci web, aby wyświetlić dane z usługi IoT hub

Otwórz w przeglądarce `http://localhost:3000`.

W **wybierz urządzenie** wybierz swoje urządzenie, aby wyświetlić wykres uruchomionych 50 ostatnich temperatury i wilgotności punktów danych wysyłane przez urządzenie do Centrum IoT.

![Wyświetlanie w czasie rzeczywistym temperatury i wilgotności strona aplikacji internetowej](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

Również powinny zostać wyświetlone dane wyjściowe w konsoli, która zawiera wiadomości, które rozgłasza aplikacji sieci web do przeglądarki klienta:  

![Aplikacja sieci Web, emisji dane wyjściowe w konsoli](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>Host aplikacji sieci web w usłudze App Service

[Funkcji Web Apps w usłudze Azure App Service](https://docs.microsoft.com/azure/app-service/overview) zapewnia to platforma jako usługa (PAAS) do hostowania aplikacji sieci web. Aplikacje sieci Web hostowaną w usłudze Azure App Service mogą korzystać z zaawansowanych funkcji platformy Azure, takich jak dodatkowe zabezpieczenia, równoważenie obciążenia, i skalowalności, jak również platformy Azure i jej partnerów DevOps rozwiązań, takich jak ciągłe wdrażanie, Zarządzanie pakietami i tak dalej. Usługa Azure App Service obsługuje aplikacje sieci web opracowanych w wielu popularnych języków i wdrożone w infrastrukturze Windows lub Linux.

W tej sekcji Inicjowanie obsługi administracyjnej aplikacji sieci web, w usłudze App Service i wdrożyć kod za pomocą poleceń interfejsu wiersza polecenia platformy Azure. Można znaleźć szczegółowe informacje o polecenia używane w [az webapp](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest) dokumentacji. Przed rozpoczęciem upewnij się, że ukończono kroki, aby [Dodaj grupę zasobów do usługi IoT hub](#add-a-consumer-group-to-your-iot-hub), [uzyskać parametry połączenia usługi IoT hub](#get-a-service-connection-string-for-your-iot-hub), i [pobieranie aplikacji internetowej z usługi GitHub](#download-the-web-app-from-github).

1. [Planu usługi App Service](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) definiuje zestaw zasobów obliczeniowych dla aplikacji hostowanej w usłudze App Service do uruchomienia. W tym samouczku używamy warstwy Deweloper/bezpłatna do hostowania tej aplikacji sieci web. W ramach warstwy bezpłatna działa aplikacja sieci web ze współużytkowanych zasobów Windows z innymi aplikacjami usługi App Service, w tym aplikacje innych klientów. Usługa Azure również oferty planów usługi App Service do wdrażania aplikacji sieci web w systemie Linux zasoby obliczeniowe. Można pominąć ten krok, jeśli masz już plan usługi App Service, do którego chcesz używać.

   Aby utworzyć plan usługi App Service, korzystając z bezpłatnej warstwy Windows, uruchom następujące polecenie. Użyj tej samej grupie zasobów, jest Centrum IoT hub. Nazwę planu usługi może zawierać górny i małe litery, cyfry i łączniki.

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. Teraz aprowizować aplikację internetową w planie usługi App Service. `--deployment-local-git` Parametr umożliwia kodu aplikacji sieci web, przekazać i wdrożyć z repozytorium Git na komputerze lokalnym. Nazwa aplikacji internetowej musi być globalnie unikatowa i może zawierać górny i małe litery, cyfry i łączniki.

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --deployment-local-git
   ```

3. Teraz dodaj ustawienia aplikacji, zmienne środowiskowe, które określają parametry połączenia Centrum IoT i grupa konsumentów Centrum zdarzeń. Poszczególne ustawienia są spacjami w `-settings` parametru. Użyj parametrów połączenia usługi dla Centrum IoT i grupy odbiorców, która została utworzona wcześniej w tym samouczku. Nie oferty wartości.

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString=<your IoT hub connection string>
   ```

4. Włącz gniazda sieci Web protokołu dla aplikacji sieci web i ustaw aplikacji sieci web, aby otrzymać HTTPS żądań tylko (żądania HTTP są przekierowywane do protokołu HTTPS).

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. Aby wdrożyć kod w usłudze App Service, użyjesz usługi [poświadczenia wdrażania na poziomie użytkownika](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials). Poświadczenia wdrażania na poziomie użytkownika różni się od swoich poświadczeń platformy Azure i są używane do lokalnej usługi Git i FTP wdrożenia aplikacji sieci web. Po ustawieniu, są one prawidłowe dla wszystkich aplikacji usługi App Service w taki sposób, w przypadku wszystkich subskrypcji na Twoim koncie platformy Azure. Jeśli już wcześniej ustawić poświadczenia wdrażania na poziomie użytkownika, można je.

   Jeśli nie zostało wcześniej ustawione poświadczenia wdrażania na poziomie użytkownika lub nie pamiętasz hasła, uruchom następujące polecenie. Nazwa użytkownika wdrożenia musi być unikatowa na platformie Azure i nie może zawierać "@" symboli dla wypycha lokalnego narzędzia Git. Po wyświetleniu monitu wprowadź i Potwierdź nowe hasło. Hasło musi mieć co najmniej ośmiu znaków i zawierać dwa z następujących trzech elementów: litery, cyfry i symbole.

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. Pobierz adres URL usługi Git służące do wypchnąć kod do usługi App Service.

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. Dodaj zdalną na klonie, który odwołuje się do repozytorium Git dla aplikacji sieci web w usłudze App Service. Aby uzyskać \<adres URL klonowania Git\>, użyj adresu URL, w zwróconym w poprzednim kroku. Uruchom następujące polecenie w oknie polecenia.

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. Aby wdrożyć kod w usłudze App Service, wprowadź następujące polecenie w oknie polecenia. Jeśli zostanie wyświetlony monit o poświadczenia, wprowadź poświadczenia wdrażania na poziomie użytkownika, które zostały utworzone w kroku 5. Upewnij się, wypchnięciu do głównej gałęzi zdalnej usługi App Service.

    ```cmd
    git push webapp master:master
    ```

9. Postęp wdrażania zostanie zaktualizowana w oknie polecenia. Pomyślne wdrożenie zakończy się wierszami podobnymi do następujących danych wyjściowych:

    ```cmd
    remote:
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://contoso-web-app-3.scm.azurewebsites.net/contoso-web-app-3.git
    6b132dd..7cbc994  master -> master
    ```

10. Uruchom następujące polecenie, aby zbadać stan aplikacji sieci web i upewnij się, że jest on uruchomiony:

    ```azurecli-interactive
    az webapp show -n <your web app name> -g <your resource group name> --query state
    ```

11. W przeglądarce przejdź do adresu `https://<your web app name>.azurewebsites.net`. Strona internetowa podobna do jednego widoczny po uruchomieniu aplikacji sieci web lokalnie Wyświetla. Przy założeniu, że urządzenie jest uruchomiony i wysyłanie danych, powinien zostać wyświetlony wykres uruchomionych 50 ostatnich temperatury i wilgotności odczytów wysyłane przez urządzenie.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli przejście na problemy z tego przykładu spróbuj wykonać kroki opisane w poniższych sekcjach. Jeśli nadal występują problemy, Wyślij do nas opinii na końcu tego tematu.

### <a name="client-issues"></a>Problemy z klientem

* Jeśli urządzenie nie ma na liście lub nie wykresu jest rysowana, upewnij się, że kod urządzenia jest uruchomiona na urządzeniu.

* W przeglądarce otwórz narzędzia deweloperskie (w wielu przeglądarkach naciśnięcie klawisza F12 klucz zostanie on otwarty) i Znajdź konsoli. Odszukaj wszelkie ostrzeżenia lub błędy drukowane.

* Można debugować skrypt po stronie klienta /js/chat-device-data.js.

### <a name="local-website-issues"></a>Problemy z lokalną witrynę sieci Web

* Obejrzyj danych wyjściowych w oknie, w której został uruchomiony węzeł dla danych wyjściowych konsoli.

* Debugowanie kodu serwera, szczególnie plików server.js i /scripts/event-hub-reader.js.

### <a name="azure-app-service-issues"></a>Problemy z usługi Azure App Service

* W witrynie Azure portal przejdź do aplikacji sieci web. W obszarze **monitorowanie** w okienku po lewej stronie wybierz **usługi App Service, dzienniki**. Włącz **rejestrowanie aplikacji (System plików)** , ustawić **poziom** błąd, a następnie wybrania **Zapisz**. Następnie otwórz **strumień dziennika** (w obszarze **monitorowanie**).

* Z aplikacji sieci web w witrynie Azure portal w obszarze **narzędzia programistyczne** wybierz **konsoli** i Zweryfikuj wersje oprogramowania node i Menedżera npm przy użyciu `node -v` i `npm -v`.

* Jeśli zostanie wyświetlony błąd o nie możesz znaleźć pakiet może trzeba uruchomić poza kolejnością kroków. Podczas wdrażania witryny (z `git push`) uruchamia usługę aplikacji `npm install`, której działa w oparciu o bieżącą wersję węzła została skonfigurowana. Jeśli się zmieniło w konfiguracji później, należy wprowadzić zmianę znaczenia w kodzie, a następnie wypchnąć ponownie.

## <a name="next-steps"></a>Kolejne kroki

Aplikacji sieci web została pomyślnie użyta, wizualizowanie danych z czujników w czasie rzeczywistym z usługi IoT hub.

Innym sposobem wizualizacji danych z usługi Azure IoT Hub, zobacz [usługa Power BI umożliwia wizualizowanie danych z czujników w czasie rzeczywistym z usługi IoT hub](iot-hub-live-data-visualization-in-power-bi.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
