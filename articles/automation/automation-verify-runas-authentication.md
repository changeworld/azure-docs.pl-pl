---
title: Sprawdź poprawność konfiguracji konta usługi Automatyzacja Azure
description: W tym artykule został opisany sposób potwierdzania, że konfiguracja konta usługi Automation jest poprawna.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 5a1e763e12163c115384a09311c2b0177481ae92
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="test-azure-automation-run-as-account-authentication"></a>Sprawdzanie uwierzytelniania konta Uruchom jako usługi Azure Automation
Po pomyślnym utworzeniu konta usługi Automation możesz wykonać prosty test, aby potwierdzić, że możesz poprawnie przeprowadzić uwierzytelnienie w usłudze Azure Resource Manager lub klasycznym wdrożeniu platformy Azure przy użyciu nowo utworzonego lub zaktualizowanego konta Uruchom jako usługi Automation.    

## <a name="automation-run-as-authentication"></a>Uwierzytelnianie konta Uruchom jako usługi Automation
Poniżej przedstawiono kod przykładowy umożliwiający [utworzenie elementu runbook programu PowerShell](automation-creating-importing-runbook.md) w celu weryfikowania uwierzytelniania przy użyciu konta Uruchom jako, a także uwierzytelniania zasobów usługi Resource Manger i zarządzania nimi przy użyciu konta usługi Automation w niestandardowych elementach runbook.   

    $connectionName = "AzureRunAsConnection"
    try
    {
        # Get the connection "AzureRunAsConnection "
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

        "Logging in to Azure..."
        Connect-AzureRmAccount `
           -ServicePrincipal `
           -TenantId $servicePrincipalConnection.TenantId `
           -ApplicationId $servicePrincipalConnection.ApplicationId `
           -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    }
    catch {
       if (!$servicePrincipalConnection)
       {
          $ErrorMessage = "Connection $connectionName not found."
          throw $ErrorMessage
      } else{
          Write-Error -Message $_.Exception
          throw $_.Exception
      }
    }

    #Get all ARM resources from all resource groups
    $ResourceGroups = Get-AzureRmResourceGroup 

    foreach ($ResourceGroup in $ResourceGroups)
    {    
       Write-Output ("Showing resources in resource group " + $ResourceGroup.ResourceGroupName)
       $Resources = Find-AzureRmResource -ResourceGroupNameContains $ResourceGroup.ResourceGroupName | Select ResourceName, ResourceType
       ForEach ($Resource in $Resources)
       {
          Write-Output ($Resource.ResourceName + " of type " +  $Resource.ResourceType)
       }
       Write-Output ("")
    } 

Zwróć uwagę, polecenia cmdlet używane do uwierzytelniania w elemencie runbook - **Connect-AzureRmAccount**, używa *ServicePrincipalCertificate* zestaw parametrów.  Uwierzytelnia się ono za pomocą certyfikatu nazwy głównej usługi, a nie poświadczeń.  

Gdy możesz [uruchamiania elementu runbook](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal) można sprawdzić poprawności konta Uruchom jako [zadanie elementu runbook](automation-runbook-execution.md) utworzeniu zadania zostanie wyświetlona strona i wyświetlić stan zadania w **Podsumowanie zadania** kafelka. Zadanie będzie miało początkowy stan *W kolejce*, wskazujący, że trwa oczekiwanie na udostępnienie procesu roboczego elementu Runbook w chmurze. Następnym stanem będzie *Uruchamianie*, gdy proces roboczy wywołuje zadanie, a następnie *Uruchomiono*, gdy element Runbook faktycznie zacznie działać.  Po zakończeniu zadania elementu Runbook powinniśmy zobaczyć stan **Ukończono**.

Aby wyświetlić szczegółowe wyniki elementu Runbook, kliknij kafelek **Dane wyjściowe**.  Na stronie **Dane wyjściowe** powinna być wyświetlana informacja o pomyślnym uwierzytelnieniu elementu oraz powinna zostać zwrócona lista wszystkich zasobów we wszystkich grupach zasobów w ramach subskrypcji.  

W przypadku ponownego użycia kodu w elementach runbook usuń blok kodu rozpoczynający się od komentarza `#Get all ARM resources from all resource groups`.

## <a name="classic-run-as-authentication"></a>Uwierzytelnianie klasycznego konta Uruchom jako
Poniżej przedstawiono kod przykładowy umożliwiający [utworzenie elementu runbook programu PowerShell](automation-creating-importing-runbook.md) w celu weryfikowania uwierzytelniania przy użyciu klasycznego konta Uruchom jako, a także uwierzytelniania zasobów w klasycznym modelu wdrażania i zarządzania nimi w niestandardowych elementach runbook.  

    $ConnectionAssetName = "AzureClassicRunAsConnection"
    # Get the connection
    $connection = Get-AutomationConnection -Name $connectionAssetName        

    # Authenticate to Azure with certificate
    Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
    if ($Conn -eq $null)
    {
       throw "Could not retrieve connection asset: $ConnectionAssetName. Assure that this asset exists in the Automation account."
    }

    $CertificateAssetName = $Conn.CertificateAssetName
    Write-Verbose "Getting the certificate: $CertificateAssetName" -Verbose
    $AzureCert = Get-AutomationCertificate -Name $CertificateAssetName
    if ($AzureCert -eq $null)
    {
       throw "Could not retrieve certificate asset: $CertificateAssetName. Assure that this asset exists in the Automation account."
    }

    Write-Verbose "Authenticating to Azure with certificate." -Verbose
    Set-AzureSubscription -SubscriptionName $Conn.SubscriptionName -SubscriptionId $Conn.SubscriptionID -Certificate $AzureCert
    Select-AzureSubscription -SubscriptionId $Conn.SubscriptionID
    
    #Get all VMs in the subscription and return list with name of each
    Get-AzureVM | ft Name

W przypadku [uruchomienia elementu runbook](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal) w celu zweryfikowania konta Uruchom jako jest tworzone [zadanie elementu runbook](automation-runbook-execution.md), jest wyświetlana strona Zadanie oraz jest wyświetlany stan zadania w kafelku **Podsumowanie zadania**. Zadanie będzie miało początkowy stan *W kolejce*, wskazujący, że trwa oczekiwanie na udostępnienie procesu roboczego elementu Runbook w chmurze. Następnym stanem będzie *Uruchamianie*, gdy proces roboczy wywołuje zadanie, a następnie *Uruchomiono*, gdy element Runbook faktycznie zacznie działać.  Po zakończeniu zadania elementu Runbook powinniśmy zobaczyć stan **Ukończono**.

Aby wyświetlić szczegółowe wyniki elementu Runbook, kliknij kafelek **Dane wyjściowe**.  Na stronie **Dane wyjściowe** powinna być wyświetlana informacja o pomyślnym uwierzytelnieniu elementu oraz powinna zostać zwrócona lista wszystkich maszyn wirtualnych platformy Azure wdrożonych w ramach subskrypcji według nazwy maszyny wirtualnej.  

W przypadku ponownego użycia kodu w elementach runbook usuń polecenie cmdlet **Get-AzureVM**.

## <a name="next-steps"></a>Kolejne kroki
* Aby rozpocząć pracę z elementami Runbook programu PowerShell, zobacz artykuł [My first PowerShell runbook](automation-first-runbook-textual-powershell.md) (Mój pierwszy element Runbook programu PowerShell).
* Aby dowiedzieć się więcej na temat tworzenia elementów graficznych, zobacz [Graphical authoring in Azure Automation](automation-graphical-authoring-intro.md) (Tworzenie elementów graficznych w usłudze Azure Automation).
