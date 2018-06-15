---
title: Utwórz interfejs API niekorzystającą przy użyciu usługi Azure Functions | Dokumentacja firmy Microsoft
description: Jak utworzyć interfejs API niekorzystającą przy użyciu usługi Azure Functions
services: functions
author: mattchenderson
manager: cfowler
ms.service: functions
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: tutorial
ms.date: 05/04/2017
ms.author: mahender
ms.custom: mvc
ms.openlocfilehash: 7c3933210c01c81077b594abb8c3183d6e3c58a0
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2017
ms.locfileid: "24811604"
---
# <a name="create-a-serverless-api-using-azure-functions"></a>Utwórz interfejs API niekorzystającą przy użyciu usługi Azure Functions

Z tego samouczka dowiesz się, jak usługi Azure Functions umożliwia tworzenie skalowalnej interfejsów API. Środowisko Azure Functions jest dostarczany z kolekcji wbudowanych HTTP wyzwalaczy i powiązań, które ułatwiają tworzenie punktu końcowego w różnych językach, łącznie z Node.JS, C# i inne. W tym samouczku będzie dostosować wyzwalacz protokołu HTTP do obsługi określonych akcji w projekcie interfejsu API. Należy również przygotować do powiększania interfejsu API integrowanie go z serwerów proxy funkcji platformy Azure i konfigurowanie zasymulować interfejsów API. Wszystko to odbywa się na górze środowiska obliczeniowe bez serwera funkcji, nie trzeba martwić skalowania zasobów — wystarczy można skoncentrować się na logice interfejsu API.

## <a name="prerequisites"></a>Wymagania wstępne 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

Funkcja wynikowy będzie służyć do pozostałej części tego samouczka.

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Otwórz Azure portal. Aby to zrobić, zaloguj się do [https://portal.azure.com](https://portal.azure.com) z Twoim kontem platformy Azure.

## <a name="customize-your-http-function"></a>Dostosowywanie funkcji HTTP

Domyślnie funkcja wyzwalanych przez protokół HTTP jest skonfigurowany do akceptowania dowolnej metody HTTP. Jest również domyślny adres URL w postaci `http://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>`. Po wykonaniu procedury szybkiego startu, następnie `<funcname>` prawdopodobnie wygląda "HttpTriggerJS1". W tej sekcji należy zmodyfikować funkcji ma odpowiadać tylko na żądania GET względem `/api/hello` zamiast tego trasy. 

1. Przejdź do funkcji w portalu Azure. Wybierz **integracji** na lewym pasku nawigacyjnym.

    ![Dostosowywanie funkcji HTTP](./media/functions-create-serverless-api/customizing-http.png)

1. Użyj ustawień wyzwalacza HTTP określoną w tabeli.

    | Pole | Wartość przykładowa | Opis |
    |---|---|---|
    | Dopuszczalnych metod HTTP | Wybrane metody zostaną usunięte | Określa, jakie metody HTTP może służyć do wywołania tej funkcji |
    | Wybrane metody HTTP | POBIERZ | Umożliwia tylko wybrane metody HTTP używanego do wywołania tej funkcji |
    | Szablon trasy | człon | Określa, jakie trasy służy do wywoływania tej funkcji |
    | Poziom dostępu | Anonimowe | Opcjonalnie: Sprawia, że funkcja dostępne bez klucza interfejsu API |

    > [!NOTE] 
    > Należy pamiętać, że nie zawiera `/api` podstawowa prefiks ścieżki w szablonie trasy, ponieważ jest to obsługiwane przez ustawienie globalne.

1. Kliknij pozycję **Zapisz**.

Dowiedz się więcej o dostosowywaniu funkcji HTTP w [powiązania HTTP funkcje platformy Azure i webhook](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook#customizing-the-http-endpoint).

### <a name="test-your-api"></a>Testowanie interfejsu API

Następnie należy przetestować funkcję wyświetlić go do pracy z nowego powierzchni interfejsu API.
1. Przejdź z powrotem do strony programowanie klikając nazwę funkcji, na lewym pasku nawigacyjnym.
1. Kliknij przycisk **uzyskać adres URL funkcji** i skopiuj adres URL. Powinny pojawić się go używa `/api/hello` teraz trasy.
1. Skopiuj adres URL do nowej karty przeglądarki lub preferowanego klienta REST. Przeglądarki użyje GET domyślnie.
1. Dodawanie parametrów do ciągu zapytania w adresie URL, np.`/api/hello/?name=John`
1. Kliknij przycisk wprowadzić, aby upewnić się, że działa. Powinny pojawić się odpowiedzi "*Hello Jan*"
1. Można też spróbować wywoływanie punkt końcowy z innej metody HTTP, aby upewnić się, że funkcja nie jest uruchomiona. W tym celu należy użyć klienta REST, takie jak cURL, Postman lub Fiddler.

## <a name="proxies-overview"></a>Omówienie serwerów proxy

W następnej sekcji zostanie powierzchni interfejsu API za pośrednictwem serwera proxy. Azure proxy funkcji służy do przekazywania żądań do innych zasobów. Zdefiniuj punkt końcowy HTTP, podobnie jak wyzwalacza HTTP, ale zamiast pisania kodu do wykonania, gdy jest wywoływana tego punktu końcowego, podaj adres URL do zdalnego wdrożenia. Umożliwia utworzenie wielu źródeł interfejsu API w jednym powierzchni interfejsu API, który ułatwia klientom korzystać. Jest to szczególnie przydatne, jeśli chcesz skompilować jako mikrousług interfejsu API.

Serwer proxy może wskazywać dowolnego zasobu HTTP, takie jak:
- Stan usługi Funkcje Azure 
- Aplikacje interfejsu API w [usługi aplikacji Azure](https://docs.microsoft.com/azure/app-service/app-service-web-overview)
- Kontenery docker w [usługi aplikacji w systemie Linux](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro)
- Inne obsługiwane interfejsu API

Aby dowiedzieć się więcej na temat serwerów proxy, zobacz [Praca z serwerów proxy funkcji Azure].

## <a name="create-your-first-proxy"></a>Tworzenie pierwszego serwera proxy

W tej sekcji utworzysz nowego serwera proxy, który służy jako frontonu do ogólnego interfejsu API. 

### <a name="setting-up-the-frontend-environment"></a>Konfigurowanie środowiska serwera sieci Web

Powtórz kroki, aby [tworzenia aplikacji funkcji](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function#create-a-function-app) Aby utworzyć nową aplikację funkcji spowoduje utworzenie serwera proxy. Adres URL tej nowej aplikacji będzie służyć jako fronton dla interfejsach API i aplikacji funkcji, które zostały wcześniej edycji będzie służyć jako wewnętrznej bazy danych.

1. Przejdź do nowej aplikacji funkcji serwera sieci Web w portalu.
1. Wybierz **funkcji platformy** i wybierz polecenie **ustawienia aplikacji**.
1. Przewiń w dół do **ustawienia aplikacji** gdzie pary klucz wartość są przechowywane i utworzyć nową ustawiania z kluczem "HELLO_HOST". Ustaw jej wartość na hoście aplikacji funkcji wewnętrznej bazy danych, takich jak `<YourBackendApp>.azurewebsites.net`. Jest to część adresu URL, które wcześniej zostały skopiowane podczas testowania funkcji HTTP. To ustawienie w konfiguracji później będziesz odwoływać.

    > [!NOTE] 
    > Ustawienia aplikacji są zalecane w przypadku konfiguracji hosta zapobiec zależności środowiska ustalony dla serwera proxy. Przy użyciu ustawień aplikacji oznacza, że konfiguracja serwera proxy można przenosić między środowiskami i zostaną zastosowane ustawienia aplikacji dla określonego środowiska.

1. Kliknij pozycję **Zapisz**.

### <a name="creating-a-proxy-on-the-frontend"></a>Tworzenie serwera proxy na frontonie

1. Przejdź z powrotem do aplikacji funkcji serwera sieci Web w portalu.
1. W obszarze nawigacji po lewej stronie, kliknij znak "+" obok "Proxy".
    ![Tworzenie serwera proxy](./media/functions-create-serverless-api/creating-proxy.png)
1. Użyj ustawień serwera proxy, jak określono w tabeli. 

    | Pole | Wartość przykładowa | Opis |
    |---|---|---|
    | Nazwa | HelloProxy | Przyjazna nazwa używana tylko w przypadku zarządzania |
    | Szablon trasy | / api/hello | Określa, jakie trasy służy do wywoływania tego serwera proxy |
    | Adres URL wewnętrznej bazy danych | https://%HELLO_HOST%/API/Hello | Określa, do którego powinien być serwerem proxy żądania punktu końcowego |
    
1. Należy pamiętać, że nie ma serwerów proxy `/api` prefiks ścieżki podstawowej i ten musi być zawarte w szablonie trasy.
1. `%HELLO_HOST%` Składni będzie odwoływać się do ustawienia aplikacji utworzone wcześniej. Funkcja oryginalnego wskaże rozpoznać adres URL.
1. Kliknij przycisk **Utwórz**.
1. Można wypróbować nowego serwera proxy, kopiując adres URL serwera Proxy i testowanie go w przeglądarce lub z ulubionych klienta HTTP.
    1. Do użycia funkcji anonimowej:
        1. `https://YOURPROXYAPP.azurewebsites.net/api/hello?name="Proxies"`
    1. Dla funkcji z użyciem autoryzacji:
        1. `https://YOURPROXYAPP.azurewebsites.net/api/hello?code=YOURCODE&name="Proxies"`

## <a name="create-a-mock-api"></a>Utwórz zasymulować interfejs API

Następnie użyje serwer proxy można utworzyć zasymulować interfejsu API dla rozwiązania. Dzięki temu programowanie klienta do postępu, bez konieczności wewnętrznej bazy danych, w pełni wdrożone. Później w rozwoju należy utworzyć nową aplikację funkcji, która obsługuje tę logikę i przekierowywania serwera proxy.

Aby utworzyć ten zasymulować interfejs API, utworzymy nowe proxy przy użyciu tego czasu [Edytor usług aplikacji](https://github.com/projectkudu/kudu/wiki/App-Service-Editor). Aby rozpocząć, przejdź do aplikacji funkcji w portalu. Wybierz **funkcji platformy** i w obszarze **narzędzi programistycznych** znaleźć **Edytor usług aplikacji**. Kliknięcie łącza zostanie otwarty Edytor usługi aplikacji na nowej karcie.

Wybierz `proxies.json` na lewym pasku nawigacyjnym. To jest plik zawierający konfigurację dla wszystkich sieci serwerów proxy. Jeśli używany jest jeden z [metody wdrażania funkcji](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment), jest to plik mają być przechowywane w kontroli źródła. Aby dowiedzieć się więcej na temat tego pliku, zobacz [proxy Zaawansowana konfiguracja](https://docs.microsoft.com/azure/azure-functions/functions-proxies#advanced-configuration).

Jeśli wykonaniu wykonanej do tej pory, proxies.json Twojego powinna wyglądać następująco:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/hello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        }
    }
}
```

Następnie dodasz zasymulować interfejsu API. Zastąp plik proxies.json następujące czynności:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/hello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        },
        "GetUserByName" : {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/users/{username}"
            },
            "responseOverrides": {
                "response.statusCode": "200",
                "response.headers.Content-Type" : "application/json",
                "response.body": {
                    "name": "{username}",
                    "description": "Awesome developer and master of serverless APIs",
                    "skills": [
                        "Serverless",
                        "APIs",
                        "Azure",
                        "Cloud"
                    ]
                }
            }
        }
    }
}
```

Spowoduje to dodanie nowego serwera proxy, "GetUserByName" bez właściwości backendUri. Zamiast kontaktować się z innym zasobem, modyfikuje domyślny z serwerów proxy przy użyciu zastąpienia odpowiedzi. Można także zastąpienia żądań i odpowiedzi w połączeniu z adresem URL wewnętrznej bazy danych. Jest to szczególnie przydatne podczas pośredniczenie dla starszej wersji systemu, której użytkownik może być konieczne zmodyfikowanie nagłówków, zapytania parametry itp. Aby dowiedzieć się więcej na temat zastąpienia żądań i odpowiedzi, zobacz [modyfikowanie żądań i odpowiedzi w proxy](https://docs.microsoft.com/azure/azure-functions/functions-proxies#a-namemodify-requests-responsesamodifying-requests-and-responses).

Testowanie zasymulować interfejsu API przez wywołanie metody `<YourProxyApp>.azurewebsites.net/api/users/{username}` punktu końcowego za pomocą przeglądarki lub Twoje ulubione klienta REST. Pamiętaj zastąpić _{username}_ z wartością ciągu reprezentujący nazwę użytkownika.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób tworzenia i dostosowywania interfejsu API na usługi Azure Functions. Przedstawiono również sposób Przełącz wielu interfejsów API, mocks, w tym razem jako powierzchnię interfejsu API unified. Te techniki służy do tworzenia interfejsów API żadnych złożoności wszystkie uruchomionej na modelu niekorzystającą obliczeniowe udostępniane przez usługi Azure Functions.

Następujące informacje mogą być pomocne podczas opracowywania dalsze interfejsu API:

- [Azure powiązania HTTP funkcje i elementu webhook](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook)
- [Praca z serwerów proxy funkcji Azure]
- [Dokumentowanie funkcje interfejsu API Azure (wersja zapoznawcza)](https://docs.microsoft.com/azure/azure-functions/functions-api-definition-getting-started)


[Create your first function]: https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function
[Praca z serwerów proxy funkcji Azure]: https://docs.microsoft.com/azure/azure-functions/functions-proxies
