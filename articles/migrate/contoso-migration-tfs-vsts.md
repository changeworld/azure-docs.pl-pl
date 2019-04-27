---
title: Refaktoryzuj wdrożenia serwera Team Foundation Server do usługi Azure DevOps Services na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Contoso refactors jego lokalnego wdrożenia programu TFS przy użyciu funkcji migracji do usługi Azure DevOps usług na platformie Azure.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 21396a10543d388b6ac360f426272f1841b2f510
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60674585"
---
# <a name="contoso-migration--refactor-a-team-foundation-server-deployment-to-azure-devops-services"></a>Migracja Contoso:  Refaktoryzowanie wdrożenia serwera Team Foundation Server do usługi Azure DevOps Services

W tym artykule pokazano, jak firmy Contoso jest Refaktoryzacja ich lokalnego wdrożenia Team Foundation Server (TFS) przy użyciu funkcji migracji do usługi Azure DevOps usług na platformie Azure. Zespół programistyczny firmy Contoso użyto ostatnich pięciu lat. TFS do pracę zespołową i kontroli źródła. Teraz firma chce przenieść do rozwiązania oparte na chmurze do tworzenia i testowania pracy i do kontroli źródła. Usługom DevOps platformy Azure będzie odtwarzany rolę, jak przenieść do modelu DevOps platformy Azure i tworzyć nowe aplikacje w natywnych dla chmury.

W tym dokumencie jest jednym z serii artykułów, które pokazują, jak fikcyjnej firmy Contoso migruje swoje zasoby lokalne do chmury Microsoft Azure. Seria zawiera dodatkowe informacje i scenariusze, które ilustrują sposób konfigurowania infrastruktury migracji i uruchamiania różnych rodzajów migracji. Scenariusze zwiększanie się stopnia skomplikowania. Dodamy dodatkowe artykuły wraz z upływem czasu.

**Artykuł** | **Szczegóły** | **Stan**
--- | --- | ---
[Artykuł 1: Przegląd](contoso-migration-overview.md) | Zawiera omówienie strategii migracji firmy Contoso, serię artykułów i przykładowe aplikacje, używanych przez firmę Microsoft. | Dostępne
[Artykuł 2: Wdrażanie infrastruktury platformy Azure](contoso-migration-infrastructure.md) | W tym artykule opisano, jak Contoso przygotowuje jej w środowisku lokalnym i infrastruktury platformy Azure do migracji. Tej samej infrastruktury jest używana w przypadku wszystkich scenariuszy migracji Contoso. | Dostępne
[Artykuł 3: Ocena zasobów lokalnych](contoso-migration-assessment.md)  | Pokazuje, jak firmy Contoso jest wykonywany oceny lokalnej SmartHotel dwuwarstwowej aplikacji działających z oprogramowaniem VMware. Ich oceny maszyn wirtualnych aplikacji, za pomocą [usługi Azure Migrate](migrate-overview.md) usługi bazy danych oraz aplikacji programu SQL Server za pomocą [Asystenta migracji bazy danych usługi Azure](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Dostępne
[Artykuł 4: Rehost maszyn wirtualnych platformy Azure i wystąpienie zarządzane SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Pokazuje, jak Contoso migruje SmartHotel aplikacji na platformie Azure. Oni migrować do aplikacji sieci web maszyny Wirtualnej przy użyciu [usługi Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)i przy użyciu bazy danych aplikacji [usługi Azure Database Migration](https://docs.microsoft.com/azure/dms/dms-overview) usługi, aby przeprowadzić migrację do wystąpienia zarządzanego SQL. | Dostępne
[Artykuł 5: Ponowne hostowanie dla maszyn wirtualnych platformy Azure](contoso-migration-rehost-vm.md) | Pokazuje, jak Contoso migrację SmartHotel ich do maszyn wirtualnych IaaS platformy Azure, przy użyciu usługi Site Recovery.
[Artykuł 6: Ponowne hostowanie dla maszyn wirtualnych platformy Azure i grup dostępności programu SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Pokazuje, jak Contoso migruje SmartHotel aplikacji. Ich Usługa Site Recovery do migrowania aplikacji, maszyny wirtualne i usługi migracji bazy danych do migracji bazy danych aplikacji do grupy dostępności programu SQL Server. | Dostępne
[Artykuł 7: Ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure](contoso-migration-rehost-linux-vm.md) | Pokazuje, jak firmy Contoso jest migrowana ich aplikacji systemu Linux osTicket do maszyn wirtualnych IaaS platformy Azure przy użyciu usługi Azure Site Recovery.
[Artykuł 8: Ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure i serwer Azure MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Pokazuje, jak Contoso migruje osTicket aplikacji systemu Linux. Używają do migracji do wystąpienia serwera MySQL w usłudze Azure Site Recovery na potrzeby migracji maszyny Wirtualnej i połączenia aplikacji MySQL Workbench. | Dostępne
[Artykuł 9: Refaktoryzuj aplikacji do aplikacji sieci Web platformy Azure i usługi Azure SQL Database](contoso-migration-refactor-web-app-sql.md) | Pokazuje, jak Contoso SmartHotel app jest migrowana do aplikacji internetowej platformy Azure opartych na kontenerach i bazy danych aplikacji jest migrowana do usługi Azure SQL Server. | Dostępne
[Artykuł 10: Refaktoryzuj aplikacji systemu Linux w usłudze Azure App Service i Azure serwera MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Pokazuje, jak firmy Contoso jest migrowana osTicket aplikacji systemu Linux do usługi Azure App Service za pomocą kontenera platformy Docker programu PHP 7.0. Baza kodów dla wdrożenia są migrowane do usługi GitHub. Baza danych aplikacji są migrowane do usługi Azure MySQL. | Dostępne
Artykuł 11: Refaktoryzuj wdrożenia programu TFS w usługom DevOps platformy Azure | Migrowanie aplikacji dev TFS do usługom DevOps platformy Azure na platformie Azure | W tym artykule
[Artykuł 12: Ponowne Ustalanie architektury aplikacji na kontenerów platformy Azure i usługi Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Pokazuje, jak Contoso migruje i rearchitects SmartHotel aplikacji na platformie Azure. Przekształcanie ich warstwy sieci web aplikacji jako kontenerów Windows bazy danych oraz aplikacji w usłudze Azure SQL Database. | Dostępne
[Artykuł 13: Ponownie skompiluj aplikację na platformie Azure](contoso-migration-rebuild.md) | Pokazuje, jak Contoso przebudować ich aplikacji SmartHotel, przy użyciu różnych funkcji platformy Azure i usług, w tym usług App Services, Azure Kubernetes, usługi Azure Functions, Cognitive services i usługi Cosmos DB. | Dostępne
[Artykuł 14: Skalowanie migracji na platformę Azure](contoso-migration-scale.md) | Po wypróbowanie kombinacje migracji, Contoso przygotowuje aby możliwe było skalowanie pełną migrację na platformę Azure. | Dostępne


## <a name="business-drivers"></a>Czynniki biznesowe

Zespół kierowniczy IT ma ściśle współpracowała z partnerami biznesowymi, aby zidentyfikować przyszłych celów. Partnerów nie są nadmiernie zaniepokojona narzędzia i technologie, ale przechwycone następujące punkty:

- **Oprogramowanie**: Niezależnie od podstawowej działalności wszyscy producenci są teraz producenci oprogramowania, włącznie z firmy Contoso. Kierownictwo firmy jest zainteresowany jak IT może pomóc prowadzić firmy za pomocą nowej praktyki pracy stosowane dla użytkowników i środowisk dla swoich klientów.
- **Wydajność**: Firma Contoso potrzebuje usprawnić proces i usunąć niepotrzebne procedury dla deweloperów i użytkowników. Dzięki temu firma wydajniej spełniającej wymagania klientów. Potrzeby biznesowe IT na szybkie bez marnowania czasu i pieniędzy.
- **Elastyczność**:  Contoso IT musi odpowiedzieć na potrzeby biznesowe i reagują szybciej niż portalu marketplace w celu włączenia sukces w globalnej gospodarki. IT nie może być blocker firmy.

## <a name="migration-goals"></a>Cele migracji

Zespół chmury firmy Contoso ma przypięty w dół do celów migracji do usługi DevOps platformy Azure:

- Zespół potrzebuje narzędzie do migracji danych do chmury. Kilka procesów ręcznych powinny być potrzebne.
- Dane elementu roboczego i muszą być migrowane historii z ostatniego roku.
- Firma nie chce konfigurowania nowej nazwy użytkownika i hasła. Wszystkie bieżące przypisania systemu muszą zostać zachowane.
- Firma chce przenieść od kontroli wersji Team Foundation (TFVC) do usługi Git do kontroli źródła.
- Jednorazowej migracji do usługi Git będzie "Migracja wierzchołków", który importuje tylko najnowszą wersję kodu źródłowego. Nastąpi podczas przestojów podczas cała praca będzie został zatrzymany, jak zmiany kodu. Rozumieją, że tylko bieżącego historii gałęzi głównej będą dostępne po przeniesieniu.
- One jest sprawą zmiany i chcesz przetestować ją przed wykonaniem pełnej przenoszenia. Firma chce zachować dostęp do programu TFS, nawet po przeniesieniu do usługom DevOps platformy Azure.
- Mają wiele kolekcji i chcesz rozpocząć pracę z taki, który ma tylko kilka projektów, aby lepiej zrozumieć ten proces.
- Rozumieją kolekcji TFS są relacja jeden do jednego z organizacjami usługom DevOps platformy Azure, dzięki czemu będą mieć wiele adresów URL. Odpowiada to jednak ich bieżący model separacji dla projektów i bazach kodu.


## <a name="proposed-architecture"></a>Proponowana architektury

- Contoso przenoszenie swoich projektów TFS do chmury i już nie udostępniać ich projektów lub źródło kontroli w środowisku lokalnym.
- TFS zostaną zmigrowane do usługom DevOps platformy Azure.
- Obecnie firma Contoso ma jednej kolekcji TFS o nazwie **ContosoDev**, które będą migrowane do organizacji usługom DevOps platformy Azure o nazwie **contosodevmigration.visualstudio.com**.
- Projekty, elementy robocze, błędy i iteracji w ciągu ostatniego roku zostaną zmigrowane do usługom DevOps platformy Azure.
- Firma Contoso będzie korzystać z usługą Azure Active Directory, które są ustawione podczas ich [wdrożyć swoją infrastrukturę platformy Azure](contoso-migration-infrastructure.md) na początku ich planowania migracji. 


![Architektura scenariusza](./media/contoso-migration-tfs-vsts/architecture.png) 


## <a name="migration-process"></a>Proces migracji

Contoso zakończy proces migracji w następujący sposób:

1. Zaangażowanych jest wiele przygotowania. Pierwszym krokiem firma Contoso potrzebuje do uaktualnienia ich wdrożenia serwera TFS na obsługiwany poziom. Firma Contoso jest obecnie uruchomiona TFS 2017 Update 3, ale do migracji bazy danych musi działać obsługiwana wersja 2018 z najnowszymi aktualizacjami.
2. Po uaktualnieniu Contoso będzie uruchomić narzędzie do migracji serwera TFS i sprawdzić ich kolekcji.
3. Contoso Tworzenie zestawu plików, przygotowywania i wykonać uruchomienia próbnego migracji do testowania.
4. Firma Contoso będzie następnie uruchom kolejną migrację, tym razem pełnej migracji, który zawiera elementy robocze, błędy, sprintach i kodu.
5. Po zakończeniu migracji Contoso przejdzie swój kod z TFVC do usługi Git.

![Proces migracji](./media/contoso-migration-tfs-vsts/migration-process.png) 


## <a name="scenario-steps"></a>Kroki w scenariuszu

Poniżej przedstawiono, jak przeprowadzić migrację przez firmy Contoso:

> [!div class="checklist"]
> * **Krok 1. Tworzenie konta usługi Azure storage**: To konto magazynu będzie używane podczas procesu migracji.
> * **Krok 2. Uaktualnij TFS**: Contoso uaktualni ich wdrożenie do wersji TFS 2018 uaktualnienia 2. 
> * **Krok 3. Sprawdź poprawność kolekcji**: Contoso zostanie przeprowadzona Weryfikacja kolekcji TFS w ramach przygotowania do migracji.
> * **Krok 4. Kompiluj plik przygotowywania**: Contoso utworzy pliki migracji przy użyciu narzędzia migracji w programie TFS. 


## <a name="step-1-create-a-storage-account"></a>Krok 1: Tworzenie konta magazynu

1. W witrynie Azure portal firmy Contoso administratorom tworzenie konta magazynu (**contosodevmigration**).
2. Konta mogą umieścić w ich regionu pomocniczego, których używają do trybu failover — środkowe stany USA. Używają standardowych konto ogólnego przeznaczenia z magazynem lokalnie nadmiarowym.

    ![Konto magazynu](./media/contoso-migration-tfs-vsts/storage1.png) 


**Potrzebujesz dodatkowej pomocy?**

- [Wprowadzenie do usługi Azure storage](https://docs.microsoft.com/azure/storage/common/storage-introduction).
- [Tworzenie konta magazynu](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).


## <a name="step-2-upgrade-tfs"></a>Krok 2: Uaktualnij TFS

Administratorzy firmy Contoso uaktualniania serwera TFS do wersji TFS 2018 Update 2. Przed rozpoczęciem:

- Będą oni mogli pobrać [TFS 2018 Update 2](https://visualstudio.microsoft.com/downloads/)
- Weryfikują [wymagania sprzętowe](https://docs.microsoft.com/tfs/server/requirements)oraz artykuł za pośrednictwem [informacje o wersji](https://docs.microsoft.com/visualstudio/releasenotes/tfs2018-relnotes) i [uaktualnienia pytania dotyczące usługi](https://docs.microsoft.com/tfs/server/upgrade/get-started#before-you-upgrade-to-tfs-2018).

Uaktualnienie w następujący sposób:

1. Aby rozpocząć, Utwórz kopię zapasową ich serwera TFS (uruchomiony na maszynie wirtualnej VMware) i migawki VMware.

    ![TFS](./media/contoso-migration-tfs-vsts/upgrade1.png) 

2. Zostanie uruchomiony Instalator programu TFS, a ich wybierz lokalizację instalacji. Instalator wymaga dostępu do Internetu.

    ![TFS](./media/contoso-migration-tfs-vsts/upgrade2.png) 

3. Po zakończeniu instalacji zostanie uruchomiony Kreator konfiguracji serwera.

    ![TFS](./media/contoso-migration-tfs-vsts/upgrade3.png) 

4. Po zakończeniu weryfikacji zakończeniu działania Kreatora uaktualniania.

     ![TFS](./media/contoso-migration-tfs-vsts/upgrade4.png) 

5. One Zweryfikuj instalację TFS, przeglądając projektów, elementów roboczych i kodu.

     ![TFS](./media/contoso-migration-tfs-vsts/upgrade5.png) 

> [!NOTE]
> Niektóre uaktualnienia programu TFS, należy po zakończeniu uaktualniania, uruchom Kreatora konfigurowania funkcji. [Dowiedz się więcej](https://docs.microsoft.com/azure/devops/reference/configure-features-after-upgrade?utm_source=ms&utm_medium=guide&utm_campaign=vstsdataimportguide&view=vsts).

**Potrzebujesz dodatkowej pomocy?**

Dowiedz się więcej o [uaktualniania programu TFS](https://docs.microsoft.com/tfs/server/upgrade/get-started).

## <a name="step-3-validate-the-tfs-collection"></a>Krok 3: Sprawdź poprawność kolekcji TFS

Uruchom narzędzie do migracji serwera TFS w bazie danych kolekcji ContosoDev Aby zweryfikować, czy przed migracją administratorów firmy Contoso.

1. Pobierz i Rozpakuj [narzędzia do migracji serwera TFS](https://www.microsoft.com/download/details.aspx?id=54274). Jest ważne pobrać wersję dla aktualizacji TFS, która jest uruchomiona. Wersji można sprawdzić w konsoli administracyjnej.

    ![TFS](./media/contoso-migration-tfs-vsts/collection1.png)

2. One Uruchom narzędzie do wykonywania sprawdzania poprawności, określając adres URL kolekcji projektu:

   **TfsMigrator zweryfikować /collection:http:\//contosotfs:8080/tfs/ContosoDev**


3. W narzędziu jest wyświetlany błąd.

    ![TFS](./media/contoso-migration-tfs-vsts/collection2.png)

5. Się one dziennika pliki znajdują się w **dzienniki** folderu tuż przed lokalizacji narzędzia. Plik dziennika jest generowany dla poszczególnych głównych sprawdzania poprawności. **TfsMigration.log** przechowuje informacje głównego.

    ![TFS](./media/contoso-migration-tfs-vsts/collection3.png)

4. Znajdź one ten wpis, związane z tożsamościami.

    ![TFS](./media/contoso-migration-tfs-vsts/collection4.png)

5. Zakres ich działania **TfsMigration zweryfikować/Help** w wierszu polecenia i zobacz, że polecenie **/tenantDomainName** wydaje się być wymagane do weryfikacji tożsamości.

     ![TFS](./media/contoso-migration-tfs-vsts/collection5.png)

6. Uruchom ponownie polecenie sprawdzania poprawności i Uwzględnij tę wartość, wraz z ich nazwy usługi Azure AD: **TfsMigrator zweryfikować /collection:http:\//contosotfs:8080/tfs/ContosoDev /tenantDomainName:contosomigration.onmicrosoft.com**.

    ![TFS](./media/contoso-migration-tfs-vsts/collection7.png)

7. Pojawi się w usłudze Azure AD ekran logowania, a użytkownik podał poświadczenia administratora globalnego.

     ![TFS](./media/contoso-migration-tfs-vsts/collection8.png)

8. Sprawdzanie poprawności przekazuje i został potwierdzony przez narzędzie.

    ![TFS](./media/contoso-migration-tfs-vsts/collection9.png)



## <a name="step-4-create-the-migration-files"></a>Krok 4: Tworzenie plików migracji

Za pomocą Walidowanie ukończone Administratorzy Contoso służy narzędzie do migracji serwera TFS do tworzenia plików migracji.

1. Działają one w kroku przygotowania w narzędziu.

    **TfsMigrator przygotowanie /collection:http:\//contosotfs:8080/tfs/ContosoDev /tenantDomainName:contosomigration.onmicrosoft.com /accountRegion:cus**

     ![Przygotowanie](./media/contoso-migration-tfs-vsts/prep1.png)

    Przygotowanie wykonuje następujące czynności:
    - Skanuje kolekcji, aby uzyskać listę wszystkich użytkowników i wypełnia dziennika mapy identyfikowanie (**IdentityMapLog.csv**])
    - Przygotowuje połączenia usługi Azure Active Directory w celu znalezienia dopasowania dla każdej tożsamości.
    - Firmy Contoso ma już wdrożone usługi Azure AD i synchronizowane za pomocą AD Connect, więc przygotowanie powinien móc znaleźć pasujące tożsamości i oznacz je jako aktywny.

2. Pojawi się w usłudze Azure AD ekran logowania, a następnie je wprowadzi Administrator globalny.

    ![Przygotowanie](./media/contoso-migration-tfs-vsts/prep2.png)

3. Przygotowanie zakończeniu i narzędzie zgłasza, że pliki import został pomyślnie wygenerowany.

    ![Przygotowanie](./media/contoso-migration-tfs-vsts/prep3.png)

4. Mogą teraz zobaczyć, że zarówno IdentityMapLog.csv, jak i plik import.json została utworzona w nowym folderze.

    ![Przygotowanie](./media/contoso-migration-tfs-vsts/prep4.png)

5. Plik import.json zawiera ustawienia importowania. Zawiera informacje, takie jak nazwa żądanego organizacji i informacje o koncie magazynu. Najczęściej pola są wypełniane automatycznie. Niektóre pola wymagane dane wejściowe użytkownika. Contoso otwiera plik i dodaje nazwę organizacji usługom DevOps platformy Azure, który ma zostać utworzony: **contosodevmigration**. Ta nazwa będzie swój adres URL usługi DevOps platformy Azure **contosodevmigration.visualstudio.com**.

    ![Przygotowanie](./media/contoso-migration-tfs-vsts/prep5.png)

    > [!NOTE]
    > Organizacja musi zostać utworzone przed migracją, można zmienić po zakończeniu migracji.

6. Przejrzyj ich pliku mapy tożsamości dziennika, który zawiera konta, które zostaną włączone do usługom DevOps platformy Azure podczas importowania. 

   - Aktywne tożsamości można znaleźć tożsamości, które staną się użytkowników w usługach infrastruktury DevOps platformy Azure po zaimportowaniu.
   - Na usługom DevOps platformy Azure te tożsamości będzie ono licencjonowane i są wyświetlane jako użytkownik w organizacji, które po migracji.
   - Te tożsamości są oznaczone jako **Active** w **oczekiwany stan importu** kolumna w pliku.

     ![Przygotowanie](./media/contoso-migration-tfs-vsts/prep6.png)



## <a name="step-5-migrate-to-azure-devops-services"></a>Krok 5. Migracja do usługi Azure DevOps Services

Przygotowanie w miejscu Administratorzy Contoso teraz skoncentrować się na temat migracji. Po uruchomieniu migracji, mogą przełączać z zastosowaniem funkcji TFVC do usługi Git do kontroli wersji.

Przed rozpoczęciem, Administratorzy zaplanować przestój zespołowi deweloperów podjęcie kolekcji w trybie offline dla migracji. Poniżej przedstawiono kroki procesu migracji:

1. **Odłącz kolekcję**: Dane tożsamości dla kolekcji znajdują się w bazie danych konfiguracji serwera TFS, gdy kolekcja jest dołączona i online. Odłączeniem kolekcji z serwera TFS ma kopię danych tożsamości i pakietów go z kolekcji dla transportu. Bez tych danych nie można wykonać część tożsamości importu. Zaleca się, że kolekcja pozostają odłączone ukończenie importu, ponieważ nie ma możliwości zaimportować zmiany, które wystąpiły podczas importowania.
2. **Generowanie kopii zapasowej**: Następnym krokiem procesu migracji jest do generowania kopii zapasowych, które mogą być importowane do usługom DevOps platformy Azure. Warstwy danych aplikacji składnika pakietów (DACPAC) to funkcja programu SQL Server, która umożliwia zmian w bazie danych umieszczonych w jednym pliku i wdrożone do innych wystąpień programu SQL Server. Również można przywrócić bezpośrednio do usługi DevOps platformy Azure i w związku z tym jest używana jako metoda tworzenia pakietów w celu uzyskania danych kolekcji w chmurze. Firma Contoso będzie narzędzie SqlPackage.exe do generowania pliku DACPAC. To narzędzie jest dostępne w programie SQL Server Data Tools.
3. **Przekaż do magazynu**: Po utworzeniu pakietu DACPAC przekazują je do usługi Azure Storage. Po przekazaniu, otrzymują sygnatury dostępu współdzielonego (SAS), aby zezwolić na dostęp narzędzia do migracji serwera TFS do magazynu.
4. **Wypełnianie importu**: Contoso można następnie wypełnij brakujące pola w pliku importu, włączając ustawienie pliku DACPAC. Na początek z ich określisz chcą wykonywać **próbnym uruchamianiem** importu, aby sprawdzić, czy wszystko działa poprawnie przed pełnej migracji.
5. **Czy uruchomienia próbnego**: Uruchamianie próbnego importuje migracja kolekcji testu pomocy. Uruchomienia próbnego mają ograniczone życia i zostaną usunięte przed uruchomieniem migracji produkcji. Są one usuwane automatycznie po ustawionym okresie czasu. Uwaga dotycząca usunięcia uruchomienia próbnego znajduje się w wiadomości e-mail powodzeniu odebrany po zakończeniu importowania. Zwróć uwagę i odpowiednio zaplanować.
6. **Kończenie migracji w środowisku produkcyjnym**: Migracja uruchomienia próbnego została zakończona Administratorzy Contoso wykonaj ostateczną migracją aktualizowanie import.json, a następnie ponownie uruchomić importowanie.



### <a name="detach-the-collection"></a>Odłącz kolekcję

Przed rozpoczęciem Administratorzy Contoso podjąć przed odłączeniem lokalna kopia zapasowa programu SQL Server i VMware migawkę serwera TFS.

1.  W konsoli administracyjnej TFS, wybierają kolekcji chcą odłączyć (**ContosoDev**).

    ![Migrate (Migracja)](./media/contoso-migration-tfs-vsts/migrate1.png)

2. W **ogólne**, wybierają **odłączania kolekcji**

    ![Migrate (Migracja)](./media/contoso-migration-tfs-vsts/migrate2.png)

3. W Kreatorze odłączania kolekcji projektu zespołowego > **wiadomość dotycząca obsługi**, zapewniają komunikatu dla użytkowników, którzy mogą próbować nawiązać połączenie z projektów w kolekcji.

    ![Migrate (Migracja)](./media/contoso-migration-tfs-vsts/migrate3.png)

4. W **postęp odłączania**, monitorować postęp i kliknij przycisk **dalej** po zakończeniu procesu.

    ![Migrate (Migracja)](./media/contoso-migration-tfs-vsts/migrate4.png)

5. W **kontroli gotowości**, kiedy sprawdzania ich kliknij **Odłącz**.

    ![Migrate (Migracja)](./media/contoso-migration-tfs-vsts/migrate5.png)

6. Polecenie **Zamknij** na zakończenie.

    ![Migrate (Migracja)](./media/contoso-migration-tfs-vsts/migrate6.png)

6. Kolekcja nie jest już przywoływany w konsoli administracyjnej TFS.

    ![Migrate (Migracja)](./media/contoso-migration-tfs-vsts/migrate7.png)


### <a name="generate-a-dacpac"></a>Generowanie pliku DACPAC

Contoso tworzy kopię zapasową (DACPAC) do zaimportowania do usługi DevOps platformy Azure.

- SqlPackage.exe w programie SQL Server Data Tools jest używany do utworzenia pakietu DACPAC. Istnieje wiele wersji instalowany z programu SQL Server Data Tools, znajduje się w folderach, przy użyciu nazwy, takie jak 120, 130 i 140 SqlPackage.exe. Koniecznie Użyj właściwej wersji do przygotowania pakietu DACPAC.
- Importy serwera TFS 2018 muszą używać SqlPackage.exe z folderu 140 lub nowszej.  Aby uzyskać CONTOSOTFS ten plik znajduje się w folderze: **C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\140**.


Administratorzy firmy Contoso wygenerowanie pakietu DACPAC w następujący sposób:

1. One Otwórz wiersz polecenia i przejdź do lokalizacji SQLPackage.exe. Tego następujące polecenie, aby wygenerować DACPAC one wpisz:

    **SqlPackage.exe /sourceconnectionstring: "źródło danych = SQLSERVERNAME\INSTANCENAME; Initial Catalog = Tfs_ContosoDev; Integrated Security = True" /targetFile:C:\TFSMigrator\Tfs_ContosoDev.dacpac /action:extract /p:ExtractAllTableData = true /p: I gnoreUserLoginMappings = true /p:IgnorePermissions = true /p:Storage = pamięci** 

    ![Backup](./media/contoso-migration-tfs-vsts/backup1.png)

2. Po uruchomieniu polecenia, pojawia się następujący komunikat.

    ![Backup](./media/contoso-migration-tfs-vsts/backup2.png)

3. Weryfikują właściwości pliku DACPAC

    ![Backup](./media/contoso-migration-tfs-vsts/backup3.png)

### <a name="update-the-file-to-storage"></a>Aktualizowanie pliku do magazynu

Po utworzeniu pakietu DACPAC Contoso przekazuje go do usługi Azure Storage.

1. One [Pobierz i zainstaluj](https://azure.microsoft.com/features/storage-explorer/) Eksploratora usługi Azure Storage.

    ![Upload](./media/contoso-migration-tfs-vsts/backup5.png)

4. Połącz z ich subskrypcją i Znajdź konto magazynu, które są tworzone w związku z migracją (**contosodevmigration**). Tworzą nowy kontener obiektów blob **azuredevopsmigration**.

    ![Upload](./media/contoso-migration-tfs-vsts/backup6.png)

5. Określają pliku DACPAC w celu przekazania jako blokowy obiekt blob.

    ![Upload](./media/contoso-migration-tfs-vsts/backup7.png)
    
7. Po przekazaniu pliku użytkownika kliknij przycisk Nazwa pliku > **Generowanie sygnatury dostępu Współdzielonego**. Rozwiń kontenery obiektów blob na koncie magazynu, wybierz kontener z plikami importu i kliknij przycisk **Uzyskaj sygnaturę dostępu współdzielonego**.

    ![Upload](./media/contoso-migration-tfs-vsts/backup8.png)

8. Zaakceptuj ustawienia domyślne i kliknij przycisk **Utwórz**. Dzięki temu dostęp przez 24 godziny.

    ![Upload](./media/contoso-migration-tfs-vsts/backup9.png)

9. Kopiują udostępnione adres URL sygnatury dostępu, dzięki czemu może służyć za pomocą narzędzia migracji w programie TFS.

    ![Upload](./media/contoso-migration-tfs-vsts/backup10.png)

> [!NOTE]
> Migracja musi się zdarzyć, zanim w przydzielonym czasie wygaśnie okna lub uprawnień.
> Nie można wygenerować klucza sygnatury dostępu Współdzielonego w witrynie Azure portal. Klucze generowane następująco są w zakresie konta i nie będzie działać z importu.

### <a name="fill-in-the-import-settings"></a>Wypełnij ustawienia importu

Wcześniej Administratorzy Contoso częściowo wypełnione plik specyfikacji importu (import.json). Teraz należy dodać pozostałe ustawienia.

Otwórz plik import.json i wypełnij następujące pola: • lokalizacji: Lokalizacja klucza sygnatury dostępu Współdzielonego, który został wygenerowany powyżej.
•   Dacpac: Ustaw nazwę pliku DACPAC, który został przekazany do konta magazynu. Rozszerzenie ".dacpac".
• ImportType: Teraz Ustaw próbnym uruchamianiem.


![Importowanie ustawień](./media/contoso-migration-tfs-vsts/import1.png)


### <a name="do-a-dry-run-migration"></a>Przeprowadzić migrację przebiegu

Administratorzy firmy Contoso rozpoczynać migracji do uruchomienia próbnego, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.

1. Otwórz wiersz polecenia i znajdź miejsce TfsMigration (C:\TFSMigrator).
2. Pierwszym krokiem sprawdzają poprawność pliku importu. Chce mieć pewność, że plik jest prawidłowo sformatowany i czy działa klucza sygnatury dostępu Współdzielonego.

    **TfsMigrator import /importFile:C:\TFSMigrator\import.json /validateonly**

3. Sprawdzanie poprawności zwraca błąd, który klucz sygnatury dostępu Współdzielonego musi dłuższy czas wygaśnięcia.

    ![Uruchomienia próbnego](./media/contoso-migration-tfs-vsts/test1.png)

3. Używają Eksploratora usługi Azure Storage, aby utworzyć nowy klucz sygnatury dostępu Współdzielonego z upływem siedmiu dni.

    ![Uruchomienia próbnego](./media/contoso-migration-tfs-vsts/test2.png)

3. One należy zaktualizować plik import.json i uruchom ponownie sprawdzenie poprawności. Tym razem zostanie pomyślnie zakończona.

    **TfsMigrator import /importFile:C:\TFSMigrator\import.json /validateonly**

    ![Uruchomienia próbnego](./media/contoso-migration-tfs-vsts/test3.png)
    
7. Uruchamiają uruchomienia próbnego:

    **TfsMigrator import /importFile:C:\TFSMigrator\import.json**

8. Aby potwierdzić migrację, zgłaszany jest komunikat. Należy pamiętać, czas, dla której będzie przechowywany użycia przemieszczonych danych po uruchomienia próbnego.

    ![Uruchomienia próbnego](./media/contoso-migration-tfs-vsts/test4.png)

9. Usługi Azure AD Zaloguj pojawia się, a powinien korzystanie z logowania administratora firmy Contoso.

    ![Uruchomienia próbnego](./media/contoso-migration-tfs-vsts/test5.png)

10. Komunikat przedstawia informacje dotyczące importowania.

    ![Uruchomienia próbnego](./media/contoso-migration-tfs-vsts/test6.png)

11. Po 15 minut przejdź pod adres URL, a widoczne następujące informacje:

     ![Uruchomienia próbnego](./media/contoso-migration-tfs-vsts/test7.png)

12. Po zakończeniu migracji pracy podpisuje Contoso Dev z potencjalnymi klientami do usługom DevOps platformy Azure, aby sprawdzić, czy uruchomienia próbnego działały prawidłowo. Po uwierzytelnieniu usługom DevOps platformy Azure wymaga kilku szczegółów, aby potwierdzić organizacji.

    ![Uruchomienia próbnego](./media/contoso-migration-tfs-vsts/test8.png)

13. W usługom DevOps platformy Azure potencjalny klient Dev można zobaczyć, że projekty zostały zmigrowane do usługom DevOps platformy Azure. Istnieje powiadomienie, że organizacji zostaną usunięte w ciągu 15 dni.

    ![Uruchomienia próbnego](./media/contoso-migration-tfs-vsts/test9.png)

14. Potencjalny klient Dev otwiera jednego z projektów i otwiera **elementów roboczych** > **przypisane do mnie**. Pokazuje to, że danych elementów roboczych zostanie poddany migracji, wraz z tożsamością.

    ![Uruchomienia próbnego](./media/contoso-migration-tfs-vsts/test10.png)

15. Potencjalny klient sprawdza również innych projektów i kodu, aby upewnić się, że kod źródłowy i Historia została zmigrowana.

    ![Uruchomienia próbnego](./media/contoso-migration-tfs-vsts/test11.png)


### <a name="run-the-production-migration"></a>Uruchom migrację w środowisku produkcyjnym

Za pomocą ukończone uruchomienia próbnego Administratorzy Contoso przejść do migracji w środowisku produkcyjnym. Ich usuwanie uruchomienia próbnego, zaktualizuj ustawienia importowania i ponownie uruchom importowanie.

1. W portalu Azure DevOps usług mogą usunąć organizację uruchomienia próbnego.
2. Zaktualizowanie pliku import.json, aby ustawić **ImportType** do **ProductionRun**.

    ![Produkcja](./media/contoso-migration-tfs-vsts/full1.png)

3. Tak samo, jak dla uruchomienia próbnego uruchamiania migracji: **TfsMigrator import /importFile:C:\TFSMigrator\import.json**.
4. Komunikat przedstawia na potwierdzenie migracji i wyświetli ostrzeżenie, że danych może być przechowywany w bezpiecznym miejscu taki obszar przygotowawczy przez maksymalnie siedem dni.

    ![Produkcja](./media/contoso-migration-tfs-vsts/full2.png)

5. W usłudze Azure AD Sign In określają logowania administratora firmy Contoso.

    ![Produkcja](./media/contoso-migration-tfs-vsts/full3.png)

6. Komunikat przedstawia informacje dotyczące importowania.

    ![Produkcja](./media/contoso-migration-tfs-vsts/full4.png)

7. Po około 15 minut, przejdź do adresu URL, a widzi następujące informacje:

    ![Produkcja](./media/contoso-migration-tfs-vsts/full5.png)

8. Po zakończeniu migracji prowadzić deweloperów firmy Contoso loguje się usługom DevOps platformy Azure do sprawdzenia, czy migracja działały prawidłowo. Po zalogowaniu on zobaczyć, że projekty zostały poddane migracji.

    ![Produkcja](./media/contoso-migration-tfs-vsts/full6.png)

8. Potencjalny klient Dev otwiera jednego z projektów i otwiera **elementów roboczych** > **przypisane do mnie**. Pokazuje to, że danych elementów roboczych zostanie poddany migracji, wraz z tożsamością.

    ![Produkcja](./media/contoso-migration-tfs-vsts/full7.png)

9. Potencjalny klient sprawdza, czy innych danych elementu roboczego, aby potwierdzić.

    ![Produkcja](./media/contoso-migration-tfs-vsts/full8.png)

15. Potencjalny klient sprawdza również innych projektów i kodu, aby upewnić się, że kod źródłowy i Historia została zmigrowana.

    ![Produkcja](./media/contoso-migration-tfs-vsts/full9.png)


### <a name="move-source-control-from-tfvc-to-git"></a>Przenoszenie kontroli źródła z TFVC do usługi GIT

Za pomocą po zakończeniu migracji firma Contoso chce przenieść z TFVC do usługi Git do zarządzania kodem źródłowym. Muszą oni zaimportowanie kodu źródłowego, obecnie w organizacji usługom DevOps platformy Azure jako repozytoriów usługi Git w tej samej organizacji.

1. W portalu Azure DevOps usług otwarciu, jednym z repozytoriów TFVC (**$/ PolicyConnect**) i przejrzyj go.

    ![Usługa Git](./media/contoso-migration-tfs-vsts/git1.png)

2. Polecenie **źródła** listy rozwijanej > **importu**.

    ![Usługa Git](./media/contoso-migration-tfs-vsts/git2.png)

3. W **typ źródła** wybierają **TFVC**, a następnie określ ścieżkę do repozytorium. Decydujesz się one rezygnacji z migracji historii.

    ![Usługa Git](./media/contoso-migration-tfs-vsts/git3.png)

    > [!NOTE]
    > Z powodu różnic w jak przechowywać informacje o kontroli wersji TFVC i Git zaleca się, że Contoso nie migrować historii. To podejście, które miały firmy Microsoft, po jego migracji Windows i innych produktów z scentralizowany system kontroli wersji Git.

4. Po zaimportowaniu Administratorzy przejrzeć kod.

    ![Usługa Git](./media/contoso-migration-tfs-vsts/git4.png)

5. One Powtórz procedurę dla drugiej repozytorium (**$/ SmartHotelContainer**).

    ![Usługa Git](./media/contoso-migration-tfs-vsts/git5.png)

6. Po zapoznaniu się ze źródłem, prowadzi Dev zgodę, odbywa się migracja do usługi DevOps platformy Azure. Usługi Azure DevOps teraz staje się źródła dla wszystkich rozwoju w obrębie zespołów zajmujących się migracji.

    ![Usługa Git](./media/contoso-migration-tfs-vsts/git6.png)



**Potrzebujesz dodatkowej pomocy?**

[Dowiedz się więcej](https://docs.microsoft.com/azure/devops/repos/git/import-from-TFVC?view=vsts) o importowaniu z TFVC.

##  <a name="clean-up-after-migration"></a>Wyczyść zasoby po ukończeniu migracji

Za pomocą po zakończeniu migracji Contoso musi wykonać następujące czynności:

- Przegląd [importu po](https://docs.microsoft.com/azure/devops/articles/migration-post-import?view=vsts) artykuł dotyczący informacji na temat importowania dodatkowych działań.
- Usuń repozytoriów TFVC lub umieść je w trybie tylko do odczytu. Kod, który nie może podstaw używane, ale można odwoływać się do jego historii.

## <a name="next-steps"></a>Kolejne kroki

Contoso będzie musiał przeszkolenie usługom DevOps platformy Azure i usługą Git dla członków zespołu odpowiednie.



