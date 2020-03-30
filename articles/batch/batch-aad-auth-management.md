---
title: Uwierzytelnianie rozwiązań zarządzania wsadami za pomocą usługi Azure Active Directory
description: Eksploruj przy użyciu usługi Azure Active Directory do uwierzytelniania z aplikacji korzystających z biblioteki .NET zarządzania wsadowego.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/27/2017
ms.author: labrenne
ms.openlocfilehash: 5c217971bd213c97a2ee31a0a1f513b601d14df9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472983"
---
# <a name="authenticate-batch-management-solutions-with-active-directory"></a>Uwierzytelnij rozwiązania do zarządzania partiami za pomocą usługi Active Directory

Aplikacje, które wywołują usługę Azure Batch Management uwierzytelniają się za pomocą [usługi Azure Active Directory][aad_about] (Azure AD). Usługa Azure AD to usługa zarządzania katalogami i tożsamościami opartymi na chmurze z wieloma dzierżawcami firmy Microsoft. Sama platforma Azure używa usługi Azure AD do uwierzytelniania swoich klientów, administratorów usług i użytkowników organizacji.

Biblioteka .NET usługi Zarządzania wsadami udostępnia typy pracy z kontami usługi Batch, kluczami kont, aplikacjami i pakietami aplikacji. Biblioteka .NET usługi Zarządzanie wsadami jest klientem dostawcy zasobów platformy Azure i jest używana razem z [usługą Azure Resource Manager][resman_overview] do programowania zarządzania tymi zasobami. Usługa Azure AD jest wymagana do uwierzytelniania żądań złożonych za pośrednictwem dowolnego klienta dostawcy zasobów platformy Azure, w tym biblioteki .NET usługi Zarządzanie wsadowe oraz za pośrednictwem [usługi Azure Resource Manager.][resman_overview]

W tym artykule eksplorujemy przy użyciu usługi Azure AD do uwierzytelniania z aplikacji korzystających z biblioteki .NET zarządzania wsadowego. Pokazujemy, jak używać usługi Azure AD do uwierzytelniania administratora subskrypcji lub współadministratora przy użyciu zintegrowanego uwierzytelniania. Używamy przykładowego projektu [AccountManagement,][acct_mgmt_sample] dostępnego w usłudze GitHub, aby przejść przez korzystanie z usługi Azure AD z biblioteką .NET usługi zarządzania wsadowego.

Aby dowiedzieć się więcej na temat korzystania z biblioteki .NET zarządzania wsadem i przykładu Zarządzanie kontem, zobacz [Zarządzanie kontami i przydziałami usługi Batch Management za pomocą biblioteki klienta usługi .NET .](batch-management-dotnet.md)

## <a name="register-your-application-with-azure-ad"></a>Rejestrowanie aplikacji za pomocą usługi Azure AD

[Biblioteka uwierzytelniania usługi][aad_adal] Azure Active Directory (ADAL) udostępnia interfejs programowy usługi Azure AD do użytku w aplikacjach. Aby wywołać usługę ADAL z aplikacji, należy zarejestrować aplikację w dzierżawie usługi Azure AD. Podczas rejestrowania aplikacji, należy podać usługę Azure AD z informacjami o aplikacji, w tym nazwę dla niego w dzierżawie usługi Azure AD. Usługa Azure AD udostępnia następnie identyfikator aplikacji używany do skojarzenia aplikacji z usługą Azure AD w czasie wykonywania. Aby dowiedzieć się więcej o identyfikatorze aplikacji, zobacz [Obiekty głównej aplikacji i usługi w usłudze Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

Aby zarejestrować przykładową aplikację AccountManagement, wykonaj kroki opisane w sekcji [Dodawanie aplikacji](../active-directory/develop/quickstart-register-app.md) w [integrowaniu aplikacji z usługą Azure Active Directory][aad_integrate]. Określ **natywną aplikację kliencką** dla typu aplikacji. Standardem branżowym OAuth 2.0 URI `urn:ietf:wg:oauth:2.0:oob`dla **identyfikatora URI przekierowania** jest . Można jednak określić dowolny prawidłowy `http://myaccountmanagementsample`identyfikator URI (na przykład) dla **identyfikatora URI przekierowania,** ponieważ nie musi być rzeczywistym punktem końcowym:

![](./media/batch-aad-auth-management/app-registration-management-plane.png)

Po zakończeniu procesu rejestracji zostanie wyświetlony identyfikator aplikacji i identyfikator obiektu (jednostki usługi) wymienione dla aplikacji.  

![](./media/batch-aad-auth-management/app-registration-client-id.png)

## <a name="grant-the-azure-resource-manager-api-access-to-your-application"></a>Udzielanie dostępu interfejsu API usługi Azure Resource Manager do aplikacji

Następnie musisz delegować dostęp do aplikacji do interfejsu API usługi Azure Resource Manager. Identyfikatorem usługi Azure AD dla interfejsu API Menedżera zasobów jest **interfejs API zarządzania usługami systemu Windows Azure**.

Wykonaj następujące kroki w witrynie Azure Portal:

1. W lewym okienku nawigacji w witrynie Azure portal wybierz pozycję **Wszystkie usługi**, kliknij pozycję **Rejestracje aplikacji**i kliknij pozycję **Dodaj**.
2. Wyszukaj nazwę aplikacji na liście rejestracji aplikacji:

    ![Wyszukaj nazwę aplikacji](./media/batch-aad-auth-management/search-app-registration.png)

3. Wyświetl ostrze **Ustawienia.** W sekcji **Dostęp do interfejsu API** wybierz pozycję Wymagane **uprawnienia**.
4. Kliknij **przycisk Dodaj,** aby dodać nowe wymagane uprawnienie. 
5. W kroku 1 wprowadź **interfejs API zarządzania usługami systemu Windows Azure**, wybierz ten interfejs API z listy wyników i kliknij przycisk **Wybierz.**
6. W kroku 2 zaznacz pole wyboru obok **klasycznego modelu wdrażania platformy Access jako użytkownicy organizacji**i kliknij przycisk **Wybierz.**
7. Kliknij przycisk **Gotowe.**

W bloku **Wymagane uprawnienia** jest teraz wyświetlany, że uprawnienia do aplikacji są przyznawane zarówno do interfejsów API menedżera ADAL, jak i Menedżera zasobów. Uprawnienia są przyznawane adal domyślnie podczas pierwszego rejestrowania aplikacji w usłudze Azure AD.

![Delegowanie uprawnień do interfejsu API usługi Azure Resource Manager](./media/batch-aad-auth-management/required-permissions-management-plane.png)

## <a name="azure-ad-endpoints"></a>Punkty końcowe usługi Azure AD

Aby uwierzytelnić rozwiązania zarządzania wsadowego za pomocą usługi Azure AD, potrzebne są dwa dobrze znane punkty końcowe.

- **Wspólny punkt końcowy usługi Azure AD** udostępnia ogólny interfejs gromadzenia poświadczeń, gdy określona dzierżawa nie jest podana, jak w przypadku zintegrowanego uwierzytelniania:

    `https://login.microsoftonline.com/common`

- **Punkt końcowy usługi Azure Resource Manager** jest używany do uzyskiwania tokenu do uwierzytelniania żądań do usługi zarządzania partią:

    `https://management.core.windows.net/`

Przykładowa aplikacja AccountManagement definiuje stałe dla tych punktów końcowych. Pozostawić te stałe bez zmian:

```csharp
// Azure Active Directory "common" endpoint.
private const string AuthorityUri = "https://login.microsoftonline.com/common";
// Azure Resource Manager endpoint 
private const string ResourceUri = "https://management.core.windows.net/";
```

## <a name="reference-your-application-id"></a>Odwoływanie się do identyfikatora aplikacji 

Aplikacja kliencka używa identyfikatora aplikacji (nazywanego również identyfikatorem klienta) do uzyskiwania dostępu do usługi Azure AD w czasie wykonywania. Po zarejestrowaniu aplikacji w witrynie Azure portal, zaktualizuj kod, aby użyć identyfikatora aplikacji dostarczonego przez usługę Azure AD dla zarejestrowanej aplikacji. W przykładowej aplikacji AccountManagement skopiuj identyfikator aplikacji z witryny Azure portal do odpowiedniej stałej:

```csharp
// Specify the unique identifier (the "Client ID") for your application. This is required so that your
// native client application (i.e. this sample) can access the Microsoft Graph API. For information
// about registering an application in Azure Active Directory, please see "Register an application with the Microsoft identity platform" here:
// https://docs.microsoft.com/en-us/azure/active-directory/develop/quickstart-register-app
private const string ClientId = "<application-id>";
```
Również skopiować identyfikator URI przekierowania, który został określony podczas procesu rejestracji. Identyfikator URI przekierowania określony w kodzie musi być zgodny z identyfikatorem URI przekierowania podanym podczas rejestracji aplikacji.

```csharp
// The URI to which Azure AD will redirect in response to an OAuth 2.0 request. This value is
// specified by you when you register an application with AAD (see ClientId comment). It does not
// need to be a real endpoint, but must be a valid URI (e.g. https://accountmgmtsampleapp).
private const string RedirectUri = "http://myaccountmanagementsample";
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Uzyskiwanie tokenu uwierzytelniania usługi Azure AD

Po zarejestrowaniu przykładu AccountManagement w dzierżawie usługi Azure AD i zaktualizowaniu przykładowego kodu źródłowego z wartościami, przykład jest gotowy do uwierzytelnienia przy użyciu usługi Azure AD. Po uruchomieniu próbki, ADAL próbuje uzyskać token uwierzytelniania. W tym kroku monituje o poświadczenia firmy Microsoft: 

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

Po podaniu poświadczeń przykładowa aplikacja może przystąpić do wystawiania uwierzytelnionych żądań do usługi zarządzania partią. 

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat [uruchamiania przykładowej aplikacji Zarządzanie kontem,][acct_mgmt_sample]zobacz [Zarządzanie kontami i przydziałami usługi Batch za pomocą biblioteki klienta usługi .NET w ramach usługi .NET](batch-management-dotnet.md).

Aby dowiedzieć się więcej o usłudze Azure AD, zobacz [Dokumentację usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/). Szczegółowe przykłady pokazujące, jak używać biblioteki ADAL są dostępne w [bibliotece przykładów kodu Azure.](https://azure.microsoft.com/resources/samples/?service=active-directory)

Aby uwierzytelnić aplikacje usługi Batch przy użyciu usługi Azure AD, zobacz [Uwierzytelnianie rozwiązań usługi wsadowej za pomocą usługi Active Directory](batch-aad-auth.md). 


[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Co to jest usługa Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]:../active-directory/develop/authentication-scenarios.md "Scenariusze uwierzytelniania dla usługi Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrowanie aplikacji z usługą Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[azure_portal]: https://portal.azure.com
[resman_overview]: ../azure-resource-manager/management/overview.md
