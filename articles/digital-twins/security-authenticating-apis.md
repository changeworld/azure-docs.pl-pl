---
title: Understanding Azure cyfrowego Twins uwierzytelniania interfejsu API | Dokumentacja firmy Microsoft
description: Za pomocą Twins cyfrowych platformy Azure do nawiązywania połączenia i uwierzytelniania do interfejsów API
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/02/2018
ms.author: lyrana
ms.openlocfilehash: 414a52141aac05aac51bbb4fedf6ab67d98f2637
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50092274"
---
# <a name="connect-and-authenticate-to-apis"></a>Się i została uwierzytelniona do interfejsów API

Azure Twins cyfrowego używa usługi Azure Active Directory (Azure AD) do uwierzytelniania użytkowników oraz chronić aplikacji. Usługa Azure AD obsługuje uwierzytelnianie dla szerokiego zakresu nowoczesnych architekturach, wszystkie z nich na podstawie standardowych protokołów uwierzytelniania OAuth 2.0 lub OpenID Connect. Ponadto usługa Azure AD umożliwia deweloperom tworzenie zarówno pojedynczej dzierżawy, line-of-business aplikacje (biznesowe LOB), jak i deweloperów chcących tworzyć aplikacje wielodostępne.

Omówienie usługi Azure AD, odwiedź stronę [strony podstawy](https://docs.microsoft.com/azure/active-directory/fundamentals/index) przewodniki krok po kroku, pojęcia i przewodników Szybki Start.

Aby zintegrować aplikację lub usługę z usługą Azure AD, deweloper musi najpierw zarejestrować aplikację w usłudze Azure AD. Aby uzyskać szczegółowe instrukcje i zrzuty ekranu widoku instrukcje [tutaj](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)

Są to [pięciu scenariuszy aplikacji głównej](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types) obsługiwane przez usługę Azure AD:

* Aplikacja jednostronicowa (SPA): użytkownik musi zalogować się do aplikacji jednostronicowej, która jest zabezpieczony przez usługę Azure AD.
* Przeglądarki sieci Web do aplikacji sieci web: użytkownik musi zalogować się do aplikacji sieci web, która jest zabezpieczony przez usługę Azure AD.
* Aplikacja natywna do internetowego interfejsu API: natywnych aplikacji, która działa na telefonie, tablecie lub komputerze wymaga uwierzytelnienia użytkownika można pobrać zasobów z internetowego interfejsu API, która jest zabezpieczony przez usługę Azure AD.
* Aplikacja do internetowego interfejsu API sieci Web: aplikacja sieci web musi pobrać zasoby z internetowego interfejsu API zabezpieczony przez usługę Azure AD.
* Demon lub serwera aplikacji do internetowego interfejsu API: aplikacji demona albo aplikacja serwera bez interfejsu użytkownika sieci web musi pobrać zasoby z internetowego interfejsu API zabezpieczony przez usługę Azure AD.

Biblioteki uwierzytelniania usługi Windows Azure oferuje wiele sposobów uzyskania tokenów usługi Active Directory. Aby uzyskać szczegółowy wgląd w bibliotece, a także kod przykłady zapoznaj się [tutaj](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki).

## <a name="calling-digital-twins-from-a-middle-tier-web-api"></a>Wywoływanie cyfrowego bliźniaczych elementów z internetowego interfejsu API warstwy środkowej

Podczas projektowanie często deweloperzy rozwiązań cyfrowych Twins wybrać do utworzenia aplikacji warstwy środkowej lub interfejsu API, który następnie wywołuje cyfrowego API bliźniaczych elementów podrzędnych. Użytkownicy będą najpierw zostać uwierzytelnione aplikacji warstwy średniej, a następnie w imieniu użytkownika z tokenu przepływ będzie używany podczas wywoływania podrzędnego. Można znaleźć szczegółowe instrukcje dotyczące sposobu organizowania przepływu w imieniu z [tę stronę](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). Można również wyświetlić przykłady kodu [tutaj](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/).


## <a name="test-with-the-postman-client"></a>Testowanie za pomocą klienta programu Postman

Aby rozpocząć pracę z interfejsami API usługi cyfrowego bliźniaczych reprezentacji klienta, takiego jak narzędzie Postman służy jako środowisko interfejsu API. Postman ułatwia szybkie tworzenie złożonych żądań HTTP. W poniższych instrukcjach koncentruje się na temat sposobu uzyskania tokenu usługi Azure AD wymagane do wywołania bliźniaczych cyfrowych elementów w Interfejsie użytkownika narzędzia Postman.


1. Przejdź do https://www.getpostman.com/ do pobrania
1. Postępuj zgodnie z instrukcjami [tutaj](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) do tworzenia aplikacji usługi Azure Active Directory (lub istnieje możliwość ponownego użycia istniejącej rejestracji). 
1. W obszarze wymaganych uprawnień Dodaj "Twins cyfrowych platformy Azure", a następnie wybierz delegowane uprawnienia. Nie zapomnij kliknąć przycisk Udziel uprawnień, aby zakończyć.
1. Otwórz w manifeście aplikacji i ustaw oauth2AllowImplicitFlow na wartość true
1. Adres url odpowiedzi, aby skonfigurować [ https://www.getpostman.com/oauth2/callback ](https://www.getpostman.com/oauth2/callback).
1. Wybierz **kartę autoryzacji**, kliknij pozycję **OAuth 2.0**i wybierz **uzyskać nowy Token dostępu**.

    |**Pole**  |**Wartość** |
    |---------|---------|
    | Typ udzielania | Niejawny |
    | Adres URL wywołania zwrotnego | [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback) |
    | Adres URL uwierzytelniania | https://login.microsoftonline.com/<Your Azure AD Tenant e.g. Contoso>.onmicrosoft.com/oauth2/Authorize?Resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0 |
    | Identyfikator klienta | Używanie identyfikatora aplikacji dla aplikacji usługi Azure AD, który został utworzony lub zmodyfikowany z kroku 1 |
    | Zakres | Pozostaw to pole puste |
    | Stan | Pozostaw to pole puste |
    | Uwierzytelnianie klienta | Wyślij jako nagłówka uwierzytelniania podstawowego |

1. Kliknij przycisk **wysłania żądania tokenu**.

    >[!NOTE]
    >Jeśli zostanie wyświetlony komunikat o błędzie "Nie można ukończyć OAuth 2", spróbuj wykonać następujące czynności:
    > * Zamknij narzędzie Postman i otwórz go ponownie i spróbuj ponownie.
   
1. Przewiń w dół i kliknij przycisk **użycia tokenu**.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o bezpieczeństwie Twins cyfrowych platformy Azure, przeczytaj [tworzenie i Zarządzanie przypisaniami ról](./security-create-manage-role-assignments.md).
