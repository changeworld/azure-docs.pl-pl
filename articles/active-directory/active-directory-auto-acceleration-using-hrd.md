---
title: Konfigurowanie logowania automatycznego przyspieszenie aplikacji przy użyciu zasad odnajdowania obszaru macierzystego | Dokumentacja firmy Microsoft
description: Wyjaśniono, jest dzierżawa usługi Azure AD oraz sposób zarządzania nią za pomocą usługi Azure Active Directory.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: it-pro
ms.date: 11/09/2017
ms.author: barbkess
ms.openlocfilehash: 5df12f905595c9b3e8caa8f372b9ba7b54672f81
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="configure-sign-in-auto-acceleration-for-an-application-by-using-a-home-realm-discovery-policy"></a>Konfigurowanie logowania automatycznego przyspieszenie dla aplikacji za pomocą zasad odnajdowania obszaru macierzystego

Ten dokument zawiera wprowadzenie do odnajdowania obszaru macierzystego i przyspieszenie automatycznie.

## <a name="home-realm-discovery"></a>Odnajdowanie obszaru macierzystego
Odnajdowanie obszaru macierzystego (HRD) jest proces, który pozwala usłudze Azure Active Directory (Azure AD), aby ustalić, podczas logowania, gdy użytkownik musi uwierzytelnić.  Gdy użytkownik zaloguje się do dzierżawy usługi Azure AD w celu uzyskania dostępu do zasobu lub do usługi Azure AD wspólnej strony logowania, ich wpisz nazwę użytkownika (UPN). Usługi Azure AD użyty do odnajdywania, gdy użytkownik musi się zalogować. 

Użytkownik może być konieczne przekierowanie do uwierzytelnienia do jednej z następujących lokalizacji:

- Dzierżawca domowej użytkownika (może to być jako zasób, którego użytkownik próbuje uzyskać dostęp do tej samej dzierżawy). 

- Microsoft account.  Użytkownik jest gościa w dzierżawie zasobów.

- Inny dostawca tożsamości, która jest Sfederowane przy użyciu dzierżawy usługi Azure AD.

-  Dostawca tożsamości lokalnych, takich jak Active Directory Federation Services (AD FS).

## <a name="auto-acceleration"></a>Akceleracja automatycznie 
Niektóre organizacje skonfigurować ich dzierżawy usługi Azure Active Directory możliwości utworzenia federacji z innego dostawców tożsamości, takie jak usługi AD FS do uwierzytelniania użytkowników.  

W tych przypadkach gdy loguje użytkownika do aplikacji są najpierw najpierw wpisywane w stronę logowania w usłudze Azure AD. Po wpisywania ich nazwy UPN, są one następnie przekierowanie do strony logowania dostawców tożsamości. W pewnych okolicznościach Administratorzy mogą chcieć przekierować użytkowników do strony logowania, gdy są one zalogowanie się do określonych aplikacji. 

Oznacza to, że użytkownicy, można pominąć stronę początkową usługi Azure Active Directory. Ten proces jest nazywany "Logowanie automatyczne przyspieszenie."

W przypadkach, gdy dzierżawcy jest Sfederowane do innego dostawców tożsamości dla logowania przyspieszenie automatycznie sprawia, że użytkownika logowania więcej uproszczone.  Możesz skonfigurować automatyczne przyspieszenie dla poszczególnych aplikacji.

>[!NOTE]
>Jeśli skonfigurujesz aplikację do przyspieszania automatycznie gości nie można zalogować. Jeśli nie podejmiesz użytkownika bezpośrednio do federacyjnych IdP uwierzytelniania, brak można dla nich wrócić do strony logowania usługi Azure Active Directory. Gość użytkowników, którzy mogą wymagać będą kierowane do innych dzierżawców lub zewnętrznych dostawców tożsamości, takie jak konto Microsoft, nie można zarejestrować do tej aplikacji, ponieważ są one pomijanie odnajdowania obszaru macierzystego krok.  

Istnieją dwa sposoby kontrolowania przyspieszenie automatycznie do federacyjnych IdP:   

- Użyć wskazówki domeny na żądania uwierzytelniania dla aplikacji. 
- Skonfiguruj zasady odnajdowania obszaru macierzystego, aby włączyć przyspieszenie automatycznie.

## <a name="domain-hints"></a>Wskazówki dotyczące domeny 
Wskazówki domeny są dyrektywy, które są częścią żądania uwierzytelniania z aplikacji. Mogą one używane w celu przyspieszenia użytkownika do ich federacyjnych IdP strony logowania. Lub może być używane przez aplikację wielodostępnej w celu przyspieszenia użytkownika bezpośrednio do marką usługi Azure AD strony logowania dla swojej dzierżawy.  

Na przykład aplikacji "largeapp.com" może pozwolić użytkownikom na dostęp do aplikacji w niestandardowy adres URL "contoso.largeapp.com." Aplikacja może również obejmować wskazówkę domeny contoso.com w żądaniu uwierzytelnienia. 

Składni wskazówka domeny może być różna w zależności od używanego protokołu i zazwyczaj jest skonfigurowany w aplikacji.

**WS-Federation**: whr=contoso.com w ciągu zapytania.

**SAML**: albo SAML uwierzytelniania żądań zawierających wskazówkę domeny lub whr=contoso.com ciągu zapytania.

**Otwórz ID Connect**: domain_hint=contoso.com ciągu zapytania. 

Jeśli wskazówkę dotyczącą domeny jest dołączony do żądania uwierzytelniania z aplikacji, a dzierżawcy jest Sfederowane przy użyciu tej domeny, usługi Azure AD podejmuje próbę przekierowania logowania do IdP, który jest skonfigurowany dla tej domeny. 

Wskazówka domeny nie odwołuje się do zweryfikowanej domeny federacyjnej, zostanie zignorowany, a wywołaniu normalne odnajdowania obszaru macierzystego.

Aby uzyskać więcej informacji na temat przyspieszenie automatycznie przy użyciu wskazówek domeny, które są obsługiwane przez usługę Azure Active Directory, zobacz [Enterprise Mobility + Security blog](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/).

>[!NOTE]
>Jeśli wskazówkę dotyczącą domeny jest uwzględniona w żądanie uwierzytelnienia, jego obecność zastępuje dowolne zasady Wyświetlają ustawionej dla aplikacji.

## <a name="home-realm-discovery-policy"></a>Zasady odnajdowania obszaru macierzystego
Niektóre aplikacje nie umożliwiają konfigurowanie, które wysyłają żądania uwierzytelnienia. W takich przypadkach nie jest można użyć wskazówki domeny do kontrolowania przyspieszenie automatycznie. Przyspieszenie automatycznie można skonfigurować za pomocą zasad, aby uzyskać takie samo zachowanie.  

### <a name="set-hrd-policy"></a>Ustaw zasady HRD
Istnieją trzy kroki w celu ustawienia logowania automatycznego przyspieszenie aplikacji:


1. Tworzenie zasad odnajdowanie obszaru macierzystego dla przyspieszenia automatycznie.

2. Wyszukiwanie nazwy głównej usługi, do którego ma zostać dołączony zasad.

3. Dołączanie zasady do nazwy głównej usługi. Zasady mogą został utworzony w dzierżawie, ale nie ma żadnego efektu, dopóki nie są one dołączone do jednostki. 

Odnajdowanie obszaru macierzystego zasad można dołączyć do nazwy głównej usługi, a tylko jedna zasada HRD mogą być aktywne w danej jednostce w dowolnym momencie.  

Microsoft Azure Active Directory interfejsu API programu Graph bezpośrednio lub środowiska PowerShell usługi Azure Active Directory polecenia cmdlet umożliwiają konfigurowanie automatycznego przyspieszenie za pomocą zasad odnajdowanie obszaru macierzystego.

Interfejsu API programu Graph manipuluje zasad jest opisane w [operacje na zasadzie](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) artykuł w witrynie MSDN.

Poniżej przedstawiono przykładowe Wyświetlają definicji zasad:
    
 ```
   {  
    "HomeRealmDiscoveryPolicy":
    {  
    "AccelerateToFederatedDomain":true,
    "PreferredDomain":"federated.example.edu"
    }
   }
```

Typ zasad jest "HomeRealmDiscoveryPolicy."

Jeśli **AccelerateToFederatedDomain** ma wartość false, zasada nie ma znaczenia.

**PreferredDomain** powinny wskazywać domeny, do której w celu przyspieszenia. Można pominąć, jeśli dzierżawca ma tylko jedną domenę federacyjną.  Jeśli zostanie pominięty i ma więcej niż jeden zweryfikować domeny federacyjnej, zasady nie ma znaczenia.

Jeśli **PreferredDomain** jest określona, musi on być zgodny domeny zweryfikowane, federacyjnych dla dzierżawcy. Wszyscy użytkownicy aplikacji musi mieć możliwość Zaloguj się do tej domeny.

### <a name="priority-and-evaluation-of-hrd-policies"></a>Priorytet i oceny zasad HRD
Odnajdowanie obszaru macierzystego zasad można tworzyć i następnie przypisane do określonych organizacji i nazwy główne usług. Oznacza to, czy jest możliwe w dla wielu zasady do zastosowania dla określonej aplikacji. Zasady HRD, które obowiązuje obowiązują następujące reguły:


- Jeśli wskazówkę dotyczącą domeny jest obecny w żądaniu uwierzytelnienia, dowolne zasady HRD jest ignorowana. Zachowanie jest określoną przez warunek domeny jest używany.

- W przeciwnym razie jeśli zasady jawnie jest przypisany do nazwy głównej usługi, jest wymuszone. 

- Jeśli nie istnieje żadne wskazówka domeny, a żadne zasady nie zostały wyraźnie przypisane do nazwy głównej usługi, zasady, które jawnie jest przypisany do organizacji nadrzędnej nazwy głównej usługi jest wymuszana. 

- Jeśli nie istnieje żadne wskazówka domeny, a żadne zasady nie został przypisany do nazwy głównej usługi lub organizacji, domyślne zachowanie HRD jest używany.

## <a name="tutorial-for-setting-sign-in-auto-acceleration-on-an-application-by-using-an-hrd-policy"></a>Samouczek dotyczący ustawienia logowania automatycznego przyspieszenie aplikacji za pomocą zasad HRD
Użyjemy polecenia cmdlet programu Azure AD PowerShell przechodzenia przez kilka scenariuszy, w tym:


- Konfigurowanie automatycznego przyspieszenie aplikacji dzierżawcy z jednej domeny federacyjnej.

- Konfigurowanie automatycznego przyspieszenie aplikacji wiele domen, które są sprawdzane dla dzierżawy.

- Wyświetlanie listy aplikacji, dla których skonfigurowano zasadę.

### <a name="prerequisites"></a>Wymagania wstępne
Poniższe przykłady służy do tworzenia, aktualizacji, łączenie i usuwania zasad na nazwy główne usług aplikacji w usłudze Azure AD.

1.  Aby rozpocząć, Pobierz najnowszą Podgląd polecenia cmdlet programu PowerShell usługi Azure AD. 

2.  Po pobraniu poleceń cmdlet programu Azure AD PowerShell, uruchom polecenie Połącz logować się do usługi Azure AD przy użyciu konta administratora:

    ``` powershell
    Connect-AzureAD -Confirm
    ```
3.  Uruchom następujące polecenie, aby wyświetlić wszystkie zasady w organizacji:

    ``` powershell
    Get-AzureADPolicy
    ```

Jeśli nic nie zostanie zwrócone, oznacza to, że masz żadnych zasad utworzony w dzierżawie.

### <a name="example-set-auto-acceleration-for-an-application"></a>Przykład: Zestaw automatycznie przyspieszenie dla aplikacji 
W tym przykładzie utworzysz zasady, które automatycznie przyspiesza użytkowników do ekranu logowania usługi AD FS podczas ich logowania do aplikacji. Użytkownicy mogą Zaloguj się do usług AD FS bez konieczności najpierw wprowadź nazwę użytkownika na stronie logowania w usłudze Azure AD. 

#### <a name="step-1-create-an-hrd-policy"></a>Krok 1: Tworzenie zasad HRD
``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Jeśli masz jednej domeny federacyjnej, który uwierzytelnia użytkowników dla aplikacji, należy utworzyć tylko jedne zasady odnajdowanie obszaru macierzystego.  

Aby wyświetlić nowe zasady i uzyskać jego **ObjectID**, uruchom następujące polecenie:

``` powershell
Get-AzureADPolicy
```


Aby włączyć przyspieszenie automatycznie po utworzeniu zasad HRD, można przypisać go do wiele elementów głównych z usługi aplikacji.

#### <a name="step-2-locate-the-service-principal-to-which-to-assign-the-policy"></a>Krok 2: Znajdź nazwy głównej usługi, do którego chcesz przypisać zasady  
Należy **ObjectID** podmiotów zabezpieczeń usługi, do których chcesz przypisać zasady. Istnieje kilka sposobów, aby znaleźć **ObjectID** podmiotów zabezpieczeń usługi.    

Mogą korzystać z portalu lub można zbadać [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity). Można także przejść do [narzędzie Explorer wykres](https://graphexplorer.cloudapp.net/) i zaloguj się do swojego konta usługi Azure AD, aby zobaczyć nazwy główne usług wszystkich organizacji. Ponieważ używasz programu PowerShell służy polecenie cmdlet get-AzureADServicePrincipal do tworzenia listy nazw głównych usług i ich identyfikatorów.

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>Krok 3: Przypisz zasady do Twojej nazwy głównej usługi  
Po utworzeniu **ObjectID** z aplikacji, dla której chcesz skonfigurować automatyczne przyspieszenie nazwy głównej usługi, uruchom następujące polecenie. To polecenie powoduje skojarzenie zasad HRD, które zostały utworzone w kroku 1 z nazwy głównej usługi, zlokalizowanego w kroku 2.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

To polecenie można powtórzyć dla każdej nazwy głównej usługi, do której chcesz dodać zasady.

#### <a name="step-4-check-which-application-service-principals-your-auto-acceleration-policy-is-assigned-to"></a>Krok 4: Sprawdź, które zasady automatycznego przyspieszenie jest przypisany do nazwy główne usług aplikacji
Aby sprawdzić, które aplikacje skonfigurowanych zasad przyspieszenie automatycznie, użyj **Get-AzureADPolicyAppliedObject** polecenia cmdlet. Przekaż go **ObjectID** zasady, które chcesz sprawdzić.

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>Krok 5: Gotowe!
Spróbuj aplikacji, aby sprawdzić, czy działa nowych zasad.

### <a name="example-list-the-applications-for-which-an-auto-acceleration-policy-is-configured"></a>Przykład: Lista aplikacji, dla których skonfigurowano zasadę automatycznego przyspieszenia

#### <a name="step-1-list-all-policies-that-were-created-in-your-organization"></a>Krok 1: Wyświetl listę wszystkich zasad, które zostały utworzone w Twojej organizacji 

``` powershell
Get-AzureADPolicy
```

Uwaga **ObjectID** zasady, które chcesz przydziałów listy.

#### <a name="step-2-list-the-service-principals-to-which-the-policy-is-assigned"></a>Krok 2: Listy nazwy główne usług, do których przypisano zasad  

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```

### <a name="example-remove-an-auto-acceleration-policy-for-an-application"></a>Przykład: Usuń zasady automatycznego przyspieszenie dla aplikacji
#### <a name="step-1-get-the-objectid"></a>Krok 1: Pobierz identyfikator obiektu
Użyj poprzedni przykład, aby uzyskać **ObjectID** zasad i który nazwy głównej usługi aplikacji, z którego chcesz usunąć. 

#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>Krok 2: Usuń przypisanie zasad z nazwy głównej usługi aplikacji  

``` powershell
Remove-AzureADApplicationPolicy -ObjectId <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-to-which-the-policy-is-assigned"></a>Krok 3: Sprawdź usuwania poprzez wyszczególnienie nazwy główne usług, do których przypisano zasad 

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
## <a name="next-steps"></a>Kolejne kroki
- Aby uzyskać więcej informacji na temat działania uwierzytelniania w usłudze Azure AD, zobacz [scenariusze uwierzytelniania dla usługi Azure AD](develop/active-directory-authentication-scenarios.md).
- Aby uzyskać więcej informacji dotyczących użytkownika logowania jednokrotnego, zobacz [dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](active-directory-enterprise-apps-manage-sso.md).
- Odwiedź stronę [przewodnik dewelopera usługi Active Directory](develop/active-directory-developers-guide.md) omówienie związane z programowaniem całą jego zawartość.
