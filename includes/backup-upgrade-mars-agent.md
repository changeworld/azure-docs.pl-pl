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
ms.openlocfilehash: c22fbcd07286ddffedd8fc2fdc12017b9338d7f7
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161857"
---
## <a name="upgrade-the-mars-agent"></a>Uaktualnianie agenta MARS

Wersje agenta usługi Microsoft Azure Recovery Service (MARS) poniżej 2.0.9083.0 mają zależność od usługi Azure Access Control Service (ACS). Agent MARS jest również nazywany agentem Azure Backup. W 2018 platforma Azure zabyła [przestarzała usługa azure Access Control Service (ACS)](../articles/active-directory/azuread-dev/active-directory-acs-migration.md). Od 19 marca 2018 wszystkie wersje agenta MARS poniżej 2.0.9083.0 będą powodować błędy kopii zapasowych. Aby uniknąć błędów kopii zapasowych, należy [uaktualnić agenta Mars do najnowszej wersji](https://go.microsoft.com/fwlink/?linkid=229525). Aby zidentyfikować serwery wymagające uaktualnienia agenta MARS, postępuj zgodnie z instrukcjami w [blogu dotyczącym tworzenia kopii zapasowych w celu uaktualnienia agentów Mars](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/). Agent MARS służy do tworzenia kopii zapasowych plików i folderów oraz danych o stanie systemu na platformie Azure. Program System Center DPM i Azure Backup Server używają agenta MARS do tworzenia kopii zapasowych danych na platformie Azure.
