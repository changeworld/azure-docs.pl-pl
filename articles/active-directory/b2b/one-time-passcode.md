---
title: Jednorazowy kod dostępu uwierzytelniania dla użytkowników-gości B2B — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak korzystać z poczty E-mail jednorazowy kod dostępu do uwierzytelniania użytkowników-gości B2B bez konieczności korzystania z konta Microsoft.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b817346c37ec43fd66d166684f5d51ecb5a9718
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59799443"
---
# <a name="email-one-time-passcode-authentication-preview"></a>Uwierzytelnianie jednorazowy kod dostępu konta e-mail (wersja zapoznawcza)

|     |
| --- |
| Jednorazowy kod dostępu poczty e-mail to funkcja publicznej wersji zapoznawczej usługi Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz temat [Dodatkowe warunki użytkowania dotyczące wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

W tym artykule opisano sposób włączania uwierzytelniania jednorazowy kod dostępu poczty E-mail dla użytkowników-gości B2B. Funkcję powiadomienia E-mail jednorazowy kod dostępu uwierzytelnia użytkowników-gości B2B nie może zostać uwierzytelnione za pośrednictwem innych metod, takich jak usługa Azure AD, konta Microsoft (MSA) lub federacyjnych Google. Przy użyciu uwierzytelniania jednorazowy kod dostępu nie ma potrzeby do utworzenia konta Microsoft. Gdy użytkownik-Gość umarza zaproszenia, lub uzyskuje dostęp do udostępnionego zasobu, będą one żądać tymczasowy kod, który jest wysyłany do swojego adresu e-mail. Następnie wprowadź ten kod, aby kontynuować logowanie.

Ta funkcja jest obecnie dostępna w wersji zapoznawczej (zobacz [zgody na korzystanie z wersji zapoznawczej](#opting-in-to-the-preview) poniżej). Po (wersja zapoznawcza) ta funkcja będzie można włączyć domyślnie dla wszystkich dzierżaw.

> [!NOTE]
> Jednorazowy kod dostępu musi logowania za pomocą linku zawierający kontekstu dzierżawy (na przykład `https://myapps.microsoft.com/?tenantid=<tenant id>` lub `https://portal.azure.com/<tenant id>`, lub w przypadku zweryfikowanej domeny `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com`). Bezpośrednie linki do aplikacji i zasobów również działać tak długo, jak należą do kontekstu dzierżawy. Użytkownicy-goście, są obecnie nie można zalogować się przy użyciu punktów końcowych, które mają nie kontekstu dzierżawy. Na przykład za pomocą `https://myapps.microsoft.com`, `https://portal.azure.com`, lub endpoint wspólnego zespoły spowoduje wystąpienie błędu. 

## <a name="user-experience-for-one-time-passcode-guest-users"></a>Środowisko użytkownika dla użytkowników-gości jednorazowy kod dostępu
Jednorazowy kod dostępu uwierzytelniania użytkownik-Gość można zrealizować zaproszenia, klikając łącze bezpośrednie lub za pomocą wiadomości e-mail z zaproszeniem. W obu przypadkach komunikat w przeglądarce wskazuje, że kod zostanie wysłany na adres e-mail użytkownika gościa. Użytkownik-Gość wybiera **Wyślij kod**:
 
   ![Zrzut ekranu przedstawiający przycisk Wyślij kod](media/one-time-passcode/otp-send-code.png)
 
Hasła są wysyłane na adres e-mail użytkownika. Użytkownik pobiera kod dostępu z wiadomości e-mail i wstawia go w oknie przeglądarki:
 
   ![Zrzut ekranu przedstawiający stronę kodową Enter](media/one-time-passcode/otp-enter-code.png)
 
Użytkownik-Gość obecnie jest uwierzytelniona i ich można wyświetlić zasobu udostępnionego lub kontynuować logowanie. 

> [!NOTE]
> Jednorazowe kody dostępu są ważne przez 30 minut. Po 30 minutach tego określonego jednorazowy kod dostępu nie jest już prawidłowy, a użytkownik musi poprosić o nowe. Sesje użytkowników wygasają po upływie 24 godzin. Po tym czasie użytkownik-Gość otrzyma nowy kod dostępu uzyskiwaniu dostępu do zasobu. Wygaśnięcia sesji zapewnia zwiększenia poziomu bezpieczeństwa, zwłaszcza w przypadku, gdy użytkownik-Gość pozostawia firmy lub już nie musi mieć dostęp.

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>Kiedy użytkownik-Gość uzyskać jednorazowy kod dostępu?

Po użytkownik-Gość umarza zaproszenia, lub używa łącze do zasobu, który został udostępniony z nimi, jeśli zostanie wyświetlony jednorazowy kod dostępu:
- Nie masz konta usługi Azure AD 
- Nie masz konta Microsoft 
- Nie określono zapraszający dzierżawca Google Federację dla @gmail.com i @googlemail.com użytkowników 

W momencie zaproszenia jest nie wskazuje, że użytkownik, którego jesteś zapraszanie będzie używać uwierzytelniania jednorazowy kod dostępu. Jednak po zalogowaniu Gość jednorazowy kod dostępu uwierzytelniania będzie Określ metodę planu awaryjnego, jeśli nie innych metod uwierzytelniania, może być używany. 

Można wyświetlić użytkowników-gości, którzy uwierzytelniają się za pomocą haseł jednorazowych w witrynie Azure portal, przechodząc do **usługi Azure Active Directory** > **relacje w organizacji**  >   **Użytkownicy z innych organizacji**.

![Zrzut ekranu przedstawiający jednorazowy kod dostępu użytkownika z źródła wartość OTP](media/one-time-passcode/otp-users.png)

> [!NOTE]
> Gdy użytkownik umarza jednorazowy kod dostępu, a później uzyskuje MSA, konto usługi Azure AD lub innego federacyjnego konta, będą one nadal zostać uwierzytelniony przy użyciu jednorazowy kod dostępu. Jeśli chcesz zaktualizować swoje metody uwierzytelniania, możesz usunąć swoje konta gościa i Zaproś je ponownie.

### <a name="example"></a>Przykład
Użytkownik-Gość alexdoe@gmail.com zostało zaproszone do firmy Fabrikam, która nie ma Google federacyjnej Konfigurowanie. Alex nie ma konta Microsoft. ADAM, otrzymasz jednorazowy kod dostępu do uwierzytelniania.

## <a name="opting-in-to-the-preview"></a>Zgody na korzystanie z wersji zapoznawczej 
Może upłynąć kilka minut, zanim Akcja zgłoszenie zgody na uczestnictwo w zaczęły obowiązywać. Po tym tylko nowo zaproszonych użytkowników, którzy spełniają powyższe warunki użyje uwierzytelniania jednorazowy kod dostępu. Użytkowników-gości, którzy wcześniej zrealizować zaproszenia, będą w dalszym ciągu używać ich z tej samej metody uwierzytelniania.

### <a name="to-opt-in-using-the-azure-ad-portal"></a>Aby zapoznać się za pomocą portalu usługi Azure AD
1.  Zaloguj się do [witryny Azure portal](https://portal.azure.com/) jako administrator globalny usługi Azure AD.
2.  W okienku nawigacji wybierz **usługi Azure Active Directory**.
3.  W obszarze **Zarządzaj**, wybierz opcję **relacje w organizacji**.
4.  Wybierz **ustawienia**.
5.  W obszarze **Włącz E-mail jednorazowego kodu dostępu dla gości (wersja zapoznawcza)**, wybierz opcję **tak**.
 
### <a name="to-opt-in-using-powershell"></a>Aby zapoznać się przy użyciu programu PowerShell

Najpierw należy zainstalować najnowszą wersję programu Azure AD PowerShell dla modułu programu Graph (AzureADPreview). Następnie należy określić czy B2B zasad już istnieją i uruchamiania odpowiednich poleceń.

#### <a name="prerequisite-install-the-latest-azureadpreview-module"></a>Wymagania wstępne: Zainstalowanie najnowszego modułu AzureADPreview
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

#### <a name="check-for-existing-policies-and-opt-in"></a>Sprawdź, czy istniejące zasady i zgoda

Następnie sprawdź, czy istnieje obecnie B2BManagementPolicy, uruchamiając następujące:

```powershell 
$currentpolicy =  Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
$currentpolicy -ne $null
```
- Jeśli wynikiem jest wartość False, zasady nie istnieje. Utwórz nowy B2BManagementPolicy i korzystania z wersji zapoznawczej, uruchamiając następujące:

   ```powershell 
   $policyValue=@("{`"B2BManagementPolicy`":{`"PreviewPolicy`":{`"Features`":[`"OneTimePasscode`"]}}}")
   New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true
   ```

- Jeśli wynik to True, zasady B2BManagementPolicy już istnieje. Aby zaktualizować zasady i korzystania z wersji zapoznawczej, uruchom następujące polecenie:
  
   ```powershell 
   $policy = $currentpolicy.Definition | ConvertFrom-Json
   $features=[PSCustomObject]@{'Features'=@('OneTimePasscode')}; $policy.B2BManagementPolicy | Add-Member 'PreviewPolicy' $features -Force; $policy.B2BManagementPolicy
   $updatedPolicy = $policy | ConvertTo-Json -Depth 3
   Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
   ```

## <a name="opting-out-of-the-preview-after-opting-in"></a>Rezygnacja z wersji zapoznawczej po zgodzie na rozwiązanie
Może upłynąć kilka minut, zanim Akcja rezygnacji z zaczęły obowiązywać. Jeśli wyłączysz korzystania z wersji zapoznawczej żadnych użytkowników-gości, którzy wprowadzono jednorazowy kod dostępu nie będzie się zalogować. Można usunąć użytkownika gościa i Zaproś ponownie użytkownika, aby umożliwić im Zaloguj się ponownie przy użyciu innej metody uwierzytelniania.

### <a name="to-turn-off-the-preview-using-the-azure-ad-portal"></a>Aby wyłączyć za pomocą portalu usługi Azure AD w wersji zapoznawczej
1.  Zaloguj się do [witryny Azure portal](https://portal.azure.com/) jako administrator globalny usługi Azure AD.
2.  W okienku nawigacji wybierz **usługi Azure Active Directory**.
3.  W obszarze **Zarządzaj**, wybierz opcję **relacje w organizacji**.
4.  Wybierz **ustawienia**.
5.  W obszarze **Włącz E-mail jednorazowego kodu dostępu dla gości (wersja zapoznawcza)**, wybierz opcję **nie**.

### <a name="to-turn-off-the-preview-using-powershell"></a>Aby wyłączyć przy użyciu programu PowerShell w wersji zapoznawczej
Zainstaluj najnowszy moduł AzureADPreview, jeśli nie masz go jeszcze (zobacz [wymagań wstępnych: Zainstaluj najnowszy moduł AzureADPreview](#prerequisite-install-the-latest-azureadpreview-module) powyżej). Następnie sprawdź, czy istnieją obecnie zasady (wersja zapoznawcza) jednorazowy kod dostępu, uruchamiając następujące:

```powershell 
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
($currentPolicy -ne $null) -and ($currentPolicy.Definition -like "*OneTimePasscode*")
```

Jeśli wynik to True, zrezygnować z korzystania z wersji zapoznawczej, uruchamiając następujące:

```powershell 
$policy = $currentpolicy.Definition | ConvertFrom-Json
$policy.B2BManagementPolicy.PreviewPolicy.Features = $policy.B2BManagementPolicy.PreviewPolicy.Features.Where({$_ -ne "OneTimePasscode"})
$updatedPolicy = $policy | ConvertTo-Json -Depth 3
Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
```

