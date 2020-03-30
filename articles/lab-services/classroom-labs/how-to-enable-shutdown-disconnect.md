---
title: Włączanie zamykania maszyn wirtualnych podczas rozłączania usług Azure Lab | Dokumenty firmy Microsoft
description: Dowiedz się, jak włączyć lub wyłączyć automatyczne zamykanie maszyn wirtualnych po odłączeniu połączenia pulpitu zdalnego.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2019
ms.author: spelluru
ms.openlocfilehash: 68a27a325a0ef02c6eeea9867a21ba0e24ab5321
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77117136"
---
# <a name="enable-automatic-shutdown-of-vms-on-disconnect"></a>Włączanie automatycznego wyłączania maszyn wirtualnych po rozłączeniu
W tym artykule pokazano, jak można włączyć lub wyłączyć automatyczne zamykanie maszyn wirtualnych z laboratorium **systemu Windows 10** (szablon lub student) po odłączeniu połączenia pulpitu zdalnego. Można również określić, jak długo maszyny wirtualne powinny czekać na ponowne połączenie użytkownika przed automatycznym zamknięciem.

Administrator konta laboratorium może skonfigurować to ustawienie dla konta laboratorium, na którym tworzysz laboratoria. Aby uzyskać więcej informacji, zobacz [Konfigurowanie automatycznego zamykania maszyn wirtualnych przy rozłączaniu dla konta laboratoryjnego](how-to-configure-lab-accounts.md#automatic-shutdown-of-vms-on-disconnect). Jako właściciel laboratorium możesz zastąpić to ustawienie podczas tworzenia laboratorium lub po utworzeniu laboratorium. 

## <a name="configure-when-creating-a-lab"></a>Konfigurowanie podczas tworzenia laboratorium
Na stronie kroku 3 kreatora tworzenia laboratorium można włączyć lub wyłączyć tę funkcję, a także określić, jak długo maszyna wirtualna powinna czekać na ponowne połączenie użytkownika przed automatycznym zamknięciem. 

![Konfigurowanie w momencie tworzenia laboratorium](../media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

## <a name="configure-after-the-lab-is-created"></a>Konfigurowanie po utworzeniu laboratorium
To ustawienie można skonfigurować na stronie **Ustawienia,** jak pokazano na poniższej ilustracji: 

![Konfigurowanie po utworzeniu laboratorium](../media/how-to-enable-shutdown-disconnect/configure-lab-automatic-shutdown.png)

> [!WARNING]
> Jeśli system operacyjny Windows (OS) zostanie zamknięty na maszynie wirtualnej przed odłączeniem sesji RDP do maszyny Wirtualnej, funkcja automatycznego wyłączania nie będzie działać poprawnie.  

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [Deska rozdzielcza do laboratoriów szkolnych](use-dashboard.md)