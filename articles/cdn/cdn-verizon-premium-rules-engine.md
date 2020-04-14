---
title: Zastępowanie zachowania HTTP za pomocą usługi Azure CDN — aparat reguł Verizon Premium
description: Aparat reguł umożliwia dostosowanie sposobu obsługi żądań HTTP przez usługę Azure CDN z usługi Verizon Premium, takich jak blokowanie dostarczania niektórych typów zawartości, definiowanie zasad buforowania i modyfikowanie nagłówków HTTP.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: allensu
ms.openlocfilehash: 2e5e4265f30631f8e68d8f9e7156ea578ae85e7a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253479"
---
# <a name="override-http-behavior-using-the-azure-cdn-from-verizon-premium-rules-engine"></a>Zastępowanie zachowania HTTP przy użyciu aparatu reguł usługi Azure CDN z usługi Verizon Premium

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Omówienie

Aparat reguł usługi Azure CDN umożliwia dostosowanie sposobu obsługi żądań HTTP. Na przykład blokowanie dostarczania niektórych typów zawartości, definiowanie zasad buforowania lub modyfikowanie nagłówka HTTP. W tym samouczku pokazano, jak utworzyć regułę, która zmienia zachowanie buforowania zasobów sieci CDN. Aby uzyskać więcej informacji na temat składni aparatu reguł, zobacz [odwołanie do aparatu reguł usługi Azure CDN](cdn-verizon-premium-rules-engine-reference.md).

## <a name="access"></a>Dostęp

Aby uzyskać dostęp do aparatu reguł, należy najpierw wybrać **opcję Zarządzaj** z górnej strony **profilu sieci CDN,** aby uzyskać dostęp do strony zarządzania siecią CDN platformy Azure. W zależności od tego, czy punkt końcowy jest zoptymalizowany pod kątem dynamicznego przyspieszania lokacji (DSA), następnie można uzyskać dostęp do aparatu reguł z zestawem reguł odpowiednich dla danego typu punktu końcowego:

- Punkty końcowe zoptymalizowane pod kątem ogólnego dostarczania w sieci Web lub innej optymalizacji innej niż DSA:
    
    Wybierz kartę **Duży HTTP,** a następnie wybierz pozycję **Aparat reguł**.

    ![Aparat reguł dla protokołu HTTP](./media/cdn-rules-engine/cdn-http-rules-engine.png)

- Punkty końcowe zoptymalizowane pod kątem DSA:
    
    Wybierz kartę **ADN,** a następnie wybierz pozycję **Aparat reguł**.
    
    ADN jest terminem używanym przez Verizon do określania zawartości DSA. Wszystkie reguły utworzone w tym miejscu są ignorowane przez wszystkie punkty końcowe w profilu, które nie są zoptymalizowane pod kątem DSA.

    ![Aparat reguł dla DSA](./media/cdn-rules-engine/cdn-dsa-rules-engine.png)

## <a name="tutorial"></a>Samouczek

1. Na stronie **profilu sieci CDN** wybierz pozycję **Zarządzaj**.
   
    ![Przycisk Zarządzanie profilem CDN](./media/cdn-rules-engine/cdn-manage-btn.png)
   
    Zostanie otwarty portal zarządzania siecią CDN.

2. Wybierz kartę **Duży HTTP,** a następnie wybierz pozycję **Aparat reguł**.
   
    Zostaną wyświetlone opcje nowej reguły.
   
    ![Nowe opcje reguł sieci CDN](./media/cdn-rules-engine/cdn-new-rule.png)
   
   > [!IMPORTANT]
   > Kolejność, w której wiele reguł są wymienione wpływa na sposób ich obsługi. Kolejna reguła może zastąpić akcje określone przez poprzednią regułę.
   >

3. Wprowadź nazwę w polu tekstowym **Nazwa / Opis.**

4. Określ typ żądań, do które ma zastosowanie reguła. Użyj domyślnego warunku **dopasowania, Zawsze**.
   
   ![Warunek dopasowania reguły sieci CDN](./media/cdn-rules-engine/cdn-request-type.png)
   
   > [!NOTE]
   > Na liście rozwijanej dostępnych jest wiele warunków dopasowania. Aby uzyskać informacje o aktualnie wybranym warunku dopasowania, wybierz niebieską ikonę informacyjną po lewej stronie.
   >
   >  Aby uzyskać szczegółową listę wyrażeń warunkowych, zobacz [Reguły aparat wyrażeń warunkowych](cdn-verizon-premium-rules-engine-reference-match-conditions.md).
   >  
   > Aby uzyskać szczegółową listę warunków dopasowania, zobacz [Zasady warunków dopasowania silnika](cdn-verizon-premium-rules-engine-reference-match-conditions.md).
   >
   >

5. Aby dodać nową funkcję, **+** wybierz przycisk obok **pozycji Funkcje**.  W menu po lewej stronie wybierz pozycję **Wymuń wewnętrzny maksymalny wiek**.  W wyświetlonym textboxie wprowadź **300**. Nie należy zmieniać pozostałych wartości domyślnych.
   
   ![Funkcja reguły sieci CDN](./media/cdn-rules-engine/cdn-new-feature.png)
   
   > [!NOTE]
   > Na liście rozwijanej dostępnych jest wiele funkcji. Aby uzyskać informacje o aktualnie wybranej funkcji, wybierz niebieską ikonę informacyjną po lewej stronie.
   >
   > W przypadku **wymuszenia wewnętrznego maksymalnego wieku**zasoby `Cache-Control` i `Expires` nagłówki są zastępowane do kontrolowania, gdy węzeł krawędzi sieci CDN odświeża zasób ze źródła. W tym przykładzie węzeł krawędzi sieci CDN buforuje zasób przez 300 sekund lub 5 minut, zanim odświeży zasób z jego początku.
   >
   > Aby uzyskać szczegółową listę funkcji, zobacz [Reguły funkcji aparatu](cdn-verizon-premium-rules-engine-reference-features.md).
   >
   >

6. Wybierz **pozycję Dodaj,** aby zapisać nową regułę.  Nowa reguła oczekuje teraz na zatwierdzenie. Po zatwierdzeniu stan zmieni się z **Oczekującego XML na** **Aktywny XML**.
   
   > [!IMPORTANT]
   > Rozpropu propagowanie zmian reguł może potrwać do 10 minut za pośrednictwem usługi Azure CDN.
   >
   >

## <a name="see-also"></a>Zobacz też

- [Omówienie usługi Azure CDN](cdn-overview.md)
- [Odwołanie do aparatu reguł](cdn-verizon-premium-rules-engine-reference.md)
- [Zasady warunków dopasowania silnika](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Reguły wyrażeń warunkowych aparatu](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Reguły funkcji aparatu](cdn-verizon-premium-rules-engine-reference-features.md)
- [Piątki platformy Azure: nowe zaawansowane funkcje premium usługi Azure CDN](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/) (wideo)