---
title: Pobieranie etykiet dla danych
titleSuffix: Azure Machine Learning
description: W tym artykule pokazano, jak tworzyć i uruchamiać projekty etykietowania w celu tagowania danych na potrzeby uczenia maszynowego.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: e66a9f8a775a46c906601ea08be52ca9dfbe0171
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689289"
---
# <a name="get-labels-for-data"></a>Pobieranie etykiet dla danych

Voluminous danych etykietowania w projektach uczenia maszynowego jest często kłopotliwej. Projekty, które mają składnik programu obsługi komputera, takie jak Klasyfikacja obrazu lub wykrywanie obiektów, zwykle wymagają etykiet dla tysięcy obrazów.
 
[Azure Machine Learning](https://ml.azure.com/) oferuje centralne miejsce do tworzenia i monitorowania projektów etykietowania oraz zarządzania nimi. Służy do koordynowania danych, etykiet i członków zespołu w celu wydajnego zarządzania zadaniami etykietowania. Machine Learning obsługuje klasyfikację obrazów, wiele etykiet lub wiele klas oraz identyfikację obiektów razem z ograniczonymi polami.

Machine Learning śledzi postęp i utrzymuje kolejkę niekompletnych zadań etykietowania. Etykiety nie potrzebują konta platformy Azure do wzięcia udziału. Po uwierzytelnieniu za pomocą konto Microsoft lub [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis), może to zrobić tak długo, jak ich czas zezwala.

W Machine Learning zaczynasz i zatrzymywaćsz projekt, dodawaj i usuwaj osoby i zespoły oraz monitoruj postęp. Dane z etykietami można eksportować w formacie COCO lub jako zestaw danych Azure Machine Learning.

W tym artykule dowiesz się, jak:

> [!div class="checklist"]
> * Tworzenie projektu
> * Określ dane i strukturę projektu
> * Zarządzanie zespołami i osobami, które pracują nad projektem
> * Uruchamianie i monitorowanie projektu
> * Eksportowanie etykiet

## <a name="prerequisites"></a>Wymagania wstępne

* Dane, które chcesz oznaczyć, w plikach lokalnych lub w usłudze Azure Storage.
* Zestaw etykiet, które mają zostać zastosowane.
* Instrukcje dotyczące etykietowania.
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://aka.ms/AMLFree).
* Obszar roboczy Machine Learning. Zobacz [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md).

## <a name="create-a-labeling-project"></a>Tworzenie projektu etykietowania

Projekty etykiet są administrowane przy użyciu Azure Machine Learning. Strona **projekty etykiet** służy do zarządzania projektami i osobami. Do projektu jest przypisany co najmniej jeden zespół, a zespół ma przypisaną co najmniej jedną osobę.

Jeśli dane są już w usłudze Azure Blob Storage, należy udostępnić ją jako magazyn danych przed utworzeniem projektu etykietowania. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie i rejestrowanie magazynów](https://docs.microsoft.com/azure/machine-learning/service/how-to-access-data#create-and-register-datastores)danych.

Aby utworzyć projekt, wybierz pozycję **Dodaj projekt**. Nadaj projektowi odpowiednią nazwę i wybierz pozycję **etykieta typ zadania**.

![Kreator tworzenia etykiet dla projektu](media/how-to-create-labeling-projects/labeling-creation-wizard.png)

* Wybierz opcję **Klasyfikacja obrazu wieloetykietowego** dla projektów, gdy chcesz zastosować *jedną lub więcej* etykiet z zestawu klas do obrazu. Na przykład zdjęcie psa może mieć etykietę z obu *pies* i *Daytime*.
* Wybierz **wiele klas klasyfikacji obrazów** dla projektów, gdy chcesz zastosować tylko *jedną klasę* z zestawu klas do obrazu.
* Wybierz pozycję **Identyfikacja obiektu (pole ograniczenia)** dla projektów, gdy chcesz przypisać klasę i pole ograniczenia do każdego obiektu w obrazie.

Wybierz pozycję **dalej** , gdy wszystko będzie gotowe do kontynuowania.

## <a name="specify-the-data-to-label"></a>Określ dane do etykiet

Jeśli utworzono już zestaw danych, który zawiera dane, wybierz go z listy rozwijanej **Wybierz istniejący zestaw danych** . Lub wybierz pozycję **Utwórz zestaw danych** , aby użyć istniejącego magazynu usługi Azure datastore lub do przekazywania plików lokalnych.

### <a name="create-a-dataset-from-an-azure-datastore"></a>Tworzenie zestawu danych na podstawie usługi Azure datastore

W wielu przypadkach warto tylko przekazać pliki lokalne. Jednak [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) zapewnia szybszy i bardziej niezawodny sposób transferu dużej ilości danych. Zalecamy Eksplorator usługi Storage jako domyślny sposób przenoszenia plików.

Aby utworzyć zestaw danych na podstawie danych, które zostały już zapisane w usłudze Azure Blob Storage:

1. Wybierz pozycję **Utwórz zestaw danych** > **ze sklepu datastore**.
1. Przypisz **nazwę** do zestawu danych.
1. Wybierz pozycję **plik** jako **Typ zestawu danych**.  
1. Wybierz magazyn danych.
1. Jeśli dane są w podfolderze w magazynie obiektów blob, wybierz pozycję **Przeglądaj** , aby wybrać ścieżkę.
    * Dołącz "/* *" do ścieżki, aby uwzględnić wszystkie pliki w podfolderach wybranej ścieżki.
    * Dołącz "* */* . *", aby uwzględnić wszystkie dane w bieżącym kontenerze i jego podfolderach.
1. Podaj opis zestawu danych.
1. Wybierz opcję **Dalej**.
1. Potwierdź szczegóły. Wybierz pozycję **Wstecz** , aby zmodyfikować ustawienia, lub **Utwórz** , aby utworzyć zestaw danych.

### <a name="create-a-dataset-from-uploaded-data"></a>Tworzenie zestawu danych na podstawie przekazanych danych

Aby bezpośrednio przekazać dane:

1. Wybierz pozycję **Utwórz zestaw danych** > **z plików lokalnych**.
1. Przypisz **nazwę** do zestawu danych.
1. Wybierz opcję "plik" jako **Typ zestawu danych**.
1. *Opcjonalne:* Wybierz pozycję **Ustawienia zaawansowane** , aby dostosowywać magazyn danych, kontener i ścieżkę do swoich potrzeb.
1. Wybierz pozycję **Przeglądaj** , aby wybrać pliki lokalne do przekazania.
1. Podaj opis zestawu danych.
1. Wybierz opcję **Dalej**.
1. Potwierdź szczegóły. Wybierz pozycję **Wstecz** , aby zmodyfikować ustawienia, lub **Utwórz** , aby utworzyć zestaw danych.

Dane są przekazywane do domyślnego magazynu obiektów BLOB ("workspaceblobstore") obszaru roboczego Machine Learning.

## <a name="specify-label-classes"></a>Określanie klas etykiet

Na stronie **klasy etykiet** Określ zestaw klas do kategoryzacji danych. Należy to uważnie robić, ponieważ ich dokładność i szybkość są zależne od ich możliwości wyboru między klasami. Na przykład zamiast wyszukać pełną rodzaj i gatunek dla roślin lub zwierząt, użyj kodu pola lub skrócić rodzaj.

Wprowadź jedną etykietę na wiersz. Użyj przycisku **+** , aby dodać nowy wiersz. Jeśli masz więcej niż 3 lub 4 etykiety, ale mniej niż 10, możesz chcieć utworzyć prefiks nazw z cyframi ("1:", "2:"), aby etykietki mogły używać klawiszy numerycznych do przyspieszenia ich pracy.

## <a name="describe-the-labeling-task"></a>Opisywanie zadania etykietowania

Ważne jest jasne wyjaśnienie zadania etykietowania. Na stronie **instrukcje etykietowania** można dodać link do zewnętrznej witryny w celu etykietowania instrukcji. Należy zachować zorientowane na zadania instrukcje i odpowiednie dla odbiorców. Weź pod uwagę następujące pytania:

* Co to są etykiety, które zobaczysz i w jaki sposób będą wybierać między nimi? Czy istnieje tekst referencyjny, do którego się odwołuje?
* Co należy zrobić, jeśli etykieta nie wydaje się odpowiednia?
* Co należy zrobić, jeśli wiele etykiet wydaje się stosowne?
* Jaki próg ufności ma mieć zastosowanie do etykiety? Czy chcesz, aby "Najlepsza wartość", jeśli nie są one określone?
* Co należy zrobić przy użyciu częściowo zamknięte lub nakładających się obiektów?
* Co należy zrobić, jeśli obiekt zainteresowania jest przycinany przez brzeg obrazu?
* Co należy zrobić po przesłaniu etykiety, jeśli uważasz, że popełniono błąd?

W przypadku pól ograniczenia ważne pytania obejmują:

* Jak jest zdefiniowane pole ograniczenia dla tego zadania? Czy powinien znajdować się w całości na wewnętrznej stronie obiektu, czy powinien znajdować się na zewnątrz? Czy powinien być przycięty jak najlepiej jak to możliwe?
* Jakiego poziomu opieka i spójność ma oczekiwać, że etykiety mają być stosowane przy definiowaniu powiązanych pól?

>[!NOTE]
> Pamiętaj, że etykiety będą mogły wybrać pierwszych 9 etykiet przy użyciu klawiszy Number 1-9.

## <a name="initialize-the-labeling-project"></a>Inicjowanie projektu etykietowania

Po zainicjowaniu projektu etykietowania niektóre aspekty projektu są niezmienne. Nie można zmienić typu zadania lub zestawu danych. *Możesz* zmodyfikować etykiety i adres URL opisu zadania. Uważnie przejrzyj ustawienia przed utworzeniem projektu. Po przesłaniu projektu nastąpi powrót do strony głównej **etykietowania** , która będzie pokazywała projekt jako **inicjujący**. Ta strona nie jest automatycznie odświeżana. Dlatego po wstrzymaniu ręcznie Odśwież stronę, aby zobaczyć stan projektu jako **utworzony**.

## <a name="manage-teams-and-people"></a>Zarządzanie zespołami i osobami

Domyślnie każdy utworzony projekt etykietowania otrzymuje nowego zespołu, który jest członkiem. Ale zespoły mogą być współużytkowane między projektami. I projekty mogą mieć więcej niż jeden zespół. Aby utworzyć zespół, wybierz pozycję **Dodaj zespół** na stronie **zespoły** .

Osobami zarządzanymi na stronie **osoby** . Dodaj i Usuń osoby według adresu e-mail. Każdy Labeler musi uwierzytelniać się za pomocą konto Microsoft lub Azure Active Directory, jeśli jest używany.  

Po dodaniu osoby można ją przypisać do jednego lub kilku zespołów: Przejdź do strony **zespoły** , wybierz zespół, a następnie wybierz pozycję **Przypisz osoby** lub **Usuń osoby**.

Aby wysłać wiadomość e-mail do zespołu, wybierz zespół, aby wyświetlić stronę **szczegółów zespołu** . Na tej stronie wybierz pozycję **zespół poczty e-mail** , aby otworzyć roboczą wiadomość e-mail z adresami wszystkich członków zespołu.

## <a name="run-and-monitor-the-project"></a>Uruchamianie i monitorowanie projektu

Po zainicjowaniu projektu platforma Azure rozpocznie działanie. Wybierz projekt na stronie głównej **etykietki** , aby przejść do **szczegółów projektu**. Karta **pulpit nawigacyjny** pokazuje postęp zadania etykietowania.

Na karcie **dane** można zobaczyć zestaw danych i przejrzeć dane z etykietami. Jeśli zobaczysz nieprawidłowe etykiety danych, zaznacz ją i wybierz polecenie **Odrzuć**, co spowoduje usunięcie etykiet i umieszczenie danych z powrotem w kolejce bez etykiety.

Za pomocą karty **zespół** Przypisz lub Cofnij przypisanie zespołów do projektu.

Aby wstrzymać lub ponownie uruchomić projekt, wybierz przycisk **wstrzymaj**/**Rozpocznij** . Dane można etykietować tylko wtedy, gdy projekt jest uruchomiony.

Dane można oznaczyć bezpośrednio na stronie **szczegółów projektu** , wybierając pozycję **dane etykiet**.

## <a name="export-the-labels"></a>Eksportowanie etykiet

W dowolnym momencie możesz wyeksportować dane etykiet dla Machine Learning eksperymentowania. Etykiety obrazów można eksportować w [formacie Coco](http://cocodataset.org/#format-data) lub jako zestaw danych Azure Machine Learning. Użyj przycisku **Eksportuj** na stronie **szczegóły projektu** w projekcie etykietowania.

Plik COCO jest tworzony w domyślnym magazynie obiektów BLOB obszaru roboczego Azure Machine Learning w folderze w obszarze *Export/Coco*. Możesz uzyskać dostęp do wyeksportowanego zestawu danych Azure Machine Learning w sekcji **zestawy** danych w Machine Learning. Strona szczegóły zestawu danych zawiera również przykładowy kod umożliwiający uzyskanie dostępu do etykiet z poziomu języka Python.

![Wyeksportowany zestaw danych](media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="next-steps"></a>Następne kroki

* Obrazy etykiet do [wykrywania klasyfikacji obrazów lub obiektów](how-to-label-images.md)
* Dowiedz się więcej o [Azure Machine Learning i Machine Learning Studio (klasyczne)](../compare-azure-ml-to-studio-classic.md)
