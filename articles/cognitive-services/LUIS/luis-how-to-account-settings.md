---
title: Zarządzanie kontem i kluczami
titleSuffix: Language Understanding - Azure Cognitive Services
description: Dwa kluczowych informacji dla konta usługi LUIS są konta użytkowników i tworzenia klucza. Informacje o logowaniu odbywa się w witrynie account.microsoft.com. Klucz tworzenia odbywa się na stronie Ustawienia portalu usługi LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: d7d63ad642ab2d3b336e15dcca606077762ceb9d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60195834"
---
# <a name="manage-account-and-authoring-key"></a>Zarządzanie kontem i tworzenia klucza

Dwa kluczowych informacji dla konta usługi LUIS są konta użytkowników i tworzenia klucza. Informacje o logowaniu odbywa się na [witryny account.microsoft.com](https://account.microsoft.com). Tworzenia klucza jest zarządzany z [LUIS](luis-reference-regions.md) portal **ustawienia** strony.

## <a name="authoring-key"></a>Tworzenie klucza

Ten pojedynczy, specyficzne dla regionu tworzenia klucza, na **ustawienia** stronie pozwala na tworzenie wszystkich aplikacji z [LUIS](luis-reference-regions.md) portalu, a także [Tworzenie interfejsów API](https://aka.ms/luis-authoring-api). Dla wygody tworzenia klucza może dokonywać [ograniczone](luis-boundaries.md) numer punktu końcowego zapytania każdego miesiąca.

[![Strona Ustawienia usługi LUIS](./media/luis-how-to-account-settings/account-settings.png)](./media/luis-how-to-account-settings/account-settings.png#lightbox)

Tworzenia klucz jest używany dla wszystkich aplikacji, których jesteś właścicielem, jak również wszelkie aplikacje, które są wymienione jako współpracownika.

## <a name="authoring-key-regions"></a>Tworzenie kluczy regionów

Dotyczy tworzenia klucza [tworzenia regionu](luis-reference-regions.md#publishing-regions). Klucz nie działa w innym regionie.

## <a name="reset-authoring-key"></a>Resetuj klucz tworzenia pakietów administracyjnych

W przypadku naruszenia zabezpieczeń klucza usługi tworzenia zresetować klucza. Klucz jest resetowany na wszystkich Twoich aplikacji w [LUIS](luis-reference-regions.md) portalu. Przy tworzeniu aplikacji za pomocą tworzenia interfejsów API, należy zmienić wartość `Ocp-Apim-Subscription-Key` do nowego klucza.

## <a name="delete-account"></a>Usuń konto

Zobacz [magazyn danych i usuwania](luis-concept-data-storage.md#accounts) informacji o jakie dane są usuwane po usunięciu konta.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o swojej [tworzenia klucza](luis-concept-keys.md#authoring-key).

