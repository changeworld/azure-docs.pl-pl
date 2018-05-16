---
title: Obsługa dużych wiadomości w usłudze Azure Logic Apps | Dokumentacja firmy Microsoft
description: Informacje o sposobie obsługi rozmiary dużych komunikatów z podziału w aplikacjach logiki
services: logic-apps
documentationcenter: ''
author: shae-hurst
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.devlang: ''
ms.tgt_pltfrm: ''
ms.topic: article
ms.date: 4/27/2018
ms.author: shhurst
ms.openlocfilehash: a99fbdd7c9beb32f640d5ca623f8bcda3cb9aba4
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="handle-large-messages-with-chunking-in-logic-apps"></a>Obsługa dużych wiadomości z podziału w aplikacjach logiki

Podczas obsługi wiadomości, Logic Apps ogranicza zawartość komunikatu do maksymalnego rozmiaru. Ten limit pomaga zmniejszyć obciążenie utworzone przez przechowywania i przetwarzania dużych wiadomości. Do obsługi wiadomości większych niż ten limit, Logic Apps można *fragmentu* dużych komunikatów w mniejszych wiadomości. W ten sposób można nadal transferu dużych plików za pomocą aplikacji logiki w określonych warunkach. Podczas komunikacji z innymi usługami za pomocą łączników lub protokołu HTTP, Logic Apps, jaką może wykorzystać dużych wiadomości, ale *tylko* w fragmentów. Ten stan oznacza, że łączniki muszą również obsługiwać podziału lub podstawowej wymiany wiadomości HTTP między te usługi i aplikacje logiki muszą używać podziału.

W tym artykule opisano, jak możesz skonfigurować obsługę komunikatów, które są większe niż limit podziału.

## <a name="what-makes-messages-large"></a>Dzięki czemu wiadomości "duże"?

Wiadomości są "duże" na podstawie usługi obsługi tych wiadomości. Limit rozmiaru dokładne na dużych wiadomości różni się między łączników i aplikacji logiki. Duże wiadomości, które muszą być fragmentaryczne bezpośrednio nie mogą korzystać z łączników i aplikacji logiki. Aby limit rozmiaru wiadomości Logic Apps, zobacz [limity Logic Apps i konfiguracji](../logic-apps/logic-apps-limits-and-config.md).
Dla poszczególnych łączników limit rozmiaru wiadomości, zobacz [szczegółowe informacje techniczne łącznika](../connectors/apis-list.md).

### <a name="chunked-message-handling-for-logic-apps"></a>Obsługa dla usługi Logic Apps podzielony komunikatów

Logic Apps nie mogą bezpośrednio używać dane wyjściowe z podzielony wiadomości, które są większe niż limit rozmiaru wiadomości. Tylko akcje, które obsługują podziału dostęp do zawartości komunikatu w tych danych wyjściowych. Tak, musi spełniać akcję, która obsługuje duże wiadomości *albo* tych kryteriów:

* Natywnie obsługuje podziału, gdy ta akcja należy do łącznika. 
* Ma podziału Obsługa włączona w konfiguracji środowiska uruchomieniowego tej akcji. 

W przeciwnym razie błąd środowiska uruchomieniowego podczas próby uzyskania dostępu do dużych zawartości danych wyjściowych. Aby włączyć podziału, zobacz [Konfigurowanie podziału obsługi](#set-up-chunking).

### <a name="chunked-message-handling-for-connectors"></a>Obsługa łączników podzielony komunikatów

Usługi, które komunikują się z usługą Logic Apps może mieć własne limity rozmiaru komunikatu. Ograniczenia te są często mniejszy niż limit Logic Apps. Rozważmy na przykład przy założeniu, że łącznik obsługuje podziału, łącznika może wiadomości 30 MB jako duży, a nie aplikacji logiki. Do wykonania tego łącznika limit, Logic Apps dzieli dowolny komunikat o większym niż 30 MB na mniejsze fragmenty.

Łączników, które obsługują podziału podstawowy protokół segmentu jest niewidoczny dla użytkowników końcowych. Niestety nie wszystkie łączniki obsługuje podziału, więc te łączniki generować błędy środowiska wykonawczego wiadomości przychodzących przekracza ograniczenia rozmiaru działania łączników.

<a name="set-up-chunking"></a>

## <a name="set-up-chunking-over-http"></a>Konfigurowanie podziału za pośrednictwem protokołu HTTP

W scenariuszach ogólnego HTTP można podzielić dużych pobierania zawartości i przekazuje za pośrednictwem protokołu HTTP, dzięki czemu aplikację logiki i punktu końcowego mogą wymieniać komunikaty dużych. Należy jednak bryłkach wiadomości w taki sposób, który oczekuje Logic Apps. 

Jeśli punkt końcowy ma włączone podziału pliki do pobrania lub przekazywania, akcje HTTP w aplikacji logiki automatycznie bryłkach dużych wiadomości. W przeciwnym razie należy skonfigurować podziału pomocy technicznej w punkcie końcowym. Jeśli nie jesteś właścicielem lub kontrolować punktu końcowego lub łącznika, nie masz opcję, aby skonfigurować podziału.

Ponadto jeśli akcji HTTP nie już włączone podziału, należy również skonfigurować podziału tego działania `runTimeConfiguration` właściwości. Ta właściwość wewnątrz działania, można ustawić bezpośrednio w edytorze widoku kodu, jak opisano wcześniej, albo w Projektancie aplikacji logiki, zgodnie z opisem w tym miejscu:

1. Akcja HTTP prawym górnym rogu, wybierz przycisk wielokropka (**...** ), a następnie wybierz pozycję **ustawienia**.

   ![Akcję należy otworzyć menu Ustawienia](./media/logic-apps-handle-large-messages/http-settings.png)

2. W obszarze **transferu zawartości**ustaw **podziału Zezwalaj** do **na**.

   ![Włącz funkcję podziału](./media/logic-apps-handle-large-messages/set-up-chunking.png)

3. Aby kontynuować konfigurowanie podziału pliki do pobrania lub przekazywania, nadal następujące sekcje.

<a name="download-chunks"></a>

## <a name="download-content-in-chunks"></a>Pobieranie zawartości w fragmentów

Wiele punktów końcowych automatycznie wysyłaj dużych wiadomości w fragmentów, gdy pobrane za pomocą żądania HTTP GET. Aby pobrać podzielony komunikaty z punktu końcowego za pośrednictwem protokołu HTTP, punkt końcowy musi obsługiwać częściowe żądań zawartości, lub *fragmentaryczne pliki do pobrania*. Gdy aplikację logiki wysyła żądanie HTTP GET do punktu końcowego w celu pobierania zawartości, a punkt końcowy odpowiedzi z kodem stanu "206", odpowiedź zawiera podzielony zawartości. Logic Apps nie może kontrolować, czy punkt końcowy obsługuje żądania częściowej. Jednak gdy aplikację logiki pobiera odpowiedź najpierw "206", aplikację logiki automatycznie wysyła wiele żądań do Pobierz całą zawartość.

Aby sprawdzić, czy punkt końcowy może obsługiwać zawartość częściowa, wysłanie żądania HEAD. To żądanie ułatwia określenie, czy odpowiedź zawiera `Accept-Ranges` nagłówka. W ten sposób, jeśli punkt końcowy obsługuje podzielony pliki do pobrania, ale nie powoduje wysłania zawartości podzielony, możesz *Sugeruj* tę opcję, ustawiając `Range` nagłówka w żądaniu HTTP GET. 

Te kroki opisano szczegółowe procesu, który korzysta z aplikacji logiki pobierania podzielony zawartości z punktu końcowego do aplikacji logiki:

1. Aplikację logiki wysyła żądanie HTTP GET do punktu końcowego.

   Nagłówek żądania można opcjonalnie dołączyć `Range` opisujący zakresu bajtów żądanych fragmentów zawartości.

2. Punkt końcowy odpowiada kodu stanu "206" i treści komunikatu HTTP.

    Szczegółowe informacje o zawartości w tym fragmencie są wyświetlane w odpowiedzi `Content-Range` przed podziału nagłówka, w tym informacje pomocne w aplikacji logiki określić początkową i końcową dla fragmentów plus łączny rozmiar całej zawartości.

3. Aplikację logiki automatycznie wyśle kolejnego żądania HTTP GET.

    Aplikację logiki wysyła kolejnego żądania GET do momentu pobrania całej zawartości.

Na przykład ta definicja działania zawiera żądanie HTTP GET, która ustawia `Range` nagłówka. Nagłówek *sugeruje* powinien odpowiadać punktu końcowego, podając fragmentaryczne zawartości:

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

Żądanie GET ustawia nagłówek "Range" "bajtów = 0 1023", który jest zakresu bajtów. Jeśli punkt końcowy obsługuje żądania zawartości częściowej, punkt końcowy odpowiada fragmentu zawartości z żądanym zakresie. Oparte na punkt końcowy, dokładnego formatu do pola nagłówka "Zakres" może się różnić.

<a name="upload-chunks"></a>

## <a name="upload-content-in-chunks"></a>Przekaż zawartość fragmentów

Aby przekazać podzielony zawartość z akcji HTTP, akcji najpierw włączyć segmentu obsługi za pomocą akcji `runtimeConfiguration` właściwości. Ustawienie to pozwala akcji można uruchomić protokołu segmentu. Aplikację logiki można następnie wysłać wiadomość początkowej POST i PUT do docelowego punktu końcowego. Po punktu końcowego odpowiada rozmiaru fragmentu sugerowane, aplikację logiki następuje przez wysyłanie żądania HTTP PATCH zawierających fragmenty zawartości.

Te kroki opisano szczegółowe procesu, który korzysta z aplikacji logiki do przekazywania zawartości podzielony z aplikacji logiki do punktu końcowego:

1. Aplikację logiki wysyła początkowe żądanie HTTP POST i PUT z pustą treść. Nagłówek żądania zawiera te informacje o zawartości, które chce przekazać w fragmentów aplikację logiki:

   | Pola nagłówka żądania Logic Apps | Wartość | Typ | Opis |
   |---------------------------------|-------|------|-------------|
   | **x-ms-— tryb transferu** | fragmentaryczne | Ciąg | Wskazuje, że zawartość została przekazana w fragmentów |
   | **x-ms-content-length.** | <*Długość zawartości*> | Liczba całkowita | Całej zawartości rozmiar w bajtach przed podziału |
   ||||

2. Punkt końcowy odpowie "200" Kod stanu powodzenie oraz opcjonalne informacje:

   | Pola nagłówka odpowiedzi punktu końcowego | Typ | Wymagane | Opis |
   |--------------------------------|------|----------|-------------|
   | **x-ms rozmiar fragmentu** | Liczba całkowita | Nie | Rozmiar fragmentu sugerowane w bajtach |
   | **Lokalizacja** | Ciąg | Nie | Adres URL lokalizacji where do wysyłania wiadomości HTTP PATCH |
   ||||

3. Aplikację logiki, tworzy i wysyła kolejnych komunikatów HTTP PATCH — każdy z tych informacji:

   * Na podstawie zawartości fragmentu **x-ms rozmiar fragmentu** lub niektóre wewnętrznie obliczony rozmiar do wszystkich zawartości sumowanie **x-ms-content-length** sekwencyjnie przekazaniu

   * Te informacje nagłówka dotyczące zawartości fragmentu, wysyłane w każdej wiadomości poprawki:

     | Pola nagłówka żądania Logic Apps | Wartość | Typ | Opis |
     |---------------------------------|-------|------|-------------|
     | **Zakres zawartości** | <*Zakres*> | Ciąg | Zakres bajtów dla bieżącego fragmentu zawartości, w tym wartości początkowej, końcową wartość i łączny rozmiar zawartości, na przykład: "bajtów = 0-1023/10100" |
     | **Typ zawartości** | <*Typ zawartości*> | Ciąg | Typ zawartości podzielony |
     | **Content-Length.** | <*Długość zawartości*> | Ciąg | Długość Rozmiar w bajtach bieżącego fragmentu |
     |||||

4. Po każdym żądaniu poprawki punktu końcowego potwierdza potwierdzenia dla każdego fragmentu odpowiedzi z kodem stanu "200".

Na przykład ta definicja działania zawiera żądanie HTTP POST do przekazywania podzielony zawartości do punktu końcowego. W akcji `runTimeConfiguration` właściwość `contentTransfer` zestawy właściwości `transferMode` do `chunked`:

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