---
title: Pojemność zasobów na potrzeby wdrożenia — QnA Maker
titleSuffix: Azure Cognitive Services
description: Przed utworzeniem usługi QnA Maker należy zdecydować, która warstwa powyższych usług jest odpowiednia dla Ciebie.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 08/30/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 5cbdb6fcf9fcdf12b54ff1db4b577bb975517131
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73793946"
---
# <a name="choosing-capacity-for-your-qna-maker-deployment"></a>Wybieranie pojemności dla wdrożenia QnA Maker

Usługa QnA Maker, bierze zależność od trzech zasobów platformy Azure:
1.  App Service (dla środowiska uruchomieniowego)
2.  Wyszukiwanie poznawcze platformy Azure (do przechowywania i wyszukiwania bazami)
3.  App Insights (opcjonalnie, do przechowywania dzienników i telemetrii rozmów)

Przed utworzeniem usługi QnA Maker należy zdecydować, która warstwa powyższych usług jest odpowiednia dla Ciebie. 

Zwykle istnieją trzy parametry, które należy wziąć pod uwagę:

1. **Wymagana przepływność z usługi**: wybierz odpowiedni [Plan aplikacji](https://azure.microsoft.com/pricing/details/app-service/plans/) dla usługi App Service na podstawie Twoich potrzeb. Możesz [skalować w górę](https://docs.microsoft.com/azure/app-service/manage-scale-up) lub w dół aplikacji. Powinno to mieć wpływ na wybór jednostki SKU platformy Azure Wyszukiwanie poznawcze, zobacz [tutaj](https://docs.microsoft.com/azure/search/search-sku-tier)więcej szczegółów.

1. **Rozmiar i liczba baz wiedzy**: wybierz odpowiednią [jednostkę SKU usługi Azure Search](https://azure.microsoft.com/pricing/details/search/) dla danego scenariusza. Bazy wiedzy o N-1 można opublikować w określonej warstwie, gdzie N to maksymalna liczba indeksów dozwolonych w warstwie. Sprawdź również maksymalny rozmiar i liczbę dokumentów dozwolonych dla każdej warstwy.

    Na przykład jeśli warstwa ma 15 dozwolonych indeksów, można opublikować 14 baz wiedzy (1 indeks na opublikowaną bazę wiedzy). 15. indeks jest używany dla wszystkich baz wiedzy na potrzeby tworzenia i testowania. 

1. **Liczba dokumentów jako źródła**: bezpłatna jednostka SKU usługi zarządzania QNA Makerą ogranicza liczbę dokumentów, którymi można zarządzać za pośrednictwem portalu i interfejsów API do 3 (o rozmiarze 1 MB). Standardowa jednostka SKU nie ma ograniczeń do liczby dokumentów, którymi można zarządzać. Więcej szczegółów można znaleźć [tutaj](https://aka.ms/qnamaker-pricing).

W poniższej tabeli przedstawiono ogólne wytyczne.

|                        | Zarządzanie QnA Maker | App Service | Azure Cognitive Search | Ograniczenia                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| Eksperymentowanie        | Bezpłatna jednostka SKU             | Warstwa Bezpłatna   | Warstwa Bezpłatna    | Publikowanie do 2 artykułów bazy wiedzy, rozmiar 50 MB  |
| Środowisko deweloperskie/testowe   | Standardowy SKU         | Udostępnione      | Podstawowa        | Publikowanie do 14 artykułów bazy wiedzy, rozmiar 2 GB    |
| Środowisko produkcyjne | Standardowy SKU         | Podstawowa       | Standardowa     | Publikowanie do 49 artykułów bazy wiedzy, rozmiar 25 GB |

Aby uaktualnić stos QnA Maker, zobacz [uaktualnianie usługi QNA Maker](../How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Uaktualnianie usługi QnA Maker](../How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker)
