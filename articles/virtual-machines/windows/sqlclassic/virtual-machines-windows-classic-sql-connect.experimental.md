---
title: Połączenie z maszyną wirtualną programu SQL Server na platformie Azure (klasyczne) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak nawiązać połączenia z programem SQL Server uruchomiony na maszynie wirtualnej na platformie Azure. W tym temacie używa klasycznego modelu wdrażania. Scenariusze są różne w zależności od konfiguracji sieci i lokalizację klienta.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
tags: azure-service-management
ms.assetid: 416948af-454f-4cfe-8fd2-7cf971cbd3e9
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: jroth
experimental_id: d51f3cc6-753b-4e
ms.openlocfilehash: e986440edc0b683d1b1cd49207a355dac060c53d
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "29398322"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure-classic-deployment"></a>Łączenie z maszyną wirtualną programu SQL Server na platformie Azure (wdrażanie klasyczne)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-connect.md)
> * [Wdrożenie klasyczne](../classic/sql-connect.md)
> 
> 

## <a name="overview"></a>Przegląd
W tym temacie opisano sposób podłączania do wystąpienia programu SQL Server uruchomiony na maszynie wirtualnej platformy Azure. Obejmuje on niektóre [scenariusze ogólne łączności](#connection-scenarios) , a następnie oferuje [szczegółowe kroki związane z konfigurowaniem łączności z serwerem SQL w maszynie Wirtualnej platformy Azure](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../../../azure-resource-manager/resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Jeśli używasz Menedżera zasobów maszyn wirtualnych, zobacz [Connect do programu SQL Server maszynę wirtualną na platformie Azure przy użyciu usługi Resource Manager](../sql/virtual-machines-windows-sql-connect.md).

## <a name="connection-scenarios"></a>Scenariusze łączenia
Sposób, który klient nawiąże połączenie z programem SQL Server uruchomiony na maszynie wirtualnej różni się w zależności od lokalizacji klienta i konfiguracji sieci/maszyny. Scenariusze obejmują:

* [Połączenia z serwerem SQL w tej samej usłudze w chmurze](#connect-to-sql-server-in-the-same-cloud-service)
* [Połączenia z serwerem SQL w Internecie](#connect-to-sql-server-over-the-internet)
* [Połączenia z serwerem SQL w tej samej sieci wirtualnej](#connect-to-sql-server-in-the-same-virtual-network)

> [!NOTE]
> Przed nawiązaniem połączenia z żadnym z tych metod, należy wykonać [kroków w tym artykule, aby skonfigurować połączenie](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).
> 
> 

### <a name="connect-to-sql-server-in-the-same-cloud-service"></a>Połączenia z serwerem SQL w tej samej usłudze w chmurze
Można tworzyć wiele maszyn wirtualnych w tej samej usłudze w chmurze. Aby zrozumieć, w tym scenariuszu maszyn wirtualnych, zobacz [sposób nawiązywania połączenia z usługą wirtualnych sieci lub w chmurze maszyn wirtualnych](../classic/connect-vms-classic.md#connect-vms-in-a-standalone-cloud-service). W tym scenariuszu klienta na jednej maszynie wirtualnej próbuje nawiązać połączenia z programem SQL Server uruchomionym na innej maszynie wirtualnej w tej samej usłudze w chmurze.

W tym scenariuszu można połączyć przy użyciu maszyny Wirtualnej **nazwa** (także wyświetlane jako **nazwy komputera** lub **hostname** w portalu). Jest to nazwa, podane dla maszyny Wirtualnej podczas tworzenia. Na przykład, jeśli nazwę maszyny Wirtualnej SQL **mysqlvm**, klient maszyny Wirtualnej w tej samej usłudze w chmurze można użyć następujące parametry połączenia do połączenia:

    "Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

### <a name="connect-to-sql-server-over-the-internet"></a>Połączenia z serwerem SQL w Internecie
Jeśli chcesz nawiązać połączenia z aparatem bazy danych programu SQL Server z Internetu, należy utworzyć punktu końcowego maszyny wirtualnej w celu komunikacji przychodzącej TCP. Ten krok konfiguracji platformy Azure, kieruje ruch przychodzący port TCP do portu TCP, który jest dostępny dla maszyny wirtualnej.

Aby połączyć się za pośrednictwem Internetu, należy użyć nazwy DNS maszyny Wirtualnej i numer portu punktu końcowego maszyny Wirtualnej (skonfigurowanej w dalszej części tego artykułu). Aby znaleźć nazwy DNS, przejdź do portalu Azure i wybierz **maszyn wirtualnych (klasyczne)**. Następnie wybierz maszynę wirtualną. **Nazwy DNS** jest wyświetlany w obszarze **omówienie** sekcji.

Rozważmy na przykład klasyczne maszyny wirtualnej o nazwie **mysqlvm** o nazwie DNS **mysqlvm7777.cloudapp.net** i punkt końcowy maszyny Wirtualnej **57500**. Zakładając, że łączność poprawnie skonfigurowane, następujący ciąg połączenia może służyć do dostęp do maszyny wirtualnej z dowolnego miejsca w Internecie:

    "Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Mimo że te parametry połączenia umożliwia łączność klientów za pośrednictwem Internetu, nie oznacza każdy można połączyć z serwerem SQL. Poza klienci muszą prawidłową nazwę użytkownika i hasło. Aby dodatkowo zwiększyć bezpieczeństwo nie używaj dobrze znanego portu 1433 dla punktu końcowego publicznego maszyny wirtualnej. A jeśli to możliwe, należy rozważyć dodanie listy ACL na punkcie końcowym, aby ograniczyć ruch tylko do klientów należy zezwolić na działanie. Aby uzyskać instrukcje dotyczące przy użyciu list kontroli dostępu z punktami końcowymi, zobacz [Zarządzanie listy ACL punktu końcowego](../classic/setup-endpoints.md#manage-the-acl-on-an-endpoint).

> [!NOTE]
> Korzystając z tej techniki do komunikowania się z programem SQL Server, wszystkie dane wychodzące z centrum danych Azure podlega normalny [ceny na transfer danych wychodzących](https://azure.microsoft.com/pricing/details/data-transfers/).
> 
> 

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>Połączenia z serwerem SQL w tej samej sieci wirtualnej
[Sieć wirtualna](../../../virtual-network/virtual-networks-overview.md) umożliwia dodatkowe scenariusze. Możesz połączyć maszyny wirtualne w tej samej sieci wirtualnej, nawet jeżeli tych maszyn wirtualnych znajdują się w innej chmurze usługi. I [sieci VPN typu lokacja lokacja](../../../vpn-gateway/vpn-gateway-site-to-site-create.md), możesz utworzyć to architektura hybrydowego łączy maszyn wirtualnych z lokalnymi sieciami i maszyn.

Sieci wirtualne umożliwiają także dołączyć do domeny maszynach wirtualnych platformy Azure. Przyłączanie do domeny jest jedynym sposobem, aby używać uwierzytelniania systemu Windows z programem SQL Server. Inne scenariusze połączenia wymagają uwierzytelniania SQL z nazwy użytkownika i hasła.

Jeśli planujesz Konfigurowanie środowiska domeny i uwierzytelniania systemu Windows, jest konieczne konfigurowanie publiczny punkt końcowy lub uwierzytelnianie SQL i logowania. W tym scenariuszu można połączyć z do wystąpienia programu SQL Server, określając nazwę maszyny Wirtualnej programu SQL Server w parametrach połączenia. W poniższym przykładzie założono, że skonfigurowano uwierzytelnianie systemu Windows i że użytkownik uzyskał dostęp do wystąpienia programu SQL Server.

    "Server=mysqlvm;Integrated Security=true"

## <a name="steps-for-configuring-sql-server-connectivity-in-an-azure-vm"></a>Kroki konfigurowania połączenia programu SQL Server w maszynie Wirtualnej platformy Azure
Poniższe kroki pokazują, jak połączyć się z wystąpieniem programu SQL Server za pośrednictwem Internetu, przy użyciu programu SQL Server Management Studio (SSMS). Jednak te same kroki dotyczą udostępnienie maszyny wirtualnej programu SQL Server dla aplikacji, a także uruchomiona lokalnie i na platformie Azure.

Zanim będzie można połączyć się z wystąpieniem programu SQL Server z inną maszynę Wirtualną lub z Internetu, należy wykonać następujące zadania:

* [Utwórz punkt końcowy protokołu TCP dla maszyny wirtualnej](#create-a-tcp-endpoint-for-the-virtual-machine)
* [Otwartych portów TCP w Zaporze systemu Windows](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
* [Konfigurowanie serwera SQL do nasłuchiwania protokołu TCP](#configure-sql-server-to-listen-on-the-tcp-protocol)
* [Konfigurowanie programu SQL Server na potrzeby uwierzytelniania w trybie mieszanym](#configure-sql-server-for-mixed-mode-authentication)
* [Tworzenie identyfikatorów logowania uwierzytelniania programu SQL Server](#create-sql-server-authentication-logins)
* [Określanie nazwy DNS maszyny wirtualnej](#determine-the-dns-name-of-the-virtual-machine)
* [Nawiąż połączenie z aparatem bazy danych z innego komputera](#connect-to-the-database-engine-from-another-computer)

Ścieżka połączenia podsumowano w poniższym diagramie:

![Połączenie z maszyną wirtualną programu SQL Server](../../../../includes/media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

[!INCLUDE [Connect to SQL Server in a VM Classic TCP Endpoint](../../../../includes/virtual-machines-sql-server-connection-steps-classic-tcp-endpoint.md)]

[!INCLUDE [Connect to SQL Server in a VM](../../../../includes/virtual-machines-sql-server-connection-steps.md)]

[!INCLUDE [Connect to SQL Server in a VM Classic Steps](../../../../includes/virtual-machines-sql-server-connection-steps-classic.md)]

## <a name="next-steps"></a>Następne kroki
Jeśli planujesz również używać zawsze włączonych grup dostępności, wysokiej dostępności i odzyskiwania po awarii, należy rozważyć wdrożenie odbiornik. Bazy danych, klienci łączą się odbiornika, a nie bezpośrednio do wystąpienia programu SQL Server. Odbiornik kieruje klientów do repliki podstawowej grupy dostępności. Aby uzyskać więcej informacji, zobacz [skonfigurować odbiornik ILB dla zawsze włączonych grup dostępności na platformie Azure](../classic/ps-sql-int-listener.md).

Należy sprawdzić wszystkie najlepsze rozwiązania w zakresie zabezpieczeń dla programu SQL Server uruchomionego na maszynie wirtualnej platformy Azure. Aby uzyskać więcej informacji, zobacz [Zagadnienia dotyczące zabezpieczeń programu SQL Server w usłudze Azure Virtual Machines](../sql/virtual-machines-windows-sql-security.md).

[Zbadaj ścieżkę szkoleniową](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) dla programu SQL Server na maszynach wirtualnych Azure. 

Do innych tematów związanych z programem SQL Server na maszynach wirtualnych Azure, zobacz [programu SQL Server na maszynach wirtualnych Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

