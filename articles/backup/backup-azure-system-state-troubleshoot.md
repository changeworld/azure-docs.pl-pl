---
title: Rozwiązywanie problemów z tworzeniem kopii zapasowej stanu systemu za pomocą Azure Backup
description: W tym artykule dowiesz się, jak rozwiązywać problemy z tworzeniem kopii zapasowej stanu systemu dla lokalnych serwerów z systemem Windows.
ms.reviewer: srinathv
author: dcurwin
manager: carmonm
keywords: Jak utworzyć kopię zapasową; stan systemu kopii zapasowej
ms.service: backup
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: dacurwin
ms.openlocfilehash: 71a2b73ab3570539a566f708ea8b1a41963d4e81
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747310"
---
# <a name="troubleshoot-system-state-backup"></a>Rozwiązywanie problemów z kopią zapasową stanu systemu

W tym artykule opisano rozwiązania problemów, które mogą wystąpić podczas korzystania z kopii zapasowej stanu systemu.

## <a name="basic-troubleshooting"></a>Podstawowe rozwiązywanie problemów

Przed rozpoczęciem rozwiązywania problemów z kopiami zapasowymi stanu systemu zalecamy wykonanie poniższej weryfikacji:

- [Upewnij się, że agent Microsoft Azure Recovery Services (MARS) jest aktualny](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Upewnij się, że istnieje połączenie sieciowe między agentem MARS i platformą Azure](https://aka.ms/AB-A4dp50)
- Upewnij się, że usługa Microsoft Azure Recovery Services jest uruchomiona (w konsoli usługi). W razie potrzeby uruchom ponownie, a następnie spróbuj ponownie wykonać operację.
- [Upewnij się, że 5–10% wolnego miejsca na woluminie jest dostępne na potrzeby lokalizacji folderu plików tymczasowych](https://aka.ms/AB-AA4dwtt)
- [Sprawdź, czy inny proces lub oprogramowanie antywirusowe nie zakłóca działania usługi Azure Backup](https://aka.ms/AB-AA4dwtk)
- [Zaplanowane tworzenie kopii zapasowej kończy się niepowodzeniem, ale ręczne tworzenie kopii zapasowej działa](https://aka.ms/ScheduledBackupFailManualWorks)
- Upewnij się, że w systemie operacyjnym zainstalowano najnowsze aktualizacje
- [Upewnij się, że nieobsługiwane dyski i pliki z nieobsługiwanymi atrybutami są wyłączone z kopii zapasowej](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- Upewnij się, że **zegar systemowy** chronionego systemu ma skonfigurowaną prawidłową strefę czasową <br>
- [Upewnij się, że na serwerze zainstalowano program .NET Framework w wersji 4.5.2 lub nowszej](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- Jeśli próbujesz **ponownie zarejestrować serwer** w magazynie, wykonaj następujące czynności: <br>
  - Upewnij się, że agent został odinstalowany z serwera oraz usunięty z portalu <br>
  - Użyj tego samego hasła, które zostało początkowo użyte podczas rejestrowania serwera <br>
- W przypadku tworzenia kopii zapasowej offline upewnij się, że Azure PowerShell wersji 3.7.0 jest zainstalowana na komputerze źródłowym i skopiuj przed rozpoczęciem operacji tworzenia kopii zapasowej w trybie offline
- [Uwzględnianie, gdy Agent kopii zapasowej jest uruchomiony na maszynie wirtualnej platformy Azure](https://aka.ms/AB-AA4dwtr)

### <a name="limitation"></a>Ograniczenia

- Odzyskiwanie na inny komputer przy użyciu funkcji odzyskiwania stanu systemu nie jest zalecane przez firmę Microsoft.
- Kopia zapasowa stanu systemu obsługuje obecnie serwery z systemem Windows "lokalne", ale ta funkcja nie jest dostępna dla maszyn wirtualnych platformy Azure.

## <a name="pre-requisite"></a>Wymagania wstępne

Przed rozpoczęciem rozwiązywania problemów z wykonywaniem kopii zapasowych stanu systemu za pomocą Azure Backup wykonaj poniższe sprawdzanie wymagań wstępnych.  

### <a name="verify-windows-server-backup-is-installed"></a>Sprawdź, czy Kopia zapasowa systemu Windows Server jest zainstalowana

Upewnij się, że na serwerze jest zainstalowana i włączona Kopia zapasowa systemu Windows Server. Aby sprawdzić stan instalacji, uruchom następujące polecenie programu PowerShell:

 ```powershell
Get-WindowsFeature Windows-Server-Backup
 ```

Jeśli w danych wyjściowych zostanie wyświetlony **stan instalacji** **dostępny**, oznacza to, że funkcja kopia zapasowa systemu Windows Server jest dostępna dla instalacji, ale nie jest zainstalowana na serwerze. Jeśli jednak Kopia zapasowa systemu Windows Server nie jest zainstalowana, użyj jednej z poniższych metod, aby ją zainstalować.

#### <a name="method-1-install-windows-server-backup-using-powershell"></a>Metoda 1. Instalowanie Kopia zapasowa systemu Windows Server przy użyciu programu PowerShell

Aby zainstalować Kopia zapasowa systemu Windows Server przy użyciu programu PowerShell, uruchom następujące polecenie:

  ```powershell
  Install-WindowsFeature -Name Windows-Server-Backup
  ```

#### <a name="method-2-install-windows-server-backup-using-server-manager"></a>Metoda 2. Instalowanie Kopia zapasowa systemu Windows Server przy użyciu Menedżer serwera

Aby zainstalować Kopia zapasowa systemu Windows Server przy użyciu Menedżer serwera, wykonaj poniższe czynności:

1. W oknie **Menedżer serwera**kliknij pozycję **Dodaj role i funkcje**. Zostanie wyświetlony **Kreator dodawania ról i funkcji** .

    ![Pulpit nawigacyjny](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. Wybierz **Typ instalacji** i kliknij przycisk **dalej**.

    ![Typ instalacji](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. Wybierz serwer z puli serwerów, a następnie kliknij przycisk **dalej**. W roli serwera pozostaw wybór domyślny i kliknij przycisk **dalej**.
4. Wybierz pozycję **kopia zapasowa systemu Windows Server** na karcie **funkcje** i kliknij przycisk **dalej**.

    ![danych](./media/backup-azure-system-state-troubleshoot/features.png)

5. Na karcie **potwierdzenie** kliknij przycisk **Zainstaluj** , aby rozpocząć proces instalacji.
6. Na karcie **wyniki** zostanie wyświetlona funkcja kopia zapasowa systemu Windows Server pomyślnie zainstalowana na serwerze z systemem Windows.

    ![wynika](./media/backup-azure-system-state-troubleshoot/results.jpg)

### <a name="system-volume-information-permission"></a>Uprawnienie do informacji o woluminie systemowym

Upewnij się, że SYSTEM lokalny ma pełną kontrolę nad folderem **informacji o woluminie systemowym** znajdującym się w woluminie, na którym zainstalowano system Windows. Zwykle jest to **C:\System informacje o woluminie**. Kopia zapasowa systemu Windows Server może zakończyć się niepowodzeniem, jeśli powyższe uprawnienia nie są ustawione prawidłowo

### <a name="dependent-services"></a>Usługi zależne

Upewnij się, że poniższe usługi są w stanie uruchomienia:

**Nazwa usługi** | **Typ uruchomienia**
--- | ---
Zdalne wywołanie procedury (RPC) | Automatyczny
System zdarzeń modelu COM+ (EventSystem) | Automatyczny
Usługa powiadamiania o zdarzeniach systemowych (SENS) | Automatyczny
Kopiowanie woluminów w tle (VSS) | Ręczna
Dostawca kopiowania oprogramowania w tle firmy Microsoft (SWPRV) | Ręczna

### <a name="validate-windows-server-backup-status"></a>Weryfikuj stan Kopia zapasowa systemu Windows Server

Aby sprawdzić stan Kopia zapasowa systemu Windows Server, wykonaj poniższe czynności:

- Upewnij się, że program WSB PowerShell jest uruchomiony

  - Uruchom `Get-WBJob` z poziomu programu PowerShell z podwyższonym poziomem uprawnień i upewnij się, że nie zwróci następującego błędu:

    > [!WARNING]
    > Get-WBJob: termin "Get-WBJob" nie jest rozpoznawany jako nazwa polecenia cmdlet, funkcji, pliku skryptu ani programu interoperacyjnego. Sprawdź pisownię nazwy lub, jeśli ścieżka została uwzględniona, sprawdź, czy ścieżka jest poprawna, i spróbuj ponownie.

    - Jeśli ten błąd nie powiedzie się, należy ponownie zainstalować funkcję Kopia zapasowa systemu Windows Server na komputerze serwera, jak wspomniano w sekcji Krok 1. wymagania wstępne.

  - Upewnij się, że kopia zapasowa WSB działa prawidłowo, uruchamiając następujące polecenie w wierszu polecenia z podwyższonym poziomem uprawnień:

      `wbadmin start systemstatebackup -backuptarget:X: -quiet`

      > [!NOTE]
      >Zastąp znak X literą dysku woluminu, na którym chcesz przechowywać kopię zapasową stanu systemu.

    - Okresowo sprawdzaj stan zadania, uruchamiając polecenie `Get-WBJob` za pomocą programu PowerShell z podwyższonym poziomem uprawnień
    - Po zakończeniu zadania tworzenia kopii zapasowej Sprawdź stan końcowy zadania, uruchamiając polecenie `Get-WBJob -Previous 1`

Jeśli zadanie nie powiedzie się, wskazuje to na problem z WSB, który spowoduje niepowodzenie tworzenia kopii zapasowych stanu systemu agenta MARS.

## <a name="common-errors"></a>Typowe błędy

### <a name="vss-writer-timeout-error"></a>Błąd limitu czasu składnika zapisywania usługi VSS

| Objaw | Przyczyna | Rozwiązanie
| -- | -- | --
| -Agent MARS kończy się niepowodzeniem z komunikatem o błędzie: "zadanie WSB nie powiodło się z błędami usługi VSS. Sprawdź dzienniki zdarzeń usługi VSS, aby rozwiązać ten problem.<br/><br/> — Następujący dziennik błędów znajduje się w dziennikach zdarzeń aplikacji usługi VSS: "składnik zapisywania usługi VSS odrzucił zdarzenie z błędem 0x800423f2, upłynął limit czasu modułu zawieszania i odblokowywania."| Nie można wykonać składnika zapisywania usługi VSS w czasie z powodu braku zasobów procesora i pamięci na maszynie <br/><br/> Inne oprogramowanie do tworzenia kopii zapasowych korzysta już z składnika zapisywania usługi VSS, ponieważ nie można ukończyć operacji migawki wyniku dla tej kopii zapasowej | Poczekaj, aż procesor/pamięć zostaną zwolnione w systemie lub przerywa procesy trwające zbyt dużą ilość pamięci/procesora, a następnie spróbuj ponownie wykonać operację. <br/><br/>  Poczekaj na zakończenie trwającej kopii zapasowej i spróbuj wykonać operację w późniejszym czasie, gdy na maszynie nie są uruchomione żadne kopie zapasowe

### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>Za mało miejsca na dysku, aby zwiększyć ilość kopii w tle

| Objaw | Rozwiązanie
| -- | --
| -Agent MARS kończy się niepowodzeniem z komunikatem o błędzie: wykonywanie kopii zapasowej nie powiodło się, ponieważ nie można było zwiększyć woluminu kopii w tle z powodu niewystarczającej ilości miejsca na woluminach <br/><br/> — Następujący dziennik błędów/ostrzeżeń jest obecny w dzienniku zdarzeń systemu Volsnap: "za mało miejsca na dysku w woluminie C:, aby zwiększyć magazyn kopii w tle dla kopii w tle C: z powodu tego błędu wszystkie kopie w tle woluminu C: są zagrożone. | -Zwolnij miejsce na wyróżnionym woluminie w dzienniku zdarzeń, tak że jest wystarczająca ilość miejsca na zwiększenie rozmiaru kopii w tle, gdy trwa wykonywanie kopii zapasowej <br/><br/> — Podczas konfigurowania obszaru kopiowania w tle możemy ograniczyć ilość miejsca używanego do kopiowania w tle. Aby uzyskać więcej informacji, zobacz ten [artykuł](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc788050(v=ws.11)#syntax)

### <a name="efi-partition-locked"></a>Zablokowana partycja EFI

| Objaw | Rozwiązanie
| -- | --
| Agent MARS kończy się niepowodzeniem z komunikatem o błędzie: "kopia zapasowa stanu systemu nie powiodła się, ponieważ partycja systemowa EFI jest Może to być spowodowane dostępem do partycji systemowej przez zabezpieczenia innej firmy lub oprogramowanie do tworzenia kopii zapasowych. | — Jeśli problem jest spowodowany przez oprogramowanie zabezpieczeń innej firmy, należy skontaktować się z dostawcą oprogramowania antywirusowego, aby umożliwić mu agenta MARS <br/><br/> — Jeśli działa oprogramowanie do tworzenia kopii zapasowych innej firmy, zaczekaj na jego zakończenie, a następnie ponów próbę wykonania kopii zapasowej

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat stanu systemu Windows w ramach wdrażania Menedżer zasobów, zobacz [Tworzenie kopii zapasowej stanu systemu Windows Server](backup-azure-system-state.md) .
