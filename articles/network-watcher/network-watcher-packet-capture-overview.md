---
title: Wprowadzenie do przechwytywania pakietów w usłudze Azure Network Watcher | Dokumenty firmy Microsoft
description: Ta strona zawiera omówienie możliwości przechwytywania pakietów Obserwatora sieci
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 1f17463125cead64bd58a2d07e53eee4d3cfcd70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840812"
---
# <a name="introduction-to-variable-packet-capture-in-azure-network-watcher"></a>Wprowadzenie do przechwytywania pakietów zmiennych w usłudze Azure Network Watcher

Przechwytywanie pakietów zmiennych Obserwatora sieciowego umożliwia tworzenie sesji przechwytywania pakietów w celu śledzenia ruchu do i z maszyny wirtualnej. Przechwytywanie pakietów pomaga diagnozować anomalie sieci zarówno reaktywnie, jak i proaktywnie. Inne zastosowania obejmują zbieranie statystyk sieciowych, uzyskiwanie informacji o włamaniach do sieci, debugowanie komunikacji klient-serwer i wiele więcej.

Przechwytywanie pakietów to rozszerzenie maszyny wirtualnej, które jest uruchamiane zdalnie za pośrednictwem funkcji Kontrola sieci. Ta funkcja zmniejsza obciążenie związane z ręcznym uruchamianiem przechwytywania pakietów na żądanej maszynie wirtualnej, co pozwala zaoszczędzić cenny czas. Przechwytywanie pakietów można wyzwolić za pośrednictwem portalu, programu PowerShell, interfejsu wiersza polecenia lub interfejsu API REST. Jednym z przykładów, jak można wyzwolić przechwytywanie pakietów, są alerty maszyny wirtualnej. Filtry są dostępne dla sesji przechwytywania, aby upewnić się, że przechwytujesz ruch, który chcesz monitorować. Filtry są oparte na 5-krotki (protokół, lokalny adres IP, zdalny adres IP, port lokalny i port zdalny) informacje. Przechwycone dane są przechowywane na dysku lokalnym lub w obiekcie blob magazynu. Istnieje limit 10 sesji przechwytywania pakietów na region na subskrypcję. Ten limit dotyczy tylko sesji i nie dotyczy zapisanych plików przechwytywania pakietów lokalnie na maszynie wirtualnej lub na koncie magazynu.

> [!IMPORTANT]
> Przechwytywanie pakietów wymaga `AzureNetworkWatcherExtension`rozszerzenia maszyny wirtualnej . Aby zainstalować rozszerzenie na maszynie Wirtualnej systemu Windows odwiedź [rozszerzenie maszyny wirtualnej usługi Azure Network Watcher Agent dla systemu Windows](../virtual-machines/windows/extensions-nwa.md) i maszyny wirtualnej z systemem Linux, odwiedź rozszerzenie maszyny [wirtualnej usługi Azure Network Watcher Agent dla systemu Linux](../virtual-machines/linux/extensions-nwa.md).

Aby ograniczyć przechwytywanie informacji tylko do żądanych informacji, dla sesji przechwytywania pakietów dostępne są następujące opcje:

**Konfiguracja przechwytywania**

|Właściwość|Opis|
|---|---|
|**Maksymalna liczba bajtów na pakiet (bajty)** | Liczba bajtów z każdego pakietu, które są przechwytywane, wszystkie bajty są przechwytywane, jeśli pozostanie puste. Liczba bajtów z każdego pakietu, które są przechwytywane, wszystkie bajty są przechwytywane, jeśli pozostanie puste. Jeśli potrzebujesz tylko nagłówka IPv4 – wskaż 34 tutaj |
|**Maksymalna liczba bajtów na sesję (bajty)** | Całkowita liczba bajtów, które są przechwytywane, po osiągnięciu wartości kończy się sesja.|
|**Limit czasu (w sekundach)** | Ustawia ograniczenie czasowe sesji przechwytywania pakietów. Wartość domyślna to 18000 sekund lub 5 godzin.|

**Filtrowanie (opcjonalnie)**

|Właściwość|Opis|
|---|---|
|**Protokół** | Protokół do filtrowania przechwytywania pakietów. Dostępne wartości to TCP, UDP i All.|
|**Lokalny adres IP** | Ta wartość filtruje przechwytywanie pakietów do pakietów, w których lokalny adres IP odpowiada tej wartości filtru.|
|**Port lokalny** | Ta wartość filtruje przechwytywanie pakietów do pakietów, w których port lokalny odpowiada tej wartości filtru.|
|**Zdalny adres IP** | Ta wartość filtruje przechwytywanie pakietów do pakietów, w których zdalny adres IP odpowiada tej wartości filtru.|
|**Port zdalny** | Ta wartość filtruje przechwytywanie pakietów do pakietów, w których port zdalny odpowiada tej wartości filtru.|

### <a name="next-steps"></a>Następne kroki

Dowiedz się, jak zarządzać przechwytywaniem pakietów za pośrednictwem portalu, odwiedzając pozycję [Zarządzaj przechwytywaniem pakietów w portalu Azure](network-watcher-packet-capture-manage-portal.md) lub za pomocą programu PowerShell, odwiedzając [witrynę Zarządzaj przechwytywaniem pakietów za pomocą programu PowerShell](network-watcher-packet-capture-manage-powershell.md).

Dowiedz się, jak tworzyć proaktywne przechwytywanie pakietów na podstawie alertów maszyn wirtualnych, odwiedzając pozycję [Tworzenie wyzwalanego alertu przechwytywania pakietów](network-watcher-alert-triggered-packet-capture.md)

<!--Image references-->
[1]: ./media/network-watcher-packet-capture-overview/figure1.png













