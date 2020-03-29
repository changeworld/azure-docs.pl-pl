---
title: Rozwiązywanie problemów z tworzeniem kopii zapasowej stanu systemu
description: W tym artykule dowiesz się, jak rozwiązywać problemy w kopii zapasowej stanu systemu dla lokalnych serwerów systemu Windows.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/22/2019
ms.openlocfilehash: 28647b72334d592692c5fe1b031735330d1a0509
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969571"
---
# <a name="troubleshoot-system-state-backup"></a>Rozwiązywanie problemów z tworzeniem kopii zapasowej stanu systemu

W tym artykule opisano rozwiązania problemów, które mogą wystąpić podczas korzystania z kopii zapasowej stanu systemu.

## <a name="basic-troubleshooting"></a>Podstawowe rozwiązywanie problemów

Zaleca się wykonanie poniższej weryfikacji, przed rozpoczęciem rozwiązywania problemów z tworzeniem kopii zapasowej stanu systemu:

- [Upewnij się, że agent usług odzyskiwania platformy Microsoft Azure (MARS) jest aktualny](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Upewnij się, że istnieje połączenie sieciowe między agentem MARS i platformą Azure](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Upewnij się, że usługa Microsoft Azure Recovery Services jest uruchomiona (w konsoli usługi). W razie potrzeby uruchom ponownie i ponów próbę wykonania operacji
- [Upewnij się, że 5–10% wolnego miejsca na woluminie jest dostępne na potrzeby lokalizacji folderu plików tymczasowych](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#whats-the-minimum-size-requirement-for-the-cache-folder)
- [Sprawdź, czy inny proces lub oprogramowanie antywirusowe nie zakłóca działania usługi Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-another-process-or-antivirus-software-interfering-with-azure-backup)
- [Zaplanowane tworzenie kopii zapasowej kończy się niepowodzeniem, ale ręczne tworzenie kopii zapasowej działa](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#backups-dont-run-according-to-schedule)
- Upewnij się, że w systemie operacyjnym zainstalowano najnowsze aktualizacje
- [Upewnij się, że nieobsługiwały dyski i pliki z nieobsługiwałymi atrybutami są wykluczone z kopii zapasowej](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- Upewnij **się, że zegar systemowy** w chronionym systemie jest skonfigurowany do prawidłowej strefy czasowej <br>
- [Upewnij się, że na serwerze zainstalowano program .NET Framework w wersji 4.5.2 lub nowszej](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- Jeśli próbujesz **ponownie zarejestrować serwer** w przechowalni, wówczas: <br>
  - Upewnij się, że agent został odinstalowany na serwerze i został usunięty z portalu <br>
  - Użyj tego samego hasła, które zostało początkowo użyte podczas rejestrowania serwera <br>
- Jeśli jest to kopia zapasowa w trybie offline, upewnij się, że program Azure PowerShell w wersji 3.7.0 jest zainstalowany zarówno na komputerze źródłowym, jak i kopiuj przed rozpoczęciem operacji tworzenia kopii zapasowej w trybie offline
- [Należy wziąć pod uwagę, gdy agent kopii zapasowej jest uruchomiony na maszynie wirtualnej platformy Azure](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-backup-agent-running-on-an-azure-virtual-machine)

### <a name="limitation"></a>Ograniczenia

- Odzyskiwanie na inny komputer przy użyciu funkcji odzyskiwania stanu systemu nie jest zalecane przez firmę Microsoft.
- Kopia zapasowa stanu systemu obsługuje obecnie "lokalne" serwery systemu Windows. Ta funkcja nie jest dostępna dla maszyn wirtualnych platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Zanim rozwiążemy problemy z tworzeniem kopii zapasowej stanu systemu za pomocą usługi Azure Backup, wykonaj poniższe wymagania wstępne.  

### <a name="verify-windows-server-backup-is-installed"></a>Sprawdź, czy jest zainstalowana kopia zapasowa systemu Windows Server

Upewnij się, że kopia zapasowa systemu Windows Server jest zainstalowana i włączona na serwerze. Aby sprawdzić stan instalacji, uruchom to polecenie programu PowerShell:

 ```powershell
Get-WindowsFeature Windows-Server-Backup
 ```

Jeśli dane wyjściowe są wyświetlane jako **dostępne**w **stanie instalacji,** oznacza to, że funkcja tworzenia kopii zapasowej systemu Windows Server jest dostępna dla instalacji, ale nie jest zainstalowana na serwerze. Jeśli jednak kopia zapasowa systemu Windows Server nie jest zainstalowana, użyj jednej z poniższych metod, aby ją zainstalować.

#### <a name="method-1-install-windows-server-backup-using-powershell"></a>Metoda 1: Instalowanie kopii zapasowej systemu Windows Server przy użyciu programu PowerShell

Aby zainstalować kopię zapasową systemu Windows Server przy użyciu programu PowerShell, uruchom następujące polecenie:

  ```powershell
  Install-WindowsFeature -Name Windows-Server-Backup
  ```

#### <a name="method-2-install-windows-server-backup-using-server-manager"></a>Metoda 2: Instalowanie kopii zapasowej systemu Windows Server przy użyciu Menedżera serwera

Aby zainstalować kopię zapasową systemu Windows Server przy użyciu Menedżera serwera, wykonaj poniższe czynności:

1. W **obszarze Menedżer serwera**kliknij pozycję Dodaj role i **funkcje**. Zostanie wyświetlony **Kreator dodawania ról i funkcji.**

    ![Pulpit nawigacyjny](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. Wybierz **pozycję Typ instalacji** i kliknij przycisk **Dalej**.

    ![Typ instalacji](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. Wybierz serwer z puli serwerów i kliknij przycisk **Dalej**. W roli serwera pozostaw domyślne zaznaczenie i kliknij przycisk **Dalej**.
4. Wybierz kartę **Kopia zapasowa systemu Windows Server** w **elementów** i kliknij przycisk **Dalej**.

    ![funkcje](./media/backup-azure-system-state-troubleshoot/features.png)

5. Na karcie **Potwierdzenie** kliknij pozycję **Zainstaluj,** aby rozpocząć proces instalacji.
6. Na karcie **Wyniki** zostanie wyświetlone pomyślnie zainstalowana funkcja kopia zapasowa systemu Windows Server w systemie Windows Server.

    ![wynik](./media/backup-azure-system-state-troubleshoot/results.jpg)

### <a name="system-volume-information-permission"></a>Uprawnienie informacje o woluminie systemowym

Upewnij się, że system lokalny ma pełną kontrolę nad folderem **Informacje o woluminie systemowym** znajdującym się na woluminie, na którym jest zainstalowany system Windows. Zwykle jest to **C:\Informacje o woluminie systemowym**. Kopia zapasowa systemu Windows Server może zakończyć się niepowodzeniem, jeśli powyższe uprawnienia nie są poprawnie ustawione

### <a name="dependent-services"></a>Usługi zależne

Upewnij się, że poniższe usługi są w stanie uruchomienia:

**Nazwa usługi** | **Typ uruchomienia**
--- | ---
Zdalne wywołanie procedury (RPC) | Automatyczny
System zdarzeń COM+ (System zdarzeń) | Automatyczny
Usługa powiadamiania o zdarzeniach systemowych (SENS) | Automatyczny
Kopiowanie woluminu w tle(VSS) | Ręcznie
Dostawca kopii w tle oprogramowania firmy Microsoft (SWPRV) | Ręcznie

### <a name="validate-windows-server-backup-status"></a>Sprawdzanie poprawności stanu kopii zapasowej systemu Windows Server

Aby sprawdzić poprawność stanu kopii zapasowej systemu Windows Server, wykonaj następujące czynności:

- Upewnij się, że program WSB PowerShell jest uruchomiony

  - Uruchom `Get-WBJob` z podwyższonego poziomu programu PowerShell i upewnij się, że nie zwraca następujący błąd:

    > [!WARNING]
    > Get-WBJob: Termin "Get-WBJob" nie jest rozpoznawany jako nazwa polecenia cmdlet, funkcji, pliku skryptu lub programu operacyjnego. Sprawdź pisownię nazwy lub jeśli ścieżka została uwzględniona, sprawdź, czy ścieżka jest poprawna i spróbuj ponownie.

    - Jeśli ten błąd nie powiedzie się, należy ponownie zainstalować funkcję kopii zapasowej systemu Windows Server na komputerze serwera, o czym wspomniano w kroku 1 wymagań wstępnych.

  - Upewnij się, że kopia zapasowa WSB działa poprawnie, uruchamiając poniższe polecenie z wiersza polecenia z podwyższonym poziomem uprawnień:

      `wbadmin start systemstatebackup -backuptarget:X: -quiet`

      > [!NOTE]
      >Zastąp X literą dysku woluminu, na którym chcesz zapisać obraz kopii zapasowej stanu systemu.

    - Okresowo sprawdzaj stan zadania, `Get-WBJob` uruchamiając polecenie z podwyższonego poziomu programu PowerShell
    - Po zakończeniu zadania tworzenia kopii zapasowej sprawdź `Get-WBJob -Previous 1` ostateczny stan zadania, uruchamiając polecenie

Jeśli zadanie nie powiedzie się, wskazuje problem WSB, który spowodowałoby błąd kopii zapasowych stanu systemu marsjańskich.

## <a name="common-errors"></a>Typowe błędy

### <a name="vss-writer-timeout-error"></a>Błąd limitu czasu modułu zapisu usługi VSS

| Objaw | Przyczyna | Rozwiązanie
| -- | -- | --
| - Agent MARS kończy się niepowodzeniem z komunikatem o błędzie: "Zadanie WSB nie powiodło się z błędami VSS. Sprawdź dzienniki zdarzeń usługi VSS, aby rozwiązać problem błędu"<br/><br/> - Następujący dziennik błędów jest obecny w dziennikach zdarzeń aplikacji VSS: "Moduł zapisujący vss odrzucił zdarzenie z błędem 0x800423f2, limit czasu modułu zapisującego wygasł między zdarzeniami Zablokuj i Odwilż."| Moduł zapisujący vss nie może zakończyć się w czasie z powodu braku zasobów procesora i pamięci na komputerze <br/><br/> Inne oprogramowanie do tworzenia kopii zapasowych jest już przy użyciu modułu zapisującego VSS, w wyniku operacji migawki wynik nie można ukończyć dla tej kopii zapasowej | Poczekaj na zwolnienie procesora CPU/pamięci w systemie lub przerwij procesy, zabierając za dużo pamięci/procesora i spróbuj ponownie wykonać operację. <br/><br/>  Poczekaj na zakończenie trwającej kopii zapasowej i spróbuj wykonać operację w późniejszym momencie, gdy na komputerze nie są uruchomione żadne kopie zapasowe.

### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>Za mało miejsca na dysku, aby rozwijać kopie w tle

| Objaw | Rozwiązanie
| -- | --
| - Agent MARS kończy się niepowodzeniem z komunikatem o błędzie: Kopia zapasowa nie powiodła się, ponieważ wolumin kopiowania w tle nie mógł wzrosnąć z powodu niewystarczającego miejsca na dysku na woluminach zawierających pliki systemowe <br/><br/> - Następujące błąd / ostrzeżenie dziennik jest obecny w dziennikach zdarzeń systemu volsnap: "Nie było wystarczającej ilości miejsca na dysku na woluminie C: aby zwiększyć przechowywanie kopii w tle dla kopii w tle C: z powodu tej awarii wszystkie kopie w tle woluminu C: są narażone na ryzyko usunięcia" | - Zwolnić miejsce w podświetlony wolumin w dzienniku zdarzeń, tak aby było wystarczająco dużo miejsca na kopie w tle rosnąć podczas tworzenia kopii zapasowych jest w toku <br/><br/> - Podczas konfigurowania przestrzeni kopiowania w tle możemy ograniczyć ilość miejsca używanego do kopiowania w tle. Aby uzyskać więcej informacji, zobacz ten [artykuł](https://docs.microsoft.com/windows-server/administration/windows-commands/vssadmin-resize-shadowstorage)

### <a name="efi-partition-locked"></a>Partycja EFI zablokowana

| Objaw | Rozwiązanie
| -- | --
| Agent MARS kończy się niepowodzeniem z komunikatem o błędzie: "Kopia zapasowa stanu systemu nie powiodła się, ponieważ partycja systemowa EFI jest zablokowana. Może to być spowodowane dostępem do partycji systemowej przez zabezpieczenia innych firm lub tworzenie kopii zapasowych oprogramowania" | - Jeśli problem jest spowodowany oprogramowaniem zabezpieczającym innej firmy, należy skontaktować się z dostawcą antywirusów, aby mógł zezwolić agentowi MARS <br/><br/> - Jeśli oprogramowanie do tworzenia kopii zapasowych innej firmy jest uruchomione, poczekaj na jego zakończenie, a następnie ponów próbę utworzenia kopii zapasowej

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji o stanie systemu Windows we wdrażaniu Menedżera zasobów, zobacz [Zapasowy zapas stanu systemu Windows Server](backup-azure-system-state.md)
