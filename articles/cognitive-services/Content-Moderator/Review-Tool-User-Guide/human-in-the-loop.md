---
title: Informacje na temat narzędzia przeglądu - Content Moderator
titlesuffix: Azure Cognitive Services
description: Dowiedz się więcej o narzędziu Content Moderator analizować, witryny sieci Web, która koordynuje połączone sztucznej Inteligencji i nakładu pracy Moderowanie przeglądu przez ludzi.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: sajagtap
ms.openlocfilehash: b7ec997fd3e9bfe294050893d80fd57a96a47aae
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58755876"
---
# <a name="content-moderator-review-tool"></a>Content narzędzie do przeglądu moderatora

Usługa Azure Content Moderator udostępnia usługi połączyć machine learning moderowanie zawartości dzięki ludziom dokonywanie i [narzędzie do przeglądu](https://contentmoderator.cognitive.microsoft.com) witryny sieci Web jest przyjazny dla użytkownika fronton, który zapewnia szczegółowe dostęp do tych usług.

![Pulpit nawigacyjny narzędzie do przeglądu w przeglądarce](./images/0-dashboard.png)

## <a name="what-it-does"></a>Wyniki działania

[Narzędzie do przeglądu](https://contentmoderator.cognitive.microsoft.com), gdy jest używana w połączeniu z wspomaganego maszynowo moderowania interfejsów API, można wykonywać następujące zadania w procesie moderowanie zawartości:

- Jeden zestaw narzędzi umożliwia moderowanie zawartości w wielu formatach (tekstu, obrazów i wideo).
- Aby zautomatyzować tworzenie ludzkich [przeglądy](../review-api.md#reviews) gdy interfejs API moderowania powoduje wróć w.
- Przypisz lub Eskalowanie recenzji zawartości do wielu zespołów przeglądu, uporządkowane według zawartości poziom kategorii lub środowiska.
- Użyj domyślnej lub niestandardowej logiki filtry ([przepływy pracy](../review-api.md#workflows)) do sortowania i śledzenie zawartości, bez konieczności pisania jakiegokolwiek kodu.
- Użyj [łączników](./configure.md#connectors) przetwarzania zawartości z PhotoDNA firmy Microsoft, analiza tekstu i interfejsy API rozpoznawania twarzy, oprócz interfejsów API Moderator zawartości.
- Tworzenie własnego łącznika można tworzyć przepływy pracy dla dowolnego interfejsu API lub procesów biznesowych.
- Uzyskaj kluczowe metryki wydajności na procesy moderowanie zawartości.

## <a name="review-tool-dashboard"></a>Pulpit nawigacyjny narzędzie do przeglądu

Na **pulpit nawigacyjny** karcie widać najważniejszych metryk dla zawartości przeglądy wykonywane w narzędziu. Zobacz liczby całkowitej, zakończone i oczekujących przeglądów dla obrazów, tekstu i zawartości wideo. Widać również podział użytkownicy i zespoły, które zostały wykonane, przeglądy, a także tagi Moderowanie, które zostały zastosowane.

![Wyświetl pulpit nawigacyjny](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>Poświadczenia narzędzia do przeglądu

Kiedy należy zarejestrować się przy użyciu [narzędzie do przeglądu](https://contentmoderator.cognitive.microsoft.com), zostanie wyświetlony monit wybierz region platformy Azure o koncie. Jest to spowodowane [narzędzie do przeglądu](https://contentmoderator.cognitive.microsoft.com) generuje klucz bezpłatnej wersji próbnej usługi Azure Content Moderator; konieczne będzie ten klucz ma dostęp do wszystkich usług z wywołania REST lub zestawu SDK klienta. Adres URL punktu końcowego klucza i interfejsu API można wyświetlić, wybierając **ustawienia** > **poświadczenia**.

![Poświadczenia usługi Content Moderator](images/settings-6-credentials.png)

## <a name="next-steps"></a>Kolejne kroki

Zobacz [skonfigurować narzędzie do przeglądu](./configure.md) Aby dowiedzieć się, jak uzyskać dostęp do zasobów narzędzie do przeglądu i zmieniać ustawienia.