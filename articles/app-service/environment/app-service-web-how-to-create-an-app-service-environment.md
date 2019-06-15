---
title: Tworzenie usługi App Service Environment w wersji 1 - Azure
description: Tworzenie opisu przepływu app service environment w wersji 1
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: 81bd32cf-7ae5-454b-a0d2-23b57b51af47
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 9bc796c4d0d449f72dc3234bc2825554eafaf77f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62128890"
---
# <a name="how-to-create-an-app-service-environment-v1"></a>Tworzenie usługi App Service Environment w wersji 1 

> [!NOTE]
> Ten artykuł dotyczy środowiska App Service Environment w wersji 1. Istnieje nowsza wersja usługi App Service Environment jest łatwiejszy w obsłudze, która działa na bardziej zaawansowanych infrastruktury. Aby dowiedzieć się więcej na temat nowej wersji rozpoczynać [wprowadzenie do usługi App Service Environment](intro.md).
> 

### <a name="overview"></a>Omówienie
App Service Environment (ASE) to opcja Premium usługi Azure App Service, która dostarcza możliwości rozszerzonej konfiguracji, która nie jest dostępna w sygnatury wielodostępu. Funkcja środowiska ASE zasadniczo wdraża w usłudze Azure App Service w sieci wirtualnej klienta. Aby uzyskać lepsze zrozumienie możliwości oferowane przez odczyt środowisk App Service Environment [co to jest środowisko usługi App Service] [ WhatisASE] dokumentacji.

### <a name="before-you-create-your-ase"></a>Przed przystąpieniem do tworzenia środowiska ASE
Należy mieć świadomość rzeczy, które nie można zmienić. Te aspekty, których nie można zmienić dotyczących środowiska ASE, po jego utworzeniu są następujące:

* Lokalizacja
* Subskrypcja
* Grupa zasobów
* Używane w sieci wirtualnej
* Podsieć używana 
* Rozmiar podsieci

Po wybraniu sieci wirtualnej i określania podsieci, upewnij się, jest wystarczająco duży, aby uwzględnienie przyszłego rozwoju. 

### <a name="creating-an-app-service-environment-v1"></a>Tworzenie aplikacji usługi Environment w wersji 1
Aby utworzyć środowisko App Service Environment w wersji 1, możesz wyszukiwać w portalu Azure Marketplace ***środowiska App Service Environment v1***, lub przejść przez **Utwórz zasób** -> **sieci Web i mobilność**  ->  **Środowiska app Service Environment**. Aby utworzyć środowiska ASEv1:

1. Podaj nazwę środowiska ASE. Nazwa określona dla środowiska ASE będzie używany w przypadku aplikacji tworzonych w środowisku ASE. Jeśli nazwa środowiska ASE jest appsvcenvdemo, nazwy domeny podrzędnej będzie: *appsvcenvdemo.p.azurewebsites.net*. Jeśli ten sposób utworzono aplikację o nazwie *MojaAplikacja*, byłoby adresowalnych na *mytestapp.appsvcenvdemo.p.azurewebsites.net*. Nie można użyć biały znak, nazwę środowiska ASE. Użycie wielkich liter w nazwie, nazwa domeny będzie całkowita wersję o takiej nazwie. Jeśli korzystasz z wewnętrznym modułem równoważenia obciążenia, nazwa środowiska ASE nie jest używany w Twojej domeny podrzędnej, ale zamiast tego jest jasno określone podczas tworzenia środowiska ASE.
   
    ![][1]
2. Wybierz subskrypcję. Subskrypcję, której używasz środowiska ASE zostaną również zastosowane do wszystkich aplikacji, który zostanie utworzony w tym środowisku ASE. Środowisko ASE nie można umieścić w sieci wirtualnej, która znajduje się w innej subskrypcji.
3. Wybierz lub określ nową grupę zasobów. Grupę zasobów używaną dla środowiska ASE musi być taka sama, używany dla sieci wirtualnej. Wybranie wcześniej istniejącej sieci wirtualnej wybranej grupy zasobów dla środowiska ASE zostanie zaktualizowany w celu odzwierciedlenia zmiany sieci wirtualnej.
   
    ![][2]
4. Wybierz odpowiednie opcje sieci wirtualnej i lokalizacji. Można utworzyć nową sieć wirtualną lub wybierz wstępnie istniejącej sieci wirtualnej. Jeśli wybierzesz nową sieć wirtualną, możesz określić nazwę i lokalizację. Nową sieć wirtualną, będzie miał 192.168.250.0/23 zakres adresów i podsieć o nazwie **domyślne** który jest zdefiniowany jako 192.168.250.0/24. Możesz też po prostu wybrać istniejące klasyczne lub siecią wirtualną Menedżera zasobów. Typ adresu VIP ten wybór decyduje, czy Twoje środowisko ASE są bezpośrednio dostępne z Internetu (zewnętrzny), czy używa wewnętrznego modułu równoważenia obciążenia (ILB). Aby dowiedzieć się więcej o nich odczytać [przy użyciu wewnętrznego modułu równoważenia obciążenia w środowisku usługi App Service][ILBASE]. Wybierz typ adresu VIP, zewnętrznych można wybrać system jest tworzony z celów IPSSL ile zewnętrzne adresy IP. Jeśli wybierzesz wewnętrzne następnie należy określić domenę podrzędną, która będzie używana w środowisku ASE. Za pomocą środowisk ASE można wdrożyć w sieciach wirtualnych, które używają *albo* zakresów adresów publicznych *lub* przestrzeni adresowych RFC1918 (czyli prywatnych adresów). Aby można było używać sieci wirtualnej z zakresem adresów publicznych, należy utworzyć sieć wirtualną wcześniej. Po wybraniu wstępnie istniejącej sieci wirtualnej należy utworzyć nowej podsieci podczas tworzenia środowiska ASE. **Nie można użyć wstępnie utworzonej podsieci, w portalu. Jeśli tworzysz środowisko ASE przy użyciu szablonu usługi resource manager, możesz utworzyć środowisko ASE przy użyciu wcześniej istniejącej podsieci.** Aby utworzyć środowisko ASE przed użyciem szablonu informacje w tym miejscu [tworzenie środowiska usługi App Service za pomocą szablonu] [ ILBAseTemplate] i w tym miejscu [tworzenie środowiska App Service Environment wewnętrznego modułu równoważenia obciążenia z szablonu] [ASEfromTemplate].

### <a name="details"></a>Szczegóły
Środowisko ASE jest tworzony przy użyciu 2 Frontony i 2 procesy robocze. Frontonów działanie jako punktów końcowych HTTP/HTTPS i wysyłania ruchu do pracowników, które są role, które hostują swoje aplikacje. Można dostosować ilość po utworzeniu środowiska ASE i może nawet skonfigurować reguły skalowania automatycznego na tych pul zasobów. Aby uzyskać więcej informacji dotyczących skalowanie ręczne zarządzania i monitorowania środowiska usługi App Service tutaj: [Jak skonfigurować środowisko usługi App Service][ASEConfig] 

Tylko jednego środowiska ASE może znajdować się w podsieci używane przez środowisko ASE. Nie można użyć tej podsieci coś innego niż środowiska ASE

### <a name="after-app-service-environment-v1-creation"></a>Po utworzeniu v1 środowiska App Service Environment
Po utworzeniu środowiska ASE można dostosować:

* Ilość Frontony (minimalna: 2)
* Liczba procesów roboczych (minimalna: 2)
* Liczba adresów IP dostępnych dla protokołu IP SSL
* Obliczenia rozmiarów zasobów, które posługują się Frontony i procesy robocze (fronton minimalny rozmiar to P2)

Dostępne są szczegółowe informacje dotyczące ręczne skalowanie, zarządzanie i monitorowanie środowisk usługi App Service w tym miejscu: [Jak skonfigurować środowisko usługi App Service][ASEConfig] 

Instrukcje dotyczące skalowania automatycznego jest przewodnik tutaj: [Jak skonfigurować automatyczne skalowanie dla środowiska usługi App Service][ASEAutoscale]

Istnieją dodatkowe zależności, które nie są możliwe do dostosowania, takie jak bazy danych i magazyn. Te są obsługiwane przez platformę Azure i dołączone do systemu. Magazyn systemu obsługuje maksymalnie 500 GB dla całej usługi App Service Environment i bazy danych jest uwzględniany przez platformę Azure, odpowiednio do potrzeb skalowania systemu.

## <a name="getting-started"></a>Wprowadzenie
Wprowadzenie do środowiska App Service Environment w wersji 1, zobacz [wprowadzenie do środowiska App Service Environment w wersji 1][WhatisASE]

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
