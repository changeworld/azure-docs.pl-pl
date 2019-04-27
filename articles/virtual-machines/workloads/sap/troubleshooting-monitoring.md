---
title: Monitorowanie środowiska SAP Hana na platformie Azure (duże wystąpienia) | Dokumentacja firmy Microsoft
description: Monitorowanie platformy SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f046304121e0aed8efa1bbc2535d34186eba3496
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60713711"
---
# <a name="how-to-monitor-sap-hana-large-instances-on-azure"></a>Jak monitorować platformy SAP HANA (duże wystąpienia) na platformie Azure

Oprogramowanie SAP HANA na platformie Azure (duże wystąpienia) nie różni się od innych wdrożeń IaaS — potrzebne do monitorowania systemu operacyjnego i aplikacji działania i jak aplikacji korzystanie z nich następujące zasoby:

- Procesor CPU
- Memory (Pamięć)
- Przepustowość sieci
- Miejsce na dysku

Usługa Azure Virtual Machines należy ustalić, czy są wystarczające klasy zasobów o nazwie powyżej lub uzyskać ich wykorzystania. Poniżej przedstawiono bardziej szczegółowo każdy z różnych klas:

**Użycie zasobów Procesora:** Współczynnik SAP zdefiniowane dla niektórych obciążeń względem HANA jest wymuszana aby upewnić się, że powinny być wystarczająco dużo zasobów procesora CPU do pracy za pomocą danych, która jest przechowywana w pamięci. Niemniej jednak może być przypadki, w którym HANA zużywa wiele procesorów CPU, wykonywanie zapytań z powodu braku indeksów lub podobne problemy. Oznacza to, że należy monitorować użycie zasobów procesora CPU jednostki dużych wystąpień HANA, a także zasobów Procesora używanych przez określonych usług platformy HANA.

**Zużycie pamięci:** Ważne jest, aby monitorować z poziomu w ramach platformy HANA, a także poza HANA w jednostce. W ramach platformy HANA monitorować, jak dane zużywa przydzielonej pamięci w celu pozostania w wytycznych rozmiaru wymaganego oprogramowania SAP HANA. Możesz także monitorować użycie pamięci na poziomie duże wystąpienie, aby upewnić się, że dodatkowe zainstalowanych innych — oprogramowanie HANA nie zużywa zbyt dużej ilości pamięci, a w związku z tym rywalizować z platformy HANA w pamięci.

**Przepustowość sieci:** Brama sieci wirtualnej platformy Azure ma ograniczoną przepustowość danych wysyłanych do sieci wirtualnej platformy Azure, więc warto monitorować dane odebrane przez wszystkie maszyny wirtualne platformy Azure w sieci wirtualnej, aby ustalić, jak blisko są limitów wybranej jednostki SKU bramy usługi Azure. W jednostce dużych wystąpień HANA sensowne przychodzący i wychodzący ruch sieciowy oraz monitorowania i do śledzenia woluminy, które są obsługiwane wraz z upływem czasu.

**Miejsce na dysku:** Użycie miejsca na dysku zwykle zwiększa się wraz z upływem czasu. Najbardziej typowe przyczyny to: zwiększa się ilość danych, wykonywania kopii zapasowej dziennika transakcji, zapisywanie plików śledzenia i wykonywanie migawek magazynu. W związku z tym należy monitorować użycie miejsca na dysku i zarządzać miejscem na dysku, skojarzony z jednostką dużych wystąpień HANA.

Aby uzyskać **jednostek SKU typu II** dużych wystąpień HANA, serwer jest dostarczany za pomocą narzędzi diagnostycznych załadowanych systemu. Możesz użyć tych narzędzi diagnostycznych sprawdzania kondycji systemu. Uruchom następujące polecenie w celu wygenerowania kondycji Sprawdź plik dziennika na /var/log/health_check.
```
/opt/sgi/health_check/microsoft_tdi.sh
```
Podczas pracy z zespołu usługi Microsoft Support w taki sposób, aby rozwiązać problem, możesz również może poproszony o podanie plików dziennika za pomocą tych narzędzi diagnostycznych. Można skompresować plik przy użyciu następującego polecenia.
```
tar  -czvf health_check_logs.tar.gz /var/log/health_check
```

**Następne kroki**

- Zapoznaj się [monitorowanie platformy SAP HANA (duże wystąpienia) na platformie Azure](troubleshooting-monitoring.md).