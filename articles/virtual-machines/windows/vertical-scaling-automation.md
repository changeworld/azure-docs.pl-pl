---
title: Używanie Azure Automation do skalowania maszyn wirtualnych z systemem Windows w pionie | Microsoft Docs
description: Skalowanie maszyny wirtualnej z systemem Windows w pionie w odpowiedzi na monitorowanie alertów za pomocą Azure Automation
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 4f964713-fb67-4bcc-8246-3431452ddf7d
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/18/2019
ms.author: kasing
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 71af54f0a1dd7ecd4d4f0dc6f7a465439993db39
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100173"
---
# <a name="vertically-scale-windows-vms-with-azure-automation"></a>Skalowanie maszyn wirtualnych z systemem Windows w pionie przy użyciu Azure Automation

Skalowanie w pionie to proces zwiększania lub zmniejszania zasobów maszyny w odpowiedzi na obciążenie. Na platformie Azure można to osiągnąć, zmieniając rozmiar maszyny wirtualnej. Może to pomóc w następujących scenariuszach

* Jeśli maszyna wirtualna nie jest często używana, można zmienić jej rozmiar na mniejszy, aby zmniejszyć koszty miesięczne
* Jeśli maszyna wirtualna widzi szczytowe obciążenie, można zmienić jej rozmiar na większy rozmiar, aby zwiększyć jej pojemność.

Poniżej przedstawiono konspekt kroków, które należy wykonać, poniżej

1. Azure Automation Instalatora, aby uzyskać dostęp do Virtual Machines
2. Zaimportuj Azure Automation elementy Runbook skalowania w pionie do subskrypcji
3. Dodawanie elementu webhook do elementu Runbook
4. Dodawanie alertu do maszyny wirtualnej


## <a name="scale-limitations"></a>Ograniczenia skalowania

Ze względu na rozmiar pierwszej maszyny wirtualnej rozmiary, do których można skalować, mogą być ograniczone ze względu na dostępność innych rozmiarów z bieżącej maszyny wirtualnej klastra wdrożonych w programie. W opublikowanym elemencie Runbook usługi Automation używanym w tym artykule zajmiemy się tym przypadkiem i skalujemy ją tylko w obrębie poniższych par rozmiaru maszyny wirtualnej. Oznacza to, że maszyna wirtualna Standard_D1v2 nie będzie nagle skalowana do Standard_G5 lub skalowana w dół do Basic_A0. Nie są obsługiwane również rozmiary maszyn wirtualnych z ograniczeniami skalowania w górę/w dół. 

Można wybrać skalowanie między następującymi parami rozmiarów:

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

| Rozmiar początkowy | Skaluj w górę | 
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

| Rozmiar początkowy | Skaluj w górę | 
| --- | --- |
| Standard_B1s | Standard_B2s |
| Standard_B1ms | Standard_B2ms |
| Standard_B2ms | Standard_B4ms |
| Standard_B4ms | Standard_B8ms |

### <a name="d-series"></a>Seria D

| Rozmiar początkowy | Skaluj w górę | 
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

| Rozmiar początkowy | Skaluj w górę | 
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

| Rozmiar początkowy | Skaluj w górę | 
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

| Rozmiar początkowy | Skaluj w górę | 
| --- | --- |
| Standardowa_G1 | Standardowa_G2 |
| Standardowa_G2 | Standardowa_G3 |
| Standardowa_G3 | Standardowa_G4 |
| Standardowa_G4 | Standard_G5 |
| Standardowa_GS1 | Standardowa_GS2 |
| Standardowa_GS2 | Standardowa_GS3 |
| Standardowa_GS3 | Standardowa_GS4 |
| Standardowa_GS4 | Standard_GS5 |

### <a name="h-series"></a>Seria H

| Rozmiar początkowy | Skaluj w górę | 
| --- | --- |
| Standardowa_H8 | Standardowa_H16 |
| Standardowa_H8m | Standardowa_H16m |

### <a name="l-series"></a>Seria L

| Rozmiar początkowy | Skaluj w górę | 
| --- | --- |
| Standardowa_L4s | Standardowa_L8s |
| Standardowa_L8s | Standardowa_L16s |
| Standardowa_L16s | Standardowa_L32s |
| Standard_L8s_v2 | Standard_L16s_v2 |
| Standard_L16s_v2 | Standard_L32s_v2 |
| Standard_L32s_v2 | Standard_L64s_v2 |
| Standard_L64s_v2 | Standard_L80s_v2 |

### <a name="m-series"></a>Seria M

| Rozmiar początkowy | Skaluj w górę | 
| --- | --- |
| Standard_M8ms | Standard_M16ms |
| Standard_M16ms | Standard_M32ms |
| Standard_M32ms | Standardowa_M64ms |
| Standardowa_M64ms | Standard_M128ms |
| Standard_M32ls | Standard_M64ls |
| Standard_M64s | Standard_M128s |
| Standard_M64 | Standard_M128 |
| Standard_M64m | Standard_M128m |

### <a name="n-series"></a>Seria N

| Rozmiar początkowy | Skaluj w górę | 
| --- | --- |
| Standardowa_NC6 | Standardowa_NC12 |
| Standardowa_NC12 | Standardowa_NC24 |
| Standard_NC6s_v2 | Standard_NC12s_v2 |
| Standard_NC12s_v2 | Standard_NC24s_v2 |
| Standard_NC6s_v3 | Standard_NC12s_v3 |
| Standard_NC12s_v3 | Standard_NC24s_v3 |
| Standard_ND6 | Standard_ND12 |
| Standard_ND12 | Standard_ND24 |
| Standardowa_NV6 | Standardowa_NV12 |
| Standardowa_NV12 | Standardowa_NV24 |
| Standard_NV6s_v2 | Standard_NV12s_v2 |
| Standard_NV12s_v2 | Standard_NV24s_v2 |
| Standard_NV12s_v3 |Standard_NV48s_v3 |

## <a name="setup-azure-automation-to-access-your-virtual-machines"></a>Azure Automation Instalatora, aby uzyskać dostęp do Virtual Machines
Najpierw należy utworzyć konto Azure Automation, które będzie hostować elementy Runbook używane do skalowania maszyny wirtualnej. Ostatnio usługa Automation wprowadziła funkcję "Uruchom jako", która umożliwia skonfigurowanie nazwy głównej usługi do automatycznego uruchamiania elementów Runbook w imieniu użytkownika. Więcej informacji na ten temat można znaleźć w poniższym artykule:

* [Uwierzytelnianie elementów Runbook przy użyciu konta Uruchom jako platformy Azure](../../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-the-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Zaimportuj Azure Automation elementy Runbook skalowania w pionie do subskrypcji
Elementy Runbook, które są zbędne do skalowania w pionie maszyny wirtualnej, zostały już opublikowane w galerii elementów Runbook Azure Automation. Konieczne będzie zaimportowanie ich do subskrypcji. Aby dowiedzieć się, jak importować elementy Runbook, przeczytaj następujący artykuł.

* [Galerie elementów Runbook i modułów dla usługi Azure Automation](../../automation/automation-runbook-gallery.md)

Elementy Runbook, które należy zaimportować, są wyświetlane na poniższej ilustracji

![Importuj elementy Runbook](./media/vertical-scaling-automation/scale-runbooks.png)

## <a name="add-a-webhook-to-your-runbook"></a>Dodawanie elementu webhook do elementu Runbook
Po zaimportowaniu elementów Runbook należy dodać element webhook do elementu Runbook, aby mógł zostać wyzwolony przez alert z maszyny wirtualnej. Szczegóły dotyczące tworzenia elementu webhook dla elementu Runbook można znaleźć tutaj

* [Azure Automation elementów webhook](../../automation/automation-webhooks.md)

Przed zamknięciem okna dialogowego elementu webhook upewnij się, że jest on kopiowany, ponieważ będzie potrzebny w następnej sekcji.

## <a name="add-an-alert-to-your-virtual-machine"></a>Dodawanie alertu do maszyny wirtualnej
1. Wybieranie ustawień maszyny wirtualnej
2. Wybierz pozycję "reguły alertów"
3. Wybierz pozycję "Dodaj Alert"
4. Wybierz metrykę, na której ma być wyzwalany alert
5. Wybierz warunek, który po spełnieniu spowoduje uruchomienie alertu
6. Wybierz próg dla warunku w kroku 5. do spełnienia
7. Wybierz okres, w którym usługa monitorowania sprawdzi warunek i próg w krokach 5 & 6
8. Wklej element webhook skopiowany z poprzedniej sekcji.

![Dodawanie alertu do maszyny wirtualnej 1](./media/vertical-scaling-automation/add-alert-webhook-1.png)

![Dodawanie alertu do maszyny wirtualnej 2](./media/vertical-scaling-automation/add-alert-webhook-2.png)

