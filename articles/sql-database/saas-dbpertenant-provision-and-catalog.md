---
title: Aprowizowanie nowych dzierżaw w aplikacji wielodostępnej, który używa usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Dowiedz się, jak do aprowizacji i wykazu nowych dzierżaw w wielodostępnej aplikacji SaaS usługi Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 09/24/2018
ms.openlocfilehash: 803d05e1aaf4d9c26a6132bde30f101ce3905924
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61388346"
---
# <a name="learn-how-to-provision-new-tenants-and-register-them-in-the-catalog"></a>Dowiedz się, jak aprowizowanie nowych dzierżaw i rejestrowanie ich w wykazie

W tym samouczku dowiesz się, jak aprowizować i katalog wzorców SaaS. Dowiesz się również, jak są one wykonywane w aplikacji SaaS o nazwie Wingtip Tickets bazy danych dla dzierżawcy. Tworzenie i Inicjowanie nowych baz danych dzierżaw i rejestrowanie ich w katalogu dzierżawy aplikacji. Katalog jest bazy danych, która utrzymuje mapowanie między wieloma dzierżawami aplikacji SaaS i ich dane. Wykaz odgrywa ważną rolę w kierowaniu żądań zarządzania, prawidłową bazę danych i aplikacji.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> 
> * Aprowizowanie pojedynczej nowej dzierżawy.
> * Aprowizowanie partii dodatkowych dzierżaw.


Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

* Aplikacja SaaS o nazwie Wingtip Tickets bazy danych dla dzierżawcy jest wdrożona. Aby wdrożyć go w mniej niż pięć minut, zobacz [wdrażanie i eksplorowanie aplikacji SaaS o nazwie Wingtip Tickets bazy danych dla dzierżawcy](saas-dbpertenant-get-started-deploy.md).
* Zainstalowany jest program Azure PowerShell. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-catalog-pattern"></a>Wprowadzenie do wzorca wykazu SaaS

W kopii bazy danych wielodostępnych aplikacji SaaS warto wiedzieć, gdzie są przechowywane informacje dla każdej dzierżawy. We wzorcu wykazu SaaS baza danych wykazu służy do przechowywania mapowania między każdego dzierżawcy i bazy danych, w którym są przechowywane ich dane. Ten wzorzec ma zastosowanie zawsze, gdy dane dzierżawy są rozproszone w wielu bazach danych.

Każda dzierżawa jest identyfikowany przez klucz w katalogu, który jest mapowany na lokalizację ich bazy danych. W aplikacji Wtp klucz jest tworzony na podstawie funkcji skrótu nazwy dzierżawy. Ten schemat pozwala aplikacji do konstruowania klucza na podstawie nazwy dzierżawy, zawarte w adresie URL aplikacji. Należy używać innych systemów klucza dzierżawy.  

Wykaz umożliwia nazwę lub lokalizację bazy danych można zmienić przy minimalnym wpływie na aplikację. W modelu wielodostępnym bazy danych ta możliwość obejmuje także, przenoszenie dzierżawy między bazami danych. Katalog można również wskazać, czy dzierżawcy lub baza danych jest w trybie offline z powodu konserwacji lub innych działań. Ta funkcja jest przedstawione w [przywracania pojedynczej dzierżawy samouczek](saas-dbpertenant-restore-single-tenant.md).

Katalog również przechowywać dodatkowe dzierżawy lub metadanych bazy danych, takie jak wersja schematu, plan usługi lub umowy SLA oferowanych dzierżawcom. Katalog może przechowywać inne informacje, które umożliwiają zarządzanie aplikacjami, dział obsługi klienta lub metodyki DevOps. 

Poza aplikacją SaaS katalogu można włączyć narzędzia graficzne bazy danych. W tym przykładzie bazy danych dla dzierżawcy SaaS o nazwie Wingtip Tickets umożliwia włączanie kwerenda międzydzierżawowa, który jest przedstawione w katalogu [raportowanie Ad hoc samouczek](saas-tenancy-cross-tenant-reporting.md). Zarządzanie bazami danych zadania są przedstawione w [Zarządzanie schematami](saas-tenancy-schema-management.md) i [Tenant analytics](saas-tenancy-tenant-analytics.md) samouczków. 

W przykładach SaaS o nazwie Wingtip Tickets wykazu jest implementowany przy użyciu funkcji zarządzania fragmentami [Biblioteka kliencka Elastic Database (EDCL)](sql-database-elastic-database-client-library.md). Biblioteka EDCL jest dostępna w języku Java i .NET Framework. Biblioteka EDCL umożliwia aplikacji na tworzenie i zarządzanie nimi za pomocą mapy fragmentów kopii bazy danych. 

Mapowania fragmentów w postaci zawiera listę fragmentów (baz danych) i mapowanie pomiędzy kluczami (dzierżawami) i fragmentów. Funkcje biblioteki EDCL są używane podczas inicjowania obsługi można utworzyć wpisów w ramach mapowania fragmentów dzierżawy. Służą one w czasie wykonywania przez aplikacje, aby nawiązać połączenie z odpowiednią bazą danych. Biblioteka EDCL buforuje informacje o połączeniu, aby zminimalizować ruch do bazy danych wykazów i aplikacja działa szybciej. 

> [!IMPORTANT]
> Dane mapowania są dostępne w bazie danych wykazu, ale *nie należy ich edytować*. Dane mapowania można edytować tylko przy użyciu biblioteki klienta elastycznej bazy danych interfejsów API. Bezpośrednie manipulowanie danymi mapowania ryzykiem uszkodzenia wykazu i nie jest obsługiwany.


## <a name="introduction-to-the-saas-provisioning-pattern"></a>Wprowadzenie do wzorca inicjowania obsługi administracyjnej SaaS

Po dodaniu nowego dzierżawcy w aplikacji SaaS, która używa modelu jednej dzierżawy bazy danych należy zarezerwować nową bazę danych dzierżawy. Bazy danych należy utworzyć w odpowiedniej lokalizacji i warstwy usług. Także ona musi zostać zainicjowany przy użyciu odpowiedniego schematu i danych referencyjnych. I musi być zarejestrowana w wykazie w kluczu odpowiedniego dzierżawy. 

Może służyć różne metody inicjowania obsługi bazy danych. Możesz wykonywania skryptów SQL, wdrożyć plik bacpac lub kopiowania szablonu bazy danych. 

Inicjowanie obsługi administracyjnej musi być częścią strategii zarządzania schematu bazy danych. Upewnij się, że nowych baz danych są aprowizowane ze schematem najnowszych. To wymaganie jest przedstawione w [samouczka dotyczącego zarządzania schematu](saas-tenancy-schema-management.md). 

Aplikacja Wtp bazy danych dla dzierżawcy aprowizuje nowe dzierżawy, kopiując szablonu bazy danych o nazwie _basetenantdb_, który jest wdrożony na serwerze wykazu. Inicjowanie obsługi administracyjnej można zintegrować aplikację jako część środowiska rejestracji. On również mogą być obsługiwane w trybie offline za pomocą skryptów. W tym samouczku przedstawiono, inicjowanie obsługi administracyjnej przy użyciu programu PowerShell. 

Inicjowanie obsługi administracyjnej skrypty kopii _basetenantdb_ bazy danych do utworzenia nowej bazy danych dzierżawy w puli elastycznej. Bazy danych dzierżawy jest tworzony na serwerze dzierżawy mapowane na _newtenant_ aliasu DNS. Ten alias obsługuje odwołania do serwera, używane do aprowizacji nowych dzierżaw i jest aktualizowana w celu wskazać serwer dzierżawy odzyskiwania w ramach samouczków odzyskiwania po awarii ([odzyskiwania po awarii przy użyciu georestore](saas-dbpertenant-dr-geo-restore.md), [odzyskiwania po awarii przy użyciu replikacji geograficznej](saas-dbpertenant-dr-geo-replication.md)). Skrypty następnie zainicjować bazy danych przy użyciu informacji specyficznych dla dzierżawy i zarejestruj je w ramach mapowania fragmentów w katalogu. Baz danych dzierżawy są podanej nazwy na podstawie nazwy dzierżawy. Ten schemat nazewnictwa jest kluczową częścią wzorzec. Wykaz służy do mapowania klucza dzierżawy Nazwa bazy danych, dzięki czemu można użyć dowolnego konwencji nazewnictwa. 


## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Pobierz skrypty bazy danych dla dzierżawcy aplikacji SaaS o nazwie Wingtip Tickets

SaaS o nazwie Wingtip Tickets skrypty i kod źródłowy aplikacji są dostępne w [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) repozytorium GitHub. Zapoznaj się z [ogólne wskazówki dotyczące](saas-tenancy-wingtip-app-guidance-tips.md) kroków pobrać i odblokować skrypty SaaS o nazwie Wingtip Tickets.


## <a name="provision-and-catalog-detailed-walkthrough"></a>Szczegółowy przewodnik po aprowizacji i wykazie

Aby dowiedzieć się, jak aplikacja Wtp implementuje inicjowania obsługi nowej dzierżawy, Dodaj punkt przerwania, a następnie postępuj zgodnie z przepływu pracy, natomiast aprowizować dzierżawy.

1. W środowisku PowerShell ISE Otwórz... \\Learning Modules\\ProvisionAndCatalog\\_Demo-ProvisionAndCatalog.ps1_ i ustaw następujące parametry:

   * **$TenantName** = nazwa nowego miejsca (na przykład *Bushwillow Blues*).
   * **$VenueType** = jeden z typów miejsc wstępnie zdefiniowane: _blues, classicalmusic, dance, jazz, judo, mechanicznych wyścigi, multipurpose, opera, rockmusic, soccer_.
   * **$DemoScenario** = **1**, *aprowizacja jednej dzierżawy*.

2. Aby Dodaj punkt przerwania, należy umieścić kursor gdziekolwiek w wierszu, który jest wyświetlany komunikat *New-Tenant '*. Naciśnij klawisz F9.

   ![Punkt przerwania](media/saas-dbpertenant-provision-and-catalog/breakpoint.png)

3. Aby uruchomić skrypt, naciśnij klawisz F5.

4. Po zatrzymaniu w punkcie przerwania wykonywania skryptu należy nacisnąć klawisz F11, aby przejść do kodu.

   ![Debugowanie](media/saas-dbpertenant-provision-and-catalog/debug.png)



Śledź wykonywanie skryptu za pomocą **debugowania** opcji menu. Naciśnij klawisz F10 i F11, aby wychodzisz nad lub do wywoływanych funkcji. Aby uzyskać więcej informacji na temat debugowania skryptów programu PowerShell, zobacz [porady dotyczące pracy z i debugowania skryptów programu PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).


Nie trzeba jawnie postępuj zgodnie z tego przepływu pracy. Wyjaśniono, jak debugować skrypt.

* **Zaimportuj moduł CatalogAndDatabaseManagement.psm1.** Zapewnia on wykazu i dzierżawy level abstrakcji przez [zarządzania fragmentami](sql-database-elastic-scale-shard-map-management.md) funkcji. Ten moduł hermetyzuje znaczną część wzorca wykazu i jest wart poznania.
* **Zaimportuj moduł SubscriptionManagement.psm1.** Zawiera ona funkcje logowania się do platformy Azure i wyboru subskrypcji platformy Azure, którą chcesz pracować.
* **Pobierz szczegóły konfiguracji.** Wkrocz pobiera konfigurację przy użyciu F11 i zobacz, jak określono konfigurację aplikacji. W tym miejscu są zdefiniowane nazwy zasobów i innych wartości specyficznych dla aplikacji. Nie należy zmieniać te wartości, dopóki nie znasz skryptów.
* **Pobierz obiekt wykazu.** Wejdź do Get-katalog, który komponuje się i zwraca obiekt wykazu, który jest używany w skrypcie wyższego poziomu. Ta funkcja korzysta z funkcji zarządzania fragmentami, które są importowane z **AzureShardManagement.psm1**. Obiekt wykazu składa się z następujących elementów:

   * Obiekt $catalogServerFullyQualifiedName jest konstruowany przy użyciu standardowego oraz nazwa użytkownika: _catalog -\<użytkownika\>. database.windows .net_.
   * Obiekt $catalogDatabaseName jest pobierany z konfiguracji: *tenantcatalog*.
   * Obiekt $shardMapManager jest inicjowany z bazy danych wykazu.
   * Obiekt $shardMap jest inicjowany z mapy fragmentów _tenantcatalog_ w bazie danych wykazu. Obiekt wykazu jest złożony i zwrócony. Jest on używany w skrypcie wyższego poziomu.
* **Oblicz nowy klucz dzierżawy.** Funkcja skrótu służy do tworzenia klucza dzierżawy na podstawie nazwy dzierżawy.
* **Sprawdź, czy klucz dzierżawy już istnieje.** Wykaz jest sprawdzany się upewnić, że klucz jest dostępny.
* **Aprowizacja bazy danych jest wykonywana przy użyciu obiektu New-TenantDatabase.** Użyj F11, aby wkroczyć do jak baza danych zostanie zaprowizowana przy użyciu [szablonu usługi Azure Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md).

    Nazwa bazy danych jest tworzona na podstawie nazwy dzierżawy, aby było jasne, który fragment należy do której dzierżawy. Można również użyć konwencji nazewnictwa inne bazy danych. Szablon usługi Resource Manager tworzy bazę danych dzierżawy, kopiując szablonu bazy danych (_baseTenantDB_) na serwerze wykazu. Alternatywnie możesz utworzyć bazę danych i zainicjowanie jej przez import obiektu bacpac. Lub można wykonywać skrypt inicjowania z dobrze znanej lokalizacji.

    Szablon usługi Resource Manager znajduje się w folderze Modules\Common\ ...\Learning: *tenantdatabasecopytemplate.json*

* **Dodatkowo zainicjowano bazy danych dzierżaw.** Nazwa miejsca (dzierżawa) i typu miejsca zostaną dodane. Możesz również tworzyć inne metody inicjowania w tym miejscu.

* **Baza danych dzierżawy jest zarejestrowana w wykazie.** Jest on zarejestrowany za pomocą *Add-TenantDatabaseToCatalog* przy użyciu klucza dzierżawy. Użyj F11, aby przejść do szczegółów:

    * Baza danych wykazu jest dodawana do mapy fragmentów (listy znanych baz danych).
    * Tworzone jest mapowanie łączące wartość klucza z fragmentem.
    * Dodatkowe metadane dotyczące dzierżawy (nazwa miejsca) jest dodawany do tabeli dzierżaw w wykazie. Tabela dzierżawy nie jest częścią schematu zarządzania fragmentami, a nie jest ona zainstalowana przez EDCL. W tej tabeli przedstawiono, jak baza danych wykazu, można rozszerzyć do obsługi dodatkowych danych specyficznych dla aplikacji.


Po ukończeniu aprowizacji wykonywanie powraca do początkowego *Demo-ProvisionAndCatalog* skryptu. **Zdarzenia** dla nowej dzierżawy w przeglądarce zostanie otwarta strona.

   ![Strona zdarzenia](media/saas-dbpertenant-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Aprowizowanie partii dzierżaw

Tego ćwiczenia jest aprowizowanie partii dzierżaw 17. Zaleca się aprowizowanie tej partii dzierżaw przed uruchomieniem innych samouczków usługi SaaS o nazwie Wingtip Tickets bazy danych dla dzierżawcy. Ma więcej niż kilku baz danych chcesz pracować.

1. W środowisku PowerShell ISE Otwórz... \\Learning Modules\\ProvisionAndCatalog\\*Demo-ProvisionAndCatalog.ps1*. Zmiana *$DemoScenario* parametr 3:

   * **$DemoScenario** = **3**, *aprowizowanie partii dzierżaw*.
2. Aby uruchomić skrypt, naciśnij klawisz F5.

Skrypt wdroży partię dodatkowych dzierżaw. Używa ona [szablonu usługi Azure Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md) który kontroluje partię i deleguje aprowizację każdej bazy danych do dołączonego szablonu. Ten sposób użycia szablonów umożliwia aplikacji Azure Resource Manager pełnienie roli brokera w procesie aprowizacji przy użyciu skryptu. Szablony inicjować obsługę baz danych w równoległych i uchwyt ponownych prób, jeśli to konieczne. Skrypt jest idempotentny, więc jeśli nie powiedzie się lub zatrzymuje się z jakiegokolwiek powodu, uruchom go ponownie.

### <a name="verify-the-batch-of-tenants-that-successfully-deployed"></a>Sprawdź partię dzierżaw, które pomyślnie wdrożona

* W [witryny Azure portal](https://portal.azure.com), przejdź do listy serwerów i Otwórz *tenants1* serwera. Wybierz **baz danych SQL**i sprawdź, czy partia 17 dodatkowych baz danych jest teraz na liście.

   ![Lista baz danych](media/saas-dbpertenant-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>Inne wzorce aprowizacji

Inne wzorce aprowizacji nie są uwzględnione w tym samouczku:

**Wstępne aprowizowanie baz danych**: Wstępne aprowizowanie wzorzec wykorzystuje fakt, że bazy danych w puli elastycznej nie należy dodawać dodatkowych kosztów. Rozliczanie jest dla puli elastycznej nie baz danych. Bezczynna baza danych korzysta z żadnych zasobów. Przez wstępne aprowizowanie baz danych w puli i przydzielanie ich w razie potrzeby można zmniejszyć czas, aby dodać dzierżaw. Liczbę wstępnie aprowizowanych baz danych można dostosować do własnych potrzeb, aby zachować bufor odpowiedni dla oczekiwanego wskaźnika aprowizacji.

**Automatycznej aprowizacji**: We wzorcu automatycznej aprowizacji usługi aprowizacji Inicjuje obsługę serwerów, pul i baz danych automatycznie, zgodnie z potrzebami. Jeśli chcesz, może zawierać wstępne aprowizowanie baz danych w elastycznej puli. Jeśli bazy danych są zamknięta, a następnie usunięty, luki w elastycznych pulach mogą zostać uzupełnione przez usługę aprowizacji. Usługa może być proste lub złożone, takie jak obsługa inicjowania obsługi administracyjnej w wielu regionach i Konfigurowanie replikacji geograficznej dla odzyskiwania po awarii. 

Za pomocą wzorca automatycznej aprowizacji aplikacja klienta lub skrypt przesyła żądanie inicjowania obsługi administracyjnej do kolejki do przetworzenia przez usługę aprowizacji. Następnie sonduje usługę w celu wykrycia ukończenia zadania. Jeśli używana jest wstępna aprowizacja, żądania są obsługiwane szybko. Usługa aprowizuje zastępczej bazy danych w tle.


## <a name="next-steps"></a>Kolejne kroki

W tym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> 
> * Aprowizowanie pojedynczej nowej dzierżawy.
> * Aprowizowanie partii dodatkowych dzierżaw.
> * Przejść do szczegółów aprowizowanie dzierżaw i rejestrowania ich w wykazie.

Spróbuj [samouczek monitorowania wydajności](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* Dodatkowe [samouczków, które są kompilowane w aplikacji SaaS o nazwie Wingtip Tickets bazy danych dla dzierżawcy](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Biblioteka kliencka Elastic Database](sql-database-elastic-database-client-library.md)
* [Debugowanie skryptów w środowisku Windows PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)
