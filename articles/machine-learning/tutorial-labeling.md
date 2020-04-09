---
title: 'Samouczek: Tworzenie projektu etykietowania dla klasyfikacji obrazów'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak zarządzać procesem etykietowania obrazów, aby można je było używać w wieloklasowych modelach klasyfikacji obrazów.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sgilley
author: sdgilley
ms.reviewer: ranku
ms.date: 04/02/2020
ms.openlocfilehash: fa33861d86ff8bee3e2a34fb3d93032ac6180880
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879721"
---
# <a name="tutorial-create-a-labeling-project-for-multi-class-image-classification"></a>Samouczek: Tworzenie projektu etykietowania dla wieloklasowej klasyfikacji obrazów 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym samouczku pokazano, jak zarządzać procesem etykietowania (nazywane również tagowaniem) obrazów, które mają być używane jako dane do tworzenia modeli uczenia maszynowego. Etykietowanie danych w usłudze Azure Machine Learning jest w publicznej wersji zapoznawczej.

Jeśli chcesz wyszkolić model uczenia maszynowego do klasyfikowania obrazów, potrzebujesz setek lub nawet tysięcy obrazów, które są poprawnie oznaczone.  Usługa Azure Machine Learning ułatwia zarządzanie postępem prywatnego zespołu ekspertów w dziedzinie domen podczas oznaczania danych.
 
W tym samouczku użyjesz obrazów kotów i psów.  Ponieważ każdy obraz jest kotem lub psem, jest to *wieloklasowy* projekt etykietowania. Omawiane tematy:

> [!div class="checklist"]
>
> * Utwórz konto magazynu platformy Azure i przekaż obrazy do konta.
> * Tworzenie obszaru roboczego usługi Azure Machine Learning.
> * Utwórz wieloklasowy projekt etykietowania obrazów.
> * Oznacz swoje dane etykietą.  Możesz wykonać to zadanie samodzielnie lub etykiety.
> * Ukończ projekt, przeglądając i eksportując dane.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://aka.ms/AMLFree).

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

Obszar roboczy usługi Azure Machine Learning to podstawowy zasób w chmurze używany do eksperymentowania, uczenia i wdrażania modeli uczenia maszynowego. Wiąże subskrypcję platformy Azure i grupę zasobów z łatwo zużywanym obiektem w usłudze.

Tworzenie obszaru roboczego za pośrednictwem witryny Azure portal, konsoli opartej na sieci Web do zarządzania zasobami platformy Azure.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

## <a name="start-a-labeling-project"></a>Rozpoczynanie projektu etykietowania

Następnie będzie zarządzać projekt etykietowania danych w usłudze Azure Machine Learning studio, skonsolidowany interfejs, który zawiera narzędzia uczenia maszynowego do wykonywania scenariuszy nauki o danych dla praktyków nauki o danych na wszystkich poziomach umiejętności. Studio nie jest obsługiwane w przeglądarkach Internet Explorer.

1. Zaloguj się do [studia usługi Azure Machine Learning](https://ml.azure.com).

1. Wybierz subskrypcję i utworzony obszar roboczy.

### <a name="create-a-datastore"></a><a name="create-datastore"></a>Tworzenie magazynu danych

Magazyny danych usługi Azure Machine Learning są używane do przechowywania informacji o połączeniu, takich jak identyfikator subskrypcji i autoryzacja tokenu. W tym miejscu należy użyć magazynu danych, aby połączyć się z kontem magazynu, który zawiera obrazy dla tego samouczka.

1. Po lewej stronie obszaru roboczego wybierz pozycję **Magazyny danych**.

1. Wybierz **+ Nowy magazyn danych**.

1. Wypełnij formularz za pomocą następujących ustawień:

    Pole|Opis 
    ---|---
    Nazwa magazynu danych | Nadaj magazynowi danych nazwę.  Tutaj używamy **labeling_tutorial**.
    Typ magazynu danych | Wybierz typ magazynu.  W tym miejscu używamy **usługi Azure Blob Storage**, preferowanego magazynu dla obrazów.
    Metoda wyboru konta | Wybierz **pozycję Wprowadź ręcznie**.
    Adres URL | `https://azureopendatastorage.blob.core.windows.net/openimagescontainer`
    Typ uwierzytelniania | Wybierz **token sygnatury dostępu Współdzielonego**.
    Klucz konta | `?sv=2019-02-02&ss=bfqt&srt=sco&sp=rl&se=2025-03-25T04:51:17Z&st=2020-03-24T20:51:17Z&spr=https&sig=7D7SdkQidGT6pURQ9R4SUzWGxZ%2BHlNPCstoSRRVg8OY%3D`

1. Wybierz **pozycję Utwórz,** aby utworzyć magazyn danych.

### <a name="add-labelers-to-workspace"></a>Dodawanie etykiet do obszaru roboczego

Skonfiguruj obszar roboczy tak, aby uwzględniał wszystkie osoby, które będą oznaczać dane dla każdego z Twoich projektów.  Później dodasz te labelery do konkretnego projektu etykietowania.

1. Po lewej stronie wybierz **pozycję Etykietowanie danych**.

1. U góry strony wybierz pozycję **Labelers**.

1. Wybierz **pozycję Dodaj labeler,** aby dodać adres e-mail labelera.

1. Kontynuuj dodawanie kolejnych labelerów, dopóki nie skończysz.

### <a name="create-a-labeling-project"></a>Tworzenie projektu etykietowania

Teraz, gdy masz listę labelers i dostęp do danych, które mają być oznaczone, utwórz projekt etykietowania.

1. U góry strony wybierz pozycję **Projekty**.

1. Wybierz **+ Dodaj projekt**.

    ![Tworzenie projektu](media/tutorial-labeling/create-project.png)

### <a name="project-details"></a>Szczegóły projektu

1. Użyj następującego danych wejściowych dla formularza **Szczegóły projektu:**

    Pole|Opis 
    ---|---
    Project name (Nazwa projektu) | Nadaj projektowi nazwę.  Tutaj użyjemy **tutorial-cats-n-psy**.
    Typ zadania etykietowania | Wybierz **klasyfikację obrazów Wieloklasowa**.  
    
    Wybierz **przycisk Dalej,** aby kontynuować tworzenie projektu.

### <a name="select-or-create-a-dataset"></a>Zaznaczanie lub tworzenie zestawu danych

1.   W formularzu **Wybierz lub utwórz zestaw danych** wybierz drugi wybór, **Utwórz zestaw danych**, a następnie wybierz łącze Z magazynu **danych**.

1. Użyj następującego danych wejściowych dla **formularza Utwórz zestaw danych z magazynu danych:**

    1. W formularzu **Podstawowe informacje** dodaj nazwę, tutaj użyjemy obrazów **do samouczka**.  Dodaj opis, jeśli chcesz.  Następnie wybierz **przycisk Dalej**.
    1. W formularzu **wyboru magazynu danych** użyj listy rozwijanej, aby wybrać **poprzednio utworzony magazyn danych**, na przykład tutorial_images **(Usługa Azure Blob Storage)**
    1. Następnie nadal w formularzu **wyboru magazynu danych** wybierz pozycję **Przeglądaj,** a następnie wybierz pozycję **MultiClass - DogsCats**.  Wybierz **zapisz,** aby użyć **/MultiClass - DogsCats** jako ścieżki.
    1. Wybierz **przycisk Dalej,** aby potwierdzić szczegóły, a następnie **utwórz,** aby utworzyć zestaw danych.
    1. Wybierz okrąg obok nazwy zestawu danych na liście, na przykład **images-for-tutorial**.

1. Wybierz **przycisk Dalej,** aby kontynuować tworzenie projektu.

### <a name="label-classes"></a>Klasy etykiet

1. W formularzu **Klasy etykiet** wpisz nazwę etykiety, a następnie wybierz pozycję **+Dodaj etykietę,** aby wpisać następną etykietę.  W przypadku tego projektu etykiety to **Cat,** **Dog**i **Uncertain.**

1. Wybierz **przycisk Dalej** po dodaniu wszystkich etykiet.

### <a name="labeling-instructions"></a>Instrukcje etykietowania

1. W formularzu **Instrukcje etykietowania** można podać łącze do witryny sieci Web zawierającej szczegółowe instrukcje dla etykiet.  Pozostawimy to puste dla tego samouczka.

1. Możesz również dodać krótki opis zadania bezpośrednio w formularzu.  Typ **Etykietowanie samouczek - Koty & Psy.**

1. Wybierz **pozycję Dalej**.

1. W formularzu **wspomaganego etykietowania ml** pozostaw pole wyboru niezaznaczone. Wspomagane etykietowanie ml wymaga więcej danych niż będzie używany w tym samouczku.

1. Wybierz pozycję **Create project** (Utwórz projekt).

Ta strona nie odświeża się automatycznie. Po wstrzymaniu należy ręcznie odświeżyć stronę, aż stan projektu zmieni się na **Utworzony**.

### <a name="add-labelers-to-your-project"></a>Dodawanie etykiet do projektu

Dodaj niektóre lub wszystkie etykiety do tego projektu.

1. Wybierz nazwę projektu, aby otworzyć projekt.  

1. U góry strony wybierz pozycję **Zespoły**.

1. Wybierz **labeling_tutorial łącze Domyślny zespół.**

1. Teraz użyj **Przypisz labelers,** aby dodać labelers chcesz uczestniczyć w tym projekcie. 

1. Wybierz z listy etykiet utworzonych wcześniej.  Po wybraniu wszystkich etykiet, których chcesz użyć, wybierz pozycję **Przypisz labelery,** aby dodać je do domyślnego zespołu projektu.

## <a name="start-labeling"></a>Rozpocznij etykietowanie

Teraz skonfigurowano zasoby platformy Azure i skonfigurowano projekt etykietowania danych. Nadszedł czas, aby dodać etykiety do danych.

### <a name="notify-labelers"></a>Powiadamianie osób etykietatorów

Jeśli masz wiele obrazów do etykiety, miejmy nadzieję, że masz również wiele labelers do wykonania zadania.  Teraz będziesz chciał wysłać im instrukcje, aby mogli uzyskać dostęp do danych i rozpocząć etykietowanie.

1. W [studiu machine learning](https://ml.azure.com)wybierz opcję **Etykietowanie danych** po lewej stronie, aby znaleźć swój projekt.  

1. Wybierz łącze nazwa projektu.

1. U góry strony wybierz pozycję **Szczegóły**.  Zobaczysz podsumowanie projektu.

    ![Szczegóły projektu](media/tutorial-labeling/project-details.png)

1. Skopiuj link **url portalu etykietowania,** aby wysłać je do etykiet.

1. Teraz wybierz **zespół** u góry, aby znaleźć swój zespół etykietowania.  

1. Wybierz łącze do nazwy zespołu.

1. U góry strony wybierz **pozycję Zespół poczty e-mail,** aby rozpocząć pocztę e-mail.  Wklej adres URL portalu etykietowania, który właśnie skopiowałeś.  

Za każdym razem, gdy labeler przejdzie do adresu URL portalu, zostanie wyświetlonych więcej obrazów do oznaczenia, dopóki kolejka nie będzie pusta.  

### <a name="tag-the-images"></a>Oznaczanie obrazów

W tej części samouczka zmienisz role z *administratora projektu* na role *labelera*.  Użyj adresu URL wysłanego do zespołu.  Ten adres URL prowadzi do portalu etykietowania dla projektu.  Jeśli dodałeś instrukcje, zobaczysz je tutaj po przybyciu na stronę.

1. U góry strony wybierz pozycję **Zadania,** aby rozpocząć etykietowanie.

1. Wybierz obraz miniatury po prawej stronie, aby wyświetlić liczbę obrazów, które chcesz oznaczyć za jednym zamachem. Aby można było przejść dalej, należy oznaczyć wszystkie te obrazy etykietami. Układy przełączają się tylko wtedy, gdy masz nową stronę danych bez etykiety. Przełączanie układów czyści prace tagowania strony w toku.

1. Zaznacz jeden lub więcej obrazów, a następnie wybierz znacznik, który ma być stosowany do zaznaczenia. Tag pojawi się pod obrazem.  Kontynuuj zaznaczanie i oznaczanie wszystkich obrazów na stronie.  Aby jednocześnie zaznaczyć wszystkie wyświetlane obrazy, wybierz pozycję **Zaznacz wszystkie**. Wybierz co najmniej jeden obraz, aby zastosować znacznik.


    > [!TIP]
    > Pierwsze dziewięć tagów można wybrać za pomocą klawiszy numerycznych na klawiaturze.

1. Po oznaczeniu wszystkich obrazów na stronie wybierz pozycję **Prześlij,** aby przesłać te etykiety.

    ![Tagowanie obrazów](media/tutorial-labeling/catsndogs.gif)

1. Po przesłaniu tagów dla danych w zasięgu ręki, Platforma Azure odświeża stronę z nowym zestawem obrazów z kolejki pracy.

## <a name="complete-the-project"></a>Ukończ projekt

Teraz przełączysz role z powrotem do *administratora projektu* dla projektu etykietowania.

Jako menedżer możesz przejrzeć pracę swojego labelera.  

### <a name="review-labeled-data"></a>Przeglądanie danych oznaczonych etykietą

1. W [studiu machine learning](https://ml.azure.com)wybierz opcję **Etykietowanie danych** po lewej stronie, aby znaleźć swój projekt.  

1. Wybierz łącze nazwa projektu.

1. Pulpit nawigacyjny pokazuje postęp projektu.

1. U góry strony wybierz pozycję **Dane**.

1. Po lewej stronie wybierz **pozycję Dane oznaczone etykietami,** aby wyświetlić oznaczone obrazy.  

1. Jeśli nie zgadzasz się z etykietą, zaznacz obraz, a następnie wybierz **pozycję Odrzuć** u dołu strony.  Znaczniki zostaną usunięte, a obraz zostanie ponownie umieszczony w kolejce obrazów bez etykiety.

### <a name="export-labeled-data"></a>Eksportowanie danych oznaczonych etykietą

Dane etykiety można wyeksportować do eksperymentowania z uczeniem maszynowym w dowolnym momencie. Użytkownicy często eksportują wiele razy i trenują różne modele, zamiast czekać na oznaczenie wszystkich obrazów.

Etykiety obrazów można eksportować w [formacie COCO](http://cocodataset.org/#format-data) lub jako zestaw danych usługi Azure Machine Learning. Format zestawu danych ułatwia korzystanie z szkoleń w usłudze Azure Machine Learning.  

1. W [studiu machine learning](https://ml.azure.com)wybierz opcję **Etykietowanie danych** po lewej stronie, aby znaleźć swój projekt.  

1. Wybierz łącze nazwa projektu.

1. Wybierz **pozycję Eksportuj** i wybierz pozycję **Eksportuj jako zestaw danych usługi Azure ML**. 

    Stan eksportu pojawi się tuż pod przyciskiem **Eksportuj.** 

1. Po pomyślnym wyeksportowanie etykiet wybierz **pozycję Zestawy danych** po lewej stronie, aby wyświetlić wyniki.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów


[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku, oznaczone obrazy.  Teraz użyj danych oznaczonych etykietą:

> [!div class="nextstepaction"]
> [Trenuj model rozpoznawania obrazów uczenia maszynowego](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb).
