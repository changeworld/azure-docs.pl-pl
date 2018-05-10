---
title: Zasady zarządzania interfejsu API platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o zasadach dostępne do użycia w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: f617abc164761b4839b188822585eaa4eb0aacd6
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="api-management-policies"></a>API Management policies
W tej sekcji znajdują się informacje na następujące zasady usługi API Management. Aby uzyskać informacje dotyczące dodawania i konfigurowania zasad, zobacz [zasad w usłudze API Management](api-management-howto-policies.md).  
  
 Zasady są zaawansowanych możliwości systemu, który umożliwia wydawcy, aby zmienić zachowanie interfejsu API za pomocą konfiguracji. Zasady są zbiór instrukcji, które są wykonywane sekwencyjnie na żądanie lub odpowiedź interfejsu API. Popularne instrukcje obejmują Konwersja formatu z pliku XML do formatu JSON i Wywołaj szybkość ograniczenie, aby ograniczyć ilość przychodzących od dewelopera. Wiele zasad są dostępne poza pole.  
  
 Wyrażenia zasad mogą służyć jako wartości atrybutów lub wartości tekstowe w dowolnej z zasad usługi API Management, o ile w zasadach nie określono inaczej. Niektóre zasady, np. [Przepływ sterowania](api-management-advanced-policies.md#choose) i [Ustawianie zmiennej](api-management-advanced-policies.md#set-variable), są oparte na wyrażeniach zasad. Aby uzyskać więcej informacji, zobacz tematy [Advanced policies](api-management-advanced-policies.md#AdvancedPolicies) (Zaawansowane zasady) i [Policy expressions](api-management-policy-expressions.md) (Wyrażenia zasad).  
  
##  <a name="ProxyPolicies"></a> zasady  
  
-   [Zasady ograniczeń dostępu](api-management-access-restriction-policies.md#AccessRestrictionPolicies)  
    -   [Nagłówek HTTP wyboru](api-management-access-restriction-policies.md#CheckHTTPHeader) — wymusza istnienia i/lub wartość nagłówka HTTP.  
    -   [Limit szybkości wywołanie przez subskrypcji](api-management-access-restriction-policies.md#LimitCallRate) — użycie uniemożliwia API wzrósł poprzez ograniczenie wywołań szybkości, na podstawie subskrypcji na.  
    -   [Limit szybkości wywołanie przez klucz](api-management-access-restriction-policies.md#LimitCallRateByKey) — użycie uniemożliwia API wzrósł ograniczając szybkość połączenia, na podstawie na klucz.  
    -   [Ograniczenia adresów IP wywołującego](api-management-access-restriction-policies.md#RestrictCallerIPs) -wywołania filtrów (umożliwia/nie zezwala na) z określonych adresów IP i/lub zakresów adresów.  
    -   [Ustaw przydział użycia subskrypcji](api-management-access-restriction-policies.md#SetUsageQuota) — umożliwia egzekwowanie odnawialnymi lub okres istnienia wywołania woluminu i/lub przepustowości limit przydziału, na podstawie na subskrypcję.  
    -   [Ustaw przydział użycia przez klucz](api-management-access-restriction-policies.md#SetUsageQuotaByKey) — umożliwia egzekwowanie odnawialnymi lub okres istnienia wywołania woluminu i/lub przepustowości limit przydziału, na podstawie według klucza.  
    -   [Sprawdź poprawność JWT](api-management-access-restriction-policies.md#ValidateJWT) — wymusza istnienia i ważności wyodrębniony z określonego nagłówka HTTP lub parametr zapytania określony token JWT.  
-   [Zasady zaawansowane](api-management-advanced-policies.md#AdvancedPolicies)  
    -   [Przepływ kontroli](api-management-advanced-policies.md#choose) — warunkowo stosuje deklaracji zasad, na podstawie oceny wyrażeń logicznych.  
    -   [Przekazanie żądania](api-management-advanced-policies.md#ForwardRequest) -przekazuje żądanie do usługi zaplecza.  
    -   [Dziennika do Centrum zdarzeń](api-management-advanced-policies.md#log-to-eventhub) — wysyła komunikaty w określonym formacie do obiektu docelowego komunikatu zdefiniowanych przez podmiot rejestratora.  
    -   [Spróbuj ponownie](api-management-advanced-policies.md#Retry) -ponowi próbę wykonania instrukcji objętego zasad, jeśli i do momentu spełnienia warunku. Wykonanie Powtórz w określonych odstępach czasu, a także do określonej liczba ponownych prób.  
    -   [Odpowiedź zwrócona](api-management-advanced-policies.md#ReturnResponse) -przerwań potoku wykonywania i zwraca określoną odpowiedzią bezpośrednio do obiektu wywołującego.  
    -   [Wyślij żądanie jednokierunkowej](api-management-advanced-policies.md#SendOneWayRequest) — wysyła żądanie pod określony adres URL bez oczekiwania na odpowiedź.  
    -   [Wyślij żądanie](api-management-advanced-policies.md#SendRequest) — wysyła żądanie do określonego adresu URL.  
    -   [Ustaw zmienną](api-management-advanced-policies.md#set-variable) -utrwalić wartość w zmiennej o nazwie kontekstu nowsze dostępu.  
    -   [Ustawia metodę żądania](api-management-advanced-policies.md#SetRequestMethod) — umożliwia zmianę metody HTTP dla żądania.  
    -   [Ustaw kod stanu](api-management-advanced-policies.md#SetStatus) — zmienia kod stanu HTTP do określonej wartości.  
    -   [Śledzenia](api-management-advanced-policies.md#Trace) -dodaje ciąg do [inspektora interfejsu API](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) danych wyjściowych.  
    -   [Poczekaj](api-management-advanced-policies.md#Wait) -oczekuje dla ujęta [żądanie wysłania](api-management-advanced-policies.md#SendRequest), [pobrać wartości z pamięci podręcznej](api-management-caching-policies.md#GetFromCacheByKey), lub [sterowania przepływem](api-management-advanced-policies.md#choose) zasad, aby ukończyć przed kontynuowaniem.  
-   [Zasady uwierzytelniania](api-management-authentication-policies.md#AuthenticationPolicies)  
    -   [Uwierzytelnianie za pomocą Basic](api-management-authentication-policies.md#Basic) -uwierzytelniania za pomocą usługi wewnętrznej bazy danych przy użyciu uwierzytelniania podstawowego.  
    -   [Uwierzytelniania za pomocą certyfikatu klienta](api-management-authentication-policies.md#ClientCertificate) -uwierzytelniania za pomocą usługi wewnętrznej bazy danych przy użyciu certyfikatów klienta.  
-   [Caching policies](api-management-caching-policies.md#CachingPolicies)  
    -   [Pobierz z pamięci podręcznej](api-management-caching-policies.md#GetFromCache) — wykonaj pamięci podręcznej wyszukiwania i zwracać prawidłową odpowiedź buforowana, jeśli jest dostępna.  
    -   [Magazynu pamięci podręcznej](api-management-caching-policies.md#StoreToCache) -buforuje odpowiedzi zgodnie z określonym pamięci podręcznej konfiguracji kontroli.  
    -   [Pobiera wartość z pamięci podręcznej](api-management-caching-policies.md#GetFromCacheByKey) -pobrania elementu pamięci podręcznej według klucza.  
    -   [Przechowywana wartość w pamięci podręcznej](api-management-caching-policies.md#StoreToCacheByKey) -przechowywania elementu w pamięci podręcznej według klucza.  
    -   [Usuń wartość z pamięci podręcznej](api-management-caching-policies.md#RemoveCacheByKey) -usunięcie elementu w pamięci podręcznej według klucza.  
-   [Zasady międzydomenowe](api-management-cross-domain-policies.md#CrossDomainPolicies)  
    -   [Zezwalaj na połączenia między domenami](api-management-cross-domain-policies.md#AllowCrossDomainCalls) — może ułatwić interfejsu API programu Adobe Flash i Microsoft Silverlight bazujące na przeglądarce klientów.  
    -   [CORS](api-management-cross-domain-policies.md#CORS) -dodaje współużytkowanie zasobów między źródłami (CORS) obsługi operacji lub interfejsu API w celu zapewnienia obsługi wywołań między domenami od klientów przeglądarki do udostępniania.  
    -   [JSONP](api-management-cross-domain-policies.md#JSONP) -dodaje JSON z obsługą dopełnienie (JSONP) do operacji lub interfejsu API w celu zapewnienia obsługi wywołań między domenami od klientów przeglądarki JavaScript.  
-   [Zasady transformacji](api-management-transformation-policies.md#TransformationPolicies)  
    -   [Konwertuj JSON do pliku XML](api-management-transformation-policies.md#ConvertJSONtoXML) — konwertuje żądania lub odpowiedzi body z formatu JSON do pliku XML.  
    -   [Konwertuj do formatu JSON XML](api-management-transformation-policies.md#ConvertXMLtoJSON) — konwertuje żądania lub odpowiedzi body z pliku XML do formatu JSON.  
    -   [Znajdowanie i zamienianie ciągów w treści](api-management-transformation-policies.md#Findandreplacestringinbody) — znajduje podciąg żądania lub odpowiedzi i zastępuje go inny podciąg.  
    -   [Maski adresów URL w zawartości](api-management-transformation-policies.md#MaskURLSContent) -ponownie zapisuje (maski) łącza w odpowiedzi body tak, aby wskazywać równoważne połączenie za pośrednictwem bramy.  
    -   [Ustaw usługę zaplecza](api-management-transformation-policies.md#SetBackendService) — zmienia usługi wewnętrznej bazy danych dla żądania przychodzącego.  
    -   [Ustaw treści](api-management-transformation-policies.md#SetBody) -ustawia treść komunikatu dla żądań przychodzących i wychodzących.  
    -   [Set — nagłówek HTTP](api-management-transformation-policies.md#SetHTTPheader) — przypisuje wartość do istniejących odpowiedzi i/lub nagłówek żądania lub dodaje nowy nagłówek odpowiedzi i/lub żądania.  
    -   [Wartość parametru ciągu zapytania](api-management-transformation-policies.md#SetQueryStringParameter) — dodaje, zastępuje wartość lub usuwa parametru ciągu zapytania żądania.  
    -   [Ponowne zapisywanie adresów URL](api-management-transformation-policies.md#RewriteURL) — konwertuje adresie URL żądania w postaci publicznego do formularza oczekiwane przez usługę sieci web.  
    -   [Przekształcanie XML za pomocą XSLT](api-management-transformation-policies.md#XSLTransform) — ma zastosowanie transformacji XSL do formatu XML w treści żądania lub odpowiedzi.  



## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji, Praca z zasad Zobacz:

+ [Zasady w usłudze API Management](api-management-howto-policies.md)
+ [Przekształć interfejsów API](transform-api.md)
+ [Informacje o zasadach](api-management-policy-reference.md) pełną listę deklaracji zasad i ich ustawienia
+ [Przykłady zasad](policy-samples.md)   
