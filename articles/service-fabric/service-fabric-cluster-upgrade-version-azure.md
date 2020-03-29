---
title: Uaktualnianie wersji sieci szkieletowej usługi Azure klastra
description: Uaktualnij kod i/lub konfigurację sieci szkieletowej usług, która uruchamia klaster sieci szkieletowej usług, w tym ustawianie trybu aktualizacji klastra, uaktualnianie certyfikatów, dodawanie portów aplikacji, wykonywanie poprawek systemu operacyjnego itd. Czego można się spodziewać po wykonaniu uaktualnień?
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: c3ffcbd4296385623ff5e2c1ee001c27598ff3fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451806"
---
# <a name="upgrade-the-service-fabric-version-of-a-cluster"></a>Uaktualnianie wersji klastra usługi Service Fabric

Dla każdego nowoczesnego systemu, projektowanie dla modernizacji jest kluczem do osiągnięcia długoterminowego sukcesu produktu. Klaster sieci szkieletowej usług Azure jest zasobem, który jest własnością, ale jest częściowo zarządzany przez firmę Microsoft. W tym artykule opisano sposób uaktualniania wersji sieci szkieletowej usług uruchomionej w klastrze platformy Azure.

Można ustawić klaster tak, aby odbierał automatyczne uaktualnienia sieci szkieletowej w miarę ich zwalniania przez firmę Microsoft lub można wybrać obsługiwaną wersję sieci szkieletowej, na której ma być włączony klaster.

Można to zrobić, ustawiając konfigurację klastra "upgradeMode" w portalu lub używając Menedżera zasobów w momencie tworzenia lub później w klastrze na żywo 

> [!NOTE]
> Upewnij się, że klaster zawsze działa w obsługiwanej wersji sieci szkieletowej. Gdy ogłaszamy wydanie nowej wersji sieci szkieletowej usług, poprzednia wersja jest oznaczona do końca pomocy technicznej po co najmniej 60 dniach od tej daty. Nowe wydania są ogłaszane [na blogu zespołu tkaniny usługi](https://blogs.msdn.microsoft.com/azureservicefabric/). Nowa wersja jest dostępna do wyboru. 
> 
> 

14 dni przed wygaśnięciem wydania klastra jest uruchomiona, zdarzenie kondycji jest generowany, który umieszcza klastra w stanie kondycji ostrzeżenie. Klaster pozostaje w stanie ostrzeżenia, dopóki nie uaktualnisz do obsługiwanej wersji sieci szkieletowej.

## <a name="set-the-upgrade-mode-in-the-azure-portal"></a>Ustawianie trybu uaktualniania w witrynie Azure portal
Podczas tworzenia klastra można ustawić klaster na automatyczny lub ręczny.

![Create_Manualmode][Create_Manualmode]

Klaster można ustawić na automatyczny lub ręczny w klastrze na żywo, korzystając z środowiska zarządzania. 

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>Uaktualnienie do nowej wersji w klastrze, który jest ustawiony na tryb ręczny za pośrednictwem portalu.
Aby uaktualnić do nowej wersji, wszystko, co musisz zrobić, to wybrać dostępną wersję z listy rozwijanej i zapisać. Uaktualnienie sieci szkieletowej zostanie automatycznie rozpoczęte. Zasady kondycji klastra (połączenie kondycji węzła i kondycji wszystkich aplikacji uruchomionych w klastrze) są przestrzegane podczas uaktualniania.

Jeśli zasady kondycji klastra nie są spełnione, uaktualnienie jest przywracane. Przewiń w dół tego dokumentu, aby dowiedzieć się więcej o tym, jak ustawić te niestandardowe zasady dotyczące kondycji. 

Po usunięciu problemów, które spowodowały wycofywanie, należy ponownie zainicjować uaktualnienie, wykonując te same kroki, co poprzednio.

![Manage_Automaticmode][Manage_Automaticmode]

## <a name="set-the-upgrade-mode-using-a-resource-manager-template"></a>Ustawianie trybu uaktualniania przy użyciu szablonu Menedżera zasobów
Dodaj konfigurację "upgradeMode" do definicji zasobu Microsoft.ServiceFabric/clusters i ustaw "clusterCodeVersion" na jedną z obsługiwanych wersji sieci szkieletowej, jak pokazano poniżej, a następnie wdrożyć szablon. Prawidłowe wartości dla "upgradeMode" to "Ręczny" lub "Automatyczny"

![Tryb ARMUpgradeMode][ARMUpgradeMode]

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>Uaktualnienie do nowej wersji w klastrze, który jest ustawiony na tryb ręczny za pomocą szablonu Menedżera zasobów.
Gdy klaster jest w trybie ręcznym, aby uaktualnić do nowej wersji, zmień "clusterCodeVersion" na obsługiwaną wersję i wdrożyć ją. Wdrożenie szablonu, kopnięcia uaktualnienia sieci szkieletowej zostanie automatycznie rozpoczęte. Zasady kondycji klastra (połączenie kondycji węzła i kondycji wszystkich aplikacji uruchomionych w klastrze) są przestrzegane podczas uaktualniania.

Jeśli zasady kondycji klastra nie są spełnione, uaktualnienie jest przywracane.  

Po usunięciu problemów, które spowodowały wycofywanie, należy ponownie zainicjować uaktualnienie, wykonując te same kroki, co poprzednio.

## <a name="set-custom-health-polices-for-upgrades"></a>Ustawianie niestandardowych sejsek zdrowotnych dla ulepszeń
Można określić niestandardowe sieci ochrony zdrowia dla uaktualnienia sieci szkieletu. Jeśli klaster został ustawiony na Automatyczne uaktualnianie sieci szkieletowej, te zasady zostaną zastosowane do [fazy 1 automatycznych uaktualnień sieci szkieletowej.](service-fabric-cluster-upgrade.md#fabric-upgrade-behavior-during-automatic-upgrades)
Jeśli ustawiono klaster dla ręcznych uaktualnień sieci szkieletowej, te zasady są stosowane za każdym razem, gdy wybierzesz nową wersję wyzwalającą system, aby rozpocząć uaktualnianie sieci szkieletowej w klastrze. Jeśli zasady nie zostaną zastąpione, zostaną użyte wartości domyślne.

Można określić niestandardowe zasady kondycji lub przejrzeć bieżące ustawienia w bloku "uaktualnianie sieci szkieletowej", wybierając zaawansowane ustawienia uaktualnienia. Przejrzyj poniższy obrazek, jak to zrobić. 

![Zarządzanie niestandardowymi zasadami kondycji][HealthPolices]

## <a name="list-all-available-versions-for-all-environments-for-a-given-subscription"></a>Lista wszystkich dostępnych wersji dla wszystkich środowisk dla danej subskrypcji
Uruchom następujące polecenie i powinieneś uzyskać dane wyjściowe podobne do tego.

"supportExpiryUtc" informuje, kiedy dana wersja wygasa lub wygasła. Najnowsza wersja nie ma prawidłowej daty - ma wartość "9999-12-31T23:59:59.99999999", co oznacza, że data ważności nie jest jeszcze ustawiona.

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
