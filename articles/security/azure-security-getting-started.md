---
title: Wprowadzenie do zabezpieczeń Microsoft Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie funkcji zabezpieczeń Microsoft Azure oraz Ogólne zagadnienia dotyczące organizacji, które migracji ich zasoby do dostawcy usług w chmurze.
services: security
documentationcenter: na
author: barclayn
manager: mbaldwin
editor: TomSh
ms.assetid: 8d8a0088-c85a-48e7-bd04-2bc7b78b0691
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: barclayn
ms.openlocfilehash: a908c242b5d41d5cd61d8775bdbe53f3cdddd3ec
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="getting-started-with-microsoft-azure-security"></a>Wprowadzenie do zabezpieczeń platformy Microsoft Azure

Podczas tworzenia lub migracji zasobów informatycznych do dostawcy usług w chmurze są zależne możliwości Twojej organizacji do ochrony aplikacji i danych z usług i formanty zapewniają do zarządzania zabezpieczeniami elementów zawartości opartej na chmurze.

Infrastruktura platformy Azure została zaprojektowana kompleksowo, począwszy od obiektu po aplikacje hostujące jednocześnie miliony klientów, i zapewnia wiarygodną podstawę zaspokajania potrzeb firm w zakresie bezpieczeństwa. Ponadto platforma Azure oferuje szeroki zakres konfigurowalnych opcji zabezpieczeń oraz możliwość sterowania nimi, co pozwala dostosować zabezpieczenia w taki sposób, aby spełniały unikatowe wymagania realizowanych wdrożeń.

Ten poglądowy artykuł dotyczący zabezpieczeń platformy Azure koncentruje się na następujących zagadnieniach:

* Funkcje i usługi Azure służy do zabezpieczania usług i danych w systemie Azure.
* Jak Microsoft zabezpiecza infrastruktury platformy Azure, aby lepiej chronić Twoje dane i aplikacje.

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

Kontrolowanie dostępu do infrastruktury IT, danych i aplikacji ma ogromne znaczenie. Microsoft Azure zapewnia te możliwości przez usługi Azure Active Directory (Azure AD), usługi Azure Storage i pomocy technicznej dla wielu standardów i interfejsów API.

[Usługi Azure AD](../active-directory/active-directory-whatis.md) jest aparatem, który udostępnia uwierzytelniania, autoryzacji i kontroli dostępu dla użytkowników w organizacji, grup i obiekty i repozytorium tożsamości. Ponadto usługa Azure AD oferuje deweloperom efektywną metodę integracji funkcji zarządzania tożsamościami z tworzonymi przez nich aplikacjami. Standardowych protokołów, takich jak [SAML 2.0](https://en.wikipedia.org/wiki/SAML_2.0), [WS-Federation](https://msdn.microsoft.com/library/bb498017.aspx), i [OpenID Connect](http://openid.net/connect/) odpowiednie możliwości logowania na platformach, takich jak .NET, Java, Node.js i PHP.

Interfejs API Graph oparty na protokole REST pozwala deweloperom na odczyt i zapis w katalogu z dowolnej platformy. Dzięki obsłudze [OAuth 2.0](http://oauth.net/2/), deweloperzy mogą tworzyć przenośnych i aplikacji sieci web, które integrują się z firmy Microsoft i innych firm interfejsów API sieci web oraz tworzenie własnych bezpiecznego interfejsów API w sieci web. Biblioteki klienckie typu „open source” są dostępne dla platformy .Net, Sklepu Windows oraz systemów iOS i Android. Ponadto trwają prace nad dodatkowymi bibliotekami.

### <a name="how-azure-enables-identity-and-access-management"></a>W jaki sposób platforma Azure umożliwia zarządzanie tożsamościami i dostępem

Usługa Azure AD może służyć jako autonomiczny katalog chmury dla organizacji lub jako rozwiązanie zintegrowane z istniejącą lokalną usługą Active Directory. Funkcje integracji obejmują synchronizację katalogów i logowanie jednokrotne (SSO). Te rozszerzenia zasięgu istniejącej tożsamości lokalnych do chmury i udoskonalanie administratora i użytkownika.

Dostępne są również następujące funkcje zarządzania tożsamościami i dostępem:

* Usługa Azure AD umożliwia [logowanie jednokrotne](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) do aplikacji SaaS, niezależnie od lokalizacji hostowania. Niektóre aplikacje są sfederowane z usługą Azure AD, a inne korzystają z logowania jednokrotnego z użyciem hasła. Aplikacje federacyjne mogą także obsługiwać inicjowanie obsługi administracyjnej użytkowników i przechowywanie haseł.
* Dostęp do danych w usłudze [Azure Storage](https://azure.microsoft.com/services/storage/) jest kontrolowany za pośrednictwem uwierzytelniania. Każde konto magazynu ma klucza podstawowego ([klucz konta magazynu](https://msdn.microsoft.com/library/azure/ee460785.aspx), lub SAK) i klucz tajny pomocniczy (sygnatury dostępu współdzielonego, lub SAS).
* Usługa Azure AD zapewnia jako usługi przy użyciu federacji tożsamości za pomocą [Active Directory Federation Services](../active-directory/fundamentals-identity.md), synchronizacji i replikacji z lokalnymi katalogami.
* [Usługa Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) jest usługą Multi-Factor authentication, która wymaga od użytkowników sprawdzić logowania przy użyciu aplikacji mobilnej, połączenia telefonicznego lub wiadomości SMS. Mogą być używane z usługą Azure AD aby ułatwić bezpieczną lokalną zasobów z serwera usługi Azure Multi-Factor Authentication, a także z niestandardowych aplikacji i katalogów przy użyciu zestawu SDK.
* [Usługi domenowe Azure AD](https://azure.microsoft.com/services/active-directory-ds/) umożliwia dołączenie maszyn wirtualnych platformy Azure do domeny bez wdrażania kontrolerów domeny. Możesz Zaloguj się do tych maszyn wirtualnych za pomocą firmowych poświadczeń usługi Active Directory i administrować przyłączonych do domeny maszyn wirtualnych za pomocą zasad grupy, aby wymusić plany bazowe zabezpieczeń na wszystkich maszyn wirtualnych platformy Azure.
* [Usługa Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) udostępnia usługę zarządzania tożsamości globalnego wysokiej dostępności dla aplikacji dla użytkowników, która może obsłużyć setki milionów tożsamości. Można ją łatwo integrować z platformami mobilnymi i platformami sieci Web. Użytkownicy mogą rejestrować do wszystkich aplikacji za pomocą środowiska można dostosować przy użyciu istniejących kont społecznościowych lub tworząc nowe poświadczenia.

## <a name="data-access-control-and-encryption"></a>Kontrola dostępu do danych i szyfrowanie

Microsoft stosuje zasady rozdzielania obowiązków i [najniższych uprawnień](https://en.wikipedia.org/wiki/Principle_of_least_privilege) we wszystkich operacjach platformy Azure. Dostęp do danych przez personel pomocy technicznej platformy Azure wymaga wyraźnej zgody użytkownika i jest przyznawany na zasadzie „dokładnie na czas”. Oznacza to, że uzyskiwany dostęp jest rejestrowany i podlega inspekcji, a po zakończeniu działań uprawnienie zostaje cofnięte.

Platforma Azure udostępnia również wiele możliwości ochrony przesyłanych i przechowywanych danych. Dotyczy to również szyfrowania danych, plików, aplikacji, usług, komunikację i dysków. Szyfrowanie informacji przed wprowadzeniem go na platformie Azure i przechowywać kluczy w centrach danych z lokalnego.

![Ochrona przed złośliwym kodem zapewniana przez Microsoft na platformie Azure](./media/azure-security-getting-started/sec-azgsfig1.PNG)

### <a name="azure-encryption-technologies"></a>Technologie szyfrowania na platformie Azure

Szczegółowe informacje dotyczące dostępu administracyjnego do środowiska subskrypcji można gromadzić przy użyciu usługi [Azure AD Reporting](../active-directory/active-directory-reporting-audit-events.md). Można skonfigurować [szyfrowania dysków funkcją BitLocker](https://technet.microsoft.com/library/cc732774.aspx) na wirtualne dyski twarde zawierające informacje poufne na platformie Azure.

Inne możliwości platformy Azure, które pomagają zapewnić bezpieczeństwo danych, obejmują:

* Deweloperzy aplikacji mogą tworzyć szyfrowania w aplikacjach ich wdrażanie na platformie Azure przy użyciu systemu Windows [CryptoAPI](https://msdn.microsoft.com/library/ms867086.aspx) i .NET Framework.
* Całkowicie kontrolować klucze szyfrowania po stronie klienta dla magazynu obiektów Blob platformy Azure. Usługa magazynu nie rozpoznaje kluczy i nie umożliwia odszyfrowania danych.
* [Usługa Azure Rights Management (Azure RMS)](https://technet.microsoft.com/library/jj585026.aspx) (z [zestawu RMS SDK](https://msdn.microsoft.com/library/dn758244.aspx)) zawiera plik i szyfrowania i wycieku danych zapobiegania poziom danych za pośrednictwem zarządzania na podstawie zasad dostępu.
* Azure obsługuje [szyfrowania na poziomie kolumny i na poziomie tabeli (funkcji TDE/CLE)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database.aspx) w maszyn wirtualnych programu SQL Server który obsługuje innych firm lokalnych serwerów zarządzania kluczami w centrach danych.
* Klucze konta magazynu, sygnatury dostępu współdzielonego, certyfikaty zarządzania i inne klucze są unikatowe dla każdego dzierżawcy na platformie Azure.
* Azure [StorSimple](http://www.microsoft.com/server-cloud/products/storsimple/overview.aspx) hybrydowym magazynie szyfruje dane za pomocą 128-bitowego pary kluczy publiczny/prywatny przed przekazaniem go do magazynu Azure.
* Azure obsługuje i używa wielu mechanizmów szyfrowania, w tym SSL/TLS, IPsec i AES, w zależności od typów danych, kontenery i transportów.

## <a name="virtualization"></a>Wirtualizacja

Platforma Azure korzysta ze środowiska zwirtualizowanego. Wystąpienia użytkownika działać jako autonomiczne maszyny wirtualne, które nie mają dostępu do serwera fizycznego hosta i Izolacja jest wymuszana za pomocą fizycznych [poziomy uprawnień procesora (pierścień-0/pierścień-3)](https://en.wikipedia.org/wiki/Protection_ring).

Pierścień 0 to najwyższy poziom uprawnień, a pierścień 3 — najniższy. System operacyjny gościa jest uruchamiany w 1 pierścień mniejszym uprzywilejowanych i aplikacje są uruchamiane w najniższych uprawnieniach 3 pierścień. Ta wirtualizacja zasobów fizycznych prowadzi do wyraźnego rozdzielenia systemu operacyjnego gościa i funkcji hypervisor, co zapewnia dodatkową separację zabezpieczeń.

Azure funkcji hypervisor działa jak jądra i przekazuje wszystkie żądania dostępu do sprzętu z maszyn wirtualnych gościa do hosta na potrzeby przetwarzania przy użyciu interfejsu pamięci współużytkowanej, nazywany magistralę maszyny wirtualnej. Uniemożliwia to użytkownikom uzyskiwanie dostępu do nieprzetworzonego odczytu/zapisu/wykonania w systemie i zmniejsza ryzyko związane z udostępnianiem zasobów systemowych.

![Ochrona przed złośliwym kodem zapewniana przez Microsoft na platformie Azure](./media/azure-security-getting-started/sec-azgsfig2.PNG)

### <a name="how-azure-implements-virtualization"></a>Implementowanie wirtualizacji na platformie Azure

Platforma Azure korzysta zaporą funkcji hypervisor (filtr pakietów), która jest zaimplementowana w funkcji hypervisor i skonfigurowany przez agenta kontrolera sieci szkieletowej. Pozwala to chronić dzierżawców przed nieautoryzowanym dostępem. Domyślnie cały ruch jest zablokowany, po utworzeniu maszyny wirtualnej, a następnie agent kontrolera sieci szkieletowej konfiguruje filtr pakietów, aby dodać *regułami i wyjątkami* zezwalająca na ruch autoryzowanych.

Istnieją dwie kategorie reguł, które są w tym miejscu programowane:

* **Reguły konfiguracji lub infrastruktury maszyny**: Domyślnie cała komunikacja jest zablokowany. Istnieją wyjątki, aby umożliwić maszynę wirtualną do wysyłania i odbierania ruchu DHCP i DNS. Maszyny wirtualne można również wysyłać ruch do Internetu "public" i przesyłają dane do innych maszyn wirtualnych w ramach klastra i Serwer aktywacji systemu operacyjnego. Maszyn wirtualnych dozwolonych lokalizacji docelowych wychodzących nie zawierają Azure routera podsieci, zarządzania platformy Azure, Utwórz kopię zakończenia i inne właściwości firmy Microsoft.
* **Plik konfiguracji roli**: definiuje przychodzących list kontroli dostępu (ACL) oparte na modelu usługi dzierżawcy. Na przykład jeśli w przypadku konfigurowania punktu końcowego w modelu [zarządzania usługą systemu Azure](../azure-resource-manager/resource-manager-deployment-model.md) dzierżawca ma fronton sieci Web w porcie 80 określonej maszyny wirtualnej, system Azure otwiera port 80 protokołu TCP dla wszystkich adresów IP. Jeśli maszyna wirtualna ma wstecz roli zakończenia lub procesu roboczego uruchomiona, zostanie otwarty roli procesu roboczego tylko do maszyny wirtualnej w ramach tej samej dzierżawy.

## <a name="isolation"></a>Izolacja

Wymaganie dotyczące zabezpieczeń innej chmurze ważne jest separacji, aby uniemożliwić przesyłanie nieautoryzowanych i niezamierzone informacji między wdrożeniami w udostępnionym przypadku architektury wielodostępnej.

Implementuje Azure [kontroli dostępu do sieci](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/) i podział poprzez izolowanie od sieci VLAN, listy ACL, obciążenia równoważenia i filtry IP. Ogranicza ona zakres zewnętrznego ruchu przychodzącego porty i protokoły na maszynach wirtualnych w zdefiniowanych przez użytkownika. Azure implementuje filtrowanie, aby zapobiec fałszywych ruchu i ograniczania ruchu przychodzącego i wychodzącego do TPM składników sieciowych. Zasady przepływu ruchu są implementowane na urządzeniach ochrony brzegowej, które domyślnie nie zezwalają na ruch.

![Ochrona przed złośliwym kodem zapewniana przez Microsoft na platformie Azure](./media/azure-security-getting-started/sec-azgsfig3.PNG)

Do oddzielania ruchu w sieci wewnętrznej od ruchu zewnętrznego służy Translator adresów sieciowych (NAT, Network Address Translation). Ruch wewnętrzny nie podlega routingowi zewnętrznemu. [Wirtualne adresy IP](http://blogs.msdn.com/b/cloud_solution_architect/archive/2014/11/08/vips-dips-and-pips-in-microsoft-azure.aspx), które podlegają routingowi zewnętrznemu, są przekształcane na [wewnętrzne dynamiczne adresy IP](http://blogs.msdn.com/b/cloud_solution_architect/archive/2014/11/08/vips-dips-and-pips-in-microsoft-azure.aspx), które podlegają routingowi tylko w obrębie platformy Azure.

Zewnętrznego ruchu do maszyn wirtualnych platformy Azure jest zaporą za pomocą listy kontroli dostępu na routerach, równoważenia obciążenia i przełączniki warstwy 3. Dozwolone są tylko określone znane protokoły. Listy ACL są spełnione, aby ograniczyć ruch pochodzący z maszyn wirtualnych gościa do innych sieci VLAN, używany do zarządzania. Ponadto ruch filtrowane za pomocą filtrów IP na hoście, który dalsze systemu operacyjnego ogranicza ruch w obu warstwach łącza i sieci danych.

### <a name="how-azure-implements-isolation"></a>Implementowanie izolacji na platformie Azure

Kontroler sieci szkieletowej Azure jest odpowiedzialny za przydzielanie zasobów infrastruktury do dzierżawy obciążeń i zarządza jednokierunkowe komunikacji między hostem maszyny wirtualnej. Azure funkcji hypervisor wymusza pamięci i procesu separacji między maszynami wirtualnymi i bezpiecznie kieruje ruchem sieciowym dzierżawcom systemu operacyjnego gościa. Azure implementuje również izolacji dzierżawców, magazynu i sieci wirtualnych.

* Każda dzierżawa usługi Azure AD jest logicznie samodzielnie przy użyciu granic zabezpieczeń.
* Kontami magazynu Azure są unikatowe dla każdej subskrypcji, i musi zostać uwierzytelniony dostęp przy użyciu klucza konta magazynu.
* Sieci wirtualne są logicznie odizolowane przy użyciu kombinacji unikatowy prywatnych adresów IP, zapory i listy ACL adresu IP. Moduły równoważenia obciążenia kierują ruch do odpowiednich dzierżawców na podstawie definicji punktów końcowych.

## <a name="virtual-networks-and-firewalls"></a>Sieci wirtualne i zapór

[Rozproszone i wirtualnych sieciach](http://download.microsoft.com/download/4/3/9/43902EC9-410E-4875-8800-0788BE146A3D/Windows%20Azure%20Network%20Security%20Whitepaper%20-%20FINAL.docx) w Pomocy usługi Azure upewnij się, że ruchu sieci prywatnej jest logicznie odizolowane od ruchu na innych sieci wirtualnych platformy Azure.

![Ochrona przed złośliwym kodem zapewniana przez Microsoft na platformie Azure](./media/azure-security-getting-started/sec-azgsfig4.PNG)

Subskrypcji może zawierać wiele izolowane sieci prywatnych (i obejmują zapory, równoważenia obciążenia i translatora adresów sieciowych).

Platforma Azure udostępnia trzy główne poziomy podziału sieci w każdym klastrze Azure służące do logicznego rozdzielania ruchu. [Wirtualnych sieci lokalnych](https://azure.microsoft.com/services/virtual-network/) (VLAN) służą do rozdzielania ruchu klientów od pozostałej części sieci Azure. Dostęp do sieci platformy Azure spoza klastra jest ograniczony za pośrednictwem modułów równoważenia obciążenia.

Ruch sieciowy do i z maszyn wirtualnych musi przejść przez przełącznik wirtualny funkcji hypervisor. Składnik filtru IP w katalogu głównym systemu operacyjnego izoluje głównej maszyny wirtualnej z maszyn wirtualnych gościa i maszyn wirtualnych gościa od siebie nawzajem. Wykonuje filtrowania ruchu w celu ograniczenia komunikacji między węzłami dzierżawcy i publiczny Internet (w oparciu konfigurację usługi klienta), ich oddzielenie od pozostałych dzierżawców.

Filtr IP zapobiega maszyn wirtualnych-gości z:

* Generowanie fałszywych ruchu.
* Odbieranie ruchu kierowanego nie do nich.
* Kierowanie ruchem do punktów końcowych chronionych infrastruktury.
* Wysyłanie i odbieranie nieodpowiednie emisji ruchu.

Można umieścić maszyny wirtualne na [sieci wirtualnych platformy Azure](https://azure.microsoft.com/documentation/services/virtual-network/). Te sieci wirtualne są podobne do sieci konfigurowanych w środowiskach lokalnych, gdzie są zwykle skojarzone z przełącznikiem wirtualnym. Maszyny wirtualne podłączone do tej samej sieci wirtualnej mogą komunikować się ze sobą bez dodatkowej konfiguracji. Można również skonfigurować różne podsieci w sieci wirtualnej.

Następujące technologie sieci wirtualnej platformy Azure umożliwia ułatwić bezpieczną komunikację w sieci wirtualnej:

* [**Sieć grupy zabezpieczeń (grup zabezpieczeń sieci)**](../virtual-network/security-overview.md). Można użyć grupy NSG sterujących ruchem do co najmniej jedno wystąpienie maszyny wirtualnej w Twojej sieci wirtualnej. Sieciowa grupa zabezpieczeń zawiera reguły kontroli dostępu, które zezwalają na ruch lub go blokują w oparciu o kierunek ruchu, protokół, adres źródłowy i docelowy oraz port.
* [**Zdefiniowane przez użytkownika routing**](../virtual-network/virtual-networks-udr-overview.md). Możesz sterować przekazywaniem pakietów przez urządzenie wirtualne przez tworzenie trasy zdefiniowane przez użytkownika, określające następnego skoku dla pakietów przepływać do określonej podsieci, aby przejść do urządzenia zabezpieczeń sieci wirtualnej.
* [**Przekazywanie IP**](../virtual-network/virtual-networks-udr-overview.md). Wirtualne urządzenie zabezpieczeń sieciowych musi umożliwiać odbieranie ruchu przychodzącego, który nie jest do niego adresowany. Aby zezwolić na maszynie wirtualnej odbieranie ruchu kierowanego do innych miejsc docelowych, należy włączyć przesyłanie dalej IP dla maszyny wirtualnej.
* [**Wymuszane tunelowanie**](../vpn-gateway/vpn-gateway-about-forced-tunneling.md). Umożliwia tunelowania wymuszonego przekierowania lub "force" wszystkie ruch do Internetu generowany przez maszyny wirtualne w sieci wirtualnej z powrotem do lokalizacji lokalnej za pośrednictwem tunelu VPN lokacja lokacja dla inspekcji i inspekcji
* [**Punkt końcowy ACL**](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Można kontrolować, komputery, które są dozwolone połączenia przychodzące z Internetu do maszyny wirtualnej w sieci wirtualnej, definiując listy ACL punktu końcowego.
* [**Rozwiązania zabezpieczeń sieciowych oferowane przez partnerów**](https://azure.microsoft.com/marketplace/). Istnieje szereg partnerskich rozwiązań zabezpieczeń sieci, które są dostępne w witrynie Azure Marketplace.

### <a name="how-azure-implements-virtual-networks-and-firewalls"></a>Jak Azure implementuje sieci wirtualnych i zapór

Azure implementuje filtrowanie pakietów zapór na wszystkich maszynach wirtualnych hosta i gościa domyślnie. Obrazy systemu operacyjnego Windows z portalu Azure Marketplace ma także włączona domyślnie Zapora systemu Windows. Moduły równoważenia obciążenia w obwodzie Azure sieci publicznych kontroli komunikacji na podstawie list ACL adresu IP, zarządzane przez administratorów klienta.

Jeśli dane klienta na platformie Azure są przenoszone w ramach zwykłych operacji lub podczas awarii, odbywa się to za pośrednictwem prywatnych, szyfrowanych kanałów komunikacyjnych. Innych funkcji zastosowane przez platformę Azure do użycia w sieciach wirtualnych i zapory są:

* **Zapora hosta natywnego**: sieć szkieletowa usług Azure i usługi Azure Storage, uruchom na natywnej systemu operacyjnego z nie funkcji hypervisor. Dlatego zapory systemu windows jest skonfigurowany z poprzednich dwóch zestawów reguł. Magazyn działa w sposób natywny w celu zoptymalizowania wydajności.
* **Zapory hostów**: zaporę hosta jest ochrona hosta systemu operacyjnego, który uruchamia funkcji hypervisor. Reguły są zaprogramowane w taki sposób, aby umożliwić z kontrolerem sieci szkieletowej usług i przejść pola, aby komunikował się z systemu operacyjnego na określonym porcie hosta. Inne wyjątki zezwalają na odpowiedzi serwerów DHCP i DNS. Azure korzysta z pliku konfiguracji komputera, który ma szablon reguły zapory dla systemu operacyjnego hosta. Sam host jest chroniony na ataki zewnętrzne przez zaporę systemu Windows skonfigurowany tak, aby zezwolić na komunikację tylko ze znanych, uwierzytelnionym źródeł.
* **Gość zapory**: replikuje reguły w filtr pakietów przełącznika maszyny wirtualnej, ale zaprogramowanych w różnych oprogramowania (na przykład Zapora systemu Windows część system operacyjny gościa). Zapory maszyny wirtualnej gościa można skonfigurować do ograniczenia komunikacji na lub z maszyny wirtualnej gościa, nawet jeśli komunikacja jest dozwolone przez konfiguracje na hoście filtr IP. Na przykład można użyć zapory maszyny wirtualnej gościa w celu ograniczenia komunikacji między dwiema Twojej sieci wirtualnych skonfigurowanej do połączenia ze sobą.
* **Zapory magazynu (PD)**: Zapora na frontonie magazynu filtruje ruch tylko porty 80/443 i inne narzędzia niezbędne porty. Zapora na magazynu zaplecza ogranicza komunikację pochodzić tylko z serwerów frontonu magazynu.
* **Brama sieci wirtualnej**: [Brama sieci wirtualnej Azure](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) służy jako brama między lokalizacjami obciążeń w sieci wirtualnej Azure nawiązywania połączenia z lokacji lokalnej. Jest wymagany do łączenia się z lokacjami lokalnymi za pośrednictwem [tuneli VPN lokacja lokacja IPsec](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), lub za pomocą [ExpressRoute](../expressroute/expressroute-introduction.md) obwodów. Dla tuneli IPsec i IKE VPN bramy przeprowadzić Uzgodnienia IKE i ustanowienia tuneli IPsec sieci VPN S2S między sieciami wirtualnymi i lokacjami lokalnymi. Bramy sieci wirtualnej również przerwanie [sieci VPN typu punkt lokacja](../vpn-gateway/vpn-gateway-point-to-site-create.md).

## <a name="secure-remote-access"></a>Bezpieczny dostęp zdalny

Dane przechowywane w chmurze muszą być chronione przy użyciu zabezpieczeń wystarczających do zapobiegania wykorzystywaniu luk oraz zachowania poufności i integralności przesyłanych danych. Obejmują one kontrolki sieci powiązane z mechanizmami zarządzania dostępem i tożsamościami podlegającymi inspekcji w oparciu o zasady.

Wbudowane technologie kryptograficzne umożliwiają szyfrowanie komunikacji w obrębie wdrożeń i między nimi, między regionami świadczenia usługi Azure oraz komunikacji z platformy Azure do lokalnych centrów danych. Dostęp administratora do maszyn wirtualnych za pośrednictwem [sesji pulpitu zdalnego](../virtual-machines/windows/classic/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json), [zdalnej programu Windows PowerShell](http://blogs.technet.com/b/heyscriptingguy/archive/2013/09/07/weekend-scripter-remoting-the-cloud-with-windows-azure-and-powershell.aspx), i portalu Azure są zawsze szyfrowane.

Bezpieczne rozszerzenie lokalnego centrum danych do chmury, platforma Azure udostępnia zarówno [sieci VPN typu lokacja lokacja](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md) i [sieci VPN typu punkt lokacja](../vpn-gateway/vpn-gateway-point-to-site-create.md), oraz dedykowane łącza o [ExpressRoute](../expressroute/expressroute-introduction.md) (połączenia z siecią wirtualną Azure za pośrednictwem połączenia VPN są szyfrowane).

### <a name="how-azure-implements-secure-remote-access"></a>Implementowanie bezpiecznego dostępu zdalnego na platformie Azure

Połączeń z portalem Azure zawsze należy uwierzytelnieni, a potrzebują SSL/TLS. Można skonfigurować certyfikaty zarządzania, aby włączyć bezpieczne zarządzanie. Standardowe protokoły, takie jak [SSTP](https://technet.microsoft.com/magazine/2007.06.cableguy.aspx) i [IPsec](https://en.wikipedia.org/wiki/IPsec) są w pełni obsługiwane.

Usługa [Azure ExpressRoute](../expressroute/expressroute-introduction.md) umożliwia tworzenie prywatnych połączeń między centrami danych platformy Azure oraz infrastrukturą znajdującą się w lokalizacji lokalnej lub wspólnej. Połączenia ExpressRoute nie odbywają się za pośrednictwem publicznego Internetu. Oferują więcej niezawodności, szybkości szybsze niższe opóźnienia i lepsze zabezpieczenia niż typowe łącza internetowego. W niektórych przypadkach transferu danych między lokalnymi lokalizacji i Azure przy użyciu połączeń ExpressRoute również może spowodować znaczne oszczędności.

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

Platforma Azure udostępnia uwierzytelnionego rejestrowania zdarzeń związanych z zabezpieczeniami, które generują dziennik inspekcji i została zaprojektowana jako odporne na próby naruszenia. W tym informacje o systemie, takie jak dzienniki zdarzeń zabezpieczeń infrastruktury platformy Azure, maszyny wirtualne i usługi Azure AD. Monitorowanie zdarzeń zabezpieczeń obejmuje zbierania zdarzeń, takie jak zmiany adresów IP serwera DHCP lub DNS; Próba dostępu do portów, protokoły lub adresy IP, które są blokowane przez projekt; zmiany w ustawieniach zasad lub zapora zabezpieczeń; Tworzenie konta lub grupy; i nieoczekiwane procesy lub instalacja sterownika.

![Ochrona przed złośliwym kodem zapewniana przez Microsoft na platformie Azure](./media/azure-security-getting-started/sec-azgsfig5.PNG)

Dzienniki inspekcji rejestrujące dostęp i działania uprawnionych użytkowników, próby uzyskania autoryzowanego i nieautoryzowanego dostępu, wyjątki systemowe i zdarzenia zabezpieczeń danych są przechowywane przez określony okres. Okres przechowywania dzienników jest ustalany przez użytkownika, ponieważ do niego należy konfigurowanie gromadzenia danych w dziennikach i ich przechowywania.

### <a name="how-azure-implements-logging-and-monitoring"></a>Implementowanie rejestrowania i monitorowania na platformie Azure

Platforma Azure wdraża agentów zarządzania i agentów usługi Azure Security Monitor w każdym zarządzanym węźle obliczeniowym, węźle magazynu lub węźle sieci szkieletowej, tak natywnym, jak i wirtualnym. Każdy agent zarządzania jest skonfigurowany w taki sposób, aby wykonywać uwierzytelnianie na koncie magazynu zespołu usługi przy użyciu certyfikatu uzyskanego z magazynu certyfikatów platformy Azure oraz przekazywać wstępnie skonfigurowane dane diagnostyczne i dane zdarzeń do konta magazynu. Agenci nie są wdrażani na maszynach wirtualnych klientów.

Administratorzy platformy Azure uzyskują dostęp do dzienników za pośrednictwem portalu sieci Web zapewniającego uwierzytelniony i kontrolowany dostęp do dzienników. Administrator może filtrować, korelować i analizować dzienniki. Konta magazynu zespołu usługi Azure przeznaczone dla dzienników są chronione przed bezpośrednim dostępem administratora, aby zapobiegać naruszeniom bezpieczeństwa dziennika.

Firma Microsoft zbiera dzienniki urządzeń sieciowych przy użyciu protokołu Syslog i serwery hosta przy użyciu usługi Microsoft Audit Collection Services (ACS). Te dzienniki są umieszczane w dziennika bazy danych, z której zostaną wygenerowane alerty dla zdarzeń podejrzane. Administrator może uzyskać dostęp do dzienników i je analizować.

[Diagnostyka Azure](https://msdn.microsoft.com/library/azure/gg433048.aspx) to funkcja platformy Azure, która umożliwia zbieranie danych diagnostycznych z aplikacji uruchomionych w systemie Azure. To są dane diagnostyczne dla debugowania i rozwiązywania problemów, pomiaru wydajności monitorowania użycia zasobów, analizy ruchu, planowanie pojemności i inspekcji. Po zebraniu danych diagnostycznych mogą one zostać przesłane na konto magazynu platformy Azure w celu ich utrwalenia. Transfery albo mogą być planowane lub na żądanie.

## <a name="threat-mitigation"></a>Ograniczenie zagrożeń

Oprócz izolacji, szyfrowania i filtrowania platforma Azure wykorzystuje szereg mechanizmów i procesów ograniczania zagrożeń mających na celu ochronę infrastruktury i usług. Należą do nich wewnętrzne środki kontroli i technologie używane do wykrywania i korygowania zaawansowanych zagrożeń, takich jak ataki DDoS, eskalacja uprawnień i zagrożenia z listy [OWASP Top 10](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).

Środki kontroli zabezpieczeń i procesy zarządzania ryzykiem wdrożone przez firmę Microsoft w celu ochrony infrastruktury chmury pozwalają ograniczyć ryzyko wystąpienia zdarzeń naruszenia bezpieczeństwa. W przypadku, gdy wystąpi zdarzenie, zespół zarządzania zdarzeniami zabezpieczeń (SIM) w obrębie zespołu Microsoft Online Services zabezpieczeń i zgodności (OSSC) jest gotowy do odpowiedzi w dowolnym momencie.

### <a name="how-azure-implements-threat-mitigation"></a>Implementowanie ograniczania zagrożeń na platformie Azure

Platforma Azure ma kontroli bezpieczeństwa w miejscu, aby zaimplementować ograniczenia zagrożeń, a także aby pomóc zminimalizować potencjalne zagrożenia w swoich środowiskach klientów. Poniższa lista zawiera podsumowanie możliwości ograniczenie zagrożeń oferowanych na platformie Azure:

* [Azure ochrony przed złośliwym oprogramowaniem](azure-security-antimalware.md) jest włączona domyślnie na wszystkich serwerach infrastruktury. Możesz opcjonalnie można udostępnić go w maszynach wirtualnych.
* Firma Microsoft prowadzi ciągłe monitorowanie serwerów, sieci i aplikacji w celu wykrywania zagrożeń i zapobiegania wykorzystywaniu luk. Zautomatyzowane alerty powiadamiają administratorów o nietypowych zachowaniach, umożliwiając im podejmowanie działań naprawczych w odpowiedzi na zagrożenia wewnętrzne i zewnętrzne.
* W ramach subskrypcji, takie jak zapory aplikacji sieci web z wdrożeniem rozwiązania innych firm zabezpieczeń [Barracuda](https://techlib.barracuda.com/ng54/deployonazure).
* Podejście firmy Microsoft do testowania penetracji obejmuje "[zespołu kart interfejsu sieciowego czerwony](http://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf)," który obejmuje specjalistom ds. zabezpieczeń firmy Microsoft do zaatakowania systemów produkcyjnych na żywo (z systemem innym niż klienta) na platformie Azure, aby przetestować ochronę przed rzeczywistych, zaawansowane, zagrożenia.
* Zintegrowane systemy wdrażania umożliwiają zarządzanie dystrybucją i instalacją poprawek zabezpieczeń na platformie Azure.

## <a name="next-steps"></a>Kolejne kroki

[Centrum zaufania Azure](https://azure.microsoft.com/support/trust-center/)

[Blog zespołu ds. zabezpieczeń platformy Azure](http://blogs.msdn.com/b/azuresecurity/)

[Centrum zabezpieczeń firmy Microsoft](https://technet.microsoft.com/library/dn440717.aspx)

[Blog usługi Active Directory](http://blogs.technet.com/b/ad/)
