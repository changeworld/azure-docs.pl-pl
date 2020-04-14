---
title: Sterowanie zachowaniem buforowania usługi Azure CDN za pomocą ciągów zapytań — warstwa standardowa
description: Buforowanie ciągów zapytań usługi Azure CDN określa sposób buforowania plików, gdy żądanie sieci web zawiera ciąg zapytania. W tym artykule opisano buforowanie ciągów zapytań w standardowych produktach usługi Azure CDN.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 17410e4f-130e-489c-834e-7ca6d6f9778d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: allensu
ms.openlocfilehash: a0df9cecc4ccd09db3f6b07fa6fd4c5283753aa2
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260211"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---standard-tier"></a>Sterowanie zachowaniem buforowania usługi Azure CDN za pomocą ciągów zapytań — warstwa standardowa
> [!div class="op_single_selector"]
> * [Warstwa standardowa](cdn-query-string.md)
> * [Warstwa Premium](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Omówienie
Za pomocą usługi Azure Content Delivery Network (CDN) można kontrolować sposób buforowania plików dla żądania sieci web zawierającego ciąg zapytania. W żądaniu sieci web z ciągiem zapytania ciąg zapytania jest tą częścią żądania, która występuje po znaku zapytania (?). Ciąg zapytania może zawierać jedną lub więcej par klucz-wartość, w których nazwa pola i jego wartość są oddzielone znakiem równości (=). Każda para klucz-wartość jest oddzielona ampersand (&). Na przykład http:\//www.contoso.com/content.mov?field1=value1&field2=value2. Jeśli istnieje więcej niż jedna para klucz-wartość w ciągu zapytania żądania, ich kolejność nie ma znaczenia. 

> [!IMPORTANT]
> Produkty standardowe usługi Azure CDN i premium zapewniają tę samą funkcję buforowania ciągów zapytań, ale interfejs użytkownika jest inny. W tym artykule opisano interfejs **usługi Azure CDN Standard firmy Microsoft**, Azure **CDN Standard firmy Akamai** i **Azure CDN Standard firmy Verizon**. Aby uzyskać informacje o buforowaniu ciągów zapytań za pomocą **usługi Azure CDN Premium firmy Verizon,** zobacz [Kontrolowanie zachowania buforowania usługi Azure CDN za pomocą ciągów zapytań — warstwa premium](cdn-query-string-premium.md).

Dostępne są trzy tryby ciągu zapytania:

- **Ignoruj ciągi zapytań:** Tryb domyślny. W tym trybie węzeł punktu obecności sieci CDN (POP) przekazuje ciągi zapytań od żądacza do serwera pochodzenia przy pierwszym żądaniu i buforuje zasób. Wszystkie kolejne żądania dla zasobu, które są obsługiwane z punktu obecności ignorują ciągi zapytań, dopóki zasób w pamięci podręcznej nie wygaśnie.

- **Pomijanie buforowania ciągów zapytań:** W tym trybie żądania z ciągami zapytań nie są buforowane w węźle POP sieci CDN. Węzeł POP pobiera zasób bezpośrednio z serwera pochodzenia i przekazuje go do żądacza przy każdym żądaniu.

- **Buforuj każdy unikatowy adres URL:** W tym trybie każde żądanie z unikatowym adresem URL, w tym ciągiem zapytania, jest traktowane jako unikatowy zasób z własną pamięcią podręczną. Na przykład odpowiedź z serwera pochodzenia dla żądania na przykład.ashx?q=test1 jest buforowana w węźle POP i zwracana dla kolejnych pamięci podręcznych z tym samym ciągiem zapytania. Żądanie na przykład.ashx?q=test2 jest buforowane jako oddzielny zasób z własnym ustawieniem czasu na żywo.
   
    >[!IMPORTANT] 
    > Nie należy używać tego trybu, gdy ciąg zapytania zawiera parametry, które będą się zmieniać przy każdym żądaniu, takie jak identyfikator sesji lub nazwa użytkownika, ponieważ spowoduje to niski współczynnik trafień pamięci podręcznej.

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Zmienianie ustawień buforowania ciągów zapytań dla standardowych profilów sieci CDN
1. Otwórz profil sieci CDN, a następnie wybierz punkt końcowy sieci CDN, który chcesz zarządzać.
   
   ![Punkty końcowe profilu SIECI CDN](./media/cdn-query-string/cdn-endpoints.png)
   
2. W lewym okienku w obszarze Ustawienia kliknij pozycję **Reguły buforowania**.
   
    ![Przycisk Reguły buforowania usługi CDN](./media/cdn-query-string/cdn-caching-rules-btn.png)
   
3. Na liście **Zachowanie buforowania ciągów kwerendy** zaznacz tryb ciągu zapytania, a następnie kliknij przycisk **Zapisz**.
   
   ![Opcje buforowania ciągów zapytań cdn](./media/cdn-query-string/cdn-query-string.png)

> [!IMPORTANT]
> Ponieważ trwa czas rejestracji do propagacji za pośrednictwem usługi Azure CDN, zmiany ustawień ciągu pamięci podręcznej może nie być natychmiast widoczne:
> - W przypadku profili usługi **Azure CDN Standard from Microsoft** propagacja zwykle trwa do 10 minut. 
> - W przypadku profili usługi **Azure CDN Standard from Akamai** propagacja zwykle trwa mniej niż jedną minutę. 
> - W przypadku profilów usługi **Azure CDN Standard from Verizon** oraz usługi **Azure CDN Premium from Verizon** propagacja zwykle trwa do 10 minut. 



