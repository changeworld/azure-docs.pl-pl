---
title: Migrowanie pakietów SSIS do wystąpienia zarządzanego SQL
titleSuffix: Azure Database Migration Service
description: Dowiedz się, jak migrować pakiety i projekty usług integracji programu SQL Server (SSIS) do wystąpienia zarządzanego usługi Azure SQL Database przy użyciu usługi migracji bazy danych Azure lub Asystenta migracji danych.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 97a466ab033a42016c0d82465d1f98e2dcae8080
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297183"
---
# <a name="migrate-sql-server-integration-services-packages-to-an-azure-sql-database-managed-instance"></a>Migrowanie pakietów SQL Server Integration Services do wystąpienia zarządzanego usługi Azure SQL Database
Jeśli używasz usług SQL Server Integration Services (SSIS) i chcesz przeprowadzić migrację projektów/pakietów SSIS ze źródła SSISDB hostowanego przez program SQL Server do docelowego pliku SSISDB obsługiwanego przez wystąpienie zarządzanego usługi Azure SQL Database, możesz użyć usługi migracji bazy danych Azure.

Jeśli używana wersja SSIS jest wcześniejsza niż 2012 lub używasz typów magazynu pakietów innych niż SSISDB, przed migracją projektów/pakietów SSIS, należy je przekonwertować za pomocą Integration Services Project Conversion Wizard, który można również uruchamiać z usługi SSMS. Aby uzyskać więcej informacji, zobacz artykuł [Konwertowanie projektów na model wdrażania projektu](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert).

> [!NOTE]
> Usługa migracji bazy danych Azure Database (DMS) obecnie nie obsługuje usługi Azure SQL Database jako docelowego miejsca docelowego migracji. Aby ponownie wdrożyć projekty/pakiety SSIS do bazy danych SQL Azure, zobacz artykuł [Ponowne wdrożenie pakietów usług SQL Server Integration Services do bazy danych SQL Database](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

W tym artykule omówiono sposób wykonywania następujących zadań:
> [!div class="checklist"]
>
> * Oceń źródła projektów/pakietów SSIS.
> * Migrowanie projektów/pakietów SSIS na platformę Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać następujące kroki, potrzebujesz:

* Aby utworzyć sieć wirtualną platformy Microsoft Azure dla usługi migracji bazy danych platformy Azure przy użyciu modelu wdrażania usługi Azure Resource Manager, który zapewnia łączność między lokacjami z lokalnymi serwerami źródłowymi przy użyciu usługi [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Aby uzyskać więcej informacji, zobacz artykuł [Topologie sieci dla migracji wystąpień zarządzanych usługi Azure SQL Database przy użyciu usługi migracji bazy danych Azure]( https://aka.ms/dmsnetworkformi). Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej, zobacz [dokumentację sieci wirtualnej,](https://docs.microsoft.com/azure/virtual-network/)a zwłaszcza artykuły przewodnika Szybki start ze szczegółami krok po kroku.
* Aby upewnić się, że reguły sieciowej sieci zabezpieczeń sieci nie blokują następujących portów komunikacji przychodzącej do usługi migracji usługi Azure Database: 443, 53, 9354, 445, 12000. Aby uzyskać więcej informacji na temat filtrowania ruchu sieciowej sieciowej sieciowej w sieci wirtualnej, zobacz artykuł [Filtrowanie ruchu sieciowego z sieciowymi grupami zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
* Aby skonfigurować [Zaporę systemu Windows w celu uzyskania dostępu do aparatu bazy danych źródłowej](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access?view=sql-server-2017).
* Aby otworzyć Zaporę systemu Windows, aby zezwolić usłudze migracji bazy danych platformy Azure na dostęp do źródłowego programu SQL Server, który domyślnie jest portem TCP 1433.
* Jeśli uruchomiono wiele nazwanych wystąpień programu SQL Server przy użyciu portów dynamicznych, konieczne może być włączenie usługi SQL Browser i zezwolenie na dostęp do portu UDP 1434 przez zapory, tak aby usługa Azure Database Migration Service mogła połączyć się z nazwanym wystąpieniem na serwerze źródłowym.
* W przypadku korzystania z urządzenia zapory przed źródłowymi bazami danych konieczne może być dodanie reguł zapory, aby zezwolić usłudze Azure Database Migration Service na dostęp do źródłowych baz danych podczas migracji oraz do plików za pośrednictwem portu SMB 445.
* Wystąpienie zarządzanej bazy danych SQL platformy Azure do obsługi usługi SSISDB. Jeśli chcesz go utworzyć, postępuj zgodnie ze szczegółami w artykule [Tworzenie wystąpienia zarządzanego bazy danych SQL platformy Azure.](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)
* Aby upewnić się, że logowania używane do łączenia źródła SQL Server i docelowego wystąpienia zarządzanego są członkami roli serwera sysadmin.
* Aby sprawdzić, czy usługa SSIS jest aprowidyfikowana w usłudze Azure Data Factory (ADF) zawierającej środowisko uruchomieniowe integracji platformy Azure-SSIS (IR) z docelowym usługą SSISDB hostowanym przez wystąpienie zarządzanego usługi Azure SQL Database (zgodnie z opisem w artykule [Tworzenie środowiska wykonawczego integracji azure-SSIS w usłudze Azure Data Factory).](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)

## <a name="assess-source-ssis-projectspackages"></a>Ocena źródłowych projektów/pakietów SSIS

Podczas oceny źródła SSISDB nie jest jeszcze zintegrowany z Asystentem migracji bazy danych (DMA), projekty/pakiety SSIS zostaną ocenione/zweryfikowane, ponieważ są one ponownie rozmieszczane do docelowego pliku SSISDB hostowanego w wystąpieniu zarządzanym usługi Azure SQL Database.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Rejestrowanie dostawcy zasobów Microsoft.DataMigration

1. Zaloguj się w witrynie Azure Portal, wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

    ![Wyświetlanie subskrypcji w portalu](media/how-to-migrate-ssis-packages-mi/portal-select-subscriptions.png)

2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie usługi migracji bazy danych platformy Azure, a następnie wybierz pozycję **Dostawcy zasobów**.

    ![Wyświetlanie dostawców zasobów](media/how-to-migrate-ssis-packages-mi/portal-select-resource-provider.png)

3. Wyszukaj migrację, a następnie po prawej stronie **microsoft.DataMigration**, wybierz pozycję **Zarejestruj**.

    ![Rejestrowanie dostawcy zasobów](media/how-to-migrate-ssis-packages-mi/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Tworzenie wystąpienia usługi Azure Database Migration Service

1. W portalu Azure wybierz + **Utwórz zasób**, wyszukaj **usługę migracji usługi Azure Database**, a następnie wybierz pozycję Usługa migracji bazy danych Platformy **Azure** z listy rozwijanej.

     ![Azure Marketplace](media/how-to-migrate-ssis-packages-mi/portal-marketplace.png)

2. Na ekranie **Azure Database Migration Service** wybierz polecenie **Utwórz**.

    ![Tworzenie wystąpienia usługi Azure Database Migration Service](media/how-to-migrate-ssis-packages-mi/dms-create1.png)

3. Na ekranie **Tworzenie usługi migracji** określ nazwę usługi, subskrypcję oraz nową lub istniejącą grupę zasobów.

4. Wybierz lokalizację, w której chcesz utworzyć wystąpienie usługi DMS.

5. Wybierz istniejącą sieć wirtualną lub utwórz jedną.

    Sieć wirtualna zapewnia usługę migracji bazy danych platformy Azure z dostępem do źródłowego programu SQL Server i docelowego wystąpienia zarządzanego usługi Azure SQL Database.

    Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej w witrynie Azure portal, zobacz artykuł [Tworzenie sieci wirtualnej przy użyciu portalu Azure](https://aka.ms/DMSVnet).

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

4. Na ekranie **Nowy projekt migracji** określ nazwę projektu w polu tekstowym Typ serwera **źródłowego** wybierz pozycję **SQL Server**, w polu **tekstowym Typ serwera docelowego,** wybierz pozycję **Wystąpienie zarządzanej bazy danych SQL azure**, a następnie wybierz typ działania , wybierz opcję **Migracja pakietów SSIS**. **Choose type of activity**

   ![Tworzenie projektu usługi DMS](media/how-to-migrate-ssis-packages-mi/dms-create-project2.png)

5. Wybierz polecenie **Create** (Utwórz), aby utworzyć projekt.

## <a name="specify-source-details"></a>Określanie szczegółów źródła

1. Na ekranie **Szczegóły źródła migracji** określ szczegóły połączenia dla źródłowego programu SQL Server.

2. Jeśli nie zainstalowano zaufanego certyfikatu na serwerze, zaznacz pole wyboru **Certyfikat serwera zaufania**.

    Jeśli zaufany certyfikat nie został zainstalowany, program SQL Server wygeneruje certyfikat z podpisem własnym po uruchomieniu wystąpienia. Ten certyfikat jest używany do szyfrowania poświadczeń połączeń klienta.

    > [!CAUTION]
    > Połączenia TLS, które są szyfrowane przy użyciu certyfikatu z podpisem własnym, nie zapewniają silnego zabezpieczenia. Są one podatne na ataki typu „man-in-the-middle”. Nie należy polegać na TLS przy użyciu certyfikatów z podpisem własnym w środowisku produkcyjnym lub na serwerach, które są połączone z Internetem.

   ![Szczegóły źródła](media/how-to-migrate-ssis-packages-mi/dms-source-details1.png)

3. Wybierz **pozycję Zapisz**.

## <a name="specify-target-details"></a>Określanie szczegółów elementu docelowego

1. Na ekranie **Szczegóły obiektu docelowego migracji** określ szczegóły połączenia dla obiektu docelowego.

     ![Szczegóły celu](media/how-to-migrate-ssis-packages-mi/dms-target-details2.png)

2. Wybierz **pozycję Zapisz**.

## <a name="review-the-migration-summary"></a>Przeglądanie podsumowania migracji

1. Na ekranie **Podsumowanie migracji** w polu tekstowym **Nazwa działania** określ nazwę działania migracji.

2. W przypadku **opcji zastępowania projektów ssis i środowiska**należy określić, czy należy zastąpić lub zignorować istniejące projekty i środowiska SSIS.

    ![Podsumowanie projektu migracji](media/how-to-migrate-ssis-packages-mi/dms-project-summary2.png)

3. Przejrzyj i sprawdź szczegóły skojarzone z projektem migracji.

## <a name="run-the-migration"></a>Uruchamianie migracji

* Wybierz polecenie **Uruchom migrację**.

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się ze wskazówkami dotyczącymi migracji w [Przewodniku migracji bazy danych firmy](https://datamigration.microsoft.com/)Microsoft .
