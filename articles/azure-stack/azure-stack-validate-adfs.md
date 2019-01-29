---
title: Zweryfikuj integrację usług AD FS dla usługi Azure Stack
description: Narzędzie sprawdzania gotowości stosu Azure umożliwia sprawdzanie poprawności integracji usług AD FS na potrzeby usługi Azure Stack.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/28/2019
ms.author: patricka
ms.reviewer: jerskine
ms.openlocfilehash: 7e6c54add856a69e1750b0b6ca0a058c2d80bfd8
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2019
ms.locfileid: "55101740"
---
# <a name="validate-ad-fs-integration-for-azure-stack"></a>Zweryfikuj integrację usług AD FS dla usługi Azure Stack

Aby sprawdzić, czy środowisko jest gotowe do integracji usługi Active Directory Federation Services (AD FS) przy użyciu usługi Azure Stack, należy użyć narzędzia narzędzie do sprawdzania gotowości usługi Azure Stack (AzsReadinessChecker). Sprawdź poprawność integracji usług AD FS, przed rozpoczęciem integracji centrum danych lub przed wdrożeniem usługi Azure Stack.

Narzędzie sprawdzania gotowości weryfikuje:

* *Metadanych Federacji* zawiera prawidłowe elementy XML dla Federacji.
* *Certyfikat SSL usług AD FS* mogą zostać pobrane i łańcuch zaufania może zostać utworzony. Dla sygnatury usług AD FS muszą ufać łańcucha certyfikatów SSL. Certyfikat musi być podpisany przez ten sam *urząd certyfikacji* używany na potrzeby certyfikatów wdrożenia usługi Azure Stack lub przez zaufany główny urząd partnera. Aby uzyskać pełną listę partnerów urząd zaufany główny urząd certyfikacji, zobacz [TechNet](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).
* *Certyfikatu podpisywania usług AD FS* jest zaufaną i nie jest bliskie wygaśnięcia.

Aby uzyskać więcej informacji na temat integracji centrum danych usługi Azure Stack, zobacz [Integracja z centrum danych usługi Azure Stack — tożsamość](azure-stack-integrate-identity.md).

## <a name="get-the-readiness-checker-tool"></a>Pobierz narzędzie sprawdzania gotowości

Pobierz najnowszą wersję narzędzia narzędzie do sprawdzania gotowości usługi Azure Stack (AzsReadinessChecker) z [galerii programu PowerShell](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Wymagania wstępne

Należy spełnić następujące wymagania wstępne.

**Komputer, na którym działa narzędzie:**

* System Windows 10 lub Windows Server 2016 przy użyciu łączności z domeną.
* Program PowerShell 5.1 lub nowszej. Aby sprawdzić swoją wersję, uruchom następujące polecenie programu PowerShell, a następnie przejrzyj *głównych* wersji i *pomocnicza* wersji:  
   > `$PSVersionTable.PSVersion`
* Najnowszą wersję [Microsoft Azure Stack gotowości Checker](https://aka.ms/AzsReadinessChecker) narzędzia.

**Active Directory Federation Services środowiska:**

Potrzebne są co najmniej jeden z następujących postaci metadanych:

* Adres URL metadanych federacji usług AD FS. Może to być na przykład `https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`.
* Pliku XML metadanych federacji. Przykładem jest FederationMetadata.xml.

## <a name="validate-ad-fs-integration"></a>Weryfikowanie integracji usług AD FS

1. Na komputerze, który spełnia wymagania wstępne Otwórz administracyjny wiersz polecenia PowerShell, a następnie uruchom następujące polecenie, aby zainstalować AzsReadinessChecker:

     `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

1. W wierszu polecenia programu PowerShell uruchom następujące polecenie, aby rozpocząć sprawdzanie poprawności. Określ wartość **- CustomADFSFederationMetadataEndpointUri** jako identyfikator URI dla metadanych federacji.

     `Invoke-AzsADFSValidation -CustomADFSFederationMetadataEndpointUri https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`

1. Po uruchomieniu narzędzia, przejrzyj dane wyjściowe. Upewnij się, że jest w stanie OK dla wymagania dotyczące integracji usług AD FS. Pomyślnej weryfikacji jest podobny do poniższego przykładu:

    ```
    Invoke-AzsADFSValidation v1.1809.1001.1 started.

    Testing ADFS Endpoint https://sts.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

            Read Metadata:                         OK
            Test Metadata Elements:                OK
            Test SSL ADFS Certificate:             OK
            Test Certificate Chain:                OK
            Test Certificate Expiry:               OK

    Details:
    [-] In standalone mode, some tests should not be considered fully indicative of connectivity or readiness the Azure Stack Stamp requires prior to Data Center Integration.
    Additional help URL: https://aka.ms/AzsADFSIntegration

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json

    Invoke-AzsADFSValidation Completed
    ```

W środowiskach produkcyjnych testowanie certyfikat tworzy łańcuch zaufania z poziomu stacji roboczej operator nie jest w pełni wskazujące na poziom zaufania PKI w infrastrukturze Azure Stack. Sygnatury usługi Azure Stack sieci publicznych adresów VIP potrzebuje łączność z listy odwołania certyfikatów infrastruktury kluczy publicznych.

## <a name="report-and-log-file"></a>Raport i plik dziennika

Uruchamia każdego czasu sprawdzania poprawności, rejestruje ono wyniki do **AzsReadinessChecker.log** i **AzsReadinessCheckerReport.json**. Lokalizacja tych plików pojawia się wynikami sprawdzania poprawności w programie PowerShell.

Pliki sprawdzania poprawności mogą pomóc udostępnianie stanu przed rozpoczęciem wdrażania usługi Azure Stack lub badania problemów weryfikacji. Oba pliki są zachowywane wyniki każdego sprawdzenie poprawności kolejne. Raport zawiera potwierdzenie zespołu wdrażania konfiguracji tożsamości. Plik dziennika może pomóc zespołowi pomocy technicznej lub wdrożenia badać problemy ze sprawdzaniem poprawności.

Domyślnie oba pliki są zapisywane w `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\`.

Użycie:

* **-OutputPath**: *Ścieżki* parametru na końcu polecenia uruchomienia, aby określić lokalizację inny raport.
* **-CleanReport**: Parametr na końcu polecenia uruchomienia, aby wyczyścić AzsReadinessCheckerReport.json poprzednich informacji w raporcie. Aby uzyskać więcej informacji, zobacz [raportu sprawdzania poprawności w usłudze Azure Stack](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Błędy sprawdzania poprawności

Jeśli sprawdzenie poprawności zakończy się niepowodzeniem, szczegółowe informacje o awarii są wyświetlane w oknie programu PowerShell. Narzędzie rejestruje także informacje *AzsReadinessChecker.log*.

Poniższe przykłady zapewnić wskazówki dotyczące typowych błędów sprawdzania poprawności.

### <a name="command-not-found"></a>Nie znaleziono polecenia

`Invoke-AzsADFSValidation : The term 'Invoke-AzsADFSValidation' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.`

**Przyczyna**: Nie można prawidłowo załadować moduł sprawdzania gotowości Załaduj programu PowerShell.

**Rozwiązanie**: Zaimportuj moduł sprawdzania gotowości jawnie. Skopiuj i wklej następujący kod do programu PowerShell i zaktualizuj \<wersji\> numerem dla obecnie zainstalowanej wersji.

`Import-Module "c:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.ReadinessChecker\<version>\Microsoft.AzureStack.ReadinessChecker.psd1" -Force`

## <a name="next-steps"></a>Kolejne kroki

[Wyświetl raport gotowości](azure-stack-validation-report.md)  
[Zagadnienia dotyczące integracji usługi Azure Stack ogólne](azure-stack-datacenter-integration.md)  
