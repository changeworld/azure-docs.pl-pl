---
title: Interakcja z użytkownikami i limity czasu w funkcje trwałe - Azure
description: Dowiedz się, jak obsługiwać z reakcji człowieka i przekroczeń limitu czasu w rozszerzenia funkcji trwałych dla usługi Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: cf43e29e967ee6f920eb38feb9c73d70f9621ea4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62123318"
---
# <a name="human-interaction-in-durable-functions---phone-verification-sample"></a>Interakcja z użytkownikami w funkcje trwałe — przykład weryfikacji telefonu

W tym przykładzie przedstawiono sposób tworzenia [funkcje trwałe](durable-functions-overview.md) aranżacji, która obejmuje z reakcji człowieka. Zawsze wtedy, gdy tworzy prawdziwa osoba zaangażowanych w zautomatyzowany proces, proces musi można wysyłać powiadomienia do osoby i otrzymywać odpowiedzi asynchronicznie. Należy także zezwolić możliwość, że osoba jest niedostępny. (Ta ostatnia część to, gdzie nabierają znaczenia przekroczeń limitu czasu).

W tym przykładzie implementuje system weryfikacji telefonu na podstawie programu SMS. Te typy przepływów są często używane podczas weryfikowania numeru telefonu klienta lub uwierzytelnianie wieloskładnikowe (MFA). To jest przykład zaawansowane, ponieważ cała implementacja jest wykonywane przy użyciu kilka małych funkcji. Nie magazynu danych zewnętrznych, takich jak bazy danych, jest wymagana.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Omówienie scenariusza

Weryfikacja telefonu służy do Sprawdź, czy użytkownicy końcowi aplikacji nie są spamerów i że są one Załóżmy, że są one. Usługa uwierzytelnianie wieloskładnikowe jest typowy przypadek użycia na potrzeby ochrony kont użytkowników przed hakerami. Wyzwanie z wdrożeniem weryfikację telefoniczną jest, że wymaga **stanowych interakcji** z człowiekiem. Użytkownik końcowy jest zwykle zapewniany kodu (na przykład 4-cyfrowy numer) i musi odpowiadać **w rozsądnym czasie**.

Zwykłe usługi Azure Functions są bezstanowe (tak jak wiele innych punkty końcowe w chmurze na innych platformach), więc tego rodzaju interakcje obejmują zarządzanie jawnie zewnętrznie w bazie danych lub niektórych innych trwały Magazyn stanów. Ponadto interakcja muszą być dzielone na kilka funkcji, które mogą być koordynowane ze sobą. Na przykład potrzebujesz co najmniej jedna funkcja ustawiania kodu, utrwalanie je gdzieś i wysłanie go na numer telefonu użytkownika. Ponadto należy co najmniej jedną funkcję otrzymują odpowiedź od użytkownika i w jakiś sposób mapowania go z powrotem na oryginalny wywołanie funkcji w celu przeprowadzenia weryfikacji kodu. Przekroczono limit czasu jest także ważny aspekt w celu zapewnienia bezpieczeństwa. To szybko dość złożony.

Podczas korzystania z funkcji trwałych znacząco zmniejsza złożoność tego scenariusza. Jak będzie widać, w tym przykładzie, funkcja orkiestratora można zarządzać stanowych interakcji łatwo i bez angażowania wszystkie magazyny danych zewnętrznych. Ponieważ funkcje programu orchestrator są *trwałe*, przepływy te interaktywne są również o wysokiej niezawodności.

## <a name="configuring-twilio-integration"></a>Konfigurowanie integracji usługi Twilio

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>Funkcje

W tym artykule przedstawiono następujące funkcje w przykładowej aplikacji:

* **E4_SmsPhoneVerification**
* **E4_SendSmsChallenge**

W poniższych sekcjach opisano konfigurację i kod, które są używane dla skryptów języka C# i JavaScript. Kod dla rozwoju programu Visual Studio jest wyświetlany na końcu tego artykułu.

## <a name="the-sms-verification-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>Organizowanie weryfikację SMS (kod przykładowy portal programu Visual Studio Code i platformy Azure)

**E4_SmsPhoneVerification** funkcja używa standardowych *function.json* dla funkcji programu orchestrator.

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/function.json)]

Poniżej przedstawiono kod, który implementuje funkcję:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (działa tylko 2.x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/index.js)]

Po rozpoczęciu tej funkcji program orchestrator zapewnia następujące funkcje:

1. Pobiera numer telefonu, do którego zostanie *wysyłania* powiadomień programu SMS.
2. Wywołania **E4_SendSmsChallenge** do wysyłania wiadomości SMS do użytkownika i zwraca z powrotem kod oczekiwanego testu 4-cyfrowego.
3. Tworzy trwałe czasomierza tego wyzwalaczy 90 sekund od bieżącego czasu.
4. Równolegle z czasomierz czeka **SmsChallengeResponse** zdarzeń od użytkownika.

Użytkownik otrzymuje wiadomość SMS z 4 cyfrowego kodu. Mają one 90 sekund do odesłania tego samego 4-cyfrowy kod do wystąpienia funkcji programu orchestrator do ukończenia procesu weryfikacji. Jeśli przesyłania nieprawidłowy kod, otrzymują dodatkowe trzy próbuje pobrać go bezpośrednio (w tym samym oknie 90-sekundowe).

> [!NOTE]
> Nie może być widocznych w pierwszym, ale ten program orchestrator jest całkowicie deterministyczna, funkcja. Jest to spowodowane `CurrentUtcDateTime` (.NET) i `currentUtcDateTime` właściwości (JavaScript) są używane do obliczania czasu wygaśnięcia czasomierza, a te właściwości zwracają taką samą wartość w każdym powtarzania na tym etapie w kodzie programu orchestrator. Ważne jest, aby upewnić się, że takie same `winner` powstały na skutek każde wywołanie powtarzanych `Task.WhenAny` (.NET) lub `context.df.Task.any` (JavaScript).

> [!WARNING]
> Ważne jest, aby [anulować czasomierzy](durable-functions-timers.md) Jeśli nie potrzebujesz już ich wygaśnie, tak jak w przykładzie powyżej po zaakceptowaniu odpowiedź na żądanie.

## <a name="send-the-sms-message"></a>Wyślij wiadomość SMS

**E4_SendSmsChallenge** funkcji wysyła wiadomość SMS z 4-cyfrowym kodem użytkownika końcowego przy użyciu powiązania usługi Twilio. *Function.json* jest zdefiniowana w następujący sposób:

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/function.json)]

A Oto kod, który generuje kod testu 4-cyfrowego i wysyła wiadomość SMS:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (działa tylko 2.x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/index.js)]

To **E4_SendSmsChallenge** funkcja pobiera wywołana tylko raz, nawet, jeśli proces ulegnie awarii lub pobiera odtwarzany. Może to być dobry, ponieważ nie chcesz, aby użytkownik końcowy wprowadzenie wiele wiadomości SMS. `challengeCode` Zwracają wartość automatycznie jest trwały, więc funkcja orkiestratora zawsze wie, co to jest prawidłowy kod.

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Korzystając z funkcji wyzwalanej przez HTTP, zawarte w przykładzie, można uruchomić aranżacji, wysyłając następujące żądanie HTTP POST:

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

Funkcja orkiestratora odbiera podany numer i natychmiast wysyła wiadomość SMS z losowo generowany 4-cyfrowy kod weryfikacyjny &mdash; na przykład *2168*. Funkcja następnie czeka 90 sekund na odpowiedź.

Aby odpowiedzieć kodem, możesz użyć [ `RaiseEventAsync` (.NET) lub `raiseEvent` (JavaScript)](durable-functions-instance-management.md) innej funkcji lub wywołaj **sendEventUrl** elementu webhook POST protokołu HTTP, do którego odwołuje się powyżej odpowiedzi o kodzie 202 , zastępując `{eventName}` o nazwie wydarzenia `SmsChallengeResponse`:

```
POST http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/SmsChallengeResponse?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Length: 4
Content-Type: application/json

2168
```

Jeśli wyślesz to przed wygaśnięciem czasomierza, kończy aranżacji i `output` pole jest ustawione na `true`, wskazujący pomyślne weryfikacji.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 200 OK
Content-Length: 144
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":true,"createdTime":"2017-06-29T19:10:49Z","lastUpdatedTime":"2017-06-29T19:12:23Z"}
```

Jeśli umożliwisz czasomierza wygaśnie lub jeśli wprowadzisz nieprawidłowy kod cztery razy, zapytanie dotyczące stanu i zobacz `false` funkcja organizowania danych wyjściowych, wskazujący, że weryfikacja telefonu nie powiodło się.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Length: 145

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":false,"createdTime":"2017-06-29T19:20:49Z","lastUpdatedTime":"2017-06-29T19:22:23Z"}
```

## <a name="visual-studio-sample-code"></a>Kod przykładowy w usłudze Visual Studio

Oto aranżacji jako pojedynczy plik języka C# w projekcie programu Visual Studio:

> [!NOTE]
> Musisz zainstalować `Microsoft.Azure.WebJobs.Extensions.Twilio` pakiet Nuget do uruchamiania kodu przykładu poniżej.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs)]

## <a name="next-steps"></a>Kolejne kroki

W tym przykładzie wykazała niektórych zaawansowanych możliwości funkcje trwałe, szczególnie `WaitForExternalEvent` i `CreateTimer`. Pokazaliśmy już, jak te można łączyć z `Task.WaitAny` wdrożenia systemu niezawodne limitu czasu, który jest często przydatne w przypadku interakcji z prawdziwi ludzie. Użytkownik może dowiedzieć się więcej o do użycia funkcje trwałe, zapoznając się seria artykułów, które oferują szczegółowe informacje dotyczące określonych tematów.

> [!div class="nextstepaction"]
> [Przejdź do pierwszego artykułu z tej serii](durable-functions-bindings.md)
