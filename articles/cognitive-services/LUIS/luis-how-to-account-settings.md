---
title: Zarządzanie kontami i kluczami — LUIS
titleSuffix: Azure Cognitive Services
description: Dwa kluczowych informacji dla konta usługi LUIS są konta użytkowników i tworzenia klucza. Twoje informacje logowania są zarządzane pod adresem account.microsoft.com. Klucz tworzenia jest zarządzany na stronie ustawień portalu LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 28f8a15922a3a3e4e3b464b86bdfea07d329a848
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638269"
---
# <a name="manage-account-and-authoring-key"></a>Zarządzanie kontem i tworzenia klucza

Dwa kluczowych informacji dla konta usługi LUIS są konta użytkowników i tworzenia klucza. Informacje o logowaniu odbywa się na [witryny account.microsoft.com](https://account.microsoft.com). Klucz tworzenia jest zarządzany na stronie **ustawień** portalu [Luis](luis-reference-regions.md) .

## <a name="authoring-key"></a>Tworzenie klucza

Ten pojedynczy, specyficzny dla regionu klucz tworzenia, na stronie **Ustawienia** umożliwia tworzenie wszystkich aplikacji z portalu [Luis](luis-reference-regions.md) oraz [interfejsów API tworzenia](https://go.microsoft.com/fwlink/?linkid=2092087). Dla wygody tworzenia klucza może dokonywać [ograniczone](luis-boundaries.md) numer punktu końcowego zapytania każdego miesiąca.

[![Strona Ustawienia usługi LUIS](./media/luis-how-to-account-settings/account-settings.png)](./media/luis-how-to-account-settings/account-settings.png#lightbox)

Tworzenia klucz jest używany dla wszystkich aplikacji, których jesteś właścicielem, jak również wszelkie aplikacje, które są wymienione jako współpracownika.

## <a name="authoring-key-regions"></a>Tworzenie kluczy regionów

Dotyczy tworzenia klucza [tworzenia regionu](luis-reference-regions.md#publishing-regions). Klucz nie działa w innym regionie.

## <a name="reset-authoring-key"></a>Resetuj klucz tworzenia pakietów administracyjnych

W przypadku naruszenia zabezpieczeń klucza usługi tworzenia zresetować klucza. Klucz jest resetowany dla wszystkich aplikacji w portalu [Luis](luis-reference-regions.md) . Przy tworzeniu aplikacji za pomocą tworzenia interfejsów API, należy zmienić wartość `Ocp-Apim-Subscription-Key` do nowego klucza.

## <a name="delete-account"></a>Usuń konto

Zobacz [magazyn danych i usuwania](luis-concept-data-storage.md#accounts) informacji o jakie dane są usuwane po usunięciu konta.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o swojej [tworzenia klucza](luis-concept-keys.md#authoring-key).

