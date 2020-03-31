---
title: Przekierowywanie ograniczeń adresów URL identyfikatora URI & odpowiedzi - Platforma tożsamości firmy Microsoft | Azure
description: Odpowiedz na adresy URL/przekieruj ograniczenia URls & ograniczenia
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 06/29/2019
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: 8fdc64632be8b5fcb3dca8de2ee833fef25719fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77656742"
---
# <a name="redirect-urireply-url-restrictions-and-limitations"></a>Identyfikator URI przekierowania lub adres URL odpowiedzi i ograniczenia

Przekierowanie identyfikatora URI lub adres URL odpowiedzi to lokalizacja, do której serwer autoryzacji wyśle użytkownika po pomyślnym autoryzowaniu aplikacji i udzieliła kodu autoryzacji lub tokenu dostępu. Kod lub token znajduje się w identyfikatorze URI przekierowania lub tokenu odpowiedzi, dlatego ważne jest, aby zarejestrować poprawną lokalizację w ramach procesu rejestracji aplikacji.

 Do odpowiedzi na adresy URL obowiązują następujące ograniczenia:

    * Adres URL odpowiedzi musi `https`zaczynać się od schematu .
    * Adres URL odpowiedzi jest rozróżniana wielkość liter. Jego przypadek musi być zgodny ze ścieżką adresu URL uruchomionej aplikacji. Na przykład jeśli aplikacja zawiera jako `.../abc/response-oidc`część swojej `.../ABC/response-oidc` ścieżki, nie należy określać w adresie URL odpowiedzi. Ponieważ przeglądarka internetowa traktuje ścieżki jako rozróżniane `.../abc/response-oidc` wielkość liter, pliki cookie skojarzone z `.../ABC/response-oidc` mogą zostać wykluczone, jeśli zostaną przekierowane do adresu URL niezgodnego z wielkością sprawy.
    
## <a name="maximum-number-of-redirect-uris"></a>Maksymalna liczba URI przekierowania

W poniższej tabeli przedstawiono maksymalną liczbę identyfikatorów URI przekierowania, które można dodać podczas rejestracji aplikacji.

| Konta są logowane | Maksymalna liczba URI przekierowania | Opis |
|--------------------------|---------------------------------|-------------|
| Konta służbowe firmy Microsoft w dzierżawie usługi Azure Active Directory (Azure AD) dowolnej organizacji | 256 | `signInAudience`pole w manifeście aplikacji jest ustawione na *AzureADMyOrg* lub *AzureADMultipleOrgs* |
| Osobiste konta Microsoft oraz konta służbowe i szkolne | 100 | `signInAudience`pole w manifeście aplikacji jest ustawione na *AzureADandPersonalMicrosoftAccount* |

## <a name="maximum-uri-length"></a>Maksymalna długość identyfikatora URI

Dla każdego identyfikatora URI przekierowania, który zostanie dodawany do rejestracji aplikacji, można użyć maksymalnie 256 znaków.

## <a name="supported-schemes"></a>Obsługiwane systemy
Model aplikacji usługi Azure AD obsługuje obecnie schematy HTTP i HTTPS dla aplikacji, które logują się na kontach służbowych firmy Microsoft w dzierżawie usługi Azure Active Directory (Azure AD) dowolnej organizacji. To `signInAudience` pole w manifeście aplikacji jest ustawione na *AzureADMyOrg* lub *AzureADMultipleOrgs*. W przypadku aplikacji, które logują się w osobistych kontach Microsoft oraz kontach służbowych i szkolnych (które są `signInAudience` ustawione na *AzureADandPersonalMicrosoftAccount)* dozwolony jest tylko schemat HTTPS.

> [!NOTE]
> Nowe środowisko rejestracji aplikacji nie zezwala deweloperom na dodawanie [identyfikatorów](https://go.microsoft.com/fwlink/?linkid=2083908) URI ze schematem HTTP w interfejsie użytkownika. Dodawanie identyfikatorów URI HTTP dla aplikacji, które logują się na kontach służbowych lub szkolnych, jest obsługiwane tylko za pośrednictwem edytora manifestów aplikacji. W przyszłości nowe aplikacje nie będą mogły używać schematów HTTP w identyfikatorze URI przekierowania. Jednak starsze aplikacje, które zawierają schematy HTTP w przekierowaniu identyfikatorów URI będzie nadal działać. Deweloperzy muszą używać schematów HTTPS w URI przekierowania.

## <a name="restrictions-using-a-wildcard-in-uris"></a>Ograniczenia dotyczące używania symbolu wieloznacznego w identyfikatorach URI

Identyfikatory URI z `https://*.contoso.com`symbolami wieloznaczną, takie jak , są wygodne, ale należy ich unikać. Używanie symboli wieloznacznych w identyfikatorze URI przekierowania ma wpływ na bezpieczeństwo. Zgodnie ze specyfikacją OAuth 2.0[(sekcja 3.1.2 RFC 6749),](https://tools.ietf.org/html/rfc6749#section-3.1.2)identyfikator URI punktu końcowego przekierowania musi być bezwzględnym identyfikatorem URI. 

Model aplikacji usługi Azure AD nie obsługuje identyfikatorów URI z symbolami wieloznacznych dla aplikacji skonfigurowanych do logowania się na osobistych kontach Microsoft i kontach służbowych lub szkolnych. Jednak identyfikatory URI z symbolami wieloznaczymi są obecnie dozwolone dla aplikacji skonfigurowanych do logowania się na kontach służbowych w dzierżawie usługi Azure AD w organizacji. 
 
> [!NOTE]
> Nowe środowisko rejestracji aplikacji nie zezwala deweloperom na dodawanie [identyfikatorów](https://go.microsoft.com/fwlink/?linkid=2083908) URI z symbolami wieloznacznych w interfejsie użytkownika. Dodawanie identyfikatora URI wilcard dla aplikacji, które logują się na kontach służbowych lub szkolnych, jest obsługiwane tylko za pośrednictwem edytora manifestów aplikacji. W przyszłości nowe aplikacje nie będą mogły używać symboli wieloznacznych w identyfikatorze URI przekierowania. Jednak starsze aplikacje, które zawierają symbole wieloznaczne w przekierowaniu identyfikatorów URI będzie nadal działać.

Jeśli scenariusz wymaga więcej identyfikatorów URI przekierowania niż maksymalny dozwolony limit, zamiast dodawać symbole wieloznaczne przekierowania URI, należy wziąć pod uwagę następujące podejście.

### <a name="use-a-state-parameter"></a>Używanie parametru stanu

Jeśli masz wiele poddomen i jeśli scenariusz wymaga przekierowania użytkowników po pomyślnym uwierzytelnieniu do tej samej strony, na której rozpoczęto, przy użyciu parametru stanu może być pomocne. 

W tym podejściu:

1. Utwórz "udostępniony" przekierowanie identyfikatora URI dla aplikacji, aby przetworzyć tokeny zabezpieczające otrzymane z punktu końcowego autoryzacji.
1. Aplikacja może wysyłać parametry specyficzne dla aplikacji (takie jak adres URL poddomeny, z którego pochodzi użytkownik lub coś podobnego do informacji o znakowaniu) w parametrze stanu. W przypadku korzystania z parametru stanu należy chronić przed ochroną CSRF, jak określono w [sekcji 10.12 RFC 6749).](https://tools.ietf.org/html/rfc6749#section-10.12) 
1. Parametry specyficzne dla aplikacji będą zawierać wszystkie informacje potrzebne do renderowania przez aplikację poprawnego środowiska dla użytkownika, czyli konstruowania odpowiedniego stanu aplikacji. Punkt końcowy autoryzacji usługi Azure AD usuwa kod HTML z parametru stanu, więc upewnij się, że nie przekazujesz zawartości HTML w tym parametrze.
1. Gdy usługa Azure AD wysyła odpowiedź do "udostępnionego" przekierowania URI, wyśle parametr stanu z powrotem do aplikacji.
1. Aplikacja może następnie użyć wartości w parametrze state, aby określić, który adres URL do dalszego wysyłania użytkownika. Upewnij się, że masz poprawność dla ochrony CSRF.

> [!NOTE]
> Takie podejście umożliwia klienta zagrożone zmodyfikować dodatkowe parametry wysyłane w parametrze stanu, przekierowując w ten sposób użytkownika do innego adresu URL, który jest [zagrożeniem open readresator](https://tools.ietf.org/html/rfc6819#section-4.2.4) opisane w RFC 6819. W związku z tym klient musi chronić te parametry, szyfrując stan lub weryfikując go za pomocą innych środków, takich jak sprawdzanie poprawności nazwy domeny w identyfikatorze URI przekierowania względem tokenu.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [manifeście aplikacji](reference-app-manifest.md)
