---
title: Aprowizuj nowych dzierżaw w aplikacji wielodostępne
description: Dowiedz się, jak aprowizować i katalogować nowych dzierżaw w wielodostępnej aplikacji SaaS usługi Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: 6ec8f8835e925663fc6ac21a6eb1df09d6927109
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74132104"
---
# <a name="learn-how-to-provision-new-tenants-and-register-them-in-the-catalog"></a>Dowiedz się, jak aprowizować nowych dzierżawców i rejestrować ich w katalogu

W tym samouczku dowiesz się, jak aprowizować i katalogować wzorce SaaS. Dowiesz się również, jak są one implementowane w aplikacji bazy danych SaaS Wingtip na dzierżawcę. Tworzenie i inicjowanie nowych baz danych dzierżawy i zarejestrować je w katalogu dzierżawy aplikacji. Katalog jest bazą danych, która przechowuje mapowanie między wieloma dzierżawami aplikacji SaaS a ich danymi. Katalog odgrywa ważną rolę w kierowaniu żądań aplikacji i zarządzania do poprawnej bazy danych.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Aprowizuj jedną nową dzierżawę.
> * Aprowizuj partię dodatkowych dzierżaw.


Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

* Wdrożono aplikację bazy danych SaaS dla wingtip. Aby wdrożyć go w mniej niż pięć minut, zobacz [Wdrażanie i eksplorowanie aplikacji bazy danych SaaS Wingtip na dzierżawę.](saas-dbpertenant-get-started-deploy.md)
* Zainstalowany jest program Azure PowerShell. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-catalog-pattern"></a>Wprowadzenie do wzorca katalogu SaaS

W bazie danych multitenant aplikacji SaaS, ważne jest, aby wiedzieć, gdzie są przechowywane informacje dla każdej dzierżawy. We wzorcu katalogu SaaS baza danych katalogu jest używana do przechowywania mapowania między każdą dzierżawcą a bazą danych, w której przechowywane są ich dane. Ten wzorzec ma zastosowanie, gdy dane dzierżawy są dystrybuowane w wielu bazach danych.

Każdy dzierżawca jest identyfikowany przez klucz w katalogu, który jest mapowany do lokalizacji ich bazy danych. W aplikacji Bilety Wingtip klucz jest tworzony z skrótu nazwy dzierżawcy. Ten schemat umożliwia aplikacji do konstruowania klucza z nazwy dzierżawy zawarte w adresie URL aplikacji. Można użyć innych schematów klucza dzierżawy.

Katalog umożliwia zmianę nazwy lub lokalizacji bazy danych przy minimalnym wpływie na aplikację. W modelu bazy danych wielodostępnych ta funkcja umożliwia również przenoszenie dzierżawy między bazami danych. Katalog może również służyć do wskazania, czy dzierżawca lub bazy danych jest w trybie offline do konserwacji lub innych akcji. Ta funkcja jest badana w [samouczku Przywracanie pojedynczej dzierżawy](saas-dbpertenant-restore-single-tenant.md).

Katalog może również przechowywać dodatkowe metadane dzierżawy lub bazy danych, takie jak wersja schematu, plan usługi lub ławy SLA oferowane dzierżawcom. Katalog może przechowywać inne informacje, które umożliwiają zarządzanie aplikacjami, obsługę klienta lub DevOps.

Poza aplikacją SaaS katalog może włączyć narzędzia bazy danych. W próbce bazy danych SaaS Wingtip Tickets na dzierżawcę katalog jest używany do włączania kwerendy między dzierżawą, która jest badana w [samouczku raportowania ad hoc](saas-tenancy-cross-tenant-reporting.md). Zarządzanie zadaniami między bazami danych jest badane w [samouczkach zarządzanie schematem](saas-tenancy-schema-management.md) i [analiza dzierżawy.](saas-tenancy-tenant-analytics.md)

W przykładach SaaS biletów Wingtip katalog jest implementowany przy użyciu funkcji zarządzania fragmentami [biblioteki klienta elastycznej bazy danych (EDCL).](sql-database-elastic-database-client-library.md) EDCL jest dostępny w języku Java i .NET Framework. EDCL umożliwia aplikacji do tworzenia, zarządzania i korzystania z mapy niezależnego fragmentu kopii bazy danych.

Mapa niezależnego fragmentu zawiera listę fragmentów (baz danych) i mapowanie między kluczami (dzierżawców) i fragmentów. Funkcje EDCL są używane podczas inicjowania obsługi administracyjnej dzierżawy do tworzenia wpisów na mapie niezależnego fragmentu. Są one używane w czasie wykonywania przez aplikacje do łączenia się z poprawną bazą danych. EDCL buforuje informacje o połączeniu, aby zminimalizować ruch do bazy danych katalogu i przyspieszyć aplikację.

> [!IMPORTANT]
> Dane mapowania są dostępne w bazie danych wykazu, ale *nie edytuj ich.* Edytuj dane mapowania przy użyciu tylko interfejsów API biblioteki klienta elastycznej bazy danych. Bezpośrednie manipulowanie danymi mapowania grozi uszkodzeniem katalogu i nie jest obsługiwane.


## <a name="introduction-to-the-saas-provisioning-pattern"></a>Wprowadzenie do wzorca inicjowania obsługi administracyjnej SaaS

Po dodaniu nowej dzierżawy w aplikacji SaaS, która używa modelu bazy danych z jedną dzierżawą, należy aprowizować nową bazę danych dzierżawy. Baza danych musi zostać utworzona w odpowiedniej warstwie lokalizacji i usługi. Należy również zainicjować przy pomocą odpowiedniego schematu i danych referencyjnych. I musi być zarejestrowany w katalogu pod odpowiednim kluczem dzierżawy.

Można użyć różnych podejść do inicjowania obsługi administracyjnej bazy danych. Można wykonać skrypty SQL, wdrożyć bacpac lub skopiować bazę danych szablonów.

Inicjowanie obsługi administracyjnej bazy danych musi być częścią strategii zarządzania schematem. Należy upewnić się, że nowe bazy danych są aprowidzone z najnowszym schematem. To wymaganie jest badane w [samouczku zarządzania schematem](saas-tenancy-schema-management.md).

Aplikacja Wingtip Tickets na dzierżawcę wprowadza nowe przepisy dla dzierżaw, kopiując bazę danych szablonu o nazwie _basetenantdb_, która jest wdrażana na serwerze katalogu. Inicjowanie obsługi administracyjnej można zintegrować z aplikacją w ramach środowiska rejestracji. Może być również obsługiwany w trybie offline przy użyciu skryptów. W tym samouczku eksploruje inicjowanie obsługi administracyjnej przy użyciu programu PowerShell.

Skrypty inicjowania obsługi administracyjnej kopiują _bazocha_ bazy danych bazy danych w celu utworzenia nowej bazy danych dzierżawy w puli elastycznej. Baza danych dzierżawy jest tworzona na serwerze dzierżawy mapowanym na _nowy_ alias DNS. Ten alias zachowuje odwołanie do serwera używanego do aprowizowania nowych dzierżaw i jest aktualizowany w celu wskazania serwera dzierżawy odzyskiwania w samouczkach odzyskiwania po awarii[(DR przy użyciu georestore](saas-dbpertenant-dr-geo-restore.md), [DR przy użyciu georeplikacji](saas-dbpertenant-dr-geo-replication.md)). Skrypty następnie zainicjować bazę danych z informacjami specyficznymi dla dzierżawy i zarejestrować go w mapie niezależnego fragmentu katalogu. Bazy danych dzierżawy są podane nazwy na podstawie nazwy dzierżawy. Ten schemat nazewnictwa nie jest krytyczną częścią wzorca. Katalog mapuje klucz dzierżawy do nazwy bazy danych, dzięki czemu można użyć dowolnej konwencji nazewnictwa.


## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Pobierz skrypty aplikacji bazy danych SaaS dla bazy danych Wingtip na dzierżawcę

Skrypty SaaS i kod źródłowy aplikacji Wingtip Tickets są dostępne w repozytorium [GitHub WingtipTicketsSaaS-DbPerTenant.](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) Zapoznaj się z [ogólnymi wskazówkami dotyczącymi](saas-tenancy-wingtip-app-guidance-tips.md) pobierania i odblokowywania skryptów SaaS biletów Wingtip.


## <a name="provision-and-catalog-detailed-walkthrough"></a>Szczegółowy przewodnik po aprowizacji i wykazie

Aby zrozumieć, jak aplikacja Wingtip Tickets implementuje nowe inicjowanie obsługi administracyjnej dzierżawy, dodaj punkt przerwania i postępuj zgodnie z przepływem pracy podczas inicjowania obsługi administracyjnej dzierżawy.

1. W programie PowerShell ISE otwórz ... \\Moduły\\szkoleniowe ProvisionAndCatalog\\_Demo-ProvisionAndCatalog.ps1_ i ustawić następujące parametry:

   * **$TenantName** = nazwa nowego miejsca (na przykład *Bushwillow Blues*).
   * **$VenueType** = jeden z predefiniowanych typów miejsc: _blues, classicalmusic, dance, jazz, judo, wyścigi samochodowe, wielofunkcyjny, operowy, rockmusic, piłka nożna_.
   * **$DemoScenario****1**, *Przepisaj jednego dzierżawcę*. = 

2. Aby dodać punkt przerwania, umieść kursor w dowolnym miejscu w wierszu z napisem *New-Tenant .* Następnie naciśnij klawisz F9.

   ![Punkt przerwania](media/saas-dbpertenant-provision-and-catalog/breakpoint.png)

3. Aby uruchomić skrypt, naciśnij klawisz F5.

4. Po zatrzymaniu wykonywania skryptu w punkcie przerwania naciśnij klawisz F11, aby przejść do kodu.

   ![Debugowanie](media/saas-dbpertenant-provision-and-catalog/debug.png)



Śledzenie wykonania skryptu przy użyciu opcji menu **debugowania.** Naciśnij klawisze F10 i F11, aby przejść przez lub do wywoływanych funkcji. Aby uzyskać więcej informacji na temat debugowania skryptów programu PowerShell, zobacz [Porady dotyczące pracy z skryptami programu PowerShell i debugowania](https://docs.microsoft.com/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise).


Nie trzeba jawnie śledzić tego przepływu pracy. Wyjaśniono, jak debugować skrypt.

* **Zaimportuj moduł CatalogAndDatabaseManagement.psm1.** Zapewnia katalog i abstrakcji na poziomie dzierżawy za pomocą funkcji [zarządzania fragmentami.](sql-database-elastic-scale-shard-map-management.md) Ten moduł hermetyzuje wiele wzorca katalogu i warto go zbadać.
* **Zaimportuj moduł SubscriptionManagement.psm1.** Zawiera funkcje logowania się na platformie Azure i wybierania subskrypcji platformy Azure, z którą chcesz pracować.
* **Uzyskaj szczegóły konfiguracji.** Krok do get-konfiguracji przy użyciu F11 i zobacz, jak określono konfigurację aplikacji. Nazwy zasobów i inne wartości specyficzne dla aplikacji są zdefiniowane w tym miejscu. Nie zmieniaj tych wartości, dopóki nie znasz skryptów.
* **Pobierz obiekt katalogu.** Krok do get-catalog, który komponuje i zwraca obiekt katalogu, który jest używany w skrypcie wyższego poziomu. Ta funkcja używa funkcji zarządzania fragmentami, które są importowane z **pliku AzureShardManagement.psm1**. Obiekt katalogu składa się z następujących elementów:

   * $catalogServerFullyQualifiedName jest skonstruowany przy użyciu standardowego łody i nazwy użytkownika: _katalog-\<\>user.database.windows .net_.
   * Obiekt $catalogDatabaseName jest pobierany z konfiguracji: *tenantcatalog*.
   * Obiekt $shardMapManager jest inicjowany z bazy danych wykazu.
   * Obiekt $shardMap jest inicjowany z mapy fragmentów _tenantcatalog_ w bazie danych wykazu. Obiekt katalogu jest skomponowany i zwracany. Jest on używany w skrypcie wyższego poziomu.
* **Oblicz nowy klucz dzierżawy.** Funkcja skrótu służy do tworzenia klucza dzierżawy na podstawie nazwy dzierżawy.
* **Sprawdź, czy klucz dzierżawy już istnieje.** Katalog jest sprawdzany, aby upewnić się, że klucz jest dostępny.
* **Aprowizacja bazy danych jest wykonywana przy użyciu obiektu New-TenantDatabase.** Użyj F11, aby przejść do sposobu aprowizowania bazy danych przy użyciu [szablonu usługi Azure Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md).

    Nazwa bazy danych jest tworzona na podstawie nazwy dzierżawy, aby było jasne, który fragment należy do której dzierżawy. Można również użyć innych konwencji nazewnictwa bazy danych. Szablon Menedżera zasobów tworzy bazę danych dzierżawy, kopiując bazę danych szablonu _(baseTenantDB)_ na serwerze wykazu. Alternatywnie można utworzyć bazę danych i zainicjować ją, importując bacpac. Można też wykonać skrypt inicjowania ze znanej lokalizacji.

    Szablon Menedżera zasobów znajduje się w folderze ...\Learning Modules\Common\: *tenantdatabasecopytemplate.json*

* **Baza danych dzierżawy jest dalej inicjowane.** Nazwa miejsca (dzierżawy) i typ miejsca są dodawane. Można również wykonać inne inicjowanie tutaj.

* **Baza danych dzierżawy jest zarejestrowana w katalogu.** Jest zarejestrowany w *Add-TenantDatabaseToCatalog* przy użyciu klucza dzierżawy. Użyj klawisza F11, aby wejść do funkcji:

    * Baza danych wykazu jest dodawana do mapy fragmentów (listy znanych baz danych).
    * Tworzone jest mapowanie łączące wartość klucza z fragmentem.
    * Dodatkowe metadane dotyczące dzierżawy (nazwa miejsca) są dodawane do tabeli Dzierżawy w katalogu. Tabela Dzierżawy nie jest częścią schematu zarządzania fragmentami i nie jest zainstalowana przez listę EDCL. W tej tabeli przedstawiono, jak baza danych katalogu można rozszerzyć do obsługi dodatkowych danych specyficznych dla aplikacji.


Po zakończeniu inicjowania obsługi administracyjnej wykonanie powraca do oryginalnego skryptu *Demo-ProvisionAndCatalog.* Zdarzenia **Events** strona otwiera się dla nowej dzierżawy w przeglądarce.

   ![Strona Wydarzenia](media/saas-dbpertenant-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Aprowizuj partię dzierżawców

To ćwiczenie rezerwuje partię 17 najemców. Zaleca się, aby udostępnić tę partię dzierżawy przed uruchomieniem innych Wingtip Bilety SaaS bazy danych na dzierżawę samouczki. Istnieje więcej niż tylko kilka baz danych do pracy.

1. W programie PowerShell ISE otwórz ... \\Moduły\\szkoleniowe ProvisionAndCatalog\\*Demo-ProvisionAndCatalog.ps1*. Zmień parametr *$DemoScenario* na 3:

   * **$DemoScenario****3**, *Udostępnić partię najemców*. = 
2. Aby uruchomić skrypt, naciśnij klawisz F5.

Skrypt wdroży partię dodatkowych dzierżaw. Używa [szablonu usługi Azure Resource Manager,](../azure-resource-manager/resource-manager-template-walkthrough.md) który steruje partii i deleguje inicjowania obsługi administracyjnej każdej bazy danych do szablonu połączonego. Ten sposób użycia szablonów umożliwia aplikacji Azure Resource Manager pełnienie roli brokera w procesie aprowizacji przy użyciu skryptu. Szablony aprowizacji baz danych równolegle i obsługi ponownych prób, w razie potrzeby. Skrypt jest idempotentny, więc jeśli nie powiedzie się lub zatrzyma się z jakiegokolwiek powodu, uruchom go ponownie.

### <a name="verify-the-batch-of-tenants-that-successfully-deployed"></a>Sprawdź partię dzierżawców, którzy pomyślnie wdrożyli

* W [witrynie Azure portal](https://portal.azure.com)przejdź do listy serwerów i otwórz serwer *tenants1.* Wybierz **bazy danych SQL**i sprawdź, czy partia 17 dodatkowych baz danych znajduje się teraz na liście.

   ![Lista baz danych](media/saas-dbpertenant-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>Inne wzorce aprowizacji

Inne wzorce inicjowania obsługi administracyjnej nie uwzględnione w tym samouczku:

**Wstępne inicjowanie obsługi administracyjnej baz danych:** wzorzec wstępnej inicjowania obsługi administracyjnej wykorzystuje fakt, że bazy danych w puli elastycznej nie dodają dodatkowych kosztów. Rozliczenia są dla puli elastycznej, a nie baz danych. Bezczynne bazy danych nie zużywają żadnych zasobów. Poprzez wstępne inicjowanie obsługi administracyjnej baz danych w puli i przydzielanie ich w razie potrzeby, można skrócić czas dodawania dzierżaw. Liczba wstępnie aprowizowanych baz danych można dostosować w razie potrzeby, aby zachować bufor odpowiedni dla przewidywanego wskaźnika inicjowania obsługi administracyjnej.

**Automatyczne inicjowanie obsługi administracyjnej**: W wzorcu automatycznej inicjowania obsługi administracyjnej usługa inicjowania obsługi administracyjnej automatycznie udostępnia serwery, pule i bazy danych, zgodnie z potrzebami. Jeśli chcesz, możesz dołączyć wstępnie inicjowania obsługi administracyjnej baz danych w pulach elastycznych. Jeśli bazy danych są likwidowane i usuwane, luki w pulach elastycznych mogą być wypełniane przez usługę inicjowania obsługi administracyjnej. Taka usługa może być prosta lub złożona, na przykład obsługa inicjowania obsługi administracyjnej w wielu lokalizacjach geograficznych i konfigurowanie replikacji geograficznej w celu odzyskiwania po awarii.

Za pomocą wzorca automatycznej inicjowania obsługi administracyjnej aplikacja kliencka lub skrypt przesyła żądanie inicjowania obsługi administracyjnej do kolejki do przetworzenia przez usługę inicjowania obsługi administracyjnej. Następnie sonduje usługę, aby określić ukończenie. Jeśli używane jest wstępne inicjowanie obsługi administracyjnej, żądania są obsługiwane szybko. Usługa udostępnia zastępczą bazę danych w tle.


## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Aprowizuj jedną nową dzierżawę.
> * Aprowizuj partię dodatkowych dzierżaw.
> * Krok do szczegółów inicjowania obsługi administracyjnej dzierżawców i rejestrowania ich w katalogu.

Wypróbuj [samouczek monitorowania wydajności](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* Dodatkowe [samouczki, które opierają się na aplikacji bazy danych SaaS Wingtip Tickets na dzierżawcę](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Biblioteka kliencka Elastic Database](sql-database-elastic-database-client-library.md)
* [Skrypty debugowania w programie Ise programu Windows PowerShell](https://docs.microsoft.com/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise)
