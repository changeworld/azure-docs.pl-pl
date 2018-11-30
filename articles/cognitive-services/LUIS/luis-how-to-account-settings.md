---
title: Zarządzanie ustawieniami konta w LUIS | Dokumentacja firmy Microsoft
description: Aby zarządzać ustawieniami konta, należy użyć witryny sieci Web usługi LUIS.
titleSuffix: Azure
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: bb41331228e700c55da21c627d617d16faa2dcb9
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/27/2018
ms.locfileid: "52335404"
---
# <a name="manage-account-and-authoring-key"></a>Zarządzanie kontem i tworzenia klucza
Dwa kluczowych informacji dla konta usługi LUIS są konta użytkowników i tworzenia klucza. Informacje o logowaniu odbywa się na [witryny account.microsoft.com](https://account.microsoft.com). Tworzenia klucza jest zarządzany z [LUIS](luis-reference-regions.md) witryny sieci Web **ustawienia** strony. 

## <a name="authoring-key"></a>Tworzenie klucza

Ten pojedynczy, specyficzne dla regionu tworzenia klucza, na **ustawienia** stronie pozwala na tworzenie wszystkich aplikacji z [LUIS](luis-reference-regions.md) witryny sieci Web, jak również [Tworzenie interfejsów API](https://aka.ms/luis-authoring-api). Dla wygody tworzenia klucza może dokonywać [ograniczone](luis-boundaries.md) numer punktu końcowego zapytania każdego miesiąca. 

[![Strona Ustawienia usługi LUIS](./media/luis-how-to-account-settings/account-settings.png)](./media/luis-how-to-account-settings/account-settings.png#lightbox)

Tworzenia klucz jest używany dla wszystkich aplikacji, których jesteś właścicielem, jak również wszelkie aplikacje, które są wymienione jako współpracownika.

## <a name="authoring-key-regions"></a>Tworzenie kluczy regionów
Dotyczy tworzenia klucza [tworzenia regionu](luis-reference-regions.md#publishing-regions). Klucz nie działa w innym regionie. 

## <a name="reset-authoring-key"></a>Resetuj klucz tworzenia pakietów administracyjnych
W przypadku naruszenia zabezpieczeń klucza usługi tworzenia zresetować klucza. Klucz jest resetowany na wszystkich Twoich aplikacji w [LUIS](luis-reference-regions.md) witryny sieci Web. Przy tworzeniu aplikacji za pomocą tworzenia interfejsów API, należy zmienić wartość `Ocp-Apim-Subscription-Key` do nowego klucza. 

## <a name="delete-account"></a>Usuń konto
Zobacz [magazyn danych i usuwania](luis-concept-data-storage.md#accounts) informacji o jakie dane są usuwane po usunięciu konta. 

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o swojej [tworzenia klucza](luis-concept-keys.md#authoring-key). 

