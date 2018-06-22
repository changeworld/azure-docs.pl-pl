---
title: Wprowadzenie do środowisk opartych na maszynach wirtualnych nauki danych zespołu - Azure | Dokumentacja firmy Microsoft
description: Wzorce wdrażania maszyny Wirtualnej nauki danych w środowisku przedsiębiorstwa zespołu.
keywords: bezpośrednie uczenia AI, narzędzia do analizy danych, maszyna wirtualna nauki danych, dane geograficzne analytics, proces nauki danych zespołu
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 8486b0be1fb5e1385da3c7ad55f6410a1059df93
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309252"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Analizy na podstawie maszyny wirtualnej nauki zespołu danych i środowiska AI 
[Maszyny wirtualnej nauki danych](overview.md) (DSVM) udostępnia bogate środowisko na platformie Azure przy użyciu oprogramowania wbudowane sztucznego analizy (AI) i analizy danych. 

Tradycyjnie DSVM został użyty jako pulpitu poszczególnych analytics. Analityków danych poszczególnych zwiększyć wydajność, wykorzystując to pojęcie udostępnionego środowiska wbudowane analytics. Jak analytics duże zespoły zaplanować środowiskami analytics ich AI i analityków danych, jeden cyklicznego motywów to infrastruktura analytics udostępnionego dla rozwoju i eksperymenty. Ta infrastruktura odbywa się zgodnie z rozwiązaniami INFORMATYCZNYMI dla firm zasad, które ułatwiają również współpracy i spójność zespołów nauki/analizy danych. 

Umożliwia również współużytkowanej infrastruktury IT na lepsze wykorzystanie środowiska analytics. Niektóre organizacje wywołać infrastruktury nauki/analiza danych zespołów "analytics piaskownicy." Umożliwia on analityków danych dostęp do różnych zasobów danych, aby szybko zrozumieć dane, uruchamiać eksperymenty, sprawdzanie poprawności hipotez i tworzenie modeli predykcyjnych bez wpływu na środowisko produkcyjne. 

Ponieważ DSVM działa na poziomie infrastruktury platformy Azure, Administratorzy IT mogą łatwo skonfigurować DSVM działać zgodnie z zasadami IT przedsiębiorstwa. DSVM oferuje pełną elastyczność we wdrażaniu różnych architektur udostępniania dostęp do zasobów firmowych danych w określony sposób. 

W tej sekcji omówiono niektóre wzorców i wskazówki, których można użyć do wdrożenia DSVM jako infrastruktury nauki danych zespołów. Bloków konstrukcyjnych dla tych wzorców pochodzą z infrastruktury platformy Azure jako usługa (IaaS), dlatego są one stosowane do dowolnego maszynach wirtualnych platformy Azure. Ta seria artykułów koncentruje się na stosowanie tych możliwości standardowych infrastruktury platformy Azure na maszynie wirtualnej analizy danych. 

Niektóre z klucza bloków konstrukcyjnych w środowisku przedsiębiorstwa zespołu analytics to:

* [Funkcję puli maszyn wirtualnych nauki danych](dsvm-pools.md)
* [Typowe tożsamości i dostępu do obszaru roboczego z dowolnej DSVMs w puli](dsvm-common-identity.md)
* [Bezpieczny dostęp do źródła danych](dsvm-secure-access-keys.md)


Ta seria artykułów zawiera również wskazówki i wskaźniki dla każdego z powyższych elementów. Go nie obejmuje zagadnienia i wymagań w wdrażania DSVM w dużych przedsiębiorstwach konfiguracjach. Oto inne dokumentacji platformy Azure, używanego podczas implementowania DSVM wystąpień w przedsiębiorstwie: 

* [Bezpieczeństwo sieci](https://docs.microsoft.com/azure/security/azure-network-security)
* [Monitorowanie](https://docs.microsoft.com/azure/virtual-machines/windows/monitor) i [zarządzania](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [Rejestrowanie i przeprowadzanie inspekcji](https://docs.microsoft.com/azure/security/azure-log-audit)
* [Kontrola dostępu oparta na rolach](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [Ustawienie zasad i wymuszania](https://docs.microsoft.com/azure/azure-policy/azure-policy-introduction)
* [Oprogramowanie chroniące przed złośliwym kodem](https://docs.microsoft.com/azure/security/azure-security-antimalware)
* [Szyfrowanie](https://docs.microsoft.com/azure/virtual-machines/windows/encrypt-disks)
* [Dane odnajdywania i nadzór nad](https://docs.microsoft.com/azure/data-catalog/)

[Centrum architektura Azure](https://docs.microsoft.com/en-us/azure/architecture/) zapewnia szczegółowe architekturę end-to-end i wzorce tworzenia oraz zarządzania nimi infrastruktury opartej na chmurze analytics. 
