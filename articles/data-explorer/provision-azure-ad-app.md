---
title: Tworzenie aplikacji usługi Azure AD w Eksploratorze danych platformy Azure
description: Dowiedz się, jak utworzyć aplikację usługi Azure AD w Eksploratorze danych platformy Azure.
author: orspod
ms.author: orspodek
ms.reviewer: herauch
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 64e8637630675120fece1bbe1fa4a57d97f36eb5
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550515"
---
# <a name="create-an-azure-active-directory-application-registration-in-azure-data-explorer"></a>Tworzenie rejestracji aplikacji usługi Azure Active Directory w Eksploratorze danych platformy Azure

Uwierzytelnianie aplikacji usługi Azure Active Directory (Azure AD) jest używane dla aplikacji, takich jak usługa nienadzorowana lub zaplanowany przepływ, które muszą uzyskać dostęp do usługi Azure Data Explorer bez obecności użytkownika. Jeśli łączysz się z bazą danych Usługi Azure Data Explorer przy użyciu aplikacji, takiej jak aplikacja sieci web, należy uwierzytelnić się przy użyciu uwierzytelniania jednostkowego usługi. W tym artykule opisano sposób tworzenia i rejestrowania jednostki usługi Azure AD, a następnie autoryzowania jej dostępu do bazy danych usługi Azure Data Explorer.

## <a name="create-azure-ad-application-registration"></a>Tworzenie rejestracji aplikacji usługi Azure AD

Uwierzytelnianie aplikacji usługi Azure AD wymaga utworzenia i zarejestrowania aplikacji za pomocą usługi Azure AD. Podmiot usługi jest tworzony automatycznie podczas tworzenia rejestracji aplikacji w dzierżawie usługi Azure AD. 

1. Zaloguj się do [portalu](https://portal.azure.com) `Azure Active Directory` Azure i otwórz blok

    ![Wybieranie usługi Azure Active Directory z menu portalu](media/provision-azure-ad-app/create-app-reg-select-azure-active-directory.png)

1. Wybierz blok **Rejestracje aplikacji** i wybierz pozycję **Nowa rejestracja**

    ![Rozpoczynanie rejestracji nowej aplikacji](media/provision-azure-ad-app/create-app-reg-new-registration.png)

1. Wypełnij następujące informacje: 

    * **Nazwa** 
    * **Obsługiwane typy konta**
    * **Przekierowywanie identyfikatora URI** > **w sieci Web**
        > [!IMPORTANT] 
        > Typ aplikacji powinien być **w sieci Web**. Identyfikator URI jest opcjonalny i pozostaje pusty w tym przypadku.
    * Wybierz **zarejestruj się**

    ![Zarejestruj nową rejestrację aplikacji](media/provision-azure-ad-app/create-app-reg-register-app.png)

1. Wybierz **blok Przegląd** i skopiuj **identyfikator aplikacji**.

    > [!NOTE]
    > Identyfikator aplikacji będzie potrzebny do autoryzowania jednostki usługi w celu uzyskania dostępu do bazy danych.

    ![Identyfikator kopiowania aplikacji](media/provision-azure-ad-app/create-app-reg-copy-applicationid.png)

1. W bloku **Klucze & wpisy tajne** wybierz pozycję **Nowy klucz tajny klienta**

    ![Rozpocznij tworzenie klucza tajnego klienta](media/provision-azure-ad-app/create-app-reg-new-client-secret.png)

    > [!TIP]
    > W tym artykule opisano przy użyciu klucza tajnego klienta dla poświadczeń aplikacji.  Do uwierzytelnienia aplikacji można również użyć certyfikatu X509. Wybierz **pozycję Przekaż certyfikat** i postępuj zgodnie z instrukcjami, aby przekazać publiczną część certyfikatu.

1. Wprowadź opis, wygaśnięcie i wybierz pozycję **Dodaj**

    ![Wprowadzanie parametrów tajnych klienta](media/provision-azure-ad-app/create-app-reg-enter-client-secret-details.png)

1. Skopiuj wartość klucza.

    > [!NOTE]
    > Po opuszczeniu tej strony wartość klucza nie będzie dostępna.  Potrzebny jest klucz do skonfigurowania poświadczeń klienta w bazie danych.

    ![Kopiowanie wartości klucza tajnego klienta](media/provision-azure-ad-app/create-app-reg-copy-client-secret.png)

Aplikacja jest tworzona. Jeśli potrzebujesz tylko dostępu do autoryzowanego zasobu usługi Azure Data Explorer, na przykład w poniższym przykładzie programowym, pomiń następną sekcję. Aby uzyskać obsługę uprawnień delegowanych, zobacz [konfigurowanie uprawnień delegowanych do rejestracji aplikacji](#configure-delegated-permissions-for-the-application-registration).

## <a name="configure-delegated-permissions-for-the-application-registration"></a>Konfigurowanie uprawnień delegowanych do rejestracji aplikacji

Jeśli aplikacja musi uzyskać dostęp do Usługi Azure Data Explorer przy użyciu poświadczeń użytkownika wywołującego, należy skonfigurować delegowane uprawnienia do rejestracji aplikacji. Na przykład jeśli budujesz internetowy interfejs API, aby uzyskać dostęp do Eksploratora danych platformy Azure i chcesz uwierzytelnić przy użyciu poświadczeń użytkownika, który *wywołuje* interfejs API.  

1. W bloku **uprawnień interfejsu API** wybierz pozycję **Dodaj uprawnienie**.
1. Wybierz **interfejsy API używane przez moją organizację**. Wyszukaj i wybierz **Eksploratora danych platformy Azure**.

    ![Dodawanie uprawnień interfejsu API usługi Azure Data Explorer](media/provision-azure-ad-app/configure-delegated-add-api-permission.png)

1. W obszarze **Uprawnienia delegowane**zaznacz pole **user_impersonation** i **Dodaj uprawnienia**

    ![Wybieranie uprawnień delegowanych z personifikacją użytkowników](media/provision-azure-ad-app/configure-delegated-click-add-permissions.png)     

## <a name="grant-the-service-principal-access-to-an-azure-data-explorer-database"></a>Udzielanie podmiotowi usługi dostępu do bazy danych Usługi Azure Data Explorer

Teraz, gdy zostanie utworzona rejestracja aplikacji jednostkowej usługi, należy udzielić odpowiedniego dostępu jednostki usługi do bazy danych usługi Azure Data Explorer. 

1. W [interfejsie użytkownika sieci Web](https://dataexplorer.azure.com/)połącz się z bazą danych i otwórz kartę kwerendy.

1. Wykonaj następujące polecenie:

    ```kusto
    .add database <DatabaseName> viewers ('<ApplicationId>') '<Notes>'
    ```

    Przykład:
    
    ```kusto
    .add database Logs viewers ('aadapp=f778b387-ba15-437f-a69e-ed9c9225278b') 'Azure Data Explorer App Registration'
    ```

    Ostatni parametr jest ciągiem, który jest wyświetlany jako notatki podczas kwerendy ról skojarzonych z bazą danych.
    
    > [!NOTE]
    > Po utworzeniu rejestracji aplikacji może wystąpić kilkuminutowe opóźnienie, dopóki Eksplorator danych platformy Azure nie będzie mógł się do niej odwoływać. Jeśli podczas wykonywania polecenia zostanie wyświetlony błąd, że aplikacja nie została znaleziona, poczekaj i spróbuj ponownie.

Aby uzyskać więcej informacji, zobacz [zarządzanie rolami zabezpieczeń](/azure/kusto/management/security-roles) i [uprawnienia pozyskiwania](/azure/kusto/api/netfx/kusto-ingest-client-permissions.md).  

## <a name="using-application-credentials-to-access-a-database"></a>Uzyskiwanie dostępu do bazy danych przy użyciu poświadczeń aplikacji

Użyj poświadczeń aplikacji, aby programowo uzyskać dostęp do bazy danych przy użyciu [biblioteki klienta usługi Azure Data Explorer.](/azure/kusto/api/netfx/about-kusto-data.md)

```C#
. . .
string applicationClientId = "<myClientID>";
string applicationKey = "<myApplicationKey>";
. . .
var kcsb = new KustoConnectionStringBuilder($"https://{clusterName}.kusto.windows.net/{databaseName}")
    .WithAadApplicationKeyAuthentication(
        applicationClientId,
        applicationKey,
        authority);
var client = KustoClientFactory.CreateCslQueryProvider(kcsb);
var queryResult = client.ExecuteQuery($"{query}");
```

   > [!NOTE]
   > Określ identyfikator aplikacji i klucz rejestracji aplikacji (jednostki usługi) utworzonej wcześniej.

Aby uzyskać więcej informacji, zobacz [uwierzytelnianie za pomocą usługi Azure AD dla usługi Azure Data Explorer i](/azure/kusto/management/access-control/how-to-authenticate-with-aad) korzystanie z usługi Azure Key Vault z [aplikacją sieci Web .NET Core](/azure/key-vault/tutorial-net-create-vault-azure-web-app#create-a-net-core-web-app).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="invalid-resource-error"></a>Nieprawidłowy błąd zasobu

If your application is used to authenticate users or applications for Azure Data Explorer access, you must set up delegated permissions for Azure Data Explorer service application. Zadeklarować aplikację można uwierzytelnić użytkowników lub aplikacji dla dostępu Usługi Azure Data Explorer. Niezagospodowania tego spowoduje błąd podobny do następującego, gdy zostanie podjęta próba uwierzytelnienia:

`AADSTS650057: Invalid resource. The client has requested access to a resource which is not listed in the requested permissions in the client's application registration...`

Należy postępować zgodnie z instrukcjami dotyczącymi [konfigurowania uprawnień delegowanych dla aplikacji usługi Usługi Azure Data Explorer](#configure-delegated-permissions-for-the-application-registration).

### <a name="enable-user-consent-error"></a>Włącz błąd zgody użytkownika

Administrator dzierżawy usługi Azure AD może uchwalić zasadę, która uniemożliwia użytkownikom dzierżawy udzielanie zgody na aplikacje. Ta sytuacja spowoduje błąd podobny do następującego, gdy użytkownik próbuje zalogować się do aplikacji:

`AADSTS65001: The user or administrator has not consented to use the application with ID '<App ID>' named 'App Name'`

Musisz skontaktować się z administratorem usługi Azure AD, aby udzielić zgody wszystkim użytkownikom w dzierżawie lub włączyć zgodę użytkownika dla określonej aplikacji.

## <a name="next-steps"></a>Następne kroki

* Zobacz [Parametry połączenia Kusto,](/azure/kusto/api/connection-strings/kusto.md) aby uzyskać listę obsługiwanych ciągów połączeń.
