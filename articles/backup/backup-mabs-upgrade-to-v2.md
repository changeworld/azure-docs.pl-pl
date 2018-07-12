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
ms.openlocfilehash: fdf69003566f704354a17335b1f46fc3077aedbc
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38598394"
---
# <a name="install-azure-backup-server-v2"></a>Instalowanie usługi Azure Backup Server w wersji 2

Usługa Azure Backup Server pomaga chronić maszyny wirtualne (VM), obciążenia, pliki i foldery i więcej. Usługa Azure Backup Server v2 jest oparta na usłudze Azure Backup Server w wersji 1 i udostępnia nowe funkcje, które nie są dostępne w wersji 1. Porównanie funkcji między v1 i v2, zobacz [macierz ochrony usługi Azure Backup Server](backup-mabs-protection-matrix.md). 

Dodatkowe funkcje w wersji 2 Utwórz kopię zapasową serwera zostały uaktualnione z kopii zapasowej serwera w wersji 1. Utwórz kopię zapasową serwera w wersji 1 nie jest jednak wymagania wstępne dotyczące instalacji Backup Server w wersji 2. Jeśli chcesz uaktualnić z kopii zapasowej serwera w wersji 1 do kopii zapasowej serwera w wersji 2, należy zainstalować Backup Server w wersji 2 na serwer ochrony Utwórz kopię zapasową serwera. Istniejące ustawienia kopii zapasowej serwera pozostają bez zmian.

Utwórz kopię zapasową serwera w wersji 2 można zainstalować w systemie Windows Server 2012 R2 lub Windows Server 2016. Aby móc korzystać z nowych funkcji, takich jak System Center 2016 danych ochrony Menedżera Modern Backup Storage, Utwórz kopię zapasową serwera w wersji 2 należy zainstalować w systemie Windows Server 2016. Zanim uaktualnisz do lub zainstaluj serwer kopii zapasowych w wersji 2, przeczytaj o [wymagania wstępne instalacji](https://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites).

> [!NOTE]
> Usługa Azure Backup Server ma ten sam kod podstawowy, jak System Center Data Protection Manager. Kopii zapasowej serwera v1 jest odpowiednikiem programu Data Protection Manager 2012 R2 i Backup Server w wersji 2 jest odpowiednikiem 2016 Data Protection Manager. W tym artykule od czasu do czasu odwołuje się w dokumentacji programu Data Protection Manager.
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

  ![Instalator instalacji — uruchom Instalatora](./media/backup-mabs-upgrade-to-v2/run-setup.png)

3. W Kreatorze serwera usługi Microsoft Azure Backup w ramach **zainstalować**, wybierz opcję **serwera usługi Microsoft Azure Backup**.

   ![Instalator instalacji — wybierz opcję Zainstaluj](./media/backup-mabs-upgrade-to-v2/mabs-installer-s1.png)

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

Aby poprawić wydajność magazynu kopii zapasowych, Utwórz kopię zapasową serwera w wersji 2 dodaje obsługę woluminów. Np. v1 Utwórz kopię zapasową serwera Utwórz kopię zapasową serwera w wersji 2 obsługuje dyski.

### <a name="add-volumes-and-disks"></a>Dodawanie woluminów i dysków
Jeśli uruchamiasz v2 kopii zapasowej serwera w systemie Windows Server 2016, można użyć woluminów do przechowywania danych kopii zapasowej. Woluminy oferują oszczędności pojemności magazynu oraz szybsze tworzenie kopii zapasowych. Ponieważ woluminy są nowe Utwórz kopię zapasową serwera, należy je dodać. 

Po dodaniu woluminu do serwera kopii zapasowych, możesz nadać woluminowi przyjazną nazwę. Kliknij przycisk **przyjazną nazwę** kolumny wolumin ma nazwę. Nazwę można zmienić później, jeśli to konieczne. Możesz również użyć programu PowerShell można dodawać lub zmieniać przyjazne nazwy dla woluminów.

Aby dodać wolumin w konsoli administratora:

1. W konsoli administratora serwera kopii zapasowych platformy Azure wybierz **zarządzania** > **Magazyn dyskowy** > **Dodaj**.

    ![Otwórz kreatora Dodawanie magazynu dyskowego](./media//backup-mabs-upgrade-to-v2/open-add-disk-storage-wizard.png)

    Spowoduje to otwarcie kreatora Dodawanie magazynu dyskowego.

2. Na **Dodawanie magazynu dyskowego** stronie **dostępne woluminy** , wybierz wolumin, a następnie wybierz **Dodaj**.
3. W **wybrane woluminy** Wprowadź przyjazną nazwę dla woluminu, a następnie wybierz pozycję **OK**.

      ![Magazyn dyskowy Kreator dodawania — Dodawanie woluminu](./media/backup-mabs-upgrade-to-v2/add-volume.png)

  Jeśli chcesz dodać dysk, dysk musi należeć do grupy ochrony, która ma magazynu w starszej wersji. Te dyski mogą być używane tylko dla tych grup ochrony. Jeśli serwer usługi Backup nie ma źródeł, które mają starszą ochroną, dysk nie ma na liście.

  Aby uzyskać więcej informacji na temat dodawania dysków, zobacz [dodawanie dysków w celu zwiększenia magazynu w starszej wersji](http://docs.microsoft.com/system-center/dpm/upgrade-to-dpm-2016#adding-disks-to-increase-legacy-storage). Nie można nadać przyjaznej nazwy dysku.


### <a name="assign-workloads-to-volumes"></a>Przypisywanie obciążeń do woluminów

Utwórz kopię zapasową serwera, należy określić w obciążeniach, które są przypisane do poszczególnych woluminów. Na przykład można ustawić kosztowne woluminy, które obsługuje dużą liczbę operacji wejścia/wyjścia na sekundę (IOPS) do przechowywania tylko w przypadku obciążeń, które wymagają częste, duże kopie zapasowe. Przykładem jest program SQL Server przy użyciu dzienników transakcji.

#### <a name="update-dpmdiskstorage"></a>Update-DPMDiskStorage

Aby zaktualizować właściwości woluminu w puli magazynów na serwerze kopii zapasowej, użyj polecenia cmdlet programu PowerShell Update-DPMDiskStorage.

Składnia:

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```

Wszystkie zmiany wprowadzone przy użyciu programu PowerShell są odzwierciedlane w interfejsie użytkownika.


## <a name="protect-data-sources"></a>Ochrona źródeł danych
Aby rozpocząć ochronę źródeł danych, należy utworzyć grupy ochrony. Poniższe kroki, Wyróżnij zmiany i dodatki w Kreatorze nowej grupy ochrony.

Aby utworzyć grupę ochrony:

1. W konsoli administratora serwera kopii zapasowych wybierz **ochrony**.

2. Na wstążce narzędzi wybierz **New**.

    Spowoduje to otwarcie Kreatora tworzenia nowej grupy ochrony.

  ![Kreator tworzenia nowej grupy ochrony](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-1.png)

3. Na **stronie powitalnej** wybierz pozycję **Dalej**.
4. Na **wybierz typ grupy ochrony** wybierz typ grupy ochrony, które chcesz utworzyć, a następnie wybierz **dalej**.

  ![Strona typu wybierz grupę ochrony](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-2.png)

5. Na **Wybierz członków grupy** stronie **dostępni członkowie** okienko, członków z ochrony agentów są wyświetlane. W tym przykładzie wybierz woluminy D:\ i E:\ i dodaj je do **wybrane elementy członkowskie** okienka. Wybierz opcję **Dalej**.

  ![Wybierz grupę elementów członkowskich strony](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-3.png)

6. Na **wybierz metodę ochrony danych** wpisz **Nazwa grupy ochrony**, wybierz metodę ochrony, a następnie wybierz **dalej**. Jeśli chcesz, aby uzyskać krótkoterminową ochronę, musisz wybrać **dysku** utworzyć kopię zapasową metodę.

  ![Wybierz metodę ochrony danych stronę](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-4.png)

7. Na **Określ cele krótkoterminowe** Wybierz szczegóły **zakres przechowywania** i **częstotliwość synchronizacji**. Następnie wybierz opcję **Dalej**. Opcjonalnie, aby zmienić harmonogram przy woluminów punktów odzyskiwania, wybierz **Modyfikuj**.

  ![Określ cele krótkoterminowe strony](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-5.png)

8. Na **Przejrzyj przydział magazynu dyskowego** strony, przejrzyj szczegółowe informacje dotyczące źródeł danych wybrano, ich rozmiar i wartości miejsce do przygotowania i docelowy wolumin magazynu.

  ![Przejrzyj przydział magazynu dyskowego strony](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-6.png)

  Woluminy magazynu na podstawie alokacji woluminu obciążenia (ustawiona przy użyciu programu PowerShell) i dostępnego magazynu. Woluminy magazynu można zmienić, wybierając inne woluminy z menu rozwijanego. Jeśli zmienisz wartość **Magazyn docelowy**, wartość **dostępny Magazyn dyskowy** zmieniają się dynamicznie, aby odzwierciedlić wartości w obszarze **wolnego miejsca** i  **Underprovisioned miejsca**.

  Jeśli źródła danych zwiększą się zgodnie z planem wartość **miejsce** kolumny w **dostępny Magazyn dyskowy** odzwierciedla ilość dodatkowego magazynu, która jest potrzebna. Użyj tej wartości, aby ułatwić zaplanowanie potrzeb dotyczących magazynu dla bezproblemowego tworzenia kopii zapasowych. Jeśli ta wartość wynosi zero, istnieją nie potencjalnych problemów z magazynem w najbliższej przyszłości. Jeśli wartość jest różna od zera, nie masz wystarczającej ilości miejsca przydzielone (na podstawie zasad ochrony i rozmiaru danych chronionych elementów członkowskich).

  ![Przydział magazynu dyskowego](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-7.png)

   Aby zakończyć tworzenie grupy ochrony, Zakończ pracę kreatora.

## <a name="migrate-legacy-storage-to-modern-backup-storage"></a>Migrowanie starszej wersji magazynu do funkcji Modern Backup Storage
Po uaktualnieniu lub zainstalować Backup Server w wersji 2 i Uaktualnij system operacyjny do systemu Windows Server 2016 można zaktualizować grup ochrony do użycia Modern Backup Storage. Domyślnie grupy ochrony nie są zmieniane. One nadal działać zgodnie z początkowo zostały ustawione. 

Aktualizowanie grup ochrony w celu użycia Modern Backup Storage jest opcjonalne. Aby zaktualizować grupę ochrony, Zatrzymaj ochronę wszystkich źródeł danych przy użyciu opcji Zachowaj dane. Następnie dodaj źródła danych do nowej grupy ochrony.

1. W konsoli administratora wybierz **ochrony** funkcji. W **elementu członkowskiego grupy ochrony** listy, kliknij prawym przyciskiem myszy element członkowski, a następnie wybierz **Zatrzymaj ochronę członka**.

  ![Zatrzymaj ochronę członka](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-stop-protection1.png)

2. W **Usuń z grupy** okna dialogowego przejrzyj używane miejsce na dysku i dostępne wolne miejsce w puli magazynów. Wartość domyślna to pozostawienie punktów odzyskiwania na dysku i umożliwić im wygaśnie za skojarzonych zasad przechowywania. Kliknij przycisk **OK**.

  Aby natychmiast przywrócić zajęte miejsce na dysku do wolnej puli magazynów, należy zaznaczyć **Usuń replikę z dysku** pole wyboru, aby usunąć dane kopii zapasowej (i punktów odzyskiwania) skojarzonych z tym elementem członkowskim.

  ![Usuń z grupy, okno dialogowe](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-retain-data.png)

3. Utwórz grupę ochrony, która używa funkcji Modern Backup Storage. Dołącz źródła danych niechronione.


## <a name="add-disks-to-increase-legacy-storage"></a>Dodawanie dysków w celu zwiększenia magazynu w starszej wersji

Jeśli chcesz używać starszej wersji magazynu z kopii zapasowej serwera, może być konieczne dodanie dysków w celu zwiększenia magazynu w starszej wersji. 

Aby dodać magazyn dyskowy:

1. W konsoli administratora wybierz **zarządzania** > **Magazyn dyskowy** > **Dodaj**.

    ![Dodaj okno dialogowe Magazyn dyskowy](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-add-disk-storage.png)

4. W **Dodawanie magazynu dyskowego** okno dialogowe, wybierz opcję **dodawać dyski**.

5. Na liście dostępnych dysków, wybierz dyski, o których chcesz dodać, wybierz **Dodaj**, a następnie wybierz pozycję **OK**.

## <a name="update-the-data-protection-manager-protection-agent"></a>Zaktualizuj agenta ochrony programu Data Protection Manager

Utwórz kopię zapasową serwera, używa agenta ochrony programu System Center Data Protection Manager aktualizacji. Jeśli przeprowadzasz uaktualnienie agenta ochrony, który nie jest podłączony do sieci, nie można użyć konsoli administratora programu Data Protection Manager do ukończenia uaktualnienia podłączonego agenta. Należy uaktualnić agenta ochrony w środowisku domeny nieaktywny. Konsoli administratora programu Data Protection Manager, dopóki komputer kliencki jest połączony z siecią, pokazuje, oczekuje aktualizacji agenta ochrony.

Poniżej opisano sposób aktualizacji agentów ochrony na komputerach klienckich, które są połączone i komputerów klienckich, które nie są połączone.

### <a name="update-a-protection-agent-for-a-connected-client-computer"></a>Zaktualizuj agenta ochrony na podłączonym komputerze klienckim

1. W konsoli administratora serwera kopii zapasowych wybierz **zarządzania** > **agentów**.

2. W okienku wyświetlania wybierz komputery klienckie, dla których chcesz zaktualizować agenta ochrony.

  > [!NOTE]
  > **Aktualizacji agenta** kolumna wskazuje, kiedy aktualizacji agenta ochrony jest dostępna dla każdego chronionego komputera. W **akcje** okienku **aktualizacji** akcja jest dostępna tylko wtedy, gdy komputer chroniony jest zaznaczony i są dostępne aktualizacje.
  >
  >

3. Aby zainstalować zaktualizowanych agentów ochrony na wybranych komputerach w **akcje** okienku wybierz **aktualizacji**.

### <a name="update-a-protection-agent-on-a-client-computer-that-is-not-connected"></a>Zaktualizuj agenta ochrony na komputerze klienckim, który nie jest połączony

1. W konsoli administratora serwera kopii zapasowych wybierz **zarządzania** > **agentów**.

2. W okienku wyświetlania wybierz komputery klienckie, dla których chcesz zaktualizować agenta ochrony.

  > [!NOTE]
   > **Aktualizacji agenta** kolumna wskazuje, kiedy aktualizacji agenta ochrony jest dostępna dla każdego chronionego komputera. W **akcje** okienku **aktualizacji** akcja jest niedostępna, gdy komputer chroniony jest zaznaczony, o ile nie są dostępne aktualizacje.
  >
  >

3. Aby zainstalować zaktualizowanych agentów ochrony na wybranych komputerach, należy wybrać **aktualizacji**.

4. Na komputerze klienckim, który nie jest podłączony do sieci, dopóki komputer jest połączony z siecią **stan agenta** kolumna pokazuje stan **oczekująca aktualizacja**.

  Po komputer kliencki jest połączony z siecią **aktualizacji agenta** kolumny dla komputera klienckiego wskazuje stan **aktualizowanie**.
  
### <a name="move-legacy-protection-groups-from-old-version-and-sync-the-new-version-with-azure"></a>Przenoś starszych grup ochrony ze starej wersji i synchronizowanie nowej wersji przy użyciu platformy Azure

Gdy aktualizacji są zarówno usługi Azure Backup Server i systemu operacyjnego, można przystąpić do ochrony nowych źródeł danych przy użyciu Modern Backup Storage. Jednak już chronione źródła danych będą w dalszym ciągu być chronione w starszy sposób znajdowały się w usłudze Azure Backup Server, ale wszystkie nowe ochrony użyje Modern Backup Storage.

Poniższe kroki dotyczą migracji źródła danych z ze starszej wersji trybu ochrony do funkcji Modern backup storage.

• Dodawanie nowych woluminów do puli magazynów programu DPM i przypisz przyjazną tagi źródła nazwiska i dane, w razie potrzeby.
• Dla każdego źródła danych, który jest w trybie starszej wersji, Zatrzymaj ochronę źródła danych i "Zachowaj chronione dane".  Umożliwi to odzyskiwanie starych punktów odzyskiwania po migracji.

• Tworzenie nowych PG i wybierz źródła danych, które mają być przechowywane przy użyciu nowego formatu.
• Program DPM wykona kopię repliki ze starszej wersji magazynu kopii zapasowych do woluminu Modern Backup Storage lokalnie.
Uwaga: Ta będzie widoczna jako czynności po odzyskiwaniu: • zadania, wszystkie nowe synchronizacji i punktów odzyskiwania będą następnie przechowywane w Modern Backup Storage.
• Stare punkty odzyskiwania będą usuwane się, jak wygasa, a ostatecznie wolnego miejsca na dysku.
• Po wszystkich starszych woluminów, które są usuwane z starego magazynu, dysk można usunąć z usługi Azure backup i systemu.
• Wykonaj kopię zapasową bazy danych dpmdb platformy Azure.

Część 2:-istotnych elementów > Nowy serwer będzie musiał mieć nazwę takie same jak oryginalny serwer usługi Azure Backup. Nie można zmienić nazwy nowej usługi Azure backup server, jeśli chcesz używać starego puli magazynów i bazy danych DPMDB do przechowywania punktów odzyskiwania — musi mieć kopię zapasową bazy danych DPMDB, jak będzie musiała zostać przywrócone

1) Zamknij oryginalny Azure Utwórz kopię zapasową serwera lub zwiększyły podczas transmisji.
2) Resetuj konta komputera w usłudze active directory.
3) Zainstaluj Server 2016 na nowej maszyny i nadaj jej nazwę na taką samą nazwę komputera, jak oryginalny serwer usługi Azure Backup.
4) Przyłączenia do domeny
5) Instalowanie usługi Azure Backup server w wersji 2 (dyski puli magazynów programu DPM przenieść ze starego serwera i import)
6) Przywróć bazę danych DPMDB pobranych z końca część 2
7) Dołącz magazyn z oryginalnego serwera kopii zapasowej do nowego serwera.
8) Z bazy danych SQL należy przywrócić bazy danych DPMDB
9) Z poziomu wiersza polecenia administratora na nowy serwer dysk cd, kopia zapasowa Microsoft Azure Zainstaluj lokalizacji i otworzyć folder bin

Przykład ścieżki: C:\windows\system32 > cd "c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\
na platformie Azure należy utworzyć kopię zapasową wykonaj polecenie DPMSYNC-SYNC

10) Uruchom polecenie DPMSYNC-SYNC Uwaga Po dodaniu nowych dysków do puli magazynów programu DPM, zamiast przenoszenia stare, uruchom polecenie DPMSYNC - Reallocatereplica

## <a name="new-powershell-cmdlets-in-v2"></a>Nowe polecenia cmdlet programu PowerShell w wersji 2

Po zainstalowaniu usługi Azure Backup Server w wersji 2, dostępne są dwa nowe polecenia cmdlet: 
* [Mount-DPMRecoveryPoint](https://technet.microsoft.com/library/mt787159.aspx)
* [Dismount-DPMRecoveryPoint](https://technet.microsoft.com/library/mt787158.aspx)

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak przygotować serwer lub rozpoczęciu ochrony obciążenia:
- [Przygotowywanie obciążeń serwera usługi Backup](backup-azure-microsoft-azure-backup.md)
- [Tworzenie kopii zapasowej serwera VMware przy użyciu kopii zapasowej serwera](backup-azure-backup-server-vmware.md)
- [Tworzenie kopii zapasowych programu SQL Server przy użyciu kopii zapasowej serwera](backup-azure-sql-mabs.md)
- [Modern Backup Storage za pomocą kopii zapasowej serwera](backup-mabs-add-storage.md)

