---
title: Instalacja Windows ASC agenta IoT (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu instalowania ASC agenta IoT na urządzeniach Windows 32-bitową lub 64-bitowych.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.service: ascforiot
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2019
ms.author: mlottner
ms.openlocfilehash: 0b1061cca03ab4465753e6c0b8d3341472007b9c
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541594"
---
# <a name="tutorial-deploy-asc-for-iot-c-based-security-agent-for-windows"></a>Samouczek: Wdrażanie usługi ASC dla IoT C#— na podstawie agenta zabezpieczeń dla Windows

> [!IMPORTANT]
> ASC IoT jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym samouczku opisano sposób instalowania ASC dla IoT C#— na podstawie agenta zabezpieczeń na Windows.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności: 
> [!div class="checklist"]
> * Instalowanie
> * Weryfikowanie wdrożenia
> * Odinstaluj agenta
> * Rozwiązywanie problemów 

## <a name="prerequisites"></a>Wymagania wstępne

Dla innych platform i smaki agenta, zobacz [wybierz agenta prawa zabezpieczeń](select-deploy-agent.md).

1. Aby wdrożyć agenta zabezpieczeń, na komputerze, który chcesz zainstalować na są wymagane uprawnienia administratora lokalnego. 

1. [Utwórz moduł zabezpieczeń](quickstart-create-security-twin.md) dla tego urządzenia.

## <a name="installation"></a>Instalacja 

Aby zainstalować agenta zabezpieczeń, wykonaj następujące czynności:

1. Aby zainstalować usługi ASC agenta IoT na urządzeniu, pobrać najnowszą wersję na komputer od usługi ASC w repozytorium IoT GitHub (.. /.. / zwalnia) folder.

2. Wyodrębnij zawartość pakietu, a następnie przejdź do folderu/Install.

3. Otwórz program Windows PowerShell. 
    1. Dodaj uprawnienia Uruchamianie skryptu InstallSecurityAgent, uruchamiając ```Unblock-File .\InstallSecurityAgent.ps1```
    
        a następnie uruchom:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```
    
    Zobacz [jak skonfigurować uwierzytelnianie](concept-security-agent-authentication-methods.md) Aby uzyskać więcej informacji na temat parametrów uwierzytelniania.

Ten skrypt wykonuje następujące czynności:

- Instaluje wstępnie wymagane składniki.

- Dodaje użytkownika usługi (za pomocą logowania interaktywnego wyłączone).

- Instaluje agenta jako **usługa systemowa**.

- Konfiguruje agenta przy użyciu parametrów podanych uwierzytelniania.


Aby uzyskać dodatkową pomoc użyj polecenia Get-Help w programie PowerShell <br>Przykład Get-Help:  
    ```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>Sprawdź stan wdrożenia

- Sprawdź stan wdrożenia agenta, uruchamiając:<br>
    ```sc.exe query "ASC IoT Agent" ```

### <a name="uninstall-the-agent"></a>Odinstaluj agenta

Aby odinstalować agenta:

1. Uruchom następujący skrypt programu PowerShell przy użyciu **— tryb** parametr **Odinstaluj**.  

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ``` 

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli agent nie powiedzie się, Włącz rejestrowanie (rejestrowanie jest *poza* domyślnie) Aby uzyskać więcej informacji.

Aby włączyć rejestrowanie:

1. Otwórz plik konfiguracji do edycji, za pomocą edytora standardowego pliku.

1. Zmodyfikuj następujące wartości:

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/> 
   <add key="diagnosticVerbosityLevel" value="Some" /> 
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > Firma Microsoft zaleca włączenie rejestrowania **poza** po zakończeniu rozwiązywania problemów. Pozostawienie rejestrowania **na** zwiększa dziennika użycie rozmiaru i danych plików. 

1. Uruchom ponownie agenta, uruchamiając następujące programu PowerShell lub wierszu polecenia:

    **PowerShell**
     ```
     Restart-Service "ASC IoT Agent"
     ```
     
   lub

    **CMD**
     ```
     sc.exe stop "ASC IoT Agent" 
     sc.exe start "ASC IoT Agent" 
     ```

1. Przejrzyj plik dziennika, aby uzyskać więcej informacji o awarii.

   Lokalizacja pliku dziennika: `<unpacked_agent_location>/IoTAgentLog.log`


## <a name="next-steps"></a>Kolejne kroki
- Przeczytaj ASC dla usługi IoT [— omówienie](overview.md)
- Dowiedz się więcej o ASC IoT [architektury](architecture.md)
- Włącz [usługi](quickstart-onboard-iot-hub.md)
- Odczyt [— często zadawane pytania](resources-frequently-asked-questions.md)
- Zrozumienie [alertów](concept-security-alerts.md)