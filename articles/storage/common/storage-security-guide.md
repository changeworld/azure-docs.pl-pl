---
title: Przewodnik po zabezpieczeniach magazynu platformy Azure | Dokumentacja firmy Microsoft
description: Szczegóły wiele metod zabezpieczania usługi Azure Storage, w tym między innymi RBAC, szyfrowanie usługi Storage, szyfrowanie po stronie klienta, protokołu SMB 3.0 i usługi Azure Disk Encryption.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 3d5bfa2426d58fa5a09d2203272536eec7fa9c55
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65789959"
---
# <a name="azure-storage-security-guide"></a>Przewodnik po zabezpieczeniach magazynu platformy Azure

Usługa Azure Storage udostępnia rozbudowany zestaw funkcji zabezpieczeń, umożliwiających deweloperom tworzenie bezpiecznych aplikacji:

- Wszystkie dane (w tym metadane) zapisywane do usługi Azure Storage są automatycznie szyfrowane przy użyciu [szyfrowanie usługi Storage (SSE)](storage-service-encryption.md). Aby uzyskać więcej informacji, zobacz [ogłoszenie domyślne szyfrowanie obiektów blob platformy Azure, pliki, tabela i Queue Storage](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).
- Azure Active Directory (Azure AD) i kontrola dostępu oparta na rolach (RBAC) są obsługiwane dla usługi Azure Storage zarówno dla zasobów operacje zarządzania i operacje na danych, w następujący sposób:   
    - Można przypisać role RBAC ograniczone do konta magazynu do podmiotów zabezpieczeń i użyj usługi Azure AD, aby autoryzować operacji zarządzania zasobów, takich jak zarządzanie kluczami.
    - Integracja z usługą Azure AD jest obsługiwana dla operacji danych obiektów blob i kolejek. Można przypisać role RBAC ograniczone do subskrypcji, grupy zasobów, konto magazynu lub pojedynczy kontener lub kolejki do podmiotu zabezpieczeń lub tożsamość zarządzaną dla zasobów platformy Azure. Aby uzyskać więcej informacji, zobacz [uwierzytelniania dostępu do usługi Azure Storage przy użyciu usługi Azure Active Directory](storage-auth-aad.md).   
- Dane mogą być chronione przesyłane między aplikacją i platformy Azure przy użyciu [szyfrowania po stronie klienta](../storage-client-side-encryption.md), HTTPS lub SMB 3.0.  
- Dysków systemu operacyjnego i danych, które są używane przez maszyny wirtualne platformy Azure może być szyfrowana przy użyciu [usługi Azure Disk Encryption](../../security/azure-security-disk-encryption.md). 
- Delegowanego dostępu do obiektów danych w usłudze Azure Storage można przyznać za pomocą [sygnatur dostępu współdzielonego](../storage-dotnet-shared-access-signature-part-1.md).

Ten artykuł zawiera omówienie każdego z tych funkcji zabezpieczeń, które mogą być używane z usługi Azure Storage. Łącza są dostarczane do artykułów, które będzie podać szczegóły dotyczące każdej funkcji to łatwo zrobić dalsze badanie dotyczące każdego tematu.

Tematy, które mają być uwzględnione w tym artykule są następujące:

* [Zabezpieczenia płaszczyzny zarządzania](#management-plane-security) — zabezpieczanie konta magazynu

  Płaszczyzna zarządzania składa się z zasobami umożliwiają zarządzanie kontem magazynu. W tej sekcji omówiono modelu wdrażania usługi Azure Resource Manager i sposobu używania kontroli dostępu opartej na rolach (RBAC) do kontrolowania dostępu do konta magazynu. Zajmuje się także, zarządzanie klucze konta magazynu i jak można wygenerować je ponownie.
* [Zabezpieczenia na płaszczyźnie danych](#data-plane-security) — zabezpieczanie dostępu do danych

  W tej sekcji omówimy zezwalania na dostęp do danych rzeczywistych obiektów w ramach konta magazynu, takie jak obiekty BLOB, plików, kolejek i tabel, za pomocą sygnatur dostępu współdzielonego i przechowywanych zasad dostępu. Omówimy zarówno sygnatura SAS na poziomie usługi, jak i interfejsie SAS na poziomie konta. Również zobaczymy, jak ograniczyć dostęp do określonego adresu IP (lub zakres adresów IP), jak ograniczyć protokół HTTPS i sposób odwołać sygnaturę dostępu współdzielonego nie trzeba czekać na jego wygaśnięcia.
* [Szyfrowanie podczas transferu](#encryption-in-transit)

  W tej sekcji omówiono sposób zabezpieczania danych podczas przesyłania do i z usługi Azure Storage. Omówimy zalecane użycie protokołu HTTPS i szyfrowania używany przez protokół SMB 3.0 udziałów plików platformy Azure. Firma Microsoft będzie również Spójrz na szyfrowanie po stronie klienta, co pozwala szyfrować dane, zanim zostanie przekazany do usługi Storage w aplikacji klienckiej i odszyfrować danych, gdy zostanie przeniesiona poza magazynu.
* [Szyfrowanie w spoczynku](#encryption-at-rest)

  Firma Microsoft będzie komunikować informacje szyfrowanie usługi Storage (SSE), który teraz jest automatycznie włączona dla konta magazynu z nowymi i istniejącymi. Również przedstawiony zostanie sposób użycia usługi Azure Disk Encryption i zapoznaj się z podstawowych różnic i przypadków szyfrowania dysku i SSE i szyfrowanie po stronie klienta. Krótko omówimy zgodności ze standardem FIPS dla Stanów Zjednoczonych Komputery dla instytucji rządowych.
* Za pomocą [Storage Analytics](#storage-analytics) inspekcji dostępu do usługi Azure Storage

  W tej sekcji omówiono sposób znajdowania informacji w dziennikach analizy magazynu dla żądania. Utworzymy Przyjrzyj się analityka magazynu rzeczywistych danych dzienników i zobacz, jak rozpoznać, czy żądanie przy użyciu klucza konta magazynu przy użyciu podpisu dostęp współdzielony lub anonimowo oraz tego, czy zakończonych powodzeniem lub niepowodzeniem.
* [Włączanie klientów oparte na przeglądarce, przy użyciu mechanizmu CORS](#cross-origin-resource-sharing-cors)

  W tej sekcji opowiada, jak umożliwić współużytkowanie zasobów między źródłami (cors). Omówimy międzydomenowy dostęp i jak go obsłużyć za pomocą funkcji CORS, wbudowana w usłudze Azure Storage.

## <a name="management-plane-security"></a>Zabezpieczenia płaszczyzny zarządzania
Płaszczyzna zarządzania składa się z działań, które wpływają na konto magazynu. Na przykład można utworzyć lub usunąć konto magazynu, uzyskać listę kont magazynu w ramach subskrypcji, pobrać klucze konta magazynu lub ponowne generowanie kluczy konta magazynu.

Podczas tworzenia nowego konta magazynu, możesz wybrać modelu wdrożenia klasycznego lub usługi Resource Manager. Klasyczny model tworzenia zasobów na platformie Azure umożliwia tylko sztywnego dostępu do subskrypcji, a w pozycji konta magazynu.

Ten przewodnik koncentruje się na modelu usługi Resource Manager, który jest zalecany sposób tworzenia kont magazynu. Z kontami magazynu usługi Resource Manager, a nie dające dostęp do całej subskrypcji możesz kontrolować dostęp na poziomie bardziej ograniczone do płaszczyzny zarządzania za pomocą kontroli dostępu opartej na rolach (RBAC).

### <a name="how-to-secure-your-storage-account-with-role-based-access-control-rbac"></a>Jak zabezpieczyć swoje konto magazynu przy użyciu kontroli dostępu opartej na rolach (RBAC)
Poniżej omówiono RBAC jest i jak można jej używać. Każda subskrypcja platformy Azure zawiera usługę Azure Active Directory. Użytkownikom, grupom i aplikacjom z tego katalogu można udzielić dostępu do zarządzania zasobami w subskrypcji platformy Azure, które używają modelu wdrażania usługi Resource Manager. Ten typ zabezpieczeń jest określany jako kontrola dostępu oparta na rolach (RBAC). Aby zarządzać dostępem, można użyć [witryny Azure portal](https://portal.azure.com/), [narzędzia wiersza polecenia platformy Azure](../../cli-install-nodejs.md), [PowerShell](/powershell/azureps-cmdlets-docs), lub [magazynu zasobów dostawcy interfejsów API REST Azure](https://msdn.microsoft.com/library/azure/mt163683.aspx).

Za pomocą modelu usługi Resource Manager należy umieścić na koncie magazynu w zasobów grupy i kontroli dostępu do płaszczyzny zarządzania tym konkretnym kontem magazynu za pomocą usługi Azure Active Directory. Na przykład można udzielić określonym użytkownikom możliwość dostępu do kluczy konta magazynu, podczas gdy inni użytkownicy mogą wyświetlać informacje o koncie magazynu, ale nie dostęp do kluczy konta magazynu.

#### <a name="granting-access"></a>Udzielanie dostępu
Dostęp jest udzielany, przypisując odpowiednie role RBAC do użytkowników, grup i aplikacji w zakresie prawo. Aby udzielić dostępu do całej subskrypcji, możesz przypisać rolę na poziomie subskrypcji. Możesz udzielić dostępu do wszystkich zasobów w grupie zasobów, przyznając uprawnienia do samej grupy zasobów. Można także przypisać określonych ról, do określonych zasobów, takich jak konta magazynu.

Poniżej przedstawiono główne punkty, które musisz wiedzieć o otwieranie operacje zarządzania kontem usługi Azure Storage za pomocą funkcji RBAC:

* W przypadku przypisania dostępu po prostu przypisać rolę do konta, które chcesz mieć dostęp. Możesz kontrolować dostęp do operacji używane do zarządzania tym kontem magazynu, ale nie do obiektów danych w ramach konta. Na przykład, można przyznać uprawnienia do pobierania właściwości konta magazynu (na przykład nadmiarowości), ale nie do kontenera lub dane z kontenera w usłudze Blob Storage.
* Niepowołanym ma uprawnienia do dostępu do obiektów danych w ramach konta magazynu można nadać im uprawnienia do odczytu klucze konta magazynu, a ten użytkownik mógł następnie użyć tych kluczy do uzyskania dostępu do obiektów blob, kolejek, tabel i plików.
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

  W tym artykule opisano wdrożenia usługi Resource Manager i klasycznych modeli wdrażania oraz informacje o korzyści z używania usługi Resource Manager i grup zasobów. Wyjaśniono, jak działają usługi Azure Compute, sieć i dostawców magazynu w ramach modelu usługi Resource Manager.
* [Zarządzanie kontrolą dostępu opartą na rolach za pomocą interfejsu API REST](../../role-based-access-control/role-assignments-rest.md)

  W tym artykule przedstawiono sposób zarządzania kontrolą dostępu opartą na rolach (RBAC) za pomocą interfejsu API REST.
* [Dokumentacja interfejsu API REST dostawcy zasobów usługi Azure Storage](https://msdn.microsoft.com/library/azure/mt163683.aspx)

  Ta dokumentacja interfejsu API opisuje interfejsy API, można użyć do programowego zarządzania konta magazynu.
* [Interfejs API uwierzytelniania Użyj usługi Resource Manager do dostępu do subskrypcji](../../azure-resource-manager/resource-manager-api-authentication.md)

  W tym artykule przedstawiono sposób uwierzytelniania przy użyciu interfejsów API usługi Resource Manager.
* [Kontrola dostępu oparta na rolach dla platformy Microsoft Azure — konferencja Ignite](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

  To jest link do filmu wideo w witrynie Channel 9 z konferencji Microsoft Ignite 2015. W tej sesji rozmawiamy o możliwościach zarządzania dostępem i raportowania na platformie Azure i eksplorujemy najlepsze rozwiązania dotyczące zabezpieczania dostępu do subskrypcji Azure za pomocą usługi Azure Active Directory.

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

Innym rozwiązaniem jest umieszczenie klucza konta magazynu w [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) jako klucz tajny i mieć aplikacji pobrać klucza z tego miejsca. Następnie gdy ponowne wygenerowanie klucza i aktualizacji usługi Azure Key Vault, aplikacje nie należy do ponownego wdrożenia, ponieważ ich przejmie nowy klucz w usłudze Azure Key Vault automatycznie. Zauważ, że może mieć aplikacja będzie odczytywać klucza każdorazowo, będą potrzebne lub można buforowanie w pamięci i jeśli zakończy się niepowodzeniem w przypadku korzystania z niego, klucz ponownego pobrania z usługi Azure Key Vault.

Również przy użyciu usługi Azure Key Vault dodaje kolejny poziom zabezpieczeń dla kluczy magazynu. Ta metoda nigdy nie będzie miała zakodowaną klucza magazynu w pliku konfiguracji, co spowoduje usunięcie tego ścieżek osoba uzyska dostęp do kluczy bez wyraźnej zgody.

Inną zaletą przy użyciu usługi Azure Key Vault jest można także kontrolować dostęp do swoich kluczy za pomocą usługi Azure Active Directory. Oznacza to, że możesz udzielić dostępu do kilku aplikacji, które musisz pobrać klucze z usługi Azure Key Vault i wiedzieć, czy inne aplikacje nie będą mogli uzyskać dostęp do kluczy bez nadawania im uprawnienia specjalnie.

> [!NOTE]
> Firma Microsoft zaleca korzystanie tylko jeden z kluczy we wszystkich aplikacjach, w tym samym czasie. Jeśli używasz klucz 1 w jednych miejscach i klawisz 2 w innych, nie można obrócić klucze bez utraty dostępu do aplikacji.

#### <a name="resources"></a>Zasoby

* [Zarządzanie ustawieniami konta magazynu w witrynie Azure portal](storage-account-manage.md)
* [Dokumentacja interfejsu API REST dostawcy zasobów usługi Azure Storage](https://msdn.microsoft.com/library/mt163683.aspx)

## <a name="data-plane-security"></a>Bezpieczeństwo płaszczyzny danych
Bezpieczeństwo płaszczyzny danych odnosi się do metody używane do zabezpieczania obiektów danych przechowywanych w usłudze Azure Storage — obiektów blob, kolejek, tabel i plików. Zobaczyliśmy, metody służące do szyfrowania danych i zabezpieczeń podczas przesyłania danych, ale jak przejdziesz temat kontrolowania dostępu do obiektów?

Są trzy opcje do autoryzowania dostępu do obiektów danych w usłudze Azure Storage, w tym:

- Używanie programu Azure AD do autoryzowania dostępu do kontenerów i kolejek. Usługa Azure AD zapewnia korzyści w porównaniu do innych metod do autoryzacji, łącznie z usunięciem konieczności przechowywania wpisów tajnych w kodzie. Aby uzyskać więcej informacji, zobacz [uwierzytelniania dostępu do usługi Azure Storage przy użyciu usługi Azure Active Directory](storage-auth-aad.md). 
- Przy użyciu kluczy konta magazynu, aby autoryzować dostęp za pomocą klucza wspólnego. Uwierzytelnianie za pomocą klucza wspólnego wymaga przechowywania kluczy konta magazynu w aplikacji, dzięki czemu firma Microsoft zaleca używanie usługi Azure AD zamiast tego, gdzie to możliwe.
- Za pomocą sygnatur dostępu współdzielonego, aby udzielić kontrolowanymi uprawnieniami do obiektów danych specyficznych dla określonego przedziału czasu.

Ponadto dla magazynu obiektów Blob, można zezwolić publicznego dostępu do obiektów blob, ustawiając poziom dostępu dla kontener, który zawiera obiekty BLOB w związku z tym. Jeśli ustawisz dostępu dla kontenera obiektów Blob lub kontenera, umożliwi publicznego dostępu do odczytu dla obiektów blob w kontenerze. Oznacza to, że każda osoba mająca adres URL wskazuje obiekt blob w kontenerze otwórz go w przeglądarce, bez przy użyciu podpisu dostępu współdzielonego oraz korzystanie z kluczy konta magazynu.

Oprócz ograniczania dostępu do autoryzacji, można również użyć [zapory i sieci wirtualne](storage-network-security.md) do ograniczania dostępu do konta magazynu, na podstawie reguł sieci.  Dzięki temu podejście odmowa dostępu do publicznego ruch internetowy i przyznać dostęp tylko do określonych sieci wirtualnych platformy Azure lub publicznej sieci internet zakresów adresów IP.

### <a name="storage-account-keys"></a>Klucze kont magazynu
Klucze konta magazynu są ciągami 512-bitowy, utworzone przez platformę Azure, która wraz z nazwą konta magazynu, może służyć do dostępu do obiektów danych przechowywanych na koncie magazynu.

Można na przykład odczytu obiektów blob, zapisu w kolejkach, tworzyć tabele i zmodyfikować pliki. Wiele z tych akcji można przeprowadzić za pośrednictwem witryny Azure portal lub przy użyciu jednego z wielu aplikacji Eksploratora usługi Storage. Można także napisać kod, aby wykonywać te operacje za pomocą interfejsu API REST lub jednej z bibliotek klienckich magazynu.

Zgodnie z opisem w sekcji na [zabezpieczeń płaszczyzny zarządzania](#management-plane-security), dostęp do magazynu kluczy dla klasycznego konta magazynu można udzielić, zapewniając pełny dostęp do subskrypcji platformy Azure. Dostęp do magazynu kluczy dla konta magazynu przy użyciu modelu usługi Azure Resource Manager mogą być kontrolowane za pomocą kontroli dostępu opartej na rolach (RBAC).

### <a name="how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies"></a>Jak delegować dostęp do obiektów na koncie przy użyciu sygnatury dostępu współdzielonego i przechowywanych zasad dostępu
Sygnaturę dostępu współdzielonego to ciąg zawierający token zabezpieczający, który można dołączyć do identyfikatora URI, który umożliwia delegowanie dostępu do magazynu obiektów oraz określanie ograniczeń takich jak uprawnienia i zakresu dat/godzin dostępu.

Możesz udzielić dostępu do obiektów blob, kontenerów, wiadomości w kolejce, plików i tabel. Z tabelami może faktycznie udzielić uprawnień dostępu zakresu jednostek w tabeli, określając zakresów kluczy partycji i wiersza do których chcesz, aby użytkownik ma dostęp. Na przykład, jeśli masz dane przechowywane w usłudze klucza partycji, geograficzne stanu, można nadasz innej osobie dostępu do tylko dane dla Kalifornia.

W kolejnym przykładzie może udzielić aplikacji sieci web token sygnatury dostępu Współdzielonego, która umożliwia zapisywanie wpisów do kolejki i nadać procesu roboczego aplikacji roli tokenu sygnatury dostępu Współdzielonego, Pobierz komunikaty z kolejki i przetwarzać je. Lub można nadać jednego odbiorcy tokenu sygnatury dostępu Współdzielonego, można Użyj, aby przekazywać obrazy do kontenera w usłudze Blob Storage i nadaj uprawnienia aplikacji sieci web, aby odczytać te obrazy. W obu przypadkach istnieje separacji zagadnień — każdej aplikacji można udzielić tylko dostępu, które są wymagane w celu wykonywania ich zadań. Jest to możliwe przy użyciu sygnatury dostępu współdzielonego.

#### <a name="why-you-want-to-use-shared-access-signatures"></a>Dlaczego, do którego chcesz użyć sygnatur dostępu współdzielonego
Dlaczego czy chcesz używać sygnatury dostępu Współdzielonego zamiast po prostu ujawnianiem klucz konta magazynu, które jest znacznie łatwiejsze? Ujawnianiem klucz konta magazynu jest podobne do udostępniania kluczy Królestwa usługi magazynu. Daje ona pełny dostęp. Ktoś może używać kluczy i przekazywanie ich całej biblioteki utworów muzycznych na koncie magazynu. One może również zastąpić pliki zainfekowanych wersjami lub kradzieży danych. Przekazywanie nieograniczony dostęp do swojego konta magazynu jest coś, co nie powinny być uwzględniane w niewielkim stopniu.

Za pomocą sygnatur dostępu współdzielonego można zaoferować klientowi tylko uprawnień wymaganych przez ograniczony czas. Na przykład jeśli ktoś przekazywania obiektu blob na koncie, można przyznać im dostęp do zapisu dla wystarczający tylko raz przekazać obiekt blob (w zależności od rozmiaru obiektów blob, oczywiście). A jeśli zmienisz zdanie, możesz odwołać dostęp.

Ponadto można określić, że żądania wysyłane za pomocą sygnatury dostępu Współdzielonego są ograniczone do określonych adresów IP lub zakres adresów IP zewnętrznej na platformie Azure. Może również wymagać, że żądania są wykonywane przy użyciu określonego protokołu (HTTPS lub HTTP/HTTPS). Oznacza to, jeśli chcesz zezwolić na ruch HTTPS, możesz ustawić wymagany protokół HTTPS tylko i ruch HTTP będzie blokowany.

#### <a name="definition-of-a-shared-access-signature"></a>Definicja sygnatury dostępu współdzielonego
Sygnaturę dostępu współdzielonego to zbiór parametrów zapytania dołączona do adresu URL wskazuje na zasób

który zawiera informacje dotyczące dostępu przyznany i czas, dla których dostęp jest dozwolony. Oto przykład; Ten identyfikator URI zapewnia dostęp do odczytu do obiektu blob przez pięć minut. Należy pamiętać, że parametry zapytania sygnatury dostępu Współdzielonego musi być zakodowane jako adres URL, takich jak 3A % dla dwukropek (:) lub % 20 spację.

```
http://mystorage.blob.core.windows.net/mycontainer/myblob.txt (URL to the blob)
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
* Sygnaturę dostępu Współdzielonego na poziomie usług może służyć do dostępu do określonych zasobów na koncie magazynu. Kilka przykładów pobierają listę obiektów blob w kontenerze, pobieranie obiektu blob, aktualizowanie jednostkę w tabeli, dodawanie komunikatów do kolejki lub próba przekazania pliku do udziału plików.
* Sygnatury dostępu Współdzielonego na poziomie konta może służyć do dostępu do wszystkich elementów, które umożliwia sygnaturę dostępu Współdzielonego na poziomie usługi. Ponadto oferuje opcje do zasobów, które nie są dozwolone z poziomu usługi sygnatury dostępu Współdzielonego, takie jak możliwość tworzenia kontenerów, tabel, kolejek i udziałów plików. Dostęp do wielu usług można również określić tylko raz. Na przykład może być nadasz innej osobie dostępu do obiektów blob i plików na koncie magazynu.

#### <a name="creating-a-sas-uri"></a>Tworzenie identyfikatora URI sygnatury dostępu Współdzielonego
1. Na żądanie, definiując wszystkie parametry zapytania każdorazowo, można utworzyć identyfikatora URI.

   To podejście jest elastyczny, ale jeśli masz logiczny zbiór parametrów, które są podobne za każdym razem, przy użyciu przechowywanych zasad dostępu jest lepiej zrozumieć.
2. Można utworzyć zasady dostępu przechowywane dla całego kontenera, udziału plików, tabel lub kolejek. Można to jako podstawy dla identyfikatorów URI sygnatury dostępu Współdzielonego, możesz utworzyć. Uprawnienia na podstawie przechowywanych zasad dostępu mogą być łatwo odwoływane. Może mieć maksymalnie pięć zasady zdefiniowane dla każdego kontenera, kolejki, tabeli lub udziału plików.

   Na przykład jeśli były chce mieć wiele osób odczytu obiektów blob w określonym kontenerze, można utworzyć przechowywanych zasad dostępu, który jest wyświetlany komunikat "zapewniają dostęp do odczytu" i inne ustawienia, które będą takie same każdorazowo. Następnie można utworzyć identyfikatora URI sygnatury dostępu Współdzielonego, za pomocą ustawień przechowywanych zasad dostępu i określając Data/godzina wygaśnięcia. Dzięki temu jest, że nie trzeba określić wszystkie parametry zapytania za każdym razem, gdy.

#### <a name="revocation"></a>Odwołania
Załóżmy, że złamał z sygnatury dostępu Współdzielonego lub chcesz go zmienić z powodu zabezpieczeń firmy lub prawnych wymagań dotyczących zgodności. Jak można odwołać dostęp do zasobów przy użyciu tej sygnatury dostępu Współdzielonego? To zależy od sposobu tworzenia identyfikatora URI sygnatury dostępu Współdzielonego.

Jeśli używasz ad-hoc identyfikatory URI są trzy opcje. Można wystawiać tokeny sygnatur dostępu Współdzielonego z zasadami wygasania krótki, a następnie poczekaj na wygaśnięcie sygnatur dostępu współdzielonego. Można zmienić lub usunąć zasób (przy założeniu, że token zostało ograniczone do pojedynczego obiektu). Można zmieniać klucze konta magazynu. Ta ostatnia opcja może mieć znaczący wpływ, w zależności od tego, ile usługi używają tego konta magazynu i prawdopodobnie nie jest coś, co chcesz zrobić bez niektóre planowania.

Korzystając z sygnatury dostępu Współdzielonego uzyskane z przechowywanych zasad dostępu, można usunąć dostęp, odwołując zasady dostępu przechowywane — Zmień to po prostu, aby już wygasł, albo usuń go całkowicie. Ma efekt natychmiastowy i unieważnia każdej sygnatury dostępu Współdzielonego utworzonych przy użyciu tego przechowywanych zasad dostępu. Aktualizowanie lub usuwanie zasady dostępu przechowywane może tabeli osób wpływ na uzyskiwanie dostępu do tego kontenera dla udziału plików lub kolejki przy użyciu sygnatury dostępu Współdzielonego, ale jeśli klienci są zapisywane, dzięki czemu będą one żądać nowej sygnatury dostępu Współdzielonego, gdy stary staje się nieprawidłowy, to będzie działać prawidłowo.

Ponieważ przy użyciu sygnatury dostępu Współdzielonego pochodną zasady dostępu przechowywane daje możliwość natychmiast odwołać tej sygnatury dostępu Współdzielonego, jest zalecanym najlepszym rozwiązaniem jest zawsze używaj przechowywanych zasad dostępu, gdy jest to możliwe.

#### <a name="resources"></a>Zasoby
Aby uzyskać szczegółowe informacje na temat używania sygnatur dostępu współdzielonego i przechowywanych zasad dostępu, wraz z przykładami można znaleźć w następujących artykułach:

* Są to gama artykułów.

  * [Sygnatura dostępu Współdzielonego usługi](https://msdn.microsoft.com/library/dn140256.aspx)

    Ten artykuł zawiera przykłady przy użyciu sygnatury dostępu Współdzielonego poziomu usługi za pomocą obiektów blob, kolejki komunikatów, zakresy tabeli i plików.
  * [Utworzenie sygnatury dostępu Współdzielonego usługi](https://msdn.microsoft.com/library/dn140255.aspx)
  * [Utworzenie sygnatury dostępu Współdzielonego konta](https://msdn.microsoft.com/library/mt584140.aspx)

* Jest to samouczek dotyczące tworzenia sygnatury dostępu współdzielonego i przechowywanych zasad dostępu za pomocą biblioteki klienckiej .NET.
  * [Używanie sygnatur dostępu współdzielonego (SAS)](../storage-dotnet-shared-access-signature-part-1.md)

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

### <a name="using-encryption-during-transit-with-azure-file-shares"></a>Przy użyciu szyfrowania podczas przesyłania przy użyciu udziałów plików platformy Azure
[Usługa Azure Files](../files/storage-files-introduction.md) obsługuje szyfrowanie, za pośrednictwem protokołu SMB 3.0 i HTTPS, korzystając z interfejsu API REST plików. Podczas instalowania poza regionem świadczenia udziału plików platformy Azure znajduje się, na przykład lokalnie lub w innym regionie platformy Azure, protokołu SMB 3.0, szyfrowanie jest zawsze wymagany. Protokół SMB 2.1 nie obsługuje szyfrowania, więc domyślnie połączenia są dozwolone tylko w obrębie tego samego regionu na platformie Azure, ale można wymusić przez protokół SMB 3.0 za pomocą szyfrowania [Wymaganie bezpiecznego transferu](../storage-require-secure-transfer.md) dla konta magazynu.

Protokół SMB 3.0 za pomocą szyfrowania jest dostępna w [wszystkie obsługiwane systemy operacyjne Windows i Windows Server](../files/storage-how-to-use-files-windows.md) tylko z wyjątkiem Windows 7 i Windows Server 2008 R2, który obsługuje protokół SMB 2.1. Obsługiwana jest również protokół SMB 3.0 na [macOS](../files/storage-how-to-use-files-mac.md) i w dystrybucjach systemu [Linux](../files/storage-how-to-use-files-linux.md) przy użyciu jądra systemu Linux 4.11 i nowsze wersje. Obsługa szyfrowania protokołu SMB 3.0 został także backported do starszych wersji jądra systemu Linux przez kilka dystrybucje systemu Linux, zapoznaj się z [wymagania dotyczące klienta SMB opis](../files/storage-how-to-use-files-linux.md#smb-client-reqs).

### <a name="using-client-side-encryption-to-secure-data-that-you-send-to-storage"></a>Za pomocą szyfrowania po stronie klienta do zabezpieczania danych wysyłanych do usługi storage
Inną opcją, który pomaga zagwarantować, że Twoje dane są bezpieczne podczas przesyłania ich między aplikacją kliencką a magazynem jest szyfrowanie po stronie klienta. Dane są szyfrowane, zanim zostaną przesłane do usługi Azure Storage. Podczas pobierania danych z usługi Azure Storage, dane są odszyfrowywane po ich odebraniu po stronie klienta. Mimo, że dane są szyfrowane, przechodząc w sieci, firma Microsoft zaleca również używać protokołu HTTPS, ponieważ zawiera ona sprawdzania integralności danych wbudowane, która ułatwić rozwiązywanie błędów związanych z sieci, wpływających na integralności danych.

Szyfrowanie po stronie klienta jest również szyfrowanie danych magazynowanych, ponieważ dane są przechowywane w postaci zaszyfrowanej. Omówimy to bardziej szczegółowo w sekcji na [szyfrowanie w spoczynku](#encryption-at-rest).

## <a name="encryption-at-rest"></a>Szyfrowanie w spoczynku
Istnieją trzy funkcje platformy Azure, które zapewniają szyfrowanie danych magazynowanych. Usługa Azure Disk Encryption umożliwia szyfrowanie dysków systemu operacyjnego i danych na maszynach wirtualnych IaaS. Szyfrowanie po stronie klienta i SSE zarówno służą do szyfrowania danych w usłudze Azure Storage. 

Podczas korzystania z szyfrowania po stronie klienta, aby szyfrować dane podczas przesyłania, (które również są przechowywane w zaszyfrowanej postaci w magazynie), warto używać protokołu HTTPS podczas transferu i mieć jakiś sposób, aby uzyskać dane, które mają być automatycznie szyfrowane, gdy jest on przechowywany. Istnieją dwa sposoby wykonania tej czynności — usługa Azure Disk Encryption i SSE. Jeden jest używany do bezpośrednio do szyfrowania danych na dyskach systemu operacyjnego i danych używane przez maszyny wirtualne, a drugi jest używany do szyfrowania danych zapisanych w usłudze Azure Blob Storage.

### <a name="storage-service-encryption-sse"></a>Szyfrowanie usługi Storage (SSE)

Funkcja SSE jest włączona dla wszystkich kont magazynu i nie może być wyłączony. Usługa SSE automatycznie szyfruje dane podczas zapisywania jej do usługi Azure Storage. Podczas odczytywania danych z usługi Azure Storage, zostaje odszyfrowywany przez usługę Azure Storage przed zwróceniem. Usługa SSE umożliwia Zabezpieczanie danych bez konieczności modyfikowania kodu ani dodać kod do dowolnej aplikacji.

Można użyć kluczy zarządzanych przez firmę Microsoft lub klucze niestandardowe. Firma Microsoft generuje klucze zarządzanych i obsługuje ich bezpiecznego magazynu, a także ich regularne obrotu, zgodnie z definicją, wewnętrzne zasady firmy Microsoft. Aby uzyskać więcej informacji na temat używania kluczy niestandardowych zobacz [szyfrowanie usługi Storage przy użyciu kluczy zarządzanych przez klienta w usłudze Azure Key Vault](storage-service-encryption-customer-managed-keys.md).

Usługa SSE automatycznie szyfruje dane we wszystkich warstwach wydajności (Standardowa i Premium), wszystkich modelach wdrażania (model usługi Azure Resource Manager i model klasyczny) oraz wszystkich usługach Azure Storage (Blob, Queue, Table i File). 

### <a name="client-side-encryption"></a>Szyfrowanie po stronie klienta
Wspomnieliśmy szyfrowania po stronie klienta podczas omawiania szyfrowanie danych podczas przesyłania. Ta funkcja umożliwia programowo szyfrować dane w aplikacji klienckiej przed wysłaniem ich faktycznie są zapisywane w usłudze Azure Storage i programowo odszyfrowywania danych po pobraniu go z usługi Azure Storage.

To zapewnia szyfrowanie podczas transferu, ale udostępnia również funkcję szyfrowanie danych magazynowanych. Mimo, że dane są szyfrowane podczas przesyłania, nadal zaleca się przy użyciu protokołu HTTPS, aby móc korzystać z sprawdzania integralności danych wbudowane, które pomagają ograniczyć błędy sieci wpływających na integralności danych.

Przykładem gdzie tej opcji można użyć jest, jeśli masz aplikację internetową, która przechowuje obiekty BLOB i umożliwia pobranie obiektów blob, i chcesz, aby być tak samo bezpieczna, jak to możliwe, aplikacji i danych. W takim przypadku należy użyć szyfrowania po stronie klienta. Ruch między klientem a usługą Azure Blob zawiera zaszyfrowane zasób, a nikt nie mogą być interpretowane danych podczas przesyłania i odtworzenia go do prywatnej obiektów blob.

Szyfrowanie po stronie klienta jest wbudowana w języka Java i .NET biblioteki klienta magazynu, które z kolei używają klucza magazynu interfejsów API usługi Azure, dzięki czemu można zaimplementować. Proces szyfrowania i odszyfrowywania danych używa techniki koperty i przechowuje metadane używane przez szyfrowanie w każdym obiekcie magazynu. Na przykład w przypadku obiektów blob, przechowuje go w metadanych obiektu blob, natomiast w przypadku kolejek, dodanie go do każdego komunikatu w kolejce.

Sam szyfrowania można wygenerować i zarządzać kluczami szyfrowania. Można również użyć kluczy wygenerowany przez bibliotekę klienta usługi Azure Storage lub masz usługi Azure Key Vault, generowania kluczy. Można przechowywać klucze szyfrowania w magazynie kluczy w środowisku lokalnym lub można go przechowywać w usłudze Azure Key Vault. Usługa Azure Key Vault umożliwia udzielenie dostępu do kluczy tajnych w usłudze Azure Key Vault do konkretnych użytkowników przy użyciu usługi Azure Active Directory. Oznacza to, że nie osoba mogła odczytać usługi Azure Key Vault i pobrać klucze używane do szyfrowania po stronie klienta.

#### <a name="resources"></a>Zasoby
* [Szyfrowanie i odszyfrowywanie obiektów blob w Microsoft Azure Storage przy użyciu usługi Azure Key Vault](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)

  W tym artykule przedstawiono sposób szyfrowania po stronie klienta za pomocą usługi Azure Key Vault, w tym sposobu tworzenia klucza KEK i zapisz go w magazynie przy użyciu programu PowerShell.
* [Szyfrowanie po stronie klienta i usługi Azure Key Vault dla usługi Microsoft Azure Storage](../storage-client-side-encryption.md)

  Ten artykuł zawiera wyjaśnienie szyfrowania po stronie klienta i zawiera przykłady przy użyciu biblioteki klienta usługi storage do szyfrowania i odszyfrowywania zasobów z czterech usług magazynu. Również opowiada o usłudze Azure Key Vault.

### <a name="using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines"></a>Do szyfrowania dysków używanych przez maszyny wirtualne przy użyciu usługi Azure Disk Encryption
Usługa Azure Disk Encryption umożliwia szyfrowanie dysków systemu operacyjnego i dysków danych używanych przez maszynę wirtualną IaaS. Dla Windows dyski są szyfrowane przy użyciu technologii szyfrowania funkcją BitLocker będące standardami branżowymi. W przypadku systemu Linux dyski są szyfrowane przy użyciu technologii DM-Crypt. Ta lokalizacja jest zintegrowana z usługą Azure Key Vault umożliwia kontrolowanie i zarządzać kluczami szyfrowania dysków.

Rozwiązanie obsługuje następujące scenariusze dla maszyn wirtualnych IaaS, gdy są one włączone w systemie Microsoft Azure:

* Integracja z usługą Azure Key Vault
* W warstwie standardowa maszyny wirtualne: [A, D, DS, G, GS i tak dalej serię maszyn wirtualnych IaaS](https://azure.microsoft.com/pricing/details/virtual-machines/)
* Włączanie szyfrowania na Windows i maszyn wirtualnych IaaS z systemem Linux
* Wyłączenie szyfrowania systemu operacyjnego i danych dyski dla maszyn wirtualnych IaaS Windows
* Wyłączenie szyfrowania dla dysków z danymi dla maszyn wirtualnych IaaS z systemem Linux
* Włączanie szyfrowania na maszynach wirtualnych IaaS, które są uruchomione w systemie operacyjnym klienta Windows
* Włączanie szyfrowania dla woluminów przy użyciu ścieżki instalacji
* Włączanie szyfrowania na maszynach wirtualnych systemu Linux, które są skonfigurowane przy użyciu rozkładanie (RAID) przy użyciu mdadm
* Włączanie szyfrowania na maszynach wirtualnych z systemem Linux przy użyciu LVM dla dysków z danymi
* Włączanie szyfrowania na maszynach wirtualnych Windows, które są konfigurowane przy użyciu funkcji miejsca do magazynowania
* Wszystkich publicznych regionach platformy Azure są obsługiwane.

Rozwiązanie nie obsługuje następujących scenariuszy, funkcji i technologii w wersji:

* Warstwa podstawowa maszyn wirtualnych IaaS
* Wyłączenie szyfrowania na dysku systemu operacyjnego dla maszyn wirtualnych IaaS z systemem Linux
* Maszyny wirtualne IaaS, które są tworzone za pomocą klasycznego metodę tworzenia maszyny Wirtualnej
* Integracja z usługą zarządzania kluczami w środowisku lokalnym
* Usługa pliki systemu Azure (udostępnionego systemu plików), Network File System (NFS), dynamiczne woluminy i maszyn wirtualnych Windows, które są skonfigurowane przy użyciu systemów programowej macierzy RAID


> [!NOTE]
> Szyfrowanie dysków systemu operacyjnego Linux jest obecnie obsługiwany w poniższe dystrybucje systemu Linux: RHEL 7.2 CentOS 7.2n i Ubuntu 16.04.
>
>

Ta funkcja pozwala zagwarantować, że wszystkie dane na dyskach maszyn wirtualnych są szyfrowane w stanie spoczynku w usłudze Azure Storage.

#### <a name="resources"></a>Zasoby
* [Usługa Azure Disk Encryption for Windows i maszyn wirtualnych IaaS z systemem Linux](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)

### <a name="comparison-of-azure-disk-encryption-sse-and-client-side-encryption"></a>Porównanie usługi Azure Disk Encryption, SSE i szyfrowanie po stronie klienta

#### <a name="iaas-vms-and-their-vhd-files"></a>Maszyny wirtualne IaaS i ich pliki wirtualnego dysku twardego

W przypadku dysków danych używany przez maszyny wirtualne IaaS Azure Disk Encryption jest zalecane. Jeśli tworzysz Maszynę wirtualną z dyskami niezarządzanymi przy użyciu obrazu z witryny Azure Marketplace, platforma Azure przeprowadza [skrócona kopiowania](https://en.wikipedia.org/wiki/Object_copying) obrazu do magazynu konta w usłudze Azure Storage, a nie są szyfrowane, nawet jeśli masz włączoną SSE. Po tworzy maszynę Wirtualną i rozpoczyna się aktualizowanie obrazu, SSE rozpocznie się zaszyfrowanie danych. Z tego powodu najlepiej jest używać usługi Azure Disk Encryption na maszynach wirtualnych z dyskami niezarządzanymi utworzone z obrazów w portalu Azure Marketplace, jeśli chcesz, w pełni zaszyfrowane. Jeśli tworzysz Maszynę wirtualną z usługą Managed Disks, usługa SSE szyfruje wszystkie dane domyślnie przy użyciu platformy zarządzanych kluczy. 

W przypadku przeniesienia wstępnie zaszyfrowanej maszyny Wirtualnej na platformie Azure ze środowiska lokalnego, można przekazać kluczy szyfrowania w usłudze Azure Key Vault i kontynuować korzystanie z szyfrowania dla tej maszyny Wirtualnej, które były używane w środowisku lokalnym. Usługa Azure Disk Encryption jest włączona w celu obsługi tego scenariusza.

Jeśli masz niezaszyfrowane wirtualny dysk twardy ze środowiska lokalnego, możesz przekazać go do galerii jako obraz niestandardowy i aprowizować maszynę Wirtualną z niego. Jeśli możesz to zrobić przy użyciu szablonów usługi Resource Manager, możesz poprosić, aby włączyć usługi Azure Disk Encryption podczas rozruchu maszynę wirtualną.

W przypadku dodania dysku danych i zainstalować go na maszynie Wirtualnej, można włączyć usługi Azure Disk Encryption na tym dysku danych. Go spowoduje zaszyfrowanie dysku danych lokalnie najpierw, a następnie warstwy modelu wdrożenia klasycznego będzie wykonaj zapis z opóźnieniem magazynu, więc zawartość magazynu jest szyfrowana.

#### <a name="client-side-encryption"></a>Szyfrowania po stronie klienta
Szyfrowanie po stronie klienta jest najbezpieczniejszą metodą szyfruje Twoje dane ponieważ je szyfruje dane przed przesyłania.  Jednak wymagać, możesz dodać kod do aplikacji za pomocą usługi storage, która może chcesz zrobić. W takich przypadkach można użyć protokołu HTTPS do zabezpieczenia danych podczas przesyłania. Gdy dane osiągną usługi Azure Storage, są szyfrowane przez SSE.

Za pomocą szyfrowania po stronie klienta można zaszyfrować jednostki z tabeli, kolejki komunikatów i obiektów blob. 

Szyfrowanie po stronie klienta odbywa się wyłącznie przez aplikację. Jest to najbezpieczniejsza metoda, ale wymaga zmiany programowy do aplikacji i umieścić procesów zarządzania kluczami w miejscu. Użyj tego chcesz zapewnienia dodatkowego bezpieczeństwa podczas przesyłania, gdy chcesz, aby dane przechowywane na szyfrowanie.

Szyfrowanie po stronie klienta jest większe obciążenie na komputerze klienckim i muszą to uwzględniać w planów skalowalność, zwłaszcza, jeśli są szyfrowania i przesyłania dużych ilości danych.

#### <a name="storage-service-encryption-sse"></a>Szyfrowanie usługi Storage (SSE)

Funkcja SSE jest zarządzane przez usługę Azure Storage. Usługa SSE nie zapewnia bezpieczeństwo danych podczas przesyłania, ale szyfrowania danych, ponieważ jest ona zapisywana w usłudze Azure Storage. Usługa SSE nie wpływa na wydajność usługi Azure Storage.

Umożliwia ona szyfrowanie dowolnego typu danych na koncie magazynu przy użyciu SSE (blokowe obiekty BLOB, uzupełnialnych obiektów blob, stronicowe obiekty BLOB, tabeli danych, dane w kolejce i plików).

W przypadku archiwum lub biblioteka plików VHD, można użyć jako podstawy do tworzenia nowych maszyn wirtualnych, które można utworzyć nowe konto magazynu i następnie przekaż pliki wirtualnego dysku twardego do tego konta. Te pliki wirtualnego dysku twardego, będą szyfrowane przez usługę Azure Storage.

Jeśli masz usługi Azure Disk Encryption włączone dla dysków na maszynie wirtualnej, wszystkie nowo napisanych dane są szyfrowane zarówno SSE i usługi Azure Disk Encryption.

## <a name="storage-analytics"></a>Analityka magazynu
### <a name="using-storage-analytics-to-monitor-authorization-type"></a>Przy użyciu usługi Storage Analytics do monitorowania typ autoryzacji
Dla każdego konta magazynu można włączyć usługi Azure Storage Analytics wykonać rejestrowania i przechowywanie danych metryk. Jest to doskonałe narzędzie do użycia, gdy chcesz sprawdzić metryki wydajności konta magazynu lub muszą rozwiązywać problemy z kontem magazynu, ponieważ występują problemy z wydajnością.

Inny element danych, które można zobaczyć w dzienniki analiz magazynu to metodę uwierzytelniania używaną przez osobę podczas uzyskiwania dostępu do magazynu. Na przykład z usługą Blob Storage, widać użycie sygnaturę dostępu współdzielonego i klucze konta magazynu lub jeśli publiczny dostęp do obiektu blob.

Może to być przydatne, jeśli są ściśle ochrona dostępu do magazynu. Na przykład w usłudze Blob Storage można ustawić wszystkie kontenery na prywatną i zaimplementować korzystania z usługi sygnatury dostępu Współdzielonego w całej aplikacji. Następnie można sprawdzić dzienniki regularnie, aby zobaczyć, czy obiekty BLOB są dostępne przy użyciu kluczy konta magazynu, które mogą wskazywać naruszenia zabezpieczeń, czy obiekty BLOB były publiczne, ale nie powinny być.

#### <a name="what-do-the-logs-look-like"></a>Jak wyglądają dzienniki?
Po włączeniu metryki konta magazynu i logujesz się za pośrednictwem witryny Azure portal, analiza danych będą kończyć się szybko wzrosnąć. Rejestrowanie i metryki dla każdej z tych usług jest oddzielona; Rejestrowanie są zapisywane tylko w przypadku działania na tym koncie magazynu, gdy metryki będą rejestrowane co minutę, co godzinę lub codziennie, w zależności od tego, jak skonfigurować.

Dzienniki są przechowywane w blokowych obiektów blob w kontenerze o nazwie $logs na koncie magazynu. Ten kontener jest tworzony automatycznie, gdy jest włączona usługa Storage Analytics. Po utworzeniu tego kontenera nie można usunąć, mimo że można usunąć jej zawartość.

W kontenerze $logs znajduje się tam folder dla każdej usługi, a następnie istnieją podfoldery dla rok/miesiąc/dzień/godz. W obszarze godzinę dzienniki są numerowane. Jest to, jak będzie wyglądać struktura katalogów:

![Wyświetl pliki dziennika](./media/storage-security-guide/image1.png)

Każde żądanie do usługi Azure Storage jest rejestrowane. Oto migawki pliku dziennika, przedstawiający pierwsze kilka pól.

![Migawki pliku dziennika](./media/storage-security-guide/image2.png)

Widać, że umożliwia dzienniki śledzenia dowolnego rodzaju połączenia z kontem magazynu.

#### <a name="what-are-all-of-those-fields-for"></a>Co to są wszystkie te pola?
Brak artykułu na liście zasobów poniżej zawiera listę wiele pól w dziennikach i ich zastosowania. Oto lista pól w kolejności:

![Migawka pola w pliku dziennika](./media/storage-security-guide/image3.png)

Jesteśmy zainteresowani wpisy dla GetBlob i w jaki sposób użytkownik jest uprawniony, więc musimy Wyszukaj wpisy operacji typu "Get-Blob" i sprawdzić stan żądania (czwarty</sup> kolumny) i typ autoryzacji (ósmego</sup> kolumny).

Na przykład w pierwszej kilka wierszy na liście powyżej, stan żądania to "Powodzenie" i typ autoryzacji "uwierzytelnieniu". Oznacza to, że żądanie było autoryzowane przy użyciu klucza konta magazynu.

#### <a name="how-is-access-to-my-blobs-being-authorized"></a>W jaki sposób dostęp do obiektów blob, Moje autoryzowanego?
Mamy trzy przypadki, które jesteśmy zainteresowani.

1. Obiekt blob jest publiczny, a następnie odbywa się przy użyciu adresu URL bez podpisu dostępu współdzielonego. W takim przypadku stan żądania jest "AnonymousSuccess" i typ autoryzacji jest "anonimowy".

   1.0;2015-11-17T02:01:29.0488963Z;GetBlob;**AnonymousSuccess**;200;124;37;**anonymous**;;mystorage…
2. Obiekt blob jest prywatny i została użyta z sygnaturę dostępu współdzielonego. W takim przypadku stan żądania jest "SASSuccess" i typ autoryzacji jest "sygnatury dostępu współdzielonego".

   1.0;2015-11-16T18:30:05.6556115Z;GetBlob;**SASSuccess**;200;416;64;**sas**;;mystorage…
3. Obiekt blob jest prywatny, a klucz magazynu został użyty do niego dostęp. W tym przypadku jest stan żądania "**Powodzenie**"i typ autoryzacji jest"**uwierzytelniony**".

   1.0;2015-11-16T18:32:24.3174537Z;GetBlob;**Success**;206;59;22;**authenticated**;mystorage…

Aby przeglądać i analizować te dzienniki, można użyć programu Microsoft Message Analyzer. Obejmuje funkcje wyszukiwania i filtrowania. Na przykład możesz chcieć wyszukać wystąpienia GetBlob, aby zobaczyć, jeśli użycie wynosi, czego można oczekiwać, oznacza to, aby upewnić się, że ktoś nie uzyskuje dostęp do konta usługi storage niewłaściwie.

#### <a name="resources"></a>Zasoby
* [Analityka magazynu](../storage-analytics.md)

  W tym artykule przedstawiono omówienie procedury analityka magazynu oraz jak je włączyć.
* [Format dziennika analizy magazynu](https://msdn.microsoft.com/library/azure/hh343259.aspx)

  W tym artykule przedstawiono Format magazynu analizy dzienników i informacje dostępne pola tam, takie jak typ uwierzytelniania, które wskazuje typ uwierzytelniania dla żądania.
* [Monitorowanie konta magazynu w witrynie Azure portal](../storage-monitor-storage-account.md)

  W tym artykule przedstawiono sposób konfigurowania, monitorowania metryk i rejestrowania dla konta magazynu.
* [End-to-End Rozwiązywanie problemów przy użyciu metryk usługi Azure Storage i rejestrowania, narzędzia AzCopy i analizatora komunikatów](../storage-e2e-troubleshooting.md)

  W tym artykule zawiera informacje o Rozwiązywanie problemów przy użyciu analizy magazynu i pokazuje, jak używać programu Microsoft Message Analyzer.
* [Microsoft Message Analyzer operacyjnego przewodnik](https://technet.microsoft.com/library/jj649776.aspx)

  W tym artykule jest odwołanie do programu Microsoft Message Analyzer oraz zawiera łącza do samouczku szybkiego startu i Podsumowanie funkcji.

## <a name="cross-origin-resource-sharing-cors"></a>Współużytkowanie zasobów między źródłami (CORS)
### <a name="cross-domain-access-of-resources"></a>Dostęp z innych domen zasobów
Gdy przeglądarki sieci web uruchomiony w jednej domenie wykonuje żądania HTTP dla zasobu z innej domeny, jest to żądanie HTTP cross-origin. Na przykład strony HTML z contoso.com sprawia, że żądanie dotyczące jpeg w serwisie fabrikam.blob.core.windows.net. Ze względów bezpieczeństwa przeglądarek ograniczanie żądań HTTP cross-origin inicjowane przy użyciu tej skryptów, takich jak JavaScript. Oznacza to, że gdy kodu JavaScript na stronie sieci web w domenie contoso.com zażąda tego jpeg na fabrikam.blob.core.windows.net, przeglądarka nie pozwoli na żądanie.

Jaki to ma sposób korzystania z usługi Azure Storage Dobrze, jeśli będą przechowywane zasoby statyczne, takich jak pliki danych JSON lub XML w usłudze Blob Storage przy użyciu konta magazynu o nazwie firmy Fabrikam, domeny dla aktywami będzie fabrikam.blob.core.windows.net i aplikacji sieci web contoso.com nie będą mogli uzyskiwać do nich dostęp za pomocą JavaScript, ponieważ domen są różne. Jest to również wartość true, jeśli próbujesz wywołać jedną z usług Azure Storage — takich jak usługi Table Storage — które zwracają dane JSON do przetworzenia przez klienta JavaScript.

#### <a name="possible-solutions"></a>Możliwe rozwiązania
Jednym ze sposobów, aby rozwiązać ten problem jest przypisywanie domeny niestandardowej, takich jak "storage.contoso.com" do fabrikam.blob.core.windows.net. Problem polega na tym, że można przypisać tylko tej domeny niestandardowej do jednego konta magazynu. Co zrobić, jeśli zasoby są przechowywane na wielu kontach magazynu?

Innym sposobem, aby rozwiązać ten problem ma działać jako serwer proxy dla połączeń magazynu aplikacji sieci web. Oznacza to, jeśli plik jest przekazywany do usługi Blob Storage, aplikacji sieci web będzie albo zapisać go lokalnie, a następnie skopiuj go do magazynu obiektów Blob lub będzie odczytywać wszystkie do pamięci, a następnie zapisać go do magazynu obiektów Blob. Alternatywnie można zapisać aplikację internetową dedykowane (np. interfejsu API sieci Web), która przekazuje te pliki lokalnie i zapisuje je do magazynu obiektów Blob. W obu przypadkach należy konta dla tej funkcji, podczas określania skalowalność potrzebuje.

#### <a name="how-can-cors-help"></a>Jak może pomóc mechanizmu CORS
Usługa Azure Storage umożliwia włączanie mechanizmu CORS — Cross współużytkowanie zasobów. Dla każdego konta magazynu można określić domeny, które mogą uzyskać dostęp do zasobów, w ramach tego konta magazynu. Na przykład w naszym przypadku opisanych powyżej, możemy włączyć mechanizm CORS na koncie magazynu fabrikam.blob.core.windows.net i skonfigurować tak, aby zezwolić na dostęp do domeny contoso.com. Następnie contoso.com aplikacji sieci web można uzyskać dostęp do zasobów w fabrikam.blob.core.windows.net.

Jedno należy pamiętać, jest, że CORS zezwala na dostęp, ale nie zapewnia uwierzytelniania, która jest wymagana dla wszystkich dostęp publiczny zasobów magazynu. Oznacza to, że możesz tylko dostęp do obiektów blob, jeśli są one publicznego lub zawierać sygnaturę dostępu współdzielonego umożliwiając odpowiednich uprawnień. Tabele, kolejki i pliki nie mają publicznego dostępu i wymagają sygnatury dostępu Współdzielonego.

Domyślnie CORS jest wyłączona na wszystkie usługi. Przy użyciu interfejsu API REST lub biblioteka klienta magazynu do wywoływania jednej z metod, które można ustawić zasady dotyczące usług, można włączyć mechanizm CORS. Po wykonaniu tej czynności, możesz dołączyć regułę CORS, który jest w formacie XML. Oto przykład reguły CORS, która została ustawiona za pomocą operacji ustawiania właściwości usługi dla usługi Blob dla konta magazynu. Można wykonać tej operacji przy użyciu biblioteki klienta usługi storage lub interfejsów API REST dla usługi Azure Storage.

```xml
<Cors>    
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
        <AllowedMethods>PUT,GET</AllowedMethods>
        <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
        <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
        <MaxAgeInSeconds>200</MaxAgeInSeconds>
    </CorsRule>
<Cors>
```

Oto, co oznacza każdy wiersz:

* **AllowedOrigins** informuje domen niezgodny może żądać i odbierać dane z usługi storage. Mówi, że domeny contoso.com i fabrikam.com można zażądać danych z magazynu obiektów Blob dla konta określonego magazynu. Można również ustawić to do symbolu wieloznacznego (\*) aby zezwolić na wszystkie domeny do żądań dostępu.
* **AllowedMethods** jest lista metody (polecenia żądań protokołu HTTP), które mogą być używane podczas wykonywania żądania. W tym przykładzie są dozwolone tylko PUT i GET. Można ustawić tego symbolu wieloznacznego (\*) zezwalać na wszystkie metody, które ma być używany.
* **AllowedHeaders** to nagłówki żądania, które można określić domenę pochodzenia, podczas wykonywania żądania. W tym przykładzie wszystkie nagłówki metadanych, począwszy od x-ms-meta-data x-ms-meta-target i x-ms-meta-abc są dozwolone. Symbol wieloznaczny (\*) wskazuje, czy jest dozwolone dowolnego nagłówka rozpoczynający się od określonego prefiksu.
* **ExposedHeaders** informuje nagłówki odpowiedzi, które powinny zostać ujawnione przez przeglądarkę do wydawcy żądania. W tym przykładzie dowolny nagłówek, rozpoczynając od "x-ms - meta-" zostaną ujawnione.
* **Atrybut MaxAgeInSeconds** to maksymalną ilość czasu, przeglądarki zostanie buforowania żądania wstępnego OPTIONS. (Aby uzyskać więcej informacji na temat żądania wstępnego Sprawdź poniższy artykuł pierwszy).

#### <a name="resources"></a>Zasoby
Aby uzyskać więcej informacji na temat mechanizmu CORS i jak go włączyć zapoznaj się z tymi zasobami.

* [Cross-Origin Resource Sharing (CORS) obsługę usług Azure Storage w witrynie Azure.com](../storage-cors-support.md)

  Ten artykuł zawiera omówienie mechanizmu CORS i jak ustawić zasady dotyczące usług innego magazynu.
* [Cross-Origin Resource Sharing (CORS) obsługę usług Azure Storage w witrynie MSDN](https://msdn.microsoft.com/library/azure/dn535601.aspx)

  Jest to dokumentacja obsługę mechanizmu CORS dla usług Azure Storage. Zawiera łącza do artykułów, stosowania do poszczególnych usług magazynu i przedstawia przykład i opisano każdy element w pliku CORS.
* [Microsoft Azure Storage: Wprowadzenie do mechanizmu CORS](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/02/03/windows-azure-storage-introducing-cors.aspx)

  To łącze do artykuł z bloga początkowej ogłoszenie CORS wyświetlanie i jak z niej korzystać.

## <a name="frequently-asked-questions-about-azure-storage-security"></a>Często zadawane pytania dotyczące zabezpieczeń usługi Azure Storage
1. **Jak mogę sprawdzić integralność obiektów blob, które mogę jestem przesyłania do i z usługi Azure Storage, jeśli nie mogę używać protokołu HTTPS**

   Jakiegokolwiek powodu, czego potrzebujesz do obsługi protokołu HTTP zamiast HTTPS i pracy z blokowymi obiektami blob, umożliwia sprawdzanie MD5 ułatwiają sprawdzanie integralności przesyłanych obiektów blob. Ułatwi to ochronę przed błędami warstwy transportu sieciowego /, ale niekoniecznie pośrednie ataków.

   Jeśli używasz protokołu HTTPS, który zapewnia zabezpieczenia na poziomie transportu, a następnie użyć sprawdzanie MD5 jest nadmiarowe i niepotrzebne.

   Aby uzyskać więcej informacji, zapoznaj się z [Omówienie usługi Azure Blob MD5](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/18/windows-azure-blob-md5-overview.aspx).
2. **Informacje o zgodności ze standardem FIPS dla Stanów Zjednoczonych Government?**

   Stany Zjednoczone informacji przetwarzania Standard FIPS (Federal) definiuje zatwierdzone do użycia przez amerykański algorytmów kryptograficznych Systemów komputerowych rządu federalnego ochrony poufnych danych. Włączanie standardu FIPS tryb na serwerze Windows lub pulpitu informuje system operacyjny powinna służyć tylko standardem FIPS algorytmów kryptograficznych. Jeśli aplikacja używa algorytmów niezgodnych, spowoduje awarię aplikacji. With.NET Framework w wersji 4.5.2 lub nowszej, aplikacja automatycznie przełącza się algorytmy kryptograficzne, aby użyć algorytmów zgodnych ze standardem FIPS, gdy komputer jest w trybie FIPS.

   Microsoft pozostawi je do każdego klienta w celu podjęcie decyzji o włączeniu w trybie FIPS. Wierzymy, że nie ma istotnych dla klientów, którzy nie są podlegających regulacjom rządowym w celu włączenia trybu FIPS domyślnie.

### <a name="resources"></a>Zasoby
* [Dlaczego one nie zalecamy "W trybie FIPS" już](https://blogs.technet.microsoft.com/secguide/2014/04/07/why-were-not-recommending-fips-mode-anymore/)

  Ten artykuł w blogu zawiera omówienie FIPS i wyjaśnia, dlaczego nie umożliwiają trybu FIPS domyślnie.
* [Standard FIPS 140 sprawdzania poprawności](https://technet.microsoft.com/library/cc750357.aspx)

  Ten artykuł zawiera informacje dotyczące sposobu modułów kryptograficznych i produktów firmy Microsoft są zgodne ze standardem FIPS dla Stanów Zjednoczonych Rządu federalnego.
* ["Kryptografia systemu: Użyj zgodnych algorytmów FIPS dla celów szyfrowania, mieszania i podpisywania"w Windows XP i nowszych wersjach systemu Windows efekty ustawienia zabezpieczeń](https://support.microsoft.com/kb/811833)

  Ten artykuł zawiera informacje o trybu FIPS w starszych komputerów Windows.
