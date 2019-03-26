---
title: Przygotowywanie maszyn źródłowych, aby zainstalować usługi mobilności za pomocą instalacji wypychanej na potrzeby odzyskiwania po awarii maszyn wirtualnych programu VMware i serwerów fizycznych na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przygotować serwer do instalacji agenta mobilności za pomocą instalacji wypychanej na potrzeby odzyskiwania po awarii maszyn wirtualnych programu VMware i serwerów fizycznych do platformy Azure przy użyciu usługi Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 628be573d03d42ec62a358071074facfe228852d
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58418673"
---
# <a name="prepare-source-machine-for-push-installation-of-mobility-agent"></a>Przygotowanie maszyny źródłowej dotyczące instalacji wypychanej agenta mobilności

Podczas konfigurowania odzyskiwania po awarii dla maszyn wirtualnych VMware i serwerów fizycznych, za pomocą [usługi Azure Site Recovery](site-recovery-overview.md), należy zainstalować [usługi Site Recovery Mobility](vmware-physical-mobility-service-overview.md) na każdej maszyny Wirtualnej VMware w środowisku lokalnym i serwera fizycznego.  Usługa mobilności służy do przechwytywania zapisów danych na maszynie i przekazuje je do serwera przetwarzania Site Recovery.

## <a name="install-on-windows-machine"></a>Zainstaluj na maszynie Windows

Na każdej maszynie, Windows, dla których chcesz chronić, wykonaj następujące czynności:

1. Upewnij się, że istnieje połączenie sieciowe między maszyną i serwerem przetwarzania. Jeśli nie skonfigurowano oddzielnego serwera przetwarzania, następnie domyślnie działa na serwerze konfiguracji.
1. Utwórz konto, za pomocą którego serwer przetwarzania będzie mógł uzyskać dostęp do komputera. Konto powinno mieć uprawnień administratora lokalnego lub domeny. Tylko w przypadku instalacji wypychanej i aktualizacji agenta, należy używać tego konta.
2. Jeśli nie używasz konta domeny, wyłącz kontrolę dostępu użytkowników zdalnych na komputerze lokalnym w następujący sposób:
    - W kluczu rejestru HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System Dodaj nową wartość typu DWORD: **LocalAccountTokenFilterPolicy**. Ustaw wartość **1**.
    -  Aby to zrobić w wierszu polecenia, uruchom następujące polecenie:  
   "Rejestru HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System Dodaj /v LocalAccountTokenFilterPolicy /t REG_DWORD /d
3. W Zaporze Windows na komputerze, który chcesz chronić, wybierz **Zezwalaj aplikacji lub funkcji za pośrednictwem zapory**. Włącz **udostępnianie plików i drukarek** i **Instrumentacji zarządzania Windows (WMI)**. Dla komputerów, które należą do domeny można skonfigurować ustawienia zapory przy użyciu obiektu zasad grupy (GPO).

   ![Ustawienia zapory](./media/vmware-azure-install-mobility-service/mobility1.png)

4. Dodaj konto utworzone w narzędziu CSPSConfigtool. Aby to zrobić, należy zalogować się do serwera konfiguracji.
5. Otwórz plik **cspsconfigtool.exe**. Jest on dostępny jako skrót na pulpicie i w folderze %ProgramData%\home\svsystems\bin.
6. Na **Zarządzanie kontami** zaznacz **Dodaj konto**.
7. Dodaj utworzone konto.
8. Wprowadź używane poświadczenia po włączeniu replikacji dla komputera.

## <a name="install-on-linux-machine"></a>Instalowanie na maszynie z systemem Linux

Na każdym komputerze systemu Linux, które mają być chronione wykonaj następujące czynności:

1. Upewnij się, że istnieje połączenie sieciowe między maszyny z systemem Linux a serwerem przetwarzania.
2. Utwórz konto, za pomocą którego serwer przetwarzania będzie mógł uzyskać dostęp do komputera. Konto powinno być użytkownikiem **root** na źródłowym serwerze z systemem Linux. Tylko w przypadku instalacji wypychanej i aktualizacje, należy używać tego konta.
3. Sprawdź, czy plik /etc/hosts na źródłowym serwerze z systemem Linux zawiera wpisy mapujące lokalną nazwę hosta na adres IP skojarzony ze wszystkimi kartami sieciowymi.
4. Zainstaluj najnowsze pakiety openssh, openssh-server i openssl na komputerze, który chcesz replikować.
5. Upewnij się, że protokół Secure Shell (SSH) jest włączony i uruchomiony na porcie 22.
4. Włącz podsystem SFTP i hasło uwierzytelnianie w pliku sshd_config. Aby to zrobić, zaloguj się jako **głównego**.
5. W **/etc/ssh/sshd_config** plików, znajdź wiersz, który rozpoczyna się od **PasswordAuthentication**.
6. Usuń znaczniki komentarza i zmień wartość na **tak**.
7. Znajdź wiersz, który rozpoczyna się od **podsystemu**, i usuń znaczniki komentarza.

      ![Linux](./media/vmware-azure-install-mobility-service/mobility2.png)

8. Uruchom ponownie usługę **sshd**.
9. Dodaj konto utworzone w narzędziu CSPSConfigtool. Aby to zrobić, należy zalogować się do serwera konfiguracji.
10. Otwórz plik **cspsconfigtool.exe**. Jest on dostępny jako skrót na pulpicie i w folderze %ProgramData%\home\svsystems\bin.
11. Na **Zarządzanie kontami** zaznacz **Dodaj konto**.
12. Dodaj utworzone konto.
13. Wprowadź używane poświadczenia po włączeniu replikacji dla komputera.

## <a name="anti-virus-on-replicated-machines"></a>Oprogramowanie antywirusowe na replikowanych maszyn

Jeśli maszyn, które mają być replikowane active oprogramowanie antywirusowe uruchomione, upewnij się, folder instalacji usługi mobilności można wykluczyć z ochrony antywirusowej operacji (*C:\ProgramData\ASR\agent*). Gwarantuje to, że replikacja działa zgodnie z oczekiwaniami.

## <a name="next-steps"></a>Kolejne kroki

Po zainstalowaniu usługi mobilności w witrynie Azure portal wybierz **+ Replikuj** aby rozpocząć ochronę tych maszyn wirtualnych. Dowiedz się więcej na temat włączania replikacji [VMware VMs(vmware-azure-enable-replication.md) i [serwerów fizycznych](physical-azure-disaster-recovery.md#enable-replication).


