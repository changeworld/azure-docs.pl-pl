---
title: Konfigurowanie raportów dla kopii zapasowej systemu Azure
description: Ten artykuł zawiera informacje o konfigurowaniu raportów usługi Power BI dla usługi Kopia zapasowa Azure przy użyciu magazynu usług odzyskiwania.
services: backup
author: JPallavi
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: pajosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b645d12255ab2bd6c3747eb966b2a6ecc3e284b3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605233"
---
# <a name="configure-azure-backup-reports"></a>Konfigurowanie raportów usługi Azure Backup
Ten artykuł zawiera informacje o procedurę konfigurowania raportów dla usługi Kopia zapasowa Azure przy użyciu magazynu usług odzyskiwania oraz dostęp do tych raportów za pomocą usługi Power BI. Po wykonaniu tych kroków, można przejść bezpośrednio do usługi Power BI do wyświetlania wszystkich raportów, dostosowywania i tworzenia raportów. 

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
1. Raporty kopia zapasowa Azure są obsługiwane dla kopii zapasowej maszyny wirtualnej platformy Azure i plik lub folder do chmury przy użyciu agenta usług odzyskiwania Azure.
2. Raporty dotyczące Azure SQL, program DPM i serwer kopii zapasowej Azure nie są obsługiwane w tej chwili.
3. Raporty można wyświetlić różnych magazynów i różnych subskrypcji, skonfigurowanie tego samego konta magazynu dla każdej z magazynów. Wybrane konto magazynu należy w tym samym regionie co magazyn usług odzyskiwania.
4. Częstotliwość odświeżania Zaplanowane raporty wynosi 24 godziny w usłudze Power BI. Można również przeprowadzić odświeżanie ad hoc raportów w usłudze Power BI, w których wielkość najnowsze dane na koncie magazynu klienta jest używany do renderowania raportów. 
5. Raporty kopia zapasowa Azure obecnie nie są obsługiwane w National chmury.

## <a name="prerequisites"></a>Wymagania wstępne
1. Utwórz [konto magazynu Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account) ją skonfigurować do raportów. To konto magazynu jest używany do przechowywania danych powiązanych raportów.
2. [Utwórz konto usługi Power BI](https://powerbi.microsoft.com/landing/signin/) wyświetlić, dostosowywanie i tworzenie własnych raportów za pomocą portalu usługi Power BI.
3. Rejestrowanie dostawcy zasobów **elemencie Microsoft.insights** Jeśli nie zarejestrowano już, z subskrypcją konta magazynu, a także z subskrypcją magazyn usług odzyskiwania, aby włączyć raportowanie przepływ danych do magazynu konto. Aby to zrobić, należy przejść do portalu Azure > subskrypcji > dostawców zasobów i wyboru dla tego dostawcy go zarejestrować. 

## <a name="configure-storage-account-for-reports"></a>Konfigurowanie konta magazynu dla raportów
Następujące kroki umożliwiają konfigurowanie konta magazynu dla magazynu usług odzyskiwania przy użyciu portalu Azure. Jest to jednorazowej konfiguracji i po skonfigurowaniu konta magazynu, można przejść do usługi Power BI bezpośrednio do wyświetlania zawartości pakietu i korzystać z raportów.
1. Jeśli masz już magazyn usług odzyskiwania, Otwórz, przejdź do następnego kroku. Jeśli nie masz otwarte magazyn usług odzyskiwania, ale znajdują się w portalu Azure, kliknij przycisk **wszystkie usługi**.

   * Na liście zasobów wpisz **Usługi odzyskiwania**.
   * Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Po wyświetleniu pozycji **Magazyny Usług odzyskiwania** kliknij ją.

      ![Tworzenie magazynu usługi Recovery Services — krok 1](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

     Zostanie wyświetlona lista magazynów Usług odzyskiwania. Wybierz magazyn z listy magazynów Usług odzyskiwania.

     Zostanie otwarty pulpit nawigacyjny wybranego magazynu.
2. Z listy elementów, który pojawia się w magazynie, kliknij przycisk **raporty kopii zapasowej** w sekcji monitorowanie i raporty, aby skonfigurować konta magazynu dla raportów.

      ![Wybierz raporty kopii zapasowej menu elementu krok 2](./media/backup-azure-configure-reports/backup-reports-settings.PNG)
3. W bloku kopia zapasowa raporty, kliknij **ustawień diagnostycznych** łącza. Spowoduje to otwarcie diagnostyki ustawienia interfejsu użytkownika, który jest używany do wypychania danych do konta magazynu klienta.

      ![Włącz diagnostykę w kroku 3](./media/backup-azure-configure-reports/backup-azure-configure-reports.png)
4. Kliknij łącze **Włącz diagnostykę**. Spowoduje to otwarcie interfejsu użytkownika do konfigurowania konta magazynu. 

      ![Włącz diagnostykę w kroku 4](./media/backup-azure-configure-reports/enable-diagnostics.png)
5. W polu wprowadź nazwę ustawienia **nazwa** i wybierz **archiwum na konto magazynu** pole wyboru, aby raportowania danych można uruchomić przepływu w koncie magazynu.

      ![Włącz diagnostykę krok 5](./media/backup-azure-configure-reports/select-setting-name.png)
6. Kliknij przycisk Wybór konta magazynu i wybierz odpowiednie konto subskrypcji i magazynu na liście do przechowywania raportowania danych i kliknij przycisk **OK**.

      ![Wybierz konto magazynu — krok 6](./media/backup-azure-configure-reports/select-subscription-sa.png)
7. Wybierz **AzureBackupReport** Sprawdź pola w sekcji dziennika i przesuń suwak na okres przechowywania wybierz dla tej danych raportowania. Raportowanie danych na koncie magazynu jest przechowywana w okresie wybranych za pomocą tego suwaka.

      ![Zapisywanie konta magazynu — krok 7](./media/backup-azure-configure-reports/save-diagnostic-settings.png)
8. Przejrzyj wszystkie zmiany, a następnie kliknij przycisk **zapisać** znajdującego się na górze, jak pokazano na rysunku powyżej. Ta akcja gwarantuje, że wszystkie zmiany są zapisywane i konto magazynu jest teraz skonfigurowane do przechowywania danych raportowania.

9. W tabeli ustawień diagnostycznych powinny być teraz wyświetlane nowe ustawienie jest włączone dla magazynu. Jeśli go nie widać, Odśwież tabelę, aby zobaczyć zaktualizowane ustawienia.

      ![Ustawienie diagnostyczne widoku kroku 9](./media/backup-azure-configure-reports/diagnostic-setting-row.png)

> [!NOTE]
> Po skonfigurowaniu raportów przez zapisanie konta magazynu należy **Poczekaj 24 godziny** do wypychania początkowej danych do ukończenia. Pakiet zawartości usługi Kopia zapasowa Azure w usłudze Power BI należy importować tylko po tym czasie. Zobacz [sekcji często zadawanych PYTAŃ](#frequently-asked-questions) uzyskać więcej szczegółowych informacji. 
>
>

## <a name="view-reports-in-power-bi"></a>Wyświetlanie raportów w usłudze Power BI 
Po Konfigurowanie konta magazynu dla raportów, za pomocą magazynu usług odzyskiwania, trwa około 24 godziny dla raportowania danych można uruchomić przepływu. Po 24 godzinach konfigurowania konta magazynu wykonaj następujące kroki, aby wyświetlić raporty w usłudze Power BI:
1. [Zaloguj się](https://powerbi.microsoft.com/landing/signin/) do usługi Power BI.
2. Kliknij przycisk **Pobierz dane** i kliknij przycisk **uzyskać** w obszarze **usług** w bibliotece zawartości pakietu. Wykonaj kroki wymienione w [dokumentacji usługi Power BI na dostęp do zawartości pakietu](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-packs-services/).

     ![Importowanie pakietu zawartości](./media/backup-azure-configure-reports/content-pack-import.png)
3. Typ **kopia zapasowa Azure** na pasku wyszukiwania i kliknij przycisk **Pobierz teraz**.

      ![Pobierz pakiet zawartości](./media/backup-azure-configure-reports/content-pack-get.png)
4. Wprowadź nazwę konta magazynu, które są skonfigurowane w kroku 5 powyżej, a następnie kliknij przycisk **dalej** przycisku.

    ![Podaj nazwę konta magazynu](./media/backup-azure-configure-reports/content-pack-storage-account-name.png)    
5. Wprowadź klucz konta magazynu dla tego konta magazynu. Możesz [wyświetlanie i kopiowanie kluczy dostępu do magazynu](../storage/common/storage-create-storage-account.md#manage-your-storage-account) przechodząc do konta magazynu w portalu Azure. 

     ![Wprowadź konto magazynu](./media/backup-azure-configure-reports/content-pack-storage-account-key.png) <br/>
     
6. Kliknij przycisk **Zaloguj** przycisku. Zaloguj się ponownie, możesz otrzymywać **importowania danych** powiadomień.

    ![Importowanie pakietu zawartości](./media/backup-azure-configure-reports/content-pack-importing-data.png) <br/>
    
    Po pewnym czasie **Powodzenie** powiadomienie po zakończeniu importowania. Może zająć nieco dłużej, aby zaimportować pakiet zawartości, w przypadku dużych ilości danych na koncie magazynu.
    
    ![Importowanie pakietu zawartości Powodzenie](./media/backup-azure-configure-reports/content-pack-import-success.png) <br/>
    
7. Gdy dane są importowane pomyślnie, **kopia zapasowa Azure** pakiet zawartości jest widoczny w **aplikacji** w okienku nawigacji. Lista zawiera teraz pulpitu nawigacyjnego usługi Kopia zapasowa Azure, raporty i zestaw danych z żółtą gwiazdką wskazującą nowo zaimportowany raportów. 

     ![Pakiet zawartości z kopii zapasowej platformy Azure](./media/backup-azure-configure-reports/content-pack-azure-backup.png) <br/>
     
8. Kliknij przycisk **kopia zapasowa Azure** w obszarze pulpity nawigacyjne, który wskazuje zestaw przypiętych raportów klucza.

      ![Pulpitu nawigacyjnego kopia zapasowa Azure](./media/backup-azure-configure-reports/azure-backup-dashboard.png) <br/>
9. Aby wyświetlić pełny zestaw raportów, kliknij przycisk żadnych raportów na pulpicie nawigacyjnym.

      ![Azure kondycji zadania tworzenia kopii zapasowej](./media/backup-azure-configure-reports/azure-backup-job-health.png) <br/>
10. Kliknij każdą kartę w raportach, aby wyświetlić raporty w tym obszarze.

      ![Karty raporty kopia zapasowa Azure](./media/backup-azure-configure-reports/reports-tab-view.png)


## <a name="frequently-asked-questions"></a>Często zadawane pytania
1. **Jak sprawdzić, czy dane raportowania rozpoczęło się przepływu w koncie magazynu**
    
    Można przejdź do skonfigurowano konto magazynu i wybierz kontenery. Kontener zawiera wpis odpowiadający insights — dzienniki azurebackupreport, wskazuje, że dane raportowania rozpoczęła przepływu.

2. **Co to jest częstotliwość wypychanie danych do konta magazynu i pakiet zawartości usługi Kopia zapasowa Azure w usłudze Power BI?**

   Dla użytkowników od dnia 0 wymagałoby około 24 godzinach przekazywaniu danych do konta magazynu. Po wykonaniu tej początkowej wypychane dane są odświeżane przy częstotliwości następujące pokazano na poniższej ilustracji. 
      * Dane powiązane z **zadań, alerty, elementy kopii zapasowej, magazynów, chronionych serwerów i zasady** zostanie przypisany do konta magazynu klienta, jak i przy rejestrowaniu.
      * Dane powiązane z **magazynu** zostanie przypisany do konta magazynu klienta co 24 godziny.
   
    ![Częstotliwość wypychania danych raportów kopia zapasowa Azure](./media/backup-azure-configure-reports/reports-data-refresh-cycle.png)

  Usługa Power BI ma [zaplanowanego odświeżania raz dziennie](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/#what-can-be-refreshed). Ręczne odświeżanie danych można wykonać w usłudze Power BI dla pakietu zawartości.

3. **Jak długo można zachować raporty?** 

   Podczas konfigurowania konta magazynu, można wybrać okres przechowywania danych raportowania na koncie magazynu (przy użyciu kroku 6 na koncie magazynu konfiguracji dla sekcji raportów powyżej). Oprócz tego możesz [raportów analizy w programie excel](https://powerbi.microsoft.com/documentation/powerbi-service-analyze-in-excel/) i zapisać je na dłuższy okres przechowywania, zgodnie z potrzebami. 

4. **Po skonfigurowaniu konta magazynu będzie widoczne wszystkie dane w raportach?**

   Wszystkie dane, które są generowane po **"Konfigurowanie konta magazynu"** zostanie przekazany do konta magazynu i będą dostępne w raportach. Jednak **rozpoczętych zadań nie są usuwane,** do raportowania. Gdy zadanie ukończenia lub niepowodzenia, są wysyłane do raportów.

5. **Jeśli już są skonfigurowano konta magazynu, aby wyświetlić raporty, można zmienić konfigurację, aby użyć innego konta magazynu?** 

   Tak, można zmienić konfigurację, aby wskazywał innego konta magazynu. Konto magazynu nowo skonfigurowanego należy używać podczas łączenia się pakiet zawartości usługi Kopia zapasowa Azure. Ponadto po skonfigurowaniu innego konta magazynu będzie przepływu nowe dane tego konta magazynu. Ale nadal pozostanie starszych danych (przed zmianą konfiguracji) w starszych konta magazynu.

6. **Raporty można wyświetlić różnych magazynów i różnych subskrypcji?** 

   Tak, można skonfigurować tego samego konta magazynu w różnych magazynów, aby wyświetlić raporty między magazynem. Ponadto można skonfigurować tego samego konta magazynu dla magazynów w subskrypcjach. Można następnie użyć tego konta magazynu podczas nawiązywania połączenia pakiet zawartości usługi Kopia zapasowa Azure w usłudze Power BI do wyświetlania raportów. Wybrane konto magazynu należy jednak w tym samym regionie co magazyn usług odzyskiwania.
   
## <a name="troubleshooting-errors"></a>Rozwiązywanie problemów z błędami
| Szczegóły błędu | Rozwiązanie |
| --- | --- |
| Po skonfigurowaniu konta magazynu dla raportów kopii zapasowej **konta magazynu** pozostanie **nieskonfigurowane**. | Jeśli został pomyślnie skonfigurowany konta magazynu danych raportowania będą przepływać w pomimo tego problemu. Aby rozwiązać ten problem, przejdź do portalu Azure > wszystkie usługi > Ustawienia diagnostyki > magazynu RS > Edytuj ustawienia. Usuń wcześniej skonfigurowane ustawienie i Utwórz nowe ustawienie w tym samym bloku. Teraz ustaw dla pola **nazwa** do **usługi**. To konto magazynu skonfigurowanych powinny być wyświetlane. |
|Po zaimportowaniu kopia zapasowa Azure zawartości pakietu w usłudze Power BI, błąd **nie znaleziono kontenera 404** pojawia się. | Zgodnie z sugestią podaną w tym dokumencie należy poczekać na 24 godziny po skonfigurowaniu raportów w magazynie usług odzyskiwania, aby poprawnie wyświetlać je w usłudze Power BI. Jeśli spróbujesz uzyskać dostępu do raportów przed 24 godziny, zostanie umieszczony błąd, ponieważ pełnych danych nie ma jeszcze do wyświetlania raportów prawidłowe. |

## <a name="next-steps"></a>Kolejne kroki
Teraz, gdy skonfigurowano konto magazynu i zaimportowany pakiet zawartości usługi Kopia zapasowa Azure, następnym krokiem jest dostosować te raporty i używaj modelu danych raportowania do tworzenia raportów. Więcej szczegółów można znaleźć w następujących artykułach.

* [Przy użyciu modelu danych raportowania usługi Kopia zapasowa Azure](backup-azure-reports-data-model.md)
* [Filtrowanie raportów w usłudze Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
* [Tworzenie raportów w usłudze Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)

