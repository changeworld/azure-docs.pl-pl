---
title: Weryfikowanie rejestracji platformy Azure dla stosu Azure | Dokumentacja firmy Microsoft
description: Narzędzie sprawdzania gotowości stosu Azure umożliwia sprawdzanie poprawności rejestracja w usłudze Azure.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 84eb1c08cc3f9ef104e2eb0b96ed397315c3f374
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937842"
---
# <a name="validate-azure-registration"></a>Sprawdź poprawność rejestracja w usłudze Azure 
Narzędzie sprawdzania gotowości stosu Azure (AzsReadinessChecker), aby sprawdzić, czy Twoja subskrypcja platformy Azure jest gotowe do użycia z stosu Azure. Sprawdzanie poprawności rejestracji, przed rozpoczęciem wdrażania stosu Azure. Narzędzie sprawdzania gotowości weryfikuje:
- Subskrypcja platformy Azure, którego używasz typ nie jest obsługiwany. Subskrypcji musi być dostawca usług chmury (CSP) lub Enterprise Agreement (EA). 
- Konto, które służy do rejestrowania subskrypcji platformy Azure można zalogować się do platformy Azure i jest właścicielem subskrypcji. 

Aby uzyskać więcej informacji o rejestracji stosu Azure, zobacz [zarejestrować stosu Azure przy użyciu platformy Azure](azure-stack-registration.md). 

## <a name="get-the-readiness-checker-tool"></a>Pobierz narzędzie sprawdzania gotowości
Pobieranie najnowszej wersji narzędzia do sprawdzania gotowości stosu Azure (AzsReadinessChecker) znajduje się na [PSGallery](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Wymagania wstępne
Należy spełnić następujące wymagania wstępne.

**Komputer, na którym działa narzędzie:**
 - Windows 10 lub Windows Server 2016 z połączeniem internetowym.
 - PowerShell 5.1 lub nowszy. Aby sprawdzić swoją wersję, uruchom następujące polecenia programu PowerShell, a następnie przejrzyj *głównych* wersji i *pomocnicza* wersji:  

    >`$PSVersionTable.PSVersion` 
 - Skonfiguruj [programu PowerShell dla usługi Azure stosu](azure-stack-powershell-install.md). 
 - Pobierz najnowszą wersję [modułu sprawdzania gotowości pakietu Microsoft Azure stosu](https://aka.ms/AzsReadinessChecker) narzędzia.  

**Środowisko usługi Azure Active Directory:**
 - Określ nazwę użytkownika i hasło dla konta, które jest właścicielem subskrypcji platformy Azure używanego z stosem platformy Azure.  
 - Określ identyfikator subskrypcji dla subskrypcji platformy Azure, które będą używane. 
 - Identyfikowanie AzureEnvironment użyjesz: *AzureCloud*, *AzureGermanCloud*, lub *AzureChinaCloud*.

## <a name="validate-azure-registration"></a>Sprawdź poprawność rejestracja w usłudze Azure
1. Na komputerze, który spełnia wymagania wstępne Otwórz administracyjny wiersz programu PowerShell, a następnie uruchom następujące polecenie, aby zainstalować AzsReadinessChecker.
    > `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

2. W wierszu polecenia programu PowerShell, uruchom następujące polecenie, aby ustawić *$registrationCredential* jako konta, który jest właścicielem subskrypcji.   Zastąp *subscriptionowner@contoso.onmicrosoft.com* przy użyciu konta i dzierżawcy. 
    > `$registrationCredential = Get-Credential subscriptionowner@contoso.onmicrosoft.com -Message "Enter Credentials for Subscription Owner"`

3. W wierszu polecenia programu PowerShell, uruchom następujące polecenie, aby ustawić *$subscriptionID* jako subskrypcji platformy Azure będzie używany. Zastąp *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx* z identyfikatorem subskrypcji  
     > `$subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"` 

4. W wierszu polecenia programu PowerShell uruchom następujące polecenie, aby rozpocząć sprawdzanie poprawności subskrypcji 
   - Określ wartość dla AzureEnvironment jako *AzureCloud*, *AzureGermanCloud*, lub *AzureChinaCloud*.  
   - Podaj administrator usługi Azure Active Directory i nazwy usługi Azure Active Directory dzierżawy. 

   > `Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -AzureEnvironment AzureCloud -RegistrationSubscriptionID $subscriptionID`

5. Po uruchomieniu narzędzia, przejrzyj dane wyjściowe. Potwierdź, że stan jest OK dla logowania i wymagania dotyczące rejestracji. Pomyślnym zweryfikowaniem wygląda podobnie jak na poniższej ilustracji:  
![Uruchom weryfikacji](./media/azure-stack-validate-registration/registration-validation.png)


## <a name="report-and-log-file"></a>Raport i plik dziennika
Uruchamia każdego sprawdzania poprawności czasu, rejestruje ono wyniki w **AzsReadinessChecker.log** i **AzsReadinessCheckerReport.json**. Wyświetla lokalizację tych plików z wynikami weryfikacji w programie PowerShell. 

Te pliki mogą pomóc udostępnianie stan weryfikacji przed wdrażanie stosu Azure lub badania problemów sprawdzania poprawności. Oba pliki utrwalić wyniki każdego wyboru kolejnych weryfikacji. Raport zawiera potwierdzenie zespołu wdrażania konfiguracji tożsamości. Zbadaj problemy z weryfikacją zespołu wdrażania lub pomocy technicznej może pomóc w pliku dziennika. 

Domyślnie oba pliki są zapisywane w *C:\Users\<nazwa użytkownika > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
 - Użyj **- OutputPath** ***&lt;ścieżki&gt;*** parametru na końcu wiersza polecenia do określenia lokalizacji raportów.   
 - Użyj **- CleanReport** parametru po zakończeniu wykonywania polecenia, aby wyczyścić informacje z *AzsReadinessCheckerReport.json*.  o poprzednim uruchamia narzędzia. Aby uzyskać więcej informacji [raport weryfikacji stosu Azure](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Wystąpiły błędy sprawdzania poprawności
Jeśli sprawdzanie poprawności nie powiedzie się, w oknie programu PowerShell są wyświetlane szczegóły dotyczące błędu. Narzędzie rejestruje także informacje o AzsReadinessChecker.log.

Poniższe przykłady wytyczne dotyczące typowe błędy sprawdzania poprawności.

### <a name="user-must-be-an-owner-of-the-subscription"></a>Użytkownik musi być właścicielem subskrypcji   
![Właściciel subskrypcji](./media/azure-stack-validate-registration/subscription-owner.png)
**Przyczyna** — konto nie jest administratorem subskrypcji platformy Azure.   

**Rozdzielczość** -Użyj konta, które jest administratorem subskrypcji platformy Azure, które będą naliczane do użycia z wdrożenia stosu Azure.


### <a name="expired-or-temporary-password"></a>Wygasły lub tymczasowe hasło 
![hasło wygasło](./media/azure-stack-validate-registration/expired-password.png)
**Przyczyna** — konta nie można zalogować, ponieważ hasło wygasł lub jest tymczasowe.     

**Rozdzielczość** — w programie PowerShell Uruchom i postępuj zgodnie z monitami, aby zresetować hasło. 
  > `Login-AzureRMAccount` 

Alternatywnie, zaloguj się do https://portal.azure.com jako konto i użytkownik musi zmienić hasło.


### <a name="microsoft-accounts-are-not-supported-for-registration"></a>Konta Microsoft nie są obsługiwane dla rejestracji  
![Nieobsługiwany konta](./media/azure-stack-validate-registration/unsupported-account.png)
**Przyczyna** — konta programu Microsoft (takich jak Outlook.com lub Hotmail.com) został określony.  Te konta nie są obsługiwane.

**Rozdzielczość** -Użyj konta i subskrypcji u dostawcy usług chmury (CSP) lub Enterprise Agreement (EA). 


### <a name="unknown-user-type"></a>Typ nieznanego użytkownika  
![Nieznany użytkownika](./media/azure-stack-validate-registration/unknown-user.png)
**Przyczyna** — konta nie można zalogować się do określonego środowiska usługi Azure Active Directory. W tym przykładzie *AzureChinaCloud* jest określony jako *AzureEnvironment*.  

**Rozdzielczość** — upewnij się, że konto jest nieprawidłowa dla określonego środowiska Azure. W programie PowerShell uruchom następujące polecenie, aby sprawdzić, czy konto jest prawidłowe dla określonego środowiska.     
  > `Login-AzureRmAccount -EnvironmentName AzureChinaCloud`


## <a name="next-steps"></a>Następne kroki
[Zweryfikuj tożsamość platformy Azure](azure-stack-validate-identity.md)
[wyświetlić raport gotowości](azure-stack-validation-report.md)
[stosu Azure Ogólne zagadnienia integracji](azure-stack-datacenter-integration.md)

