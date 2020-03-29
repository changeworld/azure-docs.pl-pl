---
title: Obsługa dużych wiadomości przy użyciu fragmentowania
description: Dowiedz się, jak obsługiwać duże rozmiary wiadomości przy użyciu fragmentowania w zautomatyzowanych zadaniach i przepływach pracy utworzonych za pomocą aplikacji Azure Logic Apps
services: logic-apps
ms.suite: integration
author: shae-hurst
ms.author: shhurst
ms.topic: article
ms.date: 12/03/2019
ms.openlocfilehash: 81e7c12b04c1ebd9691c11d76f387f7d42490180
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75456558"
---
# <a name="handle-large-messages-with-chunking-in-azure-logic-apps"></a>Obsługa dużych wiadomości z fragmentowania w usłudze Azure Logic Apps

Podczas obsługi wiadomości aplikacje logiki ogranicza zawartość wiadomości do maksymalnego rozmiaru. Ten limit pomaga zmniejszyć obciążenie utworzone przez przechowywanie i przetwarzanie dużych komunikatów. Do obsługi wiadomości większych niż ten limit, logic apps można *podzielić* dużą wiadomość na mniejsze wiadomości. W ten sposób nadal można przesyłać duże pliki przy użyciu aplikacji logiki w określonych warunkach. Podczas komunikowania się z innymi usługami za pośrednictwem łączników lub HTTP, aplikacje logiki mogą korzystać z dużych wiadomości, ale *tylko* w fragmentach. Ten warunek oznacza łączniki muszą również obsługiwać fragmentowanie lub podstawowej wymiany komunikatów HTTP między aplikacjami logiki i tych usług należy używać fragmentowania.

W tym artykule pokazano, jak można skonfigurować fragmentowanie dla akcji obsługi wiadomości, które są większe niż limit. Wyzwalacze aplikacji logiki nie obsługują fragmentowania ze względu na zwiększone obciążenie związane z wymianą wielu komunikatów. 

## <a name="what-makes-messages-large"></a>Co sprawia, że wiadomości są "duże"?

Wiadomości są "duże" na podstawie usługi obsługi tych wiadomości. Dokładny limit rozmiaru dużych wiadomości różni się w aplikacjach logiki i łącznikach. Aplikacje logiki i łączniki nie mogą bezpośrednio używać dużych komunikatów, które muszą być fragmentowane. Aby zapoznać się z limitem rozmiaru komunikatu Aplikacje logiki, zobacz [Limity i konfiguracja aplikacji logiki](../logic-apps/logic-apps-limits-and-config.md).
Informacje na temat limitu rozmiaru komunikatu każdego złącza można znaleźć w [szczegółach technicznych łącznika](../connectors/apis-list.md).

### <a name="chunked-message-handling-for-logic-apps"></a>Obsługa wiadomości fragmentowanych dla aplikacji logiki

Aplikacje logiki nie można bezpośrednio używać wyjść z wiadomości fragmentowanych, które są większe niż limit rozmiaru wiadomości. Tylko akcje, które obsługują fragmentowanie można uzyskać dostęp do zawartości wiadomości w tych danych wyjściowych. Tak więc akcja, która obsługuje duże wiadomości musi *spełniać* następujące kryteria:

* Natywnie obsługuje fragmentowanie, gdy ta akcja należy do łącznika. 
* Mieć obsługę fragmentowania włączone w konfiguracji środowiska uruchomieniowego tej akcji. 

W przeciwnym razie podczas próby uzyskania dostępu do dużych danych wyjściowych zawartości jest wyświetlony błąd środowiska uruchomieniowego. Aby włączyć tworzenie fragmentów, zobacz [Konfigurowanie obsługi fragmentów](#set-up-chunking).

### <a name="chunked-message-handling-for-connectors"></a>Obsługa fragmentów dla łączników

Usługi, które komunikują się z aplikacjami logiki może mieć własne limity rozmiaru wiadomości. Te limity są często mniejsze niż limit aplikacji logiki. Na przykład przy założeniu, że łącznik obsługuje fragmentowanie, łącznik może rozważyć komunikat 30 MB jako duży, podczas gdy aplikacje logiki nie. Aby zapewnić zgodność z limitem tego łącznika, logic apps dzieli każdą wiadomość większą niż 30 MB na mniejsze fragmenty.

W przypadku łączników obsługujących fragmentowanie podstawowy protokół fragmentowania jest niewidoczny dla użytkowników końcowych. Jednak nie wszystkie łączniki obsługują fragmentowanie, więc te łączniki generują błędy środowiska uruchomieniowego, gdy przychodzące komunikaty przekraczają limity rozmiaru łączników.

> [!NOTE]
> W przypadku akcji, które używają fragmentowania, nie można przekazać `@triggerBody()?['Content']` treści wyzwalacza ani używać wyrażeń, takich jak w tych akcjach. Zamiast tego w przypadku zawartości pliku tekstowego lub JSON można spróbować użyć akcji [ **Redagowanie** ](../logic-apps/logic-apps-perform-data-operations.md#compose-action) lub [utworzyć zmienną](../logic-apps/logic-apps-create-variables-store-values.md) do obsługi tej zawartości. Jeśli treść wyzwalacza zawiera inne typy zawartości, takie jak pliki multimedialne, należy wykonać inne kroki, aby obsłużyć tę zawartość.

<a name="set-up-chunking"></a>

## <a name="set-up-chunking-over-http"></a>Konfigurowanie fragmentowania za pośrednictwem protokołu HTTP

W ogólnych scenariuszach HTTP można podzielić pliki do pobrania i przekazywanie dużej zawartości za pośrednictwem protokołu HTTP, dzięki czemu aplikacja logiki i punkt końcowy mogą wymieniać duże wiadomości. Jednak należy fragment wiadomości w sposób, który oczekuje aplikacji logiki. 

Jeśli punkt końcowy włączył fragmentowanie do pobrania lub przekazywania, akcje HTTP w aplikacji logiki automatycznie fragmentują duże wiadomości. W przeciwnym razie należy skonfigurować obsługę fragmentowania w punkcie końcowym. Jeśli nie jesteś właścicielem lub kontrolować punkt końcowy lub łącznik, może nie mieć opcji, aby skonfigurować fragmentowanie.

Ponadto jeśli akcja HTTP nie włącza jeszcze fragmentowania, należy również skonfigurować fragmentowanie we `runTimeConfiguration` właściwości akcji. Tę właściwość można ustawić wewnątrz akcji, bezpośrednio w edytorze widoku kodu, jak opisano później, lub w Projektancie aplikacji logiki, jak opisano w tym miejscu:

1. W prawym górnym rogu akcji HTTP wybierz przycisk wielokropka (**...**), a następnie wybierz pozycję **Ustawienia**.

   ![W akcji otwórz menu ustawień](./media/logic-apps-handle-large-messages/http-settings.png)

2. W obszarze **Transfer zawartości**ustaw **opcję Zezwalaj na fragmentowanie** **na Włączone**.

   ![Włączanie tworzenia fragmentów](./media/logic-apps-handle-large-messages/set-up-chunking.png)

3. Aby kontynuować konfigurowanie fragmentowania do pobrania lub przesłania, przejdź do poniższych sekcji.

<a name="download-chunks"></a>

## <a name="download-content-in-chunks"></a>Pobieranie zawartości w kawałkach

Wiele punktów końcowych automatycznie wysyłać duże wiadomości w fragmentach po pobraniu za pośrednictwem żądania HTTP GET. Aby pobrać wiadomości fragmentowane z punktu końcowego za pośrednictwem protokołu HTTP, punkt końcowy musi obsługiwać żądania częściowej zawartości lub *pliki do pobrania w fragmentach.* Gdy aplikacja logiki wysyła żądanie HTTP GET do punktu końcowego do pobierania zawartości, a punkt końcowy odpowiada kodem stanu "206", odpowiedź zawiera zawartość fragmentową. Aplikacje logiki nie można kontrolować, czy punkt końcowy obsługuje żądania częściowe. Jednak gdy aplikacja logiki pobiera pierwszą odpowiedź "206", aplikacja logiki automatycznie wysyła wiele żądań, aby pobrać całą zawartość.

Aby sprawdzić, czy punkt końcowy może obsługiwać częściową zawartość, wyślij żądanie HEAD. To żądanie pomaga określić, `Accept-Ranges` czy odpowiedź zawiera nagłówek. W ten sposób, jeśli punkt końcowy obsługuje fragmentory pobrania, ale nie wysyła zawartości `Range` fragmentowane, można *zaproponować* tę opcję, ustawiając nagłówek w żądaniu HTTP GET. 

W tych krokach opisano szczegółowy proces, którego aplikacje logiki używa do pobierania zawartości fragmentowanych z punktu końcowego do aplikacji logiki:

1. Aplikacja logiki wysyła żądanie HTTP GET do punktu końcowego.

   Nagłówek żądania może opcjonalnie `Range` zawierać pole opisujące zakres bajtów do żądania fragmentów zawartości.

2. Punkt końcowy odpowiada kodem stanu "206" i treścią wiadomości HTTP.

    Szczegóły dotyczące zawartości w tym fragmencie pojawiają się w `Content-Range` nagłówku odpowiedzi, w tym informacje, które pomaga logic apps określić początek i koniec dla fragmentu, a także całkowity rozmiar całej zawartości przed fragmentem.

3. Aplikacja logiki automatycznie wysyła kolejne żądania HTTP GET.

    Aplikacja logiki wysyła żądania GET uzupełniające, dopóki nie zostanie pobrana cała zawartość.

Na przykład ta definicja akcji pokazuje żądanie `Range` HTTP GET, które ustawia nagłówek. Nagłówek *sugeruje,* że punkt końcowy powinien odpowiadać z zawartością fragmentami:

```json
"getAction": {
    "inputs": {
        "headers": {
            "Range": "bytes=0-1023"
        },
       "method": "GET",
       "uri": "http://myAPIendpoint/api/downloadContent"
    },
    "runAfter": {},
    "type": "Http"
}
```

Żądanie GET ustawia nagłówek "Zakres" na "bytes=0-1023", który jest zakresem bajtów. Jeśli punkt końcowy obsługuje żądania częściowej zawartości, punkt końcowy odpowiada fragmentem zawartości z żądanego zakresu. Na podstawie punktu końcowego dokładny format pola nagłówka "Zakres" może się różnić.

<a name="upload-chunks"></a>

## <a name="upload-content-in-chunks"></a>Przekazywanie zawartości w fragmentach

Aby przekazać zawartość fragmentową z akcji HTTP, akcja musi mieć włączoną obsługę fragmentów za pośrednictwem `runtimeConfiguration` właściwości akcji. To ustawienie umożliwia akcję, aby uruchomić protokół fragmentowania. Aplikacja logiki można następnie wysłać początkowy post lub put komunikat do docelowego punktu końcowego. Po punkt końcowy odpowiada z sugerowanego rozmiaru fragmentu aplikacji logiki następnie przez wysłanie żądań POPRAWKI HTTP, które zawierają fragmenty zawartości.

W tych krokach opisano szczegółowy proces, którego aplikacje logiki używa do przekazywania zawartości fragmentowanych z aplikacji logiki do punktu końcowego:

1. Aplikacja logiki wysyła początkowe żądanie HTTP POST lub PUT z pustą treścią wiadomości. Nagłówek żądania zawiera te informacje o zawartości, którą aplikacja logiki chce przekazać we fragmentach:

   | Pole nagłówka żądania aplikacji logiki | Wartość | Typ | Opis |
   |---------------------------------|-------|------|-------------|
   | **tryb x-ms-transfer** | Fragmentaryczne | Ciąg | Wskazuje, że zawartość jest przekazytana w fragmentach |
   | **x-ms-content-length** | <*długość zawartości*> | Liczba całkowita | Cały rozmiar zawartości w bajtach przed fragmentami |
   ||||

2. Punkt końcowy odpowiada kodem stanu sukcesu "200" i tymi opcjonalnymi informacjami:

   | Pole nagłówka odpowiedzi punktu końcowego | Typ | Wymagany | Opis |
   |--------------------------------|------|----------|-------------|
   | **x-ms-chunk-size** | Liczba całkowita | Nie | Sugerowany rozmiar fragmentu w bajtach |
   | **Lokalizacja** | Ciąg | Tak | Lokalizacja adresu URL, gdzie mają być wysyłane wiadomości HTTP PATCH |
   ||||

3. Aplikacja logiki tworzy i wysyła kolejne wiadomości HTTP PATCH - każdy z tymi informacjami:

   * Fragment zawartości oparty na **rozmiarze fragmentu x-ms** lub pewnym wewnętrznie obliczonym rozmiarze, dopóki cała zawartość o łącznej **długości x-ms-content nie** zostanie kolejno przesłana

   * Te szczegóły nagłówka o fragmencie zawartości wysyłane w każdej wiadomości PATCH:

     | Pole nagłówka żądania aplikacji logiki | Wartość | Typ | Opis |
     |---------------------------------|-------|------|-------------|
     | **Zakres zawartości** | <*Zakres*> | Ciąg | Zakres bajtów dla bieżącego fragmentu zawartości, w tym wartość początkowa, wartość końcowa i całkowity rozmiar zawartości, na przykład: "bajty = 0-1023/10100" |
     | **Typ zawartości** | <*typ zawartości*> | Ciąg | Typ zawartości fragmentowanych |
     | **Długość zawartości** | <*długość zawartości*> | Ciąg | Długość rozmiaru w bajtach bieżącego fragmentu |
     |||||

4. Po każdym żądaniu PATCH punkt końcowy potwierdza przyjęcie dla każdego fragmentu, odpowiadając za pomocą kodu stanu "200" i następujących nagłówków odpowiedzi:

   | Pole nagłówka odpowiedzi punktu końcowego | Typ | Wymagany | Opis |
   |--------------------------------|------|----------|-------------|
   | **Zakres** | Ciąg | Tak | Zakres bajtów dla zawartości odebranej przez punkt końcowy, na przykład: "bajty= 0-1023" |   
   | **x-ms-chunk-size** | Liczba całkowita | Nie | Sugerowany rozmiar fragmentu w bajtach |
   ||||

Na przykład ta definicja akcji pokazuje żądanie HTTP POST do przekazywania zawartości fragmentowane do punktu końcowego. We `runTimeConfiguration` właściwości akcji `contentTransfer` właściwość ustawia `transferMode` `chunked`się na:

```json
"postAction": {
    "runtimeConfiguration": {
        "contentTransfer": {
            "transferMode": "chunked"
        }
    },
    "inputs": {
        "method": "POST",
        "uri": "http://myAPIendpoint/api/action",
        "body": "@body('getAction')"
    },
    "runAfter": {
    "getAction": ["Succeeded"]
    },
    "type": "Http"
}
```
