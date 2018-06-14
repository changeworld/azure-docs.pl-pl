---
title: Człowieka i limity czasu w funkcjach trwałe - Azure
description: Informacje o sposobie obsługi człowieka i limity czasu w rozszerzeniu trwałe funkcji dla usługi Azure Functions.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/19/2018
ms.author: azfuncdf
ms.openlocfilehash: 071a9ffb8305a30b0fedeaa49c4a95d91fbce6c1
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "30168405"
---
# <a name="human-interaction-in-durable-functions---phone-verification-sample"></a>Człowieka w funkcje trwałe — przykład weryfikacji telefonu

W tym przykładzie przedstawiono sposób tworzenia [trwałe funkcje](durable-functions-overview.md) aranżacji, która obejmuje człowieka. Zawsze, gdy prawdziwa osoba uczestniczy w zautomatyzowany proces, proces musi mieć możliwość wysyłania powiadomień do osoby i odbierania odpowiedzi asynchronicznie. Należy także zezwolić możliwość, że osoba jest niedostępny. (Ta ostatnia część jest gdzie limitów czasu istotne stają się).

W tym przykładzie implementuje system weryfikacji na podstawie SMS telefonu. Tego rodzaju przepływy są często używane podczas weryfikowania numer telefonu klienta lub uwierzytelnianie wieloskładnikowe (MFA). To jest przykład wydajne, ponieważ całego wdrożenia przy użyciu kilku małe funkcje. Ma magazynu danych zewnętrznych, takich jak bazy danych, jest wymagana.

## <a name="prerequisites"></a>Wymagania wstępne

* [Instalowanie funkcji trwałe](durable-functions-install.md).
* Zakończenie [Hello sekwencji](durable-functions-sequence.md) wskazówki.

## <a name="scenario-overview"></a>Omówienie scenariusza

Weryfikacja telefonu umożliwia Sprawdź, czy użytkownicy końcowi aplikacji nie są nadawcy i czy są one kto mówią, że są one. Uwierzytelnianie wieloskładnikowe jest typowe przypadek użycia ochrony przed hakerami kont użytkowników. Problem z wdrożeniem weryfikację telefoniczną jest wymaganie **stanowe interakcji** z człowieka. Użytkownik końcowy jest zwykle zapewniany kodu (na przykład numer 4-cyfrowego) i musi odpowiadać **w rozsądnym czasie**.

Zwykłe usługi Azure Functions bezstanowej (jak to wiele innych chmury punktach końcowych na innych platformach), tego rodzaju interakcje obejmują zarządzanie jawnie zewnętrznie w bazie danych lub niektóre inne trwałego magazynu stanów. Ponadto interakcja muszą być dzielone na wiele funkcji, które mogą być jednocześnie w skoordynowany sposób. Na przykład należy co najmniej jedna funkcja podejmowania decyzji na kod, gdzieś wprowadzeniem trwałych i wysłanie ich do telefonu użytkownika. Ponadto należy co najmniej jedną funkcję otrzymują odpowiedź od użytkownika i w jakiś sposób mapowania go z powrotem na oryginalny wywołanie funkcji w celu przeprowadzenia weryfikacji kodu. Limit czasu jest również istotnym elementem do zapewnienia bezpieczeństwa. To może szybko dość złożone.

Złożoność tego scenariusza jest znacznie ograniczone podczas korzystania z funkcji trwałe. Jak pokażemy w tym przykładzie funkcji programu orchestrator mogą zarządzać stanowe interakcji łatwo i bez obejmującego wszystkie magazyny danych zewnętrznych. Ponieważ funkcje programu orchestrator są *trwałe*, przepływy interactive także są wysoce niezawodne.

## <a name="configuring-twilio-integration"></a>Konfigurowanie integracji usługi Twilio

[!INCLUDE [functions-twilio-integration](../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>Funkcje

W tym artykule przedstawiono następujące funkcje w przykładowej aplikacji:

* **E4_SmsPhoneVerification**
* **E4_SendSmsChallenge**

W poniższych sekcjach opisano konfigurację i kodu, które są używane dla języka C# skryptów. Kod dla tworzenia Visual Studio jest wyświetlany na końcu tego artykułu.
 
## <a name="the-sms-verification-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>Orchestration weryfikację SMS (Visual Studio Code i Azure portal przykładowy kod) 

**E4_SmsPhoneVerification** funkcja korzysta ze standardu *function.json* dla funkcji programu orchestrator.

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/function.json)]

Oto kod, który implementuje funkcję:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/run.csx)]

Po rozpoczęciu tej funkcji orchestrator wykonuje następujące czynności:

1. Pobiera numer telefonu, który będzie *wysyłania* powiadomień programu SMS.
2. Wywołania **E4_SendSmsChallenge** do wysyłania wiadomości SMS do użytkownika i zwraca ponownie oczekiwanego wyzwanie 4-cyfrowego kodu.
3. Tworzy trwałe czasomierza tego wyzwalaczy 90 sekund od bieżącego czasu.
4. Równolegle z czasomierzem czeka na **SmsChallengeResponse** zdarzeń od użytkownika.

Użytkownik otrzymuje wiadomość SMS z 4 cyfrowy kod. Mają one 90 sekund do odesłania do wystąpienia funkcji programu orchestrator do ukończenia procesu weryfikacji tego samego 4-cyfrowy kod. Jeśli przesyłają nieprawidłowy kod otrzymują dodatkowe trzy próbuje pobrać go w prawo (w tym samym oknie 90 sekund).

> [!NOTE]
> Nie może być widocznych w pierwszej, ale ta orchestrator funkcja jest całkowicie deterministyczna. Jest to spowodowane `CurrentUtcDateTime` właściwość jest używana do obliczania czasu wygaśnięcia czasomierza, a ta właściwość zwraca tę samą wartość w każdej Odtwarzaj na tym etapie w kodzie programu orchestrator. Jest to ważne upewnić się, że takie same `winner` wyniki z każdego powtarzane wywołania `Task.WhenAny`.

> [!WARNING]
> Ważne jest, aby [anulować czasomierze przy użyciu CancellationTokenSource](durable-functions-timers.md) Jeśli nie jest już potrzebne wygaśnie, jak pokazano w przykładzie powyżej po zaakceptowaniu odpowiedź na żądanie.

## <a name="send-the-sms-message"></a>Wyślij wiadomość SMS

**E4_SendSmsChallenge** funkcja używa powiązania usługi Twilio do wysyłania wiadomości SMS z 4-cyfrowego kodu użytkownika końcowego. *Function.json* jest zdefiniowane w następujący sposób:

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/function.json)]

A Oto kod, który generuje kod testu 4-cyfrowego i wysyła wiadomość SMS:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/run.csx)]

To **E4_SendSmsChallenge** funkcja pobiera wywołana tylko raz, nawet jeśli wystąpiła awaria procesu lub pobiera odtwarzany. Jest to dobry, ponieważ nie chcesz, aby użytkownik końcowy pobierania wielu wiadomości SMS. `challengeCode` Zwrócić wartość automatycznie jest trwały, więc funkcja orchestrator zawsze wie, co to jest prawidłowy kod.

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Używanie funkcji wyzwalanej przez HTTP wchodzących w skład próby, możesz uruchomić orchestration wysyłania następujące żądania HTTP POST:

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

Funkcja orchestrator odbiera podany numer i natychmiast wysyła on wiadomość SMS z kodem weryfikacyjnym 4-cyfrowego losowo generowany &mdash; na przykład *2168*. Funkcja czeka 90 sekund na odpowiedź.

Odpowiedzi z kodem, można użyć `RaiseEventAsync` innej funkcji, lub wywołaj **sendEventUrl** elementu webhook HTTP POST, do którego odwołuje się 202 odpowiedzi powyżej, zastępując `{eventName}` o nazwie zdarzenia, `SmsChallengeResponse`:

```
POST http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/SmsChallengeResponse?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Length: 4
Content-Type: application/json

2168
```

W przypadku wysłania to przed wygaśnięciem, zakończeniu orchestration i `output` pole jest ustawione na `true`, wskazujący pomyślne weryfikacji.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```
```
HTTP/1.1 200 OK
Content-Length: 144
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":true,"createdTime":"2017-06-29T19:10:49Z","lastUpdatedTime":"2017-06-29T19:12:23Z"}
```

Jeśli umożliwisz czasomierza wygaśnie lub jeśli wprowadzisz nieprawidłowy kod cztery razy, zapytań dotyczących stanu i zobacz `false` funkcji aranżacji output, wskazujący, że weryfikacja telefonu nie powiodło się.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Length: 145

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":false,"createdTime":"2017-06-29T19:20:49Z","lastUpdatedTime":"2017-06-29T19:22:23Z"}
```

## <a name="visual-studio-sample-code"></a>Visual Studio przykładowy kod

Oto aranżacji jako pojedynczy plik C# w projektu programu Visual Studio:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs)]

## <a name="next-steps"></a>Kolejne kroki

W tym przykładzie wykazała niektóre z zaawansowanych możliwości funkcji trwałe, szczególnie `WaitForExternalEvent` i `CreateTimer`. W tym samouczku jak te można łączyć z `Task.WaitAny` wdrożenia systemu niezawodnej limitu czasu, co przydaje się często do interakcji z rzeczywistą osób. Możesz można dowiedzieć się więcej o sposobie używania funkcji trwałe odczytując szereg artykuły, które zawierają szczegółowe informacje dotyczące określonych tematów.

> [!div class="nextstepaction"]
> [Przejdź do pierwszego artykułu serii](durable-functions-bindings.md)
