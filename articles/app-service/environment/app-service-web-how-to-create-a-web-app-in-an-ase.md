---
title: Tworzenie aplikacji sieci Web w wersji ASE w wersji 1
description: Dowiedz się, jak tworzyć aplikacje internetowe w środowisku usługi app service w wersji 1. Ten doc jest dostępna tylko dla klientów, którzy używają starszej wersji ASE w wersji 1.
author: ccompy
ms.assetid: 983ba055-e9e4-495a-9342-fd3708dcc9ac
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 5c947617f0c27708e72f9bff92e2b0041473cd92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266197"
---
# <a name="create-a-web-app-in-an-app-service-environment-v1"></a>Tworzenie aplikacji sieci Web w środowisku usługi app service w wersji 1

> [!NOTE]
> Ten artykuł dotyczy środowiska usługi app service w wersji 1.  Istnieje nowsza wersja środowiska usługi app service, która jest łatwiejsza w użyciu i działa na bardziej zaawansowanej infrastruktury. Aby dowiedzieć się więcej o nowej wersji, zacznij od [wprowadzenia do środowiska usługi app service](intro.md).
> 

## <a name="overview"></a>Omówienie
W tym samouczku pokazano, jak utworzyć aplikacje sieci Web i plany usługi App Service w [środowisku usługi app service w wersji 1](app-service-app-service-environment-intro.md) (ASE). 

> [!NOTE]
> Jeśli chcesz dowiedzieć się, jak utworzyć aplikację sieci web, ale nie trzeba tego robić w środowisku usługi aplikacji, zobacz [Tworzenie aplikacji sieci web .NET](../app-service-web-get-started-dotnet.md) lub jednego z powiązanych samouczków dla innych języków i struktur.
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne
W tym samouczku przyjęto założenie, że utworzono środowisko usługi app service. Jeśli jeszcze tego nie zrobiono, zobacz [Tworzenie środowiska usługi app service](app-service-web-how-to-create-an-app-service-environment.md). 

## <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej
1. W [witrynie Azure Portal](https://portal.azure.com/)kliknij pozycję **Utwórz zasób > aplikacji Sieci Web + Mobilna > Sieci Web**. 
   
    ![][1]
2. Wybierz subskrypcję.  
   
    Jeśli masz wiele subskrypcji należy pamiętać, że do tworzenia aplikacji w środowisku usługi App Service, należy użyć tej samej subskrypcji, która została użyta podczas tworzenia środowiska. 
3. Wybierz lub utwórz grupę zasobów.
   
    *Grupy zasobów* umożliwiają zarządzanie powiązanymi zasobami platformy Azure jako jednostką i są przydatne podczas ustanawiania reguł *kontroli dostępu opartej na rolach* (RBAC) dla aplikacji. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager][ResourceGroups]. 
4. Wybierz lub utwórz plan usługi App Service.
   
    *Plany usługi app service* są zarządzanymi zestawami aplikacji sieci web.  Zwykle po wybraniu ceny naliczona cena jest stosowana do planu usługi app service, a nie do poszczególnych aplikacji. W ASE płacisz za wystąpienia obliczeniowe przydzielone do środowiska ASE, a nie za to, co zostało wymienione w asp.  Aby zwiększyć liczbę wystąpień aplikacji sieci web, skalujesz w górę wystąpienia planu usługi App Service i wpływa na wszystkie aplikacje sieci web w tym planie.  Niektóre funkcje, takie jak gniazda lokacji lub integracja sieci wirtualnej, mają również ograniczenia ilościowe w planie.  Aby uzyskać więcej informacji, zobacz [Omówienie planów usługi Azure App Service](../overview-hosting-plans.md)
   
    Można zidentyfikować plany usługi app service w ase, patrząc na lokalizację, która jest podnazjęciem planu.  
   
    ![][5]
   
    Jeśli chcesz użyć planu usługi app service, który już istnieje w środowisku usługi app service, wybierz ten plan. Jeśli chcesz utworzyć nowy plan usługi app service, zobacz [następującą sekcję](#createplan)tego samouczka Tworzenie planu usługi app service w środowisku usługi aplikacji .
5. Wprowadź nazwę aplikacji sieci Web, a następnie kliknij przycisk **Utwórz**. 
   
    Jeśli twój ASE używa zewnętrznego adresu VIP, adres URL aplikacji w ase jest: [*nazwa witryny*]. [*nazwa środowiska usługi app service*]. p.azurewebsites.net zamiast [ nazwa*witryny*]. azurewebsites.net
   
    Jeśli twój ASE używa wewnętrznego adresu VIP, adres URL aplikacji w tym ASE to: [*nazwa witryny*]. [*poddomena określona podczas tworzenia ASE*]   
    Po wybraniu asp podczas tworzenia ASE zobaczysz aktualizację poddomeny poniżej **Nazwy**

## <a name="create-an-app-service-plan"></a><a name="createplan"></a>Tworzenie planu usługi aplikacji
Podczas tworzenia planu usługi aplikacji w środowisku usługi aplikacji, wybory procesu roboczego są różne, ponieważ nie ma żadnych udostępnionych pracowników w środowisku ASE.  Pracownicy, których musisz użyć, to te, które zostały przydzielone do ASE przez administratora.  Oznacza to, że aby utworzyć nowy plan, musisz mieć więcej pracowników przydzielonych do puli pracowników ASE niż całkowita liczba wystąpień we wszystkich planach już w tej puli pracowników.  Jeśli nie masz wystarczającej liczby pracowników w puli pracowników ASE, aby utworzyć plan, musisz współpracować z administratorem ASE, aby je dodać.

Inną różnicą w planach usługi app service obsługiwanych przez środowisko usługi app service jest brak wyboru cen.  Jeśli masz środowisko usługi app service płacisz za zasoby obliczeniowe używane przez system i nie masz dodatkowych opłat za plany w tym środowisku.  Zwykle podczas tworzenia planu usługi app service wybierasz plan cenowy, który określa rozliczenia.  Środowisko usługi aplikacji jest zasadniczo prywatną lokalizacją, w której można tworzyć zawartość.  Płacisz za środowisko i nie hostujesz swoich treści.

Poniższe instrukcje pokazują, jak utworzyć plan usługi app service podczas tworzenia aplikacji sieci web, jak wyjaśniono w poprzedniej sekcji samouczka.

1. Kliknij **pozycję Utwórz nowy** w interfejsie użytkownika wyboru planu i podaj nazwę planu, tak jak zwykle poza interfejsem ASE.
2. Wybierz ASE, którego chcesz użyć, z selektora lokalizacji.
   
    Ponieważ środowisko usługi aplikacji jest zasadniczo prywatną lokalizacją wdrożenia, jest wyświetlane w obszarze Lokalizacja. 
   
    ![][2]
   
    Po wybraniu ASE w selektorze lokalizacji usługi app service planuje tworzenie aktualizacji interfejsu użytkownika.  Lokalizacja pokazuje teraz nazwę systemu ASE i region, w którym się znajduje, a selektor planu cenowego jest zastępowany selektorem puli pracowników.  
   
    ![][3]

### <a name="selecting-a-worker-pool"></a>Wybieranie puli pracowników
Zwykle w usłudze Azure App Service i poza środowiskiem usługi aplikacji istnieją 3 rozmiary obliczeń, które są dostępne z wyborem dedykowanego planu cenowego.  W podobny sposób dla środowiska ASE można zdefiniować maksymalnie 3 pule pracowników i określić rozmiar obliczeń, który jest używany dla tej puli procesów roboczych.  Co to oznacza dla dzierżawców środowiska ASE, że zamiast wybierać plan cenowy o rozmiarze obliczeniowym dla planu usługi App Service, należy wybrać tak zwaną *pulę pracowników.*  

Interfejs użytkownika wyboru puli procesów procesowych pokazuje rozmiar obliczeń używany dla tej puli procesów roboczych poniżej nazwy.  Dostępna ilość odnosi się do liczby wystąpień obliczeniowych dostępnych do użycia w tej puli.  Całkowita pula może rzeczywiście mieć więcej wystąpień niż ta liczba, ale ta wartość odnosi się po prostu, ile nie są używane.  Jeśli chcesz dostosować środowisko usługi App Service, aby dodać więcej zasobów obliczeniowych, zobacz [Konfigurowanie środowiska usługi App Service](app-service-web-configure-an-app-service-environment.md).

![][4]

W tym przykładzie są dostępne tylko dwie pule procesów roboczych. Dzieje się tak, ponieważ administrator środowiska ASE przydzielał hosty tylko do tych dwóch pul procesów roboczych.  Trzeci pojawi się, gdy istnieją maszyny wirtualne przydzielone do niego.  

## <a name="after-web-app-creation"></a>Po utworzeniu aplikacji sieci Web
Istnieje kilka zagadnień dotyczących uruchamiania aplikacji sieci web i zarządzania planami usługi App Service w ase, które należy wziąć pod uwagę.  

Jak wspomniano wcześniej, właściciel środowiska ASE jest odpowiedzialny za rozmiar systemu i w rezultacie są również odpowiedzialni za zapewnienie wystarczającej pojemności do obsługi żądanych planów usługi app service. Jeśli nie ma dostępnych pracowników, nie będzie można utworzyć planu usługi App Service.  Dotyczy to również skalowania aplikacji sieci web.  Jeśli potrzebujesz więcej wystąpień, musisz uzyskać administratora środowiska usługi App Service, aby dodać więcej pracowników.

Po utworzeniu aplikacji sieci web i planu usługi App Service warto ją skalować.  W ase zawsze trzeba mieć co najmniej 2 wystąpienia planu usługi App Service, aby zapewnić odporność na uszkodzenia dla aplikacji.  Skalowanie planu usługi aplikacji w ase jest taka sama jak zwykle za pośrednictwem interfejsu użytkownika planu usługi app service.  Aby uzyskać więcej informacji na temat skalowania, [jak skalować aplikację sieci web w środowisku usługi aplikacji](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

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
[ResourceGroups]: ../../azure-resource-manager/management/overview.md
[AzurePowershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
