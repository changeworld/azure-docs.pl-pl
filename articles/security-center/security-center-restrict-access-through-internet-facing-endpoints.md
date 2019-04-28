---
title: Ograniczanie dostępu przez punkty końcowe łączące dostępnego z Internetu w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: W tym dokumencie przedstawiono sposób realizacji zalecenia w usłudze Azure Security Center **ograniczyć dostęp za pośrednictwem punktu końcowego z Internetem**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 727d88c9-163b-4ea0-a4ce-3be43686599f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: b736bb5549b7d236e746ba7b161cde79209e927b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60906584"
---
# <a name="restrict-access-through-internet-facing-endpoints-in-azure-security-center"></a>Ograniczanie dostępu przez punkty końcowe łączące dostępnego z Internetu w usłudze Azure Security Center
Usługa Azure Security Center zaleci, ograniczanie dostępu przez punkty końcowe dostępnego z Internetu, jeśli którakolwiek z sieciowymi grupami zabezpieczeń (NSG) z nich ma co najmniej jedną regułę ruchu przychodzącego zezwalające na dostęp z "Dowolna" źródłowy adres IP. Otwieranie dla "dowolnej" mogą umożliwić osobom atakującym dostęp do zasobów. Usługa Security Center oferuje rekomendacje dotyczące edytowania tych reguł dla ruchu przychodzącego, aby ograniczyć dostęp do źródłowych adresów IP, które faktycznie muszą mieć dostęp.

To zalecenie jest generowany dla dowolnego portu innego niż w sieci web zawierającej "dowolne" jako źródło.

> [!NOTE]
> Informacje na temat usługi przedstawiono w tym dokumencie za pomocą przykładowego wdrożenia. Nie jest to przewodnik krok po kroku.
>
>

## <a name="implement-the-recommendation"></a>Zaimplementuj zalecenia
1. W **blok zalecenia**, wybierz opcję **ograniczyć dostęp za pośrednictwem punktu końcowego z Internetem**.

   ![Ogranicz dostęp za pośrednictwem punktu końcowego mającego połączenie z Internetem][1]
2. Spowoduje to otwarcie bloku **ograniczyć dostęp za pośrednictwem punktu końcowego z Internetem**. Ten blok zawiera listę maszyn wirtualnych (VM) za pomocą reguł ruchu przychodzącego, które tworzyć potencjalny problem z zabezpieczeniami. Wybierz maszynę Wirtualną.

   ![Wybierz maszynę Wirtualną][2]
3. **Sieciowej grupy zabezpieczeń** bloku wyświetlane są informacje, powiązane reguły dla ruchu przychodzącego sieciowej grupy zabezpieczeń i skojarzonej maszyny Wirtualnej. Wybierz **Edytuj reguły dla ruchu przychodzącego** kontynuować edytowanie reguły ruchu przychodzącego.

   ![Blok sieciowej grupy zabezpieczeń][3]
4. Na **reguły zabezpieczeń dla ruchu przychodzącego** bloku Wybierz reguły dla ruchu przychodzącego do edycji. W tym przykładzie wybierzemy **AllowWeb**.

   ![Reguły zabezpieczeń dla ruchu przychodzącego][4]

   Należy pamiętać, że możesz również wybrać **domyślne reguły** aby zobaczyć zestaw reguł domyślnych zawarte przez wszystkie sieciowe grupy zabezpieczeń. Nie można usunąć reguł domyślnych, ale ponieważ mają przypisany o niższym priorytecie, mogą być zastąpione przez zasady, które tworzysz. Dowiedz się więcej o [domyślne reguły](../virtual-network/security-overview.md#default-security-rules).

   ![Reguły domyślne][5]
5. Na **AllowWeb** bloku Edycja właściwości reguły dla ruchu przychodzącego, aby **źródła** jest adres IP lub blok adresów IP. Aby dowiedzieć się więcej na temat właściwości reguły dla ruchu przychodzącego, zobacz [reguły sieciowej grupy zabezpieczeń](../virtual-network/security-overview.md#security-rules).

   ![Edytuj reguły dla ruchu przychodzącego][6]

## <a name="see-also"></a>Zobacz także
W tym artykule pokazano sposób implementacji zalecenia usługi Security Center "Ogranicz dostęp za pośrednictwem punktu końcowego dostępnego z Internetu". Aby dowiedzieć się więcej na temat włączania sieciowych grup zabezpieczeń i reguł, zobacz następujące tematy:

* [Co to jest sieciowa grupa zabezpieczeń?](../virtual-network/security-overview.md)
* [Zarządzanie sieciową grupę zabezpieczeń](../virtual-network/manage-network-security-group.md)

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w Centrum zabezpieczeń Azure](tutorial-security-policy.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Centrum zabezpieczeń Azure](security-center-recommendations.md) — informacje o tym, w jaki sposób zalecenia ułatwiają ochronę zasobów platformy Azure.
* [Monitorowanie kondycji zabezpieczeń w Centrum zabezpieczeń Azure](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w Centrum zabezpieczeń Azure](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich w Centrum zabezpieczeń Azure](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Centrum zabezpieczeń Azure — często zadawane pytania](security-center-faq.md) — odpowiedzi na najczęstsze pytania dotyczące korzystania z usługi.
* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/) — najnowsze informacje na temat zabezpieczeń platformy Azure.

<!--Image references-->
[1]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/restrict-access-thru-internet-facing-endpoint.png
[2]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/select-a-vm.png
[3]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/network-security-group-blade.png
[4]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/inbound-security-rules.png
[5]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/default-rules.png
[6]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/edit-inbound-rule.png
