---
title: Różnice sprawdzania poprawności według obsługiwanych typów kont — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się więcej o różnicach sprawdzania poprawności różnych właściwości dla różnych obsługiwanych typów kont podczas rejestrowania aplikacji na platformie tożsamości firmy Microsoft.
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 10/12/2019
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: 2a1507b008903085886f9392f3f4e5461997b6e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128858"
---
# <a name="validation-differences-by-supported-account-types-signinaudience"></a>Różnice sprawdzania poprawności według obsługiwanych typów kont (signInAudience)

Podczas rejestrowania aplikacji za pomocą platformy tożsamości firmy Microsoft dla deweloperów, użytkownik jest proszony o wybranie typów kont, które obsługuje aplikacja. W obiekcie aplikacji i manifestu ta właściwość jest `signInAudience`.

Dostępne są następujące opcje:

- *AzureADMyOrg*: Tylko konta w katalogu organizacyjnym, w którym aplikacja jest zarejestrowana (pojedyncza dzierżawa)
- *AzureADMultipleOrgs*: Konta w dowolnym katalogu organizacyjnym (wielodostępne)
- *AzureADandPersonalMicrosoftAccount*: Konta w dowolnym katalogu organizacyjnym (wielodostępne) i osobiste konta Microsoft (na przykład Skype, Xbox i Outlook.com)

W przypadku zarejestrowanych aplikacji wartość obsługiwanych typów kont można znaleźć w sekcji **Uwierzytelnianie** aplikacji. Można go również znaleźć `signInAudience` pod obiektem w **Manifeście.**

Wartość wybrana dla tej właściwości ma wpływ na inne właściwości obiektu aplikacji. W rezultacie, jeśli zmienisz tę właściwość, może być konieczna zmiana innych właściwości.

Zobacz poniższą tabelę różnice sprawdzania poprawności różnych właściwości dla różnych typów obsługiwanych kont.

| Właściwość | `AzureADMyOrg` | `AzureADMultipleOrgs` | `AzureADandPersonalMicrosoftAccount` i `PersonalMicrosoftAccount` |
|--------------|---------------|----------------|----------------|
| Identyfikator URI aplikacji`identifierURIs`( )  | Musi być unikatowa w dzierżawie <br><br> urn:// systemy są wspierane <br><br> Symbole wieloznaczne nie są obsługiwane <br><br> Obsługiwane są ciągi zapytań i fragmenty <br><br> Maksymalna długość 255 znaków <br><br> Brak limitu* liczby identyfikatorówURI  | Musi ona być unikatowa w skali globalnej <br><br> urn:// systemy są wspierane <br><br> Symbole wieloznaczne nie są obsługiwane <br><br> Obsługiwane są ciągi zapytań i fragmenty <br><br> Maksymalna długość 255 znaków <br><br> Brak limitu* liczby identyfikatorówURI | Musi ona być unikatowa w skali globalnej <br><br> urn:// nie są obsługiwane <br><br> Symbole wieloznaczne, fragmenty i ciągi zapytań nie są obsługiwane <br><br> Maksymalna długość 120 znaków <br><br> Maksymalnie 50 identyfikatorów IDENTYFIKATORY |
| Certyfikaty`keyCredentials`( ) | Klucz podpisywania symetrycznego | Klucz podpisywania symetrycznego | Klucz szyfrowania i podpisywania asymetrycznego | 
| Tajemnice`passwordCredentials`klienta ( ) | Bez limitu* | Bez limitu* | Jeśli liveSDK jest włączona: maksymalnie 2 wpisy tajne klienta | 
| Przekierowanie identyfikatorów`replyURLs`URI ( ) | Aby uzyskać więcej informacji, zobacz [Przekierowywanie ograniczeń i ograniczeń adresów URL identyfikatorów URI/odpowiedzi.](reply-url.md) | | | 
| Uprawnienia interfejsu`requiredResourceAccess`API ( ) | Bez limitu* | Bez limitu* | Maksymalnie 30 uprawnień na dozwolony zasób (np. | 
| Zakresy zdefiniowane przez`oauth2Permissions`ten interfejs API ( ) | Maksymalna długość nazwy zakresu 120 znaków <br><br> Brak limitu* liczby zdefiniowanych zakresów | Maksymalna długość nazwy zakresu 120 znaków <br><br> Brak limitu* liczby zdefiniowanych zakresów |  Maksymalna długość nazwy zakresu 40 znaków <br><br> Maksymalnie 100 zdefiniowanych zakresów | 
| Autoryzowane aplikacje`preautorizedApplications`klienckie ( ) | Bez limitu* | Bez limitu* | Całkowita maksymalna liczba 500 <br><br> Maksymalnie 100 zdefiniowanych aplikacji klienckich <br><br> Maksymalnie 30 zakresów zdefiniowanych na klienta | 
| AppRoles (Zaułki) | Obsługiwane <br> Bez limitu* | Obsługiwane <br> Bez limitu* | Nieobsługiwane | 
| Adres URL wylogowywania | http://localhostjest dozwolone <br><br> Maksymalna długość 255 znaków | http://localhostjest dozwolone <br><br> Maksymalna długość 255 znaków | <br><br> https://localhostjest dozwolone, http://localhost nie powiedzie się dla MSA <br><br> Maksymalna długość 255 znaków <br><br> Schemat HTTP jest niedozwolony <br><br> Symbole wieloznaczne nie są obsługiwane | 

* Istnieje globalny limit około 1000 elementów we wszystkich właściwościach kolekcji w obiekcie aplikacji

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [rejestracji aplikacji](app-objects-and-service-principals.md)
- Dowiedz się więcej o [manifeście aplikacji](reference-app-manifest.md)
