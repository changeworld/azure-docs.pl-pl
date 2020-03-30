---
title: Akcje w silniku reguł standardowych dla usługi Azure CDN | Dokumenty firmy Microsoft
description: Dokumentacja dotycząca akcji w silniku reguł standardowych dla usługi Azure Content Delivery Network (Azure CDN).
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: 53280bc90f629d93ff8a045c80f34a73970b43f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74171630"
---
# <a name="actions-in-the-standard-rules-engine-for-azure-cdn"></a>Akcje w silniku reguł standardowych dla usługi Azure CDN

W [silniku reguł standardowych](cdn-standard-rules-engine.md) dla usługi Azure Content Delivery Network (Azure CDN) reguła składa się z co najmniej jednego dopasowania i akcji. Ten artykuł zawiera szczegółowe opisy akcji, których można użyć w aparat reguł standardowych dla usługi Azure CDN.

Drugą częścią reguły jest akcja. Akcja definiuje zachowanie, które jest stosowane do typu żądania, który identyfikuje warunek dopasowania lub zestaw warunków dopasowania.

## <a name="actions"></a>Akcje

Następujące akcje są dostępne do użycia w aparat reguł standardowych dla usługi Azure CDN. 

### <a name="cache-expiration"></a>Wygaśnięcie pamięci podręcznej

Ta akcja służy do zastępowanie wartości czasu wygaśnięcia punktu końcowego dla żądań, które określają warunki zgodne z regułami.

#### <a name="required-fields"></a>Wymagane pola

Zachowanie pamięci podręcznej |  Opis              
---------------|----------------
Pomijanie pamięci podręcznej | Gdy ta opcja jest zaznaczona, a reguła jest zgodna, zawartość nie jest buforowana.
Zastąpienie | Gdy ta opcja jest zaznaczona, a reguła jest zgodna, wartość TTL zwrócona ze źródła pochodzi z wartości jest zastępowany wartością określoną w akcji.
Ustaw, jeśli brakuje | Gdy ta opcja jest zaznaczona i reguła jest zgodna, jeśli nie zwrócono wartości TTL od źródła, reguła ustawia czas wygaśnięcia na wartość określoną w akcji.

#### <a name="additional-fields"></a>Dodatkowe pola

Dni | Godziny | Minuty | Sekundy
-----|-------|---------|--------
int | int | int | int 

### <a name="cache-key-query-string"></a>Ciąg zapytania klucza pamięci podręcznej

Ta akcja służy do modyfikowania klucza pamięci podręcznej na podstawie ciągów zapytań.

#### <a name="required-fields"></a>Wymagane pola

Zachowanie | Opis
---------|------------
Uwzględnij | Gdy ta opcja jest zaznaczona, a reguła jest zgodna, ciągi zapytań określone w parametrach są uwzględniane podczas generowania klucza pamięci podręcznej. 
Buforuj każdy unikatowy adres URL | Gdy ta opcja jest zaznaczona, a reguła jest zgodna, każdy unikatowy adres URL ma swój własny klucz pamięci podręcznej. 
Exclude | Gdy ta opcja jest zaznaczona, a reguła jest zgodna, ciągi zapytań określone w parametrach są wykluczane podczas generowania klucza pamięci podręcznej.
Ignoruj ciągi zapytań | Gdy ta opcja jest zaznaczona, a reguła jest zgodna, ciągi zapytań nie są uwzględniane podczas generowania klucza pamięci podręcznej. 

### <a name="modify-request-header"></a>Modyfikowanie nagłówka żądania

Ta akcja służy do modyfikowania nagłówków, które są obecne w żądaniach wysłanych do źródła.

#### <a name="required-fields"></a>Wymagane pola

Akcja | Nazwa nagłówka HTTP | Wartość
-------|------------------|------
Append | Gdy ta opcja jest zaznaczona, a reguła jest zgodna, nagłówek określony w **nazwie nagłówka** jest dodawany do żądania o określonej wartości. Jeśli nagłówek jest już obecny, wartość jest dołączana do istniejącej wartości. | Ciąg
Zastąp | Gdy ta opcja jest zaznaczona, a reguła jest zgodna, nagłówek określony w **nazwie nagłówka** jest dodawany do żądania o określonej wartości. Jeśli nagłówek jest już obecny, określona wartość zastępuje istniejącą wartość. | Ciąg
Usuń | Gdy ta opcja jest zaznaczona, reguła jest zgodna, a nagłówek określony w regule jest obecny, nagłówek jest usuwany z żądania. | Ciąg

### <a name="modify-response-header"></a>Modyfikowanie nagłówka odpowiedzi

Ta akcja służy do modyfikowania nagłówków, które są obecne w odpowiedzi zwracanych do klientów.

#### <a name="required-fields"></a>Wymagane pola

Akcja | Nazwa nagłówka HTTP | Wartość
-------|------------------|------
Append | Gdy ta opcja jest zaznaczona, a reguła jest zgodna, nagłówek określony w **nazwie nagłówka** jest dodawany do odpowiedzi przy użyciu określonej **wartości**. Jeśli nagłówek jest już obecny, **Wartość** jest dołączana do istniejącej wartości. | Ciąg
Zastąp | Gdy ta opcja jest zaznaczona, a reguła jest zgodna, nagłówek określony w **nazwie nagłówka** jest dodawany do odpowiedzi przy użyciu określonej **wartości**. Jeśli nagłówek jest już obecny, **Wartość** zastępuje istniejącą wartość. | Ciąg
Usuń | Gdy ta opcja jest zaznaczona, reguła jest zgodna, a nagłówek określony w regule jest obecny, nagłówek jest usuwany z odpowiedzi. | Ciąg

### <a name="url-redirect"></a>Przekierowywanie adresów URL

Ta akcja służy do przekierowywania klientów do nowego adresu URL. 

#### <a name="required-fields"></a>Wymagane pola

Pole | Opis 
------|------------
Typ | Wybierz typ odpowiedzi, aby powrócić do żądacza: Znaleziono (302), Przeniesiony (301), Przekierowanie tymczasowe (307) i Przekierowanie stałe (308).
Protocol (Protokół) | Żądanie dopasowania, HTTP, HTTPS.
Nazwa hosta | Wybierz nazwę hosta, do której ma zostać przekierowane żądanie. Pozostaw puste miejsce, aby zachować hosta przychodzącego.
Ścieżka | Zdefiniuj ścieżkę do użycia w przekierowaniu. Pozostaw puste miejsce, aby zachować ścieżkę przychodzącą.  
Ciąg zapytania | Zdefiniuj ciąg zapytania używany w przekierowaniu. Pozostaw puste, aby zachować przychodzący ciąg zapytania. 
Fragment | Zdefiniuj fragment, który ma być używany w przekierowaniu. Pozostaw puste, aby zachować przychodzący fragment. 

Zdecydowanie zalecamy użycie bezwzględnego adresu URL. Użycie względnego adresu URL może przekierować adresy URL usługi Azure CDN do nieprawidłowej ścieżki. 

### <a name="url-rewrite"></a>Regenerowanie adresów URL

Ta akcja służy do przepisywania ścieżki żądania, która jest w drodze do źródła.

#### <a name="required-fields"></a>Wymagane pola

Pole | Opis 
------|------------
Wzorzec źródła | Zdefiniuj wzorzec źródłowy w ścieżce adresu URL do zastąpienia. Obecnie wzorzec źródłowy używa dopasowania opartego na prefiksie. Aby dopasować wszystkie ścieżki adresu URL, użyj ukośnika do przodu (**/**) jako wartości wzorca źródłowego.
Element docelowy | Zdefiniuj ścieżkę docelową, która ma być używana podczas przepisywania. Ścieżka docelowa zastępuje wzorzec źródłowy.
Zachowywanie niedopasowanej ścieżki | Jeśli ustawiona jest na **Tak,** pozostała ścieżka po wzorcu źródłowym jest dołączana do nowej ścieżki docelowej. 

## <a name="next-steps"></a>Następne kroki

- [Omówienie usługi Azure CDN](cdn-overview.md)
- [Dokumentacja aparatu reguł standardowych](cdn-standard-rules-engine-reference.md)
- [Warunki dopasowania w silniku reguł standardowych](cdn-standard-rules-engine-match-conditions.md)
- [Wymuszanie protokołu HTTPS za pomocą aparatu reguł standardowych](cdn-standard-rules-engine.md)
