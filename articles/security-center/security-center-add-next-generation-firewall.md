---
title: Dodaj zaporę nowej generacji w Centrum zabezpieczeń Azure | Dokumentacja firmy Microsoft
description: Ten dokument zawiera implementowania zaleceń Centrum zabezpieczeń Azure **dodać zaporę nowej generacji** i **traffice o trasy za pośrednictwem zapory nowej generacji tylko**.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 48b99015-4db8-4ce8-85e4-b544c0fa203e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
ms.openlocfilehash: 9f70cb03a26fd5bea7e1e034c653ece8e0b8c349
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="add-a-next-generation-firewall-in-azure-security-center"></a>Dodaj zaporę nowej generacji w Centrum zabezpieczeń Azure
Centrum zabezpieczeń Azure może zaleca dodanie zaporę nowej generacji (NGFW) od partnera firmy Microsoft w celu zwiększenia z ochrony zabezpieczeń. Ten dokument przeprowadzi Cię przez przykładowy jak to zrobić.

> [!NOTE]
> Informacje na temat usługi przedstawiono w tym dokumencie za pomocą przykładowego wdrożenia.  Nie jest to przewodnik krok po kroku.
>
>

## <a name="implement-the-recommendation"></a>Wykonania zalecenia
1. W **zalecenia** bloku, wybierz opcję **dodać zaporę nowej generacji**.
   ![Dodawanie zapory nowej generacji][1]
2. W **dodać zaporę nowej generacji** bloku, wybierz punkt końcowy.
   ![Wybierz punkt końcowy][2]
3. Drugi **dodać zaporę nowej generacji** zostanie otwarty blok. Możesz użyć istniejącego rozwiązania, jeśli jest dostępny, lub można utworzyć nowy. W tym przykładzie nie dostępnych żadnych istniejących rozwiązań, utworzymy zapory nowej generacji.
   ![Utwórz Zapora nowej generacji][3]
4. Utworzyć NGFW, należy wybrać z listy partnerów zintegrowane rozwiązanie. W tym przykładzie mamy wybierz **Check Point**.
   ![Wybierz rozwiązanie Zapora nowej generacji][4]
5. **Check Point** zostanie otwarty blok informacjami o rozwiązaniem partnerskim. Wybierz **Utwórz** w bloku informacji.
   ![Blok informacji zapory][5]
6. **Tworzenia maszyny wirtualnej** zostanie otwarty blok. Tutaj można wprowadzić informacje wymagane do pokrętła maszyny wirtualnej (VM), który uruchamia zapory nowej generacji. Postępuj zgodnie z instrukcjami i podaj wymagane informacje zapory nowej generacji. Wybierz OK, aby zastosować.
   ![Utwórz maszynę wirtualną, aby uruchomić NGFW][6]

## <a name="route-traffic-through-ngfw-only"></a>Kierowanie ruchu sieciowego tylko za pośrednictwem zapory następnej generacji
Wróć do **zalecenia** bloku. Wygenerowano nowy wpis po dodaniu NGFW za pośrednictwem Centrum zabezpieczeń o nazwie **kierowania ruchu za pośrednictwem zapory nowej generacji**. To zalecenie jest tworzony tylko w przypadku zainstalowania programu NGFW za pośrednictwem Centrum zabezpieczeń. Jeśli masz punkty końcowe skierowane do Internetu, Centrum zabezpieczeń zaleca, aby skonfigurować reguł sieciowej grupy zabezpieczeń, który wymusza ruch przychodzący do maszyny Wirtualnej za pośrednictwem sieci NGFW.

1. W **bloku zalecenia**, wybierz pozycję **kierowania ruchu za pośrednictwem zapory nowej generacji**.
   ![Kierowanie ruchu sieciowego tylko za pośrednictwem zapory następnej generacji][7]
2. Spowoduje to otwarcie bloku **kierowania ruchu za pośrednictwem zapory nowej generacji**, który zawiera listę maszyn wirtualnych, które może kierować ruchem do. Wybierz maszynę Wirtualną z listy.
   ![Wybierz maszynę Wirtualną][8]
3. Zostanie otwarty blok dla wybranej maszyny Wirtualnej, wyświetlanie powiązanych reguł ruchu przychodzącego. Opis zawiera więcej informacji na temat wykonać następujące czynności. Wybierz **edytowanie reguły ruchu przychodzącego** kontynuować edytowanie reguły ruchu przychodzącego. Oczekuje się, że **źródła** nie jest ustawiony na **żadnych** dla punktów końcowych internetowy połączone z zapory nowej generacji. Aby dowiedzieć się więcej o właściwościach reguły ruchu przychodzącego, zobacz [reguły zabezpieczeń](../virtual-network/security-overview.md#security-rules).
   ![Konfigurowanie reguł, aby ograniczyć dostęp do][9]
   ![edycji reguły dla ruchu przychodzącego][10]

## <a name="see-also"></a>Zobacz także
Ten dokument pokazano sposób wykonania zalecenia Centrum zabezpieczeń "Dodaj zaporę nowej generacji". Aby dowiedzieć się więcej na temat NGFWs i rozwiązaniem partnerskim Check Point, zobacz następujące tematy:

* [Zapora nowej generacji](https://en.wikipedia.org/wiki/Next-Generation_Firewall)
* [Punkt wyboru vSEC](https://azure.microsoft.com/marketplace/partners/checkpoint/check-point-r77-10/)

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w Centrum zabezpieczeń Azure](security-center-policies.md) — informacje o sposobie konfigurowania zasad zabezpieczeń.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Centrum zabezpieczeń Azure](security-center-recommendations.md) — Dowiedz się, w jaki sposób zalecenia ułatwiają ochronę zasobów platformy Azure.
* [Monitorowanie kondycji zabezpieczeń w Centrum zabezpieczeń Azure](security-center-monitoring.md) — informacje o sposobie monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich w Centrum zabezpieczeń Azure](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń platformy Azure i zgodności.

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
