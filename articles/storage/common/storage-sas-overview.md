---
title: Udzielanie ograniczonego dostępu do danych za pomocą sygnatur dostępu współdzielonego (SAS)
titleSuffix: Azure Storage
description: Dowiedz się więcej o używaniu sygnatur dostępu współdzielonego (SAS) do delegowania dostępu do zasobów usługi Azure Storage, w tym obiektów blob, kolejek, tabel i plików.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 7a5967f52a187fe289c6fb1ca72af2d5fd17f010
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255238"
---
# <a name="grant-limited-access-to-azure-storage-resources-using-shared-access-signatures-sas"></a>Udzielanie ograniczonego dostępu do zasobów usługi Azure Storage przy użyciu sygnatur dostępu współdzielonego (SAS)

Sygnatura dostępu współdzielonego (SAS) zapewnia bezpieczny delegowany dostęp do zasobów na koncie magazynu bez naruszania bezpieczeństwa danych. Za pomocą sygnatury dostępu Współdzielonego masz szczegółową kontrolę nad tym, jak klient może uzyskać dostęp do danych. Można kontrolować, jakie zasoby klient może uzyskać dostęp, jakie uprawnienia mają do tych zasobów i jak długo sygnatury dostępu Współdzielonego jest prawidłowy, wśród innych parametrów.

## <a name="types-of-shared-access-signatures"></a>Typy sygnatur dostępu współdzielonego

Usługa Azure Storage obsługuje trzy typy podpisów dostępu współdzielonego:

- **Sygnatura dostępu Współdzielonego delegowania użytkowników.** Sygnatura dostępu Współdzielonego delegowania użytkowników jest zabezpieczona poświadczeniami usługi Azure Active Directory (Azure AD), a także uprawnieniami określonymi dla sygnatury dostępu Współdzielonego. Sygnatura dostępu Współdzielonego delegowania użytkowników dotyczy tylko magazynu obiektów Blob.

    Aby uzyskać więcej informacji na temat sygnatury dostępu Współdzielonego delegowania użytkowników, zobacz [Tworzenie sygnatury dostępu Współdzielonego (REST API) delegowania użytkowników](/rest/api/storageservices/create-user-delegation-sas).

- **Usługa SAS.** Sygnatura dostępu Współdzielonego usługi jest zabezpieczona kluczem konta magazynu. Usługa Sygnatury dostępu do zasobów jest delegowana tylko w jednej z usług usługi Azure Storage: magazyn obiektów Blob, magazyn kolejek, magazyn tabel lub usługi Azure Files. 

    Aby uzyskać więcej informacji na temat sygnatury dostępu Współdzielonego usługi, zobacz [Tworzenie usługi SAS (REST API)](/rest/api/storageservices/create-service-sas).

- **Sygnatura dostępu Współdzielonego konta** Sygnatura dostępu Współdzielonego konta jest zabezpieczona kluczem konta magazynu. Sygnatura dostępu współdzielonego konta deleguje dostęp do zasobu w co najmniej jednej usłudze magazynu. Wszystkie operacje dostępne za pośrednictwem usługi lub delegowania użytkownika Sygnatury dostępu Współdzielonego są również dostępne za pośrednictwem sygnatury dostępu Współdzielonego konta. Ponadto za pomocą sygnatury dostępu współdzielonego konta można delegować dostęp do operacji, które mają zastosowanie na poziomie usługi, takich jak **Właściwości usługi Get/Set** i Pobierz statystyki **usług.** Możesz również delegować dostęp do operacji odczytu, zapisu i usuwania dla kontenerów obiektów blob, tabel, kolejek i udziałów plików, co jest niedozwolone w wypadku sygnatury dostępu współdzielonego usługi. 

    Aby uzyskać więcej informacji na temat sygnatury dostępu Współdzielonego [konta, utwórz sygnaturę dostępu Współdzielonego konta (REST API)](/rest/api/storageservices/create-account-sas).

> [!NOTE]
> Firma Microsoft zaleca używanie poświadczeń usługi Azure AD, jeśli to możliwe, jako najlepszego rozwiązania w zakresie zabezpieczeń, zamiast używania klucza konta, który można łatwiej zainfekować. Gdy projekt aplikacji wymaga wspólnych podpisów dostępu do dostępu do magazynu obiektów Blob, użyj poświadczeń usługi Azure AD, aby utworzyć sygnaturę współdzieloną delegowania użytkowników, jeśli jest to możliwe, aby uzyskać najwyższej jakości zabezpieczeń.

Podpis dostępu współdzielonego może przybierać jedną z dwóch form:

- **SYK ad hoc:** Podczas tworzenia sygnatury dostępu Współdzielonego ad hoc czas rozpoczęcia, czas wygaśnięcia i uprawnienia dla sygnatury dostępu Współdzielonego są określone w identyfikatorze URI sygnatury dostępu Współdzielonego (lub dorozumianym, jeśli godzina rozpoczęcia zostanie pominięta). Każdy typ sygnatury dostępu Współdzielonego może być sygnaturą dostępu Współdzielonego ad hoc.
- **Sygnatura dostępu współdzielonego usługi z zasadami dostępu przechowywanego:** Zasady dostępu przechowywane jest zdefiniowany w kontenerze zasobów, który może być kontenera obiektów blob, tabeli, kolejki lub udziału plików. Przechowywane zasady dostępu mogą służyć do zarządzania ograniczeniami dla co najmniej jednego podpisu dostępu współdzielonego usługi. Po skojarzeniu usługi Sygnatura dostępu Współdzielonego&mdash;z zasadami dostępu przechowywanego&mdash;sygnatura dostępu współdzielonego dziedziczy ograniczenia czasu rozpoczęcia, czasu wygaśnięcia i uprawnień zdefiniowanych dla zasad dostępu przechowywanego.

> [!NOTE]
> Sygnatura dostępu Współdzielonego delegowania użytkowników lub sygnatury dostępu Współdzielonego konta musi być sygnaturą dostępu Współdzielonego ad hoc. Przechowywane zasady dostępu nie są obsługiwane dla sygnatury dostępu współdzielonego delegowania użytkowników ani sygnatury dostępu współdzielonego na koncie.

## <a name="how-a-shared-access-signature-works"></a>Jak działa podpis dostępu współdzielonego

Podpis dostępu współdzielonego jest podpisanym identyfikatorem URI, który wskazuje jeden lub więcej zasobów magazynu i zawiera token zawierający specjalny zestaw parametrów kwerendy. Token wskazuje, jak zasoby mogą być dostępne przez klienta. Jeden z parametrów kwerendy, podpis, jest konstruowany z parametrów sygnatury dostępu Współdzielonego i podpisany przy użyciu klucza, który został użyty do utworzenia sygnatury dostępu Współdzielonego. Ten podpis jest używany przez usługę Azure Storage do autoryzowania dostępu do zasobu magazynu.

### <a name="sas-signature"></a>Podpis SAS

Sygnatura dostępu Współdzielonego można podpisać na jeden z dwóch sposobów:

- Przy *użyciu klucza delegowania użytkowników,* który został utworzony przy użyciu poświadczeń usługi Azure Active Directory (Azure AD). Sygnatura dostępu Współdzielonego delegowania użytkowników jest podpisana przy za pomocą klucza delegowania użytkowników.

    Aby uzyskać klucz delegowania użytkownika i utworzyć sygnatury dostępu Współdzielonego, podmiot zabezpieczeń usługi Azure AD musi mieć przypisaną rolę kontroli dostępu opartej na rolach (RBAC) opartą na rolach, która obejmuje akcję **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey.** Aby uzyskać szczegółowe informacje o rolach RBAC z uprawnieniami do uzyskania klucza delegowania użytkowników, zobacz [Tworzenie sygnatury dostępu Współdzielonego delegowania użytkowników (REST API).](/rest/api/storageservices/create-user-delegation-sas)

- Z kluczem konta magazynu. Zarówno sygnatury dostępu Współdzielonego usługi, jak i sygnatury dostępu Współdzielonego konta są podpisane przy za pomocą klucza konta magazynu. Aby utworzyć sygnatury dostępu Współdzielonego podpisanego za pomocą klucza konta, aplikacja musi mieć dostęp do klucza konta.

### <a name="sas-token"></a>Token SAS

Token sygnatury dostępu Współdzielonego jest ciągiem generowanym po stronie klienta, na przykład przy użyciu jednej z bibliotek klienta usługi Azure Storage. Token sygnatury dostępu Współdzielonego nie jest śledzony przez usługę Azure Storage w żaden sposób. Można utworzyć nieograniczoną liczbę tokenów sygnatury dostępu Współdzielonego po stronie klienta. Po utworzeniu sygnatury dostępu Współdzielonego można dystrybuować go do aplikacji klienckich, które wymagają dostępu do zasobów na koncie magazynu.

Gdy aplikacja kliencka udostępnia identyfikator URI sygnatury dostępu Współdzielonego do usługi Azure Storage w ramach żądania, usługa sprawdza parametry sygnatury dostępu Współdzielonego i podpis, aby sprawdzić, czy jest prawidłowy do autoryzowania żądania. Jeśli usługa sprawdzi, czy podpis jest prawidłowy, żądanie jest autoryzowane. W przeciwnym razie żądanie zostanie odrzucone z kodem błędu 403 (Zabronione).

Oto przykład identyfikatora URI sygnatury dostępu Współdzielonego usługi, przedstawiający identyfikator URI zasobu i token sygnatury dostępu Współdzielonego dostępu Współdzielonego:

![Składniki identyfikatora URI usługi SAS](./media/storage-sas-overview/sas-storage-uri.png)

## <a name="when-to-use-a-shared-access-signature"></a>Kiedy używać podpisu dostępu współdzielonego

Użyj sygnatury dostępu Współdzielonego, jeśli chcesz zapewnić bezpieczny dostęp do zasobów na koncie magazynu każdemu klientowi, który w przeciwnym razie nie ma uprawnień do tych zasobów.

Typowy scenariusz, w którym sygnatury dostępu Współdzielonego jest przydatna jest usługa, w której użytkownicy odczytują i zapisują własne dane na koncie magazynu. W scenariuszu, w którym dane użytkowników są przechowywane na koncie magazynu, zwykle występują dwa wzorce projektowe:

1. Klienci przekazują i pobierają dane za pośrednictwem usługi serwera proxy frontonu, która przeprowadza uwierzytelnianie. Ta usługa serwera proxy front-end ma tę zaletę, że umożliwia sprawdzanie poprawności reguł biznesowych, ale w przypadku dużych ilości danych lub transakcji dużej liczby, tworzenie usługi, która może być skalowana w celu dopasowania popytu, może być kosztowne lub trudne.

   ![Diagram scenariusza: usługa proxy frontowego](./media/storage-sas-overview/sas-storage-fe-proxy-service.png)

1. Uproszczona usługa uwierzytelnia klienta zgodnie z potrzebami, a następnie generuje sygnaturę dostępu współdzielonego. Gdy aplikacja kliencka odbiera sygnatury dostępu Współdzielonego, mogą uzyskać dostęp do zasobów konta magazynu bezpośrednio z uprawnieniami zdefiniowanymi przez sygnaturę dostępu Współdzielonego i interwału dozwolonego przez sygnaturę dostępu Współdzielonego. Sygnatura dostępu współdzielonego zmniejsza konieczność kierowania wszystkich danych przez usługę serwera proxy frontonu.

   ![Diagram scenariusza: Usługa dostawcy sygnatury dostępu Współdzielonego](./media/storage-sas-overview/sas-storage-provider-service.png)

Wiele rzeczywistych usług może używać hybrydy tych dwóch podejść. Na przykład niektóre dane mogą być przetwarzane i weryfikowane za pośrednictwem serwera proxy frontonu, podczas gdy inne dane są zapisywane i/lub odczytywane bezpośrednio przy użyciu sygnatury dostępu Współdzielonego.

Ponadto sygnatury dostępu Współdzielonego jest wymagane do autoryzowania dostępu do obiektu źródłowego w operacji kopiowania w niektórych scenariuszach:

- Podczas kopiowania obiektu blob do innego obiektu blob, który znajduje się na innym koncie magazynu, należy użyć sygnatury dostępu współdzielonego do autoryzowania dostępu do źródłowego obiektu blob. Opcjonalnie można użyć sygnatury dostępu Współdzielonego do autoryzowania dostępu do docelowego obiektu blob.
- Podczas kopiowania pliku do innego pliku, który znajduje się na innym koncie magazynu, należy użyć sygnatury dostępu współdzielonego do autoryzacji dostępu do pliku źródłowego. Opcjonalnie można użyć sygnatury dostępu Współdzielonego, aby autoryzować dostęp do pliku docelowego.
- Podczas kopiowania obiektu blob do pliku lub pliku do obiektu blob, należy użyć sygnatury dostępu współdzielonego do autoryzowania dostępu do obiektu źródłowego, nawet jeśli obiekty źródłowe i docelowe znajdują się w ramach tego samego konta magazynu.

## <a name="best-practices-when-using-sas"></a>Najlepsze rozwiązania dotyczące korzystania z sygnatury dostępu współdzielonego

Korzystając z podpisów dostępu współdzielonego w aplikacjach, musisz mieć świadomość dwóch potencjalnych zagrożeń:

- Jeśli sygnatury dostępu Współdzielonego jest przecieka, może być używany przez każdego, kto go uzyskuje, co może potencjalnie naruszyć twoje konto magazynu.
- Jeśli sygnatury dostępu Współdzielonego dostarczonego do aplikacji klienckiej wygaśnie, a aplikacja nie może pobrać nowego sygnatury dostępu Współdzielonego z usługi, funkcjonalność aplikacji może być utrudniona.

Następujące zalecenia dotyczące korzystania z podpisów dostępu współdzielonego mogą pomóc w ograniczeniu tych zagrożeń:

- **Zawsze używaj protokołu HTTPS** do tworzenia lub rozpowszechniania sygnatury dostępu Współdzielonego. Jeśli sygnatury dostępu Współdzielonego jest przekazywana przez HTTP i przechwycone, osoba atakująca wykonująca atak typu man-in-the-middle jest w stanie odczytać sygnaturę dostępu Współdzielonego, a następnie użyć go tak, jak zamierzony użytkownik może mieć, potencjalnie narażając poufne dane lub umożliwiając uszkodzenie danych przez złośliwego użytkownika.
- **Użyj delegowania użytkownika Sygnatura dostępu Współdzielonego, jeśli to możliwe.** Sygnatura dostępu Współdzielonego delegowania użytkowników zapewnia doskonałe zabezpieczenia usługi Sygnatury dostępu Współdzielonego lub sygnatury dostępu Współdzielonego konta. Sygnatura dostępu Współdzielonego delegowania użytkowników jest zabezpieczona poświadczeniami usługi Azure AD, dzięki czemu nie trzeba przechowywać klucza konta przy użyciu kodu.
- **Mieć plan odwołania w miejscu dla sygnatury dostępu Współdzielonego.** Upewnij się, że jesteś przygotowany do odpowiedzi, jeśli sygnatury dostępu Współdzielonego jest zagrożona.
- **Zdefiniuj zasadę dostępu przechowywanego dla usługi Sygnatura dostępu Współdzielonego.** Zasady dostępu przechowywane umożliwiają odwołanie uprawnień do usługi Sygnatury dostępu Współdzielonego bez konieczności ponownego generowania kluczy konta magazynu. Ustaw wygaśnięcie na nich bardzo daleko w przyszłości (lub nieskończone) i upewnij się, że jest regularnie aktualizowana, aby przenieść go dalej w przyszłość.
- **Użyj krótkoterminowych czasów wygaśnięcia w usłudze ad hoc SAS usługi sygnatury dostępu Współdzielonego lub sygnatury dostępu Współdzielonego konta.** W ten sposób, nawet jeśli sygnatury dostępu Współdzielonego jest zagrożona, jest prawidłowy tylko przez krótki czas. Ta praktyka jest szczególnie ważna, jeśli nie można odwoływać się do zasad dostępu przechowywane. W najbliższym czasie czas wygaśnięcia również ograniczyć ilość danych, które mogą być zapisywane do obiektu blob, ograniczając czas dostępny do przekazania do niego.
- **W razie potrzeby klienci automatycznie odnowić sygnaturę dostępu Współdzielonego.** Klienci powinni odnowić sygnatury dostępu Współdzielonego na długo przed wygaśnięciem, aby dać czas na ponownych prób, jeśli usługa zapewniająca sygnatury dostępu Współdzielonego jest niedostępna. Jeśli sygnatury dostępu Współdzielonego jest przeznaczony do użycia dla niewielkiej liczby natychmiastowych, krótkotrwałych operacji, które mają zostać zakończone w okresie wygaśnięcia, może to być niepotrzebne, ponieważ nie oczekuje się, że sygnatury dostępu Współdzielonego nie zostanie odnowiony. Jednak jeśli masz klienta, który jest rutynowo składania żądań za pośrednictwem sas, a następnie możliwość wygaśnięcia wchodzi w grę. Kluczową kwestią jest zrównoważenie potrzeby krótkotrwałego (jak wcześniej wspomniano) z potrzebą zapewnienia, że klient żąda odnowienia wystarczająco wcześnie (aby uniknąć zakłóceń spowodowanych wygaśnięciem sygnatury dostępu Współdzielonego przed pomyślnym odnowieniem).
- **Należy zachować ostrożność przy godzinie rozpoczęcia sas.** Jeśli ustawisz czas rozpoczęcia sygnatury dostępu Współdzielonego na **teraz**, a następnie ze względu na pochylenie zegara (różnice w bieżącym czasie w zależności od różnych maszyn), awarie mogą być obserwowane sporadycznie przez pierwsze kilka minut. Ogólnie ustaw czas rozpoczęcia na co najmniej 15 minut w przeszłości. Lub, nie ustawiaj go w ogóle, co sprawi, że będzie ważny natychmiast we wszystkich przypadkach. To samo dotyczy zwykle czasu wygaśnięcia, jak również — należy pamiętać, że można zaobserwować do 15 minut pochylenia zegara w obu kierunkach na każde żądanie. Dla klientów korzystających z wersji REST przed 2012-02-12 maksymalny czas trwania sygnatury dostępu Współdzielonego, który nie odwołuje się do zasad dostępu przechowywane jest 1 godzina, a wszelkie zasady określające dłuższy okres niż to zakończy się niepowodzeniem.
- **Należy zachować ostrożność w formacie datatime sygnatury dostępu Współdzielonego.** Jeśli ustawisz godzinę rozpoczęcia i/lub wygaśnięcia sygnatury dostępu Współdzielonego, dla niektórych narzędzi (na przykład dla narzędzia wiersza polecenia AzCopy) potrzebny jest format datetime na "+%Y-%m-%dT%H:%M:%SZ", w szczególności w tym sekundy, aby działały przy użyciu tokenu Sygnatury dostępu Współdzielonego.  
- **Należy specyficzna dla zasobu, do który ma zostać dostęp.** Najlepszym rozwiązaniem w zakresie zabezpieczeń jest zapewnienie użytkownikowi minimalnych wymaganych uprawnień. Jeśli użytkownik potrzebuje tylko dostępu do odczytu do jednej jednostki, a następnie udzielić mu dostępu do odczytu do tej pojedynczej jednostki, a nie odczytu/zapisu/usunięcia dostępu do wszystkich jednostek. Pomaga to również zmniejszyć uszkodzenia, jeśli sygnatura dostępu Współdzielonego jest zagrożona, ponieważ sas ma mniejszą moc w rękach atakującego.
- **Zrozum, że Twoje konto zostanie naliczone za każde użycie, w tym za pośrednictwem sygnatury dostępu Współdzielonego.** Jeśli zapewnisz dostęp do zapisu do obiektu blob, użytkownik może zdecydować się na przekazanie obiektu blob 200 GB. Jeśli dałeś im dostęp do odczytu, mogą pobrać go 10 razy, ponosząc 2 TB kosztów wyjścia dla Ciebie. Ponownie podaj ograniczone uprawnienia, aby pomóc w ograniczeniu potencjalnych działań złośliwych użytkowników. Użyj krótkotrwałego SYGNATURY DOSTĘPU, aby zmniejszyć to zagrożenie (ale należy pamiętać o pochyleniu zegara w czasie końcowym).
- **Sprawdzanie poprawności danych zapisanych przy użyciu sygnatury dostępu Współdzielonego.** Gdy aplikacja kliencka zapisuje dane na koncie magazynu, należy pamiętać, że mogą wystąpić problemy z tymi danymi. Jeśli aplikacja wymaga, aby dane zostały sprawdzone lub autoryzowane, zanim będzie gotowe do użycia, należy wykonać tę weryfikację po zapisaniu danych i przed ich użyciem przez aplikację. Praktyka ta chroni również przed uszkodzonymi lub złośliwymi danymi zapisywanych na koncie użytkownika, albo przez użytkownika, który prawidłowo nabył sygnaturę dostępu Współdzielonego, albo przez użytkownika korzystającego z przeciekającego sygnatury dostępu Współdzielonego.
- **Wiedzieć, kiedy nie używać sygnatury dostępu Współdzielonego.** Czasami ryzyko związane z określoną operacją względem konta magazynu przeważa nad korzyściami płynącymi z używania sygnatury dostępu Współdzielonego. W przypadku takich operacji utwórz usługę warstwy środkowej, która zapisuje na koncie magazynu po wykonaniu sprawdzania poprawności reguły biznesowej, uwierzytelniania i inspekcji. Ponadto czasami łatwiej jest zarządzać dostępem w inny sposób. Na przykład jeśli chcesz, aby wszystkie obiekty BLOB w kontenerze były publicznie czytelne, można udostępnić kontenera public, zamiast udostępniać sygnatury dostępu współdzielonego dla każdego klienta w celu uzyskania dostępu.
- **Użyj dziennika usługi Azure Monitor i Usługi Azure Storage, aby monitorować aplikację.** Za pomocą usługi Azure Monitor i rejestrowania analizy magazynu można zaobserwować wszelkie skoki błędów autoryzacji z powodu awarii w usłudze dostawcy sygnatury dostępu współdzielonego lub nieumyślnego usunięcia zasad dostępu przechowywanego. Aby uzyskać więcej informacji, zobacz [metryki usługi Azure Storage w usłudze Azure Monitor](storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) i [rejestrowaniu usługi Azure Storage Analytics.](storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="get-started-with-sas"></a>Wprowadzenie do sas

Aby rozpocząć korzystanie z podpisów dostępu współdzielonego, zobacz następujące artykuły dla każdego typu sygnatury dostępu Współdzielonego.

### <a name="user-delegation-sas"></a>Sygnatura dostępu współdzielonego delegowan

- [Tworzenie sygnatury dostępu Współdzielonego delegowania użytkowników dla kontenera lub obiektu blob za pomocą programu PowerShell](../blobs/storage-blob-user-delegation-sas-create-powershell.md)
- [Tworzenie sygnatury dostępu Współdzielonego delegowania użytkowników dla kontenera lub obiektu blob za pomocą interfejsu wiersza polecenia platformy Azure](../blobs/storage-blob-user-delegation-sas-create-cli.md)
- [Tworzenie sygnatury dostępu Współdzielonego delegowania użytkowników dla kontenera lub obiektu blob za pomocą platformy .NET](../blobs/storage-blob-user-delegation-sas-create-dotnet.md)

### <a name="service-sas"></a>Usługa SAS

- [Tworzenie usługi Sygnatury dostępu Współdzielonego dla kontenera lub obiektu blob za pomocą platformy .NET](../blobs/storage-blob-service-sas-create-dotnet.md)

### <a name="account-sas"></a>Sygnatura dostępu współdziel

- [Tworzenie sygnatury dostępu Współdzielonego konta z siecią .NET](storage-account-sas-create-dotnet.md)

## <a name="next-steps"></a>Następne kroki

- [Delegowanie dostępu za pomocą udostępnionego podpisu dostępu (REST API)](/rest/api/storageservices/delegate-access-with-shared-access-signature)
- [Tworzenie sygnatury dostępu Współdzielonego delegowania użytkowników (INTERFEJS API REST)](/rest/api/storageservices/create-user-delegation-sas)
- [Tworzenie usługi SAS (REST API)](/rest/api/storageservices/create-service-sas)
- [Tworzenie sygnatury dostępu Współdzielonego konta (INTERFEJSU API REST)](/rest/api/storageservices/create-account-sas)
