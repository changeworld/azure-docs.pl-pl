---
title: Prywatność zabezpieczeń i danych w usłudze Azure Search | Dokumentacja firmy Microsoft
description: Usługa Azure Search jest zgodne z SOC 2, HIPAA i innych certyfikatów. Filtruje połączenia i szyfrowania, uwierzytelnianie i tożsamość dostępem do danych przez użytkowników i identyfikatorach grup zabezpieczeń w usłudze Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: heidist
ms.openlocfilehash: 888f7c3ced0ef48cff222bffdbf0f278fa5f42b3
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36285733"
---
# <a name="security-and-data-privacy-in-azure-search"></a>Prywatność zabezpieczeń i danych w usłudze Azure Search

Funkcje kompleksowe zabezpieczeń i kontroli dostępu są wbudowane w usłudze Azure Search, aby upewnić się, że prywatną zawartość pozostaje w ten sposób. W tym artykule wylicza zgodności funkcji i standardów zabezpieczeń wbudowanych w usłudze Azure Search.

Architektura zabezpieczeń usługi Azure Search zakresów zabezpieczeń fizycznych, zaszyfrowane transmisje zaszyfrowanych magazynów i zgodność ze standardami całej platformy. Pod względem operacyjnym usługi Azure Search akceptuje tylko uwierzytelnione żądania. Opcjonalnie można dodać użytkownika kontroli dostępu do zawartości za pomocą filtrów zabezpieczeń. W tym artykule dotyczące zabezpieczeń w każdej warstwie, ale koncentruje się przede wszystkim na jak są zabezpieczone dane i operacji w usłudze Azure Search.

## <a name="standards-compliance-iso-27001-soc-2-hipaa"></a>Zgodność ze standardami: ISO 27001, SOC 2, HIPAA

Częściowa lista zgodność ze standardami obejmuje SOC 2 typu 2 i HIPAA funkcji ogólnie dostępna. Funkcje w wersji zapoznawczej certyfikowanych jako część ogólnej dostępności i nie mogą być używane w rozwiązaniach o standardy określone wymagania. Certyfikacji zgodności jest udokumentowany w [zgodności Przegląd platformy Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) i [Centrum zaufania](https://www.microsoft.com/en-us/trustcenter). 

Certyfikacja następujące standardy został [ogłaszane w 2018 czerwca](https://azure.microsoft.com/blog/azure-search-is-now-certified-for-several-levels-of-compliance/).

+ [ISO 27001: 2013](https://www.iso.org/isoiec-27001-information-security.html) 
+ [Zgodność SOC 2 typu 2](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html) pełny raport, aby uzyskać [Azure - i Azure dla instytucji rządowych SOC 2 typu II raport](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports). 
+ [Health Insurance Portability and Accountability Act (HIPAA)](https://en.wikipedia.org/wiki/Health_Insurance_Portability_and_Accountability_Act)
+ [GxP (część 21 CFR 11)](https://en.wikipedia.org/wiki/Title_21_CFR_Part_11)
+ [HITRUST](https://en.wikipedia.org/wiki/HITRUST)
+ [PCI DSS poziom 1](https://en.wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)
+ [Australia IRAP Niesklasyfikowanego DLM](https://asd.gov.au/infosec/irap/certified_clouds.htm)

## <a name="encrypted-transmission-and-storage"></a>Zaszyfrowane transmisji i przechowywania

Rozszerza szyfrowania w całym potoku indeksowania całej: z połączeń, za pomocą transmisji i w dół indeksowanego danych przechowywanych w usłudze Azure Search.

| Warstwa zabezpieczeń | Opis |
|----------------|-------------|
| Szyfrowanie podczas przesyłania | Usługa Azure Search nasłuchuje na porcie protokołu HTTPS 443. Na platformie połączenia do usług platformy Azure są szyfrowane. |
| Szyfrowanie w spoczynku | Szyfrowanie jest w pełni internalized indeksowaniem bez zauważalnego wpływu na indeksowania czas do zakończenia lub rozmiar indeksu. Nastąpi to automatycznie indeksowania wszystkich tym na aktualizacje przyrostowe indeksu, który nie jest w pełni szyfrowane (utworzone przed 2018 styczeń).<br><br>Wewnętrznie, szyfrowanie jest oparte na [szyfrowanie usługi Magazyn Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption), za pomocą 256-bitowego [szyfrowania AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard).|

Szyfrowanie jest wewnętrzny do usługi Azure Search, certyfikaty i klucze szyfrujące wewnętrznie zarządzany przez firmę Microsoft i powszechnie stosowane. Nie można włączyć lub wyłączyć szyfrowanie, zarządzanie lub Zastąp własnych kluczy lub wyświetlić ustawienia szyfrowania w portalu lub programowo. 

Szyfrowanie magazynowanych ogłoszono w 24 stycznia 2018 i dotyczy wszystkich warstwy usług, w tym usług udostępnionych (bezpłatnie), we wszystkich regionach. Opcja szyfrowania pełnego indeksy utworzone przed tą datą należy porzucić i odbudować aby szyfrowania. W przeciwnym razie tylko nowe dane dodane po 24 stycznia są szyfrowane.

## <a name="azure-wide-user-access-controls"></a>Kontrolę dostępu użytkowników w całej platformy Azure

Kilka mechanizmów zabezpieczeń są dostępne całej Azure i utworzyć automatycznie w związku z tym jest dostępny do zasobów usługi Azure Search.

+ [Blokad na poziomie zasobów, aby zapobiec usunięciu lub subskrypcji](../azure-resource-manager/resource-group-lock-resources.md)
+ [Oparta na rolach kontroli dostępu (RBAC) do kontrolowania dostępu do informacji i wykonywanie operacji administracyjnych](../role-based-access-control/overview.md)

Wszystkie usługi Azure obsługę kontroli dostępu opartej na rolach (RBAC) ustawienie poziomy dostępu konsekwentnie we wszystkich usługach. Na przykład wyświetlanie poufnych danych, takich jak klucz administratora jest ograniczone do właściciela i współautor role, wyświetlanie stanu usługi jest dostępne dla członków żadnej roli. RBAC dostępnych ról właściciela, współautora ani czytnika. Domyślnie w przypadku wszystkich administratorów usługi są członkami roli właściciela.

## <a name="service-access-and-authentication"></a>Dostęp do usługi i uwierzytelniania

Podczas wyszukiwania Azure dziedziczy zabezpieczenia zabezpieczeń platformy Azure, także własną uwierzytelniania opartego na kluczach. Klucz interfejsu api to ciąg składający się z losowo generowany liter i cyfr. Typ klucza (admin lub zapytanie) określa poziom dostępu. Przesłanie prawidłowego klucza jest uznawany za dowód żądanie pochodzi z zaufanego jednostki. Dwa typy klucze są używane do uzyskania dostępu do usługi wyszukiwania:

* Administrator (prawidłowe do żadnej operacji odczytu i zapisu z usługą)
* Zapytania (nieprawidłowy dla operacji tylko do odczytu, takich jak zapytań względem indeksu)

Klucze administratora są tworzone po zainicjowaniu obsługi usługi. Dostępne są dwa klucze administratora, wyznaczony jako *głównej* i *dodatkowej* zachować je bezpośrednio, ale w rzeczywistości są wymienne. Każda usługa ma dwa klucze administratora, dzięki czemu można jedną przerzucane bez utraty dostępu do usługi. Można ponownie wygenerować klucza albo administratora, ale nie można dodać do liczby całkowitej admin klucza. Brak maksymalnie dwa klucze administratora dla usługi wyszukiwania.

Klucze zapytania są tworzone w razie potrzeby i są przeznaczone dla aplikacji klienckich, które bezpośrednio wywołać wyszukiwania. Można utworzyć maksymalnie 50 klucze zapytania. W kodzie aplikacji należy określić adres URL wyszukiwania i klucz api zapytania, aby zezwolić na dostęp tylko do odczytu do usługi. Kod aplikacji określa również indeks używanych przez aplikację. Punkt końcowy, klucz interfejsu api, aby uzyskać dostęp tylko do odczytu i indeksu docelowego określa poziom dostępu i zakres połączenia do aplikacji klienckiej.

Wymagane jest uwierzytelnienie na każdym żądaniu, gdzie każde żądanie składa się z kluczem obowiązkowe, operacji i obiektu. Gdy połączonych ze sobą, dwa poziomy uprawnień (pełna lub tylko do odczytu) oraz kontekst (na przykład operacji zapytania w indeksie) są wystarczające dla zapewnianie bezpieczeństwa full spektrum na nich operacji usługi. Aby uzyskać więcej informacji o kluczach, zobacz [Utwórz i Zarządzaj kluczami interfejsu api](search-security-api-keys.md).

## <a name="index-access"></a>Dostęp do indeksu

W usłudze Azure Search konkretnego indeksu nie jest zabezpieczanego obiektu. Zamiast tego dostęp do indeksu jest określany w warstwie usług (odczytu lub zapisu), wraz z kontekstu operacji.

Dostęp użytkownika końcowego można struktury żądań zapytań nawiązywanie połączeń za pomocą klucza zapytania, co sprawia, że wszystkie żądania tylko do odczytu i obejmują określonego indeksu używany przez aplikację. W żądanie kwerendy nie ma żadnych koncepcji indeksy lub jednocześnie dostęp do wielu indeksów, więc wszystkie żądania target jeden indeks przez definicję. W efekcie konstruowania żądania zapytania sam (klucz oraz indeksu pojedynczym elementem docelowym) definiuje granicy zabezpieczeń.

Jest niesortowalne administratorów i deweloperów do indeksów: zarówno musi uzyskać dostęp do tworzenia, usuwania i aktualizowania obiektów zarządzanych przez usługę. Każda osoba mająca klucz administratora do usługi można odczytać, zmodyfikować lub usunąć indeksu w tej samej usługi. Do ochrony przed przypadkowym lub złośliwymi usuwania indeksów do kontroli źródła wewnętrznych zasobów kodu jest remedy dla cofania niechciane indeksu usunięcie lub zmiana. Usługa wyszukiwanie Azure ma trybu failover w klastrze, aby zapewnić dostępność, ale nie przechowuje ani nie wykonać zastrzeżonych kod służący do tworzenia lub załadować indeksów.

Wielodostępności rozwiązań wymagających granic zabezpieczeń na poziomie indeksu takich rozwiązań są zwykle zawierają warstwy środkowej, którzy klienci używa do obsługi izolacji indeksu. Aby uzyskać więcej informacji na temat przypadek użycia wielodostępnym, zobacz [wzorce dla wielodostępnych aplikacji SaaS i usługi Azure Search projektowe](search-modeling-multitenant-saas-applications.md).

## <a name="admin-access-from-client-apps"></a>Dostęp administratora z aplikacji klienta

Interfejsu API REST usługi Azure Search zarządzania jest rozszerzeniem z usługi Azure Resource Manager i udostępnia jego zależności. W efekcie usługi Active Directory jest wymagana do zarządzania usługi Azure Search. Wszystkie żądania administracyjnych z kodu klienta musi zostać uwierzytelniony przy użyciu usługi Azure Active Directory, zanim żądanie dotrze Menedżera zasobów.

Dane żądania do punktu końcowego usługi Azure Search, takie jak Create Index (wyszukiwania usługi interfejsu API REST Azure) lub dokumenty wyszukiwania (wyszukiwania usługi interfejsu API REST Azure), użyj klucz interfejsu api w nagłówku żądania.

Kod aplikacji obsługuje operacje administracyjne usługi, a także operacje na danych w dokumentach lub indeksy wyszukiwania, należy wdrożyć dwa podejścia do uwierzytelniania w kodzie: klucz dostępu kodu natywnego do usługi Azure Search i uwierzytelniania usługi Active Directory metody wymagane przez Menedżera zasobów. 

Aby dowiedzieć się, jak struktury żądania w usłudze Azure Search, zobacz [REST usługi wyszukiwanie Azure](https://docs.microsoft.com/rest/api/searchservice/). Aby uzyskać więcej informacji na temat wymagań dotyczących uwierzytelniania dla Menedżera zasobów, zobacz [interfejs API uwierzytelniania Użyj Menedżera zasobów do subskrypcji dostępu](../azure-resource-manager/resource-manager-api-authentication.md).

## <a name="user-access-to-index-content"></a>Dostęp użytkownika do indeksu zawartości

Dostęp użytkownika do zawartości indeksu jest implementowane za pośrednictwem filtrów zabezpieczeń na zapytań, zwracając dokumenty skojarzone z tożsamością zabezpieczeń. Zamiast wstępnie zdefiniowanych ról i przypisania ról kontroli dostępu na podstawie tożsamości jest implementowany jako filtr czy TRIM wyszukiwanie wyników dokumentów i zawartości na podstawie tożsamości. W poniższej tabeli opisano dwa podejścia do wyniki wyszukiwania przycinanie nieautoryzowanego zawartości.

| Podejście | Opis |
|----------|-------------|
|[Przycinanie zabezpieczeń oparte na tożsamości, filtry](search-security-trimming-for-azure-search.md)  | Dokumenty podstawowy przepływ pracy do wykonywania kontroli dostępu tożsamości użytkownika. Obejmuje dodawanie identyfikatorów zabezpieczeń do indeksu i wyjaśniono, filtrowania pod kątem tego pola, aby przyciąć wyniki zabronione zawartości. |
|[Przycinanie zabezpieczeń oparte na tożsamości usługi Azure Active Directory](search-security-trimming-for-azure-search-with-aad.md)  | W tym artykule jest rozwijane w poprzednim artykule etapy pobierania tożsamości z usługi Azure Active Directory (AAD), jeden z [wolne usług](https://azure.microsoft.com/free/) na platformie Azure chmury. |

## <a name="table-permissioned-operations"></a>Tabeli: Operacje Permissioned

W poniższej tabeli przedstawiono operacje dozwolone w usłudze Azure Search i klucz, który umożliwia odblokowanie dostępu do określonej operacji.

| Operacja | Uprawnienia |
|-----------|-------------------------|
| Tworzenie usługi | Posiadacz subskrypcji platformy Azure|
| Skalowanie usługi | Klucz administratora, RBAC właścicielem lub współautorem zasobu  |
| Usuwanie usługi | Klucz administratora, RBAC właścicielem lub współautorem zasobu |
| Tworzenie, modyfikowanie i usuwanie obiektów usługi: <br>Indeksy i składniki (takie jak definicje analizatora, oceniania profile, opcje mechanizmu CORS), indeksatorów, źródła danych, synonimy, sugestorów. | Klucz administratora, RBAC właścicielem lub współautorem zasobu  |
| Tworzenie zapytań względem indeksu | Klucz administratora lub zapytania (nie dotyczy RBAC) |
| Wyślij zapytanie do informacji o systemie, takich jak zwracanie statystyk, liczby i listy obiektów. | Klucz administratora, RBAC zasobu (czytnik właściciela, współautora) |
| Zarządzanie kluczami administratora | Klucz administratora, RBAC właścicielem lub współautorem zasobu. |
| Zarządzanie kluczami zapytań |  Klucz administratora, RBAC właścicielem lub współautorem zasobu.  |

## <a name="physical-security"></a>Zabezpieczenia fizyczne

Centrach danych firmy Microsoft Podaj branży zabezpieczeń fizycznych i są zgodne z szeroką gamę portfolio standardów i przepisami. Aby dowiedzieć się więcej, przejdź do [centrów danych globalnych](https://www.microsoft.com/cloud-platform/global-datacenters) strony lub Obejrzyj krótki klip wideo na danych Centrum zabezpieczeń.

> [!VIDEO https://www.youtube.com/embed/r1cyTL8JqRg]


## <a name="see-also"></a>Zobacz także

+ [Pobierz uruchomiona .NET (pokazano tworzenie indeksu za pomocą klucza administratora)](search-create-index-dotnet.md)
+ [Pobierz uruchomiona REST (pokazano tworzenie indeksu za pomocą klucza administratora)](search-create-index-rest-api.md)
+ [Kontrola dostępu oparta na tożsamości, za pomocą usługi Azure Search filtrów](search-security-trimming-for-azure-search.md)
+ [Kontrola dostępu na podstawie tożsamości katalogu Active za pomocą usługi Azure Search filtrów](search-security-trimming-for-azure-search-with-aad.md)
+ [Filtry w usłudze Azure Search](search-filters.md)