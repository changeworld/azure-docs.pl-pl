---
title: Połącz Azure stosu | Dokumentacja firmy Microsoft
description: Dowiedz się, jak połączyć Azure stosu
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
ms.date: 08/22/2017
ms.author: mabrigg
ms.openlocfilehash: 7479202a8afabf5a84560691a2bccf849206c077
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2017
ms.locfileid: "26636181"
---
# <a name="connect-to-azure-stack"></a>Nawiązywanie połączenia z usługą Azure Stack

Aby zarządzać zasobami, należy połączyć Azure stosu Development Kit. Szczegóły tego artykułu kroki wymagane do nawiązania połączenia z zestawem deweloperskim. Możesz użyć dowolnej z następujących opcji połączenia:

* [Pulpit zdalny](#connect-with-remote-desktop): umożliwia pojedynczego użytkownika równoczesnych szybko nawiązywanie połączenia z zestaw deweloperski.
* [Wirtualnej sieci prywatnej (VPN)](#connect-with-vpn): umożliwia łączenie wielu równoczesnych użytkowników z klientów poza infrastruktury Azure stosu (wymaga konfiguracji).

## <a name="connect-to-azure-stack-with-remote-desktop"></a>Połącz stos Azure przy użyciu pulpitu zdalnego
Za pomocą połączenia pulpitu zdalnego pojedynczego użytkownika równoczesnych może współpracować z portalu do zarządzania zasobami.

1. Otwórz Podłączanie pulpitu zdalnego i połącz się z zestawem deweloperskim. Wprowadź **AzureStack\AzureStackAdmin** jako nazwa użytkownika i hasło administracyjne podane podczas instalacji stosu Azure.  

2. Na komputerze dewelopera zestawu, otwórz Menedżera serwera kliknij **lokalnego serwera**, wyłącz zwiększonych zabezpieczeń programu Internet Explorer, a następnie Zamknij Menedżera serwera.

3. Aby otworzyć portal, przejdź do (https://portal.local.azurestack.external/) i zaloguj się przy użyciu poświadczeń użytkownika.


## <a name="connect-to-azure-stack-with-vpn"></a>Połącz Azure stos z siecią VPN

Można utworzyć tunel podziału połączenia wirtualnej sieci prywatnej (VPN) Azure stosu Development Kit. Za pośrednictwem połączenia sieci VPN mogą korzystać z portalu administratora portalu użytkownika i narzędzi, takich jak Visual Studio i programu PowerShell do zarządzania zasobami Azure stosu zainstalowane lokalnie. Połączenie z siecią VPN jest obsługiwana w obu Directory(AAD) Active Azure i Active Directory Federation Services(AD FS) na podstawie wdrożeń. Połączenia sieci VPN Włącz wielu klientom na łączenie się do stosu Azure w tym samym czasie. 

> [!NOTE] 
> To połączenie VPN nie zapewnia łączności z infrastruktury Azure stosu maszyn wirtualnych. 

### <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj [stosu Azure zgodne Azure PowerShell](azure-stack-powershell-install.md) na komputerze lokalnym.  
* Pobierz [narzędzia niezbędne do pracy z stosu Azure](azure-stack-powershell-download.md). 

### <a name="configure-vpn-connectivity"></a>Konfigurowanie połączeń sieci VPN

Aby utworzyć połączenie sieci VPN w taki sposób, aby zestaw deweloperski, otwórz sesję programu PowerShell z podwyższonym poziomem uprawnień z komputera lokalnego z systemem Windows i uruchom następujący skrypt (Pamiętaj zaktualizować wartości adresów i haseł IP dla danego środowiska):

```PowerShell 
# Configure winrm if it's not already configured
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module
Import-Module .\Connect\AzureStack.Connect.psm1 

# Add the development kit computer’s host IP address & certificate authority (CA) to the list of trusted hosts. Make sure to update the the IP address and password values for your environment. 

$hostIP = "<Azure Stack host IP address>"

$Password = ConvertTo-SecureString `
  "<Administrator password provided when deploying Azure Stack>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

Jeśli konfiguracja zakończy się pomyślnie, powinien zostać wyświetlony **azurestack** na liście połączeń sieci VPN.

![Połączenia sieciowe](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack"></a>Nawiązywanie połączenia z usługą Azure Stack

Połącz się z wystąpieniem stosu Azure przy użyciu jednej z następujących dwóch metod:  

* Za pomocą `Connect-AzsVpn ` polecenia: 
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  Po wyświetleniu monitu zaufania hosta stosu Azure i instalowania certyfikatu z **AzureStackCertificateAuthority** na magazynie certyfikatów komputera lokalnego. (monit może wyglądać za oknem sesji programu PowerShell). 

* Otwórz na komputerze lokalnym **ustawienia sieciowe** > **VPN** > kliknij **azurestack** > **połączyć**. W wierszu logowania wprowadź nazwę użytkownika (AzureStack\AzureStackAdmin) i hasło.

### <a name="test-the-vpn-connectivity"></a>Przetestowanie łączności z siecią VPN

Aby przetestować połączenie portalu, otwórz przeglądarkę internetową i przejdź do portalu użytkowników (https://portal.local.azurestack.external/), zaloguj się i utworzyć zasobów.  

## <a name="next-steps"></a>Następne kroki



