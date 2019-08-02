---
title: Instalacja systemu Windows Azure Security Center dla agenta IoT | Microsoft Docs
description: Dowiedz się więcej na temat sposobu instalowania Azure Security Center dla agenta IoT na 32-bitowych lub 64-bitowych urządzeniach z systemem Windows.
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
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597212"
---
# <a name="deploy-an-azure-security-center-for-iot-c-based-security-agent-for-windows"></a>Wdróż Azure Security Center dla agenta zabezpieczeń C#opartego na IoT dla systemu Windows

W tym przewodniku wyjaśniono, jak zainstalować Azure Security Center C#dla agenta zabezpieczeń opartego na IoT w systemie Windows.

Niniejszy przewodnik zawiera informacje na temat wykonywania następujących czynności: 
> [!div class="checklist"]
> * Instalowanie
> * Weryfikowanie wdrożenia
> * Odinstalowywanie agenta
> * Rozwiązywanie problemów 

## <a name="prerequisites"></a>Wymagania wstępne

W przypadku innych platform i rodzajów agentów zobacz [Wybieranie odpowiedniego agenta zabezpieczeń](how-to-deploy-agent.md).

1. Prawa administratora lokalnego na komputerze, na którym chcesz się zainstalować. 

1. [Utwórz moduł zabezpieczeń](quickstart-create-security-twin.md) dla urządzenia.

## <a name="installation"></a>Instalacja 

Aby zainstalować agenta zabezpieczeń, użyj następującego przepływu pracy:

1. Zainstaluj Azure Security Center dla agenta IoT dla C# systemu Windows na urządzeniu. Pobierz najnowszą wersję na swoją maszynę z Azure Security Center na potrzeby repozytorium IoT [GitHub](https://github.com/Azure/Azure-IoT-Security-Agent-CS).

1. Wyodrębnij zawartość pakietu i przejdź do folderu/install.

1. Otwórz program Windows PowerShell jako administrator. 
1. Dodaj uruchomione uprawnienia do skryptu InstallSecurityAgent, uruchamiając:<br>
    ```
    Unblock-File .\InstallSecurityAgent.ps1
    ```
    
    następnie uruchom polecenie:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```
    
    Na przykład:
    
    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```
    
    Aby uzyskać więcej informacji na temat parametrów uwierzytelniania, zobacz [jak skonfigurować uwierzytelnianie](concept-security-agent-authentication-methods.md).

Ten skrypt wykonuje następujące czynności:

- Instaluje wymagania wstępne.

- Dodaje użytkownika usługi (z wyłączonym logowaniem interakcyjnym).

- Instaluje agenta jako **usługę systemową**.

- Konfiguruje agenta przy użyciu podanych parametrów uwierzytelniania.


Aby uzyskać dodatkową pomoc, użyj polecenia Get-Help w programie PowerShell. <br>Przykład get-help:  
    ```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>Sprawdź stan wdrożenia

- Sprawdź stan wdrożenia agenta, uruchamiając następujące:<br>
    ```sc.exe query "ASC IoT Agent"```

### <a name="uninstall-the-agent"></a>Odinstalowywanie agenta

Aby odinstalować agenta:

1. Uruchom następujący skrypt programu PowerShell z parametrem **-mode** ustawionymdo odinstalowania.  

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ``` 

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli uruchomienie agenta nie powiedzie się, Włącz rejestrowanie (domyślnie jest ono *wyłączone* ), aby uzyskać więcej informacji.

Aby włączyć rejestrowanie:

1. Otwórz plik konfiguracji (General. config) do edycji przy użyciu standardowego edytora plików.

1. Edytuj następujące wartości:

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/> 
   <add key="diagnosticVerbosityLevel" value="Some" /> 
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > Zalecamy wyłączenie wylogowywania **po zakończeniu** rozwiązywania problemów. Pozostawienie **logowania powoduje zwiększenie** rozmiaru pliku dziennika i użycie danych. 

1. Uruchom ponownie agenta, uruchamiając następujące polecenie programu PowerShell lub wiersza polecenia:

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

1. Przejrzyj plik dziennika, aby uzyskać więcej informacji o błędzie.

   Lokalizacja pliku dziennika:`%WinDir%/System32/IoTAgentLog.log`


## <a name="next-steps"></a>Następne kroki
- Przeczytaj Azure Security Center dla usługi IoT [— Omówienie](overview.md)
- Dowiedz się więcej o [architekturze](architecture.md) Azure Security Center dla usługi IoT
- Włącz [usługę](quickstart-onboard-iot-hub.md)
- Przeczytaj [często zadawane pytania](resources-frequently-asked-questions.md)
- Informacje [](concept-security-alerts.md) o alertach