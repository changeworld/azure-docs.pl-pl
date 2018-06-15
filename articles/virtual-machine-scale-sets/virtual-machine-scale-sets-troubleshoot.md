---
title: Rozwiązywanie problemów z automatycznie skalowana za pomocą zestawów skali maszyny wirtualnej | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z automatycznie skalowana za pomocą zestawów skali maszyny wirtualnej. Zrozumieć typowych problemów i ich rozwiązania.
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
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
ms.author: negat
ms.openlocfilehash: ea634ea8bcb4fed1ed63dc8d1e17d215a00758c6
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161026"
---
# <a name="troubleshooting-autoscale-with-virtual-machine-scale-sets"></a>Rozwiązywanie problemów z automatycznie skalowana za pomocą zestawów skali maszyny wirtualnej
**Problem** — po utworzeniu Skalowanie automatyczne infrastruktury w usłudze Azure Resource Manager za pomocą zestawów skali maszyny wirtualnej — na przykład przy wdrażaniu szablonu podobny do tego: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale — masz zdefiniowanych reguł skalowania i działa ponosić, z wyjątkiem nie niezależnie od tego, ile obciążenia umieszczone na maszynach wirtualnych, nie skalowania automatycznego.

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów
Kilka rzeczy, które należy uwzględnić obejmują:

* Ile Vcpu każda maszyna wirtualna ma i czy ładowanie każdego vCPU?
  Poprzedni przykładowy szablon Szybki Start Azure ma skryptu do_work.php, który jest ładowany przez pojedynczy vCPU. Jeśli używane jest większy niż rozmiar maszyny Wirtualnej jednym vCPU, takich jak Standard_A1 lub D1 maszyny Wirtualnej, konieczne będzie uruchomienie obciążenie wiele razy. Sprawdź liczbę Vcpu dla maszyn wirtualnych, przeglądając [rozmiary dla systemu Windows maszyny wirtualne na platformie Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Jak wiele maszyn wirtualnych w zestawie skalowania maszyn wirtualnych, możesz operacji pracy na każdej z nich?
  
    Zdarzenie skalowalnego w poziomie tylko ma miejsce, gdy średnie wykorzystanie Procesora we **wszystkich** w zestawie skalowania maszyn wirtualnych przekracza wartość progową, wraz z upływem czasu wewnętrznego zdefiniowanych w zasadach automatycznego skalowania.
* Czy zapomniano wszelkie zdarzenia skalowania?
  
    Przejrzyj dzienniki inspekcji w portalu Azure zdarzeń skali. Może być wystąpił skali górę i skalowania, że została pominięta. Można filtrować według "Skala".
  
    ![Dzienniki inspekcji][audit]
* Wartości progowe w skali i skalowalnego w poziomie są różni się wystarczająco?
  
    Załóżmy, że możesz ustawić regułę do skalowania w poziomie, gdy średnie wykorzystanie Procesora jest większa niż 50% ponad pięć minut, a także do skalowania w przypadku średnie wykorzystanie Procesora na mniej niż 50%. To ustawienie może spowodować problem z "flapping", gdy użycie procesora CPU jest bliski próg z akcji skalowania stale zwiększania i zmniejszania rozmiaru zestawu. Ze względu na to ustawienie, usługi skalowania automatycznego próbuje zapobiec "niestabilny", który można manifestu jako skalowania nie. W związku z tym upewnij się, że wartości progowe skalowalnego w poziomie i w skali różnią się wystarczająco Zezwalaj odstępem Between skalowania.
* Czy pisania szablonu JSON?
  
    Jest łatwy do popełnione, więc uruchomienie przy użyciu szablonu, takie jak jest tą, nad którym sprawdzone do pracy i małych zmiany przyrostowe. 
* Można ręcznie skalowanie przychodzący lub wychodzący?
  
    Spróbuj ponownie wdrożyć ustawione zasobów w skali maszyny wirtualnej z inną "pojemność" ustawienie, aby ręcznie zmienić liczbę maszyn wirtualnych. Przykład szablonu jest w tym miejscu: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing — może być konieczne edytowanie szablonu, aby upewnić się, ponieważ korzysta z zestawu skalowania ma ten sam rozmiar maszyny. Czy można pomyślnie ręcznie zmienić liczbę maszyn wirtualnych, następnie wiadomo, że problem dotyczy tylko skalowania automatycznego.
* Sprawdź Twojej Microsoft.Compute/virtualMachineScaleSet i zasoby dostępne w elemencie Microsoft.Insights [Eksploratora zasobów Azure](https://resources.azure.com/)
  
    Eksploratora zasobów Azure jest niezbędne rozwiązywania problemów, pokazujący stan zasobów usługi Azure Resource Manager. Kliknij subskrypcję i przyjrzyj się grupa zasobów jest rozwiązywany problem. W obszarze dostawcy zasobów obliczeniowych sprawdź zestaw skali maszyny wirtualnej zostały utworzone i sprawdź widok wystąpienia, które prezentowany jest stan wdrożenia. Sprawdź również, widok wystąpienia maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. Następnie przejdź w elemencie Microsoft.Insights dostawcy zasobów i sprawdź, czy wygląda reguł skalowania automatycznego.
* Jest diagnostycznych rozszerzenia pracy i wysyłających dane dotyczące wydajności?
  
    **Aktualizacja:** Azure automatycznego skalowania zostało rozszerzone, aby użyć potoku metryki oparta na hoście, który nie wymaga już rozszerzenie diagnostyki do zainstalowania. Następny kilku akapitów nie mają już zastosowania w przypadku utworzenia aplikacji Skalowanie automatyczne przy użyciu nowego potoku. Przykład Azure szablonów, które zostały przekonwertowane na użyć potoku hosta jest dostępny w tym miejscu: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale. 
  
    Przy użyciu oparta na hoście metryki skalowania automatycznego jest lepszym rozwiązaniem w następujących sytuacjach:
  
  * Mniej części ruchome jako rozszerzenia nie diagnostyki muszą być zainstalowane.
  * Szablony prostsze. Wystarczy dodać reguły automatycznego skalowania wgląd do istniejącego szablonu zestaw skali.
  * Bardziej niezawodne, raportowanie i szybsze uruchamianie nowych maszyn wirtualnych.
    
    Tylko przyczyn, które można nadal używać diagnostycznych rozszerzenia jest konieczne będzie pamięci diagnostyki raportowania/skalowania. Metryki oparta na hoście nie zgłaszaj pamięci.
    
    Z tym pamiętać następować tylko po dalszej części tego artykułu Jeśli używasz rozszerzeń diagnostycznych dla Twojego Skalowanie automatyczne.
    
    Funkcja automatycznego skalowania usługi Azure Resource Manager można pracować (ale nie ma już do) za pomocą maszyny Wirtualnej o nazwie rozszerzenia rozszerzenia diagnostyki. Emituje on dane wydajności do konta magazynu definiowane w szablonie. Te dane są agregowane przez usługę Azure Monitor.
    
    Jeśli usługa Insights nie może odczytać danych z maszyn wirtualnych, powinien wysłać wiadomość e-mail. Na przykład otrzymasz wiadomość e-mail w przypadku maszyn wirtualnych w dół. Należy sprawdzić adres e-mail na adres e-mail określony podczas tworzenia konta platformy Azure.
    
    Można również sprawdzić dane samodzielnie. Spójrz na konto magazynu Azure, w Eksploratorze chmury. Na przykład za pomocą [programu Visual Studio Cloud Explorer](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2), zaloguj się i wybierz subskrypcję platformy Azure używasz. Następnie sprawdź nazwę konta magazynu diagnostyki w definicji rozszerzenia diagnostyki w szablonie wdrożenia.
    
    ![Eksplorator chmury][explorer]
    
   Możesz sprawdzić grupy tabel, w którym jest przechowywane dane z każdej maszyny Wirtualnej. Biorąc systemu Linux i metrykę procesora CPU, na przykład Szukaj podczas ostatnich wierszy. Eksplorator chmury programu Visual Studio obsługuje język kwerendy, można uruchomić kwerendę. Na przykład można uruchomić zapytania dla "datetime gt sygnatura czasowa" 2016-02-02T21:20:00Z "" Aby upewnić się, można pobrać najnowszych zdarzeń. Strefa czasowa odpowiada na czas UTC. Czy dane, które widać w odpowiadają reguł skalowania skonfigurowaniu? W poniższym przykładzie Procesora dla maszyny 20 uruchomiona zwiększa się do 100% w ciągu ostatnich pięciu minut.
    
    ![Magazyn tabel][tables]
    
    Jeśli dane nie są dostępne, oznacza to, że jest problem z rozszerzeniem diagnostycznych uruchomionych w maszynach wirtualnych. Jeśli dane są dostępne, oznacza to, że znajduje się problemami z regułami skali lub w usłudze Insights. Sprawdź [Azure stan](https://azure.microsoft.com/status/).
    
    Gdy już został tych kroków, jeśli nadal występują problemy z automatycznego skalowania, można skorzystać z następujących zasobów: 
    * Przeczytaj na forach [MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=WAVirtualMachinesforWindows), lub [przepełnienie stosu](http://stackoverflow.com/questions/tagged/azure) 
    * Zaloguj się pomocy technicznej. Przygotuj się na udostępnianie szablonu i widok danych dotyczących wydajności.

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png
