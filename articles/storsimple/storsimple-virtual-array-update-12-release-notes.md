---
title: Informacje o wersji programu Microsoft Azure StorSimple wirtualnego tablicy Update 1.2 | Dokumentacja firmy Microsoft
description: Opisuje krytyczne nierozwiązane problemy i rozwiązania StorSimple Virtual Array systemem 1.2 aktualizacji.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 05/29/2019
ms.author: alkohli
ms.openlocfilehash: ea7e4801dfaad533403c0f927a03735ae409cc52
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420606"
---
# <a name="storsimple-virtual-array-update-12-release-notes"></a>Informacje o wersji 1.2 aktualizacji macierzy wirtualnej StorSimple

W poniższych informacjach o Zidentyfikuj krytyczne nierozwiązane problemy i rozwiązania problemów dotyczących aktualizacji Microsoft Azure StorSimple Virtual Array.

Informacje o wersji są stale aktualizowane. Jak są wszystkie wykrywane krytyczne problemy wymagające obejścia są dodawane. Przed wdrożeniem rozwiązania StorSimple Virtual Array, należy dokładnie przejrzeć informacje zawarte w informacjach o wersji.

Aktualizacji Update 1.2 odnosi się do wersji oprogramowania **10.0.10311.0**.

> [!IMPORTANT]
> - Aktualizacje są naturalnymi i ponownym uruchomieniu urządzenia. W przypadku operacji We/Wy w toku, urządzenie wiąże się z przestojem. Aby uzyskać szczegółowe informacje dotyczące pakietów używane, aby zastosować tę aktualizację, przejdź do [pobierania aktualizacji 1.2](#download-update-12).
> - Aktualizacji Update 1.2 jest dostępne w witrynie Azure portal, tylko wtedy, gdy urządzenie działa w aktualizacji 1.0 i 1.1.

## <a name="whats-new-in-update-12"></a>What's new in Update 1.2

Ta aktualizacja zawiera następujące poprawki:

- Większa odporność podczas przetwarzania usunięte pliki.
- Ulepszona obsługa wyjątków w ścieżce uruchamiania kodu, co prowadzi do zmniejszona błędów kopii zapasowych, przywracania, chmury i automatyczne odzyskiwanie miejsca.

## <a name="download-update-12"></a>Pobieranie aktualizacji Update 1.2

Aby pobrać tę aktualizację, przejdź do [katalogu usługi Microsoft Update](https://www.catalog.update.microsoft.com/Home.aspx) server i pakiet pobierania KB4502035. Ten pakiet zawiera następujące pakiety:

 - **KB4493446** zawierający aktualizacje zbiorcze Windows 2012 R2 do kwietnia 2019 r. Aby uzyskać więcej informacji o to, co jest uwzględnione w tym pakiecie zbiorczym, przejdź do [kwietnia miesięczne zabezpieczeń zbiorczy](https://support.microsoft.com/help/4493446/windows-8-1-update-kb4493446).
 - **KB3011067** czyli WindowsTH-KB3011067-x64 pliku pakietu autonomicznego usługi Microsoft Update. Ten plik jest używany do aktualizacji oprogramowania urządzenia.

Pobierz KB4502035 i wykonaj te instrukcje, aby [zastosować aktualizację za pomocą lokalnego Interfejsu w przeglądarce](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="issues-fixed-in-update-12"></a>Problemy rozwiązane w Update 1.2

Poniższa tabela zawiera podsumowanie problemów rozwiązanych w tej wersji.

| Nie. | Cecha | Problem |
| --- | --- | --- |
| 1 |Usuwanie| W poprzednich wersjach oprogramowania wystąpił problem podczas użycia urządzenia nie została zmieniona, nawet wtedy, gdy pliki zostały usunięte. Ten problem został rozwiązany w tej wersji. Ścieżka kodu obsługi warstw wprowadzono bardziej odporne na błędy podczas przetwarzania usunięte pliki.|
| 2 |Obsługa wyjątków| W poprzednich wersjach oprogramowania wystąpił problem podczas ponownego uruchomienia systemu, który może prowadzić do błędów kopii zapasowych, przywracania, odczytu z chmury i automatyczne odzyskiwanie miejsca po. Ta wersja zawiera zmiany, aby wyjątki były obsługi w ścieżce uruchamiania.|

## <a name="known-issues-in-update-12"></a>Znane problemy w programie Update 1.2

Nie nowe problemy były wydania wymienionych w Update 1.2. Wszystkie podane wersji problemy zostaną przeniesione z poprzednich wersji. Aby wyświetlić podsumowanie znanych problemów uwzględniona, korzystając z poprzednich wersjach, przejdź do [znane problemy w wersji 1.1 aktualizacji](storsimple-virtual-array-update-11-release-notes.md#known-issues-in-update-11).

## <a name="next-steps"></a>Kolejne kroki

Pobierz KB4502035 i [zastosować aktualizację za pomocą lokalnego Interfejsu w przeglądarce](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="references"></a>Dokumentacja

Szukasz starszej wersji? Przejdź do strony:
* [StorSimple Virtual Array Update — informacje o wersji 1.1](storsimple-virtual-array-update-11-release-notes.md)
* [StorSimple Virtual Array Update — informacje o wersji 1.0](storsimple-virtual-array-update-1-release-notes.md)
* [StorSimple Virtual Array Update 0.6 wersji](storsimple-virtual-array-update-06-release-notes.md)
* [Informacje o wersji 0,5 aktualizacji macierzy wirtualnej StorSimple](storsimple-virtual-array-update-05-release-notes.md)
* [Informacje o wersji 0,4 aktualizacji macierzy wirtualnej StorSimple](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array aktualizacji 0.3 informacje o wersji](storsimple-ova-update-03-release-notes.md)
* [Informacje o wersji Update macierzy wirtualnej StorSimple 0,1 a 0.2](storsimple-ova-update-01-release-notes.md)
* [Informacje o wersji dostępności ogólne macierzy wirtualnej StorSimple](storsimple-ova-pp-release-notes.md)
