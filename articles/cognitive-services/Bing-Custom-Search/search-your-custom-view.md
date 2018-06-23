---
title: 'Usługi Bing wyszukiwanie niestandardowe: Widok niestandardowy wyszukiwania | Dokumentacja firmy Microsoft'
description: Opisuje sposób wyszukiwania niestandardowy widok sieci web
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 75f6c8d299c7eed901dda0631fca74b040f72e30
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347852"
---
# <a name="call-your-custom-search"></a>Wywołanie wyszukiwania niestandardowego
Przed utworzeniem pierwsze wywołanie interfejsu API wyszukiwania niestandardowego można pobrać wyniki wyszukiwania dla swojego wystąpienia, należy uzyskać klucz subskrypcji usługi kognitywnych. Aby uzyskać klucz interfejsu API wyszukiwania niestandardowych, zobacz [spróbuj kognitywnych usług](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).

> [!NOTE]
> Istniejące wyszukiwania usługi Bing niestandardowe klientów z kluczem Podgląd zainicjowano obsługę administracyjną w tym miejscu lub przed 15 października 2017 będzie można używać ich kluczy do 2017 30 listopada lub do momentu wyczerpać maksymalną liczbę dozwolone kwerendy. Później muszą one migracja do wersji ogólnie dostępna na platformie Azure.

## <a name="try-it-out"></a>Wypróbuj
Po skonfigurowaniu środowiska wyszukiwania niestandardowego można przetestować konfigurację z portalu wyszukiwania niestandardowego. Zaloguj się do [wyszukiwanie niestandardowe](https://customsearch.ai), kliknij wystąpienie wyszukiwanie niestandardowe i kliknij przycisk **produkcji** kartę. **Punkty końcowe** karta jest wyświetlana. Subskrypcja określi, które punkty końcowe są dostępne, spróbuj, zobacz [cennik stron](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/). Aby przetestować punkt końcowy, wybierz ją z listy rozwijanej i ustaw skojarzonych opcji konfiguracji. 

Poniżej przedstawiono dostępne opcje.

- **Zapytanie**: wyszukiwany termin do wyszukania. Dostępne tylko w przypadku sieci Web, obrazu i Autosugerowanie punktów końcowych.
- **Niestandardowy identyfikator konfiguracji**: identyfikator konfiguracji wybranego wystąpienia wyszukiwanie niestandardowe. To pole jest tylko do odczytu.
- **Rynku**: rynku, skąd wyniki. Dostępne tylko w przypadku punktów końcowych sieci Web, obrazu i hostowanej interfejsu użytkownika.
- **Klucz subskrypcji**: klucz subskrypcji do testów. Może wybrać z listy rozwijanej klucz lub wprowadzić ręcznie.
- **Bezpieczne wyszukiwania**: Filtr używane do filtrowania stron sieci Web dla dorosłych. Dostępne tylko w przypadku punktów końcowych sieci Web, obrazu i hostowanej interfejsu użytkownika.
- **Liczba**: liczba wyników wyszukiwania, aby zwrócić w odpowiedzi. Dostępne tylko w przypadku punktów końcowych sieci Web i obrazów.
- **Przesunięcie**: liczba wyników wyszukiwania, aby zwrócić w odpowiedzi. Dostępne tylko w przypadku punktów końcowych sieci Web i obrazów.

Po określeniu wszystkie wymagane opcje dla sieci Web, Image lub Autosugerowanie kliknij **wywołać** do wyświetlania w okienku po prawej stronie odpowiedź w formacie JSON. 

W przypadku wybrania punktu końcowego hostowanej interfejsu użytkownika można przetestować środowiska wyszukiwania w okienku po prawej stronie.

## <a name="next-steps"></a>Kolejne kroki
- [Wywołaj w widoku niestandardowym języku C#](./call-endpoint-csharp.md)
- [Wywołaj w widoku niestandardowym z językiem Java](./call-endpoint-java.md)
- [Wywołaj w widoku niestandardowym z NodeJs](./call-endpoint-nodejs.md)
- [Wywołaj w widoku niestandardowym języka Python](./call-endpoint-python.md)