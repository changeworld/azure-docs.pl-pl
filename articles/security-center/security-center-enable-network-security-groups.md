---
title: Włącz grupy zabezpieczeń sieci w Azure Security Center | Microsoft Docs
description: W tym dokumencie przedstawiono sposób implementacji zalecenia Azure Security Center włączania **sieciowych grup zabezpieczeń**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f53ed853-ffaf-4530-a019-1906ba6f341b
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 14b7cc8f8162574380ca21ac515af8b7d3d5ded9
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2019
ms.locfileid: "60911449"
---
# <a name="enable-network-security-groups-in-azure-security-center"></a>Włącz grupy zabezpieczeń sieci w Azure Security Center
W Azure Security Center zaleca się włączenie sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń), jeśli nie została ona jeszcze włączona. Sieciowych grup zabezpieczeń zawierają listę reguł listy Access Control (ACL), które zezwalają na ruch sieciowy do wystąpień maszyn wirtualnych Virtual Network lub go odmawiają. Grupy NSG można kojarzyć z podsieciami lub poszczególnymi wystąpieniami maszyn wirtualnych w danej podsieci. Gdy sieciowa grupa zabezpieczeń jest skojarzona z podsiecią, reguły listy ACL dotyczą wszystkich wystąpień maszyn wirtualnych w tej podsieci. Ponadto ruch do pojedynczej maszyny wirtualnej może być bardziej ograniczony przez skojarzenie sieciowej grupy zabezpieczeń bezpośrednio z tą maszyną wirtualną. Aby dowiedzieć się więcej, zobacz [co to jest sieciowa Grupa zabezpieczeń (sieciowej grupy zabezpieczeń)?](../virtual-network/security-overview.md)

Jeśli sieciowych grup zabezpieczeń nie są włączone, Security Center przedstawia dwa zalecenia: Włącz sieciowe grupy zabezpieczeń w podsieciach i Włącz sieciowe grupy zabezpieczeń na maszynach wirtualnych. Możesz wybrać poziom, podsieć lub maszynę wirtualną, aby zastosować sieciowych grup zabezpieczeń.

> [!NOTE]
> Informacje na temat usługi przedstawiono w tym dokumencie za pomocą przykładowego wdrożenia.  Nie jest to przewodnik krok po kroku.
>
>

## <a name="implement-the-recommendation"></a>Implementowanie zalecenia
1. W bloku **zalecenia** wybierz pozycję **Włącz sieciowe grupy zabezpieczeń** w podsieciach lub na maszynach wirtualnych.
   ![Włączanie sieciowych grup zabezpieczeń][1]
2. Spowoduje to otwarcie bloku **Skonfiguruj brakujące sieciowe grupy zabezpieczeń** dla podsieci lub dla maszyn wirtualnych, w zależności od wybranego zalecenia. Wybierz podsieć lub maszynę wirtualną, na której chcesz skonfigurować sieciowej grupy zabezpieczeń.

   ![Skonfiguruj sieciowej grupy zabezpieczeń dla podsieci][2]

   ![Konfigurowanie sieciowej grupy zabezpieczeń dla maszyny wirtualnej][3]
3. W bloku **Wybieranie grupy zabezpieczeń sieci** wybierz istniejący sieciowej grupy zabezpieczeń lub wybierz pozycję **Utwórz nowy** , aby utworzyć sieciowej grupy zabezpieczeń.

   ![Wybierz sieciową grupę zabezpieczeń][4]

W przypadku utworzenia sieciowej grupy zabezpieczeń należy wykonać czynności opisane w sekcji [Zarządzanie grupą zabezpieczeń sieci](../virtual-network/manage-network-security-group.md) w celu utworzenia sieciowej grupy zabezpieczeń i Ustawienia reguł zabezpieczeń.

## <a name="see-also"></a>Zobacz także
W tym artykule pokazano, jak wdrożyć zalecenie Security Center "Włącz grupy zabezpieczeń sieci" dla podsieci lub maszyn wirtualnych. Aby dowiedzieć się więcej na temat włączania sieciowych grup zabezpieczeń, zobacz następujące tematy:

* [Co to jest sieciowa grupa zabezpieczeń?](../virtual-network/security-overview.md)
* [Zarządzanie grupą zabezpieczeń sieci](../virtual-network/manage-network-security-group.md)

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Azure Security Center](security-center-recommendations.md) — Dowiedz się, jak zalecenia ułatwiają ochronę zasobów platformy Azure.
* [Monitorowanie kondycji zabezpieczeń w Azure Security Center](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich w Centrum zabezpieczeń Azure](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog dotyczący zabezpieczeń platformy Azure](https://blogs.msdn.com/b/azuresecurity/) — Uzyskaj najnowsze informacje o zabezpieczeniach platformy Azure.

<!--Image references-->
[1]: ./media/security-center-enable-nsg/enable-nsg.png
[2]:./media/security-center-enable-nsg/configure-nsg-for-subnet.png
[3]: ./media/security-center-enable-nsg/configure-nsg-for-vm.png
[4]: ./media/security-center-enable-nsg/choose-nsg.png
