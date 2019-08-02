---
title: Azure Key Vault w Australii platformy Azure
description: Wskazówki dotyczące konfigurowania i używania Azure Key Vault zarządzania kluczami w regionach australijskich w celu spełnienia określonych wymagań australijskich zasad, przepisów i przepisów obowiązujących dla instytucji rządowych.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 263765c777f994134befc52f0c63c7f18e590b39
ms.sourcegitcommit: 15f7b641a67f3d6cf4fb4b4c11eaee18cf335923
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602115"
---
# <a name="azure-key-vault-in-azure-australia"></a>Azure Key Vault w Australii platformy Azure

Bezpieczny Magazyn kluczy kryptograficznych i zarządzanie cyklem życia klucza kryptograficznego są istotnymi elementami w systemach kryptograficznych.  Usługa, która zapewnia tę możliwość na platformie Azure, to Azure Key Vault. Key Vault IRAP zabezpieczenia i ACSC certyfikat do ochrony.  W tym artykule opisano kluczowe zagadnienia związane z używaniem Key Vault w celu zachowania zgodności z przepisami dotyczącymi [kontroli ręcznej zabezpieczeń](https://acsc.gov.au/infosec/ism/) (ASD) w firmie.

Azure Key Vault to usługa w chmurze, która chroni klucze szyfrowania i wpisy tajne. Ponieważ te dane są poufne i krytyczne dla działania firmy, Key Vault umożliwia bezpieczny dostęp do magazynów kluczy, zezwalając tylko na autoryzowanych użytkowników i aplikacje. Istnieją trzy główne artefakty zarządzane i kontrolowane przez Key Vault:

- klucze
- wpisy tajne
- Certyfikaty

Ten artykuł koncentruje się na zarządzaniu kluczami przy użyciu Key Vault.

![W usłudze Azure Key Vault](media/azure-key-vault-overview.png)

*Diagram 1 — Azure Key Vault*

## <a name="key-design-considerations"></a>Podstawowe zagadnienia dotyczące projektowania

### <a name="deployment-options"></a>Opcje wdrożenia

Dostępne są dwie opcje tworzenia magazynów kluczy Azure. Obie opcje używają rodziny firmy Thales sprzętowego nshield sprzętowych modułów zabezpieczeń (HSM), są weryfikowane przez standardy FIPS (Federal Information Processing Standards) i są zatwierdzone do przechowywania kluczy w środowiskach chronionych. Dostępne opcje to:

- **Magazyny chronione oprogramowaniem:** Zweryfikowano poziom 1 trybu FIPS 140-2. Klucze przechowywane w module HSM. Operacje szyfrowania i odszyfrowywania są wykonywane w zasobach obliczeniowych na platformie Azure.
- **Magazyny chronione przez moduł HSM:** Zweryfikowano poziom 2 trybu FIPS 140-2. Klucze przechowywane w module HSM. Operacje szyfrowania i odszyfrowywania są wykonywane w module HSM.

Key Vault obsługuje klucze Rivest-Shamir-Adleman (RSA) i kryptografii eliptycznej (ECC). Wartość domyślna to RSA 2048-bitowe klucze, ale istnieje zaawansowana opcja dla RSA 3072-bit, RSA 4096-bit i klucz ECC.  Wszystkie klucze spełniają kontrolki ISM, ale preferowane są klucze krzywej eliptycznej.

### <a name="resource-operations"></a>Operacje zasobów

Istnieje kilka osóbów uwzględnionych w Azure Key Vault:

- **Key Vault administrator:** Zarządza cyklem życia magazynu
- **Administrator klucza:** Zarządza cyklem życia kluczy w magazynie
- **Deweloper/operator:** Integruj klucze z magazynu z aplikacjami i usługami
- **Kontroler** Monitorowanie użycia klucza i dostępu
- **Zastosowania** Zabezpieczanie informacji przy użyciu kluczy

Azure Key Vault jest zabezpieczony przy użyciu dwóch oddzielnych interfejsów:

- **Płaszczyzna zarządzania:** Ta płaszczyzna zajmuje się zarządzaniem magazynem i zabezpieczonym przez RBAC.
- **Płaszczyzna danych:** Ta płaszczyzna dotyczy zarządzania artefaktami w magazynie i uzyskiwania do nich dostępu.  Zabezpieczony przy użyciu zasad dostępu Key Vault.

Zgodnie z wymaganiami ISM, odpowiednie uwierzytelnienie i autoryzacja są wymagane przed obiektem wywołującym (użytkownikiem lub aplikacją) przed uzyskaniem dostępu do magazynu kluczy przez każdą płaszczyznę.

Usługa Azure RBAC ma jedną wbudowaną rolę dla Key Vault, [Key Vault współautor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#key-vault-contributor), aby kontrolować Zarządzanie magazynami kluczy. Zaleca się utworzenie ról niestandardowych wyrównanych do bardziej szczegółowych ról do zarządzania magazynami kluczy.

>[!WARNING]
>Gdy dostęp do kluczy jest włączany za pośrednictwem zasad dostępu Key Vault, użytkownik lub aplikacja ma dostęp do wszystkich kluczy w magazynie kluczy (na przykład jeśli użytkownik ma dostęp "Usuń", może usunąć wszystkie klucze).  W związku z tym należy wdrożyć wiele magazynów kluczy, aby dostosować je do domen/granic zabezpieczeń.

### <a name="networking"></a>Networking

Można skonfigurować zapory Key Vault i sieci wirtualne w celu kontrolowania dostępu do płaszczyzny danych.  Możesz zezwolić na dostęp do użytkowników lub aplikacji w określonych sieciach, pozostawiając dostęp do użytkowników lub aplikacji we wszystkich innych sieciach. [Usługi zaufane](https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints#trusted-services) są wyjątkiem od tej kontrolki, jeśli włączono opcję "Zezwalaj na zaufane usługi".  Kontrolka sieci wirtualnej nie ma zastosowania do płaszczyzny zarządzania.

Dostęp do magazynów kluczy powinien być jawnie ograniczony do minimalnego zestawu sieci, które mają użytkowników lub aplikacje wymagające dostępu do kluczy.

### <a name="bring-your-own-key-byok"></a>Bring Your Own Key (BYOK)

Key Vault obsługuje BYOK.  BYOK umożliwia użytkownikom Importowanie kluczy z istniejących infrastruktury kluczy.  Firmy Thales udostępnia [australijski zestaw narzędzi](https://www.microsoft.com/download/details.aspx?id=45345) do obsługi bezpiecznego transferu i importowania kluczy z zewnętrznego modułu HSM (na przykład kluczy generowanych za pomocą stacji roboczej offline) do Key Vault.

### <a name="key-vault-auditing-and-logging"></a>Inspekcja Key Vault i rejestrowanie

ACSC wymaga, aby jednostki Wspólnoty korzystały z odpowiednich usług platformy Azure w celu monitorowania i raportowania obciążeń związanych z platformą Azure w czasie rzeczywistym.

Rejestrowanie jest włączane przez włączenie ustawienia diagnostycznego **_"AuditEvent"_** dla wartości klucza.  Zdarzenia inspekcji będą rejestrowane na określonym koncie magazynu.  Należy ustawić okres **_"RetentionInDays"_** zgodnie z zasadami przechowywania danych.  [Operacje](https://docs.microsoft.com/azure/key-vault/key-vault-logging#interpret) na płaszczyźnie zarządzania i płaszczyzny danych są poddawane inspekcji i rejestrowane. [Azure Key Vault rozwiązanie w Azure monitor](https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault) może służyć do Key Vault przeglądania dzienników AuditEvent.  Wiele innych usług platformy Azure może służyć do przetwarzania i dystrybucji Key Vault AuditEvents.

### <a name="key-rotation"></a>Wymiana kluczy

Przechowywanie kluczy w Key Vault zapewnić jeden punkt do obsługi kluczy poza aplikacjami, które umożliwiają aktualizowanie kluczy bez wpływu na zachowanie aplikacji. Przechowywanie kluczy w Azure Key Vault umożliwia wykonywanie różnych strategii w celu obsłudze rotacji kluczy:

- Ręcznie
- Programowo za pośrednictwem interfejsów API
- Skrypty automatyzacji (na przykład przy użyciu programu PowerShell i Azure Automation)

Te opcje umożliwiają okresowe obracanie kluczy w celu spełnienia wymagań dotyczących zgodności lub na zasadzie ad hoc w przypadku problemów z naruszeniami kluczy.

#### <a name="key-rotation-strategies"></a>Strategie rotacji kluczy

Ważne jest opracowanie odpowiedniej strategii rotacji kluczy dla kluczy przechowywanych w magazynie kluczy.  Użycie nieprawidłowego klucza prowadzi do niepoprawnego odszyfrowywania informacji, a utrata kluczy może prowadzić do całkowitego utraty dostępu do informacji.  Przykłady strategii rotacji kluczy dla różnych scenariuszy obejmują:

- **Dane numerów porządkowych określających:** informacje nietrwałe są przesyłane między 2 stronami.  Gdy klucz jest obrócony, obie strony muszą mieć mechanizm synchronicznego pobierania zaktualizowanych kluczy z magazynu kluczy.
- **Dane jako REST:** Strona przechowuje zaszyfrowane dane i odszyfrowuje ją w przyszłości do użycia.  Gdy klucz ma zostać obrócony, dane muszą zostać odszyfrowane przy użyciu starego klucza, a następnie zaszyfrowane przy użyciu nowego, obróconego klucza.  Istnieją podejścia do minimalizowania wpływu procesu odszyfrowywania/szyfrowania przy użyciu kluczy szyfrowania kluczy (Zobacz przykład).  Firma Microsoft zarządza większością procesów związanych z rotacją kluczy dla usługi Azure Storage (zobacz...)
- **Klucze dostępu:** kilka usług platformy Azure ma klucze dostępu, które mogą być przechowywane w Key Vault (na przykład CosmosDB).  Usługi platformy Azure mają podstawowy i pomocniczy klucz dostępu.  Należy pamiętać, że oba klucze nie są jednocześnie obracane.  W związku z tym jeden klucz powinien zostać obrócony po upływie okresu, a operacja klucza została zweryfikowana, drugi klucz można obrócić.

### <a name="high-availability"></a>Wysoka dostępność

ISM ma kilka kontrolek odnoszących się do ciągłości biznesowej.
Azure Key Vault ma wiele warstw nadmiarowości z zawartością replikowaną w regionie i do pomocniczego, [sparowanego regionu](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

Gdy Magazyn kluczy jest w stanie awarii, jest w trybie tylko do odczytu i wróci do trybu odczytu i zapisu, a usługa podstawowa zostanie przywrócona.

ISM ma kilka kontrolek związanych z tworzeniem kopii zapasowych.  Ważne jest, aby opracować i wykonać odpowiednie plany tworzenia i przywracania kopii zapasowych dla magazynów oraz ich kluczy.

## <a name="key-lifecycle"></a>Cykl życia klucza

### <a name="key-operations"></a>Najważniejsze operacje

Key Vault obsługiwać następujące operacje na kluczu:

- **Create** Umożliwia klientowi utworzenie klucza w Key Vault. Wartość klucza jest generowana przez Key Vault i jest przechowywana i nie jest wydawana dla klienta. Klucze asymetryczne mogą być tworzone w Key Vault.
- **zaimportować** Umożliwia klientowi Importowanie istniejącego klucza do Key Vault. Klucze asymetryczne mogą być importowane do Key Vault przy użyciu wielu różnych metod pakowania w ramach konstrukcji JWK.
- **aktualizacji** Umożliwia klientowi z wystarczającymi uprawnieniami do modyfikowania metadanych (atrybutów kluczowych) skojarzonych z kluczem, który został wcześniej zapisany w Key Vault.
- **usunięty** Zezwala klientowi z odpowiednimi uprawnieniami do usuwania klucza z Key Vault.
- **staw** Umożliwia klientowi Wyświetlanie listy wszystkich kluczy w danym Key Vault.
- **wersje list:** Umożliwia klientowi Wyświetlanie listy wszystkich wersji danego klucza w danym Key Vault.
- **Pobierz:** Zezwala klientowi na pobieranie publicznych części danego klucza w Key Vault.
- **kopii zapasowej** Eksportuje klucz w formularzu chronionym.
- **przywracania** Importuje poprzednio kopię zapasową klucza.

Istnieje odpowiedni zestaw uprawnień, które mogą być przyznawane użytkownikom, podmiotom usług lub aplikacjom przy użyciu Key Vault wpisów kontroli dostępu, aby umożliwić im wykonywanie kluczowych operacji.

W Key Vault jest dostępna funkcja usuwania nietrwałego umożliwiająca Odzyskiwanie usuniętych magazynów i kluczy. Domyślnie **_"usuwanie nietrwałe"_** nie jest włączone, ale po włączeniu obiektów są przechowywane przez 90 dni (okres przechowywania), które pojawiają się do usunięcia.  Dodatkowe uprawnienie **_"przeczyszczanie"_** umożliwia trwałe usuwanie kluczy, jeśli opcja **_"przeczyszczanie ochrony"_** jest wyłączona.

Utworzenie lub zaimportowanie istniejącego klucza powoduje utworzenie nowej wersji klucza.

### <a name="cryptographic-operations"></a>Operacje kryptograficzne

Key Vault obsługuje również operacje kryptograficzne przy użyciu kluczy:

- **Podpisz i sprawdź:** ta operacja jest "skrótem znaku" lub "Weryfikuj skrót". Key Vault nie obsługuje mieszania zawartości w ramach tworzenia podpisu.
- **szyfrowanie/otoka klucza:** ta operacja służy do ochrony innego klucza.
- **szyfrowanie i odszyfrowywanie:** przechowywany klucz jest używany do szyfrowania lub odszyfrowywania pojedynczego bloku danych

Istnieje odpowiedni zestaw uprawnień, które mogą być przyznawane użytkownikom, podmiotom usług lub aplikacjom przy użyciu Key Vault wpisów kontroli dostępu, aby umożliwić im wykonywanie operacji kryptograficznych.

Istnieją trzy kluczowe atrybuty, które można ustawić, aby kontrolować, czy klucz jest włączony i w ramach operacji kryptograficznych:

- **dostępny**
- Protokół **NBF:** nie należy włączać przed określoną datą
- **exp:** Data wygaśnięcia

## <a name="storage-and-keys"></a>Magazyn i klucze

Klucze zarządzane przez klienta zapewniają większą elastyczność i umożliwiają ocenę i zarządzanie kluczami, które mają być kontrolowane. Włączają także inspekcję kluczy szyfrowania używanych do ochrony danych.
Istnieją trzy aspekty magazynowania i kluczy przechowywanych w Key Vault:

- Key Vault kluczy zarządzanego konta magazynu
- Azure szyfrowanie usługi Storage (SSE) dla danych przechowywanych w spoczynku
- Dyski zarządzane i Azure Disk Encryption

Zarządzanie kluczami w ramach konta usługi Azure Storage Key Vault to rozszerzenie klucza Key Vault, które obsługuje synchronizację i ponowne generowanie (rotacja) kluczy konta magazynu.  [Integracja z usługą Azure Storage z usługą Azure Active Directory](https://docs.microsoft.com/azure/storage/common/storage-auth-aad) (wersja zapoznawcza) jest zalecana, gdy zostanie wydana, ponieważ zapewnia ona doskonałe zabezpieczenia i łatwość użycia.
Funkcja SSE używa dwóch kluczy do zarządzania szyfrowaniem danych w spoczynku:

- Klucze szyfrowania kluczy (KEK)
- Klucze szyfrowania danych

Podczas gdy firma Microsoft zarządza DEKs, funkcja SSE ma możliwość używania KEKs zarządzanych przez klienta, które mogą być przechowywane w Key Vault. Dzięki temu można obrócić klucze w Azure Key Vault zgodnie z odpowiednimi zasadami zgodności. Gdy klucze są obracane, usługa Azure Storage ponownie szyfruje klucz szyfrowania konta dla tego konta magazynu. Nie powoduje to ponownego szyfrowania wszystkich danych i nie jest wymagana żadna inna akcja.

Funkcja SSE jest używana w przypadku dysków zarządzanych, ale klucze zarządzane przez klienta nie są obsługiwane.  Szyfrowanie dysków zarządzanych można wykonać przy użyciu Azure Disk Encryption z kluczami KEK zarządzanymi przez klienta w programie Key Vault.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z artykułem dotyczącym [federacji tożsamości](identity-federation.md)

Zapoznaj się z dodatkową dokumentacją i samouczkami Azure Key Vault w [bibliotece referencyjnej](reference-library.md)
