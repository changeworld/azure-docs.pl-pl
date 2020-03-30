---
title: Wersje układu strony
titleSuffix: Azure AD B2C
description: Historia wersji układu strony do dostosowywania interfejsu użytkownika w zasadach niestandardowych.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3d0cb06f84fdd96d099e05f55ba62c37cb1192c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183979"
---
# <a name="page-layout-versions"></a>Wersje układu strony

Pakiety układu strony są okresowo aktualizowane w celu uwzględnienia poprawek i ulepszeń w elementach strony. Poniższy dziennik zmian określa zmiany wprowadzone w każdej wersji.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="200"></a>2.0.0

- Strona samodzielnie potwierdzona`selfasserted`( )
  - Dodano obsługę [kontrolek wyświetlania](display-controls.md) w zasadach niestandardowych.

## <a name="120"></a>1.2.0

- Wszystkie strony
  - Poprawki ułatwień dostępu
  - Teraz możesz dodać `data-preload="true"` atrybut [w tagach HTML,](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) aby kontrolować kolejność ładowania css i JavaScript.
    - Ładuj połączone pliki CSS w tym samym czasie co szablon HTML, aby nie "migotał" między ładowaniem plików.
    - Kontroluj kolejność `script` pobierania i wykonywania tagów przed załadowaniem strony.
  - Pole poczty `type=email` e-mail jest teraz, a klawiatury mobilne będą dostarczać poprawne sugestie
  - Obsługa tłumaczenia Chrome
- Ujednolicone i samodzielnie dochodzone strony
  - Pola nazwy użytkownika/adresu e-mail i hasła używają teraz elementu `form` HTML, aby umożliwić programowi Edge i Internet Explorer (IE) prawidłowe zapisanie tych informacji.

## <a name="110"></a>1.1.0

- Strona wyjątku (globalexception)
  - Poprawka dotycząca ułatwień dostępu
  - Usunięto domyślną wiadomość, gdy nie ma kontaktu z zasadami
  - Usunięto domyślny CSS
- Strona usługi MFA (wieloczynnikowa)
  - Usunięto przycisk "Potwierdź kod"
  - Pole wejściowe kodu przyjmuje teraz tylko maksymalnie sześć (6) znaków
  - Strona automatycznie podejmie próbę zweryfikowania kodu wprowadzonego po wprowadzeniu 6-cyfrowego kodu, bez konieczności klikania przycisku
  - Jeśli kod jest nieprawidłowy, pole wprowadzania jest automatycznie czyszczone
  - Po trzech (3) próbach z nieprawidłowym kodem B2C wysyła błąd z powrotem do jednostki uzależniającej
  - Poprawki ułatwień dostępu
  - Usunięto domyślny CSS
- Strona z własnym potwierdzeniem (selfasserted)
  - Usunięto alert anulowania
  - Klasa CSS dla elementów błędu
  - Poprawiono logikę błędu pokazywal/ukrywanie
  - Usunięto domyślny CSS
- Ujednolicony SSP (unifiedssp)
  - Dodano kontrolę keep me signed in (KMSI)

## <a name="100"></a>1.0.0

- Wersja początkowa

## <a name="next-steps"></a>Następne kroki

Aby uzyskać szczegółowe informacje na temat dostosowywania interfejsu użytkownika aplikacji w zasadach niestandardowych, zobacz [Dostosowywanie interfejsu użytkownika aplikacji przy użyciu zasad niestandardowych](custom-policy-ui-customization.md).
