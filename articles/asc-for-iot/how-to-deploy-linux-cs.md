---
title: Przewodnik instalacji i wdrażania systemu Linux C# agenta ASC IoT (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zainstalować usługi ASC IoT agenta w systemie Linux zarówno 32-bitowych i 64-bitowych.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: b0982203-c3c8-4a0b-8717-5b5ac4038d8c
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: d6b4e6065b0ef198ad583b3760124730e658fe0b
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58619912"
---
# <a name="deploy-asc-for-iot-c-based-security-agent-for-linux"></a>Wdrażanie usługi ASC dla IoT C#— na podstawie zabezpieczeń agenta dla systemu Linux

> [!IMPORTANT]
> ASC IoT jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ten przewodnik wyjaśnia, jak zainstalować i wdrożyć usługi ASC dla IoT C#-agent zabezpieczenia oparty na w systemie Linux.

Niniejszy przewodnik zawiera informacje na temat wykonywania następujących czynności: 
> [!div class="checklist"]
> * Instalowanie
> * Weryfikowanie wdrożenia
> * Odinstaluj agenta
> * Rozwiązywanie problemów 

## <a name="prerequisites"></a>Wymagania wstępne

Dla innych platform i odmian agenta, zobacz [wybierz agenta prawa zabezpieczeń](how-to-deploy-agent.md).

1. Aby wdrożyć agenta zabezpieczeń, na komputerze, który chcesz zainstalować na są wymagane uprawnienia administratora lokalnego. 

1. [Utwórz moduł zabezpieczeń](quickstart-create-security-twin.md) dla tego urządzenia.

## <a name="installation"></a>Instalacja 

Aby wdrożyć agenta zabezpieczeń, wykonaj następujące czynności:

1. Pobierz najnowszą wersją na swoim komputerze z poziomu [Github](https://aka.ms/iot-security-github-cs).

1. Wyodrębnij zawartość pakietu, a następnie przejdź do _/Install_ folderu.

1. Dodaj uruchomionej uprawnienia do **skryptu InstallSecurityAgent** , uruchamiając `chmod +x InstallSecurityAgent.sh` 

1. Następnie uruchom: 

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```
   
   Zobacz [jak skonfigurować uwierzytelnianie](concept-security-agent-authentication-methods.md) Aby uzyskać więcej informacji na temat parametrów uwierzytelniania.

Ten skrypt wykonuje następujące czynności:

- Instaluje wstępnie wymagane składniki.

- Dodaje użytkownika usługi (za pomocą logowania interaktywnego wyłączone).

- Instaluje agenta jako **demona** -założono, że urządzenie używa **systemd** do zarządzania usługami.

- Konfiguruje **sudo** zezwolić agentowi wykonywanie określonych zadań jako katalog główny.

- Konfiguruje agenta przy użyciu parametrów podanych uwierzytelniania.


Aby uzyskać dodatkową pomoc, uruchom skrypt przy użyciu parametru – pomocy: `./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>Odinstaluj agenta

Aby odinstalować agenta, uruchom skrypt przy użyciu parametru – u: `./InstallSecurityAgent.sh -u`. 

> [!NOTE]
> Odinstalowanie nie usuwa wszystkie brakujące wymagania wstępne, które zostały zainstalowane podczas instalacji.

## <a name="troubleshooting"></a>Rozwiązywanie problemów  

1. Sprawdź stan wdrożenia, uruchamiając:

    `systemctl status ASCIoTAgent.service`

2. Włącz rejestrowanie.  
   Jeśli agent nie powiedzie się, należy włączyć rejestrowanie, aby uzyskać więcej informacji.

   Włącz rejestrowanie przez:

   1. Otwórz plik konfiguracji do edycji w dowolnym edytorze systemu Linux:

        `vi /var/ASCIoTAgent/General.config`

   1. Zmodyfikuj następujące wartości: 

      ```
      <add key="logLevel" value="Debug"/>
      <add key="fileLogLevel" value="Debug"/>
      <add key="diagnosticVerbosityLevel" value="Some" /> 
      <add key="logFilePath" value="IotAgentLog.log"/>
      ```
       **LogFilePath** ustawienie to można zmienić. 

       > [!NOTE]
       > Firma Microsoft zaleca włączenie rejestrowania **poza** po zakończeniu rozwiązywania problemów. Pozostawienie rejestrowania **na** zwiększa dziennika użycie rozmiaru i danych plików.

   1. Uruchom ponownie agenta, uruchamiając:

       `systemctl restart ASCIoTAgent.service`

   1. Wyświetl plik dziennika, aby uzyskać więcej informacji o awarii.  

       Lokalizacja pliku dziennika to: `/var/ASCIoTAgent/IotAgentLog.log`

       Zmień ścieżkę lokalizacji pliku według nazwy, wybrany dla **logFilePath** w kroku 2. 

## <a name="next-steps"></a>Kolejne kroki

- Przeczytaj ASC dla usługi IoT [— omówienie](overview.md)
- Dowiedz się więcej o ASC IoT [architektury](architecture.md)
- Włącz [usługi](quickstart-onboard-iot-hub.md)
- Odczyt [— często zadawane pytania](resources-frequently-asked-questions.md)
- Zrozumienie [alertów](concept-security-alerts.md)