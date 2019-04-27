---
title: Microsoft Azure danych szyfrowanie w spoczynku | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie programu Microsoft Azure szyfrowanie magazynowanych danych, ogólnych możliwości i zagadnienia ogólne.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2018
ms.author: barclayn
ms.openlocfilehash: 4ced712b1b2716d85f0366ea892460053db598b8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60613052"
---
# <a name="azure-data-encryption-at-rest"></a>Usługa Azure Data szyfrowanie w spoczynku

Microsoft Azure zawiera narzędzia, aby chronić dane zgodnie z potrzebami zabezpieczeń i zgodności firmy. Ten dokument koncentruje się na:

- Sposób ochrony danych magazynowanych w Microsoft Azure
- W tym artykule omówiono różne składniki biorące udział w implementacji ochrony danych
- Przegląda zalety i wady zarządzania kluczami w różnych metod ochrony. 

Szyfrowanie w spoczynku jest typowym wymogiem zabezpieczeń. Na platformie Azure organizacje mogą szyfrować danych w spoczynku, bez ryzyka i kosztów rozwiązań niestandardowych zarządzania kluczami. Organizacje mają możliwość umożliwienie zarządzania szyfrowanie w spoczynku w usłudze Azure. Ponadto organizacje mają różne opcje ściśle zarządzać szyfrowania lub kluczy szyfrowania.

## <a name="what-is-encryption-at-rest"></a>Co to jest szyfrowanie w spoczynku?

Szyfrowanie w spoczynku jest kodowania (szyfrowanie) danych, gdy jest ona utrwalona. Szyfrowanie w projekty Rest na platformie Azure Użyj szyfrowania symetrycznego do szyfrowania i odszyfrowywania dużych ilości danych szybko zgodnie z prostego modelu koncepcyjnego:

- Klucz szyfrowania symetrycznego jest używany do szyfrowania danych są zapisywane do magazynu. 
- Ten sam klucz szyfrowania jest używany do odszyfrowania danych, ponieważ jest ona readied do użycia w pamięci.
- Dane mogą być podzielone na partycje, a różne klucze mogą być używane do każdej partycji.
- Klucze muszą być przechowywane w bezpiecznej lokalizacji z kontrolą dostępu opartą na tożsamość i zasady inspekcji. Klucze szyfrowania danych często są szyfrowane za pomocą asymetrycznych, aby bardziej ograniczyć dostęp.

W praktyce kluczowych scenariuszy zarządzania i kontroli, a także gwarancje skalowalność i dostępność, Wymagaj konstrukcje dodatkowe. Microsoft Azure szyfrowania Rest pojęć i składników są opisane poniżej.

## <a name="the-purpose-of-encryption-at-rest"></a>Celem szyfrowanie w spoczynku

Szyfrowanie w spoczynku zapewnia ochronę danych przechowywanych danych (nieaktywne). Ataków magazynowanych danych obejmują próbuje uzyskać fizyczny dostęp do sprzętu, na którym dane są przechowywane, a następnie naruszyć zawartymi danymi. W przypadku ataków dysk twardy serwera może mieć zostały niewłaściwego stosowania podczas konserwacji, pozwalając osobie atakującej usunąć dysk twardy. Później, osoba atakująca spowodowałaby dysku twardego na komputerze pod jej kontrolą próbuje uzyskać dostęp do danych. 

Szyfrowanie w spoczynku zaprojektowano w celu uniemożliwia osobie atakującej uzyskanie dostępu do niezaszyfrowane dane przez zapewnienie im dane są szyfrowane, gdy na dysku. Jeśli osoba atakująca uzyskuje dysk twardy z zaszyfrowanych danych, ale nie klucze szyfrowania, osoba atakująca musi pokonania szyfrowania do odczytywania danych. Ten rodzaj ataku jest znacznie więcej złożone i korzystanie z zasobów niż dostęp do nieszyfrowanych danych na dysku twardym. Z tego powodu szyfrowanie w spoczynku zdecydowanie zaleca się i jest wymagany w przypadku wielu organizacji o wysokim priorytecie. 

Szyfrowanie w spoczynku również mogą być wymagane przez organizacji na potrzeby danych wysiłków nadzoru i zgodności. Przepisy branżowe i rządowe, takie jak HIPAA, PCI i FedRAMP, układ konkretnych zabezpieczeń dotyczące wymagania dotyczące ochrony i szyfrowania danych. Szyfrowanie w spoczynku jest miarą obowiązkowe wymaganych do zapewnienia zgodności z niektórymi z tych przepisów.

Oprócz spełniające wymagania przepisów i zgodności, szyfrowanie w spoczynku zapewnia ochronę w głębi ochronę. Microsoft Azure zapewnia platformę zgodnych usług, aplikacji i danych. Udostępnia również kompleksowe możliwości i zabezpieczeń fizycznych, kontrola dostępu do danych i przeprowadzania inspekcji. Jednak jest ważne zapewnić dodatkowe środki bezpieczeństwa "nakładające się", w przypadku awarii jednego pozostałe środki bezpieczeństwa i szyfrowanie w spoczynku zapewnia środkiem bezpieczeństwa

Firma Microsoft dba o szyfrowania rest opcje usług w chmurze i kontrolę nad kluczami szyfrowania i dzienniki użycia klucza, zapewniając klientom. Ponadto firma Microsoft pracuje się do szyfrowania wszystkich danych klientów w stanie spoczynku domyślnie.

## <a name="azure-encryption-at-rest-components"></a>Szyfrowanie usługi Azure w pozostałej części

Zgodnie z wcześniejszym opisem celem szyfrowanie w spoczynku jest, że danych, który jest utrwalony na dysku są szyfrowane przy użyciu klucza szyfrowania klucza tajnego. Aby osiągnąć ten cel bezpieczne tworzenie klucza, musi być podana magazynu, access control i zarządzanie kluczami szyfrowania. Chociaż szczegóły mogą się różnić, usług platformy Azure szyfrowania implementacje Rest można opisać w warunkach przedstawionych na poniższym diagramie.

![Składniki](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>W usłudze Azure Key Vault

Lokalizacja magazynu kluczy szyfrowania i kontroli dostępu do tych kluczy stanowi podstawę do szyfrowania w modelu rest. Klucze muszą być wysoko zabezpieczonej, ale zarządzane przez określonych użytkowników i dostępne do określonych usług. Dla usług platformy Azure Azure Key Vault to rozwiązanie zalecane magazynu kluczy i zapewnia wspólnego środowiska zarządzania w usługach. Klucze są przechowywane i zarządzane w magazynów kluczy, a dostęp do magazynu kluczy można podać dla użytkowników lub usług. Usługa Azure Key Vault obsługuje tworzenie odbiorcy kluczy lub importowanie kluczy klienta do użytku w scenariuszach klucza szyfrowania zarządzanego przez klienta.

### <a name="azure-active-directory"></a>Usługa Azure Active Directory

Uprawnienia do używania kluczy przechowywanych w usłudze Azure Key Vault, do zarządzania lub do nich dostęp do szyfrowania na Rest szyfrowania i odszyfrowywania, można podać na kontach usługi Azure Active Directory. 

### <a name="key-hierarchy"></a>Kluczową hierarchię

Więcej niż jeden klucz szyfrowania jest używany podczas szyfrowania w implementacji rest. Szyfrowanie asymetryczne przydaje się do ustanawiania relacji zaufania i uwierzytelniania, na potrzeby dostępu do kluczy i zarządzania. Szyfrowanie symetryczne jest bardziej wydajne dla zbiorczego szyfrowania i odszyfrowywania, dzięki czemu silniejszego szyfrowania i zwiększając wydajność. Ograniczanie użycia klucza szyfrowania pojedynczego zmniejsza ryzyko, że klucz zostanie naruszone i koszt ponownie szyfrować podczas klucza muszą zostać zastąpione. Alerty platformy Azure na pozostałą modeli Użyj klucza hierarchii składającej się z następujących typów kluczy:

- **Klucz szyfrowania klucza szyfrowania danych (danych)** — klucz symetryczny AES256 używany do szyfrowania na partycję lub bloku danych.  Pojedynczy zasób może mieć wiele partycji i wiele klucze szyfrowania danych. Szyfrowanie każdego bloku danych przy użyciu innego klucza utrudnia ataków kryptograficznych analizy. Dostęp do DEKs są wymagane przez dostawcę lub aplikacji wystąpienia zasobu szyfrowania i odszyfrowywania określonego bloku. Po klucza szyfrowania danych przy użyciu nowego klucza tylko dane w jego skojarzony blok musi być ponownie szyfrowane przy użyciu nowego klucza.
- **Klucz szyfrowania klucza (KEK)** — klucz asymetryczny szyfrowania używany do szyfrowania kluczy szyfrowania danych. Użyj klucza szyfrowania umożliwia klucze szyfrowania danych, się być szyfrowane i kontrolowany. Jednostka, która ma dostęp do klucza KEK może różnić się od jednostki, która wymaga klucza szyfrowania danych. Jednostki może brokera dostępu do klucza szyfrowania danych, aby ograniczyć dostęp do każdego klucza szyfrowania danych na określoną partycję. Ponieważ klucza KEK jest wymagany do odszyfrowania DEKs, klucza KEK skutecznie jest pojedynczym punktem krytycznym za pomocą którego DEKs można skutecznie usunąć przez usunięcie klucza KEK.

Klucze szyfrowania danych zaszyfrowanych przy użyciu kluczy szyfrowania klucza są przechowywane osobno, a tylko jednostki z dostępu do klucza szyfrowania znajdziesz jakiekolwiek klucze szyfrowania danych zaszyfrowanych za pomocą tego klucza. Obsługiwane są różne modele magazynów kluczy. Omówimy każdy model bardziej szczegółowo w dalszej części następnej sekcji.

## <a name="data-encryption-models"></a>Modele szyfrowania danych

Zrozumienie różnych modeli szyfrowania i ich zalet i wad jest istotne dla zrozumienia, jak zaimplementować szyfrowanie w spoczynku przez różnych dostawców zasobów na platformie Azure. Te definicje są udostępniane przez wszystkich dostawców zasobów na platformie Azure, aby upewnić się, wspólnego języka i taksonomii. 

Istnieją trzy scenariusze dotyczące szyfrowania po stronie serwera:

- Szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez usługę
    - Dostawcy zasobów platformy Azure, wykonywać operacje szyfrowania i odszyfrowywania
    - Firma Microsoft zarządza kluczami
    - Funkcje w pełni opartej na chmurze

- Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta w usłudze Azure Key Vault
    - Dostawcy zasobów platformy Azure, wykonywać operacje szyfrowania i odszyfrowywania
    - Klient kontroluje kluczy przy użyciu usługi Azure Key Vault
    - Funkcje w pełni opartej na chmurze

- Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta na sprzęcie kontrolowane przez klienta
    - Dostawcy zasobów platformy Azure, wykonywać operacje szyfrowania i odszyfrowywania
    - Klient kontroluje kluczy na sprzęcie kontrolowane przez klienta
    - Funkcje w pełni opartej na chmurze

Dla celów szyfrowania po stronie klienta należy rozważyć następujące kwestie:

- Usługi platformy Azure nie można wyświetlić odszyfrowane dane
- Klienci zarządzanie i przechowywania kluczy w środowisku lokalnym lub w innych secure magazynów. Klucze nie są dostępne dla usług platformy Azure
- Funkcji w chmurze z ograniczoną

Modele szyfrowania obsługiwanych na platformie Azure, podzielić na dwie główne grupy: "Klient szyfrowania" i "po stronie serwera szyfrowania" jako wymienionych wcześniej. Niezależnie od szyfrowania w modelu rest używany, usług platformy Azure, zawsze zaleca się korzystanie z bezpiecznym transportem, takiego jak protokół TLS lub HTTPS. Dlatego szyfrowania podczas transportu powinny być kierowane przez protokół transportu i nie powinny być czynnikiem w określeniu, które szyfrowanie w modelu rest do użycia.

### <a name="client-encryption-model"></a>Model szyfrowania klienta

Model szyfrowania klienta odwołuje się do szyfrowania, które odbywa się poza dostawcy zasobów lub platformy Azure, usługi lub aplikacji wywołującej. Szyfrowanie można wykonać przez aplikację usługi na platformie Azure lub przez aplikację działającą w centrum danych klienta. W obu przypadkach podczas korzystania z tego modelu szyfrowania dostawcy zasobów platformy Azure odbiera obiektu blob zaszyfrowanego danych bez możliwości odszyfrowywanie dane w dowolny sposób i mają dostęp do kluczy szyfrowania. W tym modelu zarządzania kluczami jest wykonywane przez wywołującego/aplikacja usługi i jest nieprzezroczysta dla usługi platformy Azure.

![Klient](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig2.png)

### <a name="server-side-encryption-model"></a>Model szyfrowanie po stronie serwera

Modele szyfrowania po stronie serwera odnoszą się do szyfrowania, które jest wykonywane przez usługi platformy Azure. W tym modelu dostawcy zasobów wykonuje operacje szyfrowania i odszyfrowywania. Na przykład Azure Storage może odbierać dane w postaci zwykłego tekstu, operacjach i wykona szyfrowania i odszyfrowywania wewnętrznie. Dostawca zasobów może używać kluczy szyfrowania, które są zarządzane przez firmę Microsoft lub przez klienta, w zależności od konfiguracji podany. 

![Serwer](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig3.png)

### <a name="server-side-encryption-key-management-models"></a>Modele zarządzania kluczami szyfrowania po stronie serwera

Każdy z szyfrowania po stronie serwera na pozostałą modeli oznacza cechy charakterystyczne zarządzania kluczami. Obejmuje to gdzie i jak klucze szyfrowania są tworzone i przechowywane oraz uzyskiwanie dostępu do modeli i procedury wymiany kluczy. 

#### <a name="server-side-encryption-using-service-managed-keys"></a>Szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez usługę

Dla wielu klientów zasadniczego wymogu jest zapewnienie, że dane są szyfrowane, zawsze wtedy, gdy jest w stanie spoczynku. Szyfrowania po stronie serwera użyć tego modelu umożliwia klucze zarządzane przez usługę, co umożliwia klientom oznaczyć określonego zasobu (konto magazynu, bazy danych SQL itp.) do szyfrowania i pozostawienie niezmienionej wszystkie aspekty zarządzania kluczami, takich jak publikowania klucza, obrotu i kopie zapasowe do firmy Microsoft . Większości usług platformy Azure, które zazwyczaj obsługują szyfrowanie w spoczynku obsługiwać ten model odciążania zarządzania kluczy szyfrowania na platformie Azure. Dostawca zasobów platformy Azure spowoduje utworzenie kluczy, umieszcza je w bezpiecznym magazynie i pobiera je w razie. Oznacza to, że usługa ma pełny dostęp do kluczy, a usługa ma pełną kontrolę nad poświadczenia zarządzania cyklem życia.

![Zarządzane](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig4.png)

Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez usługę w związku z tym szybko rozwiązuje konieczności posiadania szyfrowania nieużywanych danych za pomocą niskie obciążenie do klienta. Jeśli jest dostępny, klient zazwyczaj spowoduje otwarcie witryny Azure portal dla subskrypcji docelowej i dostawcy zasobów i sprawdza, czy pole, wskazując, chce, aby dane były szyfrowane. W niektórych menedżerów zasobów szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez usługę jest domyślnie włączone.

Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez Microsoft zakłada, że usługa ma pełny dostęp do przechowywania i zarządzania kluczami. Chociaż niektórzy klienci może być konieczne zarządzanie kluczami, ponieważ uważają, że będą mogli większe bezpieczeństwo, koszty i ryzyko związane z rozwiązaniem niestandardowego magazynu kluczy należy uwzględnić podczas oceny tego modelu. W wielu przypadkach organizacja może określić, że ograniczeń zasobów lub ryzyko związane z rozwiązaniem w firmie może być większe niż ryzyko zarządzania chmurą szyfrowania na klucze rest.  Jednak ten model nie może być wystarczające dla organizacji, które mają wymagania do kontrolowania, tworzenie i cyklem życia kluczy szyfrowania lub innego personelu, zarządzać kluczami szyfrowania usługi niż zarządzanie usługą (czyli podziału zarządzania kluczami z ogólnym modelu zarządzania dla usługi).

##### <a name="key-access"></a>Dostęp do klucza

W przypadku szyfrowania po stronie serwera za pomocą kluczy zarządzanych przez usługę Tworzenie klucza, storage i dostęp do usługi są wszystkie zarządzane przez usługę. Zazwyczaj dostawców podstawowe zasobów platformy Azure będą przechowywane klucze szyfrowania danych w magazynie, który znajduje się w pobliżu danych, a szybko dostępne i jest dostępny podczas klucze szyfrowania klucza są przechowywane w bezpiecznym magazynie wewnętrznego.

**Zalety**

- Prosta konfiguracja
- Firma Microsoft zarządza wymiany kluczy, backup i nadmiarowość
- Klient nie ma koszt związany z implementacji lub ryzyko schemat niestandardowego zarządzania kluczami.

**Wady**

- Nie klientowi kontrolę nad kluczami szyfrowania (Specyfikacja klucza cyklu życia, odwołań, itp.)
- Możliwość oddzielenie czynności związanych z zarządzania kluczami z ogólnym modelu zarządzania dla usługi

#### <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta w usłudze Azure Key Vault 

W scenariuszach, gdzie jest wymaganie szyfrowania danych magazynowanych i kontrolować szyfrowanie kluczy klienci mogą używać szyfrowania po stronie serwera za pomocą kluczy zarządzanych przez klienta w usłudze Key Vault. Niektóre usługi mogą przechowywać tylko katalogu głównego klucza szyfrowania w usłudze Azure Key Vault i Zapisz zaszyfrowany klucz szyfrowania danych w lokalizacji wewnętrznych bliżej do danych. W tym scenariuszu klienci Przynieś własne klucze do usługi Key Vault (BYOK — Bring Your Own Key), lub wygenerować nowe i ich używać do szyfrowania żądanych zasobów. Gdy dostawca zasobów wykonuje operacje szyfrowania i odszyfrowywania używa skonfigurowanego klucza jako klucza głównego dla wszystkich operacji szyfrowania. 

##### <a name="key-access"></a>Dostęp do klucza

Model szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta w usłudze Azure Key Vault obejmuje usługę dostępu do kluczy do szyfrowania i odszyfrowywania, zgodnie z potrzebami. Rest klucze szyfrowania są dostępne usługi za pomocą zasad kontroli dostępu. Ta zasada udziela dostępu do tożsamości usługi, aby otrzymać klucz. To usługa platformy Azure, które zostały uruchomione w imieniu skojarzonej subskrypcji można skonfigurować przy użyciu tożsamości w tej subskrypcji. Usługę można wykonywać uwierzytelnianie usługi Azure Active Directory i odbierania token uwierzytelniania identyfikuje się jako działający w imieniu subskrypcję usługi. Ten token może być przedstawiony w usłudze Key Vault w celu uzyskania klucza, które uzyskało dostęp do.

Dla operacji przy użyciu kluczy szyfrowania, tożsamości usługi można można udzielić dostępu do żadnego z następujących operacji: Odszyfruj, Szyfruj, unwrapKey, wrapKey, sprawdź, zaloguj się, Pobierz, listy, aktualizacji, tworzenie, importowanie, Usuń, kopii zapasowych i przywracania.

Aby uzyskać klucz do użycia w szyfrowania lub odszyfrowywania danych magazynowanych tożsamości usługi, który wystąpienie usługi Resource Manager będzie działał jako musi mieć UnwrapKey (Aby uzyskać klucz odszyfrowywania) i WrapKey (Aby wstawić klucz do magazynu kluczy, podczas tworzenia nowego klucza).


>[!NOTE] 
>Aby uzyskać więcej szczegółowych informacji dotyczących usługi Key Vault autoryzacji Zobacz bezpiecznego strony usługi key vault w [dokumentacji usługi Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault). 

**Zalety**

- Używane pełną kontrolę nad kluczami — szyfrowania klucze są zarządzane w usłudze Key Vault klienta pod kontrolą przez klienta.
- Możliwość szyfrowania wielu usług do jednego serwera głównego
- Można segregować zarządzania kluczami z ogólnym modelu zarządzania dla usługi
- Można zdefiniować usługi i lokalizację klucza w wielu regionach

**Wady**

- Klient ma pełną odpowiedzialność za zarządzanie kluczami dostępu
- Klient ma pełną odpowiedzialność za zarządzanie cyklem życia kluczy
- Dodatkowe obciążenie instalacji i konfiguracji

#### <a name="server-side-encryption-using-service-managed-keys-in-customer-controlled-hardware"></a>Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez usługę w sprzęcie kontrolowane przez klienta

Niektóre usługi platformy Azure Aktywuj model zarządzania kluczami hosta Your Own Key (HYOK). Ten tryb zarządzania jest przydatne w scenariuszach w przypadku, gdy istnieje potrzeba do szyfrowania danych magazynowanych i zarządzanie kluczami w repozytorium własnościowych poza kontrolą firmy Microsoft. W tym modelu usługa musi pobrać klucza z witryny zewnętrznej. Dotyczy gwarancji wydajności i dostępności, a konfiguracja jest bardziej złożona. Ponadto ponieważ usługa ma dostęp do klucza szyfrowania danych podczas wykonywania operacji szyfrowania i odszyfrowywania ogólną gwarancje bezpieczeństwa w tym modelu są podobne do Jeśli klucze są zarządzane przez klienta w usłudze Azure Key Vault.  W wyniku tego modelu nie jest odpowiednie dla większości organizacji, chyba że mają wymagania dotyczące określonych zarządzania kluczami. Ze względu na ograniczenia te większości usług platformy Azure nie obsługują szyfrowania po stronie serwera za pomocą kluczy zarządzanych przez serwer w sprzęcie kontrolowane przez klienta.

##### <a name="key-access"></a>Dostęp do klucza

Gdy jest używane szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez usługę w sprzęcie kontrolowane przez klienta klucze są przechowywane w systemie, skonfigurowane przez klienta. Usługi platformy Azure, które obsługują ten model udostępniają metody ustanawiania bezpiecznego połączenia z klientem podany magazyn kluczy.

**Zalety**

- Pełną kontrolę nad klucz główny używany — szyfrowania klucze są zarządzane przez sklep klienta
- Możliwość szyfrowania wielu usług do jednego serwera głównego
- Można segregować zarządzania kluczami z ogólnym modelu zarządzania dla usługi
- Można zdefiniować usługi i lokalizację klucza w wielu regionach

**Wady**

- Pełną odpowiedzialność za magazynu kluczy, zabezpieczeń, wydajności i dostępności
- Pełną odpowiedzialność za zarządzanie kluczami dostępu
- Pełną odpowiedzialność za zarządzanie cyklem życia kluczy
- Znaczące instalacji, konfiguracji i kosztów rutynowej konserwacji
- Zwiększona zależność od dostępności sieci między centrum danych klienta i centrami danych platformy Azure.

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Szyfrowanie danych magazynowanych w usługach w chmurze firmy Microsoft

Microsoft Cloud services są używane we wszystkich modelach trzy chmury: IaaS, PaaS, SaaS. Poniżej są przykłady jak zmieściły się na każdego modelu:

- Usług oprogramowania, nazywane oprogramowania jako serwer lub SaaS, która aplikacja zawartym w chmurze, takich jak Office 365.
- Usługi platformy, którzy wykorzystują możliwości w chmurze w swoich aplikacjach za pośrednictwem chmury, takich jak magazyn, analizy i funkcjonalności magistrali usług.
- Usługi infrastruktury lub infrastruktura jako usługa (IaaS), w którym klient wdraża systemy operacyjne i aplikacje, które są hostowane w chmurze i prawdopodobnie korzystanie z innych usług w chmurze.

### <a name="encryption-at-rest-for-saas-customers"></a>Szyfrowanie w spoczynku SaaS klientom

Oprogramowanie jako usługa (SaaS) klienci zazwyczaj mają szyfrowanie w spoczynku, włączone lub dostępne w poszczególnych usługach. Usługi Office 365 oferuje kilka opcji klienci będą mogli zweryfikować lub włączyć szyfrowanie danych magazynowanych. Aby uzyskać informacji na temat usługi Office 365, zobacz [szyfrowania w usłudze Office 365](https://docs.microsoft.com/office365/securitycompliance/encryption).

### <a name="encryption-at-rest-for-paas-customers"></a>Szyfrowanie danych magazynowanych na potrzeby klientów PaaS

Platforma jako dane klienta usługi (PaaS) znajduje się zwykle w środowisku wykonywania aplikacji i wszystkich dostawców zasobów platformy Azure używane do przechowywania danych klientów. Aby zobaczyć, szyfrowanie w dostępnych opcji rest, sprawdź w tabeli poniżej platformy magazynu i aplikacji, których używasz. Jeśli są obsługiwane, aby uzyskać instrukcje dotyczące włączania szyfrowanie w spoczynku linków dla każdego dostawcy zasobów. 

### <a name="encryption-at-rest-for-iaas-customers"></a>Szyfrowanie w spoczynku klientom IaaS

Infrastruktura jako usługa (IaaS) klienci może mieć różne usługi i aplikacje w użyciu. Usługi IaaS można włączyć szyfrowanie danych magazynowanych w ich Azure hostowanych, maszyn wirtualnych i wirtualnych dysków twardych za pomocą usługi Azure Disk Encryption. 

#### <a name="encrypted-storage"></a>Szyfrowany Magazyn

Paas innych usług platformy Azure, które przechowują dane szyfrowane, gdy mogą korzystać z rozwiązań IaaS. W takich przypadkach można włączyć szyfrowanie w witrynie pomocy technicznej Rest zgodnie z informacjami od każdego użyte usługi platformy Azure. Poniższa tabela wylicza główne magazynu, usługi oraz platform aplikacji i modelu szyfrowanie w spoczynku obsługiwane. Jeśli są obsługiwane, podano linki do instrukcji dotyczących włączania szyfrowanie danych magazynowanych. 

#### <a name="encrypted-compute"></a>Zaszyfrowane obliczeń

Kompleksowe rozwiązania Rest wymaga, że danych nigdy nie są utrwalane w postaci niezaszyfrowanej. W użytku na serwerze, trwa ładowanie danych w pamięci, dane mogą zostać utrwalone lokalnie na różne sposoby, m.in. plik stronicowania Windows zrzutu awaryjnego i wszelkie rejestrowania, które aplikacja może wykonywać. Aby upewnić się, że te dane są szyfrowane w stanie spoczynku, aplikacje IaaS mogą używać usługi Azure Disk Encryption, maszyny wirtualnej IaaS platformy Azure (Windows lub Linux) i dysku wirtualnego. 

#### <a name="custom-encryption-at-rest"></a>Niestandardowe szyfrowanie w spoczynku

Zaleca się, że jeśli to możliwe, IaaS aplikacje korzystać z usługi Azure Disk Encryption i szyfrowania opcjach Rest udostępniane przez użyte usługi systemu Azure. W niektórych przypadkach takich jak wymagania dotyczące szyfrowania nieregularne lub magazynu opartego na spoza platformy Azure, Deweloper aplikacji IaaS może być konieczne wdrożenie szyfrowania rest samodzielnie. Deweloperzy IaaS można lepiej rozwiązań integracji z platformy Azure, zarządzania i klienta oczekiwania dzięki wykorzystaniu niektórych składników platformy Azure. W szczególności deweloperów należy używać usługi Azure Key Vault do zapewnienia bezpiecznego magazynu kluczy, a także zapewnić klientom spójne zarządzanie kluczami opcji w przypadku większości usług platformy Azure platformy. Ponadto niestandardowych rozwiązań należy używać Azure-Managed tożsamości usługi w celu umożliwienia dostępu do kluczy szyfrowania konta usług. Aby uzyskać informacje dla deweloperów usługi Azure Key Vault i tożsamości usług zarządzanych Zobacz ich odpowiednich zestawów SDK.

## <a name="azure-resource-providers-encryption-model-support"></a>Obsługa modelu szyfrowania dostawcy zasobów platformy Azure

Usługi Microsoft Azure każdego obsługuje co najmniej szyfrowanie w modelach rest. W przypadku niektórych usług, co najmniej jeden z modeli szyfrowanie może nie mieć zastosowania. Dla usług, które obsługują zarządzane przez klienta kluczowych scenariuszy ich może obsługiwać tylko podzestaw typy kluczy, obsługiwanych przez usługę Azure Key Vault dla kluczy szyfrowania. Ponadto usługi może zwolnić obsługę scenariuszy i typy kluczy na różne harmonogramy. W tej sekcji opisano szyfrowania w witrynie pomocy technicznej rest w czasie pisania tego dokumentu dla poszczególnych usług magazynowania najważniejszych danych na platformie Azure.

### <a name="azure-disk-encryption"></a>Usługa Azure disk encryption

Każdy klient przy użyciu infrastruktury platformy Azure jako usługa (IaaS) funkcji można uzyskać, szyfrowanie w spoczynku dla swoich maszyn wirtualnych IaaS i dyski za pomocą usługi Azure Disk Encryption. Aby uzyskać więcej informacji na temat usługi Azure Disk encryption, zobacz [dokumentacji usługi Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).

#### <a name="azure-storage"></a>Azure Storage

Wszystkie usługi Azure Storage (Blob storage, Queue storage, Table storage i Azure Files) obsługuje szyfrowanie po stronie serwera w spoczynku, w przypadku niektórych usług Obsługa kluczy zarządzanych przez klienta i szyfrowanie po stronie klienta.  

- Po stronie serwera: Wszystkich usług Azure Storage włączyć szyfrowanie po stronie serwera, domyślnie przy użyciu kluczy zarządzanego przez usługę, która jest niewidoczna dla aplikacji. Aby uzyskać więcej informacji, zobacz [szyfrowanie usługi Azure Storage dla danych magazynowanych](https://docs.microsoft.com/azure/storage/storage-service-encryption). Usługa Azure Blob storage i Azure Files obsługują także szyfrowania RSA 2048-bitowych kluczy zarządzanych przez klienta w usłudze Azure Key Vault. Aby uzyskać więcej informacji, zobacz [szyfrowanie usługi Storage przy użyciu kluczy zarządzanych przez klienta w usłudze Azure Key Vault](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys).
- Po stronie klienta: Usługa Azure blob, tabel i kolejek obsługuje szyfrowanie po stronie klienta. Korzystając z szyfrowania po stronie klienta, klienci szyfrowania danych i przekazywanie danych jako obiekt blob zaszyfrowany. Zarządzanie kluczami jest wykonywane przez klienta. Aby uzyskać więcej informacji, zobacz [szyfrowanie po stronie klienta i usługi Azure Key Vault dla usługi Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/storage-client-side-encryption).


#### <a name="azure-sql-database"></a>Azure SQL Database

Usługa Azure SQL Database aktualnie obsługuje szyfrowanie danych magazynowanych po stronie usługi zarządzanych przez firmę Microsoft i scenariuszy szyfrowania po stronie klienta.

Szyfrowanie serwera jest obecnie oferowana w ramach funkcji SQL o nazwie Transparent Data Encryption. Gdy klient korzystający z usługi Azure SQL Database umożliwia TDE klucza są automatycznie tworzone i zarządzane dla nich. Szyfrowanie w spoczynku, można włączyć na poziomie bazy danych i serwera. Począwszy od czerwca 2017 r. [przezroczystego szyfrowania danych (TDE)](https://msdn.microsoft.com/library/bb934049.aspx) jest domyślnie włączona na nowo utworzonych baz danych. Usługa Azure SQL Database obsługuje szyfrowania RSA 2048-bitowych kluczy zarządzanych przez klienta w usłudze Azure Key Vault. Aby uzyskać więcej informacji, zobacz [funkcji Transparent Data Encryption z obsługą Bring Your Own Key dla usługi Azure SQL Database i Data Warehouse](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql?view=azuresqldb-current).

Szyfrowanie po stronie klienta, danych usługi Azure SQL Database jest świadczona za pośrednictwem [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) funkcji. Zawsze szyfrowane używa klucza tworzone i przechowywane przez klienta. Klientów można przechowywać klucz główny w magazynie certyfikatów Windows, usługi Azure Key Vault lub lokalnego sprzętowego modułu zabezpieczeń. Za pomocą programu SQL Server Management Studio, użytkownicy SQL decydować, jakie klucza, czy chcesz używać do szyfrowania kolumny.

|                                  |                    | **Model szyfrowanie i zarządzanie kluczami** |                    |
|----------------------------------|--------------------|-----------------------------------------|--------------------|
|                                  | **Po stronie serwera za pomocą klucza zarządzanego przez usługę**     | **Strona serwera przy użyciu zarządzanych przez klienta w usłudze Key Vault**             | **Po stronie klienta przy użyciu zarządzanych przez klienta**      |
| **Magazynowi i bazom danych**        |                    |                    |                    |
| Disk (IaaS)                      | -                  | Tak, RSA 2048-bitowe  | -                  |
| SQL Server (IaaS)                | Yes                | Tak, RSA 2048-bitowe  | Yes                |
| Usługi Azure SQL (baza danych/Data Warehouse) | Yes                | Tak, RSA 2048-bitowe  | Yes                |
| Usługi Azure SQL (wystąpienie zarządzane bazy danych) | Yes                | (Wersja zapoznawcza) i RSA 2048-bitowe  | Yes                |
| Usługa Azure Storage (Blokuj/stronicowych obiektów blob) | Yes                | Tak, RSA 2048-bitowe  | Yes                |
| Usługa Azure Storage (pliki)            | Yes                | Tak, RSA 2048-bitowe  | -                  |
| Usługa Azure Storage (tabele, kolejki)   | Yes                | -                  | Yes                |
| Usługa cosmos DB (baza danych Documentdb)          | Yes                | -                  | -                  |
| Magazyn StorSimple                       | Yes                | -                  | Yes                |
| Backup                           | Yes                | -                  | Yes                |
| **Analizy i analizy**   |                    |                    |                    |
| Azure Data Factory               | Yes                | -                  | -                  |
| Azure Machine Learning           | -                  | (Wersja zapoznawcza) i RSA 2048-bitowe | -                  |
| Usługa Azure Stream Analytics           | Yes                | -                  | -                  |
| HDInsight (magazyn obiektów Blob platformy Azure)   | Yes                | -                  | -                  |
| HDInsight (magazyn usługi Data Lake)    | Yes                | -                  | -                  |
| Apache Kafka dla usługi HDInsight       | Yes                | W wersji zapoznawczej, wszystkie długości RSA | -                  |
| Azure Data Lake Store            | Yes                | Tak, RSA 2048-bitowe  | -                  |
| Azure Data Catalog               | Yes                | -                  | -                  |
| Power BI                         | Yes                | -                  | -                  |
| **IoT Services**                 |                    |                    |                    |
| Usługa IoT Hub                          | -                  | -                  | Yes                |
| Service Bus                      | Yes                | -                  | Yes                |
| Event Hubs                       | Yes                | -                  | -                  |
| Event Grid                       | Yes                | -                  | -                  |


## <a name="conclusion"></a>Podsumowanie

Ochrona dane klienta przechowywane w ramach usług platformy Azure jest sprawą do firmy Microsoft. Azure wszystkich hostowanych usług dokłada wszelkich starań, by zapewnić szyfrowania opcjach Rest. Podstawowe usług, takich jak Azure Storage, Azure SQL Database i kluczy analizy i usługi analizy już zapewniają szyfrowanie opcjach Rest. Niektóre z tych usług pomocy technicznej, kluczy klienta kontrolowane i szyfrowanie po stronie klienta, a także klucze zarządzane przez usługę i szyfrowania. Usługi Microsoft Azure jest szeroko udoskonalanie szyfrowania po udostępnieniu Rest i nowe opcje są planowane dla wersji zapoznawczej i powszechnie udostępnione w ciągu najbliższych miesięcy.
