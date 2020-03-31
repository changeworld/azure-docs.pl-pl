---
title: Zarządzanie alertami zabezpieczeń w usłudze Azure Security Center | Microsoft Docs
description: Ten dokument ułatwia zarządzanie alertami zabezpieczeń i reagowanie na nie przy użyciu funkcji Centrum zabezpieczeń Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 6ea951b542d893b8fef3cdf19a964ce2ef8a034d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415699"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>Zarządzanie alertami zabezpieczeń i reagowanie na nie w usłudze Azure Security Center

W tym temacie pokazano, jak wyświetlać i przetwarzać alerty, które zostały otrzymane w celu ochrony zasobów. 

* Aby dowiedzieć się więcej o różnych typach alertów, zobacz [Typy alertów zabezpieczeń](alerts-reference.md).
* Aby zapoznać się z omówieniem sposobu generowania alertów przez usługę Security Center, zobacz [Jak usługa Azure Security Center wykrywa zagrożenia i reaguje na nie](security-center-alerts-overview.md).

> [!NOTE]
> Aby włączyć wykrywanie zaawansowane, przeprowadź uaktualnienie usługi Azure Security Center do wersji Standard. Dostępna jest bezpłatna wersja próbna. Aby uaktualnić, wybierz warstwę cenową w [zasadach zabezpieczeń](tutorial-security-policy.md). Aby dowiedzieć się więcej, zobacz [cennik usługi Azure Security Center](security-center-pricing.md).

## <a name="what-are-security-alerts"></a>Czym są alerty zabezpieczeń?
Usługa Security Center automatycznie gromadzi, analizuje i integruje dane dzienników z zasobów platformy Azure, sieci oraz połączonych rozwiązań partnerskich, takich jak rozwiązania zapory i ochrony punktów końcowych, aby wykrywać prawdziwe zagrożenia i redukować liczbę fałszywych alarmów. W usłudze Security Center jest wyświetlana lista alertów zabezpieczeń uporządkowanych według priorytetu oraz informacje potrzebne do szybkiego analizowania problemu i zalecenia dotyczące postępowania w razie ataku.

> [!NOTE]
> Aby uzyskać więcej informacji na temat działania funkcji wykrywania usługi Security Center, zobacz [Jak usługa Azure Security Center wykrywa zagrożenia i reaguje na nie](security-center-alerts-overview.md#detect-threats).

## <a name="manage-your-security-alerts"></a>Zarządzanie alertami zabezpieczeń

1. Na pulpicie nawigacyjnym centrum zabezpieczeń zobacz kafelek **Ochrona przed zagrożeniami,** aby wyświetlić i zapoznać się z alertami.

    ![Kafelek Alerty zabezpieczeń w usłudze Security Center](./media/security-center-managing-and-responding-alerts/security-center-dashboard-alert.png)

1. Aby wyświetlić więcej szczegółów dotyczących alertów, kliknij kafelek.

   ![Alerty zabezpieczeń w usłudze Security Center](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Aby filtrować wyświetlane alerty, kliknij przycisk **Filtruj,** a na otwarciu bloku **Filtr** wybierz opcje filtru, które chcesz zastosować. Lista zostanie zaktualizowana zgodnie z wybranym filtrem. Filtrowanie może być bardzo pomocne. Możesz na przykład sprawdzić alerty zabezpieczeń, które wystąpiły w ciągu ostatnich 24 godzin, ponieważ badasz potencjalne naruszenie zabezpieczeń systemu.

    ![Filtrowanie alertów w usłudze Security Center](./media/security-center-managing-and-responding-alerts/security-center-filter-alerts.png)

## <a name="respond-to-security-alerts"></a>Odpowiadanie na alerty zabezpieczeń

1. Na liście **Alerty zabezpieczeń** kliknij alert zabezpieczeń. Wyświetlane są zaangażowane zasoby i kroki, które należy wykonać w celu skorygowanie ataku.

    ![Odpowiadanie na alerty zabezpieczeń](./media/security-center-managing-and-responding-alerts/security-center-alert.png)

1. Po przejrzeniu informacji kliknij zasób, który został zaatakowany.

    ![Sugestie dotyczące tego, co zrobić z alertami zabezpieczeń](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    Sekcja **Informacje ogólne** może zapewnić wgląd w to, co wyzwoliło alert zabezpieczeń. Wyświetla informacje, takie jak zasób docelowy, źródłowy adres IP (w stosownych przypadkach), jeśli alert jest nadal aktywny, i zalecenia dotyczące korygować.  

    > [!NOTE]
    >W niektórych przypadkach źródłowy adres IP jest niedostępny, niektóre dzienniki zdarzeń zabezpieczeń systemu Windows nie zawierają adresu IP.

1. Czynności naprawcze sugerowane w usłudze Security Center różnią się w zależności od alertu zabezpieczeń. Postępuj zgodnie z nimi dla każdego alertu. 

    W niektórych przypadkach w celu złagodzenia alertu zabezpieczeń może być trzeba użyć innych formantów platformy Azure lub usług w celu zaimplementowania zalecanego korygowania. 

## <a name="see-also"></a>Zobacz też

W tym dokumencie przedstawiono sposób konfigurowania zasad zabezpieczeń w Centrum zabezpieczeń. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Alerty zabezpieczeń w usłudze Azure Security Center](security-center-alerts-overview.md).
* [Obsługa incydentów związanych z bezpieczeństwem](security-center-incident.md)