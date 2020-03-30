---
title: W jaki sposób odzyskać usunięte konto magazynu
description: Dowiedz się, jak odzyskać usunięte konto magazynu
author: todmccoy
manager: dcscontentpm
ms.service: storage
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: rogarana
ms.subservice: files
services: storage
tags: ''
ms.openlocfilehash: 05465d4a03335ac607ba8981116c66fd6dac9416
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252629"
---
# <a name="how-to-recover-a-deleted-storage-account"></a>W jaki sposób odzyskać usunięte konto magazynu

Usługa Azure Storage zapewnia odporność danych za pośrednictwem replik automatycznych, ale nie uniemożliwia użytkownikom lub kodowi aplikacji uszkodzenia danych, przypadkowo lub złośliwie. Zachowanie wierności danych podczas wystąpień błędu aplikacji lub użytkownika wymaga bardziej zaawansowanych technik, takich jak kopiowanie danych do dodatkowej lokalizacji magazynu za pomocą dziennika inspekcji.

Poniższa tabela zawiera omówienie zakresu odzyskiwania konta magazynu w zależności od strategii replikacji.

| |LRS|ZRS|GRS|RA - GRS|
|---|---|---|---|---|
|Menedżer zasobów konta magazynu usługi Azure|Tak|Tak|Tak|Tak|
|Klasyczne konto magazynu|Tak|Tak|Tak|Tak|

Zbierz następujące informacje i złóż żądanie pomocy technicznej za pomocą pomocy technicznej firmy Microsoft:

* Nazwa konta magazynu
* Data usunięcia
* Region konta magazynu
* W jaki sposób usunięto konto magazynu?
* Jaka metoda usunęła konto magazynu? (Portal, PowerShell itp.)

Ważne punkty

* Zazwyczaj może upłynąć do 15 dni od momentu usunięcia dla usługi magazynu do wykonywania wyrzucania elementów bezużytecznych, więc odzyskiwanie kont magazynu nie mogą być odzyskane za pomocą umowy SLA.
* Pomoc techniczna firmy Microsoft podejmie próbę odzyskania kontenera/konta na zasadzie najlepszego wysiłku i nie może zagwarantować odzyskania.

> [!NOTE]
> Odzyskiwanie może nie zakończyć się pomyślnie, jeśli konto zostało ponownie utworzone. Jeśli konto zostało już ponownie utworzone, należy je najpierw usunąć przed podjęciem próby odzyskania.
