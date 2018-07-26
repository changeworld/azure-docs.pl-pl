---
title: Usługa Azure App Service — synchronizacja konfiguracji sieci | Dokumentacja firmy Microsoft
description: W tym artykule omówiono sposób synchronizacji konfiguracji sieci dla planu hostingu usługi Azure App Service.
ms.service: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.custom: managed instance
ms.topic: conceptual
ms.date: 03/07/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: 57c4dd523a5dffc48a2d2d403d2a440a8d6cde95
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2018
ms.locfileid: "39257898"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan"></a>Synchronizacja konfiguracji sieci dla planu hostingu usługi Azure App Service

Może się zdarzyć, że chociaż możesz [zintegrować aplikację z usługą Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md), nie można ustanowić połączenia z wystąpieniem zarządzanym. Jest jedyną operacją, której możesz spróbować odświeżyć konfiguracji sieci dla planu usługi. 

## <a name="sync-network-configuration-for-app-service-hosting-plan"></a>Synchronizacja konfiguracji sieci dla usługi App Service plan hostingu

W tym celu wykonaj następujące kroki:  

1. Przejdź do aplikacji sieci web planu usługi App Service.
 
   ![plan usługi app service](./media/sql-database-managed-instance-sync-networking/app-service-plan.png)

2. Kliknij przycisk **sieć** a następnie kliknij przycisk **kliknij tutaj, aby zarządzanie**.
 
   ![plan usługi zarządzania](./media/sql-database-managed-instance-sync-networking/manage-plan.png)

3. Wybierz swoje **sieci wirtualnej** i kliknij przycisk **Synchronizuj sieć**. 
 
   ![Synchronizuj sieć](./media/sql-database-managed-instance-sync-networking/sync.png)

4. Zaczekaj, aż synchronizacja jest wykonywane.
  
   ![Synchronizacja gotowe](./media/sql-database-managed-instance-sync-networking/sync-done.png)

Teraz można przystąpić do spróbuj ponownie nawiązać połączenie do wystąpienia zarządzanego.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać informacje o konfigurowaniu sieci wirtualnej do wystąpienia zarządzanego, zobacz [konfiguracji zarządzanych sieci wirtualnej wystąpienia](sql-database-managed-instance-vnet-configuration.md).
