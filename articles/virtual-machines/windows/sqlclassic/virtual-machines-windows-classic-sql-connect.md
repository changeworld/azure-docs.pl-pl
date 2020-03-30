---
title: Łączenie się z maszyną wirtualną programu SQL Server na platformie Azure (klasyczny) | Dokumenty firmy Microsoft
description: Dowiedz się, jak połączyć się z programem SQL Server uruchomionym na maszynie wirtualnej na platformie Azure. W tym temacie użyto klasycznego modelu wdrażania. Scenariusze różnią się w zależności od konfiguracji sieci i lokalizacji klienta.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.assetid: 416948af-454f-4cfe-8fd2-7cf971cbd3e9
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mathoma
ms.reviewer: jroth
experimental: true
experimental_id: d51f3cc6-753b-4e
ms.openlocfilehash: 4627d9c4fa5c87e8e80ab80892062dabd77e9229
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249713"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure-classic-deployment"></a>Łączenie z maszyną wirtualną programu SQL Server na platformie Azure (wdrażanie klasyczne)
> [!div class="op_single_selector"]
> * [Menedżer zasobów](../sql/virtual-machines-windows-sql-connect.md)
> * [Wdrożenie klasyczne](../classic/sql-connect.md)
> 
> 

## <a name="overview"></a>Omówienie
W tym temacie opisano sposób łączenia się z wystąpieniem programu SQL Server uruchomionym na maszynie wirtualnej platformy Azure. Obejmuje ona niektóre [ogólne scenariusze łączności,](#connection-scenarios) a następnie zawiera [szczegółowe kroki konfigurowania łączności programu SQL Server w maszynie Wirtualnej platformy Azure.](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm)

> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Menedżer zasobów i Klasyczny](../../../azure-resource-manager/management/deployment-models.md). W tym artykule opisano przy użyciu modelu wdrażania klasycznego. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Jeśli używasz maszyn wirtualnych Menedżera zasobów, zobacz [Łączenie się z maszyną wirtualną programu SQL Server na platformie Azure przy użyciu Menedżera zasobów](../sql/virtual-machines-windows-sql-connect.md).

## <a name="connection-scenarios"></a>Scenariusze połączeń
Sposób, w jaki klient łączy się z programem SQL Server uruchomionym na maszynie wirtualnej, różni się w zależności od lokalizacji klienta i konfiguracji maszyny/sieci. Scenariusze obejmują:

* [Łączenie się z programem SQL Server w tej samej usłudze w chmurze](#connect-to-sql-server-in-the-same-cloud-service)
* [Łączenie się z programem SQL Server przez Internet](#connect-to-sql-server-over-the-internet)
* [Łączenie się z programem SQL Server w tej samej sieci wirtualnej](#connect-to-sql-server-in-the-same-virtual-network)

> [!NOTE]
> Przed nawiązaniem połączenia z dowolną z tych metod należy wykonać [kroki opisane w tym artykule, aby skonfigurować łączność](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).
> 
> 

### <a name="connect-to-sql-server-in-the-same-cloud-service"></a>Łączenie się z programem SQL Server w tej samej usłudze w chmurze
Wiele maszyn wirtualnych można utworzyć w tej samej usłudze w chmurze. Aby zrozumieć ten scenariusz maszyn wirtualnych, zobacz [Jak połączyć maszyny wirtualne z siecią wirtualną lub usługą w chmurze](/previous-versions/azure/virtual-machines/windows/classic/connect-vms-classic#connect-vms-in-a-standalone-cloud-service). W tym scenariuszu jest, gdy klient na jednej maszynie wirtualnej próbuje połączyć się z programem SQL Server uruchomiony na innej maszynie wirtualnej w tej samej usłudze w chmurze.

W tym scenariuszu można połączyć się przy użyciu **nazwy** maszyny Wirtualnej (również wyświetlane jako **nazwa komputera** lub **nazwa hosta** w portalu). Jest to nazwa podana dla maszyny Wirtualnej podczas tworzenia. Na przykład jeśli nazwano maszynę wirtualną SQL **mysqlvm,** maszyna wirtualna klienta w tej samej usłudze w chmurze może użyć następującego ciągu połączenia do połączenia:

    "Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

### <a name="connect-to-sql-server-over-the-internet"></a>Łączenie się z programem SQL Server przez Internet
Jeśli chcesz połączyć się z aparatem bazy danych programu SQL Server z Internetu, musisz utworzyć punkt końcowy maszyny wirtualnej dla przychodzącej komunikacji TCP. Ten krok konfiguracji platformy Azure kieruje przychodzący ruch portowy TCP do portu TCP, który jest dostępny dla maszyny wirtualnej.

Aby połączyć się przez Internet, należy użyć nazwy DNS maszyny Wirtualnej i numeru portu punktu końcowego maszyny Wirtualnej (skonfigurowany w dalszej części tego artykułu). Aby znaleźć nazwę DNS, przejdź do witryny Azure portal i wybierz pozycję **Maszyny wirtualne (klasyczne).** Następnie wybierz maszynę wirtualną. **Nazwa DNS** jest wyświetlana w sekcji **Przegląd.**

Rozważmy na przykład klasyczną maszynę wirtualną o nazwie **mysqlvm** z nazwą DNS **mysqlvm7777.cloudapp.net** i punktem końcowym maszyny Wirtualnej **57500**. Przy założeniu, że prawidłowo skonfigurowana łączność, do uzyskania dostępu do maszyny wirtualnej z dowolnego miejsca w Internecie można użyć następującego ciągu połączenia:

    "Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Chociaż umożliwia to łączność dla klientów przez Internet, nie oznacza to, że każdy może połączyć się z programem SQL Server. Klienci zewnętrzni muszą mieć prawidłową nazwę użytkownika i hasło. Aby uzyskać dodatkowe zabezpieczenia, nie należy używać dobrze znanego portu 1433 dla publicznego punktu końcowego maszyny wirtualnej. A jeśli to możliwe, należy rozważyć dodanie listy ACL w punkcie końcowym, aby ograniczyć ruch tylko do klientów, na których zezwalasz. Aby uzyskać instrukcje dotyczące używania list ACL z punktami końcowymi, zobacz [Zarządzanie listą ACL w punkcie końcowym](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint).

> [!NOTE]
> Należy pamiętać, że podczas korzystania z tej techniki do komunikowania się z programem SQL Server wszystkie dane wychodzące z centrum danych platformy Azure podlegają [normalnej cenie za transfery danych wychodzących.](https://azure.microsoft.com/pricing/details/data-transfers/)
> 
> 

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>Łączenie się z programem SQL Server w tej samej sieci wirtualnej
[Sieć wirtualna](../../../virtual-network/virtual-networks-overview.md) umożliwia dodatkowe scenariusze. Maszyny wirtualne można łączyć w tej samej sieci wirtualnej, nawet jeśli te maszyny wirtualne istnieją w różnych usługach w chmurze. A dzięki [sieci VPN typu lokacja lokacja](../../../vpn-gateway/vpn-gateway-site-to-site-create.md)można utworzyć architekturę hybrydową, która łączy maszyny wirtualne z sieciami i komputerami lokalnymi.

Sieci wirtualne umożliwiają również dołączanie maszyn wirtualnych platformy Azure do domeny. Jest to jedyny sposób użycia uwierzytelniania systemu Windows w programie SQL Server. Inne scenariusze połączeń wymagają uwierzytelniania SQL z nazwami użytkowników i hasłami.

Jeśli zamierzasz skonfigurować środowisko domeny i uwierzytelnianie systemu Windows, nie trzeba używać kroków w tym artykule, aby skonfigurować publiczny punkt końcowy lub uwierzytelnianie SQL i logowania. W tym scenariuszu można połączyć się z wystąpieniem programu SQL Server, określając nazwę maszyny Wirtualnej programu SQL Server w ciągu połączenia. W poniższym przykładzie przyjęto założenie, że uwierzytelnianie systemu Windows również zostało skonfigurowane i że użytkownikowi udzielono dostępu do wystąpienia programu SQL Server.

    "Server=mysqlvm;Integrated Security=true"

## <a name="steps-for-configuring-sql-server-connectivity-in-an-azure-vm"></a>Kroki konfigurowania łączności programu SQL Server na maszynie Wirtualnej platformy Azure
W poniższych krokach pokazano, jak połączyć się z wystąpieniem programu SQL Server za pośrednictwem Internetu przy użyciu programu SQL Server Management Studio (SSMS). Jednak te same kroki dotyczą udostępnienia maszyny wirtualnej programu SQL Server dla aplikacji, działającej lokalnie i na platformie Azure.

Aby można było połączyć się z wystąpieniem programu SQL Server z innej maszyny Wirtualnej lub Internetu, należy wykonać następujące zadania zgodnie z opisem w poniższych sekcjach:

* [Tworzenie punktu końcowego TCP dla maszyny wirtualnej](#create-a-tcp-endpoint-for-the-virtual-machine)
* [Otwieranie portów TCP w zaporze systemu Windows](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
* [Konfigurowanie programu SQL Server do nasłuchiwania w protokole TCP](#configure-sql-server-to-listen-on-the-tcp-protocol)
* [Konfigurowanie programu SQL Server do uwierzytelniania w trybie mieszanym](#configure-sql-server-for-mixed-mode-authentication)
* [Tworzenie identyfikatora logowania do uwierzytelniania w programie SQL Server](#create-sql-server-authentication-logins)
* [Określanie nazwy DNS maszyny wirtualnej](#determine-the-dns-name-of-the-virtual-machine)
* [Nawiązywanie połączenia z aparatem bazy danych z innego komputera](#connect-to-the-database-engine-from-another-computer)

Ścieżka połączenia jest podsumowana na poniższym diagramie:

![Łączenie się z maszyną wirtualną programu SQL Server](../../../../includes/media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

[!INCLUDE [Connect to SQL Server in a VM Classic TCP Endpoint](../../../../includes/virtual-machines-sql-server-connection-steps-classic-tcp-endpoint.md)]

[!INCLUDE [Connect to SQL Server in a VM](../../../../includes/virtual-machines-sql-server-connection-steps.md)]

[!INCLUDE [Connect to SQL Server in a VM Classic Steps](../../../../includes/virtual-machines-sql-server-connection-steps-classic.md)]

## <a name="next-steps"></a>Następne kroki
Jeśli planujesz również używać alwayson dostępności grup dla wysokiej dostępności i odzyskiwania po awarii, należy rozważyć wdrożenie odbiornika. Klienci bazy danych łączą się z odbiornikiem, a nie bezpośrednio z jednym z wystąpień programu SQL Server. Odbiornik kieruje klientów do repliki podstawowej w grupie dostępności. Aby uzyskać więcej informacji, zobacz [Konfigurowanie odbiornika równoważenia obciążenia dla grup dostępności AlwaysOn na platformie Azure](../classic/ps-sql-int-listener.md).

Ważne jest, aby przejrzeć wszystkie najlepsze rozwiązania dotyczące zabezpieczeń dla programu SQL Server uruchomionego na maszynie wirtualnej platformy Azure. Aby uzyskać więcej informacji, zobacz [Zagadnienia dotyczące zabezpieczeń programu SQL Server w usłudze Azure Virtual Machines](../sql/virtual-machines-windows-sql-security.md).

[Zbadaj ścieżkę szkoleniową](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) dla programu SQL Server na maszynach wirtualnych Azure. 

Aby zapoznać się z innymi tematami związanymi z uruchamianiem programu SQL Server na maszynach wirtualnych platformy Azure, zobacz [SQL Server na maszynach wirtualnych platformy Azure.](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

