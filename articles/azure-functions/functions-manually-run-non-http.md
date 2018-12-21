---
title: Ręczne uruchamianie usługi Azure Functions niewyzwalanej przez protokół HTTP
description: Uruchamianie usługi Azure Functions niewyzwalanej przez protokół HTTP za pomocą żądania HTTP
services: functions
keywords: ''
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.topic: tutorial
ms.date: 12/12/2018
ms.author: cshoe
ms.openlocfilehash: 00a72c8c7fb42c763a8b0bad1fa3914ac27c496f
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2018
ms.locfileid: "53406934"
---
# <a name="manually-run-a-non-http-triggered-function"></a>Ręczne uruchamianie funkcji niewyzwalanej przez protokół HTTP

W tym artykule pokazano, jak ręcznie uruchomić funkcję niewyzwalaną przez protokół HTTP za pomocą specjalnie sformatowanego żądania HTTP.

W niektórych kontekstach może być konieczne uruchomienie „na żądanie” funkcji platformy Azure, która jest wyzwalana pośrednio.  Przykłady wyzwalaczy pośrednich to między innymi [funkcje zgodnie z harmonogramem](./functions-create-scheduled-function.md) lub funkcje, które są uruchamiane w wyniku [akcji innego zasobu](./functions-create-storage-blob-triggered-function.md). 

W poniższym przykładzie jest używane narzędzie [Postman](https://www.getpostman.com/), ale możesz używać narzędzia [cURL](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler) lub dowolnego innego narzędzia do wysyłania żądań HTTP.

## <a name="define-the-request-location"></a>Definiowanie lokalizacji żądania

Aby uruchomić funkcję niewyzwalaną przez protokół HTTP, potrzebujesz sposobu wysyłania żądania do platformy Azure umożliwiającego uruchomienie funkcji. Adres URL używany do wykonania tego żądania przyjmuje określoną postać.

![Definiowanie lokalizacji żądania: nazwa hosta + ścieżka folderu + nazwa funkcji](./media/functions-manually-run-non-http/azure-functions-admin-url-anatomy.png)

- **Nazwa hosta:** Lokalizacja publiczna aplikacji funkcji składająca się z nazwy aplikacji funkcji i domeny *azurewebsites.net* lub Twojej domeny niestandardowej.
- **Ścieżka folderu:** Aby za pomocą żądania HTTP uzyskać dostęp do funkcji niewyzwalanych przez protokół HTTP, musisz wysłać żądanie za pośrednictwem folderów *admin/functions*.
- **Nazwa funkcji:** Nazwa funkcji, którą chcesz uruchomić.

Ta lokalizacja żądania w narzędziu Postman wraz z kluczem głównym funkcji w żądaniu do platformy Azure służą do uruchamiania funkcji.

## <a name="get-the-functions-master-key"></a>Uzyskiwanie klucza głównego funkcji

Przejdź do funkcji w witrynie Azure Portal, kliknij pozycję **Zarządzaj** i znajdź sekcję **Klucze hosta**. Kliknij przycisk **Kopiuj** w wierszu *_master*, aby skopiować klucz główny do schowka.

![Kopiowanie klucza głównego z ekranu Zarządzanie funkcjami](./media/functions-manually-run-non-http/azure-portal-functions-master-key.png)

Po skopiowaniu klucza głównego kliknij nazwę funkcji, aby wrócić do okna pliku kodu. Następnie kliknij kartę **Dzienniki**. Zostaną wyświetlone komunikaty z funkcji rejestrowane w tym miejscu po ręcznym uruchomieniu funkcji z narzędzia Postman.

> [!CAUTION]  
> Ze względu na podwyższony poziom uprawnień w aplikacji funkcji przyznany przez klucz główny nie należy udostępniać tego klucza osobom trzecim ani rozpowszechniać go w aplikacji.

## <a name="call-the-function"></a>Wywołanie funkcji

Otwórz narzędzie Postman i wykonaj następujące kroki:

1. Wprowadź **lokalizację żądania w polu tekstowym adresu URL**. 
2. **Kliknij** kartę **Nagłówki**.
3. Wprowadź **x-functions-key** jako pierwszy **klucz** i wklej klucz główny (ze schowka) w polu **wartości**.
4. Wprowadź **Content-Type** jako drugi **klucz** i wprowadź **application/json** jako **wartość**.

    ![Ustawienia nagłówków narzędzia Postman](./media/functions-manually-run-non-http/functions-manually-run-non-http-headers.png)

5. **Kliknij** kartę **Treść**.
6. Wprowadź **{ "input": "test" }** jako treść żądania.

    ![Ustawienia treści narzędzia Postman](./media/functions-manually-run-non-http/functions-manually-run-non-http-body.png)

7. Kliknij pozycję **Wyślij**.

    ![Wysyłanie żądania za pomocą narzędzia Postman](./media/functions-manually-run-non-http/functions-manually-run-non-http-send.png)

Narzędzie Postman zgłosi stan **202 Zaakceptowano**.

Następnie wróć do swojej funkcji w witrynie Azure Portal. Znajdź okno *Dzienniki*, aby zobaczyć komunikaty pochodzące z ręcznego wywołania funkcji.

![Wyniki dziennika funkcji z ręcznego wywołania](./media/functions-manually-run-non-http/azure-portal-function-log.png)

## <a name="next-steps"></a>Następne kroki

- [Strategie testowania kodu w usłudze Azure Functions](./functions-test-a-function.md)
- [Debugowanie lokalnego wyzwalacza siatki zdarzeń funkcji platformy Azure](./functions-debug-event-grid-trigger-local.md)
