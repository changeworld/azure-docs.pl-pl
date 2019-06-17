---
title: Migrowanie pakietów usług SQL Server Integration Services do wystąpienia zarządzanego usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przeprowadzić migrację pakietów usług SQL Server Integration Services do wystąpienia zarządzanego usługi Azure SQL Database.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/08/2019
ms.openlocfilehash: 82a047616c199e37bfa22f53e02f3f7b224b47c1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083186"
---
# <a name="migrate-sql-server-integration-services-packages-to-an-azure-sql-database-managed-instance"></a>Migrowanie pakietów usług SQL Server Integration Services do wystąpienia zarządzanego usługi Azure SQL Database
Jeśli używasz programu SQL Server Integration Services (SSIS) i chcesz przeprowadzić migrację projektów SSIS/pakietów ze źródła danych SSISDB hostowaną przez program SQL Server do miejsca docelowego danych SSISDB hostowaną przez wystąpienie zarządzane usługi Azure SQL Database, możesz użyć usługi Azure Database Migration Service.

Jeśli wersja korzystasz z usług SSIS jest starsza niż 2012 lub użyć typów magazynów pakietu-bazy danych SSISDB, przed projektów migracji usługi SSIS/pakietów, musisz konwertować je za pomocą integracji usług projektu konwersji kreatora, w którym można także uruchomić z programu SSMS. Aby uzyskać więcej informacji, zobacz artykuł [konwertowanie projektów do modelu wdrażania projektu](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert).

> [!NOTE]
> Azure Database Migration Service (DMS) aktualnie nie obsługuje usługi Azure SQL Database jako docelowej migracji. Aby przeprowadzić ponowne wdrożenie projektów SSIS/pakietów do usługi Azure SQL Database, zobacz artykuł [pakiety ponowne wdrożenie programu SQL Server Integration Services do usługi Azure SQL Database](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

W tym artykule omówiono sposób wykonywania następujących zadań:
> [!div class="checklist"]
>
> * Oceń źródła projektów SSIS/pakietów.
> * Migrowanie projektów SSIS/pakietów na platformę Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać te kroki, potrzebne są:

* Do tworzenia usługi Azure Virtual Network (VNet) dla usługi Azure Database Migration Service przy użyciu modelu wdrażania usługi Azure Resource Manager, co zapewnia łączność lokacja lokacja z serwerów źródłowych w środowisku lokalnym przy użyciu [usługi ExpressRoute ](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Aby uzyskać więcej informacji, zobacz artykuł [topologie sieci na potrzeby usługi Azure SQL Database managed migracji wystąpienia przy użyciu usługi Azure Database Migration Service]( https://aka.ms/dmsnetworkformi). Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej, zobacz [dokumentacja usługi Virtual Network](https://docs.microsoft.com/azure/virtual-network/), a szczególnie artykułów Szybki Start krok po kroku szczegółowe informacje.
* Aby upewnić się, że reguły sieci wirtualnej, sieciowej grupy zabezpieczeń nie blokują następujących portów komunikacji przychodzącej do usługi Azure Database Migration Service: 443, 53, 9354, 445, 12000. Aby uzyskać więcej szczegółowych informacji dotyczących filtrowania ruchu sieciowej grupy zabezpieczeń w sieci wirtualnej platformy Azure, zobacz artykuł [filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
* Aby skonfigurować swoje [Windows zapory dla dostępu aparatu bazy danych źródła](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access?view=sql-server-2017).
* Aby otworzyć zaporę Windows, aby umożliwić usłudze Azure Database Migration Service, uzyskiwanie dostępu do źródła programu SQL Server, czyli domyślnie TCP port 1433.
* Jeśli uruchomiono wiele nazwanych wystąpień programu SQL Server przy użyciu portów dynamicznych, konieczne może być włączenie usługi SQL Browser i zezwolenie na dostęp do portu UDP 1434 przez zapory, tak aby usługa Azure Database Migration Service mogła połączyć się z nazwanym wystąpieniem na serwerze źródłowym.
* W przypadku korzystania z urządzenia zapory przed źródłowymi bazami danych konieczne może być dodanie reguł zapory, aby zezwolić usłudze Azure Database Migration Service na dostęp do źródłowych baz danych podczas migracji oraz do plików za pośrednictwem portu SMB 445.
* Usługi Azure SQL Database zarządzane wystąpienia do hostowania bazy SSISDB. Jeśli musisz utworzyć jeden, postępuj zgodnie z szczegółowo w artykule [Tworzenie wystąpienia usługi Azure SQL Database Managed](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started).
* Aby upewnić się, że dane logowania używany do łączenia ze źródłem programu SQL Server i docelowego wystąpienia zarządzanego są członkami roli serwera sysadmin.
* Aby sprawdzić, czy SSIS jest aprowizowane w usłudze Azure Data Factory (ADF) zawierający środowiska Azure-SSIS Integration Runtime (IR) z lokalizacją docelową bazą danych SSISDB hostowaną przez usługi Azure SQL Database wystąpienia zarządzanego (zgodnie z opisem w artykule [tworzenie środowiska Azure-SSIS Infrastruktura Integration runtime w usłudze Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)).

## <a name="assess-source-ssis-projectspackages"></a>Ocena projektów SSIS/pakietów źródła

Podczas oceny źródłowej bazy danych SSISDB nie są jeszcze zintegrowane w bazie danych Migration Assistant (DMA), projektów SSIS/pakietów zostanie ocenione/zweryfikowana, ponieważ są one ponownie wdrażana docelowej bazy danych SSISDB hostowanego w wystąpieniu zarządzanym usługi Azure SQL Database.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Rejestrowanie dostawcy zasobów Microsoft.DataMigration

1. Zaloguj się w witrynie Azure Portal, wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

    ![Wyświetlanie subskrypcji w portalu](media/how-to-migrate-ssis-packages-mi/portal-select-subscriptions.png)

2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie usługi Azure Database Migration Service, a następnie wybierz **dostawców zasobów**.

    ![Wyświetlanie dostawców zasobów](media/how-to-migrate-ssis-packages-mi/portal-select-resource-provider.png)

3. Wyszukaj „migration”, a następnie po prawej stronie pozycji **Microsoft.DataMigration** wybierz pozycję **Zarejestruj**.

    ![Rejestrowanie dostawcy zasobów](media/how-to-migrate-ssis-packages-mi/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Tworzenie wystąpienia usługi Azure Database Migration Service

1. W witrynie Azure Portal wybierz pozycję + **Utwórz zasób**, wyszukaj usługę **Azure Database Migration Service**, a następnie wybierz usługę **Azure Database Migration Service** z listy rozwijanej.

     ![Azure Marketplace](media/how-to-migrate-ssis-packages-mi/portal-marketplace.png)

2. Na ekranie **Azure Database Migration Service** wybierz polecenie **Utwórz**.

    ![Tworzenie wystąpienia usługi Azure Database Migration Service](media/how-to-migrate-ssis-packages-mi/dms-create1.png)

3. Na ekranie **Tworzenie usługi migracji** określ nazwę usługi, subskrypcję oraz nową lub istniejącą grupę zasobów.

4. Wybierz lokalizację, w której chcesz utworzyć wystąpienie usługi DMS.

5. Wybieranie istniejącej sieci wirtualnej lub utworzyć nowe.

    Sieć wirtualna zapewnia dostęp do źródła programu SQL Server i wystąpienia zarządzanego usługi Azure SQL Database docelowej usługi Azure Database Migration Service.

    Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej w witrynie Azure portal, zobacz artykuł [Utwórz sieć wirtualną przy użyciu witryny Azure portal](https://aka.ms/DMSVnet).

    Aby uzyskać więcej informacji zobacz artykuł [Topologie sieci na potrzeby migracji wystąpienia zarządzanego usługi Azure SQL DB przy użyciu usługi Azure Database Migration Service](https://aka.ms/dmsnetworkformi).

6. Wybierz warstwę cenową.

    Aby uzyskać więcej informacji na temat kosztów i warstw cenowych, zobacz [stronę z cennikiem](https://aka.ms/dms-pricing).

    ![Tworzenie usługi DMS](media/how-to-migrate-ssis-packages-mi/dms-create-service2.png)

7. Wybierz pozycję **Utwórz**, aby utworzyć usługę.

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji

Po utworzeniu wystąpienia usługi znajdź je w witrynie Azure Portal, otwórz je, a następnie utwórz nowy projekt migracji.

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz pozycję **Azure Database Migration Services**.

    ![Znajdowanie wszystkich wystąpień usługi Azure Database Migration Service](media/how-to-migrate-ssis-packages-mi/dms-search.png)

2. Na ekranie **Azure Database Migration Service** wyszukaj nazwę utworzonego wystąpienia, a następnie wybierz to wystąpienie.

3. Wybierz pozycję + **Nowy projekt migracji**.

4. Na **nowy projekt migracji** ekranu, określ nazwę dla projektu, w **typ serwera źródła** pola tekstowego, wybierz opcję **programu SQL Server**w **serwera docelowego Typ** pola tekstowego, wybierz opcję **wystąpienia zarządzanego Azure SQL Database**, a następnie dla **wybierz typ działania**, wybierz opcję **migracjipakietówSSIS**.

   ![Tworzenie projektu usługi DMS](media/how-to-migrate-ssis-packages-mi/dms-create-project2.png)

5. Wybierz polecenie **Create** (Utwórz), aby utworzyć projekt.

## <a name="specify-source-details"></a>Określanie szczegółów źródła

1. Na ekranie **Szczegóły źródła migracji** określ szczegóły połączenia dla źródłowego programu SQL Server.

2. Jeśli nie zainstalowano zaufanego certyfikatu na serwerze, zaznacz pole wyboru **Certyfikat serwera zaufania**.

    Jeśli zaufany certyfikat nie został zainstalowany, program SQL Server wygeneruje certyfikat z podpisem własnym po uruchomieniu wystąpienia. Ten certyfikat jest używany do szyfrowania poświadczeń połączeń klienta.

    > [!CAUTION]
    > Połączenia SSL, które są szyfrowane przy użyciu certyfikatu z podpisem własnym, nie zapewniają silnego zabezpieczenia. Są one podatne na ataki typu „man-in-the-middle”. Nie należy polegać na połączeniach SSL z użyciem certyfikatów z podpisem własnym w środowisku produkcyjnym ani na serwerach, które są połączone z Internetem.

   ![Szczegóły źródła](media/how-to-migrate-ssis-packages-mi/dms-source-details1.png)

3. Wybierz pozycję **Zapisz**.

## <a name="specify-target-details"></a>Określanie szczegółów elementu docelowego

1. Na **szczegóły lokalizacji docelowej migracji** ekranu, określ szczegóły połączenia dla elementu docelowego.

     ![Szczegóły elementu docelowego](media/how-to-migrate-ssis-packages-mi/dms-target-details2.png)

2. Wybierz pozycję **Zapisz**.

## <a name="review-the-migration-summary"></a>Przeglądanie podsumowania migracji

1. Na ekranie **Podsumowanie migracji** w polu tekstowym **Nazwa działania** określ nazwę działania migracji.

2. Aby uzyskać **projektów SSIS i środowisk opcja zastępowania**, określ, czy należy zastąpić, lub zignorować istniejących projektów SSIS i środowisk.

    ![Podsumowanie projektu migracji](media/how-to-migrate-ssis-packages-mi/dms-project-summary2.png)

3. Przejrzyj i sprawdź szczegóły skojarzone z projektem migracji.

## <a name="run-the-migration"></a>Uruchamianie migracji

* Wybierz polecenie **Uruchom migrację**.

## <a name="next-steps"></a>Kolejne kroki

* Przejrzyj wskazówki dotyczące migracji w programie Microsoft [Przewodnik po migracji bazy danych](https://datamigration.microsoft.com/).
