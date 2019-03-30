---
title: Uaktualnianie klastra usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: Uaktualnienie usługi Service Fabric kodu i/lub konfiguracji, która działa w klastrze usługi Service Fabric, włącznie z ustawieniem trybu aktualizacji klastra, uaktualnianie certyfikaty, dodawanie portów aplikacji, wykonując poprawek systemu operacyjnego i tak dalej. Czego możesz oczekiwać, gdy wykonywane są uaktualnienia?
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
ms.openlocfilehash: 234bff5049babf0c4b1d036b40201720b2736228
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661636"
---
# <a name="upgrade-the-service-fabric-version-of-a-cluster"></a>Uaktualnienie wersji usługi Service Fabric w klastrze

Dla każdego nowoczesnego systemu projektowanie pod kątem możliwość jest kluczem do osiągnięcia długoterminowym sukcesie produktu. Klaster usługi Azure Service Fabric jest zasobem, który właścicielem, ale jest częściowo zarządzany przez firmę Microsoft. W tym artykule opisano sposób uaktualniania wersji usługi Service Fabric działającym w klastrze platformy Azure.

Można ustawić do klastra, aby otrzymywać uaktualnienia sieci szkieletowej automatyczne, zgodnie z ich wydaniu przez firmę Microsoft lub możesz wybrać wersję obsługiwanych w sieci szkieletowej, że chcesz użyć do klastra, aby znajdować się na.

Można to zrobić, ustawienia konfiguracji klastra "element upgradeMode" w portalu lub przy użyciu usługi Resource Manager w momencie utworzenia lub nowszych na działającym klastrem 

> [!NOTE]
> Upewnij się, że utrzymywania klastra usługi zawsze uruchomiona wersja obsługiwana w sieci szkieletowej. Gdy firma Microsoft ogłaszamy wydanie nowej wersji usługi Service fabric, poprzednia wersja jest oznaczony do zakończenia wsparcia po co najmniej 60 dni od tego dnia. Nowe wersje są anonsowane [na blogu zespołu usługi Service fabric](https://blogs.msdn.microsoft.com/azureservicefabric/). Nowa wersja jest dostępna dla wybrany. 
> 
> 

14 dni przed wygaśnięciem wersji, którą klaster działa, zdarzenie kondycji jest generowany tak, że przełączenie klastra w wskazuje ostrzegawczy stan kondycji. Klaster będzie pozostawał w stanie ostrzeżenia, dopóki nie można uaktualnić do wersji obsługiwanej sieci szkieletowej.

## <a name="set-the-upgrade-mode-in-the-azure-portal"></a>Ustaw tryb uaktualniania w witrynie Azure portal
Podczas tworzenia klastra, można ustawić automatyczne lub ręczne klastra.

![Create_Manualmode][Create_Manualmode]

Można ustawić klastra automatyczne lub ręczne, gdy w klastrze na żywo, za pomocą środowiska zarządzania. 

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>Uaktualnianie do nowej wersji w klastrze, który jest ustawiany w trybie ręcznej za pośrednictwem portalu.
Aby przeprowadzić uaktualnienie do nowej wersji, wszystko, co należy zrobić to wybierz dostępnej wersji z listy rozwijanej, a następnie zapisz. Uaktualnienia sieci szkieletowej pobiera rozpoczęła się automatycznie. Zasady dotyczące kondycji klastra (kombinację kondycji węzła i kondycji wszystkich aplikacji uruchomionych w klastrze) są przestrzegane podczas uaktualniania.

Jeśli zasady dotyczące kondycji klastra nie są spełnione, uaktualnienie zostanie wycofana. Przewiń w dół tego dokumentu, aby dowiedzieć się więcej na temat ustawiania tych zasad niestandardowych kondycji. 

Po rozwiązaniu problemów, które spowodowało wycofywania, musisz zainicjować aktualizację ponownie, wykonując te same kroki co wcześniej.

![Manage_Automaticmode][Manage_Automaticmode]

## <a name="set-the-upgrade-mode-using-a-resource-manager-template"></a>Ustaw tryb uaktualniania przy użyciu szablonu usługi Resource Manager
Dodaj konfigurację "element upgradeMode" w definicji zasobu Microsoft.ServiceFabric/clusters i ustaw opcję "clusterCodeVersion" na jedną z wersji obsługiwanych w sieci szkieletowej, jak pokazano poniżej, a następnie wdrożyć szablon. Prawidłowe wartości dla "element upgradeMode" to "Ręczny" lub "Automatyczny"

![ARMUpgradeMode][ARMUpgradeMode]

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>Uaktualnianie do nowej wersji w klastrze, który jest ustawiany w trybie ręcznie za pomocą szablonu usługi Resource Manager.
Gdy klaster jest w trybie ręcznej, aby uaktualnić do nowej wersji Zmień "clusterCodeVersion" do obsługiwanej wersji i wdrożyć ją. Wdrożenie szablonu kopnięć uaktualnienia sieci szkieletowej pobiera rozpoczęła się automatycznie. Zasady dotyczące kondycji klastra (kombinację kondycji węzła i kondycji wszystkich aplikacji uruchomionych w klastrze) są przestrzegane podczas uaktualniania.

Jeśli zasady dotyczące kondycji klastra nie są spełnione, uaktualnienie zostanie wycofana.  

Po rozwiązaniu problemów, które spowodowało wycofywania, musisz zainicjować aktualizację ponownie, wykonując te same kroki co wcześniej.

## <a name="set-custom-health-polices-for-upgrades"></a>Kondycja niestandardowy zestaw zasad do uaktualnienia
Można określić, że kondycja niestandardowe zasady dla uaktualnienie sieci szkieletowej. Jeśli ustawisz klastra do uaktualnienia sieci szkieletowej automatyczne, a następnie stosowane te zasady do [fazy 1 uaktualnienia sieci szkieletowej automatyczne](service-fabric-cluster-upgrade.md#fabric-upgrade-behavior-during-automatic-upgrades).
Jeśli ustawiono klastra dla sieci szkieletowej ręcznego uaktualnienia, te zasady zastosowana podczas wybierania nowej wersji, wyzwalając systemu, aby uruchamiał uaktualnienia sieci szkieletowej w klastrze. Jeśli nie zastąpisz zasady, są używane wartości domyślne.

Można określić zasady dotyczące kondycji niestandardowych lub Przejrzyj bieżące ustawienia w bloku "uaktualnienia sieci szkieletowej", wybierając pozycję Zaawansowane ustawienia uaktualniania. Przejrzyj następujący obraz na temat. 

![Zarządzanie zasadami kondycji niestandardowe][HealthPolices]

## <a name="list-all-available-versions-for-all-environments-for-a-given-subscription"></a>Lista wszystkich dostępnych wersji dla wszystkich środowisk w ramach danej subskrypcji
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
