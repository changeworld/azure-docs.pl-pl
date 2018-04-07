---
title: Wdrażanie aplikacji SaaS podzielonej wielodostępne bazy danych, który używa usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Wdrażanie i Eksploruj podzielonej Wingtip biletów wielodostępne bazy danych aplikacji SaaS, prezentującą wzorce SaaS przy użyciu bazy danych SQL Azure.
keywords: samouczek usługi sql database
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.workload: data-management
ms.topic: article
ms.date: 04/01/2018
ms.author: genemi
ms.openlocfilehash: 4cbf758b82bccae8efe77e197d23a090d71fd7e5
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="deploy-and-explore-a-sharded-multi-tenant-application-that-uses-azure-sql-database"></a>Wdrażanie i Eksploruj podzielonej aplikacji wielodostępnych, która używa bazy danych SQL Azure

W tym samouczku wdrażanie i Eksploruj przykładowej aplikacji SaaS wielu dzierżawców o nazwie Wingtip biletów. Aplikacja biletów Wingtip zaprojektowano w celu pokazują funkcje bazy danych SQL Azure, które upraszczająca implementację scenariusze SaaS.

Ta implementacja aplikacji biletów Wingtip korzysta ze wzorca podzielonej wielodostępne bazy danych. Dzielenia na fragmenty jest identyfikator dzierżawcy. Dane dzierżawy jest wysyłana do określonej bazy danych zgodnie z wartościami dla identyfikatora dzierżawcy. 

Ten wzorzec bazy danych pozwala przechowywać co najmniej jednego dzierżawcy w każdym niezależnego fragmentu lub bazy danych. Można zoptymalizować dla najniższy koszt o każdej być współużytkowane przez wiele dzierżaw w bazie danych. Lub aby zoptymalizować izolacji przez każdą bazę danych przechowywane tylko jednego dzierżawcy. Wybór optymalizacji mogą być dokonywane niezależnie dla każdego określonego dzierżawcy. Wybór można wprowadzić, gdy dzierżawa najpierw jest przechowywany lub później zmieni zdanie. Aplikacja jest przeznaczona do pracy oraz w obu przypadkach.

#### <a name="app-deploys-quickly"></a>Aplikacja szybko wdraża

Aplikacja działa w chmurze platformy Azure i korzysta z bazy danych SQL Azure. Poniższa sekcja wdrożenia zawiera niebieski **wdrażanie na platformie Azure** przycisku. Po naciśnięciu przycisku pełni wdrożeniu aplikacji do subskrypcji platformy Azure w ciągu pięciu minut. Masz pełny dostęp do pracy z poszczególnych składników aplikacji.

Aplikacja jest wdrażana z danymi dla trzech dzierżawców próbki. Dzierżawcy są przechowywane razem w jednym wielodostępnej bazie danych.

Każda osoba, która można pobrać kodu źródłowego C# i programu PowerShell dla biletów Wingtip z [repozytorium GitHub][link-github-wingtip-multitenantdb-55g].

#### <a name="learn-in-this-tutorial"></a>Dowiedz się, w tym samouczku

> [!div class="checklist"]
> - Jak wdrożyć aplikację Wingtip biletów SaaS.
> - Gdzie można pobrać kodu źródłowego aplikacji i skrypty zarządzania.
> - Temat serwerów i baz danych, które tworzą aplikacji.
> - Jak dzierżawcy są mapowane do swoich danych z *katalogu*.
> - Sposób obsługi administracyjnej nowej dzierżawy.
> - Jak monitorować działania dzierżawy w aplikacji.

Szereg samouczków powiązane jest dostępna które zależą od tego pierwszego wdrożenia. Samouczków Poznaj wzorce projektowania i zarządzania zakresem SaaS. Podczas pracy za pośrednictwem samouczków, zachęca się do kroku za pomocą podanego skryptów, aby zobaczyć, jak są implementowane różnych wzorców SaaS.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

- Zainstalowano najnowsze programu Azure PowerShell. Aby uzyskać więcej informacji, zobacz [wprowadzenie do programu Azure PowerShell][link-azure-get-started-powershell-41q].

## <a name="deploy-the-wingtip-tickets-app"></a>Wdrażanie Wingtip biletów aplikacji

#### <a name="plan-the-names"></a>Planowanie nazwy

W procedurze w tej sekcji, podaj *użytkownika* wartość, która służy do zapewnienia są globalnie unikatowe nazwy zasobów i nazwę *grupy zasobów* zawierającą wszystkie zasoby utworzone przez wdrożenia aplikacji. Osoby o nazwie *Finley pods*, zalecamy:
- *Użytkownik:* **af1***(jej inicjały oraz cyfry. Użyj innej wartości (np. af2) w przypadku wdrożenia aplikacji po raz drugi.)*
- *Grupa zasobów:* **wingtip-mt-af1** *(wingtip mt wskazuje jest podzielony na niezależne fragmenty aplikacja wielodostępnej. Dołączanie af1 nazwa użytkownika są powiązane z nazwy grupy zasobów z nazw zasobów, które zawiera.)*

Teraz wybierz nazwy, a następnie zapisz je. 

#### <a name="steps"></a>Kroki

1. Polecenie następujące **wdrażanie na platformie Azure** przycisku.
    - Spowoduje to otwarcie portalu Azure przy użyciu szablonu wdrożenia Wingtip biletów SaaS.

    [![Przycisk Wdrażaj na platformie Azure.][image-deploy-to-azure-blue-48d]][link-aka-ms-deploywtp-mtapp-52k]

1. Wprowadź wartości wymaganego parametru dla wdrożenia.

    > [!IMPORTANT]
    > Dla tej prezentacji nie należy używać żadnych wcześniej istniejących grup zasobów, serwerami lub pul. Zamiast tego należy wybrać **Utwórz nową grupę zasobów**. Po zakończeniu pracy z aplikacją usuń tę grupę zasobów, aby zatrzymać ich rozliczanie.
    > Nie należy używać tej aplikacji lub wszystkie zasoby, które tworzy, w środowisku produkcyjnym. Niektóre aspekty uwierzytelniania i ustawienia zapory serwera, są celowo niezabezpieczone w aplikacji w celu ułatwienia pokazu.

    - Dla **grupy zasobów** — wybierz tę opcję **Utwórz nowy**, a następnie podaj **nazwa** dla grupy zasobów (z uwzględnieniem wielkości liter).
        - Wybierz **lokalizacji** z listy rozwijanej.
    - Dla **użytkownika** — zaleca się, że wybierasz krótki **użytkownika** wartość.

1. **Wdróż aplikację**.

    - Kliknij, aby zaakceptować warunki i postanowienia.
    - Kliknij pozycję **Kup**.

1. Monitorowanie stanu wdrożenia, klikając **powiadomienia**, która jest ikonę dzwonka z prawej strony pola wyszukiwania. Wdrażanie aplikacji Wingtip trwa około pięciu minut.

   ![wdrażanie zakończyło się pomyślnie](media/saas-multitenantdb-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-management-scripts"></a>Pobierz i odblokować skrypty zarządzania

Gdy aplikacja jest wdrażana, Pobierz skrypty zarządzania i kodu źródłowego aplikacji.

> [!NOTE]
> Zawartość pliku wykonywalnego (skrypty, biblioteki dll) mogą być blokowane przez system Windows, gdy pliki zip są pobierane z zewnętrznego źródła i wyodrębnione. Podczas wyodrębniania skryptów z pliku zip, wykonaj następujące kroki, aby odblokować przed wyodrębniania pliku zip. Przez odblokowywania pliku zip, należy zwrócić uwagę, mogą uruchamiać skrypty.

1. Przejdź do [repozytorium GitHub WingtipTicketsSaaS MultiTenantDb](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb).
2. Kliknij przycisk **klonowania lub pobierania**.
3. Kliknij przycisk **Pobierz ZIP** i Zapisz plik.
4. Kliknij prawym przyciskiem myszy **WingtipTicketsSaaS-MultiTenantDb-master.zip** plik i wybierz **właściwości**.
5. Na **ogólne** wybierz opcję **Odblokuj**i kliknij przycisk **Zastosuj**.
6. Kliknij przycisk **OK**.
7. Wyodrębnij pliki.

Skrypty te są dostępne w *... \\WingtipTicketsSaaS MultiTenantDb wzorca\\modułów uczenia\\*  folderu.

## <a name="update-the-configuration-file-for-this-deployment"></a>Aktualizowanie pliku konfiguracji dla tego wdrożenia

Przed uruchomieniem skrypty należy ustawić *grupy zasobów* i *użytkownika* wartości w **UserConfig.psm1**. Te zmienne należy ustawić na te same wartości ustawione podczas wdrażania.

1. Otwórz... \\Modułów szkoleniowych\\*UserConfig.psm1* w *programu PowerShell ISE*.
2. Aktualizacja *ResourceGroupName* i *nazwa* z określonymi wartościami dla danego wdrożenia (wierszach 10 i 11 tylko).
3. Zapisz zmiany.

Wartości ustawione w tym pliku jest używana przez wszystkie skrypty, więc należy pamiętać, że są prawidłowe. Jeśli należy ponownie wdrożyć aplikację, musisz wybrać różne wartości dla użytkowników i grupy zasobów. Następnie zaktualizuj plik UserConfig.psm1 ponownie przy użyciu nowych wartości.

## <a name="run-the-application"></a>Uruchamianie aplikacji

W aplikacji Wingtip dzierżawcami są miejsc. Miejsce można hall porozumieniu, klub sportowych lub innej lokalizacji, która obsługuje zdarzenia. Miejsc Zarejestruj w Wingtip jako klientów, a identyfikator dzierżawy jest generowany dla każdego miejsca. Każda właściwość zawiera listę jego kolejnych zdarzeń w Wingtip, tak publicznego można kupić bilety do zdarzeń.

Każda właściwość pobiera aplikację sieci web spersonalizowane listy ich zdarzeń i sprzedawać bilety. Każda aplikacja sieci web jest niezależna i odizolowany od pozostałych dzierżawców. Wewnętrznie w usłudze Azure SQL Database, każdy danych dla każdego dzierżawcy jest domyślnie przechowywane w bazie podzielonej wielodostępnej. Wszystkie dane są oznaczane identyfikator dzierżawy.

Centralnego **Centrum zdarzeń** strony sieci Web zawiera listę łącza do dzierżawców w danego wdrożenia. Wykonaj następujące kroki, aby zgłaszać **Centrum zdarzeń** strony sieci Web i aplikacji sieci web poszczególnych:

1. Otwórz **Centrum zdarzeń** w przeglądarce sieci web:
    - http://events.wingtip-mt. &lt;użytkownika&gt;. trafficmanager.net &nbsp; *(Zastąp &lt;użytkownika&gt; o wartości użytkownika w danym wdrożeniu.)*

    ![centrum zdarzeń](media/saas-multitenantdb-get-started-deploy/events-hub.png)

2. Kliknij przycisk **Fabrikam Jazz Club** w **Centrum zdarzeń**.

   ![Zdarzenia](./media/saas-multitenantdb-get-started-deploy/fabrikam.png)

#### <a name="azure-traffic-manager"></a>Azure Traffic Manager

Aby kontrolować dystrybucję żądań przychodzących, używa aplikacji Wingtip [usługi Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md). Strona zdarzenia dla każdego dzierżawcy zawiera nazwę dzierżawy w jego adres URL. Każdy adres URL zawiera także wartość określonego użytkownika. Każdy adres URL przestrzega format wyświetlane przy użyciu następujących kroków:

- http://events.wingtip-mt.&lt;user&gt;.trafficmanager.net/*fabrikamjazzclub*

1. Aplikacja zdarzenia analizuje nazwę dzierżawcy z adresu URL. Nazwa dzierżawy to *fabrikamjazzclub* w poprzednim przykładzie adres URL.
2. Aplikacja następnie skróty nazwę dzierżawcy, aby utworzyć klucz, aby uzyskiwać dostęp za pomocą wykazu [zarządzania mapy niezależnego fragmentu](sql-database-elastic-scale-shard-map-management.md).
3. Aplikacja znajduje klucz w katalogu i uzyskuje odpowiednie lokalizacji dzierżawcy w bazie danych.
4. Aplikacja używa informacji o lokalizacji do znalezienia i dostęp do jednej bazy danych, który zawiera wszystkie dane dla dzierżawcy.

#### <a name="events-hub"></a>Centrum zdarzeń

1. **Centrum zdarzeń** zawiera listę wszystkich dzierżawców, które są zarejestrowane w wykazie i ich miejsc.
2. **Centrum zdarzeń** używa rozszerzonych metadanych w katalogu można pobrać nazwy dzierżawcy skojarzonej z każdego mapowania do utworzenia adresów URL.

W środowisku produkcyjnym zwykle Utwórz rekord CNAME DNS do [punktu firmowej domeny internetowej](../traffic-manager/traffic-manager-point-internet-domain.md) profilu Menedżera ruchu.

## <a name="start-generating-load-on-the-tenant-databases"></a>Rozpoczęcie generowanie obciążenia baz danych dzierżawy

Teraz, gdy aplikacja jest wdrożona umieśćmy go do pracy! *LoadGenerator pokaz* pracą dla każdego dzierżawcy uruchamia skrypt programu PowerShell. Rzeczywiste obciążenie wiele aplikacji SaaS jest zwykle sporadyczne i nieprzewidywalny. Aby symulować tego rodzaju obciążenia, generator tworzy obciążenia dystrybuowana do wszystkich dzierżawców. Obciążenie obejmuje losowe seria każdego dzierżawcy występujących w odstępach czasu losowego. Trwa kilka minut dla wzorca obciążenia się, dlatego najlepszym rozwiązaniem generator uruchamiania na co najmniej trzech lub czterech minut przed monitorowania obciążenia.

1. W *PowerShell ISE*, Otwórz... \\Modułów uczenia\\narzędzia\\*LoadGenerator.ps1 pokaz* skryptu.
2. Naciśnij klawisz **F5**, aby uruchomić skrypt i generator obciążenia (na razie pozostaw bez zmian wartości domyślne parametrów).

*LoadGenerator.ps1 pokaz* skrypt zostanie otwarty w innej sesji programu PowerShell, którym jest uruchamiany generator obciążenia. Generator obciążenia uruchamiane w tej sesji jako zadanie pierwszego planu, które wywołuje zadań w tle generacji obciążenia, po jednej dla każdego dzierżawcy.

Po uruchomieniu zadania pierwszego planu, pozostaje w stanie wywoływania zadania. Dodatkowe zadania jest uruchamiany dla nowi dzierżawcy, które są następnie udostępniane.

Zamykanie sesji programu PowerShell zatrzymuje wszystkie zadania.

Można ponownie uruchomić sesji generator obciążenia, aby użyć wartości innym parametrem. Jeśli tak, zamknij generowania sesji programu PowerShell, a następnie ponownie uruchom *LoadGenerator.ps1 pokaz*.

## <a name="provision-a-new-tenant-into-the-sharded-database"></a>Udostępnianie nowej dzierżawy do bazy danych podzielonej

Początkowe wdrożenie obejmuje trzy próbki dzierżaw w *Tenants1* bazy danych. Umożliwia tworzenie innej dzierżawy i sprawdź jej wpływ na wdrożonej aplikacji. W tym kroku naciśnij klawisz jednego klucza do utworzenia nowej dzierżawy:

1. Otwórz... \\Modułów szkoleniowych\\udostępniania i wykaz\\*ProvisionTenants.ps1 pokaz* w *programu PowerShell ISE*.
2. Naciśnij klawisz **F5** (nie **F8**) do uruchomienia skryptu (pozostaw wartości domyślne dla teraz).

   > [!NOTE]
   > Należy uruchamiać skrypty programu PowerShell, tylko przez naciśnięcie przycisku **F5** klucza nie naciskając **F8** do uruchomienia wybranej części skryptu. Problem z **F8** jest to, że *$PSScriptRoot* zmienna nie jest obliczane. Ta zmienna jest wymagana przez wiele skryptów do przeglądania folderów, wywołaj inne skrypty lub zaimportuj moduły.

Nowej dzierżawy Racing czerwony klon jest dodawany do *Tenants1* bazy danych i zarejestrowane w wykazie. Na nowym dzierżawcą sprzedaży biletów **zdarzenia** lokacji otwiera w przeglądarce:

![Nowa dzierżawa](./media/saas-multitenantdb-get-started-deploy/red-maple-racing.png)

Odśwież **Centrum zdarzeń**, i pojawi się na liście nowym dzierżawcą.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Udostępnianie nowej dzierżawy w własną bazę danych

Podzielonej modelu wielodostępnym pozwala wybrać, czy do udostępnienia nowej dzierżawy do bazy danych zawierającej innych dzierżawców lub w bazie danych własnych. Dzierżawca samodzielnie w własną bazę danych ma następujące korzyści:
- Wydajność bazy danych dzierżawcy mogą być zarządzane bez potrzeby atakowania potrzeb innych dzierżawców.
- Jeśli to konieczne, bazy danych można przywrócić do wcześniejszego punktu w czasie, ponieważ wpłynęłoby nie innych dzierżawców.

Można umieścić bezpłatnych wersji próbnych klientów lub klienci gospodarka do baz danych z wieloma dzierżawcami. Każda dzierżawa usługi premium mogą być wprowadzane w dedykowanej bazę danych. Jeśli tworzysz wiele baz danych zawierających tylko jedną dzierżawy, można zarządzać ich wszystkich zbiorczo w puli elastycznej, aby optymalizować koszty zasobów.

Następnie możemy udostępnić innej dzierżawy, to czas w własną bazę danych:

1. W... \\Modułów uczenia\\udostępniania i wykaz\\*ProvisionTenants.ps1 pokaz*, zmodyfikuj *$TenantName* do **Salsa wierzbowate**, *$VenueType* do **taniec** i *$Scenario* do **2**.

2. Naciśnij klawisz **F5** ponownie uruchomić skrypt.
    - To **F5** naciśnij przepisy nowej dzierżawy w oddzielnej bazy danych. Baza danych i dzierżawcy są rejestrowane w katalogu. Następnie przeglądarce otworzy stronę zdarzenia dzierżawy.

   ![Strona zdarzenia Salix Salsa](./media/saas-multitenantdb-get-started-deploy/salix-salsa.png)

   - Przewiń do dołu strony. Istnieje na transparencie Zobacz Nazwa bazy danych, w którym są przechowywane dane dzierżawy.

3. Odśwież **Centrum zdarzeń** i pojawi się na liście dwóch nowych dzierżaw.

## <a name="explore-the-servers-and-tenant-databases"></a>Eksploruj serwerów i baz danych dzierżawy

Teraz przyjrzymy się niektórych zasobów, które zostały wdrożone:

1. W [portalu Azure](http://portal.azure.com), przejdź do listy grup zasobów. Otwórz grupę zasobów, która utworzone podczas wdrażania aplikacji.

   ![grupa zasobów](./media/saas-multitenantdb-get-started-deploy/resource-group.png)

2. Kliknij przycisk **mt katalogu&lt;użytkownika&gt;**  serwera. Serwer katalogu zawiera dwie bazy danych o nazwie *tenantcatalog* i *basetenantdb*. *Basetenantdb* bazy danych jest bazą danych pustego szablonu. Aby utworzyć nową bazę danych dzierżawy jest kopiowany czy używany dla wielu dzierżawców lub pojedynczej dzierżawy.

   ![serwer wykazu](./media/saas-multitenantdb-get-started-deploy/catalog-server.png)

3. Wróć do grupy zasobów i wybierz *tenants1 mt* serwera baz danych dzierżawy.
    - Tenants1 bazy danych jest bazą danych wielodostępne przechowywane oryginalnego dzierżaw trzy, a także dzierżawcy pierwszy dodawane. Jest on skonfigurowany jako 50 jednostek dtu w warstwie standardowa bazy danych.
    - **Salixsalsa** bazy danych ma miejsce taniec Salsa wierzbowate jako jego tylko dzierżawy. Domyślnie jest skonfigurowany jako Standard edition baza danych o 50 Dtu.

   ![dzierżawcy serwera](./media/saas-multitenantdb-get-started-deploy/tenants-server.png)


## <a name="monitor-the-performance-of-the-database"></a>Monitorowanie wydajności bazy danych

Jeśli generator obciążenia uruchomione przez kilka minut, można znaleźć w bazie danych monitorowania funkcji wbudowanych w portalu Azure jest za mało danych telemetrii.

1. Przejdź do **tenants1 mt&lt;użytkownika&gt;**  serwera, a następnie kliknij przycisk **tenants1** do wyświetlania wykorzystania zasobów bazy danych, która ma cztery dzierżaw w nim. Każdy dzierżawca podlega sporadyczne duże obciążenie z generatora obciążenia:

   ![Monitor tenants1](./media/saas-multitenantdb-get-started-deploy/monitor-tenants1.png)

   Wykres wykorzystania jednostek dtu w warstwie dobrze pokazuje, jak wielodostępne bazy danych można obsługiwać nieprzewidywalne obciążenia między wiele dzierżaw. W takim przypadku generator obciążenia jest stosowanie sporadyczne obciążenie około 30 Dtu do każdego dzierżawcy. Ta operacja ładowania równa 60% wykorzystania 50 jednostek dtu w warstwie bazy danych. Wartości, które przekraczają 60% są wynikiem obciążenia są stosowane do więcej niż jednej dzierżawy, w tym samym czasie.

2. Przejdź do **tenants1 mt&lt;użytkownika&gt;**  serwera, a następnie kliknij przycisk **salixsalsa** bazy danych. Użycie zasobów można wyświetlić na tej bazy danych, która zawiera tylko jeden dzierżawy.

   ![salixsalsa bazy danych](./media/saas-multitenantdb-get-started-deploy/monitor-salix.png)

Generator obciążenia jest stosowanie obciążenia podobne do każdego dzierżawcy, niezależnie od tego, bazę danych, która jest każdego dzierżawcy. Z tylko jednego dzierżawcy w **salixsalsa** bazy danych, zobaczysz, że bazy danych może kontynuować działanie wyższe obciążenie niż baza danych z wielu dzierżawców. 

#### <a name="resource-allocations-vary-by-workload"></a>Zależy od obciążenia alokacji zasobów

Czasami wielodostępne bazy danych wymaga więcej zasobów na dobrą wydajność niż dzierżawy pojedynczej bazy danych, ale nie zawsze. Optymalne alokacji zasobów jest zależna od właściwości określonego obciążenia dla dzierżawcy w systemie.

Obciążeń generowanych przez skrypt generator obciążenia są wyłącznie dla celów ilustracyjnych.

## <a name="additional-resources"></a>Zasoby dodatkowe

- Aby dowiedzieć się więcej na temat wielodostępnych aplikacji SaaS, zobacz [wzorce dla aplikacji SaaS wielodostępne projektowe](saas-tenancy-app-design-patterns.md).

- Aby dowiedzieć się więcej na temat pule elastyczne, zobacz:
    - [Elastyczne pule pozwalają na zarządzanie i skalowania wielu baz danych Azure SQL](sql-database-elastic-pool.md)
    - [Scaling out with Azure SQL Database (Skalowanie w poziomie za pomocą usługi Azure SQL Database)](sql-database-elastic-scale-introduction.md)

## <a name="next-steps"></a>Kolejne kroki

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Jak wdrożyć aplikację Wingtip biletów SaaS wielodostępne w bazie danych.
> - Dotyczące serwerów i baz danych, które tworzą aplikacji.
> - Dzierżaw są mapowane do swoich danych z *katalogu*.
> - Sposób obsługi administracyjnej nowych dzierżaw do bazy danych z wieloma dzierżawcami i bazy danych z pojedynczej dzierżawy.
> - Jak wyświetlić wykorzystania puli, aby monitorować aktywność dzierżawy.
> - Jak usunąć przykładowych zasobów, aby zatrzymać rozliczeń powiązanego.

Teraz spróbuj [samouczek udostępniania i wykaz](sql-database-saas-tutorial-provision-and-catalog.md).


<!--  Link references.

A [series of related tutorials] is available that build upon this initial deployment.
[link-wtp-overivew-jumpto-saas-tutorials-97j]: saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials

-->

[link-aka-ms-deploywtp-mtapp-52k]: http://aka.ms/deploywtp-mtapp


[link-azure-get-started-powershell-41q]: https://docs.microsoft.com/powershell/azure/get-started-azureps

[link-github-wingtip-multitenantdb-55g]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/



<!--  Image references.

[image-deploy-to-azure-blue-48d]: http://aka.ms/deploywtp-mtapp "Button for Deploy to Azure."
-->

[image-deploy-to-azure-blue-48d]: media/saas-multitenantdb-get-started-deploy/deploy.png "Przycisk wdrażanie na platformie Azure."

