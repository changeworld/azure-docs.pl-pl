---
title: Pojemność zasobów dla wdrożenia — QnA Maker
titleSuffix: Azure Cognitive Services
description: Przed przystąpieniem do tworzenia usługi QnA Maker możesz zdecydować, którą warstwę usług powyżej jest odpowiednia dla Ciebie.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/24/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: a332d263526bb6507e7394c205caa1c4d1f9e3e6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60613274"
---
# <a name="choosing-capacity-for-your-qna-maker-deployment"></a>Wybieranie pojemności dla danego wdrożenia usługi QnA Maker

Usługa QnA Maker przyjmuje zależność dla trzech zasobów platformy Azure:
1.  App Service (dla środowiska wykonawczego)
2.  Usługa Azure Search (do przechowywania znacznie)
3.  App Insights (opcjonalnie, do przechowywania dzienniki czatu i telemetrii)

Przed przystąpieniem do tworzenia usługi QnA Maker możesz zdecydować, którą warstwę usług powyżej jest odpowiednia dla Ciebie. 

Zwykle istnieją trzy parametry, które należy wziąć pod uwagę:

1. **Przepływność należy z niej**: Wybierz odpowiedni [Plan usługi App](https://azure.microsoft.com/pricing/details/app-service/plans/) dla usługi App service zgodnie z potrzebami. Możesz [skalowanie w górę](https://docs.microsoft.com/azure/app-service/web-sites-scale) lub w dół aplikacji. To powinien również mieć wpływ na wybór jednostki SKU usługi Azure Search, zobacz więcej szczegółów [tutaj](https://docs.microsoft.com/azure/search/search-sku-tier).

1. **Rozmiaru i liczby baz wiedzy**: Wybierz odpowiedni [Azure wyszukiwanie jednostek SKU](https://azure.microsoft.com/pricing/details/search/) dla danego scenariusza. N-1, baz wiedzy można publikować w konkretnej warstwy, gdzie N to maksymalna liczba indeksów dozwolone w ramach warstwy. Sprawdź również maksymalnego rozmiaru i liczby dokumentów dozwolone na warstwę.

    Na przykład warstwę ma 15 dozwolonych indeksów, można opublikować 14 bazy wiedzy (1 indeks na opublikowane bazy wiedzy knowledge base). 15 indeks jest używany dla wszystkich baz wiedzy do tworzenia i testowania. 

1. **Liczba dokumentów jako źródła**: Bezpłatna jednostka SKU usługi zarządzania usługi QnA Maker ogranicza liczbę dokumentów, którymi można zarządzać za pośrednictwem portalu i interfejsów API do 3 (1 MB rozmiar każdego). Standardowa jednostka SKU nie ma limitów liczby dokumentów, którymi można zarządzać. Zobacz więcej szczegółów [tutaj](https://aka.ms/qnamaker-pricing).

W poniższej tabeli przedstawiono niektóre wytyczne wysokiego poziomu.

|                        | Usługa QnA Maker zarządzania | App Service | Azure Search | Ograniczenia                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| Eksperymentowanie        | Bezpłatna jednostka SKU             | Warstwa Bezpłatna   | Warstwa Bezpłatna    | Publikowanie do 2 artykułów bazy wiedzy, rozmiar 50 MB  |
| Środowisko programistyczne/testowe   | Standardowy SKU         | Udostępniona      | Podstawowa        | Publikowanie do 14 artykułów bazy wiedzy, rozmiar 2 GB    |
| Środowiska produkcyjnego | Standardowy SKU         | Podstawowa       | Standardowa (Standard)     | Publikowanie do 49 artykułów bazy wiedzy, rozmiar 25 GB |

Dla uaktualnienie stosie usługi QnA Maker, zobacz [uaktualnienia usługi QnA Maker](../How-To/upgrade-qnamaker-service.md).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Uaktualnianie usługi QnA Maker](../How-To/upgrade-qnamaker-service.md)
