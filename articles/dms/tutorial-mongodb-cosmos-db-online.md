---
title: 'Samouczek: Azure Database Migration Service umożliwia migrację bazy danych MongoDB do interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB online | Dokumentacja firmy Microsoft'
description: Dowiedz się, przeprowadzić migrację z bazy danych MongoDB w środowisku lokalnym do interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB online za pomocą usługi Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 07/04/2019
ms.openlocfilehash: 17f1b36ba5d5b699cce621db3917ef92654047ff
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565577"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-online-using-dms"></a>Samouczek: Migrowanie bazy danych MongoDB do interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB online przy użyciu usługi DMS

Azure Database Migration Service służy do przeprowadzenia migracji do usługi online (minimalnych przestojach) baz danych ze środowiska lokalnego lub w chmurze wystąpienia bazy danych MongoDB do interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
>
> * Utwórz wystąpienie usługi Azure Database Migration Service.
> * Utwórz projekt migracji za pomocą usługi Azure Database Migration Service.
> * Uruchamianie migracji.
> * Monitorowanie migracji.
> * Kończenie migracji, gdy wszystko będzie gotowe.

W tym samouczku można dokonać migracji zestawu danych w bazie danych MongoDB hostowanej przy minimalnych przestojach w maszynie wirtualnej platformy Azure do interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB przy użyciu usługi Azure Database Migration Service. Jeśli nie masz jeszcze skonfigurowanego źródła bazy danych MongoDB, zobacz artykuł [Install and configure MongoDB on a Windows VM in Azure (Instalowanie i konfigurowanie bazy danych MongoDB na maszynie wirtualnej z systemem Windows na platformie Azure)](https://docs.microsoft.com/azure/virtual-machines/windows/install-mongodb).

> [!NOTE]
> Korzystanie z usługi Azure Database Migration Service w celu wykonania migracji do usługi online wymaga tworzenia wystąpienia na podstawie warstwy cenowej Premium.

> [!IMPORTANT]
> Środowisko optymalne migracji firma Microsoft zaleca tworzenie wystąpienia usługi Azure Database Migration Service w tym samym regionie platformy Azure jako docelowej bazy danych. Przenoszenie danych między regionami lub lokalizacjami geograficznymi może spowalniać proces migracji.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

W tym artykule opisano migrację online z bazy danych MongoDB do interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB. Aby uzyskać informacje o migracji offline, zobacz [Migrowanie bazy danych MongoDB do interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB w trybie offline za pomocą usługi DMS](tutorial-mongodb-cosmos-db.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* [Wykonaj przed migracją](../cosmos-db/mongodb-pre-migration.md) czynności, takie jak szacowanie przepływności, Wybieranie klucza partycji i zasad indeksowania.
* [Utworzenie konta interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
* Tworzenie usługi Azure Virtual Network (VNet) dla usługi Azure Database Migration Service przy użyciu modelu wdrażania usługi Azure Resource Manager, co zapewnia łączność lokacja lokacja z serwerów źródłowych w środowisku lokalnym przy użyciu [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)lub [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

    > [!NOTE]
    > Podczas konfigurowania sieci wirtualnej, jeśli korzystasz z usługi ExpressRoute za pomocą komunikacji równorzędnej sieci do firmy Microsoft, Dodaj następujące usługi [punktów końcowych](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) do podsieci, w której będą udostępniane usługi:
    >
    > * Docelowy punkt końcowy z bazy danych (na przykład SQL punktu końcowego, punktu końcowego usługi Cosmos DB i tak dalej)
    > * Punkt końcowy magazynu
    > * Punkt końcowy usługi Service bus
    >
    > Ta konfiguracja jest konieczne, ponieważ usługa Azure Database Migration Service nie ma łączności z Internetem.

* Upewnij się, że reguły sieciowej grupy zabezpieczeń (NSG) sieci wirtualnej nie blokują następujących portów komunikacyjnych: 53, 443, 445, 9354 i 10000 20000. Aby uzyskać więcej szczegółowych informacji dotyczących filtrowania ruchu sieciowej grupy zabezpieczeń w sieci wirtualnej platformy Azure, zobacz artykuł [filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Otwórz zaporę Windows, aby umożliwić usłudze Azure Database Migration Service dostęp do serwera bazy danych MongoDB źródła, czyli domyślnie TCP port 27017.
* Podczas korzystania z urządzenia zapory przed źródłowych baz danych, użytkownik może być konieczne dodanie reguły zapory zezwalające usługi Azure Database Migration Service dostęp źródłowych baz danych do migracji do.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Rejestrowanie dostawcy zasobów Microsoft.DataMigration

1. Zaloguj się w witrynie Azure Portal, wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

   ![Wyświetlanie subskrypcji w portalu](media/tutorial-mongodb-to-cosmosdb-online/portal-select-subscription1.png)

2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie usługi Azure Database Migration Service, a następnie wybierz **dostawców zasobów**.

    ![Wyświetlanie dostawców zasobów](media/tutorial-mongodb-to-cosmosdb-online/portal-select-resource-provider.png)

3. Wyszukaj „migration”, a następnie po prawej stronie pozycji **Microsoft.DataMigration** wybierz pozycję **Zarejestruj**.

    ![Rejestrowanie dostawcy zasobów](media/tutorial-mongodb-to-cosmosdb-online/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Tworzenie wystąpienia

1. W witrynie Azure Portal wybierz pozycję + **Utwórz zasób**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz usługę **Azure Database Migration Service** na liście rozwijanej.

    ![Azure Marketplace](media/tutorial-mongodb-to-cosmosdb-online/portal-marketplace.png)

2. Na ekranie **Azure Database Migration Service** wybierz polecenie **Utwórz**.

    ![Tworzenie wystąpienia usługi Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb-online/dms-create1.png)
  
3. Na ekranie **Tworzenie usługi migracji** określ nazwę usługi, subskrypcję oraz nową lub istniejącą grupę zasobów.

4. Wybierz lokalizację, w którym można utworzyć wystąpienia usługi Azure Database Migration Service.

5. Wybieranie istniejącej sieci wirtualnej lub utworzyć nową.

   Sieć wirtualna zapewnia Azure Database Migration Service dostęp do wystąpienia bazy danych MongoDB źródłowego i docelowego konta usługi Azure Cosmos DB.

   Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej w witrynie Azure portal, zobacz artykuł [Utwórz sieć wirtualną przy użyciu witryny Azure portal](https://aka.ms/DMSVnet).

6. Wybierz jednostkę SKU z warstwy cenowej Premium.

    > [!NOTE]
    > Migracje online są obsługiwane tylko w przypadku korzystania z warstwy Premium. Aby uzyskać więcej informacji na temat kosztów i warstw cenowych, zobacz [stronę z cennikiem](https://aka.ms/dms-pricing).

    ![Konfigurowanie ustawień wystąpienia usługi Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb-online/dms-settings3.png)

7. Wybierz pozycję **Utwórz**, aby utworzyć usługę.

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji

Po utworzeniu usługi znajdź ją w witrynie Azure Portal, otwórz ją, a następnie utwórz nowy projekt migracji.

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz pozycję **Azure Database Migration Services**.

    ![Znajdź wszystkie wystąpienia usługi Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb-online/dms-search.png)

2. Na **usługi Azure Database Migration Service** ekranu, wyszukaj nazwę usługi Azure Database Migration Service wystąpienia utworzoną, a następnie wybierz wystąpienie.

    Alternatywnie można wykryć, wystąpienie usługi migracji bazy danych platformy Azure z poziomu okienka wyszukiwania w witrynie Azure portal.

    ![Korzystanie z okienka wyszukiwania w witrynie Azure Portal](media/tutorial-mongodb-to-cosmosdb-online/dms-search-portal.png)

3. Wybierz pozycję + **Nowy projekt migracji**.

4. Na ekranie **Nowy projekt migracji** wpisz nazwę projektu, w polu tekstowym **Typ serwera źródłowego** wybierz pozycję **MongoDB**, w polu tekstowym **Typ serwera docelowego** wybierz pozycję **CosmosDB (interfejs API MongoDB)** , a następnie w polu **Wybierz typ działania** wybierz pozycję **Migracja danych w trybie online [wersja zapoznawcza]** .

    ![Tworzenie projektu usługi Database Migration Service](media/tutorial-mongodb-to-cosmosdb-online/dms-create-project1.png)

5. Wybierz przycisk **Zapisz**, a następnie polecenie **Utwórz i uruchom działanie**, aby utworzyć projekt i uruchomić działanie migracji.

## <a name="specify-source-details"></a>Określanie szczegółów źródła

1. Na ekranie **Szczegóły źródła** określ szczegóły połączenia dla źródłowego serwera MongoDB.

    Istnieją trzy tryby nawiązywania połączenia ze źródłem:
   * **Tryb standardowy**, który akceptuje w pełni kwalifikowaną nazwę domeny lub adres IP, numer portu i poświadczenia połączenia.
   * **Tryb parametrów połączenia**, który akceptuje parametry połączenia bazy danych MongoDB zgodnie z opisem znajdującym się w artykule [Connection String URI Format](https://docs.mongodb.com/manual/reference/connection-string/) (Format identyfikatora URI parametrów połączenia).
   * **Tryb danych z usługi Azure Storage**, który akceptuje adres URL sygnatury dostępu współdzielonego kontenera obiektów blob. Zaznacz pole wyboru **Obiekt blob zawiera zrzuty BSON**, jeśli kontener obiektów blob zawiera zrzuty BSON utworzone przez [narzędzie bsondump](https://docs.mongodb.com/manual/reference/program/bsondump/) bazy danych MongoDB, lub usuń zaznaczenie tego pola wyboru, jeśli kontener zawiera pliki JSON.

     Jeśli wybierzesz tę opcję, upewnij się, że parametry połączenia konta magazynu mają następujący format:

     ```
     https://blobnameurl/container?SASKEY
     ```

     Ponadto, zależnie od typu informacji zrzutu w usłudze Azure Storage, pamiętaj o następujących kwestiach.

     * W przypadku zrzutów BSON dane w kontenerze obiektów blob muszą mieć format bsondump, tak aby pliki danych były umieszczone w folderach o nazwach zgodnych z odpowiednimi bazami danych w formacie kolekcja.bson. Pliki metadanych (jeśli istnieją) powinny mieć nazwy w formacie *kolekcja*.metadata.json.

     * W przypadku zrzutów JSON pliki w kontenerze obiektów blob muszą być umieszczone w folderach o nazwach zgodnych z odpowiednimi bazami danych. W każdym folderze bazy danych pliki danych muszą być umieszczone w podfolderze o nazwie „data” i mieć nazwy w formacie *kolekcja*.json. Pliki metadanych (jeśli istnieją) muszą być umieszczone w podfolderze o nazwie „metadata” i mieć nazwy w takim samym formacie *kolekcja*.json. Pliki metadanych muszą mieć taki sam format jak pliki tworzone przez narzędzie bsondump bazy danych MongoDB.

    > [!IMPORTANT]
    > Zaleca się używania certyfikatu z podpisem własnym na serwerze mongo. Jednakże, jeśli jest on używany, nawiąż połączenie z serwerem za pomocą **tryb parametrów połączenia** i upewnić się, że parametry połączenia ""
    >
    >```
    >&sslVerifyCertificate=false
    >```

    Jeśli rozpoznawanie nazw DNS nie jest możliwe, możesz użyć adresu IP.

   ![Określanie szczegółów źródła](media/tutorial-mongodb-to-cosmosdb-online/dms-specify-source1.png)

2. Wybierz pozycję **Zapisz**.

   > [!NOTE]
   > Adres serwera źródłowego powinien być adresem podstawowym, jeśli źródło jest zestawem replik, lub adresem routera, jeśli źródło jest podzielonym na fragmenty klastrem bazy danych MongoDB. W przypadku podzielonego na fragmenty klastra bazy danych MongoDB usługa Azure Database Migration Service musi mieć możliwość nawiązania połączenia z poszczególnymi fragmentami w klastrze, co może wymagać otwarcia zapory dla większej liczby maszyn.

## <a name="specify-target-details"></a>Określanie szczegółów elementu docelowego

1. Na ekranie **Szczegóły lokalizacji docelowej migracji** określ szczegóły połączenia dla docelowego konta usługi Azure Cosmos DB, czyli dla aprowizowanego wcześniej konta interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB, na które są migrowane dane MongoDB.

    ![Określanie szczegółów elementu docelowego](media/tutorial-mongodb-to-cosmosdb-online/dms-specify-target1.png)

2. Wybierz pozycję **Zapisz**.

## <a name="map-to-target-databases"></a>Mapowanie do docelowych baz danych

1. Na ekranie **Mapowanie do docelowych baz danych** określ mapowanie źródłowej i docelowej bazy danych na potrzeby migracji.

   Jeśli docelowa baza danych zawiera taką samą nazwę bazy danych, co źródłowa baza danych, Azure Database Migration Service domyślnie wybiera docelowej bazy danych.

   Jeśli ciąg **Utwórz** pojawia się obok nazwy bazy danych, oznacza to, czy Azure Database Migration Service nie znalazł docelowej bazy danych i usługi spowoduje utworzenie bazy danych dla Ciebie.

   Na tym etapie migracji, jeśli chcesz, aby udostępniać przepływności w bazie danych, określ przepustowość RU. W usłudze Cosmos DB można aprowizować przepływność na poziomie bazy danych lub na poziomie poszczególnych kolekcji. Przepływność jest mierzona w [jednostkach żądań](https://docs.microsoft.com/azure/cosmos-db/request-units) (RU). Dowiedz się więcej o [cenniku usługi Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

   ![Mapowanie do docelowych baz danych](media/tutorial-mongodb-to-cosmosdb-online/dms-map-target-databases1.png)

2. Wybierz pozycję **Zapisz**.

3. Na ekranie **Ustawienia kolekcji** rozwiń listę kolekcji, a następnie przejrzyj listę kolekcji, które będą migrowane.

   Azure Database Migration Service automatycznie wybiera wszystkie kolekcje, które istnieją w wystąpieniu bazy danych MongoDB źródłowym, które nie istnieją w miejscu docelowym kontem usługi Azure Cosmos DB. Jeśli chcesz ponownie przeprowadzić migrację kolekcji, które już zawierają dane, musisz jawnie wybrać te kolekcje na bieżącym ekranie.

   Możesz określić liczbę jednostek RU używanych przez kolekcje. W większości przypadków wartość z zakresu od 500 (co najmniej 1000 dla kolekcji podzielonych na fragmenty) do 4000 powinna być wystarczająca. Azure Database Migration Service sugeruje inteligentnych wartości domyślnych, na podstawie rozmiaru kolekcji.

    > [!NOTE]
    > Wykonać migrację bazy danych i kolekcji równolegle za pomocą wielu wystąpień usługi Azure Database Migration Service, jeśli to konieczne, aby przyspieszyć działanie.

   Możesz również wskazać klucz fragmentu, aby skorzystać z [partycjonowania w usłudze Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview), które zapewnia optymalną skalowalność. Zapoznaj się z [najlepszymi rozwiązaniami dotyczącymi wybierania klucza fragmentu/klucza partycji](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey). Jeśli nie masz klucza partycji, zawsze możesz użyć ciągu **_id** jako klucza fragmentu w celu uzyskania lepszej przepływności.

   ![Wybieranie tabel kolekcji](media/tutorial-mongodb-to-cosmosdb-online/dms-collection-setting1.png)

4. Wybierz pozycję **Zapisz**.

5. Na ekranie **Podsumowanie migracji** w polu tekstowym **Nazwa działania** określ nazwę działania migracji.

    ![Podsumowanie migracji](media/tutorial-mongodb-to-cosmosdb-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>Uruchamianie migracji

* Wybierz polecenie **Uruchom migrację**.

   Zostanie wyświetlone okno działania migracji z wyświetlonym **stanem** działania.

   ![Stan działania](media/tutorial-mongodb-to-cosmosdb-online/dms-activity-status1.png)

## <a name="monitor-the-migration"></a>Monitorowanie migracji

* Na ekranie działania migracji wybieraj polecenie **Odśwież**, aby zaktualizować ekran, aż do momentu, gdy **Stan** migracji zmieni się na **Ponowne odtwarzanie**.

   > [!NOTE]
   > Po wybraniu działania można uzyskać szczegółowe informacje o metrykach migracji na poziomie bazy danych i kolekcji.

   ![Stan działania Ponowne odtwarzanie](media/tutorial-mongodb-to-cosmosdb-online/dms-activity-replaying.png)

## <a name="verify-data-in-cosmos-db"></a>Weryfikowanie danych w usłudze Cosmos DB

1. Wprowadź zmiany do źródłowej bazy danych MongoDB.
2. Nawiąż połączenie z usługą COSMOS DB, aby sprawdzić, czy dane zostały zreplikowane ze źródłowego serwera bazy danych MongoDB.

    ![Stan działania Ponowne odtwarzanie](media/tutorial-mongodb-to-cosmosdb-online/dms-verify-data.png)

## <a name="complete-the-migration"></a>Kończenie migracji

* Po udostępnieniu wszystkich dokumentów ze źródła w docelowej bazie danych usługi COSMOS DB wybierz polecenie **Zakończ** z menu kontekstowego działania migracji, aby ukończyć migrację.

    Ta akcja spowoduje zakończenie ponownego odtwarzania wszystkich oczekujących zmian i ukończenie migracji.

    ![Stan działania Ponowne odtwarzanie](media/tutorial-mongodb-to-cosmosdb-online/dms-finish-migration.png)

## <a name="post-migration-optimization"></a>Optymalizacja po migracji

Po przeprowadzeniu migracji danych przechowywanych w bazie danych MongoDB do interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB, można połączyć z usługą Azure Cosmos DB i zarządzania danymi. Możesz również wykonanie innych czynności optymalizacji po migracji, takie jak Optymalizacja zasady indeksowania, zaktualizuj domyślny poziom spójności lub konfigurowanie dystrybucji globalnej dla konta usługi Azure Cosmos DB. Aby uzyskać więcej informacji, zobacz [optymalizacji po przeprowadzeniu migracji](../cosmos-db/mongodb-post-migration.md) artykułu.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Informacje o usłudze Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>Kolejne kroki

* Wskazówki dotyczące innych scenariuszy migracji można znaleźć w dokumencie [Przewodnik po migracji baz danych](https://datamigration.microsoft.com/) opracowanym przez firmę Microsoft.
