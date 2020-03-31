---
title: Generdyzuje zapas maszyn wirtualnych platformy Azure w magazynie usług odzyskiwania
description: W tym artykule opisano sposób tworzenia kopii zapasowych maszyn wirtualnych platformy Azure w magazynie usług odzyskiwania przy użyciu usługi Azure Backup
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: aeadd7bc798f690c67eef38c6dc645204ff39115
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273516"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Generdyzuje zapas maszyn wirtualnych platformy Azure w magazynie usług odzyskiwania

W tym artykule opisano sposób tworzenia kopii zapasowej maszyn wirtualnych platformy Azure w magazynie usług odzyskiwania przy użyciu usługi [Azure Backup.](backup-overview.md)

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
>
> * Przygotowywanie maszyn wirtualnych platformy Azure.
> * Tworzenie przechowalni.
> * Odnajduj maszyny wirtualne i skonfiguruj zasady tworzenia kopii zapasowych.
> * Włącz tworzenie kopii zapasowych dla maszyn wirtualnych platformy Azure.
> * Uruchom proces tworzenia początkowej kopii zapasowej.

> [!NOTE]
> W tym artykule opisano sposób konfigurowania przechowalni i wybierania maszyn wirtualnych do utworzenia kopii zapasowej. Jest to przydatne, jeśli chcesz zrobić z powrotem do kopii zapasowej wiele maszyn wirtualnych. Alternatywnie można [wywkprzeć pojedynczą maszynę wirtualną platformy Azure](backup-azure-vms-first-look-arm.md) bezpośrednio z ustawień maszyny Wirtualnej.

## <a name="before-you-start"></a>Przed rozpoczęciem

* [Przejrzyj](backup-architecture.md#architecture-built-in-azure-vm-backup) architekturę kopii zapasowej maszyny Wirtualnej platformy Azure.
* [Dowiedz się więcej o](backup-azure-vms-introduction.md) Kopia zapasowa maszyny Wirtualnej platformy Azure i rozszerzenie kopii zapasowej.
* [Przejrzyj macierz pomocy technicznej](backup-support-matrix-iaas.md) przed skonfigurowaniem kopii zapasowej.

Ponadto, istnieje kilka rzeczy, które mogą być konieczne do zrobienia w pewnych okolicznościach:

* **Zainstaluj agenta maszyny Wirtualnej na maszynie Wirtualnej:** Usługa Azure Backup kopie zapasowe maszyn wirtualnych platformy Azure, instalując rozszerzenie agenta maszyny wirtualnej platformy Azure uruchomionego na komputerze. Jeśli maszyna wirtualna została utworzona na podstawie obrazu portalu Azure marketplace, agent jest instalowany i uruchamiany. W przypadku utworzenia niestandardowej maszyny wirtualnej lub migracji komputera lokalnego może być konieczne [ręczne zainstalowanie agenta.](#install-the-vm-agent)

## <a name="create-a-vault"></a>Tworzenie magazynu

 Magazyn przechowuje kopie zapasowe i punkty odzyskiwania utworzone w czasie i przechowuje zasady tworzenia kopii zapasowych skojarzone z komputerami kopii zapasowych. Utwórz przechowalnię w następujący sposób:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
2. W wyszukiwaniu wpisz polecenie **Usługi odzyskiwania**. W obszarze **Usługi**kliknij pozycję **Magazyny usług odzyskiwania**.

     ![Wyszukiwanie magazynów usług odzyskiwania](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png)

3. W menu **Magazyny usług odzyskiwania** kliknij polecenie **+Dodaj**.

     ![Tworzenie magazynu Usług odzyskiwania — krok 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

4. W **magazynie usług odzyskiwania**wpisz przyjazną nazwę, aby zidentyfikować przechowalnię.
    * Nazwa musi być unikalna w tej subskrypcji platformy Azure.
    * Może zawierać od 2 do 50 znaków.
    * Musi zaczynać się od litery i może zawierać tylko litery, cyfry i łączniki.
5. Wybierz subskrypcję platformy Azure, grupę zasobów i region geograficzny, w którym ma zostać utworzony magazyn. Następnie kliknij przycisk **Utwórz**.
    * Może upłynąć trochę czasu, aby magazyn został utworzony.
    * Monitorowanie powiadomień o stanie w prawym górnym obszarze portalu.

Po utworzeniu przechowalni pojawi się na liście magazynów usług recovery services. Jeśli nie widzisz przechowalni, wybierz pozycję **Odśwież**.

![Lista magazynów kopii zapasowych](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

>[!NOTE]
> Usługa Azure Backup umożliwia teraz dostosowywanie nazwy grupy zasobów utworzonej przez usługę Azure Backup. Aby uzyskać więcej informacji, zobacz [Grupa zasobów usługi Azure Backup dla maszyn wirtualnych](backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines).

### <a name="modify-storage-replication"></a>Modyfikowanie replikacji magazynu

Domyślnie przechowalnia używają [magazynu geograficznie nadmiarowego (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Jeśli magazyn jest podstawowym mechanizmem tworzenia kopii zapasowych, zaleca się użycie GRS.
* Aby uzyskać tańszą opcję, można użyć [magazynu lokalnie nadmiarowego (LRS).](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

Zmodyfikuj typ replikacji magazynu w następujący sposób:

1. W nowym przechowalni kliknij pozycję **Właściwości** w sekcji **Ustawienia.**
2. W **obszarze Właściwości**w obszarze **Konfiguracja kopii zapasowej**kliknij pozycję **Aktualizuj**.
3. Wybierz typ replikacji magazynu i kliknij przycisk **Zapisz**.

      ![Ustawianie konfiguracji przechowywania dla nowego magazynu](./media/backup-try-azure-backup-in-10-mins/full-blade.png)

> [!NOTE]
   > Nie można zmodyfikować typu replikacji magazynu po skonfigurowaniu przechowalni i zawiera elementy kopii zapasowej. Jeśli chcesz to zrobić, musisz ponownie utworzyć przechowalnię.

## <a name="apply-a-backup-policy"></a>Stosowanie zasad tworzenia kopii zapasowych

Konfigurowanie zasad tworzenia kopii zapasowych dla przechowalni.

1. W przechowalni kliknij pozycję **+Kopia zapasowa** w sekcji **Przegląd.**

   ![Przycisk Kopia zapasowa](./media/backup-azure-arm-vms-prepare/backup-button.png)

2. W **celu** > tworzenia kopii zapasowej **Azure**Gdzie jest**uruchomione obciążenie?** W obszarze Co chcesz wykonać **Virtual machine** >  **OK** **kopii zapasowej?** Spowoduje to zarejestrowanie rozszerzenia maszyny Wirtualnej w przechowalni.

   ![Okienka tworzenia kopii zapasowych i tworzenia kopii zapasowych celów](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

3. W **obszarze Zasady kopii zapasowej**wybierz zasadę, którą chcesz skojarzyć z przechowalnią.
    * Domyślna zasada kopii zapasowej maszyny Wirtualnej raz dziennie. Codzienne kopie zapasowe są przechowywane przez 30 dni. Migawki natychmiastowego odzyskiwania są zachowywane przez dwa dni.
    * Jeśli nie chcesz używać zasad domyślnych, wybierz pozycję **Utwórz nowy**i utwórz zasady niestandardowe zgodnie z opisem w następnej procedurze.

      ![Domyślne zasady tworzenia kopii zapasowych](./media/backup-azure-arm-vms-prepare/default-policy.png)

4. W **obszarze Wybierz maszyny wirtualne**wybierz maszyny wirtualne, których tworzenie kopii zapasowych chcesz uzyskać przy użyciu zasad. Następnie kliknij przycisk **OK**.

   * Wybrane maszyny wirtualne są sprawdzane.
   * Maszyn wirtualnych można wybrać tylko w tym samym regionie co przechowalnia.
   * Kopie zapasowe maszyn wirtualnych można wywrzeć tylko w jednym magazynie.

     ![Okienko "Wybierz maszyny wirtualne"](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. W **obszarze Kopia zapasowa**kliknij pozycję **Włącz tworzenie kopii zapasowej**. Spowoduje to wdrożenie zasad do magazynu i na maszynach wirtualnych i instaluje rozszerzenie kopii zapasowej na agencie maszyny Wirtualnej uruchomionej na maszynie Wirtualnej platformy Azure.

     ![Przycisk "Włącz tworzenie kopii zapasowej"](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Po włączeniu kopii zapasowej:

* Usługa Kopia zapasowa instaluje rozszerzenie kopii zapasowej, niezależnie od tego, czy maszyna wirtualna jest uruchomiona.
* Początkowa kopia zapasowa zostanie uruchomiona zgodnie z harmonogramem tworzenia kopii zapasowych.
* Po uruchomieniu kopii zapasowych należy pamiętać, że:
  * Maszyna wirtualna, która jest uruchomiona mają największe szanse na przechwytywanie punktu odzyskiwania spójne aplikacji.
  * Jednak nawet jeśli maszyna wirtualna jest wyłączona, jest utworzona kopia zapasowa. Taka maszyna wirtualna jest znana jako maszyna wirtualna w trybie offline. W takim przypadku punkt odzyskiwania będzie zgodny z awariami.
* Jawna łączność wychodząca nie jest wymagana do umożliwienia tworzenia kopii zapasowych maszyn wirtualnych platformy Azure.

### <a name="create-a-custom-policy"></a>Tworzenie zasad niestandardowych

Jeśli wybrano utworzenie nowej zasady tworzenia kopii zapasowych, wprowadź ustawienia zasad.

1. W **obszarze Nazwa zasad**określ opisową nazwę.
2. W **harmonogramie tworzenia kopii zapasowych**określ, kiedy należy wykonać kopie zapasowe. Możesz wykonać codzienne lub tygodniowe kopie zapasowe dla maszyn wirtualnych platformy Azure.
3. W **obszarze Przywracanie błyskawiczne**określ, jak długo migawki mają być przechowywane lokalnie w celu natychmiastowego przywracania.
    * Podczas przywracania kopie zapasowe dysków maszyn wirtualnych są kopiowane z magazynu w sieci do lokalizacji magazynu odzyskiwania. Dzięki natychmiastowemu przywracaniu można korzystać z lokalnie przechowywanych migawek wykonanych podczas zadania tworzenia kopii zapasowej, bez czekania na przesłanie danych kopii zapasowej do magazynu.
    * Migawki można zachować do natychmiastowego przywracania przez okres od jednego do pięciu dni. Ustawieniem domyślnym są dwa dni.
4. W **obszarze Zakres przechowywania**określ, jak długo chcesz zachować dzienne lub tygodniowe punkty kopii zapasowej.
5. W **obszarze Przechowywanie miesięcznego punktu kopii zapasowej**określ, czy chcesz zachować miesięczną kopię zapasową dziennych czy cotygodniowych kopii zapasowych.
6. Kliknij przycisk **OK** zapisać zasady.

    ![Nowe zasady tworzenia kopii zapasowych](./media/backup-azure-arm-vms-prepare/new-policy.png)

> [!NOTE]
   > Usługa Azure Backup nie obsługuje automatycznego dostosowywania zegara w przypadku zmian czasu pracy dla kopii zapasowych maszyn wirtualnych platformy Azure. W miarę pojawiania się zmian czasu należy ręcznie modyfikować zasady tworzenia kopii zapasowych zgodnie z wymaganiami.

## <a name="trigger-the-initial-backup"></a>Wyzwalanie początkowej kopii zapasowej

Początkowa kopia zapasowa zostanie uruchomiona zgodnie z harmonogramem, ale można ją uruchomić natychmiast w następujący sposób:

1. W menu przechowalni kliknij polecenie **Zapasy elementów**.
2. W **obszarze Elementy kopii zapasowej**kliknij pozycję Maszyna **wirtualna platformy Azure**.
3. Na liście **Elementy kopii zapasowej** kliknij wielokropek (...).
4. Kliknij **pozycję Kopia zapasowa teraz**.
5. W **obszarze Kopia zapasowa teraz**użyj formantu kalendarza, aby wybrać ostatni dzień, w którym punkt odzyskiwania powinien zostać zachowany. Następnie kliknij przycisk **OK**.
6. Monitorowanie powiadomień portalu. Postęp zadania na pulpicie nawigacyjnym magazynu można monitorować > **Zadania** > kopii zapasowej**w toku**. W zależności od rozmiaru maszyny wirtualnej tworzenie początkowej kopii zapasowej może potrwać pewien czas.

## <a name="verify-backup-job-status"></a>Weryfikowanie stanu zadania kopii zapasowej

Szczegóły zadania kopii zapasowej dla każdej kopii zapasowej maszyny Wirtualnej składają się z dwóch faz, fazy **migawki,** po której następuje **faza Transferu danych do magazynu.**<br/>
Faza migawki gwarantuje dostępność punktu odzyskiwania przechowywane wraz z dysków do **przywracania błyskawicznego** i są dostępne przez maksymalnie pięć dni w zależności od przechowywania migawki skonfigurowane przez użytkownika. Transfer danych do magazynu tworzy punkt odzyskiwania w magazynie dla długoterminowego przechowywania. Przenoszenie danych do magazynu rozpoczyna się dopiero po zakończeniu fazy migawki.

  ![Stan zadania kopii zapasowej](./media/backup-azure-arm-vms-prepare/backup-job-status.png)

Istnieją dwa **zadania podrzędne** uruchomione w wewnętrznej bazy danych, jeden dla zadania tworzenia kopii zapasowej front-end, które można sprawdzić z bloku Szczegóły **zadania kopii zapasowej,** jak podano poniżej:

  ![Stan zadania kopii zapasowej](./media/backup-azure-arm-vms-prepare/backup-job-phase.png)

Faza **Transferu danych do magazynu** może potrwać wiele dni, w zależności od rozmiaru dysków, zmian na dysku i kilku innych czynników.

Stan zadania może się różnić w zależności od następujących scenariuszy:

**Migawka** | **Przenoszenie danych do magazynu** | **Stan zadania**
--- | --- | ---
Zakończone | W toku | W toku
Zakończone | Pominięto | Zakończone
Zakończone | Zakończone | Zakończone
Zakończone | Niepowodzenie | Uzupełnione ostrzeżeniem
Niepowodzenie | Niepowodzenie | Niepowodzenie

Teraz z tej możliwości, dla tej samej maszyny Wirtualnej, dwie kopie zapasowe można uruchomić równolegle, ale w każdej fazie (migawka, transfer danych do magazynu) tylko jedno zadanie podrzędne mogą być uruchomione. Tak więc w scenariuszach były zadanie tworzenia kopii zapasowej w toku spowodowało następnego dnia kopii zapasowej do niepowodzenia będzie unikać z tej funkcji oddzielenia. Kopie zapasowe następnego dnia mogą mieć wykonaną migawkę podczas **przesyłania danych do magazynu** pominięte, jeśli zadanie kopii zapasowej poprzedniego dnia jest w toku.
Przyrostowy punkt odzyskiwania utworzony w przechowalni przechwytuje wszystkie zmiany z ostatniego punktu odzyskiwania utworzonego w przechowalni. Nie ma wpływu na koszt użytkownika.

## <a name="optional-steps"></a>Kroki opcjonalne

### <a name="install-the-vm-agent"></a>Instalowanie agenta maszyny wirtualnej

Usługa Azure Backup kopii zapasowej maszyn wirtualnych platformy Azure, instalując rozszerzenie agenta maszyny wirtualnej platformy Azure uruchomionego na komputerze. Jeśli maszyna wirtualna została utworzona na podstawie obrazu portalu Azure Marketplace, agent jest instalowany i uruchamiany. Jeśli utworzysz niestandardową maszynę wirtualną lub zmigrujesz komputer lokalny, może być konieczne ręczne zainstalowanie agenta, zgodnie z podsumowaniem w tabeli.

**Vm** | **Szczegóły**
--- | ---
**Windows** | 1. [Pobierz i zainstaluj](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) plik MSI agenta.<br/><br/> 2. Zainstaluj z uprawnieniami administratora na komputerze.<br/><br/> 3. Sprawdź instalację. W *oknie C:\WindowsAzure\Packages* na maszynie wirtualnej kliknij prawym przyciskiem myszy polecenie **Właściwości programu WaAppAgent.exe** > **Properties**. Na karcie **Szczegóły** **wersja produktu** powinna mieć wersję 2.6.1198.718 lub wyższą.<br/><br/> Jeśli aktualizujesz agenta, upewnij się, że nie są uruchomione żadne operacje tworzenia kopii zapasowej, i [zainstaluj ponownie agenta](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
**Linux** | Zainstaluj przy użyciu rpm lub pakietu DEB z repozytorium pakietów dystrybucji. Jest to preferowana metoda instalowania i uaktualniania agenta systemu Azure Linux. Wszyscy [dostawcy zatwierdzonych dystrybucji](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integrują pakiet agenta usługi Azure Linux z ich obrazami i repozytoriami. Agent jest dostępny na [GitHub](https://github.com/Azure/WALinuxAgent), ale nie zalecamy instalowania stamtąd.<br/><br/> Jeśli aktualizujesz agenta, upewnij się, że nie są uruchomione żadne operacje tworzenia kopii zapasowej i zaktualizuj pliki binarne.

>[!NOTE]
> **Usługa Azure Backup obsługuje teraz selektywną kopię zapasową dysku i przywracanie przy użyciu rozwiązania do tworzenia kopii zapasowych maszyny wirtualnej platformy Azure.**
>
>Obecnie usługa Azure Backup obsługuje tworzenie kopii zapasowych wszystkich dysków (system operacyjny i dane) na maszynie wirtualnej przy użyciu rozwiązania do tworzenia kopii zapasowych maszyny wirtualnej. Dzięki funkcji wykluczeń dysku można uzyskać opcję tworzenia kopii zapasowej jednego lub kilku z wielu dysków z danymi na maszynie Wirtualnej. Zapewnia to wydajne i ekonomiczne rozwiązanie dla potrzeb związanych z tworzeniem kopii zapasowych i przywracania. Każdy punkt odzyskiwania zawiera dane dysków uwzględnionych w operacji tworzenia kopii zapasowej, co dodatkowo umożliwia przywrócenie podzbioru dysków z danego punktu odzyskiwania podczas operacji przywracania. Dotyczy to przywracania zarówno z migawki, jak i z magazynu.
>
>**Aby zapisać się do podglądu, napisz do nas naAskAzureBackupTeam@microsoft.com**

## <a name="next-steps"></a>Następne kroki

* Rozwiąż wszelkie problemy z [agentami maszyn wirtualnych platformy Azure](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) lub [kopią zapasową maszyn wirtualnych platformy Azure.](backup-azure-vms-troubleshoot.md)
* [Przywracanie](backup-azure-arm-restore-vms.md) Maszyny wirtualne platformy Azure.
