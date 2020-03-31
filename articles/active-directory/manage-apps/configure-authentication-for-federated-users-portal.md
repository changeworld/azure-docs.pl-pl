---
title: Konfigurowanie automatycznego przyspieszania logowania przy użyciu funkcji Odnajdowanie obszaru rzeczywistego
description: Dowiedz się, jak skonfigurować zasady odnajdywania obszarów domowych dla uwierzytelniania usługi Azure Active Directory dla użytkowników federowanych, w tym automatyczne przyspieszanie i wskazówki dotyczące domeny.
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
ms.openlocfilehash: 60bfc964ffc394b3f79c9d279158003f383b7331
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943445"
---
# <a name="configure-azure-active-directory-sign-in-behavior-for-an-application-by-using-a-home-realm-discovery-policy"></a>Konfigurowanie zachowania logowania usługi Azure Active Directory dla aplikacji przy użyciu zasad odnajdowania obszaru macierzystego

Ten artykuł zawiera wprowadzenie do konfigurowania zachowania uwierzytelniania usługi Azure Active Directory dla użytkowników federowanych. Obejmuje konfigurację ograniczeń automatycznego przyspieszania i uwierzytelniania dla użytkowników w domenach federacyjnych.

## <a name="home-realm-discovery"></a>Odnajdowanie obszaru głównego
Home Realm Discovery (HRD) to proces, który umożliwia usłudze Azure Active Directory (Azure AD) określenie, gdzie użytkownik musi uwierzytelnić się w czasie logowania.  Gdy użytkownik loguje się do dzierżawy usługi Azure AD, aby uzyskać dostęp do zasobu lub do wspólnej strony logowania usługi Azure AD, wpisuje nazwę użytkownika (UPN). Usługa Azure AD używa tego, aby odkryć, gdzie użytkownik musi się zalogować. 

Użytkownik może wymagać przewieźć do jednej z następujących lokalizacji, aby uwierzytelnić:

- Dzierżawa domowa użytkownika (może być tą samą dzierżawą co zasób, do który użytkownik próbuje uzyskać dostęp). 

- konto Microsoft.  Użytkownik jest gościem w dzierżawie zasobów.

-  Lokalny dostawca tożsamości, taki jak Usługi federacyjne Active Directory (AD FS).

- Inny dostawca tożsamości, który jest sfederowany z dzierżawą usługi Azure AD.

## <a name="auto-acceleration"></a>Automatyczne przyspieszanie 
Niektóre organizacje konfigurują domeny w dzierżawie usługi Azure Active Directory w celu zwiednia z innym protokołem IDP, takim jak usługi AD FS do uwierzytelniania użytkowników.  

Gdy użytkownik loguje się do aplikacji, są one po raz pierwszy prezentowane ze stroną logowania usługi Azure AD. Po wpisaniu nazwy UPN, jeśli znajdują się w domenie federacyjnej, są one następnie przekierowywane na stronę logowania do idp obsługującej tę domenę. W pewnych okolicznościach administratorzy mogą chcieć kierować użytkowników do strony logowania podczas logowania się do określonych aplikacji. 

W rezultacie użytkownicy mogą pominąć początkową stronę usługi Azure Active Directory. Ten proces jest określany jako "automatyczne przyspieszanie logowania".

W przypadkach, gdy dzierżawa jest sfederowana do innego IdP do logowania, automatyczne przyspieszanie sprawia, że logowanie użytkownika jest bardziej usprawnione.  Można skonfigurować automatyczne przyspieszanie dla poszczególnych aplikacji.

>[!NOTE]
>Jeśli skonfigurujesz aplikację do automatycznego przyspieszania, użytkownicy-goście nie mogą się zalogować. Jeśli użytkownik bezpośrednio do federacyjnego idp do uwierzytelniania, nie ma sposobu, aby uzyskać dla nich, aby wrócić do strony logowania usługi Azure Active Directory. Użytkownicy-goście, którzy mogą wymagać przekierowania do innych dzierżaw lub zewnętrznego idp, takiego jak konto Microsoft, nie mogą zalogować się do tej aplikacji, ponieważ pomijają krok odnajdowania obszaru macierzystego.  

Istnieją dwa sposoby kontrolowania automatycznego przyspieszania do federacyjnego idP:   

- Użyj wskazówki domeny dotyczące żądań uwierzytelniania aplikacji. 
- Skonfiguruj zasady odnajdowania obszaru macierzystego, aby włączyć automatyczne przyspieszanie.

### <a name="domain-hints"></a>Wskazówki dotyczące domeny    
Wskazówki dotyczące domeny są dyrektywami, które są zawarte w żądaniu uwierzytelnienia z aplikacji. Mogą one służyć do przyspieszenia użytkownika do ich federacyjnej strony logowania IdP. Lub mogą być używane przez aplikację z wieloma dzierżawami, aby przyspieszyć użytkownika bezpośrednio do strony logowania usługi Azure AD dla ich dzierżawy.  

Na przykład aplikacja "largeapp.com" może umożliwić klientom dostęp do aplikacji pod niestandardowym adresem URL "contoso.largeapp.com". Aplikacja może również zawierać wskazówkę dotyczącą domeny, aby contoso.com w żądaniu uwierzytelniania. 

Składnia wskazówek dotyczących domeny różni się w zależności od używanego protokołu i jest zazwyczaj skonfigurowana w aplikacji.

**Federacja WS**: whr=contoso.com w ciągu zapytania.

**SAML**: Żądanie uwierzytelnienia SAML zawierające wskazówkę dotyczącą domeny lub ciąg zapytania whr=contoso.com.

**Open ID Connect**: Ciąg zapytania domain_hint=contoso.com. 

Jeśli wskazówka domeny znajduje się w żądaniu uwierzytelniania z aplikacji, a dzierżawa jest sfederowana z tą domeną, usługa Azure AD próbuje przekierować logowanie do idP skonfigurowanego dla tej domeny. 

Jeśli wskazówka domeny nie odwołuje się do zweryfikowanej domeny federacyjnej, jest ignorowana i wywoływane jest normalne odnajdowanie obszaru macierzystego.

Aby uzyskać więcej informacji na temat automatycznego przyspieszania przy użyciu wskazówek dotyczących domeny obsługiwanych przez usługę Azure Active Directory, zobacz blog dotyczący mobilności i zabezpieczeń w [przedsiębiorstwie](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/).

>[!NOTE]
>Jeśli wskazówka domeny jest uwzględniona w żądaniu uwierzytelniania, jej obecność zastępuje automatyczne przyspieszanie, które jest ustawione dla aplikacji w zasadach HRD.

### <a name="home-realm-discovery-policy-for-auto-acceleration"></a>Zasady home realm discovery dotyczące automatycznego przyspieszania
Niektóre aplikacje nie umożliwiają skonfigurowania emitowanego przez nie żądania uwierzytelniania. W takich przypadkach nie jest możliwe użycie wskazówek dotyczących domeny do kontrolowania automatycznego przyspieszania. Automatyczne przyspieszanie można skonfigurować za pomocą zasad, aby osiągnąć to samo zachowanie.  

## <a name="enable-direct-authentication-for-legacy-applications"></a>Włącz uwierzytelnianie bezpośrednie dla starszych aplikacji
Najlepszym rozwiązaniem jest używanie bibliotek usługi AAD i interaktywnego logowania do uwierzytelniania użytkowników. Biblioteki zajmują się przepływami użytkowników federacyjnego.  Czasami starsze aplikacje nie są zapisywane w celu zrozumienia federacji. Nie wykonują odnajdywania obszaru macierzystego i nie wchodzą w interakcje z poprawnym sfederowanym punktem końcowym w celu uwierzytelnienia użytkownika. Jeśli wybierzesz, możesz użyć zasad HRD, aby włączyć określone starsze aplikacje, które przesyłają poświadczenia nazwy użytkownika/hasła do uwierzytelniania bezpośrednio za pomocą usługi Azure Active Directory. Synchronizacja skrótów haseł musi być włączona. 

> [!IMPORTANT]
> Włącz uwierzytelnianie bezpośrednie tylko wtedy, gdy masz włączone synchronizowanie skrótów haseł i wiesz, że uwierzytelnianie tej aplikacji jest w porządku bez żadnych zasad zaimplementowanych przez lokalny idP. Jeśli wyłączysz synchronizację skrótów haseł lub wyłączysz synchronizację katalogów z usługą AD Connect z jakiegokolwiek powodu, należy usunąć tę zasadę, aby zapobiec możliwości bezpośredniego uwierzytelniania przy użyciu przestarzałego skrótu hasła.

## <a name="set-hrd-policy"></a>Ustawianie zasad HRD
Istnieją trzy kroki ustawiania zasad HRD w aplikacji dla federacyjnych logowania automatycznego przyspieszania lub bezpośrednich aplikacji opartych na chmurze:

1. Tworzenie zasad HRD.

2. Znajdź jednostkę usługi, do której należy dołączyć zasady.

3. Dołącz zasady do jednostki usługi. 

Zasady zaczynają obowiązywać tylko dla określonej aplikacji, gdy są one dołączone do jednostki usługi. 

Tylko jedna zasada HRD może być aktywna na jednostkę usługi w dowolnym momencie.  

Polecenia cmdlet programu Azure Active Directory PowerShell umożliwiają tworzenie zasad HRD i zarządzanie nimi.

Poniżej przedstawiono przykładową definicję zasad HRD:
    
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

Typ zasad to "HomeRealmDiscoveryPolicy".

**AccelerateToFederatedDomain** jest opcjonalna. Jeśli **AccelerateToFederatedDomain** jest false, zasady nie ma wpływu na automatyczne przyspieszanie. Jeśli **AccelerateToFederatedDomain** jest true i istnieje tylko jedna zweryfikowana i federacyjne domeny w dzierżawie, a następnie użytkownicy zostaną podjęte bezpośrednio do federacyjnego IdP do logowania. Jeśli jest true i istnieje więcej niż jedna zweryfikowana domena w dzierżawie, **PreferredDomain** musi być określony.

**PreferredDomain** jest opcjonalny. **PreferredDomain** powinien wskazywać domenę, do której należy przyspieszyć. Można go pominąć, jeśli dzierżawca ma tylko jedną domenę federacyjnej.  Jeśli zostanie pominięta, a istnieje więcej niż jedna zweryfikowana domena federacyjne, zasada ta nie ma wpływu.

 Jeśli **preferredDomain** jest określony, musi odpowiadać zweryfikowanej, federacyjnej domeny dla dzierżawy. Wszyscy użytkownicy aplikacji muszą mieć możliwość logowania się do tej domeny.

**AllowCloudPasswordValidation** jest opcjonalne. Jeśli **AllowCloudPasswordValidation** jest prawdą, aplikacja może uwierzytelnić użytkownika federacyjnego, przedstawiając poświadczenia nazwy użytkownika/hasła bezpośrednio do punktu końcowego tokenu usługi Azure Active Directory. Działa to tylko wtedy, gdy synchronizacja skrótów haseł jest włączona.

### <a name="priority-and-evaluation-of-hrd-policies"></a>Priorytet i ocena polityk HRD
Zasady HRD można tworzyć, a następnie przypisywać do określonych organizacji i podmiotów usług. Oznacza to, że jest możliwe dla wielu zasad do zastosowania do określonej aplikacji. Zasady hrd, które wchodzą w życie, są zgodne z następującymi zasadami:


- Jeśli wskazówka domeny jest obecny w żądaniu uwierzytelniania, wszystkie zasady HRD jest ignorowana dla automatycznego przyspieszania. Zachowanie, które jest określone przez wskazówkę domeny jest używany.

- W przeciwnym razie jeśli zasady są jawnie przypisane do jednostki usługi, jest wymuszana. 

- Jeśli nie ma żadnej wskazówki domeny, a żadna zasada nie jest jawnie przypisana do jednostki usługi, wymuszana jest zasada, która jest jawnie przypisana do nadrzędnej organizacji jednostki usługi. 

- Jeśli nie ma żadnej wskazówki domeny i nie przypisano żadnych zasad do jednostki usługi lub organizacji, używane jest domyślne zachowanie hrd.

## <a name="tutorial-for-setting-hrd-policy-on-an-application"></a>Samouczek dotyczący ustawiania zasad HRD w aplikacji 
Użyjemy poleceń cmdlet programu Azure AD PowerShell, aby przejść przez kilka scenariuszy, w tym:


- Konfigurowanie zasad HRD do automatycznego przyspieszania dla aplikacji w dzierżawie z jedną domeną federacyjnej.

- Konfigurowanie zasad HRD do automatycznego przyspieszania dla aplikacji do jednej z kilku domen, które są weryfikowane dla dzierżawy.

- Konfigurowanie zasad HRD w celu umożliwienia starszej aplikacji bezpośredniego uwierzytelniania nazwy użytkownika/hasła w usłudze Azure Active Directory dla użytkownika federacyjnego.

- Wyświetlanie listy aplikacji, dla których skonfigurowano zasady.


### <a name="prerequisites"></a>Wymagania wstępne
W poniższych przykładach można utworzyć, zaktualizować, połączyć i usunąć zasady na jednostki usługi aplikacji w usłudze Azure AD.

1.  Aby rozpocząć, pobierz najnowszą wersję poleceń cmdlet usługi Azure POWERShell. 

2.  Po pobraniu poleceń cmdlet programu Azure AD PowerShell uruchom polecenie Connect, aby zalogować się do usługi Azure AD za pomocą konta administratora:

    ``` powershell
    Connect-AzureAD -Confirm
    ```
3.  Uruchom następujące polecenie, aby wyświetlić wszystkie zasady w organizacji:

    ``` powershell
    Get-AzureADPolicy
    ```

Jeśli nic nie jest zwracany, oznacza to, że nie masz żadnych zasad utworzonych w dzierżawie.

### <a name="example-set-hrd-policy-for-an-application"></a>Przykład: Ustawianie zasad HRD dla aplikacji 

W tym przykładzie należy utworzyć zasadę, która jest przypisana do aplikacji: 
- Automatycznie przyspiesza użytkowników do ekranu logowania usług AD FS podczas logowania się do aplikacji, gdy istnieje jedna domena w dzierżawie. 
- Automatycznie przyspiesza użytkowników do ekranu logowania usług AD FS jest więcej niż jedna domena federacyjne w dzierżawie.
- Włącza nieinterakcyjne logowanie się między użytkownikami/hasłem bezpośrednio do usługi Azure Active Directory dla użytkowników federacyjnych dla aplikacji, do dla które są przypisane zasady.

#### <a name="step-1-create-an-hrd-policy"></a>Krok 1: Tworzenie zasad HRD

Poniższe zasady automatycznie przyspiesza użytkowników do ekranu logowania usług AD FS podczas logowania się do aplikacji, gdy istnieje jedna domena w dzierżawie.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```
Poniższa zasada automatycznie przyspiesza użytkowników do ekranu logowania usług AD FS, na który znajduje się więcej niż jedna domena federacyjne w dzierżawie. Jeśli masz więcej niż jedną domenę federacyjne, która uwierzytelnia użytkowników dla aplikacji, musisz określić domenę, aby automatycznie przyspieszyć.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true, `"PreferredDomain`":`"federated.example.edu`"}}") -DisplayName MultiDomainAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Aby utworzyć zasady umożliwiające uwierzytelnianie nazwy użytkownika/hasła dla użytkowników federacyjnych bezpośrednio za pomocą usługi Azure Active Directory dla określonych aplikacji, uruchom następujące polecenie:

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuthPolicy -Type HomeRealmDiscoveryPolicy
```


Aby wyświetlić nową zasadę i uzyskać jej **objectID**, uruchom następujące polecenie:

``` powershell
Get-AzureADPolicy
```


Aby zastosować zasady HRD po utworzeniu, można przypisać go do wielu podmiotów usługi aplikacji.

#### <a name="step-2-locate-the-service-principal-to-which-to-assign-the-policy"></a>Krok 2: Zlokalizuj jednostkę usługi, do której należy przypisać zasady  
Identyfikator **objectid** podmiotów usługi, do których chcesz przypisać zasady. Istnieje kilka sposobów, aby znaleźć **ObjectID** podmiotów usługi.    

Można użyć portalu lub kwerendy [Microsoft Graph](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta). Możesz również przejść do [narzędzia Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) i zalogować się do konta usługi Azure AD, aby wyświetlić wszystkie jednostki usługi organizacji. 

Ponieważ używasz programu PowerShell, można użyć następującego polecenia cmdlet do listy podmiotów usługi i ich identyfikatorów.

``` powershell
Get-AzureADServicePrincipal
```

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>Krok 3: Przypisz zasady do jednostki usługi  
Po **objectID** jednostki usługi aplikacji, dla której chcesz skonfigurować automatyczne przyspieszanie, uruchom następujące polecenie. To polecenie kojarzy zasady HRD utworzone w kroku 1 z jednostką usługi, która znajduje się w kroku 2.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

Można powtórzyć to polecenie dla każdej jednostki usługi, do której chcesz dodać zasady.

W przypadku, gdy aplikacja ma już homerealmdiscovery zasady przypisane, nie będzie można dodać drugi.  W takim przypadku należy zmienić definicję zasady odnajdywania obszarów macierzystych, która jest przypisana do aplikacji, aby dodać dodatkowe parametry.

#### <a name="step-4-check-which-application-service-principals-your-hrd-policy-is-assigned-to"></a>Krok 4: Sprawdź, które podmioty usługi aplikacji są przypisane do
Aby sprawdzić, które aplikacje mają skonfigurowane zasady HRD, należy użyć polecenia cmdlet **Get-AzureADPolicyAppliedObject.** Przekaż go **ObjectID** zasad, które chcesz sprawdzić.

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>Krok 5: Gotowe!
Wypróbuj aplikację, aby sprawdzić, czy nowe zasady działają.

### <a name="example-list-the-applications-for-which-hrd-policy-is-configured"></a>Przykład: Lista aplikacji, dla których skonfigurowano zasady HRD

#### <a name="step-1-list-all-policies-that-were-created-in-your-organization"></a>Krok 1: Lista wszystkich zasad utworzonych w organizacji 

``` powershell
Get-AzureADPolicy
```

Zwróć uwagę **na identyfikator obiektu** zasad, dla których chcesz wyświetlić listę przypisań.

#### <a name="step-2-list-the-service-principals-to-which-the-policy-is-assigned"></a>Krok 2: Lista podmiotów świadczących usługi, do których przypisano zasady  

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

### <a name="example-remove-an-hrd-policy-for-an-application"></a>Przykład: Usuwanie zasad HRD dla aplikacji
#### <a name="step-1-get-the-objectid"></a>Krok 1: Pobierz ObjectID
Użyj poprzedniego przykładu, aby uzyskać **ObjectID** zasad i podmiotu usługi aplikacji, z którego chcesz go usunąć. 

#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>Krok 2: Usuń przypisanie zasad z jednostki usługi aplikacji  

``` powershell
Remove-AzureADServicePrincipalPolicy -id <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-to-which-the-policy-is-assigned"></a>Krok 3: Sprawdź usunięcie, wymieniając podmioty świadczące usługi, do których przypisano zasady 

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```
## <a name="next-steps"></a>Następne kroki
- Aby uzyskać więcej informacji na temat funkcjonowania uwierzytelniania w usłudze Azure AD, zobacz [Scenariusze uwierzytelniania dla usługi Azure AD](../develop/authentication-scenarios.md).
- Aby uzyskać więcej informacji na temat logowania jednokrotnego użytkownika, zobacz [Logowanie jednokrotne w aplikacjach usługi Azure Active Directory](what-is-single-sign-on.md).
- Odwiedź [platformę tożsamości firmy Microsoft,](../develop/v2-overview.md) aby uzyskać omówienie całej zawartości związanej z deweloperem.
