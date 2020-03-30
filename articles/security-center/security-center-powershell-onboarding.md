---
title: Wbudowany program Azure Security Center z programem PowerShell
description: Ten dokument przeprowadzi Cię przez proces dołączania usługi Azure Security Center przy użyciu poleceń cmdlet programu PowerShell.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e400fcbf-f0a8-4e10-b571-5a0d0c3d0c67
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/02/2018
ms.author: memildin
ms.openlocfilehash: 5aaaf539c07a7ba2c2463d5bfd1f452853f52379
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603692"
---
# <a name="automate-onboarding-of-azure-security-center-using-powershell"></a>Automatyzacja dołączania usługi Azure Security Center przy użyciu programu PowerShell

Możesz zabezpieczyć swoje obciążenia platformy Azure programowo, używając modułu Programu Azure Security Center PowerShell.
Za pomocą programu PowerShell umożliwia automatyzację zadań i uniknąć błędu ludzkiego nieodłącznie związane z zadaniami ręcznymi. Jest to szczególnie przydatne w wdrożeniach na dużą skalę, które obejmują dziesiątki subskrypcji z setkami i tysiącami zasobów — wszystkie z nich muszą być zabezpieczone od początku.

Dołączanie usługi Azure Security Center przy użyciu programu PowerShell umożliwia programowo zautomatyzować dołączanie zasobów platformy Azure i zarządzanie nimi oraz dodać niezbędne mechanizmy kontroli zabezpieczeń.

Ten artykuł zawiera przykładowy skrypt programu PowerShell, który można modyfikować i używać w środowisku do wdrażania usługi Security Center w ramach subskrypcji. 

W tym przykładzie włączymy usługę Security Center w ramach subskrypcji o identyfikatorze: d07c0080-170c-4c24-861d-9c817742786c i zastosujemy zalecane ustawienia zapewniające wysoki poziom ochrony, implementując standardową warstwę Centrum zabezpieczeń, która zapewnia zaawansowane możliwości ochrony przed zagrożeniami i wykrywania:

1. Ustaw [standardowy poziom ochrony centrum zabezpieczeń](https://azure.microsoft.com/pricing/details/security-center/). 
 
2. Ustaw obszar roboczy usługi Log Analytics, do którego agent monitorujący firmy Microsoft będzie wysyłał dane, które zbiera na maszynach wirtualnych skojarzonych z subskrypcją — w tym przykładzie istniejący obszar roboczy zdefiniowany przez użytkownika (myWorkspace).

3. Uaktywnianie automatycznego inicjowania obsługi administracyjnej programu Security Center, który [wdraża agenta monitorowania firmy Microsoft.](security-center-enable-data-collection.md#auto-provision-mma)

5. Ustaw [ciso organizacji jako kontakt zabezpieczeń dla alertów Centrum zabezpieczeń i znaczących zdarzeń](security-center-provide-security-contact-details.md).

6. Przypisz [domyślne zasady zabezpieczeń](tutorial-security-policy.md)usługi Security Center .

## <a name="prerequisites"></a>Wymagania wstępne

Te kroki należy wykonać przed uruchomieniem poleceń cmdlet centrum zabezpieczeń:

1.  Uruchom program PowerShell jako administrator.
2.  Uruchom następujące polecenia w programie PowerShell:
      
        Set-ExecutionPolicy -ExecutionPolicy AllSigned
        Install-Module -Name Az.Security -Force

## <a name="onboard-security-center-using-powershell"></a>Wbudowana usługa Security Center przy użyciu programu PowerShell

1.  Zarejestruj swoje subskrypcje u dostawcy zasobów usługi Security Center:

        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
        Register-AzResourceProvider -ProviderNamespace 'Microsoft.Security' 

2.  Opcjonalnie: ustaw poziom pokrycia (warstwę cenową) subskrypcji (jeśli nie zdefiniowano, warstwa cenowa jest ustawiona na Bezpłatna):

        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
        Set-AzSecurityPricing -Name "default" -PricingTier "Standard"

3.  Skonfiguruj obszar roboczy usługi Log Analytics, do którego będą raportować agenci. Musisz mieć obszar roboczy usługi Log Analytics, który został już utworzony, do którego będą raportować maszyny wirtualne subskrypcji. Można zdefiniować wiele subskrypcji do raportu do tego samego obszaru roboczego. Jeśli nie zostanie zdefiniowany, zostanie użyty domyślny obszar roboczy.

        Set-AzSecurityWorkspaceSetting -Name "default" -Scope
        "/subscriptions/d07c0080-170c-4c24-861d-9c817742786c" -WorkspaceId"/subscriptions/d07c0080-170c-4c24-861d-9c817742786c/resourceGroups/myRg/providers/Microsoft.OperationalInsights/workspaces/myWorkspace"

4.  Automatyczne udostępnianie instalacji agenta monitorowania firmy Microsoft na maszynach wirtualnych platformy Azure:
    
        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
    
        Set-AzSecurityAutoProvisioningSetting -Name "default" -EnableAutoProvision

    > [!NOTE]
    > Zaleca się, aby włączyć automatyczne inicjowanie obsługi administracyjnej, aby upewnić się, że maszyny wirtualne platformy Azure są automatycznie chronione przez usługę Azure Security Center.
    >

5.  Opcjonalnie: zdecydowanie zaleca się zdefiniowanie danych kontaktowych zabezpieczeń dla wbudowanych subskrypcji, które będą używane jako adresaci alertów i powiadomień generowanych przez centrum zabezpieczeń:

        Set-AzSecurityContact -Name "default1" -Email "CISO@my-org.com" -Phone "2142754038" -AlertAdmin -NotifyOnAlert 

6.  Przypisz domyślną inicjatywę zasad Centrum zabezpieczeń:

        Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
        $Policy = Get-AzPolicySetDefinition | where {$_.Properties.displayName -EQ '[Preview]: Enable Monitoring in Azure Security Center'}
        New-AzPolicyAssignment -Name 'ASC Default <d07c0080-170c-4c24-861d-9c817742786c>' -DisplayName 'Security Center Default <subscription ID>' -PolicySetDefinition $Policy -Scope '/subscriptions/d07c0080-170c-4c24-861d-9c817742786c'

Teraz pomyślnie wbudowane centrum zabezpieczeń platformy Azure z programem PowerShell!

Teraz można użyć tych poleceń cmdlet programu PowerShell ze skryptami automatyzacji do programowo iteracji między subskrypcjami i zasobami. Pozwala to zaoszczędzić czas i zmniejszyć prawdopodobieństwo wystąpienia błędu ludzkiego. Tego [przykładowego skryptu](https://github.com/Microsoft/Azure-Security-Center/blob/master/quickstarts/ASC-Samples.ps1) można użyć jako odwołania.






## <a name="see-also"></a>Zobacz też
Aby dowiedzieć się więcej o tym, jak zautomatyzować dołączanie do centrum zabezpieczeń za pomocą programu PowerShell, zobacz następujący artykuł:

* [Az.Security](https://docs.microsoft.com/powershell/module/az.security).

Aby dowiedzieć się więcej o umyciu Centrum zabezpieczeń, zobacz następujący artykuł:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.