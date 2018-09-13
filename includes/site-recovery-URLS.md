---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 09/12/2018
ms.author: raynew
ms.openlocfilehash: ae8eebf9667f2bc03fdc1082fb38c19c5c645c10
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "44723482"
---
**Nazwa** | **Adres URL komercyjnych**  | **Adres URL dla instytucji rządowych** | **Opis** |
--- | --- | --- | ---
Azure AD | ``login.microsoftonline.com`` | ``login.microsoftonline.us`` | Używane do kontrolowania dostępu i tożsamości zarządzania przy użyciu usługi AAD 
Backup | ``*.backup.windowsazure.com`` | ``*.backup.windowsazure.us`` | Służy do przesyłania danych replikacji i koordynacji 
Replikacja | ``*.hypervrecoverymanager.windowsazure.com`` | ``*.hypervrecoverymanager.windowsazure.us``  | Służy do operacji zarządzania replikacją i koordynacji 
Magazyn | ``*.blob.core.windows.net`` | ``*.blob.core.usgovcloudapi.net``  | Służy do uzyskiwania dostępu do konta magazynu przechowującego zreplikowane dane 
Dane telemetryczne (opcjonalnie) | ``dc.services.visualstudio.com`` | ``dc.services.visualstudio.com`` | Używany do telemetrii 
Synchronizacja czasu | ``time.windows.com`` | ``time.nist.gov`` | Ssed do sprawdzania synchronizacji czasu między systemem i czasem globalnym we wszystkich wdrożeniach.


