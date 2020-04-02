---
title: Konfigurowanie sposobu wyrażania zgody użytkowników końcowych na aplikacje korzystające z usługi Azure AD
description: Dowiedz się, jak zarządzać, jak i kiedy użytkownicy mogą wyrazić zgodę na aplikacje, które będą miały dostęp do danych organizacji.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42337fe958a881ee263d16c866dda69f13fe09c1
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519623"
---
# <a name="configure-how-end-users-consent-to-applications"></a>Konfigurowanie sposobu wyrażania zgody użytkowników końcowych na aplikacje

Aplikacje mogą integrować się z platformą Microsoft Identity, aby umożliwić użytkownikom logowanie się przy użyciu konta służbowego lub szkolnego w usłudze Azure Active Directory (Azure AD) oraz dostęp do danych organizacji w celu zapewnienia środowiska opartego na danych. Różne uprawnienia umożliwiają aplikacji inny poziom dostępu do danych użytkowników i organizacji.

Domyślnie użytkownicy mogą wyrażać zgodę na aplikacje uzyskujące dostęp do danych organizacji, chociaż tylko dla niektórych uprawnień. Na przykład domyślnie użytkownik może wyrazić zgodę na zezwolenie aplikacji na dostęp do własnej skrzynki pocztowej lub konwersacji usługi Teams dla zespołu, do której jest właścicielem, ale nie może wyrazić zgody na zezwalanie aplikacji na nienadzorowany dostęp do odczytu i zapisu we wszystkich witrynach programu SharePoint w organizacji. Zezwalając użytkownikom na wyrażenie zgody przez siebie pozwala użytkownikom łatwo nabyć przydatne aplikacje, które integrują się z usługami Microsoft 365, Azure i innymi usługami, może stanowić ryzyko, jeśli nie są używane i monitorowane starannie.

Firma Microsoft zaleca wyłączenie przyszłych operacji zgody użytkownika, aby zmniejszyć powierzchnię i ograniczyć to ryzyko. Jeśli zgoda użytkownika jest wyłączona, wcześniejsze dotacje zgody będą nadal honorowane, ale wszystkie przyszłe operacje zgody muszą być wykonywane przez administratora. Zgoda administratora w całej dzierżawie może być żądana przez użytkowników za pośrednictwem [przepływu pracy żądania zgody zintegrowanego administratora](configure-admin-consent-workflow.md) lub za pośrednictwem własnych procesów pomocy technicznej. Aby uzyskać więcej informacji, zobacz [Pięć kroków do zabezpieczenia infrastruktury tożsamości.](../../security/fundamentals/steps-secure-identity.md)

## <a name="configure-user-consent-to-applications"></a>Konfigurowanie zgody użytkownika na aplikacje
### <a name="disable-or-enable-user-consent-from-the-azure-portal"></a>Wyłączanie lub włączanie zgody użytkownika z witryny Azure Portal

Za pomocą witryny Azure Portal można wyłączyć lub włączyć możliwość wyrażenia zgody przez użytkowników na aplikacje uzyskujące dostęp do danych organizacji:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako [administrator globalny](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator).
2. Wybierz **pozycję Azure Active Directory**, a następnie **aplikacje przedsiębiorstwa**, a następnie **ustawienia użytkownika**.
3. Włącz lub wyłącz zgodę użytkownika za pomocą formantu **oznaczonego Użytkownicy mogą wyrazić zgodę na dostęp aplikacji do danych firmy w ich imieniu.**
4. (Opcjonalnie) Skonfiguruj [przepływ pracy żądania zgody administratora,](configure-admin-consent-workflow.md) aby upewnić się, że użytkownicy, którzy nie mogą wyrazić zgody na aplikację, mogą zażądać zatwierdzenia.

> [!TIP]
> Aby umożliwić użytkownikom żądanie przeglądu aplikacji przez administratora, na którą użytkownik nie może wyrazić zgody (na przykład dlatego, że zgoda użytkownika została wyłączona lub ponieważ aplikacja żąda uprawnień, których użytkownik nie może udzielić), rozważ [skonfigurowanie przepływu pracy zgody administratora](configure-admin-consent-workflow.md).

### <a name="disable-or-enable-user-consent-using-powershell"></a>Wyłączanie lub włączanie zgody użytkownika przy użyciu programu PowerShell

Modułu programu Azure AD PowerShell w wersji 1[(MSOnline)](https://docs.microsoft.com/powershell/module/msonline/?view=azureadps-1.0)można włączyć lub wyłączyć możliwość wyrażenia zgody przez użytkowników na aplikacje uzyskujące dostęp do danych organizacji.

1. Zaloguj się do organizacji, uruchamiając to polecenie cmdlet:

    ```powershell
    Connect-MsolService
    ```

2. Sprawdź, czy zgoda użytkownika jest włączona, uruchamiając to polecenie cmdlet:

    ```powershell
    Get-MsolCompanyInformation | Format-List UsersPermissionToUserConsentToAppEnabled
    ```

3. Włącz lub wyłącz zgodę użytkownika. Na przykład, aby wyłączyć zgodę użytkownika, uruchom to polecenie cmdlet:

    ```powershell
    Set-MsolCompanySettings -UsersPermissionToUserConsentToAppEnabled $false
    ```

## <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>Konfigurowanie zgody właściciela grupy na aplikacje uzyskujące dostęp do danych grupy

> [!IMPORTANT]
> Poniższe informacje są dla nadchodzącej funkcji, która pozwoli właścicielom grup udzielić aplikacji dostępu do danych swoich grup. Po zwolnieniu tej funkcji zostanie ono domyślnie włączone. Chociaż ta funkcja nie jest jeszcze powszechnie wydana, można użyć tych instrukcji, aby wyłączyć możliwość przed jej wydaniem.

Właściciele grup mogą autoryzować aplikacje (na przykład aplikacje publikowane przez dostawców zewnętrznych) w celu uzyskania dostępu do danych organizacji skojarzonych z grupą. Na przykład właściciel zespołu (który jest właścicielem grupy usługi Office 365 dla zespołu) może zezwolić aplikacji na odczytywanie wszystkich wiadomości usługi Teams w zespole lub wyświetlić listę podstawowego profilu członków grupy.

> [!NOTE]
> Niezależnie od tego ustawienia właściciel grupy zawsze może dodawać innych użytkowników lub aplikacje bezpośrednio jako właścicieli grupy.

### <a name="configure-group-owner-consent-using-powershell"></a>Konfigurowanie zgody właściciela grupy przy użyciu programu PowerShell

Moduł azure ad powershell preview[(AzureADPreview)](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)umożliwia włączenie lub wyłączenie możliwości właścicieli grup w celu wyrażenia zgody na aplikacje uzyskujące dostęp do danych organizacji dla grup, które są ich własnością.

1. Upewnij się, że używasz modułu [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) (ten krok jest ważny, jeśli zainstalowano zarówno moduł [AzureAD,](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0) jak i moduł [AzureADPreview).](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

2. Połącz się z programem Azure AD PowerShell.

   ```powershell
   Connect-AzureAD
   ```

3. Pobierz bieżącą wartość ustawień katalogu *Ustawienia zasad zgody* w dzierżawie. Wymaga to sprawdzenia, czy utworzono ustawienia katalogu dla tej funkcji, a jeśli nie, użycie wartości z odpowiedniego szablonu ustawień katalogu.

    ```powershell
    $consentSettingsTemplateId = "dffd5d46-495d-40a9-8e21-954ff55e198a" # Consent Policy Settings
    $settings = Get-AzureADDirectorySetting -All $true | Where-Object { $_.TemplateId -eq $consentSettingsTemplateId }

    if (-not $settings) {
        $template = Get-AzureADDirectorySettingTemplate -Id $consentSettingsTemplateId
        $settings = $template.CreateDirectorySetting()
    }

    $enabledValue = $settings.Values | ? { $_.Name -eq "EnableGroupSpecificConsent" }
    $limitedToValue = $settings.Values | ? { $_.Name -eq "ConstrainGroupSpecificConsentToMembersOfGroupId" }
    ```

4. Zrozumienie wartości ustawień. Istnieją dwie wartości ustawień, które określają, którzy użytkownicy będą mogli zezwolić aplikacji na dostęp do danych grupy:

    | Ustawienie       | Typ         | Opis  |
    | ------------- | ------------ | ------------ |
    | _EnableGroupSpecificConsent (Specyfikacja grupy)_   | Wartość logiczna |  Flaga wskazująca, czy właściciele grup mogą udzielać uprawnień specyficznych dla grupy. |
    | _ConstrainGroupSpecificConsentToMembersOfGroupId_ | Guid (identyfikator GUID) | Jeśli _EnableGroupSpecificConsent_ jest ustawiona na "True", a ta wartość ustawiona na identyfikator obiektu grupy, członkowie zidentyfikowanej grupy będą upoważnieni do udzielania uprawnień specyficznych dla grupy do grup, których są właścicielami. |

5. Aktualizuj wartości ustawień dla żądanej konfiguracji:

    ```powershell
    # Disable group-specific consent entirely
    $enabledValue.Value = "False"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for all users
    $enabledValue.Value = "True"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for users in a given group
    $enabledValue.Value = "True"
    $limitedToValue.Value = "{group-object-id}"
    ```

6. Zapisz ustawienia.

    ```powershell
    if ($settings.Id) {
        # Update an existing directory settings
        Set-AzureADDirectorySetting -Id $settings.Id -DirectorySetting $settings
    } else {
        # Create a new directory settings to override the default setting 
        New-AzureADDirectorySetting -DirectorySetting $settings
    }
    ```

## <a name="configure-risk-based-step-up-consent"></a>Konfigurowanie zgody na step-up oparte na analizie ryzyka

Zgoda na stopniowe działanie oparte na ryzyku pomaga zmniejszyć narażenie użytkowników na złośliwe aplikacje, które [żądają nielegalnej zgody.](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants) Jeśli firma Microsoft wykryje ryzykowne żądanie zgody użytkownika końcowego, żądanie będzie wymagać "step-up" do zgody administratora zamiast. Ta funkcja jest domyślnie włączona, ale spowoduje zmianę zachowania tylko wtedy, gdy włączona jest zgoda użytkownika końcowego.

Po wykryciu ryzykownego żądania zgody monit o zgodę wyświetli komunikat informujący, że potrzebne jest zatwierdzenie administratora. Jeśli [przepływ pracy żądania zgody administratora](configure-admin-consent-workflow.md) jest włączony, użytkownik może wysłać żądanie do administratora do dalszego przeglądu bezpośrednio z monitu o zgodę. Jeśli opcja ta nie jest włączona, zostanie wyświetlony następujący komunikat:

* **AADSTS90094:** &lt;clientAppDisplayName&gt; potrzebuje uprawnień dostępu do zasobów w organizacji, które tylko administrator może udzielić. Poproś administratora o udzielenie uprawnienia do tej aplikacji, aby można było z niej korzystać.

W takim przypadku zdarzenie inspekcji zostanie również zarejestrowane z kategorią "Zarządzanie aplikacjami", typem działania "Zgoda na aplikację" i powodem stanu "Wykryto ryzykowną aplikację".

> [!IMPORTANT]
> Administratorzy powinni dokładnie [ocenić wszystkie żądania zgody](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent) przed zatwierdzeniem, zwłaszcza gdy firma Microsoft wykryła ryzyko.

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>Wyłączanie lub ponowne włączanie zgody na step-up oparte na ryzyku przy użyciu programu PowerShell

Za pomocą modułu Azure AD PowerShell Preview[(AzureADPreview)](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)można wyłączyć step-up do zgody administratora wymagane w przypadkach, gdy firma Microsoft wykrywa ryzyko lub ponownie włączyć go, jeśli został wcześniej wyłączony.

Można to zrobić przy użyciu tych samych kroków, jak pokazano powyżej do [konfigurowania zgody właściciela grupy za pomocą programu PowerShell](#configure-group-owner-consent-using-powershell), ale zastąpienie innej wartości ustawień. Istnieją trzy różnice w krokach: 

1. Zrozumienie wartości ustawień zgody na step-up na podstawie ryzyka:

    | Ustawienie       | Typ         | Opis  |
    | ------------- | ------------ | ------------ |
    | _BlockUserConsentForRiskyApps_   | Wartość logiczna |  Flaga wskazująca, czy zgoda użytkownika zostanie zablokowana po wykryciu ryzykownego żądania. |

2. Zastąpić następującą wartość w kroku 3:

    ```powershell
    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```
3. Zastąpić jeden z następujących w kroku 5:

    ```powershell
    # Disable risk-based step-up consent entirely
    $riskBasedConsentEnabledValue.Value = "False"
    ```

    ```powershell
    # Re-enable risk-based step-up consent, if disabled previously
    $riskBasedConsentEnabledValue.Value = "True"
    ```

## <a name="next-steps"></a>Następne kroki

[Konfigurowanie przepływu pracy zgody administratora](configure-admin-consent-workflow.md)

[Dowiedz się, jak zarządzać zgodą aplikacji i oceniać żądania zgody](manage-consent-requests.md)

[Udzielanie zgody administratora dla całej dzierżawy na aplikację](grant-admin-consent.md)

[Uprawnienia i zgoda na platformie tożsamości firmy Microsoft](../develop/active-directory-v2-scopes.md)

[Usługa Azure AD w przepływie stosuPrzepełnienie](https://stackoverflow.com/questions/tagged/azure-active-directory)
