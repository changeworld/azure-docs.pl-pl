---
title: Konfigurowanie raportów usługi Azure Backup
description: Konfigurowanie raportów usługi Power BI dla usługi Azure Backup przy użyciu magazynu usługi Recovery Services.
services: backup
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: adigan
ms.openlocfilehash: e3004a44958d75d18d608a2fbed7ccc44a00dc93
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60642778"
---
# <a name="configure-azure-backup-reports"></a>Konfigurowanie raportów usługi Azure Backup
W tym artykule przedstawiono kroki, aby wykonać, aby skonfigurować raporty usługi Azure Backup przy użyciu magazynu usługi Recovery Services. Pokazano również, jak dostęp do raportów przy użyciu usługi Power BI. Po wykonaniu tych czynności, możesz przejść bezpośrednio do usługi Power BI, aby wyświetlić, dostosowywanie i tworzenie raportów.

> [!IMPORTANT]
> od 1 listopada 2018 r. niektórzy klienci mogą doświadczyć problemów podczas ładowania danych w aplikacji usługi Azure Backup w usłudze Power BI. Może pojawić się komunikat z następującą informacją: „Znaleźliśmy dodatkowe znaki na końcu danych wejściowych JSON. Wyjątek został zgłoszony przez interfejs IDataReader”.
Przyczyną tego jest zmiana formatu, w którym dane są ładowane do konta magazynu.
Pobierz najnowszą wersję aplikacji (wersja 1.8) aby uniknąć tego problemu.
>
>

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
- Usługa Azure Backup, raporty, są obsługiwane w przypadku kopii zapasowych maszyn wirtualnych platformy Azure, plików i folderów kopii zapasowych w chmurze za pomocą agenta usług odzyskiwania Azure.
- Raporty dotyczące usługi Azure SQL Database, udziałów plików platformy Azure, programu Data Protection Manager i usługi Azure Backup server nie są obsługiwane w tej chwili.
- Raporty można wyświetlać między magazynami i subskrypcji, skonfigurowanie tego samego konta magazynu dla każdego z magazynów. Wybrane konto magazynu musi być w tym samym regionie co magazyn usługi Recovery Services.
- Częstotliwość zaplanowanego odświeżania raportów wynosi 24 godziny w usłudze Power BI. Możesz również wykonać ad-hoc odświeżanie raportów w usłudze Power BI. W tym przypadku najnowsze dane na koncie magazynu klienta używany do renderowania raportów.

## <a name="prerequisites"></a>Wymagania wstępne
- Tworzenie [konta usługi Azure storage](../storage/common/storage-quickstart-create-account.md) ją skonfigurować do raportów. To konto magazynu jest używane do przechowywania danych związanych z raportami.
- [Utwórz konto usługi Power BI](https://powerbi.microsoft.com/landing/signin/) wyświetlić, dostosowywanie i tworzenie własnych raportów za pomocą portalu usługi Power BI.
- Zarejestruj dostawcę zasobów **Microsoft.insights**, jeśli nie jest już zarejestrowany. Subskrypcje dla konta magazynu i magazynu usług odzyskiwania, dzięki czemu dane raportowania może przepływać do konta magazynu. Aby wykonać ten krok, przejdź do witryny Azure portal, wybierz opcję **subskrypcji** > **dostawców zasobów**i sprawdź, czy dla tego dostawcy go zarejestrować.

## <a name="configure-storage-account-for-reports"></a>Konfigurowanie konta magazynu dla raportów
Wykonaj następujące kroki, aby skonfigurować konto magazynu dla magazynu usługi Recovery Services przy użyciu witryny Azure portal. Jest to jednorazowa konfiguracja. Po skonfigurowaniu konta magazynu, możesz przejść bezpośrednio do usługi Power BI, aby wyświetlić pakiet zawartości i korzystanie z raportów.

1. Jeśli masz już magazyn usługi Recovery Services, Otwórz, przejdź do następnego kroku. Jeśli nie masz magazyn usługi Recovery Services, Otwórz w witrynie Azure portal wybierz **wszystkich usług**.

   * Na liście zasobów wpisz **usługi Recovery Services**.
   * Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Po wyświetleniu **Magazyny usługi Recovery Services**, wybierz ją.
   * Zostanie wyświetlona lista magazynów Usług odzyskiwania. Wybierz magazyn z listy magazynów Usług odzyskiwania.

     Zostanie otwarty pulpit nawigacyjny wybranego magazynu.
2. Z listy elementów, który pojawia się w obszarze magazynu, w obszarze **monitorowanie i raporty** zaznacz **raporty kopii zapasowych** skonfigurować konto magazynu dla raportów.

      ![Wybierz raporty kopii zapasowych menu elementu krok 2](./media/backup-azure-configure-reports/backup-reports-settings.PNG)
3. Na **raporty kopii zapasowych** bloku wybierz **ustawień diagnostycznych** łącza. To łącze powoduje otwarcie **ustawień diagnostycznych** interfejsu użytkownika, który jest używany do wypychania danych do konta magazynu klienta.

      ![Włącz diagnostykę, krok 3](./media/backup-azure-configure-reports/backup-azure-configure-reports.png)
4. Wybierz **Włącz diagnostykę** otworzyć Interfejs użytkownika służące do konfigurowania konta magazynu.

      ![Włącz diagnostykę w kroku 4](./media/backup-azure-configure-reports/enable-diagnostics.png)
5. W **nazwa** wprowadź nazwę ustawienia. Wybierz **Zarchiwizuj na koncie magazynu** pole wyboru, dzięki czemu raportowanie danych można uruchomić przepływać do konta magazynu.

      ![Włącz diagnostykę krok 5](./media/backup-azure-configure-reports/select-setting-name.png)
6. Wybierz **konta magazynu**, a następnie wybierz odpowiednie subskrypcji i konto magazynu z listy do przechowywania danych raportowania i wybierz **OK**.

      ![Wybierz konto magazynu — krok 6](./media/backup-azure-configure-reports/select-subscription-sa.png)
7. W obszarze **dziennika** zaznacz **AzureBackupReport** pole wyboru. Przesuń suwak, aby wybrać okres przechowywania danych raportowania. Raportowanie danych w ramach konta magazynu są przechowywane przez okres, który został wybrany za pomocą tego suwaka.

      ![Zapisz konto magazynu — krok 7](./media/backup-azure-configure-reports/save-diagnostic-settings.png)
8. Przejrzyj wszystkie zmiany, a następnie wybierz **Zapisz**. Ta akcja zagwarantuje, że wszystkie zmiany są zapisywane, a konto magazynu jest teraz skonfigurowane do przechowywania danych raportowania.

9. **Ustawień diagnostycznych** tabela pokazuje teraz nowe ustawienie jest włączone dla magazynu. Jeśli nie pojawia się, należy odświeżyć tabelę, aby sprawdzić zaktualizowane ustawienia.

      ![Wyświetl ustawienia diagnostyczne w kroku 9](./media/backup-azure-configure-reports/diagnostic-setting-row.png)

> [!NOTE]
> Po skonfigurowaniu raportów, zapisując konta magazynu *Poczekaj 24 godziny* do wypychania danych początkowych zakończyć. Importowanie aplikacji kopii zapasowej platformy Azure w usłudze Power BI dopiero po tym czasie. Aby uzyskać więcej informacji, zobacz [sekcji często zadawane pytania](#frequently-asked-questions).
>
>

## <a name="view-reports-in-power-bi"></a>Wyświetlanie raportów w usłudze Power BI
Po skonfigurowaniu konta magazynu dla raportów przy użyciu magazynu usługi Recovery Services może potrwać około 24 godzin raportowania dane zaczną być przepływają w. Po 24 godzinach od skonfigurowania konta magazynu wykonaj następujące kroki, aby wyświetlić raporty w usłudze Power BI.
Jeśli chcesz dostosować i udostępnić raport, Utwórz obszar roboczy i wykonaj następujące czynności

1. [Zaloguj się](https://powerbi.microsoft.com/landing/signin/) do usługi Power BI.
2. Wybierz pozycję **Pobieranie danych**. W **więcej sposobów na tworzenie własnej zawartości**, wybierz opcję **dodatki Service Pack zawartości**. Postępuj zgodnie z instrukcjami w [dokumentacji usługi Power BI, aby połączyć się z usługą](https://powerbi.microsoft.com/documentation/powerbi-content-packs-services/).

3. W **wyszukiwania** paska, wprowadź **kopia zapasowa Azure** i wybierz **Pobierz teraz**.

      ![Pobieranie pakietu zawartości](./media/backup-azure-configure-reports/content-pack-get.png)
4. Wprowadź nazwę konta magazynu, który został skonfigurowany w poprzednim kroku 5, a następnie wybierz pozycję **dalej**.

    ![Wprowadź nazwę konta magazynu](./media/backup-azure-configure-reports/content-pack-storage-account-name.png)    
5. Przy użyciu metody uwierzytelniania "Key", wprowadź klucz konta magazynu dla tego konta magazynu. Aby [wyświetlanie i kopiowanie kluczy dostępu do magazynu](../storage/common/storage-account-manage.md#access-keys), przejdź do swojego konta magazynu w witrynie Azure portal.

     ![Wprowadź konto magazynu](./media/backup-azure-configure-reports/content-pack-storage-account-key.png) <br/>

6. Wybierz **Zaloguj**. Po logowania zakończy się pomyślnie, zobaczysz powiadomienie importowania danych.

    ![Importowanie pakietu zawartości](./media/backup-azure-configure-reports/content-pack-importing-data.png) <br/>

    Po zakończeniu importowania zostanie wyświetlony **Powodzenie** powiadomień. W przypadku dużych ilości danych w ramach konta magazynu może zająć nieco dłużej, aby zaimportować pakiet zawartości.

    ![Importowanie pakietu zawartości sukces](./media/backup-azure-configure-reports/content-pack-import-success.png) <br/>

7. Po danych pomyślnie, importowany **kopia zapasowa Azure** pakiet zawartości jest widoczna w **aplikacje** w okienku nawigacji. W obszarze **pulpity nawigacyjne**, **raporty**, i **zestawów danych**, lista zawiera teraz usługi Azure Backup.

8. W obszarze **pulpity nawigacyjne**, wybierz opcję **kopia zapasowa Azure**, który zawiera zestaw przypięte klucza raportów.

      ![Pulpit nawigacyjny usługi Azure Backup](./media/backup-azure-configure-reports/azure-backup-dashboard.png) <br/>
9. Aby wyświetlić pełny zestaw raportów, zaznacz każdy raport na pulpicie nawigacyjnym.

      ![Program Azure health zadania kopii zapasowej](./media/backup-azure-configure-reports/azure-backup-job-health.png) <br/>
10. Wybierz każdą kartę w raportach, aby wyświetlić raporty, w tym obszarze.

      ![Usługa Azure Backup raportów karty](./media/backup-azure-configure-reports/reports-tab-view.png)


## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-storage-account"></a>Jak sprawdzić, czy dane raportowania zostało przepływać do konta magazynu?
Przejdź do konta magazynu, które zostały skonfigurowane i wybierz kontenery. Jeśli kontener zawiera wpis dla azurebackupreport — dzienniki — szczegółowe informacje, oznacza to, dane raportowania uruchomienia przepływu.

### <a name="what-is-the-frequency-of-data-push-to-a-storage-account-and-the-azure-backup-content-pack-in-power-bi"></a>Co to jest częstotliwość wypychanie danych do konta magazynu i pakietowi zawartości usługi Azure Backup w usłudze Power BI?
  Dla użytkowników w dniu 0 trwa około 24 godzinach wypychanie danych do konta magazynu. Po zakończeniu tej początkowej wypychane dane są odświeżane z częstotliwością pokazano na poniższej ilustracji.

  * Dane powiązane z **zadania**, **alerty**, **elementy kopii zapasowej**, **magazynów**, **serwerów chronionych**i  **Zasady** zostanie przypisany do konta magazynu klienta, jak i po jego zalogowaniu.

  * Dane powiązane z **magazynu** zostanie przypisany do konta magazynu klienta co 24 godziny.

       ![Częstotliwość wypychania danych w usłudze Azure raporty kopii zapasowych](./media/backup-azure-configure-reports/reports-data-refresh-cycle.png)

  * Usługa Power BI ma [zaplanowanego odświeżania raz dziennie](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/#what-can-be-refreshed). Można przeprowadzić ręczne odświeżanie danych w usłudze Power BI dla pakietu zawartości.

### <a name="how-long-can-i-retain-reports"></a>Jak długo można zachować raportów
Po skonfigurowaniu konta magazynu, można wybrać okres przechowywania danych raportu na koncie magazynu. Postępuj zgodnie z kroku 6 [Konfigurowanie konta magazynu dla raportów](backup-azure-configure-reports.md#configure-storage-account-for-reports) sekcji. Możesz również [analizowanie raportów w programie Excel](https://powerbi.microsoft.com/documentation/powerbi-service-analyze-in-excel/) i zapisać je na dłuższy okres przechowywania, zgodnie z potrzebami.

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-storage-account"></a>Moje dane w raportach będą widoczne po skonfigurować konto magazynu?
 Wszystkie dane, które są generowane po skonfigurowaniu konta magazynu zostanie przypisany do konta magazynu i jest dostępna w raportach. W toku zadania nie są wypychane do raportowania. Gdy zadanie zakończy się lub kończy się niepowodzeniem, jest wysyłana do raportów.

### <a name="if-i-already-configured-the-storage-account-to-view-reports-can-i-change-the-configuration-to-use-another-storage-account"></a>Jeśli mam już skonfigurowane konto magazynu, aby wyświetlić raporty, można zmienić konfigurację, aby użyć innego konta magazynu?
Tak, można zmienić konfiguracji, aby wskazywał innego konta magazynu. Podczas łączenia z pakietem zawartości usługi Azure Backup, należy użyć nowo skonfigurowanego konta magazynu. Ponadto po skonfigurowaniu konta magazynu innego nowego przepływu danych na tym koncie magazynu. Starsze dane (przed zmianą konfiguracji) nadal pozostaje w starszych konta magazynu.

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>W subskrypcji i magazynów mogą wyświetlać raporty?
Tak, można skonfigurować tego samego konta magazynu w różnych magazynów, aby wyświetlić raporty dla wielu magazynu. Ponadto można skonfigurować tego samego konta magazynu dla magazynów w subskrypcjach. Następnie przy użyciu tego konta magazynu, gdy połączysz się z pakietem zawartości usługi Azure Backup w usłudze Power BI do wyświetlania raportów. Wybrane konto magazynu musi być w tym samym regionie co magazyn usługi Recovery Services.

## <a name="troubleshooting-errors"></a>Rozwiązywanie problemów z błędami
| Szczegóły błędu | Rozwiązanie |
| --- | --- |
| Po skonfigurowaniu konta magazynu dla raportów Backup **konta magazynu** nadal pokazuje **nieskonfigurowane**. | Jeśli został pomyślnie skonfigurowany na koncie magazynu danych raportowania przepływu w ramach niezależnie od tego problemu. Aby rozwiązać ten problem, przejdź do usługi Azure portal i wybierz pozycję **wszystkich usług** > **ustawień diagnostycznych** > **magazyn usługi Recovery Services**  >  **Edytuj ustawienie**. Usuń poprzednio skonfigurowane ustawienie i Utwórz nowe ustawienie w tym samym bloku. Tym razem w **nazwa** wybierz opcję **usługi**. Pojawi się skonfigurowanego konta magazynu. |
|Po zaimportowaniu pakietu zawartości usługi Azure Backup w usłudze Power BI, błąd "nie odnaleziono 404-container", który zostanie wyświetlony komunikat. | Jak wspomniano wcześniej należy poczekać 24 godziny po skonfigurowaniu raportów w magazynie usługi Recovery Services, aby zobaczyć je poprawnie w usłudze Power BI. Jeśli zostanie podjęta próba dostępu do raportów przed 24 godziny, ten komunikat o błędzie pojawia się, ponieważ pełnych danych nie jest jeszcze obecna, aby wyświetlić raporty prawidłowe. |

## <a name="next-steps"></a>Kolejne kroki
Po skonfigurowaniu konta magazynu i zaimportować pakiet zawartości usługi Azure Backup, kolejne kroki dotyczą Dostosowywanie raportów i tworzenie raportów przy użyciu raportowania modelu danych. Aby uzyskać więcej informacji zobacz następujące artykuły.

* [Użycie usługi Azure Backup w modelu danych raportowania](backup-azure-reports-data-model.md)
* [Filtr raportów usługi Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
* [Tworzenie raportów w usłudze Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)
