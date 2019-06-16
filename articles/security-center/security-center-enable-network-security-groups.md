---
title: Włącz sieciowe grupy zabezpieczeń w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: W tym dokumencie przedstawiono sposób realizacji zalecenia w usłudze Azure Security Center **Włącz sieciowe grupy zabezpieczeń**.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60911449"
---
# <a name="enable-network-security-groups-in-azure-security-center"></a>Włącz sieciowe grupy zabezpieczeń w usłudze Azure Security Center
Usługa Azure Security Center zaleca Włącz sieciową grupę zabezpieczeń (NSG), jeśli nie jest już włączona. Sieciowe grupy zabezpieczeń zawierają listę reguł listy kontroli dostępu (ACL), które blokują lub zezwalają na ruch sieciowy do wystąpień maszyn wirtualnych w sieci wirtualnej. Grupy NSG można kojarzyć z podsieciami lub poszczególnymi wystąpieniami maszyn wirtualnych w danej podsieci. Gdy sieciowa grupa zabezpieczeń jest skojarzona z podsiecią, reguły listy ACL dotyczą wszystkich wystąpień maszyn wirtualnych w tej podsieci. Ponadto ruch do poszczególnych maszyn wirtualnych można ograniczyć jeszcze bardziej przez skojarzenie sieciowej grupy zabezpieczeń bezpośrednio do tej maszyny Wirtualnej. Aby dowiedzieć się więcej, zobacz [co to jest grupa zabezpieczeń sieci (NSG)?](../virtual-network/security-overview.md)

Jeśli nie masz sieciowymi grupami zabezpieczeń włączonymi, usługa Security Center przedstawia zaleceń dwóch możesz: Włącz sieciowe grupy zabezpieczeń w podsieci i włączanie sieciowych grup zabezpieczeń na maszynach wirtualnych. Należy wybrać poziom, podsieci lub maszyn wirtualnych, aby zastosować sieciowe grupy zabezpieczeń.

> [!NOTE]
> Informacje na temat usługi przedstawiono w tym dokumencie za pomocą przykładowego wdrożenia.  Nie jest to przewodnik krok po kroku.
>
>

## <a name="implement-the-recommendation"></a>Zaimplementuj zalecenia
1. W **zalecenia** bloku wybierz **Włącz sieciowe grupy zabezpieczeń** w podsieciach lub maszynach wirtualnych.
   ![Włączanie sieciowych grup zabezpieczeń][1]
2. Spowoduje to otwarcie bloku **Konfiguruj brakujące grupy zabezpieczeń sieci** dla podsieci lub maszyn wirtualnych, w zależności od wybranego zalecenia. Wybierz podsieć lub maszynę wirtualną, aby skonfigurować sieciową grupę zabezpieczeń na.

   ![Konfigurowanie sieciowej grupy zabezpieczeń dla podsieci][2]

   ![Konfigurowanie sieciowej grupy zabezpieczeń dla maszyny Wirtualnej][3]
3. Na **Wybieranie grupy zabezpieczeń sieci** bloku wybierz istniejącej sieciowej grupie zabezpieczeń lub **Utwórz nową** do utworzenia sieciowej grupy zabezpieczeń.

   ![Wybieranie grupy zabezpieczeń sieci][4]

Jeśli tworzysz sieciowej grupy zabezpieczeń, wykonaj kroki opisane w [Zarządzanie sieciową grupę zabezpieczeń](../virtual-network/manage-network-security-group.md) do utworzenia sieciowej grupy zabezpieczeń i ustawienia zasad zabezpieczeń.

## <a name="see-also"></a>Zobacz także
W tym artykule pokazano, jak zaimplementować zalecenia usługi Security Center "Włącz sieciowe grupy zabezpieczeń" dla podsieci lub maszyn wirtualnych. Aby dowiedzieć się więcej na temat włączania sieciowych grup zabezpieczeń, zobacz następujące tematy:

* [Co to jest sieciowa grupa zabezpieczeń?](../virtual-network/security-overview.md)
* [Zarządzanie sieciową grupę zabezpieczeń](../virtual-network/manage-network-security-group.md)

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md) — Dowiedz się, w jaki sposób zalecenia ułatwiają ochronę zasobów platformy Azure.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md) — informacje o sposobie monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich w Centrum zabezpieczeń Azure](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) — Uzyskaj najnowsze informacje o zabezpieczeniach platformy Azure i informacji.

<!--Image references-->
[1]: ./media/security-center-enable-nsg/enable-nsg.png
[2]:./media/security-center-enable-nsg/configure-nsg-for-subnet.png
[3]: ./media/security-center-enable-nsg/configure-nsg-for-vm.png
[4]: ./media/security-center-enable-nsg/choose-nsg.png
