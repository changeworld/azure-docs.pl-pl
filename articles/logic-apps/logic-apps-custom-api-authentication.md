---
title: Dodawanie uwierzytelniania do niestandardowych interfejsów API - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Konfigurowanie uwierzytelniania w przypadku wywołań z niestandardowych interfejsów API aplikacji logiki
author: ecfan
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 705abb2a3cc25c965bdce364eb169b4e3a814bff
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298553"
---
# <a name="secure-calls-to-your-custom-apis-from-logic-apps"></a>Bezpieczne wywołania do Twojej niestandardowych interfejsów API z aplikacji logiki

Aby zabezpieczyć wywołań swoje interfejsy API, należy skonfigurować uwierzytelniania usługi Azure Active Directory (Azure AD) za pośrednictwem portalu Azure, nie trzeba zaktualizować kodu. Alternatywnie można wymagać i wymuszać uwierzytelnianie za pomocą kodu interfejsu API.

## <a name="authentication-options-for-your-api"></a>Opcje uwierzytelniania dla interfejsu API

Możesz zabezpieczyć wywołania do niestandardowego interfejsu API w następujący sposób:

* [Nie zmian w kodzie](#no-code): interfejs API ochrony [usługi Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md) za pośrednictwem portalu Azure, więc nie trzeba zaktualizować kodu lub ponownego wdrażania interfejsu API.

  > [!NOTE]
  > Domyślnie uwierzytelniania usługi Azure AD, który można włączyć w portalu Azure nie zapewnia precyzyjną autoryzacji. Na przykład to uwierzytelnianie blokuje interfejsu API do określonych dzierżawy, a nie do określonego użytkownika lub aplikacji. 

* [Zaktualizuj kod Twój interfejs API](#update-code): ochrona interfejsu API przez wymuszenie [uwierzytelnianie certyfikatu](#certificate), [uwierzytelnianie podstawowe](#basic), lub [uwierzytelniania usługi Azure AD](#azure-ad-code) za pośrednictwem Kod.

<a name="no-code"></a>

### <a name="authenticate-calls-to-your-api-without-changing-code"></a>Uwierzytelnianie wywołań interfejsu API bez zmiany kodu

Poniżej przedstawiono ogólne kroki dla tej metody:

1. Utwórz dwa tożsamości aplikacji usługi Azure Active Directory (Azure AD): jeden dla aplikacji logiki i jeden dla aplikacji sieci web (lub aplikacji interfejsu API).

2. Na potrzeby uwierzytelniania wywołań interfejsu API, należy używać poświadczeń (identyfikator klienta i klucz tajny) dla nazwy głównej usługi, skojarzoną z tożsamość aplikacji usługi Azure AD dla aplikacji logiki.

3. Zawiera identyfikatorów aplikacji w definicji aplikacji logiki.

#### <a name="part-1-create-an-azure-ad-application-identity-for-your-logic-app"></a>Część 1: Tworzenie aplikacji logiki tożsamość aplikacji usługi Azure AD

Aplikację logiki używa tego tożsamość aplikacji usługi Azure AD do uwierzytelniania usługi Azure AD. Wystarczy tylko skonfigurować tej tożsamości jeden raz dla katalogu. Na przykład możesz użyć tej samej tożsamości dla wszystkich aplikacji logiki, mimo że można utworzyć unikatowych tożsamości dla każdej aplikacji logiki. Możesz skonfigurować te tożsamości w portalu Azure lub użyć [PowerShell](#powershell).

**Tworzenie aplikacji logiki tożsamości aplikacji w portalu Azure**

1. W [portalu Azure](https://portal.azure.com "https://portal.azure.com"), wybierz **usługi Azure Active Directory**. 

2. Upewnij się, że jesteś w tym samym katalogu co aplikację sieci web lub aplikacji interfejsu API.

   > [!TIP]
   > Aby przełączyć katalogów, wybierz profil, a następnie wybierz inny katalog. Lub wybierz **omówienie** > **katalogu przełącznika**.

3. W menu katalogu w obszarze **Zarządzaj**, wybierz **rejestracji aplikacji** > **nowej rejestracji aplikacji**.

   > [!TIP]
   > Domyślnie lista rejestracji aplikacji zawiera wszystkich rejestracji aplikacji w katalogu. Aby wyświetlić tylko rejestracji aplikacji, w polu wyszukiwania, wybierz **Moje aplikacje**. 

   ![Tworzenie nowej rejestracji aplikacji](./media/logic-apps-custom-api-authentication/new-app-registration-azure-portal.png)

4. Nadaj nazwę tożsamości aplikacji, pozostaw **typu aplikacji** ustawioną **aplikacji sieci Web / interfejs API**, Podaj unikatową ciąg w formacie domeny dla **adres URL logowania**i wybierz polecenie **Utwórz**.

   ![Podaj nazwę i logowania jednokrotnego adres URL dla tożsamości aplikacji](./media/logic-apps-custom-api-authentication/logic-app-identity-azure-portal.png)

   Tożsamość aplikacji utworzonej dla aplikacji logiki teraz zostanie wyświetlony na liście rejestracji aplikacji.

   ![Tożsamość aplikacji dla aplikacji logiki](./media/logic-apps-custom-api-authentication/logic-app-identity-created.png)

5. Na liście rejestracji aplikacji wybierz nowy identyfikator aplikacji. Skopiuj i Zapisz **identyfikator aplikacji** do użycia jako "Identyfikator klienta" aplikację logiki w część 3.

   ![Skopiuj i Zapisz identyfikator aplikacji dla aplikacji logiki](./media/logic-apps-custom-api-authentication/logic-app-application-id.png)

6. Jeśli ustawienia tożsamość aplikacji nie są widoczne, wybierz **ustawienia** lub **wszystkie ustawienia**.

7. W obszarze **dostępu do interfejsu API**, wybierz **klucze**. W obszarze **opis**, podaj nazwę klucza. W obszarze **Expires**, wybierz czas trwania dla klucza.

   Klucz, który tworzysz działa jako tożsamość aplikacji "tajny" lub hasła do aplikacji logiki.

   ![Utwórz klucz tożsamości aplikacji logiki](./media/logic-apps-custom-api-authentication/create-logic-app-identity-key-secret-password.png)

8. Na pasku narzędzi wybierz **zapisać**. W obszarze **wartość**, klucz zostanie wyświetlone. 
**Upewnij się, że skopiuj i Zapisz klucz** do późniejszego użycia, ponieważ jest ukryta klucz wychodząc **klucze** strony.

   Podczas konfigurowania aplikacji logiki w część 3, należy określić ten klucz jako "secret" lub hasło.

   ![Skopiuj i Zapisz klucz do użycia później](./media/logic-apps-custom-api-authentication/logic-app-copy-key-secret-password.png)

<a name="powershell"></a>

**Tworzenie aplikacji logiki tożsamości aplikacji w programie PowerShell**

Można wykonać tego zadania za pomocą usługi Azure Resource Manager przy użyciu programu PowerShell. W programie PowerShell uruchom następujące polecenia:

1. `Switch-AzureMode AzureResourceManager`

2. `Add-AzureAccount`

3. `New-AzureADApplication -DisplayName "MyLogicAppID" -HomePage "http://mydomain.tld" -IdentifierUris "http://mydomain.tld" -Password "identity-password"`

4. Upewnij się skopiować **identyfikator dzierżawcy** (identyfikator GUID dla dzierżawy usługi Azure AD), **identyfikator aplikacji**i hasła, który został użyty.

Aby uzyskać więcej informacji, Dowiedz się, jak [Tworzenie nazwy głównej usługi przy użyciu programu PowerShell dostęp do zasobów](../azure-resource-manager/resource-group-authenticate-service-principal.md).

#### <a name="part-2-create-an-azure-ad-application-identity-for-your-web-app-or-api-app"></a>Część 2: Tworzenie tożsamość aplikacji usługi Azure AD dla aplikacji sieci web lub aplikacji interfejsu API

Jeśli aplikację sieci web lub aplikacji interfejsu API jest już wdrożone, można włączyć uwierzytelnianie i utworzenia tożsamości aplikacji w portalu Azure. W przeciwnym razie można [włączyć uwierzytelnianie podczas wdrażania z szablonem usługi Azure Resource Manager](#authen-deploy). 

**Utwórz tożsamość aplikacji i włączyć uwierzytelnianie w portalu Azure w przypadku wdrożonej aplikacji**

1. W [portalu Azure](https://portal.azure.com "https://portal.azure.com"), Znajdź i wybierz aplikację sieci web lub aplikacji interfejsu API. 

2. W obszarze **ustawienia**, wybierz **uwierzytelniania/autoryzacji**. W obszarze **aplikacji uwierzytelniania usługi**, Włącz uwierzytelnianie **na**. W obszarze **dostawców uwierzytelniania**, wybierz **usługi Azure Active Directory**.

   ![Włączanie uwierzytelniania](./media/logic-apps-custom-api-authentication/custom-web-api-app-authentication.png)

3. Teraz należy utworzyć tożsamość aplikacji dla aplikacji sieci web lub aplikacji interfejsu API, jak pokazano poniżej. Na **ustawień usługi Azure Active Directory** ustaw **tryb zarządzania** do **Express**. Wybierz **Utwórz nową aplikację usługi AD**. Nadaj nazwę tożsamości aplikacji, a następnie wybierz pozycję **OK**. 

   ![Utwórz tożsamość aplikacji dla aplikacji sieci web lub aplikacji interfejsu API](./media/logic-apps-custom-api-authentication/custom-api-application-identity.png)

4. Na stronie **Uwierzytelnianie/autoryzacja** wybierz pozycję **Zapisz**.

Teraz należy znaleźć tożsamości aplikacji, który został skojarzony z aplikacji sieci web lub aplikacji interfejsu API klienta ID oraz Identyfikatora dzierżawcy. Należy użyć tych identyfikatorów w część 3. Więc wykonaj następujące kroki w portalu Azure.

**Znajdź identyfikator klienta i Identyfikatora dzierżawcy tożsamość aplikacji dla aplikacji sieci web lub aplikacji interfejsu API w portalu Azure**

1. W obszarze **dostawców uwierzytelniania**, wybierz **usługi Azure Active Directory**. 

   ![Wybieranie pozycji „Azure Active Directory”](./media/logic-apps-custom-api-authentication/custom-api-app-identity-client-id-tenant-id.png)

2. Na **ustawień usługi Azure Active Directory** ustaw **tryb zarządzania** do **zaawansowane**.

3. Kopiuj **identyfikator klienta**i Zapisz ten identyfikator GUID do użycia w część 3.

   > [!TIP] 
   > Jeśli **identyfikator klienta** i **adres Url wystawcy** nie są wyświetlane, spróbuj odświeżyć portalu Azure i powtórz krok 1.

4. W obszarze **adres Url wystawcy**, skopiuj i Zapisz tylko identyfikator GUID dla część 3. Umożliwia także ten identyfikator GUID w aplikacji sieci web lub w szablonie wdrożenia aplikacji interfejsu API, jeśli to konieczne.

   Ten identyfikator GUID jest identyfikatorem GUID dzierżawy określonych ("identyfikator dzierżawy") i powinna być widoczna w tym adresem URL: `https://sts.windows.net/{GUID}`

5. Bez zapisywania zmian, Zamknij **ustawień usługi Azure Active Directory** strony.

<a name="authen-deploy"></a>

**Włącz uwierzytelnianie podczas wdrażania z szablonem usługi Azure Resource Manager**

Nadal należy utworzyć tożsamość aplikacji usługi Azure AD dla aplikacji sieci web lub aplikacji interfejsu API, która różni się od tożsamości aplikacji dla aplikacji logiki. Aby utworzyć tożsamość aplikacji, wykonaj poprzednie kroki w część 2 dla portalu Azure. 

Możesz również wykonaj kroki opisane w części 1, ale pamiętaj korzystać z aplikacji sieci web lub aplikacji interfejsu API rzeczywiste `https://{URL}` dla **adres URL logowania** i **identyfikator URI aplikacji**. Z tych kroków należy zapisać identyfikator klienta i identyfikator dzierżawcy do użytku w szablonie wdrożenia aplikacji, a także dla część 3.

> [!NOTE]
> Podczas tworzenia tożsamość aplikacji usługi Azure AD dla twojej aplikacji sieci web lub aplikacji interfejsu API, musisz użyć portalu Azure, a nie programu PowerShell. Polecenia programu PowerShell nie skonfigurować wymagane uprawnienia do logowania się użytkowników do witryny sieci Web.

Po pobraniu klienta identyfikator i identyfikator dzierżawy obejmują tych identyfikatorów jako subresource aplikacji sieci web lub aplikacji interfejsu API w szablonie wdrożenia:

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

Automatyczne wdrażanie aplikacji sieci web puste i aplikacji logiki wraz z uwierzytelniania usługi Azure Active Directory [wyświetlić pełną szablonu tutaj](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-custom-api/azuredeploy.json), lub kliknij przycisk **wdrażanie na platformie Azure** tutaj:

[![Wdrażanie na platformie Azure](media/logic-apps-custom-api-authentication/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

#### <a name="part-3-populate-the-authorization-section-in-your-logic-app"></a>Część 3: Wypełnić sekcję autoryzacji w aplikacji logiki

Poprzedni szablon jest już w tej sekcji autoryzacji, konfigurowanie, ale są bezpośrednie tworzenie aplikacji logiki, należy dołączyć sekcji pełne autoryzacji.

Otwórz definicję aplikacji logiki w widoku kodu, przejdź do **HTTP** sekcji Akcja znaleźć **autoryzacji** , a następnie Dołącz tę linię:

`{"tenant": "{tenant-ID}", "audience": "{client-ID-from-Part-2-web-app-or-API app}", "clientId": "{client-ID-from-Part-1-logic-app}", "secret": "{key-from-Part-1-logic-app}", "type": "ActiveDirectoryOAuth" }`

| Element | Wymagane | Opis | 
| ------- | -------- | ----------- | 
| dzierżawa | Yes | Identyfikator GUID dzierżawy usługi Azure AD | 
| grupy odbiorców | Yes | Identyfikator GUID zasobu docelowego, który chcesz uzyskać dostęp, czyli identyfikator klienta z tożsamości aplikacji dla aplikacji sieci web lub aplikacji interfejsu API | 
| clientId | Yes | Identyfikator GUID dla klienta żąda dostępu, który jest identyfikator klienta z tożsamość aplikacji dla aplikacji logiki | 
| wpis tajny | Yes | Klucz lub hasło z tożsamość aplikacji, gdy klient żąda token dostępu | 
| type | Yes | Typ uwierzytelniania. Wartość dla uwierzytelniania ActiveDirectoryOAuth jest `ActiveDirectoryOAuth`. | 
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

### <a name="secure-api-calls-through-code"></a>Bezpieczne wywołania interfejsu API przy użyciu kodu

<a name="certificate"></a>

#### <a name="certificate-authentication"></a>Uwierzytelnianie certyfikatów

Aby sprawdzić poprawność żądań przychodzących od aplikacji logiki do aplikacji sieci web lub aplikacji interfejsu API, można użyć certyfikatów klienta. Aby skonfigurować swój kod, Dowiedz się [jak skonfigurować uwierzytelnianie wzajemne TLS](../app-service/app-service-web-configure-tls-mutual-auth.md).

W **autoryzacji** sekcji, Dołącz tę linię: 

`{"type": "clientcertificate", "password": "password", "pfx": "long-pfx-key"}`

| Element | Wymagane | Opis | 
| ------- | -------- | ----------- | 
| type | Yes | Typ uwierzytelniania. Dla certyfikatów klienta SSL, wartość musi być `ClientCertificate`. | 
| hasło | Yes | Hasło do uzyskiwania dostępu do certyfikatu klienta (plik PFX) | 
| PFX | Yes | Zawartość algorytmem Base64 certyfikatu klienta (plik PFX) | 
|||| 

<a name="basic"></a>

#### <a name="basic-authentication"></a>Uwierzytelnianie podstawowe

Można sprawdzić poprawności przychodzących żądań od aplikacji logiki do aplikacji sieci web lub aplikacji interfejsu API, można użyć uwierzytelnianie podstawowe, takie jak nazwa użytkownika i hasło. Uwierzytelnianie podstawowe jest wspólnego wzorca i może uwierzytelnianie w dowolnym języku używanym do budowania aplikację sieci web lub aplikacji interfejsu API.

W **autoryzacji** sekcji, Dołącz tę linię:

`{"type": "basic", "username": "username", "password": "password"}`.

| Element | Wymagane | Opis | 
| ------- | -------- | ----------- | 
| type | Yes | Typ uwierzytelniania, którego chcesz używać. W przypadku uwierzytelniania podstawowego musi być wartością `Basic`. | 
| nazwa użytkownika | Yes | Nazwa użytkownika, która ma być używany do uwierzytelniania | 
| hasło | Yes | Hasło, które ma być używany do uwierzytelniania | 
|||| 

<a name="azure-ad-code"></a>

#### <a name="azure-active-directory-authentication-through-code"></a>Azure uwierzytelniania usługi Active Directory przy użyciu kodu

Domyślnie uwierzytelniania usługi Azure AD, który można włączyć w portalu Azure nie zapewnia precyzyjną autoryzacji. Na przykład to uwierzytelnianie blokuje interfejsu API do określonych dzierżawy, a nie do określonego użytkownika lub aplikacji. 

Aby ograniczyć dostęp do interfejsu API do aplikacji logiki do kodu, Wyodrębnij nagłówek, który ma tokenu web JSON (JWT). Sprawdź tożsamość obiektu wywołującego i odrzucanie żądań, które nie są zgodne.

<!-- Going further, to implement this authentication entirely in your own code, 
and not use the Azure portal, learn how to 
[authenticate with on-premises Active Directory in your Azure app](../app-service/app-service-authentication-overview.md).

To create an application identity for your logic app and use that identity to call your API, 
you must follow the previous steps. -->

## <a name="next-steps"></a>Kolejne kroki

* [Wdrażanie i wywoływania niestandardowych interfejsów API z logiki przepływów pracy aplikacji](../logic-apps/logic-apps-custom-api-host-deploy-call.md)