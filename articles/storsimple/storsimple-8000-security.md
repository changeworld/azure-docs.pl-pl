---
title: Zabezpieczenia serii StorSimple 8000 | Dokumentacja firmy Microsoft
description: Opisuje funkcje zabezpieczeń i prywatności, które zapewniają ochronę usługi StorSimple, urządzenia i danych lokalnie i w chmurze.
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
ms.openlocfilehash: 80cdd2bdad5962a9d5f6a0e20f34ac3db1942741
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34650941"
---
# <a name="storsimple-security-and-data-protection"></a>StorSimple zabezpieczeń i ochrony danych

## <a name="overview"></a>Przegląd

Zabezpieczeń jest głównym problemem dla każdego, kto wdraża to nowa technologia, szczególnie w przypadku technologii jest używany z danych poufnych lub zastrzeżonych. Jak można ocenić różnych technologii, należy wziąć pod uwagę zwiększone ryzyko i koszty związane z ochroną danych. Microsoft Azure StorSimple zapewnia zabezpieczeń i prywatności rozwiązania do ochrony danych w celu zapewnienia:

* **Poufność** — tylko do autoryzowanych jednostek można wyświetlić danych.
* **Integralność** — tylko autoryzowani jednostki można zmodyfikować lub usunąć dane.

Rozwiązanie Microsoft Azure StorSimple składa się z czterech głównych składników, które współdziałają ze sobą:

* **Menedżer urządzeń StorSimple usługi hostowanej na platformie Microsoft Azure** — usługę zarządzania, która służy do konfigurowania i obsługi administracyjnej urządzeniu StorSimple.
* **Urządzenia StorSimple** — urządzenia fizycznego zainstalowany w centrum danych. Wszystkie hosty i klientów, które generują dane nawiąż połączenie z urządzeniem StorSimple, urządzenie i zarządza danych przenosi je do chmury Azure jako odpowiednie.
* **Klienci/hosty są połączone z urządzeniem** — klientów w infrastrukturze, które łączą się z urządzeniem StorSimple i generować dane, które wymagają ochrony.
* **Magazyn w chmurze** — lokalizacji w chmurze Azure, w którym są przechowywane dane.

W poniższych sekcjach opisano funkcje zabezpieczeń StorSimple, pomagających w ochronie każdego z tych składników oraz danych przechowywanych w nich. Zawiera także listę pytania, na które może być o zabezpieczeniach Microsoft Azure StorSimple i odpowiednich odpowiedzi.

## <a name="storsimple-device-manager-service-protection"></a>Ochrona usługi Menedżer urządzenia StorSimple

Usługę Menedżer StorSimple urządzenia jest hostowana na platformie Microsoft Azure i używane do zarządzania wszystkimi urządzeniami StorSimple, które organizacja ma kupowane usługi zarządzania. Można uzyskać dostępu do usługi Menedżer StorSimple urządzenia przy użyciu poświadczeń w organizacji do logowania się do portalu Azure za pośrednictwem przeglądarki sieci web.

Dostęp do usługi Menedżer StorSimple urządzenia wymaga, że Twoja organizacja ma subskrypcję platformy Azure, która obejmuje StorSimple. Subskrypcja podlega funkcje, które są dostępne w portalu Azure. Jeśli Twoja organizacja nie ma subskrypcji platformy Azure i chcesz dowiedzieć się więcej o nich, zobacz [tworzenia konta platformy Azure jako organizacja](../active-directory/sign-up-organization.md).

Ponieważ usługi Menedżer StorSimple urządzenie znajduje się na platformie Azure, jest chroniona przez funkcje zabezpieczeń platformy Azure. Aby uzyskać więcej informacji na temat funkcji zabezpieczeń udostępniane przez Microsoft Azure, przejdź do [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/security/).

## <a name="storsimple-device-protection"></a>Ochrona urządzenia StorSimple

Urządzenia StorSimple jest lokalnym urządzeniem magazynującym hybrydowych, zawierający dysków półprzewodnikowych (SSD) i dysków twardych (HDD), wraz z nadmiarowe kontrolery i funkcji automatycznej pracy awaryjnej. Kontrolery zarządzania magazynem, dodając funkcje warstw, umieszczania aktualnie używane (lub gorących) danych w magazynie lokalnym (w StorSimple urządzenia lub lokalnych serwerów), podczas przenoszenia rzadziej używanych danych do chmury.

Wyłącznie autoryzowanych urządzeń mogą przyłączać usługi Menedżer urządzeń StorSimple, utworzony w ramach subskrypcji platformy Azure StorSimple. Aby autoryzować urządzenia, należy zarejestrować go w usłudze Menedżer StorSimple urządzenia zapewniając klucz rejestracji usługi. Klucz rejestracji usługi jest 128-bitowego losowy klucz wygenerowany w portalu Azure.

![klucz rejestracji usługi](./media/storsimple-security/ServiceRegistrationKey.png)

Aby dowiedzieć się, jak uzyskać klucz rejestracji usługi, przejdź do [krok 2: pobieranie klucza rejestracji usługi](storsimple-8000-deployment-walkthrough-u2.md#step-2-get-the-service-registration-key).

Klucz rejestracji usługi jest długa klucz, który zawiera znaki 100 +. Można skopiować klucz i zapisać go w pliku tekstowym w bezpiecznej lokalizacji, dzięki czemu można go używać do autoryzowania dodatkowych urządzeń w razie potrzeby. Jeśli klucz rejestracji usługi nie zostały utracone po zarejestrowaniu pierwszego urządzenia, można wygenerować nowy klucz w usłudze Menedżer StorSimple urządzenia. Nie wpłynie to na operację istniejących urządzeń.

Po zarejestrowaniu urządzenia korzysta tokenów do komunikowania się z Microsoft Azure. Po rejestracji urządzenie nie jest używany klucz rejestracji usługi.

> [!NOTE]
> Zaleca się ponowne generowanie klucza rejestracji usługi po każdym użyciu.


## <a name="protect-your-storsimple-solution-via-passwords"></a>Ochrona rozwiązania StorSimple przy użyciu hasła

Hasła są istotnym elementem zabezpieczenia komputera i są często używane w rozwiązaniu StorSimple pomaga zapewnić, że dane są dostępne tylko autoryzowanym użytkownikom. StorSimple można skonfigurować następujące hasła:

* Hasło administratora urządzenia StorSimple
* Żądanie hasła Inicjator i obiekt docelowy protokołu uwierzytelniania Handshake (CHAP)
* Hasło programu StorSimple Snapshot Manager

### <a name="windows-powershell-for-storsimple-and-the-storsimple-device-administrator-password"></a>Program Windows PowerShell dla StorSimple i hasło administratora urządzenia StorSimple

Program Windows PowerShell dla StorSimple jest interfejsu wiersza polecenia, który służy do zarządzania urządzeniem StorSimple. Program Windows PowerShell dla StorSimple zawiera funkcje, które umożliwiają zarejestrować urządzenie, skonfiguruj interfejs sieciowy na urządzeniu, zainstalować niektórych typów aktualizacji, rozwiązywania problemów z urządzeniem, uzyskując dostęp do sesji pomocy technicznej i zmienić stan urządzenia. Nawiązując połączenie z konsolą szeregową urządzenia lub przy użyciu komunikacji zdalnej programu Windows PowerShell miały dostęp do programu Windows PowerShell dla StorSimple.

Usługi zdalne środowiska PowerShell może odbywać się za pośrednictwem protokołu HTTP lub HTTPS. Po włączeniu zdalnego zarządzania za pośrednictwem protokołu HTTPS, należy pobrać certyfikat zdalnego zarządzania z urządzenia i zainstalować ją na kliencie zdalnym. Aby uzyskać więcej informacji dotyczących komunikacji zdalnej programu PowerShell, przejdź do [połączenia zdalne z urządzeniem StorSimple](storsimple-8000-remote-connect.md).

Nawiąż połączenie z urządzeniem przy użyciu programu Windows PowerShell dla StorSimple, należy podać hasło administratora urządzenia do logowania się do urządzenia.

![Hasło administratora urządzenia](./media/storsimple-security/DeviceAdminPW.png)

Pamiętać o następujących rozwiązań:

* Zdalne zarządzanie jest domyślnie wyłączona. Można użyć usługi Menedżer StorSimple urządzenia ją włączyć. Ze względów bezpieczeństwa dostępu zdalnego powinna być włączona tylko w tym okresie, które są rzeczywiście potrzebne.
* Jeśli zmienisz hasło, należy powiadomić wszystkich użytkowników dostępu zdalnego tak, aby nie będą występować utracie Nieoczekiwana łączności.
* Usługę Menedżer StorSimple urządzenia nie można pobrać istniejące hasła: można tylko zresetować je. Firma Microsoft zaleca przechowywania wszystkie hasła w bezpiecznym miejscu, dzięki czemu nie trzeba zresetować hasło, jeśli jest zapomnienia hasła. Jeśli potrzebujesz zresetować hasło, należy powiadomić wszystkich użytkowników, zanim je zresetować.

Można uzyskać dostępu do interfejsu programu Windows PowerShell za pomocą połączenia szeregowego na urządzeniu. Możesz również do niego dostęp zdalnie przy użyciu protokołu HTTP lub HTTPS, która zapewnia dodatkowe zabezpieczenia. HTTPS zapewnia wyższy poziom zabezpieczeń niż seryjny lub połączenia HTTP. Jednak do używania protokołu HTTPS, należy najpierw zainstalować certyfikat na komputerze klienckim, który będą uzyskiwać dostęp do urządzenia. Certyfikat dostępu zdalnego można pobrać ze strony konfiguracji urządzenia w usłudze Menedżer StorSimple urządzenia. W przypadku utraty certyfikatu na potrzeby dostępu zdalnego, musisz pobrać nowy certyfikat i propagację go do wszystkich klientów, które są autoryzowane do używania zdalnego zarządzania.

### <a name="challenge-handshake-authentication-protocol-chap-initiator-and-target-passwords"></a>Żądanie hasła Inicjator i obiekt docelowy protokołu uwierzytelniania Handshake (CHAP)

Protokół CHAP jest schemat uwierzytelniania używany przez urządzenie StorSimple do sprawdzania tożsamości klientów zdalnych. Weryfikacja jest oparta na wspólne hasło. Protokół CHAP, może być jednokierunkowe (jednokierunkowe) lub wzajemne (dwukierunkowe). Za pomocą jednokierunkowego protokołu CHAP element docelowy (urządzenia StorSimple) uwierzytelnia inicjatora (hosta). Wzajemnego lub wstecznego protokołu CHAP wymaga, aby obiekt docelowy uwierzytelniania inicjatora, a następnie inicjatora uwierzytelniania obiektu docelowego. Twoje StorSimple można skonfigurować do używania każdej z metod.

Konfigurowanie protokołu CHAP, należy pamiętać o następujących:

* Nazwa użytkownika protokołu CHAP musi zawierać mniej niż 233 znaków.
* Hasło protokołu CHAP musi należeć do zakresu od 12 do 16 znaków. Podjęto próbę dłużej nazwę użytkownika lub hasło spowoduje niepowodzenie uwierzytelniania na hoście systemu Windows.
* Nie można użyć tego samego hasła dla inicjatora protokołu CHAP i obiektu docelowego protokołu CHAP.
* Po ustawieniu hasła, można zmienić, ale nie można pobrać. Jeśli hasło zostało zmienione, należy powiadomić wszystkich użytkowników dostępu zdalnego tak, aby pomyślnie umożliwić im połączenie z urządzeniem StorSimple.

Aby uzyskać więcej informacji na temat protokołu CHAP i skonfigurować go do rozwiązania StorSimple, przejdź do [Konfigurowanie protokołu CHAP dla urządzenia StorSimple](storsimple-8000-configure-chap.md).

### <a name="storsimple-snapshot-manager-password"></a>Hasło programu StorSimple Snapshot Manager

StorSimple Snapshot Manager jest przystawką Microsoft Management Console (MMC), używaną grup woluminu i usługi kopiowania woluminów systemu Windows w tle do generowania kopie zapasowe spójnych z aplikacją. Ponadto można użyć programu StorSimple Snapshot Manager do tworzenia harmonogramów kopii zapasowych oraz klonowania lub Przywracanie woluminów.

Podczas konfigurowania na urządzeniu StorSimple Snapshot Manager korzystać, trzeba będzie podać hasło programu StorSimple Snapshot Manager. To hasło jest najpierw ustawić w programie Windows PowerShell dla StorSimple podczas rejestracji. Można również ustawić hasło i zmieniła się z usługi Menedżer StorSimple urządzenia. To hasło służy do uwierzytelniania urządzeń z StorSimple Snapshot Manager.

![Hasło programu StorSimple Snapshot Manager](./media/storsimple-security/SnapshotMgrPassword.png)

Hasło programu StorSimple Snapshot Manager musi być 14 do 15 znaków i może zawierać 3 lub więcej z kombinacji wielkie litery, małe litery, liczbowego i znaki specjalne. Po ustawieniu hasło programu StorSimple Snapshot Manager można zmienić, ale nie można pobrać. Jeśli zmienisz hasło, należy powiadomić wszystkich użytkowników zdalnych.

Aby uzyskać więcej informacji na temat programu StorSimple Snapshot Manager przejdź do [co to jest StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

### <a name="password-best-practices"></a>Najlepsze rozwiązania w zakresie hasła

Firma Microsoft zaleca, skorzystaj z poniższych wskazówek w celu zapewnienia, że haseł usługi StorSimple silne i dobrze chroniony:

* Zmień hasła co trzy miesiące. Zmiana hasła jest wymuszana co roku.
* Należy używać silnych haseł. Aby uzyskać więcej informacji, przejdź do [tworzyć silniejsze hasła i chronić je](http://blogs.microsoft.com/cybertrust/2014/08/25/create-stronger-passwords-and-protect-them/).
* Zawsze używaj różnych haseł dla dostępu do różnych mechanizmów; Każdy z haseł, które określisz powinna być unikatowa.
* Nie udostępniaj hasła każdemu użytkownikowi, który nie ma uprawnień dostępu do tego urządzenia StorSimple.
* Nie Czytaj o hasło przed innymi lub wskazówki na format hasła.
* Jeśli podejrzewasz, że naruszono konto lub hasło, należy zgłosić zdarzenie, aby dział bezpieczeństwa informacji.
* Traktuj wszystkie hasła jako poufne, poufne informacje. 

## <a name="storsimple-data-protection"></a>Ochrona danych StorSimple

W tej sekcji opisano funkcje zabezpieczeń StorSimple ochrony danych przesyłanych i przechowywanych danych.

Jak opisano w innych częściach, hasła są używane do autoryzacji i uwierzytelnianie użytkowników, zanim uzyskają dostęp do rozwiązania StorSimple. Kolejnym zagadnieniem zabezpieczeń jest ochrona danych przed nieautoryzowanymi użytkownikami, gdy są przesyłane między systemami magazynowania, a podczas są przechowywane. W poniższych sekcjach opisano funkcje ochrony danych, które są dostarczane z StorSimple.

> [!NOTE]
> Funkcja deduplikacji udostępnia dodatkową ochronę danych przechowywanych na urządzeniu StorSimple i magazynu Microsoft Azure. Gdy dane jest deduplikowany, obiekty danych są przechowywane oddzielnie od metadane używane do mapowania i uzyskiwać do nich dostęp: nie jest dostępny kontekst poziom przechowywania odtworzenie danych na podstawie struktury woluminu, system plików lub nazwę pliku.


## <a name="protect-data-flowing-through-the-service"></a>Ochrona danych przepływających przez usługę

Głównym celem usługi Menedżer StorSimple urządzenia jest Konfigurowanie urządzenia StorSimple oraz zarządzanie nimi. Usługa Menedżera urządzeń StorSimple działa w systemie Microsoft Azure. Wprowadź dane konfiguracji urządzenia za pomocą portalu Azure, a następnie Microsoft Azure używa usługi Menedżer StorSimple urządzenia do wysyłania danych do urządzenia. StorSimple korzysta z systemu par kluczy asymetrycznych w celu zapewnienia, że naruszenia zabezpieczeń usługi Azure nie spowoduje naruszenie przechowywanych informacji.

![Szyfrowanie danych w locie](./media/storsimple-security/DataEncryption.png)

System klucza asymetrycznego pomaga chronić dane przepływającego za pośrednictwem usługi:

1. Certyfikat szyfrowania danych, który używa publicznego i prywatnego klucza asymetrycznego pary jest generowany na urządzeniu i jest używany do ochrony danych. Klucze są generowane, gdy pierwszy urządzenie jest zarejestrowane.
2. Klucze certyfikatu szyfrowania danych zostaną wyeksportowane do pliku wymiany informacji osobistych (pfx), który jest chroniony przez klucz szyfrowania danych usługi, który jest silnego klucza 128-bitowego, losowo generowany przez pierwszego urządzenia podczas rejestracji.
3. Klucz publiczny certyfikatu jest bezpiecznie udostępnione usługi Menedżer StorSimple urządzenia i pozostaje klucz prywatny z urządzeniem.
4. Data rozpoczęcia pracy są szyfrowane przy użyciu publicznego klucza i odszyfrowane przy użyciu klucza prywatnego przechowywanych na urządzeniu, zapewniając, że usługa Azure nie może odszyfrować dane przepływające na urządzeniu.

Klucz szyfrowania danych usługi jest generowany tylko pierwszego urządzenia zarejestrowane w usłudze. Wszystkie kolejne urządzenia, które są zarejestrowane w usłudze muszą używać tego samego klucza szyfrowania danych usługi.

> [!IMPORTANT]
> Jest bardzo ważne, aby utworzyć kopię klucza szyfrowania danych usługi i zapisz go w bezpiecznym miejscu. Kopię klucza szyfrowania danych usługi powinny być przechowywane w taki sposób, mogą uzyskiwać przez osobę upoważnioną i mogą być łatwo przekazywane administratora urządzenia.
> 
> W przypadku utraty klucza szyfrowania danych usługi pomocy technicznej firmy Microsoft pomoże Ci aby go uzyskać, pod warunkiem, że masz co najmniej jedno urządzenie w stanie online. Firma Microsoft zaleca zmianę klucza szyfrowania danych usługi po jej pobraniu.

Aby zmienić klucz szyfrowania danych usługi i odpowiedni certyfikat szyfrowania danych, postępuj zgodnie z instrukcjami [zmienić klucza szyfrowania danych usługi dla usługi Menedżer StorSimple urządzenia](storsimple-8000-manage-service.md#change-the-service-data-encryption-key). Zmiana kluczy szyfrowania wymaga, aby wszystkie urządzenia można zaktualizować za pomocą nowego klucza. Dlatego zaleca się zmiany klucza, gdy wszystkie urządzenia są w trybie online. Jeśli urządzenia są w trybie offline, można zmienić ich kluczy w innym czasie. Urządzenia z kluczami nieaktualne nadal będzie można wykonywać kopie zapasowe, ale nie będzie mógł przywrócić dane, dopóki nie zostanie zaktualizowana klucza.

Klucz szyfrowania danych usługi i certyfikat szyfrowania danych nie wygasa. Jednak zaleca się, że zmienić klucza szyfrowania danych usługi co roku, aby zapobiec złamania klucza.

## <a name="protect-data-at-rest"></a>Ochrona danych w stanie spoczynku

Urządzenia StorSimple zarządza danymi przez zapisanie jej w warstwach lokalnie i w chmurze, w zależności od częstotliwości użytkowania. Wszystkie maszyny hosta, które są połączone z urządzeniem wysyłać dane do urządzenia, które przenosi dane w chmurze, zależnie od potrzeb. Dane są przesyłane z urządzenia do chmury bezpiecznie za pośrednictwem Internetu. Każde urządzenie ma jeden obiekt docelowy iSCSI, która udostępnia wszystkie udostępnione woluminy na tym urządzeniu. Wszystkie dane są szyfrowane przed wysłaniem do magazynu w chmurze. 

![klucz szyfrowania magazynu w chmurze](./media/storsimple-security/CloudStorageEncryption.png)

Do zapewnienia bezpieczeństwa i integralności danych przeniesiona do chmury, StorSimple można zdefiniować klucze szyfrowania magazynu w chmurze w następujący sposób:

* Klucz szyfrowania magazynu w chmurze można określić podczas tworzenia kontenera woluminów. Klucz nie może być zmodyfikowanie lub dodanie później.
* Wszystkie woluminy w kontenerze woluminów udostępnianie tego samego klucza szyfrowania. Jeśli chcesz inny formularz szyfrowania dla określonego woluminu, zaleca się utworzenie nowego kontenera woluminów do tego woluminu hosta.
* Po wprowadzeniu klucz szyfrowania magazynu w chmurze w usłudze Menedżer StorSimple urządzenia klucz jest zaszyfrowany przy użyciu publicznej części klucza szyfrowania danych usługi, a następnie wysyłane do urządzenia.
* Klucz szyfrowania magazynu w chmurze nie są przechowywane w dowolnym miejscu w usłudze, wiadomo, tylko urządzenia.
* Określenie klucz szyfrowania magazynu w chmurze jest opcjonalne. Możesz wysłać dane, które zostały zaszyfrowane na hoście, na urządzeniu.

### <a name="additional-security-best-practices"></a>Dodatkowe najlepsze rozwiązania

* Podziel ruchu: wyizolować z sieci SAN iSCSI z ruchu danych w sieci lokalnej firmy wdrażania całkowicie rozdzielonych sieci i przy użyciu sieci VLAN, których fizycznych izolacji nie jest opcją. Sieć dedykowanych dla magazynu iSCSI zagwarantuje bezpieczeństwa i wydajności danych biznesowych o znaczeniu krytycznym. Mieszanie ruchu magazynu i użytkownika w firmowej sieci LAN nie jest zalecana i może zwiększyć czas oczekiwania i powodować awarie sieci.
* Na stronie zabezpieczenia sieci po stronie hosta należy użyć interfejsów sieciowych, które obsługują protokół TCP/IP Offload Engine (TOE). TOE zmniejsza obciążenie procesora CPU przez przetwarzanie TCP na karcie sieciowej.

## <a name="protect-data-via-storage-accounts"></a>Ochrona danych za pomocą kont magazynu

Każda subskrypcja Microsoft Azure można utworzyć co najmniej jedno konto magazynu. (Konto magazynu zapewnia unikatową przestrzeń nazw do pracy z danych przechowywanych w chmurze Azure). Dostęp do konta magazynu jest kontrolowany przez klucze dostępu i subskrypcji skojarzonych z tym kontem magazynu.

Podczas tworzenia konta magazynu Microsoft Azure generuje dwa klucze dostępu 512-bitowe magazynu, z których jeden jest używany do uwierzytelniania, gdy urządzenie StorSimple uzyskuje dostęp do konta magazynu. Należy pamiętać, że jest używany tylko jeden z tych kluczy. Inne klucza jest przechowywana w rezerwy, umożliwiając okresowo zmieniać klucze. Obracanie klucze, uaktywnić klucza pomocniczego, a następnie usuń klucz podstawowy. Następnie można utworzyć nowego klucza do użycia podczas następnego obrotu. (Ze względów bezpieczeństwa wiele centrów danych wymagają rotacją kluczy).

Firma Microsoft zaleca, należy wykonać następujące najlepsze rozwiązania dotyczące obrotu klucza:

* Należy obrócić klucze konta magazynu regularnie w celu zapewnienia, że Twoje konto magazynu nie jest używane przez nieautoryzowanych użytkowników.
* Okresowo administratora platformy Azure, należy zmienić lub ponownie wygenerować klucz podstawowy lub pomocniczy przy użyciu sekcji magazynu w portalu Azure na bezpośredni dostęp do konta magazynu.

## <a name="protect-data-via-encryption"></a>Ochrona danych za pomocą szyfrowania

StorSimple używa następujących algorytmów szyfrowania, aby chronić dane przechowywane w lub podróży między składnikami rozwiązania StorSimple.

| Algorytm | Długość klucza | Komentarze protokołów/aplikacji |
| --- | --- | --- |
| RSA |2048 |RSA PKCS 1-1.5 służy do szyfrowania danych konfiguracji, które są wysyłane do urządzenia w portalu Azure: na przykład poświadczenia, konfiguracji urządzenia StorSimple, konta magazynu i klucze szyfrowania magazynu w chmurze. |
| AES |256 |AES z CBC służy do szyfrowania publicznej części klucza szyfrowania danych usługi przed wysłaniem do portalu Azure z urządzenia StorSimple. Jest on również używane przez urządzenia StorSimple do szyfrowania danych przed wysłaniem danych do konta magazynu w chmurze. |

## <a name="storsimple-cloud-appliance-security"></a>Urządzenia chmury StorSimple zabezpieczeń

[!INCLUDE [storsimple Cloud Appliance security](../../includes/storsimple-virtual-device-security.md)]

## <a name="managing-personal-inforamation"></a>Zarządzanie inforamation osobiste

Menedżer urządzeń StorSimple serii fizycznych i wirtualnych zbiera informacje osobiste w Oto wystąpienia klucza:

- Alert ustawienia użytkownika, gdy skonfigurowano adres e-mail użytkowników. Te informacje można wyświetlać i usunięte przez administratora. Dotyczy to urządzeń z serii StorSimple 8000 i tablice wirtualne StorSimple.
 * Aby wyświetlić i wyczyść ustawienia z serii StorSimple 8000, postępuj zgodnie z instrukcjami [widoku StorSimple alerty i zarządzaj nimi](storsimple-8000-manage-alerts.md#configure-alert-settings)
 * Aby wyświetlić i wyczyść ustawienia dla tablicy wirtualnego StorSimple, postępuj zgodnie z instrukcjami [widoku StorSimple alerty i zarządzaj nimi](storsimple-virtual-array-manage-alerts.md#configure-alert-settings)
- Użytkownicy, którzy mają dostęp do danych znajdujących się w akcji. Listę użytkowników, którzy mogą uzyskiwać dostęp do danych udziałów jest wyświetlane i można go wyświetlić. Ta lista również zostanie usunięta po usunięciu akcji. Dotyczy to tylko tablice wirtualne StorSimple.
 * Aby wyświetlić listę użytkowników, którzy mogą dostępu lub aby usunąć udział, wykonaj czynności opisane w [Zarządzanie udziałami w macierzy wirtualnego StorSimple](storsimple-virtual-array-manage-shares.md)

Aby uzyskać więcej informacji, przejrzyj zasady Privacy firmy Microsoft w [Centrum zaufania](https://www.microsoft.com/trustcenter).

## <a name="frequently-asked-questions-faq"></a>Często zadawane pytania

Poniżej przedstawiono niektóre pytania i odpowiedzi dotyczące zabezpieczeń i Microsoft Azure StorSimple.

**Pytanie:** Moje usługi zostanie naruszony. Jakie powinny być następne kroki należy wykonać?

**Odpowiedź:** natychmiast należy zmienić klucza szyfrowania danych usługi i klucze konta magazynu dla konta magazynu, który jest używany dla danych warstw. Aby uzyskać instrukcje przejdź do:

* [Zmiana klucza szyfrowania danych usługi](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [Rotacją kluczy kont magazynu](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts)

**Pytanie:** mam nowe urządzenie StorSimple, który żąda klucz rejestracji usługi. Jak pobrać go

**Odpowiedź:** ten klucz został utworzony podczas tworzenia usługi Menedżer StorSimple urządzenia. Gdy używasz usługi Menedżer StorSimple urządzenia do nawiązania połączenia z urządzeniem służy strona szybki start usługi do wyświetlania lub ponownie wygenerować klucz rejestracji usługi. Generowanie klucza rejestracji usługi nie wpłynie na istniejące zarejestrowanych urządzeń. Aby uzyskać instrukcje przejdź do:

* [Wyświetl lub ponownie wygenerować klucz rejestracji usługi](storsimple-8000-manage-service.md##regenerate-the-service-registration-key)

**Pytanie:** utratą mój klucz szyfrowania danych usługi. Co mam zrobić?

**Odpowiedź:** skontaktuj się z pomocą techniczną firmy Microsoft. Można logowania się do sesji pomocy technicznej na urządzeniu i pomoc można pobrać klucza (zakładając, że co najmniej jedno urządzenie jest w trybie online). Natychmiast po uzyskaniu klucza szyfrowania danych usługi należy zmienić, aby upewnić się, że nowy klucz jest znany tylko dla Ciebie. Aby uzyskać instrukcje przejdź do:

* [Zmiana klucza szyfrowania danych usługi](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)

**Pytanie:** autoryzowany urządzenie na potrzeby usługi zmiany klucza szyfrowania danych, ale nie zostały uruchomione proces zmiany klucza. Co mam zrobić?

**Odpowiedź:** jeśli upłynął limit czasu, konieczne będzie ponownie autoryzować urządzenie pod kątem zmian klucza szyfrowania danych usługi i ponowne uruchomienie procesu.

**Pytanie:** klucza szyfrowania danych usługi zostały zmienione, ale I nie może zaktualizować inne urządzenia w ciągu 4 godzin. Należy ponownie uruchomić ponownie?

**Odpowiedź:** 4-godzinnego okresu jest tylko do inicjowania zmiany. Po rozpoczęciu procesu aktualizacji na urządzeniu StorSimple na autoryzowanych Autoryzacja jest prawidłowa, dopóki wszystkie urządzenia są aktualizowane.

**Pytanie:** administratora nasze StorSimple odejścia z firmy. Co mam zrobić?

**Odpowiedź:** zmiany i resetowania haseł, których dostęp do urządzenia StorSimple i zmianę szyfrowania danych usługi klucza, aby upewnić się, że nowe informacje jest nieznany nieautoryzowany personel. Aby uzyskać instrukcje przejdź do:

* [Umożliwia zmianę hasła storsimple usługi Menedżer StorSimple urządzenia](storsimple-8000-change-passwords.md)
* [Zmiana klucza szyfrowania danych usługi](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [Konfigurowanie protokołu CHAP dla urządzenia StorSimple](storsimple-8000-configure-chap.md)

**Pytanie:** chcę podać hasło programu StorSimple Snapshot Manager do hosta, który nawiązuje połączenie z urządzenia StorSimple, ale hasło nie jest dostępna. Co mogę zrobić?

**Odpowiedź:** jeśli pamiętasz hasła, należy utworzyć nowy. Następnie należy informuje wszystkich istniejących użytkowników, że hasło zostało zmienione i powinien zaktualizowania ich klientom na używanie nowego hasła. Aby uzyskać instrukcje przejdź do:

* [Zmień hasło programu StorSimple Snapshot Manager](storsimple-8000-change-passwords.md#set-the-storsimple-snapshot-manager-password)
* [Uwierzytelniania urządzenia](storsimple-snapshot-manager-manage-devices.md#authenticate-a-device)

**Pytanie:** certyfikatu na potrzeby dostępu zdalnego do programu Windows PowerShell dla urządzenia StorSimple został zmieniony na urządzeniu. Jak zaktualizować moich klientów dostępu zdalnego?

**Odpowiedź:** pobrania nowego certyfikatu z usługi Menedżer StorSimple urządzenia, a następnie podaj go do zainstalowania w magazynie certyfikatów klientów dostępu zdalnego. Aby uzyskać instrukcje przejdź do:

* [Polecenia cmdlet Import certyfikatu](https://technet.microsoft.com/library/hh848630.aspx)

**Pytanie:** dane chronione w przypadku złamania zabezpieczeń usługi Menedżer StorSimple urządzenia?

**Odpowiedź:** danych konfiguracji usługi są zawsze szyfrowane kluczem publicznym, podczas wyświetlania w przeglądarce sieci web. Ponieważ usługa nie ma dostępu do klucza prywatnego, usługa nie będzie mógł wyświetlić wszystkich danych. W przypadku naruszenia zabezpieczeń usługi Menedżer StorSimple urządzenie nie ma żadnego wpływu, ponieważ nie kluczy przechowywanych w usłudze Menedżer StorSimple urządzenia.

**Pytanie:** Jeśli ktoś uzyska dostęp do certyfikatu szyfrowania danych, danych może stwarzać zagrożenie?

**Odpowiedź:** Microsoft Azure przechowuje klucz szyfrowania danych klienta (pfx) w postaci zaszyfrowanej. Ponieważ jest zaszyfrowany plik PFX, a usługa StorSimple nie ma klucza szyfrowania danych usługi do odszyfrowywania pliku PFX, po prostu uzyskiwanie dostępu do pliku .pfx nie uwidoczni żadnych kluczy tajnych.

**Pytanie:** co się stanie w przypadku jednostki rządowe zapyta firmy Microsoft dla danych?

**Odpowiedź:** , dlatego wszystkie dane są szyfrowane na usługi i klucz prywatny jest przechowywany z urządzeniem, jednostki rządowe musi uzyskać klienta danych.



## <a name="next-steps"></a>Kolejne kroki

[Wdrażanie urządzenia StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

