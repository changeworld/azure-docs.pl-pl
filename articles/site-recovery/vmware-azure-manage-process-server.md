---
title: Zarządzanie serwerem procesu w usłudze Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano zarządzanie serwera przetwarzania, ustaw dla maszyny Wirtualnej VMware i replikacji serwera fizycznego w usłudze Azure Site Recovery.
author: AnoopVasudavan
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: anoopkv
ms.openlocfilehash: d1f880a5ec9f0343891999ef3bad11279cb0cfe6
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36285534"
---
# <a name="manage-process-servers"></a>Zarządzanie serwerami przetwarzania

Domyślnie serwer przetwarzania używany w przypadku replikacji maszyn wirtualnych VMware lub serwerów fizycznych do platformy Azure jest instalowany na komputerze z serwerem konfiguracji lokalnej. Istnieje kilka wystąpień, w których należy skonfigurować oddzielnego serwera przetwarzania:

- W przypadku dużych wdrożeń mogą wymagać dodatkowych lokalnych serwerów przetwarzania w celu skalowania wydajności.
- W przypadku powrotu po awarii, należy tymczasowej serwer przetwarzania na platformie Azure. Po zakończeniu powrotu po awarii można usunąć tej maszyny Wirtualnej. 

Ten artykuł zawiera podsumowanie typowych zadań zarządzania na tych serwerach dodatkowych procesów.

## <a name="upgrade-a-process-server"></a>Uaktualnienie serwera przetwarzania

Uaktualnij serwer przetwarzania uruchomiony lokalnie lub na platformie Azure (na potrzeby powrotu po awarii) w następujący sposób:

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]

> [!NOTE]
  Zwykle gdy obraz galerii Azure umożliwia tworzenie serwera przetwarzania na platformie Azure na potrzeby powrotu po awarii, działa z najnowszą dostępną wersją. Site Recovery zespoły wersji poprawki i ulepszenia regularnie, a firma Microsoft zaleca się, że serwery procesu zapewnienia aktualności.



## <a name="reregister-a-process-server"></a>Zarejestruj ponownie serwer przetwarzania

Jeśli chcesz ponownie zarejestrować serwer przetwarzania, uruchamiane lokalnie lub na platformie Azure, z serwerem konfiguracji, wykonaj następujące czynności:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

Po zapisaniu ustawień, wykonaj następujące czynności:

1. Na serwerze przetwarzania Otwórz wiersz polecenia administratora.
2. Przejdź do folderu **%PROGRAMDATA%\ASR\Agent**, i uruchom polecenie:

    ```
    cdpcli.exe --registermt
    net stop obengine
    net start obengine
    ```

## <a name="modify-proxy-settings-for-an-on-premises-process-server"></a>Zmodyfikuj ustawienia serwera proxy dla serwera przetwarzania lokalnego

Jeśli serwer przetwarzania używa serwera proxy, aby nawiązać połączenie usługi Site Recovery na platformie Azure, użyj tej procedury, jeśli trzeba zmodyfikować istniejących ustawień serwera proxy.

1. Zaloguj się na komputerze serwera procesu. 
2. Otwórz okno poleceń programu PowerShell administratora i uruchom następujące polecenie:
  ```
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```
2. Przejdź do folderu **%PROGRAMDATA%\ASR\Agent**, i uruchom następujące polecenie:
  ```
  cmd
  cdpcli.exe --registermt

  net stop obengine

  net start obengine

  exit
  ```


## <a name="remove-a-process-server"></a>Usuń serwer przetwarzania

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]


