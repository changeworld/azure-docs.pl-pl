---
title: Przekierowywanie ograniczeń i ograniczeń adresów URL w celu przekierowania
description: Ograniczenia adresów URL odpowiedzi/adresów URL przekierowania & ograniczenia
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 06/29/2019
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9cc6ab0342682bce7befdfe412221ec581312be
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389593"
---
# <a name="redirect-urireply-url-restrictions-and-limitations"></a>Identyfikator URI przekierowania lub adres URL odpowiedzi i ograniczenia

Identyfikator URI przekierowania lub adres URL odpowiedzi to lokalizacja, do której serwer autoryzacji wyśle użytkownika, gdy aplikacja zostanie pomyślnie autoryzowana, a następnie zostanie udzielony kod autoryzacji lub token dostępu. Kod lub token jest zawarty w identyfikatorze URI przekierowania lub token odpowiedzi, dlatego ważne jest, aby zarejestrować poprawną lokalizację w ramach procesu rejestracji aplikacji.

## <a name="maximum-number-of-redirect-uris"></a>Maksymalna liczba identyfikatorów URI przekierowania

W poniższej tabeli przedstawiono maksymalną liczbę identyfikatorów URI przekierowania, które można dodać podczas rejestrowania aplikacji.

| Konta, które są zalogowane | Maksymalna liczba identyfikatorów URI przekierowania | Opis |
|--------------------------|---------------------------------|-------------|
| Konta służbowe firmy Microsoft w ramach dzierżawy Azure Active Directory (Azure AD) w organizacji | 256 | pole `signInAudience` w manifeście aplikacji ma ustawioną wartość *AzureADMyOrg* lub *AzureADMultipleOrgs* |
| Osobiste konta Microsoft i służbowe konta służbowe | 100 | pole `signInAudience` w manifeście aplikacji jest ustawione na *AzureADandPersonalMicrosoftAccount* |

## <a name="maximum-uri-length"></a>Maksymalna długość identyfikatora URI

Dla każdego identyfikatora URI przekierowania, który można dodać do rejestracji aplikacji, można użyć maksymalnie 256 znaków.

## <a name="supported-schemes"></a>Obsługiwane schematy
Model aplikacji usługi Azure AD już dziś obsługuje zarówno schematy HTTP, jak i HTTPS dla aplikacji, które logują się na kontach służbowych firmy Microsoft w ramach dzierżawy Azure Active Directory (Azure AD) w organizacji. @No__t-0 pole w manifeście aplikacji ma ustawioną wartość *AzureADMyOrg* lub *AzureADMultipleOrgs*. W przypadku aplikacji, które logują się do osobistych kont Microsoft i kont służbowych (`signInAudience` ustawionych do *AzureADandPersonalMicrosoftAccount*) dozwolony jest tylko schemat https.

> [!NOTE]
> Nowe środowisko [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) nie pozwala deweloperom dodawać identyfikatorów URI z schematem http w interfejsie użytkownika. Dodawanie identyfikatorów URI protokołu HTTP dla aplikacji, które logują się do konta służbowego, jest obsługiwane tylko za pośrednictwem Edytora manifestu aplikacji. W przód nowe aplikacje nie będą mogły używać schematów HTTP w identyfikatorze URI przekierowania. Jednak starsze aplikacje zawierające schematy HTTP w identyfikatorach URI przekierowania będą nadal działały. Deweloperzy muszą używać schematów HTTPS w identyfikatorach URI przekierowania.

## <a name="restrictions-using-a-wildcard-in-uris"></a>Ograniczenia przy użyciu symboli wieloznacznych w identyfikatorach URI

Symbole wieloznaczne, takie jak `https://*.contoso.com`, są wygodne, ale należy je unikać. Używanie symboli wieloznacznych w identyfikatorze URI przekierowania ma wpływ na zabezpieczenia. Zgodnie ze specyfikacją OAuth 2,0 ([sekcja 3.1.2 specyfikacji RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)), identyfikator URI punktu końcowego przekierowania musi być bezwzględnym identyfikatorem URI. 

Model aplikacji usługi Azure AD nie obsługuje wieloznacznych identyfikatorów URI dla aplikacji skonfigurowanych do logowania osobistych kont Microsoft i kont służbowych. Jednak symbole wieloznaczne są dozwolone dla aplikacji, które są skonfigurowane do logowania konta służbowego w ramach dzierżawy usługi Azure AD w organizacji już dziś. 
 
> [!NOTE]
> Nowe środowisko [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) nie pozwala deweloperom dodawać symboli wieloznacznych URI w interfejsie użytkownika. Dodawanie identyfikatora URI symbol wieloznaczny dla aplikacji, które logują się do konta służbowego, jest obsługiwane tylko za pomocą edytora manifestu aplikacji. W przód nowe aplikacje nie będą mogły używać symboli wieloznacznych w identyfikatorze URI przekierowania. Jednak starsze aplikacje zawierające symbole wieloznaczne w identyfikatorach URI przekierowania nadal będą działały.

Jeśli scenariusz wymaga więcej identyfikatorów URI przekierowania niż maksymalny dozwolony limit, zamiast dodawać wieloznaczny identyfikator URI przekierowania, należy wziąć pod uwagę jedną z poniższych metod.

### <a name="use-a-state-parameter"></a>Użyj parametru stanu

Jeśli masz wiele domen podrzędnych, a jeśli w Twoim scenariuszu użytkownik wymaga przekierowania użytkowników po pomyślnym uwierzytelnieniu na tej samej stronie, na której zostały uruchomione, może być przydatne użycie parametru stanu. 

W tym podejściu:

1. Utwórz "współużytkowany" identyfikator URI przekierowania dla aplikacji, aby przetworzyć tokeny zabezpieczające otrzymane z punktu końcowego autoryzacji.
1. Aplikacja może wysyłać parametry specyficzne dla aplikacji (takie jak adres URL domeny podrzędnej, w którym użytkownik wychodził lub coś, jak informacje o znakowaniu) w parametrze State. W przypadku korzystania z parametru stanu Ochrona przed CSRF ochroną zgodnie z opisem w [sekcji 10,12 specyfikacji RFC 6749](https://tools.ietf.org/html/rfc6749#section-10.12)). 
1. Parametry specyficzne dla aplikacji będą zawierać wszystkie informacje potrzebne do przetworzenia przez aplikację poprawnego środowiska dla użytkownika, czyli konstruowania odpowiedniego stanu aplikacji. Punkt końcowy autoryzacji usługi Azure AD przydzieli kod HTML z parametru State, aby upewnić się, że nie przekazujesz zawartości HTML w tym parametrze.
1. Gdy usługa Azure AD wysyła odpowiedź do "udostępnionego" identyfikatora URI przekierowania, wyśle parametr stanu z powrotem do aplikacji.
1. Aplikacja może następnie użyć wartości w parametrze State, aby określić adres URL, do którego ma być wysyłany użytkownik. Upewnij się, że jest sprawdzana poprawność ochrony CSRF.

> [!NOTE]
> Takie podejście pozwala naruszonemu klientowi zmodyfikować dodatkowe parametry wysłane w parametrze State, w związku z tym przekierowując użytkownika do innego adresu URL, który jest [otwartym zagrożeniem](https://tools.ietf.org/html/rfc6819#section-4.2.4) w dokumencie RFC 6819. W związku z tym, klient musi chronić te parametry przez szyfrowanie stanu lub Weryfikowanie go przy użyciu innych metod, takich jak Walidacja nazwy domeny w identyfikatorze URI przekierowania względem tokenu.

### <a name="add-redirect-uris-to-service-principals"></a>Dodawanie identyfikatorów URI przekierowania do jednostek usługi

Innym podejściem jest dodanie identyfikatorów URI przekierowania do jednostek [usługi](app-objects-and-service-principals.md#application-and-service-principal-relationship) , które reprezentują rejestrację aplikacji w dowolnej dzierżawie usługi Azure AD. Tego podejścia można użyć, jeśli nie można użyć parametru stanu lub twój scenariusz wymaga dodania nowych identyfikatorów URI przekierowania do rejestracji aplikacji dla każdej nowej dzierżawy, którą obsługujesz. 

## <a name="next-steps"></a>Następne kroki

- Informacje o [manifeście aplikacji](reference-app-manifest.md)
