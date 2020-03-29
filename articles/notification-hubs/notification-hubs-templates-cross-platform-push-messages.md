---
title: Szablony centrów powiadomień platformy Azure
description: Dowiedz się więcej o używaniu szablonów dla centrów powiadomień platformy Azure.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: a41897bb-5b4b-48b2-bfd5-2e3c65edc37e
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 7d88f57fe92b9da62cc9f90d64bdec4c27642fb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263748"
---
# <a name="templates"></a>Szablony

Szablony umożliwiają aplikacji klienckiej określenie dokładnego formatu powiadomień, które chce otrzymywać. Korzystając z szablonów, aplikacja może zrealizować kilka różnych korzyści, w tym następujące:

- Zaplecze niezależne od platformy
- Spersonalizowane powiadomienia
- Niezależność wersji klienta
- Łatwa lokalizacja

Ta sekcja zawiera dwa szczegółowe przykłady używania szablonów do wysyłania powiadomień z certyfikatem platformy, kierowanych na wszystkie urządzenia na różnych platformach oraz do personalizowania powiadomień o emisji na każdym urządzeniu.

## <a name="using-templates-cross-platform"></a>Korzystanie z szablonów międzyplatformowych

Standardowy sposób wysyłania powiadomień wypychanych jest wysyłanie, dla każdego powiadomienia, które mają być wysyłane, określonego ładunku do usług powiadomień platformy (WNS, APNS). Na przykład, aby wysłać alert do usługi APNS, ładunek jest obiektem JSON w następującym formularzu:

```json
{"aps": {"alert" : "Hello!" }}
```

Aby wysłać podobną wiadomość wyskakujących w aplikacji ze Sklepu Windows, ładunek XML jest następujący:

```xml
<toast>
  <visual>
    <binding template=\"ToastText01\">
      <text id=\"1\">Hello!</text>
    </binding>
  </visual>
</toast>
```

Można utworzyć podobne ładunki dla platform MPNS (Windows Phone) i FCM (Android).

To wymaganie wymusza wewnętrznej bazy danych aplikacji do tworzenia różnych ładunków dla każdej platformy i skutecznie sprawia, że wewnętrznej bazy danych odpowiedzialny za część warstwy prezentacji aplikacji. Niektóre problemy obejmują lokalizację i układy graficzne (szczególnie w przypadku aplikacji ze Sklepu Windows, które zawierają powiadomienia dla różnych typów kafelków).

Funkcja szablonu Centra powiadomień umożliwia aplikacji klienckiej tworzenie specjalnych rejestracji, nazywanych rejestracjami szablonów, które zawierają, oprócz zestawu tagów, szablon. Funkcja szablonu Centra powiadomień umożliwia aplikacji klienckiej kojarzenie urządzeń z szablonami, niezależnie od tego, czy pracujesz z instalacjami (preferowanymi) czy rejestracjami. Biorąc pod uwagę poprzednie przykłady ładunku, jedyną informacją niezależną od platformy jest rzeczywisty komunikat alertu (Hello!). Szablon to zestaw instrukcji dla Centrum powiadomień dotyczące formatowania wiadomości niezależnej od platformy do rejestracji tej konkretnej aplikacji klienckiej. W poprzednim przykładzie komunikat niezależny od platformy jest `message = Hello!`pojedynczą właściwością: .

Poniższy obraz ilustruje proces:

![](./media/notification-hubs-templates/notification-hubs-hello.png)

Szablon rejestracji aplikacji klienckiej systemu iOS jest następujący:

```json
{"aps": {"alert": "$(message)"}}
```

Odpowiedni szablon dla aplikacji klienckiej ze Sklepu Windows to:

```xml
<toast>
    <visual>
        <binding template=\"ToastText01\">
            <text id=\"1\">$(message)</text>
        </binding>
    </visual>
</toast>
```

Należy zauważyć, że rzeczywista wiadomość jest zastępowany wyrażenie $(message). To wyrażenie nakazuje Centrum powiadomień, gdy wysyła komunikat do tej konkretnej rejestracji, aby utworzyć komunikat, który następuje po nim i przełącza się we wspólnej wartości.

Jeśli pracujesz z modelem instalacji, klucz "szablony" instalacji zawiera JSON wielu szablonów. Jeśli pracujesz z modelem rejestracji, aplikacja kliencka może utworzyć wiele rejestracji w celu użycia wielu szablonów; na przykład szablon dla komunikatów alertów i szablon aktualizacji kafelków. Aplikacje klienckie można również mieszać rejestracje natywne (rejestracje bez szablonu) i rejestracji szablonów.

Centrum powiadomień wysyła jedno powiadomienie dla każdego szablonu, nie biorąc pod uwagę, czy należą one do tej samej aplikacji klienckiej. To zachowanie może służyć do tłumaczenia powiadomień niezależnych od platformy na więcej powiadomień. Na przykład ten sam komunikat niezależny od platformy do Centrum powiadomień można bezproblemowo przetłumaczyć w wyskakujący alert i aktualizację kafelków, bez konieczności wewnętrznej bazy danych, aby być tego świadomym. Niektóre platformy (na przykład iOS) może zwinąć wiele powiadomień do tego samego urządzenia, jeśli są one wysyłane w krótkim czasie.

## <a name="using-templates-for-personalization"></a>Używanie szablonów do personalizacji

Kolejną zaletą korzystania z szablonów jest możliwość korzystania z Centrum powiadomień do wykonywania personalizacji powiadomień na rejestrację. Rozważmy na przykład aplikację pogodową, która wyświetla kafelek z warunkami pogodowymi w określonej lokalizacji. Użytkownik może wybierać między stopniami Celsjusza lub Fahrenheita a prognozą pojedynczą lub pięciodniową. Korzystając z szablonów, każda instalacja aplikacji klienta może zarejestrować się w wymaganym formacie (1 dzień Celsjusza, 1-dniowy Fahrenheita, 5 dni Celsjusza, 5 dni Fahrenheita) i wysłać zaplecze jedną wiadomość, która zawiera wszystkie informacje wymagane do wypełnienia tych szablonów (na przykład prognoza pięciodniowa z stopniami Celsjusza i Fahrenheita).

Szablon prognozy jednodniowej z temperaturami Celsjusza jest następujący:

```xml
<tile>
  <visual>
    <binding template="TileWideSmallImageAndText04">
      <image id="1" src="$(day1_image)" alt="alt text"/>
      <text id="1">Seattle, WA</text>
      <text id="2">$(day1_tempC)</text>
    </binding>  
  </visual>
</tile>
```

Wiadomość wysłana do Centrum powiadomień zawiera wszystkie następujące właściwości:

```html
<table border="1">

<tr><td>day1_image</td><td>day2_image</td><td>day3_image</td><td>day4_image</td><td>day5_image</td></tr>

<tr><td>day1_tempC</td><td>day2_tempC</td><td>day3_tempC</td><td>day4_tempC</td><td>day5_tempC</td></tr>

<tr><td>day1_tempF</td><td>day2_tempF</td><td>day3_tempF</td><td>day4_tempF</td><td>day5_tempF</td></tr>
</table><br/>
```

Za pomocą tego wzorca wewnętrznej bazy danych wysyła tylko jedną wiadomość bez konieczności przechowywania określonych opcji personalizacji dla użytkowników aplikacji. Poniższy obraz ilustruje ten scenariusz:

![](./media/notification-hubs-templates/notification-hubs-registration-specific.png)

## <a name="how-to-register-templates"></a>Jak zarejestrować szablony

Aby zarejestrować się w szablonach przy użyciu modelu instalacji (preferowany) lub modelu rejestracji, zobacz [Zarządzanie rejestracją](notification-hubs-push-notification-registration-management.md).

## <a name="template-expression-language"></a>Język wyrażenia szablonu

Szablony są ograniczone do formatów dokumentów XML lub JSON. Ponadto można umieszczać wyrażenia tylko w określonych miejscach; na przykład atrybuty węzła lub wartości XML, wartości właściwości ciągu dla JSON.

W poniższej tabeli przedstawiono język dozwolony w szablonach:

| Wyrażenie       | Opis |
| ---------------- | --- |
| $(prop)          | Odwołanie do właściwości zdarzenia o podanej nazwie. W nazwach właściwości nie jest rozróżniana wielkość liter. To wyrażenie jest rozpoznawane w wartości tekstowej właściwości lub do pustego ciągu, jeśli właściwość nie jest obecny. |
| $(prop, n)       | Jak wyżej, ale tekst jest jawnie przycięty na n znaków, na przykład $(tytuł, 20) klipy zawartość właściwości tytuł na 20 znaków. |
| . (prop, n)       | Jak wyżej, ale tekst jest sufiksem z trzema kropkami, ponieważ jest przycięty. Całkowity rozmiar przyciętego ciągu i sufiksu nie przekracza n znaków. . (tytuł, 20) z właściwością wejściową "To jest wiersz tytułu" wyniki w **To jest tytuł...** |
| %(prop)          | Podobnie jak $(name), z tą różnicą, że dane wyjściowe są zakodowane w uri. |
| #(prop)          | Używane w szablonach JSON (na przykład dla szablonów systemów iOS i Android).<br><br>Ta funkcja działa dokładnie tak samo jak $(prop) wcześniej określony, z wyjątkiem, gdy jest używany w szablonach JSON (na przykład szablony Apple). W takim przypadku, jeśli ta funkcja nie jest otoczona przez "{','}" (na przykład 'myJsonProperty' : '#(name)') i ocenia na przykład liczbę w formacie Javascript, na przykład regexp: (0&#124;(&#91;1-9&#93;&#91;0-9&#93;*))(\.&#91;0-9&#93;+)? ((e&#124;E)(+&#124;-)?&#91;0-9&#93;+)?, wtedy wyjście JSON jest liczbą.<br><br>Na przykład 'plakietka: '#(name)' staje się 'plakietka': 40 (a nie '40'). |
| "tekst" lub "tekst" | Dosłowne. Literały zawierają dowolny tekst ujęty w pojedyncze lub podwójne cudzysłowy. |
| expr1 + expr2    | Operator łączenia dwóch wyrażeń w jeden ciąg. |

Wyrażenia mogą być dowolną z powyższych form.

Podczas korzystania z łączenia całe wyrażenie musi `{}`być otoczone . Na przykład `{$(prop) + ‘ - ’ + $(prop2)}`.

Na przykład poniższy szablon nie jest prawidłowym szablonem XML:

```xml
<tile>
  <visual>
    <binding $(property)>
      <text id="1">Seattle, WA</text>
    </binding>  
  </visual>
</tile>
```

Jak wyjaśniono wcześniej, podczas korzystania z łączenia wyrażenia muszą być owinięte w nawiasy klamrowe. Przykład:

```xml
<tile>
  <visual>
    <binding template="ToastText01">
      <text id="1">{'Hi, ' + $(name)}</text>
    </binding>  
  </visual>
</tile>
```

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o Centrum powiadomień platformy Azure](notification-hubs-push-notification-overview.md)