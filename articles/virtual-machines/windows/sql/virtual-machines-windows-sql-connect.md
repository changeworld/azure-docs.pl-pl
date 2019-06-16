---
title: Nawiązać połączenie z maszyną wirtualną programu SQL Server (Resource Manager) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak połączyć się z programu SQL Server uruchomionego na maszynie wirtualnej na platformie Azure. Ten temat używa klasycznego modelu wdrażania. Scenariusze różnią się w zależności od konfiguracji sieci i lokalizacji klienta.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/12/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 70e478ac70b7ab53f1357394f3a3cb0d92f41f00
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075787"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure"></a>Nawiązać połączenie z maszyną wirtualną programu SQL Server na platformie Azure

## <a name="overview"></a>Omówienie

W tym temacie opisano, jak połączyć się z wystąpieniem programu SQL Server uruchomiony na maszynie wirtualnej platformy Azure. Obejmuje ona niektóre [scenariuszy ogólna łączność](#connection-scenarios) , a następnie oferuje [kroki w portalu, aby zmiana ustawienia łączności](#change). Jeśli potrzebujesz rozwiązać lub skonfigurować łączność poza portalu, zobacz [ręcznej konfiguracji](#manual) na końcu tego tematu. 

Jeśli czy raczej masz pełne omówienie aprowizowania i łączności, zobacz [inicjowania obsługi administracyjnej maszyny wirtualnej programu SQL Server na platformie Azure](virtual-machines-windows-portal-sql-server-provision.md).

## <a name="connection-scenarios"></a>Scenariusze łączenia

Sposób, gdy klient nawiąże połączenie z SQL Server uruchomionym na maszynie wirtualnej różni się w zależności od lokalizacji klienta i konfiguracji sieci.

Jeśli możesz aprowizować maszynę Wirtualną programu SQL Server w witrynie Azure portal, masz możliwość określenia typu **łączność z serwerem SQL**.

![Publiczne opcji łączności SQL podczas inicjowania obsługi](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity.png)

Opcje łączności:

| Opcja | Opis |
|---|---|
| **Public** | Połączenia z serwerem SQL w Internecie |
| **Prywatne** | Połączenia z serwerem SQL w tej samej sieci wirtualnej |
| **lokalne** | Połączenia z serwerem SQL lokalnie na tej samej maszyny wirtualnej | 

W poniższych sekcjach opisano **publicznych** i **prywatnej** opcji bardziej szczegółowo.

## <a name="connect-to-sql-server-over-the-internet"></a>Połączenia z serwerem SQL w Internecie

Jeśli chcesz nawiązać połączenie z aparatem bazy danych programu SQL Server z Internetu, wybierz opcję **publicznych** dla **łączność z serwerem SQL** typu w portalu podczas inicjowania obsługi. Portal automatycznie wykonuje następujące czynności:

* Włącza protokół TCP/IP dla programu SQL Server.
* Konfiguruje regułę zapory, aby otworzyć port TCP programu SQL Server (domyślnie 1433).
* Umożliwia uwierzytelnianie programu SQL Server wymagane dla dostępu publicznego.
* Umożliwia skonfigurowanie sieciowej grupy zabezpieczeń na maszynie Wirtualnej, aby cały ruch TCP na porcie programu SQL Server.

> [!IMPORTANT]
> Obrazy maszyn wirtualnych dla programu SQL Server Developer i Express nie włączaj automatycznie protokołu TCP/IP. Dla wersji Developer i Express, należy użyć Menedżera konfiguracji programu SQL Server do [ręcznie włączyć protokół TCP/IP](#manualtcp) po utworzeniu maszyny Wirtualnej.

Dowolnego klienta z dostępem do Internetu można połączyć się z wystąpieniem programu SQL Server, określając publiczny adres IP maszyny wirtualnej albo wszelkie etykiety DNS przypisane do tego adresu IP. Jeśli port programu SQL Server jest 1433, nie musisz je określić w parametrach połączenia. Następujący ciąg połączenia łączy do maszyny Wirtualnej SQL z etykietą DNS `sqlvmlabel.eastus.cloudapp.azure.com` przy użyciu uwierzytelniania SQL (można także użyć publicznego adresu IP).

```
Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>
```

Mimo że pozwala to łączność klientów za pośrednictwem Internetu, to nie oznacza, że każda osoba może połączyć się z programu SQL Server. Poza klienci mają prawidłową nazwę użytkownika i hasła. Jednak dla dodatkowego bezpieczeństwa możesz uniknąć dobrze znanym porcie 1433. Na przykład jeśli skonfigurowano program SQL Server do nasłuchiwania na porcie 1500 i ustanowieniu odpowiednie zapory i reguł sieciowych grup zabezpieczeń, można połączyć, dodając numer portu do nazwy serwera. Poniższy przykład modyfikuje poprzedni, dodając niestandardowy numer portu, **1500**, aby nazwa serwera:

```
Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"
```

> [!NOTE]
> Kiedy wykonujesz zapytanie SQL Server na maszynie wirtualnej za pośrednictwem Internetu, wszystkie dane wychodzące z centrów danych platformy Azure podlega postanowieniom normalny [ceny na wychodzące transfery danych](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="connect-to-sql-server-within-a-virtual-network"></a>Połączenia z serwerem SQL w ramach sieci wirtualnej

Po wybraniu **prywatnej** dla **łączność z serwerem SQL** typu w portalu Azure konfiguruje większość ustawień taka sama jak **publicznych**. Jeden różnica polega na tym, że nie istnieje żadne reguły sieciowej grupy zabezpieczeń zezwalającą na ruch poza na port serwera SQL (domyślnie 1433).

> [!IMPORTANT]
> Obrazy maszyn wirtualnych dla programu SQL Server Developer i Express nie włączaj automatycznie protokołu TCP/IP. Dla wersji Developer i Express, należy użyć Menedżera konfiguracji programu SQL Server do [ręcznie włączyć protokół TCP/IP](#manualtcp) po utworzeniu maszyny Wirtualnej.

Połączenia prywatne jest często używana w połączeniu z [sieci wirtualnej](../../../virtual-network/virtual-networks-overview.md), co pozwala kilku scenariuszy. Możesz połączyć maszyny wirtualne w tej samej sieci wirtualnej, nawet jeżeli te maszyny wirtualne znajdują się w różnych grupach zasobów. I [sieci VPN typu lokacja lokacja](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), możesz utworzyć architektury hybrydowej, która łączy maszyn wirtualnych z sieci lokalnych i maszyn.

Sieci wirtualne umożliwiają również dołączyć maszynach wirtualnych platformy Azure do domeny. Jest to jedyny sposób korzystać z uwierzytelniania Windows do programu SQL Server. Inne scenariusze połączenia wymagają uwierzytelniania SQL przy użyciu nazwy użytkownika i hasła.

Przy założeniu, że skonfigurowano DNS w sieci wirtualnej, możesz nawiązać wystąpienia programu SQL Server, określając nazwę komputera maszyny Wirtualnej programu SQL Server w parametrach połączenia. W poniższym przykładzie założono również, czy też zostało skonfigurowane uwierzytelnianie Windows oraz czy użytkownik został udzielony dostęp do wystąpienia programu SQL Server.

```
Server=mysqlvm;Integrated Security=true
```

## <a id="change"></a> Zmiana ustawień łączności SQL

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Możesz zmienić ustawienia łączności dla maszyny wirtualnej programu SQL Server w witrynie Azure portal.

1. W witrynie Azure portal wybierz **maszyn wirtualnych SQL**.

2. Wybierz swój serwer SQL maszyny Wirtualnej.

3. W obszarze **ustawienia**, wybierz opcję **zabezpieczeń**.

4. Zmiana **poziom łączności SQL** wymagane ustawienia. Zmienić port programu SQL Server lub ustawienia uwierzytelniania SQL, można opcjonalnie Użyj tego obszaru.

   ![Zmień łączność z serwerem SQL](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity-change.png)

5. Poczekaj kilka minut w celu ukończenia aktualizacji.

   ![Powiadomienie o aktualizacji maszyny Wirtualnej SQL](./media/virtual-machines-windows-sql-connect/sql-vm-updating-notification.png)

## <a id="manualtcp"></a> Włączanie protokołu TCP/IP dla wersji Developer i Express

Po zmianie ustawienia połączenia programu SQL Server, Azure nie automatycznie włącza protokół TCP/IP dla programu SQL Server Developer i Express. W poniższych krokach omówiono, jak ręcznie włączyć protokół TCP/IP w celu zdalnego nawiązania połączenia przy użyciu adresu IP.

Po pierwsze połączenie z maszyną programu SQL Server przy użyciu pulpitu zdalnego.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Następnie Włącz protokół TCP/IP za pomocą **Menedżera konfiguracji SQL Server**.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-connection-tcp-protocol.md)]

## <a name="connect-with-ssms"></a>Nawiązywanie połączenia z programem SSMS

Poniższe kroki pokazują jak utworzyć opcjonalną etykietę DNS dla swojej maszyny Wirtualnej platformy Azure, a następnie nawiązać połączenie z SQL Server Management Studio (SSMS).

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a id="manual"></a> Konfiguracja ręczna i rozwiązywania problemów

Chociaż portal zawiera opcje, aby automatycznie skonfigurować łączność, warto wiedzieć, jak ręcznie skonfigurować łączność. Opis wymagań może również ułatwić rozwiązywanie problemów.

Poniższa tabela zawiera listę wymagań, aby nawiązać połączenie serwera SQL uruchomionego na Maszynie wirtualnej platformy Azure.

| Wymaganie | Opis |
|---|---|
| [Włącz tryb uwierzytelniania programu SQL Server](https://docs.microsoft.com/sql/database-engine/configure-windows/change-server-authentication-mode#SSMSProcedure) | Uwierzytelnianie programu SQL Server jest wymagany do połączenia z maszyny Wirtualnej zdalnie, chyba że skonfigurowano usługi Active Directory w sieci wirtualnej. |
| [Utwórz identyfikator logowania SQL](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/create-a-login) | Jeśli używasz uwierzytelniania SQL, potrzebujesz identyfikatora logowania SQL przy użyciu nazwy użytkownika i hasło, które również ma uprawnienia do docelowej bazy danych. |
| [Włącz protokół TCP/IP](#manualtcp) | Program SQL Server należy zezwolić na połączenia za pośrednictwem protokołu TCP. |
| [Włącz regułę zapory dla portu programu SQL Server](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) | Zapora na maszynie Wirtualnej muszą zezwalać na ruch przychodzący na porcie programu SQL Server (domyślnie 1433). |
| [Tworzenie reguły sieciowej grupy zabezpieczeń dla TCP 1433](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) | Musisz zezwolić na maszynę Wirtualną, aby odbierać ruch na porcie programu SQL Server (domyślnie 1433), jeśli chcesz się połączyć za pośrednictwem Internetu. Połączenia lokalne i wirtualne sieci tylko nie wymagają tego. Jest to jedyny krok wymagany w witrynie Azure portal. |

> [!TIP]
> Kroki opisane w powyższej tabeli są wykonywane tylko dla Ciebie, po skonfigurowaniu połączenia w portalu. Tylko wykonaj następujące kroki, aby upewnić się, konfiguracji lub można ręcznie skonfigurować łączność dla programu SQL Server.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, inicjowanie obsługi administracyjnej instrukcje wraz z tych kroków łączności, zobacz [inicjowania obsługi administracyjnej maszyny wirtualnej programu SQL Server na platformie Azure](virtual-machines-windows-portal-sql-server-provision.md).

Aby uzyskać inne tematy związane z programem SQL Server na maszynach wirtualnych Azure, zobacz [programu SQL Server na maszynach wirtualnych Azure](virtual-machines-windows-sql-server-iaas-overview.md).