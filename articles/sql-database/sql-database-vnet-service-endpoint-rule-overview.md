---
title: Punkty końcowe usługi sieci wirtualnej i zasady usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Oznacz podsieci jako punkt końcowy usługi sieci wirtualnej. Następnie punkt końcowy jako regułę sieci wirtualnej do listy ACL w usłudze Azure SQL Database. Użytkownik bazy danych SQL następnie akceptuje komunikację z wszystkich maszyn wirtualnych i innych węzłów w tej podsieci.
services: sql-database
ms.service: sql-database
ms.prod_service: sql-database, sql-data-warehouse
author: DhruvMsft
manager: craigg
ms.custom: VNet Service endpoints
ms.topic: conceptual
ms.date: 08/28/2018
ms.reviewer: carlrab
ms.author: dmalik
ms.openlocfilehash: 38806b3dbf47b21885ef704cfec8be3c491a66a0
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44055390"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-sql-database-and-sql-data-warehouse"></a>Na użytek punktów końcowych usługi sieci wirtualnej i zasady usługi Azure SQL Database i SQL Data Warehouse

*Reguły sieci wirtualnej* są jednym zapory funkcja zabezpieczeń, która określa, czy subskrypcji platformy Azure [bazy danych SQL](sql-database-technical-overview.md) lub [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) serwer zaakceptuje łączności, które są wysyłane z określonej podsieci w sieciach wirtualnych. W tym artykule opisano, dlaczego funkcja reguły sieci wirtualnej jest czasami najlepszym rozwiązaniem dla bezpiecznego zezwolenie na komunikację z usługi Azure SQL Database.

> [!NOTE]
> Ten temat dotyczy serwera Azure SQL oraz baz danych zarówno usługi SQL Database, jak i SQL Data Warehouse utworzonych na serwerze Azure SQL. Dla uproszczenia usługi SQL Database i SQL Data Warehouse są łącznie nazywane usługą SQL Database.

Aby utworzyć regułę sieci wirtualnej musi najpierw mieć [punkt końcowy usługi sieci wirtualnej] [ vm-virtual-network-service-endpoints-overview-649d] dla tej reguły odwołać.

#### <a name="how-to-create-a-virtual-network-rule"></a>Jak utworzyć regułę sieci wirtualnej

Jeśli tworzysz tylko reguły sieci wirtualnej, możesz przejść od razu do kroków i wyjaśnienie [w dalszej części tego artykułu](#anchor-how-to-by-using-firewall-portal-59j).






<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>Terminologia i opis

**Sieć wirtualna:** może mieć sieci wirtualne, skojarzony z subskrypcją platformy Azure.

**Podsieć:** sieć wirtualna zawiera **podsieci**. Żadnych maszyn wirtualnych (VM), do których masz są przypisywane do podsieci. W jednej podsieci może zawierać wiele maszyn wirtualnych lub innych węzłów obliczeniowych. Obliczenia, że węzły, które znajdują się poza siecią wirtualną nie może uzyskiwać dostęp do sieci wirtualnej, chyba, że konfigurowanie zabezpieczeń w taki sposób, aby zezwolić na dostęp.

**Punkt końcowy usługi sieci wirtualnej:** A [punkt końcowy usługi sieci wirtualnej] [ vm-virtual-network-service-endpoints-overview-649d] jest podsiecią, w których wartości właściwości zawierają jedną lub więcej nazw typu formalnego usługi platformy Azure. W tym artykule jesteśmy zainteresowani nazwę typu **Microsoft.Sql**, która odnosi się do usługi platformy Azure o nazwie bazy danych SQL.

**Reguła sieci wirtualnej:** reguły sieci wirtualnej dla serwera bazy danych SQL jest podsiecią, który znajduje się na liście kontroli dostępu (ACL) serwera usługi SQL Database. Aby być na liście kontroli dostępu dla usługi SQL Database, musi zawierać podsieci **Microsoft.Sql** nazwy typu.

Reguły sieci wirtualnej informuje serwer bazy danych SQL, aby akceptował komunikację z każdego węzła, który znajduje się w podsieci.







<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>Zalety reguły sieci wirtualnej

Do momentu podejmowanie akcji maszyny wirtualne w podsieci nie może komunikować się z bazą danych SQL. Jedna akcja, która ustanawia komunikacji jest tworzenie reguły sieci wirtualnej. Racjonalne uzasadnienie wyboru podejście reguły sieci wirtualnej wymaga to dyskusji porównania i kontrast, obejmujące konkurencyjnych opcje zabezpieczeń oferowanych przez zaporę.

#### <a name="a-allow-access-to-azure-services"></a>A. Zezwalaj na dostęp do usług platformy Azure

Okienko zapory ma **włączyć/wyłączyć** przycisku, która jest oznaczona etykietą **zezwolić na dostęp do usług platformy Azure**. **ON** ustawienie umożliwia komunikację ze wszystkich adresów IP platformy Azure i wszystkie podsieci platformy Azure. Te adresy IP platformy Azure lub podsieci może nie być własnością użytkownika. To **ON** ustawienie jest prawdopodobnie mniej rygorystyczne, niż potrzebujesz bazy danych SQL jako. Funkcja reguły sieci wirtualnej oferuje znacznie bardziej szczegółową kontrolę.

#### <a name="b-ip-rules"></a>B. Reguły IP

Zaporę usługi SQL Database pozwala określić zakresy adresów IP, z których łączności są akceptowane w bazie danych SQL. To podejście jest odpowiednie dla stabilne adresów IP, które są spoza sieci prywatnej platformy Azure. Jednak wiele węzłów w sieci prywatnej platformy Azure są skonfigurowane przy użyciu *dynamiczne* adresów IP. Dynamiczne adresy IP mogą zmienić, np. po ponownym uruchomieniu maszyny Wirtualnej. Byłoby folly, aby określić dynamiczny adres IP dla reguły zapory w środowisku produkcyjnym.

Opcja IP można odzyskana, uzyskując *statyczne* adresu IP dla maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [skonfigurować prywatnych adresów IP dla maszyny wirtualnej przy użyciu witryny Azure portal][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

Jednak podejście statycznego adresu IP może być trudne do zarządzania i jest kosztowny w przypadku na dużą skalę. Reguły sieci wirtualnej są łatwiejsze ustalenie, jak i do zarządzania.

#### <a name="c-cannot-yet-have-sql-database-on-a-subnet"></a>C. Jeszcze nie masz bazy danych SQL w podsieci

Jeśli serwer usługi Azure SQL Database była węzła w podsieci w sieci wirtualnej, wszystkie węzły w sieci wirtualnej można nawiązać komunikacji z bazą danych SQL. W tym przypadku maszyn wirtualnych z systemem można nawiązać komunikacji z usługą SQL Database bez żadnych reguł sieci wirtualnej lub reguły IP.

Jednak począwszy od września 2017 r. Usługa Azure SQL Database nie jest jeszcze między usługami, które można przypisać do podsieci.






<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>Szczegóły dotyczące reguł sieci wirtualnej

W tej sekcji opisano kilka szczegóły dotyczące reguł sieci wirtualnej.

#### <a name="only-one-geographic-region"></a>Tylko jeden region geograficzny

Każdy punkt końcowy usługi sieci wirtualnej mają zastosowanie do tylko jednego regionu platformy Azure. Punkt końcowy nie uwzględnia innych regionach akceptował komunikację z podsieci.

Reguły sieci wirtualnej jest ograniczona do regionu, który dotyczy podstawowym punktem końcowym.

#### <a name="server-level-not-database-level"></a>Poziom serwera, nie poziom bazy danych

Każda reguła sieci wirtualnej ma zastosowanie do całego serwera Azure SQL Database, nie tylko do jednej konkretnej bazy danych na serwerze. Innymi słowy sieć wirtualną reguła ma zastosowanie na poziomie serwera, nie na poziomie bazy danych.

- Z kolei reguły IP można zastosować na tym poziomie.

#### <a name="security-administration-roles"></a>Ról administracyjnych zabezpieczeń

Brak separacji ról zabezpieczeń w administracji punkty końcowe usługi sieci wirtualnej. Akcja jest wymagana z każdej z następujących ról:

- **Administrator sieci:** &nbsp; włączyć punkt końcowy.
- **Administrator bazy danych:** &nbsp; aktualizacji listy kontroli dostępu (ACL), aby dodać danej podsieci do serwera bazy danych SQL.

*Alternatywa RBAC:*

Role bazy danych i administratora sieci mają więcej możliwości niż są wymagane do zarządzania reguł sieci wirtualnej. Potrzebny jest tylko podzbiór ich możliwości.

Masz możliwość korzystania z [kontroli dostępu opartej na rolach (RBAC)] [ rbac-what-is-813s] na platformie Azure, aby utworzyć pojedynczy rolę niestandardową, która zawiera tylko niezbędne podzbiór funkcji. Rola niestandardowa, można użyć zamiast obejmujące administratora sieci lub administrator bazy danych. Obszar powierzchni usługi ekspozycji zabezpieczeń jest mniejszy, jeśli Dodawanie użytkownika do roli niestandardowej, a dodanie użytkownika do dwóch innych ról administratora głównych.

> [!NOTE]
> W niektórych przypadkach usługi Azure SQL Database i podsieciami sieci wirtualnej znajdują się w różnych subskrypcjach. W takich sytuacjach należy zapewnić następujące konfiguracje:
> - Obie subskrypcje muszą być w tej samej dzierżawie usługi Azure Active Directory.
> - Użytkownik ma uprawnienia wymagane do zainicjowania operacje, takie jak włączenie punktów końcowych usługi i dodawanie podsieci sieci wirtualnej do danego serwera.

## <a name="limitations"></a>Ograniczenia

Usługi Azure SQL Database funkcja reguł sieci wirtualnej ma następujące ograniczenia:

- Aplikacja sieci Web mogą być mapowane na prywatny adres IP w sieci wirtualnej/podsieci. Nawet wtedy, gdy punkty końcowe usługi są włączone w danej sieci wirtualnej/podsieci, połączeń z serwerem z aplikacji sieci Web będzie źródło IP publicznej platformy Azure nie źródło sieci wirtualnej/podsieci. Aby włączyć łączność z aplikacji sieci Web na serwerze, który ma reguły zapory sieci wirtualnej, musisz mieć **Zezwalaj na wszystkie usługi systemu Azure** na serwerze.

- W zaporze dla usługi SQL Database każda reguła sieci wirtualnej odwołuje się do podsieci. Te odwołania podsieci muszą być hostowane w tym samym regionie geograficznym, który jest hostem bazy danych SQL.

- Każdy serwer usługi Azure SQL Database może mieć maksymalnie 128 wpisy listy ACL dla dowolnego danej sieci wirtualnej.

- Reguły sieci wirtualnej mają zastosowanie tylko do sieci wirtualnej usługi Azure Resource Manager. i nie [klasycznego modelu wdrażania] [ arm-deployment-model-568f] sieci.

- NA włączenie punktów końcowych usługi sieci wirtualnej do usługi Azure SQL Database umożliwia również punktami końcowymi usługi baza danych MySQL i PostgreSQL Azure. Jednak za pomocą włączone punkty końcowe prób nawiązania połączenia z punktów końcowych do wystąpień bazy danych MySQL lub PostgreSQL zakończy się niepowodzeniem.
    - Główną przyczynę wysokiej skuteczności to, że baza danych MySQL i PostgreSQL obecnie obsługuje ACLing.

- Na zaporze zakresów adresów IP są stosowane do następujących elementów sieci, ale nie obsługują reguł sieci wirtualnej:
    - [Wirtualnej sieci prywatnej (VPN) lokacja-lokacja (S2S)][vpn-gateway-indexmd-608y]
    - W środowisku lokalnym za pośrednictwem [usługi ExpressRoute][expressroute-indexmd-744v]

#### <a name="considerations-when-using-service-endpoints"></a>Uwagi dotyczące korzystania z punktów końcowych usługi
W przypadku używania punktów końcowych usługi dla usługi Azure SQL Database, należy przejrzeć następujące zagadnienia:

- **Wymagana jest ruchu wychodzącego do publicznych adresów IP usługi Azure SQL Database**: grupy zabezpieczeń sieci (NSG) muszą być otwarte do adresów IP bazy danych SQL Azure, aby zezwolić na połączenie. Można to zrobić za pomocą sieciowej grupy zabezpieczeń [tagi usługi](../virtual-network/security-overview.md#service-tags) usługi Azure SQL Database.

#### <a name="expressroute"></a>ExpressRoute

Jeśli sieć jest połączony z siecią platformy Azure za pośrednictwem [ExpressRoute][expressroute-indexmd-744v], każdy obwód jest skonfigurowany przy użyciu dwóch publicznych adresów IP w Microsoft Edge. Dwa adresy IP są używane połączyć się programem Microsoft Services, takich jak do usługi Azure Storage, korzystając z publicznej komunikacji równorzędnej Azure.

Aby zezwalać na komunikację z obwodu do usługi Azure SQL Database, należy utworzyć zasady sieci IP publiczne adresy IP obwodów usługi. Aby można było znaleźć publiczne adresy IP obwodów usługi ExpressRoute, należy otworzyć bilet pomocy technicznej przy użyciu usługi ExpressRoute za pomocą witryny Azure portal.


<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-removing-allow-all-azure-services"></a>Wpływ usunięcia "Zezwalaj na wszystkie usługi platformy Azure"

Wielu użytkowników ma zostać usunięty **Zezwalaj na wszystkie usługi platformy Azure** ze swoich serwerów SQL platformy Azure i zastąp go reguła zapory sieci wirtualnej.
Jednak usunięcie to ma wpływ na następujące funkcje SQLDB platformy Azure:

#### <a name="import-export-service"></a>Usługa eksportu importu
SQLDB importu eksportowania usługa działa na maszynach wirtualnych na platformie Azure. Te maszyny wirtualne nie znajdują się w sieci wirtualnej i dlatego Uzyskaj adres IP platformy Azure podczas nawiązywania połączenia z bazą danych. Na temat usuwania **Zezwalaj na wszystkie usługi platformy Azure** te maszyny wirtualne nie będą mogli korzystać z bazy danych.
Można obejść ten problem. Uruchom importowanie pliku BACPAC lub wyeksportuj go bezpośrednio w kodzie za pomocą interfejsu API DACFx. Upewnij się, że został wdrożony na maszynie wirtualnej, który znajduje się w podsieci sieci wirtualnej dla którego ustawiono regułę zapory.

#### <a name="sql-database-query-editor"></a>Edytor zapytań bazy danych SQL
Edytor zapytań bazy danych SQL Azure jest wdrażany na maszynach wirtualnych na platformie Azure. Te maszyny wirtualne nie są dostępne w sieci wirtualnej. W związku z tym maszyn wirtualnych Uzyskaj adres IP platformy Azure podczas nawiązywania połączenia z bazą danych. Na temat usuwania **Zezwalaj na wszystkie usługi platformy Azure**te maszyny wirtualne nie będą mogli korzystać z bazy danych.

#### <a name="table-auditing"></a>Inspekcji tabel
Obecnie istnieją dwa sposoby włączania inspekcji usługi SQL Database. Inspekcja tabeli kończy się niepowodzeniem, po włączeniu punktów końcowych usługi na serwerze SQL platformy Azure. Środki zaradcze w tym miejscu jest przenoszenie z inspekcji obiektów Blob.

#### <a name="impact-on-data-sync"></a>Wpływ na synchronizowanie danych
Usługa Azure SQLDB ma funkcji Data Sync, który nawiązuje połączenie z baz danych przy użyciu adresów IP usługi Azure. Korzystając z punktów końcowych usługi, prawdopodobnie spowoduje wyłączenie **Zezwalaj na wszystkie usługi platformy Azure** dostęp do serwera logicznego. Spowoduje to przerwanie funkcji Data Sync.

## <a name="impact-of-using-vnet-service-endpoints-with-azure-storage"></a>Wpływ za pomocą punktów końcowych usługi sieci wirtualnej z usługą Azure storage

Usługa Azure Storage została zaimplementowana w tej samej funkcji, która pozwala ograniczyć łączność z kontem magazynu.
Jeśli zdecydujesz się używać tej funkcji przy użyciu konta magazynu, który jest używany przez program SQL Server Azure, możesz napotkać problemy. Obok listy i będzie dyskusję na temat funkcji SQLDB platformy Azure, które ma to wpływ.

#### <a name="azure-sqldw-polybase"></a>Azure SQLDW PolyBase
Program PolyBase jest najczęściej używany do ładowania danych w usłudze Azure SQLDW z konta magazynu. Jeśli konto magazynu, które są ładowane dane z ogranicza dostęp tylko do zestawu z podsieci sieci wirtualnej, spowoduje przerwanie połączenia z poziomu programu PolyBase do konta. Ma ograniczenia dla tej i pomocy technicznej firmy Microsoft może skontaktować się, aby uzyskać więcej informacji.

#### <a name="azure-sqldb-blob-auditing"></a>Obiekt Blob platformy Azure SQLDB inspekcji
Inspekcja obiektów blob wypchnięcia dzienników inspekcji na koncie magazynu. Jeśli to konto magazynu używa funkcji punktów końcowych usługi sieci wirtualnej spowoduje przerwanie łączności z SQLDB platformy Azure na koncie magazynu.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Dodawanie reguły zapory sieci Wirtualnej do serwera bez włączania na sieć Wirtualną punktów końcowych usługi

Dawno temu zanim ta funkcja została ulepszona, były wymagane było włączyć punkty końcowe usługi sieci wirtualnej przed regułę sieci wirtualnej na żywo można zaimplementować w zaporze. Punkty końcowe związane z danej podsieci sieci wirtualnej do usługi Azure SQL Database. Ale teraz od stycznia 2018 r. można ominąć, to wymaganie, ustawiając **IgnoreMissingServiceEndpoint** flagi.

Tylko ustawienie reguły zapory nie pomaga zabezpieczyć serwer. Należy również włączyć punkty końcowe usługi sieci wirtualnej dla zabezpieczeń, które zostały wprowadzone. Po włączeniu punktów końcowych usługi w podsieci sieci wirtualnej środowisk przestój dopiero po zakończeniu jego przejścia z wyłączone na. Jest to szczególnie istotne w kontekście dużych sieciach wirtualnych. Możesz użyć **IgnoreMissingServiceEndpoint** flagę, aby zredukować lub całkowicie wyeliminować przestój podczas przejścia.

Możesz ustawić **IgnoreMissingServiceEndpoint** flagi przy użyciu programu PowerShell. Aby uzyskać więcej informacji, zobacz [programu PowerShell, aby utworzyć regułę i punkt końcowy usługi sieci wirtualnej dla usługi Azure SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].


## <a name="errors-40914-and-40615"></a>Błędy 40914 i 40615

Błąd połączenia 40914 odnosi się do *reguł sieci wirtualnej*, jak określono w okienku zapory w witrynie Azure portal. Błąd 40615 jest podobny, z wyjątkiem on odnosi się do *reguły dotyczące adresów IP* na zaporze.

#### <a name="error-40914"></a>Błąd 40914

*Tekst komunikatu:* nie można otworzyć serwera "*[nazwa_serwera]*" żądanego podczas logowania. Klient nie może uzyskać dostęp do serwera.

*Opis błędu:* klient znajduje się w podsieci, która ma punkty końcowe serwera w sieci wirtualnej. Ale serwera Azure SQL Database nie ma sieci wirtualnej reguły przyznająca podsieci prawo do komunikowania się z usługą SQL Database.

*Błąd podczas rozpoznawania:* okienku na zapory w witrynie Azure Portal, użyj reguł sieci wirtualnej kontrolę [Dodaj regułę sieci wirtualnej](#anchor-how-to-by-using-firewall-portal-59j) dla podsieci.

#### <a name="error-40615"></a>Błąd 40615

*Tekst komunikatu:* nie można otworzyć serwera "{0}" żądanego podczas logowania. Klient o adresie IP{1}"nie może uzyskać dostępu do serwera.

*Opis błędu:* klient próbuje nawiązać połączenie z adresu IP, który nie ma uprawnień do łączenia się z serwerem usługi Azure SQL Database. Zapory serwera nie ma adresu IP adres reguły, klient będzie mógł komunikować się z danego adresu IP do bazy danych SQL.

*Błąd podczas rozpoznawania:* wprowadź adres IP klienta jako reguła adresów IP. W tym za pomocą okienka zapory w witrynie Azure portal.


Lista komunikaty o błędach bazy danych SQL jest udokumentowany [tutaj][sql-database-develop-error-messages-419g].





<a name="anchor-how-to-by-using-firewall-portal-59j" />

## <a name="portal-can-create-a-virtual-network-rule"></a>Portal można utworzyć regułę sieci wirtualnej

W tej sekcji przedstawiono, jak można użyć [witryny Azure portal] [ http-azure-portal-link-ref-477t] utworzyć *reguły sieci wirtualnej* w usłudze Azure SQL Database. Reguła zawiera informacje dla usługi SQL Database, aby akceptował komunikację z określonej podsieci, które zostały otagowane jako *punkt końcowy usługi sieci wirtualnej*.

> [!NOTE]
> Jeśli planujesz dodać punkt końcowy usługi do reguł zapory sieci wirtualnej serwera bazy danych SQL Azure, najpierw upewnij się tej usługi, które punkty końcowe są włączone dla tej podsieci.
>
> Jeśli punkty końcowe usługi nie są włączone dla podsieci, portalu pyta, aby je włączyć. Kliknij przycisk **Włącz** przycisk na tym samym bloku, w którym można dodać reguły.

#### <a name="powershell-alternative"></a>Zamiast programu PowerShell

Skrypt programu PowerShell można również tworzyć reguły sieci wirtualnej. Polecenia cmdlet kluczowym **New-AzureRmSqlServerVirtualNetworkRule**. Jeśli chcesz się dowiedzieć, zobacz [programu PowerShell, aby utworzyć regułę i punkt końcowy usługi sieci wirtualnej dla usługi Azure SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

#### <a name="rest-api-alternative"></a>Alternatywą dla interfejsu API REST

Wewnętrznie poleceń cmdlet programu PowerShell dla sieci wirtualnej SQL akcji wywoływać interfejsy API REST. Można bezpośrednio wywoływać interfejsy API REST.

- [Reguły sieci wirtualnej: działania][rest-api-virtual-network-rules-operations-862r]

#### <a name="prerequisites"></a>Wymagania wstępne

Musi już mieć podsieci, który jest oznaczony za pomocą określonego punktu końcowego usługi sieci wirtualnej *nazwy typu* dotyczą usługi Azure SQL Database.

- Nazwa typu odpowiedniego punktu końcowego jest **Microsoft.Sql**.
- Jeśli podsieć nie może być oznakowane za pomocą nazwy typu, zobacz [Sprawdź podsieci jest punktem końcowym][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200" />

### <a name="azure-portal-steps"></a>Kroki w portalu Azure

1. Zaloguj się w witrynie [Azure Portal][http-azure-portal-link-ref-477t].

2. Następnie przejdź w portalu w celu **serwerów SQL** &gt; **Zapora / sieci wirtualnej**.

3. Ustaw **zezwolić na dostęp do usług platformy Azure** kontroli OFF.

    > [!IMPORTANT]
    > Jeśli pozostawisz formant ustawiony na wartość ON, serwera usługi Azure SQL Database akceptuje komunikację z dowolnej podsieci. Pozostawienie formant ustawiony na wartość ON, może być nadmierne dostępu z punktu widzenia zabezpieczeń. Funkcja punktu końcowego usługi Microsoft Azure Virtual Network, w połączeniu z funkcją reguły sieci wirtualnej bazy danych SQL, razem może zmniejszyć obszar powierzchni zabezpieczeń.

4. Kliknij przycisk **+ Dodaj istniejącą** kontrolować w **sieci wirtualne** sekcji.

    ![Kliknij przycisk Dodaj, istniejące (podsieci punkt końcowy, zgodnie z zasadą SQL).][image-portal-firewall-vnet-add-existing-10-png]

5. W nowym **tworzenia/aktualizacji** okienku wypełnienie w kontrolkach z nazwami zasobów platformy Azure.

    > [!TIP]
    > Musi zawierać prawidłowy **prefiksu adresu** dla podsieci. Wartość można znaleźć w portalu.
    > Przejdź **wszystkie zasoby** &gt; **wszystkich typów** &gt; **sieci wirtualne**. Filtr wyświetla sieci wirtualnych. Kliknij sieć wirtualną, a następnie kliknij przycisk **podsieci**. **Zakres adresów** kolumna ma prefiks adresu, potrzebujesz.

    ![Wypełnij pola dla nowej reguły.][image-portal-firewall-create-update-vnet-rule-20-png]

6. Kliknij przycisk **OK** przycisk w dolnej części okienka.

7.  W okienku zapory, zobacz wynikowy reguły sieci wirtualnej.

    ![W okienku zapory, zobacz nową regułę.][image-portal-firewall-vnet-result-rule-30-png]


> [!NOTE]
> Następujące stany lub stanów dotyczą zasady:
> - **Gotowe:** wskazuje, że operacja, która zainicjowano zakończyła się pomyślnie.
> - **Nie powiodła się:** wskazuje, że operacja, która zainicjowano zakończyła się niepowodzeniem.
> - **Usunięto:** tylko ma zastosowanie do operacji usuwania i wskazuje, czy reguła została usunięta i nie ma już zastosowania.
> - **W toku:** wskazuje, że operacja jest w toku. Stary reguła ma zastosowanie, gdy operacja jest w tym stanie.


<a name="anchor-how-to-links-60h" />

## <a name="related-articles"></a>Pokrewne artykuły:

- [Punkty końcowe usługi sieci wirtualnej platformy Azure][vm-virtual-network-service-endpoints-overview-649d]
- [Reguły zapory poziomu serwera i na poziomie bazy danych Azure SQL Database][sql-db-firewall-rules-config-715d]

Funkcja reguły sieci wirtualnej dla usługi Azure SQL Database stały się dostępne w późnym września 2017 r.

## <a name="next-steps"></a>Kolejne kroki

- [Tworzenie punktu końcowego usługi sieci wirtualnej, a następnie reguły sieci wirtualnej dla usługi Azure SQL Database za pomocą programu PowerShell.][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Reguł sieci wirtualnej: Operacje] [ rest-api-virtual-network-rules-operations-862r] za pomocą interfejsów API REST



<!-- Link references, to images. -->

[image-portal-firewall-vnet-add-existing-10-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png

[image-portal-firewall-create-update-vnet-rule-20-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png

[image-portal-firewall-vnet-result-rule-30-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png



<!-- Link references, to text, Within this same Github repo. -->

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



<!-- Link references, to text, Outside this Github repo (HTTP). -->

[http-azure-portal-link-ref-477t]: https://portal.azure.com/

[rest-api-virtual-network-rules-operations-862r]: https://docs.microsoft.com/rest/api/sql/virtualnetworkrules



<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON

- Azure CLI

- ARM templates
-->
