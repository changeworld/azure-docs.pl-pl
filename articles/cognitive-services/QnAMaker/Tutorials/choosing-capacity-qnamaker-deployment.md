---
title: Wybieranie pojemności dla danego wdrożenia Maker — strona główna - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Przewodnik dotyczący Wybieranie pojemności dla danego wdrożenia Maker — strona główna
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: b0219b9f7dbbee52406dab9d808134fa2e2a689d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348660"
---
# <a name="choosing-capacity-for-your-qna-maker-deployment"></a>Wybieranie pojemności dla danego wdrożenia Maker — strona główna

Usługa Maker — strona główna ma zależność na trzy zasobów platformy Azure:
1.  Usługi aplikacji (dla środowiska wykonawczego)
2.  Wyszukiwanie Azure (na potrzeby przechowywania QnAs)
3.  Insights aplikacji (opcjonalnie, do przechowywania chatlogs i dane telemetryczne)

Przed utworzeniem usługi Maker — strona główna, należy podjąć decyzję dotyczącą które warstw usług powyżej jest odpowiednie dla Ciebie. 

Zazwyczaj są trzy parametry, które należy wziąć pod uwagę:
1. **Przepływność należy z usługi**: wybierz odpowiednie [Plan aplikacji](https://azure.microsoft.com/en-in/pricing/details/app-service/plans/) usługi aplikacji na podstawie Twoich potrzeb. Możesz [skalowanie w górę](https://docs.microsoft.com/en-us/azure/app-service/web-sites-scale) lub w dół aplikacji. To powinien również wpływać wybór SKU wyszukiwanie Azure można znaleźć więcej szczegółów [tutaj](https://docs.microsoft.com/en-us/azure/search/search-sku-tier).

2. **Rozmiaru i liczby baz wiedzy**: Wybierz odpowiedni [Azure wyszukiwania SKU](https://azure.microsoft.com/en-in/pricing/details/search/) dla danego scenariusza. Możesz opublikować baz wiedzy N-1 w określonej warstwie, gdzie N to indeksy maksymalna dozwolona w warstwie. Sprawdź również maksymalnego rozmiaru i liczby dokumentów dozwolonych dla jednej warstwy.

3. **Liczba dokumentów jako źródła**: wolnego jednostki SKU Maker — strona główna usługa zarządzania ogranicza liczbę dokumentów można zarządzać za pomocą portalu i interfejsy API 3 (1 MB rozmiar każdego). Standardowy SKU ma nie limity liczby dokumentów, którymi można zarządzać. Zobacz szczegółowe [tutaj](https://aka.ms/qnamaker-pricing).

W poniższej tabeli przedstawiono ogólne wskazówki.

|                        | Zarządzanie Maker — strona główna | App Service | Azure Search | Ograniczenia                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| Eksperymentowanie        | Wolne jednostki SKU             | Warstwa Bezpłatna   | Warstwa Bezpłatna    | Publikowanie do 2 Kb/s, rozmiar 50 MB  |
| Środowisko: tworzenie i testowanie   | Standardowy SKU         | Udostępniona      | Podstawowa        | Publikowanie do 4 Kb/s, rozmiar 2GB    |
| Środowiska produkcyjnego | Standardowy SKU         | Podstawowa       | Standardowa (Standard)     | Publikowanie do 49 KB/s, rozmiaru 25 GB |

Do uaktualnienia stosu — strona główna Maker, zobacz [Uaktualnij usługę Maker — strona główna](../How-To/upgrade-qnamaker-service.md).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Uaktualnij usługę Maker — strona główna](../How-To/upgrade-qnamaker-service.md)
