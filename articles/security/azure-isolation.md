---
title: Izolacja w chmurze publicznej Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat przetwarzania danych usług w chmurze zawierających szeroką gamę wystąpienia obliczeniowe i usług, które można skalować w górę i w dół automatycznie na potrzeby aplikacji lub przedsiębiorstwa.
services: security
documentationcenter: na
author: UnifyCloud
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 6f01c2938462f3912928e183fcec215a52a3ee48
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
ms.locfileid: "34010884"
---
# <a name="isolation-in-the-azure-public-cloud"></a>Izolacja w chmurze publicznej systemu Azure
##  <a name="introduction"></a>Wprowadzenie
### <a name="overview"></a>Przegląd
Ułatwienie Azure bieżących i przyszłych klientów zrozumieć i korzystanie z różnych funkcji zabezpieczeń dostępnych w i otaczającego platformy Azure, firma Microsoft wprowadziła serii oficjalne dokumenty, omówienie zabezpieczeń, najlepsze rozwiązania i Listy kontrolne.
Tematy zakresu pod względem szerokości i głębokość i są okresowo aktualizowane. Ten dokument jest częścią tej serii, zgodnie z opisem w następującej sekcji abstrakcyjny.

### <a name="azure-platform"></a>Platformy Azure
Azure to platforma usługi chmury Otwórz i elastyczne, która obsługuje najszerszych wybór systemów operacyjnych, programowania języków, struktury, narzędzia, baz danych i urządzeń. Można na przykład:
- Uruchom kontenery Linux z integracją rozwiązania Docker;
- Tworzenie aplikacji za pomocą języka JavaScript, Python, .NET, PHP, Java i Node.js; i
- Kompilacja zapleczy dla systemu iOS, Android i Windows urządzeń.

Microsoft Azure obsługuje te same technologie miliony deweloperów i specjalistów IT już zależne i zaufania.

Podczas tworzenia lub migracji zasobów informatycznych do dostawcy usług w chmurze publicznej, możesz używają możliwości Twojej organizacji do ochrony aplikacji i danych z usług i formanty zapewniają do zarządzania zabezpieczeniami elementów zawartości opartej na chmurze.

Infrastruktura platformy Azure została zaprojektowana kompleksowo, począwszy od obiektu po aplikacje hostujące jednocześnie miliony klientów, i zapewnia wiarygodną podstawę zaspokajania potrzeb firm w zakresie bezpieczeństwa. Ponadto platforma Azure oferuje szeroki zakres konfigurowalnych opcji zabezpieczeń oraz możliwość sterowania nimi, co pozwala dostosować zabezpieczenia w taki sposób, aby spełniały unikatowe wymagania realizowanych wdrożeń. Ten dokument pomaga spełnić te wymagania.

### <a name="abstract"></a>Abstrakcyjny

Microsoft Azure umożliwia uruchamianie aplikacji i maszynach wirtualnych (VM) na bazie infrastruktury fizycznej udostępnionego. Jednym z podstawowym motywacji gospodarczego do uruchamiania aplikacji w środowisku chmury jest możliwość dystrybucji koszt zasobów udostępnionych między wielu klientów. To rozwiązanie dotyczące obsługi wielu dzierżawców zwiększa wydajność przez multipleksowania zasobów między różnymi klientów przy niskich kosztach. Niestety również wprowadza ryzyko udostępniania serwerów fizycznych i innych zasobów infrastruktury, aby uruchomić poufnych aplikacji i maszyn wirtualnych, które mogą należeć do dowolnego i potencjalnie złośliwych użytkowników.

W tym artykule opisano, jak Microsoft Azure zapewnia izolację zarówno złośliwe i złośliwych użytkowników i służy jako przewodnik dla projektowania rozwiązań w chmurze, oferując architektów różnych opcji izolacji. Ten dokument koncentruje się na technologii platformy Azure i opcji zabezpieczeń umożliwiających dostęp klienta i nie jest podejmowana próba SLA adres, ceny modeli i zagadnienia dotyczące praktyki DevOps.

## <a name="tenant-level-isolation"></a>Poziom izolacji dzierżawców
Jeden z podstawowych zalet chmury obliczeniowej to pojęcie udostępnionego, wspólnej infrastruktury przez wielu klientów jednocześnie, co może prowadzić do korzyści skali. To pojęcie jest nazywane obsługi wielu dzierżawców. Microsoft stale pracuje nad upewnić, że architektura wielodostępnej Microsoft chmury Azure obsługuje zabezpieczeń, poufność zachowania poufności, integralności i standardów dostępności.

W przypadku miejsca pracy w chmurze dzierżawę można zdefiniować jako klienta lub organizację, do której należy określone wystąpienie danej usługi w chmurze. Z platformą tożsamości udostępniane przez Microsoft Azure dzierżawa to po prostu dedykowane wystąpienie usługi Azure Active Directory (Azure AD), który organizacja otrzymuje i posiada po zarejestrowaniu się do usługi w chmurze firmy Microsoft.

Każdy katalog usługi Azure AD jest odrębny i oddzielony od innych katalogów usługi Azure AD. Podobnie jak budynek biurowy, który jest zabezpieczonym zasobem przeznaczonym tylko dla Twojej organizacji, katalog Azure AD został zaprojektowany jako zabezpieczony zasób do użytku tylko Twojej organizacji. Architektura usługi Azure AD chroni dane klientów i informacje o tożsamości przez zmieszaniem. Oznacza to, że użytkownicy i administratorzy jednego katalogu usługi Azure AD nie mogą przypadkowo ani złośliwie uzyskać dostępu do danych w innym katalogu.

### <a name="azure-tenancy"></a>Dzierżawy usługi Azure
Dzierżawy Azure (Azure Subscription) odwołuje się do relacji "odbiorcy/billing" oraz unikatową [dzierżawy](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant) w [usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis). Poziom izolacji dzierżawców w Microsoft Azure jest osiągane przy użyciu usługi Azure Active Directory i [opartej na rolach kontroli](https://docs.microsoft.com/azure/role-based-access-control/overview) oferowane przez nią. Każda subskrypcja platformy Azure jest skojarzony z jednego katalogu usługi Azure Active Directory (AD).

Użytkownicy, grupy i aplikacje z katalogu mogą zarządzać zasobami w subskrypcji platformy Azure. Można przypisać te prawa dostępu przy użyciu portalu Azure, narzędzi wiersza polecenia platformy Azure i interfejsów API zarządzania platformy Azure. Dzierżawa usługi Azure AD jest logicznie odizolowane za pomocą granic zabezpieczeń, dzięki czemu klienta można uzyskać dostępu do lub naruszyć wspólnej dzierżaw złośliwe lub przypadkowo. Usługi Azure AD jest uruchamiany na serwerach "od zera" izolowany w segmencie sieci rozdzielone, gdzie filtrowanie pakietów na poziomie hosta i zapory systemu Windows blokować ruchu i niechciane połączenia.

- Dostęp do danych w usłudze Azure AD wymaga uwierzytelnienia użytkownika za pośrednictwem usługi tokenu zabezpieczającego (STS). Informacje dotyczące istnienia, włączona i roli użytkownika jest używany przez system autoryzacji do określenia, czy żądany dostęp do dzierżawy docelowy jest autoryzowany do tego użytkownika w tej sesji.

![Dzierżawy usługi Azure](./media/azure-isolation/azure-isolation-fig1.png)


- Dzierżaw są kontenerami odrębny i nie ma żadnych zależności między tymi.

- Brak dostępu między dzierżawy, chyba że administrator dzierżawy spowoduje przyznanie za pośrednictwem federacyjnego lub inicjowania obsługi administracyjnej kont użytkowników z innych dzierżawców.

- Fizyczny dostęp do serwerów, które obejmują usługi Azure AD i bezpośredni dostęp do usługi Azure AD systemów zaplecza, jest ograniczone.

- Azure AD użytkownicy nie mają dostępu do fizycznego zasoby lub lokalizacje i dlatego nie jest możliwe do obejścia RBAC zasad testy logiczne podać następujące.

Diagnostyka i konserwacyjnych modelu operacyjnego infrastruktury używającego systemu podniesienia uprawnień w czasie jest wymagany i używać. Azure AD Privileged Identity Management (PIM) pojęcia związane z kwalifikujących się uprawnień administratora. [Administratorzy kwalifikujących się](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) powinna być użytkowników, które wymagają uprzywilejowanego dostępu teraz, a następnie, ale nie każdego dnia. Rola jest nieaktywny, dopóki użytkownik będzie potrzebował dostępu, a następnie zakończyć proces aktywacji i stają się aktywne administratora dla wstępnie określoną ilość czasu.

![Usługa Azure AD Privileged Identity Management](./media/azure-isolation/azure-isolation-fig2.png)

Usługa Azure Active Directory obsługuje każdego dzierżawcy w jego własnej kontenerze chronionych, zasady i uprawnienia do oraz w kontenerze wyłącznie własnością i zarządzane przez dzierżawcę.

Pojęcie kontenery dzierżawy jest głęboko ingrained w usłudze katalogowej na wszystkich warstwach, z portali aż do magazynu trwałego.

Nawet wtedy, gdy metadane z wieloma dzierżawcami usługi Azure Active Directory są przechowywane na tym samym dysku fizycznego, nie ma żadnej zależności między kontenery niż zdefiniowana przez usługę katalogową, który z kolei jest definiowane przez administratora dzierżawy.

### <a name="azure-role-based-access-control-rbac"></a>Kontrola dostępu oparta na rolach na platformie Azure (RBAC)
[Azure opartej na rolach kontroli dostępu (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) ułatwia udostępnianie różnych składników dostępnych w ramach subskrypcji platformy Azure, zapewniając precyzyjne zarządzanie dostępem dla platformy Azure. Azure RBAC umożliwia segregowanie opłat w ramach organizacji i przyznać dostęp oparty na użytkowników należy do wykonywania swoich zadań. Zamiast nadanie każdy nieograniczonych uprawnień w subskrypcji platformy Azure lub zasobów, można zezwolić tylko pewne akcje.

Azure RBAC ma trzy podstawowe role, które są stosowane do wszystkich typów zasobów:

- **Właściciel** ma pełny dostęp do wszystkich zasobów łącznie z prawem delegować dostęp do innych użytkowników.

- **Współautor** można tworzyć i zarządzania wszystkimi typami zasobów platformy Azure, ale nie może udzielić dostępu do innych użytkowników.

- **Czytnik** można wyświetlić istniejących zasobów platformy Azure.

![Kontrola dostępu oparta na rolach na platformie Azure](./media/azure-isolation/azure-isolation-fig3.png)

Pozostałe role RBAC na platformie Azure umożliwiają zarządzanie określonych zasobów platformy Azure. Na przykład Rola współautora maszyny wirtualnej zezwala użytkownikowi na tworzenie i zarządzanie maszynami wirtualnymi. Nie daje im dostępu do sieci wirtualnej platformy Azure lub podsieci, która łączy się z maszyny wirtualnej.

[Wbudowane role RBAC](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) listy ról dostępnej na platformie Azure. Określa operacje i zakresem, który każdego wbudowana rola przyznaje użytkownikom. Jeśli szukasz definiować własne role, aby uzyskać większą kontrolę, zobacz sposób tworzenia [niestandardowych ról w Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/custom-roles).

Niektóre inne możliwości usługi Azure Active Directory obejmują:
- Usługi Azure AD umożliwia logowanie Jednokrotne do aplikacji SaaS, niezależnie od tego, gdzie są obsługiwane. Niektóre aplikacje są sfederowane z usługą Azure AD, a inne korzystają z logowania jednokrotnego z użyciem hasła. Aplikacji federacyjnych można również obsługę użytkowników i [archiwizowanie haseł](https://www.techopedia.com/definition/31415/password-vault).

- Dostęp do danych w usłudze [Azure Storage](https://azure.microsoft.com/services/storage/) jest kontrolowany za pośrednictwem uwierzytelniania. Każde konto magazynu ma klucza podstawowego ([klucz konta magazynu](https://docs.microsoft.com/azure/storage/storage-create-storage-account), lub SAK) i klucz tajny pomocniczy (sygnatury dostępu współdzielonego, lub SAS).

- Usługa Azure AD zapewnia jako usługi przy użyciu federacji tożsamości za pomocą [Active Directory Federation Services](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-azure-adfs), synchronizacji i replikacji z lokalnymi katalogami.

- [Usługa Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) jest usługą Multi-Factor authentication, która wymaga od użytkowników sprawdzić logowania przy użyciu aplikacji mobilnej, połączenia telefonicznego lub wiadomości SMS. Mogą być używane z usługą Azure AD aby ułatwić bezpieczną lokalną zasobów z serwera usługi Azure Multi-Factor Authentication, a także z niestandardowych aplikacji i katalogów przy użyciu zestawu SDK.

- [Usługi domenowe Azure AD](https://azure.microsoft.com/services/active-directory-ds/) umożliwia dołączenie maszyn wirtualnych platformy Azure do domeny usługi Active Directory bez wdrażania kontrolerów domeny. Możesz Zaloguj się do tych maszyn wirtualnych za pomocą firmowych poświadczeń usługi Active Directory i administrować przyłączonych do domeny maszyn wirtualnych za pomocą zasad grupy, aby wymusić plany bazowe zabezpieczeń na wszystkich maszyn wirtualnych platformy Azure.

- [Usługa Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) udostępnia usługę zarządzania tożsamości globalnego wysokiej dostępności dla aplikacji dla użytkowników, która może obsłużyć setki milionów tożsamości. Można ją łatwo integrować z platformami mobilnymi i platformami sieci Web. Użytkownicy mogą rejestrować do wszystkich aplikacji za pomocą środowiska można dostosować przy użyciu istniejących kont społecznościowych lub przez tworzenie poświadczeń.

### <a name="isolation-from-microsoft-administrators--data-deletion"></a>Izolacja Administratorzy usługi Microsoft & usunięcia danych z
Microsoft przyjmuje środki silną ochronę danych przed nieodpowiednimi dostępu lub użycia przez osoby nieupoważnione. Te procesy operacyjne i formanty bazują na [warunki dotyczące usług Online](http://aka.ms/Online-Services-Terms), które oferują umownymi kwota zobowiązania, które kontrolują dostęp do danych.

-   Pracownicy firmy Microsoft nie ma domyślnego dostępu do danych w chmurze. Zamiast tego otrzyma dostęp, w obszarze zarządzania nadzoru, tylko wtedy, gdy jest to konieczne. Program access jest dokładnie kontrolowany i rejestrowane i odwoływane, gdy nie jest już potrzebne.

-   Microsoft może zleca innym firmom świadczenie ograniczonych usług w jej imieniu. Podwykonawców firmy Microsoft może uzyskać dostępu do danych klienta tylko do świadczenia usług, dla którego, firma Microsoft wynajmuje je, aby zapewnić i nie wolno im wykorzystywać ich w jakimkolwiek innym celu. Co więcej ich podstawie umowy powiązanych z zachowania poufności informacji naszych klientów.

Usługi biznesowe z inspekcji certyfikaty, takich jak ISO/IEC 27001 są regularnie sprawdzane przez firmę Microsoft i przedsiębiorstwom akredytowanego inspekcji, które wykonaj przykładowe inspekcje celu stwierdzenia, że dostęp tylko do celów biznesowych uzasadnionych. Można zawsze dostęp do danych klienta w dowolnym momencie z dowolnej przyczyny.

Po usunięciu wszystkich danych Microsoft Azure usuwa dane, w tym wszystkie kopie pamięci podręcznej lub kopii zapasowej. W przypadku usług-scope występujących usuwania w ciągu 90 dni, po upływie okresu przechowywania. (W zakresie usług są zdefiniowane w sekcji warunków przetwarzania danych naszych [warunki dotyczące usług Online](http://aka.ms/Online-Services-Terms).)

Jeśli dysk używany do magazynowania wystąpi awaria sprzętu, jest bezpiecznie [usunięte lub zniszczone](https://www.microsoft.com/trustcenter/Privacy/You-own-your-data) przed Microsoft zwraca go do producenta dla zastąpienia lub naprawy. Danych na dysku zostanie zastąpiony, aby upewnić się, że nie można odzyskać dane w jakikolwiek sposób.

## <a name="compute-isolation"></a>Obliczenia bazy danych izolacji
Microsoft Azure oferuje różne przetwarzania danych usług w chmurze zawierających szeroką gamę wystąpienia obliczeniowe i usług, które można skalować w górę i w dół automatycznie na potrzeby aplikacji lub enterprise. Te wystąpienia obliczeniowe i usługi oferują izolacji na różnych poziomach do zabezpieczania danych bez ograniczania elastyczność w konfiguracji tego zapotrzebowania klientów.

### <a name="isolated-virtual-machine-sizes"></a>Izolowane rozmiarów maszyn wirtualnych
Obliczeń platformy Azure oferuje rozmiary maszyny wirtualnej, które są izolowany do typu określonego sprzętu i dedykowanych jednego odbiorcy.  Rozmiary maszyny wirtualnej są najbardziej odpowiednie dla obciążeń, które wymagają w przypadku obciążeń obejmujących elementy, takie jak zgodnością i przepisami dotyczącymi wysoki stopień izolacji od innych klientów.  Klientów można również dodatkowo podzielić zasoby tych izolowanego maszyn wirtualnych przy użyciu [pomocy technicznej platformy Azure dla maszyn wirtualnych zagnieżdżonych](https://azure.microsoft.com/en-us/blog/nested-virtualization-in-azure/).

Przy użyciu izolowanego rozmiar gwarantuje, że maszyna wirtualna będzie uruchomiona tylko jedna w tym wystąpieniu określonego serwera.  Bieżący ofert izolowanego maszyny wirtualnej obejmują:
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_DS15_v2
* Standard_D15_v2

Dowiedz się więcej na temat izolowane rozmiar każdego dostępne [tutaj](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sizes-memory).

### <a name="hyper-v--root-os-isolation-between-root-vm--guest-vms"></a>Funkcji Hyper-V & głównego izolacji systemu operacyjnego między główny maszyn wirtualnych i maszyn wirtualnych gościa
Platforma obliczeniowa platformy Azure jest oparta na maszynie wirtualizacji, co oznacza, że cały kod klienta jest wykonywana na maszynie wirtualnej funkcji Hyper-V. Na każdym węźle Azure (lub punktu końcowego sieci) Brak funkcji Hypervisor, która uruchamia bezpośrednio przez sprzęt i dzieli węzła numer zmiennej o maszynach wirtualnych gościa (VM).


![Funkcji Hyper-V & głównego izolacji systemu operacyjnego między główny maszyn wirtualnych i maszyn wirtualnych gościa](./media/azure-isolation/azure-isolation-fig4.jpg)


Każdy węzeł ma również jeden specjalne głównego maszynę Wirtualną, której działa system operacyjny hosta. Krytyczne granic jest izolację głównej maszyny Wirtualnej z maszyn wirtualnych gościa, a Gość maszyn wirtualnych od siebie, zarządzane przez funkcję hypervisor i katalog główny systemu operacyjnego. Parowanie funkcji hypervisor/root systemu operacyjnego korzysta z firmy Microsoft dekad środowisko zabezpieczeń systemu operacyjnego i uczenie nowsza od firmy Microsoft Hyper-V do zapewnienia silnej izolacji maszyn wirtualnych gościa.

Platforma Azure korzysta ze środowiska zwirtualizowanego. Wystąpienia użytkownika działać jako autonomiczne maszyny wirtualne, które nie mają dostępu do serwera fizycznego hosta i Izolacja jest wymuszana za pomocą procesora fizycznego poziomy uprawnień (pierścień-0/pierścień-3).

Pierścień 0 to najwyższy poziom uprawnień, a pierścień 3 — najniższy. System operacyjny gościa jest uruchamiany w 1 pierścień mniejszym uprzywilejowanych i aplikacje są uruchamiane w najniższych uprawnieniach 3 pierścień. Ta wirtualizacja zasobów fizycznych prowadzi do wyraźnego rozdzielenia systemu operacyjnego gościa i funkcji hypervisor, co zapewnia dodatkową separację zabezpieczeń.

Azure funkcji hypervisor działa jak jądra i przekazuje wszystkie żądania dostępu do sprzętu z maszyn wirtualnych gościa do hosta na potrzeby przetwarzania przy użyciu interfejsu pamięci współużytkowanej, nazywany magistralę maszyny wirtualnej. Uniemożliwia to użytkownikom uzyskiwanie dostępu do nieprzetworzonego odczytu/zapisu/wykonania w systemie i zmniejsza ryzyko związane z udostępnianiem zasobów systemowych.

### <a name="advanced-vm-placement-algorithm--protection-from-side-channel-attacks"></a>Zaawansowane algorytm umieszczania maszyny Wirtualnej i ochronę przed atakami kanału po stronie
Atakiem maszyny Wirtualnej między wymaga wykonania dwóch kroków: umieszczenie kontrolowane atakujący dokonuje maszyny Wirtualnej na tym samym hoście wśród ofiara maszyn wirtualnych i mogą spowodować granica izolacji wykradać informacje poufne ofiara lub spowolnić Chciwość lub vandalism. Microsoft Azure zapewnia ochronę w obu czynności przy użyciu zaawansowanych algorytm umieszczania maszyny Wirtualnej i ochronę przed atakami wszystkie znane po stronie kanał tym zakłócenia sąsiada maszyn wirtualnych.

### <a name="the-azure-fabric-controller"></a>Kontroler sieci szkieletowej Azure
Kontroler sieci szkieletowej Azure jest odpowiedzialny za przydzielanie zasobów infrastruktury do dzierżawy obciążeń i zarządza jednokierunkowe komunikacji między hostem maszyny wirtualnej. Maszyna wirtualna algorytm wprowadzania do kontrolera sieci szkieletowej Azure jest bardzo zaawansowane i prawie niemożliwe do prognozowania na poziomie hosta fizycznego.

![Kontroler sieci szkieletowej Azure](./media/azure-isolation/azure-isolation-fig5.png)

Azure funkcji hypervisor wymusza pamięci i procesu separacji między maszynami wirtualnymi i bezpiecznie kieruje ruchem sieciowym dzierżawcom systemu operacyjnego gościa. Eliminuje to możliwość i po stronie kanał ataku na poziomie maszyny Wirtualnej.

Na platformie Azure, głównej maszyny Wirtualnej jest szczególna: działa ze wzmocnionymi zabezpieczeniami systemu operacyjnego o nazwie głównego systemu operacyjnego obsługującego agenta sieci szkieletowej (FA). FAs są używane do zarządzania agentów gości (GA) z kolei gościu systemów operacyjnych na klienta maszyn wirtualnych. FAs również zarządzać węzłów magazynu.

Kolekcja Azure funkcji hypervisor, główna systemu operacyjnego/FA, a klienta maszyn wirtualnych lub gazu obejmuje węźle obliczeń. FAs są zarządzane przez kontroler sieci szkieletowej (FC), która istnieje poza węzłów obliczeniowych i przestrzeni dyskowej (klastrów obliczeniowych i magazynów są zarządzane przez oddzielne FCs). Jeśli klient aktualizuje plik konfiguracji ich stosowania jest uruchomiona, FC komunikuje się z FA, który następnie skontaktuje się gazu, który powiadomić aplikację o zmianie konfiguracji. W przypadku awarii sprzętu FC zostanie automatycznie znaleźć dostępnego sprzętu i ponownie uruchomić maszyny Wirtualnej istnieje.

![Kontroler sieci szkieletowej Azure](./media/azure-isolation/azure-isolation-fig6.jpg)

Komunikacja z kontrolera sieci szkieletowej do agenta jest jednokierunkowa. Agent implementuje usługi zabezpieczonego protokołem SSL tylko odpowiada na żądania z kontrolera. Nie można go inicjować połączenia do kontrolera lub inne węzły wewnętrzny uprzywilejowanego. FC traktuje wszystkie odpowiedzi, tak jakby były niezaufanych.


![Kontroler sieci szkieletowej](./media/azure-isolation/azure-isolation-fig7.png)

Izolacja rozciąga się od głównego maszyny Wirtualnej z maszyn wirtualnych gościa i maszyny wirtualne gości od siebie nawzajem. Obliczeń węzły również są odizolowane od węzłów magazynu w celu zwiększenia ochrony.


Funkcja hypervisor hosta systemu operacyjnego zapewniają pakietu sieciowego - filtrów w celu zapewnienia, że niezaufane maszyn wirtualnych nie może wygenerować fałszywych ruchu lub odbieranie ruchu kierowanego nie, kierować ruch do punktów końcowych chronionych infrastruktury i wysyłania i odbierania niewłaściwe ruch emisji.


### <a name="additional-rules-configured-by-fabric-controller-agent-to-isolate-vm"></a>Dodatkowe zasady skonfigurowane przez agenta kontrolera sieci szkieletowej do izolowania maszyn wirtualnych
Domyślnie cały ruch jest zablokowany, po utworzeniu maszyny wirtualnej i skonfiguruje filtr pakietów można dodać reguły i wyjątki, aby zezwolić na ruch autoryzowanych agenta kontrolera sieci szkieletowej.

Istnieją dwie kategorie reguł zaprogramowane:

-   **Reguły konfiguracji lub infrastruktury maszyny:** domyślnie cała komunikacja jest zablokowany. Istnieją wyjątki, aby umożliwić maszynę wirtualną do wysyłania i odbierania ruchu DHCP i DNS. Maszyny wirtualne można również wysyłać ruch do Internetu "public" i przesyłają dane do innych maszyn wirtualnych w ramach tej samej sieci wirtualnej platformy Azure i Serwer aktywacji systemu operacyjnego. Maszyny wirtualne dozwolonych lokalizacji docelowych wychodzących nie zawierają Azure routera podsieci, zarządzania platformy Azure i inne właściwości firmy Microsoft.

-   **Plik konfiguracji roli:** definiuje przychodzących list kontroli dostępu (ACL) oparte na modelu usługi dzierżawcy.

### <a name="vlan-isolation"></a>Izolacja sieci VLAN
W każdym klastrze istnieją trzy sieci VLAN:

![Izolacja sieci VLAN](./media/azure-isolation/azure-isolation-fig8.jpg)


-   Główne sieci VLAN — dla połączeń klienta niezaufanych węzłów

-   Sieci VLAN FC — zawiera zaufanych FCs i systemów pomocniczych

-   Urządzenie sieci VLAN — zawiera zaufanych sieci i innych urządzeniach infrastruktury

Komunikacja jest dozwolony z sieci VLAN FC głównego sieci VLAN, ale głównego sieci VLAN nie można zainicjować VLAN FC. Komunikacja również jest zablokowana z głównym sieci VLAN na urządzeniu sieci VLAN. Gwarantuje to, że nawet wtedy, gdy węzeł uruchamiania kodu klienta zostanie naruszony, go nie ataki węzłów na FC lub urządzenie sieci VLAN.

## <a name="storage-isolation"></a>Izolacja magazynu
### <a name="logical-isolation-between-compute-and-storage"></a>Logiczne izolację między zasobów obliczeniowych i magazynu
W ramach podstawowych projekt Microsoft Azure oddziela obliczeń na podstawie maszyny Wirtualnej z magazynu. Ta separacja umożliwia obliczeń i magazynowania można skalować niezależnie, co ułatwia zapewnienie izolacji i obsługi wielu dzierżawców.

W związku z tym usługi Magazyn Azure działa na oddzielnego sprzętu z połączenia sieciowego do rozwiązań usługi obliczenia Azure z wyjątkiem logicznie. [To](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf) oznacza, że podczas tworzenia dysku wirtualnego przydzielenie miejsca na dysku nie dla całego pojemności. Zamiast tego utworzono tabelę, która mapuje adresy na dysk wirtualny miejsca na dysku fizycznym, a ta tabela jest początkowo pusta. **Po raz pierwszy klient zapisuje dane na dysku wirtualnym zajmować miejsca na dysku fizycznym, a wskaźnik do niego znajduje się w tabeli.**
### <a name="isolation-using-storage-access-control"></a>Kontrola dostępu do magazynu przy użyciu izolacji
**Kontrola dostępu w usłudze Azure Storage** ma model kontroli dostępu za pomocą prostego. Każda subskrypcja platformy Azure można utworzyć co najmniej jedno konto magazynu. Każde konto magazynu ma pojedynczy klucz tajny, który służy do kontrolowania dostępu do wszystkich danych na tym koncie magazynu.

![Kontrola dostępu do magazynu przy użyciu izolacji](./media/azure-isolation/azure-isolation-fig9.png)

**Dostęp do danych usługi Azure Storage (łącznie z tabelami)** można sterować za pośrednictwem [SAS (Shared Access Signature)](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) token, który przyznaje zakres dostępu. Sygnatury dostępu Współdzielonego jest tworzony przy użyciu szablonu zapytania (URL), podpisany [SAK (klucz konta magazynu)](https://msdn.microsoft.com/library/azure/ee460785.aspx). Który [podpisany adresu URL](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) można przydzielić do innego procesu (który delegowane), która może, a następnie wypełnij szczegóły zapytania i żądania usługi magazynu. Sygnatury dostępu Współdzielonego można udzielić dostępu na podstawie czasu klientów bez ujawniania klucz tajny konta magazynu.

Sygnatury dostępu Współdzielonego oznacza, że możemy udzielić się, że klient ograniczone uprawnienia do obiektów w nasze konto magazynu w określonym przedziale czasu i z określonym zestawem uprawnień. Możemy udzielić uprawnieniami ograniczonymi według powyższego bez konieczności udostępniania kluczy dostępu konta.

### <a name="ip-level-storage-isolation"></a>IP magazynu poziomu izolacji
Można ustanowić zapory i zdefiniować zakres adresów IP dla zaufanych klientów. Zakres adresów IP tylko w przypadku klientów, którzy mają adres IP ze zdefiniowanego zakresu może się łączyć z [usługi Azure Storage](https://docs.microsoft.com/azure/storage/storage-security-guide).

IP magazynu danych mogą być chronione przed nieautoryzowanymi użytkownikami za pośrednictwem sieci mechanizm, który jest używany do przydzielenia dedykowanej lub dedykowanych tunelu ruchu do magazynu IP.

### <a name="encryption"></a>Szyfrowanie
Platforma Azure oferuje następujące typy szyfrowania do ochrony danych:
-   Szyfrowanie podczas przesyłania

-   Szyfrowanie w spoczynku

#### <a name="encryption-in-transit"></a>Szyfrowanie podczas przesyłania
Szyfrowanie podczas przesyłania jest mechanizm ochrony danych podczas ich przesyłania w sieciach. Z usługą Azure Storage można zabezpieczyć dane przy użyciu:

-   [Szyfrowanie na poziomie transportu](https://docs.microsoft.com/azure/storage/storage-security-guide#encryption-in-transit), taki jak HTTPS podczas transferu danych do i z usługi Azure Storage.

-   [Połączenie szyfrowania](../storage/common/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares), takimi jak szyfrowanie protokołu SMB 3.0 udziałów plików Azure.

-   [Szyfrowanie po stronie klienta](https://docs.microsoft.com/azure/storage/storage-security-guide#using-client-side-encryption-to-secure-data-that-you-send-to-storage), aby szyfrować dane, zanim zostanie przekazany do magazynu, jak i do odszyfrowania danych po przeniesieniu poza magazynu.

#### <a name="encryption-at-rest"></a>Szyfrowanie magazynowanych
W przypadku wielu organizacji [szyfrowanie danych magazynowanych](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) jest obowiązkowy krok na drodze danych suwerenności prywatności, zgodności i danych. Istnieją trzy funkcje platformy Azure, które zapewniają szyfrowania danych, która jest "w stanie spoczynku":

-   [Szyfrowanie usługi Magazyn](https://docs.microsoft.com/azure/storage/storage-security-guide#encryption-at-rest) umożliwia zażądanie, czy Usługa magazynu automatyczne szyfrowanie danych podczas zapisywania go do magazynu Azure.

-   [Szyfrowanie po stronie klienta](https://docs.microsoft.com/azure/storage/storage-security-guide#client-side-encryption) oferuje także funkcję szyfrowania w stanie spoczynku.

-   [Szyfrowanie dysków Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) umożliwia szyfrowanie dysków systemu operacyjnego i dysków danych używanych przez maszyny wirtualne IaaS.

#### <a name="azure-disk-encryption"></a>Usługa Azure Disk Encryption
[Szyfrowanie dysków Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) na maszynach wirtualnych (VM) ułatwia adres zabezpieczeń organizacji i wymagania zgodności przez szyfrowanie dysków maszyny Wirtualnej (w tym rozruchu i dysków z danymi) przy użyciu kluczy i zasad kontroli w [klucza usługi Azure Magazyn](https://azure.microsoft.com/services/key-vault/).

Szyfrowanie dysków rozwiązania dla systemu Windows jest oparta na [szyfrowania dysków funkcją BitLocker Microsoft](https://technet.microsoft.com/library/cc732774.aspx), i rozwiązanie Linux jest oparta na [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

Rozwiązanie obsługuje następujące scenariusze dla maszyn wirtualnych IaaS, jeśli są włączone w systemie Microsoft Azure:
-   Integracja z usługi Azure Key Vault

-   Maszyny wirtualne warstwy standardowa: A, D DS, G, GS i tak dalej, maszyny wirtualne IaaS serii

-   Włączenie szyfrowania w systemach Windows i maszyn wirtualnych systemu Linux IaaS

-   Wyłączenie szyfrowania systemu operacyjnego i danych dyski dla maszyn wirtualnych IaaS systemu Windows

-   Wyłączenie szyfrowania dla dysków z danymi dla maszyn wirtualnych systemu Linux IaaS

-   Włączenie szyfrowania na maszynach wirtualnych IaaS, którego uruchomiono system operacyjny klienta systemu Windows

-   Włączenie szyfrowania na woluminach ze ścieżki instalacji

-   Włączenie szyfrowania na maszynach wirtualnych systemu Linux, które są skonfigurowane przy użyciu rozkładanie (RAID) przy użyciu [mdadm](https://en.wikipedia.org/wiki/Mdadm)

-   Włączenie szyfrowania na maszynach wirtualnych systemu Linux przy użyciu [LVM (Menedżer woluminów logicznych)](https://msdn.microsoft.com/library/windows/desktop/bb540532) dla dysków z danymi

-   Włączenie szyfrowania na maszynach wirtualnych systemu Windows, które są skonfigurowane przy użyciu funkcji miejsca do magazynowania

-   Wszystkie publiczne regiony platformy Azure są obsługiwane.

Rozwiązanie nie obsługuje następujące scenariusze, funkcje i technologie w wersji:

-   Maszyny wirtualne IaaS warstwa podstawowa

-   Wyłączenie szyfrowania na dysku systemu operacyjnego dla maszyn wirtualnych systemu Linux IaaS

-   Maszyny wirtualne IaaS, które są tworzone za pomocą klasycznego metodę tworzenia maszyny Wirtualnej

-   Integracja z lokalnej usługi zarządzania kluczami

-   Usługa pliki Azure (udostępnionego systemu plików), sieciowego systemu plików (NFS), dynamiczne woluminy i maszyn wirtualnych systemu Windows, które są skonfigurowane przy użyciu systemów opartych na oprogramowaniu RAID

## <a name="sql-azure-database-isolation"></a>Izolacja Azure bazy danych SQL
Usługa SQL Database jest usługą relacyjnej bazy danych w chmurze firmy Microsoft opartą na wiodącym na rynku aparacie programu Microsoft SQL Server. Może ona obsługiwać obciążenia o znaczeniu krytycznym. Baza danych SQL oferuje przewidywalną danych izolacja na poziomie konta, geography / region, na podstawie i oparte na sieci — wszystko przy bliskich zeru nakładach administracji.

### <a name="sql-azure-application-model"></a>Model aplikacji usługi Azure SQL

[Microsoft SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started) bazy danych jest oparta na chmurze usługa relacyjnej bazy danych oparty na technologii programu SQL Server. Zapewnia usługi wysokiej dostępności, skalowalności, wielodostępne bazy danych obsługiwane przez firmę Microsoft w chmurze.

Z perspektywy aplikacji usługi SQL Azure zawiera następująca hierarchia: każdy poziom ma zawierania jeden do wielu poziomów poniżej.

![Model aplikacji usługi Azure SQL](./media/azure-isolation/azure-isolation-fig10.png)

Konto i Subskrypcja są pojęcia platformy Microsoft Azure do skojarzenia rozliczeń i zarządzania.

Logiczne serwerów i baz danych są pojęcia dotyczące usługi Azure SQL i są zarządzane za pomocą usług SQL Azure, pod warunkiem OData i TSQL interfejsy lub za pośrednictwem portalu SQL Azure, który zintegrowane w portalu Azure.

Serwery SQL Azure nie są fizycznej lub wystąpień maszyn wirtualnych, zamiast tego są one kolekcji baz danych, udostępnianie zasady zarządzania i zabezpieczeń, które są przechowywane w tak zwany "logicznych wzorca" bazy danych.

![Usługi SQL Azure](./media/azure-isolation/azure-isolation-fig11.png)

Logiczne baz danych master obejmują:

-   Kont logowania SQL używana do łączenia się z serwerem

-   Reguły zapory

Rozliczeń i dotyczące wykorzystania informacje dotyczące usługi Azure SQL bazy danych z tym samym serwerze logicznym nie ma gwarancji być tego samego wystąpienia fizycznego w klastrze usługi SQL Azure, zamiast tego aplikacje podać Nazwa docelowej bazy danych podczas nawiązywania połączenia.

Z perspektywy klienta serwera logicznego jest tworzony w graficznego geograficznie regionu, a rzeczywista tworzenia serwera odbywa się w jednym z klastrów w regionie.

### <a name="isolation-through-network-topology"></a>Izolację przez topologii sieci

Po utworzeniu serwera logicznego i nazwy DNS jest zarejestrowany, punkty adres tak zwany "VIP bramy" w centrum danych specyficznych dla rozmieszczenia serwera nazw DNS.

Za VIP (wirtualny adres IP) zostały kolekcja usług bezstanowych bramy. Ogólnie rzecz biorąc bram uzyskać wykonywane po koordynacji potrzebne między wiele źródeł danych (bazy danych master, bazy danych użytkowników itp.). Bramy usługi wykonania następujących czynności:
-   **Pośredniczenie połączenia TDS.** W tym lokalizowanie bazy danych użytkowników w wewnętrznej bazy danych klastra, wdrażanie sekwencji logowania i przesyłanie dalej pakietów TDS do wewnętrznej bazy danych i z powrotem.

-   **Zarządzanie bazą danych.** W tym wdrażanie kolekcji przepływów pracy można wykonać polecenia CREATE/ALTER/DROP operacji bazy danych. Operacje bazy danych może być wywoływany przez wykrywania pakietów TDS lub jawne API OData.

-   Użytkownik logowania CREATE/ALTER/DROP operacji

-   Operacje zarządzania serwerem logicznym za pomocą interfejsu API OData

![Izolację przez topologii sieci](./media/azure-isolation/azure-isolation-fig12.png)

Warstwa za bramy jest nazywany "wewnętrzne". Jest to, gdy wszystkie dane są przechowywane w sposób wysokiej dostępności. Każdy element danych jest określany należy do "partycji" lub "jednostki trybu failover", każde z nich o co najmniej trzy repliki. Repliki są przechowywane i replikowane za pomocą aparatu programu SQL Server i zarządzane przez system trybu failover, często określany jako "sieci szkieletowej".

Ogólnie rzecz biorąc system zaplecza nie przekazuje ruch wychodzący do innych systemów ze względów bezpieczeństwa. Jest on zarezerwowany do systemów w warstwie frontonu (bramy). Maszyny warstwy bramy ma ograniczone uprawnienia na komputerach zaplecza, aby zminimalizować możliwości ataku jako mechanizm obrony zabezpieczeń.

### <a name="isolation-by-machine-function-and-access"></a>Izolacja według funkcji maszyny i dostępu
Usługi SQL Azure (składa się z usługi działające na innym komputerze funkcje. Usługi SQL Azure jest podzielony na "wewnętrzna" bazy danych w chmurze i "frontonu" (/ zarządzania bramą) środowisk z zasady będzie tylko ruch do zaplecza, a nie wychodzących. Frontonu środowiska można przekazać do innych usług publicznie i ogólnie rzecz biorąc, tylko ma ograniczone uprawnienia w zaplecza (za mało, aby wywołać punktów wejścia, wymaganych do wywołania).

## <a name="networking-isolation"></a>Izolacja sieci
Wdrożenia usługi Azure ma wiele warstw izolacji sieci. Na poniższym diagramie przedstawiono różne warstwy izolacji sieci, które platforma Azure udostępnia klientom. Te warstwy są zarówno natywnego na platformie Azure, sam, jak i funkcje zdefiniowane przez klientów. Przychodzące z Internetu, przed atakami DDoS Azure zapewnia izolację na dużą skalę ataków na platformie Azure. Kolejna warstwa izolacji jest zdefiniowany przez klienta publicznych adresów IP (punkty końcowe), które są używane do ustalenia, jaki ruch mogą przechodzić do sieci wirtualnej usługi w chmurze. Azure macierzystego wirtualnego pełnej izolacji od innych sieci zapewnia izolację sieci i czy ruch przepływa tylko za pośrednictwem ścieżki użytkownika skonfigurowane i. Tych ścieżek i metody są następnej warstwy, których grup NSG, przez i wirtualnych urządzeń sieciowych może służyć do tworzenia granic izolacji chronić wdrożenia aplikacji w sieci chronionej.

![Izolacja sieci](./media/azure-isolation/azure-isolation-fig13.png)

**Izolacja ruchu:** A [sieci wirtualnej](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) jest granica izolacji ruchu na platformie Azure. Maszynach wirtualnych (VM) w jednej sieci wirtualnej nie może komunikować się bezpośrednio do maszyn wirtualnych w innej sieci wirtualnej, nawet jeśli obie sieci wirtualne są tworzone przez tego samego klienta. Izolacja jest krytyczne właściwość, która zapewnia klienta maszyn wirtualnych i komunikacja pozostanie prywatnej sieci wirtualnej.

[Podsieci](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview#subnets) zapewnia dodatkową warstwę izolacji o w oparciu o zakres adresów IP sieci wirtualnej. Adresy IP w sieci wirtualnej, sieć wirtualną można podzielić na wiele podsieci w celu uporządkowania i zapewnienia bezpieczeństwa. Maszyny wirtualne i wystąpienia ról PaaS wdrożone w podsieciach (tych samych lub różnych) w ramach sieci wirtualnej mogą komunikować się ze sobą bez dodatkowego konfigurowania. Można również skonfigurować [grupy zabezpieczeń sieci (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview#network-security-groups-nsg) zezwalające lub niezezwalające na wystąpienie maszyny Wirtualnej, na podstawie reguł skonfigurowanych w listy kontroli dostępu (ACL) NSG ruchu sieciowego. Grupy NSG można kojarzyć z podsieciami lub poszczególnymi wystąpieniami maszyn wirtualnych w danej podsieci. Gdy sieciowa grupa zabezpieczeń jest skojarzona z podsiecią, reguły listy ACL dotyczą wszystkich wystąpień maszyn wirtualnych w tej podsieci.

## <a name="next-steps"></a>Następne kroki

- [Izolacja sieci opcje dla komputerów w systemie Windows sieci wirtualnych platformy Azure](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/)

W tym klasycznego scenariusz frontonu i zaplecza, której komputery w określonej sieci wewnętrznej lub podsieć może Zezwalaj tylko niektórych klientów lub innym komputerom nawiązać połączenia z danego punktu końcowego oparte na listą dozwolonych adresów IP.

- [Obliczenia bazy danych izolacji](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf)

Microsoft Azure oferuje różne przetwarzania danych usług w chmurze zawierających szeroką gamę wystąpienia obliczeniowe i usług, które można skalować w górę i w dół automatycznie na potrzeby aplikacji lub enterprise.

- [Izolacja magazynu](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf)

Microsoft Azure oddziela klienta obliczeń na podstawie maszyny Wirtualnej z magazynu. Ta separacja umożliwia obliczeń i magazynowania można skalować niezależnie, co ułatwia zapewnienie izolacji i obsługi wielu dzierżawców. W związku z tym usługi Magazyn Azure działa na oddzielnego sprzętu z połączenia sieciowego do rozwiązań usługi obliczenia Azure z wyjątkiem logicznie. Uruchom wszystkie żądania HTTP i HTTPS, na podstawie wyboru klienta.

