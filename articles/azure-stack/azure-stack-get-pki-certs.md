---
title: Generowanie certyfikatów infrastruktury kluczy publicznych usługi Azure Stack dla usługi Azure Stack zintegrowane systemy wdrażania | Dokumentacja firmy Microsoft
description: W tym artykule opisano proces wdrażania certyfikatów infrastruktury kluczy publicznych do usługi Azure Stack w systemach zintegrowanych w usłudze Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: mabrigg
ms.reviewer: ppacent
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: e0556eb5cc3d0f140067a4e3b4a9054a47b91417
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58481490"
---
# <a name="azure-stack-certificates-signing-request-generation"></a>Usługa Azure Stack certyfikaty podpisywania generowania żądania

Narzędzie sprawdzania gotowości stosu platformy Azure do tworzenia żądania podpisania certyfikatu (CSR) jest odpowiedni dla wdrożenia usługi Azure Stack. Certyfikaty powinny zażądano, wygenerowany i sprawdzona wystarczająco dużo czasu, aby przetestować przed wdrożeniem. Możesz uzyskać narzędzia [z galerii programu PowerShell](https://aka.ms/AzsReadinessChecker).

Można użyć narzędzia narzędzie do sprawdzania gotowości usługi Azure Stack (AzsReadinessChecker) do żądania certyfikatów następujące:

- **Standardowe żądania certyfikatów** zgodnie z opisem w [generowanie certyfikatów PKI dla wdrażania usługi Azure Stack](azure-stack-get-pki-certs.md).
- **Platforma jako usługa**: Możesz poprosić o platforma jako usługa (PaaS) nazwy certyfikatów, jak to określono w [wymagania certyfikatów infrastruktury kluczy publicznych usługi Azure Stack — opcjonalnie certyfikaty PaaS](azure-stack-pki-certs.md#optional-paas-certificates).

## <a name="prerequisites"></a>Wymagania wstępne

System powinien spełniać następujące wymagania wstępne, przed wygenerowaniem CSR(s) certyfikatów PKI dla wdrożenia usługi Azure Stack:

- Narzędzie do sprawdzania gotowości platformy Microsoft Azure Stack
- Atrybuty certyfikatu:
  - Nazwa regionu
  - Zewnętrzne w pełni kwalifikowana nazwa domeny (FQDN)
  - Podmiot
- Windows 10 lub Windows Server 2016

  > [!NOTE]  
  > Po otrzymaniu certyfikatów kopii ze swoim urzędem certyfikacji w krokach w [przygotowywanie usługi Azure Stack certyfikatówpki](azure-stack-prepare-pki-certs.md) będą musieli wykonać na tym samym systemie!

## <a name="generate-certificate-signing-requests"></a>Generowanie żądania podpisania certyfikatu

Wykonaj następujące kroki, aby przygotować i sprawdzenia poprawności certyfikatów infrastruktury kluczy publicznych do usługi Azure Stack:

1. Zainstaluj AzsReadinessChecker z wiersz polecenia programu PowerShell (5.1 lub nowszej), uruchamiając następujące polecenie cmdlet:

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker
    ```

2. Zadeklaruj **podmiotu** jako słownik uporządkowany. Na przykład:

    ```powershell  
    $subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
    ```

    > [!note]  
    > Jeśli zostanie podana nazwa pospolita (CN) to zostanie ono zastąpione przez nazwy DNS pierwszego żądania certyfikatu.

3. Zadeklaruj katalog wyjściowy, który już istnieje. Na przykład:

    ```powershell  
    $outputDirectory = "$ENV:USERPROFILE\Documents\AzureStackCSR"
    ```

4. Zadeklaruj systemu tożsamości

    Usługa Azure Active Directory

    ```powershell
    $IdentitySystem = "AAD"
    ```

    Usługi Active Directory Federation Services

    ```powershell
    $IdentitySystem = "ADFS"
    ```

5. Zadeklaruj **nazwa regionu** i **FQDN zewnętrznej** przeznaczone do wdrożenia usługi Azure Stack.

    ```powershell
    $regionName = 'east'
    $externalFQDN = 'azurestack.contoso.com'
    ```

    > [!note]  
    > `<regionName>.<externalFQDN>` stanowi podstawę, na którym są tworzone zewnętrznych nazw DNS w usłudze Azure Stack, w tym przykładzie, byłoby portalu `portal.east.azurestack.contoso.com`.  

6. Aby wygenerować certyfikat podpisywania żądania dla każdej nazwy DNS:

    ```powershell  
    New-AzsCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

    Aby uwzględnić usługi PaaS, określ przełącznik ```-IncludePaaS```

7. Możesz też dla środowisk projektowych/testowych, aby wygenerować żądanie certyfikatu jednego z wielu nazwy alternatywnej podmiotu dodać **- RequestType SingleCSR** parametru i wartości (**nie** zalecane dla środowisk produkcyjnych):

    ```powershell  
    New-AzsCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -RequestType SingleCSR -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

    Aby uwzględnić usługi PaaS, określ przełącznik ```-IncludePaaS```

8. Przejrzyj dane wyjściowe:

    ```powershell  
    New-AzsCertificateSigningRequest v1.1809.1005.1 started.

    CSR generating for following SAN(s): dns=*.east.azurestack.contoso.com&dns=*.blob.east.azurestack.contoso.com&dns=*.queue.east.azurestack.contoso.com&dns=*.table.east.azurestack.cont
    oso.com&dns=*.vault.east.azurestack.contoso.com&dns=*.adminvault.east.azurestack.contoso.com&dns=portal.east.azurestack.contoso.com&dns=adminportal.east.azurestack.contoso.com&dns=ma
    nagement.east.azurestack.contoso.com&dns=adminmanagement.east.azurestack.contoso.com*dn2=*.adminhosting.east.azurestack.contoso.com@dns=*.hosting.east.azurestack.contoso.com
    Present this CSR to your Certificate Authority for Certificate Generation: C:\Users\username\Documents\AzureStackCSR\wildcard_east_azurestack_contoso_com_CertRequest_20180405233530.req
    Certreq.exe output: CertReq: Request Created

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    New-AzsCertificateSigningRequest Completed
    ```

9. Prześlij **. Liczba ŻĄDAŃ** pliku wygenerowanego do swojego urzędu certyfikacji (wewnętrznego lub publicznego).  Katalog wyjściowy **New AzsCertificateSigningRequest** zawiera CSR(s) niezbędne do przesłania do urzędu certyfikacji.  Katalog zawiera również, dla której można się odwołać, podrzędnych katalogu zawierającego pliki INF używane podczas generowania żądania certyfikatu. Pamiętaj, że urząd certyfikacji generuje certyfikaty przy użyciu wygenerowanego żądania, które spełniają [wymagania dotyczące usługi Azure Stack PKI](azure-stack-pki-certs.md).

## <a name="next-steps"></a>Kolejne kroki

[Przygotowywanie certyfikatów infrastruktury kluczy publicznych do usługi Azure Stack](azure-stack-prepare-pki-certs.md)
