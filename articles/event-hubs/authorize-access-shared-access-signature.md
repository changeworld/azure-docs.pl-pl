---
title: Autoryzuj dostęp z podpisem dostępu współdzielonego w usłudze Azure Event Hubs
description: Ten artykuł zawiera informacje dotyczące autoryzowania dostępu do zasobów usługi Azure Event Hubs przy użyciu sygnatur dostępu współdzielonego (SAS).
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: bdb1896f8a40c6de21ae76b536bfccec316341cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69992798"
---
# <a name="authorizing-access-to-event-hubs-resources-using-shared-access-signatures"></a>Autoryzowanie dostępu do zasobów Centrum zdarzeń przy użyciu podpisów dostępu współdzielonego
Sygnatura dostępu współdzielonego (SAS) umożliwia udzielenie ograniczonego dostępu do zasobów w obszarze nazw centrum zdarzeń. SAS chroni dostęp do zasobów centrum zdarzeń na podstawie reguł autoryzacji. Te reguły są skonfigurowane w obszarze nazw lub encji (centrum zdarzeń lub tematu). Ten artykuł zawiera omówienie modelu sygnatury dostępu Współdzielonego i przegląda najważniejsze rozwiązania sygnatury dostępu Współdzielonego.

## <a name="what-are-shared-access-signatures"></a>Co to są podpisy dostępu współdzielonego?
Sygnatura dostępu współdzielonego (SAS) zapewnia delegowany dostęp do zasobów centrum zdarzeń na podstawie reguł autoryzacji. Reguła autoryzacji ma nazwę, jest skojarzona z określonymi prawami i zawiera parę kluczy kryptograficznych. Nazwa i klucz reguły są używane za pośrednictwem klientów usługi Event Hubs lub w kodzie do generowania tokenów sygnatury dostępu Współdzielonego. Klient może następnie przekazać token do centrum zdarzeń, aby udowodnić autoryzację żądanej operacji.

Sygnatury dostępu Współdzielonego jest mechanizm autoryzacji oparty na oświadczeniach przy użyciu prostych tokenów. Za pomocą sygnatury dostępu Współdzielonego klucze nigdy nie są przekazywane na przewodach. Klucze są używane do kryptograficznie podpisywania informacji, które mogą być później zweryfikowane przez usługę. Sygnatury dostępu Współdzielonego można używać podobnie do schematu nazwy użytkownika i hasła, w którym klient jest w bezpośrednim posiadaniu nazwy reguły autoryzacji i pasującego klucza. Sygnatury dostępu Współdzielonego można używać podobnie do modelu zabezpieczeń federacyjnego, w którym klient otrzymuje ograniczony czasowo i podpisany token dostępu z usługi tokenu zabezpieczającego bez konieczności posiadania klucza podpisywania.

> [!NOTE]
> Usługa Azure Event Hubs obsługuje autoryzowanie zasobów usługi Event Hubs przy użyciu usługi Azure Active Directory (Azure AD). Autoryzowanie użytkowników lub aplikacji przy użyciu tokenu OAuth 2.0 zwracanego przez usługę Azure AD zapewnia doskonałe bezpieczeństwo i łatwość użycia za pośrednictwem sygnatur dostępu współdzielonego (SAS). Dzięki usłudze Azure AD nie ma potrzeby przechowywania tokenów w kodzie i potencjalnych luk w zabezpieczeniach.
>
> Firma Microsoft zaleca korzystanie z usługi Azure AD z aplikacjami usługi Azure Event Hubs, jeśli to możliwe. Aby uzyskać więcej informacji, zobacz [Autoryzowanie dostępu do zasobu usługi Azure Event Hubs przy użyciu usługi Azure Active Directory](authorize-access-azure-active-directory.md).

> [!IMPORTANT]
> Tokeny sas (shared access signatures) mają kluczowe znaczenie dla ochrony zasobów. Zapewniając szczegółowość, SAS udziela klientom dostępu do zasobów centrum zdarzeń. Nie powinny one być udostępniane publicznie. Podczas udostępniania, jeśli jest to wymagane ze względu na rozwiązywanie problemów, należy rozważyć użycie zmniejszonej wersji plików dziennika lub usunięcie tokenów sygnatury dostępu Współdzielonego (jeśli są obecne) z plików dziennika i upewnij się, że zrzuty ekranu nie zawierają informacji sygnatury dostępu Współdzielonego albo.

## <a name="shared-access-authorization-policies"></a>Zasady autoryzacji dostępu współdzielonego
Każdy obszar nazw Centrum zdarzeń i każda encja Centrum zdarzeń (wystąpienie centrum zdarzeń lub temat platformy Kafka) ma zasady autoryzacji dostępu współdzielonego składające się z reguł. Zasady na poziomie obszaru nazw mają zastosowanie do wszystkich jednostek wewnątrz obszaru nazw, niezależnie od ich konfiguracji poszczególnych zasad.
Dla każdej reguły zasad autoryzacji decydujesz o trzech informacjach: nazwie, zakresie i prawach. Nazwa jest unikatową nazwą w tym zakresie. Zakres jest identyfikatorem URI danego zasobu. W przypadku obszaru nazw Centrum zdarzeń zakresem jest w pełni kwalifikowana `https://<yournamespace>.servicebus.windows.net/`nazwa domeny (FQDN), na przykład .

Prawa przewidziane przez regułę zasad mogą być kombinacją:
- **Wyślij** — daje prawo do wysyłania wiadomości do jednostki
- **Słuchaj** – daje prawo do słuchania lub otrzymywania podmiotowi
- **Zarządzanie** — daje prawo do zarządzania topologią obszaru nazw, w tym tworzenia i usuwania jednostek

> [!NOTE]
> Prawo **do zarządzania** obejmuje prawa **do wysyłania** i **nasłuchij.**

Zasady obszaru nazw lub encji mogą zawierać maksymalnie 12 reguł autoryzacji dostępu współdzielonego, zapewniając miejsce dla trzech zestawów reguł, z których każdy obejmuje prawa podstawowe i kombinację wyślij i nasłuchuj. Ten limit podkreśla, że magazyn zasad sygnatury dostępu Współdzielonego nie jest przeznaczony do magazynu kont użytkowników lub usług. Jeśli aplikacja musi udzielić dostępu do zasobów usługi Event Hubs na podstawie tożsamości użytkowników lub usług, należy zaimplementować usługę tokenu zabezpieczającego, która wystawia tokeny sygnatury dostępu współdzielonego po sprawdzeniu uwierzytelniania i dostępu.

Regułie autoryzacji jest przypisywany **klucz podstawowy** i **klucz pomocniczy**. Klucze te są kryptograficznie silne klucze. Nie trać ich ani nie wyciekaj. Będą one zawsze dostępne w witrynie Azure portal. Można użyć jednego z wygenerowanych kluczy i można je ponownie wygenerować w dowolnym momencie. Jeśli ponownie wygenerujesz lub zmienisz klucz w zasadach, wszystkie wcześniej wystawione tokeny oparte na tym kluczu staną się natychmiast nieprawidłowe. Jednak bieżące połączenia utworzone na podstawie takich tokenów będą nadal działać do momentu wygaśnięcia tokenu.

Podczas tworzenia obszaru nazw Centrum zdarzeń dla obszaru nazw jest automatycznie tworzona reguła zasad o nazwie **RootManageSharedAccessKey.** Ta zasada ma **uprawnienia do zarządzania** dla całej przestrzeni nazw. Zaleca się traktowanie tej reguły jak administracyjnego konta głównego i nieużyj jej w aplikacji. Dodatkowe reguły zasad można utworzyć na karcie **Konfigurowanie** obszaru nazw w portalu za pośrednictwem programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

## <a name="best-practices-when-using-sas"></a>Najlepsze rozwiązania dotyczące korzystania z sygnatury dostępu współdzielonego
Korzystając z podpisów dostępu współdzielonego w aplikacjach, musisz mieć świadomość dwóch potencjalnych zagrożeń:

- Jeśli sygnatury dostępu Współdzielonego jest przecieka, może być używany przez każdego, kto go uzyskuje, co może potencjalnie naruszyć zasoby usługi Event Hubs.
- Jeśli sygnatury dostępu Współdzielonego dostarczonego do aplikacji klienckiej wygaśnie, a aplikacja nie może pobrać nowego sygnatury dostępu Współdzielonego z usługi, funkcjonalność aplikacji może być utrudniona.

Następujące zalecenia dotyczące korzystania z podpisów dostępu współdzielonego mogą pomóc w ograniczeniu tych zagrożeń:

- **Klienci automatycznie odnawiają sygnaturę dostępu Współdzielonego**w razie potrzeby: Klienci powinni odnowić sygnaturę dostępu Współdzielonego na długo przed wygaśnięciem, aby dać czas na ponowną ponawienie, jeśli usługa zapewniająca sygnaturę dostępu Współdzielonego jest niedostępna. Jeśli sygnatury dostępu Współdzielonego jest przeznaczony do użycia dla niewielkiej liczby natychmiastowych, krótkotrwałych operacji, które mają zostać zakończone w okresie wygaśnięcia, może to być niepotrzebne, ponieważ nie oczekuje się, że sygnatury dostępu Współdzielonego nie zostanie odnowiony. Jednak jeśli masz klienta, który jest rutynowo składania żądań za pośrednictwem sas, a następnie możliwość wygaśnięcia wchodzi w grę. Kluczową kwestią jest zrównoważenie potrzeby krótkotrwałego (jak wcześniej wspomniano) z potrzebą zapewnienia, że klient żąda odnowienia wystarczająco wcześnie (aby uniknąć zakłóceń spowodowanych wygaśnięciem sygnatury dostępu Współdzielonego przed pomyślnym odnowieniem).
- **Należy zachować ostrożność przy czasie rozpoczęcia sas**: Jeśli ustawisz czas rozpoczęcia sygnatury dostępu Współdzielonego na **teraz,** a następnie ze względu na pochylenie zegara (różnice w bieżącym czasie w zależności od różnych maszyn), awarie mogą być obserwowane sporadycznie przez pierwsze kilka minut. Ogólnie ustaw czas rozpoczęcia na co najmniej 15 minut w przeszłości. Lub, nie ustawiaj go w ogóle, co sprawi, że będzie ważny natychmiast we wszystkich przypadkach. To samo dotyczy również czasu wygaśnięcia. Pamiętaj, że możesz na razie kierować do 15 minut zegara w dowolnym kierunku. 
- **Bądź specyficzny dla zasobu, do który ma zostać dostęp:** Najlepszym rozwiązaniem w zakresie zabezpieczeń jest zapewnienie użytkownikowi minimalnych wymaganych uprawnień. Jeśli użytkownik potrzebuje tylko dostępu do odczytu do jednej jednostki, a następnie udzielić mu dostępu do odczytu do tej pojedynczej jednostki, a nie odczytu/zapisu/usunięcia dostępu do wszystkich jednostek. Pomaga również zmniejszyć obrażenia, jeśli sas jest zagrożona, ponieważ SAS ma mniejszą moc w rękach atakującego.
- **Nie zawsze używaj sygnatury dostępu**Współdzielonego: czasami ryzyko związane z określoną operacją względem centrów zdarzeń przeważa nad korzyściami płynącymi z sygnatury dostępu Współdzielonego. W przypadku takich operacji utwórz usługę warstwy środkowej, która zapisuje w centrach zdarzeń po weryfikacji reguł biznesowych, uwierzytelnianiu i inspekcji.
- **Zawsze używaj HTTPs:** Zawsze używaj protokołu Https do tworzenia lub rozpowszechniania sygnatury dostępu Współdzielonego. Jeśli sygnatury dostępu Współdzielonego jest przekazywana przez HTTP i przechwycone, osoba atakująca wykonująca man-in-the-middle attach jest w stanie odczytać sygnatury dostępu Współdzielonego, a następnie użyć go tak, jak zamierzony użytkownik może mieć, potencjalnie narażając poufnych danych lub pozwalając na uszkodzenie danych przez złośliwego użytkownika.

## <a name="conclusion"></a>Podsumowanie
Udostępnianie podpisów dostępu są przydatne do dostarczania ograniczonych uprawnień do zasobów usługi Event Hubs dla klientów. Są one istotną częścią modelu zabezpieczeń dla dowolnej aplikacji korzystającej z usługi Azure Event Hubs. Jeśli zastosujesz się do najlepszych rozwiązań wymienionych w tym artykule, można użyć sygnatury dostępu Sygnatury dostępu, aby zapewnić większą elastyczność dostępu do zasobów, bez naruszania zabezpieczeń aplikacji.

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły pokrewne: 

- [Uwierzytelnij żądania do usługi Azure Event Hubs z aplikacji przy użyciu usługi Azure Active Directory](authenticate-application.md)
- [Uwierzytelnij tożsamość zarządzaną za pomocą usługi Azure Active Directory w celu uzyskania dostępu do zasobów usługi Event Hubs](authenticate-managed-identity.md)
- [Uwierzytelnij żądania w centrach zdarzeń platformy Azure przy użyciu podpisów dostępu współdzielonego](authenticate-shared-access-signature.md)
- [Autoryzowanie dostępu do zasobów usługi Event Hubs przy użyciu usługi Azure Active Directory](authorize-access-azure-active-directory.md)


