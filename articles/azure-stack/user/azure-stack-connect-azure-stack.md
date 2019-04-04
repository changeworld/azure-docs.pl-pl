---
title: Nawiązywanie połączenia usługi Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak połączyć usługę Azure Stack
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
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: mabrigg
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 9b7a5aef76ec72b0de76fb4517ffda02808eab9d
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58882494"
---
# <a name="connect-to-azure-stack"></a>Nawiązywanie połączenia z usługą Azure Stack

Aby zarządzać zasobami, możesz nawiązać Azure Stack Development Kit. Ten artykuł zawiera kroki wymagane do połączenia z deweloperski. Można użyć jednej z następujących opcji połączenia:

* Pulpit zdalny: umożliwia pojedynczego użytkownika współbieżnych szybko Nawiąż kontakt z zestawu SDK.
* Wirtualna sieć prywatna (VPN): umożliwia jednoczesną pracę wielu użytkowników połączenia od klientów poza infrastruktury Azure Stack (wymaga konfiguracji).

## <a name="connect-to-azure-stack-with-remote-desktop"></a>Nawiązywanie połączenia usługi Azure Stack przy użyciu pulpitu zdalnego
Za pomocą połączenia pulpitu zdalnego pojedynczy równoczesny użytkownik może współpracować z portalu do zarządzania zasobami.

1. Otwórz Podłączanie pulpitu zdalnego i połącz development Kit. Wprowadź **AzureStack\AzureStackAdmin** jako nazwy użytkownika i hasło administracyjne, podane podczas instalacji usługi Azure Stack.  

2. Na komputerze deweloperskim zestaw, otwórz Menedżera serwera kliknij **lokalnego serwera**, wyłącz zwiększonych zabezpieczeń programu Internet Explorer, a następnie Zamknij Menedżera serwera.

3. Aby otworzyć portal, przejdź do (https://portal.local.azurestack.external/) i zaloguj się przy użyciu poświadczeń użytkownika.


## <a name="connect-to-azure-stack-with-vpn"></a>Nawiązywanie połączenia usługi Azure Stack za pomocą połączenia VPN

Aby ustanowić podzielony tunel połączenia wirtualnej sieci prywatnej (VPN, Virtual Private Network) do usługi Azure Stack Development Kit. Za pomocą połączenia sieci VPN mogą uzyskiwać dostęp do portalu administratora portalu użytkowników i zainstalowane lokalnie narzędzi, takich jak Visual Studio i PowerShell do zarządzania zasobami usługi Azure Stack. Połączenie z siecią VPN jest obsługiwane w obu o Active Azure i Active Directory Federation Services(AD FS) na podstawie wdrożeń. Połączenia sieci VPN umożliwiają wielu klientów do łączenia z usługą Azure Stack w tym samym czasie. 

> [!NOTE] 
> To połączenie VPN nie zapewnia łączności na maszynach wirtualnych infrastruktury Azure Stack. 

### <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj [zgodne Azure PowerShell usługi Azure Stack](azure-stack-powershell-install.md) na komputerze lokalnym.  
* Pobierz [narzędzia wymagane do pracy z usługą Azure Stack](azure-stack-powershell-download.md). 

### <a name="configure-vpn-connectivity"></a>Konfigurowanie połączeń sieci VPN

Aby utworzyć połączenie sieci VPN w taki sposób, aby zestaw development kit, otwórz sesję programu PowerShell z podwyższonym poziomem uprawnień z komputera lokalnego z systemem Windows, a następnie uruchom następujący skrypt (Pamiętaj zaktualizować wartości IP adres i hasło dla danego środowiska):

```powershell 
# Configure winrm if it's not already configured
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module
Import-Module .\Connect\AzureStack.Connect.psm1 

# Add the development kit computer’s host IP address & certificate authority (CA) to the list of trusted hosts. Make sure to update the IP address and password values for your environment. 

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

Połącz się z wystąpieniem usługi Azure Stack przy użyciu jednej z następujących dwóch metod:  

* Za pomocą `Connect-AzsVpn` polecenia: 
    
  ```powershell
  Connect-AzsVpn `
    -Password $Password
  ```

  Po wyświetleniu monitu zaufania hosta usługi Azure Stack i zainstalować certyfikat z **AzureStackCertificateAuthority** na magazynie certyfikatów komputera lokalnego. (monit może pojawić się za zaporą w oknie sesji programu PowerShell.) 

* Otwórz na komputerze lokalnym **ustawienia sieciowe** > **VPN** > kliknij **azurestack** > **połączyć**. W wierszu logowania wprowadź nazwę użytkownika (AzureStack\AzureStackAdmin) i hasło.

### <a name="test-the-vpn-connectivity"></a>Testowanie łączności sieci VPN

Aby przetestować połączenie z portalu, otwórz przeglądarkę internetową i przejdź do portalu użytkowników (https://portal.local.azurestack.external/), zaloguj się, jak i tworzenia zasobów.  

## <a name="next-steps"></a>Kolejne kroki



