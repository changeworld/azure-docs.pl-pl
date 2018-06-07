---
title: Zainstaluj serwer kopii zapasowej systemu Azure w wersji 2
description: Azure v2 Utwórz kopię zapasową serwera udostępnia udoskonalone funkcje tworzenia kopii zapasowej ochrony maszyn wirtualnych, plików i folderów oraz obciążeń. Informacje o sposobie instalacji lub uaktualnienia do serwera usługi Kopia zapasowa Azure w wersji 2.
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: adigan
ms.openlocfilehash: 27da52a46b52e06a8ea4f80fa06487aca34c1c3c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606957"
---
# <a name="install-azure-backup-server-v2"></a>Zainstaluj serwer kopii zapasowej systemu Azure w wersji 2

Serwer kopii zapasowej systemu Azure pomaga w ochronie maszyn wirtualnych (VM), obciążenia, pliki i foldery i więcej. Azure v2 Utwórz kopię zapasową serwera oparty na serwer kopii zapasowej Azure w wersji 1 i udostępnia nowe funkcje, które nie są dostępne w wersji 1. Porównanie funkcji między v1 i v2, zobacz [macierzy ochrona serwer kopii zapasowej Azure](backup-mabs-protection-matrix.md). 

Dodatkowe funkcje serwera kopii zapasowej w wersji 2 są uaktualnienie v1 Utwórz kopię zapasową serwera. Jednak v1 Utwórz kopię zapasową serwera nie jest wymagane w przypadku instalowania serwera kopii zapasowej w wersji 2. Jeśli chcesz uaktualnić serwer zapasowy v1 do v2 Utwórz kopię zapasową serwera, należy zainstalować v2 Utwórz kopię zapasową serwera na serwerze ochrony Utwórz kopię zapasową serwera. Istniejących ustawień serwera kopii zapasowej pozostaną nienaruszone.

Utwórz kopię zapasową serwera v2 można zainstalować w systemie Windows Server 2012 R2 lub Windows Server 2016. Aby móc korzystać z nowych funkcji, takich jak System Center 2016 ochrony Menedżera Modern kopii zapasowej pamięci masowej, należy zainstalować v2 Utwórz kopię zapasową serwera w systemie Windows Server 2016. Przed uaktualnić do wersji lub zainstalować serwer zapasowy v2, przeczytaj o [wymagania wstępne instalacji](https://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites).

> [!NOTE]
> Serwer kopii zapasowej systemu Azure ma taki sam kod podstawowej jako System Center Data Protection Manager. V1 serwera kopii zapasowej jest odpowiednikiem programu Data Protection Manager 2012 R2, a serwer zapasowy v2 jest odpowiednikiem programu Data Protection Manager 2016. W tym artykule od czasu do czasu odwołuje się w dokumentacji programu Data Protection Manager.
>
>

## <a name="upgrade-backup-server-to-v2"></a>Utwórz kopię zapasową serwera uaktualnienia do wersji 2
Aby uaktualnić v1 Utwórz kopię zapasową serwera do serwera kopii zapasowej w wersji 2, upewnij się, że instalacji jest wymagane aktualizacje:

- [Aktualizowanie agentów ochrony](backup-mabs-upgrade-to-v2.md#update-the-data-protection-manager-protection-agent) na chronionych serwerach.
- Uaktualnienie systemu Windows Server 2012 R2 do systemu Windows Server 2016.
- Uaktualnij administratora zdalnego serwera kopii zapasowej platformy Azure na wszystkich serwerach produkcyjnych.
- Upewnij się, że kopie zapasowe są ustawione, aby kontynuować bez ponownego uruchomienia serwera produkcyjnego.


### <a name="upgrade-steps-for-backup-server-v2"></a>Kroki uaktualniania dla kopii zapasowej serwera w wersji 2

1. W Centrum pobierania [Pobierz instalatora uaktualnienia](https://go.microsoft.com/fwlink/?LinkId=626082).

2. Po wyodrębnieniu Kreatora instalacji upewnij się, że **wykonania setup.exe** jest wybrany, a następnie wybierz **Zakończ**.

  ![Instalator Instalatora — należy uruchomić Instalator](./media/backup-mabs-upgrade-to-v2/run-setup.png)

3. W Kreatorze serwer kopii zapasowej Microsoft Azure w obszarze **zainstalować**, wybierz pozycję **serwer kopii zapasowej Microsoft Azure**.

  ![Instalator Instalatora — wybierz opcję instalacji](./media/backup-mabs-upgrade-to-v2/mabs-installer-s1.png)

4. Na **powitalnej** , przejrzyj ostrzeżenia, a następnie wybierz **dalej**.

  ![Instalator Instalatora - strony powitalnej](./media/backup-mabs-upgrade-to-v2/mabs-installer-s2.png)

5. Kreator instalacji wykonuje sprawdzanie wymagań wstępnych do upewnij się, że w środowisku można uaktualnić. Na **wymagań wstępnych sprawdza** wybierz pozycję **Sprawdź**.

  ![Instalator — strona kontroli wymagań wstępnych Instalatora](./media/backup-mabs-upgrade-to-v2/mabs-installer-s3-perform-checks.png)

6. Środowisko musi przejść testy wymagań wstępnych. Jeśli w środowisku nie zakończy się pomyślnie, należy pamiętać o zasadach i popraw je. Następnie wybierz opcję **Sprawdź ponownie**. Po przekazujesz Sprawdzanie wymagań wstępnych, wybierz **dalej**.

  ![Instalator Instalatora — przycisk Sprawdź ponownie](./media/backup-mabs-upgrade-to-v2/mabs-installer-s4-pass-checks.png)

7. Na **ustawienia SQL** , wybierz odpowiednią opcję instalacji SQL, a następnie wybierz **Sprawdź i zainstaluj**.

  ![Instalator Instalatora — strona ustawień SQL](./media/backup-mabs-upgrade-to-v2/mabs-installer-s5-sql-settings.png)

  Kontrole może potrwać kilka minut. Po zakończeniu kontroli, wybierz **dalej**.

  ![Instalator Instalatora — Sprawdź ustawienia SQL i przycisk Zainstaluj](./media/backup-mabs-upgrade-to-v2/mabs-installer-s5a-check-and fix-settings.png)

8. Na **ustawienia instalacji** strony, wprowadź zmiany do lokalizacji, w którym zainstalowano Utwórz kopię zapasową serwera lub do lokalizacji przechowywania plików tymczasowych. Wybierz opcję **Dalej**.

  ![Instalator Instalatora — strona Ustawienia instalacji](./media/backup-mabs-upgrade-to-v2/mabs-installer-s6-installation-settings.png)

9. Aby zakończyć pracę Kreatora instalacji, wybierz **Zakończ**.

  ![Instalator Instalatora — zakończenie](./media/backup-mabs-upgrade-to-v2/run-setup.png)



## <a name="add-storage-for-modern-backup-storage"></a>Dodawanie magazynu dla nowoczesnych magazynu kopii zapasowej

Aby zwiększyć wydajność magazynu kopii zapasowych, Utwórz kopię zapasową serwera v2 dodaje obsługę woluminów. Jak serwer zapasowy v1 v2 Utwórz kopię zapasową serwera obsługuje dyski.

### <a name="add-volumes-and-disks"></a>Dodaj do woluminów i dysków
Po uruchomieniu v2 Utwórz kopię zapasową serwera w systemie Windows Server 2016 może użyć woluminów do przechowywania danych kopii zapasowej. Woluminy oferują oszczędności pojemności magazynu i szybsze tworzenie kopii zapasowych. Ponieważ woluminów dopiero zaczynasz korzystać z kopii zapasowej serwera, należy je dodać. 

Podczas dodawania woluminu do serwera kopii zapasowej, można nadać wolumin przyjazną nazwę. Kliknij przycisk **przyjazną nazwę** kolumny woluminu chcesz nazwać. Nazwę można zmienić później, jeśli to konieczne. Również służy PowerShell dodać lub zmienić przyjazne nazwy dla woluminów.

Aby dodać wolumin w konsoli administratora:

1. W konsoli administratora serwera kopii zapasowej Azure wybierz **zarządzania** > **Magazyn dyskowy** > **Dodaj**.

    ![Otwórz kreatora Dodawanie magazynu dyskowego](./media//backup-mabs-upgrade-to-v2/open-add-disk-storage-wizard.png)

    Spowoduje to otwarcie kreatora Dodawanie magazynu dysku.

2. Na **dodać magazyn dyskowy** strony w **dostępnych woluminów** , wybierz wolumin, a następnie wybierz **Dodaj**.
3. W **wybrane woluminy** Wprowadź przyjazną nazwę dla woluminu, a następnie wybierz **OK**.

      ![Magazyn dyskowy Kreator dodawania — Dodawanie woluminu](./media/backup-mabs-upgrade-to-v2/add-volume.png)

  Jeśli chcesz dodać dysk, dysk musi należeć do grupy ochrony, która ma starszej wersji magazynu. Te dyski mogą być używane tylko dla tych grup ochrony. Jeśli serwer zapasowy nie ma źródeł, które mają ochronę za pomocą starszej wersji, dysku nie ma na liście.

  Aby uzyskać więcej informacji na temat dodawania dysków, zobacz [dodawania dysków, aby zwiększyć ilość miejsca starszych](http://docs.microsoft.com/system-center/dpm/upgrade-to-dpm-2016#adding-disks-to-increase-legacy-storage). Nie można nadać przyjazną nazwę dysku.


### <a name="assign-workloads-to-volumes"></a>Przypisz obciążeń do woluminów

Utwórz kopię zapasową serwera, należy określić w obciążeń, które są przypisane do woluminów. Na przykład można ustawić kosztowne woluminów, które obsługują dużej liczby operacji wejścia/wyjścia na sekundę (IOPS) do przechowywania tylko w przypadku obciążeń, które wymagają częstego, dużej liczby kopii zapasowych. Przykładem jest program SQL Server z dzienników transakcji.

#### <a name="update-dpmdiskstorage"></a>Update-DPMDiskStorage

Aby zaktualizować właściwości woluminu w puli magazynu w serwerze kopii zapasowej, użyj polecenia cmdlet programu PowerShell DPMDiskStorage aktualizacji.

Składnia:

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```

Wszystkie zmiany wprowadzone przy użyciu programu PowerShell są odzwierciedlane w interfejsie użytkownika.


## <a name="protect-data-sources"></a>Ochrona źródeł danych
Aby rozpocząć, ochrona źródeł danych, należy utworzyć grupę ochrony. Poniższe kroki zaznacz zmiany lub dodatki w Kreatorze nowej grupy ochrony.

Aby utworzyć grupy ochrony:

1. W konsoli administratora serwera kopii zapasowej wybierz **ochrony**.

2. Na wstążce narzędzi wybierz **nowy**.

    Spowoduje to otwarcie Kreatora tworzenia nowej grupy ochrony.

  ![Kreator tworzenia nowej grupy ochrony](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-1.png)

3. Na **stronie powitalnej** wybierz pozycję **Dalej**.
4. Na **wybierz typ grupy ochrony** wybierz typ grupy ochrony, które chcesz utworzyć, a następnie wybierz **dalej**.

  ![Strona Typ wybierz grupę ochrony](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-2.png)

5. Na **Wybierz członków grupy** strony w **dostępni członkowie** okienka, członków z ochrony agenci są wyświetlane. Na przykład wybierz wolumin D:\ i E:\ i dodaj je do **wybranych składników** okienka. Wybierz opcję **Dalej**.

  ![Wybierz grupy elementów członkowskich strony](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-3.png)

6. Na **wybierz metodę ochrony danych** wprowadź **Nazwa grupy ochrony**, wybierz metodę ochrony, a następnie wybierz **dalej**. Jeśli chcesz uzyskać krótkoterminową ochronę, musisz wybrać **dysku** kopii zapasowej metody.

  ![Wybierz metodę ochrony danych strony](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-4.png)

7. Na **Określ cele krótkoterminowe** Wybierz szczegóły **zakres przechowywania** i **częstotliwość synchronizacji**. Następnie wybierz opcję **dalej**. Opcjonalnie, aby zmienić harmonogram przy woluminów punktów odzyskiwania, wybierz **Modyfikuj**.

  ![Określ cele krótkoterminowe strony](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-5.png)

8. Na **Przejrzyj przydział magazynu dyskowego** strony, przejrzyj szczegółowych informacji o źródeł danych wybranego, rozmiar i wartości miejsce do przygotowania i wolumin magazynu docelowego.

  ![Przejrzyj przydział magazynu dyskowego strony](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-6.png)

  Woluminy magazynu są oparte na alokacji woluminu obciążenia (Ustaw przy użyciu programu PowerShell) i dostępnej pojemności magazynu. Woluminy magazynu można zmienić, wybierając w menu rozwijanym inne woluminy. W przypadku zmiany wartości **docelowy magazyn**, wartość **magazynu dostępnego dysku** dynamicznie zmiany do wartości w obszarze **wolnego miejsca** i **Underprovisioned miejsca**.

  Jeśli źródeł danych wzrostu zgodnie z harmonogramem, wartość **Underprovisioned miejsca** kolumny w **magazynu dostępnego dysku** odzwierciedla ilość dodatkowego magazynu, które ma potrzebne. Użyj tej wartości, aby ułatwić planowanie zapotrzebowanie na pamięć smooth kopii zapasowych. Jeśli wartość wynosi zero, nie ma żadnych potencjalnych problemów z magazynem, w najbliższej przyszłości. Jeśli wartość jest liczbą różne od zera, nie ma wystarczającej ilości miejsca przydzielone (na podstawie zasad ochrony i rozmiar danych z chronionych elementów członkowskich).

  ![Magazyn dyskowy niedostatecznej alokacji](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-7.png)

   Aby zakończyć tworzenie grupy ochrony, Zakończ pracę kreatora.

## <a name="migrate-legacy-storage-to-modern-backup-storage"></a>Migrowanie starszych magazynu do nowoczesnych magazynu kopii zapasowej
Po przeprowadzeniu uaktualnienia lub instalacji v2 Utwórz kopię zapasową serwera i uaktualniania systemu operacyjnego do systemu Windows Server 2016, należy zaktualizować używać nowoczesnych magazynu kopii zapasowej grup ochrony. Domyślnie nie są zmieniane grup ochrony. One nadal działały tak początkowo zostały ustawione. 

Aktualizowanie grupy ochrony w celu użycia nowoczesnych magazynu kopii zapasowej jest opcjonalna. Aby zaktualizować grupy ochrony, Zatrzymaj ochronę wszystkich źródeł danych przy użyciu opcji Zachowaj dane. Następnie dodaj źródła danych do nowej grupy ochrony.

1. W konsoli administratora, wybierz **ochrony** funkcji. W **członka grupy ochrony** listy, kliknij prawym przyciskiem myszy element członkowski, a następnie wybierz **Zatrzymaj ochronę członka**.

  ![Zatrzymaj ochronę członka](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-stop-protection1.png)

2. W **Usuń z grupy** okna dialogowego przejrzyj zajęte miejsce na dysku i wolnego miejsca w puli magazynów. Wartość domyślna to Pozostaw punktów odzyskiwania na dysku i zezwolić im na wygaśnie za skojarzonych zasad przechowywania. Kliknij przycisk **OK**.

  Jeśli chcesz od razu Zwróć zajęte miejsce na dysku do puli wolnego miejsca, wybierz **Usuń replikę z dysku** pole wyboru, aby usunąć dane kopii zapasowej (i punktów odzyskiwania) skojarzonego z tym członkiem.

  ![Usuń z grupy, okno dialogowe](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-retain-data.png)

3. Utwórz grupę ochrony, która używa nowoczesnej magazynu kopii zapasowej. Dołącz źródła dane niechronione.


## <a name="add-disks-to-increase-legacy-storage"></a>Dodawanie dysków, aby zwiększyć ilość miejsca w starszej wersji

Jeśli chcesz użyć starszego magazynu kopii zapasowej serwera, może być konieczne dodawanie dysków, aby zwiększyć ilość miejsca w starszej wersji. 

Aby dodać magazyn danych na dysku:

1. W konsoli administratora, wybierz **zarządzania** > **Magazyn dyskowy** > **Dodaj**.

    ![Magazyn dyskowy okno dialogowe Dodawanie](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-add-disk-storage.png)

4. W **dodać magazyn dyskowy** okno dialogowe, wybierz opcję **Dodaj dyski**.

5. Na liście dostępnych dysków, wybierz dyski, które chcesz dodać, wybierz **Dodaj**, a następnie wybierz **OK**.

## <a name="update-the-data-protection-manager-protection-agent"></a>Zaktualizuj agenta ochrony programu Data Protection Manager

Utwórz kopię zapasową serwera używa agenta ochrony programu System Center Data Protection Manager do aktualizacji. Jeśli przeprowadzasz uaktualnienie agenta ochrony, która nie jest połączona z siecią, nie można użyć konsoli administratora usługi Menedżer ochrony danych przeprowadzenie uaktualnienia podłączonego agenta. Należy uaktualnić agenta ochrony w środowisku domeny nieaktywny. Dopóki komputer kliencki jest połączony z siecią, danych ochrony konsoli administratora programu Manager zawiera oczekuje aktualizacji agenta ochrony.

W poniższych sekcjach opisano sposób aktualizacji agentów ochrony na komputerach klienckich, które są połączone i komputery klienckie, które nie są połączone.

### <a name="update-a-protection-agent-for-a-connected-client-computer"></a>Zaktualizuj agenta ochrony na podłączonym komputerze klienckim

1. W konsoli administratora serwera kopii zapasowej wybierz **zarządzania** > **agentów**.

2. W okienku wyświetlania wybierz komputery klienckie, dla których chcesz zaktualizować agenta ochrony.

  > [!NOTE]
  > **Aktualizacji agenta** kolumna wskazuje, kiedy aktualizacji agenta ochrony jest dostępne dla każdego komputera chronionego. W **akcje** okienku **aktualizacji** akcja jest dostępna tylko wtedy, gdy komputer chroniony jest zaznaczony i są dostępne aktualizacje.
  >
  >

3. Aby zainstalować zaktualizowanych agentów ochrony na wybranych komputerach w **akcje** okienku wybierz **aktualizacji**.

### <a name="update-a-protection-agent-on-a-client-computer-that-is-not-connected"></a>Zaktualizuj agenta ochrony na komputerze klienckim, który nie jest połączony

1. W konsoli administratora serwera kopii zapasowej wybierz **zarządzania** > **agentów**.

2. W okienku wyświetlania wybierz komputery klienckie, dla których chcesz zaktualizować agenta ochrony.

  > [!NOTE]
   > **Aktualizacji agenta** kolumna wskazuje, kiedy aktualizacji agenta ochrony jest dostępne dla każdego komputera chronionego. W **akcje** okienku **aktualizacji** akcja jest niedostępna, gdy komputer chroniony jest zaznaczony, chyba że są dostępne aktualizacje.
  >
  >

3. Aby zainstalować zaktualizowanych agentów ochrony na wybranych komputerach, wybierz opcję **aktualizacji**.

4. Na komputerze klienckim, który nie jest połączony z siecią, dopóki komputer jest połączony z siecią **stan agenta** kolumna zawiera stan **oczekująca aktualizacja**.

  Po podłączeniu komputera klienckiego do sieci, **aktualizacji agenta** kolumny dla komputera klienckiego wskazuje stan **aktualizacji**.
  
### <a name="move-legacy-protection-groups-from-old-version-and-sync-the-new-version-with-azure"></a>Przenoszenie starszych grup ochrony z starszą wersję i zsynchronizuj nowej wersji przy użyciu platformy Azure

Gdy aktualizacji są zarówno serwer kopii zapasowej Azure i systemu operacyjnego, można przystąpić do ochrony nowych źródeł danych przy użyciu nowoczesnych magazynu kopii zapasowej. Będzie jednak już chronione źródła danych mają być chronione w sposób starszej wersji w porównaniu z serwera usługi Kopia zapasowa Azure, ale wszystkie nowe ochrony będzie używać nowoczesnych magazynu kopii zapasowej.

Następujące czynności są do migracji źródeł danych z ze starszej wersji trybu ochrony na nowoczesnych magazynu kopii zapasowej.

• Dodaj nowe woluminy do puli magazynu programu DPM i przypisz przyjazną tagi źródła danych i nazwy, w razie potrzeby.
• Dla każdego źródła danych, która jest w trybie starszej wersji, Zatrzymaj ochronę źródła danych i "Zachowaj chronione dane".  Pozwoli to odzyskiwania starych punktów odzyskiwania po migracji.

• Tworzenie nowych PG i wybierz źródło danych, które mają być przechowywane przy użyciu nowego formatu.
• Program DPM wykona kopia repliki ze starszej wersji magazynu kopii zapasowej do woluminu nowoczesnych magazynu kopii zapasowych lokalnie.
Uwaga: To będą widoczne jako • zadania po odzyskiwaniu operacji wszystkie nowe synchronizacji i punktów odzyskiwania będą następnie przechowywane w nowoczesnych magazynu kopii zapasowej.
• Stare punkty odzyskiwania będą usuwane limit wygaśnie i ostatecznie wolnego miejsca na dysku.
• Usunięcie wszystkich starszych woluminów z starego magazynu, dysk można usunąć z kopii zapasowej systemu Azure i systemu.
• Wykonaj kopię zapasową bazy danych dpmdb Azure.

Część 2:-ważne elementy > Nowy serwer będzie musiał nosić takie same jak oryginalny serwer usługi Kopia zapasowa Azure. Nie można zmienić nazwy nowego serwera kopii zapasowej platformy Azure, jeśli chcesz użyć starego pulę magazynu i bazy danych DPMDB do zachowania punktów odzyskiwania — musi mieć kopię zapasową bazy danych dpmdb będzie konieczne jest przywrócenie

1) Zamknięcia oryginalnego Azure tworzenie kopii zapasowej serwera lub wyłączanie przesyłania.
2) Resetuj konta komputera w usłudze active directory.
3) Zainstaluj serwer 2016 na nowej maszyny i nadaj jej nazwę na taką samą nazwę komputera, jak oryginalny serwer usługi Kopia zapasowa Azure.
4) Przyłączenia do domeny
5) Zainstaluj serwer usługi Kopia zapasowa Azure w wersji 2 (dyski puli magazynów DPM przenieść ze starego serwera, a następnie zaimportuj)
6) Przywróć bazę danych DPMDB pobranych z końcem część 2
7) Dołącz magazynu z oryginalnego serwera kopii zapasowej do nowego serwera.
8) Z bazy danych SQL należy przywrócić bazy danych DPMDB
9) Z wiersza polecenia administratora na nowy serwer dysk cd kopia zapasowa Microsoft Azure Zainstaluj lokalizacji i otworzyć folder bin

Przykład ścieżki: C:\windows\system32 > cd "c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\
Azure backup wykonaj polecenie DPMSYNC-SYNC

10) Uruchom polecenie DPMSYNC-SYNC Uwaga Po dodaniu nowych dysków do puli magazynu programu DPM, zamiast przenoszenie starych, następnie uruchom polecenie DPMSYNC - Reallocatereplica

## <a name="new-powershell-cmdlets-in-v2"></a>Nowe polecenia cmdlet programu PowerShell w wersji 2

Po zainstalowaniu serwera usługi Kopia zapasowa Azure w wersji 2, dostępne są dwa nowe polecenia cmdlet: 
* [Mount-DPMRecoveryPoint](https://technet.microsoft.com/library/mt787159.aspx)
* [Dismount-DPMRecoveryPoint](https://technet.microsoft.com/library/mt787158.aspx)

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak przygotować serwer lub Włącz ochronę obciążenia:
- [Przygotowanie serwera kopii zapasowej obciążeń](backup-azure-microsoft-azure-backup.md)
- [Użyj kopii zapasowej serwera, aby utworzyć kopię zapasową serwera VMware](backup-azure-backup-server-vmware.md)
- [Utwórz kopię zapasową serwera umożliwia tworzenie kopii zapasowych programu SQL Server](backup-azure-sql-mabs.md)
- [Nowoczesne magazynu kopii zapasowej za pomocą kopii zapasowej serwera](backup-mabs-add-storage.md)

