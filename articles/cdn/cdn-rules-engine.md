---
title: Zastąpienie zachowania HTTP przy użyciu usługi Azure CDN aparatu reguł | Dokumentacja firmy Microsoft
description: Aparat reguł pozwala dostosować sposób obsługi żądań HTTP przez usługi Azure CDN, takich jak blokowanie dostarczania niektórych typów zawartości, definiowania zasad buforowania i modyfikować nagłówki HTTP.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: 625a912b-91f2-485d-8991-128cc194ee71
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: v-deasim
ms.openlocfilehash: df8114aaf5b4672ea51482978abde6f0ce724528
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261053"
---
# <a name="override-http-behavior-using-the-azure-cdn-rules-engine"></a>Zastąpienie zachowania HTTP przy użyciu aparatu reguł Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Przegląd
Aparat zasad usługi Azure CDN umożliwia dostosowanie, sposób obsługi żądań HTTP. Na przykład blokowanie dostarczania niektórych typów zawartości, definiowania zasad buforowania i modyfikowania nagłówka HTTP. Ten samouczek pokazuje, jak utworzyć regułę, która zmienia zachowanie buforowania zasobów w sieci CDN. Aby uzyskać więcej informacji na temat składni aparatu reguł, zobacz [zasady usługi Azure CDN aparat odwołanie](cdn-rules-engine-reference.md).

## <a name="access"></a>Dostęp
Aby uzyskać dostęp do aparatu reguł, należy najpierw wybrać **Zarządzaj** od góry **profilu CDN** stronę, aby uzyskać dostęp do strony zarządzania usługi Azure CDN. W zależności od tego, czy punkt końcowy jest zoptymalizowany do przyspieszania dynamiczne witryny (DSA) aby następnie dostęp do aparatu reguł z zestaw reguł, które są odpowiednie dla danego typu punktu końcowego:

- Punkty końcowe zoptymalizowane pod kątem dostarczania ogólne sieci web lub innych optymalizacji-DSA: 
    
    Wybierz **HTTP dużych** , a następnie wybierz **aparatu reguł**.

    ![Aparat reguł dla protokołu HTTP](./media/cdn-rules-engine/cdn-http-rules-engine.png)

- Punkty końcowe zoptymalizowane pod kątem DSA: 
    
    Wybierz **sieci ADN** , a następnie wybierz **aparatu reguł**. 
    
    Sieci ADN to termin używany przez Verizon do określenia zawartości DSA. Wszystkie reguły tworzone w tym miejscu są ignorowane przez żadnych punktów końcowych w Twoim profilu, które nie są zoptymalizowane pod kątem DSA. 

    ![Aparat reguł dla DSA](./media/cdn-rules-engine/cdn-dsa-rules-engine.png)

## <a name="tutorial"></a>Samouczek
1. Z **profilu CDN** wybierz pozycję **Zarządzaj**.
   
    ![Przycisk Zarządzaj profil CDN](./media/cdn-rules-engine/cdn-manage-btn.png)
   
    Zostanie otwarty w portalu zarządzania usługi CDN.

2. Wybierz **HTTP dużych** , a następnie wybierz **aparatu reguł**.
   
    Opcje dla nowej reguły są wyświetlane.
   
    ![Nowe opcje reguły CDN](./media/cdn-rules-engine/cdn-new-rule.png)
   
   > [!IMPORTANT]
   > Kolejność, w którym są wyświetlane wiele reguł ma wpływ na sposób obsługi. Kolejne reguły mogą zastąpić akcje określone przez poprzednie reguły.
   > 

3. Wprowadź nazwę w **nazwę / opis** pola tekstowego.

4. Określ typ żądań, których dotyczy reguła. Użyj domyślnego warunku dopasowania **zawsze**. 
   
   ![Warunek dopasowania reguły CDN](./media/cdn-rules-engine/cdn-request-type.png)
   
   > [!NOTE]
   > Użycie wielu warunków dopasowania są dostępne na liście rozwijanej. Aby uzyskać informacje o warunku dopasowania aktualnie zaznaczonego wybierz niebieską ikoną informacyjny po lewej stronie.
   > 
   >  Aby uzyskać szczegółową listę wyrażenia warunkowego, zobacz [reguły aparat wyrażeń warunkowych](cdn-rules-engine-reference-match-conditions.md).
   >  
   > Aby uzyskać szczegółową listę warunków dopasowania, zobacz [zasady warunków dopasowania aparat](cdn-rules-engine-reference-match-conditions.md).
   > 
   > 

5. Aby dodać nową funkcję, zaznacz **+** znajdujący się obok **funkcji**.  Na liście rozwijanej po lewej stronie wybierz **życie wewnętrzny Max-Age**.  W wyświetlonym polu tekstowym wprowadź **300**. Nie należy zmieniać pozostałe wartości domyślne.
   
   ![Funkcja reguły CDN](./media/cdn-rules-engine/cdn-new-feature.png)
   
   > [!NOTE]
   > Wiele funkcji dostępnych na liście rozwijanej. Aby uzyskać informacje o aktualnie wybranej funkcji wybierz niebieską ikoną informacyjny po lewej stronie. 
   >
   > Dla **życie wewnętrzny Max-Age**, element zawartości `Cache-Control` i `Expires` nagłówki są zastąpiona w celu kontrolowania, gdy węzeł brzegowy CDN odświeża zasobu ze źródła. W tym przykładzie węzła krawędzi CDN buforuje zasobów przez 300 sekund, czyli 5 minut, przed odświeżania zasobów ze swoją witryną źródłową.
   > 
   > Aby uzyskać szczegółową listę funkcji, zobacz [reguły aparat funkcji](cdn-rules-engine-reference-features.md).
   > 
   > 

6. Wybierz **Dodaj** Aby zapisać nową regułę.  Nowa reguła teraz oczekuje na zatwierdzenie. Po jej zatwierdzeniu zmiany stanu z **oczekujące XML** do **Active XML**.
   
   > [!IMPORTANT]
   > Zmiany zasad może potrwać do 10 minut propagację za pośrednictwem usługi Azure CDN.
   > 
   > 

## <a name="see-also"></a>Zobacz także
* [Omówienie usługi Azure CDN](cdn-overview.md)
* [Dokumentacja aparatu reguł](cdn-rules-engine-reference.md)
* [Warunki dopasowań aparatu reguł](cdn-rules-engine-reference-match-conditions.md)
* [Wyrażenia warunkowe aparatu reguł](cdn-rules-engine-reference-conditional-expressions.md)
* [Funkcje aparatu reguł](cdn-rules-engine-reference-features.md)
* [Azure piątki: Usługi Azure CDN zaawansowanych nowych funkcji premium](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/) (klip wideo)