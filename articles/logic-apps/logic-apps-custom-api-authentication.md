---
title: Dodawanie uwierzytelniania do niestandardowych interfejsów API — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Konfigurowanie uwierzytelniania, wywoływania niestandardowych interfejsów API z poziomu usługi Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/22/2017
ms.openlocfilehash: 555083235aff08476e82f0daa81203b66591f3cc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66167245"
---
# <a name="secure-calls-to-custom-apis-from-azure-logic-apps"></a>Bezpieczne wywołania niestandardowych interfejsów API usługi Azure Logic Apps

Aby zabezpieczyć wywołania interfejsów API, należy skonfigurować uwierzytelnianie usługi Azure Active Directory (Azure AD) za pośrednictwem witryny Azure portal aby nie musieli zaktualizować kod. Alternatywnie można wymagać i wymuszać uwierzytelnianie za pomocą kodu interfejsu API.

## <a name="authentication-options-for-your-api"></a>Opcje uwierzytelniania dla interfejsu API

Możesz zabezpieczyć wywołania do niestandardowego interfejsu API w następujący sposób:

* [Bez zmian w kodzie](#no-code): Ochrona interfejsu API za pomocą [usługi Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) za pośrednictwem witryny Azure portal, więc nie trzeba aktualizowania kodu ani jego ponownego wdrażania interfejsu API.

  > [!NOTE]
  > Domyślnie uwierzytelnianie usługi Azure AD, który można włączyć w witrynie Azure portal nie zawiera szczegółowych autoryzacji. Na przykład to uwierzytelnianie blokuje interfejsu API do określonej dzierżawy, a nie do określonego użytkownika lub aplikacji. 

* [Aktualizowanie kodu interfejsu API](#update-code): Ochrona interfejsu API przez wymuszenie stałego [uwierzytelnianie certyfikatu](#certificate), [uwierzytelnianie podstawowe](#basic), lub [uwierzytelniania usługi Azure AD](#azure-ad-code) za pomocą kodu.

<a name="no-code"></a>

### <a name="authenticate-calls-to-your-api-without-changing-code"></a>Uwierzytelnianie wywołania interfejsu API bez konieczności zmieniania kodu

Poniżej przedstawiono ogólne kroki dla tej metody:

1. Utwórz dwie usługi Azure Active Directory (Azure AD) aplikacji tożsamości: jeden dla aplikacji logiki i jeden dla aplikacji sieci web (lub aplikacji interfejsu API).

2. Do uwierzytelniania wywołań interfejsu API, należy użyć poświadczeń (identyfikator klienta i klucz tajny) dla jednostki usługi, która jest skojarzona z tożsamość aplikacji usługi Azure AD dla twojej aplikacji logiki.

3. Dołącz identyfikatorów aplikacji definicji aplikacji logiki.

#### <a name="part-1-create-an-azure-ad-application-identity-for-your-logic-app"></a>Część 1: Utwórz tożsamość aplikacji usługi Azure AD dla twojej aplikacji logiki

Twoja aplikacja logiki używa tej tożsamości aplikacji usługi Azure AD do uwierzytelniania względem usługi Azure AD. Musisz skonfigurować tę tożsamość jeden raz dla katalogu. Na przykład można używać tej samej tożsamości dla twojej aplikacji logiki, mimo że można utworzyć unikatowych tożsamości dla każdej aplikacji logiki. Można skonfigurować te tożsamości w witrynie Azure portal lub przy użyciu [PowerShell](#powershell).

**Utwórz tożsamość aplikacji dla aplikacji logiki w witrynie Azure portal**

1. W [witryny Azure portal](https://portal.azure.com "https://portal.azure.com"), wybierz **usługi Azure Active Directory**. 

2. Upewnij się, że jesteś w tym samym katalogu co aplikacji sieci web lub aplikacji interfejsu API.

   > [!TIP]
   > Aby przełączyć katalogi, wybierz swój profil, a następnie wybrać inny katalog. Lub wybierz **Przegląd** > **Przełącz katalog**.

3. W menu katalogu w ramach **Zarządzaj**, wybierz **rejestracje aplikacji** > **rejestrowanie nowej aplikacji**.

   > [!TIP]
   > Domyślnie lista rejestracje aplikacji zawiera wszystkie rejestracje aplikacji w Twoim katalogu. Aby wyświetlić tylko rejestracje aplikacji, obok pola wyszukiwania, wybierz **Moje aplikacje**. 

   ![Tworzenie nowej rejestracji aplikacji](./media/logic-apps-custom-api-authentication/new-app-registration-azure-portal.png)

4. Nazwij swoją tożsamość aplikacji, pozostaw **typ aplikacji** równa **aplikacji sieci Web / interfejsu API**, podaj unikatowy ciąg w formacie domeny pod kątem **adres URL logowania**i wybierz polecenie  **Utwórz**.

   ![Podaj nazwę i adres URL logowania do aplikacji tożsamości](./media/logic-apps-custom-api-authentication/logic-app-identity-azure-portal.png)

   Tożsamość aplikacji, który został utworzony dla aplikacji logiki teraz pojawia się na liście rejestracji aplikacji.

   ![Tożsamość aplikacji dla aplikacji logiki](./media/logic-apps-custom-api-authentication/logic-app-identity-created.png)

5. Na liście rejestracji aplikacji wybierz Twojej nowej tożsamości aplikacji. Skopiuj i Zapisz **identyfikator aplikacji** do użycia jako "Identyfikator klienta" dla aplikacji logiki w części 3.

   ![Skopiuj i Zapisz identyfikator aplikacji dla aplikacji logiki](./media/logic-apps-custom-api-authentication/logic-app-application-id.png)

6. Jeśli ustawienia tożsamości aplikacji nie są widoczne, wybierz opcję **ustawienia** lub **wszystkie ustawienia**.

7. W obszarze **dostęp do interfejsu API**, wybierz **klucze**. W obszarze **opis**, podaj nazwę dla swojego klucza. W obszarze **Expires**, wybierz czas trwania dla klucza.

   Klucz, który tworzysz działa jako tożsamość aplikacji "wpis tajny" lub hasła dla aplikacji logiki.

   ![Utwórz klucz tożsamości aplikacji logiki](./media/logic-apps-custom-api-authentication/create-logic-app-identity-key-secret-password.png)

8. Na pasku narzędzi wybierz **Zapisz**. W obszarze **wartość**, pojawi się klucz. 
**Upewnij się skopiować i zapisać klucz** do późniejszego użycia, ponieważ jest ukryta klucz wychodząc **klucze** strony.

   Po skonfigurowaniu aplikacji logiki w części 3, należy podać ten klucz jako "wpis tajny" lub hasło.

   ![Skopiuj i Zapisz klucz do późniejszego](./media/logic-apps-custom-api-authentication/logic-app-copy-key-secret-password.png)

<a name="powershell"></a>

**Tworzenie tożsamości aplikacji w aplikacji logiki w programie PowerShell**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Można wykonać tego zadania za pomocą usługi Azure Resource Manager przy użyciu programu PowerShell. W programie PowerShell uruchom następujące polecenia:

1. `Add-AzAccount`

2. `$SecurePassword = Read-Host -AsSecureString` (Wprowadź hasło i naciśnij klawisz enter)

3. `New-AzADApplication -DisplayName "MyLogicAppID" -HomePage "http://mydomain.tld" -IdentifierUris "http://mydomain.tld" -Password $SecurePassword`

4. Upewnij się skopiować **identyfikator dzierżawy** (identyfikator GUID dla dzierżawy usługi Azure AD), **identyfikator aplikacji**oraz hasło, których używasz.

Aby uzyskać więcej informacji, Dowiedz się, jak [utworzyć nazwę główną usługi za pomocą programu PowerShell, dostęp do zasobów](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

#### <a name="part-2-create-an-azure-ad-application-identity-for-your-web-app-or-api-app"></a>Część 2: Utwórz tożsamość aplikacji usługi Azure AD dla aplikacji sieci web lub aplikacji interfejsu API

W przypadku aplikacji sieci web lub aplikacji interfejsu API została już wdrożona, można włączyć uwierzytelnianie i tworzenie tożsamości aplikacji w witrynie Azure portal. W przeciwnym razie możesz [Włączanie uwierzytelniania w przypadku wdrażania przy użyciu szablonu usługi Azure Resource Manager](#authen-deploy). 

**Utwórz tożsamość aplikacji i włączanie uwierzytelniania w witrynie Azure portal w przypadku aplikacji wdrożonych w**

1. W [witryny Azure portal](https://portal.azure.com "https://portal.azure.com")Znajdź i zaznacz aplikacji sieci web lub aplikacji interfejsu API. 

2. W obszarze **ustawienia**, wybierz **uwierzytelniania/autoryzacji**. W obszarze **uwierzytelnianie usługi App Service**, włączyć uwierzytelnianie **na**. W obszarze **dostawców uwierzytelniania**, wybierz **usługi Azure Active Directory**.

   ![Włączanie uwierzytelniania](./media/logic-apps-custom-api-authentication/custom-web-api-app-authentication.png)

3. Teraz można utworzyć tożsamości aplikacji dla aplikacji sieci web lub aplikacji interfejsu API, jak pokazano poniżej. Na **ustawienia usługi Azure Active Directory** ustaw **tryb zarządzania** do **Express**. Wybierz **Utwórz nową aplikację usługi AD**. Nazwij swoją tożsamość aplikacji, a następnie wybierz **OK**. 

   ![Tworzenie aplikacji tożsamości dla aplikacji sieci web lub aplikacji interfejsu API](./media/logic-apps-custom-api-authentication/custom-api-application-identity.png)

4. Na stronie **Uwierzytelnianie/autoryzacja** wybierz pozycję **Zapisz**.

Teraz należy znaleźć klienta identyfikator i identyfikator dzierżawy dla tożsamości aplikacji, która jest skojarzona z aplikacji sieci web lub aplikacji interfejsu API. Możesz użyć tych identyfikatorów w części 3. Dlatego należy kontynuować te kroki dla witryny Azure portal.

**Znajdź identyfikator klienta i identyfikator dzierżawy aplikacji tożsamości dla aplikacji sieci web lub aplikacji interfejsu API w witrynie Azure portal**

1. W obszarze **dostawców uwierzytelniania**, wybierz **usługi Azure Active Directory**. 

   ![Wybieranie pozycji „Azure Active Directory”](./media/logic-apps-custom-api-authentication/custom-api-app-identity-client-id-tenant-id.png)

2. Na **ustawienia usługi Azure Active Directory** ustaw **tryb zarządzania** do **zaawansowane**.

3. Kopiuj **identyfikator klienta**i Zapisz ten identyfikator GUID do użytku w część 3.

   > [!TIP] 
   > Jeśli **identyfikator klienta** i **adres Url wystawcy** nie występować, spróbuj odświeżyć witryny Azure portal i powtórz krok 1.

4. W obszarze **adres Url wystawcy**, skopiuj i Zapisz tylko identyfikator GUID dla części 3. Umożliwia także ten identyfikator GUID w aplikacji sieci web lub aplikacji interfejsu API wdrożenia szablonu, jeśli to konieczne.

   Ten identyfikator GUID jest identyfikatorem GUID określonej dzierżawy ("identyfikator dzierżawy") i powinna zostać wyświetlona na ten adres URL: `https://sts.windows.net/{GUID}`

5. Bez zapisywania zmian, Zamknij **ustawienia usługi Azure Active Directory** strony.

<a name="authen-deploy"></a>

**Włączanie uwierzytelniania w przypadku wdrażania przy użyciu szablonu usługi Azure Resource Manager**

Nadal należy utworzyć tożsamość aplikacji usługi Azure AD dla aplikacji sieci web lub aplikacji interfejsu API, która różni się od tożsamości aplikacji dla aplikacji logiki. Aby utworzyć tożsamość aplikacji, wykonaj poprzednie kroki w części 2 dla witryny Azure portal. 

Można również postępuj zgodnie z instrukcjami w części 1, ale pamiętaj używać aplikacji sieci web lub aplikacji interfejsu API rzeczywiste `https://{URL}` dla **adres URL logowania** i **identyfikator URI Identyfikatora aplikacji**. Po wykonaniu tych kroków należy zapisać Identyfikatora klienta i identyfikator dzierżawy do użytku w szablonie wdrożenia aplikacji, a także w część 3.

> [!NOTE]
> Kiedy tworzysz tożsamość aplikacji usługi Azure AD dla aplikacji sieci web lub aplikacji interfejsu API, należy użyć witryny Azure portal, nie programu PowerShell. Polecenia cmdlet programu PowerShell nie skonfigurować wymagane uprawnienia do logowania się użytkowników do witryny sieci Web.

Po otrzymaniu klienta identyfikator i identyfikator dzierżawy obejmują tych identyfikatorów jako zasobu podrzędnego w aplikacji sieci web lub aplikacji interfejsu API w szablonie wdrożenia:

``` json
"resources": [ {
    "apiVersion": "2015-08-01",
    "name": "web",
    "type": "config",
    "dependsOn": ["[concat('Microsoft.Web/sites/','parameters('webAppName'))]"],
    "properties": {
        "siteAuthEnabled": true,
        "siteAuthSettings": {
            "clientId": "{client-ID}",
            "issuer": "https://sts.windows.net/{tenant-ID}/",
        }
    }
} ]
```

Aby automatycznie wdrażać aplikację internetową puste i aplikację logiki, wraz z uwierzytelniania usługi Azure Active Directory [Wyświetl pełny szablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-custom-api/azuredeploy.json), lub kliknij przycisk **Wdróż na platformie Azure** tutaj:

[![Wdrażanie na platformie Azure](media/logic-apps-custom-api-authentication/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

#### <a name="part-3-populate-the-authorization-section-in-your-logic-app"></a>Część 3: Wypełnij sekcję autoryzacji w aplikacji logiki

Poprzedni szablon jest już w tej sekcji autoryzacji, konfigurowanie, ale jeśli są bezpośrednie tworzenie aplikacji logiki, musi zawierać sekcję pełną autoryzację.

Otwieranie definicji aplikacji logiki w widoku kodu, przejdź do **HTTP** sekcji Akcja Znajdź **autoryzacji** sekcji, a następnie dołączyć ten wiersz:

`{"tenant": "{tenant-ID}", "audience": "{client-ID-from-Part-2-web-app-or-API app}", "clientId": "{client-ID-from-Part-1-logic-app}", "secret": "{key-from-Part-1-logic-app}", "type": "ActiveDirectoryOAuth" }`

| Element | Wymagany | Opis | 
| ------- | -------- | ----------- | 
| tenant | Tak | Identyfikator GUID dla dzierżawy usługi Azure AD | 
| audience | Tak | Identyfikator GUID zasobu docelowego, którą chcesz uzyskać dostęp, czyli identyfikator klienta z tożsamości aplikacji dla aplikacji sieci web lub aplikacji interfejsu API | 
| clientId | Tak | Identyfikator GUID dla klientów żądających dostępu, który jest identyfikator klienta z tożsamości aplikacji w aplikacji logiki | 
| secret | Tak | Klucz lub hasło za pomocą tożsamości aplikacji klienta, który żąda tokenu dostępu | 
| type | Tak | Typ uwierzytelniania. W przypadku uwierzytelniania ActiveDirectoryOAuth wartością jest `ActiveDirectoryOAuth`. | 
|||| 

Na przykład:

``` json
{
   "actions": {
      "some-action": {
         "conditions": [],
         "inputs": {
            "method": "post",
            "uri": "https://your-api-azurewebsites.net/api/your-method",
            "authentication": {
               "tenant": "tenant-ID",
               "audience": "client-ID-from-azure-ad-app-for-web-app-or-api-app",
               "clientId": "client-ID-from-azure-ad-app-for-logic-app",
               "secret": "key-from-azure-ad-app-for-logic-app",
               "type": "ActiveDirectoryOAuth"
            }
         },
      }
   }
}
```

<a name="update-code"></a>

### <a name="secure-api-calls-through-code"></a>Bezpieczne wywołania interfejsu API za pomocą kodu

<a name="certificate"></a>

#### <a name="certificate-authentication"></a>Uwierzytelnianie certyfikatów

Aby sprawdzić poprawność żądania przychodzące z aplikacji logiki do aplikacji sieci web lub aplikacji interfejsu API, można użyć certyfikatów klienta. Aby skonfigurować swój kod, Dowiedz się, [jak skonfigurować wzajemne uwierzytelnianie protokołu TLS](../app-service/app-service-web-configure-tls-mutual-auth.md).

W **autoryzacji** sekcji, podając ten wiersz: 

`{"type": "clientcertificate", "password": "password", "pfx": "long-pfx-key"}`

| Element | Wymagany | Opis | 
| ------- | -------- | ----------- | 
| type | Tak | Typ uwierzytelniania. Dla certyfikatów klientów SSL, wartość musi być `ClientCertificate`. | 
| password | Tak | Hasło do uzyskiwania dostępu do certyfikatu klienta (plik PFX) | 
| pfx | Tak | Zawartość algorytmem Base64 (plik PFX) certyfikatu klienta | 
|||| 

<a name="basic"></a>

#### <a name="basic-authentication"></a>Uwierzytelnianie podstawowe

Aby sprawdzić poprawność żądania przychodzące z aplikacji logiki do aplikacji sieci web lub aplikacji interfejsu API, można użyć uwierzytelnianie podstawowe, takie jak nazwa użytkownika i hasło. Uwierzytelnianie podstawowe jest to typowy wzorzec, a następnie można użyć tego uwierzytelnienia w dowolnym języku, używany do tworzenia aplikacji sieci web lub aplikacji interfejsu API.

W **autoryzacji** sekcji, podając ten wiersz:

`{"type": "basic", "username": "username", "password": "password"}`.

| Element | Wymagany | Opis | 
| ------- | -------- | ----------- | 
| type | Tak | Typ uwierzytelniania, którego chcesz używać. Dla uwierzytelniania podstawowego, wartość musi być `Basic`. | 
| username | Tak | Nazwa użytkownika, która ma być używany do uwierzytelniania | 
| password | Tak | Hasło, które ma być używany do uwierzytelniania | 
|||| 

<a name="azure-ad-code"></a>

#### <a name="azure-active-directory-authentication-through-code"></a>Usługa Azure uwierzytelniania usługi Active Directory za pomocą kodu

Domyślnie uwierzytelnianie usługi Azure AD, który można włączyć w witrynie Azure portal nie zawiera szczegółowych autoryzacji. Na przykład to uwierzytelnianie blokuje interfejsu API do określonej dzierżawy, a nie do określonego użytkownika lub aplikacji. 

Aby ograniczyć dostęp do interfejsu API do aplikacji logiki za pomocą kodu, Wyodrębnij nagłówek, który ma tokenu web JSON (JWT). Sprawdzanie tożsamości elementu wywołującego i odrzucanie żądań, które nie są zgodne.

<!-- Going further, to implement this authentication entirely in your own code, 
and not use the Azure portal, learn how to 
[authenticate with on-premises Active Directory in your Azure app](../app-service/overview-authentication-authorization.md).

To create an application identity for your logic app and use that identity to call your API, 
you must follow the previous steps. -->

## <a name="next-steps"></a>Kolejne kroki

* [Wdrażanie i wywoływanie niestandardowych interfejsów API od logiki przepływów pracy aplikacji](../logic-apps/logic-apps-custom-api-host-deploy-call.md)
