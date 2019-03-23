---
title: Przewodnik po zabezpieczeniach usługi Data Lake Storage Gen2 Storage platformy Azure | Dokumentacja firmy Microsoft
description: Szczegóły wiele metod zabezpieczania usługi Azure Storage, w tym między innymi RBAC i szyfrowanie usługi Storage
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: ce01301455c7abcd26006e622fcfbb8127e1c511
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372492"
---
# <a name="azure-data-lake-storage-gen2-security-guide"></a>Przewodnik po zabezpieczeniach usługi Azure Data Lake Storage Gen2

Azure Data Lake magazynu Gen2 —, to zestaw funkcji wbudowanych na kontach usługi Azure Storage. W efekcie wszelkie odwołania, w tym artykule są przeznaczone dla konta usługi Azure Storage za pomocą hierarchicznej przestrzeni nazw włączone (Data Lake Storage Gen2 możliwości).

- Wszystkie dane zapisane w usłudze Azure Storage są automatycznie szyfrowane przy użyciu [szyfrowanie usługi Storage (SSE)](storage-service-encryption.md). Aby uzyskać więcej informacji, zobacz [ogłoszenie domyślne szyfrowanie obiektów blob platformy Azure, pliki, tabele i Queue Storage](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).
- Azure Active Directory (Azure AD) i kontrola dostępu oparta na rolach (RBAC) są obsługiwane dla usługi Azure Storage zarówno dla zasobów operacje zarządzania i operacje na danych, w następujący sposób:
    - Można przypisać role RBAC ograniczone do konta magazynu do podmiotów zabezpieczeń i użyj usługi Azure AD, aby autoryzować operacji zarządzania zasobów, takich jak zarządzanie kluczami.
    - Integracja z usługą Azure AD jest obsługiwany w operacji danych w usłudze Azure Storage. Można przypisać role RBAC ograniczone do subskrypcji, grupy zasobów, konto magazynu lub poszczególnych plików do podmiotu zabezpieczeń lub tożsamość zarządzaną dla zasobów platformy Azure. Aby uzyskać więcej informacji, zobacz [uwierzytelniania dostępu do usługi Azure Storage przy użyciu usługi Azure Active Directory](storage-auth-aad.md).
- Delegowanego dostępu do obiektów danych w usłudze Azure Storage można przyznać za pomocą [sygnatur dostępu współdzielonego](../storage-dotnet-shared-access-signature-part-1.md).

Ten artykuł zawiera omówienie każdego z tych funkcji zabezpieczeń, które mogą być używane z usługi Azure Storage. Łącza są dostarczane do artykułów, które będzie podać szczegóły dotyczące każdej funkcji to łatwo zrobić dalsze badanie dotyczące każdego tematu.

Tematy, które mają być uwzględnione w tym artykule są następujące:

* [Zabezpieczenia płaszczyzny zarządzania](#management-plane-security) — zabezpieczanie konta magazynu

  Płaszczyzna zarządzania składa się z zasobami umożliwiają zarządzanie kontem magazynu. W tej sekcji omówiono modelu wdrażania usługi Azure Resource Manager i sposobu używania kontroli dostępu opartej na rolach (RBAC) do kontrolowania dostępu do konta magazynu. Zajmuje się także, zarządzanie klucze konta magazynu i jak można wygenerować je ponownie.
* [Zabezpieczenia na płaszczyźnie danych](#data-plane-security) — zabezpieczanie dostępu do danych

  W tej sekcji omówimy zezwalania na dostęp do danych rzeczywistych obiektów na koncie magazynu, np. plików i katalogów, za pomocą sygnatur dostępu współdzielonego i przechowywanych zasad dostępu. Omówimy zarówno sygnatura SAS na poziomie usługi, jak i interfejsie SAS na poziomie konta. Również zobaczymy, jak ograniczyć dostęp do określonego adresu IP (lub zakres adresów IP), jak ograniczyć protokół HTTPS i sposób odwołać sygnaturę dostępu współdzielonego nie trzeba czekać na jego wygaśnięcia.
* [Szyfrowanie podczas transferu](#encryption-in-transit)

W tej sekcji omówiono sposób zabezpieczania danych w przypadku przeniesienia jej do lub z kontem magazynu za pomocą magazynu Gen2 jeziora danych, włączone. Omówimy zalecane użycie protokołu HTTPS.

## <a name="management-plane-security"></a>Zabezpieczenia płaszczyzny zarządzania

Płaszczyzna zarządzania składa się z działań, które wpływają na konto magazynu. Na przykład można utworzyć lub usunąć konto magazynu, uzyskać listę kont magazynu w ramach subskrypcji, pobrać klucze konta magazynu lub ponowne generowanie kluczy konta magazynu.

Ten przewodnik koncentruje się na modelu usługi Resource Manager, wdrażania, czyli sposób tworzenia kont magazynu dzięki możliwościom Data Lake Storage Gen2. Z kontami magazynu usługi Resource Manager, a nie dające dostęp do całej subskrypcji możesz kontrolować dostęp na poziomie bardziej ograniczone do płaszczyzny zarządzania za pomocą kontroli dostępu opartej na rolach (RBAC).

### <a name="how-to-secure-your-storage-account-with-role-based-access-control-rbac"></a>Jak zabezpieczyć swoje konto magazynu przy użyciu kontroli dostępu opartej na rolach (RBAC)

Poniżej omówiono RBAC jest i jak można jej używać. Każda subskrypcja platformy Azure zawiera usługę Azure Active Directory. Użytkownikom, grupom i aplikacjom z tego katalogu można udzielić dostępu do zarządzania zasobami w subskrypcji platformy Azure, które używają modelu wdrażania usługi Resource Manager. Ten typ zabezpieczeń jest określany jako kontrola dostępu oparta na rolach (RBAC). Aby zarządzać dostępem, należy użyć **kontrola dostępu (IAM)** w witrynie Azure portal.

Za pomocą modelu usługi Resource Manager należy umieścić na koncie magazynu w zasobów grupy i kontroli dostępu do płaszczyzny zarządzania tym konkretnym kontem magazynu za pomocą usługi Azure Active Directory. Na przykład można udzielić określonym użytkownikom możliwość dostępu do kluczy konta magazynu, podczas gdy inni użytkownicy mogą wyświetlać informacje o koncie magazynu, ale nie dostęp do kluczy konta magazynu.

#### <a name="granting-access"></a>Udzielanie dostępu

Dostęp jest udzielany, przypisując odpowiednie role RBAC do użytkowników, grup i aplikacji w zakresie prawo. Aby udzielić dostępu do całej subskrypcji, możesz przypisać rolę na poziomie subskrypcji. Możesz udzielić dostępu do wszystkich zasobów w grupie zasobów, przyznając uprawnienia do samej grupy zasobów. Można także przypisać określonych ról, do określonych zasobów, takich jak konta magazynu.

Poniżej przedstawiono główne punkty, które musisz wiedzieć o otwieranie operacje zarządzania kontem usługi Azure Storage za pomocą funkcji RBAC:

* Osoba, która ma uprawnienia do dostępu do obiektów danych na koncie magazynu można nadać im uprawnienia do odczytu klucze konta magazynu, a ten użytkownik mógł następnie użyć tych kluczy dostępu do plików i katalogów.
* Role można przypisywać do określonego konta użytkownika, grupy użytkowników lub do określonej aplikacji.
* Każda rola zawiera listę działań i nie akcje. Na przykład rola Współautor maszyny wirtualnej ma akcję "listKeys" umożliwiająca kluczy konta magazynu do odczytu. Współautor ma "Nie akcje", takie jak aktualizowanie dostępu dla użytkowników w usłudze Active Directory.
* Role dla magazynu obejmują (ale nie są ograniczone do) następujących ról:

  * Właściciel — może on zarządzać wszystkim łącznie z dostępem.
  * Współautor — może robią niczego właściciela oprócz przypisywanie dostępu. Ktoś z tą rolą można przeglądać i ponowne generowanie kluczy konta magazynu. Za pomocą kluczy konta magazynu ich dostęp do obiektów danych.
  * Czytelnik — mogą wyświetlać informacje o koncie magazynu, z wyjątkiem wpisów tajnych. Na przykład w przypadku przypisania roli z uprawnieniami czytelnika na koncie magazynu w innej, mogą wyświetlać właściwości konta magazynu, ale nie mogą wprowadzać żadnych zmian właściwości lub Wyświetl klucze konta magazynu.
  * Współautor konta magazynu — może on zarządzać konto magazynu — może odczytywać informacje o subskrypcji grupy zasobów i zasoby, tworzenie i zarządzanie wdrożeniami grup zasobów w subskrypcji. Mogą również dostęp do kluczy konta magazynu, co z kolei oznacza, że mogą uzyskiwać dostęp do płaszczyzny danych.
  * Administrator dostępu użytkowników — one zarządzanie dostępem użytkowników do konta magazynu. Na przykład powodują udzielenie dostępu czytnik dla określonego użytkownika.
  * Współautor maszyny wirtualnej — może on zarządzać maszynami wirtualnymi, ale nie na koncie magazynu, z którym są połączone. Tę rolę można wyświetlić listę kluczy konta magazynu, co oznacza, że użytkownik, do którego ta rola została przypisana zaktualizować płaszczyzny danych.

    Użytkownika, aby utworzyć maszynę wirtualną, muszą mieć możliwość utworzenia odpowiedniego pliku VHD na koncie magazynu. Aby to zrobić, muszą mieć możliwość pobierania klucza konta magazynu i przekazywać je do interfejsu API tworzenia maszyny Wirtualnej. W związku z tym dzięki czemu można wyświetlić listę kluczy konta magazynu muszą mieć to uprawnienie.
* Możliwość definiowania ról niestandardowych to funkcja umożliwiająca tworzą zestaw akcji z listy dostępnych akcji, które mogą być wykonywane dla zasobów platformy Azure.
* Użytkownik musi skonfigurować w usłudze Azure Active Directory, zanim będzie można przypisać roli do nich.
* Można utworzyć raport o który przyznane/odwołany jakiego rodzaju dostępu do i z którego i na jakie zakresu, przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

#### <a name="resources"></a>Zasoby

* [Kontrola dostępu oparta na rolach w usłudze Azure Active Directory](../../role-based-access-control/role-assignments-portal.md)

  W tym artykule objaśniono funkcję kontroli dostępu opartej na rolach w usłudze Azure Active Directory i sposób jej działania.
* [RBAC: Wbudowane role](../../role-based-access-control/built-in-roles.md)

  Ten artykuł szczegółowo wszystkie wbudowane Role dostępne w RBAC.
* [Omówienie wdrażania przy użyciu usługi Resource Manager oraz wdrażania klasycznego](../../azure-resource-manager/resource-manager-deployment-model.md)

### <a name="managing-your-storage-account-keys"></a>Zarządzanie kluczami konta magazynu

Klucze konta magazynu są ciągami 512-bitowy, utworzone przez platformę Azure, która wraz z nazwą konta magazynu, mogą być używane do dostępu do obiektów danych przechowywanych na koncie magazynu, na przykład, obiekty BLOB, jednostki przechowywane w tabeli, kolejki komunikatów i plików w udziale plików platformy Azure. Kontrolowanie dostępu do kluczy kontroli dostępu do konta magazynu do płaszczyzny danych dla tego konta magazynu.

Każde konto magazynu ma dwa klucze, określane jako "Klucz 1" i "Klucz 2" w [witryny Azure portal](https://portal.azure.com/) w poleceniach cmdlet programu PowerShell. Te mogą zostać wygenerowane ponownie ręcznie przy użyciu jednej z kilku metod, w tym między innymi za pomocą [witryny Azure portal](https://portal.azure.com/), PowerShell, interfejsu wiersza polecenia platformy Azure lub programowo przy użyciu biblioteki klienta .NET magazynu lub usług Azure Storage INTERFEJS API REST.

Istnieje wiele powodów, aby ponownie wygenerować kluczy konta magazynu.

* Użytkownik może ponownie wygenerować je na bieżąco ze względów bezpieczeństwa.
* Jeśli ktoś zarządzane hack do aplikacji i pobierania klucza, który zostało zapisane na stałe lub zapisać w pliku konfiguracji, zapewniając im pełny dostęp do swojego konta magazynu będzie ponownie wygenerować kluczy konta magazynu.
* W innym przypadku ponownego wygenerowania klucza jest, jeśli Twój zespół używa aplikacji Eksploratora usługi Storage, która przechowuje klucz konta magazynu i jeden z członków zespołu pozostawia. Aplikacja będzie nadal działać, dając im dostęp do swojego konta magazynu po zrobi to ktoś inny. To jest faktycznie głównym powodem, utworzonych przez siebie sygnatur dostępu współdzielonego na poziomie konta — sygnatury dostępu Współdzielonego na poziomie konta można użyć zamiast przechowywać klucze dostępu w pliku konfiguracji.

#### <a name="key-regeneration-plan"></a>Ponowne generowanie klucza planu

Nie chcesz po prostu ponownie wygenerować klucz używane bez niektóre planowania. Jeśli to zrobisz, może odcięte wszelki dostęp do tego konta magazynu i może spowodować przestoje głównych. Jest to, dlaczego dostępne są dwa klucze. Należy ponownie wygenerować jeden klucz w danym momencie.

Zanim można ponownie wygenerować klucze, upewnij się, że masz listę wszystkich aplikacji, które są zależne od konta magazynu, a także innych usług, których używasz na platformie Azure. Na przykład jeśli używasz usługi Azure Media Services, które są zależne od konta magazynu musi resync klawiszy dostępu za pomocą usługi media Services po ponownym wygenerowaniu klucza. Jeśli używasz dowolnej aplikacji, takich jak Eksplorator magazynu konieczne będzie udostępnia nowe klucze do tych aplikacji, jak również. Jeśli masz maszyny wirtualne, których pliki VHD są przechowywane na koncie magazynu, nie będzie ich wpływu przez ponowne generowanie kluczy konta magazynu.

Można ponownie wygenerować klucze w witrynie Azure portal. Po klucze są generowane, ich może potrwać do 10 minut mają być synchronizowane między usługami magazynu.

Gdy wszystko będzie gotowe, Oto ogólny proces szczegółowych informacji na temat, jak należy zmienić klucz. W tym przypadku przy założeniu jest, że aktualnie używasz klucz 1 i zamierzasz zmienić wszystko, aby zamiast tego użyj klucz 2.

1. Wygeneruj ponownie klucz 2, aby upewnić się, że jest bezpieczne. Można to zrobić w witrynie Azure portal.
2. We wszystkich aplikacjach miejsce przechowywania klucza magazynu należy zmienić klucz magazynu, aby użyć wartości nowy klucz 2. Testowanie i publikowanie aplikacji.
3. Po wszystkich aplikacji i usług są włączone i uruchomione pomyślnie, Wygeneruj ponownie klucz 1. Zapewnia to, że nie będzie miało każda osoba, której nie zostały wyraźnie określone mają nowy klucz dostępu do konta magazynu.

Jeśli obecnie używasz klucza 2, użyj tego samego procesu, ale odwrotnego nazwy kluczy.

Można przeprowadzić migrację przez kilka dni, zmiana każdej aplikacji w celu używania nowego klucza i publikując ją. Po wykonaniu wszystkich tych czynności należy wrócić i ponowne generowanie klucza stare, więc nie będzie działać.

Innym rozwiązaniem jest umieszczenie klucza konta magazynu w [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) jako klucz tajny i mieć aplikacji pobrać klucza z tego miejsca. Następnie gdy ponowne wygenerowanie klucza i aktualizacji usługi Azure Key Vault, aplikacje nie należy do ponownego wdrożenia, ponieważ ich przejmie nowy klucz w usłudze Azure Key Vault automatycznie. Może mieć aplikacja będzie odczytywać klucza każdorazowo, będą potrzebne lub można buforowanie w pamięci i jeśli zakończy się niepowodzeniem w przypadku korzystania z niego, klucz ponownego pobrania z usługi Azure Key Vault.

Również przy użyciu usługi Azure Key Vault dodaje kolejny poziom zabezpieczeń dla kluczy magazynu. Ta metoda nigdy nie będzie miała zakodowaną klucza magazynu w pliku konfiguracji, co spowoduje usunięcie tego ścieżek osoba uzyska dostęp do kluczy bez wyraźnej zgody.

Inną zaletą przy użyciu usługi Azure Key Vault jest można także kontrolować dostęp do swoich kluczy za pomocą usługi Azure Active Directory. Oznacza to, że możesz udzielić dostępu do kilku aplikacji, które musisz pobrać klucze z usługi Azure Key Vault i wiedzieć, czy inne aplikacje nie będą mogli uzyskać dostęp do kluczy bez nadawania im uprawnienia specjalnie.

> [!NOTE]
> Firma Microsoft zaleca korzystanie tylko jeden z kluczy we wszystkich aplikacjach, w tym samym czasie. Jeśli używasz klucz 1 w jednych miejscach i klawisz 2 w innych, nie można obrócić klucze bez utraty dostępu do aplikacji.

#### <a name="resources"></a>Zasoby

* [Zarządzanie ustawieniami konta magazynu w witrynie Azure portal](storage-account-manage.md)
* [Dokumentacja interfejsu API REST dostawcy zasobów usługi Azure Storage](https://msdn.microsoft.com/library/mt163683.aspx)

## <a name="data-plane-security"></a>Bezpieczeństwo płaszczyzny danych
Bezpieczeństwo płaszczyzny danych odnosi się do metody używane do zabezpieczania obiektów danych przechowywanych w usłudze Azure Storage. Zobaczyliśmy, metody służące do szyfrowania danych i zabezpieczeń podczas przesyłania danych, ale jak przejdziesz temat kontrolowania dostępu do obiektów?

Są trzy opcje do autoryzowania dostępu do obiektów danych w usłudze Azure Storage, w tym:

- Używanie programu Azure AD do autoryzowania dostępu do systemów plików i kolejek. Usługa Azure AD zapewnia korzyści w porównaniu do innych metod do autoryzacji, łącznie z usunięciem konieczności przechowywania wpisów tajnych w kodzie. Aby uzyskać więcej informacji, zobacz [uwierzytelniania dostępu do usługi Azure Storage przy użyciu usługi Azure Active Directory](storage-auth-aad.md). 
- Przy użyciu kluczy konta magazynu, aby autoryzować dostęp za pomocą klucza wspólnego. Uwierzytelnianie za pomocą klucza wspólnego wymaga przechowywania kluczy konta magazynu w aplikacji, dzięki czemu firma Microsoft zaleca używanie usługi Azure AD zamiast tego, gdzie to możliwe.
- Za pomocą sygnatur dostępu współdzielonego, aby udzielić kontrolowanymi uprawnieniami do obiektów danych specyficznych dla określonego przedziału czasu.

Oprócz ograniczania dostępu do autoryzacji, można również użyć [zapory i sieci wirtualne](storage-network-security.md) do ograniczania dostępu do konta magazynu, na podstawie reguł sieci.  Dzięki temu podejście odmowa dostępu do publicznego ruch internetowy i przyznać dostęp tylko do określonych sieci wirtualnych platformy Azure lub publicznej sieci internet zakresów adresów IP.

### <a name="storage-account-keys"></a>Klucze kont magazynu

Klucze konta magazynu są ciągami 512-bitowy, utworzone przez platformę Azure, która wraz z nazwą konta magazynu, może służyć do dostępu do obiektów danych przechowywanych na koncie magazynu.

Na przykład można odczytać plików. Wiele z tych akcji można przeprowadzić za pośrednictwem witryny Azure portal lub przy użyciu jednego z wielu aplikacji Eksploratora usługi Storage. Można także napisać kod, aby używać interfejsu API REST do wykonywania tych operacji.

Zgodnie z opisem w sekcji na [zabezpieczeń płaszczyzny zarządzania](#management-plane-security), dostęp do magazynu kluczy dla konta magazynu przy użyciu modelu usługi Azure Resource Manager mogą być kontrolowane za pomocą kontroli dostępu opartej na rolach (RBAC).

### <a name="how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies"></a>Jak delegować dostęp do obiektów na koncie przy użyciu sygnatury dostępu współdzielonego i przechowywanych zasad dostępu

Sygnaturę dostępu współdzielonego to ciąg zawierający token zabezpieczający, który można dołączyć do identyfikatora URI, który umożliwia delegowanie dostępu do magazynu obiektów oraz określanie ograniczeń takich jak uprawnienia i zakresu dat/godzin dostępu.

Możesz udzielić dostępu do plików lub katalogów.

Można nadać jednego odbiorcy tokenu sygnatury dostępu Współdzielonego, można użyć przekazywać obrazy do systemu plików w magazynie obiektów Blob i nadaj uprawnienia aplikacji sieci web, aby odczytać te obrazy. W obu przypadkach istnieje separacji zagadnień — każdej aplikacji można udzielić tylko dostępu, które są wymagane w celu wykonywania ich zadań. Jest to możliwe przy użyciu sygnatury dostępu współdzielonego.

#### <a name="why-you-want-to-use-shared-access-signatures"></a>Dlaczego, do którego chcesz użyć sygnatur dostępu współdzielonego

Dlaczego czy chcesz używać sygnatury dostępu Współdzielonego zamiast po prostu ujawnianiem klucz konta magazynu, które jest znacznie łatwiejsze? Ujawnianiem klucz konta magazynu jest podobne do udostępniania kluczy Królestwa usługi magazynu. Daje ona pełny dostęp. Ktoś może używać kluczy i przekazywanie ich całej biblioteki utworów muzycznych na koncie magazynu. One może również zastąpić pliki zainfekowanych wersjami lub kradzieży danych. Przekazywanie nieograniczony dostęp do swojego konta magazynu jest coś, co nie powinny być uwzględniane w niewielkim stopniu.

Za pomocą sygnatur dostępu współdzielonego można zaoferować klientowi tylko uprawnień wymaganych przez ograniczony czas. Na przykład jeśli ktoś jest próba przekazania pliku do Twojego konta, można przyznać im dostęp do zapisu dla wystarczająco dużo czasu, można przekazać pliku (w zależności od rozmiaru pliku, oczywiście). A jeśli zmienisz zdanie, możesz odwołać dostęp.

Ponadto można określić, że żądania wysyłane za pomocą sygnatury dostępu Współdzielonego są ograniczone do określonych adresów IP lub zakres adresów IP zewnętrznej na platformie Azure. Może również wymagać, że żądania są wykonywane przy użyciu określonego protokołu (HTTPS lub HTTP/HTTPS). Oznacza to, jeśli chcesz zezwolić na ruch HTTPS, możesz ustawić wymagany protokół HTTPS tylko i ruch HTTP będzie blokowany.

#### <a name="definition-of-a-shared-access-signature"></a>Definicja sygnatury dostępu współdzielonego

Sygnaturę dostępu współdzielonego to zbiór parametrów zapytania dołączona do adresu URL wskazuje na zasób

który zawiera informacje dotyczące dostępu przyznany i czas, dla których dostęp jest dozwolony. Oto przykład; Ten identyfikator URI zapewnia dostęp do odczytu do obiektu blob przez pięć minut. Parametry zapytania sygnatury dostępu Współdzielonego musi być zakodowane jako adres URL, takich jak 3A % dla dwukropek (:) lub % 20 spację.

```
http://mystorage.dfs.core.windows.net/myfilesystem/myfile.txt (URL to the file)
?sv=2015-04-05 (storage service version)
&st=2015-12-10T22%3A18%3A26Z (start time, in UTC time and URL encoded)
&se=2015-12-10T22%3A23%3A26Z (end time, in UTC time and URL encoded)
&sr=b (resource is a blob)
&sp=r (read access)
&sip=168.1.5.60-168.1.5.70 (requests can only come from this range of IP addresses)
&spr=https (only allow HTTPS requests)
&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D (signature used for the authentication of the SAS)
```

#### <a name="how-the-shared-access-signature-is-authorized-by-the-azure-storage-service"></a>Jak sygnatura dostępu współdzielonego jest autoryzowany przez usługę Azure Storage

Gdy Usługa magazynu odbiera żądanie, przyjmuje parametry zapytania danych wejściowych i tworzy podpis przy użyciu tej samej metody co program wywołujący. Porównuje dwa podpisów. Jeśli użytkownik wyrazi zgodę, usługi magazynu można sprawdzić wersji usługi storage: Upewnij się, że jest on prawidłowy, sprawdź, czy bieżąca data i godzina są w ramach określonego okna, upewnij się, że dostęp do żądanego odnosi się do żądań zgłaszanych itp.

Na przykład za pomocą naszych adresu URL podanego powyżej, jeśli adres URL został wskazuje plik, a nie obiektu blob to żądanie będzie się niepowodzeniem, ponieważ on określa, że sygnatura dostępu współdzielonego dla obiektu blob. Jeśli wywołania polecenia REST można zaktualizować obiektu blob, zakończy się niepowodzeniem, ponieważ sygnatura dostępu współdzielonego Określa, czy jest dozwolony dostęp tylko do odczytu.

#### <a name="types-of-shared-access-signatures"></a>Rodzaje sygnatur dostępu współdzielonego

* Sygnaturę dostępu Współdzielonego na poziomie usług może służyć do dostępu do określonych zasobów na koncie magazynu. Niektóre przykłady to podczas pobierania listy plików w systemie plików lub pobieranie pliku.
* Sygnatury dostępu Współdzielonego na poziomie konta może służyć do dostępu do wszystkich elementów, które umożliwia sygnaturę dostępu Współdzielonego na poziomie usługi. Ponadto oferuje opcje do zasobów, które nie są dozwolone z poziomu usługi sygnatury dostępu Współdzielonego, takie jak możliwość tworzenia systemów plików.

#### <a name="creating-a-sas-uri"></a>Tworzenie identyfikatora URI sygnatury dostępu Współdzielonego

1. Na żądanie, definiując wszystkie parametry zapytania każdorazowo, można utworzyć identyfikatora URI.

   To podejście jest elastyczny, ale jeśli masz logiczny zbiór parametrów, które są podobne za każdym razem, przy użyciu przechowywanych zasad dostępu jest lepiej zrozumieć.
2. Można utworzyć zasady dostępu przechowywane dla całego systemu plików, udziału plików, tabel lub kolejek. Można to jako podstawy dla identyfikatorów URI sygnatury dostępu Współdzielonego, możesz utworzyć. Uprawnienia na podstawie przechowywanych zasad dostępu mogą być łatwo odwoływane. Może mieć maksymalnie pięć zasady zdefiniowane dla każdego systemu plików, kolejki, tabela lub udziału plików.

   Na przykład jeśli były chce mieć wiele osób odczytu obiektów blob w określonym systemie plików, można utworzyć przechowywanych zasad dostępu, który jest wyświetlany komunikat "zapewniają dostęp do odczytu" i inne ustawienia, które będą takie same każdorazowo. Następnie można utworzyć identyfikatora URI sygnatury dostępu Współdzielonego, za pomocą ustawień przechowywanych zasad dostępu i określając Data/godzina wygaśnięcia. Dzięki temu jest, że nie trzeba określić wszystkie parametry zapytania za każdym razem, gdy.

#### <a name="revocation"></a>Odwołania

Załóżmy, że złamał z sygnatury dostępu Współdzielonego lub chcesz go zmienić z powodu zabezpieczeń firmy lub prawnych wymagań dotyczących zgodności. Jak można odwołać dostęp do zasobów przy użyciu tej sygnatury dostępu Współdzielonego? To zależy od sposobu tworzenia identyfikatora URI sygnatury dostępu Współdzielonego.

Jeśli używasz ad-hoc identyfikatory URI są trzy opcje. Można wystawiać tokeny sygnatur dostępu Współdzielonego z zasadami wygasania krótki, a następnie poczekaj na wygaśnięcie sygnatur dostępu współdzielonego. Można zmienić lub usunąć zasób (przy założeniu, że token zostało ograniczone do pojedynczego obiektu). Można zmieniać klucze konta magazynu. Ta ostatnia opcja może mieć znaczący wpływ, w zależności od tego, ile usługi używają tego konta magazynu i prawdopodobnie nie jest coś, co chcesz zrobić bez niektóre planowania.

Korzystając z sygnatury dostępu Współdzielonego uzyskane z przechowywanych zasad dostępu, można usunąć dostęp, odwołując zasady dostępu przechowywane — Zmień to po prostu, aby już wygasł, albo usuń go całkowicie. Ma efekt natychmiastowy i unieważnia każdej sygnatury dostępu Współdzielonego utworzonych przy użyciu tego przechowywanych zasad dostępu. Aktualizowanie lub usuwanie zasady dostępu przechowywane może tabeli osób wpływ na uzyskiwanie dostępu do tego określonego systemu plików, udziału plików lub kolejki za pomocą sygnatury dostępu Współdzielonego, ale jeśli klienci są zapisywane, dzięki czemu będą one żądać nowej sygnatury dostępu Współdzielonego, gdy stary staje się nieprawidłowy, to będzie działać prawidłowo.

Ponieważ przy użyciu sygnatury dostępu Współdzielonego pochodną zasady dostępu przechowywane daje możliwość natychmiast odwołać tej sygnatury dostępu Współdzielonego, jest zalecanym najlepszym rozwiązaniem jest zawsze używaj przechowywanych zasad dostępu, gdy jest to możliwe.

#### <a name="resources"></a>Zasoby

Aby uzyskać szczegółowe informacje na temat używania sygnatur dostępu współdzielonego i przechowywanych zasad dostępu, wraz z przykładami można znaleźć w następujących artykułach:

* Są to gama artykułów.

  * [Sygnatura dostępu Współdzielonego usługi](https://msdn.microsoft.com/library/dn140256.aspx)

    Ten artykuł zawiera przykłady przy użyciu sygnatury dostępu Współdzielonego poziomu usługi za pomocą obiektów blob, kolejki komunikatów, zakresy tabeli i plików.
  * [Utworzenie sygnatury dostępu Współdzielonego usługi](https://msdn.microsoft.com/library/dn140255.aspx)
  * [Utworzenie sygnatury dostępu Współdzielonego konta](https://msdn.microsoft.com/library/mt584140.aspx)
* Są to samouczki dotyczące tworzenia sygnatury dostępu współdzielonego i przechowywanych zasad dostępu za pomocą biblioteki klienckiej .NET.

  * [Używanie sygnatur dostępu współdzielonego (SAS)](../storage-dotnet-shared-access-signature-part-1.md)
  * [Udostępnione sygnatur dostępu, część 2: Tworzenie i używanie sygnatury dostępu Współdzielonego z usługi Blob Service](../blobs/storage-dotnet-shared-access-signature-part-2.md)

    Ten artykuł zawiera opis modelu sygnatur dostępu Współdzielonego, przykłady sygnatur dostępu współdzielonego i zalecenia dotyczące najlepszych praktyk korzystanie z sygnatury dostępu Współdzielonego. Omówiono również jest cofnięcie przyznanie uprawnienia.

* Authentication

  * [Uwierzytelnianie dla usług Azure Storage](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* Udostępnione sygnatur dostępu wprowadzenie do samouczka

  * [Wprowadzenie do samouczka sygnatury dostępu Współdzielonego](https://github.com/Azure-Samples/storage-dotnet-sas-getting-started)

## <a name="encryption-in-transit"></a>Szyfrowanie podczas transferu

### <a name="transport-level-encryption--using-https"></a>Szyfrowanie na poziomie transportu — przy użyciu protokołu HTTPS

Kolejny krok, które należy podjąć w celu zapewnienia bezpieczeństwa danych usługi Azure Storage jest szyfrowanie danych między klientem i usługi Azure Storage. Pierwszy zalecenie to zawsze użycie [HTTPS](https://en.wikipedia.org/wiki/HTTPS) protokołu, który zapewnia bezpieczną komunikację za pośrednictwem publicznej sieci Internet.

Aby uzyskać bezpieczny kanał komunikacyjny, należy zawsze używać protokołu HTTPS podczas wywoływania interfejsów API REST lub uzyskiwania dostępu do obiektów w magazynie. Ponadto **sygnatur dostępu współdzielonego**, której można delegować dostępu do obiektów usługi Azure Storage, podając opcję, aby określić, że mogą być używane tylko z protokołu HTTPS, korzystając z sygnatury dostępu współdzielonego, upewniając się, że ktoś wysyła limit połączeń za pomocą sygnatury dostępu Współdzielonego tokenów użyje odpowiedni protokół.

Można wymusić użycie protokołu HTTPS podczas wywoływania interfejsów API REST w celu uzyskania dostępu do obiektów w ramach kont magazynu przez włączenie [Wymagany bezpieczny transfer](../storage-require-secure-transfer.md) dla konta magazynu. Będzie można odmówić połączenia przy użyciu protokołu HTTP, gdy ta opcja jest włączona.

## <a name="encryption-at-rest"></a>Szyfrowanie w spoczynku

### <a name="storage-service-encryption-sse"></a>Szyfrowanie usługi Storage (SSE)

Funkcja SSE jest włączona dla wszystkich kont magazynu i nie może być wyłączony. Usługa SSE automatycznie szyfruje dane podczas zapisywania jej do usługi Azure Storage. Podczas odczytywania danych z usługi Azure Storage, zostaje odszyfrowywany przez usługę Azure Storage przed zwróceniem. Usługa SSE umożliwia Zabezpieczanie danych bez konieczności modyfikowania kodu ani dodać kod do dowolnej aplikacji.

Można użyć kluczy zarządzanych przez firmę Microsoft lub klucze niestandardowe. Firma Microsoft generuje klucze zarządzanych i obsługuje ich bezpiecznego magazynu, a także ich regularne obrotu, zgodnie z definicją, wewnętrzne zasady firmy Microsoft. Aby uzyskać więcej informacji na temat używania kluczy niestandardowych zobacz [szyfrowanie usługi Storage przy użyciu kluczy zarządzanych przez klienta w usłudze Azure Key Vault](storage-service-encryption-customer-managed-keys.md).
