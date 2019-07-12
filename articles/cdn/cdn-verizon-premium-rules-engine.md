---
title: Zastępowanie zachowania HTTP przy użyciu Azure CDN from Verizon — Premium aparat reguł | Dokumentacja firmy Microsoft
description: Aparat reguł pozwala dostosować sposób obsługi żądań HTTP przez Azure CDN from Verizon — wersja Premium, takich jak blokowanie dostarczania określonych typów zawartości, definiowania zasad buforowania i modyfikowanie nagłówków HTTP.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: fa08f59f3b69395d548649bb131e04c5071c053d
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593175"
---
# <a name="override-http-behavior-using-the-azure-cdn-from-verizon-premium-rules-engine"></a>Zastępowanie zachowania HTTP przy użyciu Azure CDN from Verizon — Premium aparatu reguł

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Omówienie

Aparat reguł sieć CDN systemu Azure pozwala dostosować sposób obsługi żądań HTTP. Na przykład blokowanie dostarczania określonych typów zawartości, definiowanie zasad buforowania lub modyfikowanie nagłówków HTTP. W tym samouczku pokazano, jak utworzyć regułę, która zmienia zachowanie buforowania zasobów sieci CDN. Aby uzyskać więcej informacji na temat składni aparatu reguł zobacz [dokumentacja aparatu reguł wysokiej dostępności treści Azure](cdn-verizon-premium-rules-engine-reference.md).

## <a name="access"></a>Access

Aby uzyskać dostęp do aparatu reguł, musisz najpierw wybrać **Zarządzaj** od górnej krawędzi **profil CDN** strony, aby uzyskać dostęp do strony zarządzania usługi Azure CDN. W zależności od tego, czy punkt końcowy usługi jest zoptymalizowanej pod kątem przyspieszania witryn dynamicznych (DSA) można następnie dostęp do aparatu reguł za pomocą zbioru reguł, które są odpowiednie dla danego typu punktu końcowego:

- Punkty końcowe zoptymalizowane pod kątem ogólne dostarczanie w Internecie lub innych optymalizacji-DSA:
    
    Wybierz **HTTP dużych** kartę, a następnie wybierz **aparat reguł**.

    ![Aparat reguł dla protokołu HTTP](./media/cdn-rules-engine/cdn-http-rules-engine.png)

- Punkty końcowe zoptymalizowane pod kątem DSA:
    
    Wybierz **sieci ADN** kartę, a następnie wybierz **aparat reguł**.
    
    Sieci ADN to termin używany przez firmy Verizon, aby określić zawartość DSA. Wszystkie reguły tworzone w tym miejscu są ignorowane przez wszystkie punkty końcowe w Twoim profilu, które nie są zoptymalizowane pod kątem DSA.

    ![Aparat reguł technologię DSA](./media/cdn-rules-engine/cdn-dsa-rules-engine.png)

## <a name="tutorial"></a>Samouczek

1. Z **profil CDN** wybierz opcję **Zarządzaj**.
   
    ![Przycisk Zarządzaj w profilu CDN](./media/cdn-rules-engine/cdn-manage-btn.png)
   
    Zostanie otwarty w portalu zarządzania usługi CDN.

2. Wybierz **HTTP dużych** kartę, a następnie wybierz **aparat reguł**.
   
    Opcje dla nowej reguły są wyświetlane.
   
    ![Nowe opcje reguł sieci CDN](./media/cdn-rules-engine/cdn-new-rule.png)
   
   > [!IMPORTANT]
   > Kolejność, w którym są wyświetlane wiele reguł ma wpływ na sposób obsługi. Kolejne reguły mogą zastąpić akcji określonych przez poprzednią regułę.
   >

3. Wprowadź nazwę w **nazwę / opis** pola tekstowego.

4. Określ typ żądania, których dotyczy reguła. Użyj domyślnego warunku dopasowania, **zawsze**.
   
   ![Warunek dopasowania reguły usługi CDN](./media/cdn-rules-engine/cdn-request-type.png)
   
   > [!NOTE]
   > Wiele warunków dopasowania są dostępne na liście rozwijanej. Dla informacji dotyczących warunku dopasowania zaznaczony wybierz niebieski informacyjną ikony po lewej stronie.
   >
   >  Aby uzyskać szczegółową listę wyrażeń warunkowych, zobacz [wyrażenia warunkowe aparatu reguł](cdn-verizon-premium-rules-engine-reference-match-conditions.md).
   >  
   > Aby uzyskać szczegółową listę warunków dopasowania, zobacz [warunki dopasowań aparatu reguł](cdn-verizon-premium-rules-engine-reference-match-conditions.md).
   >
   >

5. Aby dodać nową funkcję, wybierz **+** znajdujący się obok **funkcji**.  Na liście rozwijanej po lewej stronie wybierz **życie wewnętrznego Max-Age**.  W wyświetlonym polu tekstowym wprowadź **300**. Nie zmieniaj pozostałe wartości domyślne.
   
   ![Funkcja reguły usługi CDN](./media/cdn-rules-engine/cdn-new-feature.png)
   
   > [!NOTE]
   > Wiele funkcji są dostępne na liście rozwijanej. Aby uzyskać informacje o aktualnie wybranej funkcji wybierz niebieski informacyjną ikony po lewej stronie.
   >
   > Dla **życie wewnętrznego Max-Age**, zasobu `Cache-Control` i `Expires` nagłówki są zastępowane do kontrolowania, kiedy węzła krawędzi sieci CDN odświeża zawartości ze źródła. W tym przykładzie węzła krawędzi sieci CDN buforuje zasobów przez 300 sekund lub 5 minut, zanim odświeżania zasobów na podstawie pochodzenia.
   >
   > Aby uzyskać szczegółową listę funkcji, zobacz [funkcje aparatu reguł](cdn-verizon-premium-rules-engine-reference-features.md).
   >
   >

6. Wybierz **Dodaj** Aby zapisać nową regułę.  Nowa reguła teraz oczekuje na zatwierdzenie. Po jego zatwierdzeniu stan zmienia się ze **oczekujące XML** do **Active XML**.
   
   > [!IMPORTANT]
   > Zasady zmian może potrwać do 10 minut propagować przez sieć CDN systemu Azure.
   >
   >

## <a name="see-also"></a>Zobacz także

- [Omówienie usługi Azure CDN](cdn-overview.md)
- [Dokumentacja aparatu reguł](cdn-verizon-premium-rules-engine-reference.md)
- [Warunki dopasowań aparatu reguł](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Wyrażenia warunkowe aparatu reguł](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Funkcje aparatu reguł](cdn-verizon-premium-rules-engine-reference-features.md)
- [Piątki z Windows Azure: Zaawansowane nowe funkcje premium usługi Azure CDN](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/) (wideo)