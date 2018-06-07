---
title: Usługa aplikacji Azure — konfiguracja sieci synchronizacji | Dokumentacja firmy Microsoft
description: W tym artykule omówiono sposób synchronizacji konfiguracji sieci dla planu hostingu usługi Azure App Service.
ms.service: sql-database
author: srdjan-bozovic
manager: craigg
ms.custom: managed instance
ms.topic: conceptual
ms.date: 03/07/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: ea8f4aae0324e201def6b9b6cd33b0e79042ebbe
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647973"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan"></a>Synchronizowanie konfiguracji sieci dla planu hostingu usługi aplikacji Azure

Go może się zdarzyć, że chociaż możesz [zintegrowanych aplikacji z sieci wirtualnej platformy Azure](../app-service/web-sites-integrate-with-vnet.md), nie można ustanowić połączenia z wystąpieniem zarządzane. Jedyną operacją, której można spróbować jest odświeżanie konfiguracji sieci dla planu obsługi. 

## <a name="sync-network-configuration-for-app-service-hosting-plan"></a>Konfiguracja sieci synchronizacji dla planu hostingu usługi aplikacji

W tym celu wykonaj następujące kroki:  

1. Przejdź do aplikacji sieci web planu usługi aplikacji.
 
   ![plan usługi app service](./media/sql-database-managed-instance-sync-networking/app-service-plan.png)

2. Kliknij przycisk **sieci** , a następnie kliknij przycisk **kliknij tutaj, aby zarządzanie**.
 
   ![plan usługi zarządzania](./media/sql-database-managed-instance-sync-networking/manage-plan.png)

3. Wybierz użytkownika **sieci wirtualnej** i kliknij przycisk **sieci synchronizacji**. 
 
   ![Synchronizacja sieci](./media/sql-database-managed-instance-sync-networking/sync.png)

4. Zaczekaj, aż synchronizacja jest wykonywane.
  
   ![Synchronizacja gotowe](./media/sql-database-managed-instance-sync-networking/sync-done.png)

Teraz można przystąpić do próbować ponownie nawiązać połączenie z instancją zarządzane.

## <a name="next-steps"></a>Kolejne kroki

- Informacji o konfigurowaniu sieci wirtualnej dla wystąpienia zarządzane, zobacz [konfigurację zarządzane sieci wirtualnej wystąpienia](sql-database-managed-instance-vnet-configuration.md).
