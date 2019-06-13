---
title: Obsługa dużych komunikatów — usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Dowiedz się, jak obsługiwać rozmiary dużych bloków komunikatów z segmentu w usłudze Azure Logic Apps
services: logic-apps
documentationcenter: ''
author: shae-hurst
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.devlang: ''
ms.tgt_pltfrm: ''
ms.topic: article
ms.date: 4/27/2018
ms.author: shhurst
ms.openlocfilehash: 5aa5ea2a39a0fb9f969e965fed14063522197cda
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60303794"
---
# <a name="handle-large-messages-with-chunking-in-azure-logic-apps"></a>Obsługa dużych komunikatów z segmentu w usłudze Azure Logic Apps

Podczas obsługi komunikatów, Logic Apps ogranicza zawartość komunikatu do maksymalnego rozmiaru. Ten limit, pomaga zmniejszyć obciążenie utworzone przez przechowywanie i przetwarzanie dużych komunikatów. Do obsługi komunikatów przekracza ten limit, Logic Apps może *fragmentów* dużych komunikatów w mniejszych wiadomości. W ten sposób można nadal transfer dużych plików, przy użyciu aplikacji logiki w określonych warunkach. Podczas komunikowania się z innymi usługami za pomocą łączników lub HTTP, Logic Apps mogą wykorzystywać duże komunikaty, ale *tylko* we fragmentach. Ten stan oznacza, że łączniki, musi obsługiwać segmentu lub podstawowej wymianie wiadomości HTTP między aplikacjami logiki i tych usług należy użyć segmentu.

Ten artykuł pokazuje, jak możesz skonfigurować segmentu dla obsługi wiadomości, które są większe niż limit działań. Wyzwalacze aplikacji logiki nie obsługują segmentu ze względu na zwiększone obciążenie wymiany wiele wiadomości. 

## <a name="what-makes-messages-large"></a>Co sprawia, że komunikaty "large"?

Komunikaty są "duże" na podstawie usługi obsługi tych wiadomości. Limit rozmiaru dokładnie dużych komunikatów różni się między Logic Apps i łączniki. Duże komunikaty, które musi być podzielony bezpośrednio nie mogą korzystać z Logic Apps i łączniki. Aby uzyskać limit rozmiaru komunikatu Logic Apps, zobacz [Logic Apps, limity i Konfiguracja](../logic-apps/logic-apps-limits-and-config.md).
Aby limit rozmiaru komunikatu dla poszczególnych łączników, zobacz [szczegóły techniczne dotyczące łącznika](../connectors/apis-list.md).

### <a name="chunked-message-handling-for-logic-apps"></a>Podzielony komunikatów dla usługi Logic Apps

Usługa Logic Apps nie mogą korzystać bezpośrednio dane wyjściowe z fragmentaryczne wiadomości, które są większe niż limit rozmiaru komunikatu. Tylko akcje, które obsługują segmentu można uzyskać dostęp do zawartości komunikatu te dane wyjściowe. Tak, musi spełniać akcję, która obsługuje duże komunikaty *albo* poniższe kryteria:

* Natywnie obsługuje segmentu, kiedy tę akcję należy do łącznika. 
* Mieć segmentu pomocy technicznej, włączone w konfiguracji środowiska uruchomieniowego tę akcję. 

W przeciwnym razie otrzymasz błąd czasu wykonywania podczas próby dostępu do dużych zawartości danych wyjściowych. Aby włączyć segmentu, zobacz [Konfigurowanie segmentu pomocy technicznej](#set-up-chunking).

### <a name="chunked-message-handling-for-connectors"></a>Podzielony komunikatów dla łączników

Usługi, które komunikują się z usługą Logic Apps może mieć własne limity rozmiaru wiadomości. Limity te są często mniejszy niż limit Logic Apps. Rozważmy na przykład przy założeniu, że łącznik obsługuje segmentu, łącznik może wiadomości 30 MB jako dużych, a Logic Apps nie. Do wykonania limit ten łącznik, Logic Apps dzieli każdy komunikat przekracza 30 MB na mniejsze części.

Łączników, które obsługują segmentu podstawowym protokole segmentu jest niewidoczne dla użytkowników końcowych. Jednak nie wszystkie łączniki obsługują segmentu, więc te łączniki generować błędy czasu wykonywania, gdy komunikaty przychodzące przekracza ograniczenia rozmiaru łączników programu.

<a name="set-up-chunking"></a>

## <a name="set-up-chunking-over-http"></a>Konfigurowanie segmentu za pośrednictwem protokołu HTTP

W ogólnych scenariuszy HTTP można podzielić dużych plików do pobrania zawartości, a zostanie przesłany za pośrednictwem protokołu HTTP, dzięki czemu Twoja aplikacja logiki i punkt końcowy mogą wymieniać komunikaty dużych. Należy jednak Podziel komunikatów w taki sposób, który oczekuje, że Logic Apps. 

Jeśli punkt końcowy ma włączone segmentu pobieranie lub przekazywanie, akcje HTTP w aplikacji logiki automatycznie Podziel duże komunikaty. W przeciwnym razie należy zdefiniować segmentu pomocy technicznej w punkcie końcowym. Jeśli nie jesteś właścicielem lub kontrolować punktu końcowego lub łącznik, możesz utracić możliwość konfigurowania segmentu.

Ponadto jeśli akcji HTTP nie już włączone segmentu, należy również ustawić segmentu tego działania `runTimeConfiguration` właściwości. Można ustawić tę właściwość w akcji, bezpośrednio w edytorze widoku kodu, zgodnie z opisem w dalszej części lub w Projektancie aplikacji logiki zgodnie z opisem w tym miejscu:

1. W akcji HTTP w prawym górnym rogu, wybierz przycisk wielokropka ( **...** ), a następnie wybierz **ustawienia**.

   ![W akcji należy otworzyć menu Ustawienia](./media/logic-apps-handle-large-messages/http-settings.png)

2. W obszarze **transferu zawartości**ustaw **segmentu Zezwalaj** do **na**.

   ![Włącz dzielący na fragmenty](./media/logic-apps-handle-large-messages/set-up-chunking.png)

3. Aby kontynuować konfigurowanie segmentu pobieranie lub przekazywanie, kontynuuj następujące sekcje.

<a name="download-chunks"></a>

## <a name="download-content-in-chunks"></a>Pobierz zawartość we fragmentach

Wiele punktów końcowych automatycznie wysyłać dużych we fragmentach po pobraniu przez żądanie HTTP GET. Aby pobrać fragmentaryczne komunikaty z punktu końcowego za pośrednictwem protokołu HTTP, punkt końcowy musi obsługiwać częściowej zawartości żądania, lub *fragmentaryczne pliki do pobrania*. Jeśli Twoja aplikacja logiki wysyła żądanie HTTP GET do punktu końcowego na potrzeby pobierania zawartości, a punkt końcowy odpowiada za pomocą kodu stanu "206", odpowiedź zawiera fragmentarycznej zawartości. Usługa Logic Apps nie może kontrolować, czy punkt końcowy obsługuje żądania częściowe. Jednak gdy Twoja aplikacja logiki pobiera odpowiedzi najpierw "206", Twoja aplikacja logiki automatycznie wysyła wiele żądań do pobrania całej zawartości.

Aby sprawdzić, czy punkt końcowy może obsługiwać częściowej zawartości, należy wysłać żądanie HEAD. To żądanie zawarto informacje ułatwiające określenie, czy odpowiedź zawiera `Accept-Ranges` nagłówka. W ten sposób, jeśli punkt końcowy obsługuje fragmentarycznych pliki do pobrania, ale nie wysyła fragmentarycznej zawartości, możesz to zrobić *zasugerować* tę opcję, ustawiając `Range` nagłówka w żądaniu HTTP GET. 

Poniższe kroki opisują proces szczegółowe, używanych przez aplikacje logiki pobierania fragmentarycznej zawartości z punktu końcowego do aplikacji logiki:

1. Twoja aplikacja logiki wysyła żądanie HTTP GET do punktu końcowego.

   Nagłówek żądania można opcjonalnie dołączyć `Range` pola, które opisano zakresu bajtów do żądania zawartości fragmentów.

2. Punkt końcowy odpowiada za pomocą kodu stanu "206" i treści komunikatu HTTP.

    Szczegółowe informacje o zawartości w tym fragmencie są wyświetlane w odpowiedzi `Content-Range` nagłówka, w tym informacje pomocne w aplikacji logiki przed segmentu określić wartości początkowa i końcowa dla fragmentów plus łączny rozmiar całej zawartości.

3. Twoja aplikacja logiki automatycznie wysyła żądania HTTP GET monitowania.

    Aplikacja logiki wyśle kolejnego żądania GET do momentu pobrania jest całej zawartości.

Na przykład ta definicja akcji przedstawia żądanie HTTP GET, która ustawia `Range` nagłówka. Nagłówek *sugeruje* czy punkt końcowy powinien odpowiedzieć za pomocą fragmentaryczne zawartości:

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

Żądanie GET ustawia nagłówek "Range" na "bajtów = 0 1023", czyli zakres bajtów. Jeśli punkt końcowy obsługuje żądania częściowe zawartości, punkt końcowy odpowiada za pomocą fragmentów zawartości z żądanym zakresie. Oparte na punkt końcowy, dokładny format dla pola nagłówka "Zakres" może się różnić.

<a name="upload-chunks"></a>

## <a name="upload-content-in-chunks"></a>Przekaż zawartość we fragmentach

Przekazywanie fragmentarycznej zawartości z akcji HTTP, akcji muszą mieć włączone segmentu pomocy technicznej w ramach akcji `runtimeConfiguration` właściwości. Ustawienie to pozwala akcji, aby uruchomić segmentu protokołu. Twoja aplikacja logiki może wyśle komunikat początkowy POST lub PUT docelowy punkt końcowy. Po punktu końcowego odpowiada za pomocą rozmiaru fragmentu sugerowane, aplikacja logiki jest zgodny przez wysłanie żądania HTTP PATCH, zawierające zawartości fragmentów.

Poniższe kroki opisują proces szczegółowe, używanych przez aplikacje logiki do przekazywania fragmentarycznej zawartości z aplikacji logiki do punktu końcowego:

1. Twoja aplikacja logiki wysyła początkowego żądania HTTP POST lub PUT z pustą treścią komunikatu. Nagłówek żądania zawiera informacje o zawartości, która chce przekazać we fragmentach aplikację logiki:

   | Aplikacje logiki żądania pole nagłówka | Wartość | Type | Opis |
   |---------------------------------|-------|------|-------------|
   | **x-ms-transfer-mode** | fragmentaryczne | String | Wskazuje, że zawartość została przekazana we fragmentach |
   | **x-ms-content-length** | <*content-length*> | Integer | Całą zawartość rozmiar w bajtach przed dzielący na fragmenty |
   ||||

2. Punkt końcowy odpowiada za pomocą "200" pomyślny kod statusu i tych informacji opcjonalnych:

   | Pola nagłówka odpowiedzi punktu końcowego | Type | Wymagane | Opis |
   |--------------------------------|------|----------|-------------|
   | **x-ms-chunk-size** | Integer | Nie | Rozmiar fragmentu sugerowane w bajtach |
   | **Lokalizacja** | String | Nie | Adres URL lokalizacji, gdzie wysyłać wiadomości HTTP PATCH |
   ||||

3. Twoja aplikacja logiki tworzy i wysyła kolejnych komunikatów HTTP PATCH -, każdy z tych informacji:

   * Na podstawie zawartości fragmentu **x-ms rozmiaru fragmentu** lub niektóre wewnętrznie obliczony rozmiar, aż wszystkie zawartości sumowanie **x-ms-content-length** po kolei jest przekazywany.

   * Te szczegółowe informacje o nagłówku o zawartości fragmentu, wysyłanych w każdym komunikacie poprawki:

     | Aplikacje logiki żądania pole nagłówka | Wartość | Type | Opis |
     |---------------------------------|-------|------|-------------|
     | **Content-Range** | <*range*> | String | Zakres bajtów dla bieżącego fragmentu zawartości, łącznie z wartością początkową końcową wartość i łączny rozmiar zawartości, na przykład: "bajtów = 0-1023/10100" |
     | **Typ zawartości** | <*content-type*> | String | Typ zawartości, fragmentaryczne |
     | **Długość zawartości** | <*content-length*> | String | Długość Rozmiar w bajtach bieżąca fragmentów |
     |||||

4. Po wykonaniu każdego żądania PATCH punktu końcowego potwierdza potwierdzenia dla każdego fragmentu odpowiedzi z kodem stanu "200".

Na przykład ta definicja akcji przedstawia żądanie HTTP POST do przekazywania fragmentarycznej zawartości do punktu końcowego. W tym działaniu `runTimeConfiguration` właściwości `contentTransfer` zestawy właściwości `transferMode` do `chunked`:

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