---
title: Przewodnik po zabezpieczeniach magazynu platformy Azure | Dokumentacja firmy Microsoft
description: Szczegóły wiele metod zabezpieczania usługi Azure Storage, w tym między innymi RBAC, szyfrowanie usługi Magazyn szyfrowania po stronie klienta, SMB 3.0 i szyfrowania dysków Azure.
services: storage
author: craigshoemaker
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/06/2018
ms.author: cshoe
ms.openlocfilehash: 2debe3bb06f00741100883ff7c9978f11f5be799
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="azure-storage-security-guide"></a>Przewodnik po zabezpieczeniach magazynu Azure

## <a name="overview"></a>Przegląd

Magazyn Azure oferuje rozbudowany zestaw funkcji zabezpieczeń, które razem umożliwiają deweloperom tworzenie bezpiecznych aplikacji:

- Wszystkie dane zapisane w magazynie Azure automatycznie jest szyfrowana przy użyciu [szyfrowanie usługi Magazyn (SSE)](storage-service-encryption.md). Aby uzyskać więcej informacji, zobacz [Announcing domyślne szyfrowanie dla obiektów blob Azure, plików, tabeli i magazynu kolejek](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).
- Samo konto magazynu mogą być chronione przy użyciu kontroli dostępu opartej na rolach i Azure Active Directory. 
- Dane mogą być chronione przy użyciu przesyłanych między aplikacją a Azure [szyfrowania po stronie klienta](../storage-client-side-encryption.md), HTTPS lub SMB 3.0.  
- Systemu operacyjnego i dysków z danymi używanych przez maszyny wirtualne Azure mogą być szyfrowane przy użyciu [szyfrowania dysków Azure](../../security/azure-security-disk-encryption.md). 
- Delegowany dostęp do obiektów danych w usłudze Azure Storage można otrzymać za pomocą [sygnatury dostępu współdzielonego](../storage-dotnet-shared-access-signature-part-1.md).

Ten artykuł zawiera omówienie każdego z tych funkcji zabezpieczeń, które mogą być używane z usługą Azure Storage. Łącza są dostarczane do artykułów, które zapewni szczegóły dotyczące każdej funkcji, można w prosty sposób dalszych badań na każdego tematu.

Tematy, które mają być uwzględnione w tym artykule są:

* [Zabezpieczenia płaszczyzny Management](#management-plane-security) — zabezpieczanie konta magazynu

  Płaszczyzny zarządzania składa się z zasobami umożliwiają zarządzanie kontem magazynu. W tej sekcji omówiono modelu wdrażania usługi Azure Resource Manager oraz sposób korzystania z kontroli dostępu opartej na rolach (RBAC) do kontrolowania dostępu do kont magazynu. Rozwiązuje ona również zarządzanie kluczami konta magazynu i jak można ponownie wygenerować je.
* [Dane płaszczyzny zabezpieczeń](#data-plane-security) — zabezpieczanie dostępu do danych

  W tej sekcji wyjaśniono zezwalania na dostęp do danych rzeczywistych obiektów na koncie magazynu obiektów blob, plików, kolejek i tabel, np. przy użyciu sygnatury dostępu współdzielonego i przechowywane zasad dostępu. Omówimy SAS poziomu usług i poziomie konta sygnatury dostępu Współdzielonego. Firma Microsoft będzie również sprawdzić, jak ograniczyć dostęp do określonego adresu IP (lub zakres adresów IP), jak ograniczyć protokół używany do HTTPS i jak odwołać sygnaturę dostępu współdzielonego bez oczekiwania na jej wygaśnięcie.
* [Szyfrowanie podczas transferu](#encryption-in-transit)

  W tej sekcji omówiono sposób do zabezpieczania danych podczas transferu do lub z usługi Azure Storage. Będzie omawianiu zalecane użycie protokołu HTTPS i szyfrowania używany przez protokół SMB 3.0 do udziały plików platformy Azure. Firma Microsoft będzie także Spójrz na szyfrowanie po stronie klienta, co umożliwia szyfrowanie danych, zanim zostanie przekazany do magazynu w aplikacji klienckiej oraz do odszyfrowania danych po przeniesieniu poza magazynu.
* [Szyfrowanie w spoczynku](#encryption-at-rest)

  Firma Microsoft będzie się komunikował o magazynu usługi szyfrowania (SSE), który teraz jest automatycznie włączona dla magazynu nowych i istniejących kont. Ponadto przedstawiono sposób użycia szyfrowania dysków Azure i poznać różnice podstawowych i przypadków szyfrowania dysku i SSE i szyfrowania po stronie klienta. Krótko przedstawiono zgodności ze standardem FIPS dla Stanów Zjednoczonych Komputery dla instytucji rządowych.
* Przy użyciu [analityka magazynu](#storage-analytics) inspekcji dostępu do magazynu Azure

  W tej sekcji omówiono informacje można znaleźć w dziennikach analityka magazynu dla żądania. Firma Microsoft będzie Spójrz na analityka magazynu rzeczywiste dane dziennika i zobacz, jak do wykrycia, czy żądanie kluczem konta magazynu, za pomocą podpisu dostęp współdzielony lub anonimowo oraz tego, czy powodzeniem lub niepowodzeniem.
* [Włączanie przeglądarki klientów przy użyciu mechanizmu CORS](#Cross-Origin-Resource-Sharing-CORS)

  Ta sekcja zawiera informacje o sposobu zezwalania współużytkowanie zasobów między źródłami (CORS) do udostępniania. Będzie omawianiu międzydomenowy dostęp i sposobie jego obsługa z CORS wbudowanych funkcji na magazyn Azure.

## <a name="management-plane-security"></a>Zarządzanie płaszczyzny zabezpieczeń
Płaszczyzny zarządzania składa się z operacji, które mają wpływ na samo konto magazynu. Na przykład można utworzyć lub usuwania konta magazynu, Pobierz listę kont magazynu w ramach subskrypcji, pobrać klucze konta magazynu lub ponownie wygenerować kluczy konta magazynu.

Podczas tworzenia nowego konta magazynu jest wybierz model wdrożenia klasycznego lub Menedżera zasobów. Klasycznego modelu tworzenie zasobów na platformie Azure umożliwia tylko all-or-nothing dostępu do subskrypcji, a więc konta magazynu.

Ten przewodnik koncentruje się na modelu Resource Manager, który jest zalecany sposób tworzenia kont magazynu. Za pomocą Menedżera zasobów konta magazynu, zamiast dające dostęp do całej subskrypcji można kontrolować dostęp na poziomie bardziej ograniczone do płaszczyzny zarządzania za pomocą kontroli dostępu opartej na rolach (RBAC).

### <a name="how-to-secure-your-storage-account-with-role-based-access-control-rbac"></a>Jak zabezpieczyć konto magazynu z kontroli dostępu opartej na rolach (RBAC)
Załóżmy porozmawiać na temat RBAC jest i jak można go użyć. Każda subskrypcja platformy Azure zawiera usługę Azure Active Directory. Użytkownicy, grupy i aplikacje z katalogu może otrzymać dostęp do zarządzania zasobami w subskrypcji platformy Azure, które używają modelu wdrażania usługi Resource Manager. Ten typ zabezpieczeń jest określana jako kontroli dostępu opartej na rolach (RBAC). Aby zarządzać dostępem, można użyć [portalu Azure](https://portal.azure.com/), [narzędzia wiersza polecenia platformy Azure](../../cli-install-nodejs.md), [PowerShell](/powershell/azureps-cmdlets-docs), lub [interfejsów API REST dostawcy zasobów magazynu Azure](https://msdn.microsoft.com/library/azure/mt163683.aspx).

W modelu Resource Manager należy umieścić konta magazynu w zasobów grupy i kontroli dostępu do tego konta określonego magazynu przy użyciu usługi Azure Active Directory płaszczyzny zarządzania. Na przykład można udzielić określonym użytkownikom możliwość dostępu klucze konta magazynu, podczas gdy inni użytkownicy mogą wyświetlać informacje o koncie magazynu, ale nie ma dostępu do kluczy konta magazynu.

#### <a name="granting-access"></a>Udzielanie dostępu
Dostęp przez przypisanie odpowiednie role RBAC dla użytkowników, grup i aplikacji, w zakresie prawo. Aby udzielić dostępu do całej subskrypcji, możesz przypisać rolę na poziomie subskrypcji. Dostęp do wszystkich zasobów w grupie zasobów można przyznać za udzielanie uprawnień grupy zasobów. Można również przypisać określonych ról do określonych zasobów, takich jak konta magazynu.

Poniżej przedstawiono główne punkty, które musisz wiedzieć o dostęp do operacji zarządzania konta usługi Azure Storage za pomocą RBAC:

* Po przypisaniu dostępu zasadniczo przypisać rolę do konta, które chcesz mieć dostęp. Można kontrolować dostęp do operacji umożliwia zarządzanie tym kontem magazynu, ale nie do obiektów danych w ramach konta. Na przykład można udzielić uprawnienia do pobierania właściwości konta magazynu (na przykład nadmiarowość), ale nie do kontenera lub dane w kontenerze wewnątrz magazynu obiektów Blob.
* Osoba, która ma uprawnienia dostępu do danych obiektów na koncie magazynu można nadać im uprawnienia do odczytu klucze konta magazynu, a ten użytkownik mógł następnie użyć tych kluczy można uzyskać dostępu do obiektów blob, kolejek, tabel i plików.
* Role można przypisać do określonego konta użytkownika, grupy użytkowników lub do określonej aplikacji.
* Każda rola ma listę działania i nie działania. Na przykład Rola współautora maszyny wirtualnej ma akcję "listKeys", która umożliwia odczyt kluczy konta magazynu. Współautor ma "Nie akcje" jak aktualizowanie dostępu dla użytkowników w usłudze Active Directory.
* Role dla magazynu obejmują (ale nie są ograniczone do) następujące role:

  * Właściciel — mogą zarządzać wszystkim łącznie z dostępem.
  * Współautor — Administratorzy mogą wykonywać żadnych czynności właściciela oprócz przypisywanie dostępu. Ktoś z tą rolą mogą wyświetlać i ponownie wygenerować kluczy konta magazynu. Klucze konta magazynu ich umożliwia dostęp do obiektów danych.
  * Czytnik — mogą wyświetlać informacje o koncie magazynu, z wyjątkiem kluczy tajnych. Na przykład jeśli zostaną przypisane roli z uprawnieniami czytnika na koncie magazynu, mogą wyświetlać właściwości konta magazynu, ale nie mogą wprowadzać żadnych zmian właściwości lub Wyświetl klucze konta magazynu.
  * Współautor konta magazynu — mogą zarządzać konta magazynu — może odczytywać subskrypcji grup zasobów i zasobów, tworzenie i zarządzanie wdrożeniami grup zasobów subskrypcji. Można także przejść klucze konta magazynu, które z kolei oznacza, że będą mieć dostępu do warstwy danych.
  * Administrator dostępu użytkowników — one zarządzanie dostępem użytkowników do konta magazynu. Na przykład można przyznają dostęp czytelnika do określonego użytkownika.
  * Współautor maszyny wirtualnej — mogą zarządzać maszyn wirtualnych, ale nie na koncie magazynu, do którego jest podłączony. Tę rolę można wyświetlić listę kluczy konta magazynu, co oznacza, że użytkownik, któremu można przypisać tej roli można aktualizować płaszczyzna danych.

    Użytkownika, aby utworzyć maszynę wirtualną, muszą mieć możliwość utworzenia odpowiedniego pliku VHD na koncie magazynu. W tym celu muszą być w stanie pobrać klucz konta magazynu i przekaż go do interfejsu API tworzenia maszyny Wirtualnej. W związku z tym muszą mieć te uprawnienia, można wyświetlić listę kluczy konta magazynu.
* Możliwość definiowania ról niestandardowych jest funkcją, która umożliwia utworzenie zestaw akcji z listy dostępnych akcji, które mogą być wykonywane na zasobów platformy Azure.
* Aby można było przypisać rolę do nich można skonfigurować w usłudze Azure Active Directory użytkownika.
* Można utworzyć raport, który przyznane/odwołany jakiego rodzaju dostępu do i z którego i na jakie zakresu przy użyciu programu PowerShell lub interfejsu wiersza polecenia Azure.

#### <a name="resources"></a>Zasoby
* [Kontrola dostępu oparta na rolach w usłudze Azure Active Directory](../../role-based-access-control/role-assignments-portal.md)

  W tym artykule objaśniono funkcję kontroli dostępu opartej na rolach w usłudze Azure Active Directory i sposób jej działania.
* [Kontrola dostępu oparta na rolach (RBAC): wbudowane role](../../role-based-access-control/built-in-roles.md)

  Ten artykuł zawiera szczegóły dotyczące wszystkich dostępnych w RBAC ról wbudowanych.
* [Omówienie wdrażania przy użyciu usługi Resource Manager oraz wdrażania klasycznego](../../azure-resource-manager/resource-manager-deployment-model.md)

  W tym artykule opisano wdrożenie usługi Resource Manager i klasycznych modeli wdrażania, a opis korzyści przy użyciu grup Resource Manager i zasobów. Wyjaśniono, jak działają rozwiązań usługi obliczenia Azure, sieci i dostawcy magazynu w modelu Resource Manager.
* [Zarządzanie kontrolą dostępu opartą na rolach za pomocą interfejsu API REST](../../role-based-access-control/role-assignments-rest.md)

  W tym artykule przedstawiono sposób zarządzania kontrolą dostępu opartą na rolach (RBAC) za pomocą interfejsu API REST.
* [Dokumentacja interfejsu API REST dostawcy zasobów magazynu Azure](https://msdn.microsoft.com/library/azure/mt163683.aspx)

  Ta dokumentacja interfejsu API opisano interfejsów API, można programowo zarządzać konta magazynu.
* [Przewodnik dewelopera do uwierzytelniania z interfejsu API usługi Azure Resource Manager](http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/)

  W tym artykule przedstawiono sposób uwierzytelniania przy użyciu interfejsów API Menedżera zasobów.
* [Kontrola dostępu oparta na rolach dla platformy Microsoft Azure — konferencja Ignite](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

  To jest link do filmu wideo w witrynie Channel 9 z konferencji Microsoft Ignite 2015. W tej sesji rozmawiamy o możliwościach zarządzania dostępem i raportowania na platformie Azure i eksplorujemy najlepsze rozwiązania dotyczące zabezpieczania dostępu do subskrypcji Azure za pomocą usługi Azure Active Directory.

### <a name="managing-your-storage-account-keys"></a>Zarządzanie kluczami konta magazynu
Klucze konta magazynu są ciągami 512-bitowe utworzony przez platformy Azure, która wraz z nazwy konta magazynu może służyć do dostępu do obiektów danych przechowywanych w ramach konta magazynu, na przykład, obiekty BLOB, jednostek w tabeli, kolejki komunikatów i plików w udziale plików Azure. Kontrolowanie dostępu do magazynu konta klucze kontroli dostępu do płaszczyzna danych dla tego konta magazynu.

Każde konto magazynu ma dwa klucze określane jako "Klucz 1" i "Klucz 2" w [portalu Azure](http://portal.azure.com/) i polecenia cmdlet programu PowerShell. Te mogą zostać wygenerowane ponownie ręcznie przy użyciu jednej z kilku metod, w tym między innymi przy użyciu [portalu Azure](https://portal.azure.com/), programu PowerShell, interfejsu wiersza polecenia Azure albo programowo z użyciem biblioteki klienta usługi Storage platformy .NET lub interfejsu API REST usług magazynu Azure.

Istnieje wielu sytuacjach można ponownie wygenerować kluczy konta magazynu.

* Użytkownik może ponownie wygenerować ich regularnie ze względów bezpieczeństwa.
* Jeśli ktoś zarządzane hack do aplikacji i pobierania klucza, który zostało zapisane na stałe lub zapisany w pliku konfiguracji, zapewniając im dostęp do konta magazynu będzie ponownie wygenerować kluczy konta magazynu.
* Innym przypadku ponowne generowanie klucza jest Jeśli zespół używa aplikacji Eksploratora magazynu, który zachowuje klucz konta magazynu, a jeden z członków zespołu pozostawia. Aplikacja będzie nadal działać, udzieleniem im dostępu do konta magazynu po zrobi to ktoś inny. Jest to rzeczywiście głównej przyczyny, dla której one utworzone sygnatury dostępu współdzielonego poziomie konta — SAS poziomie konta można użyć zamiast przechowywanie kluczy dostępu w pliku konfiguracji.

#### <a name="key-regeneration-plan"></a>Ponowne generowanie klucza planu
Nie chcesz ponownie wygenerować klucz używanego bez niektóre planowania. Dostęp może można to zrobić, obcięty, do tego konta magazynu, który może spowodować przestoje głównych. Jest to, dlatego dostępne są dwa klucze. Należy ponownie wygenerować jeden klucz w czasie.

Wygenerować klucze, upewnij się, że masz listę wszystkich aplikacji, które są zależne od konta magazynu, a także innych usług używanych na platformie Azure. Na przykład jeśli używasz usługi Azure Media Services, które są zależne od konta magazynu musi resync klucze dostępu z usługą multimediów po ponownym wygenerowaniu klucza. Jeśli używasz dowolnej aplikacji, takich jak Eksplorator magazynu należy podać nowe klucze do tych aplikacji, jak również. Jeśli masz maszyny wirtualne, których pliki VHD są przechowywane na koncie magazynu, ich nie dotyczy ponowne generowanie kluczy konta magazynu.

Można ponownie wygenerować klucze w portalu Azure. Gdy klucze są generowane, one mieć mają być synchronizowane między usługi magazynu do 10 minut.

Jeśli wszystko jest gotowe, Oto ogólny proces opisujące, jak należy zmienić klucz. W takim przypadku założeniu jest obecnie używasz klucz 1, a użytkownik chce zmienić wszystkie informacje niezbędne do zamiast tego Użyj klucza 2.

1. Wygeneruj ponownie klucz 2, aby upewnić się, że jest bezpieczne. Można to zrobić w portalu Azure.
2. We wszystkich aplikacjach przechowywania klucza magazynu należy zmienić wartość klucza magazynu do użycia nowej wartości klucza 2. Testowanie i publikowanie aplikacji.
3. Po wszystkich aplikacji i usług są włączone i uruchomiony pomyślnie, należy ponownie wygenerować klucz 1. Dzięki temu, że każdy, komu nie wyraźnie mają nowy klucz zostanie nie ma dostępu do konta magazynu.

Jeśli obecnie używasz 2 klucza, można użyć tego samego procesu, ale odwrotnej nazwy kluczy.

Przez kilka dni, można migrować Zmiana użycia nowego klucza dla każdej aplikacji i publikowania. Po wykonaniu wszystkich z nich czynności należy wrócić do poprzedniej strony i ponownie wygenerować starego klucza, ale nie działa.

Innym rozwiązaniem jest umieszczenie klucz konta magazynu [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) jako klucz tajny i mieć aplikacji pobrać klucza z tego miejsca. Następnie po ponownie wygenerować klucz aktualizacji usługi Azure Key Vault, aplikacje nie będzie jej ponownego wdrożenia, ponieważ ich pobierze nowy klucz z magazynu kluczy Azure automatycznie. Zauważ, że masz aplikacji, trzeba go do odczytu klucza, lub można ją buforują w pamięci i w przypadku niepowodzenia podczas korzystania z niego, Pobierz klucz ponownie z usługi Azure Key Vault.

Również przy użyciu usługi Azure Key Vault dodaje kolejny poziom zabezpieczeń dla kluczy magazynu. Jeśli używasz tej metody, nigdy nie należy ustalony klucza magazynu w pliku konfiguracji, co spowoduje usunięcie tego ścieżek ktoś uzyskiwanie dostępu do kluczy bez odpowiedniego uprawnienia.

Inną zaletą używania usługi Azure Key Vault jest można też kontrolować dostęp do kluczy przy użyciu usługi Azure Active Directory. Oznacza to, że można udzielać dostępu do grupy aplikacji, które należy pobrać klucze z usługi Azure Key Vault i dowiedzieć się, że inne aplikacje nie będą mogli uzyskać dostęp do kluczy bez przyznania im uprawnień w szczególności.

Uwaga: zalecane jest tylko jeden z kluczy Użyj we wszystkich aplikacji, w tym samym czasie. Jeśli używasz klucz 1 w niektórych miejscach i 2 klucza w innych nie można obrócić klucze bez utraty dostępu do aplikacji.

#### <a name="resources"></a>Zasoby
* [Informacji o kontach magazynu Azure](storage-create-storage-account.md#regenerate-storage-access-keys)

  Ten artykuł zawiera omówienie kont magazynu i wyświetlanie, kopiowanie i ponowne generowanie kluczy dostępu do magazynu.
* [Dokumentacja interfejsu API REST dostawcy zasobów magazynu Azure](https://msdn.microsoft.com/library/mt163683.aspx)

  Ten artykuł zawiera łącza do artykułów na temat pobierania kluczy konta magazynu i Trwa ponowne generowanie kluczy konta magazynu dla konta platformy Azure przy użyciu interfejsu API REST. Uwaga: Jest to w przypadku kont magazynu Menedżera zasobów.
* [Operacje na kontach magazynu](https://msdn.microsoft.com/library/ee460790.aspx)

  W tym artykule w dokumentacji interfejsu API REST magazynu Service Manager zawiera łącza do określonych artykułów na pobieranie i Trwa ponowne generowanie kluczy konta magazynu przy użyciu interfejsu API REST. Uwaga: Jest to w przypadku kont magazynu Classic.
* [Koniec z zarządzanie kluczami — zarządzanie dostępem do danych usługi Azure Storage za pomocą usługi Azure AD](http://www.dushyantgill.com/blog/2015/04/26/say-goodbye-to-key-management-manage-access-to-azure-storage-data-using-azure-ad/)

  W tym artykule pokazano, jak używać usługi Active Directory do kontrolowania dostępu do kluczy magazynu Azure w usłudze Azure Key Vault. Widoczny jest również sposób zadanie usługi Automatyzacja Azure umożliwia ponowne generowanie kluczy co godzinę.

## <a name="data-plane-security"></a>Zabezpieczenia warstwy danych
Bezpieczeństwo płaszczyzna danych odwołuje się do metody używane do zabezpieczania obiektów danych przechowywanych w usłudze Azure Storage — obiekty BLOB, kolejek, tabel i plików. Firma Microsoft w tym samouczku metod do szyfrowania danych i zabezpieczeń podczas przesyłania danych, ale jak uzyskać temat kontrolowania dostępu do obiektów?

Istnieją dwie metody w celu autoryzowania dostępu do danych same obiekty. Obejmują one kontrolowanie dostępu do kluczy konta magazynu i przy użyciu sygnatury dostępu współdzielonego, aby udzielić dostępu do obiektów dane specyficzne dla określonego przedziału czasu.

Ponadto dla magazynu obiektów Blob, można zezwolić publiczny dostęp do obiektów blob ustawiając poziom dostępu dla kontenera, który zawiera obiekty BLOB w związku z tym. Jeśli ustawisz dostępu do kontenera obiektów Blob lub kontenera, umożliwia publiczny dostęp do odczytu obiektów blob w tym kontenerze. Oznacza to, że każdy użytkownik z adresem URL wskazującym na obiekt blob w tym kontenerze otworzyć go w przeglądarce bez przy użyciu sygnaturę dostępu współdzielonego lub posiadanie kluczy konta magazynu.

Oprócz ograniczania dostępu do autoryzacji, należy użyć [zapory i sieci wirtualne](storage-network-security.md) Aby ograniczyć dostęp do konta magazynu, na podstawie reguł w sieci.  Ta umożliwia podejście Odmów dostępu do publicznej ruchu internetowego i przyznać dostęp tylko do określonych sieciach wirtualnych platformy Azure lub publicznego Internetu zakresów adresów IP.


### <a name="storage-account-keys"></a>Klucze kont magazynu
Klucze konta magazynu są ciągami 512-bitowe utworzone przez platformę Azure, którego wraz z nazwy konta magazynu, można uzyskać dostęp do obiektów danych przechowywanych w ramach konta magazynu.

Można na przykład obiekty BLOB do odczytu, zapisu do kolejek, tworzenie tabel i modyfikowanie plików. Wiele z tych akcji mogą być wykonywane za pośrednictwem portalu Azure lub przy użyciu jednej z wielu aplikacji Eksploratora magazynu. Można również napisać kod do wykonania tych operacji za pomocą interfejsu API REST lub jednej z bibliotek klienckich magazynu.

Zgodnie z opisem w sekcji na [zabezpieczeń płaszczyzny zarządzania](#management-plane-security), dostęp do magazynu kluczy dla konta magazynu Classic można otrzymać, zapewniając dostęp do subskrypcji platformy Azure. Dostęp do magazynu kluczy dla konta magazynu przy użyciu modelu usługi Azure Resource Manager można sterować za pośrednictwem kontroli dostępu opartej na rolach (RBAC).

### <a name="how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies"></a>Jak delegować dostęp do obiektów na koncie przy użyciu sygnatury dostępu współdzielonego i przechowywane zasad dostępu
Sygnaturę dostępu współdzielonego jest ciąg zawierający token zabezpieczający, który można dołączyć do identyfikatora URI, który umożliwia delegowanie dostępu do magazynu obiektów i określić ograniczeń, takich jak uprawnienia i zakres dostępu daty/godziny.

Mogą udzielać dostępu do obiektów blob, kontenery wiadomości w kolejce, plików i tabele. W tabelach faktycznie można przyznać uprawnień dostępu zakresu jednostek w tabeli, określając zakresami kluczy partycji i wiersza do których mają użytkownik miał dostęp do. Na przykład, jeśli masz dane przechowywane z użyciem klucza partycji geograficzne stanu, możesz podać ktoś dostęp do tylko te dane, Polski.

W kolejnym przykładzie może udzielić aplikacji sieci web token sygnatury dostępu Współdzielonego, który umożliwia zapisywanie wpisów do kolejki i nadaj roboczy aplikacji roli tokenu sygnatury dostępu Współdzielonego, aby pobrać wiadomości z kolejki i przetwarzanie ich. Lub jednego klienta można nadać tokenu sygnatury dostępu Współdzielonego, można użyć w celu przekazania obrazów do kontenera w magazynie obiektów Blob i nadaj uprawnienia aplikacji sieci web do odczytu tych obrazów. W obu przypadkach jest separacji — każdej aplikacji można przydzielić tylko dostępu, które są wymagane w celu wykonywania swoich zadań. Jest to możliwe przy użyciu sygnatury dostępu współdzielonego.

#### <a name="why-you-want-to-use-shared-access-signatures"></a>Dlaczego chcesz użyć sygnatury dostępu współdzielonego
Dlaczego czy chcesz użyć SAS zamiast tylko nadawania klucz konta magazynu jest dużo łatwiejszy? Nadawania klucz konta magazynu jest podobne do udostępniania kluczy Królestwo Twojego magazynu. Udziela uprawnień pełny dostęp. Ktoś może użycie klawiszy i przekazywanie ich całej biblioteki utworów muzycznych na koncie magazynu. One można również zastąpić pliki wersjami zainfekowany wirusów lub kradzieży danych. Przekazywanie nieograniczony dostęp do konta magazynu to element, którego nie powinny być uwzględniane w niewielkim stopniu.

Z sygnatury dostępu współdzielonego można nadać klienta tylko uprawnień wymaganych przez ograniczony czas. Na przykład jeśli ktoś jest przekazywanie obiektu blob na koncie, można przyznać im dostęp do zapisu wystarczającego czasu do przekazania obiektu blob (w zależności od rozmiaru obiektu blob, oczywiście). A jeśli zmienisz zdanie, że dostęp można odwołać.

Ponadto można określić, że żądania przy użyciu sygnatury dostępu Współdzielonego są ograniczone do niektórych adresów IP lub zakres adresów IP zewnętrznego do platformy Azure. Możesz również wymagać, że żądania są wykonywane przy użyciu określonego protokołu (HTTPS lub HTTP/HTTPS). Oznacza to, jeśli chcesz zezwolić na ruch protokołu HTTPS, wymagany protokół HTTPS można ustawić tylko i ruchu HTTP zostanie zablokowana.

#### <a name="definition-of-a-shared-access-signature"></a>Definicja sygnatury dostępu współdzielonego
Sygnaturę dostępu współdzielonego to zestaw parametrów zapytania dołączone do adresu URL, wskazując zasobu

zawierające informacje o dozwolony dostęp do i czas, dla których dostęp jest dozwolony. Oto przykład; Ten identyfikator URI zapewnia dostęp do odczytu do obiektu blob na pięć minut. Uwaga SAS parametry zapytania musi być zakodowanych jako adres URL, takich jak 3A % dwukropka (:) lub % 20 spacją.

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

#### <a name="how-the-shared-access-signature-is-authenticated-by-the-azure-storage-service"></a>Jak sygnatura dostępu współdzielonego zostanie uwierzytelniony przez usługi Azure Storage
Gdy Usługa magazynu odbiera żądanie, przyjmuje parametry zapytania i tworzy podpis przy użyciu tej samej metody co program wywołujący. Porównuje dwa podpisów. Jeśli użytkownik wyrazi zgodę, usługa Magazyn można sprawdzić wersji usług magazynu, upewnij się, że jest prawidłowy, sprawdź, czy bieżąca data i godzina są w określonym przedziale, upewnij się, że dostęp zażądał odpowiada żądania itp.

Na przykład z naszych powyżej adresu URL, jeśli adres URL został wskazuje plik zamiast obiektu blob to żądanie nie powiedzie się, ponieważ określa ona, że sygnatura dostępu współdzielonego jest dla obiekt blob. Jeśli polecenie REST wywoływana nie można zaktualizować obiektu blob, będą się kończyć niepowodzeniem, ponieważ sygnatura dostępu współdzielonego Określa, czy jest dozwolony dostęp tylko do odczytu.

#### <a name="types-of-shared-access-signatures"></a>Rodzaje sygnatur dostępu współdzielonego
* SAS poziomu usług może służyć do dostępu do określonych zasobów na koncie magazynu. Niektóre przykłady są pobiera listę obiektów blob w kontenerze, pobieranie obiektu blob, aktualizowania jednostki w tabeli, dodawanie wiadomości do kolejki lub przekazywanie pliku do udziału plików.
* SAS poziomie konta można uzyskać dostępu do wszystkich elementów, które SAS poziomu usług może służyć do. Ponadto zapewnia opcje z zasobami, które nie są dozwolone z poziomu usługi sygnatury dostępu Współdzielonego, takie jak możliwość tworzenia kontenerów, tabel, kolejek i udziałów plików. Dostęp do wielu usług można także określić jednocześnie. Na przykład może być ktoś udzielić dostępu do obiektów blob i plików na Twoim koncie magazynu.

#### <a name="creating-a-sas-uri"></a>Tworzenie identyfikatora URI sygnatury dostępu Współdzielonego
1. Na żądanie, definiujący wszystkie parametry zapytania każdorazowo, można utworzyć identyfikatora URI.

   Takie podejście jest elastyczny, ale jeśli logiczne zestaw parametrów, które są podobne za każdym razem, za pomocą zasad dostępu przechowywany jest lepiej zrozumieć.
2. Można utworzyć zasady dostępu do przechowywanych dla całego kontenera, udziału plików, tabel lub kolejek. Można to podstawę dla identyfikatorów URI sygnatury dostępu Współdzielonego, można utworzyć. Łatwo można odwołać uprawnień na podstawie zasad dostępu przechowywane. Może mieć maksymalnie pięć zasady zdefiniowane dla każdego kontenera, kolejki, tabeli lub udziału plików.

   Na przykład jeśli zostały będą mieć wiele osób do odczytu obiektów blob w określonym kontenerze, można utworzyć przechowywane zasad dostępu, stwierdzający "zapewniają dostęp do odczytu" i inne ustawienia, które będą takie same zawsze. Następnie można utworzyć identyfikatora URI połączenia SAS za pomocą ustawień zasad dostępu przechowywane i określając Data/godzina wygaśnięcia. Dzięki temu jest, że nie trzeba określać wszystkich parametrów zapytania zawsze.

#### <a name="revocation"></a>Odwołania
Załóżmy, że naruszono bezpieczeństwo sieci SAS lub chcesz zmienić go z powodu zabezpieczeń firmy lub wymagania dotyczące zgodności z przepisami. Jak można odwołać dostęp do zasobów, przy użyciu tego skojarzenia zabezpieczeń? To zależy od sposobu tworzenia identyfikatora URI połączenia SAS.

Jeśli używasz ad hoc identyfikatory URI, masz trzy opcje. Może wystawiać tokeny sygnatury dostępu Współdzielonego z zasadami wygasania krótki i poczekaj na sygnatury dostępu Współdzielonego wygaśnie. Można zmienić lub usunąć zasób (przy założeniu, że token zostało ograniczone do pojedynczego obiektu). Klucze konta magazynu, można zmienić. Ta opcja ostatniego może mieć znaczący wpływ, w zależności od tego, jak wiele usług korzystają z tego konta magazynu i prawdopodobnie nie jest coś, co chcesz zrobić bez niektóre planowania.

Jeśli używasz sygnatury dostępu Współdzielonego uzyskane z zasad dostępu przechowywany, można usunąć dostęp, odwołując zasad dostępu przechowywany — Zmień go tak, aby już wygasł, albo usuń go całkowicie. Aktywne natychmiast i unieważnia co SAS utworzone za pomocą tego przechowywane zasad dostępu. Aktualizowania lub usuwania zasad dostępu przechowywane może tabela osób wpływ uzyskuje dostęp do tego kontenera określonego udziału plików lub kolejki przy użyciu sygnatury dostępu Współdzielonego, ale jeśli klienci są zapisywane, więc żądają nowe skojarzenia zabezpieczeń, gdy stary staje się nieprawidłowy, to będzie działać prawidłowo.

Ponieważ przy użyciu sygnatury dostępu Współdzielonego uzyskane z zasad dostępu do przechowywanych daje możliwość natychmiast odwołać tego SAS, jest zalecanym najlepszym rozwiązaniem jest zawsze używaj przechowywane zasad dostępu, gdy jest to możliwe.

#### <a name="resources"></a>Zasoby
Aby uzyskać szczegółowe informacje na temat używania sygnatur dostępu współdzielonego i przechowywane zasad dostępu, wraz z przykładami można znaleźć w następujących artykułach:

* Są to artykuły odwołania.

  * [Usługa SAS](https://msdn.microsoft.com/library/dn140256.aspx)

    Ten artykuł zawiera przykłady użycia SAS poziomu usług z obiektów blob, kolejki komunikatów, zakresy tabeli i plików.
  * [Utworzenie sygnatury dostępu Współdzielonego usługi](https://msdn.microsoft.com/library/dn140255.aspx)
  * [Utworzenie konta SAS](https://msdn.microsoft.com/library/mt584140.aspx)
* Są to samouczków dotyczących za pomocą biblioteki klienta .NET można utworzyć sygnatury dostępu współdzielonego i przechowywane zasad dostępu.

  * [Przy użyciu sygnatury dostępu współdzielonego (SAS)](../storage-dotnet-shared-access-signature-part-1.md)
  * [Udostępnione sygnatur dostępu, część 2: Tworzenie i sygnatury dostępu Współdzielonego za pomocą usługi Blob](../blobs/storage-dotnet-shared-access-signature-part-2.md)

    Ten artykuł zawiera opis modelu sygnatur dostępu Współdzielonego, przykłady sygnatury dostępu współdzielonego i zalecenia dotyczące najlepszych praktyk Użyj SAS. Opisano również jest odwołania uprawnienia przyznane.

* Authentication

  * [Uwierzytelnianie dla usług Azure Storage](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* Pierwsze kroki samouczka sygnatury dostępu współdzielonego

  * [Pierwsze kroki samouczka SAS](https://github.com/Azure-Samples/storage-dotnet-sas-getting-started)

## <a name="encryption-in-transit"></a>Szyfrowanie podczas przesyłania
### <a name="transport-level-encryption--using-https"></a>Szyfrowanie na poziomie transportu — przy użyciu protokołu HTTPS
Kolejny krok, które należy podjąć w celu zapewnienia bezpieczeństwa danych usługi Azure Storage jest szyfrowanie danych między klientem a usługą Azure Storage. Pierwszy zalecane jest zawsze używaj [HTTPS](https://en.wikipedia.org/wiki/HTTPS) protokołu, który zapewnia bezpieczną komunikację za pośrednictwem publicznej sieci Internet.

Aby bezpieczny kanał komunikacyjny, zawsze należy używać protokołu HTTPS podczas wywoływania interfejsów API REST lub uzyskiwanie dostępu do obiektów w magazynie. Ponadto **sygnatury dostępu współdzielonego**, które mogą służyć do delegować dostęp do obiektów usługi Azure Storage, obejmują opcję, aby określić, że mogą być używane tylko z protokołu HTTPS przy użyciu sygnatury dostępu współdzielonego, zapewniając każdy wysyłanie linków z tokenami SAS użyje odpowiedni protokół.

Można wymusić użycie protokołu HTTPS podczas wywoływania interfejsów API REST, aby uzyskać dostęp do obiektów na kontach magazynu przez włączenie [bezpieczny transfer wymagane](../storage-require-secure-transfer.md) dla konta magazynu. Połączenia przy użyciu protokołu HTTP będą przyjmowane, gdy ta opcja jest włączona.

### <a name="using-encryption-during-transit-with-azure-file-shares"></a>Szyfrowanie podczas przesyłania z udziałami plików Azure
Usługa pliki Azure obsługuje HTTPS przy użyciu interfejsu API REST, ale jest więcej powszechnie używany jako udział plików SMB dołączony do maszyny Wirtualnej. Protokół SMB 2.1 nie obsługuje szyfrowania, więc połączeń są dozwolone tylko w obrębie tego samego regionu platformy Azure. Jednak protokół SMB 3.0 obsługuje szyfrowanie i jest dostępna w systemie Windows Server 2012 R2, Windows 8, Windows 8.1 i Windows 10, dzięki czemu zarówno między region dostępu i dostęp na pulpicie.

Chociaż udziały plików platformy Azure mogą być używane w systemie Unix, klient protokołu SMB w systemie Linux nie obsługuje jeszcze szyfrowania, więc dostęp jest dozwolony tylko w obrębie regionu platformy Azure. Obsługa szyfrowania dla systemu Linux znajduje się na plan deweloperów Linux odpowiedzialny za funkcje protokołu SMB. Podczas dodawania szyfrowania, będziesz mieć możliwości samej do uzyskiwania dostępu do udziału plików Azure w systemie Linux, podobnie jak w przypadku systemu Windows.

Przez włączenie mogą wymusić użycie szyfrowania w usłudze Azure pliki [bezpieczny transfer wymagane](../storage-require-secure-transfer.md) dla konta magazynu. Jeśli przy użyciu interfejsów API REST, wymagany jest protokół HTTPs. Dla protokołu SMB tylko połączenia protokołu SMB, które obsługuje szyfrowanie połączenie zostanie nawiązane pomyślnie.

#### <a name="resources"></a>Zasoby
* [Usługa pliki Azure wprowadzenie](../files/storage-files-introduction.md)
* [Wprowadzenie do usługi pliki Azure w systemie Windows](../files/storage-how-to-use-files-windows.md)

  Ten artykuł zawiera omówienie udziały plików platformy Azure oraz jak zainstalować i używać ich w systemie Windows.

* [How to use Azure Files with Linux (Jak używać usługi Azure Files z systemem Linux)](../files/storage-how-to-use-files-linux.md)

  W tym artykule przedstawiono sposób instalacji udziału plików platformy Azure na pliki systemu i przekaż/Pobierz systemu Linux.

### <a name="using-client-side-encryption-to-secure-data-that-you-send-to-storage"></a>Za pomocą szyfrowania po stronie klienta w celu zabezpieczenia danych, który możesz wysłać do magazynu
Inną opcją, która pomaga zagwarantować, że dane są bezpieczne podczas ich przesyłania między aplikacją klienta i magazynu jest szyfrowanie po stronie klienta. Dane są szyfrowane przed przesyłane do usługi Azure Storage. Podczas pobierania danych z usługi Azure Storage, dane zostaną odszyfrowane po odebraniu po stronie klienta. Nawet jeśli dane są szyfrowane, przechodzi przez sieć, zalecamy również używać protokołu HTTPS, ponieważ ta kolumna ma wbudowane zmniejszenia którego błędy sieciowe wpływających na integralność danych, sprawdzania integralności danych.

Szyfrowanie po stronie klienta jest również szyfrowanie danych magazynowanych, ponieważ dane są przechowywane w postaci zaszyfrowanej. Będzie omawianiu to bardziej szczegółowo w sekcji na [szyfrowanie magazynowanych](#encryption-at-rest).

## <a name="encryption-at-rest"></a>Szyfrowanie magazynowanych
Istnieją trzy funkcje platformy Azure umożliwiających szyfrowanie przechowywanych. Szyfrowanie dysków Azure jest używany do szyfrowania dysków systemu operacyjnego i danych w maszynach wirtualnych IaaS. Szyfrowanie po stronie klienta i SSE jest używany zarówno do szyfrowania danych w usłudze Azure Storage. 

Podczas szyfrowania po stronie klienta można używać do szyfrowania danych podczas przesyłania (które są także przechowywane w postaci zaszyfrowanej w magazynie), można używać protokołu HTTPS podczas transferu, a niektóre sposób dla danych mają być szyfrowane automatycznie, gdy jest on przechowywany. Istnieją dwa sposoby w tym--szyfrowania dysków Azure i SSE. Jeden służy do bezpośredniego szyfrowania danych na dyskach systemu operacyjnego i danych używany przez maszyny wirtualne, a drugi jest używany do szyfrowania danych zapisywane do magazynu obiektów Blob Azure.

### <a name="storage-service-encryption-sse"></a>Szyfrowanie usługi Magazyn (SSE)

Nie można wyłączyć SSE, jest włączona dla wszystkich kont magazynu. SSE automatycznie szyfruje dane podczas zapisywania go do magazynu Azure. Gdy odczytać danych z usługi Azure Storage, przed zwróceniem jest odszyfrowywany przez Magazyn Azure. SSE umożliwia Zabezpieczanie danych bez konieczności modyfikowania kodu lub Dodaj kod, aby wszystkie aplikacje.

Klucze używane dla SSE są zarządzane przez firmę Microsoft. Firma Microsoft generuje klucze pierwotnie i zarządza ich bezpiecznego magazynu, a także regularne obracanie zdefiniowane przez wewnętrznych zasad firmy Microsoft. Zarządzany przez klienta klucze ostatecznie będą dostępne, wraz ze ścieżki migracji z kluczy zarządzany przez firmę Microsoft do kluczy zarządzany przez klienta.

Usługa SSE automatycznie szyfruje dane we wszystkich warstwach wydajności (Standardowa i Premium), wszystkich modelach wdrażania (model usługi Azure Resource Manager i model klasyczny) oraz wszystkich usługach Azure Storage (Blob, Queue, Table i File). 

### <a name="client-side-encryption"></a>Szyfrowanie po stronie klienta
Wspomniano szyfrowania po stronie klienta przy omawianiu szyfrowanie danych podczas przesyłania. Ta funkcja umożliwia programowo szyfrowania danych w aplikacji klienta przed wysłaniem przez sieć do zapisania do magazynu Azure i programowo odszyfrować danych po pobraniu go z magazynu Azure.

To zapewnia szyfrowanie podczas przesyłania, ale oferuje także funkcję do szyfrowania w stanie spoczynku. Mimo że dane są szyfrowane podczas przesyłania, nadal zaleca się przy użyciu protokołu HTTPS, aby móc korzystać z sprawdzania integralności danych wbudowanych, które pomagają ograniczyć błędy sieciowe wpływających na integralność danych.

Przykład gdzie tej opcji można użyć, to jeśli masz aplikację sieci web przechowuje obiekty BLOB i pobiera obiekty BLOB i ma być należycie zabezpieczone aplikacji i danych. W takim przypadku użyje szyfrowania po stronie klienta. Ruch między klientem a usługą Blob Azure zawiera zaszyfrowane zasobów, a nikt nie mogą interpretować dane przesyłane i przywróć ją do prywatnego obiektów blob.

Szyfrowanie po stronie klienta jest oparty na języku Java i biblioteki klienta magazynu .NET, które z kolei używają klucza magazynu interfejsów API usługi Azure, dzięki czemu można zapewnić. Proces szyfrowania i odszyfrowywania danych używa techniki koperty i są przechowywane metadane używane przez szyfrowanie w każdym obiekcie magazynu. Na przykład dla obiektów blob, przechowuje go w metadane obiektu blob, natomiast w przypadku kolejek, dodanie go do każdej kolejki wiadomości.

Sam szyfrowania można wygenerować i zarządzanie kluczami szyfrowania. Umożliwia także klucze generowane przez bibliotekę klienta usługi Azure Storage lub masz usługi Azure Key Vault generowania kluczy. Można przechowywać kluczy szyfrowania w Twoim magazynie kluczy lokalnymi lub można przechowywać w usłudze Azure Key Vault. Usługa Azure Key Vault umożliwia udzielenie dostępu do kluczy tajnych w magazynie kluczy Azure do konkretnych użytkowników przy użyciu usługi Azure Active Directory. Oznacza to, że nie tylko każdy może odczytywać usługi Azure Key Vault i pobieranie kluczy, którego używasz do szyfrowania po stronie klienta.

#### <a name="resources"></a>Zasoby
* [Szyfrowanie i odszyfrowywanie obiektów blob w magazynie platformy Microsoft Azure przy użyciu usługi Azure Key Vault](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)

  W tym artykule przedstawiono sposób szyfrowania po stronie klienta za pomocą usługi Azure Key Vault, łącznie ze sposobem tworzenia klucza KEK i zapisze go w magazynie przy użyciu programu PowerShell.
* [Magazyn kluczy szyfrowania po stronie klienta i Azure dla magazynu Microsoft Azure](../storage-client-side-encryption.md)

  Ten artykuł zawiera wyjaśnienie szyfrowania po stronie klienta i zawiera przykłady za pomocą biblioteki klienta magazynu do szyfrowania i odszyfrowywania zasobów z czterech usług magazynu. Zawiera ona również informacje o usługi Azure Key Vault.

### <a name="using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines"></a>Przy użyciu szyfrowania dysków Azure do szyfrowania dysków używanych przez maszyny wirtualne
Szyfrowanie dysków Azure to nowa funkcja. Ta funkcja umożliwia szyfrowanie dysków systemu operacyjnego i dysków danych używanych przez maszyny wirtualne IaaS. W systemie Windows dyski są szyfrowane za pomocą technologii szyfrowania BitLocker standardowych. Dla systemu Linux dyski są szyfrowane za pomocą technologii DM-Crypt. To jest zintegrowany z usługą Azure Key Vault, co pozwala na kontrolowanie i zarządzać kluczami szyfrowania dysku.

Rozwiązanie obsługuje następujące scenariusze dla maszyn wirtualnych IaaS, jeśli są włączone w systemie Microsoft Azure:

* Integracja z usługi Azure Key Vault
* Maszyny wirtualne warstwy standardowa: [A, D DS, G, GS i itp szeregów maszyn wirtualnych IaaS](https://azure.microsoft.com/pricing/details/virtual-machines/)
* Włączenie szyfrowania w systemach Windows i maszyn wirtualnych systemu Linux IaaS
* Wyłączenie szyfrowania systemu operacyjnego i danych dyski dla maszyn wirtualnych IaaS systemu Windows
* Wyłączenie szyfrowania dla dysków z danymi dla maszyn wirtualnych systemu Linux IaaS
* Włączenie szyfrowania na maszynach wirtualnych IaaS, którego uruchomiono system operacyjny klienta systemu Windows
* Włączenie szyfrowania na woluminach ze ścieżki instalacji
* Włączenie szyfrowania na maszynach wirtualnych systemu Linux, które są skonfigurowane przy użyciu rozkładanie (RAID) przy użyciu mdadm
* Włączenie szyfrowania na maszynach wirtualnych systemu Linux przy użyciu LVM dla dysków z danymi
* Włączenie szyfrowania na maszynach wirtualnych systemu Windows, które są skonfigurowane przy użyciu funkcji miejsca do magazynowania
* Wszystkie publiczne regiony platformy Azure są obsługiwane.

Rozwiązanie nie obsługuje następujące scenariusze, funkcje i technologie w wersji:

* Maszyny wirtualne IaaS warstwa podstawowa
* Wyłączenie szyfrowania na dysku systemu operacyjnego dla maszyn wirtualnych systemu Linux IaaS
* Maszyny wirtualne IaaS, które są tworzone za pomocą klasycznego metodę tworzenia maszyny Wirtualnej
* Integracja z lokalnej usługi zarządzania kluczami
* Usługa pliki Azure (udostępnionego systemu plików), sieciowego systemu plików (NFS), dynamiczne woluminy i maszyn wirtualnych systemu Windows, które są skonfigurowane przy użyciu systemów opartych na oprogramowaniu RAID


> [!NOTE]
> Szyfrowanie dysków systemu operacyjnego Linux jest aktualnie obsługiwana w następujących dystrybucje systemu Linux: RHEL 7.2, CentOS 7.2n i Ubuntu 16.04.
>
>

Ta funkcja zapewnia, że wszystkie dane na dyskach maszyny wirtualnej jest szyfrowane, gdy w usłudze Azure Storage.

#### <a name="resources"></a>Zasoby
* [Szyfrowanie dysków Azure dla systemu Windows i maszyn wirtualnych systemu Linux IaaS](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)

### <a name="comparison-of-azure-disk-encryption-sse-and-client-side-encryption"></a>Porównanie szyfrowania dysków Azure, SSE i szyfrowania po stronie klienta

#### <a name="iaas-vms-and-their-vhd-files"></a>Maszyny wirtualne IaaS i swoich plików wirtualnego dysku twardego

W przypadku dysków danych używany przez maszyny wirtualne IaaS szyfrowania dysków Azure jest zalecane. Jeśli tworzysz Maszynę wirtualną z dyskami niezarządzanych przy użyciu obrazu z portalu Azure Marketplace, platforma Azure stosuje [skrócona kopiowania](https://en.wikipedia.org/wiki/Object_copying) obrazu do magazynu konta w usłudze Azure Storage, a nie są szyfrowane, nawet jeśli masz SSE włączone. Po tworzy maszynę Wirtualną i uruchamia aktualizacji obrazu, SSE rozpocznie się zaszyfrowanie danych. Z tego powodu najlepiej jest używać szyfrowania dysków Azure na maszynach wirtualnych z dyskami niezarządzane utworzone z obrazów w portalu Azure Marketplace, jeśli chcesz, w pełni szyfrowane. Po utworzeniu maszyny Wirtualnej z dysków zarządzanych SSE szyfruje wszystkie dane domyślnie przy użyciu platformy zarządzanych kluczy. 

W przypadku przeniesienia zaszyfrowane wstępnie maszyny Wirtualnej na platformie Azure z lokalnymi, można przekazać do usługi Azure Key Vault kluczy szyfrowania i kontynuować korzystanie z szyfrowania dla tej maszyny Wirtualnej, aby były używane lokalnie. Szyfrowanie dysków Azure jest włączona do obsługi tego scenariusza.

Jeśli masz niezaszyfrowane wirtualnego dysku twardego z lokalnej, należy przekazać go do galerii jako obraz niestandardowy i udostępnić Maszynę wirtualną z niego. Jeśli możesz to zrobić za pomocą szablonów usługi Resource Manager, poproś go o włączenie szyfrowania dysków Azure po jego rozruchu maszyny Wirtualnej.

Podczas dodawania dysku danych i zainstalować go na maszynie Wirtualnej, można włączyć szyfrowania dysków Azure na tym dysku danych. Go zostanie najpierw szyfrowania dysku danych lokalnie, a następnie warstwy modelu wdrażania klasycznego będzie wykonaj opóźnieniem zapisu względem magazynu, zawartość magazynu jest zaszyfrowana.

#### <a name="client-side-encryption"></a>Szyfrowania po stronie klienta
Szyfrowanie po stronie klienta jest najbezpieczniejszą metodą szyfrowania danych, ponieważ szyfrowanie danych przed przesyłania.  Jednak wymaga Dodaj kod aplikacji przy użyciu magazynu, który może chcesz zrobić. W takim wypadku można użyć protokołu HTTPS do zabezpieczania danych przesyłanych. Po danych osiągnie magazynu Azure, są szyfrowane przez SSE.

Szyfrowanie po stronie klienta można zaszyfrować jednostek tabeli, kolejki komunikatów i obiekty BLOB. 

Szyfrowanie po stronie klienta odbywa się wyłącznie przez aplikację. Jest to najbezpieczniejsza metoda, ale wymagają zmian programowy do aplikacji i wprowadzenia procesy zarządzania kluczami. Należy użyć to dodatkowe bezpieczeństwo podczas przesyłania, i przechowywane dane do zaszyfrowania.

Szyfrowanie po stronie klienta jest większe obciążenie na kliencie, a muszą to uwzględniać w planów skalowalność, zwłaszcza, jeśli są szyfrowania i przesyłania dużych ilości danych.

#### <a name="storage-service-encryption-sse"></a>Szyfrowanie usługi Magazyn (SSE)

SSE jest zarządzana przez usługi Azure Storage. SSE nie zapewniają bezpieczeństwo danych podczas przesyłania, ale szyfrowania danych, ponieważ jest ona zapisywana w usłudze Azure Storage. Usługa SSE nie wpływa na wydajność usługi Azure Storage.

Można zaszyfrować każdego typu danych konta magazynu przy użyciu SSE (blokowe obiekty BLOB, Dołącz obiektów blob, stronicowe obiekty BLOB, tabeli danych, kolejki danych i plików).

Jeśli masz archiwum lub biblioteka plików VHD, które używa jako podstawy do tworzenia nowych maszyn wirtualnych, możesz utworzyć nowe konto magazynu i następnie przekazać pliki wirtualnego dysku twardego do tego konta. Te pliki VHD będą szyfrowane przez Magazyn Azure.

Jeśli masz dysków na maszynie wirtualnej jest włączone szyfrowanie dysków Azure, wszystkie nowo napisanych dane są szyfrowane, zarówno SSE i szyfrowania dysków Azure.

## <a name="storage-analytics"></a>Analityka magazynu
### <a name="using-storage-analytics-to-monitor-authorization-type"></a>Przy użyciu magazynu Analytics można monitorować typu autoryzacji
Dla każdego konta magazynu można włączyć analityka magazynu Azure do przechowywania danych metryki i wykonywać rejestrowanie. Jest to doskonałe narzędzie do użycia podczas sprawdzania metryki wydajności konta magazynu lub konieczne rozwiązywanie problemów z konta magazynu, ponieważ występują problemy z wydajnością.

Inny element danych, można znaleźć w dziennikach analityka magazynu jest metodę uwierzytelniania używaną przez osobę przy uzyskiwaniu dostępu do magazynu. Na przykład z magazynem obiektów Blob widać, użycie sygnaturę dostępu współdzielonego i klucze konta magazynu lub jeśli publiczny dostęp do obiektu blob.

Może to być przydatne, jeśli są ściśle ochrona dostępu do magazynu. Na przykład w magazynie obiektów Blob można ustawić opcję prywatne dla wszystkich kontenerów i implementować korzystania z usługi SAS w całej aplikacji. Następnie można sprawdzić dzienniki regularnie, aby sprawdzić, czy obiektów blob są dostępne przy użyciu kluczy konta magazynu, które mogą wskazywać naruszenia zabezpieczeń, lub jeśli obiekty BLOB są publiczne, ale nie powinny być one.

#### <a name="what-do-the-logs-look-like"></a>Jak wyglądają dzienniki?
Po włączeniu metryki konta magazynu i rejestrowanie za pośrednictwem portalu Azure, dane analityczne rozpocznie się szybko. Rejestrowanie i metryki dla każdej usługi jest oddzielona; Rejestrowanie są zapisywane tylko w przypadku działania na tym koncie magazynu, gdy metryki będą rejestrowane co minutę, co godzinę lub codziennie, w zależności od sposobu skonfigurowania.

Dzienniki są przechowywane w blokowych obiektów blob w kontenerze o nazwie $logs na koncie magazynu. Ten kontener jest tworzony automatycznie, gdy analityka magazynu jest włączona. Po utworzeniu tego kontenera nie można usunąć, mimo że można usunąć jego zawartość.

W kontenerze $logs znajduje się tam folder dla każdej usługi, a następnie istnieją podfoldery roku/miesiąc/dzień/godzinę. W obszarze godzinę dzienniki są numerowane. Jest to, jak będzie wyglądać strukturę katalogów:

![Wyświetl pliki dziennika](./media/storage-security-guide/image1.png)

Każde żądanie do usługi Azure Storage jest rejestrowane. Oto migawki pliku dziennika, przedstawiający pierwsze kilka pola.

![Migawki pliku dziennika](./media/storage-security-guide/image2.png)

Widać, że umożliwia dzienniki śledzenia dowolnego rodzaju wywołań konta magazynu.

#### <a name="what-are-all-of-those-fields-for"></a>Co to są wszystkich tych pól?
Brak wymienionym w zasobach poniżej zawiera listę wiele pól w dziennikach i ich używać. Oto lista pól w kolejności:

![Migawki pól w pliku dziennika](./media/storage-security-guide/image3.png)

Interesuje nas wpisy GetBlob i sposób ich uwierzytelniania, dlatego musimy Wyszukaj wpisy z operacji typu "Get-obiektu Blob" i sprawdź stan żądania (czwarty</sup> kolumny) i typ autoryzacji (ósmego</sup> kolumny).

Na przykład w kilka pierwszych wierszy na liście powyżej, stan żądania jest "Powodzenie" i typ autoryzacji "uwierzytelnieniu". Oznacza to, że żądania została zweryfikowana przy użyciu klucza konta magazynu.

#### <a name="how-are-my-blobs-being-authenticated"></a>Jak są jest uwierzytelniane Moje obiektów blob?
Mamy trzech przypadkach, w których Dbamy o.

1. Obiekt blob jest publiczny i jest on dostępny przy użyciu adresu URL bez sygnaturę dostępu współdzielonego. W takim przypadku stan żądania jest "AnonymousSuccess" i "anonymous" jest typ autoryzacji.

   1.0;2015-11-17T02:01:29.0488963Z;GetBlob;**AnonymousSuccess**;200;124;37;**anonymous**;;mystorage…
2. Obiekt blob jest prywatny i została użyta z sygnaturą dostępu współdzielonego. W takim przypadku stan żądania jest "SASSuccess" i "sas" jest typ autoryzacji.

   1.0;2015-11-16T18:30:05.6556115Z;GetBlob;**SASSuccess**;200;416;64;**sas**;;mystorage…
3. Obiekt blob jest prywatny i klucz magazynu został użyty do niego dostęp. W tym przypadku jest stan żądania "**Powodzenie**"i typ autoryzacji jest"**uwierzytelniony**".

   1.0;2015-11-16T18:32:24.3174537Z;GetBlob;**Success**;206;59;22;**authenticated**;mystorage…

Aby przeglądać i analizować te dzienniki, można użyć programu Microsoft Message Analyzer. Obejmuje on możliwości wyszukiwania i filtrowania. Na przykład można wyszukać wystąpienia GetBlob, aby zobaczyć, czy jest oczekiwań, oznacza to, aby upewnić się, ktoś jest nie dostęp do konta magazynu niewłaściwie.

#### <a name="resources"></a>Zasoby
* [Analityka magazynu](../storage-analytics.md)

  W tym artykule przedstawiono analityka magazynu oraz jak je włączyć.
* [Format dziennika analityka magazynu](https://msdn.microsoft.com/library/azure/hh343259.aspx)

  W tym artykule przedstawiono Format dziennika analityka magazynu i zawiera szczegóły dostępnych pól, tym — typ uwierzytelniania, który wskazuje typ uwierzytelniania dla żądania.
* [Monitor konta magazynu w portalu Azure](../storage-monitor-storage-account.md)

  W tym artykule przedstawiono sposób konfigurowania monitorowania metryki i logowania dla konta magazynu.
* [Rozwiązywanie problemów na trasie przy użyciu metryk usługi Azure Storage i rejestrowania, AzCopy i analizatora komunikatów](../storage-e2e-troubleshooting.md)

  Ten artykuł zawiera informacje o Rozwiązywanie problemów przy użyciu analityka magazynu i przedstawia sposób użycia programu Microsoft Message Analyzer.
* [Przewodnik operacyjny analizatora wiadomości firmy Microsoft](https://technet.microsoft.com/library/jj649776.aspx)

  W tym artykule jest odwołaniem do programu Microsoft Message Analyzer oraz linki samouczek Szybki Start i Podsumowanie funkcji.

## <a name="cross-origin-resource-sharing-cors"></a>Współużytkowanie zasobów między źródłami (CORS)
### <a name="cross-domain-access-of-resources"></a>Dostęp z innych domen zasobów
Gdy przeglądarki sieci web działa w jednej domenie wysyła żądanie HTTP dla zasobu z innej domeny, jest to żądanie HTTP cross-origin. Na przykład strona HTML z contoso.com zażąda hostowanych na fabrikam.blob.core.windows.net jpeg. Ze względów bezpieczeństwa przeglądarki ograniczanie żądań HTTP cross-origin inicjowane przy użyciu skryptów, takich jak JavaScript. Oznacza to, że jeśli kod JavaScript na stronie sieci web w domenie contoso.com zażąda tej jpeg na fabrikam.blob.core.windows.net, przeglądarka nie zezwoli żądania.

Co to ma sposób korzystania z usługi Azure Storage Dobrze, jeśli przechowujesz statycznych zasobów, takich jak pliki danych JSON i XML w magazynie obiektów Blob przy użyciu konta magazynu o nazwie firmy Fabrikam, domeny zasobów będzie fabrikam.blob.core.windows.net i contoso.com aplikacji sieci web nie będzie można uzyskiwać do nich dostęp przy użyciu języka JavaScript, ponieważ różnią się domen. Jest to również wartość true, jeśli próbujesz wywoływanie jednego z usług magazynu Azure — takie jak magazyn tabel — które zwracają dane JSON do przetworzenia przez klienta języka JavaScript.

#### <a name="possible-solutions"></a>Możliwe rozwiązania
Jednym ze sposobów rozwiązania tego problemu jest przypisywany domeny niestandardowej, takie jak "storage.contoso.com" fabrikam.blob.core.windows.net. Problem polega na tym, że można przypisać tylko tej domeny niestandardowe na jedno konto magazynu. Co zrobić, jeśli zasoby są przechowywane w wielu kont magazynu?

Innym sposobem rozwiązania tego problemu ma działać jako serwer proxy dla wywołań Magazyn aplikacji sieci web. Oznacza to, czy plik jest przekazywany do magazynu obiektów Blob, aplikacji sieci web będzie zapisywany lokalnie i skopiuj go do magazynu obiektów Blob lub zostaną odczytać wszystkich go do pamięci, a następnie zapisz je do magazynu obiektów Blob. Alternatywnie można zapisać dedykowanych aplikacji sieci web (np. interfejsu API sieci Web) przekazuje plików lokalnie i zapisuje je w magazynie obiektów Blob. W obu przypadkach należy konta dla tej funkcji, gdy wymaga określenia skalowalność.

#### <a name="how-can-cors-help"></a>Jak może pomóc CORS
Magazyn Azure umożliwia CORS — Cross udostępniania zasobów pochodzenia. Dla każdego konta magazynu można określić domeny, które mogą uzyskiwać dostęp do zasobów na tym koncie magazynu. Na przykład w tym przypadku opisanych powyżej, możemy włączyć mechanizm CORS w fabrikam.blob.core.windows.net konta magazynu i skonfigurować go, aby zezwolić na dostęp do domeny contoso.com. Następnie contoso.com aplikacji sieci web może bezpośrednio uzyskać dostęp do zasobów w fabrikam.blob.core.windows.net.

Jedyną operacją, należy pamiętać, to czy CORS zezwala na dostęp, ale nie zapewnia uwierzytelniania, który jest wymagany dla wszystkich dostępu publicznego zasobów magazynu. Oznacza to, że można tylko dostęp do obiektów blob, jeśli są one publiczne lub zawierać umożliwiając odpowiednich uprawnień sygnaturę dostępu współdzielonego. Tabel, kolejek i plików nie mają publicznego dostępu i wymagają sygnatury dostępu Współdzielonego.

Domyślnie CORS jest wyłączona na wszystkich usług. Mechanizm CORS można włączyć za pomocą interfejsu API REST lub biblioteka klienta magazynu do wywoływania jednej z metod, aby ustawić zasady usługi. Po wykonaniu tej czynności, możesz dołączyć regułę CORS, która jest w formacie XML. Oto przykład reguły CORS, która została ustawiona przy użyciu operacji ustawić właściwości usługi dla usługi obiektów Blob dla konta magazynu. Można wykonać tej operacji za pomocą biblioteki klienta usługi storage lub interfejsów API REST usługi Azure Storage.

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

* **AllowedOrigins** informuje domen niezgodny żądać i odbierać dane z usługi magazynowania. To mówi, że zarówno contoso.com i fabrikam.com można zażądać danych z magazynu obiektów Blob na koncie magazynu określonym. Możesz również ustawić do symbolu wieloznacznego (\*) umożliwia wszystkich domen do żądań dostępu.
* **AllowedMethods** jest to lista metod (zleceń HTTP żądania), które mogą być używane podczas tworzenia żądania. W tym przykładzie są dozwolone tylko PUT i GET. Możesz ustawić do symbolu wieloznacznego (\*) zezwalająca na wszystkie metody mają być używane.
* **AllowedHeaders** to nagłówków żądań, które domeny pochodzenia można określić podczas zgłaszania żądania. W tym przykładzie wszystkie nagłówki metadanych, począwszy od x-ms-meta-data x-ms-meta docelowego, a x-ms-meta-abc są dozwolone. Symbol wieloznaczny (\*) wskazuje, że wszystkie nagłówka, począwszy od określonego prefiksu jest dozwolone.
* **ExposedHeaders** informuje nagłówki odpowiedzi, które powinny zostać ujawnione przez przeglądarkę, aby wystawcy żądania. W tym przykładzie wszystkie nagłówka, począwszy od "x-ms - meta-" mają być widoczne.
* **MaxAgeInSeconds** jest maksymalną ilość czasu, czy żądania wstępnego opcje zostaną zbuforowane przez przeglądarkę. (Aby uzyskać więcej informacji na temat żądania wstępnego Sprawdź pierwszego artykułu poniżej).

#### <a name="resources"></a>Zasoby
Aby uzyskać więcej informacji na temat CORS oraz jak je włączyć zapoznaj się z tych zasobów.

* [Współużytkowanie zasobów między źródłami (CORS) obsługę usług Azure Storage w witrynie Azure.com do udostępniania](../storage-cors-support.md)

  Ten artykuł zawiera omówienie mechanizmu CORS i sposobu ustawiania zasad dla różnych usług.
* [Cross-Origin Resource Sharing (CORS) obsługę usług Azure Storage w witrynie MSDN](https://msdn.microsoft.com/library/azure/dn535601.aspx)

  Jest to dokumentacji dla obsługi mechanizmu CORS dla usług magazynu Azure. Zawiera łącza do artykułów na stosowanie dla wszystkich usług magazynu i przedstawiono przykład i opisano każdy element w pliku CORS.
* [Usługi Microsoft Azure Storage: Wprowadzenie CORS](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/02/03/windows-azure-storage-introducing-cors.aspx)

  To łącze do artykułu początkowej blog o mechanizmu CORS i przedstawiający jak z niego korzystać.

## <a name="frequently-asked-questions-about-azure-storage-security"></a>Często zadawane pytania dotyczące zabezpieczeń usługi Azure Storage
1. **Jak można zweryfikować integralność obiektów blob, który I używam transferu do lub z usługi Azure Storage gdy nie jest używany protokół HTTPS?**

   Przyczyn potrzebnych do obsługi protokołu HTTP zamiast HTTPS i pracy z blokowych obiektów blob, umożliwia sprawdzanie MD5 weryfikowania integralności transferowanych obiektów blob. Pomoże to ochrony z sieci/transport layer błędów, ale niekoniecznie pośredniczące ataków.

   Jeśli używasz protokołu HTTPS, który zapewnia zabezpieczeń na poziomie transportu, następnie przy użyciu algorytmu MD5 sprawdzanie jest nadmiarowe i niepotrzebne.

   Aby uzyskać więcej informacji, zapoznaj się [Przegląd MD5 obiektów Blob Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/18/windows-azure-blob-md5-overview.aspx).
2. **Informacje o zgodności ze standardem FIPS dla Stanów Zjednoczonych Rządowych Stanów Zjednoczonych?**

   Stany Zjednoczone informacji przetwarzania Standard FIPS (Federal) definiuje algorytmy kryptograficzne zatwierdzone do użycia przez amerykański Federalnych systemów komputerowych do ochrony danych poufnych. Włączanie FIPS tryb na serwerze z systemem Windows lub pulpitu informuje system operacyjny powinien być używany tylko standardem FIPS algorytmów kryptograficznych. Jeśli aplikacja używa algorytmów niezgodnych, aplikacje zostaną przerwane. With.NET Framework w wersji 4.5.2 lub nowszym, aplikacja automatycznie przełącza algorytmy kryptografii użyj zgodnych algorytmów FIPS, gdy komputer jest w trybie FIPS.

   Microsoft pozostawia go do każdego klienta w celu podjęcie decyzji o włączeniu w trybie FIPS. Mamy nadzieję, że to nie ma powodu istotnych dla klientów, którzy nie podlegają dla instytucji rządowych przepisy, aby włączyć tryb FIPS domyślnie.

   **Zasoby**

* [Dlaczego jest nie zalecamy "Tryb FIPS" już](https://blogs.technet.microsoft.com/secguide/2014/04/07/why-were-not-recommending-fips-mode-anymore/)

  W tym artykule na blogu powinien zawierać omówienie FIPS i objaśniono, dlaczego nie umożliwiają one trybie FIPS domyślnie.
* [FIPS 140 sprawdzania poprawności](https://technet.microsoft.com/library/cc750357.aspx)

  Ten artykuł zawiera informacje dotyczące sposobu produktów firmy Microsoft i modułów kryptograficznych zgodne ze standardem FIPS dla Stanów Zjednoczonych Federalnych.
* ["Kryptografia systemu: Użyj FIPS algorytmów szyfrowania, mieszania i podpisywania" efekty ustawienia zabezpieczeń w systemie Windows XP i w nowszych wersjach systemu Windows](https://support.microsoft.com/kb/811833)

  Ten artykuł zawiera informacje o trybu FIPS w starszych komputerów z systemem Windows.
