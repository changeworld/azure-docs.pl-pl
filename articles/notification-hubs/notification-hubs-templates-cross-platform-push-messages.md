---
title: Szablony
description: W tym temacie wyjaśniono szablonów usługi Azure notification hubs.
services: notification-hubs
documentationcenter: .net
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: a41897bb-5b4b-48b2-bfd5-2e3c65edc37e
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 3e587bdf0efc7c5b416183640abb19286a5cff31
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33776655"
---
# <a name="templates"></a>Szablony
## <a name="overview"></a>Przegląd
Szablony umożliwiają służy do określania dokładnej powiadomienia, które chce otrzymywać aplikacji klienta. Za pomocą szablonów, aplikacji może realizować kilka różnych korzyści, takie jak następujące:

* Niezależne od platformy wewnętrznej bazie danych
* Spersonalizowanych powiadomień
* Niezależność od wersji klienta
* Łatwe lokalizacji

Ta sekcja zawiera dwa szczegółowe przykłady sposobów użycia szablonów do wysyłania powiadomień niezależny od platformy przeznaczonych dla wszystkich urządzeń na platformach i spersonalizować emisji powiadomienia do poszczególnych urządzeń.

## <a name="using-templates-cross-platform"></a>Za pomocą szablonów i platform
Jest standardowym sposobem wysyłania powiadomień wypychanych do wysłania do każdego powiadomienia, który ma być wysyłane określonych ładunku do usług powiadomień platformy (WNS, APNS). Na przykład aby wysyłać alert do usługi APNS, ładunku jest obiektem Json następującą postać:

    {"aps": {"alert" : "Hello!" }}

Aby wysłać wiadomość wyskakujące podobne w aplikacji Sklepu Windows, ładunek XML jest następująca:

    <toast>
      <visual>
        <binding template=\"ToastText01\">
          <text id=\"1\">Hello!</text>
        </binding>
      </visual>
    </toast>

Podobne ładunki można tworzyć dla usługi MPNS (Windows Phone) i usługi GCM platform (Android).

To wymaganie wymusza zaplecza aplikacji do tworzenia różnych ładunków dla każdej platformy i efektywnie sprawia, że wewnętrznej bazy danych odpowiada za część warstwy prezentacji w aplikacji. Niektóre problemy obejmują lokalizacja i układy graficznego (szczególnie w przypadku aplikacji ze Sklepu Windows obejmujących powiadomień dla różnych typów Kafelki).

Funkcja szablonu usługi Notification Hubs umożliwia aplikacji klienta, można utworzyć specjalne rejestracji, nazywane rejestracji szablonu, między innymi, oprócz zestawu tagów, szablon. Funkcja szablonu usługi Notification Hubs umożliwia aplikacji klienta skojarzyć urządzenia z szablonów, czy korzystasz z urządzenia (preferowane) lub rejestracji. Podana w poprzednich przykładach ładunku, informacje tylko niezależne od platformy jest rzeczywista komunikat alertu (Witaj!). Szablon jest zestaw instrukcji dla Centrum powiadomień dotyczące formatowania komunikat niezależny od platformy rejestracji, aplikacja określonego klienta. W powyższym przykładzie komunikat niezależny od platformy jest jednej właściwości: **wiadomość = Hello!**.

Poniżej przedstawiono proces:

![](./media/notification-hubs-templates/notification-hubs-hello.png)

Szablon rejestracji aplikacji dla systemu iOS klienta jest następujący:

    {"aps": {"alert": "$(message)"}}

Odpowiedni szablon dla aplikacji ze Sklepu Windows klienta jest:

    <toast>
        <visual>
            <binding template=\"ToastText01\">
                <text id=\"1\">$(message)</text>
            </binding>
        </visual>
    </toast>

Należy zauważyć, że rzeczywista komunikat zostanie zastąpiony wyrażenia $(komunikat). To wyrażenie powoduje, że Centrum powiadomień przy każdym wysyła komunikat do tej określonej rejestracji, aby utworzyć komunikat, który wykonuje go i przełączników w wartości typowych.

Podczas pracy z modelem instalacji klucz "Szablony" instalacji przechowuje JSON wielu szablonów. Podczas pracy z modelem rejestracji aplikacji klienckiej, można utworzyć wiele rejestracji aby można było używać wielu szablonów; na przykład szablon komunikaty alertów i szablon do aktualizacji kafelka. Aplikacje klienckie można również mieszać rejestracje natywnego (rejestracje z szablonu) i rejestracji szablonu.

Centrum powiadomień wysyła jedno powiadomienie dla każdego szablonu bez rozważania należą do tej samej aplikacji klienckiej. To zachowanie może służyć do tłumaczenia powiadomienia niezależne od platformy do powiadomień więcej. Na przykład ten sam komunikat niezależny od platformy do Centrum powiadomień można bezproblemowo przeliczane alert wyskakujące i aktualizacji kafelka, bez konieczności wewnętrznej bazy danych należy pamiętać o jego. Niektóre platformy (na przykład iOS) może Zwiń wiele powiadomień do tego samego urządzenia, jeśli są one wysyłane w krótkim czasie.

## <a name="using-templates-for-personalization"></a>Za pomocą szablonów na potrzeby personalizacji
Inną zaletą używania szablonów jest możliwość wykonania na rejestracji personalizacji powiadomień przy użyciu usługi Notification Hubs. Rozważmy na przykład aplikację pogodzie, która wyświetla kafelka z pogodą z określonej lokalizacji. Użytkownik może wybrać między stopni c lub f i prognozy pojedyncze lub pięć dni. Za pomocą szablonów, instalacja aplikacji każdego klienta można zarejestrować do formatu wymaganego (1-dniowego Celsjusz, 1-dniowego f, 5 dni c, 5 dni Stopnie Fahrenheita), i mieć zaplecza Wyślij pojedynczy komunikat, który zawiera wszystkie informacje wymagane do wypełnienia tych szablonów (na przykład pięciodniowego prognozy z stopni c i f).

Szablon prognozy jeden dzień z c temperatury wygląda następująco:

    <tile>
      <visual>
        <binding template="TileWideSmallImageAndText04">
          <image id="1" src="$(day1_image)" alt="alt text"/>
          <text id="1">Seattle, WA</text>
          <text id="2">$(day1_tempC)</text>
        </binding>  
      </visual>
    </tile>

Komunikat wysłany do Centrum powiadomień zawiera następujące właściwości:

<table border="1">

<tr><td>day1_image</td><td>day2_image</td><td>day3_image</td><td>day4_image</td><td>day5_image</td></tr>

<tr><td>day1_tempC</td><td>day2_tempC</td><td>day3_tempC</td><td>day4_tempC</td><td>day5_tempC</td></tr>

<tr><td>day1_tempF</td><td>day2_tempF</td><td>day3_tempF</td><td>day4_tempF</td><td>day5_tempF</td></tr>
</table><br/>

Za pomocą tego wzorca, wewnętrznej bazy danych tylko wysyła pojedynczą wiadomość bez konieczności przechowywania opcji personalizacji określonych dla użytkowników aplikacji. Poniżej przedstawiono w tym scenariuszu:

![](./media/notification-hubs-templates/notification-hubs-registration-specific.png)

## <a name="how-to-register-templates"></a>Jak zarejestrować szablonów
Aby zarejestrować z szablonów przy użyciu modelu instalacji (preferowane) lub model rejestracji, zobacz [zarządzania rejestracji](notification-hubs-push-notification-registration-management.md).

## <a name="template-expression-language"></a>Język wyrażeń
Szablony są ograniczone do formatowania dokumentu XML lub JSON. Ponadto tylko można umieścić wyrażenia w szczególności miejscach; na przykład atrybutów węzła lub wartości XML ciągu wartości właściwości dla formatu JSON.

W poniższej tabeli przedstawiono dozwolone w szablonach język:

| Wyrażenie | Opis |
| --- | --- |
| $(prop) |Odwołanie do właściwości zdarzenia o podanej nazwie. Nazwy właściwości nie jest rozróżniana. To wyrażenie rozpoznaje do właściwości wartości tekstowej lub pusty ciąg, jeśli nie ma właściwości. |
| $(prop, n) |Jak powyżej ale tekst jest jawnie przycinana n znaków, na przykład $(tytuł, 20) przycina zawartość plików właściwości title 20 znaków. |
| . (prop, n) |Jak powyżej ale tekst jest sufiksem z wielokropkiem, jak ma zostać przycięta. Całkowity rozmiar ciągu przyciętą i sufiksu nie przekracza n znaków. . (tytuł, 20) z właściwości wejściowej "Jest wiersz tytułu" skutkuje **to jest tytuł...** |
| %(Prop) |Podobnie jak $(name) z tą różnicą, że dane wyjściowe są zakodowane w formacie identyfikatora URI. |
| #(prop) |Używane w szablonach JSON (na przykład dla systemów iOS i Android szablonów).<br><br>Ta funkcja działa tak samo jak $(prop) wcześniej określony, z wyjątkiem przypadków, gdy używane w szablonach JSON (na przykład szablony Apple). W tym przypadku, jeśli ta funkcja nie jest ujęta w "{","}" (na przykład "myJsonProperty": "#(nazwa)"), a jej wynikiem jest liczba w formacie Javascript, na przykład regexp: (0&#124;(&#91;1-9&#93;&#91;0-9&#93;*)) (\. &#91;0-9&#93;+)? ((e&#124;E) (+&#124;—)? &#91;0-9&#93;+)?, a następnie dane wyjściowe JSON jest liczbą.<br><br>Na przykład "badge:"#(nazwa)"staje się"badge": 40 (a nie"40"). |
| "tekst" lub "text" |Literału. Literały zawiera dowolny tekst ujęty w pojedynczym lub podwójnym cudzysłowie. |
| Wyr1 + Wyr2 |Operator łączenia Sprzęganie dwóch wyrażeń w jednym ciągu. |

Wyrażenia może być dowolny z poprzednim formularzy.

Korzystając z łączenia, całe wyrażenie musi być ujęty w {}. Na przykład {$(prop) + "-" + $(prop2)}. |

Na przykład następujący szablon nie jest prawidłowy szablon XML:

    <tile>
      <visual>
        <binding $(property)>
          <text id="1">Seattle, WA</text>
        </binding>  
      </visual>
    </tile>


Zgodnie z opisem, korzystając z łączenia, wyrażenia musi być ujęte w nawiasach klamrowych. Na przykład:

    <tile>
      <visual>
        <binding template="ToastText01">
          <text id="1">{'Hi, ' + $(name)}</text>
        </binding>  
      </visual>
    </tile>

