---
title: Wprowadzenie do maszyny wirtualnej uczenie głębokie — Azure | Microsoft Docs
description: Najważniejsze scenariusze analizy i składniki do uczenia głębokiego Virtual Machines.
keywords: głębokie uczenie się, AI, narzędzia do nauki o danych, maszyna wirtualna do nauki o danych, narzędzia do nauki o danych, nauka danych systemu Linux
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/16/2018
ms.author: vijetaj
ms.openlocfilehash: 8541713219eb8daf880605089fe49fc585836bdc
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534959"
---
# <a name="introduction-to-the-deep-learning-virtual-machine"></a>Wprowadzenie do maszyny wirtualnej do uczenia głębokiego

> [!NOTE]
> Wszystkie narzędzia maszyny wirtualnej do uczenia głębokiego (DLVM) zostały teraz złożone w [Data Science VM (DSVM)](dsvm-tools-overview.md). DLVM i DSVM są teraz takie same. Rozważ utworzenie elementu DSVM zamiast DLVM.

## <a name="why-deep-learning-virtual-machine"></a>Dlaczego warto uczenie maszyn wirtualnych? 

Coraz bardziej zaawansowane algorytmy uczenia/głębokie sieci neuronowych stają się jedną z popularnych metod używanych w wielu problemach z uczeniem maszynowym. Są one szczególnie przydatne w przypadku zadań Cognition na komputerze, takich jak obraz, tekst, informacje audio/wideo, często zbliżają się do poziomów percepcji ludzkiej w niektórych określonych domenach z zaawansowanymi architekturami sieci głębokiej neuronowych i dostępem do dużych zestawów danych w celu uczenia modeli. Uczenie głębokie wymaga dużej ilości mocy obliczeniowej do uczenia modeli z tymi dużymi zestawami danych. Dzięki chmurze i dostępności jednostek przetwarzania graficznego (GPU) można stworzyć zaawansowane architektury głębokiego neuronowych i uczenie ich na dużym zestawie danych w ramach zaawansowanej infrastruktury obliczeniowej w chmurze.  [Data Science Virtual Machine](overview.md) udostępnia bogaty zestaw narzędzi i przykładów do przygotowywania danych, uczenia maszynowego i uczenia głębokiego. Jednak jednym z wyzwań spowodowanych przez użytkowników jest odkrywanie narzędzi i przykładów dla konkretnych scenariuszy, takich jak głębokie uczenie, a także łatwiejsze udostępnianie wystąpień maszyn wirtualnych opartych na procesorze GPU. Ta maszyna wirtualna uczenia głębokiego (DLVM) eliminuje te wyzwania. 

## <a name="what-is-deep-learning-virtual-machine"></a>Co to jest maszyna wirtualna do uczenia głębokiego? 
Maszyna wirtualna do uczenia głębokiego jest specjalnie skonfigurowanym wariantem [Data Science Virtual Machine](overview.md) (DSVM), aby łatwiej było korzystać z wystąpień maszyn wirtualnych opartych na procesorze GPU na potrzeby szkoleń modeli uczenia głębokiego. Jest on obsługiwany w systemach Windows 2016 i Ubuntu Data Science Virtual Machine.  Udostępnia ona te same podstawowe obrazy maszyn wirtualnych (i dlatego cały bogaty zestaw narzędzi) jako DSVM, ale jest skonfigurowany do ułatwienia uczenia głębokiego. Zapewniamy również kompleksowe przykłady dotyczące interpretacji obrazów i tekstu, które są szeroko stosowane do wielu rzeczywistych scenariuszy. Maszyna wirtualna głębokiego uczenia próbuje również znacznie ułatwić odnajdywanie bogatych narzędzi i przykładów na DSVM przez objęcie wykazu narzędzi i przykładów na maszynie wirtualnej. Pod względem narzędzi, maszyna wirtualna głębokiej uczenia zapewnia kilka popularnych platform uczenia głębokiego, narzędzi do uzyskiwania i wstępnego przetwarzania obrazu, danych tekstowych. Obszerną listę narzędzi można znaleźć na [stronie przegląd Data Science Virtual Machine](overview.md#whats-included-in-the-data-science-vm). 

## <a name="next-steps"></a>Następne kroki

Rozpocznij pracę z maszyną wirtualną uczenie głębokie, wykonując następujące czynności:

* [Inicjowanie obsługi administracyjnej Data Science Virtual Machine systemu Windows](provision-vm.md)
* [Inicjowanie obsługi administracyjnej Data Science Virtual Machine Ubuntu](dsvm-ubuntu-intro.md)

