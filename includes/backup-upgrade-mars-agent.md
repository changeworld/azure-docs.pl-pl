---
title: Uaktualnij agenta Azure Backup
description: Te informacje wyjaśniają, dlaczego należy uaktualnić agenta Azure Backup i skąd należy pobrać uaktualnienie.
services: backup
cloud: ''
suite: ''
author: dcurwin
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 8/29/2018
ms.author: dacurwin
ms.openlocfilehash: 227ec87081817b273b66684e31a0ebd0e11134a0
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197128"
---
## <a name="upgrade-the-mars-agent"></a>Uaktualnianie agenta MARS

Wersje agenta Microsoft Azure Recovery Services (MARS) poniżej 2.0.9083.0 mają zależność od usługi Azure Access Control. Agent MARS jest również nazywany agentem Azure Backup.

W 2018 firma Microsoft [zaprzestarzała usługę Access Control platformy Azure](../articles/active-directory/azuread-dev/active-directory-acs-migration.md). Od 19 marca 2018 wszystkie wersje agenta MARS poniżej 2.0.9083.0 będą powodować błędy kopii zapasowych. Aby uniknąć błędów kopii zapasowych, należy [uaktualnić agenta Mars do najnowszej wersji](https://go.microsoft.com/fwlink/?linkid=229525). Aby zidentyfikować serwery wymagające uaktualnienia agenta MARS, postępuj zgodnie z instrukcjami w [blogu dotyczącym tworzenia kopii zapasowych w celu uaktualnienia agentów Mars](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/).

Agent MARS służy do tworzenia kopii zapasowych plików i folderów oraz danych stanu systemu na platformie Azure. Program System Center DPM i Azure Backup Server używają agenta MARS do tworzenia kopii zapasowych danych na platformie Azure.
