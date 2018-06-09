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
ms.date: 06/08/2018
ms.author: barbkess
ms.openlocfilehash: bc1f0341f4e1c07dc16522f5a2ae36fa2e64d1fb
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248779"
---
# <a name="configure-azure-active-directory-sign-in-behavior-for-an-application-by-using-a-home-realm-discovery-policy"></a>Konfigurowanie rejestrowania usługi Azure Active Directory w zachowaniu aplikacji za pomocą zasad odnajdowania obszaru macierzystego

Ten dokument zawiera wprowadzenie do konfigurowania zachowanie uwierzytelniania usługi Azure Active Directory dla użytkowników federacyjnych.   Obejmuje ona konfiguracji ograniczeń przyspieszenie automatycznie i uwierzytelniania dla użytkowników w Sfederowanych domen.

## <a name="home-realm-discovery"></a>Odnajdowanie obszaru macierzystego
Odnajdowanie obszaru macierzystego (HRD) to proces, który pozwala usłudze Azure Active Directory (Azure AD), aby określić, w którym użytkownik musi uwierzytelnić podczas logowania.  Gdy użytkownik zaloguje się do dzierżawy usługi Azure AD w celu uzyskania dostępu do zasobu lub do usługi Azure AD wspólnej strony logowania, ich wpisz nazwę użytkownika (UPN). Usługi Azure AD użyty do odnajdywania, gdy użytkownik musi się zalogować. 

Użytkownik może być konieczne przekierowanie do uwierzytelnienia do jednej z następujących lokalizacji:

- Dzierżawca domowej użytkownika (może to być jako zasób, którego użytkownik próbuje uzyskać dostęp do tej samej dzierżawy). 

- Microsoft account.  Użytkownik jest gościa w dzierżawie zasobów.

-  Dostawca tożsamości lokalnych, takich jak Active Directory Federation Services (AD FS).

- Inny dostawca tożsamości, która jest Sfederowane przy użyciu dzierżawy usługi Azure AD.

## <a name="auto-acceleration"></a>Akceleracja automatycznie 
Niektóre organizacje skonfigurować domeny w swojej dzierżawy usługi Azure Active Directory możliwości utworzenia federacji z innego dostawców tożsamości, takie jak usługi AD FS do uwierzytelniania użytkowników.  

Jeśli użytkownik zaloguje się do aplikacji, najpierw są wpisywane w stronę logowania w usłudze Azure AD. Po wpisywania ich nazwy UPN, jeśli są one domeny federacyjnej one są następnie przekierowanie do strony logowania w rozszerzeniu IdP obsługująca tej domeny. W pewnych okolicznościach Administratorzy mogą chcieć przekierować użytkowników do strony logowania, gdy są one zalogowanie się do określonych aplikacji. 

W związku z tym użytkownikom można pominąć stronę początkową usługi Azure Active Directory. Ten proces jest nazywany "Logowanie automatyczne przyspieszenie."

W przypadkach, gdy dzierżawcy jest Sfederowane do innego dostawców tożsamości dla logowania przyspieszenie automatycznie sprawia, że użytkownika logowania więcej uproszczone.  Możesz skonfigurować automatyczne przyspieszenie dla poszczególnych aplikacji.

>[!NOTE]
>Jeśli skonfigurujesz aplikację do przyspieszania automatycznie gości nie można zalogować. Jeśli nie podejmiesz użytkownika bezpośrednio do federacyjnych IdP uwierzytelniania, brak można dla nich wrócić do strony logowania usługi Azure Active Directory. Gość użytkowników, którzy mogą wymagać będą kierowane do innych dzierżawców lub zewnętrznych dostawców tożsamości, takie jak konto Microsoft, nie można zarejestrować do tej aplikacji, ponieważ są one pomijanie odnajdowania obszaru macierzystego krok.  

Istnieją dwa sposoby kontrolowania przyspieszenie automatycznie do federacyjnych IdP:   

- Użyć wskazówki domeny na żądania uwierzytelniania dla aplikacji. 
- Skonfiguruj zasady odnajdowania obszaru macierzystego, aby włączyć przyspieszenie automatycznie.

### <a name="domain-hints"></a>Wskazówki dotyczące domeny    
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
>Jeśli wskazówkę dotyczącą domeny jest uwzględniona w żądanie uwierzytelnienia, jego obecność przesłania przyspieszenie automatycznie ustawiona dla aplikacji w zasadach odnajdowanie obszaru macierzystego.

### <a name="home-realm-discovery-policy-for-auto-acceleration"></a>Home odnajdowania obszaru zasady dla przyspieszenie automatycznie
Niektóre aplikacje nie umożliwiają konfigurowanie, które wysyłają żądania uwierzytelnienia. W takich przypadkach nie jest można użyć wskazówki domeny do kontrolowania przyspieszenie automatycznie. Przyspieszenie automatycznie można skonfigurować za pomocą zasad, aby uzyskać takie samo zachowanie.  

## <a name="enable-direct-authentication-for-legacy-applications"></a>Włącz bezpośrednie uwierzytelnianie dla starszych aplikacji
Najlepszym rozwiązaniem jest dla aplikacji, aby korzystać z biblioteki usługi AAD i interakcyjne logowanie do uwierzytelniania użytkowników. Biblioteki zajmie się przepływów użytkownika federacyjnego.  Czasami starsze aplikacje nie są zapisywane do zrozumienia federacyjnego. Nie należy stosować odnajdowania obszaru macierzystego, a nie wchodzą w interakcje z właściwego punktu końcowego federacyjnych do uwierzytelnienia użytkownika. Jeśli zostanie wybrana, można użyć zasad Wyświetlają umożliwiające określonych starsze aplikacje, które przesyłania nazwy użytkownika i hasła poświadczenia umożliwiające uwierzytelnianie bezpośrednio w usłudze Azure Active Directory. Musi być włączony synchronizacja skrótów haseł. 

> [!IMPORTANT]
> Bezpośrednie uwierzytelniania należy włączyć tylko, jeśli masz włączony synchronizacja skrótów haseł i wiesz, że można używać do uwierzytelniania tej aplikacji bez żadnych zasad implementowany przez dostawców tożsamości użytkownika lokalnego. Wyłącz synchronizacji skrótów haseł, czy wyłączyć synchronizacja katalogów z AD Connect jakiejkolwiek przyczyny, należy usunąć tych zasad w celu uniemożliwienia bezpośredniego uwierzytelniania za pomocą skrótu stare hasło.

## <a name="set-hrd-policy"></a>Ustaw zasady HRD
Istnieją trzy kroki umożliwiające ustawienie zasad odnajdowanie obszaru macierzystego w aplikacji dla federacyjnego logowania automatycznego przyspieszenie lub bezpośredniego aplikacji opartych na chmurze:

1. Utwórz zasadę odnajdowanie obszaru macierzystego.

2. Znajdź nazwy głównej usługi, do którego ma zostać dołączony zasad.

3. Dołącz zasady do nazwy głównej usługi. 

Zasady tylko zaczynają obowiązywać dla określonej aplikacji, gdy są one dołączone do nazwy głównej usługi. 

Tylko jedna zasada HRD może być aktywny na nazwy głównej usługi w dowolnym momencie.  

Polecenia cmdlet programu Microsoft Azure Active Directory interfejsu API programu Graph bezpośrednio lub środowiska PowerShell usługi Azure Active Directory służy do tworzenia i zarządzania zasadami odnajdowanie obszaru macierzystego.

Interfejsu API programu Graph manipuluje zasad jest opisane w [operacje na zasadzie](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) artykuł w witrynie MSDN.

Poniżej przedstawiono przykładowe Wyświetlają definicji zasad:
    
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

**AccelerateToFederatedDomain** jest opcjonalna. Jeśli **AccelerateToFederatedDomain** ma wartość false, zasady nie ma wpływu na przyspieszenie automatycznie. Jeśli **AccelerateToFederatedDomain** jest wartość PRAWDA, a jest tylko jeden zweryfikowane i domeny federacyjnej w dzierżawy, a następnie użytkownicy zostaną wykonane proste do federacyjnych IdP w celu logowania się. PRAWDA, jeśli ma więcej niż jednej domeny zweryfikowane w dzierżawie, **PreferredDomain** musi być określona.

**PreferredDomain** jest opcjonalna. **PreferredDomain** powinny wskazywać domeny, do której w celu przyspieszenia. Można pominąć, jeśli dzierżawca ma tylko jedną domenę federacyjną.  Jeśli zostanie pominięty i ma więcej niż jeden zweryfikować domeny federacyjnej, zasady nie ma znaczenia.

 Jeśli **PreferredDomain** jest określona, musi on być zgodny domeny zweryfikowane, federacyjnych dla dzierżawcy. Wszyscy użytkownicy aplikacji musi mieć możliwość Zaloguj się do tej domeny.

**AllowCloudPasswordValidation** jest opcjonalna. Jeśli **AllowCloudPasswordValidation** ma wartość true, a następnie aplikacja może uwierzytelnić użytkownika federacyjnego z uwzględnieniem poświadczenia nazwy użytkownika i hasła bezpośrednio do punktu końcowego tokenu w usłudze Azure Active Directory. Działa tylko po włączeniu synchronizacji skrótów haseł.

### <a name="priority-and-evaluation-of-hrd-policies"></a>Priorytet i oceny zasad HRD
Odnajdowanie obszaru macierzystego zasad można tworzyć i następnie przypisane do określonych organizacji i nazwy główne usług. Oznacza to, czy jest możliwe w dla wielu zasady do zastosowania dla określonej aplikacji. Zasady HRD, które obowiązuje obowiązują następujące reguły:


- Jeśli wskazówkę dotyczącą domeny jest obecny w żądaniu uwierzytelnienia, dowolne zasady HRD jest ignorowany dla przyspieszenie automatycznie. Zachowanie jest określoną przez warunek domeny jest używany.

- W przeciwnym razie jeśli zasady jawnie jest przypisany do nazwy głównej usługi, jest wymuszone. 

- Jeśli nie istnieje żadne wskazówka domeny, a żadne zasady nie zostały wyraźnie przypisane do nazwy głównej usługi, zasady, które jawnie jest przypisany do organizacji nadrzędnej nazwy głównej usługi jest wymuszana. 

- Jeśli nie istnieje żadne wskazówka domeny, a żadne zasady nie został przypisany do nazwy głównej usługi lub organizacji, domyślne zachowanie HRD jest używany.

## <a name="tutorial-for-setting-hrd-policy-on-an-application"></a>Samouczek dotyczący ustawiania zasad odnajdowanie obszaru macierzystego w aplikacji 
Użyjemy polecenia cmdlet programu Azure AD PowerShell przechodzenia przez kilka scenariuszy, w tym:


- Konfigurowanie zasad HRD czy automatycznie przyspieszenie dla aplikacji w dzierżawie jednej domeny federacyjnej.

- Konfigurowanie zasad HRD czy automatycznie przyspieszenie aplikacji wiele domen, które są sprawdzane dla dzierżawy.

- Konfigurowanie zasad Wyświetlają włączyć starszej wersji aplikacji do kierowania uwierzytelnianie nazwy użytkownika i hasła do usługi Azure Active Directory dla użytkowników federacyjnych.

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

### <a name="example-set-hrd-policy-for-an-application"></a>Przykład: Set zasad odnajdowanie obszaru macierzystego dla aplikacji 

W tym przykładzie należy utworzyć zasadę która po przypisaniu do aplikacji albo: 
- Auto przyspiesza użytkowników do ekranu logowania usługi AD FS podczas ich są logowania do aplikacji przypadku pojedynczej domeny w dzierżawie. 
- Przyspiesza automatycznie użytkownikom usług AD FS ekranu logowania jest więcej niż jednej domeny federacyjnej w dzierżawie.
- Umożliwia znak nieinterakcyjnego nazwy użytkownika i hasła w bezpośrednio do usługi Azure Active Directory dla użytkowników federacyjnych, aplikacje, które zasady jest przypisany do.

#### <a name="step-1-create-an-hrd-policy"></a>Krok 1: Tworzenie zasad HRD

Następujące zasady auto przyspiesza użytkowników do ekranu logowania usługi AD FS podczas ich są logowania do aplikacji przypadku pojedynczej domeny w dzierżawie.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```
Następujące zasady auto przyspiesza użytkowników do usługi AD FS ekranu logowania jest więcej niż jednej domeny federacyjnej w dzierżawie. Jeśli masz więcej niż jedną domenę federacyjną, który uwierzytelnia użytkowników dla aplikacji, należy określić domeny w celu przyspieszenia automatycznie.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true, "PreferredDomain":"federated.example.edu"}}") -DisplayName MultiDomainAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Aby utworzyć zasadę, aby włączyć uwierzytelnianie nazwy użytkownika i hasła dla użytkowników federacyjnych bezpośrednio w usłudze Azure Active Directory dla określonych aplikacji, uruchom następujące polecenie:

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuthPolicy -Type HomeRealmDiscoveryPolicy
```


Aby wyświetlić nowe zasady i uzyskać jego **ObjectID**, uruchom następujące polecenie:

``` powershell
Get-AzureADPolicy
```


Aby zastosować zasady HRD po jej utworzeniu, można przypisać go do wiele elementów głównych z usługi aplikacji.

#### <a name="step-2-locate-the-service-principal-to-which-to-assign-the-policy"></a>Krok 2: Znajdź nazwy głównej usługi, do którego chcesz przypisać zasady  
Należy **ObjectID** podmiotów zabezpieczeń usługi, do których chcesz przypisać zasady. Istnieje kilka sposobów, aby znaleźć **ObjectID** podmiotów zabezpieczeń usługi.    

Mogą korzystać z portalu lub można zbadać [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity). Można także przejść do [narzędzie Explorer wykres](https://graphexplorer.cloudapp.net/) i zaloguj się do swojego konta usługi Azure AD, aby zobaczyć nazwy główne usług wszystkich organizacji. Ponieważ używasz programu PowerShell służy polecenie cmdlet get-AzureADServicePrincipal do tworzenia listy nazw głównych usług i ich identyfikatorów.

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>Krok 3: Przypisz zasady do Twojej nazwy głównej usługi  
Po utworzeniu **ObjectID** z aplikacji, dla której chcesz skonfigurować automatyczne przyspieszenie nazwy głównej usługi, uruchom następujące polecenie. To polecenie powoduje skojarzenie zasad HRD, które zostały utworzone w kroku 1 z nazwy głównej usługi, zlokalizowanego w kroku 2.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

To polecenie można powtórzyć dla każdej nazwy głównej usługi, do której chcesz dodać zasady.

W przypadku, gdy aplikacja już ma przypisane zasady HomeRealmDiscovery nie będzie można dodać drugi z nich.  W takim przypadku Zmień definicję zasad odnajdowania obszaru macierzystego, który jest przypisany do aplikacji, aby dodać dodatkowe parametry.

#### <a name="step-4-check-which-application-service-principals-your-hrd-policy-is-assigned-to"></a>Krok 4: Sprawdź, które Wyświetlają zasad jest przypisany do nazwy główne usług aplikacji
Aby sprawdzić, które aplikacje skonfigurowanych zasad odnajdowanie obszaru macierzystego, należy użyć **Get-AzureADPolicyAppliedObject** polecenia cmdlet. Przekaż go **ObjectID** zasady, które chcesz sprawdzić.

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>Krok 5: Gotowe!
Spróbuj aplikacji, aby sprawdzić, czy działa nowych zasad.

### <a name="example-list-the-applications-for-which-hrd-policy-is-configured"></a>Przykład: Lista aplikacji, które Wyświetlają skonfigurowano zasad

#### <a name="step-1-list-all-policies-that-were-created-in-your-organization"></a>Krok 1: Wyświetl listę wszystkich zasad, które zostały utworzone w Twojej organizacji 

``` powershell
Get-AzureADPolicy
```

Uwaga **ObjectID** zasady, które chcesz przydziałów listy.

#### <a name="step-2-list-the-service-principals-to-which-the-policy-is-assigned"></a>Krok 2: Listy nazwy główne usług, do których przypisano zasad  

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```

### <a name="example-remove-an-hrd-policy-for-an-application"></a>Przykład: Usuń zasady odnajdowanie obszaru macierzystego dla aplikacji
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
