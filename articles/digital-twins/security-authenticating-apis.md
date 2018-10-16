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
ms.openlocfilehash: 1d5b1869428cec6bf80b8518485f685e38ad5997
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2018
ms.locfileid: "49324203"
---
# <a name="connect-and-authenticate-to-apis"></a>Się i została uwierzytelniona do interfejsów API

Azure Twins cyfrowego używa usługi Azure Active Directory (Azure AD) do uwierzytelniania użytkowników oraz chronić aplikacji.

Jeśli jesteś zaznajomiony z usługi Azure AD, więcej informacji znajduje się w [przewodnik dla deweloperów](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-developers-guide). Biblioteki uwierzytelniania usługi Windows Azure oferuje wiele sposobów uzyskania tokenów usługi Active Directory. Aby uzyskać szczegółowy wgląd w bibliotece, zapoznaj się [tutaj](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki).

Ten artykuł zawiera omówienie dwóch scenariuszy: Scenariusz produkcyjny przy użyciu interfejsu API warstwy środkowej i uwierzytelniania w aplikacji klienckiej narzędzia Postman do szybkiego uruchamiania i testowania.

## <a name="authentication-in-production"></a>Uwierzytelnianie w środowisku produkcyjnym

Deweloperzy rozwiązań ma dwa sposoby nawiązywania połączenia cyfrowego bliźniaczych reprezentacji.  Deweloperzy rozwiązań połączyć Twins cyfrowych platformy Azure w następujący sposób:

* Może być utworzona aplikacja kliencka lub interfejsu API warstwy środkowej. Aplikacje klienckie wymagać od użytkowników uwierzytelniania, a następnie użyj [OAuth 2.0 "w imieniu"](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow) przepływ zabezpieczeń do wywoływania podrzędnego interfejsu API.
* Utwórz lub skorzystać z istniejącej aplikacji usługi Azure AD. Przejrzyj dokumentację [tutaj](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad).
    1. Określ **logowania jednokrotnego i identyfikatory URI przekierowań** (jeśli jest to konieczne).
    1. W aplikacji manifestu zestawu `oauth2AllowImplicitFlow` na wartość true.
    1. W **wymagane uprawnienia**, Dodaj reprezentacje urządzeń cyfrowych, wyszukując "Twins cyfrowych platformy Azure." Wybierz **delegowane uprawnienia odczytu i zapisu** i kliknij przycisk **Udziel uprawnień** przycisku.

Można znaleźć szczegółowe instrukcje dotyczące sposobu organizowania przepływu w imieniu z [tę stronę](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). Można również wyświetlić przykłady kodu [tutaj](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/).

## <a name="test-with-the-postman-client"></a>Testowanie za pomocą klienta programu Postman

1. Wykonaj początkową powyższe kroki, aby utworzyć (lub zmodyfikować) aplikacji usługi Azure Active Directory. Następnie ustaw `oauth2AllowImplicitFlow` wartość true w manifeście aplikacji i udzielić uprawnień "Twins cyfrowych platformy Azure."
1. Ustaw adres url odpowiedzi [ https://www.getpostman.com/oauth2/callback ](https://www.getpostman.com/oauth2/callback).
1. Wybierz **kartę autoryzacji**, kliknij pozycję **OAuth 2.0**i wybierz **uzyskać nowy Token dostępu**.

    |**Pole**  |**Wartość** |
    |---------|---------|
    | Typ udzielania | Niejawny |
    | Adres URL wywołania zwrotnego | [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback) |
    | Adres URL uwierzytelniania | [https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0](https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0)
    | Identyfikator klienta | Używanie identyfikatora aplikacji dla aplikacji usługi Azure AD, który został utworzony lub zmodyfikowany z kroku 1 |
    | Zakres | Pozostaw to pole puste |
    | Stan | Pozostaw to pole puste |
    | Uwierzytelnianie klienta | Wyślij jako nagłówka uwierzytelniania podstawowego |

1. Kliknij przycisk **wysłania żądania tokenu**.

    >[!TIP]
    >Jeśli zostanie wyświetlony komunikat o błędzie "Nie można ukończyć OAuth 2", wypróbuj jedną z następujących czynności:
    > * Zamknij narzędzie Postman i otwórz go ponownie i spróbuj ponownie.
    > * Usuń klucz tajny w swojej aplikacji, ponownie utwórz nową i jest wartość w formularzu powyżej.

1. Przewiń w dół i kliknij przycisk **użycia tokenu**.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o bezpieczeństwie Twins cyfrowych platformy Azure, przeczytaj [tworzenie i Zarządzanie przypisaniami ról](./security-create-manage-role-assignments.md).
