---
title: Aprowizowanie maszyny wirtualnej z geograficznie sztucznej inteligencji na platformie Azure — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć i skonfigurować maszynę wirtualną do nauki o danych sztucznej Inteligencji geograficznie. Maszyny wirtualnej do nauki o danych sztucznej Inteligencji geograficzną udostępnia narzędzia umożliwiające tworzenie rozwiązań sztucznej Inteligencji i uczenia Maszynowego za pomocą danych geograficznych.
keywords: głębokiego uczenia i sztucznej Inteligencji, narzędzia do analizy danych, maszyny wirtualnej do nauki o danych, Geoprzestrzenna analiza
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: gokuma
ms.openlocfilehash: dde9b71c3615a592f8c08e040c5e9ba7bc756bd6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60578545"
---
# <a name="provision-a-geo-artificial-intelligence-virtual-machine-on-azure"></a>Aprowizowanie maszyny wirtualnej z geograficznie sztucznej inteligencji na platformie Azure 

Geograficzna sztucznej Inteligencji maszyny wirtualnej analizy danych (geograficznie DSVM) jest rozszerzeniem popularnej [maszyny wirtualnej do nauki o danych platformy Azure](https://aka.ms/dsvm) specjalnie skonfigurowanym do łączenia sztucznej Inteligencji i geoprzestrzenna analiza. Geoprzestrzenna analiza na maszynie wirtualnej są obsługiwane przez [ArcGIS Pro](https://www.arcgis.com/features/index.html). Maszyna wirtualna do nauki o danych umożliwia szybkie szkolenia maszyny modeli uczenia, a nawet z głębokiego uczenia modeli danych, które jest wzbogacone o informacji geograficznych. Jest obsługiwana tylko na maszyny wirtualnej DSVM Windows 2016. 

DSVM geograficznego zawiera szereg narzędzi sztucznej Inteligencji, w tym:

- Procesor GPU wersje popularnych platform uczenia głębokiego, takich jak Microsoft Cognitive Toolkit, TensorFlow, Keras, Caffe2, Chainer; 
- narzędzia do uzyskania i obraz przetwarzania wstępnego, dane tekstowe 
- Narzędzia dla działań programistycznych, takich jak Microsoft R Server Developer Edition, Anaconda Python notesów programu Jupyter dla języka Python i R, środowiska IDE języka Python i R, SQL bazy danych
- Firmy ESRI ArcGIS Pro oprogramowania dla komputerów stacjonarnych wraz z języka Python i R interfejsy, które można pracować z danymi dane geograficzne z poziomu aplikacji sztucznej Inteligencji. 
 

## <a name="create-your-geo-ai-data-science-vm"></a>Tworzenie usługi Geo sztucznej Inteligencji do analizy danych maszyny Wirtualnej

Poniżej przedstawiono procedurę, aby utworzyć wystąpienie maszyna wirtualna do nauki o danych sztucznej Inteligencji geograficzna: 


1. Przejdź do maszyny wirtualnej, w witrynie [witryny Azure portal](https://ms.portal.azure.com/#create/microsoft-ads.geodsvmwindows).
2. Wybierz **Utwórz** znajdujący się u dołu, aby uwzględnić kreatora.
![create-geo-ai-dsvm](./media/provision-geo-ai-dsvm/Create-Geo-AI.png)
3. Kreator używany do tworzenia DSVM geograficznie wymaga **dane wejściowe** dla każdego z **cztery kroki** wyliczane po prawej stronie tej liczby. Poniżej przedstawiono dane wejściowe wymagane do skonfigurowania każdego z następujących czynności:



   - **Podstawy**

      1. **Nazwa**: Nazwa serwera do nauki o danych, który tworzysz.

      2. **Nazwa użytkownika**: Identyfikator logowania konta administratora.

      3. **Hasło**: Hasło do konta administratora.

      4. **Subskrypcja**: Jeśli masz więcej niż jedną subskrypcję, wybierz ten, na którym maszyna ma zostać utworzona i rozliczane.

      5. **Grupa zasobów**: Możesz utworzyć nową lub użyj **pusty** istniejącą grupę zasobów platformy Azure w ramach subskrypcji.

      6. **Lokalizacja**: Wybierz centrum danych, które jest najbardziej odpowiednie. Zazwyczaj jest centrum danych, który zawiera większość danych lub znajduje się najbliżej Twojej lokalizacji fizycznej najszybszy dostęp do sieci. Jeśli konieczne uczenia głębokiego na procesorze GPU, trzeba wybrać jedną z lokalizacji platformy Azure, która ma wystąpień maszyn wirtualnych GPU seria NC. Obecnie są lokalizacji, które mają maszynach wirtualnych procesorów GPU: **Wschodnie stany USA, północno środkowe stany USA, południowo-środkowe stany USA, zachodnie stany USA 2, Europa Północna, Europa Zachodnia**. Aby wyświetlić najnowszą listę, sprawdź [produkty Azure według regionu strony](https://azure.microsoft.com/regions/services/) i poszukaj **seria NC** w obszarze **obliczenia**. 


   - **Ustawienia**: Wybierz jeden z rozmiar maszyny wirtualnej serii NC procesora GPU, jeśli planuje się uruchamianie głębokiego uczenia GPU na maszyny wirtualnej DSVM geograficznie. W przeciwnym razie można wybrać jedną z procesora CPU na podstawie wystąpienia.  Tworzenie konta magazynu dla maszyny Wirtualnej. 
   
   - **Podsumowanie**: Sprawdź, czy wszystkie wprowadzone informacje jest poprawna.

   - **Kup**: Kliknij przycisk **Kup** do uruchomienia, jego obsługi. Link znajduje się na warunki użytkowania usługi. Maszyna wirtualna nie ma żadnych dodatkowych kosztów poza obliczenia rozmiaru serwera wybranej w ramach **rozmiar** kroku. 
 
>[!NOTE]
> Aprowizacja powinno zająć około 20 – 30 minut. Stan aprowizacji jest wyświetlany w witrynie Azure portal.

 
## <a name="how-to-access-the-geo-ai-data-science-virtual-machine"></a>Jak uzyskać dostęp do maszyny wirtualnej do nauki o danych sztucznej Inteligencji geograficznie

 Po utworzeniu maszyny Wirtualnej, jesteś gotowy rozpocząć korzystanie z narzędzia, które są zainstalowane i wstępnie skonfigurowane na nim. Brak kafelków menu start i ikony pulpitu dla wielu narzędzi. Pulpit zdalny można do niej przy użyciu poświadczeń konta administratora, które skonfigurowano w poprzednim **podstawy** sekcji. 

 
## <a name="using-arcgis-pro-installed-in-the-vm"></a>Za pomocą ArcGIS Pro zainstalowane na maszynie wirtualnej

DSVM geograficzna ma już desktop ArcGIS Pro wstępnie zainstalowane i środowiska, wstępnie skonfigurowane do pracy z wszystkich narzędzi opisanych w maszyny DSVM. Po uruchomieniu ArcGIS monituje o dane logowania do konta usługi ArcGIS. Jeśli już masz konto ArcGIS i masz licencji na oprogramowanie, można użyć posiadanych poświadczeń.  

![Łuk GIS logowania](./media/provision-geo-ai-dsvm/ArcGISLogon.png)

W przeciwnym razie Załóż nowe konto ArcGIS i licencji lub uzyskać [bezpłatna wersja próbna](https://www.arcgis.com/features/free-trial.html). 

![Komponent ArcGIS bezpłatną wersję próbną](./media/provision-geo-ai-dsvm/ArcGIS-Free-Trial.png)

Po zarejestrowaniu się na płatną lub bezpłatnej wersji próbnej ArcGIS konto ArcGIS Pro można autoryzować dla swojego konta, postępując zgodnie z instrukcjami w [wprowadzenie ArcGIS Pro](https://www.esri.com/library/brochures/getting-started-with-arcgis-pro.pdf). 

Po zalogowaniu do programu desktop ArcGIS Pro z Twoim kontem ArcGIS, jesteś gotowy rozpocząć korzystanie z narzędzi do nauki o danych, które są zainstalowane i skonfigurowane na maszynie Wirtualnej Geoprzestrzenna analiza i projektów uczenia maszynowego.

## <a name="next-steps"></a>Kolejne kroki

Rozpoczęcie korzystania z maszyny Wirtualnej do analizy danych usługi Geo sztucznej Inteligencji za pomocą wytycznych z następujących tematów:

* [Użycie maszyny Wirtualnej analizy danych sztucznej Inteligencji geograficznych](use-geo-ai-dsvm.md)
