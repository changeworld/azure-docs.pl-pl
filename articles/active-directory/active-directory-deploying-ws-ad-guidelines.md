---
title: Wskazówki dotyczące wdrażania systemu Windows serwer usługi Active Directory na maszynach wirtualnych Azure | Dokumentacja firmy Microsoft
description: Jeśli wiesz, jak wdrażać usługi domenowe AD i usługi federacyjnej AD lokalnie, Dowiedz się, jak działają na maszynach wirtualnych Azure.
services: active-directory
documentationcenter: ''
author: femila
manager: mtillman
editor: ''
ms.assetid: 04df4c46-e6b6-4754-960a-57b823d617fa
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/20/2018
ms.author: femila
ms.openlocfilehash: c2d58e056cdb285be51d259492e11e6ae37b253e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
---
# <a name="guidelines-for-deploying-windows-server-active-directory-on-azure-virtual-machines"></a>Wskazówki dotyczące wdrażania usługi Active Directory systemu Windows Server na maszynach wirtualnych Azure
W tym artykule opisano istotne różnice między wdrażanie systemu Windows serwera usług domenowych Active Directory (AD DS) i Active Directory Federation Services (AD FS) lokalnie i wdrażania ich na maszynach wirtualnych Microsoft Azure.

## <a name="scope-and-audience"></a>Zakres i odbiorców
Ten artykuł jest przeznaczony dla tych już z wdrożeniem lokalne usługi Active Directory. Obejmuje ona różnice między wdrożeniem usługi Active Directory na Microsoft Azure maszyny wirtualne/sieciach wirtualnych platformy Azure i wdrożeniach usługi Active Directory tradycyjnych, lokalnie. Maszyny wirtualne platformy Azure i sieci wirtualnych platformy Azure są częścią infrastruktury jako — usługa (IaaS) oferty dla organizacji na lepsze wykorzystanie zasobów obliczeniowych w chmurze.

Dla tych, które nie są jeszcze znane z wdrażanie usługi Active Directory, zobacz [przewodniku wdrażania usług AD DS](https://technet.microsoft.com/library/cc753963) lub [Planowanie wdrożenia usług AD FS](https://technet.microsoft.com/library/dn151324.aspx) odpowiednio.

W tym artykule zakłada, że czytnik zapoznać się z następujące kwestie:

* Windows Server AD DS wdrażania i zarządzania
* Wdrażanie i konfigurowanie systemu DNS do obsługi infrastruktury systemu Windows Server AD DS
* Windows Server AD FS wdrażania i zarządzania
* Wdrażanie, konfigurowanie i zarządzanie aplikacjami danej firmy (usługi sieci web i witryn sieci Web) mogą używających tokenów systemu Windows Server AD FS
* Pojęcia ogólne maszyny wirtualnej, takie jak Konfigurowanie maszyny wirtualnej, dysków wirtualnych i sieci wirtualnych

W tym artykule omówiono wymagania dotyczące scenariusza hybrydowego wdrożenia w których systemu Windows Server AD DS lub AD FS częściowo wdrożonej lokalnymi i częściowo wdrożonych na maszynach wirtualnych Azure. Dokument obejmuje najpierw krytyczne różnice między systemem Windows Server AD DS i AD FS na maszynach wirtualnych Azure i lokalnego, a punkty ważnych decyzji, które mają wpływ na projektowanie i wdrażanie. Pozostałe papieru opisano wskazówki dla każdego z punktów decyzyjnych bardziej szczegółowo i sposób stosowania wytyczne do różnych scenariuszy wdrażania.

W tym artykule omówiono konfiguracji [usługi Azure Active Directory](http://azure.microsoft.com/services/active-directory/), czyli opartego na interfejsie REST usługi, która udostępnia możliwości kontroli dostępu i zarządzania tożsamościami dla aplikacji w chmurze. Azure Active Directory (Azure AD) i Windows Server usług AD DS jednak mają na celu współpracują dla rozwiązania do zarządzania tożsamościami i dostępem dla współczesnych hybrydowego IT środowisk i nowoczesnych aplikacji. Aby lepiej poznać różnice i relacje między systemu Windows Server AD DS i Azure AD, należy rozważyć następujące kwestie:

1. Windows Server AD DS może działać w chmurze na maszynach wirtualnych Azure korzysta z platformy Azure rozszerzenie lokalnego centrum danych do chmury.
2. Można użyć usługi Azure AD ma zostać przypisany Twojej użytkownikom logowanie jednokrotne do aplikacji oprogramowania — jako — usługa (SaaS). Microsoft Office 365 używa tej technologii, na przykład i aplikacje działające na platformie Azure lub innych platform chmury można jej również użyć.
3. Można użyć usługi Azure AD (jej usługi kontroli dostępu) umożliwiający użytkownikom rejestrowanie przy użyciu tożsamości z usługi Facebook, Google, Microsoft i innych dostawców tożsamości dla aplikacji, które znajdują się w chmurze lub lokalnie.

Aby uzyskać więcej informacji na temat tych różnic, zobacz [tożsamości Azure](fundamentals-identity.md).

## <a name="related-resources"></a>Powiązane zasoby
Może pobierać i uruchamiać [oceny gotowości maszyny wirtualnej Azure](https://www.microsoft.com/download/details.aspx?id=40898). Ocena zostanie automatycznie sprawdzać w lokalnym środowisku i generować raportu niestandardowego w oparciu ze wskazówkami w tym temacie ułatwiają migrację w środowisku Azure.

Zaleca się, że można również przejrzeć samouczki, prowadnice i klipów wideo, które obejmują następujące tematy:

* [Skonfiguruj sieć wirtualną tylko w chmurze w portalu Azure](../virtual-network/quick-create-portal.md)
* [Konfigurowanie sieci VPN lokacja lokacja w portalu Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md)
* [Instalowanie nowego lasu usługi Active Directory w sieci wirtualnej platformy Azure](active-directory-new-forest-virtual-machine.md)
* [Instalowanie repliki kontrolera domeny usługi Active Directory na platformie Azure](active-directory-install-replica-active-directory-domain-controller.md)
* [Microsoft Azure IaaS specjalista IT: (01) maszyny wirtualnej podstawy](https://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
* [Microsoft Azure IaaS specjalista IT: (05) tworzenie sieci wirtualnych i łączności między lokalizacjami](https://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

## <a name="introduction"></a>Wprowadzenie
Podstawowe wymagania dotyczące wdrażania usługi Active Directory systemu Windows Server na maszynach wirtualnych Azure różnią się bardzo mało z wdrażania go w lokalnych maszyn wirtualnych (i w pewnym stopniu maszyn fizycznych). Na przykład w przypadku systemu Windows Server AD DS, jeśli kontrolery domeny (DC), które wdrażasz na maszynach wirtualnych Azure replik w istniejącym lokalnymi firmowej domenie lub lesie, a następnie wdrożenia usługi Azure przede wszystkim może być traktowana w taki sam sposób jak inne dodatkowej lokacji usługi Active Directory systemu Windows Server mogą traktować. Oznacza to, że podsieci muszą być zdefiniowane w systemie Windows Server AD DS, lokacji utworzone, podsieci połączone z tą lokacją i podłączone do innych lokacji za pomocą odpowiednich linków lokacji. Istnieją jednak pewne różnice, które są wspólne dla wszystkich wdrożeń platformy Azure i niektóre który różny w zależności od scenariusza wdrażania. Poniżej opisano dwa podstawowe różnice:

### <a name="azure-virtual-machines-may-need-connectivity-to-the-on-premises-corporate-network"></a>Maszyn wirtualnych platformy Azure może wymagać połączenia z firmową siecią lokalną.
Połączenie maszyn wirtualnych platformy Azure do sieci firmowej lokalnymi wymaga sieci wirtualnej platformy Azure, która zawiera lokacja do lokacji lub punktu lokacji wirtualnego prywatnej (VPN) składnika sieci nawiązać bezproblemowo maszyn wirtualnych platformy Azure i lokalnej maszyny. Ten składnik sieci VPN można również włączyć komputery należące do domeny lokalnej można uzyskać dostępu do domeny usługi Active Directory systemu Windows Server, w których kontrolery domeny są obsługiwane wyłącznie na maszynach wirtualnych Azure. Należy pamiętać, że w przypadku niepowodzenia VPN uwierzytelnianie i inne operacje, które są zależne od usługi Active Directory systemu Windows Server również zakończą się. Gdy użytkownicy mogą mieć możliwość zalogować się przy użyciu istniejących poświadczeń, wszystkie peer-to-peer w pamięci podręcznej lub próby uwierzytelniania klient serwer, dla których biletów jeszcze nie być wystawiane lub są nieodświeżone zakończą się niepowodzeniem.

Zobacz [sieci wirtualnej](http://azure.microsoft.com/documentation/services/virtual-network/) pokaz wideo i listę samouczki krok po kroku, w tym [skonfigurowania sieci VPN lokacja-lokacja w portalu Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md).

> [!NOTE]
> Windows Server Active Directory można także wdrożyć w sieci wirtualnej platformy Azure, która nie ma łączności z siecią lokalną. Wskazówki zawarte w tym temacie jednak założono, że sieć wirtualną platformy Azure jest używana, ponieważ zapewnia możliwości, które są niezbędne do systemu Windows Server, adresów IP.
> 
> 

### <a name="static-ip-addresses-can-be-configured-with-azure-powershell"></a>Statyczne adresy IP można skonfigurować za pomocą programu Azure PowerShell
Dynamiczne adresy są przydzielane domyślnie, ale jeśli chcesz przypisać statyczny adres IP zamiast tego należy użyć polecenia cmdlet Set-AzureStaticVNetIP. To polecenie cmdlet ustawia statyczny adres IP, który będzie aktualny za pośrednictwem naprawą usługi i zamykania maszyny Wirtualnej ponownego uruchomienia komputera. Aby uzyskać więcej informacji, zobacz [statyczny adres IP wewnętrznego dla maszyn wirtualnych](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/). Można również skonfigurować statyczny adres IP podczas tworzenia maszyny Wirtualnej w portalu Azure, jak pokazano poniżej. Aby uzyskać więcej informacji, zobacz [utworzyć Maszynę wirtualną za pomocą statycznego publicznego adresu IP przy użyciu portalu Azure](../virtual-network/virtual-network-deploy-static-pip-arm-portal.md).

![Zrzut ekranu przedstawiający krok, aby dodać statyczny adres IP, podczas tworzenia maszyny Wirtualnej](media/active-directory-deploying-ws-ad-guidelines/static-ip.png)

## <a name="BKMK_Glossary"></a>— Warunki
Poniżej przedstawiono listę niepełna postanowień dotyczących różnych technologii platformy Azure, które zostanie dodane odwołanie w tym artykule.

* **Maszyny wirtualne platformy Azure**: oferty IaaS na platformie Azure, umożliwiający klienci mogą wdrożyć maszyny wirtualne z systemami niemal wszystkie tradycyjnie lokalnej obciążenie serwera.
* **Sieć wirtualna platformy Azure**: usługi sieci na platformie Azure, który umożliwia klientom tworzenie i zarządzanie sieciami wirtualnymi na platformie Azure oraz bezpiecznego łączenia ich z własnych lokalną infrastrukturę sieci przy użyciu wirtualnej sieci prywatnej (VPN).
* **Wirtualny adres IP**: internetowy adres IP, który nie jest powiązany z określoną kartą interfejsu komputera lub sieci. Usługi w chmurze są przypisywane wirtualnego adresu IP do odbierania ruchu sieciowego, który jest przekierowywany do maszyny Wirtualnej platformy Azure. Wirtualny adres IP jest właściwością usługi w chmurze, która może zawierać jeden lub więcej maszyn wirtualnych platformy Azure. Należy również zauważyć, że sieć wirtualną platformy Azure może zawierać co najmniej jedną usługę chmury. Wirtualne adresy IP zapewniają natywnej funkcji równoważenia obciążenia.
* **Dynamicznego adresu IP**: jest to adres IP, który jest tylko wewnętrzna. Go powinna być skonfigurowana jako statyczny adres IP (przy użyciu polecenia cmdlet Set-AzureStaticVNetIP) dla maszyn wirtualnych, w których są uruchomione role serwera DNS kontrolera domeny.
* **Naprawianie usługi**: proces, w którym Azure automatycznie zwraca usługi uruchomione ponownie po wykryciu, że usługa nie powiodła się. Naprawianie usługi jest jednym z tych aspektów platformy Azure, która obsługuje dostępność i odporność. Podczas, gdy jest to mało prawdopodobne, wynik następujące usługi naprawianie zdarzenia dla kontrolera domeny uruchomiony na maszynie Wirtualnej jest podobny do nieplanowane ponownego rozruchu, ale ma kilka efekty uboczne:
  
  * Wirtualna karta sieciowa w maszynie Wirtualnej zostanie zmieniony.
  * Adres MAC karty sieci wirtualnej zostanie zmieniony.
  * Identyfikator procesora/Procesora maszyny wirtualnej zostanie zmieniony.
  * Konfigurację adresu IP wirtualnej karty sieciowej nie zmieni się, jak długo maszyny Wirtualnej jest podłączony do sieci wirtualnej i jest statyczny adres IP maszyny Wirtualnej.
  
  Żadna z tych zachowań wpływa na Windows Server Active Directory, ponieważ ma ona zależność adres MAC lub identyfikator procesora/Procesora i wszystkie wdrożenia usługi Active Directory systemu Windows Server na platformie Azure są zalecane do uruchomienia na sieć wirtualną platformy Azure zgodnie z powyższym opisem.

## <a name="is-it-safe-to-virtualize-windows-server-active-directory-domain-controllers"></a>Jest to bezpieczne wirtualizację kontrolerów domeny usługi Active Directory systemu Windows Server?
Wdrożenie kontrolerów domeny katalogu Active systemu Windows Server na maszynach wirtualnych Azure jest zgodnie z wytycznymi sam, jak działają kontrolery domeny lokalnej maszynie wirtualnej. Uruchomienie zwirtualizowanych kontrolerów domeny jest bezpieczne rozwiązanie, tak długo, jak wytyczne dotyczące tworzenia kopii zapasowej i przywracanie kontrolery domeny zostaną wykonane. Aby uzyskać więcej informacji dotyczących ograniczenia i wytyczne dotyczące uruchamiania zwirtualizowanych kontrolerów domeny, zobacz [uruchamianie kontrolerów domeny w funkcji Hyper-V](https://technet.microsoft.com/library/dd363553).

Funkcji hypervisor Podaj lub trivialize technologie, które mogą spowodować problemy w przypadku wielu systemów rozproszonych, w tym Windows Server Active Directory. Na przykład na serwerze fizycznym, można sklonować dysku lub użyj metody nieobsługiwany do przywrócenia stanu serwera, w tym o korzystaniu z sieci SAN i tak dalej, ale operacją na serwerze fizycznym jest trudniejsze niż przywracania migawki maszyny wirtualnej w ramach funkcji hypervisor. System Azure oferuje funkcje, które może spowodować w tym samym stanie niepożądane. Na przykład nie należy kopiować pliki VHD kontrolerów domeny zamiast regularnego wykonywania kopii zapasowych przywracanie ich może powodować podobna sytuacja przy użyciu funkcji przywracania migawki.

Takie wycofań wprowadzenie rozbieżnościach numerów USN, które mogą prowadzić do stanów trwale rozbieżności między kontrolerów domeny. Które mogą spowodować problemy takie jak:

* Obiektach pokutujących
* Niespójne hasła
* Wartości atrybutów niespójna
* Niezgodność schematu, jeśli wzorzec schematu jest wycofana

Aby uzyskać więcej informacji na temat wpływ kontrolerów domeny, zobacz [numerów USN i wycofanie numerów USN](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv.aspx#usn_and_usn_rollback).

Począwszy od systemu Windows Server 2012, [dodatkowe zabezpieczenia są tworzone usługach AD DS](https://technet.microsoft.com/library/hh831734.aspx). Te zabezpieczenia chronić zwirtualizowanych kontrolerów domeny przed wyżej wymienione problemy, tak długo, jak platforma podstawowa funkcja hypervisor obsługuje identyfikator generacji maszyny Wirtualnej. Azure obsługuje identyfikator VM-GenerationID, co oznacza, że kontrolery domeny z systemem Windows Server 2012 lub nowszego Azure maszyny wirtualne mają dodatkowe zabezpieczenia.

> [!NOTE]
> Należy zamknąć i ponownie uruchomić Maszynę wirtualną działającą roli kontrolera domeny w usłudze Azure w ramach systemu operacyjnego gościa, zamiast **Shut Down** opcji w porta platformy Azure. Obecnie można zamknąć maszyny Wirtualnej za pomocą portalu powoduje, że można cofnąć przydziału maszyny Wirtualnej. Deallocated maszyna wirtualna ma tę zaletę nie są naliczane opłaty, ale jego również resetuje identyfikator VM-GenerationID, czyli niepożądanych dla kontrolera domeny. Podczas generacji maszyny Wirtualnej zostanie zresetowana, bazy danych usług AD DS atrybut invocationID jest również zresetować puli identyfikatorów RID jest odrzucany i folderu SYSVOL jest oznaczony jako nieautorytatywny. Aby uzyskać więcej informacji, zobacz [wprowadzenie do wirtualizacji usług domenowych w usłudze Active Directory (AD DS)](https://technet.microsoft.com/library/hh831734.aspx) i [bezpieczna wirtualizacja usługi DFSR](http://blogs.technet.com/b/filecab/archive/2013/04/05/safely-virtualizing-dfsr.aspx).
> 
> 

## <a name="why-deploy-windows-server-ad-ds-on-azure-virtual-machines"></a>Dlaczego wdrażania systemu Windows Server AD DS na maszynach wirtualnych platformy Azure?
Wiele scenariuszy wdrażania systemu Windows Server AD DS dobrze nadają się do wdrażania maszyn wirtualnych na platformie Azure. Na przykład załóżmy, że masz firmy w Europie, która wymaga uwierzytelnienia użytkowników w lokalizacji zdalnej w Azji. Firma nie ma wcześniej wdrożona systemu Windows Server Active Directory kontrolerów domeny w Azji z powodu kosztów wdrażania ich i ograniczona doświadczenia po wdrożeniu serwerów zarządzania. W związku z tym żądania uwierzytelniania od Azja są obsługiwane przez kontrolery domeny w Europie nieoptymalne wyniki. W takim przypadku można wdrożyć kontrolera domeny na maszynie Wirtualnej, które zostały określone, muszą być uruchamiane w centrum danych Azure w Azji. Dołączanie kontrolera domeny do sieci wirtualnej platformy Azure, który jest podłączony bezpośrednio do zdalnej lokalizacji będzie poprawić wydajność uwierzytelniania.

Azure jest również odpowiednie zamiast do lokacji odzyskiwanie po awarii w przeciwnym razie kosztowne. Stosunkowo niska — koszty obsługi niewielkiej liczby kontrolerów domeny i jednej sieci wirtualnych na platformie Azure reprezentuje atrakcyjną alternatywą.

Na koniec można wdrożyć aplikację sieci na platformie Azure, takich jak SharePoint, która wymaga usługi Active Directory systemu Windows Server, ale ma nie zależności w sieci lokalnej lub firmowych systemu Windows serwer usługi Active Directory. W takim przypadku wdrożeniem izolowanego lasu na platformie Azure w celu spełnienia programu SharePoint wymagania serwera jest optymalna. Ponownie wdrażanie aplikacji sieciowych, które wymagają łączności z siecią lokalną a firmowej usługi Active Directory jest również obsługiwany.

> [!NOTE]
> Ponieważ zapewnia połączenie warstwy 3 składnik sieci VPN, który zapewnia łączność w sieci wirtualnej platformy Azure i siecią lokalną można również włączyć serwery Członkowskie, które lokalnie wykorzystać kontrolery domeny, które działają jako maszyny wirtualnej platformy Azure w sieci wirtualnej platformy Azure. Jednak jeśli sieć VPN jest niedostępny, komunikacja między komputerami lokalnymi i kontrolery domeny z systemem Azure nie będzie działać, wynikowy uwierzytelniania i różnych innych błędów.  
> 
> 

## <a name="contrasts-between-deploying-windows-server-active-directory-domain-controllers-on-azure-virtual-machines-versus-on-premises"></a>Różni się między wdrażania kontrolerów domeny usługi Active Directory systemu Windows Server na maszynach wirtualnych platformy Azure i lokalnymi
* W żadnym scenariuszu wdrożenia usługi Active Directory systemu Windows Server, który zawiera więcej niż jednej maszyny Wirtualnej należy użyć sieci wirtualnej platformy Azure spójności adresów IP. Należy pamiętać, że w tym przewodniku założono, że kontrolery domeny są uruchomione na sieć wirtualną platformy Azure.
* Podobnie jak w przypadku lokalnego kontrolerów domeny, zaleca się statycznych adresów IP. Statyczny adres IP można skonfigurować tylko za pomocą programu Azure PowerShell. Zobacz [statyczny adres IP wewnętrznego dla maszyn wirtualnych](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/) więcej szczegółów. Jeśli monitorowanie systemów lub innych rozwiązań sprawdzających konfiguracji statycznego adresu IP w systemie operacyjnym gościa, ten sam statyczny adres IP można przypisać do właściwości karty sieciowej maszyny wirtualnej. Należy jednak pamiętać, że karty sieciowej zostaną usunięte, jeśli maszyna wirtualna ulega naprawą usługi lub jest zamknięta w portalu, a jego adres cofnięciu przydziału. W takim przypadku statyczny adres IP w ramach gościa należy zresetować.
* Wdrażanie maszyn wirtualnych w sieci wirtualnej nie pociąga za sobą (ani nie wymaga) łączności do sieci lokalnej. sieć wirtualna umożliwia jedynie tej możliwości. Należy utworzyć wirtualnej sieci prywatnej komunikacji między Azure i siecią lokalną. Należy wdrożyć punkt końcowy sieci VPN w sieci lokalnej. Sieć VPN jest otwarty z platformy Azure w sieci lokalnej. Aby uzyskać więcej informacji, zobacz [omówienie sieci wirtualnych](../virtual-network/virtual-networks-overview.md) i [skonfigurowania sieci VPN lokacja-lokacja w portalu Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md).

> [!NOTE]
> Opcja [utworzyć VPN punkt lokacja](../vpn-gateway/vpn-gateway-point-to-site-create.md) jest dostępny dla poszczególnych komputerów z systemem Windows bezpośrednie łączenie się z sieci wirtualnej platformy Azure.
> 
> 

* Niezależnie od tego, czy tworzenia wirtualnej sieci lub nie, Azure opłaty za wyjście ruch, ale nie wejściowych. Różne opcje projektowania usługi Active Directory systemu Windows Server może mieć wpływ na ilość ruchu wyjście jest generowany przez wdrożenie. Na przykład wdrożenia kontrolera domeny tylko do odczytu (RODC) ogranicza ruch wychodzący ruch nie jest replikowany ruchu wychodzącego. Decyzja o wdrożeniu kontrolera RODC musi porównywany trzeba wykonywać operacje zapisu dla kontrolera domeny i [zgodności](https://technet.microsoft.com/library/cc755190) że aplikacji i usług w lokacji mają z kontrolera RODC. Aby uzyskać więcej informacji o opłatach ruchu sieciowego, zobacz [Azure, w skrócie cennik](http://azure.microsoft.com/pricing/).
* Gdy masz pełne kontrolować za pośrednictwem którego serwera zasobów do użycia dla maszyn wirtualnych na obszarze firmy, takich jak ilość pamięci RAM, rozmiar dysku i itd., na platformie Azure musisz wybrać z listy wstępnie skonfigurowanego serwera rozmiary. Kontroler domeny dysk z danymi na potrzeby oprócz dysku systemu operacyjnego w celu przechowywania bazy danych usługi Active Directory systemu Windows Server.

## <a name="can-you-deploy-windows-server-ad-fs-on-azure-virtual-machines"></a>Można wdrażać systemu Windows Server AD FS na maszynach wirtualnych Azure?
Tak, można wdrożyć system Windows Server AD FS na maszynach wirtualnych Azure i [najlepszych rozwiązań dotyczących wdrażania usług AD FS](https://technet.microsoft.com/library/dn151324.aspx) lokalnymi stosowane jednakowo do wdrożenia usług AD FS w systemie Azure. Ale niektóre z najlepszych rozwiązań, takich jak Równoważenie obciążenia i wysokiej dostępności wymagane technologie poza co usługi AD FS oferuje, sama. Musi być one udostępniane przez podstawowej infrastruktury. Załóżmy przejrzenia niektórych z tych najlepszych rozwiązań i zobacz, jak może zostać osiągnięty przy użyciu maszyn wirtualnych platformy Azure i sieci wirtualnej platformy Azure.

1. **Nigdy nie uwidacznia zabezpieczeń serwerów usługi tokenów (STS) bezpośrednio do Internetu.**
   
    Jest to ważne, ponieważ usługa tokenu Zabezpieczającego wystawia tokeny zabezpieczające. W związku z tym STS serwerów, takich jak serwery usług AD FS powinny być traktowane z poziomu ochrony jako kontroler domeny. Naruszenie tokenu Zabezpieczającego złośliwi użytkownicy mają możliwość wystawiania tokenów dostępu potencjalnie zawierające oświadczenia ich Wybieranie aplikacji jednostek uzależnionych oraz na innych serwerach usługi STS w ufające organizacji.
2. **Wdrożenie kontrolerów domeny usługi Active Directory dla wszystkich domen użytkownika w tej samej sieci co serwerów usług AD FS.**
   
    Serwery usług AD FS używają usług domenowych w usłudze Active Directory do uwierzytelniania użytkowników. Zaleca się wdrożenie kontrolerów domeny w tej samej sieci co serwery usług AD FS. Zapewnia ciągłość działania, w przypadku skojarzenia między sieć platformy Azure i sieci lokalnej jest uszkodzona i umożliwia mniejsze opóźnienia i zwiększa wydajność dla nazwy logowania.
3. **Wdrażanie wielu węzłów usług AD FS wysokiej dostępności i równoważenia obciążenia.**
   
    W większości przypadków awarii aplikacji, która umożliwia usług AD FS jest nie do przyjęcia, ponieważ krytyczne aplikacje, które wymagają tokeny zabezpieczające są często. W związku z tym ponieważ usługi AD FS znajduje się teraz w ścieżce krytycznej do uzyskiwania dostępu do aplikacji krytycznych dla misji, usługi AD FS należy wysokiej dostępności przy użyciu wielu serwerów proxy usług AD FS i serwery usług AD FS. Uzyskanie dystrybucji żądań, usługi równoważenia obciążenia są zwykle wdrażane przed zarówno serwerów proxy usługi AD FS i serwery usług AD FS.
4. **Wdrażanie co najmniej jeden węzeł serwera Proxy aplikacji sieci Web, aby uzyskać dostęp do Internetu.**
   
    Jeśli użytkownicy muszą uzyskać dostęp do aplikacji chronionej przez usługę AD FS, usługi AD FS musi być dostępny z Internetu. Jest to osiągane przez wdrożenie usługi Serwer Proxy aplikacji sieci Web. Zdecydowanie zaleca się wdrożyć więcej niż jeden węzeł na potrzeby wysokiej dostępności i równoważenia obciążenia.
5. **Ograniczanie dostępu w węzłach serwera Proxy aplikacji sieci Web do wewnętrznych zasobów sieciowych.**
   
    Aby umożliwić użytkownikom zewnętrzny dostęp do usług AD FS z Internetu, należy wdrożyć węzły serwera Proxy aplikacji sieci Web (lub Proxy usług AD FS w starszych wersjach systemu Windows Server). Węzły serwera proxy aplikacji sieci Web są bezpośrednio widoczne w Internecie. Nie są one wymagane do domeny i tylko wymagany jest dostęp do serwerów usług AD FS za pośrednictwem portów TCP 443 i 80. Zdecydowanie zaleca się, że komunikacja na wszystkich innych komputerach (zwłaszcza w kontrolerach domeny) jest zablokowana.
   
    Jest to zazwyczaj osiągnięte lokalnej za pomocą sieci obwodowej Zapory w trybie dozwolonych operacji ograniczyć ruch od sieci obwodowej do sieci lokalnej (oznacza to, że tylko ruchu z określonych adresów IP i za pośrednictwem portów określony jest dozwolone, a wszystkie inne ruch jest blokowany).

Na poniższym diagramie przedstawiono w tradycyjnych lokalnego wdrożenia usług AD FS.

![Diagram wdrażania usług federacyjnych Active Directory tradycyjnych, lokalnie](media/active-directory-deploying-ws-ad-guidelines/ADFS_onprem.png)

Jednak ponieważ Azure nie zapewnia natywnego, możliwości kompletne zapory inne opcje potrzebujesz ma być używany do ograniczania ruchu. W poniższej tabeli przedstawiono poszczególne opcje, a jego zalety i wady.

| Opcja | Zalety | Wadą |
| --- | --- | --- |
| [Listy ACL sieć platformy Azure](../virtual-network/virtual-networks-acl.md) |Mniej kosztowne i łatwiejsze konfiguracji początkowej |Konfiguracja ACL sieci dodatkowe wymagane, jeśli wszelkie nowe maszyny wirtualne są dodawane do wdrożenia |
| [Barracuda NG zapory](https://www.barracuda.com/products/ngfirewall) |Tryb listy dozwolonych operacji i nie wymaga sieci ACL konfiguracji |Zwiększenie kosztów i wprowadzania złożoności początkowej instalacji |

Ogólny proces wdrażania usług AD FS w tym przypadku są następujące:

1. Tworzenie sieci wirtualnej z łączności między lokalizacjami, za pomocą sieci VPN lub [ExpressRoute](http://azure.microsoft.com/services/expressroute/).
2. Wdrożenie kontrolerów domeny w sieci wirtualnej. Ten krok jest opcjonalny, ale zalecane.
3. Wdrażanie serwerów w sieci wirtualnej, przyłączonych do domeny usług AD FS.
4. Utwórz [zestaw o zrównoważonym obciążeniu wewnętrzny](http://azure.microsoft.com/blog/internal-load-balancing/) zawiera serwery usług AD FS i używa nowego prywatnego adresu IP w sieci wirtualnej (dynamicznego adresu IP).
   
   1. Zaktualizuj DNS w celu utworzenia nazwy FQDN, aby wskazywał prywatnego adresu IP (dynamiczny) z zestawu o zrównoważonym obciążeniu wewnętrzny.
5. Utworzenie usługi w chmurze (lub oddzielne sieci wirtualnej) w węzłach serwera Proxy aplikacji sieci Web.
6. Wdróż węzły serwera Proxy aplikacji sieci Web w sieci wirtualnej lub usługi w chmurze
   
   1. Utwórz zestaw o zrównoważonym obciążeniu zewnętrznego, który zawiera węzeł serwera Proxy aplikacji sieci Web.
   2. Zaktualizuj zewnętrzna nazwa DNS (FQDN), aby wskazywały na chmurze usługi publiczny adres IP (wirtualny adres IP).
   3. Skonfigurować serwer proxy usług AD FS do zastosowania nazwy FQDN, która odnosi się do zestawu o zrównoważonym obciążeniu wewnętrznych serwerów usług AD FS.
   4. Zaktualizuj Użyj nazwy FQDN zewnętrznej dla dostawcy oświadczeń witryn sieci web opartej na oświadczeniach.
7. Ograniczanie dostępu między serwera Proxy aplikacji sieci Web do dowolnego komputera w sieci wirtualnej usługi AD FS.

Aby ograniczyć ruch, zestawu równoważeniem obciążenia dla platformy Azure wewnętrznego modułu równoważenia obciążenia musi być skonfigurowana dla tylko ruch skierowany do portów TCP 80 i 443, a cały ruch do dynamicznego wewnętrzny adres IP zestawu o zrównoważonym obciążeniu jest przenoszony.

![Diagram sieci usług AD FS list kontroli dostępu przy TCP 443 i 80 dozwolone.](media/active-directory-deploying-ws-ad-guidelines/ADFS_ACLs.png)

Ruch do serwerów usług AD FS mogą być dozwolone tylko przez następujących źródeł:

* Azure wewnętrznego modułu równoważenia obciążenia.
* Adres IP administrator w sieci lokalnej.

> [!WARNING]
> Projekt musi zapobiec osiągnięciu innych maszyn wirtualnych w sieci wirtualnej platformy Azure lub dowolnej lokalizacji w sieci lokalnej węzłów serwera Proxy aplikacji sieci Web. Który jest możliwe przez skonfigurowanie reguł zapory w lokalnej urządzenia dla połączeń Expressroute lub urządzenia sieci VPN w połączeniach sieci VPN typu lokacja lokacja.
> 
> 

Wadą tej opcji jest potrzebne do skonfigurowania sieci listy ACL dla wielu urządzeń, w tym wewnętrznego modułu równoważenia obciążenia serwerów usług AD FS i innych serwerów, które zostaną dodane do sieci wirtualnej. Jeśli dowolne urządzenie zostanie dodany do wdrożenia bez konfigurowania sieci listy kontroli dostępu ograniczyć ruch związany z nim, całego wdrożenia może być zagrożone. Jeśli zmiany adresów IP w węzłach serwera Proxy aplikacji sieci Web, należy zresetować sieci listy kontroli dostępu (co oznacza, że serwery proxy powinna być skonfigurowana do używania [dynamicznych adresów IP](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/)).

![Usługi AD FS na platformie Azure za pomocą sieci listy kontroli dostępu.](media/active-directory-deploying-ws-ad-guidelines/ADFS_Azure.png)

Inną opcją jest użycie [Barracuda NG zapory](https://www.barracuda.com/products/ngfirewall) urządzenia do kontroli ruchu między serwerami proxy usług AD FS i serwery usług AD FS. Ta opcja jest zgodny z najlepsze rozwiązania dotyczące zabezpieczeń i wysokiej dostępności i wymaga mniej administracji po wstępnej instalacji, ponieważ urządzenia zapory NG Barracuda zapewnia tryb dozwolonych administracji zapory i może być zainstalowana bezpośrednio na sieć wirtualną platformy Azure. Która eliminuje potrzebę skonfigurować listy kontroli dostępu sieci zawsze, gdy nowy serwer jest dodawany do wdrożenia. Jednak ta opcja dodaje początkowe wdrożenie złożoność oraz koszt projektu.

W takim przypadku dwie sieci wirtualne są wdrażane zamiast jednego. Zadzwonimy ich VNet1 i VNet2. VNet1 zawiera serwerów proxy a VNet2 STSs i połączenie sieciowe do sieci firmowej. VNet1 w związku z tym jest fizycznie (chociaż praktycznie) izolowane od VNet2 i z kolei, od sieci firmowej. VNet1 jest następnie połączony VNet2 za pomocą specjalnych technologii tunelowania, znane jako transportu niezależne sieci architektura (TINA). Tunel TINA jest dołączony do każdej sieci wirtualnej za pomocą zapory Barracuda NG — jeden Barracuda w każdej sieci wirtualnej.  Dla wysokiej dostępności zaleca się wdrożenie dwóch Szympansy w każdej sieci wirtualnej; jeden aktywny innych pasywne. Oferują one bardzo zaawansowane możliwości zapory, które umożliwiają naśladować operacji sieci obwodowej tradycyjnych, lokalnie na platformie Azure.

![Usługi AD FS na platformie Azure za pomocą zapory.](media/active-directory-deploying-ws-ad-guidelines/ADFS_Azure_firewall.png)

Aby uzyskać więcej informacji, zobacz [usług AD FS: rozszerzanie aplikacji frontonu sieci lokalnej obsługujący oświadczenia z Internetem](#BKMK_CloudOnlyFed).

### <a name="an-alternative-to-ad-fs-deployment-if-the-goal-is-office-365-sso-alone"></a>Zamiast wdrożenia usług AD FS, jeśli celem jest Office 365 logowania jednokrotnego samodzielnie
Brak alternatywą do wdrażania usługi AD FS całkowicie Jeśli celem jest tylko do włączenia logowania dla usługi Office 365. W takim przypadku można po prostu wdrożyć narzędzia DirSync przy użyciu hasła synchronizacji lokalnie i osiągnąć ten sam rezultat zakończenia z złożoności minimalnego wdrożenia, ponieważ to rozwiązanie nie wymaga usług AD FS lub Azure.

W poniższej tabeli porównano działanie procesów logowania z włączonymi i wyłączonymi wdrażanie usług AD FS.

| Usługi Office 365 pojedynczego logowania za pomocą usług AD FS oraz narzędzia DirSync | Office 365 sam logowania jednokrotnego przy użyciu narzędzia DirSync i synchronizacji haseł |
| --- | --- |
| 1. Użytkownik loguje się do sieci firmowej i jest uwierzytelniony do usługi Active Directory systemu Windows Server. |1. Użytkownik loguje się do sieci firmowej i jest uwierzytelniony do usługi Active Directory systemu Windows Server. |
| 2. Użytkownik próbuje uzyskać dostępu do usługi Office 365 (Jestem @contoso.com). |2. Użytkownik próbuje uzyskać dostępu do usługi Office 365 (Jestem @contoso.com). |
| 3. Usługi Office 365 przekierowuje użytkownika do usługi Azure AD. |3. Usługi Office 365 przekierowuje użytkownika do usługi Azure AD. |
| 4. Ponieważ usługa Azure AD nie można uwierzytelnić użytkownika i rozumie, że istnieje relacja zaufania usług AD FS w lokalnym, przekierowuje użytkownika do usług AD FS. |4. Usługi Azure AD nie może zaakceptować biletów Kerberos bezpośrednio i nie relacji zaufania nie istnieje, wymaga wprowadzenia poświadczeń przez użytkownika. |
| 5. Użytkownik wysyła bilet Kerberos do Zabezpieczającego usług AD FS. |5. Użytkownik wprowadza to samo hasło lokalne, i usługi Azure AD weryfikuje je według nazwy użytkownika i hasła, która była synchronizowana przez narzędzie DirSync. |
| 6. Usługi AD FS przekształca bilet Kerberos do wymaganego tokenu format/oświadczeń i przekierowuje użytkownika do usługi Azure AD. |6. Usługi Azure AD przekierowuje użytkownika do usługi Office 365. |
| 7. Użytkownik jest uwierzytelniany do usługi Azure AD (przekształcania innego występuje). |7. Użytkownik może Zaloguj się do usługi Office 365 i OWA za pomocą tokenu usługi Azure AD. |
| 8. Usługi Azure AD przekierowuje użytkownika do usługi Office 365. | |
| 9. Użytkownik jest zalogowany trybie dyskretnym, do usługi Office 365. | |

W usłudze Office 365 z narzędzia DirSync z scenariusz synchronizacji haseł (nie usług AD FS) logowanie jednokrotne zastępuje "tego samego logowania jednokrotnego" gdzie "sam" po prostu oznacza, że użytkownicy musi ponownie wprowadzić swoje tych samych poświadczeń lokalnego, podczas uzyskiwania dostępu do usługi Office 365. Należy pamiętać, że te dane mogą zapamiętanych przez przeglądarkę użytkownika, aby pomóc w zmniejszeniu kolejnych monitów.

### <a name="additional-food-for-thought"></a>Dodatkowe myśl for żywności
* W przypadku wdrożenia serwera proxy usług AD FS na maszynie wirtualnej platformy Azure, łączność z serwerami usług AD FS jest potrzebny. Jeśli są one lokalnymi, zaleca się korzystać z połączenie sieci VPN typu lokacja lokacja dostarczonych przez sieci wirtualnej, aby umożliwić węzłów serwera Proxy aplikacji sieci Web do komunikowania się ze swoimi serwerami usług AD FS.
* W przypadku wdrożenia serwera usług AD FS na maszynie wirtualnej platformy Azure, konieczne jest łączność z kontrolerów domeny usługi Active Directory systemu Windows Server, magazynami atrybutów i bazy danych konfiguracji, a także wymagać ExpressRoute lub połączeń VPN lokacja lokacja między sieci wirtualnej platformy Azure i siecią lokalną.
* Opłaty są stosowane do całego ruchu z maszyn wirtualnych platformy Azure (ruch wychodzący). Jeśli koszt pobudzenie współczynnik, zaleca się wdrożyć węzły serwera Proxy aplikacji sieci Web na platformie Azure, pozostawiając usług AD FS serwerów lokalnych. W przypadku wdrożenia serwerów usług AD FS na również maszyn wirtualnych platformy Azure, do uwierzytelniania użytkowników lokalnych będzie powstaje dodatkowych kosztów. Ruch wychodzący generuje koszt niezależnie od tego, czy przechodzenie usługi ExpressRoute lub połączenia z siecią VPN lokacja lokacja.
* Jeśli zdecydujesz się używać równoważenia możliwości wysokiej dostępności serwerów usług AD FS obciążenia serwera natywnego platformy Azure, należy pamiętać, że Równoważenie obciążenia sieciowego zapewnia sond, które są używane w celu ustalenia kondycji maszyn wirtualnych w ramach usługi w chmurze. W przypadku maszyn wirtualnych platformy Azure (w przeciwieństwie do ról sieci web lub procesu roboczego) należy użyć niestandardowego sondowania od agenta, który odpowiada sond domyślne nie znajduje się na maszynach wirtualnych platformy Azure. Dla uproszczenia, można użyć niestandardowej sondowaniem TCP — wymagane jest tylko że połączenia TCP (segmentów TCP SYN wysyłane i odpowiedzi z segmentem TCP SYN ACK) pomyślnie w odniesieniu do kondycja maszyny wirtualnej. Można skonfigurować niestandardowe sondowania można użyć dowolnego portu TCP, do której są aktywnie nasłuchujących maszyn wirtualnych.

> [!NOTE]
> Komputery, które należy udostępnić ten sam zestaw portów bezpośrednio z Internetem (np. port 80 i 443) nie mogą współużytkować tego samego usługi w chmurze. Zalecane jest w związku z tym, że utworzyć chmura dedykowana usług dla serwerów systemu Windows Server AD FS w celu uniknięcia potencjalnych pokrywa się między wymagania dotyczące portów dla aplikacji i usług systemu Windows Server AD FS.
> 
> 

## <a name="deployment-scenarios"></a>Scenariusze wdrażania
W tej części opisano scenariusze wdrażania popularne, aby zwrócić uwagę na istotnych kwestii, które należy wziąć pod uwagę. Każdy scenariusz zawiera łącza, aby uzyskać więcej szczegółów na temat decyzji i czynniki do rozważenia.

1. [Usługi AD DS: Wdrażanie aplikacji obsługujących usługi AD DS z nie jest wymagany dla łączności z siecią firmową](#BKMK_CloudOnly)
   
    Na przykład usługi internetowy programu SharePoint jest wdrożony na maszynie wirtualnej platformy Azure. Aplikacja nie ma zależności zasobów sieci firmowej. Aplikacja wymaga systemu Windows Server AD DS, ale nie wymaga firmowej usługi AD DS dla systemu Windows Server.
2. [Usługi AD FS: Rozszerzanie aplikacji frontonu sieci lokalnej obsługujący oświadczenia z Internetem](#BKMK_CloudOnlyFed)
   
    Na przykład aplikacji obsługującej oświadczenia, który został pomyślnie wdrożony lokalnymi i używane przez użytkowników firmowej musi stać się niedostępny z Internetu. Aplikacja musi uzyskiwać bezpośrednio przez Internet, zarówno przez partnerów biznesowych za pomocą firmowej tożsamości i istniejący użytkownicy firmowej.
3. [Usług AD DS: Windows Server AD DS obsługujący aplikację, która wymaga łączności z siecią firmową wdrażania](#BKMK_HybridExt)
   
    Na przykład aplikacji obsługujących LDAP obsługuje uwierzytelnianie zintegrowane systemu Windows, który używa systemu Windows Server AD DS jako repozytorium dla danych konfiguracji i profil użytkownika jest wdrażana na maszynie wirtualnej platformy Azure. Jest pożądane dla aplikacji, aby korzystać z istniejących firmowej DS systemu Windows Server AD i zapewnienia logowania jednokrotnego. Aplikacja nie jest obsługujący oświadczenia.

### <a name="BKMK_CloudOnly"></a>1. Usługi AD DS: Wdrażanie aplikacji obsługujących usługi AD DS z nie jest wymagany dla łączności z siecią firmową
![Tylko w chmurze wdrożenia usług AD DS](media/active-directory-deploying-ws-ad-guidelines/ADDS_cloud.png)
**rysunek 1**

#### <a name="description"></a>Opis
Program SharePoint jest wdrożony na maszynie wirtualnej platformy Azure i aplikacji nie ma zależności zasobów sieci firmowej. Aplikacja wymaga systemu Windows Server AD DS, ale nie *nie* wymagają firmowej usługi systemu Windows Server AD DS. Brak protokołu Kerberos lub federacyjnych relacji zaufania są wymagane, ponieważ użytkownicy są własnym udostępnianych za pośrednictwem aplikacji do domeny usług AD systemu Windows Server, również przechowywane w chmurze na maszynach wirtualnych platformy Azure.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Zagadnienia dotyczące scenariusza i jak obszarów technologii dotyczą scenariusza
* [Topologia sieci](#BKMK_NetworkTopology): tworzenie sieci wirtualnej platformy Azure, bez łączności między lokalizacjami (znanej także jako połączenie lokacja lokacja).
* [Konfiguracja wdrażania kontrolera domeny](#BKMK_DeploymentConfig): Wdrażanie nowego kontrolera domeny w lesie usługi Active Directory systemu Windows Server nowy, jednej domenie. To powinny być wdrażane wraz z serwera DNS systemu Windows.
* [Topologia lokacji usługi Active Directory systemu Windows Server](#BKMK_ADSiteTopology): Użyj domyślnej witryny Windows Server Active Directory (wszystkim komputerom będzie w nazwa-pierwszej-lokacji).
* [Adresowanie IP i DNS](#BKMK_IPAddressDNS):
  
  * Ustawianie statycznego adresu IP dla kontrolera domeny za pomocą polecenia cmdlet Set-AzureStaticVNetIP Azure PowerShell.
  * Instalowanie i konfigurowanie systemu Windows serwera DNS na kontrolerach domeny na platformie Azure.
  * Skonfiguruj właściwości sieci wirtualnej o nazwie i adresie IP maszyny wirtualnej, który jest hostem ról serwera DNS i kontrolera domeny.
* [Wykaz globalny](#BKMK_GC): pierwszy kontroler domeny w lesie musi być serwerem wykazu globalnego. Dodatkowe kontrolery domeny powinien również być skonfigurowany jako wykazów globalnych, ponieważ w lesie pojedynczej domeny wykazu globalnego nie wymaga żadnych dodatkowych działań z kontrolera domeny.
* [Rozmieszczenie systemu Windows Server AD DS w bazie danych i folderu SYSVOL](#BKMK_PlaceDB): dodanie dysku danych do kontrolerów domeny uruchomione jako maszyny wirtualne platformy Azure do przechowywania bazy danych, dzienników i folderu SYSVOL usługi Active Directory systemu Windows Server.
* [Kopia zapasowa i przywracanie](#BKMK_BUR): ustalić, które chcesz przechowywać kopie zapasowe stanu systemu. Jeśli to konieczne, należy dodać inny dysk danych do maszyny Wirtualnej kontrolera domeny do przechowywania kopii zapasowych.

### <a name="BKMK_CloudOnlyFed"></a>2 AD FS: rozszerzanie aplikacji frontonu sieci lokalnej obsługujący oświadczenia z Internetem
![Federacja z łączności między lokalizacjami](media/active-directory-deploying-ws-ad-guidelines/Federation_xprem.png)
**na rysunku 2**

#### <a name="description"></a>Opis
Aplikacji obsługującej oświadczenia, który został pomyślnie wdrożony lokalnymi i używane przez użytkowników firmowej musi być dostępne bezpośrednio z Internetu. Aplikacja stanowi frontonu sieci web do usługi SQL database, w którym są przechowywane dane. Serwery SQL używane przez aplikację znajdują się również w sieci firmowej. Dwa systemu Windows Server AD FS STSs i równoważenia obciążenia, zostały wdrożone lokalnymi zapewnienie dostępu do firmowych użytkowników. Teraz aplikacja musi ponadto dostęp do bezpośrednio w Internecie zarówno przez partnerów biznesowych za pomocą firmowej tożsamości i istniejący użytkownicy firmowej.

W celu uproszczenia i potrzeb wdrażania i konfigurowania tego nowe wymagania, zostanie podjęta decyzja dwa dodatkowe web frontends i dwa serwery proxy w systemie Windows Server AD FS można zainstalować na maszynach wirtualnych platformy Azure. Wszystkie cztery maszyny wirtualne mają być widoczne bezpośrednio do Internetu i zapewnia łączność z siecią lokalną przy użyciu sieci VPN lokacja lokacja sieci wirtualnej platformy Azure.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Zagadnienia dotyczące scenariusza i jak obszarów technologii dotyczą scenariusza
* [Topologia sieci](#BKMK_NetworkTopology): tworzenie sieci wirtualnej platformy Azure i [skonfigurowania łączności między lokalizacjami](../vpn-gateway/vpn-gateway-site-to-site-create.md).
  
  > [!NOTE]
  > Dla każdej wersji systemu Windows Server AD FS certyfikaty upewnij się, że adres URL zdefiniowany w szablonie certyfikatu i certyfikatów wynikowych może być osiągnięta poprzez wystąpień FS systemu Windows Server AD działających na platformie Azure. Może to wymagać łączności między lokalizacjami części infrastruktury PKI. Na przykład jeśli punkt końcowy listy CRL jest oparta na protokole LDAP i obsługiwane wyłącznie w infrastrukturze lokalnej, następnie między lokalizacjami łączności będą wymagane. Nie jest to pożądane, może być konieczne użycie certyfikatów wystawianych przez urząd certyfikacji, którego lista CRL jest dostępny za pośrednictwem Internetu.
  > 
  > 
* [Konfiguracji usługi w chmurze](#BKMK_CloudSvcConfig): Upewnij się, masz dwie usługi w chmurze w kolejności dwie wirtualne adresy IP z równoważeniem obciążenia. Wirtualny adres IP pierwszej usługi chmury, zostanie przekierowany do dwóch systemu Windows Server AD FS proxy maszyn wirtualnych na porty 80 i 443. Serwera systemu Windows Server AD FS proxy maszyn wirtualnych zostanie skonfigurowany tak, aby wskazywał adres IP usługi równoważenia obciążenia lokalnej tego Zeskanowano STSs FS AD systemu Windows Server. Drugi usługi chmury wirtualnego adresu IP, zostanie przekierowany do dwóch maszyn wirtualnych, ponownie uruchom frontonu sieci web na porty 80 i 443. Skonfiguruj niestandardowe sondowania, aby upewnić się, że moduł równoważenia obciążenia tylko kieruje ruch do funkcjonowania systemu Windows Server AD FS serwera proxy i sieci web frontonu maszyn wirtualnych.
* [Konfiguracja serwera federacyjnego](#BKMK_FedSrvConfig): Konfigurowanie systemu Windows Server AD FS jako serwer federacyjny (STS) do generowania tokenów zabezpieczających w lesie usługi Active Directory systemu Windows Server, utworzone w chmurze. Federacyjnej relacji zaufania dostawcy oświadczeń z różnych partnerów, które chcesz akceptować tożsamości z i konfigurowanie jednostki uzależnionej relacji zaufania jednostek z różnych aplikacji, które chcesz wygenerować tokenów.
  
    W większości przypadków systemu Windows Server AD FS, serwery proxy są wdrażane w charakterze internetowy ze względów bezpieczeństwa podczas ich odpowiedniki systemu Windows Server AD FS federation pozostać odizolowane od bezpośrednie połączenie z Internetem. Niezależnie od scenariusza wdrożenia należy skonfigurować usługi w chmurze z wirtualnym adresem IP, które będzie dostarczać publicznie dostępnego adresu IP i port, który jest w stanie Równoważenie obciążenia w sieci dwa wystąpienia usługi STS FS systemu Windows Server AD albo wystąpień serwera proxy.
* [Konfiguracja wysokiej dostępności systemu Windows Server AD FS](#BKMK_ADFSHighAvail): zaleca się wdrożyć farmę programu Windows Server AD FS przy użyciu co najmniej dwóch serwerów dla trybu failover i równoważenia obciążenia. Może rozważyć użycie Windows wewnętrznej bazy danych (WID) dla danych konfiguracji usług AD FS w systemie Windows Server, a następnie użyć funkcji równoważenia obciążenia wewnętrznego systemu Azure w celu dystrybucji żądań przychodzących na serwerach w farmie.

Aby uzyskać więcej informacji, zobacz [przewodniku wdrażania usług AD DS](https://technet.microsoft.com/library/cc753963).

### <a name="BKMK_HybridExt"></a>3. Usług AD DS: Windows Server AD DS obsługujący aplikację, która wymaga łączności z siecią firmową wdrażania
![Wdrażanie usług AD DS między lokalizacjami](media/active-directory-deploying-ws-ad-guidelines/ADDS_xprem.png)
**rysunek 3**

#### <a name="description"></a>Opis
Aplikacja obsługująca LDAP jest wdrożony na maszynie wirtualnej platformy Azure. Obsługuje uwierzytelnianie zintegrowane systemu Windows, a używa systemu Windows Server AD DS jako repozytorium dla danych profilu konfiguracji i użytkownika. Celem jest dla aplikacji, aby korzystać z istniejących firmowych systemu Windows Server AD DS i zapewnienia logowania jednokrotnego. Aplikacja nie jest obsługujący oświadczenia. Użytkownicy muszą uzyskać dostęp do aplikacji bezpośrednio z Internetu. Aby zoptymalizować wydajność i koszty, zdecydowano, że dwa kontrolery domeny dodatkowe, które są częścią domeny firmy można wdrożyć obok aplikacji na platformie Azure.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Zagadnienia dotyczące scenariusza i jak obszarów technologii dotyczą scenariusza
* [Topologia sieci](#BKMK_NetworkTopology): tworzenie sieci wirtualnej platformy Azure z [łączności między lokalizacjami](../vpn-gateway/vpn-gateway-site-to-site-create.md).
* [Metoda instalacji](#BKMK_InstallMethod): wdrażania replik kontrolerów domeny, z firmowej domeny usługi Active Directory systemu Windows Server. Dla repliki kontrolera domeny można zainstalować usług systemu Windows Server AD DS na maszynie Wirtualnej i opcjonalnie Użyj funkcji instalacji z nośnika (IFM), aby zmniejszyć ilość danych, który musi zostać zreplikowane na nowy kontroler domeny podczas instalacji. Samouczek, zobacz [Instalowanie repliki kontrolera domeny usługi Active Directory na platformie Azure](active-directory-install-replica-active-directory-domain-controller.md). Nawet jeśli używasz IFM może być bardziej efektywne tworzenie wirtualnego kontrolera domeny lokalnej i przenieść cały wirtualny dysk twardy (VHD) w chmurze zamiast replikować podczas instalacji systemu Windows Server AD DS. Dla bezpieczeństwa zaleca się usunąć wirtualny dysk twardy z sieci lokalnej po został skopiowany do platformy Azure.
* [Topologia lokacji usługi Active Directory systemu Windows Server](#BKMK_ADSiteTopology): Utwórz nową witrynę Azure Lokacje usługi Active Directory i usług. Utwórz obiekt podsieci usługi Active Directory systemu Windows Server do reprezentowania sieci wirtualnej platformy Azure i dodać podsieci do lokacji. Utwórz nowy link lokacji obejmującej nową witrynę Azure i lokacji, w której sieć wirtualna platformy Azure punktu końcowego sieci VPN znajduje się w celu kontrolowania i zoptymalizować Windows Server Active Directory ruch do i z usługi Azure.
* [Adresowanie IP i DNS](#BKMK_IPAddressDNS):
  
  * Ustawianie statycznego adresu IP dla kontrolera domeny za pomocą polecenia cmdlet Set-AzureStaticVNetIP Azure PowerShell.
  * Instalowanie i konfigurowanie systemu Windows serwera DNS na kontrolerach domeny na platformie Azure.
  * Skonfiguruj właściwości sieci wirtualnej o nazwie i adresie IP maszyny wirtualnej, który jest hostem ról serwera DNS i kontrolera domeny.
* [Rozproszona geograficznie kontrolery domeny](#BKMK_DistributedDCs): odpowiednio skonfigurować dodatkowe sieci wirtualnych. Jeśli topologii lokacji usługi Active Directory wymaga kontrolerów domeny w lokalizacji geograficznych odpowiadające różnych regionach platformy Azure, niż chcesz utworzyć odpowiednio Lokacje usługi Active Directory.
* [Kontrolery domeny tylko do odczytu](#BKMK_RODC): można wdrożyć tylko do odczytu w witrynie platformy Azure, w zależności od wymagań do wykonywania zapisu operacji względem kontrolera domeny i zgodności aplikacji i usług w witrynie z kontrolera RODC. Aby uzyskać więcej informacji na temat zgodności aplikacji, zobacz [Podręcznik zgodności aplikacji kontrolerów domeny tylko do odczytu](https://technet.microsoft.com/library/cc755190).
* [Wykaz globalny](#BKMK_GC): wykazów globalnych są wymagane do obsługi żądań logowania w wielu domenach lasach. Jeśli nie są wdrażane GC w witrynie platformy Azure, będą naliczane koszty ruch wychodzący, ponieważ żądania uwierzytelniania spowodować GC zapytań w innych lokacjach. Aby zminimalizować ruch, można włączyć buforowania członkostwa grup uniwersalnych dla usługi Azure site Lokacje usługi Active Directory i usług.
  
    Jeśli wdrożono wykazu Globalnego, należy skonfigurować łącza lokacji i kosztów linków lokacji tak, aby GC w witrynie Azure nie jest preferowany jako źródłowego kontrolera domeny przez inne wykazów globalnych, które trzeba replikować tej samej partycji domeny częściowej.
* [Rozmieszczenie systemu Windows Server AD DS w bazie danych i folderu SYSVOL](#BKMK_PlaceDB): dodanie dysku danych do kontrolerów domeny z na maszynach wirtualnych Azure w celu przechowywania bazy danych, dzienników i folderu SYSVOL usługi Active Directory systemu Windows Server.
* [Kopia zapasowa i przywracanie](#BKMK_BUR): ustalić, które chcesz przechowywać kopie zapasowe stanu systemu. Jeśli to konieczne, należy dodać inny dysk danych do maszyny Wirtualnej kontrolera domeny do przechowywania kopii zapasowych.

## <a name="deployment-decisions-and-factors"></a>Decyzji dotyczących wdrożenia i czynniki
Ta tabela zawiera podsumowanie obszarów technologii usługi Active Directory systemu Windows Server, które są w pełni funkcjonalne w powyższych scenariuszy i odpowiednie decyzje wziąć pod uwagę zawiera łącza do bardziej szczegółowo poniżej. Niektórych obszarów technologii może nie być stosowane do każdego scenariusza wdrażania i niektórych obszarów technologii może być scenariusza wdrażania ważniejsze niż innych obszarów technologii.

Na przykład jeśli wdrożysz repliki kontrolera domeny w sieci wirtualnej, a las zawiera pojedynczą domenę, wybierając do wdrożenia serwera wykazu globalnego w takim przypadku nie będzie krytyczne dla danego scenariusza wdrażania, ponieważ wszelkie wymagania dodatkowe replikacja nie zostanie utworzony. Z drugiej strony Jeśli las zawiera wiele domen, następnie decyzja o wdrożeniu wykazu globalnego w sieci wirtualnej mogą wpłynąć na przepustowość, wydajność, uwierzytelnianie, wyszukiwanie w katalogu macierzystym i tak dalej.

| Obszar technologii usługi Active Directory systemu Windows Server | Decyzje | Czynniki |
| --- | --- | --- |
| [Topologia sieci](#BKMK_NetworkTopology) |Czy można utworzyć sieć wirtualną? |<li>Wymagania dotyczące dostępu do zasobów firmy</li> <li>Authentication</li> <li>Zarządzanie kontami</li> |
| [Konfiguracja wdrażania kontrolera domeny](#BKMK_DeploymentConfig) |<li>Wdrażanie oddzielnego lasu bez żadnych relacji zaufania?</li> <li>Wdrażanie nowego lasu z Federacją?</li> <li>Wdrażanie nowego lasu z zaufaniem lasu usługi Active Directory systemu Windows Server lub Kerberos?</li> <li>Rozszerz las Corp, wdrażając repliki kontrolera domeny?</li> <li>Rozszerz las Corp przez wdrożenie nowej domeny podrzędnej lub drzewa domen?</li> |<li>Bezpieczeństwo</li> <li>Zgodność</li> <li>Koszty</li> <li>Odporność i odporność na uszkodzenia</li> <li>Zgodność aplikacji</li> |
| [Topologia lokacji usługi Active Directory systemu Windows Server](#BKMK_ADSiteTopology) |Jak można skonfigurować podsieci, lokacji i łączy lokacji z sieci wirtualnej platformy Azure, aby zoptymalizować ruch i ograniczania kosztów? |<li>Definicje podsieci i lokacji</li> <li>Witryny sieci Web łącza i powiadomienie o zmianie</li> <li>Kompresja replikacji</li> |
| [Adresowanie IP i DNS](#BKMK_IPAddressDNS) |Jak skonfigurować adresy IP i rozpoznawanie nazw? |<li>Przypisz statyczny adres IP za pomocą polecenia cmdlet Użyj AzureStaticVNetIP zestawu</li> <li>Instalowanie serwera DNS systemu Windows Server i konfigurowanie właściwości sieci wirtualnej o nazwie i adresie IP maszyny wirtualnej, który jest hostem ról serwera DNS i kontrolera domeny</li> |
| [Rozproszona geograficznie kontrolerów domeny](#BKMK_DistributedDCs) |Jak zostaną zreplikowane do kontrolerów domeny w oddzielnych sieciach wirtualnych? |Jeśli topologii lokacji usługi Active Directory wymaga kontrolerów domeny w lokalizacji geograficznych, które odpowiada różnych regionach platformy Azure, niż chcesz utworzyć odpowiednio Lokacje usługi Active Directory. [Skonfiguruj sieć wirtualną do sieci wirtualnej łączności](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) do replikacji między kontrolerami domeny w różnych sieciach wirtualnych. |
| [Kontrolery domeny tylko do odczytu](#BKMK_RODC) |Użyj kontrolerów domeny tylko do odczytu lub zapisu? |<li>Filtrowanie atrybutów o dużym znaczeniu Biznesowym/dane osobowe</li> <li>Klucze tajne filtru</li> <li>Limit ruchu wychodzącego</li> |
| [Wykaz globalny](#BKMK_GC) |Zainstaluj wykazu globalnego? |<li>Dla jednej domeny lasu należy wszystkie kontrolery domeny wykazów globalnych</li> <li>Dla lasu o wielu domenach wykazów globalnych są wymagane do uwierzytelniania</li> |
| [Metody instalacji](#BKMK_InstallMethod) |Jak zainstalować kontroler domeny na platformie Azure? |Albo: <li>Instalacja usług AD DS za pomocą środowiska Windows PowerShell lub narzędzia Dcpromo</li> <li>Przenieś wirtualny dysk twardy wirtualnego kontrolera domeny lokalnej</li> |
| [Rozmieszczenie systemu Windows Server AD DS w bazie danych i folderu SYSVOL](#BKMK_PlaceDB) |Miejsce przechowywania w systemie Windows Server AD DS bazy danych, dzienników i folderu SYSVOL? |Zmień wartości domyślne Dcpromo.exe. Te krytycznych plików usługi Active Directory *musi* można umieścić na dyskach danych Azure zamiast dysków systemu operacyjnego, które implementuje buforowanie zapisu. |
| [Tworzenie kopii zapasowej i przywracanie](#BKMK_BUR) |Jak chronić i odzyskiwać dane? |Utwórz kopie zapasowe stanu systemu |
| [Konfiguracja serwera federacyjnego](#BKMK_FedSrvConfig) |<li>Wdrażanie nowego lasu z Federacją w chmurze?</li> <li>Wdrażanie usług AD FS lokalnej i ujawnia serwera proxy w chmurze?</li> |<li>Bezpieczeństwo</li> <li>Zgodność</li> <li>Koszty</li> <li>Dostęp do aplikacji przez partnerów biznesowych</li> |
| [Konfiguracji usługi w chmurze](#BKMK_CloudSvcConfig) |Usługi w chmurze niejawnie jest wdrażany przy pierwszym utworzeniu maszyny wirtualnej. Potrzebujesz do wdrożenia usługi w chmurze dodatkowe? |<li>Maszyna wirtualna lub maszyn wirtualnych wymaga narażenia bezpośrednio z Internetem?</li> <li> Usługa wymaga równoważenia obciążenia?</li> |
| [Wymagania dotyczące serwera federacyjnego publicznego i prywatnego adresu IP, adresów (dynamicznego adresu IP i wirtualnego adresu IP)](#BKMK_FedReqVIPDIP) |<li>Czy wystąpienie systemu Windows Server AD FS należy można połączyć się bezpośrednio z Internetu?</li> <li>Aplikacja jest wdrożona w chmurze wymaga własnego internetowy adres i port?</li> |Utwórz jedną usługę chmury dla każdego wirtualnego adresu IP, który jest wymagany przez wdrożenie |
| [Konfiguracja wysokiej dostępności systemu Windows Server AD FS](#BKMK_ADFSHighAvail) |<li>Liczbę węzłów w mojej farmie serwerów systemu Windows Server AD FS?</li> <li>Liczbę węzłów do wdrożenia w farmy programu Windows Server AD FS serwera proxy?</li> |Odporność i odporność na uszkodzenia |

### <a name="BKMK_NetworkTopology"></a>Topologia sieci
W celu spełnienia spójności adresów IP i wymagania systemu DNS dla systemu Windows Server AD DS, należy najpierw utworzyć [sieci wirtualnej platformy Azure](../virtual-network/virtual-networks-overview.md) i Dołącz do niej maszyny wirtualne. Podczas jego tworzenia, należy podjąć decyzję o opcjonalnie rozszerzono łączności z siecią firmową lokalnych, które niewidocznie łączy maszyn wirtualnych platformy Azure z lokalnymi maszynami — to jest osiągane przy użyciu tradycyjnych technologii sieci VPN i wymaga się, że punkt końcowy sieci VPN można narażony na krawędzi sieci firmowej. Oznacza to, że sieć VPN jest inicjowane z platformy Azure do sieci firmowej, a nie na odwrót.

Należy pamiętać, że dodatkowe opłaty podczas rozszerzania sieć wirtualną do sieci lokalnej poza standardowe opłaty, które są stosowane do każdej maszyny Wirtualnej. W szczególności są opłaty za czas procesora CPU bramy sieci wirtualnej platformy Azure i wychodzący ruch generowany przez każdej maszyny Wirtualnej, która komunikuje się z lokalnymi maszynami przez sieci VPN. Aby uzyskać więcej informacji na temat opłat ruchu sieciowego, zobacz [Azure, w skrócie cennik](http://azure.microsoft.com/pricing/).

### <a name="BKMK_DeploymentConfig"></a>Konfiguracja wdrażania kontrolera domeny
Sposób konfiguracji kontrolera domeny, zależy od wymagań usługi się, że ma zostać uruchomione na platformie Azure. Na przykład mogą wdrożyć nowy las, odizolowanych od własnych las firmowy, testowania, weryfikacja koncepcji, nowych aplikacji lub inny projekt krótkoterminowa wymagającego usług katalogowych, ale nie dotyczą dostęp do wewnętrznych zasobów firmy.

Jako korzyści izolowanego lasu, który kontroler domeny nie jest replikowany z lokalnymi kontrolerów domeny, co powoduje mniej wychodzącego ruchu sieciowego generowany przez system, bezpośrednio zmniejszenie kosztów. Aby uzyskać więcej informacji na temat opłat ruchu sieciowego, zobacz [Azure, w skrócie cennik](http://azure.microsoft.com/pricing/).

Inny przykład załóżmy, że masz wymagania ochrony prywatności dla usługi, ale usługa zależy od dostęp do wewnętrznego systemu Windows serwer usługi Active Directory. Jeśli do przechowywania danych są dozwolone dla usługi w chmurze, można wdrożyć nową domenę podrzędną lasu wewnętrznego, na platformie Azure. W takim przypadku można wdrożyć kontrolera domeny dla nowej domeny podrzędnej (bez wykazu globalnego, aby ułatwić prywatności adresu). Ten scenariusz, wraz z wdrożenia kontrolera domeny repliki wymaga sieci wirtualnej dla łączności z kontrolerów domeny z lokalnymi.

Jeśli tworzysz nowy las, wybierz, czy ma być używany [relacje zaufania usługi Active Directory](https://technet.microsoft.com/library/cc771397) lub [relacje zaufania federacji](https://technet.microsoft.com/library/dd807036). Równoważenie wymagań ustawieniem zgodności, zabezpieczeń, zgodności, koszt i elastyczność. Na przykład, aby móc korzystać z [uwierzytelnianie selektywne](https://technet.microsoft.com/library/cc755844) warto wdrożyć nowy las na platformie Azure i utworzyć relację zaufania usługi Active Directory systemu Windows Server między lokalnym lesie a lasem chmury. Jeśli aplikacja obsługująca oświadczenia, jednak można wdrożyć relacje zaufania federacji, zamiast relacji zaufania lasów usługi Active Directory. Innym czynnikiem będzie koszt replikacji więcej danych dzięki rozszerzeniu lokalnej systemu Windows serwer usługi Active Directory do chmury lub generować bardziej wychodzący ruch wyniku uwierzytelniania i obciążenia zapytania.

Wymagania dotyczące dostępności i odporności na uszkodzenia wpłynąć na wybór. Na przykład jeśli łącze zostało przerwane, aplikacje, wykorzystując zaufania Kerberos lub Federacji zaufania, wszystkie prawdopodobnie całkowicie nie działają, chyba że wdrożono wystarczające infrastruktury na platformie Azure. Konfiguracje wdrożenia alternatywne, takie jak replik kontrolerów domeny (zapisywalny lub kontrolery RODC) zwiększa prawdopodobieństwo o możliwość tolerowanie awarii łącza.

### <a name="BKMK_ADSiteTopology"></a>Topologia lokacji usługi Active Directory systemu Windows Server
Należy poprawnie określić lokacji i łączy lokacji, aby zoptymalizować ruch i ograniczania kosztów. Lokacje, linki lokacji i podsieci wpływa na topologii replikacji między kontrolerów domeny i przepływu ruchu uwierzytelniania. Należy wziąć pod uwagę następujące opłaty ruchu, a następnie wdrożyć i skonfigurować kontrolery domeny, zgodnie z wymogami danego scenariusza wdrażania:

* Brak nominalnego opłaty za godzinę dla bramy:
  
  * Można uruchomić i zatrzymać zgodnie z własnymi potrzebami
  * Jeśli zatrzymana, maszynach wirtualnych platformy Azure są odizolowane od sieci firmowej
* Ruch przychodzący jest bezpłatna
* Ruch wychodzący jest rozliczana, zgodnie z [Azure, w skrócie cennik](http://azure.microsoft.com/pricing/). Aby zoptymalizować właściwości link lokacji między lokacjami lokalnymi i lokacje chmury w następujący sposób:
  
  * Jeśli używasz wielu sieci wirtualnych, linki lokacji i kosztów odpowiednio skonfigurować zapobiegające systemu Windows Server AD DS z priorytetów usługi Azure site na taki, który może zapewniać taki sam poziom usługi bez dodatkowych opłat. Można także rozważyć wyłączenie Mostek wszystkie lokacji (BASL) link — opcja (która jest domyślnie włączona). Daje to pewność, że tylko bezpośrednio połączone lokacje replikują między sobą. Kontrolery domeny w sposób przechodni podłączonej lokacji nie są już mógł zreplikować bezpośrednio ze sobą, ale musi zostać zreplikowana za pośrednictwem wspólnego lokacji lub lokacji. Jeśli pośredniczące witryn stać się niedostępne z jakiegoś powodu nie odbywa się replikacja między kontrolerów domeny w sposób przechodni podłączonej lokacji nawet jeśli dostępny jest łączność między lokacjami. Ponadto w przypadku, gdy sekcje zachowania w ramach replikacji przechodniej pozostają pożądane, Utwórz witrynę mostków, zawierających odpowiednie łącza lokacji i lokacji, takie jak lokalnie, witryn sieci firmowej.
  * [Konfigurowanie kosztów linków lokacji](https://technet.microsoft.com/library/cc794882) odpowiednio w celu uniknięcia niezamierzone ruchu. Na przykład jeśli **spróbuj dalej najbliższej lokacji** jest włączona, upewnij się, że lokacje sieci wirtualnej nie są następne najbliższą zwiększając koszt związany z obiektu łącza lokacji, które łączy tę lokację Azure do sieci firmowej.
  * Skonfiguruj lokacja [interwałów](https://technet.microsoft.com/library/cc794878) i [harmonogramy](https://technet.microsoft.com/library/cc816906) zależnie od wymagań spójności i szybkość zmian obiektu. Wyrównuje harmonogram replikacji z tolerancja opóźnienia. Kontrolery domeny są replikowane tylko ostatni stan wartości, tak Zmniejszanie interwału replikacji zmniejszyć koszty, jeśli jest wystarczające obiektu zmienić częstotliwość.
* W przypadku minimalizowania kosztów priorytet, upewnij się, replikacja jest zaplanowana i powiadomienia o zmianie nie jest włączona. Jest to konfiguracja domyślna podczas replikacji między lokacjami. Nie jest to istotne, jeśli są wdrażania kontrolera RODC w sieci wirtualnej, ponieważ kontroler RODC nie będą replikowane na wszystkie zmiany ruchu wychodzącego. Jednak w przypadku wdrożenia zapisywalnego kontrolera domeny, należy upewnij się, że lokacja nie jest skonfigurowana do replikowania aktualizacji z częstotliwością niepotrzebne. W przypadku wdrożenia serwera wykazu globalnego (GC), upewnij się, że każdej lokacji, który zawiera wykaz Globalny replikuje partycji domeny ze źródłowego kontrolera domeny w lokacji, która jest połączona z łącza lokacji lub łącza lokacji, które mają niższe koszty niż GC w witrynie platformy Azure.
* Istnieje możliwość nadal jeszcze bardziej ograniczyć ruch sieciowy generowany przez replikację między lokacjami, zmieniając algorytmu kompresji replikacji. Algorytm kompresji jest kontrolowana przez algorytm kompresji HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Replicator wpis rejestru REG_DWORD. Wartość domyślna to 3, które są powiązane z algorytmu Xpress kompresji. Wartość można zmienić na 2, który zmienia algorytm MSZip. W większości przypadków będzie to zwiększenie kompresji, ale robi to kosztem wykorzystanie procesora CPU. Aby uzyskać więcej informacji, zobacz [działa topologia replikacji jak Active Directory](https://technet.microsoft.com/library/cc755994).

### <a name="BKMK_IPAddressDNS"></a>Adresowanie IP i DNS
Maszyny wirtualne platformy Azure są przydzielane "dzierżawy DHCP adresy" domyślnie. Ponieważ dynamicznych adresów sieci wirtualnej platformy Azure będą się powtarzać z maszyną wirtualną przez czas ich istnienia maszyny wirtualnej, są spełnione wymagania systemu Windows Server AD DS.

W związku z tym użycie adresu dynamicznego na platformie Azure, możesz obowiązują przy użyciu statycznego adresu IP, ponieważ jest obsługą routingu w okresie dzierżawy, a okres dzierżawy jest równy okres istnienia usługi w chmurze.

Jednak dynamicznego adresu jest alokację, jeśli maszyna wirtualna jest zamknięta. Aby zapobiec cofana adres IP, możesz [Przypisz statyczny adres IP za pomocą zestawu AzureStaticVNetIP](http://social.technet.microsoft.com/wiki/contents/articles/23447.how-to-assign-a-private-static-ip-to-an-azure-vm.aspx).

Rozpoznawanie nazw, wdrażać własne (lub Wykorzystaj istniejące) infrastrukturze serwera DNS; DNS platformy Azure nie odpowiada potrzebom rozpoznawania nazwy zaawansowanych usług systemu Windows Server AD DS. Na przykład go nie obsługuje dynamicznych rekordów SRV i tak dalej. Rozpoznawanie nazw jest element konfiguracji krytyczne dla kontrolerów domeny oraz klientów przyłączonych do domeny. Kontrolery domeny musi istnieć możliwość rejestrowania rekordów zasobów i rozwiązywania rekordów zasobów innego kontrolera domeny.
Ze względów odporność na uszkodzenia i wydajności jest optymalne, aby zainstalować usługę DNS z systemem Windows Server na kontrolerów domeny działających na platformie Azure. Następnie można skonfigurować właściwości sieci wirtualnej platformy Azure z nazwy i adresu IP serwera DNS. Po uruchomieniu innych maszyn wirtualnych w sieci wirtualnej, ustawień programu rozpoznawania nazw DNS klienta zostaną skonfigurowane serwer DNS jako część dynamiczne przydzielanie adresów IP.

> [!NOTE]
> Komputerów lokalnych nie można przyłączyć do domeny usługi Active Directory systemu Windows Server AD DS, która jest hostowana na platformie Azure bezpośrednio przez Internet. Wymagania dotyczące portów usługi Active Directory i operacji przyłączania do domeny renderować ją ze względu bezpośrednio ujawnia niezbędne porty, a w rezultacie cały kontroler domeny z Internetem.
> 
> 

Maszyny wirtualne zarejestrować nazwy DNS automatycznie podczas uruchamiania lub zmiany nazwy.

Aby uzyskać więcej informacji na temat w tym przykładzie i inny przykład pokazujący sposób udostępnić pierwsza maszyna wirtualna i zainstalowania usług AD DS na nim, zobacz [zainstalować nowy las usługi Active Directory w systemie Microsoft Azure](active-directory-new-forest-virtual-machine.md). Aby uzyskać więcej informacji o korzystaniu z programu Windows PowerShell, zobacz [instalacji programu Azure PowerShell](/powershell/azureps-cmdlets-docs) i [poleceń cmdlet do zarządzania Azure](/powershell/module/azurerm.compute/#virtual_machines).

### <a name="BKMK_DistributedDCs"></a>Rozproszona geograficznie kontrolerów domeny
Azure zalety odnośnie do hostowania wielu kontrolerów domeny w różnych sieciach wirtualnych:

* Obejmujący wiele lokacji odporności na uszkodzenia
* Fizycznej bliskości oddziałach (mniejsze opóźnienia)

Aby uzyskać informacje o konfigurowaniu bezpośrednia komunikacja między sieciami wirtualnymi, zobacz [Konfigurowanie sieci wirtualnej do sieci wirtualnej łączności](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

### <a name="BKMK_RODC"></a>Kontrolery domeny tylko do odczytu
Należy wybrać, czy do wdrożenia kontrolerów domeny tylko do odczytu lub zapisu. Użytkownik może znajdować się w pozycji do wdrożenia kontrolerów RODC, ponieważ nie ma fizyczną kontrolę nad nimi, ale kontrolery RODC są przeznaczone do wdrożenia w lokalizacjach, gdzie ich zabezpieczeń fizycznych jest zagrożone, takich jak biur oddziałów.

Azure nie stanowi zagrożenia dla bezpieczeństwa fizycznego oddziału firmy, ale kontrolery RODC nadal mogą okazać się bardziej ekonomiczne, ponieważ funkcje, które zapewniają są odpowiednie do tych środowisk, choć bardzo różnych powodów. Na przykład kontrolery RODC ma nie replikację wychodzącą i można selektywnie wypełnić klucze tajne (hasła). Wadą interfejsu Brak tych kluczy tajnych może wymagać ruch wychodzący na żądanie do zweryfikowania ich jako użytkownik lub komputer uwierzytelnia. Ale kluczy tajnych można selektywnie wstępnie i pamięci podręcznej.

Kontrolery RODC zapewniają dodatkowe korzyści i w jego pobliżu problemów o dużym znaczeniu Biznesowym, a dane osobowe, ponieważ można dodać zestawu atrybutów (FAS) filtrowane atrybutów, które zawierają dane poufne na kontrolerze RODC. FAS to dostosowywalne zestaw atrybutów, które nie są replikowane do kontrolera RODC. W przypadku, gdy nie są dozwolone lub nie chcesz przechowywać dane osobowe lub o dużym znaczeniu Biznesowym na platformie Azure, można użyć FAS ze względów bezpieczeństwa. Aby uzyskać więcej informacji, zobacz [kontrolera RODC filtrowane ustawiony atrybut [(https://technet.microsoft.com/library/cc753459)].

Upewnij się, że aplikacje będą zgodne z kontrolera RODC ma być używany. Wiele aplikacji z obsługą usługi Active Directory systemu Windows Server działają prawidłowo w przypadku kontrolera RODC, ale niektóre aplikacje można wykonać Niewydajne lub się nie powieść, jeśli nie mają dostępu do zapisywalnego kontrolera domeny. Aby uzyskać więcej informacji, zobacz [Podręcznik zgodności aplikacji kontrolerów domeny tylko do odczytu](https://technet.microsoft.com/library/cc755190).

### <a name="BKMK_GC"></a>Wykaz globalny
Należy wybrać, czy zainstalować wykazu globalnego (GC). W lesie pojedynczej domeny wszystkie kontrolery domeny należy skonfigurować jako serwerów wykazu globalnego. Nie zwiększa kosztów ponieważ nie będzie żadnych dodatkowych replikacją.

W przypadku lasu o wielu domenach wykazów globalnych są niezbędne do rozwiń członkostwa grup uniwersalnych w procesie uwierzytelniania. Jeśli wykaz Globalny nie należy wdrażać, obciążenia w sieci wirtualnej, które uwierzytelniania kontrolera domeny na platformie Azure pośrednio wygeneruje ruchu uwierzytelniania połączeń wychodzących do badania GC lokalnymi podczas każdej próby uwierzytelnienia.

Koszty związane z wykazów globalnych są mniej przewidywalne, ponieważ one hostów w każdej domenie (w ramach). Jeśli obciążenie obsługuje usługę internetowy i uwierzytelnia użytkowników w systemie Windows Server AD DS, kosztów można całkowicie nieprzewidywalne. Aby pomóc w zmniejszeniu zapytania GC poza chmury lokacji podczas uwierzytelniania, można [Włącz buforowanie członkostwa grup uniwersalnych](https://technet.microsoft.com/library/cc816928).

### <a name="BKMK_InstallMethod"></a>Metody instalacji
Musisz wybrać sposób instalowania kontrolerów domeny w sieci wirtualnej:

* Podwyższ poziom nowych kontrolerów domeny. Aby uzyskać więcej informacji, zobacz [zainstalować nowy las usługi Active Directory w sieci wirtualnej platformy Azure](active-directory-new-forest-virtual-machine.md).
* Przenieś dysk VHD wirtualnego kontrolera domeny lokalnych do chmury. W takim przypadku Pamiętaj, że lokalnego wirtualnego kontrolera domeny "przeniesienia" nie "skopiowanych" lub "Sklonowany".

Użyj tylko Azure maszyn wirtualnych kontrolerów domeny (w przeciwieństwie do platformy Azure "web" lub "pracownik" Rola maszyn wirtualnych). Są trwałe i trwałość stanu na Kontrolerze domeny jest wymagany. Maszyny wirtualne platformy Azure są przeznaczone dla obciążeń, takich jak kontrolerów domeny.

Nie należy używać narzędzia SYSPREP do wdrożenia lub w klonowania kontrolerów domeny. Klonowanie kontrolerów domeny jest dostępna tylko w systemach w systemie Windows Server 2012. Funkcja klonowania wymaga obsługi VMGenerationID w źródłowej funkcji hypervisor. Funkcji Hyper-V w systemie Windows Server 2012 i Azure sieciach wirtualnych zarówno VMGenerationID, podobnie jak obsługują dostawców oprogramowania wirtualizacji innej firmy.

### <a name="BKMK_PlaceDB"></a>Rozmieszczenie systemu Windows Server AD DS w bazie danych i folderu SYSVOL
Wybierz gdzie umieścić bazę danych systemu Windows Server AD DS, dzienników i folderu SYSVOL. Muszą one wdrożone na dyskach danych Azure.

> [!NOTE]
> Dyski danych Azure są ograniczone do 4 TB.
> 
> 

Domyślnie stacji dysków danych wykonać nie pamięci podręcznej zapisu. Stacjach dysków dołączonych do maszyny Wirtualnej użyj zapisu do buforowania. Zapisem sprawia, że czy buforowanie zapisu dokłada starań, aby trwałego magazynu Azure, przed wykonaniem transakcji z punktu widzenia systemu operacyjnego maszyny Wirtualnej. Zapewnia trwałość kosztem wolniejsze zapisów.

Jest to ważne w przypadku systemu Windows Server AD DS, ponieważ dysk buforowanie zapisu unieważnia założeniom przez kontroler domeny. Windows Server AD DS próbuje Wyłącz buforowanie zapisu, ale jest maksymalnie dysku systemu We/Wy przestrzegać go. Nie można wyłączyć buforowanie zapisu w pewnych okolicznościach może powodować wycofania numeru USN, co powoduje pokutujące obiekty i inne problemy.

Jako najlepsze rozwiązanie dla wirtualnych kontrolerów domeny wykonaj następujące czynności:

* Ustawienia hosta preferencji pamięci podręcznej na dysk danych platformy Azure dla ŻADNEGO. Zapobiega to problemy z buforowania zapisu dla operacji usług AD DS.
* Przechowywanie bazy danych, dzienników i folderu SYSVOL w obu tym samym dysku danych lub osobne dyski z danymi. Zazwyczaj jest to innym dysku niż dysku z systemem operacyjnym. Takeaway klucza jest, że baza danych systemu Windows Server AD DS i folderu SYSVOL nie mogą być składowane typu dysku systemu operacyjnego Azure. Domyślnie proces instalacji usług AD DS instaluje te składniki w folderze % systemroot %, co nie jest zalecane dla platformy Azure.

### <a name="BKMK_BUR"></a>Kopia zapasowa i przywracanie
Należy pamiętać o co to jest i nie jest obsługiwana dla kopii zapasowej i przywracania kontrolera domeny na ogólnie rzecz biorąc, a w szczególności, systemami na maszynie wirtualnej. Zobacz [kopia zapasowa i przywracanie zagadnienia dotyczące zwirtualizowanych kontrolerów domeny](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv#backup_and_restore_considerations_for_virtualized_domain_controllers).

Utwórz kopie zapasowe stanu systemu, używając tylko oprogramowania kopii zapasowej, w szczególności rozpoznający kopii zapasowej wymagania dotyczące systemu Windows Server AD DS, takie jak Kopia zapasowa systemu Windows Server.

Nie Kopiuj i klonowanie zamiast regularnego wykonywania kopii zapasowych plików VHD kontrolerów domeny. Przywracanie kiedykolwiek należy wymagać, grozi to przy użyciu sklonowane lub skopiowane wirtualne dyski twarde bez systemu Windows Server 2012 i obsługiwanej funkcji hypervisor wprowadzi rozbieżnościach numerów USN.

### <a name="BKMK_FedSrvConfig"></a>Konfiguracja serwera federacyjnego
Konfiguracja serwerów federacyjnych systemu Windows Server AD FS (STSs) zależy od częściowo czy aplikacji, które mają zostać wdrożone na platformie Azure muszą uzyskać dostęp do zasobów w sieci lokalnej.

Czy aplikacje spełniają następujące kryteria, można wdrożyć aplikacji w izolacji od sieci lokalnej.

* Akceptacji tokenów SAML
* Są one exposable z Internetem
* Nie uzyskiwania dostępu do zasobów lokalnych

W takim przypadku należy skonfigurować systemu Windows Server AD FS STSs następująco:

1. Konfigurowanie izolowanych lesie na platformie Azure.
2. Zapewnienie dostępu federacyjnego do lasu, konfigurując farmy serwerów federacyjnych usług AD FS w systemie Windows Server.
3. Konfigurowanie systemu Windows Server AD FS (farmy serwerów federacyjnych i federacyjnego serwera proxy farmy) w lesie lokalnym.
4. Należy ustanowić relację zaufania federacji między lokalną i Azure wystąpień usług AD FS w systemie Windows Server.

Z drugiej strony jeśli aplikacje wymagają dostępu do zasobów lokalnych, użytkownik może skonfigurować usług AD FS w systemie Windows Server z aplikacji na platformie Azure w następujący sposób:

1. Skonfiguruj połączenie między lokalnymi sieciami i platformą Azure.
2. Konfigurowanie farmy serwerów federacyjnych systemu Windows Server AD FS w lokalnym lesie.
3. Skonfiguruj serwer proxy farmy serwerów federacyjnych usług AD FS w systemie Windows Server na platformie Azure.

Ta konfiguracja daje możliwość ograniczenia narażenia zasobów lokalnych, podobne do konfigurowania systemu Windows Server AD FS z aplikacji w sieci obwodowej.

Należy pamiętać, że w każdym z tych scenariuszy można ustanowić relacji zaufania z więcej dostawców tożsamości, jeśli jest potrzebny współpracy business-to-business.

### <a name="BKMK_CloudSvcConfig"></a>Konfiguracji usługi w chmurze
Usługi w chmurze są niezbędne, jeśli chcesz udostępnić maszynie Wirtualnej bezpośrednio do Internetu lub udostępnianie aplikacji z równoważeniem obciążenia internetowy. Jest to możliwe, ponieważ każda usługa w chmurze oferuje pojedynczy można skonfigurować wirtualny adres IP.

### <a name="BKMK_FedReqVIPDIP"></a>Wymagania dotyczące serwera federacyjnego publicznego i prywatnego adresu IP, adresów (dynamicznego adresu IP i wirtualnego adresu IP)
Każda maszyna wirtualna platformy Azure odbiera dynamicznego adresu IP. Dynamiczny adres IP jest prywatny adres dostępny tylko w obrębie platformy Azure. W większości przypadków, będzie konieczne konfigurowanie wirtualnego adresu IP dla wdrożeń usług AD FS w systemie Windows Server. Wirtualny adres IP jest niezbędne do ekspozycji punktów końcowych usługi AD FS w systemie Windows Server z Internetem i będzie używany przez federacyjne partnerów i klientów uwierzytelniania i bieżące zarządzanie. Wirtualny adres IP jest właściwością elementu usługi w chmurze, który zawiera co najmniej jednej maszyny wirtualnej Azure. Jeśli aplikacji obsługującej oświadczenia wdrożony na platformie Azure i Windows Server AD FS są skierowane do Internetu i udziału typowe porty, każda będzie wymagać wirtualnego adresu IP własnych i w związku z tym będzie konieczne utworzenie jedną usługę chmury dla aplikacji i drugi dla systemu Windows Server AD FS.

Definicje terminów wirtualnego adresu IP i dynamicznego adresu IP, zobacz [terminów i definicje](#BKMK_Glossary).

### <a name="BKMK_ADFSHighAvail"></a>Konfiguracja wysokiej dostępności systemu Windows Server AD FS
Mimo że jest możliwy do wdrożenia usług federacyjnych autonomicznej usług AD FS w systemie Windows Server, zaleca się wdrożyć farmę z co najmniej dwa węzły Zabezpieczającego usług AD FS i serwera proxy dla środowisk produkcyjnych.

Zobacz [zagadnienia dotyczące topologii wdrożenia 2.0 usług AD FS](https://technet.microsoft.com/library/gg982489) w [AD przewodnik dotyczący projektowania 2.0 FS](https://technet.microsoft.com/library/dd807036) zdecydować, które najlepsze opcje konfiguracji wdrażania własnych potrzeb.

> [!NOTE]
> Aby uzyskać równoważenia obciążenia dla systemu Windows Server AD FS z punktów końcowych na platformie Azure, skonfiguruj wszystkie elementy członkowskie farmy usług AD FS systemu Windows Server w tej samej usłudze w chmurze i użyj funkcji równoważenia obciążenia Azure dla protokołu HTTP (domyślnie: 80) i porty HTTPS (domyślnie: 443). Aby uzyskać więcej informacji, zobacz [sondę modułu równoważenia obciążenia Azure](https://msdn.microsoft.com/library/azure/jj151530).
> Windows Server (Równoważenia sieciowego) nie jest obsługiwana na platformie Azure.
> 
> 

