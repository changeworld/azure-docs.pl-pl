---
title: Zarządzanie zdarzeniami związanymi z zabezpieczeniami w usłudze Azure Security Center | Dokumenty firmy Microsoft
description: Ten dokument ułatwia zarządzanie zdarzeniami związanymi z zabezpieczeniami usługi Azure Security Center.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 98fc339e473ffb2bf54e7119634e93046cca1ef3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415662"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>Zarządzanie zdarzeniami związanymi z zabezpieczeniami w usłudze Azure Security Center

Klasyfikowanie i badanie alertów zabezpieczeń może być czasochłonne nawet dla najbardziej wykwalifikowanych analityków bezpieczeństwa, a dla wielu trudno jest nawet wiedzieć, od czego zacząć. Używając [analizy](security-center-detection-capabilities.md) w celu powiązania informacji z różnych [alertów zabezpieczeń](security-center-managing-and-responding-alerts.md), usługa Security Center może dostarczyć pojedynczego widoku kampanii ataku i wszystkich powiązanych alertów — dzięki temu można szybko dowiedzieć się, jakie akcje zostały podjęte przez osobę atakującą i na jakie zasoby miały wpływ.

W tym temacie wyjaśniono zdarzenia w umykarze i sposób użycia alertów.

## <a name="what-is-a-security-incident"></a>Co to jest zdarzenie naruszenia zabezpieczeń?

W usłudze Security Center zdarzenie zabezpieczeń to agregacja wszystkich alertów dotyczących zasobu, które są zgodne ze wzorcami ataku cybernetycznego typu [kill chain](alerts-reference.md#intentions). Zdarzenia są wyświetlane na liście [Alerty zabezpieczeń.](security-center-managing-and-responding-alerts.md) Kliknij zdarzenie, aby wyświetlić powiązane alerty, co umożliwia uzyskanie większej ilości informacji o każdym wystąpieniu.

## <a name="managing-security-incidents"></a>Zarządzanie zdarzeniami naruszenia zabezpieczeń

1. Na pulpicie nawigacyjnym Centrum zabezpieczeń kliknij kafelek **Alerty zabezpieczeń.** Zdarzenia i alerty są wymienione. Należy zwrócić uwagę, że ikona opisu zdarzenia naruszenia zabezpieczeń różni się od ikon innych alertów.

    ![Wyświetlanie zdarzeń związanych z zabezpieczeniami](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Aby wyświetlić szczegóły, kliknij zdarzenie. W bloku **Wykryto zdarzenie zabezpieczeń,** wyświetla dalsze szczegóły. Sekcja **Informacje ogólne** może zapewnić wgląd w to, co wyzwoliło alert zabezpieczeń. Wyświetla informacje, takie jak zasób docelowy, źródłowy adres IP (w stosownych przypadkach), jeśli alert jest nadal aktywny, i zalecenia dotyczące korygować.  

    ![Reagowanie na zdarzenia dotyczące zabezpieczeń w usłudze Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-alert-incident.png)

1. Aby uzyskać więcej informacji o każdym alertie, kliknij alert. Czynności naprawcze sugerowane w Centrum zabezpieczeń różnią się w zależności od alertu zabezpieczeń.

   > [!NOTE]
   > Ten sam alert może istnieć jako część zdarzenia, a także być widoczne jako alert autonomiczny.

    ![Szczegóły alertu](./media/security-center-incident/security-center-incident-alert.png)

1. Wykonaj kroki korygowania podane dla każdego alertu.


## <a name="see-also"></a>Zobacz też
W tym dokumencie przedstawiono sposób użycia funkcji zdarzeń naruszenia zabezpieczeń w usłudze Security Center. Aby uzyskać powiązane informacje, zobacz następujące informacje:

* [Ochrona przed zagrożeniami w usłudze Azure Security Center](threat-protection.md)
* [Alerty zabezpieczeń w usłudze Azure Security Center](security-center-alerts-overview.md)
* [Zarządzanie alertami zabezpieczeń](security-center-managing-and-responding-alerts.md)