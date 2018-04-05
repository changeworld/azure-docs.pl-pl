---
title: Użyj agenta usługi Kopia zapasowa Azure do utworzenia kopii zapasowej plików i folderów | Dokumentacja firmy Microsoft
description: Agent usługi Kopia zapasowa Microsoft Azure umożliwia tworzenie kopii zapasowej plików systemu Windows i folderów na platformie Azure. Tworzenie magazynu usług odzyskiwania, zainstalować agenta kopii zapasowej Definiowanie zasad tworzenia kopii zapasowej i uruchom tworzenie początkowej kopii zapasowej plików i folderów.
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keywords: Magazyn kopii zapasowych; Tworzenie kopii zapasowej systemu Windows server; Kopia zapasowa systemu windows;
ms.assetid: 7f5b1943-b3c1-4ddb-8fb7-3560533c68d5
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/5/2018
ms.author: markgal;trinadhk;
ms.openlocfilehash: 38f98a29edc2a0bfb0141a0a6fabad2727175f29
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="back-up-a-windows-server-or-client-to-azure-using-the-resource-manager-deployment-model"></a>Tworzenie kopii zapasowych systemu Windows Server lub Client na platformie Azure przy użyciu modelu wdrażania używającego usługi Resource Manager
W tym artykule opisano sposób wykonywania kopii zapasowej systemu Windows Server (lub klienta systemu Windows) plików i folderów na platformie Azure za pomocą usługi Kopia zapasowa Azure przy użyciu modelu wdrażania Menedżera zasobów.

![Kroki procesu tworzenia kopii zapasowej](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Przed rozpoczęciem
Aby utworzyć kopię zapasową serwera lub klienta na platformie Azure, potrzebne jest konto platformy Azure. Jeśli nie masz, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free/) w zaledwie kilka minut.

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu Usług odzyskiwania
Magazyn usług odzyskiwania jest jednostka, która przechowuje wszystkie kopie zapasowe i punkty odzyskiwania, tworzonych w czasie. Magazyn usług odzyskiwania zawiera również zasady tworzenia kopii zapasowej stosowane do chronionych plików i folderów. Podczas tworzenia magazynu usług odzyskiwania, należy również wybierz opcję nadmiarowość odpowiedniego magazynu.

### <a name="to-create-a-recovery-services-vault"></a>Aby utworzyć magazyn usługi Recovery Services
1. Jeśli nie zostało to wcześniej zrobione, zaloguj się witryny [Azure Portal](https://portal.azure.com/) przy użyciu subskrypcji platformy Azure.
2. W menu Centrum kliknij pozycję **Wszystkie usługi**, na liście zasobów wpisz ciąg **Recovery Services**, a następnie kliknij pozycję **Magazyny usługi Recovery Services**.

    ![Tworzenie magazynu usługi Recovery Services — krok 1](./media/backup-try-azure-backup-in-10-mins/open-rs-vault-list.png) <br/>

    Jeśli w ramach subskrypcji istnieją magazyny usług odzyskiwania, zostaną one wyświetlone.

3. W menu **Magazyny usługi Recovery Services** kliknij pozycję **Dodaj**.

    ![Tworzenie magazynu Usług odzyskiwania — krok 2](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

    Zostanie otwarty blok magazynu Usług odzyskiwania i pojawi się monit o podanie wartości w polach **Nazwa**, **Subskrypcja**, **Grupa zasobów** i **Lokalizacja**.

    ![Tworzenie magazynu usługi Recovery Services — krok 3](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

4. W polu **Nazwa** wprowadź przyjazną nazwę identyfikującą magazyn. Nazwa musi być unikalna w tej subskrypcji platformy Azure. Wpisz nazwę o długości od 2 do 50 znaków. Musi ona rozpoczynać się od litery i może zawierać tylko litery, cyfry i łączniki.

5. W sekcji **Subskrypcja** użyj menu rozwijanego, aby wybrać subskrypcję platformy Azure. Jeśli używasz tylko jednej subskrypcji, zostanie ona wyświetlona i możesz przejść do następnego kroku. Jeśli nie masz pewności, której subskrypcji użyć, wybierz domyślną (lub sugerowaną). Większa liczba opcji do wyboru jest dostępna tylko w przypadku, gdy konto organizacji jest skojarzone z wieloma subskrypcjami platformy Azure.

6. W sekcji **Grupa zasobów**:

    * wybierz pozycję **Utwórz nową**, jeśli chcesz utworzyć nową grupę zasobów.
    Lub
    * wybierz pozycję **Użyj istniejącej** i kliknij menu rozwijane, aby wyświetlić listę dostępnych grup zasobów.

  Pełne informacje na temat grup zasobów można znaleźć w temacie [Omówienie usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

7. Kliknij pozycję **Lokalizacja**, aby wybrać region geograficzny magazynu. Ten wybór określa region geograficzny, do którego wysyłane są dane kopii zapasowej.

8. W dolnej części bloku magazynu usług Recovery Services kliknij pozycję **Utwórz**.

  Utworzenie magazynu usług Recovery Services może potrwać kilka minut. Monitoruj powiadomienia o stanie wyświetlane w prawej górnej części obszaru portalu. Po utworzeniu magazynu pojawi się on na liście magazynów Usług odzyskiwania. Jeśli po kilku minutach nie widzisz swojego magazynu, kliknij pozycję **Odśwież**.

  ![Klikanie pozycji Odśwież](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)</br>

  Po wyświetleniu magazynu na liście magazynów usług Recovery Services możesz rozpocząć ustawianie nadmiarowości przechowywania.


### <a name="set-storage-redundancy"></a>Nadmiarowość magazynu zestawu
Podczas tworzenia magazynu usługi Recovery Services określany jest sposób replikowania magazynu.

1. W bloku **Magazyny usług Recovery Services** kliknij nowy magazyn.

    ![Wybieranie nowego magazynu z listy magazynów usług Recovery Services](./media/backup-try-azure-backup-in-10-mins/rs-vault-list.png)

    Wybranie magazynu spowoduje zwężenie bloku **Magazyn usług Recovery Services** oraz otwarcie bloków Ustawienia (*o nazwie magazynu wskazanego w górnej części*) i szczegółów magazynu.

    ![Wyświetlanie konfiguracji przechowywania dla nowego magazynu](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration-2.png)

2. W bloku ustawień nowego magazynu użyj pionowego suwaka, aby przewinąć w dół do sekcji Zarządzanie, a następnie kliknij pozycję **Infrastruktura zapasowa**.

  Zostanie otwarty blok Infrastruktura zapasowa.

3. W bloku Infrastruktura zapasowa kliknij pozycję **Konfiguracja kopii zapasowej** w celu otwarcia bloku **Konfiguracja kopii zapasowej**.

  ![Ustawianie konfiguracji przechowywania dla nowego magazynu](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration.png)

4. Wybierz odpowiednią opcję replikacji dla magazynu.

  ![Opcje konfiguracji usługi Storage](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)

  Domyślnie magazyn jest nadmiarowy geograficznie. Jeśli używasz platformy Azure jako punktu końcowego podstawowego magazynu kopii zapasowych, kontynuuj korzystanie z magazynu **geograficznie nadmiarowego**. Jeśli nie używasz platformy Azure jako punktu końcowego podstawowego magazynu kopii zapasowych, wybierz pozycję **Lokalnie nadmiarowy**, co zmniejszy koszty magazynów platformy Azure. Więcej informacji o opcjach magazynu [geograficznie nadmiarowego](../storage/common/storage-redundancy-grs.md) i [lokalnie nadmiarowego](../storage/common/storage-redundancy-lrs.md) można znaleźć w tym [omówieniu nadmiarowości magazynu](../storage/common/storage-redundancy.md).

Teraz, po utworzeniu magazynu należy przygotować infrastrukturę do tworzenia kopii zapasowej plików i folderów pobieranie i instalowanie agenta usług odzyskiwania Microsoft Azure, pobierając poświadczenia magazynu i następnie użyciu tych poświadczeń można zarejestrować agenta z Magazyn.

## <a name="configure-the-vault"></a>Konfigurowanie magazynu

1. W bloku Magazyn usługi Recovery Services (dla właśnie utworzonego magazynu) w sekcji Wprowadzenie kliknij pozycję **Kopia zapasowa**, a następnie w bloku **Wprowadzenie do kopii zapasowej** wybierz pozycję **Cel kopii zapasowej**.

  ![Otwieranie bloku celu kopii zapasowej](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

  Zostanie otwarty blok **Cel kopii zapasowej**. Jeśli wcześniej skonfigurowano Magazyn usług odzyskiwania, a następnie **cel kopii zapasowej** kliknięcie powoduje otwarcie bloków **kopii zapasowej** na usług odzyskiwania magazynu bloku.

  ![Otwieranie bloku celu kopii zapasowej](./media/backup-try-azure-backup-in-10-mins/backup-goal-blade.png)

2. Z menu **Gdzie działa Twoje obciążenie?** wybierz pozycję **Lokalnie**.

  Pozycję **Lokalnie** trzeba wybrać, ponieważ Twój serwer lub komputer z systemem Windows jest maszyną fizyczną spoza platformy Azure.

3. Z menu **Dla jakich elementów chcesz utworzyć kopię zapasową?** wybierz pozycję **Pliki i foldery**, a następnie kliknij przycisk **OK**.

  ![Konfigurowanie plików i folderów](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

  Po kliknięciu przycisku OK obok pozycji **Cel kopii zapasowej** pojawi się znacznik wyboru i zostanie otwarty blok **Przygotowywanie infrastruktury**.

  ![Cel kopii zapasowej został skonfigurowany, następnym krokiem jest przygotowanie infrastruktury](./media/backup-try-azure-backup-in-10-mins/backup-goal-configed.png)

4. W bloku **Przygotowywanie infrastruktury** kliknij pozycję **Pobierz agenta dla systemu Windows Server lub klienta systemu Windows**.

  ![Przygotowywanie infrastruktury](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

  Jeśli używasz systemu Windows Server Essential, wybierz opcję pobrania agenta dla systemu Windows Server Essential. Zostanie wyświetlone menu rozwijane z monitem o uruchomienie lub zapisanie pliku MARSAgentInstaller.exe.

  ![Okno dialogowe MARSAgentInstaller](./media/backup-try-azure-backup-in-10-mins/mars-installer-run-save.png)

5. W menu rozwijanym pobierania kliknij pozycję **Zapisz**.

  Domyślnie plik **MARSagentinstaller.exe** jest zapisywany w folderze Pobrane. Po ukończeniu pobierania pojawi się okno podręczne z pytaniem, czy chcesz uruchomić Instalatora, czy otworzyć folder.

  ![Przygotowywanie infrastruktury](./media/backup-try-azure-backup-in-10-mins/mars-installer-complete.png)

  Nie musisz jeszcze instalować agenta. Agenta możesz zainstalować po pobraniu poświadczeń magazynu.

6. W bloku **Przygotowywanie infrastruktury** kliknij pozycję **Pobierz**.

  ![Pobieranie poświadczeń magazynu](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

  Poświadczenia magazynu zostaną pobrane do folderu Pobrane. Po zakończeniu pobierania poświadczeń magazynu zobaczysz okno podręczne z pytaniem, czy chcesz otworzyć poświadczenia, czy je zapisać. Kliknij pozycję **Zapisz**. Jeśli przypadkowo klikniesz pozycję **Otwórz**, zaczekaj, aż działanie okna dialogowego, które spróbuje otworzyć poświadczenia magazynu, zakończy się niepowodzeniem. Poświadczeń magazynu nie da się otworzyć. Przejdź do następnego kroku. Poświadczenia magazynu znajdują się w folderze Pobrane.   

  ![Zakończenie pobierania poświadczeń magazynu](./media/backup-try-azure-backup-in-10-mins/vault-credentials-downloaded.png)


[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="install-and-register-the-agent"></a>Instalowanie i rejestrowanie agenta

> [!NOTE]
> Opcja włączania kopii zapasowych za pośrednictwem witryny Azure Portal jest jeszcze niedostępna. Do tworzenia kopii zapasowej plików i folderów należy używać agenta usług Microsoft Azure Recovery Services.
>

1. Zlokalizuj i kliknij dwukrotnie plik **MARSagentinstaller.exe** w folderze Pobrane (lub innej lokalizacji).

  Instalator wyświetli serię komunikatów w miarę wypakowywania, instalowania i rejestrowania agenta usługi Recovery Services.

  ![Uruchamianie Instalatora agenta usługi Recovery Services — poświadczenia](./media/backup-try-azure-backup-in-10-mins/mars-installer-registration.png)

2. Wykonaj kroki kreatora instalacji agenta usługi Microsoft Azure Recovery Services. Aby zakończyć pracę kreatora, wykonaj następujące czynności:

  * Wybierz lokalizację folderu instalacji i folderu pamięci podręcznej.
  * Podaj informacje o serwerze proxy, jeśli korzystasz z niego do łączenia się z Internetem.
  * Podaj swoją nazwę użytkownika i hasło, jeśli korzystasz z uwierzytelnionego serwera proxy.
  * Udostępnianie pobranych poświadczeń magazynu
  * Zapisz hasło szyfrowania w bezpiecznej lokalizacji.

  > [!NOTE]
  > Jeśli utracisz lub zapomnisz hasło, firma Microsoft nie będzie mogła pomóc w odzyskaniu danych kopii zapasowej. Zapisz plik w bezpiecznej lokalizacji. Jest ono wymagane do przywrócenia kopii zapasowej.
  >
  >

Agent jest teraz zainstalowany, a maszyna zarejestrowana w magazynie. Wszystko jest gotowe do skonfigurowania kopii zapasowej i zaplanowania jej tworzenia.

## <a name="network-and-connectivity-requirements"></a>Wymagania dotyczące sieci i łączności

Machine/serwera proxy został ograniczony dostęp do Internetu, upewnij się, że ustawienia zapory na komputerze/serwera proxy są skonfigurowane i umożliwiają następujące adresy URL: <br>
    1. www.msftncsi.com
    2. *.Microsoft.com
    3. *.WindowsAzure.com
    4. *.microsoftonline.com
    5. *.windows.net


## <a name="create-the-backup-policy"></a>Tworzenie zasad kopii zapasowych
Zasady tworzenia kopii zapasowej jest określają harmonogram punktów odzyskiwania są pobierane i czas, punkty odzyskiwania są zachowywane. Użyj agenta kopii zapasowej Microsoft Azure, aby utworzyć zasady tworzenia kopii zapasowej plików i folderów.

### <a name="to-create-a-backup-schedule"></a>Aby utworzyć harmonogram tworzenia kopii zapasowych
1. Otwórz agenta usługi Kopia zapasowa Microsoft Azure. Aby go znaleźć, wyszukaj na maszynie łańcuch **Microsoft Azure Backup**.

    ![Uruchamianie agenta usługi Kopia zapasowa Azure](./media/backup-configure-vault/snap-in-search.png)
2. W agencie kopii zapasowej **akcje** okienku, kliknij przycisk **harmonogram tworzenia kopii zapasowych** można uruchomić Kreatora harmonogramu kopii zapasowej.

    ![Planowanie tworzenia kopii zapasowej systemu Windows Server](./media/backup-configure-vault/schedule-first-backup.png)

3. Na **wprowadzenie** strony Kreatora harmonogramu kopii zapasowej, kliknij przycisk **dalej**.
4. Na **Wybieranie elementów do wykonania kopii zapasowej** kliknij przycisk **Dodaj elementy**.

  Zostanie otwarte okno dialogowe Wybieranie elementów.

5. Wybierz pliki i foldery, które chcesz chronić, a następnie kliknij przycisk **OK**.
6. W **Wybieranie elementów do wykonania kopii zapasowej** kliknij przycisk **dalej**.
7. Na **Określanie harmonogramu tworzenia kopii zapasowej** , określ harmonogram tworzenia kopii zapasowych i kliknij przycisk **dalej**.

    Można zaplanować tworzenie kopii zapasowych codziennie (maksymalnie trzy razy) lub co tydzień.

    ![Elementy związane z tworzeniem kopii zapasowej systemu Windows Server](./media/backup-configure-vault/specify-backup-schedule-close.png)

   > [!NOTE]
   > Aby uzyskać więcej informacji o sposobie określania harmonogramu tworzenia kopii zapasowych, zobacz artykuł [Use Azure Backup to replace your tape infrastructure](backup-azure-backup-cloud-as-tape.md) (Użycie usługi Azure Backup do zastąpienia infrastruktury taśm).
   >
   >

8. Na **Wybieranie zasady przechowywania** wybierz zasady przechowywania określonego dla kopii zapasowej i kliknij przycisk **dalej**.

    Zasady przechowywania określają czas trwania, którego kopia zapasowa jest przechowywana. Zamiast określać wspólne zasady dla wszystkich punktów kopii zapasowej, można określić różne zasady przechowywania w zależności od momentu tworzenia kopii zapasowej. Możliwe jest modyfikowanie, zgodnie z potrzebami, zasad przechowywania codziennych, cotygodniowych, comiesięcznych i corocznych kopii zapasowych.
9. Na stronie Wybieranie typu początkowej kopii zapasowej wybierz typ początkowej kopii zapasowej. Pozostaw zaznaczoną opcję **Automatycznie przez sieć**, a następnie kliknij przycisk **Dalej**.

    Kopie zapasowe można tworzyć automatycznie za pośrednictwem sieci lub w trybie offline. W dalszej części tego artykułu opisano proces automatycznego tworzenia kopii zapasowych. Jeśli chcesz tworzyć kopie zapasowe w trybie offline, zapoznaj się z artykułem [Offline backup workflow in Azure Backup](backup-azure-backup-import-export.md) (Przepływ pracy tworzenia kopii zapasowej w trybie offline w usłudze Azure Backup), aby uzyskać dodatkowe informacje.
10. Przejrzyj informacje na stronie Potwierdzenie, a następnie kliknij przycisk **Zakończ**.
11. Po ukończeniu harmonogramu tworzenia kopii zapasowej przez kreatora kliknij przycisk **Zamknij**.

### <a name="enable-network-throttling"></a>Włącz ograniczanie przepustowości sieci
Agent usługi Kopia zapasowa Microsoft Azure udostępnia ograniczanie przepustowości sieci. Ograniczanie kontrolek z wykorzystaniem przepustowości sieci podczas transferu danych. Ten formant może być przydatne, jeśli chcesz utworzyć kopię zapasową danych podczas godziny pracy, ale nie chcesz procesu tworzenia kopii zapasowej z innych ruch internetowy. Ograniczenie ma zastosowanie do kopii zapasowej i przywracania działań.

> [!NOTE]
> Ograniczanie przepustowości sieci nie jest dostępne w systemu Windows Server 2008 R2 z dodatkiem SP1, Windows Server 2008 z dodatkiem SP2 lub Windows 7 (dodatków service pack). Funkcji ograniczania sieci kopia zapasowa Azure angażujący jakości usług (QoS) w lokalnym systemie operacyjnym. Jeśli kopia zapasowa Azure może chronić tych systemów operacyjnych, wersji QoS dostępne na tych platformach nie działa z ograniczanie przepustowości sieci usługi Kopia zapasowa Azure. Ograniczanie przepustowości sieci można używać na wszystkich innych [obsługiwanych systemów operacyjnych](backup-azure-backup-faq.md).
>
>

**Aby włączyć ograniczenie przepustowości sieci**

1. W agencie programu Kopia zapasowa Microsoft Azure, kliknij przycisk **Zmień właściwości**.

    ![Zmień właściwości](./media/backup-configure-vault/change-properties.png)
2. Na **ograniczania** wybierz opcję **włączyć użycia przepustowości połączenia internetowego do operacji tworzenia kopii zapasowej** pole wyboru.

    ![Ograniczanie przepustowości sieci](./media/backup-configure-vault/throttling-dialog.png)
3. Po włączeniu ograniczenia przepustowości, należy określić dozwolony przepustowości dla transferu danych kopii zapasowej podczas **godzin pracy** i **godziny wolne**.

    Wartości przepustowości rozpocząć 512 kilobitów na sekundę (KB/s) i można przejść do 1,023 MB na sekundę (MB/s). Można również wyznaczyć rozpoczęcia i zakończenia **godzin pracy**, i dni tygodnia są uważane dniach. Godziny poza wyznaczonych są traktowane jako godzin pracy z systemem innym niż godziny pracy.
4. Kliknij przycisk **OK**.

### <a name="to-back-up-files-and-folders-for-the-first-time"></a>Aby utworzyć kopię zapasową plików i folderów po raz pierwszy
1. W agenta kopii zapasowej, kliknij przycisk **wykonaj kopię zapasową teraz** aby zakończyć początkowe umieszczanie za pośrednictwem sieci.

    ![Natychmiastowe tworzenie kopii zapasowej systemu Windows Server](./media/backup-configure-vault/backup-now.png)
2. Na stronie Potwierdzenie przejrzyj ustawienia, które zostaną użyte przez Kreatora natychmiastowego tworzenia kopii zapasowej do utworzenia kopii zapasowej maszyny. Następnie kliknij pozycję **Utwórz kopię zapasową**.
3. Kliknij przycisk **Zamknij**, aby zamknąć kreatora. Jeśli zrobisz to przed zakończeniem procesu tworzenia kopii zapasowej, kreator będzie nadal działał w tle.

Po zakończeniu tworzenia początkowej kopii zapasowej w konsoli usługi Backup zostanie wyświetlony stan **Ukończono zadanie**.

![Początkowa replikacja została zakończona](./media/backup-configure-vault/ircomplete.png)

## <a name="questions"></a>Pytania?
Jeśli masz pytania lub jeśli brakuje Ci jakiejś funkcji, [prześlij nam opinię](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać dodatkowe informacje na temat tworzenia kopii zapasowych maszyn wirtualnych lub innych obciążeń zobacz:

* Teraz, gdy utworzono kopię zapasową plików i folderów, możesz [zarządzać swoimi magazynami i serwerami](backup-azure-manage-windows-server.md).
* Jeśli chcesz przywrócić kopię zapasową, w tym artykule znajdziesz informacje dotyczące [przywracania plików na maszynę z systemem Windows](backup-azure-restore-windows-server.md).
