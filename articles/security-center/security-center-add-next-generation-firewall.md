---
title: Dodaj zaporę następnej generacji w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: W tym dokumencie przedstawiono sposób implementowania zaleceń Centrum zabezpieczeń Azure **Dodaj zaporę następnej generacji** i **kierowania ruchu przez zaporę nowej generacji tylko**.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60707078"
---
# <a name="add-a-next-generation-firewall-in-azure-security-center"></a>Dodaj zaporę następnej generacji w usłudze Azure Security Center
Usługa Azure Security Center może zalecić sytuacja: dodajesz zaporę nowej generacji (nowej NGFW) od partnera firmy Microsoft, aby zwiększyć Twoje zabezpieczenia. Ten dokument zawiera opis przykładu przekonamy się jak to zrobić.

> [!NOTE]
> Informacje na temat usługi przedstawiono w tym dokumencie za pomocą przykładowego wdrożenia.  Nie jest to przewodnik krok po kroku.
>
>

## <a name="implement-the-recommendation"></a>Zaimplementuj zalecenia
1. W **zalecenia** bloku wybierz **Dodaj zaporę następnej generacji**.
   ![Dodawanie zapory nowej generacji][1]
2. W **Dodaj zaporę następnej generacji** bloku, wybierz punkt końcowy.
   ![Wybierz punkt końcowy][2]
3. Sekundy **Dodaj zaporę następnej generacji** zostanie otwarty blok. Możesz użyć istniejącego rozwiązania, jeśli jest dostępny, lub można utworzyć nowy. W tym przykładzie Brak dostępnych nie istniejących rozwiązań, dzięki czemu możemy utworzyć zapory nowej generacji.
   ![Tworzenie zapory nowej generacji][3]
4. Aby utworzyć zapory nowej generacji, wybierz rozwiązanie z listy zintegrowanych partnerów. W tym przykładzie wybierzemy **Check Point**.
   ![Wybierz rozwiązanie zapory nowej generacji][4]
5. **Check Point** zostanie otwarty blok udostępnia informacji na temat rozwiązania partnerskiego. Wybierz **Utwórz** w bloku informacji.
   ![Blok informacji Zapora][5]
6. **Utwórz maszynę wirtualną** zostanie otwarty blok. W tym miejscu można wprowadzić informacje wymagane do uruchomienia maszyny wirtualnej (VM), uruchomionym zapory nowej generacji. Postępuj zgodnie z instrukcjami i podaj wymagane informacje zapory nowej generacji. Wybierz przycisk OK, aby zastosować.
   ![Tworzenie maszyny wirtualnej, aby uruchomić zapory nowej generacji][6]

## <a name="route-traffic-through-ngfw-only"></a>Kierowanie ruchu sieciowego tylko za pośrednictwem zapory następnej generacji
Wróć do **zalecenia** bloku. Wygenerowano nowy wpis, po dodaniu zapory nowej generacji za pośrednictwem Centrum zabezpieczeń, o nazwie **kierowania ruchu przez zaporę nowej generacji tylko**. To zalecenie jest tworzone tylko wtedy, gdy zainstalowano usługi zapory nowej generacji za pomocą usługi Security Center. Jeśli masz punktami końcowymi połączone z Internetem, usługa Security Center zaleca, aby skonfigurować reguły sieciowej grupy zabezpieczeń, które wymuszają ruch przychodzący do maszyny Wirtualnej za pośrednictwem swojej zapory nowej generacji.

1. W **blok zalecenia**, wybierz opcję **kierowania ruchu przez zaporę nowej generacji tylko**.
   ![Kierowanie ruchu sieciowego tylko za pośrednictwem zapory następnej generacji][7]
2. Spowoduje to otwarcie bloku **kierowania ruchu przez zaporę nowej generacji tylko**, który zawiera listę maszyn wirtualnych, które może kierować ruch do. Wybierz maszynę wirtualną z listy.
   ![Wybierz maszynę Wirtualną][8]
3. Zostanie otwarty blok dla wybranej maszyny Wirtualnej zawierający powiązane reguły dla ruchu przychodzącego. Opis zapewnia więcej informacji na temat wykonać następujące czynności. Wybierz **Edytuj reguły dla ruchu przychodzącego** kontynuować edytowanie reguły ruchu przychodzącego. Oczekuje się, że **źródła** nie jest ustawiony na **wszelkie** dla punktów końcowych dostępnego z Internetu, połączone za pomocą zapory nowej generacji. Aby dowiedzieć się więcej na temat właściwości reguły dla ruchu przychodzącego, zobacz [reguły zabezpieczeń](../virtual-network/security-overview.md#security-rules).
   ![Skonfiguruj reguły, aby ograniczyć dostęp][9]
   ![Edytuj reguły dla ruchu przychodzącego][10]

## <a name="see-also"></a>Zobacz także
W tym dokumencie pokazano sposób implementacji zalecenia usługi Security Center "Dodaj zaporę następnej generacji". Aby dowiedzieć się więcej na temat NGFWs i rozwiązania partnerskiego Check Point, zobacz następujące tematy:

* [Zapory następnej generacji](https://en.wikipedia.org/wiki/Next-Generation_Firewall)
* [Check Point vSEC](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/checkpoint.vsec)

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md) — informacje o sposobie konfigurowania zasad zabezpieczeń.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md) — Dowiedz się, w jaki sposób zalecenia ułatwiają ochronę zasobów platformy Azure.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md) — informacje o sposobie monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich w Centrum zabezpieczeń Azure](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń platformy Azure i zgodności.

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
