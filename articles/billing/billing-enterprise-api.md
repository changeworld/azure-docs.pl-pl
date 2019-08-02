---
title: Azure Rozlicz interfejsy API przedsiębiorstwa | Microsoft Docs
description: Dowiedz się więcej na temat interfejsów API raportowania, które umożliwiają klientom platformy Azure korzystanie z danych programistycznych.
services: ''
documentationcenter: ''
author: mumami
manager: mumami
editor: ''
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 04/25/2017
ms.author: banders
ms.openlocfilehash: f706ad86493981d5b38248ec209a7c8b936f6817
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443210"
---
# <a name="overview-of-reporting-apis-for-enterprise-customers"></a>Omówienie interfejsów API raportowania dla klientów korporacyjnych
Interfejsy API raportowania umożliwiają klientom korporacyjnym platformy Azure programowe ściąganie danych użycia i rozliczeń do preferowanych narzędzi do analizy danych. Klienci korporacyjni podpisali [Umowa Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) na platformie Azure, aby wynegocjować zobowiązania pieniężne i uzyskać dostęp do niestandardowych cen zasobów platformy Azure.

## <a name="enabling-data-access-to-the-api"></a>Włączanie dostępu do danych do interfejsu API
* **Wygeneruj lub Pobierz klucz interfejsu API** — Zaloguj się do witryny Enterprise Portal i przejdź do raportów > pobierz użycie > klucz dostępu interfejsu API, aby wygenerować lub pobrać klucz interfejsu API.
* **Przekazywanie kluczy w interfejsie API** — należy przekazać klucz interfejsu API dla każdego wywołania uwierzytelniania i autoryzacji. Następująca właściwość musi znajdować się w nagłówkach HTTP

|Klucz nagłówka żądania | Value|
|-|-|
|Authorization| Określ wartość w tym formacie: **Bearer {API_KEY}** <br/> Przykład: Bearer Eyr... 09| 

## <a name="consumption-apis"></a>Interfejsy API użycia
Punkt końcowy struktury Swagger jest dostępny w [tym miejscu](https://consumption.azure.com/swagger/ui/index) dla opisanych poniżej interfejsów API, które powinny umożliwić łatwą INTROSPEKCJI interfejsu API i możliwość generowania zestawów [](https://github.com/Azure/AutoRest) SDK klienta przy użyciu funkcji AutoRest lub [struktury Swagger codegen](https://swagger.io/swagger-codegen/). Dane od 1 maja 2014 są dostępne za poorednictwem tego interfejsu API. 

* **Saldo i podsumowanie** — [interfejs API równoważenia i podsumowania](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) oferuje comiesięczne podsumowanie informacji dotyczących sald, nowych zakupów, opłat za usługę Azure Marketplace, korekt i opłat za użycie nadwyżkowe.

* **Szczegóły użycia** — [interfejs API szczegóły użycia](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) oferuje dzienny podział zużytych ilości i szacowane opłaty przez rejestrację. Wynik zawiera również informacje dotyczące wystąpień, gazomierzy i działów. Do interfejsu API można wykonywać zapytania według okresu rozliczeniowego lub według określonej daty rozpoczęcia i zakończenia. 

* **Opłata za sklep z portalu Marketplace** — [interfejs API z opłatą za Sklep Marketplace](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) zwraca opłaty w witrynie Marketplace na podstawie użycia według dnia dla określonego okresu rozliczeniowego lub daty rozpoczęcia i zakończenia (jednorazowe opłaty nie są uwzględniane).

* **Arkusz cen** — [interfejs API arkusza cen](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) zapewnia odpowiednią stawkę za każdy licznik dla danego okresu rejestracji i rozliczeń.

* **Szczegóły wystąpienia zastrzeżonego** — [interfejs API użycia wystąpienia zarezerwowanego](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) zwraca użycie zakupów wystąpień zarezerwowanych. [Interfejs API opłat za wystąpienia zarezerwowane](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) pokazuje dokonane transakcje rozliczania. 

## <a name="data-freshness"></a>Aktualność danych
Elementy ETag zostaną zwrócone w odpowiedzi dla wszystkich powyższych interfejsów API. Zmiana w elemencie ETag wskazuje, że dane zostały odświeżone.  W kolejnych wywołaniach do tego samego interfejsu API przy użyciu tych samych parametrów Przekaż przechwycony element ETag z kluczem "If-None-Match" w nagłówku żądania HTTP. Kod stanu odpowiedzi będzie "NotModified", jeśli dane nie zostały jeszcze odświeżone i nie zostaną zwrócone żadne dane. Interfejs API zwróci pełen zestaw danych dla wymaganego okresu, gdy istnieje zmiana elementu ETag.

## <a name="helper-apis"></a>Interfejsy API pomocnika
 **Wyświetlanie listy okresów rozliczeniowych** — [interfejs API okresów rozliczeniowych](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) zwraca listę okresów rozliczeniowych, które mają dane dotyczące zużycia dla określonej rejestracji w odwrotnej kolejności chronologicznej. Każdy okres zawiera właściwość wskazującą trasę interfejsu API dla czterech zestawów danych — BalanceSummary, UsageDetails, opłat w witrynie Marketplace i arkusza cen.


## <a name="api-response-codes"></a>Kody odpowiedzi interfejsu API   
|Kod stanu odpowiedzi|Message|Opis|
|-|-|-|
|200| OK|Brak błędów|
|401| Brak autoryzacji| Nie znaleziono klucza interfejsu API, jest on nieprawidłowy, wygasł itd.|
|404| Niedostępny| Nie znaleziono punktu końcowego raportu|
|400| Nieprawidłowe żądanie| Nieprawidłowe parametry — zakresy dat, numery EA itd.|
|500| Błąd serwera| Nieoczekiwane żądanie przetworzenia błędu| 









