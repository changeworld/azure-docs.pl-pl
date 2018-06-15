---
title: Aprowizowanie maszyny wirtualnej analizy sztucznego geograficznie programu Azure - Azure | Dokumentacja firmy Microsoft
description: Jak udostępnić maszynę wirtualną z magazynu geograficznie AI na platformie Azure.
keywords: bezpośrednie uczenia AI, narzędzia do analizy danych, maszyny wirtualnej analizy danych, dane geograficzne analityka
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
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: gokuma
ms.openlocfilehash: 93dfe6594aeaf45a6905fe8cb55c98dd37cc9599
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31408612"
---
# <a name="provision-a-geo-artificial-intelligence-virtual-machine-on-azure"></a>Udostępnić maszynę wirtualną z magazynu geograficznie analizy sztucznego na platformie Azure 

Geograficznie AI danych nauki maszyny wirtualnej (geograficznie DSVM) jest rozszerzeniem popularnych [maszyny wirtualnej platformy Azure danych nauki](http://aka.ms/dsvm) specjalnie skonfigurowanym do łączenia analytics AI i dane geograficzne. Analiza dane geograficzne w maszynie Wirtualnej są obsługiwane przez [ArcGIS Pro](https://www.arcgis.com/features/index.html). Maszyna wirtualna nauki danych umożliwia szybkie szkolenia maszyny modeli uczenia, a nawet z głębokiego uczenia modeli danych, które jest wzbogaconych informacji geograficznych. Jest obsługiwana tylko na DSVM 2016 systemu Windows. 

DSVM geograficznie zawiera kilka narzędzi dla AI w tym:

- Wersje GPU popularnych głębokie uczenia struktury, takich jak kognitywnych zestaw narzędzi firmy Microsoft, TensorFlow, Keras, Caffe2, moduł łańcucha; 
- narzędzia do uzyskania i wstępnego przetwarzania obrazu, dane tekstowe 
- narzędzia do rozwoju działania, takie jak Microsoft R Server Developer Edition, Anaconda Python notesów Jupyter dla języka Python i R, IDEs Python i R, SQL bazy danych
- W ESRI ArcGIS Pro oprogramowania dla komputerów stacjonarnych oraz Python i R interfejsy, które mogą pracować z danymi aplikacji AI dane geograficzne. 


## <a name="create-your-geo-ai-data-science-vm"></a>Utwórz nauki AI danych z geograficznie maszyny Wirtualnej

Poniżej przedstawiono procedurę, aby utworzyć wystąpienie maszyny wirtualnej z magazynu geograficznie AI danych nauki: 


1. Przejdź do wyświetlania na maszynie wirtualnej [portalu Azure](https://ms.portal.azure.com/#create/microsoft-ads.geodsvmwindows).
2. Wybierz **Utwórz** znajdujący się u dołu do kreatora.
![create-geo-ai-dsvm](./media/provision-geo-ai-dsvm/Create-Geo-AI.png)
3. Kreator pozwala utworzyć DSVM geograficznie wymaga **dane wejściowe** dla każdego z **cztery kroki** wyliczyć po prawej stronie tej liczby. Poniżej przedstawiono wejść potrzebne do skonfigurowania każdego z następujących czynności:



   - **Podstawy**

      1. **Nazwa**: Nazwa serwera nauki danych tworzona.

      2. **Nazwa użytkownika**: identyfikator logowania konta administratora.

      3. **Hasło**: hasło konta administratora.

      4. **Subskrypcja**: Jeśli masz więcej niż jedną subskrypcję, wybierz jedną, na którym maszyna ma być utworzony i są rozliczane.

      5. **Grupa zasobów**: można utworzyć nową lub użyj **pusty** istniejącej grupy zasobów platformy Azure w ramach subskrypcji.

      6. **Lokalizacja**: wybierz centrum danych, która jest najbardziej odpowiednia. Zazwyczaj jest centrum danych, która zawiera większość danych lub zbliżony do fizycznej lokalizacji najszybszy dostęp do sieci. Jeśli należy wykonać głębokie uczenie na procesor GPU, trzeba wybrać jedną z lokalizacji na platformie Azure, wystąpieniami serii NC procesora GPU z maszyny Wirtualnej. Obecnie są lokalizacji, które mają GPU maszyn wirtualnych: **wschodnie stany USA, północno-środkowe stany, południowo-środkowe stany, zachodnie stany USA 2 Europa Północna, Europa Zachodnia**. Najbardziej aktualną listę Sprawdź [produkty Azure według regionu strony](https://azure.microsoft.com/regions/services/) i poszukaj **NC serii** w obszarze **obliczeniowe**. 


   - **Ustawienia**: Wybierz jedno z serii NC GPU rozmiar maszyny wirtualnej, jeśli planujesz uruchamianie głębokie uczenie na procesor GPU na DSVM Twojego magazynu geograficznie. W przeciwnym razie możesz wybrać jedną z Procesora na podstawie wystąpienia.  Utwórz konto magazynu dla maszyny Wirtualnej. 
   
   - **Podsumowanie**: Sprawdź, czy wszystkie informacje wprowadzone jest poprawna.

   - **Kup**: kliknij **kupić** uruchomić udostępnianie. Łącze znajduje się na warunki usługi. Maszyna wirtualna nie ma żadnych dodatkowych kosztów poza obliczeniowe dla rozmiaru serwera w **rozmiar** kroku. 

>[!NOTE]
> Udostępnianie powinno zająć około 20-30 minut. Stan inicjowania obsługi administracyjnej jest wyświetlana w portalu Azure.


## <a name="how-to-access-the-geo-ai-data-science-virtual-machine"></a>Jak dostęp do maszyny wirtualnej z magazynu geograficznie AI danych nauki

Po utworzeniu maszyny Wirtualnej, można przystąpić do rozpocząć korzystanie z narzędzia, które są zainstalowane i wstępnie skonfigurowane na nim. Brak Kafelki menu start i ikony pulpitu dla wielu narzędzi. Pulpit zdalny można do niego przy użyciu poświadczeń konta administratora, które skonfigurowano w poprzednim **podstawy** sekcji. 


## <a name="using-arcgis-pro-installed-in-the-vm"></a>Przy użyciu ArcGIS Pro zainstalowana na maszynie wirtualnej

DSVM geograficzna jest już desktop ArcGIS Pro preinstalowanym i wstępnie skonfigurowane do pracy przy użyciu narzędzi w DSVM środowiska. Po uruchomieniu ArcGIS monit logowania na koncie ArcGIS. Jeśli już masz konto ArcGIS i licencji na oprogramowanie, można użyć istniejących poświadczeń.  

![Łuk SIG logowania](./media/provision-geo-ai-dsvm/ArcGISLogon.png)

W przeciwnym razie Załóż nowe konto ArcGIS i licencji lub pobrać [bezpłatnej wersji próbnej](https://www.arcgis.com/features/free-trial.html). 

![ArcGIS bezpłatnych wersji próbnych](./media/provision-geo-ai-dsvm/ArcGIS-Free-Trial.png)

Po utworzeniu rejestracji dla płatna lub bezpłatne konto próbne ArcGIS, ArcGIS Pro można autoryzować dla Twojego konta zgodnie z instrukcjami w [wprowadzenie dokumentacji ArcGIS Pro](http://www.esri.com/library/brochures/getting-started-with-arcgis-pro.pdf). 

Po zalogowaniu do pulpitu ArcGIS Pro z Twoim kontem ArcGIS, można przystąpić do rozpoczęcia korzystania z narzędzi analizy danych, które są zainstalowane i skonfigurowane na Maszynie wirtualnej analytics dane geograficzne i projekty uczenia maszynowego.

## <a name="next-steps"></a>Kolejne kroki

Rozpoczynanie pracy przy użyciu maszyny Wirtualnej z magazynu geograficznie AI danych nauki ze wskazówkami w następujących tematach:

* [Użyj analizy danych AI geograficznie maszyny Wirtualnej](use-geo-ai-dsvm.md)