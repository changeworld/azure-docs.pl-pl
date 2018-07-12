---
title: Używanie baz danych udostępnionych przez dostawcę zasobu karty bazy danych SQL w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Jak utworzyć i zarządzać nimi aprowizowane za pomocą dostawcy zasobów karta SQL bazy danych SQL
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 2f286c48822956c82f99808092c26f6637be5cb1
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2018
ms.locfileid: "38968830"
---
# <a name="create-sql-databases"></a>Tworzenie bazy danych SQL

Można tworzyć i zarządzać samoobsługowego baz danych w aplikacji portal użytkowników. Użytkownik usługi Azure Stack wymaga subskrypcji przy użyciu oferta, która obejmuje usługę bazy danych SQL.

1. Zaloguj się do [usługi Azure Stack](azure-stack-poc.md) portalu użytkowników.

2. Wybierz **+ nowe** &gt; **dane + magazyn** &gt; **bazy danych SQL Server** &gt; **Dodaj**.

3. W obszarze **Create Database**, wprowadź wymagane informacje, takie jak **Nazwa bazy danych** i **maksymalny rozmiar w Megabajtach**.

   >[!NOTE]
   >Rozmiar bazy danych musi być co najmniej 64 MB, co może zwiększyć po wdrożeniu bazy danych.

   Skonfiguruj inne ustawienia zgodnie z wymaganiami dla danego środowiska.

4. W obszarze **Create Database**, wybierz opcję **jednostki SKU**. W obszarze **wybierz jednostkę SKU**, wybierz jednostkę SKU dla bazy danych.

   ![Utwórz bazę danych](./media/azure-stack-sql-rp-deploy/newsqldb.png)

   >[!NOTE]
   >Ponieważ serwery hostingu są dodawane do usługi Azure Stack, są przydzielone jednostki SKU. Bazy danych są tworzone w puli serwerów w jednostce SKU hosta.

5. Wybierz **logowania**.
6. W obszarze **wybierz identyfikator logowania**, wybierz istniejącą nazwę logowania, lub wybierz **+ Utwórz nowe dane logowania**.
7. W obszarze **nowy identyfikator logowania**, wprowadź nazwę dla **logowania do bazy danych** i **hasło**.

   >[!NOTE]
   >Te ustawienia są poświadczenia uwierzytelniania programu SQL, które są tworzone dla dostępu do tej bazy danych tylko. Nazwa użytkownika logowania musi być globalnie unikatowa. Można ponownie użyć ustawienia logowania dla innych baz danych, które używają tej samej jednostki SKU.

   ![Utwórz nowe nazwy logowania bazy danych](./media/azure-stack-sql-rp-deploy/create-new-login.png)

8. Wybierz **OK** w celu zakończenia wdrażania bazy danych.

W obszarze **Essentials**, która została przedstawiona po wdrożeniu bazy danych, zwróć uwagę na **parametry połączenia**. Można użyć tego ciągu w każdej aplikacji, która wymaga dostępu do bazy danych programu SQL Server.

![Pobieranie parametrów połączenia](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="sql-always-on-databases"></a>Bazy danych SQL Always On

Zgodnie z projektem zawsze włączone bazy danych są obsługiwane inaczej niż w środowisku serwerów autonomicznych. Aby uzyskać więcej informacji, zobacz [wprowadzenie do programu SQL Server zawsze włączonych grup dostępności na maszynach wirtualnych Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview).

### <a name="verify-sql-always-on-databases"></a>Sprawdź baz danych SQL Always On

Poniższy zrzut ekranu pokazuje, jak można użyć programu SQL Server Management Studio do wzięcia pod stan bazy danych w SQL Always On.

![Stan bazy danych (AlwaysOn)](./media/azure-stack-sql-rp-deploy/verifyalwayson.png)

Zawsze włączone bazy danych należy Pokaż jako Synchronized i jest dostępny w wszystkich wystąpień programu SQL i są wyświetlane w grupy dostępności. Na poprzednim zrzucie ekranu przykładu bazy danych jest newdb1 i jego stan to **newdb1 (synchronizowany)**.

### <a name="delete-an-alwayson-database"></a>Usuń bazę danych (AlwaysOn)

Jeśli usuniesz bazę danych SQL AlwaysOn od dostawcy zasobów SQL usuwa bazę danych z repliki podstawowej i z grupy dostępności.

SQL następnie przełącza bazy danych w stanie przywracania na innych replik i nie porzucenia bazy danych, chyba że wyzwolone. Jeśli baza danych nie została usunięta, repliki pomocnicze przechodzi w stan nie można zsynchronizować.

## <a name="next-steps"></a>Kolejne kroki

[Obsługa dostawcy zasobów programu SQL Server](azure-stack-sql-resource-provider-maintain.md)
