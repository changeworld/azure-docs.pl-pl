---
title: Samouczek dotyczący instalacji i wdrażania agenta systemu Linux C ASC agenta IoT (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zainstalować usługi ASC IoT agenta w systemie Linux zarówno 32-bitowych i 64-bitowych.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 3ccf2aec-106a-4d2c-8079-5f3e8f2afdcb
ms.service: ascforiot
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 04b41791915946820c9c7a1666ab10e880731e4b
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541588"
---
# <a name="tutorial-deploy-asc-for-iot-c-based-security-agent-for-linux"></a>Samouczek: Wdrażanie usługi ASC zabezpieczenia oparty na IoT C agenta dla systemu Linux

> [!IMPORTANT]
> ASC IoT jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym samouczku wyjaśniono, jak zainstalować i wdrożyć usługi ASC zabezpieczenia oparty na IoT C agenta w systemie Linux.

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

Aby zainstalować i wdrożyć agenta zabezpieczeń, wykonaj następujące czynności:

1. Pobierz najnowszą wersją na swoim komputerze z poziomu [Github](https://aka.ms/iot-security-github-cs).

1. Wyodrębnij zawartość pakietu, a następnie przejdź do _/installation_ folderu.

1. Dodaj uruchomionej uprawnienia do **skryptu InstallSecurityAgent** wykonując następujące czynności:
    
    `chmod +x InstallSecurityAgent.sh` 

1. Następnie uruchom: 

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```
   
   Zobacz [jak skonfigurować uwierzytelnianie](concept-security-agent-authentication-methods.md) Aby uzyskać więcej informacji na temat parametrów uwierzytelniania.

Ten skrypt wykonuje następujące czynności:

1. Instaluje wstępnie wymagane składniki.

2. Dodaje użytkownika usługi (za pomocą logowania interaktywnego wyłączone).

3. Instaluje agenta jako **demona** -przyjęto założenie, urządzenie używa **systemd** do zarządzania usługami.

4. Konfiguruje **sudo** zezwolić agentowi wykonywanie określonych zadań jako katalog główny.

5. Konfiguruje agenta z podanymi parametrami uwierzytelniania. 

Aby uzyskać dodatkową pomoc, uruchom skrypt przy użyciu parametru – pomocy: 
    
    `./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>Odinstaluj agenta

Aby odinstalować agenta, uruchom skrypt przy użyciu parametru – u:

    `./InstallSecurityAgent.sh -u`. 

```
 .\InstallSecurityAgent.sh –uninstall / -u
``` 

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Sprawdź stan wdrożenia, uruchamiając:

```
systemctl status ASCIoTAgent.service
```


## <a name="next-steps"></a>Kolejne kroki
- Przeczytaj ASC dla usługi IoT [— omówienie](overview.md)
- Dowiedz się więcej o ASC IoT [architektury](architecture.md)
- Włącz [usługi](quickstart-onboard-iot-hub.md)
- Odczyt [— często zadawane pytania](resources-frequently-asked-questions.md)
- Zrozumienie [alerty zabezpieczeń](concept-security-alerts.md)