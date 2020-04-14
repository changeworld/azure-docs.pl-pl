---
title: Sterowanie zachowaniem buforowania usługi Azure CDN za pomocą ciągów zapytań — warstwa premium
description: Buforowanie ciągów zapytań usługi Azure CDN określa sposób buforowania plików, gdy żądanie sieci web zawiera ciąg zapytania. W tym artykule opisano buforowanie ciągów zapytań w usłudze Azure CDN Premium z produktu Verizon.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: allensu
ms.openlocfilehash: 94949a31db5321929a3440281cebd01712c79bb8
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260143"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---premium-tier"></a>Sterowanie zachowaniem buforowania usługi Azure CDN za pomocą ciągów zapytań — warstwa premium
> [!div class="op_single_selector"]
> * [Warstwa standardowa](cdn-query-string.md)
> * [Warstwa Premium](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Omówienie
Za pomocą usługi Azure Content Delivery Network (CDN) można kontrolować sposób buforowania plików dla żądania sieci web zawierającego ciąg zapytania. W żądaniu sieci web z ciągiem zapytania ciąg zapytania jest tą częścią żądania, która występuje po znaku zapytania (?). Ciąg zapytania może zawierać jedną lub więcej par klucz-wartość, w których nazwa pola i jego wartość są oddzielone znakiem równości (=). Każda para klucz-wartość jest oddzielona ampersand (&). Na przykład http:\//www.contoso.com/content.mov?field1=value1&field2=value2. Jeśli istnieje więcej niż jedna para klucz-wartość w ciągu zapytania żądania, ich kolejność nie ma znaczenia. 

> [!IMPORTANT]
> Standardowe i premium produkty CDN zapewniają tę samą funkcję buforowania ciągów zapytań, ale interfejs użytkownika jest inny. W tym artykule opisano interfejs **usługi Azure CDN Premium firmy Verizon**. Aby uzyskać informacje o buforowaniu ciągów zapytań za pomocą standardowych produktów usługi Azure CDN, zobacz [Kontrolowanie zachowania buforowania usługi Azure CDN za pomocą ciągów zapytań — warstwa standardowa](cdn-query-string.md).
>


Dostępne są trzy tryby ciągu zapytania:

- **standardowa pamięć podręczna:** Tryb domyślny. W tym trybie węzeł punktu obecności sieci CDN (POP) przekazuje ciągi zapytań od żądacza do serwera pochodzenia przy pierwszym żądaniu i buforuje zasób. Wszystkie kolejne żądania dla zasobu, które są obsługiwane z serwera POP ignorują ciągi zapytań, dopóki zasób w pamięci podręcznej nie wygaśnie.

    >[!IMPORTANT] 
    > Jeśli autoryzacja tokenu jest włączona dla dowolnej ścieżki na tym koncie, tryb standardowej pamięci podręcznej jest jedynym trybem, który może być używany. 

- **no-cache:** W tym trybie żądania z ciągami zapytań nie są buforowane w węźle POP sieci CDN. Węzeł POP pobiera zasób bezpośrednio z serwera pochodzenia i przekazuje go do żądacza przy każdym żądaniu.

- **unique-cache:** W tym trybie każde żądanie z unikatowym adresem URL, w tym ciągiem zapytania, jest traktowane jako unikatowy zasób z własną pamięcią podręczną. Na przykład odpowiedź z serwera pochodzenia dla żądania na przykład.ashx?q=test1 jest buforowana w węźle POP i zwracana dla kolejnych pamięci podręcznych z tym samym ciągiem zapytania. Żądanie na przykład.ashx?q=test2 jest buforowane jako oddzielny zasób z własnym ustawieniem czasu na żywo.
   
    >[!IMPORTANT] 
    > Nie należy używać tego trybu, gdy ciąg zapytania zawiera parametry, które będą się zmieniać przy każdym żądaniu, takie jak identyfikator sesji lub nazwa użytkownika, ponieważ spowoduje to niski współczynnik trafień pamięci podręcznej.

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Zmienianie ustawień buforowania ciągów zapytań dla profilów sieci CDN premium
1. Otwórz profil sieci CDN, a następnie kliknij przycisk **Zarządzaj**.
   
    ![Przycisk Zarządzanie profilem CDN](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    Zostanie otwarty portal zarządzania siecią CDN.
2. Umieść wskaźnik myszy na karcie **Duży http,** a następnie umieść wskaźnik myszy na menu wysuwu **ustawień pamięci podręcznej.** Kliknij **pozycję Buforowanie ciągów kwerend**.
   
    Wyświetlane są opcje buforowania ciągów zapytań.
   
    ![Opcje buforowania ciągów zapytań cdn](./media/cdn-query-string-premium/cdn-query-string.png)
3. Wybierz tryb ciągu zapytania, a następnie kliknij przycisk **Aktualizuj**.

> [!IMPORTANT]
> Ponieważ wymaga czasu, aby rejestracja propagować za pośrednictwem sieci CDN, zmiany ustawień ciągu pamięci podręcznej może nie być natychmiast widoczne. Propagacja zwykle kończy się w ciągu 10 minut.
 

