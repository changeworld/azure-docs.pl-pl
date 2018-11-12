---
title: Omówienie uwierzytelniania interfejsu API Azure cyfrowego Twins | Dokumentacja firmy Microsoft
description: Użyj Twins cyfrowych platformy Azure, aby się i została uwierzytelniona do interfejsów API
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/02/2018
ms.author: lyrana
ms.openlocfilehash: f85ab05e785ea559962490b43e75b196d1602159
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2018
ms.locfileid: "51016220"
---
# <a name="connect-and-authenticate-to-apis"></a>Się i została uwierzytelniona do interfejsów API

Azure Twins cyfrowego używa usługi Azure Active Directory (Azure AD) do uwierzytelniania użytkowników oraz chronić aplikacji. Usługa Azure AD obsługuje uwierzytelnianie dla szerokiego zakresu nowoczesnych architektur. Wszystkie z nich są oparte na standardowych protokołów uwierzytelniania OAuth 2.0 lub OpenID Connect. Ponadto deweloperzy mogą używać usługi Azure AD, do tworzenia aplikacji pojedynczej dzierżawy i line-of-business (LOB) aplikacji. Tworzenie aplikacji wielodostępnych deweloperzy również mogą używać usługi Azure AD.

Omówienie usługi Azure AD, odwiedź stronę [strony podstawy](https://docs.microsoft.com/azure/active-directory/fundamentals/index) przewodniki krok po kroku, pojęcia i przewodników Szybki Start.

Aby zintegrować aplikację lub usługę z usługą Azure AD, deweloper musi najpierw zarejestrować aplikację w usłudze Azure AD. Aby uzyskać szczegółowe informacje i zrzuty ekranu, zobacz [ten przewodnik Szybki Start](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app).

[Pięciu scenariuszy aplikacji głównej](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types) są obsługiwane przez usługę Azure AD:

* Aplikacja jednostronicowa (SPA): użytkownik musi zalogować się do aplikacji jednostronicowej, która jest zabezpieczony przez usługę Azure AD.
* Przeglądarki sieci Web do aplikacji sieci web: użytkownik musi zalogować się do aplikacji sieci web, która jest zabezpieczony przez usługę Azure AD.
* Aplikacja natywna do internetowego interfejsu API: natywnych aplikacji, która działa na telefonie, tablecie lub komputerze wymaga uwierzytelnienia użytkownika można pobrać zasobów z interfejsu API sieci web, która jest zabezpieczony przez usługę Azure AD.
* Aplikacja do internetowego interfejsu API sieci Web: aplikacja sieci web musi pobrać zasoby z internetowego interfejsu API zabezpieczony przez usługę Azure AD.
* Demon lub serwera aplikacji do internetowego interfejsu API: aplikacji demona lub aplikacji serwera przy użyciu interfejsu użytkownika sieci web musi pobrać zasoby z internetowego interfejsu API zabezpieczony przez usługę Azure AD.

Biblioteki uwierzytelniania usługi Windows Azure oferuje wiele sposobów uzyskania tokenów usługi Active Directory. Aby uzyskać szczegółowe informacje dotyczące biblioteki oraz przykłady kodu, zobacz [w tym artykule](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki).

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>Wywoływanie cyfrowego bliźniaczych elementów z internetowego interfejsu API warstwy środkowej

Gdy deweloperom tworzyć architektury rozwiązań cyfrowych Twins, zwykle tworzą aplikacji warstwy środkowej lub interfejsu API. Aplikacji lub interfejsu API wywołuje cyfrowego API Twins podrzędne. Użytkownicy są uwierzytelniani najpierw do aplikacji w warstwie pośredniej, a następnie w imieniu użytkownika z tokenu przepływ jest używany do wywoływania podrzędnego. Aby uzyskać instrukcje dotyczące sposobu organizowania przepływu w imieniu z, zobacz [tę stronę](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). Możesz również wyświetlić przykłady kodu dla [tę stronę](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/).


## <a name="test-with-the-postman-client"></a>Testowanie za pomocą klienta programu Postman

Aby rozpocząć pracę z interfejsami API usługi cyfrowego Twins, można użyć z klienta takiego jak Postman jako środowisko interfejsu API. Postman ułatwia szybkie tworzenie złożonych żądań HTTP. Poniższe kroki pokazują, jak można pobrać tokenu usługi Azure AD, potrzebne do wywoływania cyfrowego bliźniaczych reprezentacji w Interfejsie użytkownika narzędzia Postman.


1. Przejdź do https://www.getpostman.com/ pobrać aplikację.
1. Postępuj zgodnie z instrukcjami w [ten przewodnik Szybki Start](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) do tworzenia aplikacji usługi Azure AD. Lub możesz ponownie użyć istniejącej rejestracji. 
1. W obszarze **wymagane uprawnienia**, wprowadź "Twins cyfrowych platformy Azure" i wybierz **delegowane uprawnienia**. Następnie wybierz pozycję **Udziel uprawnień**.
1. Otwórz w manifeście aplikacji i ustaw **oauth2AllowImplicitFlow** na wartość true.
1. Adres URL odpowiedzi, aby skonfigurować [ https://www.getpostman.com/oauth2/callback ](https://www.getpostman.com/oauth2/callback).
1. Wybierz **autoryzacji** zaznacz **OAuth 2.0**, a następnie wybierz pozycję **uzyskać nowy Token dostępu**.

    |**Pole**  |**Wartość** |
    |---------|---------|
    | Typ udzielania | Niejawny |
    | Adres URL wywołania zwrotnego | [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback) |
    | Adres URL uwierzytelniania | https://login.microsoftonline.com/<Your Azure AD Tenant e.g. Contoso>.onmicrosoft.com/oauth2/Authorize?Resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0 |
    | Identyfikator klienta | Identyfikator aplikacji na użytek aplikacji usługi Azure AD, który został utworzony lub zmodyfikowany z kroku nr 2. |
    | Zakres | Pozostaw to pole puste. |
    | Stan | Pozostaw to pole puste. |
    | Uwierzytelnianie klienta | Wyślij jako nagłówka uwierzytelniania podstawowego. |

1. Wybierz **wysłania żądania tokenu**.

    >[!NOTE]
    >Jeśli zostanie wyświetlony komunikat o błędzie "Nie można ukończyć OAuth 2", spróbuj wykonać następujące czynności:
    > * Narzędzia Postman, zamknij i otwórz go ponownie i spróbuj ponownie.
   
1. Przewiń w dół i wybierz **użycia tokenu**.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o bezpieczeństwie Twins cyfrowych platformy Azure, przeczytaj [tworzenie i Zarządzanie przypisaniami ról](./security-create-manage-role-assignments.md).
