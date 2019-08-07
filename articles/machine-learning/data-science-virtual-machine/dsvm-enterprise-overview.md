---
title: Wprowadzenie do środowiska zespołowe na podstawie maszyny wirtualnej do nauki o danych — Azure | Dokumentacja firmy Microsoft
description: Wzorce dotyczące wdrażania maszyny Wirtualnej do nauki o danych w środowisku przedsiębiorstwa zespołu.
keywords: głębokiego uczenia i sztucznej Inteligencji, narzędzia do analizy danych, maszyny wirtualnej do nauki o danych, geoprzestrzenna analiza, zespół danych dla celów naukowych
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: vijetaj
ms.openlocfilehash: 40b8f101d968514ac599b43a79b7e62b7fd837a4
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779479"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Analiza zespołu na podstawie maszyny wirtualnej do nauki o danych i sztucznej Inteligencji środowiska 
[Maszyny wirtualnej do nauki o danych](overview.md) (DSVM) zapewnia bogate środowisko na platformie Azure przy użyciu wbudowanych oprogramowania sztucznej inteligencji (AI) i analizy danych. 

Tradycyjnie maszyny DSVM został użyty jako pulpit analityczny indywidualnych. Analitycy danych poszczególnych zwiększyć wydajność, za pomocą udostępnionego Odkryliśmy ich środowiska analizy wbudowanych. Jak analizy duże zespoły planują środowisk analitycznych dla analityków danych i deweloperom sztucznej Inteligencji, jedną z motywy cyklicznego jest infrastruktury analitycznej udostępnionego, do tworzenia i eksperymentowania. Ta infrastruktura odbywa się zgodnie z IT przedsiębiorstwa zasady, które ułatwiają również współpracy i spójności między zespołami nauki i analizowanie danych. 

Umożliwia także współużytkowanej infrastruktury IT, aby lepiej wykorzystywać środowiska analizy. W niektórych organizacjach wywołania infrastruktury do analizy i analizowanie danych opartych na zespół "Analiza piaskownicy." Umożliwia on analitykom danych uzyskiwać dostęp do różnych zasobów danych, aby szybko zrozumieć dane, uruchamiaj eksperymenty, zweryfikować hipotezę i tworzyć modele predykcyjne, bez wywierania wpływu na środowisko produkcyjne. 

Ponieważ maszyny DSVM działa na poziomie infrastruktury platformy Azure, Administratorzy IT mogą łatwo skonfigurować maszyny wirtualnej DSVM działanie zgodne z zasadami IT przedsiębiorstwa. Maszyny DSVM oferuje pełną elastyczność we wdrażaniu różnych architekturach udostępniania z dostępem do zasobów firmowych danych w sposób kontrolowany. 

W tej sekcji omówiono niektóre wzorce i wskazówki, które umożliwia wdrożenie maszyny wirtualnej DSVM jako infrastruktury do nauki o danych zespołów. Bloki konstrukcyjne dla tych wzorców pochodzą z infrastruktury platformy Azure jako usługa (IaaS), więc odnoszą się do żadnych maszyn wirtualnych platformy Azure. Ta seria artykułów koncentruje się na stosowanie tych możliwości standardowa infrastruktury platformy Azure do maszyny Wirtualnej do nauki o danych. 

Niektóre z kluczowych bloki konstrukcyjne środowiska przedsiębiorstwa zespołu analytics to:

* [Przez funkcję puli maszyn wirtualnych do nauki o danych](dsvm-pools.md)
* [Typowe tożsamość i dostęp do obszaru roboczego z dowolnej maszyny w puli](dsvm-common-identity.md)
* [Bezpieczny dostęp do źródeł danych](dsvm-secure-access-keys.md)


Ta seria artykułów zawiera wskazówki i wskaźniki dla każdej z wymienionych elementów. Nie omówiono zagadnienia i wymagania podczas wdrażania maszyny wirtualnej DSVM w konfiguracjach dużych przedsiębiorstw. Poniżej przedstawiono inne dokumentacji platformy Azure, używanego podczas wdrażania wystąpień maszyny wirtualnej DSVM w przedsiębiorstwie: 

* [Zabezpieczenia sieci] (https://docs.microsoft.com/azure/security/fundamentals/network-security
* [Monitorowanie](https://docs.microsoft.com/azure/virtual-machines/windows/monitor) i [zarządzania](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [Rejestrowanie i przeprowadzanie inspekcji](https://docs.microsoft.com/azure/security/fundamentals/log-audit)
* [Kontrola dostępu oparta na rolach](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [Ustawienia zasad i wymuszania](../../governance/policy/overview.md)
* [Oprogramowanie chroniące przed złośliwym kodem](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
* [Szyfrowanie](https://docs.microsoft.com/azure/virtual-machines/windows/encrypt-disks)
* [Odnajdywanie danych i zarządzanie](https://docs.microsoft.com/azure/data-catalog/)

[Centrum architektury platformy Azure](https://docs.microsoft.com/azure/architecture/) zapewnia szczegółowe architekturę end-to-end i wzorce do tworzenia i zarządzania infrastrukturą analizę opartą na chmurze. 
