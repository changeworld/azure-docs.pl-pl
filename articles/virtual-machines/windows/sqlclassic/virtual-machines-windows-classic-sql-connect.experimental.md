---
title: Nawiązać połączenie z maszyną wirtualną programu SQL Server na platformie Azure (model klasyczny) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak połączyć się z programu SQL Server uruchomionego na maszynie wirtualnej na platformie Azure. Ten temat używa klasycznego modelu wdrażania. Scenariusze różnią się w zależności od konfiguracji sieci i lokalizacji klienta.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.assetid: 416948af-454f-4cfe-8fd2-7cf971cbd3e9
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mathoma
ms.reviewer: jroth
experimental_id: d51f3cc6-753b-4e
ms.openlocfilehash: 51694ca085e131150217ffb3fbac9830980108cb
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62108441"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure-classic-deployment"></a>Łączenie z maszyną wirtualną programu SQL Server na platformie Azure (wdrażanie klasyczne)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-connect.md)
> * [Wdrożenie klasyczne](../classic/sql-connect.md)
> 
> 

## <a name="overview"></a>Omówienie
W tym temacie opisano, jak połączyć się z wystąpieniem programu SQL Server uruchomiony na maszynie wirtualnej platformy Azure. Obejmuje ona niektóre [scenariuszy ogólna łączność](#connection-scenarios) , a następnie oferuje [szczegółową procedurę konfigurowania połączenia programu SQL Server w Maszynie wirtualnej platformy Azure](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

> [!IMPORTANT] 
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [Usługi Resource Manager i Model Klasyczny](../../../azure-resource-manager/resource-manager-deployment-model.md). Ten artykuł dotyczy klasycznego modelu wdrażania. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Jeśli używasz maszyn wirtualnych usługi Resource Manager, zobacz [Connect do maszyny wirtualnej SQL Server na platformie Azure przy użyciu usługi Resource Manager](../sql/virtual-machines-windows-sql-connect.md).

## <a name="connection-scenarios"></a>Scenariusze łączenia
Sposób, gdy klient nawiąże połączenie z SQL Server uruchomionym na maszynie wirtualnej różni się w zależności od lokalizacji klienta i Konfiguracja komputera/sieci. Scenariusze obejmują:

* [Połączenia z serwerem SQL w tej samej usłudze w chmurze](#connect-to-sql-server-in-the-same-cloud-service)
* [Połączenia z serwerem SQL w Internecie](#connect-to-sql-server-over-the-internet)
* [Połączenia z serwerem SQL w tej samej sieci wirtualnej](#connect-to-sql-server-in-the-same-virtual-network)

> [!NOTE]
> Przed nawiązaniem połączenia z dowolnym z tych metod, należy wykonać [kroki opisane w tym artykule, aby skonfigurować łączność](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).
> 
> 

### <a name="connect-to-sql-server-in-the-same-cloud-service"></a>Połączenia z serwerem SQL w tej samej usłudze w chmurze
Można utworzyć wiele maszyn wirtualnych w tej samej usłudze w chmurze. Aby dowiedzieć się, w tym scenariuszu maszyn wirtualnych, zobacz [jak połączyć maszyny wirtualne z siecią wirtualną lub chmurze usługą](/previous-versions/azure/virtual-machines/windows/classic/connect-vms-classic#connect-vms-in-a-standalone-cloud-service). W tym scenariuszu klient na jednej maszynie wirtualnej próbuje nawiązać połączenie z SQL Server uruchomionym na innej maszynie wirtualnej w tej samej usłudze w chmurze.

W tym scenariuszu można połączyć za pomocą maszyny Wirtualnej **nazwa** (także wyświetlane jako **nazwy komputera** lub **hostname** w portalu). Jest to nazwa, podane dla maszyny Wirtualnej podczas tworzenia. Na przykład, jeśli nazwę maszyny Wirtualnej SQL **mysqlvm**, klient maszyny Wirtualnej w tej samej usłudze w chmurze wystarczą następujące parametry połączenia do łączenia z:

    "Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

### <a name="connect-to-sql-server-over-the-internet"></a>Połączenia z serwerem SQL w Internecie
Jeśli chcesz nawiązać połączenie z aparatem bazy danych programu SQL Server z Internetu, należy utworzyć punkt końcowy maszyny wirtualnej dla przychodzącą komunikację protokołu TCP. Ten krok konfiguracji platformy Azure kieruje ruch przychodzący port TCP do portu TCP, który jest dostępny dla maszyny wirtualnej.

Aby połączyć się za pośrednictwem Internetu, należy użyć nazwy DNS maszyny Wirtualnej i numer portu punktu końcowego maszyny Wirtualnej (skonfigurowane w dalszej części tego artykułu). Aby znaleźć nazwę DNS, przejdź do witryny Azure portal i wybierz **maszyny wirtualne (klasyczne)**. Następnie wybierz maszynę wirtualną. **Nazwy DNS** jest wyświetlany w **Przegląd** sekcji.

Na przykład, należy wziąć pod uwagę klasycznej maszyny wirtualnej o nazwie **mysqlvm** o nazwie DNS **mysqlvm7777.cloudapp.net** i punktu końcowego maszyny Wirtualnej **57500**. Zakładając, że łączność prawidłowo skonfigurowane, następujące parametry połączenia można uzyskać dostęp do maszyny wirtualnej z dowolnego miejsca w Internecie:

    "Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Mimo że te parametry połączenia zapewnia łączność klientów za pośrednictwem Internetu, to nie oznacza, że każda osoba może połączyć się z programu SQL Server. Poza klienci mają prawidłową nazwę użytkownika i hasła. Poziom zabezpieczeń nie należy używać dobrze znanego portu 1433 dla punktu końcowego publicznego maszyny wirtualnej. A jeśli to możliwe, należy rozważyć dodanie listy ACL na punkcie końcowym, aby ograniczyć ruch tylko do klientów zezwolić. Aby uzyskać instrukcje na temat korzystania z listy ACL z punktami końcowymi, zobacz [listy ACL punktu końcowego zarządzania](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint).

> [!NOTE]
> Korzystając z tej techniki do komunikowania się z programem SQL Server, wszystkie dane wychodzące z centrów danych platformy Azure podlega postanowieniom normalny [ceny na wychodzące transfery danych](https://azure.microsoft.com/pricing/details/data-transfers/).
> 
> 

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>Połączenia z serwerem SQL w tej samej sieci wirtualnej
[Sieć wirtualna](../../../virtual-network/virtual-networks-overview.md) umożliwia dodatkowe scenariusze. Możesz połączyć maszyny wirtualne w tej samej sieci wirtualnej, nawet jeśli te maszyny wirtualne istnieją w różnych usługach w chmurze. I [sieci VPN typu lokacja lokacja](../../../vpn-gateway/vpn-gateway-site-to-site-create.md), możesz utworzyć architektury hybrydowej, która łączy maszyn wirtualnych z sieci lokalnych i maszyn.

Sieci wirtualne umożliwiają również dołączyć maszynach wirtualnych platformy Azure do domeny. Przyłączanie do domeny jest jedynym sposobem, aby korzystać z uwierzytelniania Windows z programem SQL Server. Inne scenariusze połączenia wymagają uwierzytelniania SQL przy użyciu nazwy użytkownika i hasła.

Jeśli użytkownik chce skonfigurować środowiska domeny i uwierzytelniania Windows, nie należy skonfigurować publiczny punkt końcowy lub uwierzytelnianie SQL i logowania. W tym scenariuszu należy nawiązać wystąpienia programu SQL Server, określając nazwę maszyny Wirtualnej programu SQL Server w parametrach połączenia. W poniższym przykładzie założono, że uwierzytelniania Windows została skonfigurowana i czy użytkownik uzyskał dostęp do wystąpienia programu SQL Server.

    "Server=mysqlvm;Integrated Security=true"

## <a name="steps-for-configuring-sql-server-connectivity-in-an-azure-vm"></a>Procedura konfigurowania połączenia programu SQL Server w Maszynie wirtualnej platformy Azure
Poniższe kroki pokazują, jak połączyć się z wystąpieniem programu SQL Server w Internecie przy użyciu programu SQL Server Management Studio (SSMS). Jednak te same kroki dotyczą udostępnienie maszyny wirtualnej programu SQL Server dla aplikacji uruchomionych zarówno lokalnie, jak i na platformie Azure.

Zanim będzie można połączyć się z wystąpieniem programu SQL Server z innej maszyny Wirtualnej lub z Internetu, należy wykonać następujące zadania:

* [Tworzenie punktu końcowego TCP dla maszyny wirtualnej](#create-a-tcp-endpoint-for-the-virtual-machine)
* [Otwieranie portów TCP w Zaporze Windows](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
* [Konfigurowanie programu SQL Server do nasłuchiwania protokołu TCP](#configure-sql-server-to-listen-on-the-tcp-protocol)
* [Konfigurowanie programu SQL Server na potrzeby uwierzytelniania w trybie mieszanym](#configure-sql-server-for-mixed-mode-authentication)
* [Tworzenie identyfikatorów logowania uwierzytelniania programu SQL Server](#create-sql-server-authentication-logins)
* [Określ nazwę DNS maszyny wirtualnej](#determine-the-dns-name-of-the-virtual-machine)
* [Łączenie z aparatem bazy danych z innego komputera](#connect-to-the-database-engine-from-another-computer)

Ścieżka połączenia jest podsumowane według poniższym diagramie:

![Połączenie z maszyną wirtualną programu SQL Server](../../../../includes/media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

[!INCLUDE [Connect to SQL Server in a VM Classic TCP Endpoint](../../../../includes/virtual-machines-sql-server-connection-steps-classic-tcp-endpoint.md)]

[!INCLUDE [Connect to SQL Server in a VM](../../../../includes/virtual-machines-sql-server-connection-steps.md)]

[!INCLUDE [Connect to SQL Server in a VM Classic Steps](../../../../includes/virtual-machines-sql-server-connection-steps-classic.md)]

## <a name="next-steps"></a>Następne kroki
Jeśli planowane jest również używane grupy dostępności AlwaysOn o wysokiej dostępności i odzyskiwania po awarii, należy rozważyć Implementowanie odbiornik. Klienty baz danych połączenia z odbiornikiem, a nie bezpośrednio do jednego z wystąpień programu SQL Server. Odbiornik kieruje klientów do repliki podstawowej w grupie dostępności. Aby uzyskać więcej informacji, zobacz [Konfigurowanie odbiornika ILB dla zawsze włączonych grup dostępności na platformie Azure](../classic/ps-sql-int-listener.md).

Należy przejrzeć wszystkie najważniejsze wskazówki dotyczące zabezpieczeń programu SQL Server uruchomionego na maszynie wirtualnej platformy Azure. Aby uzyskać więcej informacji, zobacz [Zagadnienia dotyczące zabezpieczeń programu SQL Server w usłudze Azure Virtual Machines](../sql/virtual-machines-windows-sql-security.md).

[Zbadaj ścieżkę szkoleniową](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) dla programu SQL Server na maszynach wirtualnych Azure. 

Aby uzyskać inne tematy związane z programem SQL Server na maszynach wirtualnych Azure, zobacz [programu SQL Server na maszynach wirtualnych Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

