---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d579e7a4fd83c1a0ce335e0b2357dcbafb217398
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183176"
---
W tym kroku można przetestować odbiornik grupy dostępności przy użyciu aplikacji klienckiej, która jest uruchomiona w tej samej sieci.

Łączność klienta ma następujące wymagania:

* Połączenia klienta z odbiornikiem muszą pochodzić z komputerów, które znajdują się w innej usłudze w chmurze niż ta, która obsługuje repliki dostępności zawsze włączone.
* Jeśli repliki always on znajdują się w różnych podsieciach, klienci muszą określić *MultisubnetFailover=True* w ciągu połączenia. Ten warunek powoduje równoległe próby połączenia replik w różnych podsieciach. Ten scenariusz obejmuje międzyregionów zawsze na dostępność wdrożenia grupy.

Jednym z przykładów jest połączenie z odbiornikiem z jednej z maszyn wirtualnych w tej samej sieci wirtualnej platformy Azure (ale nie jeden, który obsługuje replikę). Łatwym sposobem ukończenia tego testu jest próba połączenia programu SQL Server Management Studio z detektorem grupy dostępności. Inną prostą metodą jest uruchomienie [programu SQLCMD.exe](https://technet.microsoft.com/library/ms162773.aspx)w następujący sposób:

    sqlcmd -S "<ListenerName>,<EndpointPort>" -d "<DatabaseName>" -Q "select @@servername, db_name()" -l 15

> [!NOTE]
> Jeśli wartość EndpointPort wynosi *1433*, nie jest wymagane, aby określić go w wywołaniu. Poprzednie wywołanie zakłada również, że komputer kliencki jest przyłączony do tej samej domeny i że obiekt wywołujący otrzymał uprawnienia do bazy danych przy użyciu uwierzytelniania systemu Windows.
> 
> 

Podczas testowania odbiornika, należy wykonać w stan failover grupy dostępności, aby upewnić się, że klienci mogą łączyć się z odbiornikiem w pracy awaryjnej.

