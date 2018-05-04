---
title: Windows 10 informacje dotyczące ustawień roamingu | Dokumentacja firmy Microsoft
description: Pełną listę ustawień, które będą przekazywane lub kopii zapasowej w systemie Windows 10.
services: active-directory
keywords: roaming stanu przedsiębiorstwa chmury systemu windows
documentationcenter: ''
author: tanning
manager: mtillman
editor: curtand
ms.assetid: 17cffc3e-2928-4235-91f7-a685bd6bdcbf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: markvi
ms.openlocfilehash: 741b76935b5a6d9b2a6869ef57caa0ac0dc6351a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="windows-10-roaming-settings-reference"></a>Informacje dotyczące ustawień roamingu w systemie Windows 10
Poniżej znajduje się pełna lista wszystkich ustawień, które będą przekazywane lub kopii zapasowej w systemie Windows 10. 

## <a name="devices-and-endpoints"></a>Punktów końcowych i urządzeń
Poniższej tabeli przedstawiono podsumowanie urządzeń i typy kont, które są obsługiwane przez synchronizacji, kopia zapasowa i przywracanie framework w systemie Windows 10.

| Typ konta i działania | Klasyczna | Komórkowy |
| --- | --- | --- |
| Usługa Azure Active Directory: synchronizacja |Yes |Nie |
| Azure Active Directory: przywracania kopii zapasowej |Nie |Nie |
| Konto Microsoft: synchronizacja |Yes |Yes |
| Konto Microsoft: kopia zapasowa i przywracanie |Nie |Yes |

## <a name="what-is-backup"></a>Co to jest kopia zapasowa?
Ustawienia systemu Windows są zazwyczaj synchronizacji domyślnie, ale niektóre ustawienia są tylko kopię zapasową, takich jak lista zainstalowanych aplikacji na urządzeniu. Kopia zapasowa jest dla urządzeń przenośnych, a obecnie nie jest dostępna tylko dla użytkowników roamingu stanu przedsiębiorstwa. Kopia zapasowa przy użyciu konta Microsoft i przechowuje ustawienia i dane aplikacji w usłudze OneDrive. Jeśli użytkownik wyłączy synchronizacji na urządzeniu przy użyciu aplikacji ustawienia, zwykle synchronizuje dane aplikacji staje się kopii zapasowej tylko. Dane kopii zapasowej można uzyskać tylko za pośrednictwem operacji przywracania podczas środowisko pierwszego uruchomienia nowego urządzenia. Kopii zapasowych można wyłączyć za pomocą ustawień urządzenia i i można z niego usunięte za pomocą konta usługi OneDrive.

## <a name="windows-settings-overview"></a>Ustawienia systemu Windows — omówienie
Następujące grupy ustawienia są dostępne dla użytkowników końcowych włączyć lub wyłączyć synchronizację ustawień na urządzeniach z systemem Windows 10.

* Motywu: tło pulpitu użytkownika kafelka, pozycja paska zadań, itp. 
* Ustawień programu Internet Explorer: Historia przeglądania wpisane adresy URL, Ulubione itp. 
* Hasła: [skrytka na poświadczenia systemu Windows](https://technet.microsoft.com/library/jj554668.aspx), łącznie z profilów sieci Wi-Fi 
* Preferencje językowe: słownika, ustawień języka systemu 
* Ułatwienia dostępu: narrator, program Klawiatura ekranowa, Lupa 
* Inne ustawienia systemu Windows: Zobacz szczegóły ustawień systemu Windows

![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-syncyoursettings.png)

Synchronizowanie grupy (Ulubione, odczytywanie listy) ustawienie przeglądarki Microsoft Edge może włączona lub wyłączona przez użytkowników końcowych za pośrednictwem przeglądarki Microsoft Edge opcji menu Ustawienia.

![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-edge.png)

Dla systemu Windows 10 w wersji 1803 lub nowszego, programu Internet Explorer ustawienie grupy (Ulubione, wpisane adresy URL) synchronizowanie może być włączona lub wyłączona przez użytkowników końcowych za pomocą opcji menu ustawień programu Internet Explorer. 

![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-ie.png)

## <a name="windows-settings-details"></a>Szczegóły ustawień systemu Windows
W poniższej tabeli, innych pozycji w kolumnie Grupa ustawienia odwołuje się do ustawień, które można wyłączyć, przechodząc do ustawienia > konta > Synchronizuj swoje ustawienia > Ustawienia inne okna. 

Wewnętrzny pozycje w kolumnie grupa ustawień odnoszą się do ustawień i aplikacji, które można wyłączyć tylko synchronizowanie w aplikacji lub wyłączenie synchronizacji dla wszystkich danych z urządzenia przy użyciu zarządzania urządzeniami przenośnymi (MDM) lub ustawień zasad grupy.
Ustawienia, które nie są przekazywane lub synchronizacji nie będą należeć do grupy.

| Ustawienia | Klasyczna | Komórkowy | Grupa |
| --- | --- | --- | --- |
| **Konta**: obraz konta |Synchronizacji |X |Motyw |
| **Konta**: inne ustawienia konta |X |X | |
| **Zaawansowane komórkowego połączenia szerokopasmowego**: Nazwa sieci (umożliwia automatyczne odnajdowanie przenośnych hotspotami Wi-Fi za pośrednictwem połączenia Bluetooth) Udostępnianie połączenia internetowego |X |X |Hasła |
| **Dane aplikacji**: poszczególnych aplikacji można zsynchronizować danych |Kopia zapasowa synchronizacji |Kopia zapasowa synchronizacji |wewnętrzny |
| **Lista aplikacji**: lista zainstalowanych aplikacji |X |kopia zapasowa |Inne |
| **Bluetooth**: wszystkie ustawienia połączenia Bluetooth |X |X | |
| **Wiersz polecenia**: ustawienia "Domyślne" w wierszu polecenia |Synchronizacji |X | |
| **Poświadczenia**: skrytka na poświadczenia |Synchronizacji |Synchronizacji |hasło |
| **Daty, godziny i Region**: czas automatycznego (Synchronizacja czasowa Internet) |Synchronizacji |Synchronizacji |Język |
| **Daty, godziny i Region**: 24-godzinnym |Synchronizacji |X |Język |
| **Daty, godziny i Region**: Data i godzina |Synchronizacji |X |Język |
| **Daty, godziny i Region**: strefa czasowa | |X |Język |
| **Daty, godziny i Region**: czasu letniego |Synchronizacji |X |Język |
| **Daty, godziny i Region**: kraj/region |Synchronizacji |X |Język |
| **Daty, godziny i Region**: pierwszy dzień tygodnia |Synchronizacji |X |Język |
| **Daty, godziny i Region**: format region (ustawienia regionalne) |Synchronizacji |X |Język |
| **Daty, godziny i Region**: Data krótka |Synchronizacji |X |Język |
| **Daty, godziny i Region**: Data długa |Synchronizacji |X |Język |
| **Daty, godziny i Region**: krótki czas |Synchronizacji |X |Język |
| **Daty, godziny i Region**: długi czas |Synchronizacji |X |Język |
| **Personalizacja pulpitu**: kompozycji pulpitu (tła, kolorów systemu, dźwięki systemu domyślne, wygaszacz ekranu) |Synchronizacji |X |Motyw |
| **Personalizacja pulpitu**: Pokaz slajdów tapety |Synchronizacji |X |Motyw |
| **Personalizacja pulpitu**: ustawienia paska zadań (pozycja, automatyczne ukrywanie itp.) |Synchronizacji |X |Motyw |
| **Personalizacja pulpitu**: start układu ekranu |X |kopia zapasowa | |
| **Urządzenia**: nawiązaniu połączenia z drukarkami udostępnionymi |X |X |inne |
| **Przeglądarka Microsoft Edge**: odczytywanie listy |Synchronizacji |Synchronizacji |wewnętrzny |
| **Przeglądarka Microsoft Edge**: Ulubione |Synchronizacji |Synchronizacji |wewnętrzny |
| **Przeglądarka Microsoft Edge**: top witryn <sup> [[1]](#footnote-1)</sup> |Synchronizacji |Synchronizacji |wewnętrzny |
| **Przeglądarka Microsoft Edge**: wpisane adresy URL <sup> [[1]](#footnote-1)</sup> |Synchronizacji |Synchronizacji |wewnętrzny |
| **Przeglądarka Microsoft Edge**: ustawienia paska ulubionych <sup> [[1]](#footnote-1)</sup> |Synchronizacji |Synchronizacji |wewnętrzny |
| **Przeglądarka Microsoft Edge**: Pokaż przycisk Strona główna <sup> [[1]](#footnote-1)</sup> |Synchronizacji |Synchronizacji |wewnętrzny |
| **Przeglądarka Microsoft Edge**: Blokuj wyskakujące okienka <sup> [[1]](#footnote-1)</sup> |Synchronizacji |Synchronizacji |wewnętrzny |
| **Przeglądarka Microsoft Edge**: pytaj mnie, co należy zrobić z każdego pobrania <sup> [[1]](#footnote-1)</sup> |Synchronizacji |Synchronizacji |wewnętrzny |
| **Przeglądarka Microsoft Edge**: oferują na zapisywanie haseł <sup> [[1]](#footnote-1)</sup> |Synchronizacji |Synchronizacji |wewnętrzny |
| **Przeglądarka Microsoft Edge**: wysyłanie żądania nie Śledź żądań <sup> [[1]](#footnote-1)</sup> |Synchronizacji |Synchronizacji |wewnętrzny |
| **Przeglądarka Microsoft Edge**: wpisy formularza <sup> [[1]](#footnote-1)</sup> |Synchronizacji |Synchronizacji |wewnętrzny |
| **Przeglądarka Microsoft Edge**: Pokaż sugestie dotyczące wyszukiwania i lokacji, podczas pisania <sup> [[1]](#footnote-1)</sup> |Synchronizacji |Synchronizacji |wewnętrzny |
| **Przeglądarka Microsoft Edge**: preferencji pliki cookie <sup> [[1]](#footnote-1)</sup> |Synchronizacji |Synchronizacji |wewnętrzny |
| **Przeglądarka Microsoft Edge**: let witryn zapisać chronionych nośnika licencji w urządzeniu <sup> [[1]](#footnote-1)</sup> |Synchronizacji |Synchronizacji |wewnętrzny |
| **Przeglądarka Edge**: czytnik ustawienie <sup> [[1]](#footnote-1)</sup> |Synchronizacji |Synchronizacji |wewnętrzny |
| **Duży kontrast**: Włączanie/wyłączanie |Synchronizacji |X |łatwość dostępu |
| **Duży kontrast**: ustawienia motywu |Synchronizacji |X |łatwość dostępu |
| **Program Internet Explorer**: Otwórz karty (adres URL i tytuł) |Synchronizacji |Synchronizacji |Internet Explorer |
| **Program Internet Explorer**: odczytywanie listy |Synchronizacji |Synchronizacji |Internet Explorer |
| **Program Internet Explorer**: wpisane adresy URL |Synchronizacji |Synchronizacji |Internet Explorer |
| **Program Internet Explorer**: Historia przeglądania |Synchronizacji |Synchronizacji |Internet Explorer |
| **Program Internet Explorer**: Ulubione |Synchronizacji |Synchronizacji |Internet Explorer |
| **Program Internet Explorer**: wykluczone adresy URL |Synchronizacji |Synchronizacji |Internet Explorer |
| **Program Internet Explorer**: strony głównej |Synchronizacji |Synchronizacji |Internet Explorer |
| **Program Internet Explorer**: sugestie domeny |Synchronizacji |Synchronizacji |Internet Explorer |
| **Klawiatura**: użytkownicy mogą włączyć/wyłączyć program Klawiatura ekranowa |Synchronizacji |X |łatwość dostępu |
| **Klawiatura**: Włącz trwałe tak (domyślnie wyłączone) |Synchronizacji |X |łatwość dostępu |
| **Klawiatura**: Włącz klawisze filtru (domyślnie wyłączone) |Synchronizacji |X |łatwość dostępu |
| **Klawiatura**: Włączanie przełączania kluczy (domyślnie wyłączone) |Synchronizacji |X |łatwość dostępu |
| **Program Internet Explorer**: domeny języka: chiński (CHS) QWERTY - Włącz własnym nauki |Synchronizacji |X |Język |
| **Język**: CHS QWERTY - candidate dynamiczne Włączanie klasyfikacji |Synchronizacji |X |Język |
| **Język**: CHS QWERTY - char zestawu chiński uproszczony |Synchronizacji |X |Język |
| **Język**: CHS QWERTY - char zestawu chiński tradycyjny |Synchronizacji |X |Język |
| **Język**: CHS QWERTY - rozmytego pinyin |Synchronizacji |kopia zapasowa |Język |
| **Język**: CHS QWERTY - rozmytego par |Synchronizacji |kopia zapasowa |Język |
| **Język**: CHS QWERTY — pełna pinyin |Synchronizacji |X |Język |
| **Język**: CHS QWERTY - pinyin podwójne |Synchronizacji |X |Język |
| **Język**: QWERTY CHS - odczytywania automatyczne naprawianie |Synchronizacji |X |Język |
| **Język**: CHS QWERTY — klucz przełącznika C/E, shift |Synchronizacji |X |Język |
| **Język**: CHS QWERTY — klucz przełącznika C/E, Ctrl |Synchronizacji |X |Język |
| **Język**: WUBI CHS — tryb wprowadzania pojedynczy znak |Synchronizacji |X |Język |
| **Język**: WUBI CHS — Pokaż pozostałych kodowania kandydata |Synchronizacji |X |Język |
| **Język**: CHS WUBI - sygnał dźwiękowy kodowania 4 jest nieprawidłowy |Synchronizacji |X |Język |
| **Język**: Bopomofo (CHT) — obejmują CJK Ext A |Synchronizacji |X |Język |
| **Język**: japońskiego edytora IME - predykcyjnej pisania i niestandardowych słów |Synchronizacji |Synchronizacji |Język |
| **Język**: koreański (KOR) edytora IME |X |X |Język |
| **Język**: rozpoznawania pisma ręcznego |X |X |Język |
| **Język**: język profilu |Synchronizacji |kopia zapasowa |Język |
| **Język**: sprawdzanie pisowni - Autokorekty i podświetl pisowni |Synchronizacji |kopia zapasowa |Język |
| **Język**: Lista klawiatury |Synchronizacji |kopia zapasowa |Język |
| **Zablokuj ekran**: wszystkie blokady ekranu |X |X | |
| **Program Lupa**: Włączanie/wyłączanie (przełącznik główny) |X |X |Ułatwienia dostępu |
| **Program Lupa**: Włącz odwracanie kolorów lub Wyłącz (domyślnie wyłączone) |Synchronizacji |X |Ułatwienia dostępu |
| **Program Lupa**: śledzenie — wykonaj fokus klawiatury |Synchronizacji |X |Ułatwienia dostępu |
| **Program Lupa**: śledzenie — Śledź kursor myszy |Synchronizacji |X |Ułatwienia dostępu |
| **Program Lupa**: start podczas logowania (domyślnie wyłączone) |Synchronizacji |X |Ułatwienia dostępu |
| **Mysz**: Zmień rozmiar kursora myszy |Synchronizacji |X |inne |
| **Mysz**: zmiana koloru kursora myszy |Synchronizacji |X |inne |
| **Mysz**: wszystkie inne ustawienia |X |X | |
| **Narrator**: szybkiego uruchamiania |Synchronizacji |X |Ułatwienia dostępu |
| **Narrator**: użytkownicy mogą zmieniać Narrator, mówiąc wysokości |Synchronizacji |X |Ułatwienia dostępu |
| **Narrator**: użytkownicy mogą włączyć lub wyłączyć Narrator odczytywania wskazówek dotyczących wspólne elementy (na domyślne) |Synchronizacji |X |Ułatwienia dostępu |
| **Narrator**: użytkownicy mogą włączyć lub wyłączyć tego, czy można słuchać wpisane znaki (na domyślne) |Synchronizacji |X |Ułatwienia dostępu |
| **Narrator**: użytkownicy mogą włączyć lub wyłączyć tego, czy można słuchać klawiaturowe (na domyślne) |Synchronizacji |X |Ułatwienia dostępu |
| **Narrator**: ma wstawiania kursora po Narrator (na domyślne) |Synchronizacji |X |Ułatwienia dostępu |
| **Narrator**: Włącz visual wyróżnianie Narrator kursora (na domyślne) |Synchronizacji |X |Ułatwienia dostępu |
| **Narrator**: odtwarzanie dźwięku wskaźników (na domyślne) |Synchronizacji |X |Ułatwienia dostępu |
| **Narrator**: aktywowanie klawiszy touch podczas podnoszenia palca (domyślnie wyłączone) |Synchronizacji |X |Ułatwienia dostępu |
| **Łatwość dostępu**: ustawianie grubości migający kursor |Synchronizacji |X |Ułatwienia dostępu |
| **Łatwość dostępu**: Usuń obrazy tła (domyślnie wyłączone) |Synchronizacji |X |Ułatwienia dostępu |
| **Zasilania i uśpienia**: wszystkie ustawienia |X |X | |
| **Rozpocząć personalizacji ekranu**: akcent kolorów (tylko telefon) |X |Synchronizacji |Motyw |
| **Wpisywanie**: słownika |Synchronizacji |kopia zapasowa |Język |
| **Wpisywanie**: Autokorekty zawiera błąd pisowni programu word |Synchronizacji |kopia zapasowa |Język |
| **Wpisywanie**: zaznacz pisowni |Synchronizacji |kopia zapasowa |Język |
| **Wpisywanie**: Pokaż sugestie tekstu podczas pisania |Synchronizacji |kopia zapasowa |Język |
| **Wpisywanie**: dodają spację po wybraniu sugestię tekstu |Synchronizacji |kopia zapasowa |Język |
| **Wpisywanie**: Dodaj okres, po po dwukrotnym naciśnięciu spacji |Synchronizacji |kopia zapasowa |Język |
| **Wpisywanie**: Wielka litera na początku każdego zdania |Synchronizacji |kopia zapasowa |Język |
| **Wpisywanie**: Użyj wielkie litery, gdy po dwukrotnym naciśnięciu klawisza shift |Synchronizacji |kopia zapasowa |Język |
| **Wpisywanie**: odtwarzać dźwięki klucza podczas pisania |Synchronizacji |kopia zapasowa |Język |
| **Wpisywanie**: dane personalizacji touch klawiatury |Synchronizacji |kopia zapasowa |Język |
| **Wi-Fi**: profile sieci Wi-Fi (tylko WPA) |Synchronizacji |Synchronizacji |Hasła |

###### <a name="footnote-1"></a>Przypis 1
Minimalna obsługiwana wersja systemu operacyjnego Windows twórców aktualizacji (15063 kompilacji). 

## <a name="related-topics"></a>Powiązane tematy
* [Przegląd roamingu stanu przedsiębiorstwa](active-directory-windows-enterprise-state-roaming-overview.md)
* [Włącz roaming stanu przedsiębiorstwa w usłudze Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md)
* [Ustawienia i dane mobilne — często zadawane pytania](active-directory-windows-enterprise-state-roaming-faqs.md)
* [Zasady grupy i ustawienia zarządzania urządzeniami Przenośnymi dla ustawień synchronizacji](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
* [Rozwiązywanie problemów](active-directory-windows-enterprise-state-roaming-troubleshooting.md)
