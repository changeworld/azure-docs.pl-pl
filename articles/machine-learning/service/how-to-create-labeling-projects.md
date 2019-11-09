---
title: Pobieranie etykiet dla danych
titleSuffix: Azure Machine Learning
description: W tym artykule pokazano, jak tworzyć i uruchamiać projekty etykietowania w celu tagowania danych na potrzeby uczenia maszynowego.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: ca3486610d6cf71ba315e407b58a2a2551ad6ee1
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837486"
---
# <a name="get-labels-for-data"></a>Pobieranie etykiet dla danych

Etykietowanie dużych ilości danych często było kłopotliwej w projektach uczenia maszynowego. Projekty ML ze składnikiem przetwarzania obrazów, takie jak Klasyfikacja obrazu lub wykrywanie obiektów, zwykle wymagają tysięcy obrazów i odpowiednich etykiet. 
 
Azure Machine Learning stanowi centralną lokalizację do tworzenia i monitorowania projektów etykietowania oraz zarządzania nimi. Etykietowanie projektów ułatwia koordynowanie danych, etykiet i członków zespołu, co pozwala efektywniej zarządzać zadaniami etykietowania. Obecnie obsługiwane zadania to klasyfikacja obrazów, wiele etykiet lub wiele klas oraz identyfikacja obiektów przy użyciu powiązanych pól.

Platforma Azure śledzi postęp i utrzymuje kolejkę niekompletnych zadań etykietowania. Etykiety nie wymagają konta platformy Azure do wzięcia udziału. Po uwierzytelnieniu za pomocą konta Microsoft (MSA) lub [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)mogą one mieć dowolną ilość lub małą etykietę, gdy zezwoli na ich czas. Mogą przypisywać i zmieniać etykiety za pomocą skrótów klawiaturowych. 

Można uruchamiać i zatrzymywać projekt, dodawać i usuwać osoby i zespoły oraz monitorować postępy. Dane z etykietami można eksportować w formacie COCO lub jako zestaw danych usługi Azure ML. 

W tym artykule dowiesz się, jak:

> [!div class="checklist"]
> * Tworzenie projektu
> * Określ dane i strukturę projektu
> * Zarządzanie zespołami i osobami pracującymi nad projektem
> * Uruchamianie i monitorowanie projektu
> * Eksportowanie etykiet 

## <a name="prerequisites"></a>Wymagania wstępne

* Dane, które chcesz oznaczyć, w plikach lokalnych lub już w usłudze Azure Storage
* Zestaw etykiet, które chcesz zastosować.
* Instrukcje dotyczące etykietowania
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj
* Obszar roboczy usługi Azure Machine Learning. Zobacz [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md).

## <a name="create-a-labeling-project"></a>Tworzenie projektu etykietowania

Projekty etykiet są administrowane przy użyciu [Azure Machine Learning](https://ml.azure.com/). Strona **etykietowanie projektów** umożliwia zarządzanie projektami, zespołami i osobami. Do projektu jest przypisany co najmniej jeden zespół, a zespół ma przypisaną co najmniej jedną osobę. 

Jeśli dane są już przechowywane w usłudze Azure Blob Storage, należy je udostępnić jako magazyn danych przed utworzeniem projektu etykietowania. Aby uzyskać więcej informacji, zobacz [Tworzenie i rejestrowanie magazynów](https://docs.microsoft.com/azure/machine-learning/service/how-to-access-data#create-and-register-datastores)danych. 

Aby utworzyć projekt, wybierz pozycję **Dodaj projekt**. Nadaj mu odpowiednią nazwę i wybierz **Typ zadania etykietowania**. 

![Kreator tworzenia etykiet dla projektu](media/how-to-create-labeling-projects/labeling-creation-wizard.png)

* Wybierz pozycję **Klasyfikacja obrazu wielopoziomowe** dla projektów, w których **co najmniej jedna** etykieta z zestawu klas może być zastosowana do obrazu. Na przykład zdjęcie z Dog może być oznaczone jako *pies* i *Daytime*
* Wybierz **wiele klas klasyfikacji obrazów** dla projektów, w których do obrazu można zastosować tylko **jedną klasę** z zestawu klas
* Wybierz pozycję **Identyfikacja obiektu (pole ograniczenia)** dla projektów, w których zadanie ma być przypisane do obiektu w ramach obrazu, i Określ pole ograniczenia otaczające obiekt

Wybierz pozycję **dalej** , gdy wszystko będzie gotowe do przejścia.

## <a name="specify-data-to-be-labeled"></a>Określ dane, które mają być oznaczone etykietami

Jeśli utworzono już zestaw danych zawierający dane, możesz wybrać go z listy rozwijanej **Wybierz istniejący zestaw danych** . Lub wybierz pozycję **Utwórz zestaw danych** , aby wybrać istniejący magazyn usługi Azure datastore lub przekazać pliki lokalne. 

### <a name="create-a-dataset-from-an-azure-datastore"></a>Tworzenie zestawu danych na podstawie usługi Azure datastore

Mimo że wybór bezpośredniego przekazywania plików lokalnych jest prawidłowy dla wielu przypadków użycia, [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) jest to bardziej niezawodne i szybsze do przesyłania znaczących ilości danych. Zalecamy Eksplorator usługi Azure Storage jako domyślny sposób przenoszenia plików.

Aby utworzyć zestaw danych na podstawie danych, które zostały już zapisane w usłudze Azure Blob Storage:

1. Wybierz pozycję **Utwórz zestaw danych i element** **z** niego
1. Przypisywanie **nazwy** dla zestawu danych
1. Musisz wybrać "plik" jako **Typ zestawu danych**  
1. Wybierz magazyn danych 
1. Jeśli dane znajdują się w podfolderze w magazynie obiektów blob, wybierz pozycję **Przeglądaj** , aby wybrać ścieżkę. 
    * Dodatkowo możesz dołączyć `/**` po ścieżce, aby uwzględnić wszystkie pliki w podfolderach wybranej ścieżki
    * Użyj `**/*.*`, aby uwzględnić wszystkie dane w bieżącym kontenerze i podfolderach
1. Podaj opis zestawu danych
1. Wybierz **Następny** 
1. Potwierdź szczegóły. Możesz zmienić ustawienia lub wybrać pozycję **Utwórz** **, aby utworzyć** zestaw danych

### <a name="create-a-dataset-by-uploading-data"></a>Tworzenie zestawu danych przez przekazywanie danych

Jeśli chcesz przekazać bezpośrednio dane:

1. Wybierz pozycję **Utwórz zestaw danych** i **z plików lokalnych**
1. Przypisywanie **nazwy** dla zestawu danych
1. Musisz wybrać "plik" jako **Typ zestawu danych**
1. W przypadku wybrania opcji **Ustawienia zaawansowane**możesz dostosować magazyn danych, kontener i ścieżkę do swoich potrzeb.
1. Wybierz pozycję **Przeglądaj** , aby wybrać pliki lokalne do przekazania
1. Podaj opis zestawu danych
1. Wybierz **Następny** 
1. Potwierdź szczegóły. Możesz zmienić ustawienia lub wybrać pozycję **Utwórz** **, aby utworzyć** zestaw danych

Dane zostaną przekazane do domyślnego magazynu obiektów BLOB (`workspaceblobstore`) obszaru roboczego usługi Azure ML.

## <a name="specify-label-classes"></a>Określanie klas etykiet

Na stronie **klasy etykiet** należy określić zestaw klas używanych do klasyfikowania danych. Należy traktować te klasy jako stopień dokładności i szybkość etykiet, dzięki czemu mogą one być odpowiednie do wyboru. Na przykład, zamiast wyrazić pełną rodzaj i gatunek dla roślin lub zwierząt, warto lepiej wykorzystać kody pól lub skrócić rodzaj. 

Wprowadź jedną etykietę na wiersz, używając przycisku **+** , aby dodać nowy wiersz. Jeśli masz więcej niż 3 lub 4 etykiety, ale mniej niż 10, rozważ ich podawanie przy użyciu wartości "1:", "2:" itd., aby udostępnić wskazówkom etykietki z użyciem klawiszy liczbowych do przyspieszenia pracy. 

## <a name="describe-the-labeling-task"></a>Opisywanie zadania etykietowania

Jasne wyjaśnienie zadania etykietowania jest ważne. Na stronie **instrukcje etykietowania** można połączyć się z witryną zewnętrzną w celu uzyskania instrukcji dotyczących etykiet. Należy zachować zorientowane na zadania instrukcje i odpowiednie dla odbiorców. 

* Jakie będą widoczne etykiety i jak wybierają między nimi? Czy istnieje tekst referencyjny, do którego powinny się odwoływać?
* Co należy zrobić, jeśli etykieta nie wydaje się odpowiednia? 
* Co należy zrobić, jeśli wiele etykiet wydaje się stosowne?
* Jaki próg ufności ma mieć zastosowanie do etykiety? Czy chcesz, aby "Najlepsza wartość", jeśli nie są one określone?
* Co należy zrobić przy użyciu częściowo zamknięte lub nakładających się obiektów?
* Co należy zrobić, jeśli obiekt zainteresowania jest przycinany przez brzeg obrazu?
* Co należy zrobić, jeśli wystąpił błąd z obrazem po jego przesłaniu? 

W przypadku pól związanych z innymi ważnymi pytaniami należą:

* Jak jest zdefiniowane pole ograniczenia dla tego zadania? Czy powinna być całkowicie wewnętrzna dla obiektu, przycięta jak najdokładniej do tego zakresu lub czy jest to pewna ilość akceptowalnych dopuszczalnych możliwości? 
* Jakiego poziomu opieka i spójność ma oczekiwać, że Labeler ma zastosowanie do definiowania pól ograniczenia?

>[!Note]
> Upewnij się, że Labeler będzie w stanie wybrać spośród pierwszych 9 etykiet z kluczami liczb 1-9. 

## <a name="initialize-the-labeling-project"></a>Inicjowanie projektu etykietowania

Po zainicjowaniu niektóre aspekty projektu etykietowania są niezmienne: nie można zmienić typu zadania lub zestawu danych. Możesz zmodyfikować etykiety i zmienić adres URL opisu zadania. Uważnie przejrzyj ustawienia przed utworzeniem projektu. Po przesłaniu projektu nastąpi powrót do strony głównej **etykietowania** , co spowoduje wyświetlenie projektu jako **inicjującego**. Ta strona nie jest odświeżana ręcznie, więc po pewnym czasie ręczne odświeżanie spowoduje wyświetlenie projektu jako **utworzonego**. 

## <a name="manage-teams-and-people"></a>Zarządzanie zespołami i osobami

Projekt etykietowania otrzymuje zespół domyślny i dodaje Cię jako domyślnego członka. Każdy projekt etykietowania otrzymuje nowy zespół domyślny, ale zespoły mogą być współużytkowane przez projekty. Projekty mogą mieć więcej niż jeden zespół. Tworzenie zespołu odbywa się, wybierając pozycję **Dodaj zespół** na stronie **zespoły** . 

Osoby są zarządzane na stronie **osoby** . Możesz dodawać i usuwać osoby z własnymi adresami e-mail. Każdy Labeler będzie musiał uwierzytelniać się przy użyciu konta Microsoft lub Azure Active Directory, jeśli jest używany.  

Po dodaniu osoby możesz przypisać je do jednego lub kilku zespołów. Przejdź do strony **zespoły** , wybierz konkretny zespół, którego jesteś zainteresowanym, a następnie użyj opcji **Przypisz osoby** lub **Usuń osoby** zgodnie z potrzebami.

Jeśli kiedykolwiek chcesz wysłać wiadomość e-mail do wszystkich członków zespołu, możesz to zrobić, wybierając zespół, aby wyświetlić stronę **szczegółów zespołu** . Na tej stronie przycisk **zespół poczty e-mail** otworzy Edytor poczty e-mail z adresami wszystkich członków zespołu.

## <a name="run-and-monitor-the-project"></a>Uruchamianie i monitorowanie projektu

Po zainicjowaniu projektu platforma Azure rozpocznie działanie. Jeśli na głównej stronie **etykietowania** klikniesz projekt, nastąpi przekierowanie do **szczegółów projektu**. Na karcie **pulpit nawigacyjny** zostanie wyświetlony postęp zadania etykietowania. 

Na karcie **dane** można przyjrzeć się zestawowi danych i przejrzeć dane z etykietami. Jeśli widzisz nieprawidłowe dane etykiet, możesz je **zaznaczyć** i wybrać pozycję **Odrzuć**, co spowoduje usunięcie etykiet i umieszczenie danych z powrotem w kolejce bez etykiety. 

Na karcie **zespół** można przypisywać lub cofać przypisanie zespołów do tego projektu. 

Jeśli chcesz przełączyć projekt do trybu offline lub online, wybierz przycisk **wstrzymaj**/**Start** , który przełącza stan uruchomienia projektu.

Dane można oznaczyć bezpośrednio na stronie **szczegółów projektu** , wybierając pozycję **dane etykiet**. Dane można etykietować tylko wtedy, gdy projekt jest uruchomiony. 

## <a name="export-the-labels"></a>Eksportowanie etykiet

W dowolnym momencie możesz wyeksportować dane etykiety na potrzeby eksperymentowania w usłudze Machine Learning. Etykiety obrazów można eksportować w [formacie Coco](http://cocodataset.org/#format-data) lub jako zestaw danych usługi Azure ml. Przycisk **Eksportuj** znajdziesz na stronie **szczegółów projektu** w projekcie etykietowania.

Plik COCO jest tworzony w domyślnym magazynie obiektów BLOB w obszarze roboczym Azure ML w folderze w obszarze **Export/Coco**. Możesz uzyskać dostęp do wyeksportowanego zestawu danych Azure ML w sekcji **zestawy** danych Azure Machine Learning. Strona szczegóły zestawu danych zawiera również przykładowy kod umożliwiający uzyskanie dostępu do etykiet z poziomu języka Python.

![Wyeksportowany zestaw danych](media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="next-steps"></a>Następne kroki

* Obrazy etykiet do [wykrywania klasyfikacji obrazów lub obiektów](how-to-label-images.md)
* Dowiedz się więcej o [Azure Machine Learning i Studio](../compare-azure-ml-to-studio-classic.md)