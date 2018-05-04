---
title: Połączenie analiz danych z głębokiego Learning danych nauki maszyny wirtualnej na platformie Azure | Dokumentacja firmy Microsoft
description: Jak wykonać kilka typowych zadań nauki danych z maszyną Wirtualną głębokie Learning danych nauki.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: 34ef0b10-9270-474f-8800-eecb183bbce4
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 2053ed8cc420183d493097eeb2cd2ad93c82c70c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="using-the-deep-learning-virtual-machine"></a>Za pomocą bezpośrednich uczenia maszyny wirtualnej

Po aprowizowaniu Twoje głębokie Virtual Machine Learning (DLVM), można rozpocząć tworzenie modeli sieci neuronowej głębokość do tworzenia aplikacji AI w domenach, takich jak wizja komputer i opis języka. 

Istnieje szereg narzędzi dostępnych na Maszynie wirtualnej bezpośredni uczenia dla AI. [Głębokie uczenie i AI strony struktur](dsvm-deep-learning-ai-frameworks.md) zawiera szczegóły dotyczące sposobu używania tych narzędzi. 

## <a name="deep-learning-tutorials-and-walkthroughs"></a>Głębokość uczenia samouczki i przykłady

Oprócz próbek na podstawie framework zestaw kompleksowe wskazówki dostępne są również się, że zostały zatwierdzone na DLVM. Te wskazówki umożliwia szybkie rozpoczęcie tworzenia programowania aplikacji głębokie learning w domenach, takich jak obraz i zrozumienia języka/tekstu. Nadal można dodać więcej samouczków end-to-end w różnych domenach i technologii.   


- [Uruchamianie sieci neuronowe WE różnych platform](https://github.com/ilkarman/DeepLearningFrameworks): kompleksowe wskazówki, pokazujący sposób migracji kodu w ramach jednego do drugiego. On również pokazano, jak porównać modelu i uruchamiane wydajności w czasie w ramach struktury. 

- [Przewodnik rozwiązania na trasie do wykrywania produktów w obrazach](https://github.com/Azure/cortana-intelligence-product-detection-from-images): wykrywania obrazu to technika, który można odnaleźć i sklasyfikować obiekty w obrazach. Ta technologia może potencjalnie do ogromnych korzyści w wielu domenach firm rzeczywistych. Na przykład sprzedawców można użyć tej metody do określenia, który produkt klientowi została pobrana z półki. Informacje te pomagają z kolei magazyny Zarządzanie spis produktów. 

- [O nazwie wyodrębniania jednostki z streszczenia PubMed](https://docs.microsoft.com/azure/machine-learning/preview/scenario-tdsp-biomedical-recognition) ten samouczek pokazuje, jak można wyodrębnić nazwane jednostki, takich jak nazwy narkotyków lub choroby, z tekstu bez struktury. Go przygotowuje dowolne słowo osadzanie na Boże tekstu, z 18 milionów PubMed Abstract, używa modelu do konstruowania modelu powtarzającego się sieci neuronowej długi krótkoterminowe pamięci (LSTM) do wyodrębnienia jednostki i pokazuje, że można przewyższyć word specyficznego dla domeny osadzania modelu ogólny program word osadzania wyodrębniania jednostki.

- [Bezpośrednie uczenia dla audio](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/) w tym samouczku przedstawiono sposób uczenie modelu przy użyciu bezpośrednich learning wykrywania zdarzeń audio na [zurbanizowane dźwięki zestawu danych](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html) i omówiono sposób pracy z danymi audio.

- [Klasyfikacja dokumentów tekstowych](https://github.com/anargyri/lstm_han): w tym przewodniku pokazano, jak skompilować i uczenia dwóch różnych sieci neuronowej architektur: sieci hierarchiczna sieci uwagi i długi krótki okres pamięci (LSTM). Te sieci neuronowej za pomocą Keras interfejsu API dla głębokości uczenia klasyfikować dokumenty tekstowe. Keras jest frontonu do trzech najpopularniejszych dokładnego uczenia struktury: kognitywnych zestaw narzędzi firmy Microsoft, TensorFlow i Theano.

## <a name="next-steps"></a>Kolejne kroki

[Przykłady strony](dsvm-samples-and-walkthroughs.md) zapewnia szybkie rozpoczęcie pracy wskaźniki do wstępnie załadowane na maszynie Wirtualnej dla każdej z platform, aby ułatwić przykłady kodu. 
