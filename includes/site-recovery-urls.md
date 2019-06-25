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
ms.openlocfilehash: f4cd9cad3813378fcdc3f06e8ab1c28eced4f93c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183756"
---
Name (Nazwa) | Adres URL komercyjnych | Adres URL dla instytucji rządowych | Opis
--- | --- | --- | ---
Usługa Azure Active Directory | ``login.microsoftonline.com`` | ``login.microsoftonline.us`` | Używane do dostępu do kontroli i zarządzania tożsamościami, za pomocą usługi Azure Active Directory. 
Backup | ``*.backup.windowsazure.com`` | ``*.backup.windowsazure.us`` | Służy do transferowania i koordynacji danych replikacji.
Replikacja | ``*.hypervrecoverymanager.windowsazure.com`` | ``*.hypervrecoverymanager.windowsazure.us``  | Służy do wykonywania operacji i koordynacji zarządzania replikacją.
Magazyn | ``*.blob.core.windows.net`` | ``*.blob.core.usgovcloudapi.net``  | Służy do uzyskiwania dostępu do konta magazynu przechowującego zreplikowane dane.
Dane telemetryczne (opcjonalnie) | ``dc.services.visualstudio.com`` | ``dc.services.visualstudio.com`` | Używane dla danych telemetrycznych.
Synchronizacja czasu | ``time.windows.com`` | ``time.nist.gov`` | Używany do sprawdzania synchronizacji czasu między systemem i czasem globalnym we wszystkich wdrożeniach.


