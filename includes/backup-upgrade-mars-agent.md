---
title: Uaktualnianie agenta kopii zapasowych platformy Azure
description: W tych informacjach wyjaśniono, dlaczego należy uaktualnić agenta kopii zapasowej platformy Azure i gdzie pobrać uaktualnienie.
services: backup
cloud: ''
suite: ''
author: dcurwin
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 03/03/2020
ms.author: dacurwin
ms.openlocfilehash: bd298f758d6109b908db01dd2ae3b97e5e2f714a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673179"
---
## <a name="upgrade-the-mars-agent"></a>Uaktualnienie agenta MARS

Wersje agenta usług odzyskiwania platformy Microsoft Azure (MARS) poniżej 2.0.9083.0 mają zależność od usługi Azure Access Control. Agent MARS jest również określany jako agent kopii zapasowej platformy Azure.

W 2018 r. firma Microsoft [przeceniła usługę Azure Access Control](../articles/active-directory/azuread-dev/active-directory-acs-migration.md). Od 19 marca 2018 r. wszystkie wersje mars agenta poniżej 2.0.9083.0 wystąpią błędy kopii zapasowej. Aby uniknąć lub rozwiązać problemy związane z wykonywaniem kopii zapasowej, [należy uaktualnić agenta MARS do najnowszej wersji](https://support.microsoft.com/help/4538314/update-for-azure-backup-for-microsoft-azure-recovery-services-agent). Aby zidentyfikować serwery wymagające uaktualnienia programu MARS Agent, wykonaj kroki opisane w [programie Upgrade the Microsoft Azure Recovery Services (MARS).](../articles/backup/upgrade-mars-agent.md)

Agent MARS służy do utworzenia kopii zapasowej plików i folderów oraz danych o stanie systemu na platformie Azure. System Center DPM i Serwer kopii zapasowych platformy Azure używają agenta MARS do tworzenia kopii zapasowych danych na platformie Azure.
