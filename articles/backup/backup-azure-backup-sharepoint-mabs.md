---
title: Użyj serwera usługi Kopia zapasowa Azure, aby utworzyć kopię zapasową farmy programu SharePoint do platformy Azure
description: Serwer kopii zapasowej Azure umożliwia tworzenie kopii zapasowej i przywracanie danych programu SharePoint. Ten artykuł zawiera informacje, aby skonfigurować farmę programu SharePoint, tak aby żądanych danych mogą być przechowywane na platformie Azure. Chronione dane SharePoint można przywrócić z dysku lub z platformy Azure.
services: backup
author: pvrk
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 03/24/2017
ms.author: pullabhk
ms.openlocfilehash: f7b69e2558234159075161be7d58cc3695dfbbaf
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606056"
---
# <a name="back-up-a-sharepoint-farm-to-azure"></a>Tworzenie kopii zapasowych farmy programu SharePoint na platformie Azure
Możesz utworzyć kopię zapasową farmy programu SharePoint do systemu Microsoft Azure przy użyciu usługi Microsoft Azure kopii zapasowej serwera (MABS) w znacznie tak samo jak wykonanie kopii zapasowej innych źródeł danych. Kopia zapasowa Azure zapewnia elastyczność w harmonogram tworzenia kopii zapasowych, aby utworzyć codziennie, co tydzień, miesięcznego lub rocznego kopii zapasowej wskazuje i udostępnia opcje zasad przechowywania dla różnych punktów kopii zapasowej. Zapewnia także możliwość przechowywania kopii dysku lokalnym dla szybka celami czasu odzyskiwania (RTO) i Zapisz kopie Azure ekonomiczny, długoterminowego przechowywania.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>Obsługiwane wersje programu SharePoint, a powiązane scenariusze ochrony
Kopia zapasowa Azure dla programu DPM obsługuje następujące scenariusze:

| Obciążenie | Wersja | Wdrażanie SharePoint | Ochrona i odzyskiwanie |
| --- | --- | --- | --- | --- | --- |
| Sharepoint |SharePoint 2013, SharePoint programu SharePoint 2010, SharePoint 2007 3.0 |SharePoint wdrożony jako serwer fizyczny lub maszyny wirtualnej funkcji Hyper-V/VMware <br> -------------- <br> Funkcji SQL AlwaysOn | Ochrona opcje odzyskiwania farmy programu SharePoint: farmy odzyskiwania bazy danych i plik lub element listy z punktów odzyskiwania dysku.  Odzyskiwania farmy i bazy danych z punktów odzyskiwania systemu Azure. |

## <a name="before-you-start"></a>Przed rozpoczęciem
Istnieje kilka rzeczy, które trzeba upewnić się, aby wykonać kopię zapasową farmy programu SharePoint do platformy Azure.

### <a name="prerequisites"></a>Wymagania wstępne
Przed kontynuowaniem upewnij się, że masz [zainstalowane i przygotować serwer kopii zapasowej Azure](backup-azure-microsoft-azure-backup.md) ochrony obciążeń.

### <a name="protection-agent"></a>Agent ochrony
Musi być zainstalowany agent ochrony na serwerze, który jest uruchomiony, SharePoint, serwery z programem SQL Server i wszystkich serwerów, które należą do farmy programu SharePoint. Aby uzyskać więcej informacji na temat sposobu konfigurowania agenta ochrony, zobacz [instalacji agenta ochrony](https://technet.microsoft.com/library/hh758034\(v=sc.12\).aspx).  Jedynym wyjątkiem jest, zainstaluj agenta tylko na jednym serwerze sieci web frontonu (WFE). Program DPM wymaga agenta na jednym serwerze WFE tylko służy jako punkt wejścia do ochrony.

### <a name="sharepoint-farm"></a>Farma programu SharePoint
Dla każdych 10 milionów elementów w farmie musi istnieć co najmniej 2 GB miejsca na woluminie, w którym znajduje się MABS folder. Ta przestrzeń jest wymagany do generowania katalogu. W przypadku MABS odzyskać określone elementy (zbiory witryn, witryn listy, biblioteki dokumentów, foldery, pojedyncze dokumenty i elementy listy) generowania katalogu tworzy listę adresów URL, które są zawarte w każdej bazie danych zawartości. Można wyświetlić listę adresów URL w okienku elementy możliwe do odzyskania w **odzyskiwania** obszarze Konsola administratora MABS zadań.

### <a name="sql-server"></a>Oprogramowanie SQL Server
MABS działa jako konto systemu lokalnego. Aby utworzyć kopię zapasową bazy danych programu SQL Server, MABS wymaga uprawnień administratora na tym koncie dla serwera, na którym działa program SQL Server. Ustaw Zarządzanie NT\System *sysadmin* na serwerze, na którym działa program SQL Server przed jego kopię zapasową.

Jeśli farma programu SharePoint zawiera bazy danych programu SQL Server, które skonfigurowano z aliasami programu SQL Server, należy zainstalować składniki klienta programu SQL Server na serwerze frontonu sieci Web, które będzie chronione MABS.

### <a name="sharepoint-server"></a>Oprogramowanie SharePoint Server
Gdy wydajność zależy od wielu czynników, takich jak rozmiar farmy programu SharePoint, stanowią ogólne wskazówki MABS jeden może chronić farmy programu SharePoint 25 TB.

### <a name="whats-not-supported"></a>Jakie operacje nie są obsługiwane
* MABS, który zapewnia ochronę farmy programu SharePoint nie chroni indeksy wyszukiwania lub bazy danych aplikacji usługi. Należy skonfigurować osobno ochrony tych baz danych.
* MABS nie ma kopii zapasowej baz danych serwera SQL programu SharePoint, które znajdują się w udziałach serwera (SOFS) plików skalowalnego w poziomie.

## <a name="configure-sharepoint-protection"></a>Konfigurowanie ochrony programu SharePoint
Przed MABS można użyć do ochrony programu SharePoint, należy skonfigurować usługę składnika zapisywania usługi VSS programu SharePoint (usługa składnika zapisywania programu WSS) przy użyciu **ConfigureSharePoint.exe**.

Można znaleźć **ConfigureSharePoint.exe** w folderze \bin [ścieżka instalacji MABS] na serwerze frontonu sieci web. To narzędzie udostępnia agentowi ochrony poświadczenia farmy programu SharePoint. Możesz uruchomić na jednym serwerze WFE. Jeśli masz wiele serwerów WFE, należy wybrać jeden z nich podczas konfigurowania grupy ochrony.

### <a name="to-configure-the-sharepoint-vss-writer-service"></a>Aby skonfigurować usługę składnika zapisywania usługi VSS programu SharePoint
1. Na serwerze WFE, w wierszu polecenia przejdź do \bin\ [Lokalizacja instalacji MABS]
2. Wprowadź ConfigureSharePoint - EnableSharePointProtection.
3. Wprowadź poświadczenia administratora farmy. To konto powinno być członkiem lokalnej grupy administratorów na serwerze WFE. Jeśli administrator farmy nie jest lokalnym administratorem Przyznaj poniższe uprawnienia na serwerze WFE:
   * Przyznaj grupie WSS_ADMIN_WPG uprawnienia Pełna kontrola do folderu programu DPM (% Program Files%\Microsoft Azure Backup\DPM).
   * Przyznaj grupie WSS_ADMIN_WPG uprawnienia odczytu do klucza rejestru programu DPM (HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager).

> [!NOTE]
> Musisz ponownie uruchomić ConfigureSharePoint.exe, gdy istnieje zmiana poświadczeń administratora farmy programu SharePoint.
>
>

## <a name="back-up-a-sharepoint-farm-by-using-mabs"></a>Kopii zapasowej farmy programu SharePoint za pomocą MABS
Po skonfigurowaniu MABS i farmy programu SharePoint, jak opisano wcześniej, SharePoint mogą być chronione przez MABS.

### <a name="to-protect-a-sharepoint-farm"></a>Aby chronić farmy programu SharePoint
1. Z **ochrony** kartę w konsoli administratora MABS, kliknij przycisk **nowy**.
    ![Nowa karta ochrony](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)
2. Na **wybierz typ grupy ochrony** strony **tworzenia nowej grupy ochrony** kreatora, zaznacz **serwerów**, a następnie kliknij przycisk **dalej**.

    ![Wybierz grupę ochrony typu](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)
3. Na **Wybierz członków grupy** ekranu, zaznacz pole wyboru dla serwera programu SharePoint chcesz chronić, a następnie kliknij przycisk **dalej**.

    ![Wybierz członków grupy](./media/backup-azure-backup-sharepoint/select-group-members2.png)

   > [!NOTE]
   > Z zainstalowanym agentem ochrony zobacz temat serwer w kreatorze. MABS zawiera także jego struktury. Ponieważ uruchomiono ConfigureSharePoint.exe MABS komunikuje się z usługą składnik zapisywania usługi VSS programu SharePoint i jego odpowiednie baz danych programu SQL Server i rozpoznaje struktury farmy programu SharePoint i skojarzonych baz danych zawartości oraz wszystkie odpowiednie elementy.
   >
   >
4. Na **wybierz metodę ochrony danych** strony, wprowadź nazwę **grupy ochrony**i wybierz preferowany *metody ochrony*. Kliknij przycisk **Dalej**.

    ![Wybierz metodę ochrony danych](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)

   > [!NOTE]
   > Metoda ochrony dysku pozwala osiąganie celów czasu odzyskiwania krótki.
   >
   >
5. Na **Określ cele krótkoterminowe** wybierz preferowany **zakres przechowywania** i ustalić, kiedy chcesz, aby tworzenie kopii zapasowej.

    ![Określanie celów krótkoterminowych](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)

   > [!NOTE]
   > Ponieważ odzyskiwania najczęściej jest wymagana dla danych, która jest mniejsza niż pięć dni, możemy wybrany zakres przechowywania 5 dni na dysku i zapewnić, że tworzenie kopii zapasowej odbywa się podczas nieprodukcyjnych godzin, w tym przykładzie.
   >
   >
6. Przejrzyj miejsce przydzielone do grupy ochrony na dysku puli magazynów, a następnie kliknij przycisk **dalej**.
7. Dla każdej grupy ochrony MABS przydziela miejsce na dysku do przechowywania replik. W tym momencie MABS należy utworzyć kopię wybranych danych. Wybierz, jak i kiedy chcesz utworzyć replikę, a następnie kliknij przycisk **dalej**.

    ![Wybierz metodę tworzenia repliki](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)

   > [!NOTE]
   > Aby upewnić się, że ruch sieciowy nie jest wykonywane, wybierz godzinę poza godzinami produkcji.
   >
   >
8. MABS zapewnia integralność danych poprzez przeprowadzenie sprawdzania spójności w replice. Dostępne są dwie opcje dostępne. Można zdefiniować harmonogram wykonywania sprawdzania spójności lub programu DPM można uruchomić sprawdzania spójności automatycznie w replice zawsze, gdy stanie się niespójna. Wybierz preferowaną opcję, a następnie kliknij przycisk **dalej**.

    ![Sprawdzanie spójności](./media/backup-azure-backup-sharepoint/consistency-check.png)
9. Na **Określ dane chronione Online** farmy programu SharePoint, które chcesz chronić, a następnie kliknij przycisk Wybierz **dalej**.

    ![Program DPM Protection1 programu SharePoint](./media/backup-azure-backup-sharepoint/select-online-protection1.png)
10. Na **Określanie harmonogramu tworzenia kopii zapasowej Online** , wybierz preferowany harmonogram, a następnie kliknij przycisk **dalej**.

    ![Online_backup_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)

    > [!NOTE]
    > MABS zawiera maksymalnie dwóch kopii zapasowych codziennie Azure z następnie dostępne najnowszego punktu kopii zapasowej dysku. Kopia zapasowa Azure można również sterować ilość przepustowości sieci WAN, który może służyć do przechowywania kopii zapasowych w szczycie i poza godzinami pracy przy użyciu [ograniczania sieci kopia zapasowa Azure](https://azure.microsoft.com/documentation/articles/backup-configure-vault/#enable-network-throttling).
    >
    >
11. W zależności od harmonogram tworzenia kopii zapasowych, zostanie wybrany na **Określanie zasad przechowywania Online** wybierz zasady przechowywania dla punktów kopii zapasowej codziennie, co tydzień, miesięcznych i rocznych.

    ![Online_retention_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)

    > [!NOTE]
    > MABS wykorzystuje schemat przechowywania dziadek ojciec syn. w którym można wybrać zasady przechowywania różne dla różnych punktów kopii zapasowej.
    >
    >
12. Podobnie jak dysk, replika punktu początkowego odwołanie musi zostać utworzona na platformie Azure. Wybierz preferowaną opcję tworzenia początkowej kopii zapasowej na platformie Azure, a następnie kliknij przycisk **dalej**.

    ![Online_replica](./media/backup-azure-backup-sharepoint/online-replication.png)
13. Przejrzyj wybrane ustawienia na **Podsumowanie** , a następnie kliknij przycisk **Utwórz grupę**. Po utworzeniu grupy ochrony, zobaczą komunikat z potwierdzeniem.

    ![Podsumowanie](./media/backup-azure-backup-sharepoint/summary.png)

## <a name="restore-a-sharepoint-item-from-disk-by-using-mabs"></a>Przywróć element programu SharePoint z dysku przy użyciu MABS
W poniższym przykładzie *element odzyskiwanie programu SharePoint* został przypadkowo usunięty i mają zostać odzyskane.
![MABS Protection4 programu SharePoint](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Otwórz **konsoli administratora programu DPM**. Wszystkie farmy programu SharePoint, które są chronione przez program DPM są wyświetlane w **ochrony** kartę.

    ![MABS Protection3 programu SharePoint](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Aby rozpocząć odzyskać element, wybierz **odzyskiwania** kartę.

    ![MABS Protection5 programu SharePoint](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. Umożliwia wyszukiwanie programu SharePoint dla *element odzyskiwanie programu SharePoint* przy użyciu symboli wieloznacznych na podstawie wyszukiwania w ramach odzyskiwania punktu zakresu.

    ![MABS Protection6 programu SharePoint](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Wybierz punkt odzyskiwania odpowiednie w wynikach wyszukiwania, kliknij element prawym przyciskiem myszy, a następnie wybierz **odzyskać**.
5. Można również przejrzeć różnych punktów odzyskiwania i wybierz bazę danych lub element, aby odzyskać. Wybierz **Data > Godzina odzyskiwania**, a następnie wybierz poprawny **bazy danych > farmy programu SharePoint > punktu odzyskiwania > elementu**.

    ![MABS Protection7 programu SharePoint](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. Kliknij element prawym przyciskiem myszy, a następnie wybierz **odzyskać** otworzyć **Kreatora odzyskiwania**. Kliknij przycisk **Dalej**.

    ![Przegląd wybranego elementu odzyskiwania](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. Wybierz typ odzyskiwania, które chcesz przeprowadzić, a następnie kliknij przycisk **dalej**.

    ![Typ odzyskiwania](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

   > [!NOTE]
   > Wybór **Odzyskaj do oryginalnego** w przykładzie odzyskuje element do oryginalnej witryny programu SharePoint.
   >
   >
8. Wybierz **proces odzyskiwania** którego chcesz używać.

   * Wybierz **odzyskać bez używania farmy odzyskiwania** Jeśli farma programu SharePoint nie została zmieniona i jest taki sam jak punkt odzyskiwania, który jest przywracana.
   * Wybierz **odzyskać za pomocą farmy odzyskiwania** Jeśli farma SharePoint zmieniła się od czasu utworzenia punktu odzyskiwania.

     ![Proces odzyskiwania](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. Podaj tymczasowej lokalizacji wystąpienia programu SQL Server tymczasowo przywrócić bazę danych i podaj przemieszczania udziału plików na MABS oraz serwera z programem SharePoint, aby odzyskać elementu.

    ![Location1 przemieszczania](./media/backup-azure-backup-sharepoint/staging-location1.png)

    MABS dołącza bazy danych zawartości, który jest hostem element programu SharePoint do tymczasowego wystąpienia programu SQL Server. Z bazy danych zawartości odzyskuje elementu i umieszcza je na tymczasowej lokalizacji pliku na MABS. Odzyskiwanego elementu, który znajduje się w tymczasowej lokalizacji teraz musi zostać wyeksportowany do lokalizacji tymczasowej na farmie SharePoint.

    ![Location2 przemieszczania](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. Wybierz **Określ opcje odzyskiwania**i stosuje ustawienia zabezpieczeń do farmy programu SharePoint lub Zastosuj ustawienia zabezpieczeń dla punktu odzyskiwania. Kliknij przycisk **Dalej**.

    ![Opcje odzyskiwania](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > Istnieje możliwość ograniczania użycia przepustowości sieci. Pozwala to zmniejszyć wpływ na serwerze produkcyjnym poza godzinami produkcji.
    >
    >
11. Sprawdź informacje, a następnie kliknij przycisk **odzyskać** aby rozpocząć odzyskiwanie pliku.

    ![Podsumowanie odzyskiwania](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Teraz wybierz **monitorowanie** karcie **konsoli administratora MABS** Aby wyświetlić **stan** odzyskiwania.

    ![Stan odzyskiwania](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > Plik jest przywrócone. Można odświeżać Sprawdź przywróconych plików witryny programu SharePoint.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Przywróć bazę danych programu SharePoint z platformy Azure za pomocą programu DPM
1. Aby odzyskać bazę danych zawartości programu SharePoint, przejdź przez różne punkty odzyskiwania (jak pokazano wcześniej), a następnie wybierz punkt odzyskiwania, który chcesz przywrócić.

    ![MABS Protection8 programu SharePoint](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Kliknij dwukrotnie punkt odzyskiwania SharePoint, aby wyświetlić dostępne informacje o katalogu programu SharePoint.

   > [!NOTE]
   > Ponieważ farmy programu SharePoint jest chroniony w celu przechowywania długoterminowego na platformie Azure, informacje katalogu (metadanych) są niedostępne na MABS. W związku z tym zawsze, gdy baza danych zawartości programu SharePoint w momencie mają zostać odzyskane, musisz ponownie w katalogu farmy programu SharePoint.
   >
   >
3. Kliknij przycisk **ponownego katalogowania**.

    ![MABS Protection10 programu SharePoint](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    **Ponownie skatalogować chmury** otwiera się okno stanu.

    ![MABS Protection11 programu SharePoint](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Po zakończeniu skatalogowania stan zmienia się na *Powodzenie*. Kliknij przycisk **Zamknij**.

    ![MABS Protection12 programu SharePoint](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Kliknij obiekt SharePoint pokazano MABS **odzyskiwania** kartę, aby pobrać struktury bazy danych zawartości. Kliknij element prawym przyciskiem myszy, a następnie kliknij przycisk **odzyskać**.

    ![MABS Protection13 programu SharePoint](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. W tym momencie wykonać [opisane w tym artykule](#restore-a-sharepoint-item-from-disk-using-dpm) odzyskać bazę danych zawartości programu SharePoint z dysku.

## <a name="faqs"></a>Często zadawane pytania
Pytanie: czy element programu SharePoint do oryginalnej lokalizacji można odzyskać skonfigurowanie programu SharePoint przy użyciu funkcji SQL AlwaysOn (Ochrona na dysku)?<br>
Odpowiedź: tak elementu można odzyskać do oryginalnej witryny programu SharePoint.

Pytanie: czy bazy danych programu SharePoint do oryginalnej lokalizacji można odzyskać Jeśli programu SharePoint została skonfigurowana przy użyciu funkcji SQL AlwaysOn?<br>
Odpowiedź: ponieważ baz danych programu SharePoint są konfigurowane w funkcji SQL AlwaysOn, nie można modyfikować, chyba że zostanie usunięta grupa dostępności. W związku z tym MABS nie można przywrócić bazę danych do oryginalnej lokalizacji. Można odzyskać bazy danych programu SQL Server do innego wystąpienia programu SQL Server.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o MABS ochrony programu SharePoint — zobacz [wideo serii — Program DPM ochronę programu SharePoint](http://channel9.msdn.com/Series/Azure-Backup/Microsoft-SCDPM-Protection-of-SharePoint-1-of-2-How-to-create-a-SharePoint-Protection-Group)
