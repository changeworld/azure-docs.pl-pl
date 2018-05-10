---
title: Kontrolowanie Azure CDN buforowanie z ciągami zapytań — warstwa premium | Dokumentacja firmy Microsoft
description: Ciąg zapytania usługi Azure CDN buforowanie kontroli, jak pliki są buforowane na żądania sieci web zawiera ciąg zapytania. W tym artykule opisano buforowania w Azure CDN Premium from Verizon produktu ciągu zapytania.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: mazha
ms.openlocfilehash: a1ef5f3e502b5383343dbec2c427d36054a3edc4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---premium-tier"></a>Formant Azure CDN buforowanie z ciągami zapytań — warstwa premium
> [!div class="op_single_selector"]
> * [Warstwa standardowa](cdn-query-string.md)
> * [Warstwa Premium](cdn-query-string-premium.md)
> 
> 

## <a name="overview"></a>Przegląd
Z Azure Content Delivery Network (CDN), można kontrolować, jak pliki są buforowane dla żądania sieci web, który zawiera ciąg zapytania. W ramach żądania sieci web z ciągu zapytania ciąg zapytania jest częścią żądania, która występuje po znaku zapytania (?). Ciąg zapytania mogą zawierać pary klucz wartość, w których nazwy pól i wartości są oddzielone znakiem równości (=). Każda para klucz wartość oddzielona znakiem handlowego "i" (&). Na przykład http:\//www.contoso.com/content.mov?field1=value1 & pole2 = wartość2. Jeśli istnieje więcej niż jedną parę klucz wartość ciągu zapytania żądania, ich kolejność nie ma znaczenia. 

> [!IMPORTANT]
> Produkty sieci CDN w warstwie standardowa i premium zapewniają ten sam ciąg zapytania buforowanie funkcji, ale interfejs użytkownika jest inny. W tym artykule opisano interfejs dla **Azure CDN Premium from Verizon**. Dla ciągu zapytania buforowania, standardowe produkty Azure CDN, zobacz [kontroli usługi Azure CDN buforowanie z ciągami zapytań - warstwy standardowa](cdn-query-string.md).
>


Dostępne są trzy tryby ciągu zapytania:

- **pamięć podręczna standard**: domyślny tryb. W tym trybie węzeł CDN punktu z obecności (POP) przekazuje ciągi zapytań z obiektu żądającego do serwera pochodzenia na pierwsze żądanie i buforuje elementu zawartości. Wszystkie kolejne żądania dla elementu zawartości, które są udostępniane przez serwer protokołu POP ignorować ciągi kwerendy do momentu wygaśnięcia elementu pamięci podręcznej zawartości.

    >[!IMPORTANT] 
    > Jeśli token autoryzacji jest włączona dla dowolną ścieżkę na tym koncie, tryb standardowy pamięci podręcznej jest tylko tryb, który może służyć. 

- **pamięć podręczna nie**: W tym trybie żądań z ciągami zapytań nie są buforowane w węźle POP w sieci CDN. Węzeł POP pobiera elementu zawartości bezpośrednio z serwera pochodzenia i przekazuje je do obiektu żądającego z każdym żądaniem.

- **Unikatowy pamięci podręcznej**: W tym trybie każde żądanie o unikatowym adresie URL, ciąg zapytania w tym jest traktowany jako unikatowy zasób ze swojej własnej pamięci podręcznej. Na przykład odpowiedź z serwera pochodzenia żądania dla example.ashx?q=test1 jest buforowane w węźle POP i zwrócony dla kolejnych pamięci podręcznych ciągiem zapytania. Żądanie example.ashx?q=test2 jest buforowany jako osobne zasobów z własną ustawienie czasu wygaśnięcia.
   
    >[!IMPORTANT] 
    > W tym trybie nie należy używać, jeśli ciąg zapytania zawiera parametry, które zmieni się z każdym żądaniu, takich jak identyfikator sesji lub nazwę użytkownika, ponieważ spowoduje niski Stosunek trafień w pamięci podręcznej.

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Zmiana ustawienia profilów sieci CDN w warstwie premium buforowanie ciągów zapytań
1. Otwieranie profilu CDN, a następnie kliknij przycisk **Zarządzaj**.
   
    ![Przycisk Zarządzaj profil CDN](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    Zostanie otwarty w portalu zarządzania usługi CDN.
2. Umieść kursor nad **HTTP dużych** , a następnie umieść kursor nad **ustawienia pamięci podręcznej** wysuwane menu. Kliknij przycisk **buforowania ciągu kwerendy**.
   
    Opcje buforowania ciągu kwerendy są wyświetlane.
   
    ![Opcje buforowania ciągu kwerendy CDN](./media/cdn-query-string-premium/cdn-query-string.png)
3. Wybierz tryb ciąg zapytania, a następnie kliknij przycisk **aktualizacji**.

> [!IMPORTANT]
> Ponieważ czas rejestracji propagację za pośrednictwem sieci CDN, zmiany ustawień pamięci podręcznej ciąg może nie być od razu widoczne. Aby uzyskać **Azure CDN Premium from Verizon** profile, propagacji zazwyczaj kończy w ciągu 90 minut.
 

