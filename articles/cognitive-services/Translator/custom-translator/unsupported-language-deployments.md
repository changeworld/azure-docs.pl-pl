---
title: Nieobsługiwany język wdrożenia — niestandardowe w usłudze Translator
titleSuffix: Azure Cognitive Services
description: Jak wdrożyć pary nieobsługiwany język w niestandardowych w usłudze Translator.
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/24/2019
ms.author: v-pawal
ms.openlocfilehash: 09fbd771d945646fe385508779d38e4abb2ee293
ms.sourcegitcommit: daf6538427ea6effef898f2ee3d857e5fa2dccbc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/25/2019
ms.locfileid: "64476508"
---
# <a name="unsupported-language-deployments"></a>Nieobsługiwany język wdrożenia

<!--Custom Translator provides the highest-quality translations possible using the latest techniques in neural machine learning. While Microsoft intends to make neural training available in all languages, there are some limitations that prevent us from being able to offer neural machine translation in all language pairs.-->  

O zbliżającym się wycofaniu usługi Centrum w usłudze Translator firmy Microsoft Microsoft będzie można wszystkie modele, które aktualnie wdrożonych za pośrednictwem Centrum. Wiele osób ma modele wdrożonych w Centrum pary język, którego nie są obsługiwane w niestandardowych w usłudze Translator.  Nie chcemy, użytkownicy w tej sytuacji nie odwoływał się do tłumaczenia ich zawartości.

W efekcie powstał procesu, który umożliwia wdrażanie modeli nieobsługiwane za pomocą niestandardowych w usłudze Translator.  Ten proces umożliwia tłumaczenie zawartości przy użyciu najnowszej wersji 3 interfejsu API w dalszym ciągu.  Te modele będzie obsługiwana, dopóki nie zdecydujesz się wdrożyć je lub pary języka stają się dostępne w niestandardowych w usłudze Translator.  W tym artykule opisano proces wdrażania modeli z parami nieobsługiwany język.

## <a name="prerequisites"></a>Wymagania wstępne

Aby swoje modele, aby być kandydatami do wdrożenia muszą spełniać następujące kryteria:
* Projekt zawierający model musi zostały zmigrowane z koncentratora do Translator niestandardowego przy użyciu narzędzia migracji.  Proces migracji projektów i obszarów roboczych można znaleźć [tutaj](how-to-migrate.md).
* Model musi być w stanie wdrożonym w przypadku migracji.  
* Para języka modelu musi być parę nieobsługiwany język w niestandardowych w usłudze Translator.  Kierunki, w których język jest obsługiwana na lub z języka angielskiego, ale pary, sama nie ma języka angielskiego nadają się do wdrożeń nieobsługiwany język.  Na przykład model koncentratora, francuski parą języka niemieckiego jest uznawany za nieobsługiwany język pary nawet mimo że francuska, angielskiej i angielskiego na niemiecki są obsługiwanego języka parę.

## <a name="process"></a>Przetwórz
Po dokonaniu migracji modeli z koncentratora, które są kandydatami do wdrożenia, można je znaleźć, przechodząc do **ustawienia** strony dla obszaru roboczego i przewijając do końca strony, gdzie zostaną wyświetlone **nieobsługiwany W usłudze Translator Centrum szkolenia** sekcji.  W tej sekcji jest wyświetlany tylko w przypadku projektów, które spełniają wymagania wstępne wymienione powyżej.

![Jak przeprowadzić migrację z Centrum](media/unsupported-language-deployments/unsupported-translator-hub-trainings.jpg)

W ramach **nieobsługiwany w usłudze Translator Centrum szkolenia** strony wyboru **niewymagany szkoleniach** karta zawiera modeli, które kwalifikują się do wdrożenia.  Wybierz modele, które chcesz wdrożyć i prześlij żądanie.   Przed 30 kwietnia ostatecznego terminu wdrożenia możesz wybrać dowolną liczbę modeli zgodnie z wymaganiami dla wdrożenia.
 
![Jak przeprowadzić migrację z Centrum](media/unsupported-language-deployments/unsupported-translator-hub-trainings-list.jpg)

Po przesłaniu, model nie będzie już dostępny na **niewymagany szkoleniach** kartę i zamiast tego będzie wyświetlana na **żądane szkoleniach** kartę.  Możesz wyświetlić swoje żądanego szkoleniach w dowolnym momencie.

![Jak przeprowadzić migrację z Centrum](media/unsupported-language-deployments/request-unsupported-trainings.jpg) 

## <a name="whats-next"></a>Co dalej?

Modele, które zostały wybrane do wdrożenia są zapisywane po zlikwidowaniu koncentratora i wszystkie modele są wycofane.  Masz 24 maja do przesyłania żądań dla wdrożenia modeli nieobsługiwany.  Wdrożymy tych modeli od 15 czerwca, po czym będą one dostępne za pośrednictwem interfejsu API usługi Translator w wersji 3.  Ponadto będą one dostępne za pośrednictwem interfejsu API w wersji 2 do 1 lipca.  

Więcej informacji na temat ważnych dat w amortyzacja wyboru centrum [tutaj](https://www.microsoft.com/translator/business/hub/).
Raz wdrożonej, normalny hostingu naliczone zostaną opłaty.  Zobacz [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) Aby uzyskać szczegółowe informacje.  

W przeciwieństwie do standardowych modele niestandardowe w usłudze Translator modele Centrum tylko będą dostępne w jednym regionie, dzięki czemu nie będą stosowane opłaty hostingu wielu regionów.  Po wdrożeniu będzie można wdrożyć modelu Centrum w dowolnym momencie za pośrednictwem migrowanych projektu niestandardowe w usłudze Translator.

## <a name="next-steps"></a>Kolejne kroki

- [Uczenie modelu](how-to-train-model.md).
- Rozpoczynanie korzystania z modelu tłumaczenia niestandardowych wdrożone za pośrednictwem [V3 interfejs API tekstu usługi Translator firmy Microsoft](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
