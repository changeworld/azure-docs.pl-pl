---
title: Wdrażanie aplikacji SaaS podzielonej na fragmenty wielodostępną bazą danych, która używa usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Wdrażanie i eksplorowanie aplikacji podzielonej na fragmenty SaaS o nazwie Wingtip Tickets wielodostępną bazą danych, który demonstruje wzorców SaaS przy użyciu usługi Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, stein
manager: craigg
ms.date: 10/16/2018
ms.openlocfilehash: 350e67f5a1e7e1eab7abe27a6ca851ed2420af84
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65978526"
---
# <a name="deploy-and-explore-a-sharded-multi-tenant-application"></a>Wdrażanie i eksplorowanie aplikacji z wieloma dzierżawami podzielonej na fragmenty

W tym samouczku, wdrażanie i eksplorowanie przykładowej aplikacji SaaS wielu dzierżawców o nazwie Wtp. Aplikacja Wtp jest przeznaczona do prezentowania funkcji usługi Azure SQL Database, które upraszczająca implementację elementów scenariuszy SaaS.

Ta implementacja aplikacja Wtp korzysta ze wzorca podzielonej na fragmenty wielodostępną bazą danych. Identyfikator dzierżawy jest dzielenie na fragmenty. Danych dzierżawy jest wysyłana do określonej bazy danych zgodnie z wartości identyfikatora dzierżawy. 

Ten wzorzec bazy danych umożliwia przechowywanie co najmniej jednego dzierżawcy w każdym fragmencie lub bazy danych. Na najniższym koszcie można zoptymalizować przez każdą bazę danych, być współużytkowane przez wielu dzierżaw. Lub można zoptymalizować izolacji dzięki każda baza danych jest przechowywanie tylko jednej dzierżawy. Wybór optymalizacji można robić niezależnie dla każdej określonej dzierżawy. Wybór można wprowadzić, gdy dzierżawy, najpierw jest przechowywany lub później zmieni zdanie. Aplikacja jest przeznaczona do pracy także w obu przypadkach.

## <a name="app-deploys-quickly"></a>Aplikację wdraża się szybko

Aplikacja działa w chmurze platformy Azure i korzysta z usługi Azure SQL Database. Poniższa sekcja wdrożenia zawiera niebieski **Wdróż na platformie Azure** przycisku. Po naciśnięciu przycisku aplikacja jest w pełni wdrożony do subskrypcji platformy Azure w ciągu pięciu minut. Masz pełny dostęp do pracy z poszczególnych składników aplikacji.

Aplikacja jest wdrażana przy użyciu danych dla trzema przykładowymi dzierżawami. Dzierżawy są przechowywane razem w jednej wielodostępnej bazie danych.

Każda osoba może pobrać kod źródłowy języka C# i programu PowerShell o nazwie Wingtip Tickets z [jako repozytorium GitHub][link-github-wingtip-multitenantdb-55g].

## <a name="learn-in-this-tutorial"></a>Dowiedz się, w tym samouczku

> [!div class="checklist"]
> - Jak wdrożyć aplikację SaaS o nazwie Wingtip Tickets.
> - Gdzie można uzyskać kod źródłowy aplikacji i skrypty zarządzania.
> - Temat serwerów i baz danych, które składają się na aplikację.
> - Jak dzierżawcy są mapowane do ich danych za pomocą *katalogu*.
> - Jak wykonać aprowizację nowej dzierżawy.
> - Sposób monitorowania aktywności dzierżawy w aplikacji.

Seria powiązanych samouczków jest dostępna, zależą od tego wstępnego wdrożenia. Samouczki zapoznaj się z wzorców projektowania i zarządzania zakresem SaaS. Kiedy pracujesz, zapoznając się z samouczkami, zachęcamy do kroków dostarczone skrypty, aby zobaczyć, sposobu implementacji różnych wzorców SaaS.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

- Zainstalowano najnowszą wersję programu Azure PowerShell. Aby uzyskać więcej informacji, zobacz [wprowadzenie do programu Azure PowerShell][link-azure-get-started-powershell-41q].

## <a name="deploy-the-wingtip-tickets-app"></a>Wdrażanie aplikacji Wingtip Tickets aplikacji

### <a name="plan-the-names"></a>Planowanie nazwy

W krokach w tej sekcji podaj *użytkownika* wartość, która służy do zapewnienia zasobów nazwy są globalnie unikatowe oraz nazwę *grupy zasobów* zawierającą wszystkie zasoby utworzone przez wdrożenie aplikacji. Dla osoby o nazwie *Finley pods*, sugerujemy:
- *Użytkownik:* **af1***(ich skróty, a także cyfry.   Użyj innej wartości (np. af2), gdy wdrożysz aplikację po raz drugi).*
- *Grupa zasobów:* **wingtip-mt-af1** *(o nazwie wingtip-mt wskazuje to aplikacji wielodostępnych podzielonej na fragmenty. Dołączanie af1 nazwa użytkownika jest skorelowane z nazwami zasobów, które zawiera nazwy grupy zasobów.)*

Teraz wybierz nazwy i zanotuj je. 

### <a name="steps"></a>Kroki

1. Kliknij poniższy niebieski **Wdróż na platformie Azure** przycisku.
   - Zostanie otwarta witryna Azure portal za pomocą szablonu wdrożenia SaaS o nazwie Wingtip Tickets.

     [![Przycisk Wdróż na platformie Azure.][image-deploy-to-azure-blue-48d]][link-aka-ms-deploywtp-mtapp-52k]

1. Wprowadź wymagane wartości parametrów wdrożenia.

    > [!IMPORTANT]
    > W tej prezentacji nie używaj żadnych istniejących grup zasobów, serwerów i pul. Zamiast tego wybierz **Utwórz nową grupę zasobów**. Po zakończeniu pracy z aplikacją usuń tę grupę zasobów, aby zatrzymać ich rozliczanie.
    > Nie należy używać tej aplikacji lub wszystkie zasoby, które tworzy, w środowisku produkcyjnym. Niektóre aspekty uwierzytelniania i ustawieniach zapory serwera są celowo niezabezpieczone w aplikacji w celu ułatwienia pokazu.

    - Dla **grupy zasobów** — wybierz tę opcję **Utwórz nową**, a następnie podaj **nazwa** dla grupy zasobów (z uwzględnieniem wielkości liter).
        - Wybierz **lokalizacji** z listy rozwijanej.
    - Aby uzyskać **użytkownika** — zaleca się, że wybierasz krótki **użytkownika** wartość.

1. **Wdróż aplikację**.

    - Kliknij, aby zaakceptować warunki i postanowienia.
    - Kliknij pozycję **Kup**.

1. Monitoruj stan wdrożenia, klikając **powiadomienia**, który jest ikona dzwonka z prawej strony pola wyszukiwania. Wdrażanie aplikacji Wingtip trwa około pięciu minut.

   ![wdrażanie zakończyło się pomyślnie](media/saas-multitenantdb-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-management-scripts"></a>Pobierz i odblokować skrypty zarządzania

Podczas wdrażania aplikacji Pobierz skrypty kodu i zarządzanie źródłowego aplikacji.

> [!NOTE]
> Zawartość pliku wykonywalnego (skrypty, pliki dll) może być blokowany przez Windows, gdy pliki zip są pobierane z zewnętrznego źródła i wyodrębnić. Podczas wyodrębniania skryptów z pliku zip, wykonaj następujące kroki, aby odblokować plik zip weryfikowany przed wyodrębnianiem. Odblokowanie pliku zip, gwarantuje, że można uruchamiać skrypty.

1. Przejdź do [repozytorium GitHub WingtipTicketsSaaS MultiTenantDb](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb).
2. Kliknij przycisk **klonowania lub pobierania**.
3. Kliknij przycisk **Pobierz ZIP** i Zapisz plik.
4. Kliknij prawym przyciskiem myszy **WingtipTicketsSaaS-MultiTenantDb-master.zip** plik i wybierz **właściwości**.
5. Na **ogólne** zaznacz **odblokowanie**i kliknij przycisk **Zastosuj**.
6. Kliknij przycisk **OK**.
7. Wyodrębnij pliki.

Skrypty te są dostępne w *... \\WingtipTicketsSaaS MultiTenantDb wzorca\\Learning Modules\\*  folderu.

## <a name="update-the-configuration-file-for-this-deployment"></a>Należy zaktualizować plik konfiguracji dla tego wdrożenia

Przed uruchomieniem wszystkie skrypty, ustaw *grupy zasobów* i *użytkownika* wartości w **UserConfig.psm1**. Ustaw te zmienne na te same wartości ustawione podczas wdrażania.

1. Otwórz... \\Learning Modules\\*UserConfig.psm1* w *środowisku PowerShell ISE*.
2. Aktualizacja *ResourceGroupName* i *nazwa* z określonymi wartościami dla danego wdrożenia (w wierszach, 10 i 11 tylko).
3. Zapisz zmiany.

Wartości ustawione w tym pliku są używane przez wszystkie skrypty, dlatego ważne jest, aby są prawidłowe. Jeśli ponowne wdrażanie aplikacji, musisz wybrać różne wartości dla użytkowników i grupy zasobów. Następnie należy zaktualizować UserConfig.psm1 plik ponownie z nowymi wartościami.

## <a name="run-the-application"></a>Uruchamianie aplikacji

W aplikacji Wingtip dzierżawców są lokalizacje. Właściwości mogą być concert hall, club Sport lub dowolnej innej lokalizacji, który obsługuje zdarzenia. Lokalizacje zarejestrować się w aplikacji Wingtip jako klienci i identyfikator dzierżawy jest generowany dla każdego miejsca. Każde miejsce wyświetla jego Nadchodzące wydarzenia w Wingtip, więc publicznie można kupić bilety na potrzeby zdarzeń.

Każde miejsce otrzymuje spersonalizowaną aplikację sieci web do wyświetlania i sprzedawać bilety. Każda aplikacja sieci web jest niezależna i odizolowany od pozostałych dzierżawców. Wewnętrznie w usłudze Azure SQL Database, każdy danych dla każdego dzierżawcy znajduje się w podzielonej na fragmenty wielodostępną bazą danych, domyślnie. Wszystkie dane są oznakowane za pomocą identyfikatora dzierżawy.

Centralna **Centrum zdarzeń** strony sieci Web zawiera listę linków dla dzierżaw w ramach danego wdrożenia. Wykonaj następujące kroki, aby środowisko **Centrum zdarzeń** strony sieci Web i aplikację internetową poszczególnych:

1. Otwórz **Centrum zdarzeń** w przeglądarce sieci web:
   - http://events.wingtip-mt.&lt; użytkownika&gt;. trafficmanager.net &nbsp; *(Zastąp &lt;użytkownika&gt; wartością użytkownika wdrożenia.)*

     ![centrum zdarzeń](media/saas-multitenantdb-get-started-deploy/events-hub.png)

2. Kliknij przycisk **Fabrikam Jazz Club** w **Centrum zdarzeń**.

   ![Zdarzenia](./media/saas-multitenantdb-get-started-deploy/fabrikam.png)

### <a name="azure-traffic-manager"></a>Azure Traffic Manager

Aby kontrolować dystrybucję żądań przychodzących, aplikacja Wingtip używa [usługi Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md). Na stronie zdarzenia dla każdej dzierżawy zawiera nazwę dzierżawy w jego adresie URL. Każdy adres URL zawiera również wartość określonego użytkownika. Każdy adres URL przestrzegają pokazano format wykonując następujące kroki:

- http://events.wingtip-mt.&lt;user&gt;.trafficmanager.net/*fabrikamjazzclub*

1. Aplikacja zdarzeń analizuje nazwę dzierżawy w adresie URL. Nazwa dzierżawy jest *fabrikamjazzclub* w poprzednim przykładowego adresu URL.
2. Aplikacja następnie tworzy skrót nazwy dzierżawy, aby utworzyć klucz w celu uzyskania dostępu do wykazu przy użyciu [procesu zarządzania mapą fragmentów](sql-database-elastic-scale-shard-map-management.md).
3. Aplikacja umożliwia znalezienie klucza w katalogu i uzyskuje odpowiedniej lokalizacji bazy danych dzierżaw.
4. Aplikacja używa informacji o lokalizacji, aby znaleźć i uzyskać dostęp jedną bazę danych, który zawiera wszystkie dane dla dzierżawy.

### <a name="events-hub"></a>Centrum zdarzeń

1. **Centrum zdarzeń** Wyświetla listę wszystkich dzierżaw, które są zarejestrowane w wykazie i ich lokalizacje.
2. **Centrum zdarzeń** używa rozszerzonych metadanych zawartych w wykazie do pobierania nazwy dzierżawy skojarzonej z każdego mapowania do konstruowania adresów URL.

W środowisku produkcyjnym zazwyczaj tworzenie rekordu CNAME DNS, aby [punktu firmowej domeny internetowej](../traffic-manager/traffic-manager-point-internet-domain.md) do profilu usługi traffic manager.

## <a name="start-generating-load-on-the-tenant-databases"></a>Rozpoczęcie generowanie obciążenia baz danych dzierżawy

Teraz, gdy aplikacja jest wdrożona zbierzmy do pracy! *LoadGenerator pokaz* skrypt programu PowerShell uruchamia obciążenia dla każdej dzierżawy. Rzeczywiste obciążenie na wiele aplikacji SaaS jest zazwyczaj sporadyczne i nieprzewidywalne. Aby zasymulować ten typ obciążenia, generator tworzy obciążenia rozproszone we wszystkich dzierżawach. Obciążenia zawiera losowego wzrosty na każdą dzierżawę występujących w odstępach czasu losowego. Trwa kilka minut, zanim wzorca obciążenia, się zatem najlepiej pozwolić uruchamiania na co najmniej trzech lub czterech minut przed rozpoczęciem monitorowania obciążenia.

1. W *PowerShell ISE*, Otwórz... \\Learning Modules\\narzędzia\\*LoadGenerator.ps1 pokaz* skryptu.
2. Naciśnij klawisz **F5**, aby uruchomić skrypt i generator obciążenia (na razie pozostaw bez zmian wartości domyślne parametrów).

*LoadGenerator.ps1 pokaz* zostanie otwarty skrypt w innej sesji programu PowerShell, na którym jest uruchomiony generator obciążenia. Generator obciążenia działa w ramach tej sesji jako zadań pierwszego planu, który wywołuje zadania w tle Generowanie obciążenia, jeden dla każdego dzierżawcy.

Po uruchomieniu zadań pierwszego planu, pozostaje w stanie wywoływanie zadania. Zadanie uruchamia zadania w tle dodatkowe dla nowych dzierżaw, które są następnie udostępniane.

Zamykanie sesji programu PowerShell powoduje zatrzymanie wszystkich zadań.

Można ponownie uruchomić sesji generator obciążenia, aby użyć wartości różnych parametrów. Jeśli tak, zamknij generowania sesji programu PowerShell, a następnie ponownie uruchom *LoadGenerator.ps1 pokaz*.

## <a name="provision-a-new-tenant-into-the-sharded-database"></a>Aprowizacja nowej dzierżawy do bazy danych podzielonej na fragmenty

Początkowe wdrożenie obejmuje trzema przykładowymi dzierżawami w *Tenants1* bazy danych. Umożliwia tworzenie innej dzierżawy i sprawdź jej wpływ na wdrożoną aplikację. W tym kroku naciśnij klawisz jednego klucza do utworzenia nowej dzierżawy:

1. Otwórz... \\Learning Modules\\Provision and Catalog\\*ProvisionTenants.ps1 pokaz* w *środowisku PowerShell ISE*.
2. Naciśnij klawisz **F5** (nie **F8**) do uruchomienia skryptu (pozostaw wartości domyślne teraz).

   > [!NOTE]
   > Skrypty programu PowerShell należy uruchomić tylko przez naciśnięcie **F5** klucza, a nie przez naciśnięcie klawisza **F8** do uruchomienia wybranego części skryptu. Problem z **F8** jest fakt, że *$PSScriptRoot* zmienna nie jest oceniany. Ta zmienna jest wymagana przez wiele skryptów do przeglądania folderów, wywołaj inne skrypty lub zaimportować moduły.

Nowa dzierżawa Red Maple Racing jest dodawany do *Tenants1* bazy danych i zarejestrowanych w wykazie. Nowa dzierżawa użytkownika sprzedaży biletów **zdarzenia** lokacji zostanie otwarta w przeglądarce:

![Nowa dzierżawa](./media/saas-multitenantdb-get-started-deploy/red-maple-racing.png)

Odśwież **Centrum zdarzeń**, oraz nowej dzierżawy pojawi się na liście.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Aprowizacja nowej dzierżawy w oddzielnej bazie danych

Podzielonej na fragmenty modelu wielodostępnym umożliwia wybranie, czy aprowizacja nowej dzierżawy do bazy danych, który zawiera innych dzierżaw lub do własnej bazy danych. Dzierżawca samodzielnie w własną bazę danych ma następujące korzyści:

- Wydajność bazy danych dzierżawy mogą być zarządzane bez konieczności naruszyć o potrzebach innych dzierżaw.
- Jeśli to konieczne, bazy danych można przywrócić do wcześniejszego punktu w czasie, ponieważ wpłynęłoby nie innych dzierżaw.

Możesz wybrać bezpłatną wersję próbną klienci lub klienci gospodarki, umieść się wielodostępnych baz danych. Każda dzierżawa premium można umieścić w swój własny dedykowany bazy danych. Jeśli tworzysz wiele baz danych, które zawierają tylko jedną dzierżawą, można zarządzać ich wszystkich zbiorczo elastycznej puli w celu zoptymalizowania kosztów zasobów.

Następnie możemy Zapewnij innej dzierżawy, tym razem w oddzielnej bazie danych:

1. W... \\Learning Modules\\Provision and Catalog\\*ProvisionTenants.ps1 pokaz*, zmodyfikuj *$TenantName* do **Salsa wierzbowate**, *$VenueType* do **dance** i *$Scenario* do **2**.

2. Naciśnij klawisz **F5** ponownie uruchomić skrypt.
    - To **F5** naciśnij aprowizuje nową dzierżawę w oddzielnej bazy danych. Baza danych i dzierżawy są zarejestrowane w wykazie. Następnie przeglądarce otworzy stronę zdarzeń dzierżawy.

   ![Strona zdarzenia Salix Salsa](./media/saas-multitenantdb-get-started-deploy/salix-salsa.png)

   - Przewiń do dołu strony. Istnieje na transparencie zobaczysz nazwę bazy danych, w której przechowywane są dane dzierżawy.

3. Odśwież **Centrum zdarzeń** i pojawi się na liście dwóch nowych dzierżaw.

## <a name="explore-the-servers-and-tenant-databases"></a>Zapoznaj się z serwerów i baz danych dzierżaw

Teraz przyjrzymy się niektóre zasoby, które zostały wdrożone:

1. W [witryny Azure portal](https://portal.azure.com), przejdź do listy grup zasobów. Otwórz grupę zasobów, który został utworzony podczas wdrożono aplikację.

   ![grupa zasobów](./media/saas-multitenantdb-get-started-deploy/resource-group.png)

2. Kliknij przycisk **catalog-mt&lt;użytkownika&gt;**  serwera. Serwer wykazu zawiera dwie bazy danych o nazwie *tenantcatalog* i *basetenantdb*. *Basetenantdb* baza danych jest bazą danych pustego szablonu. Jest kopiowana do utworzenia nowej bazy danych dzierżawy, czy używane dla wielu dzierżawców lub tylko jednej dzierżawy.

   ![serwer wykazu](./media/saas-multitenantdb-get-started-deploy/catalog-server.png)

3. Wróć do grupy zasobów i wybierz *tenants1-mt* serwera baz danych dzierżaw.
    - Baza danych tenants1 jest przechowywane oryginalnego trzy dzierżawami oraz pierwszy dzierżawy, który został dodany, wielodostępną bazą danych. Jest on skonfigurowany jako 50 standardowych jednostek DTU bazy danych.
    - **Salixsalsa** baza danych przechowuje miejsc dance Salsa wierzbowate jako jego tylko dzierżawy. Domyślnie jest skonfigurowany jako standardową bazę danych o 50 jednostek Dtu.

   ![Serwer dzierżaw](./media/saas-multitenantdb-get-started-deploy/tenants-server.png)

## <a name="monitor-the-performance-of-the-database"></a>Monitorowanie wydajności bazy danych

Jeśli generator obciążenia działa od kilku minut, taką ilość telemetrii, jest dostępna do wyszukiwania w bazie danych monitorowania funkcji wbudowanych w witrynie Azure portal.

1. Przejdź do **tenants1-mt&lt;użytkownika&gt;**  serwera, a następnie kliknij przycisk **tenants1** do wyświetlenia wykorzystania zasobów bazy danych, która zawiera cztery dzierżaw. Każda dzierżawa podlega sporadyczne duże obciążenia z generator obciążenia:

   ![monitorowanie tenants1](./media/saas-multitenantdb-get-started-deploy/monitor-tenants1.png)

   Wykres wykorzystania jednostek DTU ilustruje dobrze, jak wielodostępną bazą danych może obsługiwać nieprzewidywalne zmiany obciążenia dla wielu dzierżaw. W tym przypadku generator obciążenia są stosowane sporadyczne obciążenia około 30 jednostek dtu do każdego dzierżawcy. Obciążenie jest równa wykorzystanie 60%, 50 jednostek DTU bazy danych. Wartości szczytowe, która przekracza 60% są wynikiem obciążenia stosowane do więcej niż jednej dzierżawy w tym samym czasie.

2. Przejdź do **tenants1-mt&lt;użytkownika&gt;**  serwera, a następnie kliknij przycisk **salixsalsa** bazy danych. Zobaczysz wykorzystanie zasobów w tej bazie danych, który zawiera tylko jedną dzierżawą.

   ![salixsalsa bazy danych](./media/saas-multitenantdb-get-started-deploy/monitor-salix.png)

Generator obciążenia są stosowane obciążenia podobne do każdej dzierżawy, niezależnie od tego, w której bazy danych jest każdego dzierżawcy. Przy użyciu tylko jednej dzierżawy w **salixsalsa** bazy danych, widać, że baza danych może kontynuować działanie znacznie wyższe obciążenie niż bazy danych z wielu dzierżaw. 

### <a name="resource-allocations-vary-by-workload"></a>Alokacji zasobów zależy od obciążenia

Czasami wielodostępną bazą danych wymaga więcej zasobów na dobrą wydajność niż bazę pojedynczego dzierżawy, ale nie zawsze. Optymalne alokacji zasobów zależy od konkretnego charakterystyki przez dzierżawców w systemie.

Obciążeń generowanych przez skryptu generatora obciążenia są wyłącznie w celach ilustracyjnych.

## <a name="additional-resources"></a>Dodatkowe zasoby

- Aby dowiedzieć się więcej o wielodostępnych aplikacji SaaS, zobacz [wzorce projektowe dla wielodostępnych aplikacji SaaS](saas-tenancy-app-design-patterns.md).

- Aby dowiedzieć się więcej o elastycznych pulach, zobacz:

  - [Pul elastycznych pozwalają na zarządzanie i skalowania wielu baz danych Azure SQL](sql-database-elastic-pool.md)
  - [Scaling out with Azure SQL Database (Skalowanie w poziomie za pomocą usługi Azure SQL Database)](sql-database-elastic-scale-introduction.md)

## <a name="next-steps"></a>Kolejne kroki

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Jak wdrożyć aplikację Wingtip Tickets SaaS wielodostępnej w bazie danych.
> - Dotyczące serwerów i baz danych, które składają się na aplikację.
> - Mapowanie dzierżaw na ich danych za pomocą *katalogu*.
> - Jak wykonać aprowizację nowych dzierżaw w wielodostępnej bazie danych i bazy danych z jedną dzierżawą.
> - Jak wyświetlić wykorzystania puli w celu monitorowania aktywności dzierżawy.
> - Jak usunąć przykładowych zasobów w celu zakończenia ich rozliczania.

Wypróbuj teraz [samouczek dotyczący aprowizacji i wykazu](sql-database-saas-tutorial-provision-and-catalog.md).


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

[image-deploy-to-azure-blue-48d]: media/saas-multitenantdb-get-started-deploy/deploy.png "Przycisk wdrażanie na platformie Azure."

