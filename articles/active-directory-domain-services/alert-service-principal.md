---
title: 'Azure Active Directory Domain Services: Konfiguracja jednostki usługi rozwiązywania problemów | Dokumentacja firmy Microsoft'
description: Rozwiązywanie problemów z konfiguracją nazwy głównej usługi dla usług domenowych Azure AD
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
ms.openlocfilehash: 5b94fca6087da61fe10d6c80b3fe7cfb5798f2d3
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67473898"
---
# <a name="troubleshoot-invalid-service-principal-configuration-for-your-managed-domain"></a>Rozwiązywanie problemów z nieprawidłową konfiguracją nazwy głównej usługi dla swojej domeny zarządzanej

Ten artykuł ułatwia rozwiązywanie problemów i eliminowanie błędów konfiguracji odnoszące się do nazwy głównej usługi, które powoduje następujący komunikat alertu:

## <a name="alert-aadds102-service-principal-not-found"></a>AADDS102 alertu: Nie można odnaleźć nazwy głównej usługi

**Komunikat alertu:** *Wymagane dla usług domenowych Azure AD do prawidłowej nazwy głównej usługi został usunięty z katalogu usługi Azure AD. Ta konfiguracja wpływa negatywnie na możliwości firmy Microsoft, aby monitorować oraz zarządzać nimi, poprawki i synchronizować swojej domeny zarządzanej.*

[Jednostki usług](../active-directory/develop/app-objects-and-service-principals.md) aplikacje, które firma Microsoft używa do zarządzania, aktualizować i obsługiwać Twojej domeny zarządzanej. Jeśli są one usuwane przerywa możliwości firmy Microsoft do obsługi Twojej domeny.


## <a name="check-for-missing-service-principals"></a>Wyszukaj Brak jednostki usługi
Aby określić, która usługa podmiotów zabezpieczeń, muszą one zostać odtworzone, wykonaj następujące kroki:

1. Przejdź do [aplikacje w przedsiębiorstwie — wszystkie aplikacje](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) strony w witrynie Azure portal.
2. W **Pokaż** listy rozwijanej wybierz **wszystkie aplikacje** i kliknij przycisk **Zastosuj**.
3. Korzystając z poniższej tabeli, należy wprowadzić wyszukiwania dla każdego Identyfikatora aplikacji, wklejając identyfikator w polu wyszukiwania, a następnie naciskając klawisz. Jeśli wyniki wyszukiwania są puste, należy ponownie utworzyć nazwy głównej usługi, wykonując kroki opisane w kolumnie "Rozwiązanie".

| Identyfikator aplikacji | Rozwiązanie |
| :--- | :--- |
| 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Utwórz ponownie Brak jednostki usługi przy użyciu programu PowerShell](#recreate-a-missing-service-principal-with-powershell) |
| 443155a6-77f3-45e3-882b-22b3a8d431fb | [Ponownie zarejestrować się do przestrzeni nazw Microsoft.AAD](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| abba844e-bc0e-44b0-947a-dc74e5d09022  | [Ponownie zarejestrować się do przestrzeni nazw Microsoft.AAD](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Ponownie zarejestrować się do przestrzeni nazw Microsoft.AAD](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |

## <a name="recreate-a-missing-service-principal-with-powershell"></a>Utwórz ponownie Brak jednostki usługi przy użyciu programu PowerShell
Wykonaj następujące kroki, jeśli nazwy głównej usługi o identyfikatorze ```2565bd9d-da50-47d4-8b85-4c97f669dc36``` nie ma w katalogu usługi Azure AD.

**Rozwiązanie:** Potrzebujesz programu Azure AD PowerShell, aby wykonać te kroki. Aby uzyskać informacje na temat instalowania programu Azure AD PowerShell, zobacz [w tym artykule](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.).

Aby rozwiązać ten problem, wpisz następujące polecenia w oknie programu PowerShell:
1. Instalowanie modułu Azure AD PowerShell i zaimportuj go.

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

2. Sprawdź, czy Brak jednostki usługi wymagane dla usług domenowych Azure AD w katalogu, wykonując następujące polecenie programu PowerShell:

    ```powershell
    Get-AzureAdServicePrincipal -filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
    ```

3. Tworzenie jednostki usługi, wpisując następujące polecenie programu PowerShell:

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```

4. Po utworzeniu brakuje usługi jednostki, Zaczekaj dwie godziny i sprawdzanie kondycji domeny zarządzanej.


## <a name="re-register-to-the-microsoft-aad-namespace-using-the-azure-portal"></a>Ponownie zarejestruj się w przestrzeni nazw AAD firmy Microsoft, za pomocą witryny Azure portal
Wykonaj następujące kroki, jeśli nazwy głównej usługi o identyfikatorze ```443155a6-77f3-45e3-882b-22b3a8d431fb``` lub ```abba844e-bc0e-44b0-947a-dc74e5d09022``` lub ```d87dcbc6-a371-462e-88e3-28ad15ec4e64``` nie ma w katalogu usługi Azure AD.

**Rozwiązanie:** Użyj następujące kroki, aby przywrócić usług domenowych w Twoim katalogu:

1. Przejdź do [subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) strony w witrynie Azure portal.
2. Wybierz subskrypcję, z tabeli, która jest skojarzona z Twoją domeną zarządzaną
3. Za pomocą nawigacji po lewej stronie wybierz **dostawców zasobów**
4. Wyszukaj "Microsoft.AAD" w tabeli i kliknij przycisk **ponownej rejestracji**
5. Aby upewnić się, że alert nie zostanie rozwiązany, należy wyświetlić stronę kondycji dla domeny zarządzanej w ciągu dwóch godzin.


## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>AADDS105 alertu: Aplikacja synchronizacji haseł jest nieaktualna

**Komunikat alertu:** Jednostki usługi o identyfikatorze aplikacji "d87dcbc6-a371-462e-88e3-28ad15ec4e64" został usunięty i tworzony ponownie. Odtworzenie pozostawia za niespójne uprawnień zasobów usługi Azure AD Domain Services, potrzebne do obsługi Twojej domeny zarządzanej. Może mieć wpływ na synchronizację haseł w domenie zarządzanej.


**Rozwiązanie:** Potrzebujesz programu Azure AD PowerShell, aby wykonać te kroki. Aby uzyskać informacje na temat instalowania programu Azure AD PowerShell, zobacz [w tym artykule](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.).

Aby rozwiązać ten problem, wpisz następujące polecenia w oknie programu PowerShell:
1. Instalowanie modułu Azure AD PowerShell i zaimportuj go.

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```
2. Usuń stare aplikacji i obiekt przy użyciu następujących poleceń programu PowerShell

    ```powershell
    $app = Get-AzureADApplication -Filter "IdentifierUris eq 'https://sync.aaddc.activedirectory.windowsazure.com'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $app.ObjectId
    ```
3. Po usunięciu obu system będzie korygowanie sam i ponowne utworzenie aplikacji służące do synchronizacji haseł. Aby upewnić się, że ten alert został skorygowany, Zaczekaj dwie godziny i sprawdzanie kondycji domeny.


## <a name="contact-us"></a>Kontakt z nami
Skontaktuj się z zespołem produktu usługi Azure Active Directory Domain Services, aby [Podziel się opinią lub pomocy technicznej](contact-us.md).
