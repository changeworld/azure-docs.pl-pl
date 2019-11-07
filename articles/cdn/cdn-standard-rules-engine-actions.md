---
title: Azure CDN z akcji aparatu standardowych reguł firmy Microsoft | Microsoft Docs
description: Dokumentacja odwołująca się do Azure CDN z akcji aparatu standardowych reguł firmy Microsoft.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: dbde93cc7ffd21e341653407e6e4f910e4620974
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615990"
---
# <a name="azure-cdn-from-microsoft-standard-rules-engine-actions"></a>Azure CDN z akcji aparatu standardowych reguł firmy Microsoft

W tym artykule przedstawiono szczegółowe opisy akcji dostępnych dla usługi Azure Content Delivery Network (CDN) z aparatu reguł firmy Microsoft w [warstwie Standardowa](cdn-standard-rules-engine.md).

Druga część reguły jest akcją. Akcja definiuje zachowanie stosowane do typu żądania, który jest identyfikowany przez zestaw warunków dopasowywania.

## <a name="actions"></a>Akcje

Następujące akcje są dostępne do użycia. 

## <a name="cache-expiration"></a>Wygaśnięcie pamięci podręcznej

Ta akcja umożliwia zastąpienie czasu wygaśnięcia punktu końcowego dla żądań określonych przez reguły dopasowuje warunki.

**Pola wymagane**

Zachowanie pamięci podręcznej |                
---------------|----------------
Pomiń pamięć podręczną | Gdy ta opcja jest zaznaczona, a reguła jest zgodna, zawartość nie będzie buforowana.
Mapowań | Gdy ta opcja jest zaznaczona, a reguła jest zgodna, wartość czasu wygaśnięcia zwracana z pochodzenia zostanie zastąpiona wartością określoną w akcji.
Ustaw, jeśli brakuje | Gdy ta opcja jest zaznaczona, a reguła jest zgodna, jeśli wartość czasu wygaśnięcia nie została zwrócona ze źródła, reguła ustawi czas wygaśnięcia na wartość określoną w akcji.

**Dodatkowe pola**

Dni | Godziny | Minuty | Sekundy
-----|-------|---------|--------
ZAOKR | ZAOKR | ZAOKR | ZAOKR 

## <a name="cache-key-query-string"></a>Ciąg zapytania klucza pamięci podręcznej

Ta akcja pozwala modyfikować klucz pamięci podręcznej na podstawie ciągów zapytań.

**Pola wymagane**

Zachowanie | Opis
---------|------------
być | W przypadku wybrania tej opcji i dopasowania reguły ciągi zapytania określone w parametrach zostaną uwzględnione podczas generowania klucza pamięci podręcznej. 
Buforuj każdy unikatowy adres URL | Gdy ta opcja jest zaznaczona, a reguła jest zgodna, każdy unikatowy adres URL będzie miał własny klucz pamięci podręcznej. 
Exclude | W przypadku wybrania tej opcji i dopasowania reguły ciągi zapytania określone w parametrach zostaną wykluczone podczas generowania klucza pamięci podręcznej.
Ignoruj ciągi zapytań | Gdy ta opcja jest zaznaczona, a reguła jest zgodna, ciągi zapytania nie będą brane pod uwagę podczas generowania klucza pamięci podręcznej. 

## <a name="modify-request-header"></a>Modyfikuj nagłówek żądania

Ta akcja pozwala modyfikować nagłówki obecne w żądaniach wysyłanych do źródła.

**Pola wymagane**

Akcja | Nazwa nagłówka HTTP | Wartość
-------|------------------|------
Append | W przypadku wybrania tej opcji i dopasowania reguły nagłówek określony w polu Nazwa nagłówka zostanie dodany do żądania o określonej wartości. Jeśli nagłówek już istnieje, wartość zostanie dołączona do istniejącej wartości. | Ciąg
Pisz | W przypadku wybrania tej opcji i dopasowania reguły nagłówek określony w polu Nazwa nagłówka zostanie dodany do żądania o określonej wartości. Jeśli nagłówek już istnieje, wartość spowoduje zastąpienie istniejącej wartości. | Ciąg
Usuwanie | Gdy ta opcja jest zaznaczona i reguła jest zgodna, a nagłówek określony w regule jest obecny, zostanie on usunięty z żądania. | Ciąg

## <a name="modify-response-header"></a>Modyfikuj nagłówek odpowiedzi

Ta akcja pozwala modyfikować nagłówki obecne w odpowiedziach zwracanych do klientów końcowych.

**Pola wymagane**

Akcja | Nazwa nagłówka HTTP | Wartość
-------|------------------|------
Append | Gdy ta opcja jest zaznaczona, a reguła jest zgodna, nagłówek określony w nazwie nagłówka zostanie dodany do odpowiedzi z określoną wartością. Jeśli nagłówek już istnieje, wartość zostanie dołączona do istniejącej wartości. | Ciąg
Pisz | Gdy ta opcja jest zaznaczona, a reguła jest zgodna, nagłówek określony w nazwie nagłówka zostanie dodany do odpowiedzi z określoną wartością. Jeśli nagłówek już istnieje, wartość spowoduje zastąpienie istniejącej wartości. | Ciąg
Usuwanie | Gdy ta opcja jest zaznaczona i reguła jest zgodna, a nagłówek określony w regule jest obecny, zostanie on usunięty z odpowiedzi. | Ciąg

## <a name="url-redirect"></a>Przekierowanie adresu URL

Ta akcja umożliwia przekierowywanie klientów końcowych do nowego adresu URL. 

**Pola wymagane**

Pole | Opis 
------|------------
Typ | Wybierz typ odpowiedzi, który zostanie zwrócony do obiektu żądającego. Dostępne opcje to-302, 301 Przeniesiono, 307 tymczasowe przekierowanie i stałe przekierowanie rozwiązania 308
Protokół | Dopasowanie żądania, HTTP lub HTTPS
Nazwa hosta | Wybierz nazwę hosta, do którego zostanie przekierowana żądanie. Pozostaw to pole puste, aby zachować hosta przychodzącego.
Ścieżka | Zdefiniuj ścieżkę, która ma być używana w przekierowaniu. Pozostaw wartość pustą, aby zachować ścieżkę przychodzącą.  
Ciąg kwerendy | Zdefiniuj ciąg zapytania używany w przekierowaniu. Pozostaw pustą wartość, aby zachować ciąg zapytania przychodzącego. 
Trwania | Zdefiniuj fragment, który ma być używany w przekierowaniu. Pozostaw pusty, aby zachować fragment przychodzący. 

Zdecydowanie zaleca się używanie bezwzględnego adresu URL. Użycie względnego adresu URL może przekierować adresy URL usługi CDN do nieprawidłowej ścieżki. 

## <a name="url-rewrite"></a>Ponowne zapisywanie adresów URL

Ta akcja umożliwia ponowne zapisanie ścieżki żądania w marszrucie do źródła.

**Pola wymagane**

Pole | Opis 
------|------------
Wzorzec źródła | Zdefiniuj wzorzec źródłowy w ścieżce URL, który ma zostać zamieniony. Obecnie wzorzec źródłowy używa dopasowania opartego na prefiksie. Aby dopasować wszystkie ścieżki URL, użyj "/" jako wartości wzorca źródła.
Element docelowy | Zdefiniuj ścieżkę docelową, która ma być używana podczas ponownego zapisywania. Spowoduje to zastąpienie wzorca źródłowego
Zachowaj niedopasowaną ścieżkę | Jeśli tak, pozostała ścieżka po wzorcu źródłowym zostanie dołączona do nowej ścieżki docelowej. 


[Powrót do początku](#actions)

</br>

## <a name="next-steps"></a>Następne kroki

- [Omówienie usługi Azure Content Delivery Network](cdn-overview.md)
- [Dokumentacja aparatu reguł](cdn-standard-rules-engine-reference.md)
- [Warunki dopasowań aparatu reguł](cdn-standard-rules-engine-match-conditions.md)
- [Wymuszanie protokołu HTTPS przy użyciu standardowego aparatu reguł](cdn-standard-rules-engine.md)
