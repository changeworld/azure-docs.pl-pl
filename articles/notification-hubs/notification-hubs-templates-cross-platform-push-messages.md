---
title: Szablony
description: W tym temacie wyjaśniono szablonów dla usługi Azure notification hubs.
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: a41897bb-5b4b-48b2-bfd5-2e3c65edc37e
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 02473eb5649c7d201b6a54fd57faea997c1a21cc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60872109"
---
# <a name="templates"></a>Szablony

Szablony pozwalają one aplikacji klienckiej do określenia dokładnego formatu powiadomienia, które chce otrzymać. Za pomocą szablonów, aplikację można weź pod uwagę kilka różne korzyści, w tym następujące czynności:

- Zaplecze niezależne od platformy
- Spersonalizowanych powiadomień
- Niezależność od wersji klienta
- Lokalizacja jest łatwe

Ta sekcja zawiera dwa przykłady szczegółowy sposób użycia szablonów, można wysyłać powiadomienia niezależne od platformy, przeznaczone dla wszystkich urządzeń na platformach i spersonalizować emisji powiadomień do poszczególnych urządzeń.

## <a name="using-templates-cross-platform"></a>Za pomocą szablonów dla wielu platform

Jest standardowy sposób wysyłania powiadomień wypychanych do wysłania do każdego powiadomienia, który ma być wysyłane określonych ładunku do usług powiadomień platformy (WNS, APNS). Na przykład aby wysłać alert do usługi APNS, ładunek jest obiekt JSON w następującej postaci:

```json
{"aps": {"alert" : "Hello!" }}
```

Aby wysłać podobny komunikat wyskakującego powiadomienia w aplikacji Windows Store, ładunek XML jest następująca:

```xml
<toast>
  <visual>
    <binding template=\"ToastText01\">
      <text id=\"1\">Hello!</text>
    </binding>
  </visual>
</toast>
```

Podobne ładunki można utworzyć dla usługi MPNS (Windows Phone) i FCM (Android).

To wymaganie wymusza zaplecza aplikacji, aby wygenerować różne ładunków dla każdej platformy i skutecznie sprawia, że zaplecze odpowiada za część warstwy prezentacji aplikacji. Niektóre problemy obejmują lokalizacji i układy graficznego (szczególnie w przypadku aplikacji Windows Store, które obejmują powiadomienia dla różnych typów kafelków).

Funkcja szablonów usługi Notification Hubs umożliwia aplikację kliencką na tworzenie specjalnych rejestracji, o nazwie rejestracja szablonu, które obejmują oprócz zestaw znaczników, szablon. Funkcja szablonu usługi Notification Hubs umożliwia aplikacji klienckiej skojarzyć urządzenia przy użyciu szablonów, czy pracujesz za pomocą instalacji (preferowany) lub rejestracji. Biorąc pod uwagę powyższych przykładach ładunek, informacje tylko niezależne od platformy jest rzeczywista komunikat alertu (Hello!). Szablon jest zestaw instrukcji dla Centrum powiadomień na temat do sformatowania komunikatu niezależne od platformy do rejestracji aplikacji określonego klienta. W powyższym przykładzie wiadomość niezależne od platformy jest jedną właściwość: `message = Hello!`.

Poniższej ilustracji przedstawiono proces:

![](./media/notification-hubs-templates/notification-hubs-hello.png)

Szablon rejestracji aplikacji klienta dla systemu iOS jest następująca:

```json
{"aps": {"alert": "$(message)"}}
```

Odpowiedni szablon dla aplikacji klienckiej Windows Store jest:

```xml
<toast>
    <visual>
        <binding template=\"ToastText01\">
            <text id=\"1\">$(message)</text>
        </binding>
    </visual>
</toast>
```

Należy zauważyć, że komunikat rzeczywiste zostanie zastąpiony wyrażenia $(komunikat). To wyrażenie powoduje, że Centrum powiadomień przy każdym do tej konkretnej rejestracji, do skompilowania komunikatu, występującego go i przełącznikach w typowych wartość jest wysyłana wiadomość.

Pracy z modelem instalacji instalacyjny klucz "templates" zawiera wiele szablonów JSON. Jeśli pracujesz z modelem rejestracji, aplikacja kliencka można utworzyć wiele rejestracji aby można było używać wielu szablonów; na przykład szablon służący do wiadomości o alertach i szablon służący do aktualizacji kafelka. Aplikacje klienckie można także łączyć rejestracje natywnego (rejestracji przy użyciu szablonu) i rejestracja szablonu.

Centrum powiadomień wysyła jedno powiadomienie dla każdego szablonu bez uwzględniania należą do tej samej aplikacji klienckiej. To zachowanie może służyć do translacji powiadomienia niezależne od platformy do więcej powiadomień. Na przykład ten sam komunikat niezależne od platformy Centrum powiadomień mogą być bezproblemowo tłumaczone alert wyskakujące i aktualizowanie kafelka, bez konieczności zaplecza informację. Niektóre platformy (na przykład, system iOS) może być Zwiń wiele powiadomień na tym samym urządzeniu, jeśli są one wysyłane w krótkim czasie.

## <a name="using-templates-for-personalization"></a>Korzystanie z szablonów na potrzeby personalizacji

Inną zaletą przy użyciu szablonów jest możliwość wykonywania personalizacji na rejestracji powiadomień za pomocą usługi Notification Hubs. Na przykład należy wziąć pod uwagę aplikację pogody, która wyświetla Kafelek z warunków pogodowych w określonej lokalizacji. Użytkownik może wybrać między stopniach Celsjusza lub Fahrenheita i prognozy pojedyncze lub pięć dni. Za pomocą szablonów, poszczególnych instalacji aplikacji klienta można zarejestrować do formatu wymaganego (1-dniowym Celsjusza, 1-dniowym Fahrenheita, 5 dni stopniach Celsjusza, 5 dni Stopnie Fahrenheita), i wysyłać jedną wiadomość, która zawiera wszystkie informacje wymagane do wypełniania tych szablonów wewnętrznej bazy danych (na przykład prognozy przy użyciu stopniach Celsjusza i Fahrenheita pięć dni).

Szablon na potrzeby prognozy jeden dzień w stopniach Celsjusza temperatury jest w następujący sposób:

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

Komunikat wysyłany do Centrum powiadomień zawiera następujące właściwości:

```html
<table border="1">

<tr><td>day1_image</td><td>day2_image</td><td>day3_image</td><td>day4_image</td><td>day5_image</td></tr>

<tr><td>day1_tempC</td><td>day2_tempC</td><td>day3_tempC</td><td>day4_tempC</td><td>day5_tempC</td></tr>

<tr><td>day1_tempF</td><td>day2_tempF</td><td>day3_tempF</td><td>day4_tempF</td><td>day5_tempF</td></tr>
</table><br/>
```

Korzystając z tego wzorca, wewnętrznej bazy danych tylko wysyła pojedynczą wiadomość bez konieczności przechowywania opcji personalizacji określone dla użytkowników aplikacji. Poniższy obraz przedstawia ten scenariusz:

![](./media/notification-hubs-templates/notification-hubs-registration-specific.png)

## <a name="how-to-register-templates"></a>Jak zarejestrować szablony

Aby zarejestrować się przy użyciu szablonów przy użyciu modelu instalacji (preferowany) lub model rejestrowania, zobacz [Zarządzanie rejestracją](notification-hubs-push-notification-registration-management.md).

## <a name="template-expression-language"></a>Język wyrażeń

Szablony są ograniczone do formatowania dokumentu XML lub JSON. Ponadto tylko można umieścić wyrażenia w szczególności miejscach; na przykład węzeł atrybutów lub wartości XML ciągu wartości właściwości dla formatu JSON.

W poniższej tabeli przedstawiono języka dozwolone w szablonach:

| Wyrażenie       | Opis |
| ---------------- | --- |
| $(prop)          | Odwołanie do właściwości zdarzenia o podanej nazwie. Nazwy właściwości nie jest rozróżniana wielkość liter. To wyrażenie rozpoznaje do właściwości wartość tekstową lub pusty ciąg, jeśli właściwość nie jest obecny. |
| $(prop, n)       | Jak powyżej ale tekst jest jawnie przycinana n znaków, na przykład $(tytuł, 20) przycina zawartość właściwości title przy 20 znaków. |
| . (prop, n)       | Jak powyżej ale tekst jako sufiks z trzema kropkami zgodnie z jego zostanie obcięta. Łączny rozmiar ciągu obciętych i sufiksem nie przekracza n znaków. . (title, 20) za pomocą właściwości wejściowej "Jest wiersz tytułu" powoduje powstanie **to tytuł...** |
| %(Prop)          | Podobnie jak $(name) z tą różnicą, że dane wyjściowe są zakodowane w formacie identyfikatora URI. |
| #(prop)          | Używane w szablonach JSON (na przykład dla systemów iOS i Android szablonów).<br><br>Ta funkcja działa tak samo jak $(prop) wcześniej określony, z wyjątkiem w szablonach JSON (na przykład szablony firmy Apple). W tym przypadku, jeśli ta funkcja nie jest ujęta w "{","}" (na przykład "myJsonProperty": "#(nazwa)"), a był oceniany na liczbę w formacie Javascript, na przykład regexp: (0&#124;(&#91;1-9&#93;&#91;0-9&#93;*)) (\.&#91;0-9&#93;+)? ((e&#124;E) (+&#124;—)? &#91;0-9&#93;+)?, a następnie dane wyjściowe JSON jest liczbą.<br><br>Na przykład "wskaźnik:"#(nazwa)"staje się"badge": 40 (i nie 40). |
| "tekst" lub "text" | Literał. Literały zawierać dowolny tekst, ujęty w pojedynczym lub podwójnym cudzysłowie. |
| Wyr1 + Wyr2    | Operator łączenia Sprzęganie dwóch wyrażeń w jeden ciąg. |

Wyrażenie może być dowolny z poprzednich formularzy.

Korzystając z łączenia, całe wyrażenie musi być ujęty w `{}`. Na przykład `{$(prop) + ‘ - ’ + $(prop2)}`.

Na przykład następujący szablon nie jest prawidłowym szablonem XML:

```xml
<tile>
  <visual>
    <binding $(property)>
      <text id="1">Seattle, WA</text>
    </binding>  
  </visual>
</tile>
```

Jak wyjaśniono wcześniej, korzystając z łączenia, wyrażenia muszą być zapakowane w nawiasach klamrowych. Na przykład:

```xml
<tile>
  <visual>
    <binding template="ToastText01">
      <text id="1">{'Hi, ' + $(name)}</text>
    </binding>  
  </visual>
</tile>
```
