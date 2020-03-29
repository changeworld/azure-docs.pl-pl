---
title: Odwołanie do ustawień roamingu systemu Windows 10 — usługa Azure Active Directory
description: Ustawienia, które będą przemieszczane lub archiwizowane w systemie Windows 10 za pomocą ESR
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a7abc402f1fc2e449e7aac5effdb01b6b941100
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672624"
---
# <a name="windows-10-roaming-settings-reference"></a>Informacje dotyczące ustawień roamingu w systemie Windows 10

Poniżej znajduje się lista ustawień, które będą przemieszczane lub archiwizowane w systemie Windows 10. 

## <a name="devices-and-endpoints"></a>Urządzenia i punkty końcowe

Zobacz poniższą tabelę, aby zapoznać się z podsumowaniem urządzeń i typów kont obsługiwanych przez strukturę synchronizacji, tworzenia kopii zapasowych i przywracania w systemie Windows 10.

| Typ i działanie konta | Aplikacje klasyczne | Urządzenia przenośne |
| --- | --- | --- |
| Usługa Azure Active Directory: synchronizacja |Tak |Nie |
| Usługa Azure Active Directory: tworzenie kopii zapasowych/przywracanie |Nie |Nie |
| Konto Microsoft: synchronizacja |Tak |Tak |
| Konto Microsoft: kopia zapasowa/przywracanie |Nie |Tak |

## <a name="what-is-backup"></a>Co to jest kopia zapasowa?

Ustawienia systemu Windows są zazwyczaj domyślnie synchronizowane, ale niektóre ustawienia są tylko kopie zapasowe, takie jak lista zainstalowanych aplikacji na urządzeniu. Kopia zapasowa jest dostępna tylko dla urządzeń przenośnych i obecnie nie jest dostępna dla użytkowników korzystających z roamingu w stanie przedsiębiorstwa. Kopia zapasowa używa konta Microsoft i przechowuje ustawienia i dane aplikacji w usłudze OneDrive. Jeśli użytkownik wyłączy synchronizację na urządzeniu za pomocą aplikacji Ustawienia, dane aplikacji, które zwykle synchronizuje, stają się tylko kopia zapasowa. Dane kopii zapasowej są dostępne tylko za pośrednictwem operacji przywracania podczas pierwszego uruchomienia nowego urządzenia. Kopie zapasowe można wyłączyć za pomocą ustawień urządzenia i można nimi zarządzać i usuwać za pośrednictwem konta usługi OneDrive użytkownika.

## <a name="windows-settings-overview"></a>Omówienie ustawień systemu Windows

Następujące grupy ustawień są dostępne dla użytkowników końcowych, aby włączyć/wyłączyć synchronizację ustawień na urządzeniach z systemem Windows 10.

* Temat: tło pulpitu, kafelek użytkownika, pozycja paska zadań itp. 
* Ustawienia programu Internet Explorer: historia przeglądania, wpisane adresy URL, ulubione itp. 
* Hasła: Menedżer poświadczeń systemu Windows, w tym profile Wi-Fi 
* Preferencje językowe: słownik pisowni, ustawienia języka systemowego 
* Łatwość dostępu: narrator, klawiatura ekranowa, lupa 
* Inne ustawienia systemu Windows: zobacz szczegóły ustawień systemu Windows
* Ustawienie przeglądarki Microsoft Edge: ulubione przeglądarki Microsoft Edge, lista lektur i inne ustawienia

![Synchronizuj swoje ustawienia](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-syncyoursettings.png)

> [!NOTE]
> Ten artykuł dotyczy przeglądarki microsoft edge legacy w formacie HTML uruchomionej w systemie Windows 10 w lipcu 2015 r. Artykuł nie ma zastosowania do nowej przeglądarki opartej na chromie Microsoft Edge, wydanej 15 stycznia 2020 roku. Aby uzyskać więcej informacji na temat zachowania synchronizacji dla nowej przeglądarki Microsoft Edge, zobacz artykuł [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync).

Synchronizacja ustawień przeglądarki Przeglądarki Microsoft Edge (ulubione, lista lektur) może być włączona lub wyłączona przez użytkowników końcowych za pomocą opcji menu Ustawień przeglądarki Microsoft Edge.

![Konto](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-edge.png)

W systemie Windows 10 w wersji 1803 lub nowszej synchronizacja grupy ustawień programu Internet Explorer (ulubione, wpisane adresy URL) może być włączana lub wyłączona przez użytkowników końcowych za pomocą opcji menu Ustawienia programu Internet Explorer. 

![Ustawienia](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-ie.png)

## <a name="windows-settings-details"></a>Szczegóły ustawień systemu Windows

W poniższej tabeli inne wpisy w kolumnie Grupa ustawień odnoszą się do ustawień, które można wyłączyć, przechodząc do ustawienia > konta > synchronizuj ustawienia > inne ustawienia systemu Windows. 

Wpisy wewnętrzne w kolumnie Grupa ustawień odnoszą się do ustawień i aplikacji, które można wyłączyć tylko z synchronizacji w samej aplikacji lub przez wyłączenie synchronizacji dla całego urządzenia przy użyciu ustawień zarządzania urządzeniami przenośnymi (MDM) lub zasad grupy.
Ustawienia, które nie są wędruj lub nie będą synchronizowane, nie będą należeć do grupy.

| Ustawienia | Aplikacje klasyczne | Urządzenia przenośne | Grupa |
| --- | --- | --- | --- |
| **Konta**: zdjęcie konta |synchronizacja |X |motyw |
| **Konta:** inne ustawienia konta |X |X | |
| **Zaawansowana komórkowa łączność szerokopasmowa:** nazwa sieci udostępniania połączenia internetowego (umożliwia automatyczne wykrywanie mobilnych hotspotów Wi-Fi przez Bluetooth) |X |X |Hasła |
| **Dane aplikacji:** poszczególne aplikacje mogą synchronizować dane |synchronizacja kopii zapasowej |synchronizacja kopii zapasowej |internal |
| **Lista aplikacji**: lista zainstalowanych aplikacji |X |kopia zapasowa |Inne |
| **Bluetooth**: wszystkie ustawienia Bluetooth |X |X | |
| **Wiersz polecenia**: Wiersz polecenia "Ustawienia domyślne" wiersza polecenia |synchronizacja |X |internal |
| **Poświadczenia**: Szafka poświadczeń |synchronizacja |synchronizacja |hasło |
| **Data, godzina i region:** czas automatyczny (synchronizacja czasu internetowego) |synchronizacja |synchronizacja |language |
| **Data, godzina i region:** zegar 24-godzinny |synchronizacja |X |language |
| **Data, godzina i region:** data i godzina |synchronizacja |X |language |
| **Data, godzina i region**: strefa czasowa | |X |language |
| **Data, godzina i region:** czas letni |synchronizacja |X |language |
| **Data, godzina i region:** kraj/region |synchronizacja |X |language |
| **Data, godzina i region:** pierwszy dzień tygodnia |synchronizacja |X |language |
| **Data, godzina i region:** format regionu (ustawienia regionalne) |synchronizacja |X |language |
| **Data, godzina i region**: krótka data |synchronizacja |X |language |
| **Data, godzina i region:** data długa |synchronizacja |X |language |
| **Data, godzina i region:** krótki czas |synchronizacja |X |language |
| **Data, godzina i region:** długi czas |synchronizacja |X |language |
| **Personalizacja pulpitu:** motyw pulpitu (tło, kolor systemu, domyślne dźwięki systemowe, wygaszacz ekranu) |synchronizacja |X |motyw |
| **Personalizacja pulpitu**: tapeta pokazu slajdów |synchronizacja |X |motyw |
| **Personalizacja pulpitu:** ustawienia paska zadań (pozycja, automatyczne ukrywanie itp.) |synchronizacja |X |motyw |
| **Personalizacja pulpitu:** układ ekranu startowego |X |kopia zapasowa | |
| **Urządzenia:** drukarki udostępnione, do których masz połączenie |X |X |inne |
| **Przeglądarka Microsoft Edge**: lista lektur |synchronizacja |synchronizacja |internal |
| **Przeglądarka Microsoft Edge**: ulubione |synchronizacja |synchronizacja |internal |
| **Przeglądarka Microsoft Edge**: najlepsze witryny <sup> [[1]](#footnote-1)</sup> |synchronizacja |synchronizacja |internal |
| **Przeglądarka Microsoft Edge**: wpisane adresy URL <sup> [[1]](#footnote-1)</sup> |synchronizacja |synchronizacja |internal |
| **Przeglądarka Microsoft Edge**: ustawienia paska ulubionych <sup> [[1]](#footnote-1)</sup> |synchronizacja |synchronizacja |internal |
| **Przeglądarka Microsoft Edge**: pokaż przycisk home <sup> [[1]](#footnote-1)</sup> |synchronizacja |synchronizacja |internal |
| **Przeglądarka Microsoft Edge**: blokowanie wyskakujących okienków <sup> [[1]](#footnote-1)</sup> |synchronizacja |synchronizacja |internal |
| **Przeglądarka Microsoft Edge**: zapytaj mnie, co zrobić z każdym pobraniem <sup> [[1]](#footnote-1)</sup> |synchronizacja |synchronizacja |internal |
| **Przeglądarka Microsoft Edge**: oferta zapisywania haseł <sup> [[1]](#footnote-1)</sup> |synchronizacja |synchronizacja |internal |
| **Przeglądarka Microsoft Edge**: send nie śledź żądań <sup> [[1]](#footnote-1)</sup> |synchronizacja |synchronizacja |internal |
| **Przeglądarka Microsoft Edge**: zapisywanie wpisów formularzy <sup> [[1]](#footnote-1)</sup> |synchronizacja |synchronizacja |internal |
| **Przeglądarka Microsoft Edge**: pokaż sugestie wyszukiwania i witryny podczas pisania <sup> [[1]](#footnote-1)</sup> |synchronizacja |synchronizacja |internal |
| **Przeglądarka Microsoft Edge**: preferencja dotycząca plików cookie <sup> [[1]](#footnote-1)</sup> |synchronizacja |synchronizacja |internal |
| **Przeglądarka Microsoft Edge**: pozwala witrynom zapisywać chronione licencje multimedialne na moim urządzeniu <sup> [[1]](#footnote-1)</sup> |synchronizacja |synchronizacja |internal |
| **Przeglądarka Microsoft Edge**: ustawienie czytnika ekranu <sup> [[1]](#footnote-1)</sup> |synchronizacja |synchronizacja |internal |
| **Wysoki kontrast:** włączanie lub wyłączanie |synchronizacja |X |ułatwienia dostępu |
| **Wysoki kontrast**: Ustawienia motywu |synchronizacja |X |ułatwienia dostępu |
| **Internet Explorer**: otwieranie kart (adres URL i tytuł) |synchronizacja |synchronizacja |Internet Explorer |
| **Internet Explorer**: lista lektur |synchronizacja |synchronizacja |Internet Explorer |
| **Internet Explorer**: wpisane adresy URL |synchronizacja |synchronizacja |Internet Explorer |
| **Internet Explorer**: historia przeglądania |synchronizacja |synchronizacja |Internet Explorer |
| **Internet Explorer**: ulubione |synchronizacja |synchronizacja |Internet Explorer |
| **Internet Explorer**: wykluczone adresy URL |synchronizacja |synchronizacja |Internet Explorer |
| **Internet Explorer**: strony główne |synchronizacja |synchronizacja |Internet Explorer |
| **Internet Explorer**: sugestie dotyczące domen |synchronizacja |synchronizacja |Internet Explorer |
| **Klawiatura:** użytkownicy mogą włączać/wyłączać klawiaturę ekranową |synchronizacja |X |ułatwienia dostępu |
| **Klawiatura:** włącz lepkie tak (domyślnie wyłączone) |synchronizacja |X |ułatwienia dostępu |
| **Klawiatura:** włączanie klawiszy filtrów (domyślnie wyłączone) |synchronizacja |X |ułatwienia dostępu |
| **Klawiatura:** włączanie klawiszy przełączania (domyślnie wyłączone) |synchronizacja |X |ułatwienia dostępu |
| **Internet Explorer**: domena Język: chiński (CHS) QWERTY - umożliwia samodzielne uczenie się |synchronizacja |X |Język |
| **Język**: CHS QWERTY - włącz dynamiczny ranking kandydatów |synchronizacja |X |Język |
| **Język**: CHS QWERTY - char-set Simplified Chinese |synchronizacja |X |Język |
| **Język**: CHS QWERTY - char-set Chiński tradycyjny |synchronizacja |X |Język |
| **Język**: CHS QWERTY - fuzzy pinyin |synchronizacja |kopia zapasowa |Język |
| **Język**: CHS QWERTY - rozmyte pary |synchronizacja |kopia zapasowa |Język |
| **Język**: CHS QWERTY - full pinyin |synchronizacja |X |Język |
| **Język**: CHS QWERTY - double pinyin |synchronizacja |X |Język |
| **Język**: CHS QWERTY - odczyt automatycznej korekcji |synchronizacja |X |Język |
| **Język**: CHS QWERTY - C/E switch key, shift |synchronizacja |X |Język |
| **Język**: CHS QWERTY - C/E switch key, Ctrl |synchronizacja |X |Język |
| **Język**: CHS WUBI - tryb wprowadzania pojedynczego znaku |synchronizacja |X |Język |
| **Język**: CHS WUBI - pokaż pozostałe kodowanie kandydata |synchronizacja |X |Język |
| **Język**: CHS WUBI - sygnał dźwiękowy, gdy kodowanie 4 jest nieprawidłowe |synchronizacja |X |Język |
| **Język**: CHT Bopomofo - obejmują CJK Ext-A |synchronizacja |X |Język |
| **Język**: JapońskiE IME - predykcyjne pisanie i niestandardowe słowa |synchronizacja |synchronizacja |Język |
| **Język**: Koreański (KOR) IME |X |X |Język |
| **Język**: rozpoznawanie pisma ręcznego |X |X |Język |
| **Język**: profil językowy |synchronizacja |kopia zapasowa |Język |
| **Język**: sprawdzanie pisowni - autokorekta i zaznaczanie błędów ortograficznych |synchronizacja |kopia zapasowa |Język |
| **Język**: lista klawiatur |synchronizacja |kopia zapasowa |Język |
| **Ekran blokady:** wszystkie ustawienia ekranu blokady |X |X | |
| **Lupa: włączanie**lub wyłączanie (przełącznik główny) |X |X |Łatwość dostępu |
| **Lupa:** włączanie lub wyłączanie koloru inwersji (domyślnie wyłączone) |synchronizacja |X |Łatwość dostępu |
| **Lupa**: śledzenie - postępuj zgodnie z fokusem klawiatury |synchronizacja |X |Łatwość dostępu |
| **Lupa**: śledzenie - podążaj za kursorem myszy |synchronizacja |X |Łatwość dostępu |
| **Lupa:** rozpoczyna się, gdy użytkownicy logują się (domyślnie wyłączają się) |synchronizacja |X |Łatwość dostępu |
| **Mysz**: zmienia rozmiar kursora myszy |synchronizacja |X |inne |
| **Mysz**: zmiana koloru kursora myszy |synchronizacja |X |inne |
| **Mysz**: wszystkie inne ustawienia |X |X | |
| **Narrator**: szybkie uruchamianie |synchronizacja |X |Łatwość dostępu |
| **Narrator**: użytkownicy mogą zmieniać ton mówienia Narratora |synchronizacja |X |Łatwość dostępu |
| **Narrator**: użytkownicy mogą włączać lub wyłączać wskazówki do czytania narratora dla typowych elementów (domyślnie włączone) |synchronizacja |X |Łatwość dostępu |
| **Narrator**: użytkownicy mogą włączać lub wyłączać, czy słyszą wpisane znaki (domyślnie włączone) |synchronizacja |X |Łatwość dostępu |
| **Narrator**: użytkownicy mogą włączać czy wyłączać, czy słyszą wpisane słowa (domyślnie włączone) |synchronizacja |X |Łatwość dostępu |
| **Narrator**: mają kursor wstawiania po Narratorze (domyślnie włączony) |synchronizacja |X |Łatwość dostępu |
| **Narrator**: włączanie wizualnego wyróżniania kursora Narratora (domyślnie włączone) |synchronizacja |X |Łatwość dostępu |
| **Narrator**: odtwarzanie sygnałów dźwiękowych (domyślnie włączone) |synchronizacja |X |Łatwość dostępu |
| **Narrator:** włączanie klawiszy na klawiaturze dotykowej po podniesieniu palca (domyślnie wyłączone) |synchronizacja |X |Łatwość dostępu |
| **Łatwość dostępu**: ustawianie grubości migającego kursora |synchronizacja |X |Łatwość dostępu |
| **Łatwość dostępu:** usuwanie obrazów tła (domyślnie wyłączone) |synchronizacja |X |Łatwość dostępu |
| **Zasilanie i uśpienie:** wszystkie ustawienia |X |X | |
| **Personalizacja ekranu startowego:** kolor akcentu (tylko telefon) |X |synchronizacja |motyw |
| **Pisanie :** słownik pisowni |synchronizacja |kopia zapasowa |Język |
| **Wpisywanie :** autokorekta błędnie napisane słowo |synchronizacja |kopia zapasowa |Język |
| **Pisanie :** wyróżnianie błędnie napisanych wyrazów |synchronizacja |kopia zapasowa |Język |
| **Pisanie : pokazywanie**sugestii tekstowych podczas pisania |synchronizacja |kopia zapasowa |Język |
| **Pisanie :** dodawanie spacji po wybraniu sugestii tekstowej |synchronizacja |kopia zapasowa |Język |
| **Pisanie :** dodawanie kropki po dwukrotnym dotknięciu spacji |synchronizacja |kopia zapasowa |Język |
| **Wpisywanie**: wielkie litery każdego zdania |synchronizacja |kopia zapasowa |Język |
| Pisanie : **używaj**wszystkich wielkich liter, gdy naciskam klawiszem shift |synchronizacja |kopia zapasowa |Język |
| Pisanie : **odtwarzanie**dźwięków klawiszy podczas pisania |synchronizacja |kopia zapasowa |Język |
| **Pisanie :** dane personalizacji klawiatury dotykowej |synchronizacja |kopia zapasowa |Język |
| **Wi-Fi**: Profile Wi-Fi (tylko WPA) |synchronizacja |synchronizacja |Hasła |

###### <a name="footnote-1"></a>Przypis 1

Minimalna obsługiwana wersja systemu operacyjnego Windows Creators Update (Kompilacja 15063). 

## <a name="next-steps"></a>Następne kroki

Aby uzyskać omówienie, zobacz [omówienie roamingu stanu przedsiębiorstwa](enterprise-state-roaming-overview.md).
