---
title: Instalacja usługi Azure Security Center dla agenta IoT w systemie Windows | Dokumenty firmy Microsoft
description: Dowiedz się, jak zainstalować usługę Azure Security Center dla agenta IoT na 32-bitowych lub 64-bitowych urządzeniach z systemem Windows.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: acc99f260931de7fd8c7566a3ff6daf43f34c5ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68597212"
---
# <a name="deploy-an-azure-security-center-for-iot-c-based-security-agent-for-windows"></a>Wdrażanie agenta zabezpieczeń usługi Azure Security Center dla IoT opartego na języku C# dla systemu Windows

W tym przewodniku wyjaśniono, jak zainstalować agenta zabezpieczeń usługi Azure Security Center dla usługi IoT C w systemie Windows.

Niniejszy przewodnik zawiera informacje na temat wykonywania następujących czynności: 
> [!div class="checklist"]
> * Zainstaluj
> * Weryfikowanie wdrożenia
> * Odinstalowywanie agenta
> * Rozwiązywanie problemów 

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać więcej informacji na temat innych platform i smaków agentów, zobacz [Wybieranie odpowiedniego agenta zabezpieczeń](how-to-deploy-agent.md).

1. Prawa administratora lokalnego na komputerze, na którym chcesz zainstalować. 

1. [Utwórz moduł zabezpieczeń](quickstart-create-security-twin.md) dla urządzenia.

## <a name="installation"></a>Instalacja 

Aby zainstalować agenta zabezpieczeń, użyj następującego przepływu pracy:

1. Zainstaluj agenta Usługi Azure Security Center dla systemu IoT Windows C# na urządzeniu. Pobierz najnowszą wersję na swój komputer z [repozytorium](https://github.com/Azure/Azure-IoT-Security-Agent-CS)Usługi Azure Security Center dla usługi IoT GitHub .

1. Wyodrębnij zawartość pakietu i przejdź do /Install folderu.

1. Otwórz program Windows PowerShell jako administrator. 
1. Dodaj uruchomione uprawnienia do skryptu InstallSecurityAgent, uruchamiając:<br>
    ```
    Unblock-File .\InstallSecurityAgent.ps1
    ```
    
    następnie uruchom:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```
    
    Przykład:
    
    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```
    
    Aby uzyskać więcej informacji na temat parametrów uwierzytelniania, zobacz [Jak skonfigurować uwierzytelnianie](concept-security-agent-authentication-methods.md).

Ten skrypt wykonuje następujące akcje:

- Instaluje wymagania wstępne.

- Dodaje użytkownika usługi (z wyłączonym logiem interaktywnym).

- Instaluje agenta jako **usługę systemową**.

- Konfiguruje agenta z podanymi parametrami uwierzytelniania.


Aby uzyskać dodatkową pomoc, użyj polecenia Get-Help w programie PowerShell <br>Przykład pomocy get:  
    ```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>Weryfikowanie stanu wdrożenia

- Sprawdź stan wdrożenia agenta, uruchamiając:<br>
    ```sc.exe query "ASC IoT Agent"```

### <a name="uninstall-the-agent"></a>Odinstalowywanie agenta

Aby odinstalować agenta:

1. Uruchom następujący skrypt programu PowerShell z parametrem **-mode** ustawionym na **Odinstaluj**.  

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ``` 

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli agent nie może uruchomić, włącz rejestrowanie (rejestrowanie jest domyślnie *wyłączone),* aby uzyskać więcej informacji.

Aby włączyć rejestrowanie:

1. Otwórz plik konfiguracyjny (General.config) do edycji za pomocą standardowego edytora plików.

1. Edytuj następujące wartości:

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/> 
   <add key="diagnosticVerbosityLevel" value="Some" /> 
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > Zalecamy wyłączenie **off** wylogowania po zakończeniu rozwiązywania problemów. Pozostawienie **on** logowania zwiększa rozmiar pliku dziennika i użycie danych. 

1. Uruchom ponownie agenta, uruchamiając następujący program PowerShell lub wiersz polecenia:

    **PowerShell**
     ```
     Restart-Service "ASC IoT Agent"
     ```
     
   lub

    **Cmd**
     ```
     sc.exe stop "ASC IoT Agent" 
     sc.exe start "ASC IoT Agent" 
     ```

1. Przejrzyj plik dziennika, aby uzyskać więcej informacji na temat błędu.

   Lokalizacja pliku dziennika:`%WinDir%/System32/IoTAgentLog.log`


## <a name="next-steps"></a>Następne kroki
- Przeczytaj [omówienie](overview.md) usługi Azure Security Center dla IoT
- Dowiedz się więcej o usłudze Azure Security Center dla [architektury](architecture.md) IoT
- Włącz [usługę](quickstart-onboard-iot-hub.md)
- Przeczytaj [często](resources-frequently-asked-questions.md) zadawane pytania
- Opis [alertów](concept-security-alerts.md)