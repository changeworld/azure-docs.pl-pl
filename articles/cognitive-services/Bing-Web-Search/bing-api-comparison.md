---
title: Co to są interfejsy API wyszukiwania Bing?
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera informacje o interfejsach API wyszukiwania Bing oraz o tym, jak włączyć kognitywne wyszukiwania w Internecie w aplikacjach i usługach.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: aahi
ms.openlocfilehash: 82b1f13562a49284059c25bcbd39a33daf949dcc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74775544"
---
# <a name="what-are-the-bing-search-apis"></a>Co to są interfejsy API wyszukiwania Bing?

Interfejsy API wyszukiwania Bing pozwalają tworzyć aplikacje i usługi połączone z Internetem, które znajdują strony internetowe, obrazy, wiadomości, lokalizacje i inne dane bez reklam. Wysyłając żądania wyszukiwania przy użyciu interfejsów API rest wyszukiwania Bing lub SDK, można uzyskać odpowiednie informacje i zawartość do wyszukiwania w sieci Web. Ten artykuł służy do zapoznania się z różnymi interfejsami API wyszukiwania Bing i jak można zintegrować wyszukiwania poznawcze z aplikacjami i usługami. Limity cen i stawek mogą się różnić w zależności od interfejsów API.

## <a name="the-bing-web-search-api"></a>Interfejs API wyszukiwania w sieci Web usługi Bing

Interfejs [API wyszukiwania w sieci Web usługi Bing](../Bing-Web-Search/overview.md) zwraca strony internetowe, obrazy, wideo, wiadomości i inne. Można filtrować zapytania wyszukiwania wysyłane do tego interfejsu API, aby uwzględnić lub wykluczyć niektóre typy zawartości.

Należy rozważyć użycie interfejsu API wyszukiwania w sieci Web Bing w aplikacjach, które mogą wymagać wyszukiwania wszystkich typów odpowiedniej zawartości sieci Web. Jeśli aplikacja wyszukuje określony typ zawartości online, należy wziąć pod uwagę jeden z poniższych interfejsów API wyszukiwania:

## <a name="content-specific-bing-search-apis"></a>Interfejsy API wyszukiwania Bing specyficzne dla zawartości

Następujące interfejsy API wyszukiwania Bing zwracają określone treści z sieci Web, takie jak obrazy, wiadomości, lokalne firmy i filmy wideo.

| Bing API | Opis |
| -- | -- |
| [Wyszukiwanie jednostek](../Bing-Entities-Search/overview.md) | Interfejs API wyszukiwania jednostek Bing zwraca wyniki wyszukiwania zawierające jednostki, które mogą być osobami, miejscami lub rzeczami. W zależności od zapytania interfejs API zwróci jedną lub więcej jednostek, które spełniają kwerendy wyszukiwania. Zapytanie wyszukiwania może zawierać godne uwagi osoby, lokalne firmy, punkty orientacyjne, miejsca docelowe i inne. |
| [Wyszukiwanie obrazów](../Bing-Image-Search/overview.md) | Interfejs API wyszukiwania obrazów Bing umożliwia wyszukiwanie i znajdowanie wysokiej jakości obrazów statycznych i animowanych podobnych do [Bing.com/images.](https://www.Bing.com/images) Można zawęzić wyszukiwania, aby uwzględnić lub wykluczyć obrazy według atrybutów, w tym rozmiaru, koloru, licencji i świeżości. Możesz też wyszukiwać popularne obrazy, przesyłać obrazy, aby uzyskać szczegółowe informacje na ich temat, i wyświetlać podglądy miniatur. |
| [Wyszukiwanie wiadomości](../Bing-News-Search/search-the-web.md) | Interfejs API wyszukiwania wiadomości Bing umożliwia znajdowanie wiadomości podobnych do [Bing.com/news](https://www.Bing.com/news). Interfejs API zwraca artykuły z wielu źródeł lub określonych domen. Możesz wyszukiwać w różnych kategoriach, aby otrzymywać popularne artykuły, najpopularniejsze artykuły i nagłówki. |
| [Wyszukiwanie wideo](../Bing-Video-Search/overview.md) | Interfejs API wyszukiwania wideo usługi Bing umożliwia znajdowanie klipów wideo w internecie. Otrzymuuj popularne filmy, powiązane treści i podglądy miniatur. |
| [Wyszukiwanie wizualne](../Bing-visual-search/overview.md) | Prześlij obraz lub użyj adresu URL, aby uzyskać wnikliwe informacje na jego temat, takie jak podobne wizualnie produkty, obrazy i powiązane wyszukiwania. |
 [Wyszukiwanie firm lokalnych](../bing-local-business-search/overview.md) | Interfejs API wyszukiwania lokalnego firmy Bing umożliwia aplikacjom znajdowanie informacji o kontaktach i lokalizacji dotyczących lokalnych firm na podstawie zapytań wyszukiwania. |

## <a name="the-bing-custom-search-api"></a>Interfejs API wyszukiwania niestandardowego usługi Bing

Tworzenie niestandardowego wystąpienia wyszukiwania za pomocą interfejsu API [wyszukiwania niestandardowego Bing](../Bing-Custom-Search/overview.md) umożliwia utworzenie środowiska wyszukiwania skoncentrowanego tylko na zawartości i tematach, na których Ci zależy. Na przykład po określeniu domen, witryn sieci Web i określonych stron sieci Web, które będą przeszukiwać bing, wyszukiwanie niestandardowe Bing dostosuje wyniki do tej określonej zawartości. Można włączyć niestandardowe interfejsy API automatycznego zasysania niestandardowych bing, obrazu i wyszukiwania wideo, aby jeszcze bardziej dostosować środowisko wyszukiwania.

## <a name="additional-bing-search-apis"></a>Dodatkowe interfejsy API wyszukiwania Bing

Poniższe interfejsy API wyszukiwania Bing umożliwiają poprawę środowiska wyszukiwania, łącząc je z innymi interfejsami API wyszukiwania Bing.

| interfejs API | Opis |
| -- | -- |
| [Automatyczne sugerowanie Bing](../Bing-Autosuggest/get-suggested-search-terms.md) | Usprawnij wyszukiwanie aplikacji za pomocą interfejsu API autosugerowania Bing, zwracając sugerowane wyszukiwania w czasie rzeczywistym.  |
| [Statystyki Bing](bing-web-stats.md) | Statystyki Bing udostępnia analizy dla interfejsów API wyszukiwania Bing aplikacji używa. Niektóre z dostępnych analiz obejmują wolumin wywołań, górne ciągi zapytań i dystrybucję geograficzną. |

## <a name="next-steps"></a>Następne kroki

* [Szczegóły cen](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) interfejsu API wyszukiwania Bing
* [Wymagania dotyczące użycia i wyświetlania Bing](./use-display-requirements.md) określają dopuszczalne zastosowania zawartości i informacji uzyskanych za pośrednictwem interfejsów API wyszukiwania Bing.
