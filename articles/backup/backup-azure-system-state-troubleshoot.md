---
title: Rozwiązywanie problemów z kopii zapasowej stanu systemu za pomocą usługi Azure Backup
description: Rozwiązywanie problemów w kopii zapasowej stanu systemu.
services: backup
author: srinathvasireddy
manager: sivan
keywords: jak tworzyć kopie zapasowe; Stan systemu tworzenia kopii zapasowych
ms.service: backup
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: srinathv
ms.openlocfilehash: 87b5fff58ecf9e89bc94f31a0bc3a591c146c88f
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704998"
---
# <a name="troubleshoot-system-state-backup"></a>Rozwiązywanie problemów z kopii zapasowej stanu systemu

W tym artykule opisano rozwiązania problemów, które mogą wystąpić podczas korzystania z kopii zapasowej stanu systemu.

## <a name="basic-troubleshooting"></a>Podstawowe rozwiązywanie problemów
Zalecane jest wykonanie poniżej sprawdzania poprawności, przed rozpoczęciem rozwiązywania problemów z kopii zapasowej stanu systemu:

- [Upewnij się, że Agent usługi Microsoft Azure Recovery Services (MARS) jest aktualny](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Upewnij się, że istnieje połączenie sieciowe między agentem MARS i platformą Azure](https://aka.ms/AB-A4dp50)
- Upewnij się, że usługa Microsoft Azure Recovery Services jest uruchomiona (w konsoli usługi). Jeśli to wymagane, ponownie uruchom i ponów operację
- [Upewnij się, że 5–10% wolnego miejsca na woluminie jest dostępne na potrzeby lokalizacji folderu plików tymczasowych](https://aka.ms/AB-AA4dwtt)
- [Sprawdź, czy inny proces lub oprogramowanie antywirusowe nie zakłóca działania usługi Azure Backup](https://aka.ms/AB-AA4dwtk)
- [Zaplanowane tworzenie kopii zapasowej kończy się niepowodzeniem, ale ręczne tworzenie kopii zapasowej działa](https://aka.ms/ScheduledBackupFailManualWorks)
- Upewnij się, że w systemie operacyjnym zainstalowano najnowsze aktualizacje
- [Upewnij się, nieobsługiwane dyski i pliki z nieobsługiwanych atrybutów, które są wykluczone z kopii zapasowej](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- Upewnij się, że **zegar systemowy** chronionego systemu ma skonfigurowaną prawidłową strefę czasową <br>
- [Upewnij się, że na serwerze zainstalowano program .NET Framework w wersji 4.5.2 lub nowszej](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- Jeśli próbujesz **ponownie zarejestrować serwer** w magazynie, wykonaj następujące czynności: <br>
  - Upewnij się, że agent został odinstalowany z serwera oraz usunięty z portalu <br>
  - Użyj tego samego hasła, które zostało początkowo użyte podczas rejestrowania serwera <br>
- W przypadku kopii zapasowej offline upewnij się, że Azure PowerShell w wersji numer 3.7.0 jest zainstalowany na komputerze źródłowym i kopiowania, przed przystąpieniem do wykonywania operacji tworzenia kopii zapasowej w trybie offline
- [Kwestia, gdy agent usługi Kopia zapasowa jest uruchomiony na maszynie wirtualnej platformy Azure](https://aka.ms/AB-AA4dwtr)

### <a name="limitation"></a>Ograniczenia
- Odzyskiwanie na inny komputer przy użyciu funkcji odzyskiwania stanu systemu nie jest zalecane przez firmę Microsoft.
- Kopia zapasowa stanu systemu obsługuje obecnie "lokalne" serwery Windows, ta funkcja nie jest dostępna dla maszyn wirtualnych platformy Azure.

## <a name="pre-requisite"></a>Wymagania wstępne

Przed rozwiązywać kopii zapasowej stanu systemu za pomocą usługi Azure Backup, upewnij się, możesz wykonywać poniższe wymagania wstępne Sprawdź.  

### <a name="verify-windows-server-backup-is-installed"></a>Sprawdź, czy kopia zapasowa systemu Windows Server jest zainstalowany

Upewnij się, kopia zapasowa systemu Windows Server jest zainstalowane i włączone na serwerze. Aby sprawdzić stan instalacji, uruchom poniższe polecenie programu PowerShell:

 ```
 PS C:\> Get-WindowsFeature Windows-Server-Backup
 ```
Jeśli dane wyjściowe wyświetla **stan instalacji** jako **dostępne**, oznacza to, funkcji Kopia zapasowa systemu Windows Server jest dostępna do instalacji, ale nie została zainstalowana na serwerze. Jednak jeśli kopia zapasowa systemu Windows Server nie jest zainstalowany, użyj jednej z poniższych metod, aby go zainstalować.

**Metoda 1. Zainstaluj program Kopia zapasowa serwera systemu Windows przy użyciu programu PowerShell**

Aby zainstalować program Kopia zapasowa serwera systemu Windows przy użyciu programu PowerShell, uruchom poniższe polecenie:

  ```
  PS C:\> Install-WindowsFeature -Name Windows-Server-Backup
  ```

**Metoda 2. Zainstaluj program Kopia zapasowa serwera systemu Windows za pomocą Menedżera serwera**

Aby zainstalować program Kopia zapasowa serwera systemu Windows za pomocą Menedżera serwera, wykonaj poniższe:

1. W **Server Manager** i kliknij przycisk **Dodaj role i funkcje**. **Kreatora dodawania ról i funkcji** pojawia się.

    ![Pulpit nawigacyjny](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. Wybierz **typu instalacji** i kliknij przycisk **dalej**.

    ![Typ instalacji](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. Wybierz serwer z puli serwerów, a następnie kliknij przycisk **dalej**. W roli serwera, należy pozostawić wybór domyślny i kliknij przycisk **dalej**.
4. Wybierz **kopia zapasowa systemu Windows Server** w **funkcji** kartę, a następnie kliknij przycisk **dalej**.

    ![danych](./media/backup-azure-system-state-troubleshoot/features.png)

5. W **potwierdzenie** kliknij pozycję **zainstalować** do uruchamiania procesu instalacji.
6. W **wyniki** kartę, zostaną wyświetlone kopia zapasowa serwera systemu Windows, które zostały pomyślnie zainstalowano funkcję w systemie Windows Server.

    ![wynik](./media/backup-azure-system-state-troubleshoot/results.jpg)


### <a name="system-volume-information-permission"></a>Uprawnienia woluminu systemu

Upewnij się, że SYSTEM lokalny ma pełną kontrolę na **informacji o woluminie systemowym** folder znajduje się w woluminie, na którym zainstalowano system windows. Zazwyczaj jest to **C:\System Volume Information**. Kopia zapasowa systemu Windows Server może zakończyć się niepowodzeniem, jeśli powyższe uprawnienia nie zostały poprawnie skonfigurowane.

### <a name="dependent-services"></a>Usług zależnych

Upewnij się, poniżej usług są w stanie uruchomienia:

**Nazwa usługi** | **Typ uruchomienia**
--- | ---
Call(RPC) procedury zdalnej | Automatyczne
System(EventSystem) zdarzeń modelu COM + | Automatyczne
System zdarzeń powiadomień Service(SENS) | Automatyczne
Copy(VSS) w tle woluminu | Ręcznie
Provider(SWPRV) kopii w tle oprogramowania firmy Microsoft | Ręcznie

### <a name="validate-windows-server-backup-status"></a>Zweryfikuj status kopia zapasowa systemu Windows Server

Aby zweryfikować stan kopia zapasowa systemu Windows Server, należy wykonać poniższe:

  * Upewnij się, że środowiska PowerShell WSB jest uruchomiona.

    -   Uruchom `Get-WBJob` z programu PowerShell z podwyższonym poziomem uprawnień i upewnij się, że nie zwrócił następujący błąd:

    > [!WARNING]
    > Get-WBJob: Termin "Get-WBJob" nie został rozpoznany jako nazwa polecenia cmdlet, funkcji, pliku skryptu lub program wykonywalny. Sprawdź pisownię nazwy lub jeśli ścieżka został uwzględniony, sprawdź, czy ścieżka jest poprawna i spróbuj ponownie.

    -   Jeśli zakończy się niepowodzeniem z powodu następującego błędu, ponownie zainstaluj funkcję kopia zapasowa systemu Windows Server na komputerze z serwerem zgodnie z opisem w kroku 1 wymagań wstępnych.

  * Upewnij się, kopia zapasowa WSB działa prawidłowo, uruchamiając poniższe polecenie w wierszu polecenia z podwyższonym poziomem uprawnień:

      `wbadmin start systemstatebackup -backuptarget:X: -quiet`

      > [!NOTE]
      >Zastąp X za pomocą litery dysku woluminu, na którym chcesz przechowywać dane o stanie systemu wykonać kopię zapasową obrazu.

    - Okresowo sprawdzać stan zadania, uruchamiając `Get-WBJob` polecenia z podwyższonym poziomem uprawnień programu PowerShell        
    - Po zakończeniu zadania tworzenia kopii zapasowej Sprawdź stan końcowy zadania, uruchamiając `Get-WBJob -Previous 1` polecenia

Jeśli zadanie nie powiedzie się, wskazuje problem programu WSB, co mogłoby spowodować agenta usług MARS błędów kopii zapasowych stanu systemu.

## <a name="common-errors"></a>Typowe błędy

### <a name="vss-writer-timeout-error"></a>Błąd upływu limitu czasu składnik zapisywania usługi VSS

| Objaw | Przyczyna | Rozwiązanie
| -- | -- | --
| -Agenta usług MARS kończy się niepowodzeniem z komunikatem o błędzie: "Zadania usługi WSB nie powiodło się z błędem usługi VSS. Sprawdź dzienniki zdarzeń usługi VSS, aby rozwiązać błąd"<br/><br/> -Następujący błąd dziennika znajduje się w dziennikach zdarzeń aplikacji usługi VSS: "Składnika zapisywania usługi VSS odrzucił zdarzenie z powodu błędu 0x800423f2, moduł zapisujący upłynął limit czasu między zdarzeniami zablokowania i odblokowania".| Składnik zapisywania usługi VSS nie może wykonać w czasie ze względu na brak zasobów Procesora i pamięci na maszynie <br/><br/> Już korzysta z innego oprogramowania kopii zapasowej składnik zapisywania usługi VSS, w wyniku migawki nie można ukończyć operacji dla tej kopii zapasowej | Poczekaj, aż Procesora/pamięci zostać zwolnione w systemie lub przerwać procesy biorąc zbyt dużej ilości pamięci/procesora CPU i spróbuj ponownie wykonać operację <br/><br/>  Poczekaj, aż trwające kopii zapasowej w celu ukończenia, a następnie spróbuj wykonać operację w późniejszym momencie, gdy żadne kopie zapasowe działają na maszynie


### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>Za mało miejsca na rozwój kopie w tle

| Objaw | Rozwiązanie
| -- | --
| -Agenta usług MARS kończy się niepowodzeniem z komunikatem o błędzie: Niepowodzenie wykonywania kopii zapasowej, ponieważ woluminu kopii w tle nie może powiększyć z powodu niewystarczającego miejsca na dysku na woluminach zawierających pliki systemowe <br/><br/> -Błędu/ostrzeżenia dziennika po znajduje się w dzienniki zdarzeń systemowych volsnap: "Wystąpił brak miejsca na dysku w woluminie C: rośnie magazynu kopii w tle dla kopii w tle c: z powodu tego błędu wszystkie kopie w tle woluminu C: są zagrożone usuwany" | -Wolnego miejsca na woluminie wyróżnione w dzienniku zdarzeń tak, że istnieje wystarczająca ilość miejsca dla kopii w tle rośnie, gdy trwa wykonywanie kopii zapasowej <br/><br/> — Podczas konfigurowania miejsca kopii w tle, firma Microsoft może ograniczyć ilość miejsca na kopie w tle, aby uzyskać więcej informacji zobacz [artykułu](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc788050(v=ws.11)#syntax)


### <a name="efi-partition-locked"></a>Partycję EFI, zablokowane

| Objaw | Rozwiązanie
| -- | --
| Agenta usług MARS kończy się niepowodzeniem z komunikatem o błędzie: "Ponownie stan systemu nawet nie powiodło się ponieważ partycja systemowa EFI jest zablokowany. To może być spowodowane przez zabezpieczenia innych firm, dostęp do partycji systemowej lub utworzyć kopię zapasową oprogramowania" | -Jeśli ten problem wynika z oprogramowania zabezpieczającego innej firmy, należy skontaktować się z dostawcą ochrony przed wirusami, dzięki czemu umożliwiają one agenta usług MARS <br/><br/> — Jeśli działa oprogramowanie do tworzenia kopii zapasowych innych firm, następnie poczekaj na jego zakończenie, a następnie ponów próbę wykonania kopii zapasowej


## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat stanu systemu Windows podczas wdrażania usługi Resource Manager, zobacz [tworzenie kopii zapasowej stanu systemu Windows Server](backup-azure-system-state.md)
