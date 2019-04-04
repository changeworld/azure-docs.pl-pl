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
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: knithinc
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: 31025582516198bdfe9da9312bae33852986a423
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58884860"
---
# <a name="connect-to-the-asdk"></a>Nawiązać połączenie z ASDK

Aby zarządzać zasobami, musisz najpierw połączyć do usługi Azure Stack Development Kit (ASDK). W tym artykule opisano kroki, które należy wykonać w celu nawiązywanie ASDK przy użyciu następujących opcji połączenia:

* [Podłączanie pulpitu zdalnego (RDP)](#connect-with-rdp). Po nawiązaniu połączenia za pomocą połączenia pulpitu zdalnego, pojedynczego użytkownika może szybko nawiązać połączenie deweloperski.
* [Wirtualna sieć prywatna (VPN)](#connect-with-vpn). Po nawiązaniu połączenia za pośrednictwem sieci VPN, wielu użytkowników może jednocześnie nawiązać portali usługi Azure Stack od klientów poza infrastruktury Azure Stack. Połączenie sieci VPN wymaga konfiguracji.

<a name="connect-with-rdp"></a>
## <a name="connect-to-azure-stack-using-rdp"></a>Nawiązywanie połączenia usługi Azure Stack przy użyciu protokołu RDP

Jeden użytkownik współbieżnych zarządzać zasobami w portalu administratora usługi Azure Stack lub portalu użytkowników za pomocą połączenia pulpitu zdalnego bezpośrednio z komputera hosta ASDK. 

> [!TIP]
> Ta opcja umożliwia także przy użyciu protokołu RDP ponownie gdy użytkownik jest zalogowany na komputerze-hoście ASDK Zaloguj się do maszyn wirtualnych utworzonych na komputerze-hoście ASDK. 

1. Otworzyć usługę Podłączanie pulpitu zdalnego (mstc.exe) i nawiązać połączenie z development kit hosta adres IP komputera za pomocą konta mającego autoryzację do logowania się zdalnie do komputera hosta ASDK. Domyślnie **AzureStack\AzureStackAdmin** ma uprawnienia do zdalnej w komputerze hosta ASDK.  

2. Na komputerze deweloperskim zestaw hosta otwórz Menedżera serwera (ServerManager.exe). Wybierz **lokalnego serwera**, wyłącz **Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer**i Zamknij Menedżera serwera.

3. Zaloguj się do portalu administratora jako **AzureStack\CloudAdmin** lub użyj innych poświadczeń usługi Azure Stack operatora. Adres portalu administrowania ASDK [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).

4. Zaloguj się do portalu użytkownika jako **AzureStack\CloudAdmin** lub użyj innych poświadczeń użytkownika usługi Azure Stack. Adres portalu użytkownika ASDK [ https://portal.local.azurestack.external ](https://portal.local.azurestack.external).

> [!NOTE]
> Aby uzyskać więcej informacji o tym, kiedy należy używać konta, którego można zobaczyć [podstawy administracji ASDK](asdk-admin-basics.md#what-account-should-i-use).

<a name="connect-with-vpn"></a>
## <a name="connect-to-azure-stack-using-vpn"></a>Nawiązywanie połączenia przy użyciu sieci VPN w usłudze Azure Stack

Aby ustanowić podzielony tunel połączenia sieci VPN na komputerze hosta ASDK dostęp do usługi Azure Stack portali i zainstalowanych lokalnie narzędzi, takich jak Visual Studio i PowerShell. Korzystając z połączenia sieci VPN, połączyć wielu użytkowników jednocześnie do zasobów usługi Azure Stack pracujących ASDK.

Połączenie z siecią VPN jest obsługiwane dla usługi Azure AD i Active Directory Federation Services (AD FS) wdrożenia.

> [!NOTE]
> Połączenie sieci VPN *nie* zapewnić łączność z maszynami wirtualnymi platformy Azure Stack. Nie można nawiązać połączenie RDP maszyny wirtualne dla platformy Azure Stack po nawiązaniu połączenia za pośrednictwem sieci VPN.

### <a name="prerequisites"></a>Wymagania wstępne
Przed skonfigurowaniem połączenia sieci VPN w taki sposób, aby ASDK, upewnij się, że zostały spełnione następujące wymagania wstępne.

- Zainstaluj [programu Azure PowerShell dla usługi Azure Stack zgodnego](asdk-post-deploy.md#install-azure-stack-powershell) na komputerze lokalnym.  
- Pobierz [narzędzia wymagane do pracy z usługą Azure Stack](asdk-post-deploy.md#download-the-azure-stack-tools).

### <a name="set-up-vpn-connectivity"></a>Konfigurowanie połączeń sieci VPN

Aby utworzyć połączenie sieci VPN w taki sposób, aby ASDK, Otwórz program PowerShell jako administrator na lokalnym komputerze z systemem Windows. Następnie uruchom następujący skrypt (Aktualizacja środowiska wartości adresu IP i hasła):

```powershell
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

![Połączenia sieciowe](media/asdk-connect/vpn.png)  

### <a name="connect-to-azure-stack"></a>Nawiązywanie połączenia z usługą Azure Stack

  Połącz się z wystąpieniem usługi Azure Stack przy użyciu jednej z następujących metod:  

  * Użyj `Connect-AzsVpn` polecenia:
      
    ```powershell
    Connect-AzsVpn `
      -Password $Password
    ```

  * Na komputerze lokalnym, wybierz **ustawienia sieciowe** > **VPN** > **azurestack** > **połączyć**. W wierszu logowania wprowadź nazwę użytkownika (**AzureStack\AzureStackAdmin**) i hasło.

Po raz pierwszy łączysz, użytkownik jest monitowany do zainstalowania certyfikatu głównego usługi Azure Stack z **AzureStackCertificateAuthority** w magazynie certyfikatów komputera lokalnego. Ten krok powoduje dodanie ASDK urzędu certyfikacji (CA) do listy zaufanych hostów. Kliknij przycisk **tak** instalacji certyfikatu.

![Certyfikat główny](media/asdk-connect/cert.png)  
  
  > [!IMPORTANT]
  > Monit mogą być ukryte przez okno programu PowerShell lub innych aplikacji.

### <a name="test-vpn-connectivity"></a>Testowanie łączności sieci VPN

Aby przetestować połączenie z portalu, otwórz przeglądarkę sieci web, a następnie przejdź do portalu użytkowników (https://portal.local.azurestack.external/) lub portalu administracyjnego (https://adminportal.local.azurestack.external/). 

Zaloguj się przy użyciu poświadczeń odpowiednią subskrypcję, aby utworzyć zasoby i zarządzać nimi.  

## <a name="next-steps"></a>Kolejne kroki

[Rozwiązywanie problemów](asdk-troubleshooting.md)
