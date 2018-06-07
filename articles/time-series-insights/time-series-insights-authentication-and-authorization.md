---
title: Sposób uwierzytelniania i autoryzacji przez interfejs API w usłudze Azure czas serii Insights
description: W tym artykule opisano sposób konfigurowania uwierzytelniania i autoryzacji dla niestandardowych aplikacji, która wywołuje interfejs API Azure czas serii szczegółowych informacji.
ms.service: time-series-insights
services: time-series-insights
author: dmdenmsft
ms.author: dmden
manager: jhubbard
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/27/2017
ms.openlocfilehash: a18caf03dbc5377f0adc540c8550129ef3bdb9e1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34652437"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Uwierzytelnianie i autoryzacja interfejsu API usługi Azure czas serii Insights

W tym artykule opisano sposób konfigurowania uwierzytelniania i autoryzacji używany w niestandardowych aplikacji, która wywołuje interfejs API Azure czas serii szczegółowych informacji.

## <a name="service-principal"></a>Jednostka usługi

W tej sekcji opisano sposób konfigurowania aplikacji na dostęp do interfejsu API Insights serii czasu w imieniu aplikacji. Aplikację można następnie zapytania na danych lub publikować dane referencyjne w środowisku czasu serii Insights z poświadczeń aplikacji, a nie poświadczenia użytkownika.

Jeśli masz aplikację, która musi czas dostępu Insights serii, należy skonfigurować aplikację usługi Azure Active Directory i przypisać zasady dostępu do danych w środowisku Insights serii czasu. Ta metoda jest preferowana, na którym uruchomiono aplikację z poświadczeniami użytkownika, ponieważ:

* Można przypisać uprawnienia do tożsamości aplikacji, które różnią się od własnych uprawnień. Te uprawnienia są zazwyczaj ograniczone tylko co aplikacja wymaga. Na przykład można zezwolić aplikacji na odczytywanie tylko dane w określonym środowisku Insights serii czasu.
* Nie trzeba zmienić poświadczenia aplikacji, jeśli zmiana Twoje obowiązki.
* Certyfikat i klucz aplikacji umożliwia automatyzację uwierzytelniania po uruchomieniu skryptu instalacji nienadzorowanej.

W tym artykule przedstawiono sposób wykonywania tych kroków za pośrednictwem portalu Azure. Głównie aplikacji pojedynczej dzierżawy, gdzie aplikacja jest przeznaczona do uruchamiania w tylko jednej z organizacji. Aplikacje pojedynczej dzierżawy jest zazwyczaj używana dla aplikacji — biznesowych, które są uruchamiane w Twojej organizacji.

Przepływ instalacji składa się z trzech ogólne kroki:

1. Tworzenie aplikacji w usłudze Azure Active Directory.
2. Autoryzowanie tej aplikacji, aby uzyskiwać dostęp do środowiska Insights serii czasu.
3. Użyj aplikacji identyfikator i klucz do uzyskania tokenu na potrzeby `"https://api.timeseries.azure.com/"` odbiorców lub zasobu. Następnie można token do wywołania interfejsu API Insights serii czasu.

Poniżej przedstawiono szczegółowy opis kroków:

1. W portalu Azure wybierz **usługi Azure Active Directory** > **rejestracji aplikacji** > **nowej rejestracji aplikacji**.

   ![Nowej rejestracji aplikacji w usłudze Azure Active Directory](media/authentication-and-authorization/active-directory-new-application-registration.png)  

2. Nadaj nazwę aplikacji, wybierz typ **aplikacji sieci Web / interfejs API**, wybierz dowolny prawidłowy identyfikator URI dla **adres URL logowania**i kliknij przycisk **Utwórz**.

   ![Tworzenie aplikacji w usłudze Azure Active Directory](media/authentication-and-authorization/active-directory-create-web-api-application.png)

3. Wybierz nowo utworzony aplikacji i skopiuj jej identyfikator aplikacji w ulubionym edytorze tekstów.

   ![Skopiuj identyfikator aplikacji](media/authentication-and-authorization/active-directory-copy-application-id.png)

4. Wybierz **klucze**, wprowadź nazwę klucza, wybierz czas wygaśnięcia, a następnie kliknij przycisk **zapisać**.

   ![Wybierz klucze aplikacji](media/authentication-and-authorization/active-directory-application-keys.png)

   ![Wprowadź nazwę klucza i wygaśnięcia i kliknij przycisk Zapisz](media/authentication-and-authorization/active-directory-application-keys-save.png)

5. Skopiuj klucz w ulubionym edytorze tekstów.

   ![Skopiuj klucz aplikacji](media/authentication-and-authorization/active-directory-copy-application-key.png)

6. Dla środowiska czasu serii Insights wybierz **zasady dostępu do danych** i kliknij przycisk **Dodaj**.

   ![Dodaj nowe zasady dostępu do danych w środowisku Insights serii czasu](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)

7. W **wybór użytkownika** okno dialogowe, Wklej nazwy aplikacji (z kroku 2) lub identyfikator aplikacji (z kroku 3).

   ![Znajdowanie aplikacji w oknie dialogowym Wybierz użytkownika](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)

8. Wybierz rolę (**czytnika** na potrzeby zapytań o dane, **współautora** kwerendy danych i modyfikowania dane referencyjne) i kliknij przycisk **Ok**.

   ![Wybierz w oknie dialogowym Wybierz rolę czytelnika lub współautora](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)

9. Zapisz zasady, klikając **Ok**.

10. Użyj Identyfikatora aplikacji (z kroku 3) i klucz aplikacji (z kroku 5) można uzyskać tokenu w imieniu aplikacji. Tokenu można następnie przekazać w `Authorization` nagłówka, gdy aplikacja wywołuje interfejs API czasu serii szczegółowych informacji.

    Jeśli używasz języka C#, można użyć następującego kodu można uzyskać tokenu w imieniu aplikacji. Dla kompletnego przykładu, zobacz [zapytania na danych przy użyciu języka C#](time-series-insights-query-data-csharp.md).

    ```csharp
    var authenticationContext = new AuthenticationContext(
        "https://login.microsoftonline.com/common",
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

Użyj aplikacji identyfikator i klucz do uwierzytelniania w usłudze Azure czas serii wgląd w aplikacji. 

## <a name="next-steps"></a>Kolejne kroki
- Aby uzyskać przykładowy kod, który wywołuje interfejs API czasu serii szczegółowe informacje, zobacz [zapytania na danych przy użyciu języka C#](time-series-insights-query-data-csharp.md).
- Aby uzyskać informacje referencyjne interfejsu API, zobacz [dokumentacja interfejsu API zapytania](/rest/api/time-series-insights/time-series-insights-reference-queryapi).

> [!div class="nextstepaction"]
> [Tworzenie jednostki usługi](../azure-resource-manager/resource-group-create-service-principal-portal.md)
