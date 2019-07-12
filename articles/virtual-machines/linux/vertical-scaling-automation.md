---
title: Skalowanie w pionie maszyn wirtualnych platformy Azure z usługą Azure Automation | Dokumentacja firmy Microsoft
description: Jak skalowanie w pionie maszyny wirtualnej systemu Linux w odpowiedzi na monitorowanie alertów w usłudze Azure Automation
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: dcee199e-fa25-44d5-9b25-df564cee9b45
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/18/2019
ms.author: kasing
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e0317344fd8ee1eb415b61d4f5035219e649b18d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67695476"
---
# <a name="vertically-scale-azure-linux-virtual-machine-with-azure-automation"></a>Skalowanie w pionie maszyny wirtualnej systemu Linux platformy Azure z usługą Azure Automation
Skalowanie w pionie polega na zwiększenie lub zmniejszenie zasoby maszyny w odpowiedzi na obciążenie. Na platformie Azure można to zrobić, zmieniając rozmiar maszyny wirtualnej. Może to pomóc w następujących scenariuszach

* Jeśli maszyna wirtualna nie jest często używane, należy zmienić jego rozmiar do mniejszego rozmiaru, aby zmniejszyć koszty miesięczne
* Jeśli maszyna wirtualna ma do czynienia z szczytowego obciążenia, jego rozmiar można zmieniać na większy rozmiar, aby zwiększyć jego pojemność

Kontur kroki osiągnąć ten cel jest jako poniżej

1. Konfigurowanie usługi Azure Automation, dostęp do sieci maszyn wirtualnych
2. Importowanie elementów runbook usługi Azure Automation pionowy skalowania w ramach subskrypcji
3. Dodawanie elementu webhook do elementu runbook
4. Dodawanie alertu do maszyny wirtualnej

## <a name="scale-limitations"></a>Ograniczenia dotyczące skali

Ze względu na rozmiar pierwszej maszyny wirtualnej, rozmiary, które mogą być skalowane, może być ograniczona z powodu dostępności o innych rozmiarach w klastrze bieżącej maszyny wirtualnej jest wdrożony w. W opublikowanych elementów runbook usługi automation używane w tym artykule zajmie się tym przypadku firma Microsoft i można skalować tylko w ramach poniżej pary rozmiar maszyny Wirtualnej. Oznacza to, że Standard_D1v2 maszyny wirtualnej będzie nie nagle skalowany w górę aby maszyna wirtualna Standard_G5 lub skalowane w dół do Basic_A0. Ponadto ograniczone maszyny wirtualnej rozmiary skalowanie w górę/w dół nie jest obsługiwana. 

Możesz skalować między następujące pary rozmiarów:

* [Seria A](#a-series)
* [Seria B](#b-series)
* [Seria D](#d-series)
* [Seria E](#e-series)
* [Seria F](#f-series)
* [Seria G](#g-series)
* [Seria H](#h-series)
* [Seria L](#l-series)
* [Seria M](#m-series)
* [Seria N](#n-series)

### <a name="a-series"></a>Seria A

| Rozmiar początkowy | Skalowanie w górę rozmiar | 
| --- | --- |
| Basic_A0 | Basic_A1 |
| Basic_A1 | Basic_A2 |
| Basic_A2 | Basic_A3 |
| Basic_A3 | Basic_A4 |
| Standardowa_A0 | Standardowa_A1 |
| Standardowa_A1 | Standardowa_A2 |
| Standardowa_A2 | Standardowa_A3 |
| Standardowa_A3 | Standardowa_A4 |
| Standardowa_A5 | Standardowa_A6 |
| Standardowa_A6 | Standardowa_A7 |
| Standard_A8 | Standard_A9 |
| Standardowa_A10 | Standardowa_A11 |
| Standardowa_A1_v2 | Standardowa_A2_v2 |
| Standardowa_A2_v2 | Standardowa_A4_v2 |
| Standardowa_A4_v2 | Standardowa_A8_v2 |
| Standardowa_A2m_v2 | Standardowa_A4m_v2 |
| Standardowa_A4m_v2 | Standardowa_A8m_v2 |

### <a name="b-series"></a>Seria B

| Rozmiar początkowy | Skalowanie w górę rozmiar | 
| --- | --- |
| Standard_B1s | Standard_B2s |
| Standard_B1ms | Standard_B2ms |
| Standard_B2ms | Standard_B4ms |
| Standard_B4ms | Standard_B8ms |

### <a name="d-series"></a>Seria D

| Rozmiar początkowy | Skalowanie w górę rozmiar | 
| --- | --- |
| Standardowa_D1 | Standardowa_D2 |
| Standardowa_D2 | Standardowa_D3 |
| Standardowa_D3 | Standardowa_D4 |
| Standardowa_D11 | Standardowa_D12 |
| Standardowa_D12 | Standardowa_D13 |
| Standardowa_D13 | Standardowa_D14 |
| Standardowa_DS1 | Standardowa_DS2 |
| Standardowa_DS2 | Standardowa_DS3 |
| Standardowa_DS3 | Standardowa_DS4 |
| Standardowa_DS11 | Standardowa_DS12 |
| Standardowa_DS12 | Standardowa_DS13 |
| Standardowa_DS13 | Standardowa_DS14 |
| Standardowa_D1_v2 | Maszyna wirtualna Standard_D2_v2 |
| Maszyna wirtualna Standard_D2_v2 | Maszyna wirtualna Standard_D3_v2 |
| Maszyna wirtualna Standard_D3_v2 | Standardowa_D4_v2 |
| Standardowa_D4_v2 | Standardowa_D5_v2 |
| Standardowa_D11_v2 | Standardowa_D12_v2 |
| Standardowa_D12_v2 | Standardowa_D13_v2 |
| Standardowa_D13_v2 | Standardowa_D14_v2 |
| Standardowa_DS1_v2 | Standardowa_DS2_v2 |
| Standardowa_DS2_v2 | Standardowa_DS3_v2 |
| Standardowa_DS3_v2 | Standardowa_DS4_v2 |
| Standardowa_DS4_v2 | Standardowa_DS5_v2 |
| Standardowa_DS11_v2 | Standardowa_DS12_v2 |
| Standardowa_DS12_v2 | Standardowa_DS13_v2 |
| Standardowa_DS13_v2 | Standardowa_DS14_v2 |
| Maszyna wirtualna Standard_D2_v3 | Maszyna wirtualna Standard_D4_v3 |
| Maszyna wirtualna Standard_D4_v3 | Standardowa_D8_v3 |
| Standardowa_D8_v3 | Standardowa_D16_v3 |
| Standardowa_D16_v3 | Standard_D32_v3 |
| Standard_D32_v3 | Standard_D64_v3 |
| Standard_D2s_v3 | Standard_D4s_v3 |
| Standard_D4s_v3 | Standardowa_D8s_v3 |
| Standardowa_D8s_v3 | Standardowa_D16s_v3 |
| Standardowa_D16s_v3 | Standard_D32s_v3 |
| Standard_D32s_v3 | Standard_D64s_v3 |
| Standard_DC2s | Standard_DC4s |

### <a name="e-series"></a>Seria E

| Rozmiar początkowy | Skalowanie w górę rozmiar | 
| --- | --- |
| Standardowa_E2_v3 | Standardowa_E4_v3 |
| Standardowa_E4_v3 | Standardowa_E8_v3 |
| Standardowa_E8_v3 | Standardowa_E16_v3 |
| Standardowa_E16_v3 | Standard_E20_v3 |
| Standard_E20_v3 | Standardowa_E32_v3 |
| Standardowa_E32_v3 | Standardowa_E64_v3 |
| Standardowa_E2s_v3 | Standardowa_E4s_v3 |
| Standardowa_E4s_v3 | Standardowa_E8s_v3 |
| Standardowa_E8s_v3 | Standardowa_E16s_v3 |
| Standardowa_E16s_v3 | Standard_E20s_v3 |
| Standard_E20s_v3 | Standardowa_E32s_v3 |
| Standardowa_E32s_v3 | Standardowa_E64s_v3 |

### <a name="f-series"></a>Seria F

| Rozmiar początkowy | Skalowanie w górę rozmiar | 
| --- | --- |
| Standardowa_F1 | Standardowa_F2 |
| Standardowa_F2 | Standardowa_F4 |
| Standardowa_F4 | Standardowa_F8 |
| Standardowa_F8 | Standardowa_F16 |
| Standardowa_F1s | Standardowa_F2s |
| Standardowa_F2s | Standardowa_F4s |
| Standardowa_F4s | Standardowa_F8s |
| Standardowa_F8s | Standardowa_F16s |
| Standard_F2s_v2 | Standard_F4s_v2 |
| Standard_F4s_v2 | Standard_F8s_v2 |
| Standard_F8s_v2 | Standard_F16s_v2 |
| Standard_F16s_v2 | Standard_F32s_v2 |
| Standard_F32s_v2 | Standard_F64s_v2 |
| Standard_F64s_v2 | Standard_F7s_v2 |

### <a name="g-series"></a>Seria G

| Rozmiar początkowy | Skalowanie w górę rozmiar | 
| --- | --- |
| Standardowa_G1 | Standardowa_G2 |
| Standardowa_G2 | Standardowa_G3 |
| Standardowa_G3 | Standardowa_G4 |
| Standardowa_G4 | Maszyna wirtualna Standard_G5 |
| Standardowa_GS1 | Standardowa_GS2 |
| Standardowa_GS2 | Standardowa_GS3 |
| Standardowa_GS3 | Standardowa_GS4 |
| Standardowa_GS4 | Standard_GS5 |

### <a name="h-series"></a>Seria H

| Rozmiar początkowy | Skalowanie w górę rozmiar | 
| --- | --- |
| Standardowa_H8 | Standardowa_H16 |
| Standardowa_H8m | Standardowa_H16m |

### <a name="l-series"></a>Seria L

| Rozmiar początkowy | Skalowanie w górę rozmiar | 
| --- | --- |
| Standardowa_L4s | Standardowa_L8s |
| Standardowa_L8s | Standardowa_L16s |
| Standardowa_L16s | Standardowa_L32s |
| Standard_L8s_v2 | Standard_L16s_v2 |
| Standard_L16s_v2 | Standard_L32s_v2 |
| Standard_L32s_v2 | Standard_L64s_v2 |
| Standard_L64s_v2 | Standard_L80s_v2 |

### <a name="m-series"></a>Seria M

| Rozmiar początkowy | Skalowanie w górę rozmiar | 
| --- | --- |
| Warstwie standardowa_m8ms | Warstwie standardowa_m16ms |
| Warstwie standardowa_m16ms | Warstwie standardowa_m32ms |
| Warstwie standardowa_m32ms | Standardowa_M64ms |
| Standardowa_M64ms | Maszyna wirtualna Standard_M128ms |
| Warstwie standardowa_m32ls | Warstwie standardowa_m64ls |
| Maszyna wirtualna Standard_M64s | Maszyna wirtualna Standard_M128s |
| Standard_M64 | Standard_M128 |
| Standard_M64m | Standard_M128m |

### <a name="n-series"></a>Seria N

| Rozmiar początkowy | Skalowanie w górę rozmiar | 
| --- | --- |
| Standardowa_NC6 | Standardowa_NC12 |
| Standardowa_NC12 | Standardowa_NC24 |
| Standard_NC6s_v2 | Standard_NC12s_v2 |
| Standard_NC12s_v2 | Standard_NC24s_v2 |
| Standard_NC6s_v3 | Standard_NC12s_v3 |
| Standard_NC12s_v3 | Standard_NC24s_v3 |
| Maszyna wirtualna Standard_ND6 | Maszyna wirtualna Standard_ND12 |
| Maszyna wirtualna Standard_ND12 | Maszyna wirtualna Standard_ND24 |
| Standardowa_NV6 | Standardowa_NV12 |
| Standardowa_NV12 | Standardowa_NV24 |
| Standard_NV6s_v2 | Standard_NV12s_v2 |
| Standard_NV12s_v2 | Standard_NV24s_v2 |

## <a name="setup-azure-automation-to-access-your-virtual-machines"></a>Konfigurowanie usługi Azure Automation, dostęp do sieci maszyn wirtualnych
Pierwszą rzeczą, jaką należy wykonać jest utworzyć konto usługi Azure Automation, które będzie hostować elementy runbook umożliwia skalowanie wystąpień zestawu skalowania maszyn wirtualnych. Ostatnio usługi Automation wprowadza funkcję "Uruchom jako konto", co sprawia, że ustawienia zapasowej jednostki usługi do automatycznego uruchamiania elementów runbook w imieniu użytkownika bardzo proste. Możesz dowiedzieć się więcej o tym w poniższym artykule:

* [Uwierzytelnianie elementów Runbook przy użyciu konta Uruchom jako platformy Azure](../../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-the-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importowanie elementów runbook usługi Azure Automation pionowy skalowania w ramach subskrypcji
Elementy runbook, które są potrzebne do skalowania w pionie maszyny wirtualnej zostały już opublikowane w galerii elementów Runbook automatyzacji Azure. Należy zaimportować je do Twojej subskrypcji. Możesz dowiedzieć się, jak można zaimportować elementy runbook, zapoznając się z następującym artykułem.

* [Galerie elementów Runbook i modułów dla usługi Azure Automation](../../automation/automation-runbook-gallery.md)

Elementy runbook, które muszą zostać zaimportowane, są wyświetlane na poniższej ilustracji

![Importowanie elementów runbook](./media/vertical-scaling-automation/scale-runbooks.png)

## <a name="add-a-webhook-to-your-runbook"></a>Dodawanie elementu webhook do elementu runbook
Po zaimportowaniu elementów runbook, które będą potrzebne do Dodaj element webhook do elementu runbook, dzięki czemu mogą być wyzwalane przez alert z maszyny wirtualnej. Szczegółowe informacje o utworzenie elementu webhook dla elementu Runbook, które mogą być odczytywane w tym miejscu

* [Usługa Azure Automation elementów webhook](../../automation/automation-webhooks.md)

Upewnij się, że kopiujesz elementu webhook przed zamknięciem okna dialogowego elementu webhook, ponieważ będzie on potrzebny w następnej sekcji.

## <a name="add-an-alert-to-your-virtual-machine"></a>Dodawanie alertu do maszyny wirtualnej
1. Wybierz ustawienia maszyny wirtualnej
2. Wybierz pozycję "Reguły alertu"
3. Wybierz pozycję "Dodaj alert"
4. Wybierz metrykę, aby wyzwalać alert na
5. Wybierz warunek, który, gdy spełniony zostanie wywołać alert ognia
6. Wybierz wartości progowej dla warunku w kroku 5. do spełnienia
7. Wybierz okres względem której będzie sprawdzać usługi monitorowania, warunek i wartość progową kroki 5 i 6
8. Wklej element webhook, który został skopiowany z poprzedniej sekcji.

![Dodaj Alert do maszyny wirtualnej 1](./media/vertical-scaling-automation/add-alert-webhook-1.png)

![Dodaj Alert do maszyny wirtualnej 2](./media/vertical-scaling-automation/add-alert-webhook-2.png)

