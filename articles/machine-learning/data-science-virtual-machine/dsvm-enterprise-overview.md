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
ms.openlocfilehash: 5f34498fbdacf7fc6e62788913c795ab70ceef23
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/23/2019
ms.locfileid: "69991626"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Analiza zespołu na podstawie maszyny wirtualnej do nauki o danych i sztucznej Inteligencji środowiska 
[Data Science Virtual Machine](overview.md) (DSVM) zapewnia rozbudowane środowisko na platformie Azure z wbudowanym oprogramowaniem do sztucznej analizy (AI) i analizy danych.

Tradycyjnie maszyny DSVM został użyty jako pulpit analityczny indywidualnych. Indywidualni analitykowie danych uzyskują produktywność dzięki udostępnionemu, wstępnie skompilowanemu środowisku analitycznemu. Ponieważ duże zespoły analityczne planują środowiska dla swoich analityków danych i deweloperów systemu AI, jednym z motywów cyklicznych jest udostępniona infrastruktura analityczna do tworzenia i eksperymentowania. Ta infrastruktura jest zarządzana zgodnie z zasadami IT dla przedsiębiorstw, które ułatwiają współpracę i spójność w ramach zespołów do nauki i analizy danych.

Infrastruktura udostępniona umożliwia lepsze wykorzystanie środowiska do analizy. Niektóre organizacje wywołują chmurową infrastrukturę analizy danych na podstawie zespołu. Umożliwia ona analitykom danych dostęp do różnych zasobów danych w celu szybkiego zrozumienia danych. To środowisko piaskownicy pomaga również analitykom danych uruchamiać eksperymenty, sprawdzać poprawność i tworzyć modele predykcyjne bez wpływu na środowisko produkcyjne.

Ponieważ maszyny DSVM działa na poziomie infrastruktury platformy Azure, Administratorzy IT mogą łatwo skonfigurować maszyny wirtualnej DSVM działanie zgodne z zasadami IT przedsiębiorstwa. DSVM zapewnia pełną elastyczność wdrażania różnych architektur udostępniania, jednocześnie zapewniając dostęp do zasobów danych firmowych w kontrolowany sposób.

W tej sekcji omówiono niektóre wzorce i wskazówki, które umożliwia wdrożenie maszyny wirtualnej DSVM jako infrastruktury do nauki o danych zespołów. Ponieważ bloki konstrukcyjne dla tych wzorców pochodzą z infrastruktury platformy Azure jako usługi (IaaS), mają zastosowanie do dowolnych maszyn wirtualnych platformy Azure. Ta seria artykułów koncentruje się na zastosowaniu standardowych funkcji infrastruktury platformy Azure do DSVM.

Kluczowymi blokami konstrukcyjnymi środowiska analizy zespołu przedsiębiorstwa są:

* [Skalowanie automatyczne puli DSVMs](dsvm-pools.md)
* [Typowe tożsamość i dostęp do obszaru roboczego z dowolnej maszyny w puli](dsvm-common-identity.md)
* [Bezpieczny dostęp do źródeł danych](dsvm-secure-access-keys.md)


Ta seria zawiera wskazówki i wskaźniki dla każdego z powyższych tematów. Nie dotyczy to wszystkich zagadnień i wymagań dotyczących wdrażania DSVMs w dużych konfiguracjach przedsiębiorstwa. Oto kilka innych zasobów platformy Azure, których można użyć podczas implementowania wystąpień DSVM w przedsiębiorstwie:

* [Bezpieczeństwo sieci](https://docs.microsoft.com/azure/security/fundamentals/network-security)
* [Monitorowanie](https://docs.microsoft.com/azure/virtual-machines/windows/monitor) i [zarządzania](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [Rejestrowanie i przeprowadzanie inspekcji](https://docs.microsoft.com/azure/security/fundamentals/log-audit)
* [Kontrola dostępu oparta na rolach](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [Ustawienia zasad i wymuszania](../../governance/policy/overview.md)
* [Oprogramowanie chroniące przed złośliwym kodem](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
* [Szyfrowanie](https://docs.microsoft.com/azure/virtual-machines/windows/encrypt-disks)
* [Odnajdywanie danych i zarządzanie](https://docs.microsoft.com/azure/data-catalog/)

Na koniec [centrum architektury platformy Azure](https://docs.microsoft.com/azure/architecture/) zawiera szczegółową kompleksową architekturę i modele umożliwiające tworzenie infrastruktury analitycznej opartej na chmurze i zarządzanie nią.
