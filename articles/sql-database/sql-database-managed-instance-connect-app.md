---
title: Wystąpienie usługi Azure SQL Database Managed łączenie aplikacji | Dokumentacja firmy Microsoft
description: W tym artykule omówiono sposób łączenia aplikacji z wystąpienia zarządzanego Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
manager: craigg
ms.date: 11/09/2018
ms.openlocfilehash: ed9fbdd3e999cfd262ecbcf05a843c19cc969ed1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60701306"
---
# <a name="connect-your-application-to-azure-sql-database-managed-instance"></a>Połącz aplikację z wystąpieniem zarządzanym usługi Azure SQL Database

Obecnie masz wiele opcji do wyboru podczas podejmowania decyzji o sposób i miejsce hostowania aplikacji.

Istnieje możliwość hostowania aplikacji w chmurze, przy użyciu usługi Azure App Service lub niektórych opcji sieć wirtualną (VNet), zintegrowane platformy Azure, takich jak zestaw skalowania maszyn wirtualnych w usłudze Azure App Service Environment, maszyny wirtualnej. Można również wykonać chmura hybrydowa i zachować swoje aplikacje w środowisku lokalnym.

Niezależnie od wyboru, które zostały wprowadzone, możesz połączyć ją do wystąpienia zarządzanego.  

![Wysoka dostępność](./media/sql-database-managed-instance/application-deployment-topologies.png)

## <a name="connect-an-application-inside-the-same-vnet"></a>Łączenie aplikacji w tej samej sieci wirtualnej

Ten scenariusz jest najprostsza. Maszynami wirtualnymi w sieci wirtualnej można połączyć ze sobą bezpośrednio, nawet jeśli znajdują się w różnych podsieciach. Oznacza to, że wszystko, czego potrzebujesz do łączenia z aplikacji wewnątrz środowiska aplikacji platformy Azure lub maszyny wirtualnej jest odpowiednie ustawienie parametrów połączenia.  

## <a name="connect-an-application-inside-a-different-vnet"></a>Łączenie aplikacji wewnątrz innej sieci wirtualnej

Ten scenariusz jest nieco bardziej skomplikowane, ponieważ wystąpienie zarządzane jest prywatny adres IP we własnej sieci wirtualnej. Nawiązać połączenie, aplikacja musi mieć dostęp do sieci wirtualnej wdrożonym wystąpienia zarządzanego. Dlatego najpierw należy utworzyć połączenie między aplikacją i sieci wirtualnej wystąpienia zarządzanego. Sieci wirtualne nie muszą znajdować się w tej samej subskrypcji, aby zrealizować ten scenariusz.

Dostępne są dwie opcje łączenia sieci wirtualnych:

- [Komunikacja równorzędna sieci wirtualnych platformy Azure](../virtual-network/virtual-network-peering-overview.md)
- Brama sieci VPN typu sieć wirtualna-sieć wirtualna ([witryny Azure portal](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), [wiersza polecenia platformy Azure](../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md))

Ta opcja komunikacji równorzędnej jest preferowane jeden, ponieważ komunikację równorzędną używa sieci szkieletowej firmy Microsoft w związku z punktu widzenia łączności, nie ma żadnej różnicy zauważalnego opóźnienia między maszynami wirtualnymi w wirtualnej sieci równorzędnej i w tej samej sieci wirtualnej. Komunikacja równorzędna sieci wirtualnych jest ograniczona do sieci, w tym samym regionie.  

> [!IMPORTANT]
> Scenariusz komunikacji równorzędnej sieci wirtualnej dla wystąpienia zarządzanego jest ograniczona do sieci, w tym samym regionie, ze względu na [ograniczenia globalne wirtualne sieci równorzędne](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints).

## <a name="connect-an-on-premises-application"></a>Łączenie aplikacji w środowisku lokalnym

Wystąpienie zarządzane jest możliwy tylko za pośrednictwem prywatnego adresu IP. Aby uzyskać do niego dostęp ze środowiska lokalnego, musisz utworzyć połączenie lokacja-lokacja między aplikacją i sieci wirtualnej wystąpienia zarządzanego.

Jak połączyć lokalnych z siecią wirtualną platformy Azure dostępne są dwie opcje:

- Połączenie sieci VPN typu lokacja-lokacja ([witryny Azure portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [wiersza polecenia platformy Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md))
- [Usługa ExpressRoute](../expressroute/expressroute-introduction.md) połączenia  

Jeśli nie można ustanowić połączenia do wystąpienia zarządzanego środowiska lokalnego do platformy Azure połączenie zostało ustanowione pomyślnie, sprawdź, czy zapory ma otwarte połączeń wychodzących w SQL porcie 1433, a także 11000 12000 zakres portów dla przekierowania.

## <a name="connect-an-application-on-the-developers-box"></a>Łączenie aplikacji w usłudze box deweloperów

Wystąpienie zarządzane jest możliwy tylko za pośrednictwem prywatnego adresu IP tak aby można było uzyskać do niego dostęp z Twojego pola dla deweloperów, najpierw musisz utworzyć połączenie między usługami box usługi dla deweloperów i sieci wirtualnej wystąpienia zarządzanego. Aby to zrobić, należy skonfigurować połączenie punkt-lokacja z siecią wirtualną przy użyciu uwierzytelniania certyfikatu platformy Azure natywnych. Aby uzyskać więcej informacji, zobacz [Konfigurowanie połączenia punkt lokacja, nawiązywanie połączenia z bazą danych wystąpienia zarządzanego Azure SQL z na komputerze lokalnym](sql-database-managed-instance-configure-p2s.md).

## <a name="connect-from-on-premises-with-vnet-peering"></a>Łączenie ze środowiska lokalnego za pomocą komunikacji równorzędnej sieci wirtualnej

Inny scenariusz implementowany przez klientów jest zainstalowaną bramę sieci VPN w oddzielnej sieci wirtualnej i subskrypcję z jednego wystąpienia zarządzanego hostingu. Następnie równorzędnym połączeniu dwóch sieci wirtualnych. Następujący przykładowy diagram architektury pokazuje, jak można to zaimplementować.

![Komunikacja równorzędna sieci wirtualnych](./media/sql-database-managed-instance-connect-app/vnet-peering.png)

Po utworzeniu podstawowej infrastruktury, musisz zmodyfikować niektóre ustawienia, aby brama sieci VPN można zobaczyć adresy IP w sieci wirtualnej, który hostuje wystąpienie zarządzane. Aby to zrobić, należy wprowadzić następujące zmiany bardzo szczegółowych w obszarze **ustawienia komunikacji równorzędnej**.

1. W sieci wirtualnej, który jest hostem bramy sieci VPN, przejdź do **komunikacje równorzędne**, następnie do wystąpienia zarządzanego równorzędne połączenie sieci wirtualnej, a następnie kliknij przycisk **Zezwalaj na tranzyt przez bramę**.
2. W sieci wirtualnej, który hostuje wystąpienie zarządzane, przejdź do **komunikacje równorzędne**, następnie do bramy sieci VPN równorzędne połączenie sieci wirtualnej, a następnie kliknij przycisk **Użyj bram zdalnych**.

## <a name="connect-an-azure-app-service-hosted-application"></a>Łączenie aplikacji hostowanej usługi Azure App Service

Wystąpienia zarządzanego jest możliwy wyłącznie za pośrednictwem prywatnego adresu IP, więc w celu uzyskania dostępu do usługi Azure App Service musisz najpierw utworzyć połączenie między aplikacją i sieci wirtualnej wystąpienia zarządzanego. Zobacz [Integrowanie aplikacji z siecią wirtualną platformy Azure](../app-service/web-sites-integrate-with-vnet.md).  

Do rozwiązywania problemów, zobacz [aplikacji i rozwiązywanie problemów z sieciami wirtualnymi](../app-service/web-sites-integrate-with-vnet.md#troubleshooting). Jeśli nie można ustanowić połączenia, spróbuj [synchronizowanie konfiguracji sieci](sql-database-managed-instance-sync-network-configuration.md).

Przypadek specjalny nawiązywania połączenia z usługi Azure App Service do wystąpienia zarządzanego jest po użytkownik zintegrowane usługi Azure App Service, aby sieć połączona z zarządzanych sieci wirtualnej wystąpienia. Ten przypadek wymaga następującej konfiguracji do skonfigurowania:

- Zarządzane wystąpienia sieci wirtualnej nie może mieć bramy  
- Zarządzane wystąpienia sieci wirtualnej musi mieć ustawioną opcję bram zdalnych Użyj
- Skomunikowanej równorzędnie sieci wirtualnej musi mieć opcję tranzyt bramy Zezwalaj ustawioną

W tym scenariuszu przedstawiono na poniższym diagramie:

![Komunikacja równorzędna w zintegrowanej aplikacji](./media/sql-database-managed-instance/integrated-app-peering.png)

>[!NOTE]
>Funkcja integracji sieci wirtualnej nie jest zintegrowana z aplikacji z siecią wirtualną, która ma bramę usługi ExpressRoute. Nawet jeśli brama usługi ExpressRoute jest konfigurowane w trybie współistnienie Integracja sieci wirtualnej nie działa. Jeśli potrzebujesz dostępu do zasobów za pośrednictwem połączenia usługi ExpressRoute, można użyć w środowisku usługi App Service, która działa w sieci wirtualnej.

## <a name="troubleshooting-connectivity-issues"></a>Rozwiązywanie problemów z łącznością

Aby informacje o rozwiązywaniu problemów z połączeniem, sprawdź następujące kwestie:

- Jeśli nie można nawiązać połączenia z wystąpieniem zarządzanym z maszyny wirtualnej platformy Azure w ramach tej samej sieci wirtualnej, ale inną podsieć, sprawdź, czy ustawić w podsieci maszyny Wirtualnej, który może blokować dostęp do grupy zabezpieczeń sieci. Ponadto należy pamiętać, że trzeba otworzyć połączeń wychodzących na SQL porcie 1433, a także porty z zakresu od 11000 12000, ponieważ te są potrzebne do nawiązywania połączenia za pośrednictwem przekierowanie wewnątrz granic platformy Azure.
- Upewnij się, że propagacja protokołu BGP jest ustawiona na **włączone** dla tabeli tras skojarzonej z siecią wirtualną.
- Jeśli przy użyciu sieci VPN typu P2S, sprawdź konfigurację w witrynie Azure portal, aby zobaczyć, jeśli zostanie wyświetlony **ruchem przychodzącym/wychodzącym** liczb. Niezerowa Koniunkcja numery wskazują Azure routing ruchu ze środowiska lokalnego.

   ![numery ruchem przychodzącym/wychodzącym](./media/sql-database-managed-instance-connect-app/ingress-egress-numbers.png)

- Sprawdź, czy komputer kliencki, (który jest uruchomiony klient sieci VPN) ma wejść dla trasy dla wszystkich sieci wirtualnych, które muszą uzyskać dostęp do. Trasy są przechowywane w `%AppData%\ Roaming\Microsoft\Network\Connections\Cm\<GUID>\routes.txt`.

   ![route.txt](./media/sql-database-managed-instance-connect-app/route-txt.png)

   Jak pokazano na tej ilustracji, istnieją dwa wpisy dla każdej sieci wirtualnej związane i trzeci wpis dla punktu końcowego sieci VPN, który jest skonfigurowany w portalu.

   Innym sposobem na sprawdzenie trasy jest za pomocą następującego polecenia. Dane wyjściowe pokazują trasy do różnych podsieci:

   ```cmd
   C:\ >route print -4
   ===========================================================================
   Interface List
   14...54 ee 75 67 6b 39 ......Intel(R) Ethernet Connection (3) I218-LM
   57...........................rndatavnet
   18...94 65 9c 7d e5 ce ......Intel(R) Dual Band Wireless-AC 7265
   1...........................Software Loopback Interface 1
   Adapter===========================================================================

   IPv4 Route Table
   ===========================================================================
   Active Routes:
   Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0       10.83.72.1     10.83.74.112     35
         10.0.0.0    255.255.255.0         On-link       172.26.34.2     43
         10.4.0.0    255.255.255.0         On-link       172.26.34.2     43
   ===========================================================================
   Persistent Routes:
   None
   ```

- Jeśli za pomocą komunikacji równorzędnej sieci wirtualnej, upewnij się, czy wykonano instrukcje dotyczące ustawienie [Zezwalaj na tranzyt przez bramę i użyj bram zdalnych](#connect-from-on-premises-with-vnet-peering).

## <a name="required-versions-of-drivers-and-tools"></a>Wymagane wersje sterowników i narzędzi

Jeśli chcesz nawiązać połączenie z wystąpieniem zarządzanym, zaleca się następujące minimalne wersje narzędzia i sterowniki:

| Narzędzie/sterownika | Wersja |
| --- | --- |
|.NET Framework | 4.6.1 (lub platformy .NET Core) |
|Sterownik ODBC| v17 |
|Sterownik języka PHP| 5.2.0 |
|Sterownik JDBC| 6.4.0 |
|Sterownik node.js| 2.1.1 |
|Sterownik OLEDB| 18.0.2.0 |
|SSMS| 17.8.1 lub [wyższy](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) |

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać informacje na temat wystąpienia zarządzanego, zobacz [What is a Managed Instance (Co to jest wystąpienie zarządzane)](sql-database-managed-instance.md).
- Aby uzyskać samouczek omawiający Tworzenie nowego wystąpienia zarządzanego, zobacz [utworzysz wystąpienie zarządzane](sql-database-managed-instance-get-started.md).
