---
title: Przygotowanie maszyn źródłowych do zainstalowania usługi mobilności za pośrednictwem instalacji wypychanej w celu odzyskiwania po awarii maszyn wirtualnych i serwerów fizycznych na platformie Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak przygotować serwer do zainstalowania agenta mobilności za pośrednictwem instalacji wypychanej w celu odzyskiwania po awarii maszyn wirtualnych vmware i serwerów fizycznych na platformie Azure przy użyciu usługi Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: a2f4bdb96b8d1ecb23ddcec844726439ec46fff2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74186450"
---
# <a name="prepare-source-machine-for-push-installation-of-mobility-agent"></a>Przygotowanie maszyny źródłowej do instalacji push agenta mobilności

Po skonfigurowaniu odzyskiwania po awarii dla maszyn wirtualnych VMware i serwerów fizycznych przy użyciu [usługi Azure Site Recovery](site-recovery-overview.md)należy zainstalować usługę mobilności odzyskiwania [witryny](vmware-physical-mobility-service-overview.md) na każdej lokalnej maszynie wirtualnej VMware i na serwerze fizycznym.  Usługa mobilności przechwytuje zapisy danych na komputerze i przekazuje je do serwera przetwarzania odzyskiwania lokacji.

## <a name="install-on-windows-machine"></a>Instalacja na komputerze z systemem Windows

Na każdym komputerze z systemem Windows, który chcesz chronić, wykonaj następujące czynności:

1. Upewnij się, że istnieje łączność sieciowa między komputerem a serwerem przetwarzania. Jeśli nie masz skonfigurowanego oddzielnego serwera przetwarzania, domyślnie jest on uruchomiony na serwerze konfiguracji.
1. Utwórz konto, za pomocą którego serwer przetwarzania będzie mógł uzyskać dostęp do komputera. Konto powinno mieć prawa administratora, lokalne lub domenowe. Tego konta należy używać tylko do instalacji wypychanej i aktualizacji agenta.
2. Jeśli nie używasz konta domeny, wyłącz kontrolę dostępu użytkownika zdalnego na komputerze lokalnym w następujący sposób:
    - W obszarze HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System registry key, add a new DWORD: **LocalAccountTokenFilterPolicy**. Ustaw wartość **na 1**.
    -  Aby to zrobić w wierszu polecenia, uruchom następujące polecenie:  
   'REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d
3. W Zaporze systemu Windows na komputerze, który chcesz chronić, wybierz pozycję **Zezwalaj na aplikację lub funkcję za pośrednictwem Zapory**. Włącz **udostępnianie plików i drukarek** oraz **instrumentację zarządzania windowsem (WMI)**. W przypadku komputerów należących do domeny można skonfigurować ustawienia zapory przy użyciu obiektu zasad grupy (GPO).

   ![Ustawienia zapory](./media/vmware-azure-install-mobility-service/mobility1.png)

4. Dodaj konto utworzone w narzędziu CSPSConfigtool. Aby to zrobić, zaloguj się do serwera konfiguracji.
5. Otwórz plik **cspsconfigtool.exe**. Jest dostępny jako skrót na pulpicie i w folderze %ProgramData%\ASR\home\svsystems\bin.
6. Na karcie **Zarządzanie kontami** wybierz pozycję **Dodaj konto**.
7. Dodaj utworzone konto.
8. Wprowadź używane poświadczenia po włączeniu replikacji dla komputera.

## <a name="install-on-linux-machine"></a>Instalacja na komputerze z systemem Linux

Na każdym komputerze z systemem Linux, który chcesz chronić, wykonaj następujące czynności:

1. Upewnij się, że istnieje łączność sieciowa między komputerem z systemem Linux a serwerem przetwarzania.
2. Utwórz konto, za pomocą którego serwer przetwarzania będzie mógł uzyskać dostęp do komputera. Konto powinno być użytkownikiem **root** na źródłowym serwerze z systemem Linux. Użyj tego konta tylko do instalacji wypychanej i aktualizacji.
3. Sprawdź, czy plik /etc/hosts na źródłowym serwerze z systemem Linux zawiera wpisy mapujące lokalną nazwę hosta na adres IP skojarzony ze wszystkimi kartami sieciowymi.
4. Zainstaluj najnowsze pakiety openssh, openssh-server i openssl na komputerze, który chcesz replikować.
5. Upewnij się, że protokół Secure Shell (SSH) jest włączony i uruchomiony na porcie 22.
4. Włącz podsystem SFTP i uwierzytelnianie hasłem w pliku sshd_config. Aby to zrobić, zaloguj się jako **root**.
5. W pliku **/etc/ssh/sshd_config** znajdź wiersz, który zaczyna się od **PasswordAuthentication**.
6. Odkomentuj wiersz i zmień wartość na **tak**.
7. Znajdź wiersz, który zaczyna się od **podsystemu**i odkomentuj wiersz.

      ![Linux](./media/vmware-azure-install-mobility-service/mobility2.png)

8. Uruchom ponownie usługę **sshd**.
9. Dodaj konto utworzone w narzędziu CSPSConfigtool. Aby to zrobić, zaloguj się do serwera konfiguracji.
10. Otwórz plik **cspsconfigtool.exe**. Jest on dostępny jako skrót na pulpicie i w folderze %ProgramData%\home\svsystems\bin.
11. Na karcie **Zarządzanie kontami** wybierz pozycję **Dodaj konto**.
12. Dodaj utworzone konto.
13. Wprowadź używane poświadczenia po włączeniu replikacji dla komputera.

## <a name="anti-virus-on-replicated-machines"></a>Ochrona antywirusowa na replikowanych maszynach

Jeśli maszyny, które chcesz replikować, mają uruchomione aktywne oprogramowanie antywirusowe, upewnij się, że folder instalacyjny usługi mobilności został wykluczony z operacji antywirusowych (*C:\ProgramData\ASR\agent*). Gwarantuje to, że replikacja działa zgodnie z oczekiwaniami.

## <a name="next-steps"></a>Następne kroki

Po zainstalowaniu usługi mobilności w witrynie Azure portal wybierz **+ Replikuj,** aby rozpocząć ochronę tych maszyn wirtualnych. Dowiedz się więcej o włączaniu replikacji maszyn [wirtualnych VMware](vmware-azure-enable-replication.md) i [serwerów fizycznych.](physical-azure-disaster-recovery.md#enable-replication)


