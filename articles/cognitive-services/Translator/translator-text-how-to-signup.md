---
title: Wprowadzenie — interfejs API tekstu tłumacza
titleSuffix: Azure Cognitive Services
description: W tym artykule pokazano, jak zarejestrować się w interfejsie API tekstowym usługi Azure Cognitive Services Translator i uzyskać klucz subskrypcji.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.custom: seodec18
ms.openlocfilehash: cfae5318213e8af164c8d0cc9ed378705a5644b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73835706"
---
# <a name="how-to-sign-up-for-the-translator-text-api"></a>Jak zarejestrować się w interfejsie API tekstu tłumacza

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

- Nie masz konta? Możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free/), aby eksperymentować bez opłat.
- Masz już konto? [Zaloguj się](https://ms.portal.azure.com/)

## <a name="create-a-subscription-to-the-translator-text-api"></a>Tworzenie subskrypcji interfejsu API tekstu tłumacza

Po zalogowaniu się do portalu można utworzyć subskrypcję interfejsu API tekstu tłumacza w następujący sposób:

1. Wybierz pozycję **+ Utwórz zasób**.
1. W polu wyszukiwania **Wyszukaj w Portalu Marketplace** wprowadź pozycję **Tekst tłumacza,** a następnie wybierz go z wyników.
1. Wybierz **pozycję Utwórz,** aby zdefiniować szczegóły subskrypcji.
1. Z listy **Warstwa cenowa** wybierz warstwę cenową, która najlepiej odpowiada Twoim potrzebom.
    1. Każda subskrypcja ma warstwę bezpłatną. Warstwa bezpłatna ma takie same funkcje jak płatne plany i nie wygasa.
    1. Możesz mieć tylko jedną bezpłatną subskrypcję na swoje konto.
1. Wybierz **pozycję Utwórz,** aby zakończyć tworzenie subskrypcji.

## <a name="authentication-key"></a>Klucz uwierzytelniania

Gdy zarejestrujesz się w aplikacji Translator Text, otrzymasz spersonalizowany klucz dostępu unikatowy dla twojej subskrypcji. Ten klucz jest wymagany dla każdego wywołania interfejsu API tekstu translatora.

1. Pobierz klucz uwierzytelniania, najpierw wybierając odpowiednią subskrypcję.
1. Wybierz **pozycję Klucze** w sekcji **Zarządzanie zasobami** szczegółów subskrypcji.
1. Skopiuj jeden z kluczy wymienionych dla subskrypcji.

## <a name="learn-test-and-get-support"></a>Ucz się, testuj i uzyskaj pomoc techniczną

- [Przykłady kodu w usłudze GitHub](https://github.com/MicrosoftTranslator)
- [Forum pomocy technicznej usługi Microsoft Translator](https://www.aka.ms/TranslatorForum)

Usługa Microsoft Translator zazwyczaj umożliwia przekazywanie pierwszych kilku żądań, zanim zweryfikuje stan konta subskrypcji. Jeśli kilka pierwszych żądań interfejsu API usługi Microsoft Translator zakończy się pomyślnie, wywołania nie powiedzie się, odpowiedź na błąd wskaże problem. Zarejestruj odpowiedź interfejsu API, aby zobaczyć przyczynę.

## <a name="pricing-options"></a>Opcje cennika

- [Interfejs API tekstu usługi Translator](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)

## <a name="customization"></a>Dostosowywanie

Użyj niestandardowego tłumacza, aby dostosować tłumaczenia i utworzyć system tłumaczenia dostrojony do własnej terminologii i stylu, począwszy od ogólnych systemów tłumaczenia maszynowego neuronalnej usługi Microsoft Translator. [Dowiedz się więcej](customization.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Wprowadzenie do platformy Azure (3-minutowy film)](https://azure.microsoft.com/get-started/?b=16.24)
- [Jak zapłacić za pomocą faktury](https://azure.microsoft.com/pricing/invoicing/)
