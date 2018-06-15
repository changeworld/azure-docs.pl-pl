---
title: Co to jest usługa decyzji niestandardowe? -Usługi azure kognitywnych | Dokumentacja firmy Microsoft
description: Ten artykuł Omówienie usługi Azure niestandardowe decyzji, oparte na chmurze interfejsu API kontekstowe podejmowania decyzji, które wyostrza doświadczenie w.
services: cognitive-services
author: alekh
manager: slivkins
ms.service: cognitive-services
ms.topic: article
ms.date: 05/08/2018
ms.author: slivkins;marcozo;alekh;marossi
ms.openlocfilehash: 774467446513dcd7ade7255d998b11f41824cafe
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "35356299"
---
# <a name="what-is-custom-decision-service"></a>Co to jest usługa decyzji niestandardowe?

W typowej sieci web lub aplikacji mobilnej, pierwsza strona zostanie połączona z artykuły kilka innych typów zawartości. Jako pierwsza strona ładuje, można go żądanie usługi decyzji niestandardowe do artykułów rangi uwzględnione na tej stronie witryny. Tak gdy użytkownik wybierze artykuł, klikając go, drugie żądanie mógł zostać wysłany do usługi decyzji niestandardowa, która będzie rejestrować wyników tej decyzji użytkownika.

Usługa decyzji niestandardowa jest łatwy w użyciu, ponieważ wymaga tylko źródła danych RSS dla zawartości i zaledwie kilku wierszach kodu JavaScript ma zostać dodana do aplikacji.

Usługa niestandardowa decyzji Konwertuje zawartość funkcje uczenia maszynowego. System używa tych funkcji do zrozumienia treści w postaci tekstu, obrazów, klipów wideo i ogólną wskaźniki nastrojów klientów. Używa kilka innych [kognitywnych usług firmy Microsoft](https://www.microsoft.com/cognitive-services), takiej jak [łączenie jednostki](../entitylinking/home.md), [Analiza tekstu](../text-analytics/overview.md), [emocji](../emotion/home.md)i [Wizji komputera](../computer-vision/home.md).

Niektóre typowe-przypadki użycia usługi decyzji niestandardowe obejmują:

* Personalizowanie artykuły w witrynie wiadomości
* Personalizowanie zawartości wideo w portalu nośnika
* Optymalizacja rozmieszczanie ad lub ad kieruje do stron sieci web
* Klasyfikacja zalecanych elementów na zakupów witryna sieci Web.

Usługa decyzji niestandardowa jest obecnie w *wolnego publicznej wersji zapoznawczej*. Można go spersonalizować listę artykułów w witrynie sieci Web lub aplikacji. Wyodrębniania funkcja działa najlepiej w języku angielskim zawartości. [Ograniczoną funkcjonalność](../text-analytics/overview.md) jest dostępna w przypadku innych języków, takich jak hiszpański, francuski, niemiecki, portugalski i japoński. Ta dokumentacja zostanie zmieniony, wraz ze wzrostem dostępności nowych funkcji.

Nie można używać niestandardowych usługi decyzji w aplikacjach, które nie znajdują się w domenie personalizacji zawartości. Te aplikacje mogą być dobrze podglądu niestandardowego. [Skontaktuj się z nami](https://azure.microsoft.com/overview/sales-number/) Aby dowiedzieć się więcej.

## <a name="api-usage-modes"></a>Tryby użycia interfejsu API

Usługa niestandardowa decyzji może odnosić się do stron sieci Web i aplikacji mobilnych. Interfejsy API można wywołać z przeglądarki lub aplikacji. Użycie interfejsu API jest podobne zarówno, ale niektóre informacje są różne.

## <a name="glossary-of-terms"></a>Słownik terminów

Kilka warunków często wykonywane w tej dokumentacji:

* **Zestaw akcji**: zestaw elementy zawartości dla usługi decyzji niestandardowe rangę. Ten zestaw może być określony jako *RSS* lub *Atom* punktu końcowego.
* **Klasyfikacja**: każdego żądania do usługi decyzji niestandardowe określa co najmniej jeden zbiór akcji. System odpowiada, wybierając opcje zawartości z tych zestawów i zwraca je w kolejności uporządkowanej według rangi.
* **Funkcja wywołania zwrotnego**: tej funkcji można określić renderuje zawartość w Interfejsie użytkownika. Zawartość jest uporządkowanych według rangi kolejność zwrócony przez usługę decyzji niestandardowe.
* **Opłatą**: miara jak odpowiedzi użytkownika do renderowanej zawartości. Usługa niestandardowa decyzji mierzy odpowiedź użytkownika za pomocą kliknięć. Kliknięć są zgłaszane do systemu za pomocą kodu niestandardowego wstawione w aplikacji.

## <a name="next-steps"></a>Kolejne kroki

* [Rejestrowanie aplikacji](custom-decision-service-get-started-register.md) z usługą decyzji niestandardowe
* Wprowadzenie do optymalizacji [strony sieci Web](custom-decision-service-get-started-browser.md) lub [aplikacja na smartfona](custom-decision-service-get-started-app.md).
* Zapoznaj się [dokumentacja interfejsu API](custom-decision-service-api-reference.md) Aby dowiedzieć się więcej o funkcjach podana.