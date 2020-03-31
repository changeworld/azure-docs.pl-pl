---
title: 'Szybki start: tworzenie maszyny wirtualnej do nauki o danych geoalogicznej'
titleSuffix: Azure Data Science Virtual Machine
description: Skonfiguruj i utwórz maszynę wirtualną do nauki o danych geoi na platformie Azure do analizy geoprzestrzennej i uczenia maszynowego.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 09/13/2019
ms.openlocfilehash: f3ff9bd64f54d8f83fd1889078e8a4c01827d135
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77525893"
---
# <a name="quickstart-set-up-a-geo-artificial-intelligence-virtual-machine-on-azure"></a>Szybki start: konfigurowanie maszyny wirtualnej ze sztuczną inteligencją geograficzną na platformie Azure 

Maszyna wirtualna do nauki o danych Geo AI (Geo-DSVM) jest rozszerzeniem popularnej [maszyny wirtualnej do nauki o danych platformy Azure,](https://aka.ms/dsvm) która jest specjalnie skonfigurowana do łączenia sztucznej inteligencji i analizy geoprzestrzennej. Analiza geoprzestrzenna na maszynie wirtualnej jest obsługiwana przez [program ArcGIS Pro.](https://www.arcgis.com/features/index.html) Maszyna wirtualna do nauki o danych (DSVM) umożliwia szybkie szkolenie modeli uczenia maszynowego, a nawet uczenia głębokiego. Aby opracować te modele, używa danych, które są wzbogacone o informacje geograficzne. Funkcja Geo-DSVM jest obsługiwana tylko w systemie Windows 2016 DSVM. 

Narzędzia AI, które są zawarte w geo-DSVM są następujące:

- Wersje GPU popularnych platform głębokiego uczenia, takich jak Microsoft Cognitive Toolkit, TensorFlow, Keras, Caffe2 i Chainer
- Narzędzia do pozyskiwania i wstępnego przetwarzania danych graficznych i tekstowych
- Narzędzia do tworzenia działań, takich jak Microsoft Machine Learning Server Developer Edition, Anaconda Python, Notesy Jupyter dla pythonu i języka R, środowiska IDe dla python i r oraz bazy danych SQL
- Oprogramowanie komputerowe ArcGIS Pro firmy ESRI wraz z interfejsami Python i R, które mogą współpracować z danymi geoprzestrzennymi z aplikacji AI
 

## <a name="create-your-geo-ai-data-science-vm"></a>Tworzenie maszyny wirtualnej do nauki o danych geoalogicznej

Aby utworzyć wystąpienie maszyny wirtualnej nauki o danych geoalogicznej, wykonaj następujące kroki:

1. Przejdź do listy maszyn wirtualnych w [witrynie Azure portal](https://ms.portal.azure.com/#create/microsoft-ads.geodsvmwindows).
1. Wybierz **pozycję Utwórz** u dołu, aby wygenerować kreatora:

   ![tworzenie-geo-ai-dsvm](./media/provision-geo-ai-dsvm/Create-Geo-AI.png)

1. Kreator wymaga danych wejściowych dla każdego z czterech kroków. Aby uzyskać szczegółowe informacje na temat tego wejścia, zobacz następującą sekcję.

### <a name="wizard-details"></a>Szczegóły kreatora ###

**Podstawy**:

- **Nazwa**: Nazwa serwera nauki o danych, który tworzysz.
    
- **Nazwa użytkownika**: Identyfikator logowania konta administratora.
    
- **Hasło**: Hasło konta administratora.
    
- **Subskrypcja:** Jeśli masz więcej niż jedną subskrypcję, wybierz tę, na której ma zostać utworzony i obciążony.
    
- **Grupa zasobów:** Można utworzyć nową lub użyć **pustej** istniejącej grupy zasobów platformy Azure w ramach subskrypcji.
    
- **Lokalizacja**: Wybierz najbardziej odpowiednie centrum danych. Zazwyczaj jest to ten, który ma większość danych lub jest najbliżej fizycznej lokalizacji dla najszybszego dostępu do sieci. Jeśli planujesz uruchomić uczenia głębokiego na gpu, należy wybrać jedną z lokalizacji na platformie Azure, która ma wystąpienia maszyn wirtualnych gpu serii NC. Obecnie te lokalizacje to: **Wschodnie stany USA, północno-środkowe stany USA, południowo-środkowe stany USA, zachodnie stany USA 2, Europa Północna, Europa Zachodnia.** Aby uzyskać najnowszą listę, sprawdź stronę [Produkty platformy Azure według regionów](https://azure.microsoft.com/regions/services/) i poszukaj serii **NC** w obszarze **Oblicz.** 
    
    
**Ustawienia:** Wybierz jeden z rozmiarów maszyn graficznych serii NC, jeśli planujesz uruchomić uczenie głębokie na procesorze GPU na geo dsvm. W przeciwnym razie można wybrać jedno z wystąpień opartych na procesorze CPU. Utwórz konto magazynu dla maszyny Wirtualnej. 
       
**Podsumowanie**: Sprawdź, czy wszystkie wprowadzone informacje są poprawne.
    
**Kup**: Aby rozpocząć proces inicjowania obsługi administracyjnej, kliknij przycisk **Kup**. Link do warunków korzystania z usługi. Maszyna wirtualna nie ma żadnych dodatkowych opłat poza opłatami obliczeniowymi dla rozmiaru serwera wybranego w kroku **Rozmiar.** 
 
 >[!NOTE]
 > Inicjowanie obsługi administracyjnej powinno potrwać około 20 do 30 minut. Stan inicjowania obsługi administracyjnej jest wyświetlany w witrynie Azure portal.

 
## <a name="how-to-access-the-geo-ai-data-science-virtual-machine"></a>Jak uzyskać dostęp do maszyny wirtualnej nauki o danych geoau

 Po utworzeniu maszyny Wirtualnej można przystąpić do korzystania z narzędzi, które są zainstalowane i wstępnie skonfigurowane na niej. Dla wielu narzędzi dostępne są kafelki menu Start i ikony pulpitu. Dostęp do maszyny Wirtualnej można uzyskać za pomocą pulpitu zdalnego przy użyciu poświadczeń konta administratora skonfigurowanych w sekcji **Podstawowe.**

 
## <a name="using-arcgis-pro-installed-in-the-vm"></a>Korzystanie z systemu ArcGIS Pro zainstalowanego na maszynie wirtualnej

Na komputerze stacjonarnym Geo-DSVM jest preinstalowany pulpit ArcGIS Pro, a środowisko jest wstępnie skonfigurowane do pracy ze wszystkimi narzędziami w systemie DSVM. Po uruchomieniu systemu ArcGIS zostanie wyświetlony monit o podanie poświadczeń na koncie ArcGIS. Jeśli masz już konto ArcGIS i masz licencje na oprogramowanie, możesz użyć istniejących poświadczeń.  

![Arc-GIS-Logowanie](./media/provision-geo-ai-dsvm/ArcGISLogon.png)

W przeciwnym razie możesz założyć nowe konto i licencję ArcGIS lub uzyskać [bezpłatną wersję próbną.](https://www.arcgis.com/features/free-trial.html) 

![ArcGIS-Free-Trial](./media/provision-geo-ai-dsvm/ArcGIS-Free-Trial.png)

Po zarejestrowaniu się w przypadku standardowego konta ArcGIS lub bezpłatnej wersji próbnej można autoryzować system ArcGIS Pro dla swojego konta, postępując zgodnie z instrukcjami podanymi w witrynie [Wprowadzenie do systemu ArcGIS Pro](https://www.esri.com/library/brochures/getting-started-with-arcgis-pro.pdf).

Po zalogowaniu się na pulpicie usługi ArcGIS Pro za pośrednictwem konta ArcGIS możesz rozpocząć korzystanie z narzędzi do nauki o danych zainstalowanych i skonfigurowanych na maszynie wirtualnej do analiz geoprzestrzennych i projektów uczenia maszynowego.

## <a name="next-steps"></a>Następne kroki

Zacznij używać maszyny wirtualnej do nauki o danych geoaulogicznej, korzystając ze wskazówek z następującego zasobu:

* [Korzystanie z maszyny wirtualnej do nauki o danych geoalogicznej](use-geo-ai-dsvm.md)
