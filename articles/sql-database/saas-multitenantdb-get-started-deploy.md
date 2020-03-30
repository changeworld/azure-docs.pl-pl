---
title: Wdrażanie podzielonej wielodostępowej aplikacji SaaS bazy danych z wieloma dzierżawami
description: Wdrażanie i eksplorowanie podzielonej aplikacji bazy danych SaaS wielodostępnych Wingtip Tickets, która demonstruje wzorce SaaS przy użyciu usługi Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, stein
ms.date: 10/16/2018
ms.openlocfilehash: 3277318e01362df8fc21ff7ca769aaeb8006abc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73827991"
---
# <a name="deploy-and-explore-a-sharded-multi-tenant-application"></a>Wdrażanie i eksplorowanie podzielonej aplikacji wielodostępowej

W tym samouczku można wdrożyć i eksplorować przykładową aplikację SaaS wielu dzierżawców o nazwie Wingtip Bilety. Aplikacja Wingtip Tickets została zaprojektowana do prezentowania funkcji usługi Azure SQL Database, które upraszczają implementację scenariuszy SaaS.

Ta implementacja aplikacji Wingtip Tickets używa wzorca bazy danych podzielonej na fragmenty. Dzielenie na fragmenty jest według identyfikatora dzierżawy. Dane dzierżawy są dystrybuowane do określonej bazy danych zgodnie z wartościami identyfikatorów dzierżawy. 

Ten wzorzec bazy danych umożliwia przechowywanie jednego lub więcej dzierżaw w każdym niezależnego fragmentu lub bazy danych. Można zoptymalizować pod kątem najniższych kosztów, aby każda baza danych była współużytkowana przez wielu dzierżawców. Lub można zoptymalizować pod kątem izolacji, mając każdy magazyn bazy danych tylko jeden dzierżawy. Wybór optymalizacji można dokonać niezależnie dla każdej konkretnej dzierżawy. Wyboru można dokonać, gdy dzierżawa jest najpierw przechowywane, lub można zmienić zdanie później. Aplikacja jest zaprojektowana do pracy dobrze w obie strony.

## <a name="app-deploys-quickly"></a>Aplikacja szybko wdraża

Aplikacja działa w chmurze platformy Azure i korzysta z usługi Azure SQL Database. Sekcja wdrażania, która następuje zawiera niebieski **przycisk Wdrażanie na platformie Azure.** Po naciśnięciu przycisku aplikacja jest w pełni wdrożona w ramach subskrypcji platformy Azure w ciągu pięciu minut. Masz pełny dostęp do pracy z poszczególnych składników aplikacji.

Aplikacja jest wdrażana z danymi dla trzech przykładowych dzierżawców. Dzierżawcy są przechowywane razem w jednej bazie danych wielu dzierżawców.

Każdy może pobrać kod źródłowy C# i PowerShell dla biletów Wingtip ze [swojego repozytorium GitHub][link-github-wingtip-multitenantdb-55g].

## <a name="learn-in-this-tutorial"></a>Dowiedz się więcej w tym samouczku

> [!div class="checklist"]
> - Jak wdrożyć aplikację SaaS bilety Wingtip.
> - Gdzie można uzyskać kod źródłowy aplikacji i skrypty zarządzania.
> - Informacje o serwerach i bazach danych, które tworzą aplikację.
> - Jak dzierżawcy są mapowane do swoich danych z *katalogu*.
> - Jak aprowizować nową dzierżawę.
> - Jak monitorować aktywność dzierżawy w aplikacji.

Seria powiązanych samouczków jest dostępna, które opierają się na tym początkowym wdrożeniu. Samouczki eksplorują szereg wzorców projektowania i zarządzania SaaS. Podczas pracy z samouczków, zachęcamy do kroku przez skrypty dostarczone, aby zobaczyć, jak różne wzorce SaaS są implementowane.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

- Zostanie zainstalowany najnowszy program Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz [Wprowadzenie do programu Azure PowerShell][link-azure-get-started-powershell-41q].

## <a name="deploy-the-wingtip-tickets-app"></a>Wdrażanie aplikacji Bilety Wingtip

### <a name="plan-the-names"></a>Planowanie nazw

W krokach tej sekcji należy podać wartość *użytkownika,* która jest używana do zapewnienia nazwy zasobów są globalnie unikatowe i nazwę *grupy zasobów,* która zawiera wszystkie zasoby utworzone przez wdrożenie aplikacji. Dla osoby o imieniu *Ann Finley*, proponujemy:
- *Użytkownik:* **af1**  *(Ich inicjały, plus cyfra. Użyj innej wartości (np. af2), jeśli aplikacja zostanie wdrożona po raz drugi).*
- *Grupa zasobów:* **wingtip-mt-af1** *(wingtip-mt wskazuje, że jest to aplikacja wielodostępna podzielona na fragmenty.*

Wybierz swoje imiona i nazwiska już teraz i zapisz je. 

### <a name="steps"></a>Kroki

1. Kliknij następujący niebieski przycisk **Wdrażanie na platformie Azure.**
   - Otwiera witrynę Azure portal z szablonem wdrażania SaaS bilety Wingtip.

     [![Przycisk wdrażania na platformie Azure.][image-deploy-to-azure-blue-48d]][link-aka-ms-deploywtp-mtapp-52k]

1. Wprowadź wymagane wartości parametrów dla wdrożenia.

    > [!IMPORTANT]
    > W przypadku tej demonstracji nie należy używać żadnych istniejących wcześniej grup zasobów, serwerów ani pul. Zamiast tego wybierz pozycję **Utwórz nową grupę zasobów**. Po zakończeniu pracy z aplikacją usuń tę grupę zasobów, aby zatrzymać ich rozliczanie.
    > Nie należy używać tej aplikacji lub żadnych zasobów, które tworzy, do produkcji. Niektóre aspekty uwierzytelniania i ustawienia zapory serwera są celowo niezabezpieczone w aplikacji, aby ułatwić demonstrację.

    - W przypadku **grupy zasobów** — wybierz **pozycję Utwórz nowy**, a następnie podaj **nazwę** grupy zasobów (z uwzględnieniem wielkości liter).
        - Wybierz **lokalizację** z listy rozwijanej.
    - Dla **użytkownika** — zaleca się wybranie krótkiej wartości **użytkownika.**

1. **Wdrażanie aplikacji**.

    - Kliknij, aby zaakceptować Warunki korzystania z serwisu.
    - Kliknij pozycję **Kup**.

1. Monitoruj stan wdrożenia, klikając **pozycję Powiadomienia**, które jest ikoną dzwonka po prawej stronie pola wyszukiwania. Wdrożenie aplikacji Wingtip trwa około pięciu minut.

   ![wdrażanie zakończyło się pomyślnie](media/saas-multitenantdb-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-management-scripts"></a>Pobieranie i odblokowywanie skryptów zarządzania

Podczas wdrażania aplikacji pobierz kod źródłowy aplikacji i skrypty zarządzania.

> [!NOTE]
> Zawartość wykonywalna (skrypty, biblioteki DLL) może być blokowana przez system Windows, gdy pliki zip są pobierane z zewnętrznego źródła i wyodrębniane. Podczas wyodrębniania skryptów z pliku zip należy wykonać następujące czynności, aby odblokować plik zip przed wyodrębnieniem. Odblokowując plik .zip, upewnij się, że skrypty mogą być uruchamiane.

1. Przejdź do [repozytorium GitHub WingtipTicketsSaaS-MultiTenantDb](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb).
2. Kliknij **pozycję Klonuj lub pobierz**.
3. Kliknij **pozycję Pobierz kod ZIP** i zapisz plik.
4. Kliknij prawym przyciskiem myszy plik **WingtipTicketsSaaS-MultiTenantDb-master.zip** i wybierz polecenie **Właściwości**.
5. Na karcie **Ogólne** wybierz pozycję **Odblokuj**i kliknij pozycję **Zastosuj**.
6. Kliknij przycisk **OK**.
7. Wyodrębnij pliki.

Skrypty znajdują się w *.. WingtipTicketsSaaS-MultiTenantDb-master\\Folder modułów szkoleniowych.\\ \\*

## <a name="update-the-configuration-file-for-this-deployment"></a>Aktualizowanie pliku konfiguracyjnego dla tego wdrożenia

Przed uruchomieniem skryptów należy ustawić *grupę zasobów* i wartości *użytkownika* w pliku **UserConfig.psm1**. Ustaw te zmienne na te same wartości, które zostały ustawione podczas wdrażania.

1. Otwórz... \\Moduły\\szkoleniowe*UserConfig.psm1* w *programie PowerShell ISE*.
2. Zaktualizuj nazwę i *nazwę* *resourcegroup* z określonymi wartościami dla wdrożenia (tylko w wierszach 10 i 11).
3. Zapisz zmiany.

Wartości ustawione w tym pliku są używane przez wszystkie skrypty, więc ważne jest, aby były dokładne. W przypadku ponownego rozmieszczania aplikacji należy wybrać różne wartości dla grupy użytkowników i zasobów. Następnie ponownie zaktualizuj plik UserConfig.psm1 o nowe wartości.

## <a name="run-the-application"></a>Uruchamianie aplikacji

W aplikacji Wingtip dzierżawcy są miejscami. Miejscem może być sala koncertowa, klub sportowy lub dowolne inne miejsce, w którym odbywają się imprezy. Miejsca rejestrują się w Wingtip jako klienci, a identyfikator dzierżawy jest generowany dla każdego miejsca. Każde miejsce wymienia swoje nadchodzące wydarzenia w Wingtip, dzięki czemu publiczność może kupić bilety na wydarzenia.

Każde miejsce otrzymuje spersonalizowaną aplikację internetową do wystawiania swoich wydarzeń i sprzedaży biletów. Każda aplikacja sieci web jest niezależna i odizolowana od innych dzierżaw. Wewnętrznie w usłudze Azure SQL Database, każdy dane dla każdej dzierżawy są przechowywane w podzielonej bazy danych wielu dzierżawców, domyślnie. Wszystkie dane są oznaczone identyfikatorem dzierżawy.

Centralna strona internetowa **centrum zdarzeń** zawiera listę łączy do dzierżaw w danym wdrożeniu. Aby korzystać ze strony internetowej **Centrum zdarzeń** i pojedynczej aplikacji sieci Web, należy wykonać następujące czynności:

1. Otwórz **Centrum zdarzeń** w przeglądarce internetowej:
   - http://events.wingtip-mt.&lt;user&gt;.trafficmanager.net &nbsp; *(Zamień&gt; &lt;użytkownika na wartość użytkownika wdrożenia).*

     ![centrum zdarzeń](media/saas-multitenantdb-get-started-deploy/events-hub.png)

2. Kliknij przycisk **Fabrikam Jazz Club** w **Centrum zdarzeń**.

   ![Zdarzenia](./media/saas-multitenantdb-get-started-deploy/fabrikam.png)

### <a name="azure-traffic-manager"></a>Azure Traffic Manager

Aby kontrolować dystrybucję żądań przychodzących, aplikacja Wingtip korzysta z [usługi Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md). Strona zdarzeń dla każdej dzierżawy zawiera nazwę dzierżawy w adresie URL. Każdy adres URL zawiera również określoną wartość użytkownika. Każdy adres URL jest posłuszny pokazanego formatowi, wykonując następujące czynności:

- http://events.wingtip-mt.&lt;użytkownik&gt;.trafficmanager.net/*fabrikamjazzclub*

1. Aplikacja zdarzenia analizuje nazwę dzierżawy z adresu URL. Nazwa dzierżawy jest *fabrikamjazzclub* w poprzednim przykładowym adresie URL.
2. Następnie aplikacja haszysz nazwy dzierżawy, aby utworzyć klucz dostępu do katalogu przy użyciu [zarządzania mapami niezależnego fragmentu.](sql-database-elastic-scale-shard-map-management.md)
3. Aplikacja znajduje klucz w katalogu i uzyskuje odpowiednią lokalizację bazy danych dzierżawy.
4. Aplikacja używa informacji o lokalizacji, aby znaleźć i uzyskać dostęp do jednej bazy danych, która zawiera wszystkie dane dla dzierżawy.

### <a name="events-hub"></a>Centrum zdarzeń

1. **Centrum zdarzeń** wyświetla listę wszystkich dzierżawców, które są zarejestrowane w katalogu i ich miejsc.
2. **Centrum zdarzeń** używa rozszerzonych metadanych w katalogu do pobierania nazwy dzierżawcy skojarzonej z każdym mapowaniem w celu skonstruowania adresów URL.

W środowisku produkcyjnym zazwyczaj tworzysz rekord DNS CNAME, aby [skierować firmową domenę internetową](../traffic-manager/traffic-manager-point-internet-domain.md) do profilu menedżera ruchu.

## <a name="start-generating-load-on-the-tenant-databases"></a>Rozpoczęcie generowanie obciążenia baz danych dzierżawy

Teraz, gdy aplikacja jest wdrożona, let's put it to work! *Skrypt Demo-LoadGenerator* PowerShell uruchamia obciążenie uruchomione dla każdej dzierżawy. Rzeczywiste obciążenie wielu aplikacji SaaS jest zazwyczaj sporadyczne i nieprzewidywalne. Aby symulować tego typu obciążenia, generator generuje obciążenie rozłożone na wszystkich dzierżawców. Obciążenie obejmuje losowe wybuchy na każdej dzierżawie występujące w losowych odstępach czasu. To trwa kilka minut dla wzorca obciążenia, aby pojawić się, więc najlepiej pozwolić generatora uruchomić przez co najmniej trzy lub cztery minuty przed monitorowaniem obciążenia.

1. W *programie PowerShell ISE*otwórz ... \\Moduły\\szkoleniowe\\Utilities*Demo-LoadGenerator.ps1* skrypt.
2. Naciśnij klawisz **F5**, aby uruchomić skrypt i generator obciążenia (na razie pozostaw bez zmian wartości domyślne parametrów).

Skrypt *Demo-LoadGenerator.ps1* otwiera kolejną sesję programu PowerShell, w której działa generator obciążenia. Generator obciążenia jest uruchamiany w tej sesji jako zadanie pierwszego planu, które wywołuje zadania generowania obciążenia w tle, po jednym dla każdej dzierżawy.

Po uruchomieniu zadania pierwszego planu pozostaje w stanie wywoływania zadania. Zadanie uruchamia dodatkowe zadania w tle dla wszystkich nowych dzierżaw, które są następnie aprowizacji.

Zamknięcie sesji programu PowerShell zatrzymuje wszystkie zadania.

Można ponownie uruchomić sesję generatora obciążenia, aby użyć różnych wartości parametrów. Jeśli tak, zamknij sesję generowania programu PowerShell, a następnie uruchom ponownie *demo-LoadGenerator.ps1*.

## <a name="provision-a-new-tenant-into-the-sharded-database"></a>Aprowizowanie nowej dzierżawy do podzielonej bazy danych

Początkowe wdrożenie obejmuje trzy przykładowe dzierżawy w bazie danych *Tenants1.* Utwórzmy inną dzierżawę i obserwujmy jej wpływ na wdrożoną aplikację. W tym kroku naciśnij jeden klawisz, aby utworzyć nową dzierżawę:

1. Otwórz... \\Udostępnianie modułów\\szkoleniowych i katalog\\*demo-provisionTenants.ps1* w programie *PowerShell ISE*.
2. Naciśnij **klawisz F5** (nie **F8),** aby uruchomić skrypt (na razie pozostaw wartości domyślne).

   > [!NOTE]
   > Skrypty programu PowerShell należy uruchamiać tylko przez naciśnięcie klawisza **F5,** a nie przez naciśnięcie **klawisza F8,** aby uruchomić wybraną część skryptu. Problem z **F8** polega na tym, że zmienna *$PSScriptRoot* nie jest oceniana. Ta zmienna jest potrzebna przez wiele skryptów do nawigacji po folderach, wywoływania innych skryptów lub importowania modułów.

Nowy dzierżawca Red Maple Racing jest dodawany do bazy danych *Tenants1* i zarejestrowany w katalogu. Nowa **witryna** sprzedaży biletów w przeglądarce zostanie otwarta:

![Nowa dzierżawa](./media/saas-multitenantdb-get-started-deploy/red-maple-racing.png)

Odśwież **Centrum zdarzeń**, a nowa dzierżawa pojawi się teraz na liście.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Aprowizowanie nowej dzierżawy we własnej bazie danych

Podzielony na fragmenty model wielodostępny pozwala wybrać, czy aprowizować nową dzierżawę do bazy danych, która zawiera innych dzierżawców lub do własnej bazy danych. Dzierżawca odizolowany we własnej bazie danych korzysta z następujących korzyści:

- Wydajność bazy danych dzierżawy można zarządzać bez konieczności kompromisu z potrzebami innych dzierżawców.
- W razie potrzeby bazy danych można przywrócić do wcześniejszego punktu w czasie, ponieważ nie ma to wpływu na innych dzierżawców.

Można umieścić klientów korzystających z bezpłatnej wersji próbnej lub klientów administracji ekonomicznej w bazach danych z wieloma dzierżawami. Można umieścić każdej dzierżawy premium do własnej dedykowanej bazy danych. Jeśli tworzysz wiele baz danych, które zawierają tylko jedną dzierżawę, można zarządzać nimi wszystkimi łącznie w puli elastycznej, aby zoptymalizować koszty zasobów.

Następnie aprowizujemy innego dzierżawcę, tym razem we własnej bazie danych:

1. Cala... \\Nauczanie\\modułów\\provision i katalog*Demo-ProvisionTenants.ps1*, zmodyfikować *$TenantName* **salix salsa**, *$VenueType* **do tańca** i *$Scenario* do **2**.

2. Naciśnij **klawisz F5,** aby ponownie uruchomić skrypt.
    - To **F5** naciśnij przepisów nowego dzierżawcy w oddzielnej bazie danych. Baza danych i dzierżawca są zarejestrowane w katalogu. Następnie przeglądarka otwiera się na stronie Zdarzenia dzierżawy.

   ![Salix Salsa strona wydarzenia](./media/saas-multitenantdb-get-started-deploy/salix-salsa.png)

   - Przewiń do dołu strony. Tam na banerze zostanie wyświetlena nazwa bazy danych, w której są przechowywane dane dzierżawy.

3. Odśwież **Centrum zdarzeń,** a dwa nowe dzierżawy są teraz wyświetlane na liście.

## <a name="explore-the-servers-and-tenant-databases"></a>Eksplorowanie serwerów i baz danych dzierżawy

Teraz przyjrzymy się niektórym zasobom, które zostały wdrożone:

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do listy grup zasobów. Otwórz grupę zasobów utworzoną podczas wdrażania aplikacji.

   ![grupa zasobów](./media/saas-multitenantdb-get-started-deploy/resource-group.png)

2. Kliknij **serwer&lt;użytkownika&gt; catalog-mt.** Serwer katalogu zawiera dwie bazy danych o nazwie *tenantcatalog* i *basetenantdb*. Baza danych *basetenantdb* jest pustą bazą danych szablonu. Jest kopiowany do tworzenia nowej bazy danych dzierżawy, czy używane dla wielu dzierżawców lub tylko jeden dzierżawy.

   ![serwer wykazu](./media/saas-multitenantdb-get-started-deploy/catalog-server.png)

3. Wróć do grupy zasobów i wybierz serwer *tenants1-mt,* który przechowuje bazy danych dzierżawy.
    - Baza danych tenants1 jest wielodostępną bazą danych, w której są przechowywane oryginalne trzy dzierżawy oraz pierwsza dodana dzierżawa. Jest skonfigurowany jako baza danych 50 DTU Standard.
    - Baza danych **salixsalsa** posiada salix Salsa miejsce tańca jako jedyny najemca. Jest skonfigurowany jako baza danych wersji standardowej z 50 DTU domyślnie.

   ![serwer dzierżawy](./media/saas-multitenantdb-get-started-deploy/tenants-server.png)

## <a name="monitor-the-performance-of-the-database"></a>Monitorowanie wydajności bazy danych

Jeśli generator obciążenia jest uruchomiony przez kilka minut, dostępna jest wystarczająca ilość danych telemetrycznych, aby przyjrzeć się możliwościom monitorowania bazy danych wbudowanym w witrynę Azure portal.

1. Przejdź do serwera **&lt;&gt; użytkowników tenants1-mt** i kliknij **tenants1,** aby wyświetlić wykorzystanie zasobów dla bazy danych, która ma czterech dzierżawców. Każdy najemca podlega sporadyczne duże obciążenie z generatora obciążenia:

   ![monitorowanie dzierżawców1](./media/saas-multitenantdb-get-started-deploy/monitor-tenants1.png)

   Wykres wykorzystania jednostek DTU ładnie ilustruje, jak wielodostępna baza danych może obsługiwać nieprzewidywalne obciążenie wielu dzierżawców. W takim przypadku generator obciążenia stosuje sporadyczne obciążenie około 30 DTU do każdej dzierżawy. To obciążenie odpowiada 60% wykorzystania bazy danych 50 DTU. Szczyty, które przekraczają 60% są wynikiem obciążenia stosowane do więcej niż jednej dzierżawy w tym samym czasie.

2. Przejdź do serwera **użytkownika&lt;&gt; tenants1-mt** i kliknij bazę danych **salixsalsa.** Można zobaczyć wykorzystanie zasobów w tej bazie danych, która zawiera tylko jedną dzierżawę.

   ![baza danych salixsalsa](./media/saas-multitenantdb-get-started-deploy/monitor-salix.png)

Generator obciążenia stosuje podobne obciążenie do każdej dzierżawy, niezależnie od tego, w której bazie danych znajduje się każda dzierżawa. Z tylko jeden dzierżawy w **salixsalsa** bazy danych, widać, że baza danych może wytrzymać znacznie większe obciążenie niż bazy danych z kilku dzierżawców. 

### <a name="resource-allocations-vary-by-workload"></a>Alokacje zasobów różnią się w zależności od obciążenia

Czasami wielodostępna baza danych wymaga więcej zasobów dla dobrej wydajności niż baza danych z jedną dzierżawą, ale nie zawsze. Optymalna alokacja zasobów zależy od charakterystyki określonego obciążenia dla dzierżawców w systemie.

Obciążenia generowane przez skrypt generatora obciążenia są tylko do celów ilustracyjnych.

## <a name="additional-resources"></a>Zasoby dodatkowe

- Aby dowiedzieć się więcej o wielodostępnych aplikacjach SaaS, zobacz [Wzorce projektowania dla aplikacji SaaS z wieloma dzierżawami.](saas-tenancy-app-design-patterns.md)

- Aby dowiedzieć się więcej o basenach elastycznych, zobacz:

  - [Elastyczne pule pozwalają na zarządzanie wieloma bazami danych Azure SQL oraz ich skalowanie](sql-database-elastic-pool.md)
  - [Scaling out with Azure SQL Database (Skalowanie w poziomie za pomocą usługi Azure SQL Database)](sql-database-elastic-scale-introduction.md)

## <a name="next-steps"></a>Następne kroki

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Jak wdrożyć aplikację bazy danych wielodostępnych Wingtip Tickets SaaS.
> - Informacje o serwerach i bazach danych, które tworzą aplikację.
> - Dzierżawcy są mapowane do swoich danych z *katalogu*.
> - Jak aprowizować nowych dzierżawców do bazy danych z wieloma dzierżawami i bazy danych z jedną dzierżawą.
> - Jak wyświetlić wykorzystanie puli do monitorowania aktywności dzierżawy.
> - Jak usunąć przykładowe zasoby, aby zatrzymać powiązane rozliczenia.

Teraz spróbuj [samouczek aprowizować i katalogować](sql-database-saas-tutorial-provision-and-catalog.md).


<!--  Link references.

A [series of related tutorials] is available that build upon this initial deployment.
[link-wtp-overivew-jumpto-saas-tutorials-97j]: saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials

-->

[link-aka-ms-deploywtp-mtapp-52k]: https://aka.ms/deploywtp-mtapp


[link-azure-get-started-powershell-41q]: https://docs.microsoft.com/powershell/azure/get-started-azureps

[link-github-wingtip-multitenantdb-55g]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/



<!--  Image references.

[image-deploy-to-azure-blue-48d]: https://aka.ms/deploywtp-mtapp "Button for Deploy to Azure."
-->

[image-deploy-to-azure-blue-48d]: media/saas-multitenantdb-get-started-deploy/deploy.png "Przycisk do wdrażania na platformie Azure."

