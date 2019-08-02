---
title: Punkty końcowe sieci wirtualnej i reguły dla jednej i puli baz danych w usłudze Azure SQL | Microsoft Docs
description: Oznacz podsieć jako punkt końcowy usługi Virtual Network. Następnie punkt końcowy jako reguła sieci wirtualnej do listy ACL Azure SQL Database. Następnie SQL Database akceptuje komunikację ze wszystkich maszyn wirtualnych i innych węzłów w podsieci.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: vanto, genemi
ms.date: 03/12/2019
ms.openlocfilehash: fbe6c4cc82272c7ab82931b089dbc3c70b07bee0
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566220"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-database-servers"></a>Korzystanie z punktów końcowych usługi sieci wirtualnej i reguł dla serwerów baz danych

*Reguły sieci wirtualnej* to jedna funkcja zabezpieczeń zapory, która kontroluje, czy serwer bazy danych dla pojedynczych baz danych i elastycznej puli na platformie Azure [SQL Database](sql-database-technical-overview.md) lub dla baz danych w [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) akceptuje komunikację są wysyłane z określonych podsieci w sieciach wirtualnych. W tym artykule wyjaśniono, dlaczego funkcja reguły sieci wirtualnej jest czasami najlepszą opcją bezpiecznego zezwalania na komunikację z Azure SQL Database i SQL Data Warehouse.

> [!IMPORTANT]
> Ten artykuł ma zastosowanie do programu Azure SQL Server oraz do baz danych SQL Database i SQL Data Warehouse utworzonych na serwerze SQL platformy Azure. Dla uproszczenia usługi SQL Database i SQL Data Warehouse są łącznie nazywane usługą SQL Database. Ten artykuł *nie* dotyczy wdrożenia **wystąpienia zarządzanego** w Azure SQL Database, ponieważ nie ma skojarzonego z nim punktu końcowego usługi.

Aby można było utworzyć regułę sieci wirtualnej, musi ona być [punktem końcowym usługi sieci wirtualnej][vm-virtual-network-service-endpoints-overview-649d] , aby uzyskać odwołanie do reguły.

## <a name="how-to-create-a-virtual-network-rule"></a>Jak utworzyć regułę sieci wirtualnej

Jeśli tworzysz tylko regułę sieci wirtualnej, możesz przejść do kroków i wyjaśnień [w dalszej części tego artykułu](#anchor-how-to-by-using-firewall-portal-59j).

<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>Terminologia i opis

**Sieć wirtualna:** Możesz mieć sieci wirtualne skojarzone z subskrypcją platformy Azure.

**Podsieci** Sieć wirtualna zawiera **podsieci**. Wszystkie maszyny wirtualne platformy Azure, które są przypisane do podsieci. Jedna podsieć może zawierać wiele maszyn wirtualnych lub innych węzłów obliczeniowych. Węzły obliczeniowe znajdujące się poza siecią wirtualną nie mogą uzyskać dostępu do sieci wirtualnej, chyba że skonfigurowano zabezpieczenia, aby zezwolić na dostęp.

**Virtual Network punkt końcowy usługi:** [Punkt końcowy usługi Virtual Network][vm-virtual-network-service-endpoints-overview-649d] jest podsiecią, której wartości właściwości zawierają co najmniej jedną formalną nazwę typu usługi platformy Azure. W tym artykule interesuje Cię nazwę typu **Microsoft. SQL**, która odnosi się do usługi platformy Azure o nazwie SQL Database.

**Reguła sieci wirtualnej:** Reguła sieci wirtualnej dla serwera SQL Database to podsieć wymieniona na liście kontroli dostępu (ACL) serwera SQL Database. Aby można było uzyskać listę ACL SQL Database, podsieć musi zawierać nazwę typu **Microsoft. SQL** .

Reguła sieci wirtualnej instruuje serwer SQL Database, aby akceptował komunikację z każdego węzła znajdującego się w podsieci.

<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>Zalety reguły sieci wirtualnej

Do momentu podjęcia działania maszyny wirtualne w podsieciach nie mogą komunikować się z SQL Database. Jedną z akcji, która ustanawia komunikację, jest utworzenie reguły sieci wirtualnej. Uzasadnienie wyboru podejścia reguły sieci wirtualnej wymaga dyskusji porównującej i kontrastowej obejmującej konkurencyjne opcje zabezpieczeń oferowane przez zaporę.

### <a name="a-allow-access-to-azure-services"></a>A. Zezwalaj na dostęp do usług platformy Azure

W okienku Zapora znajduje się przycisk **włączania/** wyłączania, który ma etykietę **Zezwalaj na dostęp do usług platformy Azure**. Ustawienie **on** umożliwia komunikację ze wszystkimi adresami IP platformy Azure i wszystkimi podsieciami platformy Azure. Te adresy IP lub podsieci platformy Azure mogą nie należeć do użytkownika. To **ustawienie jest** prawdopodobnie dłużej otwierane, niż chcesz, aby SQL Database. Funkcja reguły sieci wirtualnej oferuje znacznie bardziej precyzyjną kontrolę.

### <a name="b-ip-rules"></a>B. Reguły adresów IP

Zapora SQL Database umożliwia określenie zakresów adresów IP, z których ma zostać zaakceptowana komunikacja, SQL Database. To podejście jest odpowiednie dla stabilnych adresów IP, które są poza siecią prywatną platformy Azure. Jednak wiele węzłów wewnątrz sieci prywatnej platformy Azure jest skonfigurowanych przy użyciu *dynamicznych* adresów IP. Dynamiczne adresy IP mogą ulec zmianie, na przykład po ponownym uruchomieniu maszyny wirtualnej. Folly do określenia dynamicznego adresu IP w regule zapory w środowisku produkcyjnym.

Możesz odzyskać opcję IP, uzyskując *statyczny* adres IP dla maszyny wirtualnej. Aby uzyskać szczegółowe informacje, zobacz [Konfigurowanie prywatnych adresów IP dla maszyny wirtualnej przy użyciu Azure Portal][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

Jednak podejście ze statycznym adresem IP może być trudne do zarządzania i jest kosztowne, gdy jest wykonywane w odpowiedniej skali. Reguły sieci wirtualnej są łatwiejsze do ustanowienia i zarządzania.

> [!NOTE]
> Nie można jeszcze mieć SQL Database w podsieci. Jeśli serwer Azure SQL Database był węzłem w podsieci w sieci wirtualnej, wszystkie węzły w sieci wirtualnej mogą komunikować się z SQL Database. W takim przypadku maszyny wirtualne mogą komunikować się z SQL Database bez konieczności używania reguł sieci wirtualnej ani reguł adresów IP.

Jednak od września 2017 usługa Azure SQL Database nie należy jeszcze do usług, które mogą być przypisane do podsieci.

<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>Szczegóły dotyczące reguł sieci wirtualnej

W tej sekcji opisano kilka szczegółowych informacji o regułach sieci wirtualnej.

### <a name="only-one-geographic-region"></a>Tylko jeden region geograficzny

Każdy punkt końcowy usługi Virtual Network ma zastosowanie tylko do jednego regionu platformy Azure. Punkt końcowy nie umożliwia innym regionom akceptowania komunikacji z podsieci.

Każda reguła sieci wirtualnej jest ograniczona do regionu, do którego odnosi się podstawowy punkt końcowy.

### <a name="server-level-not-database-level"></a>Poziom serwera, nie na poziomie bazy danych

Każda reguła sieci wirtualnej ma zastosowanie do całego serwera Azure SQL Database, a nie tylko do jednej konkretnej bazy danych na serwerze. Innymi słowy reguła sieci wirtualnej jest stosowana na poziomie serwera, a nie na poziomie bazy danych.

- Z kolei reguły adresów IP mogą być stosowane na dowolnym poziomie.

### <a name="security-administration-roles"></a>Role administrowania zabezpieczeniami

Istnieje separacja ról zabezpieczeń w administrowaniu punktami końcowymi usługi Virtual Network. Wymagana jest akcja z każdej z następujących ról:

- **Administrator sieci:** &nbsp;Włącz punkt końcowy.
- **Administrator bazy danych:** &nbsp;Zaktualizuj listę kontroli dostępu (ACL), aby dodać daną podsieć do serwera SQL Database.

*Alternatywa RBAC:*

Role administratora sieci i administratora bazy danych mają więcej możliwości niż jest to konieczne do zarządzania regułami sieci wirtualnej. Wymagany jest tylko podzestaw ich możliwości.

Istnieje możliwość korzystania z [kontroli dostępu opartej na rolach (RBAC)][rbac-what-is-813s] na platformie Azure w celu utworzenia jednej roli niestandardowej, która ma tylko niezbędny podzbiór funkcji. Rola niestandardowa może być używana zamiast obejmuje administratora sieci lub administratora bazy danych. Powierzchnia obszaru ekspozycji zabezpieczeń jest niższa w przypadku dodania użytkownika do roli niestandardowej, a nie dodania użytkownika do innych dwóch głównych ról administratora.

> [!NOTE]
> W niektórych przypadkach Azure SQL Database i podsieć wirtualna znajdują się w różnych subskrypcjach. W takich przypadkach należy zapewnić następujące konfiguracje:
> - Obie subskrypcje muszą znajdować się w tej samej dzierżawie Azure Active Directory.
> - Użytkownik ma wymagane uprawnienia do inicjowania operacji, takich jak Włączanie punktów końcowych usługi i Dodawanie podsieci wirtualnej do danego serwera.
> - Obie subskrypcje muszą mieć zarejestrowany dostawca Microsoft. SQL.

## <a name="limitations"></a>Ograniczenia

W przypadku Azure SQL Database funkcja reguł sieci wirtualnej ma następujące ograniczenia:

- Aplikację sieci Web można zamapować na prywatny adres IP w sieci wirtualnej/podsieci. Nawet jeśli punkty końcowe usługi są włączone w danej sieci wirtualnej/podsieci, połączenia z aplikacji sieci Web na serwerze będą mieć publiczne Źródło IP platformy Azure, a nie Źródło sieci wirtualnej/podsieci. Aby włączyć łączność z aplikacji sieci Web na serwerze z regułami zapory VNet, należy **zezwolić usługom platformy Azure na dostęp do serwera** na serwerze.

- W zaporze dla SQL Database Każda reguła sieci wirtualnej odwołuje się do podsieci. Wszystkie te podsieci, do których istnieją odwołania, muszą być hostowane w tym samym regionie geograficznym, który hostuje SQL Database.

- Każdy serwer Azure SQL Database może mieć maksymalnie 128 wpisów listy ACL dla danej sieci wirtualnej.

- Reguły sieci wirtualnej mają zastosowanie tylko do Azure Resource Manager sieci wirtualnych; i nie do [klasycznych sieci modeli wdrażania][arm-deployment-model-568f] .

- Włączenie punktów końcowych usługi sieci wirtualnej w celu Azure SQL Database również umożliwia korzystanie z punktów końcowych dla usług MySQL i PostgreSQL platformy Azure. Jednak w przypadku punktów końcowych w systemie próby nawiązania połączenia z punktami końcowymi z wystąpieniami MySQL lub PostgreSQL mogą zakończyć się niepowodzeniem.
  - Podstawowym powodem jest to, że dla programu MySQL i PostgreSQL prawdopodobnie nie skonfigurowano reguły sieci wirtualnej. Należy skonfigurować regułę sieci wirtualnej dla Azure Database for MySQL i PostgreSQL, a połączenie powiedzie się.

- Na zaporze zakresy adresów IP dotyczą następujących elementów sieciowych, ale reguły sieci wirtualnej nie są:
  - [Wirtualna sieć prywatna (VPN) typu lokacja-lokacja (S2S)][vpn-gateway-indexmd-608y]
  - Lokalna za pośrednictwem [ExpressRoute][expressroute-indexmd-744v]

### <a name="considerations-when-using-service-endpoints"></a>Zagadnienia dotyczące korzystania z punktów końcowych usługi

Korzystając z punktów końcowych usługi dla Azure SQL Database, zapoznaj się z następującymi kwestiami:

- **Wymagane do Azure SQL Database publicznych adresów IP**: Grupy zabezpieczeń sieci (sieciowych grup zabezpieczeń) muszą zostać otwarte, aby Azure SQL Database adresy IP w celu umożliwienia łączności. Można to zrobić za pomocą [tagów usługi](../virtual-network/security-overview.md#service-tags) sieciowej grupy zabezpieczeń dla Azure SQL Database.

### <a name="expressroute"></a>ExpressRoute

jeśli korzystasz z usługi [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) w środowisku lokalnym na potrzeby publicznej komunikacji równorzędnej lub komunikacji równorzędnej firmy Microsoft, konieczne będzie określenie używanych adresów IP translatora adresów sieciowych. W przypadku publicznej komunikacji równorzędnej każdy obwód usługi ExpressRoute domyślnie używa dwóch adresów IP translatora adresów sieciowych stosowanych do ruchu w ramach usługi platformy Azure, gdy ruch trafia do sieci szkieletowej platformy Microsoft Azure. W przypadku komunikacji równorzędnej firmy Microsoft używane adresy IP translatora adresów sieciowych są dostarczane przez klienta lub przez dostawcę usług. Aby umożliwić dostęp do zasobów usługi, musisz zezwolić na te publiczne adresy IP w ustawieniu zapory adresu IP zasobu. Aby znaleźć adresy IP obwodów usługi ExpressRoute publicznej komunikacji równorzędnej, [otwórz bilet pomocy technicznej przy użyciu usługi ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) w witrynie Azure Portal. Dowiedz się więcej o [translatorze adresów sieciowych publicznej komunikacji równorzędnej i komunikacji równorzędnej firmy Microsoft dla usługi ExpressRoute.](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)
  
Aby umożliwić komunikację z obwodu do Azure SQL Database, należy utworzyć reguły sieci IP dla publicznych adresów IP translatora adresów sieciowych.

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-removing-allow-azure-services-to-access-server"></a>Wpływ usunięcia "Zezwalaj na usługi platformy Azure na dostęp do serwera"

Wielu użytkowników chce usunąć opcję **Zezwól usługom platformy Azure na dostęp do serwera** z serwerów usługi Azure SQL Server i zamienić ją na regułę zapory sieci wirtualnej.
Usunięcie tego dotyczy jednak następujących funkcji:

### <a name="import-export-service"></a>Importuj usługę eksportu

Usługa Azure SQL Database Import Export jest uruchamiana na maszynach wirtualnych na platformie Azure. Te maszyny wirtualne nie znajdują się w sieci wirtualnej, dlatego Uzyskaj adres IP platformy Azure podczas łączenia się z bazą danych. Po usunięciu **Zezwalaj usługom platformy Azure na dostęp do serwera** te maszyny wirtualne nie będą mogły uzyskać dostępu do baz danych.
Można obejść ten problem. Uruchom BACPAC zaimportować lub wyeksportować bezpośrednio w kodzie za pomocą interfejsu API DACFx. Upewnij się, że jest ona wdrożona na maszynie wirtualnej, która znajduje się w podsieci wirtualnej, dla której ustawiono regułę zapory.

### <a name="sql-database-query-editor"></a>Edytor zapytań SQL Database

Edytor zapytań Azure SQL Database jest wdrażany na maszynach wirtualnych na platformie Azure. Te maszyny wirtualne nie znajdują się w sieci wirtualnej. W związku z tym maszyny wirtualne uzyskują adres IP platformy Azure podczas łączenia się z bazą danych. Po usunięciu **Zezwalaj usługom platformy Azure na dostęp do serwera**te maszyny wirtualne nie będą mogły uzyskać dostępu do baz danych.

### <a name="table-auditing"></a>Inspekcja tabeli

Obecnie istnieją dwa sposoby włączania inspekcji na SQL Database. Inspekcja tabeli kończy się niepowodzeniem po włączeniu punktów końcowych usługi w usłudze Azure SQL Server. Środki zaradcze w tym miejscu umożliwiają przejście do inspekcji obiektów BLOB.

### <a name="impact-on-data-sync"></a>Wpływ na synchronizację danych

Azure SQL Database zawiera funkcję synchronizacji danych, która łączy się z bazami danych przy użyciu adresów IP platformy Azure. Gdy korzystasz z punktów końcowych usługi, prawdopodobnie wyłączysz opcję **Zezwól usługom platformy Azure na dostęp do serwera** na serwerze SQL Database. Spowoduje to przerwanie funkcji synchronizacji danych.

## <a name="impact-of-using-vnet-service-endpoints-with-azure-storage"></a>Wpływ używania punktów końcowych usługi sieci wirtualnej z usługą Azure Storage

Usługa Azure Storage zaimplementował tę samą funkcję, która umożliwia ograniczenie łączności z kontem usługi Azure Storage. Jeśli zdecydujesz się używać tej funkcji z kontem usługi Azure Storage, które jest używane przez usługę Azure SQL Server, możesz wypróbować problemy. Poniżej znajduje się lista i Omówienie funkcji Azure SQL Database i Azure SQL Data Warehouse, których dotyczy problem.

### <a name="azure-sql-data-warehouse-polybase"></a>Azure SQL Data Warehouse baza

Baza danych wielobase jest często używana do ładowania dane do Azure SQL Data Warehouse z kont usługi Azure Storage. Jeśli konto usługi Azure Storage, z którego są ładowane dane, ogranicza dostęp tylko do zestawu podsieci sieci wirtualnej, nastąpi przerwanie łączności z bazą danych na koncie. Aby włączyć oba podstawowe scenariusze importu i eksportu z Azure SQL Data Warehouse łączenia się z usługą Azure Storage, która jest zabezpieczona w sieci wirtualnej, wykonaj kroki opisane poniżej:

#### <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne.

1.  Zainstaluj Azure PowerShell przy użyciu [](https://docs.microsoft.com/powershell/azure/install-az-ps)tego przewodnika.
2.  Jeśli masz konto usługi Magazyn ogólnego przeznaczenia w wersji 1 lub BLOB, musisz najpierw przeprowadzić uaktualnienie do ogólnego przeznaczenia w wersji 2 przy [](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade)użyciu tego przewodnika.
3.  Musisz **zezwolić zaufanym usługom firmy Microsoft na dostęp do tego konta magazynu** , włączone w obszarze zapory konta usługi Azure Storage i menu ustawienia **sieci wirtualnych** . Aby uzyskać więcej [](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) informacji, zapoznaj się z tym przewodnikiem.
 
#### <a name="steps"></a>Kroki
1. W programie PowerShell **zarejestruj serwer SQL Database** przy użyciu usługi Azure Active Directory (AAD):

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId your-subscriptionId
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-database-servername -AssignIdentity
   ```
    
   1. Utwórz **konto magazynu ogólnego przeznaczenia w wersji 2** za pomocą [](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)tego przewodnika.

   > [!NOTE]
   > - Jeśli masz konto usługi Magazyn ogólnego przeznaczenia w wersji 1 lub BLOB, musisz **najpierw przeprowadzić uaktualnienie do wersji 2** przy [](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade)użyciu tego przewodnika.
   > - Aby uzyskać znane problemy z Azure Data Lake Storage Gen2, zapoznaj się [](https://docs.microsoft.com/azure/storage/data-lake-storage/known-issues)z tym przewodnikiem.
    
1. W obszarze konto magazynu przejdź do pozycji **Access Control (IAM)** , a następnie kliknij pozycję **Dodaj przypisanie roli**. Przypisz rolę RBAC **współautor danych obiektów blob magazynu** do serwera SQL Database.

   > [!NOTE] 
   > Tylko członkowie z uprawnieniami właściciela mogą wykonać ten krok. Aby uzyskać różne wbudowane role dla zasobów platformy Azure, zapoznaj się [](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)z tym przewodnikiem.
  
1. **Połączenie z kontem usługi Azure Storage:**

   1. Utwórz **[klucz główny](https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql)** bazy danych, jeśli nie został utworzony wcześniej:
       ```SQL
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```
    
   1. Utwórz poświadczenie o zakresie bazy danych o **tożsamości = "tożsamość usługi zarządzanej"** :

       ```SQL
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```
       > [!NOTE] 
       > - Nie ma potrzeby określania wpisu TAJNego za pomocą klucza dostępu do usługi Azure Storage, ponieważ ten mechanizm używa [tożsamości zarządzanej](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) w ramach okładek.
       > - Nazwa tożsamości powinna mieć nazwę **"tożsamość usługi zarządzanej"** , aby nawiązać połączenie z kontem usługi Azure Storage zabezpieczone do sieci wirtualnej.    
    
   1. Utwórz zewnętrzne źródło danych ze schematem abfss://na potrzeby nawiązywania połączenia z kontem magazynu ogólnego przeznaczenia w wersji 2 przy użyciu sieci podstawowej:

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```
       > [!NOTE] 
       > - Jeśli masz już tabele zewnętrzne skojarzone z kontem ogólnego przeznaczenia w wersji 1 lub BLOB, należy najpierw porzucić te tabele zewnętrzne, a następnie porzucić odpowiednie zewnętrzne źródło danych. Następnie utwórz zewnętrzne źródło danych z abfss://schematem łączącym się z kontem magazynu ogólnego przeznaczenia w wersji 2 jak powyżej i ponownie Utwórz wszystkie tabele zewnętrzne przy użyciu tego nowego zewnętrznego źródła danych. [Kreatora generowania i publikowania skryptów](https://docs.microsoft.com/sql/ssms/scripting/generate-and-publish-scripts-wizard) można użyć do łatwego generowania skryptów Create-scripts dla wszystkich tabel zewnętrznych.
       > - Więcej informacji o schemacie abfss://można znaleźć w tym [przewodniku](https://docs.microsoft.com/azure/storage/data-lake-storage/introduction-abfs-uri).
       > - Aby uzyskać więcej informacji na temat tworzenia zewnętrznego źródła danych, zapoznaj się z tym [przewodnikiem](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql).
        
   1. Zapytanie jako normalne przy użyciu [tabel zewnętrznych](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql).

### <a name="azure-sql-database-blob-auditing"></a>Inspekcja Azure SQL Database obiektów BLOB

Inspekcja obiektów BLOB powoduje wypychanie dzienników inspekcji na własne konto magazynu. Jeśli to konto magazynu używa funkcji punktów końcowych usługi sieci wirtualnej, nastąpi przerwanie łączności z Azure SQL Database na koncie magazynu.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Dodawanie reguły zapory sieci wirtualnej do serwera bez włączania punktów końcowych usługi sieci wirtualnej

Dawniej przed zwiększeniem tej funkcji przed implementacją reguły sieci wirtualnej na żywo w zaporze musisz włączyć punkty końcowe usługi sieci wirtualnej. Punkty końcowe powiązane z daną podsiecią sieci wirtualnej do Azure SQL Database. Jednak od stycznia 2018 można obejść to wymaganie, ustawiając flagę **IgnoreMissingVNetServiceEndpoint** .

Ustawienie reguły zapory nie pomaga w zabezpieczeniu serwera. Aby zabezpieczenia zaczęły obowiązywać, należy również włączyć punkty końcowe usługi sieci wirtualnej. Po włączeniu punktów końcowych usługi w usłudze Sieć wirtualna jest przestojem do momentu zakończenia przejścia z trybu do trybu. Jest to szczególnie prawdziwe w kontekście dużych sieci wirtualnych. Możesz użyć flagi **IgnoreMissingVNetServiceEndpoint** , aby zmniejszyć lub wyeliminować przestoje podczas przejścia.

Flagę **IgnoreMissingVNetServiceEndpoint** można ustawić przy użyciu programu PowerShell. Aby uzyskać szczegółowe informacje, zobacz Program [PowerShell, aby utworzyć Virtual Network punkt końcowy usługi i regułę dla Azure SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="errors-40914-and-40615"></a>Błędy 40914 i 40615

Błąd połączenia 40914 odnosi się do *reguł sieci wirtualnej*określonych w okienku zapora w Azure Portal. Błąd 40615 jest podobny, z tą różnicą, że odnosi się do *reguł adresów IP* w zaporze.

### <a name="error-40914"></a>Błąd 40914

*Tekst komunikatu:* Nie można otworzyć serwera " *[nazwa serwera]* " żądanego podczas logowania. Klient nie może uzyskać dostępu do serwera.

*Opis błędu:* Klient należy do podsieci, która ma punkty końcowe serwera sieci wirtualnej. Ale serwer Azure SQL Database nie ma reguły sieci wirtualnej, która przyznaje podsieci prawo do komunikowania się z SQL Database.

*Rozwiązywanie błędów:* W okienku Zapora w Azure Portal użyj kontrolki reguły sieci wirtualnej, aby [dodać regułę sieci wirtualnej](#anchor-how-to-by-using-firewall-portal-59j) dla podsieci.

### <a name="error-40615"></a>Błąd 40615

*Tekst komunikatu:* Nie można otworzyć serwera{0}"" żądanego przez nazwę logowania. Klient z adresem IP "{1}" nie może uzyskać dostępu do serwera.

*Opis błędu:* Klient próbuje nawiązać połączenie z adresu IP, który nie jest autoryzowany do nawiązywania połączenia z serwerem Azure SQL Database. Zapora serwera nie ma reguły adresu IP zezwalającej klientowi na komunikację z usługą SQL Database z tego adresu IP.

*Rozwiązywanie błędów:* Wprowadź adres IP klienta jako regułę adresu IP. W tym celu należy użyć okienka Zapora w Azure Portal.

Lista kilku SQL Databaseych komunikatów o błędach jest udokumentowana w [tym miejscu][sql-database-develop-error-messages-419g].

<a name="anchor-how-to-by-using-firewall-portal-59j" />

## <a name="portal-can-create-a-virtual-network-rule"></a>Portal może utworzyć regułę sieci wirtualnej

W tej sekcji pokazano, jak można użyć [Azure Portal][http-azure-portal-link-ref-477t] do utworzenia *reguły sieci wirtualnej* w Azure SQL Database. Reguła nakazuje SQL Database do akceptowania komunikacji z określonej podsieci, która została oznaczona jako *punkt końcowy usługi Virtual Network*.

> [!NOTE]
> Jeśli zamierzasz dodać punkt końcowy usługi do reguł zapory sieci wirtualnej serwera Azure SQL Database, najpierw upewnij się, że punkty końcowe usługi są włączone dla tej podsieci.
>
> Jeśli punkty końcowe usługi nie są włączone dla podsieci, w portalu zostanie wyświetlony monit o ich włączenie. Kliknij przycisk **Włącz** w tym samym bloku, w którym została dodana reguła.

## <a name="powershell-alternative"></a>Alternatywa dla programu PowerShell

Skrypt programu PowerShell może również tworzyć reguły sieci wirtualnej. Kluczowe polecenie cmdlet **New-AzSqlServerVirtualNetworkRule**. Jeśli chcesz, zobacz Program [PowerShell, aby utworzyć Virtual Network punkt końcowy usługi i regułę dla Azure SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="rest-api-alternative"></a>Alternatywa dla interfejsu API REST

Wewnętrznie polecenia cmdlet programu PowerShell dla akcji sieci wirtualnej SQL wywołują interfejsy API REST. Interfejsy API REST można wywołać bezpośrednio.

- [Reguły Virtual Network: Składowa][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Wymagania wstępne

Musisz mieć już podsieć, która jest otagowana przy użyciu konkretnej *nazwy typu* punktu końcowego usługi Virtual Network odpowiedniej dla Azure SQL Database.

- Odpowiednia nazwa typu punktu końcowego to **Microsoft. SQL**.
- Jeśli podsieć może nie być otagowana przy użyciu nazwy typu, zobacz [Sprawdź, czy podsieć jest punktem końcowym][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200" />

## <a name="azure-portal-steps"></a>Azure Portal kroki

1. Zaloguj się w witrynie [Azure Portal][http-azure-portal-link-ref-477t].

2. Następnie przejdź do portalu do **serwerów** &gt; SQL **Zapora/sieci wirtualne**.

3. Ustaw ustawienie **Zezwalaj na dostęp do usług platformy Azure** .

    > [!IMPORTANT]
    > W przypadku pozostawienia kontrolki ustawionej na wartość włączone serwer Azure SQL Database akceptuje komunikację z dowolnych podsieci. Pozostawienie kontrolki ustawionej na włączone może być nadmierne dostęp z punktu widzenia zabezpieczeń. Funkcja punktu końcowego usługi Microsoft Azure Virtual Network, w połączeniu z funkcją reguły sieci wirtualnej SQL Database, umożliwia zredukowanie obszaru zabezpieczeń.

4. Kliknij przycisk **+ Dodaj istniejący** formant, w sekcji **sieci wirtualne** .

    ![Kliknij pozycję Dodaj istniejący (punkt końcowy podsieci, jako regułę SQL).][image-portal-firewall-vnet-add-existing-10-png]

5. W okienku Nowy **Utwórz/zaktualizuj** Wypełnij kontrolki nazwami zasobów platformy Azure.

    > [!TIP]
    > Należy podać prawidłowy **prefiks adresu** dla podsieci. Wartość można znaleźć w portalu.
    > Przejdź do **wszystkich zasobów** &gt; **wszystkie typy** &gt; **sieci wirtualne**. Filtr Wyświetla sieci wirtualne. Kliknij sieć wirtualną, a następnie kliknij pozycję **podsieci**. Kolumna **zakres adresów** ma prefiks adresu, którego potrzebujesz.

    ![Wypełnij pola dla nowej reguły.][image-portal-firewall-create-update-vnet-rule-20-png]

6. Kliknij przycisk **OK** w dolnej części okienka.

7. Zobacz zasadę reguły sieci wirtualnej w okienku Zapora.

    ![Zapoznaj się z nową regułą w okienku Zapora.][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> Następujące stany lub Stany stosują się do zasad:
> - **Gotowe** Wskazuje, że zainicjowana operacja zakończyła się pomyślnie.
> - **Awarii** Wskazuje, że zainicjowana operacja zakończyła się niepowodzeniem.
> - **Skasowan** Dotyczy tylko operacji usuwania i wskazuje, że reguła została usunięta i nie ma już zastosowania.
> - **Toku** Wskazuje, że operacja jest w toku. Stara reguła jest stosowana, gdy ta operacja jest w tym stanie.

<a name="anchor-how-to-links-60h" />

## <a name="related-articles"></a>Pokrewne artykuły:

- [Punkty końcowe usługi sieci wirtualnej platformy Azure][vm-virtual-network-service-endpoints-overview-649d]
- [Azure SQL Database reguły zapory na poziomie serwera i na poziomie bazy danych][sql-db-firewall-rules-config-715d]

Funkcja reguły sieci wirtualnej dla Azure SQL Database stała się dostępna z opóźnieniem 2017 września.

## <a name="next-steps"></a>Następne kroki

- [Użyj programu PowerShell, aby utworzyć punkt końcowy usługi sieci wirtualnej, a następnie regułę sieci wirtualnej dla Azure SQL Database.][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Reguły Virtual Network: Operacje][rest-api-virtual-network-rules-operations-862r] przy użyciu interfejsów API REST

<!-- Link references, to images. -->

[image-portal-firewall-vnet-add-existing-10-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png

[image-portal-firewall-create-update-vnet-rule-20-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png

[image-portal-firewall-vnet-result-rule-30-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png

<!-- Link references, to text, Within this same GitHub repo. -->

[arm-deployment-model-568f]: ../azure-resource-manager/resource-manager-deployment-model.md

[expressroute-indexmd-744v]: ../expressroute/index.yml

[rbac-what-is-813s]:../role-based-access-control/overview.md

[sql-db-firewall-rules-config-715d]: sql-database-firewall-configure.md

[sql-database-develop-error-messages-419g]: sql-database-develop-error-messages.md

[sql-db-vnet-service-endpoint-rule-powershell-md-52d]: sql-database-vnet-service-endpoint-rule-powershell.md

[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]: sql-database-vnet-service-endpoint-rule-powershell.md#a-verify-subnet-is-endpoint-ps-100

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[vm-virtual-network-service-endpoints-overview-649d]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

<!-- Link references, to text, Outside this GitHub repo (HTTP). -->

[http-azure-portal-link-ref-477t]: https://portal.azure.com/

[rest-api-virtual-network-rules-operations-862r]: https://docs.microsoft.com/rest/api/sql/virtualnetworkrules

<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON

- Azure CLI

- ARM templates
-->
