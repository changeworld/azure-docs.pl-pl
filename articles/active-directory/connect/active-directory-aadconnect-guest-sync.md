---
title: Synchronizowanie kont użytkowników użytkowników gościa, korzystających z poczty e-mail w celu logowania do platformy Azure | Dokumenty Microsoft
description: W tym artykule wyjaśniono, jak zsynchronizować konta użytkowników gościa, które umożliwia alternatywnego Identyfikatora logowania do aplikacji.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/19/2018
ms.author: billmath
ms.openlocfilehash: d21f124858a7f98227eb301a97b9837e3adbba68
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="synchronizing-guest-user-accounts-that-use-email-for-sign-in-preview"></a>Synchronizowanie konta użytkownika gościa, korzystających z poczty e-mail w celu logowania (wersja zapoznawcza)

>[!NOTE]
> Ta funkcja jest obecnie w wersji zapoznawczej.

Poniższy scenariusz adresów sytuacji, w których może mieć użytkowników zewnętrznych w lokalnej środowiska AD, takich jak partnerów, używający alternatywna metoda logowania.

W powyższym przykładzie Morin Greta działa dla firmy Fabrikam, i ma następujący adres e-mail: nmorin@fabrikam.com. Greta jest partnera z firmy Contoso i potrzebuje dostępu do niektórych aplikacji, które firma Contoso ma. Contoso utworzył konto Greta i został skierowany Greta na potrzeby jej adresu e-mail logowania do aplikacji.

Dla swoich lokalnych aplikacji w tym scenariuszu pracuje ponosić. Ale teraz Contoso jest przeniesienie tych aplikacji w chmurze i chce mieć to samo środowisko korzystania w swoim partnerom.

Ten scenariusz dotyczy ta sytuacja.


## <a name="pre-requisites-and-assumptions"></a>Wymagania wstępne i założenia
Ta sekcja zawiera listę wymagania wstępne i założenia, które należy zwrócić uwagę przed przystąpieniem do konfigurowania tego scenariusza.

### <a name="pre-requisites"></a>Wymagania wstępne
- Poświadczenia administratora globalnego, aby skonfigurować usługi Azure AD Connect, zweryfikować domeny, a następnie skonfigurować ustawienia domeny federacyjnej
- Azure AD Connect wersji 1.1.524.0 lub nowszej
- Zweryfikowanej domeny można ustawić nazwy UPN użytkowników zewnętrznych chmury (przykład: bmcontoso.com).
- Uwierzytelnianie użytkowników zewnętrznych usługi federacyjnej. Jeśli używasz usług AD FS, musi być 2012 R2 lub nowszy
- MSOL programu PowerShell w wersji 1.1 jest zainstalowany na komputerze, aby sprawdzić ustawienia federacji. Aby uzyskać więcej informacji, zobacz [usłudze Azure ActiveDirectory (MSOnline)](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0).


### <a name="assumptions"></a>Wartości domyślne 
- Azure AD Connect ma już ustawiania i została pomyślnie zainstalowana. Aby uzyskać informacje dotyczące sposobu instalowania Azure AD Connect, zobacz [Azure AD Connect i Federacji](active-directory-aadconnectfed-whatis.md).
Ten dokument sprawia, że następujące założenia:
- Konfigurowanie usługi federacyjnej i że jest pomyślnie uwierzytelniania użytkowników.
- Użytkownicy zewnętrzni mogą uwierzytelniać przy użyciu adresu e-mail zewnętrznych.
- - Korzystanie z alternatywnego Identyfikatora do logowania ma została przygotowana i skonfigurowana. Użytkownicy mogą uwierzytelniać za pomocą ich alternatywny identyfikator. Aby uzyskać dodatkowe informacje na temat konfigurowania alternatywny identyfikator z usługami AD FS, zobacz [Konfigurowanie alternatywnego Identyfikatora logowania](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id).

## <a name="task-1--prepare-the-environment"></a>Zadanie 1: Przygotowanie środowiska
Następujące zadanie jest informacyjny, aby rozpocząć synchronizowanie kont zewnętrznych, tak aby ich można Zaloguj się przy użyciu dyskietki i, takich jak atrybut poczty.

Zdefiniuj elementy w tabeli poniżej przed przejściem do drugiego zadania.

![Architektura](media/active-directory-aadconnect-guest-sync/guest2.png)

|Aspekt środowiska|Go do czego służy?|Wdrożenia w środowisku|
|-----|-----|-----|
|Atrybut nazwy głównej użytkownika w chmurze|Atrybut, który wypełnia UPN obiektów użytkownika zewnętrznego w chmurze. Sufiks nazwy UPN kont zewnętrznych musi być zdefiniowana w wymagania wstępne. Jest to zweryfikowanej domeny.|* Przykład: UserPrincipalName (nmorin@bmcontoso.com)|
|Adresem logowania|Atrybut, który będzie wpisz użytkowników zewnętrznych podczas logowania. Ten atrybut musi mieć format adresu e-mail i w większości przypadków odpowiada on właściwy adres e-mail użytkownika zewnętrznego.|* Przykład: poczty (nmorin@fabrikam.com)|
|Azure AD Connect zakresu filtru|Filtr, który umożliwia przeznaczonych dla tożsamości zewnętrznych do zakresu reguły synchronizacji zdefiniowane w dalszej części tego przewodnika. Typowe sposoby zakresu obejmują: wstępnie zdefiniowane jednostki Organizacyjnej w organizacji, określonych konwencji nazewnictwa, określoną domenę,... itd.|* Przykład: OU zawiera Externals|
|Dzierżawy usługi Azure AD|Nazwa dzierżawy usługi Azure AD, w jakiej jest wyświetlana do programu Azure AD Connect.|Przykład: contoso.onmicrosoft.com|

Poniższy zrzut ekranu ma trzy pola określone.
- **Externals** jednostki Organizacyjnej, który jest używany w Azure AD Connect zakresu filtru i lokalizacja użytkowników zewnętrznych.
- **Poczty** atrybut, który służy do logowania przez użytkowników zewnętrznych.
- **UserPrincipalName** atrybut, który jest zweryfikowanej domeny, który środowiska lokalnego jest Sfederowane przy użyciu.

![Użytkownik](media/active-directory-aadconnect-guest-sync/guest1.png)

## <a name="task-2--configure-azure-ad-connect"></a>Zadanie 2: Konfigurowanie usługi Azure AD Connect
Po utworzeniu powyższe informacje zdefiniowane, możesz teraz przystąpić do konfigurowania reguł synchronizacji Azure AD Connect. Aby skonfigurować zasady, użyj edytora reguły synchronizacji Azure AD Connect. Aby uzyskać więcej informacji o edytorze, zobacz [Deklaratywna inicjowania obsługi administracyjnej](active-directory-aadconnectsync-understanding-declarative-provisioning.md).

### <a name="how-to-configure-the-synchronization-rule"></a>Jak skonfigurować reguły synchronizacji
Poniższa procedura umożliwia skonfigurowanie usługi Azure AD Connect.

1. Otwórz Edytor reguł synchronizacji Azure AD Connect, przechodząc do **Edytor reguł synchronizacji Start - Azure AD Connect -**.
2. Na **Edytor reguł synchronizacji** ekranu, upewnij się, w kierunku **przychodzących** i po prawej stronie, kliknij przycisk **Dodaj nową regułę**.
3. Na **opis** , skonfiguruj następujące elementy i kliknij przycisk **dalej**.
    - **Nazwa** — wprowadź nazwę reguły 
    - **System połączony:** -lokalną środowiska usługi AD
    - **Typ obiektu połączony System:** -użytkownika
    - **Typ obiektu Metaverse:** — osoba
    - **Typ łącza:** -Join
    - **Pierwszeństwo:** — 90
    - 
![](media/active-directory-aadconnect-guest-sync/guest3.png)

4. Na **filtru zakresu** kliknij **Dodaj grupę**.
5. Umożliwia skonfigurowanie filtr listach rozwijanych. Wprowadź następujące polecenie, a następnie kliknij przycisk **dalej**. Ta akcja tworzy filtr, który ma zastosowanie tylko do obiektów znajdujących się w jednostce Organizacyjnej zewnętrznych.
    - **Atrybut** — nazwa wyróżniająca
    - **Operator** — zawiera
    - **Wartość** -Externals
 
 ![Filtr](media/active-directory-aadconnect-guest-sync/guest4.png)

6. Na **dołączyć reguły** kliknij **dalej**.
7. Na **przekształcenia** kliknij **dodać przekształcania**. Wprowadź następujące informacje:
    - **Dla przepływu** — stała
    - **Atrybut TARGET** -userType
    - **Źródło** - gości
8. Na **przekształcenia** kliknij **dodać przekształcania**. Wprowadź następujące informacje:
    - **Dla przepływu** — bezpośredniego
    - **Atrybut TARGET** -onPremisesUserPrincipalName
    - **Źródło** -poczty
9. Na **przekształcenia** kliknij **dodać przekształcania**. Wprowadź następujące informacje:
    - **Dla przepływu** — bezpośredniego
    - **Atrybut TARGET** -userPrincipalName
    - **Źródło** -userPrincipalName ![przekształcania](media/active-directory-aadconnect-guest-sync/guest5.png)
10. Kliknij pozycję **Add** (Dodaj). 
11. Na **Edytor reguł synchronizacji** ekranu, upewnij się, w kierunku **wychodzących** i po prawej stronie, kliknij przycisk **Dodaj nową regułę**.
12. Na **opis** strony skonfiguruj następujące elementy i kliknij przycisk **dalej**.
    - **Nazwa** — wprowadź nazwę reguły 
    - **System połączony:** -dzierżawy usługi Azure AD
    - **Typ obiektu połączony System:** -użytkownika
    - **Typ obiektu Metaverse:** — osoba
    - **Typ łącza** -Join
    - **Pierwszeństwo:** — 90
    - 
![Reguła](media/active-directory-aadconnect-guest-sync/guest6.png)

13. Na **filtru Scoping** kliknij **dalej**.
14. Na **dołączyć reguły** kliknij **dalej**.
15. Na **przekształcenia** kliknij **dodać przekształcania**.  Wprowadź następujące informacje:
    - **Dla przepływu** — bezpośredniego
    - **Atrybut TARGET** -userType
    - **Źródło** -userType
9. Na **przekształcenia** kliknij **dodać przekształcania**. Wprowadź następujące informacje:
    - **Dla przepływu** — bezpośredniego
    - **Atrybut TARGET** -onPremisesUserPrincipalName
    - **Źródło** -onPremisesUserPrincipalName ![przekształcania](media/active-directory-aadconnect-guest-sync/guest7.png)
10. Kliknij pozycję **Add** (Dodaj). 
11. Po skonfigurowaniu tych reguł należy przeprowadzić pełną synchronizację. Uruchom pełną synchronizację za pomocą programu PowerShell. Po zakończeniu synchronizacji można przystąpić do następnego kroku.

``` powershell
    Start-ADSyncSyncCycle -PolicyType Initial
```

## <a name="task-3--federation"></a>Zadanie 3: federacyjnego
Następujące zadanie ma charakter informacyjny na kilka rzeczy, które są potrzebne w celu zapewnienia w miejscu dla scenariusza, aby pracować.

Możesz sprawdzić ustawienia federacji z platformy Azure przy użyciu programu Azure AD PowerShell. Ten dokument używa MSOL środowiska PowerShell w wersji 1.1. Można zainstalować tę wersję [tutaj](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0).

### <a name="verify-the-federation-settings"></a>Sprawdź ustawienia Federacji
Użyj poniższej procedury, aby sprawdzić ustawienia federacji.
1. Otwórz program Windows PowerShell i łączenie z usługą Azure AD za pomocą następującego polecenia:
``` powershell
      Connect-MSOLservice
```
2.  Wprowadź poświadczenia administratora globalnego
3.  Teraz wprowadź następujące polecenie:
  ``` powershell
      Get-MSOLDomainFederationSettings
  ```
4.   Należy zauważyć, że powinny być zwracane informacje federacji. Uwaga **ActiveLogonUri** jest adres URL serwera federacyjnego.

  ![Federacja](media/active-directory-aadconnect-guest-sync/guest8.png)

### <a name="verify-alternate-login-id"></a>Sprawdź alternatywnego Identyfikatora logowania
Ten dokument używa usług AD FS jako dostawcy tożsamości (Idp). Jeśli używasz różnych Idp bardzo może następujące kroki.

1. Otwórz program Windows PowerShell i wpisz następujące polecenie:
   ```powershell
    Get-ADFSClaimsProviderTrust
   ```
2. Powinny być widoczne informacje usług AD FS.  Uwaga **AlternateLoginID** i **LookupForests**.

![ADFS](media/active-directory-aadconnect-guest-sync/guest9.png)

## <a name="task-4--testing"></a>Zadanie 4: testowanie
Aby zweryfikować, że to działa prawidłowo, należy zalogować się do punktu końcowego, który został skonfigurowany do uwierzytelniania przy użyciu dostawców tożsamości. Aby to sprawdzić, możemy wdrożyć witrynę sieci Web na platformie Azure i korzystają z następującego adresu url: contososampapp.azurewebsites.net

### <a name="verify-that-you-can-sign-in-with-the-alternate-id"></a>Sprawdź, czy użytkownik może logowania z alternatywnego Identyfikatora
1. Zaloguj się do punktu końcowego.</br>
![punkt końcowy](media/active-directory-aadconnect-guest-sync/guest10.png)
1. Wprowadź nazwy użytkownika i nastąpi ponowne przekierowanie do strony logowania federacyjnego.
![Federacyjna](media/active-directory-aadconnect-guest-sync/guest11.png)
1. Wprowadź swoje poświadczenia.
2. Powinny teraz być pomyślnie zalogowano.
![Powodzenie](media/active-directory-aadconnect-guest-sync/guest12.png)

## <a name="next-steps"></a>Kolejne kroki
- [Właściwości użytkownika współpracy usługi Azure Active Directory B2B](../../active-directory/active-directory-b2b-user-properties.md#key-properties-of-the-azure-ad-b2b-collaboration-user)
- [Konfigurowanie alternatywnego Identyfikatora logowania](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)
- [Azure AD Connect: Version release history](active-directory-aadconnect-version-history.md) (Azure AD Connect: historia wersji)
