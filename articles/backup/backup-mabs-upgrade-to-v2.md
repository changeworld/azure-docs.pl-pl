---
title: Instalowanie usługi Azure Backup Server w wersji 2
description: Usługa Azure Backup Server v2 udostępnia udoskonalone funkcje tworzenia kopii zapasowej na potrzeby ochrony maszyn wirtualnych, pliki i foldery oraz obciążeń. Informacje o sposobie instalowania lub uaktualniania do serwera Azure Backup Server w wersji 2.
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: adigan
ms.openlocfilehash: 4f1e0c14d3a835b9f6d739511186bdcc19917a7a
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51230997"
---
# <a name="install-azure-backup-server-v2"></a>Instalowanie usługi Azure Backup Server w wersji 2

Usługa Azure Backup Server pomaga chronić maszyny wirtualne (VM), obciążenia, pliki i foldery i więcej. Usługa Azure Backup Server v2 jest oparta na usłudze Azure Backup Server w wersji 1 i udostępnia nowe funkcje, które nie są dostępne w wersji 1. Porównanie funkcji w wersji 1 i 2 w temacie [macierz ochrony usługi Azure Backup Server](backup-mabs-protection-matrix.md). 

Dodatkowe funkcje w wersji 2 Utwórz kopię zapasową serwera zostały uaktualnione z kopii zapasowej serwera w wersji 1. Utwórz kopię zapasową serwera w wersji 1 nie jest jednak wymagania wstępne dotyczące instalacji Backup Server w wersji 2. Jeśli chcesz uaktualnić z kopii zapasowej serwera w wersji 1 do kopii zapasowej serwera w wersji 2, należy zainstalować Backup Server w wersji 2 na serwer ochrony Utwórz kopię zapasową serwera. Istniejące ustawienia kopii zapasowej serwera pozostają bez zmian.

Utwórz kopię zapasową serwera w wersji 2 można zainstalować w systemie Windows Server 2016 lub Windows Server 2012 R2. Aby móc korzystać z nowych funkcji, takich jak System Center 2016 danych ochrony Menedżera Modern Backup Storage, Utwórz kopię zapasową serwera w wersji 2 należy zainstalować w systemie Windows Server 2016. Zanim uaktualnisz do lub zainstaluj serwer kopii zapasowych w wersji 2, przeczytaj o [wymagania wstępne instalacji](https://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites).

> [!NOTE]
> Usługa Azure Backup Server ma ten sam kod podstawowy, jak System Center Data Protection Manager. Tworzenia kopii zapasowej serwera v1 jest równoznaczne z programu Data Protection Manager 2012 R2. Tworzenia kopii zapasowej serwera v2 jest równoznaczne z programu Data Protection Manager 2016. W tym artykule od czasu do czasu odwołuje się w dokumentacji programu Data Protection Manager.
>
>

## <a name="upgrade-backup-server-to-v2"></a>Uaktualnianie Backup Server do wersji 2
Aby przeprowadzić uaktualnienie z kopii zapasowej serwera w wersji 1 Backup Server w wersji 2, upewnij się, że Twoja instalacja zawiera wymagane aktualizacje:

- [Aktualizowanie agentów ochrony](backup-mabs-upgrade-to-v2.md#update-the-data-protection-manager-protection-agent) na serwerach chronionych.
- Uaktualnij system Windows Server 2012 R2 do systemu Windows Server 2016.
- Uaktualnij administracji zdalnej serwera kopii zapasowej Azure na wszystkich serwerach produkcyjnych.
- Upewnij się, że kopie zapasowe są ustawione, aby kontynuować bez ponownego uruchamiania serwera produkcyjnego.


### <a name="upgrade-steps-for-backup-server-v2"></a>Procedura uaktualniania dla kopii zapasowej serwera w wersji 2

1. W Centrum pobierania [Pobierz Instalator uaktualnienia](https://go.microsoft.com/fwlink/?LinkId=626082).

2. Po wyodrębnieniu Kreatora instalacji upewnij się, że **wykonania setup.exe** jest wybrany, a następnie wybierz **Zakończ**.

  ![Konfigurowanie Instalator — uruchom Instalatora](./media/backup-mabs-upgrade-to-v2/run-setup.png)

3. W Kreatorze serwera usługi Microsoft Azure Backup w ramach **zainstalować**, wybierz opcję **serwera usługi Microsoft Azure Backup**.

  ![Konfigurowanie Instalator — wybierz opcję instalacji](./media/backup-mabs-upgrade-to-v2/mabs-installer-s1.png)

4. Na **powitalnej** strony, przejrzyj ostrzeżenia, a następnie wybierz pozycję **dalej**.

  ![Instalator instalacji — strona powitalna](./media/backup-mabs-upgrade-to-v2/mabs-installer-s2.png)

5. Kreator instalacji przeprowadza kontrole warunków wstępnych, aby upewnij się, że w danym środowisku można uaktualnić. Na **funkcji sprawdzania wymagań wstępnych** wybierz opcję **Sprawdź**.

  ![Instalator — strona sprawdzania wymagań wstępnych Instalatora](./media/backup-mabs-upgrade-to-v2/mabs-installer-s3-perform-checks.png)

6. Środowisko musi pomyślnie przejść testy wymagań wstępnych. Jeśli środowisko nie zakończy się pomyślnie, należy pamiętać, problemy i je rozwiązać. Następnie wybierz **Sprawdź ponownie**. Po przejściu Sprawdzanie wymagań wstępnych wybierz **dalej**.

  ![Instalator instalacji — przycisk Sprawdź ponownie](./media/backup-mabs-upgrade-to-v2/mabs-installer-s4-pass-checks.png)

7. Na **ustawień serwera SQL** strony, wybierz odpowiednią opcję instalacji programu SQL, a następnie wybierz pozycję **Sprawdź i zainstaluj**.

  ![Instalator instalacji — strona ustawień serwera SQL](./media/backup-mabs-upgrade-to-v2/mabs-installer-s5-sql-settings.png)

  Sprawdzanie może potrwać kilka minut. Gdy testy są gotowe, wybierz **dalej**.

  ![Instalator instalacji — SQL Sprawdź ustawienia i przycisk Zainstaluj](./media/backup-mabs-upgrade-to-v2/mabs-installer-s5a-check-and-fix-settings.png)

8. Na **ustawienia instalacji** strony, wprowadź zmiany do lokalizacji, w którym zainstalowano serwer kopii zapasowej lub do lokalizacji pliki tymczasowe. Wybierz opcję **Dalej**.

  ![Instalator instalacji — strona Ustawienia instalacji](./media/backup-mabs-upgrade-to-v2/mabs-installer-s6-installation-settings.png)

9. Aby zakończyć pracę Kreatora instalacji, wybierz pozycję **Zakończ**.

  ![Instalator instalacji — zakończenie](./media/backup-mabs-upgrade-to-v2/run-setup.png)

## <a name="add-storage-for-modern-backup-storage"></a>Dodawanie magazynu dla funkcji Modern Backup Storage

Aby poprawić wydajność magazynu kopii zapasowych, Utwórz kopię zapasową serwera w wersji 2 dodaje obsługę woluminów. Tworzenia kopii zapasowej serwera v1 i v2 Backup Server obsługują dysków.

### <a name="add-volumes-and-disks"></a>Dodawanie woluminów i dysków

Jeśli uruchamiasz v2 kopii zapasowej serwera w systemie Windows Server 2016, można użyć woluminów do przechowywania danych kopii zapasowej. Woluminy oferują oszczędności pojemności magazynu oraz szybsze tworzenie kopii zapasowych. Ponieważ woluminy są nowe Utwórz kopię zapasową serwera, należy je dodać. 

Po dodaniu woluminu do serwera kopii zapasowych, możesz nadać woluminowi przyjazną nazwę. Wybierz **przyjazną nazwę** kolumny wolumin ma nazwę. Nazwę można zmienić później, jeśli to konieczne. Możesz również użyć programu PowerShell można dodawać lub zmieniać przyjazne nazwy dla woluminów.

Aby dodać wolumin w konsoli administratora:

1. W konsoli administratora serwera kopii zapasowych platformy Azure wybierz **zarządzania** > **Magazyn dyskowy** > **Dodaj**.

  ![Otwórz kreatora Dodawanie magazynu dyskowego](./media//backup-mabs-upgrade-to-v2/open-add-disk-storage-wizard.png)

  Zostanie otwarty Kreator Dodawanie magazynu dyskowego.

2. Na **Dodawanie magazynu dyskowego** stronie **dostępne woluminy** , wybierz wolumin, a następnie wybierz **Dodaj**.

3. W **wybrane woluminy** Wprowadź przyjazną nazwę dla woluminu, a następnie wybierz pozycję **OK**.

  ![Magazyn dyskowy Kreator dodawania — Dodawanie woluminu](./media/backup-mabs-upgrade-to-v2/add-volume.png)

  Jeśli chcesz dodać dysk, dysk musi należeć do grupy ochrony, która ma magazynu w starszej wersji. Te dyski, można użyć tylko dla tych grup ochrony. Jeśli serwer usługi Backup nie ma źródeł, które mają starszą ochroną, dysk nie ma na liście.

  Aby uzyskać więcej informacji na temat dodawania dysków, zobacz [dodawanie dysków w celu zwiększenia magazynu w starszej wersji](https://docs.microsoft.com/system-center/dpm/upgrade-to-dpm-2016#adding-disks-to-increase-legacy-storage). Nie można nadać przyjaznej nazwy dysku.


### <a name="assign-workloads-to-volumes"></a>Przypisywanie obciążeń do woluminów

Utwórz kopię zapasową serwera, należy określić w obciążeniach, które są przypisane do poszczególnych woluminów. Na przykład można ustawić kosztowne woluminy, które obsługuje dużą liczbę operacji wejścia/wyjścia na sekundę (IOPS) do przechowywania tylko w przypadku obciążeń, które wymagają częste, duże kopie zapasowe. Przykładem jest program SQL Server przy użyciu dzienników transakcji.

#### <a name="update-dpmdiskstorage"></a>Update-DPMDiskStorage

Aby zaktualizować właściwości woluminu w puli magazynów na serwerze kopii zapasowej, użyj polecenia cmdlet programu PowerShell **Update-DPMDiskStorage**.

Składnia:

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [<CommonParameters>]
```

Wszystkie zmiany wprowadzone przy użyciu programu PowerShell są odzwierciedlane w interfejsie użytkownika.


## <a name="protect-data-sources"></a>Ochrona źródeł danych
Aby rozpocząć ochronę źródeł danych, należy utworzyć grupy ochrony. Poniższe kroki, Wyróżnij zmiany i dodatki w Kreatorze nowej grupy ochrony.

Aby utworzyć grupę ochrony:

1. W konsoli administratora serwera kopii zapasowych wybierz **ochrony**.

2. Na wstążce narzędzi wybierz **New**.

  Zostanie otwarty Kreator tworzenia nowej grupy ochrony.

  ![Kreator tworzenia nowej grupy ochrony](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-1.png)

3. Na **stronie powitalnej** wybierz pozycję **Dalej**.

4. Na **wybierz typ grupy ochrony** wybierz typ grupy ochrony, które chcesz utworzyć, a następnie wybierz **dalej**.

  ![Strona typu wybierz grupę ochrony](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-2.png)

5. Na **Wybierz członków grupy** stronie **dostępni członkowie** polu członków z ochrony agentów są wyświetlane. W tym przykładzie wybierz woluminy D:\ i E:\, , a następnie dodać je do **wybrane elementy członkowskie**. Wybierz opcję **Dalej**.

  ![Wybierz grupę elementów członkowskich strony](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-3.png)

6. Na **wybierz metodę ochrony danych** wpisz **Nazwa grupy ochrony**, wybierz metodę ochrony, a następnie wybierz **dalej**. Jeśli chcesz ochrony krótkoterminowej, wybierz opcję **dysku** utworzyć kopię zapasową metodę.

  ![Wybierz metodę ochrony danych stronę](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-4.png)

7. Na **Określ cele krótkoterminowe** Wybierz szczegóły **zakres przechowywania** i **częstotliwość synchronizacji**. Następnie wybierz opcję **Dalej**. Opcjonalnie, aby zmienić harmonogram przy woluminów punktów odzyskiwania, wybierz **Modyfikuj**.

  ![Określ cele krótkoterminowe strony](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-5.png)

8. Na **Przejrzyj przydział magazynu dyskowego** strony, przejrzyj szczegóły dotyczące źródeł danych, zaznaczone, w tym rozmiar źródła danych, wartości miejsce do przygotowania i docelowy wolumin magazynu.

  ![Przejrzyj przydział magazynu dyskowego strony](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-6.png)

  Woluminy magazynu na podstawie alokacji woluminu obciążenia (ustawiona przy użyciu programu PowerShell) i dostępnego magazynu. Woluminy magazynu można zmienić, wybierając inne woluminy z menu rozwijanego. Jeśli zmienisz wartość **Magazyn docelowy**, wartość **dostępny Magazyn dyskowy** zmieniają się dynamicznie, aby odzwierciedlić wartości w obszarze **wolnego miejsca** i  **Underprovisioned miejsca**.

  Jeśli źródła danych zwiększą się zgodnie z planem wartość **miejsce** kolumny w **dostępny Magazyn dyskowy** odzwierciedla ilość dodatkowego magazynu, która jest potrzebna. Użyj tej wartości, aby ułatwić zaplanowanie potrzeb dotyczących magazynu dla bezproblemowego tworzenia kopii zapasowych. Jeśli ta wartość wynosi zero, istnieją nie potencjalnych problemów z magazynem w najbliższej przyszłości. Jeśli wartość jest różna od zera, nie ma wystarczającej ilości miejsca przydzielone (na podstawie zasad ochrony i rozmiaru danych chronionych elementów członkowskich).

  ![Przydział magazynu dyskowego](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-7.png)

  Aby zakończyć tworzenie grupy ochrony, Zakończ pracę kreatora.

## <a name="migrate-legacy-storage-to-modern-backup-storage"></a>Migrowanie starszej wersji magazynu do funkcji Modern Backup Storage
Po uaktualnieniu do wersji lub zainstaluj serwer kopii zapasowych w wersji 2, a następnie Uaktualnij system operacyjny systemu Windows Server 2016 można zaktualizować grup ochrony do użycia Modern Backup Storage. Domyślnie grupy ochrony nie są zmieniane. One nadal działać zgodnie z początkowo zostały ustawione. 

Aktualizowanie grup ochrony w celu użycia Modern Backup Storage jest opcjonalne. Aby zaktualizować grupę ochrony, Zatrzymaj ochronę wszystkich źródeł danych przy użyciu opcji Zachowaj dane. Następnie dodaj źródła danych do nowej grupy ochrony.

1. W konsoli administratora System Center 2016 DPM wybierz **ochrony** funkcji. W **elementu członkowskiego grupy ochrony** listy, kliknij prawym przyciskiem myszy element członkowski, a następnie wybierz **Zatrzymaj ochronę członka**.

  ![Zatrzymaj ochronę członka](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-stop-protection1.png)

2. W **Usuń z grupy** okna dialogowego przejrzyj używane miejsce na dysku i dostępne wolne miejsce w puli magazynów. Wartość domyślna to pozostawienie punktów odzyskiwania na dysku i umożliwić im wygaśnie za skojarzonych zasad przechowywania. Kliknij przycisk **OK**.

  Aby natychmiast przywrócić zajęte miejsce na dysku do wolnej puli magazynów, należy zaznaczyć **Usuń replikę z dysku** pole wyboru, aby usunąć dane kopii zapasowej (i punktów odzyskiwania) skojarzonych z tym elementem członkowskim.

  ![Usuń z grupy, okno dialogowe](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-retain-data.png)

3. Utwórz grupę ochrony, która używa funkcji Modern Backup Storage. Dołącz źródła danych niechronione.


## <a name="add-disks-to-increase-legacy-storage"></a>Dodawanie dysków w celu zwiększenia magazynu w starszej wersji

Jeśli chcesz używać starszej wersji magazynu z kopii zapasowej serwera, może być konieczne dodanie dysków w celu zwiększenia magazynu w starszej wersji. 

Aby dodać magazyn dyskowy:

1. W konsoli administratora System Center 2016 DPM wybierz **zarządzania** > **Magazyn dyskowy** > **Dodaj**.

  ![Dodaj okno dialogowe Magazyn dyskowy](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-add-disk-storage.png)

2. W **Dodawanie magazynu dyskowego** okno dialogowe, wybierz opcję **dodawać dyski**.

3. Na liście dostępnych dysków Wybierz dyski które chcesz dodać. Wybierz **Dodaj**, a następnie wybierz pozycję **OK**.

## <a name="update-the-data-protection-manager-protection-agent"></a>Zaktualizuj agenta ochrony programu Data Protection Manager

Utwórz kopię zapasową serwera, używa agenta ochrony programu System Center Data Protection Manager aktualizacji. Jeśli wykonujesz uaktualnienie agenta ochrony, która nie jest połączony z siecią, nie można użyć konsoli administratora programu Data Protection Manager do ukończenia uaktualnienia podłączonego agenta. Należy uaktualnić agenta ochrony w środowisku domeny nieaktywny. Konsoli administratora programu Data Protection Manager, dopóki komputer kliencki jest połączony z siecią, pokazuje, oczekuje aktualizacji agenta ochrony.

Poniżej opisano sposób aktualizowania agentów ochrony na komputerach klienckich, które są połączone oraz dla komputerów klienckich, które nie są połączone.

### <a name="update-a-protection-agent-for-a-connected-client-computer"></a>Zaktualizuj agenta ochrony na podłączonym komputerze klienckim

1. W konsoli administratora serwera kopii zapasowych wybierz **zarządzania** > **agentów**.

2. W okienku wyświetlania wybierz komputery klienckie, dla których chcesz zaktualizować agenta ochrony.

  > [!NOTE]
  > **Aktualizacji agenta** kolumna wskazuje, kiedy aktualizacji agenta ochrony jest dostępna dla każdego chronionego komputera. W **akcje** okienku **aktualizacji** akcja jest dostępna tylko wtedy, gdy komputer chroniony jest zaznaczony i są dostępne aktualizacje.

3. Aby zainstalować zaktualizowanych agentów ochrony na wybranych komputerach w **akcje** okienku wybierz **aktualizacji**.

### <a name="update-a-protection-agent-on-a-client-computer-that-is-not-connected"></a>Zaktualizuj agenta ochrony na komputerze klienckim, który nie jest połączony

1. W konsoli administratora serwera kopii zapasowych wybierz **zarządzania** > **agentów**.

2. W okienku wyświetlania wybierz komputery klienckie, dla których chcesz zaktualizować agenta ochrony.

  > [!NOTE]
  > **Aktualizacji agenta** kolumna wskazuje, kiedy aktualizacji agenta ochrony jest dostępna dla każdego chronionego komputera. W **akcje** okienku **aktualizacji** akcja nie jest dostępna, gdy komputer chroniony jest zaznaczony, o ile nie są dostępne aktualizacje.

3. Aby zainstalować zaktualizowanych agentów ochrony na wybranych komputerach, należy wybrać **aktualizacji**.

4. Na komputerze klienckim, który nie jest połączony z siecią, dopóki komputer jest połączony z siecią **stan agenta** kolumna pokazuje stan **oczekująca aktualizacja**.

  Gdy komputer kliencki jest połączony z siecią **aktualizacji agenta** kolumny dla komputera klienckiego wskazuje stan **aktualizowanie**.
  
### <a name="move-legacy-protection-groups-from-the-old-version-and-sync-the-new-version-with-azure"></a>Przenieś grupy starszą ochroną ze starej wersji i synchronizowanie nowej wersji przy użyciu platformy Azure

Po zaktualizowaniu są zarówno usługi Azure Backup Server i systemu operacyjnego, możesz przystąpić do ochrony nowych źródeł danych przy użyciu Modern Backup Storage. Źródła danych, które są już chronione w dalszym ciągu być chroniony, ponieważ były one w usłudze Azure Backup Server (starsza wersja). Wszystkie nowe grupy ochrony, użyj Modern Backup Storage.

Aby przeprowadzić migrację źródła danych z starsza wersja trybu ochrony do funkcji Modern Backup Storage:

1.  Dodaj nowych woluminów do puli magazynu programu Data Protection Manager. Można również przypisać przyjazną nazwę i wybrać tagi źródła danych.

2. Dla każdego źródła danych, który jest w trybie starszej wersji Zatrzymaj ochronę źródła danych. Następnie wybierz **Zachowaj chronione dane**.  Dzięki temu odzyskiwania starych punktów odzyskiwania po migracji.

3. Utwórz nową grupę ochrony. Następnie wybierz źródła danych, które mają być przechowywane przy użyciu nowego formatu.

  Data Protection Manager jest przechowywana kopia repliki ze starszej wersji magazynu kopii zapasowych lokalnie na woluminie Modern Backup Storage.
    > [!NOTE] 
    > Tworzenie kopii pojawia się jako zadania po odzyskiwaniu operacji.

  Wszystkie nowe synchronizacja i punkty odzyskiwania są następnie zapisywane w Modern Backup Storage.

  Stare punkty odzyskiwania są oczyszczane się jak wygasają. Ponieważ stare punkty odzyskiwania zostaną usunięte, miejsca na dysku zostanie zwolniona.

  Po usunięciu wszystkich starszych woluminów ze starego magazynu, możesz usunąć dysk z usługi Azure Backup. Następnie możesz usunąć dysk z systemu.

4. Tworzenie kopii zapasowych bazy danych programu Data Protection Manager.

  > [!IMPORTANT]
  > - Nowa nazwa serwera musi być taką samą nazwę jak oryginalne wystąpienie usługi Azure Backup Server. Nie można zmienić nazwy nowego wystąpienia usługi Azure Backup Server, jeśli chcesz użyć poprzedniej puli magazynu i bazy danych programu Data Protection Manager do przechowywania punktów odzyskiwania.
  > - Musi mieć kopię zapasową bazy danych programu Data Protection Manager. Należy przywrócić bazę danych.

5. Wyłączony oryginalnego wystąpienia usługi Azure Backup Server, lub przełącz serwer w tryb offline.

6. Resetuj konta komputera w usłudze Active Directory.

7. Zainstaluj system Windows Server 2016 na nowej maszynie. Dla nazwy serwera należy użyć takiej samej nazwie komputera jako oryginalnego wystąpienia serwera usługi Azure Backup.

8. Dołącz do domeny.

9. Zainstaluj usługę Azure Backup Server w wersji 2. (Usuń dyski puli magazynów programu Data Protection Manager ze starego serwera i zaimportuj je do nowego serwera).

10. Przywracanie bazy danych programu Data Protection Manager, który został utworzony w kroku 4.

11. Dołącz magazyn z oryginalnego serwera kopii zapasowej do nowego serwera.

12. W programie SQL Server należy przywrócić bazę danych programu Data Protection Manager.

13. W wierszu polecenia administratora na nowym serwerze, należy użyć `cd` można przejść do folderu lokalizacji i bin instalacji kopia zapasowa Microsoft Azure.  

  Przykład:  
  C:\Windows\System32 > cd "c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\ do usługi Azure Backup

14. Uruchom polecenie `DPMSYNC -SYNC`.
  
  > [!NOTE]
  > Jeśli dodano *nowe* Uruchom dyski do puli magazynów programu Data Protection Manager zamiast przenoszenia stare `DPMSYNC -Reallocatereplica`.

## <a name="new-powershell-cmdlets-in-backup-server-v2"></a>Nowe polecenia cmdlet programu PowerShell w wersji 2 Backup Server

Po zainstalowaniu usługi Azure Backup Server w wersji 2, dostępne są dwa nowe polecenia cmdlet: 
* [Mount-DPMRecoveryPoint](https://technet.microsoft.com/library/mt787159.aspx)
* [Dismount-DPMRecoveryPoint](https://technet.microsoft.com/library/mt787158.aspx)

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak przygotować serwer lub rozpoczęciu ochrony obciążenia:
- [Przygotowywanie obciążeń serwera usługi Backup](backup-azure-microsoft-azure-backup.md)
- [Tworzenie kopii zapasowej serwera VMware przy użyciu kopii zapasowej serwera](backup-azure-backup-server-vmware.md)
- [Tworzenie kopii zapasowych programu SQL Server przy użyciu kopii zapasowej serwera](backup-azure-sql-mabs.md)
- [Modern Backup Storage za pomocą kopii zapasowej serwera](backup-mabs-add-storage.md)

