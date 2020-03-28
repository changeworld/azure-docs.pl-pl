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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183756"
---
Nazwa | Komercyjny adres URL | Adres URL instytucji rządowych | Opis
--- | --- | --- | ---
Usługa Azure Active Directory | ``login.microsoftonline.com`` | ``login.microsoftonline.us`` | Służy do kontroli dostępu i zarządzania tożsamościami przy użyciu usługi Azure Active Directory. 
Tworzenie kopii zapasowych | ``*.backup.windowsazure.com`` | ``*.backup.windowsazure.us`` | Służy do transferowania i koordynacji danych replikacji.
Replikacja | ``*.hypervrecoverymanager.windowsazure.com`` | ``*.hypervrecoverymanager.windowsazure.us``  | Służy do wykonywania operacji i koordynacji zarządzania replikacją.
Magazyn | ``*.blob.core.windows.net`` | ``*.blob.core.usgovcloudapi.net``  | Służy do uzyskiwania dostępu do konta magazynu przechowującego zreplikowane dane.
Telemetria (opcjonalnie) | ``dc.services.visualstudio.com`` | ``dc.services.visualstudio.com`` | Używane do telemetrii.
Synchronizacja czasu | ``time.windows.com`` | ``time.nist.gov`` | Służy do sprawdzania synchronizacji czasu między czasem systemowym a globalnym we wszystkich wdrożeniach.


