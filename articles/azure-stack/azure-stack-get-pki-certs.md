---
title: Generowanie certyfikatów infrastruktury kluczy publicznych stosu Azure stosu Azure zintegrowanych systemów wdrożenia | Dokumentacja firmy Microsoft
description: Zawiera opis procesu wdrażania certyfikatu PKI stosu Azure stosu Azure zintegrowanych systemów.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: cfac573bc9f1bdec3fd884f8090e11514f1e93b3
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604713"
---
# <a name="azure-stack-certificates-signing-request-generation"></a>Azure stosu certyfikaty podpisywania generowania żądania

Narzędzie sprawdzania gotowości stosu Azure opisano w tym artykule jest dostępny [z galerii programu PowerShell](https://aka.ms/AzsReadinessChecker). Narzędzie tworzy odpowiedni w przypadku wdrożenia usługi Azure stosu żądania podpisania certyfikatu (CSR). Certyfikaty powinny żądanie, generowane i zweryfikowana za pomocą wystarczająco dużo czasu na test przed przystąpieniem do wdrożenia.

Narzędzie sprawdzania gotowości stosu Azure (AzsReadinessChecker) wykonuje następujące żądania certyfikatu:

 - **Standardowy certyfikat żądań**  
    Żądania zgodnie z [wygenerować certyfikaty PKI dla wdrożenia stosu Azure](azure-stack-get-pki-certs.md).
 - **Typ żądania**  
    Określa, czy żądania podpisania certyfikatu będzie żądanie jednego lub wielu żądań.
 - **Platforma jako usługa**  
    Opcjonalnie żądania platforma jako usługa (PaaS) nazwy z certyfikatami określonymi w [wymagania certyfikatów infrastruktury kluczy publicznych stosu Azure - opcjonalne certyfikaty PaaS](azure-stack-pki-certs.md#optional-paas-certificates).



## <a name="prerequisites"></a>Wymagania wstępne

System powinna spełniać następujące wymagania wstępne, przed wygenerowaniem CSR(s) dla certyfikatów PKI dla wdrożenia usługi Azure stosu:

 - Narzędzie sprawdzania gotowości stosu platformy Microsoft Azure
 - Atrybuty certyfikatu:
    - Nazwa regionu
    - Zewnętrzne w pełni kwalifikowana nazwa domeny (FQDN)
    - Temat
 - Windows 10 lub Windows Server 2016
 
  > [!NOTE]
  > Po wyświetleniu certyfikaty kopię z urzędu certyfikacji kroki opisane w [przygotowanie Azure stosu certyfikatówpki](azure-stack-prepare-pki-certs.md) musi być wykonana na tym samym systemie!

## <a name="generate-certificate-signing-requests"></a>Generowanie żądania podpisania certyfikatu

Do przygotowania i sprawdzania poprawności certyfikatów PKI stosu Azure, wykonaj następujące kroki: 

1.  Zainstaluj AzsReadinessChecker w wierszu PowerShell (5.1 lub nowszej), uruchamiając następujące polecenie cmdlet:

    ````PowerShell  
        Install-Module Microsoft.AzureStack.ReadinessChecker
    ````

2.  Deklarowanie **podmiotu** jako słownik uporządkowanej. Na przykład: 

    ````PowerShell  
    $subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"} 
    ````
    > [!note]  
    > Jeśli zostanie podana nazwa pospolita (CN) to zostanie zastąpiony nazwą DNS pierwszego żądania certyfikatu.

3.  Deklarowanie katalog wyjściowy, który już istnieje. Na przykład:

    ````PowerShell  
    $outputDirectory = "$ENV:USERPROFILE\Documents\AzureStackCSR"
    ````
4.  Deklarowanie zidentyfikować systemu

    Usługa Azure Active Directory

    ```PowerShell
    $IdentitySystem = "AAD"
    ````

    Usługi federacyjne Active Directory

    ```PowerShell
    $IdentitySystem = "ADFS"
    ````

5. Deklarowanie **nazwa regionu** i **nazwy FQDN zewnętrznej** przeznaczonych do wdrożenia usługi Azure stosu.

    ```PowerShell
    $regionName = 'east'
    $externalFQDN = 'azurestack.contoso.com'
    ````

    > [!note]  
    > `<regionName>.<externalFQDN>` stanowi podstawę, w którym zewnętrznych nazw DNS w stosie Azure są tworzone w tym przykładzie, portalu będzie `portal.east.azurestack.contoso.com`.  

6. Aby wygenerować żądanie certyfikatu jednego z wielu nazwy alternatywnej podmiotu:

    ```PowerShell  
    Start-AzsReadinessChecker -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -RequestType SingleCSR -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ````

    Aby uwzględnić PaaS usługi określają przełącznika ```-IncludePaaS```

7. Aby wygenerować certyfikat podpisywania żądań dla każdej nazwy DNS:

    ```PowerShell  
    Start-AzsReadinessChecker -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -RequestType MultipleCSR -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ````

    Aby uwzględnić PaaS usługi określają przełącznika ```-IncludePaaS```

8. Przejrzyj dane wyjściowe:

    ````PowerShell  
    AzsReadinessChecker v1.1803.405.3 started
    Starting Certificate Request Generation

    CSR generating for following SAN(s): dns=*.east.azurestack.contoso.com&dns=*.blob.east.azurestack.contoso.com&dns=*.queue.east.azurestack.contoso.com&dns=*.table.east.azurestack.cont
    oso.com&dns=*.vault.east.azurestack.contoso.com&dns=*.adminvault.east.azurestack.contoso.com&dns=portal.east.azurestack.contoso.com&dns=adminportal.east.azurestack.contoso.com&dns=ma
    nagement.east.azurestack.contoso.com&dns=adminmanagement.east.azurestack.contoso.com
    Present this CSR to your Certificate Authority for Certificate Generation: C:\Users\username\Documents\AzureStackCSR\wildcard_east_azurestack_contoso_com_CertRequest_20180405233530.req
    Certreq.exe output: CertReq: Request Created

    Finished Certificate Request Generation

    AzsReadinessChecker Log location: C:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.ReadinessChecker\1.1803.405.3\AzsReadinessChecker.log
    AzsReadinessChecker Completed
    ````

9.  Przedstawia **. Liczba ŻĄDAŃ** pliku wygenerowane do urzędu certyfikacji (wewnętrzny lub publiczny).  Katalog wyjściowy **Start AzsReadinessChecker** zawiera CSR(s) niezbędne do przesłania do urzędu certyfikacji.  Zawiera ona także podrzędnych katalog zawierający pliki INF używane podczas generowania żądania certyfikatu, jako odwołanie. Pamiętaj, że urząd certyfikacji generuje certyfikaty przy użyciu wygenerowanego żądania, które spełnia [wymagań dotyczących infrastruktury kluczy publicznych stosu Azure](azure-stack-pki-certs.md).

## <a name="next-steps"></a>Kolejne kroki

[Przygotuj certyfikaty PKI stosu Azure](azure-stack-prepare-pki-certs.md)
