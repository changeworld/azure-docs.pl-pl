---
title: Włącz listę Microsoft AppSource i portalu Azure Marketplace przy użyciu usługi Azure Active Directory | Azure
description: Włącz typ oferty przy użyciu usługi Azure Active Directory w portalu Azure Marketplace i AppSource dla wydawcy aplikacji i usługi.
services: Azure, AppSource, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: qianw211
manager: pabutler
ms.service: marketplace
ms.topic: article
ms.date: 09/12/2018
ms.author: qianw211
ms.openlocfilehash: 981d9427dc8407c7300f01c941e39ba9723d53a8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938082"
---
# <a name="enable-an-appsource-and-marketplace-listing-by-using-azure-active-directory"></a>Włączanie możliwości publikowania ofert w witrynach AppSource i Marketplace z użyciem usługi Azure Active Directory

 Azure Active Directory (Azure AD) to chmurze Usługa zarządzania tożsamościami, która umożliwia uwierzytelnianie za pomocą konta Microsoft. Usługa Azure AD używa standardowych środowisk. [Dowiedz się więcej o usłudze Azure Active Directory](https://azure.microsoft.com/services/active-directory).

## <a name="azure-ad-benefits"></a>Korzyści z platformy Azure AD

Microsoft AppSource i portalu Azure Marketplace klienci Użyj środowiska w ramach produktu, aby wyszukać katalogi listy. Te akcje wymaga od klientów do logowania się na produkt. Integracja z usługą Azure AD zapewnia następujące korzyści:

- Szybsze zaangażowania i zoptymalizowane komfort
- Logowanie jednokrotne (SSO) dla milionów użytkowników w przedsiębiorstwach
- Spójne środowisko logowania w aplikacjach opublikowanych przez różnych partnerów
- Skalowalne, Międzyplatformowe uwierzytelnianie dla urządzeń przenośnych i aplikacji w chmurze

## <a name="offers-that-require-azure-ad"></a>Oferty, które wymagają usługi Azure AD

Różne [opcje listowania ilości pamięci i stanowią typy](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) dla usługi AppSource i portalu Azure Marketplace, mają różne wymagania dotyczące wdrożenia usługi Azure AD. Zobacz poniższą tabelę, aby uzyskać szczegółowe informacje:

| **Typ oferty**    | **Wymagane logowania jednokrotnego usługi Azure AD?**  |  |   |  |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
|  | Kontakt ze mną | Wersja próbna | Wersja testowa | Transakcja |
| Maszyna wirtualna | ND | Nie | Nie | Nie |
| Aplikacje platformy Azure (szablon rozwiązania)  | ND | ND | ND | ND |
| Aplikacje zarządzane  | ND | ND | ND | Nie |
| SaaS  | Nie | Yes | Yes | Tak |
| Containers  | ND | ND | ND | Nie |
| Usługi doradcze  | Nie | ND | ND | ND |

Aby uzyskać więcej informacji na temat wymagań technicznych SaaS, zobacz [aplikacji SaaS oferują Podręcznik publikowania](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide).

## <a name="azure-ad-integration"></a>Integracja z usługą Azure AD

- Aby uzyskać informacje o sposobie włączania logowania jednokrotnego przez Integrowanie usługi Azure AD z listy, zobacz [usługi Azure Active Directory dla deweloperów]( https://aka.ms/aaddev).
- Aby uzyskać szczegółowe informacje o usłudze Azure AD logowanie jednokrotne, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="enable-a-trial-listing"></a>Włącz listę wersji próbnej

Ustawienia automatycznego odbiorcy mogą zwiększyć prawdopodobieństwo konwersji. Gdy klient wybiera Twojej listy wersji próbnej i jest przekierowywany do środowiska próbnego, możesz skonfigurować klienta bezpośrednio, bez konieczności dodatkowe kroki logowania.

Podczas uwierzytelniania usługi Azure AD wysyła token do aplikacji lub oferty. Informacje o użytkowniku, dostarczone przez token umożliwia tworzenie konta użytkownika w aplikacji lub oferty. Aby dowiedzieć się więcej, zobacz [przykładowy tokenów](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

Kiedy używać usługi Azure AD w celu umożliwienia uwierzytelniania jednym kliknięciem w swojej aplikacji lub listę wersji próbnej, możesz:

- Usprawnij komfort z witryny Marketplace do Twojej listy wersji próbnej.
- Użytkownik jest przekierowywany z witryny Marketplace do środowiska domeny lub wersja próbna Obsługa nawet działania środowiska w ramach produktu.
- Gdy użytkownicy są przekierowywani, ponieważ istnieją żadne dodatkowe kroki logowania, należy zmniejszyć prawdopodobieństwo porzucania.
- Zmniejsz bariery wdrożenia dla dużych populacji użytkowników usługi Azure AD.

## <a name="verify-azure-ad-integration"></a>Sprawdź integracji z usługą Azure AD

### <a name="multitenant-solutions"></a>Wielodostępne rozwiązania

Użyj usługi Azure AD, aby wesprzeć następujące czynności:

- Rejestrowanie aplikacji w jednym z witryny sklepów. Widok [rejestracji aplikacji](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) lub [certyfikacja usługi AppSource](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified) Aby uzyskać więcej informacji.
- Włącz funkcję obsługi wielodostępności w usłudze Azure AD, aby korzystać z jednym kliknięciem wersji próbnej.

Jeśli jesteś nowym użytkownikiem przy użyciu usługi Azure AD federacyjnego logowania jednokrotnego, wykonaj następujące czynności:

1. Zarejestruj swoją aplikację w portalu Marketplace.
1. Tworzenie logowanie Jednokrotne z usługą Azure AD przy użyciu [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) lub [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).
1. Włącz funkcję obsługi wielodostępności w usłudze Azure AD, aby zapewnić środowisko próbne jednym kliknięciem.

### <a name="single-tenant-solutions"></a>Rozwiązania z jedną dzierżawą

Używaj usługi Azure AD do działu pomocy technicznej jedną z następujących czynności:

- Dodawanie użytkowników-gości do katalogu przy użyciu [usługi Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
- Ręczne konfigurowanie prób dla klientów przy użyciu **skontaktuj się ze mną** opcji publikowania.
- Tworzenie wersji testowej na klienta.
- Tworzenie wielodostępnych przykładową aplikację wersji demonstracyjnej, która korzysta z logowania jednokrotnego.

## <a name="next-steps"></a>Kolejne kroki

- Upewnij się, że masz [zarejestrowany w witrynie Azure Marketplace](https://azuremarketplace.microsoft.com/sell).
- Zaloguj się do [portalu Cloud Partner](https://cloudpartner.azure.com/) do utworzenia lub zakończyć oferty.
