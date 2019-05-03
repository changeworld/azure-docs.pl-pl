---
title: Zachowania zabezpieczeń i danych — usługa Azure Search
description: Usługa Azure Search jest zgodna z SOC 2, HIPAA i innych certyfikatów. Służy do przefiltrowania połączenia i szyfrowania, uwierzytelniania i tożsamości dostęp do danych przez użytkowników i grupy identyfikatorów zabezpieczeń w usłudze Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: f366726f539a817f515a78fbc35bfeaa3b65514e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024498"
---
# <a name="security-and-data-privacy-in-azure-search"></a>Zachowania zabezpieczeń i danych w usłudze Azure Search

Kompleksowe funkcje zabezpieczeń i kontroli dostępu są wbudowane w usłudze Azure Search, aby upewnić się, że prywatnej zawartość pozostaje w ten sposób. W tym artykule wylicza zgodności standardów i funkcje zabezpieczeń wbudowane w usłudze Azure Search.

Architektura zabezpieczeń usługi Azure Search zakresów zabezpieczeń fizycznych, zaszyfrowanego transmisji, szyfrowany Magazyn oraz zgodność ze standardami całej platformy. Pod względem operacyjnym usługi Azure Search akceptuje tylko uwierzytelnione żądania. Opcjonalnie można dodać użytkownika kontroli dostępu do zawartości za pomocą filtrów zabezpieczeń. W tym artykule dotyczą zabezpieczeń w każdej warstwie, ale koncentruje się głównie na jak są zabezpieczone dane i operacji w usłudze Azure Search.

## <a name="standards-compliance-iso-27001-soc-2-hipaa"></a>Zgodność ze standardami: ISO 27001, SOC 2, HIPAA

Usługa Azure Search jest certyfikowany do pracy następujące standardy jako [ogłoszone w czerwcu 2018](https://azure.microsoft.com/blog/azure-search-is-now-certified-for-several-levels-of-compliance/):

+ [ISO 27001:2013](https://www.iso.org/isoiec-27001-information-security.html) 
+ [Audyt SOC 2 typu 2 zgodności](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html) pełny raport, przejdź do [platformy Azure — i platformy Azure dla instytucji rządowych SOC 2 typu II raportu](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports). 
+ [Health Insurance Portability and Accountability Act (HIPAA)](https://en.wikipedia.org/wiki/Health_Insurance_Portability_and_Accountability_Act)
+ [GxP (21 CFR Part 11)](https://en.wikipedia.org/wiki/Title_21_CFR_Part_11)
+ [HITRUST](https://en.wikipedia.org/wiki/HITRUST)
+ [PCI DSS Level 1](https://en.wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)
+ [Australia IRAP Unclassified DLM](https://asd.gov.au/infosec/irap/certified_clouds.htm)

Zgodność ze standardami dotyczy funkcji jest ogólnie dostępna. Funkcje w wersji zapoznawczej są certyfikowane podczas przejścia, które są ogólnie dostępne i nie mogą być używane w rozwiązaniach o wymagania rygorystyczne standardy. Certyfikacja zgodności jest udokumentowany w [zgodności omówienie platformy Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) i [Centrum zaufania](https://www.microsoft.com/en-us/trustcenter). 

## <a name="encrypted-transmission-and-storage"></a>Zaszyfrowanego transmisji i przechowywania

Szyfrowanie rozszerza się w całym całego potoku indeksowania: z połączeń, za pośrednictwem przesyłania i w dół do indeksowane dane przechowywane w usłudze Azure Search.

| Warstwa zabezpieczeń | Opis |
|----------------|-------------|
| Szyfrowanie podczas transferu <br>(HTTPS/SSL/TLS) | Usługa Azure Search nasłuchuje na porcie HTTPS 443. Na platformie połączenia z usługami platformy Azure są szyfrowane. <br/><br/>Wszystkie usługi klienta usługi Azure Search interakcje są protokoły SSL/TLS 1.2 stanie.  Należy użyć zabezpieczeń TLSv1.2 dla połączeń SSL z usługą.|
| Szyfrowanie w spoczynku <br>Klucze zarządzane przez firmy Microsoft | Szyfrowanie jest w pełni internalized indeksowaniem z nie zauważalnego wpływu na indeksowanie czas do zakończenia lub rozmiar indeksu. Nastąpi to automatycznie na indeksowanie wszystkich tym na aktualizacje przyrostowe indeksu, który nie jest w pełni zaszyfrowane (utworzonym przed stycznia 2018 r.).<br><br>Wewnętrznie, szyfrowanie jest oparte na [szyfrowanie usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption), za pomocą 256-bitowego [szyfrowania AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard).<br><br> Szyfrowanie jest wewnętrzny do usługi Azure Search przy użyciu certyfikatów i kluczy szyfrowania zarządza wewnętrznie przez firmę Microsoft i powszechnie stosowane. Nie można włączyć szyfrowanie lub wyłączyć, zarządzanie lub Zastąp własnych kluczy lub wyświetlić ustawienia szyfrowania w portalu lub programowo.<br><br>Szyfrowanie w spoczynku ogłoszono 24 stycznia 2018 r. i ma zastosowanie do wszystkich warstwach usługi, w tym usług udostępnionych (wersja bezpłatna), we wszystkich regionach. Pełne szyfrowanie indeksów utworzonych przed tą datą należy porzucić i ponownie skompilowany w kolejności do szyfrowania. W przeciwnym razie są szyfrowane tylko nowe dane dodane po 24 stycznia.|
| Szyfrowanie w spoczynku <br>Klucze zarządzane przez klienta | Szyfrowanie za pomocą kluczy klienta zarządzanego jest **Podgląd** services funkcja, która nie jest dostępna bezpłatnie. Usługi płatne, jest on dostępny tylko dla usług wyszukiwania utworzonych na lub po 2019 stycznia przy użyciu najnowszej wersji zapoznawczej wersji interfejsu api (interfejs api-version = 2019-05-06-Preview).<br><br>Indeksy usługi Azure Search i mapy synonimów mogą teraz być szyfrowane, gdy za pomocą kluczy zarządzanych kluczy klienta w usłudze Azure Key Vault. Aby dowiedzieć się więcej, zobacz [zarządzać kluczami szyfrowania w usłudze Azure Search](search-security-manage-encryption-keys.md).<br>Ta funkcja nie zastępuje domyślne szyfrowanie danych magazynowanych, ale raczej zastosowane oprócz go.<br>Włączenie tej funkcji zwiększyć rozmiar indeksu i obniżyć wydajność zapytań. Na podstawie obserwacji do daty, użytkownik może pojawić się wzrost o 30 – 60% czas zapytania mimo, że rzeczywista wydajność może się różnić w zależności od definicji indeksu i typy zapytań. Ze względu na to wpływ na wydajność zaleca się tylko włączyć tę funkcję tylko dla indeksów, które rzeczywiście tego wymagają.

## <a name="azure-wide-user-access-controls"></a>Kontrolę dostępu użytkowników w całej platformy Azure

Wiele mechanizmów zabezpieczeń są dostępne sieci platformy Azure i Utwórz automatycznie dostępne w ten sposób z zasobami usługi Azure Search.

+ [Blokady na poziomie zasobów, aby zapobiec usunięciu lub subskrypcji](../azure-resource-manager/resource-group-lock-resources.md)
+ [Oparta na rolach kontrola dostępu (RBAC) do kontrolowania dostępu do informacji i operacje administracyjne](../role-based-access-control/overview.md)

Wszystkich usług platformy Azure obsługuje kontroli dostępu opartej na rolach (RBAC) do ustawiania poziomów dostępu spójne we wszystkich usługach. Na przykład wyświetlanie poufne dane, takie jak klucz administratora jest ograniczony do ról właściciel i współautor, wyświetlanie stanu usługi jest dostępne dla członków żadnej roli. RBAC zawiera role właściciela, współautora i czytelnika. Domyślnie wszyscy administratorzy usługi są członkami roli właściciela.

<a name="service-access-and-authentication"></a>

## <a name="service-access-and-authentication"></a>Dostęp do usługi i uwierzytelniania

Gdy usługa Azure Search dziedziczy zabezpieczenia zabezpieczeń platformy Azure, zapewnia również swój własny uwierzytelniania opartego na kluczu. Klucz interfejsu api to ciąg składający się z liter i cyfr generowany losowo. Typ klucza (admin lub zapytanie) określa poziom dostępu. Przesyłanie prawidłowy klucz jest traktowany jako będący dowód żądanie pochodzi z zaufanego jednostki. 

Istnieją dwa poziomy dostępu do usługi wyszukiwania włączane przez dwa typy kluczy:

* Dostęp administratora (prawidłowe w kontekście operacji odczytu i zapisu względem usługi)
* Dostęp zapytań (ważny dla operacji tylko do odczytu, takich jak zapytania względem kolekcji dokumentów indeksu)

*Klucze administratora* są tworzone, gdy usługa jest aprowizowana. Dostępne są dwa klucze administratora, wyznaczony jako *podstawowego* i *dodatkowej* je bezpośrednio, ale w rzeczywistości są one wymienne. Każda usługa ma dwa klucze administratora, dzięki czemu można je przerzucić jeden bez utraty dostępu do usługi. Możesz [administratora ponowne generowanie klucza](search-security-api-keys.md#regenerate-admin-keys) okresowo na zabezpieczeń platformy Azure najlepszych rozwiązań, ale nie można dodać do liczby kluczy administratora całkowitej. Istnieje więcej niż dwa klucze administratora dla usługi wyszukiwania.

*Kluczami zapytań* są tworzone w miarę potrzeb i są przeznaczone dla aplikacji klienckich, które wystawiają zapytania. Możesz utworzyć maksymalnie 50 klucze zapytania. W kodzie aplikacji należy określić adres URL wyszukiwania i klucz interfejsu api zapytań, aby zezwolić na dostęp tylko do odczytu do kolekcji documents określonego indeksu. Punkt końcowy, klucz interfejsu api, aby uzyskać dostęp tylko do odczytu i indeksu docelowego określa poziom zakresu i dostęp do połączenia z aplikacji klienckiej.

Wymagane jest uwierzytelnienie na każde żądanie, w którym każde żądanie składa się z kluczem obowiązkowe, operacji i obiektu. Gdy połączonych ze sobą, dwóch poziomach uprawnień (pełny lub tylko do odczytu), a także kontekst (na przykład dla operacji zapytania względem indeksu) są wystarczające dla zapewniających bezpieczeństwo szerokim w operacji usługi. Aby uzyskać więcej informacji na temat kluczy, zobacz [tworzenie i zarządzanie kluczami interfejsu api](search-security-api-keys.md).

## <a name="index-access"></a>Dostęp do indeksu

W usłudze Azure Search konkretnego indeksu nie jest zabezpieczanego obiektu. Zamiast tego dostęp do indeksu jest określany w warstwie usługi (Odczyt lub zapis), wraz z kontekstu operacji.

Na potrzeby dostępu użytkownika końcowego można strukturę żądań zapytań połączyć się przy użyciu klucza zapytania, co sprawia, że wszystkie żądania tylko do odczytu i obejmują określonego indeksu używanych przez aplikację. W żądaniu zapytania nie obowiązuje koncepcja indeksów lub jednocześnie dostęp do wielu indeksów, dzięki czemu wszystkie żądania docelowe jeden indeks zgodnie z definicją. W efekcie konstruowania żądania zapytania, sama (klucz oraz indeksu pojedynczy element docelowy) definiuje granicy zabezpieczeń.

Takimi samymi jest dostępu administratorów i deweloperów indeksów: zarówno musi uzyskać dostęp do tworzenia, usuwania i aktualizowania obiektów zarządzanych przez usługę. Każda osoba mająca klucz administratora usługi można przeczytać, zmodyfikować lub usunąć dowolnego indeksu w ramach tej samej usługi. W celu ochrony przed przypadkowym lub złośliwym usunięciem indeksów trwałych kod do kontroli źródła wewnętrznych jest nieosiągnięcia cofania niechciane indeksu usunięcie lub zmiana. Usługa Azure Search ma trybu failover w ramach klastra, aby zapewnić dostępność, ale nie przechowuje ani nie wykonywanie własności kodu użytego do utworzenia lub ładowanie indeksów.

Wielodostępność rozwiązań wymagających granic zabezpieczeń na poziomie indeksu w przypadku takich rozwiązań zazwyczaj obejmują warstwy środkowej, w których klientów używać do obsługi izolacji indeksu. Aby uzyskać więcej informacji na temat przypadek użycia wielodostępnych zobacz [wzorce projektowe dla wielodostępnych aplikacji SaaS i usługa Azure Search](search-modeling-multitenant-saas-applications.md).

## <a name="admin-access"></a>Prawa dostępu administratora

[Dostępu opartej na rolach (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) Określa, czy masz dostęp do formantów za pośrednictwem usługi i jego zawartości. Jeśli jesteś właścicielem lub współautorem w usłudze Azure Search, możesz użyć portalu lub programu PowerShell **Az.Search** modułu do utworzenia, aktualizacji lub usuwania obiektów w usłudze. Można również użyć [interfejsu API REST zarządzania usługi Azure Search](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api).

## <a name="user-access"></a>Dostęp użytkowników

Domyślnie dostęp użytkowników do indeksu jest określany przez klucz dostępu dla żądania zapytania. Większość deweloperów, tworzenie i przypisywanie [ *kluczami zapytań* ](search-security-api-keys.md) na żądanie wyszukiwania po stronie klienta. Klucz zapytania przyznaje dostęp do odczytu do całej zawartości w indeksie.

Jeśli potrzebujesz szczegółowego, na użytkownika na kontrolę zawartości, możesz utworzyć filtrów zabezpieczeń na zapytania, zwracając dokumenty skojarzone z tożsamością zabezpieczeń. Zamiast wstępnie zdefiniowanych ról i przypisań ról, kontrola dostępu oparta na tożsamości jest implementowany jako *filtru* czy TRIM wyszukiwanie wyników dokumentów i zawartości oparte na tożsamości. W poniższej tabeli opisano dwa podejścia do wyników wyszukiwania przycinania zawartości nieautoryzowanym.

| Podejście | Opis |
|----------|-------------|
|[Dostosowanie do zabezpieczeń oparte na filtrów tożsamości](search-security-trimming-for-azure-search.md)  | Dokumenty podstawowy przepływ pracy do wykonywania kontroli dostępu tożsamości użytkownika. Obejmuje dodawanie identyfikatorów zabezpieczeń do indeksu i wyjaśnia, filtrowania pod kątem tego pola, aby przyciąć wyniki zabronionych zawartości. |
|[Dostosowanie do zabezpieczeń oparte na tożsamości usługi Azure Active Directory](search-security-trimming-for-azure-search-with-aad.md)  | Ten artykuł stanowi rozszerzenie poprzednim artykule, zapewniając kroki pobierania tożsamości z usługi Azure Active Directory (AAD), jednego z [bezpłatnych usług](https://azure.microsoft.com/free/) na platformie chmury platformy Azure. |

## <a name="table-permissioned-operations"></a>Tabela: Operacje otrzymało uprawnienia

W poniższej tabeli przedstawiono operacje, dozwolone w usłudze Azure Search i klucz, który umożliwia odblokowanie dostępu do określonej operacji.

| Operacja | Uprawnienia |
|-----------|-------------------------|
| Tworzenie usługi | Właściciel subskrypcji platformy Azure|
| Skalowanie usługi | Klucz administratora, współautora w zasobie lub właściciela RBAC  |
| Usuwanie usługi | Klucz administratora, współautora w zasobie lub właściciela RBAC |
| Tworzenie, modyfikowanie i usuwanie obiektów w usłudze: <br>Indeksy i składniki (takie jak definicje analizatora, profile oceniania, opcje CORS), indeksatory, źródła danych, synonimy, sugestory. | Klucz administratora, współautora w zasobie lub właściciela RBAC  |
| Tworzenie zapytań względem indeksu | Klucz administratora lub zapytanie (RBAC, role nie dotyczy) |
| Zapytania informacje systemowe, takie jak zwracanie statystyki, liczniki i listy obiektów. | Klucz administratora, RBAC dla zasobu (właściciel, współautor, Czytelnik) |
| Zarządzanie kluczami administratora | Klucz administratora, współautora w zasobie lub właściciela RBAC. |
| Zarządzanie kluczami zapytań |  Klucz administratora, współautora w zasobie lub właściciela RBAC.  |

## <a name="physical-security"></a>Zabezpieczenia fizyczne

Centra danych firmy Microsoft zapewniają wiodące w branży zabezpieczeń fizycznych i są zgodne z szeroką gamę standardami i przepisami. Aby dowiedzieć się więcej, przejdź do [globalnych centrów danych](https://www.microsoft.com/cloud-platform/global-datacenters) strony lub Obejrzyj krótki film wideo na danych Centrum zabezpieczeń.

> [!VIDEO https://www.youtube.com/embed/r1cyTL8JqRg]


## <a name="see-also"></a>Zobacz także

+ [Rozpoczynanie pracy .NET (pokazuje przy użyciu klucza administratora, aby utworzyć indeks)](search-create-index-dotnet.md)
+ [Rozpoczynanie pracy POZOSTAŁEJ (pokazuje przy użyciu klucza administratora, aby utworzyć indeks)](search-create-index-rest-api.md)
+ [Kontrola dostępu na podstawie tożsamości przy użyciu filtrów usługi Azure Search](search-security-trimming-for-azure-search.md)
+ [Aktywna kontrola dostępu na podstawie tożsamości katalogu przy użyciu filtrów usługi Azure Search](search-security-trimming-for-azure-search-with-aad.md)
+ [Filtry w usłudze Azure Search](search-filters.md)