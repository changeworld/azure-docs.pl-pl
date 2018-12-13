---
title: Kluczowe funkcje i pojęcia w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Poznaj najważniejsze funkcje i pojęcia w usłudze Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 09ca32b7-0e81-4a27-a6cc-0ba90441d097
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2018
ms.author: jeffgilb
ms.reviewer: ''
ms.openlocfilehash: 21a6eeb4b0a83574be4c5c996e43d9867c3249d0
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2018
ms.locfileid: "53185736"
---
# <a name="key-features-and-concepts-in-azure-stack"></a>Kluczowe funkcje i pojęcia w usłudze Azure Stack
Jeśli jesteś nowym użytkownikiem usługi Microsoft Azure Stack, te warunki i opisy funkcji może być przydatny.

## <a name="personas"></a>Osoby
Istnieją dwie odmiany użytkowników dla usługi Microsoft Azure Stack, Operator oraz użytkownika.

* Azure Stack **Operator** można skonfigurować usługi Azure Stack Zarządzanie ofertami, plany, usługi, limity przydziału i cennika użytkownikom zasobów dla swojej dzierżawy. Operatory również zarządzanie pojemnością i odpowiadanie na alerty.  
* Azure Stack **użytkownika** (określane również jako dzierżawca) korzysta z usług oferowanych przez operatora. Użytkowników można aprowizować, monitorowanie i zarządzanie usługami, które masz subskrypcję, takich jak aplikacje sieci web, magazynu i maszyn wirtualnych.

## <a name="portal"></a>Portal
Podstawowe metody interakcji z usługą Microsoft Azure Stack to portal administratora portalu użytkowników i programu PowerShell.

Każdy portali usługi Azure Stack są wspierane przez osobnych wystąpień usługi Azure Resource Manager. Operator używa portalu administracyjnego do zarządzania usługi Azure Stack i do wykonywania czynności takich, jak utworzyć dzierżawę ofert. Portal użytkowników (nazywane również portalu dzierżawcy) zapewnia Samoobsługowe zużycia zasobów w chmurze, takich jak maszyny wirtualne, konta magazynu i aplikacje sieci web. Aby uzyskać więcej informacji, zobacz [przy użyciu portali administratora i użytkownika usługi Azure Stack](azure-stack-manage-portals.md).

## <a name="identity"></a>Tożsamość 
Usługa Azure Stack korzysta z usługi Azure Active Directory (Azure AD) lub usługi Active Directory Federation Services (AD FS) jako dostawcy tożsamości.  

### <a name="azure-active-directory"></a>Usługa Azure Active Directory
Usługa Azure AD jest dostawca tożsamości wielodostępnych, oparte na chmurze firmy Microsoft. Większość scenariuszy hybrydowych używać usługi Azure AD jako magazynu tożsamości.

### <a name="active-directory-federation-services"></a>Usługi Active Directory Federation Services
Można użyć Active Directory Federation Services (AD FS) w przypadku odłączonych wdrożeń usługi Azure Stack. Dostawcy zasobów usługi Azure Stack i inne aplikacje działają podobnie jak z usługami AD FS tak samo, jak za pomocą usługi Azure AD. Usługa Azure Stack obejmuje własne wystąpienie usługi Active Directory i Active interfejsu API programu Graph katalogu. Usługa Azure Stack Development Kit obsługuje następujące scenariusze usług AD FS:

- Zaloguj się do wdrożenia przy użyciu usług AD FS.
- Utwórz maszynę wirtualną przy użyciu kluczy tajnych w usłudze Key Vault
- Utwórz magazyn do przechowywania/uzyskiwania dostępu do kluczy tajnych
- Tworzenie niestandardowych ról RBAC w ramach subskrypcji
- Przypisywanie użytkowników do ról RBAC dla zasobów
- Tworzenie ról RBAC całego systemu za pomocą programu Azure PowerShell
- Zaloguj się jako użytkownik za pomocą programu Azure PowerShell
- Tworzenie usługi podmiotów zabezpieczeń należy ich używać do logowania do programu Azure PowerShell


## <a name="regions-services-plans-offers-and-subscriptions"></a>Regiony, usług, plany, oferty i subskrypcje
W usłudze Azure Stack usługi są dostarczane dzierżawcom przy użyciu regionów, subskrypcji, oferty i plany. Dzierżawcy mogą subskrybować wiele ofert. Oferty, może mieć co najmniej jeden plan i planów może mieć co najmniej jednej usługi.

![](media/azure-stack-key-features/image4.png)

Przykładowa hierarchia subskrypcji dzierżawcy przy użyciu różnych ofert usług i planów.

### <a name="regions"></a>Regiony
Regiony świadczenia usługi Azure Stack to podstawowy element skali i zarządzanie. Organizacja może mieć wielu regionów za pomocą dostępnych zasobów w każdym regionie. Regiony może być również dostępne oferty innej usługi. W usłudze Azure Stack Development Kit, jest obsługiwana tylko pojedynczy region, a ma automatycznie nadawaną nazwę *lokalnego*.

### <a name="services"></a>Usługi
Microsoft Azure Stack umożliwia dostawcom dostarczać szerokiej gamy usług i aplikacji, takich jak maszyny wirtualne programu SQL Server baz danych, SharePoint, Exchange i więcej.

### <a name="plans"></a>Plany
Plany są grupowania co najmniej jednej usługi. Jako dostawca możesz utworzyć plany do zaoferowania dzierżawcom. Z kolei dzierżawcy mogą subskrybować oferty, aby korzystać z planów i objętych nimi usług.

Każda usługa dodawane do planu można skonfigurować ustawienia limitu przydziału, aby ułatwić zarządzanie pojemnością chmury. Przydziały mogą zawierać ograniczenia, takie jak limity maszyn wirtualnych, pamięci RAM i procesora CPU i są stosowane na subskrypcję użytkownika. Przydziały mogą być rozróżniane według lokalizacji. Na przykład plan zawierające usług obliczeniowych z regionu, A może mieć limit przydziału dwie maszyny wirtualne, 4 GB pamięci RAM oraz 10 rdzeni procesora CPU.

Podczas tworzenia oferty, administrator usługi może zawierać **plan podstawowy**. Te plany podstawowe znajdują się domyślnie, gdy dzierżawca subskrybuje tę ofertę. Gdy użytkownik subskrybuje i subskrypcja zostanie utworzony, użytkownik ma dostęp do wszystkich dostawców zasobów, określone w tych planów podstawowych (z odpowiednie limity przydziału).

Administrator usługi może także zawierać **planów dodatków** w ofercie. Plany dodatkowe nie są uwzględniane domyślnie w ramach subskrypcji. Plany dodatkowe są plany dodatkowe (przydziały) dostępnych w ofercie, właściciel subskrypcji można dodać do swojej subskrypcji.

### <a name="offers"></a>Oferta
Oferty są grupami co najmniej jeden plan, które dostawcy przedstawiają dzierżawcom do zasubskrybowania (zakupienia). Na przykład alfa oferuje może zawierać Plan A zawierający zestaw usług obliczeniowych i planowanie B, zawierające zestaw usług magazynu i sieci.

Oferty, który jest dostarczany z zestawem planów podstawowych, a administratorzy usług mogą tworzyć plany dodatkowe, które dzierżaw może dodać do swojej subskrypcji.

### <a name="subscriptions"></a>Subskrypcje
Subskrypcja stanowi sposób dzierżaw kupowanie ofert. Subskrypcja stanowi kombinację dzierżawy z ofertą. Dzierżawca może mieć subskrypcji wiele ofert. Każda subskrypcja ma zastosowanie do tylko jednej oferty. Subskrypcji dzierżawcy Określ mogą uzyskiwać dostęp do planów/usługi.

Subskrypcje pomocy dostawców, organizowanie i dostęp do zasobów w chmurze i usług.

Dla administratora subskrypcję dostawcy domyślne jest tworzone podczas wdrażania. Ta subskrypcja może służyć do zarządzania usługi Azure Stack, dodatkowo wdrażanie dostawcy zasobów i utworzyć plany i oferty dla dzierżawców. Nie można stosować do uruchamiania obciążeń klientów i aplikacji. Począwszy od wersji 1804 dwóch dodatkowych subskrypcji uzupełniają domyślne dostawcy subskrypcji; Subskrypcja zliczania i użycie subskrypcji. Te dodatki ułatwiają oddzielenie zarządzania podstawowej infrastruktury, dostawców dodatkowych zasobów i obciążeń.  

## <a name="azure-resource-manager"></a>Azure Resource Manager
Za pomocą usługi Azure Resource Manager, można pracować z zasobami infrastruktury w modelu opartym na szablonie, deklaratywny.   Zapewnia jeden interfejs, który służy do wdrażania i zarządzania nimi składniki rozwiązania. Aby uzyskać pełne informacje i wskazówki, zobacz [Omówienie usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

### <a name="resource-groups"></a>Grupy zasobów
Grupy zasobów to zbiór zasobów, usług i aplikacji — i każdy zasób ma typ, takie jak maszyny wirtualne, sieci wirtualne, publiczne adresy IP, kont magazynu i witryn sieci Web. Każdy zasób musi należeć do grupy zasobów i tak grup zasobów pomóc logicznie organizowania zasobów, takich jak obciążenia lub lokalizacji. Zasoby, takie jak plany i oferty w usłudze Azure Stack, również odbywa się w grupach zasobów.

W odróżnieniu od [Azure](../azure-resource-manager/resource-group-move-resources.md), nie można przenieść zasobów usługi Azure Stack, między grupami zasobów. Podczas przeglądania właściwości zasobu lub grupy zasobów w portalu administracyjnego usługi Azure Stack *przenieść* przycisk jest wyszarzona i niedostępna. Ponadto użycie **Zmień grupę zasobów** lub **zmienić subskrypcję** akcji z grupy zasobów lub właściwości elementu grupy zasobów nie jest również obsługiwany. Wszystkich prób przenosić operacje zakończą się niepowodzeniem.
 
### <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager
Za pomocą usługi Azure Resource Manager można utworzyć szablon (w formacie JSON) do definiowania wdrażania i konfiguracji aplikacji. Ten szablon jest znany jako szablonu usługi Azure Resource Manager i pozwala na deklaratywne Definiowanie wdrożeń. Dzięki szablonowi można wielokrotnie wdrażać aplikację w całym jej cyklu życia z gwarancją spójnego stanu zasobów po każdym wdrożeniu.

## <a name="resource-providers-rps"></a>Dostawcy zasobów (jednostek uzależnionych)
Dostawcy zasobów to usługi sieci web, które stanowią podstawę dla wszystkich opartych na platformie Azure IaaS i PaaS usług. Usługa Azure Resource Manager, zależy od różnych RPs, aby zapewnić dostęp do usług.

Istnieją cztery podstawowe RPs: Sieci, magazynowania, obliczeń i magazynu kluczy. Każda z tych RPs ułatwi Ci skonfigurowanie i kontrolować jej odpowiednich zasobów. Administratorzy usługi można również dodać nowych dostawców zasobów niestandardowych.

### <a name="compute-rp"></a>Obliczenia RP
Dostawcy zasobów obliczeniowych (CRP) umożliwia usłudze Azure Stack dzierżawcom tworzenie własnych maszyn wirtualnych. Rozwiązanie CRP obejmuje możliwość tworzenia maszyn wirtualnych, a także rozszerzenia maszyny wirtualnej. Usługa rozszerzenia maszyny wirtualnej zapewnia możliwości IaaS dla maszyn wirtualnych Windows i Linux.  Na przykład rozwiązanie CRP służy również do aprowizowania maszyny wirtualnej z systemem Linux i uruchamiać skrypty powłoki systemowej podczas wdrażania, aby skonfigurować maszynę Wirtualną.

### <a name="network-rp"></a>Sieć RP
Dostawca zasobów sieciowych (NRP) zapewnia szereg funkcji oprogramowania programowalnej sieci komputerowej (SDN) i sieci funkcja wirtualizacji (NFV) w ramach chmury prywatnej.  Dostawca NRP umożliwia tworzenie zasobów, takich jak oprogramowanie obciążenia równoważenia i publiczne adresy IP, sieciowe grupy zabezpieczeń, sieci wirtualnych.

### <a name="storage-rp"></a>Magazyn RP
RP magazynu zapewnia cztery spójnych z platformą Azure usługi storage: obiektów blob, tabeli, kolejki i zarządzanie kontem. Zapewnia ona również magazynu usługi administracyjnej w chmurze w celu ułatwienia usługi zarządzania dostawcy usług spójnych z platformą Azure Storage. Usługa Azure Storage zapewnia elastyczność do przechowywania i pobierania dużych ilości danych niestrukturalnych, takich jak dokumenty i pliki multimedialne za pomocą obiektów blob platformy Azure, a NoSQL ze strukturą na podstawie danych z tabel platformy Azure. Aby uzyskać więcej informacji na temat usługi Azure Storage, zobacz [wprowadzenie do usługi Microsoft Azure Storage](../storage/common/storage-introduction.md).

#### <a name="blob-storage"></a>Blob Storage
Magazyn obiektów blob przechowuje każdego zestawu danych. Obiekt blob może być dowolnymi danymi tekstowymi lub binarnymi, takimi jak dokument, plik multimedialny lub instalator aplikacji. Usługa TABLE storage przechowuje zestawy danych ze strukturą. Magazyn tabel jest magazynem typu NoSQL zawierającym pary klucz-atrybut, co umożliwia szybkie opracowywanie i szybki dostęp do dużych ilości danych. Usługa queue storage umożliwia niezawodną obsługę komunikatów dla przetwarzania przepływu pracy i komunikacji między składnikami usług w chmurze.

Każdy obiekt blob jest zorganizowana w kontenerze. Kontenery to także wygodny sposób na przypisywanie zasad zabezpieczeń do grup obiektów. Konto magazynu może zawierać dowolną liczbę kontenerów, a kontener może zawierać dowolną liczbę obiektów blob w granicach 500 TB pojemności konta magazynu. Magazyn obiektów blob udostępnia trzy typy obiektów blob: blokowe obiekty blob, uzupełnialne obiekty blob i stronicowe obiekty blob (dyski). Blokowe obiekty blob są zoptymalizowane pod kątem przesyłania strumieniowego i przechowywania obiektów w chmurze i dobrze nadają się do przechowywania dokumentów, plików multimedialnych, kopii zapasowych itd. Uzupełnialne obiekty blob są podobne do blokowych obiektów blob, lecz są zoptymalizowane pod kątem operacji dołączania. Uzupełnialny obiekt blob można zaktualizować tylko przez dodanie nowego bloku na końcu. Uzupełnialne obiekty blob są dobrym rozwiązaniem w przypadku scenariuszy takich jak rejestrowanie, które wymaga zapisywania tylko na końcu obiektu blob. Stronicowe obiekty BLOB są zoptymalizowane pod kątem reprezentowania dysków IaaS i obsługi losowych zapisów i może być do 1 TB. Dysk IaaS dołączony do sieci maszyny wirtualnej platformy Azure to plik VHD przechowywany jako stronicowy obiekt blob.

#### <a name="table-storage"></a>Magazyn tabel
Magazyn tabel to magazyn klucz atrybut NoSQL firmy Microsoft — przedstawiono w nim projektu bez schematów, czym różni się od tradycyjnych relacyjnych baz danych. Ponieważ schematy Brak magazynów danych, jest można łatwo zaadaptować dane aplikacji rozwijających się potrzeb. Magazyn tabel jest łatwy w użyciu, dzięki czemu deweloperzy mogą szybko tworzyć aplikacje. Magazyn tabel to magazyn zawierający pary klucz-atrybut, co oznacza, że każda wartość w tabeli jest przechowywana razem z nazwą właściwości z określonym typem. Nazwa właściwości może służyć do filtrowania i określania kryteriów wyboru. Kolekcja właściwości i ich wartości stanowi jednostkę. Od tabeli magazynu braku schematów dwie jednostki w tej samej tabeli mogą zawierać różne kolekcje właściwości, a te właściwości mogą być różnych typów. Magazyn tabel umożliwia przechowywanie elastycznych zestawów danych, takich jak dane użytkownika dla aplikacji internetowych, książki adresowe, informacje o urządzeniach i wszelkie inne metadane, których wymaga Twoja usługa. W tabeli można przechowywać dowolną liczbę jednostek, a konto magazynu może zawierać dowolną liczbę tabel w granicach pojemności konta magazynu.

#### <a name="queue-storage"></a>Queue Storage
Usługa Azure Queue Storage umożliwia przesyłanie komunikatów za pomocą chmury między składnikami aplikacji. W przypadku projektowania aplikacji pod kątem skalowania składniki aplikacji są często rozłączane, dzięki czemu mogą być skalowane niezależnie. Usługa Queue Storage zapewnia asynchroniczne przesyłanie komunikatów na potrzeby komunikacji między składnikami aplikacji niezależnie od tego, czy działają w chmurze, na komputerze, serwerze lokalnym czy urządzeniu przenośnym. Usługa Queue Storage obsługuje również zarządzanie asynchronicznymi zadaniami oraz przepływy pracy procesu kompilacji.

### <a name="keyvault"></a>KeyVault
KeyVault RP zapewnia zarządzanie i inspekcji kluczy tajnych, takich jak hasła i certyfikaty. Na przykład dzierżawca umożliwia KeyVault RP podać hasło administratora lub kluczy podczas wdrażania maszyny Wirtualnej.

## <a name="high-availability-for-azure-stack"></a>Wysoka dostępność dla usługi Azure Stack
*Dotyczy: Usługa Azure Stack 1802 lub nowsze wersje*

Aby osiągnąć wysoką dostępność w systemie produkcyjnym wielu maszyn wirtualnych na platformie Azure, maszyny wirtualne są umieszczane w zestawie dostępności, który rozprzestrzenia się je w wielu domenach błędów i domenach aktualizacji. W ten sposób [maszyn wirtualnych wdrożonych w zestawach dostępności](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) są fizycznie odizolowane od siebie nawzajem na osobnym serwerze stojakami umożliwiające odporności błędów, jak pokazano na poniższym diagramie:

  ![Usługa Azure Stack wysokiej dostępności.](media/azure-stack-key-features/high-availability.png)

### <a name="availability-sets-in-azure-stack"></a>Zestawy dostępności w usłudze Azure Stack
Infrastruktury Azure Stack już jest odporna na awarie, podstawową używaną technologią (klaster trybu failover) nadal jest naliczana pewien przestój w przypadku maszyn wirtualnych na serwerze fizycznym nimi objęte w przypadku awarii sprzętu. Usługa Azure Stack obsługuje o dostępności zestawu z maksymalnie trzy domeny błędów, aby były zgodne z platformą Azure.

- **Domeny błędów**. Maszyny wirtualne umieszczone w zestawie dostępności będą fizycznie odizolowane od siebie nawzajem Dzięki rozdzieleniu je możliwie najbardziej równomiernie przez wiele domen błędów (węzłów usługi Azure Stack). W przypadku awarii sprzętu maszyn wirtualnych na podstawie domena błędów nie powiodło się będzie można ponownie uruchomiona w innych domenach błędów, ale jeśli to możliwe, przechowywane w oddzielnych domenach błędów z innymi maszynami wirtualnymi w tym samym zestawie dostępności. Gdy sprzęt powróci do trybu online, maszyn wirtualnych zostanie ponownie zbilansowana do utrzymania wysokiej dostępności. 
 
- **Domeny aktualizacji**. Domeny aktualizacji są innego koncepcji platformy Azure, która zapewnia wysoką dostępność w zestawach dostępności. Domena aktualizacji to logiczna grupa bazowego sprzętu, który może poddawane konserwacji, w tym samym czasie. Maszyn wirtualnych znajdujących się w tej samej domenie aktualizacji będą się ponownie uruchamiane razem podczas zaplanowanej konserwacji. Jak dzierżawcom Tworzenie maszyn wirtualnych w zestawie dostępności, platforma Azure automatycznie rozdziela maszyny wirtualne między domeny aktualizacji. W usłudze Azure Stack maszyny wirtualne działają migracji w trybie online hosty w klastrze, przed ich odpowiedniego hosta jest aktualizowana. Ponieważ podczas aktualizacji hosta bez przestojów dzierżawy, funkcji domeny aktualizacji w usłudze Azure Stack występuje tylko w przypadku szablonu zgodności z platformą Azure. 

### <a name="upgrade-scenarios"></a>Scenariusze uaktualniania 
Maszyny wirtualne w zestawach dostępności, które zostały utworzone przed Azure Stack w wersji 1802 otrzymują domyślną liczbę domen aktualizacji i usterek (1 i 1 odpowiednio). Uzyskanie wysokiej dostępności dla maszyn wirtualnych w tych istniejących zestawów dostępności, należy najpierw usunąć istniejących maszyn wirtualnych i następnie ponownie wdrożyć je na nowy zestaw dostępności z poprawną liczby domen błędów i aktualizacji zgodnie z opisem w [zmiany zbiór dostępności dla maszyny Wirtualnej z systemem Windows](https://docs.microsoft.com/azure/virtual-machines/windows/change-availability-set). 

Dla zestawów skalowania maszyn wirtualnych, zestaw dostępności jest tworzona wewnętrznie z domyślnej domeny i zaktualizuj liczbę domen błędów (3 i 5 odpowiednio). Zlicza domyślnej domeny błędów i aktualizacji wszystkie zestawy skalowania maszyn wirtualnych utworzonych przed aktualizacji 1802 zostaną umieszczone w zestaw dostępności (1 i 1 odpowiednio). Aby zaktualizować tych wystąpień zestawu skalowania maszyn wirtualnych do osiągnięcia nowszej rozprzestrzeniania się, skalować zestawy skalowania maszyn wirtualnych według liczby wystąpień, które znajdowały się przed aktualizacją 1802, a następnie usuń starszą wystąpień zestawów skalowania maszyn wirtualnych. 

## <a name="role-based-access-control-rbac"></a>Oparta na rolach kontrola dostępu (RBAC)
RBAC służy do udzielania dostępu do systemu do grona upoważnionych użytkowników, grup i usług, przypisując im role w subskrypcji, grupy zasobów lub poszczególnych zasobów. Każda rola definiuje poziom dostępu użytkownika, grupy lub usługi ma nad zasobami Microsoft Azure Stack.

RBAC platformy Azure ma trzy podstawowe role, które są stosowane do wszystkich typów zasobów: Właściciela, współautora i czytelnika. Właściciel ma pełny dostęp do wszystkich zasobów łącznie z prawej strony można delegować dostępu do innych osób. Współautor mogą tworzyć i zarządzać nimi wszystkie typy zasobów platformy Azure, ale nie może przyznawać dostępu innym osobom. Czytelnik mogą wyświetlać tylko istniejących zasobów platformy Azure. Pozostałe role RBAC na platformie Azure umożliwiają zarządzanie określonych zasobów platformy Azure. Na przykład rola Współautor maszyny wirtualnej umożliwia tworzenie i zarządzanie maszynami wirtualnymi, ale nie jest możliwe zarządzanie sieci wirtualnej lub podsieci, która łączy się z maszyny wirtualnej.

## <a name="usage-data"></a>Dane użycia
Microsoft Azure Stack umożliwia zbieranie informacji o agreguje dane dotyczące użycia we wszystkich dostawców zasobów i przekazuje go do platformy Azure do przetwarzania przez handlu platformy Azure. Można wyświetlić danych użycia zbieranych w usłudze Azure Stack za pośrednictwem interfejsu API REST. Istnieje interfejs API dzierżawcy spójnych z platformą Azure, a także dostawcy i delegować interfejsy API dostawcy można pobrać danych użycia dla wszystkich subskrypcji dzierżawcy. Te dane można zintegrować z zewnętrznego narzędzia lub usługi do rozliczeń i obciążeń zwrotnych. Po przetworzeniu użycia platformy Azure handlu mogą można wyświetlić w portalu rozliczeń systemu Azure.

## <a name="next-steps"></a>Kolejne kroki
[Podstawy administracji](azure-stack-manage-basics.md)

