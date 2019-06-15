---
title: Zasady usługi Azure API Management | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o zasadach dostępnych do użycia w usłudze Azure API Management.
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
ms.openlocfilehash: e27829fe5ebf57552ef4e97a2bfc7b6aefd81dc8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66254402"
---
# <a name="api-management-policies"></a>API Management policies
Ta sekcja zawiera odwołania do następujących zasad usługi API Management. Aby uzyskać informacje na temat dodawania i konfigurowania zasad, zobacz [zasad w usłudze API Management](api-management-howto-policies.md).  
  
 Zasady są zaawansowaną możliwością usługi systemu, która pozwala wydawcy zmieniać zachowanie interfejsu API za pomocą konfiguracji. Zasady to zbiór instrukcji, które są wykonywane sekwencyjnie podczas żądania lub odpowiedzi interfejsu API. Popularne instrukcje obejmują konwersję z formatu XML do formatu JSON i ograniczanie, aby ograniczyć liczbę wywołań przychodzących od dewelopera liczby wywołań. Gotowe, dostępnych jest wiele więcej zasad.  
  
 Wyrażenia zasad mogą służyć jako wartości atrybutów lub wartości tekstowe w dowolnej z zasad usługi API Management, o ile w zasadach nie określono inaczej. Niektóre zasady, np. [Przepływ sterowania](api-management-advanced-policies.md#choose) i [Ustawianie zmiennej](api-management-advanced-policies.md#set-variable), są oparte na wyrażeniach zasad. Aby uzyskać więcej informacji, zobacz tematy [Advanced policies](api-management-advanced-policies.md#AdvancedPolicies) (Zaawansowane zasady) i [Policy expressions](api-management-policy-expressions.md) (Wyrażenia zasad).  
  
##  <a name="ProxyPolicies"></a> Zasady  
  
-   [Zasady ograniczeń dostępu](api-management-access-restriction-policies.md#AccessRestrictionPolicies)  
    -   [Nagłówek HTTP wyboru](api-management-access-restriction-policies.md#CheckHTTPHeader) -wymusza obecność i/lub wartość nagłówka HTTP.  
    -   [Ograniczanie liczby wywołań według subskrypcji](api-management-access-restriction-policies.md#LimitCallRate) — użycie zapobiega interfejsu API gwałtowne wzrosty przez ograniczanie liczby wywołań, na podstawie każdej subskrypcji.  
    -   [Ograniczanie liczby wywołań według klucza](api-management-access-restriction-policies.md#LimitCallRateByKey) — użycie zapobiega interfejsu API gwałtowne wzrosty przez ograniczanie liczby wywołań, na podstawie na klucz.  
    -   [Ograniczenia adresów IP obiektu wywołującego](api-management-access-restriction-policies.md#RestrictCallerIPs) -wywołania filtrów (umożliwia/nie zezwala na) z określonych adresów IP i/lub zakresów adresów.  
    -   [Ustawianie przydziału użycia według subskrypcji](api-management-access-restriction-policies.md#SetUsageQuota) — umożliwia egzekwowanie odnawialnych lub okres istnienia woluminu i/lub przepustowości limit przydziału wywołania, na podstawie każdej subskrypcji.  
    -   [Ustawianie przydziału użycia według klucza](api-management-access-restriction-policies.md#SetUsageQuotaByKey) — umożliwia egzekwowanie odnawialnych lub okres istnienia woluminu i/lub przepustowości limit przydziału wywołania, na podstawie na klucz.  
    -   [Sprawdzanie poprawności tokenu JWT](api-management-access-restriction-policies.md#ValidateJWT) -wymusza istnienia i ważności wyodrębnione z określonego nagłówka HTTP lub parametr zapytania określony token JWT.  
-   [Zasady zaawansowane](api-management-advanced-policies.md#AdvancedPolicies)  
    -   [Przepływ sterowania](api-management-advanced-policies.md#choose) — warunkowo stosuje instrukcje zasad na podstawie oceny wyrażeń logicznych.  
    -   [Przekazanie żądania](api-management-advanced-policies.md#ForwardRequest) -przekazuje żądanie do usługi zaplecza.
    -   [Limit współbieżności](api-management-advanced-policies.md#LimitConcurrency) — uniemożliwia ujęte zasad wykonania przez więcej niż określoną liczbę żądań w danym momencie.
    -   [Dziennik do Centrum zdarzeń](api-management-advanced-policies.md#log-to-eventhub) — wysyła komunikaty w określonym formacie do obiektu docelowego komunikatu zdefiniowanych przez podmiot rejestratora.
    -   [Pozorowanie odpowiedzi](api-management-advanced-policies.md#mock-response) -przerwań potoku wykonywania i zwracał pozorowane odpowiedzi bezpośrednio do obiektu wywołującego.
    -   [Ponów próbę wykonania](api-management-advanced-policies.md#Retry) -ponawia próbę wykonania instrukcji ujęty zasad, jeśli i do momentu spełnienia warunku. Wykonywanie będzie powtarzać w określonych odstępach czasu i do określonej liczba ponownych prób.  
    -   [Odpowiedź zwrócona](api-management-advanced-policies.md#ReturnResponse) -przerwań potoku wykonywania i zwraca określoną odpowiedź bezpośrednio do obiektu wywołującego.  
    -   [Wyślij żądanie jeden ze sposobów](api-management-advanced-policies.md#SendOneWayRequest) — wysyła żądanie do określonego adresu URL bez oczekiwania na odpowiedź.  
    -   [Wyślij żądanie](api-management-advanced-policies.md#SendRequest) — wysyła żądanie do określonego adresu URL.
    -   [Ustaw serwer proxy HTTP](api-management-advanced-policies.md#SetHttpProxy) — pozwala na żądania trasy przekazywane za pośrednictwem serwera proxy HTTP.
    -   [Ustaw zmienną](api-management-advanced-policies.md#set-variable) — utrwalanie wartość w zmiennej nazwanego kontekstu na potrzeby późniejszego dostępu.  
    -   [Ustawia metodę żądania](api-management-advanced-policies.md#SetRequestMethod) — umożliwia zmianę metody HTTP dla żądania.  
    -   [Ustawionego kodu stanu](api-management-advanced-policies.md#SetStatus) — zmienia kod stanu HTTP do określonej wartości.  
    -   [Śledzenie](api-management-advanced-policies.md#Trace) — dodaje ciąg do [inspektora interfejsów API](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) danych wyjściowych.  
    -   [Poczekaj](api-management-advanced-policies.md#Wait) — oczekuje ujęte [żądań wysłania](api-management-advanced-policies.md#SendRequest), [korzyści z pamięci podręcznej](api-management-caching-policies.md#GetFromCacheByKey), lub [przepływ sterowania](api-management-advanced-policies.md#choose) zasady, które należy wykonać przed kontynuowaniem.  
-   [Zasady uwierzytelniania](api-management-authentication-policies.md#AuthenticationPolicies)  
    -   [Uwierzytelnianie Basic](api-management-authentication-policies.md#Basic) — uwierzytelnianie za pomocą usługi zaplecza przy użyciu uwierzytelniania podstawowego.  
    -   [Uwierzytelnianie za pomocą certyfikatu klienta](api-management-authentication-policies.md#ClientCertificate) — uwierzytelnianie za pomocą usługi zaplecza przy użyciu certyfikatów klienta.  
    -   [Uwierzytelnianie za pomocą tożsamości zarządzanej](api-management-authentication-policies.md#ManagedIdentity) — uwierzytelnianie za pomocą usługi zaplecza przy użyciu [tożsamości zarządzanej](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).  
-   [Caching policies](api-management-caching-policies.md#CachingPolicies)  
    -   [Pobieranie z pamięci podręcznej](api-management-caching-policies.md#GetFromCache) — wykonaj pamięci podręcznej wyszukać i zwrócić prawidłowej odpowiedzi pamięci podręcznej, jeśli jest dostępna.  
    -   [Store do pamięci podręcznej](api-management-caching-policies.md#StoreToCache) -buforuje odpowiedzi zgodnie z określonym pamięci podręcznej konfiguracji kontroli.  
    -   [Pobiera wartość z pamięci podręcznej](api-management-caching-policies.md#GetFromCacheByKey) -pobrać element pamięci podręcznej według klucza.  
    -   [Store wartość w pamięci podręcznej](api-management-caching-policies.md#StoreToCacheByKey) -Store elementu w pamięci podręcznej według klucza.  
    -   [Usuń wartość z pamięci podręcznej](api-management-caching-policies.md#RemoveCacheByKey) — usunięcie elementu w pamięci podręcznej według klucza.  
-   [Zasady międzydomenowe](api-management-cross-domain-policies.md#CrossDomainPolicies)  
    -   [Zezwalaj na międzydomenowe wywołania](api-management-cross-domain-policies.md#AllowCrossDomainCalls) — sprawia, że interfejs API dostępne od klientów programu Adobe Flash i Silverlight: Microsoft opartym na przeglądarce.  
    -   [Mechanizm CORS](api-management-cross-domain-policies.md#CORS) — dodaje zasobów między źródłami sharing (CORS) obsługi operacji lub interfejsu API, aby zezwolić na międzydomenowe wywołania z klientów opartych na przeglądarce.  
    -   [JSONP](api-management-cross-domain-policies.md#JSONP) — dodaje JSON z obsługą dopełnienie (JSONP) do operacji lub interfejsu API, aby zezwolić na międzydomenowe wywołania z klientów oparte na przeglądarce języka JavaScript.  
-   [Zasady transformacji](api-management-transformation-policies.md#TransformationPolicies)  
    -   [Konwertuj JSON do formatu XML](api-management-transformation-policies.md#ConvertJSONtoXML) — konwertuje żądania lub odpowiedzi treści z formatu JSON do formatu XML.  
    -   [Konwertuj XML do formatu JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) — konwertuje żądania lub odpowiedzi treści z pliku XML do formatu JSON.  
    -   [Znajdź i zamień ciąg w treści](api-management-transformation-policies.md#Findandreplacestringinbody) — znajduje żądania lub odpowiedzi podciąg i zastępuje go znakiem inny podciąg.  
    -   [Maski adresów URL w zawartości](api-management-transformation-policies.md#MaskURLSContent) -ponownie zapisuje (maski) łącza w odpowiedzi treści, aby wskazać równoważne link za pośrednictwem bramy.  
    -   [Ustaw usługę zaplecza](api-management-transformation-policies.md#SetBackendService) — zmiany do usługi zaplecza dla przychodzącego żądania.  
    -   [Ustaw treść](api-management-transformation-policies.md#SetBody) -ustawia treść wiadomości dla żądań przychodzących i wychodzących.  
    -   [Set — nagłówek HTTP](api-management-transformation-policies.md#SetHTTPheader) — przypisuje wartość do istniejącej odpowiedzi i/lub nagłówku żądania lub dodaje nowy nagłówek odpowiedzi i/lub żądania.  
    -   [Ustaw parametr ciągu zapytania](api-management-transformation-policies.md#SetQueryStringParameter) — dodaje, zastępuje wartość lub usuwa parametr ciągu zapytania żądania.  
    -   [Ponowne zapisywanie adresów URL](api-management-transformation-policies.md#RewriteURL) — konwertuje adres URL żądania w postaci publicznej do postaci oczekiwanej przez usługę sieci web.  
    -   [Przekształcanie kodu XML za pomocą XSLT](api-management-transformation-policies.md#XSLTransform) -stosuje przekształcenia XSL do pliku XML w treści żądania lub odpowiedzi.  



## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji, w pracy z tymi zasadami zobacz:

+ [Zasady usługi API Management](api-management-howto-policies.md)
+ [Przekształć interfejsy API](transform-api.md)
+ [Przykłady zasad](policy-samples.md)   
