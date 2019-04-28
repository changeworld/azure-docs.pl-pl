---
title: Rozwiązywanie problemów z skalowania automatycznego za pomocą programu Virtual Machine Scale Sets | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z skalowania automatycznego za pomocą zestawów skalowania maszyn wirtualnych. Dowiedz się, typowe problemy występujące oraz sposobów ich rozwiązywania.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: c7d87b72-ee24-4e52-9377-a42f337f76fa
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: windows
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: manayar
ms.openlocfilehash: 3308b22606e87853aad7e3d3a3995aab8d1b5401
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60803542"
---
# <a name="troubleshooting-autoscale-with-virtual-machine-scale-sets"></a>Rozwiązywanie problemów z skalowania automatycznego za pomocą zestawów skalowania maszyn wirtualnych
**Problem** — utworzono infrastrukturę skalowania automatycznego w usłudze Azure Resource Manager przy użyciu zestawów skalowania maszyn wirtualnych — na przykład, wdrażając szablonu podobny do tego: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale — masz zdefiniowanych reguł skalowania i jej współdziałanie, z wyjątkiem nie niezależnie od tego, ile obciążenia, umieścić na maszynach wirtualnych, nie automatycznego skalowania.

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów
Kilka rzeczy, które należy rozważyć, obejmują:

* Jak wiele procesorów wirtualnych Vcpu każda maszyna wirtualna ma i są ładowane każdy procesor wirtualny?
  Poprzedni przykładowy szablon szybkiego startu platformy Azure ma do_work.php skrypt, który ładuje pojedynczego vCPU. Jeśli używasz maszyny Wirtualnej z systemem większy niż rozmiar maszyny Wirtualnej pojedynczego vCPU, takich jak Standard_A1 lub D1, będziesz potrzebować wielokrotne uruchomienie tego obciążenia. Sprawdź liczbę procesorów wirtualnych dla maszyn wirtualnych, przeglądając [rozmiary dla Windows maszyn wirtualnych na platformie Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Ile maszyn wirtualnych w zestawie skalowania maszyn wirtualnych, to macie pracy na każdym z nich?
  
    Zdarzenie skalowalnego w poziomie tylko ma miejsce, gdy średnie użycie procesora CPU w **wszystkich** maszyn wirtualnych w zestawie skalowania przekracza wartość progowa, wraz z upływem czasu wewnętrznego zdefiniowane w reguł skalowania automatycznego.
* Ominęły Cię wszystkie zdarzenia skalowania?
  
    Sprawdź, czy dzienniki inspekcji w witrynie Azure portal dla zdarzeń dotyczących skalowania. Być może wystąpił skalowania się i skalowania w dół, które nie zostały dotrzymane. Można filtrować według "Skalowanie".
  
    ![Dzienniki inspekcji][audit]
* Wartości progowe skalowania na zewnątrz i skalowalnego w poziomie są różni się wystarczająco?
  
    Załóżmy, że możesz ustawić regułę skalowania w poziomie, gdy średnie użycie procesora CPU jest większy, niż 50% przez ponad pięć minut oraz skalowanie do wewnątrz, gdy średnie użycie procesora CPU jest mniejsza niż 50%. To ustawienie spowodowałoby "niestabilny" problem, gdy użycie procesora CPU jest bliski próg, przy użyciu akcji skalowania stale zwiększania i zmniejszania rozmiaru zestawu. Ze względu na to ustawienie, usługi skalowania automatycznego próbuje zapobiec "niestabilny", który objawiać nie na skalowaniu. W związku z tym upewnij się, że wartości progowe skalowalnego w poziomie i skalowanie w wystarczająco różnią się umożliwienie trochę miejsca między skalowanie.
* Czy napisałeś, aby utworzyć własny szablon JSON?
  
    To proste związanych z błędami, więc zacznij od szablonu, takie jak jest ten, nad którym sprawdzone do pracy i małej zmiany przyrostowe. 
* Można można ręcznie skalować wewnątrz lub na zewnątrz?
  
    Spróbuj ponowne wdrożenie, które zasobu zestawu skalowania maszyn wirtualnych z różnych "pojemność" ustawienie, aby ręcznie zmienić liczbę maszyn wirtualnych. Przykładowy szablon jest już dostępny: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing — może być konieczne edytowanie szablonu, aby upewnić się, ponieważ zestaw skalowania używa ma taki sam rozmiar maszyny. Jeśli liczba maszyn wirtualnych można pomyślnie zmienić ręcznie, następnie wiesz, że problem jest izolowane do automatycznego skalowania.
* Sprawdź swoje Microsoft.Compute/virtualMachineScaleSet i zasobów Microsoft.Insights [Eksploratora zasobów Azure](https://resources.azure.com/)
  
    Usługi Azure Resource Explorer jest niezbędne narzędzia rozwiązywania problemów, pokazujący stan zasobów usługi Azure Resource Manager. Kliknij subskrypcję i spójrz na grupę zasobów, rozwiązywania problemów. W ramach dostawcy zasobów obliczeniowych Przyjrzyj się zestawu skalowania maszyn wirtualnych, które zostały utworzone i sprawdź widok wystąpienia, w którym prezentowany jest stan wdrożenia. Należy także sprawdzić widok wystąpienia maszyn wirtualnych w zestawie skalowania maszyn wirtualnych. Następnie przejdź do dostawcy zasobów Microsoft.Insights i sprawdź, czy reguły skalowania automatycznego w porządku.
* Rozszerzenie diagnostyki pracy i wysyłających danych dotyczących wydajności?
  
    **Aktualizacja:** Skalowanie automatyczne platformy Azure zostało ulepszone, aby użyć potoku metryk opartych na hoście nie wymaga już rozszerzenie diagnostyki, do zainstalowania. Kilku następnych akapitach nie mają już zastosowania, jeśli tworzysz aplikację skalowania automatycznego za pomocą nowego potoku. Przykładem szablonów platformy Azure, które zostały przekonwertowane na korzystanie z potoku hosta jest dostępny tutaj: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale. 
  
    Za pomocą opartego na hoście metryki automatycznego skalowania jest lepszym rozwiązaniem dla następujących przyczyn:
  
  * Mniej ruchomych części jako rozszerzenia diagnostyki, nie trzeba instalować.
  * Szablony prostsze. Wystarczy dodać reguły automatycznego skalowania usługi insights do istniejącego szablonu zestawu skalowania.
  * Bardziej niezawodne, raportowanie i szybsze uruchamianie nowych maszyn wirtualnych.
    
    Tylko przyczyn, dla których warto korzystać rozszerzenie diagnostyki jest, jeśli potrzebujesz pamięci diagnostyki raportowanie/skalowanie. Oparta na hoście metryki nie zgłaszać pamięci.
    
    Mając to na uwadze tylko wykonaj pozostałej części tego artykułu, jeśli używasz rozszerzenia diagnostyki dla usługi skalowania automatycznego.
    
    Automatyczne skalowanie w usłudze Azure Resource Manager można pracować (ale nie musi już) za pomocą maszyny Wirtualnej o nazwie rozszerzenia rozszerzenie diagnostyki. Emituje dane wydajności z kontem magazynu, które należy zdefiniować w szablonie. Te dane są następnie agregowane przez usługę Azure Monitor.
    
    Jeśli usługa szczegółowych informacji nie można odczytać danych z maszyn wirtualnych, powinien wysyłać wiadomości e-mail. Na przykład otrzymasz wiadomość e-mail, jeśli maszyny wirtualne znajdują się w dół. Należy koniecznie sprawdzić wiadomość e-mail na adres e-mail, które określono podczas tworzenia konta platformy Azure.
    
    Można również sprawdzić je samodzielnie. Przyjrzyj się z kontem usługi Azure storage za pomocą Eksploratora chmury. Na przykład za pomocą [programu Visual Studio Cloud Explorer](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2), zaloguj się i wybierz subskrypcję platformy Azure jest używany. Następnie Przyjrzyj się nazwa konta magazynu diagnostyki wspomniane w definicji rozszerzenia diagnostyki w szablonie wdrożenia.
    
    ![Cloud Explorer][explorer]
    
    Zostanie wyświetlona wiele tabel, w którym są przechowywane dane z każdej maszyny Wirtualnej. Korzystając z systemów Linux i metryki procesora CPU, na przykład wyglądać podczas ostatnich wierszy. Program cloud explorer programu Visual Studio obsługuje język zapytań, aby można było uruchomić zapytanie. Na przykład, można uruchomić zapytania dla "sygnatura czasowa gt daty i godziny" 2016-02-02T21:20:00Z "" Aby upewnić się, pobrać najnowszych zdarzeń. Strefa czasowa odnosi się do czasu UTC. Czy dane, które zobaczysz w odpowiadają reguł skalowania można skonfigurować? W poniższym przykładzie Procesora dla maszyny 20 pracę, zwiększając do 100% w ciągu ostatnich pięciu minut.
    
    ![Tabele magazynu][tables]
    
    Jeśli dane nie są dostępne, oznacza to, czy problem jest za pomocą diagnostycznego rozszerzenia, które są uruchomione na maszynach wirtualnych. Jeśli dane są dostępne, sugeruje to, że istnieje problemami przy użyciu reguł skalowania lub za pomocą usługi Insights. Sprawdź [stanu platformy Azure](https://azure.microsoft.com/status/).
    
    Gdy byłeś tych kroków, jeśli nadal występują problemy z funkcją automatycznego skalowania, można skorzystać z następujących zasobów: 
    * Czytanie forum na [MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=WAVirtualMachinesforWindows), lub [przepełnienie stosu](https://stackoverflow.com/questions/tagged/azure) 
    * Zaloguj się pomocy technicznej. Przygotuj się na udostępnianie szablonu i widok danych dotyczących wydajności.

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png
