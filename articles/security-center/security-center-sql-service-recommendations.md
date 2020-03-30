---
title: Zalecenia dotyczące magazynu & danych — Usługa Azure Security Center
description: Ten dokument zawiera zalecenia zawarte w usłudze Azure Security Center, które ułatwiają ochronę danych i usługi Sql platformy Azure i pozostają zgodne z zasadami zabezpieczeń.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2019
ms.author: memildin
ms.openlocfilehash: 74ed55e1d460495bfa8d3d4c00bd37bb7f05260e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552869"
---
# <a name="protect-azure-data-and-storage-services"></a>Ochrona usług danych i magazynu platformy Azure
Gdy usługa Azure Security Center identyfikuje potencjalne luki w zabezpieczeniach, tworzy zalecenia, które prowadzą użytkownika przez proces konfigurowania potrzebnych formantów w celu wzmocnienia i ochrony zasobów.

W tym artykule opisano **stronę Zabezpieczenia danych** w sekcji zabezpieczeń zasobów w Centrum zabezpieczeń.

Aby uzyskać pełną listę zaleceń, które można znaleźć na tej stronie, zobacz [Zalecenia dotyczące danych i przechowywania](recommendations-reference.md#recs-datastorage).


## <a name="view-your-data-security-information"></a>Wyświetlanie informacji o zabezpieczeniach danych

1. W sekcji **Higiena zabezpieczeń zasobów** kliknij pozycję **Dane i zasoby magazynu**.

    ![Zasoby magazynu & danych](./media/security-center-monitoring/click-data.png)

    Zostanie otwarta strona **Zabezpieczenia danych** z zaleceniami dotyczącymi zasobów danych.

    [![Zasoby danych](./media/security-center-monitoring/sql-overview.png)](./media/security-center-monitoring/sql-overview.png#lightbox)

    Na tej stronie można:

    * Kliknij kartę **Przegląd** zawiera listę wszystkich zaleceń dotyczących zasobów danych, które mają zostać skorygowane. 
    * Kliknij każdą kartę i wyświetl zalecenia według typu zasobu.

    > [!NOTE]
    > Aby uzyskać więcej informacji na temat szyfrowania magazynu, zobacz [Szyfrowanie usługi Azure Storage dla danych w spoczynku](../storage/common/storage-service-encryption.md).


## <a name="remediate-a-recommendation-on-a-data-resource"></a>Korygowanie zalecenia dotyczące zasobu danych

1. Na dowolnej karcie zasobu kliknij zasób. Zostanie otwarta strona informacyjna zawierająca listę zaleceń, które mają zostać naprawione.

    ![Informacje o zasobie](./media/security-center-monitoring/sql-recommendations.png)

2. Kliknij zalecenie. Zostanie otwarta strona Zalecenie i zostanie wyświetlona **instrukcja korygowania** w celu zaimplementowania zalecenia.

   ![Kroki korygowania](./media/security-center-monitoring/remediate1.png)

3. Kliknij pozycję **Podejmij akcję**. Zostanie wyświetlona strona ustawień zasobów.

    ![Włącz zalecenie](./media/security-center-monitoring/remediate2.png)

4. Postępuj zgodnie z **instrukcjami korygowania** i kliknij przycisk **Zapisz**.


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o zaleceniach dotyczących innych typów zasobów platformy Azure, zobacz następujące tematy:

* [Pełna lista odwołań do zaleceń dotyczących zabezpieczeń usługi Azure Security Center](recommendations-reference.md)
* [Ochrona maszyn i aplikacji w usłudze Azure Security Center](security-center-virtual-machine-protection.md)
* [Ochrona sieci w usłudze Azure Security Center](security-center-network-recommendations.md)