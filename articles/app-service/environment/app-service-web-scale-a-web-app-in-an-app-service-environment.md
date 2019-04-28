---
title: Jak skalować aplikację w środowisku usługi App Service — platformy Azure
description: Skalowanie aplikacji w środowisku usługi App Service
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
ms.custom: seodec18
ms.openlocfilehash: 6e683eb07b690d7d5680b7a4d429d1150f22f67e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60767719"
---
# <a name="scaling-apps-in-an-app-service-environment"></a>Skalowanie aplikacji w środowisku usługi App Service
W usłudze Azure App Service są zwykle trzy rzeczy, które można skalować:

* Plan taryfowy
* rozmiar procesu roboczego 
* Liczba wystąpień.

W środowisku ASE nie ma potrzeby wybrać lub zmienić plan taryfowy.  Pod względem funkcji jest już na poziomie możliwości cenowej Premium.  

W odniesieniu do rozmiarów procesu roboczego Administrator środowiska ASE można przypisać rozmiar zasobów obliczeniowych, które ma być używany dla każdej puli procesów roboczych.  Czy oznacza, że masz 1 puli procesów roboczych P4 zasoby obliczeniowe i procesu roboczego puli 2 z zasoby obliczeniowe P1, w razie potrzeby.  Nie muszą być uporządkowane w kolejności rozmiar.  Szczegółowe informacje dotyczące rozmiarów i ich ceny można znaleźć dokumentu w tym miejscu [usługi Azure App Service — ceny][AppServicePricing].  Spowoduje to, że opcje skalowania dla aplikacji sieci web i planów usługi App Service w środowisku usługi App Service jako:

* Wybór puli procesów roboczych
* Liczba wystąpień

Zmiana albo element odbywa się za pośrednictwem odpowiedni interfejs użytkownika wyświetlany dla środowiska ASE hostowanych w planach usługi App Service.  

![][1]

Nie można skalować przekracza liczbę zasobów obliczeniowych dostępnych w puli procesów roboczych, które strona ASP jest strona ASP.  Jeśli potrzebujesz obliczenia zasobów w puli procesów roboczych należy uzyskać administrator środowiska ASE, aby dodać je.  Aby uzyskać informacje dotyczące ponowne konfigurowanie środowiska ASE przeczytać informacje w tym miejscu: [Jak skonfigurować środowisko App Service environment][HowtoConfigureASE].  Można również korzystać z funkcji automatycznego skalowania środowiska ASE można dodać pojemność na podstawie harmonogramu lub metryk.  Aby uzyskać więcej informacji na temat konfigurowania automatycznego skalowania dla środowiska ASE, sama zobacz [Konfigurowanie skalowania automatycznego dla środowiska App Service Environment][ASEAutoscale].

Można utworzyć wiele aplikacji planów usług z pul innego procesu roboczego przy użyciu zasobów obliczeniowych, lub można użyć tej samej puli procesów roboczych.  Na przykład jeśli masz (10) zasobów obliczeniowych dostępnych na 1 puli procesów roboczych, można utworzyć jeden plan usługi app service przy użyciu zasobów obliczeniowych (6) i drugi usługi app service plan, który używa (4) zasoby obliczeniowe.

### <a name="scaling-the-number-of-instances"></a>Skalowanie liczby wystąpień
Podczas tworzenia aplikacji sieci web w środowisku usługi App Service uruchamia z 1 wystąpieniem.  Następnie można skalować do dodatkowych wystąpień, aby zapewnić dodatkowe zasoby obliczeniowe dla aplikacji.   

Jeśli środowisko ASE ma wystarczające moce przerobowe, a następnie jest to dość proste.  Możesz przejść do planu usługi App Service zawierający witryn, który chcesz skalować w górę, a następnie wybierz pozycję skali.  Spowoduje to otwarcie interfejsu użytkownika, w którym można ręcznie ustawić skali dla Twojego środowiska ASP lub skonfigurować reguły skalowania automatycznego dla Twojego środowiska ASP.  Aby ręcznie skalować aplikację po prostu ustaw ***skalowanie przez*** do ***liczba wystąpień ustawiana ręcznie***.  W tym miejscu przeciągnij suwak na żądaną ilość lub wprowadź go w polu obok suwaka.  

![][2] 

Reguły skalowania automatycznego dla stron ASP w środowisku ASE działać tak samo, jak zwykle.  Możesz wybrać ***procent użycia procesora CPU*** w obszarze ***skalowanie przez*** i utworzyć strona ASP na podstawie procent użycia procesora CPU lub możesz można tworzyć bardziej złożone zasady przy użyciu reguł skalowania automatycznego ***reguły harmonogramu i wydajności*** .  Aby wyświetlić bardziej szczegółowe informacje na temat konfigurowania automatycznego skalowania za pomocą przewodnika tutaj [skalowanie aplikacji w usłudze Azure App Service][AppScale]. 

### <a name="worker-pool-selection"></a>Wybór puli procesów roboczych
Jak wspomniano wcześniej, wybór puli procesów roboczych jest dostępny z poziomu interfejsu użytkownika ASP.  Otwórz blok dla stron ASP, który chcesz skalować, a następnie wybierz pulę procesów roboczych.  Zobaczysz wszystkie pule procesów roboczych, które zostały skonfigurowane w środowisku App Service Environment.  Jeśli masz tylko jednego procesu roboczego puli następnie widoczne są tylko jedną pulę na liście.  Aby zmienić puli procesów roboczych, które strona ASP znajduje się w, po prostu wybierz pulę procesów roboczych ma planu usługi App Service można przenieść do.  

![][3]

Przed umieszczeniem jednego procesu roboczego puli strona ASP jest ważne upewnić się, że użytkownik będzie miał odpowiednią zdolność strona ASP.  Na liście pule procesów roboczych nie tylko znajduje się nazwa puli procesów roboczych, ale możesz też sprawdzić, jak wiele procesów roboczych są dostępne w puli procesów roboczych.  Upewnij się, że nie istnieją wystarczającej liczby wystąpień, które są dostępne dla zawierają planu usługi App Service.  Potrzebujesz bardziej zasoby obliczeniowe, w puli procesów roboczych, które chcesz przenieść, otrzymują administratorem środowiska ASE, aby je dodać.  

> [!NOTE]
> Przeniesienie ASP z jednego procesu roboczego puli spowoduje, że zimnego uruchamiania aplikacji ASP.  Może to powodować spowolnienie działania, jak Twoja aplikacja jest zimno pracę na nowym zasobom obliczeniowym żądania.  Można uniknąć zimny start za pomocą [rozgrzewanie możliwości aplikacji] [ AppWarmup] w usłudze Azure App Service.  Moduł inicjowania aplikacji, opisanej w artykule działa również w zimnych startów ponieważ proces inicjowania jest również wywoływany, gdy aplikacje są zimne pracę na nowym zasobom obliczeniowym. 
> 
> 

## <a name="getting-started"></a>Wprowadzenie
Aby rozpocząć pracę przy użyciu środowisk usługi App Service, zobacz [jak do utworzyć środowisko App Service Environment][HowtoCreateASE]

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
[Appserviceplans]: ../overview-hosting-plans.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[AppScale]: ../web-sites-scale.md
[AppWarmup]: https://ruslany.net/2015/09/how-to-warm-up-azure-web-app-during-deployment-slots-swap/
