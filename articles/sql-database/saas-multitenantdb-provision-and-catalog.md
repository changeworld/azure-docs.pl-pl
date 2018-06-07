---
title: Postanowienie SaaS wielodostępne Azure | Dokumentacja firmy Microsoft
description: Informacje o sposobie obsługi administracyjnej i w katalogu nowych dzierżaw w wielodostępnych aplikacji SaaS bazy danych SQL Azure
keywords: samouczek usługi sql database
services: sql-database
author: MightyPen
manager: craigg
ms.reviewer: billgib;andrela;genemi
ms.service: sql-database
ms.custom: saas apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: billgib
ms.openlocfilehash: 42f4aff50a5e3b89ee58f59c0db87f6a174e9be2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34645967"
---
# <a name="provision-and-catalog-new-tenants-in-a-saas-application-using-a-sharded-multi-tenant-azure-sql-database"></a>Dostarczanie i katalogu nowych dzierżaw w przy użyciu podzielonej bazy danych Azure SQL wielodostępnych aplikacji SaaS

W tym artykule omówiono inicjowania obsługi administracyjnej i skatalogowania nowe dzierżawców w *bazy danych podzielonej wielodostępne* modelu lub wzorzec.

Ten artykuł zawiera dwie główne części:

- [Omówienie pojęć](#goto_2_conceptual) udostępnianie i skatalogowania nowe dzierżawców.

- [Samouczek](#goto_1_tutorial) który prezentuje kodu skryptu programu PowerShell, który wykonuje inicjowania obsługi administracyjnej i skatalogowania.
    - W samouczku aplikacji SaaS biletów Wingtip, dostosowane do wzorca bazy danych podzielonej wielodostępnej.

<a name="goto_2_conceptual"/>

## <a name="database-pattern"></a>Wzorzec bazy danych

W tej sekcji, a także kilka więcej niż wykonaj, omówiono pojęcia wzorca bazy danych podzielonej wielodostępnej.

W tej podzielonej modelu wielodostępnym schematy tabeli w każdej bazie danych zawierają klucz dzierżawy w kluczu podstawowym tabel, które przechowują dane dzierżawy. Klucz dzierżawy umożliwia każdej poszczególne bazy danych do przechowywania 0, 1 lub wiele dzierżaw. Korzystanie z bazy danych podzielonej ułatwia systemu aplikacji do obsługi bardzo dużej liczby dzierżawców. Wszystkie dane dla dowolnej jednej dzierżawy są przechowywane w jednej bazie danych. Dużej liczby dzierżawców są rozproszone na wielu podzielonej baz danych. Katalog bazy danych są przechowywane mapowania każdego dzierżawcy do jego bazy danych.

#### <a name="isolation-versus-lower-cost"></a>Izolacja lub niższy koszt

Dzierżawcy z bazy danych wszystkich do samego siebie korzysta z zalet izolacji. Dzierżawcy mogą mieć przywrócona do wcześniejszego stanu bez są ograniczone przez wpływ na innych dzierżawców bazy danych. Wydajność bazy danych można przedstawić w celu zoptymalizowania dla jednego dzierżawcy ponownie bez potrzeby atakowania z innymi dzierżawcami. Problem polega na izolacji koszty więcej niż kosztach współużytkują bazę danych z innymi dzierżawcami.

Po zainicjowaniu obsługi nowej dzierżawy, można go udostępniać bazy danych z innymi dzierżawcami lub można umieścić do nowej bazy danych. Możesz później zmieni zdanie i przenieść bazę danych w innej sytuacji.

W tej samej aplikacji SaaS, aby zoptymalizować koszt lub izolacji dla każdego dzierżawcy mieszane baz danych z wieloma dzierżawcami i jednego dzierżawcy.

   ![Podzielonej wielodostępne bazy danych aplikacji z katalogu dzierżawcy](media/saas-multitenantdb-provision-and-catalog/MultiTenantCatalog.png)

## <a name="tenant-catalog-pattern"></a>Wzorzec katalogu dzierżawcy

Gdy dwa lub więcej baz danych, że każda zawiera co najmniej jednego dzierżawcy, aplikacja musi mieć sposobu wykrywania bazę danych, która przechowuje dzierżawy bieżącego zainteresowań. Katalog bazy danych są przechowywane to mapowanie.

#### <a name="tenant-key"></a>Klucz dzierżawy

Dla każdego dzierżawcy aplikacji Wingtip mogą pochodzić Unikatowy klucz jest kluczem dzierżawy. Aplikacja pobiera nazwę dzierżawcy z adres URL strony sieci Web. Nazwa, klucz skróty w aplikacji. Aplikacja używa klucza, aby uzyskać dostęp do katalogu. Odsyłacze katalogu informacje dotyczące bazy danych, w której jest przechowywany dzierżawcy. Aplikacji nawiązywał połączenie informacje o bazie danych. Można także inne systemy klucza dzierżawy.

Za pomocą wykazu umożliwia nazwę lub lokalizację bazy danych dzierżawy zostanie zmieniony po zainicjowaniu obsługi administracyjnej bez zakłócania pracy aplikacji. W modelu wielodostępnym bazy danych katalogu bierze pod uwagę przeniesienie dzierżawcy między bazami danych.

#### <a name="tenant-metadata-beyond-location"></a>Metadane dzierżawy poza lokalizacji

Katalog można również określić, czy dzierżawcy jest w trybie offline z powodu konserwacji lub innych działań. I katalogu może zostać rozszerzony do przechowywania dodatkowe dzierżawy lub metadanych bazy danych, takie jak następujące elementy:
- Warstwy usług lub wersji bazy danych.
- Wersja schematu bazy danych.
- Nazwa dzierżawy i jego umowy dotyczącej poziomu usług (Umowa dotycząca poziomu usług).
- Informacje umożliwiające zarządzanie aplikacjami, dział obsługi klienta lub procesów opracowywania oprogramowania.  

Katalog można również włączyć dzierżawy między raportowania, zarządzanie schematu i wyodrębniania danych do celów analizy. 

### <a name="elastic-database-client-library"></a>Biblioteka kliencka Elastic Database 

W Wingtip, katalog jest zaimplementowana w *tenantcatalog* bazy danych. *Tenantcatalog* jest tworzony przy użyciu funkcji do zarządzania niezależnego fragmentu [elastycznej bazy danych klienta biblioteki (EDCL)](sql-database-elastic-database-client-library.md). Biblioteka włącza aplikację do tworzenia, zarządzania i użyj *mapy niezależnego fragmentu* jest przechowywana w bazie danych. Mapa niezależnego fragmentu odsyłacze klucza dzierżawy z jego niezależnego fragmentu, co oznacza jego podzielonej bazy danych.

Podczas inicjowania obsługi administracyjnej dzierżawy można można używać funkcji EDCL z aplikacji i skryptów programu PowerShell można utworzyć wpisów w mapie niezależnego fragmentu. Funkcje EDCL można później nawiązać połączenia z poprawną bazą danych. EDCL buforuje informacje o połączeniu, aby zminimalizować ruch w bazie danych katalogu i przyspieszyć proces łączenia.

> [!IMPORTANT]
> Czy *nie* edytowanie danych w bazie danych katalogu za pomocą bezpośredniego dostępu! Bezpośrednie aktualizacje nie są obsługiwane ze względu na duże ryzyko uszkodzenia danych. Zamiast tego można edytować dane mapowania tylko przy użyciu interfejsów API EDCL.

## <a name="tenant-provisioning-pattern"></a>Wzorzec inicjowania obsługi administracyjnej dzierżawy

#### <a name="checklist"></a>Lista kontrolna

Jeśli chcesz udostępnić nową dzierżawę do istniejącej bazy danych udostępnionych udostępnionej bazy danych, możesz musi na następujące pytania:
- Ma wystarczającej ilości miejsca dla nowej dzierżawy?
- Ma tabel z danymi niezbędne odwołania dla nowej dzierżawy lub danych można dodać?
- Ma odpowiedniej zmiany schematu podstawowego dla nowej dzierżawy?
- Jest to w odpowiedniej lokalizacji geograficznej blisko nowej dzierżawy?
- Jest to w warstwie usług prawo dla nowej dzierżawy?

Nowej dzierżawy mogą działać w własną bazę danych, należy można utworzyć ją ze specyfikacjami dla dzierżawcy.

Po ukończeniu inicjowania obsługi administracyjnej, należy zarejestrować dzierżawcy w katalogu. Na koniec dzierżawy mapowania można dodać do odwołania odpowiednie niezależnego fragmentu.

#### <a name="template-database"></a>Szablonu bazy danych

Zainicjuj obsługę bazy danych przez wykonywanie skryptów SQL, wdrażanie pliku bacpac lub kopiowanie szablonu bazy danych. Aplikacje Wingtip skopiuj szablonu bazy danych do utworzenia nowej dzierżawy baz danych.

Podobnie jak wszelkie aplikacje Wingtip rozpoczyna się wraz z upływem czasu. W czasie Wingtip wymaga zmiany w bazie danych. Zmiany mogą obejmować następujące elementy:
- Schemat nowe lub zostały zmienione.
- Dane referencyjne nowe lub zostały zmienione.
- Bazy danych rutynowych zadań konserwacji, aby zapewnić optymalną wydajność aplikacji.

W przypadku aplikacji SaaS zmiany te muszą zostać wprowadzone w sposób skoordynowany — potencjalnie w bardzo wielu bazach danych dzierżaw. Aby te zmiany można w przyszłości dzierżawy baz danych muszą należy włączyć do procesu inicjowania obsługi administracyjnej. To żądanie jest przedstawione na [Samouczek zarządzania schematu](saas-tenancy-schema-management.md).

#### <a name="scripts"></a>Scripts

Skrypty inicjowania obsługi administracyjnej dzierżawy, w tym samouczku obsługują zarówno z następujących scenariuszy:
- Inicjowanie obsługi administracyjnej dzierżawcy do istniejącej bazy danych udostępnionych z innymi dzierżawcami.
- Inicjowanie obsługi administracyjnej dzierżawcy na własną bazę danych.

Dane dzierżawy jest następnie zainicjowany i zarejestrowanych w katalogu niezależnych mapy. W przykładowej aplikacji, baz danych, które zawierają wiele dzierżaw są podane nazwy ogólnej, takich jak *tenants1* lub *tenants2*. Bazy danych, które zawierają pojedynczej dzierżawy podano nazwę dzierżawcy. Określonych konwencji nazewnictwa użyty w próbce nie mogą krytyczną częścią wzorzec, ponieważ wykaz umożliwiają dowolną nazwę można przypisać do bazy danych.  

<a name="goto_1_tutorial"/>

## <a name="tutorial-begins"></a>Samouczek rozpoczyna się

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Zapewnij dzierżawcy do bazy danych z wieloma dzierżawcami
> * Zapewnij dzierżawcy w bazie danych pojedynczej dzierżawy
> * Udostępnianie partii dzierżawcy do baz danych zarówno wielodostępnej i pojedynczej dzierżawy
> * Zarejestruj bazy danych i dzierżawy mapowanie w katalogu

#### <a name="prerequisites"></a>Wymagania wstępne

Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

- Zainstalowany jest program Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

- Aplikacja Wingtip biletów SaaS wielodostępne w bazie danych jest wdrażana. Aby wdrożyć w mniej niż 5 minut, zobacz [Wdróż i eksplorowanie Wingtip biletów SaaS wielodostępne w bazie danych aplikacji](saas-multitenantdb-get-started-deploy.md)

- Pobierz Wingtip skrypty i kod źródłowy:
    - Skrypty Wingtip biletów SaaS wielodostępne w bazie danych i kodu źródłowego aplikacji są dostępne w [WingtipTicketsSaaS MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) repozytorium GitHub.
    - Zobacz [ogólne wskazówki](saas-tenancy-wingtip-app-guidance-tips.md) dla czynności, aby pobrać i odblokować Wingtip skryptów. 

## <a name="provision-a-tenant-into-a-database-shared-with-other-tenants"></a>Zapewnij dzierżawcy w bazie danych *udostępnionego* z innymi dzierżawcami

W tej sekcji możesz wyświetlić listę głównych akcji do inicjowania obsługi, które są pobierane przez skrypty programu PowerShell. Następnie użyj debugera PowerShell ISE do wykonania kroków opisanych skrypty, aby zobaczyć dostępne akcje w kodzie.

#### <a name="major-actions-of-provisioning"></a>Główne akcje inicjowania obsługi administracyjnej

Poniżej przedstawiono kluczowe elementy inicjowania obsługi administracyjnej przepływu pracy, które można wykonywać krokowo:

- **Oblicz nowego klucza dzierżawy**: funkcja skrótu jest używany do tworzenia klucza dzierżawy z nazwę dzierżawcy.
- **Sprawdź, czy klucz dzierżawy już istnieje**: katalog jest sprawdzić klucz nie został już zarejestrowany.
- **Inicjowanie dzierżawcy w bazie danych dzierżawy domyślne**: bazy danych dzierżawy jest zaktualizowano w celu dodania nowych informacji dzierżawy.  
- **Zarejestruj dzierżawy w katalogu**: mapowanie między nowego klucza dzierżawy i z istniejącą bazą danych tenants1 jest dodane do katalogu. 
- **Dodaj nazwę dzierżawcy do tabeli rozszerzenia katalogu**: Nazwa właściwość została dodana do tabeli dzierżaw w katalogu.  To dodawanie pokazuje, jak baza danych katalogu można rozszerzyć do obsługi dodatkowe dane specyficzne dla aplikacji.
- **Otwórz stronę zdarzenia dla nowej dzierżawy**: *niebieskie Bushwillow* strona zdarzenia jest otwarta w przeglądarce.

   ![zdarzenia](media/saas-multitenantdb-provision-and-catalog/bushwillow.png)

#### <a name="debugger-steps"></a>Kroki debugera

Aby zrozumieć, jak aplikacja Wingtip implementuje nowej dzierżawy alokacji w udostępnionej bazy danych, Dodaj punkt przerwania i kroków przepływu pracy:

1. W *PowerShell ISE*, Otwórz... \\Modułów uczenia\\ProvisionTenants\\*ProvisionTenants.ps1 pokaz* i ustaw następujące parametry:
   - **$TenantName** = **niebieskie Bushwillow**, nazwę nowe miejsce.
   - **$VenueType** = **niebieskie**, jeden z typów wstępnie zdefiniowanych miejscową: niebieskie, classicalmusic, tańca, jazz judo, motorracing uniwersalne, opera, rockmusic, nożną (małe, bez spacji).
   - **$DemoScenario** = **1**, aby ustanowić dzierżawy w udostępnionej bazy danych z innymi dzierżawcami.

2. Dodawanie punktu przerwania w dowolnym umieszczając kursor w wiersz 38, informujący o: *nowej dzierżawy "*, a następnie naciśnij klawisz **F9**.

   ![punkt przerwania](media/saas-multitenantdb-provision-and-catalog/breakpoint.png)

3. Uruchom skrypt, naciskając klawisz **F5**.

4. Po zatrzymaniu wykonywania skryptu na punkt przerwania, naciśnij klawisz **F11** do kroku do kodu.

   ![debuguj](media/saas-multitenantdb-provision-and-catalog/debug.png)

5. Śledzenie wykonywanie skryptu za pomocą **debugowania** opcji menu **F10** i **F11**, aby krok za pośrednictwem lub do funkcji o nazwie.

Aby uzyskać więcej informacji na temat debugowania skryptów programu PowerShell, zobacz [porady dotyczące pracy z i debugowania skryptów programu PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).

## <a name="provision-a-tenant-in-its-own-database"></a>Zapewnij dzierżawcy w jego *własnych* bazy danych

#### <a name="major-actions-of-provisioning"></a>Główne akcje inicjowania obsługi administracyjnej

Poniżej przedstawiono kluczowe elementy przepływu pracy, który krokowo podczas śledzenia skryptu:

- **Oblicz nowego klucza dzierżawy**: funkcja skrótu jest używany do tworzenia klucza dzierżawy z nazwę dzierżawcy.
- **Sprawdź, czy klucz dzierżawy już istnieje**: katalog jest sprawdzić klucz nie został już zarejestrowany.
- **Utwórz nową bazę danych dzierżawy**: baza danych została utworzona przez skopiowanie *basetenantdb* bazy danych przy użyciu szablonu usługi Resource Manager.  Nowa nazwa bazy danych opiera się na nazwę dzierżawcy.
- **Dodaj bazę danych do katalogu**: nową bazę danych dzierżawy jest zarejestrowany jako niezależnego fragmentu w katalogu.
- **Inicjowanie dzierżawcy w bazie danych dzierżawy domyślne**: bazy danych dzierżawy jest zaktualizowano w celu dodania nowych informacji dzierżawy.  
- **Zarejestruj dzierżawy w katalogu**: mapowanie między nowego klucza dzierżawy i *sequoiasoccer* bazy danych jest dodane do katalogu.
- **Nazwa dzierżawcy jest dodane do katalogu**: Nazwa właściwość została dodana do tabeli rozszerzenia dzierżaw w katalogu.
- **Otwórz stronę zdarzenia dla nowej dzierżawy**: *nożną Sequoia* strona zdarzenia jest otwarta w przeglądarce.

   ![zdarzenia](media/saas-multitenantdb-provision-and-catalog/sequoiasoccer.png)

#### <a name="debugger-steps"></a>Kroki debugera

Teraz przeprowadzenie procesu skryptu podczas tworzenia dzierżawcy w własną bazę danych:

1. Nadal... \\Modułów uczenia\\ProvisionTenants\\*ProvisionTenants.ps1 pokaz* ustaw następujące parametry:
   - **$TenantName** = **nożną sequoia**, nazwę nowe miejsce.
   - **$VenueType** = **nożną**, jeden z typów wstępnie zdefiniowanych miejscową: niebieskie, classicalmusic, tańca, jazz judo, motorracing uniwersalne, opera, rockmusic, nożną (małe litery, bez spacji).
   - **$DemoScenario** = **2**, do udostępniania dzierżawcy na własną bazę danych.

2. Dodać nowego punktu przerwania umieszczając kursor dowolne miejsce na wiersz 57, informujący:  *& &nbsp;$PSScriptRoot\New-TenantAndDatabase "* i naciśnij klawisz **F9**.

   ![punkt przerwania](media/saas-multitenantdb-provision-and-catalog/breakpoint2.png)

3. Uruchom skrypt, naciskając klawisz **F5**.

4. Po zatrzymaniu wykonywania skryptu na punkt przerwania, naciśnij klawisz **F11** do kroku do kodu.  Użyj **F10** i **F11** Przekrocz nad i wkraczać do funkcji do śledzenia realizacji.

## <a name="provision-a-batch-of-tenants"></a>Zainicjuj obsługę partii dzierżawcy

Tego ćwiczenia inicjuje partii 17 dzierżaw. Zaleca się, że przed uruchomieniem innych samouczków Wingtip biletów, więc więcej baz danych do pracy z obsługi administracyjnej tej partii dzierżaw.

1. W *PowerShell ISE*, Otwórz... \\Modułów uczenia\\ProvisionTenants\\*ProvisionTenants.ps1 pokaz* i zmienić *$DemoScenario* parametr 4:
   - **$DemoScenario** = **4**, aby udostępnić partii dzierżawcom w udostępnionej bazy danych.

2. Naciśnij klawisz **F5** i uruchom skrypt.

### <a name="verify-the-deployed-set-of-tenants"></a>Sprawdź zestaw wdrożonych dzierżawcy 

Na tym etapie masz kombinację dzierżaw wdrożonych w udostępnionej bazy danych i dzierżaw wdrożone do ich własnych baz danych. Portalu Azure można przeprowadzać inspekcję bazy danych utworzone. W [portalu Azure](https://portal.azure.com), otwórz **tenants1-mt -\<użytkownika\>**  serwer, przechodząc do listy serwerów SQL.  **Baz danych SQL** lista powinna zawierać udostępnionego **tenants1** bazy danych i baz danych w przypadku dzierżaw, które znajdują się w ich własnych bazy danych:

   ![lista baz danych](media/saas-multitenantdb-provision-and-catalog/Databases.png)

Podczas gdy portalu Azure dzierżawcy baz danych, nie umożliwia Zobacz dzierżawcami *wewnątrz* udostępnionej bazy danych. Pełną listę dzierżawców są widoczne w **Centrum zdarzeń** strony sieci Web z Wingtip i przeglądając katalogu.

#### <a name="using-wingtip-tickets-events-hub-page"></a>Za pomocą strony Centrum zdarzeń Wingtip biletów

Otwórz stronę Centrum zdarzeń w przeglądarce (http:events.wingtip-mt.\<użytkownika\>. trafficmanager.net)  

#### <a name="using-catalog-database"></a>Przy użyciu bazy danych katalogu

Pełna lista dzierżawcy i dla każdej bazy danych jest dostępne w wykazie. Widok SQL jest warunkiem, że sprzężenia dzierżawcy name Nazwa bazy danych. Widok pokazuje dobrze wartość rozszerzanie metadanych, które są przechowywane w katalogu.
- Widok SQL jest dostępna w bazie danych tenantcatalog.
- Nazwa dzierżawy jest przechowywana w tabeli dzierżaw.
- Nazwa bazy danych są przechowywane w tabelach zarządzania niezależnego fragmentu.

1. W programu SQL Server Management Studio (SSMS), nawiąż połączenie z serwerem dzierżaw w **patrz hasło mt. katalogu\<użytkownika\>. database.windows.net**, logowania = **developer**i hasło = **P@ssword1**

    ![W oknie dialogowym połączenia SSMS](media/saas-multitenantdb-provision-and-catalog/SSMSConnection.png)

2. W Eksploratorze obiektów SSMS, przejdź do widoków *tenantcatalog* bazy danych.

3. Kliknij prawym przyciskiem myszy w widoku *TenantsExtended* i wybierz polecenie **zaznacz 1000 pierwszych wierszy**. Należy pamiętać, mapowanie między nazwa dzierżawcy i bazy danych dla różnych dzierżawców.

    ![Widok ExtendedTenants w programie SSMS](media/saas-multitenantdb-provision-and-catalog/extendedtenantsview.png)
      
## <a name="other-provisioning-patterns"></a>Inne wzorce aprowizacji

W tej sekcji omówiono innych interesujących wzorców inicjowania obsługi administracyjnej.

#### <a name="pre-provisioning-databases-in-elastic-pools"></a>Wstępne inicjowanie obsługi baz danych w puli elastycznej

Wzorzec wstępnego inicjowania obsługi administracyjnej wykorzystuje fakt, że gdy używane są pule elastyczne, rozliczeń dla puli nie jest baz danych. W związku z tym baz danych można dodać do puli elastycznej przed są potrzebne bez ponoszenia dodatkowych kosztów. To wstępnie potencjał znacznie zmniejsza czas potrzebny do udostępnienia dzierżawcy w bazie danych. Wstępnie tworzyć baz danych można dostosować do własnych potrzeb, aby zachować odpowiedni w przypadku inicjowania obsługi administracyjnej oczekiwanego wskaźnika buforu.

#### <a name="auto-provisioning"></a>Automatyczne aprowizowanie

We wzorcu automatycznego inicjowania obsługi administracyjnej dedykowanego inicjowania obsługi usługi służy do obsługi administracyjnej serwerów, pule adresów i baz danych automatycznie zgodnie z potrzebami. Ta Automatyzacja obejmuje wstępnie udostępniania bazy danych w puli elastycznej. A jeśli bazy danych są zlikwidowana i usuwane, luki, który spowoduje to utworzenie w puli elastycznej mogą być wypełniane przez usługę inicjowania obsługi administracyjnej zgodnie z potrzebami.

Automatyczne usług tego typu może być prostymi lub złożonymi. Na przykład automatyzację może obsłużyć inicjowania obsługi administracyjnej na wielu różnych obszarach geograficznych i można skonfigurować replikację geograficzną odzyskiwania po awarii. Za pomocą wzorca automatycznego inicjowania obsługi administracyjnej klienta aplikacji lub skryptu czy przesłać żądanie inicjowania obsługi administracyjnej do kolejki do przetworzenia przez inicjowania obsługi administracyjnej usługi. Skrypt może następnie sondować wykryć ukończenia. Jeśli używana jest wstępnie inicjowania obsługi, żądania może szybko, obsługiwane podczas usługi tła zarządzała inicjowania obsługi administracyjnej zastąpienia bazy danych.

## <a name="additional-resources"></a>Zasoby dodatkowe

<!-- - Additional [tutorials that build upon the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
- [Biblioteka kliencka Elastic Database](sql-database-elastic-database-client-library.md)
- [Sposób debugowania skryptów w programie Windows PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)


## <a name="next-steps"></a>Kolejne kroki

W tym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Udostępnianie pojedynczej, nowej dzierżawy w udostępnionej bazy danych wielu dzierżawców i własnej bazy danych
> * Aprowizowanie partii dodatkowych dzierżaw
> * Przejrzyj kroki szczegóły udostępnianie dzierżaw i rejestrowania ich do katalogu

Spróbuj [samouczek monitorowania wydajności](saas-multitenantdb-performance-monitoring.md).

