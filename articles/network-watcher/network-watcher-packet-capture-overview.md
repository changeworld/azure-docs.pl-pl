---
title: Wprowadzenie do przechwytywania pakietów w usłudze Azure Network Watcher | Dokumentacja firmy Microsoft
description: Ta strona zawiera omówienie funkcji przechwytywania pakietów usługi Network Watcher
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 3a81afaa-ecd9-4004-b68e-69ab56913356
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 4cfbfc4bed5438ed901fca86d8c2939d3860c68e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64684157"
---
# <a name="introduction-to-variable-packet-capture-in-azure-network-watcher"></a>Wprowadzenie do Przechwytywanie pakietów zmiennych w usłudze Azure Network Watcher

Przechwytywanie pakietów zmiennych obserwatora sieci umożliwia tworzenie sesji przechwytywania pakietów, aby śledzić ruch do i z maszyny wirtualnej. Ułatwia Przechwytywanie pakietów do diagnozowania sieci anomalie zarówno w sposób reaktywny i proactivity. Inne zastosowania obejmują zbierania statystyk sieciowych, uzyskiwanie informacji na temat włamań sieci, debugowanie komunikacja klient serwer i wiele więcej.

Przechwytywanie pakietów jest uruchamiana zdalnie przy użyciu usługi Network Watcher rozszerzenie maszyny wirtualnej. Ta możliwość ułatwia obciążenia ręczne uruchomienie przechwytywania pakietów na odpowiednią maszynę wirtualną, co pozwoli zaoszczędzić cenny czas. Przechwytywanie pakietów mogą być wywoływane za pośrednictwem portalu, programu PowerShell, interfejsu wiersza polecenia lub interfejsu API REST. Jest jednym z przykładów jak przechwytywanie pakietów mogą być wywoływane z alertami maszyny wirtualnej. Filtry są dostarczane dla sesji przechwytywania, aby upewnić się, że Przechwytywanie ruchu sieciowego, który chcesz monitorować. Filtry są oparte na tuple 5 (protokół, lokalny adres IP, zdalny adres IP, portu lokalnego i zdalnego port) informacji. Przechwycone dane są przechowywane w lokalnych dysków lub magazynu obiektów blob. Obowiązuje limit 10 sesji przechwytywania pakietów na region na subskrypcję. To ograniczenie ma zastosowanie tylko do sesji i nie ma zastosowania do plików przechwytywania pakietów zapisanych lokalnie na maszynie Wirtualnej lub na koncie magazynu.

> [!IMPORTANT]
> Przechwytywanie pakietów wymaga rozszerzenia maszyny wirtualnej `AzureNetworkWatcherExtension`. Instalowanie rozszerzenia na maszynie Wirtualnej Windows można znaleźć [Agent usługi Azure Network Watcher rozszerzenie maszyny wirtualnej dla Windows](../virtual-machines/windows/extensions-nwa.md) i maszyny Wirtualnej systemu Linux można znaleźć pod adresem [Agent usługi Azure Network Watcher rozszerzenie maszyny wirtualnej dla systemu Linux](../virtual-machines/linux/extensions-nwa.md).

Aby zmniejszyć przechwycić informacje, które mają informacje, sesji przechwytywania pakietów są dostępne następujące opcje:

**Przechwyć konfigurację**

|Właściwość|Opis|
|---|---|
|**Maksymalna liczba bajtów na pakiet (w bajtach)** | Liczba bajtów z każdego pakietu, które są przechwytywane, wszystkie wartości bajtowe są przechwytywane, jeśli pole pozostanie puste. Liczba bajtów z każdego pakietu, które są przechwytywane, wszystkie wartości bajtowe są przechwytywane, jeśli pole pozostanie puste. Jeśli potrzebujesz tylko w nagłówku IPv4 — wskazują 34 tutaj |
|**Maksymalna liczba bajtów na sesję (w bajtach)** | Całkowita liczba bajtów w tym są przechwytywane, gdy wartość zostanie osiągnięty zakończenia sesji.|
|**Limit czasu (w sekundach)** | Zestawy sesji przechwytywania ograniczenie czasu dla pakietu. Wartość domyślna to 18000 sekund lub 5 godzin.|

**Filtrowanie (opcjonalne)**

|Właściwość|Opis|
|---|---|
|**Protokół** | Protokół, aby filtrować pod kątem przechwytywania pakietów. Dostępne wartości to TCP, UDP i wszystkie.|
|**Lokalny adres IP** | Ta wartość służy do przefiltrowania Przechwytywanie pakietów do pakietów Jeśli lokalny adres IP odpowiada tej wartości filtru.|
|**Port lokalny** | Ta wartość służy do przefiltrowania Przechwytywanie pakietów do pakietów Jeśli port lokalny odpowiada tej wartości filtru.|
|**Zdalny adres IP** | Ta wartość służy do przefiltrowania Przechwytywanie pakietów do pakietów Jeśli zdalny adres IP odpowiada tej wartości filtru.|
|**Port zdalny** | Ta wartość służy do przefiltrowania Przechwytywanie pakietów do pakietów gdy port zdalny zgodna z tą wartością filtru.|

### <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak zarządzać przechwytywania pakietów, za pośrednictwem portalu, odwiedzając [Zarządzanie przechwytywaniem pakietów w witrynie Azure portal](network-watcher-packet-capture-manage-portal.md) lub przy użyciu programu PowerShell, odwiedzając [Zarządzanie przechwytywania pakietów przy użyciu programu PowerShell](network-watcher-packet-capture-manage-powershell.md).

Dowiedz się, jak utworzyć przechwytywania pakietów aktywne, na podstawie maszyny wirtualnej alertów, odwiedzając [tworzenie przechwytywania pakietów wyzwolonych alertów](network-watcher-alert-triggered-packet-capture.md)

<!--Image references-->
[1]: ./media/network-watcher-packet-capture-overview/figure1.png













