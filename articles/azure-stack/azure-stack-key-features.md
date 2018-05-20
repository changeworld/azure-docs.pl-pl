---
title: Główne funkcje i pojęcia dotyczące stosu Azure | Dokumentacja firmy Microsoft
description: Informacje o najważniejszych funkcjach i pojęcia dotyczące stosu Azure.
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
ms.date: 05/10/2018
ms.author: jeffgilb
ms.reviewer: ''
ms.openlocfilehash: 851530910c702d388cd4dc8607bf09ecb5fa44e0
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="key-features-and-concepts-in-azure-stack"></a>Najważniejsze funkcje i pojęcia dotyczące stosu Azure
Jeśli jesteś nowym użytkownikiem programu Microsoft Azure stosu, te warunki i opisy funkcji mogą być pomocne.

## <a name="personas"></a>Osoby
Istnieją dwie odmiany użytkowników dla programu Microsoft Azure stosu operatorowi chmury (provider) i dzierżawców (użytkownik).

* A **operatorowi chmury** można konfigurować stosu Azure i zarządzać oferty, planów usług, przydziały i cenach zapewnienie zasobów dla swoich dzierżaw.  Operatorzy chmur również zarządzanie wydajnością i reagowania na alerty.  
* A **dzierżawy** (określane również jako użytkownik) korzysta z usług, które oferuje administrator chmury. Dzierżawcy mogą udostępniania, monitorowania i zarządzania usługami, które one subskrybowanych, takich jak aplikacje sieci Web, magazynu i maszyn wirtualnych.

## <a name="portal"></a>Portal
Podstawowe metody interakcji z Microsoft Azure stosu są portalu administratora portalu użytkownika i środowiska PowerShell.

Portale stosu Azure poszczególnych obsługiwanych przez oddzielnego wystąpienia usługi Azure Resource Manager.  Operatorowi chmury używa portalu administratora do zarządzania stosu Azure i wykonywanie czynności, takich jak tworzenie dzierżawy oferty.  Portal użytkownika (zwaną także portalu dzierżawcy) zapewnia samoobsługi zużycia zasobów w chmurze, takich jak maszyny wirtualne, konta magazynu i aplikacje sieci Web. Aby uzyskać więcej informacji, zobacz [przy użyciu portali administratora i użytkownika stosu Azure](azure-stack-manage-portals.md).

## <a name="identity"></a>Tożsamość 
Stos Azure używa usługi Azure Active Directory (AAD) lub usługi Active Directory Federation Services (AD FS) jako dostawcy tożsamości.  

### <a name="azure-active-directory"></a>Usługa Azure Active Directory
Azure Active Directory jest dostawca tożsamości opartej na chmurze, wielodostępne firmy Microsoft.  Większość scenariuszy hybrydowych używać usługi Azure Active Directory jako magazynu tożsamości.

### <a name="active-directory-federation-services"></a>Usługi federacyjne Active Directory
Możesz użyć Active Directory Federation Services (AD FS) wdrożeń odłączonego stosu Azure.  Azure stosu, dostawców zasobów i inne aplikacje działają podobnie jak z usługami AD FS tak jak w usłudze Azure Active Directory. Stos Azure obejmuje wystąpienia usług AD FS i usługi Active Directory i Active Directory interfejsu API programu Graph. Azure stosu Development Kit obsługuje następujące scenariusze usług AD FS:

- Zaloguj się do wdrożenia przy użyciu usług AD FS.
- Utwórz maszynę wirtualną z kluczy tajnych w magazynie kluczy
- Tworzenie magazynu do przechowywania/uzyskiwania dostępu do kluczy tajnych
- Utwórz niestandardowe role RBAC w subskrypcji
- Przypisywanie użytkowników do ról RBAC zasobów
- Tworzyć role RBAC całego systemu za pomocą programu Azure PowerShell
- Zaloguj się jako użytkownik za pomocą programu Azure PowerShell
- Tworzenie usługi podmiotów zabezpieczeń należy ich używać do logowania do programu Azure PowerShell


## <a name="regions-services-plans-offers-and-subscriptions"></a>Regiony, usługi, planów, ofertami i subskrypcji
W stosie Azure usług są dostarczane dzierżawcom przy użyciu regionów, subskrypcje, ofertami i planów. Dzierżawcy mogą subskrybować wielu ofert. Oferty może mieć co najmniej jeden plan i planów może mieć co najmniej jedna usługa.

![](media/azure-stack-key-features/image4.png)

Przykładowa hierarchia subskrypcji dzierżawcy ze zróżnicowanymi ofert planów i usług.

### <a name="regions"></a>Regiony
Regiony platformy Azure stosu są podstawowy element skalowania i zarządzania. Organizacja może mieć wielu regionach dostępnych zasobów w każdym regionie. Regiony mogą także mieć dostępnych ofert innej usługi. Azure stosu Development Kit, jest obsługiwana tylko pojedynczy region i nosi nazwę automatycznie *lokalnego*.

### <a name="services"></a>Usługi
Microsoft Azure stosu umożliwia dostawcom dostarczania szerokiej gamy usług i aplikacji, takich jak maszyny wirtualne, SQL Server baz danych programu SharePoint, Exchange i więcej.

### <a name="plans"></a>Plany
Plany to grupy co najmniej jedna usługa. Dostawcy możesz tworzyć plany udostępniać dzierżawcom. Z kolei dzierżawcy mogą subskrybować oferty, aby korzystać z planów i objętych nimi usług.

Każda usługa dodane do planu można skonfigurować ustawienia przydziału, aby ułatwić zarządzanie pojemność w chmurze. Zasoby mogą zawierać ograniczenia, takie jak limity maszyny Wirtualnej, pamięci RAM i procesora CPU i są stosowane na subskrypcję użytkownika. Przydziały mogą być rozróżniane według lokalizacji. Na przykład planu zawierający usługi obliczeniowe z regionu A może mieć przydziału dwóch maszyn wirtualnych, 4 GB pamięci RAM oraz 10 rdzeni Procesora.

Podczas tworzenia oferty, administrator usługi mogą obejmować **planu podstawowego**. Te plany podstawowe są domyślnie dołączone, gdy dzierżawca subskrybuje tej oferty. Gdy użytkownik subskrybuje (i utworzeniu subskrypcji), użytkownik ma dostęp do wszystkich dostawców zasobów określone w te plany podstawowe (za pomocą odpowiednich przydziały).

Administrator usługi mogą również obejmować **planów dodatek** oferty. Dodatek planów nie znajdują się domyślnie w subskrypcji. Dodatkowe programy są plany dodatkowe (limity) dostępna w propozycję właściciel subskrypcji można dodać do subskrypcji.

### <a name="offers"></a>Oferta
Oferty to grupy co najmniej jeden plan udostępniające dzierżawcom kupić dostawców (subskrybować). Na przykład oferty alfa może zawierać Plan A zawierający zestaw usług obliczeń i planu B zawierający zestaw usług magazynu i sieci.

Oferta zawiera zestaw podstawowy planów i usługi, Administratorzy mogą tworzyć plany dodatków, które dzierżawcy można dodać do ich subskrypcji.

### <a name="subscriptions"></a>Subskrypcje
Jak dzierżawcy kupowanie oferty jest subskrypcja. Subskrypcja jest kombinacją dzierżawy z ofertą. Dzierżawcy mogą mieć subskrypcje wielu ofert. Każda subskrypcja ma zastosowanie do tylko jednej oferty. Subskrypcje dzierżawy określają planów/usług, które mogą uzyskiwać dostęp do.

Subskrypcje pomocy dostawców organizowanie i dostępu do zasobów w chmurze i usług.

Dla administratora domyślny dostawca subskrypcji jest tworzony podczas wdrażania. Ta subskrypcja może służyć do zarządzania Azure stosu, wdrożyć dodatkowe dostawców zasobów oraz tworzenie planów i oferty dla dzierżawcy. Nie można stosować do uruchamiania obciążeń klientów i aplikacji. Począwszy od wersji 1804 dwóch dodatkowych subskrypcji uzupełnić subskrypcji domyślny dostawca; Subskrypcja zliczania i subskrypcji zużycia. Te dodatki ułatwiają oddzielenie zarządzania w infrastrukturze podstawowej, dostawców dodatkowych zasobów i obciążeń.  

## <a name="azure-resource-manager"></a>Azure Resource Manager
Za pomocą usługi Azure Resource Manager, może współpracować z zasobami infrastruktury w na podstawie szablonu, deklaratywny model.   Zapewnia jeden interfejs, który służy do wdrażania i zarządzania nimi składniki rozwiązania. Aby uzyskać pełne informacje i wskazówki, zobacz [Omówienie usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

### <a name="resource-groups"></a>Grupy zasobów
Grupy zasobów są kolekcjami elementów zasobów, usługi i aplikacje — i wszystkie zasoby, ma typ, takich jak maszyn wirtualnych, sieci wirtualne publicznych adresów IP, konta magazynu i witryn sieci Web. Każdy zasób musi znajdować się w grupie zasobów i, grupy zasobów ułatwiają logicznie organizowanie zasobów, takich jak według lokalizacji lub obciążenia.  Microsoft Azure stosu zasobów, takich jak plany i oferuje także są zarządzane w grupy zasobów.

W odróżnieniu od [Azure](../azure-resource-manager/resource-group-move-resources.md), nie można przenieść zasobów między grupami zasobów. Po wyświetleniu właściwości zasób lub grupa zasobów w portalu administracyjnym stosu Azure *Przenieś* przycisku jest wyszarzona i jest niedostępne. 
 
### <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager
Usługi Azure Resource Manager można utworzyć szablon (w formacie JSON), który definiuje wdrażania i konfiguracji aplikacji. Ten szablon jest nazywany szablonem usługi Azure Resource Manager i pozwala na deklaratywne Definiowanie wdrożeń. Dzięki szablonowi można wielokrotnie wdrażać aplikację w całym jej cyklu życia z gwarancją spójnego stanu zasobów po każdym wdrożeniu.

## <a name="resource-providers-rps"></a>Dostawcy zasobów (RPs)
Dostawcy zasobów są usługi sieci web, które stanowią podstawę dla wszystkich opartych na platformie Azure IaaS i PaaS usługi. Usługa Azure Resource Manager zależy od różnych RPs zapewniające dostęp do usług.

Istnieją cztery RPs podstawowych: sieci, magazynu, obliczeniowej i KeyVault. Każdy z tych RPs pomaga skonfigurować i sterować jego odpowiednich zasobów. Administratorzy usługi można również dodać nowych zasobów niestandardowych dostawców.

### <a name="compute-rp"></a>Obliczenia bazy danych planu odzyskiwania
Dostawca zasobów obliczeniowe (CRP) umożliwia dzierżawcom stosu Azure do tworzenia własnych maszyn wirtualnych. CRP obejmuje możliwość tworzenia maszyn wirtualnych, a także rozszerzenia maszyny wirtualnej. Usługi rozszerzenia maszyny wirtualnej zapewnia możliwości IaaS dla maszyn wirtualnych systemu Windows i Linux.  Na przykład CRP można użyć do udostępnienia maszyny wirtualnej systemu Linux i uruchamiać skrypty powłoki systemowej podczas wdrażania, aby skonfigurować maszynę Wirtualną.

### <a name="network-rp"></a>Sieci planu odzyskiwania
Dostawcy zasobów sieciowych (NRP) zapewnia szereg funkcji programowalnej (SDN) i Wirtualizacja funkcji sieci (NFV) dla chmury prywatnej.  Dostawca NRP służy do tworzenia zasobów, takich jak oprogramowanie obciążenia równoważenia, publiczne adresy IP, sieciowych grup zabezpieczeń, sieci wirtualnych.

### <a name="storage-rp"></a>Magazyn planu odzyskiwania
RP magazynu zapewnia cztery usługi magazynu Azure spójne: obiektów blob, tabeli, kolejki i zarządzania kontem. Zapewnia także ułatwienia usługi zarządzania dostawcy usług magazynu Azure spójne usługi administracji w chmurze magazynu. Usługa Azure Storage zapewnia elastyczność do przechowywania i pobierania dużych ilości danych bez struktury, takich jak dokumenty i pliki multimedialne z obiektami blob Azure, i strukturalne NoSQL na podstawie danych z tabel Azure. Aby uzyskać więcej informacji dotyczących usługi Azure Storage, zobacz [wprowadzenie do usługi Microsoft Azure Storage](../storage/common/storage-introduction.md).

#### <a name="blob-storage"></a>Blob Storage
Magazyn obiektów blob przechowuje każdego zestawu danych. Obiekt blob może być dowolnymi danymi tekstowymi lub binarnymi, takimi jak dokument, plik multimedialny lub instalator aplikacji. Table storage przechowuje zestawy danych ze strukturą. Magazyn tabel jest magazynem typu NoSQL zawierającym pary klucz-atrybut, co umożliwia szybkie opracowywanie i szybki dostęp do dużych ilości danych. Magazyn kolejek umożliwia niezawodną obsługę komunikatów do przetwarzania przepływu pracy i komunikacji między składnikami usług w chmurze.

Każdy obiekt blob jest zorganizowana w kontenerze. Kontenery to także wygodny sposób na przypisywanie zasad zabezpieczeń do grup obiektów. Konto magazynu może zawierać dowolną liczbę kontenerów, a kontener może zawierać dowolną liczbę obiektów blob w granicach 500 TB pojemności konta magazynu. Magazyn obiektów blob udostępnia trzy typy obiektów blob: blokowe obiekty blob, uzupełnialne obiekty blob i stronicowe obiekty blob (dyski). Blokowe obiekty blob są zoptymalizowane pod kątem przesyłania strumieniowego i przechowywania obiektów w chmurze i dobrze nadają się do przechowywania dokumentów, plików multimedialnych, kopii zapasowych itd. Uzupełnialne obiekty blob są podobne do blokowych obiektów blob, lecz są zoptymalizowane pod kątem operacji dołączania. Uzupełnialny obiekt blob można zaktualizować tylko przez dodanie nowego bloku na końcu. Uzupełnialne obiekty blob są dobrym rozwiązaniem w przypadku scenariuszy takich jak rejestrowanie, które wymaga zapisywania tylko na końcu obiektu blob. Stronicowe obiekty BLOB są zoptymalizowane pod kątem reprezentowania dysków IaaS i obsługi losowych zapisuje i może mieć maksymalnie 1 TB. Dysk IaaS dołączony do sieci maszyny wirtualnej platformy Azure to plik VHD przechowywany jako stronicowy obiekt blob.

#### <a name="table-storage"></a>Magazyn tabel
Magazyn tabel jest magazynem kluczy/atrybutów NoSQL firmy Microsoft — ma on projektu bez schematów, czym różni się od tradycyjnych relacyjnych baz danych. Ponieważ dane są przechowywane Brak schematów, jest łatwo zaadaptować dane rozwijających się potrzeb aplikacji. Magazyn tabel jest łatwy w użyciu, dzięki czemu deweloperzy mogą szybko tworzyć aplikacje. Magazyn tabel to magazyn zawierający pary klucz-atrybut, co oznacza, że każda wartość w tabeli jest przechowywana razem z nazwą właściwości z określonym typem. Nazwa właściwości może służyć do filtrowania i określania kryteriów wyboru. Kolekcja właściwości i ich wartości stanowi jednostkę. Począwszy od tabeli magazynu Brak schematów dwie jednostki w tej samej tabeli mogą zawierać różne kolekcje właściwości i te właściwości mogą być różnych typów. Magazyn tabel umożliwia przechowywanie elastycznych zestawów danych, takich jak dane użytkownika dla aplikacji sieci Web, książki adresowe, informacje o urządzeniach i wszelkie inne metadane, których wymaga Twoja usługa. W tabeli można przechowywać dowolną liczbę jednostek, a konto magazynu może zawierać dowolną liczbę tabel w granicach pojemności konta magazynu.

#### <a name="queue-storage"></a>Queue Storage
Usługa Azure Queue Storage umożliwia przesyłanie komunikatów za pomocą chmury między składnikami aplikacji. W przypadku projektowania aplikacji pod kątem skalowania składniki aplikacji są często rozłączane, dzięki czemu mogą być skalowane niezależnie. Usługa Queue Storage zapewnia asynchroniczne przesyłanie komunikatów na potrzeby komunikacji między składnikami aplikacji niezależnie od tego, czy działają w chmurze, na komputerze, serwerze lokalnym czy urządzeniu przenośnym. Usługa Queue Storage obsługuje również zarządzanie asynchronicznymi zadaniami oraz przepływy pracy procesu kompilacji.

### <a name="keyvault"></a>KeyVault
KeyVault RP oferuje zarządzania i inspekcję tajemnice, takie jak hasła i certyfikatów. Na przykład dzierżawcy umożliwia KeyVault RP podać hasło administratora lub kluczy podczas wdrażania maszyny Wirtualnej.

## <a name="high-availability-for-azure-stack"></a>Wysoka dostępność dla stosu Azure
*Dotyczy: Azure 1802 stosu lub nowszych wersji*

Aby osiągnąć wysoką dostępność systemu produkcji wielu maszyn wirtualnych na platformie Azure, maszyny wirtualne są umieszczane w zestawie dostępności, której przedstawiciele rozprzestrzeniają je w wielu domenach awarii i Aktualizacja domeny. W ten sposób [maszyn wirtualnych wdrożonych w zestawach dostępności](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) fizycznie izolowane od siebie na osobnym serwerze stojakami umożliwiające odporności awarii, jak pokazano na poniższym diagramie:

  ![Azure stosu wysokiej dostępności.](media/azure-stack-key-features/high-availability.png)

### <a name="availability-sets-in-azure-stack"></a>Zestawy dostępności w stosie Azure
Podczas infrastruktury stosu Azure jest już odporność na awarie, podstawową technologią (klastra trybu failover) nadal wiąże się pewien przestój dla maszyn wirtualnych na wpływ na serwerze fizycznym w przypadku awarii sprzętu. Stos Azure obsługuje o zbiór składającą się z maksymalnie trzech domen błędów, aby były spójne z usługi Azure dostępności.

- **Odporność domen**. Maszyny wirtualne, umieszczone w zestawie dostępności będzie fizycznie odizolowane od siebie możliwie najbardziej równomiernie się je za pośrednictwem wielu domen błędów (węzły stosu Azure). W przypadku awarii sprzętu, maszyn wirtualnych z domeny nie powiodło się błędów zostanie uruchomiony ponownie w innych domenach awarii jednak, jeśli to możliwe, znajdującej się w domenach awarii oddzielne z innych maszyn wirtualnych w tym samym zestawie dostępności. Gdy sprzęt wraca do trybu online, maszyny wirtualne będą rebalanced do obsługi wysokiej dostępności. 
 
- **Aktualizowanie domeny**. Aktualizacja domeny są innego koncepcji Azure, który zapewnia wysoką dostępność w zestawach dostępności. Domena aktualizacji jest logicznej grupy używany sprzęt, który może zostać poddane konserwacji w tym samym czasie. Maszyny wirtualne znajdujące się w tej samej domenie aktualizacji zostanie uruchomiony ponownie ze sobą podczas zaplanowanej konserwacji. Jak dzierżawcom Tworzenie maszyn wirtualnych w zestawie dostępności, platformy Azure automatycznie dystrybuuje maszyny wirtualne na tych domen aktualizacji. W stosie Azure maszyny wirtualne są aktywne migracji w trybie online hosty w klastrze, przed zaktualizowaniem ich odpowiedniego hosta. Ponieważ podczas aktualizowania hosta nie bez przestojów dzierżawy, funkcji domeny aktualizacji na stosie Azure występuje tylko w przypadku szablonu zgodności z platformą Azure. 

### <a name="upgrade-scenarios"></a>Scenariuszy uaktualniania 
Maszyny wirtualne w zestawach dostępności, utworzonych przed stosu Azure w wersji 1802 podano domyślną liczbę usterek i aktualizację domeny (1 do 1 odpowiednio). Do osiągnięcia wysokiej dostępności dla maszyn wirtualnych w tych wstępnie istniejące zestawy dostępności, należy najpierw usunąć istniejące maszyny wirtualne i wdrożenie ich do nowego zestawu z poprawną liczbą usterek i Aktualizacja domeny, zgodnie z opisem w dostępności [zmiany zbiór dostępności dla maszyny Wirtualnej systemu Windows](https://docs.microsoft.com/azure/virtual-machines/windows/change-availability-set). 

Zestawy skalowania maszyny wirtualnej, aby uzyskać zestaw dostępności jest utworzone wewnętrznie z domyślnej domeny i aktualizacji liczba domen błędów (3 i 5 odpowiednio). Zlicza domyślnej domeny błędów i zaktualizuj wszystkie zestawy skalowania maszyny wirtualnej utworzone przed 1802 aktualizacji zostaną umieszczone w zestawie o dostępności (1 do 1 odpowiednio). Aby zaktualizować te wystąpienia zestawu skali maszyny wirtualnej do osiągnięcia nowszej rozpowszechniania, skalowanie w poziomie zestawy skalowania maszyny wirtualnej według liczby wystąpień, które znajdowały się przed aktualizacją 1802, a następnie usuń starsze wystąpień zestawy skalowania maszyny wirtualnej. 

## <a name="role-based-access-control-rbac"></a>Oparta na rolach kontrola dostępu (RBAC)
Za użycie funkcji RBAC można udzielić dostępu do systemu do autoryzowanych użytkowników, grup i usług przez przypisywanie ich role w subskrypcji, grupy zasobów lub pojedynczych zasobów. Każda rola określa poziom dostępu przez użytkownika, grupy lub usługi Microsoft Azure stosu zasobów.

Azure RBAC ma trzy podstawowe role, które są stosowane do wszystkich typów zasobów: właściciela, współautora ani czytnika. Właściciel ma pełny dostęp do wszystkich zasobów łącznie z prawem delegować dostęp do innych użytkowników. Można utworzyć współautora i zarządzania wszystkimi typami zasobów platformy Azure, ale nie może udzielić dostępu do innych użytkowników. Czytnik można przeglądać tylko istniejących zasobów platformy Azure. Pozostałe role RBAC na platformie Azure umożliwiają zarządzanie określonych zasobów platformy Azure. Na przykład Rola współautora maszyny wirtualnej umożliwia tworzenie i zarządzania maszynami wirtualnymi, ale nie jest możliwe zarządzanie sieci wirtualnej lub podsieci, która łączy się z maszyny wirtualnej.

## <a name="usage-data"></a>Dane użycia
Microsoft Azure stosu zbiera i agreguje dane dotyczące użycia przez wszystkich dostawców zasobów, a następnie przesyła go do platformy Azure do przetworzenia przez handlu Azure. Można wyświetlić danych użycia zbieranych na stosie Azure za pośrednictwem interfejsu API REST. Istnieje Azure spójny interfejs API dzierżawcy, a także dostawcy i delegowane interfejsy API dostawcy do pobierania danych użycia przez wszystkie subskrypcje dzierżawy. Te dane można zintegrować z narzędzia zewnętrznego lub usługą rozliczeń lub obciążenia zwrotnego. Po użycia zostały przetworzone przez commerce platformy Azure, można można przeglądać w portalu Azure rozliczeń.

## <a name="in-development-build-of-azure-stack-development-kit"></a>Kompilacji w rozwoju systemu Azure stosu Development Kit
Kompilacje w rozwoju umożliwiają pilotażowe oceny najnowszej wersji Azure stosu Development Kit. Są one kompilacji przyrostowej oparte na najbardziej aktualną wersją główną. Wersje główne będzie nadal można zwolnić co kilka miesięcy, kompilacji w rozwoju opublikuje sporadycznie między główne wersje.

Kompilacje w rozwoju będzie zapewniają następujące korzyści:
- Poprawki błędów
- Nowe funkcje
- Inne ulepszenia

## <a name="next-steps"></a>Kolejne kroki
[Ocena Azure stosu Development Kit](azure-stack-deploy-overview.md)

