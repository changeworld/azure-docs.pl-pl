---
title: Nawiązywanie połączenia usługi Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak połączyć się z ASDK.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2018
ms.author: jeffgilb
ms.openlocfilehash: 55be312046f5cdea2c1481ed435b5859ab2c2540
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50026907"
---
# <a name="connect-to-the-azure-stack-development-kit"></a>Łączenie usługi Azure Stack Development Kit

Aby zarządzać zasobami, musisz najpierw połączyć do usługi Azure Stack Development Kit (ASDK). W tym artykule opisano kroki, które należy wykonać, aby nawiązać połączenie z ASDK. Można użyć jednej z następujących opcji połączenia:

* [Podłączanie pulpitu zdalnego](#connect-with-remote-desktop). Po nawiązaniu połączenia za pomocą połączenia pulpitu zdalnego, pojedynczego użytkownika może szybko nawiązać połączenie deweloperski.
* [Wirtualnej sieci prywatnej (VPN)](#connect-with-vpn). Po nawiązaniu połączenia za pośrednictwem sieci VPN, wielu użytkowników jednocześnie połączyć z klientom spoza infrastruktury Azure Stack. Połączenie sieci VPN wymaga konfiguracji.

<a name="connect-to-azure-stack-with-remote-desktop"></a>
##  <a name="connect-to-azure-stack-by-using-remote-desktop-connection"></a>Łączenie z usługi Azure Stack za pomocą połączenia pulpitu zdalnego

Jeden użytkownik współbieżnych można zarządzać zasobami w portalu operatora lub portalu użytkowników za pomocą połączenia pulpitu zdalnego.

1. Otworzyć usługę Podłączanie pulpitu zdalnego (mstc.exe) i Połącz do komputera hosta development kit jako **AzureStack\AzureStackAdmin** przy użyciu hasło określone podczas konfigurowania ASDK.  

2. Na komputerze deweloperskim zestaw hosta otwórz Menedżera serwera (ServerManager.exe). Wybierz **lokalnego serwera**, wyłącz **Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer**i Zamknij Menedżera serwera.

3. Zaloguj się do portalu administratora jako **AzureStack\CloudAdmin** lub użyj innych poświadczeń usługi Azure Stack operatora. Adres portalu administrowania ASDK [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).

4. Zaloguj się do portalu użytkownika jako **AzureStack\CloudAdmin** lub użyj innych poświadczeń użytkownika usługi Azure Stack. Adres portalu użytkownika ASDK [ https://portal.local.azurestack.external ](https://portal.local.azurestack.external).

> [!NOTE]
> Aby uzyskać więcej informacji o tym, kiedy należy używać konta, którego można zobaczyć [podstawy administracji ASDK](asdk-admin-basics.md#what-account-should-i-use).

<a name="connect-to-azure-stack-with-vpn"></a>
## <a name="connect-to-azure-stack-by-using-vpn"></a>Nawiązywanie połączenia usługi Azure Stack za pośrednictwem sieci VPN

Aby ustanowić podzielony tunel połączenia sieci VPN z ASDK dostęp do usługi Azure Stack portali i zainstalowanych lokalnie narzędzi, takich jak Visual Studio i PowerShell. Korzystając z połączenia sieci VPN, połączyć wielu użytkowników jednocześnie do zasobów usługi Azure Stack pracujących ASDK.

Połączenie z siecią VPN jest obsługiwane dla usługi Azure AD i Active Directory Federation Services (AD FS) wdrożenia.

> [!NOTE]
> Połączenie sieci VPN nie zapewnia łączności na maszynach wirtualnych infrastruktury Azure Stack.

### <a name="prerequisites"></a>Wymagania wstępne
Przed skonfigurowaniem połączenia sieci VPN w taki sposób, aby ASDK, upewnij się, że zostały spełnione następujące wymagania wstępne.

- Zainstaluj [programu Azure PowerShell dla usługi Azure Stack zgodnego](asdk-post-deploy.md#install-azure-stack-powershell) na komputerze lokalnym.  
- Pobierz [narzędzia wymagane do pracy z usługą Azure Stack](asdk-post-deploy.md#download-the-azure-stack-tools).

### <a name="set-up-vpn-connectivity"></a>Konfigurowanie połączeń sieci VPN

Aby utworzyć połączenie sieci VPN w taki sposób, aby ASDK, Otwórz program PowerShell jako administrator na lokalnym komputerze z systemem Windows. Następnie uruchom następujący skrypt (Aktualizacja środowiska wartości adresu IP i hasła):

```PowerShell
# Change directories to the default Azure Stack tools directory
cd C:\AzureStack-Tools-master

# Configure Windows Remote Management (WinRM), if it's not already configured.
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module.
Import-Module .\Connect\AzureStack.Connect.psm1

# Add the development kit host computer’s IP address as the ASDK certificate authority (CA) to the list of trusted hosts. Make sure you update the IP address and password values for your environment.

$hostIP = "<Azure Stack host IP address>"

$Password = ConvertTo-SecureString `
  "<operator's password provided when deploying Azure Stack>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user.
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

Jeśli Instalator zakończy się powodzeniem, **azurestack** pojawia się na liście połączeń sieci VPN.

![Połączenia sieciowe](media/asdk-connect/image3.png)  

### <a name="connect-to-azure-stack"></a>Nawiązywanie połączenia z usługą Azure Stack

Połącz się z wystąpieniem usługi Azure Stack przy użyciu jednej z następujących metod:  

* Użyj `Connect-AzsVpn ` polecenia:
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  Po wyświetleniu monitu zaufania hosta usługi Azure Stack i zainstalować certyfikat z **AzureStackCertificateAuthority** w magazynie certyfikatów komputera lokalnego. 
  
  > [!IMPORTANT]
  > Monit może być ono ukryte przez okno programu PowerShell.

* Na komputerze lokalnym, wybierz **ustawienia sieciowe** > **VPN** > **azurestack** > **połączyć**. W wierszu logowania wprowadź nazwę użytkownika (**AzureStack\AzureStackAdmin**) i hasło.

### <a name="test-vpn-connectivity"></a>Testowanie łączności sieci VPN

Aby przetestować połączenie z portalu, otwórz przeglądarkę sieci web, a następnie przejdź do portalu użytkowników (https://portal.local.azurestack.external/) lub portalu administracyjnego (https://adminportal.local.azurestack.external/). Zaloguj się i tworzenia zasobów.  

## <a name="next-steps"></a>Kolejne kroki

[Rozwiązywanie problemów](asdk-troubleshooting.md)
