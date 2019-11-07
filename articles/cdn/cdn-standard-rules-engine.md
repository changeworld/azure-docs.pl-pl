---
title: Wymuszanie protokołu HTTPS przy użyciu aparatu reguł standardu Azure CDN | Microsoft Docs
description: Aparat reguł standardowych umożliwia dostosowywanie sposobu obsługi żądań HTTP przez Azure CDN firmy Microsoft, takich jak blokowanie dostarczania określonych typów zawartości, definiowanie zasad buforowania i modyfikowanie nagłówków HTTP.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: b24c4a04e0c02258a918ee075066d90c22ea0c75
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615925"
---
# <a name="azure-cdn-standard-rules-engine"></a>Aparat reguł Azure CDN Standard

> [!NOTE]
> Aparat reguł standardowych jest dostępny tylko dla Azure CDN firmy Microsoft. 

Aparat standardowych reguł Azure CDN umożliwia dostosowywanie sposobu obsługi żądań HTTP. Na przykład wymuszanie dostarczania zawartości przez określone protokoły, definiowanie zasad buforowania lub modyfikowanie nagłówka HTTP. W tym samouczku pokazano, jak utworzyć regułę, która będzie automatycznie przekierowywać użytkowników do protokołu HTTPS. 


## <a name="tutorial"></a>Samouczek

1. Na stronie **profil usługi CDN** na Azure CDN z profilów firmy Microsoft wybierz punkt końcowy, dla którego chcesz skonfigurować reguły.
  
2. Wybierz kartę **reguły** po lewej stronie.
   
    Zostanie wyświetlony blok reguły z regułą globalną. 
   
    [Strona nowych reguł usługi CDN ![](./media/cdn-standard-rules-engine/cdn-new-rule.png)](./media/cdn-standard-rules-engine/cdn-new-rule.png#lightbox)
   
   > [!IMPORTANT]
   > Kolejność, w której są wymienione wiele reguł, ma wpływ na sposób ich obsługi. Kolejna reguła może zastąpić akcje określone przez poprzednią regułę.
   >

3. Kliknij przycisk **Dodaj regułę** i podaj nazwę reguły. Nazwy reguł muszą rozpoczynać się od litery i zawierać tylko cyfry i litery.

4. Określ typ żądań, których dotyczy reguła. Użyj listy rozwijanej, aby wybrać warunek dopasowania **protokołu żądań** i użyć wartości **równa** **http**.
   
   [![warunek dopasowania reguły sieci CDN](./media/cdn-standard-rules-engine/cdn-match-condition.png)](./media/cdn-standard-rules-engine/cdn-match-condition.png#lightbox)
   
   > [!NOTE]
   > Na liście rozwijanej są dostępne wiele warunków dopasowywania. Aby uzyskać szczegółową listę warunków zgodności, zobacz [warunki dopasowania aparatu reguł](cdn-standard-rules-engine-match-conditions.md).
   
5. Wybierz akcję, która zostanie zastosowana do zidentyfikowanych żądań. Użyj listy rozwijanej, aby wybrać akcję **przekierowania adresu URL** , i Użyj **znalezionej wartości (302)** dla typu i **https** dla protokołu. Pozostaw puste pole, aby użyć wartości przychodzących.
   
   [Akcja reguły ![sieci CDN](./media/cdn-standard-rules-engine/cdn-action.png)](./media/cdn-standard-rules-engine/cdn-action.png#lightbox)
   
   > [!NOTE]
   > Na liście rozwijanej są dostępne wiele akcji. Aby uzyskać szczegółową listę akcji, zobacz [reguły aparatu działania](cdn-standard-rules-engine-actions.md).

6. Wybierz pozycję **Zapisz** , aby zapisać nową regułę.  Nowa reguła zostanie teraz wdrożona.
   
   > [!IMPORTANT]
   > Azure CDN propagowanie zmian reguł może potrwać do 15 minut.
   >
   

## <a name="see-also"></a>Zobacz też

- [Przegląd Azure CDN](cdn-overview.md)
- [Dokumentacja aparatu reguł standardowych](cdn-standard-rules-engine-reference.md)
- [Warunki dopasowania aparatu reguł standardowych](cdn-standard-rules-engine-match-conditions.md)
- [Standardowe działania aparatu reguł](cdn-standard-rules-engine-actions.md)
