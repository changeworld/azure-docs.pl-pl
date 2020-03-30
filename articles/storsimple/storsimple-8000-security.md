---
title: Zabezpieczenia storSimple serii 8000 | Dokumenty firmy Microsoft
description: W tym artykule opisano funkcje zabezpieczeń i prywatności, które chronią usługę StorSimple, urządzenie i dane w środowisku lokalnym i w chmurze.
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
ms.openlocfilehash: 4598f71f9b611e68f8eb00676138784833c39f32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75891510"
---
# <a name="storsimple-security-and-data-protection"></a>Bezpieczeństwo i ochrona danych StorSimple

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Omówienie

Bezpieczeństwo jest głównym problemem dla każdego, kto przyjmuje nową technologię, zwłaszcza gdy technologia jest używana z poufnymi lub zastrzeżonymi danymi. Podczas oceny różnych technologii należy wziąć pod uwagę zwiększone ryzyko i koszty ochrony danych. Usługa Microsoft Azure StorSimple zapewnia zarówno rozwiązanie zabezpieczeń, jak i ochrony prywatności w celu ochrony danych, co pomaga zapewnić:

* **Poufność** — tylko autoryzowane podmioty mogą wyświetlać twoje dane.
* **Integralność** — tylko autoryzowane podmioty mogą modyfikować lub usuwać dane.

Rozwiązanie Microsoft Azure StorSimple składa się z czterech głównych składników, które współdziałają ze sobą:

* **Usługa StorSimple Device Manager hostowana na platformie Microsoft Azure** — usługa zarządzania używana do konfigurowania i aprowizowania urządzenia StorSimple.
* **Urządzenie StorSimple** — urządzenie fizyczne zainstalowane w centrum danych. Wszystkie hosty i klienci, którzy generują dane, łączą się z urządzeniem StorSimple, a urządzenie zarządza danymi i przenosi je do chmury platformy Azure.
* **Klienci/hosty podłączone do urządzenia** — klientów w infrastrukturze, które łączą się z urządzeniem StorSimple i generują dane, które muszą być chronione.
* **Magazyn w chmurze** — lokalizacja w chmurze platformy Azure, w której są przechowywane dane.

W poniższych sekcjach opisano funkcje zabezpieczeń StorSimple, które pomagają chronić każdy z tych składników i dane przechowywane na nich. Zawiera również listę pytań, które mogą mieć dotyczące zabezpieczeń usługi Microsoft Azure StorSimple i odpowiadające im odpowiedzi.

## <a name="storsimple-device-manager-service-protection"></a>Ochrona usługi StorSimple Device Manager

Usługa StorSimple Device Manager to usługa zarządzania hostowana na platformie Microsoft Azure i używana do zarządzania wszystkimi urządzeniami StorSimple, które zamówiła twoja organizacja. Dostęp do usługi StorSimple Device Manager można uzyskać przy użyciu poświadczeń organizacji, aby zalogować się do portalu Azure za pośrednictwem przeglądarki sieci Web.

Dostęp do usługi StorSimple Device Manager wymaga, aby Twoja organizacja miała subskrypcję platformy Azure, która obejmuje StorSimple. Subskrypcja określa funkcje, do których masz dostęp w witrynie Azure Portal. Jeśli Twoja organizacja nie ma subskrypcji platformy Azure i chcesz dowiedzieć się więcej o nich, zobacz [Rejestrowanie się na platformie Azure jako organizacja.](../active-directory/fundamentals/sign-up-organization.md)

Ponieważ usługa StorSimple Device Manager jest hostowana na platformie Azure, jest chroniona przez funkcje zabezpieczeń platformy Azure. Aby uzyskać więcej informacji na temat funkcji zabezpieczeń platformy Microsoft Azure, zobacz [Centrum zaufania Microsoft Azure](https://azure.microsoft.com/support/trust-center/security/).

## <a name="storsimple-device-protection"></a>Ochrona urządzenia StorSimple

Urządzenie StorSimple to lokalne hybrydowe urządzenie pamięci masowej, które zawiera dyski PÓŁPRZEWODNIKOWE (SSD) i dyski twarde (HDD), wraz z nadmiarowymi kontrolerami i funkcjami automatycznego pracy awaryjnej. Kontrolery zarządzają warstwami magazynu, umieszczając obecnie używane (lub gorące) dane w magazynie lokalnym (na urządzeniu StorSimple lub na serwerach lokalnych), przenosząc rzadziej używane dane do chmury.

Tylko autoryzowane urządzenia StorSimple mogą dołączyć do usługi StorSimple Device Manager utworzonej w ramach subskrypcji platformy Azure. Aby autoryzować urządzenie, należy zarejestrować je w usłudze StorSimple Device Manager, podając klucz rejestracji usługi. Klucz rejestracji usługi jest 128-bitowym kluczem losowym generowanym w witrynie Azure portal.

![Klucz rejestracji usługi](./media/storsimple-security/ServiceRegistrationKey.png)

Aby dowiedzieć się, jak uzyskać klucz rejestracji usługi, przejdź do [kroku 2: Pobierz klucz rejestracji usługi](storsimple-8000-deployment-walkthrough-u2.md#step-2-get-the-service-registration-key).

Klucz rejestracji usługi jest długi klucz, który zawiera ponad 100 znaków. Klucz można skopiować i zapisać w pliku tekstowym w bezpiecznej lokalizacji, aby w razie potrzeby autoryzować dodatkowe urządzenia. Jeśli klucz rejestracji usługi zostanie utracony po zarejestrowaniu pierwszego urządzenia, można wygenerować nowy klucz z usługi StorSimple Device Manager. Nie wpłynie to na działanie istniejących urządzeń.

Po zarejestrowaniu urządzenia używa tokenów do komunikowania się z platformą Microsoft Azure. Klucz rejestracji usługi nie jest używany po rejestracji urządzenia.

> [!NOTE]
> Zaleca się ponowne wygenerowanie klucza rejestracji usługi po każdym użyciu.


## <a name="protect-your-storsimple-solution-via-passwords"></a>Chroń swoje rozwiązanie StorSimple za pomocą haseł

Hasła są ważnym aspektem zabezpieczeń komputera i są szeroko stosowane w rozwiązaniu StorSimple, aby zapewnić dostępność danych tylko dla autoryzowanych użytkowników. StorSimple umożliwia skonfigurowanie następujących haseł:

* StorSimple hasło administratora urządzenia
* Zazywkowe zainicjowanie protokołu CHAP (Handshake Authentication Protocol) i hasła docelowe
* Hasło programu StorSimple Snapshot Manager

### <a name="windows-powershell-for-storsimple-and-the-storsimple-device-administrator-password"></a>Program Windows PowerShell for StorSimple i hasło administratora urządzenia StorSimple

Program Windows PowerShell for StorSimple to interfejs wiersza polecenia, którego można używać do zarządzania urządzeniem StorSimple. Program Windows PowerShell for StorSimple ma funkcje, które umożliwiają rejestrowanie urządzenia, konfigurowanie interfejsu sieciowego na urządzeniu, instalowanie niektórych typów aktualizacji, rozwiązywanie problemów z urządzeniem przez dostęp do sesji pomocy technicznej i zmienianie stanu urządzenia. Dostęp do programu Windows PowerShell for StorSimple można uzyskać, łącząc się z konsolą szeregową na urządzeniu lub korzystając z komunikacji zdalnej programu Windows PowerShell.

Komunikacji zdalnej programu PowerShell można wykonać za pośrednictwem protokołu HTTPS lub HTTP. Jeśli zdalne zarządzanie za pośrednictwem protokołu HTTPS jest włączone, należy pobrać certyfikat zdalnego zarządzania z urządzenia i zainstalować go na kliencie zdalnym. Aby uzyskać więcej informacji na temat komunikacji zdalnej programu PowerShell, przejdź do [urządzenia StorSimple w trybie "Połącz się zdalnie".](storsimple-8000-remote-connect.md)

Po nawiązaniu połączenia z urządzeniem za pomocą programu Windows PowerShell for StorSimple należy podać hasło administratora urządzenia, aby zalogować się do urządzenia.

![Hasło administratora urządzenia](./media/storsimple-security/DeviceAdminPW.png)

Należy pamiętać o następujących sprawdzonych praktykach:

* Zdalne zarządzanie jest domyślnie wyłączone. Aby ją włączyć, można użyć usługi StorSimple Device Manager. Ze względów bezpieczeństwa dostęp zdalny powinien być włączony tylko w okresie, który jest rzeczywiście potrzebny.
* Jeśli zmienisz hasło, należy powiadomić wszystkich użytkowników dostępu zdalnego, aby nie doświadczyć nieoczekiwanej utraty łączności.
* Usługa StorSimple Device Manager nie może pobrać istniejących haseł: może je tylko zresetować. Zaleca się przechowywanie wszystkich haseł w bezpiecznym miejscu, aby nie trzeba było resetować hasła, jeśli zostanie zapomniane. Jeśli musisz zresetować hasło, pamiętaj, aby powiadomić wszystkich użytkowników przed jego zresetowaniem.

Dostęp do interfejsu programu Windows PowerShell można uzyskać przy użyciu połączenia szeregowego z urządzeniem. Można również uzyskać do niego dostęp zdalnie przy użyciu protokołu HTTP lub HTTPS, które zapewniają dodatkowe zabezpieczenia. Protokół HTTPS zapewnia wyższy poziom zabezpieczeń niż połączenie szeregowe lub HTTP. Jednak aby korzystać z protokołu HTTPS, należy najpierw zainstalować certyfikat na komputerze klienckim, który będzie uzyskiwać dostęp do urządzenia. Certyfikat dostępu zdalnego można pobrać ze strony konfiguracji urządzenia w usłudze StorSimple Device Manager. Jeśli certyfikat dostępu zdalnego zostanie utracony, należy pobrać nowy certyfikat i propagować go do wszystkich klientów, którzy są upoważnieni do korzystania z zarządzania zdalnego.

### <a name="challenge-handshake-authentication-protocol-chap-initiator-and-target-passwords"></a>Zazywkowe zainicjowanie protokołu CHAP (Handshake Authentication Protocol) i hasła docelowe

PROTOKÓŁ CHAP to schemat uwierzytelniania używany przez urządzenie StorSimple do sprawdzania tożsamości klientów zdalnych. Weryfikacja jest oparta na udostępnionym haśle. Protokół CHAP może być jednokierunkowy (jednokierunkowy) lub wzajemny (dwukierunkowy). W trybie jednokierunkowym protokołu CHAP obiekt docelowy (urządzenie StorSimple) uwierzytelnia inicjatora (hosta). Wzajemne lub odwrotne CHAP wymaga, aby obiekt docelowy uwierzytelnił inicjatora, a następnie inicjatora uwierzytelnić obiekt docelowy. StorSimple można skonfigurować do użycia jednej z tych metod.

Podczas konfigurowania protokołu CHAP należy pamiętać o następujących aspektach:

* Nazwa użytkownika protokołu CHAP musi zawierać mniej niż 233 znaki.
* Hasło PROTOKOŁU CHAP musi zawierać od 12 do 16 znaków. Próba użycia dłuższej nazwy użytkownika lub hasła spowoduje niepowodzenie uwierzytelniania na hoście systemu Windows.
* Nie można używać tego samego hasła zarówno dla inicjatora protokołu CHAP, jak i docelowego protokołu CHAP.
* Po ustawieniu hasła można je zmienić, ale nie można go pobrać. Jeśli hasło zostanie zmienione, należy powiadomić wszystkich użytkowników dostępu zdalnego, aby mogli pomyślnie połączyć się z urządzeniem StorSimple.

Aby uzyskać więcej informacji na temat protokołu CHAP i sposobu konfigurowania go dla rozwiązania StorSimple, przejdź do [konfigurowania protokołu CHAP dla urządzenia StorSimple](storsimple-8000-configure-chap.md).

### <a name="storsimple-snapshot-manager-password"></a>Hasło programu StorSimple Snapshot Manager

StorSimple Snapshot Manager to przystawka programu Microsoft Management Console (MMC), która używa grup woluminów i usługi kopiowania woluminów w tle systemu Windows do generowania kopii zapasowych spójnych z aplikacjami. Ponadto można użyć StorSimple Snapshot Manager do tworzenia harmonogramów tworzenia kopii zapasowych i klonowania lub przywracania woluminów.

Podczas konfigurowania urządzenia do korzystania z StorSimple Snapshot Manager, będzie wymagane podanie storsimple migawki menedżera hasła. To hasło jest po raz pierwszy ustawione w programie Windows PowerShell dla StorSimple podczas rejestracji. Hasło można również ustawić i zmienić z usługi StorSimple Device Manager. To hasło uwierzytelnia urządzenie za pomocą StorSimple Snapshot Manager.

![Hasło programu StorSimple Snapshot Manager](./media/storsimple-security/SnapshotMgrPassword.png)

Hasło Menedżera migawek StorSimple musi zawierać od 14 do 15 znaków i musi zawierać 3 lub więcej kombinacji wielkich, małych liter, numerycznych i znaków specjalnych. Po ustawieniu storsimple migawka menedżera hasło, można zmienić, ale nie można pobrać. Jeśli zmienisz hasło, pamiętaj, aby powiadomić wszystkich użytkowników zdalnych.

Aby uzyskać więcej informacji na temat Menedżera migawek StorSimple, przejdź do [Co to jest StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

### <a name="password-best-practices"></a>Najlepsze rozwiązania w zakresie haseł

Zaleca się stosowanie następujących wskazówek, aby upewnić się, że hasła StorSimple są silne i dobrze chronione:

* Zmieniaj hasła co trzy miesiące. Zmiana haseł jest wymuszana co roku.
* Używaj silnych haseł. Aby uzyskać więcej informacji, przejdź do [tworzenia silniejszych haseł i chronić je](https://cloudblogs.microsoft.com/microsoftsecure/2014/08/25/create-stronger-passwords-and-protect-them/).
* Zawsze używaj różnych haseł dla różnych mechanizmów dostępu; każde z określonych haseł powinno być unikatowe.
* Nie udostępniaj haseł osobom, które nie są autoryzowane do uzyskiwania dostępu do urządzenia StorSimple.
* Nie mów o haśle przed innymi lub podpowiedź na format hasła.
* Jeśli podejrzewasz, że konto lub hasło zostało naruszone, zgłoś zdarzenie do działu bezpieczeństwa informacji.
* Potraktuj wszystkie hasła jako poufne, poufne informacje. 

## <a name="storsimple-data-protection"></a>StorSimple ochrony danych

W tej sekcji opisano funkcje zabezpieczeń StorSimple, które chronią dane podczas przesyłania i przechowywania danych.

Zgodnie z opisem w innych sekcjach hasła są używane do autoryzowania i uwierzytelniania użytkowników, zanim będą mogli uzyskać dostęp do rozwiązania StorSimple. Innym czynnikiem bezpieczeństwa jest ochrona danych przed nieautoryzowanymi użytkownikami podczas przesyłania między systemami pamięci masowej i podczas ich przechowywania. W poniższych sekcjach opisano funkcje ochrony danych dostarczane z StorSimple.

> [!NOTE]
> Deduplikacja zapewnia dodatkową ochronę danych przechowywanych na urządzeniu StorSimple i w magazynie platformy Microsoft Azure. Gdy dane są deduplikowane, obiekty danych są przechowywane oddzielnie od metadanych używanych do mapowania i uzyskiwania do nich dostępu: nie ma dostępnego kontekstu na poziomie magazynu, aby odtworzyć dane na podstawie struktury woluminu, systemu plików lub nazwy pliku.


## <a name="protect-data-flowing-through-the-service"></a>Ochrona danych przesyłających dane przez usługę

Głównym celem usługi StorSimple Device Manager jest zarządzanie i konfigurowanie urządzenia StorSimple. Usługa StorSimple Device Manager jest uruchamiana na platformie Microsoft Azure. Za pomocą witryny Azure Portal można wprowadzić dane konfiguracji urządzenia, a następnie platforma Microsoft Azure używa usługi StorSimple Device Manager do wysyłania danych do urządzenia. StorSimple używa systemu asymetrycznych par kluczy, aby upewnić się, że naruszenie zabezpieczeń usługi Platformy Azure nie spowoduje naruszenia przechowywanych informacji.

![Szyfrowanie danych w locie](./media/storsimple-security/DataEncryption.png)

Asymetryczny system kluczy pomaga chronić dane, które przepływa przez usługę w następujący sposób:

1. Certyfikat szyfrowania danych, który używa asymetrycznej pary kluczy publicznych i prywatnych jest generowany na urządzeniu i jest używany do ochrony danych. Klucze są generowane po zarejestrowaniu pierwszego urządzenia.
2. Klucze certyfikatów szyfrowania danych są eksportowane do pliku wymiany informacji osobistych (.pfx), który jest chroniony przez klucz szyfrowania danych usługi, który jest silnym kluczem 128-bitowym generowanym losowo przez pierwsze urządzenie podczas rejestracji.
3. Klucz publiczny certyfikatu jest bezpiecznie udostępniany usłudze StorSimple Device Manager, a klucz prywatny pozostaje z urządzeniem.
4. Dane wprowadzane do usługi są szyfrowane przy użyciu klucza publicznego i odszyfrowywane przy użyciu klucza prywatnego przechowywanego na urządzeniu, dzięki czemu usługa Azure nie może odszyfrować danych przesyłanych do urządzenia.

Klucz szyfrowania danych usługi jest generowany tylko na pierwszym urządzeniu zarejestrowanym w usłudze. Wszystkie kolejne urządzenia, które są zarejestrowane w usłudze, muszą używać tego samego klucza szyfrowania danych usługi.

> [!IMPORTANT]
> Bardzo ważne jest, aby wykonać kopię klucza szyfrowania danych usługi i zapisać go w bezpiecznej lokalizacji. Kopia klucza szyfrowania danych usługi powinna być przechowywana w taki sposób, aby mogła uzyskać do niej dostęp do autoryzowanej osoby i mogła być łatwo przekazana administratorowi urządzenia.
> 
> Jeśli klucz szyfrowania danych usługi zostanie utracony, osoba pomocy technicznej firmy Microsoft może pomóc w jego pobraniu, pod warunkiem że masz co najmniej jedno urządzenie w stanie online. Zaleca się zmianę klucza szyfrowania danych usługi po jego pobraniu.

Aby zmienić klucz szyfrowania danych usługi i odpowiedni certyfikat szyfrowania danych, wykonaj czynności opisane w [obszarze Zmienianie klucza szyfrowania danych usługi dla usługi StorSimple Device Manager](storsimple-8000-manage-service.md#change-the-service-data-encryption-key). Zmiana kluczy szyfrowania wymaga aktualizacji wszystkich urządzeń za pomocą nowego klucza. W związku z tym zaleca się zmianę klucza, gdy wszystkie urządzenia są w trybie online. Jeśli urządzenia są w trybie offline, ich klucze można zmienić w innym czasie. Urządzenia z nieaktuanych kluczy nadal będą mogły uruchamiać kopie zapasowe, ale nie będą mogły przywracać danych, dopóki klucz nie zostanie zaktualizowany.

Klucz szyfrowania danych usługi i certyfikat szyfrowania danych nie wygasają. Zaleca się jednak coroczną zmianę klucza szyfrowania danych usługi, aby zapobiec naruszenia zabezpieczeń klucza.

## <a name="protect-data-at-rest"></a>Ochrona danych magazynowanych

Urządzenie StorSimple zarządza danymi, przechowując je w warstwach lokalnie i w chmurze, w zależności od częstotliwości użycia. Wszystkie komputery hosta, które są podłączone do urządzenia, wysyłają dane do urządzenia, które następnie przenosi dane do chmury, odpowiednio. Dane są bezpiecznie przesyłane z urządzenia do chmury przez Internet. Każde urządzenie ma jeden cel iSCSI, który powierzchni wszystkich udostępnionych woluminów na tym urządzeniu. Wszystkie dane są szyfrowane przed wysłaniem do magazynu w chmurze. 

![Klucz szyfrowania magazynu w chmurze](./media/storsimple-security/CloudStorageEncryption.png)

Aby zapewnić bezpieczeństwo i integralność danych przeniesionych do chmury, StorSimple umożliwia zdefiniowanie kluczy szyfrowania magazynu w chmurze w następujący sposób:

* Podczas tworzenia kontenera woluminu należy określić klucz szyfrowania magazynu w chmurze. Klucz nie może być modyfikowany ani dodawany później.
* Wszystkie woluminy w kontenerze woluminów współużytkują ten sam klucz szyfrowania. Jeśli chcesz inną formę szyfrowania dla określonego woluminu, zaleca się utworzenie nowego kontenera woluminu do obsługi tego woluminu.
* Po wprowadzeniu klucza szyfrowania magazynu w chmurze w usłudze StorSimple Device Manager klucz jest szyfrowany przy użyciu publicznej części klucza szyfrowania danych usługi, a następnie wysyłany do urządzenia.
* Klucz szyfrowania magazynu w chmurze nie jest przechowywany w dowolnym miejscu w usłudze i jest znany tylko urządzeniu.
* Określenie klucza szyfrowania magazynu w chmurze jest opcjonalne. Można wysłać dane, które zostały zaszyfrowane na hoście do urządzenia.

### <a name="additional-security-best-practices"></a>Dodatkowe najlepsze rozwiązania w zakresie zabezpieczeń

* Ruch podzielony: izoluj sieć SAN iSCSI od ruchu użytkownika w firmowej sieci LAN, wdrażając całkowicie oddzieloną sieć i używając sieci VLAN, gdzie fizyczna izolacja nie jest opcją. Dedykowana sieć pamięci masowej iSCSI zagwarantuje bezpieczeństwo i wydajność danych o krytycznym znaczeniu dla firmy. Mieszanie pamięci masowej i ruchu użytkownika za pośrednictwem firmowej sieci LAN nie jest zalecane i może zwiększyć opóźnienia i spowodować awarie sieci.
* Aby uzyskać zabezpieczenia sieci po stronie hosta, należy użyć interfejsów sieciowych obsługujących aparat odciążania TCP/IP (TOE). TOE zmniejsza obciążenie procesora przez przetwarzanie protokołu TCP na karcie sieciowej.

## <a name="protect-data-via-storage-accounts"></a>Ochrona danych za pośrednictwem kont magazynu

Każda subskrypcja platformy Microsoft Azure może utworzyć jedno lub więcej kont magazynu. (Konto magazynu udostępnia unikatowy obszar nazw do pracy z danymi przechowywanymi w chmurze platformy Azure). Dostęp do konta magazynu jest kontrolowany przez klucze subskrypcji i dostępu skojarzone z tym kontem magazynu.

Podczas tworzenia konta magazynu platforma Microsoft Azure generuje dwa 512-bitowe klucze dostępu do magazynu, z których jeden jest używany do uwierzytelniania, gdy urządzenie StorSimple uzyskuje dostęp do konta magazynu. Należy zauważyć, że tylko jeden z tych kluczy jest w użyciu. Drugi klucz jest przechowywany w rezerwie, co pozwala na okresowe obracanie klawiszy. Aby obrócić klawisze, należy uaktywnić klucz pomocniczy, a następnie usunąć klucz podstawowy. Następnie można utworzyć nowy klucz do użycia podczas następnego obrotu. (Ze względów bezpieczeństwa wiele centrów danych wymaga rotacji kluczy).

Zaleca się przestrzeganie tych najlepszych rozwiązań dotyczących rotacji kluczy:

* Należy regularnie obracać klucze konta magazynu, aby upewnić się, że konto magazynu nie jest dostępne dla nieautoryzowanych użytkowników.
* Okresowo administrator platformy Azure należy zmienić lub ponownie wygenerować klucz podstawowy lub pomocniczy przy użyciu sekcji magazynu w witrynie Azure portal, aby bezpośrednio uzyskać dostęp do konta magazynu.

## <a name="protect-data-via-encryption"></a>Ochrona danych za pomocą szyfrowania

StorSimple używa następujących algorytmów szyfrowania do ochrony danych przechowywanych w lub podróży między składnikami rozwiązania StorSimple.

| Algorytm | Długość klucza | Protokoły/aplikacje/komentarze |
| --- | --- | --- |
| RSA |2048 |RSA PKCS 1 w wersji 1.5 jest używany przez portal Azure do szyfrowania danych konfiguracji, które są wysyłane do urządzenia: na przykład poświadczenia konta magazynu, storsimple konfiguracji urządzenia i klucze szyfrowania magazynu w chmurze. |
| AES |256 |AES z CBC służy do szyfrowania publicznej części klucza szyfrowania danych usługi przed wysłaniem do portalu Azure z urządzenia StorSimple. Jest również używany przez urządzenie StorSimple do szyfrowania danych przed wysłaniem danych do konta magazynu w chmurze. |

## <a name="storsimple-cloud-appliance-security"></a>Zabezpieczenia urządzenia StorSimple Cloud Appliance

[!INCLUDE [storsimple Cloud Appliance security](../../includes/storsimple-virtual-device-security.md)]

## <a name="managing-personal-information"></a>Zarządzanie danymi osobowymi

Menedżer urządzeń StorSimple dla serii fizycznych i wirtualnych zbiera informacje osobiste w następujących kluczowych wystąpieniach:

- Alert ustawień użytkownika, gdzie adres e-mail użytkowników są skonfigurowane. Te informacje mogą być przeglądane i czyszczone przez administratora. Dotyczy to zarówno urządzeń z serii StorSimple 8000, jak i tablic wirtualnych StorSimple.
  * Aby wyświetlić i wyczyścić ustawienia serii StorSimple 8000, wykonaj czynności opisane w [sekcji Wyświetlanie alertów StorSimple i zarządzanie nimi](storsimple-8000-manage-alerts.md#configure-alert-settings)
  * Aby wyświetlić i wyczyścić ustawienia tablicy wirtualnej StorSimple, wykonaj kroki opisane w [obszarze Wyświetl alerty StorSimple i zarządzaj nimi](storsimple-virtual-array-manage-alerts.md#configure-alert-settings)
- Użytkownicy, którzy mogą uzyskać dostęp do danych zamieszkałych w udziałach. Wyświetlana jest lista użytkowników, którzy mogą uzyskać dostęp do danych udziału i można ją wyświetlić. Ta lista jest również usuwana po usunięciu udziałów. Dotyczy to tylko tablic wirtualnych StorSimple.
  * Aby wyświetlić listę użytkowników, którzy mogą uzyskiwać dostęp do udziału lub go usunąć, wykonaj czynności opisane w [programie Manage shares w tablicy wirtualnej StorSimple](storsimple-virtual-array-manage-shares.md)

Aby uzyskać więcej informacji, zapoznaj się z Zasadami ochrony prywatności firmy Microsoft w [Centrum zaufania](https://www.microsoft.com/trustcenter).

## <a name="frequently-asked-questions-faq"></a>Często zadawane pytania

Poniżej przedstawiono kilka pytań i odpowiedzi dotyczących zabezpieczeń i usługi Microsoft Azure StorSimple.

**P.** Moja usługa jest zagrożona. Jakie powinny być moje następne kroki?

**Odp.:** Należy natychmiast zmienić klucz szyfrowania danych usługi i klucze konta magazynu dla konta magazynu, który jest używany do warstwowania danych. Aby uzyskać instrukcje, przejdź do:

* [Zmienianie klucza szyfrowania danych usługi](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [Rotacja kluczy kont magazynu](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts)

**P.** Mam nowe urządzenie StorSimple, które prosi o klucz rejestracji usługi. Jak go odzyskać?

**Odp.:** Ten klucz został utworzony podczas pierwszego tworzenia usługi StorSimple Device Manager. Korzystając z usługi StorSimple Device Manager do łączenia się z urządzeniem, można użyć strony szybkiego uruchamiania usługi, aby wyświetlić lub ponownie wygenerować klucz rejestracji usługi. Wygenerowanie nowego klucza rejestracji usługi nie wpłynie na istniejące zarejestrowane urządzenia. Aby uzyskać instrukcje, przejdź do:

* [Wyświetlanie lub ponowne generowanie klucza rejestracji usługi](storsimple-8000-manage-service.md#regenerate-the-service-registration-key)

**P.** Zgubiłem klucz szyfrowania danych usługi. Co mam zrobić?

**Odp.:** Skontaktuj się z pomocą techniczną firmy Microsoft. Mogą zalogować się do sesji pomocy technicznej na urządzeniu i pomóc w pobraniu klucza (pod warunkiem, że co najmniej jedno urządzenie jest w trybie online). Natychmiast po uzyskaniu klucza szyfrowania danych usługi należy go zmienić, aby upewnić się, że nowy klucz jest znany tylko tobie. Aby uzyskać instrukcje, przejdź do:

* [Zmienianie klucza szyfrowania danych usługi](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)

**P.**  Autoryzowałem urządzenie do zmiany klucza szyfrowania danych usługi, ale nie rozpocząłem procesu zmiany klucza. Co mam zrobić?

**Odp.:** Jeśli upłynął limit czasu, należy ponownie autoryzować urządzenie do zmiany klucza szyfrowania danych usługi i ponownie rozpocząć proces.

**P.**  Zmieniłem klucz szyfrowania danych usługi, ale nie byłem w stanie zaktualizować innych urządzeń w ciągu 4 godzin. Czy muszę zacząć od nowa?

**Odp.:** 4-godzinny okres jest tylko do inicjowania zmiany. Po uruchomieniu procesu aktualizacji na autoryzowanym urządzeniu StorSimple autoryzacja jest prawidłowa, dopóki wszystkie urządzenia nie zostaną zaktualizowane.

**P.** Nasz administrator StorSimple opuścił firmę. Co mam zrobić?

**Odp.:** Zmień i zresetuj hasła umożliwiające dostęp do urządzenia StorSimple i zmień klucz szyfrowania danych usługi, aby upewnić się, że nowe informacje nie są znane nieautoryzowanemu personelowi. Aby uzyskać instrukcje, przejdź do:

* [Zmień hasła do przechowywania za pomocą usługi StorSimple Device Manager](storsimple-8000-change-passwords.md)
* [Zmienianie klucza szyfrowania danych usługi](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [Konfigurowanie protokołu CHAP dla urządzenia StorSimple](storsimple-8000-configure-chap.md)

**P.** Chcę podać storsimple migawka manager hasło do hosta, który łączy się z urządzeniem StorSimple, ale hasło nie jest dostępne. Co mogę zrobić?

**Odp.:** Jeśli nie pamiętasz hasła, należy utworzyć nowe. Następnie należy poinformować wszystkich istniejących użytkowników, że hasło zostało zmienione i że powinni zaktualizować swoich klientów, aby użyć nowego hasła. Aby uzyskać instrukcje, przejdź do:

* [Zmienianie hasła Menedżera migawek StorSimple](storsimple-8000-change-passwords.md#set-the-storsimple-snapshot-manager-password)
* [Uwierzytelnianie urządzenia](storsimple-snapshot-manager-manage-devices.md#authenticate-a-device)

**P.** Certyfikat dostępu zdalnego do programu Windows PowerShell for StorSimple został zmieniony na urządzeniu. Jak zaktualizować klientów dostępu zdalnego?

**Odp.:** Nowy certyfikat można pobrać z usługi StorSimple Device Manager, a następnie udostępnić go do zainstalowania w magazynie certyfikatów klientów dostępu zdalnego. Aby uzyskać instrukcje, przejdź do:

* [Polecenie cmdlet certyfikatu importu](https://docs.microsoft.com/powershell/module/pkiclient/import-certificate)

**P.** Czy moje dane są chronione, jeśli usługa StorSimple Device Manager zostanie naruszona?

**Odp.:** Dane konfiguracji usługi są zawsze szyfrowane przy pomocą klucza publicznego podczas wyświetlania ich w przeglądarce internetowej. Ponieważ usługa nie ma dostępu do klucza prywatnego, usługa nie będzie w stanie zobaczyć żadnych danych. Jeśli usługa StorSimple Device Manager zostanie naruszona, nie ma żadnego wpływu, ponieważ w usłudze StorSimple Device Manager nie są przechowywane żadne klucze.

**P.** Jeśli ktoś uzyska dostęp do certyfikatu szyfrowania danych, czy moje dane zostaną naruszone?

**Odp.:** Platforma Microsoft Azure przechowuje klucz szyfrowania danych klienta (plik pfx) w zaszyfrowanym formacie. Ponieważ plik .pfx jest zaszyfrowany, a usługa StorSimple nie ma klucza szyfrowania danych usługi do odszyfrowania pliku .pfx, po prostu uzyskanie dostępu do pliku .pfx nie ujawni żadnych wpisów tajnych.

**P.** Co się stanie, jeśli jednostka rządowa poprosi firmę Microsoft o podanie moich danych?

**Odp.:** Ponieważ wszystkie dane są szyfrowane w usłudze, a klucz prywatny jest przechowywany z urządzeniem, jednostka rządowa musi poprosić klienta o dane.



## <a name="next-steps"></a>Następne kroki

[Wdrażanie urządzenia StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

