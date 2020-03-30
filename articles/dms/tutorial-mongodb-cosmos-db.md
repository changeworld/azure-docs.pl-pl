---
title: 'Samouczek: Migrowanie usługi MongoDB w trybie offline do interfejsu API bazy danych Usługi Azure Cosmos dla usługi MongoDB'
titleSuffix: Azure Database Migration Service
description: Dowiedz się, jak przeprowadzać migrację z usługi MongoDB lokalnie do interfejsu API usługi Azure Cosmos DB dla usługi MongoDB w trybie offline przy użyciu usługi migracji bazy danych azure.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 08fa94dbe71299a6653df0b40aa5083375526172
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255590"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-offline-using-dms"></a>Samouczek: Migrowanie usługi MongoDB do interfejsu API usługi Azure Cosmos DB dla mongodb w trybie offline przy użyciu usługi DMS

Usługi migracji bazy danych azure można użyć do wykonywania migracji w trybie offline (jednorazowo) baz danych z lokalnego lub chmury wystąpienia MongoDB do interfejsu API usługi Azure Cosmos DB dla mongodb.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
>
> * Utwórz wystąpienie usługi Azure Database Migration Service.
> * Utwórz projekt migracji przy użyciu usługi migracji bazy danych platformy Azure.
> * Uruchamianie migracji.
> * Monitorowanie migracji.

W tym samouczku można migrować zestaw danych w mongodb hostowane w maszynie wirtualnej platformy Azure do interfejsu API usługi Azure Cosmos DB dla mongodb przy użyciu usługi migracji bazy danych azure. Jeśli nie masz jeszcze skonfigurowanego źródła bazy danych MongoDB, zobacz artykuł [Install and configure MongoDB on a Windows VM in Azure (Instalowanie i konfigurowanie bazy danych MongoDB na maszynie wirtualnej z systemem Windows na platformie Azure)](https://docs.microsoft.com/azure/virtual-machines/windows/install-mongodb).

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Wykonaj kroki [przed migracją,](../cosmos-db/mongodb-pre-migration.md) takie jak szacowanie przepływności, wybieranie klucza partycji i zasady indeksowania.
* [Utworzenie konta interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
* Utwórz usługę migracji bazy danych usługi Azure Database dla usługi Azure Database przy użyciu modelu wdrażania usługi Azure Resource Manager, który zapewnia łączność między lokacjami z lokalnymi serwerami źródłowymi przy użyciu usługi [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej, zobacz [dokumentację sieci wirtualnej,](https://docs.microsoft.com/azure/virtual-network/)a zwłaszcza artykuły przewodnika Szybki start ze szczegółami krok po kroku.

    > [!NOTE]
    > Podczas konfigurowania sieci wirtualnej, jeśli używasz usługi ExpressRoute z komunikacją równorzędną sieciową do firmy Microsoft, dodaj następujące [punkty końcowe](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) usługi do podsieci, w której usługa będzie aprowizowana:
    >
    > * Docelowy punkt końcowy bazy danych (na przykład punkt końcowy SQL, punkt końcowy usługi Cosmos DB itd.)
    > * Punkt końcowy magazynu
    > * Punkt końcowy magistrali usług
    >
    > Ta konfiguracja jest konieczna, ponieważ usługa migracji bazy danych platformy Azure nie ma łączności z Internetem.

* Upewnij się, że reguły sieciowej sieciowej sieci zabezpieczeń sieci nie blokują następujących portów komunikacyjnych: 53, 443, 445, 9354 i 10000-20000. Aby uzyskać więcej informacji na temat filtrowania ruchu sieciowej sieciowej sieciowej w sieci wirtualnej, zobacz artykuł [Filtrowanie ruchu sieciowego z sieciowymi grupami zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Otwórz zaporę systemu Windows, aby umożliwić usłudze migracji bazy danych platformy Azure dostęp do źródłowego serwera MongoDB, który domyślnie jest portem TCP 27017.
* Podczas korzystania z urządzenia zapory przed źródłową bazą danych może być konieczne dodanie reguł zapory, aby umożliwić usłudze migracji usługi Azure Database dostęp do źródłowych baz danych w celu migracji.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Rejestrowanie dostawcy zasobów Microsoft.DataMigration

1. Zaloguj się w witrynie Azure Portal, wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

   ![Wyświetlanie subskrypcji w portalu](media/tutorial-mongodb-to-cosmosdb/portal-select-subscription1.png)

2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie usługi Azure Database Migration Service, a następnie wybierz pozycję **Dostawcy zasobów**.

    ![Wyświetlanie dostawców zasobów](media/tutorial-mongodb-to-cosmosdb/portal-select-resource-provider.png)

3. Wyszukaj migrację, a następnie po prawej stronie **microsoft.DataMigration**, wybierz pozycję **Zarejestruj**.

    ![Rejestrowanie dostawcy zasobów](media/tutorial-mongodb-to-cosmosdb/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Tworzenie wystąpienia

1. W witrynie Azure Portal wybierz pozycję + **Utwórz zasób**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz usługę **Azure Database Migration Service** na liście rozwijanej.

    ![Azure Marketplace](media/tutorial-mongodb-to-cosmosdb/portal-marketplace.png)

2. Na ekranie **Azure Database Migration Service** wybierz polecenie **Utwórz**.

    ![Tworzenie wystąpienia usługi Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-create1.png)
  
3. Na ekranie **Tworzenie usługi migracji** określ nazwę usługi, subskrypcję oraz nową lub istniejącą grupę zasobów.

4. Wybierz lokalizację, w której chcesz utworzyć wystąpienie usługi migracji bazy danych platformy Azure. 

5. Wybierz istniejącą sieć wirtualną lub utwórz nową.

    Sieć wirtualna zapewnia usługę migracji bazy danych platformy Azure z dostępem do źródłowego wystąpienia mongodb i docelowego konta usługi Azure Cosmos DB.

    Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej w witrynie Azure portal, zobacz artykuł [Tworzenie sieci wirtualnej przy użyciu portalu Azure](https://aka.ms/DMSVnet).

6. Wybierz warstwę cenową.

    Aby uzyskać więcej informacji na temat kosztów i warstw cenowych, zobacz [stronę z cennikiem](https://aka.ms/dms-pricing).

    ![Konfigurowanie ustawień wystąpienia usługi Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7. Wybierz pozycję **Utwórz**, aby utworzyć usługę.

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji

Po utworzeniu usługi znajdź ją w witrynie Azure Portal, otwórz ją, a następnie utwórz nowy projekt migracji.

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz pozycję **Azure Database Migration Services**.

      ![Znajdź wszystkie wystąpienia usługi migracji bazy danych platformy Azure](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. Na ekranie **Usługi migracji bazy danych Azure** wyszukaj nazwę utworzonego wystąpienia usługi migracji bazy danych Azure, a następnie wybierz wystąpienie.

3. Wybierz pozycję + **Nowy projekt migracji**.

4. Na ekranie **Nowy projekt migracji** wpisz nazwę projektu, w polu tekstowym **Typ serwera źródłowego** wybierz pozycję **MongoDB**, w polu tekstowym **Typ serwera docelowego** wybierz pozycję **CosmosDB (interfejs API MongoDB)**, a następnie w polu **Wybierz typ działania** wybierz pozycję **Migracja danych w trybie offline**. 

    ![Tworzenie projektu usługi Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5. Wybierz polecenie **Utwórz i uruchom działanie**, aby utworzyć projekt i uruchomić działanie migracji.

## <a name="specify-source-details"></a>Określanie szczegółów źródła

1. Na ekranie **Szczegóły źródła** określ szczegóły połączenia dla źródłowego serwera MongoDB.

   > [!IMPORTANT]
   > Usługa migracji bazy danych platformy Azure nie obsługuje usługi Azure Cosmos DB jako źródła.

    Istnieją trzy tryby nawiązywania połączenia ze źródłem:
   * **Tryb standardowy**, który akceptuje w pełni kwalifikowaną nazwę domeny lub adres IP, numer portu i poświadczenia połączenia.
   * **Tryb parametrów połączenia**, który akceptuje parametry połączenia bazy danych MongoDB zgodnie z opisem znajdującym się w artykule [Connection String URI Format](https://docs.mongodb.com/manual/reference/connection-string/) (Format identyfikatora URI parametrów połączenia).
   * **Tryb danych z usługi Azure Storage**, który akceptuje adres URL sygnatury dostępu współdzielonego kontenera obiektów blob. Zaznacz pole wyboru **Obiekt blob zawiera zrzuty BSON**, jeśli kontener obiektów blob zawiera zrzuty BSON utworzone przez [narzędzie bsondump](https://docs.mongodb.com/manual/reference/program/bsondump/) bazy danych MongoDB, lub usuń zaznaczenie tego pola wyboru, jeśli kontener zawiera pliki JSON.

     Jeśli wybierzesz tę opcję, upewnij się, że parametry połączenia konta magazynu mają następujący format:

     ```
     https://blobnameurl/container?SASKEY
     ```

     Ten ciąg połączenia kontenera obiektu blob sas można znaleźć w Eksploratorze usługi Azure Storage. Tworzenie sygnatury dostępu Współdzielonego dla danego kontenera zapewni adres URL w powyższym formacie.
     
     Ponadto na podstawie informacji o zrzutu typu w usłudze Azure Storage należy pamiętać o następujących szczegółach.

     * W przypadku zrzutów BSON dane w kontenerze obiektów blob muszą mieć format bsondump, tak aby pliki danych były umieszczone w folderach o nazwach zgodnych z odpowiednimi bazami danych w formacie kolekcja.bson. Pliki metadanych (jeśli istnieją) powinny mieć nazwy w formacie *kolekcja*.metadata.json.

     * W przypadku zrzutów JSON pliki w kontenerze obiektów blob muszą być umieszczone w folderach o nazwach zgodnych z odpowiednimi bazami danych. W każdym folderze bazy danych pliki danych muszą być umieszczone w podfolderze o nazwie „data” i mieć nazwy w formacie *kolekcja*.json. Pliki metadanych (jeśli istnieją) muszą być umieszczone w podfolderze o nazwie „metadata” i mieć nazwy w takim samym formacie *kolekcja*.json. Pliki metadanych muszą mieć taki sam format jak pliki tworzone przez narzędzie bsondump bazy danych MongoDB.

    > [!IMPORTANT]
    > Nie zaleca się używania certyfikatu z podpisem własnym na serwerze mongo. Jeśli jednak jest używany, połącz się z serwerem za pomocą **trybu połączenia** i upewnij się, że parametry połączenia mają ""
    >
    >```
    >&sslVerifyCertificate=false
    >```

   Możesz również użyć adresu IP, jeśli rozpoznawanie nazw DNS nie jest możliwe.

   ![Określanie szczegółów źródła](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. Wybierz **pozycję Zapisz**.

## <a name="specify-target-details"></a>Określanie szczegółów elementu docelowego

1. Na ekranie **Szczegóły lokalizacji docelowej migracji** określ szczegóły połączenia dla docelowego konta usługi Azure Cosmos DB, czyli dla aprowizowanego wcześniej konta interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB, na które są migrowane dane MongoDB.

    ![Określanie szczegółów elementu docelowego](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. Wybierz **pozycję Zapisz**.

## <a name="map-to-target-databases"></a>Mapowanie do docelowych baz danych

1. Na ekranie **Mapowanie do docelowych baz danych** określ mapowanie źródłowej i docelowej bazy danych na potrzeby migracji.

    Jeśli docelowa baza danych zawiera taką samą nazwę bazy danych jak źródłowa baza danych, usługa migracji bazy danych Azure Database domyślnie wybiera docelową bazę danych.

    Jeśli ciąg **Utwórz** pojawia się obok nazwy bazy danych, oznacza to, że usługa migracji bazy danych Azure Database nie znalazła docelowej bazy danych, a usługa utworzy bazę danych dla Ciebie.

    Na tym etapie migracji możesz [zaaprowizować przepływność](https://docs.microsoft.com/azure/cosmos-db/set-throughput). W usłudze Cosmos DB można aprowizować przepływność na poziomie bazy danych lub na poziomie poszczególnych kolekcji. Przepływność jest mierzona w [jednostkach żądań](https://docs.microsoft.com/azure/cosmos-db/request-units) (RU). Dowiedz się więcej o [cenniku usługi Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

    ![Mapowanie do docelowych baz danych](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. Wybierz **pozycję Zapisz**.
3. Na ekranie **Ustawienia kolekcji** rozwiń listę kolekcji, a następnie przejrzyj listę kolekcji, które będą migrowane.

    Usługa migracji bazy danych Azure automatycznie wybiera wszystkie kolekcje, które istnieją w źródłowym wystąpieniu mongodb, które nie istnieją na docelowym koncie usługi Azure Cosmos DB. Jeśli chcesz ponownie przeprowadzić migrację kolekcji, które już zawierają dane, musisz jawnie wybrać te kolekcje w tym bloku.

    Możesz określić liczbę jednostek RU używanych przez kolekcje. Usługa migracji bazy danych platformy Azure sugeruje inteligentne ustawienia domyślne na podstawie rozmiaru kolekcji.

    > [!NOTE]
    > W razie potrzeby należy przeprowadzić migrację i zbieranie danych równolegle przy użyciu wielu wystąpień usługi migracji bazy danych Azure Database, aby przyspieszyć uruchamianie.

    Możesz również wskazać klucz fragmentu, aby skorzystać z [partycjonowania w usłudze Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview), które zapewnia optymalną skalowalność. Zapoznaj się z [najlepszymi rozwiązaniami dotyczącymi wybierania klucza fragmentu/klucza partycji](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey).

    ![Wybieranie tabel kolekcji](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. Wybierz **pozycję Zapisz**.

5. Na ekranie **Podsumowanie migracji** w polu tekstowym **Nazwa działania** określ nazwę działania migracji.

    ![Podsumowanie migracji](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>Uruchamianie migracji

* Wybierz polecenie **Uruchom migrację**.

    Zostanie wyświetlone okno działania migracji, a **stan** działania będzie określony jako **Nierozpoczęte**.

    ![Stan działania](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>Monitorowanie migracji

* Na ekranie działania migracji wybieraj polecenie **Odśwież**, aby zaktualizować ekran, aż do momentu, gdy **Stan** migracji zmieni się na **Ukończono**.

   > [!NOTE]
   > Po wybraniu działania można uzyskać szczegółowe informacje o metrykach migracji na poziomie bazy danych i kolekcji.

    ![Stan działania — ukończono](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-cosmos-db"></a>Weryfikowanie danych w usłudze Cosmos DB

* Po zakończeniu migracji możesz sprawdzić konto usługi Azure Cosmos DB, aby upewnić się, że wszystkie kolekcje zostały zmigrowane pomyślnie.

    ![Stan działania — ukończono](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="post-migration-optimization"></a>Optymalizacja po migracji

Po migracji danych przechowywanych w bazie danych MongoDB do interfejsu API usługi Azure Cosmos DB dla usługi MongoDB można połączyć się z usługą Azure Cosmos DB i zarządzać danymi. Można również wykonać inne kroki optymalizacji po migracji, takie jak optymalizacja zasad indeksowania, zaktualizować domyślny poziom spójności lub skonfigurować dystrybucję globalną dla konta usługi Azure Cosmos DB. Aby uzyskać więcej informacji, zobacz artykuł [optymalizacji po migracji.](../cosmos-db/mongodb-post-migration.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Informacje o usłudze Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>Następne kroki

* Wskazówki dotyczące innych scenariuszy migracji można znaleźć w dokumencie [Przewodnik po migracji baz danych](https://datamigration.microsoft.com/) opracowanym przez firmę Microsoft.
