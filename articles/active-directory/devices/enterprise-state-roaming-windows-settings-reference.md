---
title: Informacje dotyczące ustawień roamingu w systemie Windows 10 | Dokumentacja firmy Microsoft
description: Pełna lista wszystkich ustawień, które będą korzystania z roamingu lub kopii zapasowej w systemie Windows 10.
services: active-directory
keywords: roaming stanu dla przedsiębiorstw, chmura systemu windows
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: curtand
ms.subservice: devices
ms.assetid: 17cffc3e-2928-4235-91f7-a685bd6bdcbf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2019
ms.author: joflore
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6c80ee5d2a4d72be131c6a781cf793d1981e780
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60353263"
---
# <a name="windows-10-roaming-settings-reference"></a>Informacje dotyczące ustawień roamingu w systemie Windows 10
Oto Pełna lista wszystkich ustawień, które będą korzystania z roamingu lub kopii zapasowej w systemie Windows 10. 

## <a name="devices-and-endpoints"></a>Urządzenia i punktów końcowych
Poniższej tabeli przedstawiono podsumowanie urządzeń i typy kont, które są obsługiwane przez sync, kopia zapasowa i przywracanie struktura w systemie Windows 10.

| Typ konta i operacji | Klasyczna | Komórkowy |
| --- | --- | --- |
| Usługa Azure Active Directory: synchronizacja |Yes |Nie |
| Usługi Azure Active Directory: przywracania kopii zapasowej |Nie |Nie |
| Konto Microsoft: synchronizacja |Yes |Yes |
| Konto Microsoft: / przywracania kopii zapasowej |Nie |Yes |

## <a name="what-is-backup"></a>Co to jest tworzenia kopii zapasowej?
Ustawienia Windows ogólnie synchronizacji domyślnie, ale niektóre ustawienia są tylko kopii zapasowej, takie jak lista zainstalowanych aplikacji na urządzeniu. Kopia zapasowa jest dla urządzeń przenośnych tylko i obecnie nie są dostępne dla użytkowników, Roaming stanu dla przedsiębiorstw. Kopia zapasowa używa konta Microsoft i przechowuje ustawienia i dane aplikacji do usługi OneDrive. W przypadku wyłączenia przez użytkownika synchronizacji na urządzeniu, korzystając z aplikacji ustawienia danych aplikacji, które normalnie synchronizuje staje się kopii zapasowej tylko. Dane kopii zapasowej jest możliwy tylko za pośrednictwem operacji przywracania podczas pierwszego uruchomienia nowego urządzenia. Kopie zapasowe można wyłączyć za pomocą ustawień urządzenia i i można z niego usunięte za pomocą konta usługi OneDrive.

## <a name="windows-settings-overview"></a>Przegląd ustawień Windows
Następujące grupy ustawienia są dostępne dla użytkowników końcowych włączyć/wyłączyć synchronizację ustawień na urządzeniach z systemem Windows 10.

* Motyw: tło pulpitu, Kafelek użytkownika, położenie paska zadań itd. 
* Ustawień programu Internet Explorer: Historia przeglądania wpisane adresy URL, Ulubione, itp. 
* Hasła: Menedżer poświadczeń Windows, łącznie z profilami sieci Wi-Fi 
* Preferencje językowe: słownika, ustawień języka systemu 
* Ułatwienia dostępu: narrator, program Klawiatura ekranowa, program Lupa 
* Inne ustawienia Windows: Zobacz szczegóły ustawień Windows
* Ustawienia przeglądarki Microsoft Edge: Ulubione Microsoft Edge, przeczytania i inne ustawienia

![Synchronizowanie ustawień z](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-syncyoursettings.png)

Trwa synchronizowanie grupy (Ulubione, przeczytania) ustawienia przeglądarki Microsoft Edge można włączać lub wyłączać przez użytkowników końcowych za pośrednictwem przeglądarki Microsoft Edge opcji menu Ustawienia.

![Konto](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-edge.png)

Dla systemu Windows 10 w wersji 1803 lub nowszego, programu Internet Explorer ustawienie grupy (Ulubione, wpisane adresy URL) synchronizacji można włączać lub wyłączać przez użytkowników końcowych za pomocą opcji menu ustawień programu Internet Explorer. 

![Ustawienia](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-ie.png)

## <a name="windows-settings-details"></a>Szczegóły ustawień Windows
W poniższej tabeli, pozostałe wpisy w kolumnie grupa ustawień odwołuje się do ustawień, które można wyłączyć, wybierając pozycję Ustawienia > konta > synchronizować ustawienia > Windows inne ustawienia. 

Wewnętrzny wpisy w kolumnie ustawień grupy dotyczą ustawienia i aplikacje, które można wyłączyć tylko synchronizowanie w samej aplikacji lub przez wyłączenie synchronizacji dla całego urządzenia przy użyciu zarządzania urządzeniami przenośnymi (MDM) lub ustawień zasad grupy.
Ustawienia, które nie są przekazywane lub synchronizacji nie będą należeć do grupy.

| Ustawienia | Klasyczna | Komórkowy | Grupa |
| --- | --- | --- | --- |
| **Konta**: obraz konta |Synchronizacja |X |Motyw |
| **Konta**: inne ustawienia konta |X |X | |
| **Zaawansowane komórkowego połączenia szerokopasmowego**: Nazwa sieci (umożliwia automatyczne odnajdowanie przenośnych hotspotami Wi-Fi za pośrednictwem połączenia Bluetooth) Udostępnianie połączenia internetowego |X |X |Hasła |
| **Dane aplikacji**: poszczególnych aplikacji można zsynchronizować danych |Kopia zapasowa synchronizacji |Kopia zapasowa synchronizacji |Wewnętrzne |
| **Lista aplikacji**: lista zainstalowanych aplikacji |X |kopia zapasowa |Inne |
| **Bluetooth**: wszystkie ustawienia połączeń Bluetooth |X |X | |
| **Wiersz polecenia**: Polecenie Ustawienia monitu "Domyślne" |Synchronizacja |X |Wewnętrzne |
| **poświadczenia**: Skrytka na poświadczenia |Synchronizacja |Synchronizacja |password |
| **Daty, godziny i regionu**: automatyczne czasu (Synchronizacja czasowa Internet) |Synchronizacja |Synchronizacja |language |
| **Daty, godziny i Region**: zegar 24-godzinny |Synchronizacja |X |language |
| **Daty, godziny i regionu**: Data i godzina |Synchronizacja |X |language |
| **Daty, godziny i regionu**: strefa czasowa | |X |language |
| **Daty, godziny i regionu**: czasu letniego |Synchronizacja |X |language |
| **Daty, godziny i regionu**: kraj/region |Synchronizacja |X |language |
| **Daty, godziny i regionu**: pierwszy dzień tygodnia |Synchronizacja |X |language |
| **Daty, godziny i regionu**: format region (ustawienia regionalne) |Synchronizacja |X |language |
| **Daty, godziny i regionu**: Data krótka |Synchronizacja |X |language |
| **Daty, godziny i regionu**: Data długa |Synchronizacja |X |language |
| **Daty, godziny i regionu**: krótki czas |Synchronizacja |X |language |
| **Daty, godziny i regionu**: godzina długa |Synchronizacja |X |language |
| **Personalizacja pulpitu**: motyw pulpitu (tła kolorów systemu, dźwięki systemu domyślnego, wygaszacz ekranu) |Synchronizacja |X |Motyw |
| **Personalizacja pulpitu**: Pokaz slajdów tapety |Synchronizacja |X |Motyw |
| **Personalizacja pulpitu**: ustawienia paska zadań (pozycja, automatyczne ukrywanie itp.) |Synchronizacja |X |Motyw |
| **Personalizacja pulpitu**: układ ekranu start |X |kopia zapasowa | |
| **Urządzenia**: połączenia z drukarkami udostępnionymi |X |X |inne |
| **Przeglądarka Microsoft Edge**: odczytywanie listy |Synchronizacja |Synchronizacja |Wewnętrzne |
| **Przeglądarka Microsoft Edge**: Ulubione |Synchronizacja |Synchronizacja |Wewnętrzne |
| **Przeglądarka Microsoft Edge**: top witryn <sup> [[1]](#footnote-1)</sup> |Synchronizacja |Synchronizacja |Wewnętrzne |
| **Przeglądarka Microsoft Edge**: wpisane adresy URL <sup> [[1]](#footnote-1)</sup> |Synchronizacja |Synchronizacja |Wewnętrzne |
| **Przeglądarka Microsoft Edge**: Ulubione, ustawienia paska <sup> [[1]](#footnote-1)</sup> |Synchronizacja |Synchronizacja |Wewnętrzne |
| **Przeglądarka Microsoft Edge**: Pokaż przycisk strony głównej <sup> [[1]](#footnote-1)</sup> |Synchronizacja |Synchronizacja |Wewnętrzne |
| **Przeglądarka Microsoft Edge**: Blokuj wyskakujące okienka <sup> [[1]](#footnote-1)</sup> |Synchronizacja |Synchronizacja |Wewnętrzne |
| **Przeglądarka Microsoft Edge**: Spytaj użytkownika, co można zrobić za pomocą każdego pobrania <sup> [[1]](#footnote-1)</sup> |Synchronizacja |Synchronizacja |Wewnętrzne |
| **Przeglądarka Microsoft Edge**: oferty na zapisywanie haseł <sup> [[1]](#footnote-1)</sup> |Synchronizacja |Synchronizacja |Wewnętrzne |
| **Przeglądarka Microsoft Edge**: wysyłaj nagłówki nie Śledź żądania <sup> [[1]](#footnote-1)</sup> |Synchronizacja |Synchronizacja |Wewnętrzne |
| **Przeglądarka Microsoft Edge**: zapisywanie wpisów w formularzu <sup> [[1]](#footnote-1)</sup> |Synchronizacja |Synchronizacja |Wewnętrzne |
| **Przeglądarka Microsoft Edge**: Pokaż sugestie wyszukiwania i lokacji, podczas pisania <sup> [[1]](#footnote-1)</sup> |Synchronizacja |Synchronizacja |Wewnętrzne |
| **Przeglądarka Microsoft Edge**: preferencji pliki cookie <sup> [[1]](#footnote-1)</sup> |Synchronizacja |Synchronizacja |Wewnętrzne |
| **Przeglądarka Microsoft Edge**: let Zapisz chronione nośnika licencji na urządzeniu z systemem lokacji <sup> [[1]](#footnote-1)</sup> |Synchronizacja |Synchronizacja |Wewnętrzne |
| **Przeglądarka Microsoft Edge**: czytnika zawartości ekranu, ustawienia <sup> [[1]](#footnote-1)</sup> |Synchronizacja |Synchronizacja |Wewnętrzne |
| **Duży kontrast**: Włączanie/wyłączanie |Synchronizacja |X |łatwość dostępu |
| **Duży kontrast**: Ustawienia kompozycji |Synchronizacja |X |łatwość dostępu |
| **Program Internet Explorer**: Otwórz karty (adres URL i tytuł) |Synchronizacja |Synchronizacja |Internet Explorer |
| **Program Internet Explorer**: odczytywanie listy |Synchronizacja |Synchronizacja |Internet Explorer |
| **Program Internet Explorer**: wpisane adresy URL |Synchronizacja |Synchronizacja |Internet Explorer |
| **Program Internet Explorer**: Historia przeglądania |Synchronizacja |Synchronizacja |Internet Explorer |
| **Program Internet Explorer**: Ulubione |Synchronizacja |Synchronizacja |Internet Explorer |
| **Program Internet Explorer**: wykluczone adresy URL |Synchronizacja |Synchronizacja |Internet Explorer |
| **Program Internet Explorer**: strony główne |Synchronizacja |Synchronizacja |Internet Explorer |
| **Program Internet Explorer**: sugestie dotyczące domeny |Synchronizacja |Synchronizacja |Internet Explorer |
| **Klawiatura**: użytkownicy mogą włączyć/wyłączyć program Klawiatura ekranowa |Synchronizacja |X |łatwość dostępu |
| **Klawiatura**: Włącz umocowany tak (funkcja domyślnie wyłączona) |Synchronizacja |X |łatwość dostępu |
| **Klawiatura**: Włącz funkcję Klawisze filtru (funkcja domyślnie wyłączona) |Synchronizacja |X |łatwość dostępu |
| **Klawiatura**: Włączanie przełączania kluczy (funkcja domyślnie wyłączona) |Synchronizacja |X |łatwość dostępu |
| **Program Internet Explorer**: domeny języka: Chiński (CHS) QWERTY — Włączanie, samodzielnie uczenia |Synchronizacja |X |Język |
| **Język**: QWERTY (CHS) — ranking Release candidate dynamiczne Włączanie |Synchronizacja |X |Język |
| **Język**: QWERTY (CHS) — Chiński (uproszczony) char zestawu |Synchronizacja |X |Język |
| **Język**: QWERTY (CHS) — Chiński tradycyjny char zestawu |Synchronizacja |X |Język |
| **Język**: QWERTY (CHS) — pinyin rozmytego |Synchronizacja |kopia zapasowa |Język |
| **Język**: QWERTY (CHS) — pary rozmytego |Synchronizacja |kopia zapasowa |Język |
| **Język**: QWERTY (CHS) — pełna pinyin |Synchronizacja |X |Język |
| **Język**: QWERTY (CHS) — double pinyin |Synchronizacja |X |Język |
| **Język**: QWERTY (CHS) — automatyczne poprawianie do czytania |Synchronizacja |X |Język |
| **Język**: QWERTY (CHS) — klucz przełącznika C/E, shift |Synchronizacja |X |Język |
| **Język**: QWERTY (CHS) — klucz przełącznika C/E, Ctrl |Synchronizacja |X |Język |
| **Język**: WUBI (CHS) — tryb wprowadzania pojedynczy znak |Synchronizacja |X |Język |
| **Język**: WUBI (CHS) — Pokaż pozostałe kodowania kandydata |Synchronizacja |X |Język |
| **Język**: WUBI (CHS) — sygnału dźwiękowego po 4 niezwiązanych z pisaniem kodu jest nieprawidłowy |Synchronizacja |X |Język |
| **Język**: Bopomofo (CHT) — obejmują CJK Ext A |Synchronizacja |X |Język |
| **Język**: Japońskiego edytora IME - predykcyjne Pisownia i niestandardowe słowa |Synchronizacja |Synchronizacja |Język |
| **Język**: Koreański (KOR) edytora IME |X |X |Język |
| **Język**: rozpoznawania pisma ręcznego |X |X |Język |
| **Język**: profil języka |Synchronizacja |kopia zapasowa |Język |
| **Język**: sprawdzanie pisowni - Autokorekty i podświetl błędy pisowni. |Synchronizacja |kopia zapasowa |Język |
| **Język**: Lista klawiatury |Synchronizacja |kopia zapasowa |Język |
| **Blokuj ekran**: wszystkie blokady ekranu |X |X | |
| **Program Lupa**: Włączanie/wyłączanie (Przełącz główny) |X |X |Ułatwienia dostępu |
| **Program Lupa**: Włącz odwracanie kolorów lub Wyłącz (funkcja domyślnie wyłączona) |Synchronizacja |X |Ułatwienia dostępu |
| **Program Lupa**: śledzenie - postępuj zgodnie z fokus klawiatury |Synchronizacja |X |Ułatwienia dostępu |
| **Program Lupa**: śledzenie - postępuj zgodnie z kursora myszy |Synchronizacja |X |Ułatwienia dostępu |
| **Program Lupa**: uruchomić, gdy użytkownicy logują się (funkcja domyślnie wyłączona) |Synchronizacja |X |Ułatwienia dostępu |
| **Mysz**: zmiana rozmiaru kursora myszy |Synchronizacja |X |inne |
| **Mysz**: Zmień kolor kursora myszy |Synchronizacja |X |inne |
| **Mysz**: wszystkie inne ustawienia |X |X | |
| **Narrator**: Szybkie uruchamianie |Synchronizacja |X |Ułatwienia dostępu |
| **Narrator**: użytkownicy mogą zmieniać Narrator wypowiedzi pomysłu |Synchronizacja |X |Ułatwienia dostępu |
| **Narrator**: użytkownicy mogą włączyć lub wyłączyć Narrator odczytywanie wskazówek dotyczących wspólne elementy (na domyślnie) |Synchronizacja |X |Ułatwienia dostępu |
| **Narrator**: użytkownicy mogą włączyć lub wyłączyć tego, czy ich usłyszeć wpisane znaki (w domyślnym) |Synchronizacja |X |Ułatwienia dostępu |
| **Narrator**: użytkownicy mogą włączyć lub wyłączyć tego, czy ich usłyszeć słowa wpisane (na domyślnie) |Synchronizacja |X |Ułatwienia dostępu |
| **Narrator**: mają wstawiania kursora po Narrator (na domyślnie) |Synchronizacja |X |Ułatwienia dostępu |
| **Narrator**: Włącz visual wyróżnienie kursora Narrator (na domyślnie) |Synchronizacja |X |Ułatwienia dostępu |
| **Narrator**: odtwarzanie dźwięku podpowiedzi (na domyślnie) |Synchronizacja |X |Ułatwienia dostępu |
| **Narrator**: Uaktywnij klawisze klawiatury dotykowej, gdy przenoszenie palca (funkcja domyślnie wyłączona) |Synchronizacja |X |Ułatwienia dostępu |
| **Łatwość dostępu**: Ustaw grubość migający kursor |Synchronizacja |X |Ułatwienia dostępu |
| **Łatwość dostępu**: Aby usunąć obrazy tła (funkcja domyślnie wyłączona) |Synchronizacja |X |Ułatwienia dostępu |
| **Zasilania i uśpienia**: wszystkie ustawienia |X |X | |
| **Rozpocznij personalizacji ekranu**: akcent kolorów (tylko phone) |X |Synchronizacja |Motyw |
| **Wpisywanie**: słownika |Synchronizacja |kopia zapasowa |Język |
| **Wpisywanie**: Autokorekty błędnie napisane słowa |Synchronizacja |kopia zapasowa |Język |
| **Wpisywanie**: zaznacz błędnie napisanych wyrazów |Synchronizacja |kopia zapasowa |Język |
| **Wpisywanie**: Pokazuj podpowiedzi tekstowe podczas pisania |Synchronizacja |kopia zapasowa |Język |
| **Wpisywanie**: Dodaj spację, po wybraniu podpowiedzi tekstowe |Synchronizacja |kopia zapasowa |Język |
| **Wpisywanie**: Dodaj kropkę po I dotknij spacja |Synchronizacja |kopia zapasowa |Język |
| **Wpisywanie**: wykorzystywanie pierwszą literę każdego zdania |Synchronizacja |kopia zapasowa |Język |
| **Wpisywanie**: Użyj wielkie litery, gdy I dwukrotnie naciśnij klawisz shift |Synchronizacja |kopia zapasowa |Język |
| **Wpisywanie**: odtwarzanie dźwięku klucza podczas pisania |Synchronizacja |kopia zapasowa |Język |
| **Wpisywanie**: danych personalizacji dla klawiatury dotykowej |Synchronizacja |kopia zapasowa |Język |
| **Sieci Wi-Fi**: Profile sieci Wi-Fi (tylko WPA) |Synchronizacja |Synchronizacja |Hasła |

###### <a name="footnote-1"></a>Przypis 1
Minimalna obsługiwana wersja systemu operacyjnego Windows aktualizacją dla kreatywnych (kompilacja 15063). 

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać przegląd, zobacz [Przegląd roaming stanu dla przedsiębiorstw](enterprise-state-roaming-overview.md).

