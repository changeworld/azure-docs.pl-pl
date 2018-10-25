---
title: Nawiązywanie połączenia usługi Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak połączyć się z usługi Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/28/2018
ms.author: mabrigg
ms.openlocfilehash: 1cdf013325afe4b217f5f56043e06f60a4933419
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2018
ms.locfileid: "49993176"
---
# <a name="connect-to-azure-stack-development-kit"></a>Łączenie usługi Azure Stack Development Kit

*Dotyczy: Azure Stack Development Kit*

Zarządzanie zasobami, możesz nawiązać Azure Stack Development Kit. W tym artykule opisano kroki, które należy wykonać w celu łączenia development Kit. Można użyć jednej z następujących opcji połączenia:

* [Podłączanie pulpitu zdalnego](#connect-with-remote-desktop). Po nawiązaniu połączenia za pomocą połączenia pulpitu zdalnego, pojedynczego użytkownika może szybko nawiązać połączenie deweloperski.
* [Wirtualnej sieci prywatnej (VPN)](#connect-with-vpn). Po nawiązaniu połączenia za pośrednictwem sieci VPN, wielu użytkowników jednocześnie połączyć z klientom spoza infrastruktury Azure Stack. Połączenie sieci VPN wymaga konfiguracji.

<a name="connect-to-azure-stack-with-remote-desktop"></a>
##  <a name="connect-to-azure-stack-by-using-remote-desktop-connection"></a>Łączenie z usługi Azure Stack za pomocą połączenia pulpitu zdalnego

Jeden użytkownik współbieżnych można zarządzać zasobami w portalu operatora lub portalu użytkowników za pomocą połączenia pulpitu zdalnego.

1. Otworzyć usługę Podłączanie pulpitu zdalnego i połącz się zestaw development kit. Wprowadź nazwę użytkownika **AzureStack\AzureStackAdmin**. Użyj hasła operator, który określiłeś, podczas konfigurowania usługi Azure Stack.  

2. Na komputerze deweloperskim zestaw Otwórz Menedżera serwera. Wybierz **lokalnego serwera**, wyczyść **zwiększonych zabezpieczeń programu Internet Explorer** pole wyboru, a następnie Zamknij Menedżera serwera.

3. Aby otworzyć [portal użytkowników](azure-stack-key-features.md#portal), przejdź do https://portal.local.azurestack.external/. Zaloguj się przy użyciu poświadczeń użytkownika. Aby otworzyć usługi Azure Stack [operator portal](azure-stack-key-features.md#portal), przejdź do https://adminportal.local.azurestack.external/. Zaloguj się przy użyciu poświadczeń usługi Azure Active Directory (Azure AD), które zostały określone podczas instalacji.

<a name="connect-to-azure-stack-with-vpn"></a>
## <a name="connect-to-azure-stack-by-using-vpn"></a>Nawiązywanie połączenia usługi Azure Stack za pośrednictwem sieci VPN

Aby ustanowić podzielony tunel połączenia sieci VPN do usługi Azure Stack Development Kit. Połączenie sieci VPN umożliwia dostęp do portalu usługi Azure Stack operatora, portal użytkowników i zainstalowanych lokalnie narzędzi, takich jak Visual Studio i PowerShell do zarządzania zasobami usługi Azure Stack. Połączenie z siecią VPN jest obsługiwane w usłudze Azure AD i wdrożeniach usługi Active Directory Federation Services (AD FS). Połączenia sieci VPN umożliwiają wielu klientów do łączenia z usługą Azure Stack w tym samym czasie.

> [!NOTE]
> Połączenie sieci VPN nie zapewnia łączności na maszynach wirtualnych infrastruktury Azure Stack.

### <a name="prerequisites"></a>Wymagania wstępne

1. Zainstaluj [programu Azure PowerShell dla usługi Azure Stack zgodnego](azure-stack-powershell-install.md) na komputerze lokalnym.  
2. Pobierz [narzędzia wymagane do pracy z usługą Azure Stack](azure-stack-powershell-download.md).

### <a name="set-up-vpn-connectivity"></a>Konfigurowanie połączeń sieci VPN

Aby utworzyć połączenie sieci VPN w taki sposób, aby zestaw development kit, Otwórz program Windows PowerShell jako administrator na lokalnym komputerze z systemem Windows. Następnie uruchom następujący skrypt (Aktualizacja środowiska wartości adresu IP i hasła):

```PowerShell
# Configure Windows Remote Management (WinRM), if it's not already configured.
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module.
Import-Module .\Connect\AzureStack.Connect.psm1

# Add the development kit computer’s host IP address and certificate authority (CA) to the list of trusted hosts. Make sure you update the IP address and password values for your environment.

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

![Połączenia sieciowe](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack"></a>Nawiązywanie połączenia z usługą Azure Stack

Połącz się z wystąpieniem usługi Azure Stack przy użyciu jednej z następujących metod:  

* Użyj `Connect-AzsVpn ` polecenia:
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  Po wyświetleniu monitu zaufania hosta usługi Azure Stack i zainstalować certyfikat z **AzureStackCertificateAuthority** w magazynie certyfikatów komputera lokalnego. (Monit, mogą być ukryte przez okno programu PowerShell).

* Na komputerze lokalnym, wybierz **ustawienia sieciowe** > **VPN** > **azurestack** > **połączyć**. W wierszu logowania wprowadź nazwę użytkownika (**AzureStack\AzureStackAdmin**) i hasło.

### <a name="test-vpn-connectivity"></a>Testowanie łączności sieci VPN

Aby przetestować połączenie z portalu, otwórz przeglądarkę sieci web, a następnie przejdź do portalu użytkowników (https://portal.local.azurestack.external/) lub portalu — operator (https://adminportal.local.azurestack.external/). Zaloguj się i tworzenia zasobów.  

## <a name="next-steps"></a>Kolejne kroki

[Udostępnij maszyny wirtualne dla użytkowników usługi Azure Stack](azure-stack-tutorial-tenant-vm.md)
