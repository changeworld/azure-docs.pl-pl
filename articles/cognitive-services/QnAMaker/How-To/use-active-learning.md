---
title: Poprawa bazy wiedzy - QnA Maker
description: Popraw jakość swojej bazy wiedzy dzięki aktywnemu uczeniu się. Przeglądaj, akceptuj lub odrzucaj, dodawaj bez usuwania lub zmieniania istniejących pytań.
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 33b3c547b0aea9a1e235bf8a05d01aa16b468a71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80071134"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>Ulepszanie bazy wiedzy za pomocą uczenia aktywnego

[Aktywne uczenie się](../Concepts/active-learning-suggestions.md) pozwala poprawić jakość bazy wiedzy, proponując alternatywne pytania. Zgłoszenia użytkowników są brane pod uwagę i wyświetlane jako sugestie na liście pytań alternatywnych. Masz możliwość dodania tych sugestii jako alternatywnych pytań lub odrzucenia ich.

Baza wiedzy nie zmienia się automatycznie. Aby zmiany weszły w życie, musisz zaakceptować sugestie. Te sugestie dodają pytania, ale nie zmieniają ani nie usuwają istniejących pytań.


## <a name="upgrade-runtime-version-to-use-active-learning"></a>Uaktualnianie wersji środowiska uruchomieniowego w celu użycia aktywnego uczenia się

Usługa Active Learning jest obsługiwana w wersji wykonawczej 4.4.0 lub wyższej. Jeśli baza wiedzy została utworzona na wcześniejszej wersji, [uaktualnij środowisko wykonawcze,](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates) aby użyć tej funkcji.

## <a name="turn-on-active-learning-for-alternate-questions"></a>Włączanie aktywnej nauki w przypadku pytań alternatywnych

Aktywne uczenie jest domyślnie wyłączone. Włącz go, aby wyświetlić sugerowane pytania. Po włączeniu aktywnego uczenia się należy wysłać informacje z aplikacji klienckiej do programu QnA Maker. Aby uzyskać więcej informacji, zobacz [Przepływ architektury do korzystania z generateanswer i pociągu interfejsów API z bota](improve-knowledge-base.md#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot).

1. Wybierz **pozycję Publikuj,** aby opublikować bazę wiedzy. Aktywne zapytania szkoleniowe są zbierane tylko z punktu końcowego prognozowania interfejsu API GenerateAnswer. Zapytania do okienka testowego w portalu QnA Maker nie mają wpływu na aktywne uczenie się.

1. Aby włączyć aktywne uczenie się w portalu QnA Maker, przejdź do prawego górnego rogu, wybierz **nazwę**, przejdź do [**ustawień usługi**](https://www.qnamaker.ai/UserSettings).

    ![Włącz zalecane przez aktywne uczenie się alternatywy pytań na stronie Ustawienia usługi. Wybierz nazwę użytkownika w prawym górnym menu, a następnie wybierz pozycję Ustawienia usługi.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Znajdź usługę QnA Maker, a następnie przełącz **usługę Active Learning**.

    > [!div class="mx-imgBorder"]
    > [![Na stronie Ustawienia usługi przełącz funkcję Aktywne uczenie. Jeśli nie możesz przełączyć tej funkcji, może być konieczne uaktualnienie usługi.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    > [!Note]
    > Dokładna wersja na poprzednim obrazie jest wyświetlana tylko jako przykład. Twoja wersja może być inna.

    Po włączeniu **funkcji Active Learning** baza wiedzy sugeruje nowe pytania w regularnych odstępach czasu na podstawie pytań przesłanych przez użytkownika. **Active Learning** można wyłączyć, ponownie przełączając to ustawienie.

## <a name="review-suggested-alternate-questions"></a>Przejrzyj sugerowane pytania alternatywne

[Przejrzyj alternatywne sugerowane pytania](improve-knowledge-base.md) na stronie **Edycja** każdej bazy wiedzy.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie bazy wiedzy](./manage-knowledge-bases.md)