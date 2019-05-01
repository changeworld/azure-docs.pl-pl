---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: fb3935a58e1bc4e1ab3c582d9f3b84b87349aef7
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928035"
---
## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure 

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

> [!NOTE]
> Jeśli zostanie zarejestrowana w celu używania udostępnione, galerie obrazów w trakcie okresu zapoznawczego, może być konieczne ponowne zarejestrowanie `Microsoft.Compute` dostawcy. Otwórz [Cloud Shell](https://shell.azure.com/bash) i wpisz: `az provider register -n Microsoft.Compute`

## <a name="create-an-image-gallery"></a>Tworzenie galerii obrazów

Galeria obrazów jest podstawowy zasób umożliwiający włączenie udostępniania obrazów. Dozwolone znaki dla nazwy galerii są wielkie i małe litery, cyfry, kropki i okresów. Nazwa galerii nie może zawierać łączników.  Galeria nazwy muszą być unikatowe w ramach Twojej subskrypcji. 

Poniższy przykład tworzy Galeria o nazwie *myGallery* w *myGalleryRG* grupy zasobów.

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **Utwórz zasób**.
1. Użyj typu **galerii obrazów współdzielona** w polu wyszukiwania i wybierz pozycję **galerii obrazów usługi Shared** w wynikach.
1. W **galerii obrazów współdzielona** kliknij **Utwórz**.
1. Wybierz poprawną subskrypcję.
1. W **grupy zasobów**, wybierz opcję **Utwórz nową** i typ *myGalleryRG* dla nazwy.
1. W **nazwa**, typ *myGallery* nazwę galerii.
1. Pozostaw wartość domyślną dla **Region**.
1. Można wpisać krótki opis galerii, takie jak *Moja galeria obrazów do testowania.* a następnie kliknij przycisk **przeglądu + Utwórz**.
1. Po pomyślnej weryfikacji, wybierz **Utwórz**.
1. Po zakończeniu wdrożenia wybierz **przejdź do zasobu**.
   
## <a name="create-an-image-definition"></a>Utwórz definicję obrazu 

Definicje obraz Utwórz logiczne grupowanie obrazów. Są one używane do zarządzania informacjami o wersji obrazu, które są tworzone w ramach ich. Obraz nazwy definicji może składać się z małe i wielkie litery, cyfry, kropki, łączniki i kropki. Aby uzyskać więcej informacji na temat wartości można określić definicję obrazu, zobacz [obrazu definicje](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions).

Utwórz definicję Galeria obrazów w galerii. W tym przykładzie nosi nazwę obrazu z galerii *myImageDefinition*.

1. Na stronie galerii obrazów nowe wybierz **dodać nową definicję obrazu** w górnej części strony. 
1. Aby uzyskać **nazwę definicji obrazu**, typ *myImageDefinition*.
1. Aby uzyskać **systemu operacyjnego**, wybierz opcję odpowiednią oparta na obrazie źródło.
1. Aby uzyskać **wydawcy**, typ *myPublisher*. 
1. Aby uzyskać **oferują**, typ *myOffer*.
1. Aby uzyskać **jednostki SKU**, typ *mySKU*.
1. Upewnij się, że **tak** nam wybrać **Włącz** , a następnie wybierz **przeglądu + Utwórz**.
1. Po definicję obrazu pozytywnie przejdą weryfikację, wybierz **Utwórz**.
1. Po zakończeniu wdrożenia wybierz **przejdź do zasobu**.



## <a name="create-an-image-version"></a>Utwórz wersję obrazu

Utwórz wersję obrazu na podstawie obrazu zarządzanego. W tym przykładzie jest wersja obrazu *1.0.0* i są replikowane do obu *zachodnio-środkowe stany USA* i *południowo-środkowe stany USA* centrów danych. Wybierając regiony docelowe dla replikacji, należy pamiętać, że również należy dołączyć *źródła* regionie co miejsce docelowe dla replikacji.

Dozwolone znaki wersję obrazu są liczby i kropki. Numery muszą należeć do zakresu 32-bitową liczbę całkowitą. Format: *Brak elementu MajorVersion*. *MinorVersion*. *Poprawka*.

1. Na stronie dla swojej definicji obrazu, wybierz **wersji Dodaj** w górnej części strony.
1. W **Region**, wybierz region, w miejsce przechowywania obrazu zarządzanego. Wersje obrazów muszą być utworzone w tym samym regionie jako obrazu zarządzanego, są tworzone na podstawie.
1. Aby uzyskać **nazwa**, typ *1.0.0*. Nazwa wersji obrazów należy stosować *głównych*. *drobne*. *poprawka* formatowanie przy użyciu liczb całkowitych. 
1. W **obrazu źródłowego**, wybierz źródło obrazu zarządzanego z listy rozwijanej.
1. W **wykluczania z najnowszą**, pozostaw wartość domyślną *nie*.
1. Aby uzyskać **data**, wybierz datę z kalendarza, który jest kilka miesięcy w przyszłości.
1. W **replikacji**, pozostaw **domyślna liczba replik** jako 1. Muszą być replikowane do regionu źródłowego, więc Pozostaw pierwszą replikę jako domyślne, a następnie wybierz drugi region repliki, który będzie *wschodnie stany USA*.
1. Gdy wszystko będzie gotowe, wybierz pozycję **przeglądu + Utwórz**. Azure sprawdzi poprawność konfiguracji.
1. Gdy wersja obrazu pozytywnie przejdą weryfikację, wybierz pozycję **Utwórz**.
1. Po zakończeniu wdrożenia wybierz **przejdź do zasobu**.

Replikować obraz do wszystkich regionów docelowych może potrwać.
