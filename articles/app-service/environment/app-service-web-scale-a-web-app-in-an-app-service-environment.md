---
title: Skalowanie aplikacji w wersji ASE w wersji 1
description: Skalowanie aplikacji w środowisku usługi aplikacji. Ten doc jest dostępna tylko dla klientów, którzy używają starszej wersji ASE w wersji 1.
author: ccompy
ms.assetid: 78eb1e49-4fcd-49e7-b3c7-f1906f0f22e3
ms.topic: article
ms.date: 10/17/2016
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 43849ca7084f2237c37ad537c50f4e94ac4ea7c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74688677"
---
# <a name="scaling-apps-in-an-app-service-environment-v1"></a>Skalowanie aplikacji w środowisku usługi aplikacji w wersji 1
W usłudze Azure App Service zwykle istnieją trzy rzeczy, które można skalować:

* plan cenowy
* wielkość pracownika 
* liczby wystąpień.

W ASE nie ma potrzeby wybierania ani zmieniania planu cenowego.  Pod względem możliwości jest już na poziomie możliwości wyceny Premium.  

W odniesieniu do rozmiarów procesu roboczego administrator środowiska ASE może przypisać rozmiar zasobu obliczeniowego, który ma być używany dla każdej puli procesów roboczych.  Oznacza to, że w razie potrzeby można mieć pulę roboczą 1 z zasobami obliczeniowymi P4 i pulę roboczą 2 z zasobami obliczeniowymi P1.  Nie muszą być w porządku wielkości.  Szczegółowe informacje na temat rozmiarów i ich cen można znaleźć w dokumencie [tutaj Cennik usługi Azure App Service][AppServicePricing].  Pozostawia to opcje skalowania dla aplikacji sieci web i planów usługi app service w środowisku usługi aplikacji, aby:

* wybór puli pracowników
* liczba wystąpień

Zmiana któregokolwiek elementu odbywa się za pomocą odpowiedniego interfejsu użytkownika wyświetlanego dla planów usługi app hostowane przez ASE.  

![][1]

Nie można skalować w górę asp poza liczbę dostępnych zasobów obliczeniowych w puli procesów roboczych, w których znajduje się asp.  Jeśli potrzebujesz zasobów obliczeniowych w tej puli procesów roboczych, musisz uzyskać administratora środowiska ASE, aby je dodać.  Aby uzyskać informacje dotyczące ponownej konfiguracji środowiska ASE, przeczytaj informacje tutaj: [Jak skonfigurować środowisko usługi app service][HowtoConfigureASE].  Można również skorzystać z funkcji skalowania automatycznego ASE, aby dodać pojemność na podstawie harmonogramu lub metryk.  Aby uzyskać więcej informacji na temat konfigurowania skalowania automatycznego dla samego środowiska ASE, zobacz [Jak skonfigurować skalowanie automatyczne dla środowiska usługi app service][ASEAutoscale].

Można utworzyć wiele planów usługi aplikacji przy użyciu zasobów obliczeniowych z różnych pul procesów roboczych lub tej samej puli procesów roboczych.  Na przykład jeśli masz (10) dostępnych zasobów obliczeniowych w puli roboczej 1, możesz utworzyć jeden plan usługi aplikacji przy użyciu (6) zasobów obliczeniowych i drugi plan usługi aplikacji, który używa (4) zasobów obliczeniowych.

### <a name="scaling-the-number-of-instances"></a>Skalowanie liczby wystąpień
Po pierwszym utworzeniu aplikacji sieci web w środowisku usługi aplikacji rozpoczyna się od 1 wystąpienia.  Następnie można skalować w poziomie do dodatkowych wystąpień, aby zapewnić dodatkowe zasoby obliczeniowe dla aplikacji.   

Jeśli TWÓJ ASE ma wystarczającą pojemność, jest to dość proste.  Przejdź do planu usługi app service, który zawiera witryny, które chcesz skalować w górę i wybierz skalę.  Spowoduje to otwarcie interfejsu użytkownika, w którym można ręcznie ustawić skalę dla programu ASP lub skonfigurować reguły skalowania automatycznego dla programu ASP.  Aby ręcznie skalować aplikację, wystarczy ustawić ***skalę według*** ***liczby wystąpień, które wprowadzam ręcznie***.  W tym miejscu przeciągnij suwak do żądanej ilości lub wprowadź go w polu obok suwaka.  

![][2] 

Reguły skalowania automatycznego dla asp w ase działają tak samo, jak zwykle.  Można wybrać ***procent procesora CPU*** w obszarze ***Skala według*** i utworzyć reguły skalowania automatycznego dla asp na podstawie procentu procesora CPU lub utworzyć bardziej złożone reguły przy użyciu reguł ***harmonogramu i wydajności***.  Aby wyświetlić bardziej kompletne szczegóły dotyczące konfigurowania skalowania automatycznego, skorzystaj z [przewodnika Tutaj Skaluj aplikację w usłudze Azure App Service][AppScale]. 

### <a name="worker-pool-selection"></a>Wybór puli pracowników
Jak wspomniano wcześniej, wybór puli procesów procesowych jest dostępny z interfejsu użytkownika ASP.  Otwórz blok dla asp, który chcesz skalować i wybierz pulę procesów roboczych.  Zobaczysz wszystkie pule procesów roboczych, które zostały skonfigurowane w środowisku usługi app service.  Jeśli masz tylko jedną pulę pracowników, zobaczysz tylko jedną pulę na liście.  Aby zmienić pulę procesów asp, wystarczy wybrać pulę procesów roboczych, do której ma zostać przeniesione planowanie usługi app service.  

![][3]

Przed przeniesieniem asp z jednej puli pracowników do innej należy upewnić się, że będziesz mieć odpowiednią pojemność dla asp.  Na liście pul procesowych nie tylko jest nazwa puli procesów roboczych na liście, ale można również zobaczyć, ilu pracowników są dostępne w tej puli procesów roboczych.  Upewnij się, że dostępnych jest wystarczająco dużo wystąpień, aby zawierać plan usługi aplikacji.  Jeśli potrzebujesz więcej zasobów obliczeniowych w puli procesów procesowych, do których chcesz przenieść, poproś administratora środowiska ASE o ich dodanie.  

> [!NOTE]
> Przeniesienie asp z jednej puli procesów roboczych spowoduje zimne uruchomienie aplikacji w tym asp.  Może to spowodować, że żądania będą działać powoli, ponieważ aplikacja jest zimna uruchomiona na nowych zasobach obliczeniowych.  Zimnego rozruchu można uniknąć przy użyciu [funkcji rozgrzewania aplikacji][AppWarmup] w usłudze Azure App Service.  Moduł inicjowania aplikacji opisany w tym artykule działa również na rozruchy na zimno, ponieważ proces inicjowania jest również wywoływany, gdy aplikacje są zimne uruchomione na nowych zasobach obliczeniowych. 
> 
> 

## <a name="getting-started"></a>Wprowadzenie
Aby rozpocząć korzystanie ze środowisk usługi App Service, zobacz [Jak utworzyć środowisko usługi aplikacji][HowtoCreateASE]

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-aspblade.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-manualscale.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-sizescale.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[ScaleWebapp]: ../manage-scale-up.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoConfigureASE]: app-service-web-configure-an-app-service-environment.md
[CreateWebappinASE]: app-service-web-how-to-create-a-web-app-in-an-ase.md
[Appserviceplans]: ../overview-hosting-plans.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[AppScale]: ../manage-scale-up.md
[AppWarmup]: https://ruslany.net/2015/09/how-to-warm-up-azure-web-app-during-deployment-slots-swap/
