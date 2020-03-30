---
title: Drzwi frontowe platformy Azure — przepisywanie adresów URL | Dokumenty firmy Microsoft
description: Ten artykuł pomaga zrozumieć, jak usługa Azure Front Door nie zapisuj adresy URL dla tras, jeśli jest skonfigurowany.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 1e5bd565be7a1cabf08ddf33c65eb12b5294249f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471476"
---
# <a name="url-rewrite-custom-forwarding-path"></a>Przepisanie adresu URL (niestandardowa ścieżka przekazywania dalej)
Usługa Azure Front Door obsługuje przepisywanie adresów URL, umożliwiając skonfigurowanie opcjonalnej **niestandardowej ścieżki przekazywania** do użycia podczas konstruowania żądania do przekazania do wewnętrznej bazy danych. Domyślnie jeśli nie ma określonej niestandardowej ścieżki przesyłania dalej, usługa Front Door kopiuje przychodzącą ścieżkę adresu URL do adresu URL użytego w przesłanym dalej żądaniu. Nagłówek Host użyty w przesłanym dalej żądaniu jest skonfigurowany dla wybranego zaplecza. Przeczytaj [nagłówek hosta wewnętrznej bazy danych,](front-door-backend-pool.md#hostheader) aby dowiedzieć się, co robi i jak można go skonfigurować.

Zaawansowana część tworzenia adresów URL przy użyciu niestandardowej ścieżki przekazywania danych polega na tym, że skopiuje dowolną część ścieżki przychodzącej, która pasuje do ścieżki wieloznacznej do ścieżki przekazywanej (te segmenty ścieżki są **zielonymi** segmentami w poniższym przykładzie):
</br>
![Przepisanie adresu URL drzwiami frontowymi platformy Azure][1]

## <a name="url-rewrite-example"></a>Przykład przepisywania adresu URL
Należy wziąć pod uwagę regułę routingu z następującymi hostami frontendu i skonfigurowaną ścieżkami:

| Hosts      | Ścieżki       |
|------------|-------------|
| www\.contoso.com | /\*         |
|            | /foo        |
|            | /foo/\*     |
|            | /foo/bar/\* |

Pierwsza kolumna poniższej tabeli zawiera przykłady przychodzących żądań, a druga kolumna pokazuje, jaka byłaby "najbardziej specyficzna" pasująca trasa "Ścieżka".  Trzecia i kolejne kolumny pierwszego wiersza tabeli są przykładami skonfigurowanych **niestandardowych ścieżek przekazywania,** a pozostałe wiersze w tych kolumnach reprezentują przykłady, jaka byłaby ścieżka żądania przesyłanego dalej, gdyby była dopasowana do żądania w tym wierszu.

Na przykład, jeśli czytamy w drugim wierszu, to `www.contoso.com/sub`mówi, że dla `/`żądania przychodzącego , `/sub`jeśli niestandardowa ścieżka przekazywania była , to ścieżka przesyłana dalej będzie . Jeśli niestandardowa ścieżka `/fwd/`przekazywania była , to `/fwd/sub`ścieżka przesyłana dalej będzie . I tak dalej, dla pozostałych kolumn. **Podkreślone** części ścieżek poniżej reprezentują części, które są częścią dopasowania symboli wieloznacznych.


| Żądanie przychodzące       | Najbardziej specyficzna ścieżka dopasowania | /          | /fwd/          | /foo/          | /foo/bar/          |
|------------------------|--------------------------|------------|----------------|----------------|--------------------|
| www\.contoso.com/            | /\*                      | /          | /fwd/          | /foo/          | /foo/bar/          |
| www\.contoso.com/**sub**     | /\*                      | /**Sub**   | /fwd/**sub**   | /foo/**sub**   | /foo/bar/**sub**   |
| www\.contoso.com/**a/b/c**   | /\*                      | /**o/b/c** | /fwd/**a/b/c** | /foo/**a/b/c** | /foo/bar/**a/b/c** |
| www\.contoso.com/foo         | /foo                     | /          | /fwd/          | /foo/          | /foo/bar/          |
| www\.contoso.com/foo/        | /foo/\*                  | /          | /fwd/          | /foo/          | /foo/bar/          |
| www\.contoso.com/foo/**bar** | /foo/\*                  | /**Bar**   | /fwd/**bar**   | /foo/**bar**   | /foo/bar/bar/**bar**   |


## <a name="optional-settings"></a>Ustawienia opcjonalne
Istnieją dodatkowe ustawienia opcjonalne, które można również określić dla dowolnych ustawień reguły routingu:

* **Konfiguracja pamięci podręcznej** — jeśli jest wyłączona lub nie określono, żądania zgodne z tą regułą routingu nie będą próbowały użyć zawartości w pamięci podręcznej i zamiast tego zawsze będą pobierane z wewnętrznej bazy danych. Przeczytaj więcej o [buforowaniu z drzwiami przednimi](front-door-caching.md).



## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg