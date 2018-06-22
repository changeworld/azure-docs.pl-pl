---
title: Połączenie z maszyną wirtualną programu SQL Server (Resource Manager) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak nawiązać połączenia z programem SQL Server uruchomiony na maszynie wirtualnej na platformie Azure. W tym temacie używa klasycznego modelu wdrażania. Scenariusze są różne w zależności od konfiguracji sieci i lokalizację klienta.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/12/2017
ms.author: jroth
ms.openlocfilehash: 522ece2528e43c1037dc6bb707201ecda8074dd9
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36301390"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure"></a>Połączenie z maszyną wirtualną programu SQL Server na platformie Azure

## <a name="overview"></a>Przegląd

W tym temacie opisano sposób podłączania do wystąpienia programu SQL Server uruchomiony na maszynie wirtualnej platformy Azure. Obejmuje on niektóre [scenariusze ogólne łączności](#connection-scenarios) , a następnie oferuje [kroki w portalu, aby zmiana ustawienia łączności](#change). Jeśli chcesz rozwiązać lub konfigurowania łączności poza portalu, zobacz [ręcznej konfiguracji](#manual) na końcu tego tematu. 

Jeśli trzeba będzie raczej pełny przewodnik inicjowania obsługi administracyjnej i łączność, zobacz [inicjowania obsługi maszyny wirtualnej programu SQL Server na platformie Azure](virtual-machines-windows-portal-sql-server-provision.md).

## <a name="connection-scenarios"></a>Scenariusze łączenia

Sposób którą klient nawiąże połączenie z programem SQL Server uruchomiony na maszynie wirtualnej różni się w zależności od lokalizacji klienta i konfiguracji sieci.

Jeśli dostarczasz maszyny Wirtualnej programu SQL Server w portalu Azure, masz możliwość określenia typu **łączność z serwerem SQL**.

![Publiczny opcji łączności SQL podczas inicjowania obsługi](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity.png)

Dostępne opcje dla połączenia:

| Opcja | Opis |
|---|---|
| **Public** | Połączenia z serwerem SQL w Internecie |
| **Prywatne** | Połączenia z serwerem SQL w tej samej sieci wirtualnej |
| **lokalne** | Połączenia z serwerem SQL lokalnie na tej samej maszyny wirtualnej | 

W poniższych sekcjach opisano **publicznego** i **prywatnej** szczegółowo opcje.

## <a name="connect-to-sql-server-over-the-internet"></a>Połączenia z serwerem SQL w Internecie

Jeśli chcesz nawiązać połączenia z aparatem bazy danych programu SQL Server z Internetu, wybierz opcję **publicznego** dla **łączność z serwerem SQL** typu w portalu podczas inicjowania obsługi. Portal automatycznie wykonuje następujące czynności:

* Włącza protokół TCP/IP dla programu SQL Server.
* Konfiguruje regułę zapory, aby otworzyć port TCP programu SQL Server (domyślnie 1433).
* Umożliwia uwierzytelnianie programu SQL Server wymagane dla dostępu publicznego.
* Umożliwia skonfigurowanie grupy zabezpieczeń sieci na maszynie Wirtualnej, aby cały ruch TCP na porcie programu SQL Server.

> [!IMPORTANT]
> Obrazy maszyny wirtualnej dla programu SQL Server Developer i wersji Express nie należy włączać automatycznie protokołu TCP/IP. Dla deweloperów i ekspresowej wersji, należy użyć programu SQL Server Configuration Manager do [ręcznie Włącz protokół TCP/IP](#manualtcp) po utworzeniu maszyny Wirtualnej.

Dowolnego klienta z dostępem do Internetu może nawiązać wystąpienie programu SQL Server za pośrednictwem publicznego adresu IP maszyny wirtualnej albo jakakolwiek Etykieta DNS przypisane do tego adresu IP. Port serwera SQL jest port 1433, nie trzeba określić je w parametrach połączenia. Następujący ciąg połączenia łączy się z maszyną wirtualną programu SQL z etykietą DNS `sqlvmlabel.eastus.cloudapp.azure.com` przy użyciu uwierzytelniania programu SQL (można także użyć publiczny adres IP).

```
Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>
```

Mimo że to umożliwia łączność klientów za pośrednictwem Internetu, nie oznacza każdy można połączyć z serwerem SQL. Poza klienci muszą prawidłową nazwę użytkownika i hasło. Jednak aby dodatkowo zwiększyć bezpieczeństwo, można uniknąć dobrze znanego portu 1433. Na przykład jeśli skonfigurowano program SQL Server do nasłuchiwania na port 1500 i ustalonych prawidłowego zapory i reguł grup zabezpieczeń sieci może połączyć przez dołączenie numer portu do nazwy serwera. Poniższy przykład powoduje zmianę poprzedniego przez dodanie niestandardowy numer portu, **1500**, aby nazwa serwera:

```
Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"
```

> [!NOTE]
> Określona w zapytaniu SQL Server na maszynie wirtualnej za pośrednictwem Internetu, wszystkich danych wychodzących z centrum danych Azure podlega normalny [ceny na transfer danych wychodzących](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="connect-to-sql-server-within-a-virtual-network"></a>Połączenia z serwerem SQL w ramach sieci wirtualnej

Po wybraniu **prywatnej** dla **łączność z serwerem SQL** typu w portalu Azure konfiguruje większości takie same jak ustawienia **publicznego**. Jeden różnica polega na tym, czy znajduje się żadna reguła grupy zabezpieczeń sieci zezwalająca na ruch poza na port serwera SQL (domyślnie 1433).

> [!IMPORTANT]
> Obrazy maszyny wirtualnej dla programu SQL Server Developer i wersji Express nie należy włączać automatycznie protokołu TCP/IP. Dla deweloperów i ekspresowej wersji, należy użyć programu SQL Server Configuration Manager do [ręcznie Włącz protokół TCP/IP](#manualtcp) po utworzeniu maszyny Wirtualnej.

Prywatne łączności jest często używana w połączeniu z [sieci wirtualnej](../../../virtual-network/virtual-networks-overview.md), co pozwala kilka scenariuszy. Możesz połączyć maszyn wirtualnych w tej samej sieci wirtualnej, nawet jeżeli tych maszyn wirtualnych znajdują się w różnych grupach zasobów. I [sieci VPN typu lokacja lokacja](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), możesz utworzyć to architektura hybrydowego łączy maszyn wirtualnych z lokalnymi sieciami i maszyn.

Sieci wirtualne umożliwiają także dołączyć do domeny maszynach wirtualnych platformy Azure. To jest jedynym sposobem, aby używać uwierzytelniania systemu Windows z programem SQL Server. Inne scenariusze połączenia wymagają uwierzytelniania SQL z nazwy użytkownika i hasła.

Przy założeniu, że skonfigurowano DNS w sieci wirtualnej, należy połączyć z do wystąpienia programu SQL Server, określając nazwę komputera maszyny Wirtualnej programu SQL Server w parametrach połączenia. W poniższym przykładzie założono również, czy też skonfigurowano uwierzytelnianie systemu Windows i czy użytkownik ma zostać przyznany dostęp do wystąpienia programu SQL Server.

```
Server=mysqlvm;Integrated Security=true
```

## <a id="change"></a> Zmień ustawienia połączenia SQL

Możesz zmienić ustawienia połączenia dla maszyny wirtualnej programu SQL Server w portalu Azure.

1. W portalu Azure wybierz **maszyn wirtualnych**.

2. Wybierz program SQL Server maszyny Wirtualnej.

3. W obszarze **ustawienia**, kliknij przycisk **konfigurację programu SQL Server**.

4. Zmień **poziom Połączenie SQL** wymagane ustawienia. Ten obszar służy Opcjonalnie można zmienić port programu SQL Server i jego ustawienia uwierzytelniania SQL.

   ![Zmień łączność z serwerem SQL](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity-change.png)

5. Poczekaj kilka minut na ukończenie aktualizacji.

   ![Powiadomienie o aktualizacji maszyny Wirtualnej SQL](./media/virtual-machines-windows-sql-connect/sql-vm-updating-notification.png)

## <a id="manualtcp"></a> Włącz protokół TCP/IP dla deweloperów i Express w wersji

Zmieniając ustawienia łączności serwera SQL Azure nie powoduje automatycznego włączenia protokołu TCP/IP dla programu SQL Server Developer i wersji Express. W poniższych krokach omówiono, jak ręcznie włączyć protokół TCP/IP w celu zdalnego nawiązania połączenia przy użyciu adresu IP.

Najpierw nawiąż połączenie z maszyną programu SQL Server przy użyciu pulpitu zdalnego.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Następnie Włącz protokół TCP/IP z **SQL Server Configuration Manager**.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-connection-tcp-protocol.md)]

## <a name="connect-with-ssms"></a>Nawiązywanie połączenia z programem SSMS

Poniższe kroki pokazują, jak utworzyć opcjonalną etykietę DNS dla sieci maszyny Wirtualnej platformy Azure, a następnie nawiąż połączenie z SQL Server Management Studio (SSMS).

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a id="manual"></a> Ręczne konfigurowanie i rozwiązywanie problemów

Portalu zapewnia opcje, aby automatycznie skonfigurować połączenie, jednak warto wiedzieć, jak ręcznie skonfigurować połączenie. Zapoznanie się z wymaganiami może również ułatwić rozwiązywanie problemów.

Poniższa lista zawiera wymagania dotyczące nawiązywania połączenia z programem SQL Server w maszynie Wirtualnej platformy Azure.

| Wymaganie | Opis |
|---|---|
| [Włącz tryb uwierzytelniania programu SQL Server](https://docs.microsoft.com/sql/database-engine/configure-windows/change-server-authentication-mode#SSMSProcedure) | Uwierzytelnianie programu SQL Server jest potrzebny do zdalnego łączenia się z maszyny Wirtualnej, chyba że skonfigurowano usługi Active Directory w sieci wirtualnej. |
| [Utwórz identyfikator logowania SQL](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/create-a-login) | Jeśli korzystasz z uwierzytelniania SQL, należy identyfikatora logowania SQL przy użyciu nazwy użytkownika i hasła, który ma także uprawnienia do docelowej bazy danych. |
| [Włącz protokół TCP/IP](#manualTCP) | SQL Server należy zezwolić na połączenia za pośrednictwem protokołu TCP. |
| [Włącz regułę zapory dla portu programu SQL Server](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) | Zapora na maszynie Wirtualnej musi zezwalać na ruch przychodzący na porcie programu SQL Server (domyślnie 1433). |
| [Tworzenie reguły grupy zabezpieczeń sieci dla TCP 1433](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) | Musisz zezwolić na maszynie Wirtualnej, aby odbierać ruch na port serwera SQL (domyślnie 1433), jeśli chcesz się połączyć za pośrednictwem Internetu. Lokalne i wirtualne sieci — tylko połączenia nie wymagają to. Jest to jedyny krok wymagany w portalu Azure. |

> [!TIP]
> Kroki opisane w powyższej tabeli są wykonywane automatycznie, podczas konfigurowania połączenia w portalu. Tylko wykonaj następujące czynności w celu potwierdzenia konfiguracji lub można ręcznie skonfigurować łączności dla programu SQL Server.

## <a name="next-steps"></a>Następne kroki

Aby wyświetlić inicjowania obsługi administracyjnej instrukcje wraz z tych kroków łączności, zobacz [inicjowania obsługi maszyny wirtualnej programu SQL Server na platformie Azure](virtual-machines-windows-portal-sql-server-provision.md).

Do innych tematów związanych z programem SQL Server na maszynach wirtualnych Azure, zobacz [programu SQL Server na maszynach wirtualnych Azure](virtual-machines-windows-sql-server-iaas-overview.md).