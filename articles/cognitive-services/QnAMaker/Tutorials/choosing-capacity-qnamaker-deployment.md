---
title: Pojemność zasobów dla wdrożenia — QnA Maker
titleSuffix: Azure Cognitive Services
description: Przed przystąpieniem do tworzenia usługi QnA Maker możesz zdecydować, którą warstwę usług powyżej jest odpowiednia dla Ciebie.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 08/30/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 2d8f0fce3cb8f1cd8fdb596cb4e238a79d6cee4c
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70193507"
---
# <a name="choosing-capacity-for-your-qna-maker-deployment"></a>Wybieranie pojemności dla danego wdrożenia usługi QnA Maker

Usługa QnA Maker przyjmuje zależność dla trzech zasobów platformy Azure:
1.  App Service (dla środowiska wykonawczego)
2.  Azure Search (do przechowywania i wyszukiwania bazami)
3.  App Insights (opcjonalnie, do przechowywania dzienniki czatu i telemetrii)

Przed przystąpieniem do tworzenia usługi QnA Maker możesz zdecydować, którą warstwę usług powyżej jest odpowiednia dla Ciebie. 

Zwykle istnieją trzy parametry, które należy wziąć pod uwagę:

1. **Przepływność wymagana przez usługę**: Wybierz odpowiedni [Plan aplikacji](https://azure.microsoft.com/pricing/details/app-service/plans/) dla usługi App Service na podstawie Twoich potrzeb. Możesz [skalowanie w górę](https://docs.microsoft.com/azure/app-service/manage-scale-up) lub w dół aplikacji. To powinien również mieć wpływ na wybór jednostki SKU usługi Azure Search, zobacz więcej szczegółów [tutaj](https://docs.microsoft.com/azure/search/search-sku-tier).

1. **Rozmiar i liczba baz wiedzy**: Wybierz odpowiednią [jednostkę SKU usługi Azure Search](https://azure.microsoft.com/pricing/details/search/) dla danego scenariusza. N-1, baz wiedzy można publikować w konkretnej warstwy, gdzie N to maksymalna liczba indeksów dozwolone w ramach warstwy. Sprawdź również maksymalnego rozmiaru i liczby dokumentów dozwolone na warstwę.

    Na przykład jeśli warstwa ma 15 dozwolonych indeksów, można opublikować 14 baz wiedzy (1 indeks na opublikowaną bazę wiedzy). 15. indeks jest używany dla wszystkich baz wiedzy na potrzeby tworzenia i testowania. 

1. **Liczba dokumentów jako źródła**: Bezpłatna jednostka SKU usługi zarządzania QnA Makerą ogranicza liczbę dokumentów, którymi można zarządzać za pośrednictwem portalu i interfejsów API do 3 (o rozmiarze 1 MB). Standardowa jednostka SKU nie ma limitów liczby dokumentów, którymi można zarządzać. Zobacz więcej szczegółów [tutaj](https://aka.ms/qnamaker-pricing).

W poniższej tabeli przedstawiono niektóre wytyczne wysokiego poziomu.

|                        | Usługa QnA Maker zarządzania | App Service | Azure Search | Ograniczenia                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| Eksperymentowanie        | Bezpłatna jednostka SKU             | Warstwa Bezpłatna   | Warstwa Bezpłatna    | Publikowanie do 2 artykułów bazy wiedzy, rozmiar 50 MB  |
| Środowisko programistyczne/testowe   | Standardowy SKU         | Udostępniona      | Podstawowa        | Publikowanie do 14 artykułów bazy wiedzy, rozmiar 2 GB    |
| Środowiska produkcyjnego | Standardowy SKU         | Podstawowa       | Standardowa (Standard)     | Publikowanie do 49 artykułów bazy wiedzy, rozmiar 25 GB |

Dla uaktualnienie stosie usługi QnA Maker, zobacz [uaktualnienia usługi QnA Maker](../How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Uaktualnianie usługi QnA Maker](../How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker)
