---
title: Jak uwierzytelnianie i autoryzacja w interfejsie API w usłudze Azure Time Series Insights | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania uwierzytelniania i autoryzacji dla niestandardowych aplikacji, która wywołuje interfejs API Azure czas serii szczegółowych informacji.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/27/2017
ms.custom: seodec18
ms.openlocfilehash: 66a3c40bf1e1e1dc6253520a555e19ebf011297c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64698570"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Uwierzytelnianie i autoryzacja dla interfejsu API usługi Azure czas serii szczegółowych informacji

W tym artykule opisano sposób konfigurowania uwierzytelniania i autoryzacji używany w niestandardowych aplikacji, która wywołuje interfejs API Azure czas serii szczegółowych informacji.

## <a name="service-principal"></a>Jednostka usługi

W tej sekcji opisano sposób konfigurowania aplikacji na dostęp do interfejsu API usługi Insights serii czasu imieniu aplikacji. Aplikację można następnie wykonywanie zapytań dotyczących danych lub publikować dane referencyjne w środowisku usługi Time Series Insights przy użyciu poświadczeń aplikacji, a nie poświadczeń użytkownika.

Jeśli masz aplikację, która musi czas dostępu Series Insights, należy skonfigurować aplikację w usłudze Azure Active Directory i przypisać zasady dostępu do danych w środowisku usługi Time Series Insights. To podejście jest preferowane uruchamianie aplikacji z poświadczeniami użytkownika, ponieważ:

* Można przypisać uprawnienia do tożsamości aplikacji, która różni się od własnych uprawnień. Zazwyczaj te uprawnienia są ograniczone do tylko co wymaga aplikacja. Na przykład można zezwolić aplikacji na odczytywanie tylko dane w konkretnym środowisku usługi Time Series Insights.
* Nie trzeba zmienić poświadczenia aplikacji, w przypadku zmiany Twoje obowiązki.
* Aby zautomatyzować uwierzytelniania, po uruchomieniu skryptu instalacji nienadzorowanej, można użyć certyfikatu lub klucza aplikacji.

W tym artykule przedstawiono sposób wykonywania tych kroków w witrynie Azure portal. Uwzględniono w szczególności aplikacji pojedynczej dzierżawy, w którym aplikacja jest przeznaczona do uruchamiania w tylko jednej z organizacji. Aplikacje z jedną dzierżawą jest zazwyczaj używana dla aplikacji line-of-business, które działają w Twojej organizacji.

Przepływ konfiguracji obejmuje trzy kroki wysokiego poziomu:

1. Tworzenie aplikacji w usłudze Azure Active Directory.
2. Autoryzuj tę aplikację, aby uzyskiwać dostęp do środowiska usługi Time Series Insights.
3. Umożliwia uzyskanie tokenu do Identyfikatora aplikacji i klucz `"https://api.timeseries.azure.com/"` odbiorców lub zasobu. Tokenu można następnie wywołać interfejs API usługi Insights serii czasu.

Poniżej przedstawiono szczegółowy opis kroków:

1. W witrynie Azure portal wybierz **usługi Azure Active Directory** > **rejestracje aplikacji** > **rejestrowanie nowej aplikacji**.

   ![Rejestrowanie nowej aplikacji w usłudze Azure Active Directory](media/authentication-and-authorization/active-directory-new-application-registration.png)  

2. Nadaj aplikacji nazwę, wybierz typ jako **aplikacji sieci Web / interfejs API**, wybierz dowolny prawidłowy identyfikator URI dla **adres URL logowania**i kliknij przycisk **Utwórz**.

   ![Tworzenie aplikacji w usłudze Azure Active Directory](media/authentication-and-authorization/active-directory-create-web-api-application.png)

3. Wybierz nowo utworzoną aplikację i skopiuj jej identyfikator aplikacji na swoim ulubionym edytorze tekstów.

   ![Skopiuj identyfikator aplikacji](media/authentication-and-authorization/active-directory-copy-application-id.png)

4. Wybierz **klucze**, wprowadź nazwę klucza, wybierz czas wygaśnięcia, a następnie kliknij przycisk **Zapisz**.

   ![Wybierz klucze aplikacji](media/authentication-and-authorization/active-directory-application-keys.png)

   ![Wprowadź nazwę klucza i wygaśnięcia, a następnie kliknij przycisk Zapisz](media/authentication-and-authorization/active-directory-application-keys-save.png)

5. Skopiuj klucz z ulubionego edytora tekstu.

   ![Skopiuj klucz aplikacji](media/authentication-and-authorization/active-directory-copy-application-key.png)

6. Środowiska usługi Time Series Insights wybierz **zasady dostępu do danych** i kliknij przycisk **Dodaj**.

   ![Dodaj nowe zasady dostępu do danych do środowiska usługi Time Series Insights](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)

7. W **Wybieranie: Użytkownicy** okno dialogowe, Wklej nazwę aplikacji (z kroku 2) lub identyfikator aplikacji (z kroku 3).

   ![Znajdowanie aplikacji w oknie dialogowym Wybierz użytkownika](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)

8. Wybierz rolę (**czytnika** na wykonywanie zapytań o dane, **Współautor** wykonywanie zapytań o dane i modyfikowania danych referencyjnych) i kliknij przycisk **Ok**.

   ![Wybierz w oknie dialogowym Wybierz rolę czytelnika lub współautora](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)

9. Zapisz zasady, klikając **Ok**.

10. Użyj Identyfikatora aplikacji (z kroku 3) i klucz aplikacji (z kroku 5), można uzyskać tokenu w imieniu aplikacji. Następnie można przekazać token `Authorization` nagłówka, gdy aplikacja wywołuje interfejs API Insights serii czasu.

    Jeśli używasz języka C#, można użyć następującego kodu, można uzyskać tokenu w imieniu aplikacji. Aby uzyskać pełny przykład, zobacz [wykonywanie zapytań o dane przy użyciu języka C#](time-series-insights-query-data-csharp.md).

    ```csharp
    // Enter your Active Directory tenant domain name
    var tenant = "YOUR_AD_TENANT.onmicrosoft.com";
    var authenticationContext = new AuthenticationContext(
        $"https://login.microsoftonline.com/{tenant}",
        TokenCache.DefaultShared);

    AuthenticationResult token = await authenticationContext.AcquireTokenAsync(
        // Set the resource URI to the Azure Time Series Insights API
        resource: "https://api.timeseries.azure.com/", 
        clientCredential: new ClientCredential(
            // Application ID of application registered in Azure Active Directory
            clientId: "1bc3af48-7e2f-4845-880a-c7649a6470b8", 
            // Application key of the application that's registered in Azure Active Directory
            clientSecret: "aBcdEffs4XYxoAXzLB1n3R2meNCYdGpIGBc2YC5D6L2="));

    string accessToken = token.AccessToken;
    ```

Użyj klucza i identyfikator aplikacji w aplikacji, do uwierzytelniania za pomocą usługi Azure Time Series Insights. 

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać przykładowy kod, który wywołuje interfejs API czas serii szczegółowych informacji, zobacz [wykonywanie zapytań o dane przy użyciu języka C#](time-series-insights-query-data-csharp.md).

- Aby uzyskać informacje referencyjne interfejsu API, zobacz [dokumentację interfejsu API zapytań](/rest/api/time-series-insights/ga-query-api).

- Dowiedz się, jak [utworzyć nazwę główną usługi](../active-directory/develop/howto-create-service-principal-portal.md).