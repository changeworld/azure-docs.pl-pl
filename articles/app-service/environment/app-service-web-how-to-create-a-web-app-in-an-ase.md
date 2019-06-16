---
title: Tworzenie aplikacji internetowej w środowisku App Service Environment v1 — Azure
description: Informacje o sposobie tworzenia aplikacji sieci web i aplikacji planów usług w środowisku App Service Environment w wersji 1
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: 983ba055-e9e4-495a-9342-fd3708dcc9ac
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 3e7db670a125f3c5f308107aabfbbab9301b7561
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60765169"
---
# <a name="create-a-web-app-in-an-app-service-environment-v1"></a>Tworzenie aplikacji internetowej w środowisku App Service Environment w wersji 1

> [!NOTE]
> Ten artykuł dotyczy środowiska App Service Environment w wersji 1.  Istnieje nowsza wersja usługi App Service Environment jest łatwiejszy w obsłudze, która działa na bardziej zaawansowanych infrastruktury. Aby dowiedzieć się więcej na temat nowej wersji rozpoczynać [wprowadzenie do usługi App Service Environment](intro.md).
> 

## <a name="overview"></a>Omówienie
W tym samouczku przedstawiono sposób tworzenia aplikacji sieci web i planów usługi App Service w [środowiska App Service Environment v1](app-service-app-service-environment-intro.md) (ASE). 

> [!NOTE]
> Jeśli chcesz dowiedzieć się, jak utworzyć aplikację sieci web, ale nie trzeba to zrobić w środowisku usługi App Service, zobacz [tworzenie aplikacji sieci web platformy .NET](../app-service-web-get-started-dotnet.md) lub jednego z powiązanych samouczków w przypadku pozostałych języków i struktur.
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne
W tym samouczku przyjęto założenie, że utworzono środowiska usługi App Service. Jeśli użytkownik jeszcze nie, zobacz [tworzenie środowiska usługi App Service](app-service-web-how-to-create-an-app-service-environment.md). 

## <a name="create-a-web-app"></a>Tworzenie aplikacji sieci web
1. W [witryny Azure Portal](https://portal.azure.com/), kliknij przycisk **Utwórz zasób > Sieć Web i mobilność > Aplikacja sieci Web**. 
   
    ![][1]
2. Wybierz subskrypcję.  
   
    Jeśli masz wiele subskrypcji, należy pamiętać, że aby utworzyć aplikację w środowisku usługi App Service, należy użyć tej samej subskrypcji, która została użyta podczas tworzenia środowiska. 
3. Wybierz lub utwórz grupę zasobów.
   
    *Grupy zasobów* umożliwiają zarządzanie powiązanych zasobów platformy Azure jako jednostki i są przydatne podczas ustanawiania *kontroli dostępu opartej na rolach* reguł (RBAC) dla aplikacji. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager][ResourceGroups]. 
4. Wybierz lub utwórz plan usługi App Service.
   
    *Plany usługi App Service* to zarządzane zestawy aplikacji sieci web.  Zwykle po wybraniu przycisku ceny naliczana cena jest stosowany do planu usługi App Service, a nie do poszczególnych aplikacji. W środowisku ASE płacisz za wystąpienia obliczeniowe przydzielone do środowiska ASE zamiast został zgłoszony przez z strona ASP.  Skalowanie w górę liczbę wystąpień aplikacji sieci web, którą można skalować w górę wystąpienia usługi App Service plan i ma wpływ na wszystkie aplikacje sieci web w tym planie.  Niektóre funkcje, takie jak gniazd witryny lub Integracja z siecią Wirtualną obowiązują również ograniczenia ilość w ramach planu.  Aby uzyskać więcej informacji, zobacz [omówienie planów usługi Azure App Service](../overview-hosting-plans.md)
   
    Plany usługi App Service w środowisku ASE można zidentyfikować przez wyszukiwanie w lokalizacji, która została przedstawiona w obszarze nazwy planu.  
   
    ![][5]
   
    Jeśli chcesz użyć plan usługi App Service, która już istnieje w środowisku App Service Environment, wybierz tego planu. Jeśli chcesz utworzyć nowy plan usługi App Service, zobacz następujące części tego samouczka [Utwórz plan usługi App Service w środowisku usługi App Service](#createplan).
5. Wprowadź nazwę aplikacji sieci web, a następnie kliknij przycisk **Utwórz**. 
   
    Jeśli środowisko ASE używa zewnętrznego wirtualnego adresu IP jest adres URL aplikacji w środowisku ASE: [*sitename*]. [ *Nazwa środowiska App Service Environment*]. p.azurewebsites.net zamiast [*sitename*]. azurewebsites.net
   
    Jeśli środowisko ASE używa adresu VIP wewnętrznego, a następnie adres URL aplikacji, w tym środowisku ASE jest: [*sitename*]. [ *poddomeny określone podczas tworzenia środowiska ASE*]   
    Po wybraniu strona ASP podczas tworzenia środowiska ASE zobaczysz poddomeny, zaktualizuj poniżej **nazwy**

## <a name="createplan"></a> Tworzenie planu usługi App Service
Po utworzeniu planu usługi App Service w środowisku usługi App Service, wybór procesów roboczych różnią się, jak istnieją nie udostępnionego procesów roboczych w środowisku ASE.  Procesy robocze, które muszą zostać użyte są tymi, które zostały przydzielone do środowiska ASE przez administratora.  Oznacza to, że aby utworzyć nowy plan, musisz procesów roboczych przydzielonych do środowiska ASE puli procesów roboczych niż łączna liczba wystąpień dla wszystkich planów już w puli procesów roboczych.  Jeśli nie masz wystarczającej liczby pracowników w swoje środowiska ASE puli procesów roboczych do utworzenia planu należy się z administratorem środowiska ASE można następnie dodać je pobrać.

Inna różnica z planami usługi App Service, obsługiwanych przez usługę App Service Environment jest brak cenowej.  Jeśli masz środowisko usługi App Service płatność za zasoby obliczeniowe używane przez system i nie masz dodano opłatach za plany w tym środowisku.  Zwykle po utworzeniu planu usługi App Service możesz wybrać planu cenowego, który określa rozliczeniami.  Środowisko usługi App Service jest zasadniczo lokalizacji prywatnej umożliwiającego utworzenie zawartości.  Płacisz tylko dla środowiska i nie można hostować swoją zawartość.

Poniższe instrukcje przedstawiają sposób tworzenia planu usługi App Service podczas tworzenia aplikacji sieci web, zgodnie z opisem w poprzedniej części samouczka.

1. Kliknij przycisk **Utwórz nowy** w zaplanować wybór interfejsu użytkownika i podaj nazwę dla planu, tak jak zwykle poza środowisko ASE.
2. Wybierz środowisko ASE, którą chcesz korzystać z usługi selektora lokalizacji.
   
    Ponieważ środowisko App Service jest zasadniczo lokalizacji prywatne wdrożenie, pokazuje znajdujące się w lokalizacji. 
   
    ![][2]
   
    Po zaznaczeniu środowisko ASE w selektorze lokalizacji Tworzenie planu usługi App Service aktualizacje interfejsu użytkownika.  Lokalizacja jest teraz wyświetlana nazwa systemu środowiska ASE i regionu znajduje się w a cenową próbnika plan jest zastępowany przy użyciu selektora puli procesów roboczych.  
   
    ![][3]

### <a name="selecting-a-worker-pool"></a>Wybranie puli procesów roboczych
Zwykle w usłudze Azure App Service i poza środowisku usługi App Service, istnieją 3 rozmiarów wystąpień obliczeniowych, które są dostępne z opcją plan cenowy dedykowanych.  W podobny sposób za środowisko ASE można zdefiniować maksymalnie 3 pule procesów roboczych i określ rozmiar obliczeń, który jest używany dla tej puli procesów roboczych.  Co to oznacza dla dzierżawcy środowisku ASE jest, to zamiast wybierania planu cenowego przy użyciu rozmiaru obliczeń dla planu usługi App Service, możesz wybrać, co jest nazywane *puli procesów roboczych*.  

Wybór puli procesów roboczych interfejsu użytkownika pokazano rozmiaru obliczeń dla tej puli procesów roboczych poniżej nazwy.  Dostępna ilość odnosi się do obliczeń ile wystąpień są dostępne w tej puli.  Całkowitej puli faktycznie może mieć więcej wystąpień niż ta liczba, ale ta wartość dotyczy po prostu liczby nie są używane.  Jeśli wymagane jest dostosowanie środowiska App Service, aby dodać więcej zasobów obliczeniowych, zobacz [Konfigurowanie środowiska App Service Environment](app-service-web-configure-an-app-service-environment.md).

![][4]

W tym przykładzie można zobaczyć tylko dwa dostępne pule procesów roboczych. To, ponieważ administrator środowiska ASE przydzielone tylko hosty w tych pulach dwóch procesów roboczych.  Trzeci będą wyświetlane, gdy na maszynach wirtualnych przydzielonych do niego.  

## <a name="after-web-app-creation"></a>Po utworzeniu aplikacji sieci web
Istnieje kilka istotnych kwestii do uruchamiania aplikacji sieci web i zarządzania planów usługi App Service w środowisku ASE, które mają być brane pod uwagę.  

Jak wspomniano wcześniej, właściciel środowiska ASE jest odpowiedzialny za rozmiar systemu, a w rezultacie są one również odpowiedzialny za zapewnienie, że pojemność sieci jest wystarczająca do obsługi żądane plany usługi App Service. Jeśli nie ma żadnych dostępnych procesów roboczych, nie można utworzyć planu usługi App Service.  Dotyczy to również wartość true, skalowanie w górę aplikacji sieci web.  Jeśli potrzebujesz większej liczby wystąpień będzie mieć uzyskać administrator środowiska App Service Environment, aby dodać więcej pracowników.

Po utworzeniu aplikacji internetowej i planu usługi App Service jest dobrym pomysłem jest skalowanie w górę.  W środowisku ASE zawsze musi być co najmniej 2 wystąpienia planu usługi App Service w celu zapewnienia odporności na uszkodzenia dla aplikacji.  Skalowanie planu usługi App Service w środowisku ASE jest taka sama, jak zwykle za pośrednictwem planu usługi App Service interfejsu użytkownika.  Aby uzyskać więcej informacji na temat skalowania [jak skalować aplikację sieci web w środowisku usługi App Service](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png
[5]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/selectaspinase.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[Appserviceplans]: ../overview-hosting-plans.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoScale]: app-service-web-scale-a-web-app-in-an-app-service-environment.md
[HowtoConfigureASE]: app-service-web-configure-an-app-service-environment.md
[ResourceGroups]: ../../azure-resource-manager/resource-group-overview.md
[AzurePowershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
