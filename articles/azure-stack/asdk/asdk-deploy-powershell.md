---
title: Wdrażanie programu PowerShell Azure stosu — | Dokumentacja firmy Microsoft
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
ms.date: 06/07/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: f0d7daa479f6e6ea345e010962488c1ecad5b7e2
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34849961"
---
# <a name="deploy-the-asdk-from-the-command-line"></a>Wdrażanie ASDK z wiersza polecenia
ASDK jest środowiskiem badań i rozwoju, które można wdrożyć do oceny i Wykaż stosu Azure funkcji i usług. Można go pobrać działa prawidłowo, należy przygotować środowisko sprzętu i uruchamiać skrypty, niektóre (to potrwa kilka godzin). Po wykonaniu tej można Zaloguj się do portali administratora i użytkownika na rozpoczęcie korzystania z usługi Azure stosu.

## <a name="prerequisites"></a>Wymagania wstępne 
Przygotowanie komputera-hosta development kit. Planowanie sprzętu, oprogramowania i sieci. Komputer, który obsługuje zestaw deweloperski (Programowanie hosta kit) musi spełniać sprzętu, oprogramowania i wymagania dotyczące sieci. Należy również wybrać opcję przy użyciu usługi Azure Active Directory (Azure AD) lub usługi Active Directory Federation Services (AD FS). Należy przestrzegać wymagań wstępnych przed rozpoczęciem wdrażania, dzięki czemu proces instalacji uruchamia się sprawnie. 

Przed wdrożeniem ASDK, upewnij się, że planowane development kit hosta sprzętu komputera, systemu operacyjnego, konta i konfiguracji sieci spełniają minimalne wymagania dotyczące instalowania ASDK.

**[Przejrzyj zagadnienia związane z planowaniem wdrożenia ASDK](asdk-deploy-considerations.md)**

> [!TIP]
> Można użyć [wymagania dotyczące wdrażania stosu Azure Sprawdź narzędzie](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) po zainstalowaniu systemu operacyjnego, aby upewnić się, że sprzęt spełnia wszystkie wymagania.

## <a name="download-and-extract-the-deployment-package"></a>Pobierać i wyodrębniać pakiet wdrożeniowy
Po upewnieniu się, że komputer-host zestawu programowanie spełnia podstawowe wymagania dotyczące instalowania ASDK, następnym krokiem jest pobierać i wyodrębniać ASDK pakietu wdrożeniowego. Pakiet wdrażania zawiera plik Cloudbuilder.vhdx, który jest wirtualnego dysku twardego zawierającego rozruchowego system operacyjny i pliki instalacyjne stosu Azure.

Możesz pobrać pakiet wdrożeniowy hosta zestawu rozwoju lub do innego komputera. Pliki wyodrębnionego wdrożenia podjąć 60 GB wolnego miejsca na dysku, więc za pomocą innego komputera może pomóc zmniejszyć wymagania sprzętowe dla hosta development kit.

**[Pobierać i wyodrębniać Azure stosu Development Kit (ASDK)](asdk-download.md)**

## <a name="prepare-the-development-kit-host-computer"></a>Przygotowanie komputera-hosta development kit
Przed zainstalowaniem ASDK na komputerze-hoście, należy przygotować środowisko, a system jest skonfigurowany do uruchamiania z dysku VHD. Po wykonaniu tego kroku hosta zestawu programowanie uruchomi się do Cloudbuilder.vhdx (wirtualnego dysku twardego, który zawiera rozruchowego system operacyjny i pliki instalacyjne stosu Azure).

Skonfiguruj komputer-host ASDK pod kątem rozruchu ze CloudBuilder.vhdx za pomocą programu PowerShell. Te polecenia skonfigurować komputer-host ASDK rozruchu z pobrane i wyodrębnione stosu Azure wirtualnego dysku twardego (CloudBuilder.vhdx). Po wykonaniu tych kroków, uruchom ponownie komputer-host ASDK.

Aby skonfigurować komputer-host ASDK rozruchu z CloudBuilder.vhdx:

  1. Uruchom wiersz polecenia jako administrator.
  2. Uruchom polecenie `bcdedit /copy {current} /d "Azure Stack"`
  3. Kopiowania (CTRL + C) wartość identyfikatora CLSID zwrócone, łącznie z wymaganymi {}"s. Ta wartość jest określana jako {CLSID} i należy wkleić (CTRL + V lub kliknij prawym przyciskiem myszy) w pozostałych kroków.
  4. Uruchom polecenie `bcdedit /set {CLSID} device vhd=[C:]\CloudBuilder.vhdx` 
  5. Uruchom `bcdedit /set {CLSID} osdevice vhd=[C:]\CloudBuilder.vhdx` 
  6. Uruchom `bcdedit /set {CLSID} detecthal on` 
  7. Uruchom polecenie `bcdedit /default {CLSID}`
  8. Aby sprawdzić ustawienia rozruchu, uruchom `bcdedit`. 
  9. Upewnij się, że CloudBuilder.vhdx plik został przeniesiony do katalogu głównego dysku C:\ (C:\CloudBuilder.vhdx) i uruchom ponownie na komputerze deweloperskim zestaw hosta. Po ponownym uruchomieniu komputera-hosta ASDK, należy wykonać rozruch z dysku twardego maszyny wirtualnej CloudBuilder.vhdx, aby rozpocząć wdrażanie ASDK. 

> [!IMPORTANT]
> Upewnij się, że przed ponownym uruchomieniem go masz bezpośrednie fizycznej lub KVM dostęp do komputera hosta development kit. Po pierwszym uruchomieniu maszyny Wirtualnej, monit o ukończenie Instalatora systemu Windows Server. Podaj poświadczenia administratora tej samej, użyta do zalogowania się do komputera-hosta development kit. 

### <a name="prepare-the-development-kit-host-using-powershell"></a>Przygotowanie hosta zestawu Programowanie przy użyciu programu PowerShell 
Po zestaw deweloperski, komputer-host pomyślnie uruchomił obraz CloudBuilder.vhdx, zaloguj się przy użyciu tych samych poświadczeń administratora lokalnego użyte do zalogowania się do komputera-hosta development kit (i udostępniony jako część finalizowanie systemu Windows Server Instalator podczas komputer-host rozruchu z wirtualnego dysku twardego). 

> [!NOTE]
> Opcjonalnie można także skonfigurować [ustawieniami telemetrii stosu Azure](asdk-telemetry.md#set-telemetry-level-in-the-windows-registry) *przed* instalowanie ASDK.

Otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień, a następnie uruchom polecenia w tej sekcji, aby wdrożyć ASDK na hoście development kit.

> [!IMPORTANT] 
> Instalacja ASDK obsługuje dokładnie jednej karty interfejsu sieciowego (NIC) do sieci. Jeśli masz wiele kart sieciowych, upewnij się, że tylko jeden z nich jest włączona (i wszystkich innych są wyłączone) przed uruchomieniem skryptu wdrażania.

Można wdrożyć stosu Azure z usługą Azure AD lub Windows Server AD FS jako dostawcy tożsamości. Azure stosu, dostawców zasobów i inne aplikacje działają tak samo zarówno.

> [!TIP]
> Jeśli nie podawaj parametrów instalacji (zobacz InstallAzureStackPOC.ps1 następujące parametry opcjonalne i przykłady poniżej), zostanie wyświetlony monit o wymaganych parametrów.

### <a name="deploy-azure-stack-using-azure-ad"></a>Wdrażanie stos Azure za pomocą usługi Azure AD 
Aby wdrożyć stosu Azure **przy użyciu usługi Azure AD jako dostawcy tożsamości**, musi mieć połączenie z Internetem bezpośrednio lub za pośrednictwem przezroczystego obiektu pośredniczącego. 

Uruchom następujące polecenia programu PowerShell, aby wdrożyć zestaw deweloperski, za pomocą usługi Azure AD:

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator     
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password
  ```

Kilka minut w ASDK instalacji zostanie wyświetlony monit o poświadczenia usługi Azure AD. Musisz podać poświadczenia administratora globalnego dla dzierżawy usługi Azure AD. 

### <a name="deploy-azure-stack-using-ad-fs"></a>Wdrażanie stos Azure za pomocą usług AD FS 
Aby wdrożyć zestaw deweloperski **za pomocą usług AD FS jako dostawca tożsamości**, uruchom następujące polecenia środowiska PowerShell (wystarczy dodać parametr - UseADFS): 

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator 
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -UseADFS
  ```

W przypadku wdrożeń usług AD FS sygnatury domyślna usługa katalogowa jest używany jako dostawca tożsamości. Zaloguj się przy użyciu domyślnego konta jest azurestackadmin@azurestack.local, i hasło zostaną ustawione na podany jako część polecenia Instalatora programu PowerShell.

Proces wdrażania może potrwać kilka godzin, w tym czasie automatycznie ponownego raz. Gdy wdrożenie zakończy się powodzeniem, konsoli programu PowerShell Wyświetla: **COMPLETE: Akcja "Wdrożenie"**. Jeśli wdrożenie nie powiedzie się, można spróbować uruchomić ponownie przy użyciu skryptu parametr ponownego uruchamiania. Można też [ponownie wdrożyć ASDK](asdk-redeploy.md) od początku.

> [!IMPORTANT]
> Jeśli chcesz monitorować postęp wdrażania, po ponownym uruchomieniu hosta ASDK, musisz się zalogować jako AzureStack\AzureStackAdmin. Jeśli zarejestrujesz się jako administrator lokalny po komputer-host ponownego uruchomienia (i przyłączone do domeny azurestack.local), nie będzie wyświetlany jest postęp wdrażania. Ponownie uruchom wdrożenie, nie zamiast tego zaloguj się jako azurestack można sprawdzić, czy jest uruchomiona.


#### <a name="azure-ad-deployment-script-examples"></a>Przykłady skryptów wdrażania w usłudze Azure AD
Można utworzyć skrypty całego wdrożenia usługi Azure AD. Oto kilka przykładów komentarze, obejmujące niektóre parametry opcjonalne.

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

Jeśli w środowisku nie ma DHCP włączony, musi zawierać następujące dodatkowe parametry do jednej z opcji powyżej (przykład użycia podano): 

```powershell
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -NatIPv4Subnet 10.10.10.0/24 -NatIPv4Address 10.10.10.3 -NatIPv4DefaultGateway 10.10.10.1 -TimeServer 10.222.112.26
```

### <a name="asdk-installazurestackpocps1-optional-parameters"></a>Parametry opcjonalne ASDK InstallAzureStackPOC.ps1
|Parametr|Wymagane opcjonalne|Opis|
|-----|-----|-----|
|AdminPassword|Wymagane|Ustawia konta administratora lokalnego i innymi kontami użytkowników na wszystkie maszyny wirtualne utworzone jako część development kit wdrożenia. To hasło musi być zgodna bieżące hasło administratora lokalnego na hoście.|
|InfraAzureDirectoryTenantName|Wymagane|Ustawia katalog dzierżawy. Ten parametr umożliwia określenie określonego katalogu gdzie konta usługi AAD ma uprawnienia do zarządzania wielu katalogów. Pełna nazwa dzierżawy katalogu usługi AAD w formacie. onmicrosoft.com lub usługi Azure AD zweryfikować niestandardowej nazwy domeny.|
|TimeServer|Wymagane|Ten parametr umożliwia określenie serwera określony czas. Ten parametr musi być dostarczona jako adres IP serwera czas ważności. Nazwy serwerów nie są obsługiwane.|
|InfraAzureDirectoryTenantAdminCredential|Optional (Opcjonalność)|Ustawia nazwę użytkownika usługi Azure Active Directory i hasło. Te poświadczenia platformy Azure musi być identyfikatorem organizacji.|
|InfraAzureEnvironment|Optional (Opcjonalność)|Wybierz środowisko Azure, z którym chcesz zarejestrować tego wdrożenia stosu Azure. Obejmują one publicznej Azure, Azure - Chinach, Azure - instytucji rządowych Stanów Zjednoczonych.|
|DNSForwarder|Optional (Opcjonalność)|Serwer DNS jest tworzony jako część wdrożenia stosu Azure. Aby umożliwić komputerom wewnątrz rozwiązania do rozpoznawania nazw poza sygnatury, podaj istniejącej infrastruktury serwera DNS. Serwer DNS w sygnatury przekazuje żądania rozpoznania nieznanej nazwy do tego serwera.|
|NatIPv4Address|Wymagane do obsługi protokołu DHCP translatora adresów Sieciowych|Ustawia MAS BGPNAT01 statycznego adresu IP. Tego parametru należy używać tylko wówczas, gdy usługa DHCP nie może przypisać prawidłowego adresu IP w celu uzyskania dostępu do Internetu.|
|NatIPv4Subnet|Wymagane do obsługi protokołu DHCP translatora adresów Sieciowych|Prefiks podsieci IP używanego w DHCP za pośrednictwem obsługi translatora adresów Sieciowych. Tego parametru należy używać tylko wówczas, gdy usługa DHCP nie może przypisać prawidłowego adresu IP w celu uzyskania dostępu do Internetu.|
|PublicVlanId|Optional (Opcjonalność)|Ustawia identyfikator sieci VLAN. Tego parametru należy użyć tylko, jeśli host i MAS BGPNAT01 należy skonfigurować identyfikator sieci VLAN umożliwiający dostęp do sieci fizycznej (i Internetu). Na przykład.\InstallAzureStackPOC.ps1-Verbose - PublicVLan 305|
|Uruchom ponownie|Optional (Opcjonalność)|Użyj tej flagi, aby ponownie uruchomić wdrażania. Wszystkie poprzednie dane wejściowe są używane. Ponownego wprowadzania danych podać wcześniej nie jest obsługiwana, ponieważ wiele unikatowych wartości były generowane i użyć do wdrożenia.|


## <a name="perform-post-deployment-configurations"></a>Przeprowadź konfiguracje po wdrożeniu
Po zainstalowaniu ASDK, istnieje kilka zalecanych kontroli po instalacji i zmian konfiguracji, które należy utworzyć. Można sprawdzić poprawność instalacji został zainstalowany pomyślnie za pomocą polecenia cmdlet test-AzureStack i zainstalować narzędzia Azure PowerShell stosu i GitHub. 

Należy również zresetować zasady wygasania haseł, aby upewnić się, że hasło dla hosta development kit nie wygasa przed zakończeniem okresu oceny.

> [!NOTE]
> Opcjonalnie można także skonfigurować [ustawieniami telemetrii stosu Azure](asdk-telemetry.md#enable-or-disable-telemetry-after-deployment) *po* instalowanie ASDK.

**[Zadania wdrażania ASDK po](asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Zarejestrować w usłudze Azure
Należy zarejestrować stosu Azure przy użyciu platformy Azure, co pozwala [pobieranie elementów witrynę Azure marketplace](asdk-marketplace-item.md) stos Azure.

**[Zarejestruj stosu Azure przy użyciu platformy Azure](asdk-register.md)**

## <a name="next-steps"></a>Kolejne kroki
Gratulacje! Po wykonaniu tych czynności, będziesz mieć Środowisko deweloperskie zestawu zarówno [administratora](https://adminportal.local.azurestack.external) i [użytkownika](https://portal.local.azurestack.external) portali. 

[Po zakończeniu instalacji ASDK zadania konfiguracji](asdk-post-deploy.md)

