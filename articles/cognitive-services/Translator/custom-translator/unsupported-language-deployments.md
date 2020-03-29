---
title: Nieobsługiwały wdrożenia języka — translator niestandardowy
titleSuffix: Azure Cognitive Services
description: W tym artykule pokazano, jak wdrożyć nieobsługiwał par języków w usłudze Azure Cognitive Services Custom Translator.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/24/2019
ms.author: swmachan
ms.openlocfilehash: 3c5c74fc853b5a2425a58e1704aad43350cba212
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837452"
---
# <a name="unsupported-language-deployments"></a>Wdrożenia w nieobsługiwanych językach

<!--Custom Translator provides the highest-quality translations possible using the latest techniques in neural machine learning. While Microsoft intends to make neural training available in all languages, there are some limitations that prevent us from being able to offer neural machine translation in all language pairs.-->  

Wraz ze zbliżającym się wycofaniem usługi Microsoft Translator Hub firma Microsoft będzie cofać wszystkie modele obecnie wdrażane za pośrednictwem centrum. Wielu z was ma modele wdrożone w centrum, którego pary języków nie są obsługiwane w uzywacza usługi Custom Translator.  Nie chcemy, aby użytkownicy w tej sytuacji nie mieli możliwości tłumaczenia swoich treści.

Mamy teraz proces, który pozwala na wdrożenie nieobsługiwałych modeli za pośrednictwem usługi Custom Translator.  Ten proces umożliwia kontynuowanie tłumaczenia zawartości przy użyciu najnowszego interfejsu API v3.  Te modele będą hostowane, dopóki nie zdecydujesz się ich cofnąć lub para języków stanie się dostępna w udzielono usługi Custom Translator.  W tym artykule opisano proces wdrażania modeli z nieobsługiwał parami języków.

## <a name="prerequisites"></a>Wymagania wstępne

Aby modele były kandydatami do wdrożenia, muszą spełniać następujące kryteria:
* Projekt zawierający model musi zostać zmigrowany z Centrum do translatora niestandardowego za pomocą narzędzia migracji.  Proces migracji projektów i obszarów roboczych można znaleźć [tutaj](how-to-migrate.md).
* Model musi być w stanie wdrożonym, gdy migracja się dzieje.  
* Para języków modelu musi być nieobsługiconą parą języków w translatorze niestandardowym.  Pary języków, w których język jest obsługiwany do lub z języka angielskiego, ale sama para nie zawiera języka angielskiego, są kandydatami do nieobsługiwał wdrożeń języka.  Na przykład model Centrum dla pary języka francuskiego i niemieckiego jest uważany za nieobsługiconą parę języków, mimo że pary języków francuskich na angielski i angielski na niemiecki są obsługiwaną parą języków.

## <a name="process"></a>Proces
Po migracji modeli z centrum, które są kandydatami do wdrożenia, można je znaleźć, przechodząc do **ustawienia** strony dla obszaru roboczego i przewijanie do końca strony, gdzie zostanie wyświetlony **nieobsługicony Translator Hub szkolenia** sekcji.  Ta sekcja jest wyświetlana tylko wtedy, gdy masz projekty spełniające wymagania wstępne wymienione powyżej.

![Jak przeprowadzić migrację z centrum](media/unsupported-language-deployments/unsupported-translator-hub-trainings.jpg)

Na stronie wyboru **Nieobsługiwalone szkolenia usługi Translator Hub** karta Szkolenia **nienachalne** zawiera modele, które kwalifikują się do wdrożenia.  Wybierz modele, które chcesz wdrożyć i prześlij żądanie.   Przed upływem terminu wdrożenia 30 kwietnia można wybrać dowolną liczbę modeli do wdrożenia.
 
![Jak przeprowadzić migrację z centrum](media/unsupported-language-deployments/unsupported-translator-hub-trainings-list.jpg)

Po przesłaniu model nie będzie już dostępny na karcie **Szkolenia nieosektowane** i zamiast tego pojawi się na karcie **Żądane szkolenia.**  Wymagane szkolenia można obejrzeć w dowolnym momencie.

![Jak przeprowadzić migrację z centrum](media/unsupported-language-deployments/request-unsupported-trainings.jpg) 

## <a name="whats-next"></a>Co dalej?

Modele wybrane do wdrożenia są zapisywane po wycofaniu koncentratora i cofnięciu wszystkich modeli.  Masz czas do 24 maja, aby przesłać żądania wdrożenia nieobsługiwałych modeli.  Wdrożymy te modele 15 czerwca, w którym to momencie będą one dostępne za pośrednictwem interfejsu API translatora v3.  Ponadto będą one dostępne za pośrednictwem interfejsu API V2 do 1 lipca.  

Aby uzyskać więcej informacji na temat ważnych dat w deprecation centrum sprawdzić [tutaj](https://www.microsoft.com/translator/business/hub/).
Po wdrożeniu będą obowiązywać normalne opłaty hostingowe.  Zobacz [ceny, aby](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) uzyskać szczegółowe informacje.  

W przeciwieństwie do standardowych modeli usługi Custom Translator modele centrum będą dostępne tylko w jednym regionie, więc opłaty hostingowe dla wielu regionów nie będą miały zastosowania.  Po wdrożeniu będzie można cofnąć wdrożenie modelu centrum w dowolnym momencie za pośrednictwem zmigrowanego projektu usługi Custom Translator.

## <a name="next-steps"></a>Następne kroki

- [Trenuj model](how-to-train-model.md).
- Zacznij korzystać z wdrożonego niestandardowego modelu tłumaczenia za pośrednictwem [interfejsu API tekstu usługi Microsoft Translator w wersji 3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
