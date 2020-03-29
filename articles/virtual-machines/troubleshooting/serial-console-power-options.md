---
title: Opcje zasilania konsoli szeregowych platformy Azure | Dokumenty firmy Microsoft
description: Opcje zasilania maszyny Wirtualnej dostępne w konsoli szeregowej platformy Azure
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/6/2019
ms.author: alsin
ms.openlocfilehash: 11c2549d7282bae5654ede1ac34d63a43cd0f059
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451199"
---
# <a name="power-options-available-from-the-azure-serial-console"></a>Opcje zasilania dostępne w konsoli szeregowej platformy Azure

Konsola szeregowa platformy Azure udostępnia kilka zaawansowanych narzędzi do zarządzania energią na swojej maszynie wirtualnej lub zestawie skalowania maszyny wirtualnej. Te opcje zarządzania energią mogą być mylące dla niektórych, więc jest to przegląd każdego narzędzia i jego zamierzonego przypadku użycia.

Funkcja konsoli szeregowego | Opis | Przypadek użycia
:----------------------|:------------|:---------
Ponowne uruchamianie maszyny Wirtualnej | Łagodne ponowne uruchomienie wystąpienia zestawu skalowania maszyny wirtualnej lub maszyny wirtualnej. Ta operacja jest taka sama jak wywołanie funkcji ponownego uruchamiania dostępnej na stronie Przegląd. | W większości przypadków ta opcja powinna być pierwszym narzędziem podczas próby ponownego uruchomienia maszyny Wirtualnej. Połączenie konsoli szeregowej spowoduje krótką przerwę i zostanie automatycznie wznowione po ponownym uruchomieniu maszyny wirtualnej.
Resetowanie maszyny wirtualnej | Silny cykl zasilania maszyny wirtualnej lub skali maszyny wirtualnej ustawionej przez platformę Azure. | Ta opcja służy do natychmiastowego ponownego uruchomienia systemu operacyjnego, niezależnie od jego bieżącego stanu. Ponieważ ta operacja nie jest wdzięku, istnieje ryzyko utraty danych lub uszkodzenia. Nie ma przerw w połączeniu konsoli szeregowej, które mogą być przydatne do wysyłania poleceń na początku czasu rozruchu (na przykład, dotarcie do GRUB na maszynie wirtualnej z systemem Linux lub trybie awaryjnym na maszynie Wirtualnej systemu Windows).
SysRq - Ponowne uruchomienie (b) | Żądanie systemowe wymuszanie ponownego uruchomienia gościa. | Ta funkcja ma zastosowanie tylko do systemów operacyjnych Linux i wymaga [włączenia funkcji SysRq](./serial-console-nmi-sysrq.md#system-request-sysrq) w systemie operacyjnym. Jeśli system operacyjny jest poprawnie skonfigurowany dla programu SysRq, to polecenie spowoduje ponowne uruchomienie systemu operacyjnego.
NMI (przerwanie niemaskowalne) | Polecenie przerwania, które zostanie dostarczone do systemu operacyjnego | Ta operacja jest dostępna zarówno dla maszyn wirtualnych [z systemem Windows,](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) jak i [Linux](./serial-console-nmi-sysrq.md#non-maskable-interrupt-nmi) i wymaga włączenia nmi. Wysłanie pliku NMI zazwyczaj spowoduje awarię systemu operacyjnego. Można skonfigurować system operacyjny, aby utworzyć plik zrzutu, a następnie ponownie po otrzymaniu NMI, co może być przydatne w debugowaniu niskiego poziomu.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [maszynach wirtualnych azure serial console dla systemów linux](./serial-console-linux.md)
* Dowiedz się więcej o [platformie Azure Serial Console dla maszyn wirtualnych z systemem Windows](./serial-console-windows.md)