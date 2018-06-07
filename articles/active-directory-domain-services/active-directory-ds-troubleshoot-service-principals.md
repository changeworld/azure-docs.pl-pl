---
title: 'Azure Active Directory Domain Services: Rozwiązywania problemów z konfiguracją nazwy głównej usługi | Dokumentacja firmy Microsoft'
description: Rozwiązywania problemów z konfiguracją nazwy głównej usługi dla usług domenowych Azure AD
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: ergreenl
ms.openlocfilehash: 18a09a22a5dfa4ed94627727cc844c6b7ffc11f6
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34589457"
---
# <a name="troubleshoot-invalid-service-principal-configuration-for-your-managed-domain"></a>Rozwiązywanie problemów z nieprawidłową konfigurację nazwy głównej usługi dla domeny zarządzanej

Ten artykuł pomaga w rozwiązywaniu problemów oraz usuwaniu błędów konfiguracji odnoszące się do podmiotu zabezpieczeń usługi, które powoduje następujący komunikat alertu:

## <a name="alert-aadds102-service-principal-not-found"></a>AADDS102 alertu: Nie można odnaleźć nazwy głównej usługi

**Komunikat alertu:** *wymagane dla usług domenowych Azure AD do prawidłowej nazwy głównej usługi A został usunięty z katalogu usługi Azure AD. Ta konfiguracja ma wpływ na zdolność firmy Microsoft do monitorowania, zarządzania i poprawki i zsynchronizować domeny zarządzanej.*

[Usługa podmiotów](../active-directory/develop/active-directory-application-objects.md) aplikacje, które firma Microsoft używa do zarządzania, aktualizacji i obsługa domeny zarządzanej. Jeśli są one usuwane dzieli możliwość domenę usługi firmy Microsoft.


## <a name="check-for-missing-service-principals"></a>Sprawdź, czy brak nazwy główne usług
Wykonaj następujące kroki, aby ustalić usługi, która podmiotów zabezpieczeń muszą zostać ponownie utworzone:

1. Przejdź do [aplikacje przedsiębiorstwa — wszystkie aplikacje](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) strony w portalu Azure.
2. W **Pokaż** listy rozwijanej wybierz **wszystkie aplikacje** i kliknij przycisk **Zastosuj**.
3. Korzystając z poniższej tabeli, wprowadź wyszukiwania dla każdego Identyfikatora aplikacji wklejania identyfikator w polu wyszukiwania, a następnie naciskając klawisz. Jeśli wyniki wyszukiwania są puste, wykonując kroki opisane w kolumnie "Rozwiązanie" należy ponownie utworzyć nazwy głównej usługi.

| Identyfikator aplikacji | Rozwiązanie |
| :--- | :--- | :--- |
| 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Utwórz ponownie Brak nazwy głównej usługi przy użyciu programu PowerShell](#recreate-a-missing-service-principal-with-powershell) |
| 443155a6-77f3-45e3-882b-22b3a8d431fb | [Zarejestruj ponownie do przestrzeni nazw Microsoft.AAD](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| abba844e-bc0e-44b0-947a-dc74e5d09022  | [Zarejestruj ponownie do przestrzeni nazw Microsoft.AAD](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Nazwy główne usług, które self Popraw](#service-principals-that-self-correct) |

## <a name="recreate-a-missing-service-principal-with-powershell"></a>Utwórz ponownie Brak nazwy głównej usługi przy użyciu programu PowerShell
Wykonaj następujące kroki, jeśli nazwy głównej usługi o identyfikatorze ```2565bd9d-da50-47d4-8b85-4c97f669dc36``` nie ma w katalogu usługi Azure AD.

**Rozwiązanie:** należy wykonać te kroki programu Azure AD PowerShell. Aby uzyskać informacje na temat instalowania programu Azure AD PowerShell, zobacz [w tym artykule](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.).

Aby rozwiązać ten problem, wpisz następujące polecenia w oknie programu PowerShell:
1. Zainstaluj moduł Azure AD PowerShell i zaimportuj go.

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

2. Sprawdź, czy nazwy głównej usługi wymagane dla usług domenowych Azure AD w katalogu, wykonując następujące polecenie programu PowerShell:

    ```powershell
    Get-AzureAdServicePrincipal -filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
    ```

3. Tworzenie nazwy głównej usługi, wpisując następujące polecenie programu PowerShell:

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```

4. Po utworzeniu usługi Brak podmiotu zabezpieczeń, Zaczekaj dwie godziny i Sprawdź kondycję domeny zarządzanej.


## <a name="re-register-to-the-microsoft-aad-namespace-using-the-azure-portal"></a>Zarejestruj ponownie do przestrzeni nazw AAD firmy Microsoft przy użyciu portalu Azure
Wykonaj następujące kroki, jeśli nazwy głównej usługi o identyfikatorze ```443155a6-77f3-45e3-882b-22b3a8d431fb``` lub ```abba844e-bc0e-44b0-947a-dc74e5d09022``` nie ma w katalogu usługi Azure AD.

**Rozwiązanie:** wykonaj następujące kroki, aby przywrócić usług domenowych w katalogu:

1. Przejdź do [subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) strony w portalu Azure.
2. Wybierz subskrypcję z tabeli, która jest skojarzona z domeny zarządzanej
3. Przy użyciu nawigacji po lewej stronie, wybierz **dostawców zasobów**
4. Wyszukaj "Microsoft.AAD" w tabeli, a następnie kliknij przycisk **ponownie zarejestrować**
5. Aby upewnić się, że alert nie zostanie rozwiązany, Wyświetl stronę kondycji dla domeny zarządzanej w dwóch godzin.


## <a name="service-principals-that-self-correct"></a>Nazwy główne usług, które self Popraw
Wykonaj następujące kroki, jeśli nazwy głównej usługi o identyfikatorze ```d87dcbc6-a371-462e-88e3-28ad15ec4e64``` nie ma w katalogu usługi Azure AD.

**Rozwiązanie:** usługi domenowe Azure AD można Wykryj, kiedy tego podmiotu zabezpieczeń określonej usługi jest Brak, niepoprawnie skonfigurowany lub został usunięty. Usługa automatycznie odtworzy tej nazwy głównej usługi. Jednak należy usunąć aplikację i obiektu, który pracuje z usuniętych aplikacji, jak podczas certyfikacji najedzie na, aplikacji i obiekt nie będzie można modyfikować nowej nazwy głównej usługi. To spowoduje nowy błąd w domenie. Wykonaj kroki opisane w temacie [sekcji AADDS105](#alert-aadds105-password-synchronization-application-is-out-of-date) Aby uniknąć tego problemu. Po Sprawdź kondycję domeny zarządzanej po dwóch godzinach, aby upewnić się, że ponownie utworzono nową główną nazwę usługi.


## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>Alert AADDS105: Aplikacja synchronizacji haseł jest nieaktualny

**Komunikat alertu:** nazwy głównej usługi z Identyfikatorem aplikacji "d87dcbc6-a371-462e-88e3-28ad15ec4e64" został usunięty i następnie utworzony ponownie. Odtworzenie pozostawia za niezgodne uprawnienia potrzebne do domeny zarządzanej usługi zasobów usługi domenowe Azure AD. Synchronizacja haseł dla domeny zarządzanej może to mieć wpływ.


**Rozwiązanie:** należy wykonać te kroki programu Azure AD PowerShell. Aby uzyskać informacje na temat instalowania programu Azure AD PowerShell, zobacz [w tym artykule](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.).

Aby rozwiązać ten problem, wpisz następujące polecenia w oknie programu PowerShell:
1. Zainstaluj moduł Azure AD PowerShell i zaimportuj go.

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```
2. Usuń stare aplikacji i obiektu przy użyciu następujących poleceń programu PowerShell

    ```powershell
    $app = Get-AzureADApplication -Filter "IdentifierUris eq 'https://sync.aaddc.activedirectory.windowsazure.com'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $app.ObjectId
    ```
3. Po usunięciu zarówno system samej skorygowanie i ponownie utworzyć aplikacje potrzebne do synchronizacji haseł. Aby upewnić się, że alert został skorygowany, Zaczekaj dwie godziny i Sprawdź kondycję użytkownika domeny.


## <a name="contact-us"></a>Skontaktuj się z nami
Skontaktuj się z zespołem produktu usługi Azure Active Directory Domain Services, aby [udostępnić opinię lub pomocy technicznej](active-directory-ds-contact-us.md).
