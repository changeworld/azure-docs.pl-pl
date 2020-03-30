---
title: Rozwiązywanie alertów o jednostkach usługi w usługach domenowych usługi Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z alertami konfiguracji jednostki usługi dla usług domenowych Active Directory platformy Azure
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/20/2019
ms.author: iainfou
ms.openlocfilehash: 175bfe63176b78c5aeafc7147c46dd5ab1110325
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71257963"
---
# <a name="known-issues-service-principal-alerts-in-azure-active-directory-domain-services"></a>Znane problemy: Alerty głównej usługi w usługach domenowych Active Directory platformy Azure

[Jednostki usługi](../active-directory/develop/app-objects-and-service-principals.md) to aplikacje używane przez platformę Azure do zarządzania domeną zarządzaną usługą Azure AD DS, ich aktualizowania i obsługi. Jeśli podmiot usługi zostanie usunięty, wpływa to na funkcjonalność domeny zarządzanej usług Azure AD DS.

Ten artykuł ułatwia rozwiązywanie problemów i rozwiązywanie alertów konfiguracji związanych z jednostką usługi.

## <a name="alert-aadds102-service-principal-not-found"></a>Alert AADDS102: Nie znaleziono jednostki usługi

### <a name="alert-message"></a>Komunikat ostrzegawczy

*Podmiot usługi wymagany do prawidłowego działania usług domenowych usługi Azure AD został usunięty z katalogu usługi Azure AD. Ta konfiguracja wpływa na zdolność firmy Microsoft do monitorowania, zarządzania, instalowania poprawek i synchronizowania domeny zarządzanej.*

Jeśli wymagany podmiot usługi zostanie usunięty, platforma Azure nie może wykonywać zautomatyzowanych zadań zarządzania. Domena zarządzana usługą Azure AD DS może nie poprawnie zastosować aktualizacje lub wykonać kopie zapasowe.

### <a name="check-for-missing-service-principals"></a>Sprawdź brakujące podmioty usługi

Aby sprawdzić, której jednostki usługi brakuje i należy go odtworzyć, wykonaj następujące kroki:

1. W witrynie Azure portal wybierz pozycję **Azure Active Directory** z menu nawigacji po lewej stronie.
1. Wybierz **aplikacje enterprise**. Wybierz *pozycję Wszystkie aplikacje* z menu rozwijanego **Typ aplikacji,** a następnie wybierz pozycję **Zastosuj**.
1. Wyszukaj każdy z identyfikatorów aplikacji. Jeśli nie znaleziono żadnej istniejącej aplikacji, wykonaj kroki *rozwiązania,* aby utworzyć jednostkę usługi lub ponownie zarejestrować obszar nazw.

    | Identyfikator aplikacji | Rozwiązanie |
    | :--- | :--- |
    | 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Odtworzenie brakującego podmiotu usługi](#recreate-a-missing-service-principal) |
    | 443155a6-77f3-45e3-882b-22b3a8d431fb | [Ponowne rejestrowanie obszaru nazw microsoft.aad](#re-register-the-microsoft-aad-namespace) |
    | abba844e-bc0e-44b0-947a-dc74e5d09022 | [Ponowne rejestrowanie obszaru nazw microsoft.aad](#re-register-the-microsoft-aad-namespace) |
    | d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Ponowne rejestrowanie obszaru nazw microsoft.aad](#re-register-the-microsoft-aad-namespace) |

### <a name="recreate-a-missing-service-principal"></a>Odtworzenie brakującego podmiotu obsługującego usługę

Jeśli w katalogu usługi Azure AD brakuje identyfikatora *2565bd9d-da50-47d4-8b85-4c97f669dc36,* użyj programu Azure AD PowerShell, aby wykonać następujące kroki. Aby uzyskać więcej informacji, zobacz [instalowanie programu Azure AD PowerShell](/powershell/azure/active-directory/install-adv2).

1. Zainstaluj moduł programu Azure AD PowerShell i zaimportuj go w następujący sposób:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

1. Teraz ponownie utworzyć jednostkę usługi przy użyciu polecenia cmdlet [New-AzureAdServicePrincipal:][New-AzureAdServicePrincipal]

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```

Kondycja domeny zarządzanej usług Azure AD DS automatycznie aktualizuje się w ciągu dwóch godzin i usuwa alert.

### <a name="re-register-the-microsoft-aad-namespace"></a>Ponowne rejestrowanie obszaru nazw usługi Microsoft AAD

Jeśli aplikacja ID *443155a6-77f3-45e3-882b-22b3a8d431fb*, *abba844e-bc0e-44b0-4 w katalogu usługi Azure AD brakuje 947a-dc74e5d09022*lub *d87dcbc6-a371-462e-88e3-28ad15ec4e64,* wykonaj następujące kroki, aby ponownie zarejestrować dostawcę zasobów *Microsoft.AAD:*

1. W witrynie Azure portal wyszukaj i wybierz **pozycję Subskrypcje**.
1. Wybierz subskrypcję skojarzoną z domeną zarządzana usługą Azure AD DS.
1. Z lewej strony nawigacji wybierz pozycję **Dostawcy zasobów**.
1. Wyszukaj pozycję *Microsoft.AAD*, a następnie wybierz pozycję **Ponownie zarejestruj się**.

Kondycja domeny zarządzanej usług Azure AD DS automatycznie aktualizuje się w ciągu dwóch godzin i usuwa alert.

## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>Alert AADDS105: Aplikacja do synchronizacji haseł jest nieaktualna

### <a name="alert-message"></a>Komunikat ostrzegawczy

*Podmiot usługi o identyfikatorze aplikacji "d87dcbc6-a371-462e-88e3-28ad15ec4e64" został usunięty, a następnie odtworzony. Rekreacja pozostawia niespójne uprawnienia do zasobów usług domenowych usługi azure ad potrzebne do obsługi domeny zarządzanej. Może to mieć wpływ na synchronizację haseł w domenie zarządzanej.*

Usługi Azure AD DS automatycznie synchronizuje konta użytkowników i poświadczenia z usługi Azure AD. Jeśli występuje problem z aplikacją usługi Azure AD używaną w tym procesie, synchronizacja poświadczeń między usługami Azure AD DS i usługą Azure AD kończy się niepowodzeniem.

### <a name="resolution"></a>Rozwiązanie

Aby ponownie utworzyć aplikację usługi Azure AD używaną do synchronizacji poświadczeń, wykonaj następujące kroki za pomocą programu Azure AD PowerShell. Aby uzyskać więcej informacji, zobacz [instalowanie programu Azure AD PowerShell](/powershell/azure/active-directory/install-adv2).

1. Zainstaluj moduł programu Azure AD PowerShell i zaimportuj go w następujący sposób:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

2. Teraz usuń starą aplikację i obiekt przy użyciu następujących poleceń cmdlet programu PowerShell:

    ```powershell
    $app = Get-AzureADApplication -Filter "IdentifierUris eq 'https://sync.aaddc.activedirectory.windowsazure.com'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $app.ObjectId
    ```

Po usunięciu obu aplikacji platforma Azure automatycznie odtwarza je i próbuje wznowić synchronizację haseł. Kondycja domeny zarządzanej usług Azure AD DS automatycznie aktualizuje się w ciągu dwóch godzin i usuwa alert.

## <a name="next-steps"></a>Następne kroki

Jeśli nadal występują problemy, [otwórz żądanie pomocy technicznej platformy Azure,][azure-support] aby uzyskać dodatkową pomoc w rozwiązywaniu problemów.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[New-AzureAdServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
