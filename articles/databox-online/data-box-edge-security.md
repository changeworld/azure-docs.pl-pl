---
title: Bezpieczeństwo Edge pole danych | Dokumentacja firmy Microsoft
description: W tym artykule opisano funkcje zabezpieczeń i prywatności, służących do ochrony urządzeń krawędź pola danych, usług i danych lokalnie i w chmurze.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/02/2019
ms.author: alkohli
ms.openlocfilehash: de737f20147e8208dd18388eedcac11583c8cb97
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58891807"
---
# <a name="data-box-edge-security-and-data-protection"></a>Bezpieczeństwo Edge pola danych i ochrony danych

Zabezpieczenia są głównym problemem podczas wdrażania nowej technologii, zwłaszcza, jeśli jest to technologia używana przy użyciu danych poufnych lub zastrzeżonych. Microsoft Azure Data Box nowoczesne rozwiązanie pomaga upewnić się, że tylko autoryzowani jednostki można wyświetlić, zmodyfikować lub usunąć swoje dane.

W tym artykule opisano funkcje zabezpieczeń krawędź pola danych, które pomagają w ochronie poszczególne składniki rozwiązania oraz danych przechowywanych na nich.

Rozwiązanie usługi Azure Data Box Edge składa się z czterech głównych składników, które współdziałają ze sobą:

- **Krawędź pola danych / usługi bramy pola danych hostowanych na platformie Azure** — zasób zarządzania, który umożliwia tworzenie kolejności urządzeń, konfigurowanie urządzenia i śledzić kolejności, aby ukończenie.
- **Urządzenie brzegowe pole danych** — urządzenie do transferu, który jest dostarczany w celu zaimportowania danych lokalnych na platformę Azure.
- **Klienci/hosty są połączone z urządzeniem** — klientów w infrastrukturze, połączyć się z urządzeniem krawędź pola danych, które zawierają dane, które muszą być chronione.
- **Magazyn w chmurze** — lokalizacja w chmurze platformy Azure, w której są przechowywane dane. Ta lokalizacja jest zazwyczaj konto magazynu, połączone z zasobu krawędź pola danych, który został utworzony.


## <a name="data-box-edgedata-box-gateway-service-protection"></a>Ochrona usługi bramy okno usługi Edge i dane pole danych

Usługa bramy okno usługi Edge i dane pole danych jest usługą zarządzania hostowanych na platformie Microsoft Azure. Usługa jest używana do konfigurowania i zarządzania urządzeniem.

- Uzyskiwanie dostępu do usług bramy okno usługi Edge i dane pole danych wymaga Twoja organizacja ma umowy Enterprise Agreement (EA) lub w ramach subskrypcji Cloud Solution Provider (CSP). Aby uzyskać więcej informacji, przejdź do [Zamów subskrypcję platformy Azure](https://azure.microsoft.com/resources/videos/sign-up-for-microsoft-azure/)!
- Ponieważ usługi zarządzania jest hostowany na platformie Azure, jest chroniony przy użyciu funkcji zabezpieczeń platformy Azure. Aby uzyskać więcej informacji na temat funkcji zabezpieczeń platformy Microsoft Azure, zobacz [Centrum zaufania Microsoft Azure](https://azure.microsoft.com/support/trust-center/security/).
- Dla operacji zarządzania zestawu SDK, klucz szyfrowania będzie dostępny dla Twojej przeglądarki Edge urządzenia Data Box / bramy pola danych zasobami dostępnymi w ramach **właściwości urządzenia**. Klucz szyfrowania można wyświetlić tylko wtedy, gdy masz uprawnienia do interfejsu API programu Graph zasobów.

## <a name="data-box-edge-device-protection"></a>Ochrona urządzeń krawędź pola danych

Urządzenie brzegowe pole danych jest lokalnym urządzeniem, która pomaga przekształcać dane, przetwarza go lokalnie, a następnie wysyłając je na platformie Azure. Urządzenia:

- Wymaga klucza aktywacji dostępu do usługi bramy okno usługi Edge i dane pole danych.
- Jest chroniony na cały czas hasła urządzenia.
- To urządzenie w trybie blokady. Urządzenie BMC i systemu BIOS są chronione hasłem z ograniczonym dostępem użytkownika systemu BIOS.
- Bezpieczny rozruch został włączony.
- Uruchamia program Windows Defender Device Guard. Funkcja Device Guard umożliwia uruchamianie tylko zaufane aplikacje zdefiniowane w zasadach integralności kodu.
- Ma klucz wewnątrz okładce, który może służyć do blokowania urządzenia. Firma Microsoft zaleca się, że po skonfigurowaniu urządzenia, otwórz okładki. Znajdź klucz, a następnie zablokować cover, aby uniemożliwić każdego nieautoryzowanego dostępu do dysków z danymi znajduje się uzyskać urządzenia.

### <a name="protect-the-device-via-activation-key"></a>Ochrona urządzeń za pomocą klucza aktywacji

Tylko autoryzowanym dane pole urządzenia usługi Edge jest dozwolony do przyłączenia się do usługi bramy okno usługi Edge i dane pole danych, który został utworzony w ramach subskrypcji platformy Azure. Aby autoryzować urządzenia, trzeba użyć klucza aktywacji do aktywacji urządzenia przy użyciu usługi bramy okno usługi Edge i dane pole danych. Aby uzyskać więcej informacji, przejdź do [uzyskiwanie klucza aktywacji](data-box-edge-deploy-prep.md#get-the-activation-key).

Kluczem aktywacji, który jest używany:

- Jest to klucz uwierzytelniania przy użyciu usługi Azure Active Directory (AAD).
- Wygasa po upływie trzech dni.
- Nie jest używany po aktywacji urządzenia.
 
Po aktywowaniu urządzenia używane są tokeny do komunikowania się z platformą Microsoft Azure.

### <a name="protect-the-device-via-password"></a>Ochrona urządzeń za pomocą hasła

Haseł upewnij się, że Twoje dane są dostępne tylko dla autoryzowanych użytkowników. Krawędź pola danych i bramy pola danych urządzenia rozruchu w stanie zablokowanym.

Możesz:

- Nawiązać połączenie z lokalnego interfejsu użytkownika urządzenia za pośrednictwem przeglądarki sieci web, a następnie podaj hasło do logowania się do urządzenia.
- Zdalne łączenie się z interfejsu programu PowerShell urządzenia za pośrednictwem protokołu HTTP. Zdalne zarządzanie jest domyślnie włączona. Następnie możesz podać hasło urządzenia, aby zalogować się do urządzenia. Aby uzyskać więcej informacji, przejdź do [Connect zdalnie na urządzenia brzegowe pole danych](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

Należy pamiętać poniższe najlepsze rozwiązania:

- Usługa krawędź pola danych nie można pobrać istniejących haseł: je mogą tylko je zresetować za pośrednictwem witryny Azure portal. Zaleca się przechowywanie wszystkie hasła w bezpiecznym miejscu, tak, aby nie trzeba zresetować hasło, zapomnienia jest. Jeśli możesz zresetować hasła, pamiętaj powiadomić wszystkich użytkowników, zanim je zresetować.
- Użyj lokalnego internetowego interfejsu użytkownika do [zmiany hasła](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access). Jeśli zmienisz hasło, pamiętaj powiadomić wszystkich użytkowników dostępu zdalnego tak, aby nie będą dotykać logowania awarii.
- Możesz zdalnie dostęp do interfejsu programu Windows PowerShell urządzenia, za pośrednictwem protokołu HTTP. Ze względów bezpieczeństwa HTTP należy używać tylko w sieciach zaufanych.
- Upewnij się, że hasła urządzenia silnych i dobrze chronione. Postępuj zgodnie z [najlepsze rozwiązania dotyczące haseł](https://docs.microsoft.com/azure/security/azure-security-identity-management-best-practices#enable-password-management).

## <a name="protect-the-data"></a>Ochrona danych

W tej sekcji opisano funkcje zabezpieczeń krawędź pola danych, które ochrony danych przesyłanych i przechowywanych danych.

### <a name="protect-data-at-rest"></a>Ochrona magazynowanych danych

Dla danych magazynowanych:

- Dla danych magazynowanych krawędź pola danych używa szyfrowania funkcją BitLocker XTS-AES-256 do ochrony danych lokalnych.
- W przypadku danych, która znajduje się w udziałach dostępu do udziałów jest ograniczona.

    - Dla klientów protokołu SMB uzyskujących dostęp do danych udziału muszą one poświadczenia użytkownika skojarzonego z udziałem. Te poświadczenia są definiowane w czasie tworzenia udziału.
    - Dla klientów systemu plików NFS, uzyskujących dostęp do udziałów adresy IP klientów, należy dodać w czasie tworzenia udziału.


### <a name="protect-data-in-flight"></a>Ochrona danych w locie

Aby uzyskać dane w lotu:

- Dane, które przechodzi między urządzeniem a platformą Azure używany jest standardowy protokół TLS 1.2. Brak rezerwowe, nie protokołu TLS 1.1 i starszych wersji. Komunikacja agenta zostanie zablokowane, jeśli nie jest obsługiwany protokół TLS 1.2. Wymagany dla portalu i operacje zestawu SDK jest również protokołu TLS 1.2.
- Gdy klienci uzyskują dostęp do urządzenia za pomocą lokalnego internetowego interfejsu użytkownika w przeglądarce, standardowy protokół TLS 1.2 jest używana jako domyślna bezpiecznego protokołu.

    - Najlepszym rozwiązaniem jest skonfigurowanie przeglądarki do użycia protokołu TLS 1.2.
    - Jeśli przeglądarka nie obsługuje protokołu TLS 1.2, można użyć protokołu TLS 1.1 i TLS 1.0.
- Aby chronić dane podczas kopiowania z serwerów danych, zaleca się korzystania z protokołu SMB 3.0 za pomocą szyfrowania.

### <a name="protect-data-via-storage-accounts"></a>Ochrona danych za pomocą konta magazynu

Urządzenie jest skojarzone z kontem magazynu, który jest używany jako miejsce docelowe dla danych na platformie Azure. Dostęp do konta magazynu jest kontrolowany przez subskrypcję i dwa magazynu 512-bitowe klucze skojarzone z tym kontem magazynu dostępu.

Jeden z kluczy jest używany do uwierzytelniania, gdy urządzenie brzegowe pole danych uzyskuje dostęp do konta magazynu. Drugi klucz jest przechowywany w rezerwie, dzięki czemu możesz okresowo wymiany kluczy.

Ze względów bezpieczeństwa wiele centrów danych wymagają wymiany kluczy. Firma Microsoft zaleca, aby wykonać te najlepsze praktyki dotyczące wymiany kluczy:

- Klucz konta magazynu jest podobny do hasła głównego konta magazynu. Starannie Chroń klucz konta. Nie dystrybucji haseł do innych użytkowników, ciężko zakodować go lub zapisać go w dowolnym miejscu w postaci zwykłego tekstu, który jest dostępny dla innych użytkowników.
- [Wygeneruj ponownie klucz konta](../storage/common/storage-account-manage.md#regenerate-access-keys) przy użyciu witryny Azure portal, jeśli Twoim zdaniem mogą zabezpieczenia mogły zostać naruszone.
- Obróć i następnie [synchronizowanie kluczy konta magazynu](data-box-gateway-manage-shares.md#sync-storage-keys) regularnie, aby pomóc w zapewnieniu, że konto magazynu nie jest używane przez nieautoryzowanych użytkowników.
- Okresowo administratorem platformy Azure, należy zmienić lub ponownie wygenerować klucz podstawowy lub pomocniczy przy użyciu sekcji magazyn w witrynie Azure Portal na bezpośredni dostęp do konta magazynu.


## <a name="manage-personal-information"></a>Zarządzanie informacji osobistych

Krawędź pola danych / usługi bramy pola danych zbiera informacje osobiste w następujących przypadkach klucza:

- **Szczegóły zamówienia** — po utworzeniu zamówienia na platformie Azure zostaną zapisane dane kontaktowe użytkowników, adres wysyłki, adres e-mail. Zapisane informacje obejmują:
  - Nazwisko osoby kontaktowej
  - Numer telefonu
  - Email
  - Adres
  - Miasto
  - Kod pocztowy
  - Stan
  - Kraj/Województwo/Region
  - Numer śledzenia dostawy

    Szczegóły zamówienia są zaszyfrowane i przechowywane w usłudze. Usługa zachowuje te informacje, dopóki nie usuniesz jawnie zasobu lub zamówienia. Ponadto usunięcie zasobu i odpowiadającego im zamówienia jest zablokowana od chwili, gdy urządzenie jest dostarczany, dopóki urządzenie powraca do firmy Microsoft.

- **Adres wysyłkowy** — po umieszczeniu kolejności, usługa Data Box zapewnia adres wysyłkowy operatorów innych firm, takich jak UPS.

- **Udostępnianie użytkownikom** — użytkownicy na urządzeniu z systemem również mogą uzyskiwać dostęp do danych znajdujących się w udziałach. Lista użytkowników, którzy mają dostęp do danych udziału jest wyświetlany i mogą być wyświetlane. Ta lista są także usuwane, gdy udziały zostaną usunięte. Aby wyświetlić listę użytkowników, którzy mogą uzyskać dostęp, lub usunąć udział, wykonaj kroki opisane w [Zarządzanie udziałami na krawędzi pola danych](data-box-gateway-manage-shares.md).

Aby uzyskać więcej informacji, zapoznaj się z Zasadami ochrony prywatności firmy Microsoft w [Centrum zaufania](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Kolejne kroki

[Wdrażanie urządzenia krawędź pola danych](data-box-edge-deploy-prep.md).

