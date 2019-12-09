---
title: Różnice dotyczące walidacji według typu konta — Microsoft Identity platform | Azure
description: Zapoznaj się z różnicami w walidacji różnych właściwości dla różnych obsługiwanych typów kont podczas rejestrowania aplikacji za pomocą platformy tożsamości firmy Microsoft.
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 10/12/2019
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 576adc99ef7d794f50efeb61375f3e59f8815033
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919362"
---
# <a name="validation-differences-by-supported-account-types-signinaudience"></a>Różnice dotyczące walidacji według obsługiwanych typów kont (signInAudience)

Podczas rejestrowania aplikacji za pomocą platformy tożsamości firmy Microsoft dla deweloperów zostanie wyświetlony monit o wybranie typów kont obsługiwanych przez aplikację. W obiekcie aplikacji i manifeście ta właściwość jest `signInAudience`.

Dostępne są następujące opcje:

- *AzureADMyOrg*: tylko konta w katalogu organizacyjnym, w którym zarejestrowano aplikację (pojedyncza dzierżawa)
- *AzureADMultipleOrgs*: konta w dowolnym katalogu organizacji (wiele dzierżawców)
- *AzureADandPersonalMicrosoftAccount*: konta w dowolnym katalogu organizacji (z wieloma dzierżawcami) i osobistymi kontami Microsoft (na przykład Skype, Xbox i Outlook.com)

W przypadku zarejestrowanych aplikacji można znaleźć wartość obsługiwanych typów kont w sekcji **uwierzytelnianie** aplikacji. Można go również znaleźć pod właściwością `signInAudience` w **manifeście**.

Wartość wybrana dla tej właściwości ma wpływ na inne właściwości obiektu aplikacji. W związku z tym, jeśli zmienisz tę właściwość, może być konieczne najpierw zmienić inne właściwości.

Zapoznaj się z poniższą tabelą, aby poznać różnice między walidacją różnych właściwości dla różnych obsługiwanych typów kont.

| Właściwość | `AzureADMyOrg` | `AzureADMultipleOrgs`  | `AzureADandPersonalMicrosoftAccount` |
|--------------|---------------|----------------|----------------|
| Identyfikator URI identyfikatora aplikacji (`identifierURIs`)  | Musi być unikatowa w dzierżawie <br><br> Obsługiwane są schematy urn:// <br><br> Symbole wieloznaczne nie są obsługiwane <br><br> Obsługiwane są ciągi i fragmenty zapytań <br><br> Maksymalna długość 255 znaków <br><br> Brak limitu * na liczbie identifierURIs  | Musi być globalnie unikatowa <br><br> Obsługiwane są schematy urn:// <br><br> Symbole wieloznaczne nie są obsługiwane <br><br> Obsługiwane są ciągi i fragmenty zapytań <br><br> Maksymalna długość 255 znaków <br><br> Brak limitu * na liczbie identifierURIs | Musi być globalnie unikatowa <br><br> schematy urn://nie są obsługiwane <br><br> Symbole wieloznaczne, fragmenty i ciągi zapytań nie są obsługiwane <br><br> Maksymalna długość 120 znaków <br><br> Maksymalnie 50 identifierURIs |
| Certyfikaty (`keyCredentials`) | Symetryczny klucz podpisywania | Symetryczny klucz podpisywania | Szyfrowanie i asymetryczne klucze podpisywania | 
| Wpisy tajne klienta (`passwordCredentials`) | Bez limitu * | Bez limitu * | Jeśli liveSDK jest włączona: maksymalnie 2 wpisy tajne klienta | 
| Identyfikatory URI przekierowania (`replyURLs`) | Aby uzyskać więcej informacji [, zobacz ograniczenia i ograniczenia adresów URL przekierowania URI/odpowiedzi](reply-url.md) . | | | 
| Uprawnienia interfejsu API (`requiredResourceAccess`) | Bez limitu * | Bez limitu * | Dozwolone maksimum 30 uprawnień na zasób (np. Microsoft Graph) | 
| Zakresy zdefiniowane przez ten interfejs API (`oauth2Permissions`) | Maksymalna długość nazwy zakresu wynosząca 120 znaków <br><br> Brak limitu * na określonej liczbie zakresów | Maksymalna długość nazwy zakresu wynosząca 120 znaków <br><br> Brak limitu * na określonej liczbie zakresów |  Maksymalna długość nazwy zakresu wynosząca 40 znaków <br><br> Zdefiniowano maksymalnie 100 zakresów | 
| Autoryzowane aplikacje klienckie (`preautorizedApplications`) | Bez limitu * | Bez limitu * | Suma maksymalnie 500 <br><br> Zdefiniowano maksymalnie 100 aplikacji klienckich <br><br> Zdefiniowano maksymalnie 30 zakresów na klienta | 
| appRoles | Obsługiwane <br> Bez limitu * | Obsługiwane <br> Bez limitu * | Brak obsługi | 
| Adres URL wylogowywania | http://localhost jest dozwolony <br><br> Maksymalna długość 255 znaków | http://localhost jest dozwolony <br><br> Maksymalna długość 255 znaków | <br><br> https://localhost jest dozwolony, http://localhost niepowodzenie dla elementu MSA <br><br> Maksymalna długość 255 znaków <br><br> Schemat HTTP jest niedozwolony <br><br> Symbole wieloznaczne nie są obsługiwane | 

\* Istnieje globalny limit dotyczący około 1000 elementów we wszystkich właściwościach kolekcji w obiekcie App

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [rejestrowaniu aplikacji](app-objects-and-service-principals.md)
- Informacje o [manifeście aplikacji](reference-app-manifest.md)
