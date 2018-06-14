---
title: Tworzenie aplikacji sieci web w wersji 1 środowiska usługi aplikacji
description: Informacje o sposobie tworzenia aplikacji sieci web i aplikacji planów usługi v1 środowiska usługi aplikacji
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
ms.date: 7/11/2017
ms.author: ccompy
ms.openlocfilehash: 1e8540409c6174ad02bd2d9d57c53e0279f49871
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
ms.locfileid: "29386915"
---
# <a name="create-a-web-app-in-an-app-service-environment-v1"></a>Tworzenie aplikacji sieci web w wersji 1 środowiska usługi aplikacji

> [!NOTE]
> Ten artykuł dotyczy v1 środowiska usługi aplikacji.  Istnieje nowsza wersja środowiska usługi aplikacji jest łatwiejsza w użyciu, który jest uruchamiany na bardziej zaawansowanych infrastruktury. Aby dowiedzieć się więcej o nowy początek wersji z [wprowadzenie do środowiska usługi aplikacji](intro.md).
> 

## <a name="overview"></a>Przegląd
W tym samouczku przedstawiono sposób tworzenia aplikacji sieci web i planów usługi App Service w [v1 środowiska usługi aplikacji](app-service-app-service-environment-intro.md) (ASE). 

> [!NOTE]
> Jeśli chcesz dowiedzieć się, jak utworzyć aplikację sieci web, ale nie trzeba go w środowisku usługi aplikacji, zobacz [utworzenia aplikacji sieci web .NET](../app-service-web-get-started-dotnet.md) lub w jednym z powiązanych samouczków dla innych języków i struktur.
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne
W tym samouczku założono, że utworzono środowiska usługi aplikacji. Jeśli użytkownik jeszcze nie, zobacz [tworzenie środowiska usługi aplikacji](app-service-web-how-to-create-an-app-service-environment.md). 

## <a name="create-a-web-app"></a>Tworzenie aplikacji sieci Web
1. W [Azure Portal](https://portal.azure.com/), kliknij przycisk **tworzenie zasobu > sieci Web i mobilność > Aplikacja sieci Web**. 
   
    ![][1]
2. Wybierz subskrypcję.  
   
    Jeśli masz wiele subskrypcji, należy pamiętać, że w środowisku usługi aplikacji, należy utworzyć aplikację, należy użyć tej samej subskrypcji, który został użyty podczas tworzenia środowiska. 
3. Wybierz lub utwórz grupę zasobów.
   
    *Grupy zasobów* pozwala na zarządzanie jako jednostka powiązanych zasobów systemu Azure i są przydatne podczas ustanawiania *kontroli dostępu opartej na rolach* reguły (RBAC) dla aplikacji. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager][ResourceGroups]. 
4. Wybierz lub utwórz plan usługi App Service.
   
    *Planów usługi App Service* zarządzanych zestawów aplikacji sieci web.  Zwykle po wybraniu cennika, pobierana jest stosowane do planu usług aplikacji, a nie do poszczególnych aplikacji. W elemencie ASE płacisz za wystąpienia obliczeniowe przydzielone do ASE zamiast co zostały wymienione w Twojej aplikacji ASP.  Aby skalować liczbę wystąpień aplikacji sieci web, które skalowanie w górę wystąpień usługi aplikacji planu i ma wpływ na wszystkie aplikacje sieci web w tym planie.  Niektóre funkcje, takie jak witryny gniazda lub integracji sieci Wirtualnej również podlegają ograniczeniom ilość w planie.  Aby uzyskać więcej informacji, zobacz [omówienie planów usługi aplikacji Azure](../azure-web-sites-web-hosting-plans-in-depth-overview.md)
   
    Można zidentyfikować planów usługi aplikacji w Twojej ASE, analizując lokalizacji, która jest odnotowany w obszarze Nazwa planu.  
   
    ![][5]
   
    Jeśli chcesz użyć plan usługi aplikacji, która już istnieje w środowisku usługi aplikacji, wybierz plan. Jeśli chcesz utworzyć nowy plan usługi aplikacji, zobacz następującą sekcję tego samouczka [Utwórz plan usługi aplikacji w środowisku usługi aplikacji](#createplan).
5. Wprowadź nazwę dla aplikacji sieci web, a następnie kliknij przycisk **Utwórz**. 
   
    Jeśli Twoje ASE używa zewnętrznego adresu VIP jest adres URL aplikacji w elemencie ASE: [*sitename*]. [ *Nazwa środowiska usługi aplikacji*]. p.azurewebsites.net zamiast [*sitename*]. azurewebsites.net
   
    Jeśli Twoje ASE używa wewnętrznego adresu VIP, adres URL aplikacji, w tym jest ASE: [*sitename*]. [ *poddomeny określone podczas tworzenia ASE*]   
    Po wybraniu strona ASP podczas tworzenia ASE zobaczysz poddomeny zaktualizować poniżej **nazwy**

## <a name="createplan"></a> Tworzenie planu usługi aplikacji
Po utworzeniu planu usługi aplikacji w środowisku usługi aplikacji wybrane opcje procesu roboczego są różne, ponieważ w elemencie ASE nie nie udostępnionego pracowników.  Procesy robocze, które należy użyć są te, które zostały już przydzielone ASE przez administratora.  Oznacza to, że aby utworzyć nowy plan, musisz mieć więcej pracowników przydzielonych do procesu roboczego puli ASE niż całkowita liczba wystąpień dla wszystkich planów już w tej puli procesów roboczych.  Jeśli nie ma wystarczającej liczby procesów roboczych w puli ASE procesu roboczego do utworzenia planu, należy się z administratorem ASE, aby udostępnić je dodać.

Inna różnica z planami usługi aplikacji obsługiwanych przez środowisko usługi aplikacji jest Brak ceny zaznaczenia.  Jeśli masz środowisko usługi aplikacji płatność za zasoby obliczeniowe używaną przez system i nie masz dodano opłat za plany w tym środowisku.  Zwykle podczas tworzenia planu usługi App Service wybrać planu cenowego, która określa rozliczeniowego.  Środowiska usługi aplikacji jest zasadniczo lokalizację prywatnego umożliwiającego utworzenie zawartości.  Płaci się środowiska i nie można udostępnić zawartość.

Poniższe instrukcje przedstawiają sposób utworzyć plan usługi aplikacji podczas tworzenia aplikacji sieci web, zgodnie z objaśnieniem w poprzedniej sekcji samouczka.

1. Kliknij przycisk **Utwórz nowy** w zaznaczeniu planu interfejsu użytkownika i podaj nazwę dla planu, tak jak zwykle poza ASE.
2. Wybierz ASE, który ma być używany z Twojej lokalizacji selektora.
   
    Ponieważ środowisko usługi aplikacji jest zasadniczo lokalizacji prywatne wdrażanie, przedstawia on znajdujące się w lokalizacji. 
   
    ![][2]
   
    Po zaznaczeniu ASE w selektorze lokalizacji Tworzenie planu usługi aplikacji — aktualizacje interfejsu użytkownika.  Lokalizacja teraz wyświetlana nazwa systemu ASE i region znajduje się on w i selektor cenową planu jest zastępowany selektora puli procesu roboczego.  
   
    ![][3]

### <a name="selecting-a-worker-pool"></a>Wybieranie puli procesów roboczych
Zwykle w usłudze Azure App Service i poza nimi środowiska usługi aplikacji istnieje 3 rozmiary obliczeniowe, które są dostępne w przypadku zaznaczenia planu cenie dedykowanym.  W podobny sposób dla ASE można zdefiniować maksymalnie 3 pule pracowników i określ rozmiar obliczeń, który jest używany dla tej puli procesów roboczych.  Który znaczenia dla dzierżaw ASE jest to zamiast wybrania planu cenowego o rozmiarze obliczeniowe dla planu usługi aplikacji, wybierz, co jest nazywane *puli procesów roboczych*.  

Zaznaczenie procesu roboczego puli interfejsu użytkownika pokazano rozmiar obliczeń, używany dla tej puli procesów roboczych pod nazwą.  Dostępna ilość odwołuje się do wyliczenia liczbę wystąpień są dostępne w tej puli.  Całkowitej puli faktycznie może mieć więcej wystąpień niż ten numer, ale ta wartość odwołuje się do po prostu ile nie są używane.  Jeśli wymagane jest dostosowanie środowiska usługi aplikacji, aby dodać więcej zasoby obliczeniowe, zobacz [Konfigurowanie środowiska usługi aplikacji](app-service-web-configure-an-app-service-environment.md).

![][4]

W tym przykładzie widać tylko dwa dostępne pule procesów roboczych. Wynika to z administratora ASE przydzielone tylko hosty w pulach tych dwóch procesu roboczego.  Trzeci będzie wyświetlany, gdy są przydzielone do niej maszyny wirtualne.  

## <a name="after-web-app-creation"></a>Po utworzeniu aplikacji sieci web
Istnieje kilka zagadnień dotyczących uruchamianie aplikacji sieci web i zarządzanie planami usługi aplikacji w elemencie ASE, które należy wziąć pod uwagę.  

Jak wspomniano wcześniej, właściciel ASE jest odpowiedzialny za rozmiar systemu i w związku z tym również są odpowiedzialne za zapewnienie, że jest wystarczająco duże, aby hostować odpowiednią planów usługi aplikacji. Jeśli nie ma żadnych dostępnych pracowników, nie można utworzyć planu usługi aplikacji.  Dotyczy to również wartość PRAWDA, aby skalowaniu aplikacji sieci web.  Jeśli potrzebujesz więcej wystąpień następnie trzeba uzyskać administrator środowiska usługi aplikacji, aby dodać więcej pracowników.

Po utworzeniu aplikacji sieci web i plan usługi aplikacji jest dobrym pomysłem jest skalowanie w górę.  W elemencie ASE zawsze musisz mieć co najmniej 2 wystąpienia swój plan usługi aplikacji, aby zapewnić odporność na uszkodzenia dla aplikacji.  Skalowanie planu usługi App Service w elemencie ASE jest taka sama, jak zwykle za pomocą interfejsu użytkownika plan usługi aplikacji.  Aby uzyskać więcej informacji na temat skalowania [jak skalować aplikacji sieci web w środowisku usługi aplikacji](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png
[5]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/selectaspinase.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[Appserviceplans]: ../azure-web-sites-web-hosting-plans-in-depth-overview.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoScale]: app-service-web-scale-a-web-app-in-an-app-service-environment.md
[HowtoConfigureASE]: app-service-web-configure-an-app-service-environment.md
[ResourceGroups]: ../../azure-resource-manager/resource-group-overview.md
[AzurePowershell]: http://azure.microsoft.com/documentation/articles/powershell-install-configure/
