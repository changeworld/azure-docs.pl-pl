---
title: Tworzenie projektu etykietowania danych
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak tworzyć i uruchamiać projekty etykietowania w celu oznaczania danych do uczenia maszynowego.  Narzędzia obejmują ml wspomagane etykietowanie, lub człowieka w pętli etykietowania, aby pomóc w zadaniu.
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.topic: tutorial
ms.date: 03/01/2020
ms.openlocfilehash: f6723992ac3335e6abdd78f2008130dfe136f7df
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80873892"
---
# <a name="create-a-data-labeling-project-and-export-labels"></a>Tworzenie projektu etykietowania danych i eksportowanie etykiet 

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Etykietowanie obszernych danych w projektach uczenia maszynowego jest często bólem głowy. Projekty, które mają składnik widzenia komputerowego, takie jak klasyfikacja obrazów lub wykrywanie obiektów, zazwyczaj wymagają etykiet dla tysięcy obrazów.
 
[Usługa Azure Machine Learning](https://ml.azure.com/) zapewnia centralne miejsce do tworzenia projektów etykietowania, zarządzania nimi i monitorowania ich (publiczna wersja zapoznawcza). Służy do koordynowania danych, etykiet i członków zespołu, aby skutecznie zarządzać zadaniami etykietowania. Uczenie maszynowe obsługuje klasyfikację obrazów, wieloznakową lub wieloklasową oraz identyfikację obiektów z ograniczonymi polami.

Uczenie maszynowe śledzi postępy i utrzymuje kolejkę niekompletnych zadań etykietowania. Labelers nie potrzebują konta platformy Azure do udziału. Po uwierzytelnieniu za pomocą konta Microsoft lub [usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)mogą wykonać tyle etykietowania, ile pozwala na to ich czas.

Rozpoczynasz i zatrzymujesz projekt, dodajesz i usuwasz labelery i zespoły oraz monitorujesz postęp etykietowania. Dane oznaczone etykietami można eksportować w formacie COCO lub jako zestaw danych usługi Azure Machine Learning.

> [!Important]
> Obecnie obsługiwane są tylko projekty klasyfikacji obrazów i oznaczania obiektów. Ponadto obrazy danych muszą być dostępne w magazynie danych obiektów blob platformy Azure. (Jeśli nie masz istniejącego magazynu danych, możesz przekazywać obrazy podczas tworzenia projektu).

W tym artykule dowiesz się, jak:

> [!div class="checklist"]
> * Tworzenie projektu
> * Określanie danych i struktury projektu
> * Zarządzanie zespołami i osobami, które pracują nad projektem
> * Uruchamianie i monitorowanie projektu
> * Eksportowanie etykiet


## <a name="prerequisites"></a>Wymagania wstępne


* Dane, które chcesz oznaczyć w plikach lokalnych lub w magazynie obiektów blob platformy Azure.
* Zestaw etykiet, które chcesz zastosować.
* Instrukcje dotyczące etykietowania.
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://aka.ms/AMLFree) przed rozpoczęciem.
* Obszar roboczy uczenia maszynowego. Zobacz [Tworzenie obszaru roboczego usługi Azure Machine Learning](how-to-manage-workspace.md).

## <a name="create-a-labeling-project"></a>Tworzenie projektu etykietowania

Projekty etykietowania są administrowane z usługi Azure Machine Learning. Strona **Projekty etykietowania** służy do zarządzania projektami i osobami. Projekt ma przypisany do niego jeden lub więcej zespołów, a zespół ma przypisaną do niego jedną lub więcej osób.

Jeśli dane są już w magazynie obiektów Blob platformy Azure, należy udostępnić je jako magazyn danych przed utworzeniem projektu etykietowania. Na przykład przy użyciu magazynu danych, zobacz [Samouczek: Tworzenie pierwszego projektu klasyfikacji obrazów .](tutorial-labeling.md)

Aby utworzyć projekt, wybierz pozycję **Dodaj projekt**. Nadaj projektowi odpowiednią nazwę i wybierz **opcję Etykietowanie typu zadania**.

![Kreator tworzenia projektu etykietowania](./media/how-to-create-labeling-projects/labeling-creation-wizard.png)


* Wybierz **klasyfikację obrazów Wieloklasowa** dla projektów, jeśli chcesz zastosować tylko *jedną klasę* z zestawu klas do obrazu.
* Wybierz **klasyfikację obrazów Multi-label** dla projektów, jeśli chcesz zastosować *jedną lub więcej* etykiet z zestawu klas do obrazu. Na przykład zdjęcie psa może być oznaczone zarówno *psem,* jak i *w ciągu dnia.*
* Wybierz **pozycję Identyfikacja obiektów (obwiednia)** dla projektów, jeśli chcesz przypisać klasę i obwiednię do każdego obiektu w obrazie.

Wybierz **pozycję Dalej,** gdy będziesz gotowy do kontynuowania.

## <a name="specify-the-data-to-label"></a>Określanie danych do etykiety

Jeśli utworzono już zestaw danych zawierający dane, wybierz go z listy **rozwijanej Wybierz istniejący zestaw danych.** Możesz też wybrać **pozycję Utwórz zestaw danych,** aby użyć istniejącego magazynu danych platformy Azure lub przekazać pliki lokalne.


### <a name="create-a-dataset-from-an-azure-datastore"></a>Tworzenie zestawu danych z magazynu danych platformy Azure

W wielu przypadkach dobrze jest po prostu przesłać pliki lokalne. Ale [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) zapewnia szybszy i bardziej niezawodny sposób przesyłania dużej ilości danych. Eksploratora magazynu zaleca się jako domyślny sposób przenoszenia plików.

Aby utworzyć zestaw danych na podstawie danych, które zostały już zapisane w magazynie obiektów blob platformy Azure:

1. Wybierz **pozycję Utwórz zestaw** > danych**z magazynu danych**.
1. Przypisz **nazwę** do zestawu danych.
1. Wybierz **pozycję Plik** jako typ zestawu **danych**.  
1. Wybierz magazyn danych.
1. Jeśli dane znajdują się w podfolderze w magazynie obiektów blob, wybierz pozycję **Przeglądaj,** aby wybrać ścieżkę.
    * Dołącz "/**" do ścieżki, aby uwzględnić wszystkie pliki w podfolderach wybranej ścieżki.
    * Dołącz "**/*.*", aby uwzględnić wszystkie dane w bieżącym kontenerze i jego podfolderach.
1. Podaj opis zestawu danych.
1. Wybierz **pozycję Dalej**.
1. Potwierdź szczegóły. Wybierz **przycisk Wstecz,** aby zmodyfikować ustawienia, lub **utwórz,** aby utworzyć zestaw danych.

> [!NOTE]
> Dane, które wybierzesz jest ładowany do projektu.  Dodawanie większej ilości danych do magazynu danych nie pojawi się w tym projekcie po utworzeniu projektu.  

### <a name="create-a-dataset-from-uploaded-data"></a>Tworzenie zestawu danych na podstawie przekazanych danych

Aby bezpośrednio przesłać swoje dane:

1. Wybierz **pozycję Utwórz zestaw** > danych**z plików lokalnych**.
1. Przypisz **nazwę** do zestawu danych.
1. Wybierz "Plik" jako **typ zestawu danych**.
1. *Opcjonalnie:* Wybierz **ustawienia zaawansowane,** aby dostosować magazyn danych, kontener i ścieżkę do danych.
1. Wybierz **pozycję Przeglądaj,** aby wybrać pliki lokalne do przekazania.
1. Podaj opis zestawu danych.
1. Wybierz **pozycję Dalej**.
1. Potwierdź szczegóły. Wybierz **przycisk Wstecz,** aby zmodyfikować ustawienia, lub **utwórz,** aby utworzyć zestaw danych.

Dane są przekazywane do domyślnego magazynu obiektów blob ("workspaceblobstore") obszaru roboczego uczenia maszynowego.

## <a name="specify-label-classes"></a>Określanie klas etykiet

Na **label klasy** strony, określ zestaw klas do kategoryzowania danych. Zrób to ostrożnie, ponieważ dokładność i szybkość twoich labelerów będzie miała wpływ na ich zdolność do wyboru spośród klas. Na przykład, zamiast wypisywania pełnego rodzaju i gatunków roślin lub zwierząt, użyj kodu pola lub w skrócie rodzaju.

Wprowadź jedną etykietę w wierszu. Użyj **+** przycisku, aby dodać nowy wiersz. Jeśli masz więcej niż 3 lub 4 etykiety, ale mniej niż 10, możesz chcieć ponowić nazwy z numerami ("1: ", "2: "), aby etykiety mogły używać klawiszy numerycznych, aby przyspieszyć ich pracę.

## <a name="describe-the-labeling-task"></a>Opis zadania etykietowania

Ważne jest, aby jasno wyjaśnić zadanie etykietowania. Na stronie **Instrukcje etykietowania** można dodać łącze do witryny zewnętrznej w celu uzyskania instrukcji etykietowania lub podać instrukcje w polu edycji na stronie. Instrukcje są zorientowane na zadania i odpowiednie dla odbiorców. Zastanów się nad tymi pytaniami:

* Jakie są etykiety, które zobaczą i jak wybiorą spośród nich? Czy istnieje tekst referencyjny, do którego należy się odwołać?
* Co powinni zrobić, jeśli żadna etykieta nie wydaje się odpowiednia?
* Co należy zrobić, jeśli wiele etykiet wydaje się odpowiednie?
* Jaki próg zaufania powinien mieć zastosowanie do etykiety? Czy chcesz ich "najlepsze przypuszczenie", jeśli nie są pewne?
* Co należy zrobić z częściowo okludowanych lub nakładających się obiektów zainteresowania?
* Co należy zrobić, jeśli obiekt zainteresowania jest przycięty przez krawędź obrazu?
* Co powinni zrobić po przesłaniu etykiety, jeśli uważają, że popełnili błąd?

W przypadku obwiedni ważne pytania obejmują:

* W jaki sposób dla tego zadania zdefiniowano obwiednie? Czy powinien być całkowicie na wewnętrznej stronie obiektu, czy powinien być na zewnątrz? Czy powinien być przycięty tak ściśle, jak to możliwe, czy też dopuszczalne jest ich prześwit?
* Jaki poziom opieki i spójności oczekujesz, że etykiety będą stosować przy definiowaniu obwiedni?
* Jak oznaczyć obiekt, który jest częściowo pokazany na obrazie? 
* Jak oznaczyć obiekt, który częściowo pokryty jest innym obiektem?

>[!NOTE]
> Pamiętaj, aby pamiętać, że labelers będą mogli wybrać pierwsze 9 etykiet za pomocą klawiszy numerycznych 1-9.

## <a name="use-ml-assisted-labeling"></a>Korzystanie z etykiet wspomaganych ml

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Strona **wspomaganego etykietowania ml** umożliwia wyzwalanie modeli automatycznego uczenia maszynowego w celu przyspieszenia zadania etykietowania. Na początku projektu etykietowania obrazy są tasowane w losowej kolejności, aby zmniejszyć potencjalne odchylenie. Jednak wszelkie odchylenia, które są obecne w zestawie danych zostaną odzwierciedlone w modelu przeszkolonych. Na przykład jeśli 80% obrazów są z jednej klasy, a następnie około 80% danych używanych do szkolenia modelu będzie tej klasy. Szkolenie to nie obejmuje aktywnego uczenia się.

Ta funkcja jest dostępna dla zadań klasyfikacji obrazów (wieloklasowych lub wieloznakowych).  

Wybierz *włącz etykietowanie wspomagane ml* i określ gpu, aby włączyć wspomagane etykietowanie, które składa się z dwóch faz:
* Klastrowanie
* Wstępne oznaczenie

Dokładna liczba obrazów oznaczonych etykietami niezbędnych do uruchomienia wspomaganego etykietowania nie jest stałą liczbą.  Może się to znacznie różnić w zależności od projektu etykietowania. W przypadku niektórych projektów czasami można zobaczyć zadania zesłania etykiety wstępnej lub klastra po ręcznym oznaczeniu 300 obrazów. Wspomagane etykietowanie ML wykorzystuje technikę o nazwie *Transfer Learning*, która wykorzystuje wstępnie przeszkolony model do szybkiego rozpoczęcia procesu szkoleniowego. Jeśli klasy zestawu danych są podobne do tych w wstępnie przeszkolonym modelu, etykiety wstępne mogą być dostępne po zaledwie kilkuset ręcznie oznaczonych obrazów. Jeśli zestaw danych znacznie różni się od danych używanych do wstępnego szkolenia modelu, może to potrwać znacznie dłużej.

Ponieważ końcowe etykiety nadal opierają się na danych wejściowych od labelera, technologia ta jest czasami nazywana *ludzką etykietą w pętli.*

### <a name="clustering"></a>Klastrowanie

Po przesłaniu określonej liczby etykiet model uczenia maszynowego zaczyna grupować podobne obrazy.  Te podobne obrazy są prezentowane etykietom na tym samym ekranie, aby przyspieszyć ręczne tagowanie. Klastrowanie jest szczególnie przydatne, gdy labeler wyświetla siatkę 4, 6 lub 9 obrazów. 

Po przeszkoliniu modelu uczenia maszynowego na danych oznaczonych ręcznie, model jest obcinany do ostatniej warstwy w pełni połączone. Obrazy bez etykiety są następnie przekazywane przez obcięty model w procesie powszechnie znanym jako "osadzanie" lub "featurization". Spowoduje to osadzenie każdego obrazu w przestrzeni o wysokiej wymiarze zdefiniowanej przez tę warstwę modelu. Obrazy, które są najbliższymi sąsiadami w przestrzeni są używane do klastrowania zadań. 

### <a name="prelabeling"></a>Wstępne oznaczenie

Po przesłaniu większej liczby etykiet obrazów model klasyfikacji jest używany do przewidywania znaczników obrazu.  Labeler widzi teraz strony, które zawierają przewidywane etykiety już obecne na każdym obrazie.  Zadanie polega na przejrzeniu tych etykiet i poprawieniu wszystkich obrazów z nieprawidłową etykietą przed przesłaniem strony.  

Po przeszkoliniu modelu uczenia maszynowego na danych oznaczonych ręcznie, model jest oceniany na zestaw testowy ręcznie oznakowanych obrazów, aby określić jego dokładność w różnych progów ufności. Ten proces oceny jest używany do określenia progu ufności, powyżej którego model jest wystarczająco dokładny, aby wyświetlić etykiety wstępne. Model jest następnie oceniany na podstawie danych bez etykiety. Obrazy z przewidywaniami bardziej pewnie niż ten próg są używane do wstępnego etykietowania.

> [!NOTE]
> Etykietowanie wspomagane ml jest dostępne **tylko** w obszarach roboczych w wersji Enterprise.

## <a name="initialize-the-labeling-project"></a>Inicjowanie projektu etykietowania

Po zainicjowaniu projektu etykietowania niektóre aspekty projektu są niezmienne. Nie można zmienić typu zadania ani zestawu danych. *Można* zmodyfikować etykiety i adres URL opisu zadania. Przed utworzeniem projektu należy dokładnie przejrzeć ustawienia. Po przesłaniu projektu zostaniesz przywrócony do strony głównej **Etykietowanie danych,** która wyświetli projekt jako **inicjujący**. Ta strona nie odświeża się automatycznie. Tak więc po wstrzymaniu ręcznie odśwież stronę, aby zobaczyć stan projektu jako **Utworzony**.

## <a name="manage-teams-and-people"></a>Zarządzanie zespołami i osobami

Domyślnie każdy projekt etykietowania, który tworzysz pobiera nowy zespół z Tobą jako członkiem. Ale zespoły mogą być również dzielone między projektami. A projekty mogą mieć więcej niż jeden zespół. Aby utworzyć zespół, wybierz **pozycję Dodaj drużynę** na stronie **Zespoły.** 

Zarządzasz osobami na stronie **Labelers.** Dodawanie i usuwanie osób według adresu e-mail. Każdy labeler musi uwierzytelnić się za pośrednictwem konta Microsoft lub usługi Azure Active Directory, jeśli go używasz.  

Po dodaniu osoby możesz przypisać tę osobę do jednego lub większej liczby zespołów: przejdź do strony **Zespoły,** wybierz zespół, a następnie wybierz pozycję **Przypisz osoby** lub **Usuń osoby**.

Aby wysłać wiadomość e-mail do zespołu, wybierz zespół, aby wyświetlić stronę **szczegółów zespołu.** Na tej stronie wybierz **pozycję Zespół poczty e-mail,** aby otworzyć wersje robocze wiadomości e-mail z adresami wszystkich osób w zespole.

## <a name="run-and-monitor-the-project"></a>Uruchamianie i monitorowanie projektu

Po zainicjowaniu projektu platforma Azure rozpocznie jego uruchamianie. Wybierz projekt na głównej stronie **Etykietowanie danych,** aby przejść do **szczegółów projektu**. Karta **Pulpit nawigacyjny** pokazuje postęp zadania etykietowania.

Na karcie **Dane** możesz wyświetlić zestaw danych i przejrzeć dane oznaczone etykietą. Jeśli widzisz nieprawidłowo oznaczone dane, zaznacz je i wybierz pozycję **Odrzuć**, co spowoduje usunięcie etykiet i umieszczenie danych z powrotem w kolejce bez etykiety.

Karta **Zespół** służy do przypisywania lub łajdamy zespołów do projektu.

Aby wstrzymać lub ponownie uruchomić projekt, wybierz przycisk **Wstrzymaj**/**start.** Dane można oznaczać tylko wtedy, gdy projekt jest uruchomiony.

Dane można oznaczyć bezpośrednio ze strony **Szczegóły projektu,** wybierając pozycję **Dane etykiety**.

## <a name="add-new-label-class-to-a-project"></a>Dodawanie nowej klasy etykiet do projektu

Podczas procesu etykietowania może się okazać, że do sklasyfikowania obrazów są potrzebne dodatkowe etykiety.  Na przykład możesz dodać etykietę "Nieznany" lub "Inne", aby wskazać mylące obrazy.

Wykonaj następujące kroki, aby dodać jedną lub więcej etykiet do projektu:

1. Wybierz projekt na głównej stronie **Etykietowanie danych.**
1. U góry strony wybierz **pozycję Wstrzymaj,** aby zatrzymać labelerów przed ich aktywnością.
1. Wybierz kartę **Szczegóły**.
1. Na liście po lewej stronie wybierz pozycję **Label classes**.
1. U góry listy wybierz pozycję **+ Dodaj etykiety** ![Dodaj etykietę](media/how-to-create-labeling-projects/add-label.png)
1. W formularzu dodaj nową etykietę i wybierz sposób postępowania.  Po zmianie dostępnych etykiet obrazu możesz wybrać sposób traktowania już oznaczonych danych:
    * Zacznij od nowa, usuwając wszystkie istniejące etykiety.  Wybierz tę opcję, jeśli chcesz rozpocząć etykietowanie od początku od nowego pełnego zestawu etykiet. 
    * Zacznij od nowa, zachowując wszystkie istniejące etykiety.  Wybierz tę opcję, aby oznaczyć wszystkie dane jako nieoznakowane, ale zachowaj istniejące etykiety jako znacznik domyślny dla obrazów, które były wcześniej oznaczone etykietami.
    * Kontynuuj, zachowując wszystkie istniejące etykiety. Wybierz tę opcję, aby wszystkie dane były już oznaczone etykietą, tak jak jest, i zacznij używać nowej etykiety dla danych, które nie zostały jeszcze oznaczone.
1. Zmodyfikuj stronę z instrukcjami, jeśli będzie to konieczne dla nowych etykiet.
1. Po dodaniu wszystkich nowych etykiet w górnej części strony wybierz przycisk **Start,** aby ponownie uruchomić projekt.  

## <a name="export-the-labels"></a>Eksportowanie etykiet

Dane etykiety można wyeksportować do eksperymentowania z uczeniem maszynowym w dowolnym momencie. Etykiety obrazów można eksportować w [formacie COCO](http://cocodataset.org/#format-data) lub jako zestaw danych usługi Azure Machine Learning. Użyj przycisku **Eksportuj** na stronie **Szczegóły projektu** projektu etykietowania.

Plik COCO jest tworzony w domyślnym magazynie obiektów blob obszaru roboczego usługi Azure Machine Learning w folderze w *ramach export/coco*. Można uzyskać dostęp do wyeksportowanego zestawu danych usługi Azure Machine Learning w sekcji **Zestawy danych** w usłudze Machine Learning. Strona szczegółów zestawu danych zawiera również przykładowy kod dostępu do etykiet z języka Python.

![Wyeksportowany zestaw danych](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="next-steps"></a>Następne kroki

* [Samouczek: Utwórz swój pierwszy projekt etykietowania klasyfikacji obrazów](tutorial-labeling.md).
* Obrazy etykiet do [klasyfikacji obrazów lub wykrywania obiektów](how-to-label-images.md)
* Dowiedz się więcej o [usłudze Azure Machine Learning i Machine Learning Studio (klasyczny)](compare-azure-ml-to-studio-classic.md)
