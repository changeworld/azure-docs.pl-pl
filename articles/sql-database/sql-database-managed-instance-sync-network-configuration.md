---
title: Usługa Azure App Service — konfiguracja sieci synchronizacji
description: W tym artykule omówiono sposób synchronizacji konfiguracji sieci dla planu hostingowego usługi Azure App Service.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 12/13/2018
ms.openlocfilehash: e7263d6a7716caf9f53e8496c6fb02b6d17b5509
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73687900"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan"></a>Synchronizowanie konfiguracji sieci dla planu hostingowego usługi Azure App Service

Może się zdarzyć, że chociaż [aplikacja została zintegrowana z siecią wirtualną platformy Azure,](../app-service/web-sites-integrate-with-vnet.md)nie można ustanowić połączenia z wystąpieniem zarządzanym. Jedną z rzeczy, które można spróbować jest odświeżenie konfiguracji sieci dla planu usług.

## <a name="sync-network-configuration-for-app-service-hosting-plan"></a>Konfiguracja sieci synchronizacji dla planu hostingu usługi App Service

W tym celu wykonaj następujące kroki:  

1. Przejdź do planu usługi App Service aplikacji internetowych.

   ![plan usługi aplikacji](./media/sql-database-managed-instance-sync-networking/app-service-plan.png)

2. Kliknij pozycję **Sieć,** a następnie kliknij pozycję **Kliknij tutaj, aby zarządzać**.

   ![zarządzanie planem usług](./media/sql-database-managed-instance-sync-networking/manage-plan.png)

3. Wybierz sieć **wirtualną** i kliknij pozycję **Synchronizuj sieć**.

   ![synchronizacja sieci](./media/sql-database-managed-instance-sync-networking/sync.png)

4. Poczekaj, aż synchronizacja zostanie wykonana.
  
   ![synchronizacja wykonana](./media/sql-database-managed-instance-sync-networking/sync-done.png)

Teraz można przystąpić do próby ponownego nawiązania połączenia z wystąpieniem zarządzanym.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje dotyczące konfigurowania sieci wirtualnej dla wystąpienia zarządzanego, zobacz [Architektura sieci wirtualnej wystąpienia zarządzanego](sql-database-managed-instance-connectivity-architecture.md) i [jak skonfigurować istniejącą witrynę wirtualną](sql-database-managed-instance-configure-vnet-subnet.md).
