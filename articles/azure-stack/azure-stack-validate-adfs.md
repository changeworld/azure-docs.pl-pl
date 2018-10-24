---
title: Weryfikowanie integracji usług AD FS dla usługi Azure Stack
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
ms.date: 10/22/2018
ms.author: patricka
ms.reviewer: jerskine
ms.openlocfilehash: 0aa13f2fced9122163d5278b5bb50cc1e11a0379
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2018
ms.locfileid: "49947267"
---
# <a name="validate-adfs-integration-for-azure-stack"></a>Zweryfikuj integrację usług AD FS dla usługi Azure Stack

Aby sprawdzić, czy środowisko jest gotowe do integracji usług AD FS z usługą Azure Stack, należy użyć narzędzia narzędzie do sprawdzania gotowości usługi Azure Stack (AzsReadinessChecker). Należy sprawdzić, czy integracji usług AD FS przed rozpoczęciem integracji centrum danych lub przed wdrożeniem usługi Azure Stack.

Narzędzie sprawdzania gotowości weryfikuje:

* *Metadanych Federacji* zawiera prawidłowe elementy XML dla Federacji.
* *Certyfikat SSL usług AD FS* mogą być pobierane i mogą być wbudowane w łańcuchu zaufania. Dla sygnatury usług AD FS muszą ufać łańcucha certyfikatów SSL. Certyfikat musi być podpisany przez ten sam *urząd certyfikacji* jako certyfikaty wdrożenia usługi Azure Stack lub przez partnera zaufanego głównego urzędu. Aby uzyskać pełną listę partnerów urząd zaufany główny urząd certyfikacji Zobacz: https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca
* *Certyfikat podpisywania usług AD FS* jest zaufaną i nie jest bliskie wygaśnięcia.

Aby uzyskać więcej informacji na temat integracji centrum danych usługi Azure Stack, zobacz [Integracja z centrum danych usługi Azure Stack — tożsamość](azure-stack-integrate-identity.md)

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

* Adres URL metadanych federacji usług AD FS. Na przykład: `https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`
* Pliku XML metadanych federacji. Na przykład FederationMetadata.xml

## <a name="validate-adfs-integration"></a>Weryfikowanie integracji usług AD FS

1. Na komputerze, który spełnia wymagania wstępne Otwórz administracyjny wiersz polecenia PowerShell, a następnie uruchom następujące polecenie, aby zainstalować AzsReadinessChecker.

     `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

1. W wierszu polecenia programu PowerShell uruchom następujące polecenie, aby rozpocząć sprawdzanie poprawności. Określ wartość **- CustomADFSFederationMetadataEndpointUri** jako identyfikator URI dla metadanych Federacji:

     `Invoke-AzsADFSValidation -CustomADFSFederationMetadataEndpointUri https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`

1. Po uruchomieniu narzędzia, przejrzyj dane wyjściowe. Upewnij się, że stan jest odpowiedni dla wymagań integracji usługi AD FS. Pomyślnej weryfikacji, zostanie wyświetlony podobny do następującego.

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

W środowiskach produkcyjnych testowanie certyfikat tworzy łańcuch zaufania stacji roboczej operatorów nie należy traktować pełni wskazujące na poziom zaufania PKI w infrastrukturze Azure Stack. Sieć publicznych adresów VIP sygnatury usługi Azure Stack musi mieć łączność z listy CRL dla infrastruktury kluczy publicznych.

## <a name="report-and-log-file"></a>Raport i plik dziennika

Uruchamia każdego czasu sprawdzania poprawności, rejestruje ono wyniki do **AzsReadinessChecker.log** i **AzsReadinessCheckerReport.json**. Wyświetla lokalizację tych plików z wynikami weryfikacji w programie PowerShell.

Pliki sprawdzania poprawności mogą pomóc udostępnianie stanu przed rozpoczęciem wdrażania usługi Azure Stack lub badania problemów weryfikacji. Oba pliki są zachowywane wyniki każdego sprawdzenie poprawności kolejne. Raport zawiera potwierdzenie zespołu wdrażania konfiguracji tożsamości. Plik dziennika może pomóc zespołowi pomocy technicznej lub wdrożenia badać problemy ze sprawdzaniem poprawności.

Domyślnie oba pliki są zapisywane w `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\`.

Użycie:

* **-OutputPath** *ścieżki* parametru na końcu wiersza polecenia do określenia lokalizacji inny raport.
* **-CleanReport** parametru na końcu polecenia uruchomienia, aby wyczyścić *AzsReadinessCheckerReport.json* z poprzednich informacji w raporcie. Aby uzyskać więcej informacji, zobacz [raportu sprawdzania poprawności w usłudze Azure Stack](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Błędy sprawdzania poprawności

Jeśli sprawdzenie poprawności zakończy się niepowodzeniem, w oknie programu PowerShell są wyświetlane szczegóły dotyczące błędu. Narzędzie rejestruje także informacje *AzsReadinessChecker.log*.

Poniższe przykłady zapewnić wskazówki dotyczące typowych błędów sprawdzania poprawności.

### <a name="command-not-found"></a>Nie znaleziono polecenia

`Invoke-AzsADFSValidation : The term 'Invoke-AzsADFSValidation' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.`

**Przyczyna** — nie można prawidłowo załadować moduł sprawdzania gotowości Załaduj programu PowerShell.

**Rozpoznawanie** — moduł sprawdzania gotowości importu jawnie. Skopiuj i wklej poniższy kod do programu PowerShell i zaktualizuj \<wersji\> numerem wersji obecnie zainstalowanej wersji.

`Import-Module "c:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.ReadinessChecker\<version>\Microsoft.AzureStack.ReadinessChecker.psd1" -Force`

## <a name="next-steps"></a>Następne kroki

[Wyświetl raport gotowości](azure-stack-validation-report.md)  
[Zagadnienia dotyczące integracji usługi Azure Stack ogólne](azure-stack-datacenter-integration.md)  
