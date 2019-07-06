---
title: Wdrażanie modelu dla niestandardowego głosu — usługi mowy
titlesuffix: Azure Cognitive Services
description: W tym dokumencie dowiesz się, jak utworzyć i wdrożyć punkt końcowy korzystający z portalu usługi Custom Speech.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: f2b69bf3e5d536e7e813ef4a7c36b2937141fd43
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606623"
---
# <a name="deploy-a-custom-model"></a>Wdrażanie modelu niestandardowego

Po został przekazany i kontroli danych, oceniane dokładności i przeszkolonego modelu niestandardowego, można wdrożyć niestandardowy punkt końcowy do korzystania z aplikacji, narzędzi i produktów. W tym dokumencie dowiesz się, jak utworzyć i wdrożyć punkt końcowy korzystający z portalu usługi Custom Speech.

## <a name="create-a-custom-endpoint"></a>Tworzenie niestandardowego punktu końcowego

Aby utworzyć nowy niestandardowy punkt końcowy, wybierz **wdrożenia** menu Custom Speech, w górnej części strony. Po pierwszym uruchomieniu, można zauważyć, że nie istnieją żadne punkty końcowe, wymienione w tabeli. Po utworzeniu punktu końcowego, ta strona będzie służy do śledzenia każdego wdrożonego punktu końcowego.

Następnie wybierz pozycję **Dodaj punkt końcowy** i wprowadź **nazwa** i **opis** do niestandardowego punktu końcowego. Następnie wybrać niestandardowy model, który chcesz skojarzyć z tym punktem końcowym. Na tej stronie można również włączyć rejestrowanie. Rejestrowanie umożliwia monitorowanie punktu końcowego ruchu. Wyłączenie tej opcji, ruch nie zostanie on zapisany.

![Wdrażanie modelu](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Należy pamiętać zaakceptować warunki użytkowania i szczegóły cennika.

Następnie wybierz pozycję **Utwórz**. Spowoduje powrót do **wdrożenia** strony. Tabela zawiera teraz wpis, który odnosi się do niestandardowego punktu końcowego. Stan punktu końcowego to swojego bieżącego stanu. Może potrwać do 30 minut utworzyć nowy punkt końcowy przy użyciu niestandardowych modeli. Zmiany stanu wdrożenia na **Complete**, punkt końcowy jest gotowy do użycia.

Po wdrożeniu punkt końcowy, nazwę punktu końcowego, który pojawia się jako link. Kliknij link, aby wyświetlić informacje dotyczące punktu końcowego usługi, takich jak klucza punktu końcowego adresu URL punktu końcowego i przykładowy kod.

## <a name="view-logging-data"></a>Wyświetlanie danych rejestrowania

Dane rejestrowania są dostępne do pobrania w sekcji **punktu końcowego > Szczegóły**.

## <a name="next-steps"></a>Kolejne kroki

* Użyj niestandardowego punktu końcowego za pomocą [zestaw SDK rozpoznawania mowy](speech-sdk.md)

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Przygotuj i przetestować danych](how-to-custom-speech-test-data.md)
* [Sprawdzanie danych](how-to-custom-speech-inspect-data.md)
* [Oceń swoje dane](how-to-custom-speech-evaluate-data.md)
* [Uczenie modelu](how-to-custom-speech-train-model.md)
* [Wdrażanie modelu](how-to-custom-speech-deploy-model.md)
