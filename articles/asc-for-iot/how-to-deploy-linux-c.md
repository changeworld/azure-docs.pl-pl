---
title: Przewodnik instalacji i wdrażania agenta systemu Linux C usługi Azure Security Center dla agenta IoT (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zainstalować Centrum zabezpieczeń Azure dla IoT agenta w systemie Linux zarówno 32-bitowych i 64-bitowych.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 3ccf2aec-106a-4d2c-8079-5f3e8f2afdcb
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2019
ms.author: mlottner
ms.openlocfilehash: 49ebb5932e1e918330625fd0df98811873dd5cd5
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65200654"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Wdrażanie usługi Azure Security Center dla zabezpieczenia oparty na IoT C agenta dla systemu Linux

> [!IMPORTANT]
> Centrum zabezpieczeń Azure dla IoT jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ten przewodnik wyjaśnia, jak zainstalować i wdrożyć usługi Azure Security Center (ASC) zabezpieczenia oparty na IoT C agenta w systemie Linux.

Niniejszy przewodnik zawiera informacje na temat wykonywania następujących czynności: 
> [!div class="checklist"]
> * Instalowanie
> * Weryfikowanie wdrożenia
> * Odinstaluj agenta
> * Rozwiązywanie problemów 

## <a name="prerequisites"></a>Wymagania wstępne

Dla innych platform i odmian agenta, zobacz [wybierz agenta prawa zabezpieczeń](how-to-deploy-agent.md).

1. Aby wdrożyć agenta zabezpieczeń, na maszynie którą chcesz zainstalować ("sudo") są wymagane uprawnienia administratora lokalnego.

1. [Utwórz moduł zabezpieczeń](quickstart-create-security-twin.md) dla tego urządzenia.

## <a name="installation"></a>Instalacja 

Aby zainstalować i wdrożyć agenta zabezpieczeń, wykonaj następujące czynności:


1. Pobierz najnowszą wersją na swoim komputerze z poziomu [Github](https://aka.ms/iot-security-github-c).

1. Wyodrębnij zawartość pakietu, a następnie przejdź do _/Install_ folderu.

1. Dodaj uruchomionej uprawnienia do **skryptu InstallSecurityAgent** wykonując następujące czynności:
    
   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. Następnie uruchom: 

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```
   
   Zobacz [jak skonfigurować uwierzytelnianie](concept-security-agent-authentication-methods.md) Aby uzyskać więcej informacji na temat parametrów uwierzytelniania.

Ten skrypt wykonuje następujące czynności:

1. Instaluje wstępnie wymagane składniki.

2. Dodaje użytkownika usługi (za pomocą logowania interaktywnego wyłączone).

3. Instaluje agenta jako **demona** -przyjęto założenie, urządzenie używa **systemd** do zarządzania usługami.

4. Konfiguruje agenta z podanymi parametrami uwierzytelniania. 

Aby uzyskać dodatkową pomoc, uruchom skrypt przy użyciu parametru – pomocy: 
    
    ./InstallSecurityAgent.sh --help

### <a name="uninstall-the-agent"></a>Odinstaluj agenta

Aby odinstalować agenta, uruchom skrypt za pomocą — Odinstaluj parametru:

    ./InstallSecurityAgent.sh -–uninstall

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Sprawdź stan wdrożenia, uruchamiając:

    systemctl status ASCIoTAgent.service


## <a name="next-steps"></a>Kolejne kroki
- Przeczytaj ASC dla usługi IoT [— omówienie](overview.md)
- Dowiedz się więcej o ASC IoT [architektury](architecture.md)
- Włącz [usługi](quickstart-onboard-iot-hub.md)
- Odczyt [— często zadawane pytania](resources-frequently-asked-questions.md)
- Zrozumienie [alerty zabezpieczeń](concept-security-alerts.md)