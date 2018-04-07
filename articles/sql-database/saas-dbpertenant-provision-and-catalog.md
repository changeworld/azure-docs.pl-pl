---
title: Udostępnianie nowej dzierżawy w wielodostępnym aplikacji, która używa usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Informacje o sposobie udostępniania i katalogu nowych dzierżaw w wielodostępnych aplikacji SaaS bazy danych SQL Azure
keywords: samouczek usługi sql database
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 4ddb870d0513d6834aacf0964c240260f18df0fd
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="learn-how-to-provision-new-tenants-and-register-them-in-the-catalog"></a>Dowiedz się, jak udostępnić nowi dzierżawcy i zarejestruj je w katalogu

W tym samouczku Dowiedz się jak udostępnianie i wzorce SaaS w katalogu. Możesz też dowiedzieć się, jak jest implementowane w aplikacji bazy danych dla dzierżawy Wingtip biletów SaaS. Tworzenie i zainicjować nowe dzierżawy bazy danych oraz zarejestrować je w katalogu dzierżawy aplikacji. Katalog jest bazy danych, która przechowuje mapowanie między wiele dzierżaw aplikacji SaaS i ich danych. Katalog odgrywa ważną rolę w kierowanie żądań zarządzania prawidłową bazę danych i aplikacji.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]

> * Zapewnienie pojedynczej, nowej dzierżawy.
> * Udostępnić partii dodatkowi dzierżawcy.


Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

* Aplikacja bazy danych dla dzierżawy Wingtip biletów SaaS jest wdrażana. Aby wdrożyć ją w mniej niż 5 minut, zobacz [Wdróż i eksplorowanie aplikacji bazy danych dla dzierżawy Wingtip biletów SaaS](saas-dbpertenant-get-started-deploy.md).
* Zainstalowany jest program Azure PowerShell. Aby uzyskać więcej informacji, zobacz [wprowadzenie do programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-catalog-pattern"></a>Wprowadzenie do wzorca katalogu SaaS

W kopii bazy danych wielodostępnych aplikacji SaaS należy wiedzieć, w którym przechowywane są informacje dla każdego dzierżawcy. We wzorcu katalogu SaaS katalogu bazy danych służy do przechowywania mapowanie między każdego dzierżawcy i bazy danych, w którym są przechowywane ich dane. Ten wzorzec ma zastosowanie, gdy danych dzierżawy jest dystrybuowana do wielu baz danych.

Każdy dzierżawca jest identyfikowany przy użyciu klucza w katalogu, który jest mapowany na lokalizację ich bazy danych. W aplikacji biletów Wingtip klucz został utworzony z skrót nazwy dzierżawcy. Ten program umożliwia aplikacji do utworzenia klucza w nazwę dzierżawcy zawarte w adresie URL aplikacji. Można innych systemów klucza dzierżawy.  

Katalog umożliwia nazwę lub lokalizację bazy danych zostanie zmieniony z minimalnym wpływem na aplikację. W modelu wielodostępnym bazy danych ta funkcja także bierze pod uwagę przeniesienie dzierżawcy między bazami danych. Katalog można również wskazać, czy dzierżawcy lub baza danych jest w trybie offline z powodu konserwacji lub innych działań. Ta funkcja jest przedstawione w [samouczek pojedynczej dzierżawy przywracania](saas-dbpertenant-restore-single-tenant.md).

Katalog również przechowywać dodatkowe dzierżawy lub metadanych bazy danych, takie jak wersja schematu, plan usługi lub umów SLA oferowanych dzierżawcom. Katalog może przechowywać inne informacje, które umożliwiają zarządzanie aplikacjami, dział obsługi klienta lub DevOps. 

Poza aplikacji SaaS katalogu można włączyć narzędzi bazy danych. W przykładowej bazy danych dla dzierżawy Wingtip biletów SaaS, umożliwia włączenia dzierżawy między kwerendę, która jest przedstawione w katalogu [samouczek raportowania Ad hoc](saas-tenancy-cross-tenant-reporting.md). Zarządzanie zadaniami między bazami danych jest przedstawione w [Zarządzanie schematami](saas-tenancy-schema-management.md) i [dzierżawy analytics](saas-tenancy-tenant-analytics.md) samouczki. 

W przykładach SaaS biletów Wingtip katalogu jest implementowane za pomocą funkcji do zarządzania niezależnego fragmentu [biblioteki klienta elastycznej bazy danych (EDCL)](sql-database-elastic-database-client-library.md). EDCL jest dostępna w języku Java i .NET Framework. EDCL włącza aplikację do tworzenia, zarządzania i użyć mapy niezależnych kopii bazy danych. 

Mapy niezależnego fragmentu zawiera listę odłamków (bazy danych) i mapowanie między kluczy (dzierżawcami) i fragmentów. Funkcje EDCL są używane podczas inicjowania obsługi można utworzyć wpisów w mapie niezależnego fragmentu dzierżawy. Są używane w czasie wykonywania przez aplikacje do nawiązania połączenia z poprawną bazą danych. EDCL buforuje informacje o połączeniu, aby zminimalizować ruch związany z bazą danych katalogu i przyspieszenie działania aplikacji. 

> [!IMPORTANT]
> Dane te mapowania są dostępne w bazie danych katalogu, ale *nie go edytować*. Edytowanie danych mapowania tylko przy użyciu biblioteki klienta elastycznej bazy danych interfejsów API. Bezpośrednie manipulowanie dane mapowania ryzyko związane z katalogu uszkodzenia i nie jest obsługiwany.


## <a name="introduction-to-the-saas-provisioning-pattern"></a>Wprowadzenie do inicjowania obsługi administracyjnej wzorzec SaaS

Po dodaniu nowej dzierżawy w aplikacji SaaS, która używa modelu dzierżawy pojedynczej bazy danych, należy udostępnić nową bazę danych dzierżawy. Bazy danych muszą być tworzone w odpowiedniej lokalizacji i warstwy usług. On również musi być zainicjowany odpowiedniego schematu i danych referencyjnych. I musi być zarejestrowana w katalogu w kluczu odpowiedniego dzierżawcy. 

Można różne podejścia do obsługi bazy danych. Można wykonywanie skryptów SQL, wdrażanie pliku bacpac lub skopiuj szablonu bazy danych. 

Inicjowanie obsługi administracyjnej musi być częścią strategii zarządzania schematu bazy danych. Należy się upewnić, że nowe bazy danych są udostępniane z najnowszą schematu. To wymaganie jest przedstawione w [Samouczek zarządzania schematu](saas-tenancy-schema-management.md). 

Bilety Wingtip aplikacji bazy danych na dzierżawcy udostępnia nowi dzierżawcy przez kopiowanie szablonu bazy danych o nazwie _basetenantdb_, który jest wdrożony na serwerze wykazu. Inicjowanie obsługi administracyjnej można zintegrować aplikacji w ramach obsługi rejestracji. On również może być obsługiwany w trybie offline za pomocą skryptów. W tym samouczku Eksploruje inicjowania obsługi administracyjnej za pomocą programu PowerShell. 

Inicjowanie obsługi skryptów kopiowania _basetenantdb_ bazy danych, aby utworzyć nową bazę danych dzierżawy w puli elastycznej. Skrypty następnie zainicjuj bazę danych z informacje specyficzne dla dzierżawy i zarejestruj go w katalogu niezależnych mapy. Bazy danych dzierżawy są podanej nazwy na podstawie nazwy dzierżawy. Ten schemat nazewnictwa jest krytyczną częścią wzorzec. Katalog mapuje klucza dzierżawy Nazwa bazy danych dzięki mogą być używane wszystkie konwencji nazewnictwa. 


## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Pobierz skrypty bazy danych dla dzierżawy aplikacji SaaS biletów Wingtip

SaaS biletów Wingtip skrypty i kod źródłowy aplikacji są dostępne w [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) repozytorium GitHub. Zapoznaj się z [ogólne wskazówki](saas-tenancy-wingtip-app-guidance-tips.md) dla czynności, aby pobrać i odblokować skrypty Wingtip biletów SaaS.


## <a name="provision-and-catalog-detailed-walkthrough"></a>Szczegółowy przewodnik po aprowizacji i wykazie

Aby zrozumieć, jak aplikacja biletów Wingtip implementuje nowej dzierżawy inicjowania obsługi, Dodaj punkt przerwania i wykonaj przepływu pracy, podczas udostępniania dzierżawcy.

1. W programie PowerShell ISE Otwórz... \\Modułów uczenia\\ProvisionAndCatalog\\_ProvisionAndCatalog.ps1 pokaz_ i ustaw następujące parametry:

   * **$TenantName** = nazwa nowego miejsca (na przykład *Bushwillow Blues*).
   * **$VenueType** = jeden z typów wstępnie zdefiniowanych miejscową: _niebieskie, classicalmusic, tańca, jazz, judo, silnikowych racing multipurpose, opera, rockmusic, nożną_.
   * **$DemoScenario** = **1**, *udostępnić pojedynczej dzierżawy*.

2. Aby dodać punkt przerwania, umieść kursor a dowolne miejsce w wierszu, stwierdzający, *nowej dzierżawy "*. Następnie naciśnij klawisz F9.

   ![Punkt przerwania](media/saas-dbpertenant-provision-and-catalog/breakpoint.png)

3. Aby uruchomić skrypt, naciśnij klawisz F5.

4. Po zatrzymaniu wykonywania skryptu na punkt przerwania, naciśnij klawisz F11, aby wkraczać do kodu.

   ![Debugowanie](media/saas-dbpertenant-provision-and-catalog/debug.png)



Śledzenie wykonywanie skryptu przy użyciu **debugowania** opcji menu. Naciśnij klawisz F10 i F11 kroku za pośrednictwem lub do funkcji o nazwie. Aby uzyskać więcej informacji na temat debugowania skryptów programu PowerShell, zobacz [porady dotyczące pracy z i debugowania skryptów programu PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).


Nie trzeba jawnie należy wykonać ten przepływ pracy. Wyjaśniono, jak debugować skrypt.

* **Zaimportuj moduł CatalogAndDatabaseManagement.psm1.** Zapewnia on katalogu i poziomie dzierżawy abstrakcji przez [zarządzania niezależnego fragmentu](sql-database-elastic-scale-shard-map-management.md) funkcji. Ten moduł hermetyzuje znacznie wzorca katalogu i warto eksploracji.
* **Zaimportuj moduł SubscriptionManagement.psm1.** Zawiera on funkcje logowanie do platformy Azure i wyboru subskrypcji platformy Azure, który chcesz współpracować.
* **Uzyskiwanie szczegółowych informacji konfiguracji.** Wkrocz Get-konfiguracji przy użyciu F11 i zobacz, jak określono w konfiguracji aplikacji. Nazwy zasobów i inne wartości specyficzny dla aplikacji są zdefiniowane w tym miejscu. Nie należy zmieniać tych wartości, dopóki nie znasz skryptów.
* **Pobierz obiekt katalogu.** Wkrocz Get-katalog, który Redaguj i zwraca obiekt katalogu, który jest używany w skrypcie wyższego poziomu. Ta funkcja korzysta z funkcji zarządzania niezależnego fragmentu, które są importowane z **AzureShardManagement.psm1**. Obiekt katalogu składa się z następujących elementów:

   * $catalogServerFullyQualifiedName jest tworzony przy użyciu standardowych stem oraz nazwę użytkownika: _katalogu -\<użytkownika\>. database.windows .net_.
   * Obiekt $catalogDatabaseName jest pobierany z konfiguracji: *tenantcatalog*.
   * Obiekt $shardMapManager jest inicjowany z bazy danych wykazu.
   * Obiekt $shardMap jest inicjowany z mapy fragmentów _tenantcatalog_ w bazie danych wykazu. Obiekcie wykazu jest złożony i zwrócony. Jest on używany w skrypcie wyższego poziomu.
* **Oblicz nowego klucza dzierżawy.** Funkcja skrótu służy do tworzenia klucza dzierżawy na podstawie nazwy dzierżawy.
* **Sprawdź, czy klucz dzierżawy już istnieje.** Katalog jest sprawdzane pod kątem upewnij się, że klucz jest dostępny.
* **Aprowizacja bazy danych jest wykonywana przy użyciu obiektu New-TenantDatabase.** Użyj F11 Aby wkraczać do sposobu obsługi administracyjnej bazy danych przy użyciu [szablonu usługi Azure Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md).

    Nazwa bazy danych jest tworzona na podstawie nazwy dzierżawy, aby było jasne, który fragment należy do której dzierżawy. Można też użyć innych konwencji nazewnictwa bazy danych. Szablon usługi Resource Manager tworzy bazę danych dzierżawy przez kopiowanie szablonu bazy danych (_baseTenantDB_) na serwerze wykazu. Alternatywnie można utworzyć bazę danych i zainicjować go przez zaimportowanie pliku bacpac. Lub skrypt inicjujący można wykonywać z dobrze znanej lokalizacji.

    Szablon usługi Resource Manager znajduje się w folderze Modules\Common\ ...\Learning: *tenantdatabasecopytemplate.json*

* **Dzierżawy dalsze jest inicjowana.** Nazwa miejsca (dzierżawcy) i typ miejscową zostaną dodane. Należy również inne inicjowania w tym miejscu.

* **Baza danych dzierżawy jest zarejestrowana w katalogu.** Został on zarejestrowany na *TenantDatabaseToCatalog Dodaj* przy użyciu klucza dzierżawy. Użyj F11 Aby wkraczać do szczegółów:

    * Baza danych wykazu jest dodawana do mapy fragmentów (listy znanych baz danych).
    * Tworzone jest mapowanie łączące wartość klucza z fragmentem.
    * Dodatkowe metadane na temat dzierżawy (właściwość name) jest dodawane do tabeli dzierżaw w katalogu. W tabeli dzierżawcy nie jest częścią schematu zarządzania niezależnego fragmentu, a nie została zainstalowana przez EDCL. W tej tabeli przedstawiono, jak baza danych katalogu można rozszerzyć do obsługi dodatkowe dane specyficzne dla aplikacji.


Po ukończeniu inicjowania obsługi administracyjnej, wykonanie zwraca oryginalnej *ProvisionAndCatalog pokaz* skryptu. **Zdarzenia** dla nowej dzierżawy w przeglądarce zostanie otwarta strona.

   ![Strona zdarzenia](media/saas-dbpertenant-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Zainicjuj obsługę partii dzierżawcy

Tego ćwiczenia inicjuje partii 17 dzierżaw. Zaleca się udostępniania tej partii dzierżaw przed rozpoczęciem samouczków SaaS biletów Wingtip bazy danych dla dzierżawy. Istnieje więcej niż kilka baz danych do pracy z.

1. W programie PowerShell ISE Otwórz... \\Modułów szkoleniowych\\ProvisionAndCatalog\\*ProvisionAndCatalog.ps1 pokaz*. Zmień *$DemoScenario* parametr 3:

   * **$DemoScenario** = **3**, *udostępnić partii dzierżaw*.
2. Aby uruchomić skrypt, naciśnij klawisz F5.

Skrypt wdroży partię dodatkowych dzierżaw. Używa [szablonu usługi Azure Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md) steruje partii i deleguje Inicjowanie obsługi wszystkich baz danych do połączonego szablonu. Ten sposób użycia szablonów umożliwia aplikacji Azure Resource Manager pełnienie roli brokera w procesie aprowizacji przy użyciu skryptu. Szablony udostępnić baz danych w równolegle i uchwytem ponownych prób, jeśli to konieczne. Skrypt jest idempotentności, więc jeśli nie powiedzie się lub zatrzymuje jakiejkolwiek przyczyny, uruchom go ponownie.

### <a name="verify-the-batch-of-tenants-that-successfully-deployed"></a>Sprawdź partii dzierżawcami, które pomyślnie wdrożone

* W [portalu Azure](https://portal.azure.com), przejdź do listy serwerów i Otwórz *tenants1* serwera. Wybierz **baz danych**i sprawdź, czy partii 17 dodatkowych baz danych jest teraz na liście.

   ![Listy baz danych](media/saas-dbpertenant-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>Inne wzorce aprowizacji

Inne inicjowania obsługi administracyjnej wzorce nie są uwzględnione w tym samouczku:

**Wstępne inicjowanie obsługi baz danych**: wzorzec wstępnego inicjowania obsługi administracyjnej wykorzystuje fakt, że bazy danych w puli elastycznej nie należy dodawać z żadnymi dodatkowymi kosztami. Rozliczenia jest przeznaczony dla puli elastycznej nie baz danych. Bezczynności baz danych używać żadnych zasobów. Dzięki wstępnie inicjowania obsługi administracyjnej bazy danych w puli i przydzielanie je w razie potrzeby można zmniejszyć dodaniu dzierżaw. Wstępnie tworzyć baz danych można dostosować do własnych potrzeb, aby zachować odpowiedni w przypadku inicjowania obsługi administracyjnej oczekiwanego wskaźnika buforu.

**Automatyczne inicjowanie obsługi administracyjnej**: we wzorcu inicjowania obsługi automatycznego inicjowania obsługi usługi przepisy serwerów, pul i baz danych automatycznie, zgodnie z potrzebami. Jeśli chcesz, mogą obejmować wstępnie udostępniania bazy danych w puli elastycznej. Jeśli bazy danych są zlikwidowana i usuwane, luk w pule elastyczne może zostać wypełniony przez usługę inicjowania obsługi administracyjnej. Takie usługi może być prosty lub złożonych, takie jak obsługa inicjowania obsługi administracyjnej na wielu różnych obszarach geograficznych i konfigurowanie — replikacja geograficzna odzyskiwania po awarii. 

Za pomocą wzorca automatycznego inicjowania obsługi administracyjnej klienta aplikacji lub skryptu przesyła żądanie inicjowania obsługi administracyjnej do kolejki być przetworzone przez usługę inicjowania obsługi administracyjnej. Następnie sonduje usługę, aby określić ukończenia. Jeśli używana jest wstępnie inicjowania obsługi, żądania są obsługiwane szybko. Usługa inicjuje zastąpienia bazy danych w tle.


## <a name="next-steps"></a>Kolejne kroki

W tym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]

> * Zapewnienie pojedynczej, nowej dzierżawy.
> * Udostępnić partii dodatkowi dzierżawcy.
> * Krok do szczegółów inicjowania obsługi administracyjnej dzierżawcy i rejestrując je do katalogu.

Spróbuj [samouczek monitorowania wydajności](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* Dodatkowe [samouczki, w aplikacji bazy danych dla dzierżawy Wingtip biletów SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Biblioteka kliencka Elastic Database](sql-database-elastic-database-client-library.md)
* [Debugowanie skryptów w środowisku Windows PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)
