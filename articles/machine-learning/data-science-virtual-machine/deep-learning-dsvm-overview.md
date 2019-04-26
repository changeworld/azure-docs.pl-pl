---
title: Wprowadzenie do maszyny wirtualnej — platformy Azure do uczenia głębokiego | Dokumentacja firmy Microsoft
description: Składniki głębokiego uczenia maszyny wirtualne z systemem i scenariusze analizy klucza.
keywords: głębokim uczeniem sztucznej Inteligencji, narzędzia do analizy danych, maszyny wirtualnej do nauki o danych, narzędzia do analizy danych, do nauki o danych w systemie linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 879f5939f110fb841ad160bf09f597edcdd86d31
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60502201"
---
# <a name="introduction-to-the-deep-learning-virtual-machine"></a>Wprowadzenie do maszyny wirtualnej do uczenia głębokiego

## <a name="why-deep-learning-virtual-machine"></a>Dlaczego głębokiego uczenia maszyny wirtualnej? 

Algorytmów uczenia głębokiego, w coraz większym stopniu / głębokich sieciach neuronowych stają się jednego z popularnych metod wykorzystywanych w wielu machine learning problemów. Są one szczególnie dobrze wykonywać zadania percepcji maszyny, takie jak obrazu, tekstu, audio i wideo, informacje o często zbliża się ludzi cognitive poziomy w niektórych określonych domen dzięki zaawansowanym architektury sieci neuronowej i dostęp do dużych zestawów danych do uczenia modeli. Uczenie głębokie wymaga dużej ilości mocy obliczeniowej do uczenia modeli za pomocą tych dużych zestawów danych. Korzystając z chmury i dostępności graficzny przetwarzania procesorów graficznych (GPU) staje się za umożliwia tworzenie zaawansowanych neuronowej architektury i ucz je na dużych zestawów danych na zaawansowanym infrastruktury obliczeniowej w chmurze.  [Maszyny wirtualnej do nauki o danych](overview.md) oferuje bogaty zestaw narzędzi i przykłady dotyczące przygotowywania danych, uczenia maszynowego i uczenia głębokiego. Ale jest jednym z wyzwań sterowaną przez użytkowników do odnajdywania narzędzia i przykłady dla konkretnych scenariuszy, takich jak uczenia głębokiego łatwe, a także łatwiej inicjować obsługę wystąpień maszyny Wirtualnej opartej na procesorze GPU. Ten głębokiego Virtual Machine Learning (DLVM) uwzględniają te problemy. 

## <a name="what-is-deep-learning-virtual-machine"></a>Co to jest maszyna wirtualna do uczenia głębokiego? 
Maszyna wirtualna uczenia głębokiego jest specjalnie skonfigurowanego wariant [maszyny wirtualnej do nauki o danych](overview.md) (DSVM), aby stał się prostsze używać opartą na procesorze GPU maszyna wirtualna wystąpień dla szkolenie modeli uczenia głębokiego. Jest on obsługiwany w Windows 2016 i maszyny wirtualnej do nauki o danych systemu Ubuntu.  Współdzieli ten sam core obrazów maszyn wirtualnych (a tym samym wszystkie rozbudowany zestaw narzędzi) jako maszyny DSVM, ale jest skonfigurowany w celu ułatwienia uczenia głębokiego. Oferujemy również przykłady end-to-end dla obrazu i zrozumienia tekstu, które są ogólnie dotyczy wielu scenariuszy sztucznej Inteligencji w realnym. Maszyna wirtualna uczenia głębokiego również stara się bogaty zestaw narzędzi i przykładów na maszyny DSVM łatwiej odnaleźć, dzięki czemu są ujawniane katalogu narzędzi i przykładów na maszynie wirtualnej. Pod względem narzędzi, maszyna wirtualna uczenia głębokiego udostępnia kilka popularnych platform uczenia głębokiego, narzędzia do uzyskania i przetwarzania wstępnego image, dane tekstowe. Aby uzyskać pełną listę narzędzi, mogą odwoływać się do [strona omówienie maszyny wirtualnej do nauki o danych](overview.md#whats-included-in-the-data-science-vm). 

## <a name="next-steps"></a>Następne kroki

Rozpocznij pracę z głębokiego uczenia maszyny wirtualnej wykonując następujące kroki:

* [Aprowizowanie maszyny wirtualnej do uczenia głębokiego](provision-deep-learning-dsvm.md)
* [Użyj maszyny wirtualnej do uczenia głębokiego](use-deep-learning-dsvm.md)
* [Odwołanie do narzędzia](dsvm-deep-learning-ai-frameworks.md)
* [Przykłady](dsvm-samples-and-walkthroughs.md)
