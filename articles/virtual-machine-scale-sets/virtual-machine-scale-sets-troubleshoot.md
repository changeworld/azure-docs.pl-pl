---
title: Rozwiązywanie problemów ze skalowaniem automatycznym za pomocą zestawów skalowania maszyny wirtualnej
description: Rozwiązywanie problemów ze skalowaniem automatycznym za pomocą zestawów skalowania maszyny wirtualnej. Opis typowych napotkanych problemów i sposobu ich rozwiązywania.
author: mayanknayar
tags: azure-resource-manager
ms.assetid: c7d87b72-ee24-4e52-9377-a42f337f76fa
ms.service: virtual-machine-scale-sets
ms.tgt_pltfrm: windows
ms.topic: conceptual
ms.date: 11/16/2017
ms.author: manayar
ms.openlocfilehash: 923967a902f611ce845fbdc096fd2c02e681bb6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76272430"
---
# <a name="troubleshooting-autoscale-with-virtual-machine-scale-sets"></a>Troubleshooting autoscale with Virtual Machine Scale Sets (Rozwiązywanie problemów ze skalowaniem automatycznym przy użyciu zestawów skalowania maszyn wirtualnych)
**Problem** — utworzono infrastrukturę skalowania automatycznego w usłudze Azure Resource Manager przy użyciu https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale zestawów skalowania maszyny wirtualnej — na przykład przez wdrożenie szablonu takiego jak ten: — masz zdefiniowane reguły skalowania i działa świetnie, z wyjątkiem niezależnie od tego, ile obciążenia można umieścić na maszynach wirtualnych, nie jest skalowana automatycznie.

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów
Oto kilka rzeczy, które należy wziąć pod uwagę:

* Ile procesorów wirtualnych ma każda maszyna wirtualna i czy ładujesz każdy procesor wirtualny?
  Poprzedni przykładowy szablon szybki start platformy Azure ma skrypt do_work.php, który ładuje pojedynczy procesor wirtualny. Jeśli używasz maszyny Wirtualnej większy niż rozmiar maszyny Wirtualnej z jednym procesorem wirtualnym, taki jak Standard_A1 lub D1, należy uruchomić to obciążenie wiele razy. Sprawdzanie liczby procesorów wirtualnych dla maszyn wirtualnych, przeglądając [rozmiary maszyn wirtualnych z systemem Windows na platformie Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Ile maszyn wirtualnych w zestawie skalowania maszyny wirtualnej, czy wykonujesz pracę nad każdym z nich?
  
    Zdarzenie skalowania w poziomie ma miejsce tylko wtedy, gdy średni procesor CPU **we wszystkich** maszynach wirtualnych w zestawie skalowania przekracza wartość progową, przez czas wewnętrzny zdefiniowany w regułach skalowania automatycznego.
* Czy przegapiłeś jakieś wydarzenia na skalę?
  
    Sprawdź dzienniki inspekcji w witrynie Azure portal pod kątem zdarzeń skalowania. Może była skala w górę i skala w dół, która została pominięta. Możesz filtrować według "Skaluj".
  
    ![Dzienniki inspekcji][audit]
* Czy progi skalowania i skalowania w poziomie są wystarczająco różne?
  
    Załóżmy, że ustawisz regułę skalowania w poziomie, gdy średni procesor CPU jest większy niż 50% w ciągu pięciu minut, i skalowania, gdy średni procesor jest mniejszy niż 50%. To ustawienie spowodowałoby problem "machania", gdy użycie procesora CPU jest bliskie progu, przy czym akcje skalowania stale wzrastają i zmniejszają rozmiar zestawu. Z powodu tego ustawienia usługa skalowania automatycznego próbuje zapobiec "flapping", który może objawiać się jako nie skalowanie. W związku z tym upewnij się, że progi skalowania w poziomie i skalowania są wystarczająco różne, aby umożliwić pewne miejsce między skalowanie.
* Czy napisałeś własny szablon JSON?
  
    Łatwo jest popełniać błędy, więc zacznij od szablonu takiego jak ten powyżej, który jest sprawdzony do pracy, i wprowadzać niewielkie zmiany przyrostowe. 
* Czy można ręcznie skalować lub wysuw?
  
    Spróbuj ponownie wdrożyć zasób zestawu skalowania maszyny wirtualnej z innym ustawieniem "pojemność", aby ręcznie zmienić liczbę maszyn wirtualnych. Przykładowy szablon jest https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing tutaj: — może być konieczne edytowanie szablonu, aby upewnić się, że ma taki sam rozmiar komputera, jaki używa zestawu skalowania. Jeśli można pomyślnie zmienić liczbę maszyn wirtualnych ręcznie, wiesz, że problem jest izolowany do skalowania automatycznego.
* Sprawdź zasoby microsoft.compute/virtualMachineScaleSet i Microsoft.Insights w [Eksploratorze zasobów platformy Azure](https://resources.azure.com/)
  
    Eksplorator zasobów platformy Azure jest niezbędnym narzędziem do rozwiązywania problemów, które pokazuje stan zasobów usługi Azure Resource Manager. Kliknij subskrypcję i spójrz na grupę zasobów, którą rozwiązujesz. W obszarze Dostawca zasobów obliczeniowych przyjrzyj się utworzonemu zestawowi skalowania maszyny wirtualnej i sprawdź widok wystąpienia, który pokazuje stan wdrożenia. Ponadto sprawdź widok wystąpienia maszyn wirtualnych w zestawie skalowania maszyny wirtualnej. Następnie przejdź do dostawcy zasobów microsoft.insights i sprawdź, czy reguły skalowania automatycznego wyglądają dobrze.
* Czy rozszerzenie diagnostyki działa i emituje dane o wydajności?
  
    **Aktualizacja:** Skalowanie automatyczne platformy Azure zostało rozszerzone, aby użyć potoku metryk opartych na hoście, który nie wymaga już zainstalowania rozszerzenia diagnostyki. Kilka następnych akapitów nie ma już zastosowania, jeśli utworzysz aplikację skalowania automatycznego przy użyciu nowego potoku. Przykład szablonów platformy Azure, które zostały przekonwertowane https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscaledo korzystania z potoku hosta jest dostępny tutaj: . 
  
    Używanie metryk opartych na hoście do skalowania automatycznego jest lepsze z następujących powodów:
  
  * Mniej ruchomych części, ponieważ nie trzeba instalować żadnych rozszerzeń diagnostycznych.
  * Prostsze szablony. Wystarczy dodać reguły skalowania automatycznego szczegółowych do istniejącego szablonu zestawu skalowania.
  * Bardziej niezawodne raportowanie i szybsze uruchamianie nowych maszyn wirtualnych.
    
    Jedynymi powodami, dla których możesz chcieć nadal używać rozszerzenia diagnostycznego, jest konieczność raportowania/skalowania diagnostyki pamięci. Metryki oparte na hoście nie zgłaszają pamięci.
    
    Mając to na uwadze, postępuj zgodnie z pozostałą częścią tego artykułu, jeśli używasz rozszerzeń diagnostycznych do skalowania automatycznego.
    
    Skalowanie automatyczne w usłudze Azure Resource Manager może działać (ale nie musi już) za pomocą rozszerzenia maszyny Wirtualnej o nazwie Rozszerzenie diagnostyki. Emituje dane o wydajności do konta magazynu zdefiniowanego w szablonie. Te dane są następnie agregowane przez usługę Azure Monitor.
    
    Jeśli usługa Insights nie może odczytać danych z maszyn wirtualnych, ma wysłać wiadomość e-mail. Na przykład otrzymasz wiadomość e-mail, jeśli maszyny wirtualne są w dół. Sprawdź posoń posie: adres e-mail podany podczas tworzenia konta platformy Azure.
    
    Możesz również spojrzeć na dane samodzielnie. Spójrz na konto magazynu platformy Azure przy użyciu eksploratora w chmurze. Na przykład za pomocą [Visual Studio Cloud Explorer](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2), zaloguj się i wybierz subskrypcję platformy Azure, którego używasz. Następnie zajrzyj do nazwy konta magazynu diagnostyki, do którego odwołuje się definicja rozszerzenia diagnostyki w szablonie wdrożenia.
    
    ![Cloud Explorer][explorer]
    
    Zostanie wyświetle kilka tabel, w których są przechowywane dane z każdej maszyny Wirtualnej. Biorąc linux i metryki procesora CPU jako przykład, spójrz na najnowsze wiersze. Eksplorator chmury programu Visual Studio obsługuje język zapytań, dzięki czemu można uruchomić kwerendę. Na przykład można uruchomić kwerendę dla "Sygnatura czasowa gt datetime'2016-02-02T21:20:00Z",aby upewnić się, że masz najnowsze zdarzenia. Strefa czasowa odpowiada UTC. Czy dane, które tam widzisz, odpowiadają regułom skalowania, które ustawiłeś? W poniższym przykładzie procesor CPU dla komputera 20 zaczął wzrastać do 100% w ciągu ostatnich pięciu minut.
    
    ![Tabele magazynowania][tables]
    
    Jeśli danych nie ma, oznacza to, że problem jest z rozszerzeniem diagnostycznym uruchomionym na maszynach wirtualnych. Jeśli dane są tam, oznacza to, że istnieje problem z reguł skalowania lub z usługą Insights. Sprawdź [stan platformy Azure](https://azure.microsoft.com/status/).
    
    Po przejściu tych kroków, jeśli nadal występują problemy ze skalowaniem automatycznym, możesz wypróbować następujące zasoby: 
    * Przeczytaj fora na [MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=WAVirtualMachinesforWindows)lub [Przepełnienie stosu](https://stackoverflow.com/questions/tagged/azure) 
    * Rejestrowanie połączenia pomocy technicznej. Przygotuj się na udostępnienie szablonu i widoku danych o wydajności.

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png
