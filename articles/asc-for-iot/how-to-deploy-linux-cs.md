---
title: Zainstaluj & wdrożyć agenta Systemu Linux C#
description: Dowiedz się, jak zainstalować agenta Usługi Azure Security Center dla IoT zarówno w systemie Linux 32-bitowym, jak i 64-bitowym.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: b0982203-c3c8-4a0b-8717-5b5ac4038d8c
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2019
ms.author: mlottner
ms.openlocfilehash: 40c6ea91fd84a0f088ed770cd7c4c3ea7b8b1c91
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311141"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Wdrażanie agenta zabezpieczeń usługi Azure Security Center dla IoT opartego na języku C# dla systemu Linux

W tym przewodniku wyjaśniono, jak zainstalować i wdrożyć agenta zabezpieczeń usługi Azure Security Center dla usługi IoT C w systemie Linux.

Niniejszy przewodnik zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Instalowanie
> * Weryfikowanie wdrożenia
> * Odinstalowywanie agenta
> * Rozwiązywanie problemów

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać więcej informacji na temat innych platform i smaków agentów, zobacz [Wybieranie odpowiedniego agenta zabezpieczeń](how-to-deploy-agent.md).

1. Aby wdrożyć agenta zabezpieczeń, lokalne prawa administratora są wymagane na komputerze, na którym chcesz zainstalować.

1. [Utwórz moduł zabezpieczeń](quickstart-create-security-twin.md) dla urządzenia.

## <a name="installation"></a>Instalacja

Aby wdrożyć agenta zabezpieczeń, należy wykonać następujące czynności:

1. Pobierz najnowszą wersję do swojego komputera z [GitHub](https://aka.ms/iot-security-github-cs).

1. Wyodrębnij zawartość pakietu i przejdź do folderu _/Install._

1. Dodaj uruchomione uprawnienia do **skryptu InstallSecurityAgent,** uruchamiając`chmod +x InstallSecurityAgent.sh`

1. Następnie uruchom następujące polecenie z **uprawnieniami głównymi:**

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```

   Aby uzyskać więcej informacji na temat parametrów uwierzytelniania, zobacz [Jak skonfigurować uwierzytelnianie](concept-security-agent-authentication-methods.md).

Ten skrypt wykonuje następujące akcje:

- Instaluje wymagania wstępne.

- Dodaje użytkownika usługi (z wyłączonym logiem interaktywnym).

- Instaluje agenta jako **demona** — zakłada, że urządzenie używa **systemd** dla klasycznego modelu wdrażania.

- Konfiguruje **sudoers,** aby agent do niektórych zadań jako root.

- Konfiguruje agenta z podanymi parametrami uwierzytelniania.

Aby uzyskać dodatkową pomoc, uruchom skrypt za pomocą parametru –help:`./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>Odinstalowywanie agenta

Aby odinstalować agenta, uruchom skrypt `./InstallSecurityAgent.sh -u`z parametrem –u: .

> [!NOTE]
> Odinstaluj nie usuwa brakujących wymagań wstępnych, które zostały zainstalowane podczas instalacji.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

1. Sprawdź stan wdrożenia, uruchamiając:

    `systemctl status ASCIoTAgent.service`

1. Włącz rejestrowanie.
   Jeśli agent nie uruchamia się, włącz rejestrowanie, aby uzyskać więcej informacji.

   Włącz rejestrowanie, korzystając z:

   1. Otwórz plik konfiguracyjny do edycji w dowolnym edytorze Linuksa:

        `vi /var/ASCIoTAgent/General.config`

   1. Edytuj następujące wartości:

      ```
      <add key="logLevel" value="Debug"/>
      <add key="fileLogLevel" value="Debug"/>
      <add key="diagnosticVerbosityLevel" value="Some" />
      <add key="logFilePath" value="IotAgentLog.log"/>
      ```

       Wartość **logFilePath** jest konfigurowalna.

       > [!NOTE]
       > Zalecamy wyłączenie **off** wylogowania po zakończeniu rozwiązywania problemów. Pozostawienie **on** logowania zwiększa rozmiar pliku dziennika i użycie danych.

   1. Uruchom ponownie agenta, uruchamiając:

       `systemctl restart ASCIoTAgent.service`

   1. Wyświetl plik dziennika, aby uzyskać więcej informacji na temat błędu.

       Lokalizacja pliku dziennika to:`/var/ASCIoTAgent/IotAgentLog.log`

       Zmień ścieżkę lokalizacji pliku zgodnie z nazwą wybraną dla **logFilePath** w kroku 2.

## <a name="next-steps"></a>Następne kroki

- Przeczytaj [omówienie](overview.md) usługi Azure Security Center dla IoT
- Dowiedz się więcej o usłudze Azure Security Center dla [architektury](architecture.md) IoT
- Włącz [usługę](quickstart-onboard-iot-hub.md)
- Przeczytaj [często](resources-frequently-asked-questions.md) zadawane pytania
- Opis [alertów](concept-security-alerts.md)
