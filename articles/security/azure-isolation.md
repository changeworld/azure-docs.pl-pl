---
title: Izolacja w chmurze publicznej platformy Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat obliczeń usług w chmurze, które zawierają szeroką gamę wystąpień obliczeniowych i usług, które można skalować w górę i w dół automatycznie do potrzeb aplikacji lub przedsiębiorstwa.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: b8142551d9c20c18d83c256b3f07a0deb291577c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66147654"
---
# <a name="isolation-in-the-azure-public-cloud"></a>Izolacja w chmurze publicznej platformy Azure
##  <a name="introduction"></a>Wprowadzenie
### <a name="overview"></a>Omówienie
Ułatwiają Azure bieżących i przyszłych klientów zrozumieć i korzystanie z różnych funkcji zabezpieczeń dostępnych w i związanych z platformą Azure, firma Microsoft opracowała szereg oficjalne dokumenty, przeglądy zabezpieczeń, najlepsze rozwiązania i Listy kontrolne.
Tematy zakresu pod względem szczegółowo i są okresowo aktualizowane. Ten dokument jest częścią tej serii, zgodnie z opisem w następującej sekcji abstrakcyjne.

### <a name="azure-platform"></a>Platforma Azure
Platforma Azure to otwarta i elastyczna platforma usługi, która obsługuje najszerszy zakres systemów operacyjnych, języków programowania, struktur, narzędzia, baz danych i urządzeń. Można na przykład:
- Uruchamianie kontenerów systemu Linux z integracją Docker;
- Tworzenie aplikacji w językach JavaScript, Python, .NET, PHP, Java i Node.js; i
- Tworzenie zapleczy dla systemów iOS, Android i Windows urządzenia.

Platforma Microsoft Azure obsługuje te same technologie przez miliony deweloperów i specjalistów IT, już używane i wypróbowane.

Podczas tworzenia na lub migracji zasobów informatycznych do dostawcy usług w chmurze publicznej, polegasz na możliwości w organizacji, aby chronić aplikacje i dane za pomocą usług i kontrolek, zapewniają Zarządzanie zabezpieczeniami zasobów opartych na chmurze.

Infrastruktura platformy Azure została zaprojektowana kompleksowo, począwszy od obiektu po aplikacje hostujące jednocześnie miliony klientów, i zapewnia wiarygodną podstawę zaspokajania potrzeb firm w zakresie bezpieczeństwa. Ponadto platforma Azure oferuje szeroki zakres konfigurowalnych opcji zabezpieczeń oraz możliwość sterowania nimi, co pozwala dostosować zabezpieczenia w taki sposób, aby spełniały unikatowe wymagania realizowanych wdrożeń. Ten dokument pomaga spełnić te wymagania.

### <a name="abstract"></a>Abstrakcyjny

Microsoft Azure umożliwia uruchamianie aplikacji i maszyn wirtualnych (VM) na udostępnionym infrastruktury fizycznej. Jednym z podstawowe ekonomiczne zresztą do uruchamiania aplikacji w środowisku chmury jest możliwość dystrybucji koszt zasobów udostępnionych między wieloma klientami. Taka praktyka wielodostępu zwiększa wydajność przez multipleksowania zasobów między różnymi klientów przy niskich kosztach. Niestety wprowadza również ryzyko udostępniania serwerów fizycznych i innych zasobów infrastruktury można uruchamiać aplikacji zawierających poufne dane i maszyn wirtualnych, które mogą należeć do dowolnego i potencjalnie złośliwych użytkowników.

W tym artykule opisano, jak platforma Microsoft Azure zapewnia izolację niezłośliwych i złośliwych użytkowników i służy jako przewodnik dla tworzenia rozwiązań w chmurze, udostępniając różne opcje izolacji na architektów. Ten dokument koncentruje się na technologii platformy Azure i opcji zabezpieczeń przeznaczonych dla klientów i nie jest podejmowana próba adres umowy SLA, ceny, modele i zagadnienia dotyczące praktyki DevOps.

## <a name="tenant-level-isolation"></a>Poziom izolacji dzierżawcy
Jedną z podstawowych zalet chmury obliczeniowej to pojęcie udostępnionego, wspólnej infrastruktury przez wielu klientów jednocześnie, co prowadzi do oszczędności skali. Takie podejście jest nazywany wielu dzierżawców. Firma Microsoft stale współpracuje aby upewnić się, że architektury wielodostępnej platformy chmury Microsoft Azure obsługuje bezpieczeństwa, poufności, ochrony prywatności, integralności i standardów dostępności.

W przypadku miejsca pracy w chmurze dzierżawę można zdefiniować jako klienta lub organizację, do której należy określone wystąpienie danej usługi w chmurze. Z platformą tożsamości udostępniane przez Microsoft Azure dzierżawa to po prostu dedykowane wystąpienie usługi Azure Active Directory (Azure AD), Twoja organizacja otrzymuje i której zostaje właścicielem po zarejestrowaniu się w usłudze usługi w chmurze firmy Microsoft.

Każdy katalog usługi Azure AD jest odrębny i oddzielony od innych katalogów usługi Azure AD. Podobnie jak budynek biurowy, który jest zabezpieczonym zasobem przeznaczonym tylko dla Twojej organizacji, katalog Azure AD został zaprojektowany jako zabezpieczony zasób do użytku tylko Twojej organizacji. Architektura usługi Azure AD chroni dane klientów i informacje o tożsamości przez zmieszaniem. Oznacza to, że użytkownicy i administratorzy jednego katalogu usługi Azure AD nie mogą przypadkowo ani złośliwie uzyskać dostępu do danych w innym katalogu.

### <a name="azure-tenancy"></a>Dzierżawy platformy Azure
Dzierżawy platformy Azure (subskrypcja platformy Azure), który odnosi się do relacji "klienta/billing" i unikatową [dzierżawy](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant) w [usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis). Poziom izolacji dzierżawców w systemie Microsoft Azure jest osiągane przy użyciu usługi Azure Active Directory i [kontrolki opartej na rolach](https://docs.microsoft.com/azure/role-based-access-control/overview) oferowanych przez nią. Każda subskrypcja platformy Azure jest skojarzony z jednego katalogu usługi Azure Active Directory (AD).

Użytkownikom, grupom i aplikacjom z tego katalogu można zarządzać zasobów w subskrypcji platformy Azure. Można przypisać te prawa dostępu przy użyciu witryny Azure portal, narzędzi wiersza polecenia platformy Azure i interfejsów API zarządzania platformy Azure. Dzierżawa usługi Azure AD jest logicznie izolowana za pomocą granice zabezpieczeń tak, aby klienta można uzyskać dostęp lub naruszyć wspólnej dzierżaw złośliwe lub przypadkowo. Usługi Azure AD działa na serwerach "zera" izolowane w segmencie sieci przedziałami, gdzie filtrowanie pakietów na poziomie hosta i zapory Windows Blokuj niechciane połączenia i ruch.

- Dostęp do danych w usłudze Azure AD wymaga uwierzytelniania użytkowników za pomocą usługi tokenu zabezpieczającego (STS). Informacje na temat istnienia, włączona i roli użytkownika jest używana przez system autoryzacji do określenia, czy żądany dostęp do dzierżawy docelowej jest autoryzowany do tego użytkownika w ramach tej sesji.

![Dzierżawy platformy Azure](./media/azure-isolation/azure-isolation-fig1.png)


- Dzierżawy są osobne kontenerów i nie ma relacji między tymi.

- Brak dostępu do różnych dzierżawy, chyba że administrator dzierżawy spowoduje przyznanie za pośrednictwem federacji lub inicjowania obsługi administracyjnej kont użytkowników z innych dzierżaw.

- Fizyczny dostęp do serwerów, wchodzące w skład usługi Azure AD i bezpośredni dostęp do systemów zaplecza usługi Azure AD, jest ograniczona.

- Użytkownicy usługi Azure AD nie mają dostępu do zasobów fizycznych lub lokalizacji, a w związku z tym nie jest możliwe obejścia RBAC zasad testy logiczne podane następujące.

Diagnostyka i koniecznych konserwacji działający model, korzystającej z systemu podniesienie uprawnień just in time jest wymagane i używane. Usługa Azure AD Privileged Identity Management (PIM) wprowadza koncepcję uprawnionego administratora. [Uprawnionymi administratorami](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) powinny być użytkownicy, którzy muszą uprzywilejowanego dostępu teraz, a następnie, ale nie każdego dnia. Rola pozostaje nieaktywna, a gdy użytkownik potrzebuje dostępu, przechodzi proces aktywacji i staje się aktywnym administratorem na określony z góry czas.

![Usługa Azure AD Privileged Identity Management](./media/azure-isolation/azure-isolation-fig2.png)

Usługa Azure Active Directory obsługuje każdą dzierżawę we własnym kontenerze chronione, za pomocą zasad i uprawnień, aby i w kontenerze wyłącznie do i są zarządzane przez dzierżawcę.

Koncepcja kontenerów dzierżawy jest głęboko utrwalone w usłudze katalogowej we wszystkich warstwach, z portali do skorzystania z magazynu trwałego.

Nawet wtedy, gdy metadane z wielu dzierżaw usługi Azure Active Directory są przechowywane na tym samym dysku fizycznego, nie ma relacji między kontenerów innych niż zdefiniowana przez usługę katalogową, który z kolei jest określany przez administratora dzierżawy.

### <a name="azure-role-based-access-control-rbac"></a>Kontrola dostępu oparta na rolach na platformie Azure (RBAC)
[Usługa Azure opartej na rolach kontrola dostępu (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) ułatwia udostępnianie różnych składników dostępnych w ramach subskrypcji platformy Azure, zapewniając szczegółowe zarządzanie dostępem dla platformy Azure. Azure RBAC umożliwia segregowanie zadań w Twojej organizacji i udzielanie dostępu opartego na użytkownicy potrzebują do wykonywania swoich zadań. Zamiast wszystkich nieograniczone uprawnienia w subskrypcji platformy Azure lub zasobów, można zezwolić tylko określone akcje.

RBAC platformy Azure ma trzy podstawowe role, które są stosowane do wszystkich typów zasobów:

- **Właściciel** ma pełny dostęp do wszystkich zasobów łącznie z prawej strony można delegować dostępu do innych osób.

- **Współautor** można tworzyć i zarządzać nimi wszystkie typy zasobów platformy Azure, ale nie może przyznawać dostępu innym osobom.

- **Czytnik** można wyświetlić istniejące zasoby platformy Azure.

![Kontrola dostępu oparta na rolach na platformie Azure](./media/azure-isolation/azure-isolation-fig3.png)

Pozostałe role RBAC na platformie Azure umożliwiają zarządzanie określonych zasobów platformy Azure. Na przykład rola Współautor maszyny wirtualnej umożliwia użytkownikowi tworzenie i zarządzanie maszynami wirtualnymi. Nie daje im dostęp do usługi Azure Virtual Network lub podsieci, która łączy się z maszyny wirtualnej.

[Wbudowane role RBAC](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) Utwórz listę ról, które są dostępne na platformie Azure. Określa operacje i zakres, który każdy wbudowana rola przyznaje użytkownikom. Jeśli chcesz zdefiniować własne role, aby uzyskać większą kontrolę, zobacz, jak tworzyć [niestandardowych ról RBAC platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles).

Niektóre inne funkcje usługi Azure Active Directory, obejmują:
- Usługa Azure AD umożliwia logowanie Jednokrotne do aplikacji SaaS, niezależnie od tego, gdzie są obsługiwane. Niektóre aplikacje są sfederowane z usługą Azure AD, a inne korzystają z logowania jednokrotnego z użyciem hasła. Aplikacje federacyjne może także obsługiwać Inicjowanie obsługi użytkowników i [haseł](https://www.techopedia.com/definition/31415/password-vault).

- Dostęp do danych w usłudze [Azure Storage](https://azure.microsoft.com/services/storage/) jest kontrolowany za pośrednictwem uwierzytelniania. Każde konto magazynu ma klucz podstawowy ([klucza konta magazynu](https://docs.microsoft.com/azure/storage/storage-create-storage-account), SAK) oraz pomocniczy klucz tajny (sygnatury dostępu współdzielonego, lub sygnatury dostępu Współdzielonego).

- Usługa Azure AD zapewnia tożsamość jako usługa za pośrednictwem federacji przy użyciu [Active Directory Federation Services](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-azure-adfs), synchronizacji i replikacji katalogów lokalnych.

- [Usługa Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) jest usługą Multi-Factor authentication, która wymaga od użytkowników sprawdzić logowania przy użyciu aplikacji mobilnej, połączenia telefonicznego lub wiadomości SMS. Może służyć za pomocą usługi Azure AD ułatwia bezpieczne zasobów lokalnych przy użyciu serwera Azure Multi-Factor Authentication, a także przy użyciu niestandardowych aplikacji i katalogów korzystających z zestawu SDK.

- [Usługi domenowe Azure AD](https://azure.microsoft.com/services/active-directory-ds/) umożliwiają przyłączanie maszyn wirtualnych platformy Azure do domeny usługi Active Directory bez konieczności wdrażania kontrolerów domeny. Możesz zalogować się do tych maszyn wirtualnych przy użyciu firmowych poświadczeń usługi Active Directory i zarządzać przyłączone do domeny maszynami wirtualnymi za pomocą zasad grupy do wymuszania wartości bazowych zabezpieczeń na wszystkich maszynach wirtualnych platformy Azure.

- [Usługa Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) udostępnia usługę o wysokiej dostępności globalnej tożsamości zarządzania dla aplikacji przeznaczonych dla konsumentów, która może obsłużyć setki milionów tożsamości. Można ją łatwo integrować z platformami mobilnymi i platformami sieci Web. Klientów można zarejestrować się do wszystkich aplikacji przy użyciu w pełni dostosowywanego procesu przy użyciu istniejących kont sieci społecznościowych lub tworząc poświadczenia.

### <a name="isolation-from-microsoft-administrators--data-deletion"></a>Izolacja z Administratorzy usługi Microsoft i usuwanie danych
Firma Microsoft podejmuje środki silne, aby chronić dane przed nieodpowiednim dostępu lub użyj osobom nieupoważnionym. Te kontrole i procesy operacyjne są wspierane przez [Online Services — warunki](https://aka.ms/Online-Services-Terms), które oferują zobowiązania umowne, które określają sposób dostępu do danych.

-   Inżynierowie firmy Microsoft bez domyślnego dostępu do danych w chmurze. Zamiast tego otrzyma dostęp, w obszarze zarządzania nadzoru, tylko wtedy, gdy jest to konieczne. Dostęp jest starannie kontrolowany i rejestrowane i odwoływane, gdy nie jest już potrzebny.

-   Firma Microsoft może wynajmować inne firmy do świadczenia ograniczonych usług w jej imieniu. Podwykonawcy mogą uzyskiwać dostęp do danych klienta tylko w celu dostarczania usług, dla którego, firma Microsoft wynajmuje im przesyłanie, a nie wolno im wykorzystywać ich do jakichkolwiek innych celów. Ponadto one jest zobowiązana na mocy Umowy związanych z zachowania poufności informacji naszych klientów.

Usługi biznesowe dzięki usłudze inspekcji certyfikaty, takich jak ISO/IEC 27001 są regularnie weryfikowane przez firmę Microsoft i akredytowanych audytorów, które przeprowadzają przykładowe inspekcje, aby stwierdzać, że dostęp, jedynie do celów biznesową. Można zawsze dostęp do danych klienta w dowolnym momencie i z dowolnego powodu.

Jeśli usuniesz, wszelkie dane, Microsoft Azure usuwa dane, w tym wszystkie buforowane lub kopii zapasowej kopii. W przypadku usług w zakresie, usunięcie zostanie przeprowadzona w ciągu 90 dni, po upływie okresu przechowywania. (Usługi w zakresie są zdefiniowane w sekcji warunków przetwarzania danych naszych [Online Services — warunki](https://aka.ms/Online-Services-Terms).)

Jeśli na dysku używane do przechowywania wystąpi awaria sprzętowa, jest bezpiecznie [usunięte lub zniszczone](https://microsoft.com/trustcenter/privacy/you-own-your-data) przed Microsoft zwraca go do producenta dla zastąpienia lub naprawy. Dane na dysku zostanie zastąpiony, aby upewnić się, że za pomocą jakichkolwiek środków nie można odzyskać dane.

## <a name="compute-isolation"></a>Izolacja obliczeń
Microsoft Azure oferuje różne oparte na chmurze obliczeniowej usługi, które obejmują szeroki wybór wystąpień obliczeniowych i usług, które można skalować w górę i w dół automatycznie do potrzeb aplikacji lub przedsiębiorstwa. Te wystąpienia obliczeniowego i usługi oferują izolacji na różnych poziomach w celu zabezpieczenia danych bez ograniczania elastyczności w konfiguracji zapotrzebowania klientów.

### <a name="isolated-virtual-machine-sizes"></a>Rozmiary maszyn wirtualnych odizolowane
Usługa Azure Compute oferuje różne rozmiary maszyn wirtualnych, które są izolowane pod kątem konkretnego rodzaju sprzętu i przeznaczone dla jednego klienta.  Te rozmiary maszyn wirtualnych są dostosowane do obciążeń wymagających wysokiego stopnia izolacji od innych klientów, a zwłaszcza obciążeń związanych z takimi elementami jak zgodność z przepisami i wymogi prawne.  Klienci mogą również wybrać w celu dalszego podziału zasobów izolowanych maszyn wirtualnych przy użyciu [pomocy technicznej platformy Azure dla zagnieżdżonych maszyn wirtualnych](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).

Korzystanie z rozmiarem izolowane gwarantuje, że maszyna wirtualna będzie tylko jedno uruchomione w tym wystąpieniu określonego serwera.  Bieżących ofert izolowanych maszyn wirtualnych obejmują:
* Standard_E64is_v3
* Warstwie standardowa_e64i_v3
* Maszyna wirtualna Standard_M128ms
* Standard_GS5
* Maszyna wirtualna Standard_G5
* Standardowa_ds15_v2
* Maszyna wirtualna Standard_D15_v2
* Standard_F72s_v2

Dowiedz się więcej na temat każdej izolowanej dostępny rozmiar [tutaj](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory).

### <a name="hyper-v--root-os-isolation-between-root-vm--guest-vms"></a>Funkcji Hyper-V & katalog główny systemu operacyjnego izoluje główną maszynę Wirtualną i maszyn wirtualnych gościa
Platforma obliczeniowa platformy Azure opiera się na wirtualizacji maszyny, co oznacza, że cały kod klienta jest wykonywana na maszynie wirtualnej funkcji Hyper-V. Na każdym węźle platformy Azure (lub punktu końcowego sieci) Brak funkcji Hypervisor, która jest uruchamiana bezpośrednio nad sprzętem i dzieli węzeł na numer zmiennej z maszyn wirtualnych gościa (VM).


![Funkcji Hyper-V & katalog główny systemu operacyjnego izoluje główną maszynę Wirtualną i maszyn wirtualnych gościa](./media/azure-isolation/azure-isolation-fig4.jpg)


Każdy węzeł ma również jedną specjalne głównego maszyny Wirtualnej, co działa systemu operacyjnego hosta. Krytyczne granica jest izolacja główną maszynę Wirtualną od maszyn wirtualnych gościa oraz maszyny wirtualne gościa od siebie nawzajem, zarządzane przez funkcję hypervisor i katalog główny systemu operacyjnego. Parowanie funkcji hypervisor/root systemu operacyjnego korzysta z dziesięcioleci firmy Microsoft o środowisko zabezpieczeń systemu operacyjnego i nowszej uczenie się to od firmy Microsoft Hyper-V, aby zapewnić silną izolację maszyn wirtualnych gościa.

Platforma Azure korzysta ze środowiska zwirtualizowanego. Wystąpienia użytkownika działają jako autonomiczne maszyny wirtualne, które nie mają dostępu do fizycznego serwera hosta.

Funkcja hypervisor platformy Azure działa jak mikrojądro i przekazuje wszystkie żądania dostępu do sprzętu z maszyn wirtualnych gościa do hosta w celu przetworzenia, korzystając z interfejsu pamięci współużytkowanej o nazwie VMBus. Uniemożliwia to użytkownikom uzyskiwanie dostępu do nieprzetworzonego odczytu/zapisu/wykonania w systemie i zmniejsza ryzyko związane z udostępnianiem zasobów systemowych.

### <a name="advanced-vm-placement-algorithm--protection-from-side-channel-attacks"></a>Zaawansowany algorytm umieszczania maszyn wirtualnych i ochrony przed atakami kanału po stronie
Każdego ataku maszyny Wirtualnej między obejmuje dwa kroki: umieszczenie maszyny Wirtualnej w kontrolowany przez osobę atakującą na tym samym hoście jako ofiara maszyn wirtualnych, a następnie naruszenia granic izolacji, które mają wpływ na jego wydajność, Chciwość lub vandalism lub rozwiązania pozwalające wykradać poufne ofiarą informacje. Microsoft Azure oferuje ochronę na obu czynności przy użyciu zaawansowanych algorytm umieszczania maszyny Wirtualnej i ochronę przed atakami wszystkie znane po stronie kanału łącznie zasobożernymi maszynami wirtualnymi.

### <a name="the-azure-fabric-controller"></a>Kontroler sieci szkieletowej platformy Azure
Kontroler sieci szkieletowej Azure jest odpowiedzialny za przydzielanie zasobów infrastruktury do obciążeń dzierżawcy i umożliwia zarządzanie jednokierunkową komunikacją z hosta do maszyn wirtualnych. Algorytm umieszczanie maszyn wirtualnych kontrolera sieci szkieletowej platformy Azure jest wysoce zaawansowane i przewidywanie jako hosta fizycznego poziom jest prawie niemożliwe.

![Kontroler sieci szkieletowej platformy Azure](./media/azure-isolation/azure-isolation-fig5.png)

Funkcja hypervisor platformy Azure wymusza rozdzielenie pamięci i procesów między maszynami wirtualnymi oraz bezpiecznie kieruje ruch sieciowy do dzierżawców systemu operacyjnego gościa. Pozwala to wyeliminować możliwość i stronie kanału ataku na poziomie maszyny Wirtualnej.

Na platformie Azure, główną maszynę Wirtualną jest szczególna: działa ze wzmocnionymi zabezpieczeniami systemu operacyjnego o nazwie głównej systemu operacyjnego hostującego z agentem sieci szkieletowej (da). Program fAs są używane do zarządzania agentów gości (GA) z kolei w ramach systemów operacyjnych gościa na maszynach wirtualnych klienta. Program fAs również zarządzać węzłów magazynu.

Zbiór funkcja hypervisor platformy Azure, główny system operacyjny/FA, a klient maszyny wirtualne/gaz składa się z węzła obliczeniowego. Program fAs są zarządzane przez kontroler sieci szkieletowej (FC), który istnieje poza węzłów obliczeniowych i magazynu (klastry obliczeniowe i magazynowe są zarządzane przez oddzielne FCs). Jeśli klient aktualizuje plik konfiguracji swoich aplikacji, jest uruchomiona, FC komunikuje się z FA, który następnie skontaktuje się z gaz, która powiadamia stosowania zmian w konfiguracji. W przypadku awarii sprzętu FC automatycznie znajdzie sprzętu i ponownie maszynę Wirtualną, istnieje.

![Kontroler sieci szkieletowej platformy Azure](./media/azure-isolation/azure-isolation-fig6.jpg)

Komunikacji z kontrolerem sieci szkieletowej w agencie jest jednokierunkowa. Agent implementuje usługi chroniony przez protokół SSL, który tylko odpowiada na żądania z kontrolera. Go nie można zainicjować połączenia do kontrolera lub innych uprzywilejowanych węzłów wewnętrznego. FC traktuje wszystkie odpowiedzi, jakby pochodziły z niezaufanych.


![Kontroler sieci szkieletowej](./media/azure-isolation/azure-isolation-fig7.png)

Izolacja rozciąga się od katalogu głównego maszyny Wirtualnej z maszyn wirtualnych gościa oraz maszyny wirtualne gościa od siebie nawzajem. Obliczeniowe węzły również są odizolowane od węzłów magazynu w celu zwiększenia ochrony.


Funkcja hypervisor i hosta, podane systemu operacyjnego w pakiecie sieciowym - filtrów, które pomagają zapewnić, że niezaufanych maszyn wirtualnych nie Generowanie fałszywego ruchu lub odbieranie ruchu nie jest skierowana do nich kierować ruch do punktów końcowych chronionej infrastruktury lub wysyłania i odbierania niewłaściwe ruchu emisji.


### <a name="additional-rules-configured-by-fabric-controller-agent-to-isolate-vm"></a>Dodatkowe zasady skonfigurowane przez agenta kontrolera sieci szkieletowej do izolowania maszyn wirtualnych
Domyślnie cały ruch jest blokowany, po utworzeniu maszyny wirtualnej, a następnie agent kontrolera sieci szkieletowej konfiguruje filtr pakietów, aby dodać zasady i wyjątki zezwalające na ruch autoryzowany.

Istnieją dwie kategorie reguł, które są programowane:

-   **Reguły konfiguracji lub infrastruktury maszyny:** Domyślnie cała komunikacja jest blokowana. Istnieją wyjątki umożliwiające wysyłanie i odbieranie ruchu DHCP i DNS maszyny wirtualnej. Maszyny wirtualne można również wysyłać ruch do "publicznej" sieci internet i przesyłać dane do innych maszyn wirtualnych w ramach tej samej sieci wirtualnej platformy Azure i Serwer aktywacji systemu operacyjnego. Lista dozwolonych miejsc docelowych ruchu wychodzącego maszyn wirtualnych nie zawiera podsieci routera platformy Azure, zarządzania systemu Azure i innych obszarów firmy Microsoft.

-   **Plik konfiguracji roli:** Definiuje dla ruchu przychodzącego list kontroli dostępu (ACL) oparte na modelu usługi dzierżawcy.

### <a name="vlan-isolation"></a>Izolacja sieci VLAN
W każdym klastrze istnieją trzy sieci VLAN:

![Izolacja sieci VLAN](./media/azure-isolation/azure-isolation-fig8.jpg)


-   Główny sieci VLAN — dla połączeń klientów niezaufanych węzłów

-   Sieć VLAN FC — zawiera zaufanych FCs i obsługi systemów

-   Urządzenie sieci VLAN — zawiera zaufanych sieci i innych urządzeniach infrastruktury

Komunikacji jest dozwolone z sieci VLAN FC główny sieci VLAN, ale nie można zainicjować z głównego sieci VLAN w sieci VLAN, FC. Komunikacja jest również zablokowana główny sieci VLAN do urządzenia sieci VLAN. Gwarantuje to, że nawet wtedy, gdy węzeł uruchomiony kod klienta zostanie naruszony, go nie ataki węzłów na FC lub urządzenie sieci VLAN.

## <a name="storage-isolation"></a>Izolacja magazynu
### <a name="logical-isolation-between-compute-and-storage"></a>Izolacja logiczna między zasoby obliczeniowe i Magazyn
Jako część jej podstawowe projekt Microsoft Azure oddziela maszyny Wirtualnej na podstawie obliczeń z magazynu. Ta separacja pozwala udostępniać funkcje obliczeń i magazynu, które można skalować niezależnie, co ułatwia zapewnienie obsługi wielu dzierżawców i izolacji.

W związku z tym usługi Azure Storage uruchamia oddzielne sprzętu braku łączności z siecią Azure Compute, z wyjątkiem logicznie. [To](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf) oznacza, że podczas tworzenia dysku wirtualnego miejsca na dysku nie jest przydzielony jego całą pojemność. Zamiast tego utworzono tabelę, która mapuje adresy na dysku wirtualnym miejsca na dysku fizycznego, a ta tabela jest początkowo pusta. **Po raz pierwszy klient zapisuje dane na dysku wirtualnym miejsce na dysku fizycznym jest przydzielane i wskaźnika do niego znajduje się w tabeli.**
### <a name="isolation-using-storage-access-control"></a>Kontrola dostępu do magazynu przy użyciu izolacji
**Kontrola dostępu w usłudze Azure Storage** ma modelu kontroli dostępu. Każda subskrypcja platformy Azure można utworzyć co najmniej jedno konto magazynu. Każde konto magazynu ma jednego klucza tajnego, jest używane do kontrolowania dostępu do wszystkich danych na tym koncie magazynu.

![Kontrola dostępu do magazynu przy użyciu izolacji](./media/azure-isolation/azure-isolation-fig9.png)

**Dostęp do danych usługi Azure Storage (w tym tabel)** mogą być kontrolowane za pośrednictwem [SAS (Shared Access Signature)](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) token, który przyznaje zakresu dostępu. Sygnatura dostępu Współdzielonego jest tworzona przy użyciu szablonu zapytania (URL) podpisany przy użyciu [SAK (klucz konta magazynu)](https://msdn.microsoft.com/library/azure/ee460785.aspx). Które [podpisany adresu URL](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) można nadać inny proces (który delegowane), co można, a następnie wypełnij szczegóły zapytania i wysłać żądanie usługi storage. Sygnatury dostępu Współdzielonego można udzielić dostępu na podstawie czasu dla klientów bez ujawniania tajnego klucza konta magazynu.

Sygnatura dostępu Współdzielonego oznacza, że firma Microsoft jest przyznanie klientowi ograniczonych uprawnień do obiektów w naszym konta magazynu, w określonym przedziale czasu i z określonym zestawem uprawnień. Firma Microsoft przyznawania uprawnień ograniczone, bez konieczności udostępniania kluczy dostępu do Twojego konta.

### <a name="ip-level-storage-isolation"></a>IP magazynu poziomu izolacji
Można określić zapory i określić zakres adresów IP z zaufanych klientów. Zakres adresów IP tylko klienci, którzy mają adresy IP ze zdefiniowanego zakresu może się łączyć z [usługi Azure Storage](https://docs.microsoft.com/azure/storage/storage-security-guide).

Dane magazynu IP mogą być chronione przed nieautoryzowanymi użytkownikami za pośrednictwem sieci mechanizm, który jest używany do alokowania dedykowanych lub dedykowany tunelowania ruchu do adresów IP magazynu.

### <a name="encryption"></a>Szyfrowanie
Platforma Azure oferuje następujące typy szyfrowania do ochrony danych:
-   Szyfrowanie podczas transferu

-   Szyfrowanie w spoczynku

#### <a name="encryption-in-transit"></a>Szyfrowanie podczas transferu
Szyfrowanie podczas transferu jest mechanizm ochrony danych podczas ich przesyłania w sieciach. Za pomocą usługi Azure Storage można zabezpieczyć dane przy użyciu:

-   [Szyfrowanie na poziomie transportu](https://docs.microsoft.com/azure/storage/storage-security-guide#encryption-in-transit), taki jak HTTPS podczas przesyłania danych do i z usługi Azure Storage.

-   [Połączenie szyfrowania](../storage/common/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares), takie jak szyfrowanie protokołu SMB 3.0 udziałów plików platformy Azure.

-   [Szyfrowanie po stronie klienta](https://docs.microsoft.com/azure/storage/storage-security-guide#using-client-side-encryption-to-secure-data-that-you-send-to-storage), aby szyfrować dane, zanim zostanie przekazany do magazynu, jak i do odszyfrowania danych, gdy zostanie przeniesiona poza magazynu.

#### <a name="encryption-at-rest"></a>Szyfrowanie w spoczynku
W przypadku wielu organizacji [szyfrowanie danych magazynowanych](https://docs.microsoft.com/azure/security/azure-isolation) stanowi krok obowiązkowy kierunku niezależność danych ochrony prywatności, zgodności i danych. Istnieją trzy funkcje platformy Azure, które zapewniają szyfrowania danych, które są "magazynowane":

-   [Szyfrowanie usługi Storage](https://docs.microsoft.com/azure/storage/storage-security-guide#encryption-at-rest) umożliwia zażądanie, czy usługi storage automatyczne szyfrowanie danych podczas zapisywania jej do usługi Azure Storage.

-   [Szyfrowanie po stronie klienta](https://docs.microsoft.com/azure/storage/storage-security-guide#client-side-encryption) udostępnia również funkcję szyfrowanie danych magazynowanych.

-   [Usługa Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) umożliwia szyfrowanie dysków systemu operacyjnego i dysków danych używanych przez maszynę wirtualną IaaS.

#### <a name="azure-disk-encryption"></a>Usługa Azure Disk Encryption
[Usługa Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) dla maszyn wirtualnych (VM) ułatwia zaspakajanie organizacyjnych bezpieczeństwa i zgodności z przepisami dzięki możliwości szyfrowania dysków maszyny Wirtualnej (w tym dysków rozruchowych i danych) przy użyciu kluczy i zasad w kontrolce [klucz usługi Azure Vault](https://azure.microsoft.com/services/key-vault/).

Rozwiązanie Disk Encryption for Windows opiera się na [szyfrowania dysków Microsoft BitLocker](https://technet.microsoft.com/library/cc732774.aspx), a rozwiązanie dla systemu Linux opiera się na [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

Rozwiązanie obsługuje następujące scenariusze dla maszyn wirtualnych IaaS, gdy są one włączone w systemie Microsoft Azure:
-   Integracja z usługą Azure Key Vault

-   W warstwie standardowa maszyny wirtualne: A, D, DS, G, GS i tak dalej, maszyny wirtualne z serii IaaS

-   Włączanie szyfrowania na Windows i maszyn wirtualnych IaaS z systemem Linux

-   Wyłączenie szyfrowania systemu operacyjnego i danych dyski dla maszyn wirtualnych IaaS Windows

-   Wyłączenie szyfrowania dla dysków z danymi dla maszyn wirtualnych IaaS z systemem Linux

-   Włączanie szyfrowania na maszynach wirtualnych IaaS, które są uruchomione w systemie operacyjnym klienta Windows

-   Włączanie szyfrowania dla woluminów przy użyciu ścieżki instalacji

-   Włączanie szyfrowania na maszynach wirtualnych systemu Linux, które są skonfigurowane przy użyciu rozkładanie (RAID) przy użyciu [mdadm](https://en.wikipedia.org/wiki/Mdadm)

-   Włączanie szyfrowania na maszynach wirtualnych z systemem Linux przy użyciu [LVM (Menedżer woluminów logicznych)](https://msdn.microsoft.com/library/windows/desktop/bb540532) dla dysków z danymi

-   Włączanie szyfrowania na maszynach wirtualnych Windows, które są konfigurowane przy użyciu funkcji miejsca do magazynowania

-   Wszystkich publicznych regionach platformy Azure są obsługiwane.

Rozwiązanie nie obsługuje następujących scenariuszy, funkcji i technologii w wersji:

-   Warstwa podstawowa maszyn wirtualnych IaaS

-   Wyłączenie szyfrowania na dysku systemu operacyjnego dla maszyn wirtualnych IaaS z systemem Linux

-   Maszyny wirtualne IaaS, które są tworzone za pomocą klasycznego metodę tworzenia maszyny Wirtualnej

-   Integracja z usługą zarządzania kluczami w środowisku lokalnym

-   Usługa pliki systemu Azure (udostępnionego systemu plików), Network File System (NFS), dynamiczne woluminy i maszyn wirtualnych Windows, które są skonfigurowane przy użyciu systemów programowej macierzy RAID

## <a name="sql-azure-database-isolation"></a>Izolacja bazy danych Azure SQL
Usługa SQL Database jest usługą relacyjnej bazy danych w chmurze firmy Microsoft opartą na wiodącym na rynku aparacie programu Microsoft SQL Server. Może ona obsługiwać obciążenia o znaczeniu krytycznym. Usługa SQL Database oferuje izolacja przewidywalne danych na poziomie konta, geography / region, na podstawie i oparte na sieci — wszystko przy bliskich zeru nakładach na administrację.

### <a name="sql-azure-application-model"></a>Model aplikacji usługi Azure SQL

[Microsoft SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started) baza danych jest oparta na chmurze usługa relacyjnej bazy danych oparta na technologiach programu SQL Server. Zapewnia usługa bazy danych o wysokiej dostępności, skalowalne, wielodostępne należącej do firmy Microsoft w chmurze.

Z poziomu aplikacji perspektywy SQL Azure udostępnia następującej hierarchii: Każdy poziom ma zawierania jeden do wielu poziomów poniżej.

![Model aplikacji usługi Azure SQL](./media/azure-isolation/azure-isolation-fig10.png)

Konto i Subskrypcja są koncepcji platformy Microsoft Azure, które skojarzyć Pomoc dotycząca rozliczeń i zarządzania.

Serwerów logicznych i baz danych są specyficzne dla SQL platformy Azure pojęcia i są zarządzane przy użyciu usługi SQL Azure, pod warunkiem OData i TSQL interfejsów lub za pośrednictwem portalu SQL Azure, które są zintegrowane w witrynie Azure portal.

Serwery SQL Azure nie są fizycznej lub wystąpień maszyn wirtualnych, zamiast tego są one kolekcji baz danych, udostępnianie zasady zarządzania i zabezpieczeń, które są przechowywane w tak zwany "wzorca logicznego" bazy danych.

![Usługi SQL Azure](./media/azure-isolation/azure-isolation-fig11.png)

Logicznej głównej bazy danych obejmują:

-   Używane do łączenia z serwerem nazw logowania SQL

-   Reguły zapory

Rozliczeń oraz związanych z użyciem informacji dla usługi Azure SQL bazy danych z tym samym serwerze logicznym nie musi być w jednym wystąpieniu fizycznych w klastrze usługi Azure SQL, zamiast tego aplikacje należy podać nazwę docelowej bazy danych, podczas nawiązywania połączenia.

Z perspektywy klienta serwera logicznego jest tworzony w regionu geograficznego graficznego, podczas gdy rzeczywisty tworzenia serwera odbywa się w poszczególnych klastrów w regionie.

### <a name="isolation-through-network-topology"></a>Izolacja przy użyciu topologii sieci

Po utworzeniu serwera logicznego i jego nazwa DNS jest zarejestrowana, wskazuje adres tak zwany "Brama adresu VIP" w centrum danych z konkretnych rozmieszczenie serwera nazw DNS.

Za VIP (wirtualny adres IP) mamy kolekcję usługi bezstanowej bramy. Ogólnie rzecz biorąc bram, Pobierz zaangażowane po koordynacji między wiele źródeł danych (bazy danych master, bazy danych użytkowników itp.). Usługi bramy zaimplementować następujące czynności:
-   **Pośredniczenie połączeń TDS.** W tym lokalizowanie bazy danych użytkownika w klastrze wewnętrznej bazy danych, implementacja sekwencji logowania i następnie przekazuje pakiety TDS do wewnętrznej bazy danych i z powrotem.

-   **Zarządzanie bazą danych.** W tym wdrażanie kolekcji przepływów pracy można wykonać polecenia CREATE/ALTER/DROP operacji bazy danych. Operacje bazy danych może być wywoływany przez zabezpieczeń Wykrywanie pakietów TDS lub jawne interfejsy API OData.

-   Operacji CREATE/ALTER/DROP login/użytkownika

-   Operacje zarządzania serwerem logicznym przy użyciu interfejsu API OData

![Izolacja przy użyciu topologii sieci](./media/azure-isolation/azure-isolation-fig12.png)

Warstwa za bramy jest nazywany "back-end". Jest to, gdzie wszystkie dane są przechowywane w sposób o wysokiej dostępności. Każdy element danych jest nazywany należą do "partycji" lub "jednostki trybu failover", każdy z nich posiadanie co najmniej trzema replikami. Repliki są przechowywane i replikowane za pomocą aparatu programu SQL Server i zarządzane przez system trybu failover, często nazywane "sieci szkieletowej".

Ogólnie rzecz biorąc systemu zaplecza nie komunikację wychodzącą z innymi systemami ze względów bezpieczeństwa. Jest on zarezerwowany do systemów w ramach warstwy frontonu (bramy). Warstwa maszyny bramy ma ograniczone uprawnienia, na komputerach zaplecza, aby zminimalizować obszar narażony na ataki w mechanizmie ochronę w głębi.

### <a name="isolation-by-machine-function-and-access"></a>Izolacja według funkcji usługi Machine i dostęp
Azure SQL (składa się z usług działających na innej maszynie funkcji. SQL Azure jest podzielony na bazę danych w chmurze "wewnętrzna" i "frontonu" (/ zarządzania bramą) środowisk przy użyciu ogólnej zasady będzie tylko ruch do zaplecza, a nie na poziomie. Frontonu środowiska może komunikować się ze światem innych usług i ogólnie rzecz biorąc, tylko ma ograniczone uprawnienia w infrastrukturze wewnętrznej (wystarczająco dużo, aby wywołać punkty wejścia, których potrzebuje do wywołania).

## <a name="networking-isolation"></a>Izolacja sieci
Wdrożenie usługi Azure ma wiele warstw izolacji sieci. Na poniższym diagramie przedstawiono różne warstwy izolacji sieci, w których platforma Azure oferuje klientom. Te warstwy są natywne w samą platformę Azure i funkcji zdefiniowanych przez klienta. Dla ruchu przychodzącego z Internetu — Azure przed atakami DDoS zapewnia izolację przed atakami na dużą skalę na platformie Azure. Kolejna warstwa izolacji jest zdefiniowaną przez użytkownika publiczne adresy IP (punkty końcowe.), które są używane do określenia, który ruch danych można przekazać za pośrednictwem usługi w chmurze do sieci wirtualnej. Natywne usługa Azure virtual, izolacja sieci zapewnia pełną izolację od innych sieci i że ruch przepływa tylko za pośrednictwem ścieżki skonfigurowane przez użytkownika i metody. Te ścieżki i metody jest kolejna warstwa, gdzie sieciowych grup zabezpieczeń, Routing zdefiniowany przez użytkownika i wirtualne urządzenia sieciowe mogą służyć do tworzenia granic izolacji, aby chronić wdrożenia aplikacji w chronionej sieci.

![Izolacja sieci](./media/azure-isolation/azure-isolation-fig13.png)

**Izolacja ruchu:** A [sieci wirtualnej](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) jest granicę izolacji ruchu na platformie Azure. Maszyn wirtualnych (VM) w jednej sieci wirtualnej nie może komunikować się bezpośrednio do maszyn wirtualnych w innej sieci wirtualnej, nawet jeśli obie sieci wirtualne są tworzone przez tego samego klienta. Izolacja jest krytyczne właściwości, które gwarantuje, że maszyny wirtualne klientów i komunikacja pozostanie w prywatnej sieci wirtualnej.

[Podsieci](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) zapewnia dodatkową warstwę izolacji dzięki w sieci wirtualnej na podstawie zakresu adresów IP. Adresy IP w sieci wirtualnej, sieć wirtualną można podzielić na wiele podsieci w celu uporządkowania i zapewnienia bezpieczeństwa. Maszyny wirtualne i wystąpienia ról PaaS wdrożone w podsieciach (tych samych lub różnych) w ramach sieci wirtualnej mogą komunikować się ze sobą bez dodatkowego konfigurowania. Można również skonfigurować [sieciowej grupy zabezpieczeń (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) do blokują lub zezwalają na ruch sieciowy do wystąpień maszyn wirtualnych, na podstawie reguł skonfigurowanych w listy kontroli dostępu (ACL) sieciowej grupy zabezpieczeń. Grupy NSG można kojarzyć z podsieciami lub poszczególnymi wystąpieniami maszyn wirtualnych w danej podsieci. Gdy sieciowa grupa zabezpieczeń jest skojarzona z podsiecią, reguły listy ACL dotyczą wszystkich wystąpień maszyn wirtualnych w tej podsieci.

## <a name="next-steps"></a>Następne kroki

- [Izolacja sieci opcje dla maszyn w Windows sieciami wirtualnymi platformy Azure](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/)

Obejmuje to klasyczny scenariusz frontonu i zaplecza, której komputery w określonej sieci wewnętrznej lub podsieci mogą Zezwalaj tylko na niektórych klientów lub innych komputerów nawiązać połączenie z danego punktu końcowego z listy dozwolonych adresów IP w oparciu.

- [Izolacja obliczeń](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf)

Microsoft Azure oferuje różne oparte na chmurze obliczeniowej usługi, które obejmują szeroki wybór wystąpień obliczeniowych i usług, które można skalować w górę i w dół automatycznie do potrzeb aplikacji lub przedsiębiorstwa.

- [Izolacja magazynu](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf)

Microsoft Azure oddziela klienta maszyny Wirtualnej na podstawie obliczeń z magazynu. Ta separacja pozwala udostępniać funkcje obliczeń i magazynu, które można skalować niezależnie, co ułatwia zapewnienie obsługi wielu dzierżawców i izolacji. W związku z tym usługi Azure Storage uruchamia oddzielne sprzętu braku łączności z siecią Azure Compute, z wyjątkiem logicznie. Wszystkie żądania są wykonywane za pośrednictwem protokołu HTTP lub HTTPS, w oparciu o wybranego klienta.

