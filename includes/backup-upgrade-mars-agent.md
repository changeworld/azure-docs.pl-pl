---
title: Uaktualnij agenta usługi Kopia zapasowa Azure
description: Te informacje wyjaśnia, dlaczego należy uaktualnić agenta usługi Kopia zapasowa Azure i skąd pobrać uaktualnienia.
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
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/17/2018
ms.locfileid: "29973303"
---
## <a name="upgrade-the-mars-agent"></a>Uaktualnij agenta MARS
Wersje agenta usług odzyskiwania Azure firmy Microsoft (MARS) poniżej 2.0.9083.0 zależy od usługi Azure kontroli dostępu (ACS). W 2018 Azure będzie [zastąpić usługę Azure kontroli dostępu (ACS)](../articles/active-directory/develop/active-directory-acs-migration.md). Począwszy od 19 marca 2018 wszystkie wersje agenta MARS poniżej 2.0.9083.0 wystąpi niepowodzenie wykonywania kopii zapasowej. Aby uniknąć lub rozwiązać błędy kopii zapasowych [uaktualnienia do najnowszej wersji agenta MARS](https://go.microsoft.com/fwlink/?linkid=229525). Aby zidentyfikować serwery, które wymagają uaktualnienia agenta MARS, postępuj zgodnie z instrukcjami [blogu kopii zapasowej do uaktualniania agentów kopia zapasowa Azure](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/). Agenta MARS jest używana do utworzyć kopię zapasową następujących typów danych do platformy Azure:
- Pliki 
- Dane o stanie systemu
- Tworzenie kopii zapasowych programu System Center DPM na platformie Azure
- Azure Backup Server (MABS)
