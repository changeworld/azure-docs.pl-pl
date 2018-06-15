---
title: Jak utworzyć usługę aplikacji środowiska v1
description: Tworzenie opisu przepływu v1 środowiska usługi aplikacji
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
ms.date: 7/11/2017
ms.author: ccompy
ms.openlocfilehash: 2741ea2931ddd7989fc05e1cddbeedb80bf30410
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
ms.locfileid: "29386626"
---
# <a name="how-to-create-an-app-service-environment-v1"></a>Jak utworzyć usługę aplikacji środowiska v1 

> [!NOTE]
> Ten artykuł dotyczy v1 środowiska usługi aplikacji. Istnieje nowsza wersja środowiska usługi aplikacji jest łatwiejsza w użyciu, który jest uruchamiany na bardziej zaawansowanych infrastruktury. Aby dowiedzieć się więcej o nowy początek wersji z [wprowadzenie do środowiska usługi aplikacji](intro.md).
> 

### <a name="overview"></a>Przegląd
Środowiska usługi aplikacji (ASE) to opcja Premium usługi Azure App Service, która zapewnia możliwość rozszerzonej konfiguracji, która nie jest dostępna w sygnatury wielodostępnej. Funkcja ASE zasadniczo wdraża usługi aplikacji Azure do klienta sieci wirtualnej. Aby uzyskać lepsze zrozumienie możliwości oferowane przez odczytać środowiska usługi App Service [co to jest środowisko usługi aplikacji] [ WhatisASE] dokumentacji.

### <a name="before-you-create-your-ase"></a>Przed utworzeniem sieci ASE
Należy zwrócić uwagę czynników, które nie można zmienić. Te aspekty, nie można zmienić o Twojej ASE po jego utworzeniu są:

* Lokalizacja
* Subskrypcja
* Grupa zasobów
* Używane w sieci wirtualnej
* Podsieci używane 
* Rozmiar podsieci

Podczas pobierania sieci wirtualnej i określania podsieci, upewnij się, jest wystarczająco duża, aby uwzględnić przyszłego rozwoju. 

### <a name="creating-an-app-service-environment-v1"></a>Tworzenie aplikacji usługi v1 środowiska
Aby utworzyć v1 środowiska usługi aplikacji, możesz wyszukać Azure Marketplace w celu ***v1 środowiska usługi aplikacji***, lub przejść przez **Utwórz zasób** -> **sieci Web i mobilność**  ->  **Środowiska usługi aplikacji**. Aby utworzyć ASEv1:

1. Podaj nazwę użytkownika ASE. Nazwa określona dla ASE będzie używany dla aplikacji utworzonych w ASE. Jeśli nazwa ASE appsvcenvdemo, nazwę domeny podrzędnej byłaby: *appsvcenvdemo.p.azurewebsites.net*. Jeśli w związku z tym utworzono aplikację o nazwie *mytestapp*, byłoby adresowanego na *mytestapp.appsvcenvdemo.p.azurewebsites.net*. Biały znak nie można użyć nazwy użytkownika ASE. Jeśli używasz wielkich liter w nazwie nazwy domeny będzie całkowita wersją małe o tej nazwie. Jeśli używasz ILB, nazwę ASE nie jest używana w Twojej domeny podrzędnej, ale zamiast tego jest jasno określone podczas tworzenia ASE.
   
    ![][1]
2. Wybierz subskrypcję. Subskrypcji, używanego programu ASE dotyczą również wszystkie aplikacje tworzone w tym ASE. Twoje ASE nie można umieścić w sieci wirtualnej, który znajduje się w innej subskrypcji.
3. Wybierz lub określ nową grupę zasobów. Grupa zasobów używany do Twojej ASE muszą być takie same, służący do sieci wirtualnej. W przypadku wybrania istniejącej sieci wirtualnej wybranej grupy zasobów dla Twojego ASE zostanie zaktualizowany w celu odzwierciedlenia z sieci wirtualnej.
   
    ![][2]
4. Wybierz odpowiednie opcje sieci wirtualnej i lokalizacji. Możesz utworzyć nową sieć wirtualną lub wybierz istniejące sieci wirtualnej. W przypadku wybrania nowej sieci wirtualnej, a następnie można określić nazwy i lokalizacji. Nowej sieci wirtualnej mają 192.168.250.0/23 zakres adresów i podsieć o nazwie **domyślne** zdefiniowanej jako 192.168.250.0/24. Można także po prostu wybierz klasyczny istniejące lub Menedżera zasobów w sieci wirtualnej. Wybór typu VIP określa Twojej ASE są bezpośrednio dostępne z Internetu (zewnętrzne) lub korzysta z wewnętrznego modułu równoważenia obciążenia (ILB). Aby dowiedzieć się więcej o je odczytać [przy użyciu wewnętrznego modułu równoważenia obciążenia z środowiska usługi aplikacji][ILBASE]. Należy wybrać typ VIP zewnętrznych można wybrać system jest tworzony z celów IPSSL ile zewnętrzne adresy IP. W przypadku wybrania wewnętrzne następnie należy określić poddomeny, który będzie używany przez Twoje ASE. ASEs można wdrożyć w sieci wirtualnych, które używają *albo* zakresy publicznych adresów, *lub* przestrzeni adresowych RFC1918 (czyli adresy prywatne). Aby użyć sieci wirtualnej z zakresem adresów publicznych, należy utworzyć sieć wirtualną wcześniejsze. Wybierz istniejące sieci wirtualnej należy utworzyć nową podsieć podczas tworzenia ASE. **Nie można użyć podsieci wstępnie utworzone w portalu. W przypadku utworzenia użytkownika ASE przy użyciu szablonu usługi resource manager, możesz utworzyć ASE z istniejącym podsieci.** Aby utworzyć ASE przed użyciem szablonu informacje w tym miejscu [tworzenia środowiska usługi aplikacji na podstawie szablonu] [ ILBAseTemplate] i w tym miejscu [tworzenia środowiska usługi aplikacji ILB na podstawie szablonu][ASEfromTemplate].

### <a name="details"></a>Szczegóły
ASE jest tworzony z 2 frontonu i 2 procesy robocze. Front kończy się działać jako punktów końcowych HTTP/HTTPS i wysyłania ruchu do pracowników, które są role, które Hostuj aplikacje sieci. Można dopasować ilość po utworzeniu ASE i może nawet zestawu reguł skalowania automatycznego na tych pul zasobów. Więcej szczegółów wokół ręczne skalowanie, zarządzania i monitorowania środowiska usługi aplikacji w tym miejscu: [sposobu konfigurowania środowiska usługi aplikacji][ASEConfig] 

W podsieci używane przez ASE może istnieć tylko jeden ASE. Nie można użyć podsieci dla wszystkich elementów innych niż ASE

### <a name="after-app-service-environment-v1-creation"></a>Po utworzeniu v1 środowiska usługi aplikacji
Po utworzeniu ASE można dostosować:

* Ilość frontonu (minimalna: 2)
* Liczba procesów roboczych (minimalna: 2)
* Liczba adresów IP dostępnych dla protokołu SSL z adresu IP
* Obliczeniowe rozmiary zasobów używanych przez frontonu lub pracowników (minimalny rozmiar fronton jest P2)

Brak więcej szczegółów dotyczących ręczne skalowania, zarządzania i monitorowania środowiska usługi App Service w tym miejscu: [sposobu konfigurowania środowiska usługi aplikacji][ASEConfig] 

Aby uzyskać informacje dotyczące skalowania automatycznego jest przewodnik tutaj: [Konfigurowanie automatycznego skalowania dla środowiska usługi aplikacji][ASEAutoscale]

Istnieją dodatkowe zależności, które nie są możliwe do dostosowania, takie jak bazy danych i magazynu. Są obsługiwane przez platformę Azure i wyposażone w system. Magazyn systemu obsługuje do 500 GB dla całego środowiska usługi aplikacji i bazy danych zostanie zmieniona przez platformę Azure, zgodnie z potrzebami Skala systemu.

## <a name="getting-started"></a>Wprowadzenie
Aby rozpocząć pracę z v1 środowiska usługi aplikacji, zobacz [wprowadzenie do v1 środowiska usługi aplikacji][WhatisASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-basecreateblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-vnetcreation.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[ASEConfig]: app-service-web-configure-an-app-service-environment.md
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[ILBASE]: app-service-environment-with-internal-load-balancer.md
[ILBAseTemplate]: http://azure.microsoft.com/documentation/templates/201-web-app-ase-create/
[ASEfromTemplate]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
