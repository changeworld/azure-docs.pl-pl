---
title: Monitorowanie sap HANA na platformie Azure (duże wystąpienia) | Dokumenty firmy Microsoft
description: Monitoruj sap HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 50a6b4f15a7de02533e3bb51e5659f7b4c078b40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617291"
---
# <a name="how-to-monitor-sap-hana-large-instances-on-azure"></a>Jak monitorować SAP HANA (duże wystąpienia) na platformie Azure

SAP HANA na platformie Azure (duże wystąpienia) nie różni się od innych wdrożeń IaaS — należy monitorować, co system operacyjny i aplikacja robi i jak aplikacje zużywają następujące zasoby:

- Procesor CPU
- Memory (Pamięć)
- Przepustowość sieci
- Miejsce na dysku

Za pomocą platformy Azure Virtual Machines, należy dowiedzieć się, czy klasy zasobów o nazwie powyżej są wystarczające lub są one wyczerpane. Oto więcej szczegółów na temat każdej z różnych klas:

**Zużycie zasobów procesora CPU:** Stosunek, który SAP zdefiniowane dla niektórych obciążeń względem HANA jest wymuszane, aby upewnić się, że powinno być wystarczająco dużo zasobów procesora CPU dostępne do pracy za pośrednictwem danych, które są przechowywane w pamięci. Niemniej jednak mogą wystąpić przypadki, w których HANA zużywa wiele procesorów wykonujących kwerendy z powodu brakujących indeksów lub podobnych problemów. Oznacza to, że należy monitorować zużycie zasobów procesora CPU jednostki wystąpienia dużych HANA, a także zasobów procesora CPU zużywanych przez określone usługi HANA.

**Zużycie pamięci:** Ważne jest, aby monitorować z wewnątrz HANA, jak również poza HANA na urządzeniu. W ramach hana, monitorować, jak dane zużywają hana przydzielonej pamięci w celu pozostania w ramach wymaganych wytycznych dotyczących rozmiaru SAP. Należy również monitorować zużycie pamięci na poziomie wystąpienia dużych, aby upewnić się, że dodatkowe zainstalowane oprogramowanie nie-HANA nie zużywa zbyt dużo pamięci i dlatego konkurować z HANA dla pamięci.

**Przepustowość sieci:** Brama sieci wirtualnej platformy Azure jest ograniczona przepustowością danych przenoszących się do sieci wirtualnej platformy Azure, dlatego warto monitorować dane odebrane przez wszystkie maszyny wirtualne platformy Azure w sieci wirtualnej, aby dowiedzieć się, jak blisko jesteś do granic wybranej jednostki SKU bramy platformy Azure. W jednostce dużych wystąpień HANA ma sens monitorowanie przychodzącego i wychodzącego ruchu sieciowego oraz śledzenie woluminów, które są obsługiwane w czasie.

**Miejsce na dysku:** Zużycie miejsca na dysku zwykle wzrasta wraz z czasem. Najczęstsze przyczyny to: zwiększenie ilości danych, wykonywanie kopii zapasowych dziennika transakcji, przechowywanie plików śledzenia i wykonywanie migawek magazynu. W związku z tym ważne jest, aby monitorować użycie miejsca na dysku i zarządzać miejscem na dysku skojarzonym z jednostką dużych wystąpień HANA.

Dla **jednostek SKU typu II** wystąpienia dużych hana serwer jest dostarczany z fabrycznie załadowanych narzędzi diagnostycznych systemu. Można wykorzystać te narzędzia diagnostyczne do przeprowadzenia kontroli kondycji systemu. Uruchom następujące polecenie, aby wygenerować plik dziennika sprawdzania kondycji w /var/log/health_check.
```
/opt/sgi/health_check/microsoft_tdi.sh
```
Podczas pracy z zespołem pomocy technicznej firmy Microsoft w celu rozwiązania problemu może być również monit o podanie plików dziennika przy użyciu tych narzędzi diagnostycznych. Plik można skompresować za pomocą następującego polecenia.
```
tar  -czvf health_check_logs.tar.gz /var/log/health_check
```

**Następne kroki**

- Zapoznaj się [z instrukcjami monitorowania sap HANA (dużych wystąpień) na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-monitor-troubleshoot).
