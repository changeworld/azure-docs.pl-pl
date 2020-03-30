---
title: Interakcja międzyludyjka i limity czasu w funkcjach trwałych — Azure
description: Dowiedz się, jak obsługiwać interakcja międzyludyjkami z człowiekiem i limity czasu w rozszerzeniu Funkcje trwałe dla usługi Azure Functions.
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 4e0f71369bc02fdce5625d9c74e1d52264ed86be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335759"
---
# <a name="human-interaction-in-durable-functions---phone-verification-sample"></a>Interakcja międzyludzkie w funkcji trwałych - próbka weryfikacji telefonu

W tym przykładzie pokazano, jak utworzyć aranżacji [funkcji trwałych,](durable-functions-overview.md) która obejmuje interakcji z człowiekiem. Za każdym razem, gdy prawdziwa osoba jest zaangażowana w zautomatyzowany proces, proces musi być w stanie wysyłać powiadomienia do danej osoby i otrzymywać odpowiedzi asynchronicznie. Musi również umożliwiać możliwość, że dana osoba jest niedostępna. (Ta ostatnia część to miejsce, w którym limity czasu stają się ważne).

W tym przykładzie zaimplementowano system weryfikacji telefonicznej oparty na programie SMS. Te typy przepływów są często używane podczas weryfikacji numeru telefonu klienta lub uwierzytelniania wieloskładnikowego (MFA). Jest to potężny przykład, ponieważ cała implementacja odbywa się przy użyciu kilku małych funkcji. Nie jest wymagany magazyn danych zewnętrznych, takich jak baza danych.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Omówienie scenariusza

Weryfikacja telefoniczna służy do sprawdzenia, czy użytkownicy końcowi aplikacji nie są spamerami i że są tym, kim mówią, że są. Uwierzytelnianie wieloskładnikowe jest częstym przypadkiem użycia do ochrony kont użytkowników przed hakerami. Wyzwaniem związanym z wdrożeniem własnej weryfikacji telefonicznej jest to, że wymaga ona **stanowej interakcji** z człowiekiem. Użytkownik końcowy otrzymuje zazwyczaj kod (na przykład 4-cyfrowy numer) i musi odpowiedzieć **w rozsądnym czasie.**

Zwykłe funkcje platformy Azure są bezstanowe (podobnie jak wiele innych punktów końcowych chmury na innych platformach), więc te typy interakcji obejmują jawnie zarządzanie stanem zewnętrznie w bazie danych lub innym magazynie trwałym. Ponadto interakcja musi być podzielona na wiele funkcji, które mogą być koordynowane razem. Na przykład potrzebujesz co najmniej jednej funkcji do podejmowania decyzji o kodzie, utrwalania go gdzieś i wysyłania go na telefon użytkownika. Ponadto należy co najmniej jedną inną funkcję, aby otrzymać odpowiedź od użytkownika i jakoś mapować go z powrotem do wywołania oryginalnej funkcji w celu wykonania sprawdzania poprawności kodu. Limit czasu jest również ważnym aspektem, aby zapewnić bezpieczeństwo. To może stać się dość skomplikowane szybko.

Złożoność tego scenariusza jest znacznie zmniejszona, gdy używasz funkcji trwałych. Jak widać w tym przykładzie, funkcja koordynatora można zarządzać interakcji stanowej łatwo i bez angażowania żadnych magazynów danych zewnętrznych. Ponieważ funkcje aranżatory są *trwałe,* te przepływy interaktywne są również wysoce niezawodne.

## <a name="configuring-twilio-integration"></a>Konfigurowanie integracji usługi Twilio

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>Funkcje

W tym artykule przedstawiono następujące funkcje w przykładowej aplikacji:

* `E4_SmsPhoneVerification`: [Funkcja koordynatora,](durable-functions-bindings.md#orchestration-trigger) która wykonuje proces weryfikacji telefonicznej, w tym zarządzanie limitami czasu i ponownych prób.
* `E4_SendSmsChallenge`: [Funkcja działania,](durable-functions-bindings.md#activity-trigger) która wysyła kod za pośrednictwem wiadomości tekstowej.

### <a name="e4_smsphoneverification-orchestrator-function"></a>E4_SmsPhoneVerification funkcja orkiestratora

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs?range=17-70)]

> [!NOTE]
> Na początku może to nie być oczywiste, ale ta funkcja koordynatora jest całkowicie deterministyczna. Jest deterministyczny, `CurrentUtcDateTime` ponieważ właściwość jest używana do obliczania czasu wygaśnięcia czasomierza i zwraca tę samą wartość na każdej powtórce w tym momencie w kodzie koordynatora. To zachowanie jest ważne, `winner` aby upewnić się, że takie same wyniki z każdego powtarzającego się wywołania . `Task.WhenAny`

# <a name="javascript"></a>[Javascript](#tab/javascript)

Funkcja **E4_SmsPhoneVerification** używa standardowego *function.json* dla funkcji orkiestratora.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/function.json)]

Oto kod, który implementuje funkcję:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/index.js)]

> [!NOTE]
> Na początku może to nie być oczywiste, ale ta funkcja koordynatora jest całkowicie deterministyczna. Jest deterministyczny, `currentUtcDateTime` ponieważ właściwość jest używana do obliczania czasu wygaśnięcia czasomierza i zwraca tę samą wartość na każdej powtórce w tym momencie w kodzie koordynatora. To zachowanie jest ważne, `winner` aby upewnić się, że takie same wyniki z każdego powtarzającego się wywołania . `context.df.Task.any`

---

Po uruchomieniu ta funkcja koordynatora wykonuje następujące czynności:

1. Pobiera numer telefonu, na który *wyśle* powiadomienie SMS.
2. Wywołuje **E4_SendSmsChallenge,** aby wysłać wiadomość SMS do użytkownika i zwraca oczekiwany 4-cyfrowy kod wyzwania.
3. Tworzy trwały czasomierz, który wyzwala 90 sekund od bieżącego czasu.
4. Równolegle z timerem, czeka na **SmsChallengeResponse** zdarzenia od użytkownika.

Użytkownik otrzymuje wiadomość SMS z czterocyfrowym kodem. Mają 90 sekund, aby wysłać ten sam czterocyfrowy kod z powrotem do wystąpienia funkcji koordynatora, aby zakończyć proces weryfikacji. Jeśli przesyłają niewłaściwy kod, otrzymają dodatkowe trzy próby, aby to dobrze (w tym samym 90-sekundowym oknie).

> [!WARNING]
> Ważne jest, aby [anulować czasomierze,](durable-functions-timers.md) jeśli nie są już potrzebne do ich wygaśnięcia, jak w powyższym przykładzie, gdy odpowiedź na wyzwanie zostanie zaakceptowana.

## <a name="e4_sendsmschallenge-activity-function"></a>Funkcja aktywności E4_SendSmsChallenge

Funkcja **E4_SendSmsChallenge** używa powiązania usługi Twilio do wysyłania wiadomości SMS z czterocyfrowym kodem do użytkownika końcowego.

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs?range=72-89)]

> [!NOTE]
> Należy zainstalować pakiet `Microsoft.Azure.WebJobs.Extensions.Twilio` Nuget, aby uruchomić przykładowy kod.

# <a name="javascript"></a>[Javascript](#tab/javascript)

*Function.json* jest zdefiniowany w następujący sposób:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/function.json)]

A oto kod, który generuje czterocyfrowy kod wyzwania i wysyła wiadomość SMS:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/index.js)]

---

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Korzystając z funkcji wyzwalanych przez protokół HTTP zawartych w przykładzie, można rozpocząć aranżację, wysyłając następujące żądanie HTTP POST:

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
Location: http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"id":"741c65651d4c40cea29acdd5bb47baf1","statusQueryGetUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","sendEventPostUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","terminatePostUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}"}
```

Funkcja koordynatora odbiera podany numer telefonu i natychmiast wysyła do niego wiadomość SMS &mdash; z losowo wygenerowanym 4-cyfrowym kodem weryfikacyjnym, na przykład *2168*. Następnie funkcja czeka 90 sekund na odpowiedź.

Aby odpowiedzieć za pomocą kodu, można użyć `{eventName}` `SmsChallengeResponse` [ `RaiseEventAsync` (.NET) lub `raiseEvent` (JavaScript)](durable-functions-instance-management.md) wewnątrz innej funkcji lub wywołać **sendEventUrl** HTTP POST webhook odwołuje się w 202 odpowiedzi powyżej, zastępując nazwą zdarzenia, :

```
POST http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/SmsChallengeResponse?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Length: 4
Content-Type: application/json

2168
```

Jeśli wyślesz to przed wygaśnięciem czasomierza, `output` aranżacja `true`zostanie zakończona, a pole zostanie ustawione na , wskazując pomyślną weryfikację.

```
GET http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 200 OK
Content-Length: 144
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":true,"createdTime":"2017-06-29T19:10:49Z","lastUpdatedTime":"2017-06-29T19:12:23Z"}
```

Jeśli czasomierz wygaśnie lub jeśli wprowadzisz niewłaściwy kod cztery razy, `false` możesz zapytać o stan i wyświetlić dane wyjściowe funkcji aranżacji, wskazując, że weryfikacja telefoniczna nie powiodła się.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Length: 145

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":false,"createdTime":"2017-06-29T19:20:49Z","lastUpdatedTime":"2017-06-29T19:22:23Z"}
```

## <a name="next-steps"></a>Następne kroki

W tym przykładzie zademonstrowano niektóre zaawansowane funkcje `WaitForExternalEvent` `CreateTimer` trwałe, w szczególności i interfejsy API. Widzieliście, jak można je `Task.WaitAny` połączyć w celu wdrożenia niezawodnego systemu limitu czasu, który jest często przydatny do interakcji z prawdziwymi ludźmi. Możesz dowiedzieć się więcej o tym, jak korzystać z funkcji trwałych, czytając serię artykułów, które oferują szczegółowe pokrycie określonych tematów.

> [!div class="nextstepaction"]
> [Przejdź do pierwszego artykułu z serii](durable-functions-bindings.md)
