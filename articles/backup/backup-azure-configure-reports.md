---
title: Konfigurowanie raportów usługi Azure Backup
description: Konfigurowanie raportów usługi Power BI dla usługi Azure Backup przy użyciu magazynu usługi Recovery Services.
services: backup
author: adiganmsft
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 07/26/2018
ms.author: adigan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c59653bf3709f7798fd92a44fa420b99f2cbc6b6
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2018
ms.locfileid: "45733576"
---
# <a name="configure-azure-backup-reports"></a>Konfigurowanie raportów usługi Azure Backup
Ten artykuł zawiera informacje o krokach można skonfigurować raportów dla usługi Azure Backup przy użyciu magazynu usługi Recovery Services i dostępu do tych raportów przy użyciu usługi Power BI. Po wykonaniu tych kroków, możesz bezpośrednio przejść do usługi Power BI, aby wyświetlić wszystkie raporty, dostosowywanie i tworzenie raportów. 

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
1. Raporty usługi Azure Backup są obsługiwane w przypadku kopii zapasowych maszyn wirtualnych platformy Azure i kopia zapasowa plików/folderów do chmury przy użyciu agenta usługi Azure Recovery Services.
2. Raporty dotyczące usługi Azure SQL, program DPM i usługi Azure Backup Server nie są obsługiwane w tej chwili.
3. Mogą wyświetlać raporty różnych magazynów i różnych subskrypcji, skonfigurowanie tego samego konta magazynu dla każdego z magazynów. Wybrane konto magazynu należy w tym samym regionie co magazyn usługi recovery services.
4. Częstotliwość zaplanowanego odświeżania raportów wynosi 24 godziny w usłudze Power BI. Odświeżanie zapytań ad-hoc raportów można również wykonać w usłudze Power BI, w którym wielkość najnowsze dane na koncie magazynu klienta jest używany do renderowania raportów. 

## <a name="prerequisites"></a>Wymagania wstępne
1. Tworzenie [konta usługi Azure storage](../storage/common/storage-quickstart-create-account.md) ją skonfigurować do raportów. To konto magazynu jest używane do przechowywania danych powiązanych raportów.
2. [Utwórz konto usługi Power BI](https://powerbi.microsoft.com/landing/signin/) wyświetlić, dostosowywanie i tworzenie własnych raportów za pomocą portalu usługi Power BI.
3. Zarejestruj dostawcę zasobów **Microsoft.insights** czy już nie zarejestrowane, subskrypcja konta magazynu, a także subskrypcji magazynu usług Recovery Services, aby włączyć raportowanie przepływ do magazynu danych konto. Aby zrobić to samo, należy przejść do witryny Azure portal > subskrypcji > dostawcy zasobów i wyboru dla tego dostawcy go zarejestrować. 

## <a name="configure-storage-account-for-reports"></a>Konfigurowanie konta magazynu dla raportów
Wykonaj następujące kroki, aby skonfigurować konto magazynu dla magazynu usługi recovery services za pomocą witryny Azure portal. Jest to jednorazowa konfiguracja — po skonfigurowaniu konta magazynu można przejść do usługi Power BI bezpośrednio do Wyświetl pakiet zawartości i korzystać z raportów.
1. Jeśli masz już magazyn usługi Recovery Services, Otwórz, przejdź do następnego kroku. Jeśli nie masz Otwórz magazyn usługi Recovery Services, ale znajdują się w witrynie Azure portal, kliknij przycisk **wszystkich usług**.

   * Na liście zasobów wpisz **Usługi odzyskiwania**.
   * Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Po wyświetleniu pozycji **Magazyny Usług odzyskiwania** kliknij ją.

      ![Tworzenie magazynu usługi Recovery Services — krok 1](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

     Zostanie wyświetlona lista magazynów Usług odzyskiwania. Wybierz magazyn z listy magazynów Usług odzyskiwania.

     Zostanie otwarty pulpit nawigacyjny wybranego magazynu.
2. Z listy elementów, która pojawia się w obszarze magazynu, kliknij przycisk **raporty kopii zapasowych** w sekcji monitorowanie i raporty, aby skonfigurować konto magazynu dla raportów.

      ![Wybierz raporty kopii zapasowych menu elementu krok 2](./media/backup-azure-configure-reports/backup-reports-settings.PNG)
3. W bloku kopia zapasowa raportów kliknij **ustawień diagnostycznych** łącza. Spowoduje to otwarcie ustawień diagnostycznych interfejsu użytkownika, który służy do wypychania danych do konta magazynu klienta.

      ![Włącz diagnostykę, krok 3](./media/backup-azure-configure-reports/backup-azure-configure-reports.png)
4. Kliknij łącze **Włącz diagnostykę**. Spowoduje to otwarcie interfejsu użytkownika do konfigurowania konta magazynu. 

      ![Włącz diagnostykę w kroku 4](./media/backup-azure-configure-reports/enable-diagnostics.png)
5. W polu wprowadź nazwę ustawienia **nazwa** i wybierz **Zarchiwizuj na koncie magazynu** pole wyboru, dzięki czemu raportowanie danych można uruchomić przepływu w koncie magazynu.

      ![Włącz diagnostykę krok 5](./media/backup-azure-configure-reports/select-setting-name.png)
6. Kliknij selektor konta magazynu, a następnie wybierz odpowiednie konto subskrypcji i magazynu z listy do przechowywania raportowania danych i kliknij przycisk **OK**.

      ![Wybierz konto magazynu — krok 6](./media/backup-azure-configure-reports/select-subscription-sa.png)
7. Wybierz **AzureBackupReport** Sprawdź pola w sekcji dziennika i przesuń suwak do skorzystania z okresu przechowywania wybierz dla tej, dane raportowania. Raportowanie danych w ramach konta magazynu są przechowywane w okresie wybrane za pomocą tego suwaka.

      ![Zapisz konto magazynu — krok 7](./media/backup-azure-configure-reports/save-diagnostic-settings.png)
8. Przejrzyj wszystkie zmiany, a następnie kliknij przycisk **Zapisz** znajdujący się na górze, jak pokazano na rysunku powyżej. Ta akcja zagwarantuje, że wszystkie zmiany zostaną zapisane i konto magazynu jest teraz skonfigurowane do przechowywania danych raportowania.

9. W tabeli ustawień diagnostycznych powinien teraz pokazywać nowe ustawienie jest włączone dla magazynu. Jeśli go nie widać, Odśwież tabelę, aby sprawdzić zaktualizowane ustawienia.

      ![Wyświetl ustawienia diagnostyczne w kroku 9](./media/backup-azure-configure-reports/diagnostic-setting-row.png)

> [!NOTE]
> Po skonfigurowaniu raportów, zapisując konta magazynu należy **Poczekaj 24 godziny** do wypychania danych początkowych zakończyć. Tylko po tym czasie, należy zaimportować pakiet zawartości usługi Azure Backup w usłudze Power BI. Zapoznaj się [sekcji często zadawane pytania](#frequently-asked-questions) więcej szczegółowych informacji. 
>
>

## <a name="view-reports-in-power-bi"></a>Wyświetlanie raportów w usłudze Power BI 
Po konfigurowania konta magazynu dla raportów przy użyciu magazynu usługi recovery services, trwa około 24 godziny dla raportowania danych można uruchomić przepływu. Po 24 godzinach od skonfigurowania konta magazynu umożliwia wyświetlanie raportów w usłudze Power BI następujące czynności:
1. [Zaloguj się](https://powerbi.microsoft.com/landing/signin/) do usługi Power BI.
2. Kliknij przycisk **Pobierz dane** i kliknij przycisk **uzyskać** w obszarze **usług** w bibliotece pakietów zawartości. Wykonaj kroki opisane w [dokumentacji usługi Power BI na dostęp do pakietu zawartości](https://powerbi.microsoft.com/documentation/powerbi-content-packs-services/).

     ![Importowanie pakietu zawartości](./media/backup-azure-configure-reports/content-pack-import.png)
3. Typ **kopia zapasowa Azure** w pasku wyszukiwania i kliknij **Pobierz teraz**.

      ![Pobieranie pakietu zawartości](./media/backup-azure-configure-reports/content-pack-get.png)
4. Wprowadź nazwę konta magazynu, które są skonfigurowane w kroku 5 powyżej, a następnie kliknij przycisk **dalej** przycisku.

    ![Podaj nazwę konta magazynu](./media/backup-azure-configure-reports/content-pack-storage-account-name.png)    
5. Wprowadź klucz konta magazynu dla tego konta magazynu. Możesz [wyświetlanie i kopiowanie kluczy dostępu do magazynu](../storage/common/storage-account-manage.md#access-keys) , przechodząc do swojego konta magazynu w witrynie Azure portal. 

     ![Wprowadź konto magazynu](./media/backup-azure-configure-reports/content-pack-storage-account-key.png) <br/>
     
6. Kliknij przycisk **Zaloguj** przycisku. Po logowania zakończy się pomyślnie, możesz uzyskać **importowania danych** powiadomień.

    ![Importowanie pakietu zawartości](./media/backup-azure-configure-reports/content-pack-importing-data.png) <br/>
    
    Po pewnym czasie uzyskać **Powodzenie** powiadomienie po zakończeniu importowania. Może zająć nieco dłużej, aby zaimportować pakiet zawartości, w przypadku dużej ilości danych na koncie magazynu.
    
    ![Importowanie pakietu zawartości sukces](./media/backup-azure-configure-reports/content-pack-import-success.png) <br/>
    
7. Po pomyślnym zaimportowaniu danych **kopia zapasowa Azure** pakiet zawartości jest widoczna w **aplikacje** w okienku nawigacji. Na liście wyświetlane są teraz z żółtą gwiazdką wskazującą nowo zaimportowane raporty pulpitu nawigacyjnego usługi Azure Backup, raporty i zestaw danych. 

     ![Pakiet zawartości usługi Azure Backup](./media/backup-azure-configure-reports/content-pack-azure-backup.png) <br/>
     
8. Kliknij przycisk **kopia zapasowa Azure** w ramach pulpitów nawigacyjnych, który wskazuje, zestaw raportów klucza przypiętych.

      ![Pulpit nawigacyjny usługi Azure Backup](./media/backup-azure-configure-reports/azure-backup-dashboard.png) <br/>
9. Aby wyświetlić pełny zestaw raportów, kliknij dowolny raport na pulpicie nawigacyjnym.

      ![Program Azure health zadania kopii zapasowej](./media/backup-azure-configure-reports/azure-backup-job-health.png) <br/>
10. Kliknij każdą kartę w raportach, aby wyświetlić raporty, w tym obszarze.

      ![Usługa Azure karty raporty kopii zapasowych](./media/backup-azure-configure-reports/reports-tab-view.png)


## <a name="frequently-asked-questions"></a>Często zadawane pytania
1. **Jak sprawdzić dane raportowania uruchomił, jeśli przepływają w koncie magazynu**
    
    Można przejść do konta magazynu skonfigurowanego i wybierz kontenery. Jeśli kontener zawiera wpis dla azurebackupreport — dzienniki — szczegółowe informacje, oznacza to, dane raportowania uruchomienia przepływu.

2. **Co to jest częstotliwość wypychanie danych do konta magazynu i pakietu zawartości usługi Azure Backup w usłudze Power BI?**

   Dla użytkowników w dniu 0 zajmuje około 24 godzinach wypychania danych do konta magazynu. Po ukończeniu tej początkowej wypychane dane są odświeżane przy użyciu następujących częstotliwość pokazano na poniższej ilustracji. 
      * Dane powiązane z **zadania, alerty, elementy kopii zapasowej, magazyny, chronionych serwerów i zasady** zostanie przypisany do konta magazynu klienta, jak i kiedy jest rejestrowane.
      * Dane powiązane z **magazynu** zostanie przypisany do konta magazynu klienta co 24 godziny.
   
    ![Częstotliwość wypychania danych w usłudze Azure raporty kopii zapasowych](./media/backup-azure-configure-reports/reports-data-refresh-cycle.png)

  Usługa Power BI ma [zaplanowanego odświeżania raz dziennie](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/#what-can-be-refreshed). Można przeprowadzić ręczne odświeżanie danych w usłudze Power BI dla pakietu zawartości.

3. **Jak długo można zachować raportów** 

   Podczas konfigurowania konta magazynu, można wybrać okres przechowywania danych raportowania na koncie magazynu (przy użyciu kroku 6 na koncie magazynu Konfigurowanie raporty sekcji powyżej). Oprócz, można [raportów analizy w programie excel](https://powerbi.microsoft.com/documentation/powerbi-service-analyze-in-excel/) i zapisać je na dłuższy okres przechowywania, zgodnie z potrzebami. 

4. **Wszystkie moje dane w raportach będą widoczne po skonfigurowaniu konta magazynu?**

   Wszystkie dane, które są generowane po **"Konfigurowanie konta magazynu"** zostanie wypchnięta do konta magazynu i będą dostępne w raportach. Jednak **rozpoczętych zadań nie zostaną wypchnięte** dla raportowania. Gdy zadanie zakończy się lub kończy się niepowodzeniem, jest wysyłana do raportów.

5. **Jeśli już skonfigurowano konto magazynu, aby wyświetlić raporty, można zmienić konfigurację, aby użyć innego konta magazynu?** 

   Tak, można zmienić konfiguracji, aby wskazywał innego konta magazynu. Podczas nawiązywania połączenia z pakietem zawartości usługi Azure Backup, należy używać nowo skonfigurowanego konta magazynu. Ponadto po skonfigurowaniu konta magazynu innego nowych danych będzie przepływać na tym koncie magazynu. Jednak starsze dane (przed zmianą konfiguracji) nadal pozostać w starszych konta magazynu.

6. **Raporty można wyświetlać magazynów i między subskrypcjami?** 

   Tak, można skonfigurować tego samego konta magazynu w różnych magazynów, aby wyświetlić raporty dla wielu magazynu. Ponadto można skonfigurować tego samego konta magazynu dla magazynów w subskrypcjach. Można następnie użyć tego konta magazynu podczas nawiązywania połączenia z pakietem zawartości usługi Azure Backup w usłudze Power BI do wyświetlania raportów. Jednak wybrane konto magazynu, należy w tym samym regionie co magazyn usługi recovery services.
   
## <a name="troubleshooting-errors"></a>Rozwiązywanie problemów z błędami
| Szczegóły błędu | Rozwiązanie |
| --- | --- |
| Po skonfigurowaniu konta magazynu dla raportów Backup **konta magazynu** nadal pokazuje **nieskonfigurowane**. | Jeśli został pomyślnie skonfigurowany konta magazynu danych raportowania będą przepływać w niezależnie od tego problemu. Aby rozwiązać ten problem, przejdź do witryny Azure portal > wszystkie usługi > Ustawienia diagnostyki > RS magazynu > Edytuj ustawienia. Usuń poprzednio skonfigurowane ustawienie i Utwórz nowe ustawienie w tym samym bloku. Tym razem Ustaw pole **nazwa** do **usługi**. To powinien być wyświetlony skonfigurowanego konta magazynu. |
|Po zaimportowaniu kopia zapasowa Azure zawartości pakietu w usłudze Power BI, błąd **kontenera 404 Nie znaleziono** funkcjonuje. | Zgodnie z sugestią podaną w tym dokumencie należy poczekać na 24 godziny po skonfigurowaniu raportów w magazynie usługi Recovery Services na poprawne wyświetlanie w usłudze Power BI. Jeśli zostanie podjęta próba dostępu do raportów przed 24 godzin, zostaną pobrać ten błąd, ponieważ pełnych danych nie jest jeszcze obecna, aby wyświetlić raporty prawidłowe. |

## <a name="next-steps"></a>Kolejne kroki
Teraz, gdy skonfigurowano konto magazynu i importowany pakiet zawartości usługi Azure Backup, następnym krokiem jest dostosowanie te raporty i tworzyć raporty przy użyciu raportowania modelu danych. Aby uzyskać więcej informacji, zobacz następujące artykuły.

* [Za pomocą usługi Azure Backup w modelu danych raportowania](backup-azure-reports-data-model.md)
* [Filtrowanie raportów usługi Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
* [Tworzenie raportów w usłudze Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)

