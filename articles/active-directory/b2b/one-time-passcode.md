---
title: Jednorazowe uwierzytelnianie kodu dostępu dla użytkowników-gości B2B — usługa Azure AD
description: Jak używać jednorazowego kodu dostępu do poczty e-mail do uwierzytelniania użytkowników-gości B2B bez konieczności korzystania z konta Microsoft.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6d897bb983eb06baa4f1573f1f875eea8bb8afc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263376"
---
# <a name="email-one-time-passcode-authentication-preview"></a>Wysyłanie jednorazowego uwierzytelniania kodem dostępu (wersja zapoznawcza)

|     |
| --- |
| Jednorazowy kod dostępu poczty e-mail jest publiczną funkcją w wersji zapoznawczej usługi Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawców, zobacz [Dodatkowe warunki użytkowania w wersji Zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

W tym artykule opisano, jak włączyć uwierzytelnianie jednorazowego kodu dostępu poczty e-mail dla użytkowników-gości B2B. Funkcja jednorazowego kodu poczty e-mail uwierzytelnia użytkowników-gości B2B, gdy nie można ich uwierzytelnić za pomocą innych środków, takich jak usługa Azure AD, konto Microsoft (MSA) lub federacja Google. Dzięki jednorazowemu uwierzytelnianiu kodu dostępu nie ma potrzeby tworzenia konta Microsoft. Gdy użytkownik-gość realizuje zaproszenie lub uzyskuje dostęp do zasobu udostępnionego, może zażądać kodu tymczasowego, który jest wysyłany na ich adres e-mail. Następnie wprowadź ten kod, aby kontynuować logowanie.

Ta funkcja jest obecnie dostępna w wersji zapoznawczej (zobacz [Zaznaczenie wersji zapoznawczej](#opting-in-to-the-preview) poniżej). Po wyświetleniu podglądu ta funkcja zostanie domyślnie włączona dla wszystkich dzierżawców.

> [!NOTE]
> Jednorazowy kod dostępu użytkownicy muszą zalogować się przy użyciu `https://myapps.microsoft.com/?tenantid=<tenant id>` łącza `https://portal.azure.com/<tenant id>`zawierającego kontekst dzierżawy (na `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com`przykład lub , lub w przypadku zweryfikowanej domeny). Bezpośrednie łącza do aplikacji i zasobów również działają tak długo, jak zawierają kontekst dzierżawy. Użytkownicy-goście nie mogą obecnie logować się przy użyciu punktów końcowych, które nie mają kontekstu dzierżawy. Na przykład `https://myapps.microsoft.com`za `https://portal.azure.com`pomocą , lub Teams wspólny punkt końcowy spowoduje błąd. 

## <a name="user-experience-for-one-time-passcode-guest-users"></a>Środowisko użytkownika dla użytkowników gości z jednorazowym kodem dostępu
Dzięki jednorazowemu uwierzytelnianiu kodu dostępu użytkownik-gość może zrealizować zaproszenie, klikając bezpośredni link lub korzystając z wiadomości e-mail z zaproszeniem. W obu przypadkach wiadomość w przeglądarce wskazuje, że kod zostanie wysłany na adres e-mail użytkownika gościa. Użytkownik-gość wybiera **kod wysyłania:**
 
   ![Zrzut ekranu przedstawiający przycisk Wyślij kod](media/one-time-passcode/otp-send-code.png)
 
Kod dostępu jest wysyłany na adres e-mail użytkownika. Użytkownik pobiera kod z wiadomości e-mail i wprowadza go w oknie przeglądarki:
 
   ![Zrzut ekranu przedstawiający stronę Wprowadź kod](media/one-time-passcode/otp-enter-code.png)
 
Użytkownik-gość jest teraz uwierzytelniony i może zobaczyć zasób udostępniony lub kontynuować logowanie. 

> [!NOTE]
> Jednorazowe kody dostępu są ważne przez 30 minut. Po 30 minutach ten określony jednorazowy kod dostępu nie jest już prawidłowy, a użytkownik musi zażądać nowego. Sesje użytkownika wygasają po 24 godzinach. Po tym czasie użytkownik-gość otrzymuje nowy kod dostępu podczas uzyskiwania dostępu do zasobu. Wygaśnięcie sesji zapewnia dodatkowe bezpieczeństwo, zwłaszcza gdy użytkownik-gość opuszcza firmę lub nie potrzebuje już dostępu.

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>Kiedy użytkownik-gość otrzymuje jednorazowy kod dostępu?

Gdy użytkownik-gość zrealizuje zaproszenie lub użyje łącza do zasobu, który został im udostępniony, otrzyma jednorazowy kod dostępu, jeśli:
- Nie mają konta usługi Azure AD 
- Nie mają konta Microsoft 
- Zapraszająca dzierżawa nie utworzyła @gmail.com @googlemail.com federacji Google dla użytkowników i użytkowników 

W momencie zaproszenia nic nie wskazuje na to, że zapraszany użytkownik użyje jednorazowego uwierzytelniania kodem dostępu. Ale gdy użytkownik-gość loguje się, uwierzytelnianie jednorazowego kodu będzie metodą rezerwową, jeśli nie można użyć innych metod uwierzytelniania. 

Użytkownicy-goście, którzy uwierzytelniają się przy użyciu jednorazowych kodów dostępu w witrynie Azure Portal, mogą wyświetlać**relacje organizacyjne** >  **usługi Azure Active Directory Użytkownicy** > **z innych organizacji.**

![Zrzut ekranu przedstawiający jednorazowego użytkownika kodu z wartością źródło OTP](media/one-time-passcode/otp-users.png)

> [!NOTE]
> Gdy użytkownik zrealizuje jednorazowy kod dostępu, a później uzyska konto MSA, usługę Azure AD lub inne konto federacyjne, będzie nadal uwierzytelniany przy użyciu jednorazowego kodu dostępu. Jeśli chcesz zaktualizować ich metodę uwierzytelniania, możesz usunąć ich konto użytkownika gościa i ponownie je wyrozumiale.

### <a name="example"></a>Przykład
Użytkownik-gość alexdoe@gmail.com jest proszony o utworzenie firmy Fabrikam, która nie ma skonfigurowanej federacji Google. Alex nie ma konta Microsoft. Otrzymają one jednorazowy kod dostępu do uwierzytelniania.

## <a name="opting-in-to-the-preview"></a>Włączanie podglądu 
Może upłynąć kilka minut, aby działanie opt-in stało się skuteczne. Następnie tylko nowo zaproszeni użytkownicy, którzy spełniają powyższe warunki, będą używać jednorazowego uwierzytelniania kodem dostępu. Użytkownicy-goście, którzy wcześniej zrealizowali zaproszenie, będą nadal używać tej samej metody uwierzytelniania.

### <a name="to-opt-in-using-the-azure-ad-portal"></a>Aby wyrazić zgodę na korzystanie z portalu usługi Azure AD
1.  Zaloguj się do [witryny Azure portal](https://portal.azure.com/) jako administrator globalny usługi Azure AD.
2.  W okienku nawigacji wybierz pozycję **Azure Active Directory**.
3.  W obszarze **Zarządzanie**wybierz pozycję **Relacje organizacyjne**.
4.  Wybierz **pozycję Ustawienia**.
5.  W obszarze **Włącz jednorazowy kod dostępu do poczty e-mail dla gości (wersja zapoznawcza)** wybierz pozycję **Tak**.
 
### <a name="to-opt-in-using-powershell"></a>Aby zdecydować się na korzystanie z programu PowerShell

Najpierw musisz zainstalować najnowszą wersję modułu programu Azure AD PowerShell for Graph (AzureADPreview). Następnie określisz, czy zasady B2B już istnieją i uruchomisz odpowiednie polecenia.

#### <a name="prerequisite-install-the-latest-azureadpreview-module"></a>Warunek wstępny: Zainstaluj najnowszy moduł AzureADPreview
Najpierw sprawdź, które moduły zostały zainstalowane. Otwórz program PowerShell jako użytkownik z podwyższonym poziomem uprawnień (Uruchom jako administrator) i uruchom następujące polecenie:
 
```powershell  
Get-Module -ListAvailable AzureAD*
```

Jeśli moduł AzureADPreview nie wyświetla żadnego komunikatu informującego o tym, że istnieje nowsza wersja, wszystko jest gotowe. W przeciwnym razie — w zależności od rezultatu — wykonaj jedną z następujących czynności:

- Jeśli nie są zwracane żadne wyniki, uruchom następujące polecenie, aby zainstalować moduł AzureADPreview:
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- Jeśli w wynikach jest wyświetlany tylko moduł AzureAD, uruchom następujące polecenia, aby zainstalować moduł AzureADPreview: 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- Jeśli w wynikach jest wyświetlany tylko moduł AzureADPreview, ale pojawia się komunikat informujący o tym, że istnieje nowsza wersja, uruchom następujące polecenia, aby zaktualizować moduł: 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
  ```

Może zostać wyświetlony monit z informacją, że instalujesz moduł z niezaufanego repozytorium. Dzieje się tak w sytuacji, gdy repozytorium PSGallery nie zostało ustawione wcześniej jako zaufane. Naciśnij klawisz **Y**, aby zainstalować moduł.

#### <a name="check-for-existing-policies-and-opt-in"></a>Sprawdź istniejące zasady i zdecyduj się

Następnie sprawdź, czy obecnie istnieje B2BManagementPolicy, uruchamiając następujące czynności:

```powershell 
$currentpolicy =  Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
$currentpolicy -ne $null
```
- Jeśli dane wyjściowe jest False, zasady obecnie nie istnieje. Utwórz nową B2BManagementPolicy i zapisz się do wersji zapoznawczej, uruchamiając następujące:

   ```powershell 
   $policyValue=@("{`"B2BManagementPolicy`":{`"PreviewPolicy`":{`"Features`":[`"OneTimePasscode`"]}}}")
   New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true
   ```

- Jeśli dane wyjściowe jest True, B2BManagementPolicy zasady obecnie istnieje. Aby zaktualizować zasady i zapisać się do wersji zapoznawczej, uruchom następujące czynności:
  
   ```powershell 
   $policy = $currentpolicy.Definition | ConvertFrom-Json
   $features=[PSCustomObject]@{'Features'=@('OneTimePasscode')}; $policy.B2BManagementPolicy | Add-Member 'PreviewPolicy' $features -Force; $policy.B2BManagementPolicy
   $updatedPolicy = $policy | ConvertTo-Json -Depth 3
   Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
   ```

## <a name="opting-out-of-the-preview-after-opting-in"></a>Rezygnacja z podglądu po wypowiedź
Może upłynąć kilka minut, aby działanie opt-out stało się skuteczne. Jeśli wyłączysz podgląd, użytkownicy-goście, którzy wykupili jednorazowy kod dostępu, nie będą mogli się zalogować. Można usunąć użytkownika gościa i ponownie go ponownie, aby umożliwić mu zalogowanie się ponownie przy użyciu innej metody uwierzytelniania.

### <a name="to-turn-off-the-preview-using-the-azure-ad-portal"></a>Aby wyłączyć podgląd przy użyciu portalu usługi Azure AD
1.  Zaloguj się do [witryny Azure portal](https://portal.azure.com/) jako administrator globalny usługi Azure AD.
2.  W okienku nawigacji wybierz pozycję **Azure Active Directory**.
3.  W obszarze **Zarządzanie**wybierz pozycję **Relacje organizacyjne**.
4.  Wybierz **pozycję Ustawienia**.
5.  W obszarze Włącz jednorazowy kod dostępu **do poczty e-mail dla gości (wersja zapoznawcza)** wybierz pozycję **Nie**.

### <a name="to-turn-off-the-preview-using-powershell"></a>Aby wyłączyć podgląd za pomocą programu PowerShell
Zainstaluj najnowszy moduł AzureADPreview, jeśli jeszcze go nie masz (zobacz [Warunek wstępny: Zainstaluj najnowszy moduł AzureADPreview](#prerequisite-install-the-latest-azureadpreview-module) powyżej). Następnie sprawdź, czy zasady podglądu kodu jednorazowego są obecnie istniejące, uruchamiając następujące elementy:

```powershell 
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
($currentPolicy -ne $null) -and ($currentPolicy.Definition -like "*OneTimePasscode*")
```

Jeśli dane wyjściowe są true, zrezygnować z podglądu, uruchamiając następujące:

```powershell 
$policy = $currentpolicy.Definition | ConvertFrom-Json
$policy.B2BManagementPolicy.PreviewPolicy.Features = $policy.B2BManagementPolicy.PreviewPolicy.Features.Where({$_ -ne "OneTimePasscode"})
$updatedPolicy = $policy | ConvertTo-Json -Depth 3
Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
```

