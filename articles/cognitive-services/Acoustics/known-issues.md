---
title: Znane problemy z wtyczką akustyczną projektu
titlesuffix: Azure Cognitive Services
description: W przypadku problemów akustycznych projektu mogą wystąpić następujące znane problemy.
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
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243038"
---
# <a name="project-acoustics-known-issues"></a>Znane problemy akustyczne projektu
W tym artykule opisano problemy, które mogą wystąpić w przypadku korzystania z funkcji akustycznych projektu.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Parametry akustyczne są tracone po zmianie nazwy sceny

W przypadku zmiany nazwy sceny wszystkie parametry akustyczne należące do tej sceny nie są automatycznie przenoszone do nowej sceny. Jednak nadal istnieją w starym pliku zasobów. W katalogu *edytora* obok pliku sceny Znajdź plik *[scena] _AcousticParameters. Asset* . Zmień nazwę pliku, aby odzwierciedlał nazwę nowej sceny.

## <a name="deploy-to-android-bug-from-some-unity-versions"></a>Usterka wdrożenia na platformie Android z niektórych wersji aparatu Unity

Niektóre wersje środowiska Unity zawierają [usterkę](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player) w sposobie wdrażania wtyczek audio w systemie Android. Upewnij się, że nie używasz wersji objętej tą usterką.

## <a name="could-not-find-metadata-file-systemsecuritydll-error"></a>Błąd "nie można znaleźć pliku metadanych system. Security. dll"

Upewnij się, że **wersja środowiska uruchomieniowego tworzenia skryptów** w ustawieniach **odtwarzacza** to *.NET 4. x odpowiednik*i ponownie uruchom środowisko Unity.

## <a name="authentication-problems-when-connecting-to-azure"></a>Problemy z uwierzytelnianiem podczas nawiązywania połączenia z platformą Azure

Sprawdź, czy:
- Użyto poprawnych poświadczeń dla Twojego konta platformy Azure.
- Twoje konto obsługuje typ węzła, który został żądany w tworzenie.
- Zegar systemowy jest poprawnie ustawiony.

## <a name="the-bake-tab-still-shows-deleting-after-you-cancel"></a>Karta Tworzenie nadal wyświetla "Usuwanie" po anulowaniu
Akustyczne projektu czyści wszystkie zasoby platformy Azure dla zadania po pomyślnym zakończeniu lub anulowaniu. Ten proces może potrwać do 5 minut.

## <a name="next-steps"></a>Następne kroki
* Wypróbuj zawartość przykładową [aparatu Unity](unity-quickstart.md) lub [Unreal](unreal-quickstart.md) .
