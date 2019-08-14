---
title: Dodawanie zapory nowej generacji w Azure Security Center | Microsoft Docs
description: W tym dokumencie przedstawiono sposób wdrażania Azure Security Center zalecenia **Dodaj zaporę nowej generacji** i **Roześlij ruch tylko przez zapory następnej generacji**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 48b99015-4db8-4ce8-85e4-b544c0fa203e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 731102037b596091b80fbdfa02a8ff3c111b556e
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2019
ms.locfileid: "60707078"
---
# <a name="add-a-next-generation-firewall-in-azure-security-center"></a>Dodawanie zapory nowej generacji w Azure Security Center
W Azure Security Center może być zalecane dodanie zapory nowej generacji (zapory następnej generacji) od partnera firmy Microsoft w celu zwiększenia bezpieczeństwa. W tym dokumencie przedstawiono przykład sposobu wykonania tej czynności.

> [!NOTE]
> Informacje na temat usługi przedstawiono w tym dokumencie za pomocą przykładowego wdrożenia.  Nie jest to przewodnik krok po kroku.
>
>

## <a name="implement-the-recommendation"></a>Implementowanie zalecenia
1. W bloku **zalecenia** wybierz pozycję **Dodaj zaporę nowej generacji**.
   ![Dodawanie zapory nowej generacji][1]
2. W bloku **Dodaj zaporę nowej generacji** wybierz punkt końcowy.
   ![Wybierz punkt końcowy][2]
3. Zostanie otwarty drugi blok **Zapora nowej generacji** . Możesz wybrać użycie istniejącego rozwiązania, jeśli jest dostępne lub można utworzyć nowe. W tym przykładzie nie ma dostępnych istniejących rozwiązań, więc tworzymy zapory następnej generacji.
   ![Utwórz zaporę nowej generacji][3]
4. Aby utworzyć zapory następnej generacji, wybierz rozwiązanie z listy zintegrowanych partnerów. W tym przykładzie wybieramy pozycję **Check Point**.
   ![Wybierz rozwiązanie zapory nowej generacji][4]
5. Zostanie otwarty blok **punktu kontroli** z informacjami o rozwiązaniu partnerskim. Wybierz pozycję **Utwórz** w bloku informacje.
   ![Blok informacji o zaporze][5]
6. Zostanie otwarty blok **Utwórz maszynę wirtualną** . W tym miejscu możesz wprowadzić informacje wymagane do uruchomienia maszyny wirtualnej, na której działa zapory następnej generacji. Wykonaj kroki i podaj wymagane informacje dotyczące zapory następnej generacji. Wybierz przycisk OK, aby zastosować.
   ![Utwórz maszynę wirtualną do uruchomienia zapory następnej generacji][6]

## <a name="route-traffic-through-ngfw-only"></a>Kieruj ruch tylko przez zaporę nowej generacji
Wróć do bloku **rekomendacje** . Nowy wpis został wygenerowany po dodaniu zapory następnej generacji za pośrednictwem Security Center, nazywanego **ruchem tras przez zapory następnej generacji**. To zalecenie jest tworzone tylko wtedy, gdy zainstalowano zapory następnej generacji za Security Center. Jeśli masz punkty końcowe dostępne z Internetu, Security Center zaleca się skonfigurowanie reguł sieciowej grupy zabezpieczeń, które wymuszą ruch przychodzący do maszyny wirtualnej za poorednictwem zapory następnej generacji.

1. W **bloku zalecenia**wybierz pozycję **kierowanie ruchu tylko przez zapory następnej generacji**.
   ![Kierowanie ruchu sieciowego tylko za pośrednictwem zapory następnej generacji][7]
2. Spowoduje to otwarcie bloku **tylko ruch przez zapory następnej generacji**, w którym znajdują się maszyny wirtualne, do których można kierować ruch. Wybierz maszynę wirtualną z listy.
   ![Wybierz maszynę wirtualną][8]
3. Zostanie otwarty blok dla wybranej maszyny wirtualnej, w którym wyświetlane są powiązane reguły ruchu przychodzącego. Opis zawiera więcej informacji na temat możliwych kolejnych kroków. Wybierz pozycję **Edytuj reguły ruchu przychodzącego** , aby kontynuować Edytowanie reguły ruchu przychodzącego. Oczekuje się, że **Źródło** nie jest ustawione na **żadne** dla punktów końcowych dostępnych z Internetu połączonych z zapory następnej generacji. Aby dowiedzieć się więcej o właściwościach reguły ruchu przychodzącego, zobacz [reguły zabezpieczeń](../virtual-network/security-overview.md#security-rules).
   ![Konfigurowanie reguł do ograniczania][9]
   dostępu![Edytowanie reguły ruchu przychodzącego][10]

## <a name="see-also"></a>Zobacz także
W tym dokumencie przedstawiono sposób implementacji zalecenia Security Center "Dodawanie zapory nowej generacji". Aby dowiedzieć się więcej na temat rozwiązań NGFWs i partnera Check Point, zobacz następujące tematy:

* [Zapora nowej generacji](https://en.wikipedia.org/wiki/Next-Generation_Firewall)
* [Check Point vSEC](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/checkpoint.vsec)

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w Azure Security Center](tutorial-security-policy.md) --Dowiedz się, jak skonfigurować zasady zabezpieczeń.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Azure Security Center](security-center-recommendations.md) — Dowiedz się, jak zalecenia ułatwiają ochronę zasobów platformy Azure.
* [Monitorowanie kondycji zabezpieczeń w Azure Security Center](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich w Centrum zabezpieczeń Azure](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog dotyczący zabezpieczeń platformy Azure](https://blogs.msdn.com/b/azuresecurity/) — zawiera wpisy w blogu dotyczące zabezpieczeń i zgodności platformy Azure.

<!--Image references-->
[1]: ./media/security-center-add-next-gen-firewall/add-next-gen-firewall.png
[2]: ./media/security-center-add-next-gen-firewall/select-an-endpoint.png
[3]: ./media/security-center-add-next-gen-firewall/create-new-next-gen-firewall.png
[4]: ./media/security-center-add-next-gen-firewall/select-next-gen-firewall.png
[5]: ./media/security-center-add-next-gen-firewall/firewall-solution-info-blade.png
[6]: ./media/security-center-add-next-gen-firewall/create-virtual-machine.png
[7]: ./media/security-center-add-next-gen-firewall/route-traffic-through-ngfw.png
[8]: ./media/security-center-add-next-gen-firewall/select-vm.png
[9]: ./media/security-center-add-next-gen-firewall/configure-rules-to-limit-access.png
[10]: ./media/security-center-add-next-gen-firewall/edit-inbound-rule.png
