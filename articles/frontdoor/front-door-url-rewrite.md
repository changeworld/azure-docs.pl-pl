---
title: Azure Front drzwiczki Service — ponowne zapisywanie adresów URL | Dokumentacja firmy Microsoft
description: Ten artykuł pomoże Ci zrozumieć, jak Azure drzwiami frontowymi usługa ma ponownego zapisywania adresów URL dla trasy, jeśli skonfigurowane.
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
ms.openlocfilehash: 00fe3aa7a641b9d07aad90a9d008a99efc6e9d97
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46993477"
---
# <a name="url-rewrite-custom-forwarding-path"></a>Ponowne zapisywanie adresów URL (ścieżka przekazywanie niestandardowych)
Usługa drzwiami frontowymi obsługuje ponowne zapisywanie adresów URL, umożliwiając konfigurowanie opcjonalny **ścieżki przekazywania niestandardowego** do użycia podczas konstruowania żądania do zaplecza. Domyślnie jeśli brak ścieżki niestandardowej przekazywania zostanie podany, następnie drzwiami frontowymi skopiuje przychodzących Ścieżka adresu URL na adres URL użyty w żądaniu przekazywane. Nagłówek hosta, użyty w żądaniu przekazywane jest zgodnie z konfiguracją dla wybranego zaplecza. Odczyt [nagłówek hosta zaplecza](front-door-backend-pool.md#hostheader) Aby dowiedzieć się, jak działa i jak można go skonfigurować.

Zaawansowana część ponowne zapisywanie adresów URL przy użyciu ścieżki niestandardowe przekazywania jest, spowoduje to skopiowanie dowolnej części ścieżki przychodzącej, który pasuje do ścieżki symboli wieloznacznych w ścieżce przekazywane (te segmenty ścieżki są **zielony** segmentów w poniższym przykładzie):
</br>
![Ponowne zapisywanie adresów URL w drzwi wejściowe platformy Azure][1]

## <a name="url-rewrite-example"></a>Przykład ponownego zapisywania adresu URL
Następujące hosty frontonu i skonfigurowanych ścieżek, należy wziąć pod uwagę reguły routingu:

| Hosts      | Ścieżki       |
|------------|-------------|
| www.contoso.com | /\*         |
|            | /foo        |
|            | /foo/\*     |
|            | /foo/paska /\* |

Pierwszą kolumnę w poniższej tabeli przedstawiono przykłady żądań przychodzących i druga kolumna zawiera, jaki byłyby pasującej trasy "większość specific" "Path".  Trzecia i kolejnych kolumn pierwszy wiersz w tabeli przedstawiono przykłady skonfigurowanego **ścieżki przekazywania niestandardowe**, z użyciem usług rest wierszach tych kolumn reprezentujących przykłady co przekazane żądanie byłaby to ścieżka Jeśli był zgodny z na żądanie, w tym wierszu.

Na przykład, jeśli możemy odczytać w drugim wierszu, jego jest informacją, że dla żądania przychodzącego `www.contoso.com/sub`, jeśli ścieżka przekazywanie niestandardowych `/`, a następnie przekazywane ścieżka byłaby `/sub`. Jeśli ścieżka przekazywanie niestandardowych `/fwd/`, a następnie przekazywane ścieżka byłaby `/fwd/sub`. I tak dalej dla pozostałych kolumn. **Wyróżniono** części ścieżki poniżej reprezentują te części, które są częścią dopasowanie z symbolami wieloznacznymi.


| Żądanie przychodzące       | Ścieżka specyficzne dla większości dopasowania | /          | /FWD/          | /foo/          | /foo/paska /          |
|------------------------|--------------------------|------------|----------------|----------------|--------------------|
| www.contoso.com/            | /\*                      | /          | /FWD/          | /foo/          | /foo/paska /          |
| www.contoso.com/**sub**     | /\*                      | /**Sub**   | /FWD/**sub**   | /foo/**sub**   | /foo/paska/**sub**   |
| www.contoso.com/**a/b/c.**   | /\*                      | /**a/b/c.** | /FWD/**a/b/c.** | /foo/**a/b/c.** | /foo/paska/**a/b/c.** |
| www.contoso.com/foo         | /foo                     | /          | /FWD/          | /foo/          | /foo/paska /          |
| www.contoso.com/foo/        | /foo/\*                  | /          | /FWD/          | /foo/          | /foo/paska /          |
| www.contoso.com/foo/**paska** | /foo/\*                  | /**Pasek**   | /FWD/**paska**   | /foo/**paska**   | /foo/paska/**paska**   |


## <a name="optional-settings"></a>Ustawienia opcjonalne
Dostępne są dodatkowe ustawienia opcjonalne, które można również określić dla danego ustawienia reguły routingu:

* **Konfiguracja w pamięci podręcznej** — Jeśli wyłączone lub nie określono żądania, które odpowiadają tej reguły routingu nie podejmie próbę użycia zawartości w pamięci podręcznej, a zamiast tego będzie zawsze fetch z wewnętrznej bazy danych. Przeczytaj więcej na temat [buforowania przy wejściu](front-door-caching.md).



## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [tworzenie drzwiami frontowymi](quickstart-create-front-door.md).
- Dowiedz się, [działania drzwiami frontowymi](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg