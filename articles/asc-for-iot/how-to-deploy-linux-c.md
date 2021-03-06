---
title: Zainstaluj & wdrożyć agenta Systemu Linux C
description: Dowiedz się, jak zainstalować agenta Usługi Azure Security Center dla IoT zarówno w systemie Linux 32-bitowym, jak i 64-bitowym.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 3ccf2aec-106a-4d2c-8079-5f3e8f2afdcb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: d9f9602a19a266c70b17422e90566f72de2978f6
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311185"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Wdrażanie agenta zabezpieczeń usługi Azure Security Center dla IoT opartego na języku C dla systemu Linux

W tym przewodniku wyjaśniono, jak zainstalować i wdrożyć agenta zabezpieczeń usługi Azure Security Center dla języka C w systemie Linux.

Niniejszy przewodnik zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Instalowanie
> * Weryfikowanie wdrożenia
> * Odinstalowywanie agenta
> * Rozwiązywanie problemów

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać więcej informacji na temat innych platform i smaków agentów, zobacz [Wybieranie odpowiedniego agenta zabezpieczeń](how-to-deploy-agent.md).

1. Aby wdrożyć agenta zabezpieczeń, lokalne prawa administratora są wymagane na komputerze, na którym chcesz zainstalować (sudo).

1. [Utwórz moduł zabezpieczeń](quickstart-create-security-twin.md) dla urządzenia.

## <a name="installation"></a>Instalacja

Aby zainstalować i wdrożyć agenta zabezpieczeń, użyj następującego przepływu pracy:

1. Pobierz najnowszą wersję do swojego komputera z [GitHub](https://aka.ms/iot-security-github-c).

1. Wyodrębnij zawartość pakietu i przejdź do _folderu /src/installation._

1. Dodaj uruchomione uprawnienia do **skryptu InstallSecurityAgent,** uruchamiając następujące polecenie:

   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. Następnie uruchom:

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```

   Aby uzyskać więcej informacji na temat parametrów uwierzytelniania, zobacz [Jak skonfigurować uwierzytelnianie.](concept-security-agent-authentication-methods.md)

Ten skrypt wykonuje następującą funkcję:

1. Instaluje wymagania wstępne.

1. Dodaje użytkownika usługi (z wyłączonym logiem interaktywnym).

1. Instaluje agenta jako **demona** - zakłada, że urządzenie używa **systemd** do zarządzania usługami.

1. Konfiguruje agenta z dostarczonymi parametrami uwierzytelniania.

Aby uzyskać dodatkową pomoc, uruchom skrypt za pomocą parametru –help:

```./InstallSecurityAgent.sh --help```

### <a name="uninstall-the-agent"></a>Odinstalowywanie agenta

Aby odinstalować agenta, uruchom skrypt z parametrem –-uninstall:

```./InstallSecurityAgent.sh -–uninstall```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Sprawdź stan wdrożenia, uruchamiając:

```systemctl status ASCIoTAgent.service```

## <a name="next-steps"></a>Następne kroki

- Przeczytaj [omówienie](overview.md) usługi Azure Security Center dla IoT
- Dowiedz się więcej o usłudze Azure Security Center dla [architektury](architecture.md) IoT
- Włącz [usługę](quickstart-onboard-iot-hub.md)
- Przeczytaj [często](resources-frequently-asked-questions.md) zadawane pytania
- Opis [alertów zabezpieczeń](concept-security-alerts.md)
