---
title: Tworzenie kopii zapasowej usługi App Service w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Szczegółowe wskazówki dotyczące kopii zapasowych w usłudze Azure Stack App Service.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: jeffgilb
ms.reviewer: apwestgarth
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: 66f1f1389a7e4ceebc38544f2eb9836fad2bd96a
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/21/2019
ms.locfileid: "58340552"
---
# <a name="back-up-app-service-on-azure-stack"></a>Tworzenie kopii zapasowej usługi App Service w usłudze Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*  

Ten dokument zawiera instrukcje dotyczące sposobu wykonywania kopii zapasowej usługi App Service w usłudze Azure Stack.

> [!IMPORTANT]
> Usługa App Service w usłudze Azure Stack nie jest obsługiwane w ramach [kopii zapasowej infrastruktury usługi Azure Stack](azure-stack-backup-infrastructure-backup.md). Operator usługi Azure Stack należy wykonać kroki, aby upewnić się, że usługi App Service można pomyślnie przywrócić w razie potrzeby.

Usługa Azure App Service w usłudze Azure Stack ma cztery główne składniki, które należy uwzględnić podczas planowania odzyskiwania po awarii:
1. Infrastruktury dostawcy zasobów. role serwera, warstwy procesu roboczego, itp. 
2. Wpisy tajne usługi App Service
3. Hosting aplikacji usługi SQL Server i bazy danych pomiarów
4. App Service obciążenie użytkownikami zawartość przechowywana w udziale plików usługi App Service   

## <a name="back-up-app-service-secrets"></a>Tworzenie kopii zapasowych wpisów tajnych usługi App Service
Podczas odzyskiwania usługi App Service z kopii zapasowej, należy podać kluczy usługi App Service, używanych przez początkowe wdrożenie. Te informacje powinny być zapisywane, zaraz po pomyślnym wdrożeniu i przechowywane w bezpiecznej lokalizacji usługi App Service. Konfiguracja infrastruktury dostawcy zasobów zostaną odtworzone z kopii zapasowej podczas odzyskiwania przy użyciu usługi App Service recovery poleceń cmdlet programu PowerShell.

Użyj portalu administratora do tworzenia kopii zapasowych kluczy tajnych aplikacji usługi, wykonując następujące czynności: 

1. Zaloguj się do portalu administratora usługi Azure Stack jako administratora usługi.

2. Przejdź do **usługi App Service** -> **wpisów tajnych**. 

3. Wybierz **Pobierz wpisy tajne**.

   ![Pobierz wpisy tajne](./media/app-service-back-up/download-secrets.png)

4. Gdy wpisy tajne gotowe do pobrania, kliknij przycisk **Zapisz** i przechowywać wpisy tajne usługi App Service (**SystemSecrets.JSON**) plik w bezpiecznym miejscu. 

   ![Zapisywanie wpisów tajnych](./media/app-service-back-up/save-secrets.png)

> [!NOTE]
> Powtórz te czynności, za każdym razem, gdy są obracane wpisy tajne usługi App Service.

## <a name="back-up-the-app-service-databases"></a>Tworzenie kopii zapasowej bazy danych usługi App Service
Aby przywrócić usługi App Service, konieczne będzie **Appservice_hosting** i **Appservice_metering** kopie zapasowe bazy danych. Zaleca się przy użyciu planów konserwacji programu SQL Server lub serwera usługi Azure Backup, aby upewnić się, są zapisane bezpiecznie na bieżąco i wykonywanie kopii zapasowych tych baz danych. Jednak są tworzone dowolnej metody zapewnienia regularnych kopii zapasowych SQL mogą być używane.

Ręcznego wykonywania kopii zapasowych tych baz danych, po zalogowaniu się do programu SQL Server, można użyć następujących poleceń programu PowerShell:

  ```powershell
  $s = "<SQL Server computer name>"
  $u = "<SQL Server login>" 
  $p = read-host "Provide the SQL admin password"
  sqlcmd -S $s -U $u -P $p -Q "BACKUP DATABASE appservice_hosting TO DISK = '<path>\hosting.bak'"
  sqlcmd -S $s -U $u -P $p -Q "BACKUP DATABASE appservice_metering TO DISK = '<path>\metering.bak'"
  ```

> [!NOTE]
> Jeśli potrzebujesz do tworzenia kopii zapasowych baz danych SQL AlwaysOn, wykonaj [w instrukcjach](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-backup-on-availability-replicas-sql-server?view=sql-server-2017). 

Po wszystkich baz danych zostały pomyślnie kopii zapasowej, skopiuj pliki z rozszerzeniem bak w bezpiecznym miejscu, wraz z informacjami wpisy tajne usługi App Service.

## <a name="back-up-the-app-service-file-share"></a>Tworzenie kopii zapasowej udziału plików w usłudze App Service
Usługa App Service magazynów dzierżawy informacje o aplikacji w udziale plików. To należy wykonać kopię zapasową na bieżąco wraz z bazy danych usługi App Service, aby tak małą ilością danych, jak to możliwe zostaną utracone, jeśli wymagane jest przywracanie. 

Aby utworzyć kopię zapasową udziału plików usługi App Service zawartości, można użyć usługi Azure Backup Server lub innej metody, które znajdują się regularne kopiowanie udziału plików zawartości do lokalizacji po zapisaniu wszystkich poprzednich informacji odzyskiwania. 

Na przykład te kroki służą używać narzędzia robocopy z sesji konsoli programu Windows PowerShell (nie PowerShell ISE):

```powershell
$source = "<file share location>"
$destination = "<remote backup storage share location>"
net use $destination /user:<account to use to connect to the remote share in the format of domain\username> *
robocopy $source $destination
net use $destination /delete
```

## <a name="next-steps"></a>Kolejne kroki
[Przywracanie usługi App Service w usłudze Azure Stack](app-service-recover.md)