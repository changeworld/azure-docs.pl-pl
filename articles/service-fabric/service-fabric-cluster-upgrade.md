---
title: Uaktualnianie klastra usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: Uaktualnienie usługi Service Fabric kodu i/lub konfiguracji, która działa w klastrze usługi Service Fabric, włącznie z ustawieniem trybu aktualizacji klastra, uaktualnianie certyfikaty, dodawanie portów aplikacji, wykonując poprawek systemu operacyjnego i tak dalej. Czego możesz oczekiwać, gdy wykonywane są uaktualnienia?
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: 15190ace-31ed-491f-a54b-b5ff61e718db
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/10/2017
ms.author: aljo
ms.openlocfilehash: 2fd62f8709bddfd981f4b1358c97d0acbaf7f12d
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2018
ms.locfileid: "48269107"
---
# <a name="upgrade-an-azure-service-fabric-cluster"></a>Uaktualnianie klastra usługi Azure Service Fabric
> [!div class="op_single_selector"]
> * [Klaster usługi Azure](service-fabric-cluster-upgrade.md)
> * [Klaster autonomiczny](service-fabric-cluster-upgrade-windows-server.md)
> 
> 

Dla każdego nowoczesnego systemu projektowanie pod kątem możliwość jest kluczem do osiągnięcia długoterminowym sukcesie produktu. Klaster usługi Azure Service Fabric jest zasobem, który właścicielem, ale jest częściowo zarządzany przez firmę Microsoft. W tym artykule opisano, co jest zarządzana automatycznie i samodzielnie skonfigurować.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Kontrolowanie wersja sieci szkieletowej, która działa w klastrze
Można ustawić do klastra, aby otrzymywać uaktualnienia sieci szkieletowej automatyczne, zgodnie z ich wydaniu przez firmę Microsoft lub możesz wybrać wersję obsługiwanych w sieci szkieletowej, że chcesz użyć do klastra, aby znajdować się na.

Można to zrobić, ustawienia konfiguracji klastra "element upgradeMode" w portalu lub przy użyciu usługi Resource Manager w momencie utworzenia lub nowszych na działającym klastrem 

> [!NOTE]
> Upewnij się, że utrzymywania klastra usługi zawsze uruchomiona wersja obsługiwana w sieci szkieletowej. Gdy firma Microsoft ogłaszamy wydanie nowej wersji usługi Service fabric, poprzednia wersja jest oznaczony do zakończenia wsparcia po co najmniej 60 dni od tego dnia. Nowe wersje są anonsowane [na blogu zespołu usługi Service fabric](https://blogs.msdn.microsoft.com/azureservicefabric/). Nowa wersja jest dostępna dla wybrany. 
> 
> 

14 dni przed wygaśnięciem wersji, którą klaster działa, zdarzenie kondycji jest generowany tak, że przełączenie klastra w wskazuje ostrzegawczy stan kondycji. Klaster będzie pozostawał w stanie ostrzeżenia, dopóki nie można uaktualnić do wersji obsługiwanej sieci szkieletowej.

### <a name="setting-the-upgrade-mode-via-portal"></a>Ustawienie Tryb uaktualniania za pośrednictwem portalu
Podczas tworzenia klastra, można ustawić automatyczne lub ręczne klastra.

![Create_Manualmode][Create_Manualmode]

Można ustawić klastra automatyczne lub ręczne, gdy w klastrze na żywo, za pomocą środowiska zarządzania. 

#### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>Uaktualnianie do nowej wersji w klastrze, który jest ustawiany w trybie ręcznej za pośrednictwem portalu.
Aby przeprowadzić uaktualnienie do nowej wersji, wszystko, co należy zrobić to wybierz dostępnej wersji z listy rozwijanej, a następnie zapisz. Uaktualnienia sieci szkieletowej pobiera rozpoczęła się automatycznie. Zasady dotyczące kondycji klastra (kombinację kondycji węzła i kondycji wszystkich aplikacji uruchomionych w klastrze) są przestrzegane podczas uaktualniania.

Jeśli zasady dotyczące kondycji klastra nie są spełnione, uaktualnienie zostanie wycofana. Przewiń w dół tego dokumentu, aby dowiedzieć się więcej na temat ustawiania tych zasad niestandardowych kondycji. 

Po rozwiązaniu problemów, które spowodowało wycofywania, musisz zainicjować aktualizację ponownie, wykonując te same kroki co wcześniej.

![Manage_Automaticmode][Manage_Automaticmode]

### <a name="setting-the-upgrade-mode-via-a-resource-manager-template"></a>Ustawienie Tryb uaktualniania za pomocą szablonu usługi Resource Manager
Dodaj konfigurację "element upgradeMode" w definicji zasobu Microsoft.ServiceFabric/clusters i ustaw opcję "clusterCodeVersion" na jedną z wersji obsługiwanych w sieci szkieletowej, jak pokazano poniżej, a następnie wdrożyć szablon. Prawidłowe wartości dla "element upgradeMode" to "Ręczny" lub "Automatyczny"

![ARMUpgradeMode][ARMUpgradeMode]

#### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>Uaktualnianie do nowej wersji w klastrze, który jest ustawiany w trybie ręcznie za pomocą szablonu usługi Resource Manager.
Gdy klaster jest w trybie ręcznej, aby uaktualnić do nowej wersji Zmień "clusterCodeVersion" do obsługiwanej wersji i wdrożyć ją. Wdrożenie szablonu kopnięć uaktualnienia sieci szkieletowej pobiera rozpoczęła się automatycznie. Zasady dotyczące kondycji klastra (kombinację kondycji węzła i kondycji wszystkich aplikacji uruchomionych w klastrze) są przestrzegane podczas uaktualniania.

Jeśli zasady dotyczące kondycji klastra nie są spełnione, uaktualnienie zostanie wycofana. Przewiń w dół tego dokumentu, aby dowiedzieć się więcej na temat ustawiania tych zasad niestandardowych kondycji. 

Po rozwiązaniu problemów, które spowodowało wycofywania, musisz zainicjować aktualizację ponownie, wykonując te same kroki co wcześniej.

### <a name="get-list-of-all-available-version-for-all-environments-for-a-given-subscription"></a>Pobierz listę wszystkich dostępnych wersji dla wszystkich środowisk w ramach danej subskrypcji
Uruchom następujące polecenie, a powinna pojawić się dane wyjściowe podobne do poniższego.

"supportExpiryUtc" informuje, kiedy Twoja danego wydania wygaśnie lub utracił ważność. Najnowsza wersja ma prawidłową datę — ma wartość "9999-12-31T23:59:59.9999999", po prostu oznacza to, że data wygaśnięcia nie jest jeszcze ustawiony.

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/locations/{{location}}/clusterVersions?api-version=2016-09-01

Example: https://management.azure.com/subscriptions/1857f442-3bce-4b96-ad95-627f76437a67/providers/Microsoft.ServiceFabric/locations/eastus/clusterVersions?api-version=2016-09-01

Output:
{
                  "value": [
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
                      "name": "5.0.1427.9490",
                      "type": "Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.0.1427.9490",
                        "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
                      "name": "5.1.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.1.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
                      "name": "4.4.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "4.4.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Linux"
                      }
                    }
                  ]
                }


```

## <a name="fabric-upgrade-behavior-when-the-cluster-upgrade-mode-is-automatic"></a>Zachowanie uaktualnienia sieci szkieletowej, gdy tryb uaktualniania klastra jest automatyczny
Firma Microsoft udostępnia kodu sieci szkieletowej i konfiguracji, która działa w klastrze platformy Azure. Wykonamy automatycznych uaktualnień monitorowanych oprogramowania na zgodnie z potrzebami. Te uaktualnienia może być kod i/lub konfiguracji. Aby upewnić się, że aplikacja niska bez wpływu i minimalne wpływu na te uaktualnienia, możemy przeprowadzić uaktualnienia w następujących faz:

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>Faza 1: Uaktualnianie jest przeprowadzane przy użyciu wszystkich zasad dotyczących kondycji klastra
W tej fazie uaktualnienia Przejdź jedną domenę uaktualnienia w danym momencie, a aplikacje, które były uruchomione w klastrze nadal działać bez żadnych przestojów. Zasady dotyczące kondycji klastra (kombinację kondycji węzła i kondycji wszystkich aplikacji uruchomionych w klastrze) są przestrzegane podczas uaktualniania.

Jeśli zasady dotyczące kondycji klastra nie są spełnione, uaktualnienie zostanie wycofana. Następnie zostanie wysłana wiadomość e-mail do właściciela subskrypcji. Wiadomość e-mail zawiera następujące informacje:

* Powiadomienie o Musieliśmy wycofać uaktualniania klastra.
* Zalecane czynności zaradczych, jeśli istnieje.
* Liczba dni (n), dopóki nie możemy wykonać fazy 2.

Będziemy próbować wykonać uaktualnienie samej kilka razy w przypadku uaktualnienia nie powiodła się ze względów infrastruktury. Po n dni od daty wysłania wiadomości e-mail możemy przejść do sekcji Faza 2.

Jeśli zasady dotyczące kondycji klastra są spełnione, uaktualnienie jest uznawany za pomyślny i oznaczone jako ukończone. Może to nastąpić podczas uaktualniania początkowej lub dowolne powtórkami uaktualniania na tym etapie. Nie ma żadnych e-mail z potwierdzeniem pomyślnego przebiegu. To jest unikać wysyłania, że możesz zbyt dużo żądań wiadomości e-mail — otrzymania wiadomości e-mail powinno być postrzegane jako wyjątek do normalnego. Oczekujemy, że większość Uaktualnianie klastra została wykonana pomyślnie, bez wywierania wpływu na dostępność Twojej aplikacji.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>Faza 2: Uaktualnienie odbywa się przy użyciu domyślnego tylko zasad dotyczących kondycji
Zasady dotyczące kondycji, w tej fazie są ustawiane w taki sposób, aby wiele aplikacji, będące w dobrej kondycji na początku uaktualnienia pozostała taka sama na czas trwania procesu uaktualniania. Tak jak w fazie 1 uaktualnień fazy 2 Przejdź jedną domenę uaktualnienia w danym momencie, a aplikacje, które były uruchomione w klastrze nadal działać bez żadnych przestojów. Zasady dotyczące kondycji klastra (kombinację kondycji węzła i kondycji wszystkich aplikacji uruchomionych w klastrze) są przestrzegane na czas trwania uaktualniania.

Jeśli zasady dotyczące kondycji klastra w praktyce nie są spełnione, uaktualnienie zostanie wycofana. Następnie zostanie wysłana wiadomość e-mail do właściciela subskrypcji. Wiadomość e-mail zawiera następujące informacje:

* Powiadomienie o Musieliśmy wycofać uaktualniania klastra.
* Zalecane czynności zaradczych, jeśli istnieje.
* Liczba dni (n), dopóki nie możemy wykonać fazy 3.

Będziemy próbować wykonać uaktualnienie samej kilka razy w przypadku uaktualnienia nie powiodła się ze względów infrastruktury. Wiadomość e-mail z przypomnieniem są wysyłane na kilka dni, po którym n dni są włączone. Po n dni od daty wysłania wiadomości e-mail możemy przejść do fazy 3. Wiadomości e-mail, które możemy wysłać w sekcji Faza 2, które muszą zostać podjęte naszych użytkowników bardzo poważnie i należy podjąć akcje naprawcze.

Jeśli zasady dotyczące kondycji klastra są spełnione, uaktualnienie jest uznawany za pomyślny i oznaczone jako ukończone. Może to nastąpić podczas uaktualniania początkowej lub dowolne powtórkami uaktualniania na tym etapie. Nie ma żadnych e-mail z potwierdzeniem pomyślnego przebiegu.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>Faza 3 — Uaktualnianie jest przeprowadzane przy użyciu zasady dotyczące kondycji agresywne
Te zasady dotyczące kondycji, w tej fazie są przeznaczone dla ukończenie uaktualnienia, a nie kondycji aplikacji. Uaktualnianie klastra bardzo mało znajdą się w tej fazie. Jeśli na tym etapie klaster, istnieje duże prawdopodobieństwo, że aplikacja staje się nieprawidłowy, i/lub utratę dostępności.

Podobnie jak w pozostałych faz, fazy 3 uaktualnienia Przejdź jedną domenę uaktualnienia w danym momencie.

Jeśli zasady dotyczące kondycji klastra nie są spełnione, uaktualnienie zostanie wycofana. Będziemy próbować wykonać uaktualnienie samej kilka razy w przypadku uaktualnienia nie powiodła się ze względów infrastruktury. Po utworzeniu tego klastra jest przypięty, aby go nie będziesz już otrzymywać pomocy technicznej i/lub uaktualnienia.

Wiadomość e-mail z te informacje są wysyłane do właściciela subskrypcji, oraz czynności zaradczych. Nie oczekujemy, że wszystkie klastry, aby znaleźć się w stanie, w którym fazy 3 nie powiodło się.

Jeśli zasady dotyczące kondycji klastra są spełnione, uaktualnienie jest uznawany za pomyślny i oznaczone jako ukończone. Może to nastąpić podczas uaktualniania początkowej lub dowolne powtórkami uaktualniania na tym etapie. Nie ma żadnych e-mail z potwierdzeniem pomyślnego przebiegu.

## <a name="cluster-configurations-that-you-control"></a>Konfiguracje klastra, które możesz kontrolować
Oprócz możliwości ustawiania tożsamości klastra w tryb uaktualniania, Oto konfiguracje, które mogą być zmieniane na działającym klastrem.

### <a name="certificates"></a>Certyfikaty
Można dodać nowe lub łatwo usunąć certyfikaty dla klastra i klientów za pośrednictwem portalu. Zapoznaj się [tego dokumentu, aby uzyskać szczegółowe instrukcje](service-fabric-cluster-security-update-certs-azure.md)

![Zrzut ekranu pokazujący odciski palców certyfikatu w witrynie Azure portal.][CertificateUpgrade]

### <a name="application-ports"></a>Porty aplikacji
Porty aplikacji można zmienić, zmieniając właściwości zasobu modułu równoważenia obciążenia, które są skojarzone z typem węzła. Mogą korzystać z portalu lub programu PowerShell usługi Resource Manager można używać bezpośrednio.

Aby otworzyć nowy port na wszystkich maszynach wirtualnych w typ węzła, wykonaj następujące czynności:

1. Dodaj nową sondę do odpowiedniej usługi równoważenia obciążenia.
   
    Jeśli wdrożono klaster przy użyciu portalu, moduły równoważenia obciążenia są nazywane "LB Nazwa grupy zasobów — NodeTypename", jeden dla każdego typu węzła. Ponieważ nazwy modułu równoważenia obciążenia są unikatowe tylko w obrębie grupy zasobów, najlepiej w przypadku wyszukiwania ich w określonej grupie zasobów.
   
    ![Zrzut ekranu przedstawiający dodawanie sondy do modułu równoważenia obciążenia w portalu.][AddingProbes]
2. Dodaj nową regułę modułu równoważenia obciążenia.
   
    Dodaj nową regułę do tego samego modułu równoważenia obciążenia za pomocą sondowania, który został utworzony w poprzednim kroku.
   
    ![Dodawanie nowej reguły do modułu równoważenia obciążenia w portalu.][AddingLBRules]

### <a name="placement-properties"></a>Właściwości umieszczania
Dla każdego typu węzła możesz dodać niestandardowe właściwości umieszczania, które chcesz użyć w swoich aplikacjach. Element NodeType to właściwość domyślną, która może korzystać bez dodawania go jawnie.

> [!NOTE]
> Aby uzyskać szczegółowe informacje związane z użyciem ograniczeniami dotyczącymi umieszczania, właściwości węzła i sposób definiowania ich, można znaleźć w sekcji "Ograniczenia i węzła właściwości umieszczania" w dokumencie Menedżer zasobów klastra usługi w sieci szkieletowej na [opisujące klastra](service-fabric-cluster-resource-manager-cluster-description.md).
> 
> 

### <a name="capacity-metrics"></a>Metryki wydajności
Dla każdego typu węzła możesz dodać niestandardowe metryki pojemności, którą chcesz używać w aplikacjach w celu raportowania obciążenia. Szczegółowe informacje dotyczące użycia metryki pojemności w celu raportowania obciążenia można odwoływać się do dokumentów, Menedżer zasobów klastra usługi w sieci szkieletowej na [opisujący Twój klaster](service-fabric-cluster-resource-manager-cluster-description.md) i [metryki i obciążenia](service-fabric-cluster-resource-manager-metrics.md).

### <a name="fabric-upgrade-settings---health-polices"></a>Zasady kondycji ustawienia uaktualniania sieci szkieletowej —
Można określić, że kondycja niestandardowe zasady dla uaktualnienie sieci szkieletowej. Jeśli ustawisz klastra do sieci szkieletowej automatycznych uaktualnień, następnie te zasady stosowane do fazy 1 uaktualnienia automatyczne sieci szkieletowej.
Jeśli ustawiono klastra dla sieci szkieletowej ręcznego uaktualnienia, te zasady zastosowana podczas wybierania nowej wersji, wyzwalając systemu, aby uruchamiał uaktualnienia sieci szkieletowej w klastrze. Jeśli nie zastąpisz zasady, są używane wartości domyślne.

Można określić zasady dotyczące kondycji niestandardowych lub Przejrzyj bieżące ustawienia w bloku "uaktualnienia sieci szkieletowej", wybierając pozycję Zaawansowane ustawienia uaktualniania. Przejrzyj następujący obraz na temat. 

![Zarządzanie zasadami kondycji niestandardowe][HealthPolices]

### <a name="customize-fabric-settings-for-your-cluster"></a>Dostosowywanie ustawień sieci szkieletowej dla klastra
Zapoznaj się [ustawienia sieci szkieletowej klastra w sieci szkieletowej usługi](service-fabric-cluster-fabric-settings.md) co i jak można je dostosować.

### <a name="os-patches-on-the-vms-that-make-up-the-cluster"></a>Poprawek systemu operacyjnego na maszynach wirtualnych, które tworzą klaster
Zapoznaj się [Patch Orchestration Application](service-fabric-patch-orchestration-application.md) które można wdrożyć w klastrze można instalować poprawki z witryny Windows Update w zorganizowany sposób przechowywania usług dostępnych przez cały czas. 

### <a name="os-upgrades-on-the-vms-that-make-up-the-cluster"></a>Uaktualnienia systemu operacyjnego na maszynach wirtualnych, które tworzą klaster
Jeśli musisz uaktualnić obrazu systemu operacyjnego na maszynach wirtualnych klastra, należy je jedną maszynę Wirtualną w danym momencie. Użytkownik ponosi odpowiedzialność za dla uaktualnienie — obecnie jest brak automatyzacji dla tego.

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
