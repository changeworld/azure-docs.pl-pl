---
title: Weryfikowanie integracji programu Graph usługi Azure dla usługi Azure Stack
description: Narzędzie sprawdzania gotowości stosu Azure umożliwia weryfikowanie integracji programu graph dla usługi Azure Stack.
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
ms.openlocfilehash: e1c1ba0a065a20874bf51d7464cbcfdfa13a571d
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2018
ms.locfileid: "49947262"
---
# <a name="validate-graph-integration-for-azure-stack"></a>Weryfikowanie integracji programu graph dla usługi Azure Stack

Aby sprawdzić, czy środowisko jest gotowe do wykresu integracji z usługą Azure Stack, należy użyć narzędzia narzędzie do sprawdzania gotowości usługi Azure Stack (AzsReadinessChecker). Należy sprawdzić, czy wykres integrację przed rozpoczęciem integracji centrum danych lub przed wdrożeniem usługi Azure Stack.

Narzędzie sprawdzania gotowości weryfikuje:

* Poświadczenia do konta usługi utworzonego integracji narzędzia graph mają odpowiednie uprawnienia do wykonywania zapytań w usłudze Active Directory.
* *Wykazu globalnego* można rozwiązać i jest stycznych.
* Centrum dystrybucji KLUCZY może zostać rozpoznana i jest stycznych.
* Połączenia sieciowe znajduje się w miejscu.

Aby uzyskać więcej informacji na temat integracji centrum danych usługi Azure Stack, zobacz [Integracja z centrum danych usługi Azure Stack — tożsamość](azure-stack-integrate-identity.md)

## <a name="get-the-readiness-checker-tool"></a>Pobierz narzędzie sprawdzania gotowości

Pobierz najnowszą wersję narzędzia narzędzie do sprawdzania gotowości usługi Azure Stack (AzsReadinessChecker) z [galerii programu PowerShell](https://aka.ms/AzsReadinessChecker).

## <a name="prerequisites"></a>Wymagania wstępne

Należy spełnić następujące wymagania wstępne.

**Komputer, na którym działa narzędzie:**

* System Windows 10 lub Windows Server 2016 przy użyciu łączności z domeną.
* Program PowerShell 5.1 lub nowszej. Aby sprawdzić swoją wersję, uruchom następujące polecenie programu PowerShell, a następnie przejrzyj *głównych* wersji i *pomocnicza* wersji:  
   > `$PSVersionTable.PSVersion`
* Moduł usługi Active Directory programu PowerShell
* Najnowszą wersję [Microsoft Azure Stack gotowości Checker](https://aka.ms/AzsReadinessChecker) narzędzia

**Środowiska usługi Active Directory:**

* Określenie nazwy użytkownika i hasło dla konta usługi programu graph w istniejącej usłudze Active Directory
* Identyfikowanie w pełni kwalifikowaną nazwę domeny katalogu głównego lasu usługi Active Directory

## <a name="validate-graph"></a>Sprawdź poprawność wykresu

1. Na komputerze, który spełnia wymagania wstępne Otwórz administracyjny wiersz polecenia PowerShell, a następnie uruchom następujące polecenie, aby zainstalować AzsReadinessChecker.

     `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

1. W wierszu polecenia programu PowerShell, uruchom następujące polecenie, aby ustawić *$graphCredential* zmiennej do konta programu graph. Zastąp `contoso\graphservice` o Twoim koncie za pomocą `domain\username` formatu.

    `$graphCredential = Get-Credential contoso\graphservice -Message "Enter Credentials for the Graph Service Account"`

1. W wierszu polecenia programu PowerShell uruchom następujące polecenie, aby rozpocząć sprawdzanie poprawności dla wykresu. Określ wartość **- ForestFQDN** jako nazwy FQDN dla katalogu głównego lasu:

     `Invoke-AzsGraphValidation -ForestFQDN contoso.com -Credential $graphCredential`

1. Po uruchomieniu narzędzia, przejrzyj dane wyjściowe. Upewnij się, że stan jest odpowiedni dla wymagania dotyczące integracji programu graph. Pomyślnej weryfikacji, zostanie wyświetlony podobny do następującego:

    ```
    Testing Graph Integration (v1.0)
            Test Forest Root:            OK
            Test Graph Credential:       OK
            Test Global Catalog:         OK
            Test KDC:                    OK
            Test LDAP Search:            OK
            Test Network Connectivity:   OK

    Details:

    [-] In standalone mode, some tests should not be considered fully indicative of connectivity or readiness the Azure Stack Stamp requires prior to Data Center Integration.

    Additional help URL: https://aka.ms/AzsGraphIntegration

    AzsReadinessChecker Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log

    AzsReadinessChecker Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json

    Invoke-AzsGraphValidation Completed
    ```

W środowiskach produkcyjnych testowania połączenia sieciowego z poziomu stacji roboczej operatorów nie należy traktować pełni wskazujące na połączenia, które są udostępniane w usłudze Azure Stack. Sygnatury usługi Azure Stack sieci publicznych adresów VIP, należy łączności dla ruchu protokołu LDAP, przeprowadzenie integracji tożsamości.

## <a name="report-and-log-file"></a>Raport i plik dziennika

Uruchamia każdego czasu sprawdzania poprawności, rejestruje ono wyniki do **AzsReadinessChecker.log** i **AzsReadinessCheckerReport.json**. Wyświetla lokalizację tych plików z wynikami weryfikacji w programie PowerShell.

Pliki sprawdzania poprawności mogą pomóc udostępnianie stanu przed rozpoczęciem wdrażania usługi Azure Stack lub badania problemów weryfikacji. Oba pliki są zachowywane wyniki każdego sprawdzenie poprawności kolejne. Raport zawiera potwierdzenie zespołu wdrażania konfiguracji tożsamości. Plik dziennika może pomóc zespołowi pomocy technicznej lub wdrożenia badać problemy ze sprawdzaniem poprawności.

Domyślnie oba pliki są zapisywane w `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\`

Użycie:

* **-OutputPath** *ścieżki* parametru na końcu wiersza polecenia do określenia lokalizacji inny raport.
* **-CleanReport** parametru na końcu polecenia uruchomienia, aby wyczyścić *AzsReadinessCheckerReport.json* z poprzednich informacji w raporcie. Aby uzyskać więcej informacji, zobacz [raportu sprawdzania poprawności w usłudze Azure Stack](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Błędy sprawdzania poprawności

Jeśli sprawdzenie poprawności zakończy się niepowodzeniem, w oknie programu PowerShell są wyświetlane szczegóły dotyczące błędu. Narzędzie rejestruje także informacje *AzsGraphIntegration.log*.

## <a name="next-steps"></a>Następne kroki

[Wyświetl raport gotowości](azure-stack-validation-report.md)  
[Zagadnienia dotyczące integracji usługi Azure Stack ogólne](azure-stack-datacenter-integration.md)  
