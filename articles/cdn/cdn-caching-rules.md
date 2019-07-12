---
title: Kontrolowanie zachowania buforowania przy użyciu reguł buforowania w usłudze Azure CDN | Dokumentacja firmy Microsoft
description: Zasady buforowania usługi CDN umożliwia ustawić lub zmodyfikować domyślne zachowanie wygaśnięcia pamięci podręcznej, globalnie i za pomocą warunków, takich jak ścieżki i rozszerzenia adresu URL.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: magattus
ms.openlocfilehash: d4ab3ca32f229e92ae6bae5906c6c70593e9f9d3
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594035"
---
# <a name="control-azure-cdn-caching-behavior-with-caching-rules"></a>Kontrola Azure działanie buforowania usługi CDN przy użyciu reguł buforowania

> [!NOTE] 
> Reguły buforowania są dostępne tylko dla **Azure CDN Standard from Verizon** i **Azure CDN Standard from Akamai** profilów. Dla **Azure CDN Premium from Verizon** profile, należy użyć [aparat reguł wysokiej dostępności treści Azure](cdn-rules-engine.md) w **Zarządzaj** portal dla podobne funkcje.
 
Usługa Azure Content Delivery Network (CDN) oferuje dwa sposoby kontrolowania, jak są buforowane pliki: 

- Reguły buforowania: W tym artykule opisano, jak można użyć sieci dostarczania zawartości (CDN) reguły buforowania można ustawić lub zmodyfikować domyślne zachowanie wygaśnięcia pamięci podręcznej, globalnie i za pomocą niestandardowych warunków, takich jak ścieżki i rozszerzenia adresu URL. Usługa Azure CDN oferuje dwa typy reguł buforowania:

   - Globalne reguły buforowania: Możesz ustawić jedną globalne reguły buforowania dla każdego punktu końcowego w profilu, który ma wpływ na wszystkie żądania do punktu końcowego. Globalna reguła buforowania zastępuje wszelkie nagłówki dyrektywy pamięci podręcznej HTTP, jeśli zostały one ustawione.

   - Niestandardowe reguły buforowania: Można ustawić co najmniej jeden niestandardowe reguły buforowania dla każdego punktu końcowego w Twoim profilu. Niestandardowe reguły buforowania pasują do określonych ścieżek i rozszerzeń plików, są przetwarzane kolejno i zastępują globalną regułę buforowania, jeśli została ustawiona. 

- Buforowanie ciągu zapytania: Można dostosować, jak usługa Azure CDN traktuje buforowania żądań z ciągami zapytań. Aby uzyskać informacje, zobacz [kontroli usługi Azure CDN caching zachowanie z ciągami zapytań](cdn-query-string.md). Jeśli plik jest nie podlega buforowaniu, ustawienia buforowania ciągów zapytań nie ma znaczenia, oparte na pamięci podręcznej reguły i zachowania domyślne usługi CDN.

Aby dowiedzieć się, domyślne zachowanie buforowania i nagłówki dyrektywy buforowania, zobacz [jak działa buforowanie](cdn-how-caching-works.md). 


## <a name="accessing-azure-cdn-caching-rules"></a>Uzyskiwanie dostępu do usługi Azure CDN reguły buforowania

1. Otwórz witrynę Azure portal, wybierz profil usługi CDN, a następnie wybierz punkt końcowy.

2. W lewym okienku w obszarze Ustawienia zaznacz pole **Reguły buforowania**.

   ![Przycisk Reguły buforowania usługi CDN](./media/cdn-caching-rules/cdn-caching-rules-btn.png)

   Zostanie wyświetlona strona **Reguły buforowania**.

   ![Strona Reguły buforowania usługi CDN](./media/cdn-caching-rules/cdn-caching-rules-page.png)


## <a name="caching-behavior-settings"></a>Ustawienia zachowania buforowania
W przypadku globalnych i niestandardowych regułach buforowania, można określić następujące **zachowanie buforowania** ustawienia:

- **Pomiń pamięć podręczną**: Nie w pamięci podręcznej i Zignoruj warunkiem do źródła nagłówki z dyrektywami.

- **Zastąp**: Zignoruj warunkiem do źródła czas trwania pamięci podręcznej; Zamiast tego użyj czas podany pamięci podręcznej. Nie spowoduje to zastąpienie nagłówka cache-control: nie-cache.

- **Ustaw, jeśli brak**: Uwzględnić dostarczone do źródła nagłówki z dyrektywami, jeśli takie istnieją; w przeciwnym razie użyj czas podany pamięci podręcznej.

![Globalne reguły buforowania](./media/cdn-caching-rules/cdn-global-caching-rules.png)

![Niestandardowe reguły buforowania](./media/cdn-caching-rules/cdn-custom-caching-rules.png)

## <a name="cache-expiration-duration"></a>Czas wygasania pamięci podręcznej
Dla globalnych i niestandardowych regułach buforowania można określić czas wygasania pamięci podręcznej w dni, godziny, minuty i sekundy:

- Aby uzyskać **zastąpienia** i **Ustaw, jeśli brak** **zachowanie buforowania** ustawienia, prawidłową pamięć podręczną czasów trwania zakresu między 0 sekund i 366 dni. Dla wartości 0 sekund usługa CDN buforuje zawartość, ale musi przechowywać poszczególne żądania z serwera pochodzenia.

- Aby uzyskać **Pomiń pamięć podręczną** , czas trwania pamięci podręcznej jest automatycznie ustawiana na 0 sekund i nie można jej zmienić.

## <a name="custom-caching-rules-match-conditions"></a>Buforowanie niestandardowe reguły warunki dopasowań

Dla reguły niestandardowej pamięci podręcznej dostępne są dopasowanie dwa warunki:
 
- **Ścieżka**: Ten warunek odpowiada Ścieżka adresu URL, z wyjątkiem nazwy domeny i obsługuje symbol wieloznaczny (\*). Na przykład _/myfile.html_, _/Moje/folder / *_ , i _/my/images/*.jpg_. Maksymalna długość wynosi 260 znaków.

- **Rozszerzenie**: Ten stan zastępuje rozszerzenie pliku żądanego pliku. Możesz podać listę rozszerzeń plików rozdzielanych przecinkami do dopasowania. Na przykład _.jpg_, _.mp3_, lub _.png_. Maksymalna liczba rozszerzeń wynosi 50, a maksymalna liczba znaków w rozszerzeniu to 16. 

## <a name="global-and-custom-rule-processing-order"></a>Kolejność przetwarzania reguły globalnych i niestandardowych
Globalne i niestandardowych regułach buforowania są przetwarzane w następującej kolejności:

- Globalne reguły buforowania mają pierwszeństwo przed zachowanie buforowania usługi CDN domyślne (ustawienia dyrektywami nagłówka HTTP). 

- Niestandardowe reguły buforowania pierwszeństwo globalne reguły buforowania, gdzie mają one zastosowanie. Niestandardowe reguły buforowania są przetwarzane w kolejności od góry do dołu. Oznacza to jeśli żądanie spełnia oba warunki, reguły w dolnej części listy pierwszeństwo reguły w górnej części listy. W związku z tym należy umieścić bardziej szczegółowych reguł niższe na liście.

**Przykład**:
- Globalne reguły buforowania: 
   - Zachowanie buforowania: **zastąpienie**
   - Czas wygasania pamięci podręcznej: 1 dzień

- Buforowanie niestandardowe reguły #1:
   - Warunek dopasowania: **Path**
   - Odpowiada wartości:   _/home / *_
   - Zachowanie buforowania: **zastąpienie**
   - Czas wygasania pamięci podręcznej: 2 dni

- Buforowanie niestandardowe reguły #2:
   - Warunek dopasowania: **Rozszerzenie**
   - Odpowiada wartości: _HTML_
   - Zachowanie buforowania: **Ustaw, jeśli brak**
   - Czas wygasania pamięci podręcznej: 3 dni

Gdy te zasady są ustawione, żądanie  _&lt;nazwę hosta punktu końcowego&gt;_ wyzwalaczy.azureedge.net/home/index.html buforowanie niestandardowe reguły #2, która jest ustawiona na: **Ustaw, jeśli brak** i 3 dni. W związku z tym jeśli *index.html* plik ma `Cache-Control` lub `Expires` nagłówków HTTP, są one uwzględniane; w przeciwnym razie, jeśli nie ustawiono tych nagłówków, plik jest buforowany do 3 dni.

> [!NOTE] 
> Pliki, które są buforowane, przed zmianą reguły utrzymywać ich ustawienie czas trwania pamięci podręcznej pochodzenia. Aby zresetować ich czas trwania pamięci podręcznej, należy najpierw [przeczyścić pliku](cdn-purge-endpoint.md). 
>
> Zmiany konfiguracji w usłudze Azure CDN może zająć trochę czasu, do propagowania za pośrednictwem sieci: 
> - W przypadku profili usługi **Azure CDN Standard from Akamai** propagacja zwykle trwa mniej niż jedną minutę. 
> - Aby uzyskać **Azure CDN Standard from Verizon** profile, Propagacja zwykle zostanie ukończone w 10 minut.  
>

## <a name="see-also"></a>Zobacz także

- [Jak działa buforowanie](cdn-how-caching-works.md)
- [Samouczek: ustawianie reguł buforowania usługi Azure CDN](cdn-caching-rules-tutorial.md)
