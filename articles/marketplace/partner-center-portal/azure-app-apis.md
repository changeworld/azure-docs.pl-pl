---
title: Interfejs API dołączania dla aplikacji platformy Azure w portalu Marketplace komercyjnym
description: Wymagania wstępne interfejsu API dla aplikacji platformy Azure w komercyjnym portalu Marketplace w Centrum partnerów firmy Microsoft.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: cc4d56058ce3985ec3a1d9124ef4ec73ff6be1a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80279763"
---
# <a name="api-for-onboarding-azure-apps-in-partner-center"></a>Interfejs API do dołączania aplikacji platformy Azure w Centrum partnerów

Interfejs *API przesyłania Centrum partnerów* służy do programowego wykonywania zapytań, tworzenia zgłoszeń i publikowania ofert platformy Azure.  Ten interfejs API jest przydatny, jeśli Twoje konto zarządza wieloma ofertami i chcesz zautomatyzować i zoptymalizować proces przesyłania tych ofert.

## <a name="api-prerequisites"></a>Wymagania wstępne interfejsu API

Istnieje kilka zasobów programowych, które są potrzebne do korzystania z interfejsu API Centrum partnerów dla produktów platformy Azure: 

- aplikacji usługi Azure Active Directory.
- token dostępu usługi Azure Active Directory (Azure AD).

### <a name="step-1-complete-prerequisites-for-using-the-partner-center-submission-api"></a>Krok 1: Pełne wymagania wstępne dotyczące korzystania z interfejsu API przesyłania centrum partnerów

Przed rozpoczęciem pisania kodu do wywołania interfejsu API przesyłania Centrum partnerów, upewnij się, że zostały wykonane następujące wymagania wstępne.

- Użytkownik (lub twoja organizacja) musi mieć katalog usługi Azure AD i musi mieć uprawnienia [administratora globalnego](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) dla katalogu. Jeśli korzystasz już z usługi Office 365 lub innych usług biznesowych firmy Microsoft, masz już katalog usługi Azure AD. W przeciwnym razie można [utworzyć nową usługę Azure AD w Centrum partnerów](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center#create-a-brand-new-azure-ad-to-associate-with-your-partner-center-account) bez dodatkowych opłat.

- Należy [skojarzyć aplikację usługi Azure AD z kontem Centrum partnerów](https://docs.microsoft.com/windows/uwp/monetize/create-and-manage-submissions-using-windows-store-services#associate-an-azure-ad-application-with-your-windows-partner-center-account) i uzyskać identyfikator dzierżawy, identyfikator klienta i klucz. Te wartości są potrzebne do uzyskania tokenu dostępu usługi Azure AD, który będzie używany w wywołaniach interfejsu API przesyłania sklepu Microsoft Store.

#### <a name="how-to-associate-an-azure-ad-application-with-your-partner-center-account"></a>Jak skojarzyć aplikację usługi Azure AD z kontem Centrum partnerów

Aby korzystać z interfejsu API przesyłania sklepu Microsoft Store, należy skojarzyć aplikację usługi Azure AD z kontem Centrum partnerów, pobrać identyfikator dzierżawy i identyfikator klienta dla aplikacji i wygenerować klucz. Aplikacja usługi Azure AD reprezentuje aplikację lub usługę, z której chcesz wywołać interfejs API przesyłania Centrum partnerów. Potrzebujesz identyfikatora dzierżawy, identyfikator klienta i klucza, aby uzyskać token dostępu usługi Azure AD, który można przekazać do interfejsu API.

>[!Note]
>Wystarczy wykonać to zadanie tylko raz. Po identyfikatorze dzierżawy, identyfikatorze klienta i kluczu można ich ponownie użyć w dowolnym momencie, aby utworzyć nowy token dostępu usługi Azure AD.

1. W Centrum partnerów [skojarz konto Centrum partnerów organizacji z katalogiem usługi Azure AD w organizacji.](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center)
1. Następnie na stronie **Użytkownicy** w sekcji **Ustawienia konta** w Centrum partnerów [dodaj aplikację usługi Azure AD](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#add-azure-ad-applications-to-your-partner-center-account) reprezentującą aplikację lub usługę, której będziesz używać do uzyskiwania dostępu do przesłanych materiałów dla konta Centrum partnerów. Upewnij się, że przypiszesz tę aplikację roli **Menedżera.** Jeśli aplikacja nie istnieje jeszcze w katalogu usługi Azure AD, możesz [utworzyć nową aplikację usługi Azure AD w Centrum partnerów.](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#create-a-new-azure-ad-application-account-in-your-organizations-directory-and-add-it-to-your-partner-center-account)
1. Wróć do strony **Użytkownicy,** kliknij nazwę aplikacji usługi Azure AD, aby przejść do ustawień aplikacji i skopiuj wartości **identyfikatora dzierżawy** i **identyfikatora klienta.**
1. Kliknij **pozycję Dodaj nowy klucz**. Na poniższym ekranie skopiuj wartość **Klawisz.** Po opuszczeniu tej strony nie będzie można ponownie uzyskać dostępu do tych informacji. Aby uzyskać więcej informacji, zobacz [Zarządzanie kluczami dla aplikacji usługi Azure AD](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#manage-keys).

### <a name="step-2-obtain-an-azure-ad-access-token"></a>Krok 2: Uzyskiwanie tokenu dostępu usługi Azure AD

Przed wywołaniem dowolnej z metod w interfejsie API przesyłania Centrum partnerów należy najpierw uzyskać token dostępu usługi Azure AD, który jest przekazywalny do nagłówka **autoryzacji** każdej metody w interfejsie API. Po uzyskaniu tokenu dostępu masz 60 minut na jego użycie przed jego wygaśnięciem. Po wygaśnięciu tokenu można odświeżyć token, dzięki czemu można nadal używać go w przyszłych wywołań do interfejsu API.

Aby uzyskać token dostępu, postępuj zgodnie z instrukcjami w [service to service calls using client credentials](https://azure.microsoft.com/documentation/articles/active-directory-protocols-oauth-service-to-service/) to send an `HTTP POST` to the `https://login.microsoftonline.com/<tenant_id>/oauth2/token` endpoint. Oto przykładowe żądanie:

JSONCopy (Kopia JSON)
```Json
POST https://login.microsoftonline.com/<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8

grant_type=client_credentials
&client_id=<your_client_id>
&client_secret=<your_client_secret>
&resource= https://api.partner.microsoft.com
```

Dla *wartości tenant_id* w `POST URI` *client_id* i *client_secret* parametrów, określ identyfikator dzierżawy, identyfikator klienta i klucz aplikacji pobranej z Centrum partnerskiego w poprzedniej sekcji. Dla parametru *zasobu* `https://api.partner.microsoft.com`należy określić .

### <a name="step-3-use-the-microsoft-store-submission-api"></a>Krok 3: Korzystanie z interfejsu API przesyłania sklepu Microsoft Store

Po tokenie dostępu usługi Azure AD można wywołać metody w interfejsie API przesyłania Centrum partnerów. Aby utworzyć lub zaktualizować przesłania, zazwyczaj należy wywołać wiele metod w interfejsie API przesyłania Centrum partnerów w określonej kolejności. Aby uzyskać informacje na temat każdego scenariusza i składni każdej metody, zobacz Swagger interfejsu API pozyskiwania pozyskiwania.

https://apidocs.microsoft.com/services/partneringestion/
