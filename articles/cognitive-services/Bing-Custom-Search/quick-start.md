---
title: 'Szybki start: tworzenie pierwszego wystąpienia usługi wyszukiwania niestandardowego Bing'
titlesuffix: Azure Cognitive Services
description: Aby móc skorzystać z usługi wyszukiwania niestandardowego Bing, należy utworzyć wystąpienie wyszukiwania niestandardowego definiujące widok lub wycinek sieci Internet. Wystąpienie zawiera ustawienia określające domeny publiczne, lokacje podrzędne i strony internetowe, które mają być przeszukiwane przez usługę Bing, oraz wszelkie dostosowania klasyfikacji.
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2017
ms.author: v-brapel
ms.openlocfilehash: 866d32aa4de45076fcbc4e413d8c2e67d5346878
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816243"
---
# <a name="quickstart-create-your-first-bing-custom-search-instance"></a>Szybki start: tworzenie pierwszego wystąpienia usługi wyszukiwania niestandardowego Bing
Aby móc skorzystać z usługi wyszukiwania niestandardowego Bing, należy utworzyć wystąpienie wyszukiwania niestandardowego definiujące widok lub wycinek sieci Internet. Wystąpienie zawiera ustawienia, które określają domeny publiczne, witryny i strony internetowe, które mają być wyszukiwane przez usługę Bing, oraz wszelkie dostosowania klasyfikacji. Aby utworzyć wystąpienie, użyj [portalu](https://customsearch.ai) usługi wyszukiwania niestandardowego Bing. 

## <a name="create-a-custom-search-instance"></a>Tworzenie wystąpienia wyszukiwania niestandardowego

Aby utworzyć wystąpienie wyszukiwania niestandardowego Bing:

1.  Uzyskaj klucz interfejsu API wyszukiwania niestandardowego. Zobacz [Możliwość wypróbowania usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).
2.  Kliknij przycisk **Sign in** (Zaloguj się) i zaloguj się do portalu przy użyciu konta Microsoft (MSA). 
    - Jeśli nie masz konta MSA, kliknij pozycję **Create a Microsoft account** (Utwórz konto Microsoft). Portal poprosi o udzielenie uprawnień dostępu do danych. Kliknij przycisk **Yes** (Tak).
    - Zaakceptuj warunki usługi Cognitive Services. Zaznacz pole wyboru **I agree** (Zgadzam się) i kliknij przycisk **Agree** (Zgadzam się).  
3.  Po zalogowaniu się kliknij pozycję **New instance** (Nowe wystąpienie) i podaj nazwę wystąpienia. Wprowadź istotną nazwę opisową typu zawartości zwracanej przez wyszukiwanie. Nazwę można zmienić w dowolnym momencie. 
4.  Na karcie **Active** (Aktywne) w obszarze **Search Experience** (Funkcja wyszukiwania) wprowadź adres URL jednej lub większej liczby witryn internetowych, które mają zostać uwzględnione w wyszukiwaniu.
5.  Aby upewnić się, że wystąpienie zwraca wyniki, wprowadź zapytanie w okienku podglądu po prawej stronie. Jeśli nie ma żadnych wyników, należy określić nową witrynę internetową. Wyszukiwarka Bing zwraca tylko wyniki z witryn publicznych, które zostały przez nią zindeksowane.
6.  Kliknij przycisk **Publish** (Publikuj), aby opublikować zmiany konfiguracji w środowisku produkcyjnym. Po wyświetleniu monitu kliknij przycisk **Publish** (Publikuj), aby potwierdzić.
7.  Kliknij opcję **Production** > **Endpoints** (Produkcja, Punkty końcowe) i skopiuj wartość **Custom Configuration ID** (Identyfikator konfiguracji niestandardowej). Ten identyfikator jest potrzebny do wywołania interfejsu API wyszukiwania niestandardowego.

## <a name="next-steps"></a>Następne kroki

Kontynuuj pracę z utworzonym wystąpieniem wyszukiwania niestandardowego, postępując zgodnie z instrukcjami w następujących przewodnikach:

- [Konfigurowanie środowiska wyszukiwania niestandardowego](./define-your-custom-view.md)
- [Wywoływanie wyszukiwania niestandardowego](./search-your-custom-view.md)
- [Udostępnianie wyszukiwania niestandardowego](./share-your-custom-search.md)
- [Konfigurowanie środowiska hostowanego interfejsu użytkownika](./hosted-ui.md)
- [Wyróżnianie tekstu za pomocą znaczników dekoracji](./hit-highlighting.md)
- [Dzielenie na strony wyników wyszukiwania stron internetowych](./page-webpages.md)
