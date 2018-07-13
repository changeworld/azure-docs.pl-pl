---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: backup
author: markgalioto
ms.service: backup
ms.topic: include
ms.date: 2/7/2018
ms.author: trinadhk;sogup
ms.custom: include file
ms.openlocfilehash: 1f41567f7d5dcc9b6536b47ef639ba53abff8ec4
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38756014"
---
Poniższe limity mają zastosowanie do usługi Azure Backup.

| Identyfikator limitu | Limit domyślny |
| --- | --- |
| Liczba serwerów/maszyn, które można zarejestrować w każdym magazynie |50 dla systemu Windows Server/klienta/serwera SCDPM <br/> 1000 maszyn wirtualnych IaaS |
| Rozmiar źródła danych dla danych przechowywanych w magazynie usługi Azure storage |Maksymalna liczba 54400 GB<sup>1</sup> |
| Liczby magazynów kopii zapasowych, które mogą być tworzone w każdej subskrypcji platformy Azure |500 magazynów usługi recovery Services na region |
| Liczba kopii zapasowych można zaplanować na dzień |3 na dzień dla systemu Windows Server/Client <br/> 2 dziennie w przypadku programu SCDPM <br/> Raz dziennie w przypadku maszyn wirtualnych IaaS |
| Dysków danych podłączonych do maszyny wirtualnej platformy Azure do utworzenia kopii zapasowej |16 |
| Rozmiar dysku danych dołączonych do maszyny wirtualnej platformy Azure do utworzenia kopii zapasowej| 4095 GB <sup>2</sup>|

* <sup>1</sup>limitu 54400 GB nie ma zastosowania do maszyn wirtualnych IaaS.
 

