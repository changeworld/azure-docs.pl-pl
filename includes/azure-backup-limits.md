---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: backup
author: markgalioto
ms.service: backup
ms.topic: include
ms.date: 9/10/2018
ms.author: trinadhk;sogup
ms.custom: include file
ms.openlocfilehash: 64101ea5a3bbaac4a6b2e349a04d06ea84a87081
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2018
ms.locfileid: "44381104"
---
Poniższe limity mają zastosowanie do usługi Azure Backup.

| Identyfikator limitu | Limit domyślny |
| --- | --- |
| Liczba serwerów/maszyn, które można zarejestrować w każdym magazynie |50 dla systemu Windows Server/klienta/serwera SCDPM <br/> 1000 maszyn wirtualnych IaaS |
| Rozmiar źródła danych dla danych przechowywanych w magazynie usługi Azure storage |Maksymalna liczba 54400 GB<sup>1</sup> |
| Liczby magazynów kopii zapasowych, które mogą być tworzone w każdej subskrypcji platformy Azure |500 magazynów usługi recovery Services na region |
| Liczba kopii zapasowych można zaplanować na dzień |3 na dzień dla systemu Windows Server/Client <br/> 2 dziennie w przypadku programu SCDPM <br/> Raz dziennie w przypadku maszyn wirtualnych IaaS |
| Dysków danych podłączonych do maszyny wirtualnej platformy Azure do utworzenia kopii zapasowej |16 |
| Rozmiar dysku danych dołączonych do maszyny wirtualnej platformy Azure do utworzenia kopii zapasowej| 4095 GB|

* <sup>1</sup>limitu 54400 GB nie ma zastosowania do maszyn wirtualnych IaaS.
 

