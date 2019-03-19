---
title: Znane problemy z wtyczką Akustyka projektu
titlesuffix: Azure Cognitive Services
description: W przypadku używania projektanta w wersji zapoznawczej dla Akustyka projektu, mogą wystąpić następujące znane problemy.
services: cognitive-services
author: kylestorck
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: resources
ms.date: 08/17/2018
ms.author: kylestorck
ms.openlocfilehash: 4cbede768409596365bea0cdbbb451cc5195ac4b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2019
ms.locfileid: "58136263"
---
# <a name="project-acoustics-known-issues"></a>Projekt Akustyka znane problemy
W przypadku używania projektanta w wersji zapoznawczej dla Akustyka projektu, mogą wystąpić następujące znane problemy.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Parametrów akustycznych zostaną utracone po zmianie nazwy sceny

Jeśli zmienisz scenę, wszystko, czego akustyczny parametry, które należą do tej sceny nie będzie automatycznie przenieść do sceny nowy. Będzie nadal istnieją one w starym pliku zasobów jednak. Wyszukaj **SceneName_AcousticParameters.asset** pliku wewnątrz **edytora** katalogu obok pliku sceny. Zmień nazwę pliku, aby odzwierciedlały nową nazwę sceny.

## <a name="unity-crashes-when-closing-project"></a>Unity ulega awarii podczas zamykania projektu

Na najnowsze wersje aparatu Unity (2018.2 +) jest to znana usterka, w którym Unity ulegnie awarii po zamknięciu projektu. To jest śledzona przez [ten problem Unity](https://issuetracker.unity3d.com/issues/crash-on-assetdatabase-getassetimporterversions-when-closing-a-specific-unity-project).

## <a name="trouble-deploying-to-android"></a>Problemy z wdrożenia w systemie Android
Aby użyć Akustyka projektu w systemie Android, zmień urządzenie docelowe kompilacji systemu Android. Niektóre wersje programu Unity mają usterkę z wdrażaniem audio wtyczek. Upewnij się, że nie używasz wersji dotyczy [tej usterki](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player).

## <a name="i-get-an-error-that-could-not-find-metadata-file-systemsecuritydll"></a>Otrzymuję komunikat o błędzie tego "nie można znaleźć pliku metadanych System.Security.dll"

Upewnij się, jest ustawiona wersja środowiska uruchomieniowego skryptów w ustawieniach Player **.NET 4.x odpowiednik**i ponownie uruchomić aparatu Unity.

## <a name="im-having-authentication-problems-when-connecting-to-azure"></a>Występują problemy z uwierzytelniania podczas nawiązywania połączenia z platformą Azure

Dokładnie sprawdź wykorzystano poprawne poświadczenia dla konta platformy Azure, że Twoje konto obsługuje typ węzła w tworzenie i czy zegar systemowy jest prawidłowa.

## <a name="canceling-a-bake-leaves-the-bake-tab-in-deleting-state"></a>Trwa anulowanie tworzenie pozostawia kartę tworzenie w stanie "usuwania"
Akustyka projektu spowoduje oczyszczenie wszystkich zasobów platformy Azure dla zadania o pomyślnym zakończeniu lub anulowaniu. Może to potrwać do 5 minut.

## <a name="next-steps"></a>Kolejne kroki
* Spróbuj [Unity](unity-quickstart.md) lub [Unreal](unreal-quickstart.md) zawartości

