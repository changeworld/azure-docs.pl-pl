---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/06/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 729e757c69887bbdce324e2d8383c970995dc94a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73903669"
---
## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure 

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

> [!NOTE]
> Jeśli zarejestrowałeś się do korzystania z galerii obrazów udostępnionych `Microsoft.Compute` podczas podglądu, może być konieczne ponowne zarejestrowanie dostawcy. Otwórz [powłokę chmury](https://shell.azure.com/bash) i wpisz:`az provider register -n Microsoft.Compute`

## <a name="create-an-image-gallery"></a>Tworzenie galerii obrazów

Galeria obrazów jest podstawowym zasobem używanym do włączania udostępniania obrazów. Dozwolone znaki nazwy galerii to wielkie lub małe litery, cyfry, kropki i kropki. Nazwa galerii nie może zawierać kresek.  Nazwy galerii muszą być unikatowe w ramach subskrypcji. 

Poniższy przykład tworzy galerię o nazwie *myGallery* w grupie zasobów *myGalleryRG.*

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **Utwórz zasób**.
1. Użyj wpisu **Galeria obrazów udostępnionych** w polu wyszukiwania i wybierz pozycję **Galeria obrazów udostępnionych** w wynikach.
1. Na stronie **Galeria obrazów udostępnionych** kliknij pozycję **Utwórz**.
1. Wybierz poprawną subskrypcję.
1. W **grupie zasobów**wybierz pozycję **Utwórz nowy** i wpisz *myGalleryRG* dla nazwy.
1. W **nazwie**wpisz *myGallery* dla nazwy galerii.
1. Pozostaw wartość domyślną dla **Region**.
1. Możesz wpisać krótki opis galerii, na przykład *Moja galeria zdjęć do testowania.* a następnie kliknij pozycję **Przejrzyj + utwórz**.
1. Po przejściu weryfikacji wybierz pozycję **Utwórz**.
1. Po zakończeniu wdrażania wybierz pozycję **Przejdź do zasobu**.
   
## <a name="create-an-image-definition"></a>Tworzenie definicji obrazu 

Definicje obrazów tworzą logiczne grupowanie obrazów. Są one używane do zarządzania informacjami o wersjach obrazów, które są tworzone w nich. Nazwy definicji obrazów mogą być składane z wielkich lub małych liter, cyfr, kropek, kresek i kropek. Aby uzyskać więcej informacji na temat wartości, które można określić dla definicji obrazu, zobacz [Definicje obrazów](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions).

Utwórz definicję obrazu galerii wewnątrz galerii. W tym przykładzie obraz galerii nosi nazwę *myImageDefinition*.

1. Na stronie nowej galerii obrazów wybierz pozycję **Dodaj nową definicję obrazu** u góry strony. 
1. W *myImageDefinition* **yd.**
1. W przypadku **systemu operacyjnego**wybierz właściwą opcję na podstawie źródłowej maszyny Wirtualnej.
1. W przypadku **generowania maszyn wirtualnych**wybierz opcję na podstawie źródłowej maszyny Wirtualnej. W większości przypadków będzie to *Gen 1*. Aby uzyskać więcej informacji, zobacz [Obsługa maszyn wirtualnych generacji 2](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2).
1. W przypadku **stanu systemu operacyjnego**wybierz opcję na podstawie źródłowej maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [Uogólnione i wyspecjalizowane](../articles/virtual-machines/linux/shared-image-galleries.md#generalized-and-specialized-images).
1. W programie **Publisher**wpisz *myPublisher*. 
1. W przypadku **oferty**wpisz *myOffer*.
1. W przypadku **jednostki SKU**wpisz *mySKU*.
1. Po zakończeniu wybierz **pozycję Przejrzyj + utwórz**.
1. Po przejściu weryfikacji definicji obrazu wybierz pozycję **Utwórz**.
1. Po zakończeniu wdrażania wybierz pozycję **Przejdź do zasobu**.


## <a name="create-an-image-version"></a>Tworzenie wersji obrazu

Tworzenie wersji obrazu z obrazu zarządzanego. W tym przykładzie wersja obrazu jest *1.0.0* i jest replikowana zarówno do *centrów* danych West Central US i *Południowo-Środkowej USA.* Wybierając regiony docelowe dla replikacji, należy pamiętać, że należy również uwzględnić region *źródłowy* jako miejsce docelowe replikacji.

Dozwolone znaki dla wersji obrazu to liczby i kropki. Liczby muszą mieszczeć się w zakresie 32-bitowej liczby całkowitej. Format: *MajorVersion*. *MinorVersion*. *Poprawka*.

Kroki tworzenia wersji obrazu są nieco różne, w zależności od tego, czy źródłem jest obraz uogólniony lub migawka wyspecjalizowanej maszyny Wirtualnej. 

### <a name="option-generalized"></a>Opcja: Uogólniona

1. Na stronie definicji obrazu wybierz pozycję **Dodaj wersję** z górnej części strony.
1. W **obszarze Region**wybierz region, w którym jest przechowywany obraz zarządzany. Wersje obrazów muszą być tworzone w tym samym regionie, z którego są tworzone.
1. W przypadku **nazwy**wpisz *1.0.0*. Nazwa wersji obrazu powinna być zgodna z *głównym*. *drobne*. *formatu poprawki* przy użyciu liczb całkowitych. 
1. W **obszarze Obraz źródłowy**wybierz obraz zarządzany źródłowy z listy rozwijanej.
1. W **obszarze Wyklucz z najnowszych**pozostaw domyślną wartość *Nr*.
1. W przypadku **daty zakończenia życia**wybierz datę z kalendarza, która w przyszłości będzie wynosić kilka miesięcy.
1. W **obszarze Replikacja**pozostaw **domyślną liczbę replik** jako 1. Należy replikować do regionu źródłowego, więc pozostaw pierwszą replikę jako domyślną, a następnie wybierz drugi region repliki jako *wschodnie stany USA*.
1. Po zakończeniu wybierz **pozycję Przejrzyj + utwórz**. Platforma Azure sprawdzi konfigurację.
1. Gdy wersja obrazu przechodzi sprawdzanie poprawności, wybierz pozycję **Utwórz**.
1. Po zakończeniu wdrażania wybierz pozycję **Przejdź do zasobu**.

Może upłynąć trochę czasu, aby zreplikować obraz do wszystkich regionów docelowych.

### <a name="option-specialized"></a>Opcja: Wyspecjalizowana

1. Na stronie definicji obrazu wybierz pozycję **Dodaj wersję** z górnej części strony.
1. W **obszarze Region**wybierz region, w którym jest przechowywana migawka. Wersje obrazów muszą być tworzone w tym samym regionie, z którego są tworzone.
1. W przypadku **nazwy**wpisz *1.0.0*. Nazwa wersji obrazu powinna być zgodna z *głównym*. *drobne*. *formatu poprawki* przy użyciu liczb całkowitych. 
1. W **migawce dysku systemu operacyjnego**wybierz migawkę ze źródłowej maszyny Wirtualnej z listy rozwijanej. Jeśli źródłowa maszyna wirtualna miała dysk danych, który chcesz dołączyć, wybierz poprawny numer **jednostki LUN** z listy rozwijanej, a następnie wybierz migawkę dysku danych dla **migawki dysku danych**. 
1. W **obszarze Wyklucz z najnowszych**pozostaw domyślną wartość *Nr*.
1. W przypadku **daty zakończenia życia**wybierz datę z kalendarza, która w przyszłości będzie wynosić kilka miesięcy.
1. W **obszarze Replikacja**pozostaw **domyślną liczbę replik** jako 1. Należy replikować do regionu źródłowego, więc pozostaw pierwszą replikę jako domyślną, a następnie wybierz drugi region repliki jako *wschodnie stany USA*.
1. Po zakończeniu wybierz **pozycję Przejrzyj + utwórz**. Platforma Azure sprawdzi konfigurację.
1. Gdy wersja obrazu przechodzi sprawdzanie poprawności, wybierz pozycję **Utwórz**.
1. Po zakończeniu wdrażania wybierz pozycję **Przejdź do zasobu**.

## <a name="share-the-gallery"></a>Udostępnianie galerii

Zalecamy udostępnianie dostępu na poziomie galerii obrazów. Poniżej przedstawiono udostępnianie galerii, która została właśnie utworzona.

1. Otwórz witrynę [Azure Portal](https://portal.azure.com).
1. W menu po lewej stronie wybierz pozycję **Grupy zasobów**. 
1. Na liście grup zasobów wybierz **myGalleryRG**. Zostanie otwarty blok dla grupy zasobów.
1. W menu po lewej stronie strony **myGalleryRG** wybierz pozycję **Kontrola dostępu (IAM)**. 
1. W obszarze **Dodawanie przypisania roli**wybierz pozycję **Dodaj**. Zostanie otwarte okienko **Dodaj przypisanie roli.** 
1. W obszarze **Rola**wybierz **pozycję Czytelnik**.
1. W obszarze **Przypisywanie dostępu do**, pozostaw domyślny **użytkownik, grupę lub jednostkę usługi Azure AD**.
1. W obszarze **Wybierz**wpisz adres e-mail osoby, którą chcesz zaprosić.
1. Jeśli użytkownik znajduje się poza organizacją, zostanie wyświetlony komunikat **Ten użytkownik zostanie wysłany wiadomość e-mail, która umożliwia mu współpracę z firmą Microsoft.** Wybierz użytkownika z adresem e-mail, a następnie kliknij przycisk **Zapisz**.

Jeśli użytkownik znajduje się poza organizacją, otrzyma wiadomość e-mail z zaproszeniem do dołączenia do instytucji. Użytkownik musi zaakceptować zaproszenie, a następnie będą mogli zobaczyć galerię i wszystkie definicje obrazów i wersje na swojej liście zasobów.

