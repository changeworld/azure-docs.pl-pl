---
title: Interfejs API REST zarządzania sesjami
description: W tym artykule opisano sposób zarządzania sesjami
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 46560f067e020236031487677ad4f48a9560d4e1
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681248"
---
# <a name="use-the-session-management-rest-api"></a>Korzystanie z interfejsu API REST zarządzania sesjami

Aby korzystać z funkcji zdalnego renderowania platformy Azure, należy utworzyć *sesję*. Każda sesja odpowiada maszynie wirtualnej (VM) przydzielanej na platformie Azure i oczekiwaniu na połączenie się urządzenia klienckiego. Gdy urządzenie łączy się, maszyna wirtualna renderuje żądane dane i służy wynik jako strumień wideo. Podczas tworzenia sesji, wybrać rodzaj serwera, który ma być uruchomiony na, który określa ceny. Gdy sesja nie jest już potrzebna, należy ją zatrzymać. Jeśli nie zostanie zatrzymana ręcznie, zostanie automatycznie zamknięty po upływie *czasu dzierżawy* sesji.

Udostępniamy skrypt programu PowerShell w [repozytorium próbek ARR](https://github.com/Azure/azure-remote-rendering) w *folderze Skrypty* o nazwie *RenderingSession.ps1*, który demonstruje korzystanie z naszej usługi. Skrypt i jego konfiguracja są opisane w tym miejscu: [Przykładowe skrypty programu PowerShell](../samples/powershell-example-scripts.md)

> [!TIP]
> Polecenia programu PowerShell wymienione na tej stronie mają się wzajemnie uzupełniać. Jeśli wszystkie skrypty są uruchamiane w kolejności w tym samym wierszu polecenia programu PowerShell, będą one opierać się na siebie nawzajem.

## <a name="regions"></a>Regiony

Zobacz [listę dostępnych regionów](../reference/regions.md) dla podstawowych adresów URL, do które mają być wysyłane żądania.

Dla przykładowych skryptów poniżej wybraliśmy region *westus2*.

### <a name="example-script-choose-an-endpoint"></a>Przykładowy skrypt: wybieranie punktu końcowego

```PowerShell
$endPoint = "https://remoterendering.westus2.mixedreality.azure.com"
```

## <a name="accounts"></a>Konta

Jeśli nie masz konta zdalnego renderowania, [utwórz je](create-an-account.md). Każdy zasób jest identyfikowany przez *identyfikator konta*, który jest używany w interfejsach API sesji.

### <a name="example-script-set-accountid-and-accountkey"></a>Przykładowy skrypt: Ustaw identyfikator konta i klucz konta

```PowerShell
$accountId = "********-****-****-****-************"
$accountKey = "*******************************************="
```

## <a name="common-request-headers"></a>Typowe nagłówki żądań

* Nagłówek *Autoryzacja* musi mieć`Bearer TOKEN`wartość "`TOKEN`", gdzie " jest tokenem uwierzytelniania [zwróconym przez usługę bezpiecznego tokenu.](tokens.md)

### <a name="example-script-request-a-token"></a>Przykładowy skrypt: żądanie tokenu

```PowerShell
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -ContentType "application/json" -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content
$token = $response.AccessToken;
```

## <a name="common-response-headers"></a>Typowe nagłówki odpowiedzi

* Nagłówek *MS-CV* może służyć przez zespół produktu do śledzenia wywołania w usłudze.

## <a name="create-a-session"></a>Tworzenie sesji

To polecenie tworzy sesję. Zwraca identyfikator nowej sesji. Identyfikator sesji jest potrzebny dla wszystkich innych poleceń.

| Identyfikator URI | Metoda |
|-----------|:-----------|
| /v1/accounts/accountId /sessions/create*accountId* | POST |

**Treść żądania:**

* maxLeaseTime (timespan): wartość limitu czasu, gdy maszyna wirtualna zostanie automatycznie wycofana
* modele (tablica): adresy URL kontenerów zasobów do wstępnego ładowania
* rozmiar (ciąg znaków): rozmiar maszyny Wirtualnej (**"standard"** lub **"premium").** Zobacz określone [ograniczenia rozmiaru maszyny Wirtualnej](../reference/limits.md#overall-number-of-polygons).

**Odpowiedzi:**

| Kod stanu | Ładowność JSON | Komentarze |
|-----------|:-----------|:-----------|
| 202 | - sessionId: GUID | Powodzenie |

### <a name="example-script-create-a-session"></a>Przykładowy skrypt: tworzenie sesji

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/create" -Method Post -ContentType "application/json" -Body "{ 'maxLeaseTime': '4:0:0', 'models': [], 'size': 'standard' }" -Headers @{ Authorization = "Bearer $token" }
```

Przykładowe dane wyjściowe:

```PowerShell
StatusCode        : 202
StatusDescription : Accepted
Content           : {"sessionId":"d31bddca-dab7-498e-9bc9-7594bc12862f"}
RawContent        : HTTP/1.1 202 Accepted
                    MS-CV: 5EqPJ1VdTUakDJZc6/ZhTg.0
                    Content-Length: 52
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 09 May 2019 16:17:50 GMT
                    Location: accounts/11111111-1111-1111-11...
Forms             : {}
Headers           : {[MS-CV, 5EqPJ1VdTUakDJZc6/ZhTg.0], [Content-Length, 52], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 09 May 2019 16:17:50 GMT]...}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 52
```

### <a name="example-script-store-sessionid"></a>Przykładowy skrypt: Identyfikator sesji sklepu

Odpowiedź z powyższego żądania zawiera **sessionId**, który jest potrzebny dla wszystkich żądań kontynuacji.

```PowerShell
$sessionId = "d31bddca-dab7-498e-9bc9-7594bc12862f"
```

## <a name="update-a-session"></a>Aktualizowanie sesji

To polecenie aktualizuje parametry sesji. Obecnie można przedłużyć tylko czas dzierżawy sesji.

> [!IMPORTANT]
> Czas dzierżawy jest zawsze podawany jako całkowity czas od początku sesji. Oznacza to, że jeśli utworzono sesję z czasem dzierżawy jednej godziny i chcesz przedłużyć jej czas dzierżawy o kolejną godzinę, musisz zaktualizować jego maxLeaseTime do dwóch godzin.

| Identyfikator URI | Metoda |
|-----------|:-----------|
| /v1/accounts/*accountID*/sessions/sessionId*sessionId* | Patch |

**Treść żądania:**

* maxLeaseTime (timespan): wartość limitu czasu, gdy maszyna wirtualna zostanie automatycznie wycofana

**Odpowiedzi:**

| Kod stanu | Ładowność JSON | Komentarze |
|-----------|:-----------|:-----------|
| 200 | | Powodzenie |

### <a name="example-script-update-a-session"></a>Przykładowy skrypt: aktualizowanie sesji

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId" -Method Patch -ContentType "application/json" -Body "{ 'maxLeaseTime': '5:0:0' }" -Headers @{ Authorization = "Bearer $token" }
```

Przykładowe dane wyjściowe:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : {}
RawContent        : HTTP/1.1 200 OK
                    MS-CV: Fe+yXCJumky82wuoedzDTA.0
                    Content-Length: 0
                    Date: Thu, 09 May 2019 16:27:31 GMT


Headers           : {[MS-CV, Fe+yXCJumky82wuoedzDTA.0], [Content-Length, 0], [Date, Thu, 09 May 2019 16:27:31 GMT]}
RawContentLength  : 0
```

## <a name="get-active-sessions"></a>Uatrzyj aktywne sesje

To polecenie zwraca listę aktywnych sesji.

| Identyfikator URI | Metoda |
|-----------|:-----------|
| /v1/accounts/*accountId*/sessions | GET |

**Odpowiedzi:**

| Kod stanu | Ładowność JSON | Komentarze |
|-----------|:-----------|:-----------|
| 200 | - sesje: tablica właściwości sesji | zobacz sekcję "Pobierz właściwości sesji", aby uzyskać opis właściwości sesji |

### <a name="example-script-query-active-sessions"></a>Przykładowy skrypt: kwerenda aktywnych sesji

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions" -Method Get -Headers @{ Authorization = "Bearer $token" }
```

Przykładowe dane wyjściowe:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : []
RawContent        : HTTP/1.1 200 OK
                    MS-CV: WfB9Cs5YeE6S28qYkp7Bhw.1
                    Content-Length: 15
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 25 Jul 2019 16:23:50 GMT

                    {"sessions":[]}
Forms             : {}
Headers           : {[MS-CV, WfB9Cs5YeE6S28qYkp7Bhw.1], [Content-Length, 2], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 25 Jul 2019 16:23:50 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 2
```

## <a name="get-sessions-properties"></a>Pobierz właściwości sesji

To polecenie zwraca informacje o sesji, takie jak jej nazwa hosta maszyny Wirtualnej.

| Identyfikator URI | Metoda |
|-----------|:-----------|
| /v1/accounts/*accountId*/sessions/sessionId /properties*sessionId* | GET |

**Odpowiedzi:**

| Kod stanu | Ładowność JSON | Komentarze |
|-----------|:-----------|:-----------|
| 200 | - komunikat: ciąg<br/>- sessionElapsedTime: timespan<br/>- sessionHostname: string<br/>- sessionId: ciąg<br/>- sesjaMaxLeaseTime: timespan<br/>- sessionSize: wyliczenie<br/>- sessionStatus: wyliczenie | enum sessionStatus { starting, ready, stopping, stopped, expired, error}<br/>Jeśli stan jest "błąd" lub "wygasł", wiadomość będzie zawierać więcej informacji |

### <a name="example-script-get-session-properties"></a>Przykładowy skrypt: Pobierz właściwości sesji

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId/properties" -Method Get -Headers @{ Authorization = "Bearer $token" }
```

Przykładowe dane wyjściowe:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : {"message":null,"sessionElapsedTime":"00:00:01","sessionHostname":"5018fee8-817e-4366-9179-556af79a4240.remoterenderingvm.westeurope.mixedreality.azure.com","sessionId":"e13d2c44-63e0-4591-991e-f9e05e599a93","sessionMaxLeaseTime":"04:00:00","sessionStatus":"Ready"}
RawContent        : HTTP/1.1 200 OK
                    MS-CV: CMXegpZRMECH4pbOW2j5GA.0
                    Content-Length: 60
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 09 May 2019 16:30:38 GMT

                    {"message":null,...
Forms             : {}
Headers           : {[MS-CV, CMXegpZRMECH4pbOW2j5GA.0], [Content-Length, 60], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 09 May 2019 16:30:38 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 60
```

## <a name="stop-a-session"></a>Zatrzymywać sesję

To polecenie zatrzymuje sesję. Przydzielona maszyna wirtualna zostanie odzyskana wkrótce po.

| Identyfikator URI | Metoda |
|-----------|:-----------|
| /v1/accounts/*accountId*/sessions/sessionId*sessionId* | DELETE |

**Odpowiedzi:**

| Kod stanu | Ładowność JSON | Komentarze |
|-----------|:-----------|:-----------|
| 204 | | Powodzenie |

### <a name="example-script-stop-a-session"></a>Przykładowy skrypt: Zatrzymywać sesję

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId" -Method Delete -Headers @{ Authorization = "Bearer $token" }
```

Przykładowe dane wyjściowe:

```PowerShell
StatusCode        : 204
StatusDescription : No Content
Content           : {}
RawContent        : HTTP/1.1 204 No Content
                    MS-CV: YDxR5/7+K0KstH54WG443w.0
                    Date: Thu, 09 May 2019 16:45:41 GMT


Headers           : {[MS-CV, YDxR5/7+K0KstH54WG443w.0], [Date, Thu, 09 May 2019 16:45:41 GMT]}
RawContentLength  : 0
```

## <a name="next-steps"></a>Następne kroki

* [Przykładowe skrypty programu PowerShell](../samples/powershell-example-scripts.md)
