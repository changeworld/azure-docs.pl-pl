---
title: Znane problemy z wtyczką Akustyka — Cognitive Services
description: W przypadku używania projektanta w wersji zapoznawczej dla Akustyka projektu, mogą wystąpić następujące znane problemy.
services: cognitive-services
author: kylestorck
manager: noelc
ms.service: cognitive-services
ms.component: acoustics
ms.topic: article
ms.date: 08/17/2018
ms.author: kylestorck
ms.openlocfilehash: c19b19cab33ae868f11ded0b7ce87dac99269596
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/28/2018
ms.locfileid: "47431995"
---
# <a name="known-issues"></a>Znane problemy
W przypadku używania projektanta w wersji zapoznawczej dla Akustyka projektu, mogą wystąpić następujące znane problemy.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Parametrów akustycznych zostaną utracone po zmianie nazwy sceny

Jeśli zmienisz scenę, wszystkich parametrów akustycznych należących do danej scenie nie zostaną automatycznie przeniesione do nową scenę. One będą nadal istnieć w starym pliku elementu zawartości jednak. Wyszukaj **SceneName_AcousticParameters.asset** pliku wewnątrz **edytora** katalogu obok pliku sceny. Zmień nazwę pliku, aby odzwierciedlały nową nazwę sceny.

## <a name="the-default-path-for-the-acousticsdata-folder-in-probes-tab-is-an-absolute-path"></a>Domyślna ścieżka dla folderu AcousticsData na karcie sondy jest ścieżką bezwzględną

Powinno to domyślne ścieżki względnej, aby ułatwić udostępnianie projektów między współpracowników. Jako obejście tego problemu należy zmienić ścieżkę względem katalogu projektu.

## <a name="runtime-voxels-are-a-different-size-than-scene-preview-voxels"></a>Środowisko uruchomieniowe voxels są inny rozmiar niż voxels Podgląd sceny

Jeśli to zrobisz **Calculate** na **sondy** kartę i widok voxels, a następnie wykonaj tworzenie i wyświetlanie voxels w czasie wykonywania dla tego samego sceny, voxels mają różne rozmiary. Voxels pokazano tworzenie wstępne są voxels używane w symulacji. Voxels wyświetlane w czasie wykonywania są używane do interpolacji między punktami sondowania. Może to spowodować niespójność, gdzie portali pojawią się otwarty w czasie wykonywania, które nie są faktycznie otwarte.

## <a name="uwp-builds-not-working"></a>Platformy uniwersalnej systemu Windows opiera się nie działa

W najnowszych wersjach aparatu Unity (2018.2 +) nie powiodły się kompilacji platformy uniwersalnej systemu Windows. Spowoduje zatrzymania wykonywania fazy kompilacji i uzyskasz "Unity rozszerzenia nie jest jeszcze inicjowany" błędy. To jest śledzona przez [ten problem Unity](https://fogbugz.unity3d.com/default.asp?1070491_1rgf14bakv5u779d).

## <a name="unity-crashes-when-closing-project"></a>Unity ulega awarii podczas zamykania projektu

Na najnowsze wersje aparatu Unity (2018.2 +) jest to znana usterka, w którym Unity ulegnie awarii po zamknięciu projektu. To jest śledzona przez [ten problem Unity](https://issuetracker.unity3d.com/issues/crash-on-assetdatabase-getassetimporterversions-when-closing-a-specific-unity-project).

## <a name="trouble-deploying-to-android"></a>Problemy z wdrożenia w systemie Android
Aby użyć Akustyka projektu w systemie Android, zmień urządzenie docelowe kompilacji systemu Android. Niektóre wersje programu Unity mieć usterkę z wdrażaniem audio wtyczek — upewnij się, że nie używasz wersji dotyczy [tej usterki](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player).

## <a name="i-get-an-error-that-could-not-find-metadata-file-systemsecuritydll"></a>Otrzymuję komunikat o błędzie tego "nie można znaleźć pliku metadanych System.Security.dll"

Upewnij się, jest ustawiona wersja środowiska uruchomieniowego skryptów w ustawieniach Player **.NET 4.x odpowiednik**i ponownie uruchomić aparatu Unity.

## <a name="im-having-authentication-problems-when-connecting-to-azure"></a>Występują problemy z uwierzytelniania podczas nawiązywania połączenia z platformą Azure

Dokładnie sprawdź wykorzystano poprawne poświadczenia dla konta platformy Azure, że Twoje konto obsługuje typ węzła w tworzenie i czy zegar systemowy jest prawidłowa.

## <a name="next-steps"></a>Kolejne kroki
* Rozpocznij [wprowadzanie akustyki do projektu Unity](getting-started.md)

