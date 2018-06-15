---
title: Jak skalować aplikację w środowisku usługi aplikacji
description: Skalowanie aplikacji w środowisku usługi aplikacji
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: jimbe
ms.assetid: 78eb1e49-4fcd-49e7-b3c7-f1906f0f22e3
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2016
ms.author: ccompy
ms.openlocfilehash: d04a5fce920dae25507cdf2f64832574e24c51dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23836553"
---
# <a name="scaling-apps-in-an-app-service-environment"></a>Skalowanie aplikacji w środowisku usługi App Service
W usłudze Azure App Service są zwykle trzy czynności, które można skalować:

* Cennik planu
* rozmiar procesu roboczego 
* Liczba wystąpień.

W elemencie ASE jest niepotrzebna, aby wybrać lub zmienić planu cenowego.  Pod względem możliwości jest już na poziomie możliwości cenowej Premium dostępne.  

W odniesieniu do rozmiarów procesu roboczego ASE admin można przypisać rozmiar zasobów obliczeniowych do użycia dla każdej puli procesów roboczych.  Czy oznacza, że proces roboczy puli 1 z P4 zasobów obliczeniowych i proces roboczy puli 2 z P1 zasoby obliczeniowe, w razie potrzeby.  Nie mają być w kolejności rozmiar.  Szczegółowe informacje wokół rozmiary i ich cen można znaleźć tutaj dokumentu [Azure App Service — ceny][AppServicePricing].  Spowoduje to pozostawienie opcji skalowania dla aplikacji sieci web i plany usługi App Service w środowisku usługi aplikacji jako:

* proces roboczy puli zaznaczenia
* Liczba wystąpień

Zmiana oba elementy odbywa się za pośrednictwem interfejsu użytkownika odpowiednie pokazywane dla Twojego ASE hostowanej plany usługi App Service.  

![][1]

Nie można skalować strona ASP poza liczba zasobów obliczeniowych dostępnych w puli procesów roboczych, który jest strona ASP.  Należy obliczyć zasobów w tej puli procesów roboczych należy uzyskać od administratora ASE, aby dodać je.  Dla informacji wokół ponowne konfigurowanie Twojego ASE przeczytaj informacje w tym miejscu: [jak skonfigurować środowisko usługi aplikacji][HowtoConfigureASE].  Można również korzystać z funkcji skalowania automatycznego ASE, aby dodać pojemności na podstawie harmonogramu lub metryki.  Aby uzyskać więcej informacji na temat konfigurowania automatycznego skalowania dla środowiska ASE się w temacie [Konfigurowanie automatycznego skalowania dla środowiska usługi aplikacji][ASEAutoscale].

Można utworzyć wiele aplikacji przy użyciu zasobów obliczeniowych od pule procesów roboczych różnych planów usług lub można użyć tej samej puli procesów roboczych.  Na przykład jeśli masz (10) zasobów obliczeniowych dostępnych na proces roboczy puli 1, można utworzyć jeden plan usługi app service przy użyciu zasobów obliczeniowych (6) i plan usługi aplikacji — druga używającej (4) zasoby obliczeniowe.

### <a name="scaling-the-number-of-instances"></a>Skalowanie liczby wystąpień
Podczas tworzenia aplikacji sieci web w środowisku usługi aplikacji rozpoczyna się od 1 wystąpienie.  Możesz następnie skalować w poziomie do dodatkowe wystąpienia, aby zapewnić zasoby obliczeniowe dla aplikacji.   

Jeśli Twoje ASE ma wystarczającą wydajność to bardzo proste.  Przejdź do planu usługi aplikacji przechowujący witrynami, które chcesz skalowanie w górę i wybierz skali.  Spowoduje to otwarcie interfejsu użytkownika, w którym można ręcznie ustawienie skali strona ASP lub skonfigurować reguły automatycznego skalowania dla Twojego środowiska ASP.  Aby ręcznie skalować aplikację po prostu ustaw ***skalowanie przez*** do ***liczba wystąpień ustawiana ręcznie***.  W tym miejscu przeciągnij suwak do żądanej ilości lub wprowadzić w polu obok suwaka.  

![][2] 

Reguły automatycznego skalowania dla środowiska ASP w elemencie ASE działać w identyczny sposób jak zwykle.  Możesz wybrać ***procent użycia procesora CPU*** w obszarze ***skalowanie przez*** i utworzyć zasady automatycznego skalowania do programu ASP na podstawie procent użycia procesora CPU lub można utworzyć bardziej złożone zasady przy użyciu ***reguły wydajności i harmonogram***.  Aby wyświetlić bardziej szczegółowe informacje na temat konfigurowania automatycznego skalowania za pomocą przewodnika tutaj [skalować aplikację w usłudze Azure App Service][AppScale]. 

### <a name="worker-pool-selection"></a>Proces roboczy puli zaznaczenia
Jak wspomniano wcześniej, zaznaczenie procesu roboczego puli jest dostępny z poziomu interfejsu użytkownika ASP.  Otwarcie bloku dla środowiska ASP, który chcesz skalować, a następnie wybierz puli procesów roboczych.  Są wyświetlane wszystkie pule procesów roboczych, które zostały skonfigurowane w środowisku usługi aplikacji.  Jeśli masz tylko jednego procesu roboczego puli następnie wyświetlany tylko jednej puli na liście.  Aby zmienić puli procesów roboczych, jaką strona ASP znajduje się w, po prostu zaznacz puli procesów roboczych ma Twojego planu usługi App Service można przenieść do.  

![][3]

Przed umieszczeniem jednego procesu roboczego puli strona ASP koniecznie upewnij się, że konieczne będzie odpowiedniej wydajności dla Twojego środowiska ASP.  Na liście pule procesów roboczych nie tylko znajduje się nazwa procesu roboczego puli, ale możesz też sprawdzić liczbę pracowników są dostępne w tej puli procesów roboczych.  Upewnij się, że ma wystarczającej liczby wystąpień mogła zawierać planu usługi aplikacji.  Zasoby w puli procesów roboczych, które chcesz przejść do bardziej muszą obliczeniowe, pobrać ASE administratorem, aby dodać je.  

> [!NOTE]
> Uruchamia przeniesienie spowoduje, że chłodni ASP z jednego procesu roboczego puli aplikacji w ASP.  Może to spowodować żądań działanie aplikacji jest zimnych uruchomiona na nowe zasoby obliczeniowe.  Można uniknąć zimny start przy użyciu [aplikacji ciepłych się możliwość] [ AppWarmup] w usłudze Azure App Service.  Moduł Inicjowanie aplikacji, opisanej w artykule działa także dla zimnych startów ponieważ proces inicjowania jest również wywoływana, gdy aplikacje są zimnych uruchomiona na nowe zasoby obliczeniowe. 
> 
> 

## <a name="getting-started"></a>Wprowadzenie
Wprowadzenie do środowiska usługi App Service, zobacz [jak do tworzenia środowiska usługi aplikacji][HowtoCreateASE]

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-aspblade.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-manualscale.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-sizescale.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[ScaleWebapp]: ../web-sites-scale.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoConfigureASE]: app-service-web-configure-an-app-service-environment.md
[CreateWebappinASE]: app-service-web-how-to-create-a-web-app-in-an-ase.md
[Appserviceplans]: ../azure-web-sites-web-hosting-plans-in-depth-overview.md
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[AppScale]: ../web-sites-scale.md
[AppWarmup]: http://ruslany.net/2015/09/how-to-warm-up-azure-web-app-during-deployment-slots-swap/
