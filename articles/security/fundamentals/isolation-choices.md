---
title: Izolacja w chmurze publicznej platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak platforma Azure zapewnia izolację przed złośliwymi i niezłymi użytkownikami i oferuje architektom różne opcje izolacji.
services: security
documentationcenter: na
author: UnifyCloud
manager: rkarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: TomSh
ms.openlocfilehash: c6e74e7992326d2a4b8fe24510742422b005c2e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280315"
---
# <a name="isolation-in-the-azure-public-cloud"></a>Izolacja w chmurze publicznej platformy Azure
Platforma Azure umożliwia uruchamianie aplikacji i maszyn wirtualnych (VM) na udostępnionej infrastrukturze fizycznej. Jedną z głównych motywacji ekonomicznych do uruchamiania aplikacji w środowisku chmury jest możliwość dystrybucji kosztów zasobów udostępnionych między wielu klientów. Ta praktyka multi-najmu zwiększa wydajność poprzez multipleksowanie zasobów wśród różnych klientów przy niskich kosztach. Niestety wprowadza również ryzyko udostępniania serwerów fizycznych i innych zasobów infrastruktury do uruchamiania poufnych aplikacji i maszyn wirtualnych, które mogą należeć do dowolnego i potencjalnie złośliwego użytkownika.

W tym artykule opisano, jak platforma Azure zapewnia izolację przed złośliwymi i niezłomnych użytkowników i służy jako przewodnik do zarządzania rozwiązaniami w chmurze, oferując różne opcje izolacji dla architektów.

## <a name="tenant-level-isolation"></a>Izolacja poziomu dzierżawy
Jedną z podstawowych zalet chmury obliczeniowej jest koncepcja wspólnej, wspólnej infrastruktury wielu klientów jednocześnie, co prowadzi do korzyści skali. Koncepcja ta nazywa się multi-dzierżawy. Firma Microsoft stale pracuje nad tym, aby architektura wielodostępna platformy Microsoft Cloud Azure była współużdą, a także standardami zabezpieczeń, poufności, prywatności, integralności i dostępności.

W przypadku miejsca pracy w chmurze dzierżawę można zdefiniować jako klienta lub organizację, do której należy określone wystąpienie danej usługi w chmurze. Dzięki platformie tożsamości udostępnianej przez platformę Microsoft Azure dzierżawa jest po prostu dedykowanym wystąpieniem usługi Azure Active Directory (Azure AD), które organizacja otrzymuje i jest właścicielem, gdy rejestruje się w usłudze w chmurze firmy Microsoft.

Każdy katalog usługi Azure AD jest odrębny i oddzielony od innych katalogów usługi Azure AD. Podobnie jak budynek biurowy, który jest zabezpieczonym zasobem przeznaczonym tylko dla Twojej organizacji, katalog Azure AD został zaprojektowany jako zabezpieczony zasób do użytku tylko Twojej organizacji. Architektura usługi Azure AD chroni dane klientów i informacje o tożsamości przez zmieszaniem. Oznacza to, że użytkownicy i administratorzy jednego katalogu usługi Azure AD nie mogą przypadkowo ani złośliwie uzyskać dostępu do danych w innym katalogu.

### <a name="azure-tenancy"></a>Dzierżawa platformy Azure
Dzierżawa platformy Azure (subskrypcja platformy Azure) odnosi się do relacji "klient/fakturowanie" i unikatowej [dzierżawy](../../active-directory/develop/quickstart-create-new-tenant.md) w [usłudze Azure Active Directory.](../../active-directory/fundamentals/active-directory-whatis.md) Izolacja poziomu dzierżawy na platformie Microsoft Azure jest osiągana przy użyciu usługi Azure Active Directory i [formantów opartych na rolach](../../role-based-access-control/overview.md) oferowanych przez nią. Każda subskrypcja platformy Azure jest skojarzona z jednym katalogiem usługi Azure Active Directory (AD).

Użytkownicy, grupy i aplikacje z tego katalogu mogą zarządzać zasobami w ramach subskrypcji platformy Azure. Te prawa dostępu można przypisać za pomocą witryny Azure portal, narzędzi wiersza polecenia platformy Azure i interfejsów API usługi Azure Management. Dzierżawy usługi Azure AD jest logicznie izolowane przy użyciu granic zabezpieczeń, dzięki czemu żaden klient nie może uzyskać dostępu lub naruszenia współdostępnych, złośliwie lub przypadkowo. Usługa Azure AD działa na serwerach "bez systemu operacyjnego" izolowanych w segmencie sieci segregowanej, gdzie filtrowanie pakietów na poziomie hosta i Zapora systemu Windows blokują niechciane połączenia i ruch.

- Dostęp do danych w usłudze Azure AD wymaga uwierzytelniania użytkownika za pośrednictwem usługi tokenu zabezpieczającego (STS). Informacje o istnieniu użytkownika, stanie i roli jest używany przez system autoryzacji do określenia, czy żądany dostęp do dzierżawy docelowej jest autoryzowany dla tego użytkownika w tej sesji.

![Dzierżawa platformy Azure](./media/isolation-choices/azure-isolation-fig1.png)


- Dzierżawy są kontenery dyskretne i nie ma relacji między nimi.

- Brak dostępu między dzierżawami, chyba że administrator dzierżawy udziela go za pośrednictwem federacji lub inicjowania obsługi administracyjnej kont użytkowników z innych dzierżawców.

- Fizyczny dostęp do serwerów, które składają się z usługi Azure AD i bezpośredni dostęp do systemów zaplecza usługi Azure AD, jest ograniczony.

- Użytkownicy usługi Azure AD nie mają dostępu do zasobów fizycznych lub lokalizacji i dlatego nie jest możliwe dla nich pominąć logiczne kontrole zasad RBAC podane poniżej.

W przypadku potrzeb w zakresie diagnostyki i konserwacji wymagany i używany jest model operacyjny, który wykorzystuje system podnoszenia uprawnień just-in-time. Usługa Azure AD Privileged Identity Management (PIM) wprowadza koncepcję uprawnionego administratora. Uprawnieni administratorzy powinni być [użytkownikami,](../../active-directory/privileged-identity-management/pim-configure.md) którzy potrzebują uprzywilejowanego dostępu od czasu do czasu, ale nie codziennie. Rola pozostaje nieaktywna, a gdy użytkownik potrzebuje dostępu, przechodzi proces aktywacji i staje się aktywnym administratorem na określony z góry czas.

![Azure AD Privileged Identity Management](./media/isolation-choices/azure-isolation-fig2.png)

Usługa Azure Active Directory obsługuje każdą dzierżawę we własnym kontenerze chronionym, z zasadami i uprawnieniami do kontenera i w nim wyłącznie własnością dzierżawy i jest nią zarządzana.

Koncepcja kontenerów dzierżawy jest głęboko zakorzeniona w usłudze katalogowej we wszystkich warstwach, od portali aż po magazyn trwały.

Nawet wtedy, gdy metadane z wielu dzierżaw usługi Azure Active Directory są przechowywane na tym samym dysku fizycznym, nie ma relacji między kontenerami innych niż to, co jest zdefiniowane przez usługę katalogową, która z kolei jest podyktowana przez administratora dzierżawy.

### <a name="azure-role-based-access-control-rbac"></a>Kontrola dostępu oparta na rolach platformy Azure (RBAC)
[Kontrola dostępu oparta na rolach platformy Azure (RBAC)](../../role-based-access-control/overview.md) ułatwia udostępnianie różnych składników dostępnych w ramach subskrypcji platformy Azure, zapewniając szczegółowe zarządzanie dostępem dla platformy Azure. Usługa Azure RBAC umożliwia segregować obowiązki w organizacji i udzielać dostępu na podstawie potrzeb użytkowników do wykonywania zadań. Zamiast udzielać wszystkim nieograniczonych uprawnień w subskrypcji platformy Azure lub zasobach, można zezwolić tylko na określone akcje.

Usługa Azure RBAC ma trzy podstawowe role, które mają zastosowanie do wszystkich typów zasobów:

- **Właściciel** ma pełny dostęp do wszystkich zasobów, w tym prawa do delegowania dostępu do innych osób.

- **Współautor** może tworzyć i zarządzać wszystkimi typami zasobów platformy Azure, ale nie może udzielić dostępu innym osobom.

- **Program Reader** może wyświetlać istniejące zasoby platformy Azure.

![Kontrola dostępu oparta na rolach na platformie Azure](./media/isolation-choices/azure-isolation-fig3.png)

Pozostałe role RBAC na platformie Azure umożliwiają zarządzanie określonymi zasobami platformy Azure. Na przykład rola współautora maszyny wirtualnej pozwala użytkownikom na tworzenie maszyn wirtualnych i zarządzanie nimi. Nie daje im dostępu do sieci wirtualnej platformy Azure lub podsieci, z którą łączy się maszyna wirtualna.

[Wbudowane role RBAC](../../role-based-access-control/built-in-roles.md) wyświetlają listę ról dostępnych na platformie Azure. Określa operacje i zakres, który każda wbudowana rola udziela użytkownikom. Jeśli chcesz zdefiniować własne role, aby uzyskać jeszcze większą kontrolę, zobacz, jak tworzyć [role niestandardowe w usłudze Azure RBAC.](../../role-based-access-control/custom-roles.md)

Niektóre inne możliwości usługi Azure Active Directory obejmują:
- Usługa Azure AD umożliwia logowanie jednokrotne do aplikacji SaaS, niezależnie od lokalizacji hostowania. Niektóre aplikacje są sfederowane z usługą Azure AD, a inne korzystają z logowania jednokrotnego z użyciem hasła. Aplikacje federacyjne mogą również obsługiwać aprowizacji użytkowników i [przechowywania haseł](https://www.techopedia.com/definition/31415/password-vault).

- Dostęp do danych w usłudze [Azure Storage](https://azure.microsoft.com/services/storage/) jest kontrolowany za pośrednictwem uwierzytelniania. Każde konto magazynu ma klucz podstawowy[(klucz konta magazynu](../../storage/common/storage-create-storage-account.md)lub zestaw SAK) i pomocniczy klucz tajny (podpis dostępu współdzielonego lub sygnatura dostępu współdzielonego).

- Usługa Azure AD udostępnia tożsamość jako usługę za pośrednictwem federacji przy użyciu [usług federacyjnych Active Directory,](../../active-directory/hybrid/how-to-connect-fed-azure-adfs.md)synchronizacji i replikacji z katalogami lokalnymi.

- [Uwierzytelnianie wieloskładnikowe platformy Azure](../../active-directory/authentication/multi-factor-authentication.md) to usługa uwierzytelniania wieloskładnikowego, która wymaga od użytkowników weryfikacji logowania przy użyciu aplikacji mobilnej, połączenia telefonicznego lub wiadomości tekstowej. Może służyć z usługą Azure AD, aby ułatwić zabezpieczanie zasobów lokalnych za pomocą serwera uwierzytelniania wieloskładnikowego platformy Azure, a także z niestandardowymi aplikacjami i katalogami przy użyciu zestawu SDK.

- [Usługi domenowe usługi Azure AD umożliwiają](https://azure.microsoft.com/services/active-directory-ds/) dołączanie maszyn wirtualnych platformy Azure do domeny usługi Active Directory bez wdrażania kontrolerów domeny. Możesz zalogować się do tych maszyn wirtualnych przy użyciu poświadczeń firmy Active Directory i administrować maszynami wirtualnymi przyłączanych do domeny przy użyciu zasad grupy w celu wymuszenia linii bazowych zabezpieczeń na wszystkich maszynach wirtualnych platformy Azure.

- [Usługa Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) zapewnia wysoce dostępną usługę zarządzania tożsamościami globalnymi dla aplikacji przeznaczonych dla konsumentów, która skaluje się do setek milionów tożsamości. Można ją łatwo integrować z platformami mobilnymi i platformami sieci Web. Konsumenci mogą logować się do wszystkich aplikacji za pomocą konfigurowalnych środowisk przy użyciu istniejących kont społecznościowych lub tworząc poświadczenia.

### <a name="isolation-from-microsoft-administrators--data-deletion"></a>Izolacja od administratorów firmy Microsoft & usuwanie danych
Firma Microsoft podejmuje zdecydowane działania w celu ochrony danych użytkownika przed niewłaściwym dostępem lub wykorzystaniem przez osoby nieupoważnione. Te procesy operacyjne i mechanizmy kontroli są poparte [Warunkami świadczenia usług online,](https://aka.ms/Online-Services-Terms)które oferują zobowiązania umowne regulujące dostęp do danych użytkownika.

-   Inżynierowie firmy Microsoft nie mają domyślnego dostępu do danych w chmurze. Zamiast tego przyznaje się im dostęp, pod nadzorem kierownictwa, tylko wtedy, gdy jest to konieczne. Ten dostęp jest dokładnie kontrolowany i rejestrowany i odwoływany, gdy nie jest już potrzebny.

-   Firma Microsoft może zatrudniać inne firmy do świadczenia ograniczonych usług w jej imieniu. Podwykonawcy mogą uzyskiwać dostęp do danych klientów wyłącznie w celu świadczenia usług, do których zostaliśmy wynajęci i nie mogą korzystać z nich w jakimkolwiek innym celu. Ponadto są one umownie zobowiązane do zachowania poufności informacji o naszych klientach.

Usługi biznesowe z certyfikatami poddanymi audytowi, takie jak ISO/IEC 27001, są regularnie weryfikowane przez firmę Microsoft i akredytowane firmy audytorskie, które przeprowadzają przykładowe audyty w celu uzyskania tego dostępu, wyłącznie w uzasadnionych celach biznesowych. Zawsze możesz uzyskać dostęp do własnych danych klienta w dowolnym momencie i z dowolnego powodu.

Jeśli usuniesz jakiekolwiek dane, platforma Microsoft Azure usunie dane, w tym kopie buforowane lub kopie zapasowe. W przypadku usług w zakresie to usunięcie nastąpi w ciągu 90 dni po zakończeniu okresu przechowywania. (Usługi w zakresie określonym w warunkach przetwarzania danych w naszym [Regulaminie usługi online.)](https://aka.ms/Online-Services-Terms)

Jeśli dysk używany do przechowywania ma awarię sprzętu, zostanie bezpiecznie [wymazany lub zniszczony,](https://microsoft.com/trustcenter/privacy/you-own-your-data) zanim firma Microsoft zwróci go producentowi w celu wymiany lub naprawy. Dane na dysku są zastępowane, aby upewnić się, że dane nie mogą być odzyskane w żaden sposób.

## <a name="compute-isolation"></a>Izolacja obliczeń
Platforma Microsoft Azure oferuje różne usługi obliczeniowe oparte na chmurze, które obejmują szeroki wybór wystąpień obliczeniowych & usług, które można automatycznie skalować w górę i w dół, aby zaspokoić potrzeby aplikacji lub przedsiębiorstwa. Te wystąpienia obliczeniowe i usługi oferują izolację na wielu poziomach, aby zabezpieczyć dane bez utraty elastyczności konfiguracji, których oczekują klienci.

### <a name="isolated-virtual-machine-sizes"></a>Rozmiary izolowanych maszyn wirtualnych

[!INCLUDE [virtual-machines-common-isolation](../../../includes/virtual-machines-common-isolation.md)]

### <a name="dedicated-hosts"></a>Dedykowane hosty
Oprócz izolowanych hostów opisanych w poprzedniej sekcji platforma Azure oferuje również dedykowane hosty. Dedykowane hosty na platformie Azure to usługa, która zapewnia serwery fizyczne, które mogą obsługiwać jedną lub więcej maszyn wirtualnych i które są dedykowane do pojedynczej subskrypcji platformy Azure. Dedykowane hosty zapewniają izolację sprzętową na poziomie serwera fizycznego. Żadne inne maszyny wirtualne nie zostaną umieszczone na hostach. Dedykowane hosty są wdrażane w tych samych centrach danych i współużytkują tę samą sieć i podstawową infrastrukturę magazynu, co inne hosty nieizolowane. Aby uzyskać więcej informacji, zobacz szczegółowe omówienie [dedykowanych hostów platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts).

### <a name="hyper-v--root-os-isolation-between-root-vm--guest-vms"></a>Izolacja systemu operacyjnego & głównego systemu operacyjnego funkcji Hyper-V między maszynami wirtualnymi & gośćmi
Platforma obliczeniowa platformy Azure opiera się na wirtualizacji maszyn , co oznacza, że cały kod klienta jest wykonywany na maszynie wirtualnej funkcji Hyper-V. W każdym węźle platformy Azure (lub sieciowym punkcie końcowym) istnieje funkcja Hypervisor, która działa bezpośrednio za pośrednictwem sprzętu i dzieli węzeł na zmienną liczbę maszyn wirtualnych gościa (maszyn wirtualnych).


![Izolacja systemu operacyjnego & głównego systemu operacyjnego funkcji Hyper-V między maszynami wirtualnymi & gośćmi](./media/isolation-choices/azure-isolation-fig4.jpg)


Każdy węzeł ma również jedną specjalną maszynę wirtualną głównego, która uruchamia system operacyjny hosta. Granica krytyczna to izolacja głównej maszyny Wirtualnej z maszyn wirtualnych gościa i maszyn wirtualnych gościa od siebie nawzajem, zarządzanych przez hipernadzorcę i główny system operacyjny. Parowanie hipernadzorcy z głównym systemem operacyjnym wykorzystuje wieloletnie doświadczenie firmy Microsoft w zakresie bezpieczeństwa systemu operacyjnego i nowsze uczenie się z funkcji Hyper-V firmy Microsoft, aby zapewnić silną izolację maszyn wirtualnych gości.

Platforma Azure korzysta ze środowiska zwirtualizowanego. Wystąpienia użytkowników działają jako autonomiczne maszyny wirtualne, które nie mają dostępu do fizycznego serwera hosta.

Hypervisor platformy Azure działa jak mikro jądro i przekazuje wszystkie żądania dostępu sprzętowego z maszyn wirtualnych gościa do hosta do przetwarzania przy użyciu interfejsu pamięci współużytkowanej o nazwie VMBus. Uniemożliwia to użytkownikom uzyskiwanie dostępu do nieprzetworzonego odczytu/zapisu/wykonania w systemie i zmniejsza ryzyko związane z udostępnianiem zasobów systemowych.

### <a name="advanced-vm-placement-algorithm--protection-from-side-channel-attacks"></a>Zaawansowany algorytm umieszczania maszyn wirtualnych & ochronę przed atakami kanału bocznego
Każdy atak krzyżowy maszyny wirtualnej składa się z dwóch kroków: umieszczenie maszyny wirtualnej kontrolowanej przez przeciwnika na tym samym hoście co jedna z maszyn wirtualnych ofiary, a następnie naruszenie granicy izolacji w celu kradzieży poufnych informacji o ofiarach lub wpłyć na jej wydajność w przypadku chciwości lub wandalizmu. Platforma Microsoft Azure zapewnia ochronę w obu krokach przy użyciu zaawansowanego algorytmu umieszczania maszyn wirtualnych i ochrony przed wszystkimi znanymi atakami kanału bocznego, w tym hałaśliwymi maszynami wirtualnymi sąsiada.

### <a name="the-azure-fabric-controller"></a>Kontroler sieci szkieletowej platformy Azure
Kontroler sieci szkieletowej platformy Azure jest odpowiedzialny za przydzielanie zasobów infrastruktury do obciążeń dzierżawy i zarządza komunikacją jednokierunkową z hosta do maszyn wirtualnych. Algorytm umieszczania maszyny Wirtualnej kontrolera sieci szkieletowej platformy Azure jest wysoce zaawansowany i prawie niemożliwy do przewidzenia jako fizyczny poziom hosta.

![Kontroler sieci szkieletowej platformy Azure](./media/isolation-choices/azure-isolation-fig5.png)

Funkcja hypervisor platformy Azure wymusza separację pamięci i procesów między maszynami wirtualnymi i bezpiecznie kieruje ruch sieciowy do dzierżaw systemu operacyjnego gościa. Eliminuje to możliwość ataku kanału bocznego na poziomie maszyny Wirtualnej.

Na platformie Azure główna maszyna wirtualna jest specjalna: uruchamia wzmocniony system operacyjny o nazwie główny system operacyjny, który obsługuje agenta sieci szkieletowej (FA). FA są używane z kolei do zarządzania agentami gościa (GA) w obrębie zaproszonych środowisk operacyjnych na maszynach wirtualnych klientów. Urząd Nadzoru zarządza również węzłami magazynu.

Kolekcja funkcji hypervisor platformy Azure, główny system operacyjny/Śt i maszyny wirtualne klienta/GA składa się z węzła obliczeniowego. FA są zarządzane przez kontroler sieci szkieletowej (FC), który istnieje poza węzłami obliczeniowymi i magazynu (klastry obliczeniowe i magazynowania są zarządzane przez oddzielne kontrolery domeny). Jeśli klient aktualizuje plik konfiguracyjny aplikacji podczas jego pracy, FC komunikuje się z FA, który następnie kontaktuje się z GA, które powiadamiają aplikację o zmianie konfiguracji. W przypadku awarii sprzętu FC automatycznie znajdzie dostępny sprzęt i uruchomi tam ponownie maszynę wirtualną.

![Kontroler sieci szkieletowej platformy Azure](./media/isolation-choices/azure-isolation-fig6.jpg)

Komunikacja z kontrolera sieci szkieletowej do agenta jest jednokierunkowa. Agent implementuje usługę chroniona przez SSL, która odpowiada tylko na żądania z kontrolera. Nie może inicjować połączeń z kontrolerem lub innymi uprzywilejowanymi węzłami wewnętrznymi. FC traktuje wszystkie odpowiedzi tak, jakby były niezaufane.


![Kontroler sieci szkieletowej](./media/isolation-choices/azure-isolation-fig7.png)

Izolacja rozciąga się od głównej maszyny Wirtualnej z maszyn wirtualnych gościa i maszyn wirtualnych gości od siebie nawzajem. Węzły obliczeniowe są również izolowane od węzłów magazynu w celu zwiększenia ochrony.


Hipernadzorca i system operacyjny hosta zapewniają pakiet sieciowy — filtry, aby zapewnić, że niezaufane maszyny wirtualne nie mogą generować fałszywego ruchu ani odbierać ruchu niezasyłania do nich, bezpośredniego ruchu do chronionych punktów końcowych infrastruktury lub wysyłania/odbierania nieodpowiedniego ruchu emisji.


### <a name="additional-rules-configured-by-fabric-controller-agent-to-isolate-vm"></a>Dodatkowe reguły skonfigurowane przez agenta kontrolera sieci szkieletowej do izolowania maszyny wirtualnej
Domyślnie cały ruch jest blokowany podczas tworzenia maszyny wirtualnej, a następnie agent kontrolera sieci szkieletowej konfiguruje filtr pakietów, aby dodać reguły i wyjątki, aby zezwolić na autoryzowany ruch.

Istnieją dwie kategorie reguł, które są zaprogramowane:

-   **Konfiguracja maszyny lub reguły infrastruktury:** Domyślnie cała komunikacja jest zablokowana. Istnieją wyjątki umożliwiające maszynie wirtualnej wysyłanie i odbieranie ruchu DHCP i DNS. Maszyny wirtualne mogą również wysyłać ruch do "publicznego" Internetu i wysyłać ruch do innych maszyn wirtualnych w ramach tej samej sieci wirtualnej platformy Azure i serwera aktywacji systemu operacyjnego. Lista dozwolonych miejsc docelowych wychodzących maszyn wirtualnych nie obejmuje podsieci routera platformy Azure, zarządzania platformą Azure i innych właściwości firmy Microsoft.

-   **Plik konfiguracji roli:** Definiuje przychodzące listy kontroli dostępu (ACL) na podstawie modelu usług dzierżawy.

### <a name="vlan-isolation"></a>Izolacja sieci VLAN
W każdym klastrze znajdują się trzy sieci VLAN:

![Izolacja sieci VLAN](./media/isolation-choices/azure-isolation-fig8.jpg)


-   Główna sieć VLAN – łączy niezaufane węzły klienta

-   FC VLAN – zawiera zaufane kontrolery domeny i systemy pomocnicze

-   Sieć VLAN urządzenia — zawiera zaufaną sieć i inne urządzenia infrastrukturalne

Komunikacja jest dozwolona z sieci FC VLAN do głównej sieci VLAN, ale nie może być inicjowana z głównej sieci VLAN do sieci FC VLAN. Komunikacja jest również zablokowana z głównej sieci VLAN do sieci VLAN urządzenia. Gwarantuje to, że nawet jeśli węzeł z uruchomionym kodem klienta zostanie naruszony, nie może atakować węzłów na sieciACH VLAN FC lub urządzeniach.

## <a name="storage-isolation"></a>Izolacja magazynu
### <a name="logical-isolation-between-compute-and-storage"></a>Logiczna izolacja między obliczakiem a magazynem
W ramach podstawowego projektu platforma Microsoft Azure oddziela obliczenia oparte na maszynach wirtualnych od magazynu. Ta separacja umożliwia obliczenia i magazynowanie do skalowania niezależnie, co ułatwia zapewnienie wielu dzierżaw i izolacji.

W związku z tym usługa Azure Storage działa na oddzielnym sprzęcie bez łączności sieciowej z usługą Azure Compute, z wyjątkiem logicznie. Oznacza to, że podczas tworzenia dysku wirtualnego miejsce na dysku nie jest przydzielane dla całej jego pojemności. Zamiast tego tworzona jest tabela, która mapuje adresy na dysku wirtualnym do obszarów na dysku fizycznym, a ta tabela jest początkowo pusta. **Przy pierwszym zapisie danych na dysku wirtualnym przydzielane jest miejsce na dysku fizycznym, a wskaźnik do niego umieszczany w tabeli.**
### <a name="isolation-using-storage-access-control"></a>Izolacja za pomocą kontroli dostępu do magazynu
**Kontrola dostępu w usłudze Azure Storage** ma prosty model kontroli dostępu. Każda subskrypcja platformy Azure można utworzyć co najmniej jedno konto magazynu. Każde konto magazynu ma jeden klucz tajny, który jest używany do kontrolowania dostępu do wszystkich danych na tym koncie magazynu.

![Izolacja za pomocą kontroli dostępu do magazynu](./media/isolation-choices/azure-isolation-fig9.png)

**Dostęp do danych usługi Azure Storage (w tym tabel)** można kontrolować za pomocą tokenu [Sygnatura dostępu współdzielonego (sygnatura dostępu współdzielonego),](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) który udziela dostępu o określonym zakresie. Sygnatury dostępu Współdzielonego jest tworzony za pomocą szablonu kwerendy (URL), podpisanego za pomocą [zestawu SAK (klucz konta magazynu)](https://msdn.microsoft.com/library/azure/ee460785.aspx). Ten [podpisany adres URL](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) może być podany do innego procesu (czyli delegowanego), który następnie można wypełnić szczegóły kwerendy i złożyć żądanie usługi magazynu. Sygnatura dostępu Współdzielonego umożliwia udzielanie klientom dostępu opartego na czasie bez ujawniania klucza tajnego konta magazynu.

Sygnatury dostępu Współdzielonego oznacza, że możemy udzielić klientowi ograniczonych uprawnień, do obiektów na naszym koncie magazynu przez określony czas i z określonym zestawem uprawnień. Możemy udzielić tych ograniczonych uprawnień bez konieczności udostępniania kluczy dostępu do konta.

### <a name="ip-level-storage-isolation"></a>Izolacja magazynu na poziomie IP
Można ustanowić zapory i zdefiniować zakres adresów IP dla zaufanych klientów. W przypadku zakresu adresów IP tylko klienci, którzy mają adres IP w zdefiniowanym zakresie, mogą łączyć się z [usługą Azure Storage](../../storage/blobs/security-recommendations.md).

Dane magazynu IP mogą być chronione przed nieautoryzowanymi użytkownikami za pośrednictwem mechanizmu sieciowego, który jest używany do przydzielania dedykowanego lub dedykowanego tunelu ruchu do magazynu IP.

### <a name="encryption"></a>Szyfrowanie
Platforma Azure oferuje następujące typy szyfrowania w celu ochrony danych:
-   Szyfrowanie podczas transferu

-   Szyfrowanie w spoczynku

#### <a name="encryption-in-transit"></a>Szyfrowanie podczas transferu
Szyfrowanie podczas przesyłania jest mechanizmem ochrony danych przesyłanych przez sieci. Za pomocą usługi Azure Storage można zabezpieczyć dane przy użyciu:

-   [Szyfrowanie na poziomie transportu](../../storage/blobs/security-recommendations.md), takie jak HTTPS podczas przesyłania danych do lub z usługi Azure Storage.

-   [Szyfrowanie przewodowe](../../storage/blobs/security-recommendations.md), takie jak szyfrowanie SMB 3.0 dla udziałów plików platformy Azure.

-   [Szyfrowanie po stronie klienta](../../storage/blobs/security-recommendations.md), aby zaszyfrować dane przed ich przeniesieniem do magazynu i odszyfrować dane po ich przeniesieniu z magazynu.

#### <a name="encryption-at-rest"></a>Szyfrowanie w spoczynku
Dla wielu organizacji [szyfrowanie danych w spoczynku](isolation-choices.md) jest obowiązkowym krokiem w kierunku prywatności, zgodności i suwerenności danych. Istnieją trzy funkcje platformy Azure, które zapewniają szyfrowanie danych, które są "w spoczynku":

-   [Szyfrowanie usługi magazynu](../../storage/blobs/security-recommendations.md) umożliwia żądanie, aby usługa magazynu automatycznie szyfrować dane podczas zapisywania ich w usłudze Azure Storage.

-   [Szyfrowanie po stronie klienta](../../storage/blobs/security-recommendations.md) zapewnia również funkcję szyfrowania w spoczynku.

-   [Szyfrowanie dysków platformy Azure](../azure-security-disk-encryption-overview.md) umożliwia szyfrowanie dysków systemu operacyjnego i dysków danych używanych przez maszynę wirtualną IaaS.

#### <a name="azure-disk-encryption"></a>Usługa Azure Disk Encryption
[Szyfrowanie dysków platformy Azure](../azure-security-disk-encryption-overview.md) dla maszyn wirtualnych (VM) pomaga spełnić wymagania dotyczące zabezpieczeń i zgodności organizacji, szyfrując dyski maszyn wirtualnych (w tym dyski rozruchowe i dyski danych) za pomocą kluczy i zasad kontrolujących w [usłudze Azure Key Vault.](https://azure.microsoft.com/services/key-vault/)

Rozwiązanie szyfrowania dysków dla systemu Windows opiera się na [szyfrowaniu dysków funkcją Microsoft BitLocker,](https://technet.microsoft.com/library/cc732774.aspx)a rozwiązanie Linux oparte jest na [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

Rozwiązanie obsługuje następujące scenariusze dla maszyn wirtualnych IaaS, gdy są one włączone na platformie Microsoft Azure:
-   Integracja z usługą Azure Key Vault

-   Maszyny wirtualne warstwy standardowej: A, D, DS, G, GS itd., klasy VM serii IaaS

-   Włączanie szyfrowania na maszynach wirtualnych IaaS systemu Windows i Linux

-   Wyłączanie szyfrowania na systemach operacyjnych i dyskach danych dla maszyn wirtualnych IaaS systemu Windows

-   Wyłączanie szyfrowania na dyskach danych dla maszyn wirtualnych IaaS systemu Linux

-   Włączanie szyfrowania na maszynach wirtualnych IaaS z systemem operacyjnym klienta systemu Windows

-   Włączanie szyfrowania na woluminach za pomocą ścieżek instalacji

-   Włączanie szyfrowania na maszynach wirtualnych z systemem Linux skonfigurowanych z rozkładaniem dysków (RAID) przy użyciu [mdadm](https://en.wikipedia.org/wiki/Mdadm)

-   Włączanie szyfrowania na maszynach wirtualnych z systemem Linux przy użyciu [LVM(Logical Volume Manager)](https://msdn.microsoft.com/library/windows/desktop/bb540532) dla dysków danych

-   Włączanie szyfrowania na maszynach wirtualnych systemu Windows skonfigurowanych przy użyciu miejsc do magazynowania

-   Obsługiwane są wszystkie regiony publiczne platformy Azure

Rozwiązanie nie obsługuje następujących scenariuszy, funkcji i technologii w wersji:

-   Maszyny wirtualne IaaS warstwy podstawowej

-   Wyłączanie szyfrowania na dysku systemu operacyjnego dla maszyn wirtualnych IaaS systemu Linux

-   Maszyny wirtualne IaaS, które są tworzone przy użyciu klasycznej metody tworzenia maszyn wirtualnych

-   Integracja z lokalną usługą zarządzania kluczami

-   Usługi Azure Files (udostępniony system plików), system plików NFS, woluminy dynamiczne i maszyny wirtualne systemu Windows skonfigurowane za pomocą programowych systemów RAID

## <a name="sql-azure-database-isolation"></a>Izolacja bazy danych sql azure
Usługa SQL Database jest usługą relacyjnej bazy danych w chmurze firmy Microsoft opartą na wiodącym na rynku aparacie programu Microsoft SQL Server. Może ona obsługiwać obciążenia o znaczeniu krytycznym. Baza danych SQL oferuje przewidywalną izolację danych na poziomie konta, geografii / regionu i na podstawie sieci — wszystko to z administracją bliską zeru.

### <a name="sql-azure-application-model"></a>Model aplikacji platformy SQL Azure

[Platforma Microsoft SQL Azure](../../sql-database/sql-database-single-database-get-started.md) Baza danych to oparta na chmurze relacyjnej usługi bazy danych opartej na technologiach programu SQL Server. Zapewnia wysoce dostępną, skalowalną usługę bazy danych z wieloma dzierżawami obsługiwaną przez firmę Microsoft w chmurze.

Z punktu widzenia aplikacji SQL Azure zapewnia następującą hierarchię: Każdy poziom ma jeden do wielu hermetyzacji poziomów poniżej.

![Model aplikacji platformy SQL Azure](./media/isolation-choices/azure-isolation-fig10.png)

Konto i subskrypcja to koncepcje platformy Microsoft Azure do skojarzenia rozliczeń i zarządzania.

Serwery logiczne i bazy danych są pojęciami specyficznymi dla platformy SQL i są zarządzane przy użyciu platformy SQL Azure, pod warunkiem interfejsów OData i TSQL lub za pośrednictwem witryny SQL Azure portal, która została zintegrowana z witryną Azure Portal.

Serwery SQL Azure nie są wystąpieniami fizycznymi ani vm, zamiast tego są kolekcjami baz danych, zasad zarządzania udostępnianiem i zabezpieczeń, które są przechowywane w tak zwanej bazie danych "logicznego wzorca".

![Usługi SQL Azure](./media/isolation-choices/azure-isolation-fig11.png)

Logiczne główne bazy danych obejmują:

-   Logowania SQL używane do łączenia się z serwerem

-   Reguły zapory

Rozliczenia i informacje związane z użyciem dla baz danych sql azure z tego samego serwera logicznego nie są gwarantowane, aby być w tym samym wystąpieniu fizycznym w klastrze sql azure, zamiast tego aplikacje muszą podać nazwę docelowej bazy danych podczas łączenia.

Z punktu widzenia klienta serwer logiczny jest tworzony w regionie geografialnym, podczas gdy rzeczywiste tworzenie serwera odbywa się w jednym z klastrów w regionie.

### <a name="isolation-through-network-topology"></a>Izolacja za pomocą topologii sieci

Po utworzeniu serwera logicznego i zarejestrowaniu jego nazwy DNS nazwa DNS wskazuje adres "Gateway VIP" w określonym centrum danych, w którym serwer został umieszczony.

Za VIP (wirtualny adres IP), mamy kolekcję bezstanowych usług bramy. Ogólnie rzecz biorąc bramy angażują się, gdy istnieje koordynacja między wieloma źródłami danych (główna baza danych, baza danych użytkowników itp.). Usługi bramy implementują następujące elementy:
-   **Proxy połączenia TDS.** Obejmuje to lokalizowanie bazy danych użytkowników w klastrze wewnętrznej bazy danych, implementowanie sekwencji logowania, a następnie przekazywanie pakietów TDS do wewnętrznej bazy danych i z powrotem.

-   **Zarządzanie bazami danych.** Obejmuje to implementowanie kolekcji przepływów pracy do wykonywania operacji bazy danych CREATE/ALTER/DROP. Operacje bazy danych mogą być wywoływane przez wąchanie pakietów TDS lub jawne interfejsy API OData.

-   TWORZENIE/ZMIENIANIE/UPUSZCZANIE operacji logowania/użytkownika

-   Operacje zarządzania serwerami logicznymi za pośrednictwem interfejsu API OData

![Izolacja za pomocą topologii sieci](./media/isolation-choices/azure-isolation-fig12.png)

Warstwa za bramami jest nazywana "zapleczem". W tym miejscu wszystkie dane są przechowywane w sposób o wysokiej dostępności. Każdy fragment danych jest mówi się, że należą do "partycji" lub "jednostki pracy awaryjnej", każdy z nich ma co najmniej trzy repliki. Repliki są przechowywane i replikowane przez aparat programu SQL Server i zarządzane przez system pracy awaryjnej często określane jako "sieci szkieletowej".

Ogólnie rzecz biorąc, system zaplecza nie komunikuje się wychodzących do innych systemów jako środek ostrożności. Jest to zarezerwowane dla systemów w warstwie front-end (gateway). Maszyny warstwy bramy mają ograniczone uprawnienia na komputerach zaplecza, aby zminimalizować powierzchnię ataku jako mechanizm obrony w głębi.

### <a name="isolation-by-machine-function-and-access"></a>Izolacja według funkcji maszyny i dostępu
SQL Azure (składa się z usług działających na różnych funkcji komputera. SQL Azure jest podzielony na "wewnętrznej bazy danych" w chmurze i "front-end" (brama/zarządzanie) środowiska, z ogólną zasadą ruchu tylko będzie do zaplecza, a nie obecnie. Środowisko front-end może komunikować się ze światem zewnętrznym innych usług i ogólnie ma tylko ograniczone uprawnienia w zapleczu (wystarczy wywołać punkty wejścia, które musi wywołać).

## <a name="networking-isolation"></a>Izolacja sieci
Wdrożenie platformy Azure ma wiele warstw izolacji sieci. Na poniższym diagramie przedstawiono różne warstwy izolacji sieci platformy Azure zapewnia klientom. Te warstwy są zarówno natywne w samej platformie Azure i funkcji zdefiniowanych przez klienta. Przychodzące z Internetu usługa Azure DDoS zapewnia izolację przed atakami na dużą skalę na platformie Azure. Następną warstwą izolacji są zdefiniowane przez klienta publiczne adresy IP (punkty końcowe), które są używane do określenia, który ruch może przechodzić przez usługę w chmurze do sieci wirtualnej. Izolacja sieci wirtualnej platformy Azure natywnej zapewnia pełną izolację od wszystkich innych sieci, a ruch przepływa tylko przez ścieżki i metody skonfigurowane przez użytkownika. Te ścieżki i metody są następną warstwą, w której sieciowe grupy zabezpieczeń, UDR i sieciowe urządzenia wirtualne mogą służyć do tworzenia granic izolacji w celu ochrony wdrożeń aplikacji w sieci chronionej.

![Izolacja sieci](./media/isolation-choices/azure-isolation-fig13.png)

**Izolacja ruchu:** [Sieć wirtualna](../../virtual-network/virtual-networks-overview.md) to granica izolacji ruchu na platformie Azure. Maszyny wirtualne (VM) w jednej sieci wirtualnej nie mogą komunikować się bezpośrednio z maszynami wirtualnymi w innej sieci wirtualnej, nawet jeśli obie sieci wirtualne są tworzone przez tego samego klienta. Izolacja jest właściwością krytyczną, która zapewnia klientom maszyny wirtualne i komunikację pozostaje prywatna w sieci wirtualnej.

[Podsieć](../../virtual-network/virtual-networks-overview.md) oferuje dodatkową warstwę izolacji w sieci wirtualnej na podstawie zakresu IP. Adresy IP w sieci wirtualnej, można podzielić sieć wirtualną na wiele podsieci dla organizacji i zabezpieczeń. Maszyny wirtualne i wystąpienia ról PaaS wdrożone w podsieciach (tych samych lub różnych) w ramach sieci wirtualnej mogą komunikować się ze sobą bez dodatkowego konfigurowania. Można również skonfigurować [sieciową grupę zabezpieczeń (NSG),](../../virtual-network/virtual-networks-overview.md) aby zezwalała lub odmawiała ruchu sieciowego wystąpieniu maszyny Wirtualnej na podstawie reguł skonfigurowanych na liście kontroli dostępu (ACL) sieciowej grupy zabezpieczeń. Grupy NSG można kojarzyć z podsieciami lub poszczególnymi wystąpieniami maszyn wirtualnych w danej podsieci. Gdy sieciowa grupa zabezpieczeń jest skojarzona z podsiecią, reguły listy ACL dotyczą wszystkich wystąpień maszyn wirtualnych w tej podsieci.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [opcjach izolacji sieci dla komputerów w sieciach wirtualnych platformy Windows Azure](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/). Obejmuje to klasyczny scenariusz front-end i back-end, w którym maszyny w określonej sieci zaplecza lub podsieci mogą zezwalać tylko niektórym klientom lub innym komputerom na łączenie się z określonym punktem końcowym na podstawie listy dozwolonych adresów IP.

- Dowiedz się więcej o [izolacji maszyny wirtualnej na platformie Azure](../../virtual-machines/windows/isolation.md). Usługa Azure Compute oferuje rozmiary maszyn wirtualnych, które są odizolowane od określonego typu sprzętu i dedykowane jednemu klientowi.
