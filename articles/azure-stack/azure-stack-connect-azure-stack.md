---
title: Połącz Azure stosu | Dokumentacja firmy Microsoft
description: Dowiedz się, jak nawiązać stosu Azure.
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
ms.date: 06/06/2018
ms.author: mabrigg
ms.openlocfilehash: c1932f2ed0486fb56e467466c0fed53702e8f9b0
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248690"
---
# <a name="connect-to-azure-stack-development-kit"></a>Połącz Azure stosu Development Kit

*Dotyczy: Azure stosu Development Kit*

Zarządzanie zasobami, możesz nawiązać Azure stosu Development Kit. W tym artykule opisano kroki, które należy wykonać w celu nawiązania połączenia z zestawem deweloperskim. Można użyć jednej z następujących opcji połączenia:

* [Podłączanie pulpitu zdalnego](#connect-with-remote-desktop). Po ustanowieniu połączenia przy użyciu usługi Podłączanie pulpitu zdalnego pojedynczego użytkownika można szybko połączyć development Kit.
* [Wirtualnej sieci prywatnej (VPN)](#connect-with-vpn). Po ustanowieniu połączenia przy użyciu sieci VPN wielu użytkowników jednocześnie połączyć z klientów poza infrastruktury Azure stosu. Połączenie sieci VPN wymaga konfiguracji.

<a name="connect-to-azure-stack-with-remote-desktop"></a>
##  <a name="connect-to-azure-stack-by-using-remote-desktop-connection"></a>Nawiązać stosu Azure przy użyciu usługi Podłączanie pulpitu zdalnego

Pojedynczego użytkownika równoczesnych mogą zarządzać zasobami w portalu operator lub portalu użytkowników za pomocą usługi Podłączanie pulpitu zdalnego.

1. Otwieranie usługi Podłączanie pulpitu zdalnego i połącz się z zestawem deweloperskim. Dla nazwy użytkownika, wprowadź **AzureStack\AzureStackAdmin**. Użyj hasła operator określoną podczas konfigurowania stosu Azure.  

2. Na komputerze dewelopera zestawu Otwórz Menedżera serwera. Wybierz **lokalnego serwera**, wyczyść **zwiększonych zabezpieczeń programu Internet Explorer** pole wyboru, a następnie Zamknij Menedżera serwera.

3. Aby otworzyć [portal użytkowników](azure-stack-key-features.md#portal), przejdź do https://portal.local.azurestack.external/. Zaloguj się przy użyciu poświadczeń użytkownika. Aby otworzyć stosu Azure [operator portal](azure-stack-key-features.md#portal), przejdź do https://adminportal.local.azurestack.external/. Zaloguj się przy użyciu poświadczeń usługi Azure Active Directory (Azure AD), które zostały określone podczas instalacji.

<a name="connect-to-azure-stack-with-vpn"></a>
## <a name="connect-to-azure-stack-by-using-vpn"></a>Połącz stos Azure za pomocą sieci VPN

Można ustanowić połączenia sieci VPN Azure stosu Development Kit tunelu podzielonego. Dostęp do portalu Azure stosu operatora, portal użytkowników i instalowana lokalnie narzędzia, takie jak Visual Studio i programu PowerShell do zarządzania zasobami Azure stosu, można użyć połączenia sieci VPN. Połączenie sieci VPN jest obsługiwana w usłudze Azure AD i wdrożeniach usługi Active Directory Federation Services (AD FS). Połączenia sieci VPN umożliwiają wielu klientom na łączenie się do stosu Azure w tym samym czasie.

> [!NOTE]
> Połączenie sieci VPN nie zapewnia łączności z infrastruktury Azure stosu maszyn wirtualnych.

### <a name="prerequisites"></a>Wymagania wstępne

1. Zainstaluj [programu Azure PowerShell platformy Azure zgodnego stosu](azure-stack-powershell-install.md) na komputerze lokalnym.  
2. Pobierz [narzędzia niezbędne do pracy z stosu Azure](azure-stack-powershell-download.md).

### <a name="set-up-vpn-connectivity"></a>Skonfiguruj połączenie z siecią VPN

Aby utworzyć połączenie sieci VPN w taki sposób, aby zestaw deweloperski, Otwórz program Windows PowerShell jako administrator na komputerze lokalnym opartych na systemie Windows. Następnie uruchom następujący skrypt (aktualizacja adresu IP i hasła wartości dla danego środowiska):

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

Połącz się z wystąpieniem stosu Azure przy użyciu jednej z następujących metod:  

* Użyj `Connect-AzsVpn ` polecenia:
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  Po wyświetleniu monitu zaufania hosta stosu Azure i instalowania certyfikatu z **AzureStackCertificateAuthority** w magazynie certyfikatów komputera lokalnego. (Monit mogą być ukryte przez okno programu PowerShell).

* Na komputerze lokalnym, zaznacz **ustawienia sieciowe** > **VPN** > **azurestack** > **połączyć**. W wierszu logowania, wprowadź nazwę użytkownika (**AzureStack\AzureStackAdmin**) i hasło.

### <a name="test-vpn-connectivity"></a>Przetestowanie łączności z siecią VPN

Aby przetestować połączenie portalu, otwórz przeglądarkę sieci web, a następnie przejdź do portalu użytkowników (https://portal.local.azurestack.external/) lub portalu — operator (https://adminportal.local.azurestack.external/). Zaloguj się i utworzyć zasobów.  

## <a name="next-steps"></a>Kolejne kroki

[Udostępnić użytkownikom stosu Azure maszyny wirtualne](azure-stack-tutorial-tenant-vm.md)
