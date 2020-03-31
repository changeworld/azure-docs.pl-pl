---
title: Link prywatny
description: Omówienie funkcji Prywatny punkt końcowy
author: rohitnayakmsft
ms.author: rohitna
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.service: sql-database
ms.topic: overview
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: ab9c5c5c1134d2e09a790a788a3b7e55f807dd9b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78945369"
---
# <a name="private-link-for-azure-sql-database-and-data-warehouse"></a>Prywatne łącze dla bazy danych SQL azure i magazynu danych

Private Link umożliwia łączenie się z różnymi usługami PaaS na platformie Azure za pośrednictwem **prywatnego punktu końcowego.** Aby uzyskać listę usług PaaS obsługujących funkcje łącza prywatnego, przejdź do strony [Dokumentacja łącza prywatnego.](../private-link/index.yml) Prywatny punkt końcowy to prywatny adres IP w określonej sieci [wirtualnej](../virtual-network/virtual-networks-overview.md) i podsieci. 

> [!IMPORTANT]
> Ten artykuł dotyczy serwera SQL platformy Azure oraz baz danych SQL Database i baz danych SQL Data Warehouse utworzonych na serwerze SQL platformy Azure. Dla uproszczenia usługi SQL Database i SQL Data Warehouse są łącznie nazywane usługą SQL Database. Ten artykuł *nie* dotyczy wdrożenia **wystąpienia zarządzanego** w usłudze Azure SQL Database.

## <a name="data-exfiltration-prevention"></a>Zapobieganie eksfiltracji danych

Eksfiltracja danych w usłudze Azure SQL Database jest wtedy, gdy autoryzowany użytkownik, taki jak administrator bazy danych, może wyodrębnić dane z jednego systemu i przenieść go do innej lokalizacji lub systemu poza organizację. Na przykład użytkownik przenosi dane do konta magazynu należącego do innej firmy.

Należy wziąć pod uwagę scenariusz z użytkownikiem z uruchomionym programem SQL Server Management Studio (SSMS) wewnątrz maszyny Wirtualnej platformy Azure łączącej się z bazą danych SQL. Ta baza danych SQL znajduje się w centrum danych w zachodnie stany USA. W poniższym przykładzie pokazano, jak ograniczyć dostęp z publicznych punktów końcowych w bazie danych SQL przy użyciu kontroli dostępu do sieci.

1. Wyłącz cały ruch usługi platformy Azure do bazy danych SQL za pośrednictwem publicznego punktu końcowego, ustawiając opcję Zezwalaj na usługi platformy Azure na **WYŁ.** Upewnij się, że żadne adresy IP nie są dozwolone w regułach zapory na poziomie serwera i bazy danych. Aby uzyskać więcej informacji, zobacz [Azure SQL Database and Data Warehouse kontroli dostępu do sieci](sql-database-networkaccess-overview.md).
1. Zezwalaj tylko na ruch do bazy danych SQL przy użyciu prywatnego adresu IP maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz artykuły dotyczące reguł [punktu końcowego usługi](sql-database-vnet-service-endpoint-rule-overview.md) i [sieci wirtualnej](sql-database-firewall-configure.md).
1. Na maszynie Wirtualnej platformy Azure zawęź zakres połączenia wychodzącego przy użyciu [sieciowych grup zabezpieczeń (nsg)](../virtual-network/manage-network-security-group.md) i tagów usług w następujący sposób
    - Określ regułę nsg, aby zezwolić na ruch dla tagu usługi = SQL. WestUs — zezwalanie tylko na połączenie z bazą danych SQL w zachodnie stany USA
    - Określ regułę sieciowej grupy sieciowej (o **wyższym priorytecie),** aby odmówić ruchu dla tagu usługi = SQL - odmówiając połączeń z bazą danych SQL we wszystkich regionach

Po zakończeniu tej konfiguracji maszyna wirtualna platformy Azure może łączyć się tylko z bazami danych SQL w regionie Zachodnie stany USA. Jednak łączność nie jest ograniczona do jednej bazy danych SQL. Maszyna wirtualna nadal może łączyć się z dowolnymi bazami danych SQL w regionie Zachodnie stany USA, w tym z bazami danych, które nie są częścią subskrypcji. Firma Microsoft zmniejszyła zakres eksfiltracji danych w powyższym scenariuszu do określonego regionu, nie wyeliminowaliśmy go całkowicie.

Za pomocą łącza prywatnego klienci mogą teraz skonfigurować kontrolki dostępu do sieci, takie jak sieciowe sieciowe, aby ograniczyć dostęp do prywatnego punktu końcowego. Poszczególne zasoby usługi Azure PaaS są następnie mapowane do określonych prywatnych punktów końcowych. Złośliwy niejawny tester może uzyskać dostęp tylko do zamapowanych zasobów PaaS (na przykład bazy danych SQL) i do żadnego innego zasobu. 

## <a name="on-premises-connectivity-over-private-peering"></a>Łączność lokalna za prywatną komunikację równorzędną

Gdy klienci łączą się z publicznym punktem końcowym z komputerów lokalnych, ich adres IP musi zostać dodany do zapory opartej na protokãoła IP przy użyciu [reguły zapory na poziomie serwera](sql-database-server-level-firewall-rule.md). Ten model działa dobrze, umożliwiając dostęp do poszczególnych maszyn dla obciążeń deweloperskich lub testowych, ale jest trudny do zarządzania w środowisku produkcyjnym.

Za pomocą łącza prywatnego klienci mogą włączyć dostęp międzylokacyjnymi do prywatnego punktu końcowego przy użyciu [usługi ExpressRoute,](../expressroute/expressroute-introduction.md)prywatnej komunikacji równorzędnej lub tunelowania sieci VPN. Klienci mogą następnie wyłączyć cały dostęp za pośrednictwem publicznego punktu końcowego i nie używać zapory opartej na protokãoła IP, aby zezwolić na adresy IP.

## <a name="how-to-set-up-private-link-for-azure-sql-database"></a>Jak skonfigurować łącze prywatne dla bazy danych SQL usługi Azure 

### <a name="creation-process"></a>Proces tworzenia
Prywatne punkty końcowe można tworzyć za pomocą portalu, programu PowerShell lub interfejsu wiersza polecenia platformy Azure:
- [Portal](../private-link/create-private-endpoint-portal.md)
- [Powershell](../private-link/create-private-endpoint-powershell.md)
- [Cli](../private-link/create-private-endpoint-cli.md)

### <a name="approval-process"></a>Proces zatwierdzania
Gdy administrator sieci utworzy prywatny punkt końcowy (PE), administrator SQL może zarządzać połączeniem prywatnego punktu końcowego (PEC) z bazą danych SQL.

1. Przejdź do zasobu serwera SQL w witrynie Azure portal, zgodnie z instrukcjami pokazanymi na poniższym zrzucie ekranu

    - (1) Wybierz połączenia prywatnego punktu końcowego w lewym okienku
    - (2) Pokazuje listę wszystkich prywatnych połączeń punktów końcowych (PEC)
    - (3) Odpowiedni prywatny punkt końcowy ![(PE) utworzony zrzut ekranu wszystkich PEC][3]

1. Wybierz indywidualny PEC z listy, wybierając go.
![Zrzut ekranu wybrany PEC][6]

1. Administrator SQL może zatwierdzić lub odrzucić PEC i opcjonalnie dodać krótką odpowiedź tekstową.
![Zrzut ekranu przedstawiający zatwierdzenie pec][4]

1. Po zatwierdzeniu lub odrzuceniu lista będzie odzwierciedlać odpowiedni stan wraz z tekstem odpowiedzi.
![Zrzut ekranu przedstawiający wszystkie pecs po zatwierdzeniu][5]

## <a name="use-cases-of-private-link-for-azure-sql-database"></a>Przypadki użycia łącza prywatnego dla bazy danych SQL usługi Azure 

Klienci mogą łączyć się z prywatnym punktem końcowym z tej samej sieci wirtualnej, sieci wirtualnej w tym samym regionie lub za pośrednictwem połączenia sieci wirtualnej do sieci wirtualnej w różnych regionach. Ponadto klienci mogą łączyć się z lokalnymi przy użyciu usługi ExpressRoute, prywatnej komunikacji równorzędnej lub tunelowania sieci VPN. Poniżej znajduje się uproszczony diagram przedstawiający typowe przypadki użycia.

 ![Diagram opcji łączności][1]

## <a name="test-connectivity-to-sql-database-from-an-azure-vm-in-same-virtual-network-vnet"></a>Testowanie łączności z bazą danych SQL z maszyny Wirtualnej platformy Azure w tej samej sieci wirtualnej (vnet)

W tym scenariuszu załóżmy, że utworzono maszynę wirtualną platformy Azure (VM) z systemem Windows Server 2016. 

1. [Uruchom sesję pulpitu zdalnego (RDP) i połącz się z maszyną wirtualną](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine). 
1. Następnie można wykonać kilka podstawowych kontroli łączności, aby upewnić się, że maszyna wirtualna łączy się z bazą danych SQL za pośrednictwem prywatnego punktu końcowego przy użyciu następujących narzędzi:
    1. Protokół Telnet
    1. Psping ( psping )
    1. Nmap
    1. SQL Server Management Studio (SSMS)

### <a name="check-connectivity-using-telnet"></a>Sprawdź łączność za pomocą Telnet

[Telnet Client](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754293%28v%3dws.10%29) to funkcja systemu Windows, która może służyć do testowania łączności. W zależności od wersji systemu operacyjnego Windows może być konieczne jawne włączenie tej funkcji. 

Otwórz okno wiersza polecenia po zainstalowaniu telnetu. Uruchom polecenie Telnet i określ adres IP i prywatny punkt końcowy bazy danych SQL.

```
>telnet 10.1.1.5 1433
```

Gdy Telnet połączy się pomyślnie, w oknie polecenia pojawi się pusty ekran, taki jak poniższy obraz:

 ![Schemat telnetu][2]

### <a name="check-connectivity-using-psping"></a>Sprawdź łączność za pomocą psping

[Psping](/sysinternals/downloads/psping) może służyć w następujący sposób, aby sprawdzić, czy prywatne połączenie punktu końcowego (PEC) nasłuchuje połączeń na porcie 1433.

Uruchom psping w następujący sposób, zapewniając FQDN dla serwera bazy danych SQL i portu 1433:

```
>psping.exe mysqldbsrvr.database.windows.net:1433

PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2016 Mark Russinovich
Sysinternals - www.sysinternals.com

TCP connect to 10.6.1.4:1433:
5 iterations (warmup 1) ping test:
Connecting to 10.6.1.4:1433 (warmup): from 10.6.0.4:49953: 2.83ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49954: 1.26ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49955: 1.98ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49956: 1.43ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49958: 2.28ms
```

Dane wyjściowe pokazują, że psping może ping prywatny adres IP związany z PEC.

### <a name="check-connectivity-using-nmap"></a>Sprawdzanie łączności za pomocą Nmap

Nmap (Network Mapper) to bezpłatne i otwarte narzędzie służące do odnajdowania sieci i inspekcji zabezpieczeń. Aby uzyskać więcej informacji i https://nmap.orglink do pobrania, odwiedź . Za pomocą tego narzędzia można upewnić się, że prywatny punkt końcowy nasłuchuje połączeń na porcie 1433.

Uruchom Nmap w następujący sposób, podając zakres adresów podsieci, która obsługuje prywatny punkt końcowy.

```
>nmap -n -sP 10.1.1.0/24
...
...
Nmap scan report for 10.1.1.5
Host is up (0.00s latency).
Nmap done: 256 IP addresses (1 host up) scanned in 207.00 seconds
```

Wynik pokazuje, że jeden adres IP jest w górę; który odpowiada adresowi IP prywatnego punktu końcowego.


### <a name="check-connectivity-using-sql-server-management-studio-ssms"></a>Sprawdź łączność przy użyciu programu SQL Server Management Studio (SSMS)
> [!NOTE]
> Użyj **w pełni kwalifikowanej nazwy domeny (FQDN)** serwera w połączeniach dla swoich klientów. Wszelkie próby logowania podejmowane bezpośrednio na adres IP nie powiodą się. To zachowanie jest zgodne z projektem, ponieważ prywatny punkt końcowy kieruje ruch do bramy SQL w regionie, a nazwa FQDN musi zostać określona dla logowania, aby zakończyć się pomyślnie.

Wykonaj następujące kroki, aby połączyć się z bazą danych SQL za pomocą [usługi SSMS.](sql-database-connect-query-ssms.md) Po nawiązaniu połączenia z bazą danych SQL przy użyciu usługi SSMS sprawdź, czy łączysz się z prywatnego adresu IP maszyny Wirtualnej platformy Azure, uruchamiając następującą kwerendę:

````
select client_net_address from sys.dm_exec_connections 
where session_id=@@SPID
````

## <a name="limitations"></a>Ograniczenia 
Połączenia z prywatnym punktem końcowym obsługują serwer **proxy** tylko jako [zasady połączenia](sql-database-connectivity-architecture.md#connection-policy)


## <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Łączenie się z maszyny Wirtualnej platformy Azure w sieci wirtualnej (sieci wirtualnej) 

Konfigurowanie [komunikacji równorzędnej sieci wirtualnej](../virtual-network/tutorial-connect-virtual-networks-powershell.md) w celu ustanowienia łączności z bazą danych SQL z maszyny Wirtualnej platformy Azure w sieci wirtualnej równorzędnej.

## <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Łączenie się z maszyny wirtualnej platformy Azure w środowisku sieci wirtualnej do sieci wirtualnej

Skonfiguruj [połączenie bramy sieci VPN sieci wirtualnej z siecią wirtualną](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) w celu nawiązania łączności z bazą danych SQL z maszyny wirtualnej platformy Azure w innym regionie lub subskrypcji.

## <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Łączenie się ze środowiska lokalnego za pośrednictwem sieci VPN

Aby ustanowić łączność ze środowiska lokalnego z bazą danych SQL, wybierz i zaimplementuj jedną z opcji:
- [Połączenie z punktem lokacji](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
- [Połączenie sieci VPN typu lokacja-lokacja](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
- [Obwód usługi ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)


## <a name="connecting-from-an-azure-sql-data-warehouse-to-azure-storage-using-polybase"></a>Łączenie się z magazynu sql sql usługi Azure do usługi Azure Storage przy użyciu polybase

PolyBase jest często używany do ładowania danych do usługi Azure SQL Data Warehouse z kont usługi Azure Storage. Jeśli konto usługi Azure Storage, które ładujesz dane z limitów dostępu tylko do zestawu podsieci sieci wirtualnej za pośrednictwem prywatnych punktów końcowych, punktów końcowych usługi lub zapór opartych na protokãołach IP, łączność z PolyBase do konta zostanie przerwana. Aby włączyć zarówno scenariusze importowania i eksportowania Bazy Danych PolyBase za pomocą usługi Azure SQL Data Warehouse łączącej się z usługą Azure Storage, która jest zabezpieczona w sieci wirtualnej, wykonaj kroki podane [w tym miejscu.](sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) 



## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem zabezpieczeń bazy danych SQL azure, zobacz [Zabezpieczanie bazy danych](sql-database-security-overview.md)
- Aby zapoznać się z omówieniem łączności usługi Azure SQL Database, zobacz [Architektura łączności SQL platformy Azure](sql-database-connectivity-architecture.md)

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/pe-connect-overview.png
[2]: ./media/sql-database-get-started-portal/telnet-result.png
[3]: ./media/sql-database-get-started-portal/pec-list-before.png
[4]: ./media/sql-database-get-started-portal/pec-approve.png
[5]: ./media/sql-database-get-started-portal/pec-list-after.png
[6]: ./media/sql-database-get-started-portal/pec-select.png
