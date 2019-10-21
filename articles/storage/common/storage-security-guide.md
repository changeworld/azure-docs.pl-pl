---
title: Przewodnik po zabezpieczeniach usługi Azure Storage | Microsoft Docs
description: Szczegółowe informacje na temat wielu metod zabezpieczania usługi Azure Storage, w tym między innymi kontroli RBAC, szyfrowanie usługi Storage, szyfrowania po stronie klienta, SMB 3,0 i Azure Disk Encryption.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 72e695762f2e45309787e6f62fa97aae4c959f34
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2019
ms.locfileid: "72598085"
---
# <a name="azure-storage-security-guide"></a>Przewodnik po zabezpieczeniach usługi Azure Storage

Usługa Azure Storage zapewnia kompleksowy zestaw funkcji zabezpieczeń, które wspólnie umożliwiają deweloperom tworzenie bezpiecznych aplikacji:

- Wszystkie dane (w tym metadane) zapisywane w usłudze Azure Storage są automatycznie szyfrowane przy użyciu [szyfrowanie usługi Storage (SSE)](storage-service-encryption.md). Aby uzyskać więcej informacji, zobacz [ogłaszanie domyślnego szyfrowania dla obiektów blob platformy Azure, plików, tabel i queue storage](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).
- Azure Active Directory (Azure AD) i Access Control oparte na rolach (RBAC) są obsługiwane przez usługę Azure Storage dla operacji zarządzania zasobami i operacji na danych w następujący sposób:   
    - Role RBAC można przypisywać do konta magazynu do podmiotów zabezpieczeń i używać usługi Azure AD do autoryzacji operacji zarządzania zasobami, takich jak zarządzanie kluczami.
    - Integracja z usługą Azure AD jest obsługiwana w przypadku operacji na danych obiektów blob i kolejek. Role RBAC można przypisać do zakresu subskrypcji, grupy zasobów, konta magazynu lub pojedynczego kontenera lub kolejki do podmiotu zabezpieczeń lub do zarządzanej tożsamości dla zasobów platformy Azure. Aby uzyskać więcej informacji, zobacz temat [uwierzytelnianie dostępu do usługi Azure Storage przy użyciu Azure Active Directory](storage-auth-aad.md).   
- Dane mogą być zabezpieczone podczas przesyłania między aplikacją i platformą Azure przy użyciu [szyfrowania po stronie klienta](../storage-client-side-encryption.md), protokołu HTTPS lub protokołu SMB 3,0.  
- Dyski systemu operacyjnego i danych używane przez maszyny wirtualne platformy Azure mogą być szyfrowane przy użyciu [Azure Disk Encryption](../../security/fundamentals/encryption-overview.md).
- Dostęp delegowany do obiektów danych w usłudze Azure Storage można udzielić przy użyciu sygnatury dostępu współdzielonego. Aby uzyskać więcej informacji, zobacz [udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego (SAS)](storage-sas-overview.md).

Ten artykuł zawiera omówienie każdej z tych funkcji zabezpieczeń, które mogą być używane z usługą Azure Storage. Dostępne są linki do artykułów, które będą zawierać szczegółowe informacje o każdej z tych funkcji, dzięki czemu można łatwo przeprowadzić dalsze badania dotyczące poszczególnych tematów.

Poniżej przedstawiono tematy, które mają być omówione w tym artykule:

* [Zabezpieczenia płaszczyzny zarządzania](#management-plane-security) — Zabezpieczanie konta magazynu

  Płaszczyzna zarządzania składa się z zasobów używanych do zarządzania kontem magazynu. W tej części omówiono model wdrażania Azure Resource Manager oraz sposób używania Access Control opartego na rolach (RBAC) do kontrolowania dostępu do kont magazynu. Umożliwia ona również zarządzanie kluczami kont magazynu i ich ponowne generowanie.
* [Zabezpieczenia płaszczyzny danych](#data-plane-security) — Zabezpieczanie dostępu do danych

  W tej sekcji dowiesz się, jak umożliwić dostęp do rzeczywistych obiektów danych na koncie magazynu, takich jak obiekty blob, pliki, kolejki i tabele, przy użyciu sygnatur dostępu współdzielonego i przechowywanych zasad dostępu. Będziemy obejmować zarówno SYGNATURy dostępu współdzielonego na poziomie usługi, jak i na poziomie konta. Zobaczymy również, jak ograniczyć dostęp do określonego adresu IP (lub zakresu adresów IP), jak ograniczyć protokół używany do protokołu HTTPS oraz jak odwołać sygnaturę dostępu współdzielonego bez oczekiwania na jej wygaśnięcie.
* [Szyfrowanie podczas transferu](#encryption-in-transit)

  W tej sekcji omówiono sposób zabezpieczania danych podczas ich przesyłania do usługi Azure Storage lub z niej. Porozmawiamy o zalecanym użyciu protokołu HTTPS i szyfrowaniu używanym przez protokół SMB 3,0 dla udziałów plików platformy Azure. Zajmiemy się również szyfrowaniem po stronie klienta, co pozwala na szyfrowanie danych przed ich przesłaniem do magazynu w aplikacji klienckiej i odszyfrowywanie danych po ich przeniesieniu poza magazyn.
* [Szyfrowanie w spoczynku](#encryption-at-rest)

  Będziemy mówić o szyfrowanie usługi Storage (SSE), która jest teraz automatycznie włączona dla nowych i istniejących kont magazynu. Dowiesz się również, jak można użyć Azure Disk Encryption i poznać podstawowe różnice oraz przypadki szyfrowania dysków, a także szyfrowanie z użyciem funkcji SSE i szyfrowania po stronie klienta. Na krótko Przyjrzyjmy się zgodności ze standardem FIPS dla komputerów rządowych w Stanach Zjednoczonych.
* Używanie [analityka magazynu](#storage-analytics) do inspekcji dostępu do usługi Azure Storage

  W tej sekcji omówiono sposób znajdowania informacji w dziennikach analizy magazynu dla żądania. Zajmiemy się rzeczywistymi danymi dziennika analizy magazynu i zobacz, jak rozpoznać, czy żądanie zostało wykonane przy użyciu klucza konta magazynu, z sygnaturą dostępu współdzielonego, czy anonimowe, oraz czy zakończyło się powodzeniem lub niepowodzeniem.
* [Włączanie klientów opartych na przeglądarce przy użyciu mechanizmu CORS](#cross-origin-resource-sharing-cors)

  W tej sekcji omówiono sposób zezwalania na udostępnianie zasobów między źródłami (CORS). Będziemy mówić o dostępie do wielu domen i sposobach ich obsługi przy użyciu funkcji CORS wbudowanych w usługę Azure Storage.

## <a name="management-plane-security"></a>Zabezpieczenia płaszczyzny zarządzania
Płaszczyzna zarządzania składa się z operacji, które wpływają na samo konto magazynu. Można na przykład utworzyć lub usunąć konto magazynu, uzyskać listę kont magazynu w ramach subskrypcji, pobrać klucze konta magazynu lub ponownie wygenerować klucze konta magazynu.

Podczas tworzenia nowego konta magazynu należy wybrać model wdrożenia klasyczny lub Menedżer zasobów. Klasyczny model tworzenia zasobów na platformie Azure zezwala tylko na dostęp do subskrypcji i z kolei na koncie magazynu.

Ten przewodnik koncentruje się na modelu Menedżer zasobów, który jest zalecanym sposobem tworzenia kont magazynu. W przypadku kont magazynu Menedżer zasobów, a nie udzielanie dostępu do całej subskrypcji, można kontrolować dostęp na bardziej ograniczonym poziomie do płaszczyzny zarządzania przy użyciu Access Control opartej na rolach (RBAC).

### <a name="how-to-secure-your-storage-account-with-role-based-access-control-rbac"></a>Jak zabezpieczyć konto magazynu za pomocą Access Control opartego na rolach (RBAC)
Zapoznaj się z informacjami o tym, co to jest RBAC i jak można z niej korzystać. Każda subskrypcja platformy Azure zawiera usługę Azure Active Directory. Użytkownikom, grupom i aplikacjom z tego katalogu można udzielić dostępu do zarządzania zasobami w ramach subskrypcji platformy Azure korzystającej z modelu wdrażania Menedżer zasobów. Ten typ zabezpieczeń jest określany mianem Access Control opartej na rolach (RBAC). Aby zarządzać tym dostępem, można użyć [Azure Portal](https://portal.azure.com/), [narzędzi interfejsu wiersza polecenia platformy Azure](../../cli-install-nodejs.md), [programu PowerShell](/powershell/azureps-cmdlets-docs)lub [interfejsów API REST dostawcy zasobów usługi Azure Storage](https://msdn.microsoft.com/library/azure/mt163683.aspx).

W modelu Menedżer zasobów należy umieścić konto magazynu w grupie zasobów i kontrolować dostęp do płaszczyzny zarządzania tego konkretnego konta magazynu przy użyciu Azure Active Directory. Na przykład możesz nadać określonym użytkownikom możliwość uzyskiwania dostępu do kluczy konta magazynu, a inni użytkownicy mogą wyświetlać informacje o koncie magazynu, ale nie mogą uzyskać dostępu do kluczy konta magazynu.

#### <a name="granting-access"></a>Udzielanie dostępu
Dostęp jest udzielany przez przypisanie odpowiedniej roli RBAC użytkownikom, grupom i aplikacjom w prawidłowym zakresie. Aby udzielić dostępu do całej subskrypcji, należy przypisać rolę na poziomie subskrypcji. Możesz udzielić dostępu do wszystkich zasobów w grupie zasobów, przydając im uprawnienia do samej grupy zasobów. Można także przypisać określone role do określonych zasobów, takich jak konta magazynu.

Poniżej przedstawiono główne punkty, które należy znać, aby uzyskać dostęp do operacji zarządzania kontem usługi Azure Storage za pomocą funkcji RBAC:

* Po przypisaniu dostępu należy przypisać rolę do konta, do którego chcesz uzyskać dostęp. Można kontrolować dostęp do operacji używanych do zarządzania tym kontem magazynu, ale nie do obiektów danych na koncie. Na przykład można udzielić uprawnienia do pobierania właściwości konta magazynu (na przykład nadmiarowości), ale nie do kontenera lub danych w obrębie kontenera w Blob Storage.
* Aby ktoś mógł mieć uprawnienia dostępu do obiektów danych na koncie magazynu, można przyznać im uprawnienia do odczytywania kluczy konta magazynu, a następnie używać tych kluczy do uzyskiwania dostępu do obiektów blob, kolejek, tabel i plików.
* Role można przypisywać do określonego konta użytkownika, grupy użytkowników lub do określonej aplikacji.
* Każda rola ma listę akcji, a nie akcji. Na przykład rola współautor maszyny wirtualnej ma akcję "listKeys", która umożliwia odczytywanie kluczy konta magazynu. Współautor nie ma akcji, takich jak aktualizowanie dostępu dla użytkowników w Active Directory.
* Role dla magazynu obejmują (ale nie są ograniczone do) następujące role:

  * Właściciel — może zarządzać wszystkimi, w tym dostępem.
  * Współautor — mogą wykonywać dowolne czynności przez właściciela, z wyjątkiem uprawnień do przypisywania. Ktoś mający tę rolę może wyświetlać i ponownie generować klucze konta magazynu. Klucze konta magazynu umożliwiają dostęp do obiektów danych.
  * Reader — mogą wyświetlać informacje o koncie magazynu, z wyjątkiem kluczy tajnych. Na przykład Jeśli przypiszesz rolę z uprawnieniami czytelnika na koncie magazynu dla kogoś, mogą oni wyświetlać właściwości konta magazynu, ale nie mogą wprowadzać żadnych zmian we właściwościach ani wyświetlać kluczy konta magazynu.
  * Współautor konta magazynu — mogą zarządzać kontem magazynu — mogą odczytywać grupy zasobów i zasoby subskrypcji oraz tworzyć wdrożenia grup zasobów subskrypcji i zarządzać nimi. Mogą oni również uzyskać dostęp do kluczy konta magazynu, co z kolei oznacza, że mogą uzyskać dostęp do płaszczyzny danych.
  * Administrator dostępu użytkowników — mogą zarządzać dostępem użytkowników do konta magazynu. Na przykład mogą udzielić dostępu czytelnikowi do określonego użytkownika.
  * Współautor maszyny wirtualnej — może zarządzać maszynami wirtualnymi, ale nie z konta magazynu, z którym są połączone. Ta rola może wyświetlić listę kluczy konta magazynu, co oznacza, że użytkownik, do którego jest przypisana ta rola, może aktualizować płaszczyznę danych.

    Aby użytkownik mógł utworzyć maszynę wirtualną, musi mieć możliwość utworzenia odpowiedniego pliku VHD na koncie magazynu. Aby to zrobić, muszą być w stanie pobrać klucz konta magazynu i przekazać go do interfejsu API tworzącego maszynę wirtualną. W związku z tym muszą mieć to uprawnienie, aby można było wyświetlić listę kluczy konta magazynu.
* Możliwość definiowania ról niestandardowych to funkcja, która umożliwia tworzenie zestawu akcji z listy dostępnych akcji, które mogą być wykonywane w zasobach platformy Azure.
* Aby można było przypisać rolę do nich, użytkownik musi zostać skonfigurowany w Azure Active Directory.
* Można utworzyć raport o tym, kto udzielił lub odwołać rodzaj dostępu do którego oraz w jakim zakresie używa programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

#### <a name="resources"></a>Zasoby
* [Kontrola dostępu oparta na rolach w usłudze Azure Active Directory](../../role-based-access-control/role-assignments-portal.md)

  W tym artykule objaśniono funkcję kontroli dostępu opartej na rolach w usłudze Azure Active Directory i sposób jej działania.
* [Kontrola dostępu oparta na rolach (RBAC): wbudowane role](../../role-based-access-control/built-in-roles.md)

  W tym artykule szczegółowo opisano wszystkie wbudowane role dostępne w ramach RBAC.
* [Omówienie wdrażania przy użyciu usługi Resource Manager oraz wdrażania klasycznego](../../azure-resource-manager/resource-manager-deployment-model.md)

  W tym artykule opisano wdrożenie Menedżer zasobów i klasyczne modele wdrażania oraz wyjaśniono zalety korzystania z Menedżer zasobów i grup zasobów. Wyjaśniono, jak dostawcy zasobów obliczeniowych, sieci i magazynu platformy Azure pracują pod modelem Menedżer zasobów.
* [Zarządzanie kontrolą dostępu opartą na rolach za pomocą interfejsu API REST](../../role-based-access-control/role-assignments-rest.md)

  W tym artykule przedstawiono sposób zarządzania kontrolą dostępu opartą na rolach (RBAC) za pomocą interfejsu API REST.
* [Dokumentacja interfejsu API REST dostawcy zasobów usługi Azure Storage](https://msdn.microsoft.com/library/azure/mt163683.aspx)

  Ten interfejs API zawiera opis interfejsów API, których można użyć do programistycznego zarządzania kontem magazynu.

* [Kontrola dostępu oparta na rolach dla platformy Microsoft Azure — konferencja Ignite](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

  To jest link do filmu wideo w witrynie Channel 9 z konferencji Microsoft Ignite 2015. W tej sesji rozmawiamy o możliwościach zarządzania dostępem i raportowania na platformie Azure i eksplorujemy najlepsze rozwiązania dotyczące zabezpieczania dostępu do subskrypcji Azure za pomocą usługi Azure Active Directory.

### <a name="managing-your-storage-account-keys"></a>Zarządzanie kluczami konta magazynu
Klucze konta magazynu to 512-bitowe ciągi utworzone przez platformę Azure, które razem z nazwą konta magazynu mogą być używane do uzyskiwania dostępu do obiektów danych przechowywanych na koncie magazynu, na przykład, obiektów blob, jednostek w tabeli, komunikatów w kolejce i plików w udziale plików platformy Azure. Kontrolowanie dostępu do kluczy konta magazynu kontroluje dostęp do płaszczyzny danych dla tego konta magazynu.

Każde konto magazynu ma dwa klucze, określane jako "Key 1" i "Key 2" w [Azure Portal](https://portal.azure.com/) i w poleceniach cmdlet programu PowerShell. Można je ponownie wygenerować ręcznie przy użyciu jednej z kilku metod, w tym między innymi [Azure Portal](https://portal.azure.com/), programu PowerShell, interfejsu wiersza polecenia platformy Azure lub programowo przy użyciu biblioteki klienta usługi Azure Storage lub interfejsu API REST usług Storage.

Istnieje wiele powodów, dla których można ponownie wygenerować klucze konta magazynu.

* W związku z bezpieczeństwem można regularnie generować te zasady.
* Klucze kont magazynu należy wygenerować ponownie, jeśli ktoś zarządza w aplikacji i pobierze klucz, który został stałe lub zapisany w pliku konfiguracji, zapewniając im pełny dostęp do konta magazynu.
* Innym przypadkiem ponownego wygenerowania klucza jest to, że zespół używa aplikacji Eksplorator usługi Storage, która zachowuje klucz konta magazynu i jeden z członków zespołu opuszcza. Aplikacja będzie nadal działała, dając im dostęp do konta magazynu po ich zakończeniu. Jest to w rzeczywistości podstawowym powodem, który utworzył sygnatury dostępu współdzielonego na poziomie konta — można użyć SAS na poziomie konta zamiast przechowywania kluczy dostępu w pliku konfiguracji.

#### <a name="key-regeneration-plan"></a>Plan regeneracji kluczy
Nie chcesz tylko ponownie wygenerować klucza, którego używasz bez konieczności planowania. W takim przypadku można wyciąć cały dostęp do tego konta magazynu, co może spowodować poważne zakłócenie. Dlatego istnieją dwa klucze. Należy ponownie wygenerować jeden klucz w danym momencie.

Przed ponownym wygenerowaniem kluczy upewnij się, że masz listę wszystkich aplikacji, które są zależne od konta magazynu, a także inne usługi, z których korzystasz na platformie Azure. Jeśli na przykład używasz Azure Media Services, które są zależne od konta magazynu, należy ponownie zsynchronizować klucze dostępu z usługą multimediów po ponownym wygenerowanym kluczu. W przypadku korzystania z aplikacji, takich jak Eksplorator magazynu, należy również dostarczyć nowe klucze do tych aplikacji. Jeśli masz maszyny wirtualne, których pliki VHD są przechowywane na koncie magazynu, nie wpłynie to na ponowne generowanie kluczy konta magazynu.

Klucze można wygenerować ponownie w Azure Portal. Gdy klucze zostaną ponownie wygenerowane, synchronizacja między usługami magazynu może potrwać do 10 minut.

Gdy wszystko będzie gotowe, poniżej przedstawiono ogólny proces, w którym szczegółowo opisano, jak należy zmienić klucz. W takim przypadku przyjęto, że obecnie korzystasz z klucza 1 i zmienisz wszystko tak, aby korzystało z klucza 2.

1. Wygeneruj ponownie klucz 2, aby upewnić się, że jest bezpieczny. Można to zrobić w Azure Portal.
2. We wszystkich aplikacjach, w których jest przechowywany klucz magazynu, Zmień klucz magazynu tak, aby używał nowej wartości klucza 2. Przetestuj i Opublikuj aplikację.
3. Po pomyślnym uruchomieniu wszystkich aplikacji i usług ponownie Wygeneruj klucz 1. Gwarantuje to, że każdy, do którego użytkownik nie przyznał jeszcze żadnego dostępu do konta magazynu.

Jeśli obecnie używasz klucza 2, możesz użyć tego samego procesu, ale odwrócić nazwy kluczy.

Można przeprowadzić migrację przez kilka dni, zmieniając każdą aplikację tak, aby korzystała z nowego klucza i publikować ją. Po wykonaniu wszystkich tych czynności należy wrócić i ponownie wygenerować stary klucz, aby przestanie działać.

Innym rozwiązaniem jest umieszczenie klucza konta magazynu w [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) jako klucz tajny i pozyskanie przez aplikacje z tego klucza. Następnie po ponownym wygenerowaniu klucza i zaktualizowaniu Azure Key Vault aplikacje nie będą musiały zostać wdrożone ponownie, ponieważ automatycznie spowodują pobranie nowego klucza z Azure Key Vault. Należy pamiętać, że aplikacja może odczytywać klucz za każdym razem, gdy jest potrzebny, lub w pamięci podręcznej, a jeśli go używa, należy ponownie pobrać klucz z Azure Key Vault.

Użycie Azure Key Vault powoduje również dodanie innego poziomu zabezpieczeń dla kluczy magazynu. W przypadku korzystania z tej metody w pliku konfiguracyjnym nigdy nie będzie stałe klucza magazynu, co spowoduje usunięcie tego drogi przez uzyskanie dostępu do kluczy bez określonego uprawnienia.

Inną zaletą korzystania z Azure Key Vault jest również kontrolowanie dostępu do kluczy przy użyciu Azure Active Directory. Oznacza to, że można udzielić dostępu do kilku aplikacji, które muszą pobrać klucze z Azure Key Vault i wiedzieć, że inne aplikacje nie będą mogły uzyskać dostępu do kluczy bez przyznania im uprawnienia.

> [!NOTE]
> Firma Microsoft zaleca używanie tylko jednego z kluczy we wszystkich aplikacjach w tym samym czasie. Jeśli używasz klucza 1 w niektórych miejscach i w innym miejscu niż 2, nie będziesz w stanie obrócić swoich kluczy bez utraty dostępu do aplikacji.

#### <a name="resources"></a>Zasoby

* [Zarządzanie ustawieniami konta magazynu w witrynie Azure Portal](storage-account-manage.md)
* [Dokumentacja interfejsu API REST dostawcy zasobów usługi Azure Storage](https://msdn.microsoft.com/library/mt163683.aspx)

## <a name="data-plane-security"></a>Zabezpieczenia płaszczyzny danych
Zabezpieczenia płaszczyzny danych to metody służące do zabezpieczania obiektów danych przechowywanych w usłudze Azure Storage — obiektów blob, kolejek, tabel i plików. Zaobserwowano metody szyfrowania danych i bezpieczeństwa podczas przesyłania danych, ale jak można kontrolować dostęp do obiektów?

Dostępne są trzy opcje autoryzacji dostępu do obiektów danych w usłudze Azure Storage, w tym:

- Autoryzowanie dostępu do kontenerów i kolejek za pomocą usługi Azure AD. Usługa Azure AD zapewnia korzyści w porównaniu z innymi podejściami do autoryzacji, w tym eliminuje konieczność przechowywania wpisów tajnych w kodzie. Aby uzyskać więcej informacji, zobacz temat [uwierzytelnianie dostępu do usługi Azure Storage przy użyciu Azure Active Directory](storage-auth-aad.md). 
- Korzystanie z kluczy konta magazynu w celu autoryzowania dostępu za pośrednictwem klucza współużytkowanego. Autoryzacja za pomocą klucza współużytkowanego wymaga przechowywania kluczy konta magazynu w aplikacji, dlatego firma Microsoft zaleca korzystanie z usługi Azure AD zamiast tego, jeśli jest to możliwe.
- Używanie sygnatur dostępu współdzielonego do przyznawania kontrolowanych uprawnień do określonych obiektów danych przez określony czas.

Ponadto w przypadku Blob Storage można zezwolić na publiczny dostęp do obiektów BLOB przez ustawienie poziomu dostępu dla kontenera, który przechowuje obiekty blob odpowiednio. Jeśli ustawisz dostęp dla kontenera do obiektu BLOB lub kontenera, zezwoli on na publiczny dostęp do odczytu dla obiektów BLOB w tym kontenerze. Oznacza to, że każda osoba mająca adres URL wskazujący obiekt BLOB w tym kontenerze może otworzyć ją w przeglądarce bez użycia sygnatury dostępu współdzielonego lub kluczy konta magazynu.

Oprócz ograniczania dostępu za pośrednictwem autoryzacji można także używać [zapór i sieci wirtualnych](storage-network-security.md) , aby ograniczyć dostęp do konta magazynu na podstawie reguł sieci.  Takie podejście umożliwia odmowa dostępu do publicznego ruchu internetowego i przyznanie dostępu tylko do określonych sieci wirtualnych platformy Azure lub publicznych zakresów adresów IP w Internecie.

### <a name="storage-account-keys"></a>Klucze kont magazynu
Klucze konta magazynu to 512-bitowe ciągi utworzone przez platformę Azure, które razem z nazwą konta magazynu mogą być używane do uzyskiwania dostępu do obiektów danych przechowywanych na koncie magazynu.

Można na przykład odczytywać obiekty blob, zapisywać je w kolejkach, tworzyć tabele i modyfikować pliki. Wiele z tych akcji można wykonać za pośrednictwem Azure Portal lub z jednej z wielu aplikacji Eksplorator usługi Storage. Możesz również napisać kod, aby użyć interfejsu API REST lub jednej z bibliotek klienta magazynu, aby wykonać te operacje.

Zgodnie z opisem w sekcji dotyczącej [zabezpieczeń płaszczyzny zarządzania](#management-plane-security)dostęp do kluczy magazynu dla klasycznego konta magazynu można udzielić, zapewniając pełny dostęp do subskrypcji platformy Azure. Dostęp do kluczy magazynu dla konta magazynu przy użyciu modelu Azure Resource Manager można kontrolować za pośrednictwem Access Control opartych na rolach (RBAC).

### <a name="how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies"></a>Jak delegować dostęp do obiektów na koncie przy użyciu sygnatur dostępu współdzielonego i przechowywanych zasad dostępu
Sygnatura dostępu współdzielonego jest ciągiem zawierającym token zabezpieczający, który można dołączyć do identyfikatora URI, który umożliwia delegowanie dostępu do obiektów magazynu i określanie ograniczeń, takich jak uprawnienia i zakres daty/godziny dostępu.

Można udzielić dostępu do obiektów blob, kontenerów, komunikatów w kolejce, plików i tabel. W przypadku tabel można faktycznie udzielić uprawnienia dostępu do zakresu jednostek w tabeli, określając zakresy partycji i kluczy wierszy, do których użytkownik ma mieć dostęp. Jeśli na przykład dane są przechowywane z kluczem partycji stanu geograficznego, można dać komuś dostęp tylko do danych dla Kalifornii.

Innym przykładem może być nadanie aplikacji sieci Web tokenu sygnatury dostępu współdzielonego, który umożliwia zapisanie wpisów do kolejki i nadanie roli procesu roboczego tokenu sygnatury dostępu współdzielonego w celu pobrania komunikatów z kolejki i przetworzenia ich. Można też dać jednemu klientowi token SAS, którego mogą używać do przekazywania obrazów do kontenera w Blob Storage i nadawać aplikacji sieci Web uprawnienia do odczytu tych obrazów. W obu przypadkach występuje separacja problemów — poszczególne aplikacje mogą uzyskać tylko dostęp wymagany do wykonania zadania. Jest to możliwe za pomocą sygnatur dostępu współdzielonego.

#### <a name="why-you-want-to-use-shared-access-signatures"></a>Dlaczego chcesz używać sygnatur dostępu współdzielonego
Dlaczego chcesz używać sygnatury dostępu współdzielonego, a nie tylko wydająsz klucz konta magazynu, co jest dużo łatwiejsze? Podawanie klucza konta magazynu jest podobne do udostępniania kluczy magazynu. Przyznaje pełen dostęp. Ktoś może użyć swoich kluczy i przekazać całą bibliotekę utworów muzycznych na swoje konto magazynu. Mogą również zamienić pliki na wersje zainfekowane wirusami lub wykraść dane. Przyznanie nieograniczonego dostępu do konta magazynu to coś, co nie powinno być wykonywane w sposób jasny.

Sygnatury dostępu współdzielonego umożliwiają uzyskanie klientowi tylko uprawnień wymaganych przez ograniczony czas. Na przykład jeśli ktoś przekazuje obiekt BLOB do Twojego konta, można udzielić im dostępu do zapisu przez wystarczająco dużo czasu, aby przekazać obiekt BLOB (w zależności od rozmiaru obiektu BLOB). A jeśli zmienisz zdanie, możesz odwołać ten dostęp.

Ponadto można określić, że żądania wysyłane przy użyciu sygnatury dostępu współdzielonego są ograniczone do określonego adresu IP lub zakresu adresów IP zewnętrznych od platformy Azure. Możesz również wymagać, aby żądania zostały wykonane przy użyciu określonego protokołu (HTTPS lub HTTP/HTTPS). Oznacza to, że jeśli chcesz zezwolić tylko na ruch HTTPS, możesz ustawić wymagany protokół na HTTPS, a ruch HTTP zostanie zablokowany.

#### <a name="definition-of-a-shared-access-signature"></a>Definicja sygnatury dostępu współdzielonego
Sygnatura dostępu współdzielonego to zestaw parametrów zapytania dołączanych do adresu URL wskazywanego przez zasób

zawiera informacje o dozwolonym dostępie oraz o długości czasu, przez jaki dozwolony jest dostęp. Oto przykład: Ten identyfikator URI zapewnia dostęp do odczytu do obiektu BLOB przez pięć minut. Zwróć uwagę, że parametry zapytania SAS muszą mieć zakodowany adres URL, na przykład% 3A dla dwukropka (:) lub %20 dla odstępu.

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

#### <a name="how-the-shared-access-signature-is-authorized-by-the-azure-storage-service"></a>Jak sygnatura dostępu współdzielonego jest autoryzowana przez usługę Azure Storage
Gdy usługa magazynu odbiera żądanie, przyjmuje parametry zapytania wejściowego i tworzy podpis przy użyciu tej samej metody co program wywołujący. Następnie porównuje dwa podpisy. Jeśli są one zgodne, usługa magazynu może sprawdzić wersję usługi magazynu, aby upewnić się, że jest ona prawidłowa, sprawdź, czy bieżąca data i godzina znajdują się w określonym oknie, upewnij się, że żądany dostęp odpowiada żądaniu wykonanemu itd.

Na przykład przy użyciu powyższego adresu URL, jeśli adres URL wskazuje plik zamiast obiektu BLOB, żądanie nie powiedzie się, ponieważ określa, że sygnatura dostępu współdzielonego jest dla obiektu BLOB. Jeśli wywołane polecenie REST miało na celu zaktualizowanie obiektu BLOB, nie powiedzie się, ponieważ sygnatura dostępu współdzielonego określa, że dozwolony jest tylko odczyt.

#### <a name="types-of-shared-access-signatures"></a>Typy sygnatur dostępu współdzielonego
* Za pomocą sygnatury dostępu współdzielonego na poziomie usługi można uzyskać dostęp do określonych zasobów na koncie magazynu. Przykładem jest pobranie listy obiektów BLOB w kontenerze, pobranie obiektu BLOB, zaktualizowanie jednostki w tabeli, dodanie komunikatów do kolejki lub przekazanie pliku do udziału plików.
* Za pomocą sygnatury dostępu współdzielonego na poziomie konta można uzyskać dostęp do wszystkich elementów, które mogą być używane przez skojarzenie SAS poziomu usługi. Ponadto może zapewnić dostęp do zasobów, które nie są dozwolone przy użyciu sygnatury dostępu współdzielonego na poziomie usługi, takich jak możliwość tworzenia kontenerów, tabel, kolejek i udziałów plików. Możesz również określić dostęp do wielu usług jednocześnie. Na przykład możesz dać komuś dostęp do obiektów blob i plików na koncie magazynu.

#### <a name="creating-a-sas-uri"></a>Tworzenie identyfikatora URI sygnatury dostępu współdzielonego
1. Można utworzyć identyfikator URI na żądanie, definiując wszystkie parametry zapytania za każdym razem.

   Takie podejście jest elastyczne, ale jeśli masz logiczny zestaw parametrów, które są podobne każdorazowo, używanie przechowywanych zasad dostępu jest lepszym pomysłem.
2. Można utworzyć zasady dostępu przechowywane dla całego kontenera, udziału plików, tabeli lub kolejki. Następnie można go użyć jako podstawy dla tworzonych identyfikatorów URI SAS. Uprawnienia na podstawie przechowywanych zasad dostępu można łatwo odwołać. Dla każdego kontenera, kolejki, tabeli lub udziału plików można zdefiniować maksymalnie pięć zasad.

   Na przykład jeśli chcesz, aby wiele osób odczytało obiekty blob w konkretnym kontenerze, możesz utworzyć zasady dostępu przechowywane, które mają taką samą wartość jak "Udziel dostępu do odczytu" oraz inne ustawienia, które będą takie same. Następnie można utworzyć identyfikator URI SAS przy użyciu ustawień przechowywanych zasad dostępu i określić datę/godzinę wygaśnięcia. Zaletą tego jest to, że nie trzeba określać wszystkich parametrów zapytania za każdym razem.

#### <a name="revocation"></a>Unieważni
Załóżmy, że Twoje sygnatury dostępu współdzielonego zostały naruszone, lub chcesz zmienić je ze względu na bezpieczeństwo firmowe lub wymagania dotyczące zgodności z przepisami. Jak odwołać dostęp do zasobu za pomocą tego skojarzenia zabezpieczeń? Jest to zależne od sposobu tworzenia identyfikatora URI sygnatury dostępu współdzielonego.

Jeśli używasz identyfikatorów URI ad hoc, masz trzy opcje. Można wydać tokeny sygnatury dostępu współdzielonego z krótkimi zasadami wygasania i poczekać na wygaśnięcie sygnatury dostępu współdzielonego Można zmienić nazwę zasobu lub usunąć go (przy założeniu, że token został objęty zakresem pojedynczego obiektu). Można zmienić klucze konta magazynu. Ta ostatnia opcja może mieć znaczny wpływ, w zależności od liczby usług korzystających z tego konta magazynu i prawdopodobnie nie jest to konieczne bez konieczności planowania.

Jeśli używasz sygnatury dostępu współdzielonego pochodzącego z przechowywanych zasad, możesz usunąć dostęp przez odwołanie do przechowywanych zasad dostępu — możesz po prostu zmienić ten element już wygasł lub można usunąć go całkowicie. Ta operacja zacznie obowiązywać natychmiast i unieważnia każde sygnatury dostępu współdzielonego utworzone przy użyciu tych zasad. Zaktualizowanie lub usunięcie przechowywanych zasad dostępu może mieć wpływ na osoby, które mają dostęp do tego określonego kontenera, udziału plików, tabeli lub kolejki za pośrednictwem sygnatur dostępu współdzielonego, ale jeśli klienci są zapisani, aby zażądały nowego skojarzenia zabezpieczeń, gdy stary z nich przestanie być prawidłowy, będzie to możliwe.

Ze względu na to, że używanie sygnatury dostępu współdzielonego pochodzącego z przechowywanych zasad jest możliwe, można natychmiast odwołać te sygnatury dostępu współdzielonego.

#### <a name="resources"></a>Zasoby
Aby uzyskać bardziej szczegółowe informacje na temat używania sygnatur dostępu współdzielonego i przechowywanych zasad dostępu, zapoznaj się z przykładami w następujących artykułach:

* Są to artykuły referencyjne.

  * [SAS usługi](https://msdn.microsoft.com/library/dn140256.aspx)

    W tym artykule przedstawiono przykłady użycia sygnatury dostępu współdzielonego na poziomie usługi z obiektami BLOB, kolejkami komunikatów, zakresami tabel i plikami.
  * [Konstruowanie sygnatury dostępu współdzielonego usługi](https://msdn.microsoft.com/library/dn140255.aspx)
  * [Konstruowanie sygnatury dostępu współdzielonego konta](https://msdn.microsoft.com/library/mt584140.aspx)

* Uwierzytelnianie

  * [Uwierzytelnianie dla usług Azure Storage](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* Sygnatura dostępu współdzielonego Wprowadzenie — samouczek

  * [Samouczek Wprowadzenie SAS](https://github.com/Azure-Samples/storage-dotnet-sas-getting-started)

## <a name="encryption-in-transit"></a>Szyfrowanie podczas przesyłania
### <a name="transport-level-encryption--using-https"></a>Szyfrowanie na poziomie transportu — używanie protokołu HTTPS
Innym krokiem, który należy wykonać w celu zapewnienia bezpieczeństwa danych usługi Azure Storage, jest szyfrowanie danych między klientem a usługą Azure Storage. Pierwszym zaleceniem jest zawsze używanie protokołu [https](https://en.wikipedia.org/wiki/HTTPS) , który zapewnia bezpieczną komunikację za pośrednictwem publicznego Internetu.

Aby korzystać z bezpiecznego kanału komunikacyjnego, należy zawsze używać protokołu HTTPS podczas wywoływania interfejsów API REST lub uzyskiwania dostępu do obiektów w magazynie. Ponadto **sygnatury dostępu współdzielonego**, które mogą służyć do delegowania dostępu do obiektów usługi Azure Storage, obejmują opcję określenia, że tylko protokół HTTPS może być używany podczas korzystania z sygnatur dostępu współdzielonego, co gwarantuje, że każdy wysyła linki z TOKENami SAS będzie używać właściwego protokołu.

Można wymusić użycie protokołu HTTPS podczas wywoływania interfejsów API REST w celu uzyskania dostępu do obiektów w kontach magazynu przez włączenie [bezpiecznego transferu wymaganego](../storage-require-secure-transfer.md) dla konta magazynu. Połączenia przy użyciu protokołu HTTP zostaną odrzucone po włączeniu tej opcji.

### <a name="using-encryption-during-transit-with-azure-file-shares"></a>Używanie szyfrowania podczas przesyłania z udziałami plików platformy Azure
[Azure Files](../files/storage-files-introduction.md) obsługuje szyfrowanie za pośrednictwem protokołu SMB 3,0 i z użyciem protokołu HTTPS w przypadku korzystania z interfejsu API REST plików. W przypadku instalowania poza regionem platformy Azure, w którym znajduje się udział plików platformy Azure, na przykład lokalnie lub w innym regionie świadczenia usługi Azure, jest zawsze wymagane szyfrowanie SMB 3,0 z funkcją szyfrowania. Protokół SMB 2,1 nie obsługuje szyfrowania, dlatego połączenia domyślne są dozwolone tylko w obrębie tego samego regionu na platformie Azure, ale protokół SMB 3,0 z szyfrowaniem można wymusić, [wymagając bezpiecznego transferu](../storage-require-secure-transfer.md) dla konta magazynu.

Protokół SMB 3,0 z szyfrowaniem jest dostępny we [wszystkich obsługiwanych systemach operacyjnych Windows i Windows Server,](../files/storage-how-to-use-files-windows.md) z wyjątkiem Windows 7 i windows Server 2008 R2, które obsługują tylko protokół SMB 2,1. Protokół SMB 3,0 jest również obsługiwany w przypadku [macOS](../files/storage-how-to-use-files-mac.md) i dystrybucji systemu [Linux](../files/storage-how-to-use-files-linux.md) przy użyciu jądra systemu Linux 4,11 lub nowszego. Obsługa szyfrowania protokołu SMB 3,0 była również przełączana do starszych wersji jądra systemu Linux przez kilka dystrybucji systemu Linux, zapoznaj się z [tematem wymagania klienta SMB](../files/storage-how-to-use-files-linux.md#smb-client-reqs).

### <a name="using-client-side-encryption-to-secure-data-that-you-send-to-storage"></a>Zabezpieczanie danych wysyłanych do magazynu przy użyciu szyfrowania po stronie klienta
Inna opcja, która pomaga upewnić się, że dane są bezpieczne podczas przesyłania między aplikacją kliencką a magazynem, jest szyfrowanie po stronie klienta. Dane są szyfrowane przed przekazaniem ich do usługi Azure Storage. Podczas pobierania danych z usługi Azure Storage dane są odszyfrowywane po odebraniu po stronie klienta. Mimo że dane są szyfrowane przez sieć, zalecamy również korzystanie z protokołu HTTPS, ponieważ ma ona wbudowane sprawdzenia integralności danych, które pomagają ograniczyć błędy sieci wpływające na integralność danych.

Szyfrowanie po stronie klienta to również Metoda szyfrowania danych magazynowanych, ponieważ dane są przechowywane w postaci zaszyfrowanej. Porozmawiamy tutaj z bardziej szczegółowymi informacjami w sekcji dotyczącej szyfrowania w stanie [spoczynku](#encryption-at-rest).

## <a name="encryption-at-rest"></a>Szyfrowanie w spoczynku
Istnieją trzy funkcje platformy Azure, które zapewniają szyfrowanie w spoczynku. Azure Disk Encryption służy do szyfrowania dysków systemu operacyjnego i danych w programie IaaS Virtual Machines. Szyfrowanie po stronie klienta i funkcja SSE są używane do szyfrowania danych w usłudze Azure Storage. 

Korzystając z szyfrowania po stronie klienta, można szyfrować przesyłane dane (które również są przechowywane w postaci zaszyfrowanej w magazynie), ale użytkownik może użyć protokołu HTTPS podczas transferu i mieć pewien sposób, aby dane były automatycznie szyfrowane podczas przechowywania. Istnieją dwa sposoby wykonania tej czynności — Azure Disk Encryption i SSE. Jest on używany do bezpośredniego szyfrowania danych na dyskach systemu operacyjnego i danych używanych przez maszyny wirtualne, a drugi jest używany do szyfrowania danych zapisaną na platformie Azure Blob Storage.

### <a name="storage-service-encryption-sse"></a>Szyfrowanie usługi Storage (SSE)

Włączono funkcję SSE dla wszystkich kont magazynu i nie można jej wyłączyć. Funkcja SSE automatycznie szyfruje dane podczas zapisywania ich w usłudze Azure Storage. Podczas odczytywania danych z usługi Azure Storage jest ono odszyfrowywane przez usługę Azure Storage przed zwróceniem. Funkcja SSE umożliwia Zabezpieczanie danych bez konieczności modyfikowania kodu lub dodawania kodu do dowolnych aplikacji.

Możesz użyć zarówno kluczy zarządzanych przez firmę Microsoft, jak i własnych kluczy niestandardowych. Firma Microsoft generuje klucze zarządzane i obsługuje ich bezpieczny magazyn, a także ich regularne rotacje, zgodnie z zasadami wewnętrznymi firmy Microsoft. Aby uzyskać więcej informacji o korzystaniu z kluczy niestandardowych, zobacz [szyfrowanie usługi Storage przy użyciu kluczy zarządzanych przez klienta w programie Azure Key Vault](storage-service-encryption-customer-managed-keys.md).

Usługa SSE automatycznie szyfruje dane we wszystkich warstwach wydajności (Standardowa i Premium), wszystkich modelach wdrażania (model usługi Azure Resource Manager i model klasyczny) oraz wszystkich usługach Azure Storage (Blob, Queue, Table i File). 

### <a name="client-side-encryption"></a>Szyfrowanie po stronie klienta
Wspomniano szyfrowanie po stronie klienta podczas omawiania szyfrowania danych podczas przesyłania. Ta funkcja umożliwia programowe szyfrowanie danych w aplikacji klienckiej przed wysłaniem ich przez sieć do usługi Azure Storage oraz programowe odszyfrowywanie danych po ich pobraniu z usługi Azure Storage.

Zapewnia to szyfrowanie podczas przesyłania, ale również udostępnia funkcję szyfrowania w stanie spoczynku. Mimo że dane są szyfrowane podczas przesyłania, nadal zalecamy użycie protokołu HTTPS, aby skorzystać z wbudowanych testów integralności danych, które pomagają ograniczyć błędy sieci wpływające na integralność danych.

Przykładem użycia tego elementu jest aplikacja sieci Web, która przechowuje obiekty blob i pobiera obiekty blob, i chcesz, aby aplikacja i dane były tak bezpieczne, jak to możliwe. W takim przypadku należy użyć szyfrowania po stronie klienta. Ruch między klientem a usługą Azure Blob Service zawiera zaszyfrowany zasób, a nikt nie może interpretować danych w tranzycie i przetworzyć je w prywatnych obiektach Blob.

Szyfrowanie po stronie klienta jest wbudowane w Java i w bibliotekach klienckich usługi .NET Storage, które z kolei używają Azure Key Vault interfejsów API, co ułatwia wdrażanie. Proces szyfrowania i odszyfrowywania danych wykorzystuje technikę kopert i przechowuje metadane używane przez szyfrowanie w każdym obiekcie magazynu. Na przykład w przypadku obiektów BLOB przechowuje je w metadanych obiektu BLOB, a w przypadku kolejek dodaje je do każdej wiadomości w kolejce.

W przypadku samego szyfrowania można generować własne klucze szyfrowania i zarządzać nimi. Można również użyć kluczy generowanych przez bibliotekę klienta usługi Azure Storage lub Azure Key Vault generować klucze. Klucze szyfrowania można przechowywać w lokalnym magazynie kluczy lub można je przechowywać w Azure Key Vault. Azure Key Vault pozwala udzielić dostępu do wpisów tajnych w Azure Key Vault do określonych użytkowników przy użyciu Azure Active Directory. Oznacza to, że nie tylko każdy może odczytać Azure Key Vault i pobrać klucze używane do szyfrowania po stronie klienta.

#### <a name="resources"></a>Zasoby
* [Szyfrowanie i odszyfrowywanie obiektów BLOB w Microsoft Azure Storage przy użyciu Azure Key Vault](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)

  W tym artykule przedstawiono sposób korzystania z szyfrowania po stronie klienta w programie Azure Key Vault, w tym informacje na temat tworzenia KEK i zapisywania ich w magazynie przy użyciu programu PowerShell.
* [Szyfrowanie po stronie klienta i Azure Key Vault dla Microsoft Azure Storage](../storage-client-side-encryption.md)

  W tym artykule przedstawiono wyjaśnienie szyfrowania po stronie klienta i przedstawiono przykłady użycia biblioteki klienta usługi Storage do szyfrowania i odszyfrowywania zasobów z czterech usług magazynu. Informacje o Azure Key Vault.

### <a name="using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines"></a>Używanie Azure Disk Encryption do szyfrowania dysków używanych przez maszyny wirtualne
Azure Disk Encryption umożliwia szyfrowanie dysków systemu operacyjnego i dysków danych używanych przez maszynę wirtualną IaaS. W przypadku systemu Windows dyski są szyfrowane przy użyciu standardowej technologii szyfrowania funkcji BitLocker. W przypadku systemu Linux dyski są szyfrowane przy użyciu technologii DM-Crypt. Jest ona zintegrowana z Azure Key Vault, aby umożliwić sterowanie kluczami szyfrowania dysków i zarządzanie nimi.

Rozwiązanie obsługuje następujące scenariusze dla maszyn wirtualnych IaaS, gdy są one włączone w Microsoft Azure:

* Integracja z usługą Azure Key Vault
* Maszyny wirtualne w warstwie Standardowa: [a, D, DS, G, GS i tak dalej serie maszyn wirtualnych IaaS](https://azure.microsoft.com/pricing/details/virtual-machines/)
* Włączanie szyfrowania na maszynach wirtualnych z systemem Windows i Linux IaaS
* Wyłączanie szyfrowania na dyskach systemu operacyjnego i danych dla maszyn wirtualnych z systemem Windows IaaS
* Wyłączanie szyfrowania na dyskach danych dla maszyn wirtualnych z systemem Linux IaaS
* Włączanie szyfrowania na maszynach wirtualnych IaaS, na których działa klient systemu operacyjnego Windows
* Włączanie szyfrowania woluminów z ścieżkami instalacji
* Włączanie szyfrowania na maszynach wirtualnych z systemem Linux, które są skonfigurowane przy użyciu funkcji rozkładania dysku (RAID) za pomocą mdadm
* Włączanie szyfrowania na maszynach wirtualnych z systemem Linux przy użyciu LVM dla dysków danych
* Włączanie szyfrowania na maszynach wirtualnych z systemem Windows skonfigurowanych przy użyciu funkcji miejsca do magazynowania
* Wszystkie publiczne regiony platformy Azure są obsługiwane

Rozwiązanie nie obsługuje następujących scenariuszy, funkcji i technologii w wersji:

* Maszyny wirtualne w warstwie Podstawowa IaaS
* Wyłączanie szyfrowania na dysku systemu operacyjnego dla maszyn wirtualnych z systemem Linux IaaS
* IaaS maszyny wirtualne, które są tworzone przy użyciu klasycznej metody tworzenia maszyny wirtualnej
* Integracja z lokalną usługą zarządzania kluczami
* Azure Files (udostępniony system plików), sieciowy system plików (NFS), woluminy dynamiczne i maszyny wirtualne z systemem Windows skonfigurowane z systemami RAID opartymi na oprogramowaniu


> [!NOTE]
> Szyfrowanie dysków systemu operacyjnego Linux jest obecnie obsługiwane w następujących dystrybucjach systemów Linux: RHEL 7,2, CentOS 7.2 n i Ubuntu 16,04.
>
>

Ta funkcja zapewnia, że wszystkie dane na dyskach maszyn wirtualnych są szyfrowane w usłudze Azure Storage.

#### <a name="resources"></a>Zasoby
* [Azure Disk Encryption dla maszyn wirtualnych z systemami Windows i Linux IaaS](../../security/fundamentals/encryption-overview.md)

### <a name="comparison-of-azure-disk-encryption-sse-and-client-side-encryption"></a>Porównanie Azure Disk Encryption, SSE i szyfrowania po stronie klienta

#### <a name="iaas-vms-and-their-vhd-files"></a>IaaS maszyny wirtualne i ich pliki VHD

W przypadku dysków danych używanych przez maszyny wirtualne IaaS zaleca się Azure Disk Encryption. Jeśli utworzysz maszynę wirtualną z dyskami niezarządzanymi przy użyciu obrazu z witryny Azure Marketplace, na platformie Azure zostanie utworzona [skrócona kopia](https://en.wikipedia.org/wiki/Object_copying) obrazu na konto magazynu w usłudze Azure Storage, która nie jest zaszyfrowana, nawet jeśli włączono funkcję SSE. Po utworzeniu maszyny wirtualnej i rozpoczęciu aktualizowania obrazu funkcja SSE rozpocznie szyfrowanie danych. Z tego powodu najlepiej używać Azure Disk Encryption na maszynach wirtualnych z dyskami niezarządzanymi utworzonymi na podstawie obrazów w portalu Azure Marketplace, jeśli chcesz je w pełni zaszyfrować. W przypadku tworzenia maszyny wirtualnej przy użyciu Managed Disks funkcja SSE domyślnie szyfruje wszystkie dane za pomocą kluczy zarządzanych przez platformę. 

W przypadku przełączenia wstępnie zaszyfrowanej maszyny wirtualnej na platformę Azure z lokalnego programu można przekazać klucze szyfrowania do Azure Key Vault i nadal używać szyfrowania dla tej maszyny wirtualnej, która była używana lokalnie. Azure Disk Encryption jest włączona, aby obsłużyć ten scenariusz.

W przypadku nieszyfrowanego dysku VHD z poziomu lokalnego można przekazać go do galerii jako obraz niestandardowy i zainicjować z niego maszynę wirtualną. Jeśli wykonujesz tę czynność przy użyciu szablonów Menedżer zasobów, możesz poproszony o włączenie Azure Disk Encryption podczas uruchamiania maszyny wirtualnej.

Po dodaniu dysku danych i zainstalowaniu go na maszynie wirtualnej można włączyć Azure Disk Encryption na tym dysku danych. Najpierw zaszyfruje ten dysk danych, a następnie warstwa klasycznego modelu wdrażania wykona zapis z opóźnieniem względem magazynu, aby zawartość magazynu była zaszyfrowana.

#### <a name="client-side-encryption"></a>Szyfrowania po stronie klienta
Szyfrowanie po stronie klienta to najbezpieczniejsza Metoda szyfrowania danych, ponieważ szyfruje dane przed ich przekazaniem.  Jednak wymaga to dodania kodu do aplikacji przy użyciu magazynu, którego nie trzeba robić. W takich przypadkach można zabezpieczyć dane podczas przesyłania przy użyciu protokołu HTTPS. Gdy dane docierają do usługi Azure Storage, są szyfrowane za pomocą instrukcji SSE.

Dzięki szyfrowaniu po stronie klienta można szyfrować jednostki tabeli, komunikaty w kolejce i obiekty blob. 

Szyfrowanie po stronie klienta jest zarządzane całkowicie przez aplikację. Jest to najbezpieczniejsze podejście, ale wymaga wprowadzenia programistycznych zmian do aplikacji i wprowadzenia procesów zarządzania kluczami. Użyjesz tej wartości, jeśli chcesz uzyskać dodatkowe zabezpieczenia podczas przesyłania, a dane przechowywane mają być szyfrowane.

Szyfrowanie po stronie klienta jest bardziej obciążenie klientem i należy je uwzględnić w planach skalowalności, zwłaszcza w przypadku szyfrowania i przesyłania dużej ilości danych.

#### <a name="storage-service-encryption-sse"></a>Szyfrowanie usługi Storage (SSE)

Funkcja SSE jest zarządzana przez usługę Azure Storage. Funkcja SSE nie zapewnia bezpieczeństwa danych podczas przesyłania, ale szyfruje dane podczas ich zapisywania w usłudze Azure Storage. Usługa SSE nie wpływa na wydajność usługi Azure Storage.

Możesz zaszyfrować dowolne dane na koncie magazynu przy użyciu instrukcji SSE (blokowe obiekty blob, dołączanie obiektów blob, stronicowe obiekty blob, dane tabeli, dane kolejki i pliki).

Jeśli masz archiwum lub bibliotekę plików VHD, których używasz jako podstawy do tworzenia nowych maszyn wirtualnych, możesz utworzyć nowe konto magazynu, a następnie przekazać pliki VHD do tego konta. Te pliki VHD zostaną zaszyfrowane za pomocą usługi Azure Storage.

Jeśli dla dysków w maszynie wirtualnej włączono Azure Disk Encryption, wszystkie nowo zapisywane dane są szyfrowane zarówno przez funkcję SSE, jak i Azure Disk Encryption.

## <a name="storage-analytics"></a>Analityka magazynu
### <a name="using-storage-analytics-to-monitor-authorization-type"></a>Używanie analityka magazynu do monitorowania typu autoryzacji
Dla każdego konta magazynu można włączyć analityka magazynu platformy Azure do wykonywania rejestrowania i przechowywania danych metryk. Jest to doskonałe narzędzie używane do sprawdzania metryk wydajności konta magazynu lub konieczności rozwiązywania problemów z kontem magazynu, ponieważ występują problemy z wydajnością.

Kolejną częścią danych, które można zobaczyć w dziennikach analizy magazynu, jest metoda uwierzytelniania używana przez kogoś podczas uzyskiwania dostępu do magazynu. Na przykład w przypadku Blob Storage można zobaczyć, czy użyły sygnatury dostępu współdzielonego lub kluczy konta magazynu, czy też dostęp do obiektu BLOB był publiczny.

Może to być przydatne, jeśli masz ścisłą ochronę dostępu do magazynu. Na przykład w Blob Storage można ustawić wszystkie kontenery jako prywatne i zaimplementować korzystanie z usługi sygnatury dostępu współdzielonego w aplikacjach. Następnie można regularnie sprawdzać dzienniki, aby sprawdzić, czy dostęp do obiektów BLOB odbywa się przy użyciu kluczy konta magazynu, co może wskazywać na naruszenie zabezpieczeń lub jeśli obiekty blob są publiczne, ale nie powinny być.

#### <a name="what-do-the-logs-look-like"></a>Jak wyglądają dzienniki?
Po włączeniu metryk konta magazynu i zarejestrowaniu za pomocą Azure Portal dane analityczne zaczną szybko zbierać się. Rejestrowanie i metryki dla każdej usługi są oddzielone; rejestrowanie jest zapisywane tylko wtedy, gdy istnieje aktywność na tym koncie magazynu, podczas gdy metryki będą rejestrowane co minutę, co godzinę lub codziennie, w zależności od sposobu jego konfiguracji.

Dzienniki są przechowywane w blokowych obiektach Blob w kontenerze o nazwie $logs na koncie magazynu. Ten kontener jest tworzony automatycznie, gdy analityka magazynu jest włączona. Po utworzeniu tego kontenera nie można go usunąć, chociaż można usunąć jego zawartość.

W kontenerze $logs istnieje folder dla każdej usługi, a następnie istnieją podfoldery dla roku/miesiąca/dnia/godziny. W obszarze godzina dzienniki są numerowane. Oto, jak będzie wyglądać struktura katalogów:

![Widok plików dziennika](./media/storage-security-guide/image1.png)

Każde żądanie do usługi Azure Storage jest rejestrowane. Oto migawka pliku dziennika zawierająca pierwsze kilka pól.

![Migawka pliku dziennika](./media/storage-security-guide/image2.png)

Można sprawdzić, czy można użyć dzienników do śledzenia dowolnego rodzaju wywołań do konta magazynu.

#### <a name="what-are-all-of-those-fields-for"></a>Co to są wszystkie pola?
W poniższych zasobach znajduje się artykuł zawierający listę wielu pól w dziennikach i ich użycia. Poniżej znajduje się lista pól w kolejności:

![Migawka pól w pliku dziennika](./media/storage-security-guide/image3.png)

Interesuje Cię wpisy dla elementu GetBlob i sposobu ich autoryzacji, dlatego musimy szukać wpisów z typem operacji "Get-BLOB" i sprawdzić stan żądania-status (czwartej </sup> kolumnie) i typ autoryzacji (ósma </sup> kolumny).

Na przykład w pierwszych kilku wierszach na powyższej liście żądanie-status ma wartość "powodzenie", a typ autoryzacji to "uwierzytelnione". Oznacza to, że żądanie było autoryzowane przy użyciu klucza konta magazynu.

#### <a name="how-is-access-to-my-blobs-being-authorized"></a>Jak uzyskuje się dostęp do moich obiektów blob, które są autoryzowane?
Mamy trzy przypadki, w których jesteśmy zainteresowani.

1. Obiekt BLOB jest publiczny i jest dostępny przy użyciu adresu URL bez sygnatury dostępu współdzielonego. W takim przypadku żądanie-status ma wartość "AnonymousSuccess", a typ autoryzacji to "Anonymous".

   1.0; 2015-11-17T02:01:29.0488963 Z; GetBlob **AnonymousSuccess**; 200; 124; 37; **anonimowe**;; Magazyn...
2. Obiekt BLOB jest prywatny i został użyty z sygnaturą dostępu współdzielonego. W takim przypadku żądanie-status ma wartość "SASSuccess", a typ autoryzacji to "SAS".

   1.0; 2015-11-16T18:30:05.6556115 Z; GetBlob **SASSuccess**; 200; 416; 64; **sygnatura dostępu współdzielonego**;; Magazyn...
3. Obiekt BLOB jest prywatny, a klucz magazynu został użyty w celu uzyskania do niego dostępu. W takim przypadku żądanie-status ma wartość "**powodzenie**", a typ autoryzacji to "**uwierzytelnione**".

   1.0; 2015-11-16T18:32:24.3174537 Z; GetBlob **Powodzenie**; 206; 59; 22; **uwierzytelniono**; magazyn...

Za pomocą programu Microsoft Message Analyzer można przeglądać i analizować te dzienniki. Obejmuje to możliwości wyszukiwania i filtrowania. Na przykład możesz chcieć wyszukać wystąpienia obiektu GetBlob, aby sprawdzić, czy użycie jest oczekiwane, czyli aby upewnić się, że ktoś nie uzyskuje dostępu do konta magazynu w sposób niewłaściwy.

#### <a name="resources"></a>Zasoby
* [Analityka magazynu](../storage-analytics.md)

  Ten artykuł zawiera omówienie analizy magazynu i sposobu ich włączania.
* [analityka magazynu format dziennika](https://msdn.microsoft.com/library/azure/hh343259.aspx)

  W tym artykule przedstawiono analityka magazynu format dziennika oraz szczegółowo dostępne pola, w tym typ uwierzytelniania, wskazujące typ uwierzytelniania używanego dla żądania.
* [Monitorowanie konta magazynu w Azure Portal](../storage-monitor-storage-account.md)

  W tym artykule przedstawiono sposób konfigurowania monitorowania metryk i rejestrowania dla konta magazynu.
* [Kompleksowe Rozwiązywanie problemów przy użyciu metryk usługi Azure Storage i rejestrowania, AzCopy i analizatora komunikatów](../storage-e2e-troubleshooting.md)

  W tym artykule omówiono Rozwiązywanie problemów przy użyciu analityka magazynu i przedstawiono sposób korzystania z programu Microsoft Message Analyzer.
* [Przewodnik operacyjny programu Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx)

  Ten artykuł jest odwołaniem do programu Microsoft Message Analyzer i zawiera linki do samouczka, przewodnika Szybki Start i podsumowania funkcji.

## <a name="cross-origin-resource-sharing-cors"></a>Współużytkowanie zasobów między źródłami (CORS, Cross-Origin Resource Sharing)
### <a name="cross-domain-access-of-resources"></a>Dostęp między domenami do zasobów
Gdy przeglądarka sieci Web działająca w jednej domenie wysyła żądanie HTTP do zasobu z innej domeny, jest to nazywane żądanie HTTP między źródłami. Na przykład strona HTML obsługiwana przez contoso.com wykonuje żądanie dotyczące JPEG hostowanego na fabrikam.blob.core.windows.net. Ze względów bezpieczeństwa przeglądarki ograniczają żądania HTTP między źródłami inicjowane przez skrypty, takie jak JavaScript. Oznacza to, że jeśli jakiś kod JavaScript na stronie sieci Web na contoso.com zażądał tego JPEG w fabrikam.blob.core.windows.net, przeglądarka nie zezwoli na żądanie.

Co należy zrobić z usługą Azure Storage? Ponadto, Jeśli przechowujesz statyczne zasoby, takie jak pliki danych JSON lub XML w Blob Storage przy użyciu konta magazynu o nazwie Fabrikam, domena dla zasobów zostanie fabrikam.blob.core.windows.net, a aplikacja sieci Web contoso.com nie będzie mogła uzyskać do nich dostępu za pomocą JavaScript, ponieważ domeny są różne. Jest to również prawdziwe, jeśli próbujesz wywołać jedną z usług Azure Storage, np. Table Storage — zwraca dane JSON do przetworzenia przez klienta JavaScript.

#### <a name="possible-solutions"></a>Możliwe rozwiązania
Jednym ze sposobów na rozwiązanie tego problemu jest przypisanie domeny niestandardowej, takiej jak "storage.contoso.com", do fabrikam.blob.core.windows.net. Problem polega na tym, że można przypisać tylko tę domenę niestandardową do jednego konta magazynu. Co zrobić, jeśli zasoby są przechowywane na wielu kontach magazynu?

Innym sposobem na rozwiązanie tego problemu jest to, że aplikacja sieci Web działa jako serwer proxy dla wywołań magazynu. Oznacza to, że w przypadku przekazywania pliku do Blob Storage, aplikacja sieci Web zapisze ją lokalnie, a następnie skopiuje ją do Blob Storage lub odczytaje wszystkie pliki do pamięci, a następnie zapisze ją w Blob Storage. Alternatywnie można napisać dedykowaną aplikację sieci Web (na przykład internetowy interfejs API), która przekazuje pliki lokalnie i zapisuje je do Blob Storage. W obu przypadkach należy uwzględnić tę funkcję podczas określania potrzeb dotyczących skalowalności.

#### <a name="how-can-cors-help"></a>Jak można uzyskać pomoc dotyczącą mechanizmu CORS?
Usługa Azure Storage umożliwia włączenie funkcji CORS — udostępnianie zasobów między źródłami. Dla każdego konta magazynu można określić domeny, które mogą uzyskiwać dostęp do zasobów w ramach tego konta magazynu. Przykładowo w naszym przypadku można włączyć funkcję CORS na koncie magazynu fabrikam.blob.core.windows.net i skonfigurować ją w taki sposób, aby zezwalała na dostęp do contoso.com. Następnie aplikacja sieci Web contoso.com może bezpośrednio uzyskać dostęp do zasobów w fabrikam.blob.core.windows.net.

Należy pamiętać, że funkcja CORS zezwala na dostęp, ale nie zapewnia uwierzytelniania, co jest wymagane dla wszystkich niepubliczny dostęp do zasobów magazynu. Oznacza to, że można uzyskać dostęp tylko do obiektów blob, jeśli są one publiczne lub zawierają sygnaturę dostępu współdzielonego z odpowiednimi uprawnieniami. Tabele, kolejki i pliki nie mają publicznego dostępu i wymagają skojarzenia zabezpieczeń.

Domyślnie mechanizm CORS jest wyłączony na wszystkich usługach. Mechanizm CORS można włączyć za pomocą interfejsu API REST lub biblioteki klienta usługi Storage w celu wywołania jednej z metod ustawiania zasad usługi. W takim przypadku należy uwzględnić regułę CORS, która znajduje się w formacie XML. Oto przykład reguły CORS, która została ustawiona przy użyciu operacji ustawiania właściwości usługi dla usługi BLOB dla konta magazynu. Tę operację można wykonać przy użyciu biblioteki klienta magazynu lub interfejsów API REST usługi Azure Storage.

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

Oto co oznacza każdy wiersz:

* **AllowedOrigins** Oznacza to, że niezgodne domeny mogą żądać i odbierać dane z usługi magazynu. Oznacza to, że zarówno contoso.com, jak i fabrikam.com mogą żądać danych z Blob Storage dla określonego konta magazynu. Możesz również ustawić tę opcję na symbol wieloznaczny (\*), aby zezwolić na dostęp do żądań wszystkim domenom.
* **Atrybut AllowedMethods** Jest to lista metod (czasownik żądania HTTP), które mogą być używane podczas wykonywania żądania. W tym przykładzie dozwolone są tylko PUT i GET. Możesz ustawić tę wartość na symbol wieloznaczny (\*), aby umożliwić korzystanie ze wszystkich metod.
* **AllowedHeaders** To są nagłówki żądań, które domena pochodzenia może określić podczas wykonywania żądania. W tym przykładzie wszystkie nagłówki metadanych zaczynające się od wartości x-MS-meta-data, x-MS-meta i x-MS-meta-ABC są dozwolone. Symbol wieloznaczny (\*) wskazuje, że dozwolony jest dowolny nagłówek zaczynający się od określonego prefiksu.
* **ExposedHeaders** Wskazuje to, które nagłówki odpowiedzi powinny być uwidocznione przez przeglądarkę do wystawcy żądania. W tym przykładzie zostanie uwidoczniony nagłówek zaczynający się od "x-MS-meta-".
* **Atrybut maxageinseconds** Jest to maksymalny czas buforowania żądania opcji inspekcji wstępnej w przeglądarce. (Aby uzyskać więcej informacji na temat żądania wstępnego, zapoznaj się z pierwszym artykułem poniżej).

#### <a name="resources"></a>Zasoby
Aby uzyskać więcej informacji na temat mechanizmu CORS i sposobu jego włączania, zapoznaj się z tymi zasobami.

* [Obsługa udostępniania zasobów między źródłami (CORS) dla usług Azure Storage w systemie Azure.com](../storage-cors-support.md)

  Ten artykuł zawiera omówienie mechanizmu CORS i sposobu ustawiania reguł dla różnych usług magazynu.
* [Obsługa udostępniania zasobów między źródłami (CORS) dla usług Azure Storage w witrynie MSDN](https://msdn.microsoft.com/library/azure/dn535601.aspx)

  Jest to dokumentacja referencyjna dotycząca obsługi mechanizmu CORS dla usług Azure Storage. Zawiera łącza do artykułów, które są stosowane do każdej usługi magazynu, i pokazuje przykład i objaśnia każdy element w pliku CORS.
* [Microsoft Azure Storage: wprowadzenie CORS](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/02/03/windows-azure-storage-introducing-cors.aspx)

  Jest to link do początkowego artykułu blogu z informacją, jak go używać.

## <a name="frequently-asked-questions-about-azure-storage-security"></a>Często zadawane pytania dotyczące zabezpieczeń usługi Azure Storage
1. **Jak sprawdzić integralność obiektów blob, które są transferowane do lub z usługi Azure Storage, jeśli nie można użyć protokołu HTTPS?**

   Jeśli z jakiegoś powodu musisz używać protokołu HTTP zamiast HTTPS i pracujesz z blokowymi obiektami BLOB, możesz użyć sprawdzania MD5, aby pomóc w sprawdzeniu integralności transferowanych obiektów BLOB. Ułatwi to ochronę przed błędami warstwy sieci i transportu, ale nie musi być atakami pośredniczącymi.

   Jeśli można użyć protokołu HTTPS, który zapewnia zabezpieczenia na poziomie transportu, użycie sprawdzania MD5 jest zbędne i niepotrzebne.

   Aby uzyskać więcej informacji, zobacz [Omówienie algorytmu MD5 obiektów blob platformy Azure](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/18/windows-azure-blob-md5-overview.aspx).
2. **Co o zgodności ze standardem FIPS dla instytucji rządowych USA?**

   Stany Zjednoczone Federal Information Processing Standard (FIPS) definiuje algorytmy kryptograficzne zatwierdzone do użycia przez Federalne systemy komputerowe dla instytucji rządowych w Stanach ochrony danych poufnych. Włączenie trybu FIPS na serwerze lub pulpicie systemu Windows informuje system operacyjny, że powinny być używane tylko algorytmy kryptograficzne zatwierdzone przez FIPS. Jeśli aplikacja używa niezgodnych algorytmów, aplikacje zostaną przerwane. With.NET Framework w wersji 4.5.2 lub nowszej aplikacja automatycznie przełącza algorytmy kryptografii tak, aby korzystały z algorytmów zgodnych ze standardem FIPS, gdy komputer jest w trybie FIPS.

   Firma Microsoft pozostawi do każdego klienta, aby zdecydować, czy włączyć tryb FIPS. Uważamy, że nie ma żadnych istotnych przyczyn dla klientów, którzy nie podlegają regulacjom rządowym, aby domyślnie włączyć tryb FIPS.

### <a name="resources"></a>Zasoby
* [Dlaczego nie zalecamy już "trybu FIPS"](https://blogs.technet.microsoft.com/secguide/2014/04/07/why-were-not-recommending-fips-mode-anymore/)

  Ten artykuł w blogu zawiera omówienie standardu FIPS i wyjaśnia, dlaczego domyślnie nie włącza trybu FIPS.
* [Walidacja standardu FIPS 140](https://technet.microsoft.com/library/cc750357.aspx)

  Ten artykuł zawiera informacje dotyczące sposobu, w jaki produkty i moduły kryptograficzne firmy Microsoft są zgodne ze standardem FIPS dla instytucji rządowych Stanów Zjednoczonych.
* ["Kryptografia systemu: Użyj zgodnych algorytmów FIPS do szyfrowania, mieszania i podpisywania" efektów ustawień zabezpieczeń w systemie Windows XP i nowszych wersjach systemu Windows](https://support.microsoft.com/kb/811833)

  W tym artykule omówiono użycie trybu FIPS w starszych komputerach z systemem Windows.
