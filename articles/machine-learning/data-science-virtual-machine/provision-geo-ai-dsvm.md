---
title: 'Szybki start: Skonfiguruj geograficzną sztuczną inteligencję'
titleSuffix: Azure Data Science Virtual Machine
description: Dowiedz się, jak utworzyć i skonfigurować maszynę wirtualną do nauki o danych sztucznej Inteligencji geograficznie. Maszyny wirtualnej do nauki o danych sztucznej Inteligencji geograficzną udostępnia narzędzia umożliwiające tworzenie rozwiązań sztucznej Inteligencji i uczenia Maszynowego za pomocą danych geograficznych.
keywords: głębokiego uczenia i sztucznej Inteligencji, narzędzia do analizy danych, maszyny wirtualnej do nauki o danych, Geoprzestrzenna analiza
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: quickstart
ms.date: 03/05/2018
ms.openlocfilehash: 7afeec3f71cd1af30093801fedabf3f0357ae3d0
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208058"
---
# <a name="quickstart-set-up-a-geo-artificial-intelligence-virtual-machine-on-azure"></a>Szybki start: Konfigurowanie geograficznie sztucznej maszyny wirtualnej na platformie Azure 

Geograficzna Data Science Virtual Machine AI (geograficznie DSVM) to rozszerzenie popularnych [Data Science Virtual Machine platformy Azure](https://aka.ms/dsvm) , które jest specjalnie skonfigurowane do łączenia analiz AI i geoprzestrzennych. Geoprzestrzenna analiza na maszynie wirtualnej są obsługiwane przez [ArcGIS Pro](https://www.arcgis.com/features/index.html). Data Science Virtual Machine (DSVM) umożliwia szybkie szkolenie uczenia maszynowego, a nawet modeli uczenia głębokiego. Aby opracować te modele, używa danych, które są wzbogacane o informacje geograficzne. DSVM geograficzna jest obsługiwana tylko w systemie Windows 2016 DSVM. 

Narzędzia AI zawarte w DSVM geograficznym obejmują następujące elementy:

- Wersje procesora GPU popularnych struktur głębokiego uczenia, takich jak Microsoft Cognitive Toolkit, TensorFlow, Keras, Caffe2 i łańcucha
- Narzędzia do uzyskiwania i wstępnego przetwarzania obrazu i danych tekstowych
- Narzędzia dla działań programistycznych, takie jak Microsoft Machine Learning Server Developer Edition, Anaconda Python, Jupyter Notess for Python i R, środowisk IDE dla języków Python i R oraz baz danych SQL
- Oprogramowanie ArcGIS Pro Desktop z ESRI, a także interfejsy Python i R, które mogą współdziałać z danymi geograficznymi z aplikacji AI
 

## <a name="create-your-geo-ai-data-science-vm"></a>Tworzenie usługi Geo sztucznej Inteligencji do analizy danych maszyny Wirtualnej

Aby utworzyć wystąpienie geograficznego Data Science VM AI, wykonaj następujące kroki:

1. Przejdź do listy maszyn wirtualnych na [Azure Portal](https://ms.portal.azure.com/#create/microsoft-ads.geodsvmwindows).
1. Wybierz pozycję **Utwórz** u dołu, aby wygenerować kreatora:

   ![Create-Geo-AI-dsvm](./media/provision-geo-ai-dsvm/Create-Geo-AI.png)

1. Kreator wymaga danych wejściowych dla każdego z czterech kroków. Aby uzyskać szczegółowe informacje na temat tych danych wejściowych, zobacz następującą sekcję.

### <a name="wizard-details"></a>Szczegóły Kreatora ###

**Podstawy**:

- **Nazwa**: Nazwa serwera do nauki o danych, który tworzysz.
    
- **Nazwa użytkownika**: Identyfikator logowania konta administratora.
    
- **Hasło**: Hasło konta administratora.
    
- **Subskrypcja**: Jeśli masz więcej niż jedną subskrypcję, wybierz ten, na którym maszyna ma zostać utworzona i rozliczane.
    
- **Grupa zasobów**: Możesz utworzyć nową lub użyć pustej istniejącej grupy zasobów platformy Azure w ramach subskrypcji.
    
- **Lokalizacja**: Wybierz centrum danych, które jest najbardziej odpowiednie. Zwykle jest to ten, który ma większość danych lub znajduje się najbliżej fizycznej lokalizacji, aby uzyskać najszybszy dostęp do sieci. Jeśli planujesz uruchomić uczenie głębokie na procesorze GPU, musisz wybrać jedną z lokalizacji na platformie Azure, która ma wystąpienia maszyn wirtualnych procesora GPU z serii NC. Obecnie są to następujące lokalizacje: **Wschodnie stany USA, Północno-środkowe stany USA, Południowo-środkowe stany USA, zachodnie stany USA 2, Europa Północna, Europa Zachodnia**. Aby uzyskać najnowszą listę, sprawdź stronę [produkty platformy Azure według regionów](https://azure.microsoft.com/regions/services/) i Wyszukaj **serie NC** w obszarze **obliczenia**. 
    
    
**Ustawienia**: Wybierz jeden z rozmiarów maszyn wirtualnych z serii NC, jeśli planujesz uruchomić uczenie głębokie na procesorze GPU w DSVM geograficznym. W przeciwnym razie można wybrać jedno z wystąpień opartych na PROCESORAch. Tworzenie konta magazynu dla maszyny Wirtualnej. 
       
**Podsumowanie**: Sprawdź, czy wszystkie wprowadzone informacje jest poprawna.
    
**Kup**: Aby rozpocząć proces aprowizacji, kliknij przycisk **Kup**. Link znajduje się na warunki użytkowania usługi. Na maszynie wirtualnej nie są naliczane żadne dodatkowe opłaty poza opłaty za serwer wybrany w kroku **rozmiar** . 
 
 >[!NOTE]
 > Inicjowanie obsługi powinno potrwać od 20 do 30 minut. Stan aprowizacji jest wyświetlany w witrynie Azure portal.

 
## <a name="how-to-access-the-geo-ai-data-science-virtual-machine"></a>Jak uzyskać dostęp do maszyny wirtualnej do nauki o danych sztucznej Inteligencji geograficznie

 Po utworzeniu maszyny wirtualnej możesz zacząć korzystać z narzędzi, które są zainstalowane i wstępnie skonfigurowane. Istnieją kafelki menu Start i ikony pulpitu dla wielu narzędzi. Możesz uzyskać dostęp do maszyny wirtualnej za pomocą pulpitu zdalnego przy użyciu poświadczeń konta administratora, które zostały skonfigurowane w sekcji **podstawy** .

 
## <a name="using-arcgis-pro-installed-in-the-vm"></a>Za pomocą ArcGIS Pro zainstalowane na maszynie wirtualnej

W DSVM geograficznym programu ArcGIS Pro jest preinstalowany i środowisko jest wstępnie skonfigurowane do pracy ze wszystkimi narzędziami w DSVM. Po rozpoczęciu ArcGIS zostanie wyświetlony monit o podanie poświadczeń do konta ArcGIS. Jeśli już masz konto ArcGIS i masz licencji na oprogramowanie, można użyć posiadanych poświadczeń.  

![Łuk GIS logowania](./media/provision-geo-ai-dsvm/ArcGISLogon.png)

W przeciwnym razie możesz zarejestrować się w celu uzyskania nowego konta i licencji usługi ArcGIS lub skorzystać z [bezpłatnej wersji próbnej](https://www.arcgis.com/features/free-trial.html). 

![Komponent ArcGIS bezpłatną wersję próbną](./media/provision-geo-ai-dsvm/ArcGIS-Free-Trial.png)

Po zarejestrowaniu się w celu korzystania ze standardowego konta usługi ArcGIS lub bezpłatnej wersji próbnej możesz autoryzować ArcGIS Pro dla konta, postępując zgodnie z instrukcjami podanymi w tematach Rozpoczynanie [pracy z usługą ArcGIS Pro](https://www.esri.com/library/brochures/getting-started-with-arcgis-pro.pdf).

Po zalogowaniu się do ArcGIS Pro Desktop za pośrednictwem konta ArcGIS możesz zacząć korzystać z narzędzi do nauki o danych, które są zainstalowane i skonfigurowane na maszynie wirtualnej na potrzeby analiz geoprzestrzennych i projektów uczenia maszynowego.

## <a name="next-steps"></a>Następne kroki

Zacznij korzystać z Data Science VM geograficznej AI ze wskazówkami z następującego zasobu:

* [Użycie maszyny Wirtualnej analizy danych sztucznej Inteligencji geograficznych](use-geo-ai-dsvm.md)
