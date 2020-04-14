---
title: Użyj aparatu reguł, aby wymusić https w standardowej sieci CDN platformy Azure | Dokumenty firmy Microsoft
description: Użyj aparatu reguł dla usługi Microsoft Standard Azure Content Delivery Network (Azure CDN), aby dostosować sposób obsługi żądań protokołu HTTP w usłudze Azure, w tym blokowanie dostarczania niektórych typów zawartości, definiowanie zasad buforowania i modyfikowanie nagłówków HTTP. W tym artykule dowiesz się, jak utworzyć regułę, aby przekierować użytkowników do protokołu HTTPS.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: 91a442573139bf4fdd09978290bf2380c8bcb97e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259933"
---
# <a name="set-up-the-standard-rules-engine-for-azure-cdn"></a>Konfigurowanie aparatu reguł standardowych dla usługi Azure CDN

W tym artykule opisano sposób konfigurowania i używania aparatu reguł standardowych dla usługi Azure Content Delivery Network (Azure CDN).

## <a name="standard-rules-engine"></a>Silnik reguł standardowych

Aparat reguł standardowych dla usługi Azure CDN służy do dostosowywania sposobu obsługi żądań HTTP. Na przykład można użyć aparatu reguł, aby wymusić dostarczanie zawartości, aby używać określonych protokołów, definiować zasady buforowania lub zmodyfikować nagłówek HTTP. W tym artykule pokazano, jak utworzyć regułę, która automatycznie przekierowuje użytkowników do protokołu HTTPS. 

> [!NOTE]
> Aparat reguł opisany w tym artykule jest dostępny tylko dla standardowej usługi Azure CDN firmy Microsoft. 

## <a name="redirect-users-to-https"></a>Przekierowywanie użytkowników do protokołu HTTPS

1. W swoich profilach firmy Microsoft przejdź do usługi Azure Content Delivery Network.

1. Na stronie **profilu sieci CDN** wybierz punkt końcowy, dla którego chcesz utworzyć reguły.
  
1. Wybierz kartę **Aparat reguł.**
   
    Zostanie otwarte **okienko Aparat reguł** i zostanie wyświetlona lista dostępnych reguł globalnych. 
   
    [![Strona nowych reguł usługi Azure CDN](./media/cdn-standard-rules-engine/cdn-new-rule.png)](./media/cdn-standard-rules-engine/cdn-new-rule.png#lightbox)
   
   > [!IMPORTANT]
   > Kolejność, w której wiele reguł są wymienione wpływa na sposób obsługi reguł. Akcje określone w regule mogą zostać zastąpione przez kolejną regułę.
   >

1. Wybierz **pozycję Dodaj regułę** i wprowadź nazwę reguły. Nazwy reguł muszą zaczynać się od litery i zawierać tylko cyfry i litery.

1. Aby zidentyfikować typ żądań, do które ma zastosowanie reguła, utwórz warunek dopasowania:
    1. Wybierz **pozycję Dodaj warunek**, a następnie wybierz warunek dopasowania protokołu **żądania.**
    1. W polu **Operator**wybierz pozycję **Równa się**.
    1. W polu **Wartość**wybierz **HTTP**.
   
   [![Warunek dopasowania reguły usługi Azure CDN](./media/cdn-standard-rules-engine/cdn-match-condition.png)](./media/cdn-standard-rules-engine/cdn-match-condition.png#lightbox)
   
   > [!NOTE]
   > Na liście rozwijanej Dodaj **warunek** można wybrać jedną z wielu warunków dopasowania. Aby uzyskać szczegółową listę warunków dopasowania, zobacz [Dopasowywki w silniku reguł standardowych](cdn-standard-rules-engine-match-conditions.md).
   
1. Wybierz akcję, która ma być stosowana do żądań spełniających warunek dopasowania:
   1. Wybierz **pozycję Dodaj akcję**, a następnie wybierz polecenie **Przekierowanie adresu URL**.
   1. W przypadku **opcji Typ**wybierz pozycję **Znaleziono (302)**.
   1. W polu **Protocol** (Protokół) wybierz pozycję **HTTPS**.
   1. Pozostaw wszystkie inne pola puste, aby użyć wartości przychodzących.
   
   [![Akcja reguły usługi Azure CDN](./media/cdn-standard-rules-engine/cdn-action.png)](./media/cdn-standard-rules-engine/cdn-action.png#lightbox)
   
   > [!NOTE]
   > Na liście rozwijanej **Dodaj akcję** można wybrać jedną z wielu akcji. Aby uzyskać szczegółową listę akcji, zobacz [Akcje w silniku reguł standardowych](cdn-standard-rules-engine-actions.md).

6. Wybierz **pozycję Zapisz,** aby zapisać nową regułę. Reguła jest teraz dostępna do użycia.
   
   > [!IMPORTANT]
   > Propagowanie zmian reguł może potrwać do 15 minut za pośrednictwem usługi Azure CDN.
   >
   

## <a name="next-steps"></a>Następne kroki

- [Omówienie usługi Azure CDN](cdn-overview.md)
- [Dokumentacja aparatu reguł standardowych](cdn-standard-rules-engine-reference.md)
- [Warunki dopasowania w silniku reguł standardowych](cdn-standard-rules-engine-match-conditions.md)
- [Akcje w silniku reguł standardowych](cdn-standard-rules-engine-actions.md)
