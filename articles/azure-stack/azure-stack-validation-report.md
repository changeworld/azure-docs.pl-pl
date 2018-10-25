---
title: Raport weryfikacji dla usługi Azure Stack | Dokumentacja firmy Microsoft
description: Aby przejrzeć wyniki sprawdzania poprawności, należy użyć raportu narzędzie do sprawdzania gotowości usługi Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/23/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 1b2b06c02dc54c4369dd8490b714d1444d4b3b01
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2018
ms.locfileid: "49986189"
---
# <a name="azure-stack-validation-report"></a>Raport weryfikacji usługi Azure Stack
Można uruchomić operacji walidacji, które obsługują wdrażanie i obsługa środowiska Azure Stack, należy użyć narzędzia narzędzie do sprawdzania gotowości usługi Azure Stack. Narzędzie zapisuje wyniki do pliku raportu JSON. Raport przedstawia szczegółowe i sumaryczne dane o stanie wymagania wstępne dotyczące wdrażania usługi Azure Stack. Przedstawia również informacje o rotacji kluczy tajnych dla istniejących wdrożeń programu Azure Stack.  

 ## <a name="where-to-find-the-report"></a>Gdzie można znaleźć w raporcie
Po uruchomieniu narzędzia rejestruje ono wyniki do **AzsReadinessCheckerReport.json**. Narzędzie tworzy również dziennika o nazwie **AzsReadinessChecker.log**. Wyświetla lokalizację tych plików z wynikami weryfikacji w programie PowerShell.

![Sprawdzanie poprawności na przebieg](./media/azure-stack-validation-report/validation.png)

Oba pliki są zachowywane wyniki testów weryfikacyjnych kolejne, gdy są uruchomione na tym samym komputerze.  Na przykład narzędzie można uruchomić do sprawdzenia poprawności certyfikatów, uruchom ponownie, aby zweryfikować tożsamość w systemie Azure i następnie po raz trzeci do sprawdzania poprawności rejestracji. Wyniki wszystkich trzech operacji sprawdzania poprawności są dostępne w raporcie wynikowy JSON.  

Domyślnie oba pliki są zapisywane w *C:\Users\<username > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
- Użyj **- OutputPath** ***&lt;ścieżki&gt;*** parametru na końcu wiersza polecenia do określenia lokalizacji inny raport.   
- Użyj **- CleanReport** parametru na końcu polecenia uruchomienia, aby wyczyścić informacje z *AzsReadinessCheckerReport.json*. temat poprzednie uruchomienia tego narzędzia.

## <a name="view-the-report"></a>Wyświetl raport
Aby wyświetlić raport w programie PowerShell, należy podać ścieżkę do raportu jako wartość **- ReportPath**. To polecenie wyświetla zawartość raportu i identyfikuje sprawdzanie poprawności, które nie dysponują jeszcze wyników.

Na przykład aby wyświetlić raport z wiersz polecenia programu PowerShell, która jest otwarta do lokalizacji, w którym znajduje się raport, uruchom polecenie: 
   > `Read-AzsReadinessReport -ReportPath .\AzsReadinessReport.json` 

Dane wyjściowe podobne do następującego:

````PowerShell
Reading All Validation(s) from Report C:\Contoso-AzsReadinessCheckerReport.json

############### Certificate Validation Summary ###############

Certificate Validation results not available.

############### Registration Validation Summary ###############

Azure Registration Validation results not available.

############### Azure Identity Results ###############

Test                          : ServiceAdministrator
Result                        : OK
AAD Service Admin             : admin@contoso.onmicrosoft.com
Azure Environment             : AzureCloud
Azure Active Directory Tenant : contoso.onmicrosoft.com
Error Details                 : 

############### Azure Identity Validation Summary ###############

    Azure Identity Validation found no errors or warnings.

############### Azure Stack Graph Validation Summary ###############

Azure Stack Graph Validation results not available.

############### Azure Stack ADFS Validation Summary ###############

Azure Stack ADFS Validation results not available.

############### AzsReadiness Job Summary ###############

Index             : 0
Operations        : 
StartTime         : 2018/10/22 14:24:16
EndTime           : 2018/10/22 14:24:19
Duration          : 3
PSBoundParameters : 
````

## <a name="view-the-report-summary"></a>Wyświetl podsumowanie raportu
Aby wyświetlić podsumowanie raportu, można dodać **— Podsumowanie** przełączyć się na końcu wiersza polecenia programu PowerShell. Na przykład: 
 > `Read-AzsReadinessReport -ReportPath .\Contoso-AzsReadinessReport.json -summary`  

Podsumowanie pokazuje operacji sprawdzania poprawności, które nie mają wyniki i wskazuje zakończone powodzeniem lub niepowodzeniem dla operacji sprawdzania poprawności, które zostaną zakończone. Dane wyjściowe podobne do następującego:

````PowerShell
Reading All Validation(s) from Report C:\Contoso-AzsReadinessCheckerReport.json

############### Certificate Validation Summary ###############

    Certificate Validation found no errors or warnings.
    
############### Registration Validation Summary ###############

    Registration Validation found no errors or warnings.

############### Azure Identity Validation Summary ###############

    Azure Identity Validation found no errors or warnings.

############### Azure Stack Graph Validation Summary ###############

Azure Stack Graph Validation results not available.

############### Azure Stack ADFS Validation Summary ###############

Azure Stack ADFS Validation results not available.
````


## <a name="view-a-filtered-report"></a>Wyświetl filtrowanego raportu
Aby wyświetlić raport, który jest filtrowana na jednym typie sprawdzania poprawności, należy użyć **- ReportSections** parametru przy użyciu jednego z następujących wartości:
- Certyfikat
- AzureRegistration
- AzureIdentity
- Graph
- ADFS
- Stanowiska   
- Wszyscy  

Na przykład aby wyświetlić raport podsumowania dla certyfikatów tylko wtedy, użyj następującego polecenia programu PowerShell: 
 > `Read-AzsReadinessReport -ReportPath .\Contoso-AzsReadinessReport.json -ReportSections Certificate – Summary`


## <a name="see-also"></a>Zobacz także
