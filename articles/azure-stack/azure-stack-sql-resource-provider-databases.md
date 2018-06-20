---
title: Przy użyciu baz danych dostarczonych przez dostawcę zasobów karty SQL Azure stosu | Dokumentacja firmy Microsoft
description: Jak utworzyć i zarządzać udostępnione przy użyciu dostawcy zasobów karty SQL bazy danych SQL
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
ms.openlocfilehash: a82db16f2012672f6e2669f2fd8198b177f501f3
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264186"
---
# <a name="create-sql-databases"></a>Tworzenie bazy danych SQL

Można utworzyć i zarządzać nimi Samoobsługowe baz danych w aplikacji portal użytkowników. Użytkownik Azure stos musi subskrypcji z ofertę, która obejmuje usługę bazy danych SQL.

1. Zaloguj się do [stosu Azure](azure-stack-poc.md) portal użytkowników.

2. Wybierz **+ nowy** &gt; **dane i Magazyn "** &gt; **bazy danych programu SQL Server** &gt; **dodać**.

3. W obszarze **Create Database**, wprowadź wymagane informacje, takie jak **Nazwa bazy danych** i **maksymalny rozmiar w MB**.

   >[!NOTE]
   >Rozmiar bazy danych musi być co najmniej 64 MB, co może zwiększyć po wdrożeniu bazy danych.

   Skonfiguruj inne ustawienia zgodnie z wymaganiami dla danego środowiska.

4. W obszarze **Create Database**, wybierz pozycję **SKU**. W obszarze **wybierz jednostki SKU**, wybierz jednostki SKU dla Twojej bazy danych.

   ![Utwórz bazę danych](./media/azure-stack-sql-rp-deploy/newsqldb.png)

   >[!NOTE]
   >Zgodnie z serwerami hostingu są dodawane do stosu Azure, są przydzielone jednostki SKU. Bazy danych są tworzone w puli serwerów w jednostce SKU hosta.

5. Wybierz **logowania**.
6. W obszarze **wybierz nazwę logowania**, wybierz istniejące dane logowania, lub wybierz **+ Utwórz nowe dane logowania**.
7. W obszarze **nowe dane logowania**, wprowadź nazwę **nazwy logowania bazy danych** i **hasło**.

   >[!NOTE]
   >Te ustawienia są utworzonego dla dostępu do tej bazy danych tylko poświadczenia uwierzytelniania SQL. Nazwa użytkownika logowania musi być globalnie unikatowe. Można ponownie użyć ustawień logowania dla innych baz danych, które używają tej samej jednostki SKU.

   ![Utwórz nowe nazwy logowania bazy danych](./media/azure-stack-sql-rp-deploy/create-new-login.png)

8. Wybierz **OK** do zakończenia wdrażania bazy danych.

W obszarze **Essentials**, który jest wyświetlany po wdrożeniu bazy danych, zwróć uwagę na **ciąg połączenia**. Te parametry można użyć w dowolnej aplikacji, który ma dostęp do bazy danych programu SQL Server.

![Parametry połączenia](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="sql-always-on-databases"></a>Bazy danych SQL AlwaysOn

Zgodnie z projektem zawsze włączone bazy danych są obsługiwane inaczej niż w środowisku serwerów autonomicznych. Aby uzyskać więcej informacji, zobacz [wprowadzenie do programu SQL Server zawsze włączone grupy dostępności na maszynach wirtualnych Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview).

### <a name="verify-sql-always-on-databases"></a>Sprawdź SQL zawsze włączone bazy danych

Następujące przechwytywania ekranu pokazuje, jak za pomocą programu SQL Server Management Studio aby przyjrzeć się stan bazy danych SQL zawsze na.

![Stan bazy danych (AlwaysOn)](./media/azure-stack-sql-rp-deploy/verifyalwayson.png)

Zawsze włączone bazy danych należy Pokaż jako Synchronized i dostępny we wszystkich wystąpieniach programu SQL i są wyświetlane w grupy dostępności. W poprzednim zrzucie ekranu, przykładowe bazy danych jest newdb1 i jego stan jest **newdb1 (synchronizowany)**.

### <a name="delete-an-alwayson-database"></a>Usuń bazę danych (AlwaysOn)

Po usunięciu bazy danych funkcji SQL AlwaysOn od dostawcy zasobów SQL usuwa bazy danych z repliki podstawowej i z grupy dostępności.

Następnie SQL umieszcza na innych replik w stanie przywracania bazy danych i nie porzucenia bazy danych, chyba że wyzwolone. Jeśli bazy danych nie została usunięta, replikach pomocniczych przejdź do stanu Not Synchronizing.

## <a name="next-steps"></a>Kolejne kroki

[Obsługa dostawcy zasobów programu SQL Server](azure-stack-sql-resource-provider-maintain.md)
