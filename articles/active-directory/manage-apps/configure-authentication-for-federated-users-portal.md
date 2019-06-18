---
title: Konfigurowanie logowania automatycznego przyspieszenia aplikacji za pomocą zasad odnajdowania obszaru macierzystego | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować zasady odnajdowania obszaru macierzystego dla uwierzytelniania usługi Azure Active Directory dla użytkowników federacyjnych, w tym automatycznego przyspieszenia i domeny.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mimart
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18f7f6588cb4fb3b3b480402c3dad13be4a0ed2c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65781028"
---
# <a name="configure-azure-active-directory-sign-in-behavior-for-an-application-by-using-a-home-realm-discovery-policy"></a>Konfigurowanie logowania usługi Azure Active Directory w zachowaniu aplikacji za pomocą zasad odnajdowania obszaru macierzystego

Ten artykuł zawiera wprowadzenie do konfigurowania zachowania uwierzytelniania usługi Azure Active Directory dla użytkowników federacyjnych. Poruszono w nim konfiguracji uwierzytelniania i automatycznego przyspieszenia ograniczeń dla użytkowników w domenach federacyjnych.

## <a name="home-realm-discovery"></a>Odnajdowanie obszaru głównego
Home obszaru odnajdywania (HRD) to proces, który umożliwia usłudze Azure Active Directory (Azure AD), aby określić, w którym użytkownik musi uwierzytelnić podczas logowania.  Gdy użytkownik loguje się do dzierżawy usługi Azure AD, aby uzyskać dostęp do zasobu lub usługi Azure AD wspólnej strony logowania, ich wpisz nazwę użytkownika (UPN). Usługi Azure AD używa, aby dowiedzieć się, gdy użytkownik musi się zalogować. 

Użytkownik może być konieczne jest uważana za do jednej z następujących lokalizacji można uwierzytelnić:

- Dzierżawy głównej użytkownika (może to być tej samej dzierżawie, co zasób, który użytkownik próbuje uzyskać dostęp). 

- Microsoft account.  Użytkownik jest gościa w dzierżawie zasobów.

-  Dostawca tożsamości lokalnych, takich jak Active Directory Federation Services (AD FS).

- Innego dostawcy tożsamości, które są Sfederowane z dzierżawą usługi Azure AD.

## <a name="auto-acceleration"></a>Automatycznego przyspieszenia 
W niektórych organizacjach Konfigurowanie domen w ramach ich dzierżawy usługi Azure Active Directory w celu sfederowania przy użyciu innego dostawcy tożsamości, takich jak usługi AD FS do uwierzytelniania użytkowników.  

Gdy użytkownik zaloguje się do aplikacji, najpierw zobaczą strony logowania usługi Azure AD. Po wpisywania ich nazwy UPN, jeśli są one w domeny federacyjnej następnie przejdzie do strony logowania dostawcy tożsamości obsługująca tej domeny. W pewnych okolicznościach Administratorzy mogą chcieć przekierować użytkowników do strony logowania, gdy są one logowania się do określonych aplikacji. 

W rezultacie użytkownicy mogą pomijać początkowej strony usługi Azure Active Directory. Ten proces jest nazywany "sign in automatycznego przyspieszenia."

W przypadkach, w której dzierżawy jest sfederowana do innego dostawcy tożsamości do logowania automatycznego przyspieszenia sprawia, że użytkownik logowania bardziej bezproblemowe.  Można skonfigurować automatycznego przyspieszenia dla poszczególnych aplikacji.

>[!NOTE]
>Jeśli skonfigurujesz aplikację do automatycznego przyspieszenia, nie można zalogować użytkowników-gości. Jeśli nie podejmiesz użytkownika bezpośrednio do federacyjnego dostawcę tożsamości do uwierzytelniania, nie ma możliwości można dla nich wrócić do strony logowania usługi Azure Active Directory. Użytkowników-gości, którzy mogą potrzebować były kierowane do innych dzierżaw lub zewnętrznego dostawcy tożsamości, takich jak konto Microsoft, nie można zalogować się do tej aplikacji, ponieważ są one krok zostanie pominięty odnajdowania obszaru macierzystego.  

Istnieją dwa sposoby kontrolowania automatycznego przyspieszenia do federacyjnego dostawcy tożsamości:   

- W systemie wskazówkę dotyczącą domeny żądań uwierzytelniania dla aplikacji. 
- Konfigurowanie zasad odnajdowania obszaru macierzystego w celu włączenia automatycznego przyspieszenia.

### <a name="domain-hints"></a>Wskazówki dotyczące domeny    
Wskazówki domeny są dyrektyw, które są częścią żądania uwierzytelniania z aplikacji. Mogą one używane, aby przyspieszyć użytkownika do ich federacyjnych dostawca tożsamości strony logowania. Lub mogą być używane przez aplikację wielodostępną aby przyspieszyć użytkownika bezpośrednio na marki usługi Azure AD strony logowania dla swojej dzierżawy.  

Na przykład może to umożliwić swoim klientom w celu uzyskania dostępu do aplikacji na niestandardowy adres URL "contoso.largeapp.com." aplikacja "largeapp.com" Aplikacja może również obejmować wskazówkę dotyczącą domeny do domeny contoso.com w żądaniu uwierzytelnienia. 

Składnia wskazówkę dotyczącą domeny różni się zależnie od używanego protokołu i jest zazwyczaj skonfigurowany, w aplikacji.

**WS-Federation**: whr=contoso.com w ciągu zapytania.

**SAML**:  Albo żądanie języka SAML uwierzytelniania zawierający wskazówkę dotyczącą domeny lub whr=contoso.com ciągu zapytania.

**Open ID Connect**: Domain_hint=contoso.com ciągu zapytania. 

Jeśli wskazówkę dotyczącą domeny znajduje się w żądaniu uwierzytelnienia od aplikacji oraz dzierżawy jest Sfederowane przy użyciu tej domeny usługi Azure AD próbuje przekierować Zaloguj się do dostawcy tożsamości, który jest skonfigurowany dla tej domeny. 

Jeśli wskazówkę dotyczącą domeny nie odwołuje się do zweryfikowanej domeny federacyjnej, jest ignorowany i wywoływaną normalne odnajdowania obszaru macierzystego.

Aby uzyskać więcej informacji na temat automatycznego przyspieszenia przy użyciu wskazówek dotyczących serwerów domeny, które są obsługiwane przez usługę Azure Active Directory, zobacz [pakietu Enterprise Mobility + Security blog](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/).

>[!NOTE]
>Jeśli wskazówkę dotyczącą domeny jest uwzględniony w żądaniu uwierzytelnienia, jego obecność zastępuje automatycznego przyspieszenia ustawionego dla aplikacji w zasadach odnajdowanie obszaru macierzystego.

### <a name="home-realm-discovery-policy-for-auto-acceleration"></a>Odnajdowanie obszaru macierzystego zasady automatycznego przyspieszenia
Niektóre aplikacje są oferowane sposób konfigurowania żądań uwierzytelniania, które wysyłają. W takich przypadkach nie jest możliwe kontrolowanie automatycznego przyspieszenia wskazówek dotyczących serwerów domeny. Automatycznego przyspieszenia można skonfigurować za pomocą zasad, aby osiągnąć takie samo zachowanie.  

## <a name="enable-direct-authentication-for-legacy-applications"></a>Włącz bezpośrednie uwierzytelnianie dla starszych aplikacji
Najlepszym rozwiązaniem jest aplikacje mogą korzystać z bibliotek usługi AAD i interakcyjnego logowania do uwierzytelniania użytkowników. Biblioteki zajmujemy się przepływów użytkownika federacyjnego.  Czasami starsze aplikacje nie są zapisywane do zrozumienia federacji. One nie wykonują odnajdowania obszaru macierzystego i nie wchodzą w interakcje z poprawną federacyjny punkt końcowy do uwierzytelnienia użytkownika. Jeśli użytkownik chce, można użyć zasad HRD umożliwiające określonych starsze aplikacje, które przesyłania nazwy użytkownika/hasła poświadczenia, aby uwierzytelnić bezpośrednio z usługą Azure Active Directory. Musi być włączona synchronizacja skrótów haseł. 

> [!IMPORTANT]
> Bezpośrednie uwierzytelniania należy włączyć tylko, jeśli masz włączoną synchronizacją skrótów haseł i wiesz, że ma nic złego do uwierzytelniania tej aplikacji, bez żadnych zasad implementowany przez lokalnego dostawcy tożsamości. Jeśli wyłączyć funkcję synchronizacji skrótów haseł lub wyłączyć funkcję synchronizacji katalogów za pomocą programu AD Connect dla jakiegokolwiek powodu, należy usunąć te zasady, aby zapobiec możliwość bezpośredniego uwierzytelniania przy użyciu skrótu stare hasło.

## <a name="set-hrd-policy"></a>Ustawianie zasad HRD
Istnieją trzy kroki, aby ustawienie zasad HRD na aplikacji dla federacyjnego logowania automatycznego przyspieszenia lub bezpośredniego aplikacje oparte na chmurze:

1. Tworzenie zasad HRD.

2. Znajdź jednostkę usługi, do którego ma zostać dołączony zasad.

3. Dołącz zasady do nazwy głównej usługi. 

Zasady mają zastosowanie, dla określonej aplikacji tylko wtedy, gdy są one dołączone do nazwy głównej usługi. 

Tylko jedna zasada HRD może być aktywny na nazwę główną usługi w dowolnym momencie.  

Tworzenie i zarządzanie nimi zasad HRD, można użyć poleceń cmdlet programu Microsoft Azure Active Directory interfejsu API programu Graph bezpośrednio lub Azure Active Directory PowerShell.

Interfejsu API programu Graph, która manipuluje zasad jest opisana w [operacje na zasadach](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) artykuł w witrynie MSDN.

Poniżej znajduje się przykład definicji zasad HRD:
    
 ```
   {  
    "HomeRealmDiscoveryPolicy":
    {  
    "AccelerateToFederatedDomain":true,
    "PreferredDomain":"federated.example.edu",
    "AllowCloudPasswordValidation":true
    }
   }
```

Typ zasad jest "HomeRealmDiscoveryPolicy."

**AccelerateToFederatedDomain** jest opcjonalne. Jeśli **AccelerateToFederatedDomain** ma wartość FAŁSZ, zasady nie ma wpływu na automatycznego przyspieszenia. Jeśli **AccelerateToFederatedDomain** jest prawdziwe i istnieje tylko jeden zweryfikowaniu i domeny federacyjnej w dzierżawie, a następnie użytkownicy nastąpi przejście bezpośrednio do federacyjnego dostawcę tożsamości do logowania. To PRAWDA, jeśli ma więcej niż jednej domeny zweryfikowanej w dzierżawie, **PreferredDomain** musi być określona.

**PreferredDomain** jest opcjonalne. **PreferredDomain** powinna być widoczna domenę, dla której chcesz przyspieszyć. Można pominąć, jeśli dzierżawa ma tylko jedną domenę federacyjną.  Jeśli zostanie pominięty, a istnieje więcej niż jedną zweryfikowaną domeną federacyjną, zasada nie ma znaczenia.

 Jeśli **PreferredDomain** jest określony, musi on być zgodny domenę zweryfikowaną, federacyjnego dla dzierżawy. Wszyscy użytkownicy aplikacji musi mieć możliwość Zaloguj się do tej domeny.

**AllowCloudPasswordValidation** jest opcjonalne. Jeśli **AllowCloudPasswordValidation** ma wartość true, a następnie aplikacja może uwierzytelnić użytkownika federacyjnego z uwzględnieniem nazwy użytkownika i hasła poświadczeń bezpośrednio do punktu końcowego tokenu usługi Azure Active Directory. Ta działa tylko po włączeniu synchronizacji skrótów haseł.

### <a name="priority-and-evaluation-of-hrd-policies"></a>Priorytet i oceny zasad HRD
Można tworzyć zasad HRD i następnie przypisywane do określonych organizacji i nazwy główne usług. Oznacza to, że możliwe jest wiele zasad zastosować do określonej aplikacji. Zasad HRD, która staje się skuteczny obowiązują następujące reguły:


- Jeśli wskazówkę dotyczącą domeny znajduje się w żądaniu uwierzytelnienia, żadnych zasad HRD jest ignorowany dla automatycznego przyspieszenia. Zachowanie, który jest określony przez wskazówkę dotyczącą domeny jest używany.

- W przeciwnym razie jeśli zasady jawnie jest przypisany do nazwy głównej usługi, jego zostanie wymuszona. 

- Jeśli istnieje nie wskazówkę dotyczącą domeny, a żadne zasady nie zostały wyraźnie przypisane do jednostki usługi, zasady, które są jawnie przypisane do organizacji nadrzędnej jednostki usługi jest wymuszana. 

- Jeśli istnieje nie wskazówkę dotyczącą domeny, a nie zasady zostały przypisane do jednostki usługi lub organizacji, domyślne zachowanie HRD jest używany.

## <a name="tutorial-for-setting-hrd-policy-on-an-application"></a>Samouczek dotyczący ustawiania zasad HRD w aplikacji 
Użyjemy polecenia cmdlet programu PowerShell usługi Azure AD do zaprezentowania kilka scenariuszy, w tym:


- Konfigurowanie zasad HRD w celu automatycznego przyspieszenia dla aplikacji w dzierżawie za pomocą jednej domeny federacyjnej.

- Konfigurowanie zasad HRD w celu automatycznego przyspieszenia aplikacji kilka domen, które są sprawdzane dla Twojej dzierżawy.

- Konfigurowanie zasad HRD, aby umożliwić starszych aplikacji bezpośrednie uwierzytelnianie nazwy użytkownika/hasła usługi Azure Active Directory dla użytkownika federacyjnego.

- Wyświetlanie listy aplikacji, dla których skonfigurowano zasady.


### <a name="prerequisites"></a>Wymagania wstępne
Poniższe przykłady służy do tworzenia, aktualizacji, łączenie i usuwania zasad na jednostek usług aplikacji w usłudze Azure AD.

1.  Aby rozpocząć, Pobierz najnowszą wersję zapoznawczą polecenia cmdlet programu PowerShell usługi Azure AD. 

2.  Po pobraniu poleceń cmdlet programu PowerShell usługi Azure AD, uruchom polecenie Connect, aby zalogować się do usługi Azure AD przy użyciu konta administratora:

    ``` powershell
    Connect-AzureAD -Confirm
    ```
3.  Uruchom następujące polecenie, aby wyświetlić wszystkie zasady w Twojej organizacji:

    ``` powershell
    Get-AzureADPolicy
    ```

Jeśli nic nie zostanie zwrócone, oznacza to, że masz żadnych zasad utworzonych w dzierżawie.

### <a name="example-set-hrd-policy-for-an-application"></a>Przykład: Ustawianie zasad HRD dla aplikacji 

W tym przykładzie utworzysz zasadę, gdy jest przypisany do aplikacji albo: 
- Auto przyspiesza użytkowników na ekranie logowania usług AD FS podczas ich są logowania do aplikacji po jednej domeny w dzierżawie. 
- Przyspiesza automatycznie użytkowników do usług AD FS ekran logowania jest więcej niż jednej domeny federacyjnej w Twojej dzierżawie.
- Włącza logowania nieinterakcyjnego nazwy użytkownika i hasła w bezpośrednio do usługi Azure Active Directory dla użytkowników federacyjnych dla aplikacji, których zasady są przypisane do.

#### <a name="step-1-create-an-hrd-policy"></a>Krok 1: Tworzenie zasad HRD

Następujące zasady auto przyspiesza użytkowników na ekranie logowania usług AD FS podczas ich są logowania do aplikacji po jednej domeny w dzierżawie.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```
Następujące zasady auto przyspiesza użytkowników do usług AD FS ekran logowania ma więcej niż jednej domeny federacyjnej w Twojej dzierżawie. Jeśli masz więcej niż jednej domeny federacyjnej, która uwierzytelnia użytkowników dla aplikacji, należy określić domeny, aby przyspieszyć automatycznie.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true, `"PreferredDomain`":`"federated.example.edu`"}}") -DisplayName MultiDomainAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Aby utworzyć zasadę, aby włączyć uwierzytelnianie nazwy użytkownika i hasła dla użytkowników federacyjnych bezpośrednio w usłudze Azure Active Directory dla określonych aplikacji, uruchom następujące polecenie:

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuthPolicy -Type HomeRealmDiscoveryPolicy
```


Aby zobaczyć nowe zasady i uzyskać jego **ObjectID**, uruchom następujące polecenie:

``` powershell
Get-AzureADPolicy
```


Aby zastosować zasadę HRD, po jego utworzeniu, można przypisać go do wielu jednostek usługi aplikacji.

#### <a name="step-2-locate-the-service-principal-to-which-to-assign-the-policy"></a>Krok 2: Znajdź jednostkę usługi, dla której chcesz przypisać zasady  
Potrzebujesz **ObjectID** jednostki usług, do których chcesz przypisać zasady. Istnieje kilka sposobów, aby znaleźć **ObjectID** z jednostki usługi.    

Mogą korzystać z portalu lub możesz zbadać [programu Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity). Można także przejść do [Eksploratora programu Graph](https://developer.microsoft.com/graph/graph-explorer) i zaloguj się do swojego konta usługi Azure AD, aby wyświetlić nazwy główne usług wszystkich w organizacji. Ponieważ używasz programu PowerShell służy polecenie cmdlet polecenia get-AzureADServicePrincipal Aby wyświetlić listę nazw głównych usług i ich identyfikatorów.

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>Krok 3: Przypisz zasady do jednostki usługi  
Po utworzeniu **ObjectID** jednostki usługi aplikacji, dla której chcesz skonfigurować automatycznego przyspieszenia, uruchom następujące polecenie. To polecenie powoduje skojarzenie zasad HRD, który został utworzony w kroku 1 przy użyciu jednostki usługi, która zlokalizowanego w kroku 2.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

Możesz powtórzyć to polecenie dla każdej jednostki usługi, do której chcesz dodać zasady.

W przypadku, gdy aplikacja ma już przypisane zasady HomeRealmDiscovery nie będzie mógł dodać drugi.  W takim przypadku należy zmienić definicję zasad odnajdowania obszaru macierzystego, który jest przypisany do aplikacji, aby dodać dodatkowe parametry.

#### <a name="step-4-check-which-application-service-principals-your-hrd-policy-is-assigned-to"></a>Krok 4: Sprawdź jednostek usług aplikacji, której przypisano zasad HRD
Aby sprawdzić, które aplikacje mają skonfigurowanych zasad HRD, użyj **Get AzureADPolicyAppliedObject** polecenia cmdlet. Przekaż go **ObjectID** zasady, które chcesz sprawdzić.

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>Krok 5. Gotowe!
Wypróbuj aplikację, aby sprawdzić, czy nowe zasady działają.

### <a name="example-list-the-applications-for-which-hrd-policy-is-configured"></a>Przykład: Wyświetlanie listy aplikacji, dla których HRD skonfigurowano zasady

#### <a name="step-1-list-all-policies-that-were-created-in-your-organization"></a>Krok 1: Lista wszystkich zasad, które zostały utworzone w Twojej organizacji 

``` powershell
Get-AzureADPolicy
```

Uwaga **ObjectID** , którą chcesz listy przypisań dla zasad.

#### <a name="step-2-list-the-service-principals-to-which-the-policy-is-assigned"></a>Krok 2: Lista jednostki usługi, do których przypisano zasad  

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```

### <a name="example-remove-an-hrd-policy-for-an-application"></a>Przykład: Usuwanie zasad HRD dla aplikacji
#### <a name="step-1-get-the-objectid"></a>Krok 1: Pobierz identyfikator obiektu
Użyj poprzedniego przykładu, aby uzyskać **ObjectID** zasad i który nazwy głównej usługi aplikacji, z którym chcesz go usunąć. 

#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>Krok 2: Usuń przypisanie zasad z nazwy głównej usługi aplikacji  

``` powershell
Remove-AzureADApplicationPolicy -ObjectId <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-to-which-the-policy-is-assigned"></a>Krok 3: Sprawdź usuwania, wyświetlając listę jednostek usług, do których przypisano zasad 

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
## <a name="next-steps"></a>Kolejne kroki
- Aby uzyskać więcej informacji na temat działania uwierzytelniania w usłudze Azure AD, zobacz [scenariusze uwierzytelniania dla usługi Azure AD](../develop/authentication-scenarios.md).
- Aby uzyskać więcej informacji dotyczących użytkownika logowania jednokrotnego, zobacz [dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](configure-single-sign-on-portal.md).
- Odwiedź stronę [przewodnik dewelopera usługi Active Directory](../develop/v1-overview.md) omówienie całą zawartość związane z programowaniem.
