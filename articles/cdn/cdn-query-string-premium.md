---
title: Kontrolowanie Azure działanie buforowania usługi CDN z ciągami zapytań — warstwa premium | Dokumentacja firmy Microsoft
description: Usługa Azure CDN zapytania formantów buforowania ciągu, jak pliki są buforowane na żądania sieci web zawiera ciąg zapytania. W tym artykule opisano ciągu zapytania buforowania dla programu Azure CDN Premium from Verizon produktu.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: magattus
ms.openlocfilehash: 2f0a361d53489e22ccc8e41406e5b86b423ea2f6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60324830"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---premium-tier"></a>Kontroli usługi Azure CDN caching zachowanie z ciągami zapytań — warstwa premium
> [!div class="op_single_selector"]
> * [Warstwa standardowa](cdn-query-string.md)
> * [Warstwa Premium](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Omówienie
Za pomocą usługi Azure Content Delivery Network (CDN), można kontrolować sposób pliki są buforowane dla żądania sieci web, który zawiera ciąg zapytania. W żądaniu sieci web za pomocą ciągu zapytania ciąg zapytania jest część żądanie, która występuje po znaku zapytania (?). Ciąg zapytania może zawierać pary klucz wartość, w których nazwy pól i wartości są oddzielone znak równości (=). Każdej pary klucz wartość jest oddzielona handlowe "i" (&). Na przykład http:\//www.contoso.com/content.mov?field1=value1 & pole2 = wartość2. Jeśli istnieje więcej niż jedną parę klucz wartość w ciągu zapytania żądania, ich kolejność nie ma znaczenia. 

> [!IMPORTANT]
> Produkty CDN standardowa i premium zapewniają ten sam ciąg zapytania funkcji buforowania, ale interfejs użytkownika jest inny. W tym artykule opisano interfejs **Azure CDN Premium from Verizon**. Dla ciągu zapytania buforowania przy użyciu standardowych produkty Azure CDN, zobacz [kontroli usługi Azure CDN caching zachowanie z ciągami zapytań — warstwa standardowa](cdn-query-string.md).
>


Dostępne są trzy tryby ciągu zapytania:

- **standard-cache**: Tryb domyślny. W tym trybie węzła sieci CDN "punktu obecność" (POP) przekazuje ciągi zapytań z osoby zgłaszającej żądanie do serwera pochodzenia na pierwsze żądanie i zapisuje w pamięci podręcznej elementu zawartości. Wszystkie kolejne żądania dla elementu zawartości, które są udostępniane przez serwer protokołu POP Ignoruj ciągi zapytań, do momentu wygaśnięcia pamięci podręcznej elementu zawartości.

    >[!IMPORTANT] 
    > Jeśli token autoryzacji jest włączona dla dowolnej ścieżki dla tego konta, tryb pamięci podręcznej standardu jest jedynym trybem, który może służyć. 

- **no-cache**: W tym trybie żądań z ciągami zapytań nie są buforowane w węźle POP sieci CDN. Węzeł POP pobiera element zawartości bezpośrednio z serwera pochodzenia i przekazuje je do obiektu żądającego z każdym żądaniem.

- **unique-cache**: W tym trybie każde żądanie z unikatowy adres URL, w tym ciągu zapytania jest traktowany jako unikatowy zasobu ze wszystkimi jego własnej pamięci podręcznej. Na przykład odpowiedź z serwera pochodzenia dla żądania example.ashx?q=test1 jest buforowany w węźle POP i zwrócony dla kolejnych pamięci podręcznych przy użyciu tego samego ciągu zapytania. Żądanie dotyczące example.ashx?q=test2 pamięci podręcznej w postaci oddzielnych zasobu ze wszystkimi swoje własne ustawienie czasu wygaśnięcia.
   
    >[!IMPORTANT] 
    > Ten tryb nie należy używać, gdy ciąg zapytania zawiera parametry, które zmieni się z każdym żądaniem, takie jak identyfikator sesji lub nazwę użytkownika, ponieważ spowoduje niski współczynnik trafień pamięci podręcznej.

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Zmiana ustawień dla profilów usługi CDN w warstwie premium buforowania ciągów zapytań
1. Otwórz profil usługi CDN, a następnie kliknij przycisk **Zarządzaj**.
   
    ![Przycisk Zarządzaj w profilu CDN](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    Zostanie otwarty w portalu zarządzania usługi CDN.
2. Umieść kursor nad **HTTP dużych** kartę, a następnie umieść kursor nad **ustawienia pamięci podręcznej** menu wysuwane. Kliknij przycisk **buforowania ciągu kwerendy**.
   
    Opcje buforowania ciągu kwerendy są wyświetlane.
   
    ![Opcje buforowania ciągu kwerendy usługi CDN](./media/cdn-query-string-premium/cdn-query-string.png)
3. Wybierz tryb ciągu zapytania, a następnie kliknij przycisk **aktualizacji**.

> [!IMPORTANT]
> Ponieważ zajmuje trochę czasu rejestracji do propagowania za pośrednictwem sieci CDN, zmiany ustawień ciągu pamięci podręcznej mogą nie być w natychmiast widoczne. Propagacja zwykle zostanie ukończona w ciągu 10 minut.
 

