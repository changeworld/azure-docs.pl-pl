---
title: Informacje o wersji 1.0 aktualizacji tablicy wirtualnego StorSimple | Dokumentacja firmy Microsoft
description: Opisuje krytyczne Otwórz problemy i rozwiązania dla tablicy wirtualnego StorSimple systemem Update 1.0.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: 777718c4893f1edab3613be5dc941e2716d4c972
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2017
ms.locfileid: "24012106"
---
# <a name="storsimple-virtual-array-update-10-release-notes"></a>Informacje o wersji 1.0 aktualizacji tablicy wirtualnego StorSimple

## <a name="overview"></a>Omówienie

W poniższych informacjach o zidentyfikować krytyczne problemy otwarte i rozwiązać problemy dotyczące aktualizacji tablicy wirtualne Microsoft Azure StorSimple.

Informacje o wersji są stale aktualizowane i wykrywane krytyczne problemy wymagające obejścia są dodawane. Przed wdrożeniem tablica wirtualnego StorSimple, należy dokładnie przejrzeć informacje zawarte w informacjach o wersji.

Update 1.0 odpowiada wersji oprogramowania **10.0.10296.0**.

> [!IMPORTANT]
> - Aktualizacje są one i ponownym uruchomieniu urządzenia. Jeśli we/wy jest w toku, urządzenie wiąże przestoju. Aby uzyskać szczegółowe informacje na temat zastosować aktualizację, [zainstalować aktualizacji 1.0](storsimple-virtual-array-install-update-1.md).
>
> - Aktualizacja 1 jest tylko dostępne za pośrednictwem portalu Azure, jeśli urządzenie korzysta z aktualizacji 0,6.

## <a name="whats-new-in-update-10"></a>Co to jest nowa w programie Update 1.0

**Update 1.0 zawiera zmiany związane z uwierzytelnianiem usługi Menedżer StorSimple urządzenia i powinny zostać wdrożone sieci najwcześniej.** Ta aktualizacja zawiera następujące ulepszenia i poprawki błędów:

 - **Użycie programu Azure Active Directory (AAD) do uwierzytelniania w usłudze Menedżer StorSimple urządzenia** — od Update 1.0 lub nowszej, Azure Active Directory jest używany do uwierzytelniania w usłudze Menedżer StorSimple urządzenia. Stary mechanizmu uwierzytelniania zostaną wycofane przez grudnia 2017 r. Wszyscy użytkownicy muszą zawrzeć nowe adresy URL uwierzytelniania w ich reguły zapory. Aby uzyskać więcej informacji, przejdź do uwierzytelniania adresy URL na liście [wymagania dotyczące sieci dla macierzy wirtualnego StorSimple](storsimple-ova-system-requirements.md).
 
    Jeśli adres URL uwierzytelniania nie jest uwzględniony w zasadach zapory, użytkownicy będą widzieć alert krytyczny, który ich urządzenia StorSimple nie można uwierzytelnić w usłudze. Użytkownicy widzą ten alert, muszą zawierać nowy adres URL uwierzytelniania. Aby uzyskać więcej informacji, przejdź do [StorSimple sieci alerty](storsimple-virtual-array-manage-alerts.md).

 - **Zwiększenie wydajności** -kilka poprawek były gotowe do poprawy szybkości odczyty chmury, warstwy dodatków i warstwy dokumentów. W związku z tym ulepszono wydajności kopii zapasowej i przywracania dla urządzeń z serwera iSCSI i plików.

 - **Wyrzucanie elementów kolekcji poprawy** — to wydanie zawiera poprawki, które zwiększają wydajność cykl zbierania pamięci, gdy urządzenia i konto magazynu znajdują się w dwóch regionach oddalonych.

 - **Rejestrowanie poprawy** — ta wersja zawiera ulepszenia dotyczące rejestrowania związanych z usuwaniem elementów bezużytecznych i ścieżkę We/Wy.


## <a name="issues-fixed-in-update-10"></a>Problemy rozwiązane w Update 1.0

Poniższa tabela zawiera podsumowanie problemy rozwiązane w tej wersji.

| Nie. | Funkcja | Problem |
| --- | --- | --- |
| 1 |Uwierzytelnianie za pomocą usługi AAD| Ta wersja zawiera zmiany, które umożliwia usłudze AAD, do uwierzytelniania przy użyciu Menedżera urządzeń StorSimple.|
| 2 |Wyrzucanie elementów bezużytecznych| Ten problem został zgłoszony w lokacji klienta, gdy urządzenie i przechowywania konta znajdują się w różnych regionach i klienta zgłoszone błędy sporadyczne sieci, co wpływające na rozliczenia. W tej wersji ten problem został rozwiązany. |
| 3 |Wydajność| Ta wersja zawiera zmiany, które powoduje przywrócenie/odczyty/poziomów w chmurze w / warstwy limit zwiększenie wydajności.|
| 4 |Aktualizacja| Wystąpił problem z aktualizacją w starszej wersji, które spowodowały niepowodzenie wykonywania kopii zapasowej w lokacji klienta. Tego problemu w tej wersji.|

## <a name="known-issues-in-update-10"></a>Znane problemy w Update 1.0

Poniższa tabela zawiera podsumowanie znane problemy dla tablicy wirtualnego StorSimple oraz problemów wymienionych wersji z poprzednich wersji.

| Nie. | Funkcja | Problem | Obejście/komentarzy |
| --- | --- | --- | --- |
| **1.** |Aktualizacje |Nie można zaktualizować tablic wirtualny utworzony w wersji zapoznawczej do obsługiwanej wersji ogólnej dostępności. |Te wirtualnego tablic musi można przełączyć w wersji ogólnodostępnej za pomocą przepływu pracy (DR) odzyskiwania po awarii. |
| **2.** |Dysk z danymi udostępnione |Po uprzednim udostępnieniu dysku danych o określonym rozmiarze określonym i utworzyć odpowiednie tablicy wirtualnego StorSimple, użytkownik musi nie zwiększania lub zmniejszania dysk z danymi. Próby spowoduje utratę wszystkich danych w warstwach lokalnych urządzenia. | |
| **3.** |Zasady grupy |Gdy urządzenie jest przyłączony do domeny, stosowanie zasad grupy może niekorzystnie wpłynąć na działanie urządzenia. |Sprawdź, czy tablica wirtualnej jest w jego własnej jednostce organizacyjnej (OU) usługi Active Directory i żadne obiekty zasad grupy (GPO) są stosowane do niego. |
| **4.** |Lokalnego interfejsu użytkownika sieci web |Ulepszone funkcje zabezpieczeń są włączone w programie Internet Explorer (nazywana konfiguracją IE ESC), niektórych lokalnego interfejsu użytkownika strony, takich jak rozwiązywanie problemów lub konserwacji może nie działać poprawnie. Przyciski na tych stronach również mogą nie działać. |Wyłącz funkcje zwiększonych zabezpieczeń programu Internet Explorer. |
| **5.** |Lokalnego interfejsu użytkownika sieci web |Na maszynie wirtualnej funkcji Hyper-V interfejsy sieciowe w sieci web interfejsu użytkownika są wyświetlane jako 10 GB/s interfejsów. |To zachowanie jest odbicia funkcji Hyper-v. Funkcji Hyper-V jest zawsze zawiera 10 GB/s dla wirtualnych kart sieciowych. |
| **6.** |Woluminy warstwowe lub udziały |Zakres bajtów blokady aplikacji współdziałających ze StorSimple woluminy warstwowe nie jest obsługiwane. Jeśli jest włączone blokowanie zakresu bajtów, Obsługa poziomów StorSimple nie działa. |Zalecane środki obejmują: <br></br>Wyłącz zakresu bajtów blokowania w logiki aplikacji.<br></br>Wybierz, aby umieścić dane dla tej aplikacji w woluminów przypiętych lokalnie, zamiast woluminy warstwowe.<br></br>*Zastrzeżenie:*: gdy lokalnie przy użyciu przypięty woluminów i jest włączone blokowanie zakresu bajtów, nawet przed zakończeniem przywracania można online woluminu przypiętego lokalnie. W takich przypadkach jeśli przywracania jest w toku, następnie należy poczekać na ukończenie przywracania. |
| **7.** |Udziały warstwowych |Praca z dużych plików może spowodować wolne warstwy w poziomie. |Podczas pracy z dużymi plikami, zaleca się, że największy plik jest mniejszy niż rozmiar udziału % 3. |
| **8.** |Używane pojemności dla akcji |Może zostać wyświetlony udostępnianie zużycia, gdy nie ma żadnych danych w udziale. To zużycie jest ponieważ używane pojemność udziałów obejmuje metadanych. | |
| **9.** |Odzyskiwanie po awarii |Może wyłącznie wykonywać odzyskiwanie po awarii, serwera plików do tej samej domeny co urządzenia źródłowego. Odzyskiwanie po awarii do urządzenia docelowego w innej domenie nie jest obsługiwane w tej wersji. |Ten sposób jest implementowany w nowszej wersji. Aby uzyskać więcej informacji, przejdź do [trybu Failover i odzyskiwania po awarii dla macierzy wirtualnego StorSimple](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |Tablice wirtualnego StorSimple nie mogą być zarządzane za pomocą programu Azure PowerShell w tej wersji. |Zarządzanie urządzeń wirtualnych powinna być wykonywana za pośrednictwem portalu Azure i lokalnego interfejsu użytkownika sieci web. |
| **11.** |Zmienianie hasła |Konsoli urządzenia wirtualnego tablicy akceptuje tylko danych wejściowych w en-us klawiatury formatu. | |
| **12.** |PROTOKOŁU CHAP |Nie można usunąć poświadczenia CHAP raz utworzony. Ponadto zmodyfikowania poświadczeń protokołu CHAP, należy przełączyć woluminy do trybu offline, a następnie przełączyć je na online, aby zmiany zaczęły obowiązywać. |Ten problem został rozwiązany w nowszej wersji. |
| **13.** |Serwer iSCSI |"Użyto magazynu" wyświetlany dla woluminu iSCSI mogą być różne w usługi Menedżer StorSimple urządzenia i hosta iSCSI. |Hosta iSCSI ma widok systemu plików.<br></br>Urządzenie widzi bloki przydzielone, gdy wolumin w maksymalnym rozmiarze. |
| **14.** |Serwer plików |Jeśli plik w folderze alternatywnych danych strumienia (AD) skojarzonych z nim, REKLAM nie jest kopię zapasową lub przywrócić za pomocą odzyskiwania po awarii, powielania i odzyskiwanie na poziomie elementu. | |
| **15.** |Serwer plików |Łącza symbolicznego nie są obsługiwane. | |
| **16.** |Serwer plików |Pliki chronione przez Windows System szyfrowania plików (EFS) po skopiowaniu przez lub przechowywany w tablicy wirtualnego StorSimple wynik serwera plików w nieobsługiwanej konfiguracji.  | |
| **17.** |Aktualizacje |Jeśli zostanie wyświetlony błąd kodu: 2359302 (szesnastkowo 0x240006) podczas próby instalacji poprawki za pośrednictwem lokalnego interfejsu użytkownika, następnie oznacza to, że poprawka jest już zainstalowana na urządzeniu.   | |
| **18.** |Aktualizacje |Użycie lokalnego interfejsu użytkownika sieci web do zainstalowania aktualizacji 1 na wirtualnych tablica, musi upewnij się, że używasz wersji Update 0,6. Jeśli używasz wersji mniejsze niż 0,6 aktualizacji, należy najpierw zainstalować aktualizację 0,6, a następnie Zastosuj Update 1. Po zainstalowaniu aktualizacji wersji 1.0 bezpośrednio z wersji 0,6 przed aktualizacją, następnie zostanie pominięte niektóre aktualizacje, i wykresy monitorowania nie będzie działać.   | |


## <a name="next-steps"></a>Następne kroki
[Zainstaluj aktualizację 1.0](storsimple-virtual-array-install-update-1.md) na tablica wirtualne StorSimple.

## <a name="references"></a>Dokumentacja
Szukasz starsze Uwaga wersji? Przejdź do strony:
*  [Informacje o wersji 0,6 aktualizacji tablicy wirtualnego StorSimple](storsimple-virtual-array-update-06-release-notes.md)
* [Informacje o wersji 0,5 aktualizacji tablicy wirtualnego StorSimple](storsimple-virtual-array-update-05-release-notes.md)
* [Informacje o wersji 0,4 aktualizacji tablicy wirtualnego StorSimple](storsimple-virtual-array-update-04-release-notes.md)
* [Informacje o wersji 0,3 aktualizacji tablicy wirtualnego StorSimple](storsimple-ova-update-03-release-notes.md)
* [Informacje o wersji aktualizacji tablicy wirtualnego StorSimple 0,1 i 0,2](storsimple-ova-update-01-release-notes.md)
* [Informacje o wersji dostępności ogólne tablicy wirtualnego StorSimple](storsimple-ova-pp-release-notes.md)
