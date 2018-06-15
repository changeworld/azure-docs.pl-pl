---
title: Azure rozliczeń interfejsów API Enterprise | Dokumentacja firmy Microsoft
description: Więcej informacji na temat raportowania interfejsów API, które umożliwiają klientom Enterprise Azure programowo pobierać dane dotyczące zużycia.
services: ''
documentationcenter: ''
author: anandedwin
manager: aedwin
editor: ''
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 04/25/2017
ms.author: aedwin
ms.openlocfilehash: ff658fd14700e9fdf66b9d929da133f7a3b3f3a0
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34831789"
---
# <a name="overview-of-reporting-apis-for-enterprise-customers"></a>Omówienie API raportowania dla przedsiębiorstw
Interfejsy API raportowania umożliwiają klientom Enterprise Azure programowo ściągania danych rozliczeń i zużycia do narzędzia do analizy danych preferowany. 

## <a name="enabling-data-access-to-the-api"></a>Włączanie dostępu do interfejsu API danych
* **Generowanie lub pobrać klucz interfejsu API** — Zaloguj się w witrynie Enterprise portal i przejdź do raportów > Pobierz dane użycia > klucz dostępu do interfejsu API do generowania lub pobrać klucz interfejsu API.
* **Przekazywanie kluczy w interfejsie API** — klucz interfejsu API musi zostać przekazany dla każdego wywołania do uwierzytelniania i autoryzacji. Następująca właściwość musi być z nagłówkami HTTP

|Klucz nagłówka żądania | Wartość|
|-|-|
|Autoryzacja| Określ wartość w następującym formacie: **elementu nośnego {API_KEY}** <br/> Przykład: eyr elementu nośnego... 09| 

## <a name="consumption-apis"></a>Interfejsy API zużycie
Punktu końcowego struktury Swagger jest dostępna [tutaj](https://consumption.azure.com/swagger/ui/index) dla interfejsów API opisanego poniżej którego powinien umożliwiają łatwe introspection interfejsu API oraz do generowania zestawy SDK klientów przy użyciu [AutoRest](https://github.com/Azure/AutoRest) lub [programu Swagger CodeGen](http://swagger.io/swagger-codegen/). Począwszy od 1 maja 2014 danych jest dostępna za pośrednictwem tego interfejsu API. 

* **Saldo i Podsumowanie** - [saldo oraz podsumowanie interfejsu API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) oferuje miesięczne podsumowanie informacji na temat salda, nowych zakupów, opłaty za usługę Azure Marketplace, zmiany i nadwyżkowe opłat.

* **Szczegóły użycia** - [API szczegółów użycia](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) oferuje zestawienie ilości wykorzystanego i opłat szacowany przy rejestracji. Wynik zawiera również informacje dotyczące wystąpień, mierniki i działów. Interfejs API mogą być przeszukiwane przez okres rozliczeń lub określonej daty rozpoczęcia i zakończenia. 

* **Opłata magazynu Marketplace** — [Marketplace magazynu opłat API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) zwraca opartej na użyciu marketplace podział opłaty wg dnia w określonym przedziale czasu rozliczeń lub daty rozpoczęcia i zakończenia (jeden raz opłaty nie są uwzględniane).

* **Arkusz cen** - [API arkusza cen](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) udostępnia stawkę dla każdego licznika dla danego rejestracji i okresu rozliczeniowego. 

## <a name="data-freshness"></a>Aktualność danych
Elementy etag zostanie zwrócony w odpowiedzi na wszystkie powyższe interfejsu API. Zmiana Etag wskazuje, że dane zostały odświeżone.  W kolejnych wywołaniach tego samego interfejsu API z tymi samymi parametrami należy przekazać przechwyconych Etag z kluczem "If-None-Match" w nagłówku żądania http. Kod stanu odpowiedzi będą "NotModified", jeśli dane nie zostały odświeżone kolejnych i nie zostanie zwrócone dane. Interfejs API zwróci pełnego zestawu danych w okresie wymagana zawsze, gdy nastąpiła zmiana etag.

## <a name="helper-apis"></a>Interfejsy API pomocy
 **Lista rozliczeń okresów** — [rozliczeń API okresów](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) zwraca listę rozliczeń okresów, które mają dane dotyczące zużycia dla określonej rejestracji w odwrotnej kolejności. Każdego okresu zawiera właściwość wskazujący trasę interfejsu API dla cztery zestawy danych - BalanceSummary, UsageDetails opłat w witrynie Marketplace i arkusza cen.


## <a name="api-response-codes"></a>Kody odpowiedzi interfejsu API   
|Kod stanu odpowiedzi|Komunikat|Opis|
|-|-|-|
|200| OK|Brak błędów|
|401| Brak autoryzacji| Klucz interfejsu API nie został znaleziony, nieprawidłowy, ważność itp.|
|404| Niedostępny| Nie znaleziono punktu końcowego raportu|
|400| Nieprawidłowe żądanie| Nieprawidłowe parametry — zakresy dat, liczb EA itp.|
|500| Błąd serwera| Unexoected błąd podczas przetwarzania żądania| 









