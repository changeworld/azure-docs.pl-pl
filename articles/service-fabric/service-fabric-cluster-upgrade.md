---
title: Uaktualnianie klastra sieci szkieletowej usług Azure
description: Dowiedz się więcej o uaktualnianiu wersji lub konfiguracji klastra sieci szkieletowej usług Azure — ustawianiu trybu aktualizacji klastra, uaktualnianiu certyfikatów, dodawaniu portów aplikacji, wykonywania poprawek systemu operacyjnego i tego, czego można oczekiwać po wykonaniu uaktualnień.
ms.topic: conceptual
ms.date: 11/12/2018
ms.custom: sfrev
ms.openlocfilehash: 6897854820339fc78dd9083c82147dce95ab68b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258657"
---
# <a name="upgrading-and-updating-an-azure-service-fabric-cluster"></a>Uaktualnianie i aktualizowanie klastra usługi Service Fabric na platformie Azure

Dla każdego nowoczesnego systemu, projektowanie dla modernizacji jest kluczem do osiągnięcia długoterminowego sukcesu produktu. Klaster sieci szkieletowej usług Azure jest zasobem, który jest własnością, ale jest częściowo zarządzany przez firmę Microsoft. W tym artykule opisano, co jest zarządzane automatycznie i co można skonfigurować samodzielnie.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Sterowanie wersją sieci szkieletowej, która działa w klastrze

Upewnij się, że w klastrze zawsze działa [obsługiwana wersja sieci szkieletowej](service-fabric-versions.md). Za każdym razem, gdy firma Microsoft ogłasza wydanie nowej wersji sieci szkieletowej usług, poprzednia wersja jest oznaczona do końca pomocy technicznej po co najmniej 60 dniach od tej daty. Nowe wydania są ogłaszane na [blogu zespołu Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric).

14 dni przed wygaśnięciem wydania klastra jest uruchomiona, zdarzenie kondycji jest generowany, który umieszcza klastra w stanie kondycji ostrzeżenie. Klaster pozostaje w stanie ostrzeżenia, dopóki nie uaktualnisz do obsługiwanej wersji sieci szkieletowej.

Można ustawić klaster tak, aby odbierał automatyczne uaktualnienia sieci szkieletowej w miarę ich zwalniania przez firmę Microsoft lub można wybrać obsługiwaną wersję sieci szkieletowej, na której ma być włączony klaster.  Aby dowiedzieć się więcej, przeczytaj [uaktualnienie wersji sieci szkieletowej usług klastra](service-fabric-cluster-upgrade-version-azure.md).

## <a name="fabric-upgrade-behavior-during-automatic-upgrades"></a>Zachowanie uaktualnienia sieci szkieletowej podczas automatycznych uaktualnień

Firma Microsoft przechowuje kod sieci szkieletowej i konfigurację uruchamianą w klastrze platformy Azure. W razie potrzeby wykonujemy automatyczne monitorowane uaktualnienia do oprogramowania. Te uaktualnienia mogą być kod, konfiguracja lub obu. Aby upewnić się, że aplikacja nie ma wpływu lub minimalny wpływ z powodu tych uaktualnień, uaktualnienia są wykonywane w następujących fazach:

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>Faza 1: Uaktualnienie jest wykonywane przy użyciu wszystkich zasad kondycji klastra

Podczas tej fazy uaktualnienia przejść jedną domenę uaktualnienia naraz i aplikacje, które były uruchomione w klastrze nadal działać bez żadnych przestojów. Zasady kondycji klastra (kondycji węzła i kondycji aplikacji) są przestrzegane podczas uaktualniania.

Jeśli zasady kondycji klastra nie są spełnione, uaktualnienie jest przywracane i wiadomość e-mail jest wysyłana do właściciela subskrypcji. Wiadomość e-mail zawiera następujące informacje:

* Powiadomienie, że musieliśmy wycofać uaktualnienie klastra.
* Sugerowane działania naprawcze, jeśli istnieją.
* Liczba dni (*n*) do czasu wykonania fazy 2.

Staramy się wykonać to samo uaktualnienie jeszcze kilka razy w przypadku, gdy wszelkie uaktualnienia nie powiodło się ze względu na infrastrukturę. Po *n* dniach od daty wysłania wiadomości e-mail przechodzimy do fazy 2.

Jeśli zasady kondycji klastra są spełnione, uaktualnienie jest uważane za pomyślne i oznaczone jako zakończone. Może się to zdarzyć podczas wstępnego uaktualniania lub dowolnej z ponownych premier uaktualnienia w tej fazie. Nie ma potwierdzenia e-mail udanego przebiegu. Ma to na celu uniknięcie wysyłania zbyt wielu wiadomości e-mail; otrzymywanie wiadomości e-mail powinno być postrzegane jako wyjątek od normalnego. Oczekujemy, że większość uaktualnień klastra zakończy się pomyślnie bez wpływu na dostępność aplikacji.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>Faza 2: Uaktualnienie jest wykonywane przy użyciu domyślnych zasad kondycji

Zasady kondycji w tej fazie są ustawione w taki sposób, że liczba aplikacji, które były w dobrej kondycji na początku uaktualnienia pozostaje taka sama przez cały czas trwania procesu uaktualniania. Podobnie jak w fazie 1, uaktualnienia fazy 2 przebiegają po jednej domenie uaktualnienia naraz, a aplikacje uruchomione w klastrze nadal działają bez żadnych przestojów. Zasady kondycji klastra (kombinacja kondycji węzła i kondycji wszystkich aplikacji uruchomionych w klastrze) są przestrzegane przez czas trwania uaktualnienia.

Jeśli zasady kondycji klastra w efekcie nie są spełnione, uaktualnienie jest przywracane. Następnie wiadomość e-mail jest wysyłana do właściciela subskrypcji. Wiadomość e-mail zawiera następujące informacje:

* Powiadomienie, że musieliśmy wycofać uaktualnienie klastra.
* Sugerowane działania naprawcze, jeśli istnieją.
* Liczba dni (n) do czasu wykonania fazy 3.

Staramy się wykonać to samo uaktualnienie jeszcze kilka razy w przypadku, gdy wszelkie uaktualnienia nie powiodło się ze względu na infrastrukturę. Wiadomość e-mail z przypomnieniem jest wysyłana na kilka dni przed n dniami. Po n dniach od daty wysłania wiadomości e-mail przechodzimy do fazy 3. E-maile, które wysyłamy w fazie 2, muszą być traktowane poważnie i należy podjąć działania naprawcze.

Jeśli zasady kondycji klastra są spełnione, uaktualnienie jest uważane za pomyślne i oznaczone jako zakończone. Może się to zdarzyć podczas wstępnego uaktualniania lub dowolnej z ponownych premier uaktualnienia w tej fazie. Nie ma potwierdzenia e-mail udanego przebiegu.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>Faza 3: Uaktualnienie jest wykonywane przy użyciu agresywnych zasad dotyczących zdrowia

Te zasady dotyczące kondycji w tej fazie są ukierunkowane na ukończenie uaktualnienia, a nie kondycję aplikacji. W tej fazie kończy się kilka uaktualnień klastra. Jeśli klaster zostanie do tej fazy, istnieje duża szansa, że aplikacja staje się w złej kondycji i/lub utraty dostępności.

Podobnie jak w przypadku pozostałych dwóch faz, uaktualnienia fazy 3 przebiegają po jednej domenie uaktualnienia naraz.

Jeśli zasady kondycji klastra nie są spełnione, uaktualnienie jest przywracane. Staramy się wykonać to samo uaktualnienie jeszcze kilka razy w przypadku, gdy wszelkie uaktualnienia nie powiodło się ze względu na infrastrukturę. Następnie klaster jest przypięty, dzięki czemu nie będzie już otrzymywać pomocy technicznej i/lub uaktualnień.

Wiadomość e-mail z tymi informacjami jest wysyłana do właściciela subskrypcji wraz z akcjami zaradczymi. Nie oczekujemy, że klastry, aby dostać się do stanu, w którym faza 3 nie powiodło się.

Jeśli zasady kondycji klastra są spełnione, uaktualnienie jest uważane za pomyślne i oznaczone jako zakończone. Może się to zdarzyć podczas wstępnego uaktualniania lub dowolnej z ponownych premier uaktualnienia w tej fazie. Nie ma potwierdzenia e-mail udanego przebiegu.

## <a name="manage-certificates"></a>Zarządzanie certyfikatami

Sieć szkieletowa usług używa [certyfikatów serwera X.509](service-fabric-cluster-security.md) określonych podczas tworzenia klastra w celu zabezpieczenia komunikacji między węzłami klastra i uwierzytelnienia klientów. Można dodawać, aktualizować lub usuwać certyfikaty dla klastra i klienta w [witrynie Azure portal](https://portal.azure.com) lub przy użyciu interfejsu wiersza polecenia programu PowerShell/Azure.  Aby dowiedzieć się więcej, przeczytaj [artykuł dodaj lub usuń certyfikaty](service-fabric-cluster-security-update-certs-azure.md)

## <a name="open-application-ports"></a>Otwieranie portów aplikacji

Porty aplikacji można zmienić, zmieniając właściwości zasobu modułu równoważenia obciążenia skojarzone z typem węzła. Można użyć witryny Azure portal lub można użyć powershell/Azure CLI. Aby uzyskać więcej informacji, zobacz [Otwieranie portów aplikacji dla klastra](create-load-balancer-rule.md).

## <a name="define-node-properties"></a>Definiowanie właściwości węzła

Czasami można upewnić się, że niektóre obciążenia są uruchamiane tylko na niektórych typach węzłów w klastrze. Na przykład niektóre obciążenia mogą wymagać procesorów GPU lub dysków SSD, podczas gdy inne nie. Dla każdego z typów węzłów w klastrze można dodać niestandardowe właściwości węzłów do węzłów klastra. Ograniczenia umieszczania są instrukcje dołączone do poszczególnych usług, które wybierają dla jednego lub więcej właściwości węzła. Ograniczenia umieszczania określają, gdzie usługi powinny być uruchamiane.

Aby uzyskać szczegółowe informacje na temat stosowania ograniczeń umieszczania, właściwości węzła i sposobu ich definiowania, przeczytaj [właściwości węzła i ograniczenia umieszczania](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

## <a name="add-capacity-metrics"></a>Dodawanie wskaźników pojemności

Dla każdego z typów węzłów można dodać niestandardowe metryki pojemności, których chcesz użyć w aplikacjach do raportowania obciążenia. Szczegółowe informacje na temat używania metryk pojemności do raportowania obciążenia można znaleźć w programie Dokumenty Menedżera zasobów klastra sieci szkieletowej usług w sprawie [opisu klastra](service-fabric-cluster-resource-manager-cluster-description.md) i [metryk oraz ładowania](service-fabric-cluster-resource-manager-metrics.md).

## <a name="set-health-policies-for-automatic-upgrades"></a>Ustawianie zasad kondycji dla automatycznych uaktualnień

Można określić niestandardowe zasady kondycji dla uaktualnienia sieci szkieletowej. Jeśli klaster został ustawiony na Automatyczne uaktualnienia sieci szkieletowej, te zasady zostaną zastosowane do fazy 1 automatycznych uaktualnień sieci szkieletowej.
Jeśli ustawiono klaster dla ręcznych uaktualnień sieci szkieletowej, te zasady są stosowane za każdym razem, gdy wybierzesz nową wersję wyzwalającą system, aby rozpocząć uaktualnianie sieci szkieletowej w klastrze. Jeśli zasady nie zostaną zastąpione, zostaną użyte wartości domyślne.

Można określić niestandardowe zasady kondycji lub przejrzeć bieżące ustawienia w bloku "uaktualnianie sieci szkieletowej", wybierając zaawansowane ustawienia uaktualnienia. Przejrzyj poniższy obrazek, jak to zrobić.

![Zarządzanie niestandardowymi zasadami kondycji][HealthPolices]

## <a name="customize-fabric-settings-for-your-cluster"></a>Dostosowywanie ustawień sieci szkieletowej dla klastra

Wiele różnych ustawień konfiguracji można dostosować w klastrze, takich jak poziom niezawodności właściwości klastra i węzła. Aby uzyskać więcej informacji, zobacz [Ustawienia sieci szkieletowej klastra sieci szkieletowej sieci szkieletowej usług](service-fabric-cluster-fabric-settings.md).

## <a name="patch-the-os-in-the-cluster-nodes"></a>Poprawianie systemu operacyjnego w węzłach klastra

Aplikacja aranżacji poprawek (POA) to aplikacja sieci szkieletowej usług, która automatyzuje łatanie systemu operacyjnego w klastrze sieci szkieletowej usług bez przestojów. [Aplikacja aranżacji poprawek dla systemu Windows](service-fabric-patch-orchestration-application.md) można wdrożyć w klastrze, aby zainstalować poprawki w sposób zorganizowany, przy jednoczesnym zachowaniu usług dostępnych przez cały czas.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak dostosować niektóre [ustawienia sieci szkieletowej klastra sieci szkieletowej usług](service-fabric-cluster-fabric-settings.md)
* Dowiedz się, jak [skalować klaster w](service-fabric-cluster-scale-up-down.md)
* Dowiedz się więcej o [uaktualnieniach aplikacji](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
