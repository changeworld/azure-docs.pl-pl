---
title: Aprowizacja za pomocą modelu SaaS z wieloma dzierżawcami platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak do aprowizacji i wykazu nowych dzierżaw w usłudze Azure SQL Database wielodostępną aplikację SaaS
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib,andrela,stein
manager: craigg
ms.date: 09/24/2018
ms.openlocfilehash: d29baaad6090cea5eb31f5f50bba444cb3771155
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61485980"
---
# <a name="provision-and-catalog-new-tenants-in-a-saas-application-using-a-sharded-multi-tenant-azure-sql-database"></a>Aprowizacji i wykazu nowych dzierżaw w aplikacji SaaS, używa podzielonej na fragmenty bazy danych Azure SQL wielodostępnych

W tym artykule opisano aprowizowanie i katalogowanie nowych dzierżaw w *wielodostępną bazą danych podzielonych na fragmenty* modelu lub wzorzec.

W tym artykule znajdują się dwie główne części:

- [Omówienie koncepcyjne](#goto_2_conceptual) aprowizacji i katalogowanie nowych dzierżaw.

- [Samouczek](#goto_1_tutorial) który wyróżnia wykonująca aprowizacji i katalogowanie kod skryptu programu PowerShell.
  - W tym samouczku użyto aplikacji SaaS o nazwie Wingtip Tickets, dostosowane do wzorca wielodostępną bazą danych podzielonych na fragmenty.

<a name="goto_2_conceptual"/>

## <a name="database-pattern"></a>Wzorzec bazy danych

W tej sekcji, a także kilka innych, które należy wykonać, omówienia pojęć wzorzec wielodostępną bazą danych podzielonych na fragmenty.

W tym modelu podzielonej na fragmenty wielodostępnych schematów tabel w każdej bazie danych obejmują klucz dzierżawy w kluczu podstawowym tabel, które przechowują dane dzierżawy. Klucz dzierżawy umożliwia każdej poszczególnych baz danych do przechowywania 0, 1 lub wiele dzierżaw. Korzystanie z baz danych podzielonych na fragmenty ułatwia systemu aplikacji do obsługi dużej liczby dzierżawców. Wszystkie dane dla dzierżawy jest przechowywane w jednej bazie danych. Wiele dzierżaw są rozmieszczane w wielu baz danych podzielonych na fragmenty. Baza danych wykazu przechowuje mapowanie każdego dzierżawcy z jego bazą danych.

#### <a name="isolation-versus-lower-cost"></a>Izolacja a niższe koszty

Dzierżawy jest baza danych, wszystkie sobie będą zadowoleni z tych korzyści z izolacji. Dzierżawcy mogą mieć baza danych przywrócona do wcześniejszego stanu, bez ograniczenia, wpływ na innych dzierżawców. Wydajność bazy danych mogą być dostosowane do optymalizacji dla jednej dzierżawy ponownie bez konieczności pogodzenia się z innymi dzierżawami. Problem polega na tym, że izolacji droższe niż to kosztuje do udostępniania bazy danych z innych dzierżaw.

Gdy nowa dzierżawa zostanie zaaprowizowana, go udostępnić bazę danych z innych dzierżaw lub mogą być umieszczane nowej bazie danych. Można później zmienić zdanie i przenieść bazę danych do innych sytuacji.

Bazy danych z wielu dzierżawców i jednej dzierżawy są zmieszane w ramach jednej aplikacji SaaS w celu zoptymalizowania kosztów ani izolację dla każdej dzierżawy.

   ![Aplikacji podzielonej na fragmenty wielodostępną bazą danych za pomocą dzierżawy katalogu](media/saas-multitenantdb-provision-and-catalog/MultiTenantCatalog.png)

## <a name="tenant-catalog-pattern"></a>Wzorca wykazu dzierżawy

Jeśli masz co najmniej dwóch baz danych, że każdy zawiera co najmniej jedną dzierżawę, aplikacja musi mieć sposobu wykrywania bazę danych, która przechowuje dzierżawę bieżącego zainteresowania. Baza danych wykazu przechowuje tego mapowania.

#### <a name="tenant-key"></a>klucz dzierżawy

Dla każdego dzierżawcy aplikację Wingtip może pochodzić Unikatowy klucz jest kluczem dzierżawy. Aplikacja wyodrębnia nazwy dzierżawy w adresie URL strony sieci Web. Aplikacja tworzy skrót nazw klucz. Aplikacja używa klucza dostępu do wykazu. Katalog odsyłacze informacje o bazie danych, w którym przechowywany jest dzierżawy. Aplikacja używa informacji o bazie danych do łączenia. Można także inne systemy klucza dzierżawy.

Za pomocą katalogu umożliwia nazwy lub lokalizacji dzierżawy bazę danych do można zmienić po zainicjowaniu obsługi administracyjnej bez zakłócania pracy aplikacji. W modelu wielodostępnej bazie danych wykazu obsługuje przenoszenie dzierżawy między bazami danych.

#### <a name="tenant-metadata-beyond-location"></a>Metadane dzierżawy poza lokalizacji

Katalog można również określić, czy dzierżawa jest w trybie offline z powodu konserwacji lub innych działań. I można rozszerzyć katalog do przechowywania dodatkowych dzierżawy lub metadanych bazy danych, takich jak następujące elementy:
- Warstwy usługi lub wersja bazy danych.
- Wersja schematu bazy danych.
- Nazwa dzierżawy i jej umowa SLA (Umowa dotycząca poziomu usług).
- Informacje, aby umożliwić zarządzanie aplikacjami, dział obsługi klienta i procesy metodyki devops.  

Katalog można również włączyć raportowania, Zarządzanie schematami wielu dzierżawach i wyodrębnianie danych, dla celów analizy. 

### <a name="elastic-database-client-library"></a>Biblioteka kliencka Elastic Database 

W aplikacji Wingtip, katalog jest zaimplementowana w *tenantcatalog* bazy danych. *Tenantcatalog* jest tworzony przy użyciu funkcji zarządzania fragmentami [elastycznej bazy danych klienta biblioteki EDCL ()](sql-database-elastic-database-client-library.md). Biblioteka włącza aplikację do tworzenia, zarządzania i używania *mapowanie fragmentów w postaci* przechowywane w bazie danych. Mapowania fragmentów w postaci odsyłacze klucza dzierżawy z fragmentu, co oznacza jego podzielonej na fragmenty bazy danych.

Podczas inicjowania obsługi dzierżawy EDCL functions może służyć z aplikacji lub skryptów programu PowerShell można utworzyć wpisów w ramach mapowania fragmentów. Funkcje biblioteki EDCL można później połączyć się z odpowiednią bazą danych. Biblioteka EDCL buforuje informacje o połączeniu, aby zminimalizować ruch w bazie danych wykazu i przyspieszyć proces nawiązywania połączenia.

> [!IMPORTANT]
> Czy *nie* edytowanie danych w bazie danych wykazu za pomocą bezpośredniego dostępu! Bezpośrednie aktualizacje nie są obsługiwane ze względu na duże ryzyko uszkodzenia danych. Zamiast tego dane mapowania można edytować tylko przy użyciu biblioteki EDCL interfejsów API.

## <a name="tenant-provisioning-pattern"></a>Wzorzec inicjowania obsługi dzierżawy

#### <a name="checklist"></a>Lista kontrolna

Aprowizacja nowej dzierżawy do istniejących udostępnionej bazy danych, należy z udostępnionej bazy danych, musi on odpowiedzieć na następujące pytania:
- Ma wystarczającej ilości miejsca dla nowej dzierżawy?
- Ma tabel z danymi referencyjnymi niezbędne dla nowej dzierżawy lub danych można dodać?
- Ma odpowiednią odmianę schemat bazowy dla nowej dzierżawy?
- Jest on w odpowiedniej lokalizacji geograficznej blisko nowej dzierżawy?
- Jest to w warstwie usług odpowiednie dla nowej dzierżawy?

Ma nową dzierżawę, aby można wyizolować w jego własnej bazy danych, można utworzyć, aby spełniało specyfikacją dla dzierżawy.

Po ukończeniu inicjowania obsługi administracyjnej należy zarejestrować dzierżawy w wykazie. Na koniec można dodać mapowanie dzierżawy można odwoływać się do odpowiedniego fragmentu.

#### <a name="template-database"></a>Baza danych szablonów

Aprowizacja bazy danych, wykonywania skryptów SQL, wdrażanie pliku bacpac lub kopiując szablonu bazy danych. Aplikacje o nazwie Wingtip skopiuj szablonu bazy danych do tworzenia nowych baz danych dzierżaw.

Podobnie jak każda aplikacja Wingtip ewoluuje wraz z upływem czasu. W czasie o nazwie Wingtip będzie wymagać zmiany w bazie danych. Zmiany mogą obejmować następujące elementy:
- Nowy lub zmieniony schemat.
- Nowe lub zmienione dane referencyjne.
- Zadania konserwacji bazy danych procedury zapewniające optymalną wydajność aplikacji.

W przypadku aplikacji SaaS zmiany te muszą zostać wprowadzone w sposób skoordynowany — potencjalnie w bardzo wielu bazach danych dzierżaw. Dla tych zmian w przyszłości baz danych dzierżaw muszą one należy włączyć do procesu aprowizacji. Ten problem jest przedstawione w dalszych [samouczka dotyczącego zarządzania schematu](saas-tenancy-schema-management.md).

#### <a name="scripts"></a>Scripts

Skrypty inicjowania obsługi dzierżawy, w tym samouczku obsługują oba z następujących scenariuszy:
- Inicjowanie obsługi dzierżawy do istniejącej bazy danych współużytkowane z innymi dzierżawami.
- Inicjowanie obsługi administracyjnej dzierżawy, w osobnej bazie danych.

Dane dzierżawy następnie zostanie zainicjowana i jest zarejestrowany w ramach mapowania fragmentów w katalogu. W przykładowej aplikacji baz danych, które zawierają wiele dzierżaw podane są nazwę rodzajową, taką jak *tenants1* lub *tenants2*. Bazy danych, które zawierają pojedynczej dzierżawy są podane nazwy dzierżawy. Określonych konwencji nazewnictwa użytemu w przykładzie nie są kluczową częścią wzorzec, jak dowolną nazwę można przypisać do bazy danych pozwala na korzystanie z katalogu.  

<a name="goto_1_tutorial"/>

## <a name="tutorial-begins"></a>Samouczek rozpoczyna się

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Udostępniania dzierżawy na wielodostępną bazą danych
> * Aprowizacja dzierżawy do bazy danych w pojedynczej dzierżawy
> * Aprowizowanie partii dzierżaw do baz danych z wieloma dzierżawami i pojedynczej dzierżawy
> * Zarejestruj bazę danych i mapowanie w katalogu dzierżawy

#### <a name="prerequisites"></a>Wymagania wstępne

Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

- Zainstalowany jest program Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

- Aplikacja o nazwie Wingtip Tickets SaaS wielodostępnej w bazie danych jest wdrażana. Aby wdrożyć w mniej niż pięć minut, zobacz [wdrażanie i eksplorowanie aplikacji Wingtip Tickets SaaS wielodostępnej w bazie danych](saas-multitenantdb-get-started-deploy.md)

- Pobieranie skryptów aplikacji Wingtip i kod źródłowy:
    - Skryptów aplikacji Wingtip Tickets SaaS wielodostępnej w bazie danych i kod źródłowy aplikacji są dostępne w [WingtipTicketsSaaS MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) repozytorium GitHub.
    - Zobacz [ogólne wskazówki dotyczące](saas-tenancy-wingtip-app-guidance-tips.md) kroków pobrać i odblokować skryptów aplikacji Wingtip. 

## <a name="provision-a-tenant-into-a-database-shared-with-other-tenants"></a>Aprowizowanie dzierżawcy w bazie danych *udostępnionego* z innymi dzierżawami

W tej sekcji możesz wyświetlić listę głównych akcji aprowizacji, wykonywanych przez skrypty programu PowerShell. Następnie przy użyciu debugera programu PowerShell ISE przechodzenie przez skrypty, aby zobaczyć akcje w kodzie.

#### <a name="major-actions-of-provisioning"></a>Główne akcji aprowizacji

Poniżej przedstawiono kluczowe elementy przepływu pracy aprowizacji, przechodzić krok po kroku przez:

- **Oblicz nowy klucz dzierżawy**: Funkcja skrótu służy do tworzenia klucza dzierżawy na podstawie nazwy dzierżawy.
- **Sprawdź, czy klucz dzierżawy już istnieje**: Wykaz jest sprawdzany w celu zapewnienia, że klucz nie został już zarejestrowany.
- **Inicjowanie dzierżawy w domyślna baza danych dzierżawy**: Baza danych dzierżawy jest zaktualizowano w celu dodania nowych informacji o dzierżawie.  
- **Zarejestrować dzierżawy w wykazie**: Mapowanie między nowego klucza dzierżawy i istniejącej bazy danych tenants1 zostanie dodany do katalogu. 
- **Dodaj nazwę dzierżawcy do tabeli rozszerzenia katalogu**: Nazwa właściwości została dodana do tabeli dzierżaw w wykazie.  To dodawanie pokazuje, jak baza danych wykazu, można rozszerzyć do obsługi dodatkowych danych specyficznych dla aplikacji.
- **Otwórz stronę zdarzenia dla nowej dzierżawy**: *Bushwillow Blues* stronie zdarzeń jest otwarty w przeglądarce.

   ![zdarzenia](media/saas-multitenantdb-provision-and-catalog/bushwillow.png)

#### <a name="debugger-steps"></a>Debuger nie wchodzi

Aby dowiedzieć się, jak aplikacja Wingtip implementuje nową dzierżawę, aprowizowanie w udostępnionej bazy danych, Dodaj punkt przerwania i kroku za pomocą przepływu pracy:

1. W *PowerShell ISE*, Otwórz... \\Learning Modules\\ProvisionTenants\\*ProvisionTenants.ps1 pokaz* i ustaw następujące parametry:
   - **$TenantName** = **Bushwillow Blues**, Nazwa nowego miejsca.
   - **$VenueType** = **blues**, jeden z wstępnie zdefiniowanych typów miejsca: blues, classicalmusic, dance, jazz, judo, motorracing, multipurpose, opera, rockmusic, soccer (małe, bez spacji).
   - **$DemoScenario** = **1**, aby aprowizować dzierżawy w udostępnionej bazy danych z innych dzierżaw.

2. Dodaj punkt przerwania, umieszczając kursor w dowolnym miejscu w wierszu 38 wiersz, który jest wyświetlany komunikat: *Nowa dzierżawa "* , a następnie naciśnij klawisz **F9**.

   ![punkt przerwania](media/saas-multitenantdb-provision-and-catalog/breakpoint.png)

3. Uruchom skrypt, naciskając klawisz **F5**.

4. Po zatrzymaniu w punkcie przerwania wykonywania skryptu naciśnij **F11** aby wejść do kodu.

   ![Debugowanie](media/saas-multitenantdb-provision-and-catalog/debug.png)

5. Śledź wykonywanie skryptu za pomocą **debugowania** opcje menu **F10** i **F11**, aby wychodzisz nad lub do wywoływanej funkcji.

Aby uzyskać więcej informacji na temat debugowania skryptów programu PowerShell, zobacz [porady dotyczące pracy z i debugowania skryptów programu PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).

## <a name="provision-a-tenant-in-its-own-database"></a>Aprowizacja dzierżawy w jego *własnych* bazy danych

#### <a name="major-actions-of-provisioning"></a>Główne akcji aprowizacji

Poniżej przedstawiono kluczowe elementy przepływu pracy, który krok po kroku przez podczas śledzenia skryptu:

- **Oblicz nowy klucz dzierżawy**: Funkcja skrótu służy do tworzenia klucza dzierżawy na podstawie nazwy dzierżawy.
- **Sprawdź, czy klucz dzierżawy już istnieje**: Wykaz jest sprawdzany w celu zapewnienia, że klucz nie został już zarejestrowany.
- **Utwórz nową bazę danych dzierżawy**: Baza danych jest tworzony przez skopiowanie *basetenantdb* bazy danych przy użyciu szablonu usługi Resource Manager.  Nowa nazwa bazy danych zależy od nazwy dzierżawy.
- **Dodaj bazę danych do katalogu**: Nowa baza danych dzierżawy jest zarejestrowana jako fragmentów w wykazie.
- **Inicjowanie dzierżawy w domyślna baza danych dzierżawy**: Baza danych dzierżawy jest zaktualizowano w celu dodania nowych informacji o dzierżawie.  
- **Zarejestrować dzierżawy w wykazie**: Mapowanie między nowego klucza dzierżawy i *sequoiasoccer* bazy danych zostanie dodany do katalogu.
- **Nazwa dzierżawy została dodana do wykazu**: Nazwa właściwości została dodana do tabeli rozszerzenia dzierżawy w wykazie.
- **Otwórz stronę zdarzenia dla nowej dzierżawy**: *Sequoia Soccer* stronie zdarzeń jest otwarty w przeglądarce.

   ![zdarzenia](media/saas-multitenantdb-provision-and-catalog/sequoiasoccer.png)

#### <a name="debugger-steps"></a>Debuger nie wchodzi

Teraz omówimy proces skryptu podczas tworzenia dzierżawy w oddzielnej bazie danych:

1. Nadal w... \\Learning Modules\\ProvisionTenants\\*ProvisionTenants.ps1 pokaz* ustaw następujące parametry:
   - **$TenantName** = **sequoia Soccer**, Nazwa nowego miejsca.
   - **$VenueType** = **soccer**, jeden z wstępnie zdefiniowanych typów miejsca: blues, classicalmusic, dance, jazz, judo, motorracing, multipurpose, opera, rockmusic, soccer (małe litery, bez spacji).
   - **$DemoScenario** = **2**, aby aprowizować dzierżawę w osobnej bazie danych.

2. Dodaj nowy punkt przerwania, umieszczając kursor w dowolnym miejscu w wierszu 57, wiersz, który jest wyświetlany komunikat:  *& &nbsp;$PSScriptRoot\New-TenantAndDatabase "* i naciśnij klawisz **F9**.

   ![punkt przerwania](media/saas-multitenantdb-provision-and-catalog/breakpoint2.png)

3. Uruchom skrypt, naciskając klawisz **F5**.

4. Po zatrzymaniu w punkcie przerwania wykonywania skryptu naciśnij **F11** aby wejść do kodu.  Użyj **F10** i **F11** Przekrocz nad i wejdź do funkcji do śledzenia realizacji.

## <a name="provision-a-batch-of-tenants"></a>Aprowizowanie partii dzierżaw

Tego ćwiczenia jest aprowizowanie partii dzierżaw 17. Zalecane jest, że przed uruchomieniem innych samouczków aplikacji Wingtip Tickets, więc istnieją większej liczby baz danych do pracy z obsługi administracyjnej tej partii dzierżaw.

1. W *PowerShell ISE*, Otwórz... \\Learning Modules\\ProvisionTenants\\*ProvisionTenants.ps1 pokaz* i zmień *$DemoScenario* parametr 4:
   - **$DemoScenario** = **4**, aby aprowizowanie partii dzierżaw w udostępnionej bazy danych.

2. Naciśnij klawisz **F5** i uruchom skrypt.

### <a name="verify-the-deployed-set-of-tenants"></a>Sprawdź zestaw wdrożonych dzierżaw 

Na tym etapie masz mieszaninę wdrożone w udostępnionej bazy danych dzierżaw i wdrożone w ich własnych baz danych dzierżaw. Witryna Azure portal może służyć do inspekcji bazy danych utworzone. W [witryny Azure portal](https://portal.azure.com), otwórz **tenants1-mt -\<użytkownika\>**  serwer, przechodząc do listy serwerów SQL.  **Baz danych SQL** lista powinna zawierać wspólnie **tenants1** bazy danych i baz danych dla dzierżawy, które znajdują się w ich własnych bazy danych:

   ![lista baz danych](media/saas-multitenantdb-provision-and-catalog/Databases.png)

Witryny Azure portal znajdują się dzierżawy baz danych, nie umożliwia Zobacz dzierżawcy *wewnątrz* udostępnionej bazy danych. Pełną listę dzierżawców można zobaczyć w **Centrum zdarzeń** strony sieci Web o nazwie Wingtip i przeglądając katalogu.

#### <a name="using-wingtip-tickets-events-hub-page"></a>Za pomocą strony Centrum zdarzeń o nazwie Wingtip Tickets

Otwórz stronę Centrum zdarzeń w przeglądarce (http:events.wingtip-mt.\<użytkownika\>. trafficmanager.net)  

#### <a name="using-catalog-database"></a>Przy użyciu bazy danych katalogu

Pełną listę dzierżawców i odpowiednią bazę danych dla każdego jest dostępny w katalogu. Widok SQL jest warunkiem, że sprzężeń nazwy dzierżawy, nazwa bazy danych. Widok dobrze pokazuje wartość rozszerzanie metadanych, który jest przechowywany w katalogu.
- Widok SQL jest dostępna w bazie danych tenantcatalog.
- Nazwa dzierżawy są przechowywane w tabeli dzierżaw.
- Nazwa bazy danych są przechowywane w tabelach zarządzania fragmentami.

1. W SQL Server Management Studio (SSMS), połączenie z serwerem dzierżaw w **katalogu — patrz hasło mt.\<użytkownika\>. database.windows.net**, logowania za pomocą = **developer**i hasło =  **P\@ssword1**

    ![Okno dialogowe połączenia programu SSMS](media/saas-multitenantdb-provision-and-catalog/SSMSConnection.png)

2. W Eksploratorze obiektów programu SSMS przejdź do widoków w *tenantcatalog* bazy danych.

3. Kliknij prawym przyciskiem myszy w widoku *TenantsExtended* i wybierz polecenie **zaznacz 1000 pierwszych wierszy**. Należy pamiętać, mapowanie między nazwą dzierżawy, a bazy danych dla różnych dzierżawach.

    ![Widok ExtendedTenants w programie SSMS](media/saas-multitenantdb-provision-and-catalog/extendedtenantsview.png)
      
## <a name="other-provisioning-patterns"></a>Inne wzorce aprowizacji

W tej sekcji omówiono inne wzorce aprowizacji interesujące.

#### <a name="pre-provisioning-databases-in-elastic-pools"></a>Wstępne aprowizowanie baz danych w elastycznej puli

Wstępne aprowizowanie wzorzec wykorzystuje fakt, że gdy używane są pule elastyczne, rozliczeń jest dla puli nie baz danych. Ten sposób baz danych można dodać do puli elastycznej zanim zajdzie bez ponoszenia dodatkowych kosztów. To wstępnie potencjał znacznie skraca czas potrzebny do udostępniania dzierżawy na bazę danych. Liczbę wstępnie aprowizowanych baz danych można dostosować do własnych potrzeb, aby zachować bufor odpowiedni dla oczekiwanego wskaźnika aprowizacji.

#### <a name="auto-provisioning"></a>Automatyczne aprowizowanie

We wzorcu automatycznej aprowizacji dedykowana usługa aprowizacji jest używany do aprowizacji serwerów, pul i baz danych, automatycznie zgodnie z potrzebami. Ta Automatyzacja zawiera wstępne aprowizowanie baz danych w elastycznej puli. A jeśli bazy danych są zamknięta, a następnie usunięty, luki, w których powstaje w elastycznych pulach mogą zostać uzupełnione przez usługę aprowizacji, zgodnie z potrzebami.

Tego rodzaju automatycznych usługa może być proste lub złożone. Na przykład usługi automation może obsługiwać Inicjowanie obsługi administracyjnej w wielu regionach i można skonfigurować replikację geograficzną dla odzyskiwania po awarii. Za pomocą wzorca automatycznej aprowizacji aplikacja klienta lub skrypt może przesłać żądanie inicjowania obsługi administracyjnej do kolejki do przetworzenia przez usługę aprowizacji. Następnie mogą sondować skryptu do wykrywania ukończenia. Jeśli używana jest wstępna aprowizacja, żądania będzie szybko, obsługiwane, gdy usługa w tle będzie zarządzać aprowizacji zastępczej bazy danych.

## <a name="additional-resources"></a>Dodatkowe zasoby

<!-- - Additional [tutorials that build upon the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
- [Biblioteka kliencka Elastic Database](sql-database-elastic-database-client-library.md)
- [Sposób debugowania skryptów w programie Windows PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)


## <a name="next-steps"></a>Kolejne kroki

W tym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Aprowizowanie pojedynczej nowej dzierżawy w udostępnionej bazy danych z wieloma dzierżawami i własną bazę danych
> * Aprowizowanie partii dodatkowych dzierżaw
> * Przejrzeć szczegółowe informacje o aprowizacji dzierżaw i rejestrowania ich w wykazie

Spróbuj [samouczek monitorowania wydajności](saas-multitenantdb-performance-monitoring.md).

