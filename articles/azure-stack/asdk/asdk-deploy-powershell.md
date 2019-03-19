---
title: Wdrażanie usługi Azure Stack — PowerShell | Dokumentacja firmy Microsoft
description: W tym artykule należy zainstalować ASDK z wiersza polecenia przy użyciu programu PowerShell.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: ''
ms.date: 02/08/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: e8d3653049b0160fac155ebc49329c17cb27224f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2019
ms.locfileid: "58014393"
---
# <a name="deploy-the-asdk-from-the-command-line"></a>Wdrażanie ASDK z wiersza polecenia
ASDK jest środowiskiem środowisk testowych i programistycznych, które można wdrożyć do oceny i zademonstrowania funkcji usługi Azure Stack i usług. Jego skonfigurowaniu i uruchomieniu należy przygotować środowisko sprzętu i uruchomić niektóre skrypty (będzie to potrwać kilka godzin). Po tym można Zaloguj się do portali administratora i użytkownika Aby rozpocząć korzystanie z usługi Azure Stack.

## <a name="prerequisites"></a>Wymagania wstępne 
Przygotowanie komputera hosta development kit. Zaplanuj sprzętu, oprogramowania i sieci. Komputerze, który hostuje zestaw development kit (host rozwoju kit) musi spełniać wymagania dotyczące sieci, oprogramowania i sprzętu. Należy także wybrać między za pomocą usługi Azure Active Directory (Azure AD) lub usługi Active Directory Federation Services (AD FS). Pamiętaj przestrzegania tych wymagań wstępnych przed rozpoczęciem wdrażania, aby proces instalacji uruchamia się normalnie. 

Przed wdrożeniem ASDK, upewnij się, że planowane development kit hosta sprzętu komputera, systemu operacyjnego, konta i konfiguracji sieci spełniają minimalne wymagania dotyczące instalowania ASDK.

**[Przejrzyj zagadnienia dotyczące planowania wdrożenia ASDK](asdk-deploy-considerations.md)**

> [!TIP]
> Możesz użyć [wymagania dotyczące wdrażania usługi Azure Stack Sprawdź narzędzie](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) po zainstalowaniu systemu operacyjnego, aby upewnić się, że sprzęt spełnia wszystkie wymagania.

## <a name="download-and-extract-the-deployment-package"></a>Pobierz i Wyodrębnij pakiet wdrożeniowy
Po upewnieniu się, że komputer hosta zestaw deweloperski spełnia wymagania podstawowe dotyczące instalowania ASDK, następnym krokiem jest pobieranie i wyodrębnianie ASDK pakietu wdrożeniowego. Pakiet wdrażania zawiera plik Cloudbuilder.vhdx, który jest wirtualnego dysku twardego, obejmują rozruchowego system operacyjny i pliki instalacyjne usługi Azure Stack.

Możesz pobrać pakiet wdrożeniowy na hoście kit rozwoju lub na innym komputerze. Pliki wdrożenia wyodrębnione potrwać 60 GB wolnego miejsca na dysku, więc za pomocą innego komputera może pomóc zmniejszyć wymagania dotyczące sprzętu dla hosta kit rozwoju.

**[Pobierz i Wyodrębnij usługi Azure Stack Development Kit (ASDK)](asdk-download.md)**

## <a name="prepare-the-development-kit-host-computer"></a>Przygotowanie komputera hosta development kit
Przed zainstalowaniem ASDK na komputerze-hoście, należy przygotować środowisko, a system jest skonfigurowany do rozruchu z wirtualnego dysku twardego. Po wykonaniu tego kroku hosta kit rozwoju uruchomi się do Cloudbuilder.vhdx (wirtualny dysk twardy zawierający rozruchowego system operacyjny i pliki instalacyjne usługi Azure Stack).

Konfigurowanie komputera hosta ASDK, aby rozruch następował z CloudBuilder.vhdx przy użyciu programu PowerShell. Te polecenia, skonfiguruj komputer-host ASDK aby rozruch następował z pobrane i wyodrębnione usługi Azure Stack wirtualnego dysku twardego (CloudBuilder.vhdx). Po wykonaniu tych kroków, uruchom ponownie komputer-host ASDK.

Aby skonfigurować komputer-host ASDK aby rozruch następował z CloudBuilder.vhdx:

  1. Uruchom wiersz polecenia jako administrator.
  2. Uruchom polecenie `bcdedit /copy {current} /d "Azure Stack"`
  3. Kopiuj (CTRL + C) wartość identyfikatora CLSID zwrócone, wymagane w tym {}"s. Ta wartość jest nazywana {CLSID} i należy do wklejenia (CTRL + V lub kliknij prawym przyciskiem myszy) w kolejnych krokach.
  4. Uruchom polecenie `bcdedit /set {CLSID} device vhd=[C:]\CloudBuilder.vhdx` 
  5. Uruchom `bcdedit /set {CLSID} osdevice vhd=[C:]\CloudBuilder.vhdx` 
  6. Uruchom `bcdedit /set {CLSID} detecthal on` 
  7. Uruchom polecenie `bcdedit /default {CLSID}`
  8. Aby sprawdzić ustawienia rozruchu, uruchom `bcdedit`. 
  9. Upewnij się, że CloudBuilder.vhdx plik został przeniesiony do katalogu głównego dysku C:\ (C:\CloudBuilder.vhdx) i ponowne uruchomienie komputera hosta development kit. Po ponownym uruchomieniu komputer-host ASDK, należy wykonać rozruch z dysku twardego maszyny wirtualnej CloudBuilder.vhdx, aby rozpocząć wdrażanie ASDK. 

> [!IMPORTANT]
> Upewnij się, że przed ponownym uruchomieniem go masz bezpośredni fizycznej lub KVM dostęp do komputera hosta development kit. Po pierwszym uruchomieniu maszyny Wirtualnej monit o ukończenie Instalatora systemu Windows Server. Podaj poświadczenia administratora tej samej, którego użyto do zalogowania się do komputera hosta development kit. 

### <a name="prepare-the-development-kit-host-using-powershell"></a>Przygotowanie hosta kit programowania przy użyciu programu PowerShell 
Po deweloperski host pomyślnie rozruchu komputera w obrazie CloudBuilder.vhdx, zaloguj się przy użyciu tych samych poświadczeń administratora lokalnego, które są używane do logowania się do komputera hosta development kit (i udostępniane jako część finalizowanie systemu Windows Server Instalacja przy komputerze-hoście rozruchu z wirtualnego dysku twardego). 

> [!NOTE]
> Opcjonalnie można także skonfigurować [ustawieniami telemetrii usługi Azure Stack](asdk-telemetry.md#set-telemetry-level-in-the-windows-registry) *przed* instalowanie ASDK.

Otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień i Uruchom polecenia w tej sekcji, aby wdrożyć ASDK na hoście development kit.

> [!IMPORTANT] 
> Instalacja ASDK obsługuje dokładnie jeden karta interfejsu sieciowego (NIC) dla sieci. Jeśli masz wiele kart sieciowych, upewnij się, że włączone jest tylko jeden (i wszystkie inne są wyłączone) przed uruchomieniem skryptu wdrażania.

Można wdrożyć usługę Azure Stack z usługą Azure AD lub Windows Server AD FS jako dostawcy tożsamości. Usługa Azure Stack, dostawcy zasobów i inne aplikacje działają tak samo, zarówno.

> [!TIP]
> Jeśli nie podasz żadnych parametrów instalacji (patrz InstallAzureStackPOC.ps1 opcjonalnych parametrów i przykłady poniżej), zostanie wyświetlony monit o wymaganych parametrów.

### <a name="deploy-azure-stack-using-azure-ad"></a>Wdrażanie usługi Azure Stack przy użyciu usługi Azure AD 
Aby wdrożyć usługę Azure Stack **przy użyciu usługi Azure AD jako dostawcy tożsamości**, musi mieć połączenie z Internetem, bezpośrednio lub za pośrednictwem przezroczystym serwerem proxy. 

Uruchom następujące polecenia programu PowerShell, aby wdrożyć deweloperski przy użyciu usługi Azure AD:

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator     
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password
  ```

Kilka minut, zanim do instalacji ASDK, zostanie wyświetlony monit o poświadczenia usługi Azure AD. Musisz podać poświadczenia administratora globalnego dla dzierżawy usługi Azure AD. 

Po wdrożeniu uprawnienia administratora globalnego usługi Azure Active Directory nie jest wymagana. Jednak niektóre operacje mogą wymagać poświadczenia administratora globalnego. Na przykład skryptu Instalatora dostawcy zasobów lub nową funkcję wymagające uprawnienia przyznawane. Możesz tymczasowo ponownie przywrócić uprawnienia administratora globalnego dla konta lub użyj konta oddzielne administratora globalnego, który jest właścicielem *domyślne subskrypcję dostawcy*.

### <a name="deploy-azure-stack-using-ad-fs"></a>Wdrażanie usługi Azure Stack przy użyciu usług AD FS 
Aby wdrożyć zestaw development kit **jako dostawcy tożsamości za pomocą usług AD FS**, uruchom następujące polecenia programu PowerShell (wystarczy dodać parametr - UseADFS): 

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator 
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -UseADFS
  ```

W przypadku wdrożeń usług AD FS sygnatury domyślnej usługi katalogowej jest używany jako dostawcy tożsamości. Domyślne konto do zalogowania to azurestackadmin@azurestack.local, a hasło zostanie ustawione na podana jako część polecenia Instalatora programu PowerShell.

Proces wdrażania może potrwać kilka godzin, w tym czasie system automatycznie ponownie uruchamia jeden raz. Po pomyślnym wdrożeniu Wyświetla konsoli programu PowerShell: **NALEŻY WYKONAĆ: Akcja "Wdrożenie"**. Jeśli wdrożenie zakończy się niepowodzeniem, można spróbować uruchomić skrypt ponownie, używając - Uruchom ponownie parametru. Można też [ponownie wdrożyć ASDK](asdk-redeploy.md) od podstaw.

> [!IMPORTANT]
> Jeśli chcesz monitorować postęp wdrażania, po ponownym uruchomieniu hosta ASDK, musisz zarejestrować się jako AzureStack\AzureStackAdmin. Jeśli rejestrujesz się jako administrator lokalny po komputer-host jest ponownie uruchomiona (i przyłączone do domeny azurestack.local), nie będzie wyświetlany postęp wdrożenia. Ponownie uruchom wdrożenie, nie zamiast tego zaloguj się jako azurestack można sprawdzić, czy jest uruchomiona.


#### <a name="azure-ad-deployment-script-examples"></a>Przykłady skryptów wdrażania w usłudze Azure AD
Można tworzyć skrypty dla całego wdrożenia usługi Azure AD. Poniżej przedstawiono kilka przykładów komentarze, które obejmują niektóre parametry opcjonalne.

Jeśli tożsamości usługi Azure AD tylko jest skojarzony z **jeden** katalog usługi Azure AD:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -TimeServer 52.168.138.145 #Example time server IP address.
```

Jeśli tożsamości usługi Azure AD jest skojarzony z **więcej niż jeden** katalog usługi Azure AD:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" #Example: user@AADDirName.onmicrosoft.com 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -InfraAzureDirectoryTenantName "<Azure AD directory in the form of domainname.onmicrosoft.com or an Azure AD verified custom domain name>" -TimeServer 52.168.138.145 #Example time server IP address.
```

Jeśli Twoje środowisko nie ma włączony protokół DHCP, należy uwzględnić następujące dodatkowe parametry do jednej z opcji powyżej (przykład użycia podano): 

```powershell
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -TimeServer 10.222.112.26
```

### <a name="asdk-installazurestackpocps1-optional-parameters"></a>Parametry opcjonalne ASDK InstallAzureStackPOC.ps1

|Parametr|Wymagane/opcjonalne|Opis|
|-----|-----|-----|
|AdminPassword|Wymagane|Ustawia konta administratora lokalnego i innych kont użytkowników na wszystkich maszynach wirtualnych utworzonych jako część wdrożenia zestawu rozwoju. To hasło musi być zgodna bieżące hasło administratora lokalnego na hoście.|
|InfraAzureDirectoryTenantName|Wymagane|Określa katalog dzierżawy. Ten parametr umożliwia określenie katalogu, gdzie konto usługi AAD ma uprawnienia do zarządzania wieloma katalogami. Pełna nazwa dzierżawy katalogu usługi AAD w formacie. onmicrosoft.com lub usługi Azure AD należy zweryfikować niestandardowej nazwy domeny.|
|TimeServer|Wymagane|Użyj tego parametru, aby określić serwer określony czas. Ten parametr musi zostać podana jako adres IP serwera prawidłową godzinę. Nazwy serwerów nie są obsługiwane.|
|InfraAzureDirectoryTenantAdminCredential|Optional (Opcjonalność)|Ustawia nazwę użytkownika usługi Azure Active Directory i hasło. Te poświadczenia platformy Azure musi być identyfikatorem organizacji.|
|InfraAzureEnvironment|Optional (Opcjonalność)|Wybierz środowisko platformy Azure, z którym chcesz zarejestrować tego wdrożenia usługi Azure Stack. Opcje obejmują publicznej platformy Azure, Azure — Chiny, Azure — instytucje rządowe USA.|
|DNSForwarder|Optional (Opcjonalność)|Serwer DNS jest tworzona jako część wdrożenia usługi Azure Stack. Aby zezwolić komputerom wewnątrz rozwiązania do rozpoznawania nazw poza sygnatury, podaj istniejącej infrastruktury serwera DNS. Serwer DNS w sygnatury przekazuje żądania rozpoznania nieznanej nazwy do tego serwera.|
|Uruchom ponownie|Optional (Opcjonalność)|Aby ponownie uruchomić wdrożenie, należy użyć tej flagi. Wszystkie poprzednie dane wejściowe są używane. Ponownego wprowadzania danych, które poprzednio oferowana nie jest obsługiwana, ponieważ wiele unikatowych wartości są generowane i używane w ramach wdrożenia.|


## <a name="perform-post-deployment-configurations"></a>Przeprowadź konfiguracje po wdrożeniu
Po zainstalowaniu ASDK, istnieje kilka zalecanych kontroli po instalacji i zmian konfiguracji, które powinny być. Można sprawdzić poprawność instalacji został zainstalowany pomyślnie przy użyciu polecenia cmdlet test-AzureStack i instalowanie narzędzi usługi Azure Stack PowerShell i serwisu GitHub. 

Należy także zresetować zasady wygasania haseł, aby upewnić się, że hasło dla hosta kit rozwoju nie wygasa przed zakończeniem okresu Twojej oceny.

> [!NOTE]
> Opcjonalnie można także skonfigurować [ustawieniami telemetrii usługi Azure Stack](asdk-telemetry.md#enable-or-disable-telemetry-after-deployment) *po* instalowanie ASDK.

**[Opublikuj ASDK zadania związane z wdrażaniem](asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Rejestrowania na platformie Azure
Należy zarejestrować usługi Azure Stack przy użyciu platformy Azure, aby można było [pobieranie elementów portalu Azure marketplace](asdk-marketplace-item.md) do usługi Azure Stack.

**[Rejestrowania usługi Azure Stack na platformie Azure](asdk-register.md)**

## <a name="next-steps"></a>Kolejne kroki
Gratulacje! Po wykonaniu tych czynności będziesz mieć środowisku deweloperskim zestaw zarówno [administratora](https://adminportal.local.azurestack.external) i [użytkownika](https://portal.local.azurestack.external) portali. 

[Instalacja ASDK zadania po konfiguracji](asdk-post-deploy.md)

