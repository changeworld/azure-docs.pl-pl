---
title: Dołączanie usługi Azure Security Center przy użyciu programu PowerShell i chronić sieć | Dokumentacja firmy Microsoft
description: Ten dokument przeprowadzi Cię przez proces dołączania do usługi Azure Security Center przy użyciu poleceń cmdlet programu PowerShell.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: e400fcbf-f0a8-4e10-b571-5a0d0c3d0c67
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 9/20/2018
ms.author: rkarlin
ms.openlocfilehash: 6328f4f2a3b9d657cef537ed9da339f2b9b8a6e8
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2018
ms.locfileid: "46131231"
---
# <a name="automate-onboarding-of-azure-security-center-using-powershell"></a>Automatyzowanie dołączania do usługi Azure Security Center przy użyciu programu PowerShell

Możesz zabezpieczyć obciążeń platformy Azure programowo, za pomocą modułu programu PowerShell Centrum zabezpieczeń Azure.
Przy użyciu programu PowerShell umożliwia automatyzowanie zadań i uniknąć tego błędu ludzkiego, związane z zadań wykonywanych ręcznie, jest to szczególnie przydatne w przypadku wdrożeń na dużą skalę, obejmujące dziesiątek, jak subskrypcje z setek, tysięcy zasobów — wszystkie z nich musi być zabezpieczony początek.

Dołączanie do usługi Azure Security Center przy użyciu programu PowerShell umożliwia programowe automatyzacji dołączania i zarządzania zasobami platformy Azure i dodać środki bezpieczeństwa niezbędne.

Ten artykuł zawiera przykładowy skrypt programu PowerShell, które można modyfikować i używane w danym środowisku do wdrożenia w usłudze Security Center wszystkich subskrypcji. 

W tym przykładzie firma Microsoft będzie włączyć usługę Security Center w subskrypcji o identyfikatorze: d07c0080-170c-4c24-861d-9c817742786c i zastosowania zalecanych ustawień, które zapewniają wysoki poziom ochrony, implementując warstwy standardowa usługi Security Center, która zapewnia możliwości ochrony i wykrywanie zaawansowanych zagrożeń:

1. Ustaw [ASC poziom standardów ochrony](https://azure.microsoft.com/pricing/details/security-center/). 
 
2. Ustaw obszar roboczy usługi Log Analytics, do której program Microsoft Monitoring Agent wysyła dane, które są zbierane na maszynach wirtualnych skojarzonych z tą subskrypcją — w tym przykładzie istniejącego obszaru roboczego zdefiniowanego przez użytkownika (myWorkspace).

3. Aktywuj Centrum zabezpieczeń agent automatycznego inicjowania obsługi, który [służy do wdrażania programu Microsoft Monitoring Agent](security-center-enable-data-collection.md#auto-provision-mma).

5. Ustaw organizacji [CISO jako kontakt zabezpieczeń usługi ASC alertów i istotnych zdarzeń](security-center-provide-security-contact-details.md).

6. Centrum zabezpieczeń Przypisz [domyślne zasady zabezpieczeń](security-center-azure-policy.md).

## <a name="prerequisites"></a>Wymagania wstępne

Te kroki należy wykonać przed uruchomieniem polecenia cmdlet usługi Security Center:

1.  Uruchom program PowerShell jako administrator.
2.  W programie PowerShell, uruchom następujące polecenia:
      
        Install-Module -Name PowerShellGet -Force
        Set-ExecutionPolicy -ExecutionPolicy AllSigned
        Import-Module PowerShellGet
        Install-Module -Name AzureRM.profile -RequiredVersion 5.5.0
6.  Ponowne uruchomienie programu PowerShell

7. W programie PowerShell uruchom następujące polecenia:

         Install-Module -Name AzureRM.Security -AllowPrerelease -Force

## <a name="onboard-security-center-using-powershell"></a>Dołączanie Centrum zabezpieczeń przy użyciu programu PowerShell

1.  Zarejestruj subskrypcje, aby dostawca zasobów Centrum zabezpieczeń:

        Set-AzureRmContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c”
        Register-AzureRmResourceProvider -ProviderNamespace ‘Microsoft.Security’ 

2.  Opcjonalnie: Ustaw poziom pokrycia (warstwa cenowa) z subskrypcji (Jeśli nie zdefiniowano warstwa cenowa jest ustawiona na bezpłatna):

        Set-AzureRmContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c”
        Set-AzureRmSecurityPricing -Name "default" -PricingTier "Standard"

3.  Konfigurowanie obszaru roboczego usługi Log Analytics, do którego agenci będą zgłaszać. Konieczne jest posiadanie utworzonej już, że maszyny wirtualne w subskrypcji będą raportować usłudze obszar roboczy usługi Log Analytics. Można zdefiniować wiele subskrypcji w celu przekazywania informacji do tego samego obszaru roboczego. Jeśli nie zdefiniowano domyślnego obszaru roboczego będą używane.

        Set-AzureRmSecurityWorkspaceSetting -Name "default" -Scope
        "/subscriptions/d07c0080-170c-4c24-861d-9c817742786c" -WorkspaceId"/subscriptions/d07c0080-170c-4c24-861d-9c817742786c/resourceGroups/myRg/providers/Microsoft.OperationalInsights/workspaces/myWorkspace"

4.  Automatyczna obsługa instalacji programu Microsoft Monitoring Agent na maszynach wirtualnych platformy Azure:
    
        Set-AzureRmContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c”
    
        Set-AzureRmSecurityAutoProvisioningSetting -Name "default" -EnableAutoProvision

    > [!NOTE]
    > Zalecane jest, aby włączyć automatyczne Inicjowanie obsługi administracyjnej upewnić się, że usługi Azure virtual machines są automatycznie chronione przez usługę Azure Security Center.
    >

5.  Opcjonalnie: Zdecydowanie zaleca się że zdefiniujesz szczegóły kontaktu zabezpieczeń dla subskrypcji, możesz dołączyć, który będzie używany jako adresaci powiadomień i alertów wygenerowanych przez usługę Security Center:

        Set-AzureRmSecurityContact -Name "default1" -Email "CISO@my-org.com" -Phone "2142754038" -AlertsAdmin -NotifyOnAlert 

6.  Przypisz inicjatywę zasady Centrum zabezpieczeń domyślne:

        Register-AzureRmResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
        $Policy = Get-AzureRmPolicySetDefinition -Name ' [Preview]: Enable Monitoring in Azure Security Center'
        New-AzureRmPolicyAssignment -Name 'ASC Default <d07c0080-170c-4c24-861d-9c817742786c>' -DisplayName 'Security Center Default <subscription ID>' -PolicySetDefinition $Policy -Scope ‘/subscriptions/d07c0080-170c-4c24-861d-9c817742786c’

Masz teraz pomyślnie dołączono Azure Security Center przy użyciu programu PowerShell!

Teraz można tych poleceń cmdlet programu PowerShell za pomocą skryptów automatyzacji programowo iteracji w subskrypcji i zasobów. To pozwala zaoszczędzić czas i zmniejsza prawdopodobieństwo wystąpienia ludzkiego błędu. Możesz użyć tej funkcji [przykładowy skrypt](https://github.com/Microsoft/Azure-Security-Center/blob/master/quickstarts/ASC-Samples.ps1) jako odwołanie.






## <a name="see-also"></a>Zobacz także
Aby dowiedzieć się więcej na temat wykorzystania programu PowerShell do automatyzacji dołączania do usługi Security Center, zobacz następujący artykuł:

* [AzureRM.Security](https://www.powershellgallery.com/packages/AzureRM.Security/0.1.0-preview).

Aby dowiedzieć się więcej o usłudze Security Center, zobacz następujący artykuł:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](security-center-policies.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
