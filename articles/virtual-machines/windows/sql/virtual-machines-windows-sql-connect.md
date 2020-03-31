---
title: Łączenie się z maszyną wirtualną programu SQL Server (Menedżer zasobów) | Dokumenty firmy Microsoft
description: Dowiedz się, jak połączyć się z programem SQL Server uruchomionym na maszynie wirtualnej na platformie Azure. W tym temacie użyto klasycznego modelu wdrażania. Scenariusze różnią się w zależności od konfiguracji sieci i lokalizacji klienta.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/12/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: deb337d989a3658e909cefa7a9ab028e37792562
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243174"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure"></a>Łączenie się z maszyną wirtualną programu SQL Server na platformie Azure

## <a name="overview"></a>Omówienie

W tym temacie opisano sposób łączenia się z wystąpieniem programu SQL Server uruchomionym na maszynie wirtualnej platformy Azure. Obejmuje ona niektóre [ogólne scenariusze łączności,](#connection-scenarios) a następnie udostępnia [kroki w portalu dotyczące zmiany ustawień łączności.](#change) Jeśli chcesz rozwiązać lub skonfigurować łączność poza portalem, zobacz [ręczną konfigurację](#manual) na końcu tego tematu. 

Jeśli wolisz mieć pełny przebieg zarówno inicjowania obsługi administracyjnej, jak i łączności, zobacz [Inicjowanie obsługi administracyjnej maszyny wirtualnej programu SQL Server na platformie Azure.](virtual-machines-windows-portal-sql-server-provision.md)

## <a name="connection-scenarios"></a>Scenariusze połączeń

Sposób, w jaki klient łączy się z programem SQL Server uruchomionym na maszynie wirtualnej, różni się w zależności od lokalizacji klienta i konfiguracji sieci.

Jeśli aprowizujesz maszynę wirtualną programu SQL Server w portalu Azure, możesz określić typ **łączności SQL**.

![Opcja publicznej łączności SQL podczas inicjowania obsługi administracyjnej](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity.png)

Dostępne opcje łączności obejmują:

| Opcja | Opis |
|---|---|
| **Publicznego** | Łączenie się z programem SQL Server przez Internet |
| **Prywatny** | Łączenie się z programem SQL Server w tej samej sieci wirtualnej |
| **Lokalnych** | Łączenie się z programem SQL Server lokalnie na tej samej maszynie wirtualnej | 

W poniższych sekcjach opisano bardziej szczegółowo opcje **publiczne** i **prywatne.**

## <a name="connect-to-sql-server-over-the-internet"></a>Łączenie się z programem SQL Server przez Internet

Jeśli chcesz połączyć się z aparatem bazy danych programu SQL Server z Internetu, wybierz opcję **Publiczne** dla typu **łączności SQL** w portalu podczas inicjowania obsługi administracyjnej. Portal automatycznie wykonuje następujące czynności:

* Włącza protokół TCP/IP dla programu SQL Server.
* Konfiguruje regułę zapory w celu otwarcia portu TCP programu SQL Server (domyślna wartość 1433).
* Włącza uwierzytelnianie programu SQL Server, wymagane dla dostępu publicznego.
* Konfiguruje sieciową grupę zabezpieczeń na maszynie Wirtualnej do całego ruchu TCP na porcie PROGRAMU SQL Server.

> [!IMPORTANT]
> Obrazy maszyn wirtualnych dla wersji SQL Server Developer i Express nie włączają automatycznie protokołu TCP/IP. W przypadku wersji deweloperskich i ekspresowych należy użyć programu SQL Server Configuration Manager, aby [ręcznie włączyć protokół TCP/IP](#manualtcp) po utworzeniu maszyny wirtualnej.

Każdy klient z dostępem do Internetu może połączyć się z wystąpieniem programu SQL Server, określając publiczny adres IP maszyny wirtualnej lub dowolną etykietę DNS przypisaną do tego adresu IP. Jeśli port programu SQL Server ma numer 1433, nie trzeba go określać w ciągu połączenia. Poniższy ciąg połączenia łączy się z maszyną `sqlvmlabel.eastus.cloudapp.azure.com` wirtualną SQL z etykietą DNS przy użyciu uwierzytelniania SQL (można również użyć publicznego adresu IP).

```
Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>
```

Chociaż umożliwia to łączność dla klientów przez Internet, nie oznacza to, że każdy może połączyć się z programem SQL Server. Klienci zewnętrzni muszą mieć prawidłową nazwę użytkownika i hasło. Jednak ze względów bezpieczeństwa można uniknąć dobrze znanego portu 1433. Jeśli na przykład program SQL Server został skonfigurowany do nasłuchiwania na porcie 1500 i ustanowił odpowiednie reguły zapory i sieciowej grupy zabezpieczeń, można nawiązać połączenie, dołączając numer portu do nazwy serwera. Poniższy przykład zmienia poprzedni, dodając niestandardowy numer portu **1500**do nazwy serwera:

```
Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"
```

> [!NOTE]
> Podczas wykonywania zapytań o program SQL Server na maszynie Wirtualnej przez Internet wszystkie dane wychodzące z centrum danych platformy Azure podlegają [normalnej cenie za wychodzące transfery danych.](https://azure.microsoft.com/pricing/details/data-transfers/)

## <a name="connect-to-sql-server-within-a-virtual-network"></a>Łączenie się z programem SQL Server w sieci wirtualnej

Po wybraniu **opcji Prywatne** dla typu **łączności SQL** w portalu platforma Azure konfiguruje większość ustawień identycznych z **public**. Jedną różnicą jest to, że nie ma reguły sieciowej grupy zabezpieczeń, która zezwalała na ruch zewnętrzny na porcie programu SQL Server (domyślnie 1433).

> [!IMPORTANT]
> Obrazy maszyn wirtualnych dla wersji SQL Server Developer i Express nie włączają automatycznie protokołu TCP/IP. W przypadku wersji deweloperskich i ekspresowych należy użyć programu SQL Server Configuration Manager, aby [ręcznie włączyć protokół TCP/IP](#manualtcp) po utworzeniu maszyny wirtualnej.

Łączność prywatna jest często używana w połączeniu z [siecią wirtualną,](../../../virtual-network/virtual-networks-overview.md)co umożliwia kilka scenariuszy. Maszyny wirtualne można łączyć w tej samej sieci wirtualnej, nawet jeśli te maszyny wirtualne istnieją w różnych grupach zasobów. A dzięki [sieci VPN typu lokacja lokacja](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)można utworzyć architekturę hybrydową, która łączy maszyny wirtualne z sieciami i komputerami lokalnymi.

Sieci wirtualne umożliwiają również dołączanie maszyn wirtualnych platformy Azure do domeny. Jest to jedyny sposób użycia uwierzytelniania systemu Windows w programie SQL Server. Inne scenariusze połączeń wymagają uwierzytelniania SQL z nazwami użytkowników i hasłami.

Zakładając, że system DNS został skonfigurowany w sieci wirtualnej, można połączyć się z wystąpieniem programu SQL Server, określając nazwę komputera maszyny Wirtualnej programu SQL Server w ciągu połączenia. W poniższym przykładzie przyjęto również założenie, że uwierzytelnianie systemu Windows również zostało skonfigurowane i że użytkownikowi udzielono dostępu do wystąpienia programu SQL Server.

```
Server=mysqlvm;Integrated Security=true
```

## <a name="change-sql-connectivity-settings"></a><a id="change"></a>Zmienianie ustawień łączności SQL

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Ustawienia łączności maszyny wirtualnej programu SQL Server można zmienić w witrynie Azure portal.

1. W portalu Azure wybierz maszyn **wirtualnych SQL**.

2. Wybierz maszynę wirtualną programu SQL Server.

3. W obszarze **Ustawienia**wybierz pozycję **Zabezpieczenia**.

4. Zmień **poziom łączności SQL** na wymagane ustawienie. Opcjonalnie można użyć tego obszaru, aby zmienić port programu SQL Server lub ustawienia uwierzytelniania SQL.

   ![Zmienianie łączności SQL](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity-change.png)

5. Poczekaj kilka minut na zakończenie aktualizacji.

   ![Powiadomienie o aktualizacji maszyny Wirtualnej SQL](./media/virtual-machines-windows-sql-connect/sql-vm-updating-notification.png)

## <a name="enable-tcpip-for-developer-and-express-editions"></a><a id="manualtcp"></a>Włączanie protokołu TCP/IP dla wersji deweloperskich i ekspresowych

Podczas zmiany ustawień łączności programu SQL Server platforma Azure nie włącza automatycznie protokołu TCP/IP dla wersji SQL Server Developer i Express. W poniższych krokach omówiono, jak ręcznie włączyć protokół TCP/IP w celu zdalnego nawiązania połączenia przy użyciu adresu IP.

Najpierw połącz się z komputerem programu SQL Server za pomocą pulpitu zdalnego.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Następnie włącz protokół TCP/IP za pomocą **programu SQL Server Configuration Manager**.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-connection-tcp-protocol.md)]

## <a name="connect-with-ssms"></a>Nawiązywanie połączenia z programem SSMS

Poniższe kroki pokazują, jak utworzyć opcjonalną etykietę DNS dla maszyny Wirtualnej platformy Azure, a następnie połączyć się z programem SQL Server Management Studio (SSMS).

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="manual-configuration-and-troubleshooting"></a><a id="manual"></a>Ręczna konfiguracja i rozwiązywanie problemów

Chociaż portal udostępnia opcje automatycznego konfigurowania łączności, warto wiedzieć, jak ręcznie skonfigurować łączność. Zrozumienie wymagań może również pomóc w rozwiązywaniu problemów.

W poniższej tabeli wymieniono wymagania dotyczące łączenia się z programem SQL Server uruchomionym na maszynie wirtualnej platformy Azure.

| Wymaganie | Opis |
|---|---|
| [Włącz tryb uwierzytelniania programu SQL Server](/sql/database-engine/configure-windows/change-server-authentication-mode#use-ssms) | Uwierzytelnianie programu SQL Server jest potrzebne do zdalnego łączenia się z maszyną wirtualną, chyba że skonfigurowano usługę Active Directory w sieci wirtualnej. |
| [Tworzenie logowania SQL](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/create-a-login) | Jeśli używasz uwierzytelniania SQL, potrzebujesz logowania SQL z nazwą użytkownika i hasłem, który ma również uprawnienia do docelowej bazy danych. |
| [Włączanie protokołu TCP/IP](#manualtcp) | Program SQL Server musi zezwalać na połączenia za pośrednictwem protokołu TCP. |
| [Włączanie reguły zapory dla portu programu SQL Server](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) | Zapora na maszynie Wirtualnej musi zezwalać na ruch przychodzący na porcie programu SQL Server (domyślnie 1433). |
| [Tworzenie reguły sieciowej grupy zabezpieczeń dla protokołu TCP 1433](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) | Należy zezwolić maszynie Wirtualnej na odbieranie ruchu na porcie programu SQL Server (domyślnie 1433), jeśli chcesz połączyć się przez Internet. Połączenia lokalne i tylko do sieci wirtualnej nie wymagają tego. Jest to jedyny krok wymagany w witrynie Azure portal. |

> [!TIP]
> Kroki opisane w powyższej tabeli są wykonywane podczas konfigurowania łączności w portalu. Te kroki należy wykonać tylko w celu potwierdzenia konfiguracji lub ręcznej konfiguracji łączności dla programu SQL Server.

## <a name="next-steps"></a>Następne kroki

Aby wyświetlić instrukcje inicjowania obsługi administracyjnej wraz z tymi krokami łączności, zobacz [Inicjowanie obsługi administracyjnej maszyny wirtualnej programu SQL Server na platformie Azure.](virtual-machines-windows-portal-sql-server-provision.md)

Aby zapoznać się z innymi tematami związanymi z uruchamianiem programu SQL Server na maszynach wirtualnych platformy Azure, zobacz [SQL Server na maszynach wirtualnych platformy Azure.](virtual-machines-windows-sql-server-iaas-overview.md)