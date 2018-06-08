---
title: Microsoft Azure danych szyfrowania podczas spoczynku | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie Microsoft Azure danych szyfrowania podczas spoczynku, ogólną możliwości i zagadnienia ogólne.
services: security
documentationcenter: na
author: barclayn
manager: mbaldwin
editor: TomSh
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2018
ms.author: barclayn
ms.openlocfilehash: fa03d62a3125b3bf8f23a53903a733dbec8ea662
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34839467"
---
# <a name="azure-data-encryption-at-rest"></a>Danych Azure szyfrowania na Rest

Microsoft Azure oferuje narzędzia do ochrony danych zgodnie z potrzebami firmy zabezpieczeń i zgodności. Ten dokument koncentruje się na:

- Jak dane są chronione w stanie spoczynku w Microsoft Azure
- W tym artykule omówiono różnych składników biorących udział w implementacji ochrony danych,
- Przegląda zalet i wad zarządzania kluczami różnych metod ochrony. 

Szyfrowanie Rest jest typowe wymagania dotyczące zabezpieczeń. Na platformie Azure organizacji można osiągnąć szyfrowanie magazynowanych bez konieczności koszt wdrożenia i zarządzania oraz ryzyko niestandardowe klucza rozwiązania do zarządzania. Organizacje mają możliwość umożliwienie zarządzania szyfrowanie magazynowanych Azure. Ponadto organizacje mają różne opcje, aby ściśle zarządzać szyfrowania lub kluczy szyfrowania.

## <a name="what-is-encryption-at-rest"></a>Co to jest szyfrowanie magazynowanych?

Szyfrowanie Rest jest kodowania (szyfrowanie) danych, gdy jest ona utrwalona. Szyfrowanie w projektach Rest w usłudze Azure szyfrowania symetrycznego do szyfrowania i odszyfrowywania dużych ilości danych szybko zgodnie z prostego modelu koncepcyjnego:

- Klucz szyfrowania symetrycznego jest używany do szyfrowania danych, ponieważ jest ona zapisywana w pamięci masowej. 
- Taki sam klucz szyfrowania jest używany do odszyfrowywania danych, ponieważ jest on readied do użycia w pamięci.
- Dane mogą być podzielone na partycje i może być używane różne klucze dla każdej partycji.
- Klucze muszą być przechowywane w bezpiecznej lokalizacji z zasady kontroli dostępu, ograniczając dostęp do niektórych tożsamości i rejestrowanie użycia klucza. Klucze szyfrowania danych często są szyfrowane przy użyciu szyfrowania asymetrycznego Aby bardziej ograniczyć dostęp.

W praktyce najważniejsze scenariusze zarządzania i kontroli, jak również skalowalność i dostępność gwarancji, wymagają dodatkowych konstrukcje. Microsoft Azure szyfrowanie Rest pojęcia i składniki są opisane poniżej.

## <a name="the-purpose-of-encryption-at-rest"></a>Celem szyfrowanie magazynowanych

Szyfrowanie magazynowanych zapewnia ochronę danych przechowywanych danych (w stanie spoczynku). Ataków na dane na rest obejmują próbuje uzyskać dostęp fizyczny do sprzętowych, na którym dane są przechowywane i naruszyć bezpieczeństwo danych zawartych w niej. W przypadku ataków dysk twardy serwera może mieć zostały niewłaściwego stosowania podczas konserwacji, co pozwala osobie atakującej usunięcia dysku twardego. Później, osoba atakująca spowodowałaby dysku twardego do komputera pod kontrolą tych prób uzyskania dostępu do danych. 

Szyfrowanie magazynowanych zaprojektowano w celu uniemożliwić osobie atakującej dostęp do niezaszyfrowane dane przez zapewnienie im dane są szyfrowane, gdy na dysku. Jeżeli osobie atakującej uzyskanie dysk twardy z takich zaszyfrowanych danych i Brak dostępu do kluczy szyfrowania, osoba atakująca może naruszać nie danych bez ogromne trudności. W takiej sytuacji osoba atakująca próby ataków na zaszyfrowane dane, które są bardziej złożone i korzystanie z zasobów niż dostęp do bez szyfrowania danych na dysku twardym. Z tego powodu szyfrowania magazynowane zdecydowanie zaleca się i jest wymagany w przypadku wielu organizacji o wysokim priorytecie. 

Szyfrowanie magazynowanych może być również wymagane przez organizacji potrzebę danych zarządzania i zgodności działań. Branżowych i rządowych USA przepisami, takie jak HIPAA i PCI FedRAMP, układ konkretnych zabezpieczeń dotyczące wymagania dotyczące ochrony i szyfrowania danych. Szyfrowanie rest jest obowiązkowy miary wymaganej pod kątem zgodności niektóre z tych rozporządzeń.

Oprócz zgodnością i przepisami dotyczącymi szyfrowanie magazynowanych powinien być traktowany jako możliwości platformy obrony zabezpieczeń. Firma Microsoft udostępnia platformy zgodne w przypadku aplikacji, usług i danych, kompleksowe funkcje i zabezpieczenia fizyczne, inspekcji i kontroli dostępu do danych, ważne jest, aby zapewnić dodatkowe środki bezpieczeństwa "nakładające się" w przypadku jednego z innych środki bezpieczeństwa kończy się niepowodzeniem. Szyfrowanie magazynowane zapewnia dodatkową ochronę mechanizm.

Firma Microsoft dokłada starań, aby udostępniać szyfrowanie opcje rest przez usługi w chmurze i aby zapewnić klientom zarządzania odpowiedniego klucza szyfrowania i uzyskiwanie dostępu do dzienników wyświetlane, gdy klucze szyfrowania są używane. Ponadto firma Microsoft współpracuje kierunku celem dokonywania szyfrowane, gdy domyślnie wszystkich danych klientów.

## <a name="azure-encryption-at-rest-components"></a>Szyfrowanie Azure w pozostałej części

Jak opisano wcześniej, celem szyfrowanie magazynowanych jest szyfrowanie danych, która jest utrwalony na dysku za pomocą klucza tajnego szyfrowania. Aby osiągnąć ten cel bezpiecznego tworzenia klucza, należy podać magazynu, kontroli dostępu i zarządzanie kluczami szyfrowania. Chociaż może się różnić szczegóły usług Azure szyfrowania w implementacji Rest można opisane w warunki przedstawiony na poniższym diagramie.

![Składniki](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>W usłudze Azure Key Vault

Lokalizacja magazynu kluczy szyfrowania i kontroli dostępu do tych kluczy jest podstawą do szyfrowania w modelu rest. Klucze muszą wysoce zabezpieczonych, ale zarządzane przez określonych użytkowników i udostępnienie jej do określonych usług. Dla usług Azure Azure Key Vault to rozwiązanie zalecane magazynu kluczy i udostępnia typowe możliwości zarządzania w usługach. Klucze są przechowywane i zarządzane w magazynów kluczy, a dostęp do magazynu kluczy można przydzielić do użytkowników lub usług. Usługa Azure Key Vault obsługuje tworzenie odbiorcy kluczy lub importowanie kluczy odbiorcy w scenariuszach klucza szyfrowania zarządzany przez klienta.

### <a name="azure-active-directory"></a>Usługa Azure Active Directory

Uprawnienia do zarządzania lub uzyskać do nich dostęp do szyfrowania w Rest szyfrowania i odszyfrowywania, klucze przechowywane w usłudze Azure Key Vault, można przydzielić do konta usługi Azure Active Directory. 

### <a name="key-hierarchy"></a>Hierarchia klucza

Więcej niż jeden klucz szyfrowania jest używany podczas szyfrowania w implementacji rest. Szyfrowanie asymetryczne przydaje się do ustanawiania relacji zaufania i uwierzytelniania wymagany dostęp do kluczy i zarządzania. Szyfrowanie symetryczne jest bardziej wydajny zbiorczego szyfrowania i odszyfrowywania, co zapewnia lepszą wydajność i silniejszego szyfrowania. Ograniczanie użycia klucza szyfrowania pojedynczego zmniejsza ryzyko, że klucz zostanie naruszony i koszt ponownego szyfrowania, po klucz muszą zostać zastąpione. Szyfrowanie Azure w modelach rest Użyj klucza hierarchii składają się z następujących typów kluczy:

- **Dane klucza szyfrowania** — AES256 klucza symetrycznego służącego do szyfrowania partycji lub bloku danych.  Pojedynczy zasób może mieć wiele partycji i wiele kluczy szyfrowania danych. Szyfrowanie każdy blok danych za pomocą innego klucza utrudnia ataków kryptograficznych analizy. Dostęp do DEKs jest wymagane przez dostawcę lub aplikacji wystąpienia zasobu szyfrowania i odszyfrowywania określonego bloku. Po klucz szyfrowania danych zostanie zastąpiony przy użyciu nowego klucza tylko dane w jego skojarzony blok musi być ponownie szyfrować za pomocą nowego klucza.
- **Klucz szyfrowania klucza (KEK)** — klucza asymetrycznego szyfrowania używany do szyfrowania kluczy szyfrowania danych. Klucz szyfrowania klucza umożliwiają siebie powinny być szyfrowane i kontrolowane kluczy szyfrowania danych. Jednostka, która ma dostęp do klucza KEK mogą być inne niż jednostki, która wymaga klucza szyfrowania danych. Jednostka może broker dostępu do klucza szyfrowania danych, ograniczyć dostęp do każdego klucza szyfrowania danych na określoną partycję. Ponieważ KEK jest wymagany do odszyfrowania DEKs, klucza KEK skutecznie jest pojedynczym punktem za pomocą którego DEKs mogą być skutecznie usuwane przez usunięcie klucza KEK.

Klucze szyfrowania danych zaszyfrowanych za pomocą klucza klucze szyfrowania są przechowywane osobno i tylko jednostki z dostępu do klucza szyfrowania klucza można pobrać klucze szyfrowania danych zaszyfrowanych za pomocą tego klucza. Obsługiwane są różne modele magazynu kluczy. Omówimy każdego modelu bardziej szczegółowo w dalszej części następnej sekcji.

## <a name="data-encryption-models"></a>Modele szyfrowania danych

Opis różnych modeli szyfrowania i ich zalet i wad jest istotne dla zrozumienia, jak różnych dostawców zasobów na platformie Azure wdrożenia szyfrowania w stanie spoczynku. Te definicje są współdzielone przez wszystkich dostawców zasobów na platformie Azure, aby zapewnić wspólnego języka i taksonomii. 

Istnieją trzy scenariusze szyfrowania po stronie serwera:

- Szyfrowanie po stronie serwera przy użyciu usługi zarządzania kluczami
    - Dostawcy zasobów platformy Azure wykonywać operacje szyfrowania i odszyfrowywania
    - Firma Microsoft zarządza kluczy
    - Chmura pełna funkcjonalność

- Szyfrowanie po stronie serwera za pomocą kluczy zarządzany przez klienta w usłudze Azure Key Vault
    - Dostawcy zasobów platformy Azure wykonywać operacje szyfrowania i odszyfrowywania
    - Klient kontroluje klucze za pomocą usługi Azure Key Vault
    - Chmura pełna funkcjonalność

- Szyfrowanie po stronie serwera za pomocą klawiszy zarządzany przez klienta na sprzęcie kontrolowane przez klienta
    - Dostawcy zasobów platformy Azure wykonywać operacje szyfrowania i odszyfrowywania
    - Klient kontroluje klucze na sprzęcie kontrolowane przez klienta
    - Chmura pełna funkcjonalność

Szyfrowanie po stronie klienta należy rozważyć następujące kwestie:

- Usługi platformy Azure nie widzi odszyfrowane dane
- Klienci zarządzania i przechowywanie kluczy lokalnie (lub w innych secure magazynów). Klucze nie są dostępne do usług platformy Azure
- Funkcje zmniejszenie chmury

Modele szyfrowania obsługiwanych w systemie Azure podzielony na dwie główne grupy: "Szyfrowania klienta" i "po stronie serwera szyfrowania" jak wspomniano powyżej. Należy pamiętać, że niezależnie od szyfrowania w modelu rest używanych usług Azure zawsze zalecamy użycie bezpiecznego transportu, takich jak protokołu TLS lub HTTPS. W związku z tym szyfrowanie transportu powinny być kierowane przez protokół transportu i nie powinna być główna czynnikiem umożliwiającym określenie które szyfrowanie model rest do użycia.

### <a name="client-encryption-model"></a>Model szyfrowania klienta

Model szyfrowania klienta odwołuje się do szyfrowania, które jest wykonywane poza dostawcy zasobów lub Azure przez usługę lub aplikację wywołującego. Szyfrowanie można wykonać przez aplikację usługi na platformie Azure lub aplikacji uruchomionych w centrum danych klienta. W obu przypadkach podczas korzystania z tego modelu szyfrowania dostawcy zasobów Azure otrzyma zaszyfrowany obiekt blob danych bez możliwość odszyfrowywania danych w jakikolwiek sposób i mają dostęp do kluczy szyfrowania. W tym modelu zarządzania kluczami jest realizowane przez wywołującego/aplikacji usługi i jest nieprzezroczysta dla usługi Azure.

![Klient](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig2.png)

### <a name="server-side-encryption-model"></a>Model szyfrowania po stronie serwera

Modele szyfrowania po stronie serwera odwoływać się do szyfrowania, które jest przeprowadzane przez usługę Azure. W tym modelu dostawcy zasobów wykonuje operacje szyfrowania i odszyfrowywania. Na przykład usługi Azure Storage może odbierać dane w postaci zwykłego tekstu operacjach i wykona szyfrowania i odszyfrowywania wewnętrznie. Dostawca zasobów może używać kluczy szyfrowania, które są zarządzane przez firmę Microsoft lub przez klienta w zależności od wybranej konfiguracji. 

![Serwer](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig3.png)

### <a name="server-side-encryption-key-management-models"></a>Modele zarządzania kluczami szyfrowania po stronie serwera

Każdy z szyfrowanie po stronie serwera w modelach rest oznacza cechy charakterystyczne zarządzania kluczami. W tym jak i gdzie klucze szyfrowania są tworzone i przechowywane oraz modele dostępu i procedur rotacją kluczy. 

#### <a name="server-side-encryption-using-service-managed-keys"></a>Szyfrowanie po stronie serwera przy użyciu kluczy zarządzane przez usługę

Dla wielu klientów upewnij się, że dane są szyfrowane, gdy jest w stanie spoczynku jest niezbędne wymagania. Przy użyciu usługi zarządzane klucze szyfrowania po stronie serwera umożliwia ten model umożliwia klientom oznaczyć określonego zasobu (konto magazynu, bazy danych SQL, itp.) do szyfrowania i pozostawienie wszystkie aspekty zarządzania kluczami, takie jak publikowania klucza, obracanie i kopii zapasowej do firmy Microsoft. Większość usług Azure, które obsługują szyfrowanie magazynowanych zwykle obsługuje ten model Odciążanie zarządzania kluczy szyfrowania na platformie Azure. Dostawcy zasobów platformy Azure tworzy klucze, umieszcza je w bezpiecznym magazynie i pobiera je w razie potrzeby. Oznacza to, że usługa ma pełny dostęp do kluczy i usługa ma pełną kontrolę nad poświadczeń zarządzania cyklem życia.

![Zarządzane](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig4.png)

Szyfrowanie po stronie serwera za pomocą kluczy usługi zarządzania w związku z tym szybko adresów musi mieć szyfrowania magazynowane z małym obciążeniem do klienta. Jeśli jest dostępny, klient zazwyczaj otwiera portalu Azure dla subskrypcji docelowej i dostawcy zasobów i sprawdza dialogowe informujące, chciałby do szyfrowania danych. W niektórych menedżerowie zasobów szyfrowania po stronie serwera z usługami zarządzać kluczami jest domyślnie włączone.

Szyfrowanie po stronie serwera za pomocą kluczy zarządzany przez firmę Microsoft oznaczać usługi ma pełny dostęp do przechowywania i zarządza nimi klucze. Gdy jest kilku klientów może być konieczne zarządzanie kluczy, ponieważ uznać, że będą mogli większe bezpieczeństwo, koszt i ryzyko związane z rozwiązaniem do magazynu kluczy niestandardowych należy uwzględnić podczas szacowania tego modelu. W manIn wielu przypadkach ganization mogą określić ograniczenia zasobów lub zagrożenia związane z rozwiązania lokalnego może większe niż ryzyko zarządzania chmurą w pozostałych kluczy szyfrowania.  Jednak ten model nie może być wystarczający do organizacji, które mają wymagania dotyczące kontroli tworzenie i cyklem życia kluczy szyfrowania lub innego personelu zarządzać kluczami szyfrowania usługi niż zarządzanie usługą (np. podział Zarządzanie kluczami z ogólną model zarządzania dla usługi).

##### <a name="key-access"></a>Dostęp do klucza

Gdy jest używane szyfrowanie po stronie serwera z usługami zarządzać kluczami, tworzenia klucza, magazynu i dostępu do usługi są wszystkie zarządzane przez usługę. Zazwyczaj dostawców podstawowych zasobów platformy Azure będą przechowywane klucze szyfrowania danych w magazynie, który znajduje się w pobliżu danych i szybko dostępne i jest dostępny podczas kluczy szyfrowania klucza są przechowywane w bezpiecznym magazynie wewnętrznej.

**Zalety**

- Prosta konfiguracja
- Firma Microsoft zarządza obrotu klucza, tworzenia kopii zapasowej i nadmiarowość
- Odbiorca nie ma koszt związany z implementacji lub ryzyko schemat niestandardowy zarządzania kluczami.

**Wady**

- Odbiorcy nie kontroluje klucze szyfrowania (Specyfikacja klucza cyklu życia, odwołania, itp.)
- Brak możliwości może też oddzielić zarządzania kluczami z ogólną model zarządzania dla usługi

#### <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Szyfrowanie po stronie serwera przy użyciu klienta zarządzane klucze w usłudze Azure Key Vault 

W scenariuszach, gdzie jest to wymaganie szyfrowania danych na rest i kontroli klientów klucze szyfrowania można użyć szyfrowania po stronie serwera za pomocą klienta zarządzanych kluczy w magazynie kluczy. Niektóre usługi mogą przechowywać tylko klucz szyfrowania klucza głównego w usłudze Azure Key Vault i Zapisz zaszyfrowanego klucza szyfrowania danych w wewnętrznej lokalizacji bliżej do danych. W tym scenariuszu klienci mogą doprowadzić własne klucze do magazynu kluczy (BYOK — Bring Your Own Key), lub tych samych i używać ich do szyfrowania żądanych zasobów. Gdy dostawca zasobów wykonuje operacje szyfrowania i odszyfrowywania używa skonfigurowanego klucza jako klucz główny dla wszystkich operacji szyfrowania. 

##### <a name="key-access"></a>Dostęp do klucza

Model szyfrowania po stronie serwera z kluczami klientów zarządzanych w magazynie kluczy Azure obejmuje usługę uzyskiwania dostępu do kluczy do szyfrowania i odszyfrowywania zgodnie z potrzebami. Szyfrowanie kluczy rest są dostępne usługi za pomocą zasad kontroli dostępu. Ta zasada udziela dostępu do tożsamości usługi, aby uzyskać klucz. Uruchomione w imieniu skojarzona subskrypcja usługi Azure można skonfigurować przy użyciu tożsamości w danej subskrypcji. Usługę można uwierzytelniania usługi Azure Active Directory i odbierać tokenu uwierzytelniania zidentyfikowania się jako usługi działające w imieniu subskrypcji. Token może być przedstawiony w usłudze Key Vault w celu uzyskania klucza, który udzielono dostępu do.

Dla operacji przy użyciu kluczy szyfrowania, tożsamości usługi można otrzymać dostęp do dowolnej z następujących operacji: odszyfrować, szyfrowania, unwrapKey, wrapKey, sprawdź, zaloguj się, get, listy, aktualizacji, utworzyć, zaimportować, Usuń, kopii zapasowej i przywracania.

Uzyskanie klucza do użycia w szyfrowania lub odszyfrowywania danych przechowywanych tożsamość usługi, które wystąpienie usługi Resource Manager zostanie uruchomione jako musi mieć UnwrapKey (Aby uzyskać klucz odszyfrowywania) i WrapKey (Aby wstawić klucz do magazynu kluczy podczas tworzenia nowego klucza).


>[!NOTE] 
>Aby uzyskać więcej szczegółów na Key Vault autoryzacji Zobacz bezpieczny strony magazynu kluczy w [dokumentacji usługi Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault). 

**Zalety**

- Pełną kontrolę nad klucze używany — w magazynie kluczy klienta pod kontrolą klienta zarządzania kluczami szyfrowania.
- Możliwość szyfrowania wielu usług na jeden z nich
- Może też oddzielić zarządzania kluczami z ogólną model zarządzania dla usługi
- Można zdefiniować usługi i lokalizacji klucza w regionach

**Wady**

- Klient ma pełną odpowiedzialność za zarządzanie klucza dostępu
- Klient ma pełną odpowiedzialność za zarządzanie cyklem życia klucza
- Dodatkowe obciążenie instalacji i konfiguracji

#### <a name="server-side-encryption-using-service-managed-keys-in-customer-controlled-hardware"></a>Szyfrowanie po stronie serwera za pomocą kluczy zarządzane przez usługę sprzętu kontrolowane przez klienta

Niektóre usługi Azure Włącz model zarządzania kluczami hosta swój własny klucz (HYOK). Ten tryb zarządzania jest przydatne w scenariuszach, w których jest wymagane szyfrowanie danych magazynowanych i zarządzania kluczami w repozytorium zastrzeżonych poza kontrolą firmy Microsoft. W tym modelu usługi muszą zostać pobrane z witryny zewnętrznej klucza. Wpływ na wydajność i dostępność gwarancji, a konfiguracja jest bardziej złożony. Ponadto ponieważ usługa ma dostęp do klucza szyfrowania danych podczas operacji szyfrowania i odszyfrowywania ogólną gwarancje bezpieczeństwa w tym modelu są podobne do kiedy klucze są zarządzane w usłudze Azure Key Vault klienta.  W związku z tym tego modelu nie jest odpowiedni dla większości organizacji, chyba że mają wymagania dotyczące zarządzania określonymi klucza. Ze względu na ograniczenia te większość usług Azure nie obsługują sprzętu kontrolowane przez klienta za pomocą serwer zarządzany klucze szyfrowania po stronie serwera.

##### <a name="key-access"></a>Dostęp do klucza

Gdy jest używane szyfrowanie po stronie serwera za pomocą kluczy zarządzane przez usługę w sprzęcie komputerowym kontrolowane klucze są obsługiwane w systemie, skonfigurowane przez klienta. Usług Azure, które obsługują ten model zapewniają magazynu kluczy dostarczanych oznacza ustanowienia bezpiecznego połączenia z klientem.

**Zalety**

- Pełną kontrolę nad klucz główny używany — szyfrowania kluczy są zarządzane przez sklep dostarczanych przez klienta
- Możliwość szyfrowania wielu usług na jeden z nich
- Może też oddzielić zarządzania kluczami z ogólną model zarządzania dla usługi
- Można zdefiniować usługi i lokalizacji klucza w regionach

**Wady**

- Pełną odpowiedzialność za magazynu kluczy, zabezpieczeń, wydajności i dostępności
- Pełną odpowiedzialność za zarządzanie klucza dostępu
- Pełną odpowiedzialność za zarządzanie cyklem życia klucza
- Znaczące instalacji, konfiguracji i kosztów rutynowej konserwacji
- Zwiększona zależność od dostępności sieci między centrum danych klienta i centrach danych platformy Azure.

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Szyfrowanie przechowywanych w usług chmurowych firmy Microsoft

Microsoft Cloud services są używane we wszystkich trzech chmury modelach: IaaS i PaaS, SaaS. Poniżej dostępne są przykłady sposób dopasowania na każdym modelu:

- Oprogramowanie usług, nazywany oprogramowania jako serwer lub SaaS, którego aplikacja pochodzącymi z chmury, takich jak usługi Office 365.
- Usługi platformy, którzy wykorzystać chmury w swoich aplikacjach przy użyciu chmury dla magazynu, analizy i funkcji magistrali usług.
- Infrastruktura usług lub infrastruktura jako usługa (IaaS), w którym klient wdraża systemy operacyjne i aplikacje, które są hostowane w chmurze i prawdopodobnie wykorzystaniu innych usług w chmurze.

### <a name="encryption-at-rest-for-saas-customers"></a>Szyfrowanie przechowywanych dla klientów SaaS

Oprogramowanie jako usługa (SaaS) klienci zwykle mają szyfrowania magazynowane włączone lub dostępne w poszczególnych usługach. Usługi Office 365 ma kilka opcji klienci mogą weryfikować szyfrowania w stanie spoczynku. Informacje dotyczące usług Office 365 dla technologii szyfrowania danych dla usługi Office 365.

### <a name="encryption-at-rest-for-paas-customers"></a>Szyfrowanie przechowywanych dla klientów PaaS

Platforma jako usługa (PaaS) odbiorcy danych zazwyczaj znajduje się w środowisku wykonywania aplikacji i wszystkich dostawców zasobów Azure używany do przechowywania danych klienta. Aby zobaczyć, szyfrowanie w pozostałych opcji dostępnych dla użytkownika, sprawdź w tabeli poniżej dla platform magazynu i aplikacji, których używasz. Jeśli są obsługiwane, łącza, aby uzyskać instrukcje dotyczące włączania szyfrowania magazynowane są dostępne dla każdego dostawcy zasobów. 

### <a name="encryption-at-rest-for-iaas-customers"></a>Szyfrowanie magazynowanych klientom IaaS

Infrastruktura jako usługa (IaaS) klienci mogą mieć różnych usług i aplikacji w użyciu. Usługi IaaS można włączyć szyfrowanie przechowywanych w ich Azure hostowanej maszyny wirtualne i wirtualne dyski twarde przy użyciu szyfrowania dysków Azure. 

#### <a name="encrypted-storage"></a>Zaszyfrowane magazynu

Podobnie jak PaaS innymi usługami Azure, które przechowują dane szyfrowane, gdy mogą korzystać z rozwiązań IaaS. W takich przypadkach można włączyć szyfrowanie w witrynie pomocy technicznej Rest zgodnie z każdym wykorzystanych usługi Azure. Pod tabelą wylicza główne magazynu, usług i aplikacji platformy i modelu szyfrowania, gdy nie są obsługiwane. Jeśli są obsługiwane, aby uzyskać instrukcje dotyczące włączania szyfrowania magazynowane zostały podane linki. 

#### <a name="encrypted-compute"></a>Zaszyfrowane obliczeń

Kompleksowe rozwiązania Rest wymaga nigdy nie trwałość danych w niezaszyfrowanej postaci. Znajduje się w użyciu, na serwerze podczas ładowania danych w pamięci, dane można trwale lokalnie na różne sposoby, w tym plik stronicowania systemu Windows, zrzutu awaryjnego i wszelkie rejestrowania aplikacji może wykonywać. Aby upewnić się, że te dane są szyfrowane, gdy aplikacje IaaS mogą używać szyfrowania dysków Azure na maszynie wirtualnej Azure IaaS (Windows lub Linux) i dysku wirtualnego. 

#### <a name="custom-encryption-at-rest"></a>Szyfrowanie niestandardowe przechowywane

Zaleca się, że jeśli to możliwe, IaaS aplikacje korzystać z szyfrowania dysków Azure i szyfrowania w opcji Rest dostarczanych przez dowolnego wykorzystanych usług platformy Azure. W niektórych przypadkach takich jak wymagania dotyczące szyfrowania nieregularne lub magazynu opartego na innych niż Azure, Deweloper aplikacji IaaS może być konieczne wdrożenie szyfrowanie w rest samodzielnie. Deweloperzy rozwiązań można lepiej IaaS zintegrować z platformy Azure, zarządzania i klienta oczekiwania dzięki wykorzystaniu niektóre składniki platformy Azure. W szczególności deweloperzy powinien korzystać z usługi Azure Key Vault do zapewnienia bezpiecznego magazynu kluczy, a także zapewnić klientom opcji zarządzania kluczami spójne usług platformy Azure najbardziej. Ponadto niestandardowe rozwiązania należy używać tożsamości usługi Azure-Managed do konta usługi do dostępu do kluczy szyfrowania. Aby uzyskać informacje dla deweloperów usługi Azure Key Vault i zarządzane tożsamości usługi Zobacz ich odpowiednich zestawów SDK.

## <a name="azure-resource-providers-encryption-model-support"></a>Obsługa modelu szyfrowania dostawcy zasobów platformy Azure

Usługi Microsoft Azure każdego obsługuje jednego lub więcej szyfrowania w modelach rest. W przypadku niektórych usług, co najmniej jeden z modeli szyfrowania nie można stosować. Ponadto usługi może zwolnić obsługę tych scenariuszy, w różne harmonogramy. W tej sekcji opisano szyfrowania w witrynie pomocy technicznej rest w momencie pisania tego dokumentu dla każdej z usług magazynu najważniejszych danych Azure.

### <a name="azure-disk-encryption"></a>Szyfrowanie dysków Azure

Każdy klient jako usługa (IaaS) przy użyciu infrastruktury platformy Azure funkcji można osiągnąć szyfrowanie przechowywanych maszyn wirtualnych IaaS i dysków za pomocą szyfrowania dysków Azure. Aby uzyskać więcej informacji na dysku Azure Zobacz szyfrowania [dokumentacji szyfrowania dysków Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).

#### <a name="azure-storage"></a>Azure Storage

Wszystkie usługi Azure Storage (magazynu obiektów Blob, magazyn kolejek, Magazyn tabel i plików Azure) obsługuje szyfrowanie po stronie serwera magazynowanych, z niektórych usług pomocniczych zarządzany przez klienta kluczy i szyfrowania po stronie klienta.  

- Po stronie serwera: Wszystkich usług magazynu Azure Włącz szyfrowanie po stronie serwera, domyślnie przy użyciu zarządzanych przez usługę kluczy, który jest niewidoczny dla aplikacji. Aby uzyskać więcej informacji, zobacz [szyfrowanie usługi Magazyn Azure dla danych magazynowanych](https://docs.microsoft.com/azure/storage/storage-service-encryption). Azure Blob storage i pliki Azure obsługują także zarządzane przez klienta klucze w usłudze Azure Key Vault. Aby uzyskać więcej informacji, zobacz [szyfrowanie usługi Magazyn przy użyciu kluczy zarządzany przez klienta w usłudze Azure Key Vault](https://docs.microsoft.com/en-us/azure/storage/common/storage-service-encryption-customer-managed-keys).
- Po stronie klienta: Obiekty BLOB platformy Azure, tabel i kolejek obsługuje szyfrowania po stronie klienta. Podczas korzystania z szyfrowania po stronie klienta, klienci szyfrowania danych i przekazywanie danych jako obiekt blob zaszyfrowany. Zarządzanie kluczami jest realizowane przez klienta. Aby uzyskać więcej informacji, zobacz [szyfrowania po stronie klienta i usługi Azure Key Vault dla magazynu Microsoft Azure](https://docs.microsoft.com/azure/storage/storage-client-side-encryption).


#### <a name="azure-sql-database"></a>Azure SQL Database

Baza danych SQL Azure obecnie obsługuje szyfrowanie przechowywanych dla scenariuszy szyfrowania po stronie klienta i po stronie usługi zarządzany przez firmę Microsoft.

Obsługa szyfrowania obecnie jest zapewniana za pomocą funkcji SQL o nazwie przezroczystego szyfrowania danych. Po klienta bazy danych SQL Azure umożliwia klucz funkcji TDE automatycznie są tworzone i zarządzane dla nich. Można włączyć szyfrowanie przechowywanych na poziomie bazy danych i serwera. Począwszy od czerwca 2017 r. [funkcji przezroczystego szyfrowania danych (TDE)](https://msdn.microsoft.com/library/bb934049.aspx) zostanie włączona domyślnie na nowo utworzone bazy danych.

Szyfrowanie po stronie klienta danych bazy danych SQL Azure jest obsługiwane przez [zawsze zaszyfrowane](https://msdn.microsoft.com/library/mt163865.aspx) funkcji. Zawsze zaszyfrowane używa klucza tworzone i przechowywane przez klienta. Klientów można przechowywać klucz główny w magazynie certyfikatów systemu Windows, usługi Azure Key Vault lub lokalnego sprzętowego modułu zabezpieczeń. Użytkowników SQL przy użyciu programu SQL Server Management Studio, wybierz jakie klucza chce używać do szyfrowania, która kolumna.

|                                  |                |                     | **Model szyfrowania**             |                              |        |
|----------------------------------|----------------|---------------------|------------------------------|------------------------------|--------|
|                                  |                |                     |                              |                              | **Klienta** |
|                                  | **Zarządzanie kluczami** | **Klucz zarządzany przez usługę** | **Klientów zarządzanych w magazynie kluczy** | **Zarządzany przez klienta lokalnego** |        |
| **Magazyn i baz danych**            |                |                     |                              |                              |        |
| Dysku (IaaS)                      |                | -                   | Yes                          | Tak*                         | -      |
| Program SQL Server (IaaS)                |                | Yes                 | Yes                          | Yes                          | Yes    |
| Baza danych Azure SQL (PaaS)                 |                | Yes                 | Yes                          | -                            | Yes    |
| Usługa Azure Storage (bloku/stronicowych obiektów blob) |                | Yes                 | Yes                          | -                            | Yes    |
| Magazyn Azure (pliki)            |                | Yes                 | Yes                          | -                            | -      |
| Usługa Azure Storage (tabel, kolejek)   |                | Yes                 | -                            | -                            | Yes    |
| Rozwiązania cosmos bazy danych (dokument DB)          |                | Yes                 | -                            | -                            | -      |
| Magazyn StorSimple                       |                | Yes                 | -                            | -                            | Yes    |
| Backup                           |                | -                   | -                            | -                            | Yes    |
| **Analiza i analiza**       |                |                     |                              |                              |        |
| Azure Data Factory               |                | Yes                 | -                            | -                            | -      |
| Azure Machine Learning           |                | -                   | Wersja zapoznawcza                      | -                            | -      |
| Usługa Azure Stream Analytics           |                | Yes                 | -                            | -                            | -      |
| HDInsight (magazyn obiektów Blob platformy Azure)  |                | Yes                 | -                            | -                            | -      |
| HDInsight (Data Lake Storage)   |                | Yes                 | -                            | -                            | -      |
| Azure Data Lake Store            |                | Yes                 | Yes                          | -                            | -      |
| Azure Data Catalog               |                | Yes                 | -                            | -                            | -      |
| Power BI                         |                | Yes                 | -                            | -                            | -      |
| **Usługi IoT**                     |                |                     |                              |                              |        |
| Usługa IoT Hub                          |                | -                   | -                            | -                            | Yes    |
| Service Bus                      |                | Yes              | -                            | -                            | Yes    |
| Event Hubs                       |                | Yes             | -                            | -                            | -      |


## <a name="conclusion"></a>Podsumowanie

Ochrona danych klienta przechowywanych w usługach Azure jest najważniejsze do firmy Microsoft. Wszystkie platformy Azure hostowanej usługi zobowiązaniem jest zapewnienie szyfrowania w pozostałych opcji. Podstawowych usług, takich jak usługi Azure Storage, baza danych SQL Azure i analiza klucza i usług analizy już zapewnia szyfrowanie na pozostałe opcje. Niektóre z tych usług obsługuje kluczy odbiorcy kontrolowane i szyfrowania po stronie klienta, a także zarządzane przez usługę kluczy i szyfrowania. Usług Microsoft Azure są szeroko udoskonalanie szyfrowania na dostępność Rest i nowe opcje są planowane do wersji zapoznawczej i ogólnej dostępności w ciągu przyszłych miesięcy.

