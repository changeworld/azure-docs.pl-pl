---
title: Wybierz układ strony — Azure Active Directory B2C
description: Dowiedz się więcej na temat wybierania układu strony w Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 108d86e35422e1dc1d10aeb6b2c9488f5067232e
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389690"
---
# <a name="select-a-page-layout-in-azure-active-directory-b2c-using-custom-policies"></a>Wybieranie układu strony w Azure Active Directory B2C przy użyciu zasad niestandardowych

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Kod po stronie klienta w języku JavaScript można włączyć w zasadach Azure Active Directory B2C (Azure AD B2C), niezależnie od tego, czy używasz przepływów użytkowników, czy zasad niestandardowych. Aby włączyć obsługę języka JavaScript dla aplikacji, musisz dodać element do [zasad niestandardowych](active-directory-b2c-overview-custom.md), wybrać układ strony i użyć [b2clogin.com](b2clogin.md) w swoich żądaniach.

Układ strony to skojarzenie elementów udostępnianych Azure AD B2C i zawartości.

W tym artykule omówiono sposób wybierania układu strony w Azure AD B2C przez skonfigurowanie go w niestandardowych zasadach.

> [!NOTE]
> Jeśli chcesz włączyć język JavaScript dla przepływów użytkownika, zobacz [wersje JavaScript i układ strony w Azure Active Directory B2C](user-flow-javascript-overview.md).

## <a name="replace-datauri-values"></a>Zastąp wartości DataUri

W zasadach niestandardowych można [ContentDefinitions](contentdefinitions.md) , które definiują szablony HTML używane w podróży użytkownika. **ContentDefinition** zawiera **DataUri** , który odwołuje się do elementów strony dostarczonych przez Azure AD B2C. **LoadUri** jest ścieżką względną do zawartości HTML i CSS, którą dostarczasz.

```XML
<ContentDefinition Id="api.idpselections">
  <LoadUri>~/tenant/default/idpSelector.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Idp selection page</Item>
    <Item Key="language.intro">Sign in</Item>
  </Metadata>
</ContentDefinition>
```

Aby wybrać układ strony, należy zmienić wartości **DataUri** w [ContentDefinitions](contentdefinitions.md) w zasadach. Przełączając od starych wartości **DataUri** do nowych wartości, wybierasz niezmienny pakiet. Zaletą korzystania z tego pakietu jest to, że wiadomo, że nie ulegnie zmianie i spowoduje nieoczekiwane zachowanie na stronie.

Aby skonfigurować układ strony, Skorzystaj z poniższej tabeli, aby znaleźć wartości **DataUri** .

| Stara wartość DataUri | Nowa wartość DataUri |
| ----------------- | ----------------- |
| `urn:com:microsoft:aad:b2c:elements:claimsconsent:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:claimsconsent:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.1.0` |

## <a name="version-change-log"></a>Dziennik zmian wersji

Pakiety układów stron są okresowo aktualizowane w celu uwzględnienia poprawek i ulepszeń w ich elementach strony. Następujący dziennik zmian określa zmiany wprowadzone w każdej wersji.

### <a name="120"></a>1.2.0 
- Wszystkie strony
  - Poprawki ułatwień dostępu
  - Teraz możesz dodać atrybut `data-preload="true"` w tagach HTML, aby kontrolować kolejność ładowania dla CSS i JavaScript. Scenariusze obejmują:
      - Użyj tej właściwości na linku CSS, aby załadować arkusz CSS w tym samym czasie co kod HTML, aby nie "migotać" między ładowaniem plików
      - Ten atrybut pozwala kontrolować kolejność, w jakiej Tagi skryptu są pobierane i wykonywane przed załadowaniem strony
  - Pole adresu e-mail jest teraz `type=email` i klawiatury przenośne zapewniają poprawne sugestie
  - Obsługa funkcji tłumaczenia dla programu Chrome
- Ujednolicona i własna strona
  - Pola username/email i Password używają teraz elementu HTML form.  Umożliwi to teraz prawidłowe zapisanie tych informacji w przeglądarce Edge i IE
  
### <a name="110"></a>1.1.0

- Strona wyjątku (globalexception)
  - Poprawka ułatwień dostępu
  - Usunięto komunikat domyślny, gdy nie ma kontaktu z zasad
  - Usunięto domyślny kod CSS
- Strona usługi MFA (wieloskładnikowa)
  - Przycisk "Potwierdź kod" został usunięty
  - Pole wejściowe dla kodu przyjmuje teraz tylko dane wejściowe z maksymalnie sześciu (6) znaków
  - Strona automatycznie podejmie próbę zweryfikowania kodu wprowadzonego po wprowadzeniu 6-cyfrowy kod bez kliknięcia przycisku
  - Jeśli kod jest nieprawidłowy, pole danych wejściowych zostanie automatycznie wyczyszczone
  - Po trzech (3) próbach z nieprawidłowym kodem B2C wysyła błąd z powrotem do jednostki uzależnionej
  - Poprawki ułatwień dostępu
  - Usunięto domyślny kod CSS
- Strona z własnym potwierdzeniem (selfasserted)
  - Usunięto alert dotyczący anulowania
  - Klasa CSS dla elementów Error
  - Ulepszone wyświetlanie/ukrywanie logiki błędów
  - Usunięto domyślny kod CSS
- Ujednolicony Dostawca SSP (unifiedssp)
  - Dodano kontrolkę nie wylogowuj mnie (KMSI)

### <a name="100"></a>1.0.0

- Wersja początkowa

## <a name="next-steps"></a>Następne kroki

Więcej informacji o sposobach dostosowywania interfejsu użytkownika aplikacji można znaleźć w temacie [Dostosowywanie interfejsu użytkownika aplikacji przy użyciu zasad niestandardowych w Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).
