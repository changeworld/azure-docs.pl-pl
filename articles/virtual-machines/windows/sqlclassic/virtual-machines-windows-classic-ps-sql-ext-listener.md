---
title: Konfigurowanie odbiornika zewnętrznego dla zawsze włączonych grup dostępności | Dokumentacja firmy Microsoft
description: Ten samouczek przeprowadzi Cię przez kroki tworzenia zawsze na odbiornik grupy dostępności na platformie Azure jest dostępny zewnętrznie publiczny wirtualny adres IP usługi w chmurze skojarzone.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a2453032-94ab-4775-b976-c74d24716728
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.openlocfilehash: 89623adbddce07cbc3c3ead811f5174d108c9b0e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62101629"
---
# <a name="configure-an-external-listener-for-always-on-availability-groups-in-azure"></a>Konfigurowanie odbiornika zewnętrznego dla zawsze włączonych grup dostępności na platformie Azure
> [!div class="op_single_selector"]
> * [Wewnętrznego odbiornika](../classic/ps-sql-int-listener.md)
> * [Zewnętrznego odbiornika](../classic/ps-sql-ext-listener.md)
> 
> 

W tym temacie opisano konfigurowanie odbiornika zawsze włączonej grupy dostępności, który jest dostępny zewnętrznie w Internecie. Jest to możliwe, kojarząc usługi w chmurze **publiczny wirtualnego adresu IP (VIP)** adres odbiornika.

> [!IMPORTANT] 
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [Usługi Resource Manager i Model Klasyczny](../../../azure-resource-manager/resource-manager-deployment-model.md). Ten artykuł dotyczy klasycznego modelu wdrażania. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.

Grupy dostępności mogą zawierać replik znajdujących się w środowisku lokalnym, Azure, lub obejmować zarówno lokalnych, jak i platformy Azure dla konfiguracji hybrydowych. Repliki systemu Azure mogą znajdować się w tym samym regionie lub w wielu regionach za pomocą wielu sieci wirtualnych (Vnet). W poniższych krokach przyjęto, że już [skonfigurowane grupy dostępności](../classic/portal-sql-alwayson-availability-groups.md) , ale nie został skonfigurowany odbiornik.

## <a name="guidelines-and-limitations-for-external-listeners"></a>Wytyczne i ograniczenia dotyczące odbiorników zewnętrznych
Należy zwrócić uwagę następujących wytycznych dotyczących odbiornika grupy dostępności na platformie Azure, w przypadku wdrażania przy użyciu usługi na publiczny adres VIP chmury:

* Odbiornik grupy dostępności jest obsługiwana w systemie Windows Server 2008 R2, Windows Server 2012 i Windows Server 2012 R2.
* Aplikacja kliencka musi znajdować się w usłudze w chmurze innej niż ta, która zawiera grupy dostępności maszyn wirtualnych. W tej samej usłudze w chmurze platformy Azure nie obsługuje bezpośredniego zwrotu z serwera z klientem i serwerem.
* Domyślnie w tym artykule opisano kroki do skonfigurowania jednego odbiornika do użycia adresu wirtualnego adresu IP (VIP) usługi w chmurze. Jednak jest możliwe, aby zarezerwować i tworzenie wielu adresów VIP dla usługi w chmurze. Dzięki temu wykonaj kroki w tym artykule, aby utworzyć wiele odbiorników, które są skojarzone z różnych adresów VIP. Aby uzyskać informacje na temat tworzenia wielu adresów VIP, zobacz [wielu adresów VIP dla usługi w chmurze](../../../load-balancer/load-balancer-multivip.md).
* W przypadku tworzenia odbiornika dla środowiska hybrydowego sieci lokalnej musi mieć łączność z publicznej sieci Internet, oprócz sieci VPN lokacja lokacja z siecią wirtualną platformy Azure. W podsieci platformy Azure odbiornika grupy dostępności jest dostępny tylko za pomocą publicznego adresu IP usługi odpowiedniej chmury.
* Utwórz odbiornik zewnętrzny w tej samej usłudze w chmurze, gdzie masz również wewnętrznego odbiornika przy użyciu wewnętrznego modułu równoważenia obciążenia (ILB) nie jest obsługiwane.

## <a name="determine-the-accessibility-of-the-listener"></a>Ustalić dostępność odbiornika
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Ten artykuł koncentruje się na temat tworzenia odbiornika, który używa **Równoważenie obciążenia zewnętrznych**. Odbiornik, który jest prywatny z siecią wirtualną, zobacz wersję tego artykułu, który zawiera instrukcje dotyczące konfigurowania [odbiornika z modułem równoważenia obciążenia](../classic/ps-sql-int-listener.md)

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Tworzenie ze zrównoważonym obciążeniem punktów końcowych maszyny Wirtualnej przy użyciu bezpośrednie serwera return
Równoważenie obciążenia zewnętrznych używa wirtualnego publiczny wirtualny adres IP usługi w chmurze, który jest hostem maszyny wirtualne. Więc nie trzeba tworzyć i konfigurować w tym przypadku modułu równoważenia obciążenia.

Dla każdej maszyny Wirtualnej, w którym replika platformy Azure, należy utworzyć punkt końcowy z równoważeniem obciążenia. Jeśli masz replik w wielu regionach w każdej repliki dla tego regionu musi być w tej samej usługi w chmurze w tej samej sieci wirtualnej. Repliki tworzenie grup dostępności, obejmujące wiele regionów platformy Azure wymagane jest skonfigurowanie wielu sieci wirtualnych. Aby uzyskać więcej informacji na temat konfigurowania wielu połączenie między sieciami, zobacz [Konfigurowanie sieci wirtualnej połączenie między sieciami wirtualnymi](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. W witrynie Azure portal przejdź do każdej maszyny Wirtualnej, w którym replika i wyświetlić szczegóły.
2. Kliknij przycisk **punktów końcowych** karty dla poszczególnych maszyn wirtualnych.
3. Upewnij się, że **nazwa** i **Port publiczny** odbiornika punktu końcowego, którego chcesz użyć nie jest już używany. W poniższym przykładzie nazwa to "MyEndpoint", a numer portu to "1433".
4. Na lokalnym kliencie, Pobierz i zainstaluj [najnowszy moduł programu PowerShell](https://azure.microsoft.com/downloads/).
5. Uruchom **programu Azure PowerShell**. Nowej sesji programu PowerShell jest otwierany przy użyciu administracyjne moduły platformy Azure, załadowane.
6. Run **Get-AzurePublishSettingsFile**. To polecenie cmdlet kieruje do przeglądarki, aby pobrać plik ustawień publikowania do katalogu lokalnego. Może być monit o poświadczenia logowania dla subskrypcji platformy Azure.
7. Uruchom **AzurePublishSettingsFile importu** polecenia przy użyciu ścieżki pliku ustawień publikowania, który został pobrany:
   
        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>
   
    Po zaimportowaniu ustawień publikowania można zarządzać subskrypcją platformy Azure, w sesji programu PowerShell.
    
1. Skopiuj poniższy skrypt programu PowerShell do edytora tekstów i ustaw wartości zmiennych w zależności od środowiska (wartości domyślne zostały przewidziane w niektórych parametrów). Należy pamiętać, że jeśli grupy dostępności obejmuje regiony platformy Azure, należy uruchomić skrypt raz w każdym centrum danych do usługi w chmurze i węzły, które znajdują się w tym centrum danych.
   
        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
   
        # Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
        }

2. Po ustawieniu zmiennych, skopiuj skrypt za pomocą edytora tekstu do sesji programu Azure PowerShell do jej uruchomienia. Jeśli nadal wyświetlany jest monit >>, wpisz wprowadź ponownie, aby upewnić się, że uruchomieniu skryptu.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Sprawdź, czy KB2854082 jest zainstalowana w razie potrzeby
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Otwórz porty zapory w węzły grupy dostępności
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Utwórz odbiornik grupy dostępności

Utwórz odbiornik grupy dostępności w dwóch krokach. Najpierw utwórz zasób klastra punktu dostępu klienta i skonfigurować zależności. Po drugie Skonfiguruj zasoby klastra przy użyciu programu PowerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Utwórz punkt dostępu klienta i skonfiguruj zależności klastra
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Konfigurowanie zasobów klastra w programie PowerShell
1. Równoważenie obciążenia zewnętrznych, należy uzyskać publiczny wirtualny adres IP usługi w chmurze, która zawiera repliki. Zaloguj się do witryny Azure portal. Przejdź do usługi w chmurze, która zawiera grupy dostępności maszyny Wirtualnej. Otwórz **pulpit nawigacyjny** widoku.
2. Zanotuj adres wyświetlany w obszarze **adres publiczny wirtualnego adresu IP (VIP)**. Jeśli rozwiązanie obejmuje sieci wirtualne, powtórz ten krok dla każdej usługi w chmurze, zawierający maszyny Wirtualnej, który obsługuje replikę.
3. Na jednym z maszyn wirtualnych Skopiuj poniższy skrypt programu PowerShell do edytora tekstu, a następnie ustaw zmienne do wartości, które możesz zauważyć, wcześniej.
   
        # Define variables
        $ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service
   
        Import-Module FailoverClusters
   
        # If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.
   
        # Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
        # cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255
4. Raz możesz ustawiono zmienne, Otwórz okno programu Windows PowerShell z podwyższonym poziomem uprawnień, a następnie skopiuj skrypt w edytorze tekstowym i wkleić do sesję programu Azure PowerShell, aby go uruchomić. Jeśli nadal wyświetlany jest monit >>, wpisz wprowadź ponownie, aby upewnić się, że uruchomieniu skryptu.
5. Powtórz te czynności na każdej maszynie Wirtualnej. Ten skrypt konfiguruje zasobu adresu IP przy użyciu adresu IP usługi w chmurze i ustawia inne parametry, takie jak port sondy. Jeśli zasób adresu IP w tryb online, następnie obsługiwanych sondowania na porcie sondowania z punktu końcowego ze zrównoważonym obciążeniem, utworzony wcześniej w tym samouczku.

## <a name="bring-the-listener-online"></a>Przenieś odbiornika w trybie online
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Elementy monitowania
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>Testowanie odbiornika grupy dostępności (w ramach tej samej sieci wirtualnej)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="test-the-availability-group-listener-over-the-internet"></a>Testowanie odbiornika grupy dostępności (za pośrednictwem Internetu)
Aby uzyskać dostęp do odbiornika z spoza sieci wirtualnej, należy używać równoważenia obciążenia zewnętrzne/public, (opisanych w tym temacie) zamiast wewnętrznego modułu równoważenia obciążenia, który jest dostępny tylko w ramach tej samej sieci wirtualnej. W parametrach połączenia można określić nazwa usługi w chmurze. Na przykład, jeśli masz usługi w chmurze o nazwie *mycloudservice*, instrukcja sqlcmd byłoby w następujący sposób:

    sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

W przeciwieństwie do poprzedniego przykładu należy użyć uwierzytelniania SQL, ponieważ obiekt wywołujący nie może używać uwierzytelniania systemu windows za pośrednictwem Internetu. Aby uzyskać więcej informacji, zobacz [zawsze włączonej grupy dostępności na maszynie Wirtualnej platformy Azure: Scenariusze łączności klienta](https://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx). Podczas korzystania z uwierzytelniania SQL, upewnij się, że w obu replikach tworzenia tych samych danych logowania. Aby uzyskać więcej informacji na temat rozwiązywania problemów logowania z użyciem grup dostępności, zobacz [sposób mapowania nazwy logowania lub użyj zawartych użytkownika bazy danych SQL, aby nawiązać połączenie z innymi replikami i mapowania baz danych dostępności](https://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx).

Jeśli zawsze włączonych replik znajdują się w różnych podsieciach, klienci muszą określić **MultisubnetFailover = True** w parametrach połączenia. Skutkiem próby nawiązania połączenia równoległego do replik w różnych podsieciach. Należy pamiętać, że ten scenariusz obejmuje wdrożenia zawsze włączonej grupy dostępności między regionami.

## <a name="next-steps"></a>Kolejne kroki
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]

