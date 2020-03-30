---
title: Migrowanie danych do konta interfejsu API usługi Azure Cosmos DB SQL przy użyciu usługi Striim
description: Dowiedz się, jak używać striim do migracji danych z bazy danych Oracle do konta interfejsu API usługi Azure Cosmos DB SQL.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 36ba9e2d3385184f32876a6d067b58f7c21a90bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71003278"
---
# <a name="migrate-data-to-azure-cosmos-db-sql-api-account-using-striim"></a>Migrowanie danych do konta interfejsu API usługi Azure Cosmos DB SQL przy użyciu usługi Striim
 
Obraz Striim w portalu Azure marketplace oferuje ciągłe przenoszenie danych w czasie rzeczywistym z magazynów danych i baz danych na platformę Azure. Podczas przenoszenia danych można wykonywać denormalizację w linii, transformację danych, włączać analizy w czasie rzeczywistym i scenariusze raportowania danych. Firma Striim może łatwo rozpocząć pracę, aby stale przenosić dane przedsiębiorstwa do interfejsu API SQL usługi Azure Cosmos DB. Platforma Azure oferuje ofertę portalu Marketplace, która ułatwia wdrażanie usługi Striim i migrację danych do usługi Azure Cosmos DB. 

W tym artykule pokazano, jak używać striim do migracji danych z **bazy danych Oracle** do konta interfejsu API sql usługi Azure **Cosmos DB.**

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz [subskrypcji platformy Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* Baza danych Oracle działająca lokalnie z pewnymi danymi.

## <a name="deploy-the-striim-marketplace-solution"></a>Wdrażanie rozwiązania striim marketplace

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/).

1. Wybierz **pozycję Utwórz zasób** i wyszukaj **striim** w portalu Azure Marketplace. Wybierz pierwszą opcję i **utwórz**.

   ![Znajdź element z rynku Striim](./media/cosmosdb-sql-api-migrate-data-striim/striim-azure-marketplace.png)

1. Następnie wprowadź właściwości konfiguracji instancji Striim. Środowisko Striim jest wdrażany na maszynie wirtualnej. W okienku **Podstawy** wprowadź **nazwę użytkownika maszyny Wirtualnej**, **hasło maszyny Wirtualnej** (to hasło jest używane do SSH do maszyny Wirtualnej). Wybierz **szczegóły subskrypcji,** **grupy zasobów**i **lokalizacji,** w których chcesz wdrożyć striim. Po zakończeniu wybierz przycisk **OK**.

   ![Konfigurowanie podstawowych ustawień striim](./media/cosmosdb-sql-api-migrate-data-striim/striim-configure-basic-settings.png)

1. W okienku **ustawień klastra Striim** wybierz typ wdrożenia Striim i rozmiar maszyny wirtualnej.

   |Ustawienie | Wartość | Opis |
   | ---| ---| ---|
   |Typ wdrożenia Striim |Autonomiczna | Striim można uruchomić w typach wdrażania **autonomicznego** lub **klastra.** Tryb autonomiczny wdroży serwer Striim na jednej maszynie wirtualnej i można wybrać rozmiar maszyn wirtualnych w zależności od woluminu danych. Tryb klastra wdroży serwer Striim na co najmniej dwóch maszynach wirtualnych o wybranym rozmiarze. Środowiska klastrów z więcej niż 2 węzłami oferują automatyczną wysoką dostępność i tryb failover.</br></br> W tym samouczku możesz wybrać opcję Autonomiczna. Użyj domyślnej maszyny wirtualnej o rozmiarze "Standard_F4s".  | 
   | Nazwa klastra Striim|    <Striim_cluster_Name>|  Nazwa klastra Striim.|
   | Hasło klastra Striim|   > <Striim_cluster_password|  Hasło do klastra.|

   Po wypełnieniu formularza wybierz przycisk **OK,** aby kontynuować.

1. W okienku **Ustawień dostępu Striim** skonfiguruj **publiczny adres IP** (wybierz wartości domyślne), Nazwę domeny dla **striim**, **Hasło administratora,** którego chcesz użyć do zalogowania się do interfejsu użytkownika Striim. Konfigurowanie sieci wirtualnej i podsieci (wybierz wartości domyślne). Po wypełnieniu szczegółów wybierz przycisk **OK,** aby kontynuować.

   ![Ustawienia dostępu Striim](./media/cosmosdb-sql-api-migrate-data-striim/striim-access-settings.png)

1. Platforma Azure sprawdzi weryfikację wdrożenia i upewnij się, że wszystko wygląda dobrze; sprawdzanie poprawności trwa kilka minut. Po zakończeniu sprawdzania poprawności wybierz przycisk **OK**.
  
1. Na koniec przejrzyj warunki użytkowania i wybierz pozycję **Utwórz,** aby utworzyć wystąpienie Striim. 

## <a name="configure-the-source-database"></a>Konfigurowanie źródłowej bazy danych 

W tej sekcji można skonfigurować bazę danych Oracle jako źródło przenoszenia danych.  Aby połączyć się z Oracle, potrzebny jest [sterownik Oracle JDBC.](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) Aby odczytać zmiany ze źródłowej bazy danych Oracle, można użyć [interfejsów API LogMiner](https://www.oracle.com/technetwork/database/features/availability/logmineroverview-088844.html) lub [XStream](https://docs.oracle.com/cd/E11882_01/server.112/e16545/xstrm_intro.htm#XSTRM72647). Sterownik Oracle JDBC musi znajdować się w ścieżce klasy Java firmy Striim, aby odczytywać, zapisywać lub utrwalać dane z bazy danych Oracle.

Pobierz sterownik [ojdbc8.jar](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) na swój lokalny komputer. Zainstalujesz go później w klastrze Striim.

## <a name="configure-the-target-database"></a>Konfigurowanie docelowej bazy danych

W tej sekcji skonfigurujesz konto interfejsu API usługi Azure Cosmos DB SQL jako miejsce docelowe przenoszenia danych.

1. Utwórz [konto interfejsu API SQL usługi Azure Cosmos DB](create-cosmosdb-resources-portal.md) przy użyciu portalu Azure.

1. Przejdź do okienka **Eksploratora danych** na koncie usługi Azure Cosmos. Wybierz **nowy kontener,** aby utworzyć nowy kontener. Załóżmy, że przeprowadzasz migrację *produktów* i *zamawiasz* dane z bazy danych Oracle do usługi Azure Cosmos DB. Utwórz nową bazę danych o nazwie **StriimDemo** z kontenerem o nazwie **Zamówienia**. Aprowizuj kontener z **1000 procesorów RU** (w tym przykładzie używa 1000 procesorów RU, ale należy użyć przepływności szacowanej dla obciążenia) i **/ORDER_ID** jako klucz partycji. Wartości te będą się różnić w zależności od danych źródłowych. 

   ![Tworzenie konta interfejsu API SQL](./media/cosmosdb-sql-api-migrate-data-striim/create-sql-api-account.png)

## <a name="configure-oracle-to-azure-cosmos-db-data-flow"></a>Konfigurowanie usługi Oracle do przepływu danych usługi Azure Cosmos DB

1. Wróćmy teraz do Striima. Przed rozpoczęciem interakcji ze striim należy zainstalować sterownik Oracle JDBC pobrany wcześniej.

1. Przejdź do wystąpienia Striim wdrożonego w witrynie Azure Portal. Wybierz przycisk **Połącz** na górnym pasku menu i na karcie **SSH** skopiuj adres URL w polu **Zaloguj się przy użyciu lokalnego konta maszyny Wirtualnej.**

   ![Pobierz adres URL SSH](./media/cosmosdb-sql-api-migrate-data-striim/get-ssh-url.png)

1. Otwórz nowe okno terminala i uruchom polecenie SSH skopiowane z witryny Azure portal. Ten artykuł używa terminala w systemie MacOS, można wykonać podobne instrukcje za pomocą PuTTY lub innego klienta SSH na komputerze z systemem Windows. Po wyświetleniu monitu wpisz **tak,** aby kontynuować i wprowadź **hasło** ustawione dla maszyny wirtualnej w poprzednim kroku.

   ![Łączenie się z maszyną wirtualną Striim](./media/cosmosdb-sql-api-migrate-data-striim/striim-vm-connect.png)

1. Teraz otwórz nową kartę terminala, aby skopiować pobrany wcześniej plik **ojdbc8.jar.** Użyj następującego polecenia SCP, aby skopiować plik jar z komputera lokalnego do folderu tmp wystąpienia Striim uruchomionego na platformie Azure:

   ```bash
   cd <Directory_path_where_the_Jar_file_exists> 
   scp ojdbc8.jar striimdemo@striimdemo.westus.cloudapp.azure.com:/tmp
   ```

   ![Skopiuj plik Jar z komputera lokalizacji do Striim](./media/cosmosdb-sql-api-migrate-data-striim/copy-jar-file.png)

1. Następnie przejdź z powrotem do okna, w którym zrobiłeś SSH do wystąpienia Striim i Zaloguj się jako sudo. Przenieś plik **ojdbc8.jar** z katalogu **/tmp** do katalogu **lib** wystąpienia Striim za pomocą następujących poleceń:

   ```bash
   sudo su
   cd /tmp
   mv ojdbc8.jar /opt/striim/lib
   chmod +x ojdbc8.jar
   ```

   ![Przenoszenie pliku Jar do folderu lib](./media/cosmosdb-sql-api-migrate-data-striim/move-jar-file.png)


1. Z tego samego okna terminala uruchom ponownie serwer Striim, wykonując następujące polecenia:

   ```bash
   Systemctl stop striim-node
   Systemctl stop striim-dbms
   Systemctl start striim-dbms
   Systemctl start striim-node
   ```
 
1. Striim zajmie minutę, aby rozpocząć. Jeśli chcesz zobaczyć stan, uruchom następujące polecenie: 

   ```bash
   tail -f /opt/striim/logs/striim-node.log
   ```

1. Teraz przejdź z powrotem do platformy Azure i skopiuj publiczny adres IP maszyny Wirtualnej Striim. 

   ![Kopiowanie adresu IP maszyny Wirtualnej Striim](./media/cosmosdb-sql-api-migrate-data-striim/copy-public-ip-address.png)

1. Aby przejść do interfejsu użytkownika sieci Web striim, otwórz nową kartę w przeglądarce i skopiuj publiczny adres IP, a następnie: 9080. Zaloguj się przy użyciu nazwy użytkownika **administratora** wraz z hasłem administratora określonym w witrynie Azure portal.

   ![Zaloguj się do Striim](./media/cosmosdb-sql-api-migrate-data-striim/striim-login-ui.png)

1. Teraz dotrzesz do strony głównej Striima. Istnieją trzy różne okienka - **pulpity nawigacyjne,** **aplikacje**i **SourcePreview**. Okienko Pulpity nawigacyjne umożliwia przenoszenie danych w czasie rzeczywistym i ich wizualizowanie. Okienko Aplikacje zawiera potoki danych przesyłania strumieniowego lub przepływy danych. Po prawej stronie znajduje się SourcePreview, gdzie można wyświetlić podgląd danych przed przeniesieniem.

1. Wybierz **okienko Aplikacje,** na razie skoncentrujemy się na tym okienku. Istnieje wiele przykładowych aplikacji, których możesz użyć, aby dowiedzieć się więcej o Striim, jednak w tym artykule utworzysz nasze własne. Wybierz przycisk **Dodaj aplikację** w prawym górnym rogu.

   ![Dodawanie aplikacji Striim](./media/cosmosdb-sql-api-migrate-data-striim/add-striim-app.png)

1. Istnieje kilka różnych sposobów tworzenia aplikacji Striim. Wybierz **pozycję Rozpocznij od szablonu,** aby rozpocząć od istniejącego szablonu.

   ![Uruchamianie aplikacji za pomocą szablonu](./media/cosmosdb-sql-api-migrate-data-striim/start-with-template.png)

1. W polu **Szablony wyszukiwania** wpisz "Cosmos" i wybierz **pozycję Cel: Usługa Azure Cosmos DB,** a następnie wybierz **oracle CDC do usługi Azure Cosmos DB**.

   ![Wybierz Oracle CDC do Cosmos DB](./media/cosmosdb-sql-api-migrate-data-striim/oracle-cdc-cosmosdb.png)

1. Na następnej stronie nazwij aplikację. Można podać nazwę, taką jak **oraToCosmosDB,** a następnie wybrać **zapisz**.

1. Następnie wprowadź konfigurację źródłowej źródłowego wystąpienia Oracle źródłowego. Wprowadź wartość **nazwy źródła**. Nazwa źródła jest tylko konwencją nazewnictwa aplikacji Striim, można użyć czegoś takiego jak **src_onPremOracle**. Wprowadź wartości dla pozostałych parametrów źródłowych **URL**, **Nazwa użytkownika**, **Hasło**, wybierz **LogMiner** jako czytnik do odczytu danych z Oracle. Kliknij przycisk **Dalej**, aby kontynuować.

   ![Konfigurowanie parametrów źródła](./media/cosmosdb-sql-api-migrate-data-striim/configure-source-parameters.png)

1. Striim sprawdzi twoje środowisko i upewni się, że może łączyć się z wystąpieniem Oracle źródła, mają odpowiednie uprawnienia i że CDC został poprawnie skonfigurowany. Po sprawdzeniu poprawności wszystkich wartości wybierz przycisk **Dalej**.

   ![Sprawdzanie poprawności parametrów źródła](./media/cosmosdb-sql-api-migrate-data-striim/validate-source-parameters.png)

1. Wybierz tabele z bazy danych Oracle, które chcesz przeprowadzić migrację. Na przykład wybierzmy tabelę Zamówienia i wybierzmy **przycisk Dalej**. 

   ![Wybieranie tabel źródłowych](./media/cosmosdb-sql-api-migrate-data-striim/select-source-tables.png)

1. Po wybraniu tabeli źródłowej można wykonać bardziej skomplikowane operacje, takie jak mapowanie i filtrowanie. W takim przypadku po prostu utworzysz replikę tabeli źródłowej w usłudze Azure Cosmos DB. Tak, wybierz **przycisk Dalej,** aby skonfigurować cel

1. Teraz skonfigurujmy cel:

   * **Nazwa docelowa** — podaj przyjazną nazwę obiektu docelowego. 
   * **Wejście od** — z listy rozwijanej wybierz strumień wejściowy z tego utworzonego w źródłowej konfiguracji Oracle. 
   * **Kolekcje**— wprowadź docelowe właściwości konfiguracji usługi Azure Cosmos DB. Składnią kolekcji jest **SourceSchema.SourceTable, TargetDatabase.TargetContainer**. W tym przykładzie wartość będzie "SYSTEM. ZAMÓWIENIA, StriimDemo.Orders". 
   * **AccessKey** — klucz podstawowy konta usługi Azure Cosmos.
   * **ServiceEndpoint** — identyfikator URI konta usługi Azure Cosmos, można je znaleźć w sekcji **Klucze** w witrynie Azure portal. 

   Wybierz **pozycję Zapisz** i **Dalej**.

   ![Konfigurowanie parametrów docelowych](./media/cosmosdb-sql-api-migrate-data-striim/configure-target-parameters.png)


1. Następnie dotrzesz do projektanta przepływu, gdzie można przeciągać i wypaść z łączników box, aby utworzyć aplikacje przesyłania strumieniowego. W tym momencie nie zostaną wprowadzone żadne zmiany w przepływie. więc śmiało i wdrożyć aplikację, wybierając przycisk **Wdrażanie aplikacji.**
 
   ![Wdrażanie aplikacji](./media/cosmosdb-sql-api-migrate-data-striim/deploy-app.png)

1. W oknie wdrażania można określić, czy chcesz uruchomić niektóre części aplikacji w określonych częściach topologii wdrażania. Ponieważ działamy w topologii prostego wdrażania za pośrednictwem platformy Azure, użyjemy opcji domyślnej.

   ![Użyj opcji domyślnej](./media/cosmosdb-sql-api-migrate-data-striim/deploy-using-default-option.png)

1. Po wdrożeniu można wyświetlić podgląd strumienia, aby wyświetlić przepływające dane. Wybierz ikonę **fali** i gałkę oczną obok niej. Wybierz przycisk **Wdrożone** na górnym pasku menu i wybierz pozycję **Rozpocznij aplikację**.

   ![Uruchamianie aplikacji](./media/cosmosdb-sql-api-migrate-data-striim/start-app.png)

1. Za pomocą czytnika **CDC(Change Data Capture),** Striim będzie odbierać tylko nowe zmiany w bazie danych. Jeśli dane przepływają przez tabele źródłowe, zobaczysz to. Jednak ponieważ jest to tabela demonstracyjna, źródło nie jest połączone z żadną aplikacją. Jeśli używasz przykładowego generatora danych, możesz wstawić łańcuch zdarzeń do bazy danych Oracle.

1. Zobaczysz dane przepływające przez platformę Striim. Striim pobiera wszystkie metadane skojarzone z tabelą, co jest przydatne do monitorowania danych i upewnienia się, że dane trafiają do odpowiedniego celu.

   ![Konfigurowanie potoku CDC](./media/cosmosdb-sql-api-migrate-data-striim/configure-cdc-pipeline.png)

1. Na koniec zalogujmy się do platformy Azure i przejdźmy do konta usługi Azure Cosmos. Odśwież Eksploratora danych, aby zobaczyć, że dane dotarły.  

   ![Sprawdzanie poprawności zmigrowanych danych na platformie Azure](./media/cosmosdb-sql-api-migrate-data-striim/portal-validate-results.png)

Korzystając z rozwiązania Striim na platformie Azure, można stale migrować dane do usługi Azure Cosmos DB z różnych źródeł, takich jak Oracle, Cassandra, MongoDB i wiele innych do usługi Azure Cosmos DB. Aby dowiedzieć się więcej, odwiedź [stronę internetową Striim](https://www.striim.com/), [pobierz bezpłatną 30-dniową wersję próbną Striim,](https://go2.striim.com/download-free-trial)a w przypadku jakichkolwiek problemów podczas konfigurowania ścieżki migracji ze striim złóż [wniosek o wsparcie.](https://go2.striim.com/request-support-striim)

## <a name="next-steps"></a>Następne kroki

* Jeśli przeprowadzasz migrację danych do interfejsu API SQL usługi Azure Cosmos DB, zobacz, [jak przeprowadzić migrację danych do konta interfejsu API Cassandra przy użyciu usługi Striim](cosmosdb-cassandra-api-migrate-data-striim.md)

* [Monitorowanie i debugowanie danych za pomocą metryk usługi Azure Cosmos DB](use-metrics.md)
