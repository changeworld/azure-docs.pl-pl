---
title: Korzystanie z udziału plików platformy Azure w usłudze Azure Storage | Microsoft Docs
description: Dowiedz się, jak używać udziału plików platformy Azure w systemach Windows i Windows Server.
author: todmccoy
manager: dcscontentpm
ms.service: storage
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: rogarana
ms.subservice: files
services: storage
tags: ''
ms.openlocfilehash: e1e581134b4e3821659c43bb7f55003239594bd9
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233794"
---
# <a name="how-to-recover-a-deleted-storage-account"></a>Jak odzyskać usunięte konto magazynu

Usługa Azure Storage zapewnia odporność na dane za pośrednictwem zautomatyzowanych replik, ale nie zapobiega uszkodzeniu i złośliwemu używaniu danych przez użytkowników ani kod aplikacji. Zachowanie dokładności danych podczas wystąpienia błędu aplikacji lub użytkownika wymaga bardziej zaawansowanych technik, takich jak kopiowanie danych do pomocniczej lokalizacji magazynu z dziennikiem inspekcji.

W poniższej tabeli przedstawiono omówienie zakresu odzyskiwania konta magazynu w zależności od strategii replikacji.

| |LRS|ZRS|GRS|RA-GRS|
|---|---|---|---|---|
|Azure Resource Manager konta magazynu|Yes|Yes|Yes|Yes|
|Konto magazynu klasyczne|Yes|Yes|Yes|Yes|

Zbierz następujące informacje i Zastąp żądanie pomocy technicznej pomoc techniczna firmy Microsoft:

* Nazwa konta magazynu
* Data usunięcia
* Region konta magazynu
* Jak zostało usunięte konto magazynu?
* Jaka metoda została usunięta z konta magazynu? (Portal, PowerShell itd.)

Ważne punkty

* Może to potrwać do 15 dni od momentu usunięcia usługi magazynu w celu wykonania wyrzucania elementów bezużytecznych, dzięki czemu odzyskiwanie kont magazynu może nie być odzyskiwane z umową SLA.
* Pomoc techniczna firmy Microsoft podejmie próbę odzyskania kontenera/konta na podstawie najlepszego wysiłku i nie gwarantuje odzyskania.

> [!NOTE]
> Odzyskiwanie może zakończyć się niepowodzeniem, jeśli konto zostało utworzone na nowo. Jeśli konto zostało już utworzone ponownie, należy najpierw usunąć je przed podjęciem próby odzyskania.
