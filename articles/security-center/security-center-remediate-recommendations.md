---
title: Koryguj zalecenia w Azure Security Center | Microsoft Docs
description: W tym dokumencie wyjaśniono, jak skorygować zalecenia w Azure Security Center, aby pomóc w ochronie zasobów platformy Azure i zachować zgodność z zasadami zabezpieczeń.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2019
ms.author: v-mohabe
ms.openlocfilehash: b947796d4af9def1a274f6e04f356c3173d29fff
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/05/2019
ms.locfileid: "68778992"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Koryguj zalecenia w Azure Security Center

Zalecenia zawierają sugestie dotyczące lepszego zabezpieczania zasobów.  Zalecenie jest implementowane przez wykonanie czynności zaradczych, które zostały podane w zaleceniu. 

## Kroki zaradcze<a name="remediation-steps"></a>

Po przejrzeniu wszystkich zaleceń Zdecyduj, które z nich należy skorygować w pierwszej kolejności. Zalecamy użycie [wpływu na ocenę](security-center-recommendations.md#monitor-recommendations) , aby pomóc w ustaleniu, co należy zrobić w pierwszej kolejności.

1. Na liście kliknij zalecenie.
1. Postępuj zgodnie z instrukcjami w sekcji **kroki zaradcze** . Każde zalecenie ma swój własny zestaw instrukcji. Poniżej przedstawiono procedurę korygowania służącą do konfigurowania aplikacji w celu zezwalania na ruch tylko za pośrednictwem protokołu HTTPS.

    ![Szczegóły rekomendacji](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. Po zakończeniu zostanie wyświetlone powiadomienie z informacją o tym, czy korygowanie zakończyło się pomyślnie.

## <a name="next-steps"></a>Kolejne kroki

W tym dokumencie przedstawiono sposób korygowania zaleceń w Security Center. Aby dowiedzieć się więcej na temat Security Center, zobacz następujące tematy:

* [Ustawianie zasad zabezpieczeń w Azure Security Center](tutorial-security-policy.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów platformy Azure.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
