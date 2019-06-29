---
title: Jak utworzyć zestaw danych szkoleniowych modelu niestandardowego — aparat rozpoznawania formularza
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak upewnić się, zestaw danych szkoleniowych zoptymalizowana pod kątem uczenia modelu rozpoznawania formularza.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: 611d5f7983c61fab12c55a46fedf35a3c420c4c8
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454819"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>Tworzenie szkoleniowy zestaw danych dla modelu niestandardowego

Korzystając z modelu niestandardowego formularza rozpoznawania możesz podać danych szkoleniowych, aby uczyć modelu formularzy specyficznych dla branży. Możesz uczyć model z pięciu wypełnione formularze lub pusty formularz (Dołącz słowo "puste" w nazwie pliku) oraz dwie formy wypełnione. Nawet jeśli masz za mało wypełnione formularze do jego trenowanie za pomocą, dodając pusty formularz do zestawu danych szkoleniowych zwiększa dokładność modelu.

## <a name="training-data-tips"></a>Porady dotyczące danych szkoleniowych

Należy użyć zestawu danych, który jest zoptymalizowany do trenowania. Użyj następujących wskazówek, aby zagwarantować, że uzyskać najlepsze wyniki z [Train Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/TrainCustomModel) operacji:

* Jeśli to możliwe zamiast oparte na tekście dokumentów PDF dokumentów na podstawie obrazu. Skanowanych dokumentów PDF są obsługiwane jako obrazy.
* Użyj jeden pusty formularz i dwie formy wypełniane, jeśli je, które są dostępne.
* W przypadku wypełnione formularze Użyj przykładów, które mają wszystkie pola wypełnione.
* Używanie formularzy z różnymi wartościami w każdym polu.
* W przypadku obrazów formularza niższej jakości, należy użyć większy zestaw danych (na przykład obrazy 10 – 15).

## <a name="general-input-requirements"></a>Ogólne wymagania wprowadzania

Upewnij się, że zestaw danych szkoleniowych następuje po danych wejściowych wymagania dla całej zawartości rozpoznawania formularza.
[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="next-steps"></a>Kolejne kroki

Teraz, wyjaśniono, jak można utworzyć zestaw danych szkoleniowych, postępuj zgodnie z przewodnika Szybki Start do trenowania modelu niestandardowego formularza rozpoznawania i rozpocząć korzystanie z niej na formularzach.

* [Szybki start: Uczenie modelu i wyodrębnić dane formularza za pomocą programu cURL](./quickstarts/curl-train-extract.md)
* [Szybki start: Uczenie modelu i wyodrębnianie danych formularza przy użyciu interfejsu API REST przy użyciu języka Python](./quickstarts/python-train-extract.md)

