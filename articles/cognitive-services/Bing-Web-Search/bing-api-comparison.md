---
title: Co to są interfejsy API wyszukiwania Bing?
titleSuffix: Azure Cognitive Services
description: Informacje na temat interfejsów API wyszukiwania Bing oraz jak włączyć cognitive internetowych wyszukiwania w usługach i aplikacjach, należy użyć w tym artykule.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: article
ms.date: 01/31/2019
ms.author: aahi
ms.openlocfilehash: b2277f540b076307fe74c57068ff288860f82796
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55513117"
---
# <a name="what-are-the-bing-search-apis"></a>Co to są interfejsy API wyszukiwania Bing?

Interfejsy API wyszukiwania Bing umożliwiają tworzenie aplikacji połączonych z sieci web i usług, których odnaleźć stron sieci Web, obrazów, wiadomości, lokalizacji i bez reklam. Wysyłając żądania wyszukiwania przy użyciu zestawów SDK lub interfejsy REST API wyszukiwania Bing, możesz uzyskać istotne informacje i zawartość dla wyszukiwania w Internecie. Aby dowiedzieć się więcej o różnych interfejsów API wyszukiwania Bing i jak można zintegrować cognitive wyszukiwania aplikacji i usług, należy użyć w tym artykule. Cennik i szybkość limity mogą się różnić między interfejsów API.

## <a name="the-bing-web-search-api"></a>W sieci Web interfejs API wyszukiwania Bing

[API wyszukiwania w Internecie Bing](../Bing-Web-Search/index.yml) zwraca stron sieci Web, obrazy, wideo i wiadomości. Zapytania wyszukiwania wysyłane do tego interfejsu API mogą być filtrowane do dołączania lub wykluczania określonych typów zawartości.

Należy rozważyć użycie API wyszukiwania w Internecie Bing w aplikacjach, które mogą wymagać, aby wyszukać wszystkie typy zawartości sieci web odpowiednie. Jeśli aplikacja wyszukuje określonego typu zawartości w trybie online, należy rozważyć jedną z poniższych interfejsów API wyszukiwania: 

## <a name="content-specific-bing-search-apis"></a>Interfejsami API wyszukiwania Bing specyficzne dla zawartości

Następujące interfejsy API wyszukiwania Bing zwraca określoną zawartość z sieci web, takich jak obrazy, wiadomości, lokalnych firm i filmy wideo.

| Interfejs API Bing | Opis |
| -- | -- | 
| [Wyszukiwanie jednostek](../Bing-Entities-Search/index.yml) | Interfejs API wyszukiwania jednostek Bing zwraca wyniki wyszukiwania zawierające jednostek, które mogą być osoby, miejsca lub rzeczy. Zależnie od zapytania interfejs API zwróci jeden lub więcej jednostek spełniających kryteria zapytania wyszukiwania, które mogą być warte zauważenia użytkowników indywidualnych, lokalnych firm, charakterystycznych elementów krajobrazu i miejsc docelowych. |
| [Wyszukiwanie obrazów](../Bing-Image-Search/index.yml) | Interfejs API wyszukiwania obrazów Bing umożliwia wyszukiwanie i znajdowanie wysokiej jakości statyczne i animowany obrazy podobne do [Bing.com/images](https://www.Bing.com/images). Można zawęzić wyszukiwanie do dołączania lub wykluczania obrazów za pomocą atrybutu, takie jak rozmiar, kolor, licencji i aktualność. Można także wyszukiwanie popularnych obrazów, przekazywania obrazów w celu uzyskania szczegółowych informacji o nich i wyświetlania miniatur. |
| [Wyszukiwanie wiadomości](../Bing-News-Search/index.yml) | Interfejs API wyszukiwania wiadomości Bing umożliwia znalezienie najnowsze podobne do [Bing.com/news](https://www.Bing.com/news). Interfejs API zwraca artykuły z wiadomościami z wielu źródeł lub określonych domen. Możesz dodatkowo przeszukiwać kategorii, aby uzyskać trend, artykułów, Najciekawsze i nagłówki.
| [Wyszukiwanie klipów wideo](../Bing-Video-Search/index.yml) | Interfejs API wyszukiwania wideo Bing umożliwia wyszukuj klipy wideo w sieci web. Pobierz popularne wideo, powiązanej zawartości i miniatur. |
| [Visual Search](../Bing-visual-search/index.yml) | Przekaż obraz lub użyj adresu URL, aby pobrać wnikliwe informacje o tym, jak wizualnie podobne produkty, obrazy i powiązane wyszukiwania. |
 [Wyszukiwanie lokalnych firmach](../bing-local-business-search/index.yml) | Interfejs API wyszukiwania lokalnych firm Bing pozwala aplikacjom znajdować informacje kontaktowe oraz adresowe lokalnych firm na podstawie zapytań wyszukiwania. |

## <a name="the-bing-custom-search-api"></a>Interfejs API wyszukiwania niestandardowego Bing

Tworzenie wystąpienia wyszukiwania niestandardowego za pomocą [Bing Custom Search](../Bing-Custom-Search/index.yml) interfejsu API pozwala na tworzenie środowiska wyszukiwania, koncentruje się tylko na zawartość i tematy, które Cię interesują. Na przykład po określeniu domeny, witryn sieci Web i określonych stron sieci Web, która umożliwia wyszukiwanie Bing, wyniki zostaną dostosowane do tej określonej zawartości. Możesz dołączyć do nich niestandardowego automatycznego sugerowania Bing, obraz, a środowisko interfejsy API wyszukiwania wideo w celu dalszego dostosowywania wyszukiwania.  

## <a name="additional-bing-search-apis"></a>Interfejsy API wyszukiwania Bing dodatkowe

Następujące interfejsy API wyszukiwania Bing umożliwiają ulepszyć nasze usługi wyszukiwania, łącząc je z innymi interfejsami API wyszukiwania Bing.

| Interfejs API | Opis |
| -- | -- | 
| [Automatyczne sugerowanie Bing](../Bing-Autosuggest/index.yml) | Poprawić środowisko wyszukiwania aplikacji przy użyciu interfejs API automatycznego sugerowania Bing, zwracając sugerowane wyszukiwania w czasie rzeczywistym.  |
| [Dodatek Statystyka Bing](bing-web-stats.md) | Dodatek Statystyka Bing udostępnia analizy Twoja aplikacja używa interfejsów API wyszukiwania Bing. Analiza dostępnych między innymi wolumin wywołań, ciągi zapytania dotyczącego początkowych i rozmieszczenie geograficzne. |

## <a name="next-steps"></a>Kolejne kroki

* Interfejs API wyszukiwania Bing [szczegóły cennika](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)
* [Wymagania dotyczące użycia i wyświetlania Bing](./use-display-requirements.md) określają dopuszczalne zastosowania zawartości i informacji uzyskanych za pośrednictwem interfejsów API wyszukiwania Bing.
