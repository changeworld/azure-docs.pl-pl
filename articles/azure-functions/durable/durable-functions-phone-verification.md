---
title: Interakcja ludzka i przedziały czasu w Durable Functions na platformie Azure
description: Dowiedz się, jak obsłużyć interakcję człowieka i limity czasu w rozszerzeniu Durable Functions Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 3918c37d985c6766fe6ad4601b70ddbd4597b0ba
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087148"
---
# <a name="human-interaction-in-durable-functions---phone-verification-sample"></a>Interakcja przez człowieka w przykładowej weryfikacji Durable Functions-telefonie

Ten przykład pokazuje, jak utworzyć aranżację [Durable Functions](durable-functions-overview.md) , która obejmuje interakcję człowieka. Za każdym razem, gdy w procesie zautomatyzowanym jest używana prawdziwa osoba, proces musi być w stanie wysyłać powiadomienia do osoby i odbierać odpowiedzi asynchronicznie. Musi również zezwalać na możliwość, że osoba jest niedostępna. (Ostatnia część to miejsce, w którym limity czasu stają się ważne).

Ten przykład implementuje system weryfikacyjny telefonu oparty na programie SMS. Te typy przepływów są często używane podczas weryfikowania numeru telefonu klienta lub uwierzytelniania wieloskładnikowego (MFA). Jest to zaawansowany przykład, ponieważ cała implementacja odbywa się przy użyciu kilku małych funkcji. Nie jest wymagany żaden zewnętrzny magazyn danych, na przykład baza danych.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Omówienie scenariusza

Weryfikacja telefoniczna służy do sprawdzania, czy użytkownicy końcowi Twojej aplikacji nie są spamerami oraz czy mówią o nich. Uwierzytelnianie wieloskładnikowe to typowy przypadek użycia służący do ochrony kont użytkowników przed hakerami. Wyzwanie przy wdrażaniu własnej weryfikacji telefonu polega na tym, że wymaga **interakcji stanowej** ze zdrowiem ludzkim. Użytkownik końcowy zazwyczaj udostępnia jakiś kod (na przykład 4-cyfrowy numer) i musi reagować **w rozsądnym czasie**.

Zwykłe Azure Functions są bezstanowe (tak jak wiele innych punktów końcowych w chmurze na innych platformach), więc te typy interakcji obejmują jawnie zarządzanie stanem zewnętrznym w bazie danych lub w innym magazynie trwałym. Ponadto interakcja musi być dzielona na wiele funkcji, które mogą być skoordynowane ze sobą. Na przykład potrzebna jest co najmniej jedna funkcja do wybierania kodu, zachowywania go w miejscu i wysyłania go do telefonu użytkownika. Ponadto potrzebna jest co najmniej jedna inna funkcja, która otrzymuje odpowiedź od użytkownika, a następnie mapuje ją z powrotem do oryginalnego wywołania funkcji w celu sprawdzenia poprawności kodu. Przekroczenie limitu czasu jest również ważnym aspektem zapewniania bezpieczeństwa. Dzięki temu można szybko uzyskać dość skomplikowaną.

Złożoność tego scenariusza znacznie zmniejsza się w przypadku korzystania z Durable Functions. Jak widać w tym przykładzie, funkcja programu Orchestrator będzie mogła łatwo zarządzać interakcją stanową i bez powiązana z zewnętrznymi magazynami danych. Ponieważ funkcje programu Orchestrator są *trwałe*, te interaktywne przepływy są również wysoce niezawodne.

## <a name="configuring-twilio-integration"></a>Konfigurowanie integracji Twilio

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>Funkcje

W tym artykule przedstawiono następujące funkcje w przykładowej aplikacji:

* **E4_SmsPhoneVerification**
* **E4_SendSmsChallenge**

W poniższych sekcjach opisano konfigurację i kod, które są używane C# do obsługi skryptów i języka JavaScript. Kod dla projektowania programu Visual Studio znajduje się na końcu artykułu.

## <a name="the-sms-verification-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>Aranżacja weryfikacji SMS (Visual Studio Code i Azure Portal przykładowy kod)

Funkcja **E4_SmsPhoneVerification** używa standardowego pliku *Function. JSON* dla funkcji programu Orchestrator.

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/function.json)]

Oto kod implementujący funkcję:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (tylko funkcje 2. x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/index.js)]

Po uruchomieniu ta funkcja programu Orchestrator wykonuje następujące czynności:

1. Pobiera numer telefonu, do którego wyśle powiadomienie SMS.
2. Wywołuje **E4_SendSmsChallenge** do wysłania wiadomości SMS do użytkownika i zwraca oczekiwany 4-cyfrowy kod wyzwania.
3. Tworzy trwały czasomierz wyzwalający 90 sekund od bieżącego czasu.
4. Równolegle z czasomierzem czeka na zdarzenie **SmsChallengeResponse** od użytkownika.

Użytkownik otrzymuje wiadomość SMS z kodem zawierającym cztery cyfry. Mają one 90 sekund do wysłania tego samego 4-cyfrowego kodu z powrotem do wystąpienia funkcji programu Orchestrator w celu ukończenia procesu weryfikacji. Jeśli przesyłają niewłaściwy kod, uzyskują oni dodatkowe trzy próby pobrania jej w prawo (w tym samym oknie 90 sekund).

> [!NOTE]
> Może nie być oczywisty w pierwszej kolejności, ale ta funkcja programu Orchestrator jest całkowicie deterministyczna. Wynika to z `CurrentUtcDateTime` faktu, że właściwości `currentUtcDateTime` (.NET) i (JavaScript) są używane do obliczania czasu wygaśnięcia czasomierza, a te właściwości zwracają tę samą wartość przy każdym odwróceniu w tym punkcie w kodzie programu Orchestrator. Jest to ważne, aby upewnić się `winner` , że te same wyniki są `Task.WhenAny` wykonywane z każdego Powtórzonego wywołania do (.NET) lub `context.df.Task.any` (JavaScript).

> [!WARNING]
> Ważne jest, aby [anulować czasomierze](durable-functions-timers.md) , jeśli nie są już potrzebne, jak w powyższym przykładzie, gdy odpowiedź na wyzwanie zostanie zaakceptowana.

## <a name="send-the-sms-message"></a>Wyślij wiadomość SMS

Funkcja **E4_SendSmsChallenge** używa powiązania Twilio do wysyłania wiadomości SMS z 4-cyfrowym kodem do użytkownika końcowego. *Funkcja. JSON* jest definiowana w następujący sposób:

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/function.json)]

A Oto kod generujący 4-cyfrowy kod testu i wysyła komunikat SMS:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (tylko funkcje 2. x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/index.js)]

Ta funkcja **E4_SendSmsChallenge** jest wywoływana tylko raz, nawet jeśli proces ulegnie awarii lub zostanie odtworzony. Jest to dobre, ponieważ użytkownik końcowy nie powinien otrzymywać wielu wiadomości SMS. Wartość `challengeCode` zwracana jest automatycznie utrwalana, więc funkcja Orchestrator zawsze wie, co jest prawidłowy kod.

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Korzystając z funkcji wyzwalanych przez protokół HTTP zawartych w przykładzie, można uruchomić aranżację, wysyłając następujące żądanie HTTP POST:

```
POST http://{host}/orchestrators/E4_SmsPhoneVerification
Content-Length: 14
Content-Type: application/json

"+1425XXXXXXX"
```

```
HTTP/1.1 202 Accepted
Content-Length: 695
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"id":"741c65651d4c40cea29acdd5bb47baf1","statusQueryGetUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","sendEventPostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","terminatePostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}"}
```

Funkcja Orchestrator odbiera dostarczony numer telefonu i natychmiast wysyła wiadomość SMS z losowo wygenerowanym 4-cyfrowym kodem &mdash; weryfikacyjnym na przykład *2168*. Funkcja następnie czeka 90 sekund na odpowiedź.

Aby odpowiedzieć na kod, możesz użyć [ `RaiseEventAsync` (.NET) `raiseEvent` lub (JavaScript)](durable-functions-instance-management.md) wewnątrz innej funkcji lub wywołać element webhook **sendEventUrl** http, do którego odwołuje się powyżej 202 powyżej, zastępując `{eventName}` nazwę `SmsChallengeResponse`zdarzenie:

```
POST http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/SmsChallengeResponse?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Length: 4
Content-Type: application/json

2168
```

Jeśli wyślesz ten element przed wygaśnięciem czasomierza, aranżacja zostanie zakończona `output` , a pole zostanie `true`ustawione na, co wskazuje na pomyślne zweryfikowanie.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 200 OK
Content-Length: 144
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":true,"createdTime":"2017-06-29T19:10:49Z","lastUpdatedTime":"2017-06-29T19:12:23Z"}
```

Jeśli wygaśniesz czasomierz lub wprowadzisz błędny kod cztery razy, możesz wykonać zapytanie o stan i zobaczyć `false` dane wyjściowe funkcji aranżacji, wskazując, że weryfikacja telefonu nie powiodła się.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Length: 145

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":false,"createdTime":"2017-06-29T19:20:49Z","lastUpdatedTime":"2017-06-29T19:22:23Z"}
```

## <a name="visual-studio-sample-code"></a>Przykładowy kod programu Visual Studio

Oto aranżacja jako pojedynczy C# plik w projekcie programu Visual Studio:

> [!NOTE]
> Musisz zainstalować `Microsoft.Azure.WebJobs.Extensions.Twilio` pakiet NuGet, aby uruchomić przykładowy kod poniżej.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs)]

## <a name="next-steps"></a>Następne kroki

Ten przykład przedstawia niektóre zaawansowane możliwości Durable Functions, w szczególności `WaitForExternalEvent` i. `CreateTimer` Dowiesz się, jak można je połączyć `Task.WaitAny` z programem w celu zaimplementowania niezawodnego limitu czasu, który jest często przydatny do współpracy z rzeczywistymi osobami. Więcej informacji na temat korzystania z Durable Functions można uzyskać, odczytując serie artykułów, które oferują szczegółowe pokrycie określonych tematów.

> [!div class="nextstepaction"]
> [Przejdź do pierwszego artykułu z serii](durable-functions-bindings.md)
