---
title: Przewodnik instalacji i wdrażania agenta systemu Linux C ASC agenta IoT (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zainstalować usługi ASC IoT agenta w systemie Linux zarówno 32-bitowych i 64-bitowych.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 3ccf2aec-106a-4d2c-8079-5f3e8f2afdcb
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2019
ms.author: mlottner
ms.openlocfilehash: 2b18a09e5b79e7b3d3ea837e937397ac92491f9f
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58619853"
---
# <a name="deploy-asc-for-iot-c-based-security-agent-for-linux"></a>Wdrażanie usługi ASC zabezpieczenia oparty na IoT C agenta dla systemu Linux

> [!IMPORTANT]
> ASC IoT jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ten przewodnik wyjaśnia, jak zainstalować i wdrożyć usługi ASC zabezpieczenia oparty na IoT C agenta w systemie Linux.

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