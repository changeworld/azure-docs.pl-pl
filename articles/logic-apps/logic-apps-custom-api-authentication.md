---
title: Dodawanie uwierzytelniania do zabezpieczania wywołań do niestandardowych interfejsów API
description: Jak skonfigurować uwierzytelnianie w celu zwiększenia zabezpieczeń dla wywołań niestandardowych interfejsów API z usługi Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 09/22/2017
ms.openlocfilehash: 110a684cf6ad21c13411d3bc2ada84750744f00e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191406"
---
# <a name="increase-security-for-calls-to-custom-apis-from-azure-logic-apps"></a>Zwiększ bezpieczeństwo wywołań niestandardowych interfejsów API z usługi Azure Logic Apps

Aby zwiększyć bezpieczeństwo wywołań interfejsów API, można skonfigurować uwierzytelnianie usługi Azure Active Directory (Azure AD) za pośrednictwem witryny Azure portal, dzięki czemu nie trzeba aktualizować kodu. Alternatywnie można wymagać i wymuszać uwierzytelnianie za pomocą kodu interfejsu API.

## <a name="authentication-options-for-your-api"></a>Opcje uwierzytelniania interfejsu API

Można zwiększyć bezpieczeństwo wywołań niestandardowego interfejsu API w następujących sposób:

* [Brak zmian w kodzie:](#no-code)Chroń interfejs API za pomocą [usługi Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) za pośrednictwem witryny Azure Portal, więc nie trzeba aktualizować kodu ani ponownie wdrożyć interfejsu API.

  > [!NOTE]
  > Domyślnie uwierzytelnianie usługi Azure AD, które można włączyć w witrynie Azure portal nie zapewnia szczegółowej autoryzacji. Na przykład to uwierzytelnianie blokuje interfejs API tylko do określonej dzierżawy, a nie do określonego użytkownika lub aplikacji. 

* [Zaktualizuj kod interfejsu API:](#update-code)Chroń swój interfejs API, wymuszając [uwierzytelnianie certyfikatu,](#certificate) [uwierzytelnianie podstawowe](#basic)lub [uwierzytelnianie usługi Azure AD](#azure-ad-code) za pomocą kodu.

<a name="no-code"></a>

### <a name="authenticate-calls-to-your-api-without-changing-code"></a>Uwierzytelnij wywołania do interfejsu API bez zmiany kodu

Oto ogólne kroki dla tej metody:

1. Utwórz dwie tożsamości aplikacji usługi Azure Active Directory (Azure AD): jedną dla aplikacji logiki i jedną dla aplikacji sieci Web (lub aplikacji interfejsu API).

2. Aby uwierzytelnić wywołania do interfejsu API, użyj poświadczeń (identyfikator klienta i klucz tajny) dla jednostki usługi skojarzonej z tożsamością aplikacji usługi Azure AD dla aplikacji logiki.

3. Uwzględnij identyfikatory aplikacji w definicji aplikacji logiki.

#### <a name="part-1-create-an-azure-ad-application-identity-for-your-logic-app"></a>Część 1: Tworzenie tożsamości aplikacji usługi Azure AD dla aplikacji logiki

Aplikacja logiki używa tej tożsamości aplikacji usługi Azure AD do uwierzytelniania za pomocą usługi Azure AD. Tę tożsamość należy skonfigurować tylko raz dla katalogu. Na przykład można użyć tej samej tożsamości dla wszystkich aplikacji logiki, nawet jeśli można utworzyć unikatowe tożsamości dla każdej aplikacji logiki. Te tożsamości można skonfigurować w witrynie Azure portal lub użyć programu [PowerShell](#powershell).

**Tworzenie tożsamości aplikacji dla aplikacji logiki w witrynie Azure portal**

1. W [witrynie Azure portal](https://portal.azure.com "https://portal.azure.com")wybierz pozycję **Azure Active Directory**. 

2. Upewnij się, że jesteś w tym samym katalogu co aplikacja sieci web lub aplikacja interfejsu API.

   > [!TIP]
   > Aby przełączyć katalogi, wybierz swój profil i wybierz inny katalog. Możesz też wybrać **pozycję Katalog przełączania przeglądu** > **Switch directory**.

3. W menu katalogu w obszarze **Zarządzaj**wybierz pozycję **Rejestracje** > aplikacji**Nowa rejestracja aplikacji**.

   > [!TIP]
   > Domyślnie na liście rejestracji aplikacji są wyświetlane wszystkie rejestracje aplikacji w katalogu. Aby wyświetlić tylko rejestracje aplikacji, obok pola wyszukiwania wybierz **pozycję Moje aplikacje**. 

   ![Tworzenie rejestracji nowej aplikacji](./media/logic-apps-custom-api-authentication/new-app-registration-azure-portal.png)

4. Nadaj tożsamości aplikacji nazwę, pozostaw **typ aplikacji** ustawiony na aplikację / **INTERFEJS API,** podaj unikatowy ciąg sformatowany jako domena dla **adresu URL logowania**i wybierz pozycję **Utwórz**.

   ![Podaj nazwę i adres URL logowania dla tożsamości aplikacji](./media/logic-apps-custom-api-authentication/logic-app-identity-azure-portal.png)

   Tożsamość aplikacji utworzoną dla aplikacji logiki jest teraz wyświetlana na liście rejestracji aplikacji.

   ![Tożsamość aplikacji dla aplikacji logiki](./media/logic-apps-custom-api-authentication/logic-app-identity-created.png)

5. Na liście rejestracji aplikacji wybierz nową tożsamość aplikacji. Skopiuj i zapisz **identyfikator aplikacji** do użycia jako "identyfikator klienta" dla aplikacji logiki w części 3.

   ![Kopiowanie i zapisywanie identyfikatora aplikacji dla aplikacji logiki](./media/logic-apps-custom-api-authentication/logic-app-application-id.png)

6. Jeśli ustawienia tożsamości aplikacji nie są widoczne, wybierz pozycję **Ustawienia** lub **Wszystkie ustawienia**.

7. W obszarze **Dostęp do interfejsu API**wybierz pozycję **Klawisze**. W **obszarze Opis**podaj nazwę klucza. W obszarze **Wygasa**wybierz czas trwania klucza.

   Klucz, który tworzysz działa jako "tajny" tożsamości aplikacji lub hasło dla aplikacji logiki.

   ![Tworzenie klucza dla tożsamości aplikacji logiki](./media/logic-apps-custom-api-authentication/create-logic-app-identity-key-secret-password.png)

8. Na pasku narzędzi wybierz pozycję **Zapisz**. W obszarze **Wartość**pojawi się teraz klucz. 
**Pamiętaj, aby skopiować i zapisać klucz** do późniejszego użycia, ponieważ klucz jest ukryty po opuszczeniu strony **Klucze.**

   Podczas konfigurowania aplikacji logiki w części 3, należy określić ten klucz jako "tajny" lub hasło.

   ![Kopiowanie i zapisywanie klucza na później](./media/logic-apps-custom-api-authentication/logic-app-copy-key-secret-password.png)

<a name="powershell"></a>

**Tworzenie tożsamości aplikacji dla aplikacji logiki w programie PowerShell**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

To zadanie można wykonać za pośrednictwem usługi Azure Resource Manager za pomocą programu PowerShell. W programie PowerShell uruchom następujące polecenia:

1. `Add-AzAccount`

1. `$SecurePassword = Read-Host -AsSecureString`

1. Wprowadź hasło i naciśnij klawisz Enter.

1. `New-AzADApplication -DisplayName "MyLogicAppID" -HomePage "http://mydomain.tld" -IdentifierUris "http://mydomain.tld" -Password $SecurePassword`

1. Upewnij się, aby skopiować **identyfikator dzierżawy** (GUID dla dzierżawy usługi Azure AD), **identyfikator aplikacji**i hasło, którego użyto.

Aby uzyskać więcej informacji, dowiedz się, jak [utworzyć jednostkę usługi za pomocą programu PowerShell, aby uzyskać dostęp do zasobów.](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

#### <a name="part-2-create-an-azure-ad-application-identity-for-your-web-app-or-api-app"></a>Część 2: Tworzenie tożsamości aplikacji usługi Azure AD dla aplikacji sieci Web lub aplikacji interfejsu API

Jeśli aplikacja sieci web lub aplikacja interfejsu API jest już wdrożona, możesz włączyć uwierzytelnianie i utworzyć tożsamość aplikacji w witrynie Azure portal. W przeciwnym razie można [włączyć uwierzytelnianie podczas wdrażania za pomocą szablonu usługi Azure Resource Manager](#authen-deploy). 

**Tworzenie tożsamości aplikacji i włączanie uwierzytelniania w portalu Azure dla wdrożonych aplikacji**

1. W [witrynie Azure portal](https://portal.azure.com "https://portal.azure.com")znajdź i wybierz aplikację sieci Web lub aplikację interfejsu API. 

2. W obszarze **Ustawienia**wybierz **pozycję Uwierzytelnianie/Autoryzacja**. W obszarze **Uwierzytelnianie usługi aplikacji**włącz **uwierzytelnianie**. W obszarze **Dostawcy uwierzytelniania**wybierz pozycję **Azure Active Directory**.

   ![Włączanie uwierzytelniania](./media/logic-apps-custom-api-authentication/custom-web-api-app-authentication.png)

3. Teraz utwórz tożsamość aplikacji dla aplikacji sieci web lub aplikacji interfejsu API, jak pokazano tutaj. Na stronie **Ustawienia usługi Azure Active Directory** ustaw tryb **zarządzania** na **Express**. Wybierz pozycję **Utwórz nową aplikację AD**. Nadaj swojej tożsamości aplikacji nazwę i wybierz **przycisk OK**. 

   ![Tworzenie tożsamości aplikacji dla aplikacji sieci Web lub aplikacji interfejsu API](./media/logic-apps-custom-api-authentication/custom-api-application-identity.png)

4. Na stronie **Uwierzytelnianie/autoryzacja** wybierz pozycję **Zapisz**.

Teraz musisz znaleźć identyfikator klienta i identyfikator dzierżawy dla tożsamości aplikacji skojarzonej z aplikacją sieci web lub aplikacją interfejsu API. Te identyfikatory są używane w części 3. Wykonaj te kroki dla witryny Azure portal.

**Znajdowanie identyfikatora klienta tożsamości aplikacji i identyfikatora dzierżawy aplikacji sieci Web lub aplikacji interfejsu API w witrynie Azure portal**

1. W obszarze **Dostawcy uwierzytelniania**wybierz pozycję **Azure Active Directory**. 

   ![Wybieranie pozycji „Azure Active Directory”](./media/logic-apps-custom-api-authentication/custom-api-app-identity-client-id-tenant-id.png)

2. Na stronie **Ustawienia usługi Azure Active Directory** ustaw tryb **zarządzania** na **Zaawansowane**.

3. Skopiuj **identyfikator klienta**i zapisz ten identyfikator GUID do użycia w części 3.

   > [!TIP] 
   > Jeśli **identyfikator klienta** i adres URL **wystawcy** nie są wyświetlane, spróbuj odświeżyć witrynę Azure portal i powtórz krok 1.

4. W obszarze **Adres URL wystawcy**skopiuj i zapisz tylko identyfikator GUID dla części 3. Możesz również użyć tego identyfikatora GUID w szablonie wdrażania aplikacji sieci web lub aplikacji interfejsu API, jeśli to konieczne.

   Ten identyfikator GUID jest identyfikatorem GUID określonej dzierżawy ("identyfikator dzierżawy") i powinien być wyświetlany w tym adresie URL:`https://sts.windows.net/{GUID}`

5. Bez zapisywania zmian zamknij stronę **Ustawienia usługi Azure Active Directory.**

<a name="authen-deploy"></a>

**Włączanie uwierzytelniania podczas wdrażania za pomocą szablonu usługi Azure Resource Manager**

Nadal musisz utworzyć tożsamość aplikacji usługi Azure AD dla aplikacji sieci web lub aplikacji interfejsu API, która różni się od tożsamości aplikacji dla aplikacji logiki. Aby utworzyć tożsamość aplikacji, wykonaj poprzednie kroki w części 2 dla witryny Azure portal. 

Możesz również wykonać kroki opisane w części 1, ale upewnij się, `https://{URL}` że używasz aplikacji internetowej lub aplikacji interfejsu API rzeczywistego dla **adresu URL logowania** i **identyfikatora aplikacji URI**. W tych krokach należy zapisać zarówno identyfikator klienta, jak i identyfikator dzierżawy do użycia w szablonie wdrażania aplikacji, a także w części 3.

> [!NOTE]
> Podczas tworzenia tożsamości aplikacji usługi Azure AD dla aplikacji sieci web lub aplikacji interfejsu API, należy użyć witryny Azure portal, a nie programu PowerShell. Polecenie programu PowerShell nie skonfiguruje wymaganych uprawnień do logowania użytkowników w witrynie sieci Web.

Po otrzymasz identyfikator klienta i identyfikator dzierżawy, uwzględnij te identyfikatory jako pododdział aplikacji sieci web lub aplikacji interfejsu API w szablonie wdrożenia:

``` json
"resources": [ 
   {
      "apiVersion": "2015-08-01",
      "name": "web",
      "type": "config",
      "dependsOn": ["[concat('Microsoft.Web/sites/','parameters('webAppName'))]"],
      "properties": {
         "siteAuthEnabled": true,
         "siteAuthSettings": {
            "clientId": "<client-ID>",
            "issuer": "https://sts.windows.net/<tenant-ID>/"
         }
      }
   } 
]
```

Aby automatycznie wdrożyć pustą aplikację sieci web i aplikację logiki wraz z uwierzytelnianiem usługi Azure Active Directory, [wyświetl pełny szablon tutaj](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-custom-api/azuredeploy.json)lub kliknij przycisk **Wdrażanie na platformie Azure** tutaj:

[![Wdrażanie na platformie Azure](media/logic-apps-custom-api-authentication/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

#### <a name="part-3-populate-the-authorization-section-in-your-logic-app"></a>Część 3: Wypełnij sekcję Autoryzacja w aplikacji logiki

Poprzedni szablon ma już skonfigurowany ten rozdział autoryzacji, ale jeśli bezpośrednio autoring aplikacji logiki, należy dołączyć pełną sekcję autoryzacji.

Otwórz definicję aplikacji logiki w widoku kodu, przejdź do definicji akcji **HTTP,** znajdź sekcję **Autoryzacja** i uwzględnij następujące właściwości:

```json
{
   "tenant": "<tenant-ID>",
   "audience": "<client-ID-from-Part-2-web-app-or-API app>", 
   "clientId": "<client-ID-from-Part-1-logic-app>",
   "secret": "<key-from-Part-1-logic-app>", 
   "type": "ActiveDirectoryOAuth"
}
```

| Właściwość | Wymagany | Opis | 
| -------- | -------- | ----------- | 
| Dzierżawy | Tak | Identyfikator GUID dla dzierżawy usługi Azure AD | 
| Publiczności | Tak | Identyfikator GUID dla zasobu docelowego, do którego chcesz uzyskać dostęp, czyli identyfikator klienta z tożsamości aplikacji dla aplikacji sieci Web lub aplikacji interfejsu API | 
| clientId | Tak | Identyfikator GUID dla klienta żądającego dostępu, który jest identyfikatorem klienta z tożsamości aplikacji dla aplikacji logiki | 
| wpis tajny | Tak | Klucz lub hasło z tożsamości aplikacji dla klienta, który żąda tokenu dostępu | 
| type | Tak | Typ uwierzytelniania. W przypadku uwierzytelniania ActiveDirectoryOAuth wartość to `ActiveDirectoryOAuth`. | 
|||| 

Przykład:

``` json
{
   "actions": {
      "HTTP": {
         "inputs": {
            "method": "POST",
            "uri": "https://your-api-azurewebsites.net/api/your-method",
            "authentication": {
               "tenant": "tenant-ID",
               "audience": "client-ID-from-azure-ad-app-for-web-app-or-api-app",
               "clientId": "client-ID-from-azure-ad-app-for-logic-app",
               "secret": "key-from-azure-ad-app-for-logic-app",
               "type": "ActiveDirectoryOAuth"
            }
         }
      }
   }
}
```

<a name="update-code"></a>

### <a name="secure-api-calls-through-code"></a>Bezpieczne wywołania interfejsu API za pomocą kodu

<a name="certificate"></a>

#### <a name="certificate-authentication"></a>Uwierzytelnianie certyfikatu

Aby sprawdzić poprawność żądań przychodzących z aplikacji logiki do aplikacji sieci web lub aplikacji interfejsu API, można użyć certyfikatów klienta. Aby skonfigurować kod, dowiedz się, [jak skonfigurować wzajemne uwierzytelnianie TLS](../app-service/app-service-web-configure-tls-mutual-auth.md).

W sekcji **Autoryzacja** uwzględnij następujące właściwości:

```json
{
   "type": "ClientCertificate",
   "password": "<password>",
   "pfx": "<long-pfx-key>"
} 
```

| Właściwość | Wymagany | Opis |
| -------- | -------- | ----------- |
| `type` | Tak | Typ uwierzytelniania. W przypadku certyfikatów klienta SSL wartość musi być `ClientCertificate`. |
| `password` | Nie | Hasło dostępu do certyfikatu klienta (plik PFX) |
| `pfx` | Tak | Zakodowana w bazie zawartość certyfikatu klienta (plik PFX) |
||||

<a name="basic"></a>

#### <a name="basic-authentication"></a>Uwierzytelnianie podstawowe

Aby sprawdzić poprawność żądań przychodzących z aplikacji logiki do aplikacji sieci web lub aplikacji interfejsu API, można użyć uwierzytelniania podstawowego, takiego jak nazwa użytkownika i hasło. Uwierzytelnianie podstawowe jest typowym wzorcem i można użyć tego uwierzytelniania w dowolnym języku używanym do tworzenia aplikacji sieci web lub aplikacji interfejsu API.

W sekcji **Autoryzacja** uwzględnij następujące właściwości:

```json
{
   "type": "Basic",
   "username": "<username>",
   "password": "<password>"
}
```

| Właściwość | Wymagany | Opis | 
| -------- | -------- | ----------- | 
| type | Tak | Typ uwierzytelniania, którego chcesz użyć. W przypadku uwierzytelniania podstawowego wartość musi być `Basic`. | 
| nazwa użytkownika | Tak | Nazwa użytkownika, której chcesz użyć do uwierzytelniania | 
| hasło | Tak | Hasło, którego chcesz użyć do uwierzytelniania | 
|||| 

<a name="azure-ad-code"></a>

#### <a name="azure-active-directory-authentication-through-code"></a>Uwierzytelnianie usługi Azure Active Directory za pomocą kodu

Domyślnie uwierzytelnianie usługi Azure AD, które można włączyć w witrynie Azure portal nie zapewnia szczegółowej autoryzacji. Na przykład to uwierzytelnianie blokuje interfejs API tylko do określonej dzierżawy, a nie do określonego użytkownika lub aplikacji. 

Aby ograniczyć dostęp interfejsu API do aplikacji logiki za pomocą kodu, wyodrębnić nagłówek, który ma token internetowy JSON (JWT). Sprawdź tożsamość osoby dzwoniącej i odrzuć żądania, które nie są zgodne.

<!-- Going further, to implement this authentication entirely in your own code, 
and not use the Azure portal, learn how to 
[authenticate with on-premises Active Directory in your Azure app](../app-service/overview-authentication-authorization.md).

To create an application identity for your logic app and use that identity to call your API, 
you must follow the previous steps. -->

## <a name="next-steps"></a>Następne kroki

* [Wdrażanie i wywoływanie niestandardowych interfejsów API z przepływów pracy aplikacji logiki](../logic-apps/logic-apps-custom-api-host-deploy-call.md)
