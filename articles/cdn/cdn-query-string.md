---
title: Kontrolowanie Azure działanie buforowania usługi CDN z ciągami zapytań — warstwa standardowa | Dokumentacja firmy Microsoft
description: Usługa Azure CDN zapytania formantów buforowania ciągu, jak pliki są buforowane na żądania sieci web zawiera ciąg zapytania. W tym artykule opisano buforowania w standardowych produktów Azure CDN ciągu kwerendy.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 17410e4f-130e-489c-834e-7ca6d6f9778d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: magattus
ms.openlocfilehash: f0dab3dc81c626e3e7f8c79b4142e5eb4f2a1276
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60324761"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---standard-tier"></a>Kontroli usługi Azure CDN caching zachowanie z ciągami zapytań — warstwa standardowa
> [!div class="op_single_selector"]
> * [Warstwa standardowa](cdn-query-string.md)
> * [Warstwa Premium](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Omówienie
Za pomocą usługi Azure Content Delivery Network (CDN), można kontrolować sposób pliki są buforowane dla żądania sieci web, który zawiera ciąg zapytania. W żądaniu sieci web za pomocą ciągu zapytania ciąg zapytania jest część żądanie, która występuje po znaku zapytania (?). Ciąg zapytania może zawierać pary klucz wartość, w których nazwy pól i wartości są oddzielone znak równości (=). Każdej pary klucz wartość jest oddzielona handlowe "i" (&). Na przykład http:\//www.contoso.com/content.mov?field1=value1 & pole2 = wartość2. Jeśli istnieje więcej niż jedną parę klucz wartość w ciągu zapytania żądania, ich kolejność nie ma znaczenia. 

> [!IMPORTANT]
> Usługa Azure CDN standard i premium produktów zapewnia ten sam ciąg zapytania funkcji buforowania, ale interfejs użytkownika jest inny. W tym artykule opisano interfejs **Azure CDN Standard from Microsoft**, **Azure CDN Standard from Akamai** i **Azure CDN Standard from Verizon**. Dla buforowanie ciągu zapytania za pomocą **Azure CDN Premium from Verizon**, zobacz [kontroli usługi Azure CDN caching zachowanie z ciągami zapytań — warstwa premium](cdn-query-string-premium.md).

Dostępne są trzy tryby ciągu zapytania:

- **Ignoruj ciągi zapytań**: Tryb domyślny. W tym trybie węzła sieci CDN "punktu obecność" (POP) przekazuje ciągi zapytań z osoby zgłaszającej żądanie do serwera pochodzenia na pierwsze żądanie i zapisuje w pamięci podręcznej elementu zawartości. Wszystkie kolejne żądania dla elementu zawartości, które są obsługiwane w punkcie POP Ignoruj ciągi zapytań, do momentu wygaśnięcia pamięci podręcznej elementu zawartości.

- **Pomiń buforowanie dla ciągów zapytań**: W tym trybie żądań z ciągami zapytań nie są buforowane w węźle POP sieci CDN. Węzeł POP pobiera element zawartości bezpośrednio z serwera pochodzenia i przekazuje je do obiektu żądającego z każdym żądaniem.

- **Buforuj każdy unikatowy adres URL**: W tym trybie każde żądanie z unikatowy adres URL, w tym ciągu zapytania jest traktowany jako unikatowy zasobu ze wszystkimi jego własnej pamięci podręcznej. Na przykład odpowiedź z serwera pochodzenia dla żądania example.ashx?q=test1 jest buforowany w węźle POP i zwrócony dla kolejnych pamięci podręcznych przy użyciu tego samego ciągu zapytania. Żądanie dotyczące example.ashx?q=test2 pamięci podręcznej w postaci oddzielnych zasobu ze wszystkimi swoje własne ustawienie czasu wygaśnięcia.
   
    >[!IMPORTANT] 
    > Ten tryb nie należy używać, gdy ciąg zapytania zawiera parametry, które zmieni się z każdym żądaniem, takie jak identyfikator sesji lub nazwę użytkownika, ponieważ spowoduje niski współczynnik trafień pamięci podręcznej.

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Zmiana ustawień dla standardowych profilów usługi CDN do buforowania ciągów zapytań
1. Otwórz profil usługi CDN, a następnie wybierz punkt końcowy CDN, którą chcesz zarządzać.
   
   ![Punkty końcowe profilu usługi CDN](./media/cdn-query-string/cdn-endpoints.png)
   
2. W okienku po lewej stronie w obszarze Ustawienia kliknij **reguły buforowania**.
   
    ![Przycisk Reguły buforowania usługi CDN](./media/cdn-query-string/cdn-caching-rules-btn.png)
   
3. W **zachowanie buforowania ciągu kwerendy** listy, wybierz tryb ciągu zapytania, a następnie kliknij przycisk **Zapisz**.
   
   ![Opcje buforowania ciągu kwerendy usługi CDN](./media/cdn-query-string/cdn-query-string.png)

> [!IMPORTANT]
> Ponieważ zajmuje trochę czasu rejestracji do propagowania za pośrednictwem usługi Azure CDN, zmiany ustawień ciąg w pamięci podręcznej może nie być natychmiast widoczne:
> - W przypadku profili usługi **Azure CDN Standard from Microsoft** propagacja zwykle trwa do 10 minut. 
> - W przypadku profili usługi **Azure CDN Standard from Akamai** propagacja zwykle trwa mniej niż jedną minutę. 
> - W przypadku profilów usługi **Azure CDN Standard from Verizon** oraz usługi **Azure CDN Premium from Verizon** propagacja zwykle trwa do 10 minut. 



