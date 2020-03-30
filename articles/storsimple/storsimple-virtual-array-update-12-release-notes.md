---
title: Microsoft Azure StorSimple Virtual Array Update 1.2 informacje o wersji| Dokumenty firmy Microsoft
description: W tym artykule opisano krytyczne otwarte problemy i rozwiązania dla tablicy wirtualnej StorSimple z aktualizacją 1.2.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 05/29/2019
ms.author: alkohli
ms.openlocfilehash: ea7e4801dfaad533403c0f927a03735ae409cc52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66420606"
---
# <a name="storsimple-virtual-array-update-12-release-notes"></a>StorSimple Virtual Array Update 1.2 informacje o wersji

W poniższych informacjach o wersji identyfikowano krytyczne otwarte problemy i rozwiązane problemy z aktualizacjami tablicy wirtualnej Usługi Microsoft Azure StorSimple.

Informacje o wersji są stale aktualizowane. W miarę odkrywania krytycznych problemów wymagających obejścia są do niego dodawane ich opisy. Przed wdrożeniem tablicy wirtualnej StorSimple należy dokładnie przejrzeć informacje zawarte w informacjach o wersji.

Aktualizacja 1.2 odpowiada wersji oprogramowania **10.0.10311.0**.

> [!IMPORTANT]
> - Aktualizacje są destrukcyjne i uruchom ponownie urządzenie. Jeśli we/wy są w toku, urządzenie ponosi przestoje. Aby uzyskać szczegółowe instrukcje dotyczące pakietów użytych do zastosowania tej aktualizacji, przejdź do [pobierz aktualizację 1.2](#download-update-12).
> - Aktualizacja 1.2 jest dostępna za pośrednictwem witryny Azure portal tylko wtedy, gdy na urządzeniu jest uruchomiona aktualizacja 1.0 lub 1.1.

## <a name="whats-new-in-update-12"></a>Co nowego w aktualizacji 1.2

Ta aktualizacja zawiera następujące poprawki błędów:

- Zwiększona odporność podczas przetwarzania usuniętych plików.
- Ulepszona obsługa wyjątków w ścieżce uruchamiania kodu, co prowadzi do zmniejszenia błędów w kopiach zapasowych, przywracaniu, odczytach w chmurze i automatycznej regeneracji miejsca.

## <a name="download-update-12"></a>Pobierz aktualizację 1.2

Aby pobrać tę aktualizację, przejdź do serwera [wykazu microsoft update](https://www.catalog.update.microsoft.com/Home.aspx) i pobierz pakiet KB4502035. Ten pakiet zawiera następujące pakiety:

 - **KB4493446,** który zawiera zbiorcze aktualizacje systemu Windows dla 2012 R2 do kwietnia 2019. Aby uzyskać więcej informacji na temat tego, co jest zawarte w tym zestawieniu, przejdź do [kwietniowego miesięcznego zestawienia zabezpieczeń](https://support.microsoft.com/help/4493446/windows-8-1-update-kb4493446).
 - **KB3011067,** który jest plikiem pakietu autonomicznego Microsoft Update WindowsTH-KB3011067-x64. Ten plik służy do aktualizacji oprogramowania urządzenia.

Pobierz aplikację KB4502035 i postępuj zgodnie z tymi instrukcjami, aby [zastosować aktualizację za pośrednictwem lokalnego interfejsu użytkownika sieci Web.](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)

## <a name="issues-fixed-in-update-12"></a>Problemy rozwiązane w aktualizacji 1.2

Poniższa tabela zawiera podsumowanie problemów rozwiązanych w tej wersji.

| Nie. | Funkcja | Problem |
| --- | --- | --- |
| 1 |Usunięcie| W poprzednich wersjach oprogramowania wystąpił problem, gdy użycie urządzenia nie zmieniło się nawet wtedy, gdy pliki zostały usunięte. Ten problem został rozwiązany w tej wersji. Ścieżka kodu warstwowego została bardziej odporna podczas przetwarzania usuniętych plików.|
| 2 |Obsługa wyjątków| W poprzednich wersjach oprogramowania wystąpił problem po ponownym uruchomieniu systemu, który może potencjalnie prowadzić do awarii kopii zapasowych, przywracania, odczytu z chmury i automatycznej regeneracji miejsca. Ta wersja zawiera zmiany dotyczące sposobu obsługi wyjątków w ścieżce uruchamiania.|

## <a name="known-issues-in-update-12"></a>Znane problemy w aktualizacji 1.2

W aktualizacji 1.2 nie odnotowano żadnych nowych problemów. Wszystkie problemy z wydaniem są przenoszone z poprzednich wydań. Aby wyświetlić podsumowanie znanych problemów z poprzednich wersji, przejdź do [strony Znane problemy w aktualizacji 1.1](storsimple-virtual-array-update-11-release-notes.md#known-issues-in-update-11).

## <a name="next-steps"></a>Następne kroki

Pobierz kb4502035 i [zastosuj aktualizację za pośrednictwem lokalnego interfejsu użytkownika sieci Web](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="references"></a>Dokumentacja

Szukasz starszej informacji o wydaniu? Przejdź do strony:
* [StorSimple Virtual Array Update 1.1 Informacje o wersji](storsimple-virtual-array-update-11-release-notes.md)
* [StorSimple Virtual Array Update 1.0 Release Notes](storsimple-virtual-array-update-1-release-notes.md)
* [StorSimple Virtual Array Update 0.6 Informacje o wersji](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple Virtual Array Update 0.5 Release Notes](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple Virtual Array Update 0.4 Informacje o wersji](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0.3 Informacje o wersji](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0.1 i 0.2 Informacje o wersji](storsimple-ova-update-01-release-notes.md)
* [StorSimple Informacje o ogólnej dostępności tablicy wirtualnej](storsimple-ova-pp-release-notes.md)
