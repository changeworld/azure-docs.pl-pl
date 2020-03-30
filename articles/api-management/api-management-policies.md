---
title: Zasady usługi Azure API Management | Dokumenty firmy Microsoft
description: Dowiedz się więcej o zasadach dostępnych do użycia w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 2b6e056fbfb134f0b1218b4281b9f971a0e24202
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71219470"
---
# <a name="api-management-policies"></a>API Management policies
Ta sekcja zawiera odwołanie do następujących zasad zarządzania interfejsami API. Aby uzyskać informacje na temat dodawania i konfigurowania zasad, zobacz [Zasady w zarządzania interfejsami API](api-management-howto-policies.md).  
  
 Zasady są zaawansowane możliwości systemu, które umożliwiają wydawcy, aby zmienić zachowanie interfejsu API za pomocą konfiguracji. Zasady są kolekcją instrukcji, które są wykonywane sekwencyjnie na żądanie lub odpowiedź interfejsu API. Popularne instrukcje obejmują konwersję formatu z XML do JSON i ograniczenie szybkości połączeń w celu ograniczenia liczby połączeń przychodzących od dewelopera. Wiele innych zasad jest dostępnych po wyjęciu z pudełka.  
  
 Wyrażenia zasad mogą służyć jako wartości atrybutów lub wartości tekstowe w dowolnej z zasad usługi API Management, o ile w zasadach nie określono inaczej. Niektóre zasady, np. [Przepływ sterowania](api-management-advanced-policies.md#choose) i [Ustawianie zmiennej](api-management-advanced-policies.md#set-variable), są oparte na wyrażeniach zasad. Aby uzyskać więcej informacji, zobacz tematy [Advanced policies](api-management-advanced-policies.md#AdvancedPolicies) (Zaawansowane zasady) i [Policy expressions](api-management-policy-expressions.md) (Wyrażenia zasad).  
  
##  <a name="policies"></a><a name="ProxyPolicies"></a>Zasady działalności  
  
-   [Zasady ograniczeń dostępu](api-management-access-restriction-policies.md#AccessRestrictionPolicies)  
    -   [Sprawdź nagłówek HTTP](api-management-access-restriction-policies.md#CheckHTTPHeader) — wymusza istnienie i/lub wartość nagłówka HTTP.  
    -   [Ogranicz szybkość wywołania według subskrypcji](api-management-access-restriction-policies.md#LimitCallRate) — zapobiega skokom użycia interfejsu API przez ograniczenie szybkości wywołania na podstawie subskrypcji.  
    -   [Ogranicz szybkość wywołania według klucza](api-management-access-restriction-policies.md#LimitCallRateByKey) — zapobiega skokom użycia interfejsu API, ograniczając szybkość wywołania na podstawie klucza.  
    -   [Ogranicz adresy IP wywołującego](api-management-access-restriction-policies.md#RestrictCallerIPs) — filtry (zezwala/odrzuca) wywołania z określonych adresów IP i/lub zakresów adresów.  
    -   [Ustaw przydział użycia według subskrypcji](api-management-access-restriction-policies.md#SetUsageQuota) — umożliwia wymuszanie przydziału woluminu i/lub przepustowości dla odnawialnych lub dożywotnich połączeń na podstawie subskrypcji.  
    -   [Ustaw przydział użycia według klucza](api-management-access-restriction-policies.md#SetUsageQuotaByKey) — umożliwia wymuszanie przydziału woluminu i/lub przepustowości w okresie dochodzenia w okresie dochodzenia 10 lub 10 000 000 000 000 000 000 000 000 000 000 000 000 000  
    -   [Sprawdź poprawność JWT](api-management-access-restriction-policies.md#ValidateJWT) — wymusza istnienie i ważność JWT wyodrębnione z określonego nagłówka HTTP lub określonego parametru kwerendy.  
-   [Zasady zaawansowane](api-management-advanced-policies.md#AdvancedPolicies)  
    -   [Przepływ sterowania](api-management-advanced-policies.md#choose) — warunkowo stosuje instrukcje zasad na podstawie oceny wyrażeń logicznych.  
    -   [Żądanie przesyłania dalej](api-management-advanced-policies.md#ForwardRequest) — przekazuje żądanie do usługi wewnętrznej bazy danych.
    -   [Ogranicz współbieżność](api-management-advanced-policies.md#LimitConcurrency) — zapobiega wykonywanie zamkniętych zasad przez więcej niż określoną liczbę żądań naraz.
    -   [Zaloguj się do Centrum zdarzeń](api-management-advanced-policies.md#log-to-eventhub) — wysyła wiadomości w określonym formacie do obiektu docelowego wiadomości zdefiniowanego przez jednostkę Rejestratora.
    -   [Mock odpowiedzi](api-management-advanced-policies.md#mock-response) — przerywa wykonanie potoku i zwraca mocked odpowiedzi bezpośrednio do wywołującego.
    -   [Ponów próbę](api-management-advanced-policies.md#Retry) — ponawia ponawia wykonywanie instrukcji zasad, jeśli i dopóki warunek nie zostanie spełniony. Wykonanie zostanie powtórzone w określonych odstępach czasu i do określonej liczby ponownych prób.  
    -   [Odpowiedź zwracana](api-management-advanced-policies.md#ReturnResponse) — przerywa wykonanie potoku i zwraca określoną odpowiedź bezpośrednio do wywołującego.  
    -   [Wyślij żądanie w jedną stronę](api-management-advanced-policies.md#SendOneWayRequest) — wysyła żądanie do określonego adresu URL bez oczekiwania na odpowiedź.  
    -   [Wyślij żądanie](api-management-advanced-policies.md#SendRequest) — wysyła żądanie do określonego adresu URL.
    -   [Ustaw serwer proxy HTTP](api-management-advanced-policies.md#SetHttpProxy) — umożliwia kierowanie żądań przesyłanych dalej za pośrednictwem serwera proxy HTTP.
    -   [Ustaw zmienną](api-management-advanced-policies.md#set-variable) — utrwalanie wartości w nazwanej zmiennej kontekstowej dla późniejszego dostępu.  
    -   [Ustaw metodę żądania](api-management-advanced-policies.md#SetRequestMethod) — umożliwia zmianę metody HTTP dla żądania.  
    -   [Ustaw kod stanu](api-management-advanced-policies.md#SetStatus) — zmienia kod stanu HTTP na określoną wartość.  
    -   [Śledzenie](api-management-advanced-policies.md#Trace) — dodaje niestandardowe ślady do danych wyjściowych [inspektora interfejsu API,](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) telemetrie usługi Application Insights i dzienników diagnostycznych.  
    -   [Czekaj](api-management-advanced-policies.md#Wait) — przed kontynuowaniem trwa oczekiwanie na zamknięte [żądanie wysłania](api-management-advanced-policies.md#SendRequest), [Pobierz wartość z pamięci podręcznej](api-management-caching-policies.md#GetFromCacheByKey)lub [Zasady przepływu sterowania](api-management-advanced-policies.md#choose) do ukończenia.  
-   [Zasady uwierzytelniania](api-management-authentication-policies.md#AuthenticationPolicies)  
    -   [Uwierzytelnij się za pomocą podstawowego](api-management-authentication-policies.md#Basic) — uwierzytelnij się za pomocą usługi wewnętrznej bazy danych przy użyciu uwierzytelniania podstawowego.  
    -   [Uwierzytelnij się przy użyciu certyfikatu klienta](api-management-authentication-policies.md#ClientCertificate) — uwierzytelnij się za pomocą usługi wewnętrznej bazy danych przy użyciu certyfikatów klienta.  
    -   [Uwierzytelnij się przy użyciu tożsamości zarządzanej](api-management-authentication-policies.md#ManagedIdentity) — uwierzytelnij się za pomocą usługi wewnętrznej bazy danych przy użyciu [tożsamości zarządzanej](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).  
-   [Caching policies](api-management-caching-policies.md#CachingPolicies)  
    -   [Pobierz z pamięci podręcznej](api-management-caching-policies.md#GetFromCache) — wyszukuj pamięć podręczną i zwróć prawidłową odpowiedź w pamięci podręcznej, gdy jest dostępna.  
    -   [Magazyn do pamięci podręcznej](api-management-caching-policies.md#StoreToCache) — bufory odpowiedzi zgodnie z określoną konfiguracją kontroli pamięci podręcznej.  
    -   [Pobierz wartość z pamięci podręcznej](api-management-caching-policies.md#GetFromCacheByKey) — pobieranie elementu w pamięci podręcznej według klucza.  
    -   [Wartość magazynu w pamięci podręcznej](api-management-caching-policies.md#StoreToCacheByKey) — przechowuj element w pamięci podręcznej według klucza.  
    -   [Usuń wartość z pamięci podręcznej](api-management-caching-policies.md#RemoveCacheByKey) — usuń element w pamięci podręcznej według klucza.  
-   [Zasady międzydomenowe](api-management-cross-domain-policies.md#CrossDomainPolicies)  
    -   [Zezwalaj na połączenia między domenami](api-management-cross-domain-policies.md#AllowCrossDomainCalls) — umożliwia dostęp interfejsu API od klientów korzystających z przeglądarki Adobe Flash i Microsoft Silverlight.  
    -   [CORS](api-management-cross-domain-policies.md#CORS) — dodaje obsługę udostępniania zasobów między źródłami (CORS) do operacji lub interfejsu API, aby umożliwić wywołania między domenami od klientów opartych na przeglądarce.  
    -   [JSONP](api-management-cross-domain-policies.md#JSONP) — dodaje JSON z obsługą dopełnienia (JSONP) do operacji lub interfejsu API, aby umożliwić wywołania między domenami z klientów opartych na przeglądarce JavaScript.  
-   [Zasady transformacji](api-management-transformation-policies.md#TransformationPolicies)  
    -   [Konwertuj JSON na XML](api-management-transformation-policies.md#ConvertJSONtoXML) — konwertuje treść żądania lub odpowiedzi z JSON na XML.  
    -   [Konwertuj XML na JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) — konwertuje treść żądania lub odpowiedzi z XML na JSON.  
    -   [Znajdź i zastąp ciąg w treści](api-management-transformation-policies.md#Findandreplacestringinbody) — znajduje podciąg żądania lub odpowiedzi i zastępuje go innym podciągem.  
    -   [Maskuj adresy URL w zawartości](api-management-transformation-policies.md#MaskURLSContent) — ponownie zapisuje (maskuje) łącza w treści odpowiedzi, dzięki czemu wskazują one równoważne łącze za pośrednictwem bramy.  
    -   [Ustaw usługę wewnętrznej bazy danych](api-management-transformation-policies.md#SetBackendService) — zmienia usługę wewnętrznej bazy danych dla żądania przychodzącego.  
    -   [Ustaw treść](api-management-transformation-policies.md#SetBody) — ustawia treść wiadomości dla żądań przychodzących i wychodzących.  
    -   [Ustaw nagłówek HTTP](api-management-transformation-policies.md#SetHTTPheader) — przypisuje wartość do istniejącego nagłówka odpowiedzi i/lub żądania lub dodaje nowy nagłówek odpowiedzi i/lub żądania.  
    -   [Ustaw parametr ciągu kwerendy](api-management-transformation-policies.md#SetQueryStringParameter) — dodaje, zastępuje wartość lub usuwa parametr ciągu zapytania żądania.  
    -   [Przepisz adres URL](api-management-transformation-policies.md#RewriteURL) — konwertuje adres URL żądania z formularza publicznego na formularz oczekiwany przez usługę sieci web.  
    -   [Przekształcanie xml przy użyciu XSLT](api-management-transformation-policies.md#XSLTransform) - Stosuje transformację XSL do XML w treści żądania lub odpowiedzi.  



## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat pracy z zasadami, zobacz:

+ [Zasady w zarządzaniu interfejsami API](api-management-howto-policies.md)
+ [Przekształcanie interfejsów API](transform-api.md)
+ [Przykłady zasad](policy-samples.md)   
