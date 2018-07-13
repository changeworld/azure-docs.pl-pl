---
title: Uaktualnienie agenta usługi Azure Backup
description: Te informacje wyjaśnia, dlaczego należy uaktualnić agenta usługi Azure Backup i skąd pobrać uaktualnienia.
services: backup
cloud: ''
suite: ''
author: markgalioto
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 3/16/2018
ms.author: markgal
ms.openlocfilehash: 4142f88c3ccb376acbdd2af07c5cfdf8fd275780
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38750334"
---
## <a name="upgrade-the-mars-agent"></a>Uaktualnij agenta usług MARS
Wersje agenta usługi Microsoft Azure Recovery Service (MARS) poniżej 2.0.9083.0 są zależne od Azure Access Control service (ACS). W 2018 roku będą Azure [wycofana Azure Access Control service (ACS)](../articles/active-directory/develop/active-directory-acs-migration.md). Począwszy od 19 marca 2018 r., wszystkie wersje agenta usług MARS poniżej 2.0.9083.0 doświadczy niepowodzeniami tworzenia kopii zapasowych. Aby uniknąć lub rozwiązać niepowodzeniami tworzenia kopii zapasowych [uaktualniania agenta usług MARS do najnowszej wersji](https://go.microsoft.com/fwlink/?linkid=229525). Aby zidentyfikować serwery, które wymagają aktualizacji agenta usług MARS, wykonaj kroki opisane w [blogu kopii zapasowej dla uaktualnienie agentów usługi Azure Backup](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/). Agenta usług MARS jest używany do tworzenia kopii zapasowych następujących typów danych na platformę Azure:
- Pliki 
- Dane o stanie systemu
- Tworzenie kopii zapasowych programu System Center DPM na platformie Azure
- Azure Backup Server (MABS)
