---
title: Uaktualnianie klastra usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: Więcej informacji na temat uaktualniania wersji lub konfiguracji klastra usługi Azure Service Fabric.  W tym artykule opisano ustawienia klastra tryb aktualizacji uaktualnianie certyfikaty, dodawanie portów aplikacji, wykonując poprawek systemu operacyjnego i czego mogą oczekiwać podczas uaktualnienia są wykonywane
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 15190ace-31ed-491f-a54b-b5ff61e718db
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2018
ms.author: aljo
ms.openlocfilehash: 8fa461d8c3a70d4b0d2d9973a840ffc7d1ff6470
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65472763"
---
# <a name="upgrading-and-updating-an-azure-service-fabric-cluster"></a>Uaktualnianie i aktualizowanie klastra usługi Azure Service Fabric

Dla każdego nowoczesnego systemu projektowanie pod kątem możliwość jest kluczem do osiągnięcia długoterminowym sukcesie produktu. Klaster usługi Azure Service Fabric jest zasobem, który właścicielem, ale jest częściowo zarządzany przez firmę Microsoft. W tym artykule opisano, co jest zarządzana automatycznie i samodzielnie skonfigurować.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Kontrolowanie wersja sieci szkieletowej, która działa w klastrze

Upewnij się zachować klaster [obsługiwana wersja sieci szkieletowej](service-fabric-versions.md) zawsze. Gdy firma Microsoft ogłaszamy wydanie nowej wersji usługi Service fabric, poprzednia wersja jest oznaczony do zakończenia wsparcia po co najmniej 60 dni od tego dnia. Nowe wersje są ogłaszane na blogu zespołu usługi Service fabric. Nowa wersja jest dostępna dla wybrany.

14 dni przed wygaśnięciem wersji, którą klaster działa, zdarzenie kondycji jest generowany tak, że przełączenie klastra w wskazuje ostrzegawczy stan kondycji. Klaster będzie pozostawał w stanie ostrzeżenia, dopóki nie można uaktualnić do wersji obsługiwanej sieci szkieletowej.

Można ustawić do klastra, aby otrzymywać uaktualnienia sieci szkieletowej automatyczne, zgodnie z ich wydaniu przez firmę Microsoft lub możesz wybrać wersję obsługiwanych w sieci szkieletowej, że chcesz użyć do klastra, aby znajdować się na.  Aby dowiedzieć się więcej, przeczytaj [uaktualniania wersji usługi Service Fabric klastra](service-fabric-cluster-upgrade-version-azure.md).

## <a name="fabric-upgrade-behavior-during-automatic-upgrades"></a>Sieć szkieletowa uaktualniać zachowanie podczas automatycznych uaktualnień
Firma Microsoft udostępnia kodu sieci szkieletowej i konfiguracji, która działa w klastrze platformy Azure. Wykonamy automatycznych uaktualnień monitorowanych oprogramowania na zgodnie z potrzebami. Te uaktualnienia może być kod i/lub konfiguracji. Aby upewnić się, że aplikacja niska bez wpływu i minimalne wpływu na te uaktualnienia, możemy przeprowadzić uaktualnienia w następujących faz:

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>Faza 1: Uaktualnianie jest przeprowadzane przy użyciu wszystkich zasad dotyczących kondycji klastra
W tej fazie uaktualnienia Przejdź jedną domenę uaktualnienia w danym momencie, a aplikacje, które były uruchomione w klastrze nadal działać bez żadnych przestojów. Zasady dotyczące kondycji klastra (kombinację kondycji węzła i kondycji wszystkich aplikacji uruchomionych w klastrze) są przestrzegane podczas uaktualniania.

Jeśli zasady dotyczące kondycji klastra nie są spełnione, uaktualnienie zostanie wycofana. Następnie zostanie wysłana wiadomość e-mail do właściciela subskrypcji. Wiadomość e-mail zawiera następujące informacje:

* Powiadomienie o Musieliśmy wycofać uaktualniania klastra.
* Zalecane czynności zaradczych, jeśli istnieje.
* Liczba dni (n), dopóki nie możemy wykonać fazy 2.

Będziemy próbować wykonać uaktualnienie samej kilka razy w przypadku uaktualnienia nie powiodła się ze względów infrastruktury. Po n dni od daty wysłania wiadomości e-mail możemy przejść do sekcji Faza 2.

Jeśli zasady dotyczące kondycji klastra są spełnione, uaktualnienie jest uznawany za pomyślny i oznaczone jako ukończone. Może to nastąpić podczas uaktualniania początkowej lub dowolne powtórkami uaktualniania na tym etapie. Nie ma żadnych e-mail z potwierdzeniem pomyślnego przebiegu. To jest unikać wysyłania, że możesz zbyt dużo żądań wiadomości e-mail — otrzymania wiadomości e-mail powinno być postrzegane jako wyjątek do normalnego. Oczekujemy, że większość Uaktualnianie klastra została wykonana pomyślnie, bez wywierania wpływu na dostępność Twojej aplikacji.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>Faza 2: Uaktualnianie jest przeprowadzane przy użyciu tylko zasady dotyczące kondycji domyślne
Zasady dotyczące kondycji, w tej fazie są ustawiane w taki sposób, aby wiele aplikacji, będące w dobrej kondycji na początku uaktualnienia pozostała taka sama na czas trwania procesu uaktualniania. Tak jak w fazie 1 uaktualnień fazy 2 Przejdź jedną domenę uaktualnienia w danym momencie, a aplikacje, które były uruchomione w klastrze nadal działać bez żadnych przestojów. Zasady dotyczące kondycji klastra (kombinację kondycji węzła i kondycji wszystkich aplikacji uruchomionych w klastrze) są przestrzegane na czas trwania uaktualniania.

Jeśli zasady dotyczące kondycji klastra w praktyce nie są spełnione, uaktualnienie zostanie wycofana. Następnie zostanie wysłana wiadomość e-mail do właściciela subskrypcji. Wiadomość e-mail zawiera następujące informacje:

* Powiadomienie o Musieliśmy wycofać uaktualniania klastra.
* Zalecane czynności zaradczych, jeśli istnieje.
* Liczba dni (n), dopóki nie możemy wykonać fazy 3.

Będziemy próbować wykonać uaktualnienie samej kilka razy w przypadku uaktualnienia nie powiodła się ze względów infrastruktury. Wiadomość e-mail z przypomnieniem są wysyłane na kilka dni, po którym n dni są włączone. Po n dni od daty wysłania wiadomości e-mail możemy przejść do fazy 3. Wiadomości e-mail, które możemy wysłać w sekcji Faza 2, które muszą zostać podjęte naszych użytkowników bardzo poważnie i należy podjąć akcje naprawcze.

Jeśli zasady dotyczące kondycji klastra są spełnione, uaktualnienie jest uznawany za pomyślny i oznaczone jako ukończone. Może to nastąpić podczas uaktualniania początkowej lub dowolne powtórkami uaktualniania na tym etapie. Nie ma żadnych e-mail z potwierdzeniem pomyślnego przebiegu.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>Faza 3: Uaktualnianie jest przeprowadzane przy użyciu zasady dotyczące kondycji agresywne
Te zasady dotyczące kondycji, w tej fazie są przeznaczone dla ukończenie uaktualnienia, a nie kondycji aplikacji. Uaktualnianie klastra kilka znajdą się w tej fazie. Jeśli na tym etapie klaster, istnieje duże prawdopodobieństwo, że aplikacja staje się nieprawidłowy, i/lub utratę dostępności.

Podobnie jak w pozostałych faz, fazy 3 uaktualnienia Przejdź jedną domenę uaktualnienia w danym momencie.

Jeśli zasady dotyczące kondycji klastra nie są spełnione, uaktualnienie zostanie wycofana. Będziemy próbować wykonać uaktualnienie samej kilka razy w przypadku uaktualnienia nie powiodła się ze względów infrastruktury. Po utworzeniu tego klastra jest przypięty, aby go nie będziesz już otrzymywać pomocy technicznej i/lub uaktualnienia.

Wiadomość e-mail z te informacje są wysyłane do właściciela subskrypcji, oraz czynności zaradczych. Nie oczekujemy, że wszystkie klastry, aby znaleźć się w stanie, w którym fazy 3 nie powiodło się.

Jeśli zasady dotyczące kondycji klastra są spełnione, uaktualnienie jest uznawany za pomyślny i oznaczone jako ukończone. Może to nastąpić podczas uaktualniania początkowej lub dowolne powtórkami uaktualniania na tym etapie. Nie ma żadnych e-mail z potwierdzeniem pomyślnego przebiegu.

## <a name="manage-certificates"></a>Zarządzanie certyfikatami
Usługa Service Fabric używa [serwera certyfikatu x.509](service-fabric-cluster-security.md) określić podczas tworzenia klastra do zabezpieczania komunikacji między węzłami klastra i uwierzytelniania klientów. Dodawanie, aktualizowanie lub usuwanie certyfikatów klastra i klienta w [witryny Azure portal](https://portal.azure.com) lub przy użyciu interfejsu wiersza polecenia programu PowerShell/Azure.  Aby dowiedzieć się więcej, przeczytaj [Dodawanie lub usuwanie certyfikatów](service-fabric-cluster-security-update-certs-azure.md)

## <a name="open-application-ports"></a>Porty otwartych aplikacji
Porty aplikacji można zmienić, zmieniając właściwości zasobu modułu równoważenia obciążenia, które są skojarzone z typem węzła. Można użyć witryny Azure portal, lub można użyć interfejsu wiersza polecenia programu PowerShell/Azure. Aby uzyskać więcej informacji, przeczytaj [Otwórz porty aplikacji dla klastra](create-load-balancer-rule.md).

## <a name="define-node-properties"></a>Zdefiniuj właściwości węzła
Czasami możesz chcieć upewnij się, że niektóre obciążenia uruchamiane tylko na niektóre rodzaje węzłów w klastrze. Na przykład niektóre obciążenia mogą wymagać procesorów GPU lub dyski SSD, podczas gdy inne nie. Dla każdego z typów węzłów w klastrze można dodać właściwości niestandardowe węzła do węzłów klastra. Ograniczeniami dotyczącymi umieszczania są instrukcje dołączone do poszczególnych usług, które wybierz co najmniej jedną właściwość węzła. Ograniczeniami dotyczącymi umieszczania definiują, gdzie należy uruchamiać usług.

Szczegółowe informacje dotyczące korzystania z ograniczeniami dotyczącymi umieszczania, właściwości węzła i sposób definiowania ich można odczytać [— właściwości węzła i ograniczeniami dotyczącymi umieszczania](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

## <a name="add-capacity-metrics"></a>Dodawanie metryki pojemności
Dla każdego typu węzła możesz dodać niestandardowe metryki pojemności, którą chcesz używać w aplikacjach w celu raportowania obciążenia. Szczegółowe informacje dotyczące użycia metryki pojemności w celu raportowania obciążenia można odwoływać się do dokumentów, Menedżer zasobów klastra usługi w sieci szkieletowej na [opisujący Twój klaster](service-fabric-cluster-resource-manager-cluster-description.md) i [metryki i obciążenia](service-fabric-cluster-resource-manager-metrics.md).

## <a name="set-health-policies-for-automatic-upgrades"></a>Ustaw zasady dotyczące kondycji automatycznych uaktualnień
Można określić zasady dotyczące kondycji niestandardowych do uaktualnienia sieci szkieletowej. Jeśli ustawisz klastra do sieci szkieletowej automatycznych uaktualnień, następnie te zasady stosowane do fazy 1 uaktualnienia automatyczne sieci szkieletowej.
Jeśli ustawiono klastra dla sieci szkieletowej ręcznego uaktualnienia, te zasady zastosowana podczas wybierania nowej wersji, wyzwalając systemu, aby uruchamiał uaktualnienia sieci szkieletowej w klastrze. Jeśli nie zastąpisz zasady, są używane wartości domyślne.

Można określić zasady dotyczące kondycji niestandardowych lub Przejrzyj bieżące ustawienia w bloku "uaktualnienia sieci szkieletowej", wybierając pozycję Zaawansowane ustawienia uaktualniania. Przejrzyj następujący obraz na temat. 

![Zarządzanie zasadami kondycji niestandardowe][HealthPolices]

## <a name="customize-fabric-settings-for-your-cluster"></a>Dostosowywanie ustawień sieci szkieletowej dla klastra
Wiele różnych ustawień konfiguracji można dostosować w taki sposób, w klastrze, takich jak poziom niezawodności właściwości klastra i węzła. Aby uzyskać więcej informacji, przeczytaj [ustawień sieci szkieletowej klastra usługi Service Fabric](service-fabric-cluster-fabric-settings.md).

## <a name="patch-the-os-in-the-cluster-nodes"></a>Stosowanie poprawek systemu operacyjnego w węzłach klastra
Aplikacja orchestration patch (POA) jest aplikacja usługi Service Fabric, który automatyzuje systemu operacyjnego poprawek w klastrze usługi Service Fabric, bez przestojów. [Patch Orchestration Application dla Windows](service-fabric-patch-orchestration-application.md) można wdrożyć w klastrze do instalowania poprawek w zorganizowany sposób przy jednoczesnym zachowaniu usług dostępnych przez cały czas.


## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak dostosować niektóre [usługi ustawień sieci szkieletowej klastra Service fabric](service-fabric-cluster-fabric-settings.md)
* Dowiedz się, jak [skalowanie klastra w poziomie i pionie](service-fabric-cluster-scale-up-down.md)
* Dowiedz się więcej o [uaktualnień aplikacji](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
