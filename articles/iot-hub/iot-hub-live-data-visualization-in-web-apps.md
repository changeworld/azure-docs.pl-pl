---
title: Wizualizacja danych centrum IoT w czasie rzeczywistym w aplikacji internetowej
description: Użyj aplikacji internetowej do wizualizacji danych temperatury i wilgotności, które są zbierane z czujnika i wysyłane do koncentratora Iot.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 05/31/2019
ms.author: robinsh
ms.openlocfilehash: 138e077f7b47fa9f38a4710db95eb7208cef78e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78675317"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>Wizualizuj dane czujników w czasie rzeczywistym z centrum Usługi Azure IoT w aplikacji sieci web

![Diagram end-to-end](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Omawiane zagadnienia

W tym samouczku dowiesz się, jak wizualizować dane czujników w czasie rzeczywistym odbierane przez centrum IoT za pomocą aplikacji sieci web node.js uruchomionej na komputerze lokalnym. Po uruchomieniu aplikacji sieci web lokalnie, można opcjonalnie wykonać kroki do hostowania aplikacji sieci web w usłudze Azure App Service. Jeśli chcesz spróbować wizualizować dane w centrum IoT przy użyciu usługi Power BI, zobacz [Wizualizowanie danych czujników usługi Azure IoT Hub za pomocą usługi Power BI](iot-hub-live-data-visualization-in-power-bi.md).

## <a name="what-you-do"></a>Co robisz

* Dodawanie grupy odbiorców do centrum IoT hub, której aplikacja internetowa będzie używać do odczytywania danych z czujników
* Pobierz kod aplikacji internetowej z GitHub
* Sprawdzanie kodu aplikacji sieci Web
* Konfigurowanie zmiennych środowiskowych w celu przechowywania artefaktów usługi IoT Hub wymaganych przez aplikację sieci web
* Uruchamianie aplikacji sieci web na komputerze deweloperskim
* Otwórz stronę internetową, aby wyświetlić dane dotyczące temperatury i wilgotności w czasie rzeczywistym z koncentratora IoT
* (Opcjonalnie) Hostowania aplikacji sieci Web w usłudze Azure App Service za pomocą interfejsu wiersza polecenia platformy Azure

## <a name="what-you-need"></a>Co jest potrzebne

* Ukończ samouczek [symulatora online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) lub jeden z samouczków urządzenia; na przykład [Raspberry Pi z node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Obejmują one następujące wymagania:

  * Aktywna subskrypcja platformy Azure
  * Centrum Iot w ramach subskrypcji
  * Aplikacja kliencka, która wysyła wiadomości do centrum Iot

* [Pobierz Git](https://www.git-scm.com/downloads)

* Kroki opisane w tym artykule zakładają, że komputer deweloperów systemu Windows; można jednak łatwo wykonać te kroki w systemie Linux w preferowanej powłoce.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Uruchom następujące polecenie, aby dodać rozszerzenie IoT usługi Microsoft Azure dla interfejsu wiersza polecenia platformy Azure do wystąpienia usługi Cloud Shell. Rozszerzenie IOT dodaje IoT Hub, IoT Edge i Usługi inicjowania obsługi administracyjnej urządzeń IoT (DPS) polecenia do interfejsu wiersza polecenia platformy Azure.

```azurecli-interactive
az extension add --name azure-iot
```

## <a name="add-a-consumer-group-to-your-iot-hub"></a>Dodawanie grupy odbiorców do centrum IoT

[Grupy konsumentów](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) zapewniają niezależne widoki do strumienia zdarzeń, które umożliwiają aplikacjom i usługom platformy Azure niezależne korzystanie z danych z tego samego punktu końcowego usługi Event Hub. W tej sekcji należy dodać grupę odbiorców do wbudowanego punktu końcowego centrum IoT, który będzie używany przez aplikację sieci web do odczytywania danych.

Uruchom następujące polecenie, aby dodać grupę odbiorców do wbudowanego punktu końcowego centrum IoT hub:

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

Zanotuj nazwę, którą wybierzesz, będziesz jej potrzebować w dalszej części tego samouczka.

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>Pobierz ciąg połączenia usługi dla centrum IoT

Centra IoT są tworzone przy kilku domyślnych zasadach dostępu. Jedną z takich zasad jest zasady **usługi,** która zapewnia wystarczające uprawnienia dla usługi do odczytu i zapisu punktów końcowych centrum IoT hub. Uruchom następujące polecenie, aby uzyskać parametry połączenia dla centrum IoT hub, który jest zgodny z zasadami usługi:

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIotHub --policy-name service
```

Parametry połączenia powinny wyglądać podobnie do następującego:

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

Zanotuj ciąg połączenia usługi, będziesz go potrzebować w dalszej części tego samouczka.

## <a name="download-the-web-app-from-github"></a>Pobierz aplikację internetową z GitHub

Otwórz okno polecenia i wprowadź następujące polecenia, aby pobrać próbkę z gitHub i przejść do przykładowego katalogu:

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>Sprawdzanie kodu aplikacji sieci Web

Z katalogu wizualizacji web-apps-node-iot-hub-data-visualization otwórz aplikację internetową w ulubionym edytorze. Poniżej przedstawiono strukturę plików wyświetlaną w programie VS Code:

![Struktura plików aplikacji sieci Web](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

Poświęć chwilę na zbadanie następujących plików:

* **Server.js** to skrypt po stronie usługi, który inicjuje gniazdo sieci web i klasę otoki Centrum zdarzeń. Zapewnia wywołanie zwrotne do klasy otoki Centrum zdarzeń, której klasa używa do emisji wiadomości przychodzących do gniazda sieci web.

* **Event-hub-reader.js** to skrypt po stronie usługi, który łączy się z wbudowanym punktem końcowym centrum IoT przy użyciu określonego ciągu połączenia i grupy odbiorców. Wyodrębnia DeviceId i EnqueuedTimeUtc z metadanych na wiadomości przychodzące, a następnie przekazuje wiadomość przy użyciu metody wywołania zwrotnego zarejestrowanej przez server.js.

* **Chart-device-data.js** to skrypt po stronie klienta, który nasłuchuje na gnieździe internetowym, śledzi każdy Identyfikator Urządzenia i przechowuje ostatnie 50 punktów przychodzących danych dla każdego urządzenia. Następnie wiąże wybrane dane urządzenia z obiektem wykresu.

* **Index.html** obsługuje układ interfejsu użytkownika dla strony sieci web i odwołuje się do niezbędnych skryptów dla logiki po stronie klienta.

## <a name="configure-environment-variables-for-the-web-app"></a>Konfigurowanie zmiennych środowiskowych dla aplikacji sieci Web

Aby odczytać dane z centrum IoT hub, aplikacja sieci web potrzebuje ciągu połączenia centrum IoT i nazwę grupy konsumentów, które powinny być odczytywane. Pobiera te ciągi ze środowiska przetwarzania w następujących wierszach w server.js:

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

Ustaw zmienne środowiskowe w oknie polecenia za pomocą następujących poleceń. Zastąp wartości zastępcze ciągiem połączenia usługi dla centrum IoT hub i nazwą grupy odbiorców utworzonej wcześniej. Nie cytuj ciągów.

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>Uruchamianie aplikacji internetowej

1. Upewnij się, że urządzenie jest uruchomione i wysyła dane.

2. W oknie poleceń uruchom następujące wiersze, aby pobrać i zainstalować pakiety, do których istnieje odwołanie, oraz uruchomić witrynę sieci Web:

   ```cmd
   npm install
   npm start
   ```

3. W konsoli powinno być widoczne dane wyjściowe wskazujące, że aplikacja internetowa pomyślnie połączyła się z centrum IoT hub i nasłuchuje na porcie 3000:

   ![Aplikacja sieci Web uruchomiona na konsoli](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>Otwieranie strony internetowej w celu wyświetlenia danych z centrum IoT

Otwórz przeglądarkę, aby . `http://localhost:3000`

Na liście **Wybierz urządzenie** wybierz urządzenie, aby wyświetlić bieżący wykres ostatnich 50 punktów danych temperatury i wilgotności wysyłanych przez urządzenie do centrum IoT hub.

![Strona aplikacji sieci Web z temperaturą i wilgotnością w czasie rzeczywistym](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

W konsoli powinny być również widoczne dane wyjściowe, które pokazują komunikaty, które aplikacja internetowa emituje do klienta przeglądarki:  

![Dane wyjściowe emisji aplikacji sieci Web na konsoli](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>Hostuj aplikację internetową w usłudze app service

[Funkcja aplikacji sieci Web usługi Azure App Service](https://docs.microsoft.com/azure/app-service/overview) zapewnia platformę jako usługę (PAAS) do obsługi aplikacji sieci web. Aplikacje sieci Web hostowane w usłudze Azure App Service mogą korzystać z zaawansowanych funkcji platformy Azure, takich jak dodatkowe zabezpieczenia, równoważenie obciążenia i skalowalność, a także rozwiązania DevOps platformy Azure i partnera, takie jak ciągłe wdrażanie, zarządzanie pakietami i tak dalej. Usługa Azure App Service obsługuje aplikacje internetowe opracowane w wielu popularnych językach i wdrożone w infrastrukturze systemu Windows lub Linux.

W tej sekcji aprowizujesz aplikację sieci web w usłudze App Service i wdrażasz do niej kod przy użyciu poleceń interfejsu wiersza polecenia platformy Azure. Szczegółowe informacje na temat poleceń używanych można znaleźć w dokumentacji [aplikacji az webapp.](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest) Przed rozpoczęciem upewnij się, że wykonaliśmy kroki, aby [dodać grupę zasobów do centrum IoT](#add-a-consumer-group-to-your-iot-hub)hub , [pobrać parametry połączenia usługi dla centrum IoT](#get-a-service-connection-string-for-your-iot-hub)hub i pobrać aplikację internetową z [gitHub.](#download-the-web-app-from-github)

1. [Plan usługi aplikacji](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) definiuje zestaw zasobów obliczeniowych dla aplikacji hostowane w usłudze App Service do uruchomienia. W tym samouczku używamy warstwy Deweloper/Bezpłatny do hosta aplikacji sieci web. W warstwie Bezpłatna aplikacja sieci web działa na udostępnionych zasobach systemu Windows z innymi aplikacjami usługi App Service, w tym aplikacjami innych klientów. Platforma Azure oferuje również plany usługi App Service do wdrażania aplikacji sieci web na zasoby obliczeniowe systemu Linux. Możesz pominąć ten krok, jeśli masz już plan usługi app service, którego chcesz użyć.

   Aby utworzyć plan usługi app service przy użyciu warstwy bezpłatnej systemu Windows, uruchom następujące polecenie. Użyj tej samej grupy zasobów, w której znajduje się centrum IoT Hub. Nazwa planu usług może zawierać wielkie i małe litery, cyfry i łączniki.

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. Teraz aprowizuj aplikację internetową w planie usługi App Service. Parametr `--deployment-local-git` umożliwia przekazywanie i wdrażanie kodu aplikacji sieci web z repozytorium Git na komputerze lokalnym. Nazwa aplikacji sieci web musi być unikatowa globalnie i może zawierać wielkie i małe litery, cyfry i łączniki. Należy określić węzeł w wersji 10.6 lub nowszej dla parametru, `--runtime` w zależności od wersji używanego środowiska wykonawczego Node.js. Za pomocą `az webapp list-runtimes` polecenia można uzyskać listę obsługiwanych uruchomień.

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --runtime "node|10.6" --deployment-local-git
   ```

3. Teraz dodaj ustawienia aplikacji dla zmiennych środowiskowych, które określają parametry połączenia centrum IoT i grupę odbiorców centrum zdarzeń. Poszczególne ustawienia są rozdzielane `-settings` w parametrze. Użyj ciągu połączenia usługi dla centrum IoT hub i grupy konsumentów utworzonej wcześniej w tym samouczku. Nie cytuj wartości.

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString=<your IoT hub connection string>
   ```

4. Włącz protokół Web Sockets dla aplikacji sieci web i ustaw aplikację sieci web, aby odbierała tylko żądania HTTPS (żądania HTTP są przekierowywane do protokołu HTTPS).

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. Aby wdrożyć kod w usłudze App Service, [użyjesz poświadczeń wdrożenia na poziomie użytkownika.](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials) Poświadczenia wdrożenia na poziomie użytkownika różnią się od poświadczeń platformy Azure i są używane w przypadku lokalnych i ftp wdrożeń git do aplikacji sieci web. Po ustawieniu są one prawidłowe we wszystkich aplikacjach usługi App Service we wszystkich subskrypcjach na koncie platformy Azure. Jeśli wcześniej ustawiono poświadczenia wdrażania na poziomie użytkownika, można ich używać.

   Jeśli wcześniej nie ustawiono poświadczeń wdrożenia na poziomie użytkownika lub nie można zapamiętać hasła, uruchom następujące polecenie. Nazwa użytkownika wdrożenia musi być unikatowa na platformie Azure i nie może zawierać symbolu "@" dla lokalnych wypychań Git. Po wyświetleniu monitu wprowadź i potwierdź nowe hasło. Hasło musi mieć co najmniej osiem znaków, z dwoma z następujących trzech elementów: literami, cyframi i symbolami.

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. Pobierz adres URL Git, którego użyjesz do wypchnięcia kodu do usługi App Service.

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. Dodaj pilota do klona, który odwołuje się do repozytorium Git dla aplikacji sieci web w usłudze App Service. W \<przypadku adresu\>URL klonu Git użyj adresu URL zwróconego w poprzednim kroku. Uruchom następujące polecenie w oknie polecenia.

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. Aby wdrożyć kod w usłudze App Service, wprowadź następujące polecenie w oknie polecenia. Jeśli zostanie wyświetlony monit o podanie poświadczeń, wprowadź poświadczenia wdrożenia na poziomie użytkownika utworzone w kroku 5. Upewnij się, że wypychasz do gałęzi głównej zdalnego usługi app service.

    ```cmd
    git push webapp master:master
    ```

9. Postęp wdrożenia zostanie zaktualizowany w oknie polecenia. Pomyślne wdrożenie zakończy się wierszami podobnymi do następujących danych wyjściowych:

    ```cmd
    remote:
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://contoso-web-app-3.scm.azurewebsites.net/contoso-web-app-3.git
    6b132dd..7cbc994  master -> master
    ```

10. Uruchom następujące polecenie, aby zbadać stan aplikacji sieci web i upewnić się, że jest uruchomiona:

    ```azurecli-interactive
    az webapp show -n <your web app name> -g <your resource group name> --query state
    ```

11. W przeglądarce przejdź do adresu `https://<your web app name>.azurewebsites.net`. Strona internetowa podobna do tej, którą można wyświetlić podczas wyświetlania aplikacji sieci web lokalnie. Zakładając, że urządzenie jest uruchomione i wysyła dane, powinien zostać wyświetlony wykres bieżący 50 najnowszych odczytów temperatury i wilgotności wysyłanych przez urządzenie.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli natkniesz się na jakiekolwiek problemy z tym przykładem, spróbuj wykonać kroki opisane w poniższych sekcjach. Jeśli nadal masz problemy, wyślij nam opinię na dole tego tematu.

### <a name="client-issues"></a>Problemy z klientami

* Jeśli urządzenie nie jest wyświetlane na liście lub nie jest rysowany wykres, upewnij się, że kod urządzenia jest uruchomiony na urządzeniu.

* W przeglądarce otwórz narzędzia programistyczne (w wielu przeglądarkach otworzy go klawisz F12) i znajdź konsolę. Poszukaj tam żadnych ostrzeżeń lub błędów.

* Skrypt po stronie klienta można debugować w /js/chat-device-data.js.

### <a name="local-website-issues"></a>Lokalne problemy z witryną internetową

* Obejrzyj dane wyjściowe w oknie, w którym uruchomiono węzeł dla danych wyjściowych konsoli.

* Debuguj kod serwera, w szczególności server.js i /scripts/event-hub-reader.js.

### <a name="azure-app-service-issues"></a>Problemy z usługą Azure App Service

* W witrynie Azure portal przejdź do aplikacji sieci web. W obszarze **Monitorowanie** w lewym okienku wybierz pozycję **Dzienniki usługi app service**. Włącz **rejestrowanie aplikacji (system plików),** ustaw **poziom** na błąd, a następnie wybierz pozycję **Zapisz**. Następnie otwórz **strumień dziennika** (w obszarze **Monitorowanie).**

* Z aplikacji sieci web w witrynie Azure portal, w obszarze `node -v` Narzędzia `npm -v` **programistyczne** wybierz **konsolę** i sprawdź poprawność wersji węzłów i npm z i .

* Jeśli zostanie wyświetlony błąd dotyczący nie znalezienia pakietu, być może zostały uruchomione kroki poza kolejnością. Po wdrożeniu witryny `git push`(z) `npm install`uruchamia się usługa aplikacji, która jest uruchamiana na podstawie bieżącej wersji węzła, który został skonfigurowany. Jeśli zostanie to zmienione w konfiguracji później, musisz wprowadzić bezsensowną zmianę w kodzie i wypchnąć ponownie.

## <a name="next-steps"></a>Następne kroki

Aplikacja internetowa została pomyślnie wykorzystana do wizualizacji danych czujników w czasie rzeczywistym z centrum IoT Hub.

Aby uzyskać inny sposób wizualizacji danych z usługi Azure IoT Hub, zobacz [Wizualizowanie danych czujników w czasie rzeczywistym z centrum IoT za pomocą](iot-hub-live-data-visualization-in-power-bi.md)usługi Power BI.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
