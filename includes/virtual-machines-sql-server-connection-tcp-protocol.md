---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: c9df9e0812d5c4b5a581d4b7da677623f45f8053
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2018
ms.locfileid: "50227152"
---
1. Po nawiązaniu połączenia z maszyną wirtualną przy użyciu pulpitu zdalnego wyszukaj pozycję **Menedżer konfiguracji**:

    ![Otwieranie Menedżera konfiguracji programu SQL Server](./media/virtual-machines-sql-server-connection-tcp-protocol/sql-server-configuration-manager.png)

1. W Menedżerze konfiguracji programu SQL Server w okienku konsoli rozwiń pozycję **Konfiguracja sieciowa programu SQL Server**.

1. W okienku konsoli kliknij pozycję **Protokoły dla MSSQLSERVER** (domyślna nazwa wystąpienia). W okienku szczegółów kliknij prawym przyciskiem myszy pozycję **TCP** i kliknij pozycję **Włącz**, jeśli nie została jeszcze włączona.

    ![Włączanie protokołu TCP](./media/virtual-machines-sql-server-connection-tcp-protocol/enable-tcp.png)

1. W okienku konsoli kliknij pozycję **Usługi programu SQL Server**. W okienku szczegółów kliknij prawym przyciskiem myszy **programu SQL Server (*nazwa wystąpienia*)** (wystąpienie domyślne to **programu SQL Server (MSSQLSERVER)**), a następnie kliknij przycisk **ponowne uruchomienie**, aby zatrzymać i ponownie uruchom wystąpienie programu SQL Server.

    ![Ponowne uruchamianie aparatu bazy danych](./media/virtual-machines-sql-server-connection-tcp-protocol/restart-sql-server.png)

1. Zamknij Menedżera konfiguracji programu SQL Server.

Aby uzyskać więcej informacji na temat włączana protokołów dla aparatu bazy danych programu SQL Server, zobacz [Włączanie lub wyłączanie protokołu sieciowego serwera](http://msdn.microsoft.com/library/ms191294.aspx).
