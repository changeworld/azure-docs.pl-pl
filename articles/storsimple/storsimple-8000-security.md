---
title: StorSimple 8000 series zabezpieczeń | Dokumentacja firmy Microsoft
description: W tym artykule opisano funkcje zabezpieczeń i prywatności, które chronią usługi StorSimple, urządzenia i danych lokalnie i w chmurze.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/18/2018
ms.author: alkohli
ms.openlocfilehash: 734b0cf9373ea98ab33c06b45ad53b46a3355dd6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62117029"
---
# <a name="storsimple-security-and-data-protection"></a>Usługa StorSimple zabezpieczeń i ochrony danych

## <a name="overview"></a>Omówienie

Zabezpieczenia są głównym problemem dla każdego, kto wdraża nowe technologie, szczególnie w przypadku, gdy jest to technologia używana przy użyciu danych poufnych lub zastrzeżonych. Jak można ocenić różne technologie, należy wziąć pod uwagę zwiększone ryzyko i koszty związane z ochroną danych. Usługa Microsoft Azure StorSimple oferuje zarówno rozwiązanie zabezpieczenia i ochrona prywatności dotyczące ochrony danych w celu zapewnienia:

* **Poufność** — tylko do autoryzowanych jednostek mogą wyświetlać dane.
* **Integralność** — tylko autoryzowanym jednostki można zmodyfikować lub usunąć swoje dane.

Rozwiązanie Microsoft Azure StorSimple składa się z czterech głównych składników, które współdziałają ze sobą:

* **Usługa Menedżer urządzeń StorSimple hostowanych na platformie Microsoft Azure** — Usługa zarządzania, która służy do konfigurowania i aprowizacji urządzenia StorSimple.
* **Urządzenie StorSimple** — urządzenie fizyczne zainstalowana w Twoim centrum danych. Wszystkie hosty i klientów, które generują dane połączyć się z urządzeniem StorSimple, urządzenie i zarządza danymi przenosi je do chmury platformy Azure odpowiednio.
* **Klienci/hosty są połączone z urządzeniem** — klientów w infrastrukturze, które łączą się z urządzeniem StorSimple oraz generować dane, które muszą być chronione.
* **Magazyn w chmurze** — lokalizacja w chmurze platformy Azure, w której są przechowywane dane.

W poniższych sekcjach opisano funkcje zabezpieczeń StorSimple, które pomagają w ochronie każdego z tych składników oraz danych przechowywanych na nich. Zawiera także listę pytań, które mogą się pojawić w zabezpieczeń Microsoft Azure StorSimple i odpowiednich odpowiedzi.

## <a name="storsimple-device-manager-service-protection"></a>Ochrona usługi Menedżer urządzeń StorSimple

Usługa Menedżer urządzeń StorSimple to usługa zarządzania hostowanych na platformie Microsoft Azure i umożliwia zarządzanie wszystkimi urządzeniami StorSimple, które Twoja organizacja ma zakupu. Przy użyciu poświadczeń organizacyjnych do logowania się do witryny Azure portal za pośrednictwem przeglądarki sieci web, uzyskać dostęp do usługi Menedżer urządzeń StorSimple.

Dostęp do usługi Menedżer urządzeń StorSimple wymaga, że Twoja organizacja ma subskrypcję platformy Azure, która obejmuje usługi StorSimple. Subskrypcja określa funkcje, do których masz dostęp w witrynie Azure Portal. Jeśli Twoja organizacja ma subskrypcję platformy Azure i chcesz dowiedzieć się więcej o nich, zobacz [konta na platformie Azure jako organizacja](../active-directory/fundamentals/sign-up-organization.md).

Ponieważ usługa Menedżer urządzeń StorSimple jest hostowana na platformie Azure, jest chroniony przy użyciu funkcji zabezpieczeń platformy Azure. Aby uzyskać więcej informacji na temat funkcji zabezpieczeń platformy Microsoft Azure, zobacz [Centrum zaufania Microsoft Azure](https://azure.microsoft.com/support/trust-center/security/).

## <a name="storsimple-device-protection"></a>Ochrona urządzeń StorSimple

Urządzenie StorSimple jest urządzenie magazynujące hybrydowych w środowisku lokalnym, który zawiera dyski półprzewodnikowe (SSD) i dysków twardych (HDD), nadmiarowe kontrolery i funkcje automatycznej pracy awaryjnej. Kontrolery zarządzania magazynem warstw, umieszczania aktualnie używane (lub gorąca) danych w magazynie lokalnym (w StorSimple urządzenia lub lokalnych serwerów), podczas przenoszenia rzadziej używanych danych do chmury.

Tylko autoryzowanym StorSimple, urządzenia będą mogły do dołączenia do usługi Menedżer urządzeń StorSimple, który został utworzony w ramach subskrypcji platformy Azure. Aby autoryzować urządzenia, należy zarejestrować go za pomocą usługi Menedżer urządzeń StorSimple, zapewniając klucz rejestracji usługi. Klucz rejestracji usługi jest 128-bitowego losowy klucz generowany w witrynie Azure portal.

![klucz rejestracji usługi](./media/storsimple-security/ServiceRegistrationKey.png)

Aby dowiedzieć się, jak uzyskać klucz rejestracji usługi, przejdź do [krok 2: Pobierz klucz rejestracji usługi](storsimple-8000-deployment-walkthrough-u2.md#step-2-get-the-service-registration-key).

Klucz rejestracji usługi jest długa klucz, który zawiera ponad 100 znaków. Można skopiować klucz i zapisz go w pliku tekstowym w bezpiecznej lokalizacji, aby można go autoryzować dodatkowych urządzeń zgodnie z potrzebami. Jeśli klucz rejestracji usługi zostaną utracone po zarejestrowaniu pierwszego urządzenia, należy wygenerować nowy klucz w usłudze Menedżer urządzeń StorSimple. Nie ma to wpływu na działanie istniejących urządzeń.

Po zarejestrowaniu urządzenia, używane są tokeny do komunikowania się z platformą Microsoft Azure. Klucz rejestracji usługi nie jest używany po zarejestrowaniu urządzenia.

> [!NOTE]
> Firma Microsoft zaleca ponowne generowanie klucza rejestracji usługi po każdym użyciu.


## <a name="protect-your-storsimple-solution-via-passwords"></a>Ochrona rozwiązania StorSimple przy użyciu haseł

Hasła są istotnym elementem zabezpieczenia komputera i są często używane w ramach rozwiązania StorSimple aby mieć pewność, że Twoje dane są dostępne tylko dla autoryzowanych użytkowników. Magazyn StorSimple pozwala skonfigurować następujące hasła:

* Hasło administratora urządzenia StorSimple
* Rzuć wyzwanie hasła inicjatora i docelowy protokołu uwierzytelniania Handshake (CHAP)
* Hasło programu StorSimple Snapshot Manager

### <a name="windows-powershell-for-storsimple-and-the-storsimple-device-administrator-password"></a>Program Windows PowerShell dla magazynu StorSimple i hasło administratora urządzenia StorSimple

Program Windows PowerShell dla usługi StorSimple jest interfejs wiersza polecenia, który służy do zarządzania urządzeniem StorSimple. Program Windows PowerShell dla usługi StorSimple zawiera funkcje, które umożliwiają zarejestrować urządzenie, skonfiguruj interfejs sieciowy na urządzeniu, zainstalować niektórych typów aktualizacji, problemów z urządzeniem, uzyskując dostęp do sesji pomocy technicznej i zmiany stanu urządzenia. Program Windows PowerShell dostęp dla urządzenia StorSimple przez łączenie z konsolą szeregową urządzenia lub przy użyciu komunikacji zdalnej programu Windows PowerShell.

Komunikacja zdalna programu PowerShell może odbywać się za pośrednictwem protokołu HTTP lub HTTPS. Jeśli włączono zdalne zarządzanie przy użyciu protokołu HTTPS, należy pobrać certyfikat zdalnego zarządzania z urządzenia i zainstalować ją na kliencie zdalnym. Aby uzyskać więcej informacji na temat komunikacji zdalnej programu PowerShell, przejdź do [nawiązywanie połączenia zdalnego urządzenia StorSimple](storsimple-8000-remote-connect.md).

Po nawiązania połączenia z urządzeniem przy użyciu programu Windows PowerShell dla usługi StorSimple, należy podać hasło administratora urządzenia do logowania się do urządzenia.

![Hasło administratora urządzenia](./media/storsimple-security/DeviceAdminPW.png)

Należy pamiętać poniższe najlepsze rozwiązania:

* Zdalne zarządzanie jest domyślnie wyłączona. Usługi Menedżer urządzeń StorSimple można użyć, aby go włączyć. Ze względów bezpieczeństwa dostępu zdalnego powinna być włączona tylko podczas okres czasu, który jest faktycznie potrzebny.
* Jeśli zmienisz hasło, pamiętaj powiadomić wszystkich użytkowników dostępu zdalnego tak, aby nie będą dotykać utracie łączności nieoczekiwany.
* Usługa Menedżer urządzeń StorSimple nie można pobrać istniejących haseł: tylko można zresetować je. Zaleca się przechowywanie wszystkie hasła w bezpiecznym miejscu, tak, aby nie trzeba zresetować hasło, zapomnienia jest. Jeśli potrzebujesz zresetować hasło, pamiętaj powiadomić wszystkich użytkowników, zanim je zresetować.

Za pomocą połączenia szeregowego do urządzenia mogą uzyskać dostęp do interfejsu programu Windows PowerShell. Możesz również do niego dostęp zdalnie przy użyciu protokołu HTTP lub HTTPS, które zapewniają dodatkowe zabezpieczenia. Protokół HTTPS oferuje wyższy poziom zabezpieczeń niż seryjny lub połączenia HTTP. Jednak do używania protokołu HTTPS, użytkownik musi najpierw zainstalować certyfikat na komputerze klienckim, który będzie miał dostęp urządzenia. Na stronie konfiguracji urządzenia w usłudze Menedżer urządzeń StorSimple, możesz pobrać certyfikat dostępu zdalnego. W przypadku utraty certyfikatu do dostępu zdalnego należy pobrać nowy certyfikat i jego są propagowane do wszystkich klientów, które są autoryzowane do używania zdalnego zarządzania.

### <a name="challenge-handshake-authentication-protocol-chap-initiator-and-target-passwords"></a>Rzuć wyzwanie hasła inicjatora i docelowy protokołu uwierzytelniania Handshake (CHAP)

Protokół CHAP jest schematu uwierzytelniania używany przez urządzenia StorSimple do weryfikacji tożsamości klientów zdalnych. Weryfikacja opiera się na wspólne hasło. Protokół CHAP mogą być jednokierunkowe (jednokierunkowe) lub wzajemne (dwukierunkowe). Przy użyciu jednokierunkowego CHAP inicjatora (host) uwierzytelniany element docelowy (urządzenia StorSimple). Wzajemnego lub wstecznego protokołu CHAP wymaga, aby obiekt docelowy uwierzytelniania inicjatora, a następnie inicjatora uwierzytelniania obiektu docelowego. Usługi StorSimple można skonfigurować tak, aby użyć jednej z metod.

Konfigurowanie protokołu CHAP, należy pamiętać o następujących:

* Nazwa użytkownika protokołu CHAP musi zawierać mniej niż 233 znaków.
* Hasło CHAP musi mieć długość od 12 do 16 znaków. Podjęto próbę użyć dłuższej nazwy użytkownika lub hasła spowoduje niepowodzenie uwierzytelniania na hoście Windows.
* Nie można użyć tego samego hasła, zarówno dla inicjatora protokołu CHAP i obiektu docelowego protokołu CHAP.
* Po ustawieniu hasła, można go zmienić, ale nie można pobrać. Jeśli hasło zostało zmienione, pamiętaj powiadomić wszystkich użytkowników dostępu zdalnego, aby pomyślnie umożliwić im połączenie z urządzeniem StorSimple.

Aby uzyskać więcej informacji dotyczących protokołu CHAP i sposobie konfigurowania go dla rozwiązania StorSimple, przejdź do [Konfigurowanie protokołu CHAP dla urządzenia StorSimple](storsimple-8000-configure-chap.md).

### <a name="storsimple-snapshot-manager-password"></a>Hasło programu StorSimple Snapshot Manager

Przystawki StorSimple Snapshot Manager jest przystawką Microsoft Management Console (MMC), korzystającą z grupami woluminów i usługi kopiowania w tle woluminów Windows do generowania spójnych z aplikacją kopii zapasowych. Ponadto można użyć przystawki StorSimple Snapshot Manager do tworzenia harmonogramów tworzenia kopii zapasowych oraz klonowania lub Przywróć woluminy.

Po skonfigurowaniu na urządzeniu za pomocą Menedżera migawek StorSimple, trzeba będzie podać hasło programu StorSimple Snapshot Manager. To hasło jest najpierw ustawić w programie Windows PowerShell dla usługi StorSimple podczas rejestracji. Można również ustawić hasło i zmieniła się z usługą Menedżera urządzeń StorSimple. To hasło uwierzytelnia urządzenie za pomocą przystawki StorSimple Snapshot Manager.

![Hasło programu StorSimple Snapshot Manager](./media/storsimple-security/SnapshotMgrPassword.png)

Hasło programu StorSimple Snapshot Manager musi mieć od 14 do 15 znaków i musi zawierać 3 lub więcej z kombinacji wielkie litery, małe litery, cyfry i znaki specjalne. Po ustawieniu hasło programu StorSimple Snapshot Manager można zmienić, ale nie można pobrać. Jeśli zmienisz hasło, pamiętaj powiadomić wszystkich użytkowników zdalnych.

Aby uzyskać więcej informacji na temat programu StorSimple Snapshot Manager, przejdź do [co to jest StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

### <a name="password-best-practices"></a>Najlepsze rozwiązania dotyczące haseł

Firma Microsoft zaleca, skorzystaj z poniższych wskazówek w celu zapewnienia, że haseł usługi StorSimple silnych i dobrze chronione:

* Zmień hasła co trzy miesiące. Zmiana hasła jest wymuszana rocznie.
* Należy używać silnych haseł. Aby uzyskać więcej informacji, przejdź do [tworzyć silniejsze hasła i chronić je](https://cloudblogs.microsoft.com/microsoftsecure/2014/08/25/create-stronger-passwords-and-protect-them/).
* Zawsze używaj różnych haseł dla dostępu z różnych mechanizmów; Każdy z haseł, które określisz powinny być unikatowe.
* Nie należy udostępniać hasła każdemu użytkownikowi, który nie ma uprawnień do uzyskania dostępu do urządzenia StorSimple.
* Nie mówić o hasło przed innymi ani nie podpowiedzi na format hasła.
* Jeśli podejrzewasz, że konto lub hasło bezpieczeństwo zostało naruszone, należy zgłosić zdarzenie, aby dział bezpieczeństwa informacji.
* Wszystkie hasła należy traktować jako wielkość liter, poufne informacje. 

## <a name="storsimple-data-protection"></a>Ochrona danych usługi StorSimple

W tej sekcji opisano funkcje zabezpieczeń usługi StorSimple, służących do ochrony danych przesyłanych i przechowywanych danych.

Zgodnie z opisem w innych częściach, hasła są używane do autoryzowanie i uwierzytelnianie użytkowników, zanim uzyskają dostęp do rozwiązania StorSimple. Inną ważną kwestią zabezpieczeń ochrony danych przed nieautoryzowanymi użytkownikami, podczas gdy są przesyłane między systemami magazynowania i gdy jest magazynowana. W poniższych sekcjach opisano funkcje ochrony danych, które są dostarczane za pomocą usługi StorSimple.

> [!NOTE]
> Funkcja deduplikacji udostępnia dodatkową ochronę danych przechowywanych na urządzeniu StorSimple i usługi Microsoft Azure storage. Gdy dane są deduplikowane, obiekty danych są przechowywane oddzielnie od metadane używane do mapowania i uzyskiwać do nich dostęp: Brak dostępnych kontekstu poziom miejsca do magazynowania można odtworzyć dane na podstawie struktury woluminu, system plików lub nazwę pliku.


## <a name="protect-data-flowing-through-the-service"></a>Chroń dane przepływają przez usługę

Głównym celem usługi Menedżer urządzeń StorSimple jest zarządzanie i konfigurowanie urządzenia StorSimple. Usługa Menedżer urządzeń StorSimple jest uruchamiana w systemie Microsoft Azure. Wprowadź dane konfiguracji urządzenia za pomocą witryny Azure portal, a następnie Microsoft Azure korzysta z usługi Menedżer urządzeń StorSimple do wysyłania danych do urządzenia. Usługa StorSimple używa systemu par kluczy asymetrycznych, aby mieć pewność, że naruszenia zabezpieczeń usługi Azure nie powoduje naruszenie przechowywane informacje.

![Szyfrowanie danych w locie](./media/storsimple-security/DataEncryption.png)

System klucza asymetrycznego pomaga w ochronie danych przesyłanych za pośrednictwem usługi:

1. Certyfikat szyfrowania danych, który używa publicznego i prywatnego klucza asymetrycznego pary jest generowany na urządzeniu i jest używana do ochrony danych. Klucze są generowane podczas rejestrowania pierwszego urządzenia.
2. Klucze certyfikatu szyfrowania danych są eksportowane do pliku wymiany informacji osobistych (pfx), który jest chroniony przez klucz szyfrowania danych usługi, która jest silnego klucza 128-bitowego, losowo wygenerowany przez pierwsze urządzenie podczas rejestracji.
3. Klucz publiczny certyfikatu jest bezpiecznie udostępniany w usłudze Menedżer urządzeń StorSimple, a klucz prywatny pozostaje z urządzeniem.
4. Data rozpoczęcia pracy są szyfrowane przy użyciu publicznego klucza i odszyfrowane przy użyciu klucza prywatnego przechowywanych na urządzeniu, zapewniając, że usługi platformy Azure nie można odszyfrować danych przepływających do urządzenia.

Klucz szyfrowania danych usługi jest generowany na tylko pierwsze urządzenie zarejestrowane w usłudze. Wszystkie kolejne urządzenia, które są zarejestrowane w usłudze, należy użyć tego samego klucza szyfrowania danych usługi.

> [!IMPORTANT]
> Jest to bardzo ważne, aby utworzyć kopię klucza szyfrowania danych usługi i zapisz go w bezpiecznym miejscu. Kopię klucza szyfrowania danych usługi powinny być przechowywane w taki sposób, że może zostać oceniony przez osobę upoważnioną i może być łatwo przekazywane administratora urządzenia.
> 
> W przypadku utraty klucza szyfrowania danych usługi pomocy technicznej firmy Microsoft mogą ułatwić go pobrać, pod warunkiem, że masz co najmniej jedno urządzenie w stanie online. Firma Microsoft zaleca, możesz zmienić klucz szyfrowania danych usługi po jej pobraniu.

Aby zmienić klucz szyfrowania danych usługi i odpowiedni certyfikat szyfrowania danych, wykonaj kroki opisane w [Zmień klucz szyfrowania danych usługi dla usługi Menedżer urządzeń StorSimple](storsimple-8000-manage-service.md#change-the-service-data-encryption-key). Zmiana kluczy szyfrowania wymaga, aby wszystkie urządzenia można zaktualizować przy użyciu nowego klucza. Dlatego zaleca się zmiany klucza, jeśli wszystkie urządzenia są w trybie online. Jeśli urządzenia są w trybie offline, można zmienić ich klucze w innym czasie. Urządzenia z nieaktualnymi kluczy nadal będzie można wykonywać kopie zapasowe, ale nie będzie mógł przywrócić dane do czasu zaktualizowania klucza.

Klucz szyfrowania danych usługi i certyfikat szyfrowania danych nie wygasa. Jednak zaleca się zmiany klucza szyfrowania danych usługi, co roku, aby zapobiec złamania klucza.

## <a name="protect-data-at-rest"></a>Ochrona magazynowanych danych

Urządzenie StorSimple zarządza danymi dzięki przechowywaniu go w warstwach, lokalnie i w chmurze, w zależności od częstości używania. Wszystkie maszyny hosta, które są połączone z urządzeniem wysyłać dane do urządzenia, które przenosi dane w chmurze, zgodnie z potrzebami. Dane są przesyłane z urządzenia do chmury bezpiecznie za pośrednictwem Internetu. Każde urządzenie ma jeden obiekt docelowy iSCSI, który wyświetla wszystkie udostępnione woluminy na tym urządzeniu. Wszystkie dane są szyfrowane przed wysłaniem go do magazynu w chmurze. 

![klucz szyfrowania magazynu w chmurze](./media/storsimple-security/CloudStorageEncryption.png)

Aby ułatwić zapewnienie bezpieczeństwa i integralności danych przeniesione do chmury, StorSimple umożliwia definiowanie kluczy szyfrowania magazynu w chmurze w następujący sposób:

* Klucz szyfrowania magazynu w chmurze można określić po utworzeniu kontenera woluminów. Klucz nie może być zmodyfikowanie lub dodanie później.
* Wszystkie woluminy w kontenerze woluminów współużytkować ten sam klucz szyfrowania. Jeśli chcesz, aby różne formy szyfrowania dla określonego woluminu, zaleca się tworzenia nowego kontenera woluminów w celu obsługi tego woluminu.
* Po wprowadzeniu klucz szyfrowania magazynu w chmurze w usłudze Menedżer urządzeń StorSimple, klucz jest zaszyfrowany przy użyciu publicznej części klucza szyfrowania danych usługi, a następnie wysyłane do urządzenia.
* Klucz szyfrowania magazynu w chmurze nie są przechowywane w dowolnym miejscu w usłudze a jest znane tylko na urządzeniu.
* Określenie klucz szyfrowania magazynu w chmurze jest opcjonalne. Umożliwia wysyłanie danych, które zostały zaszyfrowane na hoście, na urządzeniu.

### <a name="additional-security-best-practices"></a>Najlepsze rozwiązania dotyczące zabezpieczeń dodatkowe

* Dzielenie ruchu: izolowania sieci SAN iSCSI ruchu użytkowników w sieci lokalnej firmy przez wdrożenie całkowicie rozdzielonych sieci i przy użyciu sieci VLAN, gdzie izolacja na fizycznego nie jest opcją. Dedykowanej sieci w przypadku magazynu iSCSI gwarantuje bezpieczeństwa i wydajności danych o kluczowym znaczeniu dla firmy. Mieszanie ruch magazynu i użytkownika w firmowej sieci LAN nie jest zalecane, można zwiększyć opóźnienie i powodować awarie sieci.
* W przypadku zabezpieczeń sieci po stronie hosta Użyj interfejsów sieciowych, które obsługują protokół TCP/IP odciążania aparatu (KÓŁKO). KÓŁKO zmniejsza obciążenie procesora CPU przez przetwarzanie protokołu TCP dla karty sieciowej.

## <a name="protect-data-via-storage-accounts"></a>Ochrona danych za pomocą konta magazynu

Każda subskrypcja Microsoft Azure można utworzyć co najmniej jedno konto magazynu. (Konto magazynu zapewnia unikatową przestrzeń nazw do pracy z danymi przechowywanymi w chmurze platformy Azure). Dostęp do konta magazynu jest kontrolowana przez subskrypcję i kluczy dostępu skojarzone z tym kontem magazynu.

Podczas tworzenia konta magazynu Microsoft Azure generuje dwa klucze dostępu do magazynu 512-bitowy, z których jeden jest używany do uwierzytelniania, gdy urządzenie StorSimple uzyskuje dostęp do konta magazynu. Należy zauważyć, że tylko jeden z tych kluczy jest używany. Drugi klucz jest przechowywany w rezerwie, dzięki czemu możesz okresowo wymiany kluczy. Wymiany kluczy, uaktywnić klucza pomocniczego, a następnie usuń klucz podstawowy. Następnie można utworzyć nowego klucza do użycia podczas kolejnej rotacji. (Ze względów bezpieczeństwa wiele centrów danych wymagają wymiany kluczy).

Firma Microsoft zaleca, aby wykonać te najlepsze praktyki dotyczące wymiany kluczy:

* Należy obrócić kluczy konta magazynu, regularnie, aby pomóc w zapewnieniu, że konto magazynu nie jest używane przez nieautoryzowanych użytkowników.
* Okresowo administratorem platformy Azure, należy zmienić lub ponownie wygenerować klucz podstawowy lub pomocniczy przy użyciu sekcji magazyn w witrynie Azure Portal na bezpośredni dostęp do konta magazynu.

## <a name="protect-data-via-encryption"></a>Ochrona danych za pomocą szyfrowania

Usługa StorSimple używa następujących algorytmów szyfrowania w celu ochrony danych przechowywanych w podróży między składnikami rozwiązania StorSimple.

| Algorytm | Długość klucza | Protokoły/applications/komentarzy |
| --- | --- | --- |
| RSA |2048 |RSA PKCS 1 w wersji 1.5 służy do szyfrowania danych konfiguracji, które są wysyłane do urządzenia w portalu Azure: na przykład magazyn poświadczeń, konfiguracji urządzenia StorSimple, konta i klucze szyfrowania magazynu w chmurze. |
| AES |256 |AES z CBC jest używany do szyfrowania publicznej części klucza szyfrowania danych usługi, przed wysłaniem go do witryny Azure portal z urządzenia StorSimple. Również służy przez urządzenia StorSimple do szyfrowania danych przed wysłaniem danych do konta magazynu w chmurze. |

## <a name="storsimple-cloud-appliance-security"></a>Bezpieczeństwo urządzenia StorSimple w chmurze

[!INCLUDE [storsimple Cloud Appliance security](../../includes/storsimple-virtual-device-security.md)]

## <a name="managing-personal-information"></a>Zarządzanie informacji osobistych

Menedżer urządzeń StorSimple dla serii fizycznych i wirtualnych zbiera informacje osobiste w następujących przypadkach klucza:

- Zgłoś alert, ustawienia użytkownika, w którym skonfigurowano adres e-mail użytkowników. Te informacje można wyświetlać i wyczyszczone przez administratora. Dotyczy to zarówno urządzeń z serii StorSimple 8000 oraz macierzy wirtualnych StorSimple.
  * Aby wyświetlić i wyczyścić ustawienia dla serii StorSimple 8000, wykonaj kroki opisane w [widoku StorSimple alerty i zarządzaj nimi](storsimple-8000-manage-alerts.md#configure-alert-settings)
  * Aby wyświetlić i wyczyścić ustawienia dla rozwiązania StorSimple Virtual Array, wykonaj kroki opisane w [widoku StorSimple alerty i zarządzaj nimi](storsimple-virtual-array-manage-alerts.md#configure-alert-settings)
- Użytkownicy, którzy mają dostęp do danych znajdujących się w udziałach. Lista użytkowników, którzy mają dostęp do danych udziału jest wyświetlany i mogą być wyświetlane. Ta lista są także usuwane w momencie usunięcia akcji. Dotyczy to tylko macierze wirtualne StorSimple.
  * Aby wyświetlić listę użytkowników, kto może uzyskać dostęp lub aby usunąć udział, wykonaj kroki opisane w [Zarządzanie udziałami w ramach macierzy wirtualnej StorSimple](storsimple-virtual-array-manage-shares.md)

Aby uzyskać więcej informacji, zapoznaj się z Zasadami ochrony prywatności firmy Microsoft w [Centrum zaufania](https://www.microsoft.com/trustcenter).

## <a name="frequently-asked-questions-faq"></a>Często zadawane pytania

Poniżej przedstawiono niektóre pytania i odpowiedzi na pytania dotyczące bezpieczeństwa i Microsoft Azure StorSimple.

**Pyt.:** Moja usługa zostanie naruszone. Czymś co powinno być następne kroki należy wykonać?

**Odp.:** Natychmiast należy zmienić klucz szyfrowania danych usługi i klucze konta magazynu dla konta magazynu, który jest używany w przypadku warstw danych. Aby uzyskać instrukcje przejdź do:

* [Zmień klucz szyfrowania danych usługi](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [Wymiana kluczy kont magazynu](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts)

**Pyt.:** Masz nowe urządzenie StorSimple, który pyta o klucz rejestracji usługi. Jak pobrać go

**Odp.:** Ten klucz został utworzony podczas pierwszego utworzenia usługi Menedżer urządzeń StorSimple. Korzystając z usługi Menedżer urządzeń StorSimple do nawiązania połączenia z urządzeniem, można użyć strony szybki start usługi, aby wyświetlić lub ponownie wygenerować klucz rejestracji usługi. Generowanie nowego klucza rejestracji usługi nie wpłyną na istniejące zarejestrowane urządzenia. Aby uzyskać instrukcje przejdź do:

* [Wyświetlić lub ponownie wygenerować klucz rejestracji usługi](storsimple-8000-manage-service.md##regenerate-the-service-registration-key)

**Pyt.:** Utratą mój klucz szyfrowania danych usługi. Co mam zrobić?

**Odp.:** Contact Microsoft Support. Mogą oni się zalogować do sesji pomocy technicznej na urządzeniu i pomocy pobierania klucza (pod warunkiem co najmniej jedno urządzenie jest w trybie online). Po uzyskaniu klucza szyfrowania danych usługi, należy zmienić go, aby upewnić się, że nowy klucz jest znane tylko dla Ciebie. Aby uzyskać instrukcje przejdź do:

* [Zmień klucz szyfrowania danych usługi](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)

**Pyt.:**  Autoryzacji urządzenia dla usługi zmiany klucza szyfrowania danych, ale nie zostały uruchomione proces zmiany klucza. Co mam zrobić?

**Odp.:** Jeśli upłynął limit czasu, należy ponownie autoryzować urządzenia do zmiany klucza szyfrowania danych usługi, a następnie uruchom ponownie proces.

**Pyt.:**  Czy mogę zmienić klucza szyfrowania danych usługi, ale nie udało mi się aktualizacji na innych urządzeniach w ciągu 4 godzin. Czy muszę ponownie uruchomić?

**Odp.:** 4-godzinnego okresu jest tylko w przypadku inicjowania zmiany. Po uruchomieniu procesu aktualizacji na urządzeniu StorSimple autoryzowanych autoryzacji jest prawidłowy, dopóki wszystkie urządzenia są aktualizowane.

**Pyt.:** Nasze administratora usługi StorSimple opuścił firmę. Co mam zrobić?

**Odp.:** Zmień resetowania haseł, które umożliwiają dostęp do urządzenia StorSimple i Zmień klucz szyfrowania danych usługi, aby upewnić się, że nowych informacji nie jest znany nieautoryzowanym próbom. Aby uzyskać instrukcje przejdź do:

* [Zmienianie haseł usługi storsimple przy użyciu usługi Menedżer urządzeń StorSimple](storsimple-8000-change-passwords.md)
* [Zmień klucz szyfrowania danych usługi](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [Konfigurowanie protokołu CHAP dla urządzenia StorSimple](storsimple-8000-configure-chap.md)

**Pyt.:** Chcę podać hasło programu StorSimple Snapshot Manager do hosta, który nawiązuje połączenie z urządzeniem StorSimple, ale hasło nie jest dostępna. Co mogę zrobić?

**Odp.:** Jeśli pamiętasz hasła, należy utworzyć nową. Następnie Pamiętaj wszyscy istniejący użytkownicy informuje, że hasło zostało zmienione i czy powinny one aktualizowane swoich klientów do korzystania z nowego hasła. Aby uzyskać instrukcje przejdź do:

* [Zmień hasło programu StorSimple Snapshot Manager](storsimple-8000-change-passwords.md#set-the-storsimple-snapshot-manager-password)
* [Uwierzytelnienia urządzenia](storsimple-snapshot-manager-manage-devices.md#authenticate-a-device)

**Pyt.:** Certyfikat zdalny dostęp do programu Windows PowerShell dla usługi StorSimple została zmieniona na urządzeniu. Jak zaktualizować moich klientów dostępu zdalnego?

**Odp.:** Możesz pobrać nowy certyfikat z usługi Menedżer urządzeń StorSimple, a następnie podać go do zainstalowania w magazynie certyfikatów klientów dostępu zdalnego. Aby uzyskać instrukcje przejdź do:

* [Polecenia cmdlet Import-Certificate](https://docs.microsoft.com/powershell/module/pkiclient/import-certificate)

**Pyt.:** Jest Moje chronionych danych, jeśli zostanie naruszony usługi Menedżer urządzeń StorSimple?

**Odp.:** Dane konfiguracji usługi są zawsze szyfrowane kluczem publicznym, podczas wyświetlania w przeglądarce sieci web. Ponieważ usługa nie ma dostępu do klucza prywatnego, usługa nie będzie można zobaczyć wszystkie dane. W przypadku naruszenia zabezpieczeń usługi Menedżer urządzeń StorSimple nie ma to wpływu, ponieważ brak kluczy przechowywanych w usłudze Menedżer urządzeń StorSimple.

**Pyt.:** Jeśli osoba uzyska dostęp do certyfikatu szyfrowania danych, dane może stwarzać zagrożenie?

**Odp.:** Microsoft Azure przechowuje klucz szyfrowania danych przez klienta (plik pfx) w postaci zaszyfrowanej. Ponieważ plik PFX jest zaszyfrowany i usługę StorSimple nie ma klucza szyfrowania danych usługi do odszyfrowania pliku PFX, po prostu uzyskiwanie dostępu do pliku .pfx nie udostępni żadnych wpisów tajnych.

**Pyt.:** Co się stanie, jeśli jednostek administracji rządowej prosi firmy Microsoft dla moich danych?

**Odp.:** Ponieważ wszystkie dane są szyfrowane na komputerze usługi i klucz prywatny jest przechowywany z urządzeniem, jednostek administracji rządowej muszą uzyskać dane klienta.



## <a name="next-steps"></a>Kolejne kroki

[Wdrażanie urządzenia StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

