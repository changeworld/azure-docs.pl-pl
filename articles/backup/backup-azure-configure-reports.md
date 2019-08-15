---
title: Skonfiguruj raporty dla Azure Backup
description: Skonfiguruj Power BI raporty dla Azure Backup przy użyciu magazynu Recovery Services.
ms.reviewer: adigan
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: dacurwin
ms.openlocfilehash: 293af600f4bd58efe8383d019ca3d17f724f242c
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933332"
---
# <a name="configure-azure-backup-reports"></a>Konfiguruj raporty usługi Azure Backup
W tym artykule przedstawiono kroki, które należy wykonać, aby skonfigurować raporty dla Azure Backup przy użyciu magazynu Recovery Services. Pokazano również, jak uzyskać dostęp do raportów za pomocą Power BI. Po wykonaniu tych kroków możesz przejść bezpośrednio do Power BI, aby wyświetlać, dostosowywać i tworzyć raporty.

> [!IMPORTANT]
> od 1 listopada 2018 r. niektórzy klienci mogą doświadczyć problemów podczas ładowania danych w aplikacji usługi Azure Backup w usłudze Power BI. Może pojawić się komunikat z następującą informacją: „Znaleźliśmy dodatkowe znaki na końcu danych wejściowych JSON. Wyjątek został zgłoszony przez interfejs IDataReader”.
Przyczyną tego jest zmiana formatu, w którym dane są ładowane do konta magazynu.
Pobierz najnowszą aplikację (w wersji 1,8), aby uniknąć tego problemu.
>
>

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
- Raporty Azure Backup są obsługiwane w przypadku kopii zapasowych maszyn wirtualnych platformy Azure oraz kopii zapasowych plików i folderów w chmurze przy użyciu agenta usługi Azure Recovery Services.
- Raporty dotyczące Azure SQL Database, udziałów plików platformy Azure, Data Protection Manager i serwera Azure Backup nie są obecnie obsługiwane.
- Raporty można wyświetlać w magazynach i subskrypcjach, jeśli konto magazynu jest skonfigurowane dla każdego z magazynów. Wybrane konto magazynu musi znajdować się w tym samym regionie co magazyn Recovery Services.
- Częstotliwość zaplanowanego odświeżania raportów wynosi 24 godziny w Power BI. Można także wykonać odświeżenie raportów ad hoc w Power BI. W takim przypadku najnowsze dane na koncie magazynu klienta są używane do renderowania raportów.

## <a name="prerequisites"></a>Wymagania wstępne
- Utwórz [konto usługi Azure Storage](../storage/common/storage-quickstart-create-account.md) , aby skonfigurować je do raportów. To konto magazynu służy do przechowywania danych związanych z raportami.
- [Utwórz konto Power BI](https://powerbi.microsoft.com/landing/signin/) , aby wyświetlać, dostosowywać i tworzyć własne raporty przy użyciu portalu Power BI.
- Zarejestruj dostawcę zasobów **Microsoft. Insights**, jeśli nie jest już zarejestrowany. Użyj subskrypcji dla konta magazynu i magazynu Recovery Services, aby dane raportowania mogły zostać przekazane do konta magazynu. Aby wykonać ten krok, przejdź do Azure Portal, wybierz pozycję**dostawcy zasobów** **subskrypcji** > i sprawdź, czy ten dostawca jest zarejestrowany.

## <a name="configure-storage-account-for-reports"></a>Skonfiguruj konto magazynu dla raportów
Wykonaj następujące kroki, aby skonfigurować konto magazynu dla magazynu Recovery Services przy użyciu Azure Portal. Jest to jednorazowa konfiguracja. Po skonfigurowaniu konta magazynu możesz przejść bezpośrednio do Power BI, aby wyświetlić aplikację szablonu i korzystać z raportów.

1. Jeśli masz już otwarty magazyn Recovery Services, przejdź do następnego kroku. Jeśli nie masz otwartego magazynu Recovery Services, w Azure Portal wybierz pozycję **wszystkie usługi**.

   * Na liście zasobów wprowadź **Recovery Services**.
   * Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Gdy widzisz **Recovery Services magazynów**, wybierz ją.
   * Zostanie wyświetlona lista magazynów Usług odzyskiwania. Wybierz magazyn z listy magazynów Usług odzyskiwania.

     Zostanie otwarty pulpit nawigacyjny wybranego magazynu.
2. Z listy elementów, które pojawiają się w magazynie, w sekcji **monitorowanie i raporty** wybierz pozycję **raporty kopii zapasowych** , aby skonfigurować konto magazynu dla raportów.

      ![Wybierz element menu raporty kopii zapasowej krok 2](./media/backup-azure-configure-reports/backup-reports-settings.PNG)
3. W bloku **raporty kopii zapasowych** wybierz łącze **Ustawienia diagnostyki** . To łącze powoduje otwarcie interfejsu użytkownika **ustawień diagnostyki** , który służy do wypychania danych do konta magazynu klienta.

      ![Włączanie diagnostyki — krok 3](./media/backup-azure-configure-reports/backup-azure-configure-reports.png)
4. Wybierz pozycję **Włącz diagnostykę** , aby otworzyć interfejs użytkownika służący do konfigurowania konta magazynu.

      ![Włączanie diagnostyki — krok 4](./media/backup-azure-configure-reports/enable-diagnostics.png)
5. W polu **Nazwa** wprowadź nazwę ustawienia. Zaznacz pole wyboru **Archiwizuj do konta magazynu** , aby dane raportowania mogły zacząć przepływać do konta magazynu.

      ![Włączanie diagnostyki — krok 5](./media/backup-azure-configure-reports/select-setting-name.png)
6. Wybierz pozycję **konto magazynu**, wybierz odpowiednią subskrypcję i konto magazynu z listy w celu przechowywania danych raportowania, a następnie wybierz **przycisk OK**.

      ![Wybierz konto magazynu krok 6](./media/backup-azure-configure-reports/select-subscription-sa.png)
7. W sekcji **log (Dziennik** ) zaznacz pole wyboru **AzureBackupReport** . Przesuń suwak, aby wybrać okres przechowywania dla tych danych raportowania. Dane raportowania na koncie magazynu są przechowywane dla wybranego okresu przy użyciu tego suwaka.

      ![Zapisz konto magazynu krok 7](./media/backup-azure-configure-reports/save-diagnostic-settings.png)
8. Przejrzyj wszystkie zmiany i wybierz pozycję **Zapisz**. Ta akcja zapewnia, że wszystkie zmiany zostaną zapisane, a konto magazynu jest teraz skonfigurowane do przechowywania danych raportowania.

9. W tabeli **ustawień diagnostycznych** jest teraz wyświetlane nowe ustawienie włączone dla magazynu. Jeśli ta opcja nie zostanie wyświetlona, Odśwież tabelę, aby wyświetlić zaktualizowane ustawienie.

      ![Wyświetl ustawienie diagnostyczne krok 9](./media/backup-azure-configure-reports/diagnostic-setting-row.png)

> [!NOTE]
> Po skonfigurowaniu raportów przez zapisanie konta magazynu poczekaj *24 godziny* na zakończenie wypychania danych. Zaimportuj aplikację Azure Backup w Power BI dopiero po tym czasie. Aby uzyskać więcej informacji, zobacz [sekcję często zadawanych pytań](backup-azure-monitor-alert-faq.md).
>
>

## <a name="view-reports-in-power-bi"></a>Wyświetlanie raportów w Power BI
Po skonfigurowaniu konta magazynu dla raportów przy użyciu magazynu Recovery Services dane raportowania mogą zaczynać się około 24 godzin. Po 24 godzinach konfigurowania konta magazynu wykonaj następujące kroki, aby wyświetlić raporty w Power BI.
Jeśli chcesz dostosować i udostępnić raport, Utwórz obszar roboczy i wykonaj następujące czynności

1. [Zaloguj](https://powerbi.microsoft.com/landing/signin/) się do Power BI.
2. Przejdź do **aplikacji, > uzyskać więcej aplikacji z usługi Microsoft AppSource**. Postępuj zgodnie z instrukcjami w [dokumentacji Power BI, aby nawiązać połączenie z usługą](https://powerbi.microsoft.com/documentation/powerbi-content-packs-services/).

3. Na pasku **wyszukiwania** wprowadź **Azure Backup** i wybierz pozycję **Pobierz teraz**.

      ![Pobierz aplikację szablonu](./media/backup-azure-configure-reports/template-app-get.png)
4. Wprowadź nazwę konta magazynu, które zostało skonfigurowane w poprzednim kroku 5, a następnie wybierz przycisk **dalej**.

    ![Podaj nazwę konta magazynu](./media/backup-azure-configure-reports/content-pack-storage-account-name.png)    
5. Przy użyciu metody uwierzytelniania "klucz" Wprowadź klucz konta magazynu dla tego konta magazynu. Aby [wyświetlić i skopiować klucze dostępu do magazynu](../storage/common/storage-account-manage.md#access-keys), przejdź do swojego konta magazynu w Azure Portal.

     ![Wprowadź konto magazynu](./media/backup-azure-configure-reports/content-pack-storage-account-key.png) <br/>

6. Wybierz pozycję **Zaloguj**. Po pomyślnym zalogowaniu zostanie wyświetlone powiadomienie o zaimportowaniu danych.

    ![Importowanie pakietu zawartości](./media/backup-azure-configure-reports/content-pack-importing-data.png) <br/>

    Po zakończeniu importowania zobaczysz powiadomienie o **powodzeniu** . Jeśli ilość danych na koncie magazynu jest duża, importowanie aplikacji szablonu może zająć trochę czasu.

    ![Importuj pakiet zawartości o powodzeniu](./media/backup-azure-configure-reports/content-pack-import-success.png) <br/>

7. Po pomyślnym zaimportowaniu danych aplikacja szablonu **Azure Backup** będzie widoczna w **aplikacjach** w okienku nawigacji. W obszarze **pulpity nawigacyjne**, **raporty**i **zestawy danych**lista zawiera teraz Azure Backup.

8. Wobszarze pulpity nawigacyjne wybierz pozycję **Azure Backup**, która pokazuje zestaw przypiętych raportów kluczy.

      ![Pulpit nawigacyjny Azure Backup](./media/backup-azure-configure-reports/azure-backup-dashboard.png) <br/>
9. Aby wyświetlić pełny zestaw raportów, wybierz dowolny raport na pulpicie nawigacyjnym.

      ![Azure Backup kondycji zadania](./media/backup-azure-configure-reports/azure-backup-job-health.png) <br/>
10. Wybierz każdą kartę w raportach, aby wyświetlić raporty w tym obszarze.

      ![Karty Azure Backup raporty](./media/backup-azure-configure-reports/reports-tab-view.png)


## <a name="troubleshooting-errors"></a>Rozwiązywanie problemów z błędami
| Szczegóły błędu | Rozwiązanie |
| --- | --- |
| Po skonfigurowaniu konta magazynu dla raportów kopii zapasowych nadal **nie jest skonfigurowane** **konto magazynu** . | Po pomyślnym skonfigurowaniu konta magazynu dane raportowania są przesyłane pomimo tego problemu. Aby rozwiązać ten problem, przejdźdo Azure Portal i wybierz kolejno opcje **wszystkie usługi** > **Ustawienia** > diagnostyki**Recovery Services magazyn** > . Usuń poprzednio skonfigurowane ustawienie i Utwórz nowe ustawienie w tym samym bloku. Tym razem w polu **Nazwa** wybierz pozycję **Usługa**. Teraz zostanie wyświetlone skonfigurowane konto magazynu. |
|Po zaimportowaniu aplikacji Azure Backup Template w programie Power BI zostanie wyświetlony komunikat o błędzie "404-kontener nie zostanie znaleziony". | Jak wspomniano wcześniej, należy odczekać 24 godziny od momentu skonfigurowania raportów w magazynie Recovery Services, aby zobaczyć je prawidłowo w Power BI. Jeśli spróbujesz uzyskać dostęp do raportów przed 24 godzinami, zostanie wyświetlony komunikat o błędzie, ponieważ kompletne dane nie są jeszcze dostępne, aby pokazać prawidłowe raporty. |

## <a name="next-steps"></a>Następne kroki
Po skonfigurowaniu konta magazynu i zaimportowaniu aplikacji Azure Backup Template, następne kroki mają na celu dostosowanie raportów i użycie modelu danych raportowania do tworzenia raportów. Aby uzyskać więcej informacji, zobacz następujące artykuły.

* [Korzystanie z modelu danych raportowania Azure Backup](backup-azure-reports-data-model.md)
* [Filtrowanie raportów w Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
* [Tworzenie raportów w Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)
