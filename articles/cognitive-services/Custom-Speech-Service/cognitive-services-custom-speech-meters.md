---
title: Przydziały na platformie Azure i usługi mowy liczników niestandardowych | Dokumentacja firmy Microsoft
description: Informacje o liczników i przydziały niestandardowe mowy usługi na platformie Azure.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 07/08/2017
ms.author: panosper
ms.openlocfilehash: d2225dec818c600febfad2f9ebc42594f6ac09ac
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346932"
---
# <a name="custom-speech-service-meters-and-quotas"></a>Liczniki usługi mowy i przydziałów niestandardowych

Za pomocą niestandardowych mowy usługę w chmurze można dostosowywać mowy modeli przekształcania mowy na tekst.

Aby rozpocząć korzystanie z usługi rozpoznawania mowy niestandardowych, przejdź do [portalu niestandardowe mowy](https://cris.ai).

Bieżącego cennika liczników, przejdź do [cennik usług kognitywnych niestandardowej usługi rozpoznawania mowy](https://azure.microsoft.com/pricing/details/cognitive-services/custom-speech-service/) strony.

## <a name="tiers-explained"></a>Wyjaśniono warstw
Testowanie i prototypu tylko Sugerujemy użyć warstwę bezpłatna F0. Dla systemów produkcyjnych Sugerujemy używać warstwy S2. Za pomocą warstwy S2 można skalować wdrożenia liczby jednostek skalowania (SUs), które są wymagane w danym scenariuszu.

> [!NOTE]
> Możesz *nie* migracja między warstwą F0 a warstwą S2.
>

## <a name="meters-explained"></a>Wyjaśniono liczników

### <a name="scale-out"></a>Skalowanie w poziomie
Skaluj w poziomie jest nową funkcją, które firma Microsoft wydała z nowego modelu cenowego. Za pomocą Skaluj w poziomie, można kontrolować liczbę jednoczesnych żądań, jaką może przetworzyć modelu.

Można ustawić równoczesnych żądań przy użyciu miary SU w **tworzenia modelu wdrażania** widoku. Aby uzyskać więcej informacji, zobacz [Tworzenie niestandardowych końcowego mowy na tekst](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-endpoint.md). W zależności od ilości ruchu, który należy przewidzieć wykorzystywanie modelu można wybrać odpowiednią liczbę SUs. 

> [!NOTE]
> Każdą jednostką skalowania gwarantuje 5 współbieżnych żądań. Możesz kupić 1 lub więcej SUs, zależnie od potrzeb. Ponieważ liczba SUs zwiększa się o 1, liczbę jednoczesnych żądań dotrą do przyrost 5.
>

### <a name="log-management"></a>Zarządzanie dziennikiem
Możesz wybrać opcję Wyłącz audio śledzenia dla nowo wdrożonym modelu ponoszenia dodatkowych kosztów. Niestandardowej usługi rozpoznawania mowy nie rejestruje żądania audio i zapisy modelu.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji o sposobie korzystania z usługi rozpoznawania mowy niestandardowych, przejdź do [portalu niestandardowe mowy](https://cris.ai).

* [Wprowadzenie](cognitive-services-custom-speech-get-started.md)
* [Często zadawane pytania](cognitive-services-custom-speech-faq.md)
* [Słownik](cognitive-services-custom-speech-glossary.md)
 