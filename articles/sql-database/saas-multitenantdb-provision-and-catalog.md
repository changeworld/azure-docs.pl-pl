---
title: Udostępnianie w saaS wielodostępnych
description: Dowiedz się, jak aprowizować i katalogować nowych dzierżaw w wielodostępnej aplikacji SaaS usługi Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib,andrela,stein
ms.date: 09/24/2018
ms.openlocfilehash: 4ea18ee23d845b2d16209b23de14dc3cd70aaa59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74133154"
---
# <a name="provision-and-catalog-new-tenants-in-a-saas-application-using-a-sharded-multi-tenant-azure-sql-database"></a>Aprowizowanie i katalog nowych dzierżaw w aplikacji SaaS przy użyciu podzielonej wielodostępniesięcowej bazy danych SQL platformy Azure

W tym artykule opisano inicjowanie obsługi administracyjnej i katalogowanie nowych dzierżawców w modelu *bazy danych podzielonej na fragmenty wielu dzierżawców* lub wzorzec.

Ten artykuł ma dwie główne części:

- [Omówienie koncepcyjne](#goto_2_conceptual) inicjowania obsługi administracyjnej i katalogowania nowych dzierżaw.

- [Samouczek,](#goto_1_tutorial) który wyróżnia kod skryptu programu PowerShell, który wykonuje inicjowanie obsługi administracyjnej i katalogowanie.
  - Samouczek używa aplikacji SaaS Wingtip Tickets, dostosowanej do wzorca bazy danych podzielonej na fragmenty wielu dzierżawców.

<a name="goto_2_conceptual"/>

## <a name="database-pattern"></a>Wzorzec bazy danych

W tej sekcji oraz kilka innych, które następują, omówiono pojęcia wzorca podzielonej bazy danych z wieloma dzierżawcami.

W tym modelu podzielonej wielodostępnej schematy tabel wewnątrz każdej bazy danych zawierają klucz dzierżawy w kluczu podstawowym tabel, które przechowują dane dzierżawy. Klucz dzierżawy umożliwia każdej pojedynczej bazy danych do przechowywania 0, 1 lub wielu dzierżawców. Korzystanie z podzielonych baz danych ułatwia systemowi aplikacji do obsługi bardzo dużej liczby dzierżaw. Wszystkie dane dla jednej dzierżawy są przechowywane w jednej bazie danych. Duża liczba dzierżaw są dystrybuowane w wielu podzielonych baz danych. Baza danych wykazu przechowuje mapowanie każdej dzierżawy do bazy danych.

#### <a name="isolation-versus-lower-cost"></a>Izolacja a niższe koszty

Dzierżawca, który ma bazę danych dla siebie korzysta z zalet izolacji. Dzierżawca może mieć bazy danych przywrócone do wcześniejszej daty bez ograniczenia przez wpływ na innych dzierżawców. Wydajność bazy danych można dostroić do optymalizacji dla jednej dzierżawy, ponownie bez konieczności kompromisów z innymi dzierżawami. Problem polega na tym, że izolacja kosztuje więcej niż kosztuje udostępnienie bazy danych innym dzierżawcom.

Gdy nowa dzierżawa jest aprowizowana, może udostępnić bazę danych innym dzierżawcom lub można ją umieścić w nowej bazie danych. Później możesz zmienić zdanie i przenieść bazę danych do innej sytuacji.

Bazy danych z wieloma dzierżawcami i pojedynczymi dzierżawcami są mieszane w tej samej aplikacji SaaS, aby zoptymalizować koszt lub izolację dla każdej dzierżawy.

   ![Podzielona wielodostępna aplikacja bazy danych z katalogiem dzierżawy](media/saas-multitenantdb-provision-and-catalog/MultiTenantCatalog.png)

## <a name="tenant-catalog-pattern"></a>Wzorzec katalogu dzierżawy

Jeśli masz dwie lub więcej baz danych, które zawierają co najmniej jedną dzierżawę, aplikacja musi mieć sposób, aby odkryć, która baza danych przechowuje dzierżawy bieżącego zainteresowania. Baza danych katalogu przechowuje to mapowanie.

#### <a name="tenant-key"></a>Klucz dzierżawy

Dla każdej dzierżawy aplikacja Wingtip można wyprowadzić unikatowy klucz, który jest kluczem dzierżawy. Aplikacja wyodrębnia nazwę dzierżawy z adresu URL strony sieci Web. Aplikacja skróty nazwę, aby uzyskać klucz. Aplikacja używa klucza, aby uzyskać dostęp do katalogu. Katalog odsyła informacje o bazie danych, w której dzierżawy jest przechowywany. Aplikacja używa informacji o bazie danych do nawiązania połączenia. Można również użyć innych schematów klucza dzierżawy.

Za pomocą katalogu umożliwia nazwę lub lokalizację bazy danych dzierżawy, które mają być zmieniane po inicjowaniu obsługi administracyjnej bez zakłócania aplikacji. W modelu bazy danych z wieloma dzierżawcami katalog może przenosić dzierżawcę między bazami danych.

#### <a name="tenant-metadata-beyond-location"></a>Metadane dzierżawy poza lokalizacją

Katalog można również wskazać, czy dzierżawca jest w trybie offline do konserwacji lub innych akcji. Katalog można rozszerzyć na dodatkowe metadane dzierżawy lub bazy danych, takie jak następujące elementy:
- Warstwa usługi lub wersja bazy danych.
- Wersja schematu bazy danych.
- Nazwa dzierżawy i jego umowa SLA (umowa dotyczącą poziomu usług).
- Informacje umożliwiające zarządzanie aplikacjami, obsługę klienta lub procesy devops.

Katalog może również służyć do włączania raportowania między dzierżawami, zarządzania schematem i wyodrębniania danych do celów analitycznych.

### <a name="elastic-database-client-library"></a>Biblioteka klienta elastycznej bazy danych

W Wingtip katalog jest implementowany w bazie danych *tenantcatalog.* *Tenantcatalog* jest tworzony przy użyciu funkcji zarządzania fragmentami [biblioteki klienta elastycznej bazy danych (EDCL).](sql-database-elastic-database-client-library.md) Biblioteka umożliwia aplikacji tworzenie, zarządzanie i używanie *mapy niezależnego fragmentu,* która jest przechowywana w bazie danych. Mapa niezależnego fragmentu odsyła klucz dzierżawy z jego niezależnego fragmentu, co oznacza jego podzielonej bazy danych.

Podczas inicjowania obsługi administracyjnej dzierżawy funkcje EDCL mogą być używane z aplikacji lub skryptów programu PowerShell do tworzenia wpisów na mapie niezależnego fragmentu. Później funkcje EDCL mogą być używane do łączenia się z właściwą bazą danych. EDCL buforuje informacje o połączeniu, aby zminimalizować ruch w bazie danych katalogu i przyspieszyć proces nawiązywania połączenia.

> [!IMPORTANT]
> *Nie* należy edytować danych w bazie danych katalogu za pośrednictwem bezpośredniego dostępu! Aktualizacje bezpośrednie nie są obsługiwane ze względu na wysokie ryzyko uszkodzenia danych. Zamiast tego należy edytować dane mapowania przy użyciu tylko interfejsów API EDCL.

## <a name="tenant-provisioning-pattern"></a>Wzorzec inicjowania obsługi administracyjnej dzierżawy

#### <a name="checklist"></a>Lista kontrolna

Aby aprowizować nową dzierżawę do istniejącej udostępnionej bazy danych, udostępnionej bazy danych należy zadać następujące pytania:
- Czy ma wystarczająco dużo miejsca dla nowego dzierżawcy?
- Czy ma tabele z niezbędnymi danymi referencyjnymi dla nowej dzierżawy, czy można dodać dane?
- Czy ma odpowiednią odmianę schematu podstawowego dla nowej dzierżawy?
- Czy znajduje się w odpowiedniej lokalizacji geograficznej w pobliżu nowego dzierżawcy?
- Czy jest to właściwa warstwa usług dla nowej dzierżawy?

Jeśli chcesz, aby nowa dzierżawa została wyizolowana we własnej bazie danych, można go utworzyć, aby spełnić specyfikacje dzierżawy.

Po zakończeniu inicjowania obsługi administracyjnej należy zarejestrować dzierżawcę w katalogu. Na koniec mapowanie dzierżawy można dodać do odwołania odpowiedniego niezależnego fragmentu.

#### <a name="template-database"></a>Baza danych szablonów

Aprowizuj bazę danych, wykonując skrypty SQL, wdrażając bacpac lub kopiując bazę danych szablonu. Aplikacje Wingtip kopiują bazę danych szablonów w celu utworzenia nowych baz danych dzierżawy.

Jak każda aplikacja, Wingtip będzie ewoluować w czasie. Czasami Wingtip będzie wymagać zmian w bazie danych. Zmiany mogą obejmować następujące elementy:
- Nowy lub zmieniony schemat.
- Nowe lub zmienione dane referencyjne.
- Rutynowe zadania konserwacji bazy danych w celu zapewnienia optymalnej wydajności aplikacji.

W przypadku aplikacji SaaS zmiany te muszą zostać wprowadzone w sposób skoordynowany — potencjalnie w bardzo wielu bazach danych dzierżaw. Aby te zmiany były w przyszłych bazach danych dzierżawy, muszą zostać włączone do procesu inicjowania obsługi administracyjnej. To wyzwanie jest badane dalej w [tutorialu zarządzania schematem](saas-tenancy-schema-management.md).

#### <a name="scripts"></a>Scripts

Skrypty inicjowania obsługi administracyjnej dzierżawy w tym samouczku obsługują oba następujące scenariusze:
- Inicjowanie obsługi administracyjnej dzierżawy do istniejącej bazy danych udostępnionej innym dzierżawcom.
- Inicjowanie obsługi administracyjnej dzierżawy do własnej bazy danych.

Dane dzierżawy są następnie inicjowane i rejestrowane na mapie niezależnego fragmentu katalogu. W przykładowej aplikacji bazy danych, które zawierają wiele dzierżaw, otrzymują nazwę rodzajową, taką jak *tenants1* lub *tenants2.* Bazy danych, które zawierają jedną dzierżawę są podane nazwę dzierżawy. Określone konwencje nazewnictwa używane w przykładzie nie są krytyczną częścią wzorca, ponieważ użycie katalogu umożliwia przypisanie dowolnej nazwy do bazy danych.

<a name="goto_1_tutorial"/>

## <a name="tutorial-begins"></a>Rozpoczyna się samouczek

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Aprowizowanie dzierżawy do wielodostępnych bazy danych
> * Aprowizowanie dzierżawy w bazie danych z jedną dzierżawą
> * Aprowizowanie partii dzierżawców do baz danych wielodostępnych i jednodostępnych
> * Rejestrowanie mapowania bazy danych i dzierżawy w katalogu

#### <a name="prerequisites"></a>Wymagania wstępne

Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

- Zainstalowany jest program Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

- Wdrożono aplikację Wingtip Tickets SaaS Multi-tenant Database. Aby wdrożyć w mniej niż pięć minut, zobacz [Wdrażanie i eksplorowanie aplikacji Bazy danych wielodostępnych biletów Wingtip](saas-multitenantdb-get-started-deploy.md)

- Pobierz skrypty Wingtip i kod źródłowy:
    - Skrypty bazy danych wielodostępnych Wingtip Tickets SaaS i kod źródłowy aplikacji są dostępne w repozytorium [GitHub WingtipTicketsSaaS-MultitenantDB.](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB)
    - Zapoznaj się z [ogólnymi wskazówkami dotyczącymi](saas-tenancy-wingtip-app-guidance-tips.md) pobierania i odblokowywania skryptów Wingtip.

## <a name="provision-a-tenant-into-a-database-shared-with-other-tenants"></a>Aprowizowanie dzierżawy do bazy danych *udostępnionej* innym dzierżawcom

W tej sekcji zobaczysz listę głównych akcji inicjowania obsługi administracyjnej, które są podejmowane przez skrypty programu PowerShell. Następnie należy użyć debugera ISE programu PowerShell, aby przejść przez skrypty, aby wyświetlić akcje w kodzie.

#### <a name="major-actions-of-provisioning"></a>Główne działania związane z tworzeniem rezerw

Poniżej przedstawiono kluczowe elementy przepływu pracy inicjowania obsługi administracyjnej, który krok po kroku:

- **Oblicz nowy klucz dzierżawy:** Funkcja mieszania służy do tworzenia klucza dzierżawy z nazwy dzierżawy.
- **Sprawdź, czy klucz dzierżawy już istnieje**: Katalog jest sprawdzany, aby upewnić się, że klucz nie został jeszcze zarejestrowany.
- **Inicjuj dzierżawcę w domyślnej bazie danych dzierżawy:** Baza danych dzierżawy jest aktualizowana w celu dodania nowych informacji o dzierżawie.
- **Zarejestruj dzierżawę w katalogu:** Mapowanie między nowym kluczem dzierżawy a istniejącą bazą danych tenants1 jest dodawane do katalogu.
- **Dodaj nazwę dzierżawcy do tabeli rozszerzenia katalogu:** Nazwa miejsca jest dodawana do tabeli Dzierżawcy w katalogu.  To dodanie pokazuje, jak baza danych wykazu można rozszerzyć do obsługi dodatkowych danych specyficznych dla aplikacji.
- **Otwórz stronę Wydarzenia dla nowego dzierżawcy:** *Bushwillow Blues* strona wydarzenia jest otwarty w przeglądarce.

   ![zdarzenia](media/saas-multitenantdb-provision-and-catalog/bushwillow.png)

#### <a name="debugger-steps"></a>Kroki debugera

Aby zrozumieć, jak aplikacja Wingtip implementuje nowe inicjowanie obsługi administracyjnej dzierżawy w udostępnionej bazie danych, dodaj punkt przerwania i krok po kroku przepływu pracy:

1. W *powershell ISE*, otwórz ... \\Moduły\\szkoleniowe\\ProvisionTenants*Demo-ProvisionTenants.ps1* i ustawić następujące parametry:
   - **$TenantName****Bushwillow Blues**, nazwa nowego miejsca. = 
   - **$VenueType**blues , jeden z wcześniej zdefiniowanych typów miejsc: blues, muzyka klasyczna, taniec, jazz, judo, motorracing, wielofunkcyjny, operowy, rockmusic, piłka nożna (małe litery, bez przestrzeni).**blues** = 
   - **$DemoScenario**1 , aby udostępnić dzierżawę w udostępnionej bazie danych innym dzierżawcom.**1** = 

2. Dodaj punkt przerwania, umieszczając kursor w dowolnym miejscu w wierszu 38, wiersz, który mówi: *New-Tenant '*, a następnie naciśnij **klawisz F9**.

   ![punkt przerwania](media/saas-multitenantdb-provision-and-catalog/breakpoint.png)

3. Uruchom skrypt, naciskając **klawisz F5**.

4. Po zatrzymaniu wykonywania skryptu w punkcie przerwania naciśnij **klawisz F11,** aby przejść do kodu.

   ![debugowanie](media/saas-multitenantdb-provision-and-catalog/debug.png)

5. Śledzenie wykonywania skryptu przy użyciu opcji menu **debugowania,** **F10** i **F11**, aby przejść lub do wywoływanych funkcji.

Aby uzyskać więcej informacji na temat debugowania skryptów programu PowerShell, zobacz [Porady dotyczące pracy z skryptami programu PowerShell i debugowania](https://docs.microsoft.com/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise).

## <a name="provision-a-tenant-in-its-own-database"></a>Aprowizowanie dzierżawcy we *własnej* bazie danych

#### <a name="major-actions-of-provisioning"></a>Główne działania związane z tworzeniem rezerw

Poniżej przedstawiono kluczowe elementy przepływu pracy, przez który przechodzisz podczas śledzenia skryptu:

- **Oblicz nowy klucz dzierżawy:** Funkcja mieszania służy do tworzenia klucza dzierżawy z nazwy dzierżawy.
- **Sprawdź, czy klucz dzierżawy już istnieje**: Katalog jest sprawdzany, aby upewnić się, że klucz nie został jeszcze zarejestrowany.
- **Tworzenie nowej bazy danych dzierżawy:** Baza danych jest tworzona przez skopiowanie bazy danych *basetenantdb* przy użyciu szablonu Menedżera zasobów.  Nowa nazwa bazy danych jest oparta na nazwie dzierżawcy.
- **Dodaj bazę danych do katalogu:** Nowa baza danych dzierżawy jest zarejestrowana jako fragment w katalogu.
- **Inicjuj dzierżawcę w domyślnej bazie danych dzierżawy:** Baza danych dzierżawy jest aktualizowana w celu dodania nowych informacji o dzierżawie.
- **Zarejestruj dzierżawę w katalogu**: Mapowanie między nowym kluczem dzierżawy a bazą danych *sequoiasoccer* jest dodawany do katalogu.
- **Nazwa dzierżawy jest dodawany do katalogu:** Nazwa miejsca jest dodawany do tabeli rozszerzenia dzierżawy w katalogu.
- **Otwórz stronę Wydarzenia dla nowego dzierżawy:** Strona *Sequoia Soccer* Events jest otwierana w przeglądarce.

   ![zdarzenia](media/saas-multitenantdb-provision-and-catalog/sequoiasoccer.png)

#### <a name="debugger-steps"></a>Kroki debugera

Teraz przejdź przez proces skryptu podczas tworzenia dzierżawy we własnej bazie danych:

1. Nadal w ... \\Moduły\\szkoleniowe\\ProvisionTenants*Demo-ProvisionTenants.ps1* ustawić następujące parametry:
   - **$TenantName****Sequoia Soccer**, nazwa nowego miejsca. = 
   - **$VenueType****piłka nożna**, jeden z wcześniej zdefiniowanych typów miejsc: blues, muzyka klasyczna, taniec, jazz, judo, motorracing, wielofunkcyjny, operowy, rockmusic, piłka nożna (małe litery, bez przestrzeni). = 
   - **$DemoScenario**2 , aby udostępnić dzierżawcę do własnej bazy danych.**2** = 

2. Dodaj nowy punkt przerwania, umieszczając kursor w dowolnym miejscu w wierszu 57, wiersz, który mówi: * & &nbsp;$PSScriptRoot\New-TenantAndDatabase '* i naciśnij **klawisz F9**.

   ![punkt przerwania](media/saas-multitenantdb-provision-and-catalog/breakpoint2.png)

3. Uruchom skrypt, naciskając **klawisz F5**.

4. Po zatrzymaniu wykonywania skryptu w punkcie przerwania naciśnij **klawisz F11,** aby przejść do kodu.  Użyj **F10** i **F11,** aby przejść i krok do funkcji do śledzenia wykonywania.

## <a name="provision-a-batch-of-tenants"></a>Aprowizuj partię dzierżawców

To ćwiczenie rezerwuje partię 17 najemców. Zaleca się, aby udostępnić tę partię dzierżawców przed rozpoczęciem innych samouczków Bilety Wingtip, więc istnieje więcej baz danych do pracy.

1. W *powershell ISE*, otwórz ... \\Moduły\\szkoleniowe\\ProvisionTenants*Demo-ProvisionTenants.ps1* i zmienić parametr *$DemoScenario* na 4:
   - **$DemoScenario**4 , aby udostępnić partię dzierżawców do udostępnionej bazy danych.**4** = 

2. Naciśnij klawisz **F5** i uruchom skrypt.

### <a name="verify-the-deployed-set-of-tenants"></a>Weryfikowanie wdrożonego zestawu dzierżaw

Na tym etapie masz mieszankę dzierżaw wdrożonych do udostępnionej bazy danych i dzierżaw wdrożonych w ich własnych bazach danych. Witryna Azure portal może służyć do sprawdzania utworzonych baz danych. W [witrynie Azure portal](https://portal.azure.com)otwórz serwer **\<tenants1-mt- USER,\> ** przeglądając listę serwerów SQL.  Lista **baz danych SQL** powinna zawierać udostępnionej bazy danych **tenants1** i baz danych dla dzierżawców, które znajdują się w ich własnej bazie danych:

   ![lista baz danych](media/saas-multitenantdb-provision-and-catalog/Databases.png)

Podczas gdy portal Azure zawiera bazy danych dzierżawy, nie pozwala zobaczyć *dzierżawców wewnątrz* udostępnionej bazy danych. Pełną listę dzierżaw można zobaczyć na stronie internetowej **Centrum zdarzeń** w witrynie Wingtip i przez przeglądanie katalogu.

#### <a name="using-wingtip-tickets-events-hub-page"></a>Korzystanie ze strony centrum wydarzeń Wingtip Tickets

Otwórz stronę Centrum zdarzeń w przeglądarce (http:events.wingtip-mt.\<USER\>.trafficmanager.net)

#### <a name="using-catalog-database"></a>Korzystanie z bazy danych wykazu

Pełna lista dzierżawców i odpowiedniej bazy danych dla każdego z nich jest dostępna w katalogu. Widok SQL jest pod warunkiem, że łączy nazwę dzierżawy do nazwy bazy danych. Widok ładnie pokazuje wartość rozszerzania metadanych, które są przechowywane w katalogu.
- Widok SQL jest dostępny w bazie danych tenantcatalog.
- Nazwa dzierżawy jest przechowywana w tabeli Dzierżawcy.
- Nazwa bazy danych jest przechowywana w tabelach Zarządzanie fragmentami.

1. W programie SQL Server Management Studio (SSMS) połącz się z serwerem dzierżawców w **\<catalog-mt.\>USER .database.windows.net**, z login = **developer**i Password = **\@P ssword1**

    ![Okno dialogowe połączenia SSMS](media/saas-multitenantdb-provision-and-catalog/SSMSConnection.png)

2. W Eksploratorze obiektów SSMS przejdź do widoków w bazie danych *tenantcatalog.*

3. Kliknij prawym przyciskiem myszy widok *DzierżawcyExtended* i wybierz **pozycję Wybierz 1000 wierszy.** Należy zwrócić uwagę na mapowanie między nazwą dzierżawy i bazy danych dla różnych dzierżaw.

    ![Widok ExtendedTenants w ssms](media/saas-multitenantdb-provision-and-catalog/extendedtenantsview.png)

## <a name="other-provisioning-patterns"></a>Inne wzorce aprowizacji

W tej sekcji omówiono inne ciekawe wzorce inicjowania obsługi administracyjnej.

#### <a name="pre-provisioning-databases-in-elastic-pools"></a>Wstępne inicjowanie obsługi administracyjnej baz danych w pulach elastycznych

Wzorzec wstępnej inicjowania obsługi administracyjnej wykorzystuje fakt, że podczas korzystania z pul elastycznych rozliczeń jest dla puli nie baz danych. W związku z tym bazy danych mogą być dodawane do puli elastycznej, zanim są one potrzebne bez ponoszenia dodatkowych kosztów. To wstępne wizjonowanie znacznie skraca czas poświęcania do aprowizowania dzierżawy do bazy danych. Liczba wstępnie aprowizowanych baz danych można dostosować w razie potrzeby, aby zachować bufor odpowiedni dla przewidywanego wskaźnika inicjowania obsługi administracyjnej.

#### <a name="auto-provisioning"></a>Automatyczne aprowizowanie

We wzorcu automatycznej inicjowania obsługi administracyjnej dedykowana usługa inicjowania obsługi administracyjnej jest używana do automatycznego inicjowania obsługi administracyjnej serwerów, pul i baz danych w razie potrzeby. Ta automatyzacja obejmuje wstępne inicjowanie obsługi administracyjnej baz danych w pulach elastycznych. A jeśli bazy danych są likwidowane i usuwane, luki, które tworzy w pulach elastycznych mogą być wypełniane przez usługę inicjowania obsługi administracyjnej zgodnie z potrzebami.

Ten typ zautomatyzowanej usługi może być prosty lub złożony. Na przykład automatyzacji może obsługiwać inicjowania obsługi administracyjnej w wielu regionach geograficznych i można skonfigurować replikacji geograficznej do odzyskiwania po awarii. Za pomocą wzorca automatycznej inicjowania obsługi administracyjnej aplikacja kliencka lub skrypt przesłałby żądanie inicjowania obsługi administracyjnej do kolejki, która ma zostać przetworzona przez usługę inicjowania obsługi administracyjnej. Skrypt następnie sondy w celu wykrycia zakończenia. Jeśli używane jest wstępne inicjowanie obsługi administracyjnej, żądania będą obsługiwane szybko, podczas gdy usługa w tle będzie zarządzać inicjowania obsługi administracyjnej zastępczej bazy danych.

## <a name="additional-resources"></a>Zasoby dodatkowe

<!-- - Additional [tutorials that build upon the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
- [Biblioteka kliencka Elastic Database](sql-database-elastic-database-client-library.md)
- [Sposób debugowania skryptów w programie Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise)


## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Aprowizowanie pojedynczej nowej dzierżawy do udostępnionej bazy danych z wieloma dzierżawami i własnej bazy danych
> * Aprowizowanie partii dodatkowych dzierżaw
> * Krok po szczegółach inicjowania obsługi administracyjnej dzierżawców i rejestrowanie ich w katalogu

Wypróbuj [samouczek monitorowania wydajności](saas-multitenantdb-performance-monitoring.md).

