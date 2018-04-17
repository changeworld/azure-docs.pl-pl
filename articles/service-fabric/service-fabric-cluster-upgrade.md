---
title: Uaktualnienia klastra usługi sieć szkieletowa usług Azure | Dokumentacja firmy Microsoft
description: Uaktualnienie kodu sieci szkieletowej usług i/lub konfigurację, która działa w klastrze usługi sieć szkieletowa usług, w tym ustawieniu trybu aktualizacji klastra, certyfikaty, dodawanie portów aplikacji, sposób poprawek systemu operacyjnego, uaktualnienie i tak dalej. Czego można oczekiwać po uaktualnienia są wykonywane?
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: 15190ace-31ed-491f-a54b-b5ff61e718db
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/10/2017
ms.author: aljo
ms.openlocfilehash: 22dae85c4125c54003f65aba3ef112ffcec9b76d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="upgrade-an-azure-service-fabric-cluster"></a>Uaktualnianie klastra usługi sieć szkieletowa usług Azure
> [!div class="op_single_selector"]
> * [Klastrze platformy Azure](service-fabric-cluster-upgrade.md)
> * [Autonomiczny klastra](service-fabric-cluster-upgrade-windows-server.md)
> 
> 

Systemu nowoczesnych projektowanie na możliwość ma kluczowe znaczenie dla osiągnięcia długoterminowym sukcesie produktu. Klaster sieci szkieletowej usług Azure jest z zasobem, który jest właścicielem, ale jest częściowo zarządzany przez firmę Microsoft. W tym artykule opisano, co jest zarządzana automatycznie i samodzielnie skonfigurować.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Kontrolowanie wersję sieci szkieletowej, która działa w klastrze
Można ustawić do otrzymywania uaktualnień automatyczne sieci szkieletowej, zgodnie z ich wydania przez firmę Microsoft lub wybrać wersję obsługiwanych sieci szkieletowej, które chcesz klastra w klastrze.

Możesz to zrobić przez ustawienie konfiguracji klastra "parametr upgrademode musi mieć" w portalu lub przy użyciu Menedżera zasobów w czasie tworzenia lub nowsza w klastrze na żywo 

> [!NOTE]
> Upewnij się zachować klastra zawsze uruchomiona wersja obsługiwanych sieci szkieletowej. Gdy firma Microsoft ogłaszamy wydanie programu nowa wersja usługi service fabric, poprzednia wersja jest oznaczony do zakończenia wsparcia po co najmniej 60 dni. nowe wersje są ogłaszane [na blogu zespołu usługi sieć szkieletowa](https://blogs.msdn.microsoft.com/azureservicefabric/). Nową wersję jest dostępna do wyboru następnie. 
> 
> 

14 dni przed wygaśnięciem zlecenia, które klaster działa, kondycji zdarzenie jest generowane wstawiana klastra do stanu kondycji ostrzeżenia. Gdy klaster będzie pozostawał w stanie ostrzeżenia, aż do uaktualnienia do wersji obsługiwanych sieci szkieletowej.

### <a name="setting-the-upgrade-mode-via-portal"></a>Ustawianie trybu uaktualniania za pośrednictwem portalu
Podczas tworzenia klastra, można ustawić klastra na automatycznie lub ręcznie.

![Create_Manualmode][Create_Manualmode]

Klastra można ustawić na tryb automatyczny lub ręczny, gdy w klastrze na żywo przy użyciu środowisko zarządzania. 

#### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>Uaktualnienie do nowej wersji w klastrze, który ma ustawioną wartość ręczny tryb za pośrednictwem portalu.
Aby uaktualnić program do nowej wersji, wszystko, co należy zrobić to wybierz z listy rozwijanej dostępnych wersji i Zapisz. Uaktualnienie sieci szkieletowej pobiera rozpoczęte automatycznie. Zasady dotyczące kondycji klastra (kombinację kondycji węzła i kondycję wszystkich aplikacji uruchomionych w klastrze) są przestrzegane podczas uaktualniania.

Jeśli zasady dotyczące kondycji klastra nie są spełnione, uaktualnienie zostanie wycofana. Przewiń w dół tego dokumentu, aby dowiedzieć się więcej na temat ustawiania tych zasad dotyczących kondycji niestandardowych. 

Po rozwiązaniu problemów, które spowodowało wycofywanie, musisz zainicjować aktualizację ponownie, wykonując te same kroki jako przed.

![Manage_Automaticmode][Manage_Automaticmode]

### <a name="setting-the-upgrade-mode-via-a-resource-manager-template"></a>Ustawianie trybu uaktualnienia za pomocą szablonu usługi Resource Manager
Dodaj konfigurację "parametr upgrademode musi mieć" w definicji zasobu Microsoft.ServiceFabric/clusters i ustaw "clusterCodeVersion" do wersji obsługiwanych sieci szkieletowej, jak pokazano poniżej, a następnie wdrożyć szablon. Prawidłowe wartości dla "parametr upgrademode musi mieć" to "Ręczny" lub "Automatyczny"

![ARMUpgradeMode][ARMUpgradeMode]

#### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>Uaktualnienie do nowej wersji w klastrze, który ma ustawioną wartość ręczny tryb za pomocą szablonu usługi Resource Manager.
Gdy klaster jest w trybie ręcznym, aby uaktualnić do nowej wersji, zmień "clusterCodeVersion" do obsługiwanej wersji i wdrożyć go. Wdrożenie szablonu, kopnięć uaktualnienia sieci szkieletowej pobiera rozpoczęte automatycznie. Zasady dotyczące kondycji klastra (kombinację kondycji węzła i kondycję wszystkich aplikacji uruchomionych w klastrze) są przestrzegane podczas uaktualniania.

Jeśli zasady dotyczące kondycji klastra nie są spełnione, uaktualnienie zostanie wycofana. Przewiń w dół tego dokumentu, aby dowiedzieć się więcej na temat ustawiania tych zasad dotyczących kondycji niestandardowych. 

Po rozwiązaniu problemów, które spowodowało wycofywanie, musisz zainicjować aktualizację ponownie, wykonując te same kroki jako przed.

### <a name="get-list-of-all-available-version-for-all-environments-for-a-given-subscription"></a>Pobierz listę wszystkich dostępnych wersji dla wszystkich środowisk w ramach danej subskrypcji
Uruchom następujące polecenie, a następnie należy pobrać dane wyjściowe podobne do poniższego.

"supportExpiryUtc" informuje użytkownika w przypadku danej wersji wygaśnie lub jego ważność wygasła. Najnowsza wersja nie ma prawidłowej daty — ma wartość "9999-12-31T23:59:59.9999999", co oznacza tylko, że data wygaśnięcia nie jest jeszcze ustawiony.

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

## <a name="fabric-upgrade-behavior-when-the-cluster-upgrade-mode-is-automatic"></a>Zachowania uaktualnienia sieci szkieletowej, gdy tryb uaktualniania klastra automatyczne
Firma Microsoft udostępnia kodu sieci szkieletowej i konfigurację, która działa w klastrze platformy Azure. Możemy przeprowadzić automatycznych uaktualnień monitorowanych oprogramowania na zgodnie z potrzebami. Te uaktualnienia może być kodu i konfiguracji. Aby upewnić się, że aplikacja wystąpi nie wpływu lub minimalny wpływ z powodu te uaktualnienia, możemy przeprowadzić uaktualnienia w następujących faz:

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>Faza 1: Uaktualnianie jest przeprowadzane przy użyciu wszystkich zasad dotyczących kondycji klastra
W tej fazie uaktualnień kontynuować domeny uaktualnienia pojedynczo, a aplikacje, które były uruchomione w klastrze kontynuował pracę bez żadnego przestoju. Zasady dotyczące kondycji klastra (kombinację kondycji węzła i kondycję wszystkich aplikacji uruchomionych w klastrze) są przestrzegane podczas uaktualniania.

Jeśli zasady dotyczące kondycji klastra nie są spełnione, uaktualnienie zostanie wycofana. Następnie zostanie wysłana wiadomość e-mail do właściciela subskrypcji. Wiadomości e-mail zawiera następujące informacje:

* Powiadomienie Musieliśmy przywracać stanu sprzed uaktualnienia klastra.
* Sugerowane akcje naprawcze, jeśli istnieje.
* Liczba dni (n), dopóki nie możemy wykonać fazy 2.

Spróbujemy wykonaj to samo uaktualnienie kilka razy w przypadku uaktualniania nie powiodło się ze względów infrastruktury. Po n dni od wysłania wiadomości e-mail możemy przejść do fazy 2.

Jeśli zasady dotyczące kondycji klastra są spełnione, uaktualnianie jest uznawany za pomyślny i oznaczone jako ukończone. Może to nastąpić podczas początkowej uaktualnienia lub dowolnym powtórki uaktualnienia w tej fazie. Nie ma żadnych wiadomości e-mail z potwierdzeniem pomyślnego uruchomienia. Pozwoli to uniknąć wysyłanie zostanie zbyt dużo żądań wiadomości e-mail — odbieranie wiadomości e-mail powinno ono występować jako wyjątek do normalnego. Oczekujemy większość uaktualnienia klastra została wykonana pomyślnie bez wpływu na dostępność Twojej aplikacji.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>Faza 2: Uaktualnianie jest przeprowadzane przy użyciu tylko zasady dotyczące kondycji domyślne
Zasady dotyczące kondycji w tej fazie są ustawiane w taki sposób, że liczba aplikacje, które zostały dobrej kondycji na początku uaktualnienia nie zmienia się w czasie trwania procesu uaktualniania. Jak Faza 1 uaktualnień fazy 2 kontynuacji jedną domenę uaktualnienia w czasie, a aplikacje, które były uruchomione w klastrze kontynuował pracę bez żadnego przestoju. Zasady dotyczące kondycji klastra (kombinację kondycji węzła i kondycję wszystkich aplikacji uruchomionych w klastrze) są przestrzegane na czas trwania uaktualniania.

Jeśli zasady dotyczące kondycji klastra skutkuje nie są spełnione, uaktualnienie zostanie wycofana. Następnie zostanie wysłana wiadomość e-mail do właściciela subskrypcji. Wiadomości e-mail zawiera następujące informacje:

* Powiadomienie Musieliśmy przywracać stanu sprzed uaktualnienia klastra.
* Sugerowane akcje naprawcze, jeśli istnieje.
* Liczba dni (n), dopóki nie możemy wykonać fazy 3.

Spróbujemy wykonaj to samo uaktualnienie kilka razy w przypadku uaktualniania nie powiodło się ze względów infrastruktury. Przypomnienie e-mail jest wysyłana kilka dni, n dni są włączone. Po n dni od wysłania wiadomości e-mail możemy przejść do fazy 3. Wiadomości e-mail, który możemy wysłać Faza 2 należy podjąć poważnie i należy podjąć działania naprawcze.

Jeśli zasady dotyczące kondycji klastra są spełnione, uaktualnianie jest uznawany za pomyślny i oznaczone jako ukończone. Może to nastąpić podczas początkowej uaktualnienia lub dowolnym powtórki uaktualnienia w tej fazie. Nie ma żadnych wiadomości e-mail z potwierdzeniem pomyślnego uruchomienia.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>Faza 3: Uaktualnianie jest przeprowadzane za pomocą zasady dotyczące kondycji aktywnego
Te zasady dotyczące kondycji w tej fazie są przeznaczone dla zakończenie uaktualniania, a nie kondycji aplikacji. Bardzo mało uaktualnienia klastra przechodzili na tym etapie. Jeśli klaster pobiera na tym etapie, istnieje duże prawdopodobieństwo, że aplikacja staje się nieprawidłowy, i/lub utratę dostępności.

Podobnie jak w dwóch fazach, Faza 3 uaktualnień kontynuować jedną domenę uaktualnienia naraz.

Jeśli zasady dotyczące kondycji klastra nie są spełnione, uaktualnienie zostanie wycofana. Spróbujemy wykonaj to samo uaktualnienie kilka razy w przypadku uaktualniania nie powiodło się ze względów infrastruktury. Po utworzeniu tego klastra jest przypięty tak, aby go nie będziesz już otrzymywać pomocy technicznej i/lub uaktualnień.

Do właściciela subskrypcji, oraz czynności zaradczych zostanie wysłana wiadomość e-mail z tymi informacjami. Firma Microsoft nie oczekuje żadnych klastrów można uzyskać w stanie, w którym fazy 3 nie powiodło się.

Jeśli zasady dotyczące kondycji klastra są spełnione, uaktualnianie jest uznawany za pomyślny i oznaczone jako ukończone. Może to nastąpić podczas początkowej uaktualnienia lub dowolnym powtórki uaktualnienia w tej fazie. Nie ma żadnych wiadomości e-mail z potwierdzeniem pomyślnego uruchomienia.

## <a name="cluster-configurations-that-you-control"></a>Konfiguracje klastrów, które kontrolujesz
Oprócz tego sprawdzić możliwości można ustawić klastra w tryb uaktualniania, w tym miejscu są konfiguracje, które można zmienić w klastrze na żywo.

### <a name="certificates"></a>Certyfikaty
Możesz dodać nowe lub łatwo usunąć certyfikaty dla klastra i klienta za pośrednictwem portalu. Zapoznaj się [tego dokumentu, aby uzyskać szczegółowe instrukcje](service-fabric-cluster-security-update-certs-azure.md)

![Zrzut ekranu pokazujący odciski palców certyfikatów w portalu Azure.][CertificateUpgrade]

### <a name="application-ports"></a>Porty aplikacji
Porty aplikacji można zmienić, zmieniając właściwości zasobów usługi równoważenia obciążenia, które są skojarzone z typem węzła. Mogą korzystać z portalu lub środowiska PowerShell usługi Resource Manager można korzystać bezpośrednio.

Aby otworzyć nowy port na wszystkich maszynach wirtualnych typu węzła, wykonaj następujące czynności:

1. Dodaj nowy sondy do odpowiedniej usługi równoważenia obciążenia.
   
    Jeśli wdrożono klastra przy użyciu portalu usługi równoważenia obciążenia są nazywane "LB Nazwa grupy zasobów-NodeTypename", po jednej dla każdego typu węzła. Ponieważ nazwy usługi równoważenia obciążenia różnią się tylko w obrębie grupy zasobów, najlepiej wyszukiwania je w obszarze określonej grupy zasobów.
   
    ![Zrzut ekranu przedstawiający dodawanie badanie równoważenia obciążenia w portalu.][AddingProbes]
2. Dodaj nową regułę modułu równoważenia obciążenia.
   
    Dodaj nową regułę do tego samego modułu równoważenia obciążenia przy użyciu sondowania, który został utworzony w poprzednim kroku.
   
    ![Dodawanie nowej reguły równoważenia obciążenia w portalu.][AddingLBRules]

### <a name="placement-properties"></a>Właściwości umieszczania
Dla każdego typu węzła możesz dodać niestandardowe właściwości umieszczania, które ma być używany w aplikacjach. Typ NodeType jest używanej bez dodawania go jawnie właściwości domyślnej.

> [!NOTE]
> Szczegółowe informacje dotyczące stosowania ograniczeń umieszczania, właściwości węzła i sposób definiowania ich, zapoznaj się z rozdziałem "Ograniczenia i węzła właściwości umieszczania" w dokumencie zasobu klastra sieci szkieletowej programu Service Manager na [opisujące klastra Your](service-fabric-cluster-resource-manager-cluster-description.md).
> 
> 

### <a name="capacity-metrics"></a>Metryki pojemności
Dla każdego typu węzła możesz dodać niestandardowe metryki pojemności, który ma być używany w celu raportowania obciążenia aplikacji. Aby uzyskać więcej informacji dotyczących korzystania z metryki pojemności do raportu obciążenia, znajduje się w dokumentach zasobu klastra sieci szkieletowej programu Service Manager w [opisujące Your klastra](service-fabric-cluster-resource-manager-cluster-description.md) i [metryki i obciążenia](service-fabric-cluster-resource-manager-metrics.md).

### <a name="fabric-upgrade-settings---health-polices"></a>Zasady kondycji sieci szkieletowej ustawienia uaktualnienia —
Można określić, czy zasady niestandardowe kondycji dla uaktualnienia programu fabric. Jeśli została wybrana klastra sieci szkieletowej automatycznych uaktualnień, te zasady zostać zastosowane do pierwszą fazę aktualizacje automatyczne sieci szkieletowej.
Jeśli ustawiono klastra sieci szkieletowej ręcznego uaktualnienia, te zasady zostać zastosowane podczas wybierania nowej wersji wyzwalania systemu zaczną działać poza uaktualnienia sieci szkieletowej w klastrze. Jeśli nie zastępują zasady, zostaną użyte wartości domyślne.

Można określić zasady dotyczące kondycji niestandardowych lub Przejrzyj bieżące ustawienia w obszarze bloku "uaktualnienia programu fabric", wybierając Zaawansowane ustawienia uaktualnienia. Przejrzyj poniższe obraz na temat. 

![Zarządzanie zasadami kondycji niestandardowych][HealthPolices]

### <a name="customize-fabric-settings-for-your-cluster"></a>Dostosowywanie ustawień sieci szkieletowej dla klastra
Zapoznaj się [ustawień sieci szkieletowej klastra sieci szkieletowej usług](service-fabric-cluster-fabric-settings.md) na co jest i jak można dostosować je.

### <a name="os-patches-on-the-vms-that-make-up-the-cluster"></a>Poprawek systemu operacyjnego na maszynach wirtualnych, które tworzą klastra
Zapoznaj się [poprawki aplikacji aranżacji](service-fabric-patch-orchestration-application.md) którego można wdrożyć w klastrze, aby instalować poprawki z usługi Windows Update w sposób orchestrated zachować dostępność usług cały czas. 

### <a name="os-upgrades-on-the-vms-that-make-up-the-cluster"></a>Uaktualnień systemu operacyjnego na maszynach wirtualnych, które tworzą klastra
Jeśli musisz uaktualnić obrazu systemu operacyjnego w przypadku maszyn wirtualnych klastra, należy to robić jedną maszynę Wirtualną w czasie. Przydadzą się osobom odpowiedzialnym dla tego uaktualnienia — nie ma obecnie nie automatyzacji tego.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak dostosować niektóre [ustawień sieci szkieletowej klastra sieci szkieletowej usług](service-fabric-cluster-fabric-settings.md)
* Dowiedz się, jak [i wylogowywanie skalowanie klastra](service-fabric-cluster-scale-up-down.md)
* Dowiedz się więcej o [uaktualnienia aplikacji](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
