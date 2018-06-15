---
title: Skonfiguruj odbiornik zewnętrzny dla zawsze włączonych grup dostępności | Dokumentacja firmy Microsoft
description: Ten samouczek przedstawia kroki tworzenia zawsze na odbiornik grupy dostępności na platformie Azure, który jest dostępny zewnętrznie przy użyciu adresu publicznego wirtualnego adresu IP usługi skojarzonej chmury.
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
ms.openlocfilehash: 38bb77c6b1d083bd6b52b785a991f24965d00e12
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
ms.locfileid: "29398866"
---
# <a name="configure-an-external-listener-for-always-on-availability-groups-in-azure"></a>Skonfiguruj odbiornik zewnętrzny dla zawsze włączonych grup dostępności w systemie Azure
> [!div class="op_single_selector"]
> * [Odbiornik wewnętrzny](../classic/ps-sql-int-listener.md)
> * [Odbiornik zewnętrzny](../classic/ps-sql-ext-listener.md)
> 
> 

W tym temacie przedstawiono sposób konfigurowania odbiornika dla zawsze włączonej grupy dostępności, który jest dostępny zewnętrznie w Internecie. Jest to możliwe, kojarząc usługi w chmurze **publiczny wirtualnego adresu IP (VIP)** adres przy użyciu odbiornika.

> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../../../azure-resource-manager/resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.

Grupy dostępności mogą zawierać replik, które są lokalne, Azure, lub obejmować zarówno lokalnych, jak i Azure dla konfiguracji hybrydowego. Azure replik może się znajdować w tym samym regionie lub w wielu regionach, przy użyciu wielu sieci wirtualnych (sieci wirtualne). Poniższe kroki założono, że masz już [skonfigurowane grupy dostępności](../classic/portal-sql-alwayson-availability-groups.md) , ale nie skonfigurowano odbiornik.

## <a name="guidelines-and-limitations-for-external-listeners"></a>Zasady i ograniczenia dotyczące odbiorników zewnętrznych
Należy zwrócić uwagę poniższe wskazówki dotyczące odbiornika grupy dostępności na platformie Azure, w przypadku wdrażania za pomocą usługi na siła adresu VIP chmury:

* Odbiornik grupy dostępności jest obsługiwana w systemie Windows Server 2008 R2, Windows Server 2012 i Windows Server 2012 R2.
* Aplikacja kliencka musi znajdować się na innej usługi chmury niż ten, który zawiera grupy dostępności maszyn wirtualnych. W tej samej usłudze w chmurze Azure nie obsługuje bezpośredniego zwrotu serwera z klienta i serwera.
* Domyślnie kroki opisane w tym artykule pokazano, jak skonfigurować jeden odbiornik, aby używały adresu wirtualnego adresu IP (VIP) usługi chmury. Istnieje możliwość zarezerwować i utworzyć wiele adresów VIP dla usługi w chmurze. Dzięki temu można wykonaj kroki w tym artykule, aby utworzyć wiele odbiorników, które są skojarzone z inną adresu VIP. Aby uzyskać informacje na temat tworzenia wielu adresów VIP, zobacz [wieloma wirtualnymi adresami IP dla danej usługi chmury](../../../load-balancer/load-balancer-multivip.md).
* W przypadku tworzenia odbiornika dla środowiska hybrydowego, w sieci lokalnej musi mieć łączność z Internetem publicznego, oprócz sieć VPN lokacja lokacja z sieci wirtualnej platformy Azure. W przypadku podsieci platformy Azure, odbiornika grupy dostępności jest dostępny tylko za pomocą publicznego adresu IP usługi odpowiedniej chmury.
* Utwórz odbiornik zewnętrzny w samej usłudze w chmurze, gdzie masz również odbiornik wewnętrznej przy użyciu wewnętrznego modułu równoważenia obciążenia (ILB) nie jest obsługiwane.

## <a name="determine-the-accessibility-of-the-listener"></a>Ustalić dostępność odbiornika
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Ten artykuł skupia się na utworzenie odbiornika, który używa **równoważenia obciążenia zewnętrznych**. Odbiornik, który jest oznaczony jako prywatny sieci wirtualnej, zobacz wersję tego artykułu, który zawiera kroki tworzenia [odbiornik o ILB](../classic/ps-sql-int-listener.md)

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Tworzyć równoważeniem obciążenia punkty końcowe maszyny Wirtualnej z serwerem bezpośredniego zwrotu
Równoważenie obciążenia zewnętrznych używa wirtualnej publiczny adres wirtualnego adresu IP usługi w chmurze, który jest hostem maszyny wirtualne. Dlatego nie należy do utworzenia lub skonfigurowania funkcji równoważenia obciążenia w tym przypadku.

Punkt końcowy z równoważeniem obciążenia należy utworzyć dla każdej maszyny Wirtualnej obsługuje replikę Azure. Jeśli masz repliki w wielu regionach, każdej repliki dla tego regionu muszą być w tej samej usługi w chmurze w tej samej sieci wirtualnej. Repliki tworzenia grupy dostępności, obejmujące wiele regionów platformy Azure wymaga konfigurowania wielu sieci wirtualnych. Aby uzyskać więcej informacji na temat konfigurowania krzyżowego łączność sieci wirtualnej, zobacz [Konfigurowanie sieci wirtualnej do sieci wirtualnej łączności](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. W portalu Azure przejdź do każdej maszyny Wirtualnej obsługuje replikę i wyświetlić szczegóły.
2. Kliknij przycisk **punkty końcowe** kartę dla poszczególnych maszyn wirtualnych.
3. Sprawdź, czy **nazwa** i **Port publiczny** nasłuchującego punktu końcowego, którego chcesz użyć nie jest już używany. W poniższym przykładzie nazwa to "MyEndpoint", a numer portu to "1433".
4. Na kliencie lokalnym, Pobierz i zainstaluj [modułu programu PowerShell najnowsze](https://azure.microsoft.com/downloads/).
5. Uruchom **programu Azure PowerShell**. Nowej sesji programu PowerShell jest otwarty z Azure załadowanych modułów administracyjnych.
6. Run **Get-AzurePublishSettingsFile**. To polecenie cmdlet kieruje do przeglądarki, aby pobrać plik ustawień publikowania do katalogu lokalnego. Może pojawić się prośba o podanie poświadczeń logowania dla subskrypcji platformy Azure.
7. Uruchom **AzurePublishSettingsFile importu** polecenia ze ścieżką pobrany plik ustawień publikowania:
   
        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>
   
    Po zaimportowaniu ustawień publikowania, można zarządzać subskrypcją platformy Azure w sesji programu PowerShell.
    
1. Skopiuj poniższy skrypt programu PowerShell w edytorze tekstu i ustaw wartości zmiennych do potrzeb środowiska (wartości domyślne zostały dołączone dla niektórych parametrów). Należy pamiętać, że jeśli grupy dostępności obejmuje regiony platformy Azure, należy uruchomić skrypt raz w każdym centrum danych do usługi w chmurze i węzły, które znajdują się w tym centrum danych.
   
        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
   
        # Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
        }

2. Po ustawieniu zmienne, skopiuj skrypt w edytorze tekstu do sesji programu Azure PowerShell go uruchomić. Jeśli nadal wyświetlany jest monit >>, wpisz wprowadź ponownie, aby upewnić się, uruchomienie skryptu.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Sprawdź, czy KB2854082 jest zainstalowana w razie potrzeby
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Otworzyć porty zapory w węzłach grupy dostępności
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Tworzenie odbiornika grupy dostępności

Tworzenie odbiornika grupy dostępności w dwóch krokach. Najpierw należy utworzyć zasobu klastra punkt dostępu klienta i skonfigurować zależności. Po drugie Skonfiguruj zasoby klastra przy użyciu programu PowerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Utwórz punkt dostępu klienta i skonfiguruj zależności klastra
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Konfigurowanie zasobów klastra w programie PowerShell
1. W programie Równoważenie obciążenia zewnętrznej, należy uzyskać publiczny wirtualnego adresu IP usługi w chmurze, która zawiera repliki. Zaloguj się do portalu Azure. Przejdź do usługi w chmurze zawiera grupy dostępności maszyny Wirtualnej. Otwórz **pulpitu nawigacyjnego** widoku.
2. Należy zwrócić uwagę na adres podany w **adres publiczny wirtualnego adresu IP (VIP)**. Jeśli rozwiązanie obejmuje sieci wirtualne, powtórz ten krok dla każdej usługi w chmurze zawiera maszyny Wirtualnej, który hostuje replikę.
3. Na jednym z maszyn wirtualnych Skopiuj poniższy skrypt programu PowerShell w edytorze tekstu i ustaw zmienne wartości, które wcześniej zapisany.
   
        # Define variables
        $ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service
   
        Import-Module FailoverClusters
   
        # If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.
   
        # Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
        # cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255
4. Po ustawiono zmienne, Otwórz okno programu Windows PowerShell z podwyższonym poziomem uprawnień, a następnie skopiuj skrypt w edytorze tekstowym oraz wkleić sesję programu Azure PowerShell, aby uruchomić go. Jeśli nadal wyświetlany jest monit >>, wpisz wprowadź ponownie, aby upewnić się, uruchomienie skryptu.
5. Powtórz te czynności na każdej maszynie Wirtualnej. Ten skrypt konfiguruje zasobu adres IP przy użyciu adresu IP usługi w chmurze i ustawia innych parametrów, takich jak port sondy. Jeśli adres IP zasobu w tryb online, ją następnie odpowiedzieć sondowanie na port sondy z równoważeniem obciążenia punktu końcowego utworzonej wcześniej w tym samouczku.

## <a name="bring-the-listener-online"></a>Przełącz odbiornika w trybie online
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Elementy monitowania
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>Testowanie odbiornika grupy dostępności (w ramach tej samej sieci wirtualnej)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="test-the-availability-group-listener-over-the-internet"></a>Testowanie odbiornika grupy dostępności (za pośrednictwem Internetu)
Aby uzyskać dostęp do odbiornika z spoza sieci wirtualnej, należy używać równoważenia obciążenia zewnętrznego/public, (opisanej w tym temacie) zamiast ILB, które jest dostępne tylko w ramach tej samej sieci wirtualnej. W parametrach połączenia należy określić nazwę usługi chmury. Na przykład, jeśli masz usługi w chmurze o nazwie *mycloudservice*, instrukcja sqlcmd będzie następujące:

    sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

Inaczej niż w poprzednim przykładzie uwierzytelniania SQL należy użyć, ponieważ obiekt wywołujący nie może używać uwierzytelniania systemu windows za pośrednictwem Internetu. Aby uzyskać więcej informacji, zobacz [zawsze włączonej grupy dostępności w maszynie Wirtualnej platformy Azure: scenariusze łączności klienta](http://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx). Gdy przy użyciu uwierzytelniania SQL, upewnij się, że w obu replikach tworzenia tej samej nazwy logowania. Aby uzyskać więcej informacji dotyczących rozwiązywania problemów z logowania z użyciem grup dostępności, zobacz [sposób mapowania nazwy logowania lub użyj zawartych użytkownika bazy danych SQL, aby podłączyć się do innych replik i mapowanie do baz danych dostępności](http://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx).

Jeśli replik zawsze na znajdują się w różnych podsieciach, klienci muszą określić **MultisubnetFailover = True** w parametrach połączenia. W efekcie prób połączenia równoległego replik w różnych podsieciach. Należy pamiętać, że taki scenariusz obejmuje między region wdrożenia zawsze włączonej grupy dostępności.

## <a name="next-steps"></a>Kolejne kroki
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]

