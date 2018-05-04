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
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
Następujące ograniczenia mają zastosowanie do usługi Kopia zapasowa Azure.

| Identyfikator limit | Limit domyślny |
| --- | --- |
| Liczba serwerów/maszyny, które mogą być rejestrowane przed każdym magazynu |50 dla systemu Windows Server/klienta/SCDPM <br/> 1000 dla maszyn wirtualnych IaaS |
| Rozmiar źródła danych dla danych przechowywanych w magazynie usługi Azure storage |Maksymalna liczba 54400 GB<sup>1</sup> |
| Liczba magazynów kopii zapasowych, które mogą zostać utworzone w każdej subskrypcji platformy Azure |500 Magazyny usług odzyskiwania dla regionu |
| Liczba kopii zapasowej mogą być planowane na dzień |3 dziennie dla systemu Windows Server/Client <br/> 2 dziennie SCDPM <br/> Raz dziennie dla maszyn wirtualnych IaaS |
| Dyskach danych dołączonych do maszyny wirtualnej platformy Azure do utworzenia kopii zapasowej |16 |
| Rozmiar dysku danych poszczególnych dołączony do maszyny wirtualnej platformy Azure do utworzenia kopii zapasowej| 4095 GB <sup>2</sup>|

* <sup>1</sup>limitu 54400 GB nie ma zastosowania do kopii zapasowej maszyny Wirtualnej IaaS.
 

