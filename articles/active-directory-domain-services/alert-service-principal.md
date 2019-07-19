---
title: 'Azure Active Directory Domain Services: Rozwiązywanie problemów z nazwami podmiotów usługi | Microsoft Docs'
description: Rozwiązywanie problemów z konfiguracją główną usługi dla Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: ''
editor: ''
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: iainfou
ms.openlocfilehash: 9e5fa8c84f5e7ca58117666846b603a118826150
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234139"
---
# <a name="troubleshoot-invalid-service-principal-configurations-for-azure-active-directory-domain-services"></a>Rozwiązywanie problemów z nieprawidłowymi konfiguracjami jednostek usługi dla Azure Active Directory Domain Services

Ten artykuł pomaga rozwiązywać problemy i rozwiązywać związane z nimi błędy konfiguracji dotyczące jednostki usługi, które powodują następujący komunikat o alercie:

## <a name="alert-aadds102-service-principal-not-found"></a>AADDS102 alertu: Nie znaleziono nazwy głównej usługi

**Komunikat alertu:** *Nazwa główna usługi wymagana do poprawnego działania Azure AD Domain Services została usunięta z katalogu usługi Azure AD. Ta konfiguracja ma wpływ na zdolność firmy Microsoft do monitorowania, poprawiania i synchronizowania domeny zarządzanej oraz zarządzania nią.*

[Nazwy główne usług](../active-directory/develop/app-objects-and-service-principals.md) to aplikacje używane przez firmę Microsoft do zarządzania, aktualizowania i konserwowania domeny zarządzanej. Jeśli zostaną usunięte, przerwie zdolność firmy Microsoft do obsługi Twojej domeny.


## <a name="check-for-missing-service-principals"></a>Sprawdź brakujące jednostki usługi
Wykonaj następujące kroki, aby określić, które jednostki usługi muszą zostać utworzone ponownie:

1. Przejdź do strony [aplikacje dla przedsiębiorstw — wszystkie aplikacje](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) w Azure Portal.
2. Z listy rozwijanej **Pokaż** wybierz pozycję **wszystkie aplikacje** , a następnie kliknij przycisk **Zastosuj**.
3. Korzystając z poniższej tabeli, Wyszukaj każdy identyfikator aplikacji, wklejając identyfikator w polu wyszukiwania i naciskając klawisz ENTER. Jeśli wyniki wyszukiwania są puste, należy ponownie utworzyć jednostkę usługi, wykonując kroki z kolumny "rozwiązanie".

| Identyfikator aplikacji | Rozwiązanie |
| :--- | :--- |
| 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Ponowne tworzenie brakującej jednostki usługi przy użyciu programu PowerShell](#recreate-a-missing-service-principal-with-powershell) |
| 443155a6-77f3-45e3-882b-22b3a8d431fb | [Zarejestruj ponownie w przestrzeni nazw Microsoft. AAD](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| abba844e-bc0e-44b0-947a-dc74e5d09022  | [Zarejestruj ponownie w przestrzeni nazw Microsoft. AAD](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Zarejestruj ponownie w przestrzeni nazw Microsoft. AAD](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |

## <a name="recreate-a-missing-service-principal-with-powershell"></a>Ponowne tworzenie brakującej jednostki usługi przy użyciu programu PowerShell
Wykonaj te kroki, jeśli w katalogu usługi Azure AD ```2565bd9d-da50-47d4-8b85-4c97f669dc36``` brakuje nazwy głównej usługi o identyfikatorze.

**Tłumaczenia** Aby wykonać te kroki, potrzebujesz programu Azure AD PowerShell. Aby uzyskać informacje na temat instalowania programu Azure AD PowerShell, zobacz [ten artykuł](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.).

Aby rozwiązać ten problem, wpisz następujące polecenia w oknie programu PowerShell:
1. Zainstaluj moduł Azure AD PowerShell i zaimportuj go.

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

2. Sprawdź, czy w katalogu brakuje jednostki usługi wymaganej do Azure AD Domain Services, wykonując następujące polecenie programu PowerShell:

    ```powershell
    Get-AzureAdServicePrincipal -filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
    ```

3. Utwórz nazwę główną usługi, wpisując następujące polecenie programu PowerShell:

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```

4. Po utworzeniu brakującej jednostki usługi Poczekaj dwie godziny i Sprawdź kondycję domeny zarządzanej.


## <a name="re-register-to-the-microsoft-aad-namespace-using-the-azure-portal"></a>Zarejestruj ponownie w przestrzeni nazw usługi Microsoft AAD przy użyciu Azure Portal
Wykonaj te kroki, jeśli w katalogu usługi Azure AD ```443155a6-77f3-45e3-882b-22b3a8d431fb``` nie ```abba844e-bc0e-44b0-947a-dc74e5d09022``` ma ```d87dcbc6-a371-462e-88e3-28ad15ec4e64``` nazwy podmiotu usług o identyfikatorze lub lub.

**Tłumaczenia** Wykonaj następujące kroki, aby przywrócić usługi domenowe w Twoim katalogu:

1. Przejdź do strony [subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) w Azure Portal.
2. Wybierz subskrypcję z tabeli skojarzonej z Twoją domeną zarządzaną
3. Przy użyciu nawigacji po lewej stronie wybierz pozycję **dostawcy zasobów**
4. Wyszukaj ciąg "Microsoft. AAD" w tabeli, a następnie kliknij pozycję **zarejestruj ponownie**
5. Aby upewnić się, że alert został rozwiązany, Wyświetl stronę kondycja domeny zarządzanej w ciągu dwóch godzin.


## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>AADDS105 alertu: Aplikacja do synchronizacji haseł jest nieaktualna

**Komunikat alertu:** Nazwa główna usługi z identyfikatorem aplikacji "d87dcbc6-a371-462e-88e3-28ad15ec4e64" została usunięta, a następnie ponownie utworzona. Ponowne tworzenie jest pozostawiane za niespójnymi uprawnieniami do Azure AD Domain Services zasobów wymaganych do obsługi domeny zarządzanej. Może to wpłynąć na synchronizację haseł w domenie zarządzanej.


**Tłumaczenia** Aby wykonać te kroki, potrzebujesz programu Azure AD PowerShell. Aby uzyskać informacje na temat instalowania programu Azure AD PowerShell, zobacz [ten artykuł](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.).

Aby rozwiązać ten problem, wpisz następujące polecenia w oknie programu PowerShell:
1. Zainstaluj moduł Azure AD PowerShell i zaimportuj go.

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```
2. Usuń starą aplikację i obiekt za pomocą następujących poleceń programu PowerShell

    ```powershell
    $app = Get-AzureADApplication -Filter "IdentifierUris eq 'https://sync.aaddc.activedirectory.windowsazure.com'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $app.ObjectId
    ```
3. Po usunięciu obu systemów system koryguje się i ponownie utworzy aplikacje potrzebne do synchronizacji haseł. Aby upewnić się, że alert został skorygowany, odczekaj dwie godziny i Sprawdź kondycję domeny.


## <a name="contact-us"></a>Kontakt z nami
Skontaktuj się z zespołem pomocy technicznej Azure Active Directory Domain Services, aby [udostępnić opinię lub pomoc techniczną](contact-us.md).
