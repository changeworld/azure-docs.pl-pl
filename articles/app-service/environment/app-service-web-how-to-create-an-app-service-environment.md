---
title: Tworzenie ase v1
description: Opis przepływu tworzenia dla środowiska usługi aplikacji w wersji 1. Ten doc jest dostępna tylko dla klientów, którzy używają starszej wersji ASE w wersji 1.
author: ccompy
ms.assetid: 81bd32cf-7ae5-454b-a0d2-23b57b51af47
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 752334e3d594b1f95786aecaca134b74c4e264d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74688690"
---
# <a name="how-to-create-an-app-service-environment-v1"></a>Jak utworzyć środowisko usługi app service w wersji 1 

> [!NOTE]
> Ten artykuł dotyczy środowiska usługi app service w wersji 1. Istnieje nowsza wersja środowiska usługi app service, która jest łatwiejsza w użyciu i działa na bardziej zaawansowanej infrastruktury. Aby dowiedzieć się więcej o nowej wersji, zacznij od [wprowadzenia do środowiska usługi app service](intro.md).
> 

### <a name="overview"></a>Omówienie
Środowisko usługi aplikacji (ASE) to opcja usługi Premium usługi Azure App Service, która zapewnia ulepszoną konfigurację, która nie jest dostępna w sygnaturach wielodostępnych. Funkcja ASE zasadniczo wdraża usługę Azure App Service w sieci wirtualnej klienta. Aby uzyskać lepsze zrozumienie możliwości oferowanych przez środowiska usługi App Service, przeczytaj [dokumentację Środowiska usługi app service.][WhatisASE]

### <a name="before-you-create-your-ase"></a>Przed utworzeniem ase
Ważne jest, aby zdawać sobie sprawę z tego, czego nie można zmienić. Te aspekty, których nie można zmienić w przypadku ase po jego utworzeniu są:

* Lokalizacja
* Subskrypcja
* Grupa zasobów
* Używana sieci wirtualnej
* Używana podsieć 
* Rozmiar podsieci

Podczas wybierania sieci wirtualnej i określania podsieci upewnij się, że jest wystarczająco duża, aby pomieścić każdy przyszły wzrost. 

### <a name="creating-an-app-service-environment-v1"></a>Tworzenie środowiska usługi aplikacji w wersji 1
Aby utworzyć środowisko usługi app service w wersji 1, możesz przeszukać środowisko usługi App Service w witrynie Azure Marketplace w ***wersji 1***lub przejść przez **tworzenie zasobu** -> w sieci Web +**środowisko usługi aplikacji****mobilnej** -> . Aby utworzyć ASEv1:

1. Podaj nazwę swojego ASE. Nazwa określona dla ASE będzie używana dla aplikacji utworzonych w ase. Jeśli nazwa ASE to appsvcenvdemo, nazwa poddomeny będzie: *appsvcenvdemo.p.azurewebsites.net*. Jeśli w ten sposób stworzył aplikację o nazwie *mytestapp*, byłoby adresowane w *mytestapp.appsvcenvdemo.p.azurewebsites.net*. Nie można używać odstępu w nazwie systemu ASE. Jeśli używasz wielkich liter w nazwie, nazwa domeny będzie całkowita mała wersja tej nazwy. Jeśli używasz równoważenia obciążenia sieciowego, nazwa ase nie jest używana w poddomenie, ale zamiast tego jest jawnie podana podczas tworzenia ASE.
   
    ![][1]
2. Wybierz subskrypcję. Subskrypcja używana dla twojego ASE będzie również stosowana do wszystkich aplikacji utworzonych w tym ASE. Nie można umieścić ase w sieci wirtualnej, która jest w innej subskrypcji.
3. Wybierz lub określ nową grupę zasobów. Grupa zasobów używana dla ase musi być taka sama, która jest używana dla sieci wirtualnej. Jeśli wybierzesz istniejącą sieci wirtualną, wybór grupy zasobów dla ase zostanie zaktualizowany w celu odzwierciedlenia sieci wirtualnej.
   
    ![][2]
4. Dokonaj wyboru sieci wirtualnej i lokalizacji. Można utworzyć nową sieć wirtualną lub wybrać istniejącą sieć wirtualną. Jeśli wybierzesz nową wirtualną sieci, możesz określić nazwę i lokalizację. Nowa sieci wirtualnej będzie miała zakres adresów 192.168.250.0/23 i podsieć o nazwie **default,** która jest zdefiniowana jako 192.168.250.0/24. Można również po prostu wybrać istniejącą sieci wirtualną Klasycznego lub Menedżera zasobów. Wybór typu VIP określa, czy ase można uzyskać bezpośrednio z Internetu (zewnętrzne) lub jeśli używa wewnętrznego modułu równoważenia obciążenia (ILB). Aby dowiedzieć się więcej o nich, przeczytaj [artykuł Korzystanie z wewnętrznego modułu równoważenia obciążenia ze środowiskiem usługi aplikacji][ILBASE]. Jeśli wybierzesz typ VIP zewnętrzny, możesz wybrać liczbę zewnętrznych adresów IP, z których system jest tworzony na potrzeby IPSSL. Jeśli wybierzesz wewnętrzny, musisz określić poddomenę, której będzie używać program ASE. Ases można wdrożyć w sieciach wirtualnych, które używają zakresów adresów *publicznych* *lub* przestrzeni adresowych RFC1918 (tj. adresów prywatnych). Aby korzystać z sieci wirtualnej z zakresem adresów publicznych, należy utworzyć sieć wirtualną z wyprzedzeniem. Po wybraniu istniejącej sieci wirtualnej należy utworzyć nową podsieć podczas tworzenia ase. **Nie można użyć wstępnie utworzonej podsieci w portalu. Program ASE z istniejącą wcześniej podsiecią można utworzyć, jeśli program ASE zostanie utworzony przy użyciu szablonu Menedżera zasobów.** Aby utworzyć środowisko ASE z szablonu, użyj tych informacji w tym miejscu, [Tworzenie środowiska usługi app service z szablonu][ILBAseTemplate] i tutaj Tworzenie środowiska usługi aplikacji [równoważenia obciążenia][ASEfromTemplate]przy użyciu szablonu .

### <a name="details"></a>Szczegóły
Ase jest tworzony z 2 front ends i 2 Pracowników. Front ends działają jako punkty końcowe HTTP/HTTPS i wysyłają ruch do pracowników, które są rolami hostujymiszymi aplikacje. Można dostosować ilość po utworzeniu ase, a nawet skonfigurować reguły skalowania automatycznego dla tych pul zasobów. Aby uzyskać więcej informacji na temat ręcznego skalowania, zarządzania i monitorowania środowiska usługi app service, przejdź tutaj: [Jak skonfigurować środowisko usługi aplikacji][ASEConfig] 

Tylko jeden ASE może istnieć w podsieci używanej przez ASE. Podsieci nie można używać do niczego innego niż ASE

### <a name="after-app-service-environment-v1-creation"></a>Po utworzeniu środowiska usługi aplikacji w wersji 1
Po utworzeniu ASE można dostosować:

* Ilość frontów (minimum: 2)
* Liczba pracowników (minimum: 2)
* Ilość adresów IP dostępnych dla protokołu IP SSL
* Oblicz rozmiary zasobów używane przez frontowe końce lub pracowników (minimalny rozmiar frontu to P2)

Tutaj znajduje się więcej szczegółów dotyczących ręcznego skalowania, zarządzania i monitorowania środowisk usługi app service: [Jak skonfigurować środowisko usługi app service][ASEConfig] 

Aby uzyskać informacje na temat skalowania automatycznego, znajduje się przewodnik tutaj: [Jak skonfigurować skalowanie automatyczne dla środowiska usługi aplikacji][ASEAutoscale]

Istnieją dodatkowe zależności, które nie są dostępne do dostosowywania, takie jak baza danych i magazyn. Są one obsługiwane przez platformę Azure i są wyposażone w system. Magazyn systemowy obsługuje do 500 GB dla całego środowiska usługi App Service, a baza danych jest dostosowywana przez platformę Azure zgodnie z potrzebami skali systemu.

## <a name="getting-started"></a>Wprowadzenie
Aby rozpocząć pracę ze środowiskiem usługi app service w wersji 1, zobacz [Wprowadzenie do środowiska usługi app service w wersji 1][WhatisASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-basecreateblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-vnetcreation.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[ASEConfig]: app-service-web-configure-an-app-service-environment.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[ILBASE]: app-service-environment-with-internal-load-balancer.md
[ILBAseTemplate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/
[ASEfromTemplate]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
