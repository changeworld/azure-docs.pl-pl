---
title: Co to jest usługa Azure Stack? | Microsoft Docs
description: Dowiedz się, jak usługa Azure Stack umożliwia uruchamianie usług platformy Azure w centrum danych.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/29/2019
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: ''
ms.lastreviewed: 03/29/2019
ms.openlocfilehash: e5e05ad4f2ae7e718e160916144f03bfb74a2a52
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58631317"
---
# <a name="azure-stack-overview"></a>Omówienie usługi Azure Stack

Usługi Azure Stack stanowi rozszerzenie platformy Azure, która umożliwia uruchamianie aplikacji w środowisku lokalnym i dostarczanie usług platformy Azure w centrum danych. Przy użyciu spójnych funkcji chmury platformy organizacje mogą świadomie dokonywać decyzjach technologicznych, na podstawie wymagań biznesowych, a nie decyzje biznesowe, w oparciu o technologię ograniczenia.

## <a name="why-use-azure-stack"></a>Dlaczego warto używać usługi Azure Stack?

System Azure oferuje zaawansowaną platformę dla deweloperów do tworzenia nowoczesnych aplikacji. Jednak niektóre aplikacje oparte na chmurze stoją w obliczu przeszkód, takie jak czas oczekiwania, sporadycznie i przepisów. Platforma Azure i usługi Azure Stack odblokować nowe przypadki użycia chmury hybrydowej w wierszu przeznaczonych dla klientów i wewnętrznych aplikacji biznesowych:

- **Krawędź rozwiązań lub niepołączonych**. Adres opóźnienia i wymagań dotyczących łączności, przetwarzając dane lokalnie w usłudze Azure Stack, a następnie agregując na platformie Azure do dalszej analizy za pomocą wspólnej logiki aplikacji w obu przypadkach. Możesz nawet wdrożyć usługę Azure Stack, odłączony od Internetu bez łączności na platformie Azure. Pomyśl o fabryk, przez dostarczany i szyby wydobywania jako przykłady.

- **W chmurze aplikacji, które spełniają zróżnicowane wykonawcze**. Twórz i wdrażaj aplikacje na platformie Azure, pełną elastyczność wdrażanie w środowisku lokalnym za pomocą usługi Azure Stack, aby spełnić prawnych lub wymagań zasad, nie są wymagane żadne zmiany kodu. Przykłady aplikacji obejmują globalne inspekcji, raportowanie finansowe, walutowych handlowymi, gier online i raportowanie wydatków.

- **W chmurze aplikacji modelu w środowisku lokalnym**. Użyj usług platformy Azure, kontenery, bez użycia serwera i architektur mikrousług, Aktualizuj i rozszerzaj istniejące aplikacje lub tworzenie nowych. Procesów DevOps spójne użycia platformy Azure w chmurze usługi Azure Stack lokalnych i w celu przyspieszenia modernizacja aplikacji dla podstawowych aplikacji o znaczeniu krytycznym.

Usługa Azure Stack umożliwia tych scenariuszy użycia, zapewniając:

- Proces dostarczania zintegrowanego, aby rozpocząć i szybko dzięki systemom usługi Azure Stack zintegrowane systemy od sprzętu zaufanych partnerów. Po dostarczeniu usługi Azure Stack łatwo integruje się z centrum danych przy użyciu funkcji monitorowania za pomocą rozszerzenia pakiet administracyjny programu System Center Operations Manager lub Nagios.

- Zarządzanie tożsamościami elastycznego za pomocą usługi Azure Active Directory (Azure AD) dla środowisk hybrydowych platformy Azure i usługi Azure Stack i korzystanie z usługi Active Directory Federation Services (AD FS) w celu na odłączony wdrożeń. 

- Zbliża się do środowiska programowania aplikacji spójnych z platformą Azure, aby zmaksymalizować wydajność pracy deweloperskiej i włączyć typowych operacji deweloperskich w środowiskach hybrydowych.

- Dostarczanie usług platformy Azure z lokalnych przy użyciu chmury hybrydowej, mocy obliczeniowej. Przyjęcie wspólne praktyki operacyjne na platformie Azure i usługi Azure Stack do wdrażania i obsługi usług Azure IaaS i PaaS przy użyciu tego samego środowiska administracyjnego i narzędzi jako platformy Azure. Firma Microsoft dostarcza ciągłych innowacji platformy Azure do usługi Azure Stack, w tym nowych usług platformy Azure, aktualizacje istniejących usług i dodatkowe aplikacje z portalu Azure Marketplace i obrazy.

## <a name="azure-stack-architecture"></a>Architektura usługi Azure Stack
Usługa Azure Stack, które składają się zintegrowanych systemów w stojakach 4 – 16 serwerów utworzonych przez zaufanych partnerów sprzętowych i dostarczane bezpośrednio do centrum danych. Po dostarczeniu dostawca rozwiązań będzie działać z Wdrażanie zintegrowanego systemu i upewnij się, że rozwiązanie usługi Azure Stack spełnia Twoje wymagania biznesowe. Konieczne będzie zapewnienie wszystkich wymaganych zasilania i chłodzenia należy przygotować centrum danych, łączność obramowania i inne wymagania integracji wymagane centrum danych zostały spełnione. 

> Aby dowiedzieć się więcej o środowisku integracji centrum danych usługi Azure Stack, zobacz [Integracja z centrum danych usługi Azure Stack](azure-stack-customer-journey.md).

Usługa Azure Stack jest oparta na branży standardowego sprzętu i jest zarządzane przy użyciu tych samych narzędzi, których już używasz do zarządzania subskrypcjami platformy Azure. Co w efekcie można zastosować spójny metodykę DevOps, czy jest połączony z platformy Azure, czy nie. 

Architektura usługi Azure Stack umożliwia dostarczanie usług platformy Azure, które na urządzeniach brzegowych w lokalizacjach zdalnych lub sporadycznie rozłączony z Internetem. Można tworzyć rozwiązania hybrydowe, które przetwarzają dane lokalnie w usłudze Azure Stack i agregować je na platformie Azure dodatkowego przetwarzania i analizy. Na koniec ponieważ usługi Azure Stack jest zainstalowany w środowisku lokalnym, możesz zaspokoić określonych wymagań prawnych lub zasad z elastycznością wdrażania chmury aplikacji lokalnych bez zmieniania kodu. 

## <a name="deployment-options"></a>Opcje wdrożenia

### <a name="production-or-evaluation-environments"></a>Środowiska produkcyjnego lub oceny
Usługa Azure Stack jest oferowana w dwóch opcji wdrażania do własnych potrzeb, systemy zintegrowane w usłudze Azure Stack do użycia w produkcji i usługi Azure Stack Development Kit (ASDK) do oceny usługi Azure Stack:

- **Zintegrowane systemy usługi Azure Stack**. Usługa Azure Stack, które zintegrowane systemy są oferowane za pośrednictwem powiązania partnerów firmy Microsoft i sprzętu, tworzenia rozwiązania oferuje realizowany w chmurze wprowadzanie innowacji i uproszczenia zarządzania obliczeń. Ponieważ Azure Stack jest oferowany jako zintegrowane sprzętu i oprogramowania systemu, masz elastyczność i kontroli, którą chcesz dodać, wraz z możliwością innowacji z chmury. Systemy usługi Azure Stack, zintegrowane w zakresie rozmiaru, od 4 do 16 węzłów i wspólnie są obsługiwane przez partnerów sprzętowych i firmy Microsoft. Azure Stack zintegrowanych systemów umożliwia tworzenie nowych scenariuszy i wdrażanie nowych rozwiązań dla obciążeń produkcyjnych.

- **Usługa Azure Stack Development Kit**. [Usługi Azure Stack Development Kit (ASDK)](./asdk/asdk-what-is.md) to bezpłatne, jednowęzłowej wdrożenie usługi Azure Stack, które można użyć do oceny i Dowiedz się więcej o usłudze Azure Stack. Można również użyć ASDK w zakresie jako środowiska deweloperskiego do tworzenia aplikacji za pomocą interfejsów API i narzędzi, który jest zgodny z platformą Azure. Jednak ASDK nie jest przeznaczona do użycia jako środowisko produkcyjne i ma następujące ograniczenia w porównaniu do pełnej zintegrowane systemy wdrażania w środowisku produkcyjnym:

    - ASDK może być skojarzony tylko z jednej usługi Azure Active Directory (Azure AD) lub dostawcy tożsamości usługi Active Directory Federation Services (AD FS).
    - Składniki usługi Azure Stack są wdrażane na komputerze hosta, dlatego są ograniczone zasoby fizyczne dostępne dla zasobów dzierżawy. Ta konfiguracja nie jest przeznaczona do skalowania, lub do oceny wydajności.
    - Scenariusze sieci są ograniczone ze względu na jednym hoście i wymagania dotyczące wdrażania kart interfejsu Sieciowego.

### <a name="connection-models"></a>Modele połączeń
Możesz wdrożyć usługę Azure Stack, albo **połączone** z Internetem (i na platformie Azure) lub **odłączony** z niego. Ten wybór określa, które opcje są dostępne dla magazynu tożsamości (Azure AD lub AD FS) i model rozliczeń (zapłacić jako zgodnie z użycia albo rozliczeniem bądź oparty na pojemności rozliczeń).

> Aby uzyskać więcej informacji, zobacz Uwagi dotyczące [połączone](azure-stack-connected-deployment.md) i [odłączony](azure-stack-disconnected-deployment.md) modeli wdrażania. 

### <a name="identity-provider"></a>Dostawca tożsamości 
Usługa Azure Stack korzysta z usługi Azure Active Directory (Azure AD) lub usługi Active Directory Federation Services (AD FS) jako dostawcy tożsamości ustalenie tożsamości usługi Azure Stack. 

> [!IMPORTANT]
> To punkt kluczowe decyzje. Wybranie pozycji Azure AD lub AD FS jako dostawcy tożsamości jest jednorazowe decyzji, które należy wykonać w czasie wdrażania. Nie można zmienić to później bez ponownego wdrażania całego systemu.

Usługa Azure AD jest dostawca tożsamości wielodostępnych, oparte na chmurze firmy Microsoft. Większość scenariuszy hybrydowych wdrożeń podłączonej do Internetu Użyj usługi Azure AD jako magazynu tożsamości. Można jednak użyć Active Directory Federation Services (AD FS) w przypadku odłączonych wdrożeń usługi Azure Stack. Dostawcy zasobów usługi Azure Stack i inne aplikacje działają podobnie jak z usługami AD FS tak samo, jak za pomocą usługi Azure AD. Usługa Azure Stack obejmuje własne wystąpienie usługi Active Directory i Active interfejsu API programu Graph katalogu. 

> Dowiedz się więcej o kwestiach związanych z tożsamością usługi Azure Stack w [Przegląd tożsamości usługi Azure Stack](azure-stack-identity-overview.md).

## <a name="how-is-azure-stack-managed"></a>Jak odbywa się z usługi Azure Stack?
Po wdrożeniu usługi Azure Stack w zintegrowane systemy wdrażania lub instalacji ASDK, podstawowe metody interakcji z usługą Azure Stack są portalu administratora portalu użytkownika i programu PowerShell. Każdy portali usługi Azure Stack są wspierane przez osobnych wystąpień usługi Azure Resource Manager. **Usługi Azure Stack Operator** korzysta z portalu administracyjnego do zarządzania usługi Azure Stack oraz do wykonywania czynności takich, jak utworzyć dzierżawę, ofert i obsługa stanu kondycji i monitora systemu zintegrowanego. Portal użytkowników (nazywane również portalu dzierżawcy) zapewnia Samoobsługowe zużycia zasobów w chmurze, takich jak maszyny wirtualne, konta magazynu i aplikacje sieci web. 

> Aby uzyskać więcej informacji na temat zarządzania usługi Azure Stack przy użyciu portalu administracyjnego znajdują się w [opcji szybkiego startu portalu administrowania w usłudze Azure Stack](azure-stack-manage-portals.md).

Operator usługi Azure Stack, można dostarczać szerokiej gamy usług i aplikacji, takich jak [maszyn wirtualnych](azure-stack-tutorial-tenant-vm.md), [aplikacji sieci web](azure-stack-app-service-overview.md)o wysokiej dostępności [programu SQL Server](azure-stack-tutorial-sql.md), i [serwer MySQL](azure-stack-tutorial-mysql.md) baz danych. Można również użyć [szablony usługi Azure Resource Manager szybkiego startu usługi Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates) do wdrożenia programu SharePoint, Exchange i nie tylko. 

Za pomocą portalu administracyjnego można [Konfigurowanie usługi Azure Stack do świadczenia usług](azure-stack-plan-offer-quota-overview.md) dzierżawcom przy użyciu planów, limity przydziału, oferty i subskrypcje. Użytkownicy dzierżawcy mogą subskrybować wiele ofert. Oferty, może mieć co najmniej jeden plan i planów może mieć co najmniej jednej usługi. Operatory również zarządzanie pojemnością i odpowiadanie na alerty. 

Po skonfigurowaniu usługi Azure Stack **użytkownika usługi Azure Stack** (określane również jako dzierżawca) korzysta z usług oferowanych przez operatora. Użytkowników można aprowizować, monitorowanie i zarządzanie usługami, które masz subskrypcję, takich jak aplikacje sieci web, magazynu i maszyn wirtualnych.

> Aby dowiedzieć się więcej na temat zarządzania usługi Azure Stack, w tym, co konta do użycia w przypadku gdy typowy obowiązki operatora, co mówić użytkownikom i jak uzyskać pomoc, zapoznaj się z [podstawy administracji usługi Azure Stack](azure-stack-manage-basics.md).

## <a name="resource-providers"></a>Dostawcy zasobów 
Dostawcy zasobów to usługi sieci web, które stanowią podstawę dla wszystkich usługi Azure Stack IaaS i PaaS usług. Usługa Azure Resource Manager, zależy od dostawców innego zasobu w celu zapewnienia dostępu do usług. Każdy dostawca zasobów ułatwi Ci skonfigurowanie i kontrolować jej odpowiednich zasobów. Administratorzy usługi można również dodać nowych dostawców zasobów niestandardowych. 

### <a name="foundational-resource-providers"></a>Dostawcy zasobów podstawowe 
Istnieją trzy podstawowe dostawców zasobów IaaS: Obliczeniowych, sieci i magazynu:

- **Obliczenia**. Dostawcy zasobów obliczeniowych umożliwia usłudze Azure Stack dzierżawcom tworzenie własnych maszyn wirtualnych. Dostawcy zasobów obliczeniowych obejmuje możliwość tworzenia maszyn wirtualnych, a także rozszerzenia maszyny wirtualnej. Usługa rozszerzenia maszyny wirtualnej zapewnia możliwości IaaS dla maszyn wirtualnych Windows i Linux.  Na przykład można użyć dostawcy zasobów obliczeniowych do aprowizowania maszyny wirtualnej z systemem Linux i uruchamiać skrypty powłoki systemowej podczas wdrażania, aby skonfigurować maszynę Wirtualną.
- **Dostawca zasobów sieciowych**. Dostawca zasobów sieciowych zapewnia szereg funkcji oprogramowania programowalnej sieci komputerowej (SDN) i sieci funkcja wirtualizacji (NFV) w ramach chmury prywatnej. Dostawca zasobów sieciowych umożliwia tworzenie zasobów, takich jak moduły równoważenia obciążenia oprogramowania, publiczne adresy IP, sieciowe grupy zabezpieczeń i sieci wirtualnych.
- **Dostawcy zasobów usługi Storage**. Dostawca zasobów magazynu zapewnia cztery spójnych z platformą Azure usługi storage: [obiektu blob](https://docs.microsoft.com/azure/storage/common/storage-introduction#blob-storage), [kolejki](https://docs.microsoft.com/azure/storage/common/storage-introduction#queue-storage), [tabeli](https://docs.microsoft.com/azure/storage/common/storage-introduction#table-storage)i zarządzania kontami magazynu kluczy, zarządzaniu i Inspekcja klucze tajne, takie jak hasła i certyfikatów. Dostawcy zasobów usługi storage oferuje również magazynu usługi administracyjnej w chmurze w celu ułatwienia usługi zarządzania dostawcy usług spójnych z platformą Azure Storage. Usługa Azure Storage zapewnia elastyczność do przechowywania i pobierania dużych ilości danych niestrukturalnych, takich jak dokumenty i pliki multimedialne za pomocą obiektów blob platformy Azure, a NoSQL ze strukturą na podstawie danych z tabel platformy Azure. 

### <a name="optional-resource-providers"></a>Dostawcy zasobów opcjonalne
Istnieją trzy opcjonalne PaaS dostawców zasobów, które można wdrożyć i za pomocą usługi Azure Stack: Dostawcy zasobów usługi App Service, SQL Server i serwer MySQL:

- **App Service**. [Usługa Azure App Service w usłudze Azure Stack](azure-stack-app-service-overview.md) jest dostępna dla usługi Azure Stack to oferta platform-as-a-service (PaaS) systemu Microsoft Azure. Usługa umożliwia klientom wewnętrzne lub zewnętrzne do tworzenia sieci web, interfejsów API i usługi Azure Functions aplikacje dla dowolnej platformy lub urządzenia. 
- **SQL Server**. Użyj [dostawcy zasobów programu SQL Server](azure-stack-sql-resource-provider.md) oferowanie baz danych SQL jako usługa Azure Stack. Po zainstalowaniu dostawcy zasobów i połącz go z co najmniej jednego wystąpienia programu SQL Server, Ty i Twoi użytkownicy można utworzyć bazy danych dla natywnych aplikacji w chmurze, witryn sieci Web korzystających z programu SQL i innych obciążeń, które używają programu SQL.
- **Serwer MySQL**. Użyj [dostawcy zasobów MySQL Server](azure-stack-mysql-resource-provider-deploy.md) do udostępnienia baz danych MySQL jako usługi Azure Stack. Dostawcy zasobów MySQL działa jako usługa na maszynie wirtualnej (VM) systemu Windows Server 2016 Server Core.

## <a name="providing-high-availability"></a>Zapewnienie wysokiej dostępności
Aby uzyskać wysoką dostępność w systemie produkcyjnym wielu maszyn wirtualnych na platformie Azure, maszyny wirtualne są umieszczane w [zestaw dostępności](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) które rozprzestrzeniają się je w wielu domenach błędów i domenach aktualizacji. W mniejszą skalę usługi Azure Stack domeny błędów w zestawie dostępności jest zdefiniowana jako jeden węzeł w jednostce skalowania.  

Infrastruktury Azure Stack już jest odporna na awarie, podstawową używaną technologią (klaster trybu failover) nadal jest naliczana pewien przestój w przypadku maszyn wirtualnych na serwerze fizycznym nimi objęte w przypadku awarii sprzętu. Usługa Azure Stack obsługuje o dostępności zestawu z maksymalnie trzy domeny błędów, aby były zgodne z platformą Azure.

- **Domeny błędów**. Maszyny wirtualne umieszczone w zestawie dostępności będą fizycznie odizolowane od siebie nawzajem Dzięki rozdzieleniu je możliwie najbardziej równomiernie przez wiele domen błędów (węzłów usługi Azure Stack). W przypadku awarii sprzętu maszyn wirtualnych na podstawie domena błędów nie powiodło się będzie można ponownie uruchomiona w innych domenach błędów, ale jeśli to możliwe, przechowywane w oddzielnych domenach błędów z innymi maszynami wirtualnymi w tym samym zestawie dostępności. Gdy sprzęt powróci do trybu online, maszyn wirtualnych zostanie ponownie zbilansowana do utrzymania wysokiej dostępności. 
 
- **Domeny aktualizacji**. Domeny aktualizacji są innego koncepcji platformy Azure, która zapewnia wysoką dostępność w zestawach dostępności. Domena aktualizacji to logiczna grupa bazowego sprzętu, który może poddawane konserwacji, w tym samym czasie. Maszyn wirtualnych znajdujących się w tej samej domenie aktualizacji będą się ponownie uruchamiane razem podczas zaplanowanej konserwacji. Jak dzierżawcom Tworzenie maszyn wirtualnych w zestawie dostępności, platforma Azure automatycznie rozdziela maszyny wirtualne między domeny aktualizacji. W usłudze Azure Stack maszyny wirtualne działają migracji w trybie online hosty w klastrze, przed ich odpowiedniego hosta jest aktualizowana. Ponieważ podczas aktualizacji hosta bez przestojów dzierżawy, funkcji domeny aktualizacji w usłudze Azure Stack występuje tylko w przypadku szablonu zgodności z platformą Azure. 

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach
Kontrola dostępu na podstawie ról (RBAC) służy do udzielania dostępu do systemu do grona upoważnionych użytkowników, grup i usług, przypisując im role w subskrypcji, grupy zasobów lub poszczególnych zasobów. Każda rola definiuje poziom dostępu użytkownika, grupy lub usługi ma nad zasobami Microsoft Azure Stack.

Usługa Azure Stack RBAC ma trzy podstawowe role, które są stosowane do wszystkich typów zasobów: Właściciela, współautora i czytelnika. Właściciel ma pełny dostęp do wszystkich zasobów łącznie z prawej strony można delegować dostępu do innych osób. Współautor mogą tworzyć i zarządzać nimi wszystkie typy zasobów platformy Azure, ale nie może przyznawać dostępu innym osobom. Czytelnik mogą wyświetlać tylko istniejących zasobów. Pozostałe role RBAC umożliwia zarządzanie określonych zasobów platformy Azure. Na przykład rola Współautor maszyny wirtualnej umożliwia tworzenie i zarządzanie maszynami wirtualnymi, ale nie jest możliwe zarządzanie sieci wirtualnej lub podsieci, która łączy się z maszyny wirtualnej.

> Zobacz [kontroli dostępu Manage Role-Based](azure-stack-manage-permissions.md) Aby uzyskać więcej informacji. 

## <a name="reporting-usage-data"></a>Raportowanie danych użycia
Microsoft Azure Stack umożliwia zbieranie informacji o agreguje dane dotyczące użycia we wszystkich dostawców zasobów i przekazuje go do platformy Azure do przetwarzania przez handlu platformy Azure. Można wyświetlić danych użycia zbieranych w usłudze Azure Stack za pośrednictwem interfejsu API REST. Istnieje interfejs API dzierżawcy spójnych z platformą Azure, a także dostawcy i delegować interfejsy API dostawcy można pobrać danych użycia dla wszystkich subskrypcji dzierżawcy. Te dane można zintegrować z zewnętrznego narzędzia lub usługi do rozliczeń i obciążeń zwrotnych. Po przetworzeniu użycia platformy Azure handlu mogą można wyświetlić w portalu rozliczeń systemu Azure.

> Dowiedz się więcej o [raportowanie danych użycia usługi Azure Stack na platformie Azure](azure-stack-usage-reporting.md).

## <a name="next-steps"></a>Kolejne kroki

[Porównanie usługi Azure Stack i globalnej platformy Azure](compare-azure-azure-stack.md)

[Podstawy administracji](azure-stack-manage-basics.md)

[Szybki Start: Użyj portalu administracyjnego usługi Azure Stack](azure-stack-manage-portals.md)