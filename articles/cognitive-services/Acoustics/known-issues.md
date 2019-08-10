---
title: Znane problemy z wtyczką akustyczną projektu
titlesuffix: Azure Cognitive Services
description: W przypadku korzystania z wersji zapoznawczej projektanta w celu wystawiania problemów z projektem mogą wystąpić następujące znane problemy.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 37084480423de90f50beced187eda202b39f8bf1
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933060"
---
# <a name="project-acoustics-known-issues"></a>Znane problemy akustyczne projektu
W przypadku korzystania z wersji zapoznawczej projektanta w celu wystawiania problemów z projektem mogą wystąpić następujące znane problemy.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Parametry akustyczne są tracone po zmianie nazwy sceny

Jeśli zmienisz nazwę sceny, wszystkie parametry akustyczne należące do tej sceny nie będą automatycznie przenoszone do nowej sceny. Jednak nadal istnieją w starym pliku zasobu. Znajdź plik **SceneName_AcousticParameters. Asset** w katalogu **edytora** obok pliku sceny. Zmień nazwę pliku, aby odzwierciedlał nazwę nowej sceny.

## <a name="deploying-to-android-from-some-unity-versions"></a>Wdrażanie w systemie Android z niektórych wersji aparatu Unity

Niektóre wersje aparatu Unity mają usterkę z wdrażaniem wtyczek audio w systemie Android. Upewnij się, że nie używasz wersji, której [](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player)dotyczy problem.

## <a name="i-get-an-error-that-could-not-find-metadata-file-systemsecuritydll"></a>Otrzymuję komunikat o błędzie "nie można znaleźć pliku metadanych system. Security. dll"

Upewnij się, że wersja środowiska uruchomieniowego tworzenia skryptów w ustawieniach odtwarzacza jest ustawiona na **.NET 4. x odpowiednik**i ponownie uruchom środowisko Unity.

## <a name="im-having-authentication-problems-when-connecting-to-azure"></a>Mam problemy z uwierzytelnianiem podczas nawiązywania połączenia z platformą Azure

Sprawdź, czy użyto poprawnych poświadczeń dla Twojego konta platformy Azure, czy Twoje konto obsługuje typ węzła żądany w tworzenie i czy zegar systemowy jest prawidłowy.

## <a name="canceling-a-bake-leaves-the-bake-tab-in-deleting-state"></a>Anulowanie tworzenie pozostawia kartę tworzenie w stanie "Usuwanie"
Akustyczne projektu spowoduje oczyszczenie wszystkich zasobów platformy Azure dla zadania po pomyślnym zakończeniu lub anulowaniu. Może to potrwać do 5 minut.

## <a name="next-steps"></a>Następne kroki
* Wypróbuj zawartość przykładową [aparatu Unity](unity-quickstart.md) lub [Unreal](unreal-quickstart.md)

