---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 03/04/2019
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: 73d4d4e1a5f148dce6099b2d747ee1c290bcf7c1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "67183602"
---
## <a name="run-the-web-application"></a>Uruchamianie aplikacji internetowej

1. Dla Twojej wygody w witrynie GitHub umieszczono przykładową, jednostronicową aplikację internetową. Otwórz przeglądarkę, aby . [https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/](https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/)

    > [!NOTE]
    > Źródło pliku HTML znajduje się pod adresem [/docs/demo/chat-v2/index.html](https://github.com/Azure-Samples/signalr-service-quickstart-serverless-chat/blob/master/docs/demo/chat-v2/index.html).

1. Po wyświetleniu monitu o podstawowy adres URL dla aplikacji funkcji wprowadź adres `http://localhost:7071`.

1. Po wyświetleniu monitu wprowadź nazwę użytkownika.

1. Aplikacja internetowa wywołuje funkcję *GetSignalRInfo* w aplikacji funkcji, aby pobrać informacje o połączeniu w celu nawiązania połączenia z usługą Azure SignalR Service. Po nawiązaniu połączenia zostanie wyświetlone pole wprowadzania komunikatu czatu.

1. Wpisz komunikat, a następnie naciśnij klawisz Enter. Aplikacja wysyła komunikat do funkcji *SendMessage* w aplikacji Azure Function, która następnie używa powiązania danych wyjściowych usługi SignalR, aby rozpowszechnić komunikat do wszystkich połączonych klientów. Jeśli wszystko działa prawidłowo, komunikat powinien pojawić się w aplikacji.

    ![Uruchamianie aplikacji](../media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-run-application.png)

1. Otwórz inne wystąpienie aplikacji internetowej w innym oknie przeglądarki. Zobaczysz, że wszystkie wysłane komunikaty pojawią się we wszystkich wystąpieniach aplikacji.

> [!IMPORTANT]
> Ponieważ strona HTML jest obsługiwana przy użyciu protokołu HTTPS, ale lokalne środowisko wykonawcze usługi Azure Functions domyślnie używa protokołu HTTP, przeglądarka (taka jak Firefox) może wymusić zasady zawartości mieszanej, która blokuje żądania ze strony sieci Web do funkcji. Aby rozwiązać ten problem, należy użyć przeglądarki, która nie ma tego ograniczenia lub uruchomić lokalny serwer HTTP, taki jak [http-server](https://www.npmjs.com/package/http-server) w katalogu */docs/demo/chat-v2.* Upewnij się, że `CORS` początek jest dodawany do ustawienia w *local.settings.json*.