---
title: Usługa Azure Active Directory do uwierzytelniania rozwiązań do zarządzania usługi Batch | Dokumentacja firmy Microsoft
description: Uwierzytelnianie aplikacji utworzonych za pomocą usługi Azure Resource Manager i dostawca zasobów usługi Batch za pomocą usługi Azure AD.
services: batch
documentationcenter: .net
author: laurenhughes
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
ms.author: lahugh
ms.openlocfilehash: 0f6db6d9c86e6da047c45ae7b1c43cf5f55c7e2b
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64922855"
---
# <a name="authenticate-batch-management-solutions-with-active-directory"></a>Uwierzytelnianie rozwiązań usługi Batch Management z usługą Active Directory

Aplikacje, które wywołują usługi Azure Batch Management uwierzytelnianie za pomocą [usługi Azure Active Directory] [ aad_about] (Azure AD). Usługa Azure AD jest katalog oparte na chmurze z wieloma dzierżawami firmy Microsoft i Usługa zarządzania tożsamościami. Azure sam używa usługi Azure AD do uwierzytelniania klientów, Administratorzy usługi i użytkowników w organizacji.

Biblioteki Batch Management .NET ujawnia typy do pracy z kontami usługi Batch, klucze konta, aplikacje i pakiety aplikacji. Biblioteka zarządzania partiami platformy .NET jest klientem dostawcy zasobów platformy Azure i jest używany razem z [usługi Azure Resource Manager] [ resman_overview] Aby programistycznie zarządzać tymi zasobami. Usługa Azure AD jest wymagane do uwierzytelnienia żądania wysyłane za pośrednictwem dowolnego klienta dostawcy zasobów platformy Azure, w tym biblioteki Batch Management .NET oraz [usługi Azure Resource Manager][resman_overview].

W tym artykule omówimy przy użyciu usługi Azure AD do uwierzytelniania w aplikacjach korzystających z bibliotek zarządzania partiami platformy .NET. Firma Microsoft pokazują, jak używać usługi Azure AD do uwierzytelniania administratora subskrypcji lub administratora współpracującego, przy użyciu uwierzytelniania zintegrowanego. Używamy [zarządzania kontem] [ acct_mgmt_sample] przykładowy projekt, dostępne w serwisie GitHub w celu zaprezentowania, za pomocą usługi Azure AD przy użyciu biblioteki zarządzania partiami platformy .NET.

Aby dowiedzieć się więcej o korzystaniu z przykład zarządzania kontem i biblioteki Batch Management .NET, zobacz [partii Zarządzanie kontami i limitami przydziału za pomocą biblioteki klienta Batch Management dla platformy .NET](batch-management-dotnet.md).

## <a name="register-your-application-with-azure-ad"></a>Zarejestruj swoją aplikację z usługą Azure AD

Azure [Active Directory Authentication Library] [ aad_adal] (ADAL) zapewnia interfejs programistyczny do usługi Azure AD do użycia w aplikacjach. Do wywołania biblioteki ADAL z aplikacji, należy zarejestrować aplikację w dzierżawie usługi Azure AD. Podczas rejestrowania aplikacji podajesz usługi Azure AD przy użyciu informacji o aplikacji, w tym jej nazwę w ramach dzierżawy usługi Azure AD. Następnie usługa Azure AD zawiera identyfikator aplikacji, która umożliwia kojarzenie aplikacji z usługą Azure AD w czasie wykonywania. Aby dowiedzieć się więcej o identyfikatorze aplikacji, zobacz [aplikacji i obiektów nazw głównych usług w usłudze Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

Aby zarejestrować przykładową aplikację zarządzania kontem, wykonaj kroki opisane w [dodawania aplikacji](../active-directory/develop/quickstart-register-app.md) sekcji [Integrowanie aplikacji z usługą Azure Active Directory] [ aad_integrate]. Określ **natywną aplikację kliencką** dla typu aplikacji. Przemysł standardowego protokołu OAuth 2.0 Identyfikator URI **identyfikator URI przekierowania** jest `urn:ietf:wg:oauth:2.0:oob`. Można jednak określić dowolny prawidłowy identyfikator URI (takie jak `http://myaccountmanagementsample`) dla **identyfikator URI przekierowania**, ponieważ nie musi odpowiadać rzeczywistemu punktowi końcowemu:

![](./media/batch-aad-auth-management/app-registration-management-plane.png)

Po ukończeniu procesu rejestracji zobaczysz identyfikator aplikacji i identyfikator obiektu (nazwy głównej usługi), na liście aplikacji.  

![](./media/batch-aad-auth-management/app-registration-client-id.png)

## <a name="grant-the-azure-resource-manager-api-access-to-your-application"></a>Udzielanie dostępu do interfejsu API usługi Azure Resource Manager do aplikacji

Następnie należy delegować dostęp do aplikacji interfejsu API usługi Azure Resource Manager. Identyfikator usługi Azure AD dla interfejsu API usługi Resource Manager jest **interfejsu API zarządzania usługami Windows Azure**.

Wykonaj następujące kroki w witrynie Azure portal:

1. W okienku nawigacji po lewej stronie w witrynie Azure portal wybierz **wszystkich usług**, kliknij przycisk **rejestracje aplikacji**i kliknij przycisk **Dodaj**.
2. Wyszukaj nazwę aplikacji na liście rejestracji aplikacji:

    ![Wyszukaj nazwę swojej aplikacji](./media/batch-aad-auth-management/search-app-registration.png)

3. Wyświetlanie **ustawienia** bloku. W **dostęp do interfejsu API** zaznacz **wymagane uprawnienia**.
4. Kliknij przycisk **Dodaj** można dodać nowe wymagane uprawnienie. 
5. W kroku 1, wprowadź **interfejsu API zarządzania usługami Windows Azure**, a następnie wybierz tego interfejsu API z listy wyników i kliknij przycisk **wybierz** przycisku.
6. W kroku 2, zaznacz pole wyboru obok pozycji **Azure dostępu do klasycznego modelu wdrażania jako użytkownicy organizacji**i kliknij przycisk **wybierz** przycisku.
7. Kliknij przycisk **gotowe** przycisku.

**Wymagane uprawnienia** blok zawiera teraz, że uprawnienia do aplikacji są przyznawane do biblioteki ADAL i usługi Resource Manager interfejsów API. Uprawnienia są przyznawane ADAL domyślnie podczas rejestrowania aplikacji z usługą Azure AD.

![Delegowanie uprawnień do interfejsu API usługi Azure Resource Manager](./media/batch-aad-auth-management/required-permissions-management-plane.png)

## <a name="azure-ad-endpoints"></a>Punkty końcowe usługi AD systemu Azure

Uwierzytelnianie rozwiązań usługi Batch Management z usługą Azure AD, należy dwa punkty końcowe dobrze znane.

- **Wspólnego punktu końcowego usługi Azure AD** dostarcza poświadczenia rodzajowe zbierania interfejsu po określonej dzierżawy nie zostanie podany, jak w przypadku zintegrowanego uwierzytelniania:

    `https://login.microsoftonline.com/common`

- **Punktu końcowego usługi Azure Resource Manager** służy do uzyskania tokenu na potrzeby uwierzytelniania żądań do usługi Batch management:

    `https://management.core.windows.net/`

Przykładowa aplikacja zarządzania kontem definiuje stałe dla tych punktów końcowych. Pozostaw te stałe bez zmian:

```csharp
// Azure Active Directory "common" endpoint.
private const string AuthorityUri = "https://login.microsoftonline.com/common";
// Azure Resource Manager endpoint 
private const string ResourceUri = "https://management.core.windows.net/";
```

## <a name="reference-your-application-id"></a>Odwoływać się do Identyfikatora aplikacji 

Aplikacja kliencka używa Identyfikatora aplikacji (nazywane również identyfikator klienta) dostępu do usługi Azure AD w czasie wykonywania. Po zarejestrowaniu aplikacji w witrynie Azure portal, zaktualizować swój kod, aby użyć Identyfikatora aplikacji udostępniane przez usługę Azure AD zarejestrowanej aplikacji. W przykładowej aplikacji zarządzania kontem Skopiuj identyfikator aplikacji w witrynie Azure portal do odpowiednich stałej:

```csharp
// Specify the unique identifier (the "Client ID") for your application. This is required so that your
// native client application (i.e. this sample) can access the Microsoft Azure AD Graph API. For information
// about registering an application in Azure Active Directory, please see "Adding an Application" here:
// https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
private const string ClientId = "<application-id>";
```
Ponadto można kopiować przekierowania URI, które zostały określone podczas procesu rejestracji. Przekierowania URI określona w kodzie musi odpowiadać przekierowania URI, podane podczas rejestrowania aplikacji.

```csharp
// The URI to which Azure AD will redirect in response to an OAuth 2.0 request. This value is
// specified by you when you register an application with AAD (see ClientId comment). It does not
// need to be a real endpoint, but must be a valid URI (e.g. https://accountmgmtsampleapp).
private const string RedirectUri = "http://myaccountmanagementsample";
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Uzyskiwanie tokenu uwierzytelniania usługi Azure AD

Po zarejestrowaniu przykładowe zarządzania kontem w dzierżawie usługi Azure AD i zaktualizuj przykładowy kod źródłowy z własnymi wartościami, próbki jest gotowy do uwierzytelniania za pomocą usługi Azure AD. Po uruchomieniu przykładu biblioteki ADAL próbuje uzyskać tokenu uwierzytelniania. Na tym etapie monituje o podanie poświadczeń firmy Microsoft: 

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

Po podaniu poświadczeń przykładowej aplikacji można przejść do wystawiania żądań uwierzytelnionych usługi Batch management. 

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat używania [zarządzania kontem przykładowej aplikacji][acct_mgmt_sample], zobacz [partii Zarządzanie kontami i limitami przydziału za pomocą biblioteki klienta Batch Management dla platformy .NET](batch-management-dotnet.md) .

Aby dowiedzieć się więcej na temat usługi Azure AD, zobacz [usługi Azure Active Directory dokumentacji](https://docs.microsoft.com/azure/active-directory/). Szczegółowe przykłady, przedstawiający sposób użycia biblioteki ADAL, są dostępne w [przykłady kodu platformy Azure](https://azure.microsoft.com/resources/samples/?service=active-directory) biblioteki.

Aby uwierzytelniać aplikacje usługi Batch za pomocą usługi Azure AD, zobacz [uwierzytelnianie rozwiązań usługi Batch service z usługą Active Directory](batch-aad-auth.md). 


[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Co to jest Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]:../active-directory/develop/authentication-scenarios.md "Scenariusze uwierzytelniania dla usługi Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrowanie aplikacji z usługą Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[azure_portal]: https://portal.azure.com
[resman_overview]: ../azure-resource-manager/resource-group-overview.md
