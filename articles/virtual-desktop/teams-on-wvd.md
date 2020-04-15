---
title: Microsoft Teams na pulpicie wirtualnym systemu Windows — platforma Azure
description: Jak korzystać z usługi Microsoft Teams na pulpicie wirtualnym systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 15a4c9b16b102310fd02f8db3a4fb93cff84882b
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314068"
---
# <a name="use-microsoft-teams-on-windows-virtual-desktop"></a>Korzystanie z usługi Microsoft Teams na pulpicie wirtualnym systemu Windows

> Dotyczy: Windows 10 i Windows 10 IoT Enterprise

Środowiska zwirtualizowane stanowią unikatowy zestaw wyzwań dla aplikacji do współpracy, takich jak Microsoft Teams, w tym zwiększone opóźnienia, wysokie użycie procesora CPU hosta i słaba ogólna wydajność audio i wideo. Aby dowiedzieć się więcej na temat korzystania z usługi Microsoft Teams w środowiskach VDI, zapoznaj się z [programem Teams for Virtualized Desktop Infrastructure](https://docs.microsoft.com/microsoftteams/teams-for-vdi).

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było korzystać z usługi Microsoft Teams na pulpicie wirtualnym systemu Windows, należy wykonać następujące czynności:

- Zainstaluj [klienta pulpitu systemu Windows](connect-windows-7-and-10.md) na urządzeniu z systemem Windows 10, które spełnia wymagania [sprzętowe usługi](https://docs.microsoft.com/microsoftteams/hardware-requirements-for-the-teams-app)Microsoft Teams .
- Połącz się z wielosesyjną maszyną windows 10 lub windows 10 enterprise (VM).
- [Przygotuj swoją sieć](https://docs.microsoft.com/microsoftteams/prepare-network) do usługi Microsoft Teams.

## <a name="use-unoptimized-microsoft-teams"></a>Korzystanie z nieoptymizowanego programu Microsoft Teams

Nieoptymizowane usługi Microsoft Teams można używać w środowiskach pulpitu wirtualnego systemu Windows, aby korzystać z funkcji pełnego czatu i współpracy w usłudze Microsoft Teams, a także połączeń audio. Jakość dźwięku w połączeniach będzie się różnić w zależności od konfiguracji hosta, ponieważ nieoptymalizowane wywołania zużywają więcej procesora hosta.

### <a name="prepare-your-image-for-teams"></a>Przygotowanie obrazu do pracy w zespołach

Aby włączyć instalację usługi Teams na komputerze, ustaw następujący klucz rejestru na hoście:

```shell
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Teams\IsWVDEnvironment]
  Type: REG_DWORD
  Value: 1
```

### <a name="install-microsoft-teams"></a>Instalowanie usługi Microsoft Teams

Aplikację klasyczną Teams można wdrożyć przy użyciu instalacji na komputerze. Aby zainstalować usługę Microsoft Teams w środowisku pulpitu wirtualnego systemu Windows:

1. Pobierz [pakiet MSI teams,](https://docs.microsoft.com/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm) który pasuje do Twojego środowiska. Zalecamy użycie instalatora 64-bitowego w 64-bitowym systemie operacyjnym.
2. Uruchom to polecenie, aby zainstalować msi na maszynie wirtualnej hosta.

      ```shell
      msiexec /i <msi_name> /l*v < install_logfile_name> ALLUSER=1
      ```

      Spowoduje to zainstalowanie aplikacji Teams na pliki programów lub pliki programów (x86). Przy następnym logowanie i uruchamianie usługi Teams aplikacja poprosi o podanie poświadczeń.

      > [!NOTE]
      > Użytkownicy i administratorzy nie mogą wyłączyć automatycznego uruchamiania dla zespołów podczas logowania w tej chwili.

      Aby odinstalować msi z maszyny Wirtualnej hosta, uruchom to polecenie:

      ```shell
      msiexec /passive /x <msi_name> /l*v <uninstall_logfile_name>
      ```

      > [!NOTE]
      > Jeśli zainstalujesz teams z ustawieniem MSI ALLUSER=1, automatyczne aktualizacje zostaną wyłączone. Zalecamy, aby aktualizować zespoły co najmniej raz w miesiącu.
      
### <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Dostosowywanie właściwości protokołu pulpitu zdalnego dla puli hostów
Dostosowywanie właściwości protokołu RDP (Remote Desktop Protocol) puli hostów, takich jak obsługa wielu monitorów, włączanie przekierowywania mikrofonu i dźwięku, umożliwia zapewnienie optymalnego środowiska dla użytkowników w zależności od ich potrzeb. Właściwości protokołu RDP można dostosować na pulpicie wirtualnym systemu Windows przy użyciu parametru **-CustomRdpProperty** w pokręcie cmdlet **Set-RdsHostPool.**
Pełna lista obsługiwanych właściwości i ich wartości domyślnych można znaleźć w [obsługiwanych ustawieniach pliku RDP.](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context)
