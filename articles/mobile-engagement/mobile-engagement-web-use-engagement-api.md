---
title: Interfejsy API zestawu SDK sieci Web usługi Azure Mobile Engagement | Dokumentacja firmy Microsoft
description: Najnowsze aktualizacje i procedury dotyczące zestawu SDK sieci Web dla usługi Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 8a87d5ac-d8b7-4a0d-bdee-414dbcc561b2
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 06/07/2016
ms.author: piyushjo
ms.openlocfilehash: 6d2ae75b384b60d0383c1682a00a4fc0d19d0f43
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="use-the-azure-mobile-engagement-api-in-a-web-application"></a>Użyj interfejsu API Azure Mobile Engagement w aplikacji sieci web
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

Ten dokument jest dodaną do dokumentu, który informuje o sposobie do [zintegrowana usługa Mobile Engagement w aplikacji sieci web](mobile-engagement-web-integrate-engagement.md). Zapewnia on szczegółowe informacje o raport statystyk aplikacji przy użyciu interfejsu API Azure Mobile Engagement.

Interfejs API Mobile Engagement jest zapewniana przez `engagement.agent` obiektu. Domyślny zestaw SDK usługi Azure Mobile Engagement Web alias jest `engagement`. Można zmienić ten alias z konfiguracji zestawu SDK.

## <a name="mobile-engagement-concepts"></a>Pojęcia dotyczące usługi Mobile Engagement
Następujące części uściślić wspólnej [pojęcia dotyczące usługi Mobile Engagement](mobile-engagement-concepts.md) platformy sieci web.

### <a name="session-and-activity"></a>`Session` i `Activity`
Jeśli użytkownik pozostaje bezczynności więcej niż kilka sekund pomiędzy dwoma działaniami, sekwencję działań użytkownika jest podzielony na dwie różne sesje. Te kilka sekund, są nazywane limit czasu sesji.

Jeśli aplikacja sieci web nie deklaruje zakończenia działań użytkownika samodzielnie (przez wywołanie metody `engagement.agent.endActivity` funkcji), serwer usługi Mobile Engagement automatycznie wygaśnięcia sesji użytkownika w ciągu trzech minut po zamknięciu strony aplikacji. Jest to limit czasu sesji serwera.

### `Crash`
Domyślnie nie są tworzone automatycznych raporty dotyczące nieprzechwyconych wyjątków JavaScript. Jednak możesz zgłosić awarię ręcznie przy użyciu `sendCrash` funkcji (zobacz sekcję dotyczącą raportowania awarii).

## <a name="reporting-activities"></a>Działania raportowania
Raporty dotyczące działań użytkownika zawiera gdy użytkownik uruchamia nowe działanie, a bieżące działanie kończy się użytkownika.

### <a name="user-starts-a-new-activity"></a>Użytkownik uruchamia nowe działanie
    engagement.agent.startActivity("MyUserActivity");

Należy wywołać `startActivity()` zmiany każdego działania użytkownika. W pierwszym wywołaniu tej funkcji uruchamia nową sesję użytkownika.

### <a name="user-ends-the-current-activity"></a>Użytkownik kończy się bieżące działanie
    engagement.agent.endActivity();

Należy wywołać `endActivity()` co najmniej raz, gdy użytkownik zakończy swoje ostatnie działanie. Zestaw SDK usługi Mobile Engagement w sieci Web to informuje, czy użytkownik jest obecnie w stanie bezczynności i że sesji użytkownika musi zostać zamknięty, po upływie limitu czasu sesji. Jeśli należy wywołać `startActivity()` przed upłynięciem limitu czasu sesji, po prostu wznowić sesji.

Ponieważ nie ma niezawodne składania po zamknięciu okna navigator, często jest trudne lub niemożliwe do wychwytywania zakończenia działań użytkownika w środowisku sieci web. Dlatego serwer usługi Mobile Engagement automatycznie wygaśnięcia sesji użytkownika w ciągu trzech minut po zamknięciu strony aplikacji.

## <a name="reporting-events"></a>Zdarzenia raportowania
Raporty dotyczące zdarzeń obejmuje zdarzenia sesji i autonomicznych.

### <a name="session-events"></a>Zdarzenia sesji
Zdarzenia sesji są zwykle używane do zgłaszania akcji wykonywanych przez użytkownika podczas sesji użytkownika.

**Przykład bez dodatkowe dane:**

    loginButton.onclick = function() {
      engagement.agent.sendSessionEvent('login');
      // [...]
    }

**Przykład: dodatkowe dane:**

    loginButton.onclick = function() {
      engagement.agent.sendSessionEvent('login', {user: 'alice'});
      // [...]
    }

### <a name="standalone-events"></a>Autonomiczny zdarzenia
W przeciwieństwie do sesji zdarzeń mogą występować zdarzenia autonomiczny poza kontekstem sesji.

Do tego użyć ``engagement.agent.sendEvent`` zamiast ``engagement.agent.sendSessionEvent``.

## <a name="reporting-errors"></a>Raportowanie błędów
Raportowanie błędów obejmuje błędy sesji i błędy autonomicznych.

### <a name="session-errors"></a>Błędy sesji
Błędy sesji zwykle są używane do zgłaszania błędów, które mają wpływ na użytkownika podczas sesji użytkownika.

**Przykład bez dodatkowe dane:**

    var validateForm = function() {
      // [...]
      if (password.length < 6) {
        engagement.agent.sendSessionError('password_too_short');
      }
      // [...]
    }

**Przykład: dodatkowe dane:**

    var validateForm = function() {
      // [...]
      if (password.length < 6) {
        engagement.agent.sendSessionError('password_too_short', {length: 4});
      }
      // [...]
    }

### <a name="standalone-errors"></a>Błędy autonomiczny
W przeciwieństwie do błędów sesji mogą wystąpić błędy autonomiczny poza kontekstem sesji.

Do tego użyć `engagement.agent.sendError` zamiast `engagement.agent.sendSessionError`.

## <a name="reporting-jobs"></a>Zadania raportowania
Raporty dotyczące obejmuje zadania raportowania błędów i zdarzeń występujących podczas wykonywania zadania i raportowania awarii (Crash).

**Przykład:**

Jeśli chcesz monitorować żądaniem AJAX użyje następujących czynności:

    // [...]
    xhr.onreadystatechange = function() {
      if (xhr.readyState == 4) {
      // [...]
        engagement.agent.endJob('publish');
      }
    }
    engagement.agent.startJob('publish');
    xhr.send();
    // [...]

### <a name="reporting-errors-during-a-job"></a>Raportowanie błędów podczas wykonywania zadania
Błędy może być powiązane z uruchomionym zadaniem, a nie do bieżącej sesji użytkownika.

**Przykład:**

Jeśli chcesz zgłosić błąd, jeśli żądanie AJAX nie powiedzie się:

    // [...]
    xhr.onreadystatechange = function() {
      if (xhr.readyState == 4) {
        // [...]
        if (xhr.status == 0 || xhr.status >= 400) {
          engagement.agent.sendJobError('publish_xhr', 'publish', {status: xhr.status, statusText: xhr.statusText});
        }
        engagement.agent.endJob('publish');
      }
    }
    engagement.agent.startJob('publish');
    xhr.send();
    // [...]

### <a name="reporting-events-during-a-job"></a>Zdarzenia raportowania podczas wykonywania zadania
Zdarzenia może być związany z uruchomionym zadaniem, a nie z bieżącą sesją użytkownika Podziękowania `engagement.agent.sendJobEvent` funkcji.

Ta funkcja działa dokładnie tak samo, jak `engagement.agent.sendJobError`.

### <a name="reporting-crashes"></a>Raportowanie awarii (Crash)
Użyj `sendCrash` ręcznie ulega awarii funkcji do raportu.

`crashid` Argument jest ciąg identyfikujący typ awarii.
`crash` Argument jest zazwyczaj ślad stosu awarii (Crash) jako ciąg.

    engagement.agent.sendCrash(crashid, crash);

## <a name="extra-parameters"></a>Dodatkowe parametry
Dowolne dane można dołączyć do zdarzenia, błąd, działania lub zadania.

Dane mogą być dowolnego obiektu JSON (ale nie tablicy lub typu pierwotnego).

**Przykład:**

    var extras = {"video_id": 123, "ref_click": "http://foobar.com/blog"};
    engagement.agent.sendEvent("video_clicked", extras);

### <a name="limits"></a>Limity
Ograniczenia, które dotyczą dodatkowe parametry są w zakresie wyrażeń regularnych do kluczy, typy wartości i rozmiar.

#### <a name="keys"></a>Klucze
Każdy klucz w obiekcie musi odpowiadać następującym wyrażeniem regularnym:

    ^[a-zA-Z][a-zA-Z_0-9]*

Oznacza to, że klucze musi rozpoczynać się od co najmniej jedną literą, po której następują litery, cyfry i znaki podkreślenia (\_).

#### <a name="values"></a>Wartości
Wartości są ograniczone do ciągu, liczbę i typy Boolean.

#### <a name="size"></a>Rozmiar
Dodatki mogą zawierać maksymalnie 1024 znaków na wywołanie (po zestaw SDK usługi Mobile Engagement Web kodowane w formacie JSON).

## <a name="reporting-application-information"></a>Raportowanie informacji o aplikacji
Możesz ręcznie zgłosić śledzenia informacji (lub inne informacje specyficzne dla aplikacji) przy użyciu `sendAppInfo()` funkcji.

Należy pamiętać, że te informacje mogą być wysyłane przyrostowo. Tylko najnowszą wartość dla określonego klucza zostaną zachowane dla określonego urządzenia.

Podobnie jak dodatkowe zdarzenia służy dowolny obiekt JSON abstrakcyjnej informacje o aplikacji. Należy pamiętać, że tablice lub obiekty podrzędne są traktowane jako płaska ciągów (za pomocą serializacji JSON).

**Przykład:**

Oto przykładowy kod do wysyłania płci użytkownika oraz data urodzenia:

    var appInfos = {"birthdate":"1983-12-07","gender":"female"};
    engagement.agent.sendAppInfo(appInfos);

### <a name="limits"></a>Limity
Ograniczenia, które są stosowane do informacji o aplikacji są w obszarach wyrażeń regularnych kluczy i rozmiaru.

#### <a name="keys"></a>Klucze
Każdy klucz w obiekcie musi odpowiadać następującym wyrażeniem regularnym:

    ^[a-zA-Z][a-zA-Z_0-9]*

Oznacza to, że klucze musi rozpoczynać się od co najmniej jedną literą, po której następują litery, cyfry i znaki podkreślenia (\_).

#### <a name="size"></a>Rozmiar
Informacje o aplikacji jest ograniczony do 1024 znaków na wywołanie (po zestaw SDK usługi Mobile Engagement Web kodowane w formacie JSON).

W powyższym przykładzie JSON na serwer wysyłane jest 44 znaków:

    {"birthdate":"1983-12-07","gender":"female"}
