---
title: Refaktoryzuj wdrożenia Team Foundation Server do programu Visual Studio Team Services (VSTS) na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Contoso refactors jego lokalnego wdrożenia programu TFS, migrując je do programu Visual Studio Team Services (VSTS) na platformie Azure.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: raynew
ms.openlocfilehash: f854c269eaf65335fb5654709fe98857d11865b2
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190139"
---
# <a name="contoso-migration--refactor-a-team-foundation-server-deployment-to-visual-studio-team-services-vsts"></a>Migracja Contoso: Refaktoryzacja wdrożenia Team Foundation Server do programu Visual Studio Team Services (VSTS)

W tym artykule pokazano, jak Contoso pracuje nad refaktoryzacją jego lokalnego wdrożenia Team Foundation Server (TFS), migrując je do programu Visual Studio Team Services (VSTS) na platformie Azure. Zespół programistyczny firmy Contoso użyto ostatnich pięciu lat. TFS do pracę zespołową i kontroli źródła. Teraz zespół chce przenieść do rozwiązania oparte na chmurze do tworzenia i testowania pracy i do kontroli źródła. Usługa VSTS będzie odtwarzany roli, jak zespół przenieść do modelu DevOps i opracowywanie nowych aplikacji natywnych dla chmury.

W tym dokumencie jest jednym z serii artykułów, które pokazują, jak fikcyjnej firmy Contoso migruje swoje zasoby lokalne do chmury Microsoft Azure. Seria zawiera dodatkowe informacje i scenariusze, które ilustrują sposób konfigurowania infrastruktury migracji i uruchamiania różnych rodzajów migracji. Scenariusze zwiększanie się stopnia skomplikowania. Dodamy dodatkowe artykuły wraz z upływem czasu.


**Artykuł** | **Szczegóły** | **Stan**
--- | --- | ---
[Artykuł 1: omówienie](contoso-migration-overview.md) | Omówienie serię artykułów, strategia migracji do firmy Contoso i przykładowe aplikacje, które są używane w tej serii. | Dostępne
[Artykuł 2: Wdrażanie infrastruktury platformy Azure](contoso-migration-infrastructure.md) | Contoso przygotowuje swoją infrastrukturę lokalną i jej infrastruktury platformy Azure do migracji. Tej samej infrastruktury jest używany dla wszystkich artykułów migracji w serii. | Dostępne
[Artykuł 3: Ocena zasobów lokalnych do migracji na platformę Azure](contoso-migration-assessment.md)  | Contoso uruchamia oceny aplikacji SmartHotel jej w środowisku lokalnym, działające w programie VMware. Contoso ocenia aplikacji maszyn wirtualnych przy użyciu usługi Azure Migrate bazy danych oraz aplikacji programu SQL Server przy użyciu Data Migration Assistant. | Dostępne
[Artykuł 4: Ponowne hostowanie aplikacji na maszynie Wirtualnej platformy Azure oraz wystąpienie zarządzane usługi SQL Database](contoso-migration-rehost-vm-sql-managed-instance.md) | Firmy Contoso jest uruchamiana lift-and-shift migracja na platformę Azure dla swoich aplikacji SmartHotel w środowisku lokalnym. Firma Contoso jest migrowana aplikację frontonu maszyn wirtualnych za pomocą [usługi Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso bazy danych aplikacji jest migrowana do wystąpienia zarządzanego Azure SQL Database przy użyciu [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Dostępne  
[Artykuł 5: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure](contoso-migration-rehost-vm.md) | Contoso migruje swojej aplikacji SmartHotel maszyn wirtualnych do maszyn wirtualnych platformy Azure przy użyciu usługi Site Recovery. | Dostępne
[Artykuł 6: Ponowne hostowanie dla maszyn wirtualnych platformy Azure i grup dostępności programu SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Contoso migruje SmartHotel aplikacji. Firma Contoso używa Usługa Site Recovery do migrowania aplikacji maszyn wirtualnych. Usługa migracji bazy danych używa migrować bazę danych aplikacji do klastra programu SQL Server, który jest chroniony przez grupy dostępności AlwaysOn. | Dostępne
[Artykuł 7: Ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure](contoso-migration-rehost-linux-vm.md) | Contoso zakończeniu migracji lift-and-shift aplikacji osTicket systemu Linux na maszynach wirtualnych platformy Azure, za pomocą usługi Azure Site Recovery | Dostępne
[Artykuł 8: Ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure i serwer Azure MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Contoso aplikacji osTicket systemu Linux jest migrowana do maszyn wirtualnych platformy Azure przy użyciu usługi Azure Site Recovery i bazy danych aplikacji jest migrowana do wystąpienia serwera usługi Azure MySQL za pomocą aplikacji MySQL Workbench. | Dostępne
[Artykuł 9: Refaktoryzacja aplikację w usłudze Azure Web Apps i Azure SQL database](contoso-migration-refactor-web-app-sql.md) | Contoso SmartHotel app jest migrowana do aplikacji sieci Web platformy Azure i wykonuje migrację bazy danych aplikacji na wystąpienie serwera SQL Azure przy użyciu Asystenta migracji bazy danych | Dostępne
[Artykuł 10: Refaktoryzacja aplikacji systemu Linux w usłudze Azure Web Apps i usługi Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso migruje swoją aplikację osTicket systemu Linux do aplikacji sieci web platformy Azure w wielu regionach platformy Azure przy użyciu usługi Azure Traffic Manager, zintegrowana z usługą GitHub ciągłego dostarczania. Contoso migruje bazy danych aplikacji do usługi Azure Database for MySQL — wystąpienia. | Dostępne 
Artykuł 11: Refaktoryzacja TFS w usłudze VSTS | Contoso migruje jego lokalnego wdrożenia Team Foundation Server do usługi Visual Studio Team Services na platformie Azure. | W tym artykule.
[Artykuł 12: Przekształcanie aplikacji kontenerów platformy Azure i usługi Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Contoso migruje jego SmartHotel aplikacji na platformie Azure. Następnie rearchitects warstwy sieci web aplikacji jako kontener Windows w usłudze Azure Service Fabric i bazę danych za pomocą usługi Azure SQL Database. | Dostępne
[Artykuł 13: Ponownie skompilować aplikację na platformie Azure](contoso-migration-rebuild.md) | Contoso odbudowuje swojej aplikacji SmartHotel, korzystając z możliwości platformy Azure i usług, w tym usługi Azure App Service, Azure Kubernetes Service (AKS), usługi Azure Functions, Azure Cognitive Services i Azure Cosmos DB. | Dostępne



## <a name="business-drivers"></a>Czynniki biznesowe

Zespół kierowniczy IT ma ściśle współpracowała z partnerami biznesowymi, aby zidentyfikować przyszłych celów. Partnerów nie są nadmiernie zaniepokojona narzędzia i technologie, ale przechwycone następujące punkty:

- **Oprogramowanie**: niezależnie od podstawowej działalności, wszyscy producenci są teraz producenci oprogramowania, włącznie z firmy Contoso. Kierownictwo firmy jest zainteresowany jak IT może pomóc prowadzić firmy za pomocą nowej praktyki pracy stosowane dla użytkowników i środowisk dla swoich klientów.
- **Wydajność**: firma Contoso potrzebuje usprawnić proces i usunąć niepotrzebne procedury dla deweloperów i użytkowników. Dzięki temu firma wydajniej spełniającej wymagania klientów. Potrzeby biznesowe IT na szybkie bez marnowania czasu i pieniędzy.
- **Elastyczność**: Contoso IT musi odpowiedzieć na potrzeby biznesowe i reagują szybciej niż portalu marketplace w celu włączenia sukces w globalnej gospodarki. IT nie może być blocker firmy.

## <a name="migration-goals"></a>Cele migracji

Zespół chmury firmy Contoso ma przypięty w dół do celów migracji do usługi VSTS:

- Zespół potrzebuje narzędzie do migracji danych do chmury. Kilka procesów ręcznych powinny być potrzebne.
- Dane elementu roboczego i muszą być migrowane historii z ostatniego roku.
- Zespół nie chce skonfigurować nowe nazwy użytkownika i hasła. Wszystkie bieżące przypisania systemu muszą zostać zachowane.
- Zespół chce przenieść od kontroli wersji Team Foundation (TFVC) do usługi Git do kontroli źródła.
- Jednorazowej migracji do usługi Git będzie "Migracja wierzchołków", który importuje tylko najnowszą wersję kodu źródłowego. Nastąpi podczas przestojów podczas cała praca będzie został zatrzymany, jak zmiany kodu. Zespół rozumie, że tylko bieżącego historii gałęzi głównej będą dostępne po przeniesieniu.
- Zespół jest zajmującym się ochroną zmiany i chcesz przetestować ją przed wykonaniem pełnej przenoszenia. Zespół chce zachować dostęp do programu TFS, nawet po przeniesieniu do usługi VSTS.
- Firma Contoso ma wiele kolekcji, a ma być uruchamiany przy użyciu jednego, który ma tylko kilka projektów, aby lepiej zrozumieć ten proces.
- Zespół rozumie, że kolekcji TFS są relacja jeden do jednego z kont usługi VSTS, przy użyciu wielu adresów URL. Jednak to pasuje do bieżącego modelu separacji dla projektów i bazach kodu.


## <a name="proposed-architecture"></a>Proponowana architektury

- Contoso przenoszenie swoich projektów TFS do chmury i już nie udostępniać ich projektów lub źródło kontroli w środowisku lokalnym.
- TFS zostaną poddane migracji do usługi VSTS.
- Obecnie firma Contoso ma jednej kolekcji TFS o nazwie **ContosoDev**, które będą migrowane do konta usługi VSTS, o nazwie **contosodevmigration.visualstudio.com**.
- Projekty, elementy robocze, błędy i iteracji w ciągu ostatniego roku zostaną poddane migracji do usługi VSTS.
- Firma Contoso będzie korzystać z usługi Azure Active Directory, która Contoso konfiguruje się podczas wdrażania [infrastruktury platformy Azure](contoso-migration-infrastructure.md) na początku planowania migracji. 


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
> * **Krok 1: Tworzenie konta usługi Azure storage**: to konto magazynu będzie używane podczas procesu migracji.
> * **Krok 2: Uaktualniania TFS**: Contoso uaktualni ich wdrożenia programu TFS 2018 uaktualnienia 2. 
> * **Krok 3. weryfikowanie kolekcji**: Contoso zostanie przeprowadzona Weryfikacja kolekcji TFS w ramach przygotowania do migracji.
> * **Krok 4: Kompilacja przygotowania pliku**: Contoso utworzy pliki migracji przy użyciu narzędzia migracji w programie TFS. 


## <a name="step-1-create-a-storage-account"></a>Krok 1: Tworzenie konta magazynu

1. W witrynie Azure portal firmy Contoso administratorom tworzenie konta magazynu (**contosodevmigration**).
2. Konta mogą umieścić w ich regionu pomocniczego, których używają do trybu failover — środkowe stany USA. Używają standardowych konto ogólnego przeznaczenia z magazynem lokalnie dodatkowa.

    ![Konto magazynu](./media/contoso-migration-tfs-vsts/storage1.png) 


**Potrzebujesz dodatkowej pomocy?**

- [Wprowadzenie do usługi Azure storage](https://docs.microsoft.com/azure/storage/common/storage-introduction).
- [Tworzenie konta magazynu](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).


## <a name="step-2-upgrade-tfs"></a>Krok 2: Uaktualniania TFS

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
> Niektóre uaktualnienia programu TFS, należy po zakończeniu uaktualniania, uruchom Kreatora konfigurowania funkcji. [Dowiedz się więcej](https://docs.microsoft.com/vsts/work/customize/configure-features-after-upgrade?utm_source=ms&utm_medium=guide&utm_campaign=vstsdataimportguide&view=vsts).

**Potrzebujesz dodatkowej pomocy?**

Dowiedz się więcej o [uaktualniania programu TFS](https://docs.microsoft.com/tfs/server/upgrade/get-started).

## <a name="step-3-validate-the-tfs-collection"></a>Krok 3. weryfikowanie kolekcji TFS

Uruchom narzędzie do migracji serwera TFS w bazie danych kolekcji ContosoDev Aby zweryfikować, czy przed migracją administratorów firmy Contoso.

1. Pobierz i Rozpakuj [narzędzia do migracji serwera TFS](https://www.microsoft.com/download/details.aspx?id=54274). Jest ważne pobrać wersję dla aktualizacji TFS, która jest uruchomiona. Wersji można sprawdzić w konsoli administracyjnej.

    ![TFS](./media/contoso-migration-tfs-vsts/collection1.png)

2. One Uruchom narzędzie do wykonywania sprawdzania poprawności, określając adres URL kolekcji projektów zespołowych:

        **TfsMigrator validate /collection:http://contosotfs:8080/tfs/ContosoDev**


3. W narzędziu jest wyświetlany błąd.

    ![TFS](./media/contoso-migration-tfs-vsts/collection2.png)

5. Się one dziennika pliki znajdują się w **dzienniki** folderu tuż przed lokalizacji narzędzia. Plik dziennika jest generowany dla poszczególnych głównych sprawdzania poprawności. **TfsMigration.log** przechowuje informacje głównego.

    ![TFS](./media/contoso-migration-tfs-vsts/collection3.png)

4. Znajdź one ten wpis, związane z tożsamościami.

    ![TFS](./media/contoso-migration-tfs-vsts/collection4.png)

5. Zakres ich działania **TfsMigration zweryfikować/Help** w wierszu polecenia i zobacz, że polecenie **/tenantDomainName** wydaje się być wymagane do weryfikacji tożsamości.

     ![TFS](./media/contoso-migration-tfs-vsts/collection5.png)

6. Uruchom ponownie polecenie sprawdzania poprawności i Uwzględnij tę wartość, wraz z ich nazwy usługi Azure AD: **TfsMigrator zweryfikować/Collection:http://contosotfs:8080/tfs/ContosoDev /tenantDomainName:contosomigration.onmicrosoft.com**.

    ![TFS](./media/contoso-migration-tfs-vsts/collection7.png)

7. Pojawi się w usłudze Azure AD ekran logowania, a użytkownik podał poświadczenia administratora globalnego.

     ![TFS](./media/contoso-migration-tfs-vsts/collection8.png)

8. Sprawdzanie poprawności przekazuje i został potwierdzony przez narzędzie.

    ![TFS](./media/contoso-migration-tfs-vsts/collection9.png)



## <a name="step-4-create-the-migration-files"></a>Krok 4: Tworzenie plików migracji

Za pomocą Walidowanie ukończone Administratorzy Contoso służy narzędzie do migracji serwera TFS do tworzenia plików migracji.

1. Działają one w kroku przygotowania w narzędziu.

    **TfsMigrator przygotowanie/Collection:http://contosotfs:8080/tfs/ContosoDev /tenantDomainName:contosomigration.onmicrosoft.com /accountRegion:cus**

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

5. Plik import.json zawiera ustawienia importowania. Zawiera informacje, takie jak nazwa żądanego konta i informacje o koncie magazynu. Najczęściej pola są wypełniane automatycznie. Niektóre pola wymagane dane wejściowe użytkownika. Otwórz plik i dodaje nazwę konta usługi VSTS, który ma zostać utworzony: **contosodevmigration**. Ta nazwa będzie swój adres URL usługi VSTS **contosodevmigration.visualstudio.com**.

    ![Przygotowanie](./media/contoso-migration-tfs-vsts/prep5.png)

    > [!NOTE]
    > To konto należy utworzyć przed rozpoczęciem migracji, można zmienić po zakończeniu migracji.

6. Przejrzyj ich pliku mapy tożsamości dziennika, który zawiera konta, które zostaną przeniesione do usługi VSTS podczas importowania. 

    - Aktywne tożsamości można znaleźć tożsamości, które staną się użytkowników w usłudze VSTS po zaimportowaniu.
    - W usłudze VSTS te tożsamości będzie ono licencjonowane i wyświetlane jako użytkownik w ramach konta, po zakończeniu migracji.
    - Te tożsamości są oznaczone jako **Active** w **oczekiwany stan importu** kolumna w pliku.

    ![Przygotowanie](./media/contoso-migration-tfs-vsts/prep6.png)



## <a name="step-5-migrate-to-vsts"></a>Krok 5: Migracja do usługi VSTS

Przygotowanie w miejscu Administratorzy Contoso teraz skoncentrować się na temat migracji. Po uruchomieniu migracji, mogą przełączać z zastosowaniem funkcji TFVC do usługi Git do kontroli wersji.

Przed rozpoczęciem, Administratorzy zaplanować przestój zespołowi deweloperów podjęcie kolekcji w trybie offline dla migracji. Poniżej przedstawiono kroki procesu migracji:

1. **Odłącz kolekcję**: one dane o tożsamości dla kolekcji znajduje się w bazie danych konfiguracji serwera TFS, gdy kolekcja jest dołączona i online. Odłączeniem kolekcji z serwera TFS ma kopię danych tożsamości i pakietów go z kolekcji dla transportu. Bez tych danych nie można wykonać część tożsamości importu. Zaleca się, że kolekcja pozostają odłączone ukończenie importu, ponieważ nie ma możliwości zaimportować zmiany, które wystąpiły podczas importowania.
2. **Generowanie kopii zapasowej**: następnym krokiem procesu migracji jest dla nich generowania kopii zapasowych, który można zaimportować do usługi VSTS. Warstwy danych aplikacji składnika pakietów (DACPAC) to funkcja programu SQL Server, która umożliwia zmian w bazie danych umieszczonych w jednym pliku i wdrożone do innych wystąpień programu SQL Server. Również można przywrócić bezpośrednio do usługi VSTS i dlatego jest używana jako metoda tworzenia pakietów w celu uzyskania danych kolekcji w chmurze. Firma Contoso będzie narzędzie SqlPackage.exe do generowania pliku DACPAC. To narzędzie jest dostępne w programie SQL Server Data Tools.
3. **Przekaż do magazynu**: DACPAC — po utworzeniu, przekazują do usługi Azure Storage. Po przekazaniu, otrzymują sygnatury dostępu współdzielonego (SAS), aby zezwolić na dostęp narzędzia do migracji serwera TFS do magazynu.
4. **Wypełnianie importu**: może następnie wypełnić brakujące pola w pliku importu, włączając ustawienie pliku DACPAC. Na początek z ich określisz chcą wykonywać **próbnym uruchamianiem** importu, aby sprawdzić, czy wszystko działa poprawnie przed pełnej migracji.
5. **Czy uruchomienia próbnego**: uruchomienia próbnego Importy ułatwić, testowanie migracja kolekcji. Uruchomienia próbnego mają ograniczone życia i zostaną usunięte przed uruchomieniem migracji produkcji. Są one usuwane automatycznie po ustawionym okresie czasu. Uwaga dotycząca usunięcia uruchomienia próbnego znajduje się w wiadomości e-mail powodzeniu odebrany po zakończeniu importowania. Zwróć uwagę i odpowiednio zaplanować.
6. **Kończenie migracji w środowisku produkcyjnym**: Migracja uruchomienia próbnego została zakończona, Administratorzy Contoso wykonaj ostateczną migracją, aktualizowanie import.json i ponownie uruchomić importowanie.



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

Administratorzy firmy Contoso Utwórz kopię zapasową (DACPAC) do zaimportowania do usługi VSTS.

- SqlPackage.exe w programie SQL Server Data Tools jest używany do utworzenia pakietu DACPAC. Istnieje wiele wersji instalowany z programu SQL Server Data Tools, znajduje się w folderach, przy użyciu nazwy, takie jak 120, 130 i 140 SqlPackage.exe. Koniecznie Użyj właściwej wersji do przygotowania pakietu DACPAC.
- Importy serwera TFS 2018 muszą używać SqlPackage.exe z folderu 140 lub nowszej.  Aby uzyskać CONTOSOTFS, ten plik znajduje się w folderze: **\Microsoft Visual Studio\2017\Enterprise\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\140 C:\Program Files (x86)**.


Administratorzy firmy Contoso wygenerowanie pakietu DACPAC w następujący sposób:

1. One Otwórz wiersz polecenia i przejdź do lokalizacji SQLPackage.exe. Tego następujące polecenie, aby wygenerować DACPAC one wpisz:

    **SqlPackage.exe /sourceconnectionstring: "źródło danych = SQLSERVERNAME\INSTANCENAME; Initial Catalog = Tfs_ContosoDev; Integrated Security = True" /targetFile:C:\TFSMigrator\Tfs_ContosoDev.dacpac /action:extract /p:ExtractAllTableData = true /p: I gnoreUserLoginMappings = true /p:IgnorePermissions = true /p:Storage = pamięci** 

    ![Backup](./media/contoso-migration-tfs-vsts/backup1.png)

2. Po uruchomieniu polecenia, pojawia się następujący komunikat.

    ![Backup](./media/contoso-migration-tfs-vsts/backup2.png)

3. Weryfikują właściwości DACPACfile

    ![Backup](./media/contoso-migration-tfs-vsts/backup3.png)

### <a name="update-the-file-to-storage"></a>Aktualizowanie pliku do magazynu

Po utworzeniu pakietu DACPAC Contoso przekazuje go do usługi Azure Storage.

1. One [Pobierz i zainstaluj](https://azure.microsoft.com/features/storage-explorer/) Eksploratora usługi Azure Storage.

    ![Upload](./media/contoso-migration-tfs-vsts/backup5.png)

4. Połącz z ich subskrypcją i Znajdź konto magazynu, które są tworzone w związku z migracją (**contosodevmigration**). Tworzą nowy kontener obiektów blob **vstsmigration**.

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
• Dacpac: Ustaw nazwę pliku DACPAC przekazane do konta magazynu. Rozszerzenie ".dacpac".
• ImportType: Ustaw próbnym uruchamianiem teraz.


![Importowanie ustawień](./media/contoso-migration-tfs-vsts/import1.png)


### <a name="do-a-dry-run-migration"></a>Przeprowadzić migrację przebiegu

Administratorzy firmy Contoso rozpoczynać migracji do uruchomienia próbnego, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.

1. Otwórz wiersz polecenia i znajdź miejsce TfsMigration (C:\TFSMigrator).
2. Pierwszym krokiem sprawdzają poprawność pliku importu. Chce mieć pewność, że plik jest prawidłowo sformatowany i czy działa klucza sygnatury dostępu Współdzielonego.

    **TfsMigrator zaimportować /importFile:C:\TFSMigrator\import.json /validateonly**

3. Sprawdzanie poprawności zwraca błąd, który klucz sygnatury dostępu Współdzielonego musi dłuższy czas wygaśnięcia.

    ![Uruchomienia próbnego](./media/contoso-migration-tfs-vsts/test1.png)

3. Używają Eksploratora usługi Azure Storage, aby utworzyć nowy klucz sygnatury dostępu Współdzielonego z upływem siedmiu dni.

    ![Uruchomienia próbnego](./media/contoso-migration-tfs-vsts/test2.png)

3. One należy zaktualizować plik import.json i uruchom ponownie sprawdzenie poprawności. Tym razem zostanie pomyślnie zakończona.

    **TfsMigrator zaimportować /importFile:C:\TFSMigrator\import.json /validateonly**

    ![Uruchomienia próbnego](./media/contoso-migration-tfs-vsts/test3.png)
    
7. Uruchamiają uruchomienia próbnego:

    **/ImportFile:C:\TFSMigrator\import.json importu TfsMigrator**

8. Aby potwierdzić migrację, zgłaszany jest komunikat. Należy pamiętać, czas, dla której będzie przechowywany użycia przemieszczonych danych po uruchomienia próbnego.

    ![Uruchomienia próbnego](./media/contoso-migration-tfs-vsts/test4.png)

9. Usługi Azure AD Zaloguj pojawia się, a powinien korzystanie z logowania administratora firmy Contoso.

    ![Uruchomienia próbnego](./media/contoso-migration-tfs-vsts/test5.png)

10. Komunikat przedstawia informacje dotyczące importowania.

    ![Uruchomienia próbnego](./media/contoso-migration-tfs-vsts/test6.png)

11. Po 15 minut przejdź pod adres URL, a widoczne następujące informacje:

     ![Uruchomienia próbnego](./media/contoso-migration-tfs-vsts/test7.png)

12. Po zakończeniu migracji, prowadzi Dev Contoso zaloguje się do usługi VSTS do sprawdzenia, czy uruchomienia próbnego działały prawidłowo. Po uwierzytelnieniu usługi VSTS musi kilku szczegółów, aby potwierdzić konto.

    ![Uruchomienia próbnego](./media/contoso-migration-tfs-vsts/test8.png)

13. W usłudze VSTS potencjalny klient Dev widoczne, że projekty zostały poddane migracji do usługi VSTS. Istnieje powiadomienie, że konta zostaną usunięte w ciągu 15 dni.

    ![Uruchomienia próbnego](./media/contoso-migration-tfs-vsts/test9.png)

14. Potencjalny klient Dev otwiera jednego z projektów i otwiera **elementów roboczych** > **przypisane do mnie**. Pokazuje to, że danych elementów roboczych zostanie poddany migracji, wraz z tożsamością.

    ![Uruchomienia próbnego](./media/contoso-migration-tfs-vsts/test10.png)

15. Potencjalny klient sprawdza również innych projektów i kodu, aby upewnić się, że kod źródłowy i Historia została zmigrowana.

    ![Uruchomienia próbnego](./media/contoso-migration-tfs-vsts/test11.png)


### <a name="run-the-production-migration"></a>Uruchom migrację w środowisku produkcyjnym

Za pomocą ukończone uruchomienia próbnego Administratorzy Contoso przejść do migracji w środowisku produkcyjnym. Ich usuwanie uruchomienia próbnego, zaktualizuj ustawienia importowania i ponownie uruchom importowanie.

1. W portalu usługi VSTS mogą usunąć konto uruchomienia próbnego.
2. Zaktualizowanie pliku import.json, aby ustawić **ImportType** do **ProductionRun**.

    ![Produkcja](./media/contoso-migration-tfs-vsts/full1.png)

3. Rozpocząć migrację, tak samo, jak dla uruchomienia próbnego: **/importFile:C:\TFSMigrator\import.json importu TfsMigrator**.
4. Komunikat przedstawia na potwierdzenie migracji i wyświetli ostrzeżenie, że danych może być przechowywany w bezpiecznym miejscu taki obszar przygotowawczy przez maksymalnie siedem dni.

    ![Produkcja](./media/contoso-migration-tfs-vsts/full2.png)

5. W usłudze Azure AD Sign In określają logowania administratora firmy Contoso.

    ![Produkcja](./media/contoso-migration-tfs-vsts/full3.png)

6. Komunikat przedstawia informacje dotyczące importowania.

    ![Produkcja](./media/contoso-migration-tfs-vsts/full4.png)

7. Po około 15 minut, przejdź do adresu URL, a widzi następujące informacje:

    ![Produkcja](./media/contoso-migration-tfs-vsts/full5.png)

8. Po migracji zakończy się prowadzi deweloperów firmy Contoso loguje się do usługi VSTS, aby sprawdzić, czy migracja działały prawidłowo. Po zalogowaniu potencjalnego klienta można zobaczyć, że projekty zostały poddane migracji.

    ![Produkcja](./media/contoso-migration-tfs-vsts/full6.png)

8. Potencjalny klient Dev otwiera jednego z projektów i otwiera **elementów roboczych** > **przypisane do mnie**. Pokazuje to, że danych elementów roboczych zostanie poddany migracji, wraz z tożsamością.

    ![Produkcja](./media/contoso-migration-tfs-vsts/full7.png)

9. Potencjalny klient sprawdza, czy innych danych elementu roboczego, aby potwierdzić.

    ![Produkcja](./media/contoso-migration-tfs-vsts/full8.png)

15. Potencjalny klient sprawdza również innych projektów i kodu, aby upewnić się, że kod źródłowy i Historia została zmigrowana.

    ![Produkcja](./media/contoso-migration-tfs-vsts/full9.png)


### <a name="move-source-control-from-tfvc-to-git"></a>Przenoszenie kontroli źródła z TFVC do usługi GIT

Za pomocą po zakończeniu migracji firma Contoso chce przenieść z TFVC do usługi Git do zarządzania kodem źródłowym. Administratorzy firmy Contoso, należy zaimportować kodu źródłowego, obecnie w ramach konta usługi VSTS, jako repozytoriów Git, w ramach tego samego konta.

1. W portalu usługi VSTS otwarciu, jednym z repozytoriów TFVC (**$/ PolicyConnect**) i przejrzyj go.

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

6. Po zapoznaniu się ze źródłem, prowadzi Dev zgodę na zakończeniu migracji do usługi VSTS. Usługa VSTS teraz staje się źródła dla wszystkich rozwoju w obrębie zespołów zajmujących się migracji.

    ![Usługa Git](./media/contoso-migration-tfs-vsts/git6.png)



**Potrzebujesz dodatkowej pomocy?**

[Dowiedz się więcej](https://docs.microsoft.com/vsts/git/import-from-tfvc?view=vsts) o importowaniu z TFVC.

##  <a name="clean-up-after-migration"></a>Wyczyść zasoby po ukończeniu migracji

Za pomocą po zakończeniu migracji Contoso musi wykonać następujące czynności:

- Przegląd [importu po](https://docs.microsoft.com/vsts/articles/migration-post-import?view=vsts) artykuł dotyczący informacji na temat importowania dodatkowych działań.
- Usuń repozytoriów TFVC lub umieść je w trybie tylko do odczytu. Kod, który nie może podstaw używane, ale można odwoływać się do jego historii.

## <a name="next-steps"></a>Kolejne kroki

Contoso będzie musiał przeszkolenie usługi VSTS i usługą Git dla członków zespołu odpowiednie.



