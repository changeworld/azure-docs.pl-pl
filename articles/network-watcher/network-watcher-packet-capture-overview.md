---
title: Wprowadzenie do przechwytywania pakietów w obserwatora sieciowego Azure | Dokumentacja firmy Microsoft
description: Ta strona zawiera przegląd możliwości przechwytywania pakietów obserwatora sieciowego
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 3a81afaa-ecd9-4004-b68e-69ab56913356
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 152cc8fb61aa6115c7b5863e4d798db9e7aa5b7c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23864294"
---
# <a name="introduction-to-variable-packet-capture-in-azure-network-watcher"></a>Wprowadzenie do przechwytywania pakietów zmiennej w obserwatora sieciowego Azure

Przechwytywania pakietów zmiennej obserwatora sieciowego umożliwia tworzenie sesji przechwytywania pakietów, aby śledzić ruch do i z maszyny wirtualnej. Pomaga przechwytywania pakietów do diagnozowania sieci anomalii zarówno rozbudowy i proactivity. Innych celów obejmują zbieranie statystyk sieciowych, uzyskanie informacji na temat wtargnięcia sieci, aby debugować komunikacja klient serwer i o wiele więcej.

Przechwytywania pakietów jest uruchamiana zdalnie za pomocą Monitora sieci rozszerzenia maszyny wirtualnej. Funkcja ta ułatwia obciążeń uruchomionych przechwytywania pakietów ręcznego na odpowiednią maszynę wirtualną, która zapisuje cenny czas. Przechwytywania pakietów mogą być wyzwalane za pośrednictwem portalu, programu PowerShell, interfejsu wiersza polecenia lub interfejsu API REST. Przykład sposobu przechwytywania pakietów mogą być wyzwalane jest z alertami maszyny wirtualnej. Filtry są dostępne dla sesji przechwytywania, aby upewnić się, że Przechwytywanie ruchu, który chcesz monitorować. Filtry są oparte na krotce 5 (protokół, lokalny adres IP zdalnego adresu IP, portu lokalnego i portu zdalnego) informacje. Przechwycone dane są przechowywane w lokalnych dysków lub obiektu blob magazynu. Ma limitu 10 sesji przechwytywania pakietów, na region na subskrypcję. Ten limit ma zastosowanie tylko do sesji i nie ma zastosowania do plików przechwytywania pakietów zapisanych lokalnie na maszynie Wirtualnej lub na koncie magazynu.

> [!IMPORTANT]
> Rozszerzenie maszyny wirtualnej wymaga przechwytywania pakietów `AzureNetworkWatcherExtension`. Instalowanie rozszerzenia na maszynie Wirtualnej systemu Windows można znaleźć [rozszerzenie maszyny wirtualnej Azure sieci obserwatorów agenta dla systemu Windows](../virtual-machines/windows/extensions-nwa.md) i dla maszyny Wirtualnej systemu Linux, odwiedź [rozszerzenie maszyny wirtualnej Azure sieci obserwatorów agenta dla systemu Linux](../virtual-machines/linux/extensions-nwa.md).

Aby ograniczyć liczbę potrzebnych informacji przechwycić informacje, które mają, sesji przechwytywania pakietów są dostępne następujące opcje:

**Przechwytywanie konfiguracji**

|Właściwość|Opis|
|---|---|
|**Maksymalna liczba bajtów na pakietu (w bajtach)** | Liczba bajtów z każdego pakietu, które są przechwytywane, wszystkie bajty są przechwytywane, jeśli pole pozostanie puste. Liczba bajtów z każdego pakietu, które są przechwytywane, wszystkie bajty są przechwytywane, jeśli pole pozostanie puste. Jeśli potrzebujesz tylko w nagłówku IPv4 — wskazuje 34 tutaj |
|**Maksymalna liczba bajtów na sesji (w bajtach)** | Całkowita liczba bajtów w tym są przechwytywane, gdy wartość osiągnięciu zakończenia sesji.|
|**Limit czasu (w sekundach)** | Ustawia czas ograniczenie pakiet przechwytywania sesji. Wartość domyślna to 5 godzin lub 18000 sekundach.|

**Filtrowanie (opcjonalnie)**

|Właściwość|Opis|
|---|---|
|**Protokół** | Protokół, aby filtrować pod kątem przechwytywania pakietów. Dostępne wartości to TCP, UDP i wszystkie.|
|**Lokalny adres IP** | Ta wartość filtry przechwytywania pakietów do pakietów, jeśli lokalny adres IP odpowiada to wartości filtru.|
|**Port lokalny** | Ta wartość filtry przechwytywania pakietów do pakietów gdzie portu lokalnego zgodna z tą wartością filtru.|
|**Zdalny adres IP** | Ta wartość filtry przechwytywania pakietów do pakietów, jeśli zdalny adres IP odpowiada to wartości filtru.|
|**Port zdalny** | Ta wartość filtry przechwytywania pakietów do pakietów Jeśli port zdalny odpowiada to wartości filtru.|

### <a name="next-steps"></a>Następne kroki

Dowiedz się, jak można zarządzać przechwytywania pakietów, za pośrednictwem portalu, odwiedzając [Zarządzanie przechwytywania pakietów w portalu Azure](network-watcher-packet-capture-manage-portal.md) lub przy użyciu programu PowerShell, odwiedzając [Zarządzanie przechwytywania pakietów przy użyciu programu PowerShell](network-watcher-packet-capture-manage-powershell.md).

Informacje o tworzeniu pakietów aktywnego przechwytywania na podstawie maszyny wirtualnej alertów, odwiedzając [utworzyć przechwytywania alertów wyzwalanych pakietów](network-watcher-alert-triggered-packet-capture.md)

<!--Image references-->
[1]: ./media/network-watcher-packet-capture-overview/figure1.png













