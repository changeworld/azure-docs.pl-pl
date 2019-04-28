---
title: Windows instalacji usługi Azure Security Center dla agenta IoT (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu instalowania usługi Azure Security Center dla agenta IoT na urządzeniach Windows 32-bitową lub 64-bitowych.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2019
ms.author: mlottner
ms.openlocfilehash: 601ee706adedf522890acc3f3996a7d36b349348
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61357782"
---
# <a name="deploy-an-azure-security-center-for-iot-c-based-security-agent-for-windows"></a>Wdrażanie usługi Azure Security Center dla IoT C#— na podstawie agenta zabezpieczeń dla Windows

> [!IMPORTANT]
> Centrum zabezpieczeń Azure dla IoT jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym przewodniku objaśniono sposób instalowania usługi Azure Security Center (ASC) dla IoT C#— na podstawie agenta zabezpieczeń na Windows.

Niniejszy przewodnik zawiera informacje na temat wykonywania następujących czynności: 
> [!div class="checklist"]
> * Instalowanie
> * Weryfikowanie wdrożenia
> * Odinstaluj agenta
> * Rozwiązywanie problemów 

## <a name="prerequisites"></a>Wymagania wstępne

Dla innych platform i smaki agenta, zobacz [wybierz agenta prawa zabezpieczeń](how-to-deploy-agent.md).

1. Prawa administratora lokalnego na komputerze, który chcesz zainstalować na. 

1. [Utwórz moduł zabezpieczeń](quickstart-create-security-twin.md) dla tego urządzenia.

## <a name="installation"></a>Instalacja 

Aby zainstalować agenta zabezpieczeń, wykonaj następujące czynności:

1. Aby zainstalować usługi ASC dla Windows IoT C# agenta na urządzeniu, Pobierz najnowszą wersję na komputer od usługi ASC dla IoT [repozytorium GitHub](https://github.com/Azure/Azure-IoT-Security-Agent-CS).

2. Wyodrębnij zawartość pakietu, a następnie przejdź do folderu/Install.

3. Otwórz program Windows PowerShell jako Administrator. 
    1. Dodaj uprawnienia Uruchamianie skryptu InstallSecurityAgent, uruchamiając ```Unblock-File .\InstallSecurityAgent.ps1```
    
        a następnie uruchom:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```
    
    Na przykład:
    
    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
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
    ```sc.exe query "ASC IoT Agent"```

### <a name="uninstall-the-agent"></a>Odinstaluj agenta

Aby odinstalować agenta:

1. Uruchom następujący skrypt programu PowerShell przy użyciu **— tryb** parametr **Odinstaluj**.  

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ``` 

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli agent nie powiedzie się, Włącz rejestrowanie (rejestrowanie jest *poza* domyślnie) Aby uzyskać więcej informacji.

Aby włączyć rejestrowanie:

1. Otwórz plik konfiguracji (General.config) do edycji, za pomocą edytora standardowego pliku.

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

   Lokalizacja pliku dziennika: `%WinDir%/System32/IoTAgentLog.log`


## <a name="next-steps"></a>Kolejne kroki
- Przeczytaj ASC dla usługi IoT [— omówienie](overview.md)
- Dowiedz się więcej o ASC IoT [architektury](architecture.md)
- Włącz [usługi](quickstart-onboard-iot-hub.md)
- Odczyt [— często zadawane pytania](resources-frequently-asked-questions.md)
- Zrozumienie [alertów](concept-security-alerts.md)