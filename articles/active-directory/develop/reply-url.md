---
title: Przekierowywanie adresu URL odpowiedzi/identyfikator URI ogólnych i ograniczeń — platforma tożsamości firmy Microsoft
description: Ograniczenia adresów URL/przekierowywanie adresów URL odpowiedzi i ograniczenia
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 06/29/2019
ms.topic: article
ms.subservice: develop
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07be7d0c70193fec88782fea681e33d6b4cf4b40
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67486235"
---
# <a name="redirect-urireply-url-restrictions-and-limitations"></a>Identyfikator URI przekierowania lub adres URL odpowiedzi i ograniczenia

Przekierowania URI lub adresu URL odpowiedzi, jest uzyskał autoryzację lokalizacji, serwer autoryzacji wyśle użytkownikowi raz aplikacji oraz udzielili kod autoryzacji lub dostęp do tokenu. Kod lub token jest zawarty w przekierowania URI lub odpowiedzi tokenu dlatego należy zarejestrować prawidłową lokalizację w ramach procesu rejestracji aplikacji.

## <a name="maximum-number-of-redirect-uris"></a>Maksymalna liczba identyfikatorów URI przekierowań

W poniższej tabeli przedstawiono maksymalną liczbę przekierowań identyfikatory URI, które można dodać podczas rejestrowania aplikacji. 

| Trwa logowanie konta | Maksymalna liczba identyfikatorów URI przekierowań | Opis |
|--------------------------|---------------------------------|-------------|
| Microsoft konta służbowe w dzierżawie usługi Azure Active Directory (Azure AD) w organizacji | 256 | `signInAudience` pola w manifeście aplikacji jest ustawiona jako *AzureADMyOrg* lub *AzureADMultipleOrgs* |
| Osobiste konta i działają, a także kont służbowych | 100 | `signInAudience` pole w manifeście aplikacji jest ustawione na *AzureADandPersonalMicrosoftAccount* |

## <a name="maximum-uri-length"></a>Maksymalna długość identyfikatora URI

Dla każdego przekierowania URI, który dodasz do rejestracji aplikacji, można użyć maksymalnie 256 znaków.

## <a name="restrictions-using-a-wildcard-in-uris"></a>Ograniczenia przy użyciu symboli wieloznacznych w identyfikatorach URI

Symbol wieloznaczny URI, takich jak `https://*.contoso.com`, jest wygodne, ale należy unikać. Przy użyciu symboli wieloznacznych w przekierowania URI ma skutki dla bezpieczeństwa. Zgodnie ze specyfikacją protokołu OAuth 2.0 ([sekcji 3.1.2 RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)), punktu końcowego przekierowania URI musi być bezwzględnym identyfikatorem URI. 

Model aplikacji usługi Azure AD nie obsługuje symboli wieloznacznych identyfikatorów URI dla aplikacji, które są skonfigurowane, zaloguj się w osobistych kont Microsoft i pracy lub konta służbowego. Jednak symbol wieloznaczny URI są dozwolone w przypadku aplikacji, które są skonfigurowane do podpisywania w pracach kont służbowych w dzierżawie usługi Azure AD w organizacji już dziś. 
 
> [!NOTE]
> Nowy [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) środowisko nie zezwala na deweloperów dodać symbol wieloznaczny URI w interfejsie użytkownika. Dodawanie symbol wieloznaczny URI dla aplikacji, zaloguj się w pracy lub szkołą kont, które jest obsługiwane tylko w edytorze manifestu aplikacji. Idąc dalej, nowych aplikacji nie będzie można używać symboli wieloznacznych w identyfikator URI przekierowania. Jednak starsze aplikacje, które zawierają symbole wieloznaczne w przekierowania URI będzie kontynuować pracę.

Jeśli dany scenariusz wymaga więcej identyfikatory URI przekierowania, niż maksymalny limit dozwolony, zamiast opcji dodawania przekierowanie symbol wieloznaczny URI, należy rozważyć użycie jednej z następujących metod.

### <a name="use-a-state-parameter"></a>Użyj parametru stanu

Jeśli masz wiele domen podrzędnych i danego scenariusza należy przekierowywać użytkowników po pomyślnym uwierzytelnieniu do tej samej strony, na którym uruchomiony, pomocne może być użycie parametru stanu. 

W tym podejściu:

1. Utwórz identyfikator URI przekierowania "udostępniony" dla aplikacji do przetwarzania tokenów zabezpieczających, otrzymanymi od punktu końcowego autoryzacji.
1. Aplikację można wysłać parametry specyficzne dla aplikacji (na przykład gdy użytkownik pochodzi lub nic, takich jak znakowania firmowego adresu URL domeny podrzędnej) w parametrze state. Korzystając z parametru stanu, ochronę przed CSRF ochrony, jak to określono w [sekcji 10.12 RFC 6749](https://tools.ietf.org/html/rfc6749#section-10.12)). 
1. Parametry specyficzne dla aplikacji będzie zawierać wszystkie informacje wymagane do renderowania poprawny aplikacji środowisko użytkownika, to znaczy, utworzenia stanu odpowiedniej aplikacji. Paski punktu końcowego autoryzacji usługi Azure AD HTML w parametrze state więc upewnij się, że nie są używane HTML zawartości w tym parametrze.
1. Gdy usługi Azure AD wysyła odpowiedź do identyfikatora URI przekierowania "udostępnione", parametr state zostanie wysłane do aplikacji.
1. Aplikacji może następnie użyj wartości w parametrze state, aby określić adres URL, który dodatkowo wysyłać użytkownikowi. Upewnij się, że weryfikowanie CSRF ochrony.

> [!NOTE]
> Takie podejście umożliwia klientowi ze złamanymi zabezpieczeniami zmodyfikować dodatkowe parametry, które są wysyłane w parametrze state przekierować użytkownika do innego adresu URL, który jest [Otwórz zagrożeń readresatora](https://tools.ietf.org/html/rfc6819#section-4.2.4) opisanych w dokumencie RFC 6819. W związku z tym klient musi chronić te parametry szyfrowania stan lub zweryfikowaniem jej za pomocą innych środków, takich jak sprawdzanie poprawności nazwy domeny w identyfikatora URI przekierowania względem tokenu.

### <a name="add-redirect-uris-to-service-principals"></a>Dodaj identyfikatory URI przekierowania do jednostki usługi

Innym rozwiązaniem jest dodanie identyfikatorów URI przekierowania [jednostki usług](app-objects-and-service-principals.md#application-and-service-principal-relationship) reprezentujące rejestrację aplikacji w dowolnej dzierżawy usługi Azure AD. Można użyć tego podejścia, gdy nie można użyć parametru stanu lub danego scenariusza należy dodać nowe identyfikatory URI przekierowania do rejestracji aplikacji dla każdej nowej dzierżawy, które obsługujesz. 

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [manifest aplikacji](reference-app-manifest.md)
