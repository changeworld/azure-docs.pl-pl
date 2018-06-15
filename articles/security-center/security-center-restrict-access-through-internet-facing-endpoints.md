---
title: Ogranicz dostęp za pośrednictwem punkty końcowe skierowane do Internetu w Centrum zabezpieczeń Azure | Dokumentacja firmy Microsoft
description: Ten dokument przedstawia sposób wykonania zalecenia Centrum zabezpieczeń Azure **ograniczyć dostęp za pośrednictwem punktu końcowego internetowy**.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 727d88c9-163b-4ea0-a4ce-3be43686599f
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2017
ms.author: terrylan
ms.openlocfilehash: 92906d31f4db21f37094f192dadd080e28cc6e8e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34363057"
---
# <a name="restrict-access-through-internet-facing-endpoints-in-azure-security-center"></a>Ogranicz dostęp za pośrednictwem punkty końcowe skierowane do Internetu w Centrum zabezpieczeń Azure
Centrum zabezpieczeń Azure zaleca ograniczyć dostęp za pośrednictwem punkty końcowe skierowane do Internetu, jeśli jakakolwiek z grup zabezpieczeń sieci (NSG) ma co najmniej jednej reguły dla ruchu przychodzącego zezwalających na dostęp z "dowolny" źródłowy adres IP. Otwieranie dostępu do"" może umożliwić atakującemu dostęp do zasobów. Centrum zabezpieczeń zaleca edytowanie tych reguł ruchu przychodzącego, aby ograniczyć dostęp do źródła adresy IP, które faktycznie muszą mieć dostęp.

To zalecenie jest generowany dla dowolnego portu sieci web zawierającej "dowolne" jako źródło.

> [!NOTE]
> Informacje na temat usługi przedstawiono w tym dokumencie za pomocą przykładowego wdrożenia. Nie jest to przewodnik krok po kroku.
>
>

## <a name="implement-the-recommendation"></a>Wykonania zalecenia
1. W **bloku zalecenia**, wybierz pozycję **ograniczyć dostęp za pośrednictwem punktu końcowego internetowy**.

   ![Ogranicz dostęp za pośrednictwem punktu końcowego mającego połączenie z Internetem][1]
2. Spowoduje to otwarcie bloku **ograniczyć dostęp za pośrednictwem punktu końcowego internetowy**. Ten blok list maszynach wirtualnych (VM) z reguł ruchu przychodzącego, które utworzyć potencjalny problem z zabezpieczeniami. Wybierz maszynę Wirtualną.

   ![Wybierz maszynę Wirtualną][2]
3. **NSG** bloku Wyświetla informacje, powiązanych reguł ruchu przychodzącego i skojarzonego VM grupy zabezpieczeń sieci. Wybierz **edytowanie reguły ruchu przychodzącego** kontynuować edytowanie reguły ruchu przychodzącego.

   ![Blok grupy zabezpieczeń sieci][3]
4. Na **reguły zabezpieczeń dla ruchu przychodzącego** bloku wybierz regułę ruchu przychodzącego do edycji. W tym przykładzie załóżmy wybierz **AllowWeb**.

   ![Reguły zabezpieczeń ruchu przychodzącego][4]

   Uwaga: Możesz też wybrać **domyślne zasady** wyświetlić zestaw domyślnych reguł zawiera wszystkie grupy NSG. Nie można usunąć reguły domyślne, ale ponieważ mają przypisany o niższym priorytecie, mogą być zastąpione przez tworzone zasady. Dowiedz się więcej o [domyślne zasady](../virtual-network/security-overview.md#default-security-rules).

   ![Reguły domyślne][5]
5. Na **AllowWeb** bloku, Edytuj właściwości reguły ruchu przychodzącego, aby **źródła** jest adres IP lub blok adresów IP. Aby dowiedzieć się więcej o właściwościach reguły ruchu przychodzącego, zobacz [reguły NSG](../virtual-network/security-overview.md#security-rules).

   ![Edytowanie reguły ruchu przychodzącego][6]

## <a name="see-also"></a>Zobacz także
W tym artykule przedstawiono sposób wykonania zalecenia Centrum zabezpieczeń "Ograniczanie dostępu za pośrednictwem Internetu połączonej punktu końcowego". Aby dowiedzieć się więcej na temat włączania grup NSG i reguł, zobacz następujące tematy:

* [Co to jest sieciowa grupa zabezpieczeń?](../virtual-network/security-overview.md)
* [Zarządzanie grupy zabezpieczeń sieci](../virtual-network/manage-network-security-group.md)

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w Centrum zabezpieczeń Azure](security-center-policies.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Centrum zabezpieczeń Azure](security-center-recommendations.md) — informacje o tym, w jaki sposób zalecenia ułatwiają ochronę zasobów platformy Azure.
* [Monitorowanie kondycji zabezpieczeń w Centrum zabezpieczeń Azure](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w Centrum zabezpieczeń Azure](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich w Centrum zabezpieczeń Azure](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Centrum zabezpieczeń Azure — często zadawane pytania](security-center-faq.md) — odpowiedzi na najczęstsze pytania dotyczące korzystania z usługi.
* [Blog Azure Security](http://blogs.msdn.com/b/azuresecurity/) — najnowsze informacje na temat zabezpieczeń platformy Azure.

<!--Image references-->
[1]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/restrict-access-thru-internet-facing-endpoint.png
[2]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/select-a-vm.png
[3]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/network-security-group-blade.png
[4]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/inbound-security-rules.png
[5]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/default-rules.png
[6]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/edit-inbound-rule.png
