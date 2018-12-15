---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 09/14/2018
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: 73d40bfb5a7e691cead5a84be70398e9cbf6656a
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2018
ms.locfileid: "53262766"
---
## <a name="run-the-web-application"></a>Uruchamianie aplikacji internetowej

1. Dla Twojej wygody w witrynie GitHub umieszczono przykładową, jednostronicową aplikację internetową. W przeglądarce otwórz stronę [https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat/](https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat/).

    > [!NOTE]
    > Źródło pliku HTML znajduje się w lokalizacji [/docs/demo/chat/index.html](https://github.com/Azure-Samples/signalr-service-quickstart-serverless-chat/blob/master/docs/demo/chat/index.html).

1. Po wyświetleniu monitu o podstawowy adres URL dla aplikacji funkcji wprowadź adres *http://localhost:7071*.

1. Po wyświetleniu monitu wprowadź nazwę użytkownika.

1. Aplikacja internetowa wywołuje funkcję *GetSignalRInfo* w aplikacji funkcji, aby pobrać informacje o połączeniu w celu nawiązania połączenia z usługą Azure SignalR Service. Po nawiązaniu połączenia zostanie wyświetlone pole wprowadzania komunikatu czatu.

1. Wpisz komunikat, a następnie naciśnij klawisz Enter. Aplikacja wysyła komunikat do funkcji *SendMessage* w aplikacji Azure Function, która następnie używa powiązania danych wyjściowych usługi SignalR, aby rozpowszechnić komunikat do wszystkich połączonych klientów. Jeśli wszystko działa prawidłowo, komunikat powinien pojawić się w aplikacji.

    ![Uruchamianie aplikacji](../media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-run-application.png)

1. Otwórz inne wystąpienie aplikacji internetowej w innym oknie przeglądarki. Zobaczysz, że wszystkie wysłane komunikaty pojawią się we wszystkich wystąpieniach aplikacji.