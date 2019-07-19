---
title: Włącz Microsoft AppSource i aukcję portalu Azure Marketplace, używając Azure Active Directory | Azure
description: Włącz typ listy przy użyciu Azure Active Directory w portalu Azure Marketplace i AppSource dla wydawców aplikacji i usług.
services: Azure, AppSource, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: qianw211
manager: pabutler
ms.service: marketplace
ms.topic: article
ms.date: 09/12/2018
ms.author: pabutler
ms.openlocfilehash: 0b68687a2bbaa817f2776757ccab9571638c0fd5
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875999"
---
# <a name="enable-an-appsource-and-marketplace-listing-by-using-azure-active-directory"></a>Włączanie możliwości publikowania ofert w witrynach AppSource i Marketplace z użyciem usługi Azure Active Directory

 Azure Active Directory (Azure AD) to usługa tożsamości w chmurze, która umożliwia uwierzytelnianie przy użyciu konto Microsoft. Usługa Azure AD używa standardowych struktur branżowych. [Dowiedz się więcej o Azure Active Directory](https://azure.microsoft.com/services/active-directory).

## <a name="azure-ad-benefits"></a>Korzyści z usługi Azure AD

Klienci korzystający z usług Microsoft AppSource i Azure Marketplace wykorzystują środowiska w ramach produktu, aby przeszukiwać katalogi list. Te akcje wymagają od klientów zalogowania się do produktu. Integracja z usługą Azure AD zapewnia następujące korzyści:

- Szybsza zaangażowanie i zoptymalizowane środowisko klienta
- Logowanie jednokrotne (SSO) dla milionów użytkowników w przedsiębiorstwach
- Spójne środowisko logowania między aplikacjami opublikowanymi przez różnych partnerów
- Skalowalne, międzyplatformowe uwierzytelnianie dla aplikacji mobilnych i w chmurze

## <a name="offers-that-require-azure-ad"></a>Oferty wymagające usługi Azure AD

Różne [Opcje i typy ofert](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) dla usług AppSource i Azure Marketplace mają różne wymagania dotyczące implementacji usługi Azure AD. Szczegółowe informacje znajdują się w poniższej tabeli:

| **Typ oferty**    | **Wymagany jest logowanie jednokrotne usługi Azure AD?**  |  |   |  |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
|  | Skontaktuj się z nami | Wersja próbna | Wersja testowa | Transakcja |
| Maszyna wirtualna | ND | Nie | Nie | Nie |
| Aplikacje platformy Azure (szablon rozwiązania)  | ND | ND | ND | ND |
| Aplikacje zarządzane  | ND | ND | ND | Nie |
| SaaS  | Nie | Yes | Yes | Yes |
| Containers  | ND | ND | ND | Nie |
| Usługi doradcze  | Nie | ND | ND | ND |

Więcej informacji o wymaganiach technicznych SaaS można znaleźć w temacie [SaaS Applications oferują Podręcznik publikowania](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide).

## <a name="azure-ad-integration"></a>Integracja z usługą Azure AD

- Aby uzyskać informacje na temat włączania logowania jednokrotnego przez integrację usługi Azure AD z listą, zobacz [Azure Active Directory dla deweloperów]( https://aka.ms/aaddev).
- Aby uzyskać szczegółowe informacje na temat logowania jednokrotnego w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="enable-a-trial-listing"></a>Włącz listę próbną

Zautomatyzowana konfiguracja klienta może zwiększyć prawdopodobieństwo konwersji. Gdy klient wybierze swoją listę próbną i zostanie przekierowany do środowiska w wersji próbnej, można skonfigurować klienta bezpośrednio bez konieczności wykonywania dodatkowych czynności związanych z logowaniem.

Podczas uwierzytelniania usługa Azure AD wysyła token do aplikacji lub oferty. Informacje o użytkowniku udostępniane przez token umożliwiają utworzenie konta użytkownika w Twojej aplikacji lub ofercie. Aby dowiedzieć się więcej, zobacz [przykładowe tokeny](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

W przypadku korzystania z usługi Azure AD w celu włączenia uwierzytelniania jednym kliknięciem na liście aplikacji lub wersji próbnej:

- Usprawnij pracę klienta z portalu Marketplace na liście próbnej.
- Zapoznaj się z działaniem środowiska w produkcie nawet wtedy, gdy użytkownik jest przekierowywany z portalu Marketplace do środowiska domeny lub wersji próbnej.
- Zmniejszenie prawdopodobieństwa porzucenia w przypadku przekierowania użytkowników z powodu braku dodatkowych kroków związanych z logowaniem.
- Ogranicz bariery wdrożenia dla dużej populacji użytkowników usługi Azure AD.

## <a name="verify-azure-ad-integration"></a>Weryfikuj integrację z usługą Azure AD

### <a name="multitenant-solutions"></a>Rozwiązania z wieloma dzierżawcami

Użyj usługi Azure AD, aby obsłużyć następujące akcje:

- Zarejestruj swoją aplikację w jednym z witryny Marketplace. Aby uzyskać więcej informacji, Wyświetl [rejestrację aplikacji](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) lub [certyfikat AppSource](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified) .
- Włącz funkcję obsługi wielodostępności w usłudze Azure AD, aby skorzystać z wersji próbnej jednego kliknięcia.

Jeśli dopiero zaczynasz korzystać z federacyjnego logowania jednokrotnego usługi Azure AD, wykonaj następujące czynności:

1. Zarejestruj swoją aplikację w portalu Marketplace.
1. Utwórz Logowanie jednokrotne za pomocą usługi Azure AD przy użyciu protokołu [OAuth 2,0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) lub [OpenID Connect Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).
1. Włącz funkcję obsługi wielodostępności w usłudze Azure AD, aby udostępnić wersję próbną jednego kliknięcia.

### <a name="single-tenant-solutions"></a>Rozwiązania z jedną dzierżawą

Użyj usługi Azure AD, aby obsłużyć jedną z następujących akcji:

- Dodawanie użytkowników-Gości do katalogu za pomocą [usługi Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
- Ręcznie skonfiguruj wersje próbne dla klientów za pomocą opcji publikowania do **kontaktów** .
- Tworzenie dysku testowego dla klienta.
- Utwórz przykładową aplikację demonstracyjną obejmującą wiele dzierżawców, która korzysta z logowania jednokrotnego.

## <a name="next-steps"></a>Kolejne kroki

- Upewnij się, że zarejestrowano Cię [w portalu Azure Marketplace](https://azuremarketplace.microsoft.com/sell).
- Zobacz [jak utworzyć konto Centrum partnerskiego](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) , aby uzyskać więcej informacji na temat tworzenia lub uzupełniania oferty.
