---
title: Wdrażanie modelu na potrzeby usługi Custom Speech-Speech
titleSuffix: Azure Cognitive Services
description: W tym dokumencie dowiesz się, jak utworzyć i wdrożyć punkt końcowy przy użyciu portalu Custom Speech.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: fcd3e5fd6db0d7c18a5f9bfba030cd3d809b452f
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802525"
---
# <a name="deploy-a-custom-model"></a>Wdrażanie modelu niestandardowego

Po przekazaniu i sprawdzeniu danych, ocenie dokładności i przeszkoleniu modelu niestandardowego można wdrożyć niestandardowy punkt końcowy do użycia z aplikacjami, narzędziami i produktami. W tym dokumencie dowiesz się, jak utworzyć i wdrożyć punkt końcowy przy użyciu [portalu Custom Speech](https://speech.microsoft.com/customspeech).

## <a name="create-a-custom-endpoint"></a>Tworzenie niestandardowego punktu końcowego

Aby utworzyć nowy niestandardowy punkt końcowy, zaloguj się do [portalu Custom Speech](https://speech.microsoft.com/customspeech) i wybierz pozycję **wdrożenie** z menu Custom Speech w górnej części strony. Jeśli jest to pierwsze uruchomienie, Zauważ, że w tabeli nie ma punktów końcowych. Po utworzeniu punktu końcowego użyjesz tej strony do śledzenia wszystkich wdrożonych punktów końcowych.

Następnie wybierz pozycję **Dodaj punkt końcowy** i wprowadź **nazwę** i **Opis** niestandardowego punktu końcowego. Następnie wybierz model niestandardowy, który chcesz skojarzyć z tym punktem końcowym. Na tej stronie można również włączyć rejestrowanie. Rejestrowanie pozwala monitorować ruch punktu końcowego. W przypadku wyłączenia ruch nie będzie przechowywany.

![Jak wdrożyć model](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Nie zapomnij zaakceptować warunków użytkowania i szczegółów cennika.

Następnie wybierz pozycję **Utwórz**. Ta akcja powoduje powrót do strony **wdrożenia** . Tabela zawiera teraz wpis odpowiadający niestandardowemu punktowi końcowemu. Stan punktu końcowego pokazuje jego bieżący stan. Utworzenie wystąpienia nowego punktu końcowego przy użyciu modeli niestandardowych może potrwać do 30 minut. Gdy stan wdrożenia zmieni się na **ukończone**, punkt końcowy jest gotowy do użycia.

Po wdrożeniu punktu końcowego nazwa punktu końcowego jest wyświetlana jako link. Kliknij link, aby wyświetlić informacje specyficzne dla danego punktu końcowego, takie jak klucz punktu końcowego, adres URL punktu końcowego i przykładowy kod.

## <a name="view-logging-data"></a>Wyświetlanie danych rejestrowania

Dane rejestrowania są dostępne do pobrania w obszarze **punkt końcowy > szczegóły**.

## <a name="next-steps"></a>Następne kroki

* Używanie niestandardowego punktu końcowego z [zestawem SDK mowy](speech-sdk.md)

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Przygotowywanie i testowanie danych](how-to-custom-speech-test-data.md)
* [Inspekcja danych](how-to-custom-speech-inspect-data.md)
* [Oceń dane](how-to-custom-speech-evaluate-data.md)
* [Uczenie modelu](how-to-custom-speech-train-model.md)
* [Wdrażanie modelu](how-to-custom-speech-deploy-model.md)
