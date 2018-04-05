---
title: Użyj usługi Azure Active Directory do uwierzytelniania rozwiązania do zarządzania partii | Dokumentacja firmy Microsoft
description: Aplikacji skompilowanej za pomocą usługi Azure resource manager i dostawca zasobów partii uwierzytelniania za pomocą usługi Azure AD.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/27/2017
ms.author: danlep
ms.openlocfilehash: a16d60dd48efeced7735562884acd26640d36483
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="authenticate-batch-management-solutions-with-active-directory"></a>Uwierzytelnianie rozwiązań do zarządzania partii z usługą Active Directory

Aplikacje, które wywołują usługi zarządzania usługi partia zadań Azure uwierzytelniania za pomocą [usługi Azure Active Directory] [ aad_about] (Azure AD). Usługi Azure AD jest katalog wielu dzierżawców w chmurze firmy Microsoft i tożsamość usługi zarządzania. Azure sam używa usługi Azure AD do uwierzytelniania klientów, Administratorzy usługi i użytkowników w organizacji.

Biblioteka zarządzania partiami platformy .NET ujawnia typy do pracy z kontami partii, klucze konta, aplikacje i pakiety aplikacji. Biblioteka zarządzania partiami platformy .NET jest klientem dostawcy zasobów platformy Azure i jest używany razem z [usługi Azure Resource Manager] [ resman_overview] do programowego zarządzania tych zasobów. Usługi Azure AD jest wymagany do uwierzytelniania żądań wysyłanych za pomocą dowolnego klienta dostawcy zasobów platformy Azure, w tym biblioteki zarządzania partiami platformy .NET i za pośrednictwem [usługi Azure Resource Manager][resman_overview].

W tym artykule firma Microsoft eksplorowania przy użyciu usługi Azure AD do uwierzytelniania z aplikacji korzystających z biblioteki zarządzania partiami platformy .NET. Zostanie przedstawiony sposób uwierzytelniania administratorem subskrypcji lub administratora współpracującego przy użyciu zintegrowanego uwierzytelniania przy użyciu usługi Azure AD. Używamy [AccountManagment] [ acct_mgmt_sample] przykładowy projekt, dostępne w witrynie GitHub, aby zapoznać się z biblioteki zarządzania partiami platformy .NET przy użyciu usługi Azure AD.

Aby dowiedzieć się więcej na temat używania biblioteki zarządzania partiami platformy .NET i przykładowe AccountManagement, zobacz [partii Zarządzanie kontami i limitami przydziału z biblioteki klienta usługi partia zadań zarządzania dla programu .NET](batch-management-dotnet.md).

## <a name="register-your-application-with-azure-ad"></a>Zarejestrować aplikację w usłudze Azure AD

Azure [biblioteki uwierzytelniania usługi Active Directory] [ aad_adal] (ADAL), udostępnia interfejs programowe do usługi Azure AD do użycia w aplikacjach. Wywoływanie biblioteki ADAL z aplikacji, należy zarejestrować aplikację w dzierżawie usługi Azure AD. Podczas rejestrowania aplikacji, podanych usługi Azure AD z informacjami o aplikacji, łącznie z jej nazwę w ramach dzierżawy usługi Azure AD. Następnie usługi Azure AD zapewnia identyfikator aplikacji, która umożliwia kojarzenie aplikacji z usługą Azure AD w czasie wykonywania. Aby dowiedzieć się więcej na temat identyfikator aplikacji, zobacz [aplikacji i usług obiektów principal w usłudze Azure Active Directory](../active-directory/develop/active-directory-application-objects.md).

Aby zarejestrować AccountManagement przykładowej aplikacji, postępuj zgodnie z instrukcjami [Dodawanie aplikacji](../active-directory/develop/active-directory-integrating-applications.md#adding-an-application) sekcji [Integrowanie aplikacji z usługą Azure Active Directory][aad_integrate]. Określ **natywną aplikację kliencką** dla typu aplikacji. Branży standardowego protokołu OAuth 2.0 Identyfikator URI **identyfikator URI przekierowania** jest `urn:ietf:wg:oauth:2.0:oob`. Jednak można określić dowolny prawidłowy identyfikator URI (takich jak `http://myaccountmanagementsample`) dla **identyfikator URI przekierowania**, ponieważ nie musi być rzeczywistym punktu końcowego:

![](./media/batch-aad-auth-management/app-registration-management-plane.png)

Po zakończeniu procesu rejestracji, zostanie wyświetlony identyfikator aplikacji i identyfikator obiektu (nazwy głównej usługi) dla aplikacji.  

![](./media/batch-aad-auth-management/app-registration-client-id.png)

## <a name="grant-the-azure-resource-manager-api-access-to-your-application"></a>Udziel dostępu do interfejsu API usługi Azure Resource Manager do aplikacji

Następnie należy delegować dostęp do aplikacji interfejsu API Azure Resource Manager. Identyfikator usługi Azure AD dla interfejsu API Menedżera zasobów jest **interfejs API zarządzania usługami Windows Azure**.

Wykonaj następujące kroki w portalu Azure:

1. W okienku nawigacji po lewej stronie portalu Azure wybierz **wszystkie usługi**, kliknij przycisk **rejestracji aplikacji**i kliknij przycisk **Dodaj**.
2. Wyszukaj nazwę aplikacji na liście rejestracji aplikacji:

    ![Wyszukaj nazwę aplikacji](./media/batch-aad-auth-management/search-app-registration.png)

3. Wyświetl **ustawienia** bloku. W **dostępu do interfejsu API** zaznacz **wymagane uprawnienia**.
4. Kliknij przycisk **Dodaj** można dodać nowego wymaganych uprawnień. 
5. W kroku 1, wprowadź **interfejs API zarządzania usługami Windows Azure**, wybierz z listy wyników tego interfejsu API i kliknij przycisk **wybierz** przycisku.
6. W kroku 2, zaznacz pole wyboru **Access Azure klasycznego modelu wdrażania jako użytkowników w organizacji**i kliknij przycisk **wybierz** przycisku.
7. Kliknij przycisk **gotowe** przycisku.

**Wymagane uprawnienia** bloku teraz pokazuje, że do biblioteki ADAL i Menedżer zasobów interfejsów API zostały przyznane uprawnienia do aplikacji. Przyznano uprawnienia do biblioteki ADAL domyślnie podczas rejestrowania aplikacji z usługą Azure AD.

![Delegowanie uprawnień do interfejsu API Azure Resource Manager](./media/batch-aad-auth-management/required-permissions-management-plane.png)

## <a name="azure-ad-endpoints"></a>Punkty końcowe systemu Azure AD

Na potrzeby uwierzytelniania rozwiązań zarządzania partii z usługą Azure AD, konieczne będzie dwa punkty końcowe dobrze znany.

- **Wspólnego punktu końcowego usługi Azure AD** dostarcza poświadczenia ogólnego zbierania interfejsu po określonym dzierżawcy nie zostanie podany, jak w przypadku zintegrowanego uwierzytelniania:

    `https://login.microsoftonline.com/common`

- **Punktu końcowego usługi Azure Resource Manager** jest używany do uzyskiwania tokenu do uwierzytelniania żądań do usługi zarządzania partii:

    `https://management.core.windows.net/`

Przykładowa aplikacja AccountManagement definiuje stałe dla tych punktów końcowych. Pozostaw te stałe, bez zmian:

```csharp
// Azure Active Directory "common" endpoint.
private const string AuthorityUri = "https://login.microsoftonline.com/common";
// Azure Resource Manager endpoint 
private const string ResourceUri = "https://management.core.windows.net/";
```

## <a name="reference-your-application-id"></a>Odwołanie do Identyfikatora aplikacji 

Aplikacja kliencka używa Identyfikatora aplikacji (nazywane również identyfikator klienta) można uzyskać dostępu do usługi Azure AD w czasie wykonywania. Po aplikacji został zarejestrowany w portalu Azure, zaktualizuj kod, aby używał podany przez usługę Azure AD w zarejestrowany aplikacji identyfikator aplikacji. W przykładowej aplikacji AccountManagement skopiuj swój identyfikator aplikacji z portalu Azure do odpowiedniej stała:

```csharp
// Specify the unique identifier (the "Client ID") for your application. This is required so that your
// native client application (i.e. this sample) can access the Microsoft Azure AD Graph API. For information
// about registering an application in Azure Active Directory, please see "Adding an Application" here:
// https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
private const string ClientId = "<application-id>";
```
Kopiuje też przekierowania URI, które zostały określone podczas procesu rejestracji. Określone w kodzie identyfikator URI przekierowania musi być zgodna przekierowania URI podane podczas rejestrowania aplikacji.

```csharp
// The URI to which Azure AD will redirect in response to an OAuth 2.0 request. This value is
// specified by you when you register an application with AAD (see ClientId comment). It does not
// need to be a real endpoint, but must be a valid URI (e.g. https://accountmgmtsampleapp).
private const string RedirectUri = "http://myaccountmanagementsample";
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Uzyskanie tokenu uwierzytelniania usługi Azure AD

Po zarejestrować próbkę AccountManagement w dzierżawie usługi Azure AD i zaktualizować przykładowy kod źródłowy własnymi wartościami próbki jest gotowy do uwierzytelniania za pomocą usługi Azure AD. Po uruchomieniu próbki ADAL próbuje uzyskać tokenu uwierzytelniania. W tym kroku monituje o podanie poświadczeń Microsoft: 

```csharp
// Obtain an access token using the "common" AAD resource. This allows the application
// to query AAD for information that lies outside the application's tenant (such as for
// querying subscription information in your Azure account).
AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
AuthenticationResult authResult = authContext.AcquireToken(ResourceUri,
                                                        ClientId,
                                                        new Uri(RedirectUri),
                                                        PromptBehavior.Auto);
```

Po podaniu poświadczeń przykładowej aplikacji można przystąpić do wysyłania żądań uwierzytelnionych do usługi partia zadań zarządzania. 

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat uruchamiania [AccountManagement przykładowej aplikacji][acct_mgmt_sample], zobacz [partii Zarządzanie kontami i limitami przydziału z biblioteki klienta usługi partia zadań zarządzania dla programu .NET](batch-management-dotnet.md).

Aby dowiedzieć się więcej na temat usługi Azure AD, zobacz [Azure Active Directory dokumentacji](https://docs.microsoft.com/azure/active-directory/). Szczegółowe przykłady przedstawiająca sposób używania biblioteki ADAL są dostępne w [przykłady kodu Azure](https://azure.microsoft.com/resources/samples/?service=active-directory) biblioteki.

Na potrzeby uwierzytelniania partii usługi aplikacji za pomocą usługi Azure AD, zobacz [rozwiązań usług uwierzytelniania partii z usługą Active Directory](batch-aad-auth.md). 


[aad_about]: ../active-directory/active-directory-whatis.md "Co to jest usługa Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Scenariusze uwierzytelniania dla usługi Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrowanie aplikacji z usługą Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[azure_portal]: http://portal.azure.com
[resman_overview]: ../azure-resource-manager/resource-group-overview.md
