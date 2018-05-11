---
title: Wprowadzenie do maszyny wirtualnej nauki danych na podstawie środowisk zespołu - Azure | Dokumentacja firmy Microsoft
description: Wzorce wdrażania maszyny Wirtualnej nauki danych jako zespoły w firmie.
keywords: bezpośrednie uczenia AI, narzędzia do analizy danych, maszyna wirtualna nauki danych, dane geograficzne analytics, proces nauki danych zespołu
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 911d6484421cc9fddad0530bf8d9ab4f01d48bf8
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Maszyny wirtualnej nauki danych na podstawie analizy zespołu i środowiska AI 
[Maszyny wirtualnej nauki danych](overview.md) (DSVM) udostępnia bogate środowisko w chmurze Azure z oprogramowaniem wbudowanych AI i danych analiz. Tradycyjnie DSVM został użyty jako poszczególnych analytics pulpitu i analityków danych poszczególnych zwiększyć wydajność, wykorzystując to pojęcie udostępnionego środowiska wbudowanych analytics. Jak analytics duże zespoły zaplanować środowiskami analytics ich AI i analityków danych, jeden cyklicznego motywów jest udostępnionego analytics programowanie i eksperymenty infrastrukturę, która jest zarządzany zgodnie z organizacji IT zasad który ułatwia również współpracy i spójności między nauki danych / zespoły analytics. Umożliwia również współużytkowanej infrastruktury IT na lepsze wykorzystanie środowiska analytics. Nauki danych zespołów / analytics infrastruktury jest również określana przez niektóre organizacje jako "Piaskownicy Analytics", która umożliwia analityków danych szybko zrozumieć dane, uruchom eksperymenty sprawdzić hipoteza, tworzenie modeli predykcyjnych w bezpieczny sposób bez wpływu na środowisko produkcyjne podczas mieli dostęp do różnych zasobów danych. 

Ponieważ DSVM działa na poziomie infrastruktury platformy Azure, Administratorzy IT mogą łatwo skonfigurować DSVM działać zgodnie z zasadami IT organizacji i oferuje pełną elastyczność we wdrażaniu różnych architektur udostępniania dostęp do zasoby danych firmowych w kontrolowany sposób. 

W tej sekcji omówiono niektóre wzorców i wskazówki, które mogą być używane do wdrażania DSVM jako infrastruktury nauki danych zespołów.  Bloków konstrukcyjnych dla tych wzorców bezpośrednio można wykorzystać od Azure IaaS (infrastruktura jako usługa) i jako taki zastosowania do żadnej maszyny wirtualnej Azure. Określone tej serii artykułów koncentruje się na stosowanie tych możliwości standardowych infrastruktury platformy Azure na maszynie wirtualnej analizy danych. 

Niektóre z klucza bloków konstrukcyjnych w środowisku przedsiębiorstwa zespołu analytics to:

* [Skalowana automatyczne puli maszyn wirtualnych nauki danych](dsvm-pools.md)
* [Typowe tożsamości i dostępu do obszaru roboczego z dowolnej DSVMs w puli](dsvm-common-identity.md)
* [Bezpieczny dostęp do źródła danych](dsvm-secure-access-keys.md)
* Zarządzanie i odnajdywania zbiorów danych firmowych i otwarte

W tej serii artykuły wskazówki i wskaźniki są zawarte w każdej z tych aspektów. [Azure architektury centrum](https://docs.microsoft.com/en-us/azure/architecture/) zawiera bardziej szczegółowe end-to-end architektury infrastruktury analytics.  
