---
title: Zweryfikuj tożsamość platformy Azure dla platformy Azure stosu | Dokumentacja firmy Microsoft
description: Aby sprawdzić tożsamość platformy Azure, wykonaj narzędzie sprawdzania gotowości stosu Azure.
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
ms.openlocfilehash: fe5e7281cbe01ad11f667729df344f91ef1327d2
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="validate-azure-identity"></a>Zweryfikuj tożsamość platformy Azure 
Narzędzie sprawdzania gotowości stosu Azure (AzsReadinessChecker), aby sprawdzić, czy usługi Azure Active Directory (Azure AD) jest gotowe do użycia z stosu Azure. Sprawdzanie poprawności rozwiązania tożsamość platformy Azure, przed rozpoczęciem wdrażania stosu Azure.  

Narzędzie sprawdzania gotowości weryfikuje:
 - Azure Active Directory (Azure AD) jako dostawca tożsamości stosu Azure.
 - Konto usługi Azure AD, który planujesz użyć może zalogować się jako administrator globalny usługi Azure Active Directory. 

Sprawdzanie poprawności gwarantuje, że środowisko jest gotowe do stosu Azure do przechowywania informacji o użytkownikach, aplikacje, grupy i nazwy główne usług ze stosu Azure w usługi Azure AD.

## <a name="get-the-readiness-checker-tool"></a>Pobierz narzędzie sprawdzania gotowości
Pobierz najnowszą wersję narzędzia do sprawdzania gotowości stosu Azure (AzsReadinessChecker) z [PSGallery](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Wymagania wstępne
Należy spełnić następujące wymagania wstępne.

**Komputer, na którym działa narzędzie:**
 - Windows 10 lub Windows Server 2016 z połączeniem internetowym.
 - PowerShell 5.1 lub nowszy. Aby sprawdzić swoją wersję, uruchom następujące polecenia programu PowerShell, a następnie przejrzyj *głównych* wersji i *pomocnicza* wersji:  

   > `$PSVersionTable.PSVersion`
 - Skonfiguruj [programu PowerShell dla usługi Azure stosu](azure-stack-powershell-install.md). 
 - Najnowszą wersję [modułu sprawdzania gotowości pakietu Microsoft Azure stosu](https://aka.ms/AzsReadinessChecker) narzędzia.

**Środowisko usługi Azure Active Directory:**
 - Zidentyfikowanie konta usługi Azure AD będą używać stosu Azure i upewnij się, że jest administratorem usługi Azure Active Directory globalnego.
 - Określ nazwę Twojej dzierżawy usługi Azure AD. Nazwa dzierżawy musi być *głównej* nazwy domeny dla usługi Azure Active Directory. Na przykład *contoso.onmicrosoft.com*. 
 - Identyfikowanie AzureEnvironement użyjesz: *AzureCloud*, *AzureGermanCloud*, lub *AzureChinaCloud*.

## <a name="validate-azure-identity"></a>Zweryfikuj tożsamość platformy Azure 
1. Na komputerze, który spełnia wymagania wstępne Otwórz administracyjny wiersz programu PowerShell, a następnie uruchom następujące polecenie, aby zainstalować AzsReadinessChecker:  

   > `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

2. W wierszu polecenia programu PowerShell, uruchom następujące polecenie, aby ustawić *$serviceAdminCredential* jako administratora usługi dla dzierżawy usługi Azure AD.  Zastąp *serviceadmin@contoso.onmicrosoft.com* przy użyciu konta i dzierżawcy. 
   > `$serviceAdminCredential = Get-Credential serviceadmin@contoso.onmicrosoft.com -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant"` 

3. W wierszu polecenia programu PowerShell uruchom następujące polecenie, aby rozpocząć sprawdzanie poprawności usługi Azure AD. 
   - Określ wartość dla AzureEnvironment jako *AzureCloud*, *AzureGermanCloud*, lub *AzureChinaCloud*.  
   - Podaj Azure Active Directory dzierżawy nazwę swojej zastąpić *contoso.onmicrosoft.com*. 

   > `Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment AzureCloud -AADDirectoryTenantName contoso.onmicrosoft.com`
4. Po uruchomieniu narzędzia, przejrzyj dane wyjściowe. Upewnij się, stan jest **OK** dla logowania i wymagania dotyczące instalacji. Pomyślnym zweryfikowaniem wygląda podobnie jak na poniższej ilustracji: 
 
![Uruchom weryfikacji](./media/azure-stack-validate-identity/validation.png)


## <a name="report-and-log-file"></a>Raport i plik dziennika
Uruchamia każdego sprawdzania poprawności czasu, rejestruje ono wyniki w **AzsReadinessChecker.log** i **AzsReadinessCheckerReport.json**. Wyświetla lokalizację tych plików z wynikami weryfikacji w programie PowerShell.

Te pliki mogą pomóc udostępnianie stan weryfikacji przed wdrażanie stosu Azure lub badania problemów sprawdzania poprawności.  Oba pliki utrwalić wyniki każdego wyboru kolejnych weryfikacji. Raport zawiera potwierdzenie zespołu wdrażania konfiguracji tożsamości. Zbadaj problemy z weryfikacją zespołu wdrażania lub pomocy technicznej może pomóc w pliku dziennika. 

Domyślnie oba pliki są zapisywane w *C:\Users\<nazwa użytkownika > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
 - Użyj **- OutputPath** ***&lt;ścieżki&gt;*** parametru na końcu wiersza polecenia do określenia lokalizacji raportów.   
 - Użyj **- CleanReport** parametru po zakończeniu wykonywania polecenia, aby wyczyścić informacje z *AzsReadinessCheckerReport.json*.  o poprzednim uruchamia narzędzia. 

Aby uzyskać więcej informacji [raport weryfikacji stosu Azure](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Wystąpiły błędy sprawdzania poprawności
Jeśli sprawdzanie poprawności nie powiedzie się, w oknie programu PowerShell są wyświetlane szczegóły dotyczące błędu. Narzędzie rejestruje także informacje o AzsReadinessChecker.log.

Poniższe przykłady wytyczne dotyczące typowe błędy sprawdzania poprawności.

### <a name="expired-or-temporary-password"></a>Wygasły lub tymczasowe hasło 
 
![hasło wygasło](./media/azure-stack-validate-identity/expired-password.png)
**Przyczyna** — konta nie można zalogować, ponieważ hasło wygasł lub jest tymczasowe.     

**Rozdzielczość** — w programie PowerShell uruchom następujące polecenie, a następnie postępuj zgodnie z monitami, aby zresetować hasło.  
> `Login-AzureRMAccount`

Alternatywnie, zaloguj się do https://portal.azure.com jako konto i użytkownik musi zmienić hasło.
### <a name="unknown-user-type"></a>Typ nieznanego użytkownika 
 
![Nieznany użytkownik](./media/azure-stack-validate-identity/unknown-user.png)
**Przyczyna** — konta nie można zalogować się do określonej usługi Azure Active Directory (AADDirectoryTenantName). W tym przykładzie *AzureChinaCloud* jest określony jako *AzureEnvironment*.

**Rozdzielczość** — upewnij się, że konto jest nieprawidłowa dla określonego środowiska Azure. W programie PowerShell, uruchom następujące polecenie, aby zweryfikować konto jest prawidłowe dla określonego środowiska: Login-AzureRmAccount — EnvironmentName AzureChinaCloud 
### <a name="account-is-not-an-administrator"></a>Konto nie jest kontem administratora 
 
![administrator nie](./media/azure-stack-validate-identity/not-admin.png)

**Przyczyna** — mimo że to konto można pomyślnie zalogować się, konto nie jest administratorem usługi Azure Active Directory (AADDirectoryTenantName).  

**Rozdzielczość** — logowanie https://portal.azure.com z konta, przejdź do **usługi Azure Active Directory** > **użytkowników** > *wybierz użytkownika*  >  **Roli katalogu**, a następnie upewnij się, użytkownik jest **administratora globalnego**.  Jeśli konto jest użytkownik, przejdź do **usługi Azure Active Directory** > **domeny niestandardowe** nazwy i upewnij się, że nazwa podana dla *AADDirectoryTenantName* jest oznaczone jako podstawowej nazwy domeny dla tego katalogu.  W tym przykładzie, który jest *contoso.onmicrosoft.com*. 

Stos Azure wymaga, że nazwa domeny jest nazwa domeny głównej.

## <a name="next-steps"></a>Następne kroki
[Sprawdź poprawność rejestracja w usłudze Azure](azure-stack-validate-registration.md)  
[Wyświetl raport gotowości](azure-stack-validation-report.md)  
[Zagadnienia dotyczące integracji ogólne Azure stosu](azure-stack-datacenter-integration.md)  

