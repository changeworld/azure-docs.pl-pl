---
title: Znane problemy związane z wtyczką Project Acoustics
titlesuffix: Azure Cognitive Services
description: W programie Project Acoustics mogą wystąpić następujące znane problemy.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: b71b93f271608d946d964f70dae9eefbef77e87b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "72243038"
---
# <a name="project-acoustics-known-issues"></a>Znane problemy dotyczące akustyki projektu
W tym artykule opisano problemy, które mogą wystąpić podczas korzystania z akustyki programu Project.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Parametry akustyczne są tracone po zmianie nazwy sceny

Zmiana nazwy sceny powoduje, że wszystkie parametry akustyczne należące do tej sceny nie zostaną automatycznie przeniesione do nowej sceny. Ale nadal istnieją w starym pliku zasobów. Poszukaj pliku *[SceneName]_AcousticParameters.asset* w katalogu *Edytor obok* pliku sceny. Zmień nazwę pliku, aby odzwierciedlić nową nazwę sceny.

## <a name="deploy-to-android-bug-from-some-unity-versions"></a>Błąd wdrażania na Androida z niektórych wersji Unity

Niektóre wersje Unity mają [błąd](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player) w sposobie wdrażania wtyczek audio w systemie Android. Upewnij się, że nie używasz wersji, na którą ma wpływ ten błąd.

## <a name="could-not-find-metadata-file-systemsecuritydll-error"></a>Błąd "Nie można odnaleźć pliku metadanych System.Security.dll"

Upewnij się, że **wersja środowiska uruchomieniowego skryptów** w **ustawieniach odtwarzacza** jest *odpowiednikiem .NET 4.x*i uruchom ponownie unity.

## <a name="authentication-problems-when-connecting-to-azure"></a>Problemy z uwierzytelnianiem podczas łączenia się z platformą Azure

Sprawdź, czy:
- Użyto poprawne poświadczenia dla konta platformy Azure.
- Twoje konto obsługuje typ węzła, o który prosiłeś w piecu.
- Zegar systemowy jest ustawiony poprawnie.

## <a name="the-bake-tab-still-shows-deleting-after-you-cancel"></a>Karta Piec nadal pokazuje "usuwanie" po anulowaniu
Projekt Akustyka czyści wszystkie zasoby platformy Azure dla zadania po pomyślnym zakończeniu lub anulowaniu. Ten proces może potrwać do 5 minut.

## <a name="next-steps"></a>Następne kroki
* Wypróbuj [przykładową](unity-quickstart.md) zawartość Unity lub [Unreal.](unreal-quickstart.md)
